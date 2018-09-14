---
title: 'Rychlý start: Odeslání vyhledávací dotazy pomocí rozhraní API vyhledávání obrázků Bingu pomocí Node.js'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu použijte k vyhledání a Image můžete vyhledat na webu pomocí rozhraní API webové vyhledávání Bingu.
services: cognitive-services
documentationcenter: ''
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 8/20/2018
ms.author: aahi
ms.openlocfilehash: 43f0cfec6aa2d4263b6a627736c2a432b2943145
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2018
ms.locfileid: "45576642"
---
# <a name="quickstart-send-search-queries-using-the-bing-image-search-rest-api-and-nodejs"></a>Rychlý start: Odeslání vyhledávací dotazy s využitím REST rozhraní API pro vyhledávání obrázků Bingu a Node.js

V tomto rychlém startu můžete provést první volání do rozhraní API Bingu pro vyhledávání obrázků a získejte odpověď ve formátu JSON. Tato jednoduchá aplikace JavaScript odešle vyhledávací dotaz na rozhraní API a zobrazí nezpracované výsledky.

Zatímco tato aplikace je napsána v jazyce JavaScript a je spuštěn v Node.js s využitím rozhraní API RESTful webová služba kompatibilní většina programovacích jazyků.

Zdrojový kód pro tuto ukázku je k dispozici [na Githubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingImageSearchv7Quickstart.js) zpracování dalších chyb a poznámky kódu.

## <a name="prerequisites"></a>Požadavky

* Nejnovější verzi [Node.js](https://nodejs.org/en/download/).

* [Knihovna žádostí o jazyka JavaScript](https://github.com/request/request)
[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Vytváření a inicializace aplikace

1. Vytvořte nový soubor JavaScript ve vašich oblíbených prostředím IDE nebo editorem a nastavit požadavky přísnosti a https.

    ```javascript
    'use strict';
    let https = require('https');
    ```

2. Vytváření proměnných pro koncový bod rozhraní API, cesty pro hledání obrázků rozhraní API, váš klíč předplatného a hledané výrazy.
    ```javascript
    let subscriptionKey = 'enter key here';
    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/images/search';
    let term = 'tropical ocean';
    ```

## <a name="construct-the-search-request-and-query"></a>Vytvořte požadavek hledání a dotazu.

1. Použití proměnných v posledním kroku k formátování adresa URL pro hledání pro žádosti na rozhraní API. Všimněte si, že hledaný výraz musí být kódovaná adresou URL před odesláním do rozhraní API.

    ```javascript
    let request_params = {
        method : 'GET',
        hostname : host,
        path : path + '?q=' + encodeURIComponent(search),
        headers : {
        'Ocp-Apim-Subscription-Key' : subscriptionKey,
        }
    };
    ```

2. Použijte knihovnu žádost odeslat dotaz na rozhraní API. `response_handler` budou určené v další části.
    ```javascript
    let req = https.request(request_params, response_handler);
    req.end();
    ```

## <a name="handle-and-parse-the-response"></a>Zpracovávat a analyzovat odpověď

1. definovat funkci s názvem `response_handler` , která přijímá volání protokolu HTTP, `response`, jako parametr. v rámci této funkce proveďte následující kroky:
    
    1. Definujte proměnnou tak, aby obsahovala text JSON odpovědi.  
        ```javascript
        let response_handler = function (response) {
            let body = '';
        };
        ```

    2. Store tělo odpovědi při **data** příznak je volána. 
        ```javascript
        response.on('data', function (d) {
            body += d;
        });
        ```

    3. Když **end** signalizována příznak, může zpracovat ve formátu JSON a adresa URL obrázku lze vytisknout, společně s celkový počtem vrácených imagí.
    
        ```javascript
        response.on('end', function () {
            let firstImageResult = imageResults.value[0];
            console.log(`Image result count: ${imageResults.value.length}`);
            console.log(`First image thumbnail url: ${firstImageResult.thumbnailUrl}`);
            console.log(`First image web search url: ${firstImageResult.webSearchUrl}`);
         });
        ```

## <a name="json-response"></a>Odpověď JSON

Odpovědi z rozhraní API Bingu pro vyhledávání obrázků se vrátí jako JSON. Tato ukázková odpověď byl zkrácen na zobrazit jeden výsledek.

```json
{
"_type":"Images",
"instrumentation":{
    "_type":"ResponseInstrumentation"
},
"readLink":"images\/search?q=tropical ocean",
"webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=tropical ocean&FORM=OIIARP",
"totalEstimatedMatches":842,
"nextOffset":47,
"value":[
    {
        "webSearchUrl":"https:\/\/www.bing.com\/images\/search?view=detailv2&FORM=OIIRPO&q=tropical+ocean&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&simid=608027248313960152",
        "name":"My Life in the Ocean | The greatest WordPress.com site in ...",
        "thumbnailUrl":"https:\/\/tse3.mm.bing.net\/th?id=OIP.fmwSKKmKpmZtJiBDps1kLAHaEo&pid=Api",
        "datePublished":"2017-11-03T08:51:00.0000000Z",
        "contentUrl":"https:\/\/mylifeintheocean.files.wordpress.com\/2012\/11\/tropical-ocean-wallpaper-1920x12003.jpg",
        "hostPageUrl":"https:\/\/mylifeintheocean.wordpress.com\/",
        "contentSize":"897388 B",
        "encodingFormat":"jpeg",
        "hostPageDisplayUrl":"https:\/\/mylifeintheocean.wordpress.com",
        "width":1920,
        "height":1200,
        "thumbnail":{
        "width":474,
        "height":296
        },
        "imageInsightsToken":"ccid_fmwSKKmK*mid_8607ACDACB243BDEA7E1EF78127DA931E680E3A5*simid_608027248313960152*thid_OIP.fmwSKKmKpmZtJiBDps1kLAHaEo",
        "insightsMetadata":{
        "recipeSourcesCount":0,
        "bestRepresentativeQuery":{
            "text":"Tropical Beaches Desktop Wallpaper",
            "displayText":"Tropical Beaches Desktop Wallpaper",
            "webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=Tropical+Beaches+Desktop+Wallpaper&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&FORM=IDBQDM"
        },
        "pagesIncludingCount":115,
        "availableSizesCount":44
        },
        "imageId":"8607ACDACB243BDEA7E1EF78127DA931E680E3A5",
        "accentColor":"0050B2"
    }
}
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Kurz jednostránkovou aplikaci pro vyhledávání obrázků Bingu](../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Další informace najdete v tématech 

* [Co je pro vyhledávání obrázků Bingu?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Zkuste online interaktivní ukázka](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Získání bezplatné přístupového klíče služeb Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
* [Dokumentace ke službě Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services)
* [Referenční dokumentace rozhraní API pro vyhledávání obrázků Bingu](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)