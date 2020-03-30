---
title: Principy filtrů kolekce OData
titleSuffix: Azure Cognitive Search
description: Naučte se mechaniky, jak filtry kolekce OData fungují v dotazech Azure Cognitive Search, včetně omezení a chování, které jsou jedinečné pro kolekce.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113067"
---
# <a name="understanding-odata-collection-filters-in-azure-cognitive-search"></a>Principy filtrů kolekce OData v Azure Cognitive Search

Chcete-li [filtrovat](query-odata-filter-orderby-syntax.md) na sběrných polích v Azure Cognitive Search, můžete použít [ `any` a `all` operátory](search-query-odata-collection-operators.md) spolu s **lambda výrazy**. Lambda výrazy jsou logické výrazy, které odkazují na **proměnnou rozsahu**. A `any` `all` operátory jsou obdobou `for` smyčky ve většině programovacích jazyků, s proměnnou rozsahu s rolí proměnné smyčky a výrazem lambda jako tělo smyčky. Proměnná rozsahu přebírá "aktuální" hodnotu kolekce během iterace smyčky.

Alespoň tak to funguje koncepčně. Ve skutečnosti Azure Cognitive Search implementuje filtry `for` velmi odlišným způsobem, než jak smyčky fungují. V ideálním případě by tento rozdíl byl pro vás neviditelný, ale v určitých situacích tomu tak není. Konečným výsledkem je, že existují pravidla, která musíte dodržovat při psaní výrazů lambda.

Tento článek vysvětluje, proč existují pravidla pro filtry kolekce tím, že zkoumá, jak Azure Cognitive Search provádí tyto filtry. Pokud píšete pokročilé filtry se složitými výrazy lambda, může být tento článek užitečný při vytváření porozumění tomu, co je možné ve filtrech a proč.

Informace o tom, jaká jsou pravidla pro filtry kolekce, včetně příkladů, naleznete [v tématu Poradce při potížích s filtry kolekce OData v Azure Cognitive Search](search-query-troubleshoot-collection-filters.md).

## <a name="why-collection-filters-are-limited"></a>Proč jsou filtry kolekce omezené

Existují tři základní důvody, proč nejsou podporovány všechny funkce filtru pro všechny typy kolekcí:

1. Pro určité datové typy jsou podporovány pouze určité operátory. Například `true` nemá smysl porovnávat logické hodnoty a `false` používat `lt` `gt`, a tak dále.
1. Azure Cognitive Search nepodporuje **korelované** vyhledávání `Collection(Edm.ComplexType)`v polích typu .
1. Azure Cognitive Search používá invertované indexy ke spuštění filtrů přes všechny typy dat, včetně kolekcí.

Prvním důvodem je pouze důsledkem toho, jak jsou definovány jazyk OData a systém typu EDM. Poslední dva jsou podrobněji vysvětleny ve zbytku tohoto článku.

## <a name="correlated-versus-uncorrelated-search"></a>Korelační versus nekorelované vyhledávání

Při použití více kritérií filtru přes kolekci složitých objektů, kritéria jsou **korelovány,** protože platí pro *každý objekt v kolekci*. Například následující filtr vrátí hotely, které mají alespoň jeden luxusní pokoj s nižší sazbou nižší než 100:

    Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 100)

Pokud filtrování nebylo *korelované*, výše uvedený filtr může vrátit hotely, kde je jeden pokoj luxusní a jiný pokoj má základní sazbu nižší než 100. To by nedávalo smysl, protože obě klauzule výrazu lambda se `room`vztahují na stejnou proměnnou rozsahu, a to . To je důvod, proč tyto filtry jsou korelovány.

Pro fulltextové vyhledávání však neexistuje žádný způsob, jak odkazovat na určitou proměnnou rozsahu. Pokud používáte fielded vyhledávání k vydání [úplné lucene dotaz,](query-lucene-syntax.md) jako je tento:

    Rooms/Type:deluxe AND Rooms/Description:"city view"

můžete získat hotely zpět, kde jeden pokoj je deluxe, a jiný pokoj uvádí "pohled na město" v popisu. Například níže uvedený `Id` dokument `1` s by odpovídaly dotazu:

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

Důvodem je, že `Rooms/Type` odkazuje na všechny `Rooms/Type` analyzované termíny pole v `Rooms/Description`celém dokumentu, a podobně pro , jak je znázorněno v následujících tabulkách.

Jak `Rooms/Type` se ukládá pro fulltextové vyhledávání:

| Termín v`Rooms/Type` | ID dokumentů |
| --- | --- |
| Deluxe | 1, 2 |
| Standardní | 1 |

Jak `Rooms/Description` se ukládá pro fulltextové vyhledávání:

| Termín v`Rooms/Description` | ID dokumentů |
| --- | --- |
| Nádvoří | 2 |
| city | 1 |
| Zahrada | 1 |
| Velké | 1 |
| Motel | 2 |
| Pokoj | 1, 2 |
| Standardní | 1 |
| Suite | 1 |
| Prohlédni | 1 |

Takže na rozdíl od filtru výše, který v `Type` podstatě říká, že "zápas `BaseRate` dokumenty, kde místnost má rovna 'Deluxe `Rooms/Type` pokoj' a že `Rooms/Description` **stejný pokoj** má méně než 100", vyhledávací dotaz říká, že "zápas dokumenty, kde má termín "deluxe" a má frázi "město view". Neexistuje žádný koncept jednotlivých místností, jejichž pole mohou být v druhém případě korelována.

> [!NOTE]
> Pokud chcete, aby podpora korelovaného vyhledávání byla přidána do Azure Cognitive Search, hlasujte pro [tuto položku Hlas uživatele](https://feedback.azure.com/forums/263029-azure-search/suggestions/37735060-support-correlated-search-on-complex-collections).

## <a name="inverted-indexes-and-collections"></a>Invertované indexy a kolekce

Možná jste si všimli, že existuje mnohem méně omezení lambda výrazy přes `Collection(Edm.Int32)`složité `Collection(Edm.GeographyPoint)`kolekce, než jsou pro jednoduché kolekce jako , , a tak dále. Důvodem je, že Azure Cognitive Search ukládá komplexní kolekce jako skutečné kolekce dílčích dokumentů, zatímco jednoduché kolekce nejsou uloženy jako kolekce vůbec.

Zvažte například filtrovatelné pole `seasons` kolekce řetězců jako v indexu pro online prodejce. Některé dokumenty nahrané do tohoto indexu mohou vypadat takto:

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

Hodnoty `seasons` pole jsou uloženy ve struktuře nazývané **invertovaný index**, který vypadá přibližně takto:

| Označení | ID dokumentů |
| --- | --- |
| Jaro | 1, 2 |
| Letní | 1 |
| Podzim | 1, 2 |
| Zimní | 2, 3 |

Tato datová struktura je navržena tak, aby odpověděla na jednu otázku s velkou rychlostí: V jakých dokumentech se daný termín objevuje? Odpověď na tuto otázku funguje spíše jako kontrola prosté rovnosti než smyčka přes kolekci. Ve skutečnosti to je důvod, proč pro kolekce `eq` řetězců Azure Cognitive Search umožňuje `any`pouze jako operátor porovnání uvnitř výrazu lambda pro .

Budování z rovnosti, dále se podíváme na to, jak je možné kombinovat více `or`kontrol rovnosti na stejné proměnné rozsahu s . Funguje díky algebře a [distribuční vlastnosti kvantifikátorů](https://en.wikipedia.org/wiki/Existential_quantification#Negation). Tento výraz:

    seasons/any(s: s eq 'winter' or s eq 'fall')

odpovídá:

    seasons/any(s: s eq 'winter') or seasons/any(s: s eq 'fall')

a každý ze `any` dvou dílčích výrazů lze efektivně provést pomocí invertovaného indexu. Také díky [negaci zákona kvantifikátorů](https://en.wikipedia.org/wiki/Existential_quantification#Negation), tento výraz:

    seasons/all(s: s ne 'winter' and s ne 'fall')

odpovídá:

    not seasons/any(s: s eq 'winter' or s eq 'fall')

což je důvod, proč `all` je `ne` `and`možné použít s a .

> [!NOTE]
> Přestože podrobnosti jsou nad rámec tohoto dokumentu, tyto stejné principy se vztahují i na [testy vzdálenosti a průsečíku pro sbírky geoprostorových bodů.](search-query-odata-geo-spatial-functions.md) To je důvod, proč v `any`:
>
> - `geo.intersects`nelze negovat
> - `geo.distance`musí být porovnány s použitím `lt` nebo`le`
> - výrazy musí být `or`kombinovány s , nikoli`and`
>
> Platí pro `all`pravidla pro konverzaci .

Širší škála výrazů je povolena při filtrování kolekcí datových `gt` `le`typů, `ge` které podporují `Collection(Edm.Int32)` `lt`, , a operátory, například. Konkrétně můžete použít `and` i `or` v `any`aplikaci , pokud jsou základní srovnávací výrazy `and` **kombinovány** do porovnání rozsahu `or`pomocí , které jsou pak dále kombinovány pomocí . Tato struktura logických výrazů se nazývá [Disjunktivní normální tvar (DNF),](https://en.wikipedia.org/wiki/Disjunctive_normal_form)jinak známý jako "ORs ANDs". Naopak lambda výrazy `all` pro tyto datové typy musí být v [konjunktivní normální formě (CNF)](https://en.wikipedia.org/wiki/Conjunctive_normal_form), jinak známý jako "ANDs nejvzdálenějších odls.o". Azure Cognitive Search umožňuje takové porovnání rozsahu, protože je můžete spustit pomocí invertovaných indexů efektivně, stejně jako to může provést rychlé vyhledávání termínů pro řetězce.

Stručně řečeno, zde jsou pravidla pro to, co je povoleno ve výrazu lambda:

- Uvnitř `any`jsou vždy *povoleny pozitivní kontroly,* jako `geo.intersects`je `geo.distance` rovnost, `lt` `le` porovnání rozsahu , nebo ve srovnání s nebo (představte si "blízkost" jako rovnost, pokud jde o kontrolu vzdálenosti).
- Uvnitř `any` `or` , je vždy povoleno. Můžete použít `and` pouze pro datové typy, které mohou vyjádřit kontroly rozsahu a pouze v případě, že používáte nejvzdálenější chod and (DNF).
- Uvnitř `all`jsou pravidla stornována – jsou povoleny pouze *záporné kontroly,* můžete je použít `and` vždy a můžete je použít `or` pouze pro kontroly rozsahu vyjádřené jako AND operačních přístrojů (CNF).

V praxi se jedná o typy filtrů, které budete s největší pravděpodobností používat stejně. Je to stále užitečné pochopit hranice toho, co je možné ačkoli.

Konkrétní příklady, které typy filtrů jsou povoleny a které ne, naleznete v tématu [Jak napsat platné filtry kolekce](search-query-troubleshoot-collection-filters.md#bkmk_examples).

## <a name="next-steps"></a>Další kroky  

- [Poradce při potížích s filtry kolekce OData v Azure Cognitive Search](search-query-troubleshoot-collection-filters.md)
- [Filtry v Azure Cognitive Search](search-filters.md)
- [Přehled jazyka OData pro Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Odkaz na syntaxi výrazu OData pro Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [Hledání dokumentů &#40;azure kognitivní vyhledávání rozhraní API rozhraní&#41;API](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
