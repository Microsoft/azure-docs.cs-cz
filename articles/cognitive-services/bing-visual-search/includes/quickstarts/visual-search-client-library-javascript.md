---
title: Rychlý start klientské knihovny JavaScript pro vizuální vyhledávání Bingu
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/26/2020
ms.author: aahi
ms.openlocfilehash: 404529bf269f899603b92c6c23b0d95cd2749ee5
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550020"
---
Pomocí tohoto rychlého startu můžete začít dostávat přehledy obrázků ze služby Vizuální vyhledávání Bing pomocí klientské knihovny JavaScriptu. Zatímco vizuální vyhledávání Bing udává rozhraní REST API kompatibilní s většinou programovacích jazyků, klientská knihovna poskytuje snadný způsob integrace služby do vašich aplikací. Zdrojový kód pro tuto ukázku lze nalézt na [GitHubu](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/visualSearch.js). 

[Referenční dokumentace](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-visualsearch/?view=azure-node-latest) | [Ukázky zdrojového kódu](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-visualsearch) | [knihovny](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/) [(NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-visualsearch) | 

## <a name="prerequisites"></a>Požadavky
* [Node.js](https://www.nodejs.org/)
* Klientská knihovna Vizuálního vyhledávání Bingu pro JavaScript
    * Chcete-li nastavit konzolovou aplikaci pomocí klientské knihovny Vizuální vyhledávání Bingu, spusťte následující příkazy:
        1. `npm install ms-rest-azure`
        2. `npm install azure-cognitiveservices-visualsearch`.


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](~/includes/cognitive-services-bing-visual-search-signup-requirements.md)]

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
> [Sestavení jednostránkové webové aplikace](../../tutorial-bing-visual-search-single-page-app.md)
