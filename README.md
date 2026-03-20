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


#### Configuration files directory

- Samba | /etc/samba/smb.conf
- CUPS | /etc/cups
- Safe shut down systemd service | ...
 
#### Scripts

##### DDNS update
`~/duckdns/ddns_update

```bash
#!/usr/bin/env bash

token= <token>
domain=bassoserver

echo url="https://www.duckdns.org/update?domains=$domain&token=$token&ip=" | curl -k -o ~/duckdns/duck.log -K -
echo -e "\n" >> ~/duckdns/duck.log
echo last update: &(date) >> ~/duckdns/duck.log
```

I want the publicIP to be updating every 5 minutes...
Open crontab
`crontab -e` 
Copy and paste
```bash
#DuckDNS public IP check
*/5 * * * * ~/duckdns/ddns_update
```

##### Safe shutdown (unfinished)
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
