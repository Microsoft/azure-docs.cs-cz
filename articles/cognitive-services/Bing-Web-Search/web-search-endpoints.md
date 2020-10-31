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
ms.openlocfilehash: 25ecd1a753cb9a401408f7ed6605d53e5310df2b
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93075666"
---
# <a name="web-search-endpoint"></a>Vyhledávání na webu koncový bod

> [!WARNING]
> Rozhraní API pro vyhledávání Bingu přesouváte z Cognitive Services na Vyhledávání Bingu služby. Od **30. října 2020** musí být všechny nové instance vyhledávání Bingu zřízené [podle popsaného procesu.](https://aka.ms/cogsvcs/bingmove)
> Rozhraní API pro vyhledávání Bingu zřízené pomocí Cognitive Services budou podporované v následujících třech letech nebo na konci smlouva Enterprise, podle toho, co nastane dřív.
> Pokyny k migraci najdete v tématu [vyhledávání Bingu Services](https://aka.ms/cogsvcs/bingmigration).

**Rozhraní vyhledávání na webu API** vrací webové stránky, novinky, obrázky, videa a [entity](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web). Entity obsahují souhrnné informace o osobě, místě nebo tématu.

## <a name="endpoint"></a>Koncový bod

Chcete-li získat výsledky hledání na webu pomocí rozhraní API Bingu, odešlete `GET` požadavek do následujícího koncového bodu. Parametry hlaviček a adres URL definují další specifikace.

**Koncový bod** : vrátí výsledky webu, které jsou relevantní pro vyhledávací dotaz uživatele definovaný v `?q=""` .

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/search
```

Koncový bod: podrobnosti o hlavičkách, parametrech, kódech trhu, objektech odpovědí, chybách a dalších najdete v referenčních informacích k [v7 webového rozhraní API Bingu](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference) .

## <a name="response-json"></a>Odpověď JSON

Odpověď na požadavek na hledání na webu zahrnuje všechny výsledky jako objekty JSON. Analýza výsledku vyžaduje postupy, které zpracovávají prvky každého typu. Příklady najdete v [kurzu](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/tutorial-bing-web-search-single-page-app) a ve [zdrojovém kódu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Web-Search) .

## <a name="next-steps"></a>Další kroky

Rozhraní API **Bing** podporují akce hledání, které vracejí výsledky podle jejich typu. Všechny koncové body hledání vrátí výsledky jako objekty odezvy JSON.  Všechny koncové body podporují dotazy, které vracejí konkrétní jazyk a umístění podle délky, zeměpisné šířky a poloměru hledání.

Úplné informace o parametrech podporovaných každým koncovým bodem naleznete na referenčních stránkách pro každý typ.
Příklady základních požadavků využívajících rozhraní API pro vyhledávání na webu najdete v tématu věnovaném [hledání na webu v rychlém startu](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/search-the-web).
