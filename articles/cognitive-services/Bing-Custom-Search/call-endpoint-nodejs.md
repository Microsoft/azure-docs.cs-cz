---
title: Volání koncového bodu s využitím Node.js – vlastní vyhledávání Bingu – Microsoft Cognitive Services
description: Tento rychlý start ukazuje, jak si vyžádat výsledky hledání s použitím prostředí Node.js k volání koncového bodu pro vlastní vyhledávání Bingu z vaší instance vlastního hledání.
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 05/07/2018
ms.author: v-brapel
ms.openlocfilehash: 5d9391cc486dc868a1a291ccc7095291cddd3e4c
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2018
ms.locfileid: "37858455"
---
# <a name="call-bing-custom-search-endpoint-nodejs"></a>Volání koncového bodu pro vlastní vyhledávání Bingu (Node.js)

Tento rychlý start ukazuje, jak si vyžádat výsledky hledání s použitím prostředí Node.js k volání koncového bodu pro vlastní vyhledávání Bingu z vaší instance vlastního hledání. 

## <a name="prerequisites"></a>Požadavky
K dokončení tohoto rychlého startu je potřeba:

- Instanci vlastního vyhledávání. Zobrazit [vytvořit první instanci vlastního vyhledávání Bingu](quick-start.md).

- [Node.js](https://www.nodejs.org/) nainstalované.

-  [Účet služby cognitive Services API](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s **rozhraní API Bingu pro vyhledávání**. [Bezplatnou zkušební verzi](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search) stačí pro účely tohoto rychlého startu. Budete potřebovat přístupový klíč získáte při aktivaci vaší bezplatné zkušební verze, nebo můžete použít klíč placené předplatné z řídicího panelu Azure.

## <a name="run-the-code"></a>Spuštění kódu

K volání koncového bodu vlastní vyhledávání Bingu, postupujte podle těchto kroků:

1. Vytvořte složku pro váš kód.

2. Z příkazového řádku nebo v terminálu přejděte do složky, kterou jste právě vytvořili.

3. Nainstalujte **požadavek** uzel modulu:
    <pre>
    npm install request
    </pre>
    
4. Vytvořte soubor BingCustomSearch.js a zkopírujte do něj následující kód.

5. Nahraďte **YOUR-SUBSCRIPTION-KEY** a **YOUR-vlastní-CONFIG-ID** identifikátorem klíče a konfigurace (viz krok 1).

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
- [Konfigurace prostředí uživatelského rozhraní](./hosted-ui.md)
- [Použití dekorace značek zvýraznění textu](./hit-highlighting.md)
- [Stránka webové stránky](./page-webpages.md)
