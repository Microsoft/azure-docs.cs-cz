---
title: 'Rychlý Start: odeslání žádosti o vyhledávání pomocí sady SDK pro Node. js – Vyhledávání entit Bingu'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu můžete vyhledat entity s Vyhledávání entit Bingu SDK pro Node. js.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 01/22/2020
ms.author: aahi
ms.openlocfilehash: 6ece3d7979dc80a2c6c576b3ce279d4fb9bc9472
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76716390"
---
# <a name="quickstart-send-a-search-request-with-the-bing-entity-search-sdk-for-nodejs"></a>Rychlý Start: odeslání žádosti o vyhledávání pomocí sady Vyhledávání entit Bingu SDK pro Node. js

V tomto rychlém startu můžete začít vyhledávat entity pomocí Vyhledávání entit Bingu SDK pro Node. js. I když Vyhledávání entit Bingu má REST API kompatibilní s většinou programovacích jazyků, poskytuje sada SDK snadný způsob, jak integrovat službu do vašich aplikací. Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/entitySearch.js).

## <a name="prerequisites"></a>Předpoklady

* Nejnovější verze [Node.js](https://nodejs.org/en/download/).

* [Sada SDK vyhledávání entit Bingu pro Node. js](https://www.npmjs.com/package/@azure/cognitiveservices-entitysearch)

Instalace sady Vyhledávání entit Bingu SDK:

1. Spusťte `npm install ms-rest-azure` ve vývojovém prostředí.
2. Spusťte `npm install @azure/cognitiveservices-entitysearch` ve vývojovém prostředí.

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Vytvoření a inicializace aplikace

1. V oblíbených IDE nebo editoru vytvořte nový soubor JavaScriptu a přidejte následující požadavky.

    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    const EntitySearchAPIClient = require('@azure/cognitiveservices-entitysearch');
    ```

2. Pomocí klíče předplatného vytvořte instanci `CognitiveServicesCredentials`. Pak vytvořte instanci klienta vyhledávání s ním.

    ```javascript
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let entitySearchApiClient = new EntitySearchAPIClient(credentials);
    ```

## <a name="send-a-request-and-receive-a-response"></a>Odeslat žádost a přijmout odpověď

1. Odeslat žádost o vyhledávání entit pomocí `entitiesOperations.search()`. Po přijetí odpovědi vytiskněte `queryContext`, počet vrácených výsledků a popis prvního výsledku.

    ```javascript
    entitySearchApiClient.entitiesOperations.search('seahawks').then((result) => {
        console.log(result.queryContext);
        console.log(result.entities.value);
        console.log(result.entities.value[0].description);
    }).catch((err) => {
        throw err;
    });
    ```

<!-- Removing until we can replace with a sanitized version.
![Entity results](media/entity-search-sdk-node-quickstart-results.png)
-->

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření jednostránkové webové aplikace](../tutorial-bing-entities-search-single-page-app.md)

* [Co je rozhraní API Bingu pro vyhledávání entit?](../overview.md )