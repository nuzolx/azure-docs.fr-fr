---
title: Configurer et utiliser Azure Synapse Link pour Azure Cosmos DB (préversion)
description: Découvrez comment activer le lien Synapse pour les comptes Azure Cosmos, créer un conteneur avec le magasin analytique activé, connecter la base de données Azure Cosmos à l’espace de travail Synapse et exécuter des requêtes.
author: Rodrigossz
ms.service: cosmos-db
ms.topic: how-to
ms.date: 08/31/2020
ms.author: rosouz
ms.openlocfilehash: d77922d5957a0f6602e00621ebbabeaa3f3ac8f9
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89322464"
---
# <a name="configure-and-use-azure-synapse-link-for-azure-cosmos-db-preview"></a>Configurer et utiliser Azure Synapse Link pour Azure Cosmos DB (préversion)

Synapse Link pour Azure Cosmos DB est une fonctionnalité de traitement transactionnel et analytique (HTAP) hybride cloud native qui vous permet d’exécuter des analyses en temps quasi-réel sur les données opérationnelles dans Azure Cosmos DB. Synapse Link crée une intégration transparente entre Azure Cosmos DB et Azure Synapse Analytics.


> [!IMPORTANT]
> Pour utiliser Azure Synapse Link, veillez à provisionner votre compte Azure Cosmos et votre espace de travail Azure Synapse Analytics dans l’une des régions prises en charge. Azure Synapse Link est actuellement disponible dans les régions Azure suivantes : USA Centre-Ouest, USA Est, USA Ouest 2, Europe Nord, Europe Ouest, USA Centre Sud, Asie Sud-Est, Australie Est, USA Est 2, Royaume-Uni Sud.

Pour exécuter des requêtes analytiques avec Synapse Link pour Azure Cosmos DB, vous devez :

* [Activer les comptes Synapse Link pour Azure Cosmos](#enable-synapse-link)
* [Créer un conteneur Azure Cosmos activé pour le magasin analytique](#create-analytical-ttl)
* [Connecter votre base de données Azure Cosmos DB à un espace de travail Synapse](#connect-to-cosmos-database)
* [Interroger le magasin analytique à l’aide de Synapse Spark](#query-analytical-store)

## <a name="enable-azure-synapse-link-for-azure-cosmos-accounts"></a><a id="enable-synapse-link"></a>Activer les comptes Azure Synapse Link pour Azure Cosmos

### <a name="azure-portal"></a>Portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. [Créez un compte Azure](create-sql-api-dotnet.md#create-account) ou sélectionnez un compte Azure Cosmos existant.

1. Accédez à votre compte Azure Cosmos et ouvrez le volet **Nouvelles fonctionnalités**.

1. Sélectionnez **Synapse Link** dans la liste des fonctionnalités.

   :::image type="content" source="./media/configure-synapse-link/find-synapse-link-feature.png" alt-text="Rechercher la fonctionnalité d’évaluation Synapse Link":::

1. Ensuite, vous êtes invité à activer le lien Synapse sur votre compte. Sélectionnez Activer.

   :::image type="content" source="./media/configure-synapse-link/enable-synapse-link-feature.png" alt-text="Activer la fonctionnalité Synapse Link":::

1. Votre compte est désormais activé pour l’utilisation de Synapse Link. Ensuite, découvrez comment créer des conteneurs activés pour le magasin analytique afin de démarrer automatiquement la réplication de vos données opérationnelles entre le magasin transactionnel et le magasin analytique.

### <a name="azure-resource-manager-template"></a>Modèle Azure Resource Manager

Le [modèle Azure Resource Manager](manage-sql-with-resource-manager.md#azure-cosmos-account-with-analytical-store) crée un compte Azure Cosmos activé avec Synapse Link pour l’API SQL. Ce modèle crée un compte d’API Core (SQL) dans une région avec un conteneur configuré avec une durée de vie analytique activée et une option d’utilisation de débit manuel ou de mise à l’échelle automatique. Pour déployer ce modèle, cliquez sur **Déployer sur Azure** dans la page lisezmoi (readme).

## <a name="create-an-azure-cosmos-container-with-analytical-store"></a><a id="create-analytical-ttl"></a> Créer un conteneur Azure Cosmos avec magasin analytique

Vous pouvez activer le magasin analytique sur un conteneur Azure Cosmos lors de la création du conteneur. Vous pouvez utiliser le Portail Azure ou configurer la propriété `analyticalTTL` lors de la création du conteneur à l’aide des kits de développement logiciel (SDK) Azure Cosmos DB.

> [!NOTE]
> Actuellement, vous pouvez activer le magasin analytique pour les **nouveaux** conteneurs (à la fois dans les nouveaux comptes et les comptes existants).

### <a name="azure-portal"></a>Portail Azure

1. Connectez-vous au [Portail Azure](https://portal.azure.com/) ou à l’[explorateur Azure Cosmos](https://cosmos.azure.com/).

1. Accédez à votre compte Azure Cosmos et ouvrez l'onglet **Explorateur de données**.

1. Sélectionnez **Nouveau conteneur**, puis entrez un nom pour la base de données, le conteneur, la clé de partition et les détails du débit. Activez l’option **Magasin analytique**. Une fois que vous avez activé le magasin analytique, il crée un conteneur avec la propriété `AnalyicalTTL` définie sur la valeur par défaut -1 (conservation infinie). Ce magasin analytique conserve toutes les versions historiques des enregistrements.

   :::image type="content" source="./media/configure-synapse-link/create-container-analytical-store.png" alt-text="Activer le magasin analytique pour le conteneur Azure Cosmos":::

1. Si vous n’avez pas encore activé Synapse Link sur ce compte, vous êtes invité à le faire parce qu’il s’agit d’une condition préalable à la création d’un conteneur pour lequel le magasin analytique est activé. Si vous y êtes invité, sélectionnez **Activer Synapse Link**.

1. Sélectionnez **OK** pour créer un conteneur Azure Cosmos activé pour le magasin analytique.

### <a name="net-sdk"></a>Kit de développement logiciel (SDK) .NET

Le code suivant crée un conteneur avec le magasin analytique à l’aide du kit de développement logiciel (SDK) .NET. Définissez la propriété TTL analytique sur la valeur requise. Pour obtenir la liste des valeurs autorisées, consultez l’article sur les [valeurs de durée de vie analytique prises en charge](analytical-store-introduction.md#analytical-ttl) :

```csharp
// Create a container with a partition key, and analytical TTL configured to  -1 (infinite retention)
string containerId = “myContainerName”;
int analyticalTtlInSec = -1;
ContainerProperties cpInput = new ContainerProperties()
            {
Id = containerId,
PartitionKeyPath = "/id",
AnalyticalStorageTimeToLiveInSeconds = analyticalTtlInSec,
};
 await this. cosmosClient.GetDatabase("myDatabase").CreateContainerAsync(cpInput);
```

### <a name="java-v4-sdk"></a>SDK Java V4

Le code suivant crée un conteneur avec le magasin analytique à l’aide du kit de développement logiciel (SDK) Java V4. Définissez la propriété `AnalyticalStoreTimeToLiveInSeconds` sur la valeur requise :

```java
// Create a container with a partition key and  analytical TTL configured to  -1 (infinite retention) 
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");

containerProperties.setAnalyticalStoreTimeToLiveInSeconds(-1);

container = database.createContainerIfNotExists(containerProperties, 400).block().getContainer();
```

### <a name="python-v4-sdk"></a>Kit SDK Python V4

Le kit SDK 4.1.0 Azure Cosmos DB et Python 2.7 sont les versions minimales requises, et ce kit SDK est compatible uniquement avec l’API SQL.

La première étape consiste à s’assurer que vous utilisez au moins la version 4.1.0 du [kit SDK Python pour Azure Cosmos DB](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos) :

```python
import azure.cosmos as cosmos

print (cosmos.__version__)
```
L’étape suivante crée un conteneur avec un magasin analytique à l’aide du kit SDK Python pour Azure Cosmos DB :

```python
# Azure Cosmos DB Python SDK, for SQL API only.
# Creating an analytical store enabled container.

import azure.cosmos.cosmos_client as cosmos_client
import azure.cosmos.exceptions as exceptions
from azure.cosmos.partition_key import PartitionKey

HOST = 'your-cosmos-db-account-URI'
KEY = 'your-cosmos-db-account-key'
DATABASE = 'your-cosmos-db-database-name'
CONTAINER = 'your-cosmos-db-container-name'

client = cosmos_client.CosmosClient(HOST,  KEY )
# setup database for this sample. 
# If doesn't exist, creates a new one with the name informed above.
try:
    db = client.create_database(DATABASE)

except exceptions.CosmosResourceExistsError:
    db = client.get_database_client(DATABASE)

# Creating the container with analytical store enabled, using the name informed above.
# If a container with the same name exists, an error is returned.
#
# The 3 options for the analytical_storage_ttl parameter are:
# 1) 0 or Null or not informed (Not enabled).
# 2) -1 (The data will be stored in analytical store infinitely).
# 3) Any other number is the actual ttl, in seconds.

try:
    container = db.create_container(
        id=CONTAINER,
        partition_key=PartitionKey(path='/id', kind='Hash'),analytical_storage_ttl=-1
    )
    properties = container.read()
    print('Container with id \'{0}\' created'.format(container.id))
    print('Partition Key - \'{0}\''.format(properties['partitionKey']))

except exceptions.CosmosResourceExistsError:
    print('A container with already exists')
```

### <a name="update-the-analytical-store-time-to-live"></a><a id="update-analytical-ttl"></a> Mettre à jour la durée de vie du magasin analytique

Une fois que le magasin analytique est activé avec une valeur TTL particulière, vous pouvez le mettre à jour avec une valeur valide différente ultérieurement. Vous pouvez mettre à jour la valeur à l’aide du Portail Azure ou des kits de développement logiciel (SDK). Pour plus d’informations sur les différentes options de configuration de la durée de vie analytique, consultez l’article [Valeurs de durée de vie analytique prises en charge](analytical-store-introduction.md#analytical-ttl).

#### <a name="azure-portal"></a>Portail Azure

Si vous avez créé un conteneur de magasin analytique activé via le Portail Azure, sa durée de vie analytique par défaut est égale à -1. Pour mettre à jour cette valeur, procédez comme suit :

1. Connectez-vous au [Portail Azure](https://portal.azure.com/) ou à l’[explorateur Azure Cosmos](https://cosmos.azure.com/).

1. Accédez à votre compte Azure Cosmos et ouvrez l'onglet **Explorateur de données**.

1. Sélectionnez un conteneur existant pour lequel le magasin analytique est activé. Développez-le et modifiez les valeurs suivantes :

  * Ouvrez la fenêtre **Mise à l’échelle et paramètres**.
  * Sous **Paramètre**, recherchez ** Analytical Storage Time to Live**.
  * Sélectionnez **Activée (pas par défaut)** ou sélectionnez **Activée** et définissez une valeur de TTL.
  * Cliquez sur **Enregistrer** pour enregistrer les modifications.

#### <a name="net-sdk"></a>Kit de développement logiciel (SDK) .NET

Le code suivant illustre la mise à jour de la durée de vie du magasin analytique à l’aide du kit de développement logiciel (SDK) .NET :

```csharp
// Get the container, update AnalyticalStorageTimeToLiveInSeconds 
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync();
// Update analytical store TTL
containerResponse.Resource. AnalyticalStorageTimeToLiveInSeconds = 60 * 60 * 24 * 180  // Expire analytical store data in 6 months;
await client.GetContainer("database", "container").ReplaceContainerAsync(containerResponse.Resource);
```

#### <a name="java-v4-sdk"></a>SDK Java V4

Le code suivant illustre la mise à jour de la durée de vie du magasin analytique à l’aide du kit de développement logiciel (SDK) Java V4 :

```java
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");

// Update analytical store TTL to expire analytical store data in 6 months;
containerProperties.setAnalyticalStoreTimeToLiveInSeconds (60 * 60 * 24 * 180 );  
 
// Update container settings
container.replace(containerProperties).block();
```

## <a name="connect-to-a-synapse-workspace"></a><a id="connect-to-cosmos-database"></a> Se connecter à un espace de travail Synapse

Suivez les instructions de [Se connecter à Azure Synapse Link](../synapse-analytics/synapse-link/how-to-connect-synapse-link-cosmos-db.md) sur la façon d’accéder à une base de données Azure Cosmos DB à partir d’Azure Synapse Analytics Studio avec Azure Synapse Link.

## <a name="query-using-synapse-spark"></a><a id="query-analytical-store"></a> Requête utilisant Synapse Spark

Suivez les instructions de l’article [Interroger le magasin analytique Azure Cosmos DB](../synapse-analytics/synapse-link/how-to-query-analytical-store-spark.md) sur la procédure d’interrogation avec Synapse Spark. Cet article donne des exemples sur la façon dont vous pouvez interagir avec le magasin analytique à partir de mouvements Synapse. Ces mouvements sont visibles lorsque vous cliquez avec le bouton droit sur un conteneur. Avec les mouvements, vous pouvez rapidement générer du code et l’adapter à vos besoins. Ils sont également bien adaptés pour découvrir des données en un seul clic.

## <a name="getting-started-with-azure-synpase-link---samples"></a><a id="cosmosdb-synapse-link-samples"></a> Prise en main d’Azure Synapse Link : exemples

Vous trouverez des exemples de prise en main d’Azure Synapse Link sur [GitHub](https://aka.ms/cosmosdb-synapselink-samples). Ils mettent en avant des solutions de bout en bout pour des scénarios IoT et de vente au détail.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus, consultez les documents suivants :

* [Azure Synapse Link pour Azure Cosmos DB.](synapse-link.md)

* [Vue d’ensemble du magasin analytique Azure Cosmos DB.](analytical-store-introduction.md)

* [Forum aux questions sur Azure Synapse Link pour Azure Cosmos DB.](synapse-link-frequently-asked-questions.md)

* [Apache Spark dans Azure Synapse Analytics](../synapse-analytics/spark/apache-spark-concepts.md).

* [Prise en charge de runtime serverless SQL dans Azure Synapse Analytics](../synapse-analytics/sql/on-demand-workspace-overview.md).
