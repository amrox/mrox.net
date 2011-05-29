---
kind: article
layout: post
date: 2010-02-16 9:26:00 -04:00
title: On Background Apps
existing_url: http://mrox.net/blog/2010/02/16/on-background-apps/
---

The lack of background applications or multitasking has come into the collective chattersphere again with the announcement of the iPad. It's the new "non-user-replacable battery". The ability to run more than one application at a time sounds like a good thing. Two (or twelve) are better than one right? However, multitasking is a means to an end, not a goal in itself. I'm interested as to *why* everyone is clamoring for the ability to run applications in the background. What exactly do we they to be able to do?

### The Why of Background Applications

In an effort to understand the nature of background applications and multitasking from an regular user's point of view, I tried to come up with some basic use cases.

 - **Switching between applications efficiently.** Good iPhone (and hopefully iPad) apps solve this by preserving their state when quit and restoring it at launch. If done right it usually achieves the same effect as true multitasking.
 
 - **Viewing several application at once.** This is an obvious use-case for desktop OS's with large 15"+ screens, but a non-issue for iPhone applications. It doesn't make sense to display more than a single application at a time with such a small screen size. We'll need to wait and see how this plays out for iPad, but my guess is that the screen size is *just small enough*.

 - **Things that take a long time.** There are countless tasks that your computer could do "in the background" -- compiling, running a scientific simulation, rendering a large video -- but these aren't usually what you want to be running on your resource-constrained mobile device. Downloading sizable files is the one kind of background task that is common in even mobile devices. iPhone's built-in iTunes and App Stores already support this use case by downloading media and applications in the background.

 \* iPhone OS has no public API that allows for downloading data in the background while your application is no longer running. 

 - **Notifications.** Sometimes we want to be notified when a new email or instant message arrives. Although it's not perfect, this use case is mostly addressed by the Apple Push Notification service. Again, when done right, the actual user experience is very close to what a real background application could deliver. 

 - **Audio**. Audio is in a slightly different category than everything else. It can be consumed effectively without any on-screen interface and extremely minimal user interaction. On iPhone, the built-in iPod application can play file-based audio content in the background. However, there are many sources of audio that don't come from files stored on your device, a prime example being streaming internet radio.
 
 \* iPhone OS has no public API that allows for playing streaming audio in the background while your application is no longer running.

**Update:** Adam Engst posted some [similar thoughts](http://db.tidbits.com/article/10989) on the same subject of background apps after I began writing this post (I'm a slow writer). He brought up one use case that wasn't in my original list:

 - **Inter-application communication.** The majority of every-day inter-application communication is via Copy/Paste or Drag/Drop (which is essentially Copy/Paste anyway). As of version 3.0, iPhone OS supports Copy/Paste, and the Drag/Drop metaphor doesn't make as much sense for iPhone applications, so it's reasonable that it's ommitted.

Of the <del>five</del> six general use cases listed above four are either not applicable or addressed in some way. That leaves downloading files and playing streaming audio content unaccounted for. Both downloading files and playing streaming audio have some similarities -- they both are retrieving bits from the Internet. Initially, file downloads seem like the simpler case, but may bring some extra complexity to the user. 

Arbitrary file downloads suffer from two problems: managing multiple downloads and the "where did I put that file" problem. Multiple background downloads suggests that some UI to manage these downloads will be required -- which is exactly the kind of complexity that iPhone OS strives to avoid. Also, if downloads can happen in the background where do they go? Into a shared "Downloads" area, or directly into the application? Neither of these problems are insurmountable, but I bring them up to show that file downloads will require some additional thought and design to get right.

Streaming audio avoids the management problem with one simple restriction: only one audio stream can be active at a time. In general, you only want to play audio from a single source anyway, so this is very reasonable limitation. There are obviously battery life concerns, but let's put those aside for a moment and consider what supporting background audio could look like from the API level.

### An iPhone OS API for Streaming Audio

Apple added [AVAudioPlayer](http://developer.apple.com/iphone/library/documentation/AVFoundation/Reference/AVAudioPlayerClassReference/Reference/Reference.html) in iPhone OS 2.2. At first glance, it looks like it solves most of our problem. It has an initializer that takes a URL, an observable property `currentTime` and delegate methods to provide notifications regarding interruptions and playback completion. It's a good starting point, but we'll need to make some changes:

1. **Support for `http` URLs.** Currently `AVAudioPlayer` can only play local file URLs that are in a format supported by Core Audio. If we extend it to include `http` URLs (in a format supported by Core Audio), we have a simple streaming player for basic audio types. We could even go one step further and include support for [HTTP Live Streaming](http://developer.apple.com/iphone/library/documentation/NetworkingInternet/Conceptual/StreamingMediaGuide/Introduction/Introduction.html).

1. **Set the Background Audio Player.** Cocoa often provides shared service objects via singletons. In some cases it also allows you to set the shared instance ([`NSURLCache`'s `setSharedURLCache:`](http://developer.apple.com/mac/library/documentation/cocoa/reference/Foundation/Classes/NSURLCache_Class/Reference/Reference.html) for example).  This pattern makes sense for our use case because we only want one background audio player active at a time. We'll add class methods to `AVAudioPlayer` to get and set the background player.

 The one restriction is that the background audio player instance should only be accessible to the process that originally set it to maintain cross-application confidentiality. This can be enforced by having `AVAudioPlayer` store the bundle identifer of the application that started the background audio. Still, other applications may want to know if some background audio is already playing so we'll create a second flag called `hasBackgroundAudioPlayer` that can be queried by any process.

 `// Returns YES if a background player is active, NO otherwise.`  
 `+ (BOOL) hasBackgroundAudioPlayer;`

 `// Returns nil if called by non-owning process`  
 `+ (AVAudioPlayer *) backgroundAudioPlayer;`

 `// Overrides any existing background audio players`  
 `+ (void) setBackgroundAudioPlayer:(AVAudioPLayer *)audioPlayer;`

1. **Queue several URLs.** An `AVAudioPlayer` instance was designed to play only a single file. To play multiple files in succession, an `AVAudioPlayer` must be created for each one. While restricting to a single file simplifies the usage of `AVAudioPlayer` it would be more useful for our case if we could queue several URLs. This way `AVAudioPlayer` can work through an entire playlist in the background.

 We can solve this by adding a URL queue property to `AVAudioPlayer`. The contents of this queue may be directly manipulated, so we'll just use an `NSMutableArray`. As `AVAudioPlayer` finishes playing a URL and then URL queue isn't empty, it will remove the first URL from the queue and begin playing it.

 Lastly, we should tweak the `audioPlayerDidFinishPlaying:successfully:` delegate method to also pass the URL that finished.

 `@property (readonly) NSMutableArray *URLQueue;`

 `- (void)audioPlayer:(AVAudioPlayer *)player didFinishPlayingURL:(NSURL *)url successfully:(BOOL)flag`

Now we have an enhanced `AVAudioPlayer` that can play a list of streaming audio sources in the background. Obviously there are a number of ways to provide background audio playback. This is just one approach given to show that providing such a service can be done without full-blown multitasking and moderate API additions.

I think that some mechanism to play back streaming audio in the background would be a great addition to iPhone OS as long as battery life is acceptable. iPad makes this feature even more compelling. With an iPhone, you can start your favorite streaming player, put the iPhone back in your pocket, and go about your business. It's a sidekick device. However, when you're on your iPad, you want to *use* it. You want to be browsing the web, writing, or playing a game -- not staring at the progress bar of the current track.
