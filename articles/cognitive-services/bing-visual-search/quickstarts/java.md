---
title: 'Rychlý start: Získejte přehledy obrázků pomocí API REST pro vizuální vyhledávání Bingu a Java'
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak k nahrání obrázku do Visual API Bingu pro vyhledávání a získávat přehledy o něm.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 6a3eb9a7b85f3cd50384389ee8b03681c097dc27
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55859859"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-java"></a>Rychlý start: Získejte přehledy obrázků pomocí API REST pro vizuální vyhledávání Bingu a Java

V tomto rychlém startu můžete provést první volání do rozhraní API vizuální vyhledávání Bingu a zobrazení výsledků hledání. Tento jednoduchý C# aplikace odešle obrázek do rozhraní API a zobrazí informace vrácené o něm. Aplikace je sice napsaná v Javě, ale rozhraní API je webová služba RESTful kompatibilní s většinou programovacích jazyků.

Při nahrávání místní image, data formuláře musí zahrnovat hlavičku Content-Disposition. Její parametr `name` musí být nastavený na "image" a parametr `filename` může být nastavený na libovolný řetězec. Obsah formuláře je binární soubor obrázku. Maximální velikost obrázku, kterou můžete nahrát, je 1 MB.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

## <a name="prerequisites"></a>Požadavky

* [Java Development Kit (JDK) 7 nebo 8](https://aka.ms/azure-jdks)
* [Knihovna Gson](https://github.com/google/gson)
* [Apache HttpComponents](http://hc.apache.org/downloads.cgi)


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Vytvoření a inicializace projektu

1. V oblíbeném integrovaném vývojovém prostředí nebo editoru vytvořte nový projekt Java a naimportujte následující knihovny.

    ```java
    import java.util.*;
    import java.io.*;
    import com.google.gson.Gson;
    import com.google.gson.GsonBuilder;
    import com.google.gson.JsonObject;
    import com.google.gson.JsonParser;
    
    // HttpClient libraries
    
    import org.apache.http.HttpEntity;
    import org.apache.http.HttpResponse;
    import org.apache.http.client.methods.HttpPost;
    import org.apache.http.entity.ContentType;
    import org.apache.http.entity.mime.MultipartEntityBuilder;
    import org.apache.http.impl.client.CloseableHttpClient;
    import org.apache.http.impl.client.HttpClientBuilder;
    ```

2. Vytváření proměnných pro váš koncový bod rozhraní API, klíč předplatného a cestu k bitové kopii. 

    ```java
    static String endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
    static String subscriptionKey = "your-key-here";
    static String imagePath = "path-to-your-image";
    ```

## <a name="create-the-json-parser"></a>Vytvoření analyzátor JSON

Vytvořit metodu odpověď JSON z rozhraní API lépe čitelný pomocí `JsonParser`.

    ```java
    public static String prettify(String json_text) {
            JsonParser parser = new JsonParser();
            JsonObject json = parser.parse(json_text).getAsJsonObject();
            Gson gson = new GsonBuilder().setPrettyPrinting().create();
            return gson.toJson(json);
        }
    ```

## <a name="construct-the-search-request-and-query"></a>Sestavení žádosti o vyhledávání a dotazu

1. V hlavní metodě aplikace vytvořit klienta Http pomocí `HttpClientBuilder.create().build();`.

    ```java
    CloseableHttpClient httpClient = HttpClientBuilder.create().build();
    ```

2. Vytvoření `HttpEntity` k nahrajete svou image do rozhraní API.

    ```java
    HttpEntity entity = MultipartEntityBuilder
        .create()
        .addBinaryBody("image", new File(imagePath))
        .build();
    ```

3. Vytvoření `httpPost` objekt s váš koncový bod a nastavte záhlaví používat váš klíč předplatného.

    ```java
    HttpPost httpPost = new HttpPost(endpoint);
    httpPost.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
    httpPost.setEntity(entity);
    ```

## <a name="receive-and-process-the-json-response"></a>Příjem a zpracování odpovědi JSON

1. Použití `HttpClient.execute()` odeslat požadavek na rozhraní API a uložení odpovědi v `InputStream` objektu.
    
    ```java
    HttpResponse response = httpClient.execute(httpPost);
    InputStream stream = response.getEntity().getContent();
    ```

2. Store řetězec formátu JSON a tisku na odpověď.

```java
String json = new Scanner(stream).useDelimiter("\\A").next();
System.out.println("\nJSON Response:\n");
System.out.println(prettify(json));
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Sestavení webové aplikace s vlastní vyhledávání](../tutorial-bing-visual-search-single-page-app.md)
