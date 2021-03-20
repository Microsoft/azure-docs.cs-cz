---
title: Odkaz na filtr OData
titleSuffix: Azure Cognitive Search
description: Referenční dokumentace jazyka OData a úplná syntaxe používaná k vytváření výrazů filtru v Azure Kognitivní hledáních dotazech.
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
ms.openlocfilehash: 0f33b5a28d7c83be7e546c3f61bc517047c51312
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "88934850"
---
# <a name="odata-filter-syntax-in-azure-cognitive-search"></a>Syntaxe $filter OData v Azure Kognitivní hledání

Azure Kognitivní hledání používá [výrazy filtru OData](query-odata-filter-orderby-syntax.md) k použití dalších kritérií pro vyhledávací dotaz kromě výrazů pro fulltextové vyhledávání. Tento článek popisuje syntaxi filtrů podrobněji. Obecnější informace o tom, jaké filtry jsou a jak je použít k realizaci konkrétních scénářů dotazů, najdete v tématu [filtry v Azure kognitivní hledání](search-filters.md).

## <a name="syntax"></a>Syntax

Filtr v jazyce OData je logický výraz, který může být jedním z několika typů výrazů, jak ukazuje následující EBNF ([rozšířený Backus-Naur formulář](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)):

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
boolean_expression ::=
    collection_filter_expression
    | logical_expression
    | comparison_expression
    | boolean_literal
    | boolean_function_call
    | '(' boolean_expression ')'
    | variable

/* This can be a range variable in the case of a lambda, or a field path. */
variable ::= identifier | field_path
```

K dispozici je také diagram interaktivní syntaxe:

> [!div class="nextstepaction"]
> [Diagram syntaxe OData pro Azure Kognitivní hledání](https://azuresearch.github.io/odata-syntax-diagram/#boolean_expression)

> [!NOTE]
> Kompletní EBNF najdete v článku [referenční informace k syntaxi výrazu OData pro Azure kognitivní hledání](search-query-odata-syntax-reference.md) .

Mezi typy logických výrazů patří:

- Výrazy filtru kolekce pomocí `any` nebo `all` . Ty aplikují kritéria filtru na pole kolekce. Další informace najdete v tématu [operátory kolekce OData v Azure kognitivní hledání](search-query-odata-collection-operators.md).
- Logické výrazy, které spojují jiné logické výrazy pomocí operátorů `and` , `or` a `not` . Další informace najdete v tématu [logické operátory OData v Azure kognitivní hledání](search-query-odata-logical-operators.md).
- Výrazy porovnání, které porovnávají pole nebo proměnné rozsahu s konstantními hodnotami pomocí operátorů `eq` ,,,, `ne` `gt` `lt` `ge` a `le` . Další informace najdete v tématu [operátory porovnání OData v Azure kognitivní hledání](search-query-odata-comparison-operators.md). Výrazy porovnání jsou také použity k porovnání vzdálenosti mezi geograficky prostorovými souřadnicemi pomocí `geo.distance` funkce. Další informace najdete v tématu [funkce geografického prostoru OData v Azure kognitivní hledání](search-query-odata-geo-spatial-functions.md).
- Logické literály `true` a `false` . Tyto konstanty můžou být užitečné někdy při generování filtrů, ale jinak se v praxi nepoužívá.
- Volání logických funkcí, včetně:
  - `geo.intersects`, který testuje, zda je daný bod v rámci daného mnohoúhelníku. Další informace najdete v tématu [funkce geografického prostoru OData v Azure kognitivní hledání](search-query-odata-geo-spatial-functions.md).
  - `search.in`, který porovnává pole nebo proměnnou rozsahu s každou hodnotou v seznamu hodnot. Další informace najdete v tématu [ `search.in` funkce OData v Azure kognitivní hledání](search-query-odata-search-in-function.md).
  - `search.ismatch` a `search.ismatchscoring` , které provádějí operace fulltextového vyhledávání v kontextu filtru. Další informace najdete v tématu [funkce fulltextového vyhledávání OData v Azure kognitivní hledání](search-query-odata-full-text-search-functions.md).
- Cesty pole nebo proměnné `Edm.Boolean` rozsahu typu Například pokud má váš index pole Boolean s názvem a chcete `IsEnabled` vrátit všechny dokumenty, kde je toto pole `true` , váš výraz filtru může být pouze název `IsEnabled` .
- Logické výrazy v závorkách. Použití závorek vám může pomoci explicitně určit pořadí operací ve filtru. Další informace o výchozí prioritě operátorů OData najdete v další části.

### <a name="operator-precedence-in-filters"></a>Priorita operátorů ve filtrech

Pokud napíšete výraz filtru bez závorek kolem jeho dílčích výrazů, Azure Kognitivní hledání ho vyhodnotí podle sady pravidel priority operátorů. Tato pravidla jsou založena na operátorech, které slouží ke kombinování dílčích výrazů. V následující tabulce jsou uvedeny skupiny operátorů v pořadí od nejvyšší po nejnižší prioritu:

| Group (Skupina) | Operátor (y) |
| --- | --- |
| Logické operátory | `not` |
| Relační operátory | `eq`, `ne`, `gt`, `lt`, `ge`, `le` |
| Logické operátory | `and` |
| Logické operátory | `or` |

Operátor, který je vyšší ve výše uvedené tabulce, bude "vazba propojuje" na jeho operandy než jiné operátory. Například `and` má vyšší prioritu než `or` a operátory porovnání mají vyšší prioritu než jedna z nich, takže následující dva výrazy jsou ekvivalentní:

```odata-filter-expr
    Rating gt 0 and Rating lt 3 or Rating gt 7 and Rating lt 10
    ((Rating gt 0) and (Rating lt 3)) or ((Rating gt 7) and (Rating lt 10))
```

`not`Operátor má nejvyšší prioritu vše – dokonce i vyšší než operátory porovnání. Proto pokud se pokusíte napsat filtr takto:

```odata-filter-expr
    not Rating gt 5
```

Zobrazí se tato chybová zpráva:

```text
    Invalid expression: A unary operator with an incompatible type was detected. Found operand type 'Edm.Int32' for operator kind 'Not'.
```

K této chybě dochází, protože operátor je přidružen pouze k `Rating` poli, které je typu `Edm.Int32` a nikoli k celému srovnávacímu výrazu. Opravou je vložení operandu `not` do závorek:

```odata-filter-expr
    not (Rating gt 5)
```

<a name="bkmk_limits"></a>

### <a name="filter-size-limitations"></a>Omezení velikosti filtru

Existují omezení velikosti a složitosti výrazů filtru, které můžete odeslat do služby Azure Kognitivní hledání. Omezení jsou založena přibližně na počtu klauzulí ve výrazu filtru. Dobrým vodítkem je to, že pokud máte stovky klauzulí, riskujete překročení tohoto limitu. Doporučujeme navrhnout aplikaci tak, aby negenerovala filtry neohraničené velikosti.

> [!TIP]
> Použití [ `search.in` funkce](search-query-odata-search-in-function.md) namísto dlouhého disjunkce porovnávání rovnosti vám může pomoci zabránit omezení klauzule filtru, protože volání funkce se počítá jako jedna klauzule.

## <a name="examples"></a>Příklady

Najděte všechny hotely s minimálně jednou místností se základní sazbou nižší než $200, která je ohodnocena na nebo vyšší než 4:

```odata-filter-expr
    $filter=Rooms/any(room: room/BaseRate lt 200.0) and Rating ge 4
```

Vyhledejte všechny hotely jiné v hotelech než "Motel View", které byly renovated od 2010:

```odata-filter-expr
    $filter=HotelName ne 'Sea View Motel' and LastRenovationDate ge 2010-01-01T00:00:00Z
```

Vyhledejte všechny hotely, které se renovated v 2010 nebo novějším. Literál DateTime obsahuje informace o časovém pásmu pro Tichomoří (běžný čas):  

```odata-filter-expr
    $filter=LastRenovationDate ge 2010-01-01T00:00:00-08:00
```

Vyhledejte všechny hotely, které mají zabalení a kde jsou všechny místnosti mimo kouření:

```odata-filter-expr
    $filter=ParkingIncluded and Rooms/all(room: not room/SmokingAllowed)
```

 \- Ani  

```odata-filter-expr
    $filter=ParkingIncluded eq true and Rooms/all(room: room/SmokingAllowed eq false)
```

Vyhledejte všechny hotely, které jsou luxus nebo zahrňte do parkování a mají hodnocení 5:  

```odata-filter-expr
    $filter=(Category eq 'Luxury' or ParkingIncluded eq true) and Rating eq 5
```

Najde všechny hotely s označením "WiFi" alespoň v jedné místnosti (kde každá místnost obsahuje značky uložené v `Collection(Edm.String)` poli):  

```odata-filter-expr
    $filter=Rooms/any(room: room/Tags/any(tag: tag eq 'wifi'))
```

Vyhledejte všechny hotely pomocí všech místností:  

```odata-filter-expr
    $filter=Rooms/any()
```

Najít všechny hotely, které nemají místnosti:

```odata-filter-expr
    $filter=not Rooms/any()
```

Najde všechny hotely v průběhu 10 km od daného referenčního bodu (kde `Location` je pole typu `Edm.GeographyPoint` ):

```odata-filter-expr
    $filter=geo.distance(Location, geography'POINT(-122.131577 47.678581)') le 10
```

Najde všechny hotely v daném zobrazení, které jsou popsány jako mnohoúhelník (kde `Location` je pole typu EDM. GeographyPoint). Mnohoúhelník musí být uzavřen, což znamená, že první a poslední bod musí být stejné. [Body musí být také uvedeny v pořadí zprava doleva](/rest/api/searchservice/supported-data-types#Anchor_1).

```odata-filter-expr
    $filter=geo.intersects(Location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')
```

Najde všechny hotely, u kterých má pole Popis hodnotu null. Pole bude null, pokud nebylo nikdy nastaveno, nebo pokud bylo explicitně nastaveno na hodnotu null:  

```odata-filter-expr
    $filter=Description eq null
```

Vyhledá všechny hotely s názvem, který se rovná buď Motel, nebo "rozpočet hotel"). Tyto fráze obsahují mezery a výchozí oddělovač je mezera. Můžete zadat alternativní oddělovač v jednoduchých uvozovkách jako třetí řetězcový parametr:  

```odata-filter-expr
    $filter=search.in(HotelName, 'Sea View motel,Budget hotel', ',')
```

Najde všechny hotely s názvem, které se rovnají buď Motel, nebo "rozpočet hotel" oddělené |):  

```odata-filter-expr
    $filter=search.in(HotelName, 'Sea View motel|Budget hotel', '|')
```

Vyhledejte všechny hotely, kde všechny místnosti mají značku WiFi nebo vyskočí:

```odata-filter-expr
    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub'))
```

Najde shodu u frází v rámci kolekce, jako jsou například "zahřívaná racky navrhování ručníků" nebo "hairdryer include" ve značkách.

```odata-filter-expr
    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))
```

Vyhledá dokumenty ve slově "Waterfront". Tento dotaz filtru je stejný jako [požadavek na hledání](/rest/api/searchservice/search-documents) s `search=waterfront` .

```odata-filter-expr
    $filter=search.ismatchscoring('waterfront')
```

Najde dokumenty se slovem "Hostel" a hodnocením větším nebo rovným 4 nebo dokumenty se slovem "Motel" a hodnocením rovným 5. Tuto žádost nebylo možné vyjádřit bez `search.ismatchscoring` funkce, protože kombinuje fulltextové vyhledávání s operacemi filtru pomocí `or` .

```odata-filter-expr
    $filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5
```

Najde dokumenty bez slova "luxus".

```odata-filter-expr
    $filter=not search.ismatch('luxury')
```

Vyhledá dokumenty se frází "zobrazení v oceánu" nebo hodnocení rovno 5. `search.ismatchscoring`Dotaz bude proveden pouze proti polím `HotelName` a `Description` . Dokumenty, které odpovídají pouze druhé klauzuli disjunkce, budou vráceny příliš-Hotely s `Rating` hodnotou 5. Tyto dokumenty se vrátí se skóre, které se rovná nule, aby bylo jasné, že neodpovídaly žádné z částí výrazu ve výsledku.

```odata-filter-expr
    $filter=search.ismatchscoring('"ocean view"', 'Description,HotelName') or Rating eq 5
```

Najděte si hotely, kde jsou termíny "Hotel" a "letiště" více než pět slov od popisu a kde všechny místnosti jsou nekouření. Tento dotaz používá [celý dotazovací jazyk Lucene](query-lucene-syntax.md).

```odata-filter-expr
    $filter=search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and not Rooms/any(room: room/SmokingAllowed)
```

## <a name="next-steps"></a>Další kroky  

- [Filtry v Azure Kognitivní hledání](search-filters.md)
- [Přehled jazyka výrazů OData pro Azure Kognitivní hledání](query-odata-filter-orderby-syntax.md)
- [Referenční dokumentace syntaxe výrazu OData pro Azure Kognitivní hledání](search-query-odata-syntax-reference.md)
- [Hledání dokumentů &#40;Azure Kognitivní hledání REST API&#41;](/rest/api/searchservice/Search-Documents)