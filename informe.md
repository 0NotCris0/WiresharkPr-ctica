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

Aqui podem veure el contingut del paquet SSH de longitud total 326 bytes:

```

0000   14 da e9 62 5f 7c d4 76 ea 0f fd 58 08 00 45 00   ...b_|.v...X..E.
0010   01 38 e8 04 40 00 38 06 6b c8 cd a6 5e 11 c0 a8   .8..@.8.k...^...
0020   01 93 00 16 8b 5e a0 39 57 34 3e f3 81 0f 80 18   .....^.9W4>.....
0030   10 65 d2 78 00 00 01 01 08 0a 00 00 00 cf 00 0c   .e.x............
0040   64 e3 48 9e c1 a1 c4 f6 a4 e7 50 fb b3 4d df 6c   d.H.......P..M.l
0050   6b 9c 15 fa 09 d1 7d 46 87 b1 52 e5 a8 9b 6b a8   k.....}F..R...k.
0060   15 0c cc 2f 83 8a 8b 53 f5 2a 5f 25 e3 bc be 5b   .../...S.*_%...[
0070   48 27 2c b3 a6 03 62 b6 58 2d f0 4c e6 d2 76 1f   H',...b.X-.L..v.
0080   64 79 c9 95 65 49 95 3e 93 40 74 40 9d 2b 5b d1   dy..eI.>.@t@.+[.
0090   05 14 a0 18 bb b5 3e 7c e2 ac 0b e5 d5 43 90 e9   ......>|.....C..
00a0   92 a9 2c d8 03 21 ca f1 8f 09 18 b4 33 ec 6d 27   ..,..!......3.m'
00b0   52 b9 d0 b5 58 a3 ea 2b a2 4f 31 be a9 0f b8 2e   R...X..+.O1.....
00c0   b3 e3 5d 87 5f 34 b0 f1 6e 1f 18 87 36 e1 46 00   ..]._4..n...6.F.
00d0   a0 d5 e0 a2 89 64 9f c3 2b f2 d2 85 db b7 dc e0   .....d..+.......
00e0   7d 82 5b f2 8c 62 4f 20 a4 92 36 19 48 56 70 d6   }.[..bO ..6.HVp.
00f0   13 99 b3 0b 51 c0 ef c8 70 4f 0a 82 6d b1 44 8b   ....Q...pO..m.D.
0100   be 79 b0 de bd 8b 9a c1 c2 3c 9c 27 a6 fd 5a 35   .y.......<.'..Z5
0110   dc 33 23 2b 0d 9f 35 48 4b a8 a5 01 fa ab 37 1f   .3#+..5HK.....7.
0120   3b f4 7f 81 e4 84 1a 13 e0 d0 02 88 78 32 fe 06   ;...........x2..
0130   49 04 23 ce c5 38 f0 0f ae 36 31 d3 e7 6d b3 6c   I.#..8...61..m.l
0140   03 c6 8b 6d 43 95                                 ...mC.

``` 

5. Correu electrònic:

Ara carrega l'arxiu "captura2.pcapng" que també teniu al repositori, troba el missatge que s’ha enviat amb el protocol de correu sortint. Extreu el fitxer.

Primer filtrarem per smtp. Després busquem el paquet que té en la columna Info "DATA". Podem veure el missatge i el fitxer adjunt. El missatge és "Mensaje ultra secreto para el administrador"

Esta el arxiu en el repositori.

- [Fitxer](correu.txt)

<img src="img/21.png" width="600" />

<img src="img/22.png" width="600" />


