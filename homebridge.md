# Mi Gateway work with Homebridge
## OS: openSUSE 42.3 aarch64 Linux 4.4.92-31
1. Install nodejs 
`sudo zypper in nodejs`
2. Install mDNS devel 
`sudo zypper in avahi-compat-mDNSResponder-devel`
3. Install libopenssl-devel
`sudo zypper in libopenssl-devel`
4. Install homebridge with npm 
`sudo npm install -g --unsafe-perm homebridge`
5. Install Mihome homebridge plugin 
`sudo npm install -g homebridge-aqara`
6. `vim ~/.homebridge/config.json` and paste this

Note! paste this and change "sid", "password" into yours

Note! "sid" must be lower-case letter.

```
{
        "bridge":
        {
                "name":"Homebridge",
                "username":"FF:00:FF:00:FF:00",
                "port":10101,
                "pin":"101-01-010"
        },
        "platforms":
        [
                {
                        "platform":"AqaraPlatform",
                        "sid":["34ce008ae052"],
                        "password":["68782BE4B8A64466"]
                }
        ]
} 
```

Note! Do not paste this. This is sample, modify this, if you want add more device.
```
{
    "bridge": {
        "name": "Homebridge",
        "username": "CC:22:3D:E3:CE:30",
        "port": 51826,
        "pin": "031-45-154"
    },
    
    "description": "This is an example configuration file with one fake accessory and one fake platform. You can use this as a template for creating your own configuration file containing devices you actually own.",

    "accessories": [
        {
            "accessory": "WeMo",
            "name": "Coffee Maker"
        }
    ],

    "platforms": [
        {
            "platform" : "PhilipsHue",
            "name" : "Hue"
        }
    ]
}
```
7. run `homebridge`

## Setup to start on bootup 

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
