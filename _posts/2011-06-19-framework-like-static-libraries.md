---
kind: article
layout: post
title: "Framework-like Static Libraries"
asset_root: /assets/workspace-layout/
comments: true
---

Apple Frameworks bundle the headers required to user a library along with the library itself, which makes them easy to use in an application. Integration of built-in frameworks is a two step process:

1. Add the framework to the "Link with Binary Libraries" phase
2. Import the framework in your source (`#import <Framework/Framework.h>`)

However, frameworks are not (officially * ) available on iOS, so we are left with traditional static libraries. Static libraries are a little more cumbersome to use because there is no standard or convention for including their public headers. Usually you must either:

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

<script src="https://gist.github.com/972630.js?file=PortableStaticLibrary.xcconfig">
    
</script>

Ive shared two stand-alone libraries which use this configuration:

- [AMFoundation](https://github.com/amrox/AMFoundation)
- [AMSpringboard](https://github.com/amrox/AMSpringboard)

and a project which uses both libraries:

- [AMSpringboardDemo](https://github.com/amrox/AMSpringboardDemo)

Importing `AMFoundation` from the `AMSpringboard` library, or the `AMSpringboardDemo` app is as simple a system framework:

      #import <AMSpringboard/AMSpringboard.h>

## Discussion

After I did my initial work I discovered a [blog post by Jonah Williams](http://blog.carbonfive.com/2011/04/04/using-open-source-static-libraries-in-xcode-4/) which has agreat description of the overall problem, but provides a slightly different solution.

A big part of my inspiration for this was Python's[virtualenv](http://pypi.python.org/pypi/virtualenv) (and to a lesser extent [pip](http://www.pip-installer.org/en/latest/index.html)). An Xcode Workspace is analagous to isolated environment created by `virtualenv`, expect that modules (Projects) within the shared space are not easily imported. If static libraries were "packaged" like frameworks, we get a lot closer to having nice, portable sandbox environments for iOS projects. 

I would love a a [PyPi](http://pypi.python.org/pypi) or [gem](http://rubygems.org/)-like system for iOS code and I think better library packaging is needed to get there.


- - - 

\* *[Karl Stenerud](http://github.com/kstenerud) has done some great work on  creating "fake" [static frameworks for iOS](https://github.com/kstenerud/iOS-Universal-Framework), but they require installing additional templates or patching Xcode itself. I didn't want to require the users of my library to make changes to their system.*
