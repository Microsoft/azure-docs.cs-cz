---
title: Úvodní příručka – Odeslání dotazu do rozhraní API pomocí souboru Node.js – vyhledávání místních obchodních společností Bing
titleSuffix: Azure Cognitive Services
description: Tento rychlý start můžete začít odesílat požadavky do rozhraní API pro vyhledávání místních společností Bingu, což je služba Azure Cognitive Service.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: quickstart
ms.date: 11/29/2019
ms.author: aahi
ms.openlocfilehash: beab594126ce292ea1fc47e399a12274dbb31aa3
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74665691"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-using-nodejs"></a>Úvodní příručka: Odeslání dotazu do rozhraní API pro vyhledávání místních společností Bingu pomocí souboru Node.js

Tento rychlý start můžete začít odesílat požadavky do rozhraní API pro vyhledávání místních společností Bingu, což je služba Azure Cognitive Service. Zatímco tato jednoduchá aplikace je napsána v Node.js, rozhraní API je restful webová služba kompatibilní s libovolným programovacím jazykem schopným provádět požadavky HTTP a analyzovat JSON.

Tato ukázková aplikace získá data místní odpovědi `hotel in Bellevue`z rozhraní API pro vyhledávací dotaz .

## <a name="prerequisites"></a>Požadavky

* Nejnovější verze [Node.js](https://nodejs.org/en/download/)

* [Knihovna žádostí JavaScriptu](https://github.com/request/request)

Musíte mít [účet rozhraní API služby Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s rozhraními API Bing. Pro účely tohoto rychlého startu vám bude stačit [bezplatná zkušební verze](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api). Použijte přístupový klíč poskytnutý bezplatnou zkušební verzí.  Viz také [Cognitive Services Pricing - Bing Search API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="code-scenario"></a>Scénář kódu

Následující kód získá definuje a odešle požadavek. Implementuje se v následujících krocích:

1. Deklarujte proměnné, které za použití hostitele a cesty určují koncový bod.
2. Zadejte dotaz a přidejte parametr dotazu.
3. Vytvořte pro odpověď funkci obslužné rutiny.
4. Definujte funkci vyhledávání, která vytvoří požadavek a přidá hlavičku Ocp-Apim-Subscription-Key.
5. Spusťte funkci vyhledávání.

Následuje celý kód pro tuto ukázku:

```javascript
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

## <a name="next-steps"></a>Další kroky

* [Rychlý start hledání místní firmy](local-quickstart.md)
* [Rychlý start java vyhledávání místních společností](local-search-java-quickstart.md)
* [Rychlý start pythonu pro místní obchodní vyhledávání](local-search-python-quickstart.md)
