
Complete the following before proceeding <br>
----------------------------------------------------------------- <br>
Getting The Laptop Ready <br>
https://github.com/parvezhussain/ReadMe/blob/master/Getting%20The%20Laptop%20Ready.md <br>
Provision Your VMs <br>
https://github.com/parvezhussain/ReadMe/blob/master/VirtualBox%20-%20Provision%20Your%20VMs.md <br> 
Network Setup on VMs VirtualBox <br>
https://github.com/parvezhussain/ReadMe/blob/master/VirtualBox%20-%20Setup%20Network%20Cards%20on%20VMs.md <br>
Install OS - OPTION A: Install from local DVD <br>
https://github.com/parvezhussain/ReadMe/blob/master/Install%20OS.md <br>

----------------------------------------------------------------- <br>

The first step is to identify IP Address for the VM <br>
<br>
On the laptop command prompt, <br>
type 'ipconfig'<br>

You will see a new network created like below. (The IP address will vary)

      Ethernet adapter Ethernet 3:

      Connection-specific DNS Suffix  . :
      Link-local IPv6 Address . . . . . : fe80::b022:e867:40bc:e31c%33
      IPv4 Address. . . . . . . . . . . : 192.168.56.1
      Subnet Mask . . . . . . . . . . . : 255.255.255.0
      Default Gateway . . . . . . . . . :

This is the network used to access the VMs. <br>
So all your VMs network IPs should be 192.168.56.x

For the pxeserver, Let us take IP as <br>
192.168.56.10 <br>

We will now configure the VM network for inbound and outbound traffic  

Inbound traffic i.e ability to ssh to vm, i.e Adapter 1 (Host-only adapter)  i.e ifcfg-eth0 <br>
Outbound traffic i.e from VM to internet access i.e Adaper 2 (NAT) i.e ifcfg-eth1

Login to the VM through console. 

Run 'ifconfig' <br>
You will see only the loopback network only

Edit /etc/sysconfig/network-scripts/ifcfg-eth0

Update/Add these lines:

      ONBOOT=yes
      BOOTPROTO=static
      IPADDR=192.168.56.10
      PEERDNS=no

Edit /etc/sysconfig/network-scripts/ifcfg-eth1

Update/Add these lines

      ONBOOT=yes
      PEERDNS=no

Restart Network Service
      
      service network restart

Run 'ifconfig'

You will see eth0 and eth1 network started.

##### Test Connectivity

From your laptop using putty, ssh to the server using the ipaddress 192.168.56.10. You should be able to login. <br>
Your inbound traffic to the VM is working <br>

After you login to the VM though putty,  ping www.google.com . You should get ping response. <br>
Your outbound traffic is working. <br>

Congratulation!! Your VM is ready. <br>

To Setup a Kickstart server <br>
https://github.com/parvezhussain/ReadMe/blob/master/Setup%20Kickstart%20Server.md <br>
To Setup a DNS Server <br>
https://github.com/parvezhussain/ReadMe/blob/master/Setup%20DNS%20Server.md <br>

Main Page https://github.com/parvezhussain/kickstart <br>





