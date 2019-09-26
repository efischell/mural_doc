# Payload BMP280 Pressure Sensor

The BMP280 pressure sensor breakout from Adafruit is connected to pins 1,3,5,7,9 on the Raspberry Pi. These pins provide 3.3V, i2c comms, and GND.

You can check if the sensor is properly connected using:

```sh
i2cdetect -y 1
```

the output should be:

```sh
     0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f
00:          -- -- -- -- -- -- -- -- -- -- -- -- -- 
10: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
20: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
30: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
40: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
50: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
60: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
70: -- -- -- -- -- -- -- 77
```

demonstrating that the senor has ID 77. The code for the sensor can be found from Adfruit at:

https://github.com/adafruit/Adafruit_CircuitPython_BMP280

this code can be built to generate a Python script that will output the temperature, pressure, and estimated altitude of the payload.  The script has been copied to the base directory of the SandShark as 'check_payload_pressure_BMP280.py.  Before deploying the vehicle, check that the pressure is correct by running:

```sh
check_payload_pressure_BMP280.py
```

the output will look like:

```sh
temperature:23.81 C  pressure:1007.88 hPa   altitude:100.73 m
temperature:23.81 C  pressure:1007.88 hPa   altitude:100.73 m
temperature:23.81 C  pressure:1007.88 hPa   altitude:100.73 m
temperature:23.81 C  pressure:1007.88 hPa   altitude:100.73 m
temperature:23.81 C  pressure:1007.88 hPa   altitude:100.73 m
temperature:23.81 C  pressure:1007.88 hPa   altitude:100.73 m
```

The pressure at ambient is around 1000 hPa.  After pulling a vacuum, MAKE SURE THAT THE PRESSURE IS LOWER THAN THIS VALUE!
