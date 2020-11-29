+++
author = "J. Austin Hughey"
categories = ["xcode", "macos"]
date = 2020-06-11T08:17:37Z
description = ""
draft = false
slug = "ld-library-not-found-for-lssl-after-updating-xcode-2019"
tags = ["xcode", "macos"]
title = "ld: library not found for -lssl after updating XCode (2019)"

+++


I recently updated XCode on my MacOS Mojave hackintosh, and to my surprise thereafter, was unable to install a version of the MySQL2 gem for a project I was working on. The solution ultimately was to install a package I didn't know existed.

When upgrading to a slightly newer version of Ruby for a (somewhat legacy) project I was working on, I had to re-bundle to reinstall gems. One of those was `mysql2`. But I had just updated XCode and unbeknownst to me, that update had (re)moved multiple header files needed to compile all sorts of things, including this gem.

So when I tried `bundle`, eventually I got this:

```
Fetching mysql2 0.3.21
Installing mysql2 0.3.21 with native extensions
Gem::Ext::BuildError: ERROR: Failed to build gem native extension.

    current directory: /Users/jah/.rbenv/versions/2.3.8/lib/ruby/gems/2.3.0/gems/mysql2-0.3.21/ext/mysql2
/Users/jah/.rbenv/versions/2.3.8/bin/ruby -r ./siteconf20190326-24430-11b9ea0.rb extconf.rb
checking for ruby/thread.h... yes
checking for rb_thread_call_without_gvl() in ruby/thread.h... yes
checking for rb_thread_blocking_region()... no
checking for rb_wait_for_single_fd()... yes
checking for rb_hash_dup()... yes
checking for rb_intern3()... yes
-----
Using mysql_config at /Users/jah/.brew/bin/mysql_config
-----
checking for mysql.h... yes
checking for errmsg.h... yes
checking for mysqld_error.h... yes
-----
Don't know how to set rpath on your system, if MySQL libraries are not in path mysql2 may not load
-----
-----
Setting libpath to /Users/jah/.brew/Cellar/mysql@5.7/5.7.25/lib
-----
creating Makefile

To see why this extension failed to compile, please check the mkmf.log which can be found here:

  /Users/jah/.rbenv/versions/2.3.8/lib/ruby/gems/2.3.0/extensions/x86_64-darwin-18/2.3.0-static/mysql2-0.3.21/mkmf.log

current directory: /Users/jah/.rbenv/versions/2.3.8/lib/ruby/gems/2.3.0/gems/mysql2-0.3.21/ext/mysql2
make "DESTDIR=" clean

current directory: /Users/jah/.rbenv/versions/2.3.8/lib/ruby/gems/2.3.0/gems/mysql2-0.3.21/ext/mysql2
make "DESTDIR="
compiling client.c
compiling infile.c
compiling mysql2_ext.c
compiling result.c
linking shared-object mysql2/mysql2.bundle
ld: library not found for -lssl
clang: error: linker command failed with exit code 1 (use -v to see invocation)
make: *** [mysql2.bundle] Error 1

make failed, exit code 2

Gem files will remain installed in /Users/jah/.rbenv/versions/2.3.8/lib/ruby/gems/2.3.0/gems/mysql2-0.3.21 for inspection.
Results logged to /Users/jah/.rbenv/versions/2.3.8/lib/ruby/gems/2.3.0/extensions/x86_64-darwin-18/2.3.0-static/mysql2-0.3.21/gem_make.out

An error occurred while installing mysql2 (0.3.21), and Bundler cannot continue.

```

Well, that's rather cryptic, isnt' it? So why did it fail to compile?

When looking at `mkmf.log` as suggested, I found out why:

```
ld: library not found for -lssl

```

Well, that's not great, is it?

> So how do you fix that?

Run this in terminal:

`open /Library/Developer/CommandLineTools/Packages`

Double click `macOS_SDK_headers_for_macOS_10.14.pkg` and install, then re-try installing your gem or recompiling whatever was failing before.

Might work for multiple missing libraries that `ld` complains about not finding.

