---
title: Principy filtrů kolekce OData
titleSuffix: Azure Cognitive Search
description: Naučte se, jak filtry kolekce OData pracují v Azure Kognitivní hledání dotazy, včetně omezení a chování jedinečných pro kolekce.
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
ms.openlocfilehash: f6e8ed5baef9b8594bb1fe03942e831fd8264a56
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113067"
---
# <a name="understanding-odata-collection-filters-in-azure-cognitive-search"></a>Porozumění filtrům kolekce OData v Azure Kognitivní hledání

Chcete-li [filtrovat](query-odata-filter-orderby-syntax.md) pole kolekce v Azure kognitivní hledání, můžete použít [operátory`any` a `all`](search-query-odata-collection-operators.md) spolu s **lambda výrazy**. Výrazy lambda jsou logické výrazy, které odkazují na **proměnnou rozsahu**. Operátory `any` a `all` jsou analogické ke smyčce `for` ve většině programovacích jazyků, přičemž proměnná rozsahu přebírá roli proměnné smyčky a výraz lambda jako tělo smyčky. Proměnná rozsahu během iterace smyčky převezme hodnotu "Current" kolekce.

Alespoň to funguje koncepčně. Ve skutečnosti Azure Kognitivní hledání implementuje filtry velice jiným způsobem, jak fungují `for` cykly. V ideálním případě by tento rozdíl byl neviditelný, ale v některých situacích to není. Konečným výsledkem je, že existují pravidla, která je nutné provést při psaní výrazů lambda.

V tomto článku se dozvíte, proč existují pravidla pro filtry kolekcí, a prozkoumejte, jak Kognitivní hledání Azure tyto filtry spouští. Pokud píšete Pokročilé filtry pomocí složitých výrazů lambda, může být tento článek užitečný při vytváření znalostí o tom, co je možné ve filtrech a proč.

Informace o tom, jaká pravidla filtrů kolekcí jsou, včetně příkladů, najdete [v tématu řešení potíží s filtry kolekce OData v Azure kognitivní hledání](search-query-troubleshoot-collection-filters.md).

## <a name="why-collection-filters-are-limited"></a>Proč jsou filtry kolekcí omezené

Existují tři základní důvody, proč nejsou všechny funkce filtru podporovány pro všechny typy kolekcí:

1. Pro určité typy dat jsou podporovány pouze některé operátory. Například nemá smysl porovnat hodnoty Boolean `true` a `false` pomocí `lt`, `gt`a tak dále.
1. Azure Kognitivní hledání nepodporuje **korelační hledání** polí typu `Collection(Edm.ComplexType)`.
1. Azure Kognitivní hledání používá obrácené indexy ke spouštění filtrů přes všechny typy dat, včetně kolekcí.

Prvním důvodem je pouze důsledek, jak jsou definovány jazyky OData a systém typů EDM. Poslední dva jsou podrobněji vysvětleny ve zbývající části tohoto článku.

## <a name="correlated-versus-uncorrelated-search"></a>Prokorelujd versus nekorelační hledání

Při použití více kritérií filtru v rámci kolekce složitých objektů jsou kritéria **korelace** , protože se vztahují na *každý objekt v kolekci*. Například následující filtr vrátí hotely, které mají alespoň jednu Deluxe místnost s frekvencí menší než 100:

    Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 100)

Pokud filtrování *nekoreluje*, může výše uvedený filtr vracet hotely, kde je jedna místnost Deluxe a jiné místnosti má základní sazbu nižší než 100. To by nevedlo smysl, protože obě klauzule výrazu lambda se vztahují na stejnou proměnnou rozsahu, jmenovitě `room`. To je důvod, proč se tyto filtry korelují.

Pro fulltextové vyhledávání ale neexistuje žádný způsob, jak odkazovat na konkrétní proměnnou rozsahu. Pokud k vystavení [úplného dotazu Lucene](query-lucene-syntax.md) , jako je tato, použijete pole hledání:

    Rooms/Type:deluxe AND Rooms/Description:"city view"

Můžete se dostat do hotelů, kde je jedna místnost Deluxe, a v popisu se zobrazí jiné místo "město". Například následující dokument s `Id` `1` by odpovídal dotazu:

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

Důvodem je, že `Rooms/Type` odkazuje na všechny analyzované výrazy `Rooms/Type` pole v celém dokumentu a podobně pro `Rooms/Description`, jak je znázorněno v následujících tabulkách.

Jak je uloženo `Rooms/Type` pro fulltextové vyhledávání:

| Termín `Rooms/Type` | ID dokumentů |
| --- | --- |
| deluxe | 1, 2 |
| standard | 1 |

Jak je uloženo `Rooms/Description` pro fulltextové vyhledávání:

| Termín `Rooms/Description` | ID dokumentů |
| --- | --- |
| courtyard | 2 |
| city | 1 |
| zahrada | 1 |
| celou | 1 |
| Motel | 2 |
| konverzační | 1, 2 |
| standard | 1 |
| sad | 1 |
| zobrazit | 1 |

Proto na rozdíl od výše uvedeného filtru, který v podstatě říká "odpovídání na dokumenty", kde má místnost `Type` rovna "Deluxe Room" a **stejná místnost** má `BaseRate` menší než 100 ", vyhledávací dotaz znamená" shoda dokumentů, kde `Rooms/Type` má výraz "Deluxe" a `Rooms/Description` má frázi "zobrazení měst". Neexistuje žádný koncept jednotlivých místností, jejichž pole je možné v druhém případě sladit.

> [!NOTE]
> Pokud chcete zobrazit podporu pro korelační vyhledávání přidané do Azure Kognitivní hledání, Hlasujte prosím pro [tuto položku uživatele hlasu](https://feedback.azure.com/forums/263029-azure-search/suggestions/37735060-support-correlated-search-on-complex-collections).

## <a name="inverted-indexes-and-collections"></a>Obrácené indexy a kolekce

Možná jste si všimli, že existuje mnohem méně omezení pro výrazy lambda přes složitou kolekci, než je pro jednoduché kolekce, jako je `Collection(Edm.Int32)`, `Collection(Edm.GeographyPoint)`a tak dále. Důvodem je to, že Azure Kognitivní hledání ukládá komplexní kolekce jako skutečné kolekce poddokumentů, zatímco jednoduché kolekce se neukládají zároveň jako kolekce.

Představte si například pole pro shromažďování řetězců, jako je například `seasons` v indexu pro online prodejce. Některé dokumenty nahrané do tohoto indexu můžou vypadat takto:

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

Hodnoty pole `seasons` jsou uloženy ve struktuře s názvem **obrácený index**, který vypadá přibližně takto:

| Označení | ID dokumentů |
| --- | --- |
| návratu | 1, 2 |
| letní | 1 |
| narozeniny | 1, 2 |
| Zimní | 2, 3 |

Tato datová struktura je navržená tak, aby odpovídala jedné otázce velkou rychlostí: ve kterých dokumentech se daný pojem zobrazuje? Zodpovězení této otázky funguje více jako při jednoduché kontrole rovnosti než smyčka nad kolekcí. V takovém případě se jedná o důvody pro kolekce řetězců, ale Azure Kognitivní hledání umožňuje `eq` jako operátor porovnání uvnitř výrazu lambda pro `any`.

Při vytváření z rovnosti se teď podíváme na to, jak je možné zkombinovat více kontrol rovnosti u stejné proměnné rozsahu s `or`. Díky algebraický a [vlastnictví kvantifikátorů](https://en.wikipedia.org/wiki/Existential_quantification#Negation)to funguje. Tento výraz:

    seasons/any(s: s eq 'winter' or s eq 'fall')

je ekvivalentní:

    seasons/any(s: s eq 'winter') or seasons/any(s: s eq 'fall')

a každý ze dvou `any` dílčích výrazů lze efektivně provádět pomocí obráceného indexu. V důsledku toho, že se jedná o [zákon o negaci kvantifikátorů](https://en.wikipedia.org/wiki/Existential_quantification#Negation), tento výraz:

    seasons/all(s: s ne 'winter' and s ne 'fall')

je ekvivalentní:

    not seasons/any(s: s eq 'winter' or s eq 'fall')

To je důvod, proč je možné použít `all` s `ne` a `and`.

> [!NOTE]
> I když jsou podrobnosti nad rámec tohoto dokumentu, tyto stejné principy se také rozšíří na [dálkovou a Mezioddílové testy pro kolekce geograficky prostorových bodů](search-query-odata-geo-spatial-functions.md) . To je důvod, proč v `any`:
>
> - `geo.intersects` nemůže být negace.
> - `geo.distance` musí být porovnány pomocí `lt` nebo `le`
> - výrazy musí být kombinovány s `or`, nikoli `and`
>
> Pravidla konverzace se vztahují na `all`.

Při filtrování kolekcí datových typů, které podporují `lt`, `gt`, `le`a `ge`, jako je například `Collection(Edm.Int32)`, jsou povoleny širší škály výrazů. Konkrétně můžete použít `and` a také `or` v `any`, pokud jsou základní výrazy porovnání zkombinovány do **porovnávání rozsahů** pomocí `and`, které jsou následně zkombinovány pomocí `or`. Tato struktura logických výrazů se nazývá [Disjunctive Normal Form (DNF)](https://en.wikipedia.org/wiki/Disjunctive_normal_form), jinak se označuje jako "ORS of and". Výrazy lambda pro `all` pro tyto datové typy musí být v [normálním tvaru conjunctive (CNF)](https://en.wikipedia.org/wiki/Conjunctive_normal_form), jinak označované jako "and of ORS". Azure Kognitivní hledání umožňuje toto porovnání rozsahu, protože je může spouštět pomocí obrácených indexů, stejně jako může provádět rychlé vyhledávání řetězců.

Tady jsou shrnutá pravidla pro to, co je ve výrazu lambda povolené:

- Uvnitř `any`jsou *pozitivní kontroly* vždy povoleny, jako je rovnost, porovnávání rozsahů, `geo.intersects`nebo `geo.distance` v porovnání s `lt` nebo `le` (Představte si "těsnost" jako v případě rovnosti při kontrole vzdálenosti).
- V rámci `any`jsou `or` vždy povolena. `and` můžete použít pouze pro datové typy, které mohou expresní kontroly rozsahu a pouze v případě, že používáte ORs of and (DNF).
- V rámci `all`jsou pravidla obrácená – jsou povolené jenom *záporné kontroly* , můžete použít `and` Always a můžete použít `or` jenom pro kontroly rozsahu vyjádřené jako and of ORS (CNF).

V praxi se jedná o typy filtrů, které nejpravděpodobněji budete chtít použít. Stále je dobré pochopit hranice toho, co je možné, i když.

Konkrétní příklady, které typy filtrů jsou povolené a které nejsou, najdete v tématu [Postup zápisu platných filtrů kolekcí](search-query-troubleshoot-collection-filters.md#bkmk_examples).

## <a name="next-steps"></a>Další kroky  

- [Řešení potíží s filtry kolekce OData v Azure Kognitivní hledání](search-query-troubleshoot-collection-filters.md)
- [Filtry v Azure Kognitivní hledání](search-filters.md)
- [Přehled jazyka výrazů OData pro Azure Kognitivní hledání](query-odata-filter-orderby-syntax.md)
- [Referenční dokumentace syntaxe výrazu OData pro Azure Kognitivní hledání](search-query-odata-syntax-reference.md)
- [Hledání dokumentů &#40;Azure kognitivní hledání REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
