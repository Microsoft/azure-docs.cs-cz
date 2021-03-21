---
title: Odkaz na operátor kolekce OData
titleSuffix: Azure Cognitive Search
description: Při vytváření výrazů filtru v Azure Kognitivní hledáních dotazech použijte operátory "any" a "All" ve výrazech lambda, pokud je filtr na kolekci nebo komplexní pole kolekce.
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
ms.openlocfilehash: 4c1243d5d9122539466e94b6bbfdd5ced588e69a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "88934901"
---
# <a name="odata-collection-operators-in-azure-cognitive-search---any-and-all"></a>Operátory kolekce OData v Azure Kognitivní hledání – `any` a `all`

Při psaní [výrazu filtru OData](query-odata-filter-orderby-syntax.md) pro použití s Azure kognitivní hledání je často užitečné filtrovat pole kolekcí. Můžete to dosáhnout pomocí `any` `all` operátorů a.

## <a name="syntax"></a>Syntax

Následující EBNF ([rozšířený Backus-Naur formulář](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definuje gramatiku výrazu OData, který používá `any` nebo `all` .

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
> [Diagram syntaxe OData pro Azure Kognitivní hledání](https://azuresearch.github.io/odata-syntax-diagram/#collection_filter_expression)

> [!NOTE]
> Kompletní EBNF najdete v článku [referenční informace k syntaxi výrazu OData pro Azure kognitivní hledání](search-query-odata-syntax-reference.md) .

Existují tři formy výrazů, které filtrují kolekce.

- První dvě iterování v rámci pole kolekce s použitím predikátu, který je uveden ve formě výrazu lambda pro každý prvek kolekce.
  - Výraz using `all` vrátí `true` , zda je predikát true pro každý prvek kolekce.
  - Výraz using `any` vrátí `true` , zda je predikát true pro alespoň jeden prvek kolekce.
- Třetí forma filtru kolekce používá `any` bez výrazu lambda k otestování, zda je pole kolekce prázdné. Pokud kolekce obsahuje nějaké prvky, vrátí `true` . Pokud je kolekce prázdná, vrátí se `false` .

**Výraz lambda** v filtru kolekce je jako tělo smyčky v programovacím jazyce. Definuje proměnnou, která se nazývá **Proměnná rozsahu**, která obsahuje aktuální prvek kolekce během iterace. Definuje také další logický výraz, který je kritérium filtru, které má být použito pro proměnnou rozsahu pro každý prvek kolekce.

## <a name="examples"></a>Příklady

Porovná dokumenty `tags` , jejichž pole obsahuje přesně řetězec "WiFi":

```text
tags/any(t: t eq 'wifi')
```

Porovnává dokumenty, kde každý prvek v `ratings` poli spadá mezi 3 a 5 včetně:

```text
ratings/all(r: r ge 3 and r le 5)
```

Porovnává dokumenty, kde jsou některé geografické souřadnice v `locations` poli uvnitř daného mnohoúhelníku:

```text
locations/any(loc: geo.intersects(loc, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))
```

Odpovídá dokumentům, kde `rooms` je pole prázdné:

```text
not rooms/any()
```

Porovnává dokumenty, kde jsou všechny místnosti, `rooms/amenities` pole obsahuje "TV" a `rooms/baseRate` je menší než 100:

```text
rooms/all(room: room/amenities/any(a: a eq 'tv') and room/baseRate lt 100.0)
```

## <a name="limitations"></a>Omezení

Ne každá funkce výrazů filtru je k dispozici uvnitř těla výrazu lambda. Omezení se liší v závislosti na typu dat pole kolekce, které chcete filtrovat. Následující tabulka shrnuje omezení.

[!INCLUDE [Limitations on OData lambda expressions in Azure Cognitive Search](../../includes/search-query-odata-lambda-limitations.md)]

Další podrobnosti o těchto omezeních a příklady najdete v tématu [řešení potíží s filtry kolekcí v Azure kognitivní hledání](search-query-troubleshoot-collection-filters.md). Podrobné informace o tom, proč tato omezení existují, najdete v tématu [Principy filtrů kolekcí v Azure kognitivní hledání](search-query-understand-collection-filters.md).

## <a name="next-steps"></a>Další kroky  

- [Filtry v Azure Kognitivní hledání](search-filters.md)
- [Přehled jazyka výrazů OData pro Azure Kognitivní hledání](query-odata-filter-orderby-syntax.md)
- [Referenční dokumentace syntaxe výrazu OData pro Azure Kognitivní hledání](search-query-odata-syntax-reference.md)
- [Hledání dokumentů &#40;Azure Kognitivní hledání REST API&#41;](/rest/api/searchservice/Search-Documents)