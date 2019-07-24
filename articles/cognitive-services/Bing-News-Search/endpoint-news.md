---
title: Koncové body Vyhledávání zpráv Bingu
titleSuffix: Azure Cognitive Services
description: Souhrn koncového bodu rozhraní API pro vyhledávání zpráv
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 1/10/2019
ms.author: aahi
ms.openlocfilehash: 82e37e8fa47b467e7c2fe98f801482675809a266
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423777"
---
# <a name="bing-news-search-api-endpoints"></a>rozhraní API Bingu pro vyhledávání zpráv koncové body

**Rozhraní vyhledávání zpráv API** vrací články, webové stránky, obrázky, videa a [entity](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web)s novinkami. Entity obsahují souhrnné informace o osobě, místě nebo tématu.

## <a name="endpoints"></a>Koncové body

Chcete-li získat výsledky hledání zpráv pomocí rozhraní API Bingu pro vyhledávání zpráv, odešlete `GET` žádost jednomu z následujících koncových bodů. Parametry hlaviček a adres URL definují další specifikace.

### <a name="news-items-by-search-query"></a>Položky zpráv podle vyhledávacího dotazu

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search
```

Vrátí položky zpráv na základě vyhledávacího dotazu. Pokud je vyhledávací dotaz prázdný, vrátí rozhraní API hlavní články o novinkách z různých kategorií. Odešlete dotaz podle adresy URL pro kódování hledaného výrazu a připojíte ho`q=""` k parametru. Informace o dostupnosti najdete v článku [Podporované země/oblasti a trhy](language-support.md#supported-markets-for-news-search-endpoint).

### <a name="top-news-items-by-category"></a>Hlavní položky zpráv podle kategorií

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news  
```

Vrátí hlavní položky zpráv podle kategorií. Konkrétně si můžete vyžádat požadavky na nejlepší obchodní, sportovní nebo zábavní články `category=business`pomocí `category=sports`, nebo `category=entertainment`.  Parametr lze použít pouze `/news` s adresou URL. `category` Existují některé formální požadavky pro určení kategorií. informace najdete `category` v dokumentaci k [parametrům dotazu](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#query-parameters) . Odešlete dotaz podle adresy URL pro kódování hledaného výrazu a připojíte ho`q=""` k parametru. Informace o dostupnosti najdete v článku [Podporované země/oblasti a trhy](language-support.md#supported-markets-for-news-endpoint).

### <a name="trending-news-topics"></a>Témata týkající se vývoje zpráv 

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics
```

Vrátí témata se zprávami, které aktuálně trendují v sociálních sítích. Pokud je `/trendingtopics` Tato možnost k dispozici, vyhledávání Bingu ignoruje několik dalších `freshness` parametrů `?q=""`, například a. Informace o dostupnosti najdete v článku [Podporované země/oblasti a trhy](language-support.md#supported-markets-for-news-trending-endpoint).

## <a name="next-steps"></a>Další postup

Podrobnosti o hlavičkách, parametrech, kódech trhu, objektech odpovědí, chybách atd. najdete v referenčních informacích k [rozhraní API Bingu pro vyhledávání zpráv v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference) .

Úplné informace o parametrech podporovaných každým koncovým bodem naleznete na referenčních stránkách pro každý typ.
Příklady základních požadavků využívajících rozhraní API pro vyhledávání zpráv naleznete v tématu [vyhledávání zpráv Bingu Quick-Started](https://docs.microsoft.com/azure/cognitive-services/bing-news-search).
