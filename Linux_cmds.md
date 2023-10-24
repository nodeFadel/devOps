## Best Linux commands:
- sed -i 's/word-to-change/new-word/g'  <file-name>
- (-F) very nice for logging ex: tail -F <filename> (to see the direct changes not just printing last lines).

- awk -F':' '{print $1}' /etc/passwd  AS  cut -d: -f1 <finename>
- grep <text> * -R  (command will search for the text in all the file, in all the subdirectories).

- ls -ltr (ls with tha last updated file reversely).
- free -m  (show the memory utilization).
- df -h (harddisk partition utilization).
- cat freeeee -m &>> error.log (redirect the output and error to the same file as: 2>&1 error.log).
- usermod -aG devops ansible  ( add user:ansible to a the group called devops).
- vim /etc/groups (add the users directly to the group separated with comma).
- lsof -u <user> (will show the files oppened by this user).
- su - <username> (To switch between users in Linux cli).
- Top Process:
    - Show all processes with their parent process id: ps -ef
    - kill -9 PID command is to stop process forcefully and kill PID  is to stop process gracefully
    - ps -ef | grep httpd | grep -v 'grep'
    - kill <process-pid>
    - If the root process killed but not the child process:
        - ps -ef | grep < ex: httpd > | grep -v 'grep' | awk '{print $2}' | xargs kill -9
    
### A child process that remains running even after its parent process is terminated or    completed without waiting for the child process execution is called a Orphan process. Zombie process that has completed its task but still, it shows an entry in a process table.

- Archiving:
    - Test with tar:
        - Compress: tar -czvf (copy,compress,verbose, file) jenkins_timestamp06122020.tar.gz jenkins
        - Extract: tar -xzvf messages_archiv_testing_22102023.tar.gz
        - Extract in another place: tar -xzvf messages_archiv_testing_22102023.tar.gz -C /opt/

    - Test with zip and unzip instead for tar:
        - first: yum install zip unzip -y
        - Compress: zip -r messages_22102023.zip messages
        - Extract: unzip messages_22102023.zip 

# Ubuntu Commands
- adduser <user-name>
- If you want to edit visudo then nano shell would be open, do this: export EDITOR=vim
that would open edited file everytime with vim but if you logged out-in then do it again.
- To download the tree package for debian package for ubuntu:
    - wget http://archive.ubuntu.com/ubuntu/pool/universe/t/tree/tree_1.8.0-1_amd64.deb
    - ls to see yhe package then install it with: dpkg -i <package>
    - tree (to list the current directory files in a tree shape).
    - dpkg -l (all the debian list in your machine).
    - dpkg -l | grep tree
    - dpkg -r tree  (to remove the package).
    - In redhat machine there is yum while here there is apt:
        - cd /etc/apt/   --> cat source.list (to see the repository information).
        - apt search tree (to search for package name tree)
        - apt install apache2 (instad of httpd in redhat)
        - remove clean with iformationa and data: apt purge apache2
## The biggest difference between the two Linux distribution is that Ubuntu is based on the Debian architecture while CentOS is forked from Red Hat Enterprise Linux.

## apt command in ubuntu and yum in centos to manage packages
## Default text editor in ubuntu is nano, if you want to temporarily set vim as default editor then run below command. export EDITOR=vim