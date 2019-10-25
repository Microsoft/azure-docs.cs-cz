---
title: Odkaz na funkci fulltextového vyhledávání OData
titleSuffix: Azure Cognitive Search
description: Funkce fulltextového vyhledávání OData Search. POZVYHLEDAT a Search. ismatchscoring v dotazech Azure Kognitivní hledání.
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
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793354"
---
# <a name="odata-full-text-search-functions-in-azure-cognitive-search---searchismatch-and-searchismatchscoring"></a>Funkce fulltextového vyhledávání OData v Azure Kognitivní hledání-`search.ismatch` a `search.ismatchscoring`

Azure Kognitivní hledání podporuje fulltextové vyhledávání v kontextu [výrazů filtru OData](query-odata-filter-orderby-syntax.md) prostřednictvím funkcí `search.ismatch` a `search.ismatchscoring`. Tyto funkce umožňují kombinovat fulltextové vyhledávání s přísným filtrováním způsobem, který není možné pouze pomocí parametru `search` nejvyšší úrovně [rozhraní API pro hledání](https://docs.microsoft.com/rest/api/searchservice/search-documents).

> [!NOTE]
> Funkce `search.ismatch` a `search.ismatchscoring` jsou podporovány pouze ve filtrech [rozhraní API pro hledání](https://docs.microsoft.com/rest/api/searchservice/search-documents). Nejsou podporovány v rozhraních API pro [navrhování](https://docs.microsoft.com/rest/api/searchservice/suggestions) nebo [Automatické dokončování](https://docs.microsoft.com/rest/api/searchservice/autocomplete) .

## <a name="syntax"></a>Syntaxe

Následující EBNF ([rozšířený formulář Backus-Naur](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definuje gramatiku funkcí `search.ismatch` a `search.ismatchscoring`:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_is_match_call ::=
    'search.ismatch'('scoring')?'(' search_is_match_parameters ')'

search_is_match_parameters ::=
    string_literal(',' string_literal(',' query_type ',' search_mode)?)?

query_type ::= "'full'" | "'simple'"

search_mode ::= "'any'" | "'all'"
```

K dispozici je také diagram interaktivní syntaxe:

> [!div class="nextstepaction"]
> [Diagram syntaxe OData pro Azure Kognitivní hledání](https://azuresearch.github.io/odata-syntax-diagram/#search_is_match_call)

> [!NOTE]
> Kompletní EBNF najdete v článku [referenční informace k syntaxi výrazu OData pro Azure kognitivní hledání](search-query-odata-syntax-reference.md) .

### <a name="searchismatch"></a>Hledat. neshoda

Funkce `search.ismatch` vyhodnocuje fulltextový vyhledávací dotaz jako součást výrazu filtru. V sadě výsledků se vrátí dokumenty, které odpovídají hledanému dotazu. K dispozici jsou následující přetížení této funkce:

- `search.ismatch(search)`
- `search.ismatch(search, searchFields)`
- `search.ismatch(search, searchFields, queryType, searchMode)`

Parametry jsou definovány v následující tabulce:

| Název parametru | Typ | Popis |
| --- | --- | --- |
| `search` | `Edm.String` | Vyhledávací dotaz (buď v [jednoduché](query-simple-syntax.md) nebo [úplné](query-lucene-syntax.md) syntaxi dotazů Lucene). |
| `searchFields` | `Edm.String` | Čárkami oddělený seznam vyhledávacích polí, ve kterých chcete hledat; Výchozí hodnota pro všechna hledaná pole v indexu. Při použití [vyhledávacího pole](query-lucene-syntax.md#bkmk_fields) v parametru `search` přepíše specifikátory pole v dotazu Lucene všechna pole zadaná v tomto parametru. |
| `queryType` | `Edm.String` | `'simple'` nebo `'full'`; Výchozí hodnota je `'simple'`. Určuje, který dotazovací jazyk byl použit v parametru `search`. |
| `searchMode` | `Edm.String` | `'any'` nebo `'all'`, výchozí hodnota je `'any'`. Určuje, zda musí být některý nebo všechny hledané výrazy v parametru `search` porovnány, aby bylo možné dokument počítat jako shodu. Při použití [logických operátorů Lucene](query-lucene-syntax.md#bkmk_boolean) v parametru `search` bude mít přednost před tímto parametrem. |

Všechny výše uvedené parametry jsou ekvivalentem odpovídajících [parametrů vyhledávacího požadavku v rozhraní API pro hledání](https://docs.microsoft.com/rest/api/searchservice/search-documents).

Funkce `search.ismatch` vrací hodnotu typu `Edm.Boolean`, která umožňuje její vytvoření pomocí dalších podvýrazů filtru pomocí logických [logických operátorů](search-query-odata-logical-operators.md).

> [!NOTE]
> Azure Kognitivní hledání nepodporuje použití `search.ismatch` nebo `search.ismatchscoring` uvnitř výrazů lambda. To znamená, že není možné zapisovat filtry pro kolekce objektů, které mohou korelovat shody fulltextového vyhledávání s striktními shodami filtru u stejného objektu. Další podrobnosti o tomto omezení a příklady najdete v tématu [řešení potíží s filtry kolekcí v Azure kognitivní hledání](search-query-troubleshoot-collection-filters.md). Podrobné informace o tom, proč toto omezení existuje, najdete v tématu [Principy filtrů kolekcí v Azure kognitivní hledání](search-query-understand-collection-filters.md).


### <a name="searchismatchscoring"></a>Hledat. ismatchscoring

Funkce `search.ismatchscoring`, jako je funkce `search.ismatch`, vrátí `true` pro dokumenty, které odpovídají fulltextovém vyhledávacímu dotazu předanému jako parametr. Rozdíl mezi nimi spočívá v tom, že skóre významnosti dokumentů odpovídající `search.ismatchscoring`m dotazu bude přispívat k celkovému skóre dokumentu, zatímco v případě `search.ismatch`se skóre dokumentu nemění. Následující přetížení této funkce jsou k dispozici s parametry stejnými jako u `search.ismatch`:

- `search.ismatchscoring(search)`
- `search.ismatchscoring(search, searchFields)`
- `search.ismatchscoring(search, searchFields, queryType, searchMode)`

Ve stejném výrazu filtru lze použít jak funkce `search.ismatch`, tak `search.ismatchscoring`.

## <a name="examples"></a>Příklady

Vyhledá dokumenty ve slově "Waterfront". Tento dotaz filtru je stejný jako [požadavek hledání](https://docs.microsoft.com/rest/api/searchservice/search-documents) s `search=waterfront`.

    search.ismatchscoring('waterfront')

Najde dokumenty se slovem "Hostel" a hodnocením větším nebo rovným 4 nebo dokumenty se slovem "Motel" a hodnocením rovným 5. Poznámka: Tento požadavek nebylo možné vyjádřit bez funkce `search.ismatchscoring`.

    search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5

Najde dokumenty bez slova "luxus".

    not search.ismatch('luxury')

Vyhledá dokumenty se frází "zobrazení v oceánu" nebo hodnocení rovno 5. Dotaz `search.ismatchscoring` bude proveden pouze proti polím `HotelName` a `Rooms/Description`.

Dokumenty, které odpovídají pouze druhé klauzuli disjunkce, budou vráceny příliš-Hotely s `Rating` rovny 5. Aby bylo jasné, že se tyto dokumenty neshodují s žádnou z částí výrazu, budou vráceny výsledek se stejnou hodnotou jako nula.

    search.ismatchscoring('"ocean view"', 'Rooms/Description,HotelName') or Rating eq 5

Vyhledá dokumenty, kde jsou termíny "Hotel" a "letiště" mezi 5 slovy od sebe navzájem v popisu hotelu a tam, kde není povolený kouření aspoň na některých místnostech. Tento dotaz používá [celý dotazovací jazyk Lucene](query-lucene-syntax.md).

    search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and Rooms/any(room: not room/SmokingAllowed)

## <a name="next-steps"></a>Další kroky  

- [Filtry v Azure Kognitivní hledání](search-filters.md)
- [Přehled jazyka výrazů OData pro Azure Kognitivní hledání](query-odata-filter-orderby-syntax.md)
- [Referenční dokumentace syntaxe výrazu OData pro Azure Kognitivní hledání](search-query-odata-syntax-reference.md)
- [Hledání dokumentů &#40;Azure kognitivní hledání REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
