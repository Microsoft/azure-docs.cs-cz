---
title: Přidat vlastní analyzátory – Azure Search
description: Upravte tokenizátory textu a znaku filtry použít v dotazech fulltextového vyhledávání Azure Search.
ms.date: 01/31/2019
services: search
ms.service: search
ms.topic: conceptual
author: Yahnoosh
ms.author: jlembicz
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
ms.openlocfilehash: 150510ec09744b1350a93bde4e2a4dcb141867c0
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "56008283"
---
# <a name="add-custom-analyzers-to-an-azure-search-index"></a>Vlastní analyzátory přidat do indexu Azure Search

A *vlastní analyzátor* je kombinace tokenizátor a volitelné filtry, které se používá pro přizpůsobení zpracování v modulu vyhledávání textu zadaného uživatelem. Například můžete vytvořit vlastní analyzátor s *char filtr* před textovými vstupy jsou tokenizovaného odeberte značka jazyka HTML.

## <a name="overview"></a>Přehled

 Role [fulltextového vyhledávacího modulu](search-lucene-query-architecture.md), jednoduše řečeno, je pro zpracování a ukládání dokumentů způsobem, který umožňuje efektivní dotazování a načítání. Na vysoké úrovni to všechno se vrátí k extrahování důležité slova z dokumentů, umístit je do indexu a pak pomocí indexu najít dokumenty, které odpovídají slova daný dotaz. Proces extrahování slova z dokumentů a vyhledávací dotazy se nazývá provést lexikální analýzu. Součásti, které provést lexikální analýzu, se nazývají analyzátory.

 Ve službě Azure Search, můžete na základě sady předdefinovaných jazykové analyzátory nezávislá v [analyzátory](#AnalyzerTable) uvedené v tabulce a jazyk konkrétní analyzátory [jazykové analyzátory &#40;službu REST API&#41;](index-add-language-analyzers.md). Máte také možnost definovat vlastní vlastní analyzátory.  

 Vlastní analyzátor umožňuje vám tak převzít kontrolu nad tímto procesem převodu textu na tokeny indexovanou a prohledávatelné. To je konfigurace uživatelem definované skládající se z jednoho předdefinované tokenizátor, jeden nebo více tokenů filtrů a jeden nebo více filtrů char. Tokenizátor zodpovídá za rozbíjející text do tokenů a token filtry k úpravě tokeny, protože ho vygeneroval tokenizátor. Char filtry se použijí pro Příprava vstupního textu, než je zpracován programovacím modelem tokenizátor. Například filtr char můžete nahradit některé znaky a symboly.

 Oblíbené scénáře povolené ve vlastní analyzátory patří:  

-   Zapsané ve fonetické vyhledávání. Přidáte filtr zapsané ve fonetické umožňuje vyhledávání podle jak slovo podle názvu dalo čekat, jak to není zadána.  

-   Lexikální analýzu zakážete. Vytvoření prohledávatelná pole, které nejsou analyzovat pomocí analyzátoru – klíčové slovo.  

-   Předponu nebo příponu k rychlé hledání. Přidejte token Filtr Edge N-gramy index předpony slova pro povolení porovnávání rychlé předponu. Ho spojovat se přípony odpovídající reverzní token filtru.  

-   Vlastní Tokenizace. Například použijte tokenizátor prázdné znaky věty přerušit tokeny pomocí prázdné znaky jako oddělovač  

-   Skládání ASCII. Přidání standardní ASCII skládání filtr k normalizaci diakritiku jako cos nebo ê v hledané výrazy.  

 Můžete definovat více vlastní analyzátory postup obměny kombinací filtry, ale každé pole lze použít pouze jeden analyzátor pro indexování analýzy a jeden pro hledání analýzy.  

 Tato stránka obsahuje seznam podporovaných analyzátory, tokenizátory, token filtry a filtry char. Můžete také najít popis změny v definici indexu s příklad použití. Další informace o technologii využít v Azure Search implementaci, najdete v části [balíček souhrnné analýzy (Lucene)](https://lucene.apache.org/core/4_10_0/core/org/apache/lucene/codecs/lucene410/package-summary.html). Příklady analyzátor konfigurace najdete v tématu [analyzátory ve službě Azure Search > Příklady](https://docs.microsoft.com/azure/search/search-analyzers#examples).


## <a name="default-analyzer"></a>Výchozí analyzátor  

Ve výchozím nastavení, jsou analyzovány prohledávatelná pole ve službě Azure Search [Apache Lucene standardní analyzer (standardní lucene)](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) která rozdělí text na prvky, které následují ["Segmentace Unicode Text"](https://unicode.org/reports/tr29/) pravidla. Kromě toho standardní analyzátor převede všechny znaky do svého formuláře malými písmeny. Indexovaných dokumentů a hledané termíny procházet analýzy při indexování a zpracování dotazů.  

 Používá se automaticky na každý prohledávatelném poli explicitně ho nepřepíšete s jiný analyzátor v rámci definice pole. Alternativní analyzátory můžou být vlastního analyzátoru nebo různých předdefinovaných analyzátor ze seznamu dostupných [analyzátory](#AnalyzerTable) níže.

## <a name="validation-rules"></a>Ověřovací pravidla  
 Názvy analyzátorů, tokenizátory, token filtry a filtry znak musí být jedinečné a nesmí být stejný jako některý z předdefinovaných analyzátory, tokenizátory, token filtry nebo char filtry. Zobrazit [odkaz na vlastnost](#PropertyReference) pro názvy již používán.

## <a name="create-a-custom-analyzer"></a>Vytvoření vlastního analyzátoru
 Můžete definovat vlastní analyzátory v okamžiku vytvoření indexu. Syntaxe pro určení vlastního analyzátoru je popsané v této části. Vám může se také seznámit se syntaxí kontrolou ukázková definice v [analyzátory ve službě Azure Search](https://docs.microsoft.com/azure/search/search-analyzers#examples).  

 Definice rozhraní analyzer obsahuje název, typ, jeden nebo více filtrů char, maximálně jeden tokenizátor a jeden nebo více tokenů filtrů pro zpracování Tokenizace po. Char křížového jsou aplikována před Tokenizace. Token filtry a char filtry se použijí zleva doprava.

 `tokenizer_name` Je název tokenizátor, `token_filter_name_1` a `token_filter_name_2` jsou názvy token filtry a `char_filter_name_1` a `char_filter_name_2` jsou filtry názvů char (najdete v článku [Tokenizátory](#Tokenizers), [ Token filtry](#TokenFilters) a [Char filtry](#CharFilters) tabulky pro platné hodnoty).

Analyzátor definic je součástí větší index. Zobrazit [vytvořit Index rozhraní API](https://docs.microsoft.com/rest/api/searchservice/create-index) informace o rest index.

```  
"analyzers":(optional)[  
   {  
      "name":"name of analyzer",  
      "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",  
      "charFilters":[  
         "char_filter_name_1",  
         "char_filter_name_2"  
      ],  
      "tokenizer":"tokenizer_name",  
      "tokenFilters":[  
         "token_filter_name_1",  
         "token_filter_name_2"  
      ]  
   },  
   {  
      "name":"name of analyzer",  
      "@odata.type":"#analyzer_type",  
      "option1":value1,  
      "option2":value2,  
      ...  
   }  
],  
"charFilters":(optional)[  
   {  
      "name":"char_filter_name",  
      "@odata.type":"#char_filter_type",  
      "option1":value1,  
      "option2":value2,  
      ...  
   }  
],  
"tokenizers":(optional)[  
   {  
      "name":"tokenizer_name",  
      "@odata.type":"#tokenizer_type",  
      "option1":value1,  
      "option2":value2,  
      ...  
   }  
],  
"tokenFilters":(optional)[  
   {  
      "name":"token_filter_name",  
      "@odata.type":"#token_filter_type",  
      "option1":value1,  
      "option2":value2,  
      ...  
   }  
]  
```  

> [!NOTE]  
>  Vlastní analyzátory, které vytvoříte, se nezobrazí na portálu Azure portal. Kód, který provádí volání rozhraní API, při definování indexu je jediný způsob, jak přidat vlastní analyzátor.  

 V rámci definice indexu v této části můžete umístit kamkoli v textu žádosti o vytvoření indexu, ale obvykle odkazuje na konci:  

```  
{  
  "name": "name_of_index",  
  "fields": [ ],  
  "suggesters": [ ],  
  "scoringProfiles": [ ],  
  "defaultScoringProfile": (optional) "...",  
  "corsOptions": (optional) { },  
  "analyzers":(optional)[ ],  
  "charFilters":(optional)[ ],  
  "tokenizers":(optional)[ ],  
  "tokenFilters":(optional)[ ]  
}  
```  

Definice pro filtry char, tokenizátory a token filtry jsou přidány do indexu pouze v případě, že nastavíte vlastní možnosti. Chcete-li použít existující filtr nebo tokenizátor jako-se, zadejte jeho název v definici analyzátor.

<a name="Testing custom analyzers"></a>
## <a name="test-a-custom-analyzer"></a>Testování vlastního analyzátoru

Můžete použít **Analyzéru testu operace** v [rozhraní REST API](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) zobrazíte jak analyzátor rozdělí zadaný text do tokenů.

**Požadavek**
~~~~
  POST https://[search service name].search.windows.net/indexes/[index name]/analyze?api-version=[api-version]
  Content-Type: application/json
    api-key: [admin key]

  {
     "analyzer":"my_analyzer",
     "text": "Vis-à-vis means Opposite"
  }
~~~~
**Odpověď**
~~~~
  {
    "tokens": [
      {
        "token": "vis_a_vis",
        "startOffset": 0,
        "endOffset": 9,
        "position": 0
      },
      {
        "token": "vis_à_vis",
        "startOffset": 0,
        "endOffset": 9,
        "position": 0
      },
      {
        "token": "means",
        "startOffset": 10,
        "endOffset": 15,
        "position": 1
      },
      {
        "token": "opposite",
        "startOffset": 16,
        "endOffset": 24,
        "position": 2
      }
    ]
  }
 ~~~~

 ## <a name="update-a-custom-analyzer"></a>Aktualizovat vlastní analyzátor

Po definování analyzátor, tokenizátor, token filtru nebo char filtru nelze změnit. Nové značky mohou být přidány do existujícího indexu pouze v případě, `allowIndexDowntime` příznak je nastaven na true v žádosti o aktualizaci indexu:

~~~~
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=[api-version]&allowIndexDowntime=true
~~~~

Tato operace přebírá pro alespoň několik sekund, způsobí selhání žádostí indexování a dotazování indexu v režimu offline. Výkon a zápis dostupnost indexu může být narušena několik minut po index je aktualizované nebo delší dobu velmi velký indexů, ale tyto efekty jsou dočasné a nakonec na své vlastní řešení.

 <a name="ReferenceIndexAttributes"></a>

## <a name="index-attribute-reference"></a>Odkaz na atribut indexu

V tabulce dole najdete seznam vlastností konfigurace analyzátory, tokenizátory, token filtry a char oddílu filtrů definici indexu. Struktura analyzátoru, tokenizátor nebo filtru v indexu se skládá z těchto atributů. Informace o přiřazení hodnoty, najdete v článku [odkaz na vlastnost](#PropertyReference).

 ### <a name="analyzers"></a>Analyzátory

Atributy indexu pro analyzátory, se liší v závislosti na tom, určete, jestli používáte předdefinovanou nebo vlastní analyzátory.

#### <a name="predefined-analyzers"></a>Předdefinované analyzátory

|||  
|-|-|  
|Název|Musí obsahovat jenom písmena, číslice, mezery, pomlčky nebo podtržítka, může pouze úvodní a končit alfanumerickými znaky a je omezený na 128 znaků.|  
|Type|Analyzátor typ ze seznamu podporovaných analyzátory. Najdete v článku **analyzer_type** sloupec [analyzátory](#AnalyzerTable) následující tabulka.|  
|Možnosti|Musí být platné možnosti předdefinované analyzátor uvedené v [analyzátory](#AnalyzerTable) následující tabulka.|  

#### <a name="custom-analyzers"></a>Vlastní analyzátory

|||  
|-|-|  
|Název|Musí obsahovat jenom písmena, číslice, mezery, pomlčky nebo podtržítka, může pouze úvodní a končit alfanumerickými znaky a je omezený na 128 znaků.|  
|Type|Musí být "#Microsoft.Azure.Search.CustomAnalyzer".|  
|CharFilters|Nastavte na jednu z předdefinovaných char filtry uvedené v [Char filtry](#CharFilter) tabulky nebo char vlastní filtr zadaný v definici indexu.|  
|Tokenizátor|Povinná hodnota. Nastavte na jednu z předdefinovaných tokenizátory uvedené v [Tokenizátory](#Tokenizers) tabulky níže nebo vlastní tokenizátor zadané v definici indexu.|  
|TokenFilters|Nastavte na jednu z předdefinovaných token filtry, které jsou uvedené v [Token filtry](#TokenFilters) tabulky nebo vlastní token filtr zadaný v definici indexu.|  

<a name="CharFilter"></a>

 ### <a name="char-filters"></a>Char – filtry

 Filtr char slouží k přípravě vstupního textu, než je zpracován programovacím modelem tokenizátor. Například můžete nahradit některé znaky a symboly. Může mít několik filtrů char v vlastní analyzátor. Char filtry se spouští v pořadí, ve kterém jsou uvedeny.  

|||  
|-|-|  
|Název|Musí obsahovat jenom písmena, číslice, mezery, pomlčky nebo podtržítka, může pouze úvodní a končit alfanumerickými znaky a je omezený na 128 znaků.|  
|Type|Typ ze seznamu podporovaných char filtrů filtru Char. Zobrazit **char_filter_type** sloupec v [Char filtry](#CharFilter) následující tabulka.|  
|Možnosti|Musí být platné možnosti danou [Char filtry](#CharFilter) typu.|  

 ### <a name="tokenizers"></a>Tokenizátory

 Tokenizátor rozdělí průběžné text do sekvence tokenů, jako je zastavení větu na slova.  

 Můžete zadat přesně jeden tokenizátor za vlastní analyzátor. Pokud potřebujete tokenizátor více než jeden, můžete vytvořit více vlastních analyzátorů a přiřadit na základě pole pomocí pole ve schématu vašeho indexu.  
Vlastní analyzátor můžete použít předdefinované tokenizátor výchozí nebo vlastní možnosti.  

|||  
|-|-|  
|Název|Musí obsahovat jenom písmena, číslice, mezery, pomlčky nebo podtržítka, může pouze úvodní a končit alfanumerickými znaky a je omezený na 128 znaků.|  
|Type|Tokenizátor název ze seznamu podporovaných tokenizátory. Zobrazit **tokenizer_type** sloupec v [Tokenizátory](#Tokenizers) následující tabulka.|  
|Možnosti|Musí být platné možnosti tokenizátor daného typu uvedené v [Tokenizátory](#Tokenizers) následující tabulka.|  

 ### <a name="token-filters"></a>Token filtry

 Token filtru se používá k filtrování nebo upravit tokeny generovaných tokenizátor. Můžete například zadat filtr, který převádí všechny znaky na malá písmena malá písmena.   
Může mít více tokenů filtry ve vlastní analyzátor. Token filtry se spouští v pořadí, ve kterém jsou uvedeny.  

|||  
|-|-|  
|Název|Musí obsahovat jenom písmena, číslice, mezery, pomlčky nebo podtržítka, může pouze úvodní a končit alfanumerickými znaky a je omezený na 128 znaků.|  
|Type|Název tokenu filtr ze seznamu podporovaných token filtrů. Zobrazit **token_filter_type** sloupec v [Token filtry](#TokenFilters) následující tabulka.|  
|Možnosti|Musí být [Token filtry](#TokenFilters) typu daný token filtru.|  

<a name="PropertyReference"></a>  

## <a name="property-reference"></a>Odkaz na vlastnost

Tato část obsahuje platné hodnoty pro atributy určené v definici vlastního analyzátoru, tokenizátor, char filtru nebo token filtru v indexu. Analyzátory, tokenizátory a filtry, které jsou implementovány pomocí Apache Lucene mají odkazy na dokumentaci k rozhraní Lucene API.

<a name="AnalyzerTable"></a>

###  <a name="predefined-analyzers-reference"></a>Odkaz na předdefinované analyzátory

|**analyzer_name**|**analyzer_type**  <sup>1</sup>|**Popis a možnosti**|  
|-|-|-|  
|[keyword](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html)| (typ platí, pouze když jsou k dispozici možnosti) |Celý obsah pole považuje za jeden token. To je užitečné pro data, jako jsou PSČ, ID a některé názvy produktů.|  
|[Vzor](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/PatternAnalyzer.html)|PatternAnalyzer|Flexibilní rozděluje text do podmínky prostřednictvím vzor regulárního výrazu.<br /><br /> **Možnosti**<br /><br /> malá písmena (typ: bool)-určuje, zda podmínky jsou psané malými písmeny. Výchozí hodnota je true.<br /><br /> [vzor](https://docs.oracle.com/javase/7/docs/api/java/util/regex/Pattern.html?is-external=true) (typ: string) – vzor regulárního výrazu tak, aby odpovídaly token oddělovače. Výchozí hodnota je \w+.<br /><br /> [příznaky](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) (typ: string) – příznaky regulárního výrazu. Výchozí hodnota je prázdný řetězec. Povolené hodnoty: CANON_EQ CASE_INSENSITIVE, KOMENTÁŘE, DOTALL, LITERAL, MULTILINE, UNICODE_CASE, UNIX_LINES<br /><br /> stopslova (typ: pole řetězců) – seznam stopwords. Výchozí hodnota je prázdný seznam.|  
|[Jednoduché](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/SimpleAnalyzer.html)|(typ platí, pouze když jsou k dispozici možnosti) |Rozdělí text na jiných písmena a je převede na malá písmena. |  
|[standard](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) <br />(Také označované jako standard.lucene)|StandardAnalyzer|Standardní analyzátor Lucene tvořený standardní tokenizátor, malá písmena filtr a zastavit.<br /><br /> **Možnosti**<br /><br /> maxTokenLength (typ: int)-maximální délka tokenu. Výchozí hodnota je 255. Tokeny delší než maximální délka je rozdělit. Maximální délka tokenu, který je možné je 300 znaků.<br /><br /> stopslova (typ: pole řetězců) – seznam stopwords. Výchozí hodnota je prázdný seznam.|  
|standardasciifolding.lucene|(typ platí, pouze když jsou k dispozici možnosti) |Standardní analyzátor s Ascii skládání filtru. |  
|[stop](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/StopAnalyzer.html)|StopAnalyzer|Rozdělí text na jiných písmena, malá písmena a stopslovo token filtry platí.<br /><br /> **Možnosti**<br /><br /> stopslova (typ: pole řetězců) – seznam stopwords. Výchozí hodnota je předem definovaného seznamu pro angličtinu. |  
|[Prázdné znaky](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html)|(typ platí, pouze když jsou k dispozici možnosti) |Analyzátor, který používá tokenizátor prázdné znaky. Jsou rozděleny tokeny, které jsou delší než 255 znaků.|  

 <sup>1</sup> analyzátor typy mají vždy předponu v kódu s "#Microsoft.Azure.Search" tak, aby "PatternAnalyzer" by ve skutečnosti byl zadán jako "#Microsoft.Azure.Search.PatternAnalyzer". Odebrali jsme předpona pro zkrácení, ale ve vašem kódu se vyžaduje předpona. 
 
Analyzer_type je k dispozici pouze pro analyzátory, které lze přizpůsobit. Pokud nejsou žádné možnosti, jako je tomu u analyzátoru – klíčové slovo, neexistuje žádný typ přidružené #Microsoft.Azure.Search.


<a name="CharFilter"></a>

###  <a name="char-filters-reference"></a>Char – referenční dokumentace filtry

V následující tabulce jsou propojeny znak filtry, které jsou implementovány pomocí Apache Lucene dokumentaci rozhraní Lucene API.

|**char_filter_name**|**char_filter_type** <sup>1</sup>|**Popis a možnosti**|  
|-|-|-|
|[html_strip](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/charfilter/HTMLStripCharFilter.html)|(typ platí, pouze když jsou k dispozici možnosti)  |Vytvoří filtr char, která se pokusí odstranit HTML.|  
|[Mapování](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/charfilter/MappingCharFilter.html)|MappingCharFilter|Char filtr, který použije mapování definovaná s možností mapování. Při porovnávání se greedy (nejdelší porovnávání vzorů v daném bodě wins). Nahrazení může být prázdný řetězec.<br /><br /> **Možnosti**<br /><br /> mapování (typ: řetězec pole) – seznam mapování v následujícím formátu: "a = > b" (všechny výskyty znaku "a" se nahrazují znaky "b"). Povinná hodnota.|  
|[pattern_replace](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/pattern/PatternReplaceCharFilter.html)|PatternReplaceCharFilter|Char filtr, který nahradí znaky ve vstupním řetězci. Použije regulární výraz k identifikaci znakové sekvence zachovat a vzor pro nahrazení k identifikaci znaků k nahrazení. Zadejte například text = "aa, bb aa bb", pattern="(aa)\\\s+(bb)" nahrazení = "$# 1$ 2", výsledek = "aa #bb aa #bb".<br /><br /> **Možnosti**<br /><br /> vzor (typ: string) – povinné.<br /><br /> nahrazení (typ: string) – povinné.|  

 <sup>1</sup> char filtrování typů mají vždy předponu v kódu s "#Microsoft.Azure.Search" tak, aby "MappingCharFilter" by ve skutečnosti byl zadán jako "#Microsoft.Azure.Search.MappingCharFilter. Odebrali jsme předpona, kterou chcete omezit šířku v tabulce, ale nezapomeňte zadat v kódu. Všimněte si, že char_filter_type je k dispozici pouze pro filtry, které lze přizpůsobit. Pokud neexistují žádné možnosti, jako je tomu u html_strip, neexistuje žádný typ přidružené #Microsoft.Azure.Search.

<a name="Tokenizers"></a>

###  <a name="tokenizers-reference"></a>Tokenizátory odkaz

V následující tabulce jsou propojené, které jsou implementovány pomocí Apache Lucene tokenizátory dokumentaci rozhraní Lucene API.

|**tokenizer_name**|**tokenizer_type** <sup>1</sup>|**Popis a možnosti**|  
|-|-|-|  
|[classic](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/ClassicTokenizer.html)|ClassicTokenizer|Gramatika na základě tokenizátor, který je vhodný pro zpracování dokumentů většina Evropské jazyka.<br /><br /> **Možnosti**<br /><br /> maxTokenLength (typ: int)-maximální délka tokenu. Výchozí: 255, maximální: 300. Tokeny delší než maximální délka je rozdělit.|  
|[edgeNGram](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html)|EdgeNGramTokenizer|Tokenizes vstupu z okraj do n gramy zadaný velikost(i).<br /><br /> **Možnosti**<br /><br /> minGram (typ: int) – výchozí: 1, maximální: 300.<br /><br /> maxGram (typ: int) – výchozí: 2, maximální: 300. Musí být větší než minGram.<br /><br /> tokenChars (typ: pole řetězců)-znaku třídy potřeba mít na tokeny. Povolené hodnoty: <br />"písmeno", "číslice", "prázdné znaky", "interpunkce", "symbol". Výchozí hodnota je prázdné pole – uchovává všechny znaky. |  
|[keyword_v2](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html)|KeywordTokenizerV2|Vysílá celý vstupní jako jeden token.<br /><br /> **Možnosti**<br /><br /> maxTokenLength (typ: int)-maximální délka tokenu. Výchozí: 256, maximální: 300. Tokeny delší než maximální délka je rozdělit.|  
|[letter](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/LetterTokenizer.html)|(typ platí, pouze když jsou k dispozici možnosti)  |Rozdělí text na jiných písmena. Jsou rozděleny tokeny, které jsou delší než 255 znaků.|  
|[Malá](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/LowerCaseTokenizer.html)|(typ platí, pouze když jsou k dispozici možnosti)  |Rozdělí text na jiných písmena a je převede na malá písmena. Jsou rozděleny tokeny, které jsou delší než 255 znaků.|  
| microsoft_language_tokenizer| MicrosoftLanguageTokenizer| Rozdělí text pomocí pravidel pro konkrétní jazyk.<br /><br /> **Možnosti**<br /><br /> maxTokenLength (typ: int)-maximální délka tokenu, výchozí: 255, maximální: 300. Tokeny delší než maximální délka je rozdělit. Tokeny déle, než 300 znaků jsou nejprve rozděleno na tokeny délky 300 a potom každý z těchto tokenů rozdělení na základě maxTokenLength sady.<br /><br />isSearchTokenizer (typ: bool) – nastavení na hodnotu true, pokud se použije jako tokenizátor vyhledávání, nastavena na hodnotu false Pokud slouží jako indexování tokenizátor. <br /><br /> jazyk (typ: string) – výchozí jazyk se má použít, "Angličtina". Povolené hodnoty:<br />"Bengálština", "bulharský", "Katalánština", "chineseSimplified", "chineseTraditional", "Chorvatská", "České", "Dánská", "Nizozemská", "Angličtina", "francouzština", "němčina", "řečtina", "Gudžarádština", "hindština", "islandském", "indonéská", "italské", "japonské", "kannadština" " Korejské","malay","malajálamština","maráthština","norwegianBokmaal","polština","portugalština","portugueseBrazilian","paňdžábština","rumunský","ruština","serbianCyrillic","serbianLatin","slovinština","Španělsko","Švédská","Tamilské","Telugské","thajština"" Ukrajinská","urdštiny","vietnamština" |
| microsoft_language_stemming_tokenizer | MicrosoftLanguageStemmingTokenizer| Rozdělí text pomocí pravidel pro konkrétní jazyk a snižuje slova jejich podkladové formuláře<br /><br /> **Možnosti**<br /><br />maxTokenLength (typ: int)-maximální délka tokenu, výchozí: 255, maximální: 300. Tokeny delší než maximální délka je rozdělit. Tokeny déle, než 300 znaků jsou nejprve rozděleno na tokeny délky 300 a potom každý z těchto tokenů rozdělení na základě maxTokenLength sady.<br /><br /> isSearchTokenizer (typ: bool) – nastavení na hodnotu true, pokud se použije jako tokenizátor vyhledávání, nastavena na hodnotu false Pokud slouží jako indexování tokenizátor.<br /><br /> jazyk (typ: string) – výchozí jazyk se má použít, "Angličtina". Povolené hodnoty:<br />"arabština", "Bengálština", "bulharský", "Katalánština", "Chorvatská", "České", "Dánská", "Nizozemská", "Angličtina", "Estonská", "finština", "francouzština", "němčina", "řečtina", "Gudžarádština", "hebrejského", "hindština", "maďarské", "islandském", "indonéská", "italské", "kannadština" " Lotyština","Litevský","malay","malajálamština","maráthština","norwegianBokmaal","polština","portugalština","portugueseBrazilian","paňdžábština","rumunský","ruština","serbianCyrillic","serbianLatin","Slovenská","slovinština","Španělsko","Švédská"" Tamilské","Telugské","turecké","ukrajinská","urdština" |
|[nGram](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenizer.html)|NGramTokenizer|Vstup do n gramy dané velikost(i) tokenizes.<br /><br /> **Možnosti**<br /><br /> minGram (typ: int) – výchozí: 1, maximální: 300.<br /><br /> maxGram (typ: int) – výchozí: 2, maximální: 300. Musí být větší než minGram. <br /><br /> tokenChars (typ: pole řetězců)-znaku třídy potřeba mít na tokeny. Povolené hodnoty: "písmeno", "číslice", "prázdné znaky", "interpunkce", "symbol". Výchozí hodnota je prázdné pole – uchovává všechny znaky. |  
|[path_hierarchy_v2](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/path/PathHierarchyTokenizer.html)|PathHierarchyTokenizerV2|Tokenizátor pro cestu jako hierarchie.<br /><br /> **Možnosti**<br /><br /> oddělovač (typ: string) – výchozí: ' /.<br /><br /> nahrazení (typ: string) – Pokud je nastaven, nahradí znak oddělovače. Ve výchozím nastavení stejný jako hodnota oddělovače.<br /><br /> maxTokenLength (typ: int)-maximální délka tokenu. Výchozí: 300, maximální: 300. Cesty delší než maxTokenLength jsou ignorovány.<br /><br /> reverzní (typ: bool) – při hodnotě true se vygeneruje token v obráceném pořadí. Výchozí: false.<br /><br /> Přeskočit (typ: bool) – počáteční tokeny mají přeskočit. Výchozí hodnota je 0.|  
|[Vzor](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/pattern/PatternTokenizer.html)|PatternTokenizer|Tato tokenizátor použije k vytvoření různých tokeny regulární výraz porovnávání vzorů.<br /><br /> **Možnosti**<br /><br /> vzor (typ: string) – vzor regulárního výrazu. Výchozí hodnota je \w+.<br /><br /> [příznaky](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) (typ: string) – příznaky regulárního výrazu. Výchozí hodnota je prázdný řetězec. Povolené hodnoty: CANON_EQ CASE_INSENSITIVE, KOMENTÁŘE, DOTALL, LITERAL, MULTILINE, UNICODE_CASE, UNIX_LINES<br /><br /> skupiny (typ: int) – které skupiny k extrakci do tokenů. Výchozí hodnota je -1 (rozdělení).|  
|[standard_v2](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/StandardTokenizer.html)|StandardTokenizerV2|Dělí text následující [Unicode Text segmentace pravidla](https://unicode.org/reports/tr29/).<br /><br /> **Možnosti**<br /><br /> maxTokenLength (typ: int)-maximální délka tokenu. Výchozí: 255, maximální: 300. Tokeny delší než maximální délka je rozdělit.|  
|[uax_url_email](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/UAX29URLEmailTokenizer.html)|UaxUrlEmailTokenizer|Tokenizes adresy URL a e-mailů jako jeden token.<br /><br /> **Možnosti**<br /><br /> maxTokenLength (typ: int)-maximální délka tokenu. Výchozí: 255, maximální: 300. Tokeny delší než maximální délka je rozdělit.|  
|[Prázdné znaky](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/WhitespaceTokenizer.html)|(typ platí, pouze když jsou k dispozici možnosti) |Rozdělí text na prázdné znaky. Jsou rozděleny tokeny, které jsou delší než 255 znaků.|  

 <sup>1</sup> tokenizátor typy mají vždy předponu v kódu s "#Microsoft.Azure.Search" tak, aby "ClassicTokenizer" by ve skutečnosti byl zadán jako "#Microsoft.Azure.Search.ClassicTokenizer". Odebrali jsme předpona, kterou chcete omezit šířku v tabulce, ale nezapomeňte zadat v kódu. Všimněte si, že tento tokenizer_type je k dispozici pouze pro tokenizátory, které lze přizpůsobit. Pokud nejsou žádné možnosti, jako je tomu u tokenizátor písmeno, neexistuje žádný typ přidružené #Microsoft.Azure.Search.

<a name="TokenFilters"></a>

###  <a name="token-filters-reference"></a>Odkaz na token filtry

V následující tabulce jsou propojeny token filtry, které jsou implementovány pomocí Apache Lucene dokumentaci rozhraní Lucene API.

|**token_filter_name**|**token_filter_type** <sup>1</sup>|**Popis a možnosti**|  
|-|-|-|  
|[arabic_normalization](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ar/ArabicNormalizationFilter.html)|(typ platí, pouze když jsou k dispozici možnosti)  |Token filtru, která se použije Arabské normalizátor normalizace orthography.|  
|[apostrophe](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/tr/ApostropheFilter.html)|(typ platí, pouze když jsou k dispozici možnosti)  |Odebere všechny znaky po apostrof (včetně samotného apostrof). |  
|[asciifolding](https://lucene.apache.org/core/4_0_0/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html)|AsciiFoldingTokenFilter|Převede abecedy, číselných a symbolické znaky Unicode, které nejsou ve nejprve 127 znaků ASCII ("Základní latinky" Unicode bloku) do jejich ekvivalentů znaků ASCII, pokud existuje.<br /><br /> **Možnosti**<br /><br /> preserveOriginal (typ: bool) – Pokud je nastavena hodnota true, původní token se ukládají. Výchozí hodnota je false.|  
|[cjk_bigram](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/cjk/CJKBigramFilter.html)|CjkBigramTokenFilter|Formuláře bigrams CJK podmínek, které se generují z StandardTokenizer.<br /><br /> **Možnosti**<br /><br /> ignoreScripts (typ: řetězec pole) – skripty, které ignorovat. Povolené hodnoty jsou: "han", "znaky hiragana", "katakana", "Korejská". Výchozí hodnota je prázdný seznam.<br /><br /> outputUnigrams (typ: bool) – nastavení na hodnotu true, pokud chcete vždy výstup unigrams a bigrams. Výchozí hodnota je false.|  
|[cjk_width](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/cjk/CJKWidthFilter.html)|(typ platí, pouze když jsou k dispozici možnosti)  |Normalizuje CJK šířka rozdíly. Složení plnou šířku ASCII varianty do ekvivalentní základní latin a poloviční šířku písma Katakana varianty do ekvivalentní kana. |  
|[classic](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/ClassicFilter.html)|(typ platí, pouze když jsou k dispozici možnosti)  |Odebere anglické Přivlastňovací pád a tečky z zkratky. |  
|[common_grams](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/commongrams/CommonGramsFilter.html)|CommonGramTokenFilter|Vytvořte bigrams pro často ke kterým dochází při indexování podmínky. Jedněch podmínkách jsou stále indexována, s bigrams překrývající.<br /><br /> **Možnosti**<br /><br /> commonWords (typ: pole řetězců)-sadu běžná slova. Výchozí hodnota je prázdný seznam. Povinná hodnota.<br /><br /> ignoreCase (typ: bool) – Pokud je nastavena hodnota true, vyhledávání nejsou rozlišována velká a malá písmena. Výchozí hodnota je false.<br /><br /> queryMode (typ: bool) – vygeneruje bigrams pak odstraní běžná slova a jedné podmínky, za nímž následuje běžné slovo. Výchozí hodnota je false.|  
|[dictionary_decompounder](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/compound/DictionaryCompoundWordTokenFilter.html)|DictionaryDecompounderTokenFilter|Rozložíme složených slov v řadě jazyků Germánský nalezen.<br /><br /> **Možnosti**<br /><br /> seznam slov (typ: řetězec pole) – seznam slov k porovnání. Výchozí hodnota je prázdný seznam. Povinná hodnota.<br /><br /> minWordSize (typ: int)-zpracovat pouze slova delší než toto omezení. Výchozí hodnota je 5.<br /><br /> minSubwordSize (typ: int) – jenom subwords delší než toto omezení jsou výstupem. Výchozí hodnota je 2.<br /><br /> maxSubwordSize (typ: int) – jenom subwords kratší než toto jsou výstupem. Výchozí hodnota je 15.<br /><br /> onlyLongestMatch (typ: bool)-přidat pouze nejdelší odpovídající podslovo do výstupu. Výchozí hodnota je false.|  
|[edgeNGram_v2](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenFilter.html)|EdgeNGramTokenFilterV2|Generuje n gramy dané velikost(i) z od přední nebo zadní vstupní token.<br /><br /> **Možnosti**<br /><br /> minGram (typ: int) – výchozí: 1, maximální: 300.<br /><br /> maxGram (typ: int) – výchozí: 2, maximální 300. Musí být větší než minGram.<br /><br /> na straně (typ: string) – určuje, které straně vstupu n gramy by měl být vygenerován z. Povolené hodnoty: "přední", "zpět" |  
|[elize](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/util/ElisionFilter.html)|ElisionTokenFilter|Odebere elisions. Například "l'avion" (roviny) je převedena na "avion" (roviny).<br /><br /> **Možnosti**<br /><br /> články (typ: pole řetězců)-sadu články k odebrání. Výchozí hodnota je prázdný seznam. Pokud není žádný seznam sady článků, ve výchozím nastavení se odeberou všechny článků francouzských.|  
|[german_normalization](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/de/GermanNormalizationFilter.html)|(typ platí, pouze když jsou k dispozici možnosti)  |Normalizuje německé znaků podle heuristiky z [German2 snowball algoritmus](https://snowball.tartarus.org/algorithms/german2/stemmer.html) .|  
|[hindi_normalization](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/hi/HindiNormalizationFilter.html)|(typ platí, pouze když jsou k dispozici možnosti)  |Normalizuje textu v hindština odebrat určité rozdíly v variace pravopisu. |  
|[indic_normalization](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/in/IndicNormalizationFilter.html)|IndicNormalizationTokenFilter|Normalizuje reprezentace Unicode textu v indických jazyků.
|[zachovat](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeepWordFilter.html)|KeepTokenFilter|Token filtru, která zajišťuje pouze tokeny s textem obsažené v zadaném seznamu slov.<br /><br /> **Možnosti**<br /><br /> keepWords (typ: řetězec pole) – seznam slova, která chcete zachovat. Výchozí hodnota je prázdný seznam. Povinná hodnota.<br /><br /> keepWordsCase (typ: bool) – Pokud je nastavena hodnota true, a nižší velká všechna slova nejprve. Výchozí hodnota je false.|  
|[keyword_marker](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeywordMarkerFilter.html)|KeywordMarkerTokenFilter|Označí podmínky jako klíčová slova.<br /><br /> **Možnosti**<br /><br /> klíčová slova (typ: řetězec pole) – seznam slov, abyste mohli označit jako klíčová slova. Výchozí hodnota je prázdný seznam. Povinná hodnota.<br /><br /> ignoreCase (typ: bool) – Pokud je nastavena hodnota true, a nižší velká všechna slova nejprve. Výchozí hodnota je false.|  
|[keyword_repeat](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeywordRepeatFilter.html)|(typ platí, pouze když jsou k dispozici možnosti)  |Generuje každý příchozí token dvakrát, jednou jako klíčové slovo a jednou jako – klíčové slovo. |  
|[kstem](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/en/KStemFilter.html)|(typ platí, pouze když jsou k dispozici možnosti)  |Vysoce výkonné kstem filtru pro angličtinu. |  
|[Délka](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/LengthFilter.html)|LengthTokenFilter|Odebere slova, která jsou příliš dlouhé, příliš krátká.<br /><br /> **Možnosti**<br /><br /> min (typ: int) – minimální počet. Výchozí: 0, maximální: 300.<br /><br /> maximální počet (typ: int) – maximální počet. Výchozí: 300, maximální: 300.|  
|[Limit](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/LimitTokenCountFilter.html)|Microsoft.Azure.Search.LimitTokenFilter|Omezí počet tokenů během indexování.<br /><br /> **Možnosti**<br /><br /> maxTokenCount (typ: int) – maximální počet tokenů pro vytvoření. Výchozí hodnota je 1.<br /><br /> consumeAllTokens (typ: bool) – ať už musí být využity všechny tokeny ze vstupu, i když je dosaženo maxTokenCount. Výchozí hodnota je false.|  
|[Malá](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)|(typ platí, pouze když jsou k dispozici možnosti)  |Normalizuje token text na malá písmena. |  
|[nGram_v2](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenFilter.html)|NGramTokenFilterV2|Generuje n gramy dané velikost(i).<br /><br /> **Možnosti**<br /><br /> minGram (typ: int) – výchozí: 1, maximální: 300.<br /><br /> maxGram (typ: int) – výchozí: 2, maximální 300. Musí být větší než minGram.|  
|[pattern_capture](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/pattern/PatternCaptureGroupTokenFilter.html)|PatternCaptureTokenFilter|Regulární výrazy v jazyce Java používá ke generování více tokenů, jeden pro každou skupinou zachycení ve vzorech pro jeden nebo více.<br /><br /> **Možnosti**<br /><br /> vzorky (typ: pole řetězců) – seznam vzorů tak, aby odpovídala každý token. Povinná hodnota.<br /><br /> preserveOriginal (typ: bool) – nastavení na hodnotu true, aby vracela původní token i v případě, že jednomu ze vzorů odpovídá, výchozí: true |  
|[pattern_replace](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/pattern/PatternReplaceFilter.html)|PatternReplaceTokenFilter|Token filtru, která se vztahuje na každý token v datovém proudu, nahraďte výskyty shoda s určeným náhradním vzoru.<br /><br /> **Možnosti**<br /><br /> vzor (typ: string) – povinné.<br /><br /> nahrazení (typ: string) – povinné.|  
|[persian_normalization](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/fa/PersianNormalizationFilter.html)|(typ platí, pouze když jsou k dispozici možnosti) |Platí pro Perština normalizace. |  
|[phonetic](https://lucene.apache.org/core/4_10_3/analyzers-phonetic/org/apache/lucene/analysis/phonetic/package-tree.html)|PhoneticTokenFilter|Vytvořte tokeny pro zapsané ve fonetické shody.<br /><br /> **Možnosti**<br /><br /> Kodér (typ: string) – zapsané ve fonetické kodér používat. Povolené hodnoty jsou: "metaphone", "doubleMetaphone", "soundex", "refinedSoundex", "caverphone1", "caverphone2", "Kolín", "nysiis", "koelnerPhonetik", "haasePhonetik", "beiderMorse". Výchozí hodnota: "metaphone". Výchozí hodnota je metaphone.<br /><br /> Zobrazit [kodér](https://commons.apache.org/proper/commons-codec/archives/1.10/apidocs/org/apache/commons/codec/language/package-frame.html) Další informace.<br /><br /> Nahraďte (typ: bool) – hodnota True, pokud kódovaného tokeny by měly nahradit původní tokeny, false, pokud má být přidán jako synonyma. Výchozí hodnota je true.|  
|[porter_stem](https://lucene.apache.org/core/4_10_3/analyzers-common/org/tartarus/snowball/ext/PorterStemmer.html)|(typ platí, pouze když jsou k dispozici možnosti)  |Transformuje token datového proudu, jak je uvedeno [lemmatizátor algoritmus silné](https://tartarus.org/~martin/PorterStemmer/). |  
|[reverzní](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/reverse/ReverseStringFilter.html)|(typ platí, pouze když jsou k dispozici možnosti)  |Vrátí řetězec tokenu. |  
|[scandinavian_normalization](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianNormalizationFilter.html)|(typ platí, pouze když jsou k dispozici možnosti)  |Normalizuje zaměnitelné skandinávské znaky. |  
|[scandinavian_folding](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianFoldingFilter.html)|(typ platí, pouze když jsou k dispozici možnosti)  |-> ÅÅäæÄÆ skandinávské znaků složení a öÖøØ -> o. Také je discriminates proti použití dvojitých samohlásky aa, ae, ao, oe a zálohy, byste museli opustit pouze první z nich. |  
|[Tečkovaná mřížka](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/shingle/ShingleFilter.html)|ShingleTokenFilter|Vytvoří kombinací tokeny jako jeden token.<br /><br /> **Možnosti**<br /><br /> maxShingleSize (typ: int) – výchozí hodnota je 2.<br /><br /> minShingleSize (typ: int) – výchozí hodnota je 2.<br /><br /> outputUnigrams (typ: bool) – Pokud je nastavena hodnota true, do výstupního datového proudu obsahuje vstupní tokeny (unigrams) a také šindele. Výchozí hodnota je true.<br /><br /> outputUnigramsIfNoShingles (typ: bool) – Pokud je hodnota true, potlačení chování outputUnigrams == false pro situace, kdy jsou k dispozici žádné šindele. Výchozí hodnota je false.<br /><br /> tokenSeparator (typ: string) – řetězec, který má používat při připojování sousedících tokenů do formuláře Tečkovaná mřížka. Výchozí hodnota je "".<br /><br /> filterToken (typ: string) – řetězec, který má pro každou pozici, na které neexistuje žádný token vložení. Výchozí hodnota je "_".|  
|[snowball](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/snowball/SnowballFilter.html)|SnowballTokenFilter|Snowball Token filtru.<br /><br /> **Možnosti**<br /><br /> jazyk (typ: string) – povolené hodnoty jsou: "Arménský", "Baskičtina", "Katalánština", "Dánská", "Nizozemská", "Angličtina", "finština", "francouzština", "němčina", "german2", "maďarština", "Italština", "kp", "lovins", "norská", "silné", "portugalština", "rumunština", " Ruština","Španělsko","Švédská","turečtina"|  
|[sorani_normalization](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ckb/SoraniNormalizationFilter.html)|SoraniNormalizationTokenFilter|Normalizuje reprezentace Unicode Sorani textu.<br /><br /> **Možnosti**<br /><br /> Žádné.|  
|Stemmer|StemmerTokenFilter|Lemmatizátor filtru určeného pro konkrétní jazyk.<br /><br /> **Možnosti**<br /><br /> jazyk (typ: string) – povolené hodnoty jsou: <br /> -   ["Arabský"](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/ar/ArabicStemmer.html)<br />-   ["Arménský"](https://snowball.tartarus.org/algorithms/armenian/stemmer.html)<br />-   ["Baskičtina"](https://snowball.tartarus.org/algorithms/basque/stemmer.html)<br />-   ["brazilská"](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/br/BrazilianStemmer.html)<br />-   ["bulharský"](https://members.unine.ch/jacques.savoy/Papers/BUIR.pdf)<br />-   ["Katalánština"](https://snowball.tartarus.org/algorithms/catalan/stemmer.html)<br />-   ["České"](https://portal.acm.org/citation.cfm?id=1598600)<br />-   ["Dánská"](https://snowball.tartarus.org/algorithms/danish/stemmer.html)<br />-   ["dutch"](https://snowball.tartarus.org/algorithms/dutch/stemmer.html)<br />-   ["dutchKp"](https://snowball.tartarus.org/algorithms/kraaij_pohlmann/stemmer.html)<br />-   ["Angličtina"](https://snowball.tartarus.org/algorithms/porter/stemmer.html)<br />-   ["lightEnglish"](https://ciir.cs.umass.edu/pubfiles/ir-35.pdf)<br />-   ["minimalEnglish"](https://www.researchgate.net/publication/220433848_How_effective_is_suffixing)<br />-   ["possessiveEnglish"](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/en/EnglishPossessiveFilter.html)<br />-   ["porter2"](https://snowball.tartarus.org/algorithms/english/stemmer.html)<br />-   ["lovins"](https://snowball.tartarus.org/algorithms/lovins/stemmer.html)<br />-   ["finština"](https://snowball.tartarus.org/algorithms/finnish/stemmer.htm)<br />-   ["lightFinnish"](https://clef.isti.cnr.it/2003/WN_web/22.pdf)<br />-   ["francouzština"](https://snowball.tartarus.org/algorithms/french/stemmer.html)<br />-   ["lightFrench"](https://dl.acm.org/citation.cfm?id=1141523)<br />-   ["minimalFrench"](https://dl.acm.org/citation.cfm?id=318984)<br />-   ["Galicijština"](https://bvg.udc.es/recursos_lingua/stemming.jsp)<br />-   ["minimalGalician"](https://bvg.udc.es/recursos_lingua/stemming.jsp)<br />-   ["němčina"](https://snowball.tartarus.org/algorithms/german/stemmer.html)<br />-   ["german2"](https://snowball.tartarus.org/algorithms/german2/stemmer.html)<br />-   ["lightGerman"](https://dl.acm.org/citation.cfm?id=1141523)<br />-   ["minimalGerman"](https://members.unine.ch/jacques.savoy/clef/morpho.pdf)<br />-   ["řecké"](https://sais.se/mthprize/2007/ntais2007.pdf)<br />-   ["hindština"](https://computing.open.ac.uk/Sites/EACLSouthAsia/Papers/p6-Ramanathan.pdf)<br />-   ["maďarské"](https://snowball.tartarus.org/algorithms/hungarian/stemmer.html)<br />-   ["lightHungarian"](https://dl.acm.org/citation.cfm?id=1141523&dl=ACM&coll=DL&CFID=179095584&CFTOKEN=80067181)<br />-   ["indonéská"](https://www.illc.uva.nl/Publications/ResearchReports/MoL-2003-02.text.pdf)<br />-   ["Irská"](https://snowball.tartarus.org/otherapps/oregan/intro.html)<br />-   ["italské"](https://snowball.tartarus.org/algorithms/italian/stemmer.html)<br />-   ["lightItalian"](https://www.ercim.eu/publication/ws-proceedings/CLEF2/savoy.pdf)<br />-   ["sorani"](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/ckb/SoraniStemmer.html)<br />-   ["Lotyšský"](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/lv/LatvianStemmer.html)<br />-   ["norská"](https://snowball.tartarus.org/algorithms/norwegian/stemmer.html)<br />-   ["lightNorwegian"](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/no/NorwegianLightStemmer.html)<br />-   ["minimalNorwegian"](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/no/NorwegianMinimalStemmer.html)<br />-   ["lightNynorsk"](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/no/NorwegianLightStemmer.html)<br />-   ["minimalNynorsk"](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/no/NorwegianMinimalStemmer.html)<br />-   ["portugalština"](https://snowball.tartarus.org/algorithms/portuguese/stemmer.html)<br />-   ["lightPortuguese"](https://dl.acm.org/citation.cfm?id=1141523&dl=ACM&coll=DL&CFID=179095584&CFTOKEN=80067181)<br />-   ["minimalPortuguese"](https://www.inf.ufrgs.br/~buriol/papers/Orengo_CLEF07.pdf)<br />-   ["portugueseRslp"](https://www.inf.ufrgs.br//~viviane/rslp/index.htm)<br />-   ["rumunský"](https://snowball.tartarus.org/algorithms/romanian/stemmer.html)<br />-   ["ruština"](https://snowball.tartarus.org/algorithms/russian/stemmer.html)<br />-   ["lightRussian"](https://doc.rero.ch/lm.php?url=1000%2C43%2C4%2C20091209094227-CA%2FDolamic_Ljiljana_-_Indexing_and_Searching_Strategies_for_the_Russian_20091209.pdf)<br />-   ["Španělsko"](https://snowball.tartarus.org/algorithms/spanish/stemmer.html)<br />-   ["lightSpanish"](https://www.ercim.eu/publication/ws-proceedings/CLEF2/savoy.pdf)<br />-   ["Švédská"](https://snowball.tartarus.org/algorithms/swedish/stemmer.html)<br />-   ["lightSwedish"](https://clef.isti.cnr.it/2003/WN_web/22.pdf)<br />-   ["turecké"](https://snowball.tartarus.org/algorithms/turkish/stemmer.html)|  
|[stemmer_override](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/StemmerOverrideFilter.html)|StemmerOverrideTokenFilter|Podmínek vyplýval slovníku jsou označeny jako klíčová slova, což zabraňuje slovního rozboru v řetězu. Musí být umístěna před lemmatizátor filtrů.<br /><br /> **Možnosti**<br /><br /> pravidla (typ: pole řetězců)-slovního rozboru pravidla v následujícím formátu "slovo = > stem" například "spustili = > Spustit". Výchozí hodnota je prázdný seznam.  Povinná hodnota.|  
|[stopwords](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/StopFilter.html)|StopwordsTokenFilter|Odebere nevýznamová slova z tokenu datového proudu. Ve výchozím nastavení používá filtr seznamu slov předdefinované stop pro angličtinu.<br /><br /> **Možnosti**<br /><br /> stopslova (typ: pole řetězců) – seznam stopwords. Nelze zadat, pokud je zadán stopwordsList.<br /><br /> stopwordsList (typ: string) – předdefinovaného seznamu stopwords. Nelze zadat, pokud je zadán stopwords. Povolené hodnoty jsou: "Arabský", "Arménský", "Baskičtina", "brazilská", "bulharský", "Katalánština", "České", "Dánská", "Nizozemská", "Angličtina", "finština", "francouzština", "Galicijština", "Německé", "řecké", "hindština", "maďarské", "indonéská", "Irská", "italské", "lotyština ","norština","perský","portugalština","rumunský","ruština","sorani","Španělsko","Švédská","thajský","turecké", výchozí:"Angličtina". Nelze zadat, pokud je zadán stopwords. <br /><br /> ignoreCase (typ: bool) – Pokud je hodnota true, všechna slova jsou nižší první notaci. Výchozí hodnota je false.<br /><br /> removeTrailing (typ: bool) – Pokud je hodnota true, je slovo stop-li ignorovat poslední hledaný termín. Výchozí hodnota je true.
|[synonym](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/synonym/SynonymFilter.html)|SynonymTokenFilter|Odpovídá jedné nebo více slov synonym ve službě token datového proudu.<br /><br /> **Možnosti**<br /><br /> synonyma (typ: řetězec pole) – povinné. Seznam synonym v jednom ze dvou následujících formátů:<br /><br /> -neuvěřitelné, neuvěřitelně, slavných = > úžasné – všechny podmínky na levé straně = > symbol budou nahrazeny všechny podmínky na jeho pravé straně.<br /><br /> -neuvěřitelné, neuvěřitelně, slavných, úžasné - čárkou oddělený seznam ekvivalentních slov. Rozbalit možnosti změnit způsob interpretace tento seznam.<br /><br /> ignoreCase (typ: bool) – případ složení vstup pro porovnání. Výchozí hodnota je false.<br /><br /> Rozbalte (typ: bool) – Pokud je nastavena hodnota true a všechna slova v seznamu synonyma (Pokud = > není použit zápis) mapování mezi sebou. <br />V následujícím seznamu: neuvěřitelné, neuvěřitelně, slavných, úžasné je ekvivalentní: neuvěřitelné, neuvěřitelně, slavných, úžasné = > neuvěřitelné, neuvěřitelně, slavných, úžasné<br /><br />-Li hodnotu false, v následujícím seznamu: neuvěřitelné, neuvěřitelně, slavných, úžasné jsou ekvivalentní: neuvěřitelné, neuvěřitelně, slavných, úžasné = > neuvěřitelné.|  
|[trim](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/TrimFilter.html)|(typ platí, pouze když jsou k dispozici možnosti)  |Ořízne počáteční a koncové prázdné znaky z tokenů. |  
|[zkrátit](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/TruncateTokenFilter.html)|TruncateTokenFilter|Zkrátí podmínky do zadané délky.<br /><br /> **Možnosti**<br /><br /> Délka (typ: int) – výchozí: 300, maximální: 300. Povinná hodnota.|  
|[Jedinečný](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/RemoveDuplicatesTokenFilter.html)|UniqueTokenFilter|Filtruje tokeny se stejný text jako předchozí token.<br /><br /> **Možnosti**<br /><br /> onlyOnSamePosition (typ: bool) – Pokud je toto nastavení, odeberte duplicitní položky pouze na stejné pozici. Výchozí hodnota je true.|  
|[velká písmena](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/UpperCaseFilter.html)|(typ platí, pouze když jsou k dispozici možnosti)  |Normalizuje token text na velká písmena. |  
|[word_delimiter](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/WordDelimiterFilter.html)|WordDelimiterTokenFilter|Rozdělí slova subwords a provede volitelné transformace podslovo skupiny.<br /><br /> **Možnosti**<br /><br /> generateWordParts (typ: bool)-způsobí, že část slova, která chcete vygenerovat, například "AzureSearch" stane "Azure" "Vyhledat". Výchozí hodnota je true.<br /><br /> generateNumberParts (typ: bool)-způsobí, že číslo subwords vygenerování. Výchozí hodnota je true.<br /><br /> catenateWords (typ: bool)-příčiny maximální spuštění aplikace word částí k catenated, například "Azure Search" stane "AzureSearch". Výchozí hodnota je false.<br /><br /> catenateNumbers (typ: bool) – spustí způsobí, že maximální počet částí k být catenated, například "1-2" se změní na "12". Výchozí hodnota je false.<br /><br /> catenateAll (typ: bool)-způsobí, že všechny podslovo částí k být catenated, například "Azure-Search-1" se změní "AzureSearch1". Výchozí hodnota je false.<br /><br /> splitOnCaseChange (typ: bool) – Pokud je nastavena hodnota true a rozdělení slov na caseChange, například "AzureSearch" stane "Azure" "Vyhledat". Výchozí hodnota je true.<br /><br /> preserveOriginal - slova původní příčiny zachovány a přidat do seznamu podslovo Výchozí hodnota je false.<br /><br /> splitOnNumerics (typ: bool) – Pokud je nastavena hodnota true a rozdělení čísel, například "Azure1Search" stane "Azure" "1" "Vyhledat". Výchozí hodnota je true.<br /><br /> stemEnglishPossessive (typ: bool) – způsobí, že na konci "na" pro každý podslovo odeberou. Výchozí hodnota je true.<br /><br /> protectedWords (typ: pole řetězců)-tokeny k ochraně před jsou oddělené. Výchozí hodnota je prázdný seznam.|  

 <sup>1</sup> token filtrování typů mají vždy předponu v kódu s "#Microsoft.Azure.Search" tak, aby "ArabicNormalizationTokenFilter" by ve skutečnosti byl zadán jako "#Microsoft.Azure.Search.ArabicNormalizationTokenFilter".  Odebrali jsme předpona, kterou chcete omezit šířku v tabulce, ale nezapomeňte zadat v kódu.  

> [!NOTE]
> Vyžaduje se, že nakonfigurujete vaše vlastní analyzátor pro takové tokeny delší než 300 znaků. Indexování dokumentů s tyto tokeny se nezdaří. Trim je nebo je ignorovat, použijte **TruncateTokenFilter** a **LengthTokenFilter** v uvedeném pořadí.


## <a name="see-also"></a>Další informace najdete v tématech  
 [Rozhraní REST služby Azure Search](https://docs.microsoft.com/rest/api/searchservice/)   
 [Analyzátory ve službě Azure Search > Příklady](https://docs.microsoft.com/azure/search/search-analyzers#examples)    
 [Vytvořit Index &#40;rozhraní REST API služby Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)  
