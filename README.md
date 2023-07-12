<h1 align="center">3D Printer Control Hub</h1>
<p align="center">Setup guide for creating a Raspberry Pi EMS Printer Control Hub leveraging Octoprint</p> 

---

Connection Schematic for this demo: 

<p align="center">
  <img src="https://github.com/PTC-Education/RPi-EdgeMicroserver/blob/main/3D-Printer-Control-Hub/images/connection%20schematic.png" />
</p>


---


### Table of Contents  
* [Installing Raspbian](#raspbian)
* [Cloning the Repository](#printerControl)
* [Setting up Credentials](#keys)
* [Installing and Configuring the EMS](#ems)

### Links
* [Thingworx Developer Portal Tutorial](https://developer.thingworx.com/resources/guides/thingworx-raspberry-pi-quickstart)
---
Following this guide on a fresh install of Raspbian will allow the user to connect their Octoprint instance to Thingworx using a RemoteThing. From there we can build mashups to securey monitor
our printer's information from anywhere without worrying about port forwarding. 

---

Examples of possble mashup designs for a 3D printer control mashup with defined pages for process monitoring and remote file management.

<p align="center">
  <img width="460" height="300" src="https://github.com/PTC-Education/RPi-EdgeMicroserver/blob/main/3D-Printer-Control-Hub/images/Remote%20Monitoring.png" />
</p>

<p align="center">
  <img width="460" height="300" src="https://github.com/PTC-Education/RPi-EdgeMicroserver/blob/main/3D-Printer-Control-Hub/images/file%20management.png" />
</p>



---


<a name="raspbian"/>

<details close>
<summary>Installing Raspbian</summary>

---
  
<p>
Instead of hosting a precompiled image, we are going to go through the steps to build your own custom image on the Raspberry Pi.

You will need to download the Raspberry Pi Imager and flash the 32-Bit Raspbian OS onto a new SD card. After downloading the Raspberry Pi imager and selecting the correct os, you will see the below image. Click **Write** when ready.
</p>
<br>

<p align="center">
  <img width="460" height="300" src="https://github.com/PTC-Education/RPi-EdgeMicroserver/blob/main/3D-Printer-Control-Hub/images/raspbian-32bit.png" />
</p>

<p>
After flashing the SD card, boot the Raspberry Pi while connected to a monitor so that you are able to access the graphical user interface. Remember the default login on fresh Raspberry Pi images is **username/password: pi/raspberry**. 
</p>
<br>

<p>
From here connect to your WiFi network and ensure that ssh is enabled on your new image by typing 
</p>
  
```bash
sudo raspi-config
```
<p>
Select Interface Options > SSH > Yes to enable ssh on your system
</p>
  
---
  
</details>  





<a name="python3"/>

<details close>
<summary>Python3 Default Setup</summary>

---
  
The core of this printer control application is built off of a series of python3 scripts that are called via callback functions in our predefined services. In order for the EMS to accurately call these scripts we must ensure that the Raspberry Pi is setup to call python3 on default. 

<br>
  
In order to do this we will make use of a script made by Adafruit. The below commands will install the libraries for adafruit sensors and also set the default to python3. Answer **YES** to the prompts and wait for the process to complete.
    
```bash
cd ~
sudo pip3 install --upgrade adafruit-python-shell
wget https://raw.githubusercontent.com/adafruit/Raspberry-Pi-Installer-Scripts/master/raspi-blinka.py
sudo python3 raspi-blinka.py
```
  
If you run into issues downloading this script enter the below commands
  
```bash
# End execution of the download
^C

# Reboot the system
reboot

# In new terminal after system reboot
sudo dpkg --configure -a

# Rerun installation
sudo pyhon3 raspi-blinka.py
```

Installing Onshape-Client and Prusa-Slicer: 
 
Only do this after you have completed python3 default setup.
  
```bash
# Install onshape-client system wide
sudo pip install onshape-client
  
# Install Prusa-Slicer system wide
  
sudo apt install slic3r-prusa
```
  
---  
  
</details>




<details><summary>Installing Octoprint on Raspberry Pi</summary>

  
---

  
Once you've configured your default python on the Raspberry Pi to python 3 by running raspi-blinka.py, you can <a href="https://community.octoprint.org/t/setting-up-octoprint-on-a-raspberry-pi-running-raspbian-or-raspberry-pi-os/2337">follow this tutorial in the Octoprint forum</a> for installing and setting up Octoprint on your Raspberry Pi.
  
  
---
  
  
</details>






<a name="printerControl" />

<details close> 
<summary>Cloning the Repository</summary>

---

In order to make sure that the EMS and custom python scripts can find each other we will place all of our files in the root directory. Now we are going to clone with repository into our root folder using the below bash commands.
```bash
# Move to your root directory
cd 

# Clone this repository to your root directory. 
git clone "https://github.com/PTC-Education/RPi-EdgeMicroserver.git"
```

When cloning the repository you may have to enter your Github credentials, enter your username and password in order to authenticate the download command. Once completed, you can traverse to the **3D-Printer-Control-Hub** subfolder and investigate the folder structure. 

```bash 
cd RPi-EdgeMicroserver/3D-Printer-Control-Hub/

ls

# --- output folders ---

# - template - stores the PiTemplate.lua for the Remote Thing

# - scripts - stores all of the Python scripts driving the EMS connection
  
# - config - stores the EMS and LuascriptResource information for the Printer Control Thing setup.

# - security - contains the keys.txt file to store the users credentials

# - images - assets for the readme documentation

----------------------
```
  
---
  
</details>





<a name="keys" /> 

<details close>
<summary>Setting up Keys</summary>
  
---
  
Now that the repository has been downloaded we need to load our app keys into a keys.txt so that they will be accessible to the EMS when it is monitoring our printer instance. Included in the 3D-Printer-Control-Hub is a bash script, setup.sh, that should be run with the below command:

```bash
bash setup.sh
```

By running this script you will be prompted to enter your Thingworx, Onshape, and Octoprint credentials. These credentials will be persistently stored in the security folder of this repository in keys.txt

Before moving on to the next steps lets make sure that we accurately wrote out the api keys to the keys.txt file. In a new terminal window enter the below commands to view and verify the generated keys.txt file.

```bash
cd
  
sudo nano RPi-EdgeMicroserver/3D-Printer-Control-Hub/security/keys.txt
```
Once our api keys have been verified we can move on to install the Edge Microserver.
  
---

</details>





<a name="ems"/>

<details close>
  
<summary>Installing EMS</summary>


  
---
  
<p>
  When installing the Edge Microserver, we are going to build off the existing tutorial posted on the Thingworx Developer Portal, which can be found in the **Links** section above. In order to access the Thingworx Developer portal you must create an account. 
</p>
  
You can also run the following below to directly download the file
  
  ```
  wget https://developer.thingworx.com/-/media/developerportal/Guides/connect-raspberry-pi-thingworx/MED-61060-CD-054_SP10_Microserver-Linux-arm-hwfpu-openssl-5-4-10-1509.zip
  ```
  
<p> 
The high level of the Developer Portal Tutorial walks the user through install, api key generation, and base configuration of the remote thing. Due to the fact that we are implementing a prebuilt edge device we can skip some of the steps of the tutorial. As you progress from step 1 cross reference the differences outlined in the next section here in order to get your Printer Control Thing setup.
</p>
  
---  
  
**Step 1:**
<p>
No changes on this step
</p> 
  
---  
  
**Step 2:**

Download the EMS .zip file and drag from the Downloads folder to the root folder one level up. 
From here you can continue and unzip folder, feel free to remove the downloaded zip file with
  
```bash 
rm MED-61060-CD-054_SP9_Microserver-Linux-arm-hwfpu-openssl-5-4-6-1154.zip
```
  
---
  
**Step 3:**

If you have not made an application key for your Thingworx instance yet follow this step, but rerun **Setting up Keys** to ensure that your credentials have been accurately stored.

---

**Step 4 - 9**
  
<p>
We have already preconfigured your EMS and LuascriptResource so we will deviate from the steps here. A bash script has been written to copy over the files to the EMS server. Enter the below commands into your terminal window to start the file copy.
</p>
  
```bash 
# Move to home directory
cd ~
  
# Move to correct place for copying
cd RPi-EdgeMicroserver/3D-Printer-Control-Hub/
  
# Copy the files to EMS
bash copy.sh
```
<p>
  In order to create a unique PrinterControlThing we must rename our RemoteThing in the config.lua file. Enter the below command to open the file for editing. 
  
```bash
cd ~
  
# Move to correct place for copying
sudo nano microserver/etc/config.json
``` 
  
Upon opening you will see
  
```json
{
        "ws_servers":   [{
                        "host": "Enter host here",
                        "port": 443
                }],
        "appkey":       "Enter app key here",
        "http_server":  {
                "host": "127.0.0.1",
                "port": 8080,
                "use_default_certificate":  true,
                "ssl":  false,
                "authenticate": false
        },
        "logger":       {
                "level": "INFO"
        },
        "certificates": {
                "validate": false,
                "disable_hostname_validation":  true
        }
}
```  
Enter your Thingworx host url in the "host" field and and appkey in the "appkey" field. At this point your EMS will be configured for connection.

You will also need to alter the config.lua file with a unique name for your Thingworx server. Enter the follow bash commands to access the copied config.lua file.
  
```bash
cd ~
  
# Move to correct place for copying
sudo nano microserver/etc/config.lua
```
Upon completion you will see the file contents, in which you will have to modify the NAMEHERE with the name of your desired remote thing. 
 
```lua
scripts.log_level = "INFO"
scripts.script_resource_ssl = false
scripts.script_resource_authenticate = false
scripts.NAMEHERE = {
    file = "thing.lua",
    template = "PiTemplate",
    scanRate = 1000,
    taskRate = 30000
}
scripts.rap_host = "127.0.0.1"
scripts.rap_port = 8080
```
For example, if I wanted my RemoteThing to appear as "PrinterControlThing" on the Thingworx server, I would replace the line with NAMEHERE with
  
```lua
scripts.PrinterControlThing = {
```  
</p>
<p>
Once you have modified the config.json and config.lua files at this point in the tutorial return to Steps 5 and 7 to start the EMS and LuascriptResource. Continue to the end of the tutorial using the new Thing name that you modified on the config.lua file and your RemoteThing will be up and running!
</p>
</details>


<a name="properties"/>
