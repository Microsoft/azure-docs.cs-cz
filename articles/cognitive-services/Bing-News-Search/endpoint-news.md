---
title: Koncové body Vyhledávání zpráv Bingu
titleSuffix: Azure Cognitive Services
description: Tento článek poskytuje souhrn koncových bodů rozhraní API pro vyhledávání zpráv. Novinky, hlavní novinky a novinky trendů.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 1/10/2019
ms.author: aahi
ms.openlocfilehash: 9c095a46b3a0526b23645c9cbb5e99eb8eda9067
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94366396"
---
# <a name="bing-news-search-api-endpoints"></a>rozhraní API Bingu pro vyhledávání zpráv koncové body

> [!WARNING]
> Rozhraní API pro vyhledávání Bingu přesouváte z Cognitive Services na Vyhledávání Bingu služby. Od **30. října 2020** musí být všechny nové instance vyhledávání Bingu zřízené [podle popsaného procesu.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)
> Rozhraní API pro vyhledávání Bingu zřízené pomocí Cognitive Services budou podporované v následujících třech letech nebo na konci smlouva Enterprise, podle toho, co nastane dřív.
> Pokyny k migraci najdete v tématu [vyhledávání Bingu Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

**Rozhraní vyhledávání zpráv API** vrací články, webové stránky, obrázky, videa a [entity](../bing-entities-search/overview.md)s novinkami. Entity obsahují souhrnné informace o osobě, místě nebo tématu.

## <a name="endpoints"></a>Koncové body

Chcete-li získat výsledky hledání zpráv pomocí rozhraní API Bingu pro vyhledávání zpráv, odešlete `GET` žádost jednomu z následujících koncových bodů. Parametry hlaviček a adres URL definují další specifikace.

### <a name="news-items-by-search-query"></a>Položky zpráv podle vyhledávacího dotazu

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search
```

Vrátí položky zpráv na základě vyhledávacího dotazu. Pokud je vyhledávací dotaz prázdný, vrátí rozhraní API hlavní články o novinkách z různých kategorií. Odešlete dotaz podle adresy URL pro kódování hledaného výrazu a připojíte ho k `q=""` parametru. Informace o dostupnosti najdete v článku [Podporované země/oblasti a trhy](language-support.md#supported-markets-for-news-search-endpoint).

### <a name="top-news-items-by-category"></a>Hlavní položky zpráv podle kategorií

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news  
```

Vrátí hlavní položky zpráv podle kategorií. Konkrétně si můžete vyžádat požadavky na nejlepší obchodní, sportovní nebo zábavní články pomocí `category=business` , `category=sports` nebo `category=entertainment` .  `category`Parametr lze použít pouze s `/news` adresou URL. Existují některé formální požadavky pro určení kategorií. informace najdete `category` v dokumentaci k [parametrům dotazu](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#query-parameters) . Odešlete dotaz podle adresy URL pro kódování hledaného výrazu a připojíte ho k `q=""` parametru. Informace o dostupnosti najdete v článku [Podporované země/oblasti a trhy](language-support.md#supported-markets-for-news-endpoint).

### <a name="trending-news-topics"></a>Témata týkající se vývoje zpráv 

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics
```

Vrátí témata se zprávami, které aktuálně trendují v sociálních sítích. Pokud `/trendingtopics` je tato možnost k dispozici, vyhledávání Bingu ignoruje několik dalších parametrů, například `freshness` a `?q=""` . Informace o dostupnosti najdete v článku [Podporované země/oblasti a trhy](language-support.md#supported-markets-for-news-trending-endpoint).

## <a name="next-steps"></a>Další kroky

Podrobnosti o hlavičkách, parametrech, kódech trhu, objektech odpovědí, chybách atd. najdete v referenčních informacích k [rozhraní API Bingu pro vyhledávání zpráv v7](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference) .

Úplné informace o parametrech podporovaných každým koncovým bodem naleznete na referenčních stránkách pro každý typ.
Příklady základních požadavků využívajících rozhraní API pro vyhledávání zpráv naleznete v tématu [vyhledávání zpráv Bingu Quick-Started](/azure/cognitive-services/bing-news-search).