+++
author = "J. Austin Hughey"
date = 2020-06-11T08:15:59Z
description = ""
draft = false
slug = "hackintosh-how-to-find-out-what-motherboard-model-you-have-2018"
title = "Hackintosh: How to find out what motherboard model you have (2018)"

+++


[Hackintosh](https://en.wikipedia.org/wiki/Hackintosh) enthusiasts often have to fix small but important missing or malfunctioning features of their OS after they get their first successful build, and key to that is _knowing your hardware._ And instead of being outright lied to by MacOS and its built-in tools, I'll show you how one terminal command can help you **get the truth.**

Let's get straight to it:

```
$ ioreg -rc FakeSMCKeyStore
+-o FakeSMCKeyStore  <class FakeSMCKeyStore, id 0x100000128, registered, matched, active, busy 0 (1 ms), retain 13>
    {
      "IOProbeScore" = 0
      "CFBundleIdentifier" = "org.netkas.driver.FakeSMC"
      "IOMatchCategory" = "FakeSMCKeyStore"
      "IOClass" = "FakeSMCKeyStore"
      "IOProviderClass" = "IOResources"
      "manufacturer" = "ASUS"
      "product-name" = "Z97-AR"
      "IOUserClientClass" = "FakeSMCKeyStoreUserClient"
      "IOResourceMatch" = "IOKit"
    }

$

```

Jackpot.

> Whoa whoa whoa what?

Without making this a huge _thing_ on the SMC and UEFI and spoofing it and all that, in a nutshell we're using an open-source kernel extension to give us REAL information about our hardware. Since we loaded [FakeSMC](https://bitbucket.org/RehabMan/os-x-fakesmc-kozlek) at boot as a kernel extension, that same kernel module is now loaded and running.

> Why not just open up the case and look around?

Basically, because _I got pissed off._  [NASA](http://nasa.gov/) put a dude _on the MOON before I was born_. How the hell is it we can't, 49 years later on one of the best operating systems in existence, not get some basic information with that software?

> What was wrong with `sysctl` and `system_profiler`?

Well, `sysctl` can't really tell you much about your computer other than your processor:

```
$ sysctl -n machdep.cpu.brand_string
Intel(R) Core(TM) i7-4790K CPU @ 4.00GHz
$

```

Which, yeah that's...kind of useful. Maybe. But as far as I can tell, it won't give you anything else that's clear and useful in making an inventory of what's _actually_ in your damn computer.

Which brings me to one baby seal I'd like to club: `system_profiler`

{{< figure src="https://github.com/jahio/jahio.github.io/raw/master/assets/bad-jokes-so-a-baby-seal-walks-into-a-club-977561.png" >}}

This is basically a command-line version of the native, Apple-created desktop app similarly named (`/Applications/Utilities/System Information.app`). While you can get _some_ useful hardware information out of that app, the crucial data you need to profile your system comes from the SMBIOS settings you've configured (likely through something like [Clover Configurator](https://mackie100projects.altervista.org/download-clover-configurator/)), which you'd only do _to get compatibility with essentially the same hardware_.

Common sense 101: Don't tell me what I already know; I'm the one who told it to you!

This is why `ioreg`, talking to the aforementioned kernel extension, is so useful.

---

On Linux, we have `/proc` and `/sys`. On BSD you can access `/kern`. On Windows there are a plethora of open source, free, GPL-licensed tools (that are even still maintained!) to do the same thing. Why the hell should MacOS be any different?

Apple, I'm not happy with you. **Shit like this is WHY people are Hackintoshing.**

