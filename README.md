# INSTALL ***<span style="color:#2496ed">Docker</span>*** on ***<span style="color:#c31c4a">Raspberry Pi</span>***

## Overview
Basic steps of how to install Docker on Raspberry Pi (idorpi).

**Table of contents**
- [System time syncronize](#system-time-syncronize)
- [APT update](#apt-advanced-packaging-tools-update)
- [Docker installation](#docker-installation)
- [Proxy configuration](#proxy-configuration)

## System time syncronize
```
pi@raspberrypi:~ $ sudo dpkg-reconfigure tzdata
pi@raspberrypi:~ $ sudo date -u 0626100720
pi@raspberrypi:~ $ sudo date -s "<day> <month> <year> <hour>:<miniute>'
```
 + ##### *Troubleshooting*: [[1]][solve_link]

[solve_link]: https://www.raspberrypi.org/forums/viewtopic.php?t=278350

## APT (*advanced packaging tools*) update
```
pi@raspberrypi:~ $ su root
Password:
root@raspberrypi:/home/pi# apt-get update
```
- ##### *Resource:* [[1]][apt_update]

[apt_update]: https://www.raspberrypi.org/documentation/raspbian/updating.md

## Docker installation
<br/>

> Solution - 1
```
root@raspberrypi:/home/pi# apt-get remove docker docker-engine docker.io containerd runc
root@raspberrypi:/home/pi# apt-get update
root@raspberrypi:/home/pi# apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common
root@raspberrypi:/home/pi# curl -fsSL https://download.docker.com/linux/debian/gpg | apt-key add -
root@raspberrypi:/home/pi# apt-key fringerprint 0EBFCD88
root@raspberrypi:/home/pi# echo "deb [arch-armhf] https://download.docker.com/linux/debian buster stable" > /etc/apt/sources.list.d/docker.list
root@raspberrypi:/home/pi# apt-get update
root@raspberrypi:/home/pi# apt-get install docker-ce docker-ce-cli containerd.io
```
- #####  *Resource:* [[1]][solution_1_install]

> Solution - 2
```
root@raspberrypi:/home/pi# curl -sSL https://get.docker.com | sh
```
- ##### *Resource:* [[1]][solution_2_install]

[solution_1_install]: https://docs.docker.com/engine/install/debian/
[solution_2_install]: https://www.raspberrypi.org/blog/docker-comes-to-raspberry-pi/

## Proxy configuration 
<br/>

> Add proxy to ***<span style="color:#00b389">"system environment"</span>***
```
root@raspberrypi:/home/pi# export http_proxy="http://proxy.example.com:port"
root@raspberrypi:/home/pi# export https_proxy="https://proxy.example.com:port"
```
- ##### *Resource:* [[1]][sys_proxy]

[sys_proxy]: http://apt.com

> Add proxy to ***<span style="color:#c31c4a">"apt"</span>***
```
root@raspberrypi:/home/pi# echo 'Acquire::http::Proxy "http://proxy.example.com:port/";' > /etc/apt/apt.conf.d/01proxy
root@raspberrypi:/home/pi# echo 'Acquire::https::Proxy "https://proxy.example.com:port/";' >> /etc/apt/apt.conf.d/01proxy
```
- ##### *Resource:* [[1]][apt_proxy]
- ##### *Troubleshooting* [[1]][apt_ts_proxy]

[apt_proxy]: https://raspberrypi.stackexchange.com/questions/68580/how-do-i-set-proxy-in-raspberry-pi-raspbian-os-or-any-linux-using-command-li/68583
[apt_ts_proxy]: http://ddjhty.blogspot.com/2018/12/apt-update-could-not-handshake.html

> Add proxy to ***<span style="color:#2496ed">"docker"</span>***
```
root@raspberrypi:/home/pi# mkdir -p /etc/systemd/system/docker.service.d
root@raspberrypi:/home/pi# nano /etc/systemd/system/docker.service.d/http-proxy.conf
```

```
[Service]
Environment="HTTP_PROXY=http://proxy.example.com:port"
Environment="HTTPS_PROXY=https://proxy.example.com:port"
Environment="NO_PROXY=localhost,127.0.0.1,docker-registry.example.com,.corp"
```
```
root@raspberrypi:/home/pi# systemctl daemon-reload
root@raspberrypi:/home/pi# systemctl restart docker
root@raspberrypi:/home/pi# systemctl show --property=Environment docker
```
- ##### *Resource:* [[1]][docker_proxy]

[docker_proxy]: https://docs.docker.com/config/daemon/systemd/