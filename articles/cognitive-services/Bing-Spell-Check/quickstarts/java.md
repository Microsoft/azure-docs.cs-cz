---
title: 'Rychlý Start: Kontrola pravopisu pomocí REST API a Java-Kontrola pravopisu Bingu'
titleSuffix: Azure Cognitive Services
description: Začněte používat Kontrola pravopisu Bingu REST API a Java pro kontrolu pravopisu a gramatiky.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 05/21/2020
ms.custom: devx-track-java
ms.author: aahi
ms.openlocfilehash: a0a973b7981c97aaf0b13feaecd03158ab6ac284
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93084013"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-java"></a>Rychlý Start: Kontrola pravopisu pomocí Kontrola pravopisu Bingu REST API a Java

> [!WARNING]
> Rozhraní API pro vyhledávání Bingu přesouváte z Cognitive Services na Vyhledávání Bingu služby. Od **30. října 2020** musí být všechny nové instance vyhledávání Bingu zřízené [podle popsaného procesu.](https://aka.ms/cogsvcs/bingmove)
> Rozhraní API pro vyhledávání Bingu zřízené pomocí Cognitive Services budou podporované v následujících třech letech nebo na konci smlouva Enterprise, podle toho, co nastane dřív.
> Pokyny k migraci najdete v tématu [vyhledávání Bingu Services](https://aka.ms/cogsvcs/bingmigration).

V tomto rychlém startu můžete provést první volání REST API Kontrola pravopisu Bingu. Tato jednoduchá aplikace Java pošle požadavek do rozhraní API a vrátí seznam navrhovaných oprav. 

I když je tato aplikace napsaná v jazyce Java, rozhraní API je webová služba RESTful kompatibilní s většinou programovacích jazyků. Zdrojový kód této aplikace je k dispozici na [GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingSpellCheck.java).

## <a name="prerequisites"></a>Předpoklady

* Java Development Kit (JDK) 7 nebo novější.

* Importujte [gson-2.8.5. jar](https://libraries.io/maven/com.google.code.gson%3Agson) nebo nejaktuálnější verzi [gson](https://github.com/google/gson) . Pro spuštění příkazového řádku přidejte `.jar` do složky Java s hlavní třídou.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-an-application"></a>Vytvoření a inicializace aplikace

1. Vytvořte nový projekt Java v oblíbeném integrovaném vývojovém prostředí (IDE) nebo editoru s názvem třídy, který zvolíte, a pak importujte tyto balíčky:

    ```java
    import java.io.*;
    import java.net.*;
    import com.google.gson.*;
    import javax.net.ssl.HttpsURLConnection;
    ```

2. Vytvořte proměnné pro hostitele koncového bodu rozhraní API, cestu a klíč předplatného. Pak vytvořte proměnné pro svůj trh, text, který chcete kontrolovat pravopis, a řetězec pro režim kontroly pravopisu. Můžete použít globální koncový bod v následujícím kódu nebo použít vlastní koncový bod [subdomény](../../../cognitive-services/cognitive-services-custom-subdomains.md) zobrazený v Azure Portal pro váš prostředek.

    ```java
    static String host = "https://api.cognitive.microsoft.com";
    static String path = "/bing/v7.0/spellcheck";

    static String key = "<ENTER-KEY-HERE>";

    static String mkt = "en-US";
    static String mode = "proof";
    static String text = "Hollo, wrld!";
    ```

## <a name="create-and-send-an-api-request"></a>Vytvoření a odeslání žádosti rozhraní API

1. Vytvořte funkci volanou `check()` k vytvoření a odeslání požadavku rozhraní API. V rámci této funkce přidejte kód zadaný v následujících krocích. Vytvořte řetězec pro parametry žádosti:

   1. Přiřaďte svůj kód na trhu k `mkt` parametru pomocí `=` operátoru. 

   1. Přidejte `mode` parametr s `&` operátorem a potom přiřaďte režim kontroly pravopisu. 

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

3. Otevřete připojení k adrese URL. Nastavte metodu Request na `POST` a přidejte parametry žádosti. Nezapomeňte do hlavičky přidat svůj klíč předplatného `Ocp-Apim-Subscription-Key` .

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

1. Přidejte `prettify()` do třídy metodu, která FORMÁTUJE JSON pro čitelnější výstup.

    ``` java
    // This function prettifies the json response.
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }
    ```

1. Vytvořte `BufferedReader` a přečtěte si odpověď z rozhraní API. Vytiskněte ho do konzoly.
    
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

V hlavní funkci aplikace volejte metodu, kterou `check()` jste dříve vytvořili.
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

Sestavte a spusťte projekt. Pokud používáte příkazový řádek, sestavte a spusťte aplikaci pomocí následujících příkazů:

1. Sestavte aplikaci:

   ```bash
   javac -classpath .;gson-2.2.2.jar\* <CLASS_NAME>.java
   ```

2. Spusťte aplikaci:

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
- [Odkaz na rozhraní API Bingu pro kontrolu pravopisu v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
