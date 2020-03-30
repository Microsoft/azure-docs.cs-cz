---
title: Odkaz na operátor porovnání odatech
titleSuffix: Azure Cognitive Search
description: Syntaxe a referenční dokumentace pro použití operátorů porovnání OData (eq, ne, gt, lt, ge a le) v dotazech Azure Cognitive Search.
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
ms.openlocfilehash: 62c8c93e07326e776cbe089042abc481544794bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113218"
---
# <a name="odata-comparison-operators-in-azure-cognitive-search---eq-ne-gt-lt-ge-and-le"></a>Operátory porovnání OData v `eq` `ne`Azure `gt` `lt`Cognitive `ge`Search - , , , a`le`

Nejzákladnější operace ve [výrazu filtru OData](query-odata-filter-orderby-syntax.md) ve službě Azure Cognitive Search je porovnání pole s danou hodnotou. Dva typy porovnání jsou možné – porovnání rovnosti a porovnání rozsahu. Následující operátory můžete použít k porovnání pole s konstantní hodnotou:

Operátory rovnosti:

- `eq`: Otestujte, zda se pole **rovná** konstantní hodnotě
- `ne`: Otestujte, zda se pole **nerovná** konstantní hodnotě

Operátory rozsahu:

- `gt`: Otestujte, zda je pole **větší než** konstantní hodnota.
- `lt`: Otestujte, zda je pole **menší než** konstantní hodnota.
- `ge`: Otestujte, zda je pole **větší nebo rovno** konstantní hodnotě.
- `le`: Otestujte, zda je pole **menší nebo rovno** konstantní hodnotě

Operátory rozsahu můžete použít v kombinaci s [logickými operátory](search-query-odata-logical-operators.md) k testování, zda je pole v určitém rozsahu hodnot. Podívejte se na [příklady](#examples) dále v tomto článku.

> [!NOTE]
> Pokud chcete, můžete umístit konstantní hodnotu na levé straně operátoru a název pole na pravé straně. Pro operátory rozsahu je význam porovnání obrácen. Například pokud je konstantní hodnota vlevo, by otestovat, `gt` zda je hodnota konstanty větší než pole. Porovnání operátorů můžete také použít k porovnání výsledku `geo.distance`funkce, například , s hodnotou. Pro logické funkce, `search.ismatch`například porovnání `true` výsledku nebo `false` je volitelné.

## <a name="syntax"></a>Syntaxe

Následující formulář EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definuje gramatiku výrazu OData, který používá operátory porovnání.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
comparison_expression ::=
    variable_or_function comparison_operator constant |
    constant comparison_operator variable_or_function

variable_or_function ::= variable | function_call

comparison_operator ::= 'gt' | 'lt' | 'ge' | 'le' | 'eq' | 'ne'
```

K dispozici je také interaktivní syntaktický diagram:

> [!div class="nextstepaction"]
> [Syntaxový diagram OData pro Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#comparison_expression)

> [!NOTE]
> Viz [odkaz na syntaxi výrazu OData pro Azure Cognitive Search](search-query-odata-syntax-reference.md) pro kompletní EBNF.

Existují dvě formy porovnání výrazů. Jediným rozdílem mezi nimi je, zda se konstanta objeví na levé nebo pravé straně operátora. Výraz na druhé straně operátoru musí být **proměnná** nebo volání funkce. Proměnnou může být buď název pole, nebo proměnná rozsahu v případě [výrazu lambda](search-query-odata-collection-operators.md).

## <a name="data-types-for-comparisons"></a>Datové typy pro porovnání

Datové typy na obou stranách operátoru porovnání musí být kompatibilní. Pokud je například levá strana `Edm.DateTimeOffset`polem typu , musí být pravá strana konstantou data a času. Číselné datové typy jsou flexibilnější. Můžete porovnat proměnné a funkce libovolného číselného typu s konstantami jiného číselného typu s několika omezeními, jak je popsáno v následující tabulce.

| Typ proměnné nebo funkce | Typ konstantní hodnoty | Omezení |
| --- | --- | --- |
| `Edm.Double` | `Edm.Double` | Srovnání podléhá [zvláštním pravidlům `NaN` pro](#special-case-nan) |
| `Edm.Double` | `Edm.Int64` | Konstanta je `Edm.Double`převedena na , což má za následek ztrátu přesnosti pro hodnoty velkého rozsahu |
| `Edm.Double` | `Edm.Int32` | neuvedeno |
| `Edm.Int64` | `Edm.Double` | Porovnání s `NaN` `-INF`, `INF` , nebo nejsou povolena |
| `Edm.Int64` | `Edm.Int64` | neuvedeno |
| `Edm.Int64` | `Edm.Int32` | Konstanta je `Edm.Int64` převedena na před porovnáním |
| `Edm.Int32` | `Edm.Double` | Porovnání s `NaN` `-INF`, `INF` , nebo nejsou povolena |
| `Edm.Int32` | `Edm.Int64` | neuvedeno |
| `Edm.Int32` | `Edm.Int32` | neuvedeno |

Pro porovnání, které nejsou povoleny, jako je `Edm.Int64` `NaN`například porovnání pole typu , rozhraní REST rozhraní API Azure Cognitive Search vrátí chybu "HTTP 400: Bad Request".

> [!IMPORTANT]
> I když porovnání číselných typů jsou flexibilní, důrazně doporučujeme psát porovnání ve filtrech tak, aby konstantní hodnota je stejného datového typu jako proměnná nebo funkce, se kterou je porovnáván. To je obzvláště důležité při míchání plovoucí desetinné a celočíselné hodnoty, kde implicitní převody, které ztrácejí přesnost jsou možné.

<a name="special-case-nan"></a>

### <a name="special-cases-for-null-and-nan"></a>Zvláštní případy `null` pro`NaN`

Při použití operátorů porovnání, je důležité si uvědomit, že všechny non-sběr pole v Azure Cognitive Search může být `null`potenciálně . V následující tabulce jsou uvedeny všechny možné výsledky pro `null`porovnání výrazu, kde může být obě strany :

| Operátor | Výsledek, pokud je pouze pole nebo proměnná`null` | Výsledek, pokud je pouze konstanta`null` | Výsledek, když je pole nebo proměnná i konstanta`null` |
| --- | --- | --- | --- |
| `gt` | `false` | HTTP 400: Chyba chybného požadavku | HTTP 400: Chyba chybného požadavku |
| `lt` | `false` | HTTP 400: Chyba chybného požadavku | HTTP 400: Chyba chybného požadavku |
| `ge` | `false` | HTTP 400: Chyba chybného požadavku | HTTP 400: Chyba chybného požadavku |
| `le` | `false` | HTTP 400: Chyba chybného požadavku | HTTP 400: Chyba chybného požadavku |
| `eq` | `false` | `false` | `true` |
| `ne` | `true` | `true` | `false` |

V souhrnu `null` se rovná pouze sám sobě a není menší nebo větší než jakákoli jiná hodnota.

Pokud index má pole `Edm.Double` typu `NaN` a nahrajete hodnoty do těchto polí, budete muset účet, který při psaní filtrů. Azure Cognitive Search implementuje standard IEEE `NaN` 754 pro zpracování hodnot a porovnání s těmito hodnotami vytváří nezřejmé výsledky, jak je znázorněno v následující tabulce.

| Operátor | Výsledek, pokud je alespoň jeden operand`NaN` |
| --- | --- |
| `gt` | `false` |
| `lt` | `false` |
| `ge` | `false` |
| `le` | `false` |
| `eq` | `false` |
| `ne` | `true` |

Stručně řečeno, `NaN` se nerovná žádné hodnotě, včetně sebe sama.

### <a name="comparing-geo-spatial-data"></a>Porovnání geoprostorových dat

Pole typu `Edm.GeographyPoint` nelze přímo porovnat s konstantní hodnotou, ale `geo.distance` můžete použít funkci. Tato funkce vrátí hodnotu typu `Edm.Double`, takže ji můžete porovnat s číselnou konstantou pro filtrování na základě vzdálenosti od konstantních geoprostorových souřadnic. Podívejte se na [příklady](#examples) níže.

### <a name="comparing-string-data"></a>Porovnání dat řetězce

Řetězce lze porovnat ve filtrech pro `eq` přesné `ne` shody pomocí a operátory. Tato porovnání jsou malá a velká písmena.

## <a name="examples"></a>Příklady

Spárovat `Rating` doklady, kde je pole mezi 3 a 5, včetně:

    Rating ge 3 and Rating le 5

Spárovat `Location` dokumenty, kde je pole menší než 2 km od dané zeměpisné šířky a délky:

    geo.distance(Location, geography'POINT(-122.031577 47.578581)') lt 2.0

Spárovat `LastRenovationDate` doklady, kde je pole větší nebo rovno 1.1.2015, půlnoci UTC:

    LastRenovationDate ge 2015-01-01T00:00:00.000Z

Spárovat `Details/Sku` dokumenty, `null`kde pole není :

    Details/Sku ne null

Shodovat dokumenty pro hotely, kde alespoň jeden pokoj `Rooms/Type` má typ "Deluxe Pokoj", kde řetězec pole odpovídá filtru přesně:

    Rooms/any(room: room/Type eq 'Deluxe Room')

## <a name="next-steps"></a>Další kroky  

- [Filtry v Azure Cognitive Search](search-filters.md)
- [Přehled jazyka OData pro Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Odkaz na syntaxi výrazu OData pro Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [Hledání dokumentů &#40;azure kognitivní vyhledávání rozhraní API rozhraní&#41;API](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
