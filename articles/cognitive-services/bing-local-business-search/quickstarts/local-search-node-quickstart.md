---
title: Rychlý start – odeslat dotaz Bingu místní firmy hledání rozhraní API pomocí Node.js | Dokumentace Microsoftu
titleSuffix: Azure Cognitive Services
description: Začněte používat API Bingu pro místní obchodní vyhledávání v uzlu.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-local-business
ms.topic: article
ms.date: 11/01/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 571ac9f593e51d66a905f34be9ead2f42753635b
ms.sourcegitcommit: 6678e16c4b273acd3eaf45af310de77090137fa1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2018
ms.locfileid: "50748631"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-using-nodejs"></a>Rychlý start: Odeslání dotaz Bingu místní firmy hledání rozhraní API pomocí Node.js

V tomto rychlém startu můžete zahájit odesílání požadavků na místní firmy hledání rozhraní API Bingu, což je kognitivní služby Azure. Když je tato jednoduchá aplikace napsané v Node.js, je rozhraní API RESTful webová služba, která je kompatibilní s libovolný programovací jazyk umožňuje zasílat požadavky HTTP a parsování formátu JSON.
 
Tato ukázková aplikace získá data místní odpověď z rozhraní API pro vyhledávací dotaz `hotel in Bellevue`.

## <a name="prerequisites"></a>Požadavky

* Nejnovější verze [Node.js](https://nodejs.org/en/download/).

* [Knihovna žádostí o jazyka JavaScript](https://github.com/request/request)

Musíte mít [účet rozhraní API služeb Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s rozhraními API pro Bing. Pro účely tohoto rychlého startu stačí [bezplatná zkušební verze](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api). Použijte přístupový klíč poskytuje bezplatná zkušební verze.

##<a name="code-scenario"></a>Scénář kódu
Následující kód získá definuje a odešle žádost. Implementuje se v následujících krocích:

1. Deklarujte proměnné, které za použití hostitele a cesty určují koncový bod.
2. Zadejte dotaz a přidat parametr dotazu. 
3. Vytvořte pro odpověď funkci obslužné rutiny.
4. Definujte funkci vyhledávání, který vytvoří žádost a přidá záhlaví Ocp-Apim-Subscription-Key.
5. Spusťte funkci vyhledávání. 

Následuje celý kód pro tuto ukázku:

````
'use strict';

let https = require('https');

// Replace the subscriptionKey string value with your valid subscription key.
let subscriptionKey = 'your-access-key';

let host = 'www.bingapis.com';
let path = '/api/v7/localbusinesses/search';

let mkt = 'en-US';
let q = 'hotel in Bellevue';

let params = '?q=' + encodeURI(q) + "&appid=" + accessKey + "&traffictype=Internal_monitor&mkt=" + mkt;

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

````

## <a name="next-steps"></a>Další postup
- [Rychlý start místní firmy vyhledávání](local-quickstart.md)
- [Rychlý start místní firmy hledání Java](local-search-java-quickstart.md)
- [Rychlý start místní firmy hledání Pythonu](local-search-python-quickstart.md)
