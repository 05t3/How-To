
`curl https://downloads.plex.tv/plex-keys/PlexSign.key | sudo apt-key add -`


`echo deb https://downloads.plex.tv/repo/deb public main | sudo tee /etc/apt/sources.list.d/plexmediaserver.list`


```bash
sudo apt update
sudo apt install plexmediaserver
```

`sudo systemctl status plexmediaserver`

```bash
root@practice:/# systemctl status plexmediaserver
● plexmediaserver.service - Plex Media Server
     Loaded: loaded (/lib/systemd/system/plexmediaserver.service; enabled; vendor preset: enabled)
     Active: active (running) since Wed 2021-11-17 10:50:58 UTC; 1h 21min ago
   Main PID: 750 (Plex Media Serv)
      Tasks: 97 (limit: 1061)
     Memory: 172.0M
     CGroup: /system.slice/plexmediaserver.service
             ├─ 750 /usr/lib/plexmediaserver/Plex Media Server
             ├─1006 Plex Plug-in [com.plexapp.system] /usr/lib/plexmediaserver/Resources/Plug-ins-8dcc73a59/Framework.bundle/Contents/Re>
             ├─1233 /usr/lib/plexmediaserver/Plex Tuner Service /usr/lib/plexmediaserver/Resources/Tuner/Private /usr/lib/plexmediaserve>
             ├─1253 Plex Plug-in [tv.plex.agents.music] /usr/lib/plexmediaserver/Resources/Plug-ins-8dcc73a59/Framework.bundle/Contents/>
             ├─1256 Plex Plug-in [com.plexapp.agents.opensubtitles] /usr/lib/plexmediaserver/Resources/Plug-ins-8dcc73a59/Framework.bund>
             ├─1425 Plex Plug-in [com.plexapp.agents.movieposterdb] /usr/lib/plexmediaserver/Resources/Plug-ins-8dcc73a59/Framework.bund>
             ├─1427 Plex Plug-in [tv.plex.agents.series] /usr/lib/plexmediaserver/Resources/Plug-ins-8dcc73a59/Framework.bundle/Contents>
             └─1433 Plex Plug-in [com.plexapp.agents.plexthememusic] /usr/lib/plexmediaserver/Resources/Plug-ins-8dcc73a59/Framework.bun>

Nov 17 10:50:57 practice.com systemd[1]: Starting Plex Media Server...
Nov 17 10:50:58 practice.com systemd[1]: Started Plex Media Server.

```

`sudo nano /etc/ufw/applications.d/plexmediaserver`

```css
GNU nano 5.2                                     /etc/ufw/applications.d/plexmediaserver

[plexmediaserver]
title=Plex Media Server (Standard)
description=The Plex Media Server
ports=32400/tcp|3005/tcp|5353/udp|8324/tcp|32410:32414/udp

[plexmediaserver-dlna]
title=Plex Media Server (DLNA)
description=The Plex Media Server (additional DLNA capability only)
ports=1900/udp|32469/tcp

[plexmediaserver-all]
title=Plex Media Server (Standard + DLNA)
description=The Plex Media Server (with additional DLNA capability)
ports=32400/tcp|3005/tcp|5353/udp|8324/tcp|32410:32414/udp|1900/udp|32469/tcp

```

`sudo ufw app update plexmediaserver`

`sudo ufw allow plexmediaserver-all`

`sudo ufw status verbose`

```bash
root@practice:/etc/ufw/applications.d# ufw status verbose numbered
Status: active
Logging: on (low)
Default: deny (incoming), allow (outgoing), disabled (routed)
New profiles: skip

To                         Action      From
--                         ------      ----
22/tcp                     ALLOW IN    Anywhere
20/tcp                     ALLOW IN    Anywhere
21/tcp                     ALLOW IN    Anywhere
40000:50000/tcp            ALLOW IN    Anywhere
990                        ALLOW IN    Anywhere
10000/tcp                  ALLOW IN    Anywhere
137,138/udp (Samba)        ALLOW IN    Anywhere
139,445/tcp (Samba)        ALLOW IN    Anywhere
32400/tcp (plexmediaserver-all) ALLOW IN    Anywhere
3005/tcp (plexmediaserver-all) ALLOW IN    Anywhere
5353/udp (plexmediaserver-all) ALLOW IN    Anywhere
8324/tcp (plexmediaserver-all) ALLOW IN    Anywhere
32410:32414/udp (plexmediaserver-all) ALLOW IN    Anywhere
1900/udp (plexmediaserver-all) ALLOW IN    Anywhere
32469/tcp (plexmediaserver-all) ALLOW IN    Anywhere
22/tcp (v6)                ALLOW IN    Anywhere (v6)
20/tcp (v6)                ALLOW IN    Anywhere (v6)
21/tcp (v6)                ALLOW IN    Anywhere (v6)
40000:50000/tcp (v6)       ALLOW IN    Anywhere (v6)
990 (v6)                   ALLOW IN    Anywhere (v6)
10000/tcp (v6)             ALLOW IN    Anywhere (v6)
137,138/udp (Samba (v6))   ALLOW IN    Anywhere (v6)
139,445/tcp (Samba (v6))   ALLOW IN    Anywhere (v6)
32400/tcp (plexmediaserver-all (v6)) ALLOW IN    Anywhere (v6)
3005/tcp (plexmediaserver-all (v6)) ALLOW IN    Anywhere (v6)
5353/udp (plexmediaserver-all (v6)) ALLOW IN    Anywhere (v6)
8324/tcp (plexmediaserver-all (v6)) ALLOW IN    Anywhere (v6)
32410:32414/udp (plexmediaserver-all (v6)) ALLOW IN    Anywhere (v6)
1900/udp (plexmediaserver-all (v6)) ALLOW IN    Anywhere (v6)
32469/tcp (plexmediaserver-all (v6)) ALLOW IN    Anywhere (v6)

```

```bash
root@practice:/# mkdir -p srv/plexmedia/course srv/plexmedia/movies srv/plexmedia/series
root@practice:/# ls -la /srv/plexmedia/
total 20
drwxr-xr-x 5 root root 4096 Nov 17 12:11 .
drwxr-xr-x 5 root root 4096 Nov 17 12:11 ..
drwxr-xr-x 2 root root 4096 Nov 17 12:11 course
drwxr-xr-x 2 root root 4096 Nov 17 12:11 movies
drwxr-xr-x 2 root root 4096 Nov 17 12:11 series
```


```bash
root@practice:/# cat /etc/passwd | grep plex
plex:x:997:997::/var/lib/plexmediaserver:/usr/sbin/nologin
root@practice:/#
```

`sudo chown -R plex: /srv/plexmedia/`

`nano /etc/samba/smb.conf`

```
[PLEX MEDIA SERVER]
    comment = Upload Movies, Courses,Series here
    path = /srv/plexmedia
    read only = no
    browsable = yes
    writeable = yes
    guest ok = yes
```

`systemctl reload smbd`

`systemctl reload nmbd`


```bash
root@practice:/home/user# sudo smbpasswd -a plex
New SMB password:
Retype new SMB password:
Added user plex.
root@practice:/home/user# sudo smbpasswd -e plex
Enabled user plex.
```


http://YOUR_SERVER_IP:32400/web

![image](https://user-images.githubusercontent.com/58165365/142200664-727ecb90-cfc7-415c-b698-9174bf9a518e.png)

![image](https://user-images.githubusercontent.com/58165365/142201818-ff2ceaa5-623f-46ba-9a29-ee31df69a4af.png)

![image](https://user-images.githubusercontent.com/58165365/142202803-f49afb98-44eb-4bd9-8de9-647d559be71b.png)

![image](https://user-images.githubusercontent.com/58165365/142205859-33f5d405-7ae1-4161-b68b-a2d2c91c5dc3.png)

![image](https://user-images.githubusercontent.com/58165365/142206085-3d7249ff-6dda-484d-a5a7-397de9631183.png)

![image](https://user-images.githubusercontent.com/58165365/142206912-9ff91df7-7f37-4234-86a9-e8d356d30ab5.png)

sharing

![image](https://user-images.githubusercontent.com/58165365/142208921-70e62fd2-e5f4-4e03-8c1b-fdce3f23955a.png)

![image](https://user-images.githubusercontent.com/58165365/142209030-86f21ce3-d7a8-45a5-bbdf-6443093dcfc9.png)

![image](https://user-images.githubusercontent.com/58165365/142209075-04c21ba9-f6bf-4053-8759-f17bc25a80a9.png)

![image](https://user-images.githubusercontent.com/58165365/142209102-8a876b0d-b1e7-4881-9165-81a602e1e22a.png)

![image](https://user-images.githubusercontent.com/58165365/142209147-4ca59c35-8981-4b67-ad41-aed706316010.png)

