---
layout: post
title: "Serving local libraries during the development of angular application"
date: 2018-07-18 20:00:00 +0700
author: Igor Milla
sitemap: false
keywords: "angular webpack npm link ng-packagr"
description: "Using local angular libraries while serving local angular application"
---

Recently I was working on a project, which consisted of multiple separate angular apps, with a handful of
angular libraries of shared code. The project used angular 5, which doesn't have any native tools for generating libraries, [yet][1]{:target="_blank"}. 
So, we had to use [ng-packagr][2]{:target="_blank"} to generate our custom angular libraries. 

The main problem with such project architecture is that it makes local development into a constant juggling of multiple
terminal windows, where you try to build one project just to pull it immediately into a different one. 

I've seen it done in many different ways. Some of them are worth than another, some are better.

#### Bad

```
    ~ $ cd lib                   # in a library
~/lib $ vi code.js               # make code changes
~/lib $ npm run build            # generate a new build
~/lib $ git commit -am 'potato'  # commit changes
~/lib $ npm version 1.0.1        # increment version following semver
~/lib $ npm publish              # publish to a package manager
~/lib $ cd ../app                # in an app
~/app $ npm install -S lib@1.0.1 # install a new version of a lib
~/app $ ng serve                 # preview the changes
```
 
 This will result in the publishing of a new package version every time we would want to test a code. 
 It's a terrible idea, even if you use pre-release versions there is still no justification 
 in polluting your package manager with a ton of dev versions of questionable stability.
 
#### Better

```
    ~ $ cd lib                     # in a library
~/lib $ vi code.js                 # make code changes
~/lib $ npm run build              # generate a new build
~/lib $ npm pack                   # pack a new version of a lib
~/lib $ cd ../app                  # in an app
~/app $ npm uninstall -S lib       # uninstall an old version of a lib
~/app $ npm install ../lib/lib.tar # install lib from local .tar file
~/app $ ng serve                   # preview the changes
```
 **Pros**: You don't publish a new version of a library every time you need to test something. 
 Another plus is that you don't even need to commit any of your code changes.
 
 **Cons**: You need to `pack` and reinstall a new library version every time you make any changes

#### Good

```
    ~ $ cd ../lib                    # in a library
~/lib $ npm link                     # make code changes
~/lib $ npm run build                # generate a new build
~/lib $ cd ../app                    # in an app
~/app $ npm link lib                 # link lib package to an app
~/app $ ng serve --preserve-symlinks # run a livereload server
```

The main benefit of this option is that you will be able to fully benefit from a livereload server, 
as you will be able to focus on developing a library, and see changes in real time in your app running live in a browser.

##### Note

Currently, there is an [open bug][3]{:target="_blank"} in a `watchpack` package, used by `ng serve`. 
This bug prevents livereload to pick up changes in symlinked directories in node_modules. 
As a workaround, you just need to edit `node_modules\watchpack\lib\DirectoryWatcher.js` file in your app and 
change `followSymlinks` from `false` to `true`.

[1]: https://github.com/angular/angular-cli/wiki/stories-create-library
[2]: https://github.com/dherges/ng-packagr
[3]: https://github.com/webpack/watchpack/issues/61
