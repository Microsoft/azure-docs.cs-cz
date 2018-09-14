---
title: 'Rychlý start: Vyhledávání obrázků s využitím SDK vyhledávání obrázků Bingu a Node.js'
description: V tomto rychlém startu žádosti a filtrovat Image vrácený Bingu pro vyhledávání obrázků, pomocí Node.js.
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: cagronlund
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 08/28/2018
ms.author: aahi
ms.openlocfilehash: ebe6629344c076119c0bfdaee76a69df6274ca28
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2018
ms.locfileid: "45572572"
---
# <a name="quickstart-search-for-images-with-the-bing-image-search-sdk-and-nodejs"></a>Rychlý start: Vyhledávání obrázků pomocí sady SDK vyhledávání obrázků Bingu a Node.js

V tomto rychlém startu používejte k vytvoření vaší první vyhledávání obrázků pomocí SDK vyhledávání Bingu Image, který tvoří obálku pro rozhraní API a obsahuje stejné funkce. Tato jednoduchá aplikace JavaScript odešle dotaz vyhledávání obrázků, analyzuje odpověď JSON a zobrazí adresa URL první obrázku vrátil.

Zdrojový kód pro tuto ukázku je k dispozici na [Githubu](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/imageSearch.js) zpracování dalších chyb a poznámky.

## <a name="prerequisites"></a>Požadavky

* [Služeb Cognitive Services obrázek hledání sady SDK pro Node.js](https://www.npmjs.com/package/azure-cognitiveservices-imagesearch)
    * Nainstalovat s použitím `npm install azure-cognitiveservices-imagesearch`
* [Node.js Azure Rest](https://www.npmjs.com/package/ms-rest-azure) modulu
    * Nainstalovat s použitím `npm install ms-rest-azure`

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Vytváření a inicializace aplikace

1. Vytvořte nový soubor JavaScript ve vašich oblíbených prostředím IDE nebo editorem a nastavit přísnosti, https a další požadavky.

    ```javascript
    'use strict';
    https = require('https');
    const Search = require('azure-cognitiveservices-imagesearch');
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    ```

2. V hlavní metodě váš projekt Vytvořte proměnné pro klíč předplatného. Platné výsledky hledání obrázků, který se má vrátit v Bingu a hledaný termín. Potom vytvořte instanci klienta vyhledávání obrázků pomocí klíče.

    ```javascript
    //replace this value with your valid subscription key.
    let serviceKey = "ENTER YOUR KEY HERE";

    //the search term for the request 
    let searchTerm = "canadian rockies";

    //instantiate the the image search client 
    let credentials = new CognitiveServicesCredentials(serviceKey);
    let imageSearchApiClient = new Search.ImageSearchAPIClient(credentials);

    ```

## <a name="create-an-asynchronous-helper-function"></a>Vytvoření asynchronní pomocné funkce

1. Vytvořit funkci, která volá klienta asynchronně a vrací odpověď ze služby vyhledávání obrázků Bingu.  
    ```javascript
    //a helper function to perform an async call to the Bing Image Search API
    const sendQuery = async () => {
        return await imageSearchApiClient.imagesOperations.search(searchTerm);
    };
    ```
## <a name="send-a-query-and-handle-the-response"></a>Odeslat dotaz a zpracovat odpověď 

1. Volání pomocné funkce a popisovač jeho `promise` analyzovat výsledky hledání obrázků, vrátila v odpovědi.

    Pokud odpověď obsahuje výsledky hledání, uloží první výsledek a vytiskne jeho podrobnosti, jako je například miniatury adresa URL, původní adresu URL, společně s celkový počtem vrátil bitové kopie.  
    ```javascript
    sendQuery().then(imageResults => {
        if (imageResults == null) {
        console.log("No image results were found.");
        }
        else {
            console.log(`Total number of images returned: ${imageResults.value.length}`);
            let firstImageResult = imageResults.value[0];
            //display the details for the first image result. After running the application,
            //you can copy the resulting URLs from the console into your browser to view the image. 
            console.log(`Total number of images found: ${imageResults.value.length}`);
            console.log(`Copy these URLs to view the first image returned:`);
            console.log(`First image thumbnail url: ${firstImageResult.thumbnailUrl}`);
            console.log(`First image content url: ${firstImageResult.contentUrl}`); 
        }
      })
      .catch(err => console.error(err))
    ```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Kurz jednostránkovou aplikaci pro vyhledávání obrázků Bingu](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app)

## <a name="see-also"></a>Další informace najdete v tématech 

* [Co je pro vyhledávání obrázků Bingu?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Zkuste online interaktivní ukázka](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Získání bezplatné přístupového klíče služeb Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api) 
* [Ukázky Node.js pro sadu SDK Azure Cognitive Services](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples) 
* [Dokumentace ke službě Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services)
* [Referenční dokumentace rozhraní API pro vyhledávání obrázků Bingu](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)