Partie packet tracer :

L'ensemble de l'infra peut communiquer en utilisant une partie de routage statique (sur le routeur 1 et 2) et du routage EIGRP sur l'ensemble des routeurs en déclarant les réseaux de chaque routeur. 

R1 - 10.0.1.0

interface FastEthernet0/0
 ip address 10.0.1.254 255.255.255.0
 duplex auto
 speed auto
!
interface FastEthernet1/0
 no ip address
 duplex auto
 speed auto
!
interface Serial2/0
 ip address 40.0.1.1 255.255.255.252
 clock rate 1200
!
interface Serial3/0
 ip address 30.0.1.1 255.255.255.252
 clock rate 2000000
!
interface FastEthernet4/0
 no ip address
 shutdown
!
interface FastEthernet5/0
 no ip address
 shutdown
!
interface Modem6/0
 no ip address
!
interface Modem7/0
 no ip address
!
router eigrp 1
 redistribute connected 
 network 40.0.1.0 0.0.0.3
 network 30.0.1.0 0.0.0.3
 network 10.0.1.0 0.0.0.255
 auto-summary
!
ip classless
ip route 20.0.1.0 255.255.255.0 30.0.1.2

====================================================================================

R2 - 20.0.1.0
interface FastEthernet0/0
 ip address 20.0.1.254 255.255.255.0
 duplex auto
 speed auto
!
interface FastEthernet1/0
 no ip address
 duplex auto
 speed auto
!
interface Serial2/0
 ip address 50.0.1.1 255.255.255.252
 clock rate 2000000
!
interface Serial3/0
 ip address 30.0.1.2 255.255.255.252
!
interface FastEthernet4/0
 no ip address
 shutdown
!
interface FastEthernet5/0
 no ip address
 shutdown
!
interface Modem6/0
 no ip address
!
interface Modem7/0
 no ip address
!
router eigrp 1
 redistribute connected 
 network 20.0.1.0 0.0.0.255
 network 30.0.1.0 0.0.0.3
 network 50.0.1.0 0.0.0.3
 auto-summary
!
ip classless
ip route 10.0.1.0 255.255.255.0 30.0.1.1

====================================================================================

R3 - 60.0.1.0

interface FastEthernet0/0
 ip address 60.0.1.1 255.255.255.0
 duplex auto
 speed auto
!
interface FastEthernet1/0
 no ip address
 duplex auto
 speed auto
 shutdown
!
interface Serial2/0
 ip address 40.0.1.2 255.255.255.252
!
interface Serial3/0
 ip address 50.0.1.2 255.255.255.252
!
interface FastEthernet4/0
 no ip address
 shutdown
!
interface FastEthernet5/0
 no ip address
 shutdown
!
interface Modem6/0
 no ip address
!
interface Modem7/0
 no ip address
!
router eigrp 1
 redistribute connected 
 network 60.0.1.0 0.0.0.255
 network 40.0.1.0 0.0.0.3
 network 50.0.1.0 0.0.0.3
 auto-summary
!
ip classless

====================================================================================
====================================================================================
====================================================================================
====================================================================================
====================================================================================

Partie Machine virtuelles : 

landing-vm1 :

La commande utilisée pour changer les adresses est : nmtui

L'outil qui a été utilisé pour réaliser la partition a été 'parted'

Le dimensionnement a été fait en selectionnant le volume à particionner : 

sudo parted /dev/sdb

Avec la commande parted -l nous constatons le découpage du disque en deux partitions : 

Model: ATA UBOX HARDDISK (scsi)
Disk /dev/sda: 21.5GB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags:

Number  Start   End     Size    File system  Name                  Flags
1       1049kB  630MB   629MB   fat32        EFI System partition  boot, esp
2       630MB   1704MB  1704MB  xfs
3       1704MB  21.5GB  19.8GB                                     lvm

Model: ATA UBOX HARDDISK (scsi)
Disk /dev/sdb: 10.7GB
Sector size (logical/physical): 5128/512B
Partition Table: gpt
Disk Flags:


Number  Start    End     Size    File system  Name     Flags
1       1049kB   5000MB  4999MB               primary
2       5000MB   10.0GB  5001MB               primary

Les deux partitions sont biens présentes. 

La commande pour rendre executable un fichier bash est chmod +x 'nom_du_fichier'



Script Checking :

Voici le script :

#!/bin/bash

sudo dnf check-update

echo -e "
=======================================================
                   SCRIPT CHECKING
=======================================================
"

echo "dois-je faire la mise à jour ? (--update)"

read update

if [[ Supdate == "--update" ]]; then dnf update

else

echo "le système ne sera pas mise à jour" fi

echo "le nom d'hôte est :" $(hostname) echo "la version du système est :" $(uname -r)

echo "la date est :" $(date) echo "l'adresse IPv4 est :"

ip addr show I awk '/inet {print $2}'

echo "l'adresse IPv6 est :"

ip -6 show I auk inet {print $2}'

echo "les serveurs DNS :"

cat /etc/resolv.conf

echo "Vidage des logs..."
journalctl --rotate 
journalctl --vacuum-time=1d

echo "Verification du pare-feu"
systemctl status firewalld