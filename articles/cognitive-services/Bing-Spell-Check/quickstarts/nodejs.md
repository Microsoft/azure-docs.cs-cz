---
title: 'Rychlý start: Rozhraní API pro kontrolu pravopisu Bingu, Node.js'
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
ms.openlocfilehash: 2d9354d82b86cc3a33a57dc4745c63e688e7959d
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48800787"
---
# <a name="quickstart-for-bing-spell-check-api-with-nodejs"></a>Rychlý start pro rozhraní API pro kontrolu pravopisu Bingu s využitím Node.js 

V tomto článku se dozvíte, jak používat [rozhraní API pro kontrolu pravopisu Bingu](https://azure.microsoft.com/services/cognitive-services/spell-check/) společně s Node.js. Rozhraní API pro kontrolu pravopisu vrací seznam slov, která nerozpozná, a jejich navrhované náhrady. Většinou odešlete do tohoto rozhraní API text a potom buď provedete v textu navrhovaná nahrazení, nebo návrhy ukážete uživateli vaší aplikace, aby se mohl rozhodnout, zda chcete nahrazení provést. V tomto článku se dozvíte, jak odeslat požadavek obsahující text „Hollo, wrld!“. Navrhované náhrady budou „Hello“ a „world“.

## <a name="prerequisites"></a>Požadavky

Abyste mohli tento kód spustit, budete potřebovat [Node.js 6](https://nodejs.org/en/download/).

Potřebujete [účet rozhraní API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s **rozhraním API pro kontrolu pravopisu Bingu v7**. Pro účely tohoto rychlého startu stačí [bezplatná zkušební verze](https://azure.microsoft.com/try/cognitive-services/#lang). Při aktivaci bezplatné zkušební verze budete potřebovat poskytnutý přístupový klíč nebo můžete použít klíč placeného předplatného z řídicího panelu Azure.

## <a name="get-spell-check-results"></a>Získání výsledků kontroly pravopisu

1. Ve svém oblíbeném integrovaném vývojovém prostředí vytvořte nový projekt Node.js.
2. Přidejte níže uvedený kód.
3. Hodnotu `subscriptionKey` nahraďte přístupovým klíčem platným pro vaše předplatné.
4. Spusťte program.

```nodejs
'use strict';

let https = require ('https');

let host = 'api.cognitive.microsoft.com';
let path = '/bing/v7.0/spellcheck';

/* NOTE: Replace this example key with a valid subscription key (see the Prequisites section above). Also note v5 and v7 require separate subscription keys. */
let key = 'ENTER KEY HERE';

// These values are used for optional headers (see below).
// let CLIENT_ID = "<Client ID from Previous Response Goes Here>";
// let CLIENT_IP = "999.999.999.999";
// let CLIENT_LOCATION = "+90.0000000000000;long: 00.0000000000000;re:100.000000000000";

let mkt = "en-US";
let mode = "proof";
let text = "Hollo, wrld!";
let query_string = "?mkt=" + mkt + "&mode=" + mode;

let request_params = {
    method : 'POST',
    hostname : host,
    path : path + query_string,
    headers : {
        'Content-Type' : 'application/x-www-form-urlencoded',
        'Content-Length' : text.length + 5,
        'Ocp-Apim-Subscription-Key' : key,
//        'X-Search-Location' : CLIENT_LOCATION,
//        'X-MSEdge-ClientID' : CLIENT_ID,
//        'X-MSEdge-ClientIP' : CLIENT_ID,
    }
};

let response_handler = function (response) {
    let body = '';
    response.on ('data', function (d) {
        body += d;
    });
    response.on ('end', function () {
        console.log (body);
    });
    response.on ('error', function (e) {
        console.log ('Error: ' + e.message);
    });
};

let req = https.request (request_params, response_handler);
req.write ("text=" + text);
req.end ();
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
> [Kurz o kontrole pravopisu Bingu](../tutorials/spellcheck.md)

## <a name="see-also"></a>Viz také

- [Přehled kontroly pravopisu Bingu](../proof-text.md)
- [Referenční informace k rozhraní API pro kontrolu pravopisu Bingu v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference)
