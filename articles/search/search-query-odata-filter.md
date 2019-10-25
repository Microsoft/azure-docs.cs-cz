---
title: Odkaz na filtr OData
titleSuffix: Azure Cognitive Search
description: Referenční dokumentace jazyka OData pro syntaxi filtru v Azure Kognitivní hledání dotazy.
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
ms.openlocfilehash: ba1f5e8f2369d3222b3c31738e252a20b6de8906
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793375"
---
# <a name="odata-filter-syntax-in-azure-cognitive-search"></a>Syntaxe $filter OData v Azure Kognitivní hledání

Azure Kognitivní hledání používá [výrazy filtru OData](query-odata-filter-orderby-syntax.md) k použití dalších kritérií pro vyhledávací dotaz kromě výrazů pro fulltextové vyhledávání. Tento článek popisuje syntaxi filtrů podrobněji. Obecnější informace o tom, jaké filtry jsou a jak je použít k realizaci konkrétních scénářů dotazů, najdete v tématu [filtry v Azure kognitivní hledání](search-filters.md).

## <a name="syntax"></a>Syntaxe

Filtr v jazyce OData je logický výraz, který může být jedním z několika typů výrazů, jak je znázorněno na následujícím EBNF ([rozšířený formulář Backus-Naur](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)):

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

- Výrazy filtru kolekce používající `any` nebo `all`. Ty aplikují kritéria filtru na pole kolekce. Další informace najdete v tématu [operátory kolekce OData v Azure kognitivní hledání](search-query-odata-collection-operators.md).
- Logické výrazy, které spojují jiné logické výrazy pomocí operátorů `and`, `or`a `not`. Další informace najdete v tématu [logické operátory OData v Azure kognitivní hledání](search-query-odata-logical-operators.md).
- Výrazy porovnání, které porovnávají pole nebo proměnné rozsahu s konstantními hodnotami pomocí operátorů `eq`, `ne`, `gt`, `lt`, `ge`a `le`. Další informace najdete v tématu [operátory porovnání OData v Azure kognitivní hledání](search-query-odata-comparison-operators.md). Výrazy porovnání jsou také použity k porovnání vzdálenosti mezi geograficky prostorovými souřadnicemi pomocí funkce `geo.distance`. Další informace najdete v tématu [funkce geografického prostoru OData v Azure kognitivní hledání](search-query-odata-geo-spatial-functions.md).
- Logické literály `true` a `false`. Tyto konstanty můžou být užitečné někdy při generování filtrů, ale jinak se v praxi nepoužívá.
- Volání logických funkcí, včetně:
  - `geo.intersects`, která testuje, zda je daný bod v rámci daného mnohoúhelníku. Další informace najdete v tématu [funkce geografického prostoru OData v Azure kognitivní hledání](search-query-odata-geo-spatial-functions.md).
  - `search.in`, která porovnává proměnnou pole nebo rozsahu s každou hodnotou v seznamu hodnot. Další informace najdete v tématu [funkce `search.in` OData ve službě Azure kognitivní hledání](search-query-odata-search-in-function.md).
  - `search.ismatch` a `search.ismatchscoring`, které provádějí operace fulltextového vyhledávání v kontextu filtru. Další informace najdete v tématu [funkce fulltextového vyhledávání OData v Azure kognitivní hledání](search-query-odata-full-text-search-functions.md).
- Cesty pole nebo proměnné rozsahu typu `Edm.Boolean`. Například pokud má váš index pole Boolean s názvem `IsEnabled` a chcete vrátit všechny dokumenty, kde je toto pole `true`, váš výraz filtru může být pouze název `IsEnabled`.
- Logické výrazy v závorkách. Použití závorek vám může pomoci explicitně určit pořadí operací ve filtru. Další informace o výchozí prioritě operátorů OData najdete v další části.

### <a name="operator-precedence-in-filters"></a>Priorita operátorů ve filtrech

Pokud napíšete výraz filtru bez závorek kolem jeho dílčích výrazů, Azure Kognitivní hledání ho vyhodnotí podle sady pravidel priority operátorů. Tato pravidla jsou založena na operátorech, které slouží ke kombinování dílčích výrazů. V následující tabulce jsou uvedeny skupiny operátorů v pořadí od nejvyšší po nejnižší prioritu:

| Skupina | Operátor (y) |
| --- | --- |
| Logické operátory | `not` |
| Operátory porovnání | `eq`, `ne`, `gt`, `lt`, `ge``le` |
| Logické operátory | `and` |
| Logické operátory | `or` |

Operátor, který je vyšší ve výše uvedené tabulce, bude "vazba propojuje" na jeho operandy než jiné operátory. Například `and` má vyšší prioritu než `or`a operátory porovnání mají vyšší prioritu než jedna z nich, takže následující dva výrazy jsou ekvivalentní:

    Rating gt 0 and Rating lt 3 or Rating gt 7 and Rating lt 10
    ((Rating gt 0) and (Rating lt 3)) or ((Rating gt 7) and (Rating lt 10))

Operátor `not` má nejvyšší prioritu, a to i vyšší, než jsou operátory porovnání. Proto pokud se pokusíte napsat filtr takto:

    not Rating gt 5

Zobrazí se tato chybová zpráva:

    Invalid expression: A unary operator with an incompatible type was detected. Found operand type 'Edm.Int32' for operator kind 'Not'.

K této chybě dochází, protože operátor je přidružen pouze k poli `Rating`, které je typu `Edm.Int32`a nikoli celým výrazem porovnání. Opravou je vložení operandu `not` v závorkách:

    not (Rating gt 5)

<a name="bkmk_limits"></a>

### <a name="filter-size-limitations"></a>Omezení velikosti filtru

Existují omezení velikosti a složitosti výrazů filtru, které můžete odeslat do služby Azure Kognitivní hledání. Omezení jsou založena přibližně na počtu klauzulí ve výrazu filtru. Dobrým vodítkem je to, že pokud máte stovky klauzulí, riskujete překročení tohoto limitu. Doporučujeme navrhnout aplikaci tak, aby negenerovala filtry neohraničené velikosti.

> [!TIP]
> Použití [funkce `search.in`](search-query-odata-search-in-function.md) místo dlouhého úniku porovnání rovnosti vám může pomoci zabránit omezení klauzule filtru, protože volání funkce se počítá jako jedna klauzule.

## <a name="examples"></a>Příklady

Najděte všechny hotely s minimálně jednou místností se základní sazbou nižší než $200, která je ohodnocena na nebo vyšší než 4:

    $filter=Rooms/any(room: room/BaseRate lt 200.0) and Rating ge 4

Vyhledejte všechny hotely jiné v hotelech než "Motel View", které byly renovated od 2010:

    $filter=HotelName ne 'Sea View Motel' and LastRenovationDate ge 2010-01-01T00:00:00Z

Vyhledejte všechny hotely, které se renovated v 2010 nebo novějším. Literál DateTime obsahuje informace o časovém pásmu pro Tichomoří (běžný čas):  

    $filter=LastRenovationDate ge 2010-01-01T00:00:00-08:00

Vyhledejte všechny hotely, které mají zabalení a kde jsou všechny místnosti mimo kouření:

    $filter=ParkingIncluded and Rooms/all(room: not room/SmokingAllowed)

 \- nebo-  

    $filter=ParkingIncluded eq true and Rooms/all(room: room/SmokingAllowed eq false)

Vyhledejte všechny hotely, které jsou luxus nebo zahrňte do parkování a mají hodnocení 5:  

    $filter=(Category eq 'Luxury' or ParkingIncluded eq true) and Rating eq 5

Vyhledejte všechny hotely s označením "WiFi" alespoň v jedné místnosti (kde každá místnost obsahuje značky uložené v `Collection(Edm.String)` poli):  

    $filter=Rooms/any(room: room/Tags/any(tag: tag eq 'wifi'))

Vyhledejte všechny hotely pomocí všech místností:  

    $filter=Rooms/any()

Najít všechny hotely, které nemají místnosti:

    $filter=not Rooms/any()

Najde všechny hotely v rámci 10 kilometrů daného referenčního bodu (kde `Location` je pole typu `Edm.GeographyPoint`):

    $filter=geo.distance(Location, geography'POINT(-122.131577 47.678581)') le 10

Najde všechny hotely v rámci daného zobrazení popsané jako mnohoúhelník (kde `Location` je pole typu EDM. GeographyPoint). Mnohoúhelník musí být uzavřen, což znamená, že první a poslední bod musí být stejné. [Body musí být také uvedeny v pořadí zprava doleva](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

    $filter=geo.intersects(Location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

Najde všechny hotely, u kterých má pole Popis hodnotu null. Pole bude null, pokud nebylo nikdy nastaveno, nebo pokud bylo explicitně nastaveno na hodnotu null:  

    $filter=Description eq null

Vyhledá všechny hotely s názvem, který se rovná buď Motel, nebo "rozpočet hotel"). Tyto fráze obsahují mezery a výchozí oddělovač je mezera. Můžete zadat alternativní oddělovač v jednoduchých uvozovkách jako třetí řetězcový parametr:  

    $filter=search.in(HotelName, 'Sea View motel,Budget hotel', ',')

Najde všechny hotely s názvem, které se rovnají buď Motel, nebo "rozpočet hotel" oddělené |):  

    $filter=search.in(HotelName, 'Sea View motel|Budget hotel', '|')

Vyhledejte všechny hotely, kde všechny místnosti mají značku WiFi nebo vyskočí:

    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub'))

Najde shodu u frází v rámci kolekce, jako jsou například "zahřívaná racky navrhování ručníků" nebo "hairdryer include" ve značkách.

    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))

Vyhledá dokumenty ve slově "Waterfront". Tento dotaz filtru je stejný jako [požadavek hledání](https://docs.microsoft.com/rest/api/searchservice/search-documents) s `search=waterfront`.

    $filter=search.ismatchscoring('waterfront')

Najde dokumenty se slovem "Hostel" a hodnocením větším nebo rovným 4 nebo dokumenty se slovem "Motel" a hodnocením rovným 5. Tuto žádost nebylo možné vyjádřit bez funkce `search.ismatchscoring`, protože kombinuje fulltextové vyhledávání s operacemi filtrování pomocí `or`.

    $filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5

Najde dokumenty bez slova "luxus".

    $filter=not search.ismatch('luxury')

Vyhledá dokumenty se frází "zobrazení v oceánu" nebo hodnocení rovno 5. Dotaz `search.ismatchscoring` bude proveden pouze proti polím `HotelName` a `Description`. Dokumenty, které odpovídají pouze druhé klauzuli disjunkce, budou vráceny příliš-Hotely s `Rating` rovny 5. Tyto dokumenty se vrátí se skóre, které se rovná nule, aby bylo jasné, že neodpovídaly žádné z částí výrazu ve výsledku.

    $filter=search.ismatchscoring('"ocean view"', 'Description,HotelName') or Rating eq 5

Najděte si hotely, kde jsou termíny "Hotel" a "letiště" více než pět slov od popisu a kde všechny místnosti jsou nekouření. Tento dotaz používá [celý dotazovací jazyk Lucene](query-lucene-syntax.md).

    $filter=search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and not Rooms/any(room: room/SmokingAllowed)

## <a name="next-steps"></a>Další kroky  

- [Filtry v Azure Kognitivní hledání](search-filters.md)
- [Přehled jazyka výrazů OData pro Azure Kognitivní hledání](query-odata-filter-orderby-syntax.md)
- [Referenční dokumentace syntaxe výrazu OData pro Azure Kognitivní hledání](search-query-odata-syntax-reference.md)
- [Hledání dokumentů &#40;Azure kognitivní hledání REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
