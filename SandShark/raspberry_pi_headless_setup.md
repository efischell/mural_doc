# Headless Configuration for SandShark Raspberry Pi 3

## Disabling the desktop GUI
### Terminal commands:
```sh
> sudo systemctl set-default multi-user.target --force
> sudo systemctl disable lightdm.service --force
> sudo systemctl disable graphical.target --force
> sudo systemctl disable plymouth.service --force
```

## Re-enabling the desktop GUI
### Terminal commands:
```sh
> sudo systemctl enable lightdm.service --force
> sudo systemctl enable graphical.target --force
> sudo systemctl enable plymouth.service --force
> sudo ln -s /lib/systemd/system/lightdm.service /etc/systemd/system/display-manager.service
> systemctl set-default graphical.target --force
```

## Remove unnecessary processes
### Terminal commands:
```sh
> sudo apt-get purge brltty
```

## Removing unnecessary startup processes
### Rename the following .startup files to .backup files, located in /etc/xdg/autostart/:
* a11y-profile
* blueman
* deja-dup
* indicator-sound
* mate-volume
* mate-screensaver
* nm-applet
* onboard
* orca
* print
* pulseaudio
* update-notifier

## Disabling unnecessary services
### The following services can be disabled using the command:
```sh
> sudo systemctl disable <service>
```
* alsa-utils (sound driver)
* bluetooth (bluetooth)
* cups (printing)
* cups-browsed (printing)
* hddtemp (external drive temp)
* lightdm (desktop gui)
* plymouth
* plymouth-log
* saned (scanners)
* screen-cleanup (screen service)
* speech-dispatcher (text to speech)
* unattended-upgrades (auto security upgrades)
### You can check the status of services using:
```sh
> service --status-all
```

## Raspberry Pi hangs on boot due to emergency mode
### This can be fixed by doing the following:
* Open the bottle and remove the Raspberry Pi SD card
* Place this SD card in your computer
* Use Disks tool to unmount the SD card
* Run the commands:
```sh
> sudo fsck /dev/mmcblk0p1
> sudo fsck /dev/mmcblk0p2
```
* Place this SD card back into the Raspberry Pi and close the bottle
