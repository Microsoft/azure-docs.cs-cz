---
title: Hledání entity rychlé spuštění uzlu SDK | Microsoft Docs
description: Instalační program pro vyhledávání Entity SDK konzolové aplikace.
titleSuffix: Azure cognitive services
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: article
ms.date: 02/12/2018
ms.author: v-gedod
ms.openlocfilehash: 2904ecfed33334458f9b6a9ca2500cd0bfef13bc
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "35343816"
---
# <a name="entity-search-sdk-node-quickstart"></a>Rychlé spuštění vyhledávání SDK uzlu entity

Sada SDK vyhledávání Entity Bing obsahuje funkci rozhraní REST API pro dotazy na entity a analýza výsledků. 

[Zdrojového kódu pro C# Bing Entity vyhledávání SDK ukázky](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/entitySearch.js) je k dispozici v centru Git.
## <a name="application-dependencies"></a>Závislosti aplikací

Chcete-li nastavit konzolovou aplikaci pomocí sady SDK vyhledávání Entity Bing, spusťte `npm install azure-cognitiveservices-entitysearch` ve vašem vývojovém prostředí.

## <a name="entity-search-client"></a>Klienta vyhledávání entity
Získání [kognitivní služby přístupový klíč](https://azure.microsoft.com/try/cognitive-services/) pod *vyhledávání*. Vytvoření instance `CognitiveServicesCredentials`:
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
Pak vytvořte instanci klienta a hledat výsledky:
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
Kód zobrazí `result.value` položky ke konzole bez Analýza textu.  Výsledky, pokud existuje podle kategorií, bude zahrnovat:
- _zadejte: 'věcí.
- _zadejte: 'ImageObject.

<!-- Removing until we can replace with a sanitized version.
![Entity results](media/entity-search-sdk-node-quickstart-results.png)
-->

## <a name="next-steps"></a>Další postup

[Kognitivní služby ukázky Node.js SDK](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)