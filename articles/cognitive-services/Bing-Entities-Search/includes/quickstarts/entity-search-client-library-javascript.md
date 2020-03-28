---
title: Rychlý start klientské knihovny JavaScript pro vyhledávání entit Bingu
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/06/2020
ms.author: aahi
ms.openlocfilehash: ac2f83c824014e16cfbe9ab18483b914ed8b077d
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "79136764"
---
Pomocí tohoto rychlého startu můžete začít hledat entity v klientské knihovně Hledání entit Bingu pro JavaScript. Zatímco hledání entit Bingu má rozhraní REST API kompatibilní s většinou programovacích jazyků, klientská knihovna poskytuje snadný způsob integrace služby do vašich aplikací. Zdrojový kód pro tuto ukázku lze nalézt na [GitHubu](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/entitySearch.js).

## <a name="prerequisites"></a>Požadavky

* Nejnovější verze [Node.js](https://nodejs.org/en/download/)

* Sada [SDK hledání entit Bingu pro soubor Node.js](https://www.npmjs.com/package/@azure/cognitiveservices-entitysearch)

Instalace sady SDK pro vyhledávání entit Bingu:

1. Spouštějte `npm install ms-rest-azure` ve vývojovém prostředí.
2. Spouštějte `npm install @azure/cognitiveservices-entitysearch` ve vývojovém prostředí.

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-entity-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Vytvoření a inicializace aplikace

1. Vytvořte nový soubor JavaScriptu ve svém oblíbeném rozhraní IDE nebo editoru a přidejte následující požadavky.

    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    const EntitySearchAPIClient = require('@azure/cognitiveservices-entitysearch');
    ```

2. Vytvořte instanci `CognitiveServicesCredentials` použití klíče předplatného. Pak s ním vytvořte instanci vyhledávacího klienta.

    ```javascript
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let entitySearchApiClient = new EntitySearchAPIClient(credentials);
    ```

## <a name="send-a-request-and-receive-a-response"></a>Odeslání žádosti a přijetí odpovědi

1. Odeslat požadavek na `entitiesOperations.search()`vyhledávání entit s . Po obdržení odpovědi vytiskněte `queryContext`počet vrácených výsledků a popis prvního výsledku.

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

* [Co je rozhraní API pro vyhledávání entit Bingu?](../../overview.md)