---
title: Koncové body Vyhledávání entit Bingu
titlesuffix: Azure Cognitive Services
description: Přehled koncového bodu rozhraní API pro vyhledávání entit Bingu.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: conceptual
ms.date: 12/05/2017
ms.author: v-gedod
ms.openlocfilehash: d781a4b3cd0119f5624b4dd20b514894ea339414
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48816578"
---
# <a name="entity-search-endpoint"></a>Koncový bod vyhledávání entit
**API pro vyhledávání entit** zahrnuje jeden koncový bod, který vrací entity z webu na základě dotazu.

## <a name="endpoint"></a>Koncový bod
Chcete-li získat entity výsledků s použitím **rozhraní API Bingu pro**, odeslat `GET` požadavek na následující koncový bod. Použijte k definování další specifikace hlaviček a parametrů adresy URL.

**Koncový bod:** entity, které jsou relevantní pro určené uživatele vyhledávací dotaz vrátí `?q=""`.
```
 GET https://api.cognitive.microsoft.com/bing/v7.0/entities
```

Podrobnosti o záhlaví, parametry, kódy na trhu, objekty odpovědi, chyby dále, viz [API pro vyhledávání entit Bingu v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference) odkaz.

## <a name="response-json"></a>Odpověď JSON
Odpověď na žádost o vyhledávání entit obsahuje výsledky jako objekty JSON. Příklady výsledků najdete v tématu [Začínáme](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/quick-start).

## <a name="next-steps"></a>Další postup
**Bingu** rozhraní API podporují akce hledání, které vracejí výsledky podle jejich typu. Všechny koncové body hledání vrátí výsledky jako objekty JSON odpovědi.  Všechny koncové body podporují dotazy využívající vrátit konkrétní jazyk a/nebo umístění longitude, latitude a vyhledávání protokolu radius.

Podrobnější informace o parametrech podporuje každý koncový bod najdete v referenčních stránkách pro jednotlivé typy.
Příklady použití rozhraní API pro vyhledávání entit najdete v tématu [Začínáme](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/quick-start) a [Změna velikosti a oříznutí obrázků miniatur](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/resize-and-crop-thumbnails).