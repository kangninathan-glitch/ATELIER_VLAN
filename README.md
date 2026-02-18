# 🧩 TP VLAN & Masques — Comprendre la segmentation réseau

> Atelier pratique pour comprendre le rôle des VLAN et des masques IP dans la segmentation d’un réseau.

---

# 🎯 Objectifs pédagogiques

À la fin de ce TP, vous devrez savoir :

✅ Expliquer le rôle d’un VLAN  
✅ Comprendre le lien VLAN ↔ sous-réseau IP  
✅ Calculer et utiliser des masques IP  
✅ Mettre en place un trunk 802.1Q  
✅ Tester la communication intra/inter-VLAN

---

# 🧠 Rappel théorique simple

## 📌 VLAN
Un VLAN est un **réseau logique** sur un même switch physique.

👉 Chaque VLAN = un domaine de broadcast distinct  
👉 Chaque VLAN = un sous-réseau IP différent

---

## 📌 Masque de sous-réseau
Le masque définit :
- La partie réseau  
- La partie hôte  

| Masque | Nb hôtes |
|------|---------|
   /24 | 254 |
   /25 | 126 |
   /26 | 62 |

---

# 🗺️ Topologie du TP

```
         VLAN 10         VLAN 20
        192.168.10.0/24 192.168.20.0/24

PC1 -------- SW1 -------- R1 -------- PC3
              |  (trunk)
              |
             PC2
```

---

# 🧩 Matériel Packet Tracer

- 1 routeur (2911)  
- 1 switch (2960)  
- 3 PC  

---

# 🌐 Plan d’adressage

## VLAN 10

| Équipement | IP |
|----------|------|
PC1 | 192.168.10.10/24 |
PC2 | 192.168.10.20/24 |
GW | 192.168.10.1 |

---

## VLAN 20

| Équipement | IP |
|----------|------|
PC3 | 192.168.20.10/24 |
GW | 192.168.20.1 |

---

# 🔹 PARTIE 1 — Création des VLAN

Sur le switch :

```
enable
conf t
vlan 10
name ADMIN
vlan 20
name USERS
```

---

# 🔹 PARTIE 2 — Affectation des ports

```
interface f0/1
switchport mode access
switchport access vlan 10

interface f0/2
switchport mode access
switchport access vlan 10

interface f0/3
switchport mode access
switchport access vlan 20
```

---

# 🔹 PARTIE 3 — Trunk vers le routeur

```
interface f0/24
switchport mode trunk
```

---

# 🔹 PARTIE 4 — Router-on-a-stick

Sur R1 :

```
interface g0/0.10
encapsulation dot1Q 10
ip address 192.168.10.1 255.255.255.0

interface g0/0.20
encapsulation dot1Q 20
ip address 192.168.20.1 255.255.255.0

interface g0/0
no shutdown
```

---

# 🔹 PARTIE 5 — Configuration IP des PC

Configurer IP + passerelle selon le plan d’adressage.

---

# 🧪 TESTS

## Test 1 — Intra-VLAN
PC1 → PC2  
👉 Doit fonctionner

<img width="521" height="246" alt="image" src="https://github.com/user-attachments/assets/6aad6ae8-7f68-4eb5-a0c5-2b8d8d2c4153" />


---

## Test 2 — Inter-VLAN
PC1 → PC3  
👉 Fonctionne uniquement grâce au routeur

<img width="597" height="203" alt="image" src="https://github.com/user-attachments/assets/9fa5282c-9de7-41a2-9fb3-09b09da8e61c" />

---

# ❓ Questions de réflexion

1. Pourquoi PC1 ne voit-il pas PC3 sans routeur ?
   - Parce que un VLAN correspond à un réseau différent et il y a que un routeur qui peut faire communiquer deux réseaux différents.
3. Quel rôle joue le masque /24 ?
   - Le masque /24 sert à définir les limites du réseau et aussi il permet au PC de savoir si il peut communiquer directement ou si il doit passer par un routeur.
5. Que se passe-t-il si VLAN 10 et VLAN 20 ont le même réseau IP ?
   - Deux VLAN différents doivent avoir des réseaux IP différents, sinon le routage devient pas possible.
7. Pourquoi un trunk est-il nécessaire ?
   - Le trunk permet de faire passer plusieurs VLAN sur un seul câble car sinon sans trunk, un port ne transporte qu’un seul VLAN.
---

# ⭐ Travail sur les Masques

Changer VLAN 10 en :

```
192.168.10.0/25
```
<img width="469" height="385" alt="image" src="https://github.com/user-attachments/assets/db7f1dfb-52c0-4ff8-9335-6edaebee4017" />

Questions :
- Combien d’hôtes max ?
  - 126 hôtes maximum dans le réseau 192.168.10.0/25.

- Quelle plage IP valide ?
   - 192.168.10.1 à 192.168.10.126

- Peut-on encore communiquer avec VLAN 20 ?
   - Oui, on peut encore communiquer avec VLAN 20 car même après le changement, VLAN 10 et VLAN 20 restent deux réseaux différents

---

# 🚀 Extensions

- Ajouter VLAN 30  
- Mettre un DHCP par VLAN

<img width="660" height="259" alt="image" src="https://github.com/user-attachments/assets/d5e55201-e39d-4c5a-a2a1-b66fa4442ca4" />
    -
<img width="652" height="191" alt="image" src="https://github.com/user-attachments/assets/317ad366-9735-40de-8c8f-64f618792834" />
-

   - Après la configuration du DHCP sur le routeur, chaque PC configuré en mode DHCP a reçu automatiquement.
   - Cela signifie que le serveur DHCP fonctionne correctement pour chaque VLAN.


  
---

# 📝 Évaluation (/20)

| Critère | Points |
|--------|-------|
VLAN créés correctement | 4 |
Ports bien affectés | 2 |
Trunk opérationnel | 4 |
Inter-VLAN fonctionnel | 4 |
Travail sur les masques | 4 |  
Extention | 2 |  
  
# ✅ Fin du TP

Si vous savez expliquer :
> "Pourquoi deux VLAN ne communiquent pas sans routeur ?"

   - Deux VLAN ne communiquent pas sans routeur parce que un VLAN correspond à un réseau logique différent, même si ils sont connectés au même switch, ils sont considérés comme deux réseaux distincts. 

Alors vous avez compris la segmentation réseau 👍
