---
title: Fichier include
description: Fichier include
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.custom: include file
ms.date: 02/14/2020
ms.subservice: language-understanding
ms.author: diberry
ms.openlocfilehash: 956aa308bf1cb3736c491031239661ec6b295ddb
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77279540"
---
L’application cliente doit savoir si un énoncé est inutile ou inapproprié pour l’application. L’intention **None** est ajoutée à chaque application dans le cadre du processus de création pour déterminer si un énoncé ne doit pas être traité par l’application cliente.

Si LUIS retourne l’intention **None** pour un énoncé, votre application cliente peut demander si l’utilisateur souhaite mettre fin à la conversation ou donner d’autres instructions pour poursuivre la conversation.

Si vous ne complétez pas l’intention **Aucune**, un énoncé qui devrait être prédit en dehors du domaine du sujet le sera dans l’une des intentions de domaine du sujet existant. En conséquence, l’application cliente, par exemple un bot conversationnel, effectue des opérations incorrectes en s’appuyant sur une prédiction incorrecte.

1. Dans le panneau gauche, sélectionnez **Intents** (Intentions).

1. Sélectionnez l’intention **None**. Ajoutez trois énoncés que votre utilisateur est susceptible d’entrer, mais qui ne sont pas pertinents pour votre application de commande de pizzas :

    |Exemples d’énoncés `None`|
    |--|
    |`Barking dogs are annoying`|
    |`Penguins in the ocean`|

    Ces exemples ne doivent pas utiliser des mots auxquels vous vous attendez dans le domaine couvert, par exemple `pizza`, `cheese``crust`, `pickup` `deliver`.