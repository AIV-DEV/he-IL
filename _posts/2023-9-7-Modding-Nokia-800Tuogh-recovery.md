---
layout: post
title: Modding Nokia 800Tuogh recovery
---

All I wanted to do was hack recovery.img, and there's even a guide for that. But everything got complicated.

![SkyOSlogo](https://raw.githubusercontent.com/AshiVered/SkyOS/main/res/logo.png)

[SkyOS](https://github.com/AshiVered/SkyOS) is my custom ROM for Nokia 800Tough. A few months ago I abandoned it and [declared it as frozen](https://github.com/AshiVered/SkyOS/issues/6).

A few days ago, I started developing it again. but, about the main project - the ROM (system.img) I have some ideas, and I haven't completely decided which [direction to take the project.](https://github.com/AshiVered/SkyOS/discussions/7)

Beacuse this, I started to hack stock recovery, so that we also have a custom recovery for this device.

the project goals is to be similar to [GerdaOS recovery](https://gitlab.com/project-pris/recovery): ADB acsses, and support unsigend update.zip's.

I thought it would be easy: there is a [guide at BananaHackers](https://wiki.bananahackers.net/root/recovery-mode).
But, after following the guide…

The device just goes into bootloop.

So I flashed the stock recovery.img and tried to think. I remembered that in the past also for other devices (for example Xiaomi Qin1s+) the guide resulted in a similar result. And even if you don't edit the recovery, but only open and close with abootimg, this is the result. Why? I do not know.

So, I tried with Android Image Kitchen (AIK), that is mentioned in [this guide](https://github.com/minhduc-bui1/nokia-leo/wiki/6.-ROOT:-Boot-partition-modifying).
I did not follow this guide. I followed BananaHackers guide, but unpack&repack the recovery.img with AIK, not abootimg.

Oh, good!

The phone does not go into bootloop. Yay!

But, bad news:

Isn't hacked... no ADB acsses, can't flash unsigned ZIP's.  what???

So I decided to follow this guide, which is intended for boot.img and not for recovery.img, but explains what needs to be edited in default.prop to open adb access.
Wonderful! It works! only partially 😢.

That is, only for classic ADB commands (push, pull, etc.). yes. it's also with ROOT acsses. but, the shell not working.
![_config.yml]({{ site.baseurl }}/assets/images/shell_eror.png)

What is?! I've been developing custom ROMs for a few years now, and I've never heard of the *adbdsh* file.

What didn't I try? I compared the *ramdisk/sbin* folder in my recovery with GerdaOS recovery, and I saw that BusyBox was added there. So I added it to mine. didn't help. same error.

Maybe the *adbd* provided in BananaHackers is damaged? So I copied the adbd from GerdaOS recovery.

Same error.

Maybe it's a file that is missing due to recovery but exists in the *system/bin*? No.

But I copied *system/bin/sh* under the name adbdsh to my recovery. Finally, there seems to be progress.

This time the missing file is *sh*, not *adbdsh*. So I copied *sh* again, and this time kept its name, so I had both *adbdsh* and *sh*(and *adbd*, of course) in */ramdisk* . Didn't help, still missing *sh*. Strange... so I gave up on *adbdsh*- and of course we returned to the first error...

I don't have more ideas.

And that's even before we took care of the unsigned ZIP's problem...

Looks like there's still a lot of work to do...

Note: Thanks to [@Men770](https://github.com/men770) for some ideas (although in the end it didn't help...) and for showing me how to change the words "KaiOS recovery" to the words "SkyOS recovery" (it's quite simple actually; open the file */ramdisk/sbin/recovery* with a hex editor , search for the words "KaiOS recovery" and replace them with "SkyOS recovery").

Now I'm thinking of trying to port Philz Recovery prepared for Nokia 8110, for Nokia 800Tough. There is this guide on xda

Will it really work easily? We will update...

you can to find SkyOS recovery [here](https://github.com/AshiVered/SkyOS-Recovery/).
