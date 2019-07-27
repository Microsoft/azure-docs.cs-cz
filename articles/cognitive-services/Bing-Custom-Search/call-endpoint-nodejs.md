---
title: 'Rychlý start: Volání koncového bodu Vlastní vyhledávání Bingu pomocí Node. js | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Pomocí tohoto rychlého startu můžete začít požadovat výsledky hledání z vaší instance Vlastní vyhledávání Bingu pomocí Node. js.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 07/26/2019
ms.author: aahi
ms.openlocfilehash: e95b9aa8f01142065202e029c6298fc4c4cb1294
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565727"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-nodejs"></a>Rychlý start: Volání koncového bodu Vlastní vyhledávání Bingu pomocí Node. js

Pomocí tohoto rychlého startu můžete začít požadovat výsledky hledání z vaší instance Vlastní vyhledávání Bingu. I když je tato aplikace napsaná v JavaScriptu, rozhraní API pro vlastní vyhledávání Bingu je webová služba RESTful kompatibilní s většinou programovacích jazyků. Zdrojový kód k této ukázce je dostupný na [Githubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingCustomSearchv7.js).

## <a name="prerequisites"></a>Požadavky

- Instance Vlastní vyhledávání Bingu. Další [informace najdete v tématu rychlý Start: Pro další informace vytvořte první](quick-start.md) instanci vlastní vyhledávání Bingu.

- [Node.js](https://www.nodejs.org/)

- [Knihovna požadavků JavaScriptu](https://github.com/request/request)

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Vytvoření a inicializace aplikace

1. Vytvořte nový soubor JavaScriptu v oblíbených IDE nebo editoru a přidejte `require()` příkaz pro knihovnu požadavků. Vytvořte proměnné pro svůj klíč předplatného, ID vlastní konfigurace a hledaný termín. 

    ```javascript
    var request = require("request");
    
    var subscriptionKey = 'YOUR-SUBSCRIPTION-KEY';
    var customConfigId = 'YOUR-CUSTOM-CONFIG-ID';
    var searchTerm = 'microsoft';
    ```

## <a name="send-and-receive-a-search-request"></a>Odeslání a přijetí žádosti o vyhledávání 

1. Vytvořte proměnnou pro uložení informací odesílaných ve vaší žádosti. Vytvořte adresu URL žádosti připojením hledaného termínu k `q=` parametru dotazu a `customconfig=`ID vlastní konfigurace vaší instance hledání. parametry oddělte `&` znakem. 

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

1. Použijte knihovnu požadavků JavaScriptu k odeslání žádosti o vyhledávání do instance Vlastní vyhledávání Bingu a vytiskněte informace o výsledcích, včetně jejího názvu, adresy URL a data posledního procházení webové stránky.

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
> [Vytvoření vlastní vyhledávací webové aplikace](./tutorials/custom-search-web-page.md)
