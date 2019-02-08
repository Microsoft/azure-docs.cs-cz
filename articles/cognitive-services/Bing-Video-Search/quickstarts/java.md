---
title: 'Rychlý start: Hledání videí pomocí API REST pro vyhledávání videí Bingu a Java'
titlesuffix: Azure Cognitive Services
description: V tomto rychlém startu používáme k odeslání žádosti pro vyhledávání videí do Bingu Video Search REST API pomocí Javy.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 01/31/2019
ms.author: aahi
ms.openlocfilehash: 3b0060b189dd941a8705d7e9815f69db8ea035e8
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55863308"
---
# <a name="quickstart-search-for-videos-using-the-bing-video-search-rest-api-and-java"></a>Rychlý start: Hledání videí pomocí API REST pro vyhledávání videí Bingu a Java

V tomto rychlém startu můžete provést první volání do rozhraní API Bingu pro vyhledávání Video a zobrazení výsledků na vyhledávacím z odpovědi JSON. Tato jednoduchá aplikace Java odešle dotaz pro vyhledávání videí HTTP do rozhraní API a zobrazí odpovědi. Aplikace je sice napsaná v Javě, ale rozhraní API je webová služba RESTful kompatibilní s většinou programovacích jazyků. Zdrojový kód pro tuto ukázku je k dispozici [na Githubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingVideoSearchv7.java) zpracování dalších chyb, funkcí a kód poznámky.

## <a name="prerequisites"></a>Požadavky

* [Java Development Kit(JDK)](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html)

* [Knihovna Gson](https://github.com/google/gson)

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>Vytvoření a inicializace projektu

1. V oblíbeném integrovaném vývojovém prostředí nebo editoru vytvořte nový projekt Java a naimportujte následující knihovny.

    ```java
    import java.net.*;
    import java.util.*;
    import java.io.*;
    import javax.net.ssl.HttpsURLConnection;
    import com.google.gson.Gson;
    import com.google.gson.GsonBuilder;
    import com.google.gson.JsonObject;
    import com.google.gson.JsonParser;
    ```

2. Vytvořte novou třídu s názvem `SearchResults` k uložení záhlaví a odpověď JSON z rozhraní API.

    ```java
    // Container class for search results encapsulates relevant headers and JSON data
    class SearchResults{
        HashMap<String, String> relevantHeaders;
        String jsonResponse;
        SearchResults(HashMap<String, String> headers, String json) {
            relevantHeaders = headers;
            jsonResponse = json;
        }
    }
    ```

3. Vytvořit novou metodu s názvem `SearchVideos()` s proměnnými pro hostitele vašeho koncového bodu rozhraní API a cestu, váš klíč předplatného a hledaný termín. Vrátí `SearchResults` objektu. 

    ```java
    public static SearchResults SearchVideos (String searchQuery) throws Exception {
        static String subscriptionKey = "enter your key here";
        static String host = "https://api.cognitive.microsoft.com";
        static String path = "/bing/v7.0/videos/search";
        static String searchTerm = "kittens";
    }
    ```

## <a name="construct-and-send-the-search-request"></a>Sestavit a odeslat žádost o vyhledávání

1. V `SearchVideos()`, proveďte následující kroky:

    1. Díky spojení vašeho rozhraní API se vytvoří adresa URL pro váš požadavek hostitele, cestu a kódování vašemu vyhledávacímu dotazu. Pak pomocí `openConnection()` vytvořit připojení, a přidat váš klíč předplatného na `Ocp-Apim-Subscription-Key` záhlaví.

        ```java
        URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchQuery, "UTF-8"));
        HttpsURLConnection connection = (HttpsURLConnection)url.openConnection();
        connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);
        ```

    2. Získání odpovědi z rozhraní API a uložte řetězec formátu JSON.

        ```java
        InputStream stream = connection.getInputStream();
        String response = new Scanner(stream).useDelimiter("\\A").next();
        ```

    3. Použití `getHeaderFields();` extrahovat hlavičky protokolu HTTP z odpovědi a uložení Bingu související ty v `results` objektu. Potom zavřete datový proud a vrátí výsledek.

        ```java
        // extract Bing-related HTTP headers
        Map<String, List<String>> headers = connection.getHeaderFields();
        for (String header : headers.keySet()) {
            if (header == null) continue;      // may have null key
            if (header.startsWith("BingAPIs-") || header.startsWith("X-MSEdge-")) {
                results.relevantHeaders.put(header, headers.get(header).get(0));
            }
        }
        stream.close();
        return results;
        ```

## <a name="format-the-response"></a>Formátování odpovědi

1. Vytvořit metodu s názvem `prettify()` formátovat odpověď vrácenou z rozhraní API Bingu pro Video. Použít knihovnu Gson `JsonParser` využít v řetězci JSON a převádět je do objektu. Pak pomocí `GsonBuilder()` a `toJson()` vytvořit formátovaný řetězec. 

    ```java
    // pretty-printer for JSON; uses GSON parser to parse and re-serialize
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonObject json = parser.parse(json_text).getAsJsonObject();
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }
    ```

## <a name="send-the-request-and-print-the-response"></a>Odeslat požadavek a tisku odpověď

1. V hlavní metodě vaší aplikace, zavolejte `SearchVideos` se hledaný termín. potom můžete vytisknout hlavičky protokolu HTTP, které jsou uloženy v odpovědi, stejně jako řetězec JSON vrácená rozhraním API.

    ```java
    public static void main (String[] args) {

        SearchResults result = SearchVideos(searchTerm);
        //print the Relevant HTTP Headers
        for (String header : result.relevantHeaders.keySet())
            System.out.println(header + ": " + result.relevantHeaders.get(header));
        System.out.println(prettify(result.jsonResponse));
    }
    ```

## <a name="json-response"></a>Odpověď JSON

Úspěšná odpověď se vrátí ve formátu JSON, jak je znázorněno v následujícím příkladu:

```json
{
    "_type": "Videos",
    "instrumentation": {},
    "readLink": "https://api.cognitive.microsoft.com/api/v7/videos/search?q=kittens",
    "webSearchUrl": "https://www.bing.com/videos/search?q=kittens",
    "totalEstimatedMatches": 1000,
    "value": [
        {
            "webSearchUrl": "https://www.bing.com/videos/search?q=kittens&view=...",
            "name": "Top 10 cute kitten videos compilation",
            "description": "HELP HOMELESS ANIMALS AND WIN A PRIZE BY CHOOSING...",
            "thumbnailUrl": "https://tse4.mm.bing.net/th?id=OVP.n1aE_Oikl4MtzBb...",
            "datePublished": "2014-11-12T22:47:36.0000000",
            "publisher": [
                {
                    "name": "Fabrikam"
                }
            ],
            "creator": {
                "name": "Marcus Appel"
            },
            "isAccessibleForFree": true,
            "contentUrl": "https://www.fabrikam.com/watch?v=8HVWitAW-Qg",
            "hostPageUrl": "https://www.fabrikam.com/watch?v=8HVWitAW-Qg",
            "encodingFormat": "h264",
            "hostPageDisplayUrl": "https://www.fabrikam.com/watch?v=8HVWitAW-Qg",
            "width": 480,
            "height": 360,
            "duration": "PT3M52S",
            "motionThumbnailUrl": "https://tse4.mm.bing.net/th?id=OM.j4QyJAENJphdZQ_1501386166&pid=Api",
            "embedHtml": "<iframe width=\"1280\" height=\"720\" src=\"https://www.fabrikam.com/embed/8HVWitAW-Qg?autoplay=1\" frameborder=\"0\" allowfullscreen></iframe>",
            "allowHttpsEmbed": true,
            "viewCount": 7513633,
            "thumbnail": {
                "width": 300,
                "height": 168
            },
            "videoId": "655D98260D012432848F6558260D012432848F",
            "allowMobileEmbed": true,
            "isSuperfresh": false
        },
        . . .
    ],
    "nextOffset": 36,
    "queryExpansions": [
        {
            "text": "Kittens Meowing",
            "displayText": "Meowing",
            "webSearchUrl": "https://www.bing.com/videos/search?q=Kittens+Meowing...",
            "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search...",
            "thumbnail": {
                "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Kittens+Meowing&pid..."
            }
        },
        {
            "text": "Funny Kittens",
            "displayText": "Funny",
            "webSearchUrl": "https://www.bing.com/videos/search?q=Funny+Kittens...",
            "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search...",
            "thumbnail": {
                "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Funny+Kittens&..."
            }
        },
        . . .
    ],
    "pivotSuggestions": [
        {
            "pivot": "kittens",
            "suggestions": [
                {
                    "text": "Cat",
                    "displayText": "Cat",
                    "webSearchUrl": "https://www.bing.com/videos/search?q=Cat...",
                    "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search?...",
                    "thumbnail": {
                        "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Cat&pid=Api..."
                    }
                },
                {
                    "text": "Feral Cat",
                    "displayText": "Feral Cat",
                    "webSearchUrl": "https://www.bing.com/videos/search?q=Feral+Cat...",
                    "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search...",
                    "thumbnail": {
                        "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Feral+Cat&pid=Api&..."
                    }
                }
            ]
        }
    ],
    "relatedSearches": [
        {
            "text": "Kittens Being Born",
            "displayText": "Kittens Being Born",
            "webSearchUrl": "https://www.bing.com/videos/search?q=Kittens+Being+Born...",
            "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search?...",
            "thumbnail": {
                "thumbnailUrl": "https://tse1.mm.bing.net/th?q=Kittens+Being+Born&pid=..."
            }
        },
        . . .
    ]
}
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vytvoření webové jednostránkové aplikace](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Další informace najdete v tématech 

 [Co je API pro vyhledávání videí Bingu?](../overview.md)
