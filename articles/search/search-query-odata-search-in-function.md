---
title: Referenční informace o funkci search.in OData – Azure Search
description: Funkce search.in OData v dotazech Azure Search.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: brjohnstmsft
ms.author: brjohnst
manager: nitinme
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
ms.openlocfilehash: 8bac0205fa2de8378abaa4d9e8ba8e05ea69192e
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647939"
---
# <a name="odata-searchin-function-in-azure-search"></a>Funkce `search.in` OData v Azure Search

Běžným scénářem ve [výrazech filtru OData](query-odata-filter-orderby-syntax.md) je ověřit, jestli je jedno pole v každém dokumentu rovno jedné z mnoha možných hodnot. Například to znamená, že některé aplikace implementují [oříznutí zabezpečení](search-security-trimming-for-azure-search.md) – pomocí zaškrtnutí pole obsahujícího jedno nebo více identifikátorů zabezpečení v seznamu identifikátorů zabezpečení, který představuje uživatele, který dotaz vystavil. Jedním ze způsobů, jak napsat dotaz, je použít [`eq`](search-query-odata-comparison-operators.md) operátory a: [`or`](search-query-odata-logical-operators.md)

    group_ids/any(g: g eq '123' or g eq '456' or g eq '789')

Existuje však kratší způsob, jak to napsat pomocí `search.in` funkce:

    group_ids/any(g: search.in(g, '123, 456, 789'))

> [!IMPORTANT]
> Kromě méně lepších a snazších čtení pomocí `search.in` nástroje také poskytuje výhody související s [výkonem](#bkmk_performance) a vyhnete se určitým [omezením velikosti filtrů](search-query-odata-filter.md#bkmk_limits) , pokud jsou do filtru zahrnuty stovky nebo dokonce tisíce hodnot. Z tohoto důvodu se důrazně doporučuje použít `search.in` místo složitějšího odstavení výrazů rovnosti.

> [!NOTE]
> Verze 4,01 Standard OData nedávno zavedla [ `in` operátor](https://docs.oasis-open.org/odata/odata/v4.01/cs01/part2-url-conventions/odata-v4.01-cs01-part2-url-conventions.html#_Toc505773230), který má `search.in` podobné chování jako funkce v Azure Search. Azure Search ale tento operátor nepodporuje, takže musíte místo toho použít `search.in` funkci.

## <a name="syntax"></a>Syntaxe

Následující EBNF ([rozšířený formulář Backus-Naur](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definuje gramatiku `search.in` funkce:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_in_call ::=
    'search.in(' variable ',' string_literal(',' string_literal)? ')'
```

K dispozici je také diagram interaktivní syntaxe:

> [!div class="nextstepaction"]
> [Diagram syntaxe OData pro Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#search_in_call)

> [!NOTE]
> Kompletní EBNF najdete v referenčních informacích k [syntaxi výrazu OData pro Azure Search](search-query-odata-syntax-reference.md) .

`search.in` Funkce testuje, zda je dané pole řetězce nebo proměnná rozsahu rovno jednomu z daného seznamu hodnot. Rovnost mezi proměnnou a každou hodnotou v seznamu je určena způsobem, který rozlišuje velká a malá písmena, stejně jako u `eq` operátoru. Proto výraz jako `search.in(myfield, 'a, b, c')` je `myfield eq 'a' or myfield eq 'b' or myfield eq 'c'`ekvivalentní s, s výjimkou `search.in` toho, že bude poskytovat mnohem lepší výkon.

Existují dvě přetížení `search.in` funkce:

- `search.in(variable, valueList)`
- `search.in(variable, valueList, delimiters)`

Parametry jsou definovány v následující tabulce:

| Název parametru | type | Popis |
| --- | --- | --- |
| `variable` | `Edm.String` | Odkaz na pole řetězce (nebo proměnná rozsahu v poli kolekce řetězců v případě, kdy `search.in` se používá `any` uvnitř výrazu or `all` ). |
| `valueList` | `Edm.String` | Řetězec obsahující oddělený seznam hodnot, který se má shodovat s `variable` parametrem. `delimiters` Pokud parametr není zadán, výchozí oddělovače jsou mezera a čárka. |
| `delimiters` | `Edm.String` | Řetězec, ve kterém je každý znak považován za oddělovač při analýze `valueList` parametru. Výchozí hodnota tohoto parametru `' ,'` znamená, že všechny hodnoty s mezerami a/nebo čárkami mezi nimi budou oddělené. Pokud potřebujete použít oddělovače jiné než mezery a čárky, protože vaše hodnoty obsahují tyto znaky, můžete zadat alternativní oddělovače, jako je `'|'` například v tomto parametru. |

<a name="bkmk_performance"></a>

### <a name="performance-of-searchin"></a>Výkon`search.in`

Pokud použijete `search.in`, můžete očekávat dobu odezvy za sekundu v případě, že druhý parametr obsahuje seznam stovek nebo tisíců hodnot. Pro počet položek `search.in`, které můžete předat, neexistuje žádný explicitní limit, i když se vám i nadále omezuje maximální velikost požadavku. Latence se ale zvětšuje, jakmile se zvýší počet hodnot.

## <a name="examples"></a>Příklady

Vyhledá všechny hotely s názvem, který se rovná buď Motel, nebo "rozpočet hotel". Fráze obsahují mezery, což je výchozí oddělovač. Můžete zadat alternativní oddělovač v jednoduchých uvozovkách jako třetí řetězcový parametr:  

    search.in(HotelName, 'Sea View motel,Budget hotel', ',')

Najde všechny hotely s názvem, které se rovnají buď Motel, nebo "rozpočet hotel" oddělené |):

    search.in(HotelName, 'Sea View motel|Budget hotel', '|')

Vyhledejte všechny hotely pomocí místností s označením "WiFi" nebo "vyskočí":

    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub')))

Najde shodu u frází v rámci kolekce, jako jsou například "zahřívaná racky navrhování ručníků" nebo "hairdryer include" ve značkách.

    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))

Vyhledejte všechny hotely bez označení ' Motel ' nebo ' cabin':

    Tags/all(tag: not search.in(tag, 'motel, cabin'))

## <a name="next-steps"></a>Další kroky  

- [Filtry v Azure Search](search-filters.md)
- [Přehled jazyka výrazů OData pro Azure Search](query-odata-filter-orderby-syntax.md)
- [Referenční dokumentace syntaxe výrazu OData pro Azure Search](search-query-odata-syntax-reference.md)
- [Hledat dokumenty &#40;Azure Search REST API služby&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
