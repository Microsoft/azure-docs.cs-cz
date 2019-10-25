---
title: Odkaz na výběr OData
titleSuffix: Azure Cognitive Search
description: Referenční informace o jazyce OData pro příkaz SELECT syntax v Azure Kognitivní hledání dotazů.
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
ms.openlocfilehash: 7786974f3d39f9cbc81e1ffea955156d623f1476
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793253"
---
# <a name="odata-select-syntax-in-azure-cognitive-search"></a>Syntaxe $select OData v Azure Kognitivní hledání

 Pomocí [parametru OData **$Select** ](query-odata-filter-orderby-syntax.md) můžete zvolit, která pole se mají zahrnout do výsledků hledání z Azure kognitivní hledání. V tomto článku se podrobně popisuje syntaxe **$Select** . Obecnější informace o tom, jak používat **$Select** při prezentaci výsledků hledání, najdete v tématu [jak pracovat s výsledky hledání v Azure kognitivní hledání](search-pagination-page-layout.md).

## <a name="syntax"></a>Syntaxe

Parametr **$Select** určuje, která pole pro každý dokument jsou vrácena v sadě výsledků dotazu. Následující EBNF ([rozšířený formulář Backus-Naur](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definuje gramatiku pro parametr **$Select** :

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
select_expression ::= '*' | field_path(',' field_path)*

field_path ::= identifier('/'identifier)*
```

K dispozici je také diagram interaktivní syntaxe:

> [!div class="nextstepaction"]
> [Diagram syntaxe OData pro Azure Kognitivní hledání](https://azuresearch.github.io/odata-syntax-diagram/#select_expression)

> [!NOTE]
> Kompletní EBNF najdete v článku [referenční informace k syntaxi výrazu OData pro Azure kognitivní hledání](search-query-odata-syntax-reference.md) .

Parametr **$Select** se vyskytuje ve dvou formách:

1. Jedna hvězdička (`*`), která značí, že se mají vracet všechna pole, která lze načíst, nebo
1. Čárkami oddělený seznam cest polí a určení, která pole se mají vrátit

Při použití druhého formuláře můžete v seznamu zadat jenom pole, která lze načíst.

Pokud vypíšete komplexní pole bez explicitního určení jeho dílčích polí, budou do sady výsledků dotazu zahrnuty všechna průchozí dílčí pole. Předpokládejme například, že váš index má pole `Address` s `Street`, `City`a `Country` dílčí pole, která lze načíst. Pokud zadáte `Address` v **$Select**, budou výsledky dotazu zahrnovat všechna tři dílčí pole.

## <a name="examples"></a>Příklady

Do výsledků zahrňte pole `HotelId`, `HotelName`a `Rating` nejvyšší úrovně a také `City` dílčí pole `Address`:

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

Do výsledků zahrňte `HotelName` pole nejvyšší úrovně a také všechna dílčí pole `Address`a `Type` a `BaseRate` dílčí pole každého objektu v kolekci `Rooms`:

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

- [Jak pracovat s výsledky hledání v Azure Kognitivní hledání](search-pagination-page-layout.md)
- [Přehled jazyka výrazů OData pro Azure Kognitivní hledání](query-odata-filter-orderby-syntax.md)
- [Referenční dokumentace syntaxe výrazu OData pro Azure Kognitivní hledání](search-query-odata-syntax-reference.md)
- [Hledání dokumentů &#40;Azure kognitivní hledání REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
