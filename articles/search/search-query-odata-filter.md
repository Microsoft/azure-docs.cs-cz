---
title: Odkaz na filtr OData
titleSuffix: Azure Cognitive Search
description: Odkaz na jazyk OData a úplná syntaxe používaná pro vytváření výrazů filtru v dotazech Azure Cognitive Search.
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
ms.openlocfilehash: b966e9cfa3ef40666dbbd62135f8f964e5eb2023
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282884"
---
# <a name="odata-filter-syntax-in-azure-cognitive-search"></a>Syntaxe $filter OData v Azure Cognitive Search

Azure Cognitive Search používá [výrazy filtru OData](query-odata-filter-orderby-syntax.md) k použití dalších kritérií pro vyhledávací dotaz kromě fulltextových hledaných výrazů. Tento článek podrobně popisuje syntaxi filtrů. Obecnější informace o tom, co jsou filtry a jak je použít k realizaci konkrétních scénářů dotazů, najdete [v tématu Filtry v Azure Cognitive Search](search-filters.md).

## <a name="syntax"></a>Syntaxe

Filtr v jazyce OData je logický výraz, který může být jedním z několika typů výrazů, jak ukazuje následující formulář EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)):

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

K dispozici je také interaktivní syntaktický diagram:

> [!div class="nextstepaction"]
> [Syntaxový diagram OData pro Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#boolean_expression)

> [!NOTE]
> Viz [odkaz na syntaxi výrazu OData pro Azure Cognitive Search](search-query-odata-syntax-reference.md) pro kompletní EBNF.

Mezi typy logických výrazů patří:

- Kolekce výrazy `any` filtru `all`pomocí nebo . Ty platí kritéria filtru pro sběrná pole. Další informace najdete v tématu [Operátory kolekce OData v Azure Cognitive Search](search-query-odata-collection-operators.md).
- Logické výrazy, které kombinují jiné `and`logické `or`výrazy pomocí operátorů , a `not`. Další informace najdete v tématu [Logické operátory OData v Azure Cognitive Search](search-query-odata-logical-operators.md).
- Srovnávací výrazy, které porovnávají pole nebo proměnné `eq`rozsahu `ne` `gt`s `lt` `ge`konstantními `le`hodnotami pomocí operátorů , , , a . Další informace najdete v tématu [Operátory porovnání OData v Azure Cognitive Search](search-query-odata-comparison-operators.md). Srovnávací výrazy se také používají k porovnání vzdáleností `geo.distance` mezi geoprostorovými souřadnicemi pomocí funkce. Další informace najdete v tématu [OData geoprostorové funkce v Azure Cognitive Search](search-query-odata-geo-spatial-functions.md).
- Logické literály `true` a `false`. Tyto konstanty mohou být užitečné někdy při programově generování filtrů, ale jinak nemají tendenci být používány v praxi.
- Volání logických funkcí, včetně:
  - `geo.intersects`, který testuje, zda je daný bod v daném polygu. Další informace najdete v tématu [OData geoprostorové funkce v Azure Cognitive Search](search-query-odata-geo-spatial-functions.md).
  - `search.in`, který porovnává proměnnou pole nebo rozsahu s každou hodnotou v seznamu hodnot. Další informace najdete v tématu [Funkce OData `search.in` v Azure Cognitive Search](search-query-odata-search-in-function.md).
  - `search.ismatch`a `search.ismatchscoring`které provádějí fulltextové vyhledávací operace v kontextu filtru. Další informace najdete v tématu [OData fulltextové vyhledávací funkce v Azure Cognitive Search](search-query-odata-full-text-search-functions.md).
- Cesty polí nebo proměnné rozsahu `Edm.Boolean`typu . Pokud má například index volané `IsEnabled` logické pole a chcete vrátit všechny `true`doklady tam, kde `IsEnabled`je toto pole , může být výrazem filtru pouze název .
- Logické výrazy v závorcích. Použití závorek může pomoci explicitně určit pořadí operací ve filtru. Další informace o výchozí prioritě operátorů OData naleznete v další části.

### <a name="operator-precedence-in-filters"></a>Priorita operátora ve filtrech

Pokud napíšete výraz filtru bez závorek kolem jeho dílčí výrazy, Azure Cognitive Search vyhodnotí podle sady pravidel priority operátora. Tato pravidla jsou založena na operátory, které se používají ke kombinování podvýrazů. V následující tabulce jsou uvedeny skupiny operátorů v pořadí od nejvyšší po nejnižší prioritu:

| Skupina | Operátor (provozovatelé) |
| --- | --- |
| Logické operátory | `not` |
| Operátory porovnání | `eq`, `ne`, `gt`, `lt`, `ge`, `le` |
| Logické operátory | `and` |
| Logické operátory | `or` |

Operátor, který je vyšší ve výše uvedené tabulce bude "vázat těsněji" na jeho operandy než jiné operátory. Například `and` má vyšší prioritu `or`než , a operátory porovnání mají vyšší prioritu než jeden z nich, takže následující dva výrazy jsou ekvivalentní:

    Rating gt 0 and Rating lt 3 or Rating gt 7 and Rating lt 10
    ((Rating gt 0) and (Rating lt 3)) or ((Rating gt 7) and (Rating lt 10))

Operátor `not` má nejvyšší prioritu ze všech – ještě vyšší než operátory porovnání. To je důvod, proč, pokud se pokusíte napsat filtr, jako je tento:

    not Rating gt 5

Zobrazí se tato chybová zpráva:

    Invalid expression: A unary operator with an incompatible type was detected. Found operand type 'Edm.Int32' for operator kind 'Not'.

K této chybě dochází, protože `Rating` operátor je přidružen `Edm.Int32`pouze k poli, které je typu a nikoli k celému vyjádření porovnání. Oprava je umístit operand `not` do závorek:

    not (Rating gt 5)

<a name="bkmk_limits"></a>

### <a name="filter-size-limitations"></a>Omezení velikosti filtru

Existují omezení velikosti a složitosti výrazů filtru, které můžete odeslat do Azure Cognitive Search. Omezení jsou založeny zhruba na počtu klauzulí ve výrazu filtru. Dobrým vodítkem je, že pokud máte stovky klauzulí, jste v nebezpečí překročení limitu. Doporučujeme navrhnout aplikaci tak, aby negenerovala filtry neomezené velikosti.

> [!TIP]
> Použití [ `search.in` funkce](search-query-odata-search-in-function.md) namísto dlouhých disjunkcí porovnání rovnosti může pomoci vyhnout se limitu klauzule filtru, protože volání funkce se počítá jako jedna klauzule.

## <a name="examples"></a>Příklady

Vyhledejte všechny hotely s alespoň jedním pokojem se základní sazbou nižší než 200 USD, které jsou hodnoceny na 4 nebo vyšší:

    $filter=Rooms/any(room: room/BaseRate lt 200.0) and Rating ge 4

Najděte všechny hotely kromě motelu "Sea View Motel", které byly renovovány od roku 2010:

    $filter=HotelName ne 'Sea View Motel' and LastRenovationDate ge 2010-01-01T00:00:00Z

Najít všechny hotely, které byly renovovány v roce 2010 nebo později. Literál datetime obsahuje informace o časovém pásmu pro tichomořský standardní čas:  

    $filter=LastRenovationDate ge 2010-01-01T00:00:00-08:00

Najděte všechny hotely, které mají parkování v ceně a kde jsou všechny pokoje nekuřácké:

    $filter=ParkingIncluded and Rooms/all(room: not room/SmokingAllowed)

 \-NEBO -  

    $filter=ParkingIncluded eq true and Rooms/all(room: room/SmokingAllowed eq false)

Najít všechny hotely, které jsou luxusní nebo zahrnují parkování a mají hodnocení 5:  

    $filter=(Category eq 'Luxury' or ParkingIncluded eq true) and Rating eq 5

Najít všechny hotely s označením "wifi" alespoň v jedné místnosti `Collection(Edm.String)` (kde každá místnost má značky uložené v poli):  

    $filter=Rooms/any(room: room/Tags/any(tag: tag eq 'wifi'))

Najít všechny hotely s libovolnými pokoji:  

    $filter=Rooms/any()

Najít všechny hotely, které nemají pokoje:

    $filter=not Rooms/any()

Najděte všechny hotely do vzdálenosti 10 `Location` km od `Edm.GeographyPoint`daného referenčního bodu (kde je pole typu ):

    $filter=geo.distance(Location, geography'POINT(-122.131577 47.678581)') le 10

Najděte všechny hotely v rámci daného výřezu `Location` popsaného jako polygon (kde je pole typu Edm.GeographyPoint). Polygon musí být uzavřen, což znamená, že první a poslední sady bodů musí být stejné. Body [musí být také uvedeny proti směru hodinových ručiček](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

    $filter=geo.intersects(Location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

Najít všechny hotely, kde je "Popis" pole null. Toto pole bude null, pokud nebylo nikdy nastaveno nebo pokud bylo explicitně nastaveno na hodnotu null:  

    $filter=Description eq null

Najděte všechny hotely s názvem rovným "Motel se sea View" nebo "Levný hotel"). Tyto fráze obsahují mezery a mezera je výchozí oddělovač. Jako třetí parametr řetězce můžete zadat alternativní oddělovač v jednoduchých uvozovkách:  

    $filter=search.in(HotelName, 'Sea View motel,Budget hotel', ',')

Najděte všechny hotely s názvem rovným "Motel u Sea View" nebo "Levný hotel" odděleným "|"):  

    $filter=search.in(HotelName, 'Sea View motel|Budget hotel', '|')

Najděte všechny hotely, kde všechny pokoje mají značku "wifi" nebo "vana":

    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub'))

Ve své kolekci najdete shodu s frázemi, jako jsou "vyhřívané věšáky na ručníky" nebo "fén".

    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))

Najít dokumenty se slovem "nábřeží". Tento dotaz filtru je shodný `search=waterfront`s [požadavkem](https://docs.microsoft.com/rest/api/searchservice/search-documents) na hledání s .

    $filter=search.ismatchscoring('waterfront')

Najít dokumenty se slovem "hostel" a hodnocení větší nebo rovno 4, nebo dokumenty se slovem "motel" a hodnocení se rovná 5. Tento požadavek nelze vyjádřit bez `search.ismatchscoring` funkce, protože kombinuje fulltextové vyhledávání `or`s operacemi filtru pomocí .

    $filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5

Najít dokumenty bez slova "luxus".

    $filter=not search.ismatch('luxury')

Najděte dokumenty s výrazem "výhled na oceán" nebo hodnocením rovným 5. Dotaz `search.ismatchscoring` bude proveden pouze proti `HotelName` `Description`polím a . Budou vráceny také dokumenty, které odpovídají pouze druhé klauzuli `Rating` disjunkce - hotely se rovnající se 5. Tyto dokumenty budou vráceny se skóre rovnou nule, aby bylo jasné, že se neshodují s žádnou z bodované části výrazu.

    $filter=search.ismatchscoring('"ocean view"', 'Description,HotelName') or Rating eq 5

Najít hotely, kde termíny "hotel" a "letiště" nejsou více než pět slov od sebe v popisu, a kde jsou všechny pokoje nekuřácké. Tento dotaz používá [úplný dotazový jazyk Lucene](query-lucene-syntax.md).

    $filter=search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and not Rooms/any(room: room/SmokingAllowed)

## <a name="next-steps"></a>Další kroky  

- [Filtry v Azure Cognitive Search](search-filters.md)
- [Přehled jazyka OData pro Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Odkaz na syntaxi výrazu OData pro Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [Hledání dokumentů &#40;azure kognitivní vyhledávání rozhraní API rozhraní&#41;API](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
