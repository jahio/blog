+++
author = "J. Austin Hughey"
categories = ["macos", "cli", "terminal", "tips"]
date = 2020-06-11T08:16:58Z
description = ""
draft = false
slug = "using-dd-to-create-os-installation-disks-usb-2019"
tags = ["macos", "cli", "terminal", "tips"]
title = "Using dd to Create OS Installation Disks (USB) (2019)"

+++


When creating OS installation media, a lot of people suggest using various third party tools, such as [Etcher](https://www.balena.io/etcher/) and others. However, it's fairly easy just using `dd` (Data Dump) on any Unix-like OS to do the same thing.

### Zero Out the Disk

Insert a USB stick and identify its appropriate name under `/dev/`. For MacOS, you can try `diskutil list` - just identify the disk that wasn't there before you inserted your USB stick. Then use `diskutil unmountDisk /dev/rdisk#` where `#` is the disk number. Note the `r` in `rdisk` - stands for "raw", in other words, the disk device itself, not a filesystem or partition _on_ the device.

Now to zero out the disk, use `dd` with an _input file_ (`if=`) of `/dev/zero` and an _output file_ of `/dev/rdisk#`:

```
$ dd if=/dev/zero of=/dev/rdisk4 bs=1m

```

In this example we're zeroing-out `rdisk4` with a block size (`bs=`) of 1 megabyte.

On MacOS, you can use `CTRL+T` (not CMD, CTRL) to check progress. It'll report the number of bytes written over a number of seconds.

### Write the ISO to the disk

Once that finishes, you can use the same command, just with a different _input file_ (`if=`) to write the ISO to disk:

```
$ dd if=/Users/jah/Downloads/some-os.iso of=/dev/rdisk4 bs=1m

```

When finished, ensure the disk isn't mounted (eject it if so) and you're good to go!

