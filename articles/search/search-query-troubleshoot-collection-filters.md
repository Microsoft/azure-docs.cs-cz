---
title: Řešení potíží s filtry kolekce OData
titleSuffix: Azure Cognitive Search
description: Seznamte se s postupy pro řešení chyb filtru kolekce OData v dotazech Azure Kognitivní hledání.
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
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113090"
---
# <a name="troubleshooting-odata-collection-filters-in-azure-cognitive-search"></a>Řešení potíží s filtry kolekce OData v Azure Kognitivní hledání

Chcete-li [filtrovat](query-odata-filter-orderby-syntax.md) pole kolekce v Azure kognitivní hledání, můžete použít [operátory`any` a `all`](search-query-odata-collection-operators.md) spolu s **lambda výrazy**. Výraz lambda je dílčí filtr, který je použit pro každý prvek kolekce.

V rámci výrazu lambda nejsou k dispozici všechny funkce výrazů filtru. Které funkce jsou k dispozici, se liší v závislosti na typu dat pole kolekce, které chcete filtrovat. To může mít za následek chybu, pokud se pokusíte použít funkci ve výrazu lambda, který není v tomto kontextu podporován. Pokud se setkáte s takovými chybami při pokusu o zápis složitého filtru přes pole kolekce, Tento článek vám pomůže tento problém vyřešit.

## <a name="common-collection-filter-errors"></a>Chyby filtru běžných kolekcí

V následující tabulce jsou uvedeny chyby, se kterými se můžete setkat při pokusu o spuštění filtru kolekce. K těmto chybám dochází při použití funkce výrazů filtru, které nejsou podporovány uvnitř výrazu lambda. Každá chyba obsahuje pokyny, jak můžete přepsat filtr, abyste se vyhnuli chybám. Tabulka obsahuje také odkaz na relevantní část tohoto článku, která poskytuje další informace o tom, jak se této chybě vyhnout.

| Chybová zpráva | Status | Další informace najdete v tématu |
| --- | --- | --- |
| Funkce ' matchd ' nemá žádné parametry svázané s proměnnou rozsahu ' '. Uvnitř výrazů lambda (any nebo All) jsou podporovány pouze odkazy vázané na pole. Změňte prosím svůj filtr tak, aby byla funkce ' matchd ' mimo výraz lambda a akci opakujte. | Použití `search.ismatch` nebo `search.ismatchscoring` uvnitř výrazu lambda | [Pravidla pro filtrování složitých kolekcí](#bkmk_complex) |
| Neplatný výraz lambda Byl nalezen test rovnosti nebo nerovnosti, kde byl očekáván opak ve výrazu lambda, který se opakuje nad polem typu Collection (EDM. String). Pro Any použijte prosím výrazy ve formátu ' x EQ y ' nebo ' search.in (...) '. Pro All použijte prosím výrazy ve formátu ' x ne y ', ' not (x EQ y) ' nebo ' not search.in (...) '. | Filtrování na poli typu `Collection(Edm.String)` | [Pravidla pro filtrování kolekcí řetězců](#bkmk_strings) |
| Neplatný výraz lambda Našla se Nepodporovaná forma komplexního logického výrazu. Pro klíčové slovo any použijte výrazy, které jsou "ORs of and", označované také jako Disjunctive Normal Form. Například: "(a a b) nebo (c a d)", kde a, b, c a d, jsou porovnání nebo dílčí výrazy rovnosti. Pro All použijte výrazy, které jsou "and of ORs", označované také jako conjunctive Normal Form. Například: "(a nebo b) a (c nebo d)", kde a, b, c a d, jsou porovnání nebo dílčí výrazy nerovnosti. Příklady výrazů porovnání: ' x gt 5 ', ' x Le 2 '. Příklad výrazu rovnosti: ' x EQ 5 '. Příklad výrazu nerovnosti: ' x ne 5 '. | Filtrování polí typu `Collection(Edm.DateTimeOffset)`, `Collection(Edm.Double)`, `Collection(Edm.Int32)`nebo `Collection(Edm.Int64)` | [Pravidla pro filtrování srovnatelných kolekcí](#bkmk_comparables) |
| Neplatný výraz lambda Bylo nalezeno nepodporované použití geografické. Distance () nebo Geo. intersects () ve výrazu lambda, který projde polem typu Collection (EDM. GeographyPoint). U ' Any ' se ujistěte, že porovnáte geografické. Distance () pomocí operátorů ' lt ' nebo ' Le ' a ujistěte se, že jakékoliv použití geo. intersects () není typu negace. Pro All se ujistěte, že porovnáte geografické. Distance () pomocí operátorů gt nebo GE a zajistěte, aby jakékoli použití geograficky. intersects () bylo typu negace. | Filtrování na poli typu `Collection(Edm.GeographyPoint)` | [Pravidla pro filtrování kolekcí GeographyPoint](#bkmk_geopoints) |
| Neplatný výraz lambda Komplexní logické výrazy nejsou podporovány ve výrazech lambda, které se iterují nad poli kolekce typu (EDM. GeographyPoint). Pro klíčové slovo any připojte dílčí výrazy pomocí operátoru OR. a se nepodporuje. Pro možnost All se připojte k dílčím výrazům a. ' or ' není podporován. | Filtrování polí typu `Collection(Edm.String)` nebo `Collection(Edm.GeographyPoint)` | [Pravidla pro filtrování kolekcí řetězců](#bkmk_strings) <br/><br/> [Pravidla pro filtrování kolekcí GeographyPoint](#bkmk_geopoints) |
| Neplatný výraz lambda Byl nalezen relační operátor (jedna z ' lt ', ' Le ', ' gt ' nebo ' GE '). Ve výrazech lambda, které se iterují nad poli typu Collection (EDM. String), jsou povoleny pouze operátory rovnosti. Pro Any použijte prosím výrazy ve formátu x EQ y. Pro All použijte prosím výrazy ve formátu x ne y nebo not (x EQ y). | Filtrování na poli typu `Collection(Edm.String)` | [Pravidla pro filtrování kolekcí řetězců](#bkmk_strings) |

<a name="bkmk_examples"></a>

## <a name="how-to-write-valid-collection-filters"></a>Zápis platných filtrů kolekcí

Pravidla pro zápis platných filtrů kolekcí jsou pro každý datový typ odlišná. Následující části popisují pravidla zobrazením příkladů, které funkce filtru jsou podporovány a které nejsou:

- [Pravidla pro filtrování kolekcí řetězců](#bkmk_strings)
- [Pravidla pro filtrování logických kolekcí](#bkmk_bools)
- [Pravidla pro filtrování kolekcí GeographyPoint](#bkmk_geopoints)
- [Pravidla pro filtrování srovnatelných kolekcí](#bkmk_comparables)
- [Pravidla pro filtrování složitých kolekcí](#bkmk_complex)

<a name="bkmk_strings"></a>

## <a name="rules-for-filtering-string-collections"></a>Pravidla pro filtrování kolekcí řetězců

Uvnitř výrazů lambda pro kolekce řetězců lze použít pouze operátory porovnání, které mohou být použity `eq` a `ne`.

> [!NOTE]
> Azure Kognitivní hledání nepodporuje `lt`/`le`/`gt`/`ge` operátory pro řetězce, ať už uvnitř nebo vně výrazu lambda.

Tělo `any` může testovat pouze rovnost, zatímco tělo `all` může testovat pouze nerovnost.

Je také možné zkombinovat více výrazů prostřednictvím `or` v těle `any`a prostřednictvím `and` v těle `all`. Vzhledem k tomu, že je funkce `search.in` ekvivalentní kombinaci kontrol rovnosti s `or`, je také povoleno v těle `any`. Naopak, `not search.in` je povolená v těle `all`.

Například tyto výrazy jsou povoleny:

- `tags/any(t: t eq 'books')`
- `tags/any(t: search.in(t, 'books, games, toys'))`
- `tags/all(t: t ne 'books')`
- `tags/all(t: not (t eq 'books'))`
- `tags/all(t: not search.in(t, 'books, games, toys'))`
- `tags/any(t: t eq 'books' or t eq 'games')`
- `tags/all(t: t ne 'books' and not (t eq 'games'))`

i když tyto výrazy nejsou povoleny:

- `tags/any(t: t ne 'books')`
- `tags/any(t: not search.in(t, 'books, games, toys'))`
- `tags/all(t: t eq 'books')`
- `tags/all(t: search.in(t, 'books, games, toys'))`
- `tags/any(t: t eq 'books' and t ne 'games')`
- `tags/all(t: t ne 'books' or not (t eq 'games'))`

<a name="bkmk_bools"></a>

## <a name="rules-for-filtering-boolean-collections"></a>Pravidla pro filtrování logických kolekcí

Typ `Edm.Boolean` podporuje pouze operátory `eq` a `ne`. Vzhledem k tomu nezáleží na tom, že umožňuje kombinování takových klauzulí, které kontrolují stejnou proměnnou rozsahu, s `and`/`or`, protože by vždycky vedlo k tautologies nebo konfliktu.

Tady je několik příkladů filtrů na logických kolekcích, které jsou povolené:

- `flags/any(f: f)`
- `flags/all(f: f)`
- `flags/any(f: f eq true)`
- `flags/any(f: f ne true)`
- `flags/all(f: not f)`
- `flags/all(f: not (f eq true))`

Na rozdíl od kolekcí řetězců nemají logické kolekce žádná omezení, které operátor lze použít pro typ výrazu lambda. V těle `any` nebo `all`lze použít jak `eq`, tak `ne`.

Pro logické kolekce nejsou povoleny výrazy, jako jsou následující:

- `flags/any(f: f or not f)`
- `flags/any(f: f or f)`
- `flags/all(f: f and not f)`
- `flags/all(f: f and f eq true)`

<a name="bkmk_geopoints"></a>

## <a name="rules-for-filtering-geographypoint-collections"></a>Pravidla pro filtrování kolekcí GeographyPoint

Hodnoty typu `Edm.GeographyPoint` v kolekci nelze porovnat přímo mezi sebou. Místo toho je třeba je použít jako parametry `geo.distance` a `geo.intersects` funkce. Funkce `geo.distance` je zase nutné porovnat s hodnotou vzdálenosti pomocí jednoho z relačních operátorů `lt`, `le`, `gt`nebo `ge`. Tato pravidla platí také pro pole typu EDM. GeographyPoint, která nejsou kolekcí.

Podobně jako kolekce řetězců mají `Edm.GeographyPoint` kolekce některá pravidla pro použití geograficky funkčních funkcí a jejich kombinování v různých typech výrazů lambda:

- Které operátory porovnávání můžete použít s funkcí `geo.distance` závisí na typu výrazu lambda. Pro `any`můžete použít jenom `lt` nebo `le`. Pro `all`můžete použít jenom `gt` nebo `ge`. Můžete mít výrazy týkající se `geo.distance`, ale budete muset změnit operátor porovnání (`geo.distance(...) lt x` se stane `not (geo.distance(...) ge x)` a `geo.distance(...) le x` se stane `not (geo.distance(...) gt x)`).
- V těle `all`musí být funkce `geo.intersects` negace. Naopak v těle `any`nesmí být funkce `geo.intersects` negace.
- V těle `any`lze pomocí `or`kombinovat geografické prostorové výrazy. V těle `all`lze takové výrazy kombinovat pomocí `and`.

Výše uvedená omezení existují v podobných důvodech jako omezení rovnosti a nerovnosti u kolekcí řetězců. Podrobnější informace najdete v těchto důvodech v tématu [porozumění filtrům kolekce OData v Azure kognitivní hledání](search-query-understand-collection-filters.md) .

Tady jsou některé příklady filtrů pro `Edm.GeographyPoint` kolekce, které jsou povolené:

- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: not (geo.distance(l, geography'POINT(-122 49)') ge 10) or geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') ge 10 and not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

Pro kolekce `Edm.GeographyPoint` nejsou povoleny výrazy, jako jsou následující:

- `locations/any(l: l eq geography'POINT(-122 49)')`
- `locations/any(l: not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') gt 10)`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10 and geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') le 10 or not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

<a name="bkmk_comparables"></a>

## <a name="rules-for-filtering-comparable-collections"></a>Pravidla pro filtrování srovnatelných kolekcí

Tato část se vztahuje na všechny následující typy dat:

- `Collection(Edm.DateTimeOffset)`
- `Collection(Edm.Double)`
- `Collection(Edm.Int32)`
- `Collection(Edm.Int64)`

Typy jako `Edm.Int32` a `Edm.DateTimeOffset` podporují všechny šest z relačních operátorů: `eq`, `ne`, `lt`, `le`, `gt`a `ge`. Výrazy lambda nad kolekcí těchto typů mohou obsahovat jednoduché výrazy pomocí kteréhokoli z těchto operátorů. To platí pro `any` i `all`. Například tyto filtry jsou povolené:

- `ratings/any(r: r ne 5)`
- `dates/any(d: d gt 2017-08-24T00:00:00Z)`
- `not margins/all(m: m eq 3.5)`

Existují však omezení, jak mohou být takové výrazy porovnání kombinovány do složitějších výrazů uvnitř výrazu lambda:

- Pravidla pro `any`:
  - Jednoduché výrazy nerovnosti nelze využít v kombinaci s jinými výrazy. Například tento výraz je povolen:
    - `ratings/any(r: r ne 5)`

    ale tento výraz není:
    - `ratings/any(r: r ne 5 and r gt 2)`

    i když je tento výraz povolený, není užitečný, protože se podmínky překrývají:
    - `ratings/any(r: r ne 5 or r gt 7)`
  - Jednoduché výrazy porovnání zahrnující `eq`, `lt`, `le`, `gt`nebo `ge` lze kombinovat s `and`/`or`. Příklad:
    - `ratings/any(r: r gt 2 and r le 5)`
    - `ratings/any(r: r le 5 or r gt 7)`
  - Výrazy porovnání kombinované s `and` (spojení) lze dále kombinovat pomocí `or`. Tento formulář je známý v logické logice jako "[Disjunctive Normal Form](https://en.wikipedia.org/wiki/Disjunctive_normal_form)" (DNF). Příklad:
    - `ratings/any(r: (r gt 2 and r le 5) or (r gt 7 and r lt 10))`
- Pravidla pro `all`:
  - Jednoduché výrazy rovnosti nelze využít v kombinaci s jinými výrazy. Například tento výraz je povolen:
    - `ratings/all(r: r eq 5)`

    ale tento výraz není:
    - `ratings/all(r: r eq 5 or r le 2)`

    i když je tento výraz povolený, není užitečný, protože se podmínky překrývají:
    - `ratings/all(r: r eq 5 and r le 7)`
  - Jednoduché výrazy porovnání zahrnující `ne`, `lt`, `le`, `gt`nebo `ge` lze kombinovat s `and`/`or`. Příklad:
    - `ratings/all(r: r gt 2 and r le 5)`
    - `ratings/all(r: r le 5 or r gt 7)`
  - Výrazy porovnání kombinované s `or`em (disjunkes) se dají dál kombinovat pomocí `and`. Tento formulář je známý v logické logice jako "[conjunctive Normal Form](https://en.wikipedia.org/wiki/Conjunctive_normal_form)" (CNF). Příklad:
    - `ratings/all(r: (r le 2 or gt 5) and (r lt 7 or r ge 10))`

<a name="bkmk_complex"></a>

## <a name="rules-for-filtering-complex-collections"></a>Pravidla pro filtrování složitých kolekcí

Výrazy lambda přesahující komplexní kolekce podporují mnohem pružnější syntaxi než výrazy lambda nad kolekcemi primitivních typů. Můžete použít libovolný konstruktor filtru uvnitř takového výrazu lambda, který lze použít vně pouze dvou výjimek.

Nejprve funkce `search.ismatch` a `search.ismatchscoring` nejsou podporovány uvnitř výrazů lambda. Další informace najdete v tématu [porozumění filtrům kolekce OData v Azure kognitivní hledání](search-query-understand-collection-filters.md).

Druhý odkazující na pole, která nejsou *svázána* s proměnnou rozsahu (tedy voláním *volných proměnných*), nejsou povolena. Zvažte například následující dva ekvivalentní výrazy filtru OData:

1. `stores/any(s: s/amenities/any(a: a eq 'parking')) and details/margin gt 0.5`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and details/margin gt 0.5))`

První výraz bude povolen, zatímco druhý formulář bude odmítnut, protože `details/margin` není svázán s proměnnou rozsahu `s`.

Toto pravidlo také rozšiřuje na výrazy, které mají proměnné svázané ve vnějším oboru. Tyto proměnné jsou zdarma s ohledem na rozsah, ve kterém jsou uvedeny. Například první výraz je povolen, zatímco druhý ekvivalentní výraz není povolen, protože `s/name` je volné s ohledem na rozsah proměnné rozsahu `a`:

1. `stores/any(s: s/amenities/any(a: a eq 'parking') and s/name ne 'Flagship')`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and s/name ne 'Flagship'))`

Toto omezení by nemělo být problémem v praxi, protože je vždy možné vytvořit filtry tak, že lambda výrazy obsahují pouze vázané proměnné.

## <a name="cheat-sheet-for-collection-filter-rules"></a>Tahák list pro pravidla filtru kolekce

Následující tabulka shrnuje pravidla pro sestavování platných filtrů pro každý datový typ kolekce.

[!INCLUDE [Limitations on OData lambda expressions in Azure Cognitive Search](../../includes/search-query-odata-lambda-limitations.md)]

Příklady, jak vytvořit platné filtry pro každý případ, najdete v tématu [Postup zápisu platných filtrů kolekcí](#bkmk_examples).

Pokud filtry píšete často a rozumíte pravidlům, které vám pomůžou s tím, jak je jenom memorizing, přečtěte si téma [porozumění filtrům kolekce OData v Azure kognitivní hledání](search-query-understand-collection-filters.md).

## <a name="next-steps"></a>Další kroky  

- [Porozumění filtrům kolekce OData v Azure Kognitivní hledání](search-query-understand-collection-filters.md)
- [Filtry v Azure Kognitivní hledání](search-filters.md)
- [Přehled jazyka výrazů OData pro Azure Kognitivní hledání](query-odata-filter-orderby-syntax.md)
- [Referenční dokumentace syntaxe výrazu OData pro Azure Kognitivní hledání](search-query-odata-syntax-reference.md)
- [Hledání dokumentů &#40;Azure kognitivní hledání REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
