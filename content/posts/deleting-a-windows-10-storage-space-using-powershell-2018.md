+++
author = "J. Austin Hughey"
date = 2020-06-11T08:15:16Z
description = ""
draft = false
slug = "deleting-a-windows-10-storage-space-using-powershell-2018"
title = "Deleting a Windows 10 Storage Space Using PowerShell (2018)"

+++


If you tried to delete a Storage Space via the built-in Windows GUI and it just hangs forever, try this PowerShell method for quickly deleting it so you can re-use your drives.

I recently set up a Windows 10 Storage Space - basically a built-in software RAID - and realized the performance was _horrible_, so I went to delete it. But the GUI just sat there and hung for hours, with no progress and no errors reported. After a shut-down, removal of the drives in question, removing the "hidden" devices from Device Manager (View -> Show hidden devices), and a couple reboots, I finally found out how to remove the storage space itself from Windows, so that I could then re-use my drives in another way.

## Overview

After launching an admin PowerShell session, we'll use some built-in tools to retrieve the name and status of the "StoragePool" object in question. Then using more of Windows built-in tools, we'll nuke it.

### Launch a PowerShell Admin Session

`WIN+X` and navigate to "Windows PowerShell (Admin)" and pop that open.

### Get the name and status of the storage pool

In this case, my storage pool was just named, aptly, "Storage Pool".

```
PS C:\WINDOWS\system32> Get-StoragePool

FriendlyName OperationalStatus HealthStatus IsPrimordial IsReadOnly    Size AllocatedSize
------------ ----------------- ------------ ------------ ----------    ---- -------------
Storage pool Read-only         Unhealthy    False        False      2.73 TB        6.5 GB
Primordial   OK                Healthy      True         False      4.28 TB      931.5 GB

```

> What's "Primordial?"

I have no idea, and I'm not at all a Windows admin. If somebody knows, holler at me and I'll update this post.

### Set Read-Only to FALSE

The first major problem I ran into with this method was that trying to delete the storage pool always failed, saying that it's status was set to "read only". After some searching, I found that you can use PowerShell to fix that, too:

```
PS C:\WINDOWS\system32> Get-StoragePool -FriendlyName "Storage pool" | Set-StoragePool -IsReadOnly $false

```

Now you should be able to modify the storage pool.

### Nuke it

Now that it's no longer read-only, you can delete the storage pool with this command:

```
PS C:\WINDOWS\system32> Get-StoragePool -FriendlyName "Storage pool" | Remove-StoragePool

Confirm
Are you sure you want to perform this action?
This will remove the StoragePool "Storage pool".
[Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "Y"): y
PS C:\WINDOWS\system32>

```

As you can see here, it'll prompt you for confirmation interactively. _Real_ Windows admins can probably tell me/you/us how to script that for non-interaction, but since my use case is "power user" I'm fine with confirming interactively.

Once you run this, in my case PowerShell displayed an interesting progress indicator at the top of the window itself for a few seconds, then it was done.

> How long will this take?

Honestly, I don't know. When I used the Control Panel GUI to try to remove this storage space, it hung _forever and a day_, doing nothing but giving me a pretty green progress bar. After letting this run for quite a while, I tried to cancel it, but it just sat there doing the same thing. _I had to forcefully cut power to the computer entirely to move on._ After yanking the drives (USB 3), rebooting, yanking the USB _hub_ too, and rebooting again, I was finally able to use the above commands to delete the storage space itself, thus freeing the drives from the OS perspective to be re-used. When I finally ran this, the deletion operation itself only took about 3 seconds. Bear in mind, however, I'd already removed the USB 3 drives from Device Manager (remember: View -> Show hidden devices) under "Disk Drives", and all "hidden" and non-used volumes under "Volumes". I suspect that's why this ran so fast in PowerShell, but I'd be willing to bet you a Coke that doing it this way is almost always going to work faster and better!

> So if you're not using StorageSpaces, what _are_ you planning to use?

Right now I'm leaning toward [StableBit DrivePool](https://stablebit.com/DrivePool). It's not quite software-RAID, in that it doesn't work like a RAID setup at all. Instead, it allows you to combine `N` number of disks of any kind "in userspace" basically and then creates a virtually-exposed "disk" for the operating system that is a proxy/abstraction layer on top of the disks you tell DrivePool to use. Then, when you write to the "pooled" (virtual) disk, DrivePool handles the work of duplicating files across the _physical_ disks in the pool according to whatever settings you give it. Perhaps the biggest "win" here for this product, in my opinion, is that it does this on top of the existing filesystem on each disk, such that if you lose a disk or need to restore, you just access the files directly from the disk as if it's mounted NTFS or exFAT or whatever. **You don't need to re-install DrivePool to recover your files -- you just need to mount the disks normally.**

The one downside of DrivePool in my experience (which I admit is nascent, at best) is that it tends to be pretty I/O heavy. It'll do a lot of work to duplicate files across your pool according to your policies that you set, and to reorganize data for better storage and performance, but this means it's very often engaged in disk I/O, so if you have slower disks (e.g. 5400RPM old school hard drives) over, say, USB2, [You're Gonna Have a Bad Time](https://www.youtube.com/watch?v=6Ls5j5iz2eA).

