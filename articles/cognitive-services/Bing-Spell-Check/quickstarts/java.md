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
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: d2905d05dce48b705de44780425ed2b55b02555c
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2019
ms.locfileid: "56888980"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-java"></a>Rychlý start: Kontrola pravopisu pomocí rozhraní REST API kontrolu pravopisu Bingu a Javy.

V tomto rychlém startu můžete provést první volání do rozhraní API Bingu pro kontrolu pravopisu zkontrolujte REST. Tato jednoduchá aplikace Java odešle žádost na rozhraní API a vrátí seznam navrhovaných oprav. Zatímco tato aplikace je napsána v jazyce Java, je rozhraní API RESTful webová služba, která je kompatibilní s Většina programovacích jazyků. Zdrojový kód pro tuto aplikaci je k dispozici na [Githubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingSpellCheckv7.java).

## <a name="prerequisites"></a>Požadavky

Kit(JDK) vývoj Java 7 nebo novější.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]


## <a name="create-and-initialize-an-application"></a>Vytváření a inicializace aplikace

1. Vytvoření nového projektu v Javě v vaše oblíbené prostředím IDE nebo editorem a importovat následující balíčky.

    ```java
    import java.io.*;
    import java.net.*;
    import javax.net.ssl.HttpsURLConnection;
    ```

2. Vytváření proměnných pro koncový bod rozhraní API hostitele, cestu a váš klíč předplatného. Vytvořte proměnné pro trh, textu, který chcete kontrola pravopisu a řetězec pro režim kontroly pravopisu.

    ```java
    static String host = "https://api.cognitive.microsoft.com";
    static String path = "/bing/v7.0/spellcheck";

    static String key = "ENTER YOUR KEY HERE";

    static String mkt = "en-US";
    static String mode = "proof";
    static String text = "Hollo, wrld!";
    ```

## <a name="create-and-send-an-api-request"></a>Vytvoření a odeslání žádosti o rozhraní API

1. Vytvořit funkci s názvem `check()` vytvářet a odesílat žádosti rozhraní API. V něm postupujte podle těchto kroků. Vytvoření řetězce pro parametry požadavku. Připojte `?mkt=` parametr na řetězec vašeho trhu a `&mode=` parametr režim kontroly pravopisu.  

   ```java
   public static void check () throws Exception {
       String params = "?mkt=" + mkt + "&mode=" + mode;
   //...
   }
   ```

2. Vytvoří adresu URL kombinací řetězec hostitele, cestu a parametry koncového bodu. Vytvořte nový `HttpsURLConnection` obejct.

    ```java
    URL url = new URL(host + path + params);
    HttpsURLConnection connection = (HttpsURLConnection) 
    ```

3. Otevřete připojení k adrese URL. Nastaví metodu požadavku `POST`. Přidáte parametry požadavku. Ujistěte se, že váš klíč předplatného pro přidání `Ocp-Apim-Subscription-Key` záhlaví. 

    ```java
    url.openConnection();
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

## <a name="read-the-response"></a>Čtení odpovědi

1. Vytvoření `BufferedReader` a přečíst odpověď z rozhraní API. Vytisknout na konzole.
    
    ```java
    BufferedReader in = new BufferedReader(
    new InputStreamReader(connection.getInputStream()));
    String line;
    while ((line = in.readLine()) != null) {
        System.out.println(line);
    }
    in.close();
    ```

2. V hlavní funkci vaší aplikace zavolejte funkci vytvořili výše. 

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
