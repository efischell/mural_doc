# Checking the Payload Sensors using Minicom

To determine if the sensors attached to the payload Raspberry Pi USB ports are functioning properly, we can check them using minicom.  If the sensors have been setup with persistent tty ports, we can do them individually as follows.

For the CSAC, if the NTP server has been setup, we have to first switch the NTP service off using:

```sh
sudo service ntp stop
```

Then we can interrogate the CSAC using:

```sh
minicom -D /dev/ttyCSAC
```

The CSAC should have been configured to a baud rate of 9600 8N1.  Set this up in minicom, and if the CSAC has GPS, it will output:

```sh
$GPRMC,165533.00,A,4221.5928,N,07106.2896,W,0.0,0.0,260919,,*25
$GPZDA,165534.00,26,09,2019,+00,00*4A
```

You can ask the CSAC what the GPS state is by typing at its terminal:

```sh
scpi > GPS?
```

and you will get some information like:

```sh
ANTENNA DELAY:2.5e-08
PULSE SAWTOOTH:6.3
TRACKED SATS :4
VISIBLE SATS :12
ACTUAL POSITION:
N,4221.5923
W,7106.2925
-110.90 m
0.09 Knots
0.00 Degrees
GPS Receiver Status: 3D Fix
DYNAMIC MODE:AUTOMATIC(8)
DYNAMIC STATE:STATIONARY(1)
SURVEY MIN DURATION:3600
SURVEY VARIANCE LIMIT:150000
SURVEY STATUS:ACTIVE
Duration : 1280, ECEF 152854428,-446579423,427517546, 3D variance 1981570944
TIMING MODE:RSTSURV
HOLD POSITION:0,0,0
JAMMING LEVEL:23
FIRMWARE VERSION: 7.03
```

We can check the CTD in a similar way using:

```sh
minicom -D /dev/ttyCTD
```

The CTD has been configured with a baud rate of 9600 8N1.  You should see some information like:

```sh
-11.98572 16.01647 -83.22 38976
```

Similary for the WHOI micromodem, we can check it using:

```sh
minicom -D /dev/ttyMODEM
```

The micromodem has been configured with a baud rate of 19200 8N1.  It should be outputting some heartbeat information like:

```sh
$CAREV,170145,AUV,2.0.27087*13
```
