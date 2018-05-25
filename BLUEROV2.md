# BlueROV2

## Network configuration

* companion computer (RaspberryPi): pi@192.168.2.2 / kangaroo.local

* payload computer (upBoard): mituser@192.168.2.3 / joey.local

* sonar (mb2250): 192.168.2.4

## Tests

### Pressure monitoring

ssh into the companion computer
```sh
ssh pi@192.168.2.2
```

determine the pid for the mavlink process
```sh
ps a | grep -i mavlink
```

re-attach to the mavlink screen session
```sh
screen -r <pid>
```

run the status command and look for the `SCALED_PRESSURE` and `SCALED_PRESSURE_2` messages
