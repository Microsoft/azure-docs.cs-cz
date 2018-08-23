---
title: 'Rychlý start: Žádosti a filtrovat Image pomocí sady SDK v Node.js'
description: V tomto rychlém startu žádosti a filtrovat Image vrácený Bingu pro vyhledávání obrázků, pomocí Node.js.
titleSuffix: Azure cognitive services
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 02/12/2018
ms.author: v-gedod
ms.openlocfilehash: e88c045b220192a617e6b8caf5d8d53f70a25b5e
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2018
ms.locfileid: "41987976"
---
# <a name="quickstart-request-and-filter-images-using-the-sdk-and-nodejs"></a>Rychlý start: Žádost a filtr imagí pomocí sady SDK a Node.js

Sady SDK vyhledávání obrázků Bingu obsahuje funkce rozhraní REST API pro dotazy image a výsledky analýzy. 

[Zdrojový kód pro ukázky SDK vyhledávání obrázků Bingu uzlu](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/imageSearch.js) je k dispozici v centru Git.

## <a name="application-dependencies"></a>Závislosti aplikace

Chcete-li nastavit konzolovou aplikaci pomocí sady SDK vyhledávání obrázků Bingu, spusťte `npm install azure-cognitiveservices-imagesearch` ve vašem vývojovém prostředí.

## <a name="image-search-client"></a>Obrázek hledání klienta
Získání [přístupového klíče služeb Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) pod *hledání*. Vytvoření instance `CognitiveServicesCredentials`:
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
Potom vytvořte instanci klienta:
```
const ImageSearchAPIClient = require('azure-cognitiveservices-imagesearch');
let client = new ImageSearchAPIClient(credentials);
```
Pomocí klienta na hledání text dotazu, v tomto případě "El Capitan":
```
client.imagesOperations.search('El Capitan', function (err, result, request, response) {
    if (err) throw err;
    console.log(result.value);
});

```
<!-- Need to sanitize result
The code prints `result.value` items to the console without parsing any text. The results will be:
- _type: 'ImageObjectElementType'

![Imageresults](media/node-sdk-quickstart-image-results.png)
-->

## <a name="next-steps"></a>Další postup

[Ukázky Node.js SDK služeb cognitive services](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)