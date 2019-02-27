---
title: 'Rychlý start: Kontrola pravopisu pomocí rozhraní REST API kontrolu pravopisu Bingu a Node.js'
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
ms.openlocfilehash: 8e3379a086eb09745142f4e3997ed195eb4d1de5
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2019
ms.locfileid: "56885903"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-nodejs"></a>Rychlý start: Kontrola pravopisu pomocí rozhraní REST API kontrolu pravopisu Bingu a Node.js

V tomto rychlém startu můžete provést první volání do rozhraní API Bingu pro kontrolu pravopisu zkontrolujte REST. Tento jednoduchý Python aplikace odešle požadavek na rozhraní API a vrátí seznam slov, který nerozpozná, za nímž následuje navrhovaných oprav. Aplikace je sice napsaná v Pythonu, ale rozhraní API je webová služba RESTful kompatibilní s většinou programovacích jazyků. Zdrojový kód pro tuto aplikaci je k dispozici na [Githubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingSpellCheckv7.js).

## <a name="prerequisites"></a>Požadavky

* [Node.js 6](https://nodejs.org/en/download/) nebo novější.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>Vytvoření a inicializace projektu

1. Vytvořte nový soubor jazyka JavaScript v Oblíbené prostředí IDE nebo editoru. Nastavte přísnosti a vyžadují protokol https. Potom vytvořte proměnné pro váš koncový bod rozhraní API hostitele, cestu a váš klíč předplatného.

    ```javascript
    'use strict';
    let https = require ('https');
    
    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/spellcheck';
    let key = 'ENTER KEY HERE';
    ```

2. Vytváření proměnných pro vámi trh, režimu kontroly pravopisu a text, který chcete zkontrolovat. Pak vytvořte řetězec, který se připojí `?mkt=` parametr uvedení na trh, a `&mode=` na vašem režimu.

    ```javascript
    let mkt = "en-US";
    let mode = "proof";
    let text = "Hollo, wrld!";
    let query_string = "?mkt=" + mkt + "&mode=" + mode;
    ```

## <a name="create-the-request-parameters"></a>Vytvořit žádost o parametry

Vytvořit tak, že vytvoříte nový objekt s parametry požadavku `POST` metoda. Přidáte cestu k přidáním cesta ke koncovému bodu a řetězce dotazu. Přidat klíč předplatného. Chcete `Ocp-Apim-Subscription-Key` záhlaví.

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

Vytvořit funkci s názvem `response_handler` využít odpověď JSON z rozhraní API a vytisknout. Vytvořte proměnnou pro text odpovědi. Přidat odpověď při `data` přijatých příznak pomocí `response.on()`. Když `end` přijetí příznak tisk text JSON do konzoly.

```javascript
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
```

## <a name="send-the-request"></a>Odeslání požadavku

Volání rozhraní API pomocí `https.request()` s parametry požadavku a obslužné rutiny. Zápis textu do rozhraní API a end žádost později.

```javascript
let req = https.request (request_params, response_handler);
req.write ("text=" + text);
req.end ();
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
> [Vytvoření webové jednostránkové – aplikace](../tutorials/spellcheck.md)

- [Co je API kontrola pravopisu Bingu?](../overview.md)
- [Referenční informace k rozhraní API pro kontrolu pravopisu Bingu v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference)
