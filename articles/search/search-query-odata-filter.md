---
title: Filtr OData – reference – Azure Search
description: Referenční dokumentace jazyka OData pro syntaxi filtru v dotazech Azure Search.
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
ms.openlocfilehash: c44645ebcbf8808cc929bfaa0c3cb0d3ee9c90cd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079909"
---
# <a name="odata-filter-syntax-in-azure-search"></a>Syntaxe OData $filter ve službě Azure Search

Služba Azure Search používá [výrazů filtru OData](query-odata-filter-orderby-syntax.md) použít další kritéria pro vyhledávací dotaz kromě podmínky fulltextové vyhledávání. Tento článek popisuje syntaxi filtry podrobně. Další obecné informace o co jsou filtry a jak se dají použít k realizaci scénáře konkrétní dotaz, naleznete v tématu [filtry ve službě Azure Search](search-filters.md).

## <a name="syntax"></a>Syntaxe

Filtr v jazyce OData je logický výraz, který pak může být jedním z několika typů výrazu, jak je znázorněno v následujícím EBNF ([Extended Backus-Naur – formulář](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)):

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

Diagramu interaktivní syntaxe je také k dispozici:

> [!div class="nextstepaction"]
> [Diagram syntaxe OData pro službu Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#boolean_expression)

> [!NOTE]
> Zobrazit [referenční příručka syntaxe výrazů OData pro službu Azure Search](search-query-odata-syntax-reference.md) pro dokončení EBNF.

Typy logické výrazy patří:

- Kolekce výrazů filtru pomocí `any` nebo `all`. Kritéria filtru platí pro kolekci polí. Další informace najdete v tématu [OData shromažďování operátory ve službě Azure Search](search-query-odata-collection-operators.md).
- Logické výrazy, které kombinují jiné logické výrazy pomocí operátorů `and`, `or`, a `not`. Další informace najdete v tématu [OData logické operátory ve službě Azure Search](search-query-odata-logical-operators.md).
- Porovnání výrazů, které porovnávat pole nebo rozsahu proměnných na konstantní hodnoty pomocí operátorů `eq`, `ne`, `gt`, `lt`, `ge`, a `le`. Další informace najdete v tématu [operátorů porovnání OData ve službě Azure Search](search-query-odata-comparison-operators.md). Výrazy porovnání slouží také k porovnání vzdálenosti mezi geografické souřadnice pomocí `geo.distance` funkce. Další informace najdete v tématu [OData geoprostorové funkce ve službě Azure Search](search-query-odata-geo-spatial-functions.md).
- Logické hodnoty literálu `true` a `false`. Tyto konstanty může být užitečné v některých případech při generování programově filtry, ale jinak není pro použití v praxi.
- Volání logické funkce, včetně:
  - `geo.intersects`, který testuje, jestli je daný bod v rámci dané mnohoúhelníku. Další informace najdete v tématu [OData geoprostorové funkce ve službě Azure Search](search-query-odata-geo-spatial-functions.md).
  - `search.in`, který porovnává pole nebo rozsahu proměnné s každou hodnotu v seznamu hodnot. Další informace najdete v tématu [OData `search.in` funkce ve službě Azure Search](search-query-odata-search-in-function.md).
  - `search.ismatch` a `search.ismatchscoring`, který provádění operací vyhledávání v kontextu filtru. Další informace najdete v tématu [OData funkce fulltextového vyhledávání ve službě Azure Search](search-query-odata-full-text-search-functions.md).
- Pole cest nebo proměnné typu rozsah `Edm.Boolean`. Například, pokud má logická pole s názvem indexu `IsEnabled` a chcete vrátit všechny dokumenty, kde je toto pole `true`, výrazu filtru může být jen název `IsEnabled`.
- Logické výrazy v závorkách. Pomocí závorek může pomoct explicitně určit pořadí operací ve filtru. Další informace o výchozí priority operátorů OData najdete v další části.

### <a name="operator-precedence-in-filters"></a>Priorita operátorů v filtry

Pokud napíšete výraz filtru chybí závorky kolem jeho dílčí výrazy, vyhodnotí ji Azure Search podle sady pravidel operátor. Tato pravidla jsou založeny na operátory, které umožňují kombinovat dílčí výrazy. V následující tabulce jsou uvedeny skupiny operátorů v pořadí od nejvyšší po nejnižší prioritu:

| Skupina | Operátory |
| --- | --- |
| Logické operátory | `not` |
| Operátory porovnání | `eq`, `ne`, `gt`, `lt`, `ge`, `le` |
| Logické operátory | `and` |
| Logické operátory | `or` |

Operátor, který je vyšší ve výše uvedené tabulky "naváže těsněji" pro jeho operandy než ostatní operátory. Například `and` má vyšší prioritu než `or`, a relační operátory mají vyšší prioritu než některou z nich, tak těchto dvou výrazů jsou ekvivalentní:

    Rating gt 0 and Rating lt 3 or Rating gt 7 and Rating lt 10
    ((Rating gt 0) and (Rating lt 3)) or ((Rating gt 7) and (Rating lt 10))

`not` Operátor má nejvyšší prioritu všech – dokonce i vyšší než operátory porovnání. To je důvod, proč, pokud se pokusíte o zápis filtru tímto způsobem:

    not Rating gt 5

Budete-li zobrazit tato chybová zpráva:

    Invalid expression: A unary operator with an incompatible type was detected. Found operand type 'Edm.Int32' for operator kind 'Not'.

K této chybě dochází, protože operátor, který je přidružený jenom `Rating` pole, která má typ `Edm.Int32`a nikoli s celou porovnání výrazu. Oprava, je vložit operand `not` v závorkách:

    not (Rating gt 5)

<a name="bkmk_limits"></a>

### <a name="filter-size-limitations"></a>Omezení velikosti filtru

Existují omezení velikosti a složitosti filtru výrazů, které můžete odeslat do služby Azure Search. Omezení jsou zhruba podle počtu klauzule ve výrazu filtru. Dobré vodítko, jak je, že pokud máte stovky klauzule, jste na rizika při překročení tohoto limitu. Doporučujeme, abyste návrhu vaší aplikace tak, aby negeneroval filtry neomezené velikosti.

> [!TIP]
> Pomocí [ `search.in` funkce](search-query-odata-search-in-function.md) místo dlouhé disjunctions rovnosti porovnání se dá vyhnout limit klauzule filtru, protože volání funkce se počítá jako jedna klauzule.

## <a name="examples"></a>Příklady

Najdete všechny hotels zvládne aspoň jeden základní sazba menší než 200 USD, který jsou hodnoceny dosahovalo nebo přesahovalo 4:

    $filter=Rooms/any(room: room/BaseRate lt 200.0) and Rating ge 4

Najdete hotels než "Moře zobrazení Motel", které mají byla renovovanou od 2010:

    $filter=HotelName ne 'Sea View Motel' and LastRenovationDate ge 2010-01-01T00:00:00Z

Najdete všechny hotely, které byly renovovanou 2010 nebo novější. Datum a čas literál obsahuje informace o časovém pásmu pro Tichomořský běžný čas:  

    $filter=LastRenovationDate ge 2010-01-01T00:00:00-08:00

Najdete všechny hotely, které mají parkovací zahrnuté a kde jsou všechny místnosti nekuřák:

    $filter=ParkingIncluded and Rooms/all(room: not room/SmokingAllowed)

 \- OR -  

    $filter=ParkingIncluded eq true and Rooms/all(room: room/SmokingAllowed eq false)

Najdete všechny hotely, které jsou luxusní nebo parkovací patří a mají hodnocení 5:  

    $filter=(Category eq 'Luxury' or ParkingIncluded eq true) and Rating eq 5

Najít všechny hotely se značkou "Wi-Fi" v místnosti alespoň jeden (ve kterém má každé místnosti značky, které jsou uložené v `Collection(Edm.String)` pole):  

    $filter=Rooms/any(room: room/Tags/any(tag: tag eq 'wifi'))

Najdete všechny hotely se všechny místnosti:  

    $filter=Rooms/any()

Najdete všechny hotely, které nemají místnosti:

    $filter=not Rooms/any()

Najít všechny hotely v rámci 10 kilometrů bodu daného odkazu (kde `Location` je pole s typem `Edm.GeographyPoint`):

    $filter=geo.distance(Location, geography'POINT(-122.131577 47.678581)') le 10

Najít všechny hotely v rámci daného zobrazení popsány mnohoúhelníku (kde `Location` je pole typu Edm.GeographyPoint). Mnohoúhelník musí být uzavřeny, což znamená, první a poslední bod sad musí být stejné. Navíc [body musí být uvedeny v pořadí proti směru hodinových ručiček](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

    $filter=geo.intersects(Location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

Najdete všechny hotels kde pole "Popisu" má hodnotu null. Pole bude mít hodnotu null, pokud nikdy byl nastaven, nebo explicitně nastavit na hodnotu null:  

    $filter=Description eq null

Najdete všechny hotels s názvem rovno "Moře zobrazení motel" nebo "Rozpočtu hotel"). Následující možnosti obsahovat mezery a místo je výchozím oddělovačem. Můžete zadat alternativní oddělovač v jednoduchých uvozovkách jako třetí parametr řetězce:  

    $filter=search.in(HotelName, 'Sea View motel,Budget hotel', ',')

Najít všechny hotels s názvem rovno "Moře zobrazení motel" nebo rozpočtu hotelu oddělené "|"):  

    $filter=search.in(HotelName, 'Sea View motel|Budget hotel', '|')

Najdete všechny hotely, ve kterém mají všechny místnosti značka 'Wi-Fi' nebo "tub":

    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub'))

Najdete shoda s frází v rámci kolekce, jako je například "vyhřívaný ručníků stojany" nebo "fén zahrnuté" značky.

    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))

Najdete dokumenty obsahující slovo "waterfront". Tento dotaz filtru je stejný jako [požadavek hledání](https://docs.microsoft.com/rest/api/searchservice/search-documents) s `search=waterfront`.

    $filter=search.ismatchscoring('waterfront')

Hledání dokumentů s slovo "hostel" a hodnocení větší nebo rovna 4 nebo dokumenty obsahující slovo "motel" a hodnocení roven hodnotě 5. Tento požadavek nelze vyjádřen bez `search.ismatchscoring` fungovat, protože kombinuje fulltextové vyhledávání se operace filtru pomocí `or`.

    $filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5

Hledání dokumentů bez slovo "luxusní".

    $filter=not search.ismatch('luxury')

Hledání dokumentů pomocí fráze "oceánu zobrazení" nebo hodnocení rovna 5. `search.ismatchscoring` Dotazu se provede pouze s poli `HotelName` a `Description`. Dokumenty, které odpovídají druhé klauzule disjunkce bude vrácen příliš – hotels s `Rating` roven hodnotě 5. Tyto dokumenty budou vráceny s skóre rovna hodnotě nula. Chcete-li ji vymazat, že neodpovídají žádné Vyhodnocená částí výrazu.

    $filter=search.ismatchscoring('"ocean view"', 'Description,HotelName') or Rating eq 5

Nalezení hotelů, kde jsou podmínky "hotel" a "letiště" více než pět slov od sebe v popisu a kde jsou všechny místnosti nekuřák. Tento dotaz používá [úplný jazyk dotazu Lucene](query-lucene-syntax.md).

    $filter=search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and not Rooms/any(room: room/SmokingAllowed)

## <a name="next-steps"></a>Další postup  

- [Filtry ve službě Azure Search](search-filters.md)
- [Přehled o výraz jazyka OData pro službu Azure Search](query-odata-filter-orderby-syntax.md)
- [Reference k syntaxi výrazů OData pro službu Azure Search](search-query-odata-syntax-reference.md)
- [Hledání dokumentů &#40;rozhraní REST API služby Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
