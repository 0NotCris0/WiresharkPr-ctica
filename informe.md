# PRÀCTICA: Analitzador de protocols Wireshark

Kali ja té instal·lat Wireshark. Si utilitzeu Ubuntu Desktop, instal·leu i configureu Wireshark.

Posa en marxa la captura de paquets de Wireshark sobre la targeta de xarxa del teu Kali amb adaptador pont:

    - Adreça IP: 192.168.c.x/24 on c és 2 per 2n A i 4 per 2n B i x el teu número de llista.
    - Porta d'enllaç: 192.168.c.254
    - DNS: 8.8.8.8


<img src="img/1.png" width="600" />

Obrirem una consola i farem un `ping` al router i deixarem que faci uns quants paquets:

```bash
    ping 192.168.c.254
```

Entrarem a Wireshark i aturarem la captura. A continuació, analitzarem els paquets capturats per identificar informació rellevant.

<img src="img/2.png" width="600" />

A continuació, posarem en el filtre `icmp` per veure només els paquets ICMP i analitzarem les adreces MAC d'origen i destinació. 

- Quin número de tipus de ICMP té la petició d'eco i quin la resposta d'eco? Com ho veus? Incorpora una captura de pantalla on es vegi el tipus de ICMP.

La petició té el tipus 8 i la resposta el tipus 0. Es pot veure a la columna "Info" de Wireshark on posa "Echo (ping) request" i "Echo (ping) reply".

<img src="img/3.png" width="600" />

<img src="img/4.png" width="600" />

A les opcions avançades de la targeta, activem el mode promiscu amb l'opció Permetre-ho tot. Però en el nostre cas ho farem a la màquina virtual a l'apartat de Xarxa.

<img src="img/5.png" width="600" />

- Fes una captura de trànsit mentre navegues des de la màquina física. Quin trànsit pots veure relacionat amb el teu PC?

Podem veure els paquets ARP, paquets DNS i trànsit HTTP/HTTPS. Per exemple, he buscat google.com i he vist els paquets DNS.

<img src="img/6.png" width="600" />

## DNS

Ara filtrarem per DNS per veure les consultes i respostes. Però ho farem posant també l'adreça IP d'origen.

```bash
    dns and ip.addr == 192.168.c.x
```

Comprovarem que la resposta del servidor conté la IP de www.xtec.cat.

- DNS Query → el teu PC pregunta "quina IP té www.xtec.cat?"
- DNS Response → el servidor respon amb la IP

<img src="img/8.png" width="600" />

<img src="img/9.png" width="600" />

## ARP

Posarem el filtre ARP.

- Quina adreça MAC té el gateway de la xarxa? Quin és el fabricant de la seva NIC?

El paquet que té en la columna Info - Who has 192.168.2.254? Tell 192.168.x.x — la resposta contindrà la MAC del gateway.

Per saber el fabricant de la NIC: els tres primers bytes de la MAC.

<img src="img/10.png" width="600" />

<img src="img/11.png" width="600" />

## Anàlisi de captura

1. Al protocol ARP: pots saber quina adreça MAC té l'equip amb adreça 192.168.1.1? Fes un filtre per veure només els paquets d'aquesta adreça del protocol ARP.

Al filtre posarem: 

```bash
    ar.src.proto_ipv4 == 192.168.1.1
```

Podrem veure la MAC de l'equip.

<img src="img/12.png" width="600" />

2. A la sessió FTP:

- Quin és el password de l'usuari que inicia sessió? Quin nom té el fitxer que es descarrega del servidor?

Podem veure l'usuari i la contrasenya a la columna "Info" de Wireshark. És USER anonymous i PASS contra.

<img src="img/13.png" width="600" />

El fitxer que es descarrega del servidor és "README.md".

<img src="img/14.png" width="600" />

3. A la sessió de Telnet:

- Pots veure el que veia l'usuari quan es connectava al Telnet? Explica què és. Quins caràcters composen la nau espacial petita (posa-ho com a resposta)?
- A quin domini pertany l'adreça on ens connectem?

Filtrarem per Telnet i podrem veure el que veia l'usuari en connectar-se al Telnet. Es tracta d'una petita nau espacial feta amb caràcters ASCII.

Podem veure la nau amb Follow TCP Stream.

<img src="img/15.png" width="600" />

<img src="img/16.png" width="600" />

<img src="img/17.png" width="600" />

Per trobar el domini farem la següent comanda:

```bash
    dig -x IP_destí_del_telenet
```

<img src="img/18.png" width="600" />


4. A la sessió SSH:

- Pots saber a quin domini pertany l'adreça del servidor?
- Pots veure el contingut de les dades de la sessió? Enganxa les dades que conté el paquet ssh de longitud total 326 bytes.

Posarem el filtre:

```bash
    ssh and frame.len == 326
```

SSH xifra tot el trànsit, per tant no es pot veure el contingut de la sessió.

<img src="img/20.png" width="600" />

5. Correu electrònic:

Ara carrega l'arxiu "captura2.pcapng" que també teniu al repositori, troba el missatge que s’ha enviat amb el protocol de correu sortint. Extreu el fitxer.

Primer filtrarem per smtp. Després busquem el paquet que té en la columna Info "DATA". Podem veure el missatge i el fitxer adjunt. El missatge és "Mnesaje ultra secreto para el administrador"

<img src="img/21.png" width="600" />

<img src="img/22.png" width="600" />


