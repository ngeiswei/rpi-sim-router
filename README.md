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

### Install Raspberry Pi OS and Update

1. Install pi-imager and flash the Raspberry Pi OS onto an SD card.
2. Boot your Raspberry Pi Zero 2 W (connect it to a screen and connect
   a mouse and keyboard to it).
3. Connect to the Internet via WiFi.  You should be able to do it via
   the desktop interface.  Otherwise, if you prefer the terminal, you
   can do the following
   3.1. Scan for nearby WiFi network:
   ```bash
   nmcli dev wifi list
   ```
   and note the SSID the network you wish to connect to.
   3.2. Connect to you network:
   ```bash
   sudo nmcli --ask dev wifi connect <YOUR-NETWORK-SSID>
   ```
   3.3. You can verify that your properly connected with
   ```bash
   nmcli connection
   ```
4. Upgrade your packages and install ppp and minicom
   ```bash
   sudo apt update
   sudo apt dist-upgrade
   sudo apt install ppp minicom
   ```
5. Disable window manager at start-up:
   - go to `Main Menu > Preferences > Raspberry Pi Configuration`.
     Under the `System` tab select `Boot: To CLI`.
6. It is recommended that you create passwords for root as well as
   yourself, but it's up to you.
7. Reboot.

### Enable SIM7070G

Once rebooted you may process as follows.

1. Enable serial port
   ```bash
   raspi-config
   ```
   - Select `3 Interface Options` -> `I6 Serial Port`.  To the
     questions "Would you like a login shell to be accessible over
     serial?" answer `<No>`.  "Would you like the serial port hardware
     to be enable?"  answer `<Yes>`.
   - Exit and reboot.
2. NEXT: Study GPIO and pinctrl
   /boot/firmware/config.txt gpio=4=op,dl
   For now use the USB cable
3. Enable the SIM7070G
   ```bash
   mmcli --list-modems
   ```
   You should get something like
   ```
   /org/freedesktop/ModemManager1/Modem/0 [SIMCOM_Ltd] SIMCOM_SIM7070
   ```
   Print modem information to make sure you are tareting the correct device
   ```bash
   mmcli --modem=0
   ```
   You should get something like
   ```
   ----------------------------------
   General  |                   path: /org/freedesktop/ModemManager1/Modem/0
   ...
   ----------------------------------
   Hardware |           manufacturer: SIMCOM_Ltd
            |                  model: SIMCOM_SIM7070
   ...
   ```
   Finally enable the SIM7070G as follows
   ```bash
   mmcli --modem=0 --enable
   ```

### Enable Mobile Internet

1. Check that the SIM7070G is properly recognized by the network manager
   ```bash
   nmcli device
   ```
   You should see something like
   ```
   DEVICE         TYPE      STATE                CONNECTION
   ...
   ttyUSB2        gsm       disconnected         --
   ...
   ```
2. Create a connection
   ```bash
   sudo nmcli connection edit type gsm con-name "Mobile Internet"
   ```
   You should enter the shell nmcli
   ```
   nmcli>
   ```
   Set the APN of your network
   ```nmcli
   set gsm.apn <APN>
   ```
   Then save
   ```nmcli
   save
   ```
   and exit.
3. Connect to the Internet via the SIM7070G
   ```bash
   sudo nmcli device connect ttyUSB2
   ```
4. Test if you are connected to the Internet
   ```bash
   ping google.com
   ```

### Create a hotspot

1. Create hostspot network
```bash
sudo nmcli device wifi hotspot ssid <HOTSPOT_NAME> password <HOTSPOT_PASSWORD> ifname wlan0
```
2. Enable hotspot at start up.
   - First get UUID of the hotspot
   ```bash
   nmcli connection
   ```
   It should outputs something like
   ```
   NAME     UUID            TYPE  DEVICE
   Hotspot  <HOTSPOT_UUID>  wifi  wlan0
   ...
   ```
   - Show information about the hotstop
   ```bash
   nmcli connection show <HOTSPOT_UUID>
   ```
   It will show a lot of information, look for
   ```
   connection.autoconnect:                 no
   connection.autoconnect-priority:        0
   ```
   - Enable autoconnect and set high priority
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
3. Reboot and make sure that you are connected to both the mobile
   internet and the hotspot.
   ```bash
   nmcli connection
   ```
   which should give you something like
   ```
   NAME             UUID                      TYPE       DEVICE
   Hotspot          XXXXX                     wifi       wlan0
   Mobile Internet  XXXXX                     gsm        ttyUSB2
   ```

## Acknowledgements

The following resources were used to produce this document
- https://www.makeuseof.com/connect-to-wifi-with-nmcli/
- https://blog.zero-iee.com/en/posts/nb-iot-internet-connection-with-simcom-sim7070g-modem/
- https://www.raspberrypi.com/tutorials/host-a-hotel-wifi-hotspot/
- https://unix.stackexchange.com/questions/113975/configure-gsm-connection-using-nmcli/268285#268285
- https://superuser.com/questions/1043141/configuring-a-ppp-device-with-networkmanager-nmcli

## Author

Nil Geisweiller
