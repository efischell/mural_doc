# Timing

## Setting up time in the payload bottles

Since the RPi does not have a real-time clock, it uses the`fake-hwclock` service to keep track of the system time and periodically saves it to `/etc/fake-hwclock.data`. At boot, the time in `/etc/fake-hwclock.data` is used to set the system time. This may cause issues with NTP if the difference is very significant. One way to solve this is to manually set the system time with NTP (one-shot) while both NTP and `fake-hwclock` services are stopped. Run the following with super user privileges: 

```sh
service fake-hwclock stop
service ntp stop
ntpdate <ntp-server-of-choice>
fake-hwclock save force
service ntp restart
service fake-hwclock restart
```
Note that the script above requires `ntpdate` which is not present in the payload bottle images by default.

## Connecting to WPA Network

```sh
sudo nmcli device wifi connect <SSID> password <password>
iwconfig
```

## Remounting Frontseat Computer

In order to modify config files in /opt/bluefin/config/ you must remount the drive using"

```sh
sudo mount -o rw,remount /dev/sda1
```

edit the files using vi, then reboot the frontseat computer.
