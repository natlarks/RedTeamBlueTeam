# Red Team, Blue Team

## Overview

This week I simulated both the offensive side and defensive side of security. I set up a Kali Linux VM to attack the Linux Metasploitable 2 VM. I used IPTables as my Firewall and Snort as my IDS. I am using Windows 10 as my host machine and VMWare as my hypervisor.

## Installing The Attack VM - Kali Linux

1. Go to https://www.kali.org/downloads/ and download the Kali Linux ISO (The download will take a while). Remember the path of where the file is located.
2. Once the download is finished, open up VMWare.
3. On the home screen, click on “Create a new virtual machine”
4. Select “Installer disc image file (ISO)” and enter the path where the Kali ISO was saved.
5. The Guest Operating System is Linux and the version is Ubuntu.
6. Name the VM something related to what you are using it for instead of just “Kali”. This will help you disguise it between different VMs. In addition, save it to a path you will remember later.
7. Specify the disk capacity and how the virtual disk is stored. For this example, we are going to use of 60 GB and split the disc into multiple files.
8. Customize any hardware if you like, but we will not need to.
9. Click “Finish” and double-click on VM’s name to launch it!

## Installing The Target VM - Metasploitable 2 (Linux)
1. On your host machine, go to https://information.rapid7.com/metasploitable-download.html
2. Fill out the form to get to the download page (P.S. you do not have to enter your actual info).
3. Click the 'To download Metasploitable, click here'.
4. Once the ZIP is downloaded, extract the files to a folder you will remember.
5. Open up VMWare.
6. On the home screen, click on “Open a virtual machine” and open the ```Metasploitable.vmx``` file from the folder you just extracted.
7. Select "Add this VM to the library" so you will see it when you open up VMWare.
8. The VM should have opened itself. If it did not, double click the name.
9. Login with the default credentials ```msfadmin:msfadmin```

## Attack Prior to Firewall
This version of Metasploitable has a back door that lets you access files on the machine from another computer, without logging in. We can use ```smbclient``` on our attack VM to view these files using ```smbclient -L //x.x.x.x```(Replace the x's with your IP address). When prompted for the password, just press the carriage return.

![](https://media.giphy.com/media/TDMcJOZg6sHqKb149d/giphy.gif)

In ```auth.log``` we can see it in the log: 
>```March 19 01:37:27 metasploitable smbd[5351]: pam_unix(samba:session): session opened for user root by (uid=0)```

## Installing The Firewall - IPTables
IPTables is a chain-based policy firewall. The firewall breaks into three general categories for chains: ```INPUT```, ```FORWARD```, and ```OUTPUT ```. ```INPUT``` controls the rules for incoming connections like SSH. ```FORWARD``` chain is similar to the ```INPUT``` because it covers incoming connections. However, the connections aren't being delivered to machine, but are forwarded to other machines. ```OUTPUT``` covers all outgoing connections.
1. In your target VM, type ```sudo apt-get install iptables``` into the terminal to install IPTables.
2. To view the chains and the settings type ```sudo iptables -L -v```.
3. We are going to turn on the incoming connections so enter: ```sudo iptables --policy INPUT DROP``` and ```sudo iptables --policy FORWARD DROP```. We are using ```DROP``` instead of ```REJECT``` so potential attackers do not get an error letting them know that the firewall blocked them.

## Attack After The Firewall
As you can see, we use the same command as prior to install the firewall, yet we don't have any luck logging in. If we look in the ```auth.log``` file as well, we see nothing was logged.

![](https://media.giphy.com/media/6weYhJmcwLtUGa9Dzz/giphy.gif)

