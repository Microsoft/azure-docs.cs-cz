---
title: Koncový bod Vlastního vyhledávání Bingu
titleSuffix: Azure Cognitive Services
description: Vytváření prostředí s přizpůsobenými vyhledáváními pro témata, která vás zajímají. Uživatelé uvidí výsledky hledání přizpůsobené obsahu, o kterém mají starosti.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 03ec22caedd4e317b9e1fe781dc3d983febc7a6d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "74072793"
---
# <a name="custom-search"></a>Vlastní vyhledávání
Vlastní vyhledávání Bingu umožňuje vytvářet přizpůsobená vyhledávací prostředí pro témata, o která máte zájem. Uživatelům se budou zobrazovat výsledky hledání přizpůsobené obsahu, který je zajímá, a nebudou tak muset procházet výsledky hledání s irelevantním obsahem.

## <a name="custom-search-endpoint"></a>Vlastní koncový bod hledání
Chcete-li získat výsledky pomocí rozhraní API pro vlastní vyhledávání Bingu, odešlete `GET` požadavek do následujícího koncového bodu. K definování dalších specifikací použijte záhlaví a parametry URL.

Koncový bod: vrátí návrhy hledání jako výsledky JSON, které jsou relevantní pro vstup uživatele definovaný pomocí `?q=""` .
```  
 GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search  
```

Příklady, které popisují, jak nastavit vlastní zdroje vyhledávání, najdete v tomto [kurzu](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/tutorials/custom-search-web-page). Podrobnosti o hlavičkách, parametrech, kódech trhu, objektech odpovědí, chybách atd. naleznete v tématu [rozhraní API pro vlastní vyhledávání Bingu v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference) reference.

## <a name="custom-search-response-json"></a>KÓD JSON odpovědi vlastního vyhledávání
Vlastní požadavek na hledání vrátí výsledky jako objekty JSON, viz [objekty odezvy](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#response-objects). 

## <a name="custom-autosuggest"></a>Vlastní Automatický návrh
Rozhraní API pro vlastní automatické návrhy vám umožní odeslat do Bingu výraz částečného vyhledávacího dotazu a získat zpět seznam navrhovaných dotazů, které můžete nakonfigurovat. S vlastním automatickým návrhem přidáte návrhy vracené rozhraním API a případně určíte, jestli se mají zahrnout návrhy vygenerované bingem.

## <a name="custom-autosuggest-endpoint"></a>Vlastní koncový bod pro automatické návrhy
Pokud chcete požádat o vlastní návrhy dotazů, pošlete požadavek GET na:

```
https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/Suggestions
```  

Informace o definování vlastních návrhů najdete v tématu [Definice vlastních návrhů vyhledávání](define-custom-suggestions.md).

## <a name="custom-image-search"></a>Vlastní Vyhledávání obrázků
Rozhraní API pro vlastní Vyhledávání obrázků umožňuje odeslat vyhledávací dotaz do Bingu a vrátit seznam relevantních imagí z vaší instance vlastního vyhledávání.

## <a name="custom-image-search-endpoint"></a>Vlastní koncový bod Vyhledávání obrázků
Pokud chcete požádat o image z vlastní instance hledání, pošlete požadavek GET na následující adresu URL:

```
https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/images/search
```

Informace o konfiguraci vlastní instance vyhledávání najdete v tématu [Konfigurace vlastních možností vyhledávání](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/define-your-custom-view).

## <a name="next-steps"></a>Další kroky
Rozhraní API **Bing** podporují akce hledání, které vracejí výsledky podle jejich typu.Všechny koncové body hledání vrátí výsledky jako objekty odezvy JSON. Všechny koncové body podporují dotazy, které vracejí konkrétní jazyk a umístění podle délky, zeměpisné šířky a poloměru hledání.

Úplné informace o parametrech podporovaných každým koncovým bodem naleznete na referenčních stránkách pro každý typ.
Příklady základních požadavků využívajících rozhraní API pro vlastní vyhledávání najdete v tématu [rychlé zprovoznění vlastního vyhledávání](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/) .
