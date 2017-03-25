# THIS PAGE IS DEPRECIATED <br>

Refer https://github.com/parvezhussain/kickstart/blob/master/Getting%20The%20Laptop%20Ready.md <br>


======================================================================================================



# kickstart
Kickstart and other files

### GETTING THE LAPTOP READY
### WHAT YOU WILL NEED ON YOUR LAPTOP (also called the HOST MACHINE)

1. Oracle virtual box <br>
https://www.virtualbox.org/ <br>

2. Oracle VirtualBbox Extension Pack (required for Network Install)<br>
https://www.virtualbox.org/wiki/Downloads<br>

3. Centos DVD download - CentOS-6.8-x86_64-bin-DVD1.iso <br>
http://isoredirect.centos.org/centos/6.8/isos/x86_64/ <br>

4. Putty<br>
http://www.putty.org/ <br>

5. Winscp <br>
https://winscp.net/eng/download.php <br>

6. Create your account in github <br>
https://github.com/ <br>

Read this article to understand Networking on Oracle Virtualbox<br>
https://technology.amis.nl/2014/01/27/a-short-guide-to-networking-in-virtual-box-with-oracle-linux-inside/

How to make virtualbox guest use its host’s internet connection and still have ssh access to the guest
http://www.mycodingpains.com/how-to-make-virtualbox-guest-use-its-hosts-internet-connection-and-still-have-ssh-access-to-the-guest/

================================================

### Create and Install the Kickstart Server

Make sure all the above software are installed

====== Reference Video ============<br>
Install centos on the guest VM. Select 'Minimal" during installation <br>
https://www.youtube.com/watch?v=z0_d_06jrWE<br>
====== Reference Video ============<br>

### Create a Guest VM

1. Ceate a guest VM as pxeserver <br>
Open Oracle VirtualBox(VB). Select 'New'<br>
Name:pxeserver<br>
Type:Linux<br>
Version: Redhat (64-bit)<br>
Memory Size: 1024MB<br>

Click 'Create'

File Location: pxeserver<br>
File size: 20GB  (depends on usage 8 GB to 30 GB)<br>

Click 'Create'

========================================

========== Reference Article ============== <br>
Open Oracle VirtualBox and setup the network as per the instructions provided below. (IMPORTANT) <br>
How to make virtualbox guest use its host’s internet connection and still have ssh access to the guest<br>
http://www.mycodingpains.com/how-to-make-virtualbox-guest-use-its-hosts-internet-connection-and-still-have-ssh-access-to-the-guest/<br>
========== Reference Article ============== <br>


#### USE Option 1 or option 2
### OPTION 1: HOWTO Configure Guest VM to conect to Internet and Internal network
Select the the guest VM 'pxeserver'. Select 'Settings'<br>
Select 'Network' from navigation panel.<br>
Adaptor 1<br>
'Enable Network Adapter' checked box. Attached to: Host-Only Adapter<br>
Adaptor 2<br>
'Enable Network Adapter' checked box. Attached to: NAT<br>
Click 'OK'

### OPTION 2: HOWTO Configure Guest VM to connect to Internal Network ONLY
Select the the guest VM 'pxeserver'. Select 'Settings'<br>
Select 'Network' from navigation panel.<br>
Adaptor 1<br>
'Enable Network Adapter' checked box. Attached to: Internal Network<br>
Click 'OK'

#### Use OPTION A or Option B
### OPTION A: HOWTO Configure VM to boot/install from local DVD
Select the VM from Navigation Panel. Click 'Settings'
Select 'Storage' from Navigation Panel<br>
(From middle Panel) Select 'Storage Tree' -> Controller: IDE -> Empty<br>
(From right panel) Click on the (disk) and provide the path to CentOS-6.8-x86_64-bin-DVD1.iso that was downloaded<br>
Click OK<br>
Click 'Start' to start the VM.


### OPTION B: HOWTO Configure VM to boot/install from kickstart server
Make sure the Kickstart server is working fine. We will learn how to setup Kickstart server in the later part of this document<br>
Select the VM from Navigation Panel. Click 'Settings'<br>
Select 'System' from Navigation Panel<br>
Under Motherboard, checkbox Network and move it to the top of the list<br>
Select 'Network'. Under 'Adapter1 Setting' expand 'Advanced'. Note the MAC address.<br>
Click OK

Login to Kickstart server.<br>
Edit /etc/dhcp/dhcpd.conf <br>
Add the Lines:<br>

    host PXEClient1 {
         hardware ethernet 08:00:27:C1:B6:01;
         fixed-address 192.168.1.6;
         filename "pxelinux.0";
         option host-name "peserver.localhost.com";
    }

service dhcpd restart

========================================

### Install OS from local DVD
- Create the VM
- From network Configuration Use OPTION 1 or 2
- use OPTION A to boot/install from local DVD

From Navigation Panel, select 'pxeserver' and click 'Start'<br>
Centos will start installation.<br>
Except the below settings, select all default <br>
hostname:    pxeserver.localhost.com<br>
Server type: minimal

The pxeserver vm will automatically reboot.


========================================

### Install OS from Kickstart Server (Network Install)
- Create the VM
- From network Configuration Use OPTION 1 or 2
- use HOWTO Configure VM to boot/install from kickstart server

From Navigation Panel, select 'pxeserver' and click 'Start'<br>
DHCP will try to connect to the kickstart server.<br>
Server installation will start<br>
The pxeserver vm will automatically reboot.

========================================

### POST INSTALL Network Config

Login to the VM through the console

Post Linux Install:

If the VM has eth1 and eth2 <br>
Edit ifcfg-eth1, ifcfg-eth2:<br>
onboot=yes


edit ifcfg-eth0<br>
IPADDR=192.168.1.1<br>
bootproto=static<br>
onboot=yes

service network restart

ifconfig

Verify the network settings: <br>
Note the IP address of eth1 (host-only adapter) ex:192.168.56.101

Now open putty connect to pxeserver using 192.168.56.101<br>
if you are successful, Congratulation!! you are able to connect to the pxe server (inbound). <br>

Now test the outbound connection.<br>
From pxe server, ping www.google.com <br>
You should see response. Congratulation!! your outbound connection is working. <br>

=======================================

### BUILDING KICKSTART SERVER

There are 3 parts
 1. web server
 2. dhcp server
 3. tftp server



SERVERNAME = pxeserver.localhost.com

#### Follow Steps:
- Install OS from local DVD
- POST INSTALL Network Config

    yum -y install rsync httpd dhcp tftp-server syslinux git mlocate elinks bind-utils telnet wget

Download kickstart Repo

    mkdir /opt/git
    ls -l /opt/git
    git clone https://(your_github_id)@github.com/parvezhussain/kickstart.git /opt/git
    ls -l /opt/git

##### 1.Build the Webserver

    mkdir -p /var/www/html/centos/6.8
    cd /var/www/html/centos
    ln -s 6.8 6
    cd /var/www/html/centos/6.8

    wget -r -nH -nc --cut-dirs=4 --no-parent --reject="index.html*" http://mirror.centos.org/centos/6.8/os/x86_64/

Under /var/www/html/centos/6.8, you should see the same files as http://mirror.centos.org/centos/6.8/os/x86_64/

Disable iptables<br>

    service iptables stop
    chkconfig iptables off
    
Set SELinux to permissive Otherwise file browsing through http webserver will not work <br>
Edit the file /etc/selinux/config
        
    SELINUX=permissive
    
Start httpd

    service httpd start  (ignore any error)
    ps -ef | grep httpd

Make sure httpd start on boot

    chkconfig httpd on
    chkconfig | grep httpd


From your laptop browser check http://(IPaddress of pxeserver) <br>
You should see the Apache page. This means the apache is working.

http://(IPaddress of pxeserver)/centos/6<br>
You should see all the files and folders like http://mirror.centos.org/centos/6.8/os/x86_64/<br>
Make sure you are able to browse the FILES ALSO.<br>

CONGRATULATION!! your web file server is working.<br>
This is one part of PXE boot<br>

========================================

### BUILD the PUPPET REPO

### Create puppetlab client yum repo
Download Files For PUPPET Client yum repo---

    mkdir /var/www/html/puppetlabs
    cd /var/www/html/puppetlabs

    CMD='wget -r -nH -nc --cut-dirs=4 --no-parent --reject="index.html*"'

    $CMD https://yum.puppetlabs.com/el/6/products/x86_64/puppet-3.8.7-1.el6.noarch.rpm
    $CMD https://yum.puppetlabs.com/el/6/products/x86_64/facter-1.7.0-1.el6.x86_64.rpm
    $CMD https://yum.puppetlabs.com/el/6/products/x86_64/hiera-1.3.4-1.el6.noarch.rpm

    $CMD https://yum.puppetlabs.com/el/6/products/x86_64/libselinux-ruby-2.0.94-5.8.el6.x86_64.rpm  Present in centos 6.8

    CMD='wget -r -nH -nc --cut-dirs=7 --no-parent --reject="index.html*"'

    $CMD http://mirror.symnds.com/software/puppet/yum/el/6/dependencies/x86_64/ruby-augeas-0.4.1-3.el6.x86_64.rpm
    $CMD http://mirror.symnds.com/software/puppet/yum/el/6/dependencies/x86_64/ruby-shadow-2.2.0-2.el6.x86_64.rpm
    $CMD http://mirror.symnds.com/software/puppet/yum/el/6/dependencies/x86_64/rubygem-json-1.5.5-3.el6.x86_64.rpm

Create the Puppet Client Repo

    yum install createrepo -y
    cd /var/www/html
    createrepo puppetlabs

    ls -l puppetlabs/repodata
    -rw-r--r--. 1 root root  2986 Nov 20 10:08 repomd.xml

========================================

#### Configure PXE Server

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
    perl -pi -e 's/192.168.1.1/<pxeserver ip>/g' *
    cd /var/www/html/ks/6.7
    perl -pi -e 's/192.168.1.1/<pxeserver ip>/g' *
    


 Edit the below file and make changes<br>
 /var/www/html/ks/6.7/puppet.conf_pemaster
 /var/www/html/ks/6.7/puppet.conf
 /var/www/html/ks/6.7/CentOS-Base.repo
 
 
### Configure dhcp server 

    cd /etc/dhcp
    mv dhcpd.conf dhcpd.conf_orig
    cp -p /opt/git/dhcp/dhcpd.conf .

Edit dhcpd.conf and update the IP. <br>

Restart dhcpd service and start on boot

    service dhcpd restart
    chkconfig dhcpd on
    chkconfig | grep dhcpd

This completes your DHCP Server <br>


### Configure tftp server<br>

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
/var/www/html/ks/b67_nopuppet.ks <br>
Update the IP address of the kickstart server and make sure all the paths are correct and accessible.<br>

Start tftp service and start on boot

    service xinetd restart
    chkconfig tftp on

=======================================================


### PUPPET SERVER

SERVERNAME = peserver.localhost.com

#### Follow Steps:
-  Install OS from Kickstart Server (Network Install)
      * Select 'Install 6.8 no puppet'
- POST INSTALL Network Config

#### Configure PUPPET-SERVER Install

yum install http://yum.puppetlabs.com/puppetlabs-release-el-6.noarch.rpm -y

yum install puppet-server -y

vi /etc/puppet/puppet.conf

Add the line:

    # Where SSL certificates are kept.
    # The default value is '$confdir/ssl'.
    ssldir = $vardir/ssl
    alt_dns_names = peserver,peserver.localhost.com

/etc/init.d/iptables stop<br>
chkconfig | grep iptables<br>
chkconfig iptables off<br>
chkconfig | grep iptables<br>

service puppetmaster start

ps -ef | grep puppet<br>

chkconfig | grep puppetmaster<br>
chkconfig puppetmaster on<br>
chkconfig | grep puppetmaster<br>

## Get Puppet manifest from Git

yum install git -y

mkdir /opt/git
git clone https://(your_github_id)@github.com/parvezhussain/puppetrepo.git /opt/git

cd /etc
mv puppet puppet_orig
cp -pr /opt/git/puppet .
Edit puppet.conf
        
       ssldir = $vardir/ssl
       alt_dns_names = peserver,peserver.localhost.com

service puppetmaster restart

Edit /etc/hosts

    192.168.56.11 peserver.localhost.com

=============================================

### PUPPET CLIENT

SERVERNAME = peclient1.localhost.com

# Follow Steps:
-  Install OS from Kickstart Server (Network Install)
      * Select 'Install 6.8'
- POST INSTALL Network Config

Open peclient1 on putty session

ifconfig<br>


### Configure puppet client to connect to puppet master

The kickstart process has already configured most of the steps below. <br>
In case it does not work, verify the below steps. <br>

Edit /etc/hosts and add

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

You should see someoutput if not then puppet node is not configured correctly <br>
(Check /etc/hosts file  and /etc/puppet/puppet.conf file for puppetmaster server)

Login to peserver using putty<br>
puppet cert list<br>

    [root@peserver opt]# puppet cert list
    "peclient1.localhost.com" (SHA256) 05:D2:EB:2D:07:10:61:8F:2A:8C:E4:14:A7:20:17:DD:48:F5:51:FB:08:40:0B:F3:13:4E:C4:F5:55:44:D9:FA
    [root@peserver opt]#


Sign the node agent certificate

    puppet cert sign peclient1.localhost.com

Your puppet node is configured and connected to puppet master. <BR>

================================================= <br>

YOUR ENVIRONMENT IS READY TO LEARN PUPPET OR ANY OTHER TOOLS

######## END###############################




Installation Process:

Ping server and client so that they talk to each other

Date set correctly 

(Error: Could not request certificate: SSL_connect returned=1 errno=0 state=SSLv3 read server certificate B: certificate verify failed: [CRL is not yet valid for /CN=Puppet CA: foreman.company.com])



yum -y install http://yum.theforeman.org/releases/latest/el6/x86_64/foreman-release-1.10.0-1.el6.noarch.rpm
yum -y install epel-release

yum install foreman-installer -y

foreman-installer

INSTALL AND CONFIGURE PUPPET


http://techarena51.com/index.php/a-simple-way-to-install-and-configure-a-puppet-server-on-linux/

 

How to change how often agents "check-in"?

https://ask.puppetlabs.com/question/932/how-to-change-how-often-agents-check-in/

 

LIST ALL NODES

Puppet cert list --all


==========================================

PUPPET EXTRA NOTES
 
1.  yum install openssh-clients
2.  vi /etc/yum.repos.d/CentOS-Base.repo
3.  yum install openssh-clients -y
4.  yum install pciutils -y
5.  yum install rubygems
6.  yum install virt-what
7.  yum install augeas-libs
8.  cd /tmp/puppetlabs/
9.  ls -l
10.  rpm -i ruby-augeas-0.4.1-3.el6.x86_64.rpm
11.  rpm -i rubygem-json-1.5.5-3.el6.x86_64.rpm
12.  rpm -i hiera-1.3.4-1.el6.noarch.rpm
13.  rpm -i facter-1.7.0-1.el6.x86_64.rpm
14.  rpm -i libselinux-ruby-2.0.94-5.8.el6.x86_64.rpm
15.  rpm -i ruby-shadow-2.2.0-2.el6.x86_64.rpm
16.  rpm -i puppet-3.8.5-1.el6.noarch.rpm

[root@PXEClient yum.repos.d]# rpm -i puppet-3.8.5-1.el6.noarch.rpm

warning: puppet-3.8.5-1.el6.noarch.rpm: Header V4 RSA/SHA512 Signature, key ID 4bd6ec30: NOKEY

error: Failed dependencies:

        /usr/bin/ruby is needed by puppet-3.8.5-1.el6.noarch
        facter >= 1:1.7.0 is needed by puppet-3.8.5-1.el6.noarch
        hiera >= 1.0.0 is needed by puppet-3.8.5-1.el6.noarch
        ruby >= 1.8 is needed by puppet-3.8.5-1.el6.noarch
        ruby >= 1.8.7 is needed by puppet-3.8.5-1.el6.noarch
        ruby(selinux) is needed by puppet-3.8.5-1.el6.noarch
        ruby-augeas is needed by puppet-3.8.5-1.el6.noarch
        ruby-shadow is needed by puppet-3.8.5-1.el6.noarch
        rubygem-json is needed by puppet-3.8.5-1.el6.noarch

[root@PXEClient yum.repos.d]# rpm -i facter-1.7.0-1.el6.x86_64.rpm

warning: facter-1.7.0-1.el6.x86_64.rpm: Header V4 RSA/SHA1 Signature, key ID 4bd6ec30: NOKEY
error: Failed dependencies:
        dmidecode is needed by facter-1:1.7.0-1.el6.x86_64
        virt-what is needed by facter-1:1.7.0-1.el6.x86_64

[root@PXEClient yum.repos.d]# rpm -i hiera-1.3.4-1.el6.noarch.rpm
warning: hiera-1.3.4-1.el6.noarch.rpm: Header V4 RSA/SHA512 Signature, key ID 4bd6ec30: NOKEY
error: Failed dependencies:

        rubygem-json is needed by hiera-1.3.4-1.el6.noarch

[root@PXEClient yum.repos.d]#

[root@PXEClient yum.repos.d]# rpm -i rubygem-json-1.5.5-3.el6.x86_64.rpm
warning: rubygem-json-1.5.5-3.el6.x86_64.rpm: Header V4 RSA/SHA512 Signature, key ID 4bd6ec30: NOKEY

error: Failed dependencies:
       rubygems >= 1.3.7 is needed by rubygem-json-1.5.5-3.el6.x86_64

[root@PXEClient yum.repos.d]# rpm -i rubygems-1.3.7-5.el6.noarch.rpm
error: Failed dependencies:

        ruby-rdoc is needed by rubygems-1.3.7-5.el6.noarch

[root@PXEClient yum.repos.d]#

[root@PXEClient yum.repos.d]# rpm -i ruby-rdoc-1.8.7.374-4.el6_6.x86_64.rpm

error: Failed dependencies:

        ruby-irb = 1.8.7.374-4.el6_6 is needed by ruby-rdoc-1.8.7.374-4.el6_6.x86_64

 

[root@PXEClient yum.repos.d]# rpm -i libselinux-ruby-2.0.94-7.el6.x86_64.rpm

error: Failed dependencies:

        libselinux = 2.0.94-7.el6 is needed by libselinux-ruby-2.0.94-7.el6.x86_64

[root@PXEClient yum.repos.d]#

 

[root@PXEClient yum.repos.d]# rpm -i ruby-augeas-0.4.1-3.el6.x86_64.rpm

warning: ruby-augeas-0.4.1-3.el6.x86_64.rpm: Header V4 RSA/SHA512 Signature, key ID 4bd6ec30: NOKEY

error: Failed dependencies:

        augeas-libs >= 0.8.0 is needed by ruby-augeas-0.4.1-3.el6.x86_64

        libaugeas.so.0()(64bit) is needed by ruby-augeas-0.4.1-3.el6.x86_64

        libaugeas.so.0(AUGEAS_0.1.0)(64bit) is needed by ruby-augeas-0.4.1-3.el6.x86_64

        libaugeas.so.0(AUGEAS_0.10.0)(64bit) is needed by ruby-augeas-0.4.1-3.el6.x86_64

        libaugeas.so.0(AUGEAS_0.11.0)(64bit) is needed by ruby-augeas-0.4.1-3.el6.x86_64

        libaugeas.so.0(AUGEAS_0.12.0)(64bit) is needed by ruby-augeas-0.4.1-3.el6.x86_64

        libaugeas.so.0(AUGEAS_0.8.0)(64bit) is needed by ruby-augeas-0.4.1-3.el6.x86_64

[root@PXEClient yum.repos.d]# yum install augeas-libs

Loaded plugins: fastestmirror

Setting up Install Process

Loading mirror speeds from cached hostfile

Resolving Dependencies

--> Running transaction check

---> Package augeas-libs.x86_64 0:1.0.0-10.el6 will be installed

--> Finished Dependency Resolution

 

Dependencies Resolved

 

==================================================================================================================

 Package                      Arch                    Version                         Repository             Size

==================================================================================================================

Installing:

 augeas-libs                  x86_64                  1.0.0-10.el6                    base                  314 k

 

Transaction Summary

==================================================================================================================

Install       1 Package(s)

 

Total download size: 314 k

Installed size: 949 k

Is this ok [y/N]:

 

 

 

yum install openssh-clients

yum install pciutils

 

yum install virt-what   yum install dmidecode

yum install augeas-libs

yum install rubygems [yum install ruby yum install compat-readline5 yum install ruby-libs]

 

 

 

rpm -i ruby-augeas-0.4.1-3.el6.x86_64.rpm

rpm -i rubygem-json-1.5.5-3.el6.x86_64.rpm

rpm -i hiera-1.3.4-1.el6.noarch.rpm

 

rpm -i facter-1.7.0-1.el6.x86_64.rpm

rpm -i libselinux-ruby-2.0.94-5.8.el6.x86_64.rpm

rpm -i ruby-shadow-2.2.0-2.el6.x86_64.rpm

 
How to Create a PXE Installation Image for Oracle VM
https://docs.oracle.com/cd/E20815_01/html/E20821/gkang.html

http://www.golinuxhub.com/2014/08/how-to-configure-pxe-boot-server-in.html

   17  puppet

   18  history



