# Connecting the Payload to WiFi

The SandShark payloads are always configured to have a static ETHERNET IP address of 10.207.210.101.  This is because the SandShark platform is configured with a static ETHERNET IP of 10.207.210.100, and expects the payload to have the 101 subaddress.

The Raspberry Pi on the SandShark payloads have built-in wifi, which can be configured to automatically connect to a given wifi network by modifying/creating the file at '/etc/wpa_supplicant.conf'.  Networks can be added for example by:

```sh
network={
        ssid="sandshark"
        psk="password"
        priority=10
}

network={
        ssid="kayak-local-2ghz"
        psk="password"
        priority=5
}

network={
        ssid="MIT"
        key_mgmt=NONE
        priority=0
}
```

this wpa_supplicant.conf file allows the Raspberry Pi to connect to the 'sandshark' wifi network, with its given password, with a high priority, and similarly to the 'kayak-local-2ghz' and 'MIT' wifi networks.

Please install Avahi onto the payload using:

```sh
sudo apt-get install avahi-daemon
```

this will allow you to ssh into the payload using:

```sh
ssh mituser@platypus.local
```
