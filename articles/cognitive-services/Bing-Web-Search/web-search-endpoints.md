---
title: Koncový bod webové vyhledávání
titleSuffix: Azure Cognitive Services
description: Souhrn koncový bod rozhraní Web search API.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 11/28/2017
ms.author: v-gedod
ms.openlocfilehash: 3058ca6cf0eb99486dd4c269d43b274fb367f7a9
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "46125455"
---
# <a name="web-search-endpoint"></a>Koncový bod webové vyhledávání
**Webové rozhraní API pro vyhledávání** vrátí webové stránky, zpráv, obrázky, videa, a [entity](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web). Entity obsahují souhrnné informace o osobě, místě nebo téma.
## <a name="endpoint"></a>Koncový bod
Chcete-li získat výsledky hledání na webu pomocí rozhraní API Bingu pro odeslání `GET` požadavek na následující koncový bod. Specifikace definovat další hlaviček a parametrů adresy URL.

**Koncový bod**: vrátí webové výsledky, které jsou relevantní pro daného uživatele vyhledat dotaz definovaný sadou `?q=""`.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/search
```
Koncový bod: Další informace o hlavičkách, parametry, uvedení na trh kódy, objekty odpovědi, chyby atd., najdete v článku [webového rozhraní API Bingu v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference) odkaz.

## <a name="response-json"></a>Odpověď JSON
Odpověď na webový požadavek hledání obsahuje všechny výsledky jako objekty JSON. Analýza kódu výsledku vyžaduje postupy, které zpracovávají elementy jednotlivé typy. Zobrazit [kurzu](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/tutorial-bing-web-search-single-page-app) a [zdrojový kód](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/tutorial-bing-web-search-single-page-app-source) příklady.

## <a name="next-steps"></a>Další postup
**Bingu** rozhraní API podporují akce hledání, které vracejí výsledky podle jejich typu. Všechny koncové body hledání vrátí výsledky jako objekty JSON odpovědi.  Všechny koncové body podporují dotazy využívající vrátit konkrétní jazyk a/nebo umístění longitude, latitude a vyhledávání protokolu radius.

Podrobnější informace o parametrech podporuje každý koncový bod najdete v referenčních stránkách pro jednotlivé typy.
Příklady základní požadavky pomocí rozhraní API pro webové vyhledávání, najdete v článku [rychlé zprovoznění webové vyhledávání](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/search-the-web).
