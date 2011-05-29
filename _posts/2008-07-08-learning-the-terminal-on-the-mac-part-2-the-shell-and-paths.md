---
kind: article
layout: post
date: 2008-08-07 21:17:00 -04:00
title: "Learning the Terminal on the Mac - Part 2: The Shell and Paths"
existing_url: http://mrox.net/blog/2008/07/08/learning-the-terminal-on-the-mac-part-2-the-shell-and-paths/
asset_root: /assets/2008-07-08-learning-the-terminal-on-the-mac-part-2-the-shell-and-paths/
---

In the [first article](http://mrox.net/blog/2008/06/30/learning-the-terminal-on-the-mac-introduction-and-moving-around/) we learned how to launch Terminal and get started using the shell.  Also we covered some basic commands for moving around, including `cd`, [`pwd`](http://developer.apple.com/documentation/Darwin/Reference/ManPages/man1/pwd.1.html) and [`ls`](http://developer.apple.com/documentation/Darwin/Reference/ManPages/man1/ls.1.html).

In this action-packed episode, we are going to take a closer look at directories and paths.  Usually. I would try to cover material in a bit more breadth before going into depth on a particular topic. However, an understanding of paths is important foundational knowledge, and will be very useful for our next topic, basic file manipulation.

Before diving into paths and directories, I want to share a little more background information about the shell, and also some tips on how to get yourself out of trouble.

## About the Shell

This "shell" thing we have been talking about is actually a kind of program.  There are several different shells available on the Mac, but the most popular and the default is called [bash](http://www.gnu.org/software/bash/).  Bash stands for "Bourne-again Shell", was created over 20 years ago and it is still being actively developed<cite><a href="http://en.wikipedia.org/wiki/Bash">[1]</a></cite>.

Due to it's long history in the UNIX world, bash sometimes isn't the most user-friendly piece of software.  Sometimes you can make a typo that puts your shell into a state you don't understand.  Fortunately, all hope is not lost.

### Getting out of Trouble

You're probably familiar with some of the common Mac OS X keyboard shortcuts, such as `⌘S` (Save), `⌘P` (Print), and `⌘Q` (Quit).  Most keyboard shortcuts in the graphical part of Mac OS X use the `⌘` ([Command key](http://en.wikipedia.org/wiki/Command_key)) modifier.  These shortcuts are also used in Terminal.app for quitting, closing windows, etc.

However, remember that Terminal.app loads a shell, bash, inside of it.  Bash uses a completely different set of keyboard shortcuts, which use the <a href="http://en.wikipedia.org/wiki/Control_key">Control key</a>.  This key is usually labeled "ctrl" on your Mac keyboard, and uses `^` as its symbolic representation.

If you know only one control sequence in the shell, than it should be `^C`.  Without going into too much detail, this keystroke is similar to "cancel" or "escape".  If you end up in some mode or program that you don't understand, you can often get back to the prompt with just `^C`.


Perhaps it is best explained with a *haiku*:

<blockquote>
Lost or stuck in bash?<br/>
Prompt gone, output out of hand?  <br/>
<code>^C</code> saves you <br/>
</blockquote>

Then again, maybe not.  Time to move on to our Feature Presentation: Directories and Paths.



## Directories

As a Mac user you should already be familiar with using folders in the Finder.  Well, a *directory* is just the more shell-oriented term for a folder, and since we're working in the shell, we'll talk in terms of *directories* most often.

### Special Directories

There are a few "special" directories that we should touch on before going further.

The top-level directory in your Mac's hard drive is referred to as `/` or the *root directory* in shell terms.  All other directories are descendants of the root directory.  It's easy to navigate to `/` and see what is there, using `cd` and `ls`.

<img src="{{ page.asset_root }}lt2-ls-root.png" alt="lt2-ls-root.png" border="0" width="585" height="447" />

The next special directories we need to mention are `.` (dot) and `..` (dot dot).  The directory `.` is just an alias for your current directory.  If you type `cd .` at the prompt, essentially nothing will happen.  The purpose of the `.` directory might not be immediately apparent, but will be because especially useful when we discuss file manipulation.

The `..` directory is similar to `.`, except that it refers to the directory "above" your current directory, also known as the parent directory.  Typing `cd ..` from your home directory will move you up to the `/Users` directory, and typing it again will put you in `/` (the root).

These directories will actually be shown in the output of `ls` if the `-a` option is specified:

<img src="{{ page.asset_root }}lt2-dot-and-dotdot.png" alt="lt2-dot-and-dotdot.png" border="0" width="585" height="447" />

Astute readers may be wondering about the effects of `cd ..` if your current directory is `/`.  Well, fear not.  You will not accidently change into some forbidden directory, whose contents, once seen, may not be unseen.  When in root, `cd ..` essentially does nothing.

The final magic directory is `~`.  This is simply a shortcut to your *home directory*.  Every user has one and only one home directory.  On Mac OS X, all users' home directories are in `/Users` directory.  Also you may notice that your prompt shows `~` when you are in your home directory, rather than the full name of the directory (for basic prompt description, see the <a href="http://mrox.net/blog/2008/06/30/learning-the-terminal-on-the-mac-introduction-and-moving-around/#prompt">last article</a>).

<img src="{{ page.asset_root }}lt2-tilde.png" alt="lt2-tilde.png" border="0" width="585" height="447" />

There is one important stipulation when using `~`.  It does not work inside either single (`'`) or double (`"`) quotes.  This is due to the way `~` is implemented in the shell.  All the other special directories mentioned in this section have no problems with quotes, however.  Look to the <a href="#escaping">escaping</a> and <a href="#path-examples">examples</a> sections for how to deal with `~` in paths.

To review:
<ul>
	<li>
		<code>/</code>
		<ul>
			<li>The top-level directory of your computer's hard disk.</li>
			<li>Usually called *root* or *slash*.</li>
		</ul>
	</li>
	<li>
		<code>.</code>
		<ul>
			<li>The current directory.</li>
			<li>Usually called *dot*.</li>
		</ul>
	</li>
	<li>
		<code>..</code>
		<ul>
			<li>The directory that contains the current, or *parent* directory.</li>
			<li>Usually called *dot dot*.</li>
		</ul>
	</li>
	<li>
		<code>~</code>
		<ul>
			<li>Your home directory.</li>
			<li>Usually called *tilde*.</li>
			<li><strong>Won't work inside quotes!</strong></li>
		</ul>
	</li>
</ul>

## Paths

We've already worked with paths a little bit in the <a href="http://mrox.net/blog/2008/06/30/learning-the-terminal-on-the-mac-introduction-and-moving-around/">last article</a> with the `cd` command.  Now we will talk about them in a little more detail.

The *path separator* on Mac OS X is `/` (called a *[slash](http://en.wikipedia.org/wiki/Slash_(punctuation))* or sometimes *forward slash*).

### Kinds of Paths

There are two kinds of paths, *relative* and *absolute*.  Relative paths reference files from your current directory, while absolute paths specify the complete location of a file or directory.

This is I how would change to the `Downloads` directory of my "demo" account using an absolute path.

    cd /Users/demo/Downloads

And this is how I would change to the `Downloads` directory of my "demo" if I was already in my home directory (`~`).

    cd Downloads

Both these commands get me to the same directory, we just got there by a different *path*.

<img src="{{ page.asset_root }}lt2-ls-downloads-abs-1.png" alt="lt2-ls-downloads-abs-1.png" border="0" width="585" height="447" />

<img src="{{ page.asset_root }}lt2-ls-downloads-rel2.png" alt="lt2-ls-downloads-rel2.png" border="0" width="625" height="487" />

Above, we worked with paths to directories.  Well, paths to files work the same way.  I could open a file called "Notes.txt" on my Desktop using the absolute path:

    open /Users/demo/Desktop/Notes.txt

or a relative path (shown from my home directory):

    open Desktop/Notes.txt

### Escaping
<a name="escaping"> </a>

The shell gives special meaning to certain characters in order to implement all its features.  If you are familiar with web programming, this is similar to how URLs are [percent-ecoded](http://en.wikipedia.org/wiki/Percent-encoding).

The most notable of these characters is probably the space.  The shell uses spaces to separate commands from options and arguments.  However, this is a problem if the argument you want already has a space in it.  This occurs in many files and folders on Mac OS X, like the "About Stacks.pdf" file we used in the last article.

Special characters are *escaped* using the `\` or *backslash* character.  This can be confusing since this character is used as the path separator on Microsoft Windows.  It's probably best if you can just wipe Windows from your mind entirely (zing!).

To escape a space in a file or directory, with prefix it with a `\`, like so:

    open Documents/About\ Stacks.pdf

Alternatively, you can quote the entire argument, using double quotes (`"`):

    open "Documents/About Stacks.pdf"

Finally, we don't need to escape the entire path, just the part that actually needs the escaping:

    open Documents/"About Stacks.pdf"

Actually, single quotes (`'`) can be used instead, but they have a slightly different meaning.  We'll discuss the meanings of the different types of quotes soon.

Also, note that if you use <a href="http://mrox.net/blog/2008/06/30/learning-the-terminal-on-the-mac-introduction-and-moving-around/#completion">tab completion</a> the shell will be smart enough to do most of the escaping for you.  If you're hungry for more, you can read more about escaping in the [Escaping topic in the Advanced Bash-Scripting Guide](http://tldp.org/LDP/abs/html/escapingsection.html).

### Path Examples
<a name="path-examples"> </a>

Here are a few examples of how to put all this knowledge together:

<table>
	<tr>
		<td><code>cd ..</code></td>
		<td width="30"></td>
		<td>Go to the directory one level above</td>
	</tr>
	<tr>
		<td><code>cd ../..</code></td>
		<td width="30"> </td>
		<td>Go to the directory two levels above</td>
	</tr>
	<tr>
		<td><code>cd ../../</code></td>
		<td width="30"> </td>
		<td>Go to the directory two levels above (same as previous, extra /'s are ignored)</td>
	</tr>
	<tr>
		<td><code>cd ~</code></td>
		<td width="30"> </td>
		<td>Go to your home directory</td>
	</tr>
	<tr>
		<td><code>cd ~/Documents</code></td>
		<td width="30"> </td>
		<td>Go to your Documents directory</td>
	</tr>
	<tr>
		<td><code>cd ~/..</code></td>
		<td width="30"> </td>
		<td>Go to the directory one level above your home directory (Users)</td>
	</tr>
	<tr>
		<td><code>cd ~/Library/Application\ Support</code></td>
		<td width="30"> </td>
		<td>Go to your Application Support folder using escaping</td>
	</tr>
	<tr>
		<td><code>cd ~/Library/"Application Support"</code></td>
		<td width="30"> </td>
		<td>Go to your Application Support folder using quotes (don't quote the <code>~</code>)</td>
	</tr>
	<tr>
		<td><code>open ~/Documents</code></td>
		<td width="30"> </td>
		<td>Open your Documents folder in Finder</td>
	</tr>
	<tr>
		<td><code>open .</code></td>
		<td width="30"> </td>
		<td>Open the current directory in Finder (see, the <code>.</code> *is* useful)</td>
	</tr> 
</table>

## Wrap Up

We now have enough knowledge regarding paths to provide a solid foundation going forward.  Next up, we'll be talking about copying, moving, and deleting files.  If you have questions, comments, or suggestions for future articles, please note the comments section below.  I believe it will accommodate your needs.
