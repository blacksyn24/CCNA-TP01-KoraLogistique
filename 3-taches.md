# 🛠️ 3 — Tâches de réalisation

> **Règle d'or :** tape **toutes** les commandes à la main. Pas de copier-coller depuis le corrigé.
> Chaque phase se termine par un **🚩 checkpoint** : ne passe pas à la suite tant qu'il n'est pas validé.
> Les **indices** donnent le nom de la commande, jamais la commande complète.

Durée estimée : **3 h à 4 h**

---

## Phase 0 — Préparation (15 min)
1. Termine le **plan d'adressage** (`2-adressage.md`) sur papier.
2. Place les équipements dans Packet Tracer et câble-les selon la **Partie C**.
3. Nomme les équipements dans Packet Tracer (onglet *Config → Display Name*).

🚩 **Checkpoint 0 :** tous les voyants des liens PC ↔ switch sont **verts**. Les liens routeur ↔ switch sont **rouges** : c'est normal, pourquoi ?

---

## Phase 1 — Configuration de base de R1-KORA (30 min)
En CLI uniquement (pas l'onglet *Config*).

1. Nomme le routeur et désactive la résolution DNS des commandes mal tapées.
   💡 *Indice : `hostname`, `no ip domain-…`*
2. Configure la bannière légale.
   💡 *Indice : `banner motd` avec un caractère délimiteur*
3. Applique toutes les exigences de **E4** : mot de passe privilégié chiffré, console, timeout, messages synchrones, chiffrement des mots de passe, longueur minimale, anti-force-brute.
   💡 *Indices : `enable secret`, `line console 0`, `exec-timeout`, `logging synchronous`, `service password-…`, `security passwords …`, `login block-for …`*
4. Configure et **active** les trois interfaces avec leur IPv4 et leur description.
   💡 *Indice : une interface de routeur est éteinte par défaut*

🚩 **Checkpoint 1 :** `show ip interface brief` → les 3 interfaces G0/0/x sont **up/up** avec la bonne IP.

---

## Phase 2 — IPv6 sur R1-KORA (20 min)
1. Active le **routage IPv6** sur le routeur.
   💡 *Indice : sans cette commande, le routeur ne transmet pas l'IPv6 entre ses interfaces et n'envoie pas de RA*
2. Sur chaque interface : adresse GUA `::1/64` **et** link-local `fe80::1`.

🚩 **Checkpoint 2 :** `show ipv6 interface brief` → chaque interface affiche **fe80::1** et sa GUA.

---

## Phase 3 — Commutateurs S1, S2, S3 (45 min)
Pour **chaque** switch :

1. Nom, désactivation de la résolution DNS, bannière, sécurité **E4** (sauf longueur minimale et anti-force-brute, réservés au routeur).
2. Adresse IP de gestion sur l'**interface VLAN 1** + activation.
3. Ce qu'il faut pour que le switch réponde à un PC d'un **autre** réseau.
   💡 *Indice : un switch L2 ne route pas, mais il a besoin de savoir…*
4. Descriptions sur les ports utilisés.
5. **Désactive tous les ports inutilisés** en une seule commande.
   💡 *Indice : `interface range`*

🚩 **Checkpoint 3 :** `show ip interface brief` sur chaque switch → `Vlan1` **up/up** avec la bonne IP.
`show interfaces status` → les ports inutilisés sont **disabled**.

---

## Phase 4 — Hôtes et serveurs (30 min)
1. Configure l'IPv4, l'IPv6, les passerelles et le DNS des 4 PC et 2 serveurs (*Desktop → IP Configuration*).
2. **SRV-DNS** : *Services → DNS* → active le service, ajoute un enregistrement **A** `www.koralog.bj`.
3. **SRV-WEB** : *Services → HTTP* → vérifie que le service est **On**. Personnalise `index.html` avec « Intranet Kora Logistique ».
4. Désactive les services inutiles sur chaque serveur (DHCP, FTP, Email, etc.).

🚩 **Checkpoint 4 :** depuis PC-OPS1 → `ping 192.168.50.196` ✅ et `ping 2001:db8:acad:30::11` ✅

---

## Phase 5 — SSH partout (40 min)
Sur **R1-KORA, S1, S2, S3** :

1. Nom de domaine `koralog.bj` (nécessaire pour générer les clés).
2. Compte local `admin` (privilège 15, mot de passe **secret**).
3. Clés RSA 2048 bits.
4. SSH version 2.
5. Lignes VTY : authentification **locale**, **SSH uniquement**, même timeout que la console.
   💡 *Attention : le routeur a des VTY 0 à 4, le switch 2960 de 0 à 15. Sécurise-les **toutes**.*

🚩 **Checkpoint 5 :** depuis PC-ADM1 → `ssh -l admin 192.168.50.1` ✅
`telnet 192.168.50.1` → **refusé** ✅

---

## Phase 6 — Sauvegarde et recette (20 min)
1. Sauvegarde la configuration de **chaque** équipement.
2. Passe toute la liste de `4-verification.md`.
3. Prends des captures d'écran de la topologie et des vérifications clés → dossier `captures/`.
4. Exporte les `show running-config` finaux → dossier `configs/mes-configs/`.

🚩 **Checkpoint final :** les 6 critères de recette **E8** sont validés.

---

## ➡️ Ensuite
Passe à **`5-depannage.md`** : on va casser le réseau… et tu vas le réparer.
