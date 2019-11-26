# Monitor Readings into Rasp Pi MongoDB
The goal of this post is to get to the point where the energy monitor attached to the breaker box (with two CTs) is sending it's readings to the Rasp Pi's MongoDB via a Post command.
# Using MongoDB
[Here](Posts/ExploringEnergyDisaggregation/UsingMongoDB.md) are my notes on setting up MongoDB, Flask...

# Micropython code
The code we're currently running on the energy monitor sends readings to a Firebase RT db.  It is not written in an abstract way that makes it easy to plug in another db (although it probably should at this point!).

The micropython code is in the [FitHome Analysis project](https://github.com/BitKnitting/FitHome_Analysis).
## Changes to Monitor Firmware
We made the following changes to [the monitor firmware](https://github.com/BitKnitting/energy_monitor_firmware):  
- Collect reactive power as well as active power and current.  Given the current, active power, and reactive power have a signature for a device, we thought our deep learning model would be improved by having the three features.
- Take one second readings.
- Send the readings to our Rasp Pi Flask app
# It Works
See [the micropython main.py code](https://github.com/BitKnitting/FitHome_Analysis/blob/master/micropython/main.py)
