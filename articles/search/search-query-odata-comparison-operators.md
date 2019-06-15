---
title: Porovnání OData – reference operator - Azure Search
description: Operátory porovnání OData, eq, ne, gt, lt, ge a le v dotazech Azure Search.
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
ms.openlocfilehash: b51bf3d77283ae828f47fdb0355d2deb43f071a1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079922"
---
# <a name="odata-comparison-operators-in-azure-search---eq-ne-gt-lt-ge-and-le"></a>Operátory porovnání OData ve službě Azure Search - `eq`, `ne`, `gt`, `lt`, `ge`, a `le`

Základní operace [výraz filtru OData](query-odata-filter-orderby-syntax.md) ve službě Azure Search je pro porovnání pole dané hodnotě. Jsou dva typy porovnání možných – porovnání rovnosti a porovnání rozsahu. Chcete-li porovnat pole s konstantní hodnotou můžete použít následující operátory:

Operátory rovnosti:

- `eq`: Otestujte, zda je pole **rovna** konstantní hodnotu
- `ne`: Otestujte, zda je pole **není rovno** konstantní hodnotu

Rozsah operátory:

- `gt`: Otestujte, zda je pole **větší než** konstantní hodnotu
- `lt`: Otestujte, zda je pole **menší než** konstantní hodnotu
- `ge`: Otestujte, zda je pole **větší než nebo rovna hodnotě** konstantní hodnotu
- `le`: Otestujte, zda je pole **menší než nebo rovno** konstantní hodnotu

Operátory rozsahu můžete použít v kombinaci s [logické operátory](search-query-odata-logical-operators.md) k ověření, zda je pole v rámci rozsahu hodnot. Zobrazit [příklady](#examples) dále v tomto článku.

> [!NOTE]
> Pokud dáváte přednost, můžete umístit konstantní hodnoty na levé straně operátoru a názvu pole na pravé straně. Pro operátory rozsahu je obrácený význam porovnání. Například, pokud je hodnota konstanty na levé straně `gt` byste testovali, zda je konstantní hodnota větší než pole. Operátory porovnání můžete také použít k porovnání výsledku funkce, jako například `geo.distance`, s hodnotou. Pro logickou hodnotu funkce, jako například `search.ismatch`, porovnává výsledek, který má `true` nebo `false` je volitelný.

## <a name="syntax"></a>Syntaxe

Následující EBNF ([Extended Backus-Naur – formulář](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definuje gramatiku výrazu OData, která používá operátory porovnání.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
comparison_expression ::=
    variable_or_function comparison_operator constant |
    constant comparison_operator variable_or_function

variable_or_function ::= variable | function_call

comparison_operator ::= 'gt' | 'lt' | 'ge' | 'le' | 'eq' | 'ne'
```

Diagramu interaktivní syntaxe je také k dispozici:

> [!div class="nextstepaction"]
> [Diagram syntaxe OData pro službu Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#comparison_expression)

> [!NOTE]
> Zobrazit [referenční příručka syntaxe výrazů OData pro službu Azure Search](search-query-odata-syntax-reference.md) pro dokončení EBNF.

Existují dva typy výrazů porovnání. Jediným rozdílem mezi nimi je, zda konstanty se zobrazí na levé straně - nebo pravé straně operátoru. Výraz na druhé straně operátoru musí být **proměnnou** nebo volání funkce. Proměnná může být buď pole názvu, nebo proměnnou rozsahu v případě třídy [výraz lambda](search-query-odata-collection-operators.md).

## <a name="data-types-for-comparisons"></a>Datové typy pro porovnání

Datové typy, které na obou stranách operátoru porovnání musí být kompatibilní. Například, pokud je na levé straně pole typu `Edm.DateTimeOffset`, potom pravé straně musí být konstanta datum a čas. Číselné datové typy jsou flexibilnější. Jak je popsáno v následující tabulce můžete porovnat proměnné a funkce libovolného číselného typu s konstanty jiných číselného typu, s několika omezeními.

| Typ proměnné nebo funkce | Typ konstantní hodnoty | Omezení |
| --- | --- | --- |
| `Edm.Double` | `Edm.Double` | Porovnání se vztahuje [zvláštní pravidla pro `NaN`](#special-case-nan) |
| `Edm.Double` | `Edm.Int64` | Konstanta je převedena na `Edm.Double`výsledkem ztráty přesnosti pro hodnoty velké velikosti |
| `Edm.Double` | `Edm.Int32` | neuvedeno |
| `Edm.Int64` | `Edm.Double` | Porovnání `NaN`, `-INF`, nebo `INF` nejsou povoleny |
| `Edm.Int64` | `Edm.Int64` | neuvedeno |
| `Edm.Int64` | `Edm.Int32` | Konstanta je převedena na `Edm.Int64` před porovnání |
| `Edm.Int32` | `Edm.Double` | Porovnání `NaN`, `-INF`, nebo `INF` nejsou povoleny |
| `Edm.Int32` | `Edm.Int64` | neuvedeno |
| `Edm.Int32` | `Edm.Int32` | neuvedeno |

Pro porovnání, které nejsou povoleny, jako například porovnávání na pole typu `Edm.Int64` k `NaN`, REST API služby Azure Search vrátí "HTTP 400: Chybný požadavek"došlo k chybě.

> [!IMPORTANT]
> Přestože číselný typ porovnání jsou flexibilní, důrazně doporučujeme tak, aby hodnota konstanty je stejného datového typu jako proměnnou nebo funkci, ke kterému je porovnáván zápis porovnání ve filtrech. To je zvlášť důležité při kombinování s plovoucí desetinnou čárkou a celočíselných hodnot, ve kterém jsou možné implicitních převodů, které ztratit přesnost.

<a name="special-case-nan"></a>

### <a name="special-cases-for-null-and-nan"></a>Zvláštní případy pro `null` a `NaN`

Při použití operátory porovnání, je dobré si uvědomit, že všechna pole bez kolekce ve službě Azure Search může být potenciálně `null`. Následující tabulka uvádí možné výsledky výrazu porovnání, kde může být buď na straně `null`:

| Operátor | Výsledek, pokud je pole nebo proměnné `null` | Výsledek, pokud je pouze – konstanta `null` | Výsledek je pole nebo proměnné a konstanty `null` |
| --- | --- | --- | --- |
| `gt` | `false` | HTTP 400: Chybná žádost o chybě | HTTP 400: Chybná žádost o chybě |
| `lt` | `false` | HTTP 400: Chybná žádost o chybě | HTTP 400: Chybná žádost o chybě |
| `ge` | `false` | HTTP 400: Chybná žádost o chybě | HTTP 400: Chybná žádost o chybě |
| `le` | `false` | HTTP 400: Chybná žádost o chybě | HTTP 400: Chybná žádost o chybě |
| `eq` | `false` | `false` | `true` |
| `ne` | `true` | `true` | `false` |

Stručně řečeno `null` rovná pouze sám na sebe a není menší nebo větší než všechny ostatní hodnoty.

Pokud má pole typu indexu `Edm.Double` a nahrajete `NaN` hodnoty do těchto polí, budete muset počítat, která při zápisu filtry. Služba Azure Search implementuje standardu IEEE 754 pro zpracování `NaN` hodnoty a porovnání s tyto hodnoty výsledky není zřejmé, jak je znázorněno v následující tabulce.

| Operátor | Výsledek, pokud je alespoň jeden operand `NaN` |
| --- | --- |
| `gt` | `false` |
| `lt` | `false` |
| `ge` | `false` |
| `le` | `false` |
| `eq` | `false` |
| `ne` | `true` |

Stručně řečeno `NaN` není shodný s libovolnou hodnotu, včetně samotného.

### <a name="comparing-geo-spatial-data"></a>Porovnávání geoprostorového dat

Nelze přímo porovnat pole typu `Edm.GeographyPoint` s konstantní hodnotou, ale můžete použít `geo.distance` funkce. Tato funkce vrátí hodnotu typu `Edm.Double`, takže ho můžete porovnat s číselnou konstantu pro filtrování podle vzdálenost od konstantní geografické souřadnice. Zobrazit [příklady](#examples) níže.

### <a name="comparing-string-data"></a>Porovnání řetězců dat

Filtry lze porovnat řetězce pro přesné shody pomocí `eq` a `ne` operátory. Tato porovnání jsou malá a velká písmena.

## <a name="examples"></a>Příklady

Shoda dokumenty, kde `Rating` pole je dlouhý 3 až 5, včetně:

    Rating ge 3 and Rating le 5

Shoda dokumenty, kde `Location` je menší než 2 kilometrech od dané zeměpisné šířky a délky pole:

    geo.distance(Location, geography'POINT(-122.031577 47.578581)') lt 2.0

Shoda dokumenty, kde `LastRenovationDate` pole je větší než nebo rovno 1. ledna 2015, o půlnoci UTC:

    LastRenovationDate ge 2015-01-01T00:00:00.000Z

Shoda dokumenty, kde `Details/Sku` pole není `null`:

    Details/Sku ne null

Odpovídaly i dokumenty pro hotely, kde se aspoň jeden místnosti má typ "Deluxe místnost", kde řetězec `Rooms/Type` pole přesně odpovídá filtru:

    Rooms/any(room: room/Type eq 'Deluxe Room')

## <a name="next-steps"></a>Další postup  

- [Filtry ve službě Azure Search](search-filters.md)
- [Přehled o výraz jazyka OData pro službu Azure Search](query-odata-filter-orderby-syntax.md)
- [Reference k syntaxi výrazů OData pro službu Azure Search](search-query-odata-syntax-reference.md)
- [Hledání dokumentů &#40;rozhraní REST API služby Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
