# SNMP_Learning : ROCOMMUNITY

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
# Separation Agent et Manager : ROCOMMUNITY : Agent + Manager
## Etape1 : Lancer un conteneur Ubuntu nommé snmp-agent
``` 
docker run -itd --name snmp-agent --hostname snmp-agent ubuntu:24.04
``` 

## Accéder au conteneur
``` 
docker exec -it snmp-agent bash
``` 
## Installer SNMP + activer le service
``` 
apt-get update
apt-get install -y snmp snmpd snmp-mibs-downloader
``` 
## Configurer SNMP Agent (fichier /etc/snmp/snmpd.conf)
``` 
echo "rocommunity public" > /etc/snmp/snmpd.conf
echo "agentAddress udp:161" >> /etc/snmp/snmpd.conf
echo "sysLocation \"Unknown\"" >> /etc/snmp/snmpd.conf
echo "sysContact \"Unknown\"" >> /etc/snmp/snmpd.conf
```
## Lancer SNMP Agent

```
service snmpd start
service snmpd status
```
## Étape 2 : Créer le conteneur Manager SNMP
Tapez ctrl+shift+T
## Lancer un conteneur Ubuntu nommé snmp-manager
```
docker run -itd --name snmp-manager --hostname snmp-manager ubuntu:24.04
```

## Accéder au conteneur
```
docker exec -it snmp-manager bash
```
## Installer SNMP client
```
apt-get update
apt-get install -y snmp snmp-mibs-downloader
```
## Etape 3 : Communication entre Manager et Agent
## Récupérer l'IP du conteneur agent

```
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' snmp-agent
```
Obtenir <ip_addr_agent>
## Tester la communication depuis le manager
```
snmpwalk -v2c -c public <ip_addr_agent>
```

## Étape 4 : Interroger des MIBs spécifiques
* Nom de l'hôte (sysName)
```
snmpwalk -v2c -c public <ip_addr_agent> 1.3.6.1.2.1.1.5.0
```
* Emplacement (sysLocation)
```
snmpwalk -v2c -c public <ip_addr_agent> iso.3.6.1.2.1.1.6.0
```
* Contact (sysContact)
```
snmpwalk -v2c -c public <ip_addr_agent> iso.3.6.1.2.1.1.4.0
```

## Étape 5 : MIB pour Mémoire, CPU, Disque et Réseau
* Mémoire RAM
Mémoire totale

```
snmpwalk -v2c -c public <ip_addr_agent> 1.3.6.1.2.1.25.2.2.0
```
Utilisation mémoire

```
snmpwalk -v2c -c public <ip_addr_agent> 1.3.6.1.2.1.25.2.3.1.6
```

* Disque dur

Espace disque total

```
snmpwalk -v2c -c public <ip_addr_agent> 1.3.6.1.2.1.25.2.3.1.5
```

Espace disque libre

```
snmpwalk -v2c -c public <ip_addr_agent> 1.3.6.1.2.1.25.2.3.1.6
```

* CPU

```
snmpwalk -v2c -c public <ip_addr_agent> 1.3.6.1.2.1.25.3.3.1.2
```

* Réseau
Interfaces réseau

```
snmpwalk -v2c -c public <ip_addr_agent> 1.3.6.1.2.1.2.2.1.2
```
Octets reçus
```
snmpwalk -v2c -c public <ip_addr_agent> 1.3.6.1.2.1.2.2.1.10
```
Octets envoyés
```
snmpwalk -v2c -c public <ip_addr_agent> 1.3.6.1.2.1.2.2.1.16
```
