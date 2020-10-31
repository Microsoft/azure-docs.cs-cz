---
title: 'Rychlý Start: Kontrola pravopisu pomocí REST API a Node.js-Kontrola pravopisu Bingu'
titleSuffix: Azure Cognitive Services
description: Začněte používat REST API Kontrola pravopisu Bingu a Node.js kontrolu pravopisu a gramatiky.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 05/21/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: 1a9756277faf98939ad609231f46bb2f2c04b8b6
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93083996"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-nodejs"></a>Rychlý Start: Kontrola pravopisu pomocí Kontrola pravopisu Bingu REST API a Node.js

> [!WARNING]
> Rozhraní API pro vyhledávání Bingu přesouváte z Cognitive Services na Vyhledávání Bingu služby. Od **30. října 2020** musí být všechny nové instance vyhledávání Bingu zřízené [podle popsaného procesu.](https://aka.ms/cogsvcs/bingmove)
> Rozhraní API pro vyhledávání Bingu zřízené pomocí Cognitive Services budou podporované v následujících třech letech nebo na konci smlouva Enterprise, podle toho, co nastane dřív.
> Pokyny k migraci najdete v tématu [vyhledávání Bingu Services](https://aka.ms/cogsvcs/bingmigration).

V tomto rychlém startu můžete provést první volání REST API Kontrola pravopisu Bingu. Tato jednoduchá aplikace JavaScriptu pošle požadavek do rozhraní API a vrátí seznam navrhovaných oprav. 

I když je tato aplikace napsaná v JavaScriptu, rozhraní API je webová služba RESTful kompatibilní s většinou programovacích jazyků. Zdrojový kód této aplikace je k dispozici na [GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingSpellCheckv7.js).

## <a name="prerequisites"></a>Předpoklady

* [Node.js 6](https://nodejs.org/en/download/) nebo novější.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>Vytvoření a inicializace projektu

1. Vytvořte nový soubor JavaScriptu v oblíbeném integrovaném vývojovém prostředí (IDE) nebo editoru. Nastavte striktní a vyžadovat `https` . Pak vytvořte proměnné pro hostitele, cestu a klíč předplatného koncového bodu rozhraní API. Můžete použít globální koncový bod v následujícím kódu nebo použít vlastní koncový bod [subdomény](../../../cognitive-services/cognitive-services-custom-subdomains.md) zobrazený v Azure Portal pro váš prostředek.

    ```javascript
    'use strict';
    let https = require ('https');

    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/spellcheck';
    let key = '<ENTER-KEY-HERE>';
    ```

2. Vytvořte proměnné pro parametry vyhledávání a text, který chcete ověřit: 

   1. Přiřaďte svůj kód na trhu k `mkt` parametru pomocí `=` operátoru. Kód trhu je kód země nebo oblasti, ze které provedete požadavek. 

   1. Přidejte `mode` parametr s `&` operátorem a potom přiřaďte režim kontroly pravopisu. Režim může být buď `proof` (catch nejvíc pravopisné/gramatické chyby) `spell` , nebo (catch nejvíc pravopisných chyb, ale ne tolik gramatických chyb).

    ```javascript
    let mkt = "en-US";
    let mode = "proof";
    let text = "Hollo, wrld!";
    let query_string = "?mkt=" + mkt + "&mode=" + mode;
    ```

## <a name="create-the-request-parameters"></a>Vytvoření parametrů žádosti

Vytvořte parametry žádosti vytvořením nového objektu s `POST` metodou. Přidejte cestu tak, že připojíte cestu ke koncovému bodu a řetězec dotazu. Pak do hlavičky přidejte svůj klíč předplatného `Ocp-Apim-Subscription-Key` .

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

Vytvořte funkci volanou `response_handler` k přijetí odpovědi JSON z rozhraní API a vytiskněte ji. Vytvořte proměnnou pro tělo odpovědi. Připojit odpověď, pokud `data` je příznak přijat pomocí `response.on()` . Po `end` přijetí příznaku vytiskněte tělo JSON do konzoly.

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

Zavolejte rozhraní API pomocí `https.request()` s parametry žádosti a obslužnou rutinou odpovědi. Napište svůj text do rozhraní API a pak ukončete žádost.

```javascript
let req = https.request (request_params, response_handler);
req.write ("text=" + text);
req.end ();
```


## <a name="run-the-application"></a>Spuštění aplikace

1. Sestavte a spusťte projekt.

1. Pokud používáte příkazový řádek, pomocí následujícího příkazu Sestavte a spusťte aplikaci:

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
- [Odkaz na rozhraní API Bingu pro kontrolu pravopisu v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
