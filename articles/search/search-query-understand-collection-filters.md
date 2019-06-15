---
title: Principy filtrů kolekce OData – Azure Search
description: Principy fungování filtry kolekcí OData ve službě Azure Search dotazy.
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
ms.openlocfilehash: 7af1b0ab95d04249d6d74e3324dbeb30eda6e1de
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079597"
---
# <a name="understanding-odata-collection-filters-in-azure-search"></a>Principy filtrů kolekce OData ve službě Azure Search

K [filtr](query-odata-filter-orderby-syntax.md) v kolekci polí ve službě Azure Search, můžete použít [ `any` a `all` operátory](search-query-odata-collection-operators.md) spolu s **výrazy lambda**. Výrazy lambda jsou logické výrazy, které odkazují **proměnnou rozsahu**. `any` a `all` operátory jsou obdobou `for` smyčky ve většině programovacích jazyků, se proměnné rozsahu, přičemž role proměnná smyčky a výraz lambda jako tělo smyčky. Proměnná rozsahu přebírá hodnotu "aktuální" kolekce během iterace smyčky.

Na nejnižší, který je, jak to funguje koncepčně. Azure Search ve skutečnosti implementuje filtry velmi jiným způsobem, jak `for` smyčky práce. V ideálním případě by tento rozdíl neviditelná pro vás, ale v některých situacích není. Konečným výsledkem bude, že jsou pravidla, které je třeba dodržovat při psaní výrazů lambda.

Tento článek vysvětluje, proč existují pravidla pro kolekci filtrů ukážou, jak Azure Search spustí tyto filtry. Pokud píšete rozšířené filtry s komplexní lambda výrazy, vám můžou pomoct v tomto článku v budově pochopíte co je možné ve filtrech a proč.

Informace o tom, co pravidel jsou kolekce filtrů, včetně příkladů, najdete v části [filtry kolekce řešení potíží s OData ve službě Azure Search](search-query-troubleshoot-collection-filters.md).

## <a name="why-collection-filters-are-limited"></a>Proč jsou omezené kolekci filtrů

Existují tři základní důvody, případně proč bezpečná není všechny typy kolekcí, které podporují všechny funkce filtru:

1. Pouze některé operátory jsou podporovány pro určitých datových typů. Například nemá smysl pro porovnání logické hodnoty `true` a `false` pomocí `lt`, `gt`, a tak dále.
1. Azure Search nepodporuje **korelační hledání** na pole typu `Collection(Edm.ComplexType)`.
1. Azure Search používá obrácený indexy, které provádějí filtry nad všemi typy dat, včetně kolekcí.

Prvním důvod je právě důsledkem jak jsou definované jazyka OData a systém typů modelu EDM. Poslední dva jsou vysvětlené podrobněji ve zbývající části tohoto článku.

## <a name="correlated-versus-uncorrelated-search"></a>Korelační a bez korelace nejsou vyhledávání

Při použití více kritéria filtru v rámci kolekce komplexních objektů, kritéria jsou **korelační** vzhledem k tomu, že se vztahují na *každý objekt v kolekci*. Například následující filtr vrátí hotely, které mají alespoň jednu deluxe místnosti s rychlostí nižší než 100:

    Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 100)

Pokud se filtrování *bez korelace nejsou*, výše uvedený filtr může vrátit ohodnotit hotely, kde je jediná místnost deluxe a jiné místnosti má nastavenu základní hodnotu menší než 100. Který by nedávalo smysl, protože obě klauzule výrazu lambda použít u stejné proměnné rozsahu, a to `room`. To je důvod, proč se korelují tyto filtry.

Ale pro fulltextové vyhledávání, neexistuje žádný způsob, jak odkazovat na konkrétní rozsah proměnné. Pokud používáte fielded vyhledávání pro vydávání [úplné dotazů Lucene](query-lucene-syntax.md) tohoto objektu, jako jsou:

    Rooms/Type:deluxe AND Rooms/Description:"city view"

hotels zpět ve kterém je jediná místnost deluxe a jiné místnosti uvádí "city zobrazení" se může zobrazit v popisu. Například následujícím dokumentu s `Id` z `1` by odpovídala dotazu:

```json
{
  "value": [
    {
      "Id": "1",
      "Rooms": [
        { "Type": "deluxe", "Description": "Large garden view suite" },
        { "Type": "standard", "Description": "Standard city view room" }
      ]
    },
    {
      "Id": "2",
      "Rooms": [
        { "Type": "deluxe", "Description": "Courtyard motel room" }
      ]
    }
  ]
}
```

Důvodem je, že `Rooms/Type` odkazuje na všechny analyzované podmínky `Rooms/Type` pole v celém dokumentu a podobně pro `Rooms/Description`, jak je znázorněno v následujících tabulkách.

Jak `Rooms/Type` je uloženo pro fulltextové vyhledávání:

| Výraz v `Rooms/Type` | ID dokumentu |
| --- | --- |
| deluxe | 1, 2 |
| standard | 1 |

Jak `Rooms/Description` je uloženo pro fulltextové vyhledávání:

| Výraz v `Rooms/Description` | ID dokumentu |
| --- | --- |
| dvůr | 2 |
| city | 1 |
| zahrada | 1 |
| Velké | 1 |
| motel | 2 |
| Místnosti | 1, 2 |
| standard | 1 |
| Sada | 1 |
| zobrazit | 1 |

Proto na rozdíl od výše filtru, která v podstatě říká "odpovídaly i dokumenty, kde má prostor `Type` rovno"Deluxe místnosti"a **této stejné místnosti** má `BaseRate` kratší než 100", říká vyhledávací dotaz "shoda dokumenty, kde `Rooms/Type`má termín "deluxe" a `Rooms/Description` má frázi "city zobrazení". Neexistuje žádná koncepce jednotlivé místnosti, jejichž pole může být korelována v druhém případě.

> [!NOTE]
> Pokud jste chtěli podporu pro korelační vyhledávání přidat do služby Azure Search, prosím hlasovat pro [tuto položku User Voice](https://feedback.azure.com/forums/263029-azure-search/suggestions/37735060-support-correlated-search-on-complex-collections).

## <a name="inverted-indexes-and-collections"></a>Obráceným indexy a kolekce

Mohli jste si všimnout, že jsou mnohem méně omezení pro výrazy lambda přes komplexní kolekce než je například jednoduché kolekce pro `Collection(Edm.Int32)`, `Collection(Edm.GeographyPoint)`, a tak dále. Je to proto Azure Search ukládá komplexní kolekce jako skutečné kolekce dílčí dokumentů, zatímco jednoduché kolekce nejsou uložené jako kolekce vůbec.

Představte si třeba pole kolekce filterable řetězce jako `seasons` v indexu pro online prodejce. Některé dokumenty uložené pro tento index může vypadat takto:

```json
{
  "value": [
    {
      "id": "1",
      "name": "Hiking boots",
      "seasons": ["spring", "summer", "fall"]
    },
    {
      "id": "2",
      "name": "Rain jacket",
      "seasons": ["spring", "fall", "winter"]
    },
    {
      "id": "3",
      "name": "Parka",
      "seasons": ["winter"]
    }
  ]
}
```

Hodnoty `seasons` pole jsou uloženy ve struktuře s názvem **obrácený index**, která vypadá přibližně takto:

| Termín | ID dokumentu |
| --- | --- |
| Spring | 1, 2 |
| letní | 1 |
| spadají | 1, 2 |
| na zimní | 2, 3 |

Tuto datovou strukturu je navržené pro odpověď na otázku jeden s skvělé rychlost: V dokumenty, které daný pojem zřejmě? Odpovědi na tuto otázku pracuje podobně jako kontrolu rovnosti jednoduché než smyčce přes kolekce. Ve skutečnosti to je důvod, proč pro kolekce řetězců, Azure Search umožňuje pouze `eq` jako operátor porovnání uvnitř výrazu lambda pro `any`.

Vytváření z rovnosti, dále podíváme na tom, jak je možné kombinovat více kontroly rovnosti na proměnnou rozsahu s `or`. Funguje díky algebraický a [distributivních vlastnost kvantifikátory](https://en.wikipedia.org/wiki/Existential_quantification#Negation). Tento výraz:

    seasons/any(s: s eq 'winter' or s eq 'fall')

je ekvivalentní:

    seasons/any(s: s eq 'winter') or seasons/any(s: s eq 'fall')

a každý z nich `any` dílčí výrazy mohou být provedeny efektivně, pomocí obrácenou indexu. Navíc k [negace práva kvantifikátory](https://en.wikipedia.org/wiki/Existential_quantification#Negation), tento výraz:

    seasons/all(s: s ne 'winter' and s ne 'fall')

je ekvivalentní:

    not seasons/any(s: s eq 'winter' or s eq 'fall')

Proto je možné použít `all` s `ne` a `and`.

> [!NOTE]
> I když podrobnosti jsou nad rámec tohoto dokumentu, tyto stejné zásady rozšířit [vzdálenosti a průnik testy pro kolekce geoprostorového bodů](search-query-odata-geo-spatial-functions.md) také. To je důvod, proč v `any`:
>
> - `geo.intersects` Nelze se bude negovat
> - `geo.distance` musí být porovnány pomocí `lt` nebo `le`
> - výrazy musí být kombinován se `or`, nikoli `and`
>
> Konverzace pravidla platí pro `all`.

Širší výrazů jsou povoleny v případě, že filtrování na kolekce dat typy, které podporují `lt`, `gt`, `le`, a `ge` operátory, jako například `Collection(Edm.Int32)` třeba. Konkrétně můžete použít `and` stejně jako `or` v `any`, tak dlouho, dokud základní výrazy porovnání jsou sloučeny do **rozsahu porovnání** pomocí `and`, které jsou pak dále kombinované pomocí `or`. Tato struktura logické výrazy se nazývá [disjunktivní normální formuláře (DNF)](https://en.wikipedia.org/wiki/Disjunctive_normal_form), jinak známé jako "OR a". Naopak, lambda výrazy pro `all` pro tyto datové typy musí být v [formuláře možností (pojivové normální CNF)](https://en.wikipedia.org/wiki/Conjunctive_normal_form), jinak známé jako "Operátoru and or". Služba Azure Search umožňuje takové porovnání rozsahu, protože může spustit, je pomocí obrácený indexy efektivně, stejně to můžete provést rychlé termín vyhledávání pro řetězce.

Stručně řečeno tady jsou hrubé odhady pro je povolené ve výrazu lambda:

- Uvnitř `any`, *kladné kontroly* jsou vždy povolena, jako jsou operátory rovnosti, porovnání rozsahu `geo.intersects`, nebo `geo.distance` ve srovnání s `lt` nebo `le` (přemýšlení "těsnost", jako je například rovnost při rozhodování o kontrolu vzdálenost).
- Uvnitř `any`, `or` je vždycky povolená. Můžete použít `and` pouze pro datové typy, které můžete vyjádřit kontroly rozsahu a pouze tehdy, pokud použijete or operátoru and (DNF).
- Uvnitř `all`, pravidla se vrátit zpět – pouze *záporné kontroly* jsou povolené, můžete použít `and` vždy, můžete si `or` pouze pro rozsah kontroly, vyjádřené jako operátoru and z or (možností CNF).

V praxi jsou tyto typy filtrů, které budete nejpravděpodobněji i přesto použít. Je to pomůže vám pochopit, co je ale možné hranice.

Konkrétní příklady, které druhy filtry jsou povolené a které nejsou, naleznete v tématu [jak platnou kolekci filtrů zápisu](search-query-troubleshoot-collection-filters.md#bkmk_examples).

## <a name="next-steps"></a>Další postup  

- [Řešení potíží s filtry kolekcí OData ve službě Azure Search](search-query-troubleshoot-collection-filters.md)
- [Filtry ve službě Azure Search](search-filters.md)
- [Přehled o výraz jazyka OData pro službu Azure Search](query-odata-filter-orderby-syntax.md)
- [Reference k syntaxi výrazů OData pro službu Azure Search](search-query-odata-syntax-reference.md)
- [Hledání dokumentů &#40;rozhraní REST API služby Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
