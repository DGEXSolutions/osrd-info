---
title: "Modèle de simulation de la signalisation et des aiguillages"
linkTitle: "Modèle de simulation"
weight: 20
description: Décrit le fonctionnement du poste d'aiguillage virtuel et de la signalisation
---

{{% pageinfo color="info" %}}
Ce document est une description du modèle de fonctionnement cible d'OSRD.
Il a pour objectif de renseigner développeurs et experts métiers sur le fonctionnement du simulateur.
Des changements y sont apportés au fil de l'évolution du projet.
{{% /pageinfo %}}

{{% pageinfo color="warning" %}}
Ce modèle est en cours d'implémentation
{{% /pageinfo %}}

## Architecture

```mermaid
flowchart TD
    %%%% NODES

    train[Train]
    %% ↓
    signaling[Signalisation]
    %% ↓
    routing[Routage]
    ordering[Ordonnancement]
    spacing["Espacement"]
    %% ↓
    reservation[Réservation]
    %% ↓
    location[Localisation]
    movable-elements["Éléments mobiles"]

    %%%% EDGES

    train -- réagit à --> signaling
    train -- réclame les itinéraires --> ordering
    signaling -- observe --> routing
    ordering -- réserve --> routing
    signaling -- observe --> spacing
    spacing -- observe --> reservation
    spacing -- observe --> movable-elements
    routing -- observe et réserve --> reservation
    reservation -- observe --> location
    reservation -- réserve et actionne --> movable-elements
    train -- informe --> location

    %%%% CLICKABLE LINKS

    click train href "./train/" _self
    click ordering href "./ordering/" _self
    click signaling href "./signaling/" _self
    click routing href "./routing/" _self
    click spacing href "./spacing/" _self
    click reservation href "./reservation/" _self
    click location href "./location/" _self
    click movable-elements href "./movable-elements/" _self
```

{{% alert title="Pistes d'évolution" color="info" %}}
- Gestion des overlaps
{{% /alert %}}

## Remerciements

Par ordre alphabétique:

- Christophe Mémin
- Nathanaël Dias
