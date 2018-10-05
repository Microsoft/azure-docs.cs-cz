---
title: Sada SDK pro vyhledávání zpráv Bingu
titleSuffix: Azure Cognitive Services
description: SDK vyhledávání zpráv Bingu pro aplikace, které vyhledávání na webu.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: conceptual
ms.date: 1/24/2018
ms.author: v-gedod
ms.openlocfilehash: b1d9eaa35416adfa11647f2116171256f82fe095
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48801229"
---
# <a name="bing-search-sdk"></a>Vyhledávání Bingu SDK
Ukázky rozhraní API pro vyhledávání zpráv Bingu patří scénáře, které:
1. Novinky dotázat hledané výrazy s `market` a `count` parametry, zkontrolujte počet výsledků a vytiskne `totalEstimatedMatches`, jméno, adresu URL, popis, času publikování a název zprostředkovatele první výsledek zprávy.
2. Nejnovější informace dotázat hledané výrazy s `freshness` a `sortBy` parametry, zkontrolujte počet výsledků a vytiskne `totalEstimatedMatches`, adresa URL, popis, času publikování a název zprostředkovatele první výsledek zprávy.
3. Kategorie zpráv pro dotaz `movie` a `TV entertainment` s bezpečného hledání, zkontrolujte počet výsledků a vytiskne kategorie, název, URL, popis, času publikování a název zprostředkovatele první výsledek zprávy.
4. Dotaz populárních tématech zpráv v Bingu, zkontrolujte počet výsledků a vytiskne název, text příkazu jazyka `webSearchUrl`, `newsSearchUrl` a adresa URL první zprávy výsledku obrázku.

Sady SDK pro vyhledávání Bingu zpřístupnit webové funkce vyhledávání snadno do těchto programovacích jazycích:
* Začínáme s [ukázky .NET](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
    * [Balíček NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.NewsSearch/1.2.0)
    * Viz také [knihovny .NET](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Search/BingNewsSearch) pro definice a závislosti.
* Začínáme s [ukázky Node.js](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples) 
    * Viz také [knihoven Node.js](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/newsSearch) pro definice a závislosti.
* Začínáme s [ukázky v Javě](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples) 
    * Viz také [knihovny Java](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingNewsSearch) pro definice a závislosti.
* Začínáme s [ukázky Pythonu](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples) 
    * Viz také [knihoven Pythonu](https://github.com/Azure/azure-sdk-for-python/tree/master/azure-cognitiveservices-search-newssearch) pro definice a závislosti.

Ukázky sady SDK pro jednotlivé jazyky zahrnují soubor ReadMe s podrobnostmi o předpokladů a instalaci a spuštění ukázky.