---
title: Odkaz na fulltextové vyhledávací funkce OData
titleSuffix: Azure Cognitive Search
description: OData fulltextové vyhledávací funkce, search.ismatch a search.ismatchscoring, v dotazech Azure Cognitive Search.
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
ms.openlocfilehash: 06eb29f2f3245d3f4fd047fb86b2b57fb1f0989e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72793354"
---
# <a name="odata-full-text-search-functions-in-azure-cognitive-search---searchismatch-and-searchismatchscoring"></a>OData fulltextové vyhledávání funkce v `search.ismatch` Azure Cognitive Search - a`search.ismatchscoring`

Azure Cognitive Search podporuje fulltextové vyhledávání v kontextu [výrazů filtru OData](query-odata-filter-orderby-syntax.md) prostřednictvím funkcí `search.ismatch` a. `search.ismatchscoring` Tyto funkce umožňují kombinovat fulltextové vyhledávání s přísným logickým filtrováním způsobem, který `search` není možný pouze pomocí parametru nejvyšší úrovně [rozhraní SEARCH API](https://docs.microsoft.com/rest/api/searchservice/search-documents).

> [!NOTE]
> Funkce `search.ismatch` `search.ismatchscoring` a jsou podporovány pouze ve filtrech v [rozhraní SEARCH API](https://docs.microsoft.com/rest/api/searchservice/search-documents). Nejsou podporovány v [návrhu](https://docs.microsoft.com/rest/api/searchservice/suggestions) nebo [automatické dokončování](https://docs.microsoft.com/rest/api/searchservice/autocomplete) API.

## <a name="syntax"></a>Syntaxe

Následující EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) `search.ismatch` definuje `search.ismatchscoring` gramatiku a funkce:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_is_match_call ::=
    'search.ismatch'('scoring')?'(' search_is_match_parameters ')'

search_is_match_parameters ::=
    string_literal(',' string_literal(',' query_type ',' search_mode)?)?

query_type ::= "'full'" | "'simple'"

search_mode ::= "'any'" | "'all'"
```

K dispozici je také interaktivní syntaktický diagram:

> [!div class="nextstepaction"]
> [Syntaxový diagram OData pro Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#search_is_match_call)

> [!NOTE]
> Viz [odkaz na syntaxi výrazu OData pro Azure Cognitive Search](search-query-odata-syntax-reference.md) pro kompletní EBNF.

### <a name="searchismatch"></a>hledat.ismatch

Funkce `search.ismatch` vyhodnotí fulltextový vyhledávací dotaz jako součást výrazu filtru. Dokumenty, které odpovídají vyhledávacímu dotazu, budou vráceny v sadě výsledků. K dispozici jsou následující přetížení této funkce:

- `search.ismatch(search)`
- `search.ismatch(search, searchFields)`
- `search.ismatch(search, searchFields, queryType, searchMode)`

Parametry jsou definovány v následující tabulce:

| Název parametru | Typ | Popis |
| --- | --- | --- |
| `search` | `Edm.String` | Vyhledávací dotaz (v [jednoduché](query-simple-syntax.md) nebo [úplné](query-lucene-syntax.md) syntaxi lucene dotazu). |
| `searchFields` | `Edm.String` | Seznam prohledávatelných polí oddělených čárkami, ve kterých se má vyhledávat; výchozí pro všechna prohledávatelná pole v indexu. Při použití [fielded](query-lucene-syntax.md#bkmk_fields) `search` search v parametru, specifikátory polí v lucene dotazu přepsat všechna pole zadaná v tomto parametru. |
| `queryType` | `Edm.String` | `'simple'`nebo `'full'`; výchozí hodnota `'simple'`je . Určuje, jaký dotazovací jazyk `search` byl v parametru použit. |
| `searchMode` | `Edm.String` | `'any'`nebo `'all'`, výchozí `'any'`hodnota je . Označuje, zda některé nebo všechny `search` hledané termíny v parametru musí odpovídat, aby bylo možné spočítat dokument jako shodu. Při použití [Lucene boolean operátory](query-lucene-syntax.md#bkmk_boolean) v parametru, `search` budou mít přednost před tímto parametrem. |

Všechny výše uvedené parametry jsou ekvivalentní odpovídajícím [parametrům požadavku vyhledávání v rozhraní SEARCH API](https://docs.microsoft.com/rest/api/searchservice/search-documents).

Funkce `search.ismatch` vrátí hodnotu `Edm.Boolean`typu , která umožňuje sestavit ji s jinými dílčími výrazy filtru pomocí logických [operátorů](search-query-odata-logical-operators.md)Boolean .

> [!NOTE]
> Azure Cognitive Search nepodporuje použití `search.ismatch` nebo `search.ismatchscoring` uvnitř lambda výrazy. To znamená, že není možné psát filtry přes kolekce objektů, které mohou korelovat fulltextové hledání shody s přísné shody filtru na stejný objekt. Další podrobnosti o tomto omezení, stejně jako příklady, [najdete v tématu řešení potíží s filtry kolekce v Azure Cognitive Search](search-query-troubleshoot-collection-filters.md). Podrobnější informace o tom, proč toto omezení existuje, najdete [v tématu Principy filtrů kolekce v Azure Cognitive Search](search-query-understand-collection-filters.md).


### <a name="searchismatchscoring"></a>search.ismatchscoring

Funkce, `search.ismatchscoring` stejně `search.ismatch` jako `true` funkce, vrátí pro dokumenty, které odpovídají fulltextovévyhledávací dotaz předán jako parametr. Rozdíl mezi nimi spoří v tom, `search.ismatchscoring` že skóre relevance dokumentů odpovídajících dotazu přispěje k celkovému skóre dokumentu, zatímco v případě `search.ismatch`, skóre dokumentu se nezmění. Následující přetížení této funkce jsou k dispozici s `search.ismatch`parametry shodnými s parametry :

- `search.ismatchscoring(search)`
- `search.ismatchscoring(search, searchFields)`
- `search.ismatchscoring(search, searchFields, queryType, searchMode)`

Funkce `search.ismatch` a `search.ismatchscoring` lze použít ve stejném výrazu filtru.

## <a name="examples"></a>Příklady

Najít dokumenty se slovem "nábřeží". Tento dotaz filtru je shodný `search=waterfront`s [požadavkem](https://docs.microsoft.com/rest/api/searchservice/search-documents) na hledání s .

    search.ismatchscoring('waterfront')

Najít dokumenty se slovem "hostel" a hodnocení větší nebo rovno 4, nebo dokumenty se slovem "motel" a hodnocení se rovná 5. Všimněte si, že tento požadavek nelze vyjádřit bez `search.ismatchscoring` funkce.

    search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5

Najít dokumenty bez slova "luxus".

    not search.ismatch('luxury')

Najděte dokumenty s výrazem "výhled na oceán" nebo hodnocením rovným 5. Dotaz `search.ismatchscoring` bude proveden pouze proti `HotelName` `Rooms/Description`polím a .

Budou vráceny také dokumenty, které odpovídají pouze druhé klauzuli `Rating` disjunkce - hotely se rovnající se 5. Aby bylo jasné, že tyto dokumenty neodpovídají žádné z bodované části výrazu, budou vráceny se skóre rovnou nule.

    search.ismatchscoring('"ocean view"', 'Rooms/Description,HotelName') or Rating eq 5

Najít dokumenty, kde termíny "hotel" a "letiště" jsou do 5 slov od sebe v popisu hotelu, a kde kouření není povoleno alespoň v některých pokojích. Tento dotaz používá [úplný dotazový jazyk Lucene](query-lucene-syntax.md).

    search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and Rooms/any(room: not room/SmokingAllowed)

## <a name="next-steps"></a>Další kroky  

- [Filtry v Azure Cognitive Search](search-filters.md)
- [Přehled jazyka OData pro Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Odkaz na syntaxi výrazu OData pro Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [Hledání dokumentů &#40;azure kognitivní vyhledávání rozhraní API rozhraní&#41;API](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
