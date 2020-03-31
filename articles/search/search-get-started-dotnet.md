---
title: 'Úvodní příručka: Vytvoření indexu vyhledávání v c# pomocí rozhraní .NET'
titleSuffix: Azure Cognitive Search
description: V tomto c# rychlý start, zjistěte, jak vytvořit index, načíst data a spustit dotazy pomocí Azure Cognitive Search .NET SDK.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 02/10/2020
ms.openlocfilehash: 3d0006a3c77050c1bb21a0da8d6be51e659f933d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77589211"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-c-using-the-net-sdk"></a>Úvodní příručka: Vytvoření indexu Azure Cognitive Search v C# pomocí sady .NET SDK
> [!div class="op_single_selector"]
> * [C #](search-get-started-dotnet.md)
> * [Portál](search-get-started-portal.md)
> * [PowerShell](search-create-index-rest-api.md)
> * [Python](search-get-started-python.md)
> * [Postman](search-get-started-postman.md)
>*

Vytvořte konzolovou aplikaci .NET Core C#, která vytvoří, načte a načte index Azure Cognitive Search pomocí Sady Visual Studio a [sady Azure Cognitive Search .NET SDK](https://aka.ms/search-sdk). Tento článek vysvětluje, jak vytvořit aplikaci krok za krokem. Případně si můžete [stáhnout a spustit celou aplikaci](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/Quickstart).

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

> [!NOTE]
> Ukázkový kód v tomto článku používá synchronní metody Azure Cognitive Search .NET SDK pro jednoduchost. Pro produkční scénáře však doporučujeme používat asynchronní metody ve vlastních aplikacích, aby byly škálovatelné a responzivní. Můžete například použít `CreateAsync` `DeleteAsync` a `Create` místo `Delete`a .

## <a name="prerequisites"></a>Požadavky

Pro tento rychlý start jsou vyžadovány následující služby a nástroje.

+ [Visual Studio](https://visualstudio.microsoft.com/downloads/), libovolná edice. Ukázkový kód a pokyny byly testovány na bezplatném vydání Community.

+ [Vytvořte službu Azure Cognitive Search](search-create-service-portal.md) nebo [najděte existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) v rámci aktuálního předplatného. Pro tento rychlý start můžete využít bezplatnou službu.

<a name="get-service-info"></a>

## <a name="get-a-key-and-url"></a>Získání klíče a adresy URL

Volání služby vyžadují koncový bod adresy URL a přístupový klíč na každém požadavku. Vyhledávací služba se vytvoří s oběma, takže pokud jste do předplatného přidali Azure Cognitive Search, postupujte podle následujících kroků a získejte potřebné informace:

1. [Přihlaste se na portál Azure](https://portal.azure.com/)portal a na stránce **Přehled** vyhledávací služby získáte adresu URL. Příkladem koncového bodu může být `https://mydemo.search.windows.net`.

2. V **nastavení** > **klíče**, získat klíč správce pro úplná práva ke službě. Existují dva zaměnitelné klíče pro správu, které jsou k dispozici pro kontinuitu podnikání v případě, že potřebujete převrátit jeden. Primární nebo sekundární klíč můžete použít při požadavcích na přidávání, úpravy a odstranění objektů.

   Získejte také klíč dotazu. Je osvědčeným postupem pro vydávání požadavků na dotazy s přístupem jen pro čtení.

![Získání koncového bodu HTTP a přístupového klíče](media/search-get-started-postman/get-url-key.png "Získání koncového bodu HTTP a přístupového klíče")

Všechny požadavky vyžadují klíč rozhraní api na každý požadavek odeslaný do vaší služby. Platný klíč vytváří na základě žádosti vztah důvěryhodnosti mezi aplikací, která žádost odeslala, a službou, která ji zpracovává.

## <a name="set-up-your-environment"></a>Nastavení prostředí

Začněte otevřením sady Visual Studio a vytvořením nového projektu konzolové aplikace, který lze spustit v centru .NET Core.

### <a name="install-nuget-packages"></a>Instalace balíčků NuGet

[Sada Azure Cognitive Search .NET SDK](https://aka.ms/search-sdk) se skládá z několika klientských knihoven, které jsou distribuovány jako balíčky NuGet.

Pro tento projekt použijte verzi `Microsoft.Azure.Search` 9 balíčku NuGet a nejnovější `Microsoft.Extensions.Configuration.Json` balíček NuGet.

1. Ve **Správci** > **balíčků Nástroje NuGet**vyberte Spravovat **balíčky NuGet pro řešení...**. 

1. Klikněte na **Browse** (Procházet).

1. Vyhledejte `Microsoft.Azure.Search` a vyberte verzi 9.0.1 nebo novější.

1. Kliknutím na **Nainstalovat** vpravo přidáte sestavení do projektu a řešení.

1. Tento `Microsoft.Extensions.Configuration.Json`postup opakujte pro položku , výběr emverze 2.2.0 nebo novější.


### <a name="add-azure-cognitive-search-service-information"></a>Přidání informací o službě Azure Cognitive Search

1. V Průzkumníku řešení klikněte pravým tlačítkem myši na projekt a vyberte **Přidat** > **novou položku...** . 

1. V přidat novou položku vyhledejte "JSON" vrátit json související seznam typů položek.

1. Zvolte **Soubor JSON**, pojmenujte soubor "appsettings.json" a klepněte na **tlačítko Přidat**. 

1. Přidejte soubor do výstupního adresáře. Klepněte pravým tlačítkem myši na soubor appsettings.json a vyberte příkaz **Vlastnosti**. V **seznamu Kopírovat do výstupního adresáře**vyberte **Kopírovat, pokud je novější**.

1. Zkopírujte následující JSON do nového souboru JSON. Nahraďte název vyhledávací služby (YOUR-SEARCH-SERVICE-NAME) a klíč rozhraní API pro správu (YOUR-ADMIN-API-KEY) platnými hodnotami. Pokud je `https://mydemo.search.windows.net`koncový bod služby , název služby bude "mydemo".

```json
{
  "SearchServiceName": "<YOUR-SEARCH-SERVICE-NAME>",
  "SearchServiceAdminApiKey": "<YOUR-ADMIN-API-KEY>",
  "SearchIndexName": "hotels-quickstart"
}
```

### <a name="add-class-method-files-to-your-project"></a>Přidat třídu ". Metoda" soubory do projektu

Při tisku výsledků do okna konzoly musí být jednotlivá pole z objektu Hotel vrácena jako řetězce. Můžete implementovat [ToString()](https://docs.microsoft.com/dotnet/api/system.object.tostring?view=netframework-4.8) k provedení tohoto úkolu, kopírování potřebný kód do dvou nových souborů.

1. Přidejte do projektu dvě prázdné definice tříd: Address.Methods.cs, Hotel.Methods.cs

1. V Address.Methods.cs přepište výchozí obsah následujícím kódem [řádky 1-32](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/Quickstart/AzureSearchQuickstart/Address.Methods.cs/#L1-L32).

1. V Hotel.Methods.cs zkopírujte [řádky 1-66](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/Quickstart/AzureSearchQuickstart/Hotel.Methods.cs/#L1-L66).


## <a name="1---create-index"></a>1 - Vytvořit index

Index hotelů se skládá z jednoduchých a složitých polí, kde jednoduché pole je "HotelName" nebo "Description" a komplexní pole jsou adresa s dílčími poli nebo sbírka místností. Pokud index obsahuje komplexní typy, izolujte definice složitých polí v samostatných třídách.

1. Přidejte do projektu dvě prázdné definice tříd: Address.cs, Hotel.cs

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

1. V Hotel.cs třída definuje celkovou strukturu indexu, včetně odkazů na třídu adresy.

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

    Atributy v poli určují, jak se používá v aplikaci. `IsSearchable` Atribut musí být například přiřazen ke každému poli, které by mělo být zahrnuto do fulltextového vyhledávání. 
    
    > [!NOTE]
    > V sdk.net sdk musí být [`IsSearchable`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.issearchable?view=azure-dotnet) [`IsFilterable`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.isfilterable?view=azure-dotnet)pole [`IsSortable`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.issortable?view=azure-dotnet)explicitně přiřazena jako , , a [`IsFacetable`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.isfacetable?view=azure-dotnet). Toto chování je v rozporu s rozhraním REST API, které implicitně umožňuje atribuci na základě datového typu (například jednoduchá řetězcová pole jsou automaticky prohledávatelná).

    Přesně jedno pole v `string` indexu typu musí být *klíčové* pole, které jednoznačně identifikuje každý dokument. V tomto schématu je `HotelId`klíč .

    V tomto indexu používají pole [`analyzer`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.analyzer?view=azure-dotnet) popisu volitelnou vlastnost určenou v případě, že chcete přepsat výchozí standardní analyzátor Lucene. Pole `description_fr` používá francouzský analyzátor Lucene ([FrLucene](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername.frlucene?view=azure-dotnet)), protože ukládá francouzský text. Používá `description` volitelný analyzátor jazyka Microsoft ([EnMicrosoft](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername.enmicrosoft?view=azure-dotnet)).

1. V Program.cs vytvořte instanci [`SearchServiceClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient?view=azure-dotnet) třídy pro připojení ke službě pomocí hodnot, které jsou uloženy v konfiguračním souboru aplikace (appsettings.json). 

   `SearchServiceClient`má [`Indexes`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.indexes?view=azure-dotnet) vlastnost, která poskytuje všechny metody, které potřebujete k vytvoření, seznam, aktualizace nebo odstranění indexů Azure Cognitive Search. 

    ```csharp
    using System;
    using System.Linq;
    using System.Threading;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Microsoft.Extensions.Configuration;

    namespace AzureSearchQuickstart
    {
        class Program {
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

    Pokud je to možné, `SearchServiceClient` sdílet jednu instanci v aplikaci, aby se zabránilo otevření příliš mnoho připojení. Metody třídy jsou bezpečné pro přístup z více vláken, aby bylo možné takové sdílení povolit.

   Třída má několik konstruktorů. Ten, který chcete, přijímá jako parametry název vaší vyhledávací služby a objekt `SearchCredentials`. `SearchCredentials` zabalí váš klíč api-key.

    V definici indexu nejjednodušší způsob, `Field` jak vytvořit objekty je `FieldBuilder.BuildForType` voláním metody, předání třídy modelu pro parametr typu. Třída modelu obsahuje vlastnosti, které se mapují na pole vašeho indexu. Toto mapování umožňuje svázat dokumenty z indexu vyhledávání s instancemi třídy modelu.

    > [!NOTE]
    > Pokud nemáte v úmyslu používat třídu modelu, stále můžete definovat index přímým vytvořením objektů `Field`. Můžete zadat název pole do konstruktoru, společně s datovým typem (nebo analyzátor pro pole řetězce). Můžete také nastavit další `IsSearchable` `IsFilterable`vlastnosti, jako je , abychom jmenovali několik.
    >

1. Stisknutím klávesy F5 vytvořte aplikaci a vytvořte index. 

    Pokud se projekt úspěšně vytvoří, otevře se okno konzoly, které zapisuje stavové zprávy na obrazovku pro odstranění a vytvoření indexu. 

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - Načíst dokumenty

V Azure Cognitive Search jsou dokumenty datové struktury, které jsou vstupy pro indexování a výstupy z dotazů. Jak bylo získáno z externího zdroje dat, vstupy dokumentu mohou být řádky v databázi, objekty BLOB v úložišti objektů Blob nebo dokumenty JSON na disku. V tomto příkladu bereme zkratku a vkládání dokumentů JSON pro čtyři hotely v samotném kódu. 

Při nahrávání dokumentů je nutné [`IndexBatch`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet) použít objekt. Obsahuje `IndexBatch` kolekci [`IndexAction`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexaction?view=azure-dotnet) objektů, z nichž každý obsahuje dokument a vlastnost, která říká Azure Cognitive Search, jakou akci provést[(nahrát, sloučit, odstranit a sloučitOrUpload).](search-what-is-data-import.md#indexing-actions)

1. V Program.cs vytvořte pole dokumentů a akcí indexu a `IndexBatch`poté předajte pole aplikaci . Níže uvedené dokumenty odpovídají indexu hotel-quickstart, jak je definován v hotelových a adresních třídách.

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

    Po inicializaci`IndexBatch` objektu jej můžete odeslat [`Documents.Index`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.index?view=azure-dotnet) do [`SearchIndexClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) indexu voláním objektu. `Documents`je `SearchIndexClient` vlastnost, která poskytuje metody pro přidávání, úpravy, odstranění nebo dotazování dokumentů v indexu.

    `try` / Okolní `catch` volání `Index` metody zachytí selhání indexování, které může dojít, pokud je vaše služba pod velkým zatížením. V produkčním kódu můžete zpozdit a opakovat indexování dokumentů, které se nezdařily, nebo protokolovat a pokračovat stejně jako ukázka, nebo s ním zpracovat jiným způsobem, který splňuje požadavky na konzistenci dat vaší aplikace.

    2 sekundzpoždění kompenzuje indexování, což je asynchronní, takže všechny dokumenty mohou být indexovány před spouštění dotazů. Kódování ve zpoždění je obvykle nutné pouze v ukázkách, testech a ukázkových aplikacích.

1. V Program.cs, v hlavní, odkomentujte řádky pro "2 - Load documents". 

    ```csharp
    // Uncomment next 3 lines in "2 - Load documents"
    ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(indexClient);
    ```
1. Stisknutím klávesy F5 aplikaci znovu vytvořte. 

    Pokud se projekt úspěšně vytvoří, otevře se okno konzoly, které zapisuje stavové zprávy, tentokrát se zprávou o nahrávání dokumentů. Na webu Azure Portal na stránce **Přehled** vyhledávací služby by měl nyní mít index rychlých startů hotelů 4 dokumenty.

Další informace o zpracování dokumentů naleznete v [tématu "Jak sada .NET SDK zpracovává dokumenty"](search-howto-dotnet-sdk.md#how-dotnet-handles-documents).

## <a name="3---search-an-index"></a>3. Prohledání indexu

Výsledky dotazu můžete získat, jakmile je indexován první dokument, ale skutečné testování indexu by mělo počkat, dokud nebudou indexovány všechny dokumenty. 

Tato část přidává dvě části funkce: logiku dotazu a výsledky. Pro dotazy použijte [`Search`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.search?view=azure-dotnet
) metodu. Tato metoda přebírá hledaný text, stejně jako další [parametry](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters?view=azure-dotnet). 

Třída [`DocumentsSearchResult`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1?view=azure-dotnet) představuje výsledky.


1. V Program.cs vytvořte metodu WriteDocuments, která vytiskne výsledky hledání do konzoly.

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

1. Vytvořte metodu RunQueries pro spouštění dotazů a vrácení výsledků. Výsledky jsou hotelové objekty. Select parametr můžete použít k vykreslovat jednotlivá pole. Pokud pole není zahrnuto v parametru select, jeho odpovídající vlastnost Hotel bude null.

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

    Existují dva [způsoby párování výrazů v dotazu:](search-query-overview.md#types-of-queries)fulltextové vyhledávání a filtry. Fulltextový vyhledávací dotaz vyhledá v polích `IsSearchable` v indexu jeden nebo více termínů. Filtr je logický výraz, který `IsFilterable` je vyhodnocován přes pole v indexu. Fulltextové vyhledávání a filtry můžete použít společně nebo samostatně.

    Vyhledávání i filtrování se provádí pomocí metody `Documents.Search`. Vyhledávací dotaz lze předat v parametru `searchText`, zatímco výraz filtru lze předat ve vlastnosti `Filter` třídy `SearchParameters`. Chcete-li filtrovat bez vyhledávání, stačí předat `"*"` jako hodnotu parametru `searchText`. Chcete-li vyhledávat bez filtrování, ponechte vlastnost `Filter` nenastavenou nebo instanci `SearchParameters` vůbec nepředávejte.

1. V Program.cs, v hlavní, odkomentujte řádky pro "3 - Hledat". 

    ```csharp
    // Uncomment next 2 lines in "3 - Search an index"
    Console.WriteLine("{0}", "Searching documents...\n");
    RunQueries(indexClient);
    ```
1. Řešení je nyní dokončeno. Stisknutím klávesy F5 aplikaci znovu sestavíte a spustíte program v plném rozsahu. 

    Výstup obsahuje stejné zprávy jako dříve, s přidáním informací o dotazu a výsledků.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud pracujete s vlastním předplatným, je vhodné vždy na konci projektu zkontrolovat, jestli budete vytvořené prostředky ještě potřebovat. Prostředky, které necháte běžet, vás můžou stát peníze. Prostředky můžete odstraňovat jednotlivě nebo můžete odstranit skupinu prostředků, a odstranit tak celou sadu prostředků najednou.

Můžete najít a spravovat prostředky na portálu pomocí odkazu **Všechny prostředky** nebo **skupiny prostředků** v levém navigačním podokně.

Pokud používáte bezplatnou službu, nezapomeňte, že jste omezeni na tři indexy, indexery a zdroje dat. Můžete odstranit jednotlivé položky na portálu, abyste zůstali pod limitem. 

## <a name="next-steps"></a>Další kroky

V tomto c# rychlý start jste pracovali prostřednictvím řady úkolů k vytvoření indexu, načíst s dokumenty a spouštět dotazy. V různých fázích jsme vzali zkratky, abychom zjednodušili kód pro čitelnost a porozumění. Pokud jste spokojeni se základními pojmy, doporučujeme další článek pro zkoumání alternativních přístupů a konceptů, které prohloubí vaše znalosti. 

Ukázkový kód a index jsou rozšířené verze tohoto. Další ukázka přidá Rooms kolekce, používá různé třídy a akce a provede bližší pohled na to, jak zpracování funguje.

> [!div class="nextstepaction"]
> [Jak se rozvíjet v rozhraní .NET](search-howto-dotnet-sdk.md)
