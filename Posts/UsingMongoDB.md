
# Installation
```
sudo apt install mongodb
sudo systemctl enable mongodb
```
I was not able to get into mongo with `$mongo`  I would get the error `Error: couldn't connect to server 127.0.0.1:27017 at src/mongo/shell/mongo.js:145`.  The error resolved when I:  
  
```
sudo rm /var/lib/mongodb/mongod.lock
sudo service mongodb restart
```
I could then try [the simple example given on this blog post](https://thedatafrog.com/mongodb-remote-raspberry-pi/). 
  
