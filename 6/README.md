# TP6 Topologie Réseau

## Sommaire

* I. [Préparation du lab](#i--préparation-du-lab)
    ** [Réseaux IP et aires OSPF](#réseaux-ip-et-aires-ospf)
    **[Adressage IP de chacune des machines](#adressage-ip-de-chacune-des-machines)

* II. [Mise en place du lab](#ii--mise-en-place-du-lab)
    ** [Checklists](#checklists)
    ** [Configuration OSPF](#configuration-ospf)


* III. [Let's end this properly](#iii--lets-end-this-properly)
    **[NAT: Accès internet](#nat-acces-internet)
    **[Service d'infra](#service-d-infra)
    **[Serveur DHCP](#serveur-dhcp)

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

curl `google.com` sur `server1`:

```bash
[iroh@server1 ~]$ curl 216.58.204.100
<HTML><HEAD><meta http-equiv="content-type" content="text/html;charset=utf-8">
<TITLE>301 Moved</TITLE></HEAD><BODY>
<H1>301 Moved</H1>
The document has moved
<A HREF="http://www.google.com/">here</A>.
</BODY></HTML>
```

### Service d'infra

`client1` curl `server1`

```bash
[iroh@client1 ~]$ curl server1
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.1//EN" "http://www.w3.org/TR/xhtml11/DTD/xhtml11.dtd">

<html xmlns="http://www.w3.org/1999/xhtml" xml:lang="en">
    <head>
        <title>Test Page for the Nginx HTTP Server on Fedora</title>
        <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
    </head>

    <body>
        <h1>Welcome to <strong>nginx</strong> on Fedora!</h1>

        <div class="content">
            <p>This page is used to test the proper operation of the
            <strong>nginx</strong> HTTP server after it has been
            installed. If you can read this page, it means that the
            web server installed at this site is working
            properly.</p>

            <div class="alert">
                <h2>Website Administrator</h2>
                <div class="content">
                    <p>This is the default <tt>index.html</tt> page that
                    is distributed with <strong>nginx</strong> on
                    Fedora.  It is located in
                    <tt>/usr/share/nginx/html</tt>.</p>

                    <p>You should now put your content in a location of
                    your choice and edit the <tt>root</tt> configuration
                    directive in the <strong>nginx</strong>
                    configuration file
                    <tt>/etc/nginx/nginx.conf</tt>.</p>

                </div>
            </div>

            <div class="logos">
                <a href="http://nginx.net/"><img
                    src="nginx-logo.png"
                    alt="[ Powered by nginx ]"
                    width="121" height="32" /></a>

                <a href="http://fedoraproject.org/"><img
                    src="poweredby.png"
                    alt="[ Powered by Fedora ]"
                    width="88" height="31" /></a>
            </div>
        </div>
    </body>
</html>
```

### Serveur DHCP

Utilisation du dhcp sur `client1`:

```bash
[iroh@client1 network-scripts]$ ip a
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:df:30:89 brd ff:ff:ff:ff:ff:ff
    inet 10.6.201.50/24 brd 10.6.201.255 scope global noprefixroute dynamic enp0s3
       valid_lft 599sec preferred_lft 599sec
    inet6 fe80::a00:27ff:fedf:3089/64 scope link
       valid_lft forever preferred_lft forever
```