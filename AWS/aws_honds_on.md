## To work with CLI interface:
    - Create the IAM user with AdministrativeAccess permissions.
    - Click on the User created and click on access-key to generate a new access key and download it (secure your key)
    - Open bash cli and run aws configuration.
    - First prompt enter the access-key given, Second prompt enter the secret access key given both given from generated accessKey created.
    TO SEE IT WORKS as checkup:
    - Run cat ~/.aws/config
    - Run cat ~/.aws/credentials
    - Run aws sts get-caller-identity
    - Run aws ec2 describe-instances  (it shows your instances in this particular region).

## Create Volume and format:
    - Run fdisk -l (to see the disk)
    - Run df -h (to see filesystem and check the mount files)
    - Run fdisk /dev/xvdf  (get new disk path from this command fdisk -l)
        1. insert: n 
        2. insert: p for primary
        3. insert: 1 for partition number
        4. insert: First sector: Enter , Last sector: +3G
        5. insert: w to write
    - Run mkfs.ext4 /dev/xvdf1  (to format the disk)
    - Run mkdir -p /var/lib/mysql  (where the mysql will store data)
    - Run vi /etc/fstab  & change this path /var/www/html.. with this /dev/xvdf1
    - Run mount -a (mount the volume)
    - Run df -h (check if mounted)
Make sure the partition formated and mounted then install the service.


## Snapshots:
Before losing data take a snapshot.
    - Go to volumes and click on the volume you want to take snapshot then on actions and click create snapshot in aws console.
    - Right the name then click create snapshot then observe the completed check in snapshots.
    - In instance go to /var/lib/mysql and remoce files as incident assume removed as mistake.
    - First stop mariadb service so it doesnot overwrite anything.
    - The unmount the volume disk: umount /var/lib/mysql
    - fdisk -l
    - df -h
    - Go to aws console name the volume as name-corrupted and detach volume from action button.

    - Go to snapshots mark the snapshot and click create volume from snapshot then fill the required feilds as a volume.
    - When volume created click on this volume and attach volume.
    - Back to shell: mount -a , then it will automatic available check df -h.

## Stress the CPU utilization for cloud Watch
### STRESS Installation
    - Centos
    sudo yum install epel-release -y
    sudo yum install stress -y

    - Amazon Linux 2
    sudo amazon-linux-extras install epel -y
    sudo yum install stress -y

    - Ubuntu
    sudo apt update
    sudo apt install stress -y

    - stress command or script (nohup stress.sh &) to run it in the background.
    stress -c 4 -t 60 && sleep 60 && stress -c 4 -t 60 && sleep 60 && stress -c 4 -t 360 && sleep  && s


## EFS Shared filesystem
    1. Create security-group and add inboud rule (allow from security group of servers ex: sg-xxxx) for instances want to connect to this EFS Filesystem
    2. Open EFS service:
        - Create filesystem keep it general then Next and mount target remove defaults and add the efs-securitygroup created. (make sure to do this otherwise you would not mount to efs).
        - You can access through: IAM or access point, which gonna be use here, create access point on the left under file system.
    3. In instance: (use documentation if needed)
        a. For amazon Linux: 
            - sudo yum install -y amazon-efs-utils
        b. For other system: 
            - sudo yum -y install git
            - git clone https://github.com/aws/efs-utils
            - cd /source-code/path
            - sudo yum -y install make
            - sudo yum -y install rpm-build
            - sudo make rpm (this will make the rpm)
            - sudo yum -y install ./build/amazon-efs-utils*rpm  (this will install the rpm)
            OBS! IT IS LIKE AN ENGINE TO ACCESS THE EFS FILESYSTEM.
        C. For debian ubuntu:
            - sudo yum -y install git
            - git clone https://github.com/aws/efs-utils
            - cd /source-code/path
            - sudo apt-get -y install binutils
            - ./build-deb.sh
            - sudo apt-get -y install ./build/amazon-efs-utils*deb

    4. Using /etc/fstab with efs mount helper to automatically remount EFS file-systems:
        - For using access point: 
            - vi /etc/fstab
            -file-system-id:/ efs-mount-point efs _netdev,noresvport,tls,accesspoint=access-point-id 0 0

        - To automatically mount with IAM authorization to an Amazon EC2 instance that has an instance profile, add the following line to the /etc/fstab file:
            - vi /etc/fstab
            - ADD THIS: file-system-id:/ efs-mount-point efs _netdev,noresvport,tls,iam 0 0

        - To automatically mount with IAM authorization to a Linux instance using a credentials file, add the following line to the /etc/fstab file:
            - vi /etc/fstab
            - ADD THIS: file-system-id:/ efs-mount-point efs _netdev,noresvport,tls,iam,awsprofile=namedprofile 0 0

    5. Run mount -fav  (run this option and if you got access key then you are not following right command wich should not contain iam in the previous options.)
    Otherwise you should see: successfully mounted.
    Run df -h to see the mount filesystem.

