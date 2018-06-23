---
title: Vyhledávání na webu rychlé spuštění uzlu SDK | Microsoft Docs
description: Instalace webové vyhledávání SDK konzolové aplikace.
titleSuffix: Azure cognitive services
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 02/12/2018
ms.author: v-gedod
ms.openlocfilehash: 44f7f97f6c442df3fbb1e5e08189b8db7d4b9db0
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343670"
---
# <a name="web-search-sdk-node-quickstart"></a>Rychlé spuštění webové vyhledávání SDK uzlu

Sada SDK vyhledávání webové služby Bing obsahuje funkci rozhraní REST API pro webové dotazy a analýzy výsledků.

[Zdrojový kód pro vyhledávání sady SDK pro webové služby Bing uzlu ukázky](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/webSearch.js) je k dispozici v centru Git.

## <a name="application-dependencies"></a>Závislosti aplikací

Chcete-li nastavit konzolovou aplikaci pomocí sady SDK vyhledávání webové služby Bing, spusťte `npm install azure-cognitiveservices-websearch` ve vašem vývojovém prostředí.

## <a name="web-search-client"></a>Webový klient vyhledávání
Získání [kognitivní služby přístupový klíč](https://azure.microsoft.com/try/cognitive-services/) pod *vyhledávání*. Vytvoření instance `CognitiveServicesCredentials`:
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
Potom vytvořte instanci klienta:
```
const WebSearchAPIClient = require('azure-cognitiveservices-websearch');
let webSearchApiClient = new WebSearchAPIClient(credentials);
```
Hledat výsledky:
```
webSearchApiClient.web.search('seahawks').then((result) => {
    let properties = ["images", "webPages", "news", "videos"];
    for (let i = 0; i < properties.length; i++) {
        if (result[properties[i]]) {
            console.log(result[properties[i]].value);
        } else {
            console.log(`No ${properties[i]} data`);
        }
    }
}).catch((err) => {
    throw err;
})

```
Kód zobrazí `result.value` položky ke konzole bez Analýza textu.  Výsledky, pokud existuje podle kategorií, bude zahrnovat:
- _zadejte: 'ImageObject.
- _zadejte: 'NewsArticle.
- _zadejte: 'webové stránky.
- _zadejte: 'VideoObjectElementType.

<!-- Remove until this can be replaced with a sanitized version.
![Video results](media/web-search-sdk-node-results.png)
-->

## <a name="next-steps"></a>Další postup

[Kognitivní služby ukázky Node.js SDK](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
