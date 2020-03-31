---
title: moreLikeThis (náhled) dotaz funkce
titleSuffix: Azure Cognitive Search
description: Popisuje funkci MoreLikeThis (preview), která je dostupná ve verzi preview rozhraní REST API Azure Cognitive Search.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 95b9c76a2ff962cb2fa4bacbb1b1e9a953b7014f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873807"
---
# <a name="morelikethis-preview-in-azure-cognitive-search"></a>moreLikeThis (preview) v Azure Cognitive Search

> [!IMPORTANT] 
> Tato funkce je aktuálně ve verzi Public Preview. Funkce náhledu je k dispozici bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). [Rozhraní REST API verze 2019-05-06-Preview](search-api-preview.md) poskytuje tuto funkci. V současné době neexistuje žádná podpora portálu nebo sady .NET SDK.

`moreLikeThis=[key]`je parametr dotazu v [rozhraní API pro hledání dokumentů,](https://docs.microsoft.com/rest/api/searchservice/search-documents) který vyhledá dokumenty podobné dokumentu určenému klíčem dokumentu. Pokud je požadavek `moreLikeThis`na hledání proveden pomocí aplikace , je generován dotaz s hledanými termíny extrahovanými z daného dokumentu, které tento dokument nejlépe popisují. Generovaný dotaz se pak použije k vytvoření požadavku na hledání. Ve výchozím nastavení se uvažuje o obsahu všech prohledávatelných polí `searchFields` bez všech omezených polí, která jste zadali pomocí parametru. Parametr `moreLikeThis` nelze použít s parametrem `search=[string]`search , .

Ve výchozím nastavení se považuje obsah všech prohledávatelných polí nejvyšší úrovně. Pokud chcete místo toho zadat určitá `searchFields` pole, můžete použít parametr. 

Prohledávatelná dílčí pole nelze použít `MoreLikeThis` ve [složitém typu](search-howto-complex-data-types.md).

## <a name="examples"></a>Příklady

Všechny následující příklady používají ukázku hotelů z [úvodního startu: Vytvoření indexu vyhledávání na webu Azure Portal](search-get-started-portal.md).

### <a name="simple-query"></a>Jednoduchý dotaz

Následující dotaz vyhledá dokumenty, jejichž pole popisu jsou nejvíce podobná poli `moreLikeThis` zdrojového dokumentu určeného parametrem:

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=29&searchFields=Description&api-version=2019-05-06-Preview
```

V tomto příkladu žádost vyhledá hotely podobné `HotelId` tomu s 29.
Spíše než pomocí HTTP GET, `MoreLikeThis` můžete také vyvolat pomocí HTTP POST:

```
POST /indexes/hotels-sample-index/docs/search?api-version=2019-05-06-Preview
    {
      "moreLikeThis": "29",
      "searchFields": "Description"
    }
```

### <a name="apply-filters"></a>Použití filtrů

`MoreLikeThis`lze kombinovat s dalšími běžnými `$filter`parametry dotazu, jako je . Dotaz může být například omezen pouze na hotely, jejichž kategorie je "Rozpočet" a kde je hodnocení vyšší než 3,5:

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=20&searchFields=Description&$filter=(Category eq 'Budget' and Rating gt 3.5)&api-version=2019-05-06-Preview
```

### <a name="select-fields-and-limit-results"></a>Výběr polí a omezení výsledků

Selektor `$top` lze omezit, kolik výsledků by měla `MoreLikeThis` být vrácena v dotazu. Pole lze také vybrat `$select`pomocí . Zde jsou vybrány tři nejlepší hotely spolu s jejich ID, name a ratingem: 

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=20&searchFields=Description&$filter=(Category eq 'Budget' and Rating gt 3.5)&$top=3&$select=HotelId,HotelName,Rating&api-version=2019-05-06-Preview
```

## <a name="next-steps"></a>Další kroky

S touto funkcí můžete experimentovat pomocí libovolného nástroje pro testování webu.  Pro toto cvičení doporučujeme používat Pošťáka.

> [!div class="nextstepaction"]
> [Prozkoumejte azure kognitivní vyhledávání REST API pomocí Postman](search-get-started-postman.md)
