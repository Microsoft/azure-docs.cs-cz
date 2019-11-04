---
title: Analyzátory pro jazyk a zpracování textu
titleSuffix: Azure Cognitive Search
description: Přiřaďte analyzátory k prohledávatelným textovým polím v indexu, abyste nahradili výchozí standardní řešení Lucene vlastními, předdefinovanými nebo pro konkrétní jazyky.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 32ac91df042eb29c39cc54b738dbb96aff3104f3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73496511"
---
# <a name="analyzers-for-text-processing-in-azure-cognitive-search"></a>Analyzátory pro zpracování textu v Azure Kognitivní hledání

*Analyzátor* je součást [fulltextového vyhledávacího stroje](search-lucene-query-architecture.md) zodpovědného za zpracování textu v řetězcích dotazů a indexovaných dokumentech. Různé analyzátory zpracovávají text různými způsoby v závislosti na scénáři. Analyzátory jazyka zpracovávají text pomocí lingvistických pravidel, aby se zlepšila kvalita hledání, zatímco jiné analyzátory provádějí více základních úloh, jako je například převod znaků na malá písmena. 

Nejčastěji se používají analyzátory jazyka a ke každému prohledávatelné poli v indexu služby Azure kognitivní hledání je přiřazen výchozí analyzátor jazyka. Následující transformace jazyka jsou typické při analýze textu:

+ Nepostradatelná slova (stopslova) a interpunkční znaménka se odeberou.
+ Fráze a slova s pomlčkami jsou rozdělená na součásti komponent.
+ Velká písmena jsou malá – použita.
+ Slova jsou zmenšena na kořenové formuláře, aby bylo možné najít shodu bez ohledu na vhodné.

Analyzátory jazyka převádějí textové zadání na primitivní nebo kořenové formuláře, které jsou efektivní pro ukládání a načítání informací. Konverze probíhá při indexování, při sestavování indexu a pak znovu při hledání při čtení indexu. Pokud pro obě operace použijete stejný analyzátor, budete pravděpodobně získávat očekávané výsledky hledání.

## <a name="default-analyzer"></a>Výchozí analyzátor  

Azure Kognitivní hledání používá jako výchozí výchozí nástroj [Apache Lucene Standard Analyzer (standardní Lucene)](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) , který přerušuje text na prvky, které následují jako pravidla [segmentace textu v kódu Unicode](https://unicode.org/reports/tr29/) . Standardní analyzátor navíc převede všechny znaky na jejich malý tvar Case. Indexované dokumenty a hledané výrazy procházejí analýzou během indexování a zpracování dotazů.  

Používá se automaticky pro každé prohledávatelné pole. Můžete přepsat výchozí hodnotu pro pole na základě pole. Alternativní analyzátory můžou být analyzátorem [jazyka](index-add-language-analyzers.md), [vlastní analyzátor](index-add-custom-analyzers.md)nebo předdefinovaný analyzátor ze [seznamu dostupných analyzátorů](index-add-custom-analyzers.md#AnalyzerTable).


## <a name="types-of-analyzers"></a>Typy analyzátorů

Následující seznam popisuje, které analyzátory jsou k dispozici v Azure Kognitivní hledání.

| Kategorie | Popis |
|----------|-------------|
| [Analyzátor standardního Lucene](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) | Default (Výchozí). Není nutná žádná specifikace nebo konfigurace. Tento obecný analyzátor je vhodný pro většinu jazyků a scénářů.|
| Předdefinované analyzátory | Nabízí se jako hotový produkt určený k použití tak, jak je. <br/>Existují dva typy: specializované a jazyk. Předdefinovaným způsobem je to, že na ně odkazujete podle názvu bez konfigurace nebo přizpůsobení. <br/><br/>[Speciální analyzátory (Language-nezávislá)](index-add-custom-analyzers.md#AnalyzerTable) se používají, když textové vstupy vyžadují specializované zpracování nebo minimální zpracování. Nejazykově předdefinované analyzátory obsahují **Asciifolding**, **klíčové slovo**, **vzor**, **jednoduché**, **zastavit**a **prázdné znaky**.<br/><br/>[Analyzátory jazyka](index-add-language-analyzers.md) se používají, když potřebujete bohatou jazykovou podporu pro jednotlivé jazyky. Azure Kognitivní hledání podporuje analyzátory jazyka 35 Lucene a 50 analyzátory zpracování přirozeného jazyka Microsoftu. |
|[Vlastní analyzátory](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search) | Odkazuje na uživatelsky definovanou konfiguraci kombinace stávajících prvků, která se skládá z jednoho provádějících tokenizaci (povinné) a volitelných filtrů (Char nebo token).|

Několik předdefinovaných analyzátorů, jako je například **vzor** nebo **zastavení**, podporuje omezené množství možností konfigurace. Pokud chcete tyto možnosti nastavit, můžete efektivně vytvořit vlastní analyzátor, který se skládá z předdefinovaného analyzátoru, a jednu z alternativních možností popsaných v části [předdefinované Reference k analyzátoru](index-add-custom-analyzers.md#AnalyzerTable). Stejně jako u libovolné vlastní konfigurace zadejte novou konfiguraci s názvem, například *myPatternAnalyzer* , abyste ji rozlišili od analyzátoru vzorů Lucene.

## <a name="how-to-specify-analyzers"></a>Postup určení analyzátorů

1. (pouze pro vlastní analyzátory) V definici indexu vytvořte oddíl s názvem **analyzátor** . Další informace najdete v tématech [vytvoření indexu](https://docs.microsoft.com/rest/api/searchservice/create-index) a také [Přidání vlastních analyzátorů](index-add-custom-analyzers.md).

2. V [definici pole](https://docs.microsoft.com/rest/api/searchservice/create-index) v indexu nastavte vlastnost **Analyzer** pole na název cílového analyzátoru (například `"analyzer" = "keyword"`. Platné hodnoty obsahují název předdefinovaného analyzátoru, analyzátoru jazyka nebo vlastního analyzátoru, který je také definován ve schématu indexu. Před vytvořením indexu ve službě Naplánujte přiřazení analyzátoru ve fázi definice indexu.

3. Volitelně můžete místo jedné vlastnosti **analyzátoru** nastavit různé analyzátory pro indexování a dotazování pomocí parametrů polí **indexAnalyzer** a **searchAnalyzer** . Pokud jedna z těchto aktivit vyžaduje určitou transformaci, která není vyžadována druhou, použijte pro přípravu a načítání dat různé analyzátory.

Přiřazení **analyzátoru** nebo **indexAnalyzer** k poli, které již bylo fyzicky vytvořeno, není povoleno. Pokud některý z těchto možností není jasný, přečtěte si následující tabulku, kde najdete rozpis akcí vyžadujících nové sestavení a proč.
 
 | Scénář | Dopad | Kroky |
 |----------|--------|-------|
 | Přidat nové pole | Poskytuje | Pokud pole ve schématu ještě neexistuje, neexistuje žádná revize pole, která by se dala provést, protože pole ještě nemá v indexu fyzickou přítomnost. Pomocí [aktualizačního indexu](https://docs.microsoft.com/rest/api/searchservice/update-index) můžete přidat nové pole do existujícího indexu a [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) ho naplnit.|
 | Přidejte **Analyzer** nebo **indexAnalyzer** do existujícího indexovaného pole. | [znovu sestavit](search-howto-reindex.md) | Obrácený index pro toto pole musí být od základu znovu vytvořen a obsah těchto polí musí být znovu indexován. <br/> <br/>V případě indexů v rámci aktivního vývoje [odstraňte](https://docs.microsoft.com/rest/api/searchservice/delete-index) a [vytvořte](https://docs.microsoft.com/rest/api/searchservice/create-index) index pro výběr definice nové pole. <br/> <br/>U indexů v produkčním prostředí můžete odložit opětovné sestavení tak, že vytvoříte nové pole pro zadání revidované definice a začnete ho používat místo staré. Pomocí [aktualizačního indexu](https://docs.microsoft.com/rest/api/searchservice/update-index) zahrňte nové pole a [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) k jeho naplnění. Později jako součást plánované údržby indexu můžete vyčistit index a odebrat tak zastaralá pole. |

## <a name="when-to-add-analyzers"></a>Kdy přidat analyzátory

Nejlepším časem přidávání a přiřazování analyzátorů je při aktivním vývoji při odstraňování a opětovném vytváření indexů.

Jako definice indexu solidifies můžete k indexu připojit nové analytické konstrukce, ale pokud se chcete vyhnout této chybě, budete muset předat příznak **allowIndexDowntime** , aby se [index aktualizoval](https://docs.microsoft.com/rest/api/searchservice/update-index) :

*"Aktualizace indexu není povolená, protože by to vedlo k výpadkům. Aby bylo možné přidat nové analyzátory, tokenizátory musíte nejdřív, filtry tokenů nebo filtry znaků do existujícího indexu, nastavte parametr dotazu ' allowIndexDowntime ' na hodnotu ' true ' v žádosti o aktualizaci indexu. Všimněte si, že při této operaci bude váš index po dobu nejméně pár sekund přepnut do režimu offline, což způsobí, že vaše požadavky na indexování a dotazy selžou. V případě, že se index aktualizuje nebo je delší pro hodně velkých indexů, může dojít k narušení výkonu a zápisu indexu do několika minut. "*

Totéž platí při přiřazování analyzátoru k poli. Analyzátor je nedílnou součástí definice pole, takže jej lze přidat pouze při vytvoření pole. Pokud chcete přidat analyzátory do existujících polí, budete muset index [vyřadit a znovu sestavit](search-howto-reindex.md) nebo přidat nové pole s analyzátorem, který chcete.

Jak je uvedeno, výjimka je **searchAnalyzer** variant. Tři způsoby, jak určit analyzátory (**Analyzer**, **indexAnalyzer**, **searchAnalyzer**), může změnit pouze atribut **searchAnalyzer** v existujícím poli.

## <a name="recommendations-for-working-with-analyzers"></a>Doporučení pro práci s analyzátory

Tato část nabízí Rady, jak pracovat s analyzátory.

### <a name="one-analyzer-for-read-write-unless-you-have-specific-requirements"></a>Jeden analyzátor pro čtení i zápis, pokud nemáte specifické požadavky

Azure Kognitivní hledání umožňuje zadat různé analyzátory pro indexování a hledání prostřednictvím dalších parametrů polí **indexAnalyzer** a **searchAnalyzer** . Pokud tento parametr nezadáte, použije se pro indexování i hledání sada analyzátorů s vlastností **Analyzer** . Pokud není zadaný `analyzer`, použije se výchozí standardní analyzátor Lucene.

Obecným pravidlem je použití stejného analyzátoru při indexování i dotazování, pokud konkrétní požadavky neurčí jinak. Ujistěte se, že důkladně otestujete. Když se zpracování textu liší při hledání a indexování, dojde ke spuštění rizika neshody mezi výrazy dotazu a indexovanými podmínkami, pokud nejsou konfigurace hledání a analyzátoru indexování zarovnané.

### <a name="test-during-active-development"></a>Testování během aktivního vývoje

Přepsání standardního analyzátoru vyžaduje opětovné sestavení indexu. Pokud je to možné, rozhodněte, které analyzátory se mají použít během aktivního vývoje, a teprve potom zavedete index do produkčního prostředí.

### <a name="inspect-tokenized-terms"></a>Zkontrolovat výrazy s tokeny

Pokud vyhledávání nevrátí očekávané výsledky, nejpravděpodobnějším scénářem jsou rozdíly v tokenech mezi vstupy v dotazu a termíny s tokeny v indexu. Pokud tokeny nejsou stejné, shody se vyhodnotit nezdařily. Pro kontrolu výstupu provádějících tokenizaci doporučujeme použít jako nástroj pro šetření [rozhraní API pro analýzu](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) . Odpověď se skládá z tokenů generovaných konkrétním analyzátorem.

<a name="examples"></a>

## <a name="rest-examples"></a>Příklady REST

Následující příklady znázorňují definice analyzátoru pro několik klíčových scénářů.

+ [Příklad vlastního analyzátoru](#Custom-analyzer-example)
+ [Příklad přiřazení analyzátorů k poli](#Per-field-analyzer-assignment-example)
+ [Kombinování analyzátorů pro indexování a hledání](#Mixing-analyzers-for-indexing-and-search-operations)
+ [Příklad analyzátoru jazyka](#Language-analyzer-example)

<a name="Custom-analyzer-example"></a>

### <a name="custom-analyzer-example"></a>Příklad vlastního analyzátoru

Tento příklad ukazuje definici analyzátoru s vlastními možnostmi. Vlastní možnosti pro filtry znaků, tokenizátory musíte nejdřív a filtry tokenů jsou zadány samostatně jako pojmenované konstrukce a následně odkazovány v definici analyzátoru. Předdefinované prvky se používají tak, jak jsou, a jednoduše odkazované názvem.

Procházení v tomto příkladu:

* Analyzátory jsou vlastností třídy pole pro pole, které lze prohledávat.
* Vlastní analyzátor je součástí definice indexu. Může být lehce přizpůsobená (například přizpůsobení jedné možnosti v jednom filtru) nebo přizpůsobená na více místech.
* V tomto případě je vlastní analyzátor "my_analyzer", který dále používá vlastní provádějících tokenizaci "my_standard_tokenizer" a dva filtry tokenů: malá písmena a přizpůsobení asciifolding filtru "my_asciifolding".
* Definuje také 2 vlastní filtry znaků "map_dash" a "remove_whitespace". První z nich nahrazuje všechny pomlčky podtržítkem a druhá z nich odstraní všechny mezery. V pravidlech mapování musí být mezery v kódování UTF-8. Filtry znaků jsou aplikovány před tokenizace a budou mít vliv na výsledné tokeny (standardní provádějících tokenizaci se dělí na pomlčky a mezery, ale ne na podtržítko).

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

### <a name="per-field-analyzer-assignment-example"></a>Příklad přiřazení analyzátoru pro každé pole

Výchozím nastavením je standardní analyzátor. Předpokládejme, že chcete výchozí hodnotu nahradit jiným předdefinovaným analyzátorem, jako je například analyzátor vzoru. Pokud nenastavujete vlastní možnosti, stačí v definici pole zadat jenom název.

Prvek "Analyzer" Přepisuje standardní analyzátor pro pole po jednotlivých polích. Neexistuje žádné globální přepsání. V tomto příkladu `text1` používá analyzátor vzorů a `text2`, které nespecifikují analyzátor, používá výchozí.

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

### <a name="mixing-analyzers-for-indexing-and-search-operations"></a>Kombinování analyzátorů pro operace indexování a vyhledávání

Rozhraní API obsahují další atributy indexu pro určení různých analyzátorů pro indexování a vyhledávání. Atributy **searchAnalyzer** a **indexAnalyzer** musí být zadány jako dvojice, přičemž nahrazuje atribut s jedním **analyzátorem** .


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

Pole obsahující řetězce v různých jazycích mohou používat analyzátor jazyka, zatímco jiná pole zachovají výchozí hodnotu (nebo používají jiný předdefinovaný nebo vlastní analyzátor). Použijete-li analyzátor jazyka, je nutné jej použít pro operace indexování i vyhledávání. Pole, která používají analyzátor jazyka, nemohou mít pro indexování a vyhledávání jiné analyzátory.

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

## <a name="c-examples"></a>C#4.6

Pokud používáte ukázky kódu .NET SDK, můžete tyto příklady připojit k použití nebo konfiguraci analyzátorů.

+ [Přiřazení integrovaného analyzátoru](#Assign-a-language-analyzer)
+ [Konfigurace analyzátoru](#Define-a-custom-analyzer)

<a name="Assign-a-language-analyzer"></a>

### <a name="assign-a-language-analyzer"></a>Přiřazení analyzátoru jazyka

V definici pole je zadaný jakýkoli analyzátor, který se používá, pokud není nastavená žádná konfigurace. Neexistuje žádný požadavek na vytvoření konstrukce analyzátoru. 

V tomto příkladu se přiřadí analyzátory Microsoft English a francouzština k polím Description. Je to fragment kódu pořízený z větší definice indexu hotelů a vytváření pomocí třídy hotelu v souboru hotels.cs ukázky [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) .

Vyvolejte [analyzátor](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzer?view=azure-dotnet), zadáním typu [deanalýza](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet) , který poskytuje analyzátor textu podporovaný v Azure kognitivní hledání.

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

Pokud se vyžaduje přizpůsobení nebo konfigurace, budete muset do indexu přidat konstrukci analyzátoru. Po definování můžete přidat definici pole, jak je znázorněno v předchozím příkladu.

Vytvořte objekt [CustomAnalyzer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.customanalyzer?view=azure-dotnet) . Další příklady naleznete v tématu [CustomAnalyzerTests.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Microsoft.Azure.Search/tests/Tests/CustomAnalyzerTests.cs).

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

+ Přečtěte si naše ucelené vysvětlení fungování [fulltextového vyhledávání v Azure kognitivní hledání](search-lucene-query-architecture.md). Tento článek používá příklady k vysvětlení chování, která se můžou na povrchu zdát na čítači.

+ Vyzkoušejte si další syntaxi dotazu v části ukázka [dokumentů](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples) nebo v tématu [Jednoduchá syntaxe dotazů](query-simple-syntax.md) v Průzkumníkovi služby Search na portálu.

+ Naučte se používat [lexikální analyzátory specifické pro jazyk](index-add-language-analyzers.md).

+ [Nakonfigurujte vlastní analyzátory](index-add-custom-analyzers.md) pro minimální zpracování nebo specializované zpracování na jednotlivých polích.

## <a name="see-also"></a>Další informace najdete v tématech

 [Hledat dokumenty REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents) 

 [Jednoduchá syntaxe dotazů](query-simple-syntax.md) 

 [Úplná syntaxe dotazů Lucene](query-lucene-syntax.md) 
 
 [Zpracování výsledků vyhledávání](search-pagination-page-layout.md)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png
