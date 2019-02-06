---
title: 'Rychlý start: Odeslat žádost o vyhledávání sadou SDK pro vyhledávání entit Bingu pro Node.js'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu slouží k vyhledání entity sadou SDK pro vyhledávání entit Bingu pro Node.js
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: v-gedod
ms.openlocfilehash: db906319c04c96fded7417c932beacf7feea5b5b
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55757449"
---
# <a name="quickstart-send-a-search-request-with-the-bing-entity-search-sdk-for-nodejs"></a>Rychlý start: Odeslat žádost o vyhledávání sadou SDK pro vyhledávání entit Bingu pro Node.js

V tomto rychlém startu pomocí zahájíte hledání entit sadou SDK pro vyhledávání entit Bingu pro Node.js. Při vyhledávání entit Bingu je kompatibilní s Většina programovacích jazyků rozhraní REST API, sada SDK poskytuje snadný způsob, jak do svých aplikací integrovat službu. Zdrojový kód pro tuto ukázku můžete najít na [Githubu](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/entitySearch.js).

## <a name="prerequisites"></a>Požadavky

* Nejnovější verze [Node.js](https://nodejs.org/en/download/).

* [Vyhledávání entit Bingu sady SDK pro Node.js](https://www.npmjs.com/package/azure-cognitiveservices-entitysearch)

Instalace sady SDK vyhledávání entit Bingu:

1. Spustit `npm install ms-rest-azure` ve vašem vývojovém prostředí.
2. Spustit `npm install azure-cognitiveservices-entitysearch` ve vašem vývojovém prostředí.

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Vytvoření a inicializace aplikace

1. Vytvořte nový soubor JavaScript ve vašich oblíbených prostředím IDE nebo editorem a přidejte následující požadavky. 
    
    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    const EntitySearchAPIClient = require('azure-cognitiveservices-entitysearch');
    ```

2. Vytvoření instance `CognitiveServicesCredentials` používat váš klíč předplatného. Vytvořte instanci klienta vyhledávání s ním.

    ```javascript
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let entitySearchApiClient = new EntitySearchAPIClient(credentials);
    ```

## <a name="send-a-request-and-receive-a-response"></a>Žádost o odeslání a přijetí odpovědi

2. Poslat žádost o vyhledávání entit s `entitiesOperations.search()`. Po přijetí odpovědi, vytiskne `queryContext`, počet objektů vrácených výsledků a popis první výsledek.
      
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

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vytvoření jednostránkové webové aplikace](../tutorial-bing-entities-search-single-page-app.md)

* [Co je API pro vyhledávání entit Bingu?](../overview.md )