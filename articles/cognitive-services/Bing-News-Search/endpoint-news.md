---
title: Koncové body Vyhledávání zpráv Bingu
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje souhrn koncových bodů rozhraní API pro vyhledávání zpráv; novinky, top novinky a trendy novinky.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 1/10/2019
ms.author: aahi
ms.openlocfilehash: dc7d16fe809e3e324f384b0d9e088dd7e6ab261c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "74111480"
---
# <a name="bing-news-search-api-endpoints"></a>Koncové body rozhraní API pro vyhledávání zpráv bingu

**Rozhraní NEWS Search API** vrací diskusní články, webové stránky, obrázky, videa a [entity](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web). Entity obsahují souhrnné informace o osobě, místě nebo tématu.

## <a name="endpoints"></a>Koncové body

Chcete-li získat výsledky vyhledávání zpráv pomocí `GET` rozhraní API pro vyhledávání zpráv Bingu, odešlete požadavek na jeden z následujících koncových bodů. Záhlaví a parametry adresy URL definují další specifikace.

### <a name="news-items-by-search-query"></a>Novinky podle vyhledávacího dotazu

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search
```

Vrátí diskusní položky založené na vyhledávacím dotazu. Pokud je vyhledávací dotaz prázdný, rozhraní API vrátí hlavní zpravodajské články z různých kategorií. Odešlete dotaz podle adresy URL, která kóduje hledaný termín, a připojíte jej k parametru.`q=""` Informace o dostupnosti naleznete v [tématu Podporované země nebo oblasti a trhy](language-support.md#supported-markets-for-news-search-endpoint).

### <a name="top-news-items-by-category"></a>Nejlepší novinky podle kategorií

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news  
```

Vrátí horní novinky podle kategorie. Můžete si konkrétně vyžádat nejlepší obchodní, `category=business` `category=sports`sportovní `category=entertainment`nebo zábavní články pomocí aplikace , nebo . Parametr `category` lze použít pouze `/news` s adresou URL. Existují některé formální požadavky pro určení kategorií; naleznete `category` v dokumentaci [k parametru dotazu.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#query-parameters) Odešlete dotaz podle adresy URL, která kóduje hledaný termín, a připojíte jej k parametru.`q=""` Informace o dostupnosti naleznete v [tématu Podporované země nebo oblasti a trhy](language-support.md#supported-markets-for-news-endpoint).

### <a name="trending-news-topics"></a>Populární témata novinky 

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics
```

Vrátí témata zpráv, která jsou aktuálně populární na sociálních sítích. Pokud `/trendingtopics` je tato možnost zahrnuta, hledání bingem `freshness` ignoruje `?q=""`několik dalších parametrů, například a . Informace o dostupnosti naleznete v [tématu Podporované země nebo oblasti a trhy](language-support.md#supported-markets-for-news-trending-endpoint).

## <a name="next-steps"></a>Další kroky

Podrobnosti o záhlavích, parametrech, kódech trhu, objektech odpovědí, chybách atd., naleznete v referenční příručce [rozhraní API pro vyhledávání zpráv Bing u společnosti V7.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference)

Úplné informace o parametrech podporovaných jednotlivými koncovými body naleznete v referenčních stránkách pro každý typ.
Příklady základních požadavků pomocí rozhraní API pro vyhledávání zpráv naleznete v [tématu Rychlé spuštění hledání zpráv bingu](https://docs.microsoft.com/azure/cognitive-services/bing-news-search).
