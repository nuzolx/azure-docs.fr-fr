---
title: Stockage de données d’identité pour les clients européens - Azure AD
description: Découvrez où Azure Active Directory stocke les données relatives à l’identité de ses clients européens.
services: active-directory
author: ajburnle
manager: daveba
ms.author: ajburnle
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 03/04/2019
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: d57b7a559464bfff0d3d1c6436641257c925d75e
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89318469"
---
# <a name="identity-data-storage-for-european-customers-in-azure-active-directory"></a>Stockage de données d’identité pour les clients européens dans Azure Active Directory
Les données d’identité sont stockées par Azure AD dans un emplacement géographique en fonction de l’adresse fournie par votre organisation lors de l’abonnement à un service Microsoft Online tel qu’Office 365 ou Azure. Pour obtenir des informations sur le lieu de stockage des données d’identité, vous pouvez utiliser la section [Où se trouvent vos données ?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) du Centre de gestion de la confidentialité Microsoft.

Pour les clients qui ont fourni une adresse en Europe, Azure AD conserve la plupart des données d’identité dans des centres de données européens. Ce document apporte des informations sur toutes les données stockées en dehors de l’Europe par les services Azure AD.

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>Microsoft Azure Multi-Factor Authentication (MFA)
    
- Toutes les authentifications à deux facteurs s’appuyant sur des appels téléphoniques ou des SMS sont issues de centres de données des États-Unis et sont également routées par des fournisseurs globaux.
- Les notifications Push qui utilisent l’application Microsoft Authenticator proviennent des centres de données des États-Unis. En outre, des services spécifiques des fournisseurs d’appareils, situés en dehors de l’Europe, peuvent également entrer en jeu.
- Les codes OATH sont toujours validés aux États-Unis. 

Pour plus d’informations sur les informations utilisateur collectées par le serveur Azure Multi-Factor Authentication (serveur MFA) et Azure MFA basé sur le cloud, consultez [Collecte de données utilisateur via Azure Multi-Factor Authentication](../authentication/howto-mfa-reporting-datacollection.md).

## <a name="microsoft-azure-active-directory-b2c-azure-ad-b2c"></a>Microsoft Azure Active Directory B2C (Azure AD B2C)

Les données de configuration de stratégie Azure AD B2C et les conteneurs de clés sont stockés dans les centres de données des États-Unis. Ces derniers ne contiennent pas les données personnelles utilisateur. Pour plus d’informations sur les configurations de stratégie, consultez l’article [Azure Active Directory B2C : stratégies intégrées](../../active-directory-b2c/user-flow-overview.md).

## <a name="microsoft-azure-active-directory-b2b-azure-ad-b2b"></a>Microsoft Azure Active Directory B2B (Azure AD B2B) 
    
Azure AD B2B stocke les invitations avec lien d’acceptation et URL de redirection dans les centres de données des États-Unis. En outre, les adresses e-mail des utilisateurs qui se désabonnent des invitations B2B sont également stockées dans les centres de données des États-Unis.

## <a name="microsoft-azure-active-directory-domain-services-azure-ad-ds"></a>Microsoft Azure Active Directory Domain Services (Azure AD DS)

Azure AD DS stocke les données utilisateur au même emplacement que le réseau virtuel Azure choisi par le client. Par conséquent, si le réseau se situe en dehors de l’Europe, les données sont répliquées et stockées en dehors de l’Europe.

## <a name="federation-in-microsoft-exchange-server-2013"></a>Fédération dans Microsoft Exchange Server 2013
    
- Identificateur d’application (AppID) : numéro unique généré par le système d’authentification Azure Active Directory pour identifier les organisations Exchange.
- Liste des domaines fédérés approuvés pour l’application
- Clé publique de signature des jetons de l’application 

Pour plus d’informations sur la fédération dans Microsoft Exchange Server, consultez [Fédération : Article d’aide sur Exchange 2013](/exchange/federation-exchange-2013-help).


## <a name="other-considerations"></a>Autres considérations

Les services et les applications s’intégrant avec Azure AD ont accès aux données d’identité. Évaluez chaque service et application que vous utilisez pour déterminer comment ils traitent les données d’identité, et s’ils répondent aux besoins de stockage de données de votre entreprise.

Pour plus d’informations sur la résidence des données des services de Microsoft, consultez la section [Où se trouvent vos données ?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) du Microsoft Trust Center.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les fonctions et fonctionnalités décrites ci-dessus, consultez les articles suivants :
- [Présentation d’authentification multifacteur](../authentication/concept-mfa-howitworks.md)

- [Réinitialisation de mot de passe en libre-service Azure AD](../authentication/concept-sspr-howitworks.md)

- [Qu’est-ce qu’Azure Active Directory B2C ?](../../active-directory-b2c/overview.md)

- [Qu'est-ce que la collaboration B2B d'Azure AD ?](../external-identities/what-is-b2b.md)

- [Services de domaine Azure Active Directory (AD)](../../active-directory-domain-services/overview.md)