---
title: 'Rychlý start: Rozhraní API pro kontrolu pravopisu Bingu, Java'
titlesuffix: Azure Cognitive Services
description: Umožňuje získat informace a ukázky kódu, které vám pomůžou rychle začít používat rozhraní API pro kontrolu pravopisu Bingu.
services: cognitive-services
author: v-jaswel
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-spell-check
ms.topic: quickstart
ms.date: 09/14/2017
ms.author: v-jaswel
ms.openlocfilehash: 20677167c88b4e3cf326067fac7a33bede9ee4c3
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2018
ms.locfileid: "50420335"
---
# <a name="quickstart-for-bing-spell-check-api-with-java"></a>Rychlý start pro rozhraní API pro kontrolu pravopisu Bingu s využitím Javy 

V tomto článku se dozvíte, jak používat [rozhraní API pro kontrolu pravopisu Bingu](https://azure.microsoft.com/services/cognitive-services/spell-check/) s využitím Javy. Rozhraní API pro kontrolu pravopisu vrací seznam slov, která nerozpozná, a jejich navrhované náhrady. Většinou odešlete do tohoto rozhraní API text a potom buď provedete v textu navrhovaná nahrazení, nebo návrhy ukážete uživateli vaší aplikace, aby se mohl rozhodnout, zda chcete nahrazení provést. V tomto článku se dozvíte, jak odeslat požadavek obsahující text „Hollo, wrld!“. Navrhované náhrady budou „Hello“ a „world“.

## <a name="prerequisites"></a>Požadavky

Pro kompilaci a spuštění tohoto kódu budete potřebovat [JDK 7 nebo 8](https://aka.ms/azure-jdks). Můžete použít prostředí Java IDE, pokud je vaše oblíbené, ale stačit bude i textový editor.

Potřebujete [účet rozhraní API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s **rozhraním API pro kontrolu pravopisu Bingu v7**. Pro účely tohoto rychlého startu stačí [bezplatná zkušební verze](https://azure.microsoft.com/try/cognitive-services/#lang). Při aktivaci bezplatné zkušební verze budete potřebovat poskytnutý přístupový klíč nebo můžete použít klíč placeného předplatného z řídicího panelu Azure.

## <a name="get-spell-check-results"></a>Získání výsledků kontroly pravopisu

1. Ve svém oblíbeném integrovaném vývojovém prostředí vytvořte nový projekt Javy.
2. Přidejte níže uvedený kód.
3. Hodnotu `subscriptionKey` nahraďte přístupovým klíčem platným pro vaše předplatné.
4. Spusťte program.

```java
import java.io.*;
import java.net.*;
import javax.net.ssl.HttpsURLConnection;

public class HelloWorld {

    static String host = "https://api.cognitive.microsoft.com";
    static String path = "/bing/v7.0/spellcheck";

    // NOTE: Replace this example key with a valid subscription key.
    static String key = "ENTER KEY HERE";

    static String mkt = "en-US";
    static String mode = "proof";
    static String text = "Hollo, wrld!";

    public static void check () throws Exception {
        String params = "?mkt=" + mkt + "&mode=" + mode;
        URL url = new URL(host + path + params);
        HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
        connection.setRequestMethod("POST");
        connection.setRequestProperty("Content-Type", "application/x-www-form-urlencoded");
        connection.setRequestProperty("Content-Length", "" + text.length() + 5);
        connection.setRequestProperty("Ocp-Apim-Subscription-Key", key);
        connection.setDoOutput(true);

        DataOutputStream wr = new DataOutputStream(connection.getOutputStream());
        wr.writeBytes("text=" + text);
        wr.flush();
        wr.close();

        BufferedReader in = new BufferedReader(
        new InputStreamReader(connection.getInputStream()));
        String line;
        while ((line = in.readLine()) != null) {
            System.out.println(line);
        }
        in.close();
    }

    public static void main(String[] args) {
        try {
            check ();
        }
        catch (Exception e) {
            System.out.println (e);
        }
    }
}
```

**Odpověď**

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
> [Kurz rozhraní API pro kontrolu pravopisu Bingu](../tutorials/spellcheck.md)

## <a name="see-also"></a>Viz také

- [Přehled rozhraní API pro kontrolu pravopisu Bingu](../proof-text.md)
- [Referenční informace k rozhraní API pro kontrolu pravopisu Bingu v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference)
