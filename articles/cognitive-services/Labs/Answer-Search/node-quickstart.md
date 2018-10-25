---
title: 'Rychlý start: Project Answer Search, Node'
description: Začněte používat Project Answer Search s jazykem Node.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: answer-search
ms.topic: quickstart
ms.date: 04/13/2018
ms.author: rosh
ms.openlocfilehash: 1afd029803fc7d2709a9a9abe840db6d7f52498d
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49465742"
---
# <a name="quickstart-project-answer-search-with-node"></a>Rychlý start: Project Answer Search s jazykem Node

Následující příklad kódu Node vytvoří dotaz na informace o Yosemitském národním parku.

## <a name="prerequisites"></a>Požadavky

Získání přístupového klíče k bezplatné zkušební verzi služby [Cognitive Services Labs](https://aka.ms/answersearchsubscription)

Tento příklad používá jazyk Node verze 8.9.4.

## <a name="code-scenario"></a>Scénář kódu 

Následující kód načítá odpovědi.
Implementuje se v následujících krocích:
1. Deklarujte proměnné, které za použití hostitele a cesty určují koncový bod.
2. Zadejte adresu URL dotazu k náhledu a přidejte parametr dotazu.  
3. Vytvořte pro odpověď funkci obslužné rutiny.
4. Definujte funkci vyhledávání, která vytvoří požadavek a přidá hlavičku *Ocp-Apim-Subscription-Key*.
5. Spusťte funkci vyhledávání. 

Následuje celý kód pro tuto ukázku:

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

## <a name="next-steps"></a>Další kroky
- [Ukázkový kód v jazyce C#](c-sharp-quickstart.md)
- [Rychlý start Java](java-quickstart.md)
- [Rychlý start Python](python-quickstart.md)