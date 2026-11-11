# 🧮 2 — Plan d'adressage

> **Étape 1 :** remplis les tableaux **à la main** (papier ou ici) avant d'ouvrir Packet Tracer.
> **Étape 2 :** compare avec le corrigé (dépliable en bas). Ne l'ouvre qu'une fois ton calcul terminé.

---

## Partie A — Découpage VLSM du bloc 192.168.50.0/24

Rappel de la méthode :
1. Trier les besoins du **plus grand au plus petit**.
2. Pour chaque besoin, trouver le plus petit `n` tel que **2ⁿ − 2 ≥ nombre d'hôtes**. Le masque est alors **/(32 − n)**.
3. Attribuer les sous-réseaux **à la suite**, en partant du début du bloc.

| Service | Hôtes demandés | Hôtes max du sous-réseau | Préfixe | Masque décimal | Adresse réseau | 1ère utilisable | Dernière utilisable | Broadcast |
|---|---|---|---|---|---|---|---|---|
| OPÉRATIONS | 100 | | | | | | | |
| ADMINISTRATION | 50 | | | | | | | |
| SERVEURS | 10 | | | | | | | |
| **Espace libre restant** | — | — | — | — | | — | — | — |

**Question bonus :** combien d'adresses du /24 restent libres après ton découpage ?

---

## Partie B — Table d'adressage complète

| Équipement | Interface | IPv4 / masque | Passerelle IPv4 | IPv6 / préfixe | Link-local | Passerelle IPv6 |
|---|---|---|---|---|---|---|
| R1-KORA | G0/0/0 (OPS) | | — | | fe80::1 | — |
| R1-KORA | G0/0/1 (ADM) | | — | | fe80::1 | — |
| R1-KORA | G0/0/2 (SRV) | | — | | fe80::1 | — |
| S1-OPS | VLAN 1 (SVI) | | | — | — | — |
| S2-ADM | VLAN 1 (SVI) | | | — | — | — |
| S3-SRV | VLAN 1 (SVI) | | | — | — | — |
| PC-OPS1 | NIC | .10 du réseau OPS | | ::10 | auto (EUI-64) | |
| PC-OPS2 | NIC | .11 du réseau OPS | | ::11 | auto (EUI-64) | |
| PC-ADM1 | NIC | .140 | | ::10 | auto (EUI-64) | |
| PC-ADM2 | NIC | .141 | | ::11 | auto (EUI-64) | |
| SRV-DNS | NIC | 3e utilisable du réseau SRV | | ::10 | auto (EUI-64) | |
| SRV-WEB | NIC | 4e utilisable du réseau SRV | | ::11 | auto (EUI-64) | |

---

## Partie C — Câblage

| Équipement A | Port A | Équipement B | Port B |
|---|---|---|---|
| R1-KORA | G0/0/0 | S1-OPS | G0/1 |
| R1-KORA | G0/0/1 | S2-ADM | G0/1 |
| R1-KORA | G0/0/2 | S3-SRV | G0/1 |
| S1-OPS | F0/1 | PC-OPS1 | Fa0 |
| S1-OPS | F0/2 | PC-OPS2 | Fa0 |
| S2-ADM | F0/1 | PC-ADM1 | Fa0 |
| S2-ADM | F0/2 | PC-ADM2 | Fa0 |
| S3-SRV | F0/1 | SRV-DNS | Fa0 |
| S3-SRV | F0/2 | SRV-WEB | Fa0 |

---

<details>
<summary><b>✅ CORRIGÉ — Partie A (clique pour afficher)</b></summary>

| Service | Hôtes | Max | Préfixe | Masque | Réseau | 1ère | Dernière | Broadcast |
|---|---|---|---|---|---|---|---|---|
| OPÉRATIONS | 100 | 126 (2⁷−2) | /25 | 255.255.255.128 | 192.168.50.0 | .1 | .126 | .127 |
| ADMINISTRATION | 50 | 62 (2⁶−2) | /26 | 255.255.255.192 | 192.168.50.128 | .129 | .190 | .191 |
| SERVEURS | 10 | 14 (2⁴−2) | /28 | 255.255.255.240 | 192.168.50.192 | .193 | .206 | .207 |
| Libre | — | — | — | — | 192.168.50.208 → .255 | — | — | — |

**Bonus :** de .208 à .255 → **48 adresses libres** (= un /28 `192.168.50.208/28` + un /27 `192.168.50.224/27`).

⚠️ Erreur classique : prendre un /27 (30 hôtes) pour 50 hôtes, ou un /29 (6 hôtes) pour 10 hôtes.

</details>

<details>
<summary><b>✅ CORRIGÉ — Partie B (clique pour afficher)</b></summary>

| Équipement | Interface | IPv4 / masque | Passerelle IPv4 | IPv6 / préfixe | Link-local | Passerelle IPv6 |
|---|---|---|---|---|---|---|
| R1-KORA | G0/0/0 | 192.168.50.1 /25 | — | 2001:db8:acad:10::1/64 | fe80::1 | — |
| R1-KORA | G0/0/1 | 192.168.50.129 /26 | — | 2001:db8:acad:20::1/64 | fe80::1 | — |
| R1-KORA | G0/0/2 | 192.168.50.193 /28 | — | 2001:db8:acad:30::1/64 | fe80::1 | — |
| S1-OPS | VLAN 1 | 192.168.50.2 /25 | 192.168.50.1 | — | — | — |
| S2-ADM | VLAN 1 | 192.168.50.130 /26 | 192.168.50.129 | — | — | — |
| S3-SRV | VLAN 1 | 192.168.50.194 /28 | 192.168.50.193 | — | — | — |
| PC-OPS1 | NIC | 192.168.50.10 /25 | 192.168.50.1 | 2001:db8:acad:10::10/64 | auto | fe80::1 |
| PC-OPS2 | NIC | 192.168.50.11 /25 | 192.168.50.1 | 2001:db8:acad:10::11/64 | auto | fe80::1 |
| PC-ADM1 | NIC | 192.168.50.140 /26 | 192.168.50.129 | 2001:db8:acad:20::10/64 | auto | fe80::1 |
| PC-ADM2 | NIC | 192.168.50.141 /26 | 192.168.50.129 | 2001:db8:acad:20::11/64 | auto | fe80::1 |
| SRV-DNS | NIC | 192.168.50.195 /28 | 192.168.50.193 | 2001:db8:acad:30::10/64 | auto | fe80::1 |
| SRV-WEB | NIC | 192.168.50.196 /28 | 192.168.50.193 | 2001:db8:acad:30::11/64 | auto | fe80::1 |

DNS de tous les hôtes : **192.168.50.195**

**Pourquoi `fe80::1` peut être identique sur les 3 interfaces ?** Une adresse link-local n'est valable que **sur son lien**. Le routeur sait toujours par quelle interface elle est utilisée, donc pas de conflit. Avantage : tous les hôtes du site ont la **même passerelle IPv6**, facile à retenir.

</details>
