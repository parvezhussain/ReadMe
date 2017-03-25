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
----------------------------------------------------------------- <br>

#### ##############Reference article############################

https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-centos-7

#############################################################

You can create a saperate server as DNS server or you can use the existing pxeserver or peserver to be a DNS server also. <br>

ServerName:  mydns.localhost.com <br>
ip address:  192.168.56.100

Install the packages

    yum install bind bind-utils -y

Download the required customized files from git Repo
    
    yum install git -y
    mkdir /opt/git 
    git clone https://(your_github_id)@github.com/parvezhussain/kickstart.git /opt/git

    cd /opt/git/bind

Update named.conf <br>
perl -pi -e 's/192.168.1.1/192.168.56.100/g' named.conf <br>
perl -pi -e 's/192.168.1/192.168.56/g' named.conf

Edit  /etc/named.conf and check

cd /opt/git/bind/named <br>
Update named.conf.local and check the file

cd /opt/git/bind/named/zones <br>
Check the files <br>
Add a few servers on the db files <br>

Copy the files 

    cd /opt/git/bind
    rsync -avxz * /etc

Restart DNS service

    service named restart


#### On the client
Edit /etc/resolv.conf
    
    domain localhost.com
    nameserver 192.168.56.100

test
nslookup (any server)

