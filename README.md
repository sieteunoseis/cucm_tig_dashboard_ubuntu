# cucm_tig_dashboard_ubuntu
OVA Image TIG Stack Ubuntu

# Access/Credentials

# Ubuntu OS
dashadmin
TietERison

# View IP Address
$ ip addr show ens33 | grep inet | awk '{ print $2; }' | sed 's/\/.*$//'

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

#SFTP Configuration
$ sudo groupadd sftp
$ sudo nano /etc/ssh/sshd_config

Paste at bottom
Match group sftp
ChrootDirectory /
X11Forwarding no
AllowTcpForwarding no

$ sudo usermod -a -G dashadmin sftp

InfluxDB
no username/password

$ sudo nano /etc/influxdb/influxdb.conf
$ influx -precision rfc3339
> show databases
> CREATE DATABASE cisco_perfmon WITH DURATION 90d
> use cisco_perfmon
> show measurements

# Grafana
admin
OmituRepRa

Telegraf
$ sudo nano /etc/telegraf/telegraf.conf
$ sudo service telegraf restart

$ telegraf -test -config /etc/telegraf/telegraf.conf
$ snmpwalk -c dashboardRO -v 2c 10.10.20.1 -m +CISCO-CCM-MIB 1.3.6.1.4.1.9.9.156
$ cd /usr/share/snmp/mibs


https://angristan.xyz/2018/04/monitoring-telegraf-influxdb-grafana/
