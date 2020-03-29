---
title: Koncový bod vyhledávání na webu
titleSuffix: Azure Cognitive Services
description: Chcete-li získat výsledky `GET` hledání na webu, odešlete požadavek na následující koncový bod. Záhlaví a parametry adresy URL definují další specifikace.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: aahi
ms.openlocfilehash: 2edc6a4cf4d4fb174c1b2067d8b3fb3b6f31859f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "74111336"
---
# <a name="web-search-endpoint"></a>Koncový bod vyhledávání na webu

Rozhraní **API pro vyhledávání na webu** vrací webové stránky, zprávy, obrázky, videa a [entity](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web). Entity mají souhrnné informace o osobě, místě nebo tématu.

## <a name="endpoint"></a>Koncový bod

Chcete-li získat výsledky hledání na `GET` webu pomocí rozhraní API Bingu, odešlete požadavek na následující koncový bod. Záhlaví a parametry adresy URL definují další specifikace.

**Koncový bod**: Vrátí výsledky webu, které jsou relevantní `?q=""`pro vyhledávací dotaz uživatele definovaný aplikací .

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/search
```

Koncový bod: Podrobnosti o záhlaví, parametry, kódy trhu, objekty odezvy, chyby a další, naleznete v referenční matné rozhraní [Bing web API v7.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)

## <a name="response-json"></a>Odpověď JSON

Odpověď na požadavek na hledání na webu zahrnuje všechny výsledky jako objekty JSON. Analýza výsledku vyžaduje postupy, které zpracovávají prvky každého typu. Podívejte se na [příklady výukového programu](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/tutorial-bing-web-search-single-page-app) a [zdrojového kódu.](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Web-Search)

## <a name="next-steps"></a>Další kroky

Bing **Bing** API podporují akce vyhledávání, které vracejí výsledky podle jejich typu.Všechny koncové body hledání vrátí výsledky jako objekty odezvy JSON. Všechny koncové body podporují dotazy, které vracejí určitý jazyk a umístění podle zeměpisné délky, zeměpisné šířky a poloměru hledání.

Úplné informace o parametrech podporovaných jednotlivými koncovými body naleznete v referenčních stránkách pro každý typ.
Příklady základních požadavků pomocí rozhraní API pro vyhledávání na webu naleznete [v tématu Hledání rychlých spuštění webu](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/search-the-web).
