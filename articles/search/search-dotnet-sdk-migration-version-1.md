---
title: Upgrade na Azure Search .NET SDK verze 1,1
titleSuffix: Azure Cognitive Search
description: Migrujte kód do sady Azure Search .NET SDK verze 1,1 ze starších verzí rozhraní API. Podívejte se, co je nového a co je potřeba mít změny kódu.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 4860918fc4f995ad267fc35b142d3fcb460ce177
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "89002823"
---
# <a name="upgrade-to-azure-search-net-sdk-version-11"></a>Upgrade na Azure Search .NET SDK verze 1,1

Pokud používáte verzi 1.0.2-Preview nebo starší [Azure Search .NET SDK](/dotnet/api/overview/azure/search), Tento článek vám pomůže při upgradu vaší aplikace tak, aby používala verzi 1,1.

Obecnější návod k sadě SDK, včetně příkladů, najdete v tématu [použití Azure Search z aplikace .NET](search-howto-dotnet-sdk.md).

> [!NOTE]
> Po upgradu na verzi 1,1 nebo pokud už používáte verzi mezi 1,1 a 2,0-Preview (včetně), měli byste upgradovat na verzi 3. Pokyny najdete v tématu [upgrade na sadu Azure Search .NET SDK verze 3](search-dotnet-sdk-migration.md) .
>

Nejdřív aktualizujte svůj odkaz na NuGet pro `Microsoft.Azure.Search` použití buď konzoly Správce balíčků NuGet, nebo kliknutím pravým tlačítkem na odkazy na projekt a výběrem možnosti spravovat balíčky NuGet... v aplikaci Visual Studio.

Jakmile NuGet stáhne nové balíčky a jejich závislosti, sestavte projekt znovu.

Pokud jste dříve používali verzi 1.0.0-Preview, 1.0.1-Preview nebo 1.0.2-Preview, sestavení by mělo být úspěšné a Vy jste připraveni!

Pokud jste dříve používali verzi 0.13.0-Preview nebo starší, měli byste vidět chyby sestavení, například následující:

```output
Program.cs(137,56,137,62): error CS0117: 'Microsoft.Azure.Search.Models.IndexBatch' does not contain a definition for 'Create'
Program.cs(137,99,137,105): error CS0117: 'Microsoft.Azure.Search.Models.IndexAction' does not contain a definition for 'Create'
Program.cs(146,41,146,54): error CS1061: 'Microsoft.Azure.Search.IndexBatchException' does not contain a definition for 'IndexResponse' and no extension method 'IndexResponse' accepting a first argument of type 'Microsoft.Azure.Search.IndexBatchException' could be found (are you missing a using directive or an assembly reference?)
Program.cs(163,13,163,42): error CS0246: The type or namespace name 'DocumentSearchResponse' could not be found (are you missing a using directive or an assembly reference?)
```

Dalším krokem je opravit chyby sestavení jeden po druhém. Většina bude vyžadovat změnu některých názvů tříd a metod, které byly v sadě SDK přejmenovány. [Seznam nejnovějších změn ve verzi 1,1](#ListOfChangesV1) obsahuje seznam těchto změn názvu.

Pokud používáte vlastní třídy pro modelování dokumentů a tyto třídy mají vlastnosti primitivních typů, které neumožňují hodnotu null (například `int` nebo `bool` v jazyce C#), dojde k opravě chyb ve verzi 1,1 sady SDK, které byste měli znát. Další podrobnosti najdete [v článku opravy chyb ve verzi 1,1](#BugFixesV1) .

Nakonec, pokud jste opravili chyby sestavení, můžete v aplikaci provádět změny, abyste mohli využít nové funkce, pokud chcete.

<a name="ListOfChangesV1"></a>

## <a name="list-of-breaking-changes-in-version-11"></a>Seznam nejnovějších změn ve verzi 1,1
Následující seznam je seřazen podle pravděpodobnosti, že změna bude mít vliv na kód aplikace.

### <a name="indexbatch-and-indexaction-changes"></a>IndexBatch a IndexAction změny
`IndexBatch.Create` byl přejmenován na `IndexBatch.New` a již nemá `params` argument. Můžete použít `IndexBatch.New` pro dávky, které mají kombinaci různých typů akcí (sloučení, odstranění atd.). Kromě toho existují nové statické metody pro vytváření dávek, kde jsou všechny akce stejné: `Delete` , `Merge` , `MergeOrUpload` a `Upload` .

`IndexAction` už nemá veřejné konstruktory a jeho vlastnosti jsou teď neměnné. Je vhodné použít nové statické metody pro vytváření akcí pro různé účely: `Delete` , `Merge` , a `MergeOrUpload` `Upload` . `IndexAction.Create` bylo odebráno. Pokud jste použili přetížení, které používá pouze dokument, nezapomeňte `Upload` místo toho použít.

#### <a name="example"></a>Příklad
Pokud váš kód vypadá takto:

```csharp
var batch = IndexBatch.Create(documents.Select(doc => IndexAction.Create(doc)));
indexClient.Documents.Index(batch);
```

Můžete ji změnit na tuto možnost, chcete-li opravit chyby sestavení:

```csharp
var batch = IndexBatch.New(documents.Select(doc => IndexAction.Upload(doc)));
indexClient.Documents.Index(batch);
```

Pokud chcete, můžete ho dál zjednodušit:

```csharp
var batch = IndexBatch.Upload(documents);
indexClient.Documents.Index(batch);
```

### <a name="indexbatchexception-changes"></a>IndexBatchException změny
`IndexBatchException.IndexResponse`Vlastnost byla přejmenována na `IndexingResults` a její typ je nyní `IList<IndexingResult>` .

#### <a name="example"></a>Příklad
Pokud váš kód vypadá takto:

```csharp
catch (IndexBatchException e)
{
    Console.WriteLine(
        "Failed to index some of the documents: {0}",
        String.Join(", ", e.IndexResponse.Results.Where(r => !r.Succeeded).Select(r => r.Key)));
}
```

Můžete ji změnit na tuto možnost, chcete-li opravit chyby sestavení:

```csharp
catch (IndexBatchException e)
{
    Console.WriteLine(
        "Failed to index some of the documents: {0}",
        String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
}
```

<a name="OperationMethodChanges"></a>

### <a name="operation-method-changes"></a>Změny metody operace
Každá operace v sadě Azure Search .NET SDK je vystavena jako sada přetížení metod pro synchronní a asynchronní volající. Signatury a faktoringy těchto přetížení metod se změnily ve verzi 1,1.

Například operace "získat statistiku indexu" ve starších verzích sady SDK zveřejňuje tyto signatury:

V `IIndexOperations`:

```csharp
// Asynchronous operation with all parameters
Task<IndexGetStatisticsResponse> GetStatisticsAsync(
    string indexName,
    CancellationToken cancellationToken);
```

V `IndexOperationsExtensions`:

```csharp
// Asynchronous operation with only required parameters
public static Task<IndexGetStatisticsResponse> GetStatisticsAsync(
    this IIndexOperations operations,
    string indexName);

// Synchronous operation with only required parameters
public static IndexGetStatisticsResponse GetStatistics(
    this IIndexOperations operations,
    string indexName);
```

Signatury metody pro stejnou operaci ve verzi 1,1 vypadají takto:

V `IIndexesOperations`:

```csharp
// Asynchronous operation with lower-level HTTP features exposed
Task<AzureOperationResponse<IndexGetStatisticsResult>> GetStatisticsWithHttpMessagesAsync(
    string indexName,
    SearchRequestOptions searchRequestOptions = default(SearchRequestOptions),
    Dictionary<string, List<string>> customHeaders = null,
    CancellationToken cancellationToken = default(CancellationToken));
```

V `IndexesOperationsExtensions`:

```csharp
// Simplified asynchronous operation
public static Task<IndexGetStatisticsResult> GetStatisticsAsync(
    this IIndexesOperations operations,
    string indexName,
    SearchRequestOptions searchRequestOptions = default(SearchRequestOptions),
    CancellationToken cancellationToken = default(CancellationToken));

// Simplified synchronous operation
public static IndexGetStatisticsResult GetStatistics(
    this IIndexesOperations operations,
    string indexName,
    SearchRequestOptions searchRequestOptions = default(SearchRequestOptions));
```

Počínaje verzí 1,1 Azure Search .NET SDK uspořádá metody operací různě:

* Volitelné parametry se nyní modelují jako výchozí parametry, nikoli jako další přetížení metody. Tím se snižuje počet přetížení metod, někdy významně.
* Rozšiřující metody teď skrývají velké množství cizích podrobností HTTP od volajícího. Například starší verze sady SDK vrátily objekt Response se stavovým kódem HTTP, který často nemusíte kontrolovat, protože operace vyvolají metody operací `CloudException` pro libovolný stavový kód, který označuje chybu. Nové metody rozšíření právě vracejí objekty modelu a ušetří vám tak problémy s jejich rozbalením do kódu.
* Naopak základní rozhraní teď zveřejňují metody, které poskytují větší kontrolu na úrovni HTTP, pokud je potřebujete. Nyní můžete předat vlastní hlavičky protokolu HTTP, které budou zahrnuty do požadavků, a nový `AzureOperationResponse<T>` návratový typ vám poskytne přímý přístup k `HttpRequestMessage` a `HttpResponseMessage` pro danou operaci. `AzureOperationResponse` je definována v `Microsoft.Rest.Azure` oboru názvů a nahrazuje `Hyak.Common.OperationResponse` .

### <a name="scoringparameters-changes"></a>ScoringParameters změny
V nejnovější sadě SDK se přidala nová třída s názvem, která usnadňuje `ScoringParameter` zadání parametrů pro vyhodnocování profilů ve vyhledávacím dotazu. Dříve `ScoringProfiles` `SearchParameters` byla vlastnost třídy zapsána jako `IList<string>` ; Nyní je zapsán jako `IList<ScoringParameter>` .

#### <a name="example"></a>Příklad
Pokud váš kód vypadá takto:

```csharp
var sp = new SearchParameters();
sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
sp.ScoringParameters = new[] { "featuredParam-featured", "mapCenterParam-" + lon + "," + lat };
```

Můžete ji změnit na tuto možnost, chcete-li opravit chyby sestavení: 

```csharp
var sp = new SearchParameters();
sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
sp.ScoringParameters =
    new[]
    {
        new ScoringParameter("featuredParam", new[] { "featured" }),
        new ScoringParameter("mapCenterParam", GeographyPoint.Create(lat, lon))
    };
```

### <a name="model-class-changes"></a>Změny třídy modelu
Z důvodu změn signatur popsaných ve [změnách metod operace](#OperationMethodChanges)byl mnoho tříd v `Microsoft.Azure.Search.Models` oboru názvů přejmenováno nebo odebráno. Například:

* `IndexDefinitionResponse` byl nahrazen `AzureOperationResponse<Index>`
* Přejmenování `DocumentSearchResponse` na `DocumentSearchResult`
* Přejmenování `IndexResult` na `IndexingResult`
* `Documents.Count()` nyní vrátí `long` s počet dokumentů místo `DocumentCountResponse`
* Přejmenování `IndexGetStatisticsResponse` na `IndexGetStatisticsResult`
* Přejmenování `IndexListResponse` na `IndexListResult`

Pro sumarizaci `OperationResponse` byly odebrány odvozené třídy, které existovaly pouze pro zabalení objektu modelu. U zbývajících tříd se změnila přípona z `Response` na `Result` .

#### <a name="example"></a>Příklad
Pokud váš kód vypadá takto:

```csharp
IndexerGetStatusResponse statusResponse = null;

try
{
    statusResponse = _searchClient.Indexers.GetStatus(indexer.Name);
}
catch (Exception ex)
{
    Console.WriteLine("Error polling for indexer status: {0}", ex.Message);
    return;
}

IndexerExecutionResult lastResult = statusResponse.ExecutionInfo.LastResult;
```

Můžete ji změnit na tuto možnost, chcete-li opravit chyby sestavení:

```csharp
IndexerExecutionInfo status = null;

try
{
    status = _searchClient.Indexers.GetStatus(indexer.Name);
}
catch (Exception ex)
{
    Console.WriteLine("Error polling for indexer status: {0}", ex.Message);
    return;
}

IndexerExecutionResult lastResult = status.LastResult;
```

#### <a name="response-classes-and-ienumerable"></a>Třídy odpovědí a IEnumerable
Další změnu, která může ovlivnit váš kód, je, že třídy odpovědí, které uchovávají kolekce, již nejsou implementovány `IEnumerable<T>` . Místo toho můžete k vlastnosti kolekce přistupovat přímo. Například pokud váš kód vypadá takto:

```csharp
DocumentSearchResponse<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
foreach (SearchResult<Hotel> result in response)
{
    Console.WriteLine(result.Document);
}
```

Můžete ji změnit na tuto možnost, chcete-li opravit chyby sestavení:

```csharp
DocumentSearchResult<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
foreach (SearchResult<Hotel> result in response.Results)
{
    Console.WriteLine(result.Document);
}
```

#### <a name="special-case-for-web-applications"></a>Zvláštní případ pro webové aplikace
Máte-li webovou aplikaci, která je přímo serializována `DocumentSearchResponse` k odeslání výsledků hledání do prohlížeče, bude nutné změnit kód nebo výsledky nebudou serializovány správně. Například pokud váš kód vypadá takto:

```csharp
public ActionResult Search(string q = "")
{
    // If blank search, assume they want to search everything
    if (string.IsNullOrWhiteSpace(q))
        q = "*";

    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = _featuresSearch.Search(q)
    };
}
```

Můžete ho změnit tak, že získáte `.Results` vlastnost odpovědi vyhledávání pro opravu vykreslování výsledků hledání:

```csharp
public ActionResult Search(string q = "")
{
    // If blank search, assume they want to search everything
    if (string.IsNullOrWhiteSpace(q))
        q = "*";

    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = _featuresSearch.Search(q).Results
    };
}
```

Tyto případy budete muset ve svém kódu Hledat sami. **Kompilátor vás neupozorní,** protože `JsonResult.Data` je typu `object` .

### <a name="cloudexception-changes"></a>CloudException změny
`CloudException`Třída se přesunula z `Hyak.Common` oboru názvů do `Microsoft.Rest.Azure` oboru názvů. I jeho `Error` vlastnost byla přejmenována na `Body` .

### <a name="searchserviceclient-and-searchindexclient-changes"></a>SearchServiceClient a SearchIndexClient změny
Typ `Credentials` vlastnosti se změnil z `SearchCredentials` na svou základní třídu, `ServiceClientCredentials` . Pokud potřebujete přístup k `SearchCredentials` z `SearchIndexClient` nebo `SearchServiceClient` , použijte prosím novou `SearchCredentials` vlastnost.

Ve starších verzích sady SDK `SearchServiceClient` a `SearchIndexClient` měly konstruktory, které převzaly `HttpClient` parametr. Ty byly nahrazeny konstruktory, které přijímají `HttpClientHandler` a pole `DelegatingHandler` objektů. Díky tomu je snazší instalovat vlastní obslužné rutiny pro předběžné zpracování požadavků HTTP v případě potřeby.

Nakonec konstruktory, které trvalo `Uri` a `SearchCredentials` změnily. Například pokud máte kód, který vypadá takto:

```csharp
var client =
    new SearchServiceClient(
        new SearchCredentials("abc123"),
        new Uri("http://myservice.search.windows.net"));
```

Můžete ji změnit na tuto možnost, chcete-li opravit chyby sestavení:

```csharp
var client =
    new SearchServiceClient(
        new Uri("http://myservice.search.windows.net"),
        new SearchCredentials("abc123"));
```

Všimněte si také, že typ parametru přihlašovacích údajů se změnil na `ServiceClientCredentials` . To je pravděpodobně ovlivněno kódem, protože `SearchCredentials` je odvozen z `ServiceClientCredentials` .

### <a name="passing-a-request-id"></a>Předání ID žádosti
Ve starších verzích sady SDK jste mohli nastavit ID žádosti na `SearchServiceClient` nebo `SearchIndexClient` a v každém požadavku na REST API. To je užitečné pro řešení problémů se službou vyhledávání, pokud potřebujete kontaktovat podporu. Je ale užitečnější, abyste pro každou operaci nastavili jedinečné ID žádosti místo použití stejného ID pro všechny operace. Z tohoto důvodu `SetClientRequestId` `SearchServiceClient` `SearchIndexClient` byly odebrány metody a. Místo toho můžete každému provozní metodě předat ID žádosti prostřednictvím volitelného `SearchRequestOptions` parametru.

> [!NOTE]
> V budoucí verzi sady SDK přidáme nový mechanismus pro nastavení ID žádosti globálně na objektech klienta, který je konzistentní s přístupem používaným jinými sadami SDK Azure.
> 
> 

### <a name="example"></a>Příklad
Pokud máte kód, který vypadá takto:

```csharp
client.SetClientRequestId(Guid.NewGuid());
...
long count = client.Documents.Count();
```

Můžete ji změnit na tuto možnost, chcete-li opravit chyby sestavení:

```csharp
long count = client.Documents.Count(new SearchRequestOptions(requestId: Guid.NewGuid()));
```

### <a name="interface-name-changes"></a>Změny názvu rozhraní
Všechny názvy rozhraní skupiny operací se změnily tak, aby byly konzistentní s jejich odpovídajícími názvy vlastností:

* Typ `ISearchServiceClient.Indexes` byl přejmenován z `IIndexOperations` na `IIndexesOperations` .
* Typ `ISearchServiceClient.Indexers` byl přejmenován z `IIndexerOperations` na `IIndexersOperations` .
* Typ `ISearchServiceClient.DataSources` byl přejmenován z `IDataSourceOperations` na `IDataSourcesOperations` .
* Typ `ISearchIndexClient.Documents` byl přejmenován z `IDocumentOperations` na `IDocumentsOperations` .

Tato změna pravděpodobně neovlivní váš kód, pokud jste pro testovací účely nevytvořili modely těchto rozhraní.

<a name="BugFixesV1"></a>

## <a name="bug-fixes-in-version-11"></a>Opravy chyb ve verzi 1,1
Došlo k chybě ve starších verzích sady Azure Search .NET SDK týkající se serializace vlastních tříd modelu. K této chybě může dojít, pokud jste vytvořili vlastní třídu modelu s vlastností typu hodnoty, která není null.

### <a name="steps-to-reproduce"></a>Kroky pro reprodukování
Vytvořte vlastní třídu modelu s vlastností typu hodnoty, která není null. Například přidejte veřejnou `UnitCount` vlastnost typu `int` namísto `int?` .

Pokud nasadíte dokument s výchozí hodnotou daného typu (například 0 pro `int` ), bude pole null v Azure Search. Pokud tento dokument následně vyhledáte, `Search` volání vyvolá `JsonSerializationException` stížnost, na kterou nemůže převést `null` `int` .

Filtry také nemusí fungovat podle očekávání, protože místo zamýšlené hodnoty byla do indexu zapsána hodnota null.

### <a name="fix-details"></a>Opravit podrobnosti
Tento problém jsme vyřešili ve verzi 1,1 sady SDK. Nyní, pokud máte třídu modelu, například:

```csharp
public class Model
{
    public string Key { get; set; }

    public int IntValue { get; set; }
}
```

a nastavíte hodnotu `IntValue` 0, tato hodnota je nyní správně serializována jako 0 na lince a v indexu je uložena jako 0. Funkce Round Trip funguje také podle očekávání.

Existuje jeden možný problém s tímto přístupem: Pokud použijete typ modelu s vlastností, která neumožňuje hodnotu null, musíte **zaručit** , aby žádné dokumenty v indexu neobsahovaly pro odpovídající pole hodnotu null. Sada SDK ani Azure Search REST API vám ji nepomůže vyhovět.

Nejedná se pouze o hypotetický problém: představte si situaci, kdy přidáte nové pole do stávajícího indexu typu `Edm.Int32`. Po aktualizaci definice indexu budou mít všechny dokumenty pro toto nové pole hodnotu null (protože všechny typy jsou ve službě Azure Search s možností null). Pokud pak použijete třídu modelu s vlastností `int` se zakázanou hodnotou null, při pokusu o načtení dokumentů dojde k vyvolání podobné výjimky `JsonSerializationException`:

```output
Error converting value {null} to type 'System.Int32'. Path 'IntValue'.
```

Z tohoto důvodu doporučujeme, abyste jako osvědčený postup používali typy s možnou hodnotou null v třídách modelu.

Další informace o této chybě a opravě najdete v [tomto problému na GitHubu](https://github.com/Azure/azure-sdk-for-net/issues/1063).