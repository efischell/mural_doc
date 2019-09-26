# Setting up Date and Time on the Raspberry Pi

The date and time on the payload Raspberry Pi usualy moves to some time in 2029 upon CSAC sync.  Before deployments, its important to change it to the correct date and time, then allow the NTP to correctly sync.

To set the date using human readable format use (you can copy the date from a internet-connected computer):

```sh
sudo date -s "Wed Apr 25 16:42:08 EDT 2018"
```

You can force the Raspi fake-hwclock to save the current (correct) date and time using:

```sh
sudo fake-hwclock save force
```

Sometimes you may want to check that the CSAC is correctly outputting GPS date strings - stop ntp using:

```sh
sudo service ntp stop
```

then check using minicom:

```sh
minicom -D /dev/ttyCSAC
```

if its outputting the correct strings, you can then restart ntp:

```sh
sudo service ntp start
```

then check as ntp syncs:

```sh
watch ntpq -p
```

and finally, once synced properly, you can re-save the time on fake-hwclock which is saved to the file /etc/fake-hwclock.data
