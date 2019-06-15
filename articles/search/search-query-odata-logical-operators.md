---
title: Logický operátor OData – reference – Azure Search
description: Logické operátory prostředí OData a, nebo a ne, v Azure vyhledávací dotazy.
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
ms.openlocfilehash: de93765117b4cafe70e5ad277e32ca0a1fa8d90a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079766"
---
# <a name="odata-logical-operators-in-azure-search---and-or-not"></a>Logické operátory prostředí OData ve službě Azure Search - `and`, `or`, `not`

[Výrazy filtru OData](query-odata-filter-orderby-syntax.md) ve službě Azure Search jsou logické výrazy, která se vyhodnotí `true` nebo `false`. Můžete napsat složitějších filtrů napsáním řadu [jednodušší filtry](search-query-odata-comparison-operators.md) a sestavování pomocí logických operátorů z [algebraický datový typ Boolean](https://en.wikipedia.org/wiki/Boolean_algebra):

- `and`: Binární operátor, který se vyhodnotí `true` Pokud mají oba jeho levé a pravé dílčí výrazy `true`.
- `or`: Binární operátor, který se vyhodnotí `true` Pokud vyhodnocen jako jeden z jeho doleva nebo doprava dílčí výrazy `true`.
- `not`: Unární operátor, který se vyhodnotí `true` pokud jeho dílčí výraz vyhodnocen `false`a naopak.

Tyto společně s [shromažďování operátory `any` a `all` ](search-query-odata-collection-operators.md), můžete vytvořit filtry, které můžete vyjádřit velmi komplexní vyhledávací kritéria.

## <a name="syntax"></a>Syntaxe

Následující EBNF ([Extended Backus-Naur – formulář](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definuje gramatiku výrazu, který používá logické operátory prostředí OData.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
logical_expression ::=
    boolean_expression ('and' | 'or') boolean_expression
    | 'not' boolean_expression
```

Diagramu interaktivní syntaxe je také k dispozici:

> [!div class="nextstepaction"]
> [Diagram syntaxe OData pro službu Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#logical_expression)

> [!NOTE]
> Zobrazit [referenční příručka syntaxe výrazů OData pro službu Azure Search](search-query-odata-syntax-reference.md) pro dokončení EBNF.

Existují dvě formy logických výrazů: binární (`and`/`or`), kde jsou dva dílčí výrazy a unární (`not`), kde je zaznamenána pouze jedna. Logické výrazy jakéhokoli druhu může být dílčí výrazy:

- Proměnné typu pole nebo rozsahu `Edm.Boolean`
- Funkce, které vrací hodnoty typu `Edm.Boolean`, jako například `geo.intersects` nebo `search.ismatch`
- [Výrazy porovnání](search-query-odata-comparison-operators.md), jako například `rating gt 4`
- [Výrazy kolekce](search-query-odata-collection-operators.md), jako například `Rooms/any(room: room/Type eq 'Deluxe Room')`
- Logické hodnoty literálu `true` nebo `false`.
- Další logických výrazů, které jsou vytvářeny pomocí `and`, `or`, a `not`.

> [!IMPORTANT]
> Existují určité situace, kde jde použít všechny druhy dílčí výraz s `and` / `or`zejména uvnitř výrazů lambda. Zobrazit [OData shromažďování operátory ve službě Azure Search](search-query-odata-collection-operators.md#limitations) podrobnosti.

### <a name="logical-operators-and-null"></a>Logické operátory a `null`

Nejde produkovat většina logické výrazy, jako je například funkce a porovnání `null` hodnoty a logické operátory nejde použít u `null` přímo literál (například `x and null` není povoleno). Však může být logická pole `null`, takže je třeba vědět, jak `and`, `or`, a `not` operátory chovají za přítomnosti hodnotu null. To je shrnuto v následující tabulce, kde `b` je pole s typem `Edm.Boolean`:

| Výraz | Výsledek při `b` je `null` |
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

Když pole Boolean `b` se zobrazí samostatně ve výrazu filtru, se chová jako by byl zapsán `b eq true`, takže když `b` je `null`, je výraz vyhodnocen `false`. Obdobně `not b` se chová jako `not (b eq true)`, takže je vyhodnocen jako `true`. Tímto způsobem `null` pole se chová stejně jako `false`. To je konzistentní s jejich chování v kombinaci s jinými výrazy pomocí `and` a `or`, jak je znázorněno v předchozí tabulce. Bez ohledu na to, použít k přímému porovnání k `false` (`b eq false`) stále bude vyhodnocen `false`. Jinými slovy `null` není roven `false`, i když se chová stejně jako ji v logických výrazů.

## <a name="examples"></a>Příklady

Shoda dokumenty, kde `rating` pole je dlouhý 3 až 5, včetně:

    rating ge 3 and rating le 5

Shoda dokumenty, kde všechny prvky `ratings` pole jsou kratší než 3 nebo více než 5:

    ratings/all(r: r lt 3 or r gt 5)

Shoda dokumenty, kde `location` pole je v rámci dané mnohoúhelníku a dokument neobsahuje termín "veřejná".

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))') and not search.ismatch('public')

Odpovídaly i dokumenty pro hotely v Vancouver, Kanada tam, kde je deluxe volného místa na bázi ohodnotit nižší než 160:

    Address/City eq 'Vancouver' and Address/Country eq 'Canada' and Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 160)

## <a name="next-steps"></a>Další postup  

- [Filtry ve službě Azure Search](search-filters.md)
- [Přehled o výraz jazyka OData pro službu Azure Search](query-odata-filter-orderby-syntax.md)
- [Reference k syntaxi výrazů OData pro službu Azure Search](search-query-odata-syntax-reference.md)
- [Hledání dokumentů &#40;rozhraní REST API služby Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
