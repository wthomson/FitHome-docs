We are far from experts when it comes to using the Rasp Pi.  Here we document the goop we do each time we start up a new Rasp Pi.

# Installation
- Put the micro-SD [e.g.: cheap one on Amazon](https://www.amazon.com/gp/product/B004ZIENBA/ref=as_li_ss_tl?ie=UTF8&psc=1&linkCode=sl1&tag=bitknittingwo-20&linkId=923f12067ad3395ed04f043c37d8c39f)  that will hold the Rasp Pi image into an SD Card reader (on our Mac).
- Format using SD-Formatter.
- Download a [Rasp Pi image](https://www.raspberrypi.org/downloads/raspbian/)
- Run Etcher to copy the image onto the SD Card.
- Add "SSH" file to the root of the image.  We do this by opening a terminal on the boot partition and typing `$touch ssh` 
- Create the `wpa_supplicant.conf` file : `$touch wpa_supplicant.conf`.  Copy the contents into the file `nano wpa_supplicant.conf`:  
```
country=US
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1

network={
    ssid="YOURSSID"
    psk="YOURPWD"
}
```
Changing the ssid and psk to match your network.
- 'safely' remove the SD-card.
- Put the SD-card into the Rasp-Pi's micro-SD port
- Power up the Rasp Pi.  Hopefully wireless is working!

# Headless Config - Connect to Rasp Pi
These steps are covered on this [web page](https://itsfoss.com/ssh-into-raspberry/).  

- Figure out the local net our Rasp-Pi is on.
  - Get our local IP address: `ifconfig | grep inet`.  This command gives us:  
    
```
inet 192.168.86.233 netmask 0xffffff00 broadcast 192.168.86.255
inet 192.168.2.1 netmask 0xffffff00 broadcast 192.168.2.255
```
   192.168.2.1 is the [IP address of a router in a home network](https://192-168-1-1ip.mobi/192-168-2-1/).  This means the Rasp-Pi is on 192.168.86.
- Discover Rasp Pi's local IP using Angry IP by scanning 192.168.86.0 to 192.168.86.255.  e.g.: The local IP address on ours was `192.168.86.209`
- ssh e.g.: `ssh pi@192.168.86.209`
- The initial username is `pi` and the initial password is `raspberry`
## Update
Blindly following recommendations,
- `sudo apt-get update`   
- `sudo apt-get upgrade`

# Install MongoDB
To install mongodb:  
```
sudo apt install mongodb
sudo systemctl enable mongodb
```
- We "downgraded" to this version of pymongo. `pip3 install pymongo==3.4.0`
__NOTE: We needed to use this version of pymongo.  If not, we'd get an error:__
```
An error occurred: Server at localhost:27017 reports wire version 0, but this version of PyMongo requires at least 2
(MongoDB 2.6).
```
There's [more on mongodb in this post](Posts/UsingMongoDB.md)
# Install Blinka
Adafruit has come through for us once again with their [Blinka library](https://learn.adafruit.com/circuitpython-on-raspberrypi-linux/circuitpython-raspi).  The Blinka library will make it much easier for us to talk to the Energy monitor over SPI from our Rasp Pi.

Adafruit has provided us with [installation instructions](https://learn.adafruit.com/circuitpython-on-raspberrypi-linux/installing-circuitpython-on-raspberry-pi).



-> TODO: Undo this gunk.
# Enable SPI
The Rasp Pi and Energy Monitor communicate over SPI.  You may want to watch TonyD's video if you are not familiar with SPI on the Raspberry Pi: [_Raspberry Pi & Python SPI Deep Dive with TonyD!_](https://www.youtube.com/watch?v=bHpnu1te0uU).  Another resource is [the Raspberry Pi documenation on SPI](https://www.raspberrypi.org/documentation/hardware/raspberrypi/spi/README.md)

 SPI is not enabled by default.  To enable SPI:
- Get into an remote ssh session with your Rasp Pi.
- Turn on the interface by:
```
$ sudo raspi-config
```
  
...then get into interfacing options.  Choose SPI and choose Yes to turn on the interface.  Exit the UI.  

- Check to see that the SPI drivers are running on the Raspberry Pi:
```
$ lsmod | grep spi
spidev                  7373  0
spi_bcm2835             7596  0  
  
$ ls /dev/spi*
/dev/spidev0.0  /dev/spidev0.1
```
## Install spidev
To access SPI from Python, install spidev: 
```
$ pip3 install spidev
```
## Install Python Extensions Wrapper
spidev requires the Python Extension wrapper. This can be installed by:
```
$ sudo apt-get install python3-dev
```

# Enable Remote VS Code
YIPPEE! VS Code supports [remote development using SSH](https://code.visualstudio.com/docs/remote/ssh).  By going through the steps to get this going, we can debug the Python apps running on the Rasp Pi through VS Code.  E-X-C-I-T-I-N-G!

The first thing to do is set up the Rasp Pi for passwordless SSH access as described [in this blog post](https://www.raspberrypi.org/documentation/remote-access/ssh/passwordless.md).

Next go into the VS Code marketplace and install the [Remote Development Extension Pack](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.vscode-remote-extensionpack).

Once that's done, while in VS Code - start up a remote SSH session by going into the commands (through F1) and choose `Remote SSH: Connect to Host`.  And YIPPEE!
# Other Stuff
Here's some stuff that may or may not be useful.
## Mount Drive
While it is the most useful to use the remote VS Code goop, there are times when it is nice to see the Rasp Pi drive from the finder.  To do this, we use SSHFS.
- Install [SSHFS](https://osxfuse.github.io/). 
- Create a directory to mount to (e.g.: `/users/mj/mount`).
- Open a terminal window and run (replace the raspPi IP address and mount point) `sshfs pi@192.168.86.209: /users/mj/mount`  
  
The `/home/pi` directory of the RaspPi will be mounted as a drive in Finder.
#### Unmount
Sometimes the mount gets into a state of limbo.  When that happens, this command seems to work: `sudo umount -f /users/mj/mount`.
_____________________

This happened to us (grrrrrrr)...
## No SSH, Won't connect to wifi
We got our Rasp Pi in such a tizzy that we couldn't figure the magic incantations to make it all better (a warning to us explorers who blindly trust a blog post about `ufw`).  Luckily we were able to mount the drive on our Mac following [these directions](https://www.jeffgeerling.com/blog/2017/mount-raspberry-pi-sd-card-on-mac-read-only-osxfuse-and-ext4fuse):
```
sudo mkdir /Volumes/rpi
brew cask install osxfuse
brew install ext4fuse
diskutil list
```
Now here is where it gets a tad tricky figuring out what partition ID we want to mount.
```
/dev/disk3 (internal, physical):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:     FDisk_partition_scheme                        *63.9 GB    disk3
   1:             Windows_FAT_32 boot                    268.4 MB   disk3s1
   2:                      Linux                         63.6 GB    disk3s2
```
Our SD Card reader is internal.  We want the Linux partition.  So the ID is `disk3s2`
```
sudo ext4fuse /dev/disk3s2 /Volumes/rpi -o allow_other
```
now we can access the file from Terminal.

At least we can get the files off the SD card!





   
