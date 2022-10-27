NOTE: The steps here are accurate but Im still learning Git markdown so please bear with me as I finalize the formatting of this document. 
# Down and Dirty Install of Cisco YANG Suite 


* Download Photon OS from GitHub. I choose to use the OVA and just install it on my ESXi Host.

https://github.com/vmware/photon/wiki/Downloading-Photon-OS


* Login to PhotonOS

      - Username: root
      - Password: changeme


3.	By default PhotonOS is configured for DHCP. Let’s setup a static IP address. 

      a. Edit the PhotonOS Network config File


```
vi /etc/systemd/network/99-dhcp-en.network
```

b. Config Static IP

```
[Match]
Name=e*

[Network]
Address=10.100.100.100/24
Gateway=10.100.100.1
DNS=1.1.1.1,8.8.8.8
Domains=YOUR-DOMAIN.NAME
NTP=pool.ntp.org
```

      c. :wq  out of vi


4.	Set the timezone.  Im in USA\Eastern so I use EST5EDT, use the correct timezone for your locality. 

```
set EST5EDT timezone
ln -sf /usr/share/zoneinfo/EST5EDT /etc/localtime
```

5.	Restart networking and show interface info

```
# set up security, restart networking and show interfaces
chmod 644 /etc/systemd/network/99-dhcp-en.network
systemctl restart systemd-networkd
ifconfig
```
	

6.	Permit ping in and out & some common web ports

```
# change and save iptables
iptables -A OUTPUT -p icmp -j ACCEPT
iptables -A INPUT -p icmp -j ACCEPT
iptables -A INPUT -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -p tcp --dport 443 -j ACCEPT
iptables -A INPUT -p tcp --dport 8443 -j ACCEPT
iptables-save >/etc/systemd/scripts/ip4save
```


7.	Start and Enable Docker

```
# update to latest docker version
yum update -y

# start docker for the first time
systemctl start docker

# enable docker to start automatically
systemctl enable docker

# check that it is working
docker info
docker run hello-world
```


8.	Install Docker Compose (not pre-packaged for Photon OS)

```
DOCKER_CONFIG=${DOCKER_CONFIG:-$HOME/.docker}
mkdir -p $DOCKER_CONFIG/cli-plugins
curl -SL https://github.com/docker/compose/releases/download/v2.11.2/docker-compose-linux-x86_64 -o $DOCKER_CONFIG/cli-plugins/docker-compose

chmod +x $DOCKER_CONFIG/cli-plugins/docker-compose

docker compose version
```

9.	Install PIP

```
curl https://bootstrap.pypa.io/get-pip.py --output get-pip.py 
python get-pip.py
```

10.	Install GIT

```
yum install git
```


11.	Install Cisco YangSuite

      a. Clone the YangSuite Repo

```
git clone https://github.com/CiscoDevNet/yangsuite
```

      b. Start the YangSuite

```
cd yangsuite/docker
./start_yang_suite.sh
```


12.	If running on a remote server we need to modify the ALLOWED_HOSTS to permit all http HOST HEADERS. 

      a. Change directory to the /docker/yangsuite folder.

```
cd docker/yangsuite/ 
```

      b. Edit the production.py file. 

```
vi production.py
```

      c. In production.py comment out or remove entirely the entire DJANGO_ALLOWED_HOSTS code block and add ALLOWED_HOSTS = ‘*’. 
https://docs.djangoproject.com/en/4.1/ref/settings/
 



















