# Raspberry Pi Zero SIM Router

Document explaining how to build your own Raspberry Pi Zero router
supporting SIM cards for mobile Internet.

## Prerequisites

### Hardware

1. Raspberry Pi Zero 2 W
2. Waveshare SIM7070G NB-IoT / Cat-M / GPRS / GNSS HAT
3. Waveshare Uninterruptible Power Supply UPS HAT For Raspberry Pi Zero 5V Power
4. SD Card XXX
5. Mobile Internet SIM card

### Software

1. pi-imager

## Assemble

## Install Software

1. Install pi-imager and flash the Raspberry Pi OS onto an SD card.
2. Boot your Raspberry Pi Zero 2 W (connect it to a screen and connect
   a mouse and keyboard to it).
3. Go to Menu (raspberry logo) -> Preferences -> Raspberry Pi Configuration.
4. Select Interfaces tab and enable SSH and click OK.
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
- https://www.raspberrypi.com/tutorials/host-a-hotel-wifi-hotspot/

## Author

Nil Geisweiller
