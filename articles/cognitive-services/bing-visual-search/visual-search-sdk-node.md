---
title: 'Úvodní příručka: Získejte přehledy obrázků pomocí sady SDK pro soubor Node.js – vizuální vyhledávání Bingu'
titleSuffix: Azure Cognitive Services
description: Pomocí tohoto rychlého startu můžete začít dostávat přehledy obrázků ze služby Vizuální vyhledávání Bing pomocí sady SDK node.js.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: aahi
ms.openlocfilehash: d99aa2d2827716b2b04d059e47d9768eef8cd690
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77485093"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-sdk-for-nodejs"></a>Úvodní příručka: Získejte přehledy obrázků pomocí sady SDK vizuálního vyhledávání Bingu pro soubor Node.js

Pomocí tohoto rychlého startu můžete začít dostávat přehledy obrázků ze služby Vizuální vyhledávání Bing pomocí sady SDK node.js. Zatímco vizuální vyhledávání Bing udává rozhraní REST API kompatibilní s většinou programovacích jazyků, sada SDK poskytuje snadný způsob integrace služby do vašich aplikací. Zdrojový kód pro tuto ukázku lze nalézt na [GitHubu](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/visualSearch.js). 

## <a name="prerequisites"></a>Požadavky
* [Node.js](https://www.nodejs.org/)
* Sada SDK vizuálního vyhledávání Bingu pro soubor Node.js
    * Chcete-li nastavit konzolovou aplikaci pomocí sady SDK vizuálního vyhledávání Bingu, spusťte následující příkazy:
        1. `npm install ms-rest-azure`
        2. `npm install azure-cognitiveservices-visualsearch`.


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

<a name="client"></a>

## <a name="create-and-initialize-the-application"></a>Vytvoření a inicializace aplikace

1. Vytvořte nový soubor JavaScriptu ve svém oblíbeném rozhraní IDE nebo editoru a přidejte následující požadavky. Pak vytvořte proměnné pro klíč předplatného, Vlastní ID konfigurace a cestu k obrázku, který chcete nahrát. 

    ```javascript
    const os = require("os");
    const async = require('async');
    const fs = require('fs');
    const Search = require('azure-cognitiveservices-visualsearch');
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    
    let keyVar = 'YOUR-VISUAL-SEARCH-ACCESS-KEY';
    let credentials = new CognitiveServicesCredentials(keyVar);
    let filePath = "../Data/image.jpg";
    ```

2. Vytvořte kvit, aby klient.

    ```javascript
    let visualSearchClient = new Search.VisualSearchClient(credentials);
    ```

## <a name="search-for-images"></a>Hledání obrázků

1. Slouží `fs.createReadStream()` ke čtení v souboru obrázku a vytváření proměnných pro požadavek hledání a výsledky. Pak použijte klienta k vyhledávání obrázků.

    ```javascript
    let fileStream = fs.createReadStream(filePath);
    let visualSearchRequest = JSON.stringify({});
    let visualSearchResults;
    try {
        visualSearchResults = await visualSearchClient.images.visualSearch({
            image: fileStream,
            knowledgeRequest: visualSearchRequest
        });
        console.log("Search visual search request with binary of image");
    } catch (err) {
        console.log("Encountered exception. " + err.message);
    }
    ```

2. Parsujte výsledky předchozího dotazu:

    ```javascript
    // Visual Search results
    if (visualSearchResults.image.imageInsightsToken) {
        console.log(`Uploaded image insights token: ${visualSearchResults.image.imageInsightsToken}`);
    }
    else {
        console.log("Couldn't find image insights token!");
    }
    
    // List of tags
    if (visualSearchResults.tags.length > 0) {
        let firstTagResult = visualSearchResults.tags[0];
        console.log(`Visual search tag count: ${visualSearchResults.tags.length}`);
    
        // List of actions in first tag
        if (firstTagResult.actions.length > 0) {
            let firstActionResult = firstTagResult.actions[0];
            console.log(`First tag action count: ${firstTagResult.actions.length}`);
            console.log(`First tag action type: ${firstActionResult.actionType}`);
        }
        else {
            console.log("Couldn't find tag actions!");
        }
    
    }
    else {
        console.log("Couldn't find image tags!");
    }
    
    ```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření jednostránkové webové aplikace](tutorial-bing-visual-search-single-page-app.md)
