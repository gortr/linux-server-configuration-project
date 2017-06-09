# Linux Configuration Project
**Author:** Rigoberto Gort
**Date:** 06/07/2017

## Project Info
**Filename:** README.md

**Main Project File:** `README.md`

**IP Address:** `http://13.58.216.33`

**Accessible SSH Port:** `2200`

## Software Installed

**Installed Software:** `Git` : for accessing the server remotely, `Ubuntu` : which was installed on the remote server via Amazon Lightsail, `Python` : used to import the Python packages below in order to serve the Item Catalog project to the web IP address listed above.

**Installed Python Packages:** `sqlalchemy`, `flask`, `pip`, `requests`, `oauth2client`

## Summary of Configurations

This was a long and detailed learning experience for me as I completed this project. Let's not keep you waiting any longer, join me on how the adventure unfolded!

### 1 - Initial Login

1. I logged in as the default user for my instance of Ubuntu via Amazon Lightsail utilizing the following command,

`ssh ubuntu@13.58.216.33 -p 2200 -i ~/.ssh/LightsailDefaultPrivateKey-us-east-2.pem`

The above terminal command indicates that I would like to connect via ssh as said intended user of the servers ip address. The `-p` command indicates I want to connect to a specific port which includes the port number after it. The command including the `-i` and after it indicate that I would like to authenticate my access to the server via a private key. The private key is then matched to the servers existing public key to confirm I am who I say I am. Once authenticated I gain access to the server remotely.

2. Updated all currently installed packages

I immeadiately proceeded to updating all the server installed packages so they would be up to date for my usage in the application deployment process. I used the command `sudo apt-get upgrade` and proceeded to install the utility software called finger. To install finger I just had to run the command `apt-get install finger`. One could also run the previous command with the `sudo` prefix command as well just in case.

3. Create a new user with sudo permissions and with a key-based authentication? No problem!



### x - Final Checks and Restarts

x. After verifying that all was working appropriately and as per project specifications I then proceeded to restart the Apache service.

## Third Party Resources Utilized

- https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps
- https://help.ubuntu.com/community/Sudoers
- https://askubuntu.com/questions/500977/ah00015-unable-to-open-logs-action-start-failed-in-ubuntu-14-04
- https://askubuntu.com/questions/256013/apache-error-could-not-reliably-determine-the-servers-fully-qualified-domain-n
- https://unix.stackexchange.com/questions/155150/where-in-apache-2-do-you-set-the-servername-directive-globally
- https://www.digitalocean.com/community/tutorials/how-to-create-a-sudo-user-on-ubuntu-quickstart
- https://stackoverflow.com/questions/201893/warning-unprotected-private-key-file-when-trying-to-ssh-into-amazon-ec2-instan#217729
- https://discussions.udacity.com/t/solved-cant-log-in-as-grader/249191
- https://stackoverflow.com/questions/3710946/copying-files-across-computers-using-ssh-and-mac-os-x-terminal
- https://discussions.udacity.com/t/deploying-flask-appication-in-ubuntu/247292
- http://docs.sqlalchemy.org/en/latest/core/engines.html
- https://www.devopsderek.com/blog/2012/11/13/list-and-disconnect-postgresql-db-sessions/