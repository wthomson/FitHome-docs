# Monitor Readings into Rasp Pi MongoDB
The goal of this post is to get to the point where the energy monitor attached to the breaker box (with two CTs) is sending it's readings to the Rasp Pi's MongoDB via a Post command.
# Using MongoDB
[Here](Posts/ExploringEnergyDisaggregation/UsingMongoDB.md) are my notes on setting up MongoDB, Flask...
# Restore MongoDB
Through blindly following someone's ideas on Rasp Pi + MongoDB + remote access, I ended up not being able to connect via wifi to our Rasp Pi.  After a few hours bumbling about on this, we ended up copying the files off the SD-Card and starting the install again.  How to get the files off the SD-Card is covered [here](../../RaspPi.md).
# Micropython code
The code we're currently running on the energy monitor sends readings to a Firebase RT db.  It is not written in an abstract way that makes it easy to plug in another db (although it probably should at this point!).

The micropython code is in the [FitHome Analysis project](https://github.com/BitKnitting/FitHome_Analysis).
## Changes to Monitor Firmware
We made the following changes to [the monitor firmware](https://github.com/BitKnitting/energy_monitor_firmware):  
- Collect reactive power as well as active power and current.  Given the current, active power, and reactive power have a signature for a device, we thought our deep learning model would be improved by having the three features.
- Take one second readings.
- Send the readings to our Rasp Pi Flask app
# Detecting when the microwave is on/off
To simplify, we're focusing on a microwave.  Of course this could be any device.  The goal is to let the aggregated reading know when the microwave has been turned on and then off.  This way, a column of 0 (off) and 1 (on) is created on each sample row.  Worth noting: We don't need to plug's microwave readings as much as we need the trigger for the microwave on/off.  We are exploring alternative sensor methods to detect this based on human interaction.  Initially we will be using the [TP-LINK HS110](https://amzn.to/2WBHPUc) as we are using in [PlugE](../../PlugE.md).
We made the following changes to the PlugE code:
- Get the name of the device (should be descriptive to the device, e.g.: microwave)
- detect from readings when on/off.
- send Post to RaspPi Mongodb when change in state from on/off.