
Please complete the following before proceeding <br>
https://github.com/parvezhussain/ReadMe/blob/master/Getting%20The%20Laptop%20Ready.md


## Provision the VMs.

We will be setting up 3 VMs <br>

Example:<br>
pxeserver       <- Kickstart/pxe Server <br>
peserver        <- PuppetMaster Server<br>
peclient1       <- Puppet Client<br>


Open Oracle VirtualBox(VB). Select 'New'<br>
    Name:pxeserver<br>
    Type:Linux<br>
    Version: Redhat (64-bit)<br>
Click 'Next'<br>
    Memory Size: 1024MB<br>
Click 'Next'<br>
Hard Disk. Choose the default.Click 'Create'<br>
Hard Disk Type. Choose default, Click 'Next'<br>
Storage on physical drive. Dynamically allocated. Click 'Next'<br>

File Location: pxeserver<br>
File size: 15GB. Click 'Create'<br>

Repeat the same for 'peserver' and 'peclient1'<br>

NEXT STEP: Setup Network cards on VMs <br>
https://github.com/parvezhussain/ReadMe/blob/master/VirtualBox%20-%20Setup%20Network%20Cards%20on%20VMs.md
