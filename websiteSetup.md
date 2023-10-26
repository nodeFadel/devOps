# Website setup on a virtual machine centos 
    - create folder <new-folder i called it finance> then cd finance.
    - run: vagrant box list   (to see the eurolinux-vagrant/centos-stream-9 (virtualbox, 9.0.29) in the list).
    - create the virtual machine: vagrant init eurolinux-vagrant/centos-stream-9.
    - Edit the vagrant file: vim Vagrantfile.
        - uncomment those lines and fix a unique ip: 
            - config.vm.network "private_network", ip: "192.168.x.x"
            - config.vm.network "public_network"
            - config.vm.provider "virtualbox" do |vb|
            - vb.memory = "1024"
            - end
            Save and exit vim.
        - vagrant up
        - vagrant ssh

        - In terminal download first dependencies:
             - Because centos we use yum if ubuntu we use apt: yum install httpd wget vim unzip zip -y
        
        START AND ENABLE httpd
        - systemctl start httpd
        - systemctl enable httpd

    - visit this website https://www.tooplate.com/view/2135-mini-finance ( go down to find download and write click to copy path)

    After coping the link path go to tmp folder
    - cd /tmp/
    - get the ziped package: wget https://www.tooplate.com/zip-templates/2135_mini_finance.zip
    - unzip the package: unzip 2135_mini_finance.zip 
    - ls (to see all the files and index.html one of them).
    - cd the unzipped directory: 2135_mini_finance
    copy all to: cp -r * /var/www/html/

    Restart the httpd service
    - systemctl restart httpd

    for testing and study purpose put the firwall inactive 
    because if you use fodera the firewall be by default active then:
    - systemctl status firewalld ( should be inactive (dead))
    - systemctl stop firewalld (if active)


On terminal if you dont remember the given ip for this virtual machine:
- ip addr show
- get the ip and open a new window and try the website server to see the index.html file displayed as finance page.
