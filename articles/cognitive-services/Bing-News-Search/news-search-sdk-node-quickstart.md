---
title: 'Rychlý start: Hledání zpráv - Bingu News Search SDK pro Node.js'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu slouží k vyhledání zpráv pro Node.js pomocí sady SDK vyhledávání zpráv Bingu a zpracování odpovědi.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: quickstart
ms.date: 02/12/2018
ms.author: v-gedod
ms.custom: seodec2018
ms.openlocfilehash: 0fa78474d6400f890626859c32915c8657f92d4e
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2018
ms.locfileid: "53249341"
---
# <a name="quickstart-perform-a-news-search-with-the-bing-news-search-sdk-for-nodejs"></a>Rychlý start: Provádění sadou SDK pro vyhledávání zpráv Bingu pro vyhledávání zpráv pro Node.js

Sada SDK Bingu pro vyhledávání zpráv obsahuje funkce rozhraní REST API pro dotazy na zprávy a parsování výsledků. 

[Zdrojový kód pro ukázky sady SDK Bingu pro vyhledávání zpráv v Node](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/newsSearch.js) je dostupný na GitHubu.

## <a name="application-dependencies"></a>Závislosti aplikace
Načtěte si pod **Search** (Hledání) [přístupový klíč služeb Cognitive Services](https://azure.microsoft.com/try/cognitive-services/).  Viz také [služeb Cognitive Services ceny – rozhraní API Bingu pro vyhledávání](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/). 

Nastavení konzolovou aplikaci pomocí sady SDK vyhledávání zpráv Bingu:
* Spustit `npm install ms-rest-azure` ve vašem vývojovém prostředí.
* Spustit `npm install azure-cognitiveservices-newssearch` ve vašem vývojovém prostředí.

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
- _typ: "NewsArticle.
- _typ: "Webová stránka.
- _typ: "VideoObject.
- _typ: "ImageObject.

<!-- Remove until we can replace with santized version
![News results](media/node-sdk-quickstart-results.png)
-->

## <a name="next-steps"></a>Další postup

[Ukázky kognitivních služeb sady Node.js SDK](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
