# Getting Plug Readings
Our ultimate goal is to detect when the device being monitored by a smart plug is on in the aggregated data.  Let's say I'm using the microwave to heat something up for 2 minutes.  While we collect aggregated Pr, Pa, I aggregated data, we'll also collect, 

Additionally, as we collect different types of devices, we can start simulating aggregated data by combining multiple device readings so that we'll have more training data.
## Our Requirements
This makes our requirements:
- The PlugE software does real time detection of when a device is on and when it is off.
- When the device is detected to be on, the PlugE software writes the power and current readings to our mongoDb.  This way, we record the signature of the device.  Unfortunately, reactive power readings are not available through the interface with the HS110.
- The PlugE software sends the device state change (on or off) to the [Flask Service](https://github.com/BitKnitting/FitHome_mongodb) that is storing the aggregated readings.
- Flask Service notes the state change for the device and changes recording to either a 0 or 1...depending on the previous state within the row for aggregegated readings.
# Detecting when the microwave is on/off
To simplify, we're focusing on a microwave.  Of course this could be any device.  The goal is to let the aggregated reading know when the microwave has been turned on and then off.  This way, a column of 0 (off) and 1 (on) is created on each sample row.  Worth noting: We don't need to plug's microwave readings as much as we need the trigger for the microwave on/off.  We are exploring alternative sensor methods to detect this based on human interaction.  Initially we will be using the [TP-LINK HS110](https://amzn.to/2WBHPUc) as we are using in [PlugE](../../PlugE.md).
We made the following changes to the PlugE code:
- Get the name of the device (should be descriptive to the device, e.g.: microwave)
- detect from readings when on/off.
- send Post to RaspPi Mongodb when change in state from on/off.

Follow up: 
- [First attempt at capturing microwave readings within aggregate readings](/Posts/ExploringEnergyDisaggregation/7-AddingMicrowaveReadings.md).