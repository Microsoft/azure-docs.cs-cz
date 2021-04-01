---
title: 'Rychlý Start: vytvoření indexu vyhledávání v .NET'
titleSuffix: Azure Cognitive Search
description: V tomto rychlém startu v C# se dozvíte, jak vytvořit index, načíst data a spustit dotazy pomocí klientské knihovny Azure.Search.Documents.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 11/20/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: f0d912d5b14932c43d109f8f955d5f16381cf773
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98180094"
---
# <a name="quickstart-create-a-search-index-using-the-azuresearchdocuments-client-library"></a>Rychlý Start: vytvoření indexu vyhledávání pomocí klientské knihovny Azure.Search.Documents

Použijte novou [Azure.Search.Docknihovnu klienta uments (verze 11)](/dotnet/api/overview/azure/search.documents-readme) k vytvoření konzolové aplikace .NET Core v jazyce C#, která vytvoří, načte a dotazuje index vyhledávání.

[Zdrojový kód](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart/v11) si můžete stáhnout, abyste mohli začít s dokončeným projektem, nebo podle kroků v tomto článku vytvořit vlastní.

> [!NOTE]
> Hledáte starší verzi? Podívejte [se na téma Vytvoření indexu vyhledávání pomocí Microsoft. Azure. Search v10 za účelem](search-get-started-dotnet-v10.md) místo toho.

## <a name="prerequisites"></a>Požadavky

Než začnete, budete mít k dispozici následující nástroje a služby:

+ Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/).

+ Služba Azure Kognitivní hledání. [Vytvořte službu](search-create-service-portal.md) nebo [vyhledejte existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). Pro tento rychlý Start můžete použít bezplatnou službu. 

+ [Visual Studio](https://visualstudio.microsoft.com/downloads/), libovolná edice. Vzorový kód byl testován na bezplatnou edici Community sady Visual Studio 2019.

Při nastavování projektu budete stahovat [ balíček NuGetAzure.Search.Documents](https://www.nuget.org/packages/Azure.Search.Documents/).

Sada Azure SDK pro .NET odpovídá [.NET Standard 2,0](/dotnet/standard/net-standard#net-implementation-support), což znamená, že .NET Framework 4.6.1 a .net Core 2,0 jako minimální požadavky.

## <a name="set-up-your-project"></a>Nastavení projektu

Sestavte informace o připojení služby a potom spusťte Visual Studio a vytvořte nový projekt konzolové aplikace, který může běžet v .NET Core.

<a name="get-service-info"></a>

### <a name="copy-a-key-and-endpoint"></a>Zkopírování klíče a koncového bodu

Volání služby vyžaduje koncový bod adresy URL a přístupový klíč pro každý požadavek. Jako první krok najděte klíč rozhraní API a adresu URL, které chcete přidat do projektu. Při vytváření klienta v pozdějším kroku budete určovat obě hodnoty.

1. [Přihlaste se k Azure Portal](https://portal.azure.com/)a na stránce **Přehled** vyhledávací služby Získejte adresu URL. Příkladem koncového bodu může být `https://mydemo.search.windows.net`.

2. V části **Nastavení**  >  **klíče** Získejte klíč správce pro úplná práva ke službě, který je vyžadován při vytváření nebo odstraňování objektů. Existují dva zaměnitelné primární a sekundární klíče. Můžete použít jednu z těchto.

   ![Získání koncového bodu HTTP a přístupového klíče](media/search-get-started-rest/get-url-key.png "Získání koncového bodu HTTP a přístupového klíče")

Všechny požadavky vyžadují klíč rozhraní API na všech žádostech odeslaných službě. Platný klíč vytváří na základě žádosti vztah důvěryhodnosti mezi aplikací, která žádost odeslala, a službou, která ji zpracovává.

### <a name="install-the-nuget-package"></a>Instalace balíčku NuGet

Po vytvoření projektu přidejte knihovnu klienta. [BalíčekAzure.Search.Documents](https://www.nuget.org/packages/Azure.Search.Documents/) se skládá z jedné klientské knihovny, která poskytuje všechna rozhraní API používaná pro práci se službou vyhledávání v .NET.

1. Spusťte Visual Studio a vytvořte konzolovou aplikaci .NET Core.

1. V **nabídce nástroje**  >  **Správce balíčků NuGet** vyberte **Spravovat balíčky NuGet pro řešení...**. 

1. Klikněte na **Browse** (Procházet).

1. Vyhledejte `Azure.Search.Documents` a vyberte verze 11,0 nebo novější.

1. Kliknutím na **instalovat** na pravé straně přidejte sestavení do projektu a řešení.

### <a name="create-a-search-client"></a>Vytvoření vyhledávacího klienta

1. V **programu program. cs** změňte obor názvů na `AzureSearch.SDK.Quickstart.v11` a pak přidejte následující `using` direktivy.

   ```csharp
   using Azure;
   using Azure.Search.Documents;
   using Azure.Search.Documents.Indexes;
   using Azure.Search.Documents.Indexes.Models;
   using Azure.Search.Documents.Models;
   ```

1. Vytvoření dvou klientů: [SearchIndexClient](/dotnet/api/azure.search.documents.indexes.searchindexclient) vytvoří index a [SearchClient](/dotnet/api/azure.search.documents.searchclient) načte a dotazuje existující index. Oba potřebují koncový bod služby a klíč rozhraní API Správce pro ověřování pomocí práv pro vytvoření/odstranění.

   ```csharp
   static void Main(string[] args)
   {
       string serviceName = "<YOUR-SERVICE-NAME>";
       string indexName = "hotels-quickstart";
       string apiKey = "<YOUR-ADMIN-API-KEY>";

        // Create a SearchIndexClient to send create/delete index commands
        Uri serviceEndpoint = new Uri($"https://{serviceName}.search.windows.net/");
        AzureKeyCredential credential = new AzureKeyCredential(apiKey);
        SearchIndexClient adminClient = new SearchIndexClient(serviceEndpoint, credential);

        // Create a SearchClient to load and query documents
        SearchClient srchclient = new SearchClient(serviceEndpoint, indexName, credential);
    ```

## <a name="1---create-an-index"></a>1. Vytvoření indexu

V tomto rychlém startu se vytvoří index hotelů, který nahrajete s daty hotelu a spustíte dotazy. V tomto kroku definujete pole v indexu. Každá definice pole obsahuje název, datový typ a atributy, které určují, jak se pole používá.

V tomto příkladu se synchronní metody knihovny Azure.Search.Documents používají pro jednoduchost a čitelnost. Nicméně v produkčních scénářích byste měli použít asynchronní metody, abyste zachovali škálovatelnou a odezvu vaší aplikace. Například byste použili [CreateIndexAsync](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindexasync) namísto [CreateIndex](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindex).

1. Přidejte do projektu prázdnou definici třídy: **hotelu. cs**

1. Zkopírujte následující kód do souboru **hotelu. cs** a definujte strukturu dokumentu hotelu. Atributy pole určují, jak se používá v aplikaci. Například `IsFilterable` atribut musí být přiřazen každému poli, které podporuje výraz filtru.

    ```csharp
    using System;
    using System.Text.Json.Serialization;
    using Azure.Search.Documents.Indexes;
    using Azure.Search.Documents.Indexes.Models;

    namespace AzureSearch.Quickstart
    {
        public partial class Hotel
        {
            [SimpleField(IsKey = true, IsFilterable = true)]
            public string HotelId { get; set; }

            [SearchableField(IsSortable = true)]
            public string HotelName { get; set; }

            [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.EnLucene)]
            public string Description { get; set; }

            [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.FrLucene)]
            [JsonPropertyName("Description_fr")]
            public string DescriptionFr { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string Category { get; set; }

            [SearchableField(IsFilterable = true, IsFacetable = true)]
            public string[] Tags { get; set; }

            [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public bool? ParkingIncluded { get; set; }

            [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public DateTimeOffset? LastRenovationDate { get; set; }

            [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public double? Rating { get; set; }

            [SearchableField]
            public Address Address { get; set; }
        }
    }
    ```

   V klientské knihovně Azure.Search.Documents můžete použít [SearchableField](/dotnet/api/azure.search.documents.indexes.models.searchablefield) a [SimpleField](/dotnet/api/azure.search.documents.indexes.models.simplefield) pro zjednodušení definic polí. Obě jsou odvozené z [seachfield](/dotnet/api/azure.search.documents.indexes.models.searchfield) a mohou potenciálně zjednodušit váš kód:

   + `SimpleField` může se jednat o libovolný datový typ, je vždycky neprohledávatelné (pro fulltextové vyhledávací dotazy se ignoruje) a dá se získat (není skrytý). Ostatní atributy jsou ve výchozím nastavení vypnuté, ale dají se povolit. Můžete použít `SimpleField` pro ID dokumentů nebo pole, která se používají jenom v filtrech, omezujících objektech nebo profilech vyhodnocování. Pokud ano, nezapomeňte použít všechny atributy, které jsou nezbytné pro daný scénář, například `IsKey = true` pro ID dokumentu. Další informace naleznete v tématu [SimpleFieldAttribute. cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/src/Indexes/SimpleFieldAttribute.cs) ve zdrojovém kódu.

   + `SearchableField` musí to být řetězec, který je vždycky prohledávatelný a dá se získat. Ostatní atributy jsou ve výchozím nastavení vypnuté, ale dají se povolit. Vzhledem k tomu, že tento typ pole lze prohledávat, podporuje synonyma a úplný doplněk vlastností analyzátoru. Další informace naleznete v tématu [SearchableFieldAttribute. cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/src/Indexes/SearchableFieldAttribute.cs) ve zdrojovém kódu.

   Bez ohledu na to, jestli používáte základní `SearchField` rozhraní API nebo některý z pomocných modelů, musíte explicitně povolit atributy Filter, Face a Sort. Například může být [nefiltrovaná](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable), [nesetříditelné](/dotnet/api/azure.search.documents.indexes.models.searchfield.issortable)a vlastnost- [Face](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfacetable) , jak je znázorněno v ukázce výše. 

1. Přidejte do projektu druhou prázdnou definici třídy: **Address. cs**.  Zkopírujte následující kód do třídy.

   ```csharp
   using Azure.Search.Documents.Indexes;

    namespace AzureSearch.Quickstart
    {
        public partial class Address
        {
            [SearchableField(IsFilterable = true)]
            public string StreetAddress { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string City { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string StateProvince { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string PostalCode { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string Country { get; set; }
        }
    }
   ```

1. Vytvořte dvě další třídy: **hotelu. Methods. cs** a **Address. Methods. cs** pro přepsání metody ToString (). Tyto třídy slouží k vykreslení výsledků hledání ve výstupu konzoly.  Obsah těchto tříd není uveden v tomto článku, ale můžete zkopírovat kód ze [souborů v GitHubu](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart/v11/AzureSearchQuickstart-v11).

1. V **programu program. cs** vytvořte objekt [SearchIndex](/dotnet/api/azure.search.documents.indexes.models.searchindex) a zavolejte metodu [CreateIndex](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindex) pro vyjádření indexu ve vyhledávací službě. Index obsahuje také [SearchSuggester](/dotnet/api/azure.search.documents.indexes.models.searchsuggester) , který umožňuje automatické dokončování pro zadaná pole.

   ```csharp
    // Create hotels-quickstart index
    private static void CreateIndex(string indexName, SearchIndexClient adminClient)
    {
        FieldBuilder fieldBuilder = new FieldBuilder();
        var searchFields = fieldBuilder.Build(typeof(Hotel));

        var definition = new SearchIndex(indexName, searchFields);

        var suggester = new SearchSuggester("sg", new[] { "HotelName", "Category", "Address/City", "Address/StateProvince" });
        definition.Suggesters.Add(suggester);

        adminClient.CreateOrUpdateIndex(definition);
    }
   ```

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2. načtení dokumentů

Služba Azure Kognitivní hledání prohledává obsah uložený ve službě. V tomto kroku načtete dokumenty JSON, které odpovídají indexu hotelu, který jste právě vytvořili.

Ve službě Azure Kognitivní hledání vyhledávání dokumentů jsou datové struktury, které jsou ve dvou vstupech k indexování a výstupy z dotazů. V případě získání z externího zdroje dat můžou být vstupy dokumentů v databázi, objektech blob v úložišti objektů BLOB nebo v dokumentech JSON na disku. V tomto příkladu podáváme zástupce a vkládání dokumentů JSON pro čtyři hotely do samotného kódu. 

Při odesílání dokumentů je nutné použít objekt [IndexDocumentsBatch](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1) . `IndexDocumentsBatch`Objekt obsahuje kolekci [akcí](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1.actions), z nichž každá obsahuje dokument a vlastnost oznamuje službě Azure kognitivní hledání, jakou akci chcete provést ([nahrávání, sloučení, odstranění a mergeOrUpload](search-what-is-data-import.md#indexing-actions)).

1. V **programu program. cs** vytvořte pole dokumentů a indexových akcí a pak předejte pole do `IndexDocumentsBatch` . Následující dokumenty odpovídají indexu pro rychlé spuštění v hotelů, jak je definováno třídou hotelu.

    ```csharp
    // Upload documents in a single Upload request.
    private static void UploadDocuments(SearchClient searchClient)
    {
        IndexDocumentsBatch<Hotel> batch = IndexDocumentsBatch.Create(
            IndexDocumentsAction.Upload(
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
                }),
            IndexDocumentsAction.Upload(
                new Hotel()
                {
                    HotelId = "2",
                    HotelName = "Twin Dome Motel",
                    Description = "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
                    DescriptionFr = "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
                    Category = "Boutique",
                    Tags = new[] { "pool", "free wifi", "concierge" },
                    ParkingIncluded = false,
                    LastRenovationDate = new DateTimeOffset(1979, 2, 18, 0, 0, 0, TimeSpan.Zero),
                    Rating = 3.60,
                    Address = new Address()
                    {
                        StreetAddress = "140 University Town Center Dr",
                        City = "Sarasota",
                        StateProvince = "FL",
                        PostalCode = "34243",
                        Country = "USA"
                    }
                }),
            IndexDocumentsAction.Upload(
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
                }),
            IndexDocumentsAction.Upload(
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
                    Rating = 4.60,
                    Address = new Address()
                    {
                        StreetAddress = "7400 San Pedro Ave",
                        City = "San Antonio",
                        StateProvince = "TX",
                        PostalCode = "78216",
                        Country = "USA"
                    }
                })
            );

        try
        {
            IndexDocumentsResult result = searchClient.IndexDocuments(batch);
        }
        catch (Exception)
        {
            // If for some reason any documents are dropped during indexing, you can compensate by delaying and
            // retrying. This simple demo just logs the failed document keys and continues.
            Console.WriteLine("Failed to index some of the documents: {0}");
        }
    }
    ```

    Po inicializaci objektu [IndexDocumentsBatch](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1) jej můžete odeslat do indexu voláním [IndexDocuments](/dotnet/api/azure.search.documents.searchclient.indexdocuments) na objekt [SearchClient](/dotnet/api/azure.search.documents.searchclient) .

1. Přidejte následující řádky do Main (). Načítání dokumentů se provádí pomocí SearchClient, ale tato operace také vyžaduje oprávnění správce služby, která je obvykle přidružená k SearchIndexClient. Jedním ze způsobů, jak tuto operaci nastavit, je získat SearchClient prostřednictvím SearchIndexClient (adminClient v tomto příkladu).

   ```csharp
    SearchClient ingesterClient = adminClient.GetSearchClient(indexName);

    // Load documents
    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(ingesterClient);
   ```

1. Vzhledem k tomu, že se jedná o konzolovou aplikaci, která postupně spouští všechny příkazy, přidejte mezi indexováním a dotazy 2 sekundy čekací dobu.

    ```csharp
    // Wait 2 seconds for indexing to complete before starting queries (for demo and console-app purposes only)
    Console.WriteLine("Waiting for indexing...\n");
    System.Threading.Thread.Sleep(2000);
    ```

    Zpoždění dvou sekund se kompenzuje při indexování, což je asynchronní, aby bylo možné všechny dokumenty před spuštěním dotazů indexovat. Kódování v zpoždění je obvykle nutné pouze v ukázkách, testech a ukázkových aplikacích.

## <a name="3---search-an-index"></a>3. Prohledání indexu

Výsledky dotazu můžete získat ihned po indexování prvního dokumentu, ale skutečný test indexu by měl počkat, dokud nebudou všechny dokumenty indexovány.

V této části se přidávají dvě části funkčnosti: logika dotazů a výsledky. Pro dotazy použijte metodu [Search](/dotnet/api/azure.search.documents.searchclient.search) . Tato metoda přebírá hledaný text (řetězec dotazu) i další [Možnosti](/dotnet/api/azure.search.documents.searchoptions).

Třída [searchResults](/dotnet/api/azure.search.documents.models.searchresults-1) představuje výsledky.

1. V **programu program. cs** vytvořte metodu **WriteDocuments** , která vytiskne výsledky hledání do konzoly.

    ```csharp
    // Write search results to console
    private static void WriteDocuments(SearchResults<Hotel> searchResults)
    {
        foreach (SearchResult<Hotel> result in searchResults.GetResults())
        {
            Console.WriteLine(result.Document);
        }

        Console.WriteLine();
    }
    ```

1. Vytvořte metodu **RunQueries** , která spustí dotazy a vrátí výsledky. Výsledky jsou hotelové objekty. V této ukázce se zobrazuje signatura metody a první dotaz. Tento dotaz znázorňuje parametr SELECT, který umožňuje vytvořit výsledek pomocí vybraných polí z dokumentu.

    ```csharp
    // Run queries, use WriteDocuments to print output
    private static void RunQueries(SearchClient srchclient)
    {
        SearchOptions options;
        SearchResults<Hotel> response;

        Console.WriteLine("Query #1: Search on empty term '*' to return all documents, showing a subset of fields...\n");

        options = new SearchOptions()
        {
            IncludeTotalCount = true,
            Filter = "",
            OrderBy = { "" }
        };

        options.Select.Add("HotelId");
        options.Select.Add("HotelName");
        options.Select.Add("Address/City");

        response = srchclient.Search<Hotel>("*", options);
        WriteDocuments(response);
    ```

1. Ve druhém dotazu vyhledejte termín a přidejte filtr, který vybere dokumenty, kde hodnocení je větší než 4, a pak seřaďte podle hodnocení v sestupném pořadí. Filter je logický výraz, který se vyhodnocuje [přes pole](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable) s hodnotou v indexu. Filtrovat dotazy buď do zahrnutí, nebo z vyloučení hodnot. V takovém případě není k dotazu filtru přidruženo žádné skóre relevance. 

    ```csharp
    Console.WriteLine("Query #2: Search on 'hotels', filter on 'Rating gt 4', sort by Rating in descending order...\n");

    options = new SearchOptions()
    {
        Filter = "Rating gt 4",
        OrderBy = { "Rating desc" }
    };

    options.Select.Add("HotelId");
    options.Select.Add("HotelName");
    options.Select.Add("Rating");

    response = srchclient.Search<Hotel>("hotels", options);
    WriteDocuments(response);
    ```

1. Třetí dotaz znázorňuje searchFields, který se používá k určení rozsahu fulltextového vyhledávání na konkrétní pole.

    ```csharp
    Console.WriteLine("Query #3: Limit search to specific fields (pool in Tags field)...\n");

    options = new SearchOptions()
    {
        SearchFields = { "Tags" }
    };

    options.Select.Add("HotelId");
    options.Select.Add("HotelName");
    options.Select.Add("Tags");

    response = srchclient.Search<Hotel>("pool", options);
    WriteDocuments(response);
    ```

1. Čtvrtý dotaz znázorňuje charakteristiky, které lze použít ke strukturování omezující navigační struktury. 

   ```csharp
    Console.WriteLine("Query #4: Facet on 'Category'...\n");

    options = new SearchOptions()
    {
        Filter = ""
    };

    options.Facets.Add("Category");

    options.Select.Add("HotelId");
    options.Select.Add("HotelName");
    options.Select.Add("Category");

    response = srchclient.Search<Hotel>("*", options);
    WriteDocuments(response);
   ```

1. V pátém dotazu vrátí konkrétní dokument. Vyhledávání dokumentů je typickou odezvou na událost události Click v sadě výsledků dotazu.

   ```csharp
    Console.WriteLine("Query #5: Look up a specific document...\n");

    Response<Hotel> lookupResponse;
    lookupResponse = srchclient.GetDocument<Hotel>("3");

    Console.WriteLine(lookupResponse.Value.HotelId);
   ```

1. Poslední dotaz zobrazuje syntaxi automatického dokončování, simulující částečný vstup uživatele "sa", který se překládá na dvě možné shody v sourceFields přidružené k nástroji pro návrh, který jste definovali v indexu.

   ```csharp
    Console.WriteLine("Query #6: Call Autocomplete on HotelName that starts with 'sa'...\n");

    var autoresponse = srchclient.Autocomplete("sa", "sg");
    WriteDocuments(autoresponse);
   ```

1. Přidejte **RunQueries** do Main ().

    ```csharp
    // Call the RunQueries method to invoke a series of queries
    Console.WriteLine("Starting queries...\n");
    RunQueries(srchclient);

    // End the program
    Console.WriteLine("{0}", "Complete. Press any key to end this program...\n");
    Console.ReadKey();
    ```

Předchozí dotazy zobrazují [v dotazu několik způsobů, jak se shodují výrazy](search-query-overview.md#types-of-queries): fulltextové vyhledávání, filtry a automatické dokončování.

Fulltextové vyhledávání a filtry se provádí pomocí metody [SearchClient. Search](/dotnet/api/azure.search.documents.searchclient.search) . Vyhledávací dotaz může být předán v `searchText` řetězci, zatímco výraz filtru lze předat do vlastnosti [Filter](/dotnet/api/azure.search.documents.searchoptions.filter) třídy [příznacích searchOptions jsou](/dotnet/api/azure.search.documents.searchoptions) . Chcete-li filtrovat bez vyhledávání, stačí předat `"*"` `searchText` parametr metody [hledání](/dotnet/api/azure.search.documents.searchclient.search) . Pokud chcete vyhledávat bez filtrování, ponechte `Filter` vlastnost nastavenou na nenastavenou hodnotu, nebo nepředávejte `SearchOptions` vůbec instanci.

## <a name="run-the-program"></a>Spuštění programu

Stisknutím klávesy F5 znovu sestavte aplikaci a spusťte program v celém rozsahu. 

Výstup obsahuje zprávy ze [Console. WriteLine](/dotnet/api/system.console.writeline)s přidáním informací a výsledků dotazu.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud pracujete s vlastním předplatným, je vhodné vždy na konci projektu zkontrolovat, jestli budete vytvořené prostředky ještě potřebovat. Prostředky, které necháte běžet, vás stojí peníze. Můžete odstraňovat prostředky jednotlivě nebo odstraněním skupiny prostředků odstranit celou sadu prostředků najednou.

Prostředky můžete najít a spravovat na portálu pomocí odkazu **všechny prostředky** nebo **skupiny prostředků** v levém navigačním podokně.

Pokud používáte bezplatnou službu, pamatujte na to, že jste omezeni na tři indexy, indexery a zdroje dat. Jednotlivé položky na portálu můžete odstranit, aby zůstaly pod limitem. 

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu v C# jste pracovali pomocí sady úloh pro vytvoření indexu, načetli ho do dokumentů a spustíte dotazy. V různých fázích jsme udělali zkratky, které zjednodušují kód pro čitelnost a porozumění. Pokud jste obeznámeni se základními pojmy, doporučujeme další článek pro zkoumání alternativních přístupů a konceptů, které budou prohloubit vaše znalosti. 

> [!div class="nextstepaction"]
> [Vývoj v .NET](search-howto-dotnet-sdk.md)

Chcete optimalizovat a uložit své útraty do cloudu?

> [!div class="nextstepaction"]
> [Zahájení analýzy nákladů pomocí Cost Management](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
