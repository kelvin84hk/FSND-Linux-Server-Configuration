# Project 3 : Linux Server Configuration

## Project Details:

This project will take a baseline installation of a Linux server and prepare it to host web applications. It will secure the server from a number of attack vectors, install and configure a database server, and deploy one of the existing web applications onto it.

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

10. Continue in grader ternimal, run `chmod 700 .ssh` and `chmod 644 .ssh/authorized_keys` to change read write acess.

### Change ssh port, disable passowrd login and ssh login for root user

11. Run `sudo nano /etc/ssh/sshd_config`

12. Change the port from 22 to 2200

13. Change value of PermitRootLogin to `no`

14. Change value of PasswordAuthentication to `no`

16. Save and exit. Then run `sudo service ssh restart`

17. Goto Amazon Light Sail Instance page and then Networking, change port range allowed to 2200 and delete 22

18. Restart the ternimal and ssh in via `ssh grader@13.229.112.119 -p 2200 -i ~/.ssh/udacity`

### Configure the Uncomplicated Firewall (UFW)

19. Run commands :

```
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow ssh
sudo ufw allow 2222/tcp
sudo ufw allow www
sudo ufw enable
```
