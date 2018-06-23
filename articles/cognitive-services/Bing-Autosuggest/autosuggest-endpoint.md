---
title: Koncový bod pro automatické návrhy | Microsoft Docs
description: Souhrn rozhraní API pro automatické návrhy koncového bodu.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-autosuggest
ms.topic: article
ms.date: 12/05/2017
ms.author: v-gedod
ms.openlocfilehash: 5aaddd09006cb6f1812bb6ae213a2f5e6720fb1b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343401"
---
# <a name="autosuggest-endpoint"></a>Koncový bod pro automatické návrhy

**Rozhraní API pro automatické návrhy** obsahuje jeden koncový bod, který vrátí seznam hodnot navrhované dotazy z částečné hledaný termín.

## <a name="endpoint"></a>Koncový bod

Získat návrhy dotazy pomocí rozhraní API služby Bing, odeslání `GET` požadavek na následující koncový bod. Můžete definovat další specifikace pomocí hlaviček a parametrů adresy URL.

**Koncový bod:** vrátí vyhledávání návrhy jako výsledky JSON, které jsou relevantní pro vstup uživatele, které jsou definované `?q=""`.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/Suggestions 
```

Podrobnosti o hlavičky, parametry, trhu kódy, objekty odpovědi, chyb atd., najdete [rozhraní API pro automatické návrhy v Bingu v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference) odkaz.

## <a name="response-json"></a>Odpověď JSON

Odpověď na žádost o automatických návrhů obsahuje výsledky jako objekty JSON. Příklady Analýza výsledků najdete v tématu [kurzu](tutorials/autosuggest.md) a [zdrojový kód](tutorials/autosuggest-source.md).

## <a name="next-steps"></a>Další postup

**Bing** rozhraní API pro podporu vyhledávání akcí, které vracejí výsledky podle jejich typu. Všechny koncové body vyhledávání vrátí výsledky jako objekty JSON odpovědi.
Všechny koncové body podporu dotazů, které vracejí konkrétní jazyk nebo umístění zeměpisné délky, zeměpisnou šířku a hledání radius.

Úplné informace o parametrech nepodporuje každý koncový bod najdete na stránkách odkaz pro každý typ.
Příklady základní požadavků pomocí rozhraní API pro automatické návrhy najdete v tématu [pro automatické návrhy – elementy QuickStart](https://docs.microsoft.com/azure/cognitive-services/Bing-Autosuggest).