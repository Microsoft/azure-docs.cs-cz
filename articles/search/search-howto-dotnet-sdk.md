---
title: Jak používat Azure Search z aplikace .NET | Microsoft Docs
description: Jak používat Azure Search z aplikace .NET
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: brjohnst
ms.openlocfilehash: e8a492a0786281bdc1d7c2123a7188c32a124e13
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="how-to-use-azure-search-from-a-net-application"></a>Jak používat Azure Search z aplikace .NET
Tento článek je návod, které vám pomůžou s spuštěná [Azure Search .NET SDK](https://aka.ms/search-sdk). Můžete implementovat bohaté vyhledáváním do vaší aplikace pomocí Azure Search .NET SDK.

## <a name="whats-in-the-azure-search-sdk"></a>Novinky ve službě Azure vyhledávání SDK
Sada SDK se skládá z několika klientské knihovny, které vám umožní spravovat vaše indexy, zdrojů dat, indexery a synonymum mapuje, a také nahrávání a správě dokumentů a spouštět dotazy, aniž by museli řešit podrobnosti protokolu HTTP a JSON. Tyto knihovny klienta se distribuují jako balíčky NuGet.

Hlavní balíček NuGet je `Microsoft.Azure.Search`, což je meta balíček, který obsahuje všechny ostatní balíčky jako závislosti. Tento balíček použijte, pokud budete právě začínat nebo pokud víte, že aplikace bude vyžadovat všechny funkce Azure Search.

Další balíčky NuGet v sadě SDK jsou:
 
  - `Microsoft.Azure.Search.Data`: Tento balíček použijte, pokud vyvíjíte aplikaci .NET používání služby Azure Search a potřebujete jenom dotaz nebo aktualizovat dokumenty ve vašem indexy. Pokud také potřebujete vytvořit nebo aktualizovat indexy, synonymum mapy nebo jiným prostředkům úrovni služby, použijte `Microsoft.Azure.Search` balíček místo.
  - `Microsoft.Azure.Search.Service`: Tento balíček používejte, pokud vyvíjíte automatizace v rozhraní .NET pro správu indexů Azure Search, synonymum mapy, indexery, zdroje dat nebo jiným prostředkům na úrovni služby. Pokud potřebujete jenom dotazu nebo aktualizace dokumentů ve vašem indexy, použijte `Microsoft.Azure.Search.Data` balíček místo. Pokud budete potřebovat všechny funkce Azure Search, použijte `Microsoft.Azure.Search` balíček místo.
  - `Microsoft.Azure.Search.Common`: Vyžaduje na knihovny .NET vyhledávání Azure běžné typy. Neměli byste potřebovat pro tento balíček použít přímo v aplikaci; Smyslem je pouze má být použit jako závislost.

Různé klientské knihovny definování tříd jako `Index`, `Field`, a `Document`, stejně jako operací, jako `Indexes.Create` a `Documents.Search` na `SearchServiceClient` a `SearchIndexClient` třídy. Tyto třídy jsou uspořádány do následujících oborů názvů:

* [Microsoft.Azure.Search](https://docs.microsoft.com/dotnet/api/microsoft.azure.search)
* [Microsoft.Azure.Search.Models](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models)

Aktuální verze rozhraní .NET SDK služby Azure Search je nyní všeobecně dostupná. Pokud chcete poskytnout zpětnou vazbu, abychom mohli začlenit v příští verzi, navštivte prosím naše [zpětné vazby stránky](https://feedback.azure.com/forums/263029-azure-search/).

.NET SDK podporuje verzi `2017-11-11` z [REST API služby Azure Search](https://docs.microsoft.com/rest/api/searchservice/). Tato verze teď zahrnuje podporu pro synonyma, jakož i přírůstkové vylepšení indexery. Zobrazit náhled funkce, které jsou *není* součástí této verze, jako je podpora pro indexování pole JSON a soubory sdíleného svazku clusteru, jsou v [preview](search-api-2016-09-01-preview.md) a dostupný prostřednictvím [verze 4.0 preview .NET SDK](https://aka.ms/search-sdk-preview).

Tato sada SDK nepodporuje [operace správy](https://docs.microsoft.com/rest/api/searchmanagement/) jako je například vytváření a škálování služby vyhledávání a správa klíče rozhraní API. Pokud potřebujete ke správě prostředků vyhledávání z aplikace .NET, můžete použít [SDK služby Azure Search .NET správu](https://aka.ms/search-mgmt-sdk).

## <a name="upgrading-to-the-latest-version-of-the-sdk"></a>Upgrade na nejnovější verzi sady SDK
Pokud už používáte starší verzi .NET SDK služby Azure Search a chcete upgradovat na novou verzi všeobecně dostupná, [v tomto článku](search-dotnet-sdk-migration-version-5.md) vysvětluje, jak.

## <a name="requirements-for-the-sdk"></a>Požadavky pro sadu SDK
1. Sada Visual Studio 2017.
2. Vlastní službu Azure Search. Chcete-li použít sadu SDK, budete potřebovat název služby a jeden či více klíčů rozhraní API. [Vytvoření služby v portálu](search-create-service-portal.md) vám pomohou dokončit tyto kroky.
3. Stáhněte si sadu Azure Search .NET SDK [balíček NuGet](http://www.nuget.org/packages/Microsoft.Azure.Search) pomocí "Správa balíčků NuGet" v sadě Visual Studio. Právě vyhledejte název balíčku `Microsoft.Azure.Search` v NuGet.org (nebo jeden z dalších balíčku názvy výše, pokud potřebujete jenom podmnožinu funkcí).

.NET SDK služby Azure Search podporuje aplikace cílený na rozhraní .NET Framework 4.5.2 a vyšší, a také .NET Core.

## <a name="core-scenarios"></a>Základní scénáře
Existuje několik věcí, které musíte udělat v aplikaci vyhledávání. V tomto kurzu nabídneme tyto základní scénáře:

* Vytvoření indexu
* Naplňování indexu s dokumenty
* Hledání dokumentů pomocí fulltextové vyhledávání a filtry

Ukázkový kód, který následuje znázorňuje všechny z nich. Nebojte se, že pomocí fragmenty kódu ve vaší vlastní aplikaci.

### <a name="overview"></a>Přehled
Vytvoří novou ukázkovou aplikaci jsme budete seznamovat index s názvem "hotels", naplní s několika dokumenty a potom provede některé vyhledávací dotazy. Tady je hlavní programu, zobrazující celkové toku:

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

Projdeme tento krok po kroku. Nejprve musíme vytvořit nový `SearchServiceClient`. Tento objekt umožňuje spravovat indexy. Chcete-li jeden vytvořit, je třeba zadat název vaší služby Azure Search, jakož i klíčem rozhraní API pro správu. Můžete zadat tyto informace `appsettings.json` soubor [ukázkové aplikace](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).

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
> Pokud jste zadali nesprávné klávesy (například klíč dotazů kde nebyla nutná klíč správce), `SearchServiceClient` vyvolá výjimku `CloudException` s chybou zpráva "Zakázán" poprvé, jako například volání metody operace, `Indexes.Create`. V takovém případě vám zkontrolujte naše klíč rozhraní API.
> 
> 

Další několika řádků volat metody pro vytvoření indexu s názvem "hotels", nejprve odstraňování Pokud již existuje. Jsme provede tyto metody trochu později.

```csharp
Console.WriteLine("{0}", "Deleting index...\n");
DeleteHotelsIndexIfExists(serviceClient);

Console.WriteLine("{0}", "Creating index...\n");
CreateHotelsIndex(serviceClient);
```

V dalším kroku index musí být naplněny. Chcete-li to provést, je nutné zadat `SearchIndexClient`. Existují dva způsoby, jak získat jeden: vytváření ho nebo volání `Indexes.GetClient` na `SearchServiceClient`. Používáme k tomu ke zvýšení pohodlí.

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

> [!NOTE]
> V typické vyhledávací aplikaci se o správu a naplňování indexu stará samostatná komponenta volaná dotazy vyhledávání. `Indexes.GetClient` je vhodné pro naplňování indexu, protože díky tomu není nutné poskytovat další `SearchCredentials`. Dělá to pomocí předání klíče správce, který jste použili pro vytvoření `SearchServiceClient`, službě `SearchIndexClient`. V části aplikace, který provádí dotazy, je nicméně lepší vytvořit `SearchIndexClient` přímo, abyste mohli předávat klíč dotazů místo klíče správce. To je konzistentní s Princip nejnižších nutných oprávnění a pomůže vám to lépe zabezpečit vaši aplikaci. Můžete najít další informace o klíčích a správce dotazu [zde](https://docs.microsoft.com/rest/api/searchservice/#authentication-and-authorization).
> 
> 

Teď, když máme `SearchIndexClient`, naplníme index. K tomu je potřeba další metodou, vám ukážeme později.

```csharp
Console.WriteLine("{0}", "Uploading documents...\n");
UploadDocuments(indexClient);
```

Nakonec provést několik vyhledávací dotazy a zobrazit výsledky. Tentokrát použijeme jiné `SearchIndexClient`:

```csharp
ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

RunQueries(indexClientForQueries);
```

Jsme bude trvat bližší pohled na `RunQueries` metoda později. Zde je kód k vytvoření nové `SearchIndexClient`:

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "hotels", new SearchCredentials(queryApiKey));
    return indexClient;
}
```

Tentokrát použijeme klíč dotazů vzhledem k tomu, že jsme není nutné oprávnění k zápisu do indexu. Můžete zadat tyto informace `appsettings.json` soubor [ukázkové aplikace](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).

Pokud spustíte tuto aplikaci s platný název služby a klíče rozhraní API, výstup by měl vypadat například takto:

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

Úplný zdrojový kód aplikace je k dispozici na konci tohoto článku.

V dalším kroku jsme bude trvat bližší pohled na každou z metod volá `Main`.

### <a name="creating-an-index"></a>Vytvoření indexu
Po vytvoření `SearchServiceClient`, je další věcí `Main` nemá se odstranit index "hotels", pokud již existuje. Která se provádí následující metodu:

```csharp
private static void DeleteHotelsIndexIfExists(SearchServiceClient serviceClient)
{
    if (serviceClient.Indexes.Exists("hotels"))
    {
        serviceClient.Indexes.Delete("hotels");
    }
}
```

Tato metoda používá v dané `SearchServiceClient` zkontrolujte, zda existuje index a pokud ano, odstraňte jej.

> [!NOTE]
> Příklad kódu v tomto článku používá pro jednoduchost synchronní metody sady Azure Search .NET SDK. Doporučujeme ve vlastních aplikacích použít asynchronní metody, aby aplikace byly škálovatelné a dobře reagovaly. Například v metodě výše můžete použít `ExistsAsync` a `DeleteAsync` místo `Exists` a `Delete`.
> 
> 

Dále `Main` vytvoří nový index "hotels" voláním této metody:

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

Tato metoda vytvoří novou `Index` objekt seznam `Field` objekty, které definuje schéma nového indexu. Každé pole má název, datový typ a několika atributů, které definují chování vyhledávání. `FieldBuilder` Třídy pomocí reflexe vytvoří seznam `Field` objekty pro index prověřením veřejné vlastnosti a atributů v dané `Hotel` třída modelu. Provedeme bližší pohled na `Hotel` později na třídu.

> [!NOTE]
> Vždy můžete vytvořit seznam `Field` objekty přímo místo použití `FieldBuilder` v případě potřeby. Například nemusíte chtít použijete třídu modelu, nebo budete muset použít existující třídu modelu, který nechcete upravit přidáním atributy.
>
> 

Kromě polí můžete také přidat vyhodnocování profily, trochu nebo CORS možnosti indexu (tyto byly vynechány z ukázky jako stručný výtah). Můžete najít další informace o objektu indexu a v jejích částí [referenční informace sady SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index#microsoft_azure_search_models_index), a v [referenční dokumentace rozhraní API REST Azure Search](https://docs.microsoft.com/rest/api/searchservice/).

### <a name="populating-the-index"></a>Naplňování indexu
Na další krok v `Main` je naplňte index nově vytvořený. To se provádí v následující metodu:

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

Tato metoda obsahuje čtyři části. První vytvoří pole `Hotel` objekty, které bude sloužit jako naše vstupní data na nahrát do indexu. Tato data jsou pevně pro jednoduchost. Ve vaší vlastní aplikaci bude vaše data pravděpodobně pocházet z externího zdroje dat například do databáze SQL.

Druhá část vytvoří `IndexBatch` obsahující dokumenty. Zadejte operace, kterou chcete použít k dávky v době vytvoření, v takovém případě voláním `IndexBatch.Upload`. Dávky je pak odeslat do indexu Azure Search pomocí `Documents.Index` metoda.

> [!NOTE]
> V tomto příkladu jsme právě nahráváte dokumenty. Pokud jste chtěli sloučit změny do stávajících dokumentů nebo odstranění dokumentů, dávky můžete vytvořit voláním `IndexBatch.Merge`, `IndexBatch.MergeOrUpload`, nebo `IndexBatch.Delete` místo. Také je možné kombinovat různé operace v jedné dávkové voláním `IndexBatch.New`, která vezme kolekci `IndexAction` objekty, z nichž každý říká službě Azure Search při provádění konkrétní operace v dokumentu. Můžete vytvořit každý `IndexAction` s vlastní operaci voláním odpovídající metoda `IndexAction.Merge`, `IndexAction.Upload`a tak dále.
> 
> 

Třetí součást tato metoda je blok catch, která zpracovává s případem důležitých chyb pro indexování. Pokud služba Azure Search při indexování některých dokumentů v dávce selže, `Documents.Index` vyvolá výjimku `IndexBatchException`. To může nastat v případě indexování dokumentů, zatímco je služba velmi zatížená. **Důrazně doporučujeme v kódu explicitně zpracovávat tento případ.** Indexování dokumentů, které selhaly, můžete odložit a poté zkusit znovu, nebo v závislosti na požadavcích vaší aplikace na konzistenci dat provést něco jiného.

> [!NOTE]
> Můžete použít `FindFailedActionsToRetry` metoda vytvořit novou dávku obsahující pouze akce, které se nezdařilo předchozí volání `Index`. Metoda je popsána [sem](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexbatchexception#Microsoft_Azure_Search_IndexBatchException_FindFailedActionsToRetry_Microsoft_Azure_Search_Models_IndexBatch_System_String_) a není diskuzi o způsobu jeho použití správně [na StackOverflow](http://stackoverflow.com/questions/40012885/azure-search-net-sdk-how-to-use-findfailedactionstoretry).
>
>

Nakonec `UploadDocuments` na 2 sekundy odloží metoda. Indexování ve službě Azure Search probíhá asynchronně, takže ukázková aplikace musí chvíli počkat a ujistit se, že jsou dokumenty dostupné pro vyhledávání. Tato odložení se obvykle používají pouze v ukázkových aplikacích a při testech.

#### <a name="how-the-net-sdk-handles-documents"></a>Jak .NET SDK zpracovává dokumenty
Možná vás zajímá, jak může .NET SDK služby Azure Search odesílat do indexu instance uživatelsky definované třídy, jako třeba `Hotel`. Pro odpověď na tuto otázku se podívejme se na `Hotel` třídy:

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
> .NET SDK služby Azure Search používá k serializaci a deserializaci vlastních objektů modelu do a z JSON knihovnu [NewtonSoft JSON.NET](http://www.newtonsoft.com/json/help/html/Introduction.htm). V případě potřeby lze serializaci přizpůsobit. Další podrobnosti najdete v tématu [vlastní serializace s JSON.NET](#JsonDotNet).
> 
> 

Druhý si všimněte, jako jsou atributy, `IsFilterable`, `IsSearchable`, `Key`, a `Analyzer` , uspořádání každé veřejné vlastnosti. Tyto atributy rozvržení přímo na [odpovídající atributy indexu Azure Search](https://docs.microsoft.com/rest/api/searchservice/create-index#request). `FieldBuilder` Třída tyto používá k vytvoření definice pole pro index.

Třetí důležité o `Hotel` třídy jsou datové typy veřejných vlastností. .NET typy těchto vlastností se mapují na odpovídající typy polí v definici indexu. Například řetězcová vlastnost `Category` se mapuje na pole `category`, které je typu `Edm.String`. Podobná mapování typu probíhají mezi `bool?` a `Edm.Boolean`, `DateTimeOffset?` a `Edm.DateTimeOffset` atd. Konkrétní pravidla pro mapování typu jsou popsaná u metody `Documents.Get` v tématu [Reference k sadě .NET SDK služby Azure Search](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations#Microsoft_Azure_Search_IDocumentsOperations_GetWithHttpMessagesAsync__1_System_String_System_Collections_Generic_IEnumerable_System_String__Microsoft_Azure_Search_Models_SearchRequestOptions_System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_). `FieldBuilder` Třída má na starosti toto mapování pro vás, ale stále může být užitečné rozumět v případě, že potřebujete vyřešit jakékoliv problémy serializace.

Tato možnost používat vlastní třídy jako dokumenty funguje v obou směrech; Můžete také načíst výsledky vyhledávání a nechat sadu SDK automaticky deserializovala do typu podle vaší volby, jak vidíte v další části.

> [!NOTE]
> .NET SDK služby Azure Search také podporuje dynamicky typované dokumenty pomocí třídy `Document`, která zajišťuje mapování klíč-hodnota názvů polí na hodnoty polí. To je užitečné v situacích, kdy v době navrhování neznáte schéma indexu nebo kde by vázání na konkrétní třídy modelu bylo nepraktické. Všechny metody v sadě SDK, které pracují s dokumenty, mají přetížení, které pracují se třídou `Document`, ale i přetížení silně závislá na typu, která přebírají parametr obecného typu. Pouze ty druhé se používají v ukázkovém kódu v tomto kurzu. `Document` Třída dědí z `Dictionary<string, object>`. Můžete najít další podrobnosti o [zde](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.document#microsoft_azure_search_models_document).
> 
> 

**Proč byste měli používat datové typy s možnou hodnotou null**

Při navrhování vlastních tříd modelu pro mapování na index Azure Search doporučujeme deklarovat vlastnosti typů hodnot, jako jsou `bool` nebo `int`, s možnou hodnotou null (například `bool?` místo `bool`). Pokud použijete vlastnost se zakázanou hodnotou null, musíte **zajistit**, aby žádné dokumenty v indexu neobsahovaly pro odpovídající pole hodnotu null. Sada SDK, ani služba Azure Search vám s tím nepomůže.

Nejedná se pouze o hypotetický problém: představte si situaci, kdy přidáte nové pole do stávajícího indexu typu `Edm.Int32`. Po aktualizaci definice indexu budou mít všechny dokumenty pro toto nové pole hodnotu null (protože všechny typy jsou ve službě Azure Search s možností null). Pokud pak použijete třídu modelu s vlastností `int` se zakázanou hodnotou null, při pokusu o načtení dokumentů dojde k vyvolání podobné výjimky `JsonSerializationException`:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Z tohoto důvodu doporučujeme jako osvědčený postup používat ve třídách modelu typy s možnou hodnotou null.

<a name="JsonDotNet"></a>

#### <a name="custom-serialization-with-jsonnet"></a>Vlastní serializace s JSON.NET
Sada SDK používá technologii JSON.NET pro serializaci a deserializaci dokumenty. Můžete upravit serializace a deserializace v případě potřeby tak, že definujete vlastní `JsonConverter` nebo `IContractResolver` (najdete v článku [JSON.NET dokumentace](http://www.newtonsoft.com/json/help/html/Introduction.htm) podrobnosti). To může být užitečné, pokud chcete přizpůsobit existující třídy modelu z vaší aplikace pro použití se službou Azure Search a jiné pokročilejší scénáře. S vlastní serializace můžete například:

* Zahrnout nebo vyloučit některé vlastnosti vaší třídy modelu z uložené jako pole dokumentu.
* Mapování mezi názvy vlastností v kódu a názvy polí v indexu.
* Vytvoření vlastních atributů, které lze použít pro mapování vlastností dokumentu polí.

Můžete najít příklady implementace vlastní serializace při testování částí pro Azure Search .NET SDK na Githubu. Je to dobrý výchozí bod [tato složka](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/Search/Search.Tests/Tests/Models). Obsahuje třídy, které používají vlastní serializace testy.

### <a name="searching-for-documents-in-the-index"></a>Hledání dokumentů v indexu
Poslední krok v ukázkové aplikace je k vyhledání některých dokumentů v indexu. Následující metoda provádí toto:

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

Pokaždé, když se provede dotaz, tato metoda první vytvoří novou `SearchParameters` objektu. To slouží k určení dalších možností pro dotaz například třídění, filtrování, stránkování a používání omezujících vlastností. Tato metoda jsme nastavení `Filter`, `Select`, `OrderBy`, a `Top` vlastnost pro různé dotazy. Všechny `SearchParameters` jsou popsané vlastnosti [zde](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters).

Dalším krokem je ve skutečnosti provést vyhledávací dotaz. To se provádí pomocí `Documents.Search` metoda. Pro každý dotaz jsme předat hledaný text, který chcete použít jako řetězec (nebo `"*"` Pokud neexistuje žádný hledaný text), plus parametry vyhledávání vytvořili dříve. Můžeme také určit `Hotel` jako parametr typu pro `Documents.Search`, která sděluje sady SDK k deserializaci dokumenty ve výsledcích hledání do objektů typu `Hotel`.

> [!NOTE]
> Můžete najít další informace o syntaxe výrazu dotazu vyhledávání [zde](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search).
> 
> 

Nakonec po každém dotazu tato metoda iteruje všechny shody ve výsledcích hledání, tisk každý dokument do konzoly:

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

Podívejme bližší pohled na každý dotaz na naopak. Tady je kód provést první dotaz:

```csharp
parameters =
    new SearchParameters()
    {
        Select = new[] { "hotelName" }
    };

results = indexClient.Documents.Search<Hotel>("budget", parameters);

WriteDocuments(results);
```

V takovém případě jsme se hledající hotely, které odpovídají slovo "rozpočet", a chcete získat zpět pouze hotelů názvy, podle specifikace `Select` parametr. Zde jsou výsledky:

    Name: Roach Motel

V dalším kroku chceme nalezení hotelů s noční počet menší než 150 USD a vrátí pouze hotelů ID a popis:

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

Tento dotaz používá OData `$filter` výrazu `baseRate lt 150`, chcete-li filtrovat dokumenty v indexu. Můžete najít další informace o syntaxi OData, který podporuje Azure Search [zde](https://docs.microsoft.com/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search).

Zde jsou výsledky dotazu:

    ID: 2   Description: Cheapest hotel in town
    ID: 3   Description: Close to town hall and the river

V dalším kroku chceme nalezení hotelů nejvyšší dva, které mají byl naposledy renovovanou a zobrazit název hotelů a datum posledního obnova. Zde je kód: 

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

V takovém případě znovu používáme syntaxe OData k určení `OrderBy` parametr jako `lastRenovationDate desc`. Můžeme také nastavit `Top` 2 zajistit nám pouze získat horních dvou dokumenty. Před, jsme nastavit jako `Select` zadat pole, která má být vrácen.

Zde jsou výsledky:

    Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
    Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00

Nakonec chcete najít všechny hotels, které odpovídají slovo "motel":

```csharp
parameters = new SearchParameters();
results = indexClient.Documents.Search<Hotel>("motel", parameters);

WriteDocuments(results);
```

A tady jsou výsledky, které zahrnují všechna pole, protože jsme nezadali `Select` vlastnost:

    ID: 2   Base rate: 79.99        Description: Cheapest hotel in town     Description (French): Hôtel le moins cher en ville      Name: Roach Motel       Category: Budget        Tags: [motel, budget]   Parking included: yes   Smoking allowed: yes    Last renovated on: 4/28/1982 12:00:00 AM +00:00 Rating: 1/5     Location: Latitude 49.678581, longitude -122.131577

Dokončení tohoto kroku kurzu, ale není tady zastavit. **Další kroky** poskytuje další zdroje dalších informací o službě Azure Search.

## <a name="next-steps"></a>Další postup
* Projděte si referenční materiály pro [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search) a [REST API](https://docs.microsoft.com/rest/api/searchservice/).
* Prohloubit vašich znalostí prostřednictvím [videa a jiné ukázky a výukové programy](search-video-demo-tutorial-list.md).
* Zkontrolujte [konvence vytváření názvů](https://docs.microsoft.com/rest/api/searchservice/Naming-rules) další pravidla pro pojmenovávání různé objekty.
* Zkontrolujte [podporované datové typy](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types) ve službě Azure Search.
