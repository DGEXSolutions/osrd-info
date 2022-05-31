---
title: "Processus de calcul de marche"
linkTitle: "Processus de calcul de marche"
weight: 40
---

Le calcul de marche dans OSRD est un processus à 4 étapes, utilisant chacune [le système d'enveloppes].

#### Calcul de la marche de base

Le premier objectif d'une simulation de train isolé est d'effectuer le calcul du **temps de parcours le plus rapide**, où le train roule à la vitesse maximale autorisée. Pour y parvenir, une procédure composée de différentes étapes est utilisée. À chaque étape, de nouvelles enveloppes sont calculées et ajoutées aux précédentes. Nous appelons l'enveloppe finale résultante **Max Effort Envelope** (Enveloppe de l'effort maximal).

1 - Une première enveloppe est calculée au début de la simulation en regroupant toutes les enveloppes liées à certaines limites de vitesse statiques (vitesse maximale de la ligne, vitesse maximale du matériel roulant, limitations de vitesse temporaires, limitations de vitesse par catégorie de train, limitations de vitesse par essieu). Pour s'assurer que le train entier roule toujours à la vitesse autorisée, il faut prendre en compte l'entièreté de sa longueur $L$. Pour que la queue du train du train ne dépasse pas les limitations, on applique un "décalage" à la courbe poitillée rouge. L'enveloppe résultante (courbe noire) est appelée **Most Restricted Speed Profile (MRSP)** (Profil de vitesse le plus restrictif).
![Most Restricted Speed Profile](../mrsp.png)

> L’axe **x** est la position du train, l’axe **y** est la vitesse. La ligne pointillée rouge représente la vitesse maximale autorisée, la ligne noire est le MRSP où la longueur du train a été prise en compte.

2 - Toutes les courbes de freinage sont calculées en partant de leur point cible, c'est-à-dire le point dans l'espace où une certaine limite de vitesse est imposée (vitesse cible finie) ou le point d'arrêt (vitesse cible = 0 m/s). Les courbes de freinage sont calculées en considérant toutes les forces actives (force de freinage constante ou non, résistance à l'avancement, poids) et sont donc physiquement précises. L'enveloppe résultante est appelée **Max Speed Profile** (Profil de vitesse maximale).
![Max Speed Profile](../msp.png)

3 - Pour chaque point correspondant à une augmentation de vitesse dans le MRSP ou à la fin d'une courbe de freinage d'arrêt, une courbe d'accélération est calculée. Les courbes d'accélération sont calculées en tenant compte de toutes les forces actives (force de traction, résistance à l'avancement, poids) et sont donc physiquement précises.

4 - Pour toutes les parties de l'enveloppe qui ne sont pas physiquement exactes, une nouvelle intégration des équations de mouvement est effectuée. Ce dernier calcul est nécessaire pour reproduire le comportement correct des parties de l'enveloppe où la vitesse doit être maintenue à une valeur constante, en incluant l'effet de toutes les forces. L'enveloppe qui en résulte est appelée **Max Effort Profile** (Profil d'effort maximal).
![Max Effort Profile](../mep.png)

##### Calcul de marche avec marges

Après avoir effectué le calcul de marche le plus rapide, il est possible d'y introduire certaines marges (allowances). Dans le calcul de marche d'OSRD, nous décidons de distribuer les marges d'une manière économique, en minimisant la consommation d'énergie pendant le parcours du train. Une nouvelle **enveloppe Eco**, résultant d'un algorithme de dichotomie, est donc calculée pour distribuer une certaine valeur de marge dans l'enveloppe d'effort maximum calculée précédemment.
![Marges](../allowances.png)

#### Simulation de plusieurs trains

Dans le cas de la simulation de nombreux trains, le système de signalisation doit assurer **la sécurité**. L'effet de la signalisation sur le calcul de marche d'un train est reproduit en superposant des enveloppes dynamiques à l'enveloppe statique. Une nouvelle enveloppe dynamique est introduite par exemple lorsqu'un signal se ferme. Le train suit l'enveloppe économique statique superposée aux enveloppes dynamiques, s'il y en a. Dans ce mode de simulation, un contrôle du temps est effectué par rapport à un temps théorique provenant de l'information temporelle de l'enveloppe économique statique. Si le train est en retard par rapport à l'heure prévue, il cesse de suivre l'enveloppe économique et essaie d'aller plus vite. Sa courbe espace/vitesse sera donc limitée par l'enveloppe d'effort maximum.

Rajouter une partie Marge économique