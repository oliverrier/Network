# TP6 Topologie Réseau

## Sommaire

* I. [Préparation du lab](#i---préparation-du-lab)

* II. [Mise en place du lab](#ii---mise-en-place-du-lab)

## I- Présentation du lab

### Réseaux IP et aires OSPF

Tableaux résumant les réseaux et aires OSPF:

Réseaux | `area 0` | `area 1` | `area 2` | Commentaire
--- | --- | --- | --- | ---
`10.6.100.0/30` | X | - | - | Liaison entre `r1` et `r2`
`10.6.100.4/30` | X | - | - | Liaison entre `r1` et `r4`
`10.6.100.8/30` | X | - | - | Liaison entre `r2` et `r3` 
`10.6.100.12/30` | X | - | - | Liaison entre `r3` et `r4`
`10.6.101.0/30` | - | X | - | Liaison entre `r3` et `r5`
`10.6.201.0/24` | - | X | - | Réseau des clients
`10.6.202.0/24` | - | - | X | Réseau des serveurs

### Adressage IP de chacune des machines

Machines | `10.6.100.0/30` | `10.6.100.4/30` | `10.6.100.8/30` | `10.6.100.12/30` | `10.6.101.0/30` | `10.6.201.0/24` | `10.6.202.0/24`
--- | --- | --- | --- | --- | --- | --- | --- 
`r1.tp6.b1` | `10.6.100.1` | `10.6.100.5` | - | - | - | - | `10.6.202.254`
`r2.tp6.b1` | `10.6.100.2` | - |  `10.6.100.9` | - | - | - | -
`r3.tp6.b1` | - | - | `10.6.100.10` | `10.6.100.14` | `10.6.101.1` | - | -
`r4.tp6.b1` | - |  `10.6.100.6` | - | `10.6.100.13` | - | - | -
`r5.tp6.b1` | - | - | - | - |  `10.6.101.2` |  `10.6.201.254` | -
`client1.tp6.b1` | - | - | - | - | - |  `10.6.201.10` | -
`client2.tp6.b1` | - | - | - | - | - |  `10.6.201.11` | -
`server1.tp6.b1` | - | - | - | - | - | - | `10.6.202.10`

## II- Mise en place du lab

### Checklists

**Checklist IP Routeurs:**

On parle de `r1.tp6.b1`, `r2.tp6.b1`, `r3.tp6.b1`, `r4.tp6.b1` et `r5.tp6.b1` :

* [X] Définition des IPs statiques
* [X] Définition du nom de domaine

**Checklist VMs:**

On parle de `client1.tp6.b1`, `client2.tp6.b1` et `server1.tp6.b1` :

* [X] Désactiver SELinux
  * déja fait dans le patron
* [X] Installation de certains paquets réseau
  * déja fait dans le patron
* [X] Enlever la carte NAT
* [X] Définition des IPs statiques
* [X] Définition du nom de domaine
* [X] Remplir les fichiers `hosts`