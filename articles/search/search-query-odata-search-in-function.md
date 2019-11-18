---
title: Reference k funkci search.in OData
titleSuffix: Azure Cognitive Search
description: Syntaxe a referenční dokumentace k použití funkce search.in v Azure Kognitivní hledání dotazů.
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
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113121"
---
# <a name="odata-searchin-function-in-azure-cognitive-search"></a>Funkce `search.in` OData v Azure Kognitivní hledání

Běžným scénářem ve [výrazech filtru OData](query-odata-filter-orderby-syntax.md) je ověřit, jestli je jedno pole v každém dokumentu rovno jedné z mnoha možných hodnot. Například to znamená, že některé aplikace implementují [oříznutí zabezpečení](search-security-trimming-for-azure-search.md) – pomocí zaškrtnutí pole obsahujícího jedno nebo více identifikátorů zabezpečení v seznamu identifikátorů zabezpečení, který představuje uživatele, který dotaz vystavil. Jedním ze způsobů, jak napsat dotaz, je použít operátory [`eq`](search-query-odata-comparison-operators.md) a [`or`](search-query-odata-logical-operators.md) :

    group_ids/any(g: g eq '123' or g eq '456' or g eq '789')

Existuje však kratší způsob, jak to napsat pomocí funkce `search.in`:

    group_ids/any(g: search.in(g, '123, 456, 789'))

> [!IMPORTANT]
> Kromě méně lepších a snazších čtení pomocí `search.in` také poskytuje [výhody](#bkmk_performance) související s výkonem a vyhnete se určitým [omezením velikosti filtrů](search-query-odata-filter.md#bkmk_limits) , pokud jsou do filtru zahrnuty stovky nebo dokonce tisíce hodnot. Z tohoto důvodu se důrazně doporučuje použít `search.in` namísto složitějšího odstavení výrazů rovnosti.

> [!NOTE]
> Verze 4,01 Standard OData nedávno představila [operátor`in`](https://docs.oasis-open.org/odata/odata/v4.01/cs01/part2-url-conventions/odata-v4.01-cs01-part2-url-conventions.html#_Toc505773230), který má podobné chování jako funkce `search.in` ve službě Azure kognitivní hledání. Ale Azure Kognitivní hledání nepodporuje tento operátor, takže musíte místo toho použít funkci `search.in`.

## <a name="syntax"></a>Syntaxe

Následující EBNF ([rozšířený formulář Backus-Naur](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definuje gramatiku funkce `search.in`:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_in_call ::=
    'search.in(' variable ',' string_literal(',' string_literal)? ')'
```

K dispozici je také diagram interaktivní syntaxe:

> [!div class="nextstepaction"]
> [Diagram syntaxe OData pro Azure Kognitivní hledání](https://azuresearch.github.io/odata-syntax-diagram/#search_in_call)

> [!NOTE]
> Kompletní EBNF najdete v článku [referenční informace k syntaxi výrazu OData pro Azure kognitivní hledání](search-query-odata-syntax-reference.md) .

Funkce `search.in` testuje, zda je dané pole řetězce nebo proměnná rozsahu rovno jednomu z daného seznamu hodnot. Rovnost mezi proměnnou a každou hodnotou v seznamu je určena způsobem, který rozlišuje velká a malá písmena, stejně jako u operátoru `eq`. Výraz, jako je například `search.in(myfield, 'a, b, c')`, je ekvivalentem `myfield eq 'a' or myfield eq 'b' or myfield eq 'c'`, s tím rozdílem, že `search.in` zajistí mnohem lepší výkon.

Existují dvě přetížení funkce `search.in`:

- `search.in(variable, valueList)`
- `search.in(variable, valueList, delimiters)`

Parametry jsou definovány v následující tabulce:

| Název parametru | Typ | Popis |
| --- | --- | --- |
| `variable` | `Edm.String` | Odkaz na pole řetězce (nebo proměnná rozsahu v poli kolekce řetězců v případě, kdy se `search.in` používá uvnitř výrazu `any` nebo `all`). |
| `valueList` | `Edm.String` | Řetězec obsahující oddělený seznam hodnot, který se má shodovat s parametrem `variable`. Pokud není zadán parametr `delimiters`, jsou výchozí oddělovače mezerou a čárkou. |
| `delimiters` | `Edm.String` | Řetězec, ve kterém je každý znak považován za oddělovač při analýze `valueList` parametru. Výchozí hodnota tohoto parametru je `' ,'` to znamená, že všechny hodnoty s mezerami a/nebo čárkami mezi nimi budou oddělené. Pokud potřebujete použít oddělovače jiné než mezery a čárky, protože vaše hodnoty obsahují tyto znaky, lze v tomto parametru zadat alternativní oddělovače, například `'|'`. |

<a name="bkmk_performance"></a>

### <a name="performance-of-searchin"></a>Výkon `search.in`

Použijete-li `search.in`, můžete očekávat dobu odezvy za sekundu v případě, že druhý parametr obsahuje seznam stovek nebo tisíců hodnot. Pro počet položek, které lze předat `search.in`, neexistuje žádný explicitní limit, i když jste stále omezeni maximální velikostí požadavku. Latence se ale zvětšuje, jakmile se zvýší počet hodnot.

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

- [Filtry v Azure Kognitivní hledání](search-filters.md)
- [Přehled jazyka výrazů OData pro Azure Kognitivní hledání](query-odata-filter-orderby-syntax.md)
- [Referenční dokumentace syntaxe výrazu OData pro Azure Kognitivní hledání](search-query-odata-syntax-reference.md)
- [Hledání dokumentů &#40;Azure kognitivní hledání REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
