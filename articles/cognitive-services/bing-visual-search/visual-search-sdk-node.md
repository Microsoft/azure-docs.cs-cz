---
title: 'Rychlý Start: Získání přehledů obrázků pomocí sady SDK pro Node. js – Vizuální vyhledávání Bingu'
titleSuffix: Azure Cognitive Services
description: Pomocí tohoto rychlého startu můžete začít získávat přehledy imagí z Vizuální vyhledávání Bingu služby pomocí sady Node. js SDK.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: aahi
ms.openlocfilehash: 676427ca2985de51767939891b41f413446d4bb1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75446570"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-sdk-for-nodejs"></a>Rychlý Start: Získání přehledů imagí pomocí Vizuální vyhledávání Bingu SDK pro Node. js

Pomocí tohoto rychlého startu můžete začít získávat přehledy imagí z Vizuální vyhledávání Bingu služby pomocí sady Node. js SDK. I když Vizuální vyhledávání Bingu má REST API kompatibilní s většinou programovacích jazyků, poskytuje sada SDK snadný způsob, jak integrovat službu do vašich aplikací. Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/visualSearch.js) 

## <a name="prerequisites"></a>Požadavky
* [Node.js](https://www.nodejs.org/)
* Sada SDK Vizuální vyhledávání Bingu pro Node. js
    * Chcete-li nastavit konzolovou aplikaci pomocí sady Vizuální vyhledávání Bingu SDK, spusťte následující příkazy:
        1. `npm install ms-rest-azure`
        2. `npm install azure-cognitiveservices-search-visualSearch`.


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

<a name="client"></a>

## <a name="create-and-initialize-the-application"></a>Vytvoření a inicializace aplikace

1. V oblíbených IDE nebo editoru vytvořte nový soubor JavaScriptu a přidejte následující požadavky. Pak vytvořte proměnné pro svůj klíč předplatného, ID vlastní konfigurace a cestu k souboru k imagi, kterou chcete nahrát. 

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

2. Vytvořte instanci klienta.

    ```javascript
    let visualSearchClient = new Search.VisualSearchClient(credentials);
    ```

## <a name="search-for-images"></a>Hledání imagí

1. Pomocí `fs.createReadStream()` si můžete přečíst v souboru obrázku a vytvořit proměnné pro svůj požadavek a výsledky hledání. Pak použijte klienta k hledání imagí.

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
> [Sestavení webové aplikace s jednou stránkou](tutorial-bing-visual-search-single-page-app.md)
