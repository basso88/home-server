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
- Safe shut down systemd service | /etc/systemd/system/safe_shutdown.service
 
#### Scripts

##### DDNS update
`~/duckdns/ddns_update.sh`

```bash
#!/bin/bash

echo url="https://www.duckdns.org/update?domains=corlud-server&token=token_goes_here=" | curl -k -o ~/duckdns/duck.log -K -
```
I want the publicIP to be updating every 5 minutes...
Open crontab
`crontab -e` 
Copy and paste
```bash
#DuckDNS public IP check
*/5 * * * * ~/duckdns/ddns_update.sh >/dev/null 2>&1
```

##### Safe shutdown
~/safe_shutdown.sh

```bash
#!/bin/bash

# Home server safe shutdown triggered by router unreachable (power outage detection)
router="192.168.1.1"

count=0
while true; do
    ping -c 1 $router > /dev/null 2>&1 #ping -c 1 pings once per loop
    if [ $? -ne 0 ]; then
        ((count++))
    else
        count=0
    fi

    if [ $count -eq 12 ]; then
        echo "$(date) | router has been unreachable for 1 minute, system shutting down..." >> /var/log/autoshutdown.log
        shutdown -h now
            break
    fi
    sleep 5
done 
```
