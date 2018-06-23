---
title: Koncový bod hledání entity | Microsoft Docs
description: Souhrn rozhraní API pro vyhledávání Entity koncového bodu.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: article
ms.date: 12/05/2017
ms.author: v-gedod
ms.openlocfilehash: a2557c6000445544b3b47a05d7d356ccaa9928b4
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342371"
---
# <a name="entity-search-endpoint"></a>Koncový bod hledání entity
**Rozhraní API pro vyhledávání Entity** obsahuje jeden koncový bod, který vrací entity z webu na základě dotazu.

## <a name="endpoint"></a>Koncový bod
Chcete-li získat entity výsledky pomocí **rozhraní API Bing**, odeslání `GET` požadavek na následující koncový bod. Můžete definovat další specifikace pomocí hlaviček a parametrů adresy URL.

**Koncový bod:** vrací entity, které jsou relevantní pro uživatele vyhledávací dotaz definované `?q=""`.
```
 GET https://api.cognitive.microsoft.com/bing/v7.0/entities
```

Podrobnosti o hlavičky, parametry, trhu kódy, objekty odpovědi, chyb atd., najdete [rozhraní API služby Bing Entity Search v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference) odkaz.

## <a name="response-json"></a>Odpověď JSON
Odpověď na žádost o vyhledávání entity obsahuje výsledky jako objekty JSON. Příklady výsledků najdete v tématu [Začínáme](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/quick-start).

## <a name="next-steps"></a>Další postup
**Bing** rozhraní API pro podporu vyhledávání akcí, které vracejí výsledky podle jejich typu. Všechny koncové body vyhledávání vrátí výsledky jako objekty JSON odpovědi.  Všechny koncové body podporu dotazů, které vracejí konkrétní jazyk nebo umístění zeměpisné délky, zeměpisnou šířku a hledání radius.

Úplné informace o parametrech nepodporuje každý koncový bod najdete na stránkách odkaz pro každý typ.
Příklady použití hledání Entity rozhraní API najdete v tématu [Začínáme](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/quick-start) a [Resizing a oříznutí obrázky miniatur](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/resize-and-crop-thumbnails).