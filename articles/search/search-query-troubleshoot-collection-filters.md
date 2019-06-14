---
title: Řešení potíží s filtry kolekcí OData – Azure Search
description: Řešení potíží s chybami filtr kolekce OData v dotazech Azure Search.
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
ms.openlocfilehash: c7fa00c82eea03a50bae22fcb1ad16e230aa5bcb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079623"
---
# <a name="troubleshooting-odata-collection-filters-in-azure-search"></a>Řešení potíží s filtry kolekcí OData ve službě Azure Search

K [filtr](query-odata-filter-orderby-syntax.md) v kolekci polí ve službě Azure Search, můžete použít [ `any` a `all` operátory](search-query-odata-collection-operators.md) spolu s **výrazy lambda**. Výraz lambda je dílčí filtr, který se aplikuje na každý prvek kolekce.

Ne všechny funkce, výrazu filtru je k dispozici uvnitř výrazu lambda. Které funkce jsou dostupné se liší v závislosti na datový typ pole kolekce, kterou chcete filtrovat. Pokud se pokusíte použít funkci ve výrazu lambda, který není podporován v tomto kontextu, to může vést k chybě. Pokud máte podobné chyby při pokusu o zápis složitějších filtrů v kolekci polí, tento článek vám pomůže vyřešit problém.

## <a name="common-collection-filter-errors"></a>Běžné chyby filtr kolekce

V následující tabulce jsou uvedeny chyby, které se můžete setkat při pokusu o provedení filtr kolekce. Tyto chyby dojít, když použijete funkci filtru výrazů, který není podporován uvnitř výrazu lambda. Všechny chyby poskytuje některé pokyny, jak je možné přepsat filtr, aby nedošlo k chybě. V tabulce také obsahuje odkaz do příslušné části tohoto článku, který poskytuje další informace o tom, jak se vyhnout této chybě.

| Chybová zpráva | Situace | Další informace najdete v tématu |
| --- | --- | --- |
| Funkce 'ismatch' nemá žádné parametry vázán na proměnnou rozsahu '. Vázaný jenom pole, které jsou podporovány odkazy uvnitř výrazů lambda ("jakýkoli" nebo "vše"). Změnit filtr, aby funkce 'ismatch' je mimo výraz lambda a zkuste to znovu. | Pomocí `search.ismatch` nebo `search.ismatchscoring` uvnitř výrazu lambda | [Pravidla pro filtrování komplexní kolekce](#bkmk_complex) |
| Neplatný lambda výraz. Najít test a zjistí rovnost či nerovnost, kde byl očekáván opak ve výrazu lambda, který Iteruje přes pole typu Collection(Edm.String). Pro "libovolné" použijte prosím výrazy formuláře 'x eq y' nebo "search.in(...)". Pro: all, použijte prosím výrazy formuláře 'x ne y', 'ne (x eq y)"nebo"není search.in(...)". | Filtrování podle určitého pole typu `Collection(Edm.String)` | [Pravidla pro filtrování kolekce řetězců](#bkmk_strings) |
| Neplatný lambda výraz. Nalezena nepodporovaná formu komplexní logický výraz. Pro "libovolné" použijte prosím výrazů, které jsou "Or z operátoru and", označované také jako disjunktivní normální formuláře. Příklad: '(a and b) nebo (c a d)' a, b, c a d jsou porovnání rovnosti dílčích výrazů. Pro: all, použijte prosím výrazů, které jsou "a z or', označované také jako pojivové normální formuláře. Příklad: '(a or b) a (c nebo d)' a, b, c a d jsou dílčí výrazy porovnání nebo nerovnost. Příklady výrazů porovnání: "x gt 5', ' x le 2. Příklad výrazu rovnosti: "x eq 5'. Příklad výraz nerovnosti: "x ne 5'. | Filtrování na pole typu `Collection(Edm.DateTimeOffset)`, `Collection(Edm.Double)`, `Collection(Edm.Int32)`, nebo `Collection(Edm.Int64)` | [Pravidla pro filtrování srovnatelné kolekce](#bkmk_comparables) |
| Neplatný lambda výraz. Nepodporované použití geo.distance() nebo geo.intersects() nalezen ve výrazu lambda, který Iteruje přes pole typu Collection(Edm.GeographyPoint). Pro "libovolné" Ujistěte se, že porovnání geo.distance() pomocí operátorů "lt" nebo "le" a ujistěte se, že se veškeré využití nad geo.intersects() negovat. Pro: all, ujistěte se, že porovnání geo.distance() pomocí operátorů "gt" nebo 'ge' a ujistěte se, že je veškeré využití nad geo.intersects() negovat. | Filtrování podle určitého pole typu `Collection(Edm.GeographyPoint)` | [Pravidla pro filtrování kolekce GeographyPoint](#bkmk_geopoints) |
| Neplatný lambda výraz. Komplexní logické výrazy nejsou podporovány v lambda výrazech, které iterují v poli typu Collection(Edm.GeographyPoint). Pro "libovolné", připojte se k dílčí výrazy s 'nebo'; "a" není podporován. Pro: all, připojte se k dílčí výrazy s 'a'; nebo se nepodporuje. | Filtrování na pole typu `Collection(Edm.String)` nebo `Collection(Edm.GeographyPoint)` | [Pravidla pro filtrování kolekce řetězců](#bkmk_strings) <br/><br/> [Pravidla pro filtrování kolekce GeographyPoint](#bkmk_geopoints) |
| Neplatný lambda výraz. Najít operátor porovnání (jedna z "lt", "le", "gt" nebo "ge"). V lambda výrazech, které iteraci přes pole typu Collection(Edm.String) jsou povoleny pouze operátory rovnosti. Pro "libovolné" použijte prosím výrazy ve formátu "y x eq". Pro: all, použijte prosím výrazy formuláře "x ne y" nebo "není (x eq y)". | Filtrování podle určitého pole typu `Collection(Edm.String)` | [Pravidla pro filtrování kolekce řetězců](#bkmk_strings) |

<a name="bkmk_examples"></a>

## <a name="how-to-write-valid-collection-filters"></a>Tom, jak psát platné kolekce filtrů

Pravidla pro platné kolekce filtrů zápisu se liší pro jednotlivé datové typy. Pravidla zobrazením příklady, které filtr podporují a které nejsou v následujících částech:

- [Pravidla pro filtrování kolekce řetězců](#bkmk_strings)
- [Pravidla pro filtrování logické kolekce](#bkmk_bools)
- [Pravidla pro filtrování kolekce GeographyPoint](#bkmk_geopoints)
- [Pravidla pro filtrování srovnatelné kolekce](#bkmk_comparables)
- [Pravidla pro filtrování komplexní kolekce](#bkmk_complex)

<a name="bkmk_strings"></a>

## <a name="rules-for-filtering-string-collections"></a>Pravidla pro filtrování kolekce řetězců

Ve výrazech lambda pro kolekce řetězců, jsou pouze relační operátory, které je možné `eq` a `ne`.

> [!NOTE]
> Služba Azure Search nepodporuje `lt` / `le` / `gt` / `ge` operátory pro řetězce, zda uvnitř nebo vně výraz lambda.

Text `any` lze pouze testovat rovnost při text `all` pouze otestovat nerovnost.

Je také možné kombinovat více výrazů prostřednictvím `or` v těle `any`a přes `and` v těle `all`. Protože `search.in` funkce je ekvivalentní ke kombinování kontroly rovnosti s `or`, je také povolena v těle `any`. Naopak `not search.in` je povolen v těle `all`.

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

## <a name="rules-for-filtering-boolean-collections"></a>Pravidla pro filtrování logické kolekce

Typ `Edm.Boolean` podporuje pouze `eq` a `ne` operátory. V důsledku toho to moc nedává smysl pro umožňují sloučení těchto klauzule, které kontrolují proměnnou rozsahu s `and` / `or` vzhledem k tomu, který povede vždycky k tautologies nebo kontradikcí.

Tady jsou některé příklady filtrů na logická kolekce, které jsou povolené:

- `flags/any(f: f)`
- `flags/all(f: f)`
- `flags/any(f: f eq true)`
- `flags/any(f: f ne true)`
- `flags/all(f: not f)`
- `flags/all(f: not (f eq true))`

Na rozdíl od kolekcí řetězec logická kolekce mají žádná omezení, na kterých lze použít operátor v typu výrazu lambda. Obě `eq` a `ne` lze použít v těle `any` nebo `all`.

Výrazy, například následující nejsou povoleny pro logické kolekce:

- `flags/any(f: f or not f)`
- `flags/any(f: f or f)`
- `flags/all(f: f and not f)`
- `flags/all(f: f and f eq true)`

<a name="bkmk_geopoints"></a>

## <a name="rules-for-filtering-geographypoint-collections"></a>Pravidla pro filtrování kolekce GeographyPoint

Hodnoty typu `Edm.GeographyPoint` v kolekci nelze porovnat přímo k sobě navzájem. Místo toho je nutné použít jako parametry `geo.distance` a `geo.intersects` funkce. `geo.distance` Funkce pak musí být ve srovnání s vzdálenost hodnotu pomocí jednoho z operátorů porovnání `lt`, `le`, `gt`, nebo `ge`. Tato pravidla platí také pro pole Edm.GeographyPoint jiné kolekce.

Kolekce řetězců, jako jsou `Edm.GeographyPoint` kolekce mají některá pravidla, jak můžete použít a kombinovat v různých typech výrazů lambda geoprostorové funkce:

- Jaké operátory porovnání můžete používat `geo.distance` funkce závisí na typu výrazu lambda. Pro `any`, můžete použít pouze `lt` nebo `le`. Pro `all`, můžete použít pouze `gt` nebo `ge`. Můžete negate – výrazy zahrnující `geo.distance`, ale budete muset změnit operátor porovnání (`geo.distance(...) lt x` stane `not (geo.distance(...) ge x)` a `geo.distance(...) le x` stane `not (geo.distance(...) gt x)`).
- V těle `all`, `geo.intersects` funkce musí být negovat. Naopak v těle `any`, `geo.intersects` funkce nesmí bude negovat.
- V těle `any`, geoprostorové výrazy lze spojovat pomocí `or`. V těle `all`, mohou být kombinovány tyto výrazy pomocí `and`.

Výše uvedené omezení existovat důvodů podobné jako omezení rovnosti nebo nerovnosti na kolekce řetězců. Zobrazit [filtry kolekcí OData Principy ve službě Azure Search](search-query-understand-collection-filters.md) hlubší rozbor z těchto důvodů.

Tady je několik příkladů filtry na `Edm.GeographyPoint` kolekce, které jsou povolené:

- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: not (geo.distance(l, geography'POINT(-122 49)') ge 10) or geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') ge 10 and not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

Výrazy, jako je například následující nejsou povoleny pro `Edm.GeographyPoint` kolekce:

- `locations/any(l: l eq geography'POINT(-122 49)')`
- `locations/any(l: not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') gt 10)`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10 and geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') le 10 or not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

<a name="bkmk_comparables"></a>

## <a name="rules-for-filtering-comparable-collections"></a>Pravidla pro filtrování srovnatelné kolekce

Tato část platí pro všechny následující typy dat:

- `Collection(Edm.DateTimeOffset)`
- `Collection(Edm.Double)`
- `Collection(Edm.Int32)`
- `Collection(Edm.Int64)`

Typy, jako `Edm.Int32` a `Edm.DateTimeOffset` podporu všech šest operátory porovnání: `eq`, `ne`, `lt`, `le`, `gt`, a `ge`. Lambda výrazy nad kolekcí z těchto typů mohou obsahovat jednoduché výrazy pomocí kteréhokoli z těchto operátorů. To platí pro obě `any` a `all`. Například jsou povolené tyto filtry:

- `ratings/any(r: r ne 5)`
- `dates/any(d: d gt 2017-08-24T00:00:00Z)`
- `not margins/all(m: m eq 3.5)`

Nicméně existují omezení jak tyto výrazy porovnání mohou být kombinovány do složitější výrazy uvnitř výrazu lambda:

- Pravidla pro `any`:
  - Jednoduché nerovnost výrazy nelze kombinovat s jinými výrazy úspěšně. Například je povolený tento výraz:
    - `ratings/any(r: r ne 5)`

    ale není tento výraz:
    - `ratings/any(r: r ne 5 and r gt 2)`

    a tento výraz je povoleno, ale nejsou užitečné, protože překrývat podmínky:
    - `ratings/any(r: r ne 5 or r gt 7)`
  - Jednoduché porovnání výrazy zahrnující `eq`, `lt`, `le`, `gt`, nebo `ge` zkombinovat s `and` / `or`. Příklad:
    - `ratings/any(r: r gt 2 and r le 5)`
    - `ratings/any(r: r le 5 or r gt 7)`
  - Porovnání výrazů v kombinaci s `and` (spojky) mohou být kombinovány další, pomocí `or`. Tento formulář je znám v logický operátor jako "[disjunktivní formuláře normální](https://en.wikipedia.org/wiki/Disjunctive_normal_form)" (DNF). Příklad:
    - `ratings/any(r: (r gt 2 and r le 5) or (r gt 7 and r lt 10))`
- Pravidla pro `all`:
  - Výrazy rovnosti jednoduché nelze kombinovat s jinými výrazy úspěšně. Například je povolený tento výraz:
    - `ratings/all(r: r eq 5)`

    ale není tento výraz:
    - `ratings/all(r: r eq 5 or r le 2)`

    a tento výraz je povoleno, ale nejsou užitečné, protože překrývat podmínky:
    - `ratings/all(r: r eq 5 and r le 7)`
  - Jednoduché porovnání výrazy zahrnující `ne`, `lt`, `le`, `gt`, nebo `ge` zkombinovat s `and` / `or`. Příklad:
    - `ratings/all(r: r gt 2 and r le 5)`
    - `ratings/all(r: r le 5 or r gt 7)`
  - Porovnání výrazů v kombinaci s `or` (disjunctions) mohou být kombinovány další, pomocí `and`. Tento formulář je znám v logický operátor jako "[pojivové formuláře normální](https://en.wikipedia.org/wiki/Conjunctive_normal_form)" (možností CNF). Příklad:
    - `ratings/all(r: (r le 2 or gt 5) and (r lt 7 or r ge 10))`

<a name="bkmk_complex"></a>

## <a name="rules-for-filtering-complex-collections"></a>Pravidla pro filtrování komplexní kolekce

Výrazy lambda přes komplexní kolekce podporuje mnohem flexibilnější syntaxi než lambda výrazy nad kolekcí primitivních typů. Můžete použít libovolný filtr konstrukce uvnitř takový výraz lambda, které můžete použít mimo jeden s pouze dvěma výjimkami.

Funkce First, `search.ismatch` a `search.ismatchscoring` nejsou podporovány uvnitř výrazů lambda. Další informace najdete v tématu [filtry kolekcí OData Principy ve službě Azure Search](search-query-understand-collection-filters.md).

Za druhé, odkazuje na pole, která nejsou *vázán* k proměnné rozsahu (takzvané *zdarma proměnné*) není povolený. Představte si třeba následující dva ekvivalentní OData výrazů filtru:

1. `stores/any(s: s/amenities/any(a: a eq 'parking')) and details/margin gt 0.5`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and details/margin gt 0.5))`

První výraz, který bude možné, zatímco druhý formulář odmítne protože `details/margin` není vázán na proměnnou rozsahu `s`.

Toto pravidlo se také rozšiřuje na výrazy, které mají proměnné hranice ve vnějším oboru. Tyto proměnné jsou zdarma s ohledem na obor, ve kterém jsou zobrazeny. Například je může první výraz, zatímco druhý ekvivalentní výraz není povolená, protože `s/name` je zdarma s ohledem na rozsah proměnné rozsahu `a`:

1. `stores/any(s: s/amenities/any(a: a eq 'parking') and s/name ne 'Flagship')`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and s/name ne 'Flagship'))`

Toto omezení by neměl být problém v praxi, protože je vždy možné sestrojit filtry tak, že lambda výrazy obsahovat pouze vazby proměnné.

## <a name="cheat-sheet-for-collection-filter-rules"></a>Tahák pro kolekci pravidel filtru

Následující tabulka shrnuje pravidla pro vytváření platné filtry pro každý typ dat kolekce.

[!INCLUDE [Limitations on OData lambda expressions in Azure Search](../../includes/search-query-odata-lambda-limitations.md)]

Příklady konstruování platné filtry pro každý případ, najdete v článku [jak platnou kolekci filtrů zápisu](#bkmk_examples).

Pokud jste filtry zápisu často, a Principy pravidel od první zásad vám pomohou více než jen memorizing, najdete v části [filtry kolekcí OData Principy ve službě Azure Search](search-query-understand-collection-filters.md).

## <a name="next-steps"></a>Další postup  

- [Principy filtrů kolekce OData ve službě Azure Search](search-query-understand-collection-filters.md)
- [Filtry ve službě Azure Search](search-filters.md)
- [Přehled o výraz jazyka OData pro službu Azure Search](query-odata-filter-orderby-syntax.md)
- [Reference k syntaxi výrazů OData pro službu Azure Search](search-query-odata-syntax-reference.md)
- [Hledání dokumentů &#40;rozhraní REST API služby Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
