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
ms.date: 08/05/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: e1a52a15012e367dc902992f7f7b905fc6c6a5eb
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91541539"
---
# <a name="quickstart-create-a-search-index-using-the-azuresearchdocuments-client-library"></a>Rychlý Start: vytvoření indexu vyhledávání pomocí klientské knihovny Azure.Search.Documents

Použijte novou [Azure.Search.Docknihovnu klienta uments (verze 11)](/dotnet/api/overview/azure/search.documents-readme) k vytvoření konzolové aplikace .NET Core v jazyce C#, která vytvoří, načte a dotazuje index vyhledávání.

[Stáhněte si zdrojový kód](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart/v11) a začněte s dokončeným projektem nebo si vytvořte vlastní postup podle kroků v tomto článku.

> [!NOTE]
> Hledáte starší verzi? Podívejte [se na téma Vytvoření indexu vyhledávání pomocí Microsoft. Azure. Search v10 za účelem](search-get-started-dotnet-v10.md) místo toho.

## <a name="prerequisites"></a>Předpoklady

Než začnete, budete mít k dispozici následující nástroje a služby:

+ Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/).

+ Služba Azure Kognitivní hledání. [Vytvořte službu](search-create-service-portal.md) nebo [vyhledejte existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). Pro tento rychlý Start můžete použít bezplatnou službu. 

+ [Visual Studio](https://visualstudio.microsoft.com/downloads/), libovolná edice. Vzorový kód byl testován na bezplatnou edici Community sady Visual Studio 2019.

<a name="get-service-info"></a>

## <a name="get-a-key-and-endpoint"></a>Získání klíče a koncového bodu

Volání služby vyžaduje koncový bod adresy URL a přístupový klíč pro každý požadavek. Vyhledávací služba se vytvoří s oběma, takže pokud jste do svého předplatného přidali Azure Kognitivní hledání, postupujte podle těchto kroků a získejte potřebné informace:

1. [Přihlaste se k Azure Portal](https://portal.azure.com/)a na stránce **Přehled** vyhledávací služby Získejte adresu URL. Příkladem koncového bodu může být `https://mydemo.search.windows.net`.

2. V části **Nastavení**  >  **klíče**Získejte klíč správce pro úplná práva ke službě, který je vyžadován při vytváření nebo odstraňování objektů. Existují dva zaměnitelné primární a sekundární klíče. Můžete použít jednu z těchto.

   ![Získání koncového bodu HTTP a přístupového klíče](media/search-get-started-postman/get-url-key.png "Získání koncového bodu HTTP a přístupového klíče")

Všechny požadavky vyžadují klíč rozhraní API na všech žádostech odeslaných službě. Platný klíč vytváří na základě žádosti vztah důvěryhodnosti mezi aplikací, která žádost odeslala, a službou, která ji zpracovává.

## <a name="set-up-your-project"></a>Nastavení projektu

Spusťte Visual Studio a vytvořte nový projekt konzolové aplikace, který můžete spustit na .NET Core. 

### <a name="install-the-nuget-package"></a>Instalace balíčku NuGet

Po vytvoření projektu přidejte knihovnu klienta. [BalíčekAzure.Search.Documents](https://www.nuget.org/packages/Azure.Search.Documents/) se skládá z jedné klientské knihovny, která poskytuje všechna rozhraní API používaná pro práci se službou vyhledávání v .NET.

1. V **nabídce nástroje**  >  **Správce balíčků NuGet**vyberte **Spravovat balíčky NuGet pro řešení...**. 

1. Klikněte na **Browse** (Procházet).

1. Vyhledejte `Azure.Search.Documents` a vyberte 11.0.0 verze.

1. Kliknutím na **instalovat** na pravé straně přidejte sestavení do projektu a řešení.

### <a name="create-a-search-client"></a>Vytvoření vyhledávacího klienta

1. V **program.cs**změňte obor názvů na `AzureSearch.SDK.Quickstart.v11` a pak přidejte následující `using` direktivy.

   ```csharp
   using Azure;
   using Azure.Search.Documents;
   using Azure.Search.Documents.Indexes;
   using Azure.Search.Documents.Indexes.Models;
   using Azure.Search.Documents.Models;
   ```

1. Vytvoření dvou klientů: [SearchIndexClient](/dotnet/api/azure.search.documents.indexes.searchindexclient) vytvoří index a [SearchClient](/dotnet/api/azure.search.documents.searchclient) funguje s existujícím indexem. Oba potřebují koncový bod služby a klíč rozhraní API Správce pro ověřování pomocí práv pro vytvoření/odstranění.

   ```csharp
   static void Main(string[] args)
   {
       string serviceName = "<YOUR-SERVICE-NAME>";
       string indexName = "hotels-quickstart-v11";
       string apiKey = "<YOUR-ADMIN-API-KEY>";

       // Create a SearchIndexClient to send create/delete index commands
       Uri serviceEndpoint = new Uri($"https://{serviceName}.search.windows.net/");
       AzureKeyCredential credential = new AzureKeyCredential(apiKey);
       SearchIndexClient idxclient = new SearchIndexClient(serviceEndpoint, credential);

       // Create a SearchClient to load and query documents
       SearchClient qryclient = new SearchClient(serviceEndpoint, indexName, credential);
    ```

## <a name="1---create-an-index"></a>1. Vytvoření indexu

V tomto rychlém startu se vytvoří index hotelů, který načtete pomocí dat o hotelu a spustíte dotazy na. V tomto kroku definujete pole v indexu. Každá definice pole obsahuje název, datový typ a atributy, které určují, jak se pole používá.

V tomto příkladu se synchronní metody knihovny Azure.Search.Documents používají pro jednoduchost a čitelnost. Nicméně v produkčních scénářích byste měli použít asynchronní metody, abyste zachovali škálovatelnou a odezvu vaší aplikace. Například byste použili [CreateIndexAsync](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindexasync) namísto [CreateIndex](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindex).

1. Přidejte do projektu prázdnou definici třídy: **Hotel.cs**

1. V **Hotel.cs**definujte strukturu dokumentu hotelu.

    ```csharp
    using System;
    using System.Text.Json.Serialization;

    namespace AzureSearch.SDK.Quickstart.v11
    {
        public class Hotel
        {
            [JsonPropertyName("hotelId")]
            public string Id { get; set; }

            [JsonPropertyName("hotelName")]
            public string Name { get; set; }

            [JsonPropertyName("hotelCategory")]
            public string Category { get; set; }

            [JsonPropertyName("baseRate")]
            public Int32 Rate { get; set; }

            [JsonPropertyName("lastRenovationDate")]
            public DateTime Updated { get; set; }
        }
    }
    ```

1. V **program.cs**zadejte pole a atributy. [SearchIndex](/dotnet/api/azure.search.documents.indexes.models.searchindex) a [CreateIndex](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindex) se používají k vytvoření indexu.

   ```csharp
    // Define an index schema using SearchIndex
    // Create the index using SearchIndexClient
    SearchIndex index = new SearchIndex(indexName)
    {
        Fields =
            {
                new SimpleField("hotelId", SearchFieldDataType.String) { IsKey = true, IsFilterable = true, IsSortable = true },
                new SearchableField("hotelName") { IsFilterable = true, IsSortable = true },
                new SearchableField("hotelCategory") { IsFilterable = true, IsSortable = true },
                new SimpleField("baseRate", SearchFieldDataType.Int32) { IsFilterable = true, IsSortable = true },
                new SimpleField("lastRenovationDate", SearchFieldDataType.DateTimeOffset) { IsFilterable = true, IsSortable = true }
            }
    };

    Console.WriteLine("{0}", "Creating index...\n");
    idxclient.CreateIndex(index);
   ```

Atributy pole určují, jak se používá v aplikaci. Například `IsFilterable` atribut musí být přiřazen každému poli, které podporuje výraz filtru.

Na rozdíl od předchozích verzí sady .NET SDK, které vyžadují [vyhledávání](/dotnet/api/microsoft.azure.search.models.field.issearchable) v polích řetězců, které je možné prohledávat, můžete pomocí [SearchableField](/dotnet/api/azure.search.documents.indexes.models.searchablefield) a [SimpleField](/dotnet/api/azure.search.documents.indexes.models.simplefield) zjednodušit definice polí.

Podobně jako u předchozích verzí jsou v samotné definici stále vyžadovány další atributy. Například může být [nefiltrovaná](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable), [nesetříditelné](/dotnet/api/azure.search.documents.indexes.models.searchfield.issortable)a vlastnost- [Face](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfacetable) , jak je znázorněno v ukázce výše. 

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2. načtení dokumentů

Služba Azure Kognitivní hledání prohledává obsah uložený ve službě. V tomto kroku načtete dokumenty JSON, které odpovídají indexu hotelu, který jste právě vytvořili.

V Azure Kognitivní hledání jsou dokumenty datové struktury, které jsou ve dvou vstupech k indexování a výstupy z dotazů. V případě získání z externího zdroje dat můžou být vstupy dokumentů v databázi, objektech blob v úložišti objektů BLOB nebo v dokumentech JSON na disku. V tomto příkladu pořizujeme zástupce a vkládání dokumentů JSON pro pět hotelů v samotném kódu. 

Při odesílání dokumentů je nutné použít objekt [IndexDocumentsBatch](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1) . IndexDocumentsBatch obsahuje kolekci [akcí](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1.actions), z nichž každý obsahuje dokument a vlastnost, která oznamuje službě Azure kognitivní hledání, jakou akci chcete provést ([nahrávání, sloučení, odstranění a mergeOrUpload](search-what-is-data-import.md#indexing-actions)).

1. V **program.cs**vytvořte pole dokumentů a indexů a pak předejte pole do `ndexDocumentsBatch` níže uvedených dokumentů, které odpovídají indexu hotelů-Start-V11, jak je definováno třídou hotelu.

    ```csharp
    // Load documents (using a subset of fields for brevity)
    IndexDocumentsBatch<Hotel> batch = IndexDocumentsBatch.Create(
        IndexDocumentsAction.Upload(new Hotel { Id = "78", Name = "Upload Inn", Category = "hotel", Rate = 279, Updated = new DateTime(2018, 3, 1, 7, 0, 0) }),
        IndexDocumentsAction.Upload(new Hotel { Id = "54", Name = "Breakpoint by the Sea", Category = "motel", Rate = 162, Updated = new DateTime(2015, 9, 12, 7, 0, 0) }),
        IndexDocumentsAction.Upload(new Hotel { Id = "39", Name = "Debug Motel", Category = "motel", Rate = 159, Updated = new DateTime(2016, 11, 11, 7, 0, 0) }),
        IndexDocumentsAction.Upload(new Hotel { Id = "48", Name = "NuGet Hotel", Category = "hotel", Rate = 238, Updated = new DateTime(2016, 5, 30, 7, 0, 0) }),
        IndexDocumentsAction.Upload(new Hotel { Id = "12", Name = "Renovated Ranch", Category = "motel", Rate = 149, Updated = new DateTime(2020, 1, 24, 7, 0, 0) }));

    IndexDocumentsOptions idxoptions = new IndexDocumentsOptions { ThrowOnAnyError = true };

    Console.WriteLine("{0}", "Loading index...\n");
    qryclient.IndexDocuments(batch, idxoptions);
    ```

    Po inicializaci objektu [IndexDocumentsBatch](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1) jej můžete odeslat do indexu voláním [IndexDocuments](/dotnet/api/azure.search.documents.searchclient.indexdocuments) na objekt [SearchClient](/dotnet/api/azure.search.documents.searchclient) .

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

1. V **program.cs**vytvořte metodu WriteDocuments, která vytiskne výsledky hledání do konzoly.

    ```csharp
    private static void WriteDocuments(SearchResults<Hotel> searchResults)
    {
        foreach (SearchResult<Hotel> response in searchResults.GetResults())
        {
            Hotel doc = response.Document;
            var score = response.Score;
            Console.WriteLine($"Name: {doc.Name}, Type: {doc.Category}, Rate: {doc.Rate}, Last-update: {doc.Updated}, Score: {score}");
        }

        Console.WriteLine();
    }
    ```

1. Vytvořte metodu RunQueries, která spustí dotazy a vrátí výsledky. Výsledky jsou hotelové objekty.

    ```csharp
    private static void RunQueries(SearchClient qryclient)
    {
        SearchOptions options;
        SearchResults<Hotel> response;

        Console.WriteLine("Query #1: Search on the term 'motel' and list the relevance score for each match...\n");

        options = new SearchOptions()
        {
            Filter = "",
            OrderBy = { "" }
        };

        response = qryclient.Search<Hotel>("motel", options);
        WriteDocuments(response);

        Console.WriteLine("Query #2: Find hotels where 'type' equals hotel...\n");

        options = new SearchOptions()
        {
            Filter = "hotelCategory eq 'hotel'",
        };

        response = qryclient.Search<Hotel>("*", options);
        WriteDocuments(response);

        Console.WriteLine("Query #3: Filter on rates less than $200 and sort by when the hotel was last updated...\n");

        options = new SearchOptions()
        {
            Filter = "baseRate lt 200",
            OrderBy = { "lastRenovationDate desc" }
        };

        response = qryclient.Search<Hotel>("*", options);
        WriteDocuments(response);
    }
    ```

Tento příklad ukazuje dva [způsoby porovnání podmínek v dotazu](search-query-overview.md#types-of-queries): fulltextové vyhledávání a filtry:

+ Dotazy fulltextového vyhledávání pro jednu nebo více podmínek v rámci vyhledávacích polí v indexu. Prvním dotazem je úplné vyhledávání textu. Fulltextové vyhledávání vytváří skóre, které se používá k řazení výsledků.

+ Filter je logický výraz, který se vyhodnocuje [přes pole](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable) s hodnotou v indexu. Filtrovat dotazy buď do zahrnutí, nebo z vyloučení hodnot. V takovém případě není k dotazu filtru přidruženo žádné skóre relevance. Poslední dva dotazy ukazují hledání filtru.

Můžete použít fulltextové vyhledávání a filtry společně nebo samostatně.

Hledání i filtry se provádějí pomocí metody [SearchClient. Search](/dotnet/api/azure.search.documents.searchclient.search) . Vyhledávací dotaz může být předán v `searchText` řetězci, zatímco výraz filtru lze předat do vlastnosti [Filter](/dotnet/api/azure.search.documents.searchoptions.filter) třídy [příznacích searchOptions jsou](/dotnet/api/azure.search.documents.searchoptions) . Chcete-li filtrovat bez vyhledávání, stačí předat `"*"` `searchText` parametr metody [hledání](/dotnet/api/azure.search.documents.searchclient.search) . Pokud chcete vyhledávat bez filtrování, ponechte `Filter` vlastnost nastavenou na nenastavenou hodnotu, nebo nepředávejte `SearchOptions` vůbec instanci.

## <a name="run-the-program"></a>Spuštění programu

Stisknutím klávesy F5 znovu sestavte aplikaci a spusťte program v celém rozsahu. 

Výstup obsahuje zprávy ze [Console. WriteLIne](/dotnet/api/system.console.writeline)s přidáním informací a výsledků dotazu.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud pracujete s vlastním předplatným, je vhodné vždy na konci projektu zkontrolovat, jestli budete vytvořené prostředky ještě potřebovat. Prostředky, které necháte běžet, vás stojí peníze. Prostředky můžete odstraňovat jednotlivě nebo můžete odstranit skupinu prostředků, a odstranit tak celou sadu prostředků najednou.

Prostředky můžete najít a spravovat na portálu pomocí odkazu **všechny prostředky** nebo **skupiny prostředků** v levém navigačním podokně.

Pokud používáte bezplatnou službu, pamatujte na to, že jste omezeni na tři indexy, indexery a zdroje dat. Jednotlivé položky na portálu můžete odstranit, aby zůstaly pod limitem. 

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu v C# jste pracovali pomocí řady úkolů, abyste mohli vytvořit index, načíst ho s dokumenty a spouštět dotazy. V různých fázích jsme udělali zkratky, které zjednodušují kód pro čitelnost a porozumění. Pokud jste obeznámeni se základními pojmy, doporučujeme další článek pro zkoumání alternativních přístupů a konceptů, které budou prohloubit vaše znalosti. 

> [!div class="nextstepaction"]
> [Vývoj v .NET](search-howto-dotnet-sdk.md)

Chcete optimalizovat a uložit své útraty do cloudu?

> [!div class="nextstepaction"]
> [Zahájení analýzy nákladů pomocí Cost Management](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)