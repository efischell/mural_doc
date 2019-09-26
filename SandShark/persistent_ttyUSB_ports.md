# Configuring Persitent USB Port Names on the Payload

Because the SandShark payload has a number of attached USB devices, it is important for programming purposes to be able to connect to these devices using a persistent port.  Persistent port names can be created by adding an additional udev rule in the folder '/etc/udev/rules.d/'.

For example, we create a file called '08-usb.rules' in this folder ('/etc/udev/rules.d/08-usb.rules').  The first thing to do is find unique identifying information for each of the attached devices.  You can run, for example:

```sh
udevadm info -n /dev/ttyUSB0
```

to get general information about the device.  The output may look like:

```sh
P: /devices/platform/soc/3f980000.usb/usb1/1-1/1-1.2/1-1.2:1.0/ttyUSB0/tty/ttyUSB0
N: ttyUSB0
S: serial/by-id/usb-Prolific_Technology_Inc._USB-Serial_Controller-if00-port0
S: serial/by-path/platform-3f980000.usb-usb-0:1.2:1.0-port0
S: ttyMODEM
E: DEVLINKS=/dev/ttyMODEM /dev/serial/by-id/usb-Prolific_Technology_Inc._USB-Serial_Controller-if00-port0 /dev/serial/by-path/platform-3f980000.usb-usb-0:1.2:1.0-port0
E: DEVNAME=/dev/ttyUSB0
E: DEVPATH=/devices/platform/soc/3f980000.usb/usb1/1-1/1-1.2/1-1.2:1.0/ttyUSB0/tty/ttyUSB0
E: ID_BUS=usb
E: ID_MM_CANDIDATE=1
E: ID_MODEL=USB-Serial_Controller
E: ID_MODEL_ENC=USB-Serial\x20Controller
E: ID_MODEL_FROM_DATABASE=PL2303 Serial Port
E: ID_MODEL_ID=2303
E: ID_PATH=platform-3f980000.usb-usb-0:1.2:1.0
E: ID_PATH_TAG=platform-3f980000_usb-usb-0_1_2_1_0
E: ID_REVISION=0300
E: ID_SERIAL=Prolific_Technology_Inc._USB-Serial_Controller
E: ID_TYPE=generic
E: ID_USB_DRIVER=pl2303
E: ID_USB_INTERFACES=:ff0000:
E: ID_USB_INTERFACE_NUM=00
E: ID_VENDOR=Prolific_Technology_Inc.
E: ID_VENDOR_ENC=Prolific\x20Technology\x20Inc.
E: ID_VENDOR_FROM_DATABASE=Prolific Technology, Inc.
E: ID_VENDOR_ID=067b
E: MAJOR=188
E: MINOR=0
E: SUBSYSTEM=tty
E: TAGS=:systemd:
E: USEC_INITIALIZED=6988959
```

Look for unique information in this list.  You can find specific attributes by:

```sh
udevadm info -a -n /dev/ttyUSB0 | grep '{idVendor}'
```

which will only output:

```sh
ATTRS{idVendor}=="067b"
ATTRS{idVendor}=="0424"
ATTRS{idVendor}=="1d6b"
```

identifying this device with a unique Vendor ID of '067b'.  Sometimes, when using multiple inexpensive USB-to-serial converters, there will be no unique information in this general list.  In this case, you can assign a specific USB port to a device, by looking at unique information of the device's parent (which is the USB port).  Generally, this will be the 'KERNELS' attribute, which you can discover using:

```sh
udevadm info -a -n /dev/ttyUSB0 | grep 'KERNEL' | head -n5
```

which will output:

```sh
KERNEL=="ttyUSB0"
KERNELS=="ttyUSB0"
KERNELS=="1-1.2:1.0"
KERNELS=="1-1.2"
KERNELS=="1-1"
```

different ports will have a different identifier, 1-1.2, 1-1.3, 1-1.4, etc.  With this information we can write our udev rules for our attached USB sensors at '/etc/udev/rules.d/08-usb.rules':

```sh
#CSAC has a unique product and vendor ID as identifiers
SUBSYSTEM=="tty", ATTRS{idVendor}=="0403", ATTRS{idProduct}=="6001", ATTRS{serial}=="A7043G8M", SYMLINK+="ttyCSAC"
#CTD uses generic serial-USB converter without any unique IDs - make sure it is always plugged in the correct USB port! This rule assigns a specific USB port to the CTD
KERNEL=="ttyUSB*", KERNELS=="1-1.4:1.0", SYMLINK+="ttyCTD"
#uModem uses generic serial-USB converter without any unique IDs - make sure it is always plugged in the correct USB port! This rule assigns a specific USB port to the uModem
KERNEL=="ttyUSB*", KERNELS=="1-1.2:1.0", SYMLINK+="ttyMODEM"
```

In this case, we have three attached USB devices, the CSAC (which has unique product information), and a CTD and WHOI micromodem which are attached using generic USB-to-serial devices, and so which must be identified by the USB port to which they are attached.
