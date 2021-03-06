---
title: Créer un volume SMB pour Azure NetApp Files | Microsoft Docs
description: Cet article explique comment créer un volume SMBv3 dans Azure NetApp Files. Découvrez la configuration requise pour les services de domaine et connexions Active Directory.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/26/2020
ms.author: b-juche
ms.openlocfilehash: e85a78582c0f7aac188198ad91f9ac91ddf62961
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88950372"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>Créer un volume SMB pour Azure NetApp Files

Azure NetApp Files prend en charge la création de volumes avec NFS (NFSv3 et NFSv4.1), SMBv3 ou le double protocole (NFSv3 et SMB). La consommation de capacité d’un volume est comptée par rapport à la capacité configurée de son pool. Cet article explique comment créer un volume SMBv3.

## <a name="before-you-begin"></a>Avant de commencer 
Vous devez déjà avoir configuré un pool de capacité.   
[Configurer un pool de capacité](azure-netapp-files-set-up-capacity-pool.md)   
Un sous-réseau doit être délégué à Azure NetApp Files.  
[Déléguer un sous-réseau à Azure NetApp Files](azure-netapp-files-delegate-subnet.md)

## <a name="requirements-for-active-directory-connections"></a>Configuration requise pour les connexions Active Directory

 Vous devez créer des connexions Active Directory avant de créer un volume SMB. La configuration requise pour les connexions Active Directory est la suivante : 

* Le compte d’administrateur que vous utilisez doit avoir la capacité de créer des comptes d’ordinateur dans le chemin de l’unité d’organisation (UO) que vous allez spécifier.  

* Les ports appropriés doivent être ouverts sur le serveur Windows Active Directory (AD) applicable.  
    Les ports requis sont les suivants : 

    |     Service           |     Port     |     Protocol     |
    |-----------------------|--------------|------------------|
    |    Services web AD    |    9389      |    TCP           |
    |    DNS                |    53        |    TCP           |
    |    DNS                |    53        |    UDP           |
    |    ICMPv4             |    N/A       |    Réponse à écho    |
    |    Kerberos           |    464       |    TCP           |
    |    Kerberos           |    464       |    UDP           |
    |    Kerberos           |    88        |    TCP           |
    |    Kerberos           |    88        |    UDP           |
    |    LDAP               |    389       |    TCP           |
    |    LDAP               |    389       |    UDP           |
    |    LDAP               |    3268      |    TCP           |
    |    Nom NetBIOS       |    138       |    UDP           |
    |    SAM/LSA            |    445       |    TCP           |
    |    SAM/LSA            |    445       |    UDP           |
    |    w32time            |    123       |    UDP           |

* La topologie de site pour les services Active Directory Domain Services ciblés doit respecter les instructions, en particulier le réseau virtuel Azure où Azure NetApp Files est déployé.  

    L’espace d’adressage du réseau virtuel où Azure NetApp Files est déployé doit être ajouté à un site Active Directory nouveau ou existant (où se trouve un contrôleur de domaine accessible par Azure NetApp Files). 

* Les serveurs DNS spécifiés doivent être accessibles à partir du [sous-réseau délégué](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet) d’Azure NetApp Files.  

    Consultez [Consignes pour planifier un réseau Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) pour découvrir les topologies de réseau prises en charge.

    Les groupes de sécurité réseau et les pare-feu doivent avoir des règles configurées de manière appropriée pour autoriser les demandes de trafic Active Directory et DNS. 

* Le sous-réseau délégué Azure NetApp Files doit pouvoir accéder à tous les contrôleurs de domaine Active Directory Domain Services (AD DS) dans le domaine, y compris tous les contrôleurs de domaine locaux et distants. Si ce n'est pas le cas, une interruption de service peut se produire.  

    Si vous avez des contrôleurs de domaine que le sous-réseau délégué Azure NetApp Files ne peut pas atteindre, vous pouvez spécifier un site Active Directory au moment de créer la connexion Active Directory.  Azure NetApp Files doit communiquer uniquement avec les contrôleurs de domaine du site où se trouve l’espace d’adressage du sous-réseau délégué Azure NetApp Files.

    Consultez [Conception de la topologie du site](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/designing-the-site-topology) sur les sites et services Active Directory. 
    
<!--
* Azure NetApp Files supports DES, Kerberos AES 128, and Kerberos AES 256 encryption types (from the least secure to the most secure). The user credentials used to join Active Directory must have the highest corresponding account option enabled that matches the capabilities enabled for your Active Directory.   

    For example, if your Active Directory has only the AES-128 capability, you must enable the AES-128 account option for the user credentials. If your Active Directory has the AES-256 capability, you must enable the AES-256 account option (which also supports AES-128). If your Active Directory does not have any Kerberos encryption capability, Azure NetApp Files uses DES by default.  

    You can enable the account options in the properties of the Active Directory Users and Computers Microsoft Management Console (MMC):   

    ![Active Directory Users and Computers MMC](../media/azure-netapp-files/ad-users-computers-mmc.png)
-->

Pour plus d’informations sur AD, consultez les [Questions fréquentes sur SMB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs) pour Azure NetApp Files. 

## <a name="decide-which-domain-services-to-use"></a>Déterminer quels services de domaine utiliser 

Azure NetApp Files prend en charge à la fois [Active Directory Domain Services](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) (ADDS) et Azure Active Directory Domain Services (AADDS) pour les connexions AD.  Avant de créer une connexion AD, vous devez choisir entre ADDS et AADDS.  

Pour plus d’informations, consultez [Comparer les services Active Directory Domain Services auto-gérés, Azure Active Directory et Azure Active Directory Domain Services managés](https://docs.microsoft.com/azure/active-directory-domain-services/compare-identity-solutions). 

### <a name="active-directory-domain-services"></a>Active Directory Domain Services

Vous pouvez utiliser l’étendue [Sites et services Active Directory](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) préférée pour Azure NetApp Files. Cette option autorise les accès en lecture et écriture aux contrôleurs de domaine Active Directory Domain Services (ADDS) [accessibles à Azure NetApp Files](azure-netapp-files-network-topologies.md). Par ailleurs, elle empêche le service de communiquer avec les contrôleurs de domaine qui ne se trouvent pas dans le site Sites et services Active Directory spécifié. 

Si vous utilisez ADDS, vous pouvez obtenir le nom de votre site auprès du groupe d’administration de votre organisation qui est en charge d’Active Directory Domain Services. L’exemple ci-dessous illustre le plug-in Sites et services Active Directory dans lequel figure le nom du site : 

![Sites et services Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-sites-services.png)

Quand vous configurez une connexion AD pour Azure NetApp Files, vous devez spécifier le nom du site compris dans l’étendue dans le champ **Nom du site AD**.

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services 

Pour obtenir des recommandations et des informations sur la configuration d’Azure Active Directory Domain Services (AADDS), consultez la [documentation Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/).

Des considérations supplémentaires liées à AADDS s’appliquent à Azure NetApp Files : 

* Vérifiez que le réseau virtuel ou le sous-réseau dans lequel AADDS est déployé se trouve dans la même région Azure que le déploiement Azure NetApp Files.
* Si vous utilisez un autre réseau virtuel dans la région où Azure NetApp Files est déployé, vous devez créer un appairage entre les deux réseaux virtuels.
* Azure NetApp Files prend en charge les types `user` et `resource forest`.
* Pour le type de synchronisation, vous pouvez sélectionner `All` ou `Scoped`.   
    Si vous sélectionnez `Scoped`, vérifiez que le groupe Azure AD sélectionné pour accéder aux partages SMB convient.  Si vous n’en êtes pas certain, vous pouvez utiliser le type de synchronisation `All`.
* L’utilisation de la référence SKU Entreprise ou Premium est obligatoire. La référence SKU Standard n’est pas prise en charge.

Quand vous créez une connexion Active Directory, notez les détails suivants concernant AADDS :

* Les informations **DNS principal**, **DNS secondaire** et **Nom de domaine AD DNS** se trouvent dans le menu AADDS.  
Pour les serveurs DNS, deux adresses IP sont utilisées pour configurer la connexion Active Directory. 
* Le **chemin de l’unité d’organisation** est `OU=AADDC Computers`.  
Ce paramètre est configuré dans **Active Directory Connections** (Connexions Active Directory) en dessous de **NetApp Account** (Compte NetApp) :

  ![Chemin de l’unité d’organisation](../media/azure-netapp-files/azure-netapp-files-org-unit-path.png)

* Les informations d’identification de **Username** (Nom d’utilisateur) peuvent être celles de n’importe quel utilisateur membre du groupe Azure AD **Azure AD DC Administrators** (Administrateurs Azure AD DC).


## <a name="create-an-active-directory-connection"></a>Créer une connexion Active Directory

1. À partir de votre compte NetApp, cliquez sur **Connexions Active Directory**, puis sur **Rejoindre**.  

    ![Connexions Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. Dans la fenêtre Joindre Active Directory, entrez les informations suivantes, en fonction des services de domaine que vous voulez utiliser :  

    Pour obtenir des informations propres aux services de domaine que vous utilisez, consultez [Déterminer quels services de domaine utiliser](#decide-which-domain-services-to-use). 

    * **DNS principal**  
        Il s’agit du serveur DNS requis pour la jonction de domaine Active Directory et les opérations d’authentification SMB. 
    * **DNS secondaire**   
        Serveur DNS secondaire garantissant la redondance des services de noms. 
    * **Nom de domaine DNS AD**  
        Nom de domaine des Active Directory Domain Services que vous souhaitez rejoindre.
    * **Nom de site Active Directory**  
        Il s’agit du nom du site auquel se limitera la détection du contrôleur de domaine.
    * **Préfixe de serveur SMB (compte d’ordinateur)**  
        Préfixe de nom pour le compte d’ordinateur dans Active Directory que Azure NetApp Files utilise pour la création de nouveaux comptes.

        Par exemple, si la convention de nommage utilisée par votre organisation pour les serveurs de fichiers est NAS-01, NAS-02..., NAS-045, entrez « NAS » comme préfixe. 

        Le service crée des comptes d’ordinateurs supplémentaires dans Active Directory en fonction des besoins.

        > [!IMPORTANT] 
        > Le fait de renommer le préfixe de serveur SMB après la création de la connexion Active Directory entraîne une interruption. Vous devrez remonter les partages SMB existants après avoir renommé le préfixe de serveur SMB.

    * **Chemin d’accès de l’unité d’organisation**  
        Chemin d’accès de l’unité d’organisation (UO) où les comptes de machine du serveur SMB sont créés. Autrement dit, OU=second level, OU=first level. 

        Si vous utilisez Azure NetApp Files avec Azure Active Directory Domain Services, le chemin d’accès de l’unité d’organisation est `OU=AADDC Computers` lorsque vous configurez Active Directory pour votre compte NetApp.

     * **Utilisateurs de stratégie de sauvegarde**  
        Vous pouvez inclure des comptes supplémentaires qui requièrent des privilèges élevés sur le compte d’ordinateur créé pour une utilisation avec Azure NetApp Files. Les comptes spécifiés seront autorisés à modifier les autorisations NTFS au niveau du fichier ou du dossier. Par exemple, vous pouvez spécifier un compte de service non privilégié utilisé pour la migration des données vers un partage de fichiers SMB dans Azure NetApp Files.  

        La fonctionnalité **Utilisateurs de stratégie de sauvegarde** est actuellement en préversion. Si vous utilisez cette fonctionnalité pour la première fois, inscrivez-la avant de l’utiliser : 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFBackupOperator
        ```

        Vérifiez l’état d’inscription de la fonctionnalité : 

        > [!NOTE]
        > **RegistrationState** peut être à l’état `Registering` pendant plusieurs minutes, et jusqu’à 60 minutes, avant de passer à l’état `Registered`. Avant de continuer, attendez que l’état soit **Inscrit**.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFBackupOperator
        ```
        
        Vous pouvez également utiliser les commandes Azure CLI [`az feature register`](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-register) et [`az feature show`](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-show) pour inscrire la fonctionnalité et afficher l’état de l’inscription. 

    * Informations d’identification, y compris votre **nom d’utilisateur** et **mot de passe**

    ![Rejoindre Active Directory](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

3. Cliquez sur **Joindre**.  

    La connexion Active Directory créée s’affiche.

    ![Connexions Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

## <a name="add-an-smb-volume"></a>Ajouter un volume SMB

1. Cliquez sur le panneau **Volumes** à partir du panneau Pools de capacités. 

    ![Accédez à Volumes](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. Cliquez sur **+ Ajouter un volume** pour créer un volume.  
    La fenêtre Créer un volume s’affiche.

3. Dans la fenêtre Créer un volume, cliquez sur **Créer** et renseignez les champs suivants sous l’onglet De base :   
    * **Nom du volume**      
        Spécifiez le nom du volume que vous créez.   

        Un nom de volume doit être unique au sein de chaque pool de capacité. Il doit comprendre au moins trois caractères. Vous pouvez utiliser tous les caractères alphanumériques.   

        Vous ne pouvez pas utiliser `default` comme nom de volume.

    * **Pool de capacités**  
        Spécifiez le pool de capacité dans lequel vous souhaitez que le volume soit créé.

    * **Quota**  
        Spécifiez la quantité de stockage logique allouée au volume.  

        Le champ **Quota disponible** indique la quantité d’espace inutilisé dans le pool de capacités choisi, que vous pouvez utiliser pour créer un volume. La taille du nouveau volume ne doit pas dépasser le quota disponible.  

    * **Réseau virtuel**  
        Spécifiez le réseau virtuel Azure (VNet) à partir duquel vous voulez accéder au volume.  

        Le réseau virtuel que vous spécifiez doit avoir un sous-réseau délégué à Azure NetApp Files. Le service Azure NetApp Files est accessible seulement à partir du même réseau virtuel ou d’un réseau virtuel qui figure dans la même région que le volume via le peering de réseau virtuel. Vous pouvez également accéder au volume à partir de votre réseau local via Express Route.   

    * **Sous-réseau**  
        Spécifiez le sous-réseau que vous souhaitez utiliser pour le volume.  
        Le sous-réseau que vous spécifiez doit être délégué à Azure NetApp Files. 
        
        Si vous n’avez pas délégué un sous-réseau, vous pouvez cliquer sur **Créer** dans la page Créer un volume. Ensuite, dans la page Créer un sous-réseau, fournissez les informations sur le sous-réseau, puis sélectionnez **Microsoft.NetApp/volumes** pour déléguer le sous-réseau à Azure NetApp Files. Dans chaque réseau virtuel, un seul sous-réseau peut être délégué à Azure NetApp Files.   
 
        ![Créer un volume](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Créer un sous-réseau](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * Si vous souhaitez appliquer une stratégie d’instantané existante au volume, cliquez sur **Afficher la section avancée** pour la développer, puis sélectionnez une stratégie d’instantané dans le menu déroulant. 

        Pour plus d’informations sur la création d’une stratégie d’instantané, consultez [Gérer les stratégies d’instantané](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies).

        ![Afficher la sélection avancée](../media/azure-netapp-files/volume-create-advanced-selection.png)

4. Cliquez sur **Protocole** et renseignez les informations suivantes :  
    * Sélectionnez **SMB** comme type de protocole pour le volume. 
    * Sélectionnez votre connexion **Active Directory** dans la liste déroulante.
    * Spécifiez le nom du volume partagé dans **Nom de partage**.

    ![Spécifier le protocole SMB](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. Cliquez sur **Vérifier et créer** pour passer en revue les informations du volume.  Cliquez ensuite sur **Créer** pour créer le volume SMB.

    Le volume créé s’affiche dans la page Volumes. 
 
    Un volume hérite de l’abonnement, du groupe de ressources et des attributs d’emplacement de son pool de capacité. Vous pouvez suivre l’état du déploiement du volume dans le volet des notifications.

## <a name="control-access-to-an-smb-volume"></a>Contrôler l’accès à un volume SMB  

L’accès à un volume SMB est géré par le biais d’autorisations.  

### <a name="share-permissions"></a>Autorisations de partage  

Par défaut, un nouveau volume dispose des autorisations de partage **Tout le monde / Contrôle total**. Les membres du groupe Admins du domaine peuvent modifier les autorisations de partage à l’aide de Gestion de l’ordinateur sur le compte d’ordinateur utilisé pour le volume Azure NetApp Files.

![Chemin de montage SMB](../media/azure-netapp-files/smb-mount-path.png) 
![Définir des autorisations de partage](../media/azure-netapp-files/set-share-permissions.png) 

### <a name="ntfs-file-and-folder-permissions"></a>Autorisations de fichier et de dossier NTFS  

Vous pouvez définir des autorisations pour un fichier ou un dossier à l’aide de l’onglet **Sécurité** des propriétés de l’objet dans le client SMB Windows.
 
![Définit des autorisations de fichier et de dossier](../media/azure-netapp-files/set-file-folder-permissions.png) 

## <a name="next-steps"></a>Étapes suivantes  

* [Monter ou démonter un volume pour des machines virtuelles Windows ou Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Limites des ressources pour Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Questions fréquentes sur SMB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs)
* [En savoir plus sur l’intégration d’un réseau virtuel pour les services Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
* [Installation d’une nouvelle forêt Active Directory à l’aide d’Azure CLI](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)
