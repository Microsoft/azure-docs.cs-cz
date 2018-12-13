---
title: Upgrade na Azure Search .NET SDK verze 1.1 – Azure Search
description: Migrace kódu do služby Azure Search .NET SDK verze 1.1 ze starších verzí rozhraní API. Zjistěte, co je nového a jaké změny kódu jsou povinné.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: 82823bae76521080634d4f7ff285d94ce8495fbf
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53317283"
---
# <a name="upgrading-to-the-azure-search-net-sdk-version-11"></a>Upgrade na Azure Search .NET SDK verze 1.1

Pokud používáte verzi 1.0.2-preview nebo starší aplikace [Azure Search .NET SDK](https://aka.ms/search-sdk), tento článek vám pomůže při upgradu aplikace pomocí verze 1.1.

Další obecné návod sady SDK, včetně příkladů najdete v tématu [použití Azure Search z aplikace .NET](search-howto-dotnet-sdk.md).

> [!NOTE]
> Po upgradu na verzi 1.1, nebo pokud už používáte verzi mezi 1.1 a 2.0 preview (včetně), měli byste upgradovat na verzi 3. Zobrazit [upgrade na Azure Search .NET SDK verze 3](search-dotnet-sdk-migration.md) pokyny.
>

Nejprve aktualizujte referenci NuGet pro `Microsoft.Azure.Search` pomocí konzoly Správce balíčků NuGet nebo tím, že pravým tlačítkem myši na odkazy projektu a vyberte "Spravovat NuGet balíčky..." v sadě Visual Studio.

Po stažení nové balíčky a jejich závislostí NuGet znovu sestavte projekt.

Pokud jste používali dříve 1.0.0-preview verze, 1.0.1-preview nebo 1.0.2-preview, musí být sestavení úspěšné a jste připraveni začít!

Pokud jste dříve používali 0.13.0-preview verze nebo starší, měla by se zobrazit chyby jako následující sestavení:

    Program.cs(137,56,137,62): error CS0117: 'Microsoft.Azure.Search.Models.IndexBatch' does not contain a definition for 'Create'
    Program.cs(137,99,137,105): error CS0117: 'Microsoft.Azure.Search.Models.IndexAction' does not contain a definition for 'Create'
    Program.cs(146,41,146,54): error CS1061: 'Microsoft.Azure.Search.IndexBatchException' does not contain a definition for 'IndexResponse' and no extension method 'IndexResponse' accepting a first argument of type 'Microsoft.Azure.Search.IndexBatchException' could be found (are you missing a using directive or an assembly reference?)
    Program.cs(163,13,163,42): error CS0246: The type or namespace name 'DocumentSearchResponse' could not be found (are you missing a using directive or an assembly reference?)

Dalším krokem je opravení chyb sestavení jeden po druhém. Většina bude vyžadovat změnu některé názvy třídy a metody, které byly přejmenovány v sadě SDK. [Seznam rozbíjejících změn ve verzi 1.1](#ListOfChangesV1) obsahuje seznam těchto změn názvů.

Pokud používáte vlastní třídy pro modelování dokumentů a tyto třídy mají vlastnosti NULL primitivní typy (například `int` nebo `bool` v C#), je oprava chyby v 1.1 verzi sady SDK, které byste měli vědět. Zobrazit [opravy chyb ve verzi 1.1](#BugFixesV1) další podrobnosti.

Nakonec po jste opravě případných chyb sestavení, můžete provádět změny výhod nových funkcí, pokud chcete, aby vaše aplikace.

<a name="ListOfChangesV1"></a>

## <a name="list-of-breaking-changes-in-version-11"></a>Seznam rozbíjejících změn ve verzi 1.1
V následujícím seznamu jsou seřazená podle pravděpodobnosti, že tato změna bude mít vliv na kód vaší aplikace.

### <a name="indexbatch-and-indexaction-changes"></a>IndexBatch a IndexAction změny
`IndexBatch.Create` byl přejmenován na `IndexBatch.New` a není k dispozici `params` argument. Můžete použít `IndexBatch.New` dávek, kombinovat různé typy akcí (sloučení, odstranění atd.). Kromě toho existují nové statické metody pro vytvoření dávky kde všechny akce jsou stejné: `Delete`, `Merge`, `MergeOrUpload`, a `Upload`.

`IndexAction` není k dispozici veřejné konstruktory a její vlastnosti jsou nyní neměnné. Používejte nové statické metody pro vytváření akcí pro různé účely: `Delete`, `Merge`, `MergeOrUpload`, a `Upload`. `IndexAction.Create` byl odebrán. Pokud jste použili přetížení přijímající pouze dokumentu, nezapomeňte použít `Upload` místo.

#### <a name="example"></a>Příklad:
Pokud váš kód bude vypadat takto:

    var batch = IndexBatch.Create(documents.Select(doc => IndexAction.Create(doc)));
    indexClient.Documents.Index(batch);

Můžete ho změnit na to opravit jakékoli chyby sestavení:

    var batch = IndexBatch.New(documents.Select(doc => IndexAction.Upload(doc)));
    indexClient.Documents.Index(batch);

Pokud chcete, můžete může ještě víc zjednodušit k tomuto:

    var batch = IndexBatch.Upload(documents);
    indexClient.Documents.Index(batch);

### <a name="indexbatchexception-changes"></a>IndexBatchException změny
`IndexBatchException.IndexResponse` Vlastnost byla přejmenována na `IndexingResults`, a její typ je nyní `IList<IndexingResult>`.

#### <a name="example"></a>Příklad:
Pokud váš kód bude vypadat takto:

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexResponse.Results.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

Můžete ho změnit na to opravit jakékoli chyby sestavení:

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

<a name="OperationMethodChanges"></a>

### <a name="operation-method-changes"></a>Operace změny – metoda
Každá operace v Azure Search .NET SDK je přístupný jako sada přetížení metody pro synchronní a asynchronní volání. Podpisy a které budou zohledňovat tato přetížení metody změnil verze 1.1.

Například operace "Získání statistik indexu" ve starších verzích sady SDK vystavit tyto podpisy:

V `IIndexOperations`:

    // Asynchronous operation with all parameters
    Task<IndexGetStatisticsResponse> GetStatisticsAsync(
        string indexName,
        CancellationToken cancellationToken);

V `IndexOperationsExtensions`:

    // Asynchronous operation with only required parameters
    public static Task<IndexGetStatisticsResponse> GetStatisticsAsync(
        this IIndexOperations operations,
        string indexName);

    // Synchronous operation with only required parameters
    public static IndexGetStatisticsResponse GetStatistics(
        this IIndexOperations operations,
        string indexName);

Podpisy metod pro stejnou operaci ve verzi 1.1 vypadat nějak takto:

V `IIndexesOperations`:

    // Asynchronous operation with lower-level HTTP features exposed
    Task<AzureOperationResponse<IndexGetStatisticsResult>> GetStatisticsWithHttpMessagesAsync(
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions),
        Dictionary<string, List<string>> customHeaders = null,
        CancellationToken cancellationToken = default(CancellationToken));

V `IndexesOperationsExtensions`:

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

Od verze 1.1, Azure Search .NET SDK slouží k uspořádání metodám operace různě:

* Volitelné parametry jsou modelovány nyní jako výchozí parametry spíše než přetížení další metody. To snižuje počet přetížení metody, někdy výrazně.
* Rozšiřující metody nyní skrýt spoustu nadbytečné podrobnosti protokolu HTTP od volajícího. Například starší verze sady SDK vrátil objekt odpověď se stavovým kódem HTTP, které často nemusíte zkontrolovat, protože operace metody vyvolat `CloudException` pro stavový kód, který označuje chybu. Nové metody rozšíření vrátit jenom objekty modelu, uložení vás zbaví starostí s k rozbalení v kódu s.
* Naopak základní rozhraní nyní vystavení metody, které poskytují větší kontrolu na úrovni protokolu HTTP pokud ho potřebujete. Nyní můžete předat vlastní hlavičky protokolu HTTP, které mají být zahrnuty v žádosti a nové `AzureOperationResponse<T>` návratový typ poskytuje přímý přístup k `HttpRequestMessage` a `HttpResponseMessage` pro operaci. `AzureOperationResponse` je definován v `Microsoft.Rest.Azure` obor názvů a nahradí `Hyak.Common.OperationResponse`.

### <a name="scoringparameters-changes"></a>ScoringParameters změny
Novou třídu s názvem `ScoringParameter` se přidala nejnovější sadu SDK, aby bylo snazší poskytuje parametry pro profily skórování vyhledávacího dotazu. Dříve `ScoringProfiles` vlastnost `SearchParameters` třídy byl zadán jako `IList<string>`; Nyní je typovaný jako `IList<ScoringParameter>`.

#### <a name="example"></a>Příklad:
Pokud váš kód bude vypadat takto:

    var sp = new SearchParameters();
    sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
    sp.ScoringParameters = new[] { "featuredParam-featured", "mapCenterParam-" + lon + "," + lat };

Můžete ho změnit na to opravit jakékoli chyby sestavení: 

    var sp = new SearchParameters();
    sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
    sp.ScoringParameters =
        new[]
        {
            new ScoringParameter("featuredParam", new[] { "featured" }),
            new ScoringParameter("mapCenterParam", GeographyPoint.Create(lat, lon))
        };

### <a name="model-class-changes"></a>Změny třídy modelu
Z důvodu změn podpis je popsáno v [změny metody operace](#OperationMethodChanges), mnoho tříd v `Microsoft.Azure.Search.Models` obor názvů byly přejmenovány nebo odstraněny. Příklad:

* `IndexDefinitionResponse` byl nahrazen `AzureOperationResponse<Index>`
* Přejmenování `DocumentSearchResponse` na `DocumentSearchResult`
* Přejmenování `IndexResult` na `IndexingResult`
* `Documents.Count()` nyní vrátí `long` s počtem dokumentů místo `DocumentCountResponse`
* Přejmenování `IndexGetStatisticsResponse` na `IndexGetStatisticsResult`
* Přejmenování `IndexListResponse` na `IndexListResult`

Souhrnně řečeno, `OperationResponse`-odvozené třídy, které existovaly pouze při zabalení byly odebrány objekt modelu. Zbývající třídy měli jejich přípony změnil z `Response` k `Result`.

#### <a name="example"></a>Příklad:
Pokud váš kód bude vypadat takto:

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

Můžete ho změnit na to opravit jakékoli chyby sestavení:

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

#### <a name="response-classes-and-ienumerable"></a>Odpověď třídy a rozhraní IEnumerable
Další změny, které mohou ovlivnit váš kód je, že odpověď třídy, které uložení kolekce už implementovat `IEnumerable<T>`. Místo toho vlastnost kolekce můžete přímo přistupovat. Pokud například váš kód vypadá takto:

    DocumentSearchResponse<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response)
    {
        Console.WriteLine(result.Document);
    }

Můžete ho změnit na to opravit jakékoli chyby sestavení:

    DocumentSearchResult<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response.Results)
    {
        Console.WriteLine(result.Document);
    }

#### <a name="special-case-for-web-applications"></a>Zvláštní případ pro webové aplikace
Pokud máte webovou aplikaci, která serializuje `DocumentSearchResponse` přímo do prohlížeče odeslat výsledky hledání, bude nutné změnit váš kód nebo výsledky nebudou správně serializovat. Pokud například váš kód vypadá takto:

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

Můžete ji změnit tím, že získáme `.Results` vlastnost reakce na vyhledávání, chcete-li vyřešit vykreslování výsledků vyhledávání:

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

Budete muset vyhledat takových případech ve vašem kódu sami. **Kompilátor nezobrazí upozornění** protože `JsonResult.Data` je typu `object`.

### <a name="cloudexception-changes"></a>CloudException změny
`CloudException` Třídy se přesunula z verze `Hyak.Common` obor názvů, abyste `Microsoft.Rest.Azure` oboru názvů. Také jeho `Error` vlastnost byla přejmenována na `Body`.

### <a name="searchserviceclient-and-searchindexclient-changes"></a>Změny SearchServiceClient a SearchIndexClient
Typ `Credentials` vlastnost se změnil z `SearchCredentials` se svou základní třídou `ServiceClientCredentials`. Pokud potřebujete získat přístup `SearchCredentials` z `SearchIndexClient` nebo `SearchServiceClient`, použijte prosím nový `SearchCredentials` vlastnost.

Ve starších verzích sady SDK `SearchServiceClient` a `SearchIndexClient` měl konstruktory, které trvalo `HttpClient` parametru. Ty se nahradily konstruktorů, které `HttpClientHandler` a pole `DelegatingHandler` objekty. To usnadňuje instalaci vlastní obslužné rutiny předběžně zpracovat požadavky HTTP v případě potřeby.

Nakonec konstruktorů, které trvalo `Uri` a `SearchCredentials` změnily. Pokud například máte kód, který vypadá takto:

    var client =
        new SearchServiceClient(
            new SearchCredentials("abc123"),
            new Uri("http://myservice.search.windows.net"));

Můžete ho změnit na to opravit jakékoli chyby sestavení:

    var client =
        new SearchServiceClient(
            new Uri("http://myservice.search.windows.net"),
            new SearchCredentials("abc123"));

Všimněte si také, typ parametru přihlašovací údaje se změnila na `ServiceClientCredentials`. To se pravděpodobně ovlivňovat váš kód od `SearchCredentials` je odvozen z `ServiceClientCredentials`.

### <a name="passing-a-request-id"></a>Při předávání ID požadavku
Ve starších verzích sady SDK, můžete nastavit ID požadavku na `SearchServiceClient` nebo `SearchIndexClient` a by být součástí každého požadavku rozhraní REST API. To je užitečné pro odstraňování potíží s vaší vyhledávací služby, pokud je potřeba obraťte se na podporu. Je však další užitečné, chcete-li nastavit požadavek jedinečného ID pro každou operaci, a nikoli k používat stejné ID pro všechny operace. Z tohoto důvodu `SetClientRequestId` metody `SearchServiceClient` a `SearchIndexClient` byly odebrány. Místo toho můžete předat ID žádosti o každé operace metodě prostřednictvím nepovinný `SearchRequestOptions` parametru.

> [!NOTE]
> V budoucí verzi sady SDK přidáme nový mechanismus pro ID žádosti o globální nastavení na straně klienta objektů, který je konzistentní s přístupem používají jiné sady Azure SDK.
> 
> 

### <a name="example"></a>Příklad:
Pokud máte kód, který vypadá takto:

    client.SetClientRequestId(Guid.NewGuid());
    ...
    long count = client.Documents.Count();

Můžete ho změnit na to opravit jakékoli chyby sestavení:

    long count = client.Documents.Count(new SearchRequestOptions(requestId: Guid.NewGuid()));

### <a name="interface-name-changes"></a>Změny názvů rozhraní
Názvy rozhraní operace skupiny byla změněna na bylo v souladu s jejich odpovídající názvy vlastností:

* Typ `ISearchServiceClient.Indexes` byl přejmenován z `IIndexOperations` k `IIndexesOperations`.
* Typ `ISearchServiceClient.Indexers` byl přejmenován z `IIndexerOperations` k `IIndexersOperations`.
* Typ `ISearchServiceClient.DataSources` byl přejmenován z `IDataSourceOperations` k `IDataSourcesOperations`.
* Typ `ISearchIndexClient.Documents` byl přejmenován z `IDocumentOperations` k `IDocumentsOperations`.

Tato změna je nepravděpodobné, že pokud jste vytvořili mocks tato rozhraní pro účely testování ovlivní váš kód.

<a name="BugFixesV1"></a>

## <a name="bug-fixes-in-version-11"></a>Opravy chyb ve verzi 1.1
Ve starších verzích nástroje související se serializací tříd vlastního modelu Azure Search .NET SDK se chyby. Chyba může nastat, pokud jste vytvořili třídu vlastního modelu s vlastností typu hodnotu Null.

### <a name="steps-to-reproduce"></a>Kroky pro reprodukci
Vytvořte třídu vlastního modelu s vlastností typu hodnotu Null. Například přidejte veřejnou `UnitCount` vlastnost typu `int` místo `int?`.

Pokud se indexování dokumentů s výchozí hodnotou tohoto typu (například 0 pro `int`), pole bude mít hodnotu null ve službě Azure Search. Pokud hledáte následně pro daný dokument `Search` volání vyvolají `JsonSerializationException` stěžovali, který nelze převést `null` k `int`.

Navíc filtry nemusí fungovat podle očekávání, protože hodnota null byla zapsána do indexu místo zamýšlená hodnota.

### <a name="fix-details"></a>Opravte podrobnosti
Tento problém jsme vyřešili ve verzi 1.1 sady SDK. Nyní, pokud máte třídu modelu takto:

    public class Model
    {
        public string Key { get; set; }

        public int IntValue { get; set; }
    }

a nastavíte `IntValue` na hodnotu 0, tuto hodnotu je nyní správně serializován jako 0 na lince a uloží jako 0 v indexu. Round přeskočení také funguje podle očekávání.

Je jedním potenciálním problémem je potřeba vědět s tímto přístupem: Pokud použijete typ modelu pomocí vlastnosti NULL, budete muset **zaručit** , obsahovat žádné dokumenty v indexu pro odpovídající pole hodnotu null. Sada SDK, ani REST API služby Azure Search vám pomůže tím nepomůže.

Nejedná se pouze o hypotetický problém: Představte si situaci, kde můžete přidat nové pole do stávajícího indexu typu `Edm.Int32`. Po aktualizaci definice indexu budou mít všechny dokumenty pro toto nové pole hodnotu null (protože všechny typy jsou ve službě Azure Search s možností null). Pokud pak použijete třídu modelu s vlastností `int` se zakázanou hodnotou null, při pokusu o načtení dokumentů dojde k vyvolání podobné výjimky `JsonSerializationException`:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Z tohoto důvodu stále doporučujeme jako osvědčený postup používat typy připouštějící hodnotu Null ve třídách modelu.

Další podrobnosti o této chybě a vyřešit, najdete v tématu [tento problém na Githubu](https://github.com/Azure/azure-sdk-for-net/issues/1063).

