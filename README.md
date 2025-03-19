DALS RGB LED Controller – ESPHome Conversion
This project provides an overview of how to convert a DALS RGB LED Strip Controller to ESPHome and how to integrate it into Home Assistant. This method should also work for flashing Tasmota to the controller. The DALS RGB WIFI LED Strip is powered by an ESP8285MOD chip with soldering pads on the board that make it easy to flash. The controller includes a microphone and an IR sensor (used for the included remote) that will not be included in this tutorial but may be able to be leveraged by modifying the ESPHome bin file.
![image](https://github.com/user-attachments/assets/590c6b6d-1dc3-46a0-a220-a3b0017d9a44)![image](https://github.com/user-attachments/assets/47f36f51-ee07-470d-a607-db69eec349dd)

Pre-requisites:
This project assumes you have a functioning instance of Home Assistant running on your network (https://www.home-assistant.io/) and know how to solder.

Materials required:
•	DALS Outdoor WIFI LED Strip (Model : IU-RGBWWIFI24DW)
•	Serial-to-USB Adapter
•	Jumper wires
•	Soldering iron

Optional:
•	3D printer to print new enclosure
•	Hot Glue Gun

STEP 1: Disassemble DALS controller
•	The large brick that comes in the box is a power supply – you can ignore it for now.
•	Remove the back cover from the controller housing (the little piece with a hemisphere and 2 wires coming out that connect to the power supply and to the LED strip).
o	The housing is adhered VERY well since this is an outdoor-rated product. You may need to use a dremel like I did to remove the cover.
STEP 2: Soldering
•	On the back of the board there are soldering pads for GND, 3.3V, TX, RX, and IOO. Solder a jumper wire to each of these.
o	Note: Jumper wires with a female dupont connector can be very helpful in making the connection to the Serial-to-USB adapter board
o	For the IOO pad use a MALE dupont connector on the side that is not soldered, or simply strip the end of the wire. This wire will be touched to ground during boot up to put the board into programming mode
•	Once soldered, connect the following wires on the Serial-to-USB Adapter to the DALS controller board:
o	GND to GND
o	VCC to 3.3V
o	TX to RX
o	RX to TX
o	NOTE: If you have jumper pins on your adapter board, make sure they are connected to 3.3V and not 5V
•	Double check your connections before moving to the next step.
•	WARNING: Make sure you do NOT have the 24V power supply connected. This could feed power through the USB port to your computer and damage it.

STEP 3: Preparation for Programming/Flashing
•	Plug your Serial-to-USB adapter into your computer. Let drivers install (or find appropriate drivers for your board manually)
•	Disconnect Serial-to-USB adapter from computer
•	Bridge GPIOO jumper wire to GND by touching the soldering pad or the lead on your Serial-to-USB adapter, then plug adapter into computer
o	After a few seconds you can remove the wire. Alternatively, you can choose to solder the jumper to the GND pad/pin and leave it there for the entire flashing process. If you do this, be sure to remove the jumper after you finish flashing to allow the device to boot normally.

STEP 4: Installing ESPHome on your board (https://esphome.io/) 
•	Go to the Getting Started with ESPHome and Home Assistant page: https://esphome.io/guides/getting_started_hassio.html
•	Install ESPHome Dashboard following the instructions. This installs an add-on in Home Assistant Supervisor which you can use to manage your ESPHome devices.
•	Make sure you have the correct driver for your board (see the drivers note on the getting started page)
•	Once the ESPHome Dashboard add-on is installed you can access it by going to Home Assistant > Supervisor > Dashboard > ESPHome
•	Click Start and wait a bit for it to start up. Once it is started, Click on Open Web UI  
![image](https://github.com/user-attachments/assets/1f6cf793-b94b-429d-aca0-e473eec01291)
•	Click the green + in the bottom right to add a new device  
![image](https://github.com/user-attachments/assets/4b1ab660-e58e-4545-81d0-ae9d187bbd56)
•	Input your name (dals-rgb-led-controller), Wi-Fi SSID, and Wi-Fi password
o	NOTE: Most ESP boards only work on 2.4 GHz WIFI networks.
•	When asked for the ESP device select use "Pick specific board" and under “Other ESP8266s” select “Generic ESP8285 module”
![image](https://github.com/user-attachments/assets/959ff840-cde0-46c3-ad8b-94975647dec0)

•	Your device configuration is created!
•	Click INSTALL, Plug into this computer (NOTE you need to be connected to Home Assistant using HTTPS in your URL, not HTTP for initial configuration to work through the browser), and follow the prompts.
o	If you aren't using HTTPS or are having trouble you can use esphome-flasher (https://github.com/esphome/esphome-flasher/releases). Download the appropriate file for your file system and run it. You should see a screen that looks like this:
 ![image](https://github.com/user-attachments/assets/3bf4cfaa-3d2b-4b4a-a925-4fa5d88a7b2b)

•	Under the Serial port dropdown, select the COM port for your device. There should only be one, but if you see multiple then unplug your device, refresh, and see which port disappeared. Plug your device back in and choose the port that just showed up.  
![image](https://github.com/user-attachments/assets/b1215520-a299-4bc5-8b13-f3a8180e372a)

•	In the ESPHome Dashboard, click INSTALL and select Manual download. Let the software compile the file (a minute or so) and save the file to your desktop or downloads folder for easy access.
![image](https://github.com/user-attachments/assets/3c587482-fe8e-4ecf-920f-223305c1bfa2)

 
•	In esphome-flasher, click BROWSE next to the Firmware field and select the .bin file that was just downloaded
•	Verify that you have the correct port and file selected, then click Flash ESP  
![image](https://github.com/user-attachments/assets/478e1ee6-b7fc-4a15-8a6d-4537ab23aca7)

•	It should finish uploading in about 30 seconds and connected to your Wi-Fi. You can now disconnect the controller from the serial-to-USB adapter and power the device from the 24V power adapter. Moving forward you can update over the air (OTA).
 ![image](https://github.com/user-attachments/assets/e1106cb9-5b93-4236-b620-a770ff877276)


•	You should now see your device in the ESPHome Dashboard with a green line indicating it is online. The default configuration file doesn't do anything though, so in Step 2 we will add the features we want.

![image](https://github.com/user-attachments/assets/60da09e1-b810-4e3b-bc29-15ab9999cfca)![image](https://github.com/user-attachments/assets/b12ceed3-c906-4f21-8744-4cb1ee9452bc)


   	
•	At this point you can de-solder the jumper wires from the board and put the controller back into an enclosure.

STEP 5: Modifying the configuration to incorporate the items we want
•	Click EDIT on the new device you just created.
•	Leave all of the text in the existing file and add the following at the end (see the dals-rgb-led-strip.yaml file for full configuration file text):
![image](https://github.com/user-attachments/assets/5a80653e-ea33-47da-83d8-cb01df68ef5d)

 
•	That's it, we're done with the configuration file. Click SAVE in the bottom right corner and close.
•	Make sure your device is powered on and online in ESPHome. Click VALIDATE and make sure you see "Configuration is valid!" at the top of the window that pops up, then if everything looks good click INSTALL in the lower right corner, select Wirelessly, and you should see information indicating the upload was successful (see screenshot below).
![image](https://github.com/user-attachments/assets/38def820-e73c-4631-8aa9-ca28d18855d2)

•	Check your Home Assistant notifications (bottom left of Home Assistant and you should have "New devices discovered" notification 
![image](https://github.com/user-attachments/assets/0a8b38d9-b996-4504-82a3-d56286ffa35f)

 
•	Click "Check it out" and you should see your salt-level ESPHome device. Click CONFIGURE and then SUBMIT
![image](https://github.com/user-attachments/assets/dce62c6b-095a-4d02-80d6-16ab339261b0)

 
•	Then select the Area it is in (if you want, you can always change this later) and click FINISH
•	In Home Assistant go to Devices and find the device you just added. You should be seeing a single light switch. Clicking the light switch should turn your LED strip on and off. 
o	The White LEDs are controlled separately from the RGB LEDs – still working on addressing this.
•	If you need to make any updates, you can modify the configuration any time by updating the config file in ESPHome and installing wirelessly again.
 ![image](https://github.com/user-attachments/assets/a184bef7-338c-4b1a-9e65-48aa246be05a)
