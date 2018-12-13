---
title: Architektura programového jádra (Lucene) hledání textu v plném znění – Azure Search
description: Vysvětlení konceptů Lucene dotaz zpracování a dokument načítání pro fulltextové vyhledávání v souvislosti s Azure Search.
manager: jlembicz
author: yahnoosh
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: jlembicz
ms.custom: seodec2018
ms.openlocfilehash: 8ca9fe72e4bd5272a5303b3bacd8c0960504789d
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53315790"
---
# <a name="how-full-text-search-works-in-azure-search"></a>Jak funguje fulltextové vyhledávání ve službě Azure Search

Tento článek je určený pro vývojáře, kteří potřebují lépe pochopit, jak funguje fulltextové vyhledávání Lucene ve službě Azure Search. Za dotazy na text Azure Search můžete bez potíží doručovat očekávané výsledky ve většině scénářů, ale čas od času může získat výsledek, který se zdá se, že "off" nějakým způsobem. V těchto situacích by bylo na pozadí ve čtyřech fázích provádění dotazů Lucene (dotazy, analýzy, lexikální analýzu, zdokumentujte párování, vyhodnocování) vám pomůže identifikovat konkrétní změny parametry dotazu nebo konfigurace indexu, které bude poskytovat požadované výsledek. 

> [!Note] 
> Služba Azure Search používá Lucene pro fulltextové vyhledávání, ale Lucene integrace není úplný. Jsme selektivně vystavení a rozšířit Lucene funkce, které umožňují scénáře důležité pro Azure Search. 

## <a name="architecture-overview-and-diagram"></a>Přehled architektury a diagram

Zpracování dotazu hledání textu v plném znění začíná Analýza textu dotazu extrahovat hledané výrazy. Vyhledávací web používá index pro načtení dokumentů s odpovídající podmínky. Jednotlivé výrazy jsou někdy rozdělit a rekonstituován do nového formuláře přetypovat širší net přes co lze považovat jako potenciální shoda. Je sada výsledků dotazu se potom seřazeno podle skóre relevance přiřazená každé jednotlivé odpovídající dokumentu. V horní části seznamu seřazený jsou vráceny do volající aplikace.

Revidovat, provádění dotazu má čtyři fáze: 

1. Analýza dotazu 
2. Lexikální analýzu 
3. Načtení dokumentu 
4. Vyhodnocování 

Následující diagram znázorňuje komponenty používané ke zpracování požadavku hledání. 

 ![Diagram architektury dotazů Lucene ve službě Azure Search][1]


| Klíčové komponenty | Funkční popis | 
|----------------|------------------------|
|**Analyzátory dotazu** | Samostatné výrazy z operátorů pro dotazování a vytvořte strukturu dotazu (strom dotazu) k odeslání do vyhledávací web. |
|**Analyzátory** | Proveďte lexikální analýzu na termíny dotazu. Tento proces může zahrnovat transformace, odebrání nebo rozšiřování termíny dotazu. |
|**Index** | Efektivní datová struktura, používá k ukládání a uspořádat prohledávatelná podmínky, které se extrahují z indexovaných dokumentů. |
|**Vyhledávací web** | Načte a skóre odpovídajících dokumentů na základě obsahu obrácenou indexu. |

## <a name="anatomy-of-a-search-request"></a>Anatomie žádost o vyhledávání

Žádost o vyhledávání je kompletní specifikaci co má být vrácen do sady výsledků dotazu. V nejjednodušší podobě je prázdný dotaz se žádná kritéria jakéhokoli druhu. Víc odpovídají realitě příklad obsahuje parametry, několik termíny dotazu, možná s oborem pro určité pole, s pravděpodobně výraz filtru a pořadí pravidel.  

V následujícím příkladu je žádost o vyhledávání můžete odeslat pomocí Azure Search [rozhraní REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents).  

~~~~
POST /indexes/hotels/docs/search?api-version=2017-11-11 
{  
    "search": "Spacious, air-condition* +\"Ocean view\"",  
    "searchFields": "description, title",  
    "searchMode": "any",
    "filter": "price ge 60 and price lt 300",  
    "orderby": "geo.distance(location, geography'POINT(-159.476235 22.227659)')", 
    "queryType": "full" 
 } 
~~~~

Pro tento požadavek vyhledávací web provede následující akce:

1. Filtruje dokumentů, jejichž cena je alespoň 60 USD a menší než 300 USD.
2. Spustí dotaz. V tomto příkladu vyhledávací dotaz se skládá z fráze a podmínkami: `"Spacious, air-condition* +\"Ocean view\""` (uživatelé obvykle nezadávejte interpunkční znaménka, ale včetně v příkladu, umožníte nám vysvětlují, jak ji zpracovat analyzátory). Pro tento dotaz vyhledá vyhledávací web popis a zadat název pole v `searchFields` pro dokumenty, které obsahují "Zobrazení na oceánských" a také výraz "velké" nebo na termíny, které začínají předponou "air-condition". `searchMode` Tak, aby odpovídaly na libovolný výraz (výchozí) nebo všechny z nich pro případy, kde není výslovně potřeba termín, který se používá parametr (`+`).
3. Objednávky, výsledná sada hotels ve vzdálenosti od dané zeměpisné oblasti umístění a vrátili, aby volající aplikace. 

Většina Tento článek se týká zpracování *vyhledávací dotaz*: `"Spacious, air-condition* +\"Ocean view\""`. Filtrování a řazení se pokyny neposkytují. Další informace najdete v tématu [referenční dokumentaci rozhraní API pro vyhledávání](https://docs.microsoft.com/rest/api/searchservice/search-documents).

<a name="stage1"></a>
## <a name="stage-1-query-parsing"></a>Fáze 1: Analýza dotazu 

Jak je uvedeno, řetězec dotazu je první řádek požadavku: 

~~~~
 "search": "Spacious, air-condition* +\"Ocean view\"", 
~~~~

Analyzátor dotazů rozděluje operátory (jako například `*` a `+` v příkladu) z hledání podmínky a deconstructs vyhledávacímu dotazu do *poddotazy* podporovaného typu: 

+ *výraz dotazu* samostatné podmínek (jako jsou velké)
+ *fráze dotazu* v uvozovkách podmínek (třeba zobrazení na oceánských)
+ *Předpona dotazu* podmínek, za nímž následuje prefixový operátor `*` (jako jsou air-condition)

Úplný seznam typů podporovaných dotazů najdete v části [syntaxe dotazů Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)

Operátory, které jsou přidružené k poddotaz určit, zda dotaz "musí být" nebo "by měl být" splněny v pořadí pro dokument k být považovány za shodné. Například `+"Ocean view"` platnost "musíte" `+` operátor. 

Poddotazy do analyzátor dotazů ke změně struktury *dotazu stromu* (vnitřní struktury představující dotazu) předá vyhledávací web. V první fázi analýzy dotazu stromu dotazu vypadá například takto.  

 ![Logická hodnota dotazu searchmode any][2]

### <a name="supported-parsers-simple-and-full-lucene"></a>Podporované analyzátory: Jednoduché a úplné Lucene 

 Služba Azure Search poskytuje dva jazyků jiný dotaz, `simple` (výchozí) a `full`. Tím, že nastavíte `queryType` parametr při zpracování požadavku hledání, dáte analyzátor dotazů jazyk dotazu, který si zvolíte, aby věděl, jak interpretovat, operátorů a syntaxe. [Jednoduchý dotazovací jazyk](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) je intuitivní a robustní, bývá často interpretace vstupu uživatele jako-je bez zpracování na straně klienta. Podporuje operátorů dotazu zkušenosti z vyhledávací web. [Úplné Lucene dotazovací jazyk](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search), který můžete získat tak, že nastavíte `queryType=full`, rozšiřuje jednoduchý dotazovací jazyk výchozí přidáním podpory pro další operátory a typy dotazů jako zástupný znak, přibližných shod, regulární výraz a dotazy v rámci pole. Například regulární výraz poslaná jednoduchá syntaxe dotazů je interpretován jako řetězec dotazu a není výraz. Příklad žádosti v tomto článku používá dotazovací jazyk úplné Lucene.

### <a name="impact-of-searchmode-on-the-parser"></a>Dopad searchMode analyzátoru 

Je jiný parametr požadavku hledání, který má vliv na analýze `searchMode` parametru. Určuje výchozí operátor logická dotazů: jakékoli (výchozí) nebo všechny.  

Když `searchMode=any`, což je výchozí, místo oddělovač mezi velké a air-condition je nebo (`||`), což odpovídá ukázkový text dotazu: 

~~~~
Spacious,||air-condition*+"Ocean view" 
~~~~

Explicitní operátory, jako například `+` v `+"Ocean view"`, jsou jednoznačné v dotazu Logická konstrukce (termín *musí* odpovídat). Méně zřejmé, je způsob interpretace zbývající podmínky: velké a air-condition. By měl vyhledávacího webu vyhledejte shody na oceánských zobrazení *a* velké *a* air-condition? Nebo by měl najít na oceánských zobrazení plus *buď jeden* zbývající podmínek? 

Ve výchozím nastavení (`searchMode=any`), vyhledávací web předpokládá širší výkladu. Buď pole *by měl* odpovídat, odráží sémantika "nebo". Počáteční dotaz stromu zobrazeny dříve, s nimi "by" operace, jsou uvedena výchozí.  

Předpokládejme, že teď nastavíme `searchMode=all`. V takovém případě znak je interpretován jako operace "a". Všechny zbývající podmínky musí být přítomen v dokumentu k vyfiltrování jako shoda. Výsledný dotaz ukázka by být interpretován takto: 

~~~~
+Spacious,+air-condition*+"Ocean view"  
~~~~

Upravený dotaz stromu pro tento dotaz by měl vypadat takto, kde odpovídajících dokumentů je průsečík všechny tři poddotazy: 

 ![Všechny logické dotazu searchmode][3]

> [!Note] 
> Výběr `searchMode=any` přes `searchMode=all` je nejlepší rozhodnutí byly přijaty spouštěním reprezentativní dotazů. Uživatelé, kteří můžou obsahovat operátory (běžné při hledání dokumentů ukládá) může být výsledky hledání intuitivnější Pokud `searchMode=all` informuje dotazu Logická konstrukce. Další informace o vztahu mezi `searchMode` a operátory, naleznete v tématu [jednoduchá syntaxe dotazů](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search).

<a name="stage2"></a>
## <a name="stage-2-lexical-analysis"></a>Fáze 2: Lexikální analýzu 

Lexikální analyzátory procesu *termín dotazy* a *frázi dotazy* po strukturovaného strom dotazů. Analyzátor přijímá textovými vstupy uvedené ve analyzátor, zpracovává text a potom odešle zpět tokenizovaného podmínky má být zahrnut do stromu dotazu. 

Je nejběžnější forma provést lexikální analýzu *pro jazykovou analýzu* které transformace dotazu podmínky na základě pravidel, které jsou specifické pro daný jazyk: 

* Snížení termín dotazu do formuláře kořenové slova 
* Odebrání nepotřebných slov (stopword, jako je například "the" nebo "a" v angličtině) 
* Rozdělení složené slovo do součásti 
* Malá, malá a velká písmena velká písmena slov 

Všechny tyto operace jsou obvykle vymazat rozdíly mezi zadávání textu uživatelem zadané a podmínky, které jsou uloženy v indexu. Tyto operace nad rámec zpracování textu a vyžadují hlubší znalosti samotný jazyk. Chcete-li přidat tato vrstva lingvistické povědomí, Azure Search podporuje dlouhý seznam [jazykové analyzátory](https://docs.microsoft.com/rest/api/searchservice/language-support) od společností Lucene a Microsoft.

> [!Note]
> Analýza požadavků může být v rozsahu od minimální do propracované v závislosti na vašem scénáři. Můžete řídit složitost provést lexikální analýzu, vyberete jednu z předdefinovaných analyzátory nebo tak, že vytvoříte vlastní [vlastní analyzátor](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search). Analyzátory jsou omezená na prohledávatelná pole a jsou určené jako součást definice pole. To umožňuje provést lexikální analýzu na základě na pole se liší. Tento parametr zadán, *standardní* slouží analyzátor Lucene.

V našem příkladu, před analýzy stromu počátečního dotazu má termín "Spacious" velké písmeno "S" a čárku, který analyzátor dotazů interpretuje jako součást pojem dotazu (čárkou není považováno za operátor query language).  

Pokud výchozí analyzátor zpracovává termín, bude malé písmeno "zobrazení na oceánských" a "velké" a odeberte znak čárky. Upravený dotaz stromu bude vypadat takto: 

 ![Logická dotaz s analýze podmínky][4]

### <a name="testing-analyzer-behaviors"></a>Testování v analyzátoru chování 

Chování analyzátor lze otestovat pomocí [analyzovat rozhraní API](https://docs.microsoft.com/rest/api/searchservice/test-analyzer). Zadejte text, který chcete analyzovat Pokud chcete zobrazit, co bude generovat podmínky daný analyzátor. Například pokud chcete zobrazit, jak by standardní analyzátor zpracovat text "air-condition", můžete vydat následující žádosti:

~~~~
{ 
    "text": "air-condition",
    "analyzer": "standard"
}
~~~~

Standardní analyzátor konce vstupního textu do následujících dvou tokenů poznámky s vlastností, jako je počáteční a koncové posunutí (používá se pro zvýrazňování shod), stejně jako jejich pozice (použije k porovnání s fráze):

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

### <a name="exceptions-to-lexical-analysis"></a>Výjimky provést lexikální analýzu 

Lexikální analýzu platí pouze pro typy dotazů, které vyžadují úplný podmínky – termín dotazu nebo dotaz frázi. Nevztahuje se na typy dotazů s neúplné podmínky – předpona dotazu, dotaz zástupný znak, regulární výraz dotazu – nebo přibližné dotazu. Tyto typy, včetně předpony dotazu se termínem dotazů `air-condition*` v našem příkladu jsou přidány přímo do stromu dotazu obcházení fázi analýzy. Pouze transformace provedla termíny dotazu z těchto typů je předpoklady.

<a name="stage3"></a>

## <a name="stage-3-document-retrieval"></a>Fáze 3: Načtení dokumentu 

Načítání dokumentu odkazuje na hledání dokumentů s odpovídajícími podmínky v indexu. Tato fáze nejlépe odhalíte obsahuje příklad. Začněme indexu hotels následující jednoduché schéma: 

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

Dál Předpokládejme, že tento index obsahuje následující čtyři dokumenty: 

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

Informace o tom načítání, pomáhá zjistit pár základní informace o indexování. Jednotky úložiště je obrácenou index, jeden pro každý prohledávatelná pole. V rámci obrácenou indexu je seřazený seznam všechny podmínky ze všech dokumentů. Každý termín se mapuje na seznamu dokumentů, ve kterých se vyskytuje, zřejmá jako v následujícím příkladu.

K vytvoření podmínky v indexu obrácenou, vyhledávací web provádí provést lexikální analýzu obsah dokumentů, podobně jako na co se stane během zpracování dotazu:

1. *Textovými vstupy* jsou předány do analyzátor, nižší malými a velkými písmeny, odebrána interpunkce a tak dále, v závislosti na konfiguraci analyzátoru. 
2. *Tokeny* výstup analýzy textu.
3. *Podmínky* jsou přidány do indexu.

Je běžné, ale nejsou vyžadovány, používat stejné analyzátory pro hledání a indexování operace tak, aby výrazy vypadat více jako podmínky v indexu.

> [!Note]
> Služba Azure Search umožňuje určit různé analyzátory pro indexování a vyhledávání prostřednictvím dalších `indexAnalyzer` a `searchAnalyzer` pole parametrů. Pokud tento parametr zadán, analyzátor sadu s `analyzer` vlastnost se používá pro indexování a vyhledávání.  

**Převedený index pro příklad dokumenty**

Vrací pro náš příklad pro **název** pole, index obrácenou vypadá například takto:

| Označení | Seznam dokumentů |
|------|---------------|
| atman | 1 |
| Beach | 2 |
| hotelu | 1, 3 |
| oceánu | 4  |
| playa | 3 |
| možnost | 3 |
| Retreat | 4 |

V poli s názvem pouze *hotelu* zobrazí dva dokumenty: 1, 3.

Pro **popis** pole indexu je následujícím způsobem:

| Označení | Seznam dokumentů |
|------|---------------|
| AIR | 3
| a | 4
| Beach | 1
| záleží | 3
| pohodlné | 3
| vzdálenost | 1
| ostrov | 2
| kauaʻi | 2
| umístěné | 2
| – sever | 2
| oceánu | 1, 2, 3
| z | 2
| zapnuté |2
| Nastaví tichý | 4
| místnosti  | 1, 3
| secluded | 4
| pobřeží | 2
| Velké | 1
| Na | 1, 2
| na | 1
| zobrazit | 1, 2, 3
| procházení | 1
| with | 3


**Odpovídající výrazy proti indexované podmínky**

Zadaný výše obrácenou indexů, Pojďme vraťte se na ukázkový dotaz a naleznete v tématu Jak odpovídajících dokumentů pro náš příklad dotazu nebyly nalezeny. Připomínáme, že stromu konečný dotaz vypadá takto: 

 ![Logická dotaz s analýze podmínky][4]

Při provádění dotazu jednotlivé dotazy provádějí proti prohledávatelná pole nezávisle na sobě. 

+ Odpovídá "velké" TermQuery, zdokumentujte 1 (Atman hotelu). 

+ PrefixQuery, "air-condition *", neodpovídá žádné dokumenty. 

  Toto je chování, které někdy mate vývojáři. I když existuje klimatizovaným termín v dokumentu, rozdělí se na dvě podmínky výchozí analyzátor. Připomínáme, že předpona dotazů, které obsahují částečnou podmínky, neanalyzují. Proto jsou podmínky s předponou "air-condition" vyhledána v obrácenou index a nebyla nalezena.

+ PhraseQuery "zobrazení na oceánských", vyhledá na oceánských"podmínky" a "Zobrazit" a zkontroluje blízkosti podmínky v původním dokumentu. Dokumenty, 1, 2 a 3 odpovídají tomuto dotazu v poli popisu. Má oceánských termín v názvu Všimněte si, že dokument 4, ale není považovány za shodné, protože právě se díváme pro frázi "zobrazení na oceánských" namísto jednotlivých slov. 

> [!Note]
> Vyhledávacího dotazu se provede nezávisle na všechna prohledatelná pole v indexu Azure Search není-li omezit pole nastaven s `searchFields` parametru, jak je znázorněno v příkladu požadavku hledání. Dokumenty, které splňují v žádném z vybraných polí jsou vráceny. 

Na celém dotyčný dotazu jsou dokumenty, které odpovídají 1, 2, 3. 

## <a name="stage-4-scoring"></a>Fáze 4: Vyhodnocování  

Skóre relevance se přiřadí každému dokumentu v sadě výsledků vyhledávání. Funkce skóre relevance je na vyšší pořadí těchto dokumentů, které nejlépe odpovídají otázku uživatele vyjádřené pomocí vyhledávacího dotazu. Skóre se počítá podle statistické vlastnosti podmínek, které odpovídají. V jádru bodování vzorec je [TF/IDF (termín frekvence inverzní dokumentu frekvence)](https://en.wikipedia.org/wiki/Tf%E2%80%93idf). V dotazech obsahující vzácné a běžné podmínky TF/IDF podporuje výsledky obsahující výjimečných termín. Například v hypotetický index s všechny články Wikipedia z dokumentů odpovídající zadaným dotaz *ředitel*, dokumentů, které vyhovují na *ředitel* jsou považovány za relevantní více než dokumenty porovnávání *s*.


### <a name="scoring-example"></a>Příklad bodování

Odvolat tři dokumenty, které odpovídají náš příklad dotazu:
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

Dokument 1 namapovat dotaz nejlépe, protože obě termín *velké* a požadované frázi *zobrazení na oceánských* vyskytují v poli popisu. Následující dva dokumenty odpovídá pouze frázi *zobrazení na oceánských*. Může být překvapivé, že skóre relevance pro dokument 2 a 3 se liší, i když jejich odpovídající dotaz stejným způsobem. Je to způsobeno bodování vzorec obsahuje více součástí než jenom TF/IDF. Dokument 3 v tomto případě byl přiřazen mírně vyšší ohodnocení, protože je kratší popis. Další informace o [od Lucene praktické vyhodnocování vzorec](https://lucene.apache.org/core/4_0_0/core/org/apache/lucene/search/similarities/TFIDFSimilarity.html) pochopit, jak délku pole a dalších faktorů, mohou mít vliv na skóre relevance.

Některé typy (zástupný znak, předpony, regulární výraz) dotazů vždy měli přispívat konstantní score celkové skóre dokumentu. To umožňuje nalezených položek prostřednictvím rozšíření dotazu mají být zahrnuty ve výsledcích, ale bez ovlivnění pořadí. 

Příklad ukazuje, proč je to důležité. Vyhledávání pomocí zástupných znaků, včetně předpony vyhledávání nejsou jednoznačná podle definice, protože vstup je dílčí řetězec s potenciální shody na velmi velké množství různých podmínek (zvažte zadání "tour *", s shody na "kurzy", "tourettes", a " tourmaline"). Vzhledem k povaze tyto výsledky, neexistuje žádný způsob, jak rozumně odvodit podmínky, které jsou větší hodnotu než jiné. Z tohoto důvodu jsme ignorovat termín frekvence, při vyhodnocování výsledky v dotazech typy zástupných znaků, předpony a regulární výraz. V požadavku vícedílný hledání, která zahrnuje částečným i úplným podmínky jsou součástí výsledky z částečné vstup s konstantní score, aby Posun směrem k potenciálně neočekávané shody.

### <a name="score-tuning"></a>Skóre ladění

Existují dva způsoby, jak ladit skóre relevance ve službě Azure Search:

1. **Profily skórování** podporovat dokumenty v seřazený seznam výsledků na základě sady pravidel. V našem příkladu jsme zvažte dokumenty, které odpovídají v poli s názvem významnější než dokumenty, které odpovídají v poli popisu. Kromě toho pokud nazvali index pole s cenou pro každý hotelu, může převedeme dokumentů s nižší cenu. Zjistěte, jak Další [přidání profilů vyhodnocování do indexu vyhledávání.](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)
2. **Zvýšení skóre termínu** (k dispozici pouze v syntaxi dotazů Lucene úplná) poskytuje zvýšení skóre operátor `^` , který lze použít u všech součástí stromu dotazu. V našem příkladu, namísto vyhledávání na této předponě *air-condition*\*, jeden může vyhledáte termín přesné *air-condition* nebo předponu, ale dokumenty, které odpovídají na přesné termín vyšší seřazené podle použití boost termín dotazu: * air podmínku ^ 2 || AIR-Condition **. Další informace o [zvýšení skóre termínu](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search#bkmk_termboost).


### <a name="scoring-in-a-distributed-index"></a>Vyhodnocování v distribuované indexu

Všechny indexy ve službě Azure Search se automaticky rozdělit do několika horizontálními oddíly, což nám rychle distribuovat mezi více uzlů index při škálování služby směrem nahoru nebo vertikálně snížit kapacitu. Při vydání žádost o vyhledávání, to je vydaný pro každý horizontální oddíl nezávisle na sobě. Výsledky z jednotlivých horizontálních oddílů jsou pak sloučit a seřazené podle skóre (Pokud není definováno žádné jiné pořadí). Je důležité vědět, vyhodnocení funkce váhy dotazovat termín frekvence před jeho četnost inverzní dokumentu ve všech dokumentech v rámci horizontálního oddílu, ne napříč všemi horizontálními oddíly.

To znamená, že skóre relevance *může* být různé pro stejné dokumenty, pokud jsou umístěné v různých horizontálních oddílech. Naštěstí tyto rozdíly jsou obvykle zmizí z důvodu více i termín distribuce roste počet dokumentů v indexu. Není možné předpokládat, že na horizontální oddíl se umístí jakéhokoliv dokumentu. Však předpokládáme, že nedojde ke změně klíč dokumentu a jeho se vždycky přiřadí stejné ID horizontálního oddílu.

Obecně platí skóre dokument není nejlepší atribut pro řazení dokumenty, pokud je důležité pořadí stabilitu. Například zadány dva dokumenty s shodné skóre, není zaručeno které z nich jako první se objeví v následné spuštění nástroje stejný dotaz. Dokument skóre měl dát pouze obecném smyslu tento pojem dokumentu podle relevance vzhledem k jiné dokumenty v sadě výsledků.

## <a name="conclusion"></a>Závěr

Úspěch internet vyhledávací weby vyvolalo očekávání pro fulltextové vyhledávání přes osobní data. Pro téměř libovolný typ rozhraní pro hledání teď Očekáváme, že modul pochopit naším záměrem, i když podmínky jsou překlepy nebo neúplné. Dokonce i bychom předpokládali shody na základě blízkosti ekvivalentní termíny nebo synonyma, které jsme nikdy skutečně zadali.

Z hlediska technické fulltextové vyhledávání je velice složitých vyžadující sofistikované pro jazykovou analýzu a systematický přístup k takovým způsobem, který zjištění využitelných, rozbalte a transformovat termíny dotazu k poskytování relevantní výsledek zpracování. Vzhledem ke složitosti vyplývajících, existuje mnoho faktorů, které může mít vliv na výsledek dotazu. Z tohoto důvodu strávíte spoustu času pochopit mechanics fulltextové vyhledávání nabízí hmatatelnými výhody, při pokusu o seznámení se základními neočekávané výsledky.  

Tento článek prozkoumali fulltextové vyhledávání v rámci služby Azure Search. Doufáme, že poskytuje dostatečné znalosti potřebné k rozpoznat možné příčiny a řešení pro řešení běžných problémů dotazu. 

## <a name="next-steps"></a>Další postup

+ Vytvoření ukázkového indexu, vyzkoušejte si různé dotazy a zkontrolovat výsledky kontroly. Pokyny najdete v tématu [sestavení a dotazování indexu na portálu](search-get-started-portal.md#query-index).

+ Zkuste syntaxi dalšího dotazu [vyhledávání dokumentů](https://docs.microsoft.com/rest/api/searchservice/search-documents#examples) vzorový oddíl nebo z [jednoduchá syntaxe dotazů](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) v Průzkumníku služby Search na portálu.

+ Kontrola [profily skórování](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) Pokud chcete ladit hodnocení v vaše vyhledávací aplikace.

+ Zjistěte, jak použít [specifické pro jazyk lexikální analyzátory](https://docs.microsoft.com/rest/api/searchservice/language-support).

+ [Konfigurace vlastní analyzátory](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search) pro minimální zpracování nebo speciální zpracování na konkrétních polí.

+ [Porovnat standardní a angličtině analyzátory](http://alice.unearth.ai/)) vedle sebe na tomto webu ukázku. 

## <a name="see-also"></a>Další informace najdete v tématech

[Hledání dokumentů rozhraní REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents) 

[Jednoduchá syntaxe dotazů](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 

[Úplná syntaxe dotazů Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 

[Zpracování výsledků vyhledávání](https://docs.microsoft.com/azure/search/search-pagination-page-layout)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png
[2]: ./media/search-lucene-query-architecture/azSearch-queryparsing-should2.png
[3]: ./media/search-lucene-query-architecture/azSearch-queryparsing-must2.png
[4]: ./media/search-lucene-query-architecture/azSearch-queryparsing-spacious2.png
