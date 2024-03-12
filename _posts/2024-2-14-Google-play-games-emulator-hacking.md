---
layout: post
title: Google play games emulator hacking
---
Google Play games for PC is Android emulator by Google.
I tried to sideload apps and other hacks for this emulator.


![_config.yml]({{ site.baseurl }}/assets/images/playgames1.png)

### 1 - installation

Download the Google Play Games on PC Developer Emulator (we use the developer version to be able to use ADB).

[https://developer.android.com/games/playgames/emulator](https://developer.android.com/games/playgames/emulator)

Install the software. At the end of the installation, a tab will open in the browser (on PC) to connect to the Google account. Log in to your account - this is the account that will appear in the emulator.
After that, a dialog will pop up to confirm debugging on the emulator. confirm it.

### 2 - Sideloading

Now, we will sideload apps using ADB.
ADB is in this path

`C:\Program Files\Google\Play Games Developer Emulator\current\emulator`

(Regular ADB may also work, I haven't tried)
Installing APK files is possible by opening CMD in the above path and running the command

`adb install /path/to/your.apk`

### 3 - App store

Because it's officially an emulator designed for games, the built-in Google Play only has games.
So we will install an [Aurora store](https://auroraoss.com/).
First problem - only games are still displayed. That's why we entered the spoof manager and selected another device
![_config.yml]({{ site.baseurl }}/assets/images/playgames2.png)

And now we will have to go to accounts, select log out and log in again
![_config.yml]({{ site.baseurl }}/assets/images/playgames3.png)

But... if we try to download an app, we get the following error:
![_config.yml]({{ site.baseurl }}/assets/images/playgames4.png)

That's why you need to install through the shell. But... we don't have root. (at least at the moment).

So we will use [Shizuku](https://shizuku.rikka.app/) (an application that allows running a shell through the device even without root. You can read about it online).
Install it, open it, and run the following command in adb

`adb shell sh /sdcard/Android/data/moe.shizuku.privileged.api/start.sh`

We need to get an output like this
![_config.yml]({{ site.baseurl }}/assets/images/playgames5.png)

open Shizuku and check if all working.
![_config.yml]({{ site.baseurl }}/assets/images/playgames6.png)

Now, in the Aurora store, go to settings>installation> shizuku installer.
Amazing! Now it will work 😊.
(Note: the activation command for Shizuku will have to be run every time the emulator is turned on).

### 4 - Rooting reaserch

I was not able to root the emulator at the moment.
Attached is the information I currently have:
The boot.img is inside the aggregate.img file in the path

`C:\Program Files\Google\Play Games Developer Emulator\current\emulator\avd`

I tried to root the whole file with Magisk 27 and this is the error
![_config.yml]({{ site.baseurl }}/assets/images/playgames7.png)

I also tried to remove the boot.img from it and root only it. I succeeded, but at the moment I have no idea how to repack the aggregate.img

[*my post in XDA.*](https://xdaforums.com/t/google-play-games-on-pc-hacking-reaserch-theard.4656397/)
