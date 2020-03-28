---
title: Rychlý start klientské knihovny JavaScript pro vyhledávání videí Bing
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/19/2020
ms.author: aahi
ms.openlocfilehash: b642d981b79d13857881ec8cc5796e6365003ace
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80289760"
---
Pomocí tohoto rychlého startu můžete začít vyhledávat novinky v klientské knihovně Bing Video Search pro JavaScript. Zatímco hledání videa Bing má rozhraní REST API kompatibilní s většinou programovacích jazyků, klientská knihovna poskytuje snadný způsob integrace služby do vašich aplikací. Zdrojový kód pro tuto ukázku lze nalézt na [GitHubu](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/videoSearch.js). Obsahuje více anotací a funkcí.

## <a name="prerequisites"></a>Požadavky

- [Node.js](https://www.nodejs.org/)

Nastavení konzolové aplikace pomocí klientské knihovny Bing Video Search:
* Spouštějte `npm install ms-rest-azure` ve vývojovém prostředí.
* Spouštějte `npm install azure-cognitiveservices-videosearch` ve vývojovém prostředí.

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](~/includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Vytvoření a inicializace aplikace

1. Vytvořte nový soubor JavaScriptu ve svém oblíbeném `require()` rozhraní IDE nebo editoru `CognitiveServicesCredentials` a přidejte příkaz pro klientskou knihovnu a modul Bing Video Search. Vytvořte proměnnou pro klíč předplatného. 
    
    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    const VideoSearchAPIClient = require('azure-cognitiveservices-videosearch');
    ```

2. Vytvořte instanci `CognitiveServicesCredentials` s klíčem. Pak ji použijte k vytvoření instance klienta pro vyhledávání videa.

    ```javascript
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let client = new VideoSearchAPIClient(credentials);
    ```

## <a name="send-the-search-request"></a>Odeslat požadavek na vyhledávání

1. Slouží `client.videosOperations.search()` k odeslání požadavku na vyhledávání do rozhraní API pro vyhledávání videí Bingu. Po vrácení výsledků hledání `.then()` použijte k protokolování výsledku.
    
    ```javascript
    client.videosOperations.search('Interstellar Trailer').then((result) => {
        console.log(result.value);
    }).catch((err) => {
        throw err;
    });
    ```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření webové aplikace pro jednu stránku](../../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Viz také 

* [Co je rozhraní API Bingu pro vyhledávání videí?](../../overview.md)
* [Ukázky sady Cognitive Services .NET SDK](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)