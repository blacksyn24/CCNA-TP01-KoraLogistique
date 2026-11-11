# 💣 Injection des pannes — RÉSERVÉ AU BINÔME

> ⚠️ Si tu fais le TP, **ne lis pas ce fichier**. Donne-le à ton binôme.
> À appliquer sur une **copie** du TP fonctionnel (`TP01-PANNES.pkt`).

| Panne | Où | Action |
|---|---|---|
| T1 | PC-OPS2 → Desktop → IP Configuration | Masque → `255.255.255.0` |
| T2 | PC-ADM2 → Desktop → IP Configuration | Passerelle → `192.168.50.1` |
| T3 | R1-KORA (CLI) | voir ci-dessous |
| T4 | S1-OPS (CLI) | voir ci-dessous |
| T5 | SRV-DNS → Services → DNS | Modifier `www.koralog.bj` → `192.168.50.197` |
| T6 | R1-KORA (CLI) | voir ci-dessous |
| T7 | R1-KORA (CLI) | voir ci-dessous |
| T8 | S2-ADM (CLI) | voir ci-dessous |

```cisco
! ===== R1-KORA (T3, T6, T7) =====
enable
configure terminal
interface g0/0/2
 shutdown
exit
line vty 0 4
 transport input telnet
exit
no ipv6 unicast-routing
end
write memory
```

```cisco
! ===== S1-OPS (T4) =====
enable
configure terminal
no ip default-gateway
end
write memory
```

```cisco
! ===== S2-ADM (T8) =====
enable
configure terminal
interface vlan 1
 shutdown
end
write memory
```

💡 **Conseil au binôme :** pour un vrai défi, n'applique que 3 ou 4 pannes à la fois, et mélange un problème « hôte » avec un problème « équipement ».

⚠️ **T3 masque T1, T5 et le Web :** si tu appliques T3 en même temps, l'étudiant devra d'abord la résoudre avant de pouvoir voir les autres. C'est voulu : c'est comme ça en vrai.
