---
title: Mettre à niveau la configuration d’un cluster Azure Service Fabric
description: Découvrez comment mettre à niveau la configuration qui exécute un cluster Service Fabric dans Azure à l’aide d’un modèle Resource Manager.
author: dkkapur
ms.topic: conceptual
ms.date: 11/09/2018
ms.author: dekapur
ms.openlocfilehash: 010f2b5d6dee3a5985c421e468bcd28b18148e5a
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2020
ms.locfileid: "86247725"
---
# <a name="upgrade-the-configuration-of-a-cluster-in-azure"></a>Mettre à niveau la configuration d’un cluster dans Azure 

Cet article décrit comment personnaliser les différents paramètres de structure pour votre cluster Service Fabric. Pour des clusters hébergés dans Azure, vous pouvez personnaliser les paramètres via le [portail Azure](https://portal.azure.com) ou en utilisant un modèle Azure Resource Manager.

> [!NOTE]
> Certains paramètres ne sont pas disponibles dans le portail, et c'est une [bonne pratique de le personnaliser en utilisant un modèle Azure Resource Manager](./service-fabric-best-practices-infrastructure-as-code.md) ; le portail est réservé aux scénarios Service Fabric Dev\Test.
> 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="customize-cluster-settings-using-resource-manager-templates"></a>Personnaliser les paramètres de cluster à l’aide de modèles Resource Manager
Les clusters Azure peuvent être configurés via le modèle Resource Manager JSON. Pour en savoir plus sur les différents paramètres, consultez [Paramètres de configuration pour les clusters](service-fabric-cluster-fabric-settings.md). À titre d’exemple, la procédure ci-dessous montre comment ajouter un nouveau paramètre *MaxDiskQuotaInMB* dans la section *Diagnostics* à l’aide d’Azure Resource Explorer.

1. Accédez à https://resources.azure.com
2. Accédez à votre abonnement en développant **subscriptions** ->  **\<Your Subscription>**  -> **resourceGroups** ->  **\<Your Resource Group>**  -> **providers** -> **Microsoft.ServiceFabric** -> **clusters** ->  **\<Your Cluster Name>**
3. Dans l’angle supérieur droit, sélectionnez **Lecture/écriture**.
4. Sélectionnez **Modifier**, mettez à jour l’élément JSON `fabricSettings` et ajoutez un nouvel élément :

```json
      {
        "name": "Diagnostics",
        "parameters": [
          {
            "name": "MaxDiskQuotaInMB",
            "value": "65536"
          }
        ]
      }
```

Vous pouvez également personnaliser des paramètres de cluster de l’une des manières suivantes avec Azure Resource Manager :

- Utiliser le [portail Azure](../azure-resource-manager/templates/export-template-portal.md) pour exporter et mettre à jour le modèle Resource Manager.
- Utiliser [PowerShell](../azure-resource-manager/management/manage-resources-powershell.md) pour exporter et mettre à jour le modèle Resource Manager.
- Utiliser le [Azure CLI](../azure-resource-manager/management/manage-resources-cli.md) pour exporter et mettre à jour le modèle Resource Manager.
- Utiliser les commandes Azure PowerShell [Set-AzServiceFabricSetting](/powershell/module/az.servicefabric/set-azservicefabricsetting) et [Remove-AzServiceFabricSetting](/powershell/module/az.servicefabric/remove-azservicefabricsetting) pour modifier le paramètre directement.
- Utiliser les commandes de [paramétrage de cluster az sf](/cli/azure/sf/cluster/setting) d’Azure CLI pour modifier le paramètre directement.

## <a name="next-steps"></a>Étapes suivantes
* Apprenez-en davantage sur les [paramètres de cluster Service Fabric](service-fabric-cluster-fabric-settings.md).
* Découvrez comment [diminuer et augmenter la taille de votre cluster](service-fabric-cluster-scale-in-out.md).
