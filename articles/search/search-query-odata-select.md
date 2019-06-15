---
title: Vyberte odkaz OData – Azure Search
description: Referenční dokumentace jazyka OData pro výběr syntaxe v dotazech Azure Search.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
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
ms.openlocfilehash: 9383ae725fffac55854488ffbc6aeb161ae7e0c2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079675"
---
# <a name="odata-select-syntax-in-azure-search"></a>Syntaxe OData $select ve službě Azure Search

 Můžete použít [OData **$select** parametr](query-odata-filter-orderby-syntax.md) zvolit pole, která chcete zahrnout do výsledků hledání z Azure Search. Tento článek popisuje syntaxi **$select** podrobně. Další obecné informace o tom, jak používat **$select** při zobrazení výsledků hledání, najdete v článku [výsledků jak pracovat s hledáním ve službě Azure Search](search-pagination-page-layout.md).

## <a name="syntax"></a>Syntaxe

**$Select** parametr určuje pole, která pro každý dokument jsou vráceny v sadě výsledků dotazu. Následující EBNF ([Extended Backus-Naur – formulář](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definuje gramatiky **$select** parametr:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
select_expression ::= '*' | field_path(',' field_path)*

field_path ::= identifier('/'identifier)*
```

Diagramu interaktivní syntaxe je také k dispozici:

> [!div class="nextstepaction"]
> [Diagram syntaxe OData pro službu Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#select_expression)

> [!NOTE]
> Zobrazit [referenční příručka syntaxe výrazů OData pro službu Azure Search](search-query-odata-syntax-reference.md) pro dokončení EBNF.

**$Select** parametr je k dispozici ve dvou formách:

1. Jednu hvězdičku (`*`), což indikuje, že všechny zobrazitelná pole by měla být vrácena, nebo
1. Čárkou oddělený seznam cest pole, určení polí, která by měla vrátit.

Při použití druhý formulář, může určit pouze zobrazitelná pole v seznamu.

Pokud uvedete seznam komplexní pole bez explicitním zadáním dílčích polí, všechny zobrazitelná dílčí pole součástí sady výsledků dotazu. Předpokládejme například, indexu má `Address` pole `Street`, `City`, a `Country` dílčí pole, které jsou všechny retrievable. Pokud zadáte `Address` v **$select**, výsledky dotazu bude obsahovat všechny tři dílčí pole.

## <a name="examples"></a>Příklady

Zahrnout `HotelId`, `HotelName`, a `Rating` pole nejvyšší úrovně ve výsledcích, stejně jako `City` dílčí pole `Address`:

    $select=HotelId, HotelName, Rating, Address/City

Výsledek příklad může vypadat takto:

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

Zahrnout `HotelName` nejvyšší úrovně pole ve výsledcích, stejně jako všechny dílčí pole `Address`a `Type` a `BaseRate` dílčí pole všech objektů `Rooms` kolekce:

    $select=HotelName, Address, Rooms/Type, Rooms/BaseRate

Výsledek příklad může vypadat takto:

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

## <a name="next-steps"></a>Další postup  

- [Jak pracovat s hledání výsledků ve službě Azure Search](search-pagination-page-layout.md)
- [Přehled o výraz jazyka OData pro službu Azure Search](query-odata-filter-orderby-syntax.md)
- [Reference k syntaxi výrazů OData pro službu Azure Search](search-query-odata-syntax-reference.md)
- [Hledání dokumentů &#40;rozhraní REST API služby Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
