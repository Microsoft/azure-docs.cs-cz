---
title: Rychlý Start – odeslání dotazu do rozhraní API pomocí Node.js-Bing Local Business Search
titleSuffix: Azure Cognitive Services
description: Tento rychlý Start vám umožní začít odesílat požadavky do rozhraní API Bingu pro vyhledávání v místním obchodu, což je služba pro rozpoznávání Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: quickstart
ms.date: 05/12/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: 03e1b159dbdc26d53af290c4370581788562ff3b
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499624"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-using-nodejs"></a>Rychlý Start: odeslání dotazu do rozhraní API Bingu pro vyhledávání v místním obchodu pomocí Node.js

> [!WARNING]
> Rozhraní API pro vyhledávání Bingu přesouváte z Cognitive Services na Vyhledávání Bingu služby. Od **30. října 2020** musí být všechny nové instance vyhledávání Bingu zřízené [podle popsaného procesu.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)
> Rozhraní API pro vyhledávání Bingu zřízené pomocí Cognitive Services budou podporované v následujících třech letech nebo na konci smlouva Enterprise, podle toho, co nastane dřív.
> Pokyny k migraci najdete v tématu [vyhledávání Bingu Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

V tomto rychlém startu se dozvíte, jak odesílat požadavky do rozhraní API Bingu pro vyhledávání v místním obchodu, což je služba pro rozpoznávání Azure. I když je tato jednoduchá aplikace napsaná v Node.js, rozhraní API je webová služba RESTful, která je kompatibilní s libovolným programovacím jazykem schopným vytvářet požadavky HTTP a analyzovat JSON.

Tato ukázková aplikace získá data místních odpovědí z rozhraní API pro vyhledávací dotaz.

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure – [můžete ho vytvořit zdarma](https://azure.microsoft.com/free/cognitive-services/) .
* Nejnovější verze [Node.js](https://nodejs.org/en/download/)
* [Knihovna požadavků JavaScriptu](https://github.com/request/request)
* Jakmile budete mít předplatné Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title=" vytvořte prostředek vyhledávání Bingu vytvoření prostředku "  target="_blank"> Vyhledávání Bingu <span class="docon docon-navigate-external x-hidden-focus"></span> </a> v Azure Portal, abyste získali svůj klíč a koncový bod. Po nasazení klikněte na **Přejít k prostředku**.


## <a name="code-scenario"></a>Scénář kódu

Následující kód definuje a odešle požadavek, který je implementován v následujících krocích:

1. Deklarujte proměnné, které za použití hostitele a cesty určují koncový bod.
2. Zadejte dotaz a přidejte parametr dotazu.
3. Vytvořte pro odpověď funkci obslužné rutiny.
4. Definujte vyhledávací funkci, která vytvoří požadavek a přidá `Ocp-Apim-Subscription-Key` hlavičku.
5. Spusťte vyhledávací funkci.


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

* [Rychlé zprovoznění hledání v jazyce C# pro místní obchod](local-quickstart.md)
* [Rychlý Start pro místní vyhledávání v jazyce Java](local-search-java-quickstart.md)
* [Rychlý Start pro místní vyhledávání v Pythonu](local-search-python-quickstart.md)