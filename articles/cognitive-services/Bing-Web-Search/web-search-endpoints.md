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
ms.openlocfilehash: 2edc6a4cf4d4fb174c1b2067d8b3fb3b6f31859f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "74111336"
---
# <a name="web-search-endpoint"></a>Vyhledávání na webu koncový bod

**Rozhraní vyhledávání na webu API** vrací webové stránky, novinky, obrázky, videa a [entity](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web). Entity obsahují souhrnné informace o osobě, místě nebo tématu.

## <a name="endpoint"></a>Koncový bod

Chcete-li získat výsledky hledání na webu pomocí rozhraní API Bingu, odešlete `GET` požadavek do následujícího koncového bodu. Parametry hlaviček a adres URL definují další specifikace.

**Koncový bod**: vrátí výsledky webu, které jsou relevantní pro vyhledávací dotaz uživatele definovaný v `?q=""` .

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/search
```

Koncový bod: podrobnosti o hlavičkách, parametrech, kódech trhu, objektech odpovědí, chybách a dalších najdete v referenčních informacích k [v7 webového rozhraní API Bingu](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference) .

## <a name="response-json"></a>Odpověď JSON

Odpověď na požadavek na hledání na webu zahrnuje všechny výsledky jako objekty JSON. Analýza výsledku vyžaduje postupy, které zpracovávají prvky každého typu. Příklady najdete v [kurzu](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/tutorial-bing-web-search-single-page-app) a ve [zdrojovém kódu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Web-Search) .

## <a name="next-steps"></a>Další kroky

Rozhraní API **Bing** podporují akce hledání, které vracejí výsledky podle jejich typu.Všechny koncové body hledání vrátí výsledky jako objekty odezvy JSON. Všechny koncové body podporují dotazy, které vracejí konkrétní jazyk a umístění podle délky, zeměpisné šířky a poloměru hledání.

Úplné informace o parametrech podporovaných každým koncovým bodem naleznete na referenčních stránkách pro každý typ.
Příklady základních požadavků využívajících rozhraní API pro vyhledávání na webu najdete v tématu věnovaném [hledání na webu v rychlém startu](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/search-the-web).
