---
title: Koncový bod webové vyhledávání | Microsoft Docs
description: Shrnutí koncový bod webové vyhledávání rozhraní API.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 11/28/2017
ms.author: v-gedod
ms.openlocfilehash: 72fbe1a0eb4379ad032e649f7299e37a0214adfb
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342416"
---
# <a name="web-search-endpoint"></a>Koncový bod webové vyhledávání
**Vyhledávání webového rozhraní API** vrátí webové stránky, zprávy, obrázky, videa, a [entity](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web). Entity obsahovat souhrnné informace o osoby, místní nebo téma.
## <a name="endpoint"></a>Koncový bod
Získat výsledky hledání webové pomocí rozhraní API služby Bing, odeslání `GET` požadavek na následující koncový bod. Specifikace definovat další hlaviček a parametrů adresy URL.

**Koncový bod**: webové vrátí výsledky, které jsou relevantní pro uživatele vyhledávání dotazu definované `?q=""`.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/search
```
Koncový bod: Podrobnosti o hlavičky, parametry, na trhu kódy, objekty odpovědi, chyb, atd., najdete v článku [webového rozhraní API služby Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference) odkaz.

## <a name="response-json"></a>Odpověď JSON
Odpověď na žádost o vyhledávání webové zahrnuje všechny výsledky jako objekty JSON. Analýza výsledek vyžaduje postupy, které zpracovávají elementy každého typu. Najdete v článku [kurzu](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/tutorial-bing-web-search-single-page-app) a [zdrojový kód](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/tutorial-bing-web-search-single-page-app-source) příklady.

## <a name="next-steps"></a>Další postup
**Bing** rozhraní API pro podporu vyhledávání akcí, které vracejí výsledky podle jejich typu. Všechny koncové body vyhledávání vrátí výsledky jako objekty JSON odpovědi.  Všechny koncové body podporu dotazů, které vracejí konkrétní jazyk nebo umístění zeměpisné délky, zeměpisnou šířku a hledání radius.

Úplné informace o parametrech nepodporuje každý koncový bod najdete na stránkách odkaz pro každý typ.
Příklady základní požadavků pomocí vyhledávání webové rozhraní API najdete v tématu [hledání webové rychlé spustí](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/search-the-web).