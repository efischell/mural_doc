# BlueROV2

## Configuration

### Network

* companion computer (RaspberryPi): pi@192.168.2.2 / kangaroo.local
* payload computer (upBoard): mituser@192.168.2.3 / joey.local
* sonar (mb2250): 192.168.2.4

## Notes 

* there is an air passage between the MEH and the battery compartment through the power cable; make sure to close and plug the battery compartment before pulling a vaccuum on the MEH

## Tests

### Pressure monitoring

ssh into the companion computer
```sh
> ssh pi@192.168.2.2
```

determine the pid for the mavlink process
```sh
> ps a | grep -i mavproxy
pi         606  0.0  0.2   5204  2148 ?        Ss   19:47   0:00 SCREEN -dm -S mavproxy /home/pi/companion/scripts/start_mavproxy_telem_splitter.sh
pi         611  0.0  0.0   1912   376 pts/0    Ss+  19:47   0:00 /bin/sh /home/pi/companion/scripts/start_mavproxy_telem_splitter.sh
pi         612 17.6  1.8  39680 16688 pts/0    Sl+  19:47   0:51 /usr/bin/python /usr/local/bin/mavproxy.py --master=/dev/serial/by-id/usb-3D_Robotics_PX4_FMU_v2.x_0-if00,115200 --source-system=200 --cmd=set heartbeat 0 --out udpin:localhost:9000 --out udpbcast:192.168.2.255:14550
pi         765  0.0  0.1   4252  1756 pts/5    S+   19:51   0:00 grep --color=auto -i mavproxy

```

re-attach to the mavlink screen session
```sh
screen -r <pid>
```

run the status command and look for the `SCALED_PRESSURE` and `SCALED_PRESSURE_2` messages
``` sh
MANUAL> status
1316: SCALED_PRESSURE {time_boot_ms : 341314, press_abs : 975.212341309, press_diff : 16.7780456543, temperature : 3658}
1316: SCALED_PRESSURE2 {time_boot_ms : 341314, press_abs : 1004.27966309, press_diff : -8.97031211853, temperature : 2353}
```

## See also

* [MAVProxy commands](https://ardupilot.github.io/MAVProxy/html/uav_configuration/system.html)
* [MAVLink messages](https://mavlink.io/en/messages/common.html)
