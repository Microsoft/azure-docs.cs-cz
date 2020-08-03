---
title: Rychlý Start knihovny klienta Vvyhledávání videí Bingu JavaScript
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/19/2020
ms.author: aahi
ms.custom: devx-track-javascript
ms.openlocfilehash: 7cf28df4f009b017699c926d1ca54b7e5320a179
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2020
ms.locfileid: "87404106"
---
V tomto rychlém startu můžete začít hledat zprávy pomocí klientské knihovny Vvyhledávání videí Bingu pro JavaScript. I když Vvyhledávání videí Bingu má REST API kompatibilní s většinou programovacích jazyků, Klientská knihovna poskytuje snadný způsob, jak integrovat službu do vašich aplikací. Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/videoSearch.js). Obsahuje další poznámky a funkce.

## <a name="prerequisites"></a>Požadavky

- [Node.js](https://www.nodejs.org/)

Chcete-li nastavit konzolovou aplikaci pomocí klientské knihovny Vvyhledávání videí Bingu:
* Spusťte `npm install ms-rest-azure` ve vývojovém prostředí.
* Spusťte `npm install azure-cognitiveservices-videosearch` ve vývojovém prostředí.

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](~/includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Vytvoření a inicializace aplikace

1. Vytvořte nový soubor JavaScriptu v oblíbených IDE nebo editoru a přidejte `require()` příkaz pro klientskou knihovnu Vvyhledávání videí Bingu a `CognitiveServicesCredentials` modul. Vytvořte proměnnou pro klíč předplatného. 
    
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

1. Slouží `client.videosOperations.search()` k odeslání požadavku hledání do rozhraní API Bingu pro vyhledávání videí. Po vrácení výsledků hledání použijte `.then()` k zaznamenání výsledku.
    
    ```javascript
    client.videosOperations.search('Interstellar Trailer').then((result) => {
        console.log(result.value);
    }).catch((err) => {
        throw err;
    });
    ```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření webové aplikace s jednou stránkou](../../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Viz také 

* [Co je rozhraní API Bingu pro vyhledávání videí?](../../overview.md)
* [Ukázky sady .NET SDK pro rozpoznávání služeb](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)