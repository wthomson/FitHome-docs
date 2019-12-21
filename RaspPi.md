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

# Headless Config
## Connect to Rasp Pi
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
### Update
Blindly following recommendations,
- `sudo apt-get update`   
- `sudo apt-get upgrade`
### Mount Drive
- Install [SSHFS](https://osxfuse.github.io/). 
- Create a directory to mount to (e.g.: `/users/mj/mount`).
- Open a terminal window and run (replace the raspPi IP address and mount point) `sshfs pi@192.168.86.209: /users/mj/mount`
#### Unmount
Sometimes the mount gets into a state of limbo.  When that happens, this command seems to work: `sudo umount -f /users/mj/mount`.
# Update Python Stuff
`python3 --version` shows the version of python 3 is 3.7.3.
- Install venv `sudo apt-get install python3-venv`
- The python-dotenv is we're using flask.  It picks up environment variables from .flaskenv and .env... `pip3 install python-dotenv`

## Install MongoDB
If using mongodb:  
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
# Onwards...

The `/home/pi` directory of the RaspPi will be mounted as a drive in Finder.
# Remote VS Code
YIPPEE! VS Code supports [remote development using SSH](https://code.visualstudio.com/docs/remote/ssh).  By going through the steps to get this going, we can debug the Python apps running on the Rasp Pi.  

The first thing to do is set up the Rasp Pi for passwordless SSH access as described [in this blog post](https://www.raspberrypi.org/documentation/remote-access/ssh/passwordless.md).

Next go into the VS Code marketplace and install the [Remote Development Extension Pack](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.vscode-remote-extensionpack).

Once that's done, start up a remote SSH session by going into the commands (through F1) and choose `Remote SSH: Connect to Host`.
# OOPS - Can't get to Rasp Pi
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
   
