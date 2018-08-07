---
title: Jak používat Azure Search z aplikace .NET | Dokumentace Microsoftu
description: Jak používat Azure Search z aplikace .NET
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: brjohnst
ms.openlocfilehash: 19913f9c30992e833e5435af7066611d4662ba56
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39525633"
---
# <a name="how-to-use-azure-search-from-a-net-application"></a>Jak používat Azure Search z aplikace .NET
Tento článek je návod, který vám pomůžou začít pracovat s [Azure Search .NET SDK](https://aka.ms/search-sdk). Sady .NET SDK můžete použít k implementaci bohaté vyhledávací funkce do vaší aplikace pomocí Azure Search.

## <a name="whats-in-the-azure-search-sdk"></a>Novinky ve službě Azure vyhledat sady SDK
Sada SDK zahrnuje několik klientských knihoven, které vám umožní spravovat indexů, zdroje dat, indexery a synonymum mapy a také odesílání a spravovat dokumenty a spouštění dotazů, aniž by museli potýkat s podrobnostmi o protokolu HTTP a JSON. Tyto klientské knihovny se distribuují jako balíčky NuGet.

Hlavní balíček NuGet je `Microsoft.Azure.Search`, což je meta balíček, který obsahuje všechny ostatní balíčky jako závislosti. Tento balíček použijte, pokud právě začínáte nebo pokud víte, že vaše aplikace bude potřebovat všechny funkce služby Azure Search.

Další balíčky NuGet v sadě Windows SDK jsou:
 
  - `Microsoft.Azure.Search.Data`: Tento balíček použijte, pokud vyvíjíte aplikace .NET pomocí Azure Search a potřebujete jenom dotazování a aktualizaci dokumentů v indexů. Pokud je také potřeba vytvořit nebo aktualizovat indexů, map synonym, nebo další prostředky na úrovni služby, použijte `Microsoft.Azure.Search` balíček místo.
  - `Microsoft.Azure.Search.Service`: Tento balíček použijte, pokud vyvíjíte v rozhraní .NET pro správu indexů Azure Search, map synonym, indexery, zdroje dat nebo další prostředky na úrovni služby automation. Pokud potřebujete jenom k dokumentům dotazu nebo aktualizace v indexů, použijte `Microsoft.Azure.Search.Data` balíček místo. Pokud budete potřebovat všechny funkce služby Azure Search, použijte `Microsoft.Azure.Search` balíček místo.
  - `Microsoft.Azure.Search.Common`: Vyžadované knihoven Azure Search pro .NET běžných typů. By neměl muset použít tento balíček přímo v aplikaci; Je určená jenom pro použití jako závislost.

Definování různých klientských knihoven tříd jako `Index`, `Field`, a `Document`, stejně jako operace, jako jsou `Indexes.Create` a `Documents.Search` na `SearchServiceClient` a `SearchIndexClient` třídy. Tyto třídy jsou uspořádány do následujících oborů názvů:

* [Microsoft.Azure.Search](https://docs.microsoft.com/dotnet/api/microsoft.azure.search)
* [Microsoft.Azure.Search.Models](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models)

Aktuální verze Azure Search .NET SDK je teď obecně dostupná. Pokud chcete poskytnout zpětnou vazbu, abychom mohli začlenit v příští verzi, navštivte prosím náš [zpětnou vazbu stránky](https://feedback.azure.com/forums/263029-azure-search/).

Sady .NET SDK podporuje verzi `2017-11-11` z [REST API služby Azure Search](https://docs.microsoft.com/rest/api/searchservice/). Tato verze teď zahrnuje podporu pro synonyma, jakož i několik postupných vylepšení pro indexery. Funkce, které jsou ve verzi Preview *není* jsou součástí této verze, jako třeba podporu pro indexování pole JSON a souborů CSV v [ve verzi preview](search-api-2016-09-01-preview.md) a k dispozici prostřednictvím [verze 4.0 ve verzi preview sady .NET SDK](https://aka.ms/search-sdk-preview).

Tato sada SDK nepodporuje [operace správy](https://docs.microsoft.com/rest/api/searchmanagement/) jako je například vytváření a škálování vyhledávací služby a správu klíčů rozhraní API. Pokud potřebujete ke správě prostředků Search z aplikace .NET, můžete použít [Management SDK služby Azure Search .NET](https://aka.ms/search-mgmt-sdk).

## <a name="upgrading-to-the-latest-version-of-the-sdk"></a>Upgrade na nejnovější verzi sady SDK
Pokud už používáte starší verzi rozhraní .NET SDK služby Azure Search a chcete upgradovat na nové obecně dostupnou verzi [v tomto článku](search-dotnet-sdk-migration-version-5.md) vysvětluje, jak.

## <a name="requirements-for-the-sdk"></a>Požadavky pro sadu SDK
1. Sada Visual Studio 2017.
2. Vlastní službu Azure Search. Chcete-li použít sadu SDK, musíte název vaší služby a jeden nebo víc klíčů rozhraní API. [Vytvoření služby na portálu](search-create-service-portal.md) vám pomůže tyto kroky.
3. Stáhnout Azure Search .NET SDK [balíček NuGet](http://www.nuget.org/packages/Microsoft.Azure.Search) pomocí "Správa balíčků NuGet" v sadě Visual Studio. Právě vyhledejte název balíčku `Microsoft.Azure.Search` NuGet.org (nebo jednoho z jiných balíčků výše uvedené názvy, pokud potřebujete jenom podmnožinu funkcí).

Azure Search .NET SDK podporuje aplikace cílené na rozhraní .NET Framework 4.5.2 nebo novější, a také .NET Core.

## <a name="core-scenarios"></a>Základní scénáře
Existuje několik věcí, kterým je třeba provést v vaše vyhledávací aplikace. V tomto kurzu se budeme zabývat tyto základní scénáře:

* Vytvoření indexu
* Naplnění indexu s dokumenty
* Hledají se dokumenty pomocí fulltextového vyhledávání a filtrů

Ukázkový kód, který následuje ukazuje, každý z nich. Nebojte se využít fragmenty kódu ve své aplikaci.

### <a name="overview"></a>Přehled
Ukázkové aplikace společnost Microsoft bude konat vytvoří nový index s názvem "hotels", naplní ho s několika dokumenty a potom spustí některé vyhledávací dotazy. Tady je hlavní program zobrazující celkový tok:

```csharp
// This sample shows how to delete, create, upload documents and query an index
static void Main(string[] args)
{
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();

    SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

    Console.WriteLine("{0}", "Deleting index...\n");
    DeleteHotelsIndexIfExists(serviceClient);

    Console.WriteLine("{0}", "Creating index...\n");
    CreateHotelsIndex(serviceClient);

    ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(indexClient);

    ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

    RunQueries(indexClientForQueries);

    Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
    Console.ReadKey();
}
```

> [!NOTE]
> Úplný zdrojový kód ukázkové aplikace použité v tomto názorném postupu najdete na [Githubu](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).
> 
>

Provedeme to krok za krokem. Nejdřív je potřeba vytvořit novou `SearchServiceClient`. Tento objekt umožňuje spravovat indexy. Pokud chcete jeden vytvořit, je třeba zadat název vaší služby Azure Search, jakož i klíč správce rozhraní API. Můžete zadat tyto informace `appsettings.json` soubor [ukázkovou aplikaci](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).

```csharp
private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string adminApiKey = configuration["SearchServiceAdminApiKey"];

    SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
    return serviceClient;
}
```

> [!NOTE]
> Pokud zadáte nesprávné klíč (například klíč dotazů ve kterém byla požadována, klíč správce), `SearchServiceClient` vyvolá výjimku `CloudException` s chybou zpráva "Zakázáno", jako volání metody operace, při prvním `Indexes.Create`. V takovém případě pro vás, zkontrolujte naše klíč rozhraní API.
> 
> 

Další několik řádků volat metody pro vytvoření indexu s názvem "hotels", nejprve jeho odstraněním, pokud již existuje. Vám ukážeme tyto metody o něco později.

```csharp
Console.WriteLine("{0}", "Deleting index...\n");
DeleteHotelsIndexIfExists(serviceClient);

Console.WriteLine("{0}", "Creating index...\n");
CreateHotelsIndex(serviceClient);
```

V dalším kroku index musí být vyplněno. Chcete-li to provést, potřebujeme `SearchIndexClient`. Existují dva způsoby, jak získat jeden: tak, že se vytváří nebo voláním `Indexes.GetClient` na `SearchServiceClient`. Můžeme použít druhou možnost ke zvýšení pohodlí.

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

> [!NOTE]
> V typické vyhledávací aplikaci se o správu a naplňování indexu stará samostatná komponenta volaná dotazy vyhledávání. `Indexes.GetClient` je vhodné pro naplňování indexu, protože díky tomu není nutné poskytovat další `SearchCredentials`. Dělá to pomocí předání klíče správce, který jste použili pro vytvoření `SearchServiceClient`, službě `SearchIndexClient`. V části aplikace, který provádí dotazy, je nicméně lepší vytvořit `SearchIndexClient` přímo, abyste mohli předávat klíč dotazů místo klíče správce. To je v souladu s principem minimálního oprávnění a pomůže vám to lépe zabezpečit vaši aplikaci. Můžete najít další informace o klíčích a správce dotazů [tady](https://docs.microsoft.com/rest/api/searchservice/#authentication-and-authorization).
> 
> 

Když teď máme `SearchIndexClient`, jsme naplňte index. To se provádí tak, že vám ukážeme později.

```csharp
Console.WriteLine("{0}", "Uploading documents...\n");
UploadDocuments(indexClient);
```

A konečně spustit několik vyhledávacích dotazů a zobrazení výsledků. Tentokrát použijeme jiný `SearchIndexClient`:

```csharp
ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

RunQueries(indexClientForQueries);
```

My podnikneme na ně podívat `RunQueries` metoda později. Tady je kód pro vytvoření nového `SearchIndexClient`:

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "hotels", new SearchCredentials(queryApiKey));
    return indexClient;
}
```

Tentokrát doporučujeme používat klíč dotazů, protože jsme nepotřebují přístup pro zápis do indexu. Můžete zadat tyto informace `appsettings.json` soubor [ukázkovou aplikaci](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).

Pokud spustíte tuto aplikaci s platný název služby a klíče rozhraní API, výstup by měl vypadat takto:

    Deleting index...
    
    Creating index...
    
    Uploading documents...
    
    Waiting for documents to be indexed...
    
    Search the entire index for the term 'budget' and return only the hotelName field:
    
    Name: Roach Motel
    
    Apply a filter to the index to find hotels cheaper than $150 per night, and return the hotelId and description:
    
    ID: 2   Description: Cheapest hotel in town
    ID: 3   Description: Close to town hall and the river
    
    Search the entire index, order by a specific field (lastRenovationDate) in descending order, take the top two results, and show only hotelName and lastRenovationDate:
    
    Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
    Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00
    
    Search the entire index for the term 'motel':
    
    ID: 2   Base rate: 79.99        Description: Cheapest hotel in town     Description (French): Hôtel le moins cher en ville      Name: Roach Motel       Category: Budget        Tags: [motel, budget]   Parking included: yes   Smoking allowed: yes    Last renovated on: 4/28/1982 12:00:00 AM +00:00 Rating: 1/5     Location: Latitude 49.678581, longitude -122.131577
    
    Complete.  Press any key to end application...

Úplný zdrojový kód aplikace najdete na konci tohoto článku.

V dalším kroku my podnikneme bližší pohled na každé z metod volá `Main`.

### <a name="creating-an-index"></a>Vytvoření indexu
Po vytvoření `SearchServiceClient`, je dalším krokem `Main` nemá je odstranit index "hotels", pokud již existuje. Který provádí následující metodu:

```csharp
private static void DeleteHotelsIndexIfExists(SearchServiceClient serviceClient)
{
    if (serviceClient.Indexes.Exists("hotels"))
    {
        serviceClient.Indexes.Delete("hotels");
    }
}
```

Tato metoda používá daný `SearchServiceClient` Pokud chcete zkontrolovat, zda existuje index a pokud ano, odstraňte ho.

> [!NOTE]
> Příklad kódu v tomto článku používá pro jednoduchost synchronní metody sady Azure Search .NET SDK. Doporučujeme ve vlastních aplikacích použít asynchronní metody, aby aplikace byly škálovatelné a dobře reagovaly. Například ve výše uvedené metody můžete použít `ExistsAsync` a `DeleteAsync` místo `Exists` a `Delete`.
> 
> 

Dále `Main` vytvoří nový index "hotels" pomocí volání této metody:

```csharp
private static void CreateHotelsIndex(SearchServiceClient serviceClient)
{
    var definition = new Index()
    {
        Name = "hotels",
        Fields = FieldBuilder.BuildForType<Hotel>()
    };

    serviceClient.Indexes.Create(definition);
}
```

Tato metoda vytvoří nový `Index` objektu se seznamem `Field` objekty, které definuje schéma nového indexu. Každé pole má název, datový typ a několik atributů, které definují chování vyhledávání. `FieldBuilder` Třída používá reflexi k vytvoření seznamu `Field` objekty indexu, kontrolou veřejné vlastnosti a atributy dané `Hotel` třída modelu. Provedeme na ně podívat `Hotel` třídy později.

> [!NOTE]
> Vždy můžete vytvořit seznam `Field` objekty přímo namísto použití `FieldBuilder` v případě potřeby. Například možná nebudete chtít používat třídu modelu nebo budete muset použít existující třídu modelu, který nechcete upravit přidáním atributů.
>
> 

Kromě polí můžete také přidat bodovací profily, moduly pro návrhy nebo možnosti CORS do indexu (tyto jsou vynechány z ukázky pro zkrácení). Můžete najít další informace o objektu indexu a v jejích částí [odkaz na sadu SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index#microsoft_azure_search_models_index), a v [REST API služby Azure Search odkaz](https://docs.microsoft.com/rest/api/searchservice/).

### <a name="populating-the-index"></a>Naplnění indexu
Na další krok v `Main` je k naplnění indexu nově vytvořený. To se provádí v následující metodu:

```csharp
private static void UploadDocuments(ISearchIndexClient indexClient)
{
    var hotels = new Hotel[]
    {
        new Hotel()
        { 
            HotelId = "1", 
            BaseRate = 199.0, 
            Description = "Best hotel in town",
            DescriptionFr = "Meilleur hôtel en ville",
            HotelName = "Fancy Stay",
            Category = "Luxury", 
            Tags = new[] { "pool", "view", "wifi", "concierge" },
            ParkingIncluded = false, 
            SmokingAllowed = false,
            LastRenovationDate = new DateTimeOffset(2010, 6, 27, 0, 0, 0, TimeSpan.Zero), 
            Rating = 5, 
            Location = GeographyPoint.Create(47.678581, -122.131577)
        },
        new Hotel()
        { 
            HotelId = "2", 
            BaseRate = 79.99,
            Description = "Cheapest hotel in town",
            DescriptionFr = "Hôtel le moins cher en ville",
            HotelName = "Roach Motel",
            Category = "Budget",
            Tags = new[] { "motel", "budget" },
            ParkingIncluded = true,
            SmokingAllowed = true,
            LastRenovationDate = new DateTimeOffset(1982, 4, 28, 0, 0, 0, TimeSpan.Zero),
            Rating = 1,
            Location = GeographyPoint.Create(49.678581, -122.131577)
        },
        new Hotel() 
        { 
            HotelId = "3", 
            BaseRate = 129.99,
            Description = "Close to town hall and the river"
        }
    };

    var batch = IndexBatch.Upload(hotels);

    try
    {
        indexClient.Documents.Index(batch);
    }
    catch (IndexBatchException e)
    {
        // Sometimes when your Search service is under load, indexing will fail for some of the documents in
        // the batch. Depending on your application, you can take compensating actions like delaying and
        // retrying. For this simple demo, we just log the failed document keys and continue.
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

    Console.WriteLine("Waiting for documents to be indexed...\n");
    Thread.Sleep(2000);
}
```

Tato metoda má čtyři části. První vytvoří pole `Hotel` objekty, které bude sloužit jako naše vstupní data a nahrát do indexu. Tato data jsou pevně zakódované pro zjednodušení. Ve své aplikaci bude vaše data pravděpodobně pocházet z externího zdroje dat jako je SQL database.

Vytvoří druhou částí `IndexBatch` obsahující dokumenty. Zadejte operace, které chcete použít pro služby batch v době vytvoření, v tomto případě voláním `IndexBatch.Upload`. Batch se pak nahrají do indexu Azure Search `Documents.Index` metody.

> [!NOTE]
> V tomto příkladu jsme se právě odesílá dokumenty. Pokud chcete sloučit změny do stávajících dokumentů a odstraňování dokumentů, dávky můžete vytvořit pomocí volání `IndexBatch.Merge`, `IndexBatch.MergeOrUpload`, nebo `IndexBatch.Delete` místo. Můžete také kombinovat různé operace v jedné dávce voláním `IndexBatch.New`, který vezme kolekci `IndexAction` objektů, z nichž každý říká službě Azure Search k provedení určité operace v dokumentu. Můžete vytvořit každý `IndexAction` s vlastní operace voláním odpovídající metody `IndexAction.Merge`, `IndexAction.Upload`, a tak dále.
> 
> 

Třetí část této metody je blok catch, který zpracovává s případem důležitých chyb pro indexování. Pokud služba Azure Search při indexování některých dokumentů v dávce selže, `Documents.Index` vyvolá výjimku `IndexBatchException`. To může nastat v případě indexování dokumentů, zatímco je služba velmi zatížená. **Důrazně doporučujeme v kódu explicitně zpracovávat tento případ.** Indexování dokumentů, které selhaly, můžete odložit a poté zkusit znovu, nebo v závislosti na požadavcích vaší aplikace na konzistenci dat provést něco jiného.

> [!NOTE]
> Můžete použít `FindFailedActionsToRetry` metoda vytvořit nový list obsahující jenom akce, které se nepodařilo v předchozím volání `Index`. Metoda je popsána [tady](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexbatchexception#Microsoft_Azure_Search_IndexBatchException_FindFailedActionsToRetry_Microsoft_Azure_Search_Models_IndexBatch_System_String_) a diskusi o tom, jak správně používat [na StackOverflow](http://stackoverflow.com/questions/40012885/azure-search-net-sdk-how-to-use-findfailedactionstoretry).
>
>

Nakonec `UploadDocuments` metoda 2 sekundy odloží. Indexování ve službě Azure Search probíhá asynchronně, takže ukázková aplikace musí chvíli počkat a ujistit se, že jsou dokumenty dostupné pro vyhledávání. Tato odložení se obvykle používají pouze v ukázkových aplikacích a při testech.

#### <a name="how-the-net-sdk-handles-documents"></a>Jak .NET SDK zpracovává dokumenty
Možná vás zajímá, jak může .NET SDK služby Azure Search odesílat do indexu instance uživatelsky definované třídy, jako třeba `Hotel`. Chcete-li odpověď na tuto otázku, Podívejme se na `Hotel` třídy:

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Spatial;
using Newtonsoft.Json;

// The SerializePropertyNamesAsCamelCase attribute is defined in the Azure Search .NET SDK.
// It ensures that Pascal-case property names in the model class are mapped to camel-case
// field names in the index.
[SerializePropertyNamesAsCamelCase]
public partial class Hotel
{
    [System.ComponentModel.DataAnnotations.Key]
    [IsFilterable]
    public string HotelId { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }

    [IsSearchable]
    public string Description { get; set; }

    [IsSearchable]
    [Analyzer(AnalyzerName.AsString.FrLucene)]
    [JsonProperty("description_fr")]
    public string DescriptionFr { get; set; }

    [IsSearchable, IsFilterable, IsSortable]
    public string HotelName { get; set; }

    [IsSearchable, IsFilterable, IsSortable, IsFacetable]
    public string Category { get; set; }

    [IsSearchable, IsFilterable, IsFacetable]
    public string[] Tags { get; set; }

    [IsFilterable, IsFacetable]
    public bool? ParkingIncluded { get; set; }

    [IsFilterable, IsFacetable]
    public bool? SmokingAllowed { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public DateTimeOffset? LastRenovationDate { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public int? Rating { get; set; }

    [IsFilterable, IsSortable]
    public GeographyPoint Location { get; set; }
}
```

Nejprve si všimněte, že každá veřejná vlastnost třídy `Hotel` odpovídá poli v definici indexu s jedním zásadním rozdílem: Název každého pole začíná malým písmenem („CamelCase“), zatímco název každé veřejné vlastnosti třídy `Hotel` začíná velkým písmenem („PascalCase“). To je běžný scénář v .NET aplikacích provádějících datové vazby, kde je cílové schéma mimo kontrolu vývojáře aplikace. Místo porušování směrnic pojmenování .NET psaním názvů vlastností ve stylu CamelCase můžete pomocí atributu `[SerializePropertyNamesAsCamelCase]` říct sadě SDK, aby mapovala názvy vlastností na CamelCase automaticky.

> [!NOTE]
> .NET SDK služby Azure Search používá k serializaci a deserializaci vlastních objektů modelu do a z JSON knihovnu [NewtonSoft JSON.NET](http://www.newtonsoft.com/json/help/html/Introduction.htm). V případě potřeby lze serializaci přizpůsobit. Další podrobnosti najdete v tématu [vlastní serializace pomocí technologie JSON.NET](#JsonDotNet).
> 
> 

Druhou věcí a Všimněte si, jako jsou atributy `IsFilterable`, `IsSearchable`, `Key`, a `Analyzer` , který uspořádání každé veřejné vlastnosti. Tyto atributy mapují přímo [odpovídající atributy indexu Azure Search](https://docs.microsoft.com/rest/api/searchservice/create-index#request). `FieldBuilder` Tato třída používá k vytvoření definice polí pro index.

Třetí důležité o `Hotel` třídy jsou datové typy veřejných vlastností. .NET typy těchto vlastností se mapují na odpovídající typy polí v definici indexu. Například řetězcová vlastnost `Category` se mapuje na pole `category`, které je typu `Edm.String`. Podobná mapování typu probíhají mezi `bool?` a `Edm.Boolean`, `DateTimeOffset?` a `Edm.DateTimeOffset` atd. Konkrétní pravidla pro mapování typu jsou popsaná u metody `Documents.Get` v tématu [Reference k sadě .NET SDK služby Azure Search](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations#Microsoft_Azure_Search_IDocumentsOperations_GetWithHttpMessagesAsync__1_System_String_System_Collections_Generic_IEnumerable_System_String__Microsoft_Azure_Search_Models_SearchRequestOptions_System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_). `FieldBuilder` Třídy postará za vás toto mapování, ale stále může být užitečné k pochopení případ potřeby řešení potíží, serializace.

Tato možnost používat vaše vlastní třídy jako dokumenty funguje v obou směrech; Můžete také načíst výsledky hledání a uvidíme v další části jsou sady SDK automaticky deserializovala do požadovaného typu podle vašeho výběru.

> [!NOTE]
> .NET SDK služby Azure Search také podporuje dynamicky typované dokumenty pomocí třídy `Document`, která zajišťuje mapování klíč-hodnota názvů polí na hodnoty polí. To je užitečné v situacích, kdy v době navrhování neznáte schéma indexu nebo kde by vázání na konkrétní třídy modelu bylo nepraktické. Všechny metody v sadě SDK, které pracují s dokumenty, mají přetížení, které pracují se třídou `Document`, ale i přetížení silně závislá na typu, která přebírají parametr obecného typu. Pouze ty druhé se používají ve vzorovém kódu v tomto kurzu. `Document` Třída dědí z `Dictionary<string, object>`. Další podrobnosti můžete najít [tady](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.document#microsoft_azure_search_models_document).
> 
> 

**Proč byste měli používat datové typy s možnou hodnotou null**

Při navrhování vlastních tříd modelu pro mapování na index Azure Search doporučujeme deklarovat vlastnosti typů hodnot, jako jsou `bool` nebo `int`, s možnou hodnotou null (například `bool?` místo `bool`). Pokud použijete vlastnost se zakázanou hodnotou null, musíte **zajistit**, aby žádné dokumenty v indexu neobsahovaly pro odpovídající pole hodnotu null. Sada SDK, ani služba Azure Search vám s tím nepomůže.

Nejedná se pouze o hypotetický problém: představte si situaci, kdy přidáte nové pole do stávajícího indexu typu `Edm.Int32`. Po aktualizaci definice indexu budou mít všechny dokumenty pro toto nové pole hodnotu null (protože všechny typy jsou ve službě Azure Search s možností null). Pokud pak použijete třídu modelu s vlastností `int` se zakázanou hodnotou null, při pokusu o načtení dokumentů dojde k vyvolání podobné výjimky `JsonSerializationException`:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Z tohoto důvodu doporučujeme jako osvědčený postup používat ve třídách modelu typy s možnou hodnotou null.

<a name="JsonDotNet"></a>

#### <a name="custom-serialization-with-jsonnet"></a>Vlastní serializace pomocí technologie JSON.NET
Sada SDK používá technologii JSON.NET pro serializaci a deserializaci dokumenty. Můžete přizpůsobit serializaci a deserializaci v případě potřeby tak, že definujete vlastní `JsonConverter` nebo `IContractResolver` (najdete v článku [JSON.NET dokumentaci](http://www.newtonsoft.com/json/help/html/Introduction.htm) další podrobnosti). To může být užitečné, pokud chcete upravit existující třídu modelu z vaší aplikace pro použití se službou Azure Search a další pokročilé scénáře. Například vlastní serializace vám umožní:

* Zahrnout nebo vyloučit určité vlastnosti třídy modelu ukládat jako pole dokumentů.
* Mapování mezi názvy vlastností v kódu a názvy polí v indexu.
* Vytvoření vlastních atributů, které lze použít pro mapování vlastností pole dokumentů.

Můžete najít příklady implementace vlastní serializace při testech jednotek pro Azure Search .NET SDK na Githubu. Dobrým výchozím bodem je [této složky](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/Search/Search.Tests/Tests/Models). Obsahuje třídy, které používají vlastní serializace testy.

### <a name="searching-for-documents-in-the-index"></a>Hledají se dokumenty v indexu
Posledním krokem v ukázkové aplikaci je vyhledání některé dokumenty v indexu. Následující metoda provede následující:

```csharp
private static void RunQueries(ISearchIndexClient indexClient)
{
    SearchParameters parameters;
    DocumentSearchResult<Hotel> results;

    Console.WriteLine("Search the entire index for the term 'budget' and return only the hotelName field:\n");

    parameters =
        new SearchParameters()
        {
            Select = new[] { "hotelName" }
        };

    results = indexClient.Documents.Search<Hotel>("budget", parameters);

    WriteDocuments(results);

    Console.Write("Apply a filter to the index to find hotels cheaper than $150 per night, ");
    Console.WriteLine("and return the hotelId and description:\n");

    parameters =
        new SearchParameters()
        {
            Filter = "baseRate lt 150",
            Select = new[] { "hotelId", "description" }
        };

    results = indexClient.Documents.Search<Hotel>("*", parameters);

    WriteDocuments(results);

    Console.Write("Search the entire index, order by a specific field (lastRenovationDate) ");
    Console.Write("in descending order, take the top two results, and show only hotelName and ");
    Console.WriteLine("lastRenovationDate:\n");

    parameters =
        new SearchParameters()
        {
            OrderBy = new[] { "lastRenovationDate desc" },
            Select = new[] { "hotelName", "lastRenovationDate" },
            Top = 2
        };

    results = indexClient.Documents.Search<Hotel>("*", parameters);

    WriteDocuments(results);

    Console.WriteLine("Search the entire index for the term 'motel':\n");

    parameters = new SearchParameters();
    results = indexClient.Documents.Search<Hotel>("motel", parameters);

    WriteDocuments(results);
}
```

Pokaždé, když se provede dotaz, tato metoda nejprve vytvoří nové `SearchParameters` objektu. To slouží k určení dalších možností pro dotaz, jako je například řazení, filtrování, stránkování a "faceting". V této metodě jsme nastavení `Filter`, `Select`, `OrderBy`, a `Top` vlastnost pro různé dotazy. Všechny `SearchParameters` jsou popsané vlastnosti [tady](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters).

Dalším krokem je ve skutečnosti spustit vyhledávací dotaz. To se provádí pomocí `Documents.Search` metody. Pro každý dotaz předáme hledaný text, který se použije jako řetězec (nebo `"*"` Pokud neexistuje žádný hledaný text), a navíc parametry hledání vytvořili dříve. Můžeme také určit `Hotel` jako parametr typu pro `Documents.Search`, který dává pokyn sady SDK k deserializaci dokumenty ve výsledcích hledání do objektů typu `Hotel`.

> [!NOTE]
> Můžete najít další informace o syntaxe výrazu dotazu hledání [tady](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search).
> 
> 

Nakonec po jednotlivých dotazů tato metoda projde všechny shody ve výsledcích hledání, tisk do konzoly pro každý dokument:

```csharp
private static void WriteDocuments(DocumentSearchResult<Hotel> searchResults)
{
    foreach (SearchResult<Hotel> result in searchResults.Results)
    {
        Console.WriteLine(result.Document);
    }

    Console.WriteLine();
}
```

Pojďme zase bližší pohled na všechny dotazy. Tady je kód pro spuštění prvního dotazu:

```csharp
parameters =
    new SearchParameters()
    {
        Select = new[] { "hotelName" }
    };

results = indexClient.Documents.Search<Hotel>("budget", parameters);

WriteDocuments(results);
```

V tomto případě jsme už hledající hotely, které odpovídají slova "budget", a chceme se vrátit pouze hotelu názvy, jak jsou určené `Select` parametru. Tady jsou výsledky:

    Name: Roach Motel

V dalším kroku chceme nalezení hotelů noční sazba míň než 150 USD a vrátit pouze hotelu ID a description:

```csharp
parameters =
    new SearchParameters()
    {
        Filter = "baseRate lt 150",
        Select = new[] { "hotelId", "description" }
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);
```

Tento dotaz používá OData `$filter` výrazu, `baseRate lt 150`, chcete-li filtrovat dokumenty v indexu. Můžete najít další informace o syntaxi OData, která podporuje Azure Search [tady](https://docs.microsoft.com/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search).

Tady jsou výsledky dotazu:

    ID: 2   Description: Cheapest hotel in town
    ID: 3   Description: Close to town hall and the river

V dalším kroku chceme nalezení hotelů horní dva, které mají byl naposledy renovovanou a zobrazit název hotelu a datum posledního renovace. Zde je kód: 

```csharp
parameters =
    new SearchParameters()
    {
        OrderBy = new[] { "lastRenovationDate desc" },
        Select = new[] { "hotelName", "lastRenovationDate" },
        Top = 2
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);
```

V tomto případě jsme znovu pomocí syntaxe OData k určení `OrderBy` parametr jako `lastRenovationDate desc`. Také nastavíme `Top` na 2, aby nám pouze získat první dva dokumenty. Jak jsme předtím nastavili `Select` k určení pole, která má být vrácen.

Tady jsou výsledky:

    Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
    Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00

Nakonec chceme nalezení hotelů všechny, které odpovídají slova "motel":

```csharp
parameters = new SearchParameters();
results = indexClient.Documents.Search<Hotel>("motel", parameters);

WriteDocuments(results);
```

A tady jsou výsledky, které zahrnují všechna pole, protože jsme nezadali `Select` vlastnost:

    ID: 2   Base rate: 79.99        Description: Cheapest hotel in town     Description (French): Hôtel le moins cher en ville      Name: Roach Motel       Category: Budget        Tags: [motel, budget]   Parking included: yes   Smoking allowed: yes    Last renovated on: 4/28/1982 12:00:00 AM +00:00 Rating: 1/5     Location: Latitude 49.678581, longitude -122.131577

Tento krok dokončení tohoto kurzu, ale nepřestávejte tady. **Další kroky** poskytuje další zdroje dalších informací o Azure Search.

## <a name="next-steps"></a>Další postup
* Projděte si referenční materiály pro [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search) a [REST API](https://docs.microsoft.com/rest/api/searchservice/).
* Kontrola [zásady vytváření názvů](https://docs.microsoft.com/rest/api/searchservice/Naming-rules) další pravidla pro pojmenovávání různé objekty.
* Kontrola [podporované datové typy](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types) ve službě Azure Search.
