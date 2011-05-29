---
kind: article
layout: post
date: 2008-11-16 11:21:00 -04:00
title: Adding Debug-only Preferences in iPhone Applications
existing_url: http://mrox.net/blog/2008/11/16/adding-debug-only-preferences-in-iphone-applications/
asset_root: /assets/2008-11-16-adding-debug-only-preferences-in-iphone-applications/
---

Recently I bumped into an iPhone application configuration/deployment problem, and thought someone else might benefit from my findings.

### Motivation and Goals

Here's the basic scenario:  I wanted to expose certain options in my iPhone application that were only available in Debug builds.  One choice would be to add the ability to change the options in the application itself, and use the usual conditional compilation (`#ifdef DEBUG`…) techniques to hide or expose them based on build configuration.

However, this would require adding a whole bunch of custom UI in my application, which didn't really seem appealing.  The application was already using the standard iPhone application Setting system, which is driven by simple plists.  I thought to myself, boy it would be nice if my Debug-only options could be automagically added to Settings bundle at build time.

For example, the standard settings pane would look like this:

<img src="{{ page.asset_root }}nodebug.png" alt="NoDebug.png" border="0" width="360" height="654" />

And debug-only options would be added automatically, to produce this:

<img src="{{ page.asset_root }}hasdebug.png" alt="HasDebug.png" border="0" width="360" height="654" />
<img src="{{ page.asset_root }}debugpane2.png" alt="DebugPane2.png" border="0" width="360" height="654" />

I'm a believer in the <a href="http://en.wikipedia.org/wiki/Don't_repeat_yourself">DRY</a> principle, so I wanted to avoid duplicating and information at all possible.  Also I wanted to "set it and forget it" and not require any manual build steps.

After some experimentation, trial, and error, I arrived at the following solution:

 * **Store Debug settings in a separate child pane.** The Settings system allows for child panes of the root pane.  To keep the main settings and debug settings separate, we keep them on different panes.
 * **Add an reference to the child pane from the root pane at build time.**  To keep the main root settings clean, we'll inject the reference to the debug clild pane at build time.


### Create the Property List

First, add a Settings Bundle to your project if you haven't already.  Choose `File -> New File… -> Settings -> Settings Bundle`.

Next we need to add a second plist for our Debug settings.  Xcode doesn't allow you to create a new plist inside the Settings bundle (or any bundle for that matter), so we'll have to create our Debug.plist by some other means.  If you're familiar with the shell, the easiest way is to navigate to `Settings.bundle` inside your project and simply duplicate the main Root settings to create Debug settings.

    cp Settings.bundle/Root.plist Settings.bundle/Debug.plist

If you prefer to use Finder, navigate to Settings.bundle and then right- or control-click and choose *Show Package Contents*.  Then option-drag to duplicate `Root.plist` and rename it to `Debug.plist`.

Once the file has been created, collapse and expand the Settings bundle in your projects Groups & Files section to refresh it's contents.  It should look like this:

<img src="{{ page.asset_root }}adddebugsettingsexample-debugplist.png" alt="AddDebugSettingsExample - Debug.plist .png" border="0" width="250" height="272" />

Note, you can name the child settings plist something other than "Debug" if you want.  We'll see how to do that in the next section.

### The Injection Script

The purpose of the injection script is to add a child pane option to an existing settings plist file.  It uses the built in <a href="http://developer.apple.com/documentation/Darwin/Reference/ManPages/man8/PlistBuddy.8.html">PlistBuddy</a> utility to do all the real work.

The `addDebugSettingsChild.sh` takes two arguments.  The first is of course that path to the target plist file.  The second is the name of the new child pane, or 'Debug' if unspecified.

Here's the script:


    #!/bin/sh
    
    # addDebugSettingsChild.sh
    #
    # Simple script to inject a Debug menu in an iPhone Settings plist.
    #
    # created 10.15.2008 by Andy Mroczkowski, mrox.net
    
    THIS="`basename $0`"
    PLISTBUDDY="/usr/libexec/PlistBuddy -x"
    
    set -e
    
    if [ -z "$1" ]; then
    	echo "Usage:"
    	echo "   $THIS plist_file [child_pane_name]"
    	echo "   - If unspecified, child_pane_name is 'Debug'"
    	exit 1
    fi
    
    if [ ! -e "$1" ]; then
    	echo "[$THIS] file not found: '$1'"
    	exit 2
    fi
    
    if [ ! -z "$2" ]; then
    	CHILD_PANE_NAME="$2"
    else
    	CHILD_PANE_NAME="Debug"
    fi
    
    TARGET="$1"
    echo "[$THIS] adding '$CHILD_PANE_NAME' child to: $TARGET"
    
    $PLISTBUDDY -c "Add PreferenceSpecifiers:0 dict" "$TARGET"
    $PLISTBUDDY -c "Add PreferenceSpecifiers:0:Type string 'PSGroupSpecifier'" "$TARGET"
    
    $PLISTBUDDY -c "Add PreferenceSpecifiers:1 dict" "$TARGET"
    $PLISTBUDDY -c "Add PreferenceSpecifiers:1:Type string 'PSChildPaneSpecifier'" "$TARGET"
    $PLISTBUDDY -c "Add PreferenceSpecifiers:1:Title string '$CHILD_PANE_NAME Settings'" "$TARGET"
    $PLISTBUDDY -c "Add PreferenceSpecifiers:1:File string '$CHILD_PANE_NAME'" "$TARGET"



<a href="{{ page.asset_root }}addDebugSettingsChild.sh">Download</a>

### Creating the Build Step

We've created a Debug settings child pane in our settings bundle, and a script to add an option to access our child pane from the root pane.  Now we just have to tie the pieces together in Xcode's build settings.

Copy `addDebugSettingsChild.sh` to your project's folder.  You don't need to add it to the project itself.  Next add a new "Run Script Build Phase" to the target for your application.  Order it so be comes after the "Copy Bundle Resources" phase, and name it something more descriptive if you'd like.  I called mine "Run Debug Settings Script".

<img src="{{ page.asset_root }}debugplist-adddebugsettingsexample-2.png" alt="Debug.plist - AddDebugSettingsExample-2.png" border="0" width="240" height="104" />

Next we have to make the our new Run Script phase actually do something.  Basically, we need to call the `addDebugSettingsChild.sh` if we're building with the "Debug" configuration, and also set up the input and out files so Xcode knows when to run the script and when not to.  Let's set up the input and output files first.

Add the path to your Root settings plist as an input file.  If you used the default names and locations, this should be:

    $(SRCROOT)/Settings.bundle/Root.plist

The file we will be altering will be in the built products area, not the project source root, so the output file should be:

    $(BUILT_PRODUCTS_DIR)/$(UNLOCALIZED_RESOURCES_FOLDER_PATH)/Settings.bundle/Root.plist

Finally create the body of the script.  I only want to add my extra settings if I'm building the "Debug" configuration, so my script looks like this:

    if [ "$CONFIGURATION" == "Debug" ]; then
	    sh addDebugSettingsChild.sh "$SCRIPT_OUTPUT_FILE_0"
    fi

You may notice that the script's input is specified as the output file.  This is not a mistake.  The `addDebugSettingsChild.sh` script modifies the specified plist in place, and we want to alter the one in our built-products area, not the source tree.

Again it's important to specify the input and output files.  If you don't, Xcode may run the script on every build, which will result in repeated Debug child pane choices in our main settings plist.

Once you're done, it should look like this:

<img src="{{ page.asset_root }}run-script-phase-for-adddebugsettingsexample-info.png" alt="Run Script Phase for “AddDebugSettingsExample” Info.png" border="0" width="630" height="654" />

### Conclusion

And that's it.  You can now add edit the Root and Debug plists and they will be updated appropriately and everything will be  happy.  Well, mostly happy (see Caveats).  I hope you found if useful.  If not, I'm sorry.  Here's some pictures of <a href="http://www.flickr.com/photos/87258185@N00/sets/72157603724213121/">Robocop on a Unicorn</a> to cheer you up.

#### Sample Project

You can download a sample project with all the above steps already completed <a href="{{ page.asset_root }}AddDebugSettingsExample.zip">here</a>.

#### Caveats and Future Work

 * **Be careful when switching configurations and using Build and Run.**  The products in the build folder for each configuration should always be correct, but Xcode won't necessarily re-install the right Setting bundle if it doesn't detect a change.  The workaround is to just build clean and re-build when switching from Debug to Release or vice versa.  Any suggestions on how to improve this are welcome.
 * **Child pane options are always injected at the top.**  I'd prefer if I could add the "Debug Settings" option at the end of the main preferences, but I could not find a simple way to append an element to the end of an array with PlistBuddy.  I considered using PlistBuddy to loop through the array to determine the size, and then use that to append it to the end, but I didn't want to add all that complexity to the script for a minor issue just yet.
 * **Debug.plist still exists, but is inaccessible, in Release builds.** This method does not prevent the installation of the `Debug.plist`, but there will be no way to get to it, since it's not referenced as a child from the root plist.  If this bothers you, you'll probably have to set up another script to remove `Debug.plist`.
 * **What if I'm not using a Settings bundle in my Release builds?** This technique assumes that you're already using a Settings bundle to your application, and you want to add extra stuff to it based on build configuration.  If you'd like to *only* have Settings bundle for Debug builds, this isn't going to work for you, though hopefully some of the information can help you get started on a similar solution.
