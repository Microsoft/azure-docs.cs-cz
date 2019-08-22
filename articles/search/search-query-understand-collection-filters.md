---
title: Porozumění filtrům kolekce OData – Azure Search
description: Porozumění způsobu, jakým filtry kolekce OData fungují v Azure Searchch dotazech.
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
ms.openlocfilehash: 5c3a0205f5a9ac5115e78f1bc11f70b2c50a9714
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647412"
---
# <a name="understanding-odata-collection-filters-in-azure-search"></a>Principy filtrů kolekce OData v Azure Search

Chcete-li [filtrovat](query-odata-filter-orderby-syntax.md) pole kolekce v Azure Search, můžete použít [ `any` operátory a `all` ](search-query-odata-collection-operators.md) společně s **lambda výrazy**. Výrazy lambda jsou logické výrazy, které odkazují na **proměnnou rozsahu**. Operátory `any` `for` a `all` jsou analogické jako smyčka ve většině programovacích jazyků, přičemž proměnná rozsahu přebírá roli proměnné smyčky a výraz lambda jako tělo smyčky. Proměnná rozsahu během iterace smyčky převezme hodnotu "Current" kolekce.

Alespoň to funguje koncepčně. Ve skutečnosti Azure Search implementuje filtry ve velmi jiném způsobu, jak `for` cykly fungují. V ideálním případě by tento rozdíl byl neviditelný, ale v některých situacích to není. Konečným výsledkem je, že existují pravidla, která je nutné provést při psaní výrazů lambda.

V tomto článku se dozvíte, proč pravidla pro filtry kolekcí existují. Prozkoumejte, jak Azure Search tyto filtry spouští. Pokud píšete Pokročilé filtry pomocí složitých výrazů lambda, může být tento článek užitečný při vytváření znalostí o tom, co je možné ve filtrech a proč.

Informace o tom, jaká pravidla filtrů kolekcí jsou, včetně příkladů, najdete [v tématu řešení potíží s filtry kolekce OData v Azure Search](search-query-troubleshoot-collection-filters.md).

## <a name="why-collection-filters-are-limited"></a>Proč jsou filtry kolekcí omezené

Existují tři základní důvody, proč nejsou všechny funkce filtru podporovány pro všechny typy kolekcí:

1. Pro určité typy dat jsou podporovány pouze některé operátory. Například nemá smysl porovnat logické `true` hodnoty a `false` použít `lt`, `gt`a tak dále.
1. Azure Search nepodporuje **korelační hledání** polí typu `Collection(Edm.ComplexType)`.
1. Azure Search používá obrácené indexy ke spouštění filtrů přes všechny typy dat, včetně kolekcí.

Prvním důvodem je pouze důsledek, jak jsou definovány jazyky OData a systém typů EDM. Poslední dva jsou podrobněji vysvětleny ve zbývající části tohoto článku.

## <a name="correlated-versus-uncorrelated-search"></a>Prokorelujd versus nekorelační hledání

Při použití více kritérií filtru v rámci kolekce složitých objektů jsou kritéria korelace , protože se vztahují na *každý objekt v kolekci*. Například následující filtr vrátí hotely, které mají alespoň jednu Deluxe místnost s frekvencí menší než 100:

    Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 100)

Pokud filtrování nekoreluje, může výše uvedený filtr vracet hotely, kde je jedna místnost Deluxe a jiné místnosti má základní sazbu nižší než 100. To by nevedlo smysl, protože obě klauzule výrazu lambda se vztahují na stejnou proměnnou rozsahu, konkrétně `room`. To je důvod, proč se tyto filtry korelují.

Pro fulltextové vyhledávání ale neexistuje žádný způsob, jak odkazovat na konkrétní proměnnou rozsahu. Pokud k vystavení [úplného dotazu Lucene](query-lucene-syntax.md) , jako je tato, použijete pole hledání:

    Rooms/Type:deluxe AND Rooms/Description:"city view"

Můžete se dostat do hotelů, kde je jedna místnost Deluxe, a v popisu se zobrazí jiné místo "město". Například dokument uvedený níže se shoduje s `Id` `1` dotazem:

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

Důvodem je, že `Rooms/Type` odkazuje na všechny analyzované výrazy `Rooms/Type` v celém dokumentu a podobně pro `Rooms/Description`, jak je znázorněno v následujících tabulkách.

Jak `Rooms/Type` je uloženo pro fulltextové vyhledávání:

| Termín`Rooms/Type` | ID dokumentů |
| --- | --- |
| deluxe | 1, 2 |
| standard | 1 |

Jak `Rooms/Description` je uloženo pro fulltextové vyhledávání:

| Termín`Rooms/Description` | ID dokumentů |
| --- | --- |
| courtyard | 2 |
| city | 1 |
| zahrada | 1 |
| velké | 1 |
| Motel | 2 |
| konverzační | 1, 2 |
| standard | 1 |
| sad | 1 |
| zobrazení | 1 |

Proto na rozdíl od výše uvedeného filtru, který v podstatě říká "odpovídání na dokumenty, `Type` kde se místnost rovná" Deluxe místnost "a **že stejná místnost** má `BaseRate` méně než 100", vyhledávací dotaz znamená "odpovídání `Rooms/Type` na dokumenty", kde má výraz "Deluxe "a `Rooms/Description` má frázi" zobrazení měst ". Neexistuje žádný koncept jednotlivých místností, jejichž pole je možné v druhém případě sladit.

> [!NOTE]
> Pokud chcete zobrazit podporu pro korelační vyhledávání přidané do Azure Search, Hlasujte prosím pro [tuto položku uživatelského hlasu](https://feedback.azure.com/forums/263029-azure-search/suggestions/37735060-support-correlated-search-on-complex-collections).

## <a name="inverted-indexes-and-collections"></a>Obrácené indexy a kolekce

Možná jste si všimli, že existuje mnohem méně omezení pro výrazy lambda přes složitou kolekci `Collection(Edm.Int32)`, než je pro jednoduché kolekce, `Collection(Edm.GeographyPoint)`například, a tak dále. Důvodem je to, že Azure Search ukládá komplexní kolekce jako skutečné kolekce poddokumentů, zatímco jednoduché kolekce nejsou uloženy ve všech kolekcích.

Představte si například pole pro shromažďování řetězců, jako `seasons` je třeba v indexu pro online prodejce. Některé dokumenty nahrané do tohoto indexu můžou vypadat takto:

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

Hodnoty `seasons` pole jsou uloženy ve struktuře s názvem **obrácený index**, který vypadá přibližně takto:

| Termín | ID dokumentů |
| --- | --- |
| návratu | 1, 2 |
| letní | 1 |
| narozeniny | 1, 2 |
| Zimní | 2, 3 |

Tato datová struktura je navržená tak, aby odpovídala jedné otázce s velkou rychlostí: Ve kterých dokumentech se daný pojem zobrazuje? Zodpovězení této otázky funguje více jako při jednoduché kontrole rovnosti než smyčka nad kolekcí. Ve skutečnosti to je důvod, proč pro kolekce řetězců Azure Search umožňuje `eq` pouze jako operátor porovnání uvnitř výrazu lambda pro. `any`

Sestavování od rovnosti: dále se podíváme na to, jak je možné zkombinovat více kontrol rovnosti na stejnou `or`proměnnou rozsahu s. Díky algebraický a [vlastnictví kvantifikátorů](https://en.wikipedia.org/wiki/Existential_quantification#Negation)to funguje. Tento výraz:

    seasons/any(s: s eq 'winter' or s eq 'fall')

je ekvivalentní:

    seasons/any(s: s eq 'winter') or seasons/any(s: s eq 'fall')

a každý ze dvou `any` dílčích výrazů je možné efektivně provádět pomocí obráceného indexu. V důsledku toho, že se jedná o [zákon o negaci kvantifikátorů](https://en.wikipedia.org/wiki/Existential_quantification#Negation), tento výraz:

    seasons/all(s: s ne 'winter' and s ne 'fall')

je ekvivalentní:

    not seasons/any(s: s eq 'winter' or s eq 'fall')

což je důvod, proč je možné použít `all` s `ne` a `and`.

> [!NOTE]
> I když jsou podrobnosti nad rámec tohoto dokumentu, tyto stejné principy se také rozšíří na [dálkovou a Mezioddílové testy pro kolekce geograficky prostorových bodů](search-query-odata-geo-spatial-functions.md) . To je důvod, proč `any`v:
>
> - `geo.intersects`nemůže být negace
> - `geo.distance`musí být porovnány `lt` pomocí nebo`le`
> - výrazy musí být kombinovány `or`s, ne`and`
>
> Pravidla konverzace platí pro `all`.

Při filtrování kolekcí datových `lt`typů, které podporují operátory `le`, `gt`, a `ge` , jako `Collection(Edm.Int32)` například, jsou povoleny širší škály výrazů. Konkrétně můžete `and` použít stejně jako `or` v `any`, pokud jsou základní výrazy porovnání zkombinovány do **porovnávání rozsahů** pomocí `and`, které jsou poté dále kombinovány pomocí `or`. Tato struktura logických výrazů se nazývá [Disjunctive Normal Form (DNF)](https://en.wikipedia.org/wiki/Disjunctive_normal_form), jinak se označuje jako "ORS of and". Naopak výrazy lambda pro `all` pro tyto datové typy musí být v [conjunctive normální formě (CNF)](https://en.wikipedia.org/wiki/Conjunctive_normal_form), jinak označované jako "and of ORS". Azure Search umožňuje toto porovnání rozsahu, protože je může spouštět pomocí obrácených indexů, stejně jako může provádět rychlé vyhledávání řetězců.

Tady jsou shrnutá pravidla pro to, co je ve výrazu lambda povolené:

- Uvnitř `any`jsou *pozitivní kontroly* vždy povoleny, `geo.intersects`jako je rovnost, porovnávání rozsahů nebo `geo.distance` porovnání s `lt` nebo `le` (Představte "těsnost" jako rovnost, pokud je to možné. vzdálenost).
- Uvnitř `any`jevždypovoleno `or` . Můžete použít `and` pouze pro datové typy, které mohou vyjádřit kontroly rozsahu a pouze v případě, že používáte ORS of and (DNF).
- V `all`rámci jsou pravidla stornována – jsou povoleny pouze *záporné kontroly* , můžete použít `and` možnost Always a můžete použít `or` pouze pro kontroly rozsahu vyjádřené jako and of ORS (CNF).

V praxi se jedná o typy filtrů, které nejpravděpodobněji budete chtít použít. Stále je dobré pochopit hranice toho, co je možné, i když.

Konkrétní příklady, které typy filtrů jsou povolené a které nejsou, najdete v tématu [Postup zápisu platných filtrů kolekcí](search-query-troubleshoot-collection-filters.md#bkmk_examples).

## <a name="next-steps"></a>Další postup  

- [Řešení potíží s filtry kolekce OData v Azure Search](search-query-troubleshoot-collection-filters.md)
- [Filtry v Azure Search](search-filters.md)
- [Přehled jazyka výrazů OData pro Azure Search](query-odata-filter-orderby-syntax.md)
- [Referenční dokumentace syntaxe výrazu OData pro Azure Search](search-query-odata-syntax-reference.md)
- [Hledat dokumenty &#40;Azure Search REST API služby&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
