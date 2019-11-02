# The FitHome Experience Flow
Through:
- An app available for ios and android devices.
- An energy monitor monitoring a home's electricity use.
- A PlugE device that coordinates monitoring and behaviorial activity within the home.
- Smart Plugs that control power outlets within a home.

The busy homeowner can make simple changes to save a significant amount of electricity.
# (1) Download the App
The homeowner downloads the FitHome app from the Appstore (ios) or Google Play store (Android).
# (2) Install Devices
The homeowner fills out the info needed within the app and schedules an appointment for an electrician to come out and install the energy monitor and PlugE device.  The electrician also leaves one Smart Plug that will be used by the homeowner once the monitor has read enough information to generate insights.
# (3) Learning Phase
The whole home monitor starts monitoring energy use.  The first milestone is getting a baseline reading.  
## Baseline Reading
This [Jupyter notebook](https://colab.research.google.com/github/BitKnitting/FitHome_Analysis/blob/master/notebooks/Baseline.ipynb) documents how the baseline is calculated.  It requires at least 3 days of readings in which there are enough readings to represent 24 hours in the day.
# (4) Insights
Once there is enough readings to provide a baseline, the FitHome experience moves from Learning to Insights state.  We have enough readings at this point to focus on areas where we can help the homeowner "trim the fat" from their electricity use.
