---
authors:
  - Equipe RecoveryBox
date:
  created: 2025-01-15
categories:
  - Sortie
tags:
  - v1.0
---

# Sortie de RecoveryBox v1.0

Nous sommes heureux d'annoncer la première version stable de RecoveryBox !

## Nouveautés

- Accès hors ligne complet à Wikipédia via Kiwix
- Point d'accès WiFi avec routage automatique
- Intégration BRouter pour le calcul d'itinéraires hors ligne
- OpenWebRX Plus pour la réception radio SDR
- Client web Meshtastic et daemon cartographique BRouter
- Console terminal accessible via le web
- Supervision des services avec rbstatus

## Installation

```bash
git clone https://github.com/mr-dgidgi/recoverybox.git
cd recoverybox
sudo ./recovery_box_install.sh
```

Suivez les instructions pour sélectionner votre langue et attendez la fin de l'installation.

!!! tip "Durée d'installation"
    Une installation complète avec cartographie de la France prend environ **4 à 5 heures** avec une bonne connexion internet.

## À Venir

- Fonctionnalités APRS pour la transmission de données en temps réel
- Support de langues supplémentaires
- Bibliothèque PDF étendue

Restez à l'écoute !
