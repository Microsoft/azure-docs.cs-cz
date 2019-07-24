---
title: 'Rychlý start: Kontrola pravopisu pomocí Kontrola pravopisu Bingu REST API a Java'
titleSuffix: Azure Cognitive Services
description: Začněte používat REST API Kontrola pravopisu Bingu pro kontrolu pravopisu a gramatiky.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 04/11/2019
ms.author: aahi
ms.openlocfilehash: 6680ddd65ce43a71873acb823f8ae57b449a56be
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423521"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-java"></a>Rychlý start: Kontrola pravopisu pomocí Kontrola pravopisu Bingu REST API a Java

V tomto rychlém startu můžete provést první volání REST API Kontrola pravopisu Bingu. Tato jednoduchá aplikace Java pošle požadavek do rozhraní API a vrátí seznam navrhovaných oprav. I když je tato aplikace napsaná v jazyce Java, rozhraní API je webová služba RESTful kompatibilní s většinou programovacích jazyků. Zdrojový kód této aplikace je k dispozici na [GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingSpellCheckv7.java).

## <a name="prerequisites"></a>Požadavky

* Java Development Kit (JDK) 7 nebo novější.

* Importujte [gson-2.8.5. jar](https://libraries.io/maven/com.google.code.gson%3Agson) nebo nejaktuálnější verzi [gson](https://github.com/google/gson) . Pro spuštění příkazového řádku přidejte `.jar` do složky Java s hlavní třídou.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-an-application"></a>Vytvoření a inicializace aplikace

1. Vytvořte nový projekt Java v oblíbeném integrovaném vývojovém prostředí (IDE) nebo editoru s názvem třídy, který zvolíte, a pak importujte následující balíčky.

    ```java
    import java.io.*;
    import java.net.*;
    import com.google.gson.*;
    import javax.net.ssl.HttpsURLConnection;
    ```

2. Vytvořte proměnné pro hostitele koncového bodu rozhraní API, cestu a klíč předplatného. Pak vytvořte proměnné pro svůj trh, text, který chcete kontrolovat pravopis, a řetězec pro režim kontroly pravopisu.

    ```java
    static String host = "https://api.cognitive.microsoft.com";
    static String path = "/bing/v7.0/spellcheck";

    static String key = "<ENTER-KEY-HERE>";

    static String mkt = "en-US";
    static String mode = "proof";
    static String text = "Hollo, wrld!";
    ```

## <a name="create-and-send-an-api-request"></a>Vytvoření a odeslání žádosti rozhraní API

1. Vytvořte funkci volanou `check()` k vytvoření a odeslání požadavku rozhraní API. V takovém případě postupujte podle těchto kroků. Vytvořte řetězec pro parametry požadavku. Přidejte parametr do vašeho tržního řetězce `&mode=` a parametr do režimu kontroly pravopisu. `?mkt=`  

   ```java
   public static void check () throws Exception {
       String params = "?mkt=" + mkt + "&mode=" + mode;
      // add the rest of the code snippets here (except prettify() and main())...
   }
   ```

2. Vytvořte adresu URL tak, že zkombinujete řetězec hostitele koncového bodu, cestu a parametry. Vytvoří nový `HttpsURLConnection` objekt.

    ```java
    URL url = new URL(host + path + params);
    HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
    ```

3. Otevřete připojení k adrese URL. Nastavte metodu Request na `POST`. Přidejte parametry žádosti. Nezapomeňte do `Ocp-Apim-Subscription-Key` hlavičky přidat svůj klíč předplatného.

    ```java
    connection.setRequestMethod("POST");
    connection.setRequestProperty("Content-Type", "application/x-www-form-urlencoded");
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", key);
    connection.setDoOutput(true);
    ```

4. Vytvořte nový `DataOutputStream` objekt a odešlete požadavek do rozhraní API.

    ```java
        DataOutputStream wr = new DataOutputStream(connection.getOutputStream());
        wr.writeBytes("text=" + text);
        wr.flush();
        wr.close();
    ```

## <a name="format-and-read-the-api-response"></a>Formátování a čtení odpovědi rozhraní API

1. Přidejte tuto metodu do vaší třídy. Formátuje kód JSON pro čitelnější výstup.

    ``` java
    // This function prettifies the json response.
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }
    ```

1. `BufferedReader` Vytvořte a přečtěte si odpověď z rozhraní API. Vytiskněte ho do konzoly.
    
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

V hlavní funkci aplikace zavolejte metodu check (), kterou jste vytvořili výše.

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

Sestavte a spusťte projekt.

Pokud používáte příkazový řádek, pomocí následujících příkazů Sestavte a spusťte aplikaci.

**Budování**
```bash
javac -classpath .;gson-2.2.2.jar\* <CLASS_NAME>.java
```

**Spouštěl**
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

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vytvoření jednostránkové webové aplikace](../tutorials/spellcheck.md)

- [Co je rozhraní API Bingu pro kontrolu pravopisu?](../overview.md)
- [Referenční informace k rozhraní API pro kontrolu pravopisu Bingu v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
