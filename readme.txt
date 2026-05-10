Week 5: VPC Creation and Ec2 Instance Connection

🔹 Step 1: Create the VPC
1.	Go to AWS Management Console → VPC
2.	Click Create VPC
3.	Choose VPC only
4.	Enter:
o	Name: Custom-VPC
o	IPv4 CIDR block: e.g. 10.0.0.0/16
5.	Click Create VPC
 
🔹 Step 2: Create Subnets (Public & Private)
Create subnets in same Availability Zones for high availability.
Example:
•	Public Subnet: 10.0.1.0/24 (AZ-A)
•	Private Subnet: 10.0.2.0/24 (AZ-A)
Steps:
1.	Go to Subnets → Create subnet
2.	Select your VPC
3.	Choose AZ
4.	Enter CIDR
5.	Create subnet


🔹 Step 3: Create an Internet Gateway (IGW)
1.	Go to Internet Gateways
2.	Click Create internet gateway
3.	Name it Custom-IGW
4.	Attach it to your VPC


🔹 Step 4: Create Route Tables
You need separate route tables for public and private subnets.
Public Route Table
1.	Go to Route Tables → Create route table
2.	Select VPC
3.	Add route:
o	Destination: 0.0.0.0/0
o	Target: Internet Gateway
4.	Associate with public subnet
Private Route Table
•	Keep default local route only (no IGW)
•	Associate with private subnet

🔹 Step 5: Enable Auto-Assign Public IP (Public Subnet)
1.	Select Public Subnet
2.	Go to Edit subnet settings
3.	Enable Auto-assign public IPv4 address

🔹 Step 6: Configure Security Groups
1.	Create a Security Group
2.	Add inbound rules:
o	HTTP (80) / HTTPS (443)
o	SSH (22) from trusted IP
3.	Attach to EC2 instances

🔹 Step 7: Launch EC2 Instances
•	Public EC2 → Public subnet
•	Private EC2 → Private subnet
Attach correct security groups.

🔹 Step 8: Test the Setup
•	Public EC2 → Internet access ✔
•	Private EC2 → Internet via NAT ✔
•	Private EC2 → No direct inbound access ✔




WEEK 4 – Amazon S3

Creating an S3 Bucket and Enabling Public Access

Step 1: Create Bucket
Open AWS Console
Go to Amazon S3
Click Create bucket
Enter Unique Bucket Name
Under Block Public Access:
Uncheck “Block all public access”
Check “I acknowledge…”
Click Create bucket

Step 2: Enable ACL and Public Permissions (Bucket Level)

Open the bucket
Go to Permissions tab
Click Edit under Object Ownership
Select ACLs enabled
Acknowledge warning
Scroll to Access Control List (ACL)
Click Edit
Enable:
Read (Public access)
List (Public access)
Save changes
Upload Object and Make It Public

Step 1: Upload File

Go to Objects tab
Click Upload
Add file
Click Upload
Close upload status page

Step 2: Enable Public Access for Object

Click the uploaded object
Go to Permissions
Click Edit
Enable Read (Public access)
Acknowledge warning
Save changes
Click Open to verify file opens in browser
Enable Versioning in S3

Steps:
Open bucket
Go to Properties
Scroll to Bucket Versioning
Click Edit
Select Enable
Save changes

Test Versioning:

Upload file
Modify file locally
Upload again with same file name
Click Show versions
You will see same file name with different Version IDs

Delete Marker Testing

Steps:
Go to Objects
Select file
Click Delete
Confirm delete
Enable Show versions
You will see Delete Marker with Version ID


Cross-Region Replication (CRR)

Step 1: Create Source Bucket (us-east-1)
Change region to US East (N. Virginia)
Create bucket: source-shivani
Enable ACLs enabled
Disable Block Public Access
Enable Versioning
Create bucket

Step 2: Create Destination Bucket (us-west-2)

Change region to US West (Oregon)
Create bucket: destination-shivani
Enable ACLs enabled
Disable Block Public Access
Enable Versioning
Create bucket

Step 3: Create Replication Rule

Open Source bucket
Go to Management tab
Scroll to Replication Rules
Click Create replication rule
Name: src-dest-crr-rule
Status: Enabled
Apply to all objects
Select destination bucket
IAM Role: Choose existing (Lab role)
Save

Verification:

Upload file in source bucket
File appears automatically in destination bucket
Static Website Hosting in S3
Step 1: Create Bucket

Create bucket
Enable ACLs enabled
Disable Block Public Access
Enable Versioning

Step 2: Upload Website Files

Upload index.html
Upload error.html

Step 3: Enable Static Website Hosting

Go to Properties
Scroll to Static Website Hosting
Click Edit
Enable

Hosting type: Host a static website
Index document: index.html
Error document: error.html
Save changes

Step 4: Make Files Public

For both index.html and error.html:
Go to Permissions
Click Edit
Enable Read (Public access)
Save

Step 5: Access Website

Go to Properties
Copy Bucket Website Endpoint
Paste in browser
Website loads successfully


WEEK 3

Step 1: Launch EC2 Instances
1.	Sign in to the AWS Management Console.
2.	Navigate to the EC2 Dashboard.
3.	Click Launch Instance.
4.	Configure instance details:
o	Instance Name: EFS-1
5.	Choose an Amazon Machine Image (AMI) for Linux.
6.	Select Instance Type: t2.micro.
o	Key Pair: Create a new key pair.
7.	Choose Subnet: us-east-1a.
8.	Configure Security Group: Create a new security group (SG) and add NFS along with SSH, allowing access from anywhere. Note the security group name (e.g., launch-wizard-1).
9.	Click Launch Instance.
10.	Repeat the steps above to launch another instance:
o	Instance Name: EFS-2
o	Subnet: us-east-1b
o	Use the same configuration. Note the security group name (e.g., launch-wizard-2).
________________________________________
Step 2: Create an EFS File System
1.	Go to the EFS service in the AWS Management Console.
2.	Click Create File System.
3.	Click Customize.
4.	Specify details:
o	Name: Optional
o	VPC: Default
o	Enable region button
5.	Click Next.
6.	In Network Settings:
o	Delete all default security groups. Keep only the required availability zones: us-east-1a and us-east-1b.
o	Select the newly created security group (NFS) corresponding to the instances.
7.	Click Next.
8.	Skip the File System Policy.
9.	Click Next.
10.	In Review and Create, click Create.
________________________________________
Step 3: Access EC2 Instances via PowerShell
Accessing EFS-1 Instance
1.	Open two separate PowerShell sessions on your local machine.
2.	SSH into the EFS-1 instance in both sessions:
3.	ssh -i [path-to-your-keypair.pem] ec2-user@[instance-public-ip]
(Ensure you are in the folder where the key is downloaded.)
4.	Switch to root user:
5.	sudo su
6.	Create a directory named efs:
7.	mkdir efs
8.	Install Amazon EFS utilities:
9.	yum install -y amazon-efs-utils
10.	List files in the current directory:
11.	ls
12.	(Optional) Verify installation:
13.	rpm -qa | grep amazon-efs-utils
14.	Repeat steps 2–7 for the EFS-2 instance.
________________________________________
Step 4: Mount EFS on EC2 Instances
1.	Go to the EFS service in the AWS Management Console.
2.	Click the target EFS file system.
3.	Click Attach.
4.	Choose Mount via DNS option.
5.	Copy the displayed command (using the EFS mount helper).
6.	Paste and execute the command in both EC2 instance terminals to mount the EFS file system.
________________________________________
Step 5: Verify and Test EFS
1.	Change the directory to efs on both instances:
2.	cd efs
3.	Create a file on one instance:
4.	echo "working" > test.txt
5.	Verify that the file automatically synchronizes and appears on the other instance:
6.	cd efs
7.	ls
8.	cat test.txt
________________________________________
If you want, I can also create a clean, numbered flowchart version of these steps that makes it visually easier to follow in a lab environment. Do you want me to do that?




WEEK – 2

1 a) Creating  and attaching  an EBS volume to an EC2 instance
What We Are Doing (Horizontal Scalling) 

We are adding an extra hard disk to an existing EC2 server.
Think of it like:
Buying a new hard drive and connecting it to your computer.
So we will create an ec2 in some Az(Availability Zone) and create a EBS volume in same AZ , attach that volume to the ec2 .

Step 1 : Create an EC2 instance 

name : EC2-for-Volume
Select ubuntu
Generate key-value pair .pem file 
Launch instance
In the instance tab check it AZ(availability zone)  us-east-1c
________________________________________

Step 2: Create a Volume 
	Go to EC2 service 
	Then in the left sidebar , go to Elastic Block Store , Select Volumes under it 
	Now click on create volume 
	Volume Type → gp3 Size → Example: 10 GiB
	Availability Zone → ⚠ MUST match your EC2 instance AZ (us-east-1c)
	Copy the volume id so that is is easy for you to selct while attaching .. 	(vol-08edb2c5e7b8482cf)
________________________________________

Step 3: Attach the volume create to the Ec2 instance 
	Go back to the ec2 instances 
	Select the instance that you have created earlier 
	Click on Actions > Storage > Attach Volume 
	Select the volume that you have created from the drop down list of Volumes
	Select any one of the device name from the drop down list (/dev/sdr)
	And click on attach volume 
________________________________________

Step 4: verify wheather your ec2 has been attached with new EBS volume or not 
	Open the AWS cloud shell ( you can see a terminal symbol at the bottom left )
	Clcik on actions in the terminal select upload file and then upload the .pem file 	of the ec2 instance .
	Now in the AWS console got the ec2 instance , select you instance click on 	connect , choose SSH client , copy the commands ..chmod , ssh (run both commands 	one after other )
	Once you are connected to you instance , then run the command 
	Lsblk (lsblk stands for List Block devices It is a Linux command used to display 	information about all storage devices attached to your system. )

You will get smtg like this …
ubuntu@ip-172-31-25-57:~$ lsblk
NAME         MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
loop0          7:0    0 27.6M  1 loop /snap/amazon-ssm-agent/11797
loop1          7:1    0   74M  1 loop /snap/core22/2163
loop2          7:2    0 50.9M  1 loop /snap/snapd/25577
nvme0n1      259:0    0    8G  0 disk 
├─nvme0n1p1  259:1    0    7G  0 part /
├─nvme0n1p14 259:2    0    4M  0 part 
├─nvme0n1p15 259:3    0  106M  0 part /boot/efi
└─nvme0n1p16 259:4    0  913M  0 part /boot
nvme1n1      259:5    0   10G  0 disk

at the end the disk space with 10G is the volume that you have attached successfully .
________________________________________________________________________________

b)Procedure: increasing/decreasing  of CPU and RAM
What We Are Doing (Vertical Scalling) 

Step 1 : Open AWS console , go to EC2 instances , 
	Create one new instance 
	Give name , use ubuntu , key value pair download (.pem) , select instance type as 	t2.medium
	Open AWS shell , upload .pem file , connect the ec2 instance using SSH client 
	Run the command lscpu (it will display the number of cpus = 2 ) 
	Now stop the ec2 instance ( to do that selct your instance , click on instance 	state , then click on stop instance )
	Select the instance . click on actions > Instance settings > Change instance type > 	select t2.nano 
	Click on modify changes 
	Come back to your instances .. select you instance , go to instance settings , 	start instance 
	Open AWS shell , upload .pem file , connect the ec2 instance using SSH client 
	Run the command lscpu (it will display the number of cpus = 1 ) 

	Successfully increased / decreased cpu and ram .. 
________________________________________________________________________________

2.Attach and permanently mount an EBS volume to a Linux EC2 instance to ensure data persistence across reboots.

To do task 2 first you should comple the task 1a)

Create ec2 instance and EBS volume in same AZ , attach the volume to instance , verify it with the command lsblk

Right now we just have storage but it is not partitioned or not mounted so now lets do that .

Open aws cloud shell , connect to the ec2 instance using SSH client 
Run the following commands : 


lsblk : 
ubuntu@ip-172-31-25-57:~$ lsblk
NAME     MAJ:MIN  RM  SIZE RO TYPE MOUNTPOINTS
loop0      7:0     0 27.6M  1 loop /snap/amazon-ssm-agent/11797
loop1      7:1     0 50.9M  1 loop /snap/snapd/25577
loop2      7:2     0   74M  1 loop /snap/core22/2163
xvda     202:0     0    8G  0 disk 
├─xvda1  202:1     0    7G  0 part /
├─xvda14 202:14    0    4M  0 part 
├─xvda15 202:15    0  106M  0 part /boot/efi
└─xvda16 259:0     0  913M  0 part /boot
xvdr     202:4352  0   10G  0 disk 
ubuntu@ip-172-31-25-57:~$ 

here xvdr is the vloumw that we attached now we will partition it 

sudo fdisk /dev/xvdr ( you should go like this , n, p, enter , enter , enter, w) 


ubuntu@ip-172-31-25-57:~$ sudo fdisk /dev/xvdr
Welcome to fdisk (util-linux 2.39.3).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.
Device does not contain a recognized partition table.
Created a new DOS (MBR) disklabel with disk identifier 0x1c4a040c.
Command (m for help): n
Partition type
   p   primary (0 primary, 0 extended, 4 free)
   e   extended (container for logical partitions)
Select (default p): p
Partition number (1-4, default 1): 
First sector (2048-20971519, default 2048): 
Last sector, +/-sectors or +/-size{K,M,G,T,P} (2048-20971519, default 20971519): 
Created a new partition 1 of type 'Linux' and of size 10 GiB.
Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.


sudo partprobe

ubuntu@ip-172-31-25-57:~$ sudo partprobe

lsblk ( to check if the partition is done successfully or not ) 

ubuntu@ip-172-31-25-57:~$ lsblk
NAME     MAJ:MIN  RM  SIZE RO TYPE MOUNTPOINTS
loop0      7:0     0 27.6M  1 loop /snap/amazon-ssm-agent/11797
loop1      7:1     0 50.9M  1 loop /snap/snapd/25577
loop2      7:2     0   74M  1 loop /snap/core22/2163
xvda     202:0     0    8G  0 disk 
├─xvda1  202:1     0    7G  0 part /
├─xvda14 202:14    0    4M  0 part 
├─xvda15 202:15    0  106M  0 part /boot/efi
└─xvda16 259:0     0  913M  0 part /boot
xvdr     202:4352  0   10G  0 disk 
└─xvdr1  202:4353  0   10G  0 part 
ubuntu@ip-172-31-25-57: 
(here we can see inside xvdr we have one more called xvdr1)
Now we have to format the partition 
sudo mkfs.xfs /dev/xvdr1
ubuntu@ip-172-31-25-57:~$ sudo mkfs.xfs /dev/xvdr1
meta-data=/dev/xvdr1             isize=512    agcount=4, agsize=655296 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=1, sparse=1, rmapbt=1
         =                       reflink=1    bigtime=1 inobtcount=1 nrext64=0
data     =                       bsize=4096   blocks=2621184, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0, ftype=1
log      =internal log           bsize=4096   blocks=16384, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
ubuntu@ip-172-31-25-57:~$

Create a Mount Directory 
sudo mkdir /mnt/shivani (can give any name )

ubuntu@ip-172-31-25-57:~$ sudo mkdir /mnt/shivani
Mount the Volume : mount /dev/xvdr1 /mnt/shivani
ubuntu@ip-172-31-25-57:~$ sudo mount /dev/xvdr1 /mnt/shivani
Verify Mount : df –h 
ubuntu@ip-172-31-25-57:~$ df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/root       6.8G  1.8G  4.9G  27% /
tmpfs           228M     0  228M   0% /dev/shm
tmpfs            91M  876K   90M   1% /run
tmpfs           5.0M     0  5.0M   0% /run/lock
/dev/xvda16     881M   89M  730M  11% /boot
/dev/xvda15     105M  6.2M   99M   6% /boot/efi
tmpfs            46M   12K   46M   1% /run/user/1000
/dev/xvdr1       10G  228M  9.8G   3% /mnt/shivani
ubuntu@ip-172-31-25-57:~$
(here you can verify that /dev/xvdr1 is mounted with /mnt/shivani)
So now we have mounted manually , but if we do sudo reboot then this mount will be gone .. to avoid that we will auto mount 

Get the UUID of the Volume : sudo blkid /dev/xvdr1

ubuntu@ip-172-31-25-57:~$ sudo blkid /dev/xvdr1
/dev/xvdr1: UUID="057c3415-9ffd-4926-9280-9cbca391456a" BLOCK_SIZE="512" TYPE="xfs" PARTUUID="1c4a040c-01"
ubuntu@ip-172-31-25-57:~$

Edit /etc/fstab : sudo nano /etc/fstab

Add this line at the end:
UUID=1234abcd-56ef-78gh-90ij-klmnopqrstuv   /mnt/shivani   xfs   defaults,nofail   0   0
In place of UUID keep the one you copy pasted 
Followed by : ctr+o , enter , ctrl+x
sudo mount –a
df –h
sudo reboot
connect again 
df –h
after root also you shld still see that /mnt/shivani 

________________________________________________________________________________

3. Create a snapshot of the attached EBS volume and use it to create and attach a new volume to an EC2 instance in another AWS region.
Attaching Volumes across regions of EC2 using the snapshot
To create a snapshot of an EBS volume and attach it to an instance in another region in AWS, you'll need to follow these steps:

Step 1. Create a Snapshot:
   a. Sign in to the AWS Management Console.
   b. Navigate to the EC2 Dashboard.
   c. Click on "Volumes" in the left-hand navigation pane.
   d. Select the EBS volume you want to create a snapshot of.
   e. Click on the "Actions" dropdown menu above the volume list and select "Create Snapshot."
   f. Provide a name and description for the snapshot.
   g. Click on the "Create Snapshot" button to initiate the snapshot creation process.
________________________________________

Step 2. Copy the Snapshot to Another Region:
   a. Once the snapshot is created, go to the "Snapshots" section in the EC2 Dashboard.
   b. Select the snapshot you just created.
   c. Click on the "Actions" dropdown menu above the snapshot list and select "Copy Snapshot."
   d. Choose the destination region where you want to copy the snapshot.
   e. Click on the "Copy Snapshot" button to initiate the copy process. This may take some time depending on the size of the snapshot and the network speed.
________________________________________

Step 3. Monitor the Snapshot Copy Progress:
   a. You can monitor the progress of the snapshot copy by navigating to the "Snapshots" section in the EC2 Dashboard of the source region.
   b. Look for the snapshot you copied and check its status. It will change from "pending" to "completed" once the copy process is finished.

________________________________________


Step 4. Switch to the Destination Region:
a.	Use the region selector in the top-right corner of the AWS Management Console to switch to the destination region where you copied the snapshot.
________________________________________

Step 5. Create a Volume from the Snapshot:
   a. In the EC2 Dashboard of the destination region, go to the "Snapshots" section.
   b. Find the snapshot you copied from the source region.
   c. Click on the snapshot, then click on the "Actions" dropdown menu and select "Create Volume."
   d. Configure the volume settings, such as volume type, size, and availability zone.
   e. Click on the "Create Volume" button to create the volume from the snapshot.
________________________________________

Step 6. Create the ec2 instance & Attach the Volume to an Instance:
   a. Once the volume is created, navigate to the "Volumes" section in the EC2 Dashboard.
   b. Find the newly created volume and select it.
   c. Click on the "Actions" dropdown menu and select "Attach Volume."
   D  the EC2 instance to which you want to attach the volume and specify the device name.
   e. Click on the "Attach" button to attach the volume to the instance.

We have created a snapshot of an EBS volume, copied it to another region, created a volume from the snapshot, and attached it to an instance in the destination region.

________________________________________________________________________________

Week 1

1 . Creation of Amazon EC2 Instances (Linux and Windows) and Establishing Secure Connections Using SSH from Local Machine
Create ec2 instance
Choose ubuntu as AMI (Amazon Machine Image)
Generate .pem key pair file 
In network setting allow ssh traffic , https , http traffic
Then click launch instance 
Go back to instnaces 
Select you instance 
Click on security group >> click on security group >> click on edit inbound rule 
Change SSH source type to Anywhere – Ipv4 
Save rules 
Open cmd , cd downloads , go to instances , click on connect go to SSH client  tab 
Copy the “ ssh -I …….” Command and pate it in cmd 
It will ask for yes/no enter yes 
Once connected verify with command “whoami” it should say ubuntu
________________________________________

2. Creation of Amazon EC2 Instances (Linux and Windows) and Establishing Secure Connections Using SSH, PuTTY (.pem & .ppk)
Create an ec2 instance 
Open puttygen , load the .pem file 
Give some paraphrase like(hello) , confirm the paraphrase 
Click on save private key 
Name the file smtg like week1pemtoppk 
Now open putty , in place of hostname give ubuntu@<ipv4address>
you can get the ipv4 address from the aws console 
On the left of putty go to connection > SSH> Auth > credentials 
There upload the file that you have converted from .pem to .ppk 
Click on open 
A terminal will open and it will ask to enter the parapharase , enter it and boom you are connected to your ec2 instance .. verify with command “whoami” it will say ubuntu

________________________________________

3 . Connecting to EC2 Using AWS CloudShell

Click on aws terminal in aws console 
Under actions click on upload file 
Upload the pem file 
Select you ec2 instance click on connect copy the chmod command paste and run it in the aws cloudshell
Copy the shh command and paste in the cloud shell 
Enter yes when it asks 
You will be connected to the ec2 instance , verify it using command whoami command 
It should say ubuntu

________________________________________

4 . Create Windows EC2 Instance and Connect via RDP

Create ec2 instance 
With AIM as windows 
.pem key-pair 
Launch instance
security group rule RDP .. source MyIP
Save changes 
Select the instance -> click on connect -> go to RDP client tab
Click on get password 
Upload .pem file 
Click on decrypt 
Copy the password 
Download remote desktop 
Open it 
Click on connect 
Username : admin
Password : pate the one you copied 
Click ok
•	Your Windows EC2 instance desktop should appear.
•	You can now use it as a normal Windows machine.




Week 12 : IAM USERS 
Open aws 
Search for IAM > IAM USERS >  create users 
Give a name to user > check the option (provide user access to the AWS management console )
Custom password > user@123 > click on next > check the button Attach policies directly and select AmazonS3FullAcess > then click on create user 
Copy your account id 765899424827
And sign out and then sign in with new IAM user name and password  , open EC2 you will get access denied .. , open s3 it will be opened because you have access for it .
Sign in as root user > open IAM user > open the user that you have created > 
Click on create Access Key > select CLI access > give a name > click on next it will download .csv file 
Then download https://awscli.amazonaws.com/AWSCLIV2.msi
After downloading open cmd 
aws configure
it will ask for access key name , access key , region , output format 

C:\Users\Govardhan>aws configure
AWS Access Key ID [****************NZH4]: AKIA3EUZJYQ52HCXNZH4
AWS Secret Access Key [****************zt+Z]: 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
Default region name [None]: ap-south-1
Default output format [None]: json 

Enter aws ec2 describe-instances 
You should get unautorized access
Then enter 
C:\Users\Govardhan>aws s3 mb s3://shivani-test-12345
make_bucket: shivani-test-12345
C:\Users\Govardhan>aws s3 ls
2026-04-30 18:17:00 shivani-test-12345

User name : s3-user
Password : user@123
Acc no : 765899424827

🚀 WEEK-13: IAM Role → EC2 (STEP-BY-STEP)
________________________________________
🟢 PART 1: Create IAM Role
🔹 Step 1:
1.	Go to AWS Console 
2.	Search → IAM 
3.	Click Roles (left side) 
4.	Click Create role 
________________________________________
🔹 Step 2: Select Trusted Entity
•	Select → AWS Service 
•	Use case → EC2 
•	Click Next 
________________________________________
🔹 Step 3: Add Permissions
•	Search → AmazonS3FullAccess 
•	Tick the checkbox 
•	Click Next 
________________________________________
🔹 Step 4: Name Role
•	Role name → EC2-S3-Role 
•	Click Create role 
✔️ Role created
________________________________________
🟢 PART 2: Launch EC2 Instance
🔹 Step 5:
1.	Go to EC2 
2.	Click Launch Instance 
Fill:
•	Name → role-test-instance 
•	AMI → Amazon Linux 
•	Instance type → t2.micro 
•	Key pair → select/create 
•	Security group: 
o	SSH (22) → My IP 
Click Launch
________________________________________
🟢 PART 3: Attach Role to EC2
🔹 Step 6:
1.	Go to EC2 → Instances 
2.	Select your instance 
3.	Click: 
Actions → Security → Modify IAM role
4.	Select: 
•	EC2-S3-Role 
5.	Click Update IAM role 
✔️ Role attached
________________________________________
🟢 PART 4: Connect to EC2
🔹 Step 7:
•	Click Connect → EC2 Instance Connect 
________________________________________
🟢 PART 5: Verify (VERY IMPORTANT)
________________________________________
🔹 Step 8: Check S3 (should work)
aws s3 ls
✔️ Works (even without aws configure)
________________________________________
🔹 Step 9: Check EC2 (should fail)
aws ec2 describe-instances
❌ Access Denied
________________________________________
🎯 Why this works
👉 You did NOT run:
aws configure
👉 Still S3 works because:
•	IAM Role gives temporary credentials automatically 
________________________________________
🧠 What you proved
Test	Result
S3 access	✅ Allowed
EC2 access	❌ Denied
Access keys used	❌ No
________________________________________
🎯 Final Conclusion (write this)
IAM roles allow EC2 instances to securely access AWS services using temporary credentials without requiring access keys.
 
week 11 :
✅ WEEK 11 – Amazon Lex (Hotel Booking Bot)
________________________________________
🔹 STEP 1: Open Lex
1.	Login to AWS Console 
2.	Search Amazon Lex 
3.	Click Create Bot 
________________________________________
🔹 STEP 2: Create Bot
1.	Select → Create a blank bot 
2.	Bot name → HotelBookingBot 
3.	IAM Role → Create new role 
4.	Click Next (keep defaults) 
5.	Language → English 
6.	Click Done 
________________________________________
🔹 STEP 3: Create Intent
1.	Go to Intents 
2.	Click Create intent 
3.	Name → BookHotel 
________________________________________
🔹 STEP 4: Add Utterances
Add these exactly:
•	I want to book a hotel 
•	Book a room 
•	Reserve hotel 
•	I need a room 
________________________________________
🔹 STEP 5: Add Slot → AGE
1.	Click Add slot 
2.	Slot name → age 
3.	Slot type → AMAZON.Number 
4.	Prompt → What is your age? 
5.	✅ Mark as Required 
________________________________________
🔹 STEP 6: Add Condition (IMPORTANT)
1.	Go to → Slot → Advanced options 
2.	Find → Conditional branching 
3.	Add condition: 
•	Condition → {age} < 18 
•	Response → You are not eligible for hotel booking 
________________________________________
🔹 STEP 7: Add Slot → LOCATION
1.	Slot name → location 
2.	Type → AMAZON.City 
3.	Prompt → Which city do you want? 
4.	Required ✅ 
________________________________________
🔹 STEP 8: Add Slot → CHECK-IN
1.	Slot name → checkin 
2.	Type → AMAZON.Date 
3.	Prompt → What is your check-in date? 
4.	Required ✅ 
5.	Retry prompt →
Please provide valid date (e.g., 2026-03-25) 
________________________________________
🔹 STEP 9: Add Slot → NIGHTS
1.	Slot name → nights 
2.	Type → AMAZON.Number 
3.	Prompt → How many nights will you stay? 
4.	Required ✅ 
________________________________________
🔹 STEP 10: Create Custom Slot (Room Type)
1.	Go to Slot Types 
2.	Click Add slot type → Blank 
3.	Name → RoomType 
4.	Add values: 
o	Single 
o	Double 
o	Suite 
5.	Save 
👉 Go back to Intent → Add slot:
•	Slot name → roomType 
•	Slot type → RoomType 
________________________________________
🔹 STEP 11: Add Response Cards (Buttons)
1.	Go to slot prompts → More options 
2.	Add Card group 
•	Title → Select Room Type 
•	Buttons: 
o	Single 
o	Double 
o	Suite 
________________________________________
🔹 STEP 12: Add Responses
Initial Response:
Welcome to Hotel Booking! What is your name?
Confirmation:
Do you want to confirm booking in {location} for {nights} nights?
________________________________________
🔹 STEP 13: Build & Test
1.	Click Build ⚠️ (VERY IMPORTANT) 
2.	Open Test chatbot panel 
3.	Type: 
Book a hotel
________________________________________
🔹 EXPECTED FLOW (CHECK THIS)
•	Ask age 
•	Ask city 
•	Ask date 
•	Ask nights 
•	Ask room type 
•	Confirm booking

WEEK 10 : 
🔹 STEP 0: Keep Ready
•	File: working-eb-app.war ✅ 
•	No need to unzip / modify 
________________________________________
🔹 STEP 1: Open Elastic Beanstalk
1.	Login AWS 
2.	Search Elastic Beanstalk 
3.	Click Create Application 
________________________________________
🔹 STEP 2: Create Application
1.	Name → MyApp 
2.	Click Create 
________________________________________
🔹 STEP 3: Create Environment
1.	Click Create Environment 
2.	Choose → Web Server Environment 
3.	Click Select 
________________________________________
🔹 STEP 4: Configure Environment (MOST IMPORTANT)
👉 Environment name:
MyApp-env
👉 Platform:
⚠️ SELECT THIS ONLY:
👉 Java (Corretto / Tomcat)
________________________________________
👉 Application Code:
•	Select → Upload your code 
•	Upload → working-eb-app.war 
________________________________________
🔹 STEP 5: Service Access
•	Service Role → LabRole 
•	Instance Profile → LabInstanceProfile 
________________________________________
🔹 STEP 6: Network
•	VPC → Default 
•	Enable Public IP ✅ 
________________________________________
🔹 STEP 7: Instance Setup
•	Instance → t2.micro 
•	Min → 1 
•	Max → 2 
________________________________________
🔹 STEP 8: Create
1.	Click Review 
2.	Click Create 
⏳ Wait 3–5 mins (VERY IMPORTANT — don’t panic)
________________________________________
🔹 STEP 9: Access Your App
•	After status = Green / Ready 
•	Open the provided URL 


WEEK-9: ELB + Auto Scaling (FULL STEP-BY-STEP)
________________________________________
🟢 PART 1: Create 2 EC2 Web Servers
🔹 Step 1: Launch EC2 Instance 1
1.	Go to AWS Console → EC2 
2.	Click Launch Instance 
3.	Name: webserver-1 
4.	AMI: Amazon Linux 2 
5.	Instance type: t2.micro 
6.	Key pair: Create/select key 
7.	Network settings: 
o	✔ Allow SSH (22) → My IP 
o	✔ Allow HTTP (80) → Anywhere (0.0.0.0/0) 
8.	Click Launch 
________________________________________
🔹 Step 2: Connect to Instance 1
•	Click Connect → EC2 Instance Connect 
Run:
sudo yum update -y
sudo yum install httpd -y
sudo systemctl start httpd
sudo systemctl enable httpd
echo "This is Server 1" | sudo tee /var/www/html/index.html
________________________________________
🔹 Step 3: Launch EC2 Instance 2
👉 Repeat same steps
•	Name: webserver-2 
Run:
sudo yum update -y
sudo yum install httpd -y
sudo systemctl start httpd
sudo systemctl enable httpd
echo "This is Server 2" | sudo tee /var/www/html/index.html
________________________________________
🔹 Step 4: Test both servers
•	Copy Public IP 
•	Open in browser 
👉 Should show:
•	Server 1 page 
•	Server 2 page 
________________________________________
🟢 PART 2: Create Load Balancer (ALB)
________________________________________
🔹 Step 5: Create Security Group for LB
1.	EC2 → Security Groups 
2.	Click Create 
3.	Name: lb-sg 
4.	Add rule: 
o	HTTP (80) → Anywhere 
5.	Create 
________________________________________
🔹 Step 6: Create Load Balancer
1.	EC2 → Load Balancers 
2.	Click Create → Application Load Balancer 
Basic:
•	Name: my-alb 
•	Scheme: Internet-facing 
•	IP: IPv4 
________________________________________
🔹 Step 7: Network
•	Select Default VPC 
•	Select at least 2 subnets 
________________________________________
🔹 Step 8: Security Group
•	Select lb-sg 
________________________________________
🔹 Step 9: Target Group
1.	Click Create new target group 
2.	Name: web-servers-tg 
3.	Target type: Instance 
4.	Protocol: HTTP 
5.	Port: 80 
6.	Health check: / 
________________________________________
🔹 Step 10: Register Targets
•	Select: 
o	webserver-1 
o	webserver-2 
•	Click Include → Register 
________________________________________
🔹 Step 11: Create LB
•	Review → Create 
⏳ Wait 2–3 minutes
________________________________________
🔹 Step 12: Test Load Balancer
1.	Copy DNS name 
2.	Paste in browser 
👉 Refresh multiple times:
•	Server 1 
•	Server 2 
✔️ Load balancing working
________________________________________
🟢 PART 3: Auto Scaling Setup
________________________________________
🔹 Step 13: Create AMI
1.	EC2 → Instances 
2.	Select one instance 
3.	Actions → Create Image 
4.	Name: my-webserver-ami 
5.	Create 
⏳ Wait until available
________________________________________
🔹 Step 14: Create Launch Template
1.	EC2 → Launch Templates 
2.	Click Create 
Fill:
•	Name: my-launch-template 
•	AMI: select your AMI 
•	Instance: t2.micro 
•	Key pair: select 
•	Security group: same as EC2 (allow HTTP) 
Click Create
________________________________________
🔹 Step 15: Create Auto Scaling Group
1.	EC2 → Auto Scaling Groups 
2.	Click Create 
Basic:
•	Name: webserver-asg 
•	Select launch template 
________________________________________
🔹 Step 16: Network
•	VPC: Default 
•	Select all subnets 
________________________________________
🔹 Step 17: Attach Load Balancer
•	Choose: 
o	Attach existing LB 
o	Select your target group 
________________________________________
🔹 Step 18: Health Check
•	Type: HTTP 
•	Grace period: 300 sec 
________________________________________
🔹 Step 19: Group Size
•	Desired: 2 
•	Min: 1 
•	Max: 4 
________________________________________
🔹 Step 20: Create ASG
•	Click Create 
________________________________________
🧪 PART 4: Testing
________________________________________
🔹 Test 1: Load Balancer
•	Open DNS 
•	Refresh → different servers 
________________________________________
🔹 Test 2: Auto Scaling
1.	Go to EC2 → Instances 
2.	Terminate one instance 
👉 Result:
•	New instance automatically created 
________________________________________
🔹 Test 3: Health Check
•	Target group → check both are healthy 
________________________________________
🎯 FINAL RESULT (write this)
Successfully implemented Elastic Load Balancer to distribute traffic across EC2 instances and Auto Scaling Group to automatically adjust the number of instances based on demand, ensuring high availability and scalability.



🎯 WEEK-8: What you are building
👉 You are learning event-driven architecture
Instead of:
❌ Manually checking things
You create:
✅ Automatic notifications
✅ Message queues
✅ Background processing
________________________________________
🟢 PART 1: SNS (Email Notification)
🔹 Step 1: Create SNS Topic
1.	AWS Console → Search SNS 
2.	Click Topics → Create topic 
3.	Select: 
o	Type → Standard 
o	Name → MyEmailTopic 
4.	Click Create 
________________________________________
🔹 Step 2: Add Email Subscription
1.	Open topic → Subscriptions 
2.	Click Create subscription 
3.	Select: 
o	Protocol → Email 
o	Endpoint → Your email 
4.	Click Create 
________________________________________
🔹 Step 3: Confirm Email
•	Go to inbox → Click Confirm subscription 
________________________________________
🔹 Step 4: Test SNS
1.	Click Publish message 
2.	Enter: 
o	Subject → Test 
o	Message → Hello SNS 
3.	Publish 
✔️ You receive email
________________________________________
🟢 PART 2: S3 → SNS → Email
________________________________________
🔹 Step 5: Create S3 Bucket
1.	Go to S3 
2.	Click Create bucket 
3.	Name: shivani-upload-123 
4.	Region: same as SNS 
5.	Create 
________________________________________
🔹 Step 6: Connect S3 to SNS
1.	Open bucket → Properties 
2.	Scroll → Event notifications 
3.	Click Create 
4.	Configure: 
o	Name → UploadEvent 
o	Event type → All object create 
o	Destination → SNS 
o	Select your topic 
5.	Save 
________________________________________
🔹 Step 7: Test
•	Upload a file 
✔️ You get email
________________________________________
🟢 PART 3: SQS (Queue System)
________________________________________
🔹 Step 8: Create Queue
1.	Go to SQS 
2.	Click Create queue 
3.	Name → MyQueue 
4.	Type → Standard 
5.	Create 
________________________________________
🔹 Step 9: Send Message
1.	Open queue 
2.	Click Send and receive 
3.	Enter: 
Hello this is SQS test
4.	Send 
________________________________________
🔹 Step 10: Receive Message
•	Click Poll for messages 
✔️ Message appears
________________________________________
🟢 PART 4: S3 → SNS → SQS → Lambda (MAIN PART)
________________________________________
🔹 Step 11: Create SNS Topic
•	Name → MyS3SNSTopic 
________________________________________
🔹 Step 12: Create SQS Queue
•	Name → MyS3Queue 
________________________________________
🔹 Step 13: Connect SNS → SQS
1.	Open SNS topic 
2.	Click Create subscription 
3.	Protocol → SQS 
4.	Select your queue 
________________________________________
🔹 Step 14: Add SQS Policy (IMPORTANT)
Go to SQS → Permissions → Edit
👉 Allow SNS to send messages
(You already have JSON — just replace ARNs)
________________________________________
🔹 Step 15: Add SNS Policy
👉 Allow S3 to publish
________________________________________
🔹 Step 16: Connect S3 → SNS
•	Same as earlier: 
o	Event → Upload 
o	Destination → SNS 
________________________________________
🔹 Step 17: Test Flow
Upload file →
Flow:
S3 → SNS → SQS
________________________________________
🔹 Step 18: Check SQS
•	Poll messages → You’ll see event data 
________________________________________
🟢 PART 5: Lambda (Automatic Processing)
________________________________________
🔹 Step 19: Create Lambda
1.	Go to Lambda 
2.	Click Create 
3.	Name → SQSConsumer 
4.	Runtime → Python 
________________________________________
🔹 Step 20: Add Trigger
•	Add trigger → Select SQS → choose queue 
________________________________________
🔹 Step 21: Add Code
def lambda_handler(event, context):
    for record in event['Records']:
        print("Message:", record['body'])
    return {"statusCode": 200}
Click Deploy
________________________________________
🔹 Step 22: Final Test
Upload file →
✔️ Automatically:
•	S3 → SNS 
•	SNS → SQS 
•	SQS → Lambda 
•	Lambda prints logs 
________________________________________
✅ Policy:
Replace:
•	SNS_TOPIC_ARN 
•	S3_BUCKET_ARN 
•	ACCOUNT_ID 
{
  "Version": "2012-10-17",
  "Id": "S3PublishToSNS",
  "Statement": [
    {
      "Sid": "AllowS3ToPublish",
      "Effect": "Allow",
      "Principal": {
        "Service": "s3.amazonaws.com"
      },
      "Action": "SNS:Publish",
      "Resource": "SNS_TOPIC_ARN",
      "Condition": {
        "ArnLike": {
          "aws:SourceArn": "S3_BUCKET_ARN"
        },
        "StringEquals": {
          "aws:SourceAccount": "ACCOUNT_ID"
        }
      }
    }
  ]
}
________________________________________
🔐 ✅ 2. SQS Policy (Allow SNS → SQS)
👉 Use this when:
SNS should send messages to SQS
________________________________________
📍 Where to paste:
SQS → Queue → Permissions → Edit Policy
✅ Policy:
Replace:
•	SQS_QUEUE_ARN 
•	SNS_TOPIC_ARN 
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowSNSToSendMessage",
      "Effect": "Allow",
      "Principal": {
        "Service": "sns.amazonaws.com"
      },
      "Action": "sqs:SendMessage",
      "Resource": "SQS_QUEUE_ARN",
      "Condition": {
        "ArnEquals": {
          "aws:SourceArn": "SNS_TOPIC_ARN"
        }
      }
    }
  ]
}
________________________________________
🔐 ✅ 3. Lambda Role Policy (Allow Lambda to Read SQS)
👉 Use this when:
Lambda reads messages from SQS
________________________________________
📍 Where to set:
IAM → Role attached to Lambda
✅ Policy:
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "sqs:ReceiveMessage",
        "sqs:DeleteMessage",
        "sqs:GetQueueAttributes"
      ],
      "Resource": "*"
    }
  ]
}


🎯 What WEEK-7 (Lambda + S3 + DynamoDB) is doing
👉 Automatically run code when a file is uploaded
S3 (upload) → Lambda → DynamoDB
✔️ No servers
✔️ Fully automatic
________________________________________
🚀 STEP-BY-STEP (Do exactly this)
________________________________________
🟢 STEP 1: Create S3 Bucket
1.	Go to S3 
2.	Click Create bucket 
3.	Enter: 
o	Name → shivani-lambda-bucket-123 (must be unique) 
o	Region → same for all services 
4.	Enable Versioning 
5.	Click Create 
________________________________________
🟢 STEP 2: Create DynamoDB Table
1.	Go to DynamoDB 
2.	Click Create table 
3.	Enter: 
o	Table name → newtable 
o	Partition key → unique (String) 
4.	Click Create 
________________________________________
🟢 STEP 3: Create Lambda Function
1.	Go to Lambda 
2.	Click Create function 
3.	Select: 
o	Author from scratch 
o	Name → S3ToDynamoFunction 
o	Runtime → Python 3.x 
4.	Permissions: 
o	Create new role (basic Lambda role) 
5.	Click Create 
________________________________________
🟢 STEP 4: Add S3 Trigger
1.	Inside Lambda → Click Add Trigger 
2.	Select: 
o	Source → S3 
o	Bucket → your bucket 
o	Event → All object create events 
3.	Tick acknowledgment 
4.	Click Add 
________________________________________
🟢 STEP 5: Add Lambda Code
Replace code with:
import boto3
from uuid import uuid4

def lambda_handler(event, context):
    dynamodb = boto3.resource('dynamodb')
    table = dynamodb.Table('newtable')

    for record in event['Records']:
        bucket_name = record['s3']['bucket']['name']
        object_key = record['s3']['object']['key']
        size = record['s3']['object'].get('size', -1)
        event_name = record.get('eventName', 'Unknown')
        event_time = record.get('eventTime', 'Unknown')

        table.put_item(
            Item={
                'unique': str(uuid4()),
                'Bucket': bucket_name,
                'Object': object_key,
                'Size': size,
                'Event': event_name,
                'EventTime': event_time
            }
        )

    return {'statusCode': 200}
👉 Click Deploy
________________________________________
🟢 STEP 6: Fix Permissions (VERY IMPORTANT)
If error occurs → add policy
📍 Go to:
IAM → Roles → Select your Lambda role → Add inline policy
✅ Policy:
(Replace REGION + ACCOUNT ID)
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": ["dynamodb:PutItem"],
      "Resource": "arn:aws:dynamodb:REGION:ACCOUNT_ID:table/newtable"
    }
  ]
}
________________________________________
🟢 STEP 7: Test
1.	Go to S3 
2.	Upload any file 
________________________________________
🟢 STEP 8: Verify DynamoDB
1.	Go to DynamoDB → Explore items 
2.	Select newtable 
✔️ You will see:
•	bucket name 
•	file name 
•	size 
•	timestamp 
________________________________________
🟢 STEP 9: Check Logs
1.	Lambda → Monitor 
2.	Click View CloudWatch Logs 
________________________________________



🎯 WEEK-6: What are you building?
👉 A secure AWS network
•	Public server → Bastion (entry point) 
•	Private server → Database (hidden) 
•	NAT Gateway → Internet access for private server 
________________________________________
🧠 Core Idea (VERY IMPORTANT)
Your Laptop → Bastion (Public) → Private Server
                                ↓
                           NAT Gateway → Internet
________________________________________
🟢 PART 1: Create VPC
🔹 Step 1: Create VPC
1.	Go to VPC 
2.	Click Create VPC 
3.	Select VPC only 
4.	Enter: 
o	Name → my-vpc 
o	CIDR → 10.0.0.0/16 
5.	Create 
________________________________________
🟢 PART 2: Create Subnets
🔹 Step 2: Public Subnet
•	Name → public-subnet 
•	CIDR → 10.0.1.0/24 
•	AZ → any 
________________________________________
🔹 Step 3: Private Subnet
•	Name → private-subnet 
•	CIDR → 10.0.2.0/24 
________________________________________
🟢 PART 3: Internet Gateway
🔹 Step 4: Create IGW
1.	VPC → Internet Gateways 
2.	Create → my-igw 
3.	Attach to VPC 
________________________________________
🟢 PART 4: Route Tables
🔹 Step 5: Public Route Table
1.	Create route table → public-rt 
2.	Add route: 
0.0.0.0/0 → Internet Gateway
3.	Associate with public subnet 
________________________________________
🔹 Step 6: Private Route Table
👉 No internet yet (leave default)
________________________________________
🟢 PART 5: Security Groups
🔹 Step 7: Bastion SG
•	SSH (22) → My IP 
________________________________________
🔹 Step 8: DB Server SG
•	SSH (22) → Bastion private IP 
•	MySQL (3306) → 10.0.1.0/24 
👉 No public access ❌
________________________________________
🟢 PART 6: Launch EC2 Instances
🔹 Step 9: Bastion Server
•	Subnet → Public 
•	Enable Auto-assign Public IP 
•	Key → bastion.pem 
________________________________________
🔹 Step 10: DB Server
•	Subnet → Private 
•	NO public IP 
•	Key → dbserver.pem 
________________________________________
🟢 PART 7: Connect
________________________________________
🔹 Step 11: SSH into Bastion
ssh -i bastion.pem ubuntu@<bastion-public-ip>
________________________________________
🔹 Step 12: Copy DB Key
scp -i bastion.pem dbserver.pem ubuntu@<bastion-ip>:/home/ubuntu/
________________________________________
🔹 Step 13: SSH to DB from Bastion
ssh -i dbserver.pem ubuntu@10.0.2.x
✔️ Success → Private server accessed securely
________________________________________
🔴 Problem: No Internet in Private Server
👉 apt update / yum update FAILS
________________________________________
🟢 PART 8: NAT Gateway (Fix Internet)
________________________________________
🔹 Step 14: Allocate Elastic IP
•	VPC → Elastic IP → Allocate 
________________________________________
🔹 Step 15: Create NAT Gateway
1.	Go to NAT Gateway 
2.	Create: 
o	Subnet → Public subnet 
o	Attach Elastic IP 
3.	Create 
________________________________________
🔹 Step 16: Update Private Route Table
1.	Go to Private Route Table 
2.	Add: 
0.0.0.0/0 → NAT Gateway
________________________________________
🧪 Test Again
Inside DB server:
sudo apt update
✔️ Now it works
________________________________________
🎯 Final Result
•	Bastion → accessible from internet 
•	DB → NOT publicly accessible 
•	DB → internet via NAT 
•	Secure architecture 



