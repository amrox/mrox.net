---
kind: article
layout: post
title: "Framework-like Static Libraries"
asset_root: /assets/workspace-layout/
---



Apple Frameworks bundle the headers required to user a library along with the library itself, which makes them easy to use in an application. Integration of built-in frameworks is a two step process:

1. Add the framework to the "Link with Binary Libraries" phase
2. Import the framework in your source (`#import <Framework/Framework.h>`)

However, frameworks are not (officially*) available on iOS, so we are left with traditional static libraries. Static libraries are a little more cumbersome to use because there is no standard or convention for including their public headers. Usually you must either:

 * Drag the headers into you Xcode project, or
 * Set the `HEADER_SEARCH_PATHS` or `USER_HEADER_SEARCH_PATHS` in your project.
 
While neither task is particularly difficult, it would be better if the extra step of adding the headers wasn't necessary. I wanted to make my library as easy to integrate framework, and the headers were the primary thing preventing this.

## Headers

Xcode allows headers to be declared as "Public" as part of the "Copy Headers" build phase. If you're using a Workspace, Xcode will "install" the public headers to the shared build location of the Workspace, instead of the system root. Unfortunately the path where the library's headers and installed by default does not match the path where all Projects search by default, preventing them from being imported easily.

We can fix the public header path by setting the `PUBLIC_HEADERS_FOLDER_PATH` variable in the library's build settings:

    PUBLIC_HEADERS_FOLDER_PATH = include/$(PROJECT_NAME)

Now headers from `MyLib` can be imported in other Projects in the Workspace with the familiar convention:

    #import <MyLib/MyLib.h>
    
No changes headers need to be added to the project and no search paths need to be altered. The headers are just "there".

## Archiving

The previous step will allow you to build and run the application, but the Archive build action will fail. There are actually two problems:

 * Xcode uses different header search paths for archiving that it does for regular builds. It won't find the `PUBLIC_HEADERS_FOLDER_PATH` we set above.
 * If Xcode *could* find the headers, it would treat the headers it found in the `PUBLIC_HEADERS_FOLDER_PATH` as build artifacts, and will create an `xcarchive` instead of the desired `ipa` file.
 
We can fix both problems by setting the `INSTALL_PATH` build variable:

    INSTALL_PATH = /../BuildProductsPath/$(CONFIGURATION)$(EFFECTIVE_PLATFORM_NAME)/

This moves the headers to a location in the default search path for archiving (which again is different than regular build), *and* moves the headers out of the `BUILT_PRODUCTS_DIR`, so Xcode will make an `ipa`.
 
## Putting it All Together

I bundled the build settings described above into a file called `PortableStaticLibrary.xcconfig`:

<script src="https://gist.github.com/972630.js?file=PortableStaticLibrary.xcconfig"></script>

I've shared two stand-alone libraries which use this configuration:

- [AMFoundation](https://github.com/amrox/AMFoundation)
- [AMSpringboard](https://github.com/amrox/AMSpringboard)

and a project which uses both libraries:

- [AMSpringboardDemo](https://github.com/amrox/AMSpringboardDemo)

Importing `AMFoundation` from the `AMSpringboard` library, or the `AMSpringboardDemo` app is as simple a system framework:

    #import <AMSpringboard/AMSpringboard.h>

## Discussion

After I did my initial work I discovered a [blog post by Jonah Williams](http://blog.carbonfive.com/2011/04/04/using-open-source-static-libraries-in-xcode-4/) which has agreat description of the overall problem, but provides a slightly different solution.

A big part of my inspiration for this was Python's[virtualenv](http://pypi.python.org/pypi/virtualenv) (and to a lesser extent [pip](http://www.pip-installer.org/en/latest/index.html)). An Xcode Workspace is analagous to isolated environment created by `virtualenv`, expect that modules (Projects) within the shared space are not easily imported. If static libraries were "packaged" like frameworks, we get a lot closer to having nice, portable sandbox environments for iOS and OS X projects. 

I would love a a [PyPi](http://pypi.python.org/pypi) or [gem](http://rubygems.org/)-like system for iOS code and I think better library packaging is needed to get there.


<hr/>

\* *[Karl Stenerud](http://github.com/kstenerud) has done some great work on  creating "fake" [static frameworks for iOS](), but they require installing additional templates or patching Xcode itself. I didn't want to require the users of my library to make changes to their system.*























$ cd MyWorkspace
$ pios install AMFoundation
$ open MyWorskpace.xcworkspace

( )













There are no copies of any code. All code is tracked within it's own repository and assembled using git submodules.

You can see it in action in my [AMSpringboardDemo](https://github.com/amrox/AMSpringboardDemo) project.




 
 putting the headers in a place where the archiving step will find them

Xcode uses different header search paths for archiving than it does for regular builds. If we did add the necessary path to our `HEADER_SEARCH_PATHS`, archiving *still* would not work. Xcode will headers treat the public headers it finds in the `BUILT_PRODUCTS_DIR` as archifacts, and create an `xcarchive` an `.ipa` or `.app`. We can kill two birds: 

  * moving the public headers out of the `BUILT_PRODUCTS_DIR`, so archiving will 



We can tell (or fool) Xcode by changing the libary's `INSTALL_PATH`:

    INSTALL_PATH = /../BuildProductsPath/$(CONFIGURATION)$(EFFECTIVE_PLATFORM_NAME)/

Setting `INSTALL_PATH` will cause Xcode to Build and Archive as you'd expect.





## Using a Library

Integrating a properly-configured library is now simple process:

Add the library's Xcode project to your workspace:






## Putting it All Together

I bundled the build settings described above into a file called `PortableStaticLibrary.xcconfig`:

<script src="https://gist.github.com/972630.js?file=PortableStaticLibrary.xcconfig"></script>


I have a [few](https://github.com/amrox/AMFoundation) [projects](https://github.com/amrox/AMSpringboard)



Apple Frameworks are easy to use and integrate. Integrating is a two step process:

 1. Add the framework to the "Link with Binary Libraries" phase
 2. Import the framework in your source




The import is also very clean and understandable:

    #import <UIKit/UIKit.h>

The process for static libraries is similar, but you must also add the library's headers to your project, either by dragging them in or setting your header search paths. 


Some good work has been done to fake static frameworks for iOS. I think these techniques are useful, but they a little out of mainstream. What if static libraries


    #import "MyLib.h"




    





    






I have been experimenting with project layout and "packaging" style that takes some of the pain out of integrating iOS static libraries. 

In short, this article describes how to:

- Organize your code into smaller, more portable static libraries.
- Greatly simplify the amount of configuration needed by "comsumers" of static libraries.
- Do less configuration of build settings, particularly header search paths.


## Motivation &amp; Goals

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

### Goals

- **Convention Over Configuration.** Reduce the amount of configuration by consumers of the library. 
- **Respect Encapsulation.** Easily import all public headers. Avoid all private headers.




## Workspace Layout

I laid out my workspace in a folder, and included dependencies as submodules within the folder.

<img src="{{ page.asset_root }}layout-on-disk2.png">

In Xcode, the workspace looks like this:

<img src="{{ page.asset_root }}layout-in-xcode.png">



## Library Configuration

Normally you'd have to edit the Header Search paths (`HEADER_SEARCH_PATHS` or `USER_HEADER_SEARCH_PATHS`) for each consumer of the library. This can make you build configuration messy and there is no ______________ 

### Headers

Xcode allows you to declare headers as "Public", and will copy them to a shared location. Unfortunetly the location Xcode uses by default doesn't make much sense for the general use case. We want to copy the public headers to our workspace's shared build location. We can fix the public header path by setting the `PUBLIC_HEADERS_FOLDER_PATH` variable in the library's build settings

    PUBLIC_HEADERS_FOLDER_PATH = include/$(PROJECT_NAME)

Now the public headers are copied to a common location, which all other targets will include automatically. The library's headers can now be imported by:

    #import <AMFoundation/AMFoundation.h>
    
with *no change* to the consumer's build configuration.

### Linking

Some additional flags are usually required to get Objective-C static libraries to link correctly. First, we need to add `-ObjC`

    OTHER_LDFLAGS = -ObjC

The `-ObjC` flag will correctly link in 


There is a long-standing bug with categories in static libaries. One common solution is to use the `-add_load` flag. However this flag often introduces a lot of other problems. Instead we can force a category to link correctly by declaring a dummy class in the category's implementation:

    @implementation NSString (MyStuff)
	// your methods here
	@end

	// Hack to force category to link correctly

	@interface Dummy_MyStringCategory
	@end

	@implementation Dummy_MyStringCategory
	@end

The name of the dummy class doesn't matter, as long as it's unique.

To make it even easier, my colleague [Karl Stenerud](https://github.com/kstenerud) wrote a [marco for creating a dummy class](https://github.com/kstenerud/Objective-Gems/blob/master/Objective-Gems/LoadableCategory.h). Example:

    @implementation NSString (MyStuff)
    // your methods here
    @end

    MAKE_CATEGORIES_LOADABLE(MyStringCategory)

### Archiving

The previous two steps will let you build and run the application, but you'll encounter problems Archiving. Xcode now sees the public headers as part of the final package so it will create an `xcarchive` instead of what we really want, an `.ipa` or `.app`. We can tell (or fool) Xcode by changing the libary's `INSTALL_PATH`:

    INSTALL_PATH = /../BuildProductsPath/$(CONFIGURATION)$(EFFECTIVE_PLATFORM_NAME)/

Setting `INSTALL_PATH` will cause Xcode to Build and Archive as you'd expect.

## Using a Library

Integrating a properly-configured library is now simple process:

Add the library's Xcode project to your workspace:






## Putting it All Together

I bundled the build settings described above into a file called `PortableStaticLibrary.xcconfig`:

<script src="https://gist.github.com/972630.js?file=PortableStaticLibrary.xcconfig"></script>

Both [AMFoundation](https://github.com/amrox/AMFoundation) and [AMSpringboard](https://github.com/amrox/AMSpringboard) use this configuration.

Now I have two stand-alone libraries, with their own repositories:

- [AMFoundation](https://github.com/amrox/AMFoundation)
- [AMSpringboard](https://github.com/amrox/AMSpringboard)

and a project which uses both libraries:

- [AMSpringboardDemo](https://github.com/amrox/AMSpringboardDemo)

There are no copies of any code. All code is tracked within it's own repository and assembled using git submodules.

You can see it in action in my [AMSpringboardDemo](https://github.com/amrox/AMSpringboardDemo) project.





