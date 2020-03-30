---
title: Odkaz na operátor kolekce OData
titleSuffix: Azure Cognitive Search
description: Při vytváření výrazů filtru v dotazech Azure Cognitive Search použijte operátory "any" a "all" ve výrazech lambda, když je filtr v poli kolekce nebo komplexní kolekce.
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
ms.openlocfilehash: 54ddc8222816831b5b436297bbb1b40d03230f0c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113234"
---
# <a name="odata-collection-operators-in-azure-cognitive-search---any-and-all"></a>Operátory kolekce OData v `any` Azure Cognitive Search – a`all`

Při psaní [výrazu filtru OData](query-odata-filter-orderby-syntax.md) pro použití s Azure Cognitive Search, je často užitečné filtrovat na sběrných polích. Můžete toho dosáhnout `any` pomocí `all` a operátory.

## <a name="syntax"></a>Syntaxe

Následující formulář EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definuje gramatiku výrazu OData, který používá `any` nebo `all`.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
collection_filter_expression ::=
    field_path'/all(' lambda_expression ')'
    | field_path'/any(' lambda_expression ')'
    | field_path'/any()'

lambda_expression ::= identifier ':' boolean_expression
```

K dispozici je také interaktivní syntaktický diagram:

> [!div class="nextstepaction"]
> [Syntaxový diagram OData pro Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#collection_filter_expression)

> [!NOTE]
> Viz [odkaz na syntaxi výrazu OData pro Azure Cognitive Search](search-query-odata-syntax-reference.md) pro kompletní EBNF.

Existují tři formy výrazu, které filtrují kolekce.

- První dva iterát přes pole kolekce, použití predikátu dané ve formě výrazu lambda pro každý prvek kolekce.
  - Výraz pomocí `all` `true` vrátí, pokud predikát je true pro každý prvek kolekce.
  - Výraz pomocí `any` `true` vrátí, pokud predikát je true pro alespoň jeden prvek kolekce.
- Třetí forma filtru kolekce `any` používá bez výrazu lambda k testování, zda je pole kolekce prázdné. Pokud kolekce obsahuje nějaké prvky, vrátí `true`. Pokud je kolekce prázdná, vrátí `false`.

**Lambda výraz** ve filtru kolekce je jako tělo smyčky v programovacím jazyce. Definuje proměnnou, nazývanou **proměnná rozsahu**, která obsahuje aktuální prvek kolekce během iterace. Definuje také jiný logický výraz, který je kritéria filtru použít na proměnnou rozsahu pro každý prvek kolekce.

## <a name="examples"></a>Příklady

Shodovat dokumenty, jejichž `tags` pole obsahuje přesně řetězec "wifi":

    tags/any(t: t eq 'wifi')

Porovná dokumenty, kde `ratings` každý prvek pole spadá mezi 3 a 5, včetně:

    ratings/all(r: r ge 3 and r le 5)

Porovná dokumenty, kde se některý `locations` z geosouřadnic v poli nachází v daném polygu:

    locations/any(loc: geo.intersects(loc, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))

Spárovat `rooms` dokumenty, ve kterých je pole prázdné:

    not rooms/any()

Spárovat dokumenty, kde `rooms/amenities` pro všechny místnosti, pole obsahuje "tv" a `rooms/baseRate` je menší než 100:

    rooms/all(room: room/amenities/any(a: a eq 'tv') and room/baseRate lt 100.0)

## <a name="limitations"></a>Omezení

Ne všechny funkce výrazů filtru je k dispozici uvnitř těla výrazu lambda. Omezení se liší v závislosti na datovém typu sběrného pole, které chcete filtrovat. Následující tabulka shrnuje omezení.

[!INCLUDE [Limitations on OData lambda expressions in Azure Cognitive Search](../../includes/search-query-odata-lambda-limitations.md)]

Další podrobnosti o těchto omezeních a příklady, najdete [v tématu řešení potíží s filtry kolekce v Azure Cognitive Search](search-query-troubleshoot-collection-filters.md). Podrobnější informace o tom, proč tato omezení existují, najdete [v tématu Principy filtrů kolekce v Azure Cognitive Search](search-query-understand-collection-filters.md).

## <a name="next-steps"></a>Další kroky  

- [Filtry v Azure Cognitive Search](search-filters.md)
- [Přehled jazyka OData pro Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Odkaz na syntaxi výrazu OData pro Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [Hledání dokumentů &#40;azure kognitivní vyhledávání rozhraní API rozhraní&#41;API](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
