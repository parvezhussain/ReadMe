### PUPPET SERVER

SERVERNAME:    peserver.localhost.com <br>
IP address:    192.168.56.11


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
In the installation menu select "Install 6.8" <br>
https://github.com/parvezhussain/ReadMe/blob/master/Install%20OS.md <br>

Post Install Network Configuration (Optional - because kickstart file has automated this section) <br>
https://github.com/parvezhussain/ReadMe/blob/master/Configuring%20Network.md <br>


#### Configure PUPPET-SERVER Install

     yum install http://yum.puppetlabs.com/puppetlabs-release-el-6.noarch.rpm -y
     yum install puppet-server -y

Edit the file /etc/puppet/puppet.conf

Add the line:

    # Where SSL certificates are kept.
    # The default value is '$confdir/ssl'.
    ssldir = $vardir/ssl
    alt_dns_names = peserver,peserver.localhost.com

Start PuppetMaser service

     service puppetmaster start
     ps -ef | grep puppet

Start on boot

     chkconfig | grep puppetmaster
     chkconfig puppetmaster on
     chkconfig | grep puppetmaster

## Get Puppet manifest from Git

     yum install git -y
     mkdir /opt/git
     git clone https://(your_github_id)@github.com/parvezhussain/puppetrepo.git /opt/git

     cd /etc
     mv puppet puppet_orig
     cp -pr /opt/git/puppet .

Edit /etc/puppet/puppet.conf
        
       ssldir = $vardir/ssl
       alt_dns_names = peserver,peserver.localhost.com

Restart puppetmaser service

     service puppetmaster restart

Your Puppet Server is Ready. <br>

------------------------------------------------------------------------------------------------------- <br>
NEXT STEP : Setup and Configure Puppet Client <br>
https://github.com/parvezhussain/ReadMe/blob/master/Setup%20and%20Configure%20Puppet%20Client.md
<br>
