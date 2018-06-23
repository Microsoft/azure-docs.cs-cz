---
title: Rychlý start uzel pro kognitivní služby společnosti Microsoft, vyhledání odpovědí projektu | Microsoft Docs
description: Začněte používat projektu odpovědí hledání, kognitivní služby společnosti Microsoft na platformě Azure.
services: cognitive-services
author: mikedodaro
ms.service: cognitive-services
ms.technology: project-answer-search
ms.topic: article
ms.date: 04/13/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 36b2709d39230aae7929164ba4c9306f57043b43
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343228"
---
# <a name="project-answer-search-node-quickstart"></a>Rychlé spuštění uzlu vyhledávání odpovědí projektu

Následující příklad uzlu vytvoří dotaz na informace o Yosemite národním parku.

## <a name="prerequisites"></a>Požadavky

Získat přístupový klíč pro bezplatné zkušební verze [kognitivní Labs služby](https://aka.ms/answersearchsubscription)

Tento příklad používá v8.9.4 uzlu

## <a name="code-scenario"></a>Scénář kódu 

Následující kód získá odpovědi.
Jsou implementované v následujících krocích:
1. Deklarujte proměnné zadat koncový bod hostitele a cestu.
2. Zadejte adresu URL dotazu si verzi preview a přidat parametr dotazu.  
3. Vytvořte obslužnou rutinu pro odpověď.
4. Definování funkce vyhledávání, která vytvoří žádost a přidá *Ocp-Apim-Subscription-Key* záhlaví.
5. Spusťte funkci hledání. 

Kód dokončení v této ukázce takto:

````
'use strict';

let https = require('https');

// Replace the subscriptionKey string value with your valid subscription key.
let subscriptionKey = 'YOUR-SUBSCRIPTION-KEY'; 

let host = 'api.labs.cognitive.microsoft.com';
let path = '/answerSearch/v7.0/search';

let mkt = 'en-us';
let q = 'Yosemite National Park';

let params = '?q=' + encodeURI(q) + '&mkt=en-us';

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
- [Příklad kódu C#](c-sharp-quickstart.md)
- [Rychlý start Java](java-quickstart.md)
- [Rychlý start Python](python-quickstart.md)