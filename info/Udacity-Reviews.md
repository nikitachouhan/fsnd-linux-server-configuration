# Project Review

- [Review 1](#review-1)
- [Review 2](#review-2)
- [Suggestions](#suggestions)

## Review 1

### Requires Changes

4 specifications require changes
Dear student,

I've really enjoyed reviewing your server, Please check and update the packages and provide sudo password to check the missing rubric items.

Keep learning and good luck! :)

### User Management

- You cannot log in as root remotely.

Looks like your SSH is configured to only prohibit log in as root with passwords, with the option 'prohibit-password'.
The safer option would be 'no'.
take a look at the sshd_config [documentation](http://manpages.ubuntu.com/manpages/xenial/en/man5/sshd_config.5.html).
Also here is a great [guide](https://www.digitalocean.com/community/tutorials/how-to-tune-your-ssh-daemon-configuration-on-a-linux-vps) explaining a little about sshd_config.

```sh
grader@ip-172-26-15-157:~$ cat /etc/ssh/sshd_config | grep PermitRootLogin
PermitRootLogin prohibit-password
# the setting of "PermitRootLogin without-password".
```

- The SSH key submitted with the project can be used to log in as grader on the server.

Awesome! Your SSH key works perfectly!

```sh
$ ssh -i ~/.ssh/grader.rsa -p 2200 grader@3.6.92.91
Welcome to Ubuntu 16.04.4 LTS (GNU/Linux 4.4.0-1052-aws x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud
```

- The grader user can run commands using sudo to inspect files that are readable only by root.

sudo password need to be provided to test if grader can run sudo commands normally.

```sh
grader@ip-172-26-15-157:~$ ll /root/
ls: cannot open directory '/root/': Permission denied
grader@ip-172-26-15-157:~$ sudo ll /root/
[sudo] password for grader:
sudo: ll: command not found
```

### Security

- Only allow connections for SSH (port 2200), HTTP (port 80), and NTP (port 123).

sudo password is not provided or ufw is not installed on your instance.

```sh
grader@ip-172-26-15-157:~$ sudo ufw status
[sudo] password for grader:
sudo: ufw: command not found
```

Install ufw by typing :

```sh
sudo apt-get install ufw
```

Read more: [How To Set Up a Firewall with UFW on Ubuntu 18.04](https://www.digitalocean.com/community/tutorials/how-to-set-up-a-firewall-with-ufw-on-ubuntu-18-04)

- Key-based SSH authentication is enforced.

Perfect!

```sh
$ ssh -p 2200 grader@3.6.92.91
grader@3.6.92.91: Permission denied (publickey).
```

- All system packages have been updated to most recent versions.

There are 17 packages to be updated. I know apt-get update and apt-get upgrade commands can be a little confusing, so I'll leave [this link](https://askubuntu.com/questions/94102/what-is-the-difference-between-apt-get-update-and-upgrade) here to clarify any doubt you might have.

17 packages can be updated.
2 updates are security updates.

- SSH is hosted on non-default port.

SSH is hosted on port 2200!

```sh
grader@ip-172-26-15-157:~$ cat /etc/ssh/sshd_config | grep Port
Port 2200
```

## Review 2

### Meets Specifications

This is a great submission and an excellent work ! :clap:
Great job addressing previous reviewer's comments.

You have done a fantastic job!
Congratulations!
You've successfully completed the project.
Good luck with the next project!

Please, keep up the great work and have a nice day! :clap:

#### Issue with Google Sign-in

When you set up OAuth for your application, you will need a DNS name
that refers to your instance's IP address.

#### Configuring DNS

To get a hostname or a domain name on your machine, you can sign up for any free service,
as [No-IP](https://www.noip.com/), and register the IP with the desired hostname and any of the free domain names available.

### User Management

- You cannot log in as root remotely.

:white_check_mark: Inspecting /etc/ssh/sshd_config file:

```sh
grader@ip-172-26-15-157:~$ grep -i root /etc/ssh/sshd_config
PermitRootLogin no
# the setting of "PermitRootLogin without-password".
grader@ip-172-26-15-157:~$
```

Login as root remotely is not allowed!
Well done! :clap:

- The SSH key submitted with the project can be used to log in as grader on the server.

```sh
$ ssh -p 2200 grader@3.6.92.91 -i grader-priv.rsa
Welcome to Ubuntu 16.04.7 LTS (GNU/Linux 4.4.0-1128-aws x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

17 packages can be updated.
2 updates are security updates.


Last login: Sat Sep 11 16:02:47 2021 from 182.70.22.28
grader@ip-172-26-15-157:~$
```

Great job! :thumbsup:
I can login as "grader" user with the provided SSH private key! :clap:

- The grader user can run commands using sudo to inspect files that are readable only by root.

```sh
grader@ip-172-26-15-157:~$ ufw status
ERROR: You need to be root to run this script
grader@ip-172-26-15-157:~$
```

```sh
grader@ip-172-26-15-157:~$ sudo ufw status
[sudo] password for grader:
Status: active

To                         Action      From
--                         ------      ----
2200/tcp                   ALLOW       Anywhere
80/tcp                     ALLOW       Anywhere
123/udp                    ALLOW       Anywhere
2200/tcp (v6)              ALLOW       Anywhere (v6)
80/tcp (v6)                ALLOW       Anywhere (v6)
123/udp (v6)               ALLOW       Anywhere (v6)

grader@ip-172-26-15-157:~$
```

:clap: grader user can run sudo commands!
Great job!

[How to Control sudo Access on Linux](https://www.howtogeek.com/447906/how-to-control-sudo-access-on-linux/)
The sudo command lets you run commands on Linux as though you were someone else, such as root.

### Security

- Only allow connections for SSH (port 2200), HTTP (port 80), and NTP (port 123).

```sh
grader@ip-172-26-15-157:~$ sudo ufw status
[sudo] password for grader:
Status: active

To                         Action      From
--                         ------      ----
2200/tcp                   ALLOW       Anywhere
80/tcp                     ALLOW       Anywhere
123/udp                    ALLOW       Anywhere
2200/tcp (v6)              ALLOW       Anywhere (v6)
80/tcp (v6)                ALLOW       Anywhere (v6)
123/udp (v6)               ALLOW       Anywhere (v6)

grader@ip-172-26-15-157:~$
```

Excellent! Your firewall is perfect! :thumbsup:
UFW, or Uncomplicated FireWall, is a frontend for managing firewall rules in Linux.
Actually, ufw is a command line front-end for iptables.

[How to Configure a Firewall with UFW](https://www.linode.com/docs/guides/configure-firewall-with-ufw/)

- All system packages have been updated to most recent versions.

:white_check_mark: This is the message when logging into the server:

17 packages can be updated.
2 updates are security updates.
:warning: This means there are some packages (17) to be updated.

However, the welcome message may be not precisely.
So I used the command below to check the system updates:

```sh
grader@ip-172-26-15-157:~$ apt-get upgrade --dry-run
NOTE: This is only a simulation!
      apt-get needs root privileges for real execution.
      Keep also in mind that locking is deactivated,
      so don't depend on the relevance to the real current situation!
Reading package lists... Done
Building dependency tree
Reading state information... Done
Calculating upgrade... Done
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
grader@ip-172-26-15-157:~$
```

:bulb: --dry-run argument: it won't actually do anything. It'll just tell you what it would do.
In this case, it would upgrade 0 packages.
:arrow_up: No upgrade needed: 0 upgraded, ...

Also, I can see in your server history that you got them updated:

```sh
grader@ip-172-26-15-157:~$ history|egrep "update|upgrade|apt"
   ...
  851  sudo apt install software-properties-common
  852  sudo add-apt-repository ppa:jonathonf/python-3.6
  927  sudo apt install ufw
  938  sudo apt-get update
  939  sudo apt-get upgrade
   ...
```

Finally Check for [unattended-upgrades](https://help.ubuntu.com/community/AutomaticSecurityUpdates)
:white_check_mark: Another mechanism to verify system packages upgradable is unattended-upgrades.
:bulb: The purpose of unattended-upgrades is to keep the computer
current with the latest security (and others) updates automatically.

:white_check_mark: Test for unattended-upgrade installed

```sh
grader@ip-172-26-15-157:~$ dpkg --get-selections | grep unattended-upgrade
unattended-upgrades                             install
grader@ip-172-26-15-157:~$
```

:thumbsup: unattended-upgrade is installed!
Great job using unattended-upgrades! :thumbsup:

[How do you see what packages are available for update](https://askubuntu.com/questions/99834/how-do-you-see-what-packages-are-available-for-update)

## Suggestions

1. When you set up OAuth for your application, you will need a DNS name
   that refers to your instance's IP address.

   Configuring DNS
   To get a hostname or a domain name on your machine, you can sign up for any free service,
   as No-IP, and register the IP with the desired hostname and any of the free domain names available.

Great job providing access information :clap:

- Server public IP: 3.6.92.91
- URL: http://3.6.92.91/
- App directory (WSGIScript File web root path): /var/www/catalog/ :arrow_left:
  ```sh
  grader@ip-172-26-15-157:~$ grep -i WSGIScriptAlias /etc/apache2/sites-enabled/catalog.conf
  WSGIScriptAlias / /var/www/catalog/wsgi.py
  grader@ip-172-26-15-157:~$
  ```
- 'grader' SSH private key :thumbsup:
- SSH port: 2200 (This is NOT a default port): :thumbsup:
- SSH private key passphrase (empty in this case)
- 'grader' user password: grader

sudo setup

```sh
grader@ip-172-26-15-157:~$ sudo grep grader /etc/sudoers.d/grader
grader ALL=(ALL:ALL) ALL
grader@ip-172-26-15-157:~$
```

- 'grader' user is in '/etc/sudoers.d/grader' file :arrow_right: grader ALL=(ALL:ALL) ALL

- 'grader' user is not in sudo group:

```sh
    grader@ip-172-26-15-157:~$ id grader
    uid=1001(grader) gid=1001(grader) groups=1001(grader)
    grader@ip-172-26-15-157:~$
```

The sudo command lets you run commands on Linux as though you were someone else, such as root.
