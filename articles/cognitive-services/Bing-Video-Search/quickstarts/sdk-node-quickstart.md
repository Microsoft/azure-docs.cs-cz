---
title: 'Rychlý start: Hledání videí pomocí Bingu Video Search SDK pro Node.js'
titleSuffix: Azure Cognitive Services
description: Použít tento rychlý start k odesílání požadavků pro vyhledávání videí pomocí Bingu videa hledání SDK pro Node.js
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 01/31/2019
ms.author: rosh
ms.openlocfilehash: ffd03a900ec304a930dfc64e7860e28e0374da55
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55863751"
---
# <a name="quickstart-perform-a-video-search-with-the-bing-video-search-sdk-for-nodejs"></a>Rychlý start: Vyhledávání videí pomocí sady SDK videa pro vyhledávání Bingu pro Node.js

V tomto rychlém startu můžete zahájit hledání zpráv se sadou SDK vyhledávání Bingu videa pro Node.js. Při vyhledávání videí Bingu je kompatibilní s Většina programovacích jazyků rozhraní REST API, sada SDK poskytuje snadný způsob, jak do svých aplikací integrovat službu. Zdrojový kód pro tuto ukázku můžete najít na [Githubu](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/videoSearch.js). Obsahuje další poznámky a funkce.

## <a name="prerequisites"></a>Požadavky

- [Node.js](https://www.nodejs.org/)

Nastavení konzolovou aplikaci pomocí sady SDK Video vyhledávání Bingu:
* Spustit `npm install ms-rest-azure` ve vašem vývojovém prostředí.
* Spustit `npm install azure-cognitiveservices-videosearch` ve vašem vývojovém prostředí.

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Vytvoření a inicializace aplikace

1. Vytvořte nový soubor JavaScript ve vašich oblíbených prostředím IDE nebo editorem a přidejte `require()` příkazu pro sadu SDK vyhledávání Bingu Video a `CognitiveServicesCredentials` modulu. Vytvořte proměnnou pro váš klíč předplatného. 
    
    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    const VideoSearchAPIClient = require('azure-cognitiveservices-videosearch');
    ```

2. Vytvoření instance `CognitiveServicesCredentials` vaším klíčem. Pak přes ni vytvořit instanci klienta pro vyhledávání videí.

    ```javascript
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let client = new VideoSearchAPIClient(credentials);
    ```

## <a name="send-the-search-request"></a>Poslat žádost o vyhledávání

1. Použití `client.videosOperations.search()` k odeslání žádosti o hledání API Bingu pro vyhledávání videí. Pokud jsou vráceny výsledky hledání, použijte `.then()` protokolovat výsledek.
    
    ```javascript
    client.videosOperations.search('Interstellar Trailer').then((result) => {
        console.log(result.value);
    }).catch((err) => {
        throw err;
    });
    ```

<!-- Remove until the response can be replace with a sanitized version.
The code prints `result.value` items to the console without parsing any text. The results will be:
- _type: 'VideoObjectElementType'

![Video results](media/video-search-sdk-node-results.png)
-->

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vytvoření webové aplikace v jediné stránce](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Další informace najdete v tématech 

* [Co je API pro vyhledávání videí Bingu?](../overview.md)
* [Ukázky kognitivních služeb sady .NET SDK](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)