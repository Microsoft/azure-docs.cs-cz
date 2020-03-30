---
title: OData vybrat odkaz
titleSuffix: Azure Cognitive Search
description: Syntaxe a odkaz na jazyk pro explicitní výběr polí, která se mají vrátit ve výsledcích hledání dotazů Azure Cognitive Search.
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
ms.openlocfilehash: 64f15bf3d262249cdda2760c7ddf768be2590419
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113099"
---
# <a name="odata-select-syntax-in-azure-cognitive-search"></a>Syntaxe $select OData v Azure Cognitive Search

 [Pomocí parametru OData **$select** ](query-odata-filter-orderby-syntax.md) můžete zvolit, která pole mají být zahrnuta do výsledků hledání z Azure Cognitive Search. Tento článek podrobně popisuje syntaxi **$select.** Obecnější informace o tom, jak používat **$select** při prezentaci výsledků hledání, najdete [v tématu Jak pracovat s výsledky hledání v Azure Cognitive Search](search-pagination-page-layout.md).

## <a name="syntax"></a>Syntaxe

Parametr **$select** určuje, která pole pro každý dokument jsou vrácena v sadě výsledků dotazu. Gramatiku parametru **$select** definuje následující formulář EBNF ([Extended Backus-Naur Form):](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
select_expression ::= '*' | field_path(',' field_path)*

field_path ::= identifier('/'identifier)*
```

K dispozici je také interaktivní syntaktický diagram:

> [!div class="nextstepaction"]
> [Syntaxový diagram OData pro Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#select_expression)

> [!NOTE]
> Viz [odkaz na syntaxi výrazu OData pro Azure Cognitive Search](search-query-odata-syntax-reference.md) pro kompletní EBNF.

Parametr **$select** je dodáván ve dvou formách:

1. Jedna hvězdička`*`( ), označující, že všechna načítatelná pole by měla být vrácena, nebo
1. Seznam cest polí oddělených čárkami, který určuje, která pole mají být vrácena.

Při použití druhého formuláře můžete v seznamu zadat pouze navíjecí pole.

Pokud uvedete složité pole bez explicitního zadání jeho dílčích polí, budou všechna načítatelná dílčí pole zahrnuta do sady výsledků dotazu. Předpokládejme například, že `Address` index `Street` `City`má `Country` pole s , a dílčí pole, které lze vyhledat. Pokud zadáte `Address` v **$select**, budou výsledky dotazu obsahovat všechna tři dílčí pole.

## <a name="examples"></a>Příklady

Do `HotelId`výsledků `HotelName`uveďte pole , a `Rating` horní úroveň `City` a podpole `Address`:

    $select=HotelId, HotelName, Rating, Address/City

Příklad výsledku může vypadat takto:

```json
{
  "HotelId": "1",
  "HotelName": "Secret Point Motel",
  "Rating": 4,
  "Address": {
    "City": "New York"
  }
}
```

Do `HotelName` výsledků zahrňte pole nejvyšší úrovně a také `Address`všechna `Type` dílčí `BaseRate` pole položky , `Rooms` a dílčí pole a pole každého objektu v kolekci:

    $select=HotelName, Address, Rooms/Type, Rooms/BaseRate

Příklad výsledku může vypadat takto:

```json
{
  "HotelName": "Secret Point Motel",
  "Rating": 4,
  "Address": {
    "StreetAddress": "677 5th Ave",
    "City": "New York",
    "StateProvince": "NY",
    "Country": "USA",
    "PostalCode": "10022"
  },
  "Rooms": [
    {
      "Type": "Budget Room",
      "BaseRate": 9.69
    },
    {
      "Type": "Budget Room",
      "BaseRate": 8.09
    }
  ]
}
```

## <a name="next-steps"></a>Další kroky  

- [Jak pracovat s výsledky hledání v Azure Cognitive Search](search-pagination-page-layout.md)
- [Přehled jazyka OData pro Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Odkaz na syntaxi výrazu OData pro Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [Hledání dokumentů &#40;azure kognitivní vyhledávání rozhraní API rozhraní&#41;API](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
