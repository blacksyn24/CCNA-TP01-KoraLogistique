# 📄 1 — Cahier des charges : Kora Logistique SARL

> **Règle du TP :** ce document décrit uniquement les **besoins du client**. Aucune commande n'est donnée ici. À toi de traduire chaque exigence en configuration.

---

## 🏢 Le client

**Kora Logistique SARL** est une entreprise de transport et d'entreposage installée dans la zone portuaire de **Cotonou**. Elle vient d'emménager dans un nouveau bâtiment de deux étages et n'a **aucun réseau** pour l'instant.

Le directeur, M. Adéchina, t'engage comme technicien réseau pour mettre en service l'infrastructure. Son message :

> « Nos équipes ne peuvent pas partager de fichiers et nos serveurs sont injoignables. Je veux un réseau propre, sécurisé, prêt pour IPv6, et documenté. Notre ancien prestataire a laissé tous les mots de passe en clair et le Telnet ouvert : ça ne doit plus jamais arriver. »

---

## 👥 Les services

| Service | Emplacement | Besoin en hôtes (aujourd'hui + croissance 3 ans) |
|---|---|---|
| **OPÉRATIONS** (quai, dispatch, chauffeurs) | Rez-de-chaussée | **100** hôtes |
| **ADMINISTRATION** (direction, compta, RH, IT) | 1er étage | **50** hôtes |
| **SERVEURS** (DNS, Web intranet) | Local technique | **10** hôtes |

---

## 🧰 Matériel fourni

| Qté | Équipement | Modèle Packet Tracer |
|---|---|---|
| 1 | Routeur | ISR **4331** |
| 3 | Commutateurs | Catalyst **2960-24TT** |
| 4 | Postes de travail | PC-PT |
| 2 | Serveurs | Server-PT |

Câblage : cuivre droit (straight-through) pour tous les liens.

---

## 📌 Exigences

### E1 — Adressage IPv4
- Le FAI a attribué le bloc **192.168.50.0/24** pour tout le site.
- Le découpage doit être fait en **VLSM**, du plus grand besoin au plus petit, **sans gaspillage inutile**.
- Les adresses restantes doivent rester **disponibles** pour une future extension (Wi-Fi invités).
- Convention d'adressage dans chaque sous-réseau :
  - **1ère adresse utilisable** → routeur (passerelle)
  - **2e adresse utilisable** → commutateur du service (administration)
  - Postes et serveurs : voir plan d'adressage.

### E2 — Adressage IPv6 (double pile)
- Préfixe global attribué : **2001:db8:acad::/48**.
- Un **/64 par service** : sous-réseau `10` pour OPÉRATIONS, `20` pour ADMINISTRATION, `30` pour SERVEURS.
- Le routeur utilise **`::1`** comme identifiant d'interface dans chaque /64 **et** une adresse link-local **fixe et identique** sur toutes ses interfaces : **`fe80::1`**.
- Les hôtes sont configurés **statiquement** et utilisent l'adresse **link-local** du routeur comme passerelle.

### E3 — Nommage et documentation
- Noms d'équipements : **R1-KORA**, **S1-OPS**, **S2-ADM**, **S3-SRV**.
- Chaque interface utilisée porte une **description** claire (vers quel équipement / quel service).
- Domaine DNS de l'entreprise : **koralog.bj**.

### E4 — Sécurité des équipements (routeur ET commutateurs)
- Bannière d'avertissement légal affichée à toute connexion.
- Accès au mode privilégié protégé par un mot de passe **chiffré fort** : `Kora@Enable26`.
- Accès console protégé : `Kora@Cons26`, avec **déconnexion automatique après 5 minutes** d'inactivité, et des messages système qui ne coupent pas la saisie.
- **Aucun mot de passe en clair** dans la configuration.
- Sur le routeur uniquement :
  - mots de passe d'au moins **10 caractères** obligatoires ;
  - après **3 échecs de connexion en 60 secondes**, blocage des connexions pendant **120 secondes**.
- La résolution DNS involontaire des commandes mal tapées doit être désactivée.

### E5 — Administration à distance
- Administration **uniquement en SSH version 2** — **Telnet interdit**.
- Authentification par **compte local** : utilisateur `admin`, mot de passe `Kora@Admin26`, privilège maximal.
- Clés RSA de **2048 bits**.
- Chaque commutateur doit être joignable à distance depuis n'importe quel service (pense à ce dont un switch a besoin pour répondre à une machine d'un **autre** réseau).

### E6 — Durcissement des commutateurs
- Tous les ports **non utilisés** sont **désactivés**.

### E7 — Services applicatifs
- **SRV-DNS** résout `www.koralog.bj` vers l'adresse IPv4 de **SRV-WEB**.
- **SRV-WEB** héberge l'intranet (HTTP).
- Tous les postes utilisent **SRV-DNS** comme serveur DNS.

### E8 — Recette (validation finale)
Le client signe la recette si :
1. Tous les postes se joignent entre eux en **IPv4 et IPv6**.
2. Un poste OPÉRATIONS ouvre **http://www.koralog.bj** dans son navigateur.
3. Depuis **PC-ADM1**, l'admin se connecte en **SSH** au routeur **et** aux trois commutateurs.
4. Une tentative de **Telnet** échoue.
5. `show running-config` ne montre **aucun** mot de passe lisible.
6. Toute la configuration est **sauvegardée** (survit à un redémarrage).
