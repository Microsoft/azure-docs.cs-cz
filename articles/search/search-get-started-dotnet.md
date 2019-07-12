---
title: 'C#Rychlý start: Vytvoření, načtení a dotazování pomocí sady .NET SDK – Azure Search indexů'
description: Vysvětluje, jak vytvořit index, načtení dat a spouštění dotazů pomocí C# a Azure Search .NET SDK.
author: heidisteen
manager: cgronlun
ms.author: heidist
tags: azure-portal
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/11/2019
ms.openlocfilehash: ddbe517510a3f7d1295c8970c13020baa3efacf0
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840299"
---
# <a name="quickstart-create-an-azure-search-index-in-c-using-the-net-sdk"></a>Rychlý start: Vytvoření indexu Azure Search v C# pomocí sady .NET SDK
> [!div class="op_single_selector"]
> * [C#](search-get-started-dotnet.md)
> * [Azure Portal](search-get-started-portal.md)
> * [PowerShell](search-create-index-rest-api.md)
> * [Python](search-get-started-python.md)
> * [Postman](search-get-started-postman.md)
>*

Vytvoření .NET Core C# konzolovou aplikaci, která vytvoří, načte a dotazy index Azure Search pomocí sady Visual Studio a [Azure Search .NET SDK](https://aka.ms/search-sdk). Tento článek vysvětluje, jak vytvořit aplikaci krok za krokem. Alternativně můžete [stáhnout a spustit je aplikace dokončena a](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/Quickstart).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

> [!NOTE]
> Ukázka kódu v tomto článku používá pro jednoduchost synchronní metody sady Azure Search .NET SDK. Pro produkční scénáře, ale doporučujeme používat asynchronní metody ve svých vlastních aplikacích je zajištění škálovatelných a interaktivních. Například můžete použít `CreateAsync` a `DeleteAsync` místo `Create` a `Delete`.

## <a name="prerequisites"></a>Požadavky

Následující služby, nástroje a data se používají v tomto rychlém startu. 

+ [Visual Studio](https://visualstudio.microsoft.com/downloads/), všechny edice. Ukázky kódu a instrukce byly testovány v bezplatná edice Community.

+ Ukázkový index a dokumenty jsou uvedené v tomto článku, stejně jako v [řešení sady Visual Studio](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart) pro tento rychlý start.

+ [Vytvoření služby Azure Search](search-create-service-portal.md) nebo [najít existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) pod vaším aktuálním předplatným. Můžete použít bezplatnou službou pro tento rychlý start.

<a name="get-service-info"></a>

## <a name="get-a-key-and-url"></a>Získejte klíč a adresy URL

Volání služby vyžadují koncový bod adresy URL a přístupový klíč pro každý požadavek. Vyhledávací služba se vytvoří s oběma, takže pokud jste do svého předplatného přidali službu Azure Search, získejte potřebné informace pomocí následujícího postupu:

1. [Přihlaste se k webu Azure portal](https://portal.azure.com/)a ve vyhledávací službě **přehled** stránce, získat adresu URL. Příkladem koncového bodu může být `https://mydemo.search.windows.net`.

2. V **nastavení** > **klíče**, získat klíč pro úplná práva správce na službu. Existují dva klíče zaměnitelné správce, v případě, že budete potřebovat k výměně jeden k dispozici zajišťuje nepřetržitý chod podniků. U požadavků můžete použít buď primární nebo sekundární klíč pro přidání, úpravy a odstraňování objektů.

   Získáte také klíč dotazu. Je osvědčeným postupem k vydávání požadavků na dotazy s přístupem jen pro čtení.

![Získejte koncový bod a přístupový klíč rozhraní HTTP](media/search-get-started-postman/get-url-key.png "získat HTTP koncový bod a přístupový klíč")

Všechny požadavky vyžaduje klíč rozhraní api na každou požadavku odeslaného do vaší služby. Platný klíč vytváří na základě žádosti vztah důvěryhodnosti mezi aplikací, která žádost odeslala, a službou, která ji zpracovává.

## <a name="set-up-your-environment"></a>Nastavení prostředí

Začněte tím, že otevření sady Visual Studio a vytvoření nového projektu konzolové aplikace, které můžete spustit na .NET Core.

### <a name="install-nuget-packages"></a>Instalace balíčků NuGet

[Azure Search .NET SDK](https://aka.ms/search-sdk) se skládá z několika klientských knihoven, které jsou distribuovány jako balíčky NuGet.

Pro tento projekt používat verzi 9 `Microsoft.Azure.Search` NuGet package a nejnovější `Microsoft.Extensions.Configuration.Json` balíček NuGet.

1. V **nástroje** > **Správce balíčků NuGet**vyberte **spravovat balíčky NuGet pro řešení...** . 

1. Klikněte na **Browse** (Procházet).

1. Vyhledejte `Microsoft.Azure.Search` a vyberte verzi 9.0.1 nebo novější.

1. Klikněte na tlačítko **nainstalovat** na pravé straně sestavení přidejte do projektu a řešení.

1. Opakujte pro `Microsoft.Extensions.Configuration.Json`, výběr verze 2.2.0 nebo novější.


### <a name="add-azure-search-service-information"></a>Přidejte informace o službě Azure Search

1. V Průzkumníku řešení klikněte pravým tlačítkem na projekt a vyberte **přidat** > **novou položku...**  . 

1. Přidání nové položky vyhledejte "JSON" vrátí seznam typů položek souvisejících s JSON.

1. Zvolte **soubor JSON**, pojmenujte soubor "appsettings.json" a klikněte na tlačítko **přidat**. 

1. Přidejte soubor do výstupního adresáře. Klikněte pravým tlačítkem na appsettings.json a vyberte **vlastnosti**. V **kopírovat do výstupního adresáře**vyberte **kopírovat, pokud je novější**.

1. Zkopírujte následující kód JSON do nového souboru JSON. Platné hodnoty nahraďte název vyhledávací služby (YOUR-SEARCH-SERVICE-NAME) a klíč rozhraní API pro správu (YOUR-ADMIN-API-KEY). Pokud váš koncový bod služby je `https://mydemo.search.windows.net`, název služby by měl být "mydemo".

```json
{
  "SearchServiceName": "<YOUR-SEARCH-SERVICE-NAME>",
  "SearchServiceAdminApiKey": "<YOUR-ADMIN-API-KEY>",
  "SearchIndexName": "hotels-quickstart"
}
```

### <a name="add-class-method-files-to-your-project"></a>Přidejte třídu ". Metoda"soubory do projektu

Při tisku výsledků v okně konzoly, jednotlivá pole z objektu hotelu musí být vrácena jako řetězce. Můžete implementovat [ToString()](https://docs.microsoft.com/dotnet/api/system.object.tostring?view=netframework-4.8) k provedení této úlohy kopírování nezbytného kódu do dva nové soubory.

1. Do projektu přidejte dvě definice prázdné třídy: Address.Methods.cs, Hotel.Methods.cs

1. V Address.Methods.cs, přepsat výchozí obsah následujícím kódem, [řádky 1-32](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/Quickstart/AzureSearchQuickstart/Address.Methods.cs/#L1-L32).

1. V Hotel.Methods.cs, zkopírujte [řádky 1 66](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/Quickstart/AzureSearchQuickstart/Hotel.Methods.cs/#L1-L66).


## <a name="1---create-index"></a>1 – Vytvoření indexu

Jednoduché a komplexní polí, kde je jednoduché pole "HotelName" nebo "Popis" a komplexní pole jsou adresu s dílčí pole nebo kolekce místnosti se skládá indexu hotels. Pokud indexu zahrnuje komplexní typy, izolujte definice komplexní polí v samostatné třídy.

1. Do projektu přidejte dvě definice prázdné třídy: Address.cs, Hotel.cs

1. V Address.cs přepište výchozí obsah následujícím kódem:

    ```csharp
    using System;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Newtonsoft.Json;

    namespace AzureSearchQuickstart
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

1. Soubor hotel.cs definuje třídu celkovou strukturu indexu, včetně odkazů na třídy adres.

    ```csharp
    namespace AzureSearchQuickstart
    {
        using System;
        using Microsoft.Azure.Search;
        using Microsoft.Azure.Search.Models;
        using Newtonsoft.Json;

        public partial class Hotel
        {
            [System.ComponentModel.DataAnnotations.Key]
            [IsFilterable]
            public string HotelId { get; set; }

            [IsSearchable, IsSortable]
            public string HotelName { get; set; }

            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.EnMicrosoft)]
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

            [IsFilterable, IsSortable, IsFacetable]
            public DateTimeOffset? LastRenovationDate { get; set; }

            [IsFilterable, IsSortable, IsFacetable]
            public double? Rating { get; set; }

            public Address Address { get; set; }
        }
    }
    ```

    Atributy pole určují, jak se používají v aplikaci. Například `IsSearchable` atributu se musí přiřadit každé pole, které by měl být součástí fulltextové vyhledávání. 
    
    > [!NOTE]
    > V sadě .NET SDK, musí být explicitně atributy polí retrievable a searchable [ `IsSearchable` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.issearchable?view=azure-dotnet), [ `IsFilterable` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.isfilterable?view=azure-dotnet), [ `IsSortable` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.issortable?view=azure-dotnet), a [ `IsFacetable` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.isfacetable?view=azure-dotnet). Toto chování je rozdíl od rozhraní REST API, která implicitně umožňuje přiřazení na základě datového typu (například jednoduchým řetězcem pole jsou automaticky prohledávatelné).

    Právě jedno pole v indexu typu `string` musí být *klíč* pole jednoznačné identifikaci každého dokumentu. V tomto schématu, že klíč je `HotelId`.

    V indexu, použijte volitelný popis pole [ `analyzer` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.analyzer?view=azure-dotnet) zadaná vlastnost, pokud chcete přepsat výchozí standardní analyzátor Lucene. `description_fr` Pole používá analyzátor Lucene francouzština ([FrLucene](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername.frlucene?view=azure-dotnet)) vzhledem k tomu, že ukládá francouzského textu. `description` Používá volitelné analyzátor jazyka Microsoft ([EnMicrosoft](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername.enmicrosoft?view=azure-dotnet)).

1. V souboru Program.cs vytvořte instanci [ `SearchServiceClient` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient?view=azure-dotnet) třídy pro připojení ke službě pomocí hodnoty, které jsou uložené v konfiguračním souboru aplikace (appsettings.json). 

   `SearchServiceClient` má [ `Indexes` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.indexes?view=azure-dotnet) vlastnost poskytuje všechny metody, které potřebujete k vytvoření, seznam, aktualizace nebo odstranění indexů Azure Search. 

    ```csharp
    using System;
    using System.Linq;
    using System.Threading;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Microsoft.Extensions.Configuration;

    namespace AzureSearchQuickstart
    {
        class Program
            // Demonstrates index delete, create, load, and query
            // Commented-out code is uncommented in later steps
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

                // Uncomment next 3 lines in "2 - Load documents"
                // ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
                // Console.WriteLine("{0}", "Uploading documents...\n");
                // UploadDocuments(indexClient);

                // Uncomment next 2 lines in "3 - Search an index"
                // Console.WriteLine("{0}", "Searching index...\n");
                // RunQueries(indexClient);

                Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
                Console.ReadKey();
            }

            // Create the search service client
            private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
            {
                string searchServiceName = configuration["SearchServiceName"];
                string adminApiKey = configuration["SearchServiceAdminApiKey"];

                SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
                return serviceClient;
            }

            // Delete an existing index to reuse its name
            private static void DeleteIndexIfExists(string indexName, SearchServiceClient serviceClient)
            {
                if (serviceClient.Indexes.Exists(indexName))
                {
                    serviceClient.Indexes.Delete(indexName);
                }
            }

            // Create an index whose fields correspond to the properties of the Hotel class.
            // The Address property of Hotel will be modeled as a complex field.
            // The properties of the Address class in turn correspond to sub-fields of the Address complex field.
            // The fields of the index are defined by calling the FieldBuilder.BuildForType() method.
            private static void CreateIndex(string indexName, SearchServiceClient serviceClient)
            {
                var definition = new Index()
                {
                    Name = indexName,
                    Fields = FieldBuilder.BuildForType<Hotel>()
                };

                serviceClient.Indexes.Create(definition);
            }
        }
    }    
    ```

    Pokud je to možné, sdílet jednu instanci `SearchServiceClient` ve vaší aplikaci, aby se zabránilo otevírání příliš mnoha připojení. Metody třídy jsou vláknově bezpečné pro takové sdílení umožňují.

   Třída obsahuje několik konstruktorů. Ten, který chcete, přijímá jako parametry název vaší vyhledávací služby a objekt `SearchCredentials`. `SearchCredentials` zabalí váš klíč api-key.

    V definici indexu, nejjednodušší způsob, jak vytvořit `Field` objekty je voláním `FieldBuilder.BuildForType` metoda předání třídy modelu pro parametr typu. Třída modelu obsahuje vlastnosti, které se mapují na pole vašeho indexu. Toto mapování umožňuje vytvořit vazbu mezi dokumenty z indexu search do instance třídy modelu.

    > [!NOTE]
    > Pokud nemáte v úmyslu používat třídu modelu, stále můžete definovat index přímým vytvořením objektů `Field`. Můžete zadat název pole do konstruktoru, společně s datovým typem (nebo analyzátor pro pole řetězce). Můžete také nastavit další vlastnosti, například `IsSearchable`, `IsFilterable`, pár.
    >

1. Stiskněte klávesu F5 k sestavení aplikace a vytvořte index. 

    Pokud projekt vytvoří úspěšně, otevře se okno konzoly vytváření stavových zpráv na obrazovku pro odstranění a vytvořit index. 

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 – nahrání dokumentů

Ve službě Azure Search dokumenty představují datové struktury, které jsou indexování i vstupy a výstupy z dotazů. Získaný z externího zdroje dat, může být dokument vstupy řádků v databázi, objekty BLOB v úložišti objektů Blob nebo dokumenty JSON na disku. V tomto příkladu jsme udělali zástupce a vkládání dokumentů JSON pro 4 hotely v samotný kód. 

Při nahrávání dokumentů, je nutné použít [ `IndexBatch` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet) objektu. `IndexBatch` Obsahuje kolekci [ `IndexAction` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexaction?view=azure-dotnet) objektů, z nichž každý obsahuje dokument a vlastnost Azure Search o tom, jakou akci chcete provést ([odeslání, sloučení, odstranění a mergeOrUpload](search-what-is-data-import.md#indexing-actions)).

1. V souboru Program.cs vytvořte pole akcí index a dokumenty a pak předejte pole, které chcete `IndexBatch`. Následující dokumenty odpovídat hotelu – rychlý start index, jak jsou definovány v hotelu a adresu tříd.

    ```csharp
    // Upload documents as a batch
    private static void UploadDocuments(ISearchIndexClient indexClient)
    {
        var actions = new IndexAction<Hotel>[]
        {
            IndexAction.Upload(
                new Hotel()
                {
                    HotelId = "1",
                    HotelName = "Secret Point Motel",
                    Description = "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
                    DescriptionFr = "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
                    Category = "Boutique",
                    Tags = new[] { "pool", "air conditioning", "concierge" },
                    ParkingIncluded = false,
                    LastRenovationDate = new DateTimeOffset(1970, 1, 18, 0, 0, 0, TimeSpan.Zero),
                    Rating = 3.6,
                    Address = new Address()
                    {
                        StreetAddress = "677 5th Ave",
                        City = "New York",
                        StateProvince = "NY",
                        PostalCode = "10022",
                        Country = "USA"
                    }
                }
            ),
            IndexAction.Upload(
                new Hotel()
                {
                    HotelId = "2",
                    HotelName = "Twin Dome Motel",
                    Description = "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
                    DescriptionFr = "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
                    Category = "Boutique",
                    Tags = new[] { "pool", "free wifi", "concierge" },
                    ParkingIncluded = false,
                    LastRenovationDate =  new DateTimeOffset(1979, 2, 18, 0, 0, 0, TimeSpan.Zero),
                    Rating = 3.60,
                    Address = new Address()
                    {
                        StreetAddress = "140 University Town Center Dr",
                        City = "Sarasota",
                        StateProvince = "FL",
                        PostalCode = "34243",
                        Country = "USA"
                    }
                }
            ),
            IndexAction.Upload(
                new Hotel()
                {
                    HotelId = "3",
                    HotelName = "Triple Landscape Hotel",
                    Description = "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
                    DescriptionFr = "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
                    Category = "Resort and Spa",
                    Tags = new[] { "air conditioning", "bar", "continental breakfast" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(2015, 9, 20, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4.80,
                    Address = new Address()
                    {
                        StreetAddress = "3393 Peachtree Rd",
                        City = "Atlanta",
                        StateProvince = "GA",
                        PostalCode = "30326",
                        Country = "USA"
                    }
                }
            ),
            IndexAction.Upload(
                new Hotel()
                {
                    HotelId = "4",
                    HotelName = "Sublime Cliff Hotel",
                    Description = "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
                    DescriptionFr = "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
                    Category = "Boutique",
                    Tags = new[] { "concierge", "view", "24-hour front desk service" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1960, 2, 06, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4.6,
                    Address = new Address()
                    {
                        StreetAddress = "7400 San Pedro Ave",
                        City = "San Antonio",
                        StateProvince = "TX",
                        PostalCode = "78216",
                        Country = "USA"
                    }
                }
            ),
        };

        var batch = IndexBatch.New(actions);

        try
        {
            indexClient.Documents.Index(batch);
        }
        catch (IndexBatchException e)
        {
            // When a service is under load, indexing might fail for some documents in the batch. 
            // Depending on your application, you can compensate by delaying and retrying. 
            // For this simple demo, we just log the failed document keys and continue.
            Console.WriteLine(
                "Failed to index some of the documents: {0}",
                String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
        }

        // Wait 2 seconds before starting queries 
        Console.WriteLine("Waiting for indexing...\n");
        Thread.Sleep(2000);
    }
    ```

    Po inicializaci`IndexBatch` objektu, můžete ho odeslat do indexu zavoláním [ `Documents.Index` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.index?view=azure-dotnet) na vaše [ `SearchIndexClient` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) objektu. `Documents` je součástí `SearchIndexClient` , který poskytuje metody pro přidání, úprava, odstranění nebo dotazování dokumentů v indexu.

    `try` / `catch` Obaluje volání `Index` zachytává metoda indexování chyb, může dojít, pokud je služba v případě velkého zatížení. V produkčním kódu může zpoždění a poté opakujte indexování dokumentů, které se nezdařilo, nebo protokolů jako ukázka dělá a pokračovat zpracování jiným způsobem, který splňuje požadavky na konzistenci dat vaší aplikace.

    Zpoždění 2sekundové vyrovnává indexování, což je asynchronní, takže všechny dokumenty můžete indexovat, předtím, než se spustí dotazy. Kódování zpoždění je obvykle nutné pouze v ukázky, testy a ukázkové aplikace.

1. V souboru Program.cs ve funkci main zrušte komentář u řádky pro "2 - zatížení dokumenty". 

    ```csharp
    // Uncomment next 3 lines in "2 - Load documents"
    ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(indexClient);
    ```
1. Stisknutím klávesy F5 aplikaci znovu vytvořit. 

    Pokud projekt vytvoří úspěšně, otevře se okno konzoly vytváření stavových zpráv, tentokrát se zprávou o nahrávání dokumentů. Na webu Azure Portal, ve službě search **přehled** stránce indexu hotels-quickstart teď měli mít 4 dokumenty.

Další informace o zpracování dokumentů najdete v tématu ["Jak .NET SDK zpracovává dokumenty"](search-howto-dotnet-sdk.md#how-dotnet-handles-documents).

## <a name="3---search-an-index"></a>3\. Prohledání indexu

Výsledky dotazu můžete získat, poté, co se indexuje zpětně první dokument, ale skutečné testování indexu měli počkat, dokud všechny dokumenty se indexují. 

Tato část přidá dva druhy funkce: logiku dotazu a výsledky. Pro dotazy, použijte [ `Search` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.search?view=azure-dotnet
) metody. Tato metoda přebírá hledaný text a také další [parametry](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters?view=azure-dotnet). 

[ `DocumentsSearchResult` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1?view=azure-dotnet) Třída představuje výsledky.


1. V souboru Program.cs vytvořte metodu WriteDocuments, vypíše výsledky do konzoly pro hledání.

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

1. Vytvořte metodu RunQueries ke spouštění dotazů a vrátí výsledky. Výsledky jsou objekty hotelu. Vyberte parametr slouží k surface jednotlivá pole. Pokud pole nejsou zahrnuty v parametru select, jeho odpovídající vlastnost hotelu bude mít hodnotu null.

    ```csharp
    private static void RunQueries(ISearchIndexClient indexClient)
    {
        SearchParameters parameters;
        DocumentSearchResult<Hotel> results;

        // Query 1 
        Console.WriteLine("Query 1: Search for term 'Atlanta' with no result trimming");
        parameters = new SearchParameters();
        results = indexClient.Documents.Search<Hotel>("Atlanta", parameters);
        WriteDocuments(results);

        // Query 2
        Console.WriteLine("Query 2: Search on the term 'Atlanta', with trimming");
        Console.WriteLine("Returning only these fields: HotelName, Tags, Address:\n");
        parameters =
            new SearchParameters()
            {
                Select = new[] { "HotelName", "Tags", "Address" },
            };
        results = indexClient.Documents.Search<Hotel>("Atlanta", parameters);
        WriteDocuments(results);

        // Query 3
        Console.WriteLine("Query 3: Search for the terms 'restaurant' and 'wifi'");
        Console.WriteLine("Return only these fields: HotelName, Description, and Tags:\n");
        parameters =
            new SearchParameters()
            {
                Select = new[] { "HotelName", "Description", "Tags" }
            };
        results = indexClient.Documents.Search<Hotel>("restaurant, wifi", parameters);
        WriteDocuments(results);

        // Query 4 -filtered query
        Console.WriteLine("Query 4: Filter on ratings greater than 4");
        Console.WriteLine("Returning only these fields: HotelName, Rating:\n");
        parameters =
            new SearchParameters()
            {
                Filter = "Rating gt 4",
                Select = new[] { "HotelName", "Rating" }
            };
        results = indexClient.Documents.Search<Hotel>("*", parameters);
        WriteDocuments(results);

        // Query 5 - top 2 results
        Console.WriteLine("Query 5: Search on term 'boutique'");
        Console.WriteLine("Sort by rating in descending order, taking the top two results");
        Console.WriteLine("Returning only these fields: HotelId, HotelName, Category, Rating:\n");
        parameters =
            new SearchParameters()
            {
                OrderBy = new[] { "Rating desc" },
                Select = new[] { "HotelId", "HotelName", "Category", "Rating" },
                Top = 2
            };
        results = indexClient.Documents.Search<Hotel>("boutique", parameters);
        WriteDocuments(results);
    }
    ```

    Existují dva [způsoby odpovídající podmínky v dotazu](search-query-overview.md#types-of-queries): fulltextové vyhledávání a filtrů. Fulltextové vyhledávání vyhledá jeden nebo více výrazů ve `IsSearchable` pole v indexu. Filtr je logický výraz, který je vyhodnocován v `IsFilterable` polí v indexu. Fulltextové vyhledávání a filtrů můžete použít společně nebo samostatně.

    Vyhledávání i filtrování se provádí pomocí metody `Documents.Search`. Vyhledávací dotaz lze předat v parametru `searchText`, zatímco výraz filtru lze předat ve vlastnosti `Filter` třídy `SearchParameters`. Chcete-li filtrovat bez vyhledávání, stačí předat `"*"` jako hodnotu parametru `searchText`. Chcete-li vyhledávat bez filtrování, ponechte vlastnost `Filter` nenastavenou nebo instanci `SearchParameters` vůbec nepředávejte.

1. V souboru Program.cs ve funkci main zrušte komentář u řádky pro "3 - hledání". 

    ```csharp
    // Uncomment next 2 lines in "3 - Search an index"
    Console.WriteLine("{0}", "Searching documents...\n");
    RunQueries(indexClient);
    ```
1. Toto řešení je nyní dokončena. Stisknutím klávesy F5 aplikaci znovu vytvořit a spustit program v celém rozsahu. 

    Výstup obsahuje stejné zprávy jako dříve, uveďte informace dotazu a výsledky.

## <a name="clean-up"></a>Vyčištění

Pokud pracujete ve svém vlastním předplatném, je vhodné na konci projektu a zjistěte, jestli stále potřebují prostředky, že kterou jste vytvořili. Levé může spuštěné prostředky nákladů peníze. Můžete odstranit prostředky jednotlivě nebo odstranit skupinu prostředků, kterou chcete odstranit celou sadu prostředků.

Můžete najít a spravovat prostředky na portálu pro použití **všechny prostředky** nebo **skupiny prostředků** odkaz v levém navigačním podokně.

Pokud používáte bezplatné služby, mějte na paměti, že jste omezeni na tři indexy, indexery a datového zdroje. Můžete odstranit jednotlivé položky na portálu, abychom dodrželi omezení. 

## <a name="next-steps"></a>Další kroky

V tomto C# rychlém startu jste nepracovali prostřednictvím řady úloh k vytvoření indexu načíst s dokumenty a spouštět dotazy. V různých fázích nám trvalo klávesové zkratky pro zjednodušení kódu pro lepší čitelnost procházením a porozuměním. Pokud jste obeznámeni se základními koncepty, doporučujeme, abyste dalším článku pro vysvětlení přístupy a koncepty, které budou prohloubit své znalosti v oblasti. 

Ukázkový kód a indexu jsou rozšířené verze tohoto objektu. Bude další vzorek přidá kolekci místnosti, používá jiné třídy a akcí a trvá blíže podívat, jak funguje zpracování.

> [!div class="nextstepaction"]
> [Jak vyvíjet v .NET](search-howto-dotnet-sdk.md)