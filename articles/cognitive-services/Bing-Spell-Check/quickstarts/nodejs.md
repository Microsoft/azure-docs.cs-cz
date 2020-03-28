---
title: 'Úvodní příručka: Kontrola pravopisu pomocí rozhraní REST API a node.js – kontrola pravopisu bingu'
titleSuffix: Azure Cognitive Services
description: Začněte používat rozhraní REST API kontroly pravopisu Bingu ke kontrole pravopisu a gramatiky pomocí tohoto rychlého startu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 12/16/2019
ms.author: aahi
ms.openlocfilehash: 69c391e6c3f93a998ade7c5721a528d895f8df76
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75382846"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-nodejs"></a>Úvodní příručka: Kontrola pravopisu pomocí rozhraní REST API pro kontrolu pravopisu Bingu a souboru Node.js

Pomocí tohoto rychlého startu můžete provést první volání do rozhraní REST API kontroly pravopisu Bingu. Tato jednoduchá aplikace uzlu odešle požadavek do rozhraní API a vrátí seznam slov, která nepoznala, následovaná navrhovanými opravami. Zatímco tato aplikace je napsána v Node.js, rozhraní API je restful webová služba kompatibilní s většinou programovacích jazyků. Zdrojový kód pro tuto aplikaci je k dispozici na [GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingSpellCheckv7.js).

## <a name="prerequisites"></a>Požadavky

* [Node.js 6](https://nodejs.org/en/download/) nebo novější.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>Vytvoření a inicializace projektu

1. Vytvořte nový soubor JavaScript u svého oblíbeného ide nebo editoru. Nastavte přísnost a `https`vyžadovat . Pak vytvořte proměnné pro hostitele, cestu a klíč předplatného koncového bodu rozhraní API. Můžete použít globální koncový bod níže nebo vlastní koncový bod [subdomény](../../../cognitive-services/cognitive-services-custom-subdomains.md) zobrazený na portálu Azure pro váš prostředek.

    ```javascript
    'use strict';
    let https = require ('https');

    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/spellcheck';
    let key = '<ENTER-KEY-HERE>';
    ```

2. Vytvořte proměnné pro parametry vyhledávání a text, který chcete zkontrolovat. Připojit kód trhu `mkt=`za . Kód trhu je země, ze které podáváte žádost. Také připojit režim kontroly pravopisu po `&mode=`. Režim je `proof` buď (zachytí většinu `spell` pravopisných/gramatických chyb) nebo (zachytí většinu pravopisu, ale ne tolik gramatické chyby).

    ```javascript
    let mkt = "en-US";
    let mode = "proof";
    let text = "Hollo, wrld!";
    let query_string = "?mkt=" + mkt + "&mode=" + mode;
    ```

## <a name="create-the-request-parameters"></a>Vytvoření parametrů požadavku

Vytvořte parametry požadavku vytvořením nového objektu `POST` pomocí metody. Přidejte cestu připojením cesty koncového bodu a řetězce dotazu. Přidejte klíč předplatného `Ocp-Apim-Subscription-Key` do záhlaví.

```javascript
let request_params = {
   method : 'POST',
   hostname : host,
   path : path + query_string,
   headers : {
   'Content-Type' : 'application/x-www-form-urlencoded',
   'Content-Length' : text.length + 5,
      'Ocp-Apim-Subscription-Key' : key,
   }
};
```

## <a name="create-a-response-handler"></a>Vytvoření obslužné rutiny odpovědi

Vytvořte funkci `response_handler` volanou tak, aby převzala odpověď JSON z rozhraní API a vytiskla ji. Vytvořte proměnnou pro tělo odezvy. Připojit odpověď při `data` přijetí příznaku `response.on()`pomocí . Po `end` přijetí příznaku vytiskněte tělo JSON do konzoly.

```javascript
let response_handler = function (response) {
    let body = '';
    response.on ('data', function (d) {
        body += d;
    });
    response.on ('end', function () {
        let body_ = JSON.parse (body);
        console.log (body_);
    });
    response.on ('error', function (e) {
        console.log ('Error: ' + e.message);
    });
};
```

## <a name="send-the-request"></a>Odeslat žádost

Volání rozhraní `https.request()` API pomocí parametrů požadavku a obslužné rutiny odpovědi. Napište text do rozhraní API a žádost ukončit později.

```javascript
let req = https.request (request_params, response_handler);
req.write ("text=" + text);
req.end ();
```


## <a name="run-the-application"></a>Spuštění aplikace

Sestavení a spuštění projektu.

Pokud používáte příkazový řádek, vytvořte a spusťte aplikaci pomocí následujících příkazů.

```bash
node <FILE_NAME>.js
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
