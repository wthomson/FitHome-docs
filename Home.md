# Welcome
Welcome to the FitHome Wiki. 
# The FitHome Experience
The FitHome experience runs for one month within a home.  The experience reads a homeowner's electricity use and sends these readings to a database that is read by our FitHome Analysis service.  The FitHome Analysis service learns about a homeowner's electricity use.  The Analysis service studies the power and current sample to optimize personal electricity savings insights.  These insights are displayed by the FitHome Smartphone app.  The Smartphone app will let the homeowner quickly know how much electricity they are using.  The app will also provide personalized insight and guidance that makes it easy for a busy homeowner to save at least 15% of their electricity use.
# High Level System Components
![overview](images/Overview/Overview.png)
## (A and B) Electricity Monitors to DB
The electricity monitoring hardware includes:
- An [electricity monitor](https://github.com/BitKnitting/FitHome/wiki/ElectricityMonitor) within a homeowner's breaker box.  
- A [PlugE device](https://github.com/BitKnitting/FitHome/wiki/PlugE) within the home.
- One or more [TP-Link HS110 Smart Plugs](https://amzn.to/2MFSVmH). 
  
The readings are sent by the electricity monitor and the PlugE device to a Firebase RT db (B).  
## (C) Analysis
A backend service scoops up the electricity readings.  It uses data analytics and deep learning to provide personalized insights to the homeowner.
## (D) FitHome App
The [FitHome App](https://github.com/BitKnitting/FitHome/wiki/FitHomeAppExperienceFlow) gives the busy homeowner insights into how to quickly and painlessly lower the amount of electricity they use.  As they lower their use, they see the financial and climate change impact they are making.



