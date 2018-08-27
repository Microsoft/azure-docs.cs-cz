---
title: 'Rychlý start: Použití vyhledávání na webu Bingu sady SDK pro Node.js'
description: Instalační program pro vyhledávání na webu SDK konzolové aplikace.
titleSuffix: Azure cognitive services
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 08/16/2018
ms.author: v-gedod, erhopf
ms.openlocfilehash: e25c295fc0fc144110325d3c494a513ea35aeb05
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/24/2018
ms.locfileid: "42888586"
---
# <a name="quickstart-use-the-bing-web-search-sdk-for-nodejs"></a>Rychlý start: Použití vyhledávání na webu Bingu sady SDK pro Node.js

Sady SDK webové vyhledávání Bingu obsahuje funkce rozhraní REST API pro dotazy a analýzy výsledků.

[Zdrojový kód pro sadu SDK uzel Bingu webové vyhledávání ukázek](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/webSearch.js) je k dispozici na Githubu.

## <a name="application-dependencies"></a>Závislosti aplikace

Chcete-li nastavit konzolovou aplikaci pomocí sady SDK webové vyhledávání Bingu, spusťte `npm install azure-cognitiveservices-websearch` ve vašem vývojovém prostředí.

## <a name="web-search-client"></a>Webový klient vyhledávání
Získání [klíč předplatného služeb Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) pod *hledání*. Vytvoření instance `CognitiveServicesCredentials`:
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
Vytiskne kód `result.value` položky konzoly bez parsování libovolný text.  Výsledky, pokud existuje podle jednotlivých kategorií, bude obsahovat:
- _typ: "ImageObject.
- _typ: "NewsArticle.
- _typ: "webové stránky.
- _typ: "VideoObjectElementType.

<!-- Remove until this can be replaced with a sanitized version.
![Video results](media/web-search-sdk-node-results.png)
-->

## <a name="next-steps"></a>Další postup

[Ukázky Node.js SDK služeb cognitive services](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
