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

-we need to add ATAKs multicast group 239.2.3.1 to send traffic to the appropriate network interface that is being used to host our AP

$ sudo ip route replace 239.2.3.1/32 via 192.168.42.1

-replace 192.168.42.1 with the ip address of your raspberry pi on the AP interface, you can check this using

$ ip addr show

-we now check to make sure that our multicast address is getting routed to our specified interface/gateway ip

$ ip route get 239.2.3.1

-if 239.2.3.1 is getting routed to your AP interface (likely "wlan0") at your pi's AP IP address then we can add this command to start at boot since it will be wiped on the pis reboot

$ sudo crontab -e

- scroll down to the very bottom and add:

@reboot sudo ip route replace 239.2.3.1/32 via 192.168.20.1

-making sure to use your pis AP IP address









