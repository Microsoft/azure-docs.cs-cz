---
title: Rychlé spuštění vyhledávání SDK uzlu zprávy | Microsoft Docs
description: Nastavit SDK Search zprávy konzolové aplikace
titleSuffix: Azure cognitive services
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 02/12/2018
ms.author: v-gedod
ms.openlocfilehash: 4ae99aa100b697a0dd75863c6f0c3c556dfa3d21
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343665"
---
# <a name="news-search-sdk-node-quickstart"></a>Rychlé spuštění vyhledávání SDK uzlu zprávy

Sada SDK vyhledávání zprávy Bing obsahuje funkci rozhraní REST API pro zprávy dotazy a analýzy výsledků. 

[Zdrojový kód pro uzel Bing zprávy vyhledávání SDK ukázky](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/newsSearch.js) je k dispozici v centru Git.

## <a name="application-dependencies"></a>Závislosti aplikací

Chcete-li nastavit konzolovou aplikaci pomocí sady SDK vyhledávání zprávy Bing, spusťte `npm install azure-cognitiveservices-newssearch` ve vašem vývojovém prostředí.

## <a name="news-search-client"></a>Příspěvky hledání klienta
Získání [kognitivní služby přístupový klíč](https://azure.microsoft.com/try/cognitive-services/) pod *vyhledávání*. Vytvoření instance `CognitiveServicesCredentials`:
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
Potom vytvořte instanci klienta:
```
const NewsSearchAPIClient = require('azure-cognitiveservices-newssearch');
let client = new NewsSearchAPIClient(credentials);
```
Klient použijte při hledání se text dotazu, v tomto případě 'Jedná o zimní olympiáda':
```
client.newsOperations.search('Winter Olympics').then((result) => {
    console.log(result.value);
}).catch((err) => {
    throw err;
});

```
Kód zobrazí `result.value` položky ke konzole bez Analýza textu. Výsledky, pokud existuje podle kategorií, bude zahrnovat:
- _zadejte: 'NewsArticle.
- _zadejte: 'webové stránky.
- _zadejte: 'VideoObject.
- _zadejte: 'ImageObject.

<!-- Remove until we can replace with santized version
![News results](media/node-sdk-quickstart-results.png)
-->

## <a name="next-steps"></a>Další postup

[Kognitivní služby ukázky Node.js SDK](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
