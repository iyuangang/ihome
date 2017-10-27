# My main issues were:

The location of my homebridge binary. Step 2
Permissions were not correct and the service failed to load. Step 7
I needed the persist folder in /var/homebridge directory. Step 6
Here are the steps that worked for me:
1. `sudo vim /etc/default/homebridge` and paste this 
```
# Defaults / Configuration options for homebridge
# The following settings tells homebridge where to find the config.json file and where to persist the data (i.e. pairing and others)
HOMEBRIDGE_OPTS=-U /var/homebridge

# If you uncomment the following line, homebridge will log more 
# You can display this via systemd's journalctl: journalctl -f -u homebridge
# DEBUG=*
```
2. `sudo vim /etc/systemd/system/homebridge.service` and paste this 
```
[Unit]
Description=Node.js HomeKit Server 
After=syslog.target network-online.target

[Service]
Type=simple
User=homebridge
EnvironmentFile=/etc/default/homebridge
ExecStart=/usr/local/bin/homebridge $HOMEBRIDGE_OPTS
Restart=on-failure
RestartSec=10
KillMode=process

[Install]
WantedBy=multi-user.target
```
I had to remove local from:  ExecStart=/usr/local/bin/homebridge $HOMEBRIDGE_OPTS  because my homebridge installed in /usr/bin/

3. Create a user to run service: `sudo useradd --system homebridge`

4. `sudo mkdir /var/homebridge`

5. `sudo cp ~/.homebridge/config.json /var/homebridge/`

This copies your current user’s config. This assumes you have already added accessories etc.

6. `sudo cp -r ~/.homebridge/persist /var/homebridge`

7. `sudo chmod -R 0777 /var/homebridge`

8. `sudo systemctl daemon-reload`

9. `sudo systemctl enable homebridge`

10. `sudo systemctl start homebridge`

11. Type `systemctl status homebridge` to check the status of the service.


Hopefully this helps anyone who is having trouble with Homebridge starting on boot on a Raspberry Pi. Please comment below if you have any questions.
