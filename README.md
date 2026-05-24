# PRÀCTICA: Analitzador de protocols Wireshark

## Introducció

En aquesta pràctica treballarem amb **Wireshark**, una eina d’anàlisi de xarxa que permet capturar i inspeccionar paquets per estudiar diferents protocols de comunicació.

L’objectiu és aprendre a utilitzar filtres i analitzar trànsit de xarxa per obtenir informació sobre protocols com:

- `ICMP` → utilitzat per eines com `ping` i `traceroute`.
- `ARP` → relaciona adreces IP amb adreces MAC.
- `DNS` → tradueix noms de domini a adreces IP.
- `FTP` → transferència de fitxers.
- `HTTP/HTTPS` → navegació web.
- `SSH` i `Telnet` → connexions remotes.

Durant la pràctica es realitzaran captures en viu i també s’analitzaran fitxers `.pcapng` per identificar informació rellevant com:
- adreces MAC,
- dominis,
- contrasenyes en protocols insegurs,
- transferències de fitxers,
- i contingut de paquets.

També es farà ús dels filtres de visualització de Wireshark per facilitar l’anàlisi del trànsit capturat.

- [Anar al infrome](/informe.md)