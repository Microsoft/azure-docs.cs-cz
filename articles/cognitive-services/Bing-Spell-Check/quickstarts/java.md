---
title: 'Rychlý start: Kontrola pravopisu pomocí rozhraní REST API kontrolu pravopisu Bingu a Javy.'
titlesuffix: Azure Cognitive Services
description: Začněte používat rozhraní API Bingu pro kontrolu pravopisu zkontrolujte REST pro kontrolu pravopisu a gramatiky.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 04/11/2019
ms.author: aahi
ms.openlocfilehash: a139d0558565114725c6198f64e139e5a5019c75
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2019
ms.locfileid: "59616691"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-java"></a>Rychlý start: Kontrola pravopisu pomocí rozhraní REST API kontrolu pravopisu Bingu a Javy.

V tomto rychlém startu můžete provést první volání do rozhraní API Bingu pro kontrolu pravopisu zkontrolujte REST. Tato jednoduchá aplikace Java odešle žádost na rozhraní API a vrátí seznam navrhovaných oprav. Zatímco tato aplikace je napsána v jazyce Java, je rozhraní API RESTful webová služba, která je kompatibilní s Většina programovacích jazyků. Zdrojový kód pro tuto aplikaci je k dispozici na [Githubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingSpellCheckv7.java).

## <a name="prerequisites"></a>Požadavky

* Kit(JDK) vývoj Java 7 nebo novější.

* Import [gson 2.8.5.jar](https://libraries.io/maven/com.google.code.gson%3Agson) nebo nejnovější [Gson](https://github.com/google/gson) verze. Pro spuštění příkazového řádku, přidejte `.jar` do složky s hlavní třída Java.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-an-application"></a>Vytváření a inicializace aplikace

1. Vytvoření nového projektu Java v Oblíbené prostředí IDE nebo editoru s názvem třídy, které si vyberete a importujte následující balíčky.

    ```java
    import java.io.*;
    import java.net.*;
    import com.google.gson.*;
    import javax.net.ssl.HttpsURLConnection;
    ```

2. Vytváření proměnných pro koncový bod rozhraní API hostitele, cestu a váš klíč předplatného. Vytvořte proměnné pro trh, textu, který chcete kontrola pravopisu a řetězec pro režim kontroly pravopisu.

    ```java
    static String host = "https://api.cognitive.microsoft.com";
    static String path = "/bing/v7.0/spellcheck";

    static String key = "<ENTER-KEY-HERE>";

    static String mkt = "en-US";
    static String mode = "proof";
    static String text = "Hollo, wrld!";
    ```

## <a name="create-and-send-an-api-request"></a>Vytvoření a odeslání žádosti o rozhraní API

1. Vytvořit funkci s názvem `check()` vytvářet a odesílat žádosti rozhraní API. V něm postupujte podle těchto kroků. Vytvoření řetězce pro parametry požadavku. Připojte `?mkt=` parametr na řetězec vašeho trhu a `&mode=` parametr režim kontroly pravopisu.  

   ```java
   public static void check () throws Exception {
       String params = "?mkt=" + mkt + "&mode=" + mode;
      // add the rest of the code snippets here (except prettify() and main())...
   }
   ```

2. Vytvoří adresu URL kombinací řetězec hostitele, cestu a parametry koncového bodu. Vytvořte nový `HttpsURLConnection` objektu.

    ```java
    URL url = new URL(host + path + params);
    HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
    ```

3. Otevřete připojení k adrese URL. Nastaví metodu požadavku `POST`. Přidáte parametry požadavku. Ujistěte se, že váš klíč předplatného pro přidání `Ocp-Apim-Subscription-Key` záhlaví.

    ```java
    connection.setRequestMethod("POST");
    connection.setRequestProperty("Content-Type", "application/x-www-form-urlencoded");
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", key);
    connection.setDoOutput(true);
    ```

4. Vytvořte nový `DataOutputStream` objekt a odešlete žádost na rozhraní API.

    ```java
        DataOutputStream wr = new DataOutputStream(connection.getOutputStream());
        wr.writeBytes("text=" + text);
        wr.flush();
        wr.close();
    ```

## <a name="format-and-read-the-api-response"></a>Formátování a čtení odpovědi rozhraní API

1. Přidejte tuto metodu do vaší třídy. Formátuje JSON pro lépe čitelný výstup.

    ``` java
    // This function prettifies the json response.
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

1. Create a `BufferedReader` and read the response from the API. Print it to the console.
    
    ```java
    BufferedReader in = new BufferedReader(
    new InputStreamReader(connection.getInputStream()));
    String line;
    while ((line = in.readLine()) != null) {
        System.out.println(prettify(line);
    }
    in.close();
    ```

## <a name="call-the-api"></a>Volání rozhraní API

V hlavní funkci vaší aplikace zavolejte metodu check() vytvořili výše.

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

Sestavte a spusťte váš projekt.

Pokud používáte příkazového řádku, použijte následující příkazy pro sestavení a spuštění aplikace.

**Sestavení:**
```bash
javac -classpath .;gson-2.2.2.jar\* <CLASS_NAME>.java
```

**Spusťte:**
```bash
java -cp .;gson-2.2.2.jar\* <CLASS_NAME>
```

## <a name="example-json-response"></a>Příklad JSON odpovědi

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

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vytvoření jednostránkové webové aplikace](../tutorials/spellcheck.md)

- [Co je API kontrola pravopisu Bingu?](../overview.md)
- [Referenční informace k rozhraní API pro kontrolu pravopisu Bingu v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference)
