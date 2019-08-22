---
title: Odkaz na operátor kolekce OData – Azure Search
description: Operátory kolekce OData, všechny a všechny a výrazy lambda v Azure Search dotazů.
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
ms.openlocfilehash: e057d0b57162d10aab13d8b1f77e0eaddca2ec2a
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647642"
---
# <a name="odata-collection-operators-in-azure-search---any-and-all"></a>Operátory kolekce OData v Azure Search- `any` a`all`

Při psaní [výrazu filtru OData](query-odata-filter-orderby-syntax.md) pro použití s Azure Search je často vhodné filtrovat pole kolekcí. Můžete to dosáhnout pomocí `any` operátorů a. `all`

## <a name="syntax"></a>Syntaxe

Následující EBNF ([rozšířený formulář Backus-Naur](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definuje gramatiku výrazu OData, který používá `any` nebo. `all`

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
collection_filter_expression ::=
    field_path'/all(' lambda_expression ')'
    | field_path'/any(' lambda_expression ')'
    | field_path'/any()'

lambda_expression ::= identifier ':' boolean_expression
```

K dispozici je také diagram interaktivní syntaxe:

> [!div class="nextstepaction"]
> [Diagram syntaxe OData pro Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#collection_filter_expression)

> [!NOTE]
> Kompletní EBNF najdete v referenčních informacích k [syntaxi výrazu OData pro Azure Search](search-query-odata-syntax-reference.md) .

Existují tři formy výrazů, které filtrují kolekce.

- První dvě iterování v rámci pole kolekce s použitím predikátu, který je uveden ve formě výrazu lambda pro každý prvek kolekce.
  - Výraz using `all` vrátí `true` , zda je predikát true pro každý prvek kolekce.
  - Výraz using `any` vrátí `true` , zda je predikát true pro alespoň jeden prvek kolekce.
- Třetí forma filtru kolekce používá `any` bez výrazu lambda k otestování, zda je pole kolekce prázdné. Pokud kolekce obsahuje nějaké prvky, vrátí `true`. Pokud je kolekce prázdná, vrátí `false`se.

**Výraz lambda** v filtru kolekce je jako tělo smyčky v programovacím jazyce. Definuje proměnnou, která se nazývá **Proměnná rozsahu**, která obsahuje aktuální prvek kolekce během iterace. Definuje také další logický výraz, který je kritérium filtru, které má být použito pro proměnnou rozsahu pro každý prvek kolekce.

## <a name="examples"></a>Příklady

Porovná dokumenty `tags` , jejichž pole obsahuje přesně řetězec "WiFi":

    tags/any(t: t eq 'wifi')

Porovnává dokumenty, kde každý prvek `ratings` v poli spadá mezi 3 a 5 včetně:

    ratings/all(r: r ge 3 and r le 5)

Porovnává dokumenty, kde jsou některé geografické souřadnice v `locations` poli uvnitř daného mnohoúhelníku:

    locations/any(loc: geo.intersects(loc, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))

Odpovídá dokumentům, `rooms` kde je pole prázdné:

    not rooms/any()

Porovnává dokumenty, kde jsou všechny místnosti `rooms/amenities` , pole obsahuje "TV" `rooms/baseRate` a je menší než 100:

    rooms/all(room: room/amenities/any(a: a eq 'tv') and room/baseRate lt 100.0)

## <a name="limitations"></a>Omezení

Ne každá funkce výrazů filtru je k dispozici uvnitř těla výrazu lambda. Omezení se liší v závislosti na typu dat pole kolekce, které chcete filtrovat. Následující tabulka shrnuje omezení.

[!INCLUDE [Limitations on OData lambda expressions in Azure Search](../../includes/search-query-odata-lambda-limitations.md)]

Další informace o těchto omezeních a příklady najdete v tématu [řešení potíží s filtry kolekce v Azure Search](search-query-troubleshoot-collection-filters.md). Podrobné informace o tom, proč tato omezení existují, najdete v tématu [Principy filtrů kolekcí v Azure Search](search-query-understand-collection-filters.md).

## <a name="next-steps"></a>Další postup  

- [Filtry v Azure Search](search-filters.md)
- [Přehled jazyka výrazů OData pro Azure Search](query-odata-filter-orderby-syntax.md)
- [Referenční dokumentace syntaxe výrazu OData pro Azure Search](search-query-odata-syntax-reference.md)
- [Hledat dokumenty &#40;Azure Search REST API služby&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
