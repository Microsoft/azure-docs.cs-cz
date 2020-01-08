---
title: 'Rychlý Start: provedení hledání zpráv pomocí sady SDK pro Node. js – Vyhledávání zpráv Bingu'
titleSuffix: Azure Cognitive Services
description: Pomocí tohoto rychlého startu můžete vyhledat zprávy pomocí Vyhledávání zpráv Bingu SDK pro Node. js a zpracovat odpověď.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 12/12/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 832ce90db1d4eac0ef8db87c10a5bc2a1658216d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75383188"
---
# <a name="quickstart-perform-a-news-search-with-the-bing-news-search-sdk-for-nodejs"></a>Rychlý Start: provedení zprávy s Vyhledávání zpráv Bingu SDK pro Node. js

V tomto rychlém startu můžete začít hledat zprávy s Vyhledávání zpráv Bingu SDK pro Node. js. I když Vyhledávání zpráv Bingu má REST API kompatibilní s většinou programovacích jazyků, poskytuje sada SDK snadný způsob, jak integrovat službu do vašich aplikací. Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/newsSearch.js)

## <a name="prerequisites"></a>Požadavky

* [Node.js](https://nodejs.org/en/)

Nastavení konzolové aplikace pomocí sady Vyhledávání zpráv Bingu SDK:
1. Spusťte `npm install ms-rest-azure` ve vývojovém prostředí.
2. Spusťte `npm install azure-cognitiveservices-newssearch` ve vývojovém prostředí.


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Vytvoření a inicializace aplikace

1. Vytvořte instanci `CognitiveServicesCredentials`. Vytvořte proměnné pro svůj klíč předplatného a hledaný termín.

    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let search_term = 'Winter Olympics'
    ```

2. vytvoření instance klienta:
    
    ```javascript
    const NewsSearchAPIClient = require('azure-cognitiveservices-newssearch');
    let client = new NewsSearchAPIClient(credentials);
    ```

## <a name="send-a-search-query"></a>Odeslat vyhledávací dotaz

1. Použijte klienta pro hledání v termínu dotazu, v tomto případě "zimní olympiáda":
    
    ```javascript
    client.newsOperations.search(search_term).then((result) => {
        console.log(result.value);
    }).catch((err) => {
        throw err;
    });
    ```

Kód vytiskne položky `result.value` do konzoly bez analýzy textu. Případné výsledky podle kategorií budou zahrnovat tyto položky:

- `_type: 'NewsArticle'`
- `_type: 'WebPage'`
- `_type: 'VideoObject'`
- `_type: 'ImageObject'`

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření jednostránkové webové aplikace](tutorial-bing-news-search-single-page-app.md)
