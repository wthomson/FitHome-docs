# Welcome
 
  
Welcome to the FitHome Wiki. Think of the FitHome experience as a home health Personal Trainer for the busy homeowner. During a one month training period, the homeowner gets personalized, simple guidance on the easiest way to lower electricity consumption by at least 15%. The homeowner receives immediate feedback, including the amount of money you are saving and your impact on helping to minimize climate change. It's amazing how much electricity we waste yet don't have any idea how we can use less with no change to our lifestyle. Saving electricity means saving money and making a positive impact on climate change. The FitHome Experience gets us mindful and connected to a future that is reliant on renewable energy.
# The FitHome Experience
The FitHome experience runs for one month within a home.  The experience reads a homeowner's electricity use and sends these readings to a database that is read by our FitHome Analysis service.  The FitHome Analysis service learns about a homeowner's electricity use.  The Analysis service studies the power and current sample to optimize personal electricity savings insights.  These insights are displayed by the FitHome Smartphone app.  The Smartphone app will let the homeowner quickly know how much electricity they are using.  The app will also provide personalized insight and guidance that makes it easy for a busy homeowner to save at least 15% of their electricity use.
# High Level System Components
![overview](images/Overview/Overview.png)
## (A and B) Electricity Monitors to DB
Monitors send readings  to a Firebase RT db (B).  
  
The electricity monitoring hardware includes:
- An [electricity monitor](https://github.com/BitKnitting/FitHome/wiki/ElectricityMonitor) within a homeowner's breaker box.  
- A [PlugE device](https://github.com/BitKnitting/FitHome/wiki/PlugE) within the home.
- One or more [TP-Link HS110 Smart Plugs](https://amzn.to/2MFSVmH). 
  
## (C) Analysis
A backend service scoops up the electricity readings.  It uses data analytics and deep learning to provide personalized insights to the homeowner.
## (D) FitHome App
The [FitHome App](https://github.com/BitKnitting/FitHome/wiki/FitHomeAppExperienceFlow) gives the busy homeowner insights into how to quickly and painlessly lower the amount of electricity they use.  


