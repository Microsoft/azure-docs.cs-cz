---
title: Koncový bod volání pomocí Node.js - Bing vlastní vyhledávání - Microsoft kognitivní služby
description: Tento rychlý start ukazuje, jak požádat výsledky hledání instance vlastní vyhledávání pomocí Node.js volat koncový bod služby Bing vlastní vyhledávání.
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 05/07/2018
ms.author: v-brapel
ms.openlocfilehash: 48fc234e15ce3b9172d766f6fae11b51a017ce70
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "35343814"
---
# <a name="call-bing-custom-search-endpoint-nodejs"></a>Volání hledání vlastní Bing koncový bod (Node.js)

Tento rychlý start ukazuje, jak požádat výsledky hledání instance vlastní vyhledávání pomocí Node.js volat koncový bod služby Bing vlastní vyhledávání. 

## <a name="prerequisites"></a>Požadavky
K dokončení tohoto rychlého startu je potřeba:

- Vlastní hledání instance. V tématu [vytvoření vaší první instance Bing vlastní vyhledávání](quick-start.md).

- [Node.js](https://www.nodejs.org/) nainstalována.

-  [Kognitivní účet rozhraní API služby](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s **rozhraní API pro Bing vyhledávání**. [Bezplatnou zkušební verzi](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search) stačí pro tento rychlý start. Je nutné přístupový klíč zadaný při aktivaci bezplatné zkušební verze, nebo může použít klíč placené předplatné z řídicího panelu Azure.

## <a name="run-the-code"></a>Spuštění kódu

Volání hledání vlastní Bing koncový bod, postupujte takto:

1. Vytvořte složku pro váš kód.
2. Z příkazového řádku nebo terminálu přejděte do složky, kterou jste právě vytvořili.
3. Nainstalujte **požadavku** modulu uzlu:
    <pre>
    npm install request
    </pre>
4. Vytvoření souboru BingCustomSearch.js a zkopírujte do ní následující kód.
5. Nahraďte **YOUR-SUBSCRIPTION-KEY** a **YOUR-vlastní-CONFIG-ID** pomocí svého ID klíče a konfigurace (viz krok 1).

    ``` javascript
    var request = require("request");
    
    var subscriptionKey = 'YOUR-SUBSCRIPTION-KEY';
    var customConfigId = 'YOUR-CUSTOM-CONFIG-ID';
    var searchTerm = 'microsoft';
    
    var options = {
        url: 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?' + 
          'q=' + searchTerm + 
          '&customconfig=' + customConfigId,
        headers: {
            'Ocp-Apim-Subscription-Key' : subscriptionKey
        }
    }
    
    request(options, function(error, response, body){
        var searchResponse = JSON.parse(body);
        for(var i = 0; i < searchResponse.webPages.value.length; ++i){
            var webPage = searchResponse.webPages.value[i];
            console.log('name: ' + webPage.name);
            console.log('url: ' + webPage.url);
            console.log('displayUrl: ' + webPage.displayUrl);
            console.log('snippet: ' + webPage.snippet);
            console.log('dateLastCrawled: ' + webPage.dateLastCrawled);
            console.log();
        }
    })
    ```
6. Spuštění kódu pomocí následujícího příkazu.
    ```    
    node BingCustomSearch.js
   ``` 

## <a name="next-steps"></a>Další postup
- [Konfigurace prostředí hostované uživatelského rozhraní](./hosted-ui.md)
- [Použití decoration značky zvýraznění textu](./hit-highlighting.md)
- [Stránka webové stránky](./page-webpages.md)