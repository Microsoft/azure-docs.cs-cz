---
title: 'Rychlý start: Kontrola pravopisu pomocí Kontrola pravopisu Bingu REST API a Node. js'
titleSuffix: Azure Cognitive Services
description: Začněte používat REST API Kontrola pravopisu Bingu pro kontrolu pravopisu a gramatiky.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 04/02/2019
ms.author: aahill
ms.openlocfilehash: c45e9e7743aca2d091b62caed6a7c5a724bacebf
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423499"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-nodejs"></a>Rychlý start: Kontrola pravopisu pomocí Kontrola pravopisu Bingu REST API a Node. js

V tomto rychlém startu můžete provést první volání REST API Kontrola pravopisu Bingu. Tato jednoduchá aplikace uzlů pošle požadavek do rozhraní API a vrátí seznam slov, která nerozpoznala, a potom doporučované opravy. I když je tato aplikace napsaná v Node. js, je rozhraní API webová služba RESTful kompatibilní s většinou programovacích jazyků. Zdrojový kód této aplikace je k dispozici na [GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingSpellCheckv7.js).

## <a name="prerequisites"></a>Požadavky

* [Node. js 6](https://nodejs.org/en/download/) nebo novější.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>Vytvoření a inicializace projektu

1. Vytvořte nový soubor JavaScriptu v oblíbeném integrovaném vývojovém prostředí (IDE) nebo editoru. Nastavte striktní a vyžadovat `https`. Pak vytvořte proměnné pro hostitele, cestu a klíč předplatného koncového bodu rozhraní API.

    ```javascript
    'use strict';
    let https = require ('https');

    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/spellcheck';
    let key = '<ENTER-KEY-HERE>';
    ```

2. Vytvořte proměnné pro parametry vyhledávání a text, který chcete kontrolovat. Přidejte svůj kód na trhu `mkt=`po. Kód na trhu je země, ze které provedete požadavek. Také přidejte svůj režim kontroly pravopisu po `&mode=`. Režim je buď `proof` (catch nejvíc pravopisné/gramatické chyby `spell` ), nebo (catch nejvíc pravopisu, ale ne tolik gramatických chyb).

    ```javascript
    let mkt = "en-US";
    let mode = "proof";
    let text = "Hollo, wrld!";
    let query_string = "?mkt=" + mkt + "&mode=" + mode;
    ```

## <a name="create-the-request-parameters"></a>Vytvoření parametrů žádosti

Vytvořte parametry žádosti vytvořením nového objektu s `POST` metodou. Přidejte cestu tak, že připojíte cestu ke koncovému bodu a řetězec dotazu. Přidejte do `Ocp-Apim-Subscription-Key` záhlaví klíč předplatného.

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

Vytvořte funkci volanou `response_handler` k přijetí odpovědi JSON z rozhraní API a vytiskněte ji. Vytvořte proměnnou pro tělo odpovědi. Připojit odpověď, když `data` je přijat příznak, pomocí. `response.on()` Když se přijme příznak, vytiskněte tělo JSON do konzoly. `end`

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

Zavolejte rozhraní API pomocí `https.request()` parametrů s parametry žádosti a obslužnou rutinou odpovědi. Napište svůj text do rozhraní API a potom tento požadavek ukončete.

```javascript
let req = https.request (request_params, response_handler);
req.write ("text=" + text);
req.end ();
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
> [Vytvoření webové aplikace na jedné stránce](../tutorials/spellcheck.md)

- [Co je rozhraní API Bingu pro kontrolu pravopisu?](../overview.md)
- [Referenční informace k rozhraní API pro kontrolu pravopisu Bingu v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
