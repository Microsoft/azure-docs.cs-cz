---
title: Referenční dokumentace funkcí Hledat.v OData – Azure Search
description: Hledat.v funkci prostředí OData v dotazech Azure Search.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
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
ms.openlocfilehash: a61291e547021077341a5f1b3db7422afa5b9440
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449974"
---
# <a name="odata-searchin-function-in-azure-search"></a>OData `search.in` funkce ve službě Azure Search

Běžný scénář v [výrazů filtru OData](query-odata-filter-orderby-syntax.md) je zkontrolovat, zda je jedno pole v jednotlivých dokumentech rovná některého mnoho možných hodnot. To je třeba, jak implementovat některé aplikace [oříznutí zabezpečení](search-security-trimming-for-azure-search.md) – zaškrtnutím pole obsahující jeden nebo více ID instančního objektu seznam identifikátorů objektu zabezpečení reprezentující uživatele vydáním dotazu. Jeden ze způsobů, jak napsat dotaz tímto způsobem je použít [ `eq` ](search-query-odata-comparison-operators.md) a [ `or` ](search-query-odata-logical-operators.md) operátory:

    group_ids/any(g: g eq '123' or g eq '456' or g eq '789')

Existuje ale kratší způsob, jak zapisovat, pomocí `search.in` funkce:

    group_ids/any(g: search.in(g, '123, 456, 789'))

> [!IMPORTANT]
> Kromě ještě kratší a snadněji čtou, pomocí `search.in` také poskytuje [přinese zlepšení výkonu](#bkmk_performance) a předchází některé [velikost omezení filtrů](search-query-odata-filter.md#bkmk_limits) když existují stovky nebo i tisíce hodnoty zahrnout do filtru. Z tohoto důvodu, důrazně doporučujeme používat `search.in` místo složitější disjunkce rovnosti výrazů.

> [!NOTE]
> Nedávno zavedl 4.01 verzi OData standard [ `in` operátor](https://docs.oasis-open.org/odata/odata/v4.01/cs01/part2-url-conventions/odata-v4.01-cs01-part2-url-conventions.html#_Toc505773230), která má podobné chování jako `search.in` funkce ve službě Azure Search. Nicméně Azure Search nepodporuje tento operátor, takže je nutné použít `search.in` namísto toho funkci.

## <a name="syntax"></a>Syntaxe

Následující EBNF ([Extended Backus-Naur – formulář](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definuje gramatiku `search.in` funkce:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_in_call ::=
    'search.in(' variable ',' string_literal(',' string_literal)? ')'
```

Diagramu interaktivní syntaxe je také k dispozici:

> [!div class="nextstepaction"]
> [Diagram syntaxe OData pro službu Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#search_in_call)

> [!NOTE]
> Zobrazit [referenční příručka syntaxe výrazů OData pro službu Azure Search](search-query-odata-syntax-reference.md) pro dokončení EBNF.

`search.in` Funkce testuje, jestli daný řetězec pole nebo proměnné rozsahu se rovná k jednomu z daného seznamu hodnot. Rovnost mezi proměnnou a každá hodnota v seznamu je určena stejným způsobem jako pro malá a velká písmena způsobem `eq` operátor. Proto výraz jako `search.in(myfield, 'a, b, c')` je ekvivalentní `myfield eq 'a' or myfield eq 'b' or myfield eq 'c'`, s tím rozdílem, že `search.in` předá mnohem lepší výkon.

Existují dvě přetížení `search.in` funkce:

- `search.in(variable, valueList)`
- `search.in(variable, valueList, delimiters)`

Parametry jsou definovány v následující tabulce:

| Název parametru | Type | Popis |
| --- | --- | --- |
| `variable` | `Edm.String` | Odkaz na pole řetězce (nebo proměnnou rozsahu přes kolekce pole řetězce v případě, kdy `search.in` je použit uvnitř `any` nebo `all` výraz). |
| `valueList` | `Edm.String` | Řetězec obsahující oddělený seznam hodnot tak, aby odpovídala `variable` parametru. Pokud `delimiters` parametr není zadán, výchozí oddělovače jsou místa a čárku. |
| `delimiters` | `Edm.String` | Řetězec, ve kterém každý znak je považován za oddělovačem při analýze `valueList` parametru. Výchozí hodnota tohoto parametru je `' ,'` to znamená, že všechny hodnoty s prostory a/nebo čárkou oddělené. Pokud je potřeba použít oddělovače kromě mezery a čárky, protože hodnoty obsahovat tyto znaky, můžete zadat alternativní oddělovače jako `'|'` v tomto parametru. |

<a name="bkmk_performance"></a>

### <a name="performance-of-searchin"></a>Výkon `search.in`

Pokud používáte `search.in`, můžete očekávat, že když druhý parametr obsahuje seznam stovky nebo tisíce hodnoty doby odezvy sekunda. Neexistuje žádné explicitní omezení na počet položek, které můžete předat `search.in`, i když jsou dál omezené na žádost o maximální velikosti. Však latence bude růst počet hodnot.

## <a name="examples"></a>Příklady

Najdete všechny hotels s názvem rovno "Moře zobrazení motel" nebo "Rozpočtu hotel". Fráze obsahovat mezery, která je výchozím oddělovačem. Můžete zadat alternativní oddělovač v jednoduchých uvozovkách jako třetí parametr řetězce:  

    search.in(HotelName, 'Sea View motel,Budget hotel', ',')

Najít všechny hotels s názvem rovno "Moře zobrazení motel" nebo rozpočtu hotelu oddělené "|"):

    search.in(HotelName, 'Sea View motel|Budget hotel', '|')

Najdete všechny hotels s prostory, které mají značky 'Wi-Fi' nebo "tub":

    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub')))

Najdete shoda s frází v rámci kolekce, jako je například "vyhřívaný ručníků stojany" nebo "fén zahrnuté" značky.

    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))

Najdete všechny hotels bez značky "motel" a "kabině":

    Tags/all(tag: not search.in(tag, 'motel, cabin'))

## <a name="next-steps"></a>Další postup  

- [Filtry ve službě Azure Search](search-filters.md)
- [Přehled o výraz jazyka OData pro službu Azure Search](query-odata-filter-orderby-syntax.md)
- [Reference k syntaxi výrazů OData pro službu Azure Search](search-query-odata-syntax-reference.md)
- [Hledání dokumentů &#40;rozhraní REST API služby Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
