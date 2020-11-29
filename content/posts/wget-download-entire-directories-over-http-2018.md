+++
author = "J. Austin Hughey"
date = 2020-06-11T08:14:40Z
description = ""
draft = false
slug = "wget-download-entire-directories-over-http-2018"
title = "wget: Download entire directories over HTTP (2018)"

+++


Say you want to download everything in a certian directory that's shown via HTTP (like [nginx directory listing](http://nginxlibrary.com/enable-directory-listing/)). `wget` can help you get the job done.

For example, let's say you have `http://somehost/ISO/`, and in that directory are subdirectories for `linux`, `bsd`, and `windows`.

```
/ISO
  - /linux
  - /bsd
  - /windows
  - index.html

```

And let's say that inside `linux` are several Linux distribution ISOs, same with `bsd` and `windows` contains ISOs for multiple versions of Windows.

Using `wget`, we can tell the computer to download everything under each subdirectory and preserve the same directory organization.

```
$ wget -np -m --reject html,tmp "http://somehost/ISO"

```

This command will recursively enter each directory (`/linux /bsd /windows`) and download every file there UNLESS that file ends in `.tmp` or `.html`.

**-np** tells wget "no parent". This means wget will stick ONLY to the `/ISO` directory in this case. Otherwise, it would recurse up to the root directory (`/`) and download all subdirectories and their contents along with `/ISO`. We don't want that here, _just_ the stuff under `/ISO` so we tell `wget`, "no parent (directory)".

**-m** is the _"mirror"_ argument, telling `wget` to copy the file and directory structure, file names, recursive directories under the URL we give it later. This is what allows for downloading all the files and maintaining the organization structure that's reflected on the server.

**--reject html,tmp** tells `wget`, "hey if you see a file ending in `.tmp` or `.html`, _ignore it_ and don't bother writing it to the disk."

And finally, the last argument is the location to start downloading/mirroring from.

