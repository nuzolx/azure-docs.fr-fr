---
title: Sortie dans Azure SQL Database d’Azure Stream Analytics
description: Cet article décrit le Azure SQL Database en tant que sortie pour Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: d61cad0be25f5aa1a4c63bf3dc128196ad80fdad
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88875412"
---
# <a name="azure-sql-database-output-from-azure-stream-analytics"></a>Sortie dans Azure SQL Database d’Azure Stream Analytics

Vous pouvez utiliser [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) comme sortie pour les données relationnelles ou pour les applications qui dépendent de contenus hébergés dans une base de données relationnelle. Les travaux Azure Stream Analytics écrivent dans une table existante dans SQL Database. Le schéma de table doit correspondre exactement aux champs et aux types dans la sortie de votre travail. Vous pouvez également spécifier [Azure SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) en tant que sortie via l’option de sortie SQL Database. Pour découvrir les moyens d’améliorer le débit d’écriture, consultez l’article [Sortie d’Azure Stream Analytics dans Azure SQL Database](stream-analytics-sql-output-perf.md).

Vous pouvez aussi utiliser [Azure SQL Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) comme sortie. Vous devez [configurer un point de terminaison public dans SQL Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure), puis configurer manuellement les paramètres suivants dans Azure Stream Analytics. Une machine virtuelle Azure exécutant SQL Server auquel une base de données est attachée est également prise en charge en configurant manuellement les paramètres ci-dessous.

## <a name="output-configuration"></a>Configuration de la sortie

Le tableau suivant répertorie les noms de propriétés et leur description pour la création d’une sortie SQL Database.

| Nom de la propriété | Description |
| --- | --- |
| Alias de sortie |Nom convivial utilisé dans les requêtes pour diriger la sortie de requête vers cette base de données. |
| Base de données | Nom de la base de données où vous envoyez votre sortie. |
| Nom du serveur | Nom du serveur SQL logique ou nom de l’instance managée. Pour SQL Managed Instance, il est nécessaire de spécifier le port 3342. Par exemple, *sampleserver.public.database.windows.net,3342* |
| Nom d’utilisateur | Nom de l’utilisateur qui a accès en écriture à la base de données. Stream Analytics prend uniquement en charge l’authentification SQL. |
| Mot de passe | Mot de passe de connexion à la base de données. |
| Table de charge de travail | Nom de la table dans laquelle la sortie sera écrite. Le nom de la table respecte la casse. Le schéma de cette table doit correspondre exactement au nombre de champs et aux types que votre sortie de travail génère. |
|Hériter du schéma de partition| Option qui permet d’hériter du schéma de partition de l’étape de requête précédente afin d’obtenir une topologie entièrement parallèle avec plusieurs rédacteurs dans la table. Pour plus d'informations, consultez [Sortie d'Azure Stream Analytics dans Azure SQL Database](stream-analytics-sql-output-perf.md).|
|Nombre maximal de lots| Limite supérieure recommandée pour le nombre d’enregistrements envoyés avec chaque transaction d’insertion en bloc.|

Deux adaptateurs sont activés pour la sortie d'Azure Stream Analytics vers Azure Synapse Analytics (anciennement SQL Data Warehouse) : SQL Database et Azure Synapse. Nous vous recommandons d’opter pour l’adaptateur Azure Synapse Analytics plutôt que l’adaptateur SQL Database si l’une des conditions suivantes est remplie :

* **Débit** : Si le débit attendu est supérieur à 10 Mo/s, utilisez l’option de sortie Azure Synapse pour obtenir de meilleures performances.

* **Partitions d'entrée** : Si vous disposez d'un minimum de huit partitions, utilisez l’option de sortie Azure Synapse pour améliorer la montée en charge.

## <a name="partitioning"></a>Partitionnement

Le partitionnement doit être activé et est basé sur la clause PARTITION BY de la requête. Lorsque l’option Héritage du partitionnement est activée, elle suit le partitionnement d’entrée pour les [requêtes entièrement parallélisables](stream-analytics-scale-jobs.md). Pour améliorer vos performances de débit d’écriture lorsque vous chargez des données dans Azure SQL Database, consultez [Sortie d’Azure Stream Analytics dans Azure SQL Database](stream-analytics-sql-output-perf.md).

## <a name="output-batch-size"></a>Taille de lot de sortie

Vous pouvez configurer la taille maximale de message à l’aide du **Nombre maximal de lots**. La valeur maximale par défaut est 10 000 et la valeur minimale par défaut est de 100 lignes par insertion en bloc. Pour plus d’informations, consultez [Limites d’Azure DNS](../sql-database/sql-database-resource-limits.md). Chaque lot est initialement inséré en bloc avec le nombre de lots maximum. Le lot est divisé de moitié (jusqu’au nombre de lots minimum) en fonction des erreurs renouvelables de SQL.

## <a name="next-steps"></a>Étapes suivantes

* [Démarrage rapide : Créer un travail Stream Analytics à l’aide du portail Azure](stream-analytics-quick-create-portal.md)
* [Démarrage rapide : Créer un travail Azure Stream Analytics avec l’interface de ligne de commande Azure](quick-create-azure-cli.md)
* [Démarrage rapide : Créer une tâche Azure Stream Analytics à l’aide d’un modèle Resource Manager](quick-create-azure-resource-manager.md)
* [Démarrage rapide : Créer un travail Stream Analytics à l’aide d’Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Démarrage rapide : Créer une tâche Azure Stream Analytics à l’aide de Visual Studio](stream-analytics-quick-create-vs.md)
* [Démarrage rapide : Créer une tâche Azure Stream Analytics dans Visual Studio Code](quick-create-vs-code.md)
