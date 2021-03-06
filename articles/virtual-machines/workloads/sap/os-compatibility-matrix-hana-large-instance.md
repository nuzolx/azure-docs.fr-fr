---
title: Matrice de compatibilité du système d'exploitation pour SAP HANA (Grandes instances) | Microsoft Docs
description: La matrice de compatibilité présente la compatibilité des différentes versions du système d'exploitation avec différents types de matériel (Grandes instances)
services: virtual-machines-linux
documentationcenter: ''
author: sasarava
manager: hrushib
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/21/2020
ms.author: sasarava
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 18e97aea5c556c4f8e6ff1fb1b91a82da6de1b59
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/22/2020
ms.locfileid: "88749365"
---
# <a name="compatible-operating-systems-for-hana-large-instances"></a>Systèmes d'exploitation compatibles pour HANA - Grandes instances

## <a name="hana-large-instance-type-i"></a>HANA - Grandes instances Type I     
  | Système d’exploitation | Disponibilité        | Références (SKU)                                                          |
  |------------------|---------------------|---------------------------------------------------------------|
  | SLES 12 SP2      | Plus proposé | S72, S72m, S96, S144, S144m, S192, S192m, S192xm              |
  | SLES 12 SP3      | Disponible           | S72, S72m, S96, S144, S144m, S192, S192m, S192xm              |
  | SLES 12 SP4      | Disponible           | S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224, S224m |
  | SLES 12 SP5      | Disponible           | S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224, S224m |
  | SLES 15 SP1      | Disponible           | S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224, S224m |
  | RHEL 7.6         | Disponible           | S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224, S224m |

  
### <a name="persistent-memory-skus"></a>Références SKU de mémoire persistante
  | Système d’exploitation | Disponibilité | Références (SKU)                             |
  |------------------|--------------|----------------------------------|
  | SLES 12 SP4      | Disponible    | S224oo, S224om, S224ooo, S224oom |
  
## <a name="hana-large-instance-type-ii"></a>HANA - Grandes instances Type II     
  |  Système d’exploitation       | Disponibilité        | Références (SKU)                                                              |
  |-------------------------|---------------------|-------------------------------------------------------------------|
  | SLES 12 SP2             | Plus proposé | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S960m |
  | SLES 12 SP3             | Disponible           | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S960m |
  | SLES 12 SP4             | Disponible           | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S960m |
  | SLES 12 SP5             | Disponible           | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S896m, S960m |
  | SLES 15 SP1             | Disponible           | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S896m, S960m |
  
## <a name="related-documents"></a>Documents associés

- Pour en savoir plus sur les [Références SKU disponibles](hana-available-skus.md)
- Pour savoir plus sur la [Mise à niveau du système d'exploitation](os-upgrade-hana-large-instance.md)
  

  
  
