---
title: Fichier include
description: Fichier include
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 09/12/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: ec6cbcbc93fe87634c87caeb0041b75ec916a22f
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/26/2020
ms.locfileid: "67177033"
---
Pour ouvrir un port ou créer un point de terminaison sur une machine virtuelle dans Azure, créez un filtre réseau sur un sous-réseau ou une interface réseau de machine virtuelle. Vous placez ces filtres, qui contrôlent le trafic entrant et sortant, dans un groupe de sécurité réseau associé à la ressource qui reçoit le trafic.

L'exemple de cet article montre comment créer un filtre réseau qui utilise le port TCP standard 80 (en supposant que vous avez déjà lancé les services appropriés et ouvert toutes les règles de pare-feu du système d'exploitation sur la machine virtuelle).

Une fois que vous avez créé une machine virtuelle configurée pour traiter des requêtes web sur le port TCP 80 standard, vous pouvez :

1. Créer des groupes de sécurité réseau

2. Créer une règle de sécurité de trafic entrant autorisant le trafic et affecter des valeurs aux paramètres suivants :

   - **Plages de ports de destination** : 80

   - **Plages de ports sources** : * (autorise n’importe quel port source)

   - **Valeur de priorité** : entrez une valeur de priorité inférieure à 65 500 et prioritaire par rapport à la règle fourre-tout par défaut de refus de trafic entrant.

3. Associer le groupe de sécurité réseau à l’interface réseau de machine virtuelle ou au sous-réseau.

Bien que cet exemple utilise une règle simple pour autoriser le trafic HTTP, vous pouvez également utiliser des groupes de sécurité réseau et des règles pour créer des configurations réseau plus complexes. 




