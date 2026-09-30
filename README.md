# sysLogConfiguration
Centralized log system using rsyslog

### Description 

This project describes the process of setting a centralized log system 

2. Goals 
- Comunicate the server and client of rsyslog to send and recive logs 
- Implement iptables rules 
- Get log of the services 


### Archithecture

[FOTOOOOOOOOOO]





### Requeriment 

- Linux Server Machine 
- Linux Client Machine 
- Internet connection 

## Instalación

In both roles (server and client), the installation is the same

Debian/

```
apt install rsyslog
```

Arch

```
pacman -S rsyslog 
```

### Classical configuration 

**In both setting, we use IP-CLIENt to refering a ip client, in last one, we use IP-SERVER to specify the ip server**

Server 

```
$template RemoteLogs,"/var/log/remotehosts/%HOSTNAME%/%$NOW%.%syslogseverity-text%.log"
if $FROMHOST-IP=='IP-CLIENT' then ?RemoteLogs
& stop
```

Client 

```
# For udp packages 
*.* @192.168.1.

# For tcp packages
*.* @192.168.1.
```

### RainerScript Configuration 

Server 
```
template(name="test" type="string" string="/var/log/remotehost/%fromhost-ip%/NTP/NTP.log")

if ( $FROMHOST-IP=="IP-CLIENT" ) then{
       action(type="omfile" dynaFile="test")
       stop
}

```

Client

```
if ($programname == "chronyd" ) then {
    action(type="omfile" file="/var/log/[NAME_FILE].log")
    action(type="omfwd" target="IP-SERVER" port="514" protocol="tcp")
}
```

### Logrotate

Logrotate is a utility used to manage log files and prevent them from growing indefinitely.

It rotates log files according to a defined schedule or size, and can also compress and remove old log files.

For example, a single log file can result in several rotated
versions:

    messages
    messages.1
    messages.2.gz
    messages.3.gz
    messages.4.gz

Here is an example configuration:

```
/var/log/messages {
    rotate 4
    weekly
    missingok
    notifempty
    compress
    delaycompress
    sharedscripts
    postrotate
        invoke-rc.d rsyslog rotate > /dev/null
    endscript
}
```

#### On systems using systemd, logrotate can be executed periodically via a timer

The corresponding files are: 

1. /usr/lib/systemd/system/logrotate.timer -> define when the process will be executed 
2. /usr/lib/systemd/system/logrotate.service -> 

The timer has the time where will be executed the logrotate.service, we can start manually to execute the service 

```
systemctl start logrotate.service 
```

Or we can use the following command to check the sintax and execute the file 

```
sudo logrotate -v /etc/logrotate.d/rsyslog 
```




10. Differences and similarities
10. Verificación
11. Troubleshooting
12. Rollback
13. Decisiones de diseño
14. Mejoras futuras





