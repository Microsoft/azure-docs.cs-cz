---
title: Analyzátory ve službě Azure Search | Microsoft Docs
description: Přiřazení analyzátorům prohledávatelné textových polí v indexu nahradit výchozí standardní Lucene s alternativami vlastní, předdefinované nebo konkrétní jazyk.
services: search
ms.service: search
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: heidist
manager: cgronlun
author: HeidiSteen
ms.openlocfilehash: e858966fb5a15b84af1952399a5eff3ca50d0d59
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
ms.locfileid: "31795693"
---
# <a name="analyzers-in-azure-search"></a>Analyzátory ve službě Azure Search

*Analyzátor* je součástí [fulltextové vyhledávání](search-lucene-query-architecture.md) zodpovědná za zpracování textu v řetězců dotazů a indexované dokumenty. Následující transformace jsou typické během analýzy:

+ Není nezbytné slova (stopslova) a interpunkce se odeberou.
+ Fráze a rozděleným slova jsou rozdělené podle součásti.
+ Velká slova jsou použita nižší.
+ Slova jsou omezeny na kořenové formuláře tak, aby bez ohledu na to slovesného času může být nalezena shoda.

Převést lingvistické analyzátorů textový vstup na primitivní nebo kořenové formuláře, které jsou efektivní informace o ukládání a načítání. Převod dojde během indexování, když je sestavena index a pak znovu při hledání, pokud je pro index čtení. Se pravděpodobně získat výsledky hledání očekáváte, že pokud použijete stejný text analyzátor pro obě operace.

Služba Azure Search používá [standardní Lucene analyzátor](https://lucene.apache.org/core/4_0_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) jako výchozí. Můžete přepsat výchozí na základě pole pole. Tento článek popisuje rozsah hodnot a nabízí osvědčené postupy pro vlastní analýzu. Také poskytuje příklad konfigurace pro klíčové scénáře.

## <a name="supported-analyzers"></a>Podporované analyzátory

Následující seznam popisuje, jaké analyzátorů jsou podporovány ve službě Azure Search.

| Kategorie | Popis |
|----------|-------------|
| [Standardní analyzátor Lucene](https://lucene.apache.org/core/4_0_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) | Výchozí hodnota. Není potřeba žádná specifikace nebo konfigurace. Tento pro obecné účely analyzátor provede vhodné pro většinu jazyků a scénáře.|
| Předdefinované analyzátory | Nabízí tak, jak má produkt dokončení má být použit jako-je s omezenou přizpůsobení. <br/>Existují dva typy: specializované a jazyk. Díky je "předdefinované" je odkaz je podle názvu s neexistuje vlastní nastavení. <br/><br/>[Specializuje (bez ohledu na jazyk) analyzátorů](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search#AnalyzerTable) se používají, když text vstupy vyžadují speciální zpracování nebo minimální zpracování. Zahrnout předdefinované analyzátory non jazyka **Asciifolding**, **– klíčové slovo**, **vzor**, **jednoduché**, **Zastavit**, **Prázdné**.<br/><br/>[Analyzátory jazyka](https://docs.microsoft.com/rest/api/searchservice/language-support) se používají, když potřebujete bohaté jazykovou podporu pro jednotlivé jazyky. Služba Azure Search podporuje 35 Lucene analyzátory jazyka a 50 analyzátorů zpracování přirozeného jazyka Microsoft. |
|[Vlastní analyzátory](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search) | Uživatelem definované konfigurace kombinaci existující prvky, který se skládá z jednoho tokenizátor (povinné) a volitelné filtry (char nebo token).|

Můžete přizpůsobit předdefinované analyzátor, jako například **vzor** nebo **Zastavit**, použít alternativní možnosti, které jsou dokumentovány v článku [předdefinované analyzátor odkazu](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search#AnalyzerTable). Pouze několik předdefinovaných analyzátorů mají možností, které můžete nastavit. Jak se všechny vlastní nastavení, zadejte nové konfigurace s názvem, jako třeba *myPatternAnalyzer* ho odlišuje od Lucene vzor analyzátor.

## <a name="how-to-specify-analyzers"></a>Určení analyzátory

1. (pro vlastní analyzátorů pouze) Vytvoření **analyzátor** část v definici indexu. Další informace najdete v tématu [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) a také [vlastní analyzátorů > vytvořit](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search#create-a-custom-analyzer).

2. Na [pole definice](https://docs.microsoft.com/rest/api/searchservice/create-index) v indexu, nastavte **analyzátor** vlastnost na název cílového analyzer (například `"analyzer" = "keyword"`. Platné hodnoty patří název předdefinované analyzátor, analyzátor jazyka nebo vlastní analyzátor rovněž definovaný ve schématu indexu.

3. Volitelně můžete místo jeden **analyzátor** vlastnost můžete nastavit různé analyzátory pro indexování a dotazování pomocí **indexAnalyzer** a **searchAnalyzer'** pole Parametry. 

3. Přidání analyzátor do definice pole způsobuje operaci zápisu na index. Pokud přidáte **analyzátor** do existujícího indexu, Všimněte si následujících kroků:
 
 | Scénář | Dopad | Kroky |
 |----------|--------|-------|
 | Přidat nové pole | minimální | Pokud pole ještě neexistuje ve schématu, neexistuje žádná pole revize pro provést, protože pole ještě nemá fyzickou přítomnost v indexu. Použití [aktualizace indexu](https://docs.microsoft.com/rest/api/searchservice/update-index) a [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) pro tuto úlohu.|
 | Přidáte analyzátor na stávající indexovaného pole. | Opětovné sestavení | Převedený index pro toto pole je nutné znovu vytvořit od základů nahoru a obsah těchto polí musí být přeindexovány. <br/> <br/>Pro indexy active vyvíjené [odstranit](https://docs.microsoft.com/rest/api/searchservice/delete-index) a [vytvořit](https://docs.microsoft.com/rest/api/searchservice/create-index) index pro vyzvednutí novou definici pole. <br/> <br/>Pro indexy v produkčním prostředí měli byste vytvořit nové pole a zadejte definici revidovaný jej začít používat. Použití [aktualizace indexu](https://docs.microsoft.com/rest/api/searchservice/update-index) a [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) začlenit nové pole. Později v rámci obsluhy plánované index vyčistěte index odebrat zastaralé pole. |

## <a name="tips-and-best-practices"></a>Tipy a osvědčené postupy

Tato část nabízí Rady, jak pracovat s analyzátorů.

### <a name="one-analyzer-for-read-write-unless-you-have-specific-requirements"></a>Jeden analyzátor pro čtení a zápis Pokud nemáte konkrétní požadavky

Služba Azure Search umožňuje určit různé analyzátory pro indexování a hledání prostřednictvím další `indexAnalyzer` a `searchAnalyzer` pole parametrů. Pokud tento parametr zadán, analyzátor nastavit `analyzer` vlastnost se používá pro indexování a vyhledávání. Pokud `analyzer` je tento parametr nezadáte, analyzátor standardní Lucene výchozí se používá.

Obecným pravidlem je použít stejné analyzátor pro indexování i dotazování, není-li konkrétní požadavky nevyzvou jinak. Ujistěte se, že jste důkladně otestovat. Při zpracování textu se liší v hledání a indexování čas, spustíte riziko neshody mezi indexované termínů, pokud nejsou zarovnány vyhledávání a indexování analyzátor konfigurace a dotazu.

### <a name="test-during-active-development"></a>Testování během vývoje active

Přepsání standardního analyzátor vyžaduje opětovné sestavení indexu. Rozhodněte, pokud je to možné, na které analyzátorů používat během active vývoje, před distribucí indexu v produkčním prostředí.

### <a name="inspect-tokenized-terms"></a>Zkontrolujte tokenizovaná podmínky

Pokud se hledání nezdaří vracet očekávané výsledky, je nejpravděpodobnější scénář tokenu nesrovnalostí mezi termín vstupů v dotazu a tokenizovaná podmínky v indexu. Pokud tokeny nejsou stejné, shoduje se nepodařilo vyhodnotit. Chcete-li prověřit tokenizátor výstup, doporučujeme používat [analyzovat rozhraní API](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) jako nástroj šetření. Odpověď se skládá z tokenů, generovaná konkrétní analyzátor.

### <a name="compare-english-analyzers"></a>Porovnání anglické analyzátory

[Vyhledávání analyzátor ukázku](http://alice.unearth.ai/) je třetí strany ukázkové aplikace zobrazuje vedle sebe porovnání standardní analyzátor Lucene, Lucene na anglickém jazyce analyzátor a procesor anglické přirozeného jazyka společnosti Microsoft. Index vyřešen; obsahuje text z oblíbených scénáře. Pro každý vstupní hledání zadáte, výsledky z každé analyzátor jsou zobrazeny v sousedících podokna budete představu o tom, jak každý analyzátor zpracovává do jednoho řetězce. 

## <a name="examples"></a>Příklady

Následující příklady ukazují definice analyzátor pro několik klíčových scénářů.

<a name="Example1"></a>
### <a name="example-1-custom-options"></a>Příklad 1: Vlastní možnosti

Tento příklad ukazuje definici analyzátor s vlastní možnosti. Vlastní možnosti pro filtry char, tokenizers a tokenu filtry se zadávají samostatně jako s názvem konstrukce a pak odkazuje v definici analyzátor. Předdefinované elementy jsou použity jako-je a jednoduše odkazuje jeho názvem.

Tento příklad s návodem:

* Analyzátory jsou vlastností třídy pole pro prohledávatelné pole.
* Vlastní analyzátor je součástí definice indexu. Mohl být lehce přizpůsobené (např. přizpůsobení jednu možnost v jeden filtr) nebo přizpůsobit na více místech.
* V takovém případě je vlastní analyzátor "my_analyzer", které dále používá vlastní standardní tokenizátor "my_standard_tokenizer" a dva filtry tokenu: malá písmena a přizpůsobit asciifolding filtru "my_asciifolding".
* Definuje také filtr char vlastní "map_dash" nahradit všechny pomlčky podtržítka před tokenizaci (standardní tokenizátor zalomení na dash, ale ne na podtržítko).

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
              "map_dash"
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
        }
     ],
     "tokenizers":[
        {
           "name":"my_standard_tokenizer",
           "@odata.type":"#Microsoft.Azure.Search.StandardTokenizer",
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

Standardní Analyzátor je výchozí. Předpokládejme, že chcete nahradit výchozí různých předdefinovaných analyzátor, jako je například analyzátor vzor. Pokud si nejste vlastních možností nastavení, stačí zadat název v definici pole.

Element "analyzer" přepsání standardních analyzátor na základě pole pole. Neexistuje žádný globální přepsání. V tomto příkladu `text1` používá vzor analyzátor a `text2`, která neurčuje analyzátor, používá výchozí.

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

Rozhraní API obsahovat atributy další index pro zadání různé analyzátory pro indexování a vyhledávání. `searchAnalyzer` a `indexAnalyzer` atributy musí být zadány jako pár nahrazení jedné `analyzer` atribut.


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

Pole obsahující řetězce v různých jazycích můžete použít analyzátor jazyka, zatímco ostatní pole ponechte výchozí (nebo pomocí některé jiné předdefinované nebo vlastní analyzátoru). Pokud chcete použít analyzátor jazyka, musíte ho použít pro operace indexování a vyhledávání. Pole, která je použít analyzátor jazyka nemůže mít jiný analyzátory pro indexování a hledání.

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

+ Přečtěte si naše komplexní vysvětlení [jak úplné textové vyhledávání funguje ve službě Azure Search](search-lucene-query-architecture.md). Tento článek používá příklady vysvětlení chování, které vám mohou připadat counter-intuitive na ploše.

+ Zkuste syntaxe další dotazu z [vyhledávání dokumentů](https://docs.microsoft.com/rest/api/searchservice/search-documents#examples) příklad části nebo z [jednoduchá syntaxe dotazů](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) v Průzkumník služby Search na portálu.

+ Zjistěte, jak použít [lexikální analyzátory jazyka](https://docs.microsoft.com/rest/api/searchservice/language-support).

+ [Konfigurace vlastní analyzátorů](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search) pro minimální zpracování nebo speciální zpracování u jednotlivých polí.

+ [Porovnání standardní a anglické analyzátorů](http://alice.unearth.ai/) v sousedících podokna na tento ukázkový web. 

## <a name="see-also"></a>Další informace najdete v tématech

 [Hledání dokumentů rozhraní REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents) 

 [Jednoduchá syntaxe dotazů](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 

 [Úplná syntaxe dotazů Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 
 
 [Zpracování výsledků vyhledávání](https://docs.microsoft.com/azure/search/search-pagination-page-layout)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png
