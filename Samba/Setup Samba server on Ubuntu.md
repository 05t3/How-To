# Installing Samba on Ubuntu 21.10 Server

Fire up your terminal and update packages

`sudo apt update -y`

To install Samba, run the following command

`sudo apt install samba`

We can check if the installation was successful by running:

`whereis samba`

The output should be almost similar

```python
root@practice:/home/user# whereis samba
samba: /usr/sbin/samba /usr/lib/x86_64-linux-gnu/samba /etc/samba /usr/share/samba /usr/share/man/man8/samba.8.gz /usr/share/man/man7/samba.7.gz
root@practice:/home/user#
```

We’ll create the following Samba shares and users.

Users;

- Manager
  - michael
  - jan
- Sales
  - jim
  - dwight
  - andrew
- Hr
  - toby
- Finance
  - kevin
  - angela
  - oscar

Samba Share
- Confidential _This share will be accessible with read/write permissions by Manager group + Hr group_ 
- Movies _This share will be accessible with read/write permissions by all users._
- Finance _This share will be accessible with read/write permissions by Finance group + Manager group_
- Sales _This share will be accessible with read/write permissions by Sales group. + Manager group_


After installing Samba, we need to make directories which we will share. In this case, we will create directories in the `/opt` directory.

```bash
root@practice:~# cd /opt/
root@practice:/opt# ls -la
total 8
drwxr-xr-x  2 root root 4096 Oct 22  2020 .
drwxr-xr-x 20 root root 4096 Nov 15 21:00 ..
root@practice:/opt# mkdir Confidential Movies Finance Sales HR
root@practice:/opt# ls -la
total 24
drwxr-xr-x  6 root root 4096 Nov 15 22:35 .
drwxr-xr-x 20 root root 4096 Nov 15 21:00 ..
drwxr-xr-x  2 root root 4096 Nov 15 22:35 Confidential
drwxr-xr-x  2 root root 4096 Nov 15 22:35 Finance
drwxr-xr-x  2 root root 4096 Nov 15 22:35 Movies
drwxr-xr-x  2 root root 4096 Nov 15 22:35 Sales
drwxr-xr-x  2 root root 4096 Nov 15 22:35 HR

```

Create groups as follows:

```bash
root@practice:/opt# groupadd HR
root@practice:/opt# groupadd Sales
root@practice:/opt# groupadd Finance
root@practice:/opt# groupadd Managers
```

Verify the groups were added successfully:

```bash
user@practice:/opt$ cat /etc/group
HR:x:1006:
Sales:x:1007:
Finance:x:1008:
Managers:x:1009:
```

Lets now create our users who we'll add to their respective groups.

```bash
root@practice:/home/user# useradd michael
root@practice:/home/user# passwd michael
New password:
Retype new password:
passwd: password updated successfully
root@practice:/home/user# useradd jan
root@practice:/home/user# passwd jan
New password:
Retype new password:
passwd: password updated successfully
root@practice:/home/user# useradd jim
root@practice:/home/user# passwd jim
New password:
Retype new password:
passwd: password updated successfully
root@practice:/home/user# useradd dwight
root@practice:/home/user# passwd dwight
New password:
Retype new password:
passwd: password updated successfully
root@practice:/home/user# useradd andrew
root@practice:/home/user# passwd andrew
New password:
Retype new password:
passwd: password updated successfully
root@practice:/home/user# useradd toby
root@practice:/home/user# passwd toby
New password:
Retype new password:
passwd: password updated successfully
root@practice:/home/user# useradd kevin
root@practice:/home/user# passwd kevin
New password:
Retype new password:
passwd: password updated successfully
root@practice:/home/user# useradd angela
root@practice:/home/user# passwd angela
New password:
Retype new password:
passwd: password updated successfully
root@practice:/home/user# useradd oscar
root@practice:/home/user# passwd oscar
New password:
Retype new password:
passwd: password updated successfully
```

Verify that users were added successfully by running:
	
```bash
root@practice:/home/user# cat /etc/passwd | grep home
michael:x:1006:1010::/home/michael:/bin/sh
jan:x:1007:1011::/home/jan:/bin/sh
jim:x:1008:1012::/home/jim:/bin/sh
dwight:x:1009:1013::/home/dwight:/bin/sh
andrew:x:1010:1014::/home/andrew:/bin/sh
toby:x:1011:1015::/home/toby:/bin/sh
kevin:x:1012:1016::/home/kevin:/bin/sh
angela:x:1013:1017::/home/angela:/bin/sh
oscar:x:1014:1018::/home/oscar:/bin/sh
```

Add users to groups as follows:

```bash
root@practice:/home/user# usermod -aG Managers michael
root@practice:/home/user# usermod -aG Managers jan
root@practice:/home/user# usermod -aG Sales jim
root@practice:/home/user# usermod -aG Sales dwight
root@practice:/home/user# usermod -aG Sales andrew
root@practice:/home/user# usermod -aG Finance kevin
root@practice:/home/user# usermod -aG Finance angela
root@practice:/home/user# usermod -aG Finance oscar
root@practice:/home/user# usermod -aG HR toby
```

Verify that users were added to their groups successfully:

```bash
root@practice:/home/user# cat /etc/group
//redacted

HR:x:1006:toby
Sales:x:1007:jim,dwight,andrew
Finance:x:1008:kevin,angela,oscar
Managers:x:1009:jan,michael
```

Update the firewall to allow Samba traffic.

`sudo ufw allow 'Samba'`

The command above will allow incoming UDP connections on ports `137` and `138` and TCP connections on ports `139` and `445`.


We need to set up a Samba password for our user accounts since samba doesn't use the system account password.

`-a` _adds a user_

`-e` _enables the user_

```bash
root@practice:/home/user# sudo smbpasswd -a michael
New SMB password:
Retype new SMB password:
Added user michael.
root@practice:/home/user# sudo smbpasswd -e michael
Enabled user michael.
root@practice:/home/user# sudo smbpasswd -a jan
New SMB password:
Retype new SMB password:
Added user jan.
root@practice:/home/user# sudo smbpasswd -e jan
Enabled user jan.
root@practice:/home/user# sudo smbpasswd -a jim
New SMB password:
Retype new SMB password:
Added user jim.
root@practice:/home/user# sudo smbpasswd -e jim
Enabled user jim.
root@practice:/home/user# sudo smbpasswd -a dwight
New SMB password:
Retype new SMB password:
Added user dwight.
root@practice:/home/user# sudo smbpasswd -e dwight
Enabled user dwight.
root@practice:/home/user# sudo smbpasswd -a andrew
New SMB password:
Retype new SMB password:
Added user andrew.
root@practice:/home/user# sudo smbpasswd -e andrew
Enabled user andrew.
root@practice:/home/user# sudo smbpasswd -a toby
New SMB password:
Retype new SMB password:
Added user toby.
root@practice:/home/user# sudo smbpasswd -e toby
Enabled user toby.
root@practice:/home/user# sudo smbpasswd -a kevin
New SMB password:
Retype new SMB password:
Added user kevin.
root@practice:/home/user# sudo smbpasswd -e kevin
Enabled user kevin.
root@practice:/home/user# sudo smbpasswd -a angela
New SMB password:
Retype new SMB password:
Added user angela.
root@practice:/home/user# sudo smbpasswd -e angela
Enabled user angela.
root@practice:/home/user# sudo smbpasswd -a oscar
New SMB password:
Retype new SMB password:
Added user oscar.
root@practice:/home/user# sudo smbpasswd -e oscar
Enabled user oscar.
```

When installing samba, a group called **sambashare**  is created. 

We also need to change the group ownership of the respective directories we created as follows:

```bash
root@practice:/home/user# sudo chgrp HR /opt/HR/
root@practice:/home/user# sudo chgrp Sales /opt/Sales/
root@practice:/home/user# sudo chgrp Finance /opt/Finance/
root@practice:/home/user# sudo chgrp Managers /opt/Confidential/
```

Create individual user's directories as follows:

```bash
root@practice:/home/user# sudo mkdir /opt/HR/toby
root@practice:/home/user# sudo mkdir /opt/Sales/jim
root@practice:/home/user# sudo mkdir /opt/Sales/dwight
root@practice:/home/user# sudo mkdir /opt/Sales/andrew
root@practice:/home/user# sudo mkdir /opt/Finance/kevin
root@practice:/home/user# sudo mkdir /opt/Finance/angela
root@practice:/home/user# sudo mkdir /opt/Finance/oscar
root@practice:/home/user# sudo mkdir /opt/Confidential/michael
root@practice:/home/user# sudo mkdir /opt/Confidential/jan
```

Set directory ownership to individual users as follows:

```bash
root@practice:/home/user# sudo chown toby:HR /opt/HR/toby/
root@practice:/home/user# sudo chown jim:Sales /opt/Sales/jim/
root@practice:/home/user# sudo chown dwight:Sales /opt/Sales/dwight/
root@practice:/home/user# sudo chown andrew:Sales /opt/Sales/andrew/
root@practice:/home/user# sudo chown kevin:Finance /opt/Finance/kevin/
root@practice:/home/user# sudo chown angela:Finance /opt/Finance/angela/
root@practice:/home/user# sudo chown oscar:Finance /opt/Finance/oscar/
root@practice:/home/user# sudo chown michael:Managers /opt/Confidential/michael/
root@practice:/home/user# sudo chown jan:Managers /opt/Confidential/jan/
```

```bash
root@practice:/home/user# cd /opt/
root@practice:/opt# ls -la
total 28
drwxr-xr-x  7 root root     4096 Nov 16 09:17 .
drwxr-xr-x 20 root root     4096 Nov 15 21:00 ..
drwxr-xr-x  4 root Managers 4096 Nov 16 11:25 Confidential
drwxr-xr-x  5 root Finance  4096 Nov 16 11:21 Finance
drwxr-xr-x  3 root HR       4096 Nov 16 11:12 HR
drwxr-xr-x  2 root root     4096 Nov 15 22:35 Movies
drwxr-xr-x  5 root Sales    4096 Nov 16 11:16 Sales
```

The following command will add the setgid bit to the users directories so that the newly created files in this directories will inherit the group of the parent directory. This way, no matter which user creates a new file, the file will have the group-owner in it.
```bash
root@practice:/opt# chmod 2770 Confidential/jan/ Confidential/michael/
root@practice:/opt# chmod 2770 Finance/angela/ Finance/kevin/ Finance/oscar/
root@practice:/opt# chmod 2770 HR/toby/
root@practice:/opt# chmod 2770 Sales/jim/ Sales/dwight/ Sales/andrew/
```


Samba's configuration file is located in `/etc/samba/smb.conf`. We need to make a backup copy of the file incase we want to revert back to its original state if we mess up.

`cp /etc/samba/smb.conf /etc/samba/smb.conf.bak`

<details>
  <summary>Default Configuration File</summary>
  
    ```bash
    root@practice:/home/user# cat /etc/samba/smb.conf
    #
    # Sample configuration file for the Samba suite for Debian GNU/Linux.
    #
    #
    # This is the main Samba configuration file. You should read the
    # smb.conf(5) manual page in order to understand the options listed
    # here. Samba has a huge number of configurable options most of which
    # are not shown in this example
    #
    # Some options that are often worth tuning have been included as
    # commented-out examples in this file.
    #  - When such options are commented with ";", the proposed setting
    #    differs from the default Samba behaviour
    #  - When commented with "#", the proposed setting is the default
    #    behaviour of Samba but the option is considered important
    #    enough to be mentioned here
    #
    # NOTE: Whenever you modify this file you should run the command
    # "testparm" to check that you have not made any basic syntactic
    # errors.

    #======================= Global Settings =======================

    [global]

    ## Browsing/Identification ###

    # Change this to the workgroup/NT-domain name your Samba server will part of
       workgroup = WORKGROUP

    # server string is the equivalent of the NT Description field
       server string = %h server (Samba, Ubuntu)

    #### Networking ####

    # The specific set of interfaces / networks to bind to
    # This can be either the interface name or an IP address/netmask;
    # interface names are normally preferred
    ;   interfaces = 127.0.0.0/8 eth0

    # Only bind to the named interfaces and/or networks; you must use the
    # 'interfaces' option above to use this.
    # It is recommended that you enable this feature if your Samba machine is
    # not protected by a firewall or is a firewall itself.  However, this
    # option cannot handle dynamic or non-broadcast interfaces correctly.
    ;   bind interfaces only = yes



    #### Debugging/Accounting ####

    # This tells Samba to use a separate log file for each machine
    # that connects
       log file = /var/log/samba/log.%m

    # Cap the size of the individual log files (in KiB).
       max log size = 1000

    # We want Samba to only log to /var/log/samba/log.{smbd,nmbd}.
    # Append syslog@1 if you want important messages to be sent to syslog too.
       logging = file

    # Do something sensible when Samba crashes: mail the admin a backtrace
       panic action = /usr/share/samba/panic-action %d


    ####### Authentication #######

    # Server role. Defines in which mode Samba will operate. Possible
    # values are "standalone server", "member server", "classic primary
    # domain controller", "classic backup domain controller", "active
    # directory domain controller".
    #
    # Most people will want "standalone server" or "member server".
    # Running as "active directory domain controller" will require first
    # running "samba-tool domain provision" to wipe databases and create a
    # new domain.
       server role = standalone server

       obey pam restrictions = yes

    # This boolean parameter controls whether Samba attempts to sync the Unix
    # password with the SMB password when the encrypted SMB password in the
    # passdb is changed.
       unix password sync = yes

    # For Unix password sync to work on a Debian GNU/Linux system, the following
    # parameters must be set (thanks to Ian Kahan <<kahan@informatik.tu-muenchen.de> for
    # sending the correct chat script for the passwd program in Debian Sarge).
       passwd program = /usr/bin/passwd %u
       passwd chat = *Enter\snew\s*\spassword:* %n\n *Retype\snew\s*\spassword:* %n\n *password\supdated\ssuccessfully* .

    # This boolean controls whether PAM will be used for password changes
    # when requested by an SMB client instead of the program listed in
    # 'passwd program'. The default is 'no'.
       pam password change = yes

    # This option controls how unsuccessful authentication attempts are mapped
    # to anonymous connections
       map to guest = bad user

    ########## Domains ###########

    #
    # The following settings only takes effect if 'server role = primary
    # classic domain controller', 'server role = backup domain controller'
    # or 'domain logons' is set
    #

    # It specifies the location of the user's
    # profile directory from the client point of view) The following
    # required a [profiles] share to be setup on the samba server (see
    # below)
    ;   logon path = \\%N\profiles\%U
    # Another common choice is storing the profile in the user's home directory
    # (this is Samba's default)
    #   logon path = \\%N\%U\profile

    # The following setting only takes effect if 'domain logons' is set
    # It specifies the location of a user's home directory (from the client
    # point of view)
    ;   logon drive = H:
    #   logon home = \\%N\%U

    # The following setting only takes effect if 'domain logons' is set
    # It specifies the script to run during logon. The script must be stored
    # in the [netlogon] share
    # NOTE: Must be store in 'DOS' file format convention
    ;   logon script = logon.cmd

    # This allows Unix users to be created on the domain controller via the SAMR
    # RPC pipe.  The example command creates a user account with a disabled Unix
    # password; please adapt to your needs
    ; add user script = /usr/sbin/adduser --quiet --disabled-password --gecos "" %u

    # This allows machine accounts to be created on the domain controller via the
    # SAMR RPC pipe.
    # The following assumes a "machines" group exists on the system
    ; add machine script  = /usr/sbin/useradd -g machines -c "%u machine account" -d /var/lib/samba -s /bin/false %u

    # This allows Unix groups to be created on the domain controller via the SAMR
    # RPC pipe.
    ; add group script = /usr/sbin/addgroup --force-badname %g

    ############ Misc ############

    # Using the following line enables you to customise your configuration
    # on a per machine basis. The %m gets replaced with the netbios name
    # of the machine that is connecting
    ;   include = /home/samba/etc/smb.conf.%m

    # Some defaults for winbind (make sure you're not using the ranges
    # for something else.)
    ;   idmap config * :              backend = tdb
    ;   idmap config * :              range   = 3000-7999
    ;   idmap config YOURDOMAINHERE : backend = tdb
    ;   idmap config YOURDOMAINHERE : range   = 100000-999999
    ;   template shell = /bin/bash

    # Setup usershare options to enable non-root users to share folders
    # with the net usershare command.

    # Maximum number of usershare. 0 means that usershare is disabled.
    #   usershare max shares = 100

    # Allow users who've been granted usershare privileges to create
    # public shares, not just authenticated ones
       usershare allow guests = yes

    #======================= Share Definitions =======================

    # Un-comment the following (and tweak the other settings below to suit)
    # to enable the default home directory shares. This will share each
    # user's home directory as \\server\username
    ;[homes]
    ;   comment = Home Directories
    ;   browseable = no

    # By default, the home directories are exported read-only. Change the
    # next parameter to 'no' if you want to be able to write to them.
    ;   read only = yes

    # File creation mask is set to 0700 for security reasons. If you want to
    # create files with group=rw permissions, set next parameter to 0775.
    ;   create mask = 0700

    # Directory creation mask is set to 0700 for security reasons. If you want to
    # create dirs. with group=rw permissions, set next parameter to 0775.
    ;   directory mask = 0700

    # By default, \\server\username shares can be connected to by anyone
    # with access to the samba server.
    # Un-comment the following parameter to make sure that only "username"
    # can connect to \\server\username
    # This might need tweaking when using external authentication schemes
    ;   valid users = %S

    # Un-comment the following and create the netlogon directory for Domain Logons
    # (you need to configure Samba to act as a domain controller too.)
    ;[netlogon]
    ;   comment = Network Logon Service
    ;   path = /home/samba/netlogon
    ;   guest ok = yes
    ;   read only = yes

    # Un-comment the following and create the profiles directory to store
    # users profiles (see the "logon path" option above)
    # (you need to configure Samba to act as a domain controller too.)
    # The path below should be writable by all users so that their
    # profile directory may be created the first time they log on
    ;[profiles]
    ;   comment = Users profiles
    ;   path = /home/samba/profiles
    ;   guest ok = no
    ;   browseable = no
    ;   create mask = 0600
    ;   directory mask = 0700

    [printers]
       comment = All Printers
       browseable = no
       path = /var/spool/samba
       printable = yes
       guest ok = no
       read only = yes
       create mask = 0700

    # Windows clients look for this share name as a source of downloadable
    # printer drivers
    [print$]
       comment = Printer Drivers
       path = /var/lib/samba/printers
       browseable = yes
       read only = yes
       guest ok = no
    # Uncomment to allow remote administration of Windows print drivers.
    # You may need to replace 'lpadmin' with the name of the group your
    # admin users are members of.
    # Please note that you also need to set appropriate Unix permissions
    # to the drivers directory for these users to have write rights in it
    ;   write list = root, @lpadmin
    ```
</details>

    ```bash
    [FINANCE]
        comment = Finance Share
        path = /opt/Finance
        read only = no
        browsable = yes
        valid users = @Finance @Managers
        force create mode = 0660
        force directory mode = 2770

    [SALES]
        comment = Sales Share
        path = /opt/Sales
        read only = no
        browsable = yes
        valid users = @Sales @Managers
        force create mode = 0660
        force directory mode = 2770

    [CONFIDENTIAL]
        comment = Confidential
        path = /opt/Confidential
        read only = no
        browsable = yes
        valid users = @Managers
        force create mode = 0660
        force directory mode = 2770

    [HR]
        comment = Human Resource
        path = /opt/HR
        read only = no
        browsable = yes
        valid users = @HR @Managers
        force create mode = 0660
        force directory mode = 2770

    [MOVIES]
        comment = Movies
        path = /opt/Movies
        read only = yes
        browsable = yes
        writeable = yes
        guest ok = yes

    [PLEX MEDIA SERVER]
        comment = Upload Movies, Courses,Series here
        path = /srv/plexmedia
        read only = no
        browsable = yes
        writeable = yes
        guest ok = yes
    ```


-   _comment:_ a short description of the share. Adjust to fit your needs.
-   _path:_ the path to the directory to share.
-   _browsable:_ enables Windows clients to browse the shared directory using Windows Explorer. By setting to `no` other users will not be able to see the share.
-   _guest ok:_ allows clients to connect to the share without supplying a password.
-   _read only:_ determines if the share is read only or if write privileges are granted. Write privileges are allowed only when the value is _no_, as is seen in this example. If the value is _yes_, then access to the share is read only.
-   _writable:_ makes the share writable to authenticated users
-   _force create mode:_ Sets the permissions for the newly created files in this share.
-   _force directory mode:_ Sets the permissions for the newly created directories in this share.
-   _valid users:_ A list of users and groups that are allowed to access the share. Even if a user has been added to Samba (with the smbpasswd command) they will not be able to access this share unless they are a member of the listed group. Groups are prefixed with the `@` symbol while users are just listed without the "@" symbol

It is  recommended that you verify the Samba configuration each time you update the `/etc/samba/smb.conf` file using the `**testparm**` utility as shown below:

```bash
root@practice:/opt/Confidential# testparm
Load smb config files from /etc/samba/smb.conf
Loaded services file OK.
Server role: ROLE_STANDALONE

```

Restart `smbd` & `nmbd`

`systemctl restart smbd`

`systemctl restart nmbd`


### smbclient

smbclient utility is a command like tool which provides FTP-Like client to access SMB or Samba shares on a server. 

`sudo apt install smbclient`

Connecting/list a shared folder

```bash
root@practice:/home/user# smbclient --no-pass //192.168.100.10/MOVIES
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Mon Nov 15 22:35:22 2021
  ..                                  D        0  Tue Nov 16 09:17:40 2021

                20507216 blocks of size 1024. 11482300 blocks available
smb: \> exit
root@practice:/home/user# smbclient --no-pass //192.168.100.10/FINANCE
tree connect failed: NT_STATUS_ACCESS_DENIED
```

To list shared folders as a null/guest user:

```bash
root@practice:/home/user# smbclient --no-pass -L //192.168.100.10

        Sharename       Type      Comment
        ---------       ----      -------
        print$          Disk      Printer Drivers
        FINANCE         Disk      Finance Share
        SALES           Disk      Sales Share
        CONFIDENTIAL    Disk      Confidential
        HR              Disk      Human Resource
        MOVIES          Disk      Movies
        PLEX MEDIA SERVER Disk      Upload Movies, Courses,Series here
        IPC$            IPC       IPC Service (practice server (Samba, Ubuntu))
SMB1 disabled -- no workgroup available
root@practice:/home/user# 
```

To list and connect to a share as an authenticated user:

`smbclient //samba_hostname_or_server_ip/share_name -U username`

```bash
user@practice:~$ smbclient //192.168.100.10/FINANCE -U angela
Enter WORKGROUP\angela's password:
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Tue Nov 16 11:21:46 2021
  ..                                  D        0  Tue Nov 16 09:17:40 2021
  kevin                               D        0  Tue Nov 16 11:21:36 2021
  oscar                               D        0  Tue Nov 16 11:21:46 2021
  angela                              D        0  Tue Nov 16 11:21:41 2021

                20507216 blocks of size 1024. 12288748 blocks available
smb: \> cd kevin\
smb: \kevin\> ls
  .                                   D        0  Tue Nov 16 11:21:36 2021
  ..                                  D        0  Tue Nov 16 11:21:46 2021

                20507216 blocks of size 1024. 12288748 blocks available
```

### smbmap

smbmap is a command line tool that allows users to enumerate samba share drives across an entire domain. It can List share drives, drive permissions, share contents, upload/download functionality, file name auto-download pattern matching, and even execute remote commands.

**Installation:**

`sudo apt install smbmap`

To list shares as an authenticated user:

```bash
root@practice:/home/user# smbmap -u michael -p 12345678 -H 192.168.100.10
[+] IP: 192.168.100.10:445      Name: practice.com
        Disk                                                    Permissions     Comment
        ----                                                    -----------     -------
        print$                                                  READ ONLY       Printer Drivers
        FINANCE                                                 READ ONLY       Finance Share
        SALES                                                   READ ONLY       Sales Share
        CONFIDENTIAL                                            READ ONLY       Confidential
        HR                                                      READ ONLY       Human Resource
        MOVIES                                                  READ ONLY       Movies
        PLEX MEDIA SERVER                                       READ ONLY       Upload Movies, Courses,Series here
        IPC$                                                    NO ACCESS       IPC Service (practice server (Samba, Ubuntu))
root@practice:/home/user# smbmap -u jim -p 12345678 -H 192.168.100.10
[+] IP: 192.168.100.10:445      Name: practice.com
        Disk                                                    Permissions     Comment
        ----                                                    -----------     -------
        print$                                                  READ ONLY       Printer Drivers
        FINANCE                                                 NO ACCESS       Finance Share
        SALES                                                   READ ONLY       Sales Share
        CONFIDENTIAL                                            NO ACCESS       Confidential
        HR                                                      NO ACCESS       Human Resource
        MOVIES                                                  READ ONLY       Movies
        PLEX MEDIA SERVER                                       READ ONLY       Upload Movies, Courses,Series here
        IPC$                                                    NO ACCESS       IPC Service (practice server (Samba, Ubuntu))
root@practice:/home/user# smbmap -u angela -p 12345678 -H 192.168.100.10
[+] IP: 192.168.100.10:445      Name: practice.com
        Disk                                                    Permissions     Comment
        ----                                                    -----------     -------
        print$                                                  READ ONLY       Printer Drivers
        FINANCE                                                 READ ONLY       Finance Share
        SALES                                                   NO ACCESS       Sales Share
        CONFIDENTIAL                                            NO ACCESS       Confidential
        HR                                                      NO ACCESS       Human Resource
        MOVIES                                                  READ ONLY       Movies
        PLEX MEDIA SERVER                                       READ ONLY       Upload Movies, Courses,Series here
        IPC$                                                    NO ACCESS       IPC Service (practice server (Samba, Ubuntu))
root@practice:/home/user# smbmap -u toby -p 12345678 -H 192.168.100.10
[+] IP: 192.168.100.10:445      Name: practice.com
        Disk                                                    Permissions     Comment
        ----                                                    -----------     -------
        print$                                                  READ ONLY       Printer Drivers
        FINANCE                                                 NO ACCESS       Finance Share
        SALES                                                   NO ACCESS       Sales Share
        CONFIDENTIAL                                            NO ACCESS       Confidential
        HR                                                      READ ONLY       Human Resource
        MOVIES                                                  READ ONLY       Movies
        PLEX MEDIA SERVER                                       READ ONLY       Upload Movies, Courses,Series here
        IPC$                                                    NO ACCESS       IPC Service (practice server (Samba, Ubuntu))
root@practice:/home/user#

```

To recursively list directories:

```bash
root@practice:/home/user# smbmap -u angela -p 12345678 -H 192.168.100.10 -R FINANCE
[+] IP: 192.168.100.10:445      Name: practice.com
        Disk                                                    Permissions     Comment
        ----                                                    -----------     -------
        FINANCE                                                 READ ONLY
        .\FINANCE\*
        dr--r--r--                0 Tue Nov 16 11:21:46 2021    .
        dr--r--r--                0 Tue Nov 16 09:17:40 2021    ..
        dr--r--r--                0 Thu Nov 18 14:49:44 2021    kevin
        dr--r--r--                0 Thu Nov 18 14:49:31 2021    oscar
        dr--r--r--                0 Thu Nov 18 14:49:12 2021    angela
        .\FINANCE\kevin\*
        dr--r--r--                0 Thu Nov 18 14:49:44 2021    .
        dr--r--r--                0 Tue Nov 16 11:21:46 2021    ..
        fr--r--r--               16 Thu Nov 18 14:49:57 2021    Kevin.txt
        .\FINANCE\oscar\*
        dr--r--r--                0 Thu Nov 18 14:49:31 2021    .
        dr--r--r--                0 Tue Nov 16 11:21:46 2021    ..
        fr--r--r--                0 Thu Nov 18 14:49:24 2021    Oscar.txt
        .\FINANCE\angela\*
        dr--r--r--                0 Thu Nov 18 14:49:12 2021    .
        dr--r--r--                0 Tue Nov 16 11:21:46 2021    ..
        fr--r--r--                0 Thu Nov 18 14:48:10 2021    Angela.txt
```


### Mounting Shares In Linux

CIFS filesystem is generally the preferred method for mounting SMB/CIFS shares on Linux.

_Common Internet File System (CIFS)_ 

**Installation:**

`sudo apt install cifs-utils`

We can begin by creating a directory that we will mount our share

`sudo mkdir /mnt/submount`

syntax for mounting a share is shown below

`sudo mount -t cifs -o username=username,password=password //samba_hostname_or_server_ip/sharename /mnt/smbmount`

For example lets try mounting the `SALES` share with _dwight's_ credentials in the `/mnt/submount`

```bash
root@practice:/home/user# mkdir /mnt/submount
root@practice:/home/user# sudo mount -t cifs -o username=dwight,password=12345678 //192.168.100.10/SALES /mnt/submount/
root@practice:/home/user# ls -la /mnt/submount/
total 4
drwxr-xr-x 2 root root    0 Nov 16 11:16 .
drwxr-xr-x 3 root root 4096 Nov 18 13:54 ..
drwxr-xr-x 2 root root    0 Nov 16 11:16 andrew
drwxr-xr-x 2 root root    0 Nov 16 11:16 dwight
drwxr-xr-x 2 root root    0 Nov 16 11:16 jim
root@practice:/home/user#
```

If we want to mount a share that doesnt require credentials, we would do so as follows:

`mount -t cifs //x.x.x.x/share /mnt/share`

```bash
root@practice:/home/user# mkdir /mnt/media
root@practice:/home/user# sudo mount -t cifs //192.168.100.10/"PLEX MEDIA SERVER" /mnt/media/
root@practice:/home/user# ls -la /mnt/media/
total 4
drwxr-xr-x 2 root root    0 Nov 17 12:11 .
drwxr-xr-x 4 root root 4096 Nov 18 14:08 ..
drwxr-xr-x 2 root root    0 Nov 17 13:01 course
drwxr-xr-x 2 root root    0 Nov 17 12:11 movies
drwxr-xr-x 2 root root    0 Nov 17 12:11 series
```

## Connecting to shares on Windows
Open File Explorer 
`\\samba_hostname_or_server_ip\sharename`

![image](https://user-images.githubusercontent.com/58165365/142448837-948bac7b-f8bd-4ecd-829b-654c4f54702b.png)

or

Open File Explorer. Right click and select "_Add a network location_"

![image](https://user-images.githubusercontent.com/58165365/142448873-b2ab3a8e-1d71-4e07-b00c-ca7ab0789353.png)

Choose custom network location

![image](https://user-images.githubusercontent.com/58165365/142448908-ba69b02e-c772-4aac-bfd7-434fa424c044.png)

Enter the address of the Samba share in the following format `\\samba_hostname_or_server_ip\sharename`.

![image](https://user-images.githubusercontent.com/58165365/142448972-2d20a092-8f4e-45eb-b065-58bdbed79290.png)

Hit “Next” and you will be prompted to enter the login credentials as shown below:

![image](https://user-images.githubusercontent.com/58165365/142449000-6fb8dd56-2cf3-418f-8ca6-51fcbeebe26f.png)

In the next window you can type a custom name for the network location. The default one will be picked up by the Samba server.

![image](https://user-images.githubusercontent.com/58165365/142449046-58a13033-3233-403c-bd27-80d42aaa6ffb.png)

Click “Next” to move to the last screen of the connection setup wizard.
Click “Finish” and the files on the Samba server will be shown.

![image](https://user-images.githubusercontent.com/58165365/142449084-888df2fe-401c-4395-987e-bd81d44afb7c.png)

![image](https://user-images.githubusercontent.com/58165365/142449121-11fc6c0d-34d8-46d6-83d1-e9b42945c16f.png)

or 

`win + r`

![image](https://user-images.githubusercontent.com/58165365/142449164-0d043f1f-2243-4c31-ad90-7b66b14855fe.png)

![image](https://user-images.githubusercontent.com/58165365/142449187-c2fefd39-1634-4ce1-b611-549ae6166a0f.png)



# References
- [Securing File and Print Server](https://ubuntu.com/server/docs/samba-securing)
- [Install and Configure Samba](https://ubuntu.com/tutorials/install-and-configure-samba)
- [File Server](https://ubuntu.com/server/docs/samba-file-server)
- [Tech Republic](https://www.techrepublic.com/article/how-to-create-a-samba-share-on-ubuntu-server-20-04/)
- [Linuxize](https://linuxize.com/post/how-to-install-and-configure-samba-on-ubuntu-18-04/)
- [Kifarunix](https://kifarunix.com/install-and-configure-samba-file-server-on-ubuntu-20-04/)
