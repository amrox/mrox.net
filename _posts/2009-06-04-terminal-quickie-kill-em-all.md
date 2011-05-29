---
kind: article
layout: post
date: 2009-06-04 18:56:00 -04:00
title: "Terminal Quickie: Kill Em All"
existing_url: http://mrox.net/blog/2009/06/04/terminal-quickie-kill-em-all/
---

More often than I'd like, my Mac gets into a very unhappy state. Running applications won't respond, but they won't force quit either. New applications won't launch, and the unresponsive applications block a proper system restart.

Usually I want to cut my losses, and get my machine back into a good state as soon as possible.  As a last resort, I could do a hard restart with the old hold-the-power-button-down-for-5-seconds trick, but then I have to wait for the machine to reboot and risk damaging my filesystem.

There is an alternative method that will kill all of your processes, responsive or not, and get you back working much more quickly - as long as you still have shell access:

    $ kill -9 -1

**Warning:** this really will kill all your running processes immediately, without saving any data.  Use wisely.

## Discussion ##

So what's going on here?  Basically, the `kill` ([man page](http://developer.apple.com/documentation/Darwin/Reference/ManPages/man1/kill.1.html)) is used to terminate processes.  Usually it is used to kill a single process, like so:

    $ kill 1234

where `1234` is the process id. The process id can be obtained a number of ways, but the most common is with the `ps` ([man page](http://developer.apple.com/documentation/Darwin/Reference/ManPages/man1/ps.1.html)) command.

The `kill` command also lets you specify a [signal](http://en.wikipedia.org/wiki/POSIX_signal) to send to the process. By default, it sends the TERM signal, which ask the program to terminate.  However, the TERM signal isn't always enough.  Sn unresponsive program often won't be able to react to the signal, and programs can even choose to ignore the signal completely.

Luckily, the [`KILL` signal](http://en.wikipedia.org/wiki/SIGKILL) does not suffer from the problem.  When this signal is sent, the operating system will stop the process dead in its tracks, whether it wants to stop or not. The `KILL` signal is very powerful, but don't only use it as a last resort because it does not allow processes to save data, close resources, and otherwise exit cleanly.

You can send the `KILL` signal to a process like this:

    $ kill -KILL 1234

Or use the numeric value for the signal, which is `9`:

    $ kill -9 1234

There is one final piece to the puzzle.  Notice that in the original command we pass a `-1` as the process id.  This is a special argument that tells `kill` to send the signal to *all* processes belonging to you (or every process on the system, if you're the super user).

Basically, `kill -9 -1` is a quick way or killing every process belonging to you.  Running this on a OS X system is a quick a dirty way to clear your environment and start fresh.  Remember, this will kill all processes immediately - without saving data - and log you out, so use with care.