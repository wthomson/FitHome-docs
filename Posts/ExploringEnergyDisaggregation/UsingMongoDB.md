
# GitHub location
I run git on my Rasp Pi to push changes to the [RaspPi_mongodb](https://github.com/BitKnitting/RaspPi_mongodb) project.
# Installation
Installing on a Rasp Pi configured as discussed in the [PlugE post](../../PlugE.md)
```
sudo apt install mongodb
sudo systemctl enable mongodb
```
_Note: [In a previous project, I wrote up what I learned about systemctl](https://github.com/BitKnitting/should_I_water/wiki/systemd-services)
I was not able to get into mongo with `$mongo`  I would get the error `Error: couldn't connect to server 127.0.0.1:27017 at src/mongo/shell/mongo.js:145`.  The error resolved when I:  
  
```
sudo rm /var/lib/mongodb/mongod.lock
sudo service mongodb restart
```
I could then try [the simple example given on this blog post](https://thedatafrog.com/mongodb-remote-raspberry-pi/). 

## Project Directory  
```
pi@raspberrypi:~/projects/mongodb_learn $
```  
Changing permissions:
```
pi@raspberrypi:~$ sudo chmod -R o+rwx directory
```

## Virtual Environment
```
$ sudo apt-get install python3-venv   

pi@raspberrypi:~/projects/mongodb_learn $ python3 -m venv venv --prompt m_test   

pi@raspberrypi:~/projects/mongodb_learn $ source venv/bin/activate  

(m_test) pi@raspberrypi:~/projects/mongodb_learn $
```
# Using Flask
I followed along with the [Connecting to a MongoDB in Flask Using Flask-PyMongo](https://www.youtube.com/watch?v=3ZS7LEH_XBg&list=PLXmMXHVSvS-Db9KK1LA7lifcyZm4c-rwj&index=3).  I am using MongoDb on the Rasp Pi and not from a cloud service.

## Setting the Port
One challenge I have had in the past is running on the default `5000` port.  I found out if `FLASK_RUN_PORT=<port number>` is in .flaskenv, Flask will use the port set in .flaskenv.  This [stackoverflow post](https://stackoverflow.com/questions/50389273/how-to-get-all-available-command-options-to-set-environment-variables) shows how we can go from flash run --help to setting FLASK_RUN_<command in CAPS> from the .flaskenv file.
## Setting the Host
Similarly to the port, I needed to set the host from 127.0.0.1 to 0.0.0.0...`FLASK_RUN_HOST=<host IP address>`

## Flask / Python

```
(m_test) pi@raspberrypi:~/projects/mongodb_learn $ pip install flask flask-pymongo python-dotenv  
```
## Some Simple Mongo Command
First, get into the mongo client: `$mongo`.  Go to your database: `>use YOURDATABASE`.  `>show collections` 
```
> show dbs
> show collections
> use testdata
switched to db testdata
> show collections
system.indexes
users
> db.users.find()
{ "_id" : ObjectId("5dd94f0074fece1e23950257"), "name" : "Cristina" }
{ "_id" : ObjectId("5dd94f0174fece1e23950258"), "name" : "Derek" }
{ "_id" : ObjectId("5dd975e574fece1e23950259"), "name" : "Cristina" }
{ "_id" : ObjectId("5dd975e574fece1e2395025a"), "name" : "Derek" }
```

  
