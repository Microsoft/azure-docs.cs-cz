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
ms.openlocfilehash: 73c31c7175bd4dfcb182fb76784937c176ac7702
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46977872"
---
# <a name="call-bing-custom-search-endpoint-nodejs"></a>Volání koncového bodu pro vlastní vyhledávání Bingu (Node.js)

Tento rychlý start ukazuje, jak si vyžádat výsledky hledání z vaší instance vlastního hledání použití Node.js k volání koncového bodu pro vlastní vyhledávání Bingu. 

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu je potřeba:

- Instanci vlastního vyhledávání připravené k použití. Zobrazit [vytvořit první instanci vlastního vyhledávání Bingu](quick-start.md).
- [Node.js](https://www.nodejs.org/) nainstalované.
- Klíč předplatného. Klíč předplatného můžete získat, když aktivujete vaše [bezplatnou zkušební verzi](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search), nebo můžete použít klíč placené předplatné z řídicího panelu Azure (naleznete v tématu [účet rozhraní API služeb Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)).    

## <a name="run-the-code"></a>Spuštění kódu

Chcete-li spustit tento příklad, postupujte podle těchto kroků:

1. Vytvořte složku pro váš kód.  
  
2. Z příkazového řádku nebo v terminálu přejděte do složky, kterou jste právě vytvořili.  
  
3. Nainstalujte **požadavek** uzel modulu:
    <pre>
    npm install request
    </pre>  
    
4. Vytvořte soubor s názvem BingCustomSearch.js ve složce, kterou jste vytvořili a zkopírujte do něj následující kód. Nahraďte **YOUR-SUBSCRIPTION-KEY** a **YOUR-vlastní-CONFIG-ID** s klíč předplatného a konfigurací ID.  
  
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
  
6. Spuštění kódu pomocí následujícího příkazu:  
  
    ```    
    node BingCustomSearch.js
    ``` 

## <a name="next-steps"></a>Další postup
- [Konfigurace prostředí uživatelského rozhraní](./hosted-ui.md)
- [Použití dekorace značek zvýraznění textu](./hit-highlighting.md)
- [Stránka webové stránky](./page-webpages.md)
