---
title: Koncový bod Automatických návrhů Bingu
titlesuffix: Azure Cognitive Services
description: Souhrn koncový bod rozhraní API pro automatické návrhy Bingu.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: conceptual
ms.date: 12/05/2017
ms.author: v-gedod
ms.openlocfilehash: 03f7ac11b08d9cad633e207337ff963114f2c68f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55149233"
---
# <a name="bing-autosuggest-endpoint"></a>Koncový bod Automatických návrhů Bingu

**Rozhraní API pro automatické návrhy Bingu** zahrnuje jeden koncový bod, který vrátí seznam navrhovaných dotazů z hledaný termín.

## <a name="endpoint"></a>Koncový bod

Chcete-li získat navrhované dotazy pomocí rozhraní API Bingu pro odeslání `GET` požadavek na následující koncový bod. Použijte k definování další specifikace hlaviček a parametrů adresy URL.

**Koncový bod:** Návrhy hledání vrátí jako výsledky JSON, které se týkají vstupu uživatele určené `?q=""`.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/Suggestions 
```

Podrobnosti o záhlaví, parametry, kódy na trhu, objekty odpovědi, chyby dále, viz [rozhraní API pro automatické návrhy Bingu v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference) odkaz.

## <a name="response-json"></a>Odpověď JSON

Odpovědi na požadavek pro automatické návrhy obsahuje výsledky jako objekty JSON. Viz příklady parsování výsledků [kurzu](tutorials/autosuggest.md) a [zdrojový kód](tutorials/autosuggest-source.md).

## <a name="next-steps"></a>Další postup

**Bingu** rozhraní API podporují akce hledání, které vracejí výsledky podle jejich typu. Všechny koncové body hledání vrátí výsledky jako objekty JSON odpovědi.
Všechny koncové body podporují dotazy využívající vrátit konkrétní jazyk a/nebo umístění longitude, latitude a vyhledávání protokolu radius.

Podrobnější informace o parametrech podporuje každý koncový bod najdete v referenčních stránkách pro jednotlivé typy.
Příklady základní požadavky na používání rozhraní API pro automatické návrhy najdete v tématu [rychlých startů pro automatické návrhy](https://docs.microsoft.com/azure/cognitive-services/Bing-Autosuggest).
