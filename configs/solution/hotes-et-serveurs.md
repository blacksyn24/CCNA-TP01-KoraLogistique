# 💻 Hôtes et serveurs — Configuration finale (CORRIGÉ)

*Desktop → IP Configuration* (IPv4 et IPv6 en **Static**)

| Poste | IPv4 | Masque | Passerelle | DNS | IPv6 / préfixe | Passerelle IPv6 |
|---|---|---|---|---|---|---|
| PC-OPS1 | 192.168.50.10 | 255.255.255.128 | 192.168.50.1 | 192.168.50.195 | 2001:db8:acad:10::10 / 64 | fe80::1 |
| PC-OPS2 | 192.168.50.11 | 255.255.255.128 | 192.168.50.1 | 192.168.50.195 | 2001:db8:acad:10::11 / 64 | fe80::1 |
| PC-ADM1 | 192.168.50.140 | 255.255.255.192 | 192.168.50.129 | 192.168.50.195 | 2001:db8:acad:20::10 / 64 | fe80::1 |
| PC-ADM2 | 192.168.50.141 | 255.255.255.192 | 192.168.50.129 | 192.168.50.195 | 2001:db8:acad:20::11 / 64 | fe80::1 |
| SRV-DNS | 192.168.50.195 | 255.255.255.240 | 192.168.50.193 | 192.168.50.195 | 2001:db8:acad:30::10 / 64 | fe80::1 |
| SRV-WEB | 192.168.50.196 | 255.255.255.240 | 192.168.50.193 | 192.168.50.195 | 2001:db8:acad:30::11 / 64 | fe80::1 |

## SRV-DNS — *Services → DNS*
- DNS Service : **On**
- Enregistrement : Name `www.koralog.bj` · Type `A Record` · Address `192.168.50.196` → **Add**
- *Services → HTTP / DHCP / FTP / EMAIL…* : **Off** (un serveur = un rôle)

## SRV-WEB — *Services → HTTP*
- HTTP : **On**, HTTPS : **Off**
- Éditer `index.html` : remplacer le titre par **Intranet Kora Logistique**
- *DNS / DHCP / FTP / EMAIL…* : **Off**
