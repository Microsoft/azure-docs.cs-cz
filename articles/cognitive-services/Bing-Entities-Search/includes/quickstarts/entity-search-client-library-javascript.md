---
title: Rychlý Start knihovny klienta Vyhledávání entit Bingu JavaScript
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/06/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: 783896c4266159b167a95a2f8e9a8bb9edd8e86c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91277546"
---
Pomocí tohoto rychlého startu můžete začít vyhledávat entity pomocí Vyhledávání entit Bingu klientské knihovny pro JavaScript. I když Vyhledávání entit Bingu má REST API kompatibilní s většinou programovacích jazyků, Klientská knihovna poskytuje snadný způsob, jak integrovat službu do vašich aplikací. Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/entitySearch.js).

## <a name="prerequisites"></a>Požadavky

* Nejnovější verze [Node.js](https://nodejs.org/en/download/)

* [Sada vyhledávání entit Bingu SDK pro Node.js](https://www.npmjs.com/package/@azure/cognitiveservices-entitysearch)

Instalace sady Vyhledávání entit Bingu SDK:

1. Spusťte `npm install ms-rest-azure` ve vývojovém prostředí.
2. Spusťte `npm install @azure/cognitiveservices-entitysearch` ve vývojovém prostředí.

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-entity-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Vytvoření a inicializace aplikace

1. V oblíbených IDE nebo editoru vytvořte nový soubor JavaScriptu a přidejte následující požadavky.

    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    const EntitySearchAPIClient = require('@azure/cognitiveservices-entitysearch');
    ```

2. Vytvořte instanci s `CognitiveServicesCredentials` použitím klíče předplatného. Pak vytvořte instanci klienta vyhledávání s ním.

    ```javascript
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let entitySearchApiClient = new EntitySearchAPIClient(credentials);
    ```

## <a name="send-a-request-and-receive-a-response"></a>Odeslat žádost a přijmout odpověď

1. Odešlete žádost o vyhledávání entit pomocí `entitiesOperations.search()` . Po přijetí odpovědi si vytiskněte `queryContext` počet vrácených výsledků a popis prvního výsledku.

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
> [Vytvoření jednostránkové webové aplikace](../../tutorial-bing-entities-search-single-page-app.md)

* [Co je rozhraní API Bingu pro vyhledávání entit?](../../overview.md)