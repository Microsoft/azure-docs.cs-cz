---
title: Koncový bod vyhledávání na webu
titleSuffix: Azure Cognitive Services
description: Chcete-li získat výsledky hledání na webu, odešlete `GET` požadavek do následujícího koncového bodu. Parametry hlaviček a adres URL definují další specifikace.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: aahi
ms.openlocfilehash: c882e3e4d0cd6ba594a700f4fd53c14103a8d1d1
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2020
ms.locfileid: "94381248"
---
# <a name="web-search-endpoint"></a>Vyhledávání na webu koncový bod

> [!WARNING]
> Rozhraní API pro vyhledávání Bingu přesouváte z Cognitive Services na Vyhledávání Bingu služby. Od **30. října 2020** musí být všechny nové instance vyhledávání Bingu zřízené [podle popsaného procesu.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)
> Rozhraní API pro vyhledávání Bingu zřízené pomocí Cognitive Services budou podporované v následujících třech letech nebo na konci smlouva Enterprise, podle toho, co nastane dřív.
> Pokyny k migraci najdete v tématu [vyhledávání Bingu Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

**Rozhraní vyhledávání na webu API** vrací webové stránky, novinky, obrázky, videa a [entity](../bing-entities-search/overview.md). Entity obsahují souhrnné informace o osobě, místě nebo tématu.

## <a name="endpoint"></a>Koncový bod

Chcete-li získat výsledky hledání na webu pomocí rozhraní API Bingu, odešlete `GET` požadavek do následujícího koncového bodu. Parametry hlaviček a adres URL definují další specifikace.

**Koncový bod** : vrátí výsledky webu, které jsou relevantní pro vyhledávací dotaz uživatele definovaný v `?q=""` .

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/search
```

Koncový bod: podrobnosti o hlavičkách, parametrech, kódech trhu, objektech odpovědí, chybách a dalších najdete v referenčních informacích k [v7 webového rozhraní API Bingu](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference) .

## <a name="response-json"></a>Odpověď JSON

Odpověď na požadavek na hledání na webu zahrnuje všechny výsledky jako objekty JSON. Analýza výsledku vyžaduje postupy, které zpracovávají prvky každého typu. Příklady najdete v [kurzu](./tutorial-bing-web-search-single-page-app.md) a ve [zdrojovém kódu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Web-Search) .

## <a name="next-steps"></a>Další kroky

Rozhraní API **Bing** podporují akce hledání, které vracejí výsledky podle jejich typu. Všechny koncové body hledání vrátí výsledky jako objekty odezvy JSON.  Všechny koncové body podporují dotazy, které vracejí konkrétní jazyk a umístění podle délky, zeměpisné šířky a poloměru hledání.

Úplné informace o parametrech podporovaných každým koncovým bodem naleznete na referenčních stránkách pro každý typ.
Příklady základních požadavků využívajících rozhraní API pro vyhledávání na webu najdete v tématu věnovaném [hledání na webu v rychlém startu](./overview.md).