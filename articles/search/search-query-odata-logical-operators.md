---
title: Referenční informace o logickém operátoru OData – Azure Search
description: Logické operátory OData, nebo, a, v Azure Search dotazů.
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
ms.openlocfilehash: bf4939a40a2fdf1c8fc6cf97beca0184b1604c98
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647994"
---
# <a name="odata-logical-operators-in-azure-search---and-or-not"></a>Logické operátory OData v Azure Search- `and`, `or`,`not`

[Výrazy filtru OData](query-odata-filter-orderby-syntax.md) v Azure Search jsou logické výrazy, které se `true` vyhodnotí na nebo `false`. Složitý filtr můžete napsat tak, že napíšete řadu [jednodušších filtrů](search-query-odata-comparison-operators.md) a seřadíte je pomocí logických operátorů z [Boolean algebraický](https://en.wikipedia.org/wiki/Boolean_algebra):

- `and`: Binární operátor, který se `true` vyhodnotí, pokud jsou jeho levé i pravé dílčí výrazy `true`vyhodnoceny jako.
- `or`: Binární operátor, který se `true` vyhodnotí, pokud je jeden z jeho levého nebo pravého dílčího výrazu vyhodnocen `true`jako.
- `not`: Unární operátor, který se `true` vyhodnotí `false`, pokud je jeho dílčí výraz vyhodnocen jako a naopak.

Spolu s [ `any` operátory kolekce a `all` ](search-query-odata-collection-operators.md)umožňují vytvářet filtry, které mohou vyjádřit velmi složitá kritéria hledání.

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
> [Diagram syntaxe OData pro Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#logical_expression)

> [!NOTE]
> Kompletní EBNF najdete v referenčních informacích k [syntaxi výrazu OData pro Azure Search](search-query-odata-syntax-reference.md) .

Existují dvě formy logických výrazů: Binary (`and`/`or`), kde existují dva dílčí výrazy a unární (`not`), kde je pouze jeden. Dílčí výrazy mohou být logické výrazy libovolného druhu:

- Pole nebo proměnné rozsahu typu`Edm.Boolean`
- Funkce, které vracejí hodnoty typu `Edm.Boolean`, `geo.intersects` například nebo`search.ismatch`
- [Výrazy porovnání](search-query-odata-comparison-operators.md), jako např.`rating gt 4`
- [Výrazy kolekce](search-query-odata-collection-operators.md), například`Rooms/any(room: room/Type eq 'Deluxe Room')`
- Logické literály `true` nebo `false`.
- Jiné logické výrazy vytvořené pomocí `and`, `or`a `not`.

> [!IMPORTANT]
> Existují situace `and` `or`, kdy není možné použít /všechny druhy dílčího výrazu, zejména uvnitř výrazů lambda. Podrobnosti najdete [v tématu operátory kolekce OData v Azure Search](search-query-odata-collection-operators.md#limitations) .

### <a name="logical-operators-and-null"></a>Logické operátory a`null`

Většina logických výrazů, jako jsou funkce a porovnávání `null` , nemůže vydávat hodnoty a logické operátory nelze použít `null` na literál `x and null` přímo (například není povoleno). Nicméně `null`logická pole mohou být, takže je třeba vědět, `and`jak se operátory, `or`a `not` chovají v přítomnosti hodnoty null. Toto je shrnuto v následující tabulce, kde `b` je pole typu: `Edm.Boolean`

| Výraz | Výsledek, `b` Pokud je`null` |
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

Pokud se `b` pole Boolean zobrazí ve výrazu filtru samostatně, chová se, jako by bylo zapsáno `b` `b eq true`, takže pokud je `null`, výraz se vyhodnotí `false`jako. Podobně se `not b` chová jako, `not (b eq true)` `true`takže se vyhodnotí jako. Tímto způsobem se `null` pole chovají stejně jako `false`. To je konzistentní s tím, jak se chovají v kombinaci s jinými výrazy `and` pomocí `or`a, jak je znázorněno v tabulce výše. Navzdory tomu bude stále vyhodnoceno `false` přímé`b eq false`porovnání na ( `false`). Jinými slovy, `null` není `false`rovno, i když se chová jako v logických výrazech.

## <a name="examples"></a>Příklady

Porovnává dokumenty, `rating` kde je pole mezi 3 a 5 včetně:

    rating ge 3 and rating le 5

Porovnává dokumenty, kde jsou všechny `ratings` prvky pole menší než 3 nebo větší než 5:

    ratings/all(r: r lt 3 or r gt 5)

Porovnává dokumenty, `location` kde je pole v daném mnohoúhelníku, a dokument neobsahuje pojem "public".

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))') and not search.ismatch('public')

Porovnává dokumenty pro hotely v Vancouver, Kanada, kde je Deluxe místnost se základní sazbou nižší než 160:

    Address/City eq 'Vancouver' and Address/Country eq 'Canada' and Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 160)

## <a name="next-steps"></a>Další postup  

- [Filtry v Azure Search](search-filters.md)
- [Přehled jazyka výrazů OData pro Azure Search](query-odata-filter-orderby-syntax.md)
- [Referenční dokumentace syntaxe výrazu OData pro Azure Search](search-query-odata-syntax-reference.md)
- [Hledat dokumenty &#40;Azure Search REST API služby&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
