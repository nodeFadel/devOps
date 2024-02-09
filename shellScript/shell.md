# User specific aliases and functions
alias rm='rm -i'
alias cp='cp -i'
alias mv='mv -i'
alias k='kubectl'
alias img='kubectl get images'
alias containers='kubectl get containers'

## To add substitution variable
VarName=`Command`
VarName=$(Command)

SEASON="Monsoon"
echo $SEASON 

when you do EXIT AND SSH AGAIN with 'sudo -i' new shell terminal will open
so we need to make this command permenant whenever the shell exit and enter again.
In every home directory there is hidden file called .bashrc
add: export SEASON='Summer'

if you want to make it permanent for all users edit cd /etc/profile and add: export SEASON='Winter' 

but! remember if the user edited the .bashrc inside his profile it will take it and not the etc/profile but if nothing in .bashrc for specific user it will take the general for all profiles.


## User Input
How to make the script interactive take the input from the user and then execute the script.
read is used to get the input and substitute in a variable ex: read SKILL
-p prompt for username
-sp prompt for password


## Decision making if-else

if [<some text>]
then
  <commands>
  echo "Seuccessfully completed."

else
  <commands>
  echo "Sorry, Try again later."

fi


# Make the script on regular basis, checking on time
    >> crontab -e (-e to edit)
            ## MM HH DOM mm DOW COMMAND 
            30 20 * * 1-5 COMMAND
            * * * * * /opt/scripts/11_monit.sh &>> /var/log/monit_httpd.log
            MM minutes
            HH hours
            DOM day of month 1, 2, 3, .. 31
            mm Jan - Feb..Dec
            DOW Sat, Mon, ..Sun


# How to install or troubleshoot chain of VirtualMachine with one code line
    - You have [scriptbox, web01, web02, web03]
    - Create publi/private key in scriptbox and ssh to every vm ex: here we have web01,web02,web03, then copy the public key (the Lock) to every Vmachine.
    - Create a folder in sriptbox Vmachine: mkdir remote_setup 
    - Then cd remote_setup and create file to Loop on the Vmachines ex: vim Vm_setup >> web01 web02 web03
    - With this code line: 
        [root@scriptbox remote_websetup]# for host in `cat Vm_setup`; do ssh devops@$host hostname;done
    - This will loop over the VM's and execute the desired setup.