# Ubuntu OVA image running a TIG Stack (Telegraf, InfluxDB, and Grafana) on Ubuntu 18.04 LTS
OVA Image running a TIG Stack on Ubuntu to graph Call Manager Perfmon and Risport data

[Download](https://github.com/sieteunoseis/cucm_tig_dashboard_ubuntu/releases/download/v1.0/CUCM_TIG_Ubuntu_18.04.4.ova)

# Access/Credentials

### Ubuntu OS
* username: dashadmin
* password: *TietERison*

### InfluxDB
* http://localhost:8086/
* username: none
* password: none

### Grafana
* http://localhost:3000/
* username: admin
* password: OmituRepRa

Nginx is install if you want to create a reverse proxy to port 3000.

* [Grafana Config](https://grafana.com/tutorials/run-grafana-behind-a-proxy/#0)
* [NGINX Config](https://grafana.com/tutorials/run-grafana-behind-a-proxy/#1)

# Ubuntu Settings

### View IP Address
```
$ ip addr show ens33 | grep inet | awk '{ print $2; }' | sed 's/\/.*$//'
```

### Set static IP address
https://www.howtoforge.com/linux-basics-set-a-static-ip-on-ubuntu

### PM2 Commands (Process manager running python scripts)

PM2 is already set up to run scripts on Cisco's DevNet 12.5 Collaboration Sandbox. You'll need to delete all the processes and add new ones for your IP addresses. I created an Excel spreadsheet to help create the command lines for this step. The Jabber Registration example is below.

```
$ pm2 [list|ls|status]
$ pm2 flush
$ pm2 log
$ pm2 restart app_name
$ pm2 reload app_name
$ pm2 stop app_name
$ pm2 delete app_name
$ pm2 start perfmon_arg.py --interpreter python3 --name thread --cron '*/5 * * * *' --no-autorestart -- -ip 10.10.20.1 10.10.20.2 -u administrator -p ciscopsdt -c 'Cisco CallManager'
$ pm2 start  cisco_axl_jabber.py --interpreter python3 --name jabber_status --cron '*/5 * * * *' --no-autorestart -- -ip 10.10.20.1 -u administrator -p ciscopsdt -v 12.0
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

InfluxDB is already configured with sample data in it from Cisco's DevNet CUCM's

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
If you'd like to remove the data and start fresh do the following:

```
$ influx -precision rfc3339
> drop database cisco_perfmon
> CREATE DATABASE cisco_perfmon WITH DURATION 90d
> drop database cisco_risport
> CREATE DATABASE cisco_risport WITH DURATION 90d
> drop database telegraf
> CREATE DATABASE telegraf WITH DURATION 90d
```

### Telegraf

Telgraf config file needs to be edit with updated IP addresses. Telegraf is used to pull stats from CUCM via SNMP. This was done due to a bug in the Perfmon API.

[CSCvn19112](https://bst.cloudapps.cisco.com/bugsearch/bug/CSCvn19112/?rfs=iqvred)

```
$ sudo nano /etc/telegraf/telegraf.conf
$ sudo service telegraf restart
$ telegraf -test -config /etc/telegraf/telegraf.conf
$ snmpwalk -c dashboardRO -v 2c 10.10.20.1 -m +CISCO-CCM-MIB 1.3.6.1.4.1.9.9.156
$ cd /usr/share/snmp/mibs
```

Majority of the telegraf configuration was from this guide:

https://angristan.xyz/2018/04/monitoring-telegraf-influxdb-grafana/

### Python Scripts

Scripts are in the /home/dashboard/development directory. They are pulled from the following:

[Perfmon](https://github.com/sieteunoseis/cisco_perfmon_influxdb)
[Jabber Risport](https://github.com/sieteunoseis/cisco_risport_influxdb)
