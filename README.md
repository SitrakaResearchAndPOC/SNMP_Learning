# SNMP_Learning

``` 
docker run -itd --name snmp-container --hostname snmp-container ubuntu:24.04
``` 
``` 
docker exec -it snmp-container bash
``` 
``` 
apt-get update
``` 
``` 
apt-get install -y snmp snmpd snmp-mibs-downloader
``` 
``` 
echo "rocommunity public" > /etc/snmp/snmpd.conf
echo "agentAddress udp:161" >> /etc/snmp/snmpd.conf
echo "sysLocation \"Unknown\"" >> /etc/snmp/snmpd.conf
echo "sysContact \"Unknown\"" >> /etc/snmp/snmpd.conf
``` 
``` 
service snmpd start
``` 
``` 
service snmpd status
``` 
``` 
snmpwalk -v2c -c public localhost
```
MIB SysName :
``` 
snmpwalk -v2c -c public localhost 1.3.6.1.2.1.1.5
``` 
MIB sysLocation :
``` 
snmpwalk -v2c -c public localhost iso.3.6.1.2.1.1.6.0
``` 
MIB syscontact: 
```
snmpwalk -v2c -c public localhost iso.3.6.1.2.1.1.4.0
``` 



