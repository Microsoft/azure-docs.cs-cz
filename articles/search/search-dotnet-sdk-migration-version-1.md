---
title: Upgrade na Azure Search .NET SDK verze 1.1
titleSuffix: Azure Cognitive Search
description: Migrace kódu do sady Azure Search .NET SDK verze 1.1 ze starších verzí rozhraní API. Zjistěte, co je nového a jaké změny kódu jsou požadovány.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 159aaa8424c3d7a711b587464b80696929f02186
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72792382"
---
# <a name="upgrade-to-azure-search-net-sdk-version-11"></a>Upgrade na Azure Search .NET SDK verze 1.1

Pokud používáte verzi 1.0.2-preview nebo starší azure [search .NET SDK](https://aka.ms/search-sdk), tento článek vám pomůže upgradovat aplikaci na verzi 1.1.

Obecnější návod sady SDK včetně příkladů naleznete v tématu [Jak používat Azure Search z aplikace .NET](search-howto-dotnet-sdk.md).

> [!NOTE]
> Jakmile upgradujete na verzi 1.1 nebo pokud už používáte verzi mezi verzí 1.1 a 2.0-preview inclusive, měli byste upgradovat na verzi 3. Pokyny najdete [v tématu Upgrade na Azure Search .NET SDK verze 3.](search-dotnet-sdk-migration.md)
>

Nejprve aktualizujte odkaz `Microsoft.Azure.Search` NuGet pro použití konzoly NuGet Package Manager nebo kliknutím pravým tlačítkem myši na odkazy na projekt a výběrem možnosti "Spravovat balíčky NuGet..." v sadě Visual Studio.

Jakmile NuGet stáhla nové balíčky a jejich závislosti, znovu sestavit projekt.

Pokud jste dříve používali verzi 1.0.0-preview, 1.0.1-preview nebo 1.0.2-preview, sestavení by mělo být úspěšné a jste připraveni jít!

Pokud jste dříve používali verzi 0.13.0-preview nebo starší, měli byste vidět chyby sestavení, jako je následující:

    Program.cs(137,56,137,62): error CS0117: 'Microsoft.Azure.Search.Models.IndexBatch' does not contain a definition for 'Create'
    Program.cs(137,99,137,105): error CS0117: 'Microsoft.Azure.Search.Models.IndexAction' does not contain a definition for 'Create'
    Program.cs(146,41,146,54): error CS1061: 'Microsoft.Azure.Search.IndexBatchException' does not contain a definition for 'IndexResponse' and no extension method 'IndexResponse' accepting a first argument of type 'Microsoft.Azure.Search.IndexBatchException' could be found (are you missing a using directive or an assembly reference?)
    Program.cs(163,13,163,42): error CS0246: The type or namespace name 'DocumentSearchResponse' could not be found (are you missing a using directive or an assembly reference?)

Dalším krokem je oprava chyb sestavení jeden po druhém. Většina bude vyžadovat změnu některých názvů tříd a metod, které byly přejmenovány v sadě SDK. [Seznam narušujících změn ve verzi 1.1](#ListOfChangesV1) obsahuje seznam těchto změn názvů.

Pokud používáte vlastní třídy k modelování dokumentů a tyto třídy mají vlastnosti `int` typů `bool` primitivních neobsahujících s nulou (například nebo v c#), je oprava chyby v 1.1 verze sady SDK, o které byste měli vědět. Další podrobnosti najdete [v tématu Opravy chyb ve verzi 1.1.](#BugFixesV1)

Nakonec, jakmile opravíte všechny chyby sestavení, můžete provést změny v aplikaci a využít nové funkce, pokud chcete.

<a name="ListOfChangesV1"></a>

## <a name="list-of-breaking-changes-in-version-11"></a>Seznam nejnovějších změn ve verzi 1.1
Následující seznam je seřazen podle pravděpodobnosti, že změna ovlivní kód aplikace.

### <a name="indexbatch-and-indexaction-changes"></a>Změny indexů a indexových akcí
`IndexBatch.Create`byla přejmenována `IndexBatch.New` na a `params` již nemá argument. Můžete použít `IndexBatch.New` pro dávky, které kombinují různé typy akcí (sloučení, odstranění atd.). Kromě toho existují nové statické metody pro vytváření dávek, kde `Delete` `Merge`jsou `MergeOrUpload`všechny `Upload`akce stejné: , , a .

`IndexAction`již má veřejné konstruktory a jeho vlastnosti jsou nyní neměnné. Nové statické metody byste měli použít pro `Delete`vytváření `Merge` `MergeOrUpload`akcí `Upload`pro různé účely: , , a . `IndexAction.Create`byla odebrána. Pokud jste použili přetížení, které trvá pouze `Upload` dokument, ujistěte se, že použít místo.

#### <a name="example"></a>Příklad
Pokud váš kód vypadá takto:

    var batch = IndexBatch.Create(documents.Select(doc => IndexAction.Create(doc)));
    indexClient.Documents.Index(batch);

Můžete jej změnit na to to opravit všechny chyby sestavení:

    var batch = IndexBatch.New(documents.Select(doc => IndexAction.Upload(doc)));
    indexClient.Documents.Index(batch);

Pokud chcete, můžete jej dále zjednodušit takto:

    var batch = IndexBatch.Upload(documents);
    indexClient.Documents.Index(batch);

### <a name="indexbatchexception-changes"></a>Změny indexbatchvýjimky
Vlastnost `IndexBatchException.IndexResponse` byla přejmenována `IndexingResults`na a její `IList<IndexingResult>`typ je nyní .

#### <a name="example"></a>Příklad
Pokud váš kód vypadá takto:

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexResponse.Results.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

Můžete jej změnit na to to opravit všechny chyby sestavení:

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

<a name="OperationMethodChanges"></a>

### <a name="operation-method-changes"></a>Změny způsobu operace
Každá operace ve službě Azure Search .NET SDK je vystavena jako sada přetížení metody pro synchronní a asynchronní volající. Podpisy a faktoring těchto přetížení metody se změnil ve verzi 1.1.

Například operace "Získat statistiku indexu" ve starších verzích sady SDK odhalila tyto podpisy:

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

Podpisy metody pro stejnou operaci ve verzi 1.1 vypadají takto:

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

Počínaje verzí 1.1 sada Azure Search .NET SDK organizuje metody operací odlišně:

* Volitelné parametry jsou nyní modelovány jako výchozí parametry, nikoli jako další přetížení metody. To snižuje počet přetížení metody, někdy dramaticky.
* Rozšiřující metody nyní skrýt mnoho nadbytečných podrobností http z volajícího. Například starší verze sady SDK vrátily objekt odpovědi se stavovým kódem HTTP, který jste `CloudException` často nemuseli kontrolovat, protože metody operace vyvolány pro jakýkoli stavový kód, který označuje chybu. Nové metody rozšíření pouze vrátit objekty modelu, což vám ušetří potíže s nutností rozbalit je v kódu.
* Naopak základní rozhraní nyní vystavit metody, které poskytují větší kontrolu na úrovni PROTOKOLU HTTP, pokud ji potřebujete. Nyní můžete předat vlastní hlavičky HTTP, které mají být `AzureOperationResponse<T>` zahrnuty do požadavků `HttpRequestMessage` a `HttpResponseMessage` nový návratový typ umožňuje přímý přístup k a pro operaci. `AzureOperationResponse`je definována `Microsoft.Rest.Azure` v oboru `Hyak.Common.OperationResponse`názvů a nahrazuje .

### <a name="scoringparameters-changes"></a>Změny parametrů skórování
Do nejnovější `ScoringParameter` sady SDK byla přidána nová třída s názvem, která usnadňuje poskytování parametrů profilům hodnocení ve vyhledávacím dotazu. Dříve `ScoringProfiles` byla vlastnost `SearchParameters` třídy zadána jako `IList<string>`; Nyní je napsán `IList<ScoringParameter>`jako .

#### <a name="example"></a>Příklad
Pokud váš kód vypadá takto:

    var sp = new SearchParameters();
    sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
    sp.ScoringParameters = new[] { "featuredParam-featured", "mapCenterParam-" + lon + "," + lat };

Můžete jej změnit na to to opravit všechny chyby sestavení: 

    var sp = new SearchParameters();
    sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
    sp.ScoringParameters =
        new[]
        {
            new ScoringParameter("featuredParam", new[] { "featured" }),
            new ScoringParameter("mapCenterParam", GeographyPoint.Create(lat, lon))
        };

### <a name="model-class-changes"></a>Změny třídy modelu
Kvůli změnám podpisu popsaným ve [změnách](#OperationMethodChanges) `Microsoft.Azure.Search.Models` metody operace bylo mnoho tříd v oboru názvů přejmenováno nebo odebráno. Například:

* `IndexDefinitionResponse`byla nahrazena`AzureOperationResponse<Index>`
* Přejmenování `DocumentSearchResponse` na `DocumentSearchResult`
* Přejmenování `IndexResult` na `IndexingResult`
* `Documents.Count()`nyní vrátí `long` s počtem dokumentů namísto`DocumentCountResponse`
* Přejmenování `IndexGetStatisticsResponse` na `IndexGetStatisticsResult`
* Přejmenování `IndexListResponse` na `IndexListResult`

Chcete-li `OperationResponse`shrnout, -odvozené třídy, které existovaly pouze pro obtékání objektu modelu byly odebrány. Zbývající třídy mají své přípony `Response` změnil `Result`z na .

#### <a name="example"></a>Příklad
Pokud váš kód vypadá takto:

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

Můžete jej změnit na to to opravit všechny chyby sestavení:

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

#### <a name="response-classes-and-ienumerable"></a>Třídy odpovědí a iEnumerable
Další změna, která může ovlivnit váš kód je, že `IEnumerable<T>`třídy odpovědí, které mají kolekce již implementovat . Místo toho můžete přistupovat k vlastnosti kolekce přímo. Pokud například váš kód vypadá takto:

    DocumentSearchResponse<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response)
    {
        Console.WriteLine(result.Document);
    }

Můžete jej změnit na to to opravit všechny chyby sestavení:

    DocumentSearchResult<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response.Results)
    {
        Console.WriteLine(result.Document);
    }

#### <a name="special-case-for-web-applications"></a>Zvláštní případ pro webové aplikace
Pokud máte webovou aplikaci, `DocumentSearchResponse` která serializuje přímo odeslat výsledky hledání do prohlížeče, budete muset změnit kód nebo výsledky nebudou serializovat správně. Pokud například váš kód vypadá takto:

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

Můžete ji změnit tak, `.Results` že získáte vlastnost odpovědi na vyhledávání a opravíte vykreslování výsledků hledání:

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

Budete muset hledat takové případy ve vašem kódu sami; **Kompilátor vás neupozorní,** `JsonResult.Data` protože `object`je typu .

### <a name="cloudexception-changes"></a>CloudException změny
Třída `CloudException` byla přesunuta `Hyak.Common` z oboru `Microsoft.Rest.Azure` názvů do oboru názvů. Jeho vlastnost `Error` byla také přejmenována na `Body`.

### <a name="searchserviceclient-and-searchindexclient-changes"></a>Změny služby SearchServiceClient a SearchIndexClient
Typ vlastnosti `Credentials` se změnil `SearchCredentials` z na `ServiceClientCredentials`základní třídu . Pokud potřebujete přístup `SearchCredentials` k `SearchIndexClient` a `SearchServiceClient`nebo , `SearchCredentials` použijte prosím nové zařízení.

Ve starších verzích sady `SearchServiceClient` SDK a `SearchIndexClient` měl `HttpClient` konstruktory, které přijaly parametr. Ty byly nahrazeny konstruktory, `HttpClientHandler` které berou `DelegatingHandler` a pole objektů. To usnadňuje instalaci vlastních obslužných rutin pro předběžné zpracování požadavků HTTP v případě potřeby.

Nakonec konstruktory, které `Uri` se `SearchCredentials` a změnily. Například pokud máte kód, který vypadá takto:

    var client =
        new SearchServiceClient(
            new SearchCredentials("abc123"),
            new Uri("http://myservice.search.windows.net"));

Můžete jej změnit na to to opravit všechny chyby sestavení:

    var client =
        new SearchServiceClient(
            new Uri("http://myservice.search.windows.net"),
            new SearchCredentials("abc123"));

Všimněte si také, že typ parametru pověření byl změněn na `ServiceClientCredentials`. To pravděpodobně neovlivní váš `SearchCredentials` kód, `ServiceClientCredentials`protože je odvozen od .

### <a name="passing-a-request-id"></a>Předání ID žádosti
Ve starších verzích sady SDK můžete nastavit ID požadavku na `SearchServiceClient` nebo `SearchIndexClient` a bude zahrnuto do každého požadavku rozhraní REST API. To je užitečné při řešení problémů s vyhledávací službou, pokud potřebujete kontaktovat podporu. Je však užitečnější nastavit jedinečné ID požadavku pro každou operaci, nikoli použít stejné ID pro všechny operace. Z tohoto důvodu `SetClientRequestId` metody `SearchServiceClient` `SearchIndexClient` a byly odstraněny. Místo toho můžete předat ID požadavku na každou `SearchRequestOptions` metodu operace prostřednictvím volitelného parametru.

> [!NOTE]
> V budoucí verzi sady SDK přidáme nový mechanismus pro nastavení ID požadavku globálně na klientských objektech, který je konzistentní s přístupem používaným jinými sadami Azure SDK.
> 
> 

### <a name="example"></a>Příklad
Pokud máte kód, který vypadá takto:

    client.SetClientRequestId(Guid.NewGuid());
    ...
    long count = client.Documents.Count();

Můžete jej změnit na to to opravit všechny chyby sestavení:

    long count = client.Documents.Count(new SearchRequestOptions(requestId: Guid.NewGuid()));

### <a name="interface-name-changes"></a>Změny názvu rozhraní
Všechny názvy rozhraní operační skupiny se změnily tak, aby byly konzistentní s odpovídajícími názvy vlastností:

* Typ byl `ISearchServiceClient.Indexes` přejmenován z `IIndexOperations` `IIndexesOperations`na .
* Typ byl `ISearchServiceClient.Indexers` přejmenován z `IIndexerOperations` `IIndexersOperations`na .
* Typ byl `ISearchServiceClient.DataSources` přejmenován z `IDataSourceOperations` `IDataSourcesOperations`na .
* Typ byl `ISearchIndexClient.Documents` přejmenován z `IDocumentOperations` `IDocumentsOperations`na .

Tato změna pravděpodobně neovlivní váš kód, pokud jste pro testovací účely nevytvořili mocks těchto rozhraní.

<a name="BugFixesV1"></a>

## <a name="bug-fixes-in-version-11"></a>Opravy chyb ve verzi 1.1
Ve starších verzích sady Azure Search .NET SDK došlo k chybě týkající se serializace vlastních tříd modelu. K chybě může dojít, pokud jste vytvořili vlastní třídu modelu s vlastností typu hodnoty s hodnotou, kterou nelze hodnotit.

### <a name="steps-to-reproduce"></a>Kroky k reprodukci
Vytvořte vlastní třídu modelu s vlastností typu hodnoty s hodnotou, která nesmožno neuplatní. Například přidejte `UnitCount` veřejnou `int` vlastnost `int?`typu namísto .

Pokud indexujete dokument s výchozí hodnotou tohoto typu `int`(například 0 pro ), bude mít pole ve službě Azure Search hodnotu null. Pokud následně hledáte tento `Search` dokument, `JsonSerializationException` volání vyvolá stížnost, `null` že `int`jej nelze převést na .

Filtry také nemusí fungovat podle očekávání, protože null byl zapsán do indexu namísto zamýšlené hodnoty.

### <a name="fix-details"></a>Oprava podrobností
Tento problém jsme opravili ve verzi 1.1 sady SDK. Nyní, pokud máte třídu modelu, jako je tento:

    public class Model
    {
        public string Key { get; set; }

        public int IntValue { get; set; }
    }

a nastavíte `IntValue` na 0, tato hodnota je nyní správně serializována jako 0 na drátě a uloženy jako 0 v indexu. Zakopnutí zaoblení také funguje podle očekávání.

Existuje jeden potenciální problém, který je třeba znát s tímto přístupem: Pokud používáte typ modelu s vlastností, kterou nelze utnout, musíte **zaručit,** že žádné dokumenty v indexu neobsahují hodnotu null pro odpovídající pole. Sada SDK ani rozhraní REST azure search vám to nepomůžou vynutit.

Nejedná se pouze o hypotetický problém: představte si situaci, kdy přidáte nové pole do stávajícího indexu typu `Edm.Int32`. Po aktualizaci definice indexu budou mít všechny dokumenty pro toto nové pole hodnotu null (protože všechny typy jsou ve službě Azure Search s možností null). Pokud pak použijete třídu modelu s vlastností `int` se zakázanou hodnotou null, při pokusu o načtení dokumentů dojde k vyvolání podobné výjimky `JsonSerializationException`:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Z tohoto důvodu stále doporučujeme použít typy s možnou hodnotou null ve třídách modelu jako osvědčený postup.

Další podrobnosti o této chybě a opravě naleznete [v tomto problému na GitHubu](https://github.com/Azure/azure-sdk-for-net/issues/1063).

