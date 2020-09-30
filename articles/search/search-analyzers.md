---
title: Analyzátory pro jazyk a zpracování textu
titleSuffix: Azure Cognitive Search
description: Přiřaďte analyzátory k prohledávatelným textovým polím v indexu, abyste nahradili výchozí standardní řešení Lucene vlastními, předdefinovanými nebo pro konkrétní jazyky.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/20/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 01b57526b15a806271d58b250f06a4372fe56b72
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91532257"
---
# <a name="analyzers-for-text-processing-in-azure-cognitive-search"></a>Analyzátory pro zpracování textu v Azure Kognitivní hledání

*Analyzátor* je součást [fulltextového vyhledávacího stroje](search-lucene-query-architecture.md) zodpovědného za zpracování textu v řetězcích dotazů a indexovaných dokumentech. Zpracování textu (označované také jako lexikální analýza) je transformativní a úprava řetězce prostřednictvím následujících akcí:

+ Odebrat nepostradatelná slova (stopslova) a interpunkční znaménka
+ Rozdělení frází a dělení slov na části komponent
+ Malá písmena v malých a velkých slovech
+ Omezte slova na primitivní kořenové formuláře pro efektivitu úložiště, takže shody se dají najít bez ohledu na vhodnéu.

Analýza se vztahuje na `Edm.String` pole, která jsou označená jako "prohledávatelné", což indikuje fulltextové vyhledávání. V případě polí s touto konfigurací dojde k analýze během indexování při vytváření tokenů a pak znovu při provádění dotazu při analýze dotazů a modul hledá shodné tokeny. K výskytu shody dochází v případě, že se stejný analyzátor používá pro indexování i pro dotazy, ale můžete nastavit analyzátor pro každou úlohu nezávisle na závislosti na vašich požadavcích.

Typy dotazů, které nejsou fulltextovým vyhledáváním, jako například regulární výraz nebo přibližné vyhledávání, neprojde fází analýzy na straně dotazu. Místo toho analyzátor odesílá tyto řetězce přímo do vyhledávacího modulu pomocí vzoru, který zadáte jako základ pro shodu. Tyto formuláře dotazů obvykle vyžadují tokeny celého řetězce, aby bylo možné provést porovnávání vzorů. Pokud chcete během indexování získat tokeny s úplnými výrazy, možná budete potřebovat [vlastní analyzátory](index-add-custom-analyzers.md). Další informace o tom, kdy a proč jsou výrazy dotazu analyzovány, najdete v tématu [fulltextové vyhledávání v Azure kognitivní hledání](search-lucene-query-architecture.md).

Další informace o lexikální analýze získáte v následujícím videoklipu, kde najdete stručné vysvětlení.

> [!VIDEO https://www.youtube.com/embed/Y_X6USgvB1g?version=3&start=132&end=189]

## <a name="default-analyzer"></a>Výchozí analyzátor  

V Azure Kognitivní hledání dotazy se analyzátor automaticky vyvolá na všechna pole řetězců označená jako prohledávatelné. 

Ve výchozím nastavení používá Azure Kognitivní hledání [standardní analyzátor Apache Lucene (standardní Lucene)](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html), který přerušuje text na prvky podle pravidel ["segmentace textu v Unicode"](https://unicode.org/reports/tr29/) . Standardní analyzátor navíc převede všechny znaky na jejich malý tvar Case. Indexované dokumenty a hledané výrazy procházejí analýzou během indexování a zpracování dotazů.  

Můžete přepsat výchozí hodnotu pro pole na základě pole. Alternativní analyzátory můžou být [analyzátorem jazyka](index-add-language-analyzers.md) pro jazykové zpracování, [vlastní analyzátor](index-add-custom-analyzers.md)nebo předdefinovaný analyzátor ze [seznamu dostupných analyzátorů](index-add-custom-analyzers.md#AnalyzerTable).

## <a name="types-of-analyzers"></a>Typy analyzátorů

Následující seznam popisuje, které analyzátory jsou k dispozici v Azure Kognitivní hledání.

| Kategorie | Popis |
|----------|-------------|
| [Analyzátor standardního Lucene](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) | Default (Výchozí). Není nutná žádná specifikace nebo konfigurace. Tento obecný analyzátor je vhodný pro mnoho jazyků a scénářů.|
| Předdefinované analyzátory | Nabízí se jako hotový produkt určený k použití tak, jak je. <br/>Existují dva typy: specializované a jazyk. Předdefinovaným způsobem je to, že na ně odkazujete podle názvu bez konfigurace nebo přizpůsobení. <br/><br/>[Speciální analyzátory (Language-nezávislá)](index-add-custom-analyzers.md#AnalyzerTable) se používají, když textové vstupy vyžadují specializované zpracování nebo minimální zpracování. Nejazykově předdefinované analyzátory obsahují **Asciifolding**, **klíčové slovo**, **vzor**, **jednoduché**, **zastavit**a **prázdné znaky**.<br/><br/>[Analyzátory jazyka](index-add-language-analyzers.md) se používají, když potřebujete bohatou jazykovou podporu pro jednotlivé jazyky. Azure Kognitivní hledání podporuje analyzátory jazyka 35 Lucene a 50 analyzátory zpracování přirozeného jazyka Microsoftu. |
|[Vlastní analyzátory](/rest/api/searchservice/Custom-analyzers-in-Azure-Search) | Odkazuje na uživatelsky definovanou konfiguraci kombinace stávajících prvků, která se skládá z jednoho provádějících tokenizaci (povinné) a volitelných filtrů (Char nebo token).|

Několik předdefinovaných analyzátorů, jako je například **vzor** nebo **zastavení**, podporuje omezené množství možností konfigurace. Pokud chcete tyto možnosti nastavit, můžete efektivně vytvořit vlastní analyzátor, který se skládá z předdefinovaného analyzátoru, a jednu z alternativních možností popsaných v části [předdefinované Reference k analyzátoru](index-add-custom-analyzers.md#AnalyzerTable). Stejně jako u libovolné vlastní konfigurace zadejte novou konfiguraci s názvem, například *myPatternAnalyzer* , abyste ji rozlišili od analyzátoru vzorů Lucene.

## <a name="how-to-specify-analyzers"></a>Postup určení analyzátorů

Nastavení analyzátoru je volitelné. Jako obecné pravidlo zkuste nejprve použít výchozí standardní analyzátor Lucene, abyste viděli, jak to funguje. Pokud se dotazům nepodaří vracet očekávané výsledky, je často to správné řešení přepnutí na jiný analyzátor.

1. Při vytváření definice pole v [indexu](/rest/api/searchservice/create-index)nastavte vlastnost  **Analyzer** na jednu z následujících možností: [předdefinovaný analyzátor](index-add-custom-analyzers.md#AnalyzerTable) , jako je `keyword` například [analyzátor jazyka](index-add-language-analyzers.md) , například `en.microsoft` , nebo vlastní analyzátor (definovaný ve stejném schématu indexu).  
 
   ```json
     "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": "en.microsoft",
      "indexAnalyzer": null,
      "searchAnalyzer": null
    },
   ```

   Používáte-li [analyzátor jazyka](index-add-language-analyzers.md), je nutné ji zadat pomocí vlastnosti **Analyzer** . Vlastnosti **searchAnalyzer** a **indexAnalyzer** nepodporují analyzátory jazyků.

1. Případně nastavte **indexAnalyzer** a **searchAnalyzer** pro změnu analyzátoru pro jednotlivé úlohy. Tyto vlastnosti jsou nastaveny společně a nahrazují vlastnost **analyzátoru** , která musí mít hodnotu null. Můžete použít různé analyzátory pro přípravu a načítání dat, pokud jedna z těchto aktivit vyžaduje určitou transformaci, kterou druhý nepotřebuje.

   ```json
     "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": null,
      "indexAnalyzer": "keyword",
      "searchAnalyzer": "whitespace"
    },
   ```

1. Pouze vlastní analyzátory vytvořte záznam v části **[analyzátory]** v indexu a potom přiřaďte vlastní analyzátor k definici pole podle kteréhokoli z předchozích dvou kroků. Další informace najdete v tématech [vytvoření indexu](/rest/api/searchservice/create-index) a také [Přidání vlastních analyzátorů](index-add-custom-analyzers.md).

## <a name="when-to-add-analyzers"></a>Kdy přidat analyzátory

Nejlepším časem přidávání a přiřazování analyzátorů je při aktivním vývoji při odstraňování a opětovném vytváření indexů.

Vzhledem k tomu, že analyzátory slouží k tokenizovatí podmínek, měli byste při vytváření pole přiřadit analyzátor. Skutečnost, že přiřazení **analyzátoru** nebo **indexAnalyzer** k poli, které již bylo fyzicky vytvořeno, není povoleno (i když vlastnost **searchAnalyzer** můžete kdykoli změnit, aniž by to mělo vliv na index).

Chcete-li změnit analyzátor stávajícího pole, bude nutné [znovu sestavit index úplně](search-howto-reindex.md) (jednotlivá pole nemůžete znovu sestavit). U indexů v produkčním prostředí můžete znovu odložit vytvořením nového pole s novým přiřazením analyzátoru a začít ho používat místo starého. Pomocí [aktualizačního indexu](/rest/api/searchservice/update-index) zahrňte nové pole a [mergeOrUpload](/rest/api/searchservice/addupdate-or-delete-documents) k jeho naplnění. Později jako součást plánované údržby indexu můžete vyčistit index a odebrat tak zastaralá pole.

Chcete-li přidat nové pole do existujícího indexu, přidejte do [indexu aktualizace](/rest/api/searchservice/update-index) volání pole a [mergeOrUpload](/rest/api/searchservice/addupdate-or-delete-documents) jej naplňte.

Chcete-li přidat vlastní analyzátor do existujícího indexu, předejte příznak **allowIndexDowntime** v [indexu aktualizace](/rest/api/searchservice/update-index) , pokud se chcete vyhnout této chybě:

*"Aktualizace indexu není povolená, protože by to vedlo k výpadkům. Aby bylo možné přidat nové analyzátory, tokenizátory musíte nejdřív, filtry tokenů nebo filtry znaků do existujícího indexu, nastavte parametr dotazu ' allowIndexDowntime ' na hodnotu ' true ' v žádosti o aktualizaci indexu. Všimněte si, že při této operaci bude váš index po dobu nejméně pár sekund přepnut do režimu offline, což způsobí, že vaše požadavky na indexování a dotazy selžou. V případě, že se index aktualizuje nebo je delší pro hodně velkých indexů, může dojít k narušení výkonu a zápisu indexu do několika minut. "*

## <a name="recommendations-for-working-with-analyzers"></a>Doporučení pro práci s analyzátory

Tato část nabízí Rady, jak pracovat s analyzátory.

### <a name="one-analyzer-for-read-write-unless-you-have-specific-requirements"></a>Jeden analyzátor pro čtení i zápis, pokud nemáte specifické požadavky

Azure Kognitivní hledání umožňuje zadat různé analyzátory pro indexování a hledání prostřednictvím dalších vlastností polí **indexAnalyzer** a **searchAnalyzer** . Pokud tento parametr nezadáte, použije se pro indexování i hledání sada analyzátorů s vlastností **Analyzer** . Pokud není parametr **Analyzer** zadán, použije se výchozí standardní analyzátor Lucene.

Obecným pravidlem je použití stejného analyzátoru při indexování i dotazování, pokud konkrétní požadavky neurčí jinak. Ujistěte se, že důkladně otestujete. Když se zpracování textu liší při hledání a indexování, dojde ke spuštění rizika neshody mezi výrazy dotazu a indexovanými podmínkami, pokud nejsou konfigurace hledání a analyzátoru indexování zarovnané.

### <a name="test-during-active-development"></a>Testování během aktivního vývoje

Přepsání standardního analyzátoru vyžaduje opětovné sestavení indexu. Pokud je to možné, rozhodněte, které analyzátory se mají použít během aktivního vývoje, a teprve potom zavedete index do produkčního prostředí.

### <a name="inspect-tokenized-terms"></a>Zkontrolovat výrazy s tokeny

Pokud vyhledávání nevrátí očekávané výsledky, nejpravděpodobnějším scénářem jsou rozdíly v tokenech mezi vstupy v dotazu a termíny s tokeny v indexu. Pokud tokeny nejsou stejné, shody se vyhodnotit nezdařily. Pro kontrolu výstupu provádějících tokenizaci doporučujeme použít jako nástroj pro šetření [rozhraní API pro analýzu](/rest/api/searchservice/test-analyzer) . Odpověď se skládá z tokenů generovaných konkrétním analyzátorem.

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
* V tomto případě je vlastní analyzátor "my_analyzer", který zase používá přizpůsobené standardní provádějících tokenizaci "my_standard_tokenizer" a dva filtry tokenů: malá písmena a přizpůsobený asciifolding filtr "my_asciifolding".
* Definuje také 2 vlastní filtry znaků "map_dash" a "remove_whitespace". První z nich nahrazuje všechny pomlčky podtržítkem a druhá z nich odstraní všechny mezery. V pravidlech mapování musí být mezery v kódování UTF-8. Filtry znaků jsou aplikovány před tokenizace a budou mít vliv na výsledné tokeny (standardní provádějících tokenizaci se dělí na pomlčky a mezery, ale ne na podtržítko).

```json
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
```

<a name="Per-field-analyzer-assignment-example"></a>

### <a name="per-field-analyzer-assignment-example"></a>Příklad přiřazení analyzátoru pro každé pole

Výchozím nastavením je standardní analyzátor. Předpokládejme, že chcete výchozí hodnotu nahradit jiným předdefinovaným analyzátorem, jako je například analyzátor vzoru. Pokud nenastavujete vlastní možnosti, stačí v definici pole zadat jenom název.

Prvek "Analyzer" Přepisuje standardní analyzátor pro pole po jednotlivých polích. Neexistuje žádné globální přepsání. V tomto příkladu `text1` používá analyzátor vzorů a `text2` , který neurčuje analyzátor, používá výchozí.

```json
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
```

<a name="Mixing-analyzers-for-indexing-and-search-operations"></a>

### <a name="mixing-analyzers-for-indexing-and-search-operations"></a>Kombinování analyzátorů pro operace indexování a vyhledávání

Rozhraní API obsahují další atributy indexu pro určení různých analyzátorů pro indexování a vyhledávání. Atributy **searchAnalyzer** a **indexAnalyzer** musí být zadány jako dvojice, přičemž nahrazuje atribut s jedním **analyzátorem** .


```json
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
```

<a name="Language-analyzer-example"></a>

### <a name="language-analyzer-example"></a>Příklad analyzátoru jazyka

Pole obsahující řetězce v různých jazycích mohou používat analyzátor jazyka, zatímco jiná pole zachovají výchozí hodnotu (nebo používají jiný předdefinovaný nebo vlastní analyzátor). Použijete-li analyzátor jazyka, je nutné jej použít pro operace indexování i vyhledávání. Pole, která používají analyzátor jazyka, nemohou mít pro indexování a vyhledávání jiné analyzátory.

```json
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
```

## <a name="c-examples"></a>Příklady jazyka C#

Pokud používáte ukázky kódu .NET SDK, můžete tyto příklady připojit k použití nebo konfiguraci analyzátorů.

+ [Přiřazení integrovaného analyzátoru](#Assign-a-language-analyzer)
+ [Konfigurace analyzátoru](#Define-a-custom-analyzer)

<a name="Assign-a-language-analyzer"></a>

### <a name="assign-a-language-analyzer"></a>Přiřazení analyzátoru jazyka

V definici pole je zadaný jakýkoli analyzátor, který se používá, pokud není nastavená žádná konfigurace. Neexistuje žádný požadavek na vytvoření položky v části **[Analyzer]** v indexu. 

V tomto příkladu se přiřadí analyzátory Microsoft English a francouzština k polím Description. Je to fragment kódu pořízený z větší definice indexu hotelů a vytváření pomocí třídy hotelu v souboru hotels.cs ukázky [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) .

Vyvolejte [analyzátor](/dotnet/api/microsoft.azure.search.models.analyzer), zadáním typu [deanalýza](/dotnet/api/microsoft.azure.search.models.analyzername) , který poskytuje analyzátor textu podporovaný v Azure kognitivní hledání.

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

Vytvořte objekt [CustomAnalyzer](/dotnet/api/microsoft.azure.search.models.customanalyzer) . Další příklady naleznete v tématu [CustomAnalyzerTests.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Microsoft.Azure.Search/tests/Tests/CustomAnalyzerTests.cs).

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

+ Vyzkoušejte si další syntaxi dotazu v části ukázka [dokumentů](/rest/api/searchservice/search-documents#bkmk_examples) nebo v tématu [Jednoduchá syntaxe dotazů](query-simple-syntax.md) v Průzkumníkovi služby Search na portálu.

+ Naučte se používat [lexikální analyzátory specifické pro jazyk](index-add-language-analyzers.md).

+ [Nakonfigurujte vlastní analyzátory](index-add-custom-analyzers.md) pro minimální zpracování nebo specializované zpracování na jednotlivých polích.

## <a name="see-also"></a>Viz také

 [Rozhraní API pro vyhledávání v dokumentech](/rest/api/searchservice/search-documents) 

 [Jednoduchá syntaxe dotazů](query-simple-syntax.md) 

 [Úplná syntaxe dotazů Lucene](query-lucene-syntax.md) 
 
 [Zpracování výsledků vyhledávání](search-pagination-page-layout.md)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png