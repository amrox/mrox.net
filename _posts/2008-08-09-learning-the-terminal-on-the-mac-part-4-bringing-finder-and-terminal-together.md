---
kind: article
layout: post
date: 2008-08-09 07:46:00 -04:00
title: "Learning the Terminal on the Mac - Part 4: Bringing Finder and Terminal Together"
existing_url: http://mrox.net/blog/2008/08/09/learning-the-terminal-on-the-mac-part-4-bringing-finder-and-terminal-together/
asset_root: /assets/2008-08-09-learning-the-terminal-on-the-mac-part-4-bringing-finder-and-terminal-together/

---

The <a href="http://mrox.net/blog/2008/06/30/learning-the-terminal-on-the-mac-introduction-and-moving-around/">first</a> <a href="http://mrox.net/blog/2008/07/08/learning-the-terminal-on-the-mac-part-2-the-shell-and-paths/">few</a> <a href="http://mrox.net/blog/2008/07/17/learning-the-terminal-on-the-mac-part-3-basic-file-operations/">articles</a> laid out a good foundation of basic shell usage, but little Mac-specific information.  In this article, all that will change.  We're going to show a few ways to get back and forth between Terminal and Finder.

<h2>From Finder to Terminal</h2>

There a few methods to get information from Finder into the Terminal, or use Finder in Terminal-like ways.

<h3>Path Bar</h3>

One new feature in the 10.5 Finder is <em>Path Bar</em>.  The Path Bar makes it easy to see the full path to the current folder.  To enable the Path Bar (if you haven't already), activate Finder, and choose <code>View → Show Path Bar</code> from the menu.

<div style="text-align:center;"><img src="{{ page.asset_root }}lt2-show-path-bar.png" alt="lt2-show-path-bar.png" border="0" width="765" height="477" /></div>

Once enabled, the Path Bar will appear at the bottom of all your Finder windows, just above the Status Bar.  Below is how the Path Bar appears when I have my Home folder open in Finder.

<div style="text-align:center;"><img src="{{ page.asset_root }}lt2-path-bar.png" alt="lt2-path-bar.png" border="0" width="830" height="542" /></div>

The the above path would be interpreted as <code>/Users/demo</code> in the shell.

<div style="text-align:center;"><img src="{{ page.asset_root }}lt4-ls-path-bar.png" alt="lt4-ls-path-bar.png" border="0" width="585" height="447" /></div>

<h3>Go to Folder…</h3>

Finder also has a built in command called <em>Go to Folder…</em> that allows a path to be entered in a similar way as in the shell.  This feature can be accessed by choosing <code>Go → Go to Folder…</code> from the menu, but it's faster to use the shortcut <code>⌘⇧G</code>.

<div style="text-align:center;"><img src="{{ page.asset_root }}lt2-go-to-folder.png" alt="lt2-go-to-folder.png" border="0" width="754" height="465" /></div>

The path entry field of the <em>Go to Folder…</em> feature also has rudimentary tab-completion support.  If you type a few letters and either press the tab key (⇥) or just wait a few seconds, it will complete the current path component with the first match it finds.  It's not nearly as good as the completion in bash, but it's there.

<h3>Drag &amp; Drop Paths</h3>

Files or folders in Finder can also be dragged and dropped directly onto Terminal.  Doing so will insert the <strong>path</strong> to the file or folder into the shell.

<div style="text-align:center;"><img src="{{ page.asset_root }}lt4-drag-drop-path1.png" alt="lt4-drag-drop-path1.png" border="0" width="551" height="336" /></div>

And voilà, the path is inserted into the shell:

<div style="text-align:center;"><img src="{{ page.asset_root }}lt4-drag-drop-path2.png" alt="lt4-drag-drop-path2.png" border="0" width="550" height="340" /></div>

<h3>OpenTerminalHere</h3>

<em>OpenTerminalHere</em> is a small Applescript application that launches Terminal and automatically <code>cd</code>'s to the current folder in Finder.  It was originally written by <a href="http://www.entropy.ch/software/applescript/">Marc Liyanage</a> and has since been <a href="http://jo.irisson.free.fr/?p=59">updated</a> to make it more Leopard-friendly.  I find it extremely handy, and if you think you would too, go <a href="http://jo.irisson.free.fr/?p=59">here for the download and setup instructions</a>.

OpenTerminalHere is meant to be installed into Finder's toolbar.  This is done by simply dragging the OpenTerminalHere icon to Finder's toolbar and pausing a few seconds until a small green "plus" indicator appears next to the mouse pointer.  If you want to tweak the position of OpenTerminalHere or add spacing, choose <code>View → Customize Toolbar…</code> in Finder's menu after installing.

<div style="text-align:center;"><img src="{{ page.asset_root }}lt4-install-openterminalhere.png" alt="lt4-install-openterminalhere.png" border="0" width="694" height="469" /></div>


Now just click on the OpenTerminalHere icon in the Finder window's toolbar, and a Terminal will launch and <code>cd</code> to the current folder.

<h2>From Terminal to Finder</h2>

Even though the shell is a powerful tool, sometimes you may want to switch back to Finder to finish a task.  Here are a couple tools to do just that.

<h3><code>open</code></h3>

We've used the <code>open</code> command (<a href="http://developer.apple.com/documentation/Darwin/Reference/ManPages/man1/open.1.html">man page</a>) a few times already to open files with their default application and folders in Finder.  This command is actually specific to Mac OS and is not found in other Unix-like operating systems.  The <code>open</code> command also allows you to choose the application with which to open the file, or open the file in the default text editor.

To open a file using a specific application, use the <code>-a</code> option:

<blockquote>
<code>
open -a [application] [file]
</code>
</blockquote>

For example, if I wanted to open a file called "hello.txt" in <a href="http://code.google.com/p/macvim/">MacVim</a> instead of the default editor, TextEdit, I would type:

<blockquote>
<code>
open -a /Applications/MacVim.app hello.txt
</code>
</blockquote>

Also as we mentioned just sentences ago, you can use the <code>open</code> command to open a file using the default text editor.  This is done with the <code>-t</code> option.

<blockquote>
<code>
open -t [file]
</code>
</blockquote>

Let's say I'm a curious individual, and I want to see exactly what iTunes stores in its XML library file.  I would type:

<blockquote>
<code>
open -t ~/Music/iTunes/iTunes\ Music\ Library.xml
</code>
</blockquote>

(Remember those extra <code>\</code>'s are to <em>escape</em> the spaces, which was discussed in the <a href="http://mrox.net/blog/2008/07/08/learning-the-terminal-on-the-mac-part-2-the-shell-and-paths/#escaping">second article</a>.

<h3>Revealing Files in Finder</h3>

The <code>open</code> command can be used to open folders in Finder, but we don't have a way to <em>reveal</em> a file within a folder.  Many applications have a built-in "Show in Finder" feature, but unfortunately the shell does not.  However, all hope is not lost.

One of the simplest ways to add new commands in the shell is through creating <em>shell scripts</em>.  Shell scripting is a deep topic in itself, but for know, just think of a shell script as a small program that is interpreted by the shell itself.  Shell scripts are written in plain text so they are quick to create and easy to modify.

A hint entitled "<a href="http://www.macosxhints.com/article.php?story=20060519105305580">Select files in Finder from Terminal</a>" on <a href="http://www.macosxhints.com/">Mac OS X Hints</a> presents a way add a "Show in Finder" feature to the shell using a custom shell script.  Several others submitted improvements and alternative approaches in the comments.  The solution I liked best is described in <a href="http://www.macosxhints.com/comment.php?mode=view&cid=75316">this comment</a> by <a href="http://www.macosxhints.com/users.php?mode=profile&uid=1002162">caesurae</a>.

I liked this solution because it was relatively concise and easy to read (for the bash-literate), and it is contained within a single file.  I modified the script very slightly to fix a problem with revealing directories.

Here is the full script.  Immediately following is a download link, which is probably more useful.  Following that, and even more useful still are installation instructions.

<h4>The Script</h4>

<blockquote>
<code>
<pre>
#!/bin/bash
#
# revealInFinder.bash
#
# 05.26.2006
#
# caesurae@gmail.com
#
# tested on Mac OS X 10.3.9 build 7W98 - Darwin 7.9.0 - AppleScript 1.9.3
#
# reveal the given file(s) and/or folder(s) in a Finder window
#
# usage: revealInFinder.bash ~/dir/file "/dir/dir/my file" file
#
########
#
# 08.07.2008
#
# Andy Mroczkowski
#
# minor update to allow for more reliable opening of directories
#
# tested on Mac OS X 10.5.4 - AppleScript 2.0.1k
#
##

# if no arguments are given, echo the usage string
if [ $# -lt 1 ]; then
  echo 'Usage: '`basename "$0"`' [files]' >&2
  exit 1
fi

# define $n, gets +1 for each argument given
# not sure if $n is needed, how to get index number of $1, $2, etc.?
n=0

# define $act, gets +1 for each argument that passes test
act=0

# step thru each argument one at a time
for thearg in "$@"; do

  n=$(( n + 1 ))

  # if $thearg exists then
  if [ -e "$thearg" ]; then

    # get the absolute path to the argument
    thearg="`cd \`dirname \"$thearg\"\`; pwd`/`basename \"$thearg\"`"

    # create a applescript statement using $thearg for osascript to execute
    osatext='tell application "Finder" to reveal POSIX file "'"$thearg"'"'
    /usr/bin/osascript -e "$osatext"

    # $act activates Finder after processing the remaining arguments
    act=$(( act + 1 ))

  # else if $thearg does not exist then
  else

    # if $thearg is not the last argument given then
    if [ $n -lt $# ]; then

      # ask to continue processing the remaining arguments
      echo -n '"'"$thearg"'" does not exist and will be ignored. continue?  (y/n)? ' >&2
      read ans
      case $ans in
        "n" ) exit 1 ;;
        "y" ) continue ;;
      esac

    # else if $thearg is the last arguement then
    else

      # print the "does not exist" string and exit
      echo '"'"$thearg"'" does not exist.' >&2

      # exit status 1 indicates an error occurred
      exit 1
    fi
  fi
done

# if $act is greater than 0 then activate the finder
if [ $act -gt 0 ]; then
  /usr/bin/osascript -e 'tell application "Finder" to activate'
fi

# exit status 0 indicates all is ok
exit 0
</pre>
</code>
</blockquote>

<a href="{{ page.asset_root }}reveal.zip">Download the <code>reveal</code> script</a> (and unzip it if necessary).

<h4>Installation</h4>

Once you have <code>reveal</code> downloaded, you need to put it a place where the shell can find it.  Executable files are usually kept in a directory called "<code>bin</code>".  To avoid any permissions problems, we're just going to set up this script for the current user.  To create a <code>bin</code> directory for yourself, type:

<blockquote>
<code>
mkdir ~/bin
</code>
</blockquote>

Now the <code>reveal</code> script must be moved into the <code>bin</code> directory.  If you saved <code>reveal</code> in your <code>Downlaods</code> folder, you would type the following:

<blockquote>
<code>
mv ~/Downloads/reveal ~/bin
</code>
</blockquote>

Of course if you saved <code>reveal</code> to some place other than your <code>Downloads</code> folder, you'll have use the appropriate path.

Finally we need to ensure that <code>reveal</code> is executable.  If you downloaded it from the using the link above, the permissions should have been preserved within the zip file, but we'll reset them just in case.  Changing basic permissions from the command line is done with <code>chmod</code> (<a href="http://developer.apple.com/documentation/Darwin/Reference/ManPages/man1/chmod.1.html">man page</a>).  We'll go into detail on <code>chmod</code> later.  For now just type

<blockquote>
<code>
chmod +x ~/bin/reveal
</code>
</blockquote>

Ok, now everything is in place, but there is still one more thing left to do.  The shell only looks in a few pre-defined places for programs and although a <code>~/bin</code> directory is common, it does not search that directory by default.  We can tell the shell to look for programs in our <code>~/bin</code> directory by adding it to our <code><em>PATH</em></code>.

The <code>PATH</code> variable controls where the shell looks for programs.  You can manually type in the command to add <code>~/bin</code> to our <code>PATH</code>, but the <code>PATH</code> variable is cleared every time we open a new shell.  That's a hassle.  Instead we want it so our <code>PATH</code> is set up for us automatically.  To do that, we will add the command to a special file called <code>.bash_profile</code> which is loaded every time a new shell is started.

First, create the file.  The file <strong>must</strong> be in your home directory (<code>~</code>) or the shell won't find it.  If you already have a <code>.bash_profile</code> the following command won't overwrite it.

<blockquote>
<code>
touch ~/.bash_profile
</code>
</blockquote>

Now open the new, empty <code>.bash_profile</code> file in your default text editor:

<blockquote>
<code>
open -t ~/.bash_profile
</code>
</blockquote>

Next add the following line <strong>exactly</strong> as it appears below.  Copy and Paste are your friends.  If you already have some stuff in your <code>.bash_profile</code> just add it to the end.

<blockquote>
<code>
export PATH=$PATH:$HOME/bin
</code>
</blockquote>

Save and close the file.  Then close the Terminal you were working in and open a new one (your <code>.bash_profile</code> is only read when the shell starts up).

Congratulations, you now have installed a shell script.  Have a beer.

<h4>Usage</h4>

Our new <code>reveal</code> script is easy to use.  You just type <code>reveal</code> followed by one or more paths to files or directories.  In fact, if you type <code>reveal</code> with no arguments, it prints a helpful usage statement:

<blockquote>
<code>
Usage: /Users/demo/bin/reveal ~/dir/file "/dir/dir/my file" file
</code>
</blockquote>

Of course since we went through all the trouble of putting <code>reveal</code> into our path, we can just type:

<blockquote>
<code>
reveal ~/dir/file "/dir/dir/my file" file
</code>
</blockquote>

Here it is in action:

<div style="text-align:center;"><img src="{{ page.asset_root }}lt4-reveal-in-action.png" alt="lt4-reveal-in-action.png" border="0" width="700" height="528" /></div>

<h2>Wrap-up</h2>

So there we have it - Terminal and Finder peacefully co-existing.  What a wonderful world.  If you have more suggestions about integrating these fine pieces of software, or a question, feel free to comment.  Feedback is always welcome.