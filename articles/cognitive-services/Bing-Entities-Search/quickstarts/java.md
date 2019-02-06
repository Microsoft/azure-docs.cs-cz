---
title: 'Rychlý start: Odeslat žádost o vyhledávání Bingu Entity Search REST API pomocí Javy'
titlesuffix: Azure Cognitive Services
description: V tomto rychlém startu můžete odeslat požadavek na rozhraní Bing Entity REST API pro vyhledávání pomocí Javy a přijetí odpovědi JSON.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: befcdca4dc98e3c69868834daf77d8d7810af3dd
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55749112"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-java"></a>Rychlý start: Odeslat žádost o vyhledávání Bingu Entity Search REST API pomocí Javy

V tomto rychlém startu můžete provést první volání do rozhraní API Bingu pro vyhledávání entit a zobrazit odpověď JSON. Tato jednoduchá aplikace Java odešle dotaz vyhledávání zpráv na rozhraní API a zobrazí odpovědi.

Aplikace je sice napsaná v Javě, ale rozhraní API je webová služba RESTful kompatibilní s většinou programovacích jazyků.

## <a name="prerequisites"></a>Požadavky

* [Java Development Kit(JDK)](https://www.oracle.com/technetwork/java/javase/downloads/)
* [Knihovna Gson](https://github.com/google/gson)


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Vytvoření a inicializace projektu

1. V oblíbeném integrovaném vývojovém prostředí nebo editoru vytvořte nový projekt Java a naimportujte následující knihovny.

  ```java
  import java.io.*;
  import java.net.*;
  import java.util.*;
  import javax.net.ssl.HttpsURLConnection;
  import com.google.gson.Gson;
  import com.google.gson.GsonBuilder;
  import com.google.gson.JsonObject;
  import com.google.gson.JsonParser;
  import com.google.gson.Gson;
  import com.google.gson.GsonBuilder;
  import com.google.gson.JsonObject;
  import com.google.gson.JsonParser;
  ```

2. Ve třídě nové vytvoření proměnné pro koncový bod rozhraní API, váš klíč předplatného a vyhledávací dotaz.

  ```java
  public class EntitySearch {

      static String subscriptionKey = "ENTER KEY HERE";
    
        static String host = "https://api.cognitive.microsoft.com";
        static String path = "/bing/v7.0/entities";
    
        static String mkt = "en-US";
        static String query = "italian restaurant near me";
  //...
    
  ```

## <a name="construct-a-search-request-string"></a>Vytvořit žádost o hledaný řetězec

1. Vytvořit funkci s názvem `search()` JSON, který vrací `String`. Adresa URL – kódování vašemu vyhledávacímu dotazu a přidejte ho do parametrů řetězec s `&q=`. Přidání vašeho trhu do řetězce bez `?mkt=`.
 
2. Vytvořte objekt adresy URL v řetězci hostitele, cestu a parametry.
    
    ```java
    //...
    public static String search () throws Exception {
        String encoded_query = URLEncoder.encode (query, "UTF-8");
        String params = "?mkt=" + mkt + "&q=" + encoded_query;
        URL url = new URL (host + path + params);
    //...
    ```
      
## <a name="send-a-search-request-and-receive-a-response"></a>Odeslat žádost o vyhledávání a přijetí odpovědi

1. V `search()` funkce vytvořili výše, vytvořte nový `HttpsURLConnection` objekt s `url.openCOnnection()`. Nastaví metodu požadavku `GET`a přidat váš klíč předplatného na `Ocp-Apim-Subscription-Key` záhlaví.

    ```java
    //...
    HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
    connection.setRequestMethod("GET");
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);
    connection.setDoOutput(true);
    //...
    ```

2. Vytvořte nový `StringBuilder`. Použít novou `InputStreamReader` jako parametr při vytváření instance `BufferedReader` ke čtení odpovědi rozhraní API.  
    
    ```java
    //...
    StringBuilder response = new StringBuilder ();
    BufferedReader in = new BufferedReader(
        new InputStreamReader(connection.getInputStream()));
    //...
    ```

3. Vytvoření `String` objekt pro uložení odpovědi `BufferedReader`. Iterovat přes něj a připojit každý řádek na řetězec. Potom zavřete čtečky a vrací odpověď. 
    
    ```java
    String line;
    
    while ((line = in.readLine()) != null) {
      response.append(line);
    }
    in.close();
    
    return response.toString();
    ```

## <a name="format-the-json-response"></a>Formátovat odpověď JSON

1. Vytvořit novou funkci s názvem `prettify` formátovat odpověď JSON. Vytvořte nový `JsonParser`a volat `parse()` na json text a uložte ho jako objekt JSON. 

2. Použít knihovnu Gson k vytvoření nového `GsonBuilder()`a použijte `setPrettyPrinting().create()` do formátu json. Následně je vrátíte.    
  
  ```java
  //...
  public static String prettify (String json_text) {
    JsonParser parser = new JsonParser();
    JsonObject json = parser.parse(json_text).getAsJsonObject();
    Gson gson = new GsonBuilder().setPrettyPrinting().create();
    return gson.toJson(json);
  }
  //...
  ```

## <a name="call-the-search-function"></a>Volání funkce vyhledávání

1. Hlavní metodou váš projekt, zavolejte `search()`a použijte `prettify()` k formátování textu.
    
    ```java
        public static void main(String[] args) {
            try {
                String response = search ();
                System.out.println (prettify (response));
            }
            catch (Exception e) {
                System.out.println (e);
            }
        }
    ```

## <a name="example-json-response"></a>Příklad JSON odpovědi

Úspěšná odpověď se vrátí ve formátu JSON, jak je znázorněno v následujícím příkladu: 

```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "italian restaurant near me",
    "askUserForLocation": true
  },
  "places": {
    "value": [
      {
        "_type": "LocalBusiness",
        "webSearchUrl": "https://www.bing.com/search?q=sinful+bakery&filters=local...",
        "name": "Liberty's Delightful Sinful Bakery & Cafe",
        "url": "https://www.contoso.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98112",
          "addressCountry": "US",
          "neighborhood": "Madison Park"
        },
        "telephone": "(800) 555-1212"
      },

      . . .
      {
        "_type": "Restaurant",
        "webSearchUrl": "https://www.bing.com/search?q=Pickles+and+Preserves...",
        "name": "Munson's Pickles and Preserves Farm",
        "url": "http://www.princi.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness",
            "Restaurant"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98101",
          "addressCountry": "US",
          "neighborhood": "Capitol Hill"
        },
        "telephone": "(800) 555-1212"
      },
      
      . . .
    ]
  }
}
```

[Zpět na začátek](#HOLTop)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vytvoření webové jednostránkové aplikace](../tutorial-bing-entities-search-single-page-app.md)

* [Co je API pro vyhledávání entit Bingu?](../overview.md )
* [Reference k rozhraní API vyhledávání entit Bingu](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference)
