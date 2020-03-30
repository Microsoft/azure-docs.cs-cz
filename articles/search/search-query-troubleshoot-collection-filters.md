---
title: Poradce při potížích s filtry kolekce OData
titleSuffix: Azure Cognitive Search
description: Naučte se přístupy k řešení chyb filtru kolekce OData v dotazech Azure Cognitive Search.
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
ms.openlocfilehash: e82fa00226c964d5ba774cdf06f5b0f3898bdc55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113090"
---
# <a name="troubleshooting-odata-collection-filters-in-azure-cognitive-search"></a>Poradce při potížích s filtry kolekce OData v Azure Cognitive Search

Chcete-li [filtrovat](query-odata-filter-orderby-syntax.md) na sběrných polích v Azure Cognitive Search, můžete použít [ `any` a `all` operátory](search-query-odata-collection-operators.md) spolu s **lambda výrazy**. Výraz lambda je dílčí filtr, který se použije pro každý prvek kolekce.

Ne všechny funkce výrazů filtru jsou k dispozici uvnitř výrazu lambda. Funkce, které jsou k dispozici, se liší v závislosti na datovém typu sběrného pole, které chcete filtrovat. To může mít za následek chybu, pokud se pokusíte použít funkci ve výrazu lambda, který není v tomto kontextu podporován. Pokud dochází k těmto chybám při pokusu o zápis složitého filtru přes sběrná pole, tento článek vám pomůže problém vyřešit.

## <a name="common-collection-filter-errors"></a>Běžné chyby filtru kolekce

V následující tabulce jsou uvedeny chyby, ke kterým může dojít při pokusu o spuštění filtru kolekce. K těmto chybám dochází, když použijete funkci výrazů filtru, která není podporována uvnitř výrazu lambda. Každá chyba poskytuje některé pokyny, jak můžete přepsat filtr, aby se zabránilo chybě. Tabulka také obsahuje odkaz na příslušnou část tohoto článku, která obsahuje další informace o tom, jak se této chybě vyhnout.

| Chybová zpráva | Situaci | Další informace najdete v tématu |
| --- | --- | --- |
| Funkce 'ismatch' nemá žádné parametry vázané na proměnnou rozsahu 's'. Uvnitř výrazů lambda ("any" nebo "all" jsou podporovány pouze odkazy na vázané pole). Změňte filtr tak, aby funkce ismatch byla mimo výraz lambda, a akci opakujte. | Použití `search.ismatch` `search.ismatchscoring` výrazu lambda nebo uvnitř | [Pravidla pro filtrování složitých kolekcí](#bkmk_complex) |
| Neplatný výraz lambda. Byl nalezen test rovnosti nebo nerovnosti, kde byl očekáván opak ve výrazu lambda, který itezuje pole typu Collection(Edm.String). Pro "any" použijte výrazy formuláře "x eq y" nebo "search.in(...)". Pro "vše" použijte výrazy formuláře "x ne y", "not (x eq y)" nebo "not search.in(...)". | Filtrování na poli typu`Collection(Edm.String)` | [Pravidla pro filtrování kolekcí řetězců](#bkmk_strings) |
| Neplatný výraz lambda. Byla nalezena nepodporovaná forma složitého logického výrazu. Pro "any" použijte výrazy, které jsou "nejvzdálenějšími andy", známé také jako disjunktivní normální tvar. Například: '(a a b) nebo (c a d)" kde a, b, c a d jsou podvýrazy porovnání nebo rovnosti. Pro "vše" použijte výrazy, které jsou "AND s nejvzdálenějšími a konzumními", známé také jako konjunktivní normální tvar. Například: '(a nebo b) a (c nebo d)" kde a, b, c a d jsou podvýrazy porovnání nebo nerovnosti. Příklady porovnávacích výrazů: 'x gt 5', 'x le 2'. Příklad výrazu rovnosti: "x eq 5". Příklad výrazu nerovnosti: 'x ne 5'. | Filtrování polí typu `Collection(Edm.DateTimeOffset)`, `Collection(Edm.Double)` `Collection(Edm.Int32)`, , nebo`Collection(Edm.Int64)` | [Pravidla pro filtrování srovnatelných kolekcí](#bkmk_comparables) |
| Neplatný výraz lambda. Nalezeno nepodporované použití geo.distance() nebo geo.intersects() ve výrazu lambda, který itezuje přes pole typu Collection(Edm.GeographyPoint). Pro 'any', ujistěte se, že porovnat geo.distance() pomocí 'lt' nebo 'le' operátory a ujistěte se, že žádné použití geo.intersects() není negována. U "all" se ujistěte, že porovnáváte geo.distance() pomocí operátorů 'gt' nebo 'ge' a ujistěte se, že je negováno jakékoli použití geo.intersects(). | Filtrování na poli typu`Collection(Edm.GeographyPoint)` | [Pravidla pro filtrování kolekcí GeographyPoint](#bkmk_geopoints) |
| Neplatný výraz lambda. Komplexní logické výrazy nejsou podporovány ve výrazech lambda, které iterát přes pole typu Collection(Edm.GeographyPoint). Pro "any" se prosím připojte k dílčím výrazům s "nebo"; 'a' není podporována. Pro "vše" se prosím připojte k dílčím výrazům s "a"; 'nebo' není podporována. | Filtrování polí typu `Collection(Edm.String)` nebo`Collection(Edm.GeographyPoint)` | [Pravidla pro filtrování kolekcí řetězců](#bkmk_strings) <br/><br/> [Pravidla pro filtrování kolekcí GeographyPoint](#bkmk_geopoints) |
| Neplatný výraz lambda. Byl nalezen operátor porovnání (jeden z "lt", 'le', 'gt' nebo 'ge'). Pouze operátory rovnosti jsou povoleny v lambda výrazy, které iterate přes pole typu Collection(Edm.String). Pro "any" použijte výrazy formuláře "x eq y". Pro "vše" použijte výrazy formuláře "x ne y" nebo "not (x eq y)". | Filtrování na poli typu`Collection(Edm.String)` | [Pravidla pro filtrování kolekcí řetězců](#bkmk_strings) |

<a name="bkmk_examples"></a>

## <a name="how-to-write-valid-collection-filters"></a>Jak psát platné filtry kolekce

Pravidla pro psaní platných filtrů kolekce se liší pro každý datový typ. Následující části popisují pravidla tím, že ukazují příklady, které funkce filtru jsou podporovány a které nejsou:

- [Pravidla pro filtrování kolekcí řetězců](#bkmk_strings)
- [Pravidla pro filtrování logických kolekcí](#bkmk_bools)
- [Pravidla pro filtrování kolekcí GeographyPoint](#bkmk_geopoints)
- [Pravidla pro filtrování srovnatelných kolekcí](#bkmk_comparables)
- [Pravidla pro filtrování složitých kolekcí](#bkmk_complex)

<a name="bkmk_strings"></a>

## <a name="rules-for-filtering-string-collections"></a>Pravidla pro filtrování kolekcí řetězců

Uvnitř lambda výrazy pro kolekce řetězců, pouze operátory `eq` `ne`porovnání, které lze použít, jsou a .

> [!NOTE]
> Azure Cognitive Search nepodporuje operátory `lt` / `le` / `gt` / `ge` pro řetězce, ať už uvnitř nebo vně výraz lambda.

Tělo může `any` pouze test rovnosti, zatímco tělo `all` těla může pouze test nerovnosti.

Je také možné kombinovat více výrazů `or` prostřednictvím `any`v těle `and` , a `all`prostřednictvím v těle . Vzhledem `search.in` k tomu, že funkce `or`je ekvivalentní kombinování kontrol rovnosti `any`s , je také povoleno v těle . Naopak, `not search.in` je povoleno v těle `all`.

Například tyto výrazy jsou povoleny:

- `tags/any(t: t eq 'books')`
- `tags/any(t: search.in(t, 'books, games, toys'))`
- `tags/all(t: t ne 'books')`
- `tags/all(t: not (t eq 'books'))`
- `tags/all(t: not search.in(t, 'books, games, toys'))`
- `tags/any(t: t eq 'books' or t eq 'games')`
- `tags/all(t: t ne 'books' and not (t eq 'games'))`

zatímco tyto výrazy nejsou povoleny:

- `tags/any(t: t ne 'books')`
- `tags/any(t: not search.in(t, 'books, games, toys'))`
- `tags/all(t: t eq 'books')`
- `tags/all(t: search.in(t, 'books, games, toys'))`
- `tags/any(t: t eq 'books' and t ne 'games')`
- `tags/all(t: t ne 'books' or not (t eq 'games'))`

<a name="bkmk_bools"></a>

## <a name="rules-for-filtering-boolean-collections"></a>Pravidla pro filtrování logických kolekcí

Typ `Edm.Boolean` podporuje pouze `eq` `ne` a operátory. Jako takový, to nedává moc smysl, aby kombinování těchto doložek, `and` / `or` které kontrolují stejný rozsah proměnné s tím, protože to by vždy vedlo k tautologie nebo rozpory.

Zde jsou některé příklady filtrů v logických kolekcích, které jsou povoleny:

- `flags/any(f: f)`
- `flags/all(f: f)`
- `flags/any(f: f eq true)`
- `flags/any(f: f ne true)`
- `flags/all(f: not f)`
- `flags/all(f: not (f eq true))`

Na rozdíl od kolekce řetězců logické kolekce nemají žádná omezení, na kterém operátor lze použít, ve kterém typu výrazu lambda. Oba `eq` `ne` a mohou být použity v těle `any` nebo `all`.

Pro logické kolekce nejsou povoleny následující výrazy:

- `flags/any(f: f or not f)`
- `flags/any(f: f or f)`
- `flags/all(f: f and not f)`
- `flags/all(f: f and f eq true)`

<a name="bkmk_geopoints"></a>

## <a name="rules-for-filtering-geographypoint-collections"></a>Pravidla pro filtrování kolekcí GeographyPoint

Hodnoty typu `Edm.GeographyPoint` v kolekci nelze porovnat přímo k sobě navzájem. Místo toho musí být použity `geo.distance` jako `geo.intersects` parametry a funkce. Funkce `geo.distance` musí být porovnána s hodnotou vzdálenosti pomocí `lt`jednoho `le` `gt`ze `ge`porovnávacích operátorů , , nebo . Tato pravidla platí také pro pole Edm.GeographyPoint bez kolekce.

Stejně jako `Edm.GeographyPoint` kolekce řetězců mají kolekce některá pravidla pro to, jak lze geografické prostorové funkce použít a kombinovat v různých typech výrazů lambda:

- Operátory porovnání, které `geo.distance` můžete použít s funkcí, závisí na typu výrazu lambda. Pro `any`, můžete `lt` použít `le`pouze nebo . Pro `all`, můžete `gt` použít `ge`pouze nebo . Můžete negovat výrazy `geo.distance`zahrnující , ale budete muset změnit`geo.distance(...) lt x` `not (geo.distance(...) ge x)` operátor `geo.distance(...) le x` `not (geo.distance(...) gt x)`porovnání (stane a stane).
- V těle `all`, `geo.intersects` musí být funkce negována. Naopak v těle `any`, `geo.intersects` funkce nesmí být negována.
- V těle `any`, geoprostorové výrazy lze kombinovat `or`pomocí . V těle `all`, tyto výrazy lze kombinovat `and`pomocí .

Výše uvedená omezení existují z podobných důvodů jako omezení rovnosti a nerovnosti na kolekce řetězců. Viz [Principy filtrů kolekce OData v Azure Cognitive Search](search-query-understand-collection-filters.md) pro hlubší pohled na tyto důvody.

Zde jsou některé příklady `Edm.GeographyPoint` filtrů na kolekce, které jsou povoleny:

- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: not (geo.distance(l, geography'POINT(-122 49)') ge 10) or geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') ge 10 and not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

Výrazy, jako jsou následující `Edm.GeographyPoint` nejsou povoleny pro kolekce:

- `locations/any(l: l eq geography'POINT(-122 49)')`
- `locations/any(l: not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') gt 10)`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10 and geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') le 10 or not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

<a name="bkmk_comparables"></a>

## <a name="rules-for-filtering-comparable-collections"></a>Pravidla pro filtrování srovnatelných kolekcí

Tato část se vztahuje na všechny následující datové typy:

- `Collection(Edm.DateTimeOffset)`
- `Collection(Edm.Double)`
- `Collection(Edm.Int32)`
- `Collection(Edm.Int64)`

Typy, `Edm.Int32` jako `Edm.DateTimeOffset` je například a `eq`podporují `ne` `lt`všech `le` `gt`šest `ge`operátorů porovnání: , , , , , a . Lambda výrazy přes kolekce těchto typů může obsahovat jednoduché výrazy pomocí některého z těchto operátorů. To platí pro `any` `all`obě a . Například tyto filtry jsou povoleny:

- `ratings/any(r: r ne 5)`
- `dates/any(d: d gt 2017-08-24T00:00:00Z)`
- `not margins/all(m: m eq 3.5)`

Existují však omezení, jak lze tyto srovnávací výrazy kombinovat do složitějších výrazů uvnitř výrazu lambda:

- Pravidla `any`pro :
  - Jednoduché výrazy nerovnosti nelze užitečně kombinovat s jinými výrazy. Tento výraz je například povolen:
    - `ratings/any(r: r ne 5)`

    ale tento výraz není:
    - `ratings/any(r: r ne 5 and r gt 2)`

    a i když je tento výraz povolen, není užitečný, protože se podmínky překrývají:
    - `ratings/any(r: r ne 5 or r gt 7)`
  - Jednoduché srovnávací výrazy `eq` `lt`zahrnující `le` `gt`, `ge` , , , `and` / `or`nebo lze kombinovat s . Například:
    - `ratings/any(r: r gt 2 and r le 5)`
    - `ratings/any(r: r le 5 or r gt 7)`
  - Srovnávací výrazy v `and` kombinaci s (spojky) `or`lze dále kombinovat pomocí . Tento formulář je v logické logice znám jako "[Disjunctive Normal Form](https://en.wikipedia.org/wiki/Disjunctive_normal_form)" (DNF). Například:
    - `ratings/any(r: (r gt 2 and r le 5) or (r gt 7 and r lt 10))`
- Pravidla `all`pro :
  - Jednoduché výrazy rovnosti nelze užitečně kombinovat s jinými výrazy. Tento výraz je například povolen:
    - `ratings/all(r: r eq 5)`

    ale tento výraz není:
    - `ratings/all(r: r eq 5 or r le 2)`

    a i když je tento výraz povolen, není užitečný, protože se podmínky překrývají:
    - `ratings/all(r: r eq 5 and r le 7)`
  - Jednoduché srovnávací výrazy `ne` `lt`zahrnující `le` `gt`, `ge` , , , `and` / `or`nebo lze kombinovat s . Například:
    - `ratings/all(r: r gt 2 and r le 5)`
    - `ratings/all(r: r le 5 or r gt 7)`
  - Srovnávací výrazy v `or` kombinaci s (disjunkce) `and`lze dále kombinovat pomocí . Tento formulář je v logické logice znám jako "[Konjunktivní normální tvar](https://en.wikipedia.org/wiki/Conjunctive_normal_form)" (CNF). Například:
    - `ratings/all(r: (r le 2 or gt 5) and (r lt 7 or r ge 10))`

<a name="bkmk_complex"></a>

## <a name="rules-for-filtering-complex-collections"></a>Pravidla pro filtrování složitých kolekcí

Lambda výrazy přes složité kolekce podporují mnohem pružnější syntaxi než lambda výrazy přes kolekce primitivní typy. Můžete použít libovolný filtr konstrukce uvnitř takový výraz lambda, který můžete použít mimo jeden, pouze dvě výjimky.

Za prvé, `search.ismatch` `search.ismatchscoring` funkce a nejsou podporovány uvnitř lambda výrazy. Další informace najdete [v tématu Principy filtrů kolekce OData v Azure Cognitive Search](search-query-understand-collection-filters.md).

Za druhé, odkazování na pole, která nejsou *vázána* na proměnnou rozsahu (takzvané *proměnné zdarma),* není povoleno. Zvažte například následující dva ekvivalentní výrazy filtru OData:

1. `stores/any(s: s/amenities/any(a: a eq 'parking')) and details/margin gt 0.5`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and details/margin gt 0.5))`

První výraz bude povolen, zatímco druhý formulář `details/margin` bude odmítnut, protože není `s`vázán na proměnnou rozsahu .

Toto pravidlo se také vztahuje na výrazy, které mají proměnné vázané ve vnějším oboru. Tyto proměnné jsou zdarma, pokud jde o rozsah, ve kterém se objevují. Například první výraz je povolen, zatímco druhý ekvivalentní výraz `s/name` není povolen, protože je volný `a`s ohledem na rozsah proměnné rozsahu :

1. `stores/any(s: s/amenities/any(a: a eq 'parking') and s/name ne 'Flagship')`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and s/name ne 'Flagship'))`

Toto omezení by neměl být problém v praxi, protože je vždy možné vytvořit filtry tak, aby výrazy lambda obsahují pouze vázané proměnné.

## <a name="cheat-sheet-for-collection-filter-rules"></a>Tahák pro pravidla filtru kolekce

Následující tabulka shrnuje pravidla pro vytváření platných filtrů pro každý datový typ kolekce.

[!INCLUDE [Limitations on OData lambda expressions in Azure Cognitive Search](../../includes/search-query-odata-lambda-limitations.md)]

Příklady, jak vytvořit platné filtry pro každý případ, naleznete v [tématu Jak napsat platné filtry kolekce](#bkmk_examples).

Pokud často píšete filtry a porozumění pravidlům z prvních zásad by vám pomohlo víc než jen zapamatování, přečtěte si informace [o filtrech kolekce OData v Azure Cognitive Search](search-query-understand-collection-filters.md).

## <a name="next-steps"></a>Další kroky  

- [Principy filtrů kolekce OData v Azure Cognitive Search](search-query-understand-collection-filters.md)
- [Filtry v Azure Cognitive Search](search-filters.md)
- [Přehled jazyka OData pro Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Odkaz na syntaxi výrazu OData pro Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [Hledání dokumentů &#40;azure kognitivní vyhledávání rozhraní API rozhraní&#41;API](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
