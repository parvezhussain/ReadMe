Complete the following before proceeding <br>
----------------------------------------------------------------- <br>
Getting The Laptop Ready <br>
https://github.com/parvezhussain/kickstart/blob/master/Getting%20The%20Laptop%20Ready.md <br>
Provision Your VMs <br>
https://github.com/parvezhussain/kickstart/blob/master/VirtualBox%20-%20Provision%20Your%20VMs.md <br> 
Network Setup on VMs VirtualBox <br>
https://github.com/parvezhussain/kickstart/blob/master/VirtualBox%20-%20Setup%20Network%20Cards%20on%20VMs.md <br>
----------------------------------------------------------------- <br>

OS: CentOS 6.8

We will use 2 different ways to install OS.
- From Local DVD              (OPTION A)
- From Kickstart Server       (OPTION B)

## OPTION A: Install OS on VM from local DVD

Example: pxeserver.localhost.com <br>

Select the VM from Navigation Panel. Click 'Settings' Select 'Storage' from Navigation Panel <br>
(From middle Panel) Select 'Storage Tree' -> Controller: IDE -> Empty <br>
(From right panel) Click on the (disk) and provide the path to CentOS-6.8-x86_64-bin-DVD1.iso that was downloaded <br>
Click OK <br>
Click 'Start' to start the VM. <br>


From Navigation Panel, select 'pxeserver' and click 'Start' <br>
Centos will start installation. <br>
Except the below settings, select all default <br>
- TimeZone (your local time)
- hostname: pxeserver.localhost.com
- Server type: minimal

The pxeserver vm will automatically reboot.

POST INSTALL <br>

Configure the Network <br>
https://github.com/parvezhussain/kickstart/blob/master/Configuring%20Network.md


## OPTION B: Install OS on VM from kickstart server <br>

Make sure the Kickstart server is setup. <br>
https://github.com/parvezhussain/kickstart/blob/master/Setup%20Kickstart%20Server.md <br> <br>

#### On the VM
Open VirtualBox <br>
Select the VM from Navigation Panel. Click 'Settings' <br>
Select 'System' from Navigation Panel <br>
Under Motherboard -> Boot Order, checkbox Network and move it to the TOP of the list <br>
Select 'Network'. Under 'Adapter1 Setting' expand 'Advanced'. Note the MAC address. <br>
This MAC address will be used on DHCP server for kickstart <br>
Click OK <br>


#### On the Kickstart Server
Login to Kickstart server.
Edit /etc/dhcp/dhcpd.conf
Add the Lines:

host PXEClient1 {
     hardware ethernet 08:00:27:C1:B6:01;
     fixed-address 192.168.1.6;
     filename "pxelinux.0";
     option host-name "peserver.localhost.com";
}

Update the MAC Address (noted from above) <br>
Update the IP address

service dhcpd restart

#### On the VM (again)

On the VirtualBox, Start the VM <br>
Open VirtualBox, select the VM and click 'Start'

======================================== <br>

## Install OS from local DVD

Provision the VM <br>
https://github.com/parvezhussain/kickstart/blob/master/Provision%20Your%20VMs.md

Complete the Network Configuration. <br> 
Adapter 1 - Host-only Adapter <br>
Adapter 2 - NAT

    Create the VM
    From network Configuration Use OPTION 1 or 2
    use OPTION A to boot/install from local DVD

From Navigation Panel, select 'pxeserver' and click 'Start'
Centos will start installation.
Except the below settings, select all default
hostname: pxeserver.localhost.com
Server type: minimal

The pxeserver vm will automatically reboot.

========================================
Install OS from Kickstart Server (Network Install)

    Create the VM
    From network Configuration Use OPTION 1 or 2
    use HOWTO Configure VM to boot/install from kickstart server

From Navigation Panel, select 'pxeserver' and click 'Start'
DHCP will try to connect to the kickstart server.
Server installation will start
The pxeserver vm will automatically reboot.

========================================
