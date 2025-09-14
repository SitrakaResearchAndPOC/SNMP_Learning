# COURS 1 : COMMANDE SNMP
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

# COURS 2 : Installation download-mib
```
ls /usr/share/snmp/mibs/
```
Éviter les messages "Unknown Object Identifier"
* Ajouter le chemin du mibs dans : /etc/snmp/snmpd.conf
```
nano /etc/snmp/snmp.conf
```
```
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

# EXERCICES 1 : SNMP_Learning : ROCOMMUNITY

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
# EXERCICES 2 : Separation Agent et Manager : ROCOMMUNITY : Agent + Manager
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

# EXERCICES 3 : SNMP avec RW COMMUNITY
```
docker rm -f snmp-container
```
```
docker run -itd --name snmp-container --hostname snmp-container -p 161:161/udp ubuntu:24.04
```
```
docker exec -it snmp-container bash
```
```
apt update
```
```
apt install -y snmpd snmp nano  snmp-mibs-downloader
```
```
nano /etc/snmp/snmpd.conf 
```
```
###########################################################################
#
# snmpd.conf
# An example configuration file for configuring the Net-SNMP agent ('snmpd')
# See snmpd.conf(5) man page for details
#
###########################################################################
# SECTION: System Information Setup
#

# syslocation: The [typically physical] location of the system.
#   Note that setting this value here means that when trying to
#   perform an snmp SET operation to the sysLocation.0 variable will make
#   the agent return the "notWritable" error code.  IE, including
#   this token in the snmpd.conf file will disable write access to
#   the variable.
#   arguments:  location_string
#rwuser    private
sysLocation    Sitting on the Dock of the Bay
sysContact     Me <me@example.org>

# sysservices: The proper value for the sysServices object.
#   arguments:  sysservices_number
sysServices    72



###########################################################################
# SECTION: Agent Operating Mode
#
#   This section defines how the agent will operate when it
#   is running.

# master: Should the agent operate as a master agent or not.
#   Currently, the only supported master agent type for this token
#   is "agentx".
#   
#   arguments: (on|yes|agentx|all|off|no)

master  agentx

# agentaddress: The IP address and port number that the agent will listen on.
#   By default the agent listens to any and all traffic from any
#   interface on the default SNMP port (161).  This allows you to
#   specify which address, interface, transport type and port(s) that you
#   want the agent to listen on.  Multiple definitions of this token
#   are concatenated together (using ':'s).
#   arguments: [transport:]port[@interface/address],...

# agentaddress  127.0.0.1,[::1]
agentaddress udp:161,udp6:[::]


###########################################################################
# SECTION: Access Control Setup
#
#   This section defines who is allowed to talk to your running
#   snmp agent.

# Views 
#   arguments viewname included [oid]

#  system + hrSystem groups only
# view   systemonly  included   .1.3.6.1.2.1.1
# view   systemonly  included   .1.3.6.1.2.1.25.1



# rocommunity: a SNMPv1/SNMPv2c read-only access community name
#   arguments:  community [default|hostname|network/bits] [oid | -V view]

# Read-only access to everyone to the systemonly view
# rocommunity  public default -V systemonly
# rocommunity6 public default -V systemonly
rocommunity  public default 
rocommunity6 public default 

rwcommunity private default 
rwcommunity6 private default 

# SNMPv3 doesn't use communities, but users with (optionally) an
# authentication and encryption string. This user needs to be created
# with what they can view with rouser/rwuser lines in this file.
#
# createUser username (MD5|SHA|SHA-512|SHA-384|SHA-256|SHA-224) authpassphrase [DES|AES] [privpassphrase]
# e.g.
# createuser authPrivUser SHA-512 myauthphrase AES myprivphrase
#
# This should be put into /var/lib/snmp/snmpd.conf 
#
# rouser: a SNMPv3 read-only access username
#    arguments: username [noauth|auth|priv [OID | -V VIEW [CONTEXT]]]
rouser authPrivUser authpriv -V systemonly

# include a all *.conf files in a directory
```
Démarrer et tester le services
```
service snmp start
```
```
service snmp status
```


```
snmpwalk -v2c -c public localhost 1.3.6.1.2.1.1.5.0
```
```
snmpset -v2c -c private localhost 1.3.6.1.2.1.1.5.0 s test
```
```
snmpwalk -v2c -c public localhost 1.3.6.1.2.1.1.5.0
```


# EXERCICE 4 :  Traps  

```
docker rm -f snmp-container
```
```
docker run -itd --name snmp-container --hostname snmp-container -p 161:161/udp  -p 162:162/udp  ubuntu:24.04
```
```
docker exec -it snmp-container bash
```
```
apt update
```
```
apt install -y snmpd snmp nano  snmp-mibs-downloader snmptrapd net-tools
```
* Pour ecouter le trap :
Activer l'ecoute de trap
```
nano /etc/snmp/snmptrapd.conf
```
```

#
# EXAMPLE-trap.conf:
#   An example configuration file for configuring the Net-SNMP snmptrapd agent.
#
###############################################################################
#
# This file is intended to only be an example.
# When the snmptrapd agent starts up, this is where it will look for it.
#
# All lines beginning with a '#' are comments and are intended for you
# to read.  All other lines are configuration commands for the agent.

#
# PLEASE: read the snmptrapd.conf(5) manual page as well!
#
authCommunity log,execute,net private　
authCommunity log,execute,net public
#
## send mail when get any events
#traphandle default /usr/bin/traptoemail -s smtp.example.org foobar@example.org
#
## send mail when get linkDown
#traphandle .1.3.6.1.6.3.1.1.5.3 /usr/bin/traptoemail -s smtp.example.org foobar@example.org
```

Lancer snmptrapd en mode console
```
snmptrapd -f -Lo
```
-f : ne pas daemoniser, rester en premier plan (utile pour debug) </br>
-Lo : logger les traps reçus sur la sortie standard (stdout) </br>

* Nouveau terminal pour lancer le traps : ctrl+shift+t
```
docker exec -it snmp-container bash
```
Ajouter l'activation de trap via : trapsink, trap2sink, inform2sink
```
ifconfig
```
<ip_addr> :  172.17.0.4

```
nano /etc/snmp/snmpd.conf 
```
Dans la configuration, nous utilisons trap2sink : trap2sink  <ip_addr> public
```
###########################################################################
#
# snmpd.conf
# An example configuration file for configuring the Net-SNMP agent ('snmpd')
# See snmpd.conf(5) man page for details
#
###########################################################################
# SECTION: System Information Setup
#

# syslocation: The [typically physical] location of the system.
#   Note that setting this value here means that when trying to
#   perform an snmp SET operation to the sysLocation.0 variable will make
#   the agent return the "notWritable" error code.  IE, including
#   this token in the snmpd.conf file will disable write access to
#   the variable.
#   arguments:  location_string
sysLocation    Sitting on the Dock of the Bay
sysContact     Me <me@example.org>

# sysservices: The proper value for the sysServices object.
#   arguments:  sysservices_number
sysServices    72



###########################################################################
# SECTION: Agent Operating Mode
#
#   This section defines how the agent will operate when it
#   is running.

# master: Should the agent operate as a master agent or not.
#   Currently, the only supported master agent type for this token
#   is "agentx".
#   
#   arguments: (on|yes|agentx|all|off|no)

master  agentx

# agentaddress: The IP address and port number that the agent will listen on.
#   By default the agent listens to any and all traffic from any
#   interface on the default SNMP port (161).  This allows you to
#   specify which address, interface, transport type and port(s) that you
#   want the agent to listen on.  Multiple definitions of this token
#   are concatenated together (using ':'s).
#   arguments: [transport:]port[@interface/address],...

agentaddress  127.0.0.1,[::1]



###########################################################################
# SECTION: Access Control Setup
#
#   This section defines who is allowed to talk to your running
#   snmp agent.

# Views 
#   arguments viewname included [oid]

#  system + hrSystem groups only
view   systemonly  included   .1.3.6.1.2.1.1
view   systemonly  included   .1.3.6.1.2.1.25.1


# rocommunity: a SNMPv1/SNMPv2c read-only access community name
#   arguments:  community [default|hostname|network/bits] [oid | -V view]

# Read-only access to everyone to the systemonly view
rocommunity  public default -V systemonly
rocommunity6 public default -V systemonly

# SNMPv3 doesn't use communities, but users with (optionally) an
# authentication and encryption string. This user needs to be created
# with what they can view with rouser/rwuser lines in this file.
#
# createUser username (MD5|SHA|SHA-512|SHA-384|SHA-256|SHA-224) authpassphrase [DES|AES] [privpassphrase]
# e.g.
# createuser authPrivUser SHA-512 myauthphrase AES myprivphrase
#
# This should be put into /var/lib/snmp/snmpd.conf 
#
# rouser: a SNMPv3 read-only access username
#    arguments: username [noauth|auth|priv [OID | -V VIEW [CONTEXT]]]
rouser authPrivUser authpriv -V systemonly

# include a all *.conf files in a directory
includeDir /etc/snmp/snmpd.conf.d
trap2sink  <ip_addr> public
```
```
snmptrap -v 2c -c public localhost ''   .1.3.6.1.6.3.1.1.5.1   .1.3.6.1.2.1.1.3.0 s "Trap test depuis agent"
```

## COURS 3 : trapsink, trap2sink, inform2sink
* 1. trapsink
Envoie des traps SNMP v1 uniquement. </br>
Trap = notification non confirmée envoyée par l’agent SNMP à un manager. </br>
Pas d’accusé de réception : l’agent envoie le trap et ne sait pas si le manager l’a reçu. </br>
Syntaxe classique pour les vieilles infrastructures SNMP. </br>

2. trap2sink
Envoie des traps SNMP v2c (version 2 community-based). </br>
Toujours non confirmés (pas d’ACK), comme pour trapsink. </br>
Supporte le format plus riche SNMP v2 (plus d’informations, types améliorés). </br>
C’est la version recommandée si tu utilises SNMP v2. </br>

3. informsink (ou inform2sink)
Envoie des informs SNMP v2c. </br>
Informs = traps confirmés, c’est-à-dire que le manager doit envoyer un accusé de réception à l’agent. </br>
Si le manager ne répond pas, l’agent peut réessayer d’envoyer le message. </br>
Permet une communication plus fiable, avec gestion des pertes. </br>
Plus “coûteux” en overhead réseau car implique des échanges supplémentaires. </br>
 <table>
    <caption>Résumé des directives SNMP</caption>
    <thead>
      <tr>
        <th>Directive</th>
        <th>Version SNMP</th>
        <th>Type de notification</th>
        <th>Confirmation requise</th>
        <th>Usage recommandé pour</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td>trapsink</td>
        <td>SNMP v1</td>
        <td>Trap (non confirmé)</td>
        <td>Non</td>
        <td>Compatibilité v1</td>
      </tr>
      <tr>
        <td>trap2sink</td>
        <td>SNMP v2c</td>
        <td>Trap (non confirmé)</td>
        <td>Non</td>
        <td>SNMP v2, simple</td>
      </tr>
      <tr>
        <td>informsink</td>
        <td>SNMP v2c</td>
        <td>Inform (confirmé)</td>
        <td>Oui</td>
        <td>Notifications fiables</td>
      </tr>
    </tbody>
  </table>

  
