# Ubuntu OVA image running a TIG Stack (Telegraf, InfluxDB, and Grafana) on Ubuntu 18.04 LTS
OVA Image running a TIG Stack on Ubuntu to graph Call Manager Perfmon and Risport data

# Access/Credentials

### Ubuntu OS
* username: dashadmin
* password: *TietERison*

### InfluxDB
* username: none
* password: none

### Grafana
* username: admin
* password: OmituRepRa

# Ubuntu Settings

### View IP Address
```
$ ip addr show ens33 | grep inet | awk '{ print $2; }' | sed 's/\/.*$//'
```

### Set static IP address
https://www.howtoforge.com/linux-basics-set-a-static-ip-on-ubuntu

### PM2 Commands (Process manager running python scripts)

```
$ pm2 [list|ls|status]
$ pm2 flush
$ pm2 log
$ pm2 restart app_name
$ pm2 reload app_name
$ pm2 stop app_name
$ pm2 delete app_name
$ pm2 start perfmon_arg.py --interpreter python3 --name thread --cron '*/5 * * * *' --no-autorestart -- -ip 10.10.20.1 10.10.20.2 -u administrator -p ciscopsdt -c 'Cisco CallManager'
$ pm2 start  cisco_axl_jabber.py --interpreter python3 --name jabber_status --cron '*/5 * * * *' --no-autorestart -- -ip 170.2.96.82 -u wordenj -p Timbers2019! -v 12.0
$ pm2 save or pm2 set pm2:autodump true
$ pm2 stop all
$ pm2 show <id|name>
$ pm2 startup
$ pm2 monit
```

### SFTP Configuration (This is already preconfigured)
```
$ sudo groupadd sftp
$ sudo nano /etc/ssh/sshd_config
```

#### Paste at bottom
```
Match group sftp
ChrootDirectory /
X11Forwarding no
AllowTcpForwarding no
```
#### Add user to group
```
$ sudo usermod -a -G dashadmin sftp
```

### InfluxDB commands

* InfluxDB is already configured with sample data in it from Cisco's DevNet CUCM's

```
$ sudo nano /etc/influxdb/influxdb.conf
$ influx -precision rfc3339
> show databases
> CREATE DATABASE cisco_perfmon WITH DURATION 90d
> use cisco_perfmon
> show measurements
> CREATE DATABASE cisco_risport WITH DURATION 90d
> use cisco_risport
> show measurements
```
* If you'd like to remove the data and start fresh
```
$ influx -precision rfc3339
> drop database cisco_perfmon
> CREATE DATABASE cisco_perfmon WITH DURATION 90d
> drop database cisco_risport
> CREATE DATABASE cisco_risport WITH DURATION 90d
```

###Telegraf
$ sudo nano /etc/telegraf/telegraf.conf
$ sudo service telegraf restart

$ telegraf -test -config /etc/telegraf/telegraf.conf
$ snmpwalk -c dashboardRO -v 2c 10.10.20.1 -m +CISCO-CCM-MIB 1.3.6.1.4.1.9.9.156
$ cd /usr/share/snmp/mibs


https://angristan.xyz/2018/04/monitoring-telegraf-influxdb-grafana/
