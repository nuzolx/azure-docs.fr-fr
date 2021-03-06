---
title: Vue d’ensemble – Azure Database pour MySQL
description: Découvrez le service Azure Database pour MySQL, service de base de données relationnelle dans le cloud Microsoft qui repose sur MySQL Community Edition.
author: ajlam
ms.service: mysql
ms.author: andrela
ms.custom: mvc
ms.topic: overview
ms.date: 3/18/2020
ms.openlocfilehash: 37bc99d9f83f185a5372fd45634351987b85e20b
ms.sourcegitcommit: e2b36c60a53904ecf3b99b3f1d36be00fbde24fb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2020
ms.locfileid: "88763658"
---
# <a name="what-is-azure-database-for-mysql"></a>Qu’est-ce qu’Azure Database pour MySQL ?

Azure Database pour MySQL est un service de base de données relationnelle du cloud Microsoft qui repose sur le moteur de base de données [MySQL Community Edition](https://www.mysql.com/products/community/) versions 5.6, 5.7 et 8.0 (disponible avec la licence GPLv2). Azure Database pour MySQL offre :

- Une haute disponibilité intégrée sans coût supplémentaire ;
- Des performances prévisibles, grâce aux tarifs du paiement à l’utilisation ;
- Mise à l’échelle en fonction des besoins en quelques secondes ;
- La protection des données sensibles au repos et en mouvement ;
- Des sauvegardes automatiques et une restauration à un point dans le temps jusqu’à 35 jours ;
- Une sécurité et une conformité de classe Entreprise.

Ces fonctionnalités ne demandent pratiquement aucune administration, et toutes sont fournies sans coût supplémentaire. Elles vous permettent de vous concentrer sur le développement rapide de vos applications et d’accélérer leur mise sur le marché, plutôt que de consacrer du temps et des ressources à gérer des machines virtuelles et une infrastructure. Par ailleurs, vous pouvez continuer à développer votre application avec les outils open source et la plateforme de votre choix pour travailler avec la rapidité et l’efficacité exigées par votre activité, et ce sans avoir à acquérir de nouvelles compétences.

![Diagramme conceptuel d’Azure Database pour MySQL](media/overview/1-azure-db-for-mysql-conceptual-diagram.png)

Cet article présente les principaux concepts et fonctionnalités d’Azure Database pour MySQL qui ont trait aux performances, à l’extensibilité et à la facilité de gestion. Il contient également des liens pour en explorer les détails. Consultez ces démarrages rapides pour bien commencer :

- [Créer un serveur de base de données Azure pour MySQL à l’aide du Portail Azure](quickstart-create-mysql-server-database-using-azure-portal.md)
- [Création d’un serveur de base de données Azure pour MySQL à l’aide d’Azure CLI](quickstart-create-mysql-server-database-using-azure-cli.md)

Pour accéder à des exemples Azure CLI, consultez :

- [Exemples de CLI Azure pour Azure Database pour MySQL](sample-scripts-azure-cli.md)

## <a name="automated-patching"></a>Mise à jour corrective automatisée
Le service effectue une mise à jour corrective automatisée du matériel, du système d’exploitation et du moteur de base de données sous-jacents. La mise à jour corrective inclut des mises à jour logicielles et de sécurité pour le matériel, le système d’exploitation et le moteur de base de données sous-jacents. Pour le moteur MySQL, la mise à niveau des versions mineures est automatique et incluse dans le cadre de la mise à jour corrective. Lorsque la communauté publie une version mineure, elle est automatiquement intégrée dans le cadre du cycle de test du service. Le test de la version mineure est effectué sur certaines charges de travail canoniques pour MySQL. La publication de versions mineures du moteur MySQL fait l’objet d’une évaluation en termes de fiabilité (pas d’incident), de disponibilité, de sécurité et de performances. Toutes les versions mineures ne sont pas mises en production dans le service, mais elles sont évaluées en fonction du caractère critique des correctifs de bogues et de la nouvelle valeur incrémentielle. Cela permet de trouver un juste équilibre entre la nouvelle valeur incrémentielle et la réduction maximale des variables dans le système à des fins de stabilité. Aucune action de l’utilisateur ni aucun paramètre de configuration ne sont requis pour la mise à jour corrective. La fréquence des mises à jour correctives est gérée par le service en fonction du caractère critique de la charge utile. En général, le service suit un calendrier de publication mensuel dans le cadre de l’intégration et de la publication continues. Les utilisateurs peuvent s’abonner à la [notification de maintenance planifiée](concepts-monitoring.md) pour recevoir une notification de la maintenance à venir 72 heures avant l’événement.

## <a name="adjust-performance-and-scale-within-seconds"></a>Ajustez les performances et la mise à l’échelle en quelques secondes
Le service Azure Database pour MySQL offre plusieurs niveaux de service : De base, Usage général et À mémoire optimisée. Chaque niveau offre différentes performances et fonctionnalités pour prendre en charge des charges de travail de base de données plus ou moins denses. Vous pouvez créer votre première application sur une petite base de données pour un faible coût mensuel, puis adapter l’échelle aux besoins de votre solution. L’évolutivité dynamique permet de répondre en toute transparence à l’évolution rapide des besoins en ressources de votre base de données. Vous payez uniquement pour les ressources dont vous avez besoin et seulement quand vous en avez besoin. Pour plus d’informations, consultez  [Niveaux tarifaires](concepts-service-tiers.md).

## <a name="monitoring-and-alerting"></a>Surveillance et alerte
Comment savoir quand augmenter ou diminuer la taille des instances ? Vous utilisez les fonctionnalités intégrées de surveillance et d’alerte de performances, combinées avec les évaluations de performance basées sur des vCores. Ces outils vous permettent d’évaluer rapidement l’impact des mises à l’échelle des vCores (montées ou descentes en charge) en fonction de vos besoins en performances actuels ou pour un projet. Pour plus d’informations, consultez [Alertes](howto-alert-on-metric.md).

## <a name="keep-your-app-and-business-running"></a>Votre application et votre activité ne s’arrêtent jamais
Avec un temps de disponibilité de 99,99 %, l’excellent contrat de niveau de service (SLA) d’Azure, soutenu par un réseau mondial de centres de données gérés par Microsoft, permet d’exécuter votre application 24 heures sur 24, 7 jours sur 7. Avec chaque serveur Azure Database pour MySQL, vous tirez parti de la sécurité intégrée, d’une tolérance en cas de panne et de la protection des données que vous seriez de toute manière contraint d’acheter ou de concevoir, de créer et de gérer. Avec Azure Database pour MySQM, vous pouvez utiliser la limite de restauration dans le temps pour récupérer un serveur à un état antérieur, jusqu’à 35 jours.

## <a name="secure-your-data"></a>Sécurisez vos données
Les services de base de données Azure ont une tradition de sécurité des données qu’Azure Database pour MySQL respecte, avec des fonctionnalités qui limitent l’accès, protègent les données au repos et en mouvement et vous aident à surveiller l’activité. Visitez le [Centre de confidentialité Azure](https://www.microsoft.com/trustcenter/security) pour plus d’informations sur la sécurité de la plateforme Azure. Pour plus d’informations sur les fonctionnalités de sécurité d’Azure Database pour MySQL, consultez la [vue d’ensemble de la sécurité](concepts-security.md).

## <a name="contacts"></a>Contacts
Pour toute question ou suggestion au sujet de l’utilisation d’Azure Database pour MySQL, envoyez un e-mail à l’équipe Azure Database pour MySQL ([@Ask Azure DB pour MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com)). Cette adresse e-mail n’est pas un alias de support technique.

En outre, tenez compte des points de contact suivants le cas échéant :

- Pour contacter le support technique Azure, [émettez un ticket à partir du Portail Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Pour résoudre un problème relatif à votre compte, enregistrez une [demande de support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) sur le portail Azure.
- Pour donner votre avis ou demander de nouvelles fonctionnalités, créez une entrée via [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql).

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez lu la présentation d’Azure Database pour MySQL et répondu à la question « Qu’est-ce qu’Azure Database pour MySQL ? », vous êtes prêt à passer aux étapes suivantes :

- Consultez la page de tarification pour des comparaisons de coûts et des calculatrices. [Tarification](https://azure.microsoft.com/pricing/details/mysql/)
- Commencez par créer votre premier serveur. [Créer un serveur de base de données Azure pour MySQL à l’aide du Portail Azure](quickstart-create-mysql-server-database-using-azure-portal.md)
- Générez votre première application à l’aide de votre langage préféré :
  - [Python](./connect-python.md)
  - [Node.JS](./connect-nodejs.md)
  - [Java](./connect-java.md)
  - [Ruby](./connect-ruby.md)
  - [PHP](./connect-php.md)
  - [.NET (C#)](./connect-csharp.md)
  - [Go](./connect-go.md)
