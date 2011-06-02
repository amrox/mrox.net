---
kind: article
layout: post
title: "An Xcode Workspace Layout for Better Dependency Management"
asset_root: /assets/workspace-layout/
---

It's relatively easy to add static libraries to an iOS app. However it's sometimes a pain to get the header search paths right, especially if the dependencies are non-trivial.

I've been playing around with project layout and library "packaging" style that takes some of the pain out managing dependencies and headers. It is far from perfect, but has worked well in a few projects so far.

In short, this technique allows you to:

- Organize your code into smaller, more portable static libraries
- Do less configuration of build settings, particular header search paths.


## Motivation

I was building a re-usable iOS springboard view called [AMSpringboard](https://github.com/amrox/AMSpringboard). I wanted to make it slim and easy to drop into projects, so I packaged it as a iOS static library. I also wanted to use some utilities I packaged in an important-sounding library called [AMFoundation](https://github.com/amrox/AMFoundation). Finally, I wanted to provide an application to demo AMSpringboard. The demo application *also* used parts of AMFoundation directly.

The logical dependency graph looks something like this: 

<img src="{{ page.asset_root }}dep-graph.png">

It wasn't clear how best to &quot;package&quot; AMSpringboard and AMSpringboardDemo.

My options were:

- include a single copy of AMFoundation for both dependencies to use. This is a pain because if I update the main AMSpringboard project, I have to remember go update any copies in my other projects as well.
- add AMFoundation as a *submodule* of AMSpringboard and AMSprinboardDemo, but it seemed messy to have two versions of the same code in the project.
- provide manual instructions on how to set up AMSpringboard an all its dependencies. I wanted to avoid this because I wanted to provide a &quot;clone and run&quot; demo of AMSpringboard.

I didn't like any of the options. What I really wanted was something akin to python's [virtualenv](http://pypi.python.org/pypi/virtualenv) -- a workspace where dependencies can be easily installed and references.

I'm still not quite sure how Xcode Workspaces are *supposed* to be used, but it seemed like I could use it like a sandbox similiar to virtualenv.

## Step 1: Workspace Layout

First, I laid out my workspace in a folder, and included dependencies as submodules within the folder.

<img src="{{ page.asset_root }}layout-on-disk2.png">

In Xcode, the workspace looks like this:

<img src="{{ page.asset_root }}layout-in-xcode.png">

## Step 2: Scheme Configuration

Next, we have to do a little Scheme configuration. This seems like it would be obviated by the 'Find Implicit Dependencies' option, but I couldn't get it to work.

### Add Dependencies to the Scheme


- Click the `+` button at the bottom of the Targets list:
<img src="{{ page.asset_root }}scheme-config-1.png">

- Select the appropriate dependency and click `Add`:
<img src="{{ page.asset_root }}scheme-config-2.png">


### Rearrange to satisfy dependencies

Rearrange the targets in the list so that the targets that are dependencies of other targets are built first.

<img src="{{ page.asset_root }}scheme-config-3.png">


### Finally

- Turn off 'Parellelize Build'. Otherwise dependencies may not be built in the correct order.
- (I left 'Find Implict Dependencies' on, even though I couldn't get it to work)

The final scheme configuration should look like this:

<img src="{{ page.asset_root }}scheme-config-4.png">


## Build Configuration


We're almost done. Normally you'd have to edit the Header Search paths (`HEADER_SEARCH_PATHS` or `USER_HEADER_SEARCH_PATHS`) for each target that required the headers. Fortunately, headers marked Public are automatically copied to a shared location when building a Workshop. Unfortunately, that shared location isn't in the standard header search paths, so consumers of the library can't find it.

We can fix this by setting the `PUBLIC_HEADERS_FOLDER_PATH` variable in the library's build settings

    PUBLIC_HEADERS_FOLDER_PATH = include/$(PROJECT_NAME)

Now consumers of the library can just import it like this:

    #import <AMFoundation/AMFoundation.h>

without needing to mess around with any header search paths. This works great for building and running, but archiving work work. To fix archiving, I set the `INSTALL_PATH` as follows:

    INSTALL_PATH = /../BuildProductsPath/$(CONFIGURATION)$(EFFECTIVE_PLATFORM_NAME)/

I bundled these two settings into a file called `PortableStaticLibrary.xcconfig`:

<script src="https://gist.github.com/972630.js?file=PortableStaticLibrary.xcconfig"></script>

Both [AMFoundation](https://github.com/amrox/AMFoundation) and [AMSpringboard](https://github.com/amrox/AMSpringboard) use this configuration.

## Wrap Up

Now I have two stand-alone libraries, with their own repositories:

- [AMFoundation](https://github.com/amrox/AMFoundation)
- [AMSpringboard](https://github.com/amrox/AMSpringboard)

and a project which uses both libraries:

- [AMSpringboardDemo](https://github.com/amrox/AMSpringboardDemo)

There are no copies of any code. All code is tracked within it's own repository and assembled using git submodules.

You can see it in action in my [AMSpringboardDemo](https://github.com/amrox/AMSpringboardDemo) project.





