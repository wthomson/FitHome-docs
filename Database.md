The Database holds status and accountability info as well as th eenergy readings.  We currently use Firebase RT because:
- The APIs are well thought out when using with the Flutter platform.
- The pricing was favorable to db writes when compared to Cloud Firestore.  
- Google is responsible for both Firebase and Flutter.  Easier to work with one vendor for the front and back-ends.
# Status and Accountability
The monitor is in one of these states:
* not_active - The homeowner has filled out the information FitHome needs to create a member. 
* learn - The monitor has been installed.  It is gathering data so that the system can personalize electricity savings advice.  
* active - Enough learning data has been gathered to make initial personalized electricity savings recommendations.  

If the member does not have a monitor node, they are in a wait state.  Else, they have been assigned a monitor.

# Database Paths
All paths used by FitHome is contained within [DB_model.dart](https://github.com/BitKnitting/FitHome_app/blob/master/lib/database/DB_model.dart)
There are several db paths that are used to manage the status and recent activity of the FitHome experience.  
## available_monitors
One of the db paths is <firebaseProjectID>.firebaseio.com/available_monitors.  The available monitors node holds the list of available monitors by name and whether they are in use.  As an example, we created a json file of available_monitors for use when testing the app:
```
{
  "mango": true,
  "apple": true,
  "blue": true,
  "pokey": true,
  "flower": true,
  "thumper": true,
  "bambi": true,
  "frank": true,
  "gumbi": true,
  "lucy": true
}
```
After creating the available_monitors node by typing the URL in the browser, e.g.:
```
https://console.firebase.google.com/u/0/project/<firebase project id>/database/fithome-9ebbd/data/available_monitors
```
we import this json file.  
### Monitor Assignment
When the homeowner signs up for FitHome training, an available monitor is assigned.  If a monitor is not available during sign up, a notification is logged within the database.  The homeowner is sent to the WaitListPage.
