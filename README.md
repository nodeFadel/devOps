# devOps
Creating, developing and monitoring CI/CD for my project.

## Starting my new project with different tools and aws as well.
This project coming soon after finishing my epics preparation and planning...

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