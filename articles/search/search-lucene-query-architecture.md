---
title: Fulltextový dotaz a architektura modulu indexování (Lucene)
titleSuffix: Azure Cognitive Search
description: Zkontroluje Lucene zpracování dotazů a načtení dokumentů koncepty pro fulltextové vyhledávání, jak souvisí s Azure Cognitive Search.
manager: nitinme
author: yahnoosh
ms.author: jlembicz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: d46d0309b3d2ffb638016e88ba022e49009eedf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282936"
---
# <a name="how-full-text-search-works-in-azure-cognitive-search"></a>Jak funguje fulltextové vyhledávání ve službě Azure Cognitive Search

Tento článek je určen pro vývojáře, kteří potřebují hlubší pochopení toho, jak lucene fulltextové vyhledávání funguje v Azure Cognitive Search. V případě textových dotazů Azure Cognitive Search ve většině scénářů bezproblémově doručí očekávané výsledky, ale občas se může zobrazit výsledek, který se zdá být nějakým způsobem „mimo“. V takových situacích vám čtyři fáze provádění dotazů Lucene (parsování dotazu, lexikální analýza, porovnávání dokumentů, hodnocení) na pozadí můžou pomoct s identifikací konkrétních změn parametrů dotazu nebo konfigurace indexu, které zajistí požadovaný výsledek. 

> [!Note] 
> Azure Cognitive Search používá Lucene pro fulltextové vyhledávání, ale integrace Lucene není vyčerpávající. Selektivně zveřejňujeme a rozšiřujeme funkce Lucene, abychom umožnili scénáře důležité pro Azure Cognitive Search. 

## <a name="architecture-overview-and-diagram"></a>Přehled architektury a diagram

Zpracování fulltextového vyhledávacího dotazu začíná analýzou textu dotazu pro extrahování hledaných výrazů. Vyhledávač používá index k načtení dokumentů s odpovídajícími termíny. Jednotlivé termíny dotazu jsou někdy rozděleny a rekonstituovány do nových formulářů, aby se širší síť přemístila na to, co by mohlo být považováno za potenciální shodu. Sada výsledků je pak seřazena podle skóre relevance přiřazeného každému jednotlivému odpovídajícímu dokumentu. Ti v horní části seznamu seřazených jsou vráceny do volající aplikace.

Přeformulovat, spuštění dotazu má čtyři fáze: 

1. Analýza dotazu 
2. Lexikální analýza 
3. Načítání dokumentu 
4. Vyhodnocování 

Následující diagram znázorňuje součásti použité ke zpracování požadavku na hledání. 

 ![Diagram architektury dotazů Lucene v Azure Cognitive Search][1]


| Klíčové komponenty | Funkční popis | 
|----------------|------------------------|
|**Analyzátory dotazů** | Samostatné termíny dotazů od operátorů dotazů a vytvořte strukturu dotazů (strom dotazů), která má být odeslána do vyhledávače. |
|**Analyzátory** | Provádějte lexikální analýzu termínů dotazu. Tento proces může zahrnovat transformaci, odebrání nebo rozšíření dotazových termínů. |
|**Index** | Efektivní datová struktura používaná k ukládání a uspořádání prohledávatelných termínů extrahovaných z indexovaných dokumentů. |
|**Vyhledávač** | Načte a skóre odpovídající dokumenty na základě obsahu invertovaného indexu. |

## <a name="anatomy-of-a-search-request"></a>Anatomie požadavku hledání

Požadavek na vyhledávání je úplná specifikace toho, co by mělo být vráceno v sadě výsledků. V nejjednodušší formě se jedná o prázdný dotaz bez kritérií jakéhokoli druhu. Realističtější příklad obsahuje parametry, několik termínů dotazu, případně vymezené na určitá pole, s případně výrazem filtru a pravidly řazení.  

Následující příklad je požadavek na vyhledávání, který můžete odeslat do Azure Cognitive Search pomocí [rozhraní REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents).  

~~~~
POST /indexes/hotels/docs/search?api-version=2019-05-06
{
    "search": "Spacious, air-condition* +\"Ocean view\"",
    "searchFields": "description, title",
    "searchMode": "any",
    "filter": "price ge 60 and price lt 300",
    "orderby": "geo.distance(location, geography'POINT(-159.476235 22.227659)')", 
    "queryType": "full" 
}
~~~~

Pro tento požadavek vyhledávač provádí následující:

1. Filtruje dokumenty, kde cena je nejméně 60 dolarů a méně než 300 dolarů.
2. Spustí dotaz. V tomto příkladu se vyhledávací dotaz skládá `"Spacious, air-condition* +\"Ocean view\""` z frází a termínů: (uživatelé obvykle nezadávají interpunkci, ale její zahrnutí do příkladu nám umožňuje vysvětlit, jak je analyzátory zpracovávají). Pro tento dotaz vyhledávač prohledá pole popisu a `searchFields` názvu zadané v dokumentu, které obsahují "Zobrazení oceánu", a navíc na termín "prostorný", nebo na termínu "prostornost", nebo na termíny, které začínají předponou "klimatizace". Parametr `searchMode` se používá tak, aby odpovídal libovolnému termínu (výchozí) nebo všem`+`z nich, v případech, kdy termín není výslovně vyžadován ( ).
3. Objednávky výsledné sady hotelů podle blízkosti daného zeměpisného umístění a pak se vrátí do volající aplikace. 

Většina tohoto článku je o zpracování *vyhledávacího dotazu*: `"Spacious, air-condition* +\"Ocean view\""`. Filtrování a řazení jsou mimo rozsah. Další informace naleznete v [referenční dokumentaci rozhraní API pro vyhledávání](https://docs.microsoft.com/rest/api/searchservice/search-documents).

<a name="stage1"></a>
## <a name="stage-1-query-parsing"></a>Fáze 1: Analýza dotazu 

Jak již bylo uvedeno, řetězec dotazu je první řádek požadavku: 

~~~~
 "search": "Spacious, air-condition* +\"Ocean view\"", 
~~~~

Analyzátor dotazu odděluje operátory `*` (například a `+` v příkladu) od hledaných výrazů a dekonstruuje vyhledávací dotaz na *poddotazy* podporovaného typu: 

+ *termínový dotaz* pro samostatné termíny (například prostorná)
+ *frázový dotaz* pro citované termíny (například zobrazení oceánu)
+ *předpona dotaz* pro termíny `*` následovaný operátorem předpony (například klimatizace)

Úplný seznam podporovaných typů dotazů naleznete v [tématu Syntaxe dotazu Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)

Operátory přidružené k poddotazu určují, zda dotaz "musí být" nebo "by měl být" splněn, aby byl dokument považován za shodu. Například `+"Ocean view"` je "musí" vzhledem k operátor. `+` 

Analyzátor dotazu restrukturalizuje poddotazy do *stromu dotazů* (vnitřní struktura představující dotaz), který předává do vyhledávače. V první fázi analýzy dotazu strom dotazu vypadá takto.  

 ![Logický dotaz vyhledávací režim žádné][2]

### <a name="supported-parsers-simple-and-full-lucene"></a>Podporované analyzátory: Jednoduché a plné Lucene 

 Azure Cognitive Search zveřejňuje dva `simple` různé jazyky `full`dotazu (výchozí) a . Nastavením `queryType` parametru s požadavkem na vyhledávání sdělte analyzátoru dotazů, který dotaz, který zvolíte, aby věděl, jak interpretovat operátory a syntaxi. [Jednoduchý dotazovací jazyk](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) je intuitivní a robustní, často vhodný pro interpretaci vstupu uživatele tak, jak je bez zpracování na straně klienta. Podporuje operátory dotazů známé z webových vyhledávačů. [Úplný dotazovací jazyk Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search), `queryType=full`který získáte nastavením , rozšiřuje výchozí jednoduchý dotazovací jazyk přidáním podpory pro další operátory a typy dotazů, jako jsou zástupné, přibližné dotazy, dotazy regulární výraz a dotazy s rozsahem polí. Například regulární výraz odeslaný v syntaxi jednoduchého dotazu by byl interpretován jako řetězec dotazu a nikoli jako výraz. Příklad požadavku v tomto článku používá dotazovací jazyk Full Lucene.

### <a name="impact-of-searchmode-on-the-parser"></a>Dopad searchMode na analyzátor 

Dalším parametrem požadavku hledání, který `searchMode` ovlivňuje analýzu, je parametr. Řídí výchozí operátor pro logické dotazy: všechny (výchozí) nebo všechny.  

Když `searchMode=any`, což je výchozí, je oddělovač místa mezi`||`prostornou a klimatizací OR ( ), takže text ukázkového dotazu je ekvivalentní: 

~~~~
Spacious,||air-condition*+"Ocean view" 
~~~~

Explicitní operátory, `+` `+"Ocean view"`například v , jsou jednoznačné ve konstrukci logického dotazu (termín *se musí* shodovat). Méně zřejmé je, jak interpretovat zbývající pojmy: prostorné a klimatizované. Měl by vyhledávač najít shody na výhled na oceán *a* prostorné *a* vzduch-stav? Nebo by měl najít výhled na oceán plus *jeden ze* zbývajících podmínek? 

Ve výchozím`searchMode=any`nastavení ( ), vyhledávač předpokládá širší výklad. Buď pole *by měla* být uzavřeno, což odráží "nebo" sémantiku. Počáteční strom dotazu ilustrované dříve, se dvěma operacemi "by", zobrazuje výchozí.  

Předpokládejme, že `searchMode=all`jsme nyní nastaveny . V tomto případě je prostor interpretován jako operace "a". Aby se mohl yřadí jako shoda, musí být v dokumentu k dispozici všechny zbývající podmínky. Výsledný ukázkový dotaz by byl interpretován takto: 

~~~~
+Spacious,+air-condition*+"Ocean view"
~~~~

Upravený strom dotazu pro tento dotaz by byl následující, kde odpovídající dokument je průsečík všech tří poddotazů: 

 ![Logický režim vyhledávání dotazů vše][3]

> [!Note] 
> Volba `searchMode=any` `searchMode=all` nad je rozhodnutí nejlépe dosazeno spuštěním reprezentativních dotazů. Uživatelé, kteří mohou zahrnout operátory (běžné při hledání `searchMode=all` úložiště dokumentů) může najít výsledky intuitivnější, pokud informuje logické dotaz konstrukce. Další informace o souhře mezi `searchMode` operátory naleznete v [tématu Syntaxe jednoduchého dotazu](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search).

<a name="stage2"></a>
## <a name="stage-2-lexical-analysis"></a>2. etapa: Lexikální analýza 

Lexikální analyzátory zpracovávají *termínové dotazy* a *dotazy frází* po strukturování stromu dotazů. Analyzátor přijme textové vstupy, které mu analyzátor, zpracuje text a potom odešle zpět tokenizované termíny, které mají být začleněny do stromu dotazů. 

Nejběžnější formou lexikální analýzy je *lingvistická analýza,* která transformuje termíny dotazů na základě pravidel specifických pro daný jazyk: 

* Zmenšení termínu dotazu na kořenovou formu slova 
* Odstranění nepodstatných slov (stopwords, například "the" nebo "and" v angličtině) 
* Rozdělení složeného slova na součásti 
* Dolní kryt velké písmeno 

Všechny tyto operace mají tendenci mazat rozdíly mezi zadávání textu poskytované uživatelem a podmínky uložené v indexu. Tyto operace jdou nad rámec zpracování textu a vyžadují důkladnou znalost samotného jazyka. Chcete-li přidat tuto vrstvu jazykového povědomí, Azure Cognitive Search podporuje dlouhý seznam [analyzátorů jazyků](https://docs.microsoft.com/rest/api/searchservice/language-support) od Lucene i Microsoftu.

> [!Note]
> Požadavky na analýzu se mohou pohybovat od minimální až po komplikované v závislosti na vašem scénáři. Složitost lexikální analýzy můžete řídit výběrem jednoho z předdefinovaných analyzátorů nebo vytvořením vlastního [analyzátoru](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search). Analyzátory jsou vymezeny na prohledávatelná pole a jsou určeny jako součást definice pole. To umožňuje měnit lexikální analýzu na základě pole. Nespecifikovaný se používá *standardní* analyzátor Lucene.

V našem příkladu před analýzou má počáteční strom dotazu termín "Prostorný", s velkým písmenem "S" a čárkou, kterou analyzátor dotazu interpretuje jako součást termínu dotazu (čárka se nepovažuje za operátor dotazovacího jazyka).  

Když výchozí analyzátor zpracuje termín, bude malá písmena "pohled na oceán" a "prostorný" a odstraní znak čárky. Upravený strom dotazů bude vypadat takto: 

 ![Logický dotaz s analyzovanými termíny][4]

### <a name="testing-analyzer-behaviors"></a>Testování chování analyzátoru 

Chování analyzátoru lze testovat pomocí [analyzovat rozhraní API](https://docs.microsoft.com/rest/api/searchservice/test-analyzer). Zadejte text, který chcete analyzovat, abyste zjistili, jaké termíny daný analyzátor bude generovat. Chcete-li například zjistit, jak by standardní analyzátor zpracoval text "klimatizace", můžete vydat následující požadavek:

~~~~
{
    "text": "air-condition",
    "analyzer": "standard"
}
~~~~

Standardní analyzátor rozdělí vstupní text na následující dva tokeny a oznamuje je atributy, jako je počáteční a koncový posun (používaný pro zvýraznění přístupů) a jejich pozici (používá se pro porovnávání frází):

~~~~
{
  "tokens": [
    {
      "token": "air",
      "startOffset": 0,
      "endOffset": 3,
      "position": 0
    },
    {
      "token": "condition",
      "startOffset": 4,
      "endOffset": 13,
      "position": 1
    }
  ]
}
~~~~

<a name="exceptions"></a>

### <a name="exceptions-to-lexical-analysis"></a>Výjimky z lexikální analýzy 

Lexikální analýza se vztahuje pouze na typy dotazů, které vyžadují úplné termíny – termínový dotaz nebo frázový dotaz. Nevztahuje se na typy dotazů s neúplnými termíny – předpona dotaz, zástupný dotaz, dotaz regulárního výrazu – nebo na přibližný dotaz. Tyto typy dotazů, včetně dotazu předpony s termínem `air-condition*` v našem příkladu, jsou přidány přímo do stromu dotazu, obcházet fázi analýzy. Jedinou transformací provedenou na dotaz termíny těchto typů je lowercasing.

<a name="stage3"></a>

## <a name="stage-3-document-retrieval"></a>3. etapa: Načítání dokumentů 

Načítání dokumentu odkazuje na hledání dokumentů s odpovídajícími podmínkami v indexu. Tato fáze je nejlépe chápána prostřednictvím příkladu. Začněme s indexem hotelů s následujícím jednoduchým schématem: 

~~~~
{
    "name": "hotels",
    "fields": [
        { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
        { "name": "title", "type": "Edm.String", "searchable": true },
        { "name": "description", "type": "Edm.String", "searchable": true }
    ] 
} 
~~~~

Dále předpokládejme, že tento index obsahuje následující čtyři dokumenty: 

~~~~
{
    "value": [
        {
            "id": "1",
            "title": "Hotel Atman",
            "description": "Spacious rooms, ocean view, walking distance to the beach."
        },
        {
            "id": "2",
            "title": "Beach Resort",
            "description": "Located on the north shore of the island of Kauaʻi. Ocean view."
        },
        {
            "id": "3",
            "title": "Playa Hotel",
            "description": "Comfortable, air-conditioned rooms with ocean view."
        },
        {
            "id": "4",
            "title": "Ocean Retreat",
            "description": "Quiet and secluded"
        }
    ]
}
~~~~

**Jak jsou indexovány podmínky**

Chcete-li pochopit vyhledávání, je dobré znát několik základů o indexování. Jednotka úložiště je invertovaný index, jeden pro každé prohledávatelné pole. V rámci obráceného indexu je seřazený seznam všech termínů ze všech dokumentů. Každý termín se mapuje na seznam dokumentů, ve kterých se vyskytuje, jak je zřejmé v příkladu níže.

Chcete-li vytvořit termíny v obráceném indexu, vyhledávač provádí lexikální analýzu obsahu dokumentů, podobně jako to, co se děje při zpracování dotazů:

1. *Textové vstupy* jsou předány analyzátoru, malá písmena, zbaveninterpunkce a tak dále, v závislosti na konfiguraci analyzátoru. 
2. *Tokeny* jsou výstupem analýzy textu.
3. *Podmínky* jsou přidány do indexu.

Je běžné, ale není nutné, používat stejné analyzátory pro operace vyhledávání a indexování tak, aby termíny dotazu vypadat spíše jako termíny uvnitř indexu.

> [!Note]
> Azure Cognitive Search umožňuje zadat různé analyzátory pro `indexAnalyzer` `searchAnalyzer` indexování a vyhledávání pomocí dalších parametrů a parametrů pole. Pokud není zadán, analyzátor `analyzer` nastavit s vlastností se používá pro indexování a vyhledávání.  

**Invertovaný index, například dokumenty**

Po návratu do našeho příkladu, pro **pole názvu,** obrácený index vypadá takto:

| Označení | Seznam dokumentů |
|------|---------------|
| Atman | 1 |
| Pláže | 2 |
| Hotel | 1, 3 |
| Oceánu | 4  |
| Playa | 3 |
| Resort | 3 |
| Ústup | 4 |

V názvu pole, pouze *hotel* se objeví ve dvou dokumentech: 1, 3.

U pole **popisu** je index následující:

| Označení | Seznam dokumentů |
|------|---------------|
| Vzduchu | 3
| a | 4
| Pláže | 1
| Podmíněna | 3
| Pohodlné | 3
| Vzdálenost | 1
| Ostrov | 2
| kauai | 2
| Nachází | 2
| north | 2
| Oceánu | 1, 2, 3
| z | 2
| on |2
| Tichý | 4
| Pokoje  | 1, 3
| Samotě | 4
| Pobřeží | 2
| Prostorné | 1
| prostředek | 1, 2
| na | 1
| Prohlédni | 1, 2, 3
| Chůze | 1
| with | 3


**Párování dotazů s indexovanými termíny**

Vzhledem k výše uvedeným invertovaným indexům se vraťme k ukázkovému dotazu a podívejte se, jak se pro náš ukázkový dotaz najdou odpovídající dokumenty. Připomeňme, že strom konečných dotazů vypadá takto: 

 ![Logický dotaz s analyzovanými termíny][4]

Během provádění dotazu jsou jednotlivé dotazy prováděny nezávisle na prohledávatelných polích. 

+ TermQuery, "prostorný", odpovídá dokumentu 1 (Hotel Atman). 

+ PrefixQuery, "klimatizace*", neodpovídá žádné dokumenty. 

  Toto je chování, které někdy mate vývojáře. Přestože termín klimatizovaný existuje v dokumentu, je rozdělen do dvou termínů výchozí analyzátor. Připomeňme, že předpony dotazy, které obsahují částečné termíny, nejsou analyzovány. Proto jsou termíny s předponou "klimatizace" vyhledány v obráceném indexu a nebyly nalezeny.

+ PhraseQuery, "pohled na oceán", vyhledá termíny "oceán" a "zobrazení" a zkontroluje blízkost termínů v původním dokumentu. Dokumenty 1, 2 a 3 odpovídají tomuto dotazu v poli popisu. Všimněte si, že dokument 4 má v názvu termín oceán, ale není považován za shodu, protože hledáme frázi "pohled na oceán" spíše než jednotlivá slova. 

> [!Note]
> Vyhledávací dotaz se spouští nezávisle na všech prohledávatelných polích v indexu `searchFields` Azure Cognitive Search, pokud neomezíte pole nastavená parametrem, jak je znázorněno v ukázkovém požadavku na vyhledávání. Budou vráceny dokumenty, které se shodují v libovolném vybraném poli. 

Celkově jsou pro daný dotaz dokumenty, které se shodují, 1, 2, 3. 

## <a name="stage-4-scoring"></a>4. etapa: Bodování  

Každému dokumentu v sadě výsledků hledání je přiřazeno skóre relevance. Funkce skóre relevance je pořadí vyšší ty dokumenty, které nejlépe odpovědět na otázku uživatele, jak je vyjádřeno vyhledávací dotaz. Skóre se vypočítá na základě statistických vlastností termínů, které odpovídají. Jádrem vzorce hodnocení je [TF/IDF (frekvence inverzní frekvence dokumentu termínu).](https://en.wikipedia.org/wiki/Tf%E2%80%93idf) V dotazech obsahujících vzácné a běžné termíny TF/IDF podporuje výsledky obsahující vzácný termín. Například v hypotetickém indexu se všemi články wikipedie, z dokumentů, které odpovídaly *dotazu prezidenta*, dokumenty odpovídající *prezidentovi* jsou považovány za relevantnější než dokumenty odpovídající *na*.


### <a name="scoring-example"></a>Příklad bodování

Odvolání tři dokumenty, které odpovídají náš příklad dotazu:
~~~~
search=Spacious, air-condition* +"Ocean view"  
~~~~
~~~~
{
  "value": [
    {
      "@search.score": 0.25610128,
      "id": "1",
      "title": "Hotel Atman",
      "description": "Spacious rooms, ocean view, walking distance to the beach."
    },
    {
      "@search.score": 0.08951007,
      "id": "3",
      "title": "Playa Hotel",
      "description": "Comfortable, air-conditioned rooms with ocean view."
    },
    {
      "@search.score": 0.05967338,
      "id": "2",
      "title": "Ocean Resort",
      "description": "Located on a cliff on the north shore of the island of Kauai. Ocean view."
    }
  ]
}
~~~~

Dokument 1 nejlépe odpovídá dotazu, protože v poli popisu se vyskytuje termín *prostorný* i požadovaný *výraz pro zobrazení oceánu.* Další dva dokumenty odpovídají pouze frázi *pohled na oceán*. Může být překvapivé, že skóre relevance pro dokument 2 a 3 se liší, i když odpovídá dotazu stejným způsobem. Je to proto, že vzorec hodnocení má více komponent než pouze TF/IDF. V tomto případě byl dokumentu 3 přiřazen o něco vyšší skóre, protože jeho popis je kratší. Seznamte se s [Luceneiným praktickým vzorcem bodování,](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/search/similarities/TFIDFSimilarity.html) abyste pochopili, jak může délka pole a další faktory ovlivnit skóre relevance.

Některé typy dotazů (zástupný znak, předpona, regulární výraz) vždy přispívají konstantnískóre celkové skóre dokumentu. To umožňuje shody nalezené prostřednictvím rozšíření dotazu, které mají být zahrnuty do výsledků, ale bez ovlivnění pořadí. 

Příklad ilustruje, proč na tom záleží. Hledání se zástupnými kódy, včetně vyhledávání předpon, jsou podle definice nejednoznačné, protože vstup je částečný řetězec s potenciálními shodami na velmi velkém počtu různorodých termínů (zvažte zadání "tour*", se shodami nalezenými na "tours", "tourettes" a " turmalín"). Vzhledem k povaze těchto výsledků neexistuje způsob, jak rozumně vyvodit, které podmínky jsou cennější než jiné. Z tohoto důvodu ignorujeme frekvence termínů při vyhodnocování výsledků v dotazech typů zástupných, prefix a regulární výraz. V požadavku na více částí hledání, který obsahuje částečné a úplné termíny, jsou výsledky částečného vstupu začleněny s konstantním skóre, aby se zabránilo zkreslení vůči potenciálně neočekávaným shodám.

### <a name="score-tuning"></a>Ladění skóre

Existují dva způsoby, jak vazure cognitive search vyladit skóre relevantnosti:

1. **Profily hodnocení** propagují dokumenty v seřazeném seznamu výsledků na základě sady pravidel. V našem příkladu bychom mohli považovat dokumenty, které odpovídají v poli nadpisu, za relevantnější než dokumenty, které odpovídají poli popisu. Navíc, pokud by náš index měl cenové pole pro každý hotel, mohli bychom propagovat dokumenty s nižší cenou. Přečtěte si další informace o tom, jak [přidat profily hodnocení do vyhledávacího indexu.](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)
2. **Rozšíření termínu** (k dispozici pouze v syntaxi dotazu `^` Úplné Lucene) poskytuje operátor zesílení, který lze použít pro libovolnou část stromu dotazů. V našem příkladu lze místo vyhledávání v předponě *klimatizace*\*vyhledat přesný termín *klimatizace* nebo předponu, ale dokumenty, které odpovídají přesnému termínu, jsou seřazeny výše použitím podpory na termín dotaz: *klimatizace^2|| klimatizace**. Přečtěte si další informace o [podpoře termínů](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search#bkmk_termboost).


### <a name="scoring-in-a-distributed-index"></a>Bodování v distribuovaném indexu

Všechny indexy v Azure Cognitive Search jsou automaticky rozděleny do více horizontálních oddílů, což nám umožňuje rychle distribuovat index mezi více uzlů během škálování služby navýšit nebo vertikálně navýšit kapacitu. Při vydání požadavku na vyhledávání je vydána proti každý oddíl nezávisle. Výsledky z každého úlomku jsou pak sloučeny a seřazeny podle skóre (pokud není definováno žádné jiné řazení). Je důležité vědět, že funkce hodnocení váží četnost termínů dotazu proti jeho frekvenci inverzního dokumentu ve všech dokumentech v rámci střepu, nikoli napříč všemi střepy!

To znamená, že skóre relevance *se může* lišit pro identické dokumenty, pokud jsou umístěny na různých úlomcích. Naštěstí tyto rozdíly mají tendenci mizet, protože počet dokumentů v indexu roste kvůli rovnoměrnějšímu rozdělení termínů. Není možné předpokládat, na kterém úlovku bude umístěn daný dokument. Však za předpokladu, že klíč dokumentu nezmění, bude vždy přiřazen ke stejnému šiřidlu.

Skóre dokumentu obecně není nejlepším atributem pro objednání dokumentů, pokud je důležitá stabilita objednávky. Například zadejte dva dokumenty s identickým skóre, neexistuje žádná záruka, který z nich se zobrazí jako první v následných spuštěních stejného dotazu. Skóre dokumentu by mělo pouze obecný pocit relevance dokumentu vzhledem k ostatním dokumentům v sadě výsledků.

## <a name="conclusion"></a>Závěr

Úspěch internetových vyhledávačů vyvolal očekávání fulltextového vyhledávání soukromých dat. Pro téměř jakýkoli druh vyhledávání, nyní očekáváme, že motor pochopit náš záměr, i když termíny jsou chybně nebo neúplné. Můžeme dokonce očekávat shody založené na téměř ekvivalentních termínech nebo synonymech, které jsme ve skutečnosti nikdy nespecifikovali.

Z technického hlediska je fulltextové vyhledávání velmi složité, což vyžaduje sofistikovanou jazykovou analýzu a systematický přístup ke zpracování způsoby, které destilují, rozšiřují a transformují dotazovací termíny, aby přinesly relevantní výsledek. Vzhledem k vlastní složitosti, existuje mnoho faktorů, které mohou ovlivnit výsledek dotazu. Z tohoto důvodu, investovat čas pochopit mechaniku fulltextového vyhledávání nabízí hmatatelné výhody při pokusu pracovat přes neočekávané výsledky.  

Tento článek prozkoumal fulltextové vyhledávání v kontextu Azure Cognitive Search. Doufáme, že vám poskytne dostatečné pozadí k rozpoznání potenciálních příčin a řešení pro řešení běžných problémů s dotazy. 

## <a name="next-steps"></a>Další kroky

+ Vytvořte ukázkový index, vyzkoušejte různé dotazy a zkontrolujte výsledky. Pokyny naleznete [v tématu Sestavení a dotaz na index na portálu](search-get-started-portal.md#query-index).

+ Vyzkoušejte další syntaxi dotazu z ukázkové části [Hledat dokumenty](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples) nebo z [syntaxe jednoduchého dotazu](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) v průzkumníku vyhledávání na portálu.

+ Zkontrolujte [profily hodnocení,](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) pokud chcete vyladit hodnocení ve své vyhledávací aplikaci.

+ Přečtěte si, jak používat [lexikální analyzátory specifické pro jazyk](https://docs.microsoft.com/rest/api/searchservice/language-support).

+ [Nakonfigurujte vlastní analyzátory](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search) pro minimální zpracování nebo specializované zpracování v konkrétních polích.

## <a name="see-also"></a>Viz také

[Rozhraní API pro vyhledávání v dokumentech](https://docs.microsoft.com/rest/api/searchservice/search-documents) 

[Jednoduchá syntaxe dotazů](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 

[Úplná syntaxe dotazů Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 

[Zpracování výsledků vyhledávání](https://docs.microsoft.com/azure/search/search-pagination-page-layout)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png
[2]: ./media/search-lucene-query-architecture/azSearch-queryparsing-should2.png
[3]: ./media/search-lucene-query-architecture/azSearch-queryparsing-must2.png
[4]: ./media/search-lucene-query-architecture/azSearch-queryparsing-spacious2.png
