# Spells and incantations to make ntp work with CSAC in Ubuntu 14.04/16.10:
## (edits for Raspberry Pi configuration)

### Setup:
#### 1. a)

Install relevent packages: 

```sh
sudo apt-get install ntp pps-tools setserial
```

Plug in CSAC serial output from J1 connector to Serial port or USB-serial converter to USB port.  Check via minicom that messages are being sent- you should see $GPRMC messages.  If you don't, enter minicom and give the following command to the USB port:

```sh
GPS:GPRMC 1
```

Or print it over the port:

```sh
printf 'GPS:GPRMC 1\r\n' > /dev/ttyUSB0
```

similary, setup the CSAC with the following settings (you can query the settings using ?, i.e. GPS:GPRMC?):

```sh
GPS:PORT USB
SYST:COMM:SER:BAUD 9600
GPS:GPZDA 1
```

#### 1. b) 
Create a file /etc/udev/rules.d/08-usb.rules (to map a symlink between the CSAC USB and a persistent port - the second line is an example of an additional USB CTD device):

```sh
SUBSYSTEM=="tty", ATTRS{idVendor}=="0403", ATTRS{idProduct}=="6001", ATTRS{serial}=="A7040W32", SYMLINK+="ttyCSAC"
SUBSYSTEM=="tty", ATTRS{idVendor}=="067b", ATTRS{idProduct}=="2303", ATTRS{product}=="USB-Serial Controller D", SYMLINK+="ttyCTD"
```

note: you can use "udevadm info -a -n /dev/ttyUSB0 | grep '{idVendor}'" to get the values of the USB device attributes

#### 2. a) 

Create a file /etc/udev/rules.d/09.pps.rules:

```sh
# Provide a symlink to /dev/ttyCSAC and set low_latency for latency improvement
SUBSYSTEM=="tty", ATTRS{idVendor}=="0403", ATTRS{idProduct}=="6001", ATTRS{serial}=="A7040W32", SYMLINK+="gps0", MODE="0666"
SUBSYSTEM=="tty", ATTRS{idVendor}=="0403", ATTRS{idProduct}=="6001", ATTRS{serial}=="A7040W32", RUN+="/bin/setserial /dev/%k low_latency"

# Symlink /dev/pps0 to /dev/gpspps0
KERNEL=="pps0", SUBSYSTEM=="pps", DRIVER=="", SYMLINK+="gpspps0"
KERNEL=="pps0", GROUP="dialout"
KERNEL=="pps0", MODE="0666"
```

#### 2. b) 

Add the following lines to /etc/rc.local before "exit 0" line:

```sh
# Attach CSAC pps for ntp server, and restart ntp server
ldattach pps /dev/ttyCSAC
sudo service ntp restart
```

#### 3. a) 
Add the following to /etc/ntp.conf: (flag1 0 and flag3 0 if not using pps):

```sh
# CSAC (using $GPRMC via gps0 @ 9600 baud - mode indicates baud and msg: 16 is 9600, 1 is process $GPRMC)
server 127.127.20.0 mode 17 minpoll 4 maxpoll 4 iburst prefer true
fudge 127.127.20.0 flag1 1 flag2 0 flag3 1 time1 0.0 time2 0.1
```

#### 3. b) 
(Optional) Add the following to /etc/ntp.conf:

```sh
# CSAC (using pps0)
server 127.127.22.0 minpoll 4 maxpoll 4
fudge 127.127.22.0 flag2 1
```

#### 4. 

Add the following to /etc/apparmor.d/tunables/ntpd:

```sh
@{NTPD_DEVICE}="/dev/ttyCSAC" /dev/pps0
```

Substituting your USB or serial port for ttyUSB0, and commenting out any other lines (e.g. /dev/null)

#### 5. 

Make sure port permissions are set correctly by adding yourself to dialout group:

```sh
sudo usermod -a -G dialout mituser
groups mituser
```

(make sure dialout shows up)

#### 6. 
Reboot computer

#### 7. 

Check if ntp is working:

```sh
watch ntpq -p
```

This should output something like: 

```sh
Every 2.0s: ntpq -p                                   Thu Dec 10 16:05:21 2015

     remote           refid      st t when poll reach   delay   offset  jitter
==============================================================================
oGPS_NMEA(0)     .GPS.            0 l    9   16  377    0.000   -0.388   0.774
```

NOTES:
* If you look at the serial or USB port via minicom, ntp will stop seeing the CSAC as a time source.  Restart ntp for it to work again
* The * should become an o before the GPS_NMEA line in ntpq when PPS lock is acquired.  If it expects pps and does not see it, it will not show up.
* Look for /dev/pps0, /dev/gps0 and /dev/gpspps0 as signs that things are mapped.  A useful tool if it is not working with pps is to using ppswatch or ppstest to see if something is on the port, e.g.
> sudo ppswatch -a /dev/pps0

