---
title: 'Démarrage rapide : Appeler votre point de terminaison Recherche personnalisée Bing avec Java | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Utilisez ce guide de démarrage rapide pour commencer à demander des résultats de recherche à partir de votre instance Recherche personnalisée Bing, en Java.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.custom: devx-track-java
ms.author: aahi
ms.openlocfilehash: 069f3b95d76d8321ce0b1bba4ae61f35d0f14a57
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87326781"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-java"></a>Démarrage rapide : Appeler votre point de terminaison Recherche personnalisée Bing avec Java

Utilisez ce guide de démarrage rapide pour découvrir comment demander des résultats de recherche à partir de votre instance Recherche personnalisée Bing. Bien que cette application soit écrite en Java, l’API Recherche personnalisée Bing est un service web RESTful compatible avec la plupart des langages de programmation. Le code source de cet exemple est disponible sur [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingCustomSearchv7.java).

## <a name="prerequisites"></a>Prérequis

- Une instance Recherche personnalisée Bing. Pour plus d’informations, consultez [Démarrage rapide : Créer votre première instance de Recherche personnalisée Bing](quick-start.md).

- La version la plus récente de [Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/index.html).

- La [bibliothèque Gson](https://github.com/google/gson).

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Créer et initialiser l’application

1. Créez un projet Java dans votre éditeur ou votre IDE favori, puis importez les bibliothèques suivantes :

    ```java
    import java.io.InputStream;
    import java.net.URL;
    import java.net.URLEncoder;
    import java.util.HashMap;
    import java.util.List;
    import java.util.Map;
    import java.util.Scanner;
    import javax.net.ssl.HttpsURLConnection;
    import com.google.gson.Gson;
    import com.google.gson.GsonBuilder;
    import com.google.gson.JsonObject;
    import com.google.gson.JsonParser;
    ```

2. Créez une classe nommée `CustomSrchJava`, puis créez des variables pour votre clé d’abonnement, le point de terminaison de recherche personnalisée et l’ID de configuration personnalisée de l’instance de recherche. Vous pouvez utiliser le point de terminaison global dans le code suivant, ou le point de terminaison de [sous-domaine personnalisé](../../cognitive-services/cognitive-services-custom-subdomains.md) affiché dans le portail Azure pour votre ressource.
    ```java
    public class CustomSrchJava {
        static String host = "https://api.cognitive.microsoft.com";
        static String path = "/bingcustomsearch/v7.0/search";
        static String subscriptionKey = "YOUR-SUBSCRIPTION-KEY"; 
        static String customConfigId = "YOUR-CUSTOM-CONFIG-ID";
        static String searchTerm = "Microsoft";  
    ...
    ```

3. Créez une autre classe nommée `SearchResults` qui recevra réponse de votre instance Recherche personnalisée Bing.

    ```java
    class SearchResults {
        HashMap<String, String> relevantHeaders;
        String jsonResponse;
        SearchResults(HashMap<String, String> headers, String json) {
            relevantHeaders = headers;
            jsonResponse = json;
        }
    }
    ```

4. Créez une fonction nommée `prettify()` pour mettre en forme la réponse JSON à partir de l’API Recherche personnalisée Bing.

    ```java
        // pretty-printer for JSON; uses GSON parser to parse and re-serialize
        public static String prettify(String json_text) {
            JsonParser parser = new JsonParser();
            JsonObject json = parser.parse(json_text).getAsJsonObject();
            Gson gson = new GsonBuilder().setPrettyPrinting().create();
            return gson.toJson(json);
        }
    ```

## <a name="send-and-receive-a-search-request"></a>Envoyer et recevoir une requête de recherche 

1. Créez une fonction nommée `SearchWeb()` qui envoie une requête et renvoie un objet `SearchResults`. Créez l’URL de la requête en combinant l’ID de votre configuration personnalisée, la requête et les informations de point de terminaison. Ajoutez votre clé d’abonnement dans l’en-tête `Ocp-Apim-Subscription-Key`.

    ```java
    public class CustomSrchJava {
    ...
        public static SearchResults SearchWeb (String searchQuery) throws Exception {
            // construct the URL for your search request (endpoint + query string)
            URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchTerm, "UTF-8") + "&CustomConfig=" + customConfigId);
            HttpsURLConnection connection = (HttpsURLConnection)url.openConnection();
            connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);
    ...
    ```

2. Créez un flux de données et stockez la réponse JSON dans un objet `SearchResults`.

    ```java
    public class CustomSrchJava {
    ...
        public static SearchResults SearchWeb (String searchQuery) throws Exception {
            ...
            // receive the JSON body
            InputStream stream = connection.getInputStream();
            String response = new Scanner(stream).useDelimiter("\\A").next();
        
            // construct result object for return
            SearchResults results = new SearchResults(new HashMap<String, String>(), response);
            
            stream.close();
            return results;
        }
    ```

3. Imprimez la réponse JSON.

    ```java
    System.out.println("\nJSON Response:\n");
    System.out.println(prettify(result.jsonResponse));
    ```

4. Exécutez le programme.
    
## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer une application web Recherche personnalisée](./tutorials/custom-search-web-page.md)
