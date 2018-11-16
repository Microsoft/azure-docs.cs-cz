---
title: 'Rychlý start: Sada SDK Bingu pro vyhledávání entit, Node'
titleSuffix: Azure Cognitive Services
description: Nastavení pro konzolovou aplikaci sady SDK pro vyhledávání entit za použití jazyka Node.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: quickstart
ms.date: 02/12/2018
ms.author: v-gedod
ms.openlocfilehash: 69bcca7871d9bf4bebf64c0c0ae1b54cd8408927
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2018
ms.locfileid: "51684116"
---
# <a name="quickstart-bing-entity-search-sdk-with-node"></a>Rychlý start: Sada SDK Bingu pro vyhledávání entit pomocí Node

Sada SDK Bingu pro vyhledávání entit obsahuje funkce rozhraní REST API pro dotazy na entity a analýzu výsledků. 

[Zdrojový kód pro ukázky sady SDK Bingu pro vyhledávání entit pomocí jazyka C#](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/entitySearch.js) je k dispozici na GitHubu.
## <a name="application-dependencies"></a>Závislosti aplikace

Nastavení konzolovou aplikaci pomocí sady SDK vyhledávání entit Bingu:
* Spustit `npm install ms-rest-azure` ve vašem vývojovém prostředí.
* Spustit `npm install azure-cognitiveservices-entitysearch` ve vašem vývojovém prostředí.

## <a name="entity-search-client"></a>Klient pro vyhledávání entit
Načtěte si pod *Search* (Hledání) [přístupový klíč služeb Cognitive Services](https://azure.microsoft.com/try/cognitive-services/). Vytvoření instance `CognitiveServicesCredentials`:
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
Potom vytvořte instanci klienta a vyhledejte výsledky:
```
const EntitySearchAPIClient = require('azure-cognitiveservices-entitysearch');

let entitySearchApiClient = new EntitySearchAPIClient(credentials);

entitySearchApiClient.entitiesOperations.search('seahawks').then((result) => {
    console.log(result.queryContext);
    console.log(result.entities.value);
    console.log(result.entities.value[0].description);
}).catch((err) => {
    throw err;
});

```
Kód vytiskne položky `result.value` do konzoly bez analýzy textu.  Případné výsledky podle kategorií budou zahrnovat tyto položky:
- _type: 'Thing'
- _type: 'ImageObject'

<!-- Removing until we can replace with a sanitized version.
![Entity results](media/entity-search-sdk-node-quickstart-results.png)
-->

## <a name="next-steps"></a>Další postup

[Ukázky kognitivních služeb sady Node.js SDK](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)