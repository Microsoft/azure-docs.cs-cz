---
title: funkce dotazu moreLikeThis (Preview)
titleSuffix: Azure Cognitive Search
description: Popisuje funkci moreLikeThis (Preview), která je k dispozici ve verzi Preview REST API Azure Kognitivní hledání.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 95b9c76a2ff962cb2fa4bacbb1b1e9a953b7014f
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873807"
---
# <a name="morelikethis-preview-in-azure-cognitive-search"></a>moreLikeThis (Preview) v Azure Kognitivní hledání

> [!IMPORTANT] 
> Tato funkce je aktuálně ve verzi Public Preview. Funkce Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Tato funkce poskytuje [REST API verze 2019-05-06-Preview](search-api-preview.md) . V tuto chvíli není k dispozici žádný portál ani podpora sady .NET SDK.

`moreLikeThis=[key]` je parametr dotazu v [rozhraní API pro hledání dokumentů](https://docs.microsoft.com/rest/api/searchservice/search-documents) , který najde dokumenty podobné dokumentu určenému klíčem dokumentu. Když se v `moreLikeThis`vytvoří požadavek hledání, vygeneruje se dotaz s hledanými výrazy získanými z daného dokumentu, který tento dokument nejlépe popisuje. Vygenerovaný dotaz se pak použije k vytvoření žádosti o vyhledávání. Ve výchozím nastavení se považuje obsah všech prohledávatelných polí, mínus všechna pole s omezením, která jste zadali pomocí parametru `searchFields`. Parametr `moreLikeThis` nelze použít společně s parametrem Search `search=[string]`.

Ve výchozím nastavení se považuje obsah všech vyhledávacích polí na nejvyšší úrovni. Pokud chcete místo toho zadat konkrétní pole, můžete použít parametr `searchFields`. 

Nemůžete použít `MoreLikeThis` pro prohledávatelné dílčí pole ve [složitém typu](search-howto-complex-data-types.md).

## <a name="examples"></a>Příklady

Všechny následující příklady používají ukázku hotelů z [rychlého startu: vytvoření indexu vyhledávání v Azure Portal](search-get-started-portal.md).

### <a name="simple-query"></a>Jednoduchý dotaz

Následující dotaz vyhledá dokumenty, jejichž pole popisu jsou nejvíce podobná poli zdrojového dokumentu, jak je určeno parametrem `moreLikeThis`:

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=29&searchFields=Description&api-version=2019-05-06-Preview
```

V tomto příkladu požadavek vyhledává hotely podobně jako u `HotelId` 29.
Místo použití HTTP GET můžete také vyvolat `MoreLikeThis` pomocí HTTP POST:

```
POST /indexes/hotels-sample-index/docs/search?api-version=2019-05-06-Preview
    {
      "moreLikeThis": "29",
      "searchFields": "Description"
    }
```

### <a name="apply-filters"></a>Použití filtrů

`MoreLikeThis` lze kombinovat s dalšími společnými parametry dotazů, jako je `$filter`. Dotaz lze například omezit pouze na hotely, jejichž kategorie je "rozpočet" a kde je hodnocení vyšší než 3,5:

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=20&searchFields=Description&$filter=(Category eq 'Budget' and Rating gt 3.5)&api-version=2019-05-06-Preview
```

### <a name="select-fields-and-limit-results"></a>Výběr polí a omezení výsledků

`$top` selektor lze použít k omezení počtu výsledků, které by měly být vráceny v dotazu `MoreLikeThis`. Pole lze také vybrat pomocí `$select`. Tady se vybere první tři hotely spolu s jejich ID, názvem a hodnocením: 

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=20&searchFields=Description&$filter=(Category eq 'Budget' and Rating gt 3.5)&$top=3&$select=HotelId,HotelName,Rating&api-version=2019-05-06-Preview
```

## <a name="next-steps"></a>Další kroky

K experimentování s touto funkcí můžete použít libovolný nástroj webového testování.  Pro toto cvičení doporučujeme použít metodu post.

> [!div class="nextstepaction"]
> [Prozkoumejte rozhraní REST API pro Azure Kognitivní hledání pomocí post](search-get-started-postman.md)
