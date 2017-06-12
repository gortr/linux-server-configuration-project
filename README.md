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

I logged in as the default user for my instance of Ubuntu via Amazon Lightsail utilizing the following command,

`ssh ubuntu@13.58.216.33 -p 22 -i ~/.ssh/LightsailDefaultPrivateKey-us-east-2.pem`

The above terminal command indicates that I would like to connect via ssh as said intended user of the servers ip address. The `-p` command indicates I want to connect to a specific port which includes the port number after it. The command including the `-i` and after it indicate that I would like to authenticate my access to the server via a private key. The private key is then matched to the servers existing public key to confirm I am who I say I am. Once authenticated I gain access to the server remotely.

### 2 - Updated all currently installed packages

I immeadiately proceeded to updating all the server installed packages so they would be up to date for my usage in the application deployment process. I used the command `sudo apt-get upgrade` and proceeded to install the utility software called finger. To install finger I just had to run the command `apt-get install finger`. One could also run the previous command with the `sudo` prefix command as well just in case.

### 3 - Create a new user with sudo permissions and with a key-based authentication? No problem!

I had to create the grader user via the `sudo adduser grader` command. After that I needed to update that user's permissions to allow sudo commands. I achieved that via the `usermod -aG sudo grader` command. This added them to the sudo group. In order to confirm the change I had to `su grader` in order to test their sudo permissions.

Once I confirmed the sudo permissions I created a key for the grader user.The `ssh-keygen` command allowed me to create a key pair for the grader user. I then had to create an /home/grader/.ssh directory to store the public key on the server. I also had to create the `authorized_keys` file in order to store the public key on the server for the grader user.

After that I had to make sure anyone could just access the directory of file freely. This was achieved with the following commands.

`sudo chmod 700 /home/grader/.ssh` which made it only accessible by admin/sudo users.

`sudo chmod 644 /home/grader/.ssh/authorized_keys` made sure that the file could still be read for authentication purposes but nothing more such as editing without sudo permissions.

### 4 - Altering the SSH port

I had to acces to configuration file that stored the SSH port.

`sudo nano /etc/ssh/sshd_config`

I had to the port listed as 22 and alter it to 2200 followed by restarting the ssh service.

`sudo service ssh restart`

To verify everything took effect I disconnected from the server and reconnected with the following command.

`ssh grader@13.58.216.33 -p 2200 -i ~/.ssh/lightsail.pem`

Everything checks out so let's move on to the next task.

### 5 - Disabling root user ssh login

For this process I had already disabled password logins but changed the sshd_config file to indicate `PermitRootLogin no`. Without the private key no user would be able to gain access as the root aka primary user anyways but better to be safe than sorry.

We would then, once again, run `sudo service ssh restart` and test our changes.

### 6 - Configure the firewall settings

Per the guidelines I needed to configure the firewall to allow certain ports access to requests.

- `sudo ufw allow 2200/tcp`
- `sudo ufw allow 80/tcp`
- `sudo ufw allow 123/udp`
- `sudo ufw enable`
- `sudo ufw status` to confirm everything is setup correctly and online

### 7 - Install Apache and WSGI and setup Flask App folders/files

1. `sudo apt-get install apache2`
2. `sudo apt-get install libapache2-mod-wsgi`
3. `sudo a2enmod wsgi`
4. `sudo service apache2 start`
5. `cd /var/www`
6. `sudo mkdir FlaskApp`
7. `cd FlaskApp`
8. `sudo mkdir FlaskApp`
9. `cd FlaskApp`
10. `sudo mkdir static templates`
11. `sudo nano __init__.py`
12. For the __init__.py file you must add the following,

from flask import Flask
app = Flask(__name__)
@app.route("/")
def hello():
	return "Hello, I love Digital Ocean!"
if __name__ == "__main__":
	app.run()

### 8 - Installing Flask and virtual environment

1. `sudo apt-get install python-pip`
2. `sudo pip install virtualenv`
3. `sudo virtualenv venv`
4. `source venv/bin/activate`
5. `sudo pip install Flask`
6. `sudo python __init__.py`
- In order to leave the virtual environment you would type `deactivate`
7. `sudo nano /etc/apache2/sites-available/FlaskApp.conf`
8. The FlaskApp.conf file will require the following,

```
<VirtualHost *:80>
		ServerName mywebsite.com
		ServerAdmin admin@mywebsite.com
		WSGIScriptAlias / /var/www/FlaskApp/flaskapp.wsgi
		<Directory /var/www/FlaskApp/FlaskApp/>
			Order allow,deny
			Allow from all
		</Directory>
		Alias /static /var/www/FlaskApp/FlaskApp/static
		<Directory /var/www/FlaskApp/FlaskApp/static/>
			Order allow,deny
			Allow from all
		</Directory>
		ErrorLog ${APACHE_LOG_DIR}/error.log
		LogLevel warn
		CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

9. `sudo a2ensite FlaskApp`
10. `cd /var/www/FlaskApp`
11. `sudo nano flaskapp.wsgi`
12. The flaskapp.wsgi file must contain the following,

```python
#!/usr/bin/python
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0,"/var/www/FlaskApp/")

from FlaskApp import app as application
application.secret_key = 'Add your secret key'
```

13. `sudo service apache2 restart`

### 9 - Installing PostgreSQL

The PostgreSQL was fun to setup. I also learned how to remove connections to the database along with removing them from the system. One could say that I had accidently setup the intended one without a user/owner, etc.

1. `sudo apt-get install postgresql`
2. `sudo su postgres`
3. `psql`
4. `create user rigo;`
5. `\password rigo;`
6. `create database itemcatalog with owner rigo;`
7. `\c itemcatalog`
8. `\q` in order to quit out of postgresql
9. Updated my itemcatalog project files, now named as `__init__.py`, with the following line of code,

```
engine = create_engine('postgresql://rigo:rigo@localhost/itemcatalog')
```

### 10 - Update Google OAuth Settings

1. Updated the settings to permit the project IP Address via the Google Developer Console.
2. Updated the client_secrets.json file to reflect the addition of the IP Address/URL.

### 11 - Update Item Catalog Project Files

1. Updated all python files/scripts that made requests to a database. Verified they reflected the change over to PostgreSQL from SQLite3.
2. Updated the client authorized section of code that pertained to the oauth for google users.
3. Afterwards `sudo service apache2 restart`
4. Confirmed all code functioned accordingly.

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