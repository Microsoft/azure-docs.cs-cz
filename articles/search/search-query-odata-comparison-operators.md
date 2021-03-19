---
title: Odkaz na relační operátor OData
titleSuffix: Azure Cognitive Search
description: Syntaxe a referenční dokumentace pro použití relačních operátorů OData (EQ, ne, gt, lt, GE a Le) v Azure Kognitivní hledáních dotazech.
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
ms.openlocfilehash: fc5803f96c30ea1df362676aa8c4104bb0b69db3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "88934867"
---
# <a name="odata-comparison-operators-in-azure-cognitive-search---eq-ne-gt-lt-ge-and-le"></a>Relační operátory OData v Azure kognitivní hledání- `eq` , `ne` ,,, `gt` `lt` `ge` a `le`

Nejzákladnější operace ve [výrazu filtru OData](query-odata-filter-orderby-syntax.md) v Azure kognitivní hledání slouží k porovnání pole s danou hodnotou. Dva typy porovnání jsou možné – porovnání rovnosti a porovnání rozsahu. K porovnání pole s konstantní hodnotou můžete použít následující operátory:

Operátory rovnosti:

- `eq`: Test, zda je pole **rovno** konstantní hodnotě
- `ne`: Otestujte, jestli se pole **nerovná** konstantní hodnotě.

Operátory rozsahu:

- `gt`: Otestujte, jestli je pole **větší než** hodnota konstanty.
- `lt`: Otestujte, jestli je pole **menší než** hodnota konstanty.
- `ge`: Otestujte, jestli je pole **větší než nebo rovno** konstantní hodnotě.
- `le`: Otestujte, jestli je pole **menší nebo rovno** konstantní hodnotě.

Operátory rozsahu v kombinaci s [logickými operátory](search-query-odata-logical-operators.md) můžete použít k otestování, zda je pole v určitém rozsahu hodnot. Podívejte se na [Příklady](#examples) dále v tomto článku.

> [!NOTE]
> Pokud dáváte přednost, můžete vložit konstantní hodnotu na levou stranu operátoru a název pole na pravé straně. Pro operátory rozsahu je význam porovnání obrácený. Například pokud je konstantní hodnota na levé straně, `gt` bude testována, zda je konstantní hodnota větší než pole. Operátory porovnání můžete použít také k porovnání výsledku funkce, jako `geo.distance` je například, s hodnotou. Pro logické funkce, jako `search.ismatch` je například, porovnání výsledku s `true` nebo `false` je volitelné.

## <a name="syntax"></a>Syntax

Následující EBNF ([rozšířený Backus-Naur formulář](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definuje gramatiku výrazu OData, který používá operátory porovnání.

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

Datové typy na obou stranách relačního operátoru musí být kompatibilní. Například pokud je levá strana pole typu `Edm.DateTimeOffset` , pak musí být pravá strana konstanta data a času. Numerické datové typy jsou flexibilnější. Můžete porovnat proměnné a funkce libovolného číselného typu s konstantami libovolného jiného číselného typu s několika omezeními, jak je popsáno v následující tabulce.

| Typ proměnné nebo funkce | Typ hodnoty konstanty | Omezení |
| --- | --- | --- |
| `Edm.Double` | `Edm.Double` | Pro porovnání se vztahují [zvláštní pravidla pro `NaN` ](#special-case-nan) |
| `Edm.Double` | `Edm.Int64` | Konstanta je převedena na `Edm.Double` , což vede ke ztrátě přesnosti pro hodnoty velké velikosti. |
| `Edm.Double` | `Edm.Int32` | Není k dispozici |
| `Edm.Int64` | `Edm.Double` | Porovnání s `NaN` , `-INF` , nebo `INF` nejsou povolena. |
| `Edm.Int64` | `Edm.Int64` | Není k dispozici |
| `Edm.Int64` | `Edm.Int32` | Konstanta je převedena na `Edm.Int64` Před porovnáním |
| `Edm.Int32` | `Edm.Double` | Porovnání s `NaN` , `-INF` , nebo `INF` nejsou povolena. |
| `Edm.Int32` | `Edm.Int64` | Není k dispozici |
| `Edm.Int32` | `Edm.Int32` | Není k dispozici |

Pro porovnávání, které nejsou povoleny, jako je například porovnání pole typu `Edm.Int64` do `NaN` , REST API Azure kognitivní hledání vrátí chybu HTTP 400: Bad Request.

> [!IMPORTANT]
> I když je porovnávání číselných typů flexibilní, důrazně doporučujeme zapsat porovnávání ve filtrech tak, aby konstantní hodnota byla stejného datového typu jako proměnná nebo funkce, na kterou je porovnávána. To je obzvláště důležité při kombinování plovoucí desetinné čárky a celočíselných hodnot, kde je možné implicitní převody, které ztratí přesnost.

<a name="special-case-nan"></a>

### <a name="special-cases-for-null-and-nan"></a>Zvláštní případy pro `null` a `NaN`

Při použití relačních operátorů je důležité si uvědomit, že všechna pole, která nejsou v kolekci v Azure Kognitivní hledání, můžou být potenciálně `null` . Následující tabulka uvádí všechny možné výsledky pro výraz porovnání, kde může být jedna strana `null` :

| Operátor | Výsledek, pokud je pouze pole nebo proměnná `null` | Výsledek, pokud je pouze konstanta `null` | Výsledek, pokud pole nebo proměnná i konstanta jsou `null` |
| --- | --- | --- | --- |
| `gt` | `false` | HTTP 400: Chybná chyba žádosti | HTTP 400: Chybná chyba žádosti |
| `lt` | `false` | HTTP 400: Chybná chyba žádosti | HTTP 400: Chybná chyba žádosti |
| `ge` | `false` | HTTP 400: Chybná chyba žádosti | HTTP 400: Chybná chyba žádosti |
| `le` | `false` | HTTP 400: Chybná chyba žádosti | HTTP 400: Chybná chyba žádosti |
| `eq` | `false` | `false` | `true` |
| `ne` | `true` | `true` | `false` |

Souhrnně se `null` rovná pouze sobě a není menší nebo rovno jiné hodnotě.

Pokud má váš index pole typu `Edm.Double` a nahráváte `NaN` do těchto polí hodnoty, budete se muset při psaní filtrů brát v úvahu. Azure Kognitivní hledání implementuje standard IEEE 754 pro zpracování `NaN` hodnot a porovnání s těmito hodnotami produkuje Nezřejmé výsledky, jak je znázorněno v následující tabulce.

| Operátor | Výsledek, pokud je alespoň jeden operand `NaN` |
| --- | --- |
| `gt` | `false` |
| `lt` | `false` |
| `ge` | `false` |
| `le` | `false` |
| `eq` | `false` |
| `ne` | `true` |

Souhrnně `NaN` se nerovná žádné hodnotě, včetně sebe samé.

### <a name="comparing-geo-spatial-data"></a>Porovnávání geograficky geografických dat

Nemůžete přímo porovnat pole typu `Edm.GeographyPoint` s konstantní hodnotou, ale můžete použít `geo.distance` funkci. Tato funkce vrací hodnotu typu `Edm.Double` , takže ji můžete porovnat s číselnou konstantou pro filtrování na základě vzdálenosti od konstantních geografických souřadnic. Podívejte se na následující [Příklady](#examples) .

### <a name="comparing-string-data"></a>Porovnávání řetězcových dat

Řetězce lze porovnat v filtrech pro přesné shody pomocí `eq` `ne` operátorů a. U těchto porovnávání se rozlišují malá a velká písmena.

## <a name="examples"></a>Příklady

Porovnává dokumenty `Rating` , kde je pole mezi 3 a 5 včetně:

```text
Rating ge 3 and Rating le 5
```

Porovnává dokumenty, kde `Location` je pole menší než 2 kilometry od dané zeměpisné šířky a délky:

```text
geo.distance(Location, geography'POINT(-122.031577 47.578581)') lt 2.0
```

Porovná dokumenty, kde `LastRenovationDate` je pole větší nebo rovno 1. ledna 2015, půlnoci UTC:

```text
LastRenovationDate ge 2015-01-01T00:00:00.000Z
```

Porovnává dokumenty, kde `Details/Sku` pole není `null` :

```text
Details/Sku ne null
```

Porovná dokumenty pro hotely, kde aspoň jedna místnost má typ "Deluxe místnost", kde řetězec `Rooms/Type` pole odpovídá filtru přesně:

```text
Rooms/any(room: room/Type eq 'Deluxe Room')
```

## <a name="next-steps"></a>Další kroky  

- [Filtry v Azure Kognitivní hledání](search-filters.md)
- [Přehled jazyka výrazů OData pro Azure Kognitivní hledání](query-odata-filter-orderby-syntax.md)
- [Referenční dokumentace syntaxe výrazu OData pro Azure Kognitivní hledání](search-query-odata-syntax-reference.md)
- [Hledání dokumentů &#40;Azure Kognitivní hledání REST API&#41;](/rest/api/searchservice/Search-Documents)