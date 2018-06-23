---
title: Rychlý úvodní kurz Node.js pro náhled adresy URL projektu - kognitivní služby Microsoft | Microsoft Docs
description: Začněte používat ve verzi Preview adresy URL v kognitivní služby společnosti Microsoft na platformě Azure.
services: cognitive-services
author: mikedodaro
ms.service: cognitive-services
ms.technology: project-url-preview
ms.topic: article
ms.date: 03/16/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 195033d2740b11873baae095cec028dc8d19ce49
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343241"
---
# <a name="url-preview-node-quickstart"></a>Adresa URL Preview uzlu rychlý start

Následující příklad uzlu vytvoří Náhled adresy Url pro web SwiftKey: https://swiftkey.com/en.

## <a name="prerequisites"></a>Požadavky

Získat přístupový klíč pro bezplatné zkušební verze [kognitivní Labs služby](https://aka.ms/answersearchsubscription)

## <a name="code-scenario"></a>Scénář kódu 

Následující kód získá adresu URL náhledu dat.
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
let subscriptionKey = 'YOUR-ACCESS-KEY'; 
let host = 'api.labs.cognitive.microsoft.com';
let path = '/urlpreview/v7.0/search';

let mkt = 'en-US';
let q = 'https://swiftkey.com/';

let params = '?q=' + encodeURI(q);

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
- [Příklad kódu C#](csharp.md)
- [Rychlý start Java](java-quickstart.md)
- [Rychlý start JavaScript](javascript.md)
- [Rychlý start Python](python-quickstart.md)