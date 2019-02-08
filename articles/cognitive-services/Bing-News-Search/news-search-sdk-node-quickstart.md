---
title: 'Rychlý start: Hledání zpráv - Bingu News Search SDK pro Node.js'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu slouží k vyhledání zpráv pro Node.js pomocí sady SDK vyhledávání zpráv Bingu a zpracování odpovědi.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 01/10/2019
ms.author: v-gedod
ms.custom: seodec2018
ms.openlocfilehash: 262f7f49987dba8340ba4a1f0e6e505b9858e1e5
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55863869"
---
# <a name="quickstart-perform-a-news-search-with-the-bing-news-search-sdk-for-nodejs"></a>Rychlý start: Provádění sadou SDK pro vyhledávání zpráv Bingu pro vyhledávání zpráv pro Node.js

V tomto rychlém startu pomocí zahájíte hledání zpráv pomocí sady SDK vyhledávání zpráv Bingu pro Node.js. Při vyhledávání zpráv Bingu je kompatibilní s Většina programovacích jazyků rozhraní REST API, sada SDK poskytuje snadný způsob, jak do svých aplikací integrovat službu. Zdrojový kód pro tuto ukázku můžete najít na [Githubu](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/newsSearch.js).

## <a name="prerequisites"></a>Požadavky

* [Node.js](https://nodejs.org/en/)

Nastavení konzolovou aplikaci pomocí sady SDK vyhledávání zpráv Bingu:
1. Spustit `npm install ms-rest-azure` ve vašem vývojovém prostředí.
2. Spustit `npm install azure-cognitiveservices-newssearch` ve vašem vývojovém prostředí.


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Vytvoření a inicializace aplikace

1. Vytvořte instanci `CognitiveServicesCredentials`. Vytváření proměnných pro váš klíč susbcription a hledaný termín.

    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let search_term = 'Winter Olympics'
    ```

2. Vytvořit instanci klienta:
    
    ```javascript
    const NewsSearchAPIClient = require('azure-cognitiveservices-newssearch');
    let client = new NewsSearchAPIClient(credentials);
    ```

## <a name="send-a-search-query"></a>Odeslat vyhledávací dotaz

3. Pomocí klienta na hledání termín dotazu, v tomto případě "Olympijské hry":
    
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

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
[Vytvoření jednostránkové webové aplikace](tutorial-bing-news-search-single-page-app.md)
