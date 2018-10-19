---
title: 'Rychlý start: Sada SDK Bingu pro vyhledávání zpráv, Node'
titleSuffix: Azure Cognitive Services
description: Nastavení konzolové aplikace sady SDK Bingu pro vyhledávání zpráv
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: quickstart
ms.date: 02/12/2018
ms.author: v-gedod
ms.openlocfilehash: 2279a6475ab8c39b3ff599f7244caea59d622651
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48802997"
---
# <a name="quickstart-bing-news-search-sdk-with-node"></a>Rychlý start: Sada SDK Bingu pro vyhledávání zpráv s využitím jazyka Node

Sada SDK Bingu pro vyhledávání zpráv obsahuje funkce rozhraní REST API pro dotazy na zprávy a parsování výsledků. 

[Zdrojový kód pro ukázky sady SDK Bingu pro vyhledávání zpráv v Node](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/newsSearch.js) je dostupný na GitHubu.

## <a name="application-dependencies"></a>Závislosti aplikace

Pokud chcete nastavit konzolovou aplikaci pomocí sady SDK Bingu pro vyhledávání zpráv, spusťte ve vašem vývojovém prostředí `npm install azure-cognitiveservices-newssearch`.

## <a name="news-search-client"></a>Klient pro vyhledávání zpráv
Načtěte si pod *Search* (Hledání) [přístupový klíč služeb Cognitive Services](https://azure.microsoft.com/try/cognitive-services/). Vytvoření instance `CognitiveServicesCredentials`:
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
Potom vytvořte instanci klienta:
```
const NewsSearchAPIClient = require('azure-cognitiveservices-newssearch');
let client = new NewsSearchAPIClient(credentials);
```
Pomocí klienta vyhledejte text dotazu, v tomto případě „Winter Olympics“.
```
client.newsOperations.search('Winter Olympics').then((result) => {
    console.log(result.value);
}).catch((err) => {
    throw err;
});

```
Kód vytiskne položky `result.value` do konzoly bez analýzy textu. Případné výsledky podle kategorií budou zahrnovat tyto položky:
- _type: 'NewsArticle'
- _type: 'WebPage'
- _type: 'VideoObject'
- _type: 'ImageObject'

<!-- Remove until we can replace with santized version
![News results](media/node-sdk-quickstart-results.png)
-->

## <a name="next-steps"></a>Další kroky

[Ukázky kognitivních služeb sady Node.js SDK](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
