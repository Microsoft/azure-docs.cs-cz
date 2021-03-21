---
title: Vyhledávání přibližných shod
titleSuffix: Azure Cognitive Search
description: Pokud chcete automaticky opravovat nesprávně napsaný nebo překlepy, implementujte vyhledávací prostředí "měli jste na mysli".
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: b2f2c8497d5365104a5ffc361b791450925d0c19
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101694780"
---
# <a name="fuzzy-search-to-correct-misspellings-and-typos"></a>Nepřibližné vyhledávání pro opravu chybných pravopisů a překlepů

Azure Kognitivní hledání podporuje hledání přibližných hodnot, což je typ dotazu, který kompenzuje překlepy a špatně napsané výrazy ve vstupním řetězci. Provede to tak, že vyhledá výrazy s podobným složením. Rozbalení vyhledávání na pokrytí blízkou shody má vliv na automatické opravy překlepů, pokud je rozdílová část pouze pár nesprávně umístěných znaků. 

## <a name="what-is-fuzzy-search"></a>Co je přibližné vyhledávání?

Je to rozšíření cvičení, které vytváří shodu s podobným složením. Při určení přibližného hledání vytvoří modul graf (založený na [deterministické konečné Automation teorie](https://en.wikipedia.org/wiki/Deterministic_finite_automaton)) podobně složené výrazy, pro všechny celé výrazy v dotazu. Pokud například váš dotaz obsahuje tři pojmy "University of Washington", vytvoří se graf pro každý výraz v dotazu `search=university~ of~ washington~` (při hledání přibližného počtu stop se neprovádí odebráním), takže "z" získá graf).

Graf se skládá z až 50 rozšíření nebo permutací každého období zachytávání správné i nesprávné varianty v procesu. Modul pak vrátí nejvyšší odpovídající shody v odpovědi. 

Pro termín, jako je "univerzita", může mít graf "unversty, Universty, University, Universe, Inverted". Všechny dokumenty, které odpovídají na těch v grafu, jsou součástí výsledků. Na rozdíl od dalších dotazů, které analyzují text pro zpracování různých forem stejného slova ("myši" a "myš"), se porovnávání v přibližných dotazech provádí na úrovni obličeje bez jakékoli jazykové analýzy textu. "Základní" a "invertované", které jsou sémanticky odlišné, se shodují, protože syntaktické rozdíly jsou malé.

Shoda je úspěšná, pokud jsou rozdíly omezené na dvě nebo méně úprav, kde Edit je vložený, odstraněný, nahrazený nebo převedený znak. Algoritmus opravy řetězce, který určuje rozdíl, je metrika [Damerau-Levenshtein](https://en.wikipedia.org/wiki/Damerau%E2%80%93Levenshtein_distance) , která je popsaná jako "minimální počet operací (vložení, odstranění, náhrady nebo přemístění dvou sousedících znaků), která se vyžadují ke změně jednoho slova na druhý. 

V Azure Kognitivní hledání:

+ Nepřibližný dotaz platí pro celé výrazy, ale můžete podporovat fráze prostřednictvím a konstrukcí. Například "Unviersty ~ ~" Wshington ~ "by se shodovala s" University of Washington ".

+ Výchozí vzdálenost úpravy je 2. Hodnota `~0` znamená bez rozšíření (pouze přesný termín je považován za shodu), ale můžete zadat `~1` pro jeden stupeň rozdílu nebo jeden z úprav. 

+ Nepřibližný dotaz může rozšířit výraz až na 50 dalších permutací. Toto omezení není konfigurovatelné, ale můžete omezit počet rozšíření tím, že zmenšíte vzdálenost úprav na 1.

+ Odpovědi se skládají z dokumentů obsahujících relevantní shodu (až do 50).

Souhrnně jsou grafy odesílány jako kritéria shody s tokeny v indexu. Jak si představím, přibližné vyhledávání je z hlediska jejich podstaty pomalejší než u jiných formulářů dotazů. Velikost a složitost indexu můžou určit, jestli jsou výhody dostatečné pro posun latence odpovědi.

> [!NOTE]
> Vzhledem k tomu, že přibližné vyhledávání je v úmyslu pomalé, může být vhodné prozkoumat alternativy, jako je například indexování n-gramů, s průběhem krátkých znakových sekvencí (dvě a tři sekvence znaků pro bigram a Hiragana). V závislosti na vašem jazyku a na ploše dotazů vám může n-gram zvýšit výkon. Obchodování je, že n-gramové indexování je velmi náročné na úložiště a generuje mnohem větší indexy.
>
> Další alternativou, kterou můžete zvážit, pokud chcete zpracovat pouze egregious případy, by to byla [Mapa synonym](search-synonyms.md). Například mapování "Search" na "vyhledávání, Serch, sarch" nebo "načíst" na "načtení".

## <a name="indexing-for-fuzzy-search"></a>Indexování pro hledání přibližné vyhledávání

Analyzátory se během zpracování dotazu nepoužívají k vytvoření rozšiřujícího grafu, ale to neznamená, že analyzátory by se měly ignorovat ve scénářích hledání přibližné. Po všech vzdálení analyzátory se při indexování používají k vytvoření tokenů, u kterých se shoduje, zda je dotaz v podobě bezplatné formě, filtrovaného vyhledávání nebo přibližného vyhledávání s grafem jako vstupem. 

Obecně platí, že při přiřazování analyzátorů pro každé pole je rozhodnutí o vyladění řetězu analýz založeno na primárním případu použití (filtr nebo fulltextové vyhledávání) místo specializovaných formulářů dotazů, jako je hledání přibližné. Z tohoto důvodu není k dispozici konkrétní doporučení analyzátoru pro přibližné vyhledávání. 

Pokud ale testovací dotazy nezpůsobují očekávané shody, můžete zkusit provést změny v analyzátoru indexování, nastavit ji na [analyzátor jazyka](index-add-language-analyzers.md), abyste viděli, jestli získáte lepší výsledky. Některé jazyky, zejména ty, které mají samohlásky, mohou těžit z informování a nepravidelných slovových tvarů generovaných procesory přirozeného jazyka společnosti Microsoft. V některých případech může použití správného jazykového analyzátoru udělat rozdíl v tom, zda je výraz vypsaný způsobem, který je kompatibilní s hodnotou poskytnutou uživatelem.

## <a name="how-to-use-fuzzy-search"></a>Jak používat hledání fuzzy

Fuzzy dotazy jsou vytvářeny pomocí úplné syntaxe dotazů Lucene a vyvolává [analyzátor dotazů Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html).

1. Nastavte úplný analyzátor Lucene na dotaz ( `queryType=full` ).

1. Volitelně můžete určit rozsah žádosti na konkrétní pole pomocí tohoto parametru ( `searchFields=<field1,field2>` ). 

1. Připojí operátor tilda ( `~` ) na konci celého výrazu ( `search=<string>~` ).

   Zahrňte volitelný parametr, číslo mezi 0 a 2 (výchozí), pokud chcete určit vzdálenost úprav ( `~1` ). Například "Blue ~" nebo "Blue ~ 1" vrátí "Blue", "blues" a "Glue".

V Azure Kognitivní hledání kromě termínu a vzdálenosti (maximálně 2) neexistují žádné další parametry pro nastavení dotazu.

> [!NOTE]
> Během zpracování dotazu nejdou dotazy s fuzzy [analýzou podléhat lexikální analýze](search-lucene-query-architecture.md#stage-2-lexical-analysis). Vstup dotazu se přidá přímo do stromu dotazu a rozbalí se, aby se vytvořil graf podmínek. Jediná provedená transformace je malá písmena.

## <a name="testing-fuzzy-search"></a>Testování přibližného vyhledávání

Pro jednoduché testování doporučujeme, abyste provedli příkaz [Průzkumník služby Search](search-explorer.md) nebo [publikování](search-get-started-rest.md) pro iteraci na výrazu dotazu. Oba nástroje jsou interaktivní, což znamená, že můžete rychle krokovat více variant termínu a vyhodnotit odpovědi, které se vrátí zpět.

Pokud jsou výsledky dvojznačné, [zvýrazňování přístupů](search-pagination-page-layout.md#hit-highlighting) vám může pomáhat identifikovat shodu v odpovědi. 

> [!Note]
> Použití zvýraznění přístupů k identifikaci přibližných shod má omezení a funguje pouze pro základní přibližné vyhledávání. Pokud má váš index vyhodnocování profilů nebo pokud zařadíte do vrstvy dotaz s další syntaxí, může zvýrazňování chyb identifikovat shodu. 

### <a name="example-1-fuzzy-search-with-the-exact-term"></a>Příklad 1: hledání přibližného vyhledávání s přesným termínem

V `"Description"` poli vyhledávacího dokumentu se předpokládá následující řetězec: `"Test queries with special characters, plus strings for MSFT, SQL and Java."`

Začněte s přibližným hledáním "Special" a přidejte zvýraznění přístupů do pole Popis:

```console
search=special~&highlight=Description
```

Vzhledem k tomu, že jste přidali zvýraznění přístupů, se v odpovědi použije jako shodný termín formátování "Special".

```output
"@search.highlights": {
    "Description": [
        "Test queries with <em>special</em> characters, plus strings for MSFT, SQL and Java."
    ]
```

Opakujte pokus o zadání několika písmen ("PE"):

```console
search=scial~&highlight=Description
```

Zatím se žádná změna odezvy nezměnila. Při použití výchozí vzdálenosti 2 stupňů se při odebrání dvou znaků "PE" z "zvláštního" stále povoluje úspěšná shoda s tímto termínem.

```output
"@search.highlights": {
    "Description": [
        "Test queries with <em>special</em> characters, plus strings for MSFT, SQL and Java."
    ]
```

Když se pokusíte o jednu další žádost, dál upravte hledaný termín tak, že si vyberete jeden poslední znak celkem tři odstranění (od "speciální" až po "škálovatelnost"):

```console
search=scal~&highlight=Description
```

Všimněte si, že se vrátí stejná odpověď, ale teď místo porovnání s "speciální" je přibližná shoda na SQL.

```output
        "@search.score": 0.4232868,
        "@search.highlights": {
            "Description": [
                "Mix of special characters, plus strings for MSFT, <em>SQL</em>, 2019, Linux, Java."
            ]
```

Bodem tohoto rozbaleného příkladu je ilustrovat přehlednost, který zvýrazňování výsledků může přinést nejednoznačným výsledkům. Ve všech případech se vrátí stejný dokument. Při ověřování shody jste se spoléhali na ID dokumentů, možná jste nenalezli Shift "Special" na "SQL".

## <a name="see-also"></a>Viz také

+ [Jak funguje fulltextové vyhledávání v Azure Kognitivní hledání (architektura analýzy dotazů)](search-lucene-query-architecture.md)
+ [Průzkumník vyhledávání](search-explorer.md)
+ [Dotazování v .NET](./search-get-started-dotnet.md)
+ [Dotazování v REST](./search-get-started-powershell.md)