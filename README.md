# COMMANDE SNMP
Commandes SNMP (paquet snmp)
## 1. snmpwalk
Parcourt récursivement un sous-arbre d’un agent SNMP
``` 
snmpwalk -v2c -c <community> <host> <OID>
``` 
Exemple : 
``` 
snmpwalk -v2c -c public 192.168.1.100 1.3.6.1.2.1.1
``` 

</br>

## 2. snmpget
Récupère une seule valeur SNMP
``` 
snmpget -v2c -c <community> <host> <OID>
``` 
Exemple :
``` 
snmpget -v2c -c public 192.168.1.100 1.3.6.1.2.1.1.5.0
``` 

## 3. snmpset
Modifie une valeur SNMP (si autorisé)
``` 
snmpset -v2c -c <community> <host> <OID> <type> <value>
``` 
Exemple :
```
snmpset -v2c -c private 192.168.1.100 1.3.6.1.2.1.1.6.0 s "DataCenter"
```
Types courants : </br>
i = INTEGER  </br>
u = UNSIGNED  </br>
t = TIMETICKS  </br>
a = IPADDRESS  </br>
o = OID  </br>
s = STRING  </br>
x = HEX STRING  </br>

## 4. snmpbulkwalk
Comme snmpwalk, mais plus rapide pour gros volumes (SNMPv2c et v3)
```
snmpbulkwalk -v2c -c public 192.168.1.100
```
## 5. snmpstatus
Résumé rapide : état, nom, uptime, interfaces...
```
snmpstatus -v2c -c public 192.168.1.100
```
## 6. snmpdf
Affiche l’espace disque (comme df)
```
snmpdf -v2c -c public 192.168.1.100
```
## 7. snmpnetstat
Affiche l’état réseau d’un hôte (comme netstat)
```
snmpnetstat -v2c -c public 192.168.1.100 -r
```

# Installation download-mib
```
ls /usr/share/snmp/mibs/
```
Éviter les messages "Unknown Object Identifier"
* Ajouter le chemin du mibs dans : /etc/snmp/snmpd.conf

```
root@snmp-agent:/# cat /etc/snmp/snmp.conf
# As the snmp packages come without MIB files due to license reasons, loading
# of MIBs is disabled by default. If you added the MIBs you can reenable
# loading them by commenting out the following line.
mibs :

# If you want to globally change where snmp libraries, commands and daemons
# look for MIBS, change the line below. Note you can set this for individual
# tools with the -M option or MIBDIRS environment variable.
#
mibdirs /usr/share/snmp/mibs:/usr/share/snmp/mibs/iana:/usr/share/snmp/mibs/ietf
```
* Tester l’interprétation avec MIBs
Au lieu de voir juste des OIDs numériques, tu verras les noms lisibles :
```
snmpwalk -v2c -c public 192.168.1.100 system
```
Résultat lisible comme SNMPv2-MIB::sysName.0 = STRING: myhost

</br>
  <table>
    <thead>
      <tr>
        <th>Commande</th>
        <th>Utilité principale</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td>snmpwalk</td>
        <td>Parcourir un sous-arbre SNMP</td>
      </tr>
      <tr>
        <td>snmpget</td>
        <td>Récupérer une seule valeur</td>
      </tr>
      <tr>
        <td>snmpset</td>
        <td>Modifier une valeur (si autorisé)</td>
      </tr>
      <tr>
        <td>snmpbulkwalk</td>
        <td>Version rapide de snmpwalk (SNMPv2c)</td>
      </tr>
      <tr>
        <td>snmpstatus</td>
        <td>Résumé rapide de l’état d’un hôte SNMP</td>
      </tr>
      <tr>
        <td>snmpdf</td>
        <td>Infos sur l’espace disque (comme df)</td>
      </tr>
      <tr>
        <td>snmpnetstat</td>
        <td>Infos sur le réseau (comme netstat)</td>
      </tr>
      <tr>
        <td>download-mibs</td>
        <td>Télécharger tous les fichiers MIB disponibles</td>
      </tr>
    </tbody>
  </table>

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
