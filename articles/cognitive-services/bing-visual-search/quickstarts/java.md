---
title: 'Úvodní příručka: Získejte přehledy obrázků pomocí rozhraní REST API a Javy – vizuální vyhledávání Bingu'
titleSuffix: Azure Cognitive Services
description: Přečtěte si, jak nahrát obrázek do rozhraní API pro vizuální vyhledávání Bingu a získat o něm přehledy.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: scottwhi
ms.openlocfilehash: fe323fc27062ad1bee9abdfaf3408430e28523a9
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75446619"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-java"></a>Úvodní příručka: Získejte přehledy obrázků pomocí rozhraní API PRO Vizuální vyhledávání Bingu REST API a Javy

Pomocí tohoto rychlého startu můžete provést první volání do rozhraní API pro vizuální vyhledávání Bingu a zobrazit výsledky. Tato java aplikace nahraje obrázek do rozhraní API a zobrazí informace, které vrátí. Ačkoli tato aplikace je napsána v Jazyce Java, API je RESTful webová služba kompatibilní s většinou programovacích jazyků.

## <a name="prerequisites"></a>Požadavky

* [Java Development Kit (JDK) 7 nebo 8](https://aka.ms/azure-jdks)
* [Knihovna Gson Java](https://github.com/google/gson)
* [Apache HttpComponents](https://hc.apache.org/downloads.cgi)

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Vytvoření a inicializace projektu

1. Vytvořte nový projekt Java ve svém oblíbeném rozhraní IDE nebo editoru a importujte následující knihovny:

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

2. Vytvořte proměnné pro koncový bod rozhraní API, klíč předplatného a cestu k bitové kopii. `endpoint`může být globální koncový bod níže nebo vlastní koncový bod [subdomény](../../../cognitive-services/cognitive-services-custom-subdomains.md) zobrazený na webu Azure Portal pro váš prostředek:

    ```java
    static String endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
    static String subscriptionKey = "your-key-here";
    static String imagePath = "path-to-your-image";
    ```

    
    Při nahrávání místního obrázku musí data `Content-Disposition` formuláře obsahovat záhlaví. Je nutné `name` nastavit jeho parametr na "image" `filename` a můžete nastavit parametr na libovolný řetězec. Obsah formuláře obsahuje binární data obrazu. Maximální velikost obrázku, kterou můžete nahrát, je 1 MB.
    
    ```
    --boundary_1234-abcd
    Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
    
    ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...
    
    --boundary_1234-abcd--
    ```

## <a name="create-the-json-parser"></a>Vytvoření analyzátoru JSON

Vytvořte metodu, aby odpověď JSON z `JsonParser`rozhraní API čitelnější pomocí :

```java
public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonObject json = parser.parse(json_text).getAsJsonObject();
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }
```

## <a name="construct-the-search-request-and-query"></a>Sestavení žádosti o vyhledávání a dotazu

1. V hlavní metodě aplikace vytvořte klienta HTTP pomocí `HttpClientBuilder.create().build();`:

    ```java
    CloseableHttpClient httpClient = HttpClientBuilder.create().build();
    ```

2. Vytvořte `HttpEntity` objekt pro nahrání obrázku do rozhraní API:

    ```java
    HttpEntity entity = MultipartEntityBuilder
        .create()
        .addBinaryBody("image", new File(imagePath))
        .build();
    ```

3. Vytvořte `httpPost` objekt s koncovým bodem a nastavte záhlaví tak, aby používalo klíč předplatného:

    ```java
    HttpPost httpPost = new HttpPost(endpoint);
    httpPost.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
    httpPost.setEntity(entity);
    ```

## <a name="receive-and-process-the-json-response"></a>Příjem a zpracování odpovědi JSON

1. Pomocí `HttpClient.execute()` metody odešlete požadavek do rozhraní API `InputStream` a uložte odpověď do objektu:
    
    ```java
    HttpResponse response = httpClient.execute(httpPost);
    InputStream stream = response.getEntity().getContent();
    ```

2. Uložte řetězec JSON a vytiskněte odpověď:

    ```java
    String json = new Scanner(stream).useDelimiter("\\A").next();
    System.out.println("\nJSON Response:\n");
    System.out.println(prettify(json));
    ```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření jednostránkové webové aplikace vizuálního vyhledávání](../tutorial-bing-visual-search-single-page-app.md)
