# ✅ 4 — Vérification et recette

> Pour chaque commande : **ce que tu dois voir**. Coche quand c'est conforme.
> Si un résultat diffère, **ne corrige pas au hasard** : note le symptôme, puis suis la méthode de `5-depannage.md`.

---

## A. Routeur R1-KORA

| ✔ | Commande | Résultat attendu |
|---|---|---|
| ☐ | `show ip interface brief` | G0/0/0 `192.168.50.1`, G0/0/1 `192.168.50.129`, G0/0/2 `192.168.50.193` — toutes **up / up** |
| ☐ | `show ipv6 interface brief` | Chaque G0/0/x : `FE80::1` + sa GUA `2001:DB8:ACAD:x0::1` — **[up/up]** |
| ☐ | `show interfaces description` | Une description sur chaque interface utilisée |
| ☐ | `show running-config \| include ipv6 unicast` | `ipv6 unicast-routing` présent |
| ☐ | `show running-config \| section line` | `transport input ssh`, `login local`, `exec-timeout 5 0` sur VTY ; aucun mot de passe lisible |
| ☐ | `show running-config \| include secret\|password` | Uniquement des hash (`secret 5` / `secret 9` ou `password 7`) |
| ☐ | `show ip ssh` | `SSH Enabled - version 2.0` |
| ☐ | `show login` | Blocage configuré : **120 s** après **3** échecs en **60 s** |
| ☐ | `show ip route connected` | 3 routes **C** (une par LAN) + 3 routes **L** (/32) |
| ☐ | `show arp` | Entrées pour les PC/serveurs qui ont déjà communiqué |
| ☐ | `show ipv6 neighbors` | Voisins IPv6 (GUA et link-local des hôtes) |

## B. Commutateurs S1-OPS, S2-ADM, S3-SRV

| ✔ | Commande | Résultat attendu |
|---|---|---|
| ☐ | `show ip interface brief` | `Vlan1` **up / up** avec la bonne IP |
| ☐ | `show running-config \| include default-gateway` | Passerelle = IP du routeur **dans le même réseau** |
| ☐ | `show interfaces status` | Ports utilisés **connected**, tous les autres **disabled** |
| ☐ | `show mac address-table` | MAC des PC/serveurs sur F0/1-F0/2, MAC du routeur sur G0/1 |
| ☐ | `show ip ssh` | `SSH Enabled - version 2.0` |
| ☐ | `show running-config \| section line vty` | VTY **0 4** et **5 15** sécurisés |

## C. Tests de bout en bout

| ✔ | Depuis | Test | Attendu |
|---|---|---|---|
| ☐ | PC-OPS1 | `ping 192.168.50.140` (PC-ADM1) | ✅ réponses (le 1er ping peut perdre 1 paquet : ARP) |
| ☐ | PC-OPS1 | `ping 2001:db8:acad:20::10` | ✅ |
| ☐ | PC-OPS1 | `tracert 192.168.50.196` | 2 sauts : `192.168.50.1` puis `192.168.50.196` |
| ☐ | PC-OPS2 | `nslookup www.koralog.bj` | Server `192.168.50.195` → Address `192.168.50.196` |
| ☐ | PC-OPS2 | Navigateur → `http://www.koralog.bj` | Page « Intranet Kora Logistique » |
| ☐ | PC-ADM1 | `ssh -l admin 192.168.50.1` | Bannière + prompt `R1-KORA#` |
| ☐ | PC-ADM1 | `ssh -l admin 192.168.50.2` (S1) | Prompt `S1-OPS#` (preuve que la passerelle du switch marche) |
| ☐ | PC-ADM1 | `ssh -l admin 192.168.50.194` (S3) | Prompt `S3-SRV#` |
| ☐ | PC-ADM1 | `telnet 192.168.50.129` | ❌ connexion **refusée** |
| ☐ | PC-ADM2 | 3 SSH avec un **mauvais** mot de passe sur R1 | Puis 4e essai **bloqué** pendant 120 s |
| ☐ | R1-KORA | `show users` (pendant une session SSH) | Ligne `vty 0` avec l'utilisateur `admin` |

## D. Persistance

| ✔ | Test | Attendu |
|---|---|---|
| ☐ | `show startup-config` sur chaque équipement | Identique à la running-config |
| ☐ | `reload` du routeur | Toute la config est toujours là après redémarrage |

---

<details>
<summary><b>💬 Réponse au Checkpoint 0 (liens rouges)</b></summary>

Les interfaces d'un **routeur** Cisco sont **administrativement désactivées** (`shutdown`) par défaut. Tant que tu n'as pas fait `no shutdown`, le lien reste rouge. Les ports d'un **switch**, eux, sont actifs par défaut : c'est justement pour ça qu'on doit éteindre **manuellement** les ports inutilisés (E6).

</details>
