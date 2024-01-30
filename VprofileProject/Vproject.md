# Define the services used in this virual profile project
    - Tomcat is an application server to host Java Web Application like vprofile.

    - Nginx is a frontent server, web server and can be used as a Load balancer.

    - Mysql is a SQL Database server, similar are Mariadb, MSSql etc

    - RabbitMQ is the most widely deployed open source message broker.

    - Memcached is an in-memory key-value store for small chunks of arbitrary data (strings, objects) from results of database calls.

    - Application.properties is used by tomcat server to find information of all the backend services mysql, memcache, RabbitMQ, Elasticsearch


## If you dont have vagrant plugin install it
    - vagrant plugin install vagrant-hostmanager
        - example what will get when we run: cat /etc/hosts
                ## vagrant-hostmanager-start
                        192.x.x.30   app01

                        192.x.x.31   db01

                        192.x.x.32   mc01

                        192.x.x.33   rmq01

                        192.x.x.34   web01

                ## vagrant-hostmanager-end
            ** NOTE: All the services connect by name so that they get the ip depending of the name from vagrant-hostmanager. (thus we put as global variable in vagrantfile in the begining).
    
 ## Write the multi vagrant vms in vagrantfile
    - MYSQL Database 
    - Memcache (DB CACHING SVC)
    - RabbitMQ (Broker/Queue SVC)
    - Tomcat (Application SVC)
    - Nginx (Web SVC)

## Start the vagrant 5 VM's
    - vagrant up
    - vagrant ssh (to every machine and test to ping to other services try: cat /etc/hosts in every machine )

#  MYSQL Setup
    Login to the db vm
    - vagrant ssh db01

    Verify Hosts entry, if entries missing update the it with IP and hostnames
    - cat /etc/hosts

    Update OS with latest patches
    - yum update -y  (update os with last patches)

    Set Repository
    - yum install epel-release -y  ( Set repository to get access to more packages)

    Install Maria DB Package
    - yum install git mariadb-server -y

    Starting & enabling mariadb-server
    - systemctl start mariadb
    - systemctl enable mariadb

    RUN mysql secure installation script
    - mysql_secure_installation  -->> prompts-questions --> Y, Y, n, Y, Y.

    ** Till now:  The service is up and running we need to setup the database, service is created but the database is not created yet.

    Set DB name and users.
    - mysql -u root -padmin123  (Set DB name and users **Remark no space between -padmin123)
    - mysql > create database accounts;
    - mysql > grant all privileges on accounts. * TO 'admin'@'%' identified by 'admin123';
    - mysql > FLUSH PRIVILEGES;
    - mysql > exit;

    Download Source code & Intialize Database
    - git clone -b main https://github.com/hkhcoder/vprofile-project.git
    - cd vprofile-project
    - mysql -u root -padmin123 accounts < src/main/resources/db_backup.sql
    - mysql -u root -padmin123 accounts

    - mysql> show tables;
    - mysql> exit;

    Restart mariadb-server
    - systemctl restart mariadb

    Starting the firewall and allowing the mariadb to access from port number 3306
    - systemctl start firewalld
    - systemctl enable firewalld
    - firewall-cmd --get-active-zones
    - firewall-cmd --zone=public --add-port=3306/tcp --permanent
    - firewall-cmd --reload
    - systemctl restart mariadb


# MEMCACHE SETUP

    Login to the Memcache vm
    - vagrant ssh mc01

    Verify Hosts entry, if entries missing update the it with IP and hostnames
    - cat /etc/hosts

    Update OS with latest patches
    - yum update -y

    Install, start & enable memcache on port 11211
    - sudo dnf install epel-release -y
    - sudo dnf install memcached -y
    - sudo systemctl start memcached
    - sudo systemctl enable memcached
    - sudo systemctl status memcached
    - sed -i 's/127.0.0.1/0.0.0.0/g' /etc/sysconfig/memcached
    - sudo systemctl restart memcached

    Starting the firewall and allowing the port 11211 to access memcache
    - firewall-cmd --add-port=11211/tcp
    - firewall-cmd --runtime-to-permanent
    - firewall-cmd --add-port=11111/udp
    - firewall-cmd --runtime-to-permanent
    - sudo memcached -p 11211 -U 11111 -u memcached -d


# RABBITMQ SETUP
    Login to the RabbitMQ vm
    -  vagrant ssh rmq01

    Verify Hosts entry, if entries missing update the it with IP and hostnames
    - cat /etc/hosts

    Update OS with latest patches
    -  yum update -y

    Set EPEL Repository
    -  yum install epel-release -y

    Install Dependencies
    - sudo yum install wget -y
    - cd /tmp/
    - dnf -y install centos-release-rabbitmq-38
    - dnf --enablerepo=centos-rabbitmq-38 -y install rabbitmq-server
    - systemctl enable --now rabbitmq-server

    Setup access to user test and make it admin
    - sudo sh -c 'echo "[{rabbit, [{loopback_users, []}]}]." > /etc/rabbitmq/rabbitmq.config'
    - sudo rabbitmqctl add_user test test
    - sudo rabbitmqctl set_user_tags test administrator
    - sudo systemctl restart rabbitmq-server

    Starting the firewall and allowing the port 5672 to access rabbitmq
    - firewall-cmd --add-port=5672/tcp
    - firewall-cmd --runtime-to-permanent
    - sudo systemctl start rabbitmq-server
    - sudo systemctl enable rabbitmq-server
    - sudo systemctl status rabbitmq-server



# TOMCAT SETUP

    Login to the tomcat vm
    -  vagrant ssh app01

    Verify Hosts entry, if entries missing update the it with IP and hostnames
    -  cat /etc/hosts

    Update OS with latest patches
    -  yum update -y

    Set Repository
    -  yum install epel-release -y

    Install Dependencies
    -  dnf -y install java-11-openjdk java-11-openjdk-devel
    -  dnf install git maven wget -y

    Change dir to /tmp
    -  cd /tmp/

    Download & Tomcat Package
    -  wget https://archive.apache.org/dist/tomcat/tomcat-9/v9.0.75/bin/apache-tomcat-9.0.75.tar.gz
    - tar xzvf apache-tomcat-9.0.75.tar.gz

    Add tomcat user
    - useradd --home-dir /usr/local/tomcat --shell /sbin/nologin tomcat

    Copy data to tomcat home dir
    - cp -r /tmp/apache-tomcat-9.0.75/* /usr/local/tomcat/

    Make tomcat user owner of tomcat home dir
    - chown -R tomcat.tomcat /usr/local/tomcat

    ## Setup systemctl command for tomcat 
    Create tomcat service file
    -  vi /etc/systemd/system/tomcat.service
    - Update the file with below content
        [Unit]
        Description=Tomcat
        After=network.target
        [Service]
        User=tomcat
        WorkingDirectory=/usr/local/tomcat
        Environment=JRE_HOME=/usr/lib/jvm/jre
        Environment=JAVA_HOME=/usr/lib/jvm/jre
        Environment=CATALINA_HOME=/usr/local/tomcat
        Environment=CATALINE_BASE=/usr/local/tomcat
        ExecStart=/usr/local/tomcat/bin/catalina.sh run
        ExecStop=/usr/local/tomcat/bin/shutdown.sh
        SyslogIdentifier=tomcat-%i
        [Install]
        WantedBy=multi-user.target

    Reload systemd files
    - systemctl daemon-reload

    Start & Enable service
    -  systemctl start tomcat
    - systemctl enable tomcat

    Enabling the firewall and allowing port 8080 to access the tomcat
    - systemctl start firewalld
    - systemctl enable firewalld
    - firewall-cmd --get-active-zones
    - firewall-cmd --zone=public --add-port=8080/tcp --permanent
    - firewall-cmd --reload
## CODE BUILD & DEPLOY (app01)
    Download Source code
    -  git clone -b main https://github.com/hkhcoder/vprofile-project.git

    Update configuration
    - cd vprofile-project
    - vim src/main/resources/application.properties
    - Update file with backend server details

    Build code
    Run below command inside the repository (vprofile-project)
    -  mvn install

    Deploy artifact
    - systemctl stop tomcat
    - rm -rf /usr/local/tomcat/webapps/ROOT*
    - cp target/vprofile-v2.war /usr/local/tomcat/webapps/ROOT.war
    - systemctl start tomcat
    - chown tomcat.tomcat /usr/local/tomcat/webapps -R
    - systemctl restart tomcat



# NGINX SETUP

    Login to the Nginx vm
    - vagrant ssh web01
    - sudo -i

    Verify Hosts entry, if entries missing update the it with IP and hostnames
    - cat /etc/hosts

    Update OS with latest patches
    - apt update
    - apt upgrade

    Install nginx
    - apt install nginx -y

    Create Nginx conf file
    - vi /etc/nginx/sites-available/vproapp

    Update with below content
        upstream vproapp {
        server app01:8080;
        }
        server {
        listen 80;
        location / {
        proxy_pass http://vproapp;
        }
        }

    Remove default nginx conf
    - rm -rf /etc/nginx/sites-enabled/default

    Create link to activate website
    - ln -s /etc/nginx/sites-available/vproapp /etc/nginx/sites-enabled/vproapp

    Restart Nginx
    - systemctl restart nginx
    

############################ All services on the VM'S are installed and configured ###########################
