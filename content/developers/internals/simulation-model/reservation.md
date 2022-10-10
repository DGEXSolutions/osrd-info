---
title: "Reservation"
linkTitle: "Reservation"
weight: 30
description: "Gère l'état de réservation des zones"
---

{{% alert color="warning" %}}
Le mot réservation ici a pour sens réservation de la resource itinéraire, et non le sens commande plannifiée d'itinéraire
{{% /alert %}}

## Description

Les zones (ou TVDSection / DetectionSection) sont des partitions physiques des voies :

- capables de détecter la présence d'un train
- qui fournissent un service de réservation à l'usage des routes

Chaque zone a un certain nombre de configurations différentes.
Par exemple, une zone sans aiguille aura deux configurations :

- sens pair
- sens impair

Une zone avec une aiguille aura 4 configurations :

- sens pair voie principale
- sens impair voie principale
- sens pair voie déviation
- sens impair voie déviation

**Chaque zone ne peut être réservée que pour une configuration donnée à la fois, mais peut être réservée simultanément par plusieurs routes**.
Une zone ne peut changer de configuration que lorsqu'elle n'est pas réservée.

{{% alert color="info" %}}
En discutant avec des experts métiers, vont entendrez parler d'[enclenchements](https://fr.wikipedia.org/wiki/Enclenchement):
le terme viens de l'époque où les postes de signalisations étaient entièrement mécaniques.
Les enclanchements étaient alors des objets physiques qui empêchaient certaines configurations.

Une conséquence de cet héritage historique et que beaucoup de règles ferroviaires sont exprimées par contraintes au lieu d'être exprimées par garanties.
Aujourd'hui, un enclanchement, c'est une garantie qu'une situation particulière ne peut pas se produire.
{{% /alert %}}


### État dynamique d'une zone

```rust
enum ZoneState {
    Free,
    Reserved {
        config: ZoneConfig,
        trains: VecDeque<ZoneReservation>,
    }
}

struct ZoneReservation {
    train: TrainHandle,
    status: ZoneReservationStatus,
}

enum ZoneReservationStatus {
    /// In the process of being reserved, but not yet ready
    PRE_RESERVED,
    /// The train can arrive anytime
    RESERVED,
    /// The train is inside the zone
    OCCUPIED,
    /// The zone is pending release
    PENDING_RELEASE,
}
```

## Exigences de conception

- Les zones doivent pouvoir être **vérouillées** dans une configuration particulière.
- Il doit être possible pour plusieurs routes de **partager une réservation** de configuration.
- Il doit être possible d'observer l'**évolution de statut d'une réservation**.
- Il doit être possible de faire évoluer le statut d'une réservation.

## Dépendances

- `statique` une liste de zones
- `dynamique` capacité d'observer l'occupation des zones
- `dynamique` capacité d'actionner les éléments mobiles

## Opérations

- **espacement**: Observer l'état d'une zone
- **routage**: Pré-réserver une configuration de zone
- **routage**: Confirmer la réservation d'une zone
- **routage**: Attendre que la réservation de la zone soit utilisée par son train
- **routage**: Relacher une réservation de zone
