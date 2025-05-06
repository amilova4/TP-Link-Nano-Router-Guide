# 🚀 Install OpenWrt on TP-Link TL-WR802N V4

This guide walks you through installing OpenWrt firmware on the TP-Link TL-WR802N V4 using **Kali Linux** and TFTP recovery mode. These steps will work for any Linux distro that uses NetworkManager and APT package manager.

**If you are using a Windows or Mac computer please refer for more details to this documentation[https://openwrt.org/toh/tp-link/tl-wr802n_v4]**

## 🧰 What You Need

- ✅ TP-Link TL-WR802N (hardware version **V4**)
- 🔌 Ethernet cable
- 💻 Kali Linux computer with Ethernet port
- 📦 OpenWrt factory image for WR802N V4

**Before starting with the steps please connect your Kali Linux computer with the TP-Link Router via the ethernet cable.**

## 📥 Step 1: Download the OpenWrt Firmware

1. Go to: [OpenWrt Wiki](https://openwrt.org/toh/tp-link/tl-wr802n_v4)
2. Download the **factory image** for version 4.
![Factory images](/Firmware_Update/OpenWrt%20Firmware/images/Factor%20image.png)
3. Rename the file you downloaded as ***tp_recovery.bin***.(**It is very important to use this naming covention as it is what the router is expecting**)
4. Move it to your TFTP root directory:  
***sudo mkdir -p /srv/tftp***  
***sudo mv ~/Downloads/your_openwrt_file.bin /srv/tftp/tp_recovery.bin***

## 🌐 Step 2: Set Static IP on Kali

Assign your Kali Ethernet adapter a static IP of 192.168.0.66 by using the following commands:  
***sudo nmcli connection modify "Wired connection 1" ipv4.method manual ipv4.addresses 192.168.0.66/24***  
***sudo nmcli connection up "Wired connection 1"***

## 📡 Step 3: Install and Start TFTP Server
Installing TFTP Server:  
***sudo apt update***  
***sudo apt install atftpd***

Start the server:   
***sudo atftpd --daemon --port 69 --tftpd-timeout 300 --maxthread 100 --verbose=5 /srv/tftp***

Breakdown of the command:

| Part                  | What it means                                                     |
| --------------------- | ----------------------------------------------------------------- |
| `sudo`                | Run as administrator (needed to use port 69)                      |
| `atftpd`              | The TFTP server program                                           |
| `--daemon`            | Runs in the background (so it doesn’t block your terminal)        |
| `--port 69`           | Uses port **69**, which is what routers use to request firmware   |
| `--tftpd-timeout 300` | Waits up to **300 seconds** for a connection (5 minutes)          |
| `--maxthread 100`     | Can handle up to 100 connections at once (you only need 1)        |
| `--verbose=5`         | Shows detailed log messages — helpful for debugging               |
| `/srv/tftp`           | The folder where your firmware (`tp_recovery.bin`) must be stored |


## 🔁 Step 4: Router Recovery Mode

🔌 Unplug the router

📍 Hold the reset button with a pin

🔌 While holding reset, plug in power

⏱️ Hold for 10–15 seconds

💡 Release and you should see the light flashing.

Open another terminal and run:  
***sudo journalctl -f***

✅ If everything is running correctly you should see something like this:

![Installation](/Firmware_Update/OpenWrt%20Firmware/images/Tp_link_install.png)

## 🔄 Step 5: Switch Back to DHCP

Request a dynamic IP by using the following command:    
***sudo dhclient eth0***

## 🌍 Step 6: Access OpenWrt

If everything worked as it should so far you should be able to open a browser and access:
[http://192.168.1.1]

You will be prompted to login. Just use username ***root*** and leave password ***blank*** and click login.

![Login Page](/Firmware_Update/OpenWrt%20Firmware/images/Last_picture.png)

You will be promted to change the password as shown below:

![Menu page](/Firmware_Update/OpenWrt%20Firmware/images/openwrt.png)

## 🎉 Done!
You now have OpenWrt running on your WR802N V4!

# 🛠️ Troubleshooting
## ❌ TFTP Not Starting?

If you see something like this:

![Port Error](/Firmware_Update/OpenWrt%20Firmware/images/Port_error.png)

That means the port is occupied so run the following commands:

***sudo pkill atftpd***  
***sudo lsof -i :69*** 
Start the server again: 
***sudo atftpd --daemon --port 69 --tftpd-timeout 300 --maxthread 100 --verbose=5 /srv/tftp***  

## ❌ Can't Set Static IP?

### Bind to Ethernet interface
***sudo nmcli connection modify "Wired connection 1" connection.interface-name eth0***

### Set static IP
***sudo nmcli connection modify "Wired connection 1" ipv4.method manual ipv4.addresses 192.168.0.66/24***  
***sudo nmcli connection modify "Wired connection 1" ipv4.gateway ""***  
***sudo nmcli connection modify "Wired connection 1" ipv4.dns ""***  
***sudo nmcli connection up "Wired connection 1"***  

### Verify by running:
***ip addr show eth0***
You should see soemthing like this:

![Correct IP](/Firmware_Update/OpenWrt%20Firmware/images/correct_ip.png)

## 🔁 Router Not Booting Into OpenWrt?

Re-flash using TFTP again

Check if the LED stays off (means it's flashing)

Wait at least 2 full minutes before trying to ping





