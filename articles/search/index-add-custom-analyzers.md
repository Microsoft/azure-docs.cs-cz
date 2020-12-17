---
title: Přidat vlastní analyzátory do polí řetězců
titleSuffix: Azure Cognitive Search
description: Nakonfigurujte textové tokenizátory musíte nejdřív a filtry znaků používané v Azure Kognitivní hledání fulltextových vyhledávacích dotazech.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/05/2020
ms.openlocfilehash: fef73a9b98fef40aaceeacca43836d4b2f3c5de0
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2020
ms.locfileid: "97630203"
---
# <a name="add-custom-analyzers-to-string-fields-in-an-azure-cognitive-search-index"></a>Přidání vlastních analyzátorů do polí řetězců v indexu služby Azure Kognitivní hledání

*Vlastní analyzátor* je konkrétní typ [analyzátoru textu](search-analyzers.md) , který se skládá z uživatelsky definované kombinace stávajících provádějících tokenizaci a volitelných filtrů. Kombinací tokenizátory musíte nejdřív a filtrů novými způsoby můžete přizpůsobit zpracování textu v rámci vyhledávacího modulu, abyste dosáhli konkrétních výsledků. Můžete například vytvořit vlastní analyzátor s *filtrem znaků* pro odebrání značek HTML před tím, než budou textové vstupy vyraženy.

 Můžete definovat několik vlastních analyzátorů, které budou měnit kombinaci filtrů, ale každé pole může použít pouze jeden analyzátor pro analýzu indexů a jeden pro analýzu vyhledávání. Ilustraci toho, co vypadá jako analyzátor zákazníka, najdete v tématu [vlastní příklad analyzátoru](search-analyzers.md#Custom-analyzer-example).

## <a name="overview"></a>Přehled

 Role [fulltextového vyhledávacího modulu](search-lucene-query-architecture.md)v jednoduchých případech je zpracování a ukládání dokumentů způsobem, který umožňuje efektivní dotazování a načítání. Na vysoké úrovni se vše rozbalí k extrakci důležitých slov z dokumentů, jejich vložení do indexu a následnému použití indexu k vyhledání dokumentů, které odpovídají slovům daného dotazu. Proces extrakce slov z dokumentů a vyhledávacích dotazů se nazývá *lexikální analýza*. Komponenty, které provádějí lexikální analýzy, se nazývají *analyzátory*.

 V Azure Kognitivní hledání můžete vybrat ze sady předdefinovaných nezávislá analyzátorů v tabulce [analyzátory](#AnalyzerTable) nebo analyzátorů specifických pro jednotlivé jazyky, které jsou uvedené v části [analyzátory jazyka &#40;službě Azure kognitivní hledání REST API&#41;](index-add-language-analyzers.md). Máte také možnost definovat vlastní analyzátory.  

 Vlastní analyzátor umožňuje převzít kontrolu nad procesem převodu textu na indexovaelné a prohledávatelný tokeny. Je to uživatelsky definovaná konfigurace, která se skládá z jednoho předdefinovaného provádějících tokenizaci, jednoho nebo více filtrů tokenů a jednoho nebo více filtrů znaků. Provádějících tokenizaci zodpovídá za rozdělení textu na tokeny a filtry tokenů pro úpravu tokenů emitovaných provádějících tokenizaci. Filtry znaků jsou aplikovány na pro přípravu vstupního textu před jeho zpracováním pomocí provádějících tokenizaci. Například znakový filtr může nahradit určité znaky nebo symboly.

 K oblíbeným scénářům povoleným vlastními analyzátory patří:  

- Fonetické vyhledávání. Přidejte fonetický filtr, který umožní hledání na základě zvukového slova, nikoli způsobu jeho pravopisu.  

- Zakáže lexikální analýzu. Pomocí analyzátoru klíčového slova můžete vytvořit hledaná pole, která nejsou analyzovaná.  

- Rychlé hledání předpony a přípon Přidejte filtr tokenů pro Edge N-gram k indexům předpon slov, aby se povolila rychlá shoda předpony. Zkombinujete ho pomocí filtru reverzních tokenů, aby se shodoval s příponou.  

- Vlastní tokenizace Například můžete použít prázdné znaky provádějících tokenizaci k rozdělení vět na tokeny pomocí prázdného znaku jako oddělovače.  

- Skládání ASCII. Přidáním standardního filtru skládání ASCII můžete normalizovat diakritická znaménka, jako je ö nebo ê, ve hledaných výrazech.  

  Tato stránka poskytuje seznam podporovaných analyzátorů, tokenizátory musíte nejdřív, filtrů tokenů a znakových filtrů. Můžete také najít popis změn definice indexu s příkladem použití. Další informace o základní technologii, která se využívá v implementaci Azure Kognitivní hledání, najdete v tématu [Přehled balíčků analýz (Lucene)](https://lucene.apache.org/core/6_0_0/core/org/apache/lucene/codecs/lucene60/package-summary.html). Příklady konfigurací analyzátoru najdete v tématu [Přidání analyzátorů do Azure kognitivní hledání](search-analyzers.md#examples).

## <a name="validation-rules"></a>Ověřovací pravidla  
 Názvy analyzátorů, tokenizátory musíte nejdřív, filtrů tokenů a filtrů znaků musí být jedinečné a nesmí být stejné jako jakékoli předdefinované analyzátory, tokenizátory musíte nejdřív, filtry tokenů nebo filtry znaků. Přečtěte si [odkaz na vlastnost](#PropertyReference) pro názvy, které se už používají.

## <a name="create-custom-analyzers"></a>Vytvořit vlastní analyzátory
 Vlastní analyzátory můžete definovat při vytváření indexu. V této části je popsána syntaxe pro určení vlastního analyzátoru. Můžete se také seznámit se syntaxí, a to kontrolou ukázkových definic v části [Přidání analyzátorů v Azure kognitivní hledání](search-analyzers.md#examples).  

 Definice analyzátoru zahrnuje název, typ, jeden nebo více filtrů znaků, maximálně jeden provádějících tokenizaci a jeden nebo více filtrů tokenů pro zpracování po pořizování tokenů. Char filers se před tokenizace aplikuje. Filtry tokenů a filtry znaků jsou aplikovány zleva doprava.

 `tokenizer_name`Je název provádějících tokenizaci, `token_filter_name_1` a `token_filter_name_2` jsou názvy filtrů tokenů a `char_filter_name_1` a `char_filter_name_2` jsou názvy filtrů znaků (pro platné hodnoty viz tabulka [tokenizátory musíte nejdřív](#Tokenizers), [filtry tokenů](#TokenFilters) a filtry znaků).

Definice analyzátoru je součástí většího indexu. Informace o zbývajících částech indexu najdete v tématu [Vytvoření rozhraní API pro index](/rest/api/searchservice/create-index) .

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
>  Vlastní analyzátory, které vytvoříte, nejsou ve Azure Portal zveřejněny. Jediným způsobem, jak přidat vlastní analyzátor, je kód, který při definování indexu volá rozhraní API.  

 V rámci definice indexu můžete umístit tuto část kamkoli do těla žádosti o vytvoření indexu, ale obvykle na konci:  

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

Definice pro filtry znaků, tokenizátory musíte nejdřív a filtry tokenů jsou přidány do indexu pouze v případě, že nastavujete vlastní možnosti. Pokud chcete použít existující filtr nebo provádějících tokenizaci tak, jak jsou, zadejte ho podle názvu v definici analyzátoru.

<a name="Testing custom analyzers"></a>

## <a name="test-custom-analyzers"></a>Testování vlastních analyzátorů

Můžete použít **operaci analyzátoru testů** v [REST API](/rest/api/searchservice/test-analyzer) k zobrazení, jak analyzátor přerušuje daný text na tokeny.

**Žádost**
```
  POST https://[search service name].search.windows.net/indexes/[index name]/analyze?api-version=[api-version]
  Content-Type: application/json
    api-key: [admin key]

  {
     "analyzer":"my_analyzer",
     "text": "Vis-à-vis means Opposite"
  }
```
**Response** (Odpověď)
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

Jakmile je definován analyzátor, provádějících tokenizaci, filtr tokenu nebo filtr znaků, nelze jej změnit. Nové lze přidat do existujícího indexu pouze v případě, že `allowIndexDowntime` je příznak nastaven na hodnotu true v žádosti o aktualizaci indexu:

```
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=[api-version]&allowIndexDowntime=true
```

Tato operace převezme váš index po dobu nejméně pár sekund do offline režimu, což způsobí, že vaše požadavky na indexování a dotazy selžou. Dostupnost a zápis indexu může být po několik minut od aktualizace indexu nebo delší pro hodně velkých indexů narušeno, ale tyto účinky jsou dočasné a mohou se na jejich základě vyřešit sami.

 <a name="ReferenceIndexAttributes"></a>

## <a name="analyzer-reference"></a>Referenční informace k analyzátoru

V následujících tabulkách jsou uvedeny vlastnosti konfigurace pro analyzátory, tokenizátory musíte nejdřív, filtry tokenů a část filtru znaků definice indexu. Struktura analyzátoru, provádějících tokenizaci nebo filtru ve vašem indexu se skládá z těchto atributů. Informace o přiřazení hodnot naleznete v [odkazu na vlastnost](#PropertyReference).

### <a name="analyzers"></a>Analyzátory

V případě analyzátorů se atributy indexu liší v závislosti na tom, zda používáte předdefinované nebo vlastní analyzátory.

#### <a name="predefined-analyzers"></a>Předdefinované analyzátory

| Typ | Popis |
| ---- | ----------- |  
|Název|Musí obsahovat jenom písmena, číslice, mezery, pomlčky a podtržítka, může začínat a končit jenom alfanumerickými znaky a je omezený na 128 znaků.|  
|Typ|Typ analyzátoru ze seznamu podporovaných analyzátorů. Podívejte se do sloupce **analyzer_type** v níže uvedené tabulce [analyzátorů](#AnalyzerTable) .|  
|Možnosti|Musí se jednat o platné možnosti předdefinovaného analyzátoru uvedeného v tabulce [analyzátory](#AnalyzerTable) níže.|  

#### <a name="custom-analyzers"></a>Vlastní analyzátory

| Typ | Popis |
| ---- | ----------- |  
|Název|Musí obsahovat jenom písmena, číslice, mezery, pomlčky a podtržítka, může začínat a končit jenom alfanumerickými znaky a je omezený na 128 znaků.|  
|Typ|Musí být "#Microsoft. Azure. Search. CustomAnalyzer".|  
|CharFilters|Nastavte buď na jeden z předdefinovaných filtrů znaků uvedených v tabulce [filtrů znaků](#char-filters-reference) , nebo na vlastní filtr znaků zadaný v definici indexu.|  
|Provádějících tokenizaci|Povinná hodnota. Nastavte buď na jednu z předdefinovaných tokenizátory musíte nejdřív uvedených v tabulce [tokenizátory musíte nejdřív](#Tokenizers) , nebo na vlastní provádějících tokenizaci zadané v definici indexu.|  
|TokenFilters|Nastavte buď na jeden z předdefinovaných filtrů tokenů uvedených v tabulce [filtrů tokenů](#TokenFilters) , nebo na vlastní filtr tokenu zadaný v definici indexu.|  

> [!NOTE]
> Je nutné, abyste vlastní analyzátor nakonfigurovali tak, aby nevytvořil tokeny delší než 300 znaků. U dokumentů s takovými tokeny se indexování nezdařila. Pokud je chcete oříznout nebo je ignorovat, použijte **TruncateTokenFilter** a **LengthTokenFilter** .  Podívejte se na [**filtry tokenů**](#TokenFilters) pro referenci.

<a name="CharFilter"></a>

### <a name="char-filters"></a>Filtry znaků

 Filtr znaků slouží k přípravě vstupního textu před jeho zpracováním pomocí provádějících tokenizaci. Například může nahradit určité znaky nebo symboly. Ve vlastním analyzátoru můžete mít více filtrů znaků. Filtry znaků jsou spouštěny v pořadí, ve kterém jsou uvedeny.  

| Typ | Popis |
| ---- | ----------- | 
|Název|Musí obsahovat jenom písmena, číslice, mezery, pomlčky a podtržítka, může začínat a končit jenom alfanumerickými znaky a je omezený na 128 znaků.|  
|Typ|Typ filtru znaků ze seznamu podporovaných filtrů znaků. Viz **char_filter_type** sloupec v tabulce [filtry znaků](#char-filters-reference) níže.|  
|Možnosti|Musí být platné možnosti pro daný typ [filtru znaků](#char-filters-reference) .|  

### <a name="tokenizers"></a>Tokenizátory musíte nejdřív

 Provádějících tokenizaci rozdělí souvislý text na sekvenci tokenů, jako je například porušení věty na slova.  

 Můžete zadat přesně jeden provádějících tokenizaci na vlastní analyzátor. Pokud potřebujete více než jeden provádějících tokenizaci, můžete vytvořit více vlastních analyzátorů a přiřadit je podle pole v rámci schématu indexu.  
Vlastní analyzátor může použít předdefinované provádějících tokenizaci buď s výchozími, nebo s přizpůsobenými možnostmi.  

| Typ | Popis |
| ---- | ----------- | 
|Název|Musí obsahovat jenom písmena, číslice, mezery, pomlčky a podtržítka, může začínat a končit jenom alfanumerickými znaky a je omezený na 128 znaků.|  
|Typ|Provádějících tokenizaci název ze seznamu podporovaných tokenizátory musíte nejdřív. Viz **tokenizer_type** sloupec v tabulce [tokenizátory musíte nejdřív](#Tokenizers) níže.|  
|Možnosti|Musí se jednat o platné možnosti daného typu provádějících tokenizaci uvedeného v tabulce [tokenizátory musíte nejdřív](#Tokenizers) .|  

### <a name="token-filters"></a>Filtry tokenů

 Filtr tokenu slouží k odfiltrování nebo úpravě tokenů generovaných objektem provádějících tokenizaci. Můžete například zadat filtr malých písmen, který převede všechny znaky na malá písmena.   
Ve vlastním analyzátoru můžete mít více filtrů tokenů. Filtry tokenů jsou spouštěny v pořadí, ve kterém jsou uvedeny.  

| Typ | Popis |
| ---- | ----------- |  
|Název|Musí obsahovat jenom písmena, číslice, mezery, pomlčky a podtržítka, může začínat a končit jenom alfanumerickými znaky a je omezený na 128 znaků.|  
|Typ|Název filtru tokenů ze seznamu podporovaných filtrů tokenů. Viz sloupec **token_filter_type** v následující tabulce [filtrů tokenů](#TokenFilters) .|  
|Možnosti|Musí se jednat o [filtry tokenů](#TokenFilters) daného typu filtru tokenu.|  

<a name="PropertyReference"></a>  

## <a name="property-reference"></a>Odkaz na vlastnost

Tato část poskytuje platné hodnoty pro atributy zadané v definici vlastního analyzátoru, provádějících tokenizaci, filtru znaků nebo filtru tokenů ve vašem indexu. Analyzátory, tokenizátory musíte nejdřív a filtry implementované pomocí Apache Lucene mají odkazy na dokumentaci k rozhraní API pro Lucene.

<a name="AnalyzerTable"></a>

###  <a name="predefined-analyzers-reference"></a>Odkazy na předdefinované analyzátory

|**analyzer_name**|**analyzer_type**  <sup>1</sup>|**Popis a možnosti**|  
|-|-|-|  
|[klíčové slovo](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html)| (typ platí pouze v případě, že jsou k dispozici možnosti) |Zachází s celým obsahem pole jako s jedním tokenem. To je užitečné pro data, jako jsou kódy PSČ, ID a některé názvy produktů.|  
|[vzorku](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/PatternAnalyzer.html)|PatternAnalyzer|Flexibilní oddělení textu do výrazů pomocí vzoru regulárního výrazu.<br /><br /> **Možnosti**<br /><br /> malá písmena (typ: bool) – určuje, zda jsou výrazy v malých písmenech. Výchozí hodnota je true.<br /><br /> [vzor](https://docs.oracle.com/javase/7/docs/api/java/util/regex/Pattern.html?is-external=true) (typ: String) – vzor regulárního výrazu, který odpovídá oddělovačům tokenů. Výchozí hodnota je `\W+` , která odpovídá znakům jiným než Word.<br /><br /> [Flags](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) (Type: String) – příznaky regulárního výrazu. Výchozí hodnota je prázdný řetězec. Povolené hodnoty: CANON_EQ, CASE_INSENSITIVE, komentáře, DOTALL, LITERÁLy, VÍCEŘÁDKOVé UNICODE_CASE, UNIX_LINES<br /><br /> stopslova (typ: pole řetězců) – seznam stopslova. Výchozí hodnota je prázdný seznam.|  
|[pouh](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/SimpleAnalyzer.html)|(typ platí pouze v případě, že jsou k dispozici možnosti) |Vydělí text bez písmen a převede je na malá písmena. |  
|[standardní](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) <br />(Označuje se také jako standardní. Lucene)|StandardAnalyzer|Nástroj pro standardní Lucene, který se skládá ze standardního filtru provádějících tokenizaci, malých písmen a filtru stop.<br /><br /> **Možnosti**<br /><br /> maxTokenLength (typ: int) – maximální délka tokenu. Výchozí hodnota je 255. Tokeny delší než maximální délka jsou rozděleny. Maximální délka tokenu, kterou lze použít, je 300 znaků.<br /><br /> stopslova (typ: pole řetězců) – seznam stopslova. Výchozí hodnota je prázdný seznam.|  
|standardasciifolding. Lucene|(typ platí pouze v případě, že jsou k dispozici možnosti) |Standard Analyzer s filtrem skládání ASCII. |  
|[Stop](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/StopAnalyzer.html)|StopAnalyzer|Vydělí text v nepísmenech, aplikuje na ně filtry pro malé a stopslovo tokeny.<br /><br /> **Možnosti**<br /><br /> stopslova (typ: pole řetězců) – seznam stopslova. Výchozím nastavením je předdefinovaný seznam pro angličtinu. |  
|[typy](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html)|(typ platí pouze v případě, že jsou k dispozici možnosti) |Analyzátor, který používá prázdné znaky provádějících tokenizaci. Tokeny, které jsou delší než 255 znaků, jsou rozděleny.|  

 <sup>1</sup> typy analyzátoru jsou vždycky pevně uvedené v kódu s "#Microsoft. Azure. Search" tak, že "PatternAnalyzer" by ve skutečnosti byly zadané jako "#Microsoft. Azure. Search. PatternAnalyzer". Odebrali jsme předponu pro zkrácení, ale ve vašem kódu se vyžaduje předpona. 
 
Analyzer_type je k dispozici pouze pro analyzátory, které lze přizpůsobit. Pokud nejsou k dispozici žádné možnosti, stejně jako v případě analyzátoru klíčového slova není k dispozici žádný přidružený #Microsoft. typ Azure. Search.


<a name="CharFilter"></a>

###  <a name="char-filters-reference"></a>Reference pro filtry znaků

V následující tabulce jsou filtry znaků implementované pomocí Apache Lucene propojeny s dokumentací k rozhraní API Lucene.

|**char_filter_name**|**char_filter_type** <sup>1</sup>|**Popis a možnosti**|  
|-|-|-|
|[html_strip](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/charfilter/HTMLStripCharFilter.html)|(typ platí pouze v případě, že jsou k dispozici možnosti)  |Filtr znaků, který se pokusí vydělit konstrukce jazyka HTML.|  
|[mapování](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/charfilter/MappingCharFilter.html)|MappingCharFilter|Filtr znaků, který aplikuje mapování definované s možností mapování Porovnávání je hladec (nejdelší porovnávání vzorů v daném bodu WINS). Nahrazení může být prázdný řetězec.<br /><br /> **Možnosti**<br /><br /> mapování (typ: pole řetězců) – seznam mapování v následujícím formátu: "A =>b" (všechny výskyty znaku "a" jsou nahrazeny znakem "b"). Povinná hodnota.|  
|[pattern_replace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternReplaceCharFilter.html)|PatternReplaceCharFilter|Filtr znaků, který nahradí znaky ve vstupním řetězci. Pomocí regulárního výrazu identifikuje sekvence znaků, které mají být zachovány, a vzor pro nahrazení, který identifikuje znaky, které mají být nahrazeny. Například input text = "AA BB AA BB", vzor = "(AA) \\ \s + (BB)" nahrazení = "$ 1 # $2", výsledek = "AA # BB AA # BB".<br /><br /> **Možnosti**<br /><br /> Pattern (typ: String) – povinný.<br /><br /> náhrada (typ: String) – povinné.|  

 <sup>1</sup> typy filtru znaků jsou vždy předpony v kódu s "#Microsoft. Azure. Search" tak, že "MappingCharFilter" by ve skutečnosti byly zadány jako "#Microsoft. Azure. Search. MappingCharFilter. Odebrali jsme předponu, aby se snížila šířka tabulky, ale nezapomeňte ji zahrnout do kódu. Všimněte si, že char_filter_type je k dispozici pouze pro filtry, které lze přizpůsobit. Pokud neexistují žádné možnosti, jako je například případ s html_strip, není k dispozici žádný přidružený #Microsoft. typ Azure. Search.

<a name="Tokenizers"></a>

###  <a name="tokenizers-reference"></a>Odkaz na tokenizátory musíte nejdřív

V následující tabulce jsou tokenizátory musíte nejdřív implementované pomocí Apache Lucene propojeny s dokumentací k rozhraní API pro Lucene.

|**tokenizer_name**|**tokenizer_type** <sup>1</sup>|**Popis a možnosti**|  
|-|-|-|  
|[klasický](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/standard/ClassicTokenizer.html)|ClassicTokenizer|Gramatika založená na provádějících tokenizaci, která je vhodná pro zpracování většiny dokumentů v evropském jazyce.<br /><br /> **Možnosti**<br /><br /> maxTokenLength (typ: int) – maximální délka tokenu. Výchozí: 255, maximum: 300. Tokeny delší než maximální délka jsou rozděleny.|  
|[edgeNGram](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html)|EdgeNGramTokenizer|Tokenizes vstup z okraje do n-gramů dané velikosti (y).<br /><br /> **Možnosti**<br /><br /> minGram (typ: int) – výchozí hodnota: 1, maximum: 300.<br /><br /> maxGram (typ: int) – výchozí: 2, maximum: 300. Musí být větší než minGram.<br /><br /> tokenChars (typ: pole řetězců) – třídy znaků, které mají být v tokenech uchovávány. Povolené hodnoty: <br />"Letter", "číslice", "prázdný", "interpunkce", "symbol". Výchozí hodnota je prázdné pole – zachová všechny znaky. |  
|[keyword_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html)|KeywordTokenizerV2|Vygeneruje celý vstup jako jeden token.<br /><br /> **Možnosti**<br /><br /> maxTokenLength (typ: int) – maximální délka tokenu. Výchozí: 256, maximum: 300. Tokeny delší než maximální délka jsou rozděleny.|  
|[jednotky](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LetterTokenizer.html)|(typ platí pouze v případě, že jsou k dispozici možnosti)  |Vydělí text bez písmen. Tokeny, které jsou delší než 255 znaků, jsou rozděleny.|  
|[malá](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseTokenizer.html)|(typ platí pouze v případě, že jsou k dispozici možnosti)  |Vydělí text bez písmen a převede je na malá písmena. Tokeny, které jsou delší než 255 znaků, jsou rozděleny.|  
| microsoft_language_tokenizer| MicrosoftLanguageTokenizer| Vydělí text pomocí pravidel specifických pro jazyk.<br /><br /> **Možnosti**<br /><br /> maxTokenLength (typ: int) – maximální délka tokenu, výchozí: 255, maximum: 300. Tokeny delší než maximální délka jsou rozděleny. Tokeny delší než 300 znaků jsou nejprve rozděleny na tokeny o délce 300 a následně jsou jednotlivé tokeny rozděleny na základě maxTokenLength sady.<br /><br />isSearchTokenizer (typ: bool) – nastavte na hodnotu true, pokud se použije jako vyhledávací provádějících tokenizaci, nastavte na hodnotu false, pokud se používá jako indexovací provádějících tokenizaci. <br /><br /> Language (typ: String) – jazyk, který se má použít, výchozí "Angličtina". Mezi povolené hodnoty patří:<br />bengálština, bulharština, Katalánština "," chineseSimplified "," chineseTraditional "," chorvatština "," Čeština "," Dánština "," Holandština "," Angličtina "," francouzština "," Němčina "," Řečtina "," gudžarátština "," Hindština "," Italština "Malajština", "malajalámština", "maráthština", "norwegianBokmaal", "Polština", "portugalština", "portugueseBrazilian", "paňdžábština", "Rumunština", "ruština", "serbianCyrillic", "serbianLatin", "slovinské", "španělština", "Švédština", "tamilština", "telugština", "Thajština", "ukrajinština", "urdština", "Vietnamština" |
| microsoft_language_stemming_tokenizer | MicrosoftLanguageStemmingTokenizer| Rozděluje text pomocí pravidel specifických pro jazyk a zkracuje slova na jejich základní formy.<br /><br /> **Možnosti**<br /><br />maxTokenLength (typ: int) – maximální délka tokenu, výchozí: 255, maximum: 300. Tokeny delší než maximální délka jsou rozděleny. Tokeny delší než 300 znaků jsou nejprve rozděleny na tokeny o délce 300 a následně jsou jednotlivé tokeny rozděleny na základě maxTokenLength sady.<br /><br /> isSearchTokenizer (typ: bool) – nastavte na hodnotu true, pokud se použije jako vyhledávací provádějících tokenizaci, nastavte na hodnotu false, pokud se používá jako indexovací provádějících tokenizaci.<br /><br /> Language (typ: String) – jazyk, který se má použít, výchozí "Angličtina". Mezi povolené hodnoty patří:<br />"Arabština", "bengálština", "Bulharština", "Katalánština", "chorvatština", "Čeština", "Dánská", "Holandština", "Angličtina", "finština", "francouzština", "Němčina", "Řečtina", "gudžarátština", "Hebrejština", "Hindština", "Maďarština", "Čeština", "gudžarátština", "Italština" Litevština "," Malajština "," malajalámština "," maráthština "," norwegianBokmaal "," Polština "," portugalština "," portugueseBrazilian "," paňdžábština "," Rumunština "," ruština "," serbianCyrillic "," serbianLatin "," Slovenština "," Slovinština "," španělština "," Švédština "," tamilština "," telugština "," Turečtina "," ukrajinština "," urdština " |
|[nGram](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenizer.html)|NGramTokenizer|Tokenizes vstup do n-gramů dané velikosti (y).<br /><br /> **Možnosti**<br /><br /> minGram (typ: int) – výchozí hodnota: 1, maximum: 300.<br /><br /> maxGram (typ: int) – výchozí: 2, maximum: 300. Musí být větší než minGram. <br /><br /> tokenChars (typ: pole řetězců) – třídy znaků, které mají být v tokenech uchovávány. Povolené hodnoty: "písmeno", "číslice", "prázdný", "interpunkce", "symbol". Výchozí hodnota je prázdné pole – zachová všechny znaky. |  
|[path_hierarchy_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/path/PathHierarchyTokenizer.html)|PathHierarchyTokenizerV2|Provádějících tokenizaci pro hierarchie jako cesty.<br /><br /> **Možnosti**<br /><br /> oddělovač (typ: String) – výchozí: '/.<br /><br /> nahrazení (typ: String) – Pokud je nastaveno, nahradí znak oddělovače. Výchozí hodnota je stejná jako hodnota oddělovače.<br /><br /> maxTokenLength (typ: int) – maximální délka tokenu. Výchozí: 300, maximum: 300. Cesty delší než maxTokenLength jsou ignorovány.<br /><br /> Reverse (Type: bool) – Pokud má hodnotu true, vygeneruje token v opačném pořadí. Výchozí hodnota: false.<br /><br /> Skip (Type: bool) – počáteční tokeny, které se mají přeskočit Výchozí hodnota je 0.|  
|[vzorku](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternTokenizer.html)|PatternTokenizer|Tento provádějících tokenizaci používá porovnávání vzorů regulárního výrazu pro vytváření jedinečných tokenů.<br /><br /> **Možnosti**<br /><br /> [vzor](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html) (typ: String) – vzor regulárního výrazu, který odpovídá oddělovačům tokenů. Výchozí hodnota je `\W+` , která odpovídá znakům jiným než Word. <br /><br /> [Flags](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) (Type: String) – příznaky regulárního výrazu. Výchozí hodnota je prázdný řetězec. Povolené hodnoty: CANON_EQ, CASE_INSENSITIVE, komentáře, DOTALL, LITERÁLy, VÍCEŘÁDKOVé UNICODE_CASE, UNIX_LINES<br /><br /> Group (typ: int) – skupina, do které se mají extrahovat tokeny Výchozí hodnota je-1 (rozdělená).|
|[standard_v2](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardTokenizer.html)|StandardTokenizerV2|Rozdělí text podle [pravidel segmentace textu Unicode](https://unicode.org/reports/tr29/).<br /><br /> **Možnosti**<br /><br /> maxTokenLength (typ: int) – maximální délka tokenu. Výchozí: 255, maximum: 300. Tokeny delší než maximální délka jsou rozděleny.|  
|[uax_url_email](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/standard/UAX29URLEmailTokenizer.html)|UaxUrlEmailTokenizer|Tokenizes adresy URL a e-maily jako jeden token.<br /><br /> **Možnosti**<br /><br /> maxTokenLength (typ: int) – maximální délka tokenu. Výchozí: 255, maximum: 300. Tokeny delší než maximální délka jsou rozděleny.|  
|[typy](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceTokenizer.html)|(typ platí pouze v případě, že jsou k dispozici možnosti) |Vydělí text na prázdný znak. Tokeny, které jsou delší než 255 znaků, jsou rozděleny.|  

 <sup>1</sup> typy provádějících tokenizaci jsou vždycky pevně uvedené v kódu s "#Microsoft. Azure. Search" tak, že "ClassicTokenizer" by ve skutečnosti byly zadány jako "#Microsoft. Azure. Search. ClassicTokenizer". Odebrali jsme předponu, aby se snížila šířka tabulky, ale nezapomeňte ji zahrnout do kódu. Všimněte si, že tokenizer_type je k dispozici pouze pro tokenizátory musíte nejdřív, které lze přizpůsobit. Pokud nejsou k dispozici žádné možnosti, jako je například případ s písmenem provádějících tokenizaci, neexistuje žádný přidružený #Microsoft. typ Azure. Search.

<a name="TokenFilters"></a>

###  <a name="token-filters-reference"></a>Odkazy na filtry tokenů

V následující tabulce jsou filtry tokenů implementované pomocí Apache Lucene propojeny s dokumentací k rozhraní API Lucene.

|**token_filter_name**|**token_filter_type** <sup>1</sup>|**Popis a možnosti**|  
|-|-|-|  
|[arabic_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ar/ArabicNormalizationFilter.html)|(typ platí pouze v případě, že jsou k dispozici možnosti)  |Filtr tokenu, který aplikuje arabskou normalizaci pro normalizaci orthography.|  
|[apostrof](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/tr/ApostropheFilter.html)|(typ platí pouze v případě, že jsou k dispozici možnosti)  |Odříznout všechny znaky za apostrofem (včetně apostrofu samotného). |  
|[asciifolding](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html)|AsciiFoldingTokenFilter|Převede abecední, číselné a symbolické znaky Unicode, které nejsou v prvních 127 znacích ASCII (blok "Basic Latin" Unicode) na své ekvivalenty standardu ASCII, pokud jeden existuje.<br /><br /> **Možnosti**<br /><br /> preserveOriginal (typ: bool) – Pokud je nastaveno na true, původní token se zachová. Výchozí hodnotou je hodnota false.|  
|[cjk_bigram](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/cjk/CJKBigramFilter.html)|CjkBigramTokenFilter|Forms bigrams výrazy CJK, které se generují z StandardTokenizer.<br /><br /> **Možnosti**<br /><br /> ignoreScripts (typ: pole řetězců) – skripty, které mají být ignorovány. Mezi povolené hodnoty patří: "Han", "Hiragana", "Katakana", "hangul". Výchozí hodnota je prázdný seznam.<br /><br /> outputUnigrams (typ: bool) – nastavte na hodnotu true, pokud chcete vždy vytvořit výstup unigrams i bigrams. Výchozí hodnotou je hodnota false.|  
|[cjk_width](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/cjk/CJKWidthFilter.html)|(typ platí pouze v případě, že jsou k dispozici možnosti)  |Normalizuje rozdíly v šířce CJK. Přeloží varianty standardu ASCII s plnou šířkou do ekvivalentních variant jazyka Katakana v latince a poloviční šířky do ekvivalentních znaků kana. |  
|[klasický](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/standard/ClassicFilter.html)|(typ platí pouze v případě, že jsou k dispozici možnosti)  |Odstraní anglické possessives a tečky z akronymů. |  
|[common_grams](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/commongrams/CommonGramsFilter.html)|CommonGramTokenFilter|Při indexování Sestavte bigrams, kde se často vyskytují výrazy. Jednoduché výrazy jsou pořád indexované, přičemž bigrams překrývají.<br /><br /> **Možnosti**<br /><br /> commonWords (typ: pole řetězců) – sada běžných slov. Výchozí hodnota je prázdný seznam. Povinná hodnota.<br /><br /> ignoreCase (typ: bool) – Pokud je nastaveno na true, porovnávání rozlišuje malá a velká písmena. Výchozí hodnotou je hodnota false.<br /><br /> queryMode (typ: bool) – vygeneruje bigrams a pak odebere společná slova a jednoduché výrazy následované běžným slovem. Výchozí hodnotou je hodnota false.|  
|[dictionary_decompounder](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/compound/DictionaryCompoundWordTokenFilter.html)|DictionaryDecompounderTokenFilter|Deformuluje složená slova nalezená v mnoha Germanic jazycích.<br /><br /> **Možnosti**<br /><br /> seznam slov (typ: pole řetězců) – seznam slov, která mají být shodná s. Výchozí hodnota je prázdný seznam. Povinná hodnota.<br /><br /> minWordSize (typ: int) – jsou zpracována pouze slova delší než tato zpráva. Výchozí hodnota je 5.<br /><br /> minSubwordSize (typ: int) – výstupy jsou delší než ty, které jsou na výstupu. Výchozí hodnota je 2.<br /><br /> maxSubwordSize (typ: int) – pouze podslovo kratší, než je výstup. Výchozí hodnota je 15.<br /><br /> onlyLongestMatch (typ: bool) – přidejte do výstupu pouze nejdelší vyhovující podslovo. Výchozí hodnotou je hodnota false.|  
|[edgeNGram_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenFilter.html)|EdgeNGramTokenFilterV2|Generuje n-gramů dané velikosti od začátku dopředu nebo zpět od konce vstupního tokenu.<br /><br /> **Možnosti**<br /><br /> minGram (typ: int) – výchozí hodnota: 1, maximum: 300.<br /><br /> maxGram (typ: int) – výchozí: 2, maximum 300. Musí být větší než minGram.<br /><br /> Side (typ: String) – určuje, která strana vstupu, z něhož má být n-gram vygenerována. Povolené hodnoty: "front", "back" |  
|[Elizi](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/util/ElisionFilter.html)|ElisionTokenFilter|Odebere elisions. Například "L'Avion" (rovina) se převede na "Avion" (rovinu).<br /><br /> **Možnosti**<br /><br /> články (typ: pole řetězců) – sada článků, které se mají odebrat. Výchozí hodnota je prázdný seznam. Pokud není nastaven žádný seznam článků, odeberou se ve výchozím nastavení všechny francouzské články.|  
|[german_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/de/GermanNormalizationFilter.html)|(typ platí pouze v případě, že jsou k dispozici možnosti)  |Normalizuje německé znaky podle heuristiky [algoritmu Snowball German2](https://snowballstem.org/algorithms/german2/stemmer.html) .|  
|[hindi_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/hi/HindiNormalizationFilter.html)|(typ platí pouze v případě, že jsou k dispozici možnosti)  |Normalizuje text v hindštině a odstraní některé rozdíly v variacích pravopisu. |  
|[indic_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/in/IndicNormalizationFilter.html)|IndicNormalizationTokenFilter|Normalizuje reprezentace textu v jazyce Unicode v indických jazycích.
|[Mějte](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeepWordFilter.html)|KeepTokenFilter|Filtr tokenu, který uchovává pouze tokeny s textem obsaženým v zadaném seznamu slov.<br /><br /> **Možnosti**<br /><br /> keepWords (typ: pole řetězců) – seznam slov, která mají být zachována. Výchozí hodnota je prázdný seznam. Povinná hodnota.<br /><br /> keepWordsCase (typ: bool) – Pokud je nastavena hodnota true, malá písmena nejprve všechna slova. Výchozí hodnotou je hodnota false.|  
|[keyword_marker](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeywordMarkerFilter.html)|KeywordMarkerTokenFilter|Označí výrazy jako klíčová slova.<br /><br /> **Možnosti**<br /><br /> Klíčová slova (typ: pole řetězců) – seznam slov, která mají být označena jako klíčová slova. Výchozí hodnota je prázdný seznam. Povinná hodnota.<br /><br /> ignoreCase (typ: bool) – Pokud je nastavena hodnota true, malá písmena nejprve všechna slova. Výchozí hodnotou je hodnota false.|  
|[keyword_repeat](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeywordRepeatFilter.html)|(typ platí pouze v případě, že jsou k dispozici možnosti)  |Vygeneruje každý příchozí token dvakrát jednou jako klíčové slovo a jednou jako jiné než klíčové slovo. |  
|[kstem](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/en/KStemFilter.html)|(typ platí pouze v případě, že jsou k dispozici možnosti)  |Vysoce výkonný kstem filtr pro angličtinu |  
|[length](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/LengthFilter.html)|LengthTokenFilter|Odebere slova, která jsou příliš dlouhá nebo příliš krátká.<br /><br /> **Možnosti**<br /><br /> min (typ: int) – minimální číslo. Výchozí hodnota: 0, maximum: 300.<br /><br /> Max (typ: int) – maximální počet. Výchozí: 300, maximum: 300.|  
|[počtu](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/LimitTokenCountFilter.html)|Microsoft. Azure. Search. LimitTokenFilter|Omezuje počet tokenů při indexování.<br /><br /> **Možnosti**<br /><br /> maxTokenCount (typ: int) – maximální počet tokenů, které mají být vyprodukovány. Výchozí hodnota je 1.<br /><br /> consumeAllTokens (typ: bool) – zda jsou všechny tokeny ze vstupu využívány i v případě, že je dosaženo maxTokenCount. Výchozí hodnotou je hodnota false.|  
|[malá](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)|(typ platí pouze v případě, že jsou k dispozici možnosti)  |Normalizuje text tokenu na malá písmena. |  
|[nGram_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenFilter.html)|NGramTokenFilterV2|Generuje n-gramů dané velikosti (y).<br /><br /> **Možnosti**<br /><br /> minGram (typ: int) – výchozí hodnota: 1, maximum: 300.<br /><br /> maxGram (typ: int) – výchozí: 2, maximum 300. Musí být větší než minGram.|  
|[pattern_capture](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternCaptureGroupTokenFilter.html)|PatternCaptureTokenFilter|Používá regulární výrazy jazyka Java k vygenerování více tokenů, jeden pro každou skupinu zachycení v jednom nebo více vzorcích.<br /><br /> **Možnosti**<br /><br /> vzory (typ: pole řetězců) – seznam vzorů, které se mají pro každý token porovnat. Povinná hodnota.<br /><br /> preserveOriginal (typ: bool) – nastavte na hodnotu true, chcete-li vrátit původní token, i když jedna ze vzorů odpovídá, výchozí hodnota: true |  
|[pattern_replace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternReplaceFilter.html)|PatternReplaceTokenFilter|Filtr tokenu, který u každého tokenu v datovém proudu používá určitý vzorek, který nahradí odpovídající výskyty zadaným náhradním řetězcem.<br /><br /> **Možnosti**<br /><br /> Pattern (typ: String) – povinný.<br /><br /> náhrada (typ: String) – povinné.|  
|[persian_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/fa/PersianNormalizationFilter.html)|(typ platí pouze v případě, že jsou k dispozici možnosti) |Aplikuje normalizaci pro perské. |  
|[fonetické](https://lucene.apache.org/core/6_6_1/analyzers-phonetic/org/apache/lucene/analysis/phonetic/package-tree.html)|PhoneticTokenFilter|Vytvoří tokeny pro fonetické shody.<br /><br /> **Možnosti**<br /><br /> Encoder (typ: String) – fonetický kodér, který se má použít. Mezi povolené hodnoty patří: "metaphone", "doubleMetaphone", "SOUNDEX", "refinedSoundex", "caverphone1", "caverphone2", "Cologne", "nysiis", "koelnerPhonetik", "haasePhonetik", "beiderMorse". Výchozí: "metaphone". Výchozí hodnota je metaphone.<br /><br /> Další informace najdete v tématu [kodér](https://commons.apache.org/proper/commons-codec/archives/1.10/apidocs/org/apache/commons/codec/language/package-frame.html) .<br /><br /> Replace (Type: bool)-true, pokud mají kódované tokeny nahradit původní tokeny, false, pokud by měly být přidány jako synonyma. Výchozí hodnota je true.|  
|[porter_stem](https://lucene.apache.org/core/6_6_1/analyzers-common/org/tartarus/snowball/ext/PorterStemmer.html)|(typ platí pouze v případě, že jsou k dispozici možnosti)  |Transformuje datový proud tokenu na základě [Porter algoritmu](https://tartarus.org/~martin/PorterStemmer/). |  
|[zpět](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/reverse/ReverseStringFilter.html)|(typ platí pouze v případě, že jsou k dispozici možnosti)  |Obrátí řetězec tokenu. |  
|[scandinavian_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianNormalizationFilter.html)|(typ platí pouze v případě, že jsou k dispozici možnosti)  |Normalizuje použití přiměnitelné Scandinavian znaků. |  
|[scandinavian_folding](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianFoldingFilter.html)|(typ platí pouze v případě, že jsou k dispozici možnosti)  |Skládání Scandinavian znaků åÅäæÄÆ->a a öÖøØ->o. Také se odkládá na použití dvojitých samohláskami AA, AE, Ao, OE a ó, a proto zbývá jenom první z nich. |  
|[střešní tašky](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/shingle/ShingleFilter.html)|ShingleTokenFilter|Vytvoří kombinace tokenů jako jeden token.<br /><br /> **Možnosti**<br /><br /> maxShingleSize (typ: int) – výchozí hodnota je 2.<br /><br /> minShingleSize (typ: int) – výchozí hodnota je 2.<br /><br /> outputUnigrams (typ: bool) – Pokud má hodnotu true, výstupní datový proud obsahuje vstupní tokeny (unigrams) a také Shingles. Výchozí hodnota je true.<br /><br /> outputUnigramsIfNoShingles (typ: bool) – je-li nastavena hodnota true, přepište chování outputUnigrams = = false v případě, že nejsou k dispozici žádné Shingles. Výchozí hodnotou je hodnota false.<br /><br /> tokenSeparator (typ: řetězec) – řetězec, který má být použit při spojování sousedících tokenů, aby bylo možné vytvořit střešní tašky. Výchozí hodnota je "".<br /><br /> filterToken (typ: řetězec) – řetězec, který má být vložen pro každou pozici, na které není žádný token. Výchozí hodnota je "_".|  
|[snowball](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/snowball/SnowballFilter.html)|SnowballTokenFilter|Filtr tokenu Snowball<br /><br /> **Možnosti**<br /><br /> jazyk (typ: řetězec) – povolené hodnoty jsou: "arménština", "baskičtina", "Katalánština", "Dánština", "Holandština", "Angličtina", "finština", "francouzština", "Němčina", "german2", "Maďarština", "Italština", "KP", "Lovins", "Norština", "Porter", "portugalština", "Rumunština", "ruština", "španělština", "Čeština"|  
|[sorani_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ckb/SoraniNormalizationFilter.html)|SoraniNormalizationTokenFilter|Normalizuje reprezentace textu sorani v kódování Unicode.<br /><br /> **Možnosti**<br /><br /> Žádné|  
|stemmer|StemmerTokenFilter|Filtr lemmatizátor konkrétního jazyka.<br /><br /> **Možnosti**<br /><br /> jazyk (typ: řetězec) – povolené hodnoty zahrnují: <br /> -   [načtení](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ar/ArabicStemmer.html)<br />-   [iniciála](https://snowballstem.org/algorithms/armenian/stemmer.html)<br />-   [baskičtina](https://snowballstem.org/algorithms/basque/stemmer.html)<br />-   [brazilský](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/br/BrazilianStemmer.html)<br />– "Bulharština"<br />-   [Katalánština](https://snowballstem.org/algorithms/catalan/stemmer.html)<br />-   [českém](https://portal.acm.org/citation.cfm?id=1598600)<br />-   [Dánsk](https://snowballstem.org/algorithms/danish/stemmer.html)<br />-   [nizozemštin](https://snowballstem.org/algorithms/dutch/stemmer.html)<br />-   ["dutchKp"](https://snowballstem.org/algorithms/kraaij_pohlmann/stemmer.html)<br />-   [verzi](https://snowballstem.org/algorithms/porter/stemmer.html)<br />-   ["lightEnglish"](https://ciir.cs.umass.edu/pubfiles/ir-35.pdf)<br />-   ["minimalEnglish"](https://www.researchgate.net/publication/220433848_How_effective_is_suffixing)<br />-   ["possessiveEnglish"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/en/EnglishPossessiveFilter.html)<br />-   ["porter2"](https://snowballstem.org/algorithms/english/stemmer.html)<br />-   ["lovins"](https://snowballstem.org/algorithms/lovins/stemmer.html)<br />-   [finštin](https://snowballstem.org/algorithms/finnish/stemmer.html)<br />- "lightFinnish"<br />-   [Francouzská](https://snowballstem.org/algorithms/french/stemmer.html)<br />-   ["lightFrench"](https://dl.acm.org/citation.cfm?id=1141523)<br />-   ["minimalFrench"](https://dl.acm.org/citation.cfm?id=318984)<br />– "galicijština"<br />- "minimalGalician"<br />-   [německé](https://snowballstem.org/algorithms/german/stemmer.html)<br />-   ["german2"](https://snowballstem.org/algorithms/german2/stemmer.html)<br />-   ["lightGerman"](https://dl.acm.org/citation.cfm?id=1141523)<br />- "minimalGerman"<br />-   [německé](https://sais.se/mthprize/2007/ntais2007.pdf)<br />– "Hindština"<br />-   [Maďarština](https://snowballstem.org/algorithms/hungarian/stemmer.html)<br />-   ["lightHungarian"](https://dl.acm.org/citation.cfm?id=1141523&dl=ACM&coll=DL&CFID=179095584&CFTOKEN=80067181)<br />-   [Indonéská](https://eprints.illc.uva.nl/741/2/MoL-2003-03.text.pdf)<br />-   [Irska](https://snowballstem.org/otherapps/oregan/)<br />-   [Italština](https://snowballstem.org/algorithms/italian/stemmer.html)<br />-   ["lightItalian"](https://www.ercim.eu/publication/ws-proceedings/CLEF2/savoy.pdf)<br />-   ["sorani"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ckb/SoraniStemmer.html)<br />-   [rozložení](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/lv/LatvianStemmer.html)<br />-   [Norsko](https://snowballstem.org/algorithms/norwegian/stemmer.html)<br />-   ["lightNorwegian"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/no/NorwegianLightStemmer.html)<br />-   ["minimalNorwegian"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/no/NorwegianMinimalStemmer.html)<br />-   ["lightNynorsk"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/no/NorwegianLightStemmer.html)<br />-   ["minimalNynorsk"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/no/NorwegianMinimalStemmer.html)<br />-   [portugalštinu](https://snowballstem.org/algorithms/portuguese/stemmer.html)<br />-   ["lightPortuguese"](https://dl.acm.org/citation.cfm?id=1141523&dl=ACM&coll=DL&CFID=179095584&CFTOKEN=80067181)<br />-   ["minimalPortuguese"](https://www.inf.ufrgs.br/~buriol/papers/Orengo_CLEF07.pdf)<br />-   ["portugueseRslp"](https://www.inf.ufrgs.br//~viviane/rslp/index.htm)<br />-   [rumunské](https://snowballstem.org/otherapps/romanian/)<br />-   [jazykové](https://snowballstem.org/algorithms/russian/stemmer.html)<br />-   ["lightRussian"](https://doc.rero.ch/lm.php?url=1000%2C43%2C4%2C20091209094227-CA%2FDolamic_Ljiljana_-_Indexing_and_Searching_Strategies_for_the_Russian_20091209.pdf)<br />-   [španělštin](https://snowballstem.org/algorithms/spanish/stemmer.html)<br />-   ["lightSpanish"](https://www.ercim.eu/publication/ws-proceedings/CLEF2/savoy.pdf)<br />-   [Švédština](https://snowballstem.org/algorithms/swedish/stemmer.html)<br />- "lightSwedish"<br />-   [Tureck](https://snowballstem.org/algorithms/turkish/stemmer.html)|  
|[stemmer_override](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/StemmerOverrideFilter.html)|StemmerOverrideTokenFilter|Všechny výrazy, které jsou v slovníkovém formátu, jsou označeny jako klíčová slova, což znemožňuje odvozování řetězce. Musí být umístěn před všemi lemmatizátor filtry.<br /><br /> **Možnosti**<br /><br /> Rules (typ: pole řetězců) – odvozená pravidla v následujícím formátu "Word => STEM" například "spuštěno => Run". Výchozí hodnota je prázdný seznam.  Povinná hodnota.|  
|[stopwords](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/StopFilter.html)|StopwordsTokenFilter|Odstraní slova stop z datového proudu tokenu. Ve výchozím nastavení používá filtr předdefinovaný seznam stop slov pro angličtinu.<br /><br /> **Možnosti**<br /><br /> stopslova (typ: pole řetězců) – seznam stopslova. Nelze zadat, pokud je zadán parametr stopwordsList.<br /><br /> stopwordsList (typ: String)-předdefinovaný seznam stopslova. Nelze zadat, pokud je zadán parametr stopslova. Povolené hodnoty zahrnují: "Arabština", "arménština", "baskičtina", "Brazílie", "Bulharština", "Katalánština", "Čeština", "Dánština", "Holandština", "Angličtina", "finština", "francouzština", "galicijština" Němčina "," Řečtina "," Hindština "," Maďarština "," indonéština "," Irská "," Italština "," Norština "," Norština "," portugalština "," ruština "," Čeština "," ruština "," sorani "," španělština " Nelze zadat, pokud je zadán parametr stopslova. <br /><br /> ignoreCase (typ: bool) – je-li nastavena hodnota true, jsou nejprve méně použitaa všechna slova. Výchozí hodnotou je hodnota false.<br /><br /> removeTrailing (typ: bool) – Pokud má hodnotu true, ignoruje Poslední hledaný termín, pokud se jedná o slovo typu stop. Výchozí hodnota je true.
|[Určete](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/synonym/SynonymFilter.html)|SynonymTokenFilter|Porovná synonyma jednoho nebo více slov v proudu tokenů.<br /><br /> **Možnosti**<br /><br /> synonyma (typ: pole řetězců) – povinné. Seznam synonym v jednom z následujících dvou formátů:<br /><br /> -nenáročné, slavná => úžasné – všechny výrazy na levé straně symbolu => se nahrazují všemi výrazy na pravé straně.<br /><br /> -nenáročné, neslavnáelné, úžasné čárkami oddělený seznam ekvivalentních slov. Nastavte možnost rozbalení pro změnu způsobu interpretace tohoto seznamu.<br /><br /> ignoreCase (typ: bool) – vstup s ohyby pro porovnání Výchozí hodnotou je hodnota false.<br /><br /> expand (typ: bool) – Pokud je nastaveno na true, všechna slova v seznamu synonym (if => Notation se nepoužívají) namapují na sebe navzájem. <br />Následující seznam: nestejné, neslavnáelné, úžasné je ekvivalentem: neelné, neelné, slavná, úžasné => neelné, neshodné, slavná, úžasné<br /><br />– Pokud je hodnota false, následující seznam: nenáročné, nepravdivý, slavná, úžasné jsou ekvivalentem: nenáročné, nepravdivě, slavná, úžasné => nepřít.|  
|[sklon](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/TrimFilter.html)|(typ platí pouze v případě, že jsou k dispozici možnosti)  |Ořízne úvodní a koncové mezery z tokenů. |  
|[zkrátit](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/TruncateTokenFilter.html)|TruncateTokenFilter|Zkrátí tyto výrazy na konkrétní délku.<br /><br /> **Možnosti**<br /><br /> length (typ: int) – výchozí: 300, maximum: 300. Povinná hodnota.|  
|[tabulka](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/RemoveDuplicatesTokenFilter.html)|UniqueTokenFilter|Filtruje tokeny se stejným textem jako s předchozím tokenem.<br /><br /> **Možnosti**<br /><br /> onlyOnSamePosition (typ: bool) – Pokud je nastavena, odebrání duplicitních hodnot pouze na stejné pozici. Výchozí hodnota je true.|  
|[všechna](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/UpperCaseFilter.html)|(typ platí pouze v případě, že jsou k dispozici možnosti)  |Normalizuje text tokenu na velká písmena. |  
|[word_delimiter](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/WordDelimiterFilter.html)|WordDelimiterTokenFilter|Rozdělí slova na podslova a provede volitelné transformace pro skupiny podslov.<br /><br /> **Možnosti**<br /><br /> generateWordParts (typ: bool) – způsobí vygenerování částí slov, například "AzureSearch" se bude "vyhledávání" Azure. Výchozí hodnota je true.<br /><br /> generateNumberParts (typ: bool) – způsobí vygenerování číselných podslov. Výchozí hodnota je true.<br /><br /> catenateWords (typ: bool) – způsobí, že maximální počet spuštění částí Wordu, které se mají catenated, například "Azure-Search", bude "AzureSearch". Výchozí hodnotou je hodnota false.<br /><br /> catenateNumbers (typ: bool) – způsobí, že se v případě maximálního počtu částí pro catenated, například "1-2", bude "12". Výchozí hodnotou je hodnota false.<br /><br /> catenateAll (typ: bool) – způsobí, že se všechny části podwordu catenated, např. "Azure-Search-1" se bude "AzureSearch1". Výchozí hodnotou je hodnota false.<br /><br /> splitOnCaseChange (typ: bool) – Pokud má hodnotu true, rozdělí slova na caseChange, například "AzureSearch" se bude "Azure" Search ". Výchozí hodnota je true.<br /><br /> preserveOriginal – způsobí zachovávání původních slov a přidání do seznamu podslov. Výchozí hodnotou je hodnota false.<br /><br /> splitOnNumerics (typ: bool) – Pokud je hodnota true, rozdělí se na čísla, například "Azure1Search" se bude "Azure" "1" hledání ". Výchozí hodnota je true.<br /><br /> stemEnglishPossessive (typ: bool) – způsobí odebrání koncového slova pro každé podslovo. Výchozí hodnota je true.<br /><br /> protectedWords (typ: pole řetězců) – tokeny, které mají být chráněny před oddělenými hodnotami. Výchozí hodnota je prázdný seznam.|  

 <sup>1</sup> typy filtru tokenů jsou vždycky pevně uvedené v kódu s "#Microsoft. Azure. Search" tak, že "ArabicNormalizationTokenFilter" by ve skutečnosti byly zadané jako "#Microsoft. Azure. Search. ArabicNormalizationTokenFilter".  Odebrali jsme předponu, aby se snížila šířka tabulky, ale nezapomeňte ji zahrnout do kódu.  


## <a name="see-also"></a>Viz také  
 [Rozhraní REST API pro Azure Kognitivní hledání](/rest/api/searchservice/)   
 [Příklady analyzátorů v Azure Kognitivní hledání >](search-analyzers.md#examples)    
 [Vytvoření indexu &#40;Azure Kognitivní hledání REST API&#41;](/rest/api/searchservice/create-index)