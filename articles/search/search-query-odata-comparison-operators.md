---
title: Odkaz na relační operátor OData
titleSuffix: Azure Cognitive Search
description: Relační operátory OData, EQ, ne, gt, lt, GE a Le v Azure Kognitivní hledání dotazy.
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
ms.openlocfilehash: 068e2ec822f0a292ac83b3e48049830eb77b49f6
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793388"
---
# <a name="odata-comparison-operators-in-azure-cognitive-search---eq-ne-gt-lt-ge-and-le"></a>Operátory porovnání OData v Azure Kognitivní hledání-`eq`, `ne`, `gt`, `lt`, `ge`a `le`

Nejzákladnější operace ve [výrazu filtru OData](query-odata-filter-orderby-syntax.md) v Azure kognitivní hledání slouží k porovnání pole s danou hodnotou. Dva typy porovnání jsou možné – porovnání rovnosti a porovnání rozsahu. K porovnání pole s konstantní hodnotou můžete použít následující operátory:

Operátory rovnosti:

- `eq`: test, zda je pole **rovno** konstantní hodnotě
- `ne`: test, zda pole není **rovno** konstantní hodnotě

Operátory rozsahu:

- `gt`: test, zda je pole **větší než** konstantní hodnota
- `lt`: test, zda je pole **menší než** hodnota konstanty
- `ge`: test, zda je pole **větší nebo rovno** konstantní hodnotě
- `le`: test, zda je pole **menší nebo rovno** konstantní hodnotě

Operátory rozsahu v kombinaci s [logickými operátory](search-query-odata-logical-operators.md) můžete použít k otestování, zda je pole v určitém rozsahu hodnot. Podívejte se na [Příklady](#examples) dále v tomto článku.

> [!NOTE]
> Pokud dáváte přednost, můžete vložit konstantní hodnotu na levou stranu operátoru a název pole na pravé straně. Pro operátory rozsahu je význam porovnání obrácený. Například pokud je konstantní hodnota na levé straně, `gt` by otestovala, zda je konstantní hodnota větší než pole. Operátory porovnání můžete použít také k porovnání výsledku funkce, jako je například `geo.distance`, s hodnotou. U logických funkcí, jako je například `search.ismatch`, porovnává výsledek s `true` nebo `false` je volitelná.

## <a name="syntax"></a>Syntaxe

Následující EBNF ([rozšířený formulář Backus-Naur](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definuje gramatiku výrazu OData, který používá operátory porovnání.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
comparison_expression ::=
    variable_or_function comparison_operator constant |
    constant comparison_operator variable_or_function

variable_or_function ::= variable | function_call

comparison_operator ::= 'gt' | 'lt' | 'ge' | 'le' | 'eq' | 'ne'
```

K dispozici je také diagram interaktivní syntaxe:

> [!div class="nextstepaction"]
> [Diagram syntaxe OData pro Azure Kognitivní hledání](https://azuresearch.github.io/odata-syntax-diagram/#comparison_expression)

> [!NOTE]
> Kompletní EBNF najdete v článku [referenční informace k syntaxi výrazu OData pro Azure kognitivní hledání](search-query-odata-syntax-reference.md) .

Existují dvě formy srovnávacích výrazů. Jediný rozdíl mezi nimi spočívá v tom, zda se konstanta zobrazuje na levé nebo pravé straně operátoru. Výraz na druhé straně operátoru musí být **Proměnná** nebo volání funkce. Proměnná může být buď název pole, nebo proměnná rozsahu v případě [výrazu lambda](search-query-odata-collection-operators.md).

## <a name="data-types-for-comparisons"></a>Datové typy pro porovnání

Datové typy na obou stranách relačního operátoru musí být kompatibilní. Například pokud je levá strana pole typu `Edm.DateTimeOffset`, pak musí být pravá strana konstanta data a času. Numerické datové typy jsou flexibilnější. Můžete porovnat proměnné a funkce libovolného číselného typu s konstantami libovolného jiného číselného typu s několika omezeními, jak je popsáno v následující tabulce.

| Typ proměnné nebo funkce | Typ hodnoty konstanty | Omezení |
| --- | --- | --- |
| `Edm.Double` | `Edm.Double` | Pro porovnání se vztahují [zvláštní pravidla `NaN`](#special-case-nan) |
| `Edm.Double` | `Edm.Int64` | Konstanta se převede na `Edm.Double`, což vede ke ztrátě přesnosti pro hodnoty velké velikosti. |
| `Edm.Double` | `Edm.Int32` | – |
| `Edm.Int64` | `Edm.Double` | Porovnávání `NaN`, `-INF`nebo `INF` nejsou povolena. |
| `Edm.Int64` | `Edm.Int64` | – |
| `Edm.Int64` | `Edm.Int32` | Konstanta se převede na `Edm.Int64` před porovnáním. |
| `Edm.Int32` | `Edm.Double` | Porovnávání `NaN`, `-INF`nebo `INF` nejsou povolena. |
| `Edm.Int32` | `Edm.Int64` | – |
| `Edm.Int32` | `Edm.Int32` | – |

Pro porovnávání, které nejsou povolené, jako je například porovnání pole typu `Edm.Int64` s `NaN`, REST API Azure Kognitivní hledání vrátí chybu HTTP 400: Bad Request.

> [!IMPORTANT]
> I když je porovnávání číselných typů flexibilní, důrazně doporučujeme zapsat porovnávání ve filtrech tak, aby konstantní hodnota byla stejného datového typu jako proměnná nebo funkce, na kterou je porovnávána. To je obzvláště důležité při kombinování plovoucí desetinné čárky a celočíselných hodnot, kde je možné implicitní převody, které ztratí přesnost.

<a name="special-case-nan"></a>

### <a name="special-cases-for-null-and-nan"></a>Zvláštní případy pro `null` a `NaN`

Při použití relačních operátorů je důležité si uvědomit, že všechna pole, která nejsou v kolekci v Azure Kognitivní hledání, mohou být `null`. Následující tabulka uvádí všechny možné výsledky pro výraz porovnání, kde může být jedna strana `null`:

| Operátor | Výsledek, pokud je `null` pouze pole nebo proměnná | Výsledek, pokud je `null` pouze konstanta | Výsledek, pokud je pole nebo proměnná i konstanta `null` |
| --- | --- | --- | --- |
| `gt` | `false` | HTTP 400: Chybná chyba žádosti | HTTP 400: Chybná chyba žádosti |
| `lt` | `false` | HTTP 400: Chybná chyba žádosti | HTTP 400: Chybná chyba žádosti |
| `ge` | `false` | HTTP 400: Chybná chyba žádosti | HTTP 400: Chybná chyba žádosti |
| `le` | `false` | HTTP 400: Chybná chyba žádosti | HTTP 400: Chybná chyba žádosti |
| `eq` | `false` | `false` | `true` |
| `ne` | `true` | `true` | `false` |

V souhrnu se `null` rovná pouze sobě a není menší nebo větší než jakákoli jiná hodnota.

Pokud má váš index pole typu `Edm.Double` a do těchto polí nahrajete `NaN` hodnoty, budete se muset při psaní filtrů brát v úvahu. Azure Kognitivní hledání implementuje standard IEEE 754 pro zpracování hodnot `NaN` a porovnání s těmito hodnotami produkuje Nezřejmé výsledky, jak je znázorněno v následující tabulce.

| Operátor | Výsledek, pokud je `NaN` aspoň jeden operand |
| --- | --- |
| `gt` | `false` |
| `lt` | `false` |
| `ge` | `false` |
| `le` | `false` |
| `eq` | `false` |
| `ne` | `true` |

V souhrnu `NaN` není rovna žádné hodnotě, včetně sebe samé.

### <a name="comparing-geo-spatial-data"></a>Porovnávání geograficky geografických dat

Nemůžete přímo porovnat pole typu `Edm.GeographyPoint` s konstantní hodnotou, ale můžete použít funkci `geo.distance`. Tato funkce vrací hodnotu typu `Edm.Double`, takže ji můžete porovnat s číselnou konstantou pro filtrování na základě vzdálenosti od konstantních geografických prostorových souřadnic. Podívejte se na následující [Příklady](#examples) .

### <a name="comparing-string-data"></a>Porovnávání řetězcových dat

Řetězce je možné porovnat v filtrech pro přesné shody pomocí operátorů `eq` a `ne`. U těchto porovnávání se rozlišují malá a velká písmena.

## <a name="examples"></a>Příklady

Porovnává dokumenty, ve kterých je `Rating` pole mezi 3 a 5 včetně:

    Rating ge 3 and Rating le 5

Porovnává dokumenty, kde `Location` pole je méně než 2 km od dané zeměpisné šířky a délky:

    geo.distance(Location, geography'POINT(-122.031577 47.578581)') lt 2.0

Porovná dokumenty, kde `LastRenovationDate` pole je větší nebo rovno 1. ledna 2015, půlnoci UTC:

    LastRenovationDate ge 2015-01-01T00:00:00.000Z

Odpovídá dokumentům, kde pole `Details/Sku` není `null`:

    Details/Sku ne null

Porovná dokumenty pro hotely, kde aspoň jedna místnost má typ "Deluxe místnost", kde řetězec `Rooms/Type` pole odpovídá filtru přesně:

    Rooms/any(room: room/Type eq 'Deluxe Room')

## <a name="next-steps"></a>Další kroky  

- [Filtry v Azure Kognitivní hledání](search-filters.md)
- [Přehled jazyka výrazů OData pro Azure Kognitivní hledání](query-odata-filter-orderby-syntax.md)
- [Referenční dokumentace syntaxe výrazu OData pro Azure Kognitivní hledání](search-query-odata-syntax-reference.md)
- [Hledání dokumentů &#40;Azure kognitivní hledání REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
