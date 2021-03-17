---
title: Starší verze C# – rychlý Start
titleSuffix: Azure Cognitive Search
description: V tomto rychlém startu jazyka C# se k vytvoření, načtení a dotazování indexu vyhledávání používá knihovna klienta verze 10 (Microsoft. Azure. Search).
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 10/27/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 8dc2eb898c12e374bc503c5a05f00eb20667443b
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94701836"
---
# <a name="quickstart-create-a-search-index-using-the-legacy-microsoftazuresearch-v10-client-library"></a>Rychlý Start: vytvoření indexu vyhledávání pomocí starší verze Microsoft. Azure. Search Client Library v10 za účelem

Tento článek je rychlým startem v jazyce C# pro starší verzi klientské knihovny [**Microsoft. Azure. Search**](/dotnet/api/overview/azure/search/client10) (verze 10), která je teď nahrazená klientskou knihovnou [**Azure.Search.Documents**](/dotnet/api/overview/azure/search.documents-readme) (verze 11).

> [!NOTE]
> Pokud máte existující nebo vývojové projekty pro vývoj, můžete dál používat verzi 10. Ale pro nové projekty nebo pro použití nových funkcí byste měli přejít na [novou knihovnu](/dotnet/api/overview/azure/search.documents-readme).

## <a name="about-this-quickstart"></a>O tomto rychlém startu

Vytvořte konzolovou aplikaci .NET Core v jazyce C#, která vytvoří, načte a zadá dotaz na index služby Azure Kognitivní hledání pomocí sady Visual Studio a [Microsoft. Azure. Search Client](/dotnet/api/overview/azure/search/client10)Library. 

Tento článek vysvětluje, jak vytvořit aplikaci. Můžete si také [Stáhnout a spustit úplnou aplikaci](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart/v10).

> [!NOTE]
> Ukázkový kód v tomto článku používá pro jednoduchost synchronní metody Azure Kognitivní hledání verze 10 .NET SDK. U produkčních scénářů ale doporučujeme používat asynchronní metody ve vašich vlastních aplikacích, abyste je zachovali a mohli reagovat. Můžete například použít `CreateAsync` a `DeleteAsync` místo `Create` a `Delete` .

## <a name="prerequisites"></a>Předpoklady

Než začnete, musíte mít následující:

+ Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/).

+ Služba Azure Kognitivní hledání. [Vytvořte službu](search-create-service-portal.md) nebo [vyhledejte existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) v rámci aktuálního předplatného. Pro tento rychlý Start můžete použít bezplatnou službu. 

+ [Visual Studio](https://visualstudio.microsoft.com/downloads/), libovolná edice. Vzorový kód a pokyny byly testovány na bezplatnou edici Community.

<a name="get-service-info"></a>

## <a name="get-a-key-and-url"></a>Získat klíč a adresu URL

Volání služby vyžaduje koncový bod adresy URL a přístupový klíč pro každý požadavek. Vyhledávací služba se vytvoří s oběma, takže pokud jste do svého předplatného přidali Azure Kognitivní hledání, postupujte podle těchto kroků a získejte potřebné informace:

1. [Přihlaste se k Azure Portal](https://portal.azure.com/)a na stránce **Přehled** vyhledávací služby Získejte adresu URL. Příkladem koncového bodu může být `https://mydemo.search.windows.net`.

2. V části **Nastavení**  >  **klíče** Získejte klíč správce s úplnými právy k této službě. Existují dva zaměnitelné klíče správce poskytované pro zajištění kontinuity podnikových služeb pro případ, že byste museli nějakou dobu navrátit. V žádostech o přidání, úpravu a odstranění objektů můžete použít primární nebo sekundární klíč.

   Získejte taky klíč dotazu. Osvědčeným postupem je vystavovat požadavky na dotazy s přístupem jen pro čtení.

![Získání koncového bodu HTTP a přístupového klíče](media/search-get-started-rest/get-url-key.png "Získání koncového bodu HTTP a přístupového klíče")

Všechny požadavky vyžadují klíč rozhraní API na všech žádostech odeslaných službě. Platný klíč vytváří na základě žádosti vztah důvěryhodnosti mezi aplikací, která žádost odeslala, a službou, která ji zpracovává.

## <a name="set-up-your-environment"></a>Nastavení prostředí

Začněte otevřením sady Visual Studio a vytvořením nového projektu konzolové aplikace, který může běžet v .NET Core.

### <a name="install-nuget-packages"></a>Instalace balíčků NuGet

[Balíček Microsoft. Azure. Search](https://www.nuget.org/packages/Microsoft.Azure.Search/) se skládá z několika klientských knihoven, které jsou distribuované jako balíčky NuGet.

Pro tento projekt použijte verzi 10 `Microsoft.Azure.Search` balíčku NuGet a nejnovější `Microsoft.Extensions.Configuration.Json` balíček NuGet.

1. V **nabídce nástroje**  >  **Správce balíčků NuGet** vyberte **Spravovat balíčky NuGet pro řešení...**. 

1. Klikněte na **Browse** (Procházet).

1. Vyhledejte `Microsoft.Azure.Search` a vyberte verze 10.

1. Kliknutím na **instalovat** na pravé straně přidejte sestavení do projektu a řešení.

1. Opakujte pro `Microsoft.Extensions.Configuration.Json` , výběr verze 2.2.0 nebo novější.


### <a name="add-azure-cognitive-search-service-information"></a>Přidání informací o službě Azure Kognitivní hledání

1. V Průzkumník řešení klikněte pravým tlačítkem myši na projekt a vyberte možnost **Přidat**  >  **novou položku...** . 

1. V části Přidat novou položku vyhledejte "JSON" a vraťte seznam typů položek souvisejících s JSON.

1. Vyberte **soubor JSON**, pojmenujte soubor appsettings.json a klikněte na **Přidat**. 

1. Přidejte soubor do výstupního adresáře. Klikněte pravým tlačítkem na appsettings.jsa vyberte **vlastnosti**. V **adresáři kopírovat do výstupního adresáře** vyberte možnost **Kopírovat, pokud je novější**.

1. Zkopírujte následující kód JSON do nového souboru JSON. 

    ```json
    {
      "SearchServiceName": "<YOUR-SEARCH-SERVICE-NAME>",
      "SearchServiceAdminApiKey": "<YOUR-ADMIN-API-KEY>",
      "SearchIndexName": "hotels-quickstart"
    }
    ```

1. Nahraďte název vyhledávací služby (klíč-SEARCH-SERVICE-NAME) a klíč rozhraní API pro správu (kód-správce-rozhraní API-KEY) pomocí platných hodnot. Pokud je koncový bod služby `https://mydemo.search.windows.net` , název služby by byl " `mydemo` ".

### <a name="add-class-method-files-to-your-project"></a>Přidat třídu ". Metoda "soubory do projektu

Tento krok je nutný k vytváření smysluplného výstupu v konzole nástroje. Při tisku výsledků do okna konzoly musí být jednotlivá pole z objektu hotelu vrácena jako řetězce. Tento krok implementuje [metodu ToString ()](/dotnet/api/system.object.tostring) pro provedení této úlohy, kterou provedete tak, že zkopírujete potřebný kód do dvou nových souborů.

1. Přidejte do projektu dvě prázdné definice tříd: Address.Methods.cs, Hotel.Methods.cs

1. V Address.Methods.cs přepište výchozí obsah následujícím kódem, [řádky 1-25](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/quickstart/v10/AzureSearchQuickstart/Address.Methods.cs#L1-L25).

1. Na Hotel.Methods.cs zkopírujte [řádky 1-68](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/quickstart/v10/AzureSearchQuickstart/Hotel.Methods.cs#L1-L68).

## <a name="1---create-index"></a>1. vytvoření indexu

Index hotelů se skládá z jednoduchých a složitých polí, kde je jednoduché pole "hotelů" nebo "Description" a složitá pole jsou adresa s podpoli nebo kolekcí místností. Pokud index obsahuje komplexní typy, izolujte definice komplexních polí v samostatných třídách.

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

1. V Hotel.cs třída definuje celkovou strukturu indexu, včetně odkazů na třídu adres.

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

    Atributy pole určují, jak se používá v aplikaci. Například `IsSearchable` atribut musí být přiřazen každému poli, které by mělo být zahrnuto do fulltextového vyhledávání. 
    
    > [!NOTE]
    > V sadě .NET SDK musí být pole explicitně přidělena atributům [`IsSearchable`](/dotnet/api/microsoft.azure.search.models.field.issearchable) , [`IsFilterable`](/dotnet/api/microsoft.azure.search.models.field.isfilterable) , [`IsSortable`](/dotnet/api/microsoft.azure.search.models.field.issortable) a [`IsFacetable`](/dotnet/api/microsoft.azure.search.models.field.isfacetable) . Toto chování je v kontrastu s REST API, která implicitně povoluje připsat na základě datového typu (například pole jednoduchých řetězců jsou automaticky prohledávatelné).

    Právě jedno pole v indexu typu `string` musí být *klíčovým* polem a jednoznačně identifikovat každý dokument. V tomto schématu je klíč `HotelId` .

    V tomto indexu pole Popis používají volitelnou [`analyzer`](/dotnet/api/microsoft.azure.search.models.field.analyzer) vlastnost zadanou v případě, že chcete přepsat výchozí standardní analyzátor Lucene. `description_fr`Pole používá nástroj pro francouzštinu Lucene ([FrLucene](/dotnet/api/microsoft.azure.search.models.analyzername.frlucene)), protože ukládá francouzský text. Používá `description` nepovinný nástroj Microsoft Language Analyzer ([EnMicrosoft](/dotnet/api/microsoft.azure.search.models.analyzername.enmicrosoft)).

1. V Program.cs vytvořte instanci [`SearchServiceClient`](/dotnet/api/microsoft.azure.search.searchserviceclient) třídy pro připojení ke službě pomocí hodnot, které jsou uložené v konfiguračním souboru aplikace (appsettings.json). 

   `SearchServiceClient` má [`Indexes`](/dotnet/api/microsoft.azure.search.searchserviceclient.indexes) vlastnost, která poskytuje všechny metody, které potřebujete k vytvoření, výpisu, aktualizaci nebo odstranění indexů služby Azure kognitivní hledání. 

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
                var definition = new Microsoft.Azure.Search.Models.Index()
                {
                    Name = indexName,
                    Fields = FieldBuilder.BuildForType<Hotel>()
                };

                serviceClient.Indexes.Create(definition);
            }
        }
    }    
    ```

    Pokud je to možné, sdílejte jednu instanci `SearchServiceClient` v aplikaci, aby nedocházelo k otevření příliš velkého počtu připojení. Metody třídy jsou bezpečné pro přístup z více vláken umožňující takové sdílení.

   Třída má několik konstruktorů. Ten, který chcete, přijímá jako parametry název vaší vyhledávací služby a objekt `SearchCredentials`. `SearchCredentials` zabalí váš klíč api-key.

    Nejjednodušší způsob, jak vytvořit objekty, je v definici indexu `Field` voláním `FieldBuilder.BuildForType` metody, předáním třídy modelu pro parametr typu. Třída modelu obsahuje vlastnosti, které se mapují na pole vašeho indexu. Toto mapování umožňuje vytvořit vazby dokumentů z indexu vyhledávání do instancí třídy modelu.

    > [!NOTE]
    > Pokud nemáte v úmyslu používat třídu modelu, stále můžete definovat index přímým vytvořením objektů `Field`. Můžete zadat název pole do konstruktoru, společně s datovým typem (nebo analyzátor pro pole řetězce). Můžete také nastavit další vlastnosti, například `IsSearchable` , `IsFilterable` , k pojmenování několika.
    >

1. Stisknutím klávesy F5 sestavte aplikaci a vytvořte index. 

    Pokud se projekt úspěšně sestaví, otevře se okno konzoly, ve kterém se zapisuje stavové zprávy na obrazovku pro odstranění a vytvoření indexu. 

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2. načtení dokumentů

V Azure Kognitivní hledání jsou dokumenty datové struktury, které jsou ve dvou vstupech k indexování a výstupy z dotazů. V případě získání z externího zdroje dat můžou být vstupy dokumentů v databázi, objektech blob v úložišti objektů BLOB nebo v dokumentech JSON na disku. V tomto příkladu podáváme zástupce a vkládání dokumentů JSON pro čtyři hotely do samotného kódu. 

Při odesílání dokumentů je nutné použít [`IndexBatch`](/dotnet/api/microsoft.azure.search.models.indexbatch) objekt. `IndexBatch`Obsahuje kolekci [`IndexAction`](/dotnet/api/microsoft.azure.search.models.indexaction) objektů, z nichž každý obsahuje dokument a vlastnost sdělující Azure kognitivní hledání, jakou akci chcete provést ([nahrávání, sloučení, odstranění a mergeOrUpload](search-what-is-data-import.md#indexing-actions)).

1. V Program.cs vytvořte pole dokumentů a indexových akcí a pak předejte pole do `IndexBatch` . Níže uvedené dokumenty odpovídají indexu pro rychlé spuštění v hotelu, jak je definováno v rámci tříd hotelů a Address.

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

    Po inicializaci `IndexBatch` objektu jej můžete odeslat do indexu voláním [`Documents.Index`](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.index) [`SearchIndexClient`](/dotnet/api/microsoft.azure.search.searchindexclient) objektu. `Documents` je vlastnost `SearchIndexClient` , která poskytuje metody pro přidání, úpravu, odstranění nebo dotazování dokumentů v indexu.

    `try` / `catch` Okolní volání `Index` metody zachytí chyby indexování, ke kterým může dojít, pokud je vaše služba přetížená. V produkčním kódu můžete odložit a potom opakovat indexování dokumentů, které selhaly, nebo můžete protokolovat a pokračovat, jako je ukázka, nebo ho zpracovat jiným způsobem, který splňuje požadavky vaší aplikace na konzistenci dat.

    Zpoždění dvou sekund se kompenzuje při indexování, což je asynchronní, aby bylo možné všechny dokumenty před spuštěním dotazů indexovat. Kódování v zpoždění je obvykle nutné pouze v ukázkách, testech a ukázkových aplikacích.

1. V Program.cs v části Main Odkomentujte řádky pro "2-Load Documents". 

    ```csharp
    // Uncomment next 3 lines in "2 - Load documents"
    ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(indexClient);
    ```
1. Stisknutím klávesy F5 znovu sestavte aplikaci. 

    Pokud se projekt úspěšně sestaví, otevře se okno konzoly, ve kterém se zapisuje stavové zprávy, tentokrát se zprávou o nahrávání dokumentů. V Azure Portal na stránce **Přehled** služby vyhledávání by měl mít index pro rychlý začátek v hotely nyní 4 dokumenty.

Další informace o zpracování dokumentů naleznete v tématu ["jak sada .NET SDK zpracovává dokumenty"](search-howto-dotnet-sdk-v10.md#how-dotnet-handles-documents).

## <a name="3---search-an-index"></a>3. Prohledání indexu

Výsledky dotazu můžete získat ihned po indexování prvního dokumentu, ale skutečný test indexu by měl počkat, dokud nebudou všechny dokumenty indexovány. 

V této části se přidávají dvě části funkčnosti: logika dotazů a výsledky. Pro dotazy použijte [`Search`](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.search) metodu. Tato metoda přebírá hledaný text i další [parametry](/dotnet/api/microsoft.azure.search.models.searchparameters). 

[`DocumentsSearchResult`](/dotnet/api/microsoft.azure.search.models.documentsearchresult-1)Třída představuje výsledky.


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

1. Vytvořte metodu RunQueries, která spustí dotazy a vrátí výsledky. Výsledky jsou hotelové objekty. Pomocí parametru Select můžete Surface jednotlivých polí. Pokud pole není zahrnuto v parametru SELECT, jeho odpovídající vlastnost hotelu bude mít hodnotu null.

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

    Existují dva [způsoby, kterými se v dotazu shodují výrazy](search-query-overview.md#types-of-queries): fulltextové vyhledávání a filtry. Fulltextový vyhledávací dotaz vyhledává jeden nebo více podmínek v `IsSearchable` polích indexu. Filtr je logický výraz, který se vyhodnocuje přes `IsFilterable` pole v indexu. Můžete použít fulltextové vyhledávání a filtry společně nebo samostatně.

    Vyhledávání i filtrování se provádí pomocí metody `Documents.Search`. Vyhledávací dotaz lze předat v parametru `searchText`, zatímco výraz filtru lze předat ve vlastnosti `Filter` třídy `SearchParameters`. Chcete-li filtrovat bez vyhledávání, stačí předat `"*"` jako hodnotu parametru `searchText`. Chcete-li vyhledávat bez filtrování, ponechte vlastnost `Filter` nenastavenou nebo instanci `SearchParameters` vůbec nepředávejte.

1. V Program.cs v části Main Odkomentujte řádky pro "3-hledání". 

    ```csharp
    // Uncomment next 2 lines in "3 - Search an index"
    Console.WriteLine("{0}", "Searching documents...\n");
    RunQueries(indexClient);
    ```
1. Řešení je nyní dokončeno. Stisknutím klávesy F5 znovu sestavte aplikaci a spusťte program v celém rozsahu. 

    Výstup obsahuje stejné zprávy jako před a s přidáním informací a výsledků dotazu.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud pracujete s vlastním předplatným, je vhodné vždy na konci projektu zkontrolovat, jestli budete vytvořené prostředky ještě potřebovat. Prostředky, které necháte běžet, vás stojí peníze. Můžete odstraňovat prostředky jednotlivě nebo odstraněním skupiny prostředků odstranit celou sadu prostředků najednou.

Prostředky můžete najít a spravovat na portálu pomocí odkazu **všechny prostředky** nebo **skupiny prostředků** v levém navigačním podokně.

Pokud používáte bezplatnou službu, pamatujte na to, že jste omezeni na tři indexy, indexery a zdroje dat. Jednotlivé položky na portálu můžete odstranit, aby zůstaly pod limitem. 

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu v C# jste pracovali pomocí řady úkolů, abyste mohli vytvořit index, načíst ho s dokumenty a spouštět dotazy. V různých fázích jsme udělali zkratky, které zjednodušují kód pro čitelnost a porozumění. Pokud jste obeznámeni se základními pojmy, doporučujeme další článek pro zkoumání alternativních přístupů a konceptů, které budou prohloubit vaše znalosti. 

Vzorový kód a index jsou rozšířené verze tohoto. Další ukázka přidá kolekci místností, používá různé třídy a akce a podrobněji podíváme se na to, jak zpracování funguje.

> [!div class="nextstepaction"]
> [Vývoj v .NET](search-howto-dotnet-sdk.md)

Chcete optimalizovat a uložit své útraty do cloudu?

> [!div class="nextstepaction"]
> [Zahájení analýzy nákladů pomocí Cost Management](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)