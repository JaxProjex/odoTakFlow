# odoTakFlow
Node-Red Flow to forward MQTT payload from odo to ATAK via multicast or TAKServer. (used in conjunction with Ampledatas node-red-contrib-tak)

![node red flow](/odoTakFlow.png?raw=true "Node Red Flow")

REQUIREMENTS:

-Node-Red Server installed on Pi

-node-red-contrib-tak

-node-red-contrib-gpsd

-GPS Module (USB or UART)

-GPSD Service installed

-Info on odo here: https://github.com/Red-Team-Alliance/Odo

-----------------------------------------------

SETUP NODE-RED:

-https://nodered.org/docs/getting-started/
-options include installing node-red on your local PC, a Raspberry Pi or similar single board computer or mini PC, deploying node-red on a cloud server or virtual private server (VPS), Docker Container, or local virtual environment. After installing node-red you should be able to go to the node-red dashboard at http://*nodeRedIPaddress:1880 you may have to open appropriate ports (1880) to allow devices to access the node-red dashboard.

-ensure you install the "node-red-contrib-tak" node. In Node-Red: Menu (3 horizontal lines) > Manage palette > Install > Search "node-red-contrib-tak" > Install > Install

-do the same for: node-red-contrib-gpsd

--------------------------------------------------

IMPORT .JSON FLOW TO NODE RED:

-in GitHub: click on "odoTakFlow.json" > click on the download icon "Download raw file" > note where the "odoTakFlow.json" file downloaded to, default is in your Downloads folder
-in Node-Red: click on menu icon (3 horizontal lines top right) > click on "Import" > click on "select a file to import" > go to Downloads folder and click on "odoTakFlow.json" > Upload > Import

ALTERNATIVELY..
-you can just copy the whole "odoTakFlow.json" code from GitHub and paste it into the Node-Red Import Clipboard.

--------------------------------------------------

MQTT IN NODE:

-configure the "mqtt in" node of the necessary subscribed topic and the authentication needed (if necessary).

---------------------------------------------------

SETUP GPSD:

$ sudo apt install gpsd gpsd-clients

$ ls /dev

-look at what tty device gets added after connecting your GPS Module. to test if gps data is getting sent you can do (replacing with your tty device)..

$ cat /dev/ttyACM0

-once you find the device you can place that device in gpsd config:

$ sudo nano /etc/default/gpsd

-config and add: DEVICES="/dev/ttyACM0" #replacing tty with your device***

-kill any existing gpsd services and manually start gpsd on your gps dev, then check gps status of gpsd

$ sudo killall gpsd

$ sudo gpsd /dev/ttyACM0 -F /var/run/gpsd.sock

$ cgps

-additionally you could restart, stop or start the gpsd service if needed at any point

$ sudo service gpsd restart

---------------------------------------------

CONFIGURE MULTICAST ADDRESS TO INTERFACE:

-check multicast addresses for network interfaces:

$ ip maddr

-likely well need to add 239.2.3.1 (ATAK Multicast) to our wlan0 for our RPi AP, unless using a wificard as AP then it could be wlan1.

$ ip addr add 239.2.3.1/32 dev wlan0

-multicast should work if multiple atak devices are connected on the RPi AP but now we need the pi to multicast a CoT and make sure it gets sent to that wlan0 traffic.

$ ip route add 239.2.3.1/32 dev wlan0

-now we check that ATAK multicast address gets sent to our wlan interface

$ ip route get 239.2.3.1

-node-red RPi should now be able to send CoT via multicast to ATAK devices connected to its AP.









