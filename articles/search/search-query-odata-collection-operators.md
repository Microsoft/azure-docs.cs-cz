---
title: Kolekce OData – reference operator - Azure Search
description: Operátory kolekce OData, všechny a výrazy lambda v dotazech Azure Search.
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
ms.openlocfilehash: 7afafe158732b14ebe314eeee5d015acddc55b72
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079935"
---
# <a name="odata-collection-operators-in-azure-search---any-and-all"></a>Operátory kolekce OData ve službě Azure Search - `any` a `all`

Při psaní [výraz filtru OData](query-odata-filter-orderby-syntax.md) pro použití s Azure Search, často je užitečné pro filtrování pole kolekce. Můžete dosáhnout použitím `any` a `all` operátory.

## <a name="syntax"></a>Syntaxe

Následující EBNF ([Extended Backus-Naur – formulář](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definuje gramatiku výrazu OData, která používá `any` nebo `all`.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
collection_filter_expression ::=
    field_path'/all(' lambda_expression ')'
    | field_path'/any(' lambda_expression ')'
    | field_path'/any()'

lambda_expression ::= identifier ':' boolean_expression
```

Diagramu interaktivní syntaxe je také k dispozici:

> [!div class="nextstepaction"]
> [Diagram syntaxe OData pro službu Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#collection_filter_expression)

> [!NOTE]
> Zobrazit [referenční příručka syntaxe výrazů OData pro službu Azure Search](search-query-odata-syntax-reference.md) pro dokončení EBNF.

Existují tři formy výrazu, která filtrují kolekce.

- První dva iteraci přes pole shromažďování, použití predikátu formu výraz lambda pro každý prvek kolekce.
  - Pomocí výrazu `all` vrátí `true` Pokud predikát je hodnota true pro každý prvek kolekce.
  - Pomocí výrazu `any` vrátí `true` Pokud predikát je hodnota true pro alespoň jeden prvek kolekce.
- Třetí forma kolekce filtrovat používá `any` bez lambda výraz k ověření, zda kolekce pole je prázdné. Pokud kolekce obsahuje všechny elementy, vrátí `true`. Pokud kolekce je prázdná, vrátí `false`.

A **výraz lambda** v kolekci filtr vypadá jako tělo smyčky v programovacím jazyce. Definuje proměnnou, volá se **proměnnou rozsahu**, obsahující aktuálního prvku kolekce během iterace. Definuje také jiný logický výraz, který se má použít pro proměnnou rozsahu pro každý prvek kolekce kritéria filtru.

## <a name="examples"></a>Příklady

Shoda dokumenty, jejichž `tags` pole obsahuje přesně řetězec "Wi-Fi":

    tags/any(t: t eq 'wifi')

Shoda dokumenty, kde každý prvek `ratings` pole se dělí mezi 3 a 5, včetně:

    ratings/all(r: r ge 3 and r le 5)

Shoda dokumenty, kde některé geografické souřadnice v `locations` pole je v rámci dané mnohoúhelníku:

    locations/any(loc: geo.intersects(loc, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))

Shoda dokumenty, kde `rooms` pole je prázdné:

    not rooms/any()

Pokud pro všechny místnosti, odpovídaly i dokumenty `rooms/amenities` pole obsahuje "tv" a `rooms/baseRate` je menší než 100:

    rooms/all(room: room/amenities/any(a: a eq 'tv') and room/baseRate lt 100.0)

## <a name="limitations"></a>Omezení

Ne všechny funkce, výrazu filtru je k dispozici uvnitř těla výrazu lambda. Omezení se liší v závislosti na datový typ pole kolekce, kterou chcete filtrovat. Následující tabulka shrnuje omezení.

[!INCLUDE [Limitations on OData lambda expressions in Azure Search](../../includes/search-query-odata-lambda-limitations.md)]

Další podrobnosti o těchto omezení, jakož i příklady najdete v tématu [řešení potíží s filtry kolekcí ve službě Azure Search](search-query-troubleshoot-collection-filters.md). Podrobnější informace o důvod, proč tato omezení neexistuje, najdete v článku [Principy kolekci filtrů ve službě Azure Search](search-query-understand-collection-filters.md).

## <a name="next-steps"></a>Další postup  

- [Filtry ve službě Azure Search](search-filters.md)
- [Přehled o výraz jazyka OData pro službu Azure Search](query-odata-filter-orderby-syntax.md)
- [Reference k syntaxi výrazů OData pro službu Azure Search](search-query-odata-syntax-reference.md)
- [Hledání dokumentů &#40;rozhraní REST API služby Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
