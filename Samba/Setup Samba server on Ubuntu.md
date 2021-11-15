# Installing Samba on Ubuntu 21.10 Server

Fire up your terminal and update packages

`sudo apt update -y`

To install Samba, run the following command

`sudo apt install samba`

We can check if the installation was successful by running:

`whereis sammba`

The output should be almost similar

```python
root@practice:/home/user# whereis samba
samba: /usr/sbin/samba /usr/lib/x86_64-linux-gnu/samba /etc/samba /usr/share/samba /usr/share/man/man8/samba.8.gz /usr/share/man/man7/samba.7.gz
root@practice:/home/user#
```

We’ll create the following Samba shares and users.

Users;

- Manager
  - Michael Scott
  - Jan Levinson
- Sales
  - Jim Halpert
  - Dwight Schrute
  - Andrew Bernard
- Hr
  - Toby Flanderson
- Finance
  - Kevin Malone
  - Angel
  - Oscar

Samba Share
- Confidential _This share will be accessible with read/write permissions by Manager group + Hr group 
- Movies _This share will be accessible with read/write permissions by all users.
- Finance _This share will be accessible with read/write permissions by Finance group + Manager group
- Sales _This share will be accessible with read/write permissions by Sales group. + Manager group
