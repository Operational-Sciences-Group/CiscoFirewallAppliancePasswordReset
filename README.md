# How To TedTalk-Reset a Cisco Firewall Appliance Password
Hi, welcome to my Ted Talk.  Today we're learning how to reset the password on a Cisco Firewall Appliance.  (Model tested ASA5505)\

# Preliminary: Get the stuff you need.
Putty\
A serial Cisco Rollover Cable (Yost Cable)\
A USB to DB-9 cable (because who the fuck has a serial port on a computer anymore).  I prefer BENFEI's USB to DB-9, as it ships with a proprietary driver on an install disc that ensures COM port availability.

# Step 1: Connect to the appliance.
Connect your rollover cable to the console port on the appliance.\
Note: While you maybe connecting to an RJ-45 Ethernet port, the connection needs to be made over serial- this is why alot of the proprietary Cisco cables are DB-9 to RJ-45.\
In my case, I used a USB to DB-9 cable that came with proprietary drivers (So that my PC would 'see' the COM port), then connected that cable to the Rollover Cable into the device.\
Next, open Putty and establish a connection on your active COM port @ baud 9600, 8 parity bits, and 1 stop bit.

# Step 2: Get into it.
While leaving your serial connection open, power cycle the appliance by disconnecting and reconnecting the power supply.\
While the device is beginning it's boot scroll press [esc] to halt the boot process.  This will put you into ROM Monitor mode.

# Step 3: Booting without the password with rommon.
Your prompt should display 'rommon #0>'.  Enter the 'confreg' command to view the current configuration register setting.\
The current register should be the default 0x01.  The terminal will ask if you want to make changes to the configuration register, answer no when prompted.\
Next, we will change the configuration register to 0x41, to tell the appliance to ignore it's startup configuration upon boot, using:\
'rommon #1>confreg 0x41'\
Next, reset the appliance with the boot command:\
'rommon #2>boot'

# Step 4: Reconfiguring the password.
After the appliance fully boots, you should be faced with the standard CLI prompt of (in the case of an ASA model) 'ciscoasa>'\
To enter privleged mode, enter:\
'ciscoasa>enable'\
When prompted with the password, press enter without entering anything.  Your CLI prompt should now show the # for privileged mode:\
'ciscoasa#'\
Next, enter the appliance configuration mode with:\
'ciscoasa#conf t'\
Your CLI prompt should now show:\
'ciscoasa(config)#'\
Next, configure the desired password for the device (this example uses yourpassword as the filler for, well, your password.):\
'ciscoasa(config)#enable password yourpassword'\
While we are still in configuration mode, reset the configuration register to the default to force the device to read the startup configuration at next boot with:\
'ciscoasa(config)#config-register 0x01'\
Exit the appliance configuration mode with:\
'ciscoasa(config)#exit'\
To verify that the previous command has been accepted, and that the device will return to 0x01 configuration register, use the command:\
'ciscoasa#show version'\
Enter down, until you see something similar to:\
'Configuration Register is 0x41 (will be 0x01 at next reload)'\
Now, let's restart the device with:\
'ciscoasa#reload' - you will be prompted to save configuration changes, enter yes to save our password reconfiguration.  You will then be asked to reconfirm the restart, press enter to reconfirm.

# Step 5: Verifying our new password.
Once the appliance finishes rebooting, you should be met with the 'ciscoasa>' prompt.  Enable privileged access again with:
'ciscoasa>enable'\
When prompted for a password, enter the password we configured in step 4.  You should now be in a standard priviledged access mode with the prompt 'ciscoasa#'.
