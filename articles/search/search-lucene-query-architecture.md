---
title: Úplný text search engine (Lucene) architektura ve službě Azure Search | Microsoft Docs
description: Vysvětlení Lucene dotaz zpracování a dokumentu načtení koncepty pro fulltextové vyhledávání v souvislosti s Azure Search.
manager: jlembicz
author: yahnoosh
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 04/06/2017
ms.author: jlembicz
ms.openlocfilehash: d05aade97ea5713b63b921d9e39d4df0c30b8fa0
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="how-full-text-search-works-in-azure-search"></a>Jak úplné textové vyhledávání funguje ve službě Azure Search

Tento článek je pro vývojáře, kteří potřebují lépe pochopili, jak funguje Lucene fulltextové vyhledávání ve službě Azure Search. Pro dotazy text Azure Search ve většině scénářů bezproblémově dodá očekávané výsledky, ale někdy může získat výsledek, který se zdá, že "off" nějakým způsobem. V těchto situacích s pozadí ve čtyři fáze provádění dotazů Lucene (dotaz analýzy, lexikální analýzy, dokumentů párování, vyhodnocování) můžete identifikovat konkrétní změny parametry dotazu nebo konfigurace indexu, která bude poskytovat požadovanou výsledek. 

> [!Note] 
> Služba Azure Search používá Lucene pro fulltextové vyhledávání, ale Lucene integrace není vyčerpávající. Jsme selektivně vystavit a rozšiřovat funkce Lucene k povolení scénářů důležité do služby Azure Search. 

## <a name="architecture-overview-and-diagram"></a>Přehled architektury a diagram

Zpracování textu v plném znění vyhledávací dotaz začíná Analýza textu dotazu k extrakci hledaný text. Na vyhledávací web používá index k získávání dokumentů s odpovídajícími položkami. Konkrétní dotaz podmínky jsou někdy rozdělit a rekonstituovaných do nové formuláře přetypovat širší net přes co může považovat za potenciální shody. Je sada výsledků dotazu je pak seřazené podle skóre relevance přiřazené jednotlivé odpovídající dokumenty. Ty v horní části seznamu seřazený se vrátíte na volající aplikace.

Revidovat, provádění dotazů má čtyři fáze: 

1. Analýza dotazu 
2. Lexikální analýzy 
3. Načtení dokumentu 
4. Vyhodnocování 

Následující diagram znázorňuje komponenty, používá ke zpracování žádost o vyhledávání. 

 ![Diagram architektury dotazů Lucene ve službě Azure Search][1]


| Klíčové komponenty | Funkční popis | 
|----------------|------------------------|
|**Dotaz analyzátory** | Operátory dotazu nezávislá na infrastruktuře vyhledávacích dotazů a vytvořit strukturu dotazu (stromu dotazu) k odeslání na vyhledávací web. |
|**Analyzátory** | Lexikální analýzám na vyhledávacích dotazů. Tento proces může zahrnovat transformace, odebrání nebo rozšiřování vyhledávacích dotazů. |
|**Index** | Efektivní datová struktura, které slouží k ukládání a uspořádání prohledávatelné podmínky extrahovat z indexovaného dokumentů. |
|**Vyhledávací web** | Načte a skóre na základě obsahu obráceným indexu odpovídající dokumenty. |

## <a name="anatomy-of-a-search-request"></a>Anatomie žádost o vyhledávání

Žádost o vyhledávání je dokončení specifikaci co má být vrácen sadě výsledků dotazu. V nejjednodušší podobě je prázdný dotaz s žádná kritéria jakéhokoli druhu. Realističtější příklad obsahuje parametry, několika vyhledávacích dotazů, případně omezená na určitá pole s pravděpodobně výraz filtru a řazení pravidla.  

Následující příklad je žádost o vyhledávání můžete odeslat pomocí Azure Search [REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents).  

~~~~
POST /indexes/hotels/docs/search?api-version=2016-09-01 
{  
    "search": "Spacious, air-condition* +\"Ocean view\"",  
    "searchFields": "description, title",  
    "searchMode": "any",
    "filter": "price ge 60 and price lt 300",  
    "orderby": "geo.distance(location, geography'POINT(-159.476235 22.227659)')", 
    "queryType": "full" 
 } 
~~~~

Pro tento požadavek na vyhledávací web provede následující akce:

1. Filtruje dokumentů, jejichž cena je alespoň $60 a menší než 300 USD.
2. Provede daný dotaz. V tomto příkladu dotaz vyhledávání se skládá z frází a podmínky: `"Spacious, air-condition* +\"Ocean view\""` (uživatelé obvykle nezadávejte interpunkční znaménka, ale včetně v příkladu umožňuje vysvětlují, jak ji zpracovat analyzátorů). Pro tento dotaz na vyhledávací web vyhledá popis a název pole zadaný v `searchFields` pro dokumenty, které obsahují "Oceánu zobrazení" a dále na termín "velké" nebo na podmínky, které začínají předponou "air-condition". `searchMode` Parametr se používá k odpovídat na všechny termín (výchozí) nebo všechny z nich pro případy, kdy není explicitně požadované termín (`+`).
3. Příkazy, které výsledná sada hotels podle blízkosti umístění dané Geografie a poté vrácen volající aplikace. 

Většina Tento článek se týká zpracování *vyhledávací dotaz*: `"Spacious, air-condition* +\"Ocean view\""`. Filtrování a řazení jsou mimo rozsah. Další informace najdete v tématu [referenční dokumentace rozhraní API pro vyhledávání](https://docs.microsoft.com/rest/api/searchservice/search-documents).

<a name="stage1"></a>
## <a name="stage-1-query-parsing"></a>Fáze 1: Analýza dotazu 

Jak jsme uvedli, řetězec dotazu je první řádek žádosti: 

~~~~
 "search": "Spacious, air-condition* +\"Ocean view\"", 
~~~~

Analyzátor dotazu odděluje operátory (například `*` a `+` v příkladu) z hledání podmínky a deconstructs vyhledávací dotaz do *poddotazy* podporovaného typu: 

+ *Termín dotazu* pro samostatné podmínky (třeba velké)
+ *dotaz frázi* uvozovkách podmínek (podobně jako zobrazení oceánu)
+ *Předpona dotazu* podmínek, za nímž následuje operátor předponu `*` (jako jsou air-condition)

Úplný seznam podporovaných dotazu typy najdete v části [syntaxe dotazů Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)

Operátory přidružené poddotazu určit, zda dotaz "musí být" nebo "by měla být" uspokojit, aby dokument být považovány za shodné. Například `+"Ocean view"` je "musí" kvůli `+` operátor. 

Analyzátor dotazu ke změně struktury poddotazy do *dotazu stromu* (interní strukturu představující dotazu) předává vyhledávací web. V první fázi dotazu analýzy stromu dotazu vypadat třeba takto.  

 ![Logická hodnota dotazu searchmode všechny][2]

### <a name="supported-parsers-simple-and-full-lucene"></a>Podporované analyzátory: jednoduchý a úplné Lucene 

 Služba Azure Search zpřístupní dvou různých dotazu jazyků, `simple` (výchozí) a `full`. Nastavením `queryType` parametr s vaši žádost o vyhledávání se zjistit analyzátor dotazu jazyk dotazu, který zvolíte, aby věděl, že může interpretace operátory a syntaxe. [Jednoduchý dotaz jazyka](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) je intuitivní a robustní, často vhodný interpretace vstupu uživatele jako-je bez zpracování na straně klienta. Podporuje operátory dotazu známých z webové vyhledávacích webů. [Úplné Lucene dotazovací jazyk](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search), které dostanete tak, že nastavení `queryType=full`, rozšiřuje výchozí jazyk jednoduchý dotaz přidáním podpory pro další operátory a typy dotazů jako zástupný znak, přibližné, regulární výraz a dotazy v rámci pole. Například by odeslaných za jednoduchá syntaxe dotazů regulární výraz vyhodnocen jako řetězec dotazu a není výraz. Žádost o příklad v tomto článku používá úplné Lucene dotazovací jazyk.

### <a name="impact-of-searchmode-on-the-parser"></a>Dopad searchMode analyzátoru 

Je jiný parametr žádost o vyhledávání, který ovlivňuje analýza `searchMode` parametr. Ovládá výchozí operátor pro logickou dotazy: všechny (výchozí) nebo všechny.  

Když `searchMode=any`, což je výchozí, místo oddělovač mezi velké a air-condition nebo (`||`), provedení ekvivalentní ukázkový text dotazu: 

~~~~
Spacious,||air-condition*+"Ocean view" 
~~~~

Explicitní operátory, jako například `+` v `+"Ocean view"`, jsou jednoznačné v dotazu Logická konstrukce (termín *musí* odpovídat). Méně zřejmé je interpretace zbývající podmínky: velké a air-condition. Měli vyhledávacího webu najít odpovídá na zobrazení oceánu *a* velké *a* air-condition? Nebo by měl zjistit oceánu zobrazení plus *buď jeden* zbývající podmínek? 

Ve výchozím nastavení (`searchMode=any`), na vyhledávací web předpokládá širší interpretace. Buď pole *by měl* odpovídat, které odráží sémantiku "nebo". Stromu počátečního dotazu zobrazeny dříve, s dva "by měl" operations, jsou uvedena výchozí.  

Předpokládejme, že teď nastavujeme `searchMode=all`. V takovém případě se místo interpretována jako operace "a". Každý zbývající podmínek musí být přítomen v dokumentu ke kvalifikaci jako shoda. Výsledný ukázkový dotaz by interpretovat následujícím způsobem: 

~~~~
+Spacious,+air-condition*+"Ocean view"  
~~~~

Strom upravený dotaz pro tento dotaz bude následující, kde odpovídající dokument je průnik všechny tři poddotazy: 

 ![Logická hodnota dotazu searchmode všechny][3]

> [!Note] 
> Výběr `searchMode=any` přes `searchMode=all` je nejlepší rozhodnutí byly přijaty spuštěním reprezentativní dotazy. Uživatelé, kteří jsou pravděpodobně patří operátory (společný při vyhledávání dokument uloží) může intuitivnější výsledky pokud `searchMode=all` informuje konstrukce boolean dotazu. Další informace o vztahu mezi `searchMode` a operátory, najdete v části [jednoduchá syntaxe dotazů](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search).

<a name="stage2"></a>
## <a name="stage-2-lexical-analysis"></a>Fáze 2: Lexikální analysis 

Lexikální analyzátorů proces *termín dotazy* a *fráze dotazy* po strukturovaná stromu dotazu. Analyzátor přijímá vstupní textové hodnoty uvedené ve analyzátor, zpracuje text a pak odešle zpět tokenizovaného podmínky mají být zahrnuty do stromu dotazu. 

Nejběžnější formu lexikální analýzy je *lingvistické analysis* který transformací dotaz podmínky na základě pravidel, které jsou specifické pro daný jazyk: 

* Snižuje termín dotazu do formuláře kořenové slova 
* Odebrání nepotřebných slova (stopslova, jako je například "na" nebo "a" v angličtině) 
* Rozdělení složené slovo do součásti 
* Nižší velká slova velká a malá písmena 

Všechny tyto operace jsou obvykle vymazat rozdíly mezi text vstup uživatelem a podmínky, které jsou uloženy v indexu. Tyto operace nad rámec zpracování textu a vyžaduje zevrubnou znalost jazyka sám sebe. Pokud chcete přidat tuto vrstvu lingvistické sledování, podporuje Azure Search dlouhý seznam [analyzátory jazyka](https://docs.microsoft.com/rest/api/searchservice/language-support) Lucene a společnosti Microsoft.

> [!Note]
> Analýza požadavků může být v rozsahu od minimální do vypracovala v závislosti na vašem scénáři. Můžete řídit složitost lexikální analýzy vyberete jeden z předdefinovaných analyzátorů nebo vytvoření vlastních [vlastní analyzátor](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search). Analyzátory jsou omezená na prohledatelná pole a jsou zadané jako součást definice pole. To umožňuje měnit lexikální analýzy na základě za pole. Tento parametr zadán, *standardní* Lucene analyzer je použít.

V našem příkladu před analýzy stromu počátečního dotazu má termín "Spacious, velká písmena"S"a čárkami, který analyzátor dotazu interpretuje jako součást termín dotazu (čárkou nepovažuje operátor dotazu jazyka)".  

Když analyzátor výchozí zpracovává termín, bude malá písmena "zobrazení oceánu" a "velké" a odeberte čárku. Stromu upravené dotazu bude vypadat takto: 

 ![Logická hodnota dotaz s analyzovaných podmínky][4]

### <a name="testing-analyzer-behaviors"></a>Testování Analyzátor chování 

Chování analyzátor lze otestovat pomocí [analyzovat rozhraní API](https://docs.microsoft.com/rest/api/searchservice/test-analyzer). Zadejte text, který chcete analyzovat Pokud chcete zobrazit, co bude generovat podmínky zadané analyzátor. Například informace o tom, jak by standardní analyzátor zpracovat text "air-condition", můžete použít následující požadavek:

~~~~
{ 
    "text": "air-condition",
    "analyzer": "standard"
}
~~~~

Standardní analyzátor dělí vstupního textu do následujících dvou tokenů, zadávání poznámek o nich s atributy, jako je počáteční a koncové posunutí (používá se pro přístupů zvýraznění), jakož i jejich polohu (používá se pro párování frázi):

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

### <a name="exceptions-to-lexical-analysis"></a>Výjimky lexikální analýzy 

Lexikální analýzy se vztahuje pouze na typy dotazů, které vyžadují úplný podmínky – dotazu termín nebo frázi dotazu. Netýká typy dotazů s neúplné podmínkami – předpona dotazu, dotaz zástupný znak, regulární výraz dotazu – nebo přibližné dotazu. Ty typy, včetně předponu dotazu s termín dotazů *air-condition\**  v našem příkladu jsou přidány přímo do stromu dotazu obcházení fázi analýzy. Pouze transformaci u vyhledávacích dotazů těchto typů provést, je předpoklady.

<a name="stage3"></a>

## <a name="stage-3-document-retrieval"></a>Fáze 3: Načtení dokumentu 

Načtení dokumentu odkazuje na hledání dokumentů s odpovídajícím podmínky v indexu. Tato fáze odhalíte nejlépe v příkladu. Začněme s indexem hotels s následující jednoduché schématu: 

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

Další Předpokládejme, že tento index obsahuje následující čtyři dokumenty: 

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

**Jak jsou indexované podmínky**

Zjistit, načtení, je důležité znát několik základní informace o indexování. Jednotka úložiště je obráceným index, jeden pro každé prohledávatelné pole. V rámci obráceným index je seřazený seznam všechny podmínky ze všech dokumentů. Každému termínu se mapuje na seznam dokumentů, v nichž se vyskytuje, zřejmá jako v následujícím příkladu.

K vytvoření podmínky převedený index, provede na vyhledávací web lexikální analýzu přes obsah dokumentů, podobně jako co se stane, že při zpracování dotazu:

1. *Text vstupy* jsou předávány analyzátor použita nižší odstraní interpunkční znaménka, a tak dále, v závislosti na konfiguraci analyzátor. 
2. *Tokeny* jsou výstup analýzy text.
3. *Podmínky* jsou přidány do indexu.

Je běžné, ale nejsou vyžadovány, používat stejné analyzátory pro vyhledávání a indexování operations tak, aby vypadal jako podmínky uvnitř index další podmínky dotazu.

> [!Note]
> Služba Azure Search umožňuje určit různé analyzátory pro indexování a hledání prostřednictvím další `indexAnalyzer` a `searchAnalyzer` pole parametrů. Pokud tento parametr zadán, analyzátor nastavit `analyzer` vlastnost se používá pro indexování a vyhledávání.  

**Převedený index pro příklad dokumenty**

Návrat na našem příkladu pro **název** pole, obráceným index vypadat třeba takto:

| Označení | Seznam dokumentů |
|------|---------------|
| atman | 1 |
| plážový | 2 |
| hotelů | 1, 3 |
| oceánu | 4  |
| playa | 3 |
| možnost | 3 |
| Retreat | 4 |

V poli s názvem pouze *hotelů* se zobrazí v dva dokumenty: 1, 3.

Pro **popis** pole indexu je následujícím způsobem:

| Označení | Seznam dokumentů |
|------|---------------|
| letecké | 3
| a | 4
| plážový | 1
| záleží | 3
| možnost | 3
| Vzdálenost | 1
| Island | 2
| kauaʻi | 2
| nachází | 2
| – sever | 2
| oceánu | 1, 2, 3
| z | 2
| zapnuto |2
| quiet | 4
| místnosti  | 1, 3
| secluded | 4
| pobřeží | 2
| Velké | 1
| na | 1, 2
| na | 1
| zobrazit | 1, 2, 3
| procházení | 1
| with | 3


**Odpovídající vyhledávacích dotazů vůči indexované podmínky**

Zadané obráceným indexy výše, můžeme vraťte se na ukázkový dotaz a v tématu Jak odpovídající dokumenty pro náš příklad dotazu nebyly nalezeny. Odvolat, aby stromu poslední dotaz vypadá takto: 

 ![Logická hodnota dotaz s analyzovaných podmínky][4]

Během provádění dotazu jednotlivé dotazy jsou u spustit prohledatelná pole nezávisle. 

+ "Velké", odpovídá TermQuery, dokumentů 1 (hotelů Atman). 

+ PrefixQuery, "air-condition *", se neshoduje se žádné dokumenty. 

  Toto je chování, které někdy confuses vývojáři. I když termín klimatizovaným existuje v dokumentu, je rozdělený do dvou podmínky podle výchozí analyzátor. Odvolat, že předpona dotazy, které obsahují částečnou podmínky, nejsou analýza. Proto jsou podmínky s předponou "air-condition" prohledávat převedený index a nebyl nalezen.

+ PhraseQuery, "oceánu zobrazení" podmínky "oceánu" a "Zobrazit" a, zkontroluje blízko podmínky v původním dokumentu. Dokumenty, 1, 2 a 3 odpovídat tento dotaz do pole Popis. Všimněte si dokument 4 má oceánu termín v názvu, ale není považovány za shodné, jako jsme to potřebné pro frázi "zobrazení oceánu" místo jednotlivých slov. 

> [!Note]
> Vyhledávací dotaz je spustit nezávisle pro všechna prohledatelná pole v indexu Azure Search není-li omezit pole nastavit `searchFields` parametr, jak ukazuje následující příklad požadavek hledání. Dokumenty, které odpovídají v žádném z vybraných polí jsou vráceny. 

Celkově v dotazu, jsou dokumenty, které odpovídají 1, 2, 3. 

## <a name="stage-4-scoring"></a>Fáze 4: vyhodnocování  

Každému dokumentu v sadě výsledků hledání je přiřazen relevance skóre. Funkce skóre relevance je vyšší pořadí těchto dokumentů, které nejlépe odpověď na otázku uživatele jako vyjádřená vyhledávací dotaz. Výpočet skóre je založen na statistické vlastnosti podmínky, které odpovídá. Základem vyhodnocování vzorec je [TF/IDF (termín frekvence inverzní dokumentu frekvenci)](https://en.wikipedia.org/wiki/Tf%E2%80%93idf). V dotazech obsahující výjimečná a běžné podmínky TF/IDF zvýší úroveň výsledky obsahující výjimečných podmínek. Například v hypotetický index s všechny články Wikipedia z dokumentů odpovídající zadaným dotaz *ředitel*, dokumentů, které vyhovují na *ředitel* jsou považovány za relevantní více než dokumenty porovnávání *s*.


### <a name="scoring-example"></a>Příklad vyhodnocování

Odvolat tři dokumenty, které odpovídá náš příklad dotazu:
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

Dokument 1 namapovat dotaz nejlépe, protože obě termín *velké* a požadované frázi *oceánu zobrazení* dojít do pole Popis. Následující dva dokumenty odpovídat jenom fráze *oceánu zobrazení*. Může to být překvapivé, relevance skóre pro dokument 2 a 3 je jiné, přestože se shodoval dotaz stejným způsobem. Je to proto, že vyhodnocování vzorec obsahuje více součástí než právě TF/IDF. V takovém případě dokumentu 3 byl přiřazen mírně vyšší skóre, protože jeho popis je kratší. Další informace o [Lucene je praktické vyhodnocování vzorec](https://lucene.apache.org/core/4_0_0/core/org/apache/lucene/search/similarities/TFIDFSimilarity.html) pochopit, jak můžete skóre relevance ovlivnit délka pole a dalších faktorů.

Některé typy (zástupný znak, předpony, regulární výraz) dotazů vždy přispívat konstantní skóre na celkové skóre dokumentu. To umožňuje odpovídá nalezen prostřednictvím rozšíření dotazu mají být zahrnuty do výsledků, ale bez vlivu hodnocení. 

Příklad ukazuje, proč to záleží. Vyhledávání pomocí zástupných znaků, včetně předpony hledání, jsou nejednoznačné podle definice, protože vstup je částečné řetězce s potenciální odpovídá na velký počet různorodých podmínky (zvažte vstup "prohlídka *", s odpovídá na "kurzy", "tourettes" nalezena, a " tourmaline"). Vzhledem k povaze těchto výsledků, neexistuje žádný způsob, jak to bude přiměřeně infer – které podmínky jsou cenné více než jiné. Z tohoto důvodu jsme ignorovat termín frekvence, při vyhodnocování výsledky v dotazech typy zástupný znak, předpony a regulární výraz. V žádosti o více částech hledání obsahující částečným i úplným podmínky jsou součástí výsledků z částečné vstup s konstantní skóre, aby se zabránilo odchylka směrem potenciálně neočekávané odpovídá.

### <a name="score-tuning"></a>Skóre ladění

Existují dva způsoby, jak ladit relevance skóre ve službě Azure Search:

1. **Vyhodnocování profily** povýšit dokumenty v seřazený seznam výsledků na základě sady pravidel. V našem příkladu jsme zvažte dokumenty, které odpovídá v poli Název relevantnější než dokumenty, které odpovídá do pole Popis. Kromě toho Pokud index měli pole ceny pro každý hotelů, jsme může zvýšit úroveň dokumenty s nižší cenou. Zjistěte, jak Další [profily vyhodnocování přidat do indexu vyhledávání.](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)
2. **Zvýšení skóre termínu** (k dispozici pouze v syntaxe dotazů Lucene úplná) poskytuje zvýšení skóre operátor `^` , který lze použít na všechny části stromu dotazu. V našem příkladu, namísto hledání na předponě *air-condition*\*, jeden může hledat buď přesnou termín *air-condition* nebo předponu, ale dokumenty, které odpovídají na přesný termín řazeny výše použitím nárůst termín dotazu: * letecké podmínku ^ 2 || AIR-Condition **. Další informace o [zvýšení skóre termínu](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search#bkmk_termboost).


### <a name="scoring-in-a-distributed-index"></a>Vyhodnocování v distribuované indexu

Všechny indexy ve službě Azure Search se automaticky rozdělí do víc horizontálních oddílů, abychom mohli rychle distribuovat index mezi několika uzly při škálování služby nahoru i dolů. Když je vydaný žádost o vyhledávání, jeho vydání před každou horizontálního oddílu nezávisle. Výsledky z každé horizontálního oddílu jsou pak sloučit a seřazené podle skóre (Pokud není definováno žádné jiné pořadí). Je důležité vědět, že vyhodnocování vah funkce dotazování termín frekvence před jeho frekvence inverzní dokumentu v všechny dokumenty v rámci horizontálního oddílu, není mezi všechny horizontálních oddílů!

To znamená, relevance skóre *může* být různé pro identické dokumenty, pokud budou umístěny v různých horizontálních oddílů. Naštěstí tyto rozdíly jsou obvykle zmizí z důvodu další i termín distribuční růstem počet dokumentů v indexu. Není možné předpokládají, na které horizontálního oddílu se umístí všechny daného dokumentu. Ale za předpokladu, že klíč dokumentu nemění, jej bude vždy přiřadit stejné ID horizontálního oddílu.

Obecně platí skóre dokumentů není nejlepší atribut pro řazení dokumenty, pokud stabilitu pořadí je důležité. Například zadány dva dokumenty s identické skóre, neexistuje žádná záruka, která jako první se objeví v následných spustí stejný dotaz. V sadě výsledků skóre dokumentu pouze získat obecný přehled o dokument relevance relativně k jiné dokumenty.

## <a name="conclusion"></a>Závěr

Úspěch internet vyhledávací weby vyvolalo očekávání pro fulltextové vyhledávání přes osobní data. Pro téměř k libovolnému druhu vyhledávání nyní Očekáváme, že modul pochopit naše záměr, i když podmínky jsou uvedeny překlepu nebo jsou neúplné. Může i Očekáváme, že shody na základě téměř ekvivalentní termíny nebo synonyma, které jsme zadali ve skutečnosti.

Z hlediska technické fulltextové vyhledávání je vysoce komplexní, vyžadují pokročilé analýzy lingvistické a systematicky pro zpracování způsoby, které generovat, rozbalit a transformace vyhledávacích dotazů k poskytování výsledku relevantní. Vzhledem ke vyplývajících složitosti, je celá řada faktorů, které můžou ovlivnit výsledek dotazu. Z tohoto důvodu začne investovat dobu pochopit mechanismů fulltextové vyhledávání nabízí konkrétní výhody, při pokusu o fungovat prostřednictvím neočekávané výsledky.  

Tento článek prozkoumali fulltextového vyhledávání v rámci Azure Search. Věříme, že poskytuje dostatečnou pozadí rozpoznat možné příčiny a řešení pro řešení běžných problémů s dotazu. 

## <a name="next-steps"></a>Další postup

+ Sestavení indexu ukázkové, vyzkoušejte různé dotazy a zkontrolovat výsledky. Pokyny najdete v tématu [sestavení a dotazování indexu na portálu](search-get-started-portal.md#query-index).

+ Zkuste syntaxe další dotazu z [vyhledávání dokumentů](https://docs.microsoft.com/rest/api/searchservice/search-documents#examples) příklad části nebo z [jednoduchá syntaxe dotazů](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) v Průzkumník služby Search na portálu.

+ Zkontrolujte [vyhodnocování profily](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) Pokud chcete ladit hodnocení v aplikaci vyhledávání.

+ Zjistěte, jak použít [lexikální analyzátory jazyka](https://docs.microsoft.com/rest/api/searchservice/language-support).

+ [Konfigurace vlastní analyzátorů](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search) pro minimální zpracování nebo speciální zpracování na konkrétních polí.

+ [Porovnání standardní a anglické analyzátorů](http://alice.unearth.ai/)) na tento ukázkový web vedle sebe. 

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
