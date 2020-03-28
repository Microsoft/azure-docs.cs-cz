---
title: 'Úvodní příručka: Kontrola pravopisu pomocí rozhraní REST API a javy – kontrola pravopisu bingu'
titleSuffix: Azure Cognitive Services
description: Můžete začít používat rozhraní REST API kontroly pravopisu Bingu ke kontrole pravopisu a gramatiky.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 12/16/2019
ms.author: aahi
ms.openlocfilehash: 43c528a1e9e46a67e895679b1a0fd04fef8900a7
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75382955"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-java"></a>Úvodní příručka: Kontrola pravopisu pomocí rozhraní API PRO KONTROLU PRAVOPISU Bingu REST API a Javy

Pomocí tohoto rychlého startu můžete provést první volání do rozhraní REST API kontroly pravopisu Bingu. Tato jednoduchá java aplikace odešle požadavek do rozhraní API a vrátí seznam navrhovaných oprav. Zatímco tato aplikace je napsána v Javě, API je RESTful webová služba kompatibilní s většinou programovacích jazyků. Zdrojový kód pro tuto aplikaci je k dispozici na [GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingSpellCheckv7.java).

## <a name="prerequisites"></a>Požadavky

* Java Development Kit (JDK) 7 nebo novější.

* Importujte [gson-2.8.5.jar](https://libraries.io/maven/com.google.code.gson%3Agson) nebo nejaktuálnější verzi [Gson.](https://github.com/google/gson) Pro spuštění příkazového `.jar` řádku přidejte do složky Java s hlavní třídou.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-an-application"></a>Vytvoření a inicializaci aplikace

1. Vytvořte nový projekt Java ve svém oblíbeném rozhraní IDE nebo editoru s názvem třídy podle vašeho výběru a pak importujte následující balíčky.

    ```java
    import java.io.*;
    import java.net.*;
    import com.google.gson.*;
    import javax.net.ssl.HttpsURLConnection;
    ```

2. Vytvořte proměnné pro hostitele koncového bodu rozhraní API, cestu a klíč předplatného. Poté vytvořte proměnné pro váš trh, text, který chcete zkontrolovat pravopisem, a řetězec pro režim kontroly pravopisu. Můžete použít globální koncový bod níže nebo vlastní koncový bod [subdomény](../../../cognitive-services/cognitive-services-custom-subdomains.md) zobrazený na portálu Azure pro váš prostředek.

    ```java
    static String host = "https://api.cognitive.microsoft.com";
    static String path = "/bing/v7.0/spellcheck";

    static String key = "<ENTER-KEY-HERE>";

    static String mkt = "en-US";
    static String mode = "proof";
    static String text = "Hollo, wrld!";
    ```

## <a name="create-and-send-an-api-request"></a>Vytvoření a odeslání požadavku rozhraní API

1. Vytvořte funkci `check()` volanou k vytvoření a odeslání požadavku rozhraní API. V něm postupujte takto. Vytvořte řetězec pro parametry požadavku. připojujte `?mkt=` parametr k řetězci `&mode=` trhu a parametr do režimu kontroly pravopisu.  

   ```java
   public static void check () throws Exception {
       String params = "?mkt=" + mkt + "&mode=" + mode;
      // add the rest of the code snippets here (except prettify() and main())...
   }
   ```

2. Vytvořte adresu URL kombinací řetězce hostitele koncového bodu, cesty a parametrů. Vytvořte `HttpsURLConnection` nový objekt.

    ```java
    URL url = new URL(host + path + params);
    HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
    ```

3. Otevřete připojení k adrese URL. Nastavte metodu `POST`požadavku na . Přidejte parametry požadavku. Nezapomeňte do `Ocp-Apim-Subscription-Key` záhlaví přidat klíč předplatného.

    ```java
    connection.setRequestMethod("POST");
    connection.setRequestProperty("Content-Type", "application/x-www-form-urlencoded");
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", key);
    connection.setDoOutput(true);
    ```

4. Vytvořte `DataOutputStream` nový objekt a odeslat požadavek do rozhraní API.

    ```java
        DataOutputStream wr = new DataOutputStream(connection.getOutputStream());
        wr.writeBytes("text=" + text);
        wr.flush();
        wr.close();
    ```

## <a name="format-and-read-the-api-response"></a>Formátování a čtení odpovědi rozhraní API

1. Přidejte tuto metodu do třídy. Formátuje JSON pro čitelnější výstup.

    ``` java
    // This function prettifies the json response.
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }
    ```

1. Vytvořte `BufferedReader` a přečtěte si odpověď z rozhraní API. Vytiskněte ji do konzole.
    
    ```java
    BufferedReader in = new BufferedReader(
    new InputStreamReader(connection.getInputStream()));
    String line;
    while ((line = in.readLine()) != null) {
        System.out.println(prettify(line));
    }
    in.close();
    ```

## <a name="call-the-api"></a>Volání rozhraní API

V hlavní funkci aplikace volejte výše vytvořenou metodu check().
```java
        public static void main(String[] args) {
            try {
                check();
            }
            catch (Exception e) {
                System.out.println (e);
            }
        }
```

## <a name="run-the-application"></a>Spuštění aplikace

Sestavení a spuštění projektu.

Pokud používáte příkazový řádek, vytvořte a spusťte aplikaci pomocí následujících příkazů.

**Budovat:**
```bash
javac -classpath .;gson-2.2.2.jar\* <CLASS_NAME>.java
```

**Spuštěním příkazu**
```bash
java -cp .;gson-2.2.2.jar\* <CLASS_NAME>
```

## <a name="example-json-response"></a>Příklad odpovědi JSON

Úspěšná odpověď se vrátí ve formátu JSON, jak je znázorněno v následujícím příkladu:

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření jednostránkové webové aplikace](../tutorials/spellcheck.md)

- [Co je rozhraní API pro kontrolu pravopisu Bingu?](../overview.md)
- [Referenční informace k rozhraní API pro kontrolu pravopisu Bingu v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
