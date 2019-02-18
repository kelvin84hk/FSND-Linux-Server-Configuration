# Project 3 : Linux Server Configuration

## Project Details:

This project will take a baseline installation of a Linux server and prepare it to host web applications. It will secure the server from a number of attack vectors, install and configure a database server, and deploy the Catalog web application in previous project onto it.

## Instance Created:

IP address: 13.229.112.119

SSH port: 2200

Application URL: http://13.229.112.119.xip.io

## Steps:

### Create a new user named grader and give it the permission to sudo

1. Run `sudo adduser grader`

2. Run `cp /etc/sudoers.d /etc/sudoers.d/grader` and then `sudo nano /etc/sudoers.d/grader`

3. Insert `grader ALL=(ALL) NOPASSWD:ALL` into the file. Then save and exit.

4. Open a new ternimal and ssh in as `grader` to continue.

### Update all currently installed packages

5. Run `sudo apt-get update` to download package lists

6. Run `sudo apt-get upgrade` to fetch new versions of packages 

### Configure key-based authentication for grader user

7. Oepn a new ternimal and run `ssh-keygen` to generate public and private keys and name it udacity

8. In grader ternimal, run `mkdir .ssh` and then `nano .ssh/authorized_keys`

9. In the new ternimal, run `cat .ssh/udacity.pub` and copy paste the contnet to the grader ternimal. Then save and exit.

10. Continue in grader ternimal, run `chmod 700 .ssh` and `chmod 644 .ssh/authorized_keys` to change access rights.

### Change ssh port, disable passowrd login and ssh login for root user

11. Run `sudo nano /etc/ssh/sshd_config`

12. Change the port from 22 to 2200

13. Change value of PermitRootLogin to `no`

14. Change value of PasswordAuthentication to `no`

16. Save and exit. Then run `sudo service ssh restart`

17. Goto Amazon Light Sail Instance page and then Networking, change port range allowed to 2200 and delete 22 under Firewall

18. Restart the ternimal and ssh in via `ssh grader@13.229.112.119 -p 2200 -i ~/.ssh/udacity`

### Configure the Uncomplicated Firewall (UFW)

19. Run commands :

```
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow 2200/tcp
sudo ufw allow 123/udp
sudo ufw allow 80/tcp
sudo ufw enable
```

### Install Apache

20. Run `sudo apt-get install apache2`

### Install mod_wsgi

21. Run 'sudo apt-get install libapache2-mod-wsgi python-dev'

22. Run `sudo a2enmod wsgi` to enable mod_wsgi

23. Run `sudo service apache2 start` to start the web server

### Install Flask and other dependencies

24. Run `sudo apt-get install python-pip` to install pip

25. Run `pip install Flask` to install Flask

26. Run `sudo pip install httplib2 oauth2client sqlalchemy psycopg2 sqlalchemy_utils requests` to install other dependencies

### Install and configure PostgreSQL

27. Run `sudo apt-get install libpq-dev python-dev`

28. Run `sudo apt-get install postgresql postgresql-contrib`

29. Run `sudo su - postgres`

30. Run `psql`

31. Run `CREATE USER catalog WITH PASSWORD 'catalog';`

32. Run `ALTER USER catalog CREATEDB;`

33. Run `CREATE DATABASE catalog WITH OWNER catalog;`

34. Run `\c catalog`

35. Run `REVOKE ALL ON SCHEMA public FROM public;`

36. Run `GRANT ALL ON SCHEMA public TO catalog;`

37. Run `\q`

38. Run `exit`

#### Deploy the Catalog App

39. Run `cd /var/www/`

40. Run `sudo mkdir catalog`

41. Run `sudo chown -R grader:grader catalog`

42. In the Catalog App folder of the local comuputer, change `server.py` to `__init__.py`

43. Open `__init__.py` and change the value of `google_client_secrets_path` to `'/var/www/catalog/catalog/client_secrets.json'`

44. Change argument of `create_engine` line to `'postgresql://catalog:catalog@localhost/catalog'`. Then save and exit.

45. Open `database_setup.py` and change argument of `create_engine` line to `'postgresql://catalog:catalog@localhost/catalog'`. Then save and exit.

46. Goto console.developers.google.com. In Oauth consent screen of the Credentials, add `xip.io` under authorised domains. Then add `http://13.229.112.119.xip.io:80` in Authorised JavaScript origins and `http://13.229.112.119.xip.io:80/catalog` in Authorised redirect URIs. Then download json to replace the `client_secrets.json` file.

47. Download and install FileZilla Client from https://filezilla-project.org/download.php

48. Open FileZilla then goto Edit -> Settings -> SFTP and click Add key file to add the private key (udacity in /.ssh) generated before.

49. Then fill Host: sftp://13.229.112.119 , Username: grader, Port: 2200 and click `Quickconnect` to connect to the server.

50. In Local site goto the path of the Catalog folder and in Remote site, type `/var/www/catalog/`

51. Upload the local Catalog folder to remote site. 
    
     ![alt text](https://github.com/kelvin84hk/FSND-Linux-Server-Configuration/blob/master/filezilla.jpg)

52. Go back to grader terminal and run `sudo nano /etc/apache2/sites-available/catalog.conf`

53. Copy paste code below. Then save and exit.

```
<VirtualHost *:80>
		ServerName 13.229.112.119
		WSGIScriptAlias / /var/www/catalog/catalog.wsgi
		<Directory /var/www/catalog/catalog/>
			Order allow,deny
			Allow from all
		</Directory>
		Alias /static /var/www/catalog/catalog/static
		<Directory /var/www/catalog/catalog/static/>
			Order allow,deny
			Allow from all
		</Directory>
		
</VirtualHost>
```

54. Run `sudo a2ensite catalog`

55. Run `cd /var/www/catalog/`

56. Run `sudo nano catalog.wsgi`. Copy paste code below and then save and exit.

```
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0,"/var/www/catalog/")

from catalog import app as application
application.secret_key = 'super_secret_key'
```

57 run `sudo service apache2 restart` to restart Apache.

58 Visit the site at http://13.229.112.119.xip.io/
