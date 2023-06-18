# MySQL Setup On Rocky Linux 9

Goal: Describe the process of setting up MySQL on Rocky Linux 9. Obvioulsy this should be applicable for other RHEL related distrobutions.

# Add MySQL repository and install mysql.
By entering the following command, an rpm can be download, through which the mysql repository can be added to the device.
`wget https://repo.mysql.com//mysql80-community-release-el9-1.noarch.rpm`
Install the rpm package, and then install mysql-community-server,
`sudo dnf in mysql-community-server`

# Log into mysql
Use systemctl to start and enable mysqld, before loging in, it is necisary to get
the initial passward for the root account, which is found via the following command ...
`sudo grep 'temporary password' /var/log/mysqld.log`
Identify the password and copy it, then, as root, enter
`mysql -u root -p`
Paste the password when prompted fo it.

# Set up accounts
Being logged in as root, the first step is to change the root password, which is done as follows ...
`ALTER USER 'root'@'localhost' IDENTIFIED BY '<password>'; `
Now I am going to create an admin user, be sure host is '%' if you are loggin in remotly.
`CREATE USER '<user>'@'%' INDENTIFIED BY '<password>';`
Next I create a database for the user to manipulate.
`CREATE DATABASE <database>;`
And then give the user full rights on that database
`grant all privileges on <database>.* to '<database>'@'%';`

# Enable remote connection.
First, put SELinux into permisve mode (Note, I understand this is not a good way of doing things in
a production environment, but this device is just being used on a local network for development purposes, and 
at the moment I want to spend my time learning MySQL and not SELinux, in the future I plan to learn how to properly
enable the specific connection on port 3306 via SELinux). In the /etc/selinux/config file, find SELINUX, and set it to
'permissive', then reboot vm.

In /etc/my.cnf, under [mysqld], add entety bind-address = <ip conecting to server>, or bind-address = 0.0.0.0 to enable all incoming conections.
