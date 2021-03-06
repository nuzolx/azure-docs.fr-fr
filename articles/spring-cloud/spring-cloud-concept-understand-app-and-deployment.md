---
title: Comprendre l’application et le déploiement dans Azure Spring Cloud
description: Distinction entre l’application et le déploiement dans Azure Spring Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 07/23/2020
ms.custom: devx-track-java
ms.openlocfilehash: a1f6d318c123b5907a8c434bb097fb86a351f5d1
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89297533"
---
# <a name="understand-app-and-deployment-in-azure-spring-cloud"></a>Comprendre l’application et le déploiement dans Azure Spring Cloud

**L’application** et **le déploiement** sont les deux concepts clés du modèle de ressources Azure Spring Cloud. Dans Azure Spring Cloud, une *application* est l’abstraction d’une application métier ou d’un microservice.  Une version de code ou de binaire déployée comme *application* s’exécute dans un *déploiement*.  Les applications s’exécutent dans *une instance du service Azure Spring Cloud* ou simplement dans un *instance de service*, comme indiqué ci-dessous.

 ![Applications et déploiements](./media/spring-cloud-app-and-deployment/app-deployment-rev.png)

Vous pouvez avoir plusieurs instances de service au sein d’un seul abonnement Azure, mais le service Azure Spring Cloud est la plus facile à utiliser lorsque toutes les applications qui composent une application métier ou un microservice résident dans une instance de service unique.

Le niveau Standard d’Azure Spring Cloud permet à une application de disposer d’un déploiement de production et d’un déploiement intermédiaire, ce qui vous permet d’effectuer facilement un déploiement bleu/vert.

## <a name="app"></a>Application
Les fonctionnalités/propriétés suivantes sont définies au niveau de l’application.

| Énumération | Définition |
|:--|:----------------|
| Public</br>Point de terminaison | URL pour accéder à l’application |
| Custom</br>Domain | Enregistrement CNAME qui sécurise le domaine personnalisé |
| Service</br>Liaison | Propriétés de configuration de liaison définies dans le fichier function.json et l’attribut *ServiceBusTrigger* |
| Adresses IP gérées</br>Identité | Identité managée par Azure Active Directory qui permet à votre application d’accéder facilement à d’autres ressources protégées par Azure AD telles qu’Azure Key Vault |
| Persistante</br>Stockage | Paramètre qui permet aux données d’être conservées au-delà du redémarrage de l’application |

## <a name="deployment"></a>Déploiement

Les fonctionnalités/propriétés suivantes sont définies au niveau du déploiement et seront échangées lors du basculement entre un déploiement de production et un déploiement intermédiaire.

| Énumération | Définition |
|:--|:----------------|
| UC | Nombre de vCores par instance d’application |
| Mémoire | Paramètre qui alloue de la mémoire pour effectuer un scale-up ou un scale-out des déploiements |
| Instance</br>Count | Nombre d’instances d’application, défini manuellement ou automatiquement |
| Mise à l’échelle automatique | Met automatiquement à l’échelle le nombre d’instances en fonction des planifications et des règles prédéfinies |
| JVM</br>Options | paramètre : JAVA_OPTS |
| Environnement</br>Variables | Paramètres qui s’appliquent à l’ensemble de l’environnement Azure Spring Cloud |
| Runtime</br>Version | Java 8/Java 11|

## <a name="restrictions"></a>Restrictions

* **Une application doit avoir un déploiement de production** : la suppression d’un déploiement de production est bloquée par l’API. Vous devez basculer vers le déploiement intermédiaire avant de supprimer le déploiement de production.
* **Une application peut avoir au maximum deux déploiements** : la création de plus de deux déploiements est bloquée par l’API. Déployez votre nouveau binaire dans le déploiement de production ou intermédiaire existant.
* **La gestion du déploiement n’est pas disponible au niveau de service De base** : utilisez le niveau Standard pour obtenir la capacité de déploiement bleu/vert.

## <a name="see-also"></a>Voir aussi
* [Configurer un environnement intermédiaire dans Azure Spring Cloud](spring-cloud-howto-staging-environment.md)
