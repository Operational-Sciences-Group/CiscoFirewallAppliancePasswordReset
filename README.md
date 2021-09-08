# Cisco Firewall Appliance Firewall Password Reset
###### Hi, welcome to my Ted Talk. Today we're learning how to reset the password on a Cisco Firewall Appliance.  (Model tested ASA5505)

## Preliminary: Get the stuff you need.

- [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/)
- A serial Cisco Rollover Cable (Yost Cable)
- A Computer with a serial port. Or USB to DB-9 cable.  (I prefer BENFEI's USB to DB-9, as it ships with a proprietary driver on an install disc that ensures COM port availability.)


# Step 1: Connect to the appliance.
1. Connect your rollover cable to the console port on the appliance. (Note: While you maybe connecting to an RJ-45 Ethernet port, the connection needs to be made over serial- this is why alot of the proprietary Cisco cables are DB-9 to RJ-45.)
2. In my case, I used a USB to DB-9 cable that came with proprietary drivers (So that my PC would 'see' the COM port), then connected that cable to the Rollover Cable into the device.
3. Next, open PuTTY and establish a connection on your active COM port @ baud 9600, 8 parity bits, and 1 stop bit.

# Step 2: Get into it.
4. While leaving your serial connection open, power cycle the appliance by disconnecting and reconnecting the power supply.
5. While the device is beginning it's boot scroll press [esc] to halt the boot process.  This will put you into ROM Monitor mode.

# Step 3: Booting without the password with rommon.
6. Your prompt should display ```rommon #0>```.  Enter the ```confreg``` command to view the current configuration register setting.
7. The current register should be the default 0x01.  The terminal will ask if you want to make changes to the configuration register, answer ```no``` when prompted.
8. Next, we will change the configuration register to 0x41, to tell the appliance to ignore it's startup configuration upon boot, using: ```rommon #1>confreg 0x41```
9. Next, reset the appliance with the boot command:```rommon #2>boot```

# Step 4: Reconfiguring the password.
10. After the appliance fully boots, you should be faced with the standard CLI prompt of (in the case of an ASA model) ```ciscoasa```
11. To enter privleged mode, enter: ```ciscoasa>enable```
12. When prompted with the password, press enter without entering anything.  Your CLI prompt should now show the # for privileged mode: ```ciscoasa#```
13. Next, enter the appliance configuration mode with: ```ciscoasa#conf t```
14. Your CLI prompt should now show: ```ciscoasa(config)#```
15. Next, configure the desired password for the device : ```ciscoasa(config)#enable password <yourpassword>```
16. While we are still in configuration mode, reset the configuration register to the default to force the device to read the startup configuration at next boot with:\ ```ciscoasa(config)#config-register 0x01```
17. Exit the appliance configuration mode with: ```ciscoasa(config)#exit```
18. To verify that the previous command has been accepted, and that the device will return to 0x01 configuration register, use the command: ```ciscoasa#show version```
19. Enter down, until you see something similar to: ```Configuration Register is 0x41 (will be 0x01 at next reload)```
20. Now, let's restart the device with: ```ciscoasa#reload``` - you will be prompted to save configuration changes, enter ```yes``` to save our password reconfiguration.  You will then be asked to reconfirm the restart, press [enter] to reconfirm.

# Step 5: Verifying our new password.
21. Once the appliance finishes rebooting, you should be met with the ```ciscoasa>``` prompt.  
22. Enable privileged access again with: ```ciscoasa>enable```
23. When prompted for a password, enter the password we configured in step 15.  
***You should now be in a standard priviledged access mode with the prompt 'ciscoasa#'.***
