
### PUPPET CLIENT



SERVERNAME:    peclient.localhost.com <br>
IP Address:    192.168.56.20 <br> 


Complete the following before proceeding <br>
----------------------------------------------------------------- <br>
Getting The Laptop Ready <br>
https://github.com/parvezhussain/ReadMe/blob/master/Getting%20The%20Laptop%20Ready.md <br>
Provision Your VMs <br>
https://github.com/parvezhussain/ReadMe/blob/master/VirtualBox%20-%20Provision%20Your%20VMs.md <br> 
Network Setup on VMs VirtualBox <br>
https://github.com/parvezhussain/ReadMe/blob/master/VirtualBox%20-%20Setup%20Network%20Cards%20on%20VMs.md <br>

Kickstart Server is Working <br>
https://github.com/parvezhussain/ReadMe/blob/master/Setup%20Kickstart%20Server.md

DNS Server is Working <br>
https://github.com/parvezhussain/ReadMe/blob/master/Setup%20DNS%20Server.md <br>

----------------------------------------------------------------- <br>

Install the OS using Kickstart server <br>
- OPTION B: Install OS on VM from kickstart server  <br>

In the installation menu select "Install 6.8 with PuppetClient" <br>

https://github.com/parvezhussain/ReadMe/blob/master/Install%20OS.md <br>

Post Install Network Configuration (Optional - because kickstart file has automated this section) <br>
https://github.com/parvezhussain/ReadMe/blob/master/Configuring%20Network.md <br>

Add the puppet client to DNS server <br>

### Configure puppet client to connect to puppet master


    192.168.56.20  peclient1.localhost.com
    192.168.56.11 peserver.localhost.com
    192.168.56.10 pxeserver.localhost.com

Test the connectivity (important for puppet to work)

    ping peserver.localhost.com
    ping www.google.com
    ping pxeserver.localhost.com

Open crontab and add the line 
             
      * * * * * /usr/bin/puppet agent -tv >> /tmp/puppet.out

Run 'puppet agent -tv'

You should see some output if not then puppet node is not configured correctly <br>

From Puppet client: ping peserver.localhost.com
From puppetmaster: ping peclient1.localhost.com


Login to peserver using putty<br>
puppet cert list<br>

    [root@peserver opt]# puppet cert list
    "peclient1.localhost.com" (SHA256) 05:D2:EB:2D:07:10:61:8F:2A:8C:E4:14:A7:20:17:DD:48:F5:51:FB:08:40:0B:F3:13:4E:C4:F5:55:44:D9:FA
    [root@peserver opt]#

This shows that the puppet client is able to talk to puppet master

Sign the node agent certificate

    puppet cert sign peclient1.localhost.com

Your puppet node is configured and connected to puppet master. <BR>

================================================= <br>
Login to puppet client and run 'puppet agent -tv' <br>

You should see output with good results. <br>

YOUR ENVIRONMENT IS READY TO LEARN PUPPET OR ANY OTHER TOOLS

######## END###############################

