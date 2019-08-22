---
title: Referenční informace o funkcích fulltextového vyhledávání OData – Azure Search
description: Funkce fulltextového vyhledávání OData Search. POZVYHLEDAT a Search. ismatchscoring v Azure Searchch dotazech.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: brjohnstmsft
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
ms.openlocfilehash: c3b28c8799b09ddfe008df8539709c5a704ac6b4
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648014"
---
# <a name="odata-full-text-search-functions-in-azure-search---searchismatch-and-searchismatchscoring"></a>Funkce fulltextového vyhledávání OData v Azure Search – `search.ismatch` a`search.ismatchscoring`

Azure Search podporuje fulltextové vyhledávání v kontextu [výrazů filtru OData](query-odata-filter-orderby-syntax.md) prostřednictvím `search.ismatch` funkcí a `search.ismatchscoring` . Tyto funkce umožňují kombinovat fulltextové vyhledávání s přísným filtrováním, které není možné pouze pomocí parametru nejvyšší úrovně `search` [rozhraní API pro hledání](https://docs.microsoft.com/rest/api/searchservice/search-documents).

> [!NOTE]
> Funkce `search.ismatch` a`search.ismatchscoring` jsou podporovány pouze ve filtrech v [rozhraní API pro hledání](https://docs.microsoft.com/rest/api/searchservice/search-documents). Nejsou podporovány v rozhraních API pro [navrhování](https://docs.microsoft.com/rest/api/searchservice/suggestions) nebo [Automatické dokončování](https://docs.microsoft.com/rest/api/searchservice/autocomplete) .

## <a name="syntax"></a>Syntaxe

Následující EBNF ([rozšířený formulář Backus-Naur](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definuje gramatiku `search.ismatch` funkcí a: `search.ismatchscoring`

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
> [Diagram syntaxe OData pro Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#search_is_match_call)

> [!NOTE]
> Kompletní EBNF najdete v referenčních informacích k [syntaxi výrazu OData pro Azure Search](search-query-odata-syntax-reference.md) .

### <a name="searchismatch"></a>Hledat. neshoda

`search.ismatch` Funkce vyhodnocuje fulltextový vyhledávací dotaz jako součást výrazu filtru. V sadě výsledků se vrátí dokumenty, které odpovídají hledanému dotazu. K dispozici jsou následující přetížení této funkce:

- `search.ismatch(search)`
- `search.ismatch(search, searchFields)`
- `search.ismatch(search, searchFields, queryType, searchMode)`

Parametry jsou definovány v následující tabulce:

| Název parametru | type | Popis |
| --- | --- | --- |
| `search` | `Edm.String` | Vyhledávací dotaz (buď v [jednoduché](query-simple-syntax.md) nebo [úplné](query-lucene-syntax.md) syntaxi dotazů Lucene). |
| `searchFields` | `Edm.String` | Čárkami oddělený seznam vyhledávacích polí, ve kterých chcete hledat; Výchozí hodnota pro všechna hledaná pole v indexu. Při použití [vyhledávacího pole](query-lucene-syntax.md#bkmk_fields) v `search` parametru přepíše specifikátory pole v dotazu Lucene všechna pole zadaná v tomto parametru. |
| `queryType` | `Edm.String` | `'simple'`nebo `'full'` ;`'simple'`výchozí hodnota je. Určuje, který dotazovací jazyk byl použit v `search` parametru. |
| `searchMode` | `Edm.String` | `'any'`nebo `'all'` ,`'any'`standardně. Označuje, zda musí být některé nebo všechny hledané výrazy v `search` parametru porovnány, aby bylo možné dokument počítat jako shodu. Při použití [logických operátorů Lucene](query-lucene-syntax.md#bkmk_boolean) v `search` parametru budou mít přednost před tímto parametrem. |

Všechny výše uvedené parametry jsou ekvivalentem odpovídajících [parametrů vyhledávacího požadavku v rozhraní API pro hledání](https://docs.microsoft.com/rest/api/searchservice/search-documents).

Funkce vrátí hodnotu typu `Edm.Boolean`, která umožňuje její sestavení pomocí dalších podvýrazů filtru s použitím logických [logických operátorů](search-query-odata-logical-operators.md). `search.ismatch`

> [!NOTE]
> Azure Search nepodporuje použití `search.ismatch` výrazů lambda nebo `search.ismatchscoring` uvnitř nich. To znamená, že není možné zapisovat filtry pro kolekce objektů, které mohou korelovat shody fulltextového vyhledávání s striktními shodami filtru u stejného objektu. Další podrobnosti o tomto omezení a příklady najdete v tématu [řešení potíží s filtry kolekce v Azure Search](search-query-troubleshoot-collection-filters.md). Podrobné informace o tom, proč toto omezení existuje, najdete v tématu [Principy filtrů kolekcí v Azure Search](search-query-understand-collection-filters.md).


### <a name="searchismatchscoring"></a>Hledat. ismatchscoring

Funkce, jako je `search.ismatch` funkce, vrací `true` pro dokumenty, které odpovídají dotazu fulltextového vyhledávání předanému jako parametr. `search.ismatchscoring` Rozdíl mezi nimi spočívá v tom, že skóre významnosti dokumentů odpovídající `search.ismatchscoring` dotazu bude přispívat k celkovému skóre dokumentu, zatímco v `search.ismatch`případě se skóre dokumentu nemění. Následující přetížení této funkce jsou k dispozici s parametry, které jsou stejné jako `search.ismatch`u:

- `search.ismatchscoring(search)`
- `search.ismatchscoring(search, searchFields)`
- `search.ismatchscoring(search, searchFields, queryType, searchMode)`

`search.ismatch` Funkce i `search.ismatchscoring` lze použít ve stejném výrazu filtru.

## <a name="examples"></a>Příklady

Vyhledá dokumenty ve slově "Waterfront". Tento dotaz filtru je stejný jako [požadavek na hledání](https://docs.microsoft.com/rest/api/searchservice/search-documents) s `search=waterfront`.

    search.ismatchscoring('waterfront')

Najde dokumenty se slovem "Hostel" a hodnocením větším nebo rovným 4 nebo dokumenty se slovem "Motel" a hodnocením rovným 5. Poznámka: Tento požadavek nebylo možné vyjádřit bez `search.ismatchscoring` funkce.

    search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5

Najde dokumenty bez slova "luxus".

    not search.ismatch('luxury')

Vyhledá dokumenty se frází "zobrazení v oceánu" nebo hodnocení rovno 5. Dotaz bude proveden pouze proti polím `HotelName` a `Rooms/Description`. `search.ismatchscoring`

Dokumenty, které odpovídají pouze druhé klauzuli disjunkce, budou vráceny příliš-Hotely s `Rating` hodnotou 5. Aby bylo jasné, že se tyto dokumenty neshodují s žádnou z částí výrazu, budou vráceny výsledek se stejnou hodnotou jako nula.

    search.ismatchscoring('"ocean view"', 'Rooms/Description,HotelName') or Rating eq 5

Vyhledá dokumenty, kde jsou termíny "Hotel" a "letiště" mezi 5 slovy od sebe navzájem v popisu hotelu a tam, kde není povolený kouření aspoň na některých místnostech. Tento dotaz používá [celý dotazovací jazyk Lucene](query-lucene-syntax.md).

    search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and Rooms/any(room: not room/SmokingAllowed)

## <a name="next-steps"></a>Další postup  

- [Filtry v Azure Search](search-filters.md)
- [Přehled jazyka výrazů OData pro Azure Search](query-odata-filter-orderby-syntax.md)
- [Referenční dokumentace syntaxe výrazu OData pro Azure Search](search-query-odata-syntax-reference.md)
- [Hledat dokumenty &#40;Azure Search REST API služby&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
