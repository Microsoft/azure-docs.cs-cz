---
title: Obrázek hledání rychlé spuštění uzlu SDK | Microsoft Docs
description: Instalace bitové kopie vyhledávání SDK konzolové aplikace.
titleSuffix: Azure cognitive services
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 02/12/2018
ms.author: v-gedod
ms.openlocfilehash: e4c8303e39accbb7caec15c0ef47d701971ce632
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343655"
---
# <a name="image-search-sdk-node-quickstart"></a>Rychlé spuštění vyhledávání SDK uzlu bitové kopie

Sada SDK vyhledávání bitové kopie Bingu obsahuje funkci rozhraní REST API pro dotazy na bitovou kopii a analýza výsledků. 

[Zdrojový kód pro ukázky SDK Search bitové kopie Bingu uzlu](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/imageSearch.js) je k dispozici v centru Git.

## <a name="application-dependencies"></a>Závislosti aplikací

Chcete-li nastavit konzolovou aplikaci pomocí sady SDK vyhledávání bitové kopie Bingu, spusťte `npm install azure-cognitiveservices-imagesearch` ve vašem vývojovém prostředí.

## <a name="image-search-client"></a>Obrázek hledání klienta
Získání [kognitivní služby přístupový klíč](https://azure.microsoft.com/try/cognitive-services/) pod *vyhledávání*. Vytvoření instance `CognitiveServicesCredentials`:
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
Potom vytvořte instanci klienta:
```
const ImageSearchAPIClient = require('azure-cognitiveservices-imagesearch');
let client = new ImageSearchAPIClient(credentials);
```
Klient použijte při hledání se text dotazu, v tomto případě 'El Capitan':
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

[Kognitivní služby ukázky Node.js SDK](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)