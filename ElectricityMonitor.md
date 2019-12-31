# Overview
This part of the Wiki documents (1) in the diagram:  
![overview](images/EnergyMonitorFirmware/component_design.png)
The Electricity Monitor (details below) combined with a Raspberry Pi collects aggregate power readings and stores them in the Raspberry Pi's mongo db.
  
The readings can then be used by analytical packages such as Pandas, Keras to learn more about how a home's energy is used.
# Thanks to Those That Went Before
There is _so much_ prior work that made it easier to evolve the atm90e32 micropython library.  Efforts include:  
* Tisham Dhar's [atm90e26 Arduino library](https://github.com/whatnick/ATM90E26_Arduino).    
* The [atm90e26 Circuit Python library I wrote](https://github.com/BitKnitting/HappyDay_ATM90e26_CircuitPython).
* Circuit Setup's [atm90e32 Arduino library](https://github.com/CircuitSetup/Split-Single-Phase-Energy-Meter/tree/master/Software/libraries/ATM90E32).
# For Collaborators
We are thrilled you want to collaborate with us on this project.  Welcome!  To get started, please follow the steps outlined [in this medium post on GitHub collaboration](https://medium.com/faun/collaborating-on-github-22fd5886fce).     

[Here is our energy monitor firmware GitHub repository](https://github.com/BitKnitting/energy_monitor_firmware).

Let's use our [issues](https://github.com/BitKnitting/energy_monitor_firmware/issues) section to exchange questions, pass along information, assign tasks, etc.

_PLEASE evolve the documentation if it can be improved. This will benefit us all!_
# Collecting Aggregate Power Readings
The Electricity Monitor:
- gathers power readings from our breaker box.
- put the readings into the mongo db that comes with the Raspberry Pi OS.
# Required Hardware
The hardwere we use to gather readings include:
- Two Current Transformers (CTs) that work for your power lines (see the discussion on Current Transformers below).
- [CircuitSetup's Split Single Phase Real Time Whole House Energy Meter (v 1.4)](https://circuitsetup.us/index.php/product/split-single-phase-real-time-whole-house-energy-meter-v1-4/) is the breakout board we use.   This breakout board is based on the [ATM90e32 chip](https://www.microchip.com/wwwproducts/en/atm90e32as).

Besides the monitor, the breakout board needs a [9V AC Transformer](https://amzn.to/2t7AUro).  What transformer you use becomes important because there are calibration steps (see the Calibration section below) that require different "numbers" depending on the transformer. 
- A raspberry Pi where the collected readings are stored within the Raspberry Pi's mongodb.  We are currently using a Raspberry Pi 3 B +.  We just ordered a Pi Zero W to see if that would work since it is smaller and 1/3 the price.  The Pi 3 B+ has done great for this prototype.
- A power cord for the Raspberry Pi.  We order whatever is recommended since not getting the voltage/current right will cause the board to die (a probably not painful death).

- "Standard" DIY proto stuff that we all most likely already have.  This includes a red and green LEDs along with associated resistors, wires, and a breadboard.

If you're building, order the stuff listed above.  If you are unfamiliar with CT's (like we were), you'll want to read this next section on CTs.

__More about CTs__

We're starting at ordering the CTs, since we found this to be the most confusing part of the hardware.
## First
Aggregate power readings are measured by attaching Current Transformers to the power lines within a home's breaker box.  Our breaker box is located in the garage.  We have two electricity monitors hooked up.  One is the Sense monitor (the red box uses the white CTs), the other is this project (using the blue CTs). 

Go to your breaker panel and take a picture similar to the picture shown here.  

Picture 1:  
![breaker box](images/EnergyMonitorFirmware/breaker_box.jpg)   
_Overview picture of breaker box_ 

Then post the image to our GitHub.  This way, we can learn more about how houses have their electricity installed.  By doing so, we can make this project more robust and accomodating to different installations.  

## Second
You'll need to know what your house is wired for.  As we note below, many homes are wired for 100 Amp service.  As a home's electricity use increased, the service increased to 200 Amps.  Read about the characteristics of a CT below and figure out what CT model will work with your power lines.  Send the info on the CT you will be using to GitHub so we can better understand what we are building.

## Current Transformers

Current Transformers (CTs) are our "ears" into how devices are using power within our home.  You can see the CTs on our power line.
![Current Transformers](images/EnergyMonitorFirmware/CTs.png)  
Each of our lines has two CTs - one white, one blue.  The white ones come with the Sense monitor.  The blue ones are the ones we use for this project.  

The CT model we use is [the SCT-013-000 CT](/https://learn.openenergymonitor.org/electricity-monitoring/ct-sensors/yhdc-sct-013-000-ct-sensor-report).  Our home's power lines are 100A service.  You may have 200A service.  That will require a different CT.

The info below should help you get a better idea of CTs and what works for 100A versus 200A services.

### CT Installation
Installation is the step where it is important to know that most people would recommend an electrician to install the CTs.  This is because = as you can imagine - the amount of voltage and current pouring through the lines WILL kill you because skin is a great conductor of electricity.

I chose to install the CTs myself.  While my family thought this was foolish, I researched and decided:
- I'm snapping a plastic "thing" (CT) around a heavily cabled line.
- For added protection, I turned off the power before putting on the CTs.  Just to make sure, I double checked the power was off with a voltage detector  
  
![Voltage detector](images/EnergyMonitorFirmware/voltage_detector.png)

Or you can pay an electrician to install.

Your choice.

### Characteristics of the CT
 The characteristics of a CT to be considered when sourcing include:
 * The amount of Amp Service.  This is important because it dictates the hole diameter of the CT.  Many homes are wired for 100 Amp service.  As a homes electricity use increased, the service increased to 200 Amps.[From this article _Understanding Your Home's Electrical Load_](https://www.bhg.com/home-improvement/electrical/how-to-check-your-homes-electrical-capacity/) _Different homes need different amp services. A 60-amp service is probably inadequate for a modern home. A 100-amp service is good for a home of less than 3,000 square feet that does not have central air-conditioning or electric heat. A home larger than 2,000 square feet that has central air-conditioning or electric heat probably needs a 200-amp service._  According to [Bill Thompson of the Open Energy Monitor Project](https://community.openenergymonitor.org/t/ct-hole-diameter-for-north-america/5149), _US homes built before the late 60s were wired with Copper and typically had 100 Amp service, which used AWG 0 copper...Sometime in the late 60s to early 70s, Copper Service Entrance Wires were replaced by Aluminum. Since Aluminum has more resistance per foot than Copper, the equivalent Aluminum wire is two gauges larger than its Copper counterpart. About that same time, 200 Amp service became the norm._  
 * The wire gauge.  The Outside Diameter of the wire gauge determines how large the diameter of the CT clamp must be.  As Bill noted, wires could be copper or aluminum, or copper-clad aluminum.  More aluminum, more current - the wider the diameter.  
   * [100 Amp Service uses 1/0 AWG](https://community.openenergymonitor.org/t/ct-hole-diameter-for-north-america/5149/3) = [12.08 mm](https://lugsdirect.com/Wire_Insulation_Outside_Diameter_Thickness_600V.html).  
   * 200 Amp Service uses (up to?) 2/0 - 4/0 AWG.  [4/0 AWG has an Outside diameter of 16.04mm](https://lugsdirect.com/Wire_Insulation_Outside_Diameter_Thickness_600V.html).  
* Whether the burden resistor is included.  This design assumes the CT __does not__ include the burden resistor.  I.e.: it's output is a current and not a voltage.  
* The ratio of I(in) : I(out)  
* The inclusion of a zener diode.  This is a terrific safety measure to make sure when the CT is clamped on, the circuit is not open.    
### Our Choice of Current Transformers 
 Two Current Transformers (CTs) are needed to get current readings on the two 120V lines.
 #### 100 Amp   
  We'll be using [the SCT-013-000 CT](/https://learn.openenergymonitor.org/electricity-monitoring/ct-sensors/yhdc-sct-013-000-ct-sensor-report) for 100A homes.  It is popular with DIY home energy monitors.  There are two numbers of interest in the [YHDC SCT-013-000 datasheet](http://statics3.seeedstudio.com/assets/file/bazaar/product/101990029-SCT-013-000-Datasheet.pdf):    
* The diameter of the clamp opening - 13 mm  
* The I(OUT) - 50 mA  
As Robert Wall of [the Open Energy Monitor project](https://openenergymonitor.org/) noted to me _...the manufacturer will tweak the number of secondary turns to give the best accuracy overall.  The ratio of a c.t. is __ALWAYS__ specified as a ratio of two currents, the rated primary current to the corresponding secondary current. So your SCT-013-000 is __100 A : 50 mA__._ 
#### 200 Amp
TBD: We'll know what to use as the project progresses. 

At this point, you should  know which CTs to use, and hopefully ordered two!

We've got our hardware.  Time to wire the energy monitor to the Raspberry Pi.
# Get the Rasp Pi Up and Running
We've got our Rasp Pi.  Time to install the OS and configure.  We document the steps on our [Raspberry Pi page](https://github.com/BitKnitting/FitHome/wiki/RaspPi).
# Wire the Rasp Pi to the Energy Monitor
We'll wire:
- SPI between the two boards.
- a Red and Green LED onto the Rasp Pi.

## SPI

