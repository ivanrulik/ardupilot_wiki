.. _ros2-pi:

==========================
Purpose
==========================

The purpose of this tutorial is to set up a Raspberry Pi for running ROS 2.
The Pi will communicate with ArduPilot using DDS over serial.
Using `ros2 bag`, a ROS bag (a log) will be captured.

==========================
Hardware options
==========================

Full Hardware
1) A Raspberry Pi 4
2) A Pixhawk with 2MB flash
3) A telemetry cable to connect the Pixhawk to the Pi

Simulated Pixhawk with Pi
1) A Raspberry Pi 4
2) A computer that can run SITL
3) A USB to serial cable


==========================
Flashing the Pi
==========================

Flash Ubuntu Server 22.04.2 LTS (64-BIT) to the Pi with an 8GB or greater SD card using `Raspberry Pi Imager` 
https://ubuntu.com/tutorials/how-to-install-ubuntu-on-your-raspberry-pi#1-overview
Enable SSH. If you have ethernet, plug the Pi into ethernet, otherwise set up your WiFi credentials.
https://www.raspberrypi.com/software/

Install the SD card, and power on the Pi. 
From your computer, log into the Pi with `ssh`. I used my router's admin page to find the DHCP address.
`ssh pi@192.168.1.101`

For frequent use, copy your ssh key over. On linux, ssh-copy-id is a great tool.
`ssh-copy-id pi@192.168.1.101`


Now, ssh into the Pi. Start with an update and upgrade right away.

```
sudo apt update && sudo apt upgrade
```
If prompted about a new kernel, select Ok twice, then reboot.
```
sudo reboot
```

If using Linux on your computer, install `avahi-daemon` on the Pi to make it easy to find the IP address.

```
sudo apt update && sudo apt install avahi-daemon
```
Then, turn workstation broadcasting on by editing the file with your favorite text editor.
```
sudo nano /etc/avahi/avahi-daemon.conf
```
Under the `[publish]` section, ensure `publish-workstation` is set to `yes`.
```
publish-workstation=yes
```

Save, then restart avahi. 
```
sudo systemctl restart avahi-daemon.service
```

Now, log out of the Pi, and now you can use avahi-browse to find the pi's address.
```
avahi-browse -arlt

...
+ wlp13s0 IPv6 ubuntu [dc:a6:32:82:f5:6b]                    Workstation          local
+ wlp13s0 IPv4 ubuntu [dc:a6:32:82:f5:6b]                    Workstation          local
= wlp13s0 IPv6 ubuntu [dc:a6:32:82:f5:6b]                    Workstation          local
   hostname = [ubuntu.local]
   address = [fe80::dea6:32ff:fe82:f56b]
   port = [9]
   txt = []
= wlp13s0 IPv4 ubuntu [dc:a6:32:82:f5:6b]                    Workstation          local
   hostname = [ubuntu.local]
   address = [192.168.1.101]
   port = [9]
   txt = []
```


Log back in to the Pi. Use the address above, or its local domain name.
```
ssh pi@ubuntu.local
```

Install ROS 2 using Debians.
https://docs.ros.org/en/humble/Installation/Ubuntu-Install-Debians.html

Since we installed Ubuntu Server, don't install `ros-humble-desktop`. Only install `ros-humble-ros-base` and `ros-dev-tools`.
This takes 421MB + 162MB = 583MB of storage at this point.


Enable DDS on ArduPilot, and run it. 
Run the micro ros agent on the Pi.
Echo topics.

TODO maybe add figures for the process
