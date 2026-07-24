---
authors:
  - Equipe RecoveryBox
date:
  created: 2025-01-20
categories:
  - Tutoriel
tags:
  - cartes
  - hors-ligne
---

# Configurer les Cartes Hors Ligne

L'une des fonctionnalités principales de RecoveryBox est sa **capacité cartographique entièrement hors ligne**. Voici comment cela fonctionne.

## Architecture

La pile cartographique se compose de trois composants :

1. **generate_map** — Télécharge les données OSM depuis geofabrik.de et crée des fichiers MBTiles
2. **tileserver-gl** — Rend et sert les tuiles vectorielles/raster depuis des MBTiles locaux
3. **BRouter** — Calcule les itinéraires avec des données de profils locales

## Génération des Données Cartographiques

L'outil `generate_map` automatise le processus :

```bash
# Les cartes sont générées lors de l'installation
# Pour régénérer ou ajouter des régions :
generate_map --region france
```

L'outil télécharge les fichiers PBF, les traite avec Planetiler et produit des MBTiles que tileserver-gl peut servir.

## Accès aux Cartes

Une fois installé, les cartes sont disponibles à :

- **BRouter** : `http://recovery.box/brouter`
- **Tileserver** : `http://recovery.box/tileserver`

Les deux services fonctionnent entièrement hors ligne — aucune connexion internet requise.

!!! info "Intégration GPS"
    Si un module GPS est connecté, RecoveryBox superpose automatiquement votre position sur la carte BRouter.

## Profils de Routage Supportés

BRouter inclut plusieurs profils de routage :

- `fastest` — Optimisé pour la vitesse
- `shortest` — Distance minimale
- `mtb` — Adapté au VTT
- `hiking` — Routes de randonnée
