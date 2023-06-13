# Raspberry Pi
The [Raspberry-pi](https://www.raspberrypi.org/) is a popular single board Linux computer that is perfect for edge computing cases such as FishNET.  Its low power footprint, on board video capture and image processing capabilities inspired our selection of this device for FishNET.

![](raspi.jpg)

Raspberry Pi's come in a variety of versions.  We chose to use the Pi 3B because of its balance of computing capabilities and low power usage.  The Pi 3 draws around 360 mA at 5 V under normal operations with WiFi disabled ([1](https://www.raspberrypi-spy.co.uk/2018/11/raspberry-pi-power-consumption-data/)).  This comes out to around 1.8 Watts.

## Setup
We recommend installing [Raspberry Pi OS Lite](https://www.raspberrypi.com/software/operating-systems/) on your Pi to avoid installing unnecessary software that could increase your power consumption.

Flash the Pi's micro SD card using the [Raspberry Pi Imager Utility](https://www.raspberrypi.com/news/raspberry-pi-imager-imaging-utility/).  Before inserting the SD card into the Pi, open it on another computer.  Enter the boot directory and create a file called `ssh`.  This will enable ssh automatically when the Pi boots up for the first time.

Insert the SD card into the Pi and power it on.  Once it boots up, connect it to another device over a direct Ethernet connection.  In the other device's network settings, locate the Ethernet connection to the Pi.  Make sure that you enable network sharing over this connection.  This can be done differently in [Windows](https://www.tomshardware.com/how-to/share-internet-connection-windows-ethernet-wi-fi), [Linux](https://medium.com/@TarunChinmai/sharing-internet-connection-from-a-linux-machine-over-ethernet-a5cbbd775a4f), and [MacOS](https://www.macworld.com/article/352173/how-to-share-a-wi-fi-connection-on-one-mac-over-ethernet-to-another.html).  This ensures that the Pi can access the internet during setup without needing to enable WiFi.  

Establish an ssh connection to the pi using the following command:
```bash
ssh [hostname]@[hostname].local
```
where the default username is `pi` and the default hostname is `raspberrypi`.  When prompted for the password, use the default `raspberry`.  To ensure a secure connection, change the hostname and password upon logging in.

Connect the Pi Camera to the Pi using the camera module port (see below).

![](raspi-cam-port.jpg)

On the Pi, open a terminal or ssh session.  Enter the following command to enable the camera module:
```bash
sudo raspi-config
```
Go to `Interface options` and select `Camera`.  Make sure it says enabled.

To test the camera, run the following commands.  These will save a picture from the camera to the desktop.
```bash
raspistill -o Desktop/image.jpg
```
To view the image, use [TCP](https://linuxize.com/post/how-to-use-scp-command-to-securely-transfer-files/) to transfer it from the Pi to the device used to ssh to it.  On the device used to ssh to the pi, open a new terminal.  Run the following command to transfer the file using TCP:
```bash
scp [username]@[hostname].local:Desktop/image.jpg /path/to/save/file
```
You can now view the captured image on the other device in your choice of image viewing software.  The `scp` command is utilized often in this project to quickly and easily transfer images and videos stored on the camera Pi to other devices heedlessly.

## Reducing Power Consumption

There are several services running by default on the Pi that are unnecessary for headless operation of the camera.  These services draw additional power and reduce the efficiency of the system.  They are listed below for reference, with instructions to disable them if you so choose.

- Disabling onboard WiFi
```bash
sudo systemctl disable wpa_supplicant
sudo reboot
```
- Disabling onboard Bluetooth
```bash
sudo systemctl disable hciuart.service
sudo systemctl disable bluealsa.service
sudo systemctl disable bluetooth.service
```
- Disabling HDMI
```bash
sudo xset -display :0.0 dpms force off
```
- Disabling the Desktop
```bash
sudo raspi-config
```
Go to `System Options` > `Boot / Auto Login` and select `Console`.  Upon the next reboot the desktop will be disabled.  **NOTE**, performing this step will prevent you from interfacing with the Pi over VNC, so do not disable the desktop if you would like to use VNC.

## Software on the Pi
Thanks to the Pi's versatile computing capabilities, it can serve various needs within the FishNET framework:  
- The Pi can record video at regular intervals and store the footage locally.  The video is offloaded by connecting to the Pi directly after the deployment is complete.  This is the most straightforward approach and what we implemented for our first deployment in a remote waterway environment.  Example code to capture video at regular intervals can be found [here](recording.md).
- The Pi can stream video to streaming services such as YouTube.  This enables constant, 24/7 footage capture, ensuring the maximum possible odds of capturing fish.  This solution does require a wireless radio setup as described in [connectivity](connectivity.md), or for the pi to be connected directly to the internet.  The AI back end can then be configured to run the live stream through the fish detection model.  Example code for this set up can be found [here](livestreaming.md).
- The Pi can run the AI back end directly, only saving footage that it determines to contain a fish.  This approach requires Tensorflow 2 to be installed on the Pi, and a local video stream for the back end to monitor.  This can be done, but will drastically increase the Pi's power usage.  A Pi 4 will be needed for the best results, further hurting efficiency.  This approach does have the benefit of not needing a separate back end to filter images of fish.
