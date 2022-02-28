#WEB SOLUTION WITH WORDPRESS

In this project you will be tasked to prepare storage infrastructure on two Linux servers and implement a basic web solution using WordPress. 
WordPress is a free and open-source content management system written in PHP and paired with MySQL or MariaDB as its backend Relational Database Management System (RDBMS).

Project 6 consists of two parts:

-Configure storage subsystem for Web and Database servers based on Linux OS. The focus of this part is to give you practical experience of working with disks, partitions and volumes in Linux.

-Install WordPress and connect it to a remote MySQL database server. This part of the project will solidify your skills of deploying Web and DB tiers of Web solution.

#Three-tier Architecture

Generally, web, or mobile solutions are implemented based on what is called the Three-tier Architecture.

Three-tier Architecture is a client-server software architecture pattern that comprise of 3 separate layers.

![Screenshot 2022-02-28 012712](https://user-images.githubusercontent.com/97651517/155934942-cb75d9f5-561b-44c6-b47b-fbfe627b5116.png)

#STEP 1

—Prepare a Web Server

Launch an EC2 instance that will serve as "Web Server". Create 3 volumes in the same AZ as your Web Server EC2, each of 10 GiB.
Learn How to Add EBS Volume to an EC2 instance

Attach all three volumes one by one to your Web Server EC2 instance

![Screenshot 2022-02-25 222442](https://user-images.githubusercontent.com/97651517/155935437-b59f423c-aa90-4fea-80e0-f72e4463945a.png)

![Screenshot 2022-02-25 222512](https://user-images.githubusercontent.com/97651517/155938580-02ab6216-7d16-4b53-a7d7-bb3c49905cf4.png)


Open up the Linux terminal to begin configuration

#STEP 2

Use lsblk command to inspect what block devices are attached to the server.
Notice names of your newly created devices.
All devices in Linux reside in /dev/ directory. Inspect it with ls /dev/ and make sure you see all 3 newly created block devices there – their names will likely be xvdf, xvdh, xvdg.

command: lsblk

![Screenshot 2022-02-25 222534](https://user-images.githubusercontent.com/97651517/155936325-463711bc-dcac-4f0e-8ca0-bd875fc40a67.png)

Use df -h command to see all mounts and free space on your server


![Screenshot 2022-02-25 222907](https://user-images.githubusercontent.com/97651517/155936450-6a9082e3-a92d-47e9-be92-d50150b32235.png)

Inspect it with ls /dev/ and make sure you see all 3 newly created block devices there – their names will likely be xvdf, xvdh, xvdg.

![Screenshot 2022-02-25 223926](https://user-images.githubusercontent.com/97651517/155936676-a688a76f-78f2-4264-ab1d-94d4c52f79bc.png)

Let's start the partioning each disk using commands like sudo gdisk or fdisk or cfdisk or parted or even gparted.
but for this project let's just use sudo gdisk for each disk.

command: sudo gdisk /dev/xvdf

![Screenshot 2022-02-25 224626](https://user-images.githubusercontent.com/97651517/155937341-090141da-4062-476b-a3ba-89ce0810162e.png)

![Screenshot 2022-02-25 224655](https://user-images.githubusercontent.com/97651517/155937411-d53e569e-a654-4848-8658-8c8719d4ee9a.png)

Use lsblk utility to view the newly configured partition on each of the 3 disks.

![Screenshot 2022-02-25 225155](https://user-images.githubusercontent.com/97651517/155939071-ddb48a0c-5adf-4dbf-b04a-fea84a2e2cee.png)

#STEP 3

Install lvm2 package using sudo yum install lvm2. 

Run sudo lvmdiskscan command to check for available partitions.

![Screenshot 2022-02-25 225538](https://user-images.githubusercontent.com/97651517/155939699-e9426d9a-e6cf-4423-8df2-b01aa97546ec.png)

![Screenshot 2022-02-25 225602](https://user-images.githubusercontent.com/97651517/155939711-05ed3a8c-de7a-4c4c-8773-42448394c106.png)

![Screenshot 2022-02-25 225719](https://user-images.githubusercontent.com/97651517/155939728-46a8ef93-a07b-4c4b-86d6-6e4d073d9f6c.png)

Note: Previously, in Ubuntu we used apt command to install packages,
but in RedHat/CentOS a different package manager is used, so we shall use yum command instead.

Check if lvm2 is installed using the which command
command: which lvm

![Screenshot 2022-02-25 225830](https://user-images.githubusercontent.com/97651517/155939999-70913673-76a1-40b4-855b-b8fefeac1bea.png)

#STEP 4

Use pvcreate utility to mark each of 3 disks as physical volumes (PVs) to be used by LVM

![Screenshot 2022-02-25 230359](https://user-images.githubusercontent.com/97651517/155941356-b72f0629-1b0f-4f56-a3ce-c684a428d15a.png)

Verify that your Physical volume has been created successfully by running 

command: sudo pvs

![Screenshot 2022-02-25 230512](https://user-images.githubusercontent.com/97651517/155941522-6b96db8b-bd61-460b-b544-7fd6690f21b6.png)

Use vgcreate utility to add all 3 PVs to a volume group (VG). Name the VG webdata-vg

command: sudo vgcreate vg-webdata /dev/xvdh1 /dev/xvdg1 /dev/xvdf1

Verify that your VG has been created successfully by running thi command.

command: sudo vgs

![Screenshot 2022-02-25 231654](https://user-images.githubusercontent.com/97651517/155942960-cf13f786-d778-4b2d-a2fe-064279d6e4b3.png)

Use lvcreate utility to create 2 logical volumes. apps-lv (Use half of the PV size), and logs-lv Use the remaining space of the PV size.
NOTE: apps-lv will be used to store data for the Website while, logs-lv will be used to store data for logs.

commands:
sudo lvcreate -n apps-lv -L 14G vg-webdata
sudo lvcreate -n logs-lv -L 14G vg-webdata

![Screenshot 2022-02-25 232641](https://user-images.githubusercontent.com/97651517/155943795-f68ff00b-b31b-4b8f-b9dd-cc5567bddfc2.png)

Verify that your Logical Volume has been created successfully by running sudo lvs

[Screenshot 2022-02-25 232708](https://user-images.githubusercontent.com/97651517/155943932-40aa5fec-16b7-4813-a717-17c850ccc5b9.png)

What we have done so far with partioniong of our disk so run these commands:
sudo lvs (logical volume)
sudo vgs (volume group)
sudo pvs (physical volume)

![Screenshot 2022-02-25 233008 sudo vgs pvs and lvs](https://user-images.githubusercontent.com/97651517/155944331-cf783f99-9e70-40a6-8749-744b48b5a4ec.png)


Use mkfs.ext4 to format the logical volumes with ext4 filesystem

sudo mkfs -t ext4 /dev/vg-webdata/apps-lv
sudo mkfs -t ext4 /dev/vg-webdata/logs-lv

![Screenshot 2022-02-25 233439](https://user-images.githubusercontent.com/97651517/155946020-ec0a3875-f6fc-461c-881b-d82d398f6a07.png)
e70-40a6-8749-744b48b5a4ec.png)

Create /var/www/html directory to store website files.

sudo mkdir -p /var/www/html

Note: but make sure these directories exist, if not then create. 
During the project i forund out that /www/ as a directory doesnt exist in /var/ so run the command:
sudo mkdir -p /var/www/html
The flag -p in the above command indicates that if such directory doesnt exist then create it.

![Screenshot 2022-02-25 233905](https://user-images.githubusercontent.com/97651517/155946863-022d578b-6dc2-4ecf-b9a6-c632ad7a79f6.png)

Create /home/recovery/logs to store backup of log data

sudo mkdir -p /home/recovery/logs

#STEP 5

Mount /var/www/html on apps-lv logical volume
sudo mount /dev/webdata-vg/apps-lv /var/www/html/
Note: Before mounting the device it's best practice to check wether there is any contents in the direcory or folder, 
because when you mount devices on a paticular directory it automaticaly deletes whats in that directory so always backup such content existing in that directory.
if there is existing content or files existing then run this command to backup such files

sudo rsync -av /var/log/. /home/recovery/logs/

So check by using this command:

ls -l /var/www/html

![Screenshot 2022-02-25 234430 check that theres no content exisiting on your html folder before mounting otherwise it will auto delete it](https://user-images.githubusercontent.com/97651517/155948325-352a14fd-4dc4-462d-b413-eca2e384b710.png)

From the above image you can see that the content is 0, so we are good to go yaaaay!

Mount /var/www/html on apps-lv logical volume

sudo mount /dev/vg-webdata/apps-lv /var/www/html/

![Screenshot 2022-02-25 234804](https://user-images.githubusercontent.com/97651517/155949596-203e50b4-47f7-4bd7-954b-fd36f9dafbee.png)

Mount /var/log on logs-lv logical volume. 

(Note that all the existing data on /var/log will be deleted. That is why step 15 above is very
important)

sudo rsync -av /var/log/. /home/recovery/logs/

![Screenshot 2022-02-25 235039](https://user-images.githubusercontent.com/97651517/155950049-feb30c6b-bf75-494b-9a4a-1a97eedd2e3a.png)
 so our files has ben backed up

sudo mount /dev/vg-webdata/logs-lv /var/log

Restore log files back into /var/log directory

sudo rsync -av /home/recovery/logs/. /var/log

![Screenshot 2022-02-26 000717 copying back to log folder](https://user-images.githubusercontent.com/97651517/155951086-f79a7ab1-7a72-4aaf-a9c8-931f65c1a45d.png)

To confirm our backup was successfull run this command:
sudo ls -l /var/log

[Screenshot 2022-02-25 235346 backing the log folder1](https://user-images.githubusercontent.com/97651517/155951361-fbe85a72-d78c-4308-931f-8c9ffe381f9f.png)
 yaaaay! it was successfully backed up
 
 Update /etc/fstab file so that the mount configuration will persist after restart of the server.
 
 sudo blkid
 sudo vi /etc/fstab
 Update /etc/fstab in this format using your own UUID and rememeber to remove the leading and ending quotes.
 
 ![Screenshot 2022-02-26 002703](https://user-images.githubusercontent.com/97651517/155952681-28366738-1e44-4024-916a-94e0ff04015a.png)

 Test the configuration and reload the daemon
 
 sudo mount -a
 sudo systemctl daemon-reload
 
 ![Screenshot 2022-02-26 003014](https://user-images.githubusercontent.com/97651517/155953032-8cb7d5f5-7a35-43dc-9505-195a3016788a.png)
 
 ![Screenshot 2022-02-26 010253](https://user-images.githubusercontent.com/97651517/155953631-6c36e99e-197b-4c8a-8048-6fea3ad3ccf4.png)

Verify your setup by running df -h, output must look like this:

![Screenshot 2022-02-25 234804](https://user-images.githubusercontent.com/97651517/155953996-bf8585ea-b9b7-430e-b397-31966ef45ff6.png)

#Step 2 — Prepare the Database Server
 
 Use the second RedHat EC2 instance that will have a role – ‘DB Server’
Repeat the same steps as for the Web Server, but instead of apps-lv create db-lv and mount it to /db directory instead of /var/www/html/.

![Screenshot 2022-02-26 004833](https://user-images.githubusercontent.com/97651517/155954921-f11b3e6e-0acb-4ae4-a50b-68f16aec3ff5.png)

Before as stated earlier check for existing files
command: sudo ls -l /db

![Screenshot 2022-02-26 005731](https://user-images.githubusercontent.com/97651517/155955044-ce66795b-10e4-476e-a13f-4bfb049e7d77.png)

Next step is to mount the device to the directory

command: sudo mount /dev/vg-database/db-lv /db
check: df -h

![Screenshot 2022-02-26 005802](https://user-images.githubusercontent.com/97651517/155955541-8dde78f1-d7f3-4547-bc9a-b210622c4ca1.png)

Next step is to make mount persist upon restart of server see above documentation

#STEP 6

Install WordPress on your Web Server EC2

Update the repository for both web server and database server

sudo yum -y update

![Screenshot 2022-02-26 011228](https://user-images.githubusercontent.com/97651517/155956561-59904e65-1374-43af-b8e4-ae6dadcbae3c.png)

![Screenshot 2022-02-26 011307](https://user-images.githubusercontent.com/97651517/155956607-a7a3d70e-d5db-4e21-8b54-73a8b4766991.png)

Install wget, Apache and it’s dependencies

sudo yum -y install wget httpd php php-mysqlnd php-fpm php-json

![Screenshot 2022-02-26 014856 install wget](https://user-images.githubusercontent.com/97651517/155957796-18999aee-80fd-426f-8068-e3f2e58be40d.png)

#Next step installing the latest version of php

sudo dnf install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
sudo dnf install yum-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm

![Screenshot 2022-02-26 011632](https://user-images.githubusercontent.com/97651517/155959480-cab5e965-4f7a-48aa-8a03-2ff3eb53f43f.png)

sudo dnf module list php

![Screenshot 2022-02-26 011736](https://user-images.githubusercontent.com/97651517/155959782-0aa1fa88-198f-4f6e-93b7-ba96a1a9c791.png)

sudo dnf module reset php

![Screenshot 2022-02-26 011932](https://user-images.githubusercontent.com/97651517/155960387-47ecd09a-50c6-4866-9be7-3a92c2056057.png)

sudo dnf module enable php:remi-7.4

![Screenshot 2022-02-26 012132](https://user-images.githubusercontent.com/97651517/155960542-4cf5c989-1f5a-4e83-8d52-d0be2f964bb9.png)

sudo dnf install php php-opcache php-gd php-curl php-mysqlnd

![Screenshot 2022-02-26 012407](https://user-images.githubusercontent.com/97651517/155960665-0d46df65-a317-457b-b413-143c092eb428.png)

![Screenshot 2022-02-26 012431](https://user-images.githubusercontent.com/97651517/155960962-fc2a6291-4326-4f02-8518-1d048e088503.png)

![Screenshot 2022-02-26 012505](https://user-images.githubusercontent.com/97651517/155960976-7b36d437-9770-4af1-b684-7b79f721d5b4.png)

![Screenshot 2022-02-26 012532](https://user-images.githubusercontent.com/97651517/155960989-35456a3b-07da-4f62-b776-813a31a30b6f.png)

Check version of php

php -v

![Screenshot 2022-02-26 012611](https://user-images.githubusercontent.com/97651517/155961249-cfde79cf-a080-4b82-98d2-7ea07eb321c8.png)

sudo systemctl start php-fpm
sudo systemctl enable php-fpm

![Screenshot 2022-02-26 012736](https://user-images.githubusercontent.com/97651517/155961489-717eb6fb-7886-4b8a-9844-c5f4f3aa407c.png)

sudo systemctl status php-fpm

![Screenshot 2022-02-26 012824](https://user-images.githubusercontent.com/97651517/155961576-33da235a-8612-4a9a-a9a8-cb670b5d8b89.png)

sudo setsebool -P httpd_execmem 1

![Screenshot 2022-02-26 013002](https://user-images.githubusercontent.com/97651517/155961963-9d3b2f22-fb3a-403b-88a0-8fd8f8351701.png)

sudo systemctl restart httpd

![Screenshot 2022-02-26 013244](https://user-images.githubusercontent.com/97651517/155962385-eb6aad56-bc1e-47f8-a25d-7e7c650a81ab.png)

Next step get public ip address of your web-server and paste on the browser's URL

![Screenshot 2022-02-26 013418](https://user-images.githubusercontent.com/97651517/155962718-6c25aa9c-db7a-478c-b8bf-422b6f91461c.png)

Your Output should be this:

![Screenshot 2022-02-26 013603](https://user-images.githubusercontent.com/97651517/155962827-8673f173-eb4f-4e49-a930-26609bd01992.png)

#STEP 7
 
 mkdir wordpress
 cd   wordpress
 sudo wget http://wordpress.org/latest.tar.gz

![Screenshot 2022-02-26 015057](https://user-images.githubusercontent.com/97651517/155963200-0e5563b1-46d6-46d5-8f49-6b9ba5fad95d.png)

ls -l

![Screenshot 2022-02-26 015215](https://user-images.githubusercontent.com/97651517/155963325-6113450c-65ff-48a5-b0b2-cdef2d0a1b01.png)

 sudo tar xzvf latest.tar.gz
 
 ![Screenshot 2022-02-26 015422](https://user-images.githubusercontent.com/97651517/155963477-c80f5053-6751-4173-b1f1-9b38f29a716d.png)
   
 ls -l showing new folder called 'wordpress'

![Screenshot 2022-02-26 015507](https://user-images.githubusercontent.com/97651517/155963523-7293f98e-07ce-44cb-ad52-c607451030c6.png)

wordpress directory listed

![Screenshot 2022-02-26 015610](https://user-images.githubusercontent.com/97651517/155963750-39863c9f-8354-4a5e-abd4-d6ca2ace5322.png)

copying the wp-config-sample.php into the wp.config.php

![Screenshot 2022-02-26 015901](https://user-images.githubusercontent.com/97651517/155964197-c3dd0f84-24f0-4fa5-8477-9a420c8e7fb8.png)

wp.config.php showing

![Screenshot 2022-02-26 020001](https://user-images.githubusercontent.com/97651517/155964425-d1cac4fd-f7d8-4da0-ba23-692ebcaf98ce.png)

cp -R wordpress /var/www/html/

![Screenshot 2022-02-26 021207](https://user-images.githubusercontent.com/97651517/155965147-973765bd-6bd0-4ad4-8eb7-1657ab7eae42.png)

cd /var/www/html
ls

![Screenshot 2022-02-26 021311](https://user-images.githubusercontent.com/97651517/155965324-8df268e5-0483-4ce3-904d-c2443182bb5d.png)

#STEP 8

You need to install mysql-server on your web-server and also for your database-server for connection between both servers.

![Screenshot 2022-02-26 021645](https://user-images.githubusercontent.com/97651517/155965706-83d2440d-d60d-4f45-98a4-54ef7d8a30bd.png)

Check status of both servers after installing

![Screenshot 2022-02-26 022039](https://user-images.githubusercontent.com/97651517/155966414-11a08dba-177a-449f-8225-c0c9073f00d8.png)

![Screenshot 2022-02-26 022428 same command for mysql](https://user-images.githubusercontent.com/97651517/155966417-09f646a6-a1af-45d3-9e80-2d6f85c6bc60.png)

sudo mysql-secure-installation

![Screenshot 2022-02-26 022819](https://user-images.githubusercontent.com/97651517/155967090-9e62ef13-606a-43ad-bc8c-48cb1d78e209.png)


![Screenshot 2022-02-26 022850](https://user-images.githubusercontent.com/97651517/155967445-83b6e1a7-b7be-4acc-8b79-60dee9b94571.png)

sudo mysql

![Screenshot 2022-02-26 023040](https://user-images.githubusercontent.com/97651517/155967491-250e3d70-6eb9-4f9d-86cf-475cf2a5755d.png)
 
 create database wordpress

![Screenshot 2022-02-26 023313](https://user-images.githubusercontent.com/97651517/155967694-2ca944ee-a05b-4bd0-9ea1-289a1ef8382e.png)

show databses and create user and password

![Screenshot 2022-02-26 024246](https://user-images.githubusercontent.com/97651517/155967850-ae550cbf-58bc-4a32-a371-c2a18c7fc160.png)

Edit the config file for mysql

![Screenshot 2022-02-26 024709](https://user-images.githubusercontent.com/97651517/155969066-6a8083dd-15e2-4c4b-a51b-95e0cbbbe82c.png)

#STEP 9

Next config the wp-config.php file on your web-server

command: sudo vi wp-config.php

NB: make sure to replace <localhost> to <private IP addrr of database-server> because they are in thesame subnet and connection is within

![Screenshot 2022-02-26 025524](https://user-images.githubusercontent.com/97651517/155970247-576121b1-d747-443d-b52a-93c239baefc5.png)

 Run the following command
  
![Screenshot 2022-02-26 030140](https://user-images.githubusercontent.com/97651517/155970694-f8d07b62-8b8c-4bd8-b4da-7d1a0d241701.png)
  
 #STEP 10
  
  To ensure connection has been establsihed between web-server and database-serevr you should check
  
 
  \Nice....
 
  ![Screenshot 2022-02-26 030428](https://user-images.githubusercontent.com/97651517/155971109-cef65466-a75f-4636-8b7c-65c718bf821b.png)
  
  showing databases also yaaay!
 
  ![Screenshot 2022-02-26 030514](https://user-images.githubusercontent.com/97651517/155971334-8cf820f7-ad9b-4301-b802-2ab4ba1d1fec.png)
  
 #STEP 11
  
  #Configure SELinux Policies
  
  sudo chown -R apache:apache /var/www/html/wordpress
  
  ls -l
  
  ![Screenshot 2022-02-26 031150](https://user-images.githubusercontent.com/97651517/155972202-a36f5fff-a582-437d-840c-104f10504f05.png)
  
  sudo chcon -t httpd_sys_rw_content_t /var/www/html/wordpress -R
  
  sudo setsebool -P httpd_can_network_connect=1
  
  sudo setsebool -P httpd_can_network_connection_db 1
  
  ![Screenshot 2022-02-26 031609](https://user-images.githubusercontent.com/97651517/155972651-9373430b-d7b5-449b-9370-3c57419f368f.png)
  
  # FINAL STEP
  
  Refresh the Apche page on your browser URL you should get this output
  
  ![Screenshot 2022-02-26 031917](https://user-images.githubusercontent.com/97651517/155972792-6cf09358-7d9d-4463-bd75-e65e2a5f4ea8.png)

  ![Screenshot 2022-02-26 032044](https://user-images.githubusercontent.com/97651517/155972838-38682498-d9cd-4145-864e-3a2a8a8f05b8.png)
 
  ![Screenshot 2022-02-26 032152](https://user-images.githubusercontent.com/97651517/155972877-721347ac-865e-454b-8b98-18eda40226f5.png)

  ![Screenshot 2022-02-26 032232](https://user-images.githubusercontent.com/97651517/155972905-63e50bbd-9ac9-4511-8c78-4ff69403b931.png)
   
  And that concludes this project buildng a Web Solution with WORDPRESS
  
  THANK YOU!
