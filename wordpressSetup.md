# Creating wordpress setup on ubuntu 20 
    To get more closely to wordpress, visit this link that cover how wordpress should be install and configure: https://ubuntu.com/tutorials/install-and-configure-wordpress#6-configure-wordpress-to-connect-to-the-database

    - installing dependencies
    - installing the package
    - configure Apache for wordpress
    - configure database
    - configure database to connect to the database
    - configure wordpress

Once you grasp the info about installation and configuration go to vagrant box and search for ubuntu 20 and you can use ubuntu/focal64:
    - You should see this:
        - Vagrant.configure("2") do |config|
            config.vm.box = "ubuntu/focal64"    
        end
        # copy ubuntu/focal64 

    - Create new folder <new-folder i called worpress>
    - cd wordpress
    - vagrant init ubuntu/focal64
    - ls to see: vagrantfile
    - vim vagrantfile 
        - edit ip: config.vm.network "private_network", ip: "192.168.x.x"
        - to get ip from local network uncomment:  config.vm.network "public_network"
        - uncommit: config.vm.provider "virtualbox" do |vb|
        - uncommit & change to 1600 depend on computer used: vb.memory = "1600"
        - uncommit: end

    Start the virtual machine
    - vagrant up
    - vagrant ssh
    
    (optional) To change hostname:
        - sudo -i
        - vim /etc/hostname  ---> then write wordpress or any other name | save and quit
        - hostname wordpress ---> exit and ssh again to take place.

    ## First go to the link and install dependencies 
    DONT COPY AND PASTE DIRECTLY ON A TERMINAL, copy them in anot pad and then to the terminal.
    - run first: sudo apt update
    - second: At the end of php-zip add -y: php-zip -y | then copy all and paste in terminal to install.
    
    ## Second install WordPress
    Copy and Paste this in the notepad : 
        sudo mkdir -p /srv/www
        sudo chown www-data: /srv/www
        curl https://wordpress.org/latest.tar.gz | sudo -u www-data tar zx -C /srv/www

    wait until the previous step to be done then paste the three lines one by one to the terminal. (so that you can figure out which command did an error).
    - ls -l /srv/www/wordpress  (to see all the files)
    ** NOTE: wordpress will play the same role as /srv/www/html to see that in the website setup file.

    ## Third Configure Apache for WordPress
    Create Apache site for WordPress. Create /etc/apache2/sites-available/wordpress.conf with following lines:
        - vim /etc/apache2/sites-available/wordpress.conf   (wordpress file when you save the file will be created)
        - copy the content from the website: https://ubuntu.com/tutorials/install-and-configure-wordpress#4-configure-apache-for-wordpress
        - Paste the content in the vim wordpress.conf
        - copy the followed commands in anotepad before pasting them to the terminal:
            sudo a2ensite wordpress   (enable wordpress site)
            sudo a2enmod rewrite        (Set the redirect rule)
            sudo a2dissite 000-default   (disable apache default web page)
        - Relaod the apache2 service: sudo service apache2 reload
        - ls -l /etc/apache2/sites-available/  (to see the config files and configuration file is inside wordpress.conf).

        ## Fourth configure the database (should be more carefull in this stage)
        - copy to notepad:
            - sudo mysql -u root   (logging to sql as root user)
            - CREATE DATABASE wordpress;   ( create the database named worpress)
                - show databases;   (you can see wordpress)
            - CREATE USER wordpress@localhost IDENTIFIED BY '<your-password>'; (dont forget to write the pass and remove the < >)
            - GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP,ALTER
                -> ON wordpress.*
                -> TO wordpress@localhost;  (change it to be one single command in notepad like this ---> GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP,ALTER ON wordpress.* TO wordpress@localhost;)
            
            - Then observe that every command finish with semi-colon, paste the four commands in the terminal one by one.


        ## configure WordPress to connect to the database
        Now, how does wordpress where is the database, what is the user name and password.
        - After copying to notepad paste to terminal some need to be change:
            - sudo -u www-data cp /srv/www/wordpress/wp-config-sample.php /srv/www/wordpress/wp-config.php
            - sudo -u www-data sed -i 's/database_name_here/wordpress/' /srv/www/wordpress/wp-config.php
            - sudo -u www-data sed -i 's/username_here/wordpress/' /srv/www/wordpress/wp-config.php
            - sudo -u www-data sed -i 's/password_here/<your-password>/' /srv/www/wordpress/wp-config.php
            ** NOTE: Dont forget to edit the password prompt

            - vim /srv/www/wordpress/wp-config.php   (to see the config requirements)

            - Open this link https://api.wordpress.org/secret-key/1.1/salt/ and copy all of them.
            -  sudo -u www-data vi /srv/www/wordpress/wp-config.php
            - search down for 8 lines start with define remove them and paste the lact copied ones.  Save and exit.
            

        ## Configure WordPress
            - ip addr show (get the ip address if not remembered)
            - open it in new window.  ( you should see the wordpress setup).

            - continue for english
            - site title: ex: devops
            - username: ex: your-name
            - password: ex: vtj85XJ!SXuU6c4   as mentioned ready password-needed to login.
            - emailadd: ex: fadel@opsdev.com  
            - Install WordPress
        


