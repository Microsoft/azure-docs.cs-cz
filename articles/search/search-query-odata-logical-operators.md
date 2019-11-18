---
title: Odkaz na logický operátor OData
titleSuffix: Azure Cognitive Search
description: Syntaxe a referenční dokumentace k používání logických operátorů OData,, a, a ne, v Azure Kognitivní hledáních dotazech.
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
ms.openlocfilehash: 2d3952f7d2adc26892cbebcd962f2ea25b86de7d
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113181"
---
# <a name="odata-logical-operators-in-azure-cognitive-search---and-or-not"></a>Logické operátory OData v Azure Kognitivní hledání-`and`, `or``not`

[Výrazy filtru OData](query-odata-filter-orderby-syntax.md) ve službě Azure kognitivní hledání jsou logické výrazy, které se vyhodnotí jako `true` nebo `false`. Složitý filtr můžete napsat tak, že napíšete řadu [jednodušších filtrů](search-query-odata-comparison-operators.md) a seřadíte je pomocí logických operátorů z [Boolean algebraický](https://en.wikipedia.org/wiki/Boolean_algebra):

- `and`: binární operátor, který se vyhodnotí jako `true`, pokud jsou jeho levé i pravé dílčí výrazy vyhodnoceny jako `true`.
- `or`: binární operátor, který se vyhodnotí jako `true`, pokud je jeden z jeho levého nebo pravého dílčího výrazu vyhodnocen jako `true`.
- `not`: unární operátor, který se vyhodnotí jako `true`, pokud je jeho dílčí výraz vyhodnocen jako `false`a naopak.

Spolu s [operátory kolekce `any` a `all`](search-query-odata-collection-operators.md)umožňují vytvářet filtry, které mohou vyjádřit velmi složitá kritéria hledání.

## <a name="syntax"></a>Syntaxe

Následující EBNF ([rozšířený formulář Backus-Naur](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definuje gramatiku výrazu OData, který používá logické operátory.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
logical_expression ::=
    boolean_expression ('and' | 'or') boolean_expression
    | 'not' boolean_expression
```

K dispozici je také diagram interaktivní syntaxe:

> [!div class="nextstepaction"]
> [Diagram syntaxe OData pro Azure Kognitivní hledání](https://azuresearch.github.io/odata-syntax-diagram/#logical_expression)

> [!NOTE]
> Kompletní EBNF najdete v článku [referenční informace k syntaxi výrazu OData pro Azure kognitivní hledání](search-query-odata-syntax-reference.md) .

Existují dvě formy logických výrazů: Binary (`and`/`or`), kde jsou dva dílčí výrazy a unární (`not`), kde je pouze jeden. Dílčí výrazy mohou být logické výrazy libovolného druhu:

- Pole nebo proměnné rozsahu typu `Edm.Boolean`
- Funkce, které vracejí hodnoty typu `Edm.Boolean`, například `geo.intersects` nebo `search.ismatch`
- [Výrazy porovnání](search-query-odata-comparison-operators.md), například `rating gt 4`
- [Výrazy kolekce](search-query-odata-collection-operators.md), například `Rooms/any(room: room/Type eq 'Deluxe Room')`
- Logické literály `true` nebo `false`.
- Jiné logické výrazy vytvořené pomocí `and`, `or`a `not`.

> [!IMPORTANT]
> Existují situace, kdy není možné použít všechny druhy dílčího výrazu s `and`/`or`, zejména uvnitř výrazů lambda. Podrobnosti najdete [v tématu operátory kolekce OData v Azure kognitivní hledání](search-query-odata-collection-operators.md#limitations) .

### <a name="logical-operators-and-null"></a>Logické operátory a `null`

Většina logických výrazů, jako jsou funkce a porovnávání, nemůže způsobit `null` hodnoty a logické operátory nelze použít na `null` literály přímo (například `x and null` není povolena). Logická pole však mohou být `null`, takže je třeba vědět, jak se operátory `and`, `or`a `not` chovají v přítomnosti hodnoty null. Toto je shrnuto v následující tabulce, kde `b` je pole typu `Edm.Boolean`:

| Výraz | Výsledek, pokud je `b` `null` |
| --- | --- |
| `b` | `false` |
| `not b` | `true` |
| `b eq true` | `false` |
| `b eq false` | `false` |
| `b eq null` | `true` |
| `b ne true` | `true` |
| `b ne false` | `true` |
| `b ne null` | `false` |
| `b and true` | `false` |
| `b and false` | `false` |
| `b or true` | `true` |
| `b or false` | `false` |

Když se pole Boolean `b` zobrazí samostatně ve výrazu filtru, chová se, jako kdyby byla napsána `b eq true`, takže pokud `b` `null`, výraz se vyhodnotí jako `false`. Podobně se `not b` chová jako `not (b eq true)`, takže se vyhodnotí jako `true`. Tímto způsobem se `null` pole chovají stejně jako `false`. To je konzistentní s tím, jak se chovají v kombinaci s jinými výrazy pomocí `and` a `or`, jak je znázorněno v tabulce výše. Navzdory tomu bude stále vyhodnoceno přímé porovnání s `false` (`b eq false`) `false`. Jinými slovy `null` není rovno `false`, i když se chová jako v logických výrazech.

## <a name="examples"></a>Příklady

Porovnává dokumenty, ve kterých je `rating` pole mezi 3 a 5 včetně:

    rating ge 3 and rating le 5

Porovnává dokumenty, kde jsou všechny prvky `ratings`ho pole menší než 3 nebo větší než 5:

    ratings/all(r: r lt 3 or r gt 5)

Porovnává dokumenty, kde je pole `location` v daném mnohoúhelníku, a dokument neobsahuje pojem "public".

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))') and not search.ismatch('public')

Porovnává dokumenty pro hotely v Vancouver, Kanada, kde je Deluxe místnost se základní sazbou nižší než 160:

    Address/City eq 'Vancouver' and Address/Country eq 'Canada' and Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 160)

## <a name="next-steps"></a>Další kroky  

- [Filtry v Azure Kognitivní hledání](search-filters.md)
- [Přehled jazyka výrazů OData pro Azure Kognitivní hledání](query-odata-filter-orderby-syntax.md)
- [Referenční dokumentace syntaxe výrazu OData pro Azure Kognitivní hledání](search-query-odata-syntax-reference.md)
- [Hledání dokumentů &#40;Azure kognitivní hledání REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
