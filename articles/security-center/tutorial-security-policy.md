---
title: Utilisation de stratégies de sécurité | Microsoft Docs
description: Cet article décrit comment utiliser des stratégies de sécurité dans Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2d248817-ae97-4c10-8f5d-5c207a8019ea
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 94a0c0af5071397ee4deb859f8f58f2ed1c73a77
ms.sourcegitcommit: c94a177b11a850ab30f406edb233de6923ca742a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89277661"
---
# <a name="working-with-security-policies"></a>Utilisation de stratégies de sécurité

Cet article explique comment configurer des stratégies de sécurité et comment les afficher dans Security Center. 

## <a name="introduction-to-security-policies"></a>Présentation des stratégies de sécurité

Une stratégie de sécurité définit la configuration souhaitée de vos charges de travail, et vous permet de vous assurer que vous êtes en conformité avec les exigences de sécurité de votre organisation ou des régulateurs.

Azure Security Center émet ses recommandations de sécurité en fonction des stratégies que vous avez choisies. Les stratégies de Security Center sont basées sur des initiatives de stratégie créées dans Azure Policy. Vous pouvez utiliser [Azure Policy](../governance/policy/overview.md) pour gérer vos stratégies et définir des stratégies pour plusieurs groupes d’administration et abonnements.

Security Center offre les options suivantes pour l’utilisation des stratégies de sécurité :

* **Afficher et modifier la stratégie par défaut intégrée** : lorsque vous activez Security Center, une initiative intégrée nommée « ASC default » est automatiquement attribuée à tous les abonnements inscrits auprès de Security Center (niveaux tarifaires gratuit ou standard). Pour personnaliser cette initiative, vous pouvez activer ou désactiver des stratégies individuelles au sein de celle-ci. Pour comprendre les options disponibles prêtes à l’emploi, consultez la liste des [stratégies de sécurité intégrées](security-center-policy-definitions.md).

* **Ajouter vos propres stratégies personnalisées** : si vous souhaitez personnaliser les initiatives de sécurité appliquées à votre abonnement, vous pouvez le faire dans Security Center. Vous recevez ensuite des recommandations si vos machines ne suivent pas les stratégies que vous créez. Pour obtenir des instructions sur la création et l’attribution de stratégies personnalisées, voir [Utilisation de stratégies de sécurité personnalisées](custom-security-policies.md).

* **Ajouter des stratégies de conformité réglementaire** : le tableau de bord de conformité réglementaire du Security Center affiche l’état de toutes les évaluations au sein de votre environnement dans le contexte d’une norme ou d’une réglementation particulières (par exemple, Azure CIS, NIST SP 800-53 R4, CSP SWIFT CSCF-v2020). Pour plus d’informations, voir [Améliorer votre conformité aux normes](security-center-compliance-dashboard.md).


## <a name="manage-your-security-policies"></a>Gérer vos stratégies de sécurité

Pour afficher vos stratégies de sécurité dans Security Center :

1. Dans le tableau de bord **Security Center**, sélectionnez **Stratégie de sécurité**.

    ![Le volet Gestion des stratégies](./media/security-center-policies/security-center-policy-mgt.png)

   Dans l’écran **Gestion de stratégie**, vous pouvez voir le nombre de groupes d’administration, d’abonnements et d’espaces de travail, ainsi que votre structure de groupes d’administration.

1. Sélectionnez le groupe d’administration ou l’abonnement dont vous souhaitez afficher les stratégies.

1. La page relative à la stratégie de sécurité de cet abonnement ou ce groupe d’administration s’affiche. Elle présente les stratégies disponibles et attribuées.

   ![page stratégie](./media/tutorial-security-policy/security-policy-page.png)

    > [!NOTE]
    > Si l’étiquette « Groupe d’administration hérité » figure en regard de votre stratégie par défaut, cela signifie que la stratégie a été attribuée à un groupe d’administration et que l’abonnement que vous consultez en a hérité.


1. Choisissez parmi les options disponibles sur cette page :

    1. Pour utiliser des stratégies sectorielles, sélectionnez **Ajouter d'autres standards**. Pour plus d’informations, voir [Mise à jour des packages de conformité dynamique](update-regulatory-compliance-packages.md).

    1. Pour attribuer et gérer des initiatives personnalisées, sélectionnez **Ajouter des initiatives personnalisées**. Pour plus d’informations, voir [Utilisation de stratégies de sécurité personnalisées](custom-security-policies.md).

    1. Pour afficher et modifier la stratégie par défaut, sélectionnez **Afficher la stratégie actuelle**, puis procédez comme décrit ci-dessous. 

       ![Écran des stratégies](./media/security-center-policies/policy-screen.png)
       
       L’écran **Stratégie de sécurité** reflète l’action effectuée par les stratégies affectées sur l’abonnement ou le groupe d’administration que vous avez sélectionnés.
       
       * Utilisez les liens fournis en haut pour ouvrir une **attribution de stratégie** s’appliquant à l’abonnement ou au groupe d’administration. Ces liens vous permettent d’accéder à l’attribution et de modifier ou désactiver la stratégie. Par exemple, si vous voyez qu’une attribution de stratégie particulière refuse effectivement la protection du point de terminaison, utilisez le lien pour modifier ou désactiver la stratégie.
       
       * Dans la liste des stratégies, vous pouvez voir l’application effective de la stratégie sur votre groupe d’administration ou abonnement. Les paramètres de chaque stratégie qui s’appliquent à l’étendue sont pris en compte et le résultat cumulé des actions effectuées par la stratégie s’affiche. Par exemple, si un attribution de la stratégie est désactivée, mais définie sur AuditIfNotExist dans une autre, l’effet cumulé applique AuditIfNotExist. L’effet le plus actif est toujours prioritaire.
       
       * L’effet des stratégies peut être : Append, Audit, AuditIfNotExists, Deny, DeployIfNotExists, Disabled. Pour plus d’informations sur la manière dont les effets sont appliqués, consultez [Comprendre les effets de Policy](../governance/policy/concepts/effects.md).

       > [!NOTE]
       > Lorsque vous affichez les stratégies attribuées, vous pouvez voir plusieurs affectations et comment chacune d’elles est configurée individuellement.


## <a name="who-can-edit-security-policies"></a>Qui peut modifier des stratégies de sécurité ?

Vous pouvez modifier les stratégies de sécurité sur le portail Azure Policy, par l’intermédiaire de l’API REST ou à l’aide de Windows PowerShell.

Security Center utilise le contrôle d’accès en fonction du rôle (RBAC), lequel fournit des rôles intégrés que vous pouvez attribuer à des utilisateurs, groupes et services Azure. Lorsque les utilisateurs ouvrent Security Center, ils ne voient que les informations associées aux ressources auxquelles ils peuvent accéder. Autrement dit, les utilisateurs se voient attribuer le rôle de *propriétaire*, *contributeur* ou *lecteur* pour l’abonnement de la ressource. Il existe également deux rôles spécifiques à Security Center :

- **Lecteur Sécurité** : a le droit de consulter les éléments de Security Center tels que les recommandations, les alertes, la stratégie et l’intégrité. Ne peut pas apporter de modifications.
- **Administrateur Sécurité** : a les mêmes droits de consultation que *Lecteur Sécurité*. Peut également mettre à jour la stratégie de sécurité et ignorer les alertes.


## <a name="disable-security-policies-and-disable-recommendations"></a>Désactiver des stratégies de sécurité et des recommandations

Lorsque votre initiative de sécurité déclenche une recommandation qui n’est pas pertinente pour votre environnement, vous pouvez empêcher cette recommandation de s’afficher à nouveau. Pour désactiver une recommandation, désactivez la stratégie spécifique qui génère la recommandation.

La recommandation que vous souhaitez désactiver continuera de s’afficher si elle est requise par une norme réglementaire que vous avez appliquée à l’aide des outils de conformité réglementaire de Security Center. Même si vous avez désactivé une stratégie dans l’initiative intégrée, une autre de l’initiative de la norme réglementaire déclenchera toujours la recommandation si celle-ci est nécessaire à la conformité. Vous ne pouvez pas désactiver les stratégies d’initiatives de normes réglementaires.

Pour plus d’informations sur les recommandations, consultez [Gestion des recommandations de sécurité](security-center-recommendations.md).

1. Dans la section **Stratégie et conformité** de Security Center, sélectionnez **Stratégie de sécurité**.

   ![gestion des stratégies](./media/tutorial-security-policy/policy-management.png)

2. Sélectionnez le groupe d'abonnements ou d'administration pour lequel vous souhaitez désactiver la recommandation.

   > [!NOTE]
   > N’oubliez pas qu’un groupe d’administration applique ses stratégies à ses abonnements. Par conséquent, si vous désactivez la stratégie d’un abonnement et que celui-ci appartient à un groupe d’administration qui utilise encore la même stratégie, vous continuez à recevoir les recommandations de la stratégie. La stratégie sera toujours appliquée à partir du niveau d’administration, et les suggestions seront toujours générées.

1. Sélectionnez **Afficher la stratégie actuelle**.

   ![afficher la stratégie](./media/tutorial-security-policy/view-effective-policy.png)

1. Sélectionnez la stratégie attribuée.

   ![sélectionner une stratégie](./media/tutorial-security-policy/security-policy.png)

1. Dans la section **PARAMÈTRES**, recherchez la stratégie qui appelle la suggestion que vous souhaitez désactiver, puis sélectionnez **Désactiver** dans la liste déroulante.

   ![désactiver une stratégie](./media/tutorial-security-policy/disable-policy.png)

1. Sélectionnez **Enregistrer**.

   > [!NOTE]
   > La désactivation d’une stratégie peut prendre jusqu’à 12 heures pour entrer en vigueur.



## <a name="next-steps"></a>Étapes suivantes
Cet article a décrit les stratégies de sécurité. Pour accéder à des informations connexes, voir les articles suivants :

* Pour obtenir des instructions sur la façon de définir des stratégies à l’aide de PowerShell, consultez [Démarrage rapide : Créer une affectation de stratégie pour identifier les ressources non conformes à l’aide du module Azure PowerShell](../governance/policy/assign-policy-powershell.md)

* Pour obtenir des instructions sur la façon de modifier une stratégie de sécurité dans Azure Policy, voir [Créer et gérer des stratégies pour appliquer la conformité](../governance/policy/tutorials/create-and-manage.md).

* Pour obtenir des instructions sur la manière de définir une stratégie sur des abonnements ou des groupes d’administration à l’aide d’Azure Policy, voir [Présentation d’Azure Policy](../governance/policy/overview.md)