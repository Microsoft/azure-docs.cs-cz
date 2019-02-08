---
title: Koncové body Vyhledávání zpráv Bingu
titlesuffix: Azure Cognitive Services
description: Přehled koncového bodu rozhraní API hledání zpráv.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 1/10/2019
ms.author: v-gedod
ms.openlocfilehash: a3badf7772c6be6509608fbdb305dc3bc225acc0
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55879984"
---
# <a name="bing-news-search-api-endpoints"></a>Koncové body rozhraní API pro vyhledávání zpráv Bingu

**Rozhraní API pro vyhledávání zpráv** vrátí články, webových stránek, obrázků, videa, zprávy a [entity](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web). Entity obsahují souhrnné informace o osobě, místě nebo téma.

## <a name="endpoints"></a>Koncové body

Chcete-li dostávat informace o výsledcích pomocí rozhraní API pro vyhledávání zpráv Bingu, odešlete `GET` požadavek na jednu z následujících koncových bodů. Specifikace definovat další hlaviček a parametrů adresy URL.

### <a name="news-items-by-search-query"></a>Příspěvky ve vyhledávací dotaz

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search
```

Vrátí příspěvků na základě dotazu hledání. Pokud vyhledávacímu dotazu je prázdný, rozhraní API vrátí hlavní vybrané články z různých kategorií. Odeslat dotaz pomocí adresy url kódování hledaný termín a umožňuje připojení`q=""` parametru. Dostupnost, naleznete v tématu [podporované země a trhy](language-support.md#supported-markets-for-news-search-endpoint).

### <a name="top-news-items-by-category"></a>Hlavní příspěvky podle kategorie

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news  
```

Vrátí hlavní příspěvky podle kategorie. Konkrétně žádosti špičková obchodní, sportu nebo Zábava článků pomocí `category=business`, `category=sports`, nebo `category=entertainment`.  `category` Parametr jde použít jenom s `/news` adresy URL. Existují některé formální požadavky pro určení kategorií; odkazovat na `category` v [parametr dotazu](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#query-parameters) dokumentaci. Odeslat dotaz pomocí adresy url kódování hledaný termín a umožňuje připojení`q=""` parametru. Dostupnost, naleznete v tématu [podporované země a trhy](language-support.md#supported-markets-for-news-endpoint).

### <a name="trending-news-topics"></a>Populárních tématech zpráv 

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics
```

Vrátí témata zpráv, které jsou aktuálně trendů v sociálních sítích. Když `/trendingtopics` je zadán parametr, vyhledávání Bingu ignoruje několik dalších parametrů, jako například `freshness` a `?q=""`. Dostupnost, naleznete v tématu [podporované země a trhy](language-support.md#supported-markets-for-news-trending-endpoint).

## <a name="next-steps"></a>Další postup

Podrobnosti o záhlaví, parametry, kódy na trhu, objekty odpovědi, chyby dále, viz [vyhledávání zpráv Bingu, rozhraní API v7 nabízí](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference) odkaz.

Podrobnější informace o parametrech podporuje každý koncový bod najdete v referenčních stránkách pro jednotlivé typy.
Příklady základní požadavky pomocí rozhraní API pro vyhledávání zpráv, najdete v článku [rychlé zprovoznění Bingu pro vyhledávání zpráv](https://docs.microsoft.com/azure/cognitive-services/bing-news-search).
