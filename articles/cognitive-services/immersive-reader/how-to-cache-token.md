---
title: Mettre en cache le jeton d’authentification
titleSuffix: Azure Cognitive Services
description: Cet article vous montre comment mettre en cache le jeton d’authentification.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metan
ms.custom: devx-track-javascript, devx-track-csharp
ms.openlocfilehash: 6850012272251c910362b61363dacd9090976680
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88918415"
---
# <a name="how-to-cache-the-authentication-token"></a>Comment mettre en cache le jeton d’authentification

Cet article montre comment mettre en cache le jeton d’authentification afin d’améliorer les performances de votre application.

## <a name="using-aspnet"></a>Utilisation d’ASP.NET

Importez le package NuGet **Microsoft.IdentityModel.Clients.ActiveDirectory** qui est utilisé pour acquérir un jeton. Ensuite, utilisez le code suivant pour acquérir un `AuthenticationResult` en utilisant les valeurs d’authentification que vous avez obtenues quand vous avez [créé la ressource Lecteur immersif](./how-to-create-immersive-reader.md).

```csharp
private async Task<AuthenticationResult> GetTokenAsync()
{
    AuthenticationContext authContext = new AuthenticationContext($"https://login.windows.net/{TENANT_ID}");
    ClientCredential clientCredential = new ClientCredential(CLIENT_ID, CLIENT_SECRET);
    AuthenticationResult authResult = await authContext.AcquireTokenAsync("https://cognitiveservices.azure.com/", clientCredential);
    return authResult;
}
```

L’objet `AuthenticationResult` a une propriété `AccessToken` qui est le jeton que vous allez utiliser lors du lancement du Lecteur immersif à l’aide du kit SDK. Il a également une propriété `ExpiresOn` qui indique quand expire le jeton. Avant de lancer le Lecteur immersif, vous pouvez vérifier si le jeton a expiré et en acquérir un nouveau uniquement s’il a effectivement expiré.

## <a name="using-nodejs"></a>Utilisation de Node.JS

Ajoutez le package npm [**request**](https://www.npmjs.com/package/request) à votre projet. Utilisez le code suivant pour acquérir un jeton en utilisant les valeurs d’authentification que vous avez obtenues quand vous avez [créé la ressource Lecteur immersif](./how-to-create-immersive-reader.md).

```javascript
router.get('/token', function(req, res) {
    request.post(
        {
            headers: { 'content-type': 'application/x-www-form-urlencoded' },
            url: `https://login.windows.net/${TENANT_ID}/oauth2/token`,
            form: {
                grant_type: 'client_credentials',
                client_id: CLIENT_ID,
                client_secret: CLIENT_SECRET,
                resource: 'https://cognitiveservices.azure.com/'
            }
        },
        function(err, resp, json) {
            const result = JSON.parse(json);
            return res.send({
                access_token: result.access_token,
                expires_on: result.expires_on
            });
        }
    );
});
```

La propriété `expires_on` correspond à la date et à l’heure d’expiration du jeton, exprimées en nombre de secondes depuis le 1er janvier 1970 UTC. Utilisez cette valeur pour déterminer si votre jeton a expiré avant d’essayer d’en acquérir un nouveau.

```javascript
async function getToken() {
    if (Date.now() / 1000 > CREDENTIALS.expires_on) {
        CREDENTIALS = await refreshCredentials();
    }
    return CREDENTIALS.access_token;
}
```

## <a name="next-steps"></a>Étapes suivantes

* Explorer le [Guide de référence du SDK du Lecteur immersif](./reference.md)