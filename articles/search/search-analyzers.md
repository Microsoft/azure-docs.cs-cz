---
title: Analyzátory pro jazykovou a zpracování textu – Azure Search
description: Přiřazení analyzátorům prohledávatelná textová pole v indexu nahradit výchozí standardní Lucene s alternativami vlastní, předdefinovaný nebo konkrétní jazyk.
services: search
ms.service: search
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: heidist
manager: cgronlun
author: HeidiSteen
ms.custom: seodec2018
ms.openlocfilehash: 008a24fe9822ca51b81e1f6979a3731d794a8867
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55964334"
---
# <a name="analyzers-for-text-processing-in-azure-search"></a>Analyzátory pro zpracování ve službě Azure Search textu

*Analyzátor* je součástí [fulltextové vyhledávání](search-lucene-query-architecture.md) zodpovědná za zpracování textu v řetězci dotazu a indexovaných dokumentů. Tyto transformace jsou typické během analýzy:

+ Bez nezbytné slova (stopword) a interpunkční znaménka se odeberou.
+ Fráze a slovech jsou rozdělené do součásti.
+ Slov velká písmena jsou malá malými a velkými písmeny.
+ Slova jsou zmenšeny na kořenové formulářů tak, aby bez ohledu na čas může být nalezena shoda.

Jazykové analyzátory převést textový vstup do primitivních nebo kořenový formuláře, které jsou efektivní informace o ukládání a načítání. Převod vyvolá se při indexování, při vytváření indexu a pak znovu během hledání, pokud je index pro čtení. Pravděpodobněji k získání požadovaných výsledků hledání očekáváte, že pokud použijete stejný analyzátor textu pro obě operace.

Používá služba Azure Search [analyzátor Lucene standardní](https://lucene.apache.org/core/4_0_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) jako výchozí. Můžete přepsat výchozí na základě pole pomocí pole. Tento článek popisuje škálu možností a nabízí osvědčené postupy pro vlastní analýza. Také poskytuje příklad konfigurace pro klíčové scénáře.

## <a name="supported-analyzers"></a>Podporované analyzátory

Následující seznam popisuje, jaké analyzátory jsou podporované ve službě Azure Search.

| Kategorie | Popis |
|----------|-------------|
| [Standardní analyzátor Lucene](https://lucene.apache.org/core/4_0_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) | Default (Výchozí). Vyžaduje se žádné specifikace nebo konfigurace. Tento analyzátor pro obecné účely provádí dobře pro většinu scénářů a jazyky.|
| Předdefinované analyzátory | Nabízí jak finální produkt je určen pro použití jako-je omezená vlastními změnami. <br/>Existují dva typy: specializované a jazyk. Co je kvůli tomu je "předdefinovaný" můžete odkazovat podle názvu se žádné přizpůsobení. <br/><br/>[Specializované (jazykově nezávislé) analyzátory](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search#AnalyzerTable) se používají při textovými vstupy vyžadují speciální zpracování a minimální zpracování. Zahrnout předdefinované non jazykové analyzátory **Asciifolding**, **– klíčové slovo**, **vzor**, **jednoduché**, **Zastavit**, **Prázdné znaky**.<br/><br/>[Analyzátory jazyka](https://docs.microsoft.com/rest/api/searchservice/language-support) se používají, když budete potřebovat bohatou jazykovou podporu pro jednotlivé jazyky. Služba Azure Search podporuje 35 Lucene jazykové analyzátory a 50 analyzátory zpracování přirozeného jazyka Microsoft. |
|[Vlastní analyzátory](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search) | Uživatelem definované konfigurace kombinaci stávající elementy skládající se z jednoho tokenizátor (povinné) a volitelné filtry (char nebo token).|

Můžete přizpůsobit předdefinovanou analyzátoru, jako **vzor** nebo **Zastavit**, použít alternativní možnosti uvedené v [předdefinovaný odkaz analyzátoru](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search#AnalyzerTable). Pouze několik předdefinovaných analyzátory máte možnosti, které můžete nastavit. Jak pomocí jakéhokoli přizpůsobení, zadejte nové konfigurace s názvem, jako třeba *myPatternAnalyzer* ho odlišuje od analyzátor Lucene vzor.

## <a name="how-to-specify-analyzers"></a>Určení analyzátory

1. (pro vlastní analyzátory jenom) Vytvoření **analyzátor** části v definici indexu. Další informace najdete v tématu [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) a také [vlastní analyzátory > vytvořit](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search#create-a-custom-analyzer).

2. Na [pole definice](https://docs.microsoft.com/rest/api/searchservice/create-index) v indexu, nastavte **analyzátor** vlastnost na název cílového analyzer (například `"analyzer" = "keyword"`. Platné hodnoty jsou název předdefinovaného analyzátoru, analyzátor jazyka nebo vlastní analyzátor také definováno ve schématu indexu.

3. Volitelně můžete místo jednoho **analyzátor** vlastnost můžete nastavit různé analyzátory pro indexování a dotazování pomocí **indexAnalyzer** a **searchAnalyzer "** pole Parametry. 

3. Analyzátor přidání definice pole pro s sebou nese náklady operace zápisu na index. Pokud chcete přidat **analyzátor** do existujícího indexu, mějte na paměti následující kroky:
 
 | Scénář | Dopad | Kroky |
 |----------|--------|-------|
 | Přidání nového pole | Minimální | Pokud pole ještě neexistuje ve schématu, neexistuje žádné pole revize provést, protože pole ještě nemá fyzickou přítomnost v indexu. Použití [aktualizaci indexu](https://docs.microsoft.com/rest/api/searchservice/update-index) a [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) pro tuto úlohu.|
 | Analyzátor přidání do existujícího indexovaného pole. | Opětovné sestavení | Obráceným index pro toto pole musí se znovu vytvořit zdola nahoru a musí být přeindexovány obsah těchto polí. <br/> <br/>Pro indexy aktivně vyvíjí [odstranit](https://docs.microsoft.com/rest/api/searchservice/delete-index) a [vytvořit](https://docs.microsoft.com/rest/api/searchservice/create-index) index ke sbírání nová definice pole. <br/> <br/>Pro indexy v produkčním prostředí měli byste vytvořit nové pole zadejte definici upravená a začít používat. Použití [aktualizaci indexu](https://docs.microsoft.com/rest/api/searchservice/update-index) a [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) začlenit nové pole. Později, v rámci index plánované údržby můžete vyčistit index odebrat zastaralé pole. |

## <a name="tips-and-best-practices"></a>Tipy a osvědčené postupy

Tato část nabízí Rady o tom, jak pracovat s analyzátory.

### <a name="one-analyzer-for-read-write-unless-you-have-specific-requirements"></a>Jeden analyzátor pro čtení i zápis Pokud nemáte konkrétní požadavky

Služba Azure Search umožňuje určit různé analyzátory pro indexování a vyhledávání prostřednictvím dalších `indexAnalyzer` a `searchAnalyzer` pole parametrů. Pokud tento parametr zadán, analyzátor sadu s `analyzer` vlastnost se používá pro indexování a vyhledávání. Pokud `analyzer` je tento parametr zadán, výchozí analyzátor Lucene standardní se používá.

Obecně je použití stejné analyzátoru pro indexování a dotazování, není-li konkrétní požadavky stanovují jinak. Třeba důkladně otestovat. Při zpracování textu se liší v hledání a indexování čas, vystavujete se riziku Neshoda mezi indexované termínů, pokud nejsou zarovnány hledání a indexování analyzátor konfigurace a dotaz.

### <a name="test-during-active-development"></a>Test při aktivním vývoji

Přepsání standardního analyzátor vyžaduje opětovné sestavení indexu. Rozhodněte, pokud je to možné, na které analyzátory pro použití při aktivním vývoji, před distribucí indexu do produkčního prostředí.

### <a name="inspect-tokenized-terms"></a>Kontrola tokenizovaná podmínky

Pokud se hledání nezdaří vrátit očekávané výsledky, je nejpravděpodobnější scénář token nesrovnalosti mezi termín vstupů v dotazu a tokenizovaná podmínky v indexu. Pokud tokeny nejsou stejné, odpovídá nepodaří sloučit. Chcete-li prověřit tokenizátor výstup, doporučujeme použít [analyzovat rozhraní API](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) jako nástroj pro zkoumání. Odpověď se skládá z tokenů, generovaná konkrétní Analyzer.

### <a name="compare-english-analyzers"></a>Porovnání anglické analyzátory

[Ukázka analyzátoru hledání](https://alice.unearth.ai/) je třetí strany ukázkovou aplikaci ukazující srovnání vedle sebe standardní analyzátor Lucene, analyzátor Lucene pro angličtinu a procesor anglické přirozeného jazyka od Microsoftu. Index je pevná; obsahuje text z oblíbených scénáře. Pro každý vstupní hledání zadáte, výsledky z každé analyzátoru jsou zobrazeny v sousední podoken, získáte tak představu o způsob, jak každý analyzátor zpracuje do jednoho řetězce. 

## <a name="examples"></a>Příklady

Následující příklady ukazují analyzátor definice pro několik klíčových scénářů.

<a name="Example1"></a>
### <a name="example-1-custom-options"></a>Příklad 1: Vlastní možnosti

Tento příklad ukazuje definici analyzátoru s vlastními možnostmi. Vlastní možnosti pro filtry char, tokenizátory a token filtry se zadávají samostatně jako pojmenované konstrukcí a pak odkazuje v definici analyzátor. Předdefinované elementy se používají jako-je a jednoduše odkazovat podle názvu.

Provede v tomto příkladu:

* Analyzátory jsou vlastnosti třídy pole pro prohledávatelné pole.
* Vlastní analyzátor je součástí definice indexu. Pravděpodobně je lehce přizpůsobit (například přizpůsobení jednu možnost v jednom filtru) nebo přizpůsobit na více místech.
* V takovém případě je vlastní analyzátor "my_analyzer", která dále používá vlastní standardní tokenizátor "my_standard_tokenizer" a dva filtry token: malá písmena a přizpůsobené asciifolding filtr "my_asciifolding".
* Definuje také 2 vlastní char filtry "map_dash" a "remove_whitespace". První z nich nahradí všechny pomlčky podtržítky, zatímco druhá odebere všechny mezery. Mezery musí být v pravidlech mapování kódování UTF-8. Char filtry se použijí před Tokenizace a bude mít vliv na použitím výsledných tokenů (standardní tokenizátor zalomení na pomlčky a mezery, ale ne na podtržítko).

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

<a name="Example2"></a>
### <a name="example-2-override-the-default-analyzer"></a>Příklad 2: Přepsat výchozí analyzátor

Výchozím nastavením je standardní analyzátor. Předpokládejme, že budete chtít nahradit výchozí pomocí různých předdefinovaných analyzátoru, jako je model analyzátoru. Pokud si nejste možnosti vlastního nastavení, stačí určit podle názvu v definici pole.

Prvek "analyzátor" přepisuje standardní analyzátor na základě pole pomocí pole. Neexistuje žádné globální přepsání. V tomto příkladu `text1` používá model analyzátoru a `text2`, která neurčuje analyzátor, používá výchozí.

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

<a name="Example3"></a>
### <a name="example-3-different-analyzers-for-indexing-and-search-operations"></a>Příklad 3: Různé analyzátory pro operace indexování a vyhledávání

Rozhraní API obsahuje další index atributy pro určení jiné analyzátory pro indexování a vyhledávání. `searchAnalyzer` a `indexAnalyzer` atributy musí být zadán jako dvojice, nahrazení jedné `analyzer` atribut.


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

<a name="Example4"></a>
### <a name="example-4-language-analyzer"></a>Příklad 4: Analyzátor jazyka

Pole obsahující řetězce v různých jazycích můžete použít analyzátor jazyka, zatímco ostatní pole ponechte výchozí hodnotu (nebo pomocí některé jiné předdefinovaným nebo vlastním analyzátoru). Pokud používáte analyzátor jazyka, musí být použit pro operace indexování a vyhledávání. Pole, která lze použít analyzátor jazyka nemůže mít různé analyzátory pro indexování a vyhledávání.

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

## <a name="next-steps"></a>Další postup

+ Projděte si naše komplexní vysvětlení [jak funguje fulltextové vyhledávání ve službě Azure Search](search-lucene-query-architecture.md). V tomto článku se používají příklady který vysvětluje chování, která se může zdát counter-intuitive na povrchu.

+ Zkuste syntaxi dalšího dotazu [vyhledávání dokumentů](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples) vzorový oddíl nebo z [jednoduchá syntaxe dotazů](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) v Průzkumníku služby Search na portálu.

+ Zjistěte, jak použít [specifické pro jazyk lexikální analyzátory](https://docs.microsoft.com/rest/api/searchservice/language-support).

+ [Konfigurace vlastní analyzátory](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search) pro minimální zpracování nebo speciální zpracování pro jednotlivá pole.

+ [Porovnat standardní a angličtině analyzátory](https://alice.unearth.ai/) v sousední podokna na tomto webu ukázku. 

## <a name="see-also"></a>Další informace najdete v tématech

 [Hledání dokumentů rozhraní REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents) 

 [Jednoduchá syntaxe dotazů](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 

 [Úplná syntaxe dotazů Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 
 
 [Zpracování výsledků vyhledávání](https://docs.microsoft.com/azure/search/search-pagination-page-layout)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png
