---
title: 'Rychlý start: Volání vlastního vyhledávání Bingu koncový bod pomocí Node.js | Dokumentace Microsoftu'
titlesuffix: Azure Cognitive Services
description: V tomto rychlém startu můžete začít si vyžádat výsledky hledání od vaší instance vlastního vyhledávání Bingu pomocí Node.js
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 05/07/2018
ms.author: aahi
ms.openlocfilehash: 81746f1c1516bd59cbfb917dcaefa00195558d05
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55875446"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-nodejs"></a>Rychlý start: Volání vlastního vyhledávání Bingu koncový bod pomocí Node.js

V tomto rychlém startu můžete začít si vyžádat výsledky hledání od vaší instance vlastního vyhledávání Bingu. Zatímco tato aplikace je napsána v jazyce JavaScript, rozhraní API pro vlastní vyhledávání Bingu je kompatibilní s Většina programovacích jazyků rozhraní RESTful webová služba. Zdrojový kód k této ukázce je dostupný na [Githubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingCustomSearchv7.js).

## <a name="prerequisites"></a>Požadavky

- Instanci vlastního vyhledávání Bingu. Zobrazit [rychlý start: Vytvoření první instanci vlastního vyhledávání Bingu](quick-start.md) Další informace.

- [Node.js](https://www.nodejs.org/)

- [Knihovna žádostí o jazyka JavaScript](https://github.com/request/request)

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Vytvoření a inicializace aplikace

1. Vytvořte nový soubor JavaScript ve vašich oblíbených prostředím IDE nebo editorem a přidejte `require()` příkaz pro knihovnu požadavky. Vytváření proměnných pro váš klíč předplatného, ID konfigurace vlastní a hledaný termín. 

    ```javascript
    var request = require("request");
    
    var subscriptionKey = 'YOUR-SUBSCRIPTION-KEY';
    var customConfigId = 'YOUR-CUSTOM-CONFIG-ID';
    var searchTerm = 'microsoft';
    ```

## <a name="send-and-receive-a-search-request"></a>Odeslat a přijmout žádost o vyhledávání 

1. Vytvoření proměnné k ukládání informací odesílaných ve vaší žádosti. Vytvořit žádost o adresu URL připojením hledaný termín `q=` parametr dotazu a search instance vlastní ID konfigurace `customconfig=`. oddělení parametrů s `&` znak. 

    ```javascript
    var info = {
        url: 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?' + 
            'q=' + searchTerm + "&" +
            'customconfig=' + customConfigId,
        headers: {
            'Ocp-Apim-Subscription-Key' : subscriptionKey
        }
    }
    ```

1. Použijte knihovnu JavaScript žádost odeslat žádost o vyhledávání vaší instance vlastního vyhledávání Bingu a vytiskne informace o výsledcích, včetně názvu, adresu url a datum, že byl naposledy procházen webovou stránku.

    ```javascript
    request(info, function(error, response, body){
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
    ```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Sestavení webové aplikace s vlastní vyhledávání](./tutorials/custom-search-web-page.md)
