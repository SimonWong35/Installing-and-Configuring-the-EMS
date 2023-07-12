<h1 align="center">Guide for installing and configuring the EMS</h1>


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
Unzip the zipped file.
</p> 
  
---  
  
**Step 2:**

Download the EMS .zip file and drag from the Downloads folder to the root folder one level up. 
From here you can continue and unzip folder, feel free to remove the downloaded zip file with
  
```bash 
rm MED-61060-CD-054_SP10_Microserver-Linux-arm-hwfpu-openssl-5-4-10-1509.zip
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
