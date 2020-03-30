---
title: Odkaz na logický operátor OData
titleSuffix: Azure Cognitive Search
description: Syntaxe a referenční dokumentace pro použití logických operátorů OData a nebo ne v dotazech Azure Cognitive Search.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113181"
---
# <a name="odata-logical-operators-in-azure-cognitive-search---and-or-not"></a>Logické operátory OData v `and` `or`Azure Cognitive Search - , ,`not`

[Výrazy filtru OData](query-odata-filter-orderby-syntax.md) v Azure Cognitive Search jsou `true` `false`logické výrazy, které vyhodnocují nebo . Komplexní filtr můžete napsat tak, že napíšete řadu [jednodušších filtrů](search-query-odata-comparison-operators.md) a sloučíte je pomocí logických operátorů z [logické algebry](https://en.wikipedia.org/wiki/Boolean_algebra):

- `and`: Binární operátor, který `true` vyhodnotí, pokud jeho levé `true`a pravé dílčí výrazy vyhodnotit .
- `or`: Binární operátor, který `true` vyhodnotí, pokud jeden z jeho levé `true`nebo pravé podvýrazy vyhodnotí .
- `not`: Unární operátor, který `true` vyhodnotí, pokud `false`jeho dílčí výraz vyhodnotí , a naopak.

Ty spolu s [ `any` operátory `all`kolekce a ](search-query-odata-collection-operators.md), umožňují vytvářet filtry, které mohou vyjádřit velmi složitá kritéria vyhledávání.

## <a name="syntax"></a>Syntaxe

Následující formulář EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definuje gramatiku výrazu OData, který používá logické operátory.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
logical_expression ::=
    boolean_expression ('and' | 'or') boolean_expression
    | 'not' boolean_expression
```

K dispozici je také interaktivní syntaktický diagram:

> [!div class="nextstepaction"]
> [Syntaxový diagram OData pro Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#logical_expression)

> [!NOTE]
> Viz [odkaz na syntaxi výrazu OData pro Azure Cognitive Search](search-query-odata-syntax-reference.md) pro kompletní EBNF.

Existují dvě formy logických výrazů: binární`and`/`or`( ), kde jsou dva`not`dílčí výrazy a unární ( ), kde je pouze jeden. Dílčí výrazy mohou být logické výrazy jakéhokoli druhu:

- Pole nebo proměnné rozsahu typu`Edm.Boolean`
- Funkce, které vracejí `Edm.Boolean`hodnoty `geo.intersects` typu , například`search.ismatch`
- [Srovnávací výrazy](search-query-odata-comparison-operators.md), například`rating gt 4`
- [Kolekce výrazy](search-query-odata-collection-operators.md), například`Rooms/any(room: room/Type eq 'Deluxe Room')`
- Logické literály `true` nebo `false`.
- Jiné logické výrazy `and`vytvořené `or`pomocí `not`, a .

> [!IMPORTANT]
> Existují některé situace, kdy nelze použít všechny druhy `and` / `or`podvýrazu s , zejména uvnitř lambda výrazy. Podrobnosti najdete [v tématu Operátory kolekce OData v Azure Cognitive Search.](search-query-odata-collection-operators.md#limitations)

### <a name="logical-operators-and-null"></a>Logické operátory a`null`

Většina logických výrazů, jako jsou `null` funkce a porovnání, nemůže vytvářet hodnoty a logické operátory nelze použít přímo na `null` literál (například `x and null` není povoleno). Logická pole však `null`může být , takže je `and` `or`třeba `not` být vědomi toho, jak , , a operátory chovají v přítomnosti null. To je shrnuto v `b` následující tabulce, `Edm.Boolean`kde je pole typu :

| Expression | Výsledek, `b` když je`null` |
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

Když se booleovské pole `b` zobrazí samo o sobě ve výrazu `b eq true`filtru, `b` chová `null`se, jako `false`by bylo napsáno , takže pokud je , výraz vyhodnotí na . Podobně `not b` se chová jako `not (b eq true)`, takže se `true`vyhodnocuje na . Tímto způsobem `null` se pole chovají `false`stejně jako . To je v souladu s tím, jak se `and` `or`chovají v kombinaci s jinými výrazy pomocí a , jak je znázorněno v tabulce výše. Navzdory tomu bude přímé `false` `b eq false`srovnání s ( `false`) stále vyhodnoceno . Jinými slovy, `null` není `false`rovno , i když se chová podobně v logické výrazy.

## <a name="examples"></a>Příklady

Spárovat `rating` doklady, kde je pole mezi 3 a 5, včetně:

    rating ge 3 and rating le 5

Porovná dokumenty, kde `ratings` jsou všechny prvky pole menší než 3 nebo větší než 5:

    ratings/all(r: r lt 3 or r gt 5)

Porovná dokumenty, `location` kde je pole v daném polygu a dokument neobsahuje termín "veřejné".

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))') and not search.ismatch('public')

Zápas dokumenty pro hotely ve Vancouveru, Kanada, kde je deluxe pokoj se základní sazbou nižší než 160:

    Address/City eq 'Vancouver' and Address/Country eq 'Canada' and Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 160)

## <a name="next-steps"></a>Další kroky  

- [Filtry v Azure Cognitive Search](search-filters.md)
- [Přehled jazyka OData pro Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Odkaz na syntaxi výrazu OData pro Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [Hledání dokumentů &#40;azure kognitivní vyhledávání rozhraní API rozhraní&#41;API](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
