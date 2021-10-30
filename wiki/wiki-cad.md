---
sort: 2
---

# CAD 
This wiki contains all the details (except the private and propreitary info) for the CAD tools used at Advanced VLSI Lab at SIT, BBSR.

## OPEN-SOURCE CUSTOM DESIGN FLOW 
This section contains the instruction to setup open-source CAD tools (ngspice, Sue2, xschem, magic & netgen) in a Virtual Machine (Virtual Box) running a Ubuntu-based Linux distribution LXLE. Although the binaries of the CAD tools are compiled and tested on a 64-bit LXLE Linux distribution, it should run on other 64-bit Ubuntu or Debian based distribution like Xubuntu.

- [**YouTube PLaylist-OpenSourceEDA**](https://www.youtube.com/playlist?list=PL7R2OODNugWFY2qeZ7qlVFNIkN8ABhuSO):  Santunu Sarangi, Satabdi Panda and Pracheeta Mohapatra, "[15VLSI7T](https://github.com/silicon-vlsi/15VLSI7T):VLSI Design Course for 7th Sem AEI, 2020" -- Set of detailed videos of Labs conducted for the course using ngspice, sue2, magic and netgen.

**SETTING UP LXLE LINUX ON VIRTUAL BOX**
- [**Linux on Virtual Box**](https://www.dropbox.com/s/2lovix0ntsw8yfw/2020-0917-Open%20Source%20EDA%20Setup.pdf): Step-by-Step instruction on how to setup LInux LXLE on Virtual Box. 
- Here are some of the essential steps:
  - Download and install Virtual Box (with default settings) from http://virtualbox.org
  - Download the 64-bit Linux LXLE Iso Image from http://www.lxle.net/download (eg. lxle-18043-64.iso). **NOTE** The CAD tools are all compiled for 64-bit OS.
  - Start Virtual Box and click **New** and follow the steps to complete the installation. Check the above [doc](https://www.dropbox.com/s/2lovix0ntsw8yfw/2020-0917-Open%20Source%20EDA%20Setup.pdf)

**DOWNLOAD AND SETUP THE CAD TOOLS**

[**YouTube Link**](https://www.youtube.com/watch?v=GUHCrM-v24w) demonstrating the download and install of the following tools:

- [**NGSPICE**](https://github.com/silicon-vlsi-org/eda-ngspice): See Download and Setup instruction in the [gitHub page](https://github.com/silicon-vlsi-org/eda-ngspice#downloading-&-setting-up-ngspice).
- [**SUE2**](https://github.com/silicon-vlsi-org/eda-sue2Plus): See Download and Setup instruction in the [gitHub page](https://github.com/silicon-vlsi-org/eda-sue2Plus)
- [**XSCHEME**](https://github.com/silicon-vlsi-org/eda-xschem): See Download and Setup instruction in the [gitHub page](https://github.com/silicon-vlsi-org/eda-xschem#downloading-&-setting-up-xschem)
- [**MAGIC**](https://github.com/silicon-vlsi-org/eda-magic): See Download and Setup instruction in the [gitHub page](https://github.com/silicon-vlsi-org/eda-magic#downloading-&-setting-up-magic)
- [**NETGEN**](https://github.com/silicon-vlsi-org/eda-netgen): See Download and Setup instruction in the [gitHub page](https://github.com/silicon-vlsi-org/eda-netgen#downloading-&-setting-up-netgen)
- [**TECHNOLOGY**](https://github.com/silicon-vlsi-org/eda-technology): See Download and Setup instruction in the [gitHub page](https://github.com/silicon-vlsi-org/eda-technology)

## CLOUD-BASED MULTI-USER OPEN-SOURCE LAB (CMOSLab)

The following instuction illustrates the steps to setup open-source EDA tools (ngspice, sue2, magic, netgen, etc.) on a Linux Virtual Machine in cloud. The steps illustrate **Ubuntu 18.04** with **LXDE display manager** on a [**Digital Ocean**](https://digitalocean.com) Droplet (VM).

- Create a Virtual Machine (VM) or Droplet in Digital Ocean with password option for `root`.
- Login to the instance as root using PuTTy (or anything equivalent).
- Update the distro: `#apt update; apt upgrade`
- create a user for admin purpose: `#adduser <user>`
- Give the user `sudo` power: `#usermod -a -G sudo <user>`
- Install the **LXDE** Display Manager and it's depndencies+goodies: `#apt install lxde`
  - This will create all the necessary configs and session in `/etc/X11` eg. `Xsession` etc.
- Install the **tightvncserver**: `#apt install tightvncserver`
- Install `wish` for `Sue2`:`apt install wish`
- Test vncserver by loging in as the user and set the vnc password: `$vncpasswd`
  - This will create `$HOME/.vnc` with a `xstartup` file with the LXDE Xsession.
- Start the vncserver: `$vncserver -geometry 1280x720 :1`
  - This will start the vncserver on port `5901` (5900+1)
- In order to automate the server launch on startup, followed the instruction from [here](https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-vnc-on-ubuntu-18-04)
  - Create a service file for each user (see above doc): ```$ sudo vim /etc/systemd/system/vncserver-user1@.service```
  - Make system aware of the file: ```$ sudo systemctl daemon-reload```
  - Enable the unit file (@1 is the display number): ```$ sudo systemctl enable vncserver@1.service```
  - Start the service: ```$ sudo systemctl start vncserver@1```
  - You can see the status: ```$ sudo systemctl status vncserver@1```
  - Now it should start automatically on reboot.
  - Follow above steps for wach user
- Use vnc client eg. `tightvnc` to connect to the instance using the <IP addre>:1 and the passwd set by `vncpasswd`.
- Now clone all the EDA tools from github in the root location eg. `/cad` and tech/pdk in `/tech`
- Add all the env variables in `/etc/skel/.bashrc`
- Create all users and setup each users. For creating batch users, see [this article](https://www.tecmint.com/create-multiple-user-accounts-in-linux/)
- **CREATE SNAPSHOT** to use it to replicate VMs

**ALTERNATIVES**
- **Xfce4** is another alternative to **LXDE** but sue2 schematics are blank in xfce4 so we decided on LXDE.
  - Install packages `xfce4 xfce4-goodies` and some optional packages depending on your need eg. `xorg dbus-x11 x11-xserver-utils`
- [noVNC](https://novnc.com) is an alternative VNC client that connect through a webbrowser without having to download a client app.
  - git clone the [repo](https://github.com/novnc/noVNC)
  - Use the `novnc_proxy` script to automatically download and start websockify: `./utils/novnc_proxy --vnc localhost:5901`
  - Make sure the vncserver is running and the above script should output an URL that you can navigate to connect to the instance. Use the vnc password to authenticate. Rememeber to substitute the hostname with the Public IP.
- If you want to connect using the Windows RDP protocol (For some reason was very slow for us):
  
```bash
sudo apt install xrdp;
sudo systemctl status xrdp;
sudo adduser xrdp ssl-cert;
sudo systemctl restart xrdp;

sudo ufw allow from 192.168.1.0/24 to any port 3389;
sudo ufw allow 3389
```

**DEVELOPER READY**
  
- Following packages were required to compile source codes for all the EDA tools:
  - ```sudo apt install build-essential linux-headers-‘uname -r‘```
  - ```sudo apt install bison flex libx11-dev libxaw7-dev libtool tcl-dev tk-dev tcsh tclsh tcllib wish libreadline-dev libncurces-dev```
  - ```sudo apt install automake autoconf texinfo``` (required for ngspice git repo )
- Additional packages needed for various other tools:
  - ```libffi-dev git graphviz xdot pkg-config python3 libglu1-mesa-dev freeglut3-dev```
  - ```clang-6.0 gsl-bin libgsl0-dev m4 swig tcllib```
  
- **RESOURCES**
  - [Tutorial: Installing and configure VNC on Ubuntu 18-04](https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-vnc-on-ubuntu-18-04)
  - [How to Create Multiple User Accounts in Linux](https://www.tecmint.com/create-multiple-user-accounts-in-linux/)
  - [Headless X Session with x11vnc](https://jasonmurray.org/posts/2021/x11vnc/)
  

















