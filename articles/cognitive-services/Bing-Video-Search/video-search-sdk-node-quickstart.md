---
title: 'Rychlý start: Sada SDK k Vyhledávání videí Bingu, Node'
titleSuffix: Azure Cognitive Services
description: Nastavení pro konzolovou aplikaci sady SDK Vyhledávání videí Bingu.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: quickstart
ms.date: 02/12/2018
ms.author: rosh
ms.openlocfilehash: 4dcc9220d4d38bfe34514edd6a3ad47c7a7d4ba8
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2018
ms.locfileid: "47225612"
---
# <a name="quickstart-bing-video-search-sdk-with-node"></a>Rychlý start: Sada SDK k Vyhledávání videí Bingu s Node

Sada SDK Vyhledávání videí Bingu obsahuje funkce rozhraní REST API pro dotazy na videa a parsování výsledků. 

[Zdrojový kód pro ukázky sady SDK Vyhledávání videí Bingu v Node](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/videoSearch.js) je k dispozici v Git Hubu.

## <a name="application-dependencies"></a>Závislosti aplikace

Pokud chcete nastavit konzolovou aplikaci pomocí sady SDK Video vyhledávání Bingu, spusťte `npm install azure-cognitiveservices-videosearch` ve vašem vývojovém prostředí.

## <a name="video-search-client"></a>Klient Vyhledávání videí
Načtěte si pod *Search* (Hledání) [přístupový klíč služeb Cognitive Services](https://azure.microsoft.com/try/cognitive-services/). Vytvoření instance `CognitiveServicesCredentials`:
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
Potom vytvořte instanci klienta:
```
const VideoSearchAPIClient = require('azure-cognitiveservices-videosearch');
let client = new VideoSearchAPIClient(credentials);
```
Vyhledejte výsledky.
```
client.videosOperations.search('Interstellar Trailer').then((result) => {
    console.log(result.value);
}).catch((err) => {
    throw err;
});

```

<!-- Remove until the response can be replace with a sanitized version.
The code prints `result.value` items to the console without parsing any text. The results will be:
- _type: 'VideoObjectElementType'

![Video results](media/video-search-sdk-node-results.png)
-->

## <a name="next-steps"></a>Další kroky

[Ukázky kognitivních služeb sady Node.js SDK](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
