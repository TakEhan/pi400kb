# Raspberry Pi 400 as a USB HID Keyboard & Mouse <!-- omit in toc -->

Hook your Pi 400 up to your PC somehow, using a USB Type-C cable into the *power* port.
Anker make good ones- I used a 3m white one for my tests.

Our USB-C to USB-A is great if you're using a USB-A port (but make sure it's a *high power* one): https://shop.pimoroni.com/products/usb-c-to-usb-a-cable-1m-black

For the shortage of power supply, ELECOM makes a splitter that PD via USB-C and data link via USB-A: https://www.elecom.co.jp/products/MPA-CAPDBK.html

A Raspberry Pi Mouse is also supported if plugged in, eg: https://shop.pimoroni.com/products/raspberry-pi-mouse?variant=29390982119507

Following mice works: https://www.elecom.co.jp/products/M-Y8UBPN.html

This project started out life as a gist - https://gist.github.com/Gadgetoid/5a8ceb714de8e630059d30612503653f

Thank you to all the people who dropped by with kind words, suggestions and improvements.

- [YASetup](#yet-another-setup)

--Original--
- [Quickstart (Ish)](#quickstart-ish)
  - [Mouse Support](#mouse-support)
- [Building & Contributing](#building--contributing)
  - [Building](#building)
  - [Custom Mouse/Keyboard Devices](#custom-mousekeyboard-devices)


## Yet another setup
```
sudo apt install libconfig-dev git cmake
git clone https://github.com/Gadgetoid/pi400kb
cd pi400kb
git submodule update --init
mkdir build
cd build
cmake ..
make
chmod +x pi400kb
ln -s ./pi400kb /usr/sbin/pi400kbl
sudo systemctl edit --force --full pi400kb.service
```
It is need to add `dtoverlay=dwc2` to `/boot/config.txt`. 
Then reboot. 
On the terminal, type `sudo modprobe libcomposite`. 
```
cd ..
mv pi400kb.service /etc/systemd/system/pi400kb.service
mv modprobe.service /etc/systemd/system/modprobe.service
sudo systemctl enable pi400kb.service
sudo systemctl enable modprobe.service
```
Or use /etc/modules-load.d/modules.conf instead of enabling. 

However, it is recommended to test before enabling services by following commands. 
```
sudo systemctl start pi400kb.service
sudo systemctl start modprobe.service
sudo systemctl status pi400kb.service
sudo systemctl status modprobe.service
```

Press `Ctrl + Raspberry` to grab/release your keyboard and mouse, switching between local use and USB.
Press `Ctrl + Shift + Raspberry` (on the grabbed keyboard) to exit. (This exit means break of detection loop)


## Modifications
### Simply implementation 
- improve error handling #25 DeeNewcum:main
- Pass through all the devices #41 Daft-Freak:all-the-devices
- Adds the modprobe.service on startup #33 tigertank591:main

### Modification
- Modify the key to grab/release keyboard and mouse from the host #27 Shuya4
  - DeeNewcum:pi400kb


# Original
## Quickstart (Ish)

Add `dtoverlay=dwc2` to `/boot/config.txt`

Reboot!

`sudo modprobe libcomposite`

Grab the latest pi400kb for your system from releases: https://github.com/Gadgetoid/pi400kb/releases

`chmod +x pi400kb`

`sudo ./pi400kb`

:sparkles: YOUR PI 400 IS NOW A FREAKING KEYBOARD & MOUSE FOR YOUR PC WHAAAAT!? :sparkles: 

Your keyboard input will be detached from your Pi while it's forwarded to your host computer.

Press `Ctrl + Raspberry` to grab/release your keyboard and mouse, switching between local use and USB.

Press `Ctrl + Shift + Raspberry` (on the grabbed keyboard) to exit.


### Mouse Support

Pi 400 KB supports the official Raspberry Pi Mouse VID:PID = 093a:2510 by default, but other mice should work.

### Autostart

```
sudo cp pi400kb /usr/sbin/pi400kb
sudo systemctl edit --force --full pi400kb.service
```

Add the contents of the `pi400kb.service` file.

Start the service and check its status:

```
sudo systemctl start pi400kb.service
sudo systemctl status pi400kb.service
```

Enable start on boot if it's okay:

```
sudo systemctl enable pi400kb.service
```

## Building & Contributing

### Building

```
sudo apt install libconfig-dev git cmake
git clone https://github.com/Gadgetoid/pi400kb
cd pi400kb
git submodule update --init
mkdir build
cd build
cmake ..
make
```

### Custom Mouse/Keyboard Devices

CMake accepts the following build arguments to customise the VID/PID and device path for the mouse/keyboard:

* `KEYBOARD_VID` - Keyboard Vendor ID, default: 0x04d9
* `KEYBOARD_PID` - Keyboard Product ID, default: 0x0007
* `KEYBOARD_DEV` - Keyboard device path, default: /dev/input/by-id/usb-_Raspberry_Pi_Internal_Keyboard-event-kbd
* `MOUSE_VID` - Mouse Vendor ID, default: 0x093a
* `MOUSE_PID` - Mouse Product ID, default: 0x2510
* `MOUSE_DEV` - Mouse device path, default: /dev/input/by-id/usb-PixArt_USB_Optical_Mouse-event-mouse

Supply these arguments when configuring with CMake, eg:

```
cmake .. -DMOUSE_DEV="/dev/input/by-id/usb-EndGameGear_XM1_Gaming_Mouse_0000000000000000-event-mouse" -DMOUSE_VID=0x3367 -DMOUSE_PID=0x1903
```
