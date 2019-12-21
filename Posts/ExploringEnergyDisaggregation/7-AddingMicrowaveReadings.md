# Adding Microwave Readings
I wanted to get aggregate readings that included known times when an appliance within the aggregate was on or off.  The goal is to get aggregate readings with columns like 'microwave','washing macine', etc. that have a 1 in the row for the timestamped reading if on, 0 if not.  

I started with microwave readings.
# System Setup
Shown in the overview image:
![microwave on,off](/images/ExploringDisaggregation/microwave_on_off.png)
- Aggregate readings are sent from the monitor at the breaker box to the Rasp Pi.
- Microwave readings are sent from the microwave's smart plug to the Rasp Pi.
- The Rasp Pi uses a simple

start collecting microwave data.
device name = ‘microwave’
real time on/off.
start_threshold = P > std dev of stuff I’ve taken = std dev = 126W so P readings > 126.
on_threshold >126 P < 2000 
off = anything else.