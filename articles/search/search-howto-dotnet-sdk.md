---
title: Použití Azure Cognitive Search v rozhraní .NET
titleSuffix: Azure Cognitive Search
description: Zjistěte, jak používat Azure Cognitive Search v aplikaci .NET pomocí Jazyka C# a sady .NET SDK. Úlohy založené na kódu zahrnují připojení ke službě, obsah indexu a dotaz na index.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.devlang: dotnet
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: b31a4e40c1e9095499faf265673ab4213ad6bde0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283066"
---
# <a name="how-to-use-azure-cognitive-search-from-a-net-application"></a>Jak používat Azure Cognitive Search z aplikace .NET

Tento článek je návod, jak vám zprovoznit azure [kognitivní vyhledávání .NET SDK](https://aka.ms/search-sdk). Pomocí sady .NET SDK můžete implementovat bohaté možnosti vyhledávání ve vaší aplikaci pomocí Azure Cognitive Search.

## <a name="whats-in-the-azure-cognitive-search-sdk"></a>Co je v Azure Cognitive Search SDK
Sada SDK se skládá z několika klientských knihoven, které umožňují spravovat indexy, zdroje dat, indexery a mapy synonym, stejně jako nahrát a spravovat dokumenty a spouštět dotazy, aniž byste museli řešit podrobnosti protokolu HTTP a JSON. Tyto klientské knihovny jsou všechny distribuovány jako balíčky NuGet.

Hlavní balíček NuGet je `Microsoft.Azure.Search`, což je meta balíček, který obsahuje všechny ostatní balíčky jako závislosti. Tento balíček použijte, pokud teprve začínáte nebo pokud víte, že vaše aplikace bude potřebovat všechny funkce Azure Cognitive Search.

Ostatní balíčky NuGet v sadě SDK jsou:
 
  - `Microsoft.Azure.Search.Data`: Tento balíček použijte, pokud vyvíjíte aplikaci .NET pomocí Azure Cognitive Search a stačí zadat dotaz nebo aktualizovat dokumenty v indexech. Pokud také potřebujete vytvořit nebo aktualizovat indexy, mapy synonym nebo jiné `Microsoft.Azure.Search` prostředky na úrovni služeb, použijte balíček.
  - `Microsoft.Azure.Search.Service`: Tento balíček použijte, pokud vyvíjíte automatizaci v rozhraní .NET ke správě indexů Azure Cognitive Search, map synonym, indexerů, zdrojů dat nebo jiných prostředků na úrovni služeb. Pokud potřebujete pouze dotaz nebo aktualizaci dokumentů v `Microsoft.Azure.Search.Data` indexech, použijte balíček místo. Pokud potřebujete všechny funkce Azure Cognitive Search, `Microsoft.Azure.Search` použijte balíček místo.
  - `Microsoft.Azure.Search.Common`: Běžné typy, které potřebují knihovny Azure Cognitive Search .NET. Tento balíček není nutné používat přímo ve vaší aplikaci. Je určen pouze pro použití jako závislost.

Různé klientské knihovny `Index`definují třídy jako `Field`, `Documents.Search` , `SearchServiceClient` `SearchIndexClient` a `Document`, stejně jako operace jako `Indexes.Create` a na třídách a. Tyto třídy jsou uspořádány do následujících oborů názvů:

* [Microsoft.Azure.Search](https://docs.microsoft.com/dotnet/api/microsoft.azure.search)
* [Microsoft.Azure.Search.Models](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models)

Pokud chcete poskytnout zpětnou vazbu pro budoucí aktualizaci sady SDK, podívejte se na naši [stránku se zpětnou vazbou](https://feedback.azure.com/forums/263029-azure-search/) nebo vytvořte problém na [GitHubu](https://github.com/azure/azure-sdk-for-net/issues) a uveďte "Azure Cognitive Search" v názvu problému.

Sada .NET SDK `2019-05-06` podporuje verzi [rozhraní REST rozhraní AZURE Cognitive Search](https://docs.microsoft.com/rest/api/searchservice/). Tato verze zahrnuje podporu pro [komplexní typy](search-howto-complex-data-types.md), [Obohacení AI](cognitive-search-concept-intro.md), [automatické dokončování](https://docs.microsoft.com/rest/api/searchservice/autocomplete)a [režim analýzy JsonLines](search-howto-index-json-blobs.md) při indexování objektů BLOB Azure. 

Tato sada SDK nepodporuje [operace správy,](https://docs.microsoft.com/rest/api/searchmanagement/) jako je vytváření a škálování vyhledávacích služeb a správa klíčů rozhraní API. Pokud potřebujete spravovat prostředky vyhledávání z aplikace .NET, můžete použít [azure cognitive search .NET Management SDK](https://aka.ms/search-mgmt-sdk).

## <a name="upgrading-to-the-latest-version-of-the-sdk"></a>Upgrade na nejnovější verzi sady SDK
Pokud už používáte starší verzi sady Azure Cognitive Search .NET SDK a chcete upgradovat na nejnovější obecně dostupnou verzi, [tento článek](search-dotnet-sdk-migration-version-9.md) vysvětluje, jak.

## <a name="requirements-for-the-sdk"></a>Požadavky na sdk
1. Visual Studio 2017 nebo novější.
2. Vaše vlastní služba Azure Cognitive Search. Chcete-li používat sadu SDK, budete potřebovat název služby a jeden nebo více klíčů rozhraní API. [Vytvoření služby na portálu](search-create-service-portal.md) vám pomůže s těmito kroky.
3. Stáhněte si balíček Azure Cognitive Search .NET SDK [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Search) pomocí "Spravovat balíčky NuGet" v sadě Visual Studio. Stačí vyhledat název `Microsoft.Azure.Search` balíčku na NuGet.org (nebo jeden z výše uvedených názvů balíčků, pokud potřebujete pouze podmnožinu funkce).

Sada Azure Cognitive Search .NET SDK podporuje aplikace, které cílí na rozhraní .NET Framework 4.5.2 a vyšší, stejně jako na rozhraní .NET Core 2.0 a vyšší.

## <a name="core-scenarios"></a>Základní scénáře
Existuje několik věcí, které budete muset udělat ve své vyhledávací aplikaci. V tomto kurzu se budeme zabývat těmito základními scénáři:

* Vytvoření indexu
* Naplnění indexu dokumenty
* Hledání dokumentů pomocí fulltextového vyhledávání a filtrů

Následující ukázkový kód ilustruje každý z těchto scénářů. Nebojte se použít fragmenty kódu ve vlastní aplikaci.

### <a name="overview"></a>Přehled
Ukázková aplikace, kterou budeme zkoumat, vytvoří nový index s názvem "hotely", naplní jej několika dokumenty a pak provede některé vyhledávací dotazy. Zde je hlavní program, který ukazuje celkový tok:

```csharp
// This sample shows how to delete, create, upload documents and query an index
static void Main(string[] args)
{
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();

    SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

    string indexName = configuration["SearchIndexName"];

    Console.WriteLine("{0}", "Deleting index...\n");
    DeleteIndexIfExists(indexName, serviceClient);

    Console.WriteLine("{0}", "Creating index...\n");
    CreateIndex(indexName, serviceClient);

    ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);

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

Projdeme si tím krok za krokem. Nejprve musíme vytvořit `SearchServiceClient`nový . Tento objekt umožňuje spravovat indexy. Chcete-li vytvořit jeden, musíte zadat název služby Azure Cognitive Search, stejně jako klíč rozhraní API pro správu. Tyto informace můžete zadat `appsettings.json` do souboru [ukázkové aplikace](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).

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
> Pokud zadáte nesprávný klíč (například klíč dotazu, kde `SearchServiceClient` byl vyžadován `CloudException` klíč správce), vyvolá se s chybovou zprávou "Zakázáno" při prvním volání metody operace, například `Indexes.Create`. Pokud se vám to stane, zkontrolujte náš klíč rozhraní API.
> 
> 

Několik dalších řádků volání metody k vytvoření indexu s názvem "hotely", jeho odstranění nejprve, pokud již existuje. Tyto metody projdeme o něco později.

```csharp
Console.WriteLine("{0}", "Deleting index...\n");
DeleteIndexIfExists(indexName, serviceClient);

Console.WriteLine("{0}", "Creating index...\n");
CreateIndex(indexName, serviceClient);
```

Dále musí být index naplněn. Chcete-li naplnit index, `SearchIndexClient`budeme potřebovat . Existují dva způsoby, jak získat jeden: tím, že staví, nebo voláním `Indexes.GetClient` na `SearchServiceClient`. Používáme druhé pro pohodlí.

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
```

> [!NOTE]
> V typické vyhledávací aplikaci může být správa indexu a základní soubor zpracovány samostatnou komponentou z vyhledávacích dotazů. `Indexes.GetClient`je vhodný pro vyplnění indexu, protože vám ušetří potíže `SearchCredentials`s poskytováním dalších . Dělá to pomocí předání klíče správce, který jste použili pro vytvoření `SearchServiceClient`, službě `SearchIndexClient`. Však v části aplikace, která provádí dotazy, je lepší `SearchIndexClient` vytvořit přímo tak, že můžete předat klíč dotazu, který umožňuje pouze číst data, namísto klíče správce. To je konzistentní s principem minimálního oprávnění a pomůže vám to lépe zabezpečit vaši aplikaci. Další informace o klíčích správce a klávesách dotazů naleznete [zde](https://docs.microsoft.com/rest/api/searchservice/#authentication-and-authorization).
> 
> 

Teď, když `SearchIndexClient`máme , můžeme naplnit index. Index populace se provádí jinou metodou, kterou budeme procházet později.

```csharp
Console.WriteLine("{0}", "Uploading documents...\n");
UploadDocuments(indexClient);
```

Nakonec provedeme několik vyhledávacích dotazů a zobrazíme výsledky. Tentokrát použijeme jiný `SearchIndexClient`:

```csharp
ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(indexName, configuration);

RunQueries(indexClientForQueries);
```

Na `RunQueries` metodu se podíváme blíže později. Zde je kód pro `SearchIndexClient`vytvoření nového :

```csharp
private static SearchIndexClient CreateSearchIndexClient(string indexName, IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, indexName, new SearchCredentials(queryApiKey));
    return indexClient;
}
```

Tentokrát používáme klíč dotazu, protože nepotřebujeme přístup pro zápis do indexu. Tyto informace můžete zadat `appsettings.json` do souboru [ukázkové aplikace](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).

Pokud spustíte tuto aplikaci s platným názvem služby a klíči rozhraní API, výstup by měl vypadat jako v tomto příkladu: (Některé výstupkonzoly byl nahrazen "..." pro ilustrační účely.)

    Deleting index...

    Creating index...

    Uploading documents...

    Waiting for documents to be indexed...

    Search the entire index for the term 'motel' and return only the HotelName field:

    Name: Secret Point Motel

    Name: Twin Dome Motel


    Apply a filter to the index to find hotels with a room cheaper than $100 per night, and return the hotelId and description:

    HotelId: 1
    Description: The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Times Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.

    HotelId: 2
    Description: The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.


    Search the entire index, order by a specific field (lastRenovationDate) in descending order, take the top two results, and show only hotelName and lastRenovationDate:

    Name: Triple Landscape Hotel
    Last renovated on: 9/20/2015 12:00:00 AM +00:00

    Name: Twin Dome Motel
    Last renovated on: 2/18/1979 12:00:00 AM +00:00


    Search the hotel names for the term 'hotel':

    HotelId: 3
    Name: Triple Landscape Hotel
    ...

    Complete.  Press any key to end application... 

Úplný zdrojový kód aplikace je k dispozici na konci tohoto článku.

Dále se blíže podíváme na každou metodu `Main`volanou .

### <a name="creating-an-index"></a>Vytvoření indexu
Po vytvoření `SearchServiceClient` `Main` , odstraní "hotely" index, pokud již existuje. Toto odstranění se provádí následující metodou:

```csharp
private static void DeleteIndexIfExists(string indexName, SearchServiceClient serviceClient)
{
    if (serviceClient.Indexes.Exists(indexName))
    {
        serviceClient.Indexes.Delete(indexName);
    }
}
```

Tato metoda používá `SearchServiceClient` dané ke kontrole, pokud existuje index, a pokud ano, odstranit.

> [!NOTE]
> Ukázkový kód v tomto článku používá synchronní metody azure kognitivní vyhledávání .NET SDK pro jednoduchost. Doporučujeme ve vlastních aplikacích použít asynchronní metody, aby aplikace byly škálovatelné a dobře reagovaly. Například ve výše uvedené metodě `DeleteAsync` můžete `Exists` `Delete`použít `ExistsAsync` a místo a .
> 
> 

Dále `Main` vytvoří nový index "hotels" voláním této metody:

```csharp
private static void CreateIndex(string indexName, SearchServiceClient serviceClient)
{
    var definition = new Index()
    {
        Name = indexName,
        Fields = FieldBuilder.BuildForType<Hotel>()
    };
    
    serviceClient.Indexes.Create(definition);
}
```

Tato metoda vytvoří `Index` nový objekt `Field` se seznamem objektů, který definuje schéma nového indexu. Každé pole má název, datový typ a několik atributů, které definují jeho chování při hledání. Třída `FieldBuilder` používá reflexe k `Field` vytvoření seznamu objektů pro index zkoumáním veřejné vlastnosti a atributy dané `Hotel` třídy modelu. Později se na hodinu `Hotel` podíváme zblízka.

> [!NOTE]
> Vždy můžete vytvořit seznam `Field` objektů přímo `FieldBuilder` namísto použití v případě potřeby. Například nebudete chtít použít třídu modelu nebo budete muset použít existující třídu modelu, kterou nechcete upravovat přidáním atributů.
>
> 

Kromě polí můžete také přidat profily hodnocení, návrhy nebo možnosti CORS do indexu (tyto parametry jsou vynechány ze vzorku pro stručnost). Další informace o objektu Index a jeho součástech najdete v [odkazu sady SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index)a také v [odkazu rozhraní REST rozhraní AZURE Cognitive Search .](https://docs.microsoft.com/rest/api/searchservice/)

### <a name="populating-the-index"></a>Naplnění indexu
Dalším krokem `Main` v naplní nově vytvořený index. Tento index populace se provádí v následující metodě: (Některé kód nahrazen "..." pro ilustrační účely.  Úplný ukázkový roztok naleznete v úplném kódu naplnění dat.)

```csharp
private static void UploadDocuments(ISearchIndexClient indexClient)
{
    var hotels = new Hotel[]
    {
        new Hotel()
        {
            HotelId = "1",
            HotelName = "Secret Point Motel",
            ...
            Address = new Address()
            {
                StreetAddress = "677 5th Ave",
                ...
            },
            Rooms = new Room[]
            {
                new Room()
                {
                    Description = "Budget Room, 1 Queen Bed (Cityside)",
                    ...
                },
                new Room()
                {
                    Description = "Budget Room, 1 King Bed (Mountain View)",
                    ...
                },
                new Room()
                {
                    Description = "Deluxe Room, 2 Double Beds (City View)",
                    ...
                }
            }
        },
        new Hotel()
        {
            HotelId = "2",
            HotelName = "Twin Dome Motel",
            ...
            {
                StreetAddress = "140 University Town Center Dr",
                ...
            },
            Rooms = new Room[]
            {
                new Room()
                {
                    Description = "Suite, 2 Double Beds (Mountain View)",
                    ...
                },
                new Room()
                {
                    Description = "Standard Room, 1 Queen Bed (City View)",
                    ...
                },
                new Room()
                {
                    Description = "Budget Room, 1 King Bed (Waterfront View)",
                    ...
                }
            }
        },
        new Hotel()
        {
            HotelId = "3",
            HotelName = "Triple Landscape Hotel",
            ...
            Address = new Address()
            {
                StreetAddress = "3393 Peachtree Rd",
                ...
            },
            Rooms = new Room[]
            {
                new Room()
                {
                    Description = "Standard Room, 2 Queen Beds (Amenities)",
                    ...
                },
                new Room ()
                {
                    Description = "Standard Room, 2 Double Beds (Waterfront View)",
                    ...
                },
                new Room()
                {
                    Description = "Deluxe Room, 2 Double Beds (Cityside)",
                    ...
                }
            }
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

Tato metoda má čtyři části. První vytvoří pole 3 `Hotel` objekty `Room` každý s 3 objekty, které budou sloužit jako naše vstupní data pro nahrání do indexu. Tato data jsou pevně zakódována pro jednoduchost. Ve vlastní aplikaci budou data pravděpodobně pocházet z externího zdroje dat, jako je například databáze SQL.

Druhá část vytvoří `IndexBatch` obsahující dokumenty. Operaci, kterou chcete použít pro dávku v okamžiku vytvoření, určíte `IndexBatch.Upload`v tomto případě voláním . Dávka se pak odešle do indexu Azure `Documents.Index` Cognitive Search metodou.

> [!NOTE]
> V tomto příkladu jsme právě nahrávání dokumentů. Pokud chcete sloučit změny do existujících dokumentů nebo odstranit `IndexBatch.Merge` `IndexBatch.MergeOrUpload`dokumenty, `IndexBatch.Delete` můžete vytvořit dávky voláním , nebo místo něj. Můžete také kombinovat různé operace v `IndexBatch.New`jedné dávce voláním , který trvá `IndexAction` kolekci objektů, z nichž každý říká Azure Cognitive Search provést určitou operaci v dokumentu. Můžete vytvořit `IndexAction` každý s vlastní operaci voláním `IndexAction.Merge`odpovídající `IndexAction.Upload`metody, jako je například , a tak dále.
> 
> 

Třetí část této metody je catch blok, který zpracovává důležitý případ chyby pro indexování. Pokud se službě Azure Cognitive Search nepodaří indexovat `IndexBatchException` některé dokumenty `Documents.Index`v dávce, je vyvolána . K této výjimce může dojít, pokud indexujete dokumenty, zatímco je služba pod velkým zatížením. **Důrazně doporučujeme v kódu explicitně zpracovávat tento případ.** Indexování dokumentů, které selhaly, můžete odložit a poté zkusit znovu, nebo v závislosti na požadavcích vaší aplikace na konzistenci dat provést něco jiného.

> [!NOTE]
> Tuto metodu [`FindFailedActionsToRetry`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexbatchexception.findfailedactionstoretry) můžete použít k vytvoření nové dávky obsahující pouze `Index`akce, které se nezdařily v předchozím volání . Tam je diskuse o tom, jak správně používat [na StackOverflow](https://stackoverflow.com/questions/40012885/azure-search-net-sdk-how-to-use-findfailedactionstoretry).
>
>

Nakonec metoda `UploadDocuments` zpoždění po dobu dvou sekund. Indexování probíhá asynchronně ve službě Azure Cognitive Search, takže ukázková aplikace musí počkat krátkou dobu, aby zajistila, že dokumenty jsou k dispozici pro vyhledávání. Tato odložení se obvykle používají pouze v ukázkových aplikacích a při testech.

<a name="how-dotnet-handles-documents"></a>

#### <a name="how-the-net-sdk-handles-documents"></a>Jak .NET SDK zpracovává dokumenty
Možná se divíte, jak azure kognitivní vyhledávání .NET SDK je možné `Hotel` nahrát instance uživatelem definované třídy, jako je index. Abychom vám pomohli odpovědět na tuto `Hotel` otázku, podívejme se na třídu:

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Spatial;
using Newtonsoft.Json;

public partial class Hotel
{
    [System.ComponentModel.DataAnnotations.Key]
    [IsFilterable]
    public string HotelId { get; set; }

    [IsSearchable, IsSortable]
    public string HotelName { get; set; }

    [IsSearchable]
    [Analyzer(AnalyzerName.AsString.EnLucene)]
    public string Description { get; set; }

    [IsSearchable]
    [Analyzer(AnalyzerName.AsString.FrLucene)]
    [JsonProperty("Description_fr")]
    public string DescriptionFr { get; set; }

    [IsSearchable, IsFilterable, IsSortable, IsFacetable]
    public string Category { get; set; }

    [IsSearchable, IsFilterable, IsFacetable]
    public string[] Tags { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public bool? ParkingIncluded { get; set; }

    // SmokingAllowed reflects whether any room in the hotel allows smoking.
    // The JsonIgnore attribute indicates that a field should not be created 
    // in the index for this property and it will only be used by code in the client.
    [JsonIgnore]
    public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;

    [IsFilterable, IsSortable, IsFacetable]
    public DateTimeOffset? LastRenovationDate { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public double? Rating { get; set; }

    public Address Address { get; set; }

    [IsFilterable, IsSortable]
    public GeographyPoint Location { get; set; }

    public Room[] Rooms { get; set; }
}
```

První věc, kterou si všimnete, je, `Hotel` že název každé veřejné vlastnosti ve třídě bude mapovat na pole se stejným názvem v definici indexu. Pokud chcete, aby každé pole začínalo písmenem malých písmen ("případ velblouda"), můžete sdělit sadu SDK, aby automaticky mapovala názvy vlastností na camel-case s atributem `[SerializePropertyNamesAsCamelCase]` ve třídě. Tento scénář je běžný v aplikacích .NET, které provádějí datovou vazbu, kde cílové schéma je mimo kontrolu vývojáře aplikace bez nutnosti porušovat pokyny pro pojmenování "Pascal case" v rozhraní .NET.

> [!NOTE]
> Azure Cognitive Search .NET SDK používá [knihovnu NewtonSoft JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm) serializovat a rekonstruovat vlastní objekty modelu do a z JSON. V případě potřeby lze serializaci přizpůsobit. Další informace naleznete [v tématu Vlastní serializace s JSON.NET](#JsonDotNet).
> 
> 

Druhá věc, kterou si všimnete, je, `IsFilterable` `IsSearchable`že `Key`každá `Analyzer`vlastnost je zdobena atributy, jako je , , a . Tyto atributy se mapují přímo na [odpovídající atributy pole v indexu Azure Cognitive Search](/rest/api/searchservice/create-index). Třída `FieldBuilder` používá tyto vlastnosti k vytvoření definice polí pro index.

Třetí důležitou věcí `Hotel` třídy jsou datové typy veřejných vlastností. .NET typy těchto vlastností se mapují na odpovídající typy polí v definici indexu. Například řetězcová vlastnost `Category` se mapuje na pole `category`, které je typu `Edm.String`. Existují podobná mapování typů `bool?` `Edm.Boolean`mezi `DateTimeOffset?`, `Edm.DateTimeOffset` , a tak dále. Konkrétní pravidla pro mapování typů jsou `Documents.Get` dokumentována metodou v [odkazu Azure Cognitive Search .NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.get). Třída `FieldBuilder` se postará o toto mapování za vás, ale může být stále užitečné pochopit v případě, že potřebujete vyřešit problémy serializace.

Nevšimli jste si `SmokingAllowed` toho pozemku?

```csharp
[JsonIgnore]
public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;
```

Atribut `JsonIgnore` na této vlastnosti `FieldBuilder` říká, že není serializovat do indexu jako pole.  To je skvělý způsob, jak vytvořit vypočtené vlastnosti na straně klienta, které můžete použít jako pomocné uživatele ve vaší aplikaci.  V tomto případě `SmokingAllowed` nemovitost odráží, `Room` zda `Rooms` některý z kolekce umožňuje kouření.  Pokud jsou všechny falešné, znamená to, že celý hotel neumožňuje kouření.

Některé vlastnosti, jako `Address` jsou a `Rooms` jsou instance .NET třídy.  Tyto vlastnosti představují složitější datové struktury a v důsledku toho vyžadují pole se [složitým datovým typem](https://docs.microsoft.com/azure/search/search-howto-complex-data-types) v indexu.

Vlastnost `Address` představuje sadu více hodnot `Address` ve třídě, definované níže:

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Newtonsoft.Json;

namespace AzureSearch.SDKHowTo
{
    public partial class Address
    {
        [IsSearchable]
        public string StreetAddress { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string City { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string StateProvince { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string PostalCode { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string Country { get; set; }
    }
}
```

Tato třída obsahuje standardní hodnoty používané k popisu adres ve Spojených státech nebo Kanadě. Typy, jako je tento, můžete použít k seskupení logických polí v indexu.

Vlastnost `Rooms` představuje pole `Room` objektů:

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Newtonsoft.Json;

namespace AzureSearch.SDKHowTo
{
    public partial class Room
    {
        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.EnMicrosoft)]
        public string Description { get; set; }

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.FrMicrosoft)]
        [JsonProperty("Description_fr")]
        public string DescriptionFr { get; set; }

        [IsSearchable, IsFilterable, IsFacetable]
        public string Type { get; set; }

        [IsFilterable, IsFacetable]
        public double? BaseRate { get; set; }

        [IsSearchable, IsFilterable, IsFacetable]
        public string BedOptions { get; set; }

        [IsFilterable, IsFacetable]
        public int SleepsCount { get; set; }

        [IsFilterable, IsFacetable]
        public bool? SmokingAllowed { get; set; }

        [IsSearchable, IsFilterable, IsFacetable]
        public string[] Tags { get; set; }
    }
}
```

Datový model v rozhraní .NET a jeho odpovídající schéma indexu by měly být navrženy tak, aby podporovaly prostředí vyhledávání, které chcete poskytnout koncovému uživateli. Každý objekt nejvyšší úrovně v rozhraní .NET, tedy dokument v indexu, odpovídá výsledku hledání, který byste prezentovali ve vašem uživatelském rozhraní. Například v aplikaci pro vyhledávání hotelů mohou koncoví uživatelé chtít vyhledávat podle názvu hotelu, vlastností hotelu nebo charakteristik konkrétního pokoje. Některé příklady dotazů se dozvíme o něco později.

Tato schopnost používat vlastní třídy pro interakci s dokumenty v indexu funguje v obou směrech; Můžete také načíst výsledky hledání a nechat sdk automaticky rekonstruovat je na typ vašeho výběru, jak uvidíme v další části.

> [!NOTE]
> Sada Azure Cognitive Search .NET SDK také podporuje `Document` dynamicky zadané dokumenty pomocí třídy, což je mapování názvů polí klíč/hodnota na hodnoty polí. To je užitečné v situacích, kdy v době navrhování neznáte schéma indexu nebo kde by vázání na konkrétní třídy modelu bylo nepraktické. Všechny metody v sadě SDK, které pracují s dokumenty, mají přetížení, které pracují se třídou `Document`, ale i přetížení silně závislá na typu, která přebírají parametr obecného typu. Pouze ty se používají v ukázkovém kódu v tomto kurzu. [ `Document` Třída](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.document) dědí `Dictionary<string, object>`z .
> 
>

**Proč byste měli používat datové typy s možnou hodnotou null**

Při navrhování vlastní třídy modelu mapovat na index Azure Cognitive Search, doporučujeme `int` deklarovat vlastnosti `bool?` typů `bool`hodnot, jako `bool` je například a má být null (například namísto). Pokud použijete vlastnost se zakázanou hodnotou null, musíte **zajistit**, aby žádné dokumenty v indexu neobsahovaly pro odpovídající pole hodnotu null. Sada SDK ani služba Azure Cognitive Search vám to nepomůžou vynutit.

Nejedná se pouze o hypotetický problém: představte si situaci, kdy přidáte nové pole do stávajícího indexu typu `Edm.Int32`. Po aktualizaci definice indexu budou mít všechny dokumenty hodnotu null pro toto nové pole (protože všechny typy lze v Azure Cognitive Search zrušit). Pokud pak použijete třídu modelu s vlastností `int` se zakázanou hodnotou null, při pokusu o načtení dokumentů dojde k vyvolání podobné výjimky `JsonSerializationException`:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Z tohoto důvodu doporučujeme jako osvědčený postup používat ve třídách modelu typy s možnou hodnotou null.

<a name="JsonDotNet"></a>

#### <a name="custom-serialization-with-jsonnet"></a>Vlastní serializace s JSON.NET
Sada SDK používá JSON.NET pro serializaci a rekonstrukci dokumentů. Serializaci a deserializaci můžete v případě potřeby `JsonConverter` `IContractResolver`přizpůsobit definováním vlastní nebo . Další informace naleznete v [dokumentaci k JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm). To může být užitečné, když chcete přizpůsobit existující třídu modelu z vaší aplikace pro použití s Azure Cognitive Search a další pokročilejší scénáře. Například s vlastní serializací můžete:

* Zahrnout nebo vyloučit určité vlastnosti třídy modelu z ukládání jako pole dokumentu.
* Mapování mezi názvy vlastností v kódu a názvy polí v indexu.
* Vytvořte vlastní atributy, které lze použít pro mapování vlastností na pole dokumentu.

Příklady implementace vlastní serializace najdete v jednotkových testech pro Azure Cognitive Search .NET SDK na GitHubu. Dobrým výchozím bodem je [tato složka](https://github.com/Azure/azure-sdk-for-net/tree/4f6f4e4c90200c1b0621c4cead302a91e89f2aba/sdk/search/Microsoft.Azure.Search/tests/Tests/Models). Obsahuje třídy, které jsou používány vlastní serializace testy.

### <a name="searching-for-documents-in-the-index"></a>Hledání dokumentů v rejstříku
Posledním krokem v ukázkové aplikaci je vyhledat některé dokumenty v indexu:

```csharp
private static void RunQueries(ISearchIndexClient indexClient)
{
    SearchParameters parameters;
    DocumentSearchResult<Hotel> results;

    Console.WriteLine("Search the entire index for the term 'motel' and return only the HotelName field:\n");

    parameters =
        new SearchParameters()
        {
            Select = new[] { "HotelName" }
        };

    results = indexClient.Documents.Search<Hotel>("motel", parameters);

    WriteDocuments(results);

    Console.Write("Apply a filter to the index to find hotels with a room cheaper than $100 per night, ");
    Console.WriteLine("and return the hotelId and description:\n");

    parameters =
        new SearchParameters()
        {
            Filter = "Rooms/any(r: r/BaseRate lt 100)",
            Select = new[] { "HotelId", "Description" }
        };

    results = indexClient.Documents.Search<Hotel>("*", parameters);

    WriteDocuments(results);

    Console.Write("Search the entire index, order by a specific field (lastRenovationDate) ");
    Console.Write("in descending order, take the top two results, and show only hotelName and ");
    Console.WriteLine("lastRenovationDate:\n");

    parameters =
        new SearchParameters()
        {
            OrderBy = new[] { "LastRenovationDate desc" },
            Select = new[] { "HotelName", "LastRenovationDate" },
            Top = 2
        };

    results = indexClient.Documents.Search<Hotel>("*", parameters);

    WriteDocuments(results);

    Console.WriteLine("Search the entire index for the term 'hotel':\n");

    parameters = new SearchParameters();
    results = indexClient.Documents.Search<Hotel>("hotel", parameters);

    WriteDocuments(results);
}
```

Pokaždé, když spustí dotaz, tato metoda `SearchParameters` nejprve vytvoří nový objekt. Tento objekt se používá k určení dalších možností pro dotaz, jako je řazení, filtrování, stránkování a fazetování. V této metodě `Filter`nastavujeme vlastnost , `Select` `OrderBy`, a `Top` pro různé dotazy. Všechny `SearchParameters` vlastnosti jsou [zde](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters)zdokumentovány .

Dalším krokem je skutečně spustit vyhledávací dotaz. Spuštění hledání se provádí `Documents.Search` pomocí metody. Pro každý dotaz předáme hledaný text, který `"*"` se použije jako řetězec (nebo pokud není žádný vyhledávací text), plus parametry vyhledávání vytvořené dříve. Také jsme `Hotel` zadat jako `Documents.Search`parametr typu pro , který říká SDK rekonstruovat dokumenty `Hotel`ve výsledcích hledání do objektů typu .

> [!NOTE]
> Další informace o syntaxi výrazu vyhledávacího dotazu naleznete [zde](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search).
> 
> 

Nakonec po každém dotazu tato metoda iterenuje všechny shody ve výsledcích hledání a tiskne každý dokument do konzoly:

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

Podívejme se blíže na každý z dotazů v pořadí. Zde je kód pro spuštění prvního dotazu:

```csharp
parameters =
    new SearchParameters()
    {
        Select = new[] { "HotelName" }
    };

results = indexClient.Documents.Search<Hotel>("motel", parameters);

WriteDocuments(results);
```

V tomto případě prohledáváme celý index a hledáme slovo "motel" v jakémkoli vyprohledaném poli `Select` a chceme pouze získat názvy hotelů, jak je specifikováno parametrem. Zde jsou výsledky:

    Name: Secret Point Motel

    Name: Twin Dome Motel

Další dotaz je trochu zajímavější.  Chceme najít všechny hotely, které mají pokoj s noční sazbou nižší než $ 100 a vrátit pouze id hotelu a popis:

```csharp
parameters =
    new SearchParameters()
    {
        Filter = "Rooms/any(r: r/BaseRate lt 100)",
        Select = new[] { "HotelId", "Description" }
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);
```

Tento dotaz používá `$filter` výraz `Rooms/any(r: r/BaseRate lt 100)`OData , k filtrování dokumentů v indexu. To používá [libovolný operátor](https://docs.microsoft.com/azure/search/search-query-odata-collection-operators) použít "BaseRate lt 100" pro každou položku v Rooms kolekce. Další informace o syntaxi OData, kterou Azure Cognitive Search [podporuje, najdete tady](https://docs.microsoft.com/azure/search/query-odata-filter-orderby-syntax).

Zde jsou výsledky dotazu:

    HotelId: 1
    Description: The hotel is ideally located on the main commercial artery of the city in the heart of New York...

    HotelId: 2
    Description: The hotel is situated in a nineteenth century plaza, which has been expanded and renovated to...

Dále chceme najít dva nejlepší hotely, které byly naposledy zrekonstruovány, a ukázat název hotelu a datum poslední renovace. Zde je kód: 

```csharp
parameters =
    new SearchParameters()
    {
        OrderBy = new[] { "LastRenovationDate desc" },
        Select = new[] { "HotelName", "LastRenovationDate" },
        Top = 2
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);
```

V tomto případě opět použijeme syntaxi `OrderBy` OData k určení parametru jako `lastRenovationDate desc`. Také jsme `Top` nastavili na 2, abychom zajistili, že dostaneme pouze první dva dokumenty. Stejně jako `Select` dříve jsme nastavili, abychom určili, která pole mají být vrácena.

Zde jsou výsledky:

    Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
    Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00

Nakonec chceme najít všechny názvy hotelů, které odpovídají slovu "hotel":

```csharp
parameters = new SearchParameters()
{
    SearchFields = new[] { "HotelName" }
};
results = indexClient.Documents.Search<Hotel>("hotel", parameters);

WriteDocuments(results);
```

A zde jsou výsledky, které zahrnují všechna pole, protože jsme nespecifikovali `Select` vlastnost:

    HotelId: 3
    Name: Triple Landscape Hotel
    ...

Tento krok dokončí kurz, ale nezastavujte zde. **Další kroky poskytují další materiály pro další informace o Azure Cognitive Search.

## <a name="next-steps"></a>Další kroky
* Projděte si referenční materiály pro [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search) a [REST API](https://docs.microsoft.com/rest/api/searchservice/).
* Zkontrolujte [konvence pojmenování,](https://docs.microsoft.com/rest/api/searchservice/Naming-rules) abyste se naučili pravidla pro pojmenování různých objektů.
* Zkontrolujte [podporované datové typy](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types) v Azure Cognitive Search.
