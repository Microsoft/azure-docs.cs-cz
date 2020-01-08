---
title: 'Rychlý Start: hledání videí pomocí sady SDK pro Node. js – Vvyhledávání videí Bingu'
titleSuffix: Azure Cognitive Services
description: Pomocí tohoto rychlého startu můžete odesílat žádosti o vyhledávání videí pomocí sady SDK Vvyhledávání videí Bingu SDK pro Node. js.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: aahi
ms.openlocfilehash: 10d59da26c4ad583e3cb80b7d5cfc0d569f83ac1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75382612"
---
# <a name="quickstart-perform-a-video-search-with-the-bing-video-search-sdk-for-nodejs"></a>Rychlý Start: provedení videa s Vvyhledávání videí Bingu SDK pro Node. js

V tomto rychlém startu můžete začít hledat zprávy s Vvyhledávání videí Bingu SDK pro Node. js. I když Vvyhledávání videí Bingu má REST API kompatibilní s většinou programovacích jazyků, poskytuje sada SDK snadný způsob, jak integrovat službu do vašich aplikací. Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/videoSearch.js) Obsahuje další poznámky a funkce.

## <a name="prerequisites"></a>Požadavky

- [Node.js](https://www.nodejs.org/)

Nastavení konzolové aplikace pomocí sady Vvyhledávání videí Bingu SDK:
* Spusťte `npm install ms-rest-azure` ve vývojovém prostředí.
* Spusťte `npm install azure-cognitiveservices-videosearch` ve vývojovém prostředí.

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Vytvoření a inicializace aplikace

1. Vytvořte nový soubor JavaScriptu v oblíbených IDE nebo editoru a přidejte příkaz `require()` pro sadu Vvyhledávání videí Bingu SDK a modul `CognitiveServicesCredentials`. Vytvořte proměnnou pro klíč předplatného. 
    
    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    const VideoSearchAPIClient = require('azure-cognitiveservices-videosearch');
    ```

2. Vytvořte instanci `CognitiveServicesCredentials` s klíčem. Pak ho použijte k vytvoření instance klienta hledání videí.

    ```javascript
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let client = new VideoSearchAPIClient(credentials);
    ```

## <a name="send-the-search-request"></a>Odeslat požadavek hledání

1. K odeslání žádosti o vyhledávání do rozhraní API Bingu pro vyhledávání videí použijte `client.videosOperations.search()`. Po vrácení výsledků hledání použijte `.then()` k zaprotokolování výsledku.
    
    ```javascript
    client.videosOperations.search('Interstellar Trailer').then((result) => {
        console.log(result.value);
    }).catch((err) => {
        throw err;
    });
    ```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření webové aplikace s jednou stránkou](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Další informace najdete v tématech 

* [Co je rozhraní API Bingu pro vyhledávání videí?](../overview.md)
* [Ukázky kognitivních služeb sady .NET SDK](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)