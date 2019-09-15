---
title: Rychlý Start – odeslání dotazu do rozhraní API pro místní vyhledávání Bingu pomocí Node. js
titleSuffix: Azure Cognitive Services
description: Začněte používat rozhraní API pro vyhledávání místních obchodních aplikací Bingu v uzlu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: quickstart
ms.date: 09/13/2019
ms.author: aahi
ms.openlocfilehash: 02513d0596588b4e6ba05edf5342769e78c24242
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996659"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-using-nodejs"></a>Rychlý start: Odeslání dotazu do rozhraní API Bingu pro vyhledávání v místním obchodu pomocí Node. js

Tento rychlý Start vám umožní začít odesílat požadavky do rozhraní API Bingu pro vyhledávání v místním obchodu, což je služba pro rozpoznávání Azure. I když je tato jednoduchá aplikace napsaná v Node. js, je rozhraní API webová služba RESTful kompatibilní s jakýmkoli programovacím jazykem schopným vytvářet požadavky HTTP a analyzovat JSON.

Tato ukázková aplikace získá data místní odpovědi z rozhraní API vyhledávacího dotazu `hotel in Bellevue`.

## <a name="prerequisites"></a>Požadavky

* Nejnovější verze [Node.js](https://nodejs.org/en/download/).

* [Knihovna požadavků JavaScriptu](https://github.com/request/request)

Musíte mít [Cognitive Services účet rozhraní API](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s rozhraními API Bingu. Pro účely tohoto rychlého startu stačí [bezplatná zkušební verze](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api). Použijte přístupový klíč, který je k dispozici v bezplatné zkušební verzi.  Viz také [služeb Cognitive Services ceny – rozhraní API Bingu pro vyhledávání](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="code-scenario"></a>Scénář kódu

Následující kód získá definici a odešle požadavek. Implementuje se v následujících krocích:

1. Deklarujte proměnné, které za použití hostitele a cesty určují koncový bod.
2. Zadejte dotaz a přidejte parametr dotazu.
3. Vytvořte pro odpověď funkci obslužné rutiny.
4. Zadejte vyhledávací funkci, která vytvoří požadavek a přidá hlavičku OCP-APIM-Subscription-Key.
5. Spusťte funkci vyhledávání.

Následuje celý kód pro tuto ukázku:

```
'use strict';

let https = require('https');

// Replace the subscriptionKey string value with your valid subscription key.
let subscriptionKey = 'your-access-key';

let host = 'api.cognitive.microsoft.com/bing';
let path = '/v7.0/localbusinesses/search';

let mkt = 'en-US';
let q = 'hotel in Bellevue';

let params = '?q=' + encodeURI(q) + "&mkt=" + mkt;

let response_handler = function (response) {
    let body = '';
    response.on('data', function (d) {
        body += d;
    });
    response.on('end', function () {
        let body_ = JSON.parse(body);
        let body__ = JSON.stringify(body_, null, '  ');
        console.log(body__);
    });
    response.on('error', function (e) {
        console.log('Error: ' + e.message);
    });
};

let Search = function () {
    let request_params = {
        method: 'GET',
        hostname: host,
        path: path + params,
        headers: {
            'Ocp-Apim-Subscription-Key': subscriptionKey,
        }
    };

    let req = https.request(request_params, response_handler);
    req.end();
}

Search();

```

## <a name="next-steps"></a>Další postup

* [Rychlý start hledání místních obchodních obchodů](local-quickstart.md)
* [Rychlý Start pro místní vyhledávání v jazyce Java](local-search-java-quickstart.md)
* [Rychlý Start pro místní vyhledávání v Pythonu](local-search-python-quickstart.md)
