# 🔧 5 — Dépannage : les tickets du support

> C'est la partie qui fait **vraiment** maîtriser les commandes.
> En entreprise, personne ne te dit « la passerelle est fausse ». On te dit « ça marche pas ». À toi de trouver.

---

## 🎯 Mise en place

1. **Sauvegarde** ton TP fonctionnel : *Fichier → Enregistrer sous* → `TP01-OK.pkt`.
2. Fais une copie : `TP01-PANNES.pkt`.
3. Un **binôme** applique les pannes du fichier `configs/pannes/injection-pannes.md` **sans te dire lesquelles**.
   *Seul ?* Applique-les toi-même, ferme Packet Tracer, attends le lendemain, puis traite les tickets dans le désordre.
4. Pour chaque ticket : remplis la **fiche d'intervention** ci-dessous **avant** de corriger.

---

## 🧭 Méthode obligatoire (approche par couches)

```
1. Reproduire le symptôme          → ping, tracert, nslookup, ssh
2. Délimiter                       → qui est touché ? tout le monde ? un seul poste ? IPv4 seulement ?
3. Couche 1-2                      → voyants, show interfaces status, show ip int brief (up/down ?)
4. Couche 3 hôte                   → ipconfig /all : IP, masque, passerelle, DNS
5. Couche 3 réseau                 → show ip route, show arp, show ipv6 interface brief
6. Couches 4-7                     → nslookup, services serveur, show ip ssh, lignes vty
7. Corriger UNE chose à la fois    → puis retester
```

---

## 🎫 Les tickets

| # | Signalé par | Message |
|---|---|---|
| **T1** | Chauffeur, poste PC-OPS2 | « Je ne peux plus ouvrir l'intranet. Mon collègue à côté (PC-OPS1) y arrive très bien. » |
| **T2** | Comptable, poste PC-ADM2 | « Je peux envoyer des fichiers à mon voisin de bureau, mais rien d'autre ne marche. » |
| **T3** | Tout le personnel | « L'intranet est mort pour tout le monde depuis ce matin. » |
| **T4** | Admin IT, poste PC-ADM1 | « Je n'arrive plus à me connecter en SSH sur le switch du rez-de-chaussée. Pourtant les gens d'en bas le pinguent. » |
| **T5** | Dispatch, poste PC-OPS1 | « Quand je tape l'adresse de l'intranet, ça dit que la page est introuvable. Le ping vers 192.168.50.196 marche pourtant ! » |
| **T6** | Admin IT, poste PC-ADM1 | « Le SSH vers le routeur me renvoie *connection refused*. Je n'ai touché à rien. » |
| **T7** | Admin IT | « Les tests IPv6 entre étages échouent tous. L'IPv4 marche parfaitement. » |
| **T8** | Admin IT | « Le switch de l'administration ne répond plus du tout à distance, alors que les PC de l'étage fonctionnent. » |

---

## 📝 Fiche d'intervention (à remplir pour chaque ticket)

```
Ticket n° : ____
Symptôme reproduit (commande + résultat) : ______________________
Périmètre (qui est touché / qui ne l'est pas) : ______________________
Couche OSI suspectée : ____
Commandes de diagnostic utilisées : ______________________
Cause trouvée : ______________________
Correction appliquée (commande exacte) : ______________________
Test de validation : ______________________
```

---

<details>
<summary><b>🔓 CORRIGÉ DES TICKETS — n'ouvre qu'après avoir tout tenté</b></summary>

| # | Cause | Commande révélatrice | Correction |
|---|---|---|---|
| T1 | PC-OPS2 a un masque **/24** au lieu de /25. Il croit que 192.168.50.196 est dans son réseau, envoie un ARP direct… qui n'aura jamais de réponse. | `ipconfig` sur PC-OPS2 | Masque `255.255.255.128` |
| T2 | Passerelle de PC-ADM2 = `192.168.50.1` (passerelle d'un **autre** réseau). | `ipconfig` | Passerelle `192.168.50.129` |
| T3 | Interface **G0/0/2** du routeur en `shutdown`. | `show ip interface brief` → *administratively down* | `interface g0/0/2` → `no shutdown` |
| T4 | **`ip default-gateway`** supprimée sur S1. Le switch répond aux machines de **son** réseau, mais ne sait pas renvoyer les réponses vers 192.168.50.128/26. | `show running-config \| include default` | `ip default-gateway 192.168.50.1` |
| T5 | Enregistrement DNS faux : `www.koralog.bj` → `192.168.50.197`. Couche 3 OK, problème **applicatif**. | `nslookup www.koralog.bj` | Corriger l'enregistrement A → `.196` |
| T6 | VTY du routeur en `transport input telnet`. | `show running-config \| section line vty` | `transport input ssh` |
| T7 | **`ipv6 unicast-routing`** supprimée. Les interfaces ont encore leurs adresses, mais le routeur ne **route** plus l'IPv6. | `show running-config \| include ipv6 unicast` | `ipv6 unicast-routing` |
| T8 | Interface **VLAN 1** de S2 en `shutdown`. Les ports physiques commutent toujours (couche 2), mais le switch n'a plus d'adresse joignable. | `show ip interface brief` sur S2 | `interface vlan 1` → `no shutdown` |

**Leçons à retenir :**
- T1/T2 : 80 % des pannes « un seul poste » viennent de la **configuration de l'hôte**.
- T4/T8 : un switch qui **commute** bien n'est pas forcément **administrable**. Deux choses différentes.
- T5 : « le ping marche mais pas le site » → regarde **au-dessus** de la couche 3.
- T7 : en double pile, toujours tester **les deux** protocoles séparément.

</details>
