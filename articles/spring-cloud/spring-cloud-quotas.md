---
title: Plans et quotas de service pour Azure Spring Cloud
description: Découvrez les quotas de service et les plans de service pour Azure Spring Cloud.
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 7d47311385848f96a12a50c1dcf8b8284869c71d
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89260354"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Quotas et plans de service pour Azure Spring Cloud

Tous les services Azure définissent des quotas et des limites par défaut pour les ressources et fonctionnalités.   Azure Spring Cloud offre deux niveaux tarifaires : De base et Standard. Nous allons détailler les limites des deux niveaux dans cet article.

## <a name="azure-spring-cloud-service-tiers-and-limits"></a>Niveaux de service et limites Azure Spring Cloud

| Ressource | De base | Standard
------- | ------- | -------
Processeurs virtuels | 1 par instance de service | 4 par instance de service
Mémoire | 2 Go par instance de service | 8 Go par instance de service
Instances du service Azure Spring Cloud par région et par abonnement | 10 | 10
Nombre total d’instances d’application par instance du service Azure Spring Cloud | 25 | 500
Volumes persistants | 1 Go/application x 10 applications | 50 Go/application x 10 applications

## <a name="next-steps"></a>Étapes suivantes

Certaines limites par défaut peuvent être augmentées. Si votre configuration nécessite une augmentation, [créez une demande de support](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).
