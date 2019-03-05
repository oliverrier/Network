# TP6 Topologie Réseau

## Sommaire

* I. [Préparation du lab](#i---préparation-du-lab)

* II. [Mise en place du lab](#ii---mise-en-place-du-lab)

* III. [Let's end this properly](#iii---let-s-end-this-properly)

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

### Configuration OSPF

*Configs*

* [r1.tp6](./configs/configR1.txt)
* [r2.tp6](./configs/configR2.txt)
* [r3.tp6](./configs/configR3.txt)
* [r4.tp6](./configs/configR4.txt)
* [r5.tp6](./configs/configR5.txt)

*traceroute*

* `client1` -> `server1`:

```bash
[iroh@client1 ~]$ traceroute server1
traceroute to server1 (10.6.202.10), 30 hops max, 60 byte packets
 1  gateway (10.6.201.254)  4.200 ms  4.267 ms  4.132 ms
 2  10.6.101.1 (10.6.101.1)  16.507 ms  16.392 ms  16.273 ms
 3  10.6.100.9 (10.6.100.9)  30.210 ms  31.302 ms  31.450 ms
 4  10.6.100.1 (10.6.100.1)  53.192 ms  53.033 ms  52.992 ms
 5  server1 (10.6.202.10)  65.502 ms !X  66.025 ms !X  65.944 ms !X
 ```

* `client2` -> `server1`:

```bash
[iroh@client2 ~]$ traceroute server1
traceroute to server1 (10.6.202.10), 30 hops max, 60 byte packets
 1  gateway (10.6.201.254)  10.398 ms  12.375 ms  12.630 ms
 2  10.6.101.1 (10.6.101.1)  25.294 ms  26.751 ms  26.834 ms
 3  10.6.100.9 (10.6.100.9)  52.240 ms  52.893 ms  52.671 ms
 4  10.6.100.1 (10.6.100.1)  63.338 ms  63.221 ms  63.087 ms
 5  server1 (10.6.202.10)  62.949 ms !X  62.782 ms !X  62.642 ms !X
```

* `server1` -> `client1`:

```bash
[iroh@server1 ~]$ traceroute client1
traceroute to client1 (10.6.201.10), 30 hops max, 60 byte packets
 1  gateway (10.6.202.254)  3.058 ms  2.929 ms  2.815 ms
 2  10.6.100.2 (10.6.100.2)  16.926 ms  16.842 ms  16.721 ms
 3  10.6.100.10 (10.6.100.10)  28.367 ms  28.545 ms  28.674 ms
 4  10.6.101.2 (10.6.101.2)  42.688 ms  43.857 ms  43.741 ms
 5  client1 (10.6.201.10)  56.153 ms !X  56.066 ms !X  55.937 ms !X
```

* `server1` -> `client2`:

```bash
[iroh@server1 ~]$ traceroute client2
traceroute to client2 (10.6.201.11), 30 hops max, 60 byte packets
 1  gateway (10.6.202.254)  9.587 ms  9.062 ms  8.840 ms
 2  10.6.100.2 (10.6.100.2)  20.191 ms  20.137 ms  20.451 ms
 3  10.6.100.10 (10.6.100.10)  33.305 ms  45.189 ms  45.087 ms
 4  10.6.101.2 (10.6.101.2)  55.567 ms  55.835 ms  55.703 ms
 5  client2 (10.6.201.11)  55.537 ms !X  55.426 ms !X  55.294 ms !X
```

## III- Let's end this properly

### NAT: Accès internet

curl google.com sur `server1`:

```bash
[iroh@server1 ~]$ curl 216.58.204.100
<HTML><HEAD><meta http-equiv="content-type" content="text/html;charset=utf-8">
<TITLE>301 Moved</TITLE></HEAD><BODY>
<H1>301 Moved</H1>
The document has moved
<A HREF="http://www.google.com/">here</A>.
</BODY></HTML>
```