# PlugE
This project is called PlugE because of it's focus around the TP-Link HS110 Smart Plug.

The purpose of the PlugE project is to collect energy readings from [TP-Link HS110 Smart Plugs](https://amzn.to/2MFSVmH) and send them to a Firebase database project.
# Hardware/Software
- [TP-LINK HS110](https://amzn.to/2WBHPUc)
- [GadfetReactor pyHS100](https://github.com/GadgetReactor/pyHS100) Python Library 
- [Raspberry Pi](RaspPi.md)
# Server
The RaspPi Server runs in the homeowner's house.
- __Set IP address (TBD)__
# TP-Link HS110 Installation 
Follow [these instructions](https://www.tp-link.com/us/support/faq/946/).
- 
# Rasp Pi Installation
- Put the micro-SD [e.g.: cheap one on Amazon](https://www.amazon.com/gp/product/B004ZIENBA/ref=as_li_ss_tl?ie=UTF8&psc=1&linkCode=sl1&tag=bitknittingwo-20&linkId=923f12067ad3395ed04f043c37d8c39f)  that will hold the Rasp Pi image into an SD Card reader (on our Mac).
- Format using SD-Formatter.
- Download a [Rasp Pi image](https://www.raspberrypi.org/downloads/raspbian/)
- Run Etcher to copy the image onto the SD Card.
- Add "SSH" file to the root of the image.  We do this by opening a terminal on the boot partition and typing `$touch ssh` 
- Create the `wpa_supplicant.conf` file and copy to the root of the image.  We used [the step 4 guide in this post](https://desertbot.io/blog/headless-raspberry-pi-3-bplus-ssh-wifi-setup).
- Put the SD-micro into the Rasp-Pi's micro-SD port
- Power up the Rasp Pi.  Hopefully wireless is working!
## Update
Blindly following recommendations,
- `sudo apt-get update`   
- `sudo apt-get upgrade`
# Headless Config
## Connect to Rasp Pi
These steps are covered on this [web page](https://itsfoss.com/ssh-into-raspberry/)
- Figure out the local IP of the Rasp Pi.  We used:
- Get our local IP address: `ifconfig | grep inet`
- Discover Rasp Pi's local IP using Angry IP.  e.g.: The local IP address on ours was `192.168.86.209`
- ssh e.g.: `ssh pi@192.168.86.209`
- The initial username is `pi` and the initial password is `raspberry`
### Mount Drive
- Install [SSHFS](https://osxfuse.github.io/). 
- Create a directory to mount to (e.g.: `/users/mj/mount`).
= Open a terminal window and run (replace the raspPi IP address and mount point) `sshfs pi@192.168.86.209: /users/mj/mount`
#### Unmount
Sometimes the mount gets into a state of limbo.  When that happens, this command seems to work: `sudo umount -f /users/mj/mount`.
# Update Python Stuff
`python3 --version` shows the version of python 3 is 3.7.3.
- Install venv `sudo apt-get install python3-venv`
- Get the python3 version of pip `sudo apt-get install python3-pip`

The `/home/pi` directory of the RaspPi will be mounted as a drive in Finder.
# Create Project
Our project -PlugE-gets energy readings from the [TP-Link HS110 Smart Plug](https://smile.amazon.com/gp/product/B0178IC5ZY/ref=ppx_yo_dt_b_asin_title_o08_s00?ie=UTF8&psc=1)
- Create the `pi@raspberrypi:~/projects/PlugE` directory.
- Create a venv `python3  -m venv venv --prompt PlugE`
- Activate the venv `source venv/bin/activate`
- Note python being used `which python` (should be venv)
# Copy Library
- Copy directory `pyHS100` from [GitHub](https://github.com/GadgetReactor/pyHS100)
# Open VS Code
We use VS Code for our editor.  After using SSHFS to mount the Rasp Pi directory into our filesystem, we go into the projects directory we created (`/home/pi/projects`), and opened the PlugE directory within VS Code.  This way, we can edit/save in VS Code.
# Environment Variables
We create environment variables for:
* the path to the python libraries
* the Firebase project ID.  
e.g.:
```
LIB_PATH = /home/pi//home/pi/projects/PlugE
PROJECT_ID = fithome-<blahblah>  
```
## Production
For production, we needed a way to pass environment variables into the systemd service file.  [Here's](https://github.com/BitKnitting/should_I_water/wiki/systemd-services#environment-variables) where I talk about using environment variables with systemd.
## Test
For test, I put the environment variables into a shell script (e.g.: set_env.sh).  e.g.:  
```export LIB_PATH='/home/pi//home/pi/projects/PlugE'```  
Then (here's the tricky part), instead of running the local script with ./, run it with .e.g.:  
```. set_env.sh```
Run ```env``` to make sure the environment variables were set within the test session.
# Running Code
We run all code within the venv on the Rasp Pi.  To run repl, we first activate the virtual environment: `pi@raspberrypi:~/projects/PlugE $ source venv/bin/activate`  
  
# Try Simple stuff
On command line, start a python session: `$python3`.  This starts up REPL
```
>>> import pyHS100
>>> from pyHS100 import Discover
>>> for dev in Discover.discover().values():
...     print(dev)
<SmartPlug model HS110(US) at 192.168.86.210 (Energy plug), is_on: True - dev specific: {'LED state': False, 'On since': datetime.datetime(2019, 10, 3, 22, 58, 44, 796107)}>
  
>>> from pyHS100 import SmartPlug, SmartBulb
>>> from pprint import pformat as pf
>>> 
>>> plug = SmartPlug("192.168.86.210")
>>> print("Current consumption: %s" % plug.get_emeter_realtime())  
Current consumption: {'current': 15.947763, 'voltage': 115.332946, 'power': 1790.277694, 'total': 0.008}
```
_Note: Not sure what `total` is?_
# Plugs library
We created [plugs_lib.py](https://github.com/BitKnitting/FitHome_PlugE/blob/master/PlugE/plugs_lib.py) to focus on our requirement to continually get readings from the HS110 and send these readings to a Firebase Project.
# Systemd Service
We use [systemd](https://en.wikipedia.org/wiki/Systemd) to run PlugE in the background on our Rasp Pi.
## New to SystemD
Not being fluent systemd users, we found the following info useful:
* [Intro to systemd video](https://youtu.be/AtEqbYTLHfs?t=147).  
  * [Place in the video where using commands starts](https://youtu.be/AtEqbYTLHfs?t=230).
* [systemd in Raspberry Pi documentation](https://www.raspberrypi.org/documentation/linux/usage/systemd.md)
* [Article on how to autorun service using systemd](https://www.raspberrypi-spy.co.uk/2015/10/how-to-autorun-a-python-script-on-boot-using-systemd/).
## Make sure to...
* set permissions so systemd can execute the script: ```sudo chmod +x {python script}```
* copy service file to where systemd expects it to be.  systemd service files are located at ```/lib/systemd/system```.  Once we created my .service file, I copied it there and tried out some of the commands. e.g.: ```sudo happyday-collect.service /lib/systemd/system/.```
* enable the service with ```sudo systemctl enable PlugE.service```.
* check to make sure the service has been enabled with ```systemctl is-enabled PlugE.service```
* start the service with ```sudo systemctl start PlugE.service```.
* check to make sure the service has been started with ```systemctl is-active PlugE.service```
See the ```systemd status``` command info below to debug why your service did not start.
## Debugging
If `$ systemctl status PlugE.service` returns
```
PlugE.service - Collect and send power readings.
   Loaded: loaded (/lib/systemd/system/PlugE.service; enabled; vendor preset: enabled)
   Active: failed (Result: exit-code) since Sun 2019-10-13 21:03:49 BST; 22s ago
 Main PID: 2813 (code=exited, status=1/FAILURE)
 ```  
 Check the debug records with `journalctl _PID=2813` = where the PID comes from the Main PID.  This shows `ModuleNotFoundError: No module named 'deprecation'`.  This error is occuring because the deprecation library was PIP'd for the virtual environment.  To fix this, we exected the virtual environment (`$deactivate`) and `pip3 install deprecation`.  We were then able to start the PlugE.service and verified by looking at the status.
 ## Changes to Code and Systemd
 Changes made to the systemd service (in this case the Python code), requires the command `systemctl daemon-reload` to make systemd aware of the changes.  This is followed by `systemctl restart PlugE.service`.
 ## List All Services
 ```systemctl list-units | grep .service``` - lists all the current services running.