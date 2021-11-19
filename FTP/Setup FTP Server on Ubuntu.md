# How to setup FTP Server on Ubuntu Server 21.10

**FTP** - File Transfer Protocol

By default, it runs on port **21**

In this walkthrough, we will be installing FTPS which runs on port **990**

1. Update System Packages

    `sudo apt update`

2. Install vsftpd

    `sudo apt install vsftpd`

3. Check if the Service is running.

```bash
root@practice:/home/user# systemctl status vsftpd
● vsftpd.service - vsftpd FTP server
     Loaded: loaded (/lib/systemd/system/vsftpd.service; enabled; vendor preset: enabled)
     Active: inactive (dead)
root@practice:/home/user#
```

In our case, the service is _inactive_. We simply need to start the service.
    
    sudo systemctl start vsftpd

Enable it on boot using the following command if it's currently disabled.
    
    sudo systemctl enable vsftpd
    
If all goes well, you should have a similar output:

 ```bash
 root@practice:/home/user# systemctl start vsftpd
root@practice:/home/user# systemctl enable vsftpd
Synchronizing state of vsftpd.service with SysV service script with /lib/systemd/systemd-sysv-install.
Executing: /lib/systemd/systemd-sysv-install enable vsftpd
root@practice:/home/user# systemctl status vsftpd
● vsftpd.service - vsftpd FTP server
     Loaded: loaded (/lib/systemd/system/vsftpd.service; enabled; vendor preset: enabled)
     Active: active (running) since Mon 2021-11-15 13:54:04 UTC; 2min 45s ago
   Main PID: 1716 (vsftpd)
      Tasks: 1 (limit: 1061)
     Memory: 828.0K
     CGroup: /system.slice/vsftpd.service
             └─1716 /usr/sbin/vsftpd /etc/vsftpd.conf

Nov 15 13:54:04 practice.com systemd[1]: Starting vsftpd FTP server...
Nov 15 13:54:04 practice.com systemd[1]: Started vsftpd FTP server.
 ```
 
 # Configuring VSFTPD
 
 By default, the configuration file can be found in `/etc/vsftpd.conf`. We need to make a backup copy of the same before we can start modifying it. This is a good practice when dealing with any config files since you can always revert back to it incase you mess up with the edited version.
 
 ` cp /etc/vsftpd.conf /etc/vsftpd.conf.bak`
 
 <details>
  <summary>Default Config File</summary>
  
  ```bash
    root@practice:/home/user# cat /etc/vsftpd.conf
# Example config file /etc/vsftpd.conf
#
# The default compiled in settings are fairly paranoid. This sample file
# loosens things up a bit, to make the ftp daemon more usable.
# Please see vsftpd.conf.5 for all compiled in defaults.
#
# READ THIS: This example file is NOT an exhaustive list of vsftpd options.
# Please read the vsftpd.conf.5 manual page to get a full idea of vsftpd's
# capabilities.
#
#
# Run standalone?  vsftpd can run either from an inetd or as a standalone
# daemon started from an initscript.
listen=NO
#
# This directive enables listening on IPv6 sockets. By default, listening
# on the IPv6 "any" address (::) will accept connections from both IPv6
# and IPv4 clients. It is not necessary to listen on *both* IPv4 and IPv6
# sockets. If you want that (perhaps because you want to listen on specific
# addresses) then you must run two copies of vsftpd with two configuration
# files.
listen_ipv6=YES
#
# Allow anonymous FTP? (Disabled by default).
anonymous_enable=NO
#
# Uncomment this to allow local users to log in.
local_enable=YES
#
# Uncomment this to enable any form of FTP write command.
#write_enable=YES
#
# Default umask for local users is 077. You may wish to change this to 022,
# if your users expect that (022 is used by most other ftpd's)
#local_umask=022
#
# Uncomment this to allow the anonymous FTP user to upload files. This only
# has an effect if the above global write enable is activated. Also, you will
# obviously need to create a directory writable by the FTP user.
#anon_upload_enable=YES
#
# Uncomment this if you want the anonymous FTP user to be able to create
# new directories.
#anon_mkdir_write_enable=YES
#
# Activate directory messages - messages given to remote users when they
# go into a certain directory.
dirmessage_enable=YES
#
# If enabled, vsftpd will display directory listings with the time
# in  your  local  time  zone.  The default is to display GMT. The
# times returned by the MDTM FTP command are also affected by this
# option.
use_localtime=YES
#
# Activate logging of uploads/downloads.
xferlog_enable=YES
#
# Make sure PORT transfer connections originate from port 20 (ftp-data).
connect_from_port_20=YES
#
# If you want, you can arrange for uploaded anonymous files to be owned by
# a different user. Note! Using "root" for uploaded files is not
# recommended!
#chown_uploads=YES
#chown_username=whoever
#
# You may override where the log file goes if you like. The default is shown
# below.
#xferlog_file=/var/log/vsftpd.log
#
# If you want, you can have your log file in standard ftpd xferlog format.
# Note that the default log file location is /var/log/xferlog in this case.
#xferlog_std_format=YES
#
# You may change the default value for timing out an idle session.
#idle_session_timeout=600
#
# You may change the default value for timing out a data connection.
#data_connection_timeout=120
#
# It is recommended that you define on your system a unique user which the
# ftp server can use as a totally isolated and unprivileged user.
#nopriv_user=ftpsecure
#
# Enable this and the server will recognise asynchronous ABOR requests. Not
# recommended for security (the code is non-trivial). Not enabling it,
# however, may confuse older FTP clients.
#async_abor_enable=YES
#
# By default the server will pretend to allow ASCII mode but in fact ignore
# the request. Turn on the below options to have the server actually do ASCII
# mangling on files when in ASCII mode.
# Beware that on some FTP servers, ASCII support allows a denial of service
# attack (DoS) via the command "SIZE /big/file" in ASCII mode. vsftpd
# predicted this attack and has always been safe, reporting the size of the
# raw file.
# ASCII mangling is a horrible feature of the protocol.
#ascii_upload_enable=YES
#ascii_download_enable=YES
#
# You may fully customise the login banner string:
#ftpd_banner=Welcome to blah FTP service.
#
# You may specify a file of disallowed anonymous e-mail addresses. Apparently
# useful for combatting certain DoS attacks.
#deny_email_enable=YES
# (default follows)
#banned_email_file=/etc/vsftpd.banned_emails
#
# You may restrict local users to their home directories.  See the FAQ for
# the possible risks in this before using chroot_local_user or
# chroot_list_enable below.
#chroot_local_user=YES
#
# You may specify an explicit list of local users to chroot() to their home
# directory. If chroot_local_user is YES, then this list becomes a list of
# users to NOT chroot().
# (Warning! chroot'ing can be very dangerous. If using chroot, make sure that
# the user does not have write access to the top level directory within the
# chroot)
#chroot_local_user=YES
#chroot_list_enable=YES
# (default follows)
#chroot_list_file=/etc/vsftpd.chroot_list
#
# You may activate the "-R" option to the builtin ls. This is disabled by
# default to avoid remote users being able to cause excessive I/O on large
# sites. However, some broken FTP clients such as "ncftp" and "mirror" assume
# the presence of the "-R" option, so there is a strong case for enabling it.
#ls_recurse_enable=YES
#
# Customization
#
# Some of vsftpd's settings don't fit the filesystem layout by
# default.
#
# This option should be the name of a directory which is empty.  Also, the
# directory should not be writable by the ftp user. This directory is used
# as a secure chroot() jail at times vsftpd does not require filesystem
# access.
secure_chroot_dir=/var/run/vsftpd/empty
#
# This string is the name of the PAM service vsftpd will use.
pam_service_name=vsftpd
#
# This option specifies the location of the RSA certificate to use for SSL
# encrypted connections.
rsa_cert_file=/etc/ssl/certs/ssl-cert-snakeoil.pem
rsa_private_key_file=/etc/ssl/private/ssl-cert-snakeoil.key
ssl_enable=NO

#
# Uncomment this to indicate that vsftpd use a utf8 filesystem.
#utf8_filesystem=YES
root@practice:/home/user#

  ```
</details>


When we installed vsftpd, a user **ftp** was installed and has a default ftp directory `/srv/ftp`

```bash
root@practice:/home/user# cat /etc/passwd | grep ftp
ftp:x:114:120:ftp daemon,,,:/srv/ftp:/usr/sbin/nologin
root@practice:/home/user# 
```

Lets create our own FTP User

```bash
root@practice:/home/user# sudo useradd -m bond
root@practice:/home/user# sudo passwd bond
New password:
Retype new password:
passwd: password updated successfully
root@practice:/home/user#
```

Create a user list and add our user to the file

```bash
root@practice:~# echo "bond" | sudo tee -a /etc/vsftpd.userlist
bond
root@practice:~# cat /etc/vsftpd.userlist
bond
```

Create ftp directory in _bond's_ home directory

`sudo mkdir /home/bond/ftp`

Set the ownership using:

`sudo chown nobody:nogroup /home/bond/ftp`

Finally, remove the write permission:

`sudo chmod a-w /home/bond/ftp`

Verify permissions

```bash
root@practice:~# ls -la /home/bond/ftp/
total 8
dr-xr-xr-x 2 nobody nogroup 4096 Nov 15 18:13 .
drwxr-xr-x 3 bond   bond    4096 Nov 15 18:13 ..
root@practice:~# 
```

Create the file-holding directory, assign ownership and add a test file to the directory which will be used when we test everything later on

```bash
root@practice:~# sudo mkdir /home/bond/ftp/files
root@practice:~# sudo chown bond:bond /home/bond/ftp/files/
root@practice:~# echo "You found me" | sudo tee /home/bond/ftp/files/File.txt
You found me
```


We can then proceed to allow FTP traffic on the UFW

```bash
root@practice:/home/user# ufw status
Status: active
root@practice:/home/user# ufw allow 20/tcp
Rule added
Rule added (v6)
root@practice:/home/user# ufw allow 21/tcp
Rule added
Rule added (v6)
root@practice:/home/user# ufw allow 22/tcp
Rule added
Rule added (v6)
root@practice:/home/user# ufw allow 990
Rule added
Rule added (v6)
root@practice:/home/user# ufw allow 40000:50000/tcp
Rule added
Rule added (v6)
root@practice:~# ufw status numbered
Status: active

     To                         Action      From
     --                         ------      ----
[ 1] 22/tcp                     ALLOW IN    Anywhere
[ 2] 20/tcp                     ALLOW IN    Anywhere
[ 3] 21/tcp                     ALLOW IN    Anywhere
[ 4] 40000:50000/tcp            ALLOW IN    Anywhere
[ 5] 990/tcp                    ALLOW IN    Anywhere
[ 6] 22/tcp (v6)                ALLOW IN    Anywhere (v6)
[ 7] 20/tcp (v6)                ALLOW IN    Anywhere (v6)
[ 8] 21/tcp (v6)                ALLOW IN    Anywhere (v6)
[ 9] 40000:50000/tcp (v6)       ALLOW IN    Anywhere (v6)
[10] 990/tcp (v6)               ALLOW IN    Anywhere (v6)
```

By default, FTP doesn’t encrypt data, so we will be using SSL/TLS certificate to secure data transfer. The first step is we need to create the SSL certificate for the Ubuntu FTP server.


```bash
root@practice:/home/user# sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/vsftpd.pem -out /etc/ssl/private/vsftpd.pem
Generating a RSA private key
.......................+++++
........+++++
writing new private key to '/etc/ssl/private/vsftpd.pem'
-----
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [AU]:KE
State or Province Name (full name) [Some-State]:Nairobi
Locality Name (eg, city) []:Nairobi
Organization Name (eg, company) [Internet Widgits Pty Ltd]:Testnet Inc
Organizational Unit Name (eg, section) []:Testnet Inc
Common Name (e.g. server FQDN or YOUR name) []:practice.com
Email Address []:
root@practice:/home/user# ls -la /etc/ssl/private/
total 20
drwx--x--- 2 root ssl-cert 4096 Nov 15 17:31 .
drwxr-xr-x 4 root root     4096 Nov  8 15:23 ..
-rw------- 1 root root     1704 Nov  9 08:27 apache-selfsigned.key
-rw-r----- 1 root ssl-cert 1704 Nov  8 15:57 ssl-cert-snakeoil.key
-rw------- 1 root root     3079 Nov 15 17:32 vsftpd.pem
root@practice:/home/user#
```

I then referred to the manpage, [man 5 vsftpd.conf](https://linux.die.net/man/5/vsftpd.conf) to see what parameters can be included in the configuration file. Here are some of the configuration parameters i included.

```bash
anonymous_enable=NO
local_enable=YES
write_enable=YES
chroot_local_user=YES
chroot_local_user=YES
rsa_cert_file=/etc/ssl/private/vsftpd.pem
rsa_private_key_file=/etc/ssl/private/vsftpd.pem
ssl_enable=YES
allow_anon_ssl=NO
force_local_data_ssl=YES
force_local_logins_ssl=YES
ssl_tlsv1=YES
ssl_sslv2=NO
ssl_sslv3=NO
require_ssl_reuse=NO
ssl_ciphers=HIGH
user_sub_token=bond
local_root=/home/bond/ftp
pasv_min_port=40000
pasv_max_port=50000
userlist_enable=YES
userlist_file=/etc/vsftpd.userlist
userlist_deny=NO
```

Restart the daemon using the following command to load the configuration changes

`systemctl restart vsftpd`

# Testing Connection with FileZilla

Launch FileZilla and click on the Site Manager icon. Then, click the New Site button in the prompted window to enter the Ubuntu FTP server details.

![image](https://user-images.githubusercontent.com/58165365/141836559-e441eb50-5e46-45aa-8811-76d9838512c4.png)

Fill in all the required columns with your newly created Ubuntu FTP server information. Since we configured it to use TLS, we may also choose the Use explicit FTP over TLS option. The final configuration should look like this:

![image](https://user-images.githubusercontent.com/58165365/141836981-f968a824-7073-4055-8108-b77ad7afeffa.png)

Once ready, click Connect, and a screen asking to enter the FTP user’s password will appear. After that, hit OK.

![image](https://user-images.githubusercontent.com/58165365/141837205-d880178e-7da0-4691-bd27-e36038fe3ccc.png)

Finally, you will need to verify the SSL certificate of your FTP server on Ubuntu VPS. After confirming, the root directory with the test file should now appear on your screen.

![image](https://user-images.githubusercontent.com/58165365/141837450-e65b285c-03ae-47c7-829a-e3d2a9d711b5.png)

![image](https://user-images.githubusercontent.com/58165365/141837991-5cf973f3-d3ea-4e19-860f-8f60501ad1d6.png)

That’s all! Now, you can perform various files transfers from your computer to the Ubuntu FTP server and vice versa.

![image](https://user-images.githubusercontent.com/58165365/141839223-85f35b5b-6d24-4523-9fe9-c97920801cb0.png)

![image](https://user-images.githubusercontent.com/58165365/141839297-efc93bae-344f-4887-ab92-f2e4558b9688.png)




