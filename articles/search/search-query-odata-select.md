---
title: Odkaz na výběr OData – Azure Search
description: Referenční dokumentace jazyka OData pro příkaz SELECT syntax v Azure Searchch dotazech.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
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
ms.openlocfilehash: 64e9ad75d88f595ab5def6fe8b63fee9407ae0fe
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647878"
---
# <a name="odata-select-syntax-in-azure-search"></a>Syntaxe $select OData v Azure Search

 Pomocí [parametru **$Select** OData](query-odata-filter-orderby-syntax.md) můžete zvolit, která pole se mají zahrnout do výsledků hledání z Azure Search. V tomto článku se podrobně popisuje syntaxe **$Select** . Obecnější informace o tom, jak používat **$Select** při prezentaci výsledků hledání, najdete [v tématu Jak pracovat s výsledky hledání v Azure Search](search-pagination-page-layout.md).

## <a name="syntax"></a>Syntaxe

Parametr **$Select** určuje, která pole pro každý dokument jsou vrácena v sadě výsledků dotazu. Následující EBNF ([rozšířený formulář Backus-Naur](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definuje gramatiku pro parametr **$Select** :

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
select_expression ::= '*' | field_path(',' field_path)*

field_path ::= identifier('/'identifier)*
```

K dispozici je také diagram interaktivní syntaxe:

> [!div class="nextstepaction"]
> [Diagram syntaxe OData pro Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#select_expression)

> [!NOTE]
> Kompletní EBNF najdete v referenčních informacích k [syntaxi výrazu OData pro Azure Search](search-query-odata-syntax-reference.md) .

Parametr **$Select** se vyskytuje ve dvou formách:

1. Jedna hvězdička (`*`), která označuje, že by měla být vrácena všechna pole k dispozici, nebo
1. Čárkami oddělený seznam cest polí a určení, která pole se mají vrátit

Při použití druhého formuláře můžete v seznamu zadat jenom pole, která lze načíst.

Pokud vypíšete komplexní pole bez explicitního určení jeho dílčích polí, budou do sady výsledků dotazu zahrnuty všechna průchozí dílčí pole. Předpokládejme například, že `Address` váš index obsahuje pole s `Street`, `City`a `Country` podpole, která lze načíst. Pokud zadáte `Address` v **$Select**, budou výsledky dotazu zahrnovat všechna tři dílčí pole.

## <a name="examples"></a>Příklady

`HotelName` `Rating` Dovýsledků`Address`uveďte pole `HotelId`, a nejvyšší úrovně a také `City` dílčí pole pro:

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

`BaseRate` `Address` `Type` Do výsledků zahrňte pole `Rooms` nejvyšší úrovně a také všechna dílčí pole a a dílčí pole jednotlivých objektů v kolekci: `HotelName`

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

- [Jak pracovat s výsledky hledání v Azure Search](search-pagination-page-layout.md)
- [Přehled jazyka výrazů OData pro Azure Search](query-odata-filter-orderby-syntax.md)
- [Referenční dokumentace syntaxe výrazu OData pro Azure Search](search-query-odata-syntax-reference.md)
- [Hledat dokumenty &#40;Azure Search REST API služby&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
