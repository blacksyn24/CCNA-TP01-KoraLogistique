# 🎓 6 — Questions type examen CCNA

> Réponds **sans regarder** tes notes, puis vérifie. Objectif : 10/12 minimum.

**Q1.** Un administrateur tape `enable password Cisco123` puis `enable secret Kora@Enable26`. Quel mot de passe donne accès au mode privilégié ? Pourquoi ?

**Q2.** Quelle est la différence entre `service password-encryption` et `enable secret` en termes de robustesse ?

**Q3.** Pourquoi `crypto key generate rsa` échoue-t-il sur un équipement neuf qui n'a reçu que la commande `hostname` ?

**Q4.** Un switch 2960 a une IP sur `interface vlan 1` mais pas de `ip default-gateway`. Depuis quels postes peut-on le joindre ?

**Q5.** Combien d'adresses hôtes utilisables dans un /26 ? Dans un /28 ? Donne le calcul.

**Q6.** Un hôte est configuré en `192.168.50.140/26`. Quelle est l'adresse réseau ? L'adresse de broadcast ?

**Q7.** Pourquoi une adresse link-local IPv6 peut-elle être identique sur plusieurs interfaces d'un même routeur ?

**Q8.** Quel est le rôle de `ipv6 unicast-routing` ? Que se passe-t-il pour les hôtes en SLAAC si on l'oublie ?

**Q9.** En IPv6, quel mécanisme remplace ARP ? Quels messages ICMPv6 utilise-t-il ?

**Q10.** Que fait `login block-for 120 attempts 3 within 60` exactement ?

**Q11.** Lors d'un `tracert` de PC-OPS1 vers SRV-WEB, pourquoi la première ligne est-elle `192.168.50.1` ?

**Q12.** Pourquoi sécuriser les lignes `vty 0 15` sur un switch, et pas seulement `vty 0 4` ?

---

<details>
<summary><b>✅ Réponses</b></summary>

**R1.** `Kora@Enable26`. Si les deux existent, **`enable secret` est prioritaire** et `enable password` est ignoré.

**R2.** `service password-encryption` utilise le **type 7**, réversible en quelques secondes : il évite seulement la lecture « par-dessus l'épaule ». `enable secret` utilise un **hachage** (type 5 MD5, ou 8/9 sur les IOS récents), non réversible.

**R3.** Les clés RSA sont nommées `hostname.domaine`. Sans **`ip domain-name`**, IOS refuse de les générer.

**R4.** Uniquement depuis les postes de **son propre réseau** (192.168.50.0/25 pour S1). Pour répondre à un autre réseau, il doit envoyer ses réponses à une passerelle, qu'il ne connaît pas.

**R5.** /26 → 32 − 26 = 6 bits hôtes → 2⁶ − 2 = **62**. /28 → 4 bits → 2⁴ − 2 = **14**.

**R6.** /26 = blocs de 64 (0, 64, 128, 192). 140 est dans le bloc **128** → réseau `192.168.50.128`, broadcast `192.168.50.191`.

**R7.** Une link-local n'est valide **que sur son lien** et n'est jamais routée. Le routeur sait toujours par quelle interface elle est utilisée : aucune ambiguïté.

**R8.** Elle active le **routage IPv6** entre interfaces **et** l'envoi des **Router Advertisements**. Sans elle, les hôtes en SLAAC ne reçoivent pas de préfixe et n'obtiennent pas de GUA.

**R9.** **NDP** (Neighbor Discovery Protocol) : **Neighbor Solicitation** (type 135) et **Neighbor Advertisement** (type 136). On a aussi RS (133) et RA (134) pour le routeur.

**R10.** Si **3 tentatives** de connexion échouent dans une fenêtre de **60 s**, le routeur **refuse toute nouvelle connexion** (Telnet/SSH/HTTP) pendant **120 s** (*quiet mode*).

**R11.** La destination est dans un **autre réseau** : le PC envoie le paquet à sa **passerelle par défaut** (R1, `192.168.50.1`), qui est donc le premier saut.

**R12.** Le 2960 possède **16 lignes VTY** (0 à 15). Laisser 5 à 15 sans protection = une porte d'entrée ouverte si les 5 premières sont occupées ou mal configurées.

</details>
