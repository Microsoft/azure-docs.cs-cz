---
title: Odkaz na funkci OData search.in
titleSuffix: Azure Cognitive Search
description: Syntaxe a referenční dokumentace pro použití funkce search.in v dotazech Azure Cognitive Search.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: b43c46599cbacaf40bc9583e364d088fa27a3ac9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113121"
---
# <a name="odata-searchin-function-in-azure-cognitive-search"></a>Funkce `search.in` OData v Azure Cognitive Search

Běžným scénářem [ve výrazech filtru OData](query-odata-filter-orderby-syntax.md) je kontrola, zda se jedno pole v každém dokumentu rovná jedné z mnoha možných hodnot. Například je to, jak některé aplikace [implementovat oříznutí zabezpečení](search-security-trimming-for-azure-search.md) – kontrolou pole obsahující jeden nebo více primárních ID proti seznamu primárních ID představující uživatele vydávající dotaz. Jedním ze způsobů, jak napsat dotaz, jako je tento, je použití [`eq`](search-query-odata-comparison-operators.md) a [`or`](search-query-odata-logical-operators.md) operátory:

    group_ids/any(g: g eq '123' or g eq '456' or g eq '789')

Existuje však kratší způsob, jak to `search.in` napsat pomocí funkce:

    group_ids/any(g: search.in(g, '123, 456, 789'))

> [!IMPORTANT]
> Kromě toho, že kratší `search.in` a čitelnější, pomocí také poskytuje [výhody výkonu](#bkmk_performance) a vyhýbá se určité [omezení velikosti filtrů,](search-query-odata-filter.md#bkmk_limits) když existují stovky nebo dokonce tisíce hodnot zahrnout do filtru. Z tohoto důvodu důrazně doporučujeme použít `search.in` místo složitější disjunkce výrazů rovnosti.

> [!NOTE]
> Verze 4.01 standardu OData nedávno zavedla [ `in` operátor](https://docs.oasis-open.org/odata/odata/v4.01/cs01/part2-url-conventions/odata-v4.01-cs01-part2-url-conventions.html#_Toc505773230), který `search.in` má podobné chování jako funkce v Azure Cognitive Search. Azure Cognitive Search však nepodporuje tento operátor, `search.in` takže je nutné použít funkci místo.

## <a name="syntax"></a>Syntaxe

Gramatiku `search.in` funkce definuje následující eBNF ([Rozšířený backus-naur)](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)formulář:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_in_call ::=
    'search.in(' variable ',' string_literal(',' string_literal)? ')'
```

K dispozici je také interaktivní syntaktický diagram:

> [!div class="nextstepaction"]
> [Syntaxový diagram OData pro Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#search_in_call)

> [!NOTE]
> Viz [odkaz na syntaxi výrazu OData pro Azure Cognitive Search](search-query-odata-syntax-reference.md) pro kompletní EBNF.

Funkce `search.in` testuje, zda se dané řetězcové pole nebo proměnná rozsahu rovná jednomu z daného seznamu hodnot. Rovnost mezi proměnnou a každou hodnotou v seznamu je určena způsobem rozlišujícím malá a velká písmena, stejným způsobem jako pro `eq` operátor. Proto výraz `search.in(myfield, 'a, b, c')` jako je `myfield eq 'a' or myfield eq 'b' or myfield eq 'c'`ekvivalentní `search.in` , kromě toho, že přinese mnohem lepší výkon.

Existují dvě přetížení `search.in` funkce:

- `search.in(variable, valueList)`
- `search.in(variable, valueList, delimiters)`

Parametry jsou definovány v následující tabulce:

| Název parametru | Typ | Popis |
| --- | --- | --- |
| `variable` | `Edm.String` | Odkaz na pole řetězce (nebo proměnná rozsahu přes `search.in` pole kolekce `any` řetězců `all` v případě, kde se používá uvnitř nebo výraz). |
| `valueList` | `Edm.String` | Řetězec obsahující oddělený seznam hodnot, které mají `variable` odpovídat parametru. Pokud `delimiters` parametr není zadán, výchozí oddělovače jsou mezery a čárky. |
| `delimiters` | `Edm.String` | Řetězec, kde je každý znak považován za oddělovač při analýzě parametru. `valueList` Výchozí hodnota tohoto parametru je, `' ,'` což znamená, že všechny hodnoty s mezerami a čárkami mezi nimi budou odděleny. Pokud potřebujete použít oddělovače jiné než mezery a čárky, protože vaše hodnoty obsahují `'|'` tyto znaky, můžete zadat alternativní oddělovače, například v tomto parametru. |

<a name="bkmk_performance"></a>

### <a name="performance-of-searchin"></a>Výkon`search.in`

Pokud používáte `search.in`, můžete očekávat, že doba odezvy sub-second, pokud druhý parametr obsahuje seznam stovek nebo tisíců hodnot. Neexistuje žádné explicitní omezení počtu položek, které `search.in`můžete předat , i když jste stále omezeni maximální velikostí požadavku. Latence se však zvýší s růstem počtu hodnot.

## <a name="examples"></a>Příklady

Najděte všechny hotely s názvem rovným "Motel Sea View" nebo "Levný hotel". Fráze obsahují mezery, což je výchozí oddělovač. Jako třetí parametr řetězce můžete zadat alternativní oddělovač v jednoduchých uvozovkách:  

    search.in(HotelName, 'Sea View motel,Budget hotel', ',')

Najděte všechny hotely s názvem rovným "Motel u Sea View" nebo "Levný hotel" odděleným "|"):

    search.in(HotelName, 'Sea View motel|Budget hotel', '|')

Najít všechny hotely s pokoji, které mají značku 'wifi' nebo 'vana':

    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub')))

Ve své kolekci najdete shodu s frázemi, jako jsou "vyhřívané věšáky na ručníky" nebo "fén".

    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))

Najít všechny hotely bez označení 'motel' nebo 'kabina':

    Tags/all(tag: not search.in(tag, 'motel, cabin'))

## <a name="next-steps"></a>Další kroky  

- [Filtry v Azure Cognitive Search](search-filters.md)
- [Přehled jazyka OData pro Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Odkaz na syntaxi výrazu OData pro Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [Hledání dokumentů &#40;azure kognitivní vyhledávání rozhraní API rozhraní&#41;API](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
