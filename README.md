# oem_node-red

Default Node-RED config for emonPi

# Install using NPM 
We might switch to using Deb PPA in the future but for now we need latest version (12.4) from NPM

http://nodered.org/docs/hardware/raspberrypi.html

At time of writing: 

    sudo apt-get remove nodered
    
To install Node.js on Pi 2 - and other Arm7 processor based boards, run the following commands:

    curl -sL https://deb.nodesource.com/setup_4.x | sudo bash -
    sudo apt-get install -y build-essential python-dev python-rpi.gpio nodejs
    sudo npm install -g --unsafe-perm  node-red

## Start node Red at boot:

http://nodered.org/docs/hardware/raspberrypi.html#making-node-red-autostart-on-boot

## View node Red log 
(now in /var/syslog) filter with

    $ sudo journalctl -f -u nodered -o cat

## Move user data partition to RW ~/data partition on emonPi

    mv ~/.node-red ~/data/node-red

Test with:

    node-red-pi --max-old-space-size=128  --userDir /home/pi/data/node-red

Start at boot using custom user data location 

    sudo nano /lib/systemd/system/nodered.service

Add line
  
    Environment="NODE_RED_OPTIONS=--userDir /home/pi/data/node-red"
    sudo systemctl daemon-reload
    
## Make authentication work on read-only (12.4 +)
https://groups.google.com/forum/#!topic/node-red/qC0PC0yTpLk

    sudo nano ~/.node-red/settings.js

Add line:

    readOnly: true

in module.exports. 

 The storage plugin won't do anything that writes to disk - the operations will silently fail, so the runtime will continue as normal, but nothing will be saved.
 
## Add authentication security for flow editor

    sudo nano /home/pi/data/node-red/settings.js

Default settings file example: https://github.com/node-red/node-red/blob/master/settings.js

copy adminAuth security section and generate password hash

    node -e "console.log(require('bcryptjs').hashSync(process.argv[1], 8));"  your-password

if required: $ sudo npm install bcryptjs

## Install emoncms flow
    
    $ cd ~/data/node-red
    $ npm install node-red-node-emoncms
    
    
