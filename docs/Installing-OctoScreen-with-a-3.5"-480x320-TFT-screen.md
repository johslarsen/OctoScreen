Many thanks to @mattglg, @DanielP5433, @brotherchris, and many others for help tracking down and troubleshooting this issue.

Here are the steps to install OctoScreen on a RaspberryPi 4B, with a 3.5" 480x320 TFT screen.

**Step 1**, start with a fresh install.  Please start over and don't reuse an existing installation.  Download the latest OctoPi image (from https://octoprint.org/download/).  At the time of this writing, it was OctoPi 1.0.0 & OctoPrint 1.10.2.  Flash OctoPi onto a SD card, and go through the OctoPrint install steps listed at https://octoprint.org/download/ , e.g. on a linux machine do for instance:

```sh
wget https://github.com/OctoPrint/OctoPi-UpToDate/releases/download/1.0.0-1.10.2-20240618101629/octopi-1.0.0-1.10.2-20240618101629.zip
unzip octopi-1.0.0-1.10.2-20240618101629.zip
sudo dd if=octopi-1.0.0-1.10.2-20240618101629.img bs=4M of=/dev/sdX  # where X is the device for the SD-card
# mount /dev/sdX1 somewhere and update octopi-wpa-supplicant.txt
```

_(NOTE:  After flashing OctoPi onto an SD card, when you ssh into the Raspberry Pi, you will see this message:
![image](https://user-images.githubusercontent.com/10328858/108611440-606bdb00-7393-11eb-9044-935b20c6e753.png)
**DO NOT run install-desktop**.  OctoScreen runs on X11... if you install an other GUI desktop, it will conflict with OctoScreen)_

**Step 2**, Install OctoPi, log in to OctoPrint, configure an OctoPrint user, go through the OctoPrint setup wizard, and then update OctoPrint to the latest version.  At the time of this writing, that was OctoPrint 1.10.2.

_(NOTE:  Don't install any other plugins or make any additional configuration changes just yet - let's keep things simple... you can add plugins after OctoScreen is installed and working.)_

**Step 3**, After updating OctoPrint to the latest version and then rebooting, let's now set up the display.
Please follow the following steps:

```sh
git clone https://github.com/waveshare/LCD-show.git
cd LCD-show

# but this is unmaintained and outdated, so we have to do some damage control (i.e. replace relevant lines with noops):
sed -i 's/.*99-fbturbo.conf.*/true/' LCD35-show      # because that driver is unmaintained and incompatible with todays Xorg, default works fine though
sed -i 's/.*reboot.*/true/' LCD35-show               # because we have more to do

sudo ./LCD35-show  # NOTE: default is screen orientation with power plug on the bottom, e.g. `sudo ./LCD35-show 180` to have it reversed
sudo sed -i 's/^hdmi_cvt.*/#\0\nhdmi_cvt=800 533 60 6 0 0 0/' /boot/config.txt  # simulate compatiblity with OctoScreen resolution
```

**Step 4.** And now onto installing OctoScreen...

```
wget https://github.com/Z-Bolt/OctoScreen/releases/download/v2.7.4/octoscreen_2.7.4_armhf.deb
sudo dpkg -i octoscreen_2.7.0_armhf.deb  # this will say there are errors about missing dependencies, but otherwise work fine
sudo apt-get install --fix-broken        # fix those missing dependencies
sudo apt-get install x11-xserver-utils   # and some more missing dependencies

sudo sed -i 's/OCTOSCREEN_RESOLUTION=.*/OCTOSCREEN_RESOLUTION=800x533/' /etc/octoscreen/config

sudo systemctl set-default graphical  # otherwise octoscreen does not start by default
```

_(NOTE: This document is not kept up to date, and the build listed above might not be the latest release.  To instll the lastest release, see https://github.com/Z-Bolt/OctoScreen/releases)_

*Step 5.* Now reboot your system:

```
sudo reboot now
```

Upon reboot if you are lucky, OctoScreen should run and should display correctly, without any screen resizing or any of the screen shifting issues that have been reported.

And hopefully the touchscreen works and is calibrated good enough. In some installations I managed to brick this, possible because the touchpad was reclassified as an xinput device, and then nothing like the touchpad calibration LCD-show installed had any effect. What helped was to go through and use the config from this calibration procedure:

```
git clone https://github.com/reinderien/xcalibrate
sudo apt-get install xinput python3-tk
DISPLAY=:0 xcalibrate/xcalibrate  # IMPORTANT: answer 'n' to "Disable rotation?"
```
