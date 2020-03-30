---
title: Přidání vlastních analyzátorů do řetězcových polí
titleSuffix: Azure Cognitive Search
description: Nakonfigurujte textové tokenizátory a filtry znaků používané v fulltextových vyhledávacích dotazech Azure Cognitive Search.
manager: nitinme
author: Yahnoosh
ms.author: jlembicz
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
ms.openlocfilehash: 9bf0fb1a33a98031a78155a3956ac6d6abe33029
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113632"
---
# <a name="add-custom-analyzers-to-string-fields-in-an-azure-cognitive-search-index"></a>Přidání vlastních analyzátorů do řetězcových polí v indexu Azure Cognitive Search

*Vlastní analyzátor* je specifický typ [analyzátoru textu,](search-analyzers.md) který se skládá z uživatelem definované kombinace existujícího tokenizeru a volitelných filtrů. Kombinací tokenizerů a filtrů novými způsoby můžete přizpůsobit zpracování textu ve vyhledávači, abyste dosáhli konkrétních výsledků. Můžete například vytvořit vlastní analyzátor s *filtrem char* k odstranění značek HTML před tokenizací textových vstupů.

 Můžete definovat více vlastních analyzátorů, které mění kombinaci filtrů, ale každé pole může použít pouze jeden analyzátor pro analýzu indexování a jeden pro analýzu vyhledávání. Obrázek toho, jak vypadá analyzátor zákazníků, najdete v [příkladu vlastního analyzátoru](search-analyzers.md#Custom-analyzer-example).

## <a name="overview"></a>Přehled

 Úlohou [fulltextového vyhledávače](search-lucene-query-architecture.md)je zpracovat a ukládat dokumenty způsobem, který umožňuje efektivní dotazování a načítání. Na vysoké úrovni, to vše přijde na extrahování důležitých slov z dokumentů, jejich uvedení do indexu a pak pomocí indexu najít dokumenty, které odpovídají slova daného dotazu. Proces extrahování slov z dokumentů a vyhledávacích dotazů se nazývá *lexikální analýza*. Součásti, které provádějí lexikální analýzu, se nazývají *analyzátory*.

 V Azure Cognitive Search si můžete vybrat ze sady předdefinovaných analyzátorů pro jazykově nezávislá v tabulce [Analyzátory](#AnalyzerTable) nebo analyzátory specifické pro jazyk uvedené v [analyzátorech jazyka &#40;azure cognitive search služby REST API&#41;](index-add-language-analyzers.md). Máte také možnost definovat vlastní analyzátory.  

 Vlastní analyzátor umožňuje převzít kontrolu nad procesem převodu textu na indexovatelné a prohledávatelné tokeny. Jedná se o uživatelsky definovanou konfiguraci skládající se z jednoho předdefinovaného tokenizeru, jednoho nebo více filtrů tokenů a jednoho nebo více filtrů znaků. Tokenizer je zodpovědný za rozdělení textu na tokeny a filtry tokenů pro úpravu tokenů vyzařovaných tokenizátorem. Char filtry jsou použity pro přípravu vstupní text před tím, než je zpracován tokenizer. Například znakový filtr může nahradit určité znaky nebo symboly.

 Mezi oblíbené scénáře povolené vlastními analyzátory patří:  

- Fonetické hledání. Přidejte fonetický filtr, který umožní vyhledávání podle toho, jak slovo zní, ne podle toho, jak se píše.  

- Zakažte lexikální analýzu. Analyzátor klíčových slov slouží k vytvoření prohledávatelných polí, která nejsou analyzována.  

- Rychlé vyhledávání předpony/přípony. Přidejte filtr tokenu N-gram okraje do indexu předpon slov, abyste povolili rychlou shodu předpony. Zkombinujte jej s filtrem reverzní token, aby se párování přípona.  

- Vlastní tokenizace. Tokenizér mezer y například slouží k přerušení vět na tokeny pomocí mezer y jako oddělovače  

- ASCII skládání. Přidejte standardní filtr skládání ASCII pro normalizaci diakritiky jako ö nebo ê ve vyhledávacích dotazech.  

  Tato stránka obsahuje seznam podporovaných analyzátorů, tokenizerů, filtrů tokenů a filtrů znaků. Můžete také najít popis změn definice indexu s příklad použití. Další informace o základní technologii využité v implementaci Azure Cognitive Search najdete v článku [Souhrn balíčku analýzy (Lucene).](https://lucene.apache.org/core/6_0_0/core/org/apache/lucene/codecs/lucene60/package-summary.html) Příklady konfigurací analyzátoru najdete v tématu [Přidání analyzátorů v Azure Cognitive Search](search-analyzers.md#examples).

## <a name="validation-rules"></a>Ověřovací pravidla  
 Názvy analyzátorů, tokenizerů, filtrů tokenů a filtrů znaků musí být jedinečné a nemohou být stejné jako všechny předdefinované analyzátory, tokenizátory, filtry tokenů nebo filtry znaků. Názvy, které se již používají, naleznete v [odkazu na vlastnosti.](#PropertyReference)

## <a name="create-custom-analyzers"></a>Vytvoření vlastních analyzátorů
 Vlastní analyzátory můžete definovat v době vytvoření indexu. Syntaxe pro určení vlastního analyzátoru je popsána v této části. Můžete se také seznámit se syntaxí kontrolou ukázkových definic v [add analyzátorech v Azure Cognitive Search](search-analyzers.md#examples).  

 Definice analyzátoru obsahuje název, typ, jeden nebo více znaků filtry, maximálně jeden tokenizer a jeden nebo více filtrů tokenů pro zpracování po tokenizaci. Char filers jsou použity před tokenizace. Filtry tokenů a znakové filtry se používají zleva doprava.

 Je `tokenizer_name` `token_filter_name_1` název tokenizer a `token_filter_name_2` jsou názvy filtrů tokenů a `char_filter_name_1` a `char_filter_name_2` jsou názvy char filtry (viz [Tokenizers](#Tokenizers), Token [filtry](#TokenFilters) a Char filtry tabulky platné hodnoty).

Definice analyzátoru je součástí většího indexu. Informace o zbytku indexu najdete v tématu [Vytvoření rozhraní API indexu.](https://docs.microsoft.com/rest/api/searchservice/create-index)

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
>  Vlastní analyzátory, které vytvoříte, nejsou na webu Azure Portal vystaveny. Jediný způsob, jak přidat vlastní analyzátor je prostřednictvím kódu, který umožňuje volání do rozhraní API při definování indexu.  

 V rámci definice indexu můžete umístit tuto část kdekoli v těle požadavku vytvořit index, ale obvykle to jde na konci:  

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

Definice pro char filtry, tokenizers a token filtry jsou přidány do indexu pouze v případě, že nastavujete vlastní možnosti. Chcete-li použít existující filtr nebo tokenizer tak, jak je, zadejte jej podle názvu v definici analyzátoru.

<a name="Testing custom analyzers"></a>

## <a name="test-custom-analyzers"></a>Testování vlastních analyzátorů

Pomocí operace **Test Analyzer** v [rozhraní REST API](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) můžete zjistit, jak analyzátor rozdělí daný text na tokeny.

**Požadavek**
```
  POST https://[search service name].search.windows.net/indexes/[index name]/analyze?api-version=[api-version]
  Content-Type: application/json
    api-key: [admin key]

  {
     "analyzer":"my_analyzer",
     "text": "Vis-à-vis means Opposite"
  }
```
**Reakce**
```
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
```

## <a name="update-custom-analyzers"></a>Aktualizace vlastních analyzátorů

Jakmile je definován analyzátor, tokenizer, filtr tokenů nebo filtr char, nelze jej změnit. Nové lze přidat do existujícího indexu `allowIndexDowntime` pouze v případě, že příznak je nastaven a true v požadavku na aktualizaci indexu:

```
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=[api-version]&allowIndexDowntime=true
```

Tato operace přenese index do režimu offline po dobu nejméně několika sekund, což způsobí selhání indexování a požadavků na dotazy. Dostupnost indexu může být snížena na několik minut po aktualizaci indexu nebo déle pro velmi velké indexy, ale tyto efekty jsou dočasné a nakonec vyřešit na vlastní pěst.

 <a name="ReferenceIndexAttributes"></a>

## <a name="analyzer-reference"></a>Odkaz na analyzátor

V následujících tabulkách jsou uvedeny vlastnosti konfigurace analyzátorů, tokenizerů, filtrů tokenů a části znakového filtru definice indexu. Struktura analyzátoru, tokenizer nebo filtr v indexu se skládá z těchto atributů. Informace o přiřazení hodnoty naleznete v [odkazu na vlastnost .](#PropertyReference)

### <a name="analyzers"></a>Analyzátory

U analyzátorů se atributy indexu liší v závislosti na tom, zda používáte předdefinované nebo vlastní analyzátory.

#### <a name="predefined-analyzers"></a>Předdefinované analyzátory

|||  
|-|-|  
|Name (Název)|Musí obsahovat pouze písmena, číslice, mezery, pomlčky nebo podtržítka, může začínat a končit pouze alfanumerickými znaky a je omezeno na 128 znaků.|  
|Typ|Typ analyzátoru ze seznamu podporovaných analyzátorů. Podívejte se na **sloupec analyzer_type** v tabulce [Analyzátory](#AnalyzerTable) níže.|  
|Možnosti|Musí se jednat o platné možnosti předdefinovaného analyzátoru uvedené ho v tabulce [analyzátorů](#AnalyzerTable) níže.|  

#### <a name="custom-analyzers"></a>Vlastní analyzátory

|||  
|-|-|  
|Name (Název)|Musí obsahovat pouze písmena, číslice, mezery, pomlčky nebo podtržítka, může začínat a končit pouze alfanumerickými znaky a je omezeno na 128 znaků.|  
|Typ|Musí být "#Microsoft.Azure.Search.CustomAnalyzer".|  
|Filtry svatojáne|Nastavte jeden z předdefinovaných sáčcích filtrů uvedených v tabulce [Sát filtry](#char-filters-reference) nebo na vlastní znakový filtr určený v definici indexu.|  
|Tokenizér|Povinná hodnota. Nastavte jeden z předdefinovaných tokenizerů uvedených v tabulce [Tokenizers](#Tokenizers) níže nebo vlastní tokenizer zadaný v definici indexu.|  
|Tokenfilters|Nastavte jeden z předdefinovaných filtrů tokenů uvedených v tabulce [filtrů tokenů](#TokenFilters) nebo na vlastní filtr tokenu zadaný v definici indexu.|  

> [!NOTE]
> Je nutné nakonfigurovat vlastní analyzátor nevytvářet tokeny delší než 300 znaků. Indexování se nezdaří pro dokumenty s takovými tokeny. Chcete-li je oříznout nebo ignorovat, použijte **TruncateTokenFilter** a **LengthTokenFilter.**  Zkontrolujte, zda [**jsou filtry tokenů**](#TokenFilters) odkazováno.

<a name="CharFilter"></a>

### <a name="char-filters"></a>Znakové filtry

 Znak filtr se používá k přípravě vstupní text před zpracováním tokenizer. Mohou například nahradit určité znaky nebo symboly. Ve vlastním analyzátoru můžete mít více filtrů znaků. Char filtry spustit v pořadí, ve kterém jsou uvedeny.  

|||  
|-|-|  
|Name (Název)|Musí obsahovat pouze písmena, číslice, mezery, pomlčky nebo podtržítka, může začínat a končit pouze alfanumerickými znaky a je omezeno na 128 znaků.|  
|Typ|Typ filtru Char ze seznamu podporovaných filtrů znaků. Viz **char_filter_type** sloupec v tabulce [Sáčné filtry](#char-filters-reference) níže.|  
|Možnosti|Musí být platné možnosti daného typu [sáčcích.](#char-filters-reference)|  

### <a name="tokenizers"></a>Tokenizátory

 Tokenizer rozdělí souvislý text do posloupnosti tokenů, jako je například rozdělení věty na slova.  

 Můžete zadat přesně jeden tokenizer na vlastní analyzátor. Pokud potřebujete více než jeden tokenizer, můžete vytvořit více vlastních analyzátorů a přiřadit je na základě pole po poli ve schématu indexu.  
Vlastní analyzátor můžete použít předdefinovaný tokenizer s výchozí nebo vlastní možnosti.  

|||  
|-|-|  
|Name (Název)|Musí obsahovat pouze písmena, číslice, mezery, pomlčky nebo podtržítka, může začínat a končit pouze alfanumerickými znaky a je omezeno na 128 znaků.|  
|Typ|Tokenizer název ze seznamu podporovaných tokenizers. Viz **tokenizer_type** sloupec v tabulce [Tokenizers](#Tokenizers) níže.|  
|Možnosti|Musí být platné možnosti daného typu tokenizer udávaný v tabulce [Tokenizers](#Tokenizers) níže.|  

### <a name="token-filters"></a>Filtry tokenů

 Filtr tokenů se používá k odfiltrování nebo úpravě tokenů generovaných tokenizérem. Můžete například zadat filtr s malou písmena, který převede všechny znaky na malá písmena.   
Ve vlastním analyzátoru můžete mít více filtrů tokenů. Filtry tokenů jsou spuštěny v pořadí, ve kterém jsou uvedeny.  

|||  
|-|-|  
|Name (Název)|Musí obsahovat pouze písmena, číslice, mezery, pomlčky nebo podtržítka, může začínat a končit pouze alfanumerickými znaky a je omezeno na 128 znaků.|  
|Typ|Název filtru tokenu ze seznamu podporovaných filtrů tokenů. Viz **token_filter_type** sloupec v tabulce [filtrů tokenů](#TokenFilters) níže.|  
|Možnosti|Musí být [token filtry](#TokenFilters) daného typu filtru tokenu.|  

<a name="PropertyReference"></a>  

## <a name="property-reference"></a>Odkaz na vlastnost

Tato část obsahuje platné hodnoty pro atributy zadané v definici vlastního analyzátoru, tokenizeru, filtru char nebo filtru tokenů v indexu. Analyzátory, tokenizers a filtry, které jsou implementovány pomocí Apache Lucene mají odkazy na dokumentaci Lucene API.

<a name="AnalyzerTable"></a>

###  <a name="predefined-analyzers-reference"></a>Předdefinovaný odkaz analyzátorů

|**analyzer_name**|**analyzer_type**  <sup>1.</sup>|**Popis a možnosti**|  
|-|-|-|  
|[klíčové slovo](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html)| (typ platí pouze v případě, že jsou k dispozici možnosti) |Zachází s celým obsahem pole jako s jediným tokenem. To je užitečné pro data, jako jsou PSČ, ID a některé názvy produktů.|  
|[Vzor](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/PatternAnalyzer.html)|Analyzátor vzorů|Flexibilně odděluje text do termínů pomocí vzoru regulárního výrazu.<br /><br /> **Možnosti**<br /><br /> malá písmena (typ: bool) - Určuje, zda jsou termíny malá písmena. Výchozí hodnota je true.<br /><br /> [vzorek](https://docs.oracle.com/javase/7/docs/api/java/util/regex/Pattern.html?is-external=true) (typ: řetězec) - Vzor regulárního výrazu, který odpovídá oddělovačům tokenů. Výchozí hodnota je \w+.<br /><br /> [příznaky](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) (typ: řetězec) - Příznaky regulárních výrazů. Výchozí hodnota je prázdný řetězec. Povolené hodnoty: CANON_EQ, CASE_INSENSITIVE, KOMENTÁŘE, DOTALL, LITERAL, MULTILINE, UNICODE_CASE, UNIX_LINES<br /><br /> stopwords (typ: string array) - Seznam stopwords. Výchozí hodnota je prázdný seznam.|  
|[Jednoduché](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/SimpleAnalyzer.html)|(typ platí pouze v případě, že jsou k dispozici možnosti) |Rozdělí text na nepísmena a převede je na malá písmena. |  
|[Standardní](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) <br />(Také odkazoval se na jako standard.lucene)|StandardAnalyzer|Standardní Analyzátor Lucene, který se skládá ze standardního tokenizátoru, filtru s nižšími písmeny a filtru stop.<br /><br /> **Možnosti**<br /><br /> maxTokenLength (typ: int) - Maximální délka tokenu. Výchozí hodnota je 255. Tokeny delší než maximální délka jsou rozděleny. Maximální délka tokenu, který lze použít, je 300 znaků.<br /><br /> stopwords (typ: string array) - Seznam stopwords. Výchozí hodnota je prázdný seznam.|  
|standardasciifolding.lucene|(typ platí pouze v případě, že jsou k dispozici možnosti) |Standardní analyzátor s ascii skládacím filtrem. |  
|[Stop](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/StopAnalyzer.html)|StopAnalyzer|Rozdělí text na non-letters, použije filtry tokenů s malá písmena a stopword.<br /><br /> **Možnosti**<br /><br /> stopwords (typ: string array) - Seznam stopwords. Výchozí je předdefinovaný seznam pro angličtinu. |  
|[Mezery](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html)|(typ platí pouze v případě, že jsou k dispozici možnosti) |Analyzátor, který používá tokenizéru prázdného místa. Tokeny, které jsou delší než 255 znaků jsou rozděleny.|  

 <sup>1</sup> Typy analyzátoru jsou vždy předponou v kódu s "#Microsoft.Azure.Search" tak, aby "PatternAnalyzer" by ve skutečnosti být zadán jako "#Microsoft.Search.Pattern.PatternAnalyzer". Odebrali jsme předponu pro stručnost, ale předpona je vyžadována ve vašem kódu. 
 
Analyzer_type je k dispozici pouze pro analyzátory, které lze přizpůsobit. Pokud neexistují žádné možnosti, jako je tomu v případě analyzátoru klíčových slov, neexistuje žádný přidružený typ #Microsoft.Azure.Search.


<a name="CharFilter"></a>

###  <a name="char-filters-reference"></a>Odkaz na filtry svitku

V následující tabulce jsou filtry znaků, které jsou implementovány pomocí Apache Lucene, propojeny s dokumentací rozhraní API Lucene.

|**char_filter_name**|**char_filter_type** <sup>1</sup>|**Popis a možnosti**|  
|-|-|-|
|[html_strip](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/charfilter/HTMLStripCharFilter.html)|(typ platí pouze v případě, že jsou k dispozici možnosti)  |Znakový filtr, který se pokouší odstranit konstrukce HTML.|  
|[mapování](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/charfilter/MappingCharFilter.html)|MappingCharFilter|Znakový filtr, který aplikuje mapování definovaná s možností mapování. Párování je chamtivý (nejdelší vzor odpovídající v daném bodě vyhrává). Nahrazení je povoleno být prázdný řetězec.<br /><br /> **Možnosti**<br /><br /> mapování (typ: pole řetězců) - Seznam mapování následujícího formátu: "a=>b" (všechny výskyty znaku "a" jsou nahrazeny znakem "b"). Povinná hodnota.|  
|[pattern_replace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternReplaceCharFilter.html)|VzorReplaceCharFilter|Znakový filtr, který nahrazuje znaky ve vstupním řetězci. Používá regulární výraz k identifikaci sekvencí znaků k zachování a náhradní vzorek k identifikaci znaků, které mají být nahrazeny. Například vstupní text = "aa bb aa bb",\\pattern="(aa) \s+(bb)" replacement="$1#$2", result = "aa#bb aa#bb".<br /><br /> **Možnosti**<br /><br /> vzorek (typ: řetězec) - Povinné.<br /><br /> nahrazení (typ: řetězec) - Povinné.|  

 <sup>1</sup> Char typy filtrů jsou vždy předponou v kódu s "#Microsoft.Azure.Search" tak, aby "MappingCharFilter" by ve skutečnosti být zadán jako "#Microsoft.Azure.Search.MappingCharFilter. Odebrali jsme předponu, abychom zmenšili šířku tabulky, ale nezapomeňte ji zahrnout do kódu. Všimněte si, že char_filter_type je k dispozici pouze pro filtry, které lze přizpůsobit. Pokud neexistují žádné možnosti, jako je tomu v případě html_strip, neexistuje žádný přidružený typ #Microsoft.Azure.Search.

<a name="Tokenizers"></a>

###  <a name="tokenizers-reference"></a>Odkaz tokenizerů

V následující tabulce tokenizers, které jsou implementovány pomocí Apache Lucene jsou propojeny s dokumentací Rozhraní API Lucene.

|**tokenizer_name**|**tokenizer_type** <sup>1</sup>|**Popis a možnosti**|  
|-|-|-|  
|[Klasické](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/standard/ClassicTokenizer.html)|ClassicTokenizer|Tokenizer založený na gramatice, který je vhodný pro zpracování většiny dokumentů v evropském jazyce.<br /><br /> **Možnosti**<br /><br /> maxTokenLength (typ: int) - Maximální délka tokenu. Výchozí hodnota: 255, maximálně: 300. Tokeny delší než maximální délka jsou rozděleny.|  
|[edgeNGram](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html)|EdgeNGramTokenizer|Tokenizes vstup z okraje do n-gramů dané velikosti(s).<br /><br /> **Možnosti**<br /><br /> minGram (typ: int) - Výchozí: 1, maximum: 300.<br /><br /> maxGram (typ: int) - Výchozí: 2, maximum: 300. Musí být větší než minGram.<br /><br /> tokenChars (typ: pole řetězců) - Třídy znaků, které mají být uchovány v tokenech. Povolené hodnoty: <br />"písmeno", "číslice", "mezery", "interpunkce", "symbol". Výchozí hodnota je prázdná – zachová všechny znaky. |  
|[keyword_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html)|Klíčové slovoTokenizerV2|Vyzařuje celý vstup jako jeden token.<br /><br /> **Možnosti**<br /><br /> maxTokenLength (typ: int) - Maximální délka tokenu. Výchozí hodnota: 256, maximálně: 300. Tokeny delší než maximální délka jsou rozděleny.|  
|[Písmeno](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LetterTokenizer.html)|(typ platí pouze v případě, že jsou k dispozici možnosti)  |Rozdělí text na nepísmena. Tokeny, které jsou delší než 255 znaků jsou rozděleny.|  
|[Malá písmena](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseTokenizer.html)|(typ platí pouze v případě, že jsou k dispozici možnosti)  |Rozdělí text na nepísmena a převede je na malá písmena. Tokeny, které jsou delší než 255 znaků jsou rozděleny.|  
| microsoft_language_tokenizer| MicrosoftLanguageTokenizer| Rozdělí text pomocí pravidel specifických pro jazyk.<br /><br /> **Možnosti**<br /><br /> maxTokenLength (typ: int) - Maximální délka tokenu, výchozí hodnota: 255, maximum: 300. Tokeny delší než maximální délka jsou rozděleny. Tokeny delší než 300 znaků jsou nejprve rozděleny do tokenů o délce 300 a pak každý z těchto tokenů je rozdělen na základě maxTokenLength nastavit.<br /><br />isSearchTokenizer (typ: bool) - Nastavte na hodnotu true, pokud se používá jako tokenizér vyhledávání, nastavený na hodnotu false, pokud je použit jako tokenizér indexování. <br /><br /> jazyk (typ: řetězec) - Jazyk k použití, výchozí "angličtina". Povolené hodnoty zahrnují:<br />"bangla", "bulgarian", "catalan", "chineseSimplified", "chineseTraditional", "croatian", "czech", "danish", "dutch", "english", "french", "german", "greek", "gujarati", "hindština", "iceland", """, "italian", "japanese", "kannada", " korean", "malajský", "malayalam", "marathi", "norwegianBokmaal", "polish", "portuguese", "portugueseBrazilian", "punjabi", "romanian", "russian", "serbianCyrillic", "serbianLatin", "slovenian", "spanish", "swedish", "tamil", "telugu", "thai", " ukrajinská", "urdu", "vietnamské" |
| microsoft_language_stemming_tokenizer | MicrosoftLanguageStemmingTokenizer| Rozdělí text pomocí pravidel specifických pro jazyk a omezí počet slov do základních formulářů.<br /><br /> **Možnosti**<br /><br />maxTokenLength (typ: int) - Maximální délka tokenu, výchozí hodnota: 255, maximum: 300. Tokeny delší než maximální délka jsou rozděleny. Tokeny delší než 300 znaků jsou nejprve rozděleny do tokenů o délce 300 a pak každý z těchto tokenů je rozdělen na základě maxTokenLength nastavit.<br /><br /> isSearchTokenizer (typ: bool) - Nastavte na hodnotu true, pokud se používá jako tokenizér vyhledávání, nastavený na hodnotu false, pokud je použit jako tokenizér indexování.<br /><br /> jazyk (typ: řetězec) - Jazyk k použití, výchozí "angličtina". Povolené hodnoty zahrnují:<br />"arabština", "bangla", "bulharština", "katalánština", "chorvatština", "čeština", "dánština", "nizozemština", "angličtina", "estonština", "finština", "francouzština", "řečtina", "gujarati", "hebrejština", "hindština", "maďarština", "islandština", "indonéština", "italština", "kannada", "kannada", lotyšský", "litevský", "malajský", "malayalam", "marathi", "norwegianBokmaal", "polish", "portuguese", "portugueseBrazilian", "punjabi", "romanian", "russian", "serbianCyrillic", "serbianLatin", "slovak", "slovak", "slovenian", "spanish", "swedish", " tamil", "telugu", "turečtina", "ukrajinština", "urdu" |
|[nGram](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenizer.html)|NGramTokenizer|Tokenizes vstup do n-gramů dané velikosti(s).<br /><br /> **Možnosti**<br /><br /> minGram (typ: int) - Výchozí: 1, maximum: 300.<br /><br /> maxGram (typ: int) - Výchozí: 2, maximum: 300. Musí být větší než minGram. <br /><br /> tokenChars (typ: pole řetězců) - Třídy znaků, které mají být uchovány v tokenech. Povolené hodnoty: "písmeno", "číslice", "prázdné znaky", "interpunkce", "symbol". Výchozí hodnota je prázdná – zachová všechny znaky. |  
|[path_hierarchy_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/path/PathHierarchyTokenizer.html)|PathHierarchyTokenizerV2|Tokenizer pro hierarchie podobné cestě.<br /><br /> **Možnosti**<br /><br /> oddělovač (typ: řetězec) - Výchozí: '/.<br /><br /> nahrazení (typ: řetězec) - Pokud je nastavena, nahradí znak oddělovače. Výchozí hodnota je stejná jako hodnota oddělovače.<br /><br /> maxTokenLength (typ: int) - Maximální délka tokenu. Výchozí hodnota: 300, maximálně: 300. Cesty delší než maxTokenLength jsou ignorovány.<br /><br /> reverzní (typ: bool) - Pokud true, generuje token v opačném pořadí. Výchozí hodnota: false.<br /><br /> přeskočit (typ: bool) - Počáteční tokeny přeskočit. Výchozí hodnota je 0.|  
|[Vzor](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternTokenizer.html)|Vzortokenizátor|Tento tokenizer používá regex vzor odpovídající vytvořit odlišné tokeny.<br /><br /> **Možnosti**<br /><br /> [vzorek](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html) (typ: řetězec) - Vzor regulárního výrazu. Výchozí hodnota je \W+. <br /><br /> [příznaky](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) (typ: řetězec) - Příznaky regulárních výrazů. Výchozí hodnota je prázdný řetězec. Povolené hodnoty: CANON_EQ, CASE_INSENSITIVE, KOMENTÁŘE, DOTALL, LITERAL, MULTILINE, UNICODE_CASE, UNIX_LINES<br /><br /> Skupina (typ: int) - Která skupina extrahovat do tokenů. Výchozí hodnota je -1 (rozdělení).|
|[standard_v2](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardTokenizer.html)|StandardTokenizerV2|Zalomí text podle [pravidel segmentace textu unicode](https://unicode.org/reports/tr29/).<br /><br /> **Možnosti**<br /><br /> maxTokenLength (typ: int) - Maximální délka tokenu. Výchozí hodnota: 255, maximálně: 300. Tokeny delší než maximální délka jsou rozděleny.|  
|[uax_url_email](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/standard/UAX29URLEmailTokenizer.html)|UaxUrlEmailTokenizer|Tokenizes url a e-maily jako jeden token.<br /><br /> **Možnosti**<br /><br /> maxTokenLength (typ: int) - Maximální délka tokenu. Výchozí hodnota: 255, maximálně: 300. Tokeny delší než maximální délka jsou rozděleny.|  
|[Mezery](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceTokenizer.html)|(typ platí pouze v případě, že jsou k dispozici možnosti) |Rozdělí text na prázdné znaky. Tokeny, které jsou delší než 255 znaků jsou rozděleny.|  

 <sup>1</sup> Tokenizer Typy jsou vždy předponou v kódu s "#Microsoft.Azure.Search" tak, aby "ClassicTokenizer" by ve skutečnosti být zadán jako "#Microsoft.Azure.Search.ClassicTokenizer". Odebrali jsme předponu, abychom zmenšili šířku tabulky, ale nezapomeňte ji zahrnout do kódu. Všimněte si, že tokenizer_type je k dispozici pouze pro tokenizers, které lze přizpůsobit. Pokud neexistují žádné možnosti, jako je tomu v případě tokenizer písmen, neexistuje žádný přidružený typ #Microsoft.Azure.Search.

<a name="TokenFilters"></a>

###  <a name="token-filters-reference"></a>Odkaz na filtry tokenů

V následující tabulce jsou filtry tokenů, které jsou implementovány pomocí Apache Lucene, propojeny s dokumentací rozhraní API Lucene.

|**token_filter_name**|**token_filter_type** <sup>1</sup>|**Popis a možnosti**|  
|-|-|-|  
|[arabic_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ar/ArabicNormalizationFilter.html)|(typ platí pouze v případě, že jsou k dispozici možnosti)  |Token filtr, který používá arabský normalizátor normalizovat ortografii.|  
|[Apostrof](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/tr/ApostropheFilter.html)|(typ platí pouze v případě, že jsou k dispozici možnosti)  |Proužky všechny znaky po apostrof (včetně apostrof sám). |  
|[usitožování](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html)|AsciiFoldingTokenFilter|Převede abecední, číselné a symbolické znaky Unicode, které nejsou v prvních 127 znaky ASCII ("Základní latinka" Unicode blok) do jejich ASCII ekvivalenty, pokud existuje.<br /><br /> **Možnosti**<br /><br /> preserveOriginal (typ: bool) - Pokud true, původní token je zachována. Výchozí hodnotou je hodnota false.|  
|[cjk_bigram](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/cjk/CJKBigramFilter.html)|CjkBigramTokenFiltr|Vytvoří bigrams cjk termíny, které jsou generovány z StandardTokenizer.<br /><br /> **Možnosti**<br /><br /> ignoreScripts (typ: pole řetězců) - Skripty, které chcete ignorovat. Povolené hodnoty zahrnují: "han", "hiragana", "katakana", "hangul". Výchozí hodnota je prázdný seznam.<br /><br /> outputUnigrams (typ: bool) - Nastavte na hodnotu true, pokud chcete vždy výstup jak unigrams, tak bigramů. Výchozí hodnotou je hodnota false.|  
|[cjk_width](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/cjk/CJKWidthFilter.html)|(typ platí pouze v případě, že jsou k dispozici možnosti)  |Normalizuje rozdíly šířky CJK. Přeloží varianty ASCII s plnou šířkou do ekvivalentních základních latina a poloviční šířka Katakana varianty do ekvivalentní kana. |  
|[Klasické](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/standard/ClassicFilter.html)|(typ platí pouze v případě, že jsou k dispozici možnosti)  |Odstraní anglické přivlastňovací a tečky z akronymů. |  
|[common_grams](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/commongrams/CommonGramsFilter.html)|CommonGramTokenFilter|Při indexování vytvořte bigrams pro často se vyskytující termíny. Jednotlivé termíny jsou stále indexovány příliš, s bigrams překryté.<br /><br /> **Možnosti**<br /><br /> commonWords (typ: řetězec pole) - Sada běžných slov. Výchozí hodnota je prázdný seznam. Povinná hodnota.<br /><br /> ignoreCase (typ: bool) - Pokud true, odpovídající je malá a velká písmena. Výchozí hodnotou je hodnota false.<br /><br /> queryMode (typ: bool) - Generuje bigrams pak odstraní běžná slova a jednotlivé termíny následuje společné slovo. Výchozí hodnotou je hodnota false.|  
|[dictionary_decompounder](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/compound/DictionaryCompoundWordTokenFilter.html)|DictionaryDecompounderTokenFilter|Rozkládá složená slova nalezená v mnoha germánských jazycích.<br /><br /> **Možnosti**<br /><br /> wordList (typ: řetězec pole) - Seznam slov, proti které se má shodovat. Výchozí hodnota je prázdný seznam. Povinná hodnota.<br /><br /> minWordSize (typ: int) - Pouze slova delší než to získat zpracovány. Výchozí hodnota je 5.<br /><br /> minSubwordSize (typ: int) - Pouze podslova delší než je to jsou výstupy. Výchozí hodnota je 2.<br /><br /> maxSubwordSize (typ: int) - Pouze podslova kratší než to jsou výstupy. Výchozí hodnota je 15.<br /><br /> onlyLongestMatch (typ: bool) - Přidejte do výstupu pouze nejdelší odpovídající podslovo. Výchozí hodnotou je hodnota false.|  
|[edgeNGram_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenFilter.html)|EdgeNGramTokenFilterV2|Generuje n-gramů dané velikosti od spuštění z přední nebo zadní straně vstupního tokenu.<br /><br /> **Možnosti**<br /><br /> minGram (typ: int) - Výchozí: 1, maximum: 300.<br /><br /> maxGram (typ: int) - Výchozí: 2, maximálně 300. Musí být větší než minGram.<br /><br /> strana (typ: řetězec) - Určuje, ze které strany vstupu má být n-gram generován. Povolené hodnoty: "přední", "zadní" |  
|[elision](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/util/ElisionFilter.html)|ElisionTokenFilter|Odstraňuje elize. Například "l'avion" (rovina) je převedena na "avion" (rovina).<br /><br /> **Možnosti**<br /><br /> články (typ: pole řetězců) - Sada článků, které chcete odebrat. Výchozí hodnota je prázdný seznam. Pokud není nastaven žádný seznam článků, ve výchozím nastavení jsou odebrány všechny francouzské články.|  
|[german_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/de/GermanNormalizationFilter.html)|(typ platí pouze v případě, že jsou k dispozici možnosti)  |Normalizuje německé znaky podle heuristiky [german2 algoritmu sněhové koule](https://snowballstem.org/algorithms/german2/stemmer.html) .|  
|[hindi_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/hi/HindiNormalizationFilter.html)|(typ platí pouze v případě, že jsou k dispozici možnosti)  |Normalizuje text v hindštině, aby odstranil některé rozdíly v pravopisných variantách. |  
|[indic_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/in/IndicNormalizationFilter.html)|IndicNormalizationTokenFilter|Normalizuje reprezentaci textu unicode v indických jazycích.
|[Udržet](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeepWordFilter.html)|Filtr KeepTokenFilter|Filtr tokenů, který uchovává pouze tokeny s textem obsaženým v zadaném seznamu slov.<br /><br /> **Možnosti**<br /><br /> keepWords (typ: pole řetězců) - Seznam slov, která je třeba zachovat. Výchozí hodnota je prázdný seznam. Povinná hodnota.<br /><br /> keepWordsCase (typ: bool) - Pokud je to pravda, malá písmena všechna slova jako první. Výchozí hodnotou je hodnota false.|  
|[keyword_marker](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeywordMarkerFilter.html)|Filtr Značka k neměnné|Označí termíny jako klíčová slova.<br /><br /> **Možnosti**<br /><br /> klíčová slova (typ: pole řetězců) - Seznam slov k označení jako klíčová slova. Výchozí hodnota je prázdný seznam. Povinná hodnota.<br /><br /> ignoreCase (typ: bool) - Pokud true, malá písmena všechna slova jako první. Výchozí hodnotou je hodnota false.|  
|[keyword_repeat](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeywordRepeatFilter.html)|(typ platí pouze v případě, že jsou k dispozici možnosti)  |Vyzařuje každý příchozí token dvakrát jako klíčové slovo a jednou jako jiné klíčové slovo. |  
|[kstem](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/en/KStemFilter.html)|(typ platí pouze v případě, že jsou k dispozici možnosti)  |Vysoce výkonný kstem filtr pro angličtinu. |  
|[Délka](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/LengthFilter.html)|LengthTokenFilter|Odstraní slova, která jsou příliš dlouhá nebo příliš krátká.<br /><br /> **Možnosti**<br /><br /> min (typ: int) - Minimální počet. Výchozí hodnota: 0, maximálně: 300.<br /><br /> max (typ: int) - Maximální počet. Výchozí hodnota: 300, maximálně: 300.|  
|[Limit](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/LimitTokenCountFilter.html)|Filtr Microsoft.Azure.Search.LimitTokenFilter|Omezuje počet tokenů při indexování.<br /><br /> **Možnosti**<br /><br /> maxTokenCount (typ: int) - Maximální počet tokenů k výrobě. Výchozí hodnota je 1.<br /><br /> consumeAllTokens (typ: bool) - Zda všechny tokeny ze vstupu musí být spotřebovány i v případě, že maxTokenCount je dosaženo. Výchozí hodnotou je hodnota false.|  
|[Malá písmena](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)|(typ platí pouze v případě, že jsou k dispozici možnosti)  |Normalizuje text tokenu na malá písmena. |  
|[nGram_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenFilter.html)|NGramTokenFilterV2|Generuje n-gramů dané velikosti(ů).<br /><br /> **Možnosti**<br /><br /> minGram (typ: int) - Výchozí: 1, maximum: 300.<br /><br /> maxGram (typ: int) - Výchozí: 2, maximálně 300. Musí být větší než minGram.|  
|[pattern_capture](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternCaptureGroupTokenFilter.html)|VzorCaptureTokenFilter|Používá java reexes k vyzařování více tokenů, jeden pro každou skupinu zachycení v jednom nebo více vzorcích.<br /><br /> **Možnosti**<br /><br /> vzory (typ: pole řetězců) - Seznam vzorů, které odpovídají každému tokenu. Povinná hodnota.<br /><br /> preserveOriginal (typ: bool) - Nastavit na true vrátit původní token, i když jeden ze vzorků odpovídá, výchozí: true |  
|[pattern_replace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternReplaceFilter.html)|VzorReplaceTokenFilter|Filtr tokenu, který použije vzorek pro každý token v datovém proudu a nahradí výskyty shody zadaným náhradním řetězcem.<br /><br /> **Možnosti**<br /><br /> vzorek (typ: řetězec) - Povinné.<br /><br /> nahrazení (typ: řetězec) - Povinné.|  
|[persian_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/fa/PersianNormalizationFilter.html)|(typ platí pouze v případě, že jsou k dispozici možnosti) |Platí normalizace pro perštinu. |  
|[Fonetické](https://lucene.apache.org/core/6_6_1/analyzers-phonetic/org/apache/lucene/analysis/phonetic/package-tree.html)|Fonetickátokenfiltr|Vytvořte tokeny pro foetické shody.<br /><br /> **Možnosti**<br /><br /> kodér (typ: řetězec) - Fonetický kodér, který chcete použít. Povolené hodnoty zahrnují: "metafon", "doubleMetaphone", "soundex", "refinedSoundex", "caverphone1", "caverphone2", "kolínská", "nysiis", "koelnerPhonetik", "haasePhonetik", "beiderMorse". Výchozí: "metafon". Výchozí je metafon.<br /><br /> Další informace naleznete [v tématu kodér.](https://commons.apache.org/proper/commons-codec/archives/1.10/apidocs/org/apache/commons/codec/language/package-frame.html)<br /><br /> nahradit (typ: bool) - True pokud kódované tokeny by měly nahradit původní tokeny, false, pokud by měly být přidány jako synonyma. Výchozí hodnota je true.|  
|[porter_stem](https://lucene.apache.org/core/6_6_1/analyzers-common/org/tartarus/snowball/ext/PorterStemmer.html)|(typ platí pouze v případě, že jsou k dispozici možnosti)  |Transformuje datový proud tokenu podle [algoritmu vyplývajícího z Porter](https://tartarus.org/~martin/PorterStemmer/). |  
|[Reverzní](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/reverse/ReverseStringFilter.html)|(typ platí pouze v případě, že jsou k dispozici možnosti)  |Obrátí řetězec tokenu. |  
|[scandinavian_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianNormalizationFilter.html)|(typ platí pouze v případě, že jsou k dispozici možnosti)  |Normalizuje použití zaměnitelných skandinávských znaků. |  
|[scandinavian_folding](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianFoldingFilter.html)|(typ platí pouze v případě, že jsou k dispozici možnosti)  |Skládá skandinávské znaky åÅäæÄÆ->a öÖØØ->o. To také diskriminuje použití dvojitých samohlásek aa, ae, ao, oe a oo, takže jen první. |  
|[Šindel](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/shingle/ShingleFilter.html)|Filtr ShingleToken|Vytvoří kombinace tokenů jako jeden token.<br /><br /> **Možnosti**<br /><br /> maxShingleSize (typ: int) - Výchozí hodnota je 2.<br /><br /> minShingleSize (typ: int) - Výchozí hodnota je 2.<br /><br /> outputUnigrams (typ: bool) - pokud je true, výstupní proud obsahuje vstupní tokeny (unigramy) stejně jako šindele. Výchozí hodnota je true.<br /><br /> outputUnigramsIfNoShingles (typ: bool) - Pokud true, přepsat chování outputUnigrams==false pro ty časy, kdy nejsou k dispozici žádné šindele. Výchozí hodnotou je hodnota false.<br /><br /> tokenSeparator (typ: řetězec) - Řetězec, který se má použít při spojování sousedních tokenů, aby vytvořil šindel. Výchozí hodnota je " ".<br /><br /> filterToken (typ: řetězec) - řetězec vložit pro každou pozici, na které neexistuje žádný token. Výchozí hodnota je "_".|  
|[Sněhulka](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/snowball/SnowballFilter.html)|SnowballTokenFilter|Filtr sněhových koulí.<br /><br /> **Možnosti**<br /><br /> jazyk (typ: řetězec) - Povolené hodnoty zahrnují: "armenian", "basque", "katalánština", "dánština", "holandský", "angličtina", "finština", "francouzština", "němčina", "german2", "maďarština", "italština", "kp", "lovins", "norština", "porter", "portugalština", "rumunský", " ruština", "španělština", "švédština", "turečtina"|  
|[sorani_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ckb/SoraniNormalizationFilter.html)|SoraniNormalizationTokenFilter|Normalizuje reprezentaci Unicode textu Sorani.<br /><br /> **Možnosti**<br /><br /> Žádné.|  
|Stemmer|Filtr StemmerToken|Filtr vyplývající pro jazyk.<br /><br /> **Možnosti**<br /><br /> jazyk (typ: řetězec) - Povolené hodnoty zahrnují: <br /> -   ["arabština"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ar/ArabicStemmer.html)<br />-   ["arménský"](https://snowballstem.org/algorithms/armenian/stemmer.html)<br />-   ["baskické"](https://snowballstem.org/algorithms/basque/stemmer.html)<br />-   ["brazilský"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/br/BrazilianStemmer.html)<br />- "bulharský"<br />-   ["Katalánština"](https://snowballstem.org/algorithms/catalan/stemmer.html)<br />-   ["česká"](https://portal.acm.org/citation.cfm?id=1598600)<br />-   ["Dánština"](https://snowballstem.org/algorithms/danish/stemmer.html)<br />-   ["holandský"](https://snowballstem.org/algorithms/dutch/stemmer.html)<br />-   ["HolandskýKp"](https://snowballstem.org/algorithms/kraaij_pohlmann/stemmer.html)<br />-   ["anglicky"](https://snowballstem.org/algorithms/porter/stemmer.html)<br />-   ["světle anglicky"](https://ciir.cs.umass.edu/pubfiles/ir-35.pdf)<br />-   ["minimální angličtina"](https://www.researchgate.net/publication/220433848_How_effective_is_suffixing)<br />-   ["přivlastňovací angličtina"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/en/EnglishPossessiveFilter.html)<br />-   ["Porter2"](https://snowballstem.org/algorithms/english/stemmer.html)<br />-   ["lovins"](https://snowballstem.org/algorithms/lovins/stemmer.html)<br />-   ["finština"](https://snowballstem.org/algorithms/finnish/stemmer.html)<br />- "světle finská"<br />-   ["francouzsky"](https://snowballstem.org/algorithms/french/stemmer.html)<br />-   ["světle francouzsky"](https://dl.acm.org/citation.cfm?id=1141523)<br />-   ["minimální francouzština"](https://dl.acm.org/citation.cfm?id=318984)<br />- "galicijská"<br />- "minimalgalicijština"<br />-   ["německy"](https://snowballstem.org/algorithms/german/stemmer.html)<br />-   ["německy2"](https://snowballstem.org/algorithms/german2/stemmer.html)<br />-   ["světle německy"](https://dl.acm.org/citation.cfm?id=1141523)<br />- "minimálníněmčina"<br />-   ["Řečtina"](https://sais.se/mthprize/2007/ntais2007.pdf)<br />- "hindština"<br />-   ["maďarština"](https://snowballstem.org/algorithms/hungarian/stemmer.html)<br />-   ["světle maďarští"](https://dl.acm.org/citation.cfm?id=1141523&dl=ACM&coll=DL&CFID=179095584&CFTOKEN=80067181)<br />-   ["indonéský"](https://www.illc.uva.nl/Publications/ResearchReports/MoL-2003-02.text.pdf)<br />-   ["irský"](https://snowballstem.org/otherapps/oregan/)<br />-   ["Italsky"](https://snowballstem.org/algorithms/italian/stemmer.html)<br />-   ["světloitalské"](https://www.ercim.eu/publication/ws-proceedings/CLEF2/savoy.pdf)<br />-   ["sorani"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ckb/SoraniStemmer.html)<br />-   ["Lotyš"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/lv/LatvianStemmer.html)<br />-   ["Norština"](https://snowballstem.org/algorithms/norwegian/stemmer.html)<br />-   ["lightNorwegian"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/no/NorwegianLightStemmer.html)<br />-   ["minimální norština"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/no/NorwegianMinimalStemmer.html)<br />-   ["lightNynorsk"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/no/NorwegianLightStemmer.html)<br />-   ["minimalNynorsk"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/no/NorwegianMinimalStemmer.html)<br />-   ["Portugalský"](https://snowballstem.org/algorithms/portuguese/stemmer.html)<br />-   ["světle portugalský"](https://dl.acm.org/citation.cfm?id=1141523&dl=ACM&coll=DL&CFID=179095584&CFTOKEN=80067181)<br />-   ["minimální portugalština"](https://www.inf.ufrgs.br/~buriol/papers/Orengo_CLEF07.pdf)<br />-   ["PortugalskýRslp"](https://www.inf.ufrgs.br//~viviane/rslp/index.htm)<br />-   ["Rumunský"](https://snowballstem.org/otherapps/romanian/)<br />-   ["rusky"](https://snowballstem.org/algorithms/russian/stemmer.html)<br />-   ["lightRussian"](https://doc.rero.ch/lm.php?url=1000%2C43%2C4%2C20091209094227-CA%2FDolamic_Ljiljana_-_Indexing_and_Searching_Strategies_for_the_Russian_20091209.pdf)<br />-   ["Španělsky"](https://snowballstem.org/algorithms/spanish/stemmer.html)<br />-   ["světle španělsky"](https://www.ercim.eu/publication/ws-proceedings/CLEF2/savoy.pdf)<br />-   ["Švédština"](https://snowballstem.org/algorithms/swedish/stemmer.html)<br />- "lightSwedish"<br />-   ["turečtina"](https://snowballstem.org/algorithms/turkish/stemmer.html)|  
|[stemmer_override](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/StemmerOverrideFilter.html)|StemmerOverrideTokenFilter|Všechny termíny se slem slovníku jsou označeny jako klíčová slova, která brání vyplývající z řetězce. Musí být umístěnpřed všechny vyplývající filtry.<br /><br /> **Možnosti**<br /><br /> pravidla (typ: pole řetězců) - Vyplývající pravidla v následujícím formátu "slovo => stem" například "ran => run". Výchozí hodnota je prázdný seznam.  Povinná hodnota.|  
|[stopwords](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/StopFilter.html)|StopwordsTokenFilter|Odebere stop slova z datového proudu tokenu. Ve výchozím nastavení filtr používá předdefinovaný seznam stop word pro angličtinu.<br /><br /> **Možnosti**<br /><br /> stopwords (typ: string array) - Seznam stopwords. Nelze zadat, pokud je zadán stopwordsList.<br /><br /> stopwordsList (typ: řetězec) - Předdefinovaný seznam stopwords. Nelze zadat, pokud je zadána stopwords. Povolené hodnoty zahrnují:"arabština", "arménský", "baskický", "brazilský", "bulharský", "katalánština", "čeština", "dánština", "holandský", "angličtina", "finština", "francouzština", "galicijština", "němčina", "řečtina", "hindština", "maďarština", "indonéština", "irský", "italský", "lotyšský", "lotyšský", "lotyšský", "lotyšský", "lotyšský", ", "norština", "perština", "portugalština", "rumunský", "ruský", "sorani", "španělština", "švédština", "thajština", "turečtina", výchozí: "angličtina". Nelze zadat, pokud je zadána stopwords. <br /><br /> ignoreCase (typ: bool) - Pokud true, všechna slova jsou malá písmena jako první. Výchozí hodnotou je hodnota false.<br /><br /> removeTrailing (typ: bool) - Pokud true, ignorovat poslední hledaný termín, pokud je to stop slovo. Výchozí hodnota je true.
|[Synonymum](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/synonym/SynonymFilter.html)|Synonymtokenfilter|Odpovídá jedno slově nebo více slov synonyma v datovém proudu tokenu.<br /><br /> **Možnosti**<br /><br /> synonyma (typ: pole řetězců) - Povinné. Seznam synonym v jednom z následujících dvou formátů:<br /><br /> -neuvěřitelné, neuvěřitelné, báječné => úžasné - všechny termíny na levé straně => symbol jsou nahrazeny všemi podmínkami na pravé straně.<br /><br /> -neuvěřitelné, neuvěřitelné, báječné, úžasné - čárka-oddělený seznam ekvivalentních slov. Chcete-li změnit způsob interpretace tohoto seznamu, nastavte možnost rozbalení.<br /><br /> ignoreCase (typ: bool) - Case-folds vstup pro odpovídající. Výchozí hodnotou je hodnota false.<br /><br /> rozbalte (typ: bool) - Pokud true, všechna slova v seznamu synonym (pokud => notace se nepoužívá) mapovat na sebe navzájem. <br />Následující seznam: neuvěřitelný, neuvěřitelný, báječný, úžasný je ekvivalentní: neuvěřitelné, neuvěřitelné, báječné, úžasné => neuvěřitelné, neuvěřitelné, báječné, úžasné<br /><br />- Pokud false, následující seznam: neuvěřitelné, neuvěřitelné, báječné, úžasné jsou ekvivalentní: neuvěřitelné, neuvěřitelné, báječné, úžasné => neuvěřitelné.|  
|[Trim](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/TrimFilter.html)|(typ platí pouze v případě, že jsou k dispozici možnosti)  |Ořízne úvodní a koncové mezery z tokenů. |  
|[Zkrátit](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/TruncateTokenFilter.html)|TruncateTokenFilter|Zkrátí podmínky na určitou délku.<br /><br /> **Možnosti**<br /><br /> délka (typ: int) - Výchozí: 300, maximum: 300. Povinná hodnota.|  
|[Jedinečný](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/RemoveDuplicatesTokenFilter.html)|Filtr UniqueTokenFilter|Filtruje tokeny se stejným textem jako předchozí token.<br /><br /> **Možnosti**<br /><br /> onlyOnSamePosition (typ: bool) - Pokud je nastaveno, odeberte duplikáty pouze na stejné pozici. Výchozí hodnota je true.|  
|[Velká](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/UpperCaseFilter.html)|(typ platí pouze v případě, že jsou k dispozici možnosti)  |Normalizuje text tokenu na velká písmena. |  
|[word_delimiter](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/WordDelimiterFilter.html)|Filtr WordDelimiterTokenFilter|Rozdělí slova do podslov a provede volitelné transformace ve skupinách podslov.<br /><br /> **Možnosti**<br /><br /> generateWordParts (typ: bool) - Způsobí, že části slova, které mají být generovány, například "AzureSearch" se změní na "Azure" "Search". Výchozí hodnota je true.<br /><br /> generateNumberParts (typ: bool) - Způsobí, že číslo subwords, které mají být generovány. Výchozí hodnota je true.<br /><br /> catenateWords (typ: bool) - Způsobí, že maximální počet částí slova, které mají být katenován, například "Azure-Search" se změní na "AzureSearch". Výchozí hodnotou je hodnota false.<br /><br /> catenateNumbers (typ: bool) - Způsobí, že maximální počet částí, které mají být katenovány, například "1-2" se změní na "12". Výchozí hodnotou je hodnota false.<br /><br /> catenateAll (typ: bool) - Způsobí, že všechny podvětšené části, které mají být katenovány, například "Azure-Search-1" se změní na "AzureSearch1". Výchozí hodnotou je hodnota false.<br /><br /> splitOnCaseChange (typ: bool) - Pokud true, rozdělí slova na caseChange, například "AzureSearch" se změní na "Azure" "Search". Výchozí hodnota je true.<br /><br /> preserveOriginal - Způsobí, že původní slova, která mají být zachována a přidána do seznamu podslov. Výchozí hodnotou je hodnota false.<br /><br /> splitOnNumerics (typ: bool) - Pokud true, rozdělí na čísla, například "Azure1Search" se změní na "Azure" "1" "Search". Výchozí hodnota je true.<br /><br /> stemEnglishPossessive (typ: bool) - Způsobí, že koncové "s" má být odstraněn pro každé podslovo. Výchozí hodnota je true.<br /><br /> protectedWords (typ: řetězec pole) - Tokeny chránit před vymezením. Výchozí hodnota je prázdný seznam.|  

 <sup>1</sup> Typy filtru tokenů jsou vždy předponou v kódu s "#Microsoft.Azure.Search" tak, aby "ArabicNormalizationTokenFilter" by ve skutečnosti být zadán jako "#Microsoft.Azure.Search.ArabicNormalizationTokenFilter".  Odebrali jsme předponu, abychom zmenšili šířku tabulky, ale nezapomeňte ji zahrnout do kódu.  


## <a name="see-also"></a>Viz také  
 [Azure Cognitive Search REST API](https://docs.microsoft.com/rest/api/searchservice/)   
 [Analyzátory v Azure Cognitive Search > příklady](search-analyzers.md#examples)    
 [Vytvoření indexu &#40;rozhraní REST API pro kognitivní vyhledávání Azure&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)  
