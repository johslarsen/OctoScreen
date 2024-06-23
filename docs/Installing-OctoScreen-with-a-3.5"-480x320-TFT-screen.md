Many thanks to @mattglg, @DanielP5433, @brotherchris, and many others for help tracking down and troubleshooting this issue.

Here are the steps to install OctoScreen on a RaspberryPi 3B, with a 3.5" 480x320 TFT screen.

**Step 1**, start with a fresh install.  Please start over and don't reuse an existing installation.  Download the latest OctoPi image (from https://octoprint.org/download/).  At the time of this writing, it was 0.17.0.  Flash OctoPi onto a SD card, and go through the OctoPrint install steps listed at https://octoprint.org/download/.
<br />

_(NOTE:  After flashing OctoPi onto an SD card, when you ssh into the Raspberry Pi, you will see this message:
![image](https://user-images.githubusercontent.com/10328858/108611440-606bdb00-7393-11eb-9044-935b20c6e753.png)
**DO NOT run install-desktop**.  OctoScreen runs on X11... if you install an other GUI desktop, it will conflict with OctoScreen)_

<br />
<br />

**Step 2**, Install OctoPi, log in to OctoPrint, configure an OctoPrint user, go through the OctoPrint setup wizard, and then update OctoPrint to the latest version.  At the time of this writing, that was OctoPrint 1.4.2.

_(NOTE:  Don't install any other plugins or make any additional configuration changes just yet - let's keep things simple... you can add plugins after OctoScreen is installed and working.)_
<br />
<br />
<br />

**Step 3**, After updating OctoPrint to the latest version and then rebooting, let's now set up the display.
Please follow the following steps:

`$ sudo apt-get update`

`$ sudo apt-get install -f`

`$ git clone https://github.com/waveshare/LCD-show.git`

`$ chmod -R 755 LCD-show`

`$ cd LCD-show/`

`$ chmod +x LCD35-show`

_(NOTE: the waveshare drivers are display-specific.  Please consult the manufacturer of your display for the video drivers and the install steps)_
<br />
<br />


`$ sudo ./LCD35-show`

This last step will reboot your system and you will need to SSH back in.  

_(NOTE: Use `sudo ./LCD35-show` if you want the orientation of the screen to have the USB ports on the right and the power plug on the bottom, or use `sudo ./LCD35-show 180` if you want the orientation of the screen to have the USB ports on the left and the power plug on the top)_
<br />
<br />


Once SSH'd back in, continue with the following steps...

`$ sudo nano /boot/config.txt`
<br />
<br />

There should be an existing hdmi_cvt line near the bottom of config.txt.  Change the line to:

`hdmi_cvt=800 533 60 6 0 0 0`
<br />
<br />


Now reboot...
`$ sudo reboot now`
...and SSH back in.
<br />
<br />

`$ sudo apt-get install libgtk-3-0 xserver-xorg xinit x11-xserver-utils`

Now reboot... ...and SSH back in.
<br />
<br />


`$ sudo apt-get install git build-essential xorg-dev xutils-dev x11proto-dri2-dev`

Now reboot... ...and SSH back in.

_(NOTE: many of these reboots probably aren't necessary, but do them anyway, just to be safe)_
<br />
<br />

`$ sudo apt-get install libltdl-dev libtool automake libdrm-dev`

Now reboot... ...and SSH back in.
<br />
<br />

`$ git clone https://github.com/ssvb/xf86-video-fbturbo.git`

`$ cd xf86-video-fbturbo`

`$ autoreconf -vi`

`$ ./configure --prefix=/usr`

`$ make`

_(NOTE: at this point I got several warnings about "const", and "expected char * but type is const char *".  I ignored the warnings and continued on)_

`$ sudo make install`

`$ sudo cp xorg.conf /etc/X11/xorg.conf`

Now reboot... ...and SSH back in.
<br />
<br />

**Step 4.** And now onto installing OctoScreen...

`$ wget https://github.com/Z-Bolt/OctoScreen/releases/download/2.6.0/octoscreen_2.7.0_armhf.deb`

`$ sudo dpkg -i octoscreen_2.7.0_armhf.deb`

_(NOTE: This document is not kept up to date, and the build listed above might not be the latest release.  To instll the lastest release, see https://github.com/Z-Bolt/OctoScreen/releases)_
<br />
<br />


`$ sudo nano /etc/octoscreen/config`

Change

`OCTOSCREEN_RESOLUTION=`

to

`OCTOSCREEN_RESOLUTION=800x533`

Save, and then exit nano.
<br />
<br />

Now reboot your system once more:

`$ sudo reboot now`

Upon reboot, OctoScreen should run and should display correctly, without any screen resizing or any of the screen shifting issues that have been reported.
