---
title: 'Rychlý start: Hledání obrázků - API REST pro vyhledávání obrázků Bingu a Node.js'
titleSuffix: Azure Cognitive Services
description: Použít tento rychlý start k odesílání požadavků vyhledávání obrázků Bingu Image Search REST API prostřednictvím JavaScriptu a přijímat odpověďmi ve formátu JSON.
services: cognitive-services
documentationcenter: ''
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: quickstart
ms.date: 8/20/2018
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: d33d63cc7d13a5c2c6e1923607102c4ddc554650
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2019
ms.locfileid: "54062754"
---
# <a name="quickstart-search-for-images-using-the-bing-image-search-rest-api-and-nodejs"></a>Rychlý start: Hledání obrázků pomocí API REST pro vyhledávání obrázků Bingu a Node.js

V tomto rychlém startu poprvé zavoláte rozhraní API Bingu pro vyhledávání obrázků a dostanete odpověď ve formátu JSON. Tato jednoduchá aplikace JavaScriptu odesílá vyhledávací dotaz do rozhraní API a zobrazuje nezpracované výsledky.

I když je tato aplikace napsaná v JavaScriptu a běží v Node.js, jsou toto rozhraní API a webová služba RESTful kompatibilní s většinou programovacích jazyků.

Zdrojový kód této ukázky je dostupný na [GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingImageSearchv7Quickstart.js) s dalším ošetřením chyb a poznámkami ke kódu.

## <a name="prerequisites"></a>Požadavky

* Nejnovější verze [Node.js](https://nodejs.org/en/download/).

* [Knihovna žádostí o jazyka JavaScript](https://github.com/request/request)  
[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

Viz také [služeb Cognitive Services ceny – rozhraní API Bingu pro vyhledávání](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="create-and-initialize-the-application"></a>Vytvoření a inicializace aplikace

1. Vytvořte ve svém oblíbeném integrovaném vývojovém prostředí nebo editoru nový soubor JavaScriptu a nastavte striktnost a požadavky protokolu HTTPS.

    ```javascript
    'use strict';
    let https = require('https');
    ```

2. Vytvořte proměnné pro koncový bod rozhraní API, cestu k rozhraní API pro vyhledávání obrázků, klíč předplatného a hledaný výraz.
    ```javascript
    let subscriptionKey = 'enter key here';
    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/images/search';
    let term = 'tropical ocean';
    ```

## <a name="construct-the-search-request-and-query"></a>Sestavení žádosti o vyhledávání a dotazu

1. Pomocí proměnných z posledního kroku naformátujte vyhledávací adresu URL pro žádost rozhraní API. Hledaný výraz musí být před odesláním do rozhraní API zakódovaný do adresy URL.

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

2. Pomocí knihovny žádostí odešlete dotaz do rozhraní API. `response_handler` se bude definovat v další části.
    ```javascript
    let req = https.request(request_params, response_handler);
    req.end();
    ```

## <a name="handle-and-parse-the-response"></a>Zpracování a parsování odpovědi

1. Definujte funkci s názvem `response_handler`, která jako parametr přijímá volání protokolu HTTP `response`. V rámci této funkce proveďte následující kroky:

    1. Definujte proměnnou, která bude obsahovat text odpovědi JSON.  
        ```javascript
        let response_handler = function (response) {
            let body = '';
        };
        ```

    2. Uložte text odpovědi při volání příznaku **data**.
        ```javascript
        response.on('data', function (d) {
            body += d;
        });
        ```

    3. Při signalizaci příznaku **end** lze zpracovat JSON a zobrazit adresu URL obrázku společně s celkovým počtem vrácených obrázků.

        ```javascript
        response.on('end', function () {
            let firstImageResult = imageResults.value[0];
            console.log(`Image result count: ${imageResults.value.length}`);
            console.log(`First image thumbnail url: ${firstImageResult.thumbnailUrl}`);
            console.log(`First image web search url: ${firstImageResult.webSearchUrl}`);
         });
        ```

## <a name="json-response"></a>Odpověď JSON

Odpovědi od rozhraní API Bingu pro vyhledávání obrázků se vrátí jako JSON. Ukázková odpověď je zkrácená, aby zobrazovala jenom jeden výsledek.

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
> [Vyhledávání obrázků Bingu – kurz jednostránkové aplikace](../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Další informace najdete v tématech

* [Co je Vyhledávání obrázků Bingu?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Vyzkoušet online interaktivní ukázku](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Získání přístupového klíče služeb Cognitive Services zdarma](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
* [Dokumentace Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services)
* [Referenční informace k rozhraní API Bingu pro vyhledávání obrázků](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
