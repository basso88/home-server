This repo contains the Docker-based configuration for my personal server plus other useful scripts.

🚀 Services Deployed:
    Pihole
    nginx
    Vaultwarden
    Transmission
    Jellyfin


File structure
```
homelab/
├── pihole/
│   └── docker-compose.yml
├── npm/
│   └── docker-compose.yml
├── transmission/
│   └── docker-compose.yml
├── jellyfin/
│   └── docker-compose.yml
└── vaultwarden/
    └── docker-compose.yml
```


## Configuration files directories

- Samba | /etc/samba/smb.conf
- CUPS | /etc/cups
- Safe shut down systemd service | ...
 
## Scripts

#### DDNS update

`~/duckdns/ddns_update`

```bash
#!/usr/bin/env bash

token= <token>
domain=bassoserver

echo url="https://www.duckdns.org/update?domains=$domain&token=$token&ip=" | curl -k -o ~/duckdns/duck.log -K -
echo -e "\n" >> ~/duckdns/duck.log
echo last update: &(date) >> ~/duckdns/duck.log
```

I want the publicIP to be updating every 5 minutes...

`crontab -e` 

`#DuckDNS public IP check`
`*/5 * * * * ~/duckdns/ddns_update`

#### Auto Update
~/automations/autoupdate

```bash
apt update && apt upgrade -y > /home/basso/autoupdate.log 2>&1
echo "$(date)" >> /home/basso/autoupdate.log
```
Update every sunday at 6PM

`sudo crontab -e`

`#System update`
`0 18 * * 0 /home/basso/automations/autoupdate`

#### Safe shutdown (unfinished)
~/safe_shutdown.sh

```bash
router="192.168.1.1"
log="/home/basso/safe_shutdown.log"

count=0
while true; do
	if ping -c 1 $router > /dev/null 2>&1; then
		echo "power ok, $(date)" > $log
		count=0
		sleep 5
	else
		echo "power out, system shutting down..." >> $log
		((count++))
		sleep 5
	fi
done
```
