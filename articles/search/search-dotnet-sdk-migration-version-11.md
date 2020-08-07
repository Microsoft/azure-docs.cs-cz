---
title: Upgrade na .NET SDK verze 11
titleSuffix: Azure Cognitive Search
description: Migruje kód do služby Azure Kognitivní hledání .NET SDK verze 11 ze starších verzí. Zjistěte, co je nového a kdy se vyžadují změny kódu.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 08/05/2020
ms.openlocfilehash: 03d40dcaeaefe01fecbc201cf28dc20c8634af9d
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/07/2020
ms.locfileid: "87926667"
---
# <a name="upgrade-to-azure-cognitive-search-net-sdk-version-11"></a>Upgrade na Azure Kognitivní hledání .NET SDK verze 11

Pokud používáte verzi [sady .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search)verze 10,0 nebo starší, Tento článek vám pomůže při upgradu na verzi 11.

Verze 11 je plně přepracovaná Klientská knihovna, kterou vydala vývojový tým Azure SDK (předchozí verze vytvořil vývojářský tým Azure Kognitivní hledání). Pro zajištění větší konzistence s jinými klientskými knihovnami Azure došlo k přepracování této knihovny, která využívá závislost na [Azure. Core](https://docs.microsoft.com/dotnet/api/azure.core) a [System.Text.Jsna](https://docs.microsoft.com/dotnet/api/system.text.json)a implementuje známé přístupy k běžným úlohám.

Mezi hlavní rozdíly, které si všimnete v nové verzi, patří:

+ Jeden balíček a knihovna na rozdíl od více
+ Nový název balíčku: `Azure.Search.Documents` místo `Microsoft.Azure.Search` .
+ Tři klienti místo dvou: `SearchClient` , `SearchIndexClient` ,`SearchIndexerClient`
+ Rozdíly v pojmenovávání napříč celou řadou rozhraní API a malé strukturální rozdíly, které zjednodušují některé úkoly

## <a name="package-and-library-consolidation"></a>Konsolidace balíčků a knihoven

Verze 11 slučuje více balíčků a knihoven do jedné. Po migraci budete mít k dispozici méně knihoven ke správě.

+ [Balíček umentsAzure.Search.Doc](https://www.nuget.org/packages/Azure.Search.Documents/)

+ [Reference k rozhraní API pro klientskou knihovnu](https://docs.microsoft.com/dotnet/api/overview/azure/search.documents-readme?view=azure-dotnet)

## <a name="client-differences"></a>Rozdíly klientů

V případě potřeby následující tabulka mapuje klientské knihovny mezi těmito dvěma verzemi.

| Rozsah operací | Microsoft. Azure. Search &nbsp; (v10 za účelem) | Azure.Search.Documents &nbsp; (V11) |
|---------------------|------------------------------|------------------------------|
| Klient se používá pro dotazy a k naplnění indexu. | [SearchIndexClient](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.searchindexclient) | [SearchClient](https://docs.microsoft.com/dotnet/api/azure.search.documents.searchclient) |
| Klient používaný pro indexy, analyzátory, mapy synonym | [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient) | [SearchIndexClient](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.searchindexclient) |
| Klient používaný pro indexery, zdroje dat, dovednosti | [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient) | [SearchIndexerClient (**nové**)](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.searchindexerclient) |

> [!Important]
> `SearchIndexClient`existuje v obou verzích, ale podporuje různé věci. Ve verzi 10 `SearchIndexClient` Vytvořte indexy a další objekty. Ve verzi 11 `SearchIndexClient` funguje se stávajícími indexy. Aby nedocházelo k nejasnostem při aktualizaci kódu, nezapomeňte na pořadí, ve kterém jsou odkazy na klienta aktualizovány. Po sekvencování [kroků k upgradu](#UpgradeSteps) by měl být možné zmírnit případné problémy s nahrazením řetězce.

<a name="naming-differences"></a>

## <a name="naming-and-other-api-differences"></a>Pojmenovávání a jiné rozdíly v rozhraní API

Kromě rozdílů klientů (uvedených dříve a tedy vynechán) bylo přejmenováno více dalších rozhraní API a v některých případech byly přepracovány. Rozdíly v názvech tříd jsou shrnuty níže. Tento seznam není vyčerpávající, ale změní rozhraní API skupiny podle úlohy, což může být užitečné pro revize na konkrétní bloky kódu. Seznam aktualizovaných aktualizací rozhraní API najdete v [protokolu změn](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md) pro `Azure.Search.Documents` na GitHubu.

### <a name="authentication-and-encryption"></a>Ověřování a šifrování

| Verze 10 | Ekvivalent verze 11 |
|------------|-----------------------|
| [SearchCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchcredentials) | [AzureKeyCredential](https://docs.microsoft.com/dotnet/api/azure.azurekeycredential) |
| `EncryptionKey`(existovala v [sadě Preview SDK](https://www.nuget.org/packages/Microsoft.Azure.Search/8.0.0-preview) jako obecně dostupná funkce) | [SearchResourceEncryptionKey](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchresourceencryptionkey) |

### <a name="indexes-analyzers-synonym-maps"></a>Indexy, analyzátory, mapy synonym

| Verze 10 | Ekvivalent verze 11 |
|------------|-----------------------|
| [Index](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.index) | [SearchIndex](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchindex) |
| [Pole](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field) | [Seachfield](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchfield) |
| [Programátor](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datatype) | [SearchFieldDataType](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchfielddatatype) |
| [ItemError](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.itemerror) | [SearchIndexerError](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchindexererror) |
| [MBSA](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzer) | [LexicalAnalyzer](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.lexicalanalyze) (také `AnalyzerName` na `LexicalAnalyzerName` ) |
| [AnalyzeRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzerequest) | [AnalyzeTextOptions](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.analyzetextoptions) |
| [StandardAnalyzer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.standardanalyzer) | [LuceneStandardAnalyzer](https://docs.microsoft.com//dotnet/api/azure.search.documents.indexes.models.lucenestandardanalyzer) |
| [StandardTokenizer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.standardtokenizer) | [LuceneStandardTokenizer](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.lucenestandardtokenizer) (také `StandardTokenizerV2` na `LuceneStandardTokenizerV2` ) |
| [TokenInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.tokeninfo) | [AnalyzedTokenInfo](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.analyzedtokeninfo) |
| [Provádějících tokenizaci](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.tokenizer) | [LexicalTokenizer](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.lexicaltokenizer) (také `TokenizerName` na `LexicalTokenizerName` ) |
| [SynonymMap. Format](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.synonymmap.format) | Žádné Odeberte odkazy na `Format` . |

Definice polí jsou zjednodušené: [SearchableField](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchablefield), [SimpleField](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.simplefield), [ComplexField](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.complexfield) jsou nová rozhraní API pro vytváření definic polí.

### <a name="indexers-datasources-skillsets"></a>Indexery, zdroje dat, dovednosti

| Verze 10 | Ekvivalent verze 11 |
|------------|-----------------------|
| [Indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) | [SearchIndexer](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchindexer) |
| [Datového](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource) | [SearchIndexerDataSourceConnection](https://docs.microsoft.com//dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) |
| [Tuhle](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.skill) | [SearchIndexerSkill](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchindexerskill) |
| [Dovednosti](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.skillset) | [SearchIndexerSkillset](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchindexerskillse) |
| [DataSourceType](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype) | [SearchIndexerDataSourceType](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourcetype) |

### <a name="data-import"></a>Import dat

| Verze 10 | Ekvivalent verze 11 |
|------------|-----------------------|
| [IndexAction](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexaction) | [IndexDocumentsAction](https://docs.microsoft.com/dotnet/api/azure.search.documents.models.indexdocumentsaction) |
| [IndexBatch](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch) | [IndexDocumentsBatch](https://docs.microsoft.com/dotnet/api/azure.search.documents.models.indexdocumentsbatch) |

### <a name="query-definitions-and-results"></a>Definice a výsledky dotazu

| Verze 10 | Ekvivalent verze 11 |
|------------|-----------------------|
| [DocumentSearchResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1) | [SearchResult](https://docs.microsoft.com/dotnet/api/azure.search.documents.models.searchresult-1) nebo [searchResults](https://docs.microsoft.com/dotnet/api/azure.search.documents.models.searchresults-1), v závislosti na tom, zda je výsledkem jeden nebo více dokumentů. |
| [DocumentSuggestResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsuggestresult-1) | [SuggestResults](https://docs.microsoft.com/dotnet/api/azure.search.documents.models.suggestresults-1) |
| [SearchParameters](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters) |  [Příznacích searchOptions jsou](https://docs.microsoft.com/dotnet/api/azure.search.documents.searchoptions)  |

<a name="WhatsNew"></a>

## <a name="whats-in-version-11"></a>Verze 11

Každá verze klientské knihovny Azure Kognitivní hledání cílí na odpovídající verzi REST API. REST API se považuje za základ služby a jednotlivé sady SDK zabalí verzi REST API. Jako vývojář rozhraní .NET může být užitečné zkontrolovat [dokumentaci REST API](https://docs.microsoft.com/rest/api/searchservice/) , pokud chcete mít více na pozadí konkrétních objektů nebo operací.

Verze 11 cílí na [službu vyhledávání 2020-06-30](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/search/data-plane/Azure.Search/preview/2020-06-30/searchservice.json). Vzhledem k tomu, že verze 11 je také novou knihovnou klienta vytvořenou od základů, je většina úsilí při vývoji zaměřená na rovnocennost s verzí 10 a stále ještě čeká na určitou podporu funkcí REST API.

Verze 11 plně podporuje následující objekty a operace:

+ Vytváření a Správa indexů
+ Vytváření a Správa mapování synonym
+ Všechny typy dotazů a syntaxe (s výjimkou geografických filtrů)
+ Objekty a operace indexeru pro indexování datových zdrojů Azure, včetně zdrojů dat a dovednosti

### <a name="pending-features"></a>Nedokončené funkce

Následující funkce verze 10 ještě nejsou k dispozici ve verzi 11. Pokud tyto funkce použijete, při migraci se držte, dokud se nepodporují.

+ geoprostorové typy
+ [FieldBuilder](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.fieldbuilder) (i když můžete použít [Toto alternativní řešení](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/tests/Samples/FieldBuilder/FieldBuilder.cs)).
+ [Knowledge Store](knowledge-store-concept-intro.md)

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Postup upgradu

Následující kroky vám pomohou začít s migrací kódu proprocházením první sady požadovaných úloh, zejména v souvislosti s klientskými odkazy.

1. Nainstalujte [balíčekAzure.Search.Documents](https://www.nuget.org/packages/Azure.Search.Documents/) kliknutím pravým tlačítkem na odkazy na projekt a výběrem možnosti spravovat balíčky NuGet... v aplikaci Visual Studio.

1. Nahraďte direktivy using pro Microsoft. Azure. Search pomocí následujících kroků:

   ```csharp
   using Azure;
   using Azure.Search.Documents;
   using Azure.Search.Documents.Indexes;
   using Azure.Search.Documents.Indexes.Models;
   using Azure.Search.Documents.Models;
   ```

1. Nahraďte [SearchCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchcredentials) pomocí [AzureKeyCredential](https://docs.microsoft.com/dotnet/api/azure.azurekeycredential).

1. Aktualizujte klientské odkazy pro objekty související s indexerem. Pokud používáte indexery, zdroje dat nebo dovednosti, změňte odkazy klienta na [SearchIndexerClient](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.searchindexerclient). Tento klient je ve verzi 11 novinkou a nemá žádného předchůdce.

1. Aktualizujte klientské odkazy na dotazy a import dat. Instance [SearchIndexClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient) by měly být změněny na [SearchClient](https://docs.microsoft.com/dotnet/api/azure.search.documents.searchclient). Aby nedošlo k nejasnostem názvů, před pokračováním na další krok nezapomeňte zachytit všechny instance.

1. Aktualizujte odkazy klienta pro objekty index, indexer, mapování synonym a objekty analyzátoru. Instance [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient) by měly být změněny na [SearchIndexClient](https://docs.microsoft.com/dotnet/api/azure.search.documents.searchindexclient). 

1. Co nejvíc je to možné, aktualizační třídy, metody a vlastnosti pro použití rozhraní API nové knihovny. Oddíl [rozdíly v pojmenování](#naming-differences) je místo, kde se má začít, ale můžete si také prohlédnout [protokol změn](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md).

   Pokud máte potíže při hledání ekvivalentních rozhraní API, doporučujeme, abyste nahlásili problém, abychom [https://github.com/MicrosoftDocs/azure-docs/issues](https://github.com/MicrosoftDocs/azure-docs/issues) mohli vylepšit dokumentaci nebo prozkoumat problém.

1. Znovu sestavte řešení. Po opravě chyb nebo upozornění sestavení můžete v aplikaci provést další změny, abyste mohli využívat [nové funkce](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-11"></a>Přerušující změny ve verzi 11

Vzhledem k rozbalení změn knihoven a rozhraní API je upgrade na verzi 11 netriviální a představuje zásadní změnu v tom smyslu, že váš kód již nebude zpětně kompatibilní s verzí 10 a starší. Důkladné přezkoumání rozdílů najdete v [protokolu změn](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md) pro `Azure.Search.Documents` .

V souvislosti s verzemi služeb přináší přechod z 10 na 11 následující změny chování: 

+ [Algoritmus řazení BM25](index-ranking-similarity.md) nahrazuje algoritmus předchozí klasifikace novější technologií. Nové služby budou tento algoritmus používat automaticky. Pro existující služby musíte nastavit parametry pro použití nového algoritmu.

+ [Seřazené výsledky](search-query-odata-orderby.md) pro hodnoty null se v této verzi změnily, přičemž hodnoty null se zobrazují jako první, pokud je řazení `asc` a poslední `desc` . Pokud jste napsali kód pro zpracování, jak jsou hodnoty null seřazeny, měli byste zkontrolovat a potenciálně odstranit tento kód, pokud již není nutné.

## <a name="next-steps"></a>Další kroky

+ [Balíček umentsAzure.Search.Doc](https://www.nuget.org/packages/Azure.Search.Documents/)
+ [Ukázky na GitHubu](https://github.com/azure/azure-sdk-for-net/tree/Azure.Search.Documents_11.0.0/sdk/search/Azure.Search.Documents/samples)
+ [Reference k rozhraní APIAzure.Search.Document](https://docs.microsoft.com/dotnet/api/overview/azure/search.documents-readme?view=azure-dotnet)