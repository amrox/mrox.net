---
kind: article
layout: post
created_at: 2008-11-30 19:54:00 -04:00
title: Learning the Terminal on the Mac - Introduction and Moving Around
existing_url: http://mrox.net/blog/2008/06/30/learning-the-terminal-on-the-mac-introduction-and-moving-around/
asset_root: /assets/2008-06-30-learning-the-terminal-on-the-mac-introduction-and-moving-around/

---

The is the first of hopefully several short articles describing the basics of using the terminal in Mac OS X.  This is aimed at absolute beginners who have never used a command line interface before.  The first few installments are likely to be very screenshot heavy, because the command line has a rather steep learning curve.  Every little bit helps.

First, a bit of motivation.  If you have no interest in learning to use the terminal, then I probably can't convince you otherwise.  However, if you are slightly curious, here are a few reasons why it might be worthwhile (and fun!) to start tinkering with it.

The terminal...

 * can be faster for some tasks.
 * exposes advanced Mac OS features.
 * opens up access to a wealth of open source software.
 * lets you transfer your skills to other Unix-like operating systems.

Now, just a little bit of terminology before we get started.

Terminal.app, often referred to as just the terminal, is a Mac OS application that wraps an interactive command line interface to your computer called the *shell*.  This article and the ones that follow will really be focusing on the shell, and not Terminal.app itself.  These same lessons should apply if you are using another terminal application, such as <a href="http://iterm.sourceforge.net/">iTerm</a> or the one built in to <a href="http://www.panic.com/coda/">Coda</a>.  Much of the information can also be transferred to other Unix like operating systems, but we will be using some Mac specific commands as well.

## Moving Around

If you haven't already, open Terminal.app.  it's in the Utilities folder inside your Applications folder, or just type "Terminal" into SpotLight. Once it launches, you should see a window like the one below.  I have annotated that section called the *prompt*.  This is where we type commands for the shell to interpret.  The information in the prompt itself isn't relevant quite yet so don't worry about it.

<img src="{{ page.asset_root }}terminal-prompt.jpg" border="0" alt="terminal-prompt.jpg" width="585" height="447" />

First, let's determine where we are.  Similar to Finder, your shell is always focused on a directory.  This is called the *current working directory*, or just *current directory* or *working directory*.  There is a command `pwd` that prints the working directory (hence its name: **p**rint **w**orking **d**irectory).  Type `pwd` at the prompt and press `<return>`.

<img src="{{ page.asset_root }}tt1-02-pwd.png" border="0" alt="tt1_02-pwd.png" width="585" height="447" />

My account name is "demo", so the shell started in my home directory, which is "/Users/demo".

Now that we know where we are, let's see what else is here.  The command to list the contents of a directory is `ls` (think "list").  This is also one of the most commonly used commands.  Type `ls` at the prompt and you should see a list of the contents of your home directory.  My "demo" account looks like this:

<img src="{{ page.asset_root }}tt1-03-ls.png" border="0" alt="tt1_03-ls.png" width="585" height="447" />

For the sake of completeness, this is how my home directory looks in Finder.  All the same folders are there.

<img src="{{ page.asset_root }}tt1-04-finder.png" border="0" alt="tt1_04-finder.png" width="830" height="516" />

Let's move into our Documents folder and see what's there.  We will use another extremely common shell command `cd` to change the current directory.

The `cd` command takes the directory to change to as an *argument*.  Arguments follow the command and are separated by spaces.

Type `cd Documents` and press `<enter>`.  Make sure you capitalize `Documents`.  The shell is case-sensitive.

<img src="{{ page.asset_root }}tt1-05-cd.png" border="0" alt="tt1_05-cd.png" width="585" height="447" />

<a name="prompt"> </a> <!-- the space between the open and close tags is required for proper md parsing -->

You may have noticed that the prompt changed.  The default prompt looks fairly cryptic at first, but it's actually very simple.  The first part before the colon is the computer name or *hostname*.  It may seem like this is superfluous information - of course you are on your computer.  But as we'll see in future articles, this will come in handy.  The second portion is the name of the current working directory.  Next, we have our current user name, which again will be more useful in the future.  The final `$` is standard in many shell prompts.  It denotes that you are a standard user, as opposed to a *root* or *super* user, in which case you would see a `#` instead.

<img src="{{ page.asset_root }}tt1-05a-prompt.jpg" border="0" alt="tt1_05a-prompt.jpg" width="585" height="447" />

You probably have a lot more than just one file in your Documents folder, so the contents will scroll off window.  This is fine for now.  We'll talk about controlling pagination and output flow soon.

So now we can see all the names of the files, but nothing else.  Let's say we want to see more information akin to the list view in Finder.  We're particularly interested in the size of the files.

In addition to arguments, many commands also take *options*.  They are usually prefixed by the `-` character, but it can vary.  I happen to know that `ls` has an `l` option that prints out files in long format.  Type `ls -l`.

<img src="{{ page.asset_root }}tt1-07a-lsl.jpg" border="0" alt="tt1_07a-lsl.jpg" width="585" height="447" />

Now we see several pieces of metadata associated with our files.  There is some permissions information, the file size in bytes, the file's creation date, and finally the filename.  File size in bytes isn't very intuitive to most people.  We tend to think about in kilobytes (K), megabytes (M) and gigabytes (G).  Fortunately there is another option, `h`,  that makes the output of `ls` a little more human-readable.

<img src="{{ page.asset_root }}tt1-08-lslh1.png" border="0" alt="tt1_08-lslh.png" width="585" height="447" />

That's better.  So now let's say we want to open a Document.  We can do this from the shell with the `open` command.  Type:

    open "About Stacks.pdf"

The quotes are important (we'll learn why later), and again don't forget about the case sensitivity.  If you don't have "About Stacks.pdf", you can pick another file.  The file will open up in your default application for that kind of file.

<img src="{{ page.asset_root }}tt1-09-open.png" border="0" alt="tt1_09-open.png" width="585" height="447" />

Let's go back to our home directory.  A quick way to do this is just type `cd` with no arguments.

<img src="{{ page.asset_root }}tt1-10-lshome.png" border="0" alt="tt1_10-lshome.png" width="585" height="447" />

By now you now may be a bit curious about other options for the `ls` command.  (If you're not, just pretend).

The `man` command lets us view the manual or *man page* for almost any command.  To display the manual for `ls` just type: `man ls`

<img src="{{ page.asset_root }}tt1-11-manls1.png" border="0" alt="tt1_11-manls1.png" width="585" height="447" />

Use the up and down arrow keys to scroll the page, or space to advance a whole page at a time.  Browse around for a bit.  When you're done, type `q` to quit.

Colorized output sounds interesting.  Let's give that a try.

<img src="{{ page.asset_root }}tt1-12-manls2.jpg" border="0" alt="tt1_12-manls2.jpg" width="585" height="447" />

<img src="{{ page.asset_root }}tt1-13-lsg.png" border="0" alt="tt1_13-lsG.png" width="585" height="447" />

Hooray, pretty colors.  By default, it will color directories in blue and executable files in green.  This is of course is all configurable.  See the man page for more information.

## Completion
<a name="completion"> </a>

Up to now we've working with relatively simple paths and filenames.  However, you may already feel that typing out the full names of files seems somewhat onerous.  There is a shortcut known as *tab completion* that greatly improves path entry at the command line.  It is actually a fairly simple technique, so don't let the seemingly long description that follows scare you.  Read over the demo scenario, watch the demo movie that shows the steps in action, and then try it out for yourself.

To illustrate how tab completion works, we'll set up a simple scenario.  We want to view all the custom Preference Panes that are installed for all users of the system.  However, we're not exactly sure where they are stored.  We'll use the `ls` command and tab completion to efficiently explore the file system.

At the prompt type the following, but don't press `<enter>`

    ls

Now press the `<tab>` key once.  Nothing should happen.  This is because it needs more information before it can complete the next part of the path for you.  To see all the possibilities, press the `<tab>` key a second time.

You should see a list of all the files and directories in top-level or root directory of your hard disk.  The Library folder seems like a likely place for the Preference Panes to live.

You've already typed `ls /`, and you shouldn't have pressed the `<enter>` key yet.  Type a `L` but still don't press `<enter>`.  You should have the following on the prompt:

    $ ls /L

Now press the tab key once.  Nothing should happen.  This is because it needs more information before it can complete the next part of the path for you.  To see all the possibilities, press the  key a second time.

You should see a list of all the files and directories in top-level or root directory of your hard disk.  The Library folder seems like a likely place for the Preference Panes to live.

You've already typed `ls /`, and you shouldn't have pressed the  key yet.  Type a `L` but still don't press `<enter>`.  You should have the following on the prompt:

    $ ls /L

Now press the `<tab>` key.  If you haven't any other directories in the root of your disk that begin with capital L, the shell should automatically fill in "Library" for you.  Magic!

You prompt should now look like this:

    $ ls /Library/

Press `<tab>` twice (still no `<enter>`) to show the contents of the "/Library" directory.

The "PreferencePanes" directory looks very promising.  Let's see what's inside.

Type `Pref` (the first few letters of "PreferencePanes") and press `<tab>` to let the shell complete the rest.  Again, don't press `<enter>` yet. The shell should complete your path so it looks like:

    $ ls /Library/Preference

But notice that the path now says "Preference" and we want "PreferencePanes".  The shell could not complete the path fully because it had more than one match for a folder that begins with "Preference".  To see all the possible matches, press the `<tab>` key.

We know see that it has matched "Preferences" and "PreferencePanes".  We want "PreferencePanes".  In order for the shell to complete it for us, we need to give it  the next letter of the path we want.  So we type `P` and press `<tab>` again.

Now our prompt should look like:

    $ ls /Library/PreferencePanes/

This is the path we want.  Now, finally, press `<enter>` to view the contents.  I have the Growl and Flip4Mac Preference Panes installed for all users.  Yay.

Here is a <a href="tt1-tab-completion-demo.mov">demo movie</a> of the same process.

<object classid="clsid:02BF25D5-8C17-4B23-BC80-D3488ABDDC6B" width="889" height="443" codebase="http://www.apple.com/qtactivex/qtplugin.cab">
        <param name="src" value="{{ page.asset_root }}tt1-tab-completion-demo.mov"/>
        <param name="autoplay" value="false"/>
        <param name="controller" value="true"/>
        <param name="loop" value="false"/>
		<embed src="{{ page.asset_root }}tt1-tab-completion-demo.mov" width="889" height="443" autoplay="false" controller="true" loop="false" pluginspage="http://www.apple.com/quicktime/download/">
		</embed>
</object>

That may have seemed like a lot of work, but tab completion is really a huge time saver.  Experiment with it whenever you are typing a path.  You'll soon find that it becomes fairly natural and saves a ton of keystrokes.

One final note on tab completion.  You may notice that it sometimes puts weird slashes in the filenames.  Take our "About Stacks.pdf" file as an example.

<img src="{{ page.asset_root }}tt1-14-space-escape.jpg" border="0" alt="tt1_14-space_escape.jpg" width="585" height="447" />

The shell is doing what is known as *escaping*.  We'll touch on this much more in the future, but basically it's a way to tell the shell what you really mean.  In this case, our file, "About Stacks.pdf" has a space in the name.  Remember earlier we said that arguments are separated by spaces.  In order to tell the shell to treat the name of the file as one, we need to *escape* the space.  This is why we put the name of the file in "" (double quotes) earlier.

## Wrap-up

Up to now we've gotten a little background about the terminal and the program that runs inside it, the shell.  You should now be able to move around with some efficiency, list files, and open them up.  There is still a lot more to learn, but this is a solid start.  The next article will cover some more common shell commands.  In the meantime, feel free to explore, and if you have any comments or suggestions for future articles, please leave a comment!