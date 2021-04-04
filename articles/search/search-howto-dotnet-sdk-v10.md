---
title: Použití Microsoft. Azure. Search (v10 za účelem) v .NET
titleSuffix: Azure Cognitive Search
description: Naučte se vytvářet a spravovat vyhledávací objekty v aplikaci .NET pomocí C# a Microsoft. Azure. Search (verze 10) sady .NET SDK. Fragmenty kódu ukazují připojení ke službě, vytváření indexů a dotazy.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.devlang: dotnet
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/27/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 11102d95bc2aba65e6bc3cba71805a67f195947b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92681487"
---
# <a name="how-to-use-microsoftazuresearch-in-a-c-net-application"></a>Jak používat Microsoft. Azure. Hledat v aplikaci C# .NET

Tento článek vysvětluje, jak vytvořit a spravovat vyhledávací objekty pomocí jazyka C# a starší klientské knihovny, [**Microsoft. Azure. Search**](/dotnet/api/overview/azure/search/client10) (verze 10) v sadě Azure SDK pro .NET. 

Verze 10 je poslední verzí balíčku Microsoft. Azure. Search. V [**Azure.Search.Documents**](/dotnet/api/overview/azure/search.documents-readme) od týmu Azure SDK se budou zavádět nové funkce.

> [!NOTE]
> Pokud máte existující nebo vývojové projekty pro vývoj, můžete dál používat verzi 10. Pro nové projekty nebo pro použití nových funkcí byste měli přejít na [novou knihovnu](/dotnet/api/overview/azure/search.documents-readme).

## <a name="about-version-10"></a>O verzi 10

Sada SDK se skládá z několika klientských knihoven, které vám umožní spravovat vaše indexy, zdroje dat, indexery a mapy synonym a také nahrávat a spravovat dokumenty a provádět dotazy, a to vše bez nutnosti zabývat se podrobnostmi o HTTP a JSON. Tyto klientské knihovny jsou distribuovány jako balíčky NuGet.

Hlavní balíček NuGet je `Microsoft.Azure.Search` , což je meta balíček, který obsahuje všechny ostatní balíčky jako závislosti. Tento balíček použijte, pokud jste právě začali, nebo pokud víte, že vaše aplikace bude potřebovat všechny funkce služby Azure Kognitivní hledání.

Ostatní balíčky NuGet v sadě SDK jsou:
 
  - `Microsoft.Azure.Search.Data`: Tento balíček použijte v případě, že vyvíjíte aplikaci .NET pomocí Azure Kognitivní hledání a potřebujete dotazovat nebo aktualizovat dokumenty ve svých indexech. Pokud potřebujete také vytvořit nebo aktualizovat indexy, mapy synonym nebo jiné prostředky na úrovni služby, použijte `Microsoft.Azure.Search` místo toho balíček.
  - `Microsoft.Azure.Search.Service`: Tento balíček použijte v případě, že vyvíjíte automatizaci v .NET za účelem správy indexů Kognitivní hledání Azure, map synonym, indexerů, zdrojů dat nebo jiných prostředků na úrovni služby. Pokud potřebujete dotazovat nebo aktualizovat pouze dokumenty v indexech, použijte `Microsoft.Azure.Search.Data` místo toho balíček. Pokud potřebujete všechny funkce služby Azure Kognitivní hledání, použijte `Microsoft.Azure.Search` místo toho balíček.
  - `Microsoft.Azure.Search.Common`: Běžné typy vyžadované knihovnami Azure Kognitivní hledání .NET. Tento balíček nemusíte používat přímo v aplikaci. Má sloužit pouze jako závislost.

Různé klientské knihovny definují třídy jako `Index` , `Field` , a `Document` i operace jako `Indexes.Create` a `Documents.Search` v `SearchServiceClient` `SearchIndexClient` třídách a. Tyto třídy jsou uspořádány do následujících oborů názvů:

* [Microsoft. Azure. Search](/dotnet/api/microsoft.azure.search)
* [Microsoft. Azure. Search. Models](/dotnet/api/microsoft.azure.search.models)

Pokud chcete poskytnout zpětnou vazbu k budoucí aktualizaci sady SDK, přečtěte si naši [zpětnou vazbu](https://feedback.azure.com/forums/263029-azure-search/) nebo vytvořte problém na [GitHubu](https://github.com/azure/azure-sdk-for-net/issues) a uveďte "Azure kognitivní hledání" v názvu problému.

Sada .NET SDK cílí [ `2019-05-06` na verzi Azure kognitivní hledání REST API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/search/data-plane/Microsoft.Azure.Search.Data/stable/2019-05-06). Tato verze zahrnuje podporu [komplexních typů](search-howto-complex-data-types.md), funkce [rozšíření AI](cognitive-search-concept-intro.md), [automatického dokončování](/rest/api/searchservice/autocomplete)a [režimu analýzy JsonLines](search-howto-index-json-blobs.md) při indexování objektů blob Azure. 

Tato sada SDK nepodporuje [operace správy](/rest/api/searchmanagement/) , jako je vytváření a škálování vyhledávacích služeb a Správa klíčů rozhraní API. Pokud potřebujete spravovat svoje prostředky hledání z aplikace .NET, můžete použít [sadu SDK pro správu Azure kognitivní hledání .NET](/dotnet/api/overview/azure/search/management).

## <a name="upgrade-to-v10"></a>Upgrade na v10 za účelem
Pokud už používáte starší verzi sady Azure Kognitivní hledání .NET SDK a chcete upgradovat na nejnovější verzi, která je obecně dostupná, [Tento článek](search-dotnet-sdk-migration-version-9.md) vysvětluje, jak.

## <a name="sdk-requirements"></a>Požadavky na sadu SDK
1. Visual Studio 2017 nebo novější.
2. Vaše vlastní služba Azure Kognitivní hledání. Aby bylo možné používat sadu SDK, budete potřebovat název služby a jeden nebo více klíčů rozhraní API. Pomocí těchto kroků vám pomůže [vytvořit službu na portálu](search-create-service-portal.md) .
3. Stáhněte si [balíček NuGet](https://www.nuget.org/packages/Microsoft.Azure.Search) sady Azure kognitivní hledání .NET SDK pomocí tématu Správa balíčků NuGet v sadě Visual Studio. `Microsoft.Azure.Search`Pokud potřebujete jenom podmnožinu funkcí, hledejte jenom název balíčku na NuGet.org (nebo jeden z dalších názvů balíčků výše).

Sada Azure Kognitivní hledání .NET SDK podporuje aplikace zaměřené na .NET Framework 4.5.2 a vyšší, jakož i .NET Core 2,0 a vyšší.

## <a name="core-scenarios"></a>Základní scénáře
V aplikaci pro hledání je potřeba udělat několik věcí. V tomto kurzu se zaměříme na tyto základní scénáře:

* Vytvoření indexu
* Naplnění indexu dokumenty
* Hledání dokumentů pomocí fulltextového vyhledávání a filtrů

Následující vzorový kód znázorňuje každý z těchto scénářů. Používejte fragmenty kódu ve vaší vlastní aplikaci.

### <a name="overview"></a>Přehled
Ukázková aplikace, kterou prozkoumáme, vytvoří nový index s názvem "hotely", naplní ho několika dokumenty a potom spustí některé vyhledávací dotazy. Toto je hlavní program, který zobrazuje celkový tok:

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

Provede vás tento krok za krokem. Nejdřív musíme vytvořit nový `SearchServiceClient` . Tento objekt umožňuje správu indexů. Aby bylo možné jednu instanci sestavit, musíte zadat název služby Azure Kognitivní hledání a klíč rozhraní API pro správu. Tyto informace můžete zadat v `appsettings.json` souboru [ukázkové aplikace](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).

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
> Pokud zadáte nesprávný klíč (například klíč dotazu, kde byl vyžadován klíč správce), `SearchServiceClient` vyvolá `CloudException` při prvním volání metody operace s chybovou zprávou "zakázáno", například `Indexes.Create` . Pokud k tomu dojde, dvakrát si Projděte náš klíč rozhraní API.
> 
> 

Několik následujících řádků volá metody k vytvoření indexu s názvem "hotely" a jeho první odstranění, pokud již existuje. Tato metoda vás provede trochu později.

```csharp
Console.WriteLine("{0}", "Deleting index...\n");
DeleteIndexIfExists(indexName, serviceClient);

Console.WriteLine("{0}", "Creating index...\n");
CreateIndex(indexName, serviceClient);
```

V dalším kroku je potřeba naplnit index. K naplnění indexu budete potřebovat `SearchIndexClient` . Existují dva způsoby, jak jednu získat: sestavením, nebo voláním `Indexes.GetClient` na `SearchServiceClient` . Pro usnadnění práce používáme druhé.

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
```

> [!NOTE]
> V typické aplikaci vyhledávání může být Správa a naplnění indexu zpracována samostatnou komponentou z vyhledávacích dotazů. `Indexes.GetClient` je vhodný pro naplnění indexu, protože vám ušetří problémy s dalším `SearchCredentials` . Dělá to pomocí předání klíče správce, který jste použili pro vytvoření `SearchServiceClient`, službě `SearchIndexClient`. V části aplikace, která spouští dotazy, je ale lepší vytvořit `SearchIndexClient` přímo, abyste mohli předat klíč dotazu, který umožňuje číst jenom data namísto klíče správce. To je konzistentní s principem minimálního oprávnění a pomůže vám to lépe zabezpečit vaši aplikaci. Další informace o klíčích pro správu a klíčích dotazů najdete [tady](/rest/api/searchservice/#authentication-and-authorization).
> 
> 

Teď, když máme `SearchIndexClient` , můžeme index naplnit. Naplnění indexu je prováděno jinou metodou, kterou vás provede později.

```csharp
Console.WriteLine("{0}", "Uploading documents...\n");
UploadDocuments(indexClient);
```

Nakonec spustíme několik vyhledávacích dotazů a zobrazíme výsledky. Tentokrát používáme jiný `SearchIndexClient` :

```csharp
ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(indexName, configuration);

RunQueries(indexClientForQueries);
```

Podíváme se na `RunQueries` metodu později. Zde je kód pro vytvoření nového `SearchIndexClient` :

```csharp
private static SearchIndexClient CreateSearchIndexClient(string indexName, IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, indexName, new SearchCredentials(queryApiKey));
    return indexClient;
}
```

Tentokrát používáme klíč dotazu, protože k indexu nepotřebujeme přístup pro zápis. Tyto informace můžete zadat v `appsettings.json` souboru [ukázkové aplikace](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).

Pokud spouštíte tuto aplikaci s platným názvem služby a klíči rozhraní API, výstup by měl vypadat jako v tomto příkladu: (některý výstup konzoly byl nahrazen znakem "..." pro ilustraci.)

```output

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
```

Úplný zdrojový kód aplikace je k dispozici na konci tohoto článku.

V dalším kroku se podíváme na každou metodu, kterou volá `Main` .

### <a name="creating-an-index"></a>Vytvoření indexu
Po vytvoření `SearchServiceClient` `Main` odstraní index "hotely", pokud již existuje. Odstranění se provádí pomocí následující metody:

```csharp
private static void DeleteIndexIfExists(string indexName, SearchServiceClient serviceClient)
{
    if (serviceClient.Indexes.Exists(indexName))
    {
        serviceClient.Indexes.Delete(indexName);
    }
}
```

Tato metoda používá daný `SearchServiceClient` pro kontrolu, zda index existuje, a pokud ano, odstraňte jej.

> [!NOTE]
> Vzorový kód v tomto článku používá pro jednoduchost synchronní metody sady Azure Kognitivní hledání .NET SDK. Doporučujeme ve vlastních aplikacích použít asynchronní metody, aby aplikace byly škálovatelné a dobře reagovaly. Například v metodě výše byste mohli použít `ExistsAsync` a `DeleteAsync` místo `Exists` a `Delete` .
> 
> 

Dále `Main` vytvoří nový index "hotely" voláním této metody:

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

Tato metoda vytvoří nový `Index` objekt se seznamem `Field` objektů, které definují schéma nového indexu. Každé pole má název, datový typ a několik atributů, které definují jeho chování hledání. `FieldBuilder`Třída používá reflexi k vytvoření seznamu `Field` objektů pro index prozkoumáním veřejných vlastností a atributů dané `Hotel` třídy modelu. Později se podrobněji podíváme na `Hotel` třídu.

> [!NOTE]
> Seznam objektů můžete kdykoli vytvořit `Field` přímo místo použití v `FieldBuilder` případě potřeby. Například nebudete chtít použít třídu modelu nebo může být nutné použít existující třídu modelu, kterou nechcete upravovat, přidáním atributů.
>
> 

Kromě polí můžete také do indexu přidat profily vyhodnocování, moduly pro návrhy a možnosti CORS (tyto parametry jsou vynechány v ukázce pro zkrácení). Další informace o objektu index a jeho částech najdete v [Referenční příručce k sadě SDK](/dotnet/api/microsoft.azure.search.models.index)a také v referenčních informacích k [Azure kognitivní hledání REST API](/rest/api/searchservice/).

### <a name="populating-the-index"></a>Naplnění indexu
V dalším kroku `Main` naplníte nově vytvořený index. Tento index naplnění se provádí v následující metodě: (kód se nahradil znakem "...". pro ilustrační účely.  Kompletní kód pro naplnění dat najdete v úplném ukázkovém řešení.)

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

Tato metoda má čtyři části. První vytvoří pole 3 `Hotel` objekty každý se 3 `Room` objekty, které budou sloužit jako naše vstupní data pro nahrání do indexu. Tato data jsou pevně kódovaná pro zjednodušení. Vaše data jsou ve vaší vlastní aplikaci pravděpodobná z externího zdroje dat, jako je třeba databáze SQL.

Druhá část vytvoří `IndexBatch` dokumenty obsahující. Určete operaci, kterou chcete použít pro dávku v okamžiku jejího vytvoření, v tomto případě voláním `IndexBatch.Upload` . Dávka se pak nahraje do indexu Azure Kognitivní hledání pomocí `Documents.Index` metody.

> [!NOTE]
> V tomto příkladu budeme nahrávat jenom dokumenty. Pokud jste chtěli sloučit změny do existujících dokumentů nebo odstraňovat dokumenty, mohli byste vytvořit dávky voláním `IndexBatch.Merge` , `IndexBatch.MergeOrUpload` nebo `IndexBatch.Delete` místo toho. Různé operace můžete v jedné dávce kombinovat také voláním metody `IndexBatch.New` , která přebírá kolekci `IndexAction` objektů, z nichž každý z nich oznamuje, že Azure kognitivní hledání provádět konkrétní operace s dokumentem. Můžete vytvořit každý `IndexAction` s vlastní operací voláním odpovídající metody, jako například `IndexAction.Merge` , `IndexAction.Upload` , a tak dále.
> 
> 

Třetí část této metody je blok catch, který zpracovává důležitý chybový případ pro indexování. Pokud se službě Azure Kognitivní hledání nepovede indexování některých dokumentů v dávce, `IndexBatchException` vyvolá se `Documents.Index` . Tato výjimka se může vyskytnout, pokud indexování dokumentů probíhá v případě velkého zatížení služby. **Důrazně doporučujeme v kódu explicitně zpracovávat tento případ.** Indexování dokumentů, které selhaly, můžete odložit a poté zkusit znovu, nebo v závislosti na požadavcích vaší aplikace na konzistenci dat provést něco jiného.

> [!NOTE]
> Metodu lze použít [`FindFailedActionsToRetry`](/dotnet/api/microsoft.azure.search.indexbatchexception.findfailedactionstoretry) k vytvoření nové dávky obsahující pouze akce, které selhaly v předchozím volání `Index` . Existuje diskuzi o tom, jak ho správně používat [na StackOverflow](https://stackoverflow.com/questions/40012885/azure-search-net-sdk-how-to-use-findfailedactionstoretry).
>
>

Nakonec je `UploadDocuments` Metoda zpožděna po dobu dvou sekund. Indexování probíhá asynchronně ve službě Azure Kognitivní hledání, takže ukázková aplikace potřebuje počkat krátkou dobu, aby se zajistila dostupnost dokumentů pro hledání. Tato odložení se obvykle používají pouze v ukázkových aplikacích a při testech.

<a name="how-dotnet-handles-documents"></a>

#### <a name="how-the-net-sdk-handles-documents"></a>Jak .NET SDK zpracovává dokumenty
Možná vás zajímá, jak bude sada Azure Kognitivní hledání .NET SDK schopna nahrávat instance uživatelsky definované třídy, jako je `Hotel` index. Abychom vám pomohli zodpovědět tuto otázku, Podívejme se na `Hotel` třídu:

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

První věc, kterou je třeba si všimnout, je, že název každé veřejné vlastnosti ve `Hotel` třídě se namapuje na pole se stejným názvem v definici indexu. Pokud chcete, aby každé pole začínalo malým písmenem ("ve stylu CamelCase případ"), můžete sadě SDK sdělit, aby názvy vlastností namapovaly na ve stylu CamelCase-Case automaticky s `[SerializePropertyNamesAsCamelCase]` atributem třídy. Tento scénář je běžný v aplikacích .NET, které provádějí datovou vazbu, kde cílové schéma je mimo ovládací prvek vývojářů aplikací, aniž by bylo nutné porušovat zásady vytváření názvů "Pascal" v rozhraní .NET.

> [!NOTE]
> Sada Azure Kognitivní hledání .NET SDK používá knihovnu [JSON.NET Newtonsoft](https://www.newtonsoft.com/json/help/html/Introduction.htm) k serializaci a deserializaci vašich vlastních objektů modelu do a z formátu JSON. V případě potřeby lze serializaci přizpůsobit. Další informace najdete v tématu [vlastní serializace pomocí JSON.NET](#JsonDotNet).
> 
> 

Druhá věc k oznámení je, že každá vlastnost je upravena s atributy `IsFilterable` , jako například, `IsSearchable` , `Key` a `Analyzer` . Tyto atributy jsou mapovány přímo k [odpovídajícím atributům polí v indexu služby Azure kognitivní hledání](/rest/api/searchservice/create-index). `FieldBuilder`Třída pomocí těchto vlastností vytvoří definice polí pro index.

Třetí důležitou věcí o této `Hotel` třídě jsou datové typy veřejných vlastností. .NET typy těchto vlastností se mapují na odpovídající typy polí v definici indexu. Například řetězcová vlastnost `Category` se mapuje na pole `category`, které je typu `Edm.String`. Existují podobné mapování typů mezi `bool?` , `Edm.Boolean` , a a `DateTimeOffset?` `Edm.DateTimeOffset` tak dále. Konkrétní pravidla pro mapování typů jsou zdokumentována pomocí `Documents.Get` metody v [referenčních informacích k sadě Azure kognitivní hledání .NET SDK](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.get). `FieldBuilder`Tato třída se pro vás postará o toto mapování, ale přesto může být užitečné pochopit, jak potřebujete řešit problémy s serializací.

Nedošlo k tomu, abyste si poznamenali `SmokingAllowed` vlastnost?

```csharp
[JsonIgnore]
public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;
```

`JsonIgnore`Atribut v této vlastnosti říká, že `FieldBuilder` ho neserializovat do indexu jako pole.  Toto je skvělý způsob, jak vytvořit počítané vlastnosti na straně klienta, které můžete použít jako pomocníky ve vaší aplikaci.  V tomto případě `SmokingAllowed` vlastnost odráží, zda některý `Room` z `Rooms` kolekcí umožňuje kouření.  Pokud jsou všechny hodnoty false, znamená to, že celý Hotel nedovoluje kouření.

Některé vlastnosti, jako `Address` `Rooms` jsou a jsou instancemi tříd .NET.  Tyto vlastnosti reprezentují složitější datové struktury a v důsledku toho vyžadují pole se [složitým datovým typem](./search-howto-complex-data-types.md) v indexu.

`Address`Vlastnost představuje sadu více hodnot ve `Address` třídě, která je definována níže:

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

Tato třída obsahuje standardní hodnoty používané k popisu adres v USA nebo Kanadě. Můžete použít typy, jako je to, k seskupení logických polí v indexu.

`Rooms`Vlastnost představuje pole `Room` objektů:

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

Váš datový model v .NET a odpovídající schéma indexu by měly být navržené tak, aby podporovaly hledání, které byste chtěli dát koncovému uživateli. Každý objekt nejvyšší úrovně v dokumentu .NET, dokument IE v indexu, odpovídá výsledku hledání, který byste nacházeli v uživatelském rozhraní. Například v aplikaci pro vyhledávání hotelů můžou koncoví uživatelé hledat podle názvu hotelu, funkce hotelu nebo charakteristiky konkrétní místnosti. Některé příklady dotazů pokryjeme trochu později.

Tato možnost použití vlastních tříd k interakci s dokumenty v indexu funguje v obou směrech. Můžete také načíst výsledky hledání a nechat sadu SDK automaticky deserializovat podle vámi zvoleného typu, jak je uvedeno v následující části.

> [!NOTE]
> Sada Azure Kognitivní hledání .NET SDK také podporuje dynamicky typové dokumenty pomocí `Document` třídy, což je mapování klíč/hodnota názvů polí na hodnoty polí. To je užitečné v situacích, kdy v době navrhování neznáte schéma indexu nebo kde by vázání na konkrétní třídy modelu bylo nepraktické. Všechny metody v sadě SDK, které pracují s dokumenty, mají přetížení, které pracují se třídou `Document`, ale i přetížení silně závislá na typu, která přebírají parametr obecného typu. V ukázkovém kódu v tomto kurzu se používá jenom ta druhá. [ `Document` Třída](/dotnet/api/microsoft.azure.search.models.document) dědí z `Dictionary<string, object>` .
> 
>

**Proč byste měli používat datové typy s možnou hodnotou null**

Při navrhování vlastních tříd modelů pro mapování k indexu služby Azure Kognitivní hledání doporučujeme deklarovat vlastnosti typů hodnot, například `bool` a `int` , aby byly možnou hodnotou null (například `bool?` místo `bool` ). Pokud použijete vlastnost se zakázanou hodnotou null, musíte **zajistit**, aby žádné dokumenty v indexu neobsahovaly pro odpovídající pole hodnotu null. Sada SDK ani služba Azure Kognitivní hledání vám ji nepomůže vyhovět.

Nejedná se pouze o hypotetický problém: představte si situaci, kdy přidáte nové pole do stávajícího indexu typu `Edm.Int32`. Po aktualizaci definice indexu budou mít všechny dokumenty pro toto nové pole hodnotu null (vzhledem k tomu, že všechny typy jsou v Azure Kognitivní hledání s možnou hodnotou null). Pokud pak použijete třídu modelu s vlastností `int` se zakázanou hodnotou null, při pokusu o načtení dokumentů dojde k vyvolání podobné výjimky `JsonSerializationException`:

```output
Error converting value {null} to type 'System.Int32'. Path 'IntValue'.
```

Z tohoto důvodu doporučujeme jako osvědčený postup používat ve třídách modelu typy s možnou hodnotou null.

<a name="JsonDotNet"></a>

#### <a name="custom-serialization-with-jsonnet"></a>Vlastní serializace pomocí JSON.NET
Sada SDK používá JSON.NET k serializaci a deserializaci dokumentů. Můžete přizpůsobit serializaci a deserializaci v případě potřeby definováním vlastních `JsonConverter` nebo `IContractResolver` . Další informace najdete v [dokumentaci k JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm). To může být užitečné, pokud chcete přizpůsobit existující třídu modelu z aplikace pro použití s Azure Kognitivní hledání a dalších pokročilejších scénářů. Například s vlastní serializací můžete:

* Zahrnutí nebo vyloučení některých vlastností vaší třídy modelu z ukládání jako polí dokumentů.
* Mapování mezi názvy vlastností ve vašem kódu a názvech polí v indexu.
* Vytvořte vlastní atributy, které lze použít pro mapování vlastností na pole dokumentu.

Příklady implementace vlastní serializace najdete v části testy jednotek pro sadu Azure Kognitivní hledání .NET SDK na GitHubu. Dobrým výchozím bodem je [Tato složka](https://github.com/Azure/azure-sdk-for-net/tree/4f6f4e4c90200c1b0621c4cead302a91e89f2aba/sdk/search/Microsoft.Azure.Search/tests/Tests/Models). Obsahuje třídy, které jsou používány vlastními testy serializace.

### <a name="searching-for-documents-in-the-index"></a>Hledání dokumentů v indexu
Posledním krokem v ukázkové aplikaci je hledání některých dokumentů v indexu:

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

Pokaždé, když spustí dotaz, tato metoda nejprve vytvoří nový `SearchParameters` objekt. Tento objekt se používá k určení dalších možností pro dotaz, jako je řazení, filtrování, stránkování a omezující vlastnosti. V této metodě nastavujeme `Filter` `Select` vlastnost,, `OrderBy` a `Top` pro různé dotazy. Všechny `SearchParameters` vlastnosti jsou popsány [zde](/dotnet/api/microsoft.azure.search.models.searchparameters).

Dalším krokem je ve skutečnosti spustit vyhledávací dotaz. Spuštění vyhledávání je provedeno pomocí `Documents.Search` metody. Pro každý dotaz předáte hledaný text, který se má použít jako řetězec (nebo `"*"` Pokud není k dispozici žádný hledaný text) a také parametry hledání, které jste vytvořili dříve. Také určujeme `Hotel` jako parametr typu pro `Documents.Search` , který dává sadě SDK pokyn k deserializaci dokumentů ve výsledcích hledání do objektů typu `Hotel` .

> [!NOTE]
> Další informace o syntaxi výrazu vyhledávacího dotazu najdete [tady](/rest/api/searchservice/Simple-query-syntax-in-Azure-Search).
> 
> 

Nakonec po každém dotazu Tato metoda projde všechny shody ve výsledcích hledání a vytiskne každý dokument do konzoly:

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

Pojďme se podrobněji podívat na každý z dotazů. Zde je kód pro provedení prvního dotazu:

```csharp
parameters =
    new SearchParameters()
    {
        Select = new[] { "HotelName" }
    };

results = indexClient.Documents.Search<Hotel>("motel", parameters);

WriteDocuments(results);
```

V tomto případě prohledáváme celý index pro slovo "Motel" v jakémkoli vyhledávacím poli a chceme pouze načíst názvy hotelů, jak je uvedeno v `Select` parametru. Tady jsou výsledky:

```output
Name: Secret Point Motel

Name: Twin Dome Motel
```

Další dotaz je trochu zajímavější.  Chceme najít všechny hotely, které mají místnost s noční frekvencí menší než $100 a vracet jenom ID a popis hotelu:

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

Tento dotaz používá výraz OData `$filter` `Rooms/any(r: r/BaseRate lt 100)` pro filtrování dokumentů v indexu. Pomocí [operátoru any](./search-query-odata-collection-operators.md) se v kolekci místností aplikuje ' BaseRate lt 100 ' na všechny položky. Další informace o syntaxi OData, kterou Azure Kognitivní hledání podporuje, najdete [tady](./query-odata-filter-orderby-syntax.md).

Tady jsou výsledky dotazu:

```output
HotelId: 1
Description: The hotel is ideally located on the main commercial artery of the city in the heart of New York...

HotelId: 2
Description: The hotel is situated in a nineteenth century plaza, which has been expanded and renovated to...
```

Nyní chceme najít první dva hotely, které byly naposledy renovated, a zobrazit název hotelu a datum poslední renovace. Zde je kód: 

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

V tomto případě znovu použijeme syntaxi OData k zadání `OrderBy` parametru jako `lastRenovationDate desc` . Také jsme nastavili `Top` na 2, aby bylo zajištěno, že pouze získáme horní dva dokumenty. Stejně jako dřív jsme nastavili, `Select` která pole se mají vrátit.

Tady jsou výsledky:

```output
Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00
```

Nakonec chceme najít všechny názvy hotelů, které odpovídají slovu "Hotel":

```csharp
parameters = new SearchParameters()
{
    SearchFields = new[] { "HotelName" }
};
results = indexClient.Documents.Search<Hotel>("hotel", parameters);

WriteDocuments(results);
```

A tady jsou výsledky, které zahrnují všechna pole, protože jsme neurčili `Select` vlastnost:

```output
    HotelId: 3
    Name: Triple Landscape Hotel
    ...
```

Tento krok dokončí kurz, ale nezastaví se zde. * * Další kroky poskytují další zdroje informací, které vám pomůžou získat další informace o Azure Kognitivní hledání.

## <a name="next-steps"></a>Další kroky
* Projděte si referenční materiály pro [.NET SDK](/dotnet/api/microsoft.azure.search) a [REST API](/rest/api/searchservice/).
* Přečtěte si [zásady vytváření názvů](/rest/api/searchservice/Naming-rules) a Naučte se pravidla pro pojmenovávání různých objektů.
* Zkontrolujte [podporované datové typy](/rest/api/searchservice/Supported-data-types) v Azure kognitivní hledání.