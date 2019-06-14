---
title: Fulltextové vyhledávání OData – reference funkce – Azure Search
description: Funkce fulltextového vyhledávání protokolu OData, search.ismatch a search.ismatchscoring v dotazech Azure Search.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: brjohnstmsft
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
ms.openlocfilehash: 158312a7afe88e7b9885376c5d28b01958acbbfb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079805"
---
# <a name="odata-full-text-search-functions-in-azure-search---searchismatch-and-searchismatchscoring"></a>OData funkce fulltextového vyhledávání ve službě Azure Search - `search.ismatch` a `search.ismatchscoring`

Služba Azure Search podporuje fulltextové vyhledávání v rámci [výrazů filtru OData](query-odata-filter-orderby-syntax.md) prostřednictvím `search.ismatch` a `search.ismatchscoring` funkce. Tyto funkce umožňují vyhledávání v kombinaci s striktní logická filtrování takovým způsobem, který stačí použít na nejvyšší úrovni nejsou možné `search` parametr [rozhraní API pro vyhledávání](https://docs.microsoft.com/rest/api/searchservice/search-documents).

> [!NOTE]
> `search.ismatch` a `search.ismatchscoring` funkce jsou podporovány pouze ve filtrech v [rozhraní API pro vyhledávání](https://docs.microsoft.com/rest/api/searchservice/search-documents). Nejsou podporovány v [navrhnout](https://docs.microsoft.com/rest/api/searchservice/suggestions) nebo [automatické dokončování](https://docs.microsoft.com/rest/api/searchservice/autocomplete) rozhraní API.

## <a name="syntax"></a>Syntaxe

Následující EBNF ([Extended Backus-Naur – formulář](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definuje gramatiku `search.ismatch` a `search.ismatchscoring` funkce:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_is_match_call ::=
    'search.ismatch'('scoring')?'(' search_is_match_parameters ')'

search_is_match_parameters ::=
    string_literal(',' string_literal(',' query_type ',' search_mode)?)?

query_type ::= "'full'" | "'simple'"

search_mode ::= "'any'" | "'all'"
```

Diagramu interaktivní syntaxe je také k dispozici:

> [!div class="nextstepaction"]
> [Diagram syntaxe OData pro službu Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#search_is_match_call)

> [!NOTE]
> Zobrazit [referenční příručka syntaxe výrazů OData pro službu Azure Search](search-query-odata-syntax-reference.md) pro dokončení EBNF.

### <a name="searchismatch"></a>search.ismatch

`search.ismatch` Funkce vyhodnocuje fulltextového vyhledávací dotaz jako součást výrazu filtru. V sadě výsledků se vrátí dokumenty, které odpovídají vyhledávacímu dotazu. K dispozici jsou následující přetížení této funkce:

- `search.ismatch(search)`
- `search.ismatch(search, searchFields)`
- `search.ismatch(search, searchFields, queryType, searchMode)`

Parametry jsou definovány v následující tabulce:

| Název parametru | Type | Popis |
| --- | --- | --- |
| `search` | `Edm.String` | Vyhledávací dotaz (buď [jednoduché](query-simple-syntax.md) nebo [úplné](query-lucene-syntax.md) syntaxe dotazů Lucene). |
| `searchFields` | `Edm.String` | Čárkami oddělený seznam prohledávatelná pole pro hledání v; Výchozí hodnota je všechna prohledatelná pole v indexu. Při použití [fielded hledání](query-lucene-syntax.md#bkmk_fields) v `search` parametr, pole specifikátorů v dotazu Lucene přepsat všech polí zadat v tomto parametru. |
| `queryType` | `Edm.String` | `'simple'` nebo `'full'`; výchozí hodnota je `'simple'`. Určuje, jaký jazyk dotazu byla použita v `search` parametru. |
| `searchMode` | `Edm.String` | `'any'` nebo `'all'`, výchozí hodnota je `'any'`. Určuje, zda podmínky v některých nebo všech hledání `search` parametru musí odpovídat k sečtení dokument jako shoda. Při použití [operátory pro datový typ Lucene Boolean](query-lucene-syntax.md#bkmk_boolean) v `search` parametr, se bude mít přednost nad tento parametr. |

Všechny výše uvedené parametry jsou ekvivalentní odpovídající [vyhledávat parametry požadavku rozhraní API pro vyhledávání](https://docs.microsoft.com/rest/api/searchservice/search-documents).

`search.ismatch` Funkce vrátí hodnotu typu `Edm.Boolean`, která umožňuje compose s jinými dílčí výrazy filtru pomocí logickou hodnotu [logické operátory](search-query-odata-logical-operators.md).

> [!NOTE]
> Služba Azure Search nepodporuje použití `search.ismatch` nebo `search.ismatchscoring` uvnitř výrazů lambda. To znamená, že není možné filtrů zápisu přes kolekce objektů, které můžete korelovat fulltextové vyhledávání shody s striktní filtru odpovídá na stejný objekt. Podrobné informace o tomto omezení, jakož i příklady najdete v článku [řešení potíží s filtry kolekcí ve službě Azure Search](search-query-troubleshoot-collection-filters.md). Další podrobné informace o důvod, proč se toto omezení existuje, naleznete v tématu [Principy kolekci filtrů ve službě Azure Search](search-query-understand-collection-filters.md).


### <a name="searchismatchscoring"></a>search.ismatchscoring

`search.ismatchscoring` Funkce, jako je třeba `search.ismatch` vrací funkce `true` pro dokumenty, které odpovídají dotazu fulltextové vyhledávání předaného jako parametr. Rozdíl mezi nimi je, že skóre relevance odpovídajících dokumentů `search.ismatchscoring` dotazu přispěje k celkové skóre dokument, zatímco v případě `search.ismatch`, skóre dokumentu se nezmění. Následující přetížení této funkce je k dispozici s parametry, které jsou stejné jako `search.ismatch`:

- `search.ismatchscoring(search)`
- `search.ismatchscoring(search, searchFields)`
- `search.ismatchscoring(search, searchFields, queryType, searchMode)`

Jak `search.ismatch` a `search.ismatchscoring` funkce můžete používat ve stejném výrazu filtru.

## <a name="examples"></a>Příklady

Najdete dokumenty obsahující slovo "waterfront". Tento dotaz filtru je stejný jako [požadavek hledání](https://docs.microsoft.com/rest/api/searchservice/search-documents) s `search=waterfront`.

    search.ismatchscoring('waterfront')

Hledání dokumentů s slovo "hostel" a hodnocení větší nebo rovna 4 nebo dokumenty obsahující slovo "motel" a hodnocení roven hodnotě 5. Všimněte si, že tuto žádost nelze vyjádřen bez `search.ismatchscoring` funkce.

    search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5

Hledání dokumentů bez slovo "luxusní".

    not search.ismatch('luxury')

Hledání dokumentů pomocí fráze "oceánu zobrazení" nebo hodnocení rovna 5. `search.ismatchscoring` Dotazu se provede pouze s poli `HotelName` a `Rooms/Description`.

Dokumenty, které odpovídají druhé klauzule disjunkce bude vrácen příliš – hotels s `Rating` roven hodnotě 5. Chcete-li ji vymazat, že tyto dokumenty neměli shodují s některým z Vyhodnocená části výrazu, vrátí se skóre rovna hodnotě nula.

    search.ismatchscoring('"ocean view"', 'Rooms/Description,HotelName') or Rating eq 5

Hledání dokumentů, kde jsou podmínky "hotel" a "letiště" v rámci 5 slov od sebe navzájem v popisu hotelu a kde kouření není povoleno v alespoň některé místností. Tento dotaz používá [úplný jazyk dotazu Lucene](query-lucene-syntax.md).

    search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and Rooms/any(room: not room/SmokingAllowed)

## <a name="next-steps"></a>Další postup  

- [Filtry ve službě Azure Search](search-filters.md)
- [Přehled o výraz jazyka OData pro službu Azure Search](query-odata-filter-orderby-syntax.md)
- [Reference k syntaxi výrazů OData pro službu Azure Search](search-query-odata-syntax-reference.md)
- [Hledání dokumentů &#40;rozhraní REST API služby Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
