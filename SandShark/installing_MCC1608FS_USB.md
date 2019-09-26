# Installing and Using the MC USB 1608FS-Plus

#### 1) 
Plug in USB to unit.  If using a USB hub, the unit must be provided the full amperage of a USB port- this may require a powered usb hub or powering the power pins on the USB seperately.

#### 2) 
Connect PPS line (from GPS or CSAC) to pin 37, connect ground to pin 40.

#### 3) 
Connect hydrophone inputs (outputs from amp/filter board) to pin pairs 1/2, 3/4, 5/6 and 7/8.  The odd pin is the signal, even is ground.  If you are skipping the amp/filter board, it is necessary to convert the current sense hydrophones to connect a 510ohm resistor between the white wire (signal) and ground.  The signal wire then goes into the odd pin, ground to the even. The red wire should be connected to +12V.

#### 4) 

The code to record signal using PPS-triggered aquisition is located in lamss-internal/src/tools/read_mcc_1608FS_plus/.  This is commented out in the CMakeLists.txt of lamss-internal/src/tools/read_mcc_1608FS_plus/ because the dependencies are specific and some need to be compiled from source.  If it is commented out, comment it in and rebuild.  On the beaglebone black, the dependencies are installed and the line in the CMakeLists.txt is uncommented, but if you revert the change to it will be lost.  

To prep a new computer, follow the instructions found here ("Download the Linux and MCC Drivers for the Raspberry Pi" and "Install the Linux and MCC Drivers for Raspberry Pi" are generic and work:
http://www.mccdaq.com/TechTips/TechTip-9.aspx (USB1608FS_softwareinstall.pdf).  The instructions (specific to our application) are also shown below.
 
#### 5) 
Run:

```sh
./read_mcc_1608FS_plus
```

This will write files to "missions-lamss/.tmp/MCC1608FS/", unless you change the variable TEMPDIR in read_mcc_1608FS_plus.h

The files contain plain text float values pulled from the first four channels of the unit.  These files can then be used by another process handling signal processing.

Drivers from:
ftp://lx10.tx.ncsu.edu/pub/Linux/drivers/USB/


### Prepping a new computer (based on http://www.mccdaq.com/TechTips/TechTip-9.aspx):

#### 0) 

Download and install dependencies: non-packaged dependencies can be gotten via ftp (see USB1608FS_softwareinstall.pdf), or are located in lamss-shared/src/tools/read_mcc_1608FS_plus/dependencies/ (up to date as to 4/20/2016).

```sh
sudo apt-get install libusb-dev
sudo cp lamss-internal/src/tools/read_mcc_1608FS_plus/dependencies/61-mcc.rules /etc/udev/rules.d
```

#### 1) 
Reboot computer to make rules.d take effect.

#### 2) 
Install linux drivers:

libhid:

```sh
cd
tar zxvf lamss-internal/src/tools/read_mcc_1608FS_plus/dependencies/libhid-0.2.16.tar.gz
cd libhid-0.2.16
./configure
sudo make
sudo make install
sudo depmod –a
```
MCC libraries:

```sh
cd
tar zxvf lamss-internal/src/tools/read_mcc_1608FS_plus/dependencies/MCCLIBHID.1.63.tgz
cd mcc-libhid
sudo make
sudo make install
sudo ldconfig
```

Run test app to make sure it works:

```sh
cd ~/mcc-libhid
./test-usb1608FS-Plus
```

You should now be able to build and run the read_mcc_1608_plus application. 

(Note: make sure whatever code you run shuts it down properly. If not, you'll have to unplug and plug.) 
