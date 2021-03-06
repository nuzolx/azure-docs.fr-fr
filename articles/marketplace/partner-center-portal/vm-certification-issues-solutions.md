---
title: Certification des machines virtuelles - problèmes et solutions
description: Cet article explique les messages d’erreur courants liés aux images de machine virtuelle. Il aborde également les solutions associées.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: troubleshooting
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 06/16/2020
ms.openlocfilehash: 5b6d1ee41434d8aebac81d38ced9cadd93e51ba8
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89181440"
---
# <a name="issues-and-solutions-during-virtual-machine-certification"></a>Certification des machines virtuelles : problèmes et solutions 

Lorsque vous publiez l'image d'une machine virtuelle sur la Place de marché Azure, l'équipe Azure la valide pour garantir sa capacité de démarrage, sa sécurité et sa compatibilité avec Azure. Si l'un des tests haute qualité échoue, la publication échouera et vous recevrez un message d'erreur décrivant le problème.

Cet article présente les messages d'erreur les plus couramment rencontrés lors de la publication d'images de machine virtuelle, ainsi que les solutions associées.

> [!NOTE]
> Si vous avez des questions ou des commentaires, contactez le [Support de l’Espace partenaires](https://partner.microsoft.com/support/v2/?stage=1).

## <a name="approved-base-image"></a>Image de base approuvée

Lorsque vous envoyez une demande de nouvelle publication de votre image avec des mises à jour, le cas de test de vérification du numéro de référence peut échouer. Votre image ne sera alors pas approuvée.

Cette erreur se produit lorsque vous utilisez une image de base qui appartient à un autre éditeur et que vous avez procédé à une mise à jour de l'image. Dans ce cas, vous n’êtes pas autorisé à publier votre image.

Pour résoudre ce problème, récupérez l'image à partir de la Place de marché Azure et apportez-y des modifications. Pour plus d’informations, consultez les articles suivants :

- [Images Linux](../../virtual-machines/linux/endorsed-distros.md?toc=/azure/virtual-machines/linux/toc.json)
- [Images Windows](create-azure-vm-technical-asset.md#create-a-vm-image-using-an-approved-base)

## <a name="vm-extension-failure"></a>Échec de l’extension de machine virtuelle

Déterminez si votre image prend en charge les extensions de machine virtuelle.

Pour activer les extensions de machine virtuelle, procédez comme suit :

1. Sélectionnez votre machine virtuelle Linux.
1. Accédez à **Paramètres de diagnostic**.
1. Activez les matrices de base en mettant à jour le **Compte de stockage**.
1. Sélectionnez **Enregistrer**.

   ![Activer la supervision d’invités](./media/vm-certification-issues-solutions-1.png)

Pour vérifier que les extensions de machine virtuelle sont correctement activées, procédez comme suit :

1. Dans la machine virtuelle, sélectionnez l'onglet **Extensions de machine virtuelle**, puis vérifiez l'état de l'**Extension de diagnostic Linux**.
    * Si l'état est *Approvisionnement réussi*, le cas de test Extensions est réussi.  
    * Si l'état est *Échec de l'approvisionnement*, le cas de test Extensions est un échec et vous devez définir l'indicateur Renforcé.

      ![Capture d'écran montrant un approvisionnement réussi](./media/vm-certification-issues-solutions-2.png)

      Si l'extension de machine virtuelle échoue, accédez à [Utiliser l'Extension de diagnostic Linux pour superviser les métriques et les journaux](../../virtual-machines/extensions/diagnostics-linux.md) afin de l'activer. Si vous ne souhaitez pas que l'extension de machine virtuelle soit activée, contactez l'équipe du support technique et demandez-lui de la désactiver.

## <a name="vm-provisioning-issue"></a>Problème d'approvisionnement de machine virtuelle

Assurez-vous d'avoir rigoureusement suivi le processus d'approvisionnement de la machine virtuelle avant de soumettre votre offre. Pour afficher le format JSON à des fins d'approvisionnement de la machine virtuelle, consultez [Certification d'image de machine virtuelle Azure](azure-vm-image-certification.md).

Les problèmes de provisionnement peuvent inclure les scénarios d’échec suivants :

|Scénario|Error|Motif|Solution|
|---|---|---|---|
|1|Disque dur virtuel (VHD) non valide|Si la valeur du cookie spécifiée dans le pied de page du disque dur virtuel est incorrecte, le disque dur virtuel sera considéré comme non valide.|Recréez l'image et envoyez la demande.|
|2|Type d'objet blob non valide|L'approvisionnement de la machine virtuelle a échoué car le bloc utilisé est un type d'objet blob et non un type de page.|Recréez l'image et envoyez la demande.|
|3|Expiration du délai de provisionnement ou généralisation incorrecte|Il y a un problème avec la généralisation de la machine virtuelle.|Recréez l'image avec la généralisation et envoyez la demande.|

> [!NOTE]
> Pour plus d'informations sur la généralisation d'une machine virtuelle, consultez :
> - [Documentation Linux](create-azure-vm-technical-asset.md#generalize-the-image)
> - [Documentation Windows](../../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep)

## <a name="software-compliance-for-windows"></a>Conformité logicielle pour Windows

Si votre demande d'image Windows est rejetée en raison d'un problème de conformité logicielle, vous avez peut-être créé une image Windows sur laquelle une instance de SQL Server est installée au lieu de prendre l'image de base de la version SQL appropriée sur la Place de marché Azure.

Ne créez pas d'image Windows dotée de SQL Server. Utilisez plutôt les images de base SQL approuvées (Entreprise/Standard/Web) à partir de la Place de marché Azure.

Si vous essayez d'installer Visual Studio ou un produit sous licence Office, contactez l'équipe du support technique pour obtenir une approbation préalable.

Pour plus d'informations sur la sélection d'une base approuvée, consultez [Créer vos ressources techniques de machine virtuelle Azure](create-azure-vm-technical-asset.md#create-a-vm-image-using-an-approved-base).

## <a name="tool-kit-test-case-execution-failed"></a>Échec de l’exécution du cas de test du kit de ressources 

Le kit de ressources de certification Microsoft peut vous aider à exécuter des cas de test et à vérifier que votre disque dur virtuel ou votre image est compatible avec l'environnement Azure.

Téléchargez le [kit de ressources de certification Microsoft](azure-vm-image-certification.md).

## <a name="linux-test-cases"></a>Cas de test Linux

Le tableau suivant répertorie les cas de test Linux que le kit de ressources exécutera. La validation de test est indiquée dans la description.

|Scénario|Cas de test|Description|
|---|---|---|
|1|Historique Bash|Les fichiers d'historique Bash doivent être effacés avant la création de l'image de machine virtuelle.|
|2|Version de l'agent Linux|L'agent Linux Azure 2.2.41 ou version ultérieure doit être installé.|
|3|Paramètres de noyau requis|Vérifie que les paramètres de noyau suivants sont définis : <br>console=ttyS0<br>earlyprintk=ttyS0<br>rootdelay=300|
|4|Partition d'échange sur le disque du système d'exploitation|Vérifie qu’aucune partition d’échange n’est créée sur le disque du système d’exploitation.|
|5|Partition racine sur le disque du système d'exploitation|Créez une partition racine unique pour le disque de système d’exploitation.|
|6|Version d'OpenSSL|OpenSSL version 0.9.8 ou ultérieure doit être installé.|
|7|Version Python|Python version 2.6 ou ultérieure est fortement recommandé.|
|8|Intervalle d’activité du client|Affectez la valeur 180 à ClientAliveInterval. Pour les besoins de l'application, vous pouvez affecter une valeur comprise entre 30 et 235. Si vous activez le protocole SSH pour vos utilisateurs finaux, cette valeur doit être définie comme expliqué.|
|9|Architecture du système d’exploitation|Seuls les systèmes d’exploitation 64 bits sont pris en charge.|
|10|Mise à jour automatique|Indique si la mise à jour automatique de l’agent Linux est activée.|

### <a name="common-errors-found-while-executing-previous-test-cases"></a>Erreurs courantes détectées lors de l'exécution de cas de test précédents

Le tableau suivant répertorie les erreurs courantes qui ont été détectées lors de l'exécution de cas de test précédents :
 
|Scénario|Cas de test|Error|Solution|
|---|---|---|---|
|1|Cas de test Version de l'agent Linux|La version minimale de l’agent Linux est 2.2.41 ou ultérieure. Cette exigence est obligatoire depuis le 1er mai 2020.|Veuillez mettre à jour la version de l’agent Linux et celle-ci doit être 2.2.41 ou une version ultérieure. Pour plus d’informations, consultez la [page de mise à jour de la version de l’agent Linux](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).|
|2|Cas de test Historique Bash|Une erreur s'affiche si la taille de l'historique Bash de l'image que vous avez envoyée est supérieure à 1 kilo-octet (Ko). La taille est limitée à 1 Ko pour garantir qu’aucune information potentiellement sensible n’est capturée dans votre fichier d’historique Bash.|Pour résoudre ce problème, montez le disque dur virtuel sur n'importe quelle autre machine virtuelle opérationnelle et apportez les modifications de votre choix (par exemple, supprimez les fichiers d'historique *.bash*) pour que la taille soit inférieure ou égale à 1 Ko.|
|3|Cas de test Paramètres de noyau requis|Vous recevez cette erreur quand la valeur de **console** n’est pas **ttyS0**. Vérifiez en exécutant la commande suivante :<br>`cat /proc/cmdline`|Affectez la valeur **ttyS0** à **console** et renvoyez la demande.|
|4|Cas de test Intervalle d'activité du client|Si le résultat du kit de ressources est un échec pour ce cas de test, cela signifie que la valeur de **ClientAliveInterval** est incorrecte.|Affectez une valeur inférieure ou égale à 235 à **ClientAliveInterval**, puis renvoyez la demande.|

### <a name="windows-test-cases"></a>Cas de test Windows

Le tableau suivant répertorie les cas de test Windows que le kit de ressources exécutera, et fournit une description de la validation du test :

|Scénario |Scénarios de test|Description|
|---|---|---|---|
|1|Architecture du système d’exploitation|Azure prend uniquement en charge les systèmes d'exploitation 64 bits.|
|2|Dépendance de compte d’utilisateur|L'exécution de l'application ne doit pas dépendre du compte d'administrateur.|
|3|Cluster de basculement|La fonctionnalité de clustering de basculement Windows Server n'est pas encore prise en charge. L’application ne doit pas être dépendante de cette fonctionnalité.|
|4|IPV6|Le protocole IPv6 n’est pas encore pris en charge dans l’environnement Azure. L’application ne doit pas être dépendante de cette fonctionnalité.|
|5|DHCP|Le rôle serveur DHCP (Dynamic Host Configuration Protocol) n’est pas encore pris en charge. L’application ne doit pas être dépendante de cette fonctionnalité.|
|6|Hyper-V|Le rôle serveur Hyper-V n’est pas encore pris en charge. L’application ne doit pas être dépendante de cette fonctionnalité.|
|7|Accès à distance|Le rôle serveur Accès à distance (Accès direct) n’est pas encore pris en charge. L’application ne doit pas être dépendante de cette fonctionnalité.|
|8|Rights Management Services|Rights Management Services. Le rôle serveur n’est pas encore pris en charge. L’application ne doit pas être dépendante de cette fonctionnalité.|
|9|Services de déploiement Windows|Services de déploiement Windows. Le rôle serveur n’est pas encore pris en charge. L’application ne doit pas être dépendante de cette fonctionnalité.|
|10|Chiffrement de lecteur BitLocker|Le chiffrement de lecteur BitLocker n'est pas pris en charge sur le disque dur du système d'exploitation, mais peut être utilisé sur les disques de données.|
|11|Serveur de nom de stockage Internet|La fonctionnalité Serveur de nom de stockage Internet n'est pas encore prise en charge. L’application ne doit pas être dépendante de cette fonctionnalité.|
|12|MPIO (Multipath I/O)|MPIO (Multipath I/O). Cette fonctionnalité de serveur n’est pas encore prise en charge. L’application ne doit pas être dépendante de cette fonctionnalité.|
|13|Équilibrage de charge réseau|Équilibrage de charge réseau. Cette fonctionnalité de serveur n’est pas encore prise en charge. L’application ne doit pas être dépendante de cette fonctionnalité.|
|14|Protocole PNRP|Protocole PNRP. Cette fonctionnalité de serveur n’est pas encore prise en charge. L’application ne doit pas être dépendante de cette fonctionnalité.|
|15|Services SNMP|La fonctionnalité Services SNMP n'est pas encore prise en charge. L’application ne doit pas être dépendante de cette fonctionnalité.|
|16|Service WINS (Windows Internet Name Service)|Service WINS (Windows Internet Name Service). Cette fonctionnalité de serveur n’est pas encore prise en charge. L’application ne doit pas être dépendante de cette fonctionnalité.|
|17|Service de réseau local sans fil|Service de réseau local sans fil. Cette fonctionnalité de serveur n’est pas encore prise en charge. L’application ne doit pas être dépendante de cette fonctionnalité.|

Si vous observez des échecs avec les cas de test précédents, consultez la colonne **Description** du tableau pour connaître la solution. Si vous avez besoin de davantage d'informations, contactez l'équipe du support technique. 

## <a name="data-disk-size-verification"></a>Vérification de la taille du disque de données

Si la taille d'une demande envoyée avec le disque de données est supérieure à 1 023 gigaoctets (Go), la demande n'est pas approuvée. Cette règle s'applique à la fois à Linux et à Windows.

Renvoyez la demande avec une taille inférieure ou égale à 1023 Go.

## <a name="os-disk-size-validation"></a>Validation de la taille du disque du système d’exploitation

Pour plus d’informations sur les limitations relatives à la taille du disque du système d’exploitation, consultez les règles suivantes. Quand vous envoyez une demande, vérifiez que la taille du disque du système d’exploitation est comprise dans les limites de Linux ou de Windows.

|Système d''exploitation|Taille de disque dur virtuel recommandée|
|---|---|
|Linux|30 à 1023 Go|
|Windows|30 à 250 Go|

Comme les machines virtuelles autorisent l’accès au système d’exploitation sous-jacent, assurez-vous que la taille du disque dur virtuel est suffisamment élevée pour le disque dur virtuel. Les disques n'étant pas extensibles sans temps d'arrêt, utilisez une taille de disque comprise entre 30 et 50 Go.

|Taille de disque dur virtuel|Taille réelle occupée|Solution|
|---|---|---|
|> 500 tébioctets (Tio).|n/a|Contactez l'équipe du support technique pour obtenir une approbation d'exception.|
|250 à 500 Tio|> 200 gibioctets (Gio) de différence par rapport à la taille de l'objet blob|Contactez l'équipe du support technique pour obtenir une approbation d'exception.|

> [!NOTE]
> Les disques de plus grande taille génèrent des coûts plus élevés et entraînent un retard pendant le processus d'installation et de réplication. En raison de ce retard et de ce coût, l'équipe du support technique est susceptible de demander la justification de l'approbation d'exception.

## <a name="wannacry-patch-verification-test-for-windows"></a>Test de vérification de correctif WannaCry pour Windows

Pour prévenir toute attaque liée au virus WannaCry, veillez à ce que toutes les demandes d'image Windows soient mises à jour avec le correctif le plus récent.

Pour vérifier la version corrigée de Windows Server et obtenir des informations détaillées sur le système d'exploitation et la version minimale qu'il prendra en charge, consultez le tableau suivant : 

La version du fichier image peut être vérifiée à partir de `C:\windows\system32\drivers\srv.sys` ou `srv2.sys`.

> [!NOTE]
> Aucune spécification de version obligatoire ne s'applique à Windows Server 2019.

|Système d''exploitation|Version|
|---|---|
|Windows Server 2008 R2|6.1.7601.23689|
|Windows Server 2012|6.2.9200.22099|
|Windows Server 2012 R2|6.3.9600.18604|
|Windows Server 2016|10.0.14393.953|
|Windows Server 2019|N/D|

## <a name="sack-vulnerability-patch-verification"></a>Vérification du correctif de vulnérabilité SACK

Lorsque vous envoyez une image Linux, votre demande peut être rejetée en raison de problèmes liés à la version du noyau.

Mettez à jour le noyau avec une version approuvée et renvoyez la demande. Vous trouverez la version du noyau approuvée dans le tableau suivant. Le numéro de version doit être supérieur ou égal à celui indiqué ici.

Si votre image n'est pas installée avec l'une des versions de noyau suivantes, mettez-la à jour avec les correctifs appropriés. Demandez l'approbation nécessaire à l'équipe du support technique une fois que l'image a été mise à jour avec les correctifs requis suivants :

- CVE-2019-11477 
- CVE-2019-11478 
- CVE-2019-11479

|Famille de système d’exploitation|Version|Noyau|
|---|---|---|
|Ubuntu|14.04 LTS|4.4.0-151| 
||14.04 LTS|4.15.0-1049-*-azure|
||LTS 16.04|4.15.0-1049|
||18.04 LTS|4.18.0-1023|
||18.04 LTS|5.0.0-1025|
||18.10|4.18.0-1023|
||19.04|5.0.0-1010|
||19.04|5.3.0-1004|
|RHEL et CentOS|6.10|2.6.32-754.15.3|
||7.2|3.10.0-327.79.2|
||7.3|3.10.0-514.66.2|
||7.4|3.10.0-693.50.3|
||7.5|3.10.0-862.34.2|
||7.6|3.10.0-957.21.3|
||7,7|3.10.0-1062.1.1|
||8.0|4.18.0-80.4.2|
||8.1|4.18.0-147|
||"7-RAW" (7.6)||
||"7-LVM" (7.6)|3.10.0-957.21.3|
||RHEL-SAP 7.4|TBD|
||RHEL-SAP 7.5|TBD|
|SLES|SLES11SP4 (y compris SAP)|3.0.101-108.95.2|
||SLES12SP1 pour SAP|3.12.74-60.64.115.1|
||SLES12SP2 pour SAP|4.4.121-92.114.1|
||SLES12SP3|4.4180-4.31.1 (noyau-Azure)|
||SLES12SP3 pour SAP|4.4.180-94.97.1|
||SLES12SP4|4.12.14-6.15.2 (noyau-Azure)|
||SLES12SP4 pour SAP|4.12.14-95.19.1|
||SLES15|4.12.14-5.30.1 (noyau-Azure)|
||SLES15 pour SAP|4.12.14-5.30.1 (noyau-Azure)|
||SLES15SP1|4.12.14-5.30.1 (noyau-Azure)|
|Oracle|6.10|UEK2 2.6.39-400.312.2<br>UEK3 3.8.13-118.35.2<br>RHCK 2.6.32-754.15.3 
||7.0-7.5|UEK3 3.8.13-118.35.2<br>UEK4 4.1.12-124.28.3<br>RHCK suit RHEL ci-dessus|
||7.6|RHCK 3.10.0-957.21.3<br>UEK5 4.14.35-1902.2.0|
|CoreOS Stable 2079.6.0|4.19.43*|
||Bêta 2135.3.1|4.19.50*|
||Alpha 2163.2.1|4.19.50*|
|Debian|jessie (sécurité)|3.16.68-2|
||jessie (rétroportage)|4.9.168-1+deb9u3|
||stretch (sécurité)|4.9.168-1+deb9u3|
||Debian GNU/Linux 10 (buster)|Debian 6.3.0-18+deb9u1|
||buster, sid (rétroportage stretch)|4.19.37-5|

## <a name="image-size-should-be-in-multiples-of-megabytes"></a>La taille de l’image doit être exprimée en multiples de mégaoctets.

La taille virtuelle de tous les disques durs virtuels exécutés sur Azure doit être alignée sur des multiples de 1 mégaoctet (Mo). Si votre disque dur virtuel n’est pas conforme à la taille virtuelle recommandée, votre demande peut être rejetée.

Suivez les instructions lors de la conversion d'un disque brut en disque dur virtuel, et vérifiez que la taille du disque brut correspond à un multiple de 1 Mo. Pour plus d'informations, consultez [Informations concernant les distributions non approuvées](../../virtual-machines/linux/create-upload-generic.md).

## <a name="vm-access-denied"></a>Accès refusé à la machine virtuelle

Si vous rencontrez des problèmes de refus d'accès lors de l'exécution des cas de test sur la machine virtuelle, cela peut être dû au fait que les privilèges sont insuffisants pour exécuter les cas de test.

Vérifiez si l'accès approprié est activé pour le compte sur lequel les cas d'auto-test sont exécutés. Si l'accès n'est pas activé, activez-le pour exécuter les cas de test. Si vous ne souhaitez pas activer l'accès, vous pouvez partager les résultats de l'auto-test avec l'équipe du support technique.

## <a name="download-failure"></a>Échec du téléchargement
    
Reportez-vous au tableau suivant pour tout problème survenant lorsque vous téléchargez l'image de machine virtuelle à l'aide d'une URL de signature d'accès partagé (SAP).

|Scénario|Error|Motif|Solution|
|---|---|---|---|
|1|Objet blob introuvable|Le disque dur virtuel a peut-être été supprimé ou déplacé de l'emplacement spécifié.|| 
|2|Objet blob en cours d'utilisation|Le disque dur virtuel est utilisé par un autre processus interne.|Le disque dur virtuel doit être dans un état Utilisé lorsque vous le téléchargez à l'aide d'une URL de signature d'accès partagé.|
|3|URL SAS non valide|L'URL de signature d'accès partagé associée au disque dur virtuel est incorrecte.|Récupérez l’URL SAS correcte.|
|4|Signature incorrecte|L'URL de signature d'accès partagé associée au disque dur virtuel est incorrecte.|Récupérez l’URL SAS correcte.|
|6|En-tête HTTP conditionnel|L'URL de signature d'accès partagé n'est pas valide.|Récupérez l’URL SAS correcte.|
|7|Nom de disque dur virtuel non valide|Recherchez la présence de caractères spéciaux, comme un signe de pourcentage (%) ou des guillemets ("), dans le nom du disque dur virtuel.|Renommez le fichier VHD en supprimant les caractères spéciaux.|

## <a name="first-1-mb-partition"></a>Première partition de 1 Mo

Lors de l'envoi du disque dur virtuel, vérifiez que la première partition de 1 Mo de celui-ci est vide. Si ce n’est pas le cas, votre demande échouera.

## <a name="default-credentials"></a>Informations d’identification par défaut

Vérifiez toujours que les informations d’identification par défaut ne sont pas envoyées avec le disque dur virtuel. L’ajout d’informations d’identification par défaut rend le VHD plus vulnérable aux menaces de sécurité. Au lieu de cela, créez vos propres informations d'identification lors de l'envoi du disque dur virtuel.
  
## <a name="datadisk-mapped-incorrectly"></a>DataDisk mappé de manière incorrecte

Quand une demande est envoyée avec plusieurs disques de données, mais que leur ordre n’est pas en séquence, cela est considéré comme un problème de mappage. Par exemple, s’il existe trois disques de données, l’ordre de numérotation doit être *0, 1, 2*. Tout autre ordre sera traité comme un problème de mappage.

Renvoyez la demande avec un séquencement approprié des disques de données.

## <a name="incorrect-os-mapping"></a>Mappage du système d’exploitation incorrect

Lorsqu'une image est créée, elle peut être mappée ou attribuée à une étiquette de système d'exploitation incorrecte. Par exemple, lorsque vous sélectionnez **Windows** comme partie du nom du système d'exploitation lors de la création de l'image, le disque du système d'exploitation doit uniquement être installé avec Windows. La même exigence s'applique à Linux.

## <a name="vm-not-generalized"></a>Machine virtuelle non généralisée

Si toutes les images issues de la Place de marché Azure doivent être réutilisées, le disque dur virtuel du système d'exploitation doit être généralisé.

* Pour **Linux**, le processus suivant généralise une machine virtuelle Linux et la redéploie sous la forme d'une machine virtuelle distincte.

  Dans la fenêtre SSH, entrez la commande suivante : `sudo waagent -deprovision+user`

* Pour **Windows**, vous pouvez généraliser les images Windows à l'aide de `sysreptool`.

Pour plus d'informations sur cet outil, consultez [Présentation de l'outil de préparation système (Sysprep)]( https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

## <a name="datadisk-errors"></a>Erreurs DataDisk

Pour remédier aux erreurs liées au disque de données, utilisez le tableau suivant :

|Error|Motif|Solution|
|---|---|---|
|`DataDisk- InvalidUrl:`|Cette erreur peut survenir lorsqu'un numéro non valide est spécifié pour le numéro d'unité logique au moment de l'envoi de l'offre.|Vérifiez que la séquence de numéros d'unités logiques du disque de données figure dans l'Espace partenaires.|
|`DataDisk- NotFound:`|Cette erreur peut survenir lorsqu'un disque de données ne se trouve pas à l'emplacement d'une URL de signature d'accès partagé spécifiée.|Vérifiez que le disque de données se trouve à l'emplacement de l'URL de signature d'accès partagé spécifiée dans la demande.|

## <a name="remote-access-issue"></a>Problème d'accès à distance

Si l'option RDP (Remote Desktop Protocol) n'est pas activée pour l'image Windows, vous recevrez cette erreur. 

Activez l'accès RDP pour les images Windows avant de les envoyer.

## <a name="bash-history-failed"></a>Échec de l’historique Bash

Une erreur s’affiche si la taille de l’historique Bash de l’image que vous avez envoyée est supérieure à 1 kilooctet (ko). La taille est limitée à 1 Ko pour garantir qu’aucune information potentiellement sensible n’est capturée dans votre fichier d’historique Bash.

Vous trouverez ci-dessous les étapes permettant de supprimer l’historique Bash.

Étape 1. Déployez la machine virtuelle et cliquez sur l’option « Run Command » sur Portail Azure.
![Run command on Azure portal](./media/vm-certification-issues-solutions-3.png)

Étape 2. Sélectionnez la première option « RunShellScript » et exécutez la commande ci-dessous.

Commande : « cat /dev/null > ~/.bash_history && history -c » ![Bash History command on Azure portal](./media/vm-certification-issues-solutions-4.png)

Étape 3. Une fois l’exécution de la commande terminée, redémarrez la machine virtuelle.

Étape 4. Généralisez la machine virtuelle, prenez le disque dur virtuel de l’image et arrêtez la machine virtuelle.

Étape 5.     Soumettez à nouveau l’image généralisée.

## <a name="requesting-exceptions-custom-templates-on-vm-images-for-selective-tests"></a>Demande d’exceptions (modèles personnalisés) sur les images de machine virtuelle pour des tests sélectifs

Les éditeurs peuvent demander des exceptions pour quelques tests effectués pendant la certification de la machine virtuelle. Des exceptions sont prévues dans des cas extrêmement rares où l’éditeur fournit des preuves à l’appui de la demande.
L’équipe de certification se réserve le droit de refuser ou d’approuver des exceptions à tout moment.

Dans les sections ci-dessous, nous parlerons des principaux scénarios dans lesquels des exceptions sont demandées et de la manière de demander une exception.

Scénarios pour une exception

Il existe trois scénarios/cas où les éditeurs demandent généralement ces exceptions. 

* **Exception pour un ou plusieurs cas de test :** les éditeurs peuvent s’adresser au [Support aux éditeurs de Place de marché](https://aka.ms/marketplacepublishersupport) pour demander des exceptions pour les cas tests. 

* **Machines virtuelles verrouillées/aucun accès racine :** peu d’éditeurs ont des scénarios dans lesquels les machines virtuelles doivent être verrouillées, car des logiciels tels que des pare-feu sont installés sur la machine virtuelle. 
       Dans ce cas, les éditeurs peuvent télécharger l’[outil de test certifié](https://aka.ms/AzureCertificationTestTool) ici et fournir le rapport au [Support aux éditeurs de Place de marché](https://aka.ms/marketplacepublishersupport).


* **Modèles personnalisés :** certains éditeurs publient des images de machine virtuelle qui nécessitent un modèle Resource Manager personnalisé pour déployer les machines virtuelles. Dans ce cas, les éditeurs sont invités à fournir les modèles personnalisés au [Support aux éditeurs de Place de marché](https://aka.ms/marketplacepublishersupport) afin que l’équipe de certification puisse les utiliser pour la validation. 

### <a name="information-to-provide-for-exception-scenarios"></a>Informations à fournir pour les scénarios d’exception

Les éditeurs doivent s’adresser au [Support aux éditeurs de Place de marché](https://aka.ms/marketplacepublishersupport) pour demander des exceptions pour le scénario ci-dessus en fournissant les informations supplémentaires suivantes :

   1.   ID de l’éditeur : ID de l’éditeur sur le portail Espace partenaires
   2.   ID/nom de l’offre : ID/nom de l’offre pour laquelle l’exception est demandée 
   3.   Réf. SKU/ID de plan : ID de plan/référence SKU de l’offre de machine virtuelle pour laquelle l’exception est demandée
   4.    Version : version de l’offre de machine virtuelle pour laquelle l’exception est demandée
   5.   Type d’exception : tests, machine virtuelle verrouillée, modèles personnalisés
   6.   Raison de la demande : raison de cette exception et informations sur les tests à exempter 
   7. Chronologie : date jusqu’à laquelle cette exception a été demandée 
   8.   Pièce jointe : joindre tout document de preuve d’importance. Pour les machines virtuelles verrouillées, joignez le rapport de test et, pour les modèles personnalisés, fournissez le modèle Resource Manager personnalisé en pièce jointe. Ne pas joindre les documents requis entraîne un refus de demande.


## <a name="next-steps"></a>Étapes suivantes

Si vous avez des questions ou des commentaires, contactez le [Support de l’Espace partenaires](https://partner.microsoft.com/support/v2/?stage=1).
