
Complete the below items before proceeding <br>
--------------------------------------------------------------------------------------------------- <br>
https://github.com/parvezhussain/kickstart/blob/master/Getting%20The%20Laptop%20Ready.md <br>
https://github.com/parvezhussain/kickstart/blob/master/VirtualBox%20-%20Provision%20Your%20VMs.md <br>
--------------------------------------------------------------------------------------------------- <br>

## Setting up the Network

The most important topic is to understand the Guest VM Networking. There are many different ways to configure the VMs network.

My requirement is:

- I should be able to ssh to the VMs from my laptop.
- The VMs should be able to access internet.

For my requirement I will choose:
- Adapter 1 -> Host-only Adapter
- Adapter 2 -> NAT

------------------------------------------------------------- <br>
Read this article to understand Networking on Oracle Virtualbox <br>
https://technology.amis.nl/2014/01/27/a-short-guide-to-networking-in-virtual-box-with-oracle-linux-inside/ <br>

How to make virtualbox guest use its host’s internet connection and still have ssh access to the guest <br>
http://www.mycodingpains.com/how-to-make-virtualbox-guest-use-its-hosts-internet-connection-and-still-have-ssh-access-to-the-guest/ <br>
------------------------------------------------------------- <br>

Select the the guest VM 'pxeserver'. Select 'Settings'<br>
Select 'Network' from navigation panel.<br>
Adaptor 1<br>
'Enable Network Adapter' checked box. Attached to: Host-Only Adapter<br>
Adaptor 2<br>
'Enable Network Adapter' checked box. Attached to: NAT<br>
Click 'OK'<br>

Repeat the same for 'peserver' and 'peclient1

NEXT STEP: Installing OS on VM <br>
https://github.com/parvezhussain/kickstart/blob/master/Install%20OS.md

