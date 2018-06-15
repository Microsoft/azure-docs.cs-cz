---
title: Video Rychlé spuštění uzlu vyhledávání SDK | Microsoft Docs
description: Instalační program pro vyhledávání SDK Video konzolové aplikace.
titleSuffix: Azure cognitive services
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: article
ms.date: 02/12/2018
ms.author: v-gedod
ms.openlocfilehash: 5718c750288e0a5605db3296d2911cca5e03375c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "35343820"
---
# <a name="video-search-sdk-node-quickstart"></a>Video vyhledávání SDK uzlu rychlý start

Sada SDK Bing Video vyhledávání obsahuje funkci rozhraní REST API pro video dotazy a analýzy výsledků. 

[Zdrojový kód pro uzel Bing Video vyhledávání SDK ukázky](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/videoSearch.js) je k dispozici v centru Git.

## <a name="application-dependencies"></a>Závislosti aplikací

Chcete-li nastavit konzolovou aplikaci pomocí sady SDK Bing Video vyhledávání, spusťte `npm install azure-cognitiveservices-videosearch` ve vašem vývojovém prostředí.

## <a name="video-search-client"></a>Video vyhledávání klienta
Získání [kognitivní služby přístupový klíč](https://azure.microsoft.com/try/cognitive-services/) pod *vyhledávání*. Vytvoření instance `CognitiveServicesCredentials`:
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
Potom vytvořte instanci klienta:
```
const VideoSearchAPIClient = require('azure-cognitiveservices-videosearch');
let client = new VideoSearchAPIClient(credentials);
```
Hledat výsledky.
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

## <a name="next-steps"></a>Další postup

[Kognitivní služby ukázky Node.js SDK](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
