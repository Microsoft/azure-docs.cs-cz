---
title: 'Úvodní příručka: Volání vlastního koncového bodu služby Bing pomocí souboru Node.js | Dokumenty společnosti Microsoft'
titleSuffix: Azure Cognitive Services
description: Pomocí tohoto rychlého startu můžete začít požadovat výsledky hledání z instance vlastního vyhledávání Bingpomocí souboru Node.js
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: aahi
ms.openlocfilehash: 1c7bd97de4e46e1c8da467840006fe2520851caf
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80238864"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-nodejs"></a>Úvodní příručka: Volání vlastního koncového bodu služby Bing pomocí souboru Node.js

Pomocí tohoto rychlého startu můžete začít požadovat výsledky hledání z instance vlastního vyhledávání Bingu. Zatímco tato aplikace je napsána v JavaScriptu, Bing Custom Search API je RESTful webová služba kompatibilní s většinou programovacích jazyků. Zdrojový kód pro tuto ukázku je k dispozici na [GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingCustomSearchv7.js).

## <a name="prerequisites"></a>Požadavky

- Instance vlastního vyhledávání Bingu. Další informace najdete [v tématu Úvodní příručka: Vytvoření první instance vlastního vyhledávání Bingu.](quick-start.md)

- [Node.js](https://www.nodejs.org/)

- [Knihovna žádostí JavaScriptu](https://github.com/request/request)

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Vytvoření a inicializace aplikace

1. Vytvořte nový soubor JavaScriptu ve svém oblíbeném `require()` rozhraní IDE nebo editoru a přidejte příkaz pro knihovnu požadavků. Vytvořte proměnné pro klíč předplatného, Vlastní ID konfigurace a hledaný termín. 

    ```javascript
    var request = require("request");
    
    var subscriptionKey = 'YOUR-SUBSCRIPTION-KEY';
    var customConfigId = 'YOUR-CUSTOM-CONFIG-ID';
    var searchTerm = 'microsoft';
    ```

## <a name="send-and-receive-a-search-request"></a>Odeslání a přijetí požadavku na vyhledávání 

1. Vytvořte proměnnou pro uložení informací odesílaných v požadavku. Vytvořte adresu URL požadavku připojením `q=` hledaného výrazu k parametru dotazu `customconfig=`a ID vlastní konfigurace instance hledání. oddělte parametry `&` znakem. Můžete použít globální koncový bod níže nebo vlastní koncový bod [subdomény](../../cognitive-services/cognitive-services-custom-subdomains.md) zobrazený na portálu Azure pro váš prostředek.

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

1. Pomocí knihovny požadavků javascriptu odešlete žádost o hledání do vaší instance vlastního vyhledávání Bingu a vytiskněte informace o výsledcích, včetně jejího názvu, adresy URL a data posledního procházení webové stránky.

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

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření webové aplikace pro vlastní vyhledávání](./tutorials/custom-search-web-page.md)
