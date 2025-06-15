# Raspberry Pi Zero SIM Router

Document explaining how to build your own Raspberry Pi Zero router
supporting SIM cards for mobile Internet.

## Prerequisites

### Hardware

1. Raspberry Pi Zero 2 W
2. Optionally a slim Heatsink.
3. Waveshare SIM7070G NB-IoT / Cat-M / GPRS / GNSS HAT
4. Waveshare Uninterruptible Power Supply UPS HAT For Raspberry Pi Zero 5V Power
5. SD Card XXX
6. Mobile Internet SIM card

### Software

1. pi-imager

## Install Hardware

1. Connect the battery of the UPS HAT to the UPS HAT.  You can hold
   the battery in place with a small rubber band.
2. Screw the UPS HAT below the Raspberry Pi Zero, where the soldering
   is visible.  Make sure the six pins of the UPS HAT are in contact
   with the solderings of the 6 first pins of the GPOI.
3. Place the SIM card inside the SIM7070G.
4. Insert the SIM7070G on top of the Raspberry Pi Zero.  Check for
   misalignment as a measure of precaution.
5. Screw the antenna to the SIM7070G.

## Install Software

1. Install pi-imager and flash the Raspberry Pi OS onto an SD card.
2. Boot your Raspberry Pi Zero 2 W (connect it to a screen and connect
   a mouse and keyboard to it).
3. Connect to the Internet via WiFi.  You should be able to do it via
   the desktop interface.  Otherwise, if you prefer the terminal, you
   can do the following
   3.1. Scan for nearby WiFi network:
   ```
   nmcli dev wifi list
   ```
   and note the SSID the network you wish to connect to.
   3.2. Connect to you network:
   ```
   sudo nmcli --ask dev wifi connect <YOUR-NETWORK-SSID>
   ```
4. Upgrade your packages and install ppp
```
sudo apt update
sudo apt dist-upgrade
sudo apt install ppp
```
5. 
4. Enable the SIM7070G
   4.1. Launch `raspi-config` (you probably need to be root)
   4.2. Select `3 Interface Options` -> `I6 Serial Port`.  To the questions
   "Would you like a login shell to be accessible over serial?" answer `<No>`.
   "Would you like the serial port hardware to be enable?" answer `<Yes>`.
   4.2. Exit and reboot.
5. Create hostspot network
```bash
sudo nmcli device wifi hotspot ssid <HOTSPOT_NAME> password <HOTSPOT_PASSWORD> ifname wlan0
```
6. Enable hotspot at start up.
   6.1. First get UUID of the hotspot
   ```bash
   nmcli connection
   ```
   It should outputs something like
   ```
   NAME     UUID            TYPE  DEVICE
   Hotspot  <HOTSPOT_UUID>  wifi  wlan0
   ...
   ```
   6.2. Show information about the hotstop
   ```bash
   nmcli connection show <HOTSPOT_UUID>
   ```
   It will show a lot of information, look for
   ```
   connection.autoconnect:                 no
   connection.autoconnect-priority:        0
   ```
   6.3. Enable autoconnect and set high priority
   ```bash
   sudo nmcli connection modify <HOTSPOT_UUID> connection.autoconnect yes connection.autoconnect-priority 100
   ```
   Make sure it has been properly set
   ```bash
   nmcli connection show <HOTSPOT_UUID>
   ```
   It should show
   ```
   connection.autoconnect:                 yes
   connection.autoconnect-priority:        100
   ```

## Acknowledgements

The following resources were used to produce this document
- https://www.makeuseof.com/connect-to-wifi-with-nmcli/
- https://blog.zero-iee.com/en/posts/nb-iot-internet-connection-with-simcom-sim7070g-modem/
- https://www.raspberrypi.com/tutorials/host-a-hotel-wifi-hotspot/

## Author

Nil Geisweiller
