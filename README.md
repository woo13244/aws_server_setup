# aws_server_setup
Creating a test server for your website


// Step 1 --> Create an aws.amazon account and confirm account.

// Step 2 --> Sign in to your account and go to the Home page.

  - On the top right of the page choose the closest host server. Ex. US East (N.Virginia).
  - Click EC2 link under services in the Compute category. This will take you to the EC2 Dashboard.
  
// Step 3 --> Create an Instance

  - Click Instances under INSTANCES category in the side menu after entering the EC2 Dashboard.
  - Click Launch Instance.
  - Select correct setup for your server Ex. Ubuntu Server 16.04 LTS(HVM), SSD Volume Type.
  - Select appropriate Instance Type usually for mid sized applications the type is usually m5d.large and up.
  - Click Next: Configure Instance Details.
  - Change subset -> this should be a subset that is unique to this specific instance. YOU NEED TO REMEBER THIS VALUE!!!
  - Click Next: Add Storage.
  - Select appropriate Size(GiB) usually 30gb for small applications.
  - Click Next: Add Tags.
  - Click Next: Configure Security Group
  - Add all Types your site requires, usually HTTP, HTTPS, SSH.
  - Change all security group's source to Anywhere.
  - Click Review and Launch
  - .....review.....anndd........
  - Click Launch
  - Select an existing key pair or create a new key pair.
  - Create new key pair with unique name.
  - Download the key pair file. This is a one copy document if you lose this you are screwed....SAVE IT IN A SAFE PLACE.
  - Launch Instance.-> Name your new Instance.
  
// Step 4 --> Create and associate an Elastic Ip

  - Click Elastic IPs under NETWORK & SECURITY in the side menu after entering the EC2 Dashboard.
  - Click Allocate new address
  - Click Allocate
  - Return to Elastic IPs
  - Name your new Elastic Ip and name it.
  - Select the new Elastic IP.
  - Click Actions -> Associate address.
  - Select your previously made Instance.
  - Select Private IP -> should be auto generated after choosing instance.
  - Click Associate.
  
// Step 5 --> Create a Volume for your Data

  - Click Volumes under ELASTIC BLOCK STORE in the side menu after entering EC2 Dashboard.
  - There should already be a volume generated for your newly created Instance, name it. We need a new one for our data.
  - Click Create Volume.
  - Change Volume Type to General Purpose SSD.
  - Change Size(GiB) to appropriate size. Usually 100 for small to mid sized applications.
  - Select Availablility Zone -> this must match the server subset you chose for your instance. I TOLD YOU TO REMEMBER THE VALUE!!
  - Click Create Volume.
  - Try to refresh the page if you don't see your new volume.
  - Name your new volume usually named your instance name with "data" at the end.
  - Click on volume and Click Actions.
  - Click Actions -> Attach Volume.
  - Search and enter your Instance name. If you selected a matching availability zone it should auto generate.
  - Click Attach
  
// Step 6 --> Server setup

  - Retreive your Instance IP from Instances. This is the IP under the IPV4 Public IP column.
  - Open Gitbash and cd into the folder where your Intance's key pair file is located.
  - Run command -> ssh -i KeyPairFileName.pem ubuntu@InstanceIP
  - Run command -> sudo su
  - Run command -> adduser NameOfUser
  - create your password and add account details.
  - Run command -> usermod -a -G www-data NameOfUser
  - Run command -> usermod -a -G sudo NameOfUser
  - Run command -> vim /etc/ssh/sshd_config
  - Change PasswordAuthentication no to PasswordAuthentication yes.
  - exit file and Run command -> service ssh restart
  - Run command -> exit
  - Repeat until you exit out of ssh server.
  - Sign in to server with newly added user by Running command -> ssh UserName@InstanceIp
  - Run command -> sudo su
  - Run command -> lsblk -> this will give you a list of the devices
  - Run command -> mkfs -t ext4 /dev/DeviceName
  - Run command -> mkdir /data
  - Run command -> chown -R threeon:threeon /data
  - Run command -> mount /dev/DeviceName /data
  - Run command -> blkid
  - Get UUID and copy it somewhere.
  - Run command -> vim /etc/fstab
  - On line 2 Enter -> UUID=YourUUID `hit tab` /data `hit tab` ext4 `hit tab` defaults,nofail `hit space` 0 `hit tab` 2
  - Exit file
  - Run command -> mount -a
  - If there is no errors after running the mount -a command then continue to Run command -> reboot
  - Sign in to server with user Run command -> ssh UserName@InstanceIp
  - Run command -> sudo chown -R UserName /data/
  - Run command -> sudo chgrp -R UserName /data/
  - Run command -> sudo chmod -R 775 /data/
  - Run command -> sudo chmod -R g+s /data/
  - Run command -> cd /data
  - Run command -> git clone https://wonsong82@bitbucket.org/wonsong82/server.git -> make sure you are not in sudo mode
  - Run command -> mkdir www
  - Run command -> mkdir UserName
  - Run command -> sudo chown -R www-data:www-data www
  - Run command -> sudo chgrp -R www-data www
  - Run command -> sudo chmod -R g+s www
  - Run command -> sudo ln -s /data/server/scripts /scripts
  - Run command -> sudo ln -s /data/www /var/www
  - Run command -> cd /scripts/config/lamp
  - Run command -> cp conf.example conf
  - Run command -> vim conf
  - Change databases to databases=()
  - Change db_users to db_users=( 'UserName,UserPass' )
  - exit file.
  - Run command -> cd /scripts/config/lamp/sites
  - Run command -> touch test.conf
  - Run command -> cd sample -> vim sample.conf -> copy content.
  - Run command -> cd .. -> vim test.conf
  - Paste text from sample.conf and update namespace
  - Run command -> cd /scripts
  - Make sure you are not in sudo mode.
  - Run command -> bash install
  - Run command -> cd /data
  - Run command -> sudo su
  - Run command -> rsync -avzh /var/lib/mysql /data
  - Run command -> vim /etc/fstab
  - Bellow UUID line on line 3 enter -> /data/mysql `hit tab` /var/lib/mysql `hit tab` none `hit tab` bind `hit tab` 0 `hit tab` 0
  - exit file.
  - Run command -> mount -a
  - Run command -> cd /scripts/config/cron
  - Run command -> cp backup.json.sample backup.json
  - Run command -> vim backup.json
  - Change backup to point to /var/www/namespace/backups.
  - Change dir to point to /var/www/namespace.
  - Change db to database name. example test
  - Run command -> crontab -e -> choose 2 if asked which kind
  - Enter on next line -> 0 1 * * * /usr/bin/php /scripts/cron/backup.php
  - exit file by ctrl X, y, enter.
  - Make sure you are not in sudo mode.
  - cd /var/www/
  - rename html to test by Running command -> mv html/ test
  - Run command -> sudo chown -R threeon:www-data test/
  - cd /var/www/test
  - Run command -> mkdir _logs
  - vim index.html -> write something in there like "Hello World"
  - exit file.
  - Run command -> cd /scripts
  - Run command -> bash setup 
  
  
  








  

  
  
  
  
  
  
  
