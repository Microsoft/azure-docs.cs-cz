---
title: Vyhledávání přibližných shod
titleSuffix: Azure Cognitive Search
description: Implementujte prostředí vyhledávání "máte na mysli" k automatické opravě chybně napsaného výrazu nebo překlepu.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: 32ad34bcfb42bf8fc45ba7fdb7fba5e797ee6106
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262430"
---
# <a name="fuzzy-search-to-correct-misspellings-and-typos"></a>Přibližné hledání pro opravu překlepů a překlepů

Azure Cognitive Search podporuje přibližné vyhledávání, typ dotazu, který kompenzuje překlepy a chybně napsané termíny ve vstupním řetězci. Je to tím, že hledá termíny, které mají podobné složení. Rozbalení hledání na pokrytí blízkých shod má za následek automatickou opravu překlepu, když je rozdíl jen několik chybně umístěných znaků. 

## <a name="what-is-fuzzy-search"></a>Co je fuzzy vyhledávání?

Je to expanzní cvičení, které vytváří zápas za podmínek, které mají podobné složení. Když je zadáno přibližné vyhledávání, modul vytvoří graf (založený na [deterministické teorii konečného automatu)](https://en.wikipedia.org/wiki/Deterministic_finite_automaton)podobně složených termínů pro všechny celé termíny v dotazu. Například pokud váš dotaz obsahuje tři termíny "university of Washington", `search=university~ of~ washington~` graf je vytvořen pro každý termín v dotazu (neexistuje žádný stop-word odstranění v fuzzy vyhledávání, takže "of" získá graf).

Graf se skládá z až 50 rozšíření nebo permutací každého termínu, zachycující správné i nesprávné varianty v procesu. Motor pak vrátí nejvyšší relevantní shody v odpovědi. 

Pro termín jako "univerzita", graf může mít "unversty, universty, univerzita, vesmír, inverzní". Všechny dokumenty, které odpovídají na ty v grafu jsou zahrnuty ve výsledcích. Na rozdíl od jiných dotazů, které analyzují text pro zpracování různých forem stejného slova ("myši" a "myš"), jsou porovnání v přibližném dotazu přijímána v nominální hodnotě bez jazykové analýzy textu. "Vesmír" a "inverzní", které jsou sémanticky odlišné, bude odpovídat, protože syntaktické nesrovnalosti jsou malé.

Shoda je úspěšná, pokud jsou nesrovnalosti omezeny na dvě nebo méně úprav, kde je úprava vloženým, odstraněným, nahrazeným nebo transponovaným znakem. Algoritmus korekce řetězce, který určuje rozdíl je [Damerau-Levenshtein vzdálenost](https://en.wikipedia.org/wiki/Damerau%E2%80%93Levenshtein_distance) metrika, popsal jako "minimální počet operací (vložení, odstranění, substituce, nebo transpozice dvou sousedních znaků) potřebné ke změně jednoho slova do druhého". 

V Azure Kognitivní vyhledávání:

+ Přibližovací dotaz se vztahuje na celé termíny, ale můžete podporovat fráze prostřednictvím a konstrukce. Například "Unviersty~ z ~ "Wshington~" by odpovídala na "University of Washington".

+ Výchozí vzdálenost úpravy je 2. Hodnota `~0` znamená žádné rozšíření (pouze přesný termín je považován za shodu), ale můžete zadat `~1` pro jeden stupeň rozdílu nebo jednu úpravu. 

+ Přibližný dotaz může rozšířit termín až o 50 dalších permutací. Toto omezení nelze konfigurovat, ale můžete efektivně snížit počet rozšíření snížením vzdálenosti úprav na 1.

+ Odpovědi se skládají z dokumentů obsahujících příslušnou shodu (až 50).

Společně grafy jsou odeslány jako kritéria shody proti tokeny v indexu. Jak si dokážete představit, přibližné hledání je ze své podstaty pomalejší než jiné formuláře dotazu. Velikost a složitost indexu můžete určit, zda výhody jsou dostatečné k vyrovnání latence odpovědi.

> [!NOTE]
> Vzhledem k tomu, že fuzzy hledání má tendenci být pomalé, může být užitečné prozkoumat alternativy, jako je indexování n-gram, s jeho průběh krátkých sekvencí znaků (dvě a tři sekvence znaků pro tokeny bigram a trigram). V závislosti na vašem jazyce a povrchu dotazu n-gram může poskytnout lepší výkon. Kompromis je, že n-gram indexování je velmi náročné na úložiště a generuje mnohem větší indexy.
>
> Další alternativou, kterou byste mohli zvážit, pokud chcete zvládnout jen ty nejkřiklavější případy, by byla [mapa synonym .](search-synonyms.md) Například mapování "hledat" na "serach, serch, sarch" nebo "načíst" na "retreive".

## <a name="indexing-for-fuzzy-search"></a>Indexování pro přibližné vyhledávání

Analyzátory se nepoužívají při zpracování dotazů k vytvoření grafu rozšíření, ale to neznamená, že analyzátory by měly být ignorovány ve scénářích přibližné hledání. Koneckonců analyzátory se používají při indexování k vytvoření tokenů, proti kterým se provádí párování, zda je dotaz volný formulář, filtrované vyhledávání nebo přibližné vyhledávání s grafem jako vstup. 

Obecně platí, že při přiřazování analyzátorů na základě pole, rozhodnutí doladit řetězce analýzy je založena na primární případ použití (filtr nebo fulltextové vyhledávání) spíše než specializované formuláře dotazu, jako je přibližné vyhledávání. Z tohoto důvodu neexistuje konkrétní doporučení analyzátoru pro přibližné vyhledávání. 

Pokud však testovací dotazy nevytvářejí očekávaných shod, můžete zkusit měnit analyzátor indexování a nastavit jej na [analyzátor jazyka](index-add-language-analyzers.md), abyste zjistili, zda dosáhnete lepších výsledků. Některé jazyky, zejména jazyky se samohláskami, mohou těžit z inflexních a nepravidelných slovních formulářů generovaných procesory přirozeného jazyka společnosti Microsoft. V některých případech pomocí analyzátoru správného jazyka může mít vliv na to, zda je termín tokenizován způsobem, který je kompatibilní s hodnotou poskytovanou uživatelem.

## <a name="how-to-use-fuzzy-search"></a>Jak používat přibližné vyhledávání

Přibližné dotazy jsou vytvořeny pomocí syntaxe úplného dotazu Lucene a vyvolávají [analyzátor dotazů Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html).

1. Nastavte úplný analyzátor Lucene v`queryType=full`dotazu ( ).

1. Volitelně můžete požadavek určit na určitá`searchFields=<field1,field2>`pole pomocí tohoto parametru ( ). 

1. Připojit operátor tilde`~`( ) na konci celého termínu (`search=<string>~`).

   Pokud chcete zadat vzdálenost úprav , zahrňte volitelný parametr číslo`~1`mezi 0 a 2 (výchozí). Například "modrá~" nebo "modrá~1" vrátí "modrá", "modrá" a "lepidlo".

V Azure Cognitive Search, kromě termínu a vzdálenosti (maximálně 2) neexistují žádné další parametry, které by bylo možné nastavit v dotazu.

> [!NOTE]
> Během zpracování dotazů fuzzy dotazy neprocházejí [lexikální analýzy](search-lucene-query-architecture.md#stage-2-lexical-analysis). Vstup dotazu je přidán přímo do stromu dotazu a rozbalen, aby se vytvořil graf termínů. Jediná provedená transformace je nižší kryt.

## <a name="testing-fuzzy-search"></a>Testování přibližné vyhledávání

Pro jednoduché testování doporučujeme [Průzkumník a hledání](search-explorer.md) nebo [Pošťák](search-get-started-postman.md) pro iterace přes výraz dotazu. Oba nástroje jsou interaktivní, což znamená, že můžete rychle projít více variant termínu a vyhodnotit odpovědi, které se vrátí.

Pokud jsou výsledky nejednoznačné, [zvýraznění přístupů](search-pagination-page-layout.md#hit-highlighting) vám může pomoci identifikovat shodu v odpovědi. 

> [!Note]
> Použití zvýraznění přístupů k identifikaci fuzzy shody má omezení a funguje pouze pro základní přibližné vyhledávání. Pokud index obsahuje profily hodnocení nebo pokud vrstvy dotazu s další syntaxe, zvýraznění přístupů může selhat k identifikaci shody. 

### <a name="example-1-fuzzy-search-with-the-exact-term"></a>Příklad 1: fuzzy vyhledávání s přesným termínem

Předpokládejme, že v `"Description"` poli ve vyhledávacím dokumentu existuje následující řetězec:`"Test queries with special characters, plus strings for MSFT, SQL and Java."`

Začněte s přimazaným vyhledáváním na "special" a přidejte zvýraznění přístupů do pole Popis:

    search=special~&highlight=Description

V odpovědi, protože jste přidali zvýraznění přístupů, formátování se použije na "zvláštní" jako odpovídající termín.

    "@search.highlights": {
        "Description": [
            "Test queries with <em>special</em> characters, plus strings for MSFT, SQL and Java."
        ]

Zkuste žádost znovu, chybně napsané "zvláštní" tím, že se několik písmen ("pe"):

    search=scial~&highlight=Description

Zatím žádná změna v odpovědi. Použití výchozí vzdálenosti 2 stupně, odstranění dvou znaků "pe" z "speciální" stále umožňuje úspěšnou shodu na tento termín.

    "@search.highlights": {
        "Description": [
            "Test queries with <em>special</em> characters, plus strings for MSFT, SQL and Java."
        ]

Pokusem o další požadavek dále upravte hledaný výraz tím, že vyložíte poslední znak pro celkem tři odstranění (od "speciální" do "scal"):

    search=scal~&highlight=Description

Všimněte si, že je vrácena stejná odpověď, ale nyní místo odpovídající na "zvláštní", přibližná shoda je na "SQL".

            "@search.score": 0.4232868,
            "@search.highlights": {
                "Description": [
                    "Mix of special characters, plus strings for MSFT, <em>SQL</em>, 2019, Linux, Java."
                ]

Bod tohoto rozšířeného příkladu je ilustrovat srozumitelnost, že zvýraznění přístupů může přinést nejednoznačné výsledky. Ve všech případech je vrácen stejný dokument. Pokud byste se při ověřování shody spoléhali na ID dokumentů, možná jste zmeškali přechod od "special" k "SQL".

## <a name="see-also"></a>Viz také

+ [Jak fulltextové vyhledávání funguje v Azure Cognitive Search (architektura analýzy dotazů)](search-lucene-query-architecture.md)
+ [Průzkumník hledání](search-explorer.md)
+ [Jak dotazovat v rozhraní .NET](search-query-dotnet.md)
+ [Jak dotazovat v REST](search-create-index-rest-api.md)
