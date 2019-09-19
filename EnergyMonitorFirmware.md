### GitHub Location
[energy monitor firmware GitHub repository](https://github.com/BitKnitting/energy_monitor_firmware)
# Thanks to Those That Went Before
There is _so much_ prior work that made it easier to evolve the atm90e32 micropython library.  Efforts include:  
* Tisham Dhar's [atm90e26 Arduino library](https://github.com/whatnick/ATM90E26_Arduino).    
* The [atm90e26 Circuit Python library I wrote](https://github.com/BitKnitting/HappyDay_ATM90e26_CircuitPython).
* Circuit Setup's [atm90e32 Arduino library](https://github.com/CircuitSetup/Split-Single-Phase-Energy-Meter/tree/master/Software/libraries/ATM90E32).
# What the Code Does
We can write [Circuit Python](https://github.com/adafruit/circuitpython) instead of Arduino IDE to talk to the atm90e32. 
Here's an [example](examples/CP_ATM90E32_Basic_SPI.py):  

# Overview
The energy monitor firmware is built on micropython to:
* Join the homeowner's wifi.  
* Send energy readings to the Firebase RT db.  
### Hardware
- Energy Monitor - The firmware was written for [CircuitSetup's Split SIngle Phase Real Time Whole House Energy Meter (v 1.4)](https://circuitsetup.us/index.php/product/split-single-phase-real-time-whole-house-energy-meter-v1-4/).  This breakout board is based on the [ATM90e32 chip](https://www.microchip.com/wwwproducts/en/atm90e32as).
- Microprocessor - At least for testing, we are using [the ESP32 DevKit C](https://amzn.to/2JInYgj).
### Software 
- OS: [micropython v1.11](https://github.com/BitKnitting/energy_monitor_firmware/tree/master/micropython_build)
- IDE: [uPyCraft](http://docs.dfrobot.com/upycraft/).  I started with uPyCraft.  Then modifying a file, copying to the ESP32 started having too many issues (we can't wait for true USB!).  So we started using [rshell](https://pypi.org/project/rshell/).  
#### rshell
rshell has great documentation.  We start a Terminal within the workspace folder of the project.  From within rshell we can go between repl and copying files from our Mac to the ESP32.
#### uPyCraftism
__IMPORTANT__: uPyCraft has a folder in it called _workspace_.  This folder is mapped (mounted) to a directory on the Mac's/PC's hard drive.  This is why the firmware starts below the _workspace_ folder.  This folder needs to be mapped to uPyCraft's folder.  These are the steps we took:
- Choose Tools/InitConfig  You'll be asked if you want to init. Choose yes.  
- Click on the workspace folder.  This brings up a Finder dialog box.  Choose the energy_monitor_firmware directory.
# In Practice
There are a few "not ready for primetime" gotchas we found out about while testing that we just had to work around:
- After setting up an Access Point (see below under Monitor Install), ocassionally the micropython libraries seemed to leak memory.  Then the ESP32 froze.  Restarting the ESP32 at this point "fixed" this.
- The ESP32 needs to be plugged in BEFORE the 9V power transformer starts up the energy monitor.  We put a few second delay in main.py to accomodate plugging in the 9V monitor after restarting the ESP32.

# Software Design
[main.py](https://github.com/BitKnitting/energy_monitor_firmware/blob/master/workspace/main.py) gives us a general flow of the code.
## Monitor Status
The goal with monitor status is to quickly and simply figure out why we're not getting readings into the db when we think we should be getting them.

Two LEDs - one red (errors) one green (energy reading sent to database) are used to let us the status of the monitor when the code is loading or running.  
### Program Start
The code in [main.py] starts off by blinking the green LED 4 times.  This lets us know that the ESP32 has been able to load and start the code in main.py.  E
### Errors
Errors are identified in [app_error.py](https://github.com/BitKnitting/energy_monitor_firmware/blob/master/workspace/errors/app_error.py) as simply Python classes. e.g.:  
```
class NoMonitor():
    # When the atm class is initialized, it writes calibration info to
    # registers.  This error means the microcontroller couldn't write to
    # the atm.  Most likely the monitor isn't plugged in or the SPI
    # connection isn't working properly.
    number = 301
    explanation = 'Could not contact a monitor.'
    blinks = 1
 ```
 Each error is assigned a number of blinks.  So if the red LED starts blinking, we need to check how many blinks happen and then figure  out from there is we can debug why the monitor is not working.
 ### All OK
 Every time a reading is sent to the db, the green light blinks.  If we watch the monitor for a time within which a reading should occur, the green LED should blink.  If it doesn't, there is a really, really good chance readings are not being made or at least not being sent to the db.
 ## Connecting to WiFi
 A big chunk of the firmware code is dedicated to handling wifi.  The monitor either knows the ssid and password of the homeowner's wifi (and of course is close enough to get a usable signal) or does not.  

 From [main.py](https://github.com/BitKnitting/energy_monitor_firmware/blob/master/workspace/main.py):

 - Load the class that will handle all the wifi calls: `from wifi_connect import WifiAccess`
 - Make an instance of the wifi class: `join_wifi = WifiAccess()`
 - Connect to the wifi network: `join_wifi.get_connected()`
 ### Monitor Install
 When the monitor is first installed, the homeowner's wifi ssid and password is not known.  The `__init__` method of the WifiAccess class tries to read the SSID and password using the `read_config` method in [config.py](https://github.com/BitKnitting/energy_monitor_firmware/blob/master/workspace/config/config.py).  If the SSID and password can't be retrieved, the class's wifi_state variable is set to no_ssid_pwd.  If the SSID and password were retrieve, wifi_state is set to not_connected.

 #### Setting SSID and password

 Monitor install focuses on the no_ssid_password state.  When WifiAccess()'s `get_connected()` method figures out the monitor doesn't know the wifi's SSID and password, it moves the code into being a web server acting as a wireless Access Point with the SSID `fithome_abc`.   An Access Point shows up within the Mac's/PC's list of wifi networks.

![wifi ssid](images/EnergyMonitorFirmware/wifi_ssid.png)   
- Connect to the `fithome_abc` Access Point on your Mac/PC.  If you don't see the Access Point and the monitor isn't blinking red, there's a good chance the monitor is too far from the wifi signal.  Right now, we have a poor solution - get the wifi signal to be stronger in the area where the monitor is...hmmm....
- Once connected to `fithome_abc`, open a browser window and enter the address `192.168.4.1`.  At this point, you should be "talking" with the monitor's AP firmware and see a page that shows the SSIDs available in the homeowner's house.

![wifi client](images/EnergyMonitorFirmware/wifi_client.png)  

Click on the radio button of the SSID the monitor should use and type in the password.  If the monitor can log in, you'll see:    
  
![wifi successful login](images/EnergyMonitorFirmware/wifi_success_ssid_password.png)  
Behind the scenes, the code in `wifi_connect.py` is using `<form action="configure"` to figure out if the homeowner has sent the SSID and password.  
```
  try:
      tag = ure.search("(?:GET|POST) /(.*?)(?:\\?.*?)? HTTP",
                        request).group(1).decode("utf-8").rstrip("/")
  except Exception:
      tag = ure.search(
          "(?:GET|POST) /(.*?)(?:\\?.*?)? HTTP", request).group(1).rstrip("/")
  if tag == "":
      # Get the ssid and password.
      self._handle_ssid_pwd_ui(client)
      # Try to join the self.wifi.
  elif tag == "configure":
      self._handle_join_wifi(client, request)
```                    
`_handle_join_wifi()` takes care of adding the SSID and password into the config file in case the monitor's firmware needs it again.  It also connects to the network. _Note: We haven't spent time exploring this, but from what we can tell, the ESP32 stores the SSID and password somewhere (EEPROM? FLASH?).  It doesn't seem to access the variables from the config file, rather can just log in._
### Ongoing wifi
Once the monitor knows the SSID and password, the monitor should be able to connect to the homeowner's wifi.
## Sending Readings
Reading the atm9e32 registers relies on the [ATM90e32 class](https://github.com/BitKnitting/energy_monitor_firmware/blob/master/workspace/atm90_e32/atm90e32_u.py).  
### Calibration 
The trickiest part in getting quality readings from the atm90e32 is setting up the right calibration settings.  The settings we are using have been verified by testing to work with the 9V transformer and CTs (Current Transformers) we are using.  Check out [Circuit Setup's documentation](https://github.com/CircuitSetup/Split-Single-Phase-Energy-Meter#calibration) if you are unsure if the calibration numbers will work with your hardware.
### Initializing the ATM90e32 Class
We start by initializing an instance of the ATM90e32 twice with a time delay in between.  We have found that there are times when initializing only once fails to be able to send correct power readings.  We then go into a - hopefully - endless while True loop:
- check the system status register to verify we can "talk" correctly with the atm90e32.
- send a reading to the database.
### Send Readings
The [SendReadings() class](https://github.com/BitKnitting/energy_monitor_firmware/blob/master/workspace/send_reading/send_reading.py) handles sending power readings to the database.  Given how rich the atm90e32 readings are, there are many different readings we could send back.  We chose to send only power readings since these are the only ones we need to gain and provide insight on a homeowner's energy use.  

The SendReadings() class uses the REST PUT API to send power readings to the FitHome db.  We are currently using Firebase RT.  
***********************************
__TBD PICTURE__
***********************************
Each reading is formatted:  
```
<Firebase referende>/readings/<monitor name>/<Unix Epoch timestamp>/
{{"P": 1002.4}}
```
#### Getting the Timestamp
The path to a power reading in the db includes the Unix Epoch timestamp.  To know what timestamp to use, we must:
* Know the current date/time as a timestamp. 
* Set the ESP32's date/time to the timestamp.  
* Get the timestamp from the ESP32 when sending a power reading and build up the db path.
##### Get the Current date/time from Firebase
We get the current timestamp from the Firebases db.  We do this in [`SendReadings() __init__()`](https://github.com/BitKnitting/energy_monitor_firmware/blob/master/workspace/send_reading/send_reading.py)  
```
path = 'https://fithome-9ebbd.firebaseio.com/current_timestamp/.json'
data = '{ "timestamp":{".sv": "timestamp"} }'
response = requests.put(path, data=data)
```
`response.json()` gives us the Unix Epoch timestamp. `{'timestamp': 1568821802745}`.  We then use micropython's utime library to interpret the date and time.  
##### Set the ESP32 date/time
Now we have the Epoch Unix timestamp.  We need to set the ESP32 to that time.  To do this:
* The Firebase timestamp is in ms.  We need to divide the timestamp by 1000.
* The Firebase timestamp uses a different epoch than the ESP32/micropython timestamp,  as pointed out within [the utime micropython docs](https://docs.micropython.org/en/latest/library/utime.html):
_Time Epoch: Unix port uses standard for POSIX systems epoch of 1970-01-01 00:00:00 UTC. However, embedded ports use epoch of 2000-01-01 00:00:00 UTC._
  We need to subtract out the number of seconds between the Unix and micropython Epoch time: `time_diff = 946684800`.
Using the timestamp from above:
```
import utime
ts = 1568821802745 // 1000
time_diff = 946684800
year,month,day,hour,minute,second, dayofweek,dayofyear = utime.localtime(ts-time_diff)
```
As noted in [the micropython docs](https://docs.micropython.org/en/latest/library/utime.html), "_The current calendar time may be set using machine.RTC().datetime(tuple) function..._"
```
import utime
import machine
rtc = machine.RTC()
# Set the time on the ESP32
rtc.datetime((year,month,day,dayofweek,hour,minute,second,0))
# Check to see if the time is set right.
now = utime.time()
# Ask what the current time is.
tm = utime.localtime(now)
print(tm)
```
Here's an _UGH_ we ran across... notice how `dayofweek` is in the middle of setting the datetime within the rtc instance.  This is different than what the documenation shows.  Looking at [the code](https://github.com/micropython/micropython/blob/master/ports/esp32/machine_rtc.c):
```
mp_obj_new_int(tm.tm_year),
mp_obj_new_int(tm.tm_mon),
mp_obj_new_int(tm.tm_mday),
mp_obj_new_int(tm.tm_wday),
mp_obj_new_int(tm.tm_hour),
mp_obj_new_int(tm.tm_min),
mp_obj_new_int(tm.tm_sec),
mp_obj_new_int(tv.tv_usec)
```
# Send Reading
Recall the last node on the db path to the power readings is created by a timestamp.  We need a string timestamp in Unix Epoch time.  
```
import utime
now = utime.time()
# now is the number of seconds since 2000-01-01... We need to convert tis to the number of seconds since 1970-01-01.
time_diff = 946684800
now_unix = now + time_diff
now_unix_str = str(now_unix)
```
_Note: we could multiply by 1000 to be in the same units as the Firebase timestamp (i.e.: ms).  However, we cannot find a reason to do this.  Since the monitor is the only hardware that doesn't use Unix epoch, it made sense to us to use Unix epoch without ms._

 # Preparing the ESP32
 At least for testing, we are using [the ESP32 DevKit C](https://amzn.to/2JInYgj).  For the IDE we are using [uPyCraft](http://docs.dfrobot.com/upycraft/).   
 # Install micropython
- Plug the microprocessor into the Mac's USB port.
- Open uPyCraft on the Mac.
- Check Tools/Build from the menu and make sure ESP32 is checked.
- Check Tools/Serial from the menu and select the SLAB_USBtoUART serial port.  When this is done, the >>> repl command prompt should be seen in the lower window.  
- Go to Tools/BurnFirmware.    [This is the micropython build we currently use](https://github.com/BitKnitting/energy_monitor_firmware/tree/master/micropython_build).  This version of micropython is available within [the energy monitor firmware's GitHub repository](https://github.com/BitKnitting/energy_monitor_firmware/tree/master/micropython_build).  For ESP32 micropython firmware, the settings should be:  
![micropython burn firmware dialog](images/EnergyMonitorFirmware/uPyCraft_burnimage_dialog.png)  
Once the fields are filled in, click on OK.  This starts the erase and burn process.   
- uPyCraft disconnects.  Once the microprocessor restarts, go ack to Tools/Select and choose the SLAB_USBtoUSART serial port. 
## Copy micropython Libraries
The libraries we use to connect to wifi and read/send energy readings include:
  - [atm90e32_registers.py](https://github.com/BitKnitting/energy_monitor_firmware/blob/master/workspace/read_monitor/atm90e32_registers.py) and [atm90e32_u.py](https://github.com/BitKnitting/energy_monitor_firmware/blob/master/workspace/read_monitor/atm90e32_u.py) from workspace/read_monitor.
  - [config.py](https://github.com/BitKnitting/energy_monitor_firmware/blob/master/workspace/config/config.py) from workspace/config.
  - [wifi_connect.py](https://github.com/BitKnitting/energy_monitor_firmware/blob/master/workspace/join_wifi/wifi_connect.py) from workspace/join_wifi.
  - [send_reading](https://github.com/BitKnitting/energy_monitor_firmware/blob/master/workspace/send_reading/send_reading.py) from workspace/send_reading.
  - [app_error.py](https://github.com/BitKnitting/energy_monitor_firmware/blob/master/workspace/errors/app_error.py) from workspace/errors.
  ### Steps
  Within the uPyCraft IDE:  
  - Make sure you are "talking" with micropython by checking if the repl >>> prompts are in the bottom window.
  - create a lib file under the device folder.
  - drag/drop each of the lib files from within the workspace folder to the lib directory under the device folder.
  - drag/drop main.py to be under the device folder.
  - create `config.json` and copy it to the lib directory.  The `config.json` file looks like:  
  ```
  {
    "monitor":"bambi-07052019",
    "project_id":"my-firebase-projectid-00989"
}  
```
The monitor name is in the Database under the homeowner's member record.  
![monitor name in db](images/Database/monitor_node.png) 
  
The monitor name is created when the homeowner becomes a FitHome member.  The conceptual model is a homeowner becomes a FitHome member for one month.  The homeowner uses the FitHome App to start their month of training.  During this process, one of the available monitors is assigned to the owner.  To uniquely identify this month of use, the monitor name assigned to the homeowner is appended with the date the homeowner signed up for FitHome membership.  In this example, the monitor named "bambi" was assigned to the homeowner.  The homeowner signed up on July 5th, 2019.


