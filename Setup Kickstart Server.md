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
Post Install Network Configuration <br>
https://github.com/parvezhussain/ReadMe/blob/master/Configuring%20Network.md <br>

Configure DNS server (if you want your kickstart server to be a DNS server also) <br>
https://github.com/parvezhussain/ReadMe/blob/master/Setup%20DNS%20Server.md

----------------------------------------------------------------- <br>

========== Refrence Article =============== <br>
https://wiki.centos.org/HowTos/NetworkInstallServer <br>
========== Refrence Article =============== <br>


### BUILDING KICKSTART SERVER

There are 3 parts
 1. web server
 2. dhcp server
 3. tftp server



SERVERNAME = pxeserver.localhost.com <br>
IP Address: 192.168.56.10

Install the required Packages

     yum -y install rsync httpd dhcp tftp-server syslinux git mlocate elinks bind-utils telnet wget

Download kickstart Repo

    mkdir /opt/git
    ls -l /opt/git
    git clone https://(your_github_id)@github.com/parvezhussain/kickstart.git /opt/git
    ls -l /opt/git

##### 1. Build the Webserver

Centos 6

    mkdir -p /var/www/html/centos/6.8
    cd /var/www/html/centos
    ln -s 6.8 6
    cd /var/www/html/centos/6.8

Centos 7

    mkdir -p /var/www/html/centos/7.2
    cd /var/www/html/centos
    ln -s 7.2 7
    cd /var/www/html/centos/7.2


Download the full CentOS packages

    wget -r -nH -nc --cut-dirs=4 --no-parent --reject="index.html*" http://mirror.centos.org/centos/6/os/x86_64/
    wget -r -nH -nc --cut-dirs=4 --no-parent --reject="index.html*" http://mirror.centos.org/centos/7/os/x86_64/


Under /var/www/html/centos/6.8, you should see the same files as http://mirror.centos.org/centos/6/os/x86_64/

Disable iptables and make the changes parmanent<br>

Centos 6

    service iptables stop
    chkconfig iptables off

Centos 7

    systemctl stop firewalld
    systemctl status firewalld
    systemctl disable firewalld
    systemctl is-enabled firewalld

Set SELinux to permissive Otherwise file browsing through http webserver will not work <br>
Edit the file /etc/selinux/config
        
    SELINUX=permissive
    
Start httpd
Centos 6

    service httpd start  (ignore any error)
    ps -ef | grep httpd

Centos 7

    systemctl start httpd.service  (ignore any error)
    ps -ef | grep httpd



Make sure httpd start on boot

Centos 6

    chkconfig httpd on
    chkconfig | grep httpd

Centos 7

    systemctl enable httpd
    systemctl is-enabled httpd

From your laptop browser check http://(IPaddress of pxeserver) <br>
You should see the Apache page. This means the apache is working.

http://(IPaddress of pxeserver)/centos/6<br>
You should see all the files and folders like http://mirror.centos.org/centos/6.8/os/x86_64/<br>
Make sure you are able to browse the FILES ALSO.<br>

CONGRATULATION!! your web file server is working.<br>
This is one part of PXE boot<br>

========================================

we want to install puppet client using the kickstart server <br>

#### 2.  BUILD the PUPPET REPO

### Create puppetlab client yum repo
Download Files For PUPPET Client yum repo---

    mkdir /var/www/html/puppetlabs
    cd /var/www/html/puppetlabs

Centos 7

    CMD='wget -r -nH -nc --cut-dirs=4 --no-parent --reject="index.html*"'

    $CMD https://yum.puppetlabs.com/el/7/products/x86_64/puppet-3.8.7-1.el7.noarch.rpm
    $CMD https://yum.puppetlabs.com/el/7/products/x86_64/facter-2.4.6-1.el7.x86_64.rpm
    $CMD https://yum.puppetlabs.com/el/7/products/x86_64/hiera-1.3.4-1.el7.noarch.rpm
    $CMD https://yum.puppetlabs.com/el/7/dependencies/x86_64/ruby-augeas-0.4.1-3.el7.x86_64.rpm
    $CMD https://yum.puppetlabs.com/el/7/dependencies/x86_64/ruby-shadow-2.2.0-2.el7.x86_64.rpm

    $CMD https://yum.puppetlabs.com/el/7/products/x86_64/libselinux-ruby-2.0.94-5.8.el7.x86_64.rpm  Present in centos 7 repo

    CMD='wget -r -nH -nc --cut-dirs=7 --no-parent --reject="index.html*"'

    $CMD http://mirror.symnds.com/software/puppet/yum/el/7/dependencies/x86_64/rubygem-json-1.7.7-30.el7.x86_64.rpm Present in centos 7 repo

Centos 6

    CMD='wget -r -nH -nc --cut-dirs=4 --no-parent --reject="index.html*"'

    $CMD https://yum.puppetlabs.com/el/6/products/x86_64/puppet-3.8.7-1.el6.noarch.rpm
    $CMD https://yum.puppetlabs.com/el/6/products/x86_64/facter-1.7.0-1.el6.x86_64.rpm
    $CMD https://yum.puppetlabs.com/el/6/products/x86_64/hiera-1.3.4-1.el6.noarch.rpm
    $CMD https://yum.puppetlabs.com/el/6/dependencies/x86_64/ruby-augeas-0.4.1-3.el6.x86_64.rpm
    $CMD https://yum.puppetlabs.com/el/6/dependencies/x86_64/ruby-shadow-2.2.0-2.el6.x86_64.rpm
    $CMD https://yum.puppetlabs.com/el/6/dependencies/x86_64/rubygem-json-1.5.5-3.el6.x86_64.rpm


    $CMD https://yum.puppetlabs.com/el/6/products/x86_64/libselinux-ruby-2.0.94-5.8.el6.x86_64.rpm  Present in centos 6.8





Create the Puppet Client Repo

    yum install createrepo -y
    cd /var/www/html
    createrepo puppetlabs

    ls -l puppetlabs/repodata
    -rw-r--r--. 1 root root  2986 Nov 20 10:08 repomd.xml

Congratulation!! You just learned how to create a yum repo <br>

========================================

#### 3. Configure PXE Server

========== Refrence Article =============== <br>
https://wiki.centos.org/HowTos/NetworkInstallServer <br>
========== Refrence Article =============== <br>

### Copy kickstart files and config

    cd /var/www/html
    cp -pr /opt/git/ks .

Find and replace 192.168.1.1 with the kiskstart server Ipaddress on all the files
    
    [root@pxeserver html]# ls -l
    total 12
    drwxr-xr-x. 3 root root 4096 Nov 20 07:36 centos
    drwxr-xr-x. 3 root root 4096 Nov 20 07:34 ks
    drwxr-xr-x. 2 root root 4096 Nov 20 07:48 puppetlabs
    
    cd /var/www/html/ks
    perl -pi -e 's/192.168.1.1/192.168.56.10/g' *
    cd /var/www/html/ks/6.7
    perl -pi -e 's/192.168.1.1/192.168.56.10/g' *
    


 Edit the below file and make changes<br>
 /var/www/html/ks/6.7/puppet.conf_pemaster
 /var/www/html/ks/6.7/puppet.conf
 /var/www/html/ks/6.7/CentOS-Base.repo
 
 
#### 4. Configure dhcp server 

    cd /etc/dhcp
    mv dhcpd.conf dhcpd.conf_orig
    cp -p /opt/git/dhcp/dhcpd.conf .

Edit dhcpd.conf and update the IP. <br>

Restart dhcpd service and start on boot

    service dhcpd restart
    chkconfig dhcpd on
    chkconfig | grep dhcpd

This completes your DHCP Server <br>


#### 5. Configure tftp server<br>

    cd /var/lib/tftpboot
    cp /usr/share/syslinux/pxelinux.0 .
    cp /usr/share/syslinux/menu.c32 .
    mkdir -p pxelinux.cfg centos/6.8
    cp -p /opt/git/tftpboot/pxelinux.cfg/default /var/lib/tftpboot/pxelinux.cfg/
    cp -p /var/www/html/centos/6/images/pxeboot/* /var/lib/tftpboot/centos/6.8/

    ls -l /var/lib/tftpboot/centos/6.8/
    total 43904
    -rw-r--r--. 1 root root 40688737 May 22 01:06 initrd.img
    -rw-r--r--. 1 root root  4264528 May 22 01:06 vmlinuz

Edit <br>
/var/lib/tftpboot/pxelinux.cfg/default <br>
/var/www/html/ks/b67.ks <br>
/var/www/html/ks/b67_with_puppetclient.ks <br>
Update the IP address of the kickstart server and make sure all the paths are correct and accessible.<br>

Start tftp service and start on boot

    service xinetd restart
    chkconfig tftp on


Your Kickstart Server is ready. <br>

=======================================================

Next Kickstart a VM. <br>
Rrefer to the below link under section <br>
OPTION B: Install OS on VM Using kickstart server <br>

https://github.com/parvezhussain/ReadMe/blob/master/Install%20OS.md



