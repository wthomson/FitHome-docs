# Contact
<a href="mailto:contact@fithome.life">Email Us</a>  
# Document Goal
The goal of this document is to get your home electricity monitoring system up and running.

# Overview

The Electricity Monitor communicates to a Raspberry Pi via SPI to read and send active and reactive aggregate power readings to the Pi.  Readings are stored within the Pi's mongo DB.  

![overview](images/Electricity_Monitor_Rasp_Pi.png)  
  
The readings can then be used by analytical packages such as Pandas, Keras - or whatever you want to use! - to learn more about how your home's energy is used.
# Thanks to Those That Went Before
There is _so much_ prior work that made it easier to evolve the atm90e32 micropython library.  Efforts include:  
* Tisham Dhar's [atm90e26 Arduino library](https://github.com/whatnick/ATM90E26_Arduino).    
* The [atm90e26 Circuit Python library I wrote](https://github.com/BitKnitting/HappyDay_ATM90e26_CircuitPython).
* Circuit Setup's [atm90e32 Arduino library](https://github.com/CircuitSetup/Split-Single-Phase-Energy-Meter/tree/master/Software/libraries/ATM90E32).
# GitHub
[Our GitHub](https://github.com/BitKnitting/FitHome_monitor)

# Hardware
The hardware we use to gather readings include:
- Two Current Transformers (CTs) attached to your Service Entrance Wires.  (see the discussion on Current Transformers below).
- [CircuitSetup's Split Single Phase Real Time Whole House Energy Meter (v 1.4)](https://circuitsetup.us/index.php/product/split-single-phase-real-time-whole-house-energy-meter-v1-4/) is the breakout board we use.   This breakout board is based on the [ATM90e32 chip](https://www.microchip.com/wwwproducts/en/atm90e32as).

In addition to the monitor, the breakout board needs a [9V AC Transformer](https://amzn.to/2t7AUro).  What transformer you use becomes important because there are calibration steps (see the Calibration section below) that require different "numbers" depending on the transformer. 
- A raspberry Pi where the collected readings are stored within the Raspberry Pi's mongodb.  We are currently using a Raspberry Pi 3B+.  We just ordered a Pi Zero W to see if that will work since it is smaller and 1/3 the price.  The Pi 3B+ has performed great for this prototype.
- A power cable for the Pi.  We order whatever is recommended since not getting the voltage/current right can cause erratic/intermittant operation.

- "Standard" DIY proto stuff that most makers/hobbyists likely already have.  This includes a green LED along with associated resistor, wires, and a breadboard.

## More about CTs

Aggregate power readings are measured by attaching Current Transformers to the Service Entrance Wires inside a home's Load center. (circuit breaker box)  Our breaker box is located in our garage.  We have two electricity monitors connected.  One is the Sense monitor (the red box, which uses the white CTs), the other is this project (using the blue CTs). 

Go to your breaker panel and take a picture similar to the picture shown here.  

Picture 1:  
![breaker box](images/breaker_box.jpg)   
_Overview picture of breaker box_ 

Then post the image to our GitHub.  This way, we can learn more about how houses have their electrical service panels installed.  By doing so, we can make this project more robust and accomodating differing installations.  

### House Wiring
You'll need to know what "service" your house is wired for.  As we note below, many homes are wired for 100 Amp service.  As the typical home's energy use increased, the installed "service" was increased to 200 Amps. The term 200 Amp service is somewhat of a misnomer. Many think the term refers to the maximum continuously available current supplied from their electrical utility. But that's not correct. It refers to the rating of the _weakest link_ in the Service Entrance Wiring/Load Center/Meter/Meter Base/Main Breaker chain. Read about the characteristics of a CT below to figure out which CT will work with your Service Entrance Wires.  Send the info on the CT you will be using to GitHub so we can better understand how to accomodate the wide variety of electrical hardware we are building this monitor for.

### Current Transformers

Current Transformers (CTs) along with voltage measurments and some math, enable us to determine how much energy the devices in our home are consuming. Here you can see the CTs on our Service Entrane Wires. (SEWs)
![Current Transformers](images/CTs.png)  
Each of our SEWs has two CTs installed - one white, one blue.  The white ones came with the Sense monitor.  The blue ones are the ones we use for this project.  

The CT model we use is [the SCT-013-000 CT](/https://learn.openenergymonitor.org/electricity-monitoring/ct-sensors/yhdc-sct-013-000-ct-sensor-report).  Our home has 100 Amp service.  You may have 200 Amp service.  That will most likely require a CT with a larger wire-window. (the opening on the CT the wire passes through)

The info below should help you get a better idea of CTs and what works for 100A versus 200A services.

### CT Installation
This is the step where most people would recommend hiring an electrician to install the CTs.
That is because there are LETHAL voltages inside a Load Center.

I chose to install the CTs myself.  While my family thought this was foolish, I researched and decided:
- I'm snapping a plastic "thing" (CT) around a heavily insulated wire.
- For added protection, I turned off the power before installing the CTs.
- I double checked the power was off with a voltage detector.
  
![Voltage detector](images/voltage_detector.png)

Or, you can pay an electrician to install them.

Your choice.

### Characteristics of the CT
 The CT characteristics to be considered when sourcing include:
 * The service rating.  This is important because it determines the maximum rating of the CTs you'll use.  Many homes are wired for 100 Amp service.  As a homes electricity use increased, the service increased to 200 Amps.[From this article _Understanding Your Home's Electrical Load_](https://www.bhg.com/home-improvement/electrical/how-to-check-your-homes-electrical-capacity/) _Different homes need different size services. A 60-amp service is probably inadequate for a modern home. In general, A 100 amp service is good for a home of less than 3,000 square feet that does not have central air-conditioning or electric heat. A home larger than 2,000 square feet that has central air-conditioning or electric heat probably needs a 200-amp service._  According to [Bill Thomson of the Open Energy Monitor Project](https://community.openenergymonitor.org/t/ct-hole-diameter-for-north-america/5149), _US homes built before the late 60s were wired with Copper and typically had 100 Amp service, which used AWG 0 copper. Sometime in the late 60s to early 70s, Copper Service Entrance Wires were replaced by Aluminum. Since Aluminum has more resistance per foot than Copper, the equivalent Aluminum wire is two gauges larger than its Copper counterpart. About that same time, 200 Amp service became the norm._  
 * The wire gauge.  The Outside Diameter of the wire determines the minimum size of CT wire window.  
   * [100 Amp Service uses 1/0 AWG](https://community.openenergymonitor.org/t/ct-hole-diameter-for-north-america/5149/3) = [12.08 mm](https://lugsdirect.com/Wire_Insulation_Outside_Diameter_Thickness_600V.html).  
   * 200 Amp Service uses 3/0 copper or 4/0 AWG Aluminum. [4/0 AWG has an Outside diameter of ~16mm](https://lugsdirect.com/Wire_Insulation_Outside_Diameter_Thickness_600V.html).  
* Burdened or unburdened. This design assumes the CT is unburdened. i.e. it __does not__ have an internal burden resistor which means the CT output is current, not voltage.  
* The ratio I<sub>in</sub> to I<sub>out</sub>  
* The inclusion of overvoltage protection.  This is a safety measure that prevents an unburdened CT from producing dangerous voltage when the CT is attached to a current carrying wire.    
### Our Choice of Current Transformers 
 Two Current Transformers (CTs) are needed to get current readings on each of the two 120V legs.
 #### 100 Amp   
  We'll be using [the SCT-013-000 CT](/https://learn.openenergymonitor.org/electricity-monitoring/ct-sensors/yhdc-sct-013-000-ct-sensor-report) for 100A homes.  It is popular with DIY home energy monitors.  There are two numbers of interest in the [YHDC SCT-013-000 datasheet](http://statics3.seeedstudio.com/assets/file/bazaar/product/101990029-SCT-013-000-Datasheet.pdf):    
* The wire window size - 13 mm  
* The ouput current - 50 mA  
As Robert Wall of [the Open Energy Monitor project](https://openenergymonitor.org/) noted to me _...the manufacturer will tweak the number of secondary turns to give the best accuracy overall.  The ratio of a c.t. is __ALWAYS__ specified as a ratio of two currents, the rated primary current to the corresponding secondary current. So your SCT-013-000 is __100 A : 50 mA__._ 
#### 200 Amp
TBD: We'll know what to use as the project progresses. 

At this point, you should have a good idea about which CTs will work for you, and hopefully, have ordered two!

We've got our hardware.  Time to connect the energy monitor to the Raspberry Pi.
# Set up the RasPi
We've got our Pi.  Time to install the OS and configure.  We document the steps on our [Raspberry Pi page](https://github.com/BitKnitting/FitHome/wiki/RaspPi).
# Enable SPI
SPI needs to be enabled on the Pi.  
- Start an `ssh` session.
- `sudo raspi-config`
- Go to `Interfacing Options`, choose SPI, choose Enable.

# Set up FitHome_monitor Git repo
  
Now that the Pi has been configured, let's get the Python code used to talk with the energy monitor, up and running.
- Create a projects directory on your Pi.
- Go into the projects directory and clone [the FitHome monitor repo](https://github.com/BitKnitting/FitHome_monitor).
We can see our git remote has been set up:  
```
$ git remote -v
origin	https://github.com/BitKnitting/FitHome_monitor.git (fetch)
origin	https://github.com/BitKnitting/FitHome_monitor.git (push)
``` 

- Go into the FitHome_monitor directory and create a [Python Virtual Environment](https://docs.python.org/3/tutorial/venv.html). E.g.:  
```
python3 -m venv venv --prompt FH_monitor
```
creates a virtual environment in the venv directory.  When the venv is activated, the prompt will be FH_monitor.  Now that the venv is installed, activate it, e.g.:  
```
source venv/bin/activate
```
- Install Python packages (stuff like everything needed for [Blinka](https://learn.adafruit.com/circuitpython-on-raspberrypi-linux/circuitpython-raspi))using `pip install -r requirements.txt`.
# Access Project through VS Code
Follow the steps outlined in the section on [Remote VS Code](RaspPi.md)
# Getting to Blinka
Run [blinkatest.py](https://github.com/BitKnitting/FitHome_monitor/blob/master/blinkatest.py). 
```
(venv) pi@raspberrypi:~/projects/FitHome_monitor $ python3 blinkatest.py
Hello blinka!
Digital IO ok!
SPI ok!
done!
```
# Other SPI Tests
- [simple_read.py](https://github.com/BitKnitting/FitHome_monitor/blob/master/simple_read.py)
- [simple_write.py](https://github.com/BitKnitting/FitHome_monitor/blob/master/simple_write.py)
- [simple_atm90e32_test.py](https://github.com/BitKnitting/FitHome_monitor/blob/master/simple_atm90e32_test.py)
# Connect the Pi to the Energy Monitor
We'll connect:
- SPI between the two boards.
- a Red and Green LED onto the Pi.  
## SPI Pins on Pi
We've tried this on the RasPi 3B+, Zero W, 3B v1.2

![RaspPi pinout](images/RaspPi_pinout.png)
- MOSI: pin 19
- MISO: pin 21
- CLK:  pin 23
- GND: pin 25
We'll use GPIO 5 (pin 29) for CS, noting in the [Blinka documentation](https://learn.adafruit.com/circuitpython-on-raspberrypi-linux/spi-sensors-devices),
_The upshot here is basically never connect anything to CE0 (or CE1 for that matter). Use whatever chip select pin you define in CircuitPython and just leave the CE pins alone, it will toggle as if it is the chip select line, completely on its own, so you shouldn't try to use it as a digital input/output/whatever._
- Connect the MOSI, MISO, SCLK, GND, CS lines from the Pi to the Energy monitor.

![CS_SPI_PINS](images/CircuitSetupPins.png)
# Test SPI
Run [atm90e32_spi_test.py](https://github.com/BitKnitting/FitHome_monitor/blob/master/atm90e32_spi_test.py).  It performs a simple SPI read.

Here is an image of the traffic from our logic analyzer:
![SPI_read_logic](images/SPI_read_test.png)

Now we know SPI reads are working correctly.

# Getting to Power Readings
The [atm90e32 class](https://github.com/BitKnitting/FitHome_monitor/blob/master/RaspPi/atm90e32/atm90_e32_pi.py) holds the code that provides easy access to the energy monitor.

## The atm90e32 Python Class
The class:
- initializes the monitor by setting the calibration properties.  Calibration properties differ depending on the CT and power supply used with the moniter (see below).
- fetches the power readings.

### Calibration 
We need to calibrate prior to using the library.  The default values are discussed in [Circuit Setup's documentation](https://github.com/CircuitSetup/Split-Single-Phase-Energy-Meter#calibration).  
  
We use the default values for:  
```
lineFreq = 4485  # 4485 for 60 Hz (North America)
PGAGain = 21     # 21 for 100A (2x), 42 for >100A (4x)
```
All that's left is voltage and current gain value cailbration.  These are important because they can cause inaccurate voltage, current and power readings.

It is more likely the voltage channel will need calibration than the current channel. But it's a good idea to calibrate both.  
### Voltage Calibration
The gain value is tied to the transformer we're using.  We decided to standardize on the [Jameco 9V power supply, part no. 157041](https://www.jameco.com/shop/ProductDisplay?catalogId=10001&langId=-1&storeId=10001&productId=157041).  The default voltage gain for a 9V AC transformer was 42080.  With this voltage gain setting, our readings were over 15 Volts higher than the actual voltage.  

To determine the actual voltage, we used the extremely useful [Kill-A-Watt](https://amzn.to/2Mcjkt7).

To calibrate the voltage gain, we used the formula/info in [the app note](https://github.com/BitKnitting/energy_monitor_firmware/blob/master/docs/Atmel-46103-SE-M90E32AS-ApplicationNote.pdf) _see section 4.2.6 Voltage/Current Measurement Calibration where it discusses using existing voltage gain_

where:
- reference voltage = reading from Kill-A-Watt
- voltage measurement value = the reading for voltage obtained by initializing the atm90e32 instance with the voltage gain value and reading the `line_voltageA` property.

new `VoltageGain` = reference voltage/voltage measurement * current voltage gain

e.g. using a different 9V transformer:
- Kill-A-Watt shows V = 121.5
- reading shows voltage at 117.5
- old `VoltageGain` is 36650

new `Voltage Gain = 121.5/117.5*36650 = 37898`

Calculate the value, and change the `VoltageGain` to the calculated value.
### Current Calibration
We found the default current gain value gave current readings close to what we got with the Kill-A-Watt.  Because it was easy to do so, we set the `CurrentGainCT1` and `CurrentGainCT2` values to our calculation, using the current readings in place of the voltage readings as discussed in the app note.
# Main Code
The main code is in [ReadAndStore.py](https://github.com/BitKnitting/FitHome_monitor/blob/master/ReadAndStore.py)
# Systemd Service
We use [systemd](https://en.wikipedia.org/wiki/Systemd) to run ReadAndStore.py in the background on our Pi.  The file is [ReadAndStore.service](https://github.com/BitKnitting/FitHome_monitor/blob/master/ReadAndStore.service).
## New to SystemD
Not being fluent systemd users, we found the following info useful:
* [Intro to systemd video](https://youtu.be/AtEqbYTLHfs?t=147).  
  * [Place in the video where using commands starts](https://youtu.be/AtEqbYTLHfs?t=230).
* [systemd in Raspberry Pi documentation](https://www.raspberrypi.org/documentation/linux/usage/systemd.md)
* [Article on how to autorun service using systemd](https://www.raspberrypi-spy.co.uk/2015/10/how-to-autorun-a-python-script-on-boot-using-systemd/).
## Make sure to...
* set permissions so systemd can execute the script: ```sudo chmod +x {python script}```
* copy service file to where systemd expects it to be:  ```sudo cp {service script} /lib/systemd/system/.```
* enable the service with: ```sudo systemctl enable {service script}```.
* check to make sure the service has been enabled with: ```systemctl is-enabled {service script}```
* start the service with: ```sudo systemctl start {service script}```.
* check to make sure the service has been started with: ```systemctl is-active {service script}```
See the ```systemd status``` command info below to debug if your service did not start.
## Debugging
If `$ systemctl status {service script}` returns something like...
```
PlugE.service - Collect and send power readings.
   Loaded: loaded (/lib/systemd/system/PlugE.service; enabled; vendor preset: enabled)
   Active: failed (Result: exit-code) since Sun 2019-10-13 21:03:49 BST; 22s ago
 Main PID: 2813 (code=exited, status=1/FAILURE)
 ```  
 Check the debug records with `journalctl _PID=2813` = where the PID comes from the line containing: Main PID.  
 ## Changes to Code and Systemd
 Changes made to the systemd service (in this case the Python code), requires the command `systemctl daemon-reload` to make systemd aware of the changes.  This is followed by `systemctl restart {service script}`.
 ## List All Services
 ```systemctl list-units | grep .service``` - lists all the current services running.
 # MongoDB
 The RasPi OS comes with a super easy and flexible database, mongodb.  Installation is discussed within our [Rasp Pi Setup documentation](RaspPi.md).
 ## Some Simple Mongo Commands
First, get into the mongo client: `$mongo`.  Go to your database: `>use YOURDATABASE`.  `>show collections` 
```
> show dbs
> use FitHome
switched to db FitHome
> show collections
> db.aggregate.find()
> db.stats()
system.indexes
users
>db.users.stats()

> db.users.find()
{ "_id" : ObjectId("5dd94f0074fece1e23950257"), "name" : "Cristina" }
{ "_id" : ObjectId("5dd94f0174fece1e23950258"), "name" : "Derek" }
{ "_id" : ObjectId("5dd975e574fece1e23950259"), "name" : "Cristina" }
{ "_id" : ObjectId("5dd975e574fece1e2395025a"), "name" : "Derek" }
```
## [Export Readings to Pandas](#readings_to_pandas)
We run a SystemD service - [extract_readings.service](https://github.com/BitKnitting/FitHome_monitor/blob/master/data_extraction/extract_readings.service) that relies on a [SystemD timer file](https://wiki.archlinux.org/index.php/Systemd/Timers) -  [extract_readings.timer](https://github.com/BitKnitting/FitHome_monitor/blob/master/data_extraction/extract_readings.timer) - to run
 [extract_readings.py](https://github.com/BitKnitting/FitHome_monitor/blob/master/data_extraction/extract_readings.py).  This python script relies on the [MonitorData](https://github.com/BitKnitting/FitHome_monitor/blob/master/data_extraction/monitor_data.py) to get the records out of the mongodb (database = FitHome, collection = aggregate) and create a pickled zip file.  We chose this format because it is easy to read the data with the Pandas package.
 ### Environment Variables
Notice the MonitorData class uses environment variables. We used the technique discussed [in this post](https://unix.stackexchange.com/questions/287743/making-environment-variables-available-for-downstream-processes-started-within-a).


# [Explore Readings with colab](#colab_readings)
On to exploring the data!

For this part of the workflow, we use [SSHFS](https://github.com/BitKnitting/FitHome/wiki/RaspPi#mount-drive), colab, and some simple utility functions to start playing with the data.
Steps:
- Mount the Rasp Pi's drive so we can access it like a local drive. e.g.:
```
sshfs pi@192.168.86.20: /Users/auser/mount
```
You will have a different mount point as well as RasPi username and IP address.
Navigate to the file where the updated aggregate readings are located.  Our file was located in:  
```
pi@raspberrypi:~/projects/FitHome_monitor/data_extraction
```  
We use the default filename: ```aggregate.pkl.zip```

Now we can see the aggregate file created by the SystemD service within our file system.

![aggregate file in filesystem](images/EnergyMonitorFirmware/data_extraction_mount.png)
 
We have a colab notebook - [00-load_data.ipynb](https://colab.research.google.com/github/BitKnitting/FitHome_monitor/blob/master/notebooks/00_load_data.ipynb) that walks you through loading the aggregated data into a pandas dataframe.
