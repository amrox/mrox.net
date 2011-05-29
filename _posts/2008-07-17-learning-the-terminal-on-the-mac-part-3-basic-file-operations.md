---
kind: article
layout: post
date: 2008-07-17 21:17:00 -04:00
title: "Learning the Terminal on the Mac - Part 3: Basic File Operations"
existing_url: http://mrox.net/blog/2008/07/17/learning-the-terminal-on-the-mac-part-3-basic-file-operations/
asset_root: /assets/2008-07-17-learning-the-terminal-on-the-mac-part-3-basic-file-operations/
---

By now you hopefully have a good understanding about how to reference files with paths and list files using [`ls`](http://developer.apple.com/documentation/Darwin/Reference/ManPages/man1/ls.1.html), and move around with `cd`.  Now we're going to go over basic file operations including:

 * Create
 * Copy
 * Move
 * Delete

There is a lot of information to cover this time around, but I want to make one quick note about the notation being used before we begin.  Like the `man` pages, we'll use an underline to denote a placeholder for an option or argument to a command to describe its general form.  For example, the general way to describe how to change to a directory is:

<code>cd <u>path</u></code>

Here <code><u>path</u></code> is just a placeholder. You would replace it with a path to an actual directory.


## Creating Files and Directories

There are actually many, many ways to create files on your Mac using commands in the shell, but I'm going to demonstrate a few quick ways so you can easily experiment with copy, move, and delete operations.


### `touch`

The <code>touch</code> (<a href="http://developer.apple.com/documentation/Darwin/Reference/ManPages/man1/touch.1.html">man page</a>) is a simple command that "touches" a file, meaning it updates its last modification date to the current time.

<code>touch <u>file</u></code>

A nifty feature of `touch` is that it will create the file it it doesn't exist.  This command is the easiest way to create a new file to play with.  For example to create a file called "Bananas!":

<code>touch Bananas!</code>

And the same command, in the terminal:

<div style="text-align:center;"><img src="{{ page.asset_root }}lt3-touch.png" alt="lt3-touch.png" border="0" width="585" height="447" /></div>

<h3><code>echo</code></h3>

The simplest way to create a file that has some contents is with the <code>echo</code> (<a href="http://developer.apple.com/documentation/Darwin/Reference/ManPages/man1/echo.1.html">man page</a>) command, and <em>output redirection</em>.  We'll cover output redirection in detail soon.  For now just follow the form:

<blockquote>
<code>echo &quot;<u>some text</u>&quot; &gt; <u>filename</u></code>
</blockquote>

An example:

<div style="text-align:center;"><img src="{{ page.asset_root }}lt3-echo.png" alt="lt3-echo.png" border="0" width="585" height="447" /></div>

We snuck in another command there too: <code>cat</code> (<a href="http://developer.apple.com/documentation/Darwin/Reference/ManPages/man1/cat.1.html">man page</a>).  <code>cat</code> is used for concatenating (hence its name) files, though it's most often used for dumping the contents of a text file to the screen.

<h3><code>mkdir</code></h3>

Finally, directories can be created with <code>mkdir</code> (<a href="http://developer.apple.com/documentation/Darwin/Reference/ManPages/man1/mkdir.1.html">man page</a>).

<blockquote>
<code>mkdir <u>new directory</u></code>
</blockquote>

To make a directory called "Stuff" in my home directory:

<blockquote>
<code>mkdir ~/Stuff</code>
</blockquote>

A simple example, creating the same "Stuff" directory, but using a relative path:

<div style="text-align:center;"><img src="{{ page.asset_root }}lt3-mkdir1.png" alt="lt3-mkdir1.png" border="0" width="585" height="447" /></div>


<h2>Copying: <code>cp</code></h2>

Copying files is done with the <code>cp</code> command (<a href="http://developer.apple.com/documentation/Darwin/Reference/ManPages/man1/cp.1.html">man page</a>).  This command takes at least two arguments; the first being the source and the second being the destination.

<blockquote>
<code>cp <u>source</u> <u>destination</u></code>
</blockquote>

Copying a file from my Downloads folder to my Desktop looks like this:

<blockquote>
<code>
computer:~ demo$ cd Downloads/ <br/>
computer:~ demo$ cp file.dmg ~/Desktop/
</code>
</blockquote>

A real example:

<div style="text-align:center;"><img src="{{ page.asset_root }}lt2-cp1.png" alt="lt2-cp1.png" border="0" width="585" height="447" /></div>

The <code>cp</code> command can also be used to copy several files to a single destination:

<blockquote>
<code>cp <u>source 1</u> <u>source 2</u> <u>...</u> <u>source n</u> <u>destination</u></code>
</blockquote>

For example:

<blockquote>
<code>
computer:~ demo$ cd Downloads/ <br/>
computer:Downloads demo$ cp file1.dmg file2.dmg ../Desktop/<br/>
</code>
</blockquote>

<h2>Moving: <code>mv</code></h2>


The <code>mv</code> command (<a href="http://developer.apple.com/documentation/Darwin/Reference/ManPages/man1/mv.1.html">man page</a>) command is used for moving files to a different location, or renaming files.  It's usage is very similar to <code>cp</code>:

<blockquote>
<code>mv <u>source</u> <u>destination</u></code>
</blockquote>
<blockquote>
<code>mv <u>source 1</u> <u>source 2</u> <u>...</u> <u>source n</u> <u>destination</u></code>
</blockquote>

For example:

<blockquote>
<code>
computer:~ demo$ cd Downloads/ <br/>
computer:Downloads demo$ mv file1.dmg file2.dmg ~/.Trash/
</code>
</blockquote>

Here is a real example of moving a file from my Downloads folder to my Desktop, and then renaming that file:

<div style="text-align:center;"><img src="{{ page.asset_root }}lt2-mv1.png" alt="lt2-mv1.png" border="0" width="585" height="447" /></div>

Also, <code>mv</code> can be used for moving entire directories.  Here's an example that shows moving a folder called "Stuff" from the Desktop to the home directory:

<div style="text-align:center;"><img src="{{ page.asset_root }}lt2-mv3.png" alt="lt2-mv3.png" border="0" width="585" height="447" /></div>

<h2>Deleting: <code>rm</code></h2>

Files and directories can be deleted (or removed) with the <code>rm</code> command (<a href="http://developer.apple.com/documentation/Darwin/Reference/ManPages/man1/rm.1.html">man page</a>).  A word of caution:  <code>rm</code> will remove files <em>immediately</em>, skipping the Trash Can.  Use it carefully.

<blockquote>
<code>rm <u>file 1</u> <u>file 2</u> <u>...</u> <u>file n</u></code>
</blockquote>

The following is an example using <code>touch</code> to create several files and <code>rm</code> to delete them:

<div style="text-align:center;"><img src="{{ page.asset_root }}lt3-rm1.png" alt="lt3-rm1.png" border="0" width="585" height="447" /></div>

<h2>Recursion</h2>
<a name="recursion"> </a>

For more information see <a href="#recursion">recursion</a>.

Ok, we got that lame computer science joke out of the way.  Recursion isn't all fun and games, though.  It is an option that is found in many shell commands, including some of the ones we just learned!  In this context, <em>recursion</em> basically means to include all files including sub-directories, sub-sub-directories, etc.  Both <code>cp</code> and <code>rm</code> can be instructed to act recursively with the <code>-r</code> option.

<h3><code>cp -r</code></h3>

With the <code>-r</code> option, <code>cp</code> can be used to copy a directory and all its contents to another location.

<blockquote>
<code>cp -r <u>source dir</u> <u>destination</u></code>
</blockquote>

For example, if I wanted to copy my "Documents" folder to a "Backup" volume:

<blockquote>
<code>cp -r Documents /Volumes/Backup/</code>
</blockquote>

Note that there is no trailing <code>/</code> after the source directory, <code>Documents</code>.  This is an important subtlety when using <code>cp -r</code>.  If the trailing <code>/</code> is omitted, the directory and all its contents will be copied.  However, if the trailing <code>/</code> is included, only the <em>contents</em> of the directory will be copied to the destination, not the directory itself.  Here are two examples showing the differences between the two commands:

With a trailing <code>/</code>, only the contents will be copied:
<div style="text-align:center;"><img src="{{ page.asset_root }}lt3-cp-r2-annotated.png" alt="lt3-cp-r2-annotated.png" border="0" width="585" height="447" /></div>

And now without the trailing <code>/</code>, the entire directory will be copied:

<div style="text-align:center;"><img src="{{ page.asset_root }}lt3-cp-r3-annotated.png" alt="lt3-cp-r3-annotated.png" border="0" width="585" height="447" /></div>


<h3><code>rm -r</code></h3>

If you thought <code>rm</code> was dangerous before, you ain't seen nuttin' yet.  Just like with <code>cp</code>, the <code>-r</code> option allows you to remove a directory and all its contents.  It is typically paired with the force option, <code>-f</code>, which swallows certain errors and skips all prompts and warnings.

<blockquote>
<code>rm -rf <u>stuff you never want to see again</u></code>
</blockquote>

Example:

<div style="text-align:center;"><img src="{{ page.asset_root }}lt3-rm-rf.png" alt="lt3-rm-rf.png" border="0" width="585" height="447" /></div>

Be especially careful with <code>-f</code>.  There is no undo.

<h2>Wildcards</h2>

If you haven't guessed already, bash supports basic wildcards for matching files and paths.  The real term for this functionality is <a href="http://tldp.org/LDP/abs/html/globbingref.html">globbing</a>, which we'll cover in more detail in the future.  For now, we'll cover two special characters which you're probably already familiar with from other pieces of software: <code>*</code> and <code>?</code>.

<h3>Match many characters:<code>*</code></h3>

The asterisk (<code>*</code>), or sometimes called "star",  is the standard "match everything" wildcard.  For example, say you want to copy all DMGs from the Downloads directory to /Volumes/Backup, but not the other files.

<blockquote>
<code>
computer:~ demo$ cd Downloads/ <br/>
computer:Downloads demo$ cp *.dmg /Volumes/Backup
</code>
</blockquote>

This would match any file (or directory) that ends with <code>.dmg</code>.  However, some DMGs are also zipped and end with <code>.gz</code> or <code>.zip</code>.  We can make sure we get all the DMGs but using two asterisks:

<blockquote>
<code>
computer:Downloads demo$ cp *.dmg* /Volumes/Backup
</code>
</blockquote>


Most commands that take a path as an argument, including <code>mv</code> and <code>rm</code> will also work with <code>*</code>. But again, be <em>very</em> careful with <code>rm</code> and <code>*</code>.

Here is how you could clean out your <code>~/Library/Caches</code> folder by moving al its contents to the Trash.  If you actually do this, you should reboot after the operation is complete.

<div style="text-align:center;"><img src="{{ page.asset_root }}lt3-mv-caches-to-trash.png" alt="lt3-mv-caches-to-trash.png" border="0" width="585" height="447" /></div>

<h3>Match one character: <code>?</code></h3>

The <code>?</code> wildcard isn't used as often as <code>*</code> but it is still fairly common.  It differs from <code>*</code> in that it only matches <em>one</em> character.  It's similar to the blank tile in <a href="http://en.wikipedia.org/wiki/Scrabble">Scrabble</a>.  If you many digital photos downloaded from your camera, and they all have names like <code>IMG_0020.jpg</code> and <code>IMG_0164.jpg</code>, you could move them all like this:

<blockquote>
<code>
computer:New Photos demo$ mv IMG_????.jpg ~/Pictures
</code>
</blockquote>

The <code>?</code> only matches a single character, so to match a four-digit number, we need to place four <code>?</code>'s in a row.

<h2>Files and Directories Revisited</h2>

You may have noticed that the same commands can be used to copy, move, and delete files and directories (though not create, sorry).

This is because UNIX-based operating systems treat directories as a special kind of file.  This means that many of the commands used for manipulating files can also be used for manipulating directories.  UNIX actually goes beyond just directories. It also handles things like devices and network sockets as files as well, but that is a bit beyond the scope of our current article.

<h2>Wrap-up</h2>

The information covered in these last three tutorials should give you a very powerful toolbox.  You can now do many of the basic file management tasks you would normally do in the Finder in the shell instead.  This article didn't discuss much Mac-specific information, but the tentative topic for the next article is how to bring the Finder and the Terminal together.

As always, I appreciate your feedback about the current material and future directions.  What would <strong>you</strong> like to see next?
