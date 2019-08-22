---
title: Odkaz na relační operátor OData – Azure Search
description: Relační operátory OData, EQ, ne, gt, lt, GE a Le v Azure Search dotazů.
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
ms.openlocfilehash: a8bd8b05fd874e05e5e59042d461f4a4286c81e4
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648061"
---
# <a name="odata-comparison-operators-in-azure-search---eq-ne-gt-lt-ge-and-le"></a>Operátory porovnání OData v Azure Search- `eq`, `ne`, `gt`, `lt`, `ge`a`le`

Nejzákladnější operace ve [výrazu filtru OData](query-odata-filter-orderby-syntax.md) v Azure Search slouží k porovnání pole s danou hodnotou. Dva typy porovnání jsou možné – porovnání rovnosti a porovnání rozsahu. K porovnání pole s konstantní hodnotou můžete použít následující operátory:

Operátory rovnosti:

- `eq`: Testování, zda je pole **rovno** konstantní hodnotě
- `ne`: Testuje, jestli se pole **nerovná** konstantní hodnotě.

Operátory rozsahu:

- `gt`: Otestuje, zda je pole **větší než** hodnota konstanty.
- `lt`: Otestuje, zda je pole **menší než** hodnota konstanty.
- `ge`: Otestuje, zda je pole **větší než nebo rovno** konstantní hodnotě.
- `le`: Otestuje, zda je pole **menší nebo rovno** konstantní hodnotě

Operátory rozsahu v kombinaci s [logickými operátory](search-query-odata-logical-operators.md) můžete použít k otestování, zda je pole v určitém rozsahu hodnot. Podívejte se na [Příklady](#examples) dále v tomto článku.

> [!NOTE]
> Pokud dáváte přednost, můžete vložit konstantní hodnotu na levou stranu operátoru a název pole na pravé straně. Pro operátory rozsahu je význam porovnání obrácený. Například pokud je konstantní hodnota na levé straně, `gt` bude testována, zda je konstantní hodnota větší než pole. Operátory porovnání můžete použít také k porovnání výsledku funkce, `geo.distance`jako je například, s hodnotou. Pro logické funkce `search.ismatch`, jako je například, porovnání výsledku `true` s `false` nebo je volitelné.

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
> [Diagram syntaxe OData pro Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#comparison_expression)

> [!NOTE]
> Kompletní EBNF najdete v referenčních informacích k [syntaxi výrazu OData pro Azure Search](search-query-odata-syntax-reference.md) .

Existují dvě formy srovnávacích výrazů. Jediný rozdíl mezi nimi spočívá v tom, zda se konstanta zobrazuje na levé nebo pravé straně operátoru. Výraz na druhé straně operátoru musí být **Proměnná** nebo volání funkce. Proměnná může být buď název pole, nebo proměnná rozsahu v případě [výrazu lambda](search-query-odata-collection-operators.md).

## <a name="data-types-for-comparisons"></a>Datové typy pro porovnání

Datové typy na obou stranách relačního operátoru musí být kompatibilní. Například pokud je levá strana pole typu `Edm.DateTimeOffset`, pak musí být pravá strana konstanta data a času. Numerické datové typy jsou flexibilnější. Můžete porovnat proměnné a funkce libovolného číselného typu s konstantami libovolného jiného číselného typu s několika omezeními, jak je popsáno v následující tabulce.

| Typ proměnné nebo funkce | Typ hodnoty konstanty | Omezení |
| --- | --- | --- |
| `Edm.Double` | `Edm.Double` | Pro porovnání se vztahují [zvláštní pravidla pro `NaN` ](#special-case-nan) |
| `Edm.Double` | `Edm.Int64` | Konstanta je převedena na `Edm.Double`, což vede ke ztrátě přesnosti pro hodnoty velké velikosti. |
| `Edm.Double` | `Edm.Int32` | neuvedeno |
| `Edm.Int64` | `Edm.Double` | Porovnání s `NaN`, `-INF`, nebo `INF` nejsou povolena. |
| `Edm.Int64` | `Edm.Int64` | neuvedeno |
| `Edm.Int64` | `Edm.Int32` | Konstanta je převedena na `Edm.Int64` před porovnáním |
| `Edm.Int32` | `Edm.Double` | Porovnání s `NaN`, `-INF`, nebo `INF` nejsou povolena. |
| `Edm.Int32` | `Edm.Int64` | neuvedeno |
| `Edm.Int32` | `Edm.Int32` | neuvedeno |

Pro porovnávání, které nejsou povoleny, jako je například porovnání pole typu `Edm.Int64` do `NaN`, Azure Search REST API vrátí "HTTP 400: Chybný požadavek: Chyba.

> [!IMPORTANT]
> I když je porovnávání číselných typů flexibilní, důrazně doporučujeme zapsat porovnávání ve filtrech tak, aby konstantní hodnota byla stejného datového typu jako proměnná nebo funkce, na kterou je porovnávána. To je obzvláště důležité při kombinování plovoucí desetinné čárky a celočíselných hodnot, kde je možné implicitní převody, které ztratí přesnost.

<a name="special-case-nan"></a>

### <a name="special-cases-for-null-and-nan"></a>Zvláštní případy pro `null` a`NaN`

Při použití relačních operátorů je důležité si uvědomit, že všechna pole, která nejsou v kolekci Azure Search, `null`mohou být potenciálně. Následující tabulka uvádí všechny možné výsledky pro výraz porovnání, kde může být `null`jedna strana:

| Operator | Výsledek, pokud je pouze pole nebo proměnná`null` | Výsledek, pokud je pouze konstanta`null` | Výsledek, pokud pole nebo proměnná i konstanta jsou`null` |
| --- | --- | --- | --- |
| `gt` | `false` | HTTP 400: Chybná chyba žádosti | HTTP 400: Chybná chyba žádosti |
| `lt` | `false` | HTTP 400: Chybná chyba žádosti | HTTP 400: Chybná chyba žádosti |
| `ge` | `false` | HTTP 400: Chybná chyba žádosti | HTTP 400: Chybná chyba žádosti |
| `le` | `false` | HTTP 400: Chybná chyba žádosti | HTTP 400: Chybná chyba žádosti |
| `eq` | `false` | `false` | `true` |
| `ne` | `true` | `true` | `false` |

Souhrnně `null` se rovná pouze sobě a není menší nebo rovno jiné hodnotě.

Pokud má váš index pole typu `Edm.Double` a nahráváte `NaN` do těchto polí hodnoty, budete se muset při psaní filtrů brát v úvahu. Azure Search implementuje standard IEEE 754 pro zpracování `NaN` hodnot a porovnání s těmito hodnotami produkuje Nezřejmé výsledky, jak je znázorněno v následující tabulce.

| Operator | Výsledek, pokud je alespoň jeden operand`NaN` |
| --- | --- |
| `gt` | `false` |
| `lt` | `false` |
| `ge` | `false` |
| `le` | `false` |
| `eq` | `false` |
| `ne` | `true` |

Souhrnně `NaN` se nerovná žádné hodnotě, včetně sebe samé.

### <a name="comparing-geo-spatial-data"></a>Porovnávání geograficky geografických dat

Nemůžete přímo porovnat pole typu `Edm.GeographyPoint` s konstantní hodnotou, ale můžete `geo.distance` použít funkci. Tato funkce vrací hodnotu typu `Edm.Double`, takže ji můžete porovnat s číselnou konstantou pro filtrování na základě vzdálenosti od konstantních geografických souřadnic. Podívejte se na následující [Příklady](#examples) .

### <a name="comparing-string-data"></a>Porovnávání řetězcových dat

Řetězce lze porovnat v filtrech pro přesné shody pomocí `eq` operátorů a. `ne` U těchto porovnávání se rozlišují malá a velká písmena.

## <a name="examples"></a>Příklady

Porovnává dokumenty, `Rating` kde je pole mezi 3 a 5 včetně:

    Rating ge 3 and Rating le 5

Porovnává dokumenty, `Location` kde je pole menší než 2 kilometry od dané zeměpisné šířky a délky:

    geo.distance(Location, geography'POINT(-122.031577 47.578581)') lt 2.0

Porovná dokumenty, `LastRenovationDate` kde je pole větší nebo rovno 1. ledna 2015, půlnoci UTC:

    LastRenovationDate ge 2015-01-01T00:00:00.000Z

Porovnává dokumenty, `Details/Sku` kde pole `null`není:

    Details/Sku ne null

Porovná dokumenty pro hotely, kde aspoň jedna místnost má typ "Deluxe místnost", kde řetězec `Rooms/Type` pole odpovídá filtru přesně:

    Rooms/any(room: room/Type eq 'Deluxe Room')

## <a name="next-steps"></a>Další kroky  

- [Filtry v Azure Search](search-filters.md)
- [Přehled jazyka výrazů OData pro Azure Search](query-odata-filter-orderby-syntax.md)
- [Referenční dokumentace syntaxe výrazu OData pro Azure Search](search-query-odata-syntax-reference.md)
- [Hledat dokumenty &#40;Azure Search REST API služby&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
