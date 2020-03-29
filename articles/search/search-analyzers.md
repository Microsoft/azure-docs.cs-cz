---
title: Analyzátory pro jazykové a textové zpracování
titleSuffix: Azure Cognitive Search
description: Přiřaďte analyzátory k prohledávatelným textovým polím v indexu a nahraďte výchozí standard Lucene vlastními, předdefinovanými nebo jazykově specifickými alternativami.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/10/2019
ms.openlocfilehash: 2e4a6ab8825982969ffa4654c2418f7a9d168d2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75460714"
---
# <a name="analyzers-for-text-processing-in-azure-cognitive-search"></a>Analyzátory pro zpracování textu v Azure Cognitive Search

*Analyzátor* je součástí [fulltextového vyhledávače](search-lucene-query-architecture.md) zodpovědného za zpracování textu v řetězecch dotazů a indexovaných dokumentech. Různé analyzátory pracují s textem různými způsoby v závislosti na scénáři. Analyzátory jazyků zpracovávají text pomocí jazykových pravidel za účelem zlepšení kvality vyhledávání, zatímco jiné analyzátory provádějí například více základních úloh, jako je například převod znaků na malá písmena. 

Analyzátory jazyků se nejčastěji používají a ke každému prohledávatelnému poli v indexu Azure Cognitive Search je přiřazen výchozí analyzátor jazyka. Následující jazykové transformace jsou typické během analýzy textu:

+ Slova, která nejsou nezbytná (stopwords) a interpunkce, budou odebrána.
+ Fráze a slova rozdělená do vět se rozdělí na části.
+ Velká slova jsou malá písmena.
+ Slova jsou redukována na kořenové formy, takže shodu lze nalézt bez ohledu na čas.

Analyzátory jazyka převádějí textový vstup do primitivních nebo kořenových formulářů, které jsou efektivní pro ukládání a načítání informací. Převod dochází během indexování, při index je sestaven a pak znovu při hledání při čtení indexu. Je pravděpodobnější, že získáte výsledky hledání, které očekáváte, pokud použijete stejný analyzátor pro obě operace.

## <a name="default-analyzer"></a>Výchozí analyzátor  

Azure Cognitive Search používá [Apache Lucene Standard analyzer (standard lucene)](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) jako výchozí, který rozdělí text na prvky podle pravidel ["Unicode text segmentace".](https://unicode.org/reports/tr29/) Standardní analyzátor navíc převede všechny znaky na jejich malá písmena formuláře. Indexované dokumenty i hledané termíny procházejí analýzou během indexování a zpracování dotazů.  

Používá se automaticky na každém prohledávatelném poli. Výchozí nastavení můžete přepsat v poli podle pole. Alternativníanalyzátory mohou být [analyzátor jazyka](index-add-language-analyzers.md), [vlastní analyzátor](index-add-custom-analyzers.md)nebo předdefinovaný analyzátor [ze seznamu dostupných analyzátorů](index-add-custom-analyzers.md#AnalyzerTable).


## <a name="types-of-analyzers"></a>Typy analyzátorů

Následující seznam popisuje, které analyzátory jsou k dispozici v Azure Cognitive Search.

| Kategorie | Popis |
|----------|-------------|
| [Standardní analyzátor Lucene](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) | Default (Výchozí). Není vyžadována žádná specifikace ani konfigurace. Tento univerzální analyzátor funguje dobře pro většinu jazyků a scénářů.|
| Předdefinované analyzátory | Nabízeno jako hotový výrobek určený k použití tak, jak je. <br/>Existují dva typy: specializované a jazyk. Co je dělá "předdefinovanými", je to, že na ně odkazujete podle názvu, bez konfigurace nebo přizpůsobení. <br/><br/>[Specializované (jazyk-agnostik) analyzátory](index-add-custom-analyzers.md#AnalyzerTable) se používají, když textové vstupy vyžadují specializované zpracování nebo minimální zpracování. Mezi nejazykové předdefinované analyzátory patří **Asciifolding**, **Klíčové slovo**, **Vzorek**, **Jednoduché**, **Zastavit**, **Prázdné znaky**.<br/><br/>[Analyzátory jazyků](index-add-language-analyzers.md) se používají, když potřebujete bohatou jazykovou podporu pro jednotlivé jazyky. Azure Cognitive Search podporuje 35 analyzátorů jazyka Lucene a 50 analyzátorů zpracování přirozeného jazyka Microsoftu. |
|[Vlastní analyzátory](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search) | Odkazuje na uživatelem definovanou konfiguraci kombinace existujících prvků, která se skládá z jednoho tokenizéru (povinné) a volitelných filtrů (char nebo token).|

Několik předdefinovaných analyzátorů, například **Pattern** nebo **Stop**, podporuje omezenou sadu možností konfigurace. Chcete-li tyto možnosti nastavit, vytvořte vlastní analyzátor, který se skládá z předdefinovaného analyzátoru a jedné z alternativních možností zdokumentovaných v [předdefinované matné referenci analyzátoru](index-add-custom-analyzers.md#AnalyzerTable). Stejně jako u jakékoli vlastní konfigurace, zadejte novou konfiguraci s názvem, jako je *například myPatternAnalyzer* odlišit od analyzátoru Lucene Pattern.

## <a name="how-to-specify-analyzers"></a>Jak určit analyzátory

1. (pouze pro vlastní analyzátory) Vytvořte oddíl s pojmenovaným **analyzátorem** v definici indexu. Další informace naleznete v [tématu Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) a také [Add custom analyzers](index-add-custom-analyzers.md).

2. V [definici pole](https://docs.microsoft.com/rest/api/searchservice/create-index) v indexu nastavte vlastnost **analyzátoru** pole na název cílového `"analyzer" = "keyword"`analyzátoru (například . Mezi platné hodnoty patří název předdefinovaného analyzátoru, analyzátor jazyka nebo vlastní analyzátor definovaný také ve schématu indexu. Plán ování přiřazení analyzátoru ve fázi definice indexu před vytvořením indexu ve službě.

3. Volitelně můžete namísto jedné **vlastnosti analyzátoru** nastavit různé analyzátory pro indexování a dotazování pomocí parametrů pole **indexAnalyzer** a **searchAnalyzer.** Pro přípravu a načítání dat byste použili různé analyzátory, pokud jedna z těchto aktivit vyžaduje konkrétní transformaci, kterou druhá nepotřebuje.

> [!NOTE]
> Není možné použít jiný [analyzátor jazyka](index-add-language-analyzers.md) v době indexování než v době dotazu pro pole. Tato funkce je vyhrazena pro [vlastní analyzátory](index-add-custom-analyzers.md). Z tohoto důvodu pokud se pokusíte nastavit **searchAnalyzer** nebo **indexAnalyzer** vlastnosti na název analyzátoru jazyka, rozhraní REST API vrátí odpověď na chybu. Místo toho je nutné použít vlastnost **analyzátoru.**

Přiřazení **analyzátoru** nebo **indexAnalyzer** k poli, které již bylo fyzicky vytvořeno, není povoleno. Pokud je něco z toho nejasné, přečtěte si následující tabulku, která akce vyžaduje opětovné sestavení a proč.
 
 | Scénář | Dopad | Kroky |
 |----------|--------|-------|
 | Přidání nového pole | Minimální | Pokud pole ve schématu ještě neexistuje, není třeba provést žádnou revizi pole, protože pole ještě nemá fyzickou přítomnost v indexu. Aktualizovat [index](https://docs.microsoft.com/rest/api/searchservice/update-index) můžete použít k přidání nového pole do existujícího indexu a [sloučeníMOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) k jeho naplnění.|
 | Přidejte **analyzátor** nebo **indexAnalyzer** do existujícího indexovaného pole. | [Obnovit](search-howto-reindex.md) | Obrácený index pro toto pole musí být znovu vytvořen od základů a obsah těchto polí musí být přeindexován. <br/> <br/>Pro indexy v rámci aktivního vývoje [odstraňte](https://docs.microsoft.com/rest/api/searchservice/delete-index) a [vytvořte](https://docs.microsoft.com/rest/api/searchservice/create-index) index pro vyzvednutí nové definice pole. <br/> <br/>U indexů ve výrobě můžete odložit opětovné sestavení vytvořením nového pole, které poskytne revidovanou definici, a začít ji používat místo starého pole. Pomocí [indexu aktualizace](https://docs.microsoft.com/rest/api/searchservice/update-index) zahrňte nové pole a [sloučitOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) k jeho naplnění. Později jako součást plánované údržby indexu můžete vyčistit index a odebrat zastaralá pole. |

## <a name="when-to-add-analyzers"></a>Kdy přidat analyzátory

Nejlepší čas přidat a přiřadit analyzátory je během aktivního vývoje, při přetažení a opětovné vytvoření indexy je rutina.

Jako definice indexu ztuhne, můžete připojit nové konstrukce analýzy do indexu, ale budete muset předat **allowIndexDowntime** příznak [aktualizovat index,](https://docs.microsoft.com/rest/api/searchservice/update-index) pokud chcete, aby se zabránilo této chybě:

*"Aktualizace indexu není povolena, protože by způsobila prostoje. Chcete-li do existujícího indexu přidat nové analyzátory, tokenizátory, filtry tokenů nebo filtry znaků, nastavte parametr dotazu allowIndexDowntime na hodnotu true. Všimněte si, že tato operace bude umístit index do režimu offline po dobu nejméně několika sekund, což způsobuje indexování a požadavky na dotazy nezdaří. Dostupnost indexu může být snížena na několik minut po aktualizaci indexu nebo déle pro velmi velké indexy."*

Totéž platí při přiřazování analyzátoru k poli. Analyzátor je nedílnou součástí definice pole, takže jej můžete přidat pouze při vytvoření pole. Pokud chcete přidat analyzátory do existujících polí, budete muset [přetáhnout a znovu sestavit](search-howto-reindex.md) index nebo přidat nové pole s analyzátorem, který chcete.

Jak již bylo uvedeno, výjimkou je varianta **searchAnalyzer.** Ze tří způsobů, jak určit analyzátory (**analyzátor**, **indexAnalyzer**, **searchAnalyzer**), lze změnit pouze atribut **searchAnalyzer** na existujícím poli.

## <a name="recommendations-for-working-with-analyzers"></a>Doporučení pro práci s analyzátory

Tato část nabízí rady, jak pracovat s analyzátory.

### <a name="one-analyzer-for-read-write-unless-you-have-specific-requirements"></a>Jeden analyzátor pro čtení a zápis, pokud nemáte specifické požadavky

Azure Cognitive Search umožňuje zadat různé analyzátory pro indexování a vyhledávání pomocí dalších parametrů **pole indexAnalyzer** a **searchAnalyzer.** Pokud není zadán, analyzátor sada s **vlastností analyzátoru** se používá pro indexování a vyhledávání. Pokud `analyzer` není zadán, použije se výchozí analyzátor Standard Lucene.

Obecným pravidlem je použití stejného analyzátoru pro indexování i dotazování, pokud konkrétní požadavky nevyžadují jinak. Ujistěte se, že důkladně otestujete. Pokud se zpracování textu liší v době vyhledávání a indexování, riskujete neshodu mezi termíny dotazu a indexovanými termíny, pokud nejsou zarovnány konfigurace analyzátoru vyhledávání a indexování.

### <a name="test-during-active-development"></a>Test během aktivního vývoje

Přepsání standardního analyzátoru vyžaduje opětovné sestavení indexu. Pokud je to možné, rozhodněte se, které analyzátory použít během aktivního vývoje, před zaujetím indexu do produkčního prostředí.

### <a name="inspect-tokenized-terms"></a>Kontrola tokenizovaných termínů

Pokud hledání se nezdaří vrátit očekávané výsledky, nejpravděpodobnější scénář je token nesrovnalosti mezi termín vstupy v dotazu a tokenizované termíny v indexu. Pokud tokeny nejsou stejné, shody se nepodaří zhmotnit. Chcete-li zkontrolovat výstup tokenizeru, doporučujeme použít [analyzovat rozhraní API](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) jako nástroj šetření. Odpověď se skládá z tokenů, které jsou generovány konkrétním analyzátorem.

<a name="examples"></a>

## <a name="rest-examples"></a>Příklady REST

Níže uvedené příklady ukazují definice analyzátoru pro několik klíčových scénářů.

+ [Příklad vlastního analyzátoru](#Custom-analyzer-example)
+ [Přiřazení analyzátorů k příkladu pole](#Per-field-analyzer-assignment-example)
+ [Míchání analyzátorů pro indexování a vyhledávání](#Mixing-analyzers-for-indexing-and-search-operations)
+ [Příklad analyzátoru jazyka](#Language-analyzer-example)

<a name="Custom-analyzer-example"></a>

### <a name="custom-analyzer-example"></a>Příklad vlastního analyzátoru

Tento příklad ilustruje definici analyzátoru s vlastními možnostmi. Vlastní možnosti pro char filtry, tokenizers a token filtry jsou určeny samostatně jako pojmenované konstrukce a pak odkazuje v definici analyzátoru. Předdefinované prvky se používají tak, jak jsou, a jednoduše odkazovány podle názvu.

Procházení tohoto příkladu:

* Analyzátory jsou vlastností třídy pole pro prohledávatelné pole.
* Vlastní analyzátor je součástí definice indexu. Může být lehce přizpůsobena (například přizpůsobení jedné možnosti v jednom filtru) nebo přizpůsobena na více místech.
* V tomto případě je vlastní analyzátor "my_analyzer", který zase používá vlastní standardní tokenizer "my_standard_tokenizer" a dva filtry tokenů: malý a přizpůsobený filtr asciifolding "my_asciifolding".
* Definuje také 2 vlastní char filtry "map_dash" a "remove_whitespace". První nahradí všechny pomlčky podtržítkem, zatímco druhý odstraní všechny mezery. Mezery musí být zakódovány v pravidlech mapování UTF-8. Char filtry jsou použity před tokenizace a ovlivní výsledné tokeny (standardní tokenizer přestávky na pomlčku a mezery, ale ne na podtržítko).

~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"my_analyzer"
        }
     ],
     "analyzers":[
        {
           "name":"my_analyzer",
           "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
           "charFilters":[
              "map_dash",
              "remove_whitespace"
           ],
           "tokenizer":"my_standard_tokenizer",
           "tokenFilters":[
              "my_asciifolding",
              "lowercase"
           ]
        }
     ],
     "charFilters":[
        {
           "name":"map_dash",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["-=>_"]
        },
        {
           "name":"remove_whitespace",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["\\u0020=>"]
        }
     ],
     "tokenizers":[
        {
           "name":"my_standard_tokenizer",
           "@odata.type":"#Microsoft.Azure.Search.StandardTokenizerV2",
           "maxTokenLength":20
        }
     ],
     "tokenFilters":[
        {
           "name":"my_asciifolding",
           "@odata.type":"#Microsoft.Azure.Search.AsciiFoldingTokenFilter",
           "preserveOriginal":true
        }
     ]
  }
~~~~

<a name="Per-field-analyzer-assignment-example"></a>

### <a name="per-field-analyzer-assignment-example"></a>Příklad přiřazení analyzátoru pro pole

Standardní analyzátor je výchozí. Předpokládejme, že chcete nahradit výchozí hodnotu jiným předdefinovaným analyzátorem, například analyzátorem vzorků. Pokud nenastavujete vlastní možnosti, stačí je zadat podle názvu v definici pole.

Prvek "analyzátor" přepíše standardní analyzátor na základě pole po poli. Neexistuje žádné globální přepsání. V tomto `text1` příkladu používá analyzátor vzorů a `text2`, který neurčuje analyzátor, používá výchozí.

~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text1",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"pattern"
        },
        {
           "name":"text2",
           "type":"Edm.String",
           "searchable":true
        }
     ]
  }
~~~~

<a name="Mixing-analyzers-for-indexing-and-search-operations"></a>

### <a name="mixing-analyzers-for-indexing-and-search-operations"></a>Míchání analyzátorů pro indexování a vyhledávání

Api obsahují další atributy indexu pro určení různých analyzátorů pro indexování a vyhledávání. Atributy **searchAnalyzer** a **indexAnalyzer** musí být zadány jako pár, který nahradí atribut jednoho **analyzátoru.**


~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "indexAnalyzer":"whitespace",
           "searchAnalyzer":"simple"
        },
     ],
  }
~~~~

<a name="Language-analyzer-example"></a>

### <a name="language-analyzer-example"></a>Příklad analyzátoru jazyka

Pole obsahující řetězce v různých jazycích mohou používat analyzátor jazyka, zatímco jiná pole si zachovávají výchozí hodnotu (nebo jiná předdefinovaná nebo vlastní analyzátor). Pokud používáte analyzátor jazyka, musí být použit pro indexování i vyhledávací operace. Pole, která používají analyzátor jazyka, nemohou mít různé analyzátory pro indexování a vyhledávání.

~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "indexAnalyzer":"whitespace",
           "searchAnalyzer":"simple"
        },
        {
           "name":"text_fr",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"fr.lucene"
        }
     ],
  }
~~~~

## <a name="c-examples"></a>Příklady jazyka C#

Pokud používáte ukázky kódu sady .NET SDK, můžete připojit tyto příklady k použití nebo konfiguraci analyzátorů.

+ [Přiřazení integrovaného analyzátoru](#Assign-a-language-analyzer)
+ [Konfigurace analyzátoru](#Define-a-custom-analyzer)

<a name="Assign-a-language-analyzer"></a>

### <a name="assign-a-language-analyzer"></a>Přiřazení analyzátoru jazyka

V definici pole je určen každý analyzátor, který se používá tak, jak je, bez konfigurace. Neexistuje žádný požadavek na vytvoření konstrukce analyzátoru. 

Tento příklad přiřadí analyzátory Microsoft English a French k polím popisu. Je to úryvek převzatý z větší definice indexu hotelů, který se vytváří pomocí třídy Hotel v hotels.cs souboru ukázky [DotNetHowTo.](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)

Analyzátor volání , určení [typu AnalyzerName](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet) poskytující analyzátor textu podporovaný v Azure Cognitive Search. [Analyzer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzer?view=azure-dotnet)

```csharp
    public partial class Hotel
    {
       . . . 

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.EnMicrosoft)]
        [JsonProperty("description")]
        public string Description { get; set; }

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.FrLucene)]
        [JsonProperty("description_fr")]
        public string DescriptionFr { get; set; }

      . . .
    }
```
<a name="Define-a-custom-analyzer"></a>

### <a name="define-a-custom-analyzer"></a>Definování vlastního analyzátoru

Pokud je požadováno přizpůsobení nebo konfigurace, budete muset přidat konstrukci analyzátoru do indexu. Jakmile ji definujete, můžete ji přidat definici pole, jak je znázorněno v předchozím příkladu.

Vytvořte objekt [CustomAnalyzer.](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.customanalyzer?view=azure-dotnet) Další příklady naleznete [v tématu CustomAnalyzerTests.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Microsoft.Azure.Search/tests/Tests/CustomAnalyzerTests.cs).

```csharp
{
   var definition = new Index()
   {
         Name = "hotels",
         Fields = FieldBuilder.BuildForType<Hotel>(),
         Analyzers = new[]
            {
               new CustomAnalyzer()
               {
                     Name = "url-analyze",
                     Tokenizer = TokenizerName.UaxUrlEmail,
                     TokenFilters = new[] { TokenFilterName.Lowercase }
               }
            },
   };

   serviceClient.Indexes.Create(definition);
```

## <a name="next-steps"></a>Další kroky

+ Projděte si naše komplexní vysvětlení toho, [jak fulltextové vyhledávání funguje v Azure Cognitive Search](search-lucene-query-architecture.md). Tento článek používá příklady vysvětlit chování, které se může zdát counter-intuitivní na povrchu.

+ Vyzkoušejte další syntaxi dotazu z ukázkové části [Hledat dokumenty](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples) nebo z [syntaxe jednoduchého dotazu](query-simple-syntax.md) v průzkumníku vyhledávání na portálu.

+ Přečtěte si, jak používat [lexikální analyzátory specifické pro jazyk](index-add-language-analyzers.md).

+ [Nakonfigurujte vlastní analyzátory](index-add-custom-analyzers.md) pro minimální zpracování nebo specializované zpracování v jednotlivých polích.

## <a name="see-also"></a>Viz také

 [Rozhraní API pro vyhledávání v dokumentech](https://docs.microsoft.com/rest/api/searchservice/search-documents) 

 [Jednoduchá syntaxe dotazů](query-simple-syntax.md) 

 [Úplná syntaxe dotazů Lucene](query-lucene-syntax.md) 
 
 [Zpracování výsledků vyhledávání](search-pagination-page-layout.md)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png
