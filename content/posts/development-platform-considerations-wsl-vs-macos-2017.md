+++
author = "J. Austin Hughey"
date = 2020-06-11T08:14:07Z
description = ""
draft = false
slug = "development-platform-considerations-wsl-vs-macos-2017"
title = "Development Platform Considerations: WSL vs. MacOS (2017)"

+++


For over a year now, my development environment has been a mix of Microsoft's [Windows Subsystem for Linux](https://blogs.msdn.microsoft.com/wsl/), and the more often-used (at least in my industry) [MacOS](https://www.apple.com/macos/high-sierra/) environment. Each has its virtues, and each has problems. This article discusses reasons for switching _away_ from MacOS, the differences between the two development platforms, and compares and contrasts developer tools available for each.

## Apple's MacOS: The Gold Standard

In my "industry" (really, a small slice of the overall "developer/devops" community that usually works with technologies like Ruby, Linux, Chef, maybe some Python, etc.), the vast majority of developers seem to gravitate toward the typical Macbook Pro laptop, and thus the officially-sanctioned MacOS operating system. As most people are aware, buying an Apple computer is usually going to cost you a significant amount more than an equivalently-built PC. In the past, though, this has been totally worth it: for the money you pay you get what I still believe is _the best operating system on the planet_, dependable hardware, and a warranty that actually means something.

What you don't get anymore, however, is _modern hardware_.

### Apple Abandons Professional Users

Recent releases of the MacBook Pro (and other Apple laptops) have received [mixed reviews](https://en.wikipedia.org/wiki/MacBook_Pro#Reception_4) at best, and many people in the professional user market - not just developers, but graphic designers, 3D artists, and video editors have criticized Apple's failure to add meaningfully-powerufl hardware to their laptop lineup since roughly _2013_.

Apple generates over **four times the revenue** from the iPhone product line than from the Mac product line. [These figures from businessinsider.com](http://www.businessinsider.com/apple-q4-earnings-2017-11) show Q4 2017 iPhone revenue exceeding $28 billion - dwarfing the only $7 billion coming from Apple's Mac product line.

With such a rift in revenue streams, it's no wonder Apple has, as many critics and former enthusiasts claim, abandoned the professional user who relies on the Mac computer product line. Apple has responded by adding features to its latest MacOS release, High Sierra, that support the use of [external GPUs over Thunderbolt 3](https://developer.apple.com/development-kit/external-graphics/). This may satisfy _some_ professional users, but others will still demand more from Apple's expensively-priced personal computer product line.

Like many professional Apple users, I saw this coming and still remain very skeptical as to whether or not Apple will continue to innovate - or even care - about their personal computer product line. From a strictly financial standpoint, I can't fault them whatsoever for doubling down on the iPhone, shifting internal resources and focus away from the less-profitable product lines like the Mac. But as a professional user, who needs a dependable computer from Apple because it's the only supported, "legal" way to use MacOS, this trend has me worried.

I'm far from the only person who's seen this coming, or who's skeptical on the future viability of the Mac product line. As such, I started thinking about ways to utilize MacOS _without_ being subjected to buying a glorified abacus. And while building a "hackintosh" or finding ways to safely virtualize MacOS on a Windows 10 or Linux host (that don't break and/or compromise the hypervisor itself) are valid approaches for those who are willing to accept the risk or spend the time on it, I wanted something better, more "pure".

## Enter Windows Subsystem for Linux

Earlier this year, Microsoft released a new feature for its Windows 10 operating system, called "Windows Subsystem for Linux". This is an additional feature you can enable in certain versions of Windows that will install features directly into the Windows kernel that are direct impementations of, or shims that delegate to equivalent, native Linux kernel syscalls.

So for example, if a natively-compiled Linux binary makes "write" system call, thinking that it's running natively on Linux, the Windows kernel will intercept and handle that syscall as if it **actually is** the Linux kernel.

This enables Microsoft to install a Linux "subsystem" - not a full operating system, and not virtualized - directly on the user's computer. The installed binaries are the  **exact same binaries Ubuntu users get via `apt` and come directly from Canonical's repository - NO CHANGES.**

With this feature available for people using Windows, development of things like Ruby apps - compiling extensions natively - are no longer such a hassle. _This means they no longer "must" have MacOS!_

### But is it as good as MacOS?

Leaving issues of user interface design and aesthetics aside, Windows 10 is a perfectly capable and stable operating system. It's fully capable of being used as your "daily driver" for Ruby/Chef/Python/Go/Elixir/etc. development from a technical standpoint, but unlike MacOS, suffers from a lack of well-designed, maintained and useful development tools.

## Developer Tools Make the Difference

The biggest problem using Windows Subsystem for Linux is the lack of a good set of development tools. On MacOS we have the excellent [iTerm2](https://www.iterm2.com/); on Windows, the ecosystem is fragmented, the options appear many but are mostly outdated, and the available console replacements (as the regular Windows terminal doesn't even do tabs, let alone all the other advanced stuff you get with iTerm2) suffer from hacks that are necessary due to the design of various parts of the Windows API, way too many configuration options, and a desire to be "all things to all people" that comes out in the design, workflow, and general usability of terminal replacement tools on the Windows platform.

There are, however, some nascent projects that seek to alleviate this problem by providing a solid terminal replacement, complete with first-rate features, in a platform-agnostic way. Take [hyper](https://hyper.is/) for example; using GitHub's [Electron](https://electron.atom.io/) shell project, it's a somewhat-viable terminal replacement that doesn't suffer from the aforementioned issues, yet does seem a bit slower than a natively-compiled application, and in my personal experience, has a ton of bugs yet to be fixed due to the relative lack of interest and less usage on the Windows platform.

Other terminal replacement possibilities:

* [ConsoleZ](https://github.com/cbucher/console)
* [Terminals](https://github.com/Terminals-Origin/Terminals)
* [Terminus](https://eugeny.github.io/terminus/)
* [Alacritty](https://github.com/jwilm/alacritty)

### Graphics Development

On MacOS, many people seem to be in love with [Sketch](https://www.sketchapp.com/), a MacOS-exclusive graphics development environment. [Pixelmator](http://www.pixelmator.com/) is another long-standing MacOS-exclusive application that provides a very strong competitor to Adobe Photoshop.

While these tools don't exist on the Windows side of things, my personal favorite, [Affinity Designer](https://affinity.serif.com/en-us/designer/) is an affordable alternative to Sketch that runs on both MacOS and Windows. Serif, the publisher of Affinity Designer, also offers Affinity Photo for all your raster-editing needs.

And there's always, of course, Photoshop. If you're into the whole pay-every-month pricing model thing (personally, not a fan).

Linux users will likely lament my exclusion of ["The Gimp"](https://www.gimp.org/) here; this is because it's aptly named. I've rarely seen anyone with any professional design experience willingly utilize "The Gimp" to create visuals anyone would actually _want_ to look at. That isn't to say it's not possible - it definitely is, no question - but it's very rare some one with real experience in graphics editing will be as efficient or satisfied using The Gimp as they would with the other professional tools mentioned above.

### Source Code Management (git, svn)

MacOS has a bevy of beautiful, easy-to-use source code management tools, though on the Windows side of things, the picture isn't quite so pretty and the workflow is drastically different depending on what tools you may be using.

Take for example the excellent [Tower](https://www.git-tower.com/) app for managing Git repositories. It was first released on MacOS several years ago, yet just released its first Windows version in 2017. MacOS has long had two great SVN clients: [Versions](https://versionsapp.com/) and [Cornerstone](https://www.zennaware.com/cornerstone/index.php). Both sport a refined and useful user interface and have been staples of developers on MacOS working with [Subversion](https://subversion.apache.org/) for nearly a decade.

Windows, on the other hand, suffers from a lack of _well-designed_ equivalent products that _are still maintained_. Sure, there's [Tortoise SVN](https://tortoisesvn.net/) and [Tortoise Git](https://tortoisegit.org/), but both integrate directly with the Windows Explorer experience which can be both confusing and imply a scattered interface, requiring the user to run all over the filesystem with multiple windows open to see what changed. It's also not clear to me if either provides a built-in diff interface (and if so, built-in _to what?_).

Thankfully there are a great number of git-friendly GUI applications out there that are cross-platform compatible to varying degrees. The git project website maintains a [great list of git desktop applications](https://git-scm.com/downloads/guis) that allow you to filter by operating system: Windows, MacOS, and Linux.

### Editors

MacOS has long been envied as the native platform for the venerable [Textmate](https://macromates.com/) editor. Popularized during the rise of the Ruby on Rails framework, even a decade later it still has die-hard users. Its second version is both [open source](https://github.com/textmate/textmate) and currently designated "beta". Development on the project seems to have stalled, however, as GitHub shows, that as of the date of this writing, the most recent commit to its `master` branch is August 24.

There's also the user-friendly [Coda](http://panic.com/coda) WYSIWYG editor, providing a purely graphical way to assemble an HTML-based interface, complete with the ability to work with CSS and Javascript. It also provides a source-code view that allows the user to edit source code by hand and see changes reflected in real time. The only equivalent Windows application I know of is Adobe's Dreamweaver - a project they acquired with the Macromedia acquisition so many years ago. Neither product is open source or free of charge, but Coda is certainly more affordable over the long term, given Adobe's "monthly rental" style licensing is the only option available for purchase.

Like Coda, there's also [Hype](http://tumult.com/hype/), an advanced editor aimed toward HTML5 animation and complex static site and/or interface development. It seems to have more features than Coda, but also appears more complex and geared more toward professional front-end designers and developers for the creation of standards-compliant infographics, interactive interfaces, and the like. I'm aware of nothing even remotely similar on Windows or Linux.

Thankfully for Windows users, projects like GitHub's [Atom](http://atom.io/) editor and Microsoft's [Visual Studio Code](http://code.visualstudio.com/) are both free, cross-platform (including Linux) editors that are extremely veratile, easy-to-use, and have large, thriving developer communities, creating useful extensions for both.

There are also editors like [Komodo Edit](https://www.activestate.com/komodo-ide/downloads/edit), [Brackets](http://brackets.io/) and [Sublime Text](http://sublimetext.com/), all three of which are available on all three major desktop platforms: Windows, Linux and MacOS.

### Diff Tools

While there doesn't appear to be a large _quantity_ of diff tools available on MacOS, the sheer _quality_ of the leading tool I know of is unparalleled. [Kaleidoscope](https://www.kaleidoscopeapp.com/) is by far the best designed and most useful diff tool I've ever seen or used. I'm aware of no equivalent within the Windows or Linux worlds, however for most users, a good SCM will include a solid diff tool built into the application, which may be all that's needed. But for diffing larger numbers of documents or things not within source control, Kaleidoscope appears unparalleled.

You're not totally out of luck on Windows or Linux, however: [Meld](http://meldmerge.org/) is a great cross-platform GUI diff tool that's free and open source. Other tools include Beyond Compare, Araxis Merge, and Code Compare, among others. Tobias Günther of fournova has a [great blog entry on the Tower git client website describing your options](https://www.git-tower.com/blog/diff-tools-windows/), including some not mentioned here that I highly recommend.

## What about the console? CLI-only tools?

If you're happy with a CLI-only workflow, more power to ya. With the exception of graphics, video and maybe music editing or management needs, it's entirely possible (and to some, preferable) to run your entire development environment inside of various `tmux` sessions, relying on raw CLI tools like `diff`, `vim`, `emacs`, `git`, and so on. **You can absolutely leverage all these tools inside Windows Subsystem for Linux just as well as you could/would on MacOS**.

## Which do you use?

After over a year of mixing both development environments to try to separate myself from the Apple ecosystem, I find myself coming "back to the Mac". The tooling available on Windows just isn't there yet, and truth be told, Windows Subsystem for Linux, while a legendary feat of modern software engineering (hey Microsoft, _be proud of yourselves here!_), still has a way to go. For 90% or more of anyone's use cases, it's great, but things like slow disk I/O (due to emulation necessary to store files on NTFS or ReFS in a Linux-friendly way) and the presence of WSL behind a "barrier" in userspace (processes in WSL aren't aware of NT processes and vice versa), coupled with advancements that can still be made in operating system integration, Microsoft still needs to do some more work.

Available tools for Windows that are equivalent in quality and usefulness to their MacOS peers simply don't yet exist for Windows in many cases, which is a real shame. I'm hopeful that in the near future this statement will no longer be valid, but for the time being, it's hard finding a well-designed tool (commercial or otherwise) that's equal in quality to MacOS equivalents that are available now and have been, in some cases, for around a decade.

Microsoft's CEO, [Satya Nadella](https://twitter.com/satyanadella), seems to be in large part to credit with Microsoft's evolution in recent years that embraces Linux and attempts to make Windows the best platform available to develop on, regardless of your project's target platform. I think Mr. Nadella deserves a _lot_ of credit for moving Microsoft past the archaic and draconian views that defined it for over a decade, and I, for one, strongly hope to see continued success for Microsoft as an excellent development platform. The only thing holding it back - just like with its Windows Phone initiative - is a lack of enthusiastic developers building excellent tools and apps for its platform and vision. Here's hoping that changes.

