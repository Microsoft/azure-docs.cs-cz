---
title: Hledání Bing SDK | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Bing vyhledávání SDK pro aplikace, které vyhledávání na webu.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.assetid: ''
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 1/24/2018
ms.author: v-gedod
ms.openlocfilehash: 4a40ea665e153536d2322706b455598902ce41eb
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343660"
---
# <a name="bing-search-sdk"></a>Hledání Bing SDK
Ukázky rozhraní API pro Bing příspěvky hledání zahrnout scénáře který:
1. Dotaz zprávy podmínek vyhledávání s `market` a `count` parametry, ověřte počet výsledků a vytiskněte `totalEstimatedMatches`, název, URL, popis, publikované čas a název zprostředkovatele první výsledek zprávy.
2. Vyhledat nejnovější příspěvky hledaných termínů s `freshness` a `sortBy` parametry, ověřte počet výsledků a vytiskněte `totalEstimatedMatches`, adresa URL, popis, publikované čas a název zprostředkovatele první výsledek zprávy.
3. Dotaz kategorie zprávy pro `movie` a `TV entertainment` s bezpečné vyhledávání, ověřte počet výsledků a vytiskněte kategorie, název, URL, popis, publikované čas a název zprostředkovatele první výsledek zprávy.
4. Dotaz trendů témata zprávy v Bing, ověřte počet výsledků a vytiskněte název, text dotazu, `webSearchUrl`, `newsSearchUrl` a adresa URL první zprávy výsledku obrázku.

Sady SDK vyhledávání Bing zpřístupněte funkce vyhledávání web snadno v těchto programovacích jazycích:
* Začínáme s [.NET – ukázky](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
    * [Balíček NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.NewsSearch/1.2.0)
    * Viz také [knihovny .NET](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Search/BingNewsSearch) pro definice a závislosti.
* Začínáme s [ukázky Node.js](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples) 
    * Viz také [Node.js knihovny](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/newsSearch) pro definice a závislosti.
* Začínáme s [ukázky Java](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples) 
    * Viz také [Java knihovny](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingNewsSearch) pro definice a závislosti.
* Začínáme s [ukázky Python](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples) 
    * Viz také [Python knihovny](https://github.com/Azure/azure-sdk-for-python/tree/master/azure-cognitiveservices-search-newssearch) pro definice a závislosti.

Ukázky sady SDK pro každý jazyk můžete zahrnout soubor ReadMe s podrobnostmi o požadavcích a instalaci nebo spuštění ukázky.