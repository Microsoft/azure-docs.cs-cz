---
title: Kurz C# – indexování více zdrojů dat Azure
titleSuffix: Azure Cognitive Search
description: Naučte se importovat data z více zdrojů dat do jednoho indexu služby Azure Kognitivní hledání pomocí indexerů. Tento kurz a vzorový kód jsou v jazyce C#.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 10/13/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: c9d9c43ae1be755ccb30fc377692257a81332ea8
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2020
ms.locfileid: "94593718"
---
# <a name="tutorial-index-from-multiple-data-sources-using-the-net-sdk"></a>Kurz: indexování z více zdrojů dat pomocí sady .NET SDK

Azure Kognitivní hledání může importovat, analyzovat a indexovat data z více zdrojů dat do jednoho konsolidovaného indexu vyhledávání. 

V tomto kurzu se pomocí C# aAzure.Search.Docklientské knihovny [ uments](/dotnet/api/overview/azure/search) v sadě Azure SDK pro .NET indexují ukázková data o hotelu z Azure Cosmos DB a sloučí se s podrobnějšími informacemi o hotelových místnostech z Azure Blob Storage Documents. Výsledkem bude kombinovaný index vyhledávání hotelu obsahující dokumenty hotelového typu, kde jsou místnosti jako komplexní datové typy.

V tomto kurzu provedete následující úlohy:

> [!div class="checklist"]
> * Nahrání ukázkových dat a vytváření zdrojů dat
> * Identifikujte klíč dokumentu
> * Definování a vytvoření indexu
> * Indexovat data hotelu z Azure Cosmos DB
> * Sloučení dat z hotelových místností z úložiště objektů BLOB

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="overview"></a>Přehled

V tomto kurzu se používá nová knihovna klienta [Azure.Search.Documents](/dotnet/api/overview/azure/search)verze 11. x k vytvoření a spuštění více indexerů. V tomto kurzu nastavíte dva zdroje dat Azure, abyste mohli nakonfigurovat indexer, který načte z obou pro naplnění jediného vyhledávacího indexu. Tyto dvě sady dat musí mít společnou hodnotu pro podporu sloučení. V této ukázce je toto pole ID. Pokud existuje pole společné pro podporu mapování, může indexer sloučit data z různorodých prostředků: strukturovaná data z Azure SQL, nestrukturovaná data z úložiště objektů BLOB nebo jakékoli kombinace [podporovaných zdrojů dat](search-indexer-overview.md#supported-data-sources) v Azure.

Hotovou verzi kódu v tomto kurzu najdete v následujícím projektu:

* [Multiple Data-Sources/V11 (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources/v11)

Tento kurz byl aktualizován tak, aby používal balíček Azure.Search.Documents (verze 11). Starší verzi sady .NET SDK najdete v tématu [Ukázka kódu Microsoft. Azure. Search (verze 10)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources/v10) na GitHubu.

## <a name="prerequisites"></a>Požadavky

+ [Azure Cosmos DB](../cosmos-db/create-cosmosdb-resources-portal.md)
+ [Azure Storage](../storage/common/storage-account-create.md)
+ [Visual Studio](https://visualstudio.microsoft.com/)
+ [Balíček NuGet pro Azure Kognitivní hledání (verze 11. x)](https://www.nuget.org/packages/Azure.Search.Documents/)
+ [Vytvoření](search-create-service-portal.md) nebo [vyhledání existující vyhledávací služby](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> Pro tento kurz můžete použít bezplatnou službu. Bezplatná vyhledávací služba omezuje tři indexy, tři indexery a tři zdroje dat. V tomto kurzu se vytváří od každého jeden. Než začnete, ujistěte se, že máte ve své službě místo pro přijímání nových prostředků.

## <a name="1---create-services"></a>1. vytvoření služeb

V tomto kurzu se používá Azure Kognitivní hledání pro indexování a dotazy, Azure Cosmos DB pro jednu sadu dat a Azure Blob Storage pro druhou datovou sadu. 

Pokud je to možné, vytvořte všechny služby ve stejné oblasti a skupině prostředků pro možnost blízkost a spravovatelnost. V praxi se vaše služby můžou nacházet v jakékoli oblasti.

Tato ukázka používá dvě malé sady dat, které popisují sedm fiktivních hotelů. Jedna sada popisuje hotely samotné a načte se do databáze Azure Cosmos DB. Druhá sada obsahuje podrobnosti o hotelu místností a poskytuje se jako sedm samostatných souborů JSON, které se mají nahrát do Azure Blob Storage.

### <a name="start-with-cosmos-db"></a>Začínáme s Cosmos DB

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a potom přejděte na stránku přehled účtu Azure Cosmos DB.

1. Vyberte **Průzkumník dat** a pak vyberte **Nová databáze**.

   :::image type="content" source="media/tutorial-multiple-data-sources/cosmos-newdb.png" alt-text="Vytvoření nové databáze" border="false":::

1. Zadejte název **hotelu-místnosti-DB**. Přijměte výchozí hodnoty pro zbývající nastavení.

   :::image type="content" source="media/tutorial-multiple-data-sources/cosmos-dbname.png" alt-text="Konfigurace databáze" border="false":::

1. Vytvořte nový kontejner. Použijte existující databázi, kterou jste právě vytvořili. Zadejte **hotely** pro název kontejneru a použijte **/HotelId** pro klíč oddílu.

   :::image type="content" source="media/tutorial-multiple-data-sources/cosmos-add-container.png" alt-text="Přidání kontejneru" border="false":::

1. Vyberte **položky** v části **hotely** a pak klikněte na tlačítko **nahrát položku** na panelu příkazů. Přejděte na a potom vyberte soubor **cosmosdb/HotelsDataSubset_CosmosDb.jsve** složce projektu.

   :::image type="content" source="media/tutorial-multiple-data-sources/cosmos-upload.png" alt-text="Nahrát do kolekce Azure Cosmos DB" border="false":::

1. Pomocí tlačítka Aktualizovat aktualizujte zobrazení položek v kolekci hotelů. Měli byste vidět sedm nových databázových dokumentů uvedených v seznamu.

1. Zkopírujte připojovací řetězec ze stránky **klíče** do poznámkového bloku. Budete ho potřebovat pro **appsettings.js** v pozdějším kroku. Pokud jste nepoužili navrhovaný název databáze "Hotel-místnosti-DB", zkopírujte také název databáze.

### <a name="azure-blob-storage"></a>Azure Blob Storage

1. Přihlaste se k [Azure Portal](https://portal.azure.com), přejděte k účtu úložiště Azure, klikněte na **objekty blob** a pak klikněte na **+ kontejner**.

1. [Vytvořte kontejner objektů BLOB](../storage/blobs/storage-quickstart-blobs-portal.md) s názvem **hotelové místnosti** pro uložení ukázkových souborů JSON pro hotelovou místnost. Úroveň veřejného přístupu můžete nastavit na libovolnou z jeho platných hodnot.

   :::image type="content" source="media/tutorial-multiple-data-sources/blob-add-container.png" alt-text="Vytvoření kontejneru objektů blob" border="false":::

1. Po vytvoření kontejneru ho otevřete a na panelu příkazů vyberte **nahrát** . Přejděte do složky, která obsahuje ukázkové soubory. Vyberte všechny z nich a pak klikněte na **nahrát**.

   :::image type="content" source="media/tutorial-multiple-data-sources/blob-upload.png" alt-text="Nahrání souborů" border="false":::

1. Zkopírujte název účtu úložiště a připojovací řetězec ze stránky **přístupové klíče** do poznámkového bloku. V pozdějším kroku budete potřebovat obě hodnoty pro **appsettings.js** .

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

Třetí součástí je Azure Kognitivní hledání, kterou můžete vytvořit na [portálu](search-create-service-portal.md). 

### <a name="copy-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Kopírování klíče rozhraní API pro správu a adresy URL pro Azure Kognitivní hledání

K ověření pro vaši vyhledávací službu budete potřebovat adresu URL služby a přístupový klíč.

1. [Přihlaste se k Azure Portal](https://portal.azure.com/)a na stránce **Přehled** vyhledávací služby Získejte adresu URL. Příkladem koncového bodu může být `https://mydemo.search.windows.net`.

1. V části **Nastavení**  >  **klíče** Získejte klíč správce s úplnými právy k této službě. Existují dva zaměnitelné klíče správce poskytované pro zajištění kontinuity podnikových služeb pro případ, že byste museli nějakou dobu navrátit. V žádostech o přidání, úpravu a odstranění objektů můžete použít primární nebo sekundární klíč.

   :::image type="content" source="media/search-get-started-javascript/service-name-and-keys.png" alt-text="Získání názvu služby a klíčů pro správu a dotazy" border="false":::

Platný klíč vytváří na základě žádosti vztah důvěryhodnosti mezi aplikací, která žádost odeslala, a službou, která ji zpracovává.

## <a name="2---set-up-your-environment"></a>2. nastavení prostředí

1. Spusťte Visual Studio a v nabídce **nástroje** vyberte **Správce balíčků NuGet** a pak **spravujte balíčky NuGet pro řešení...**. 

1. Na kartě **Procházet** vyhledejte a pak nainstalujte **Azure.Search.Documents** (verze 11,0 nebo novější). K dokončení instalace budete muset kliknout na další dialogová okna.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png" alt-text="Přidání knihoven Azure pomocí NuGet" border="false":::

1. Vyhledejte **Microsoft.Extensions.Configuration** a **Microsoft.Extensions.Configuration.Jsna** balíčcích NuGet a nainstalujte je taky.

1. Otevřete soubor řešení **/V11/AzureSearchMultipleDataSources.sln**.

1. V Průzkumník řešení upravte **appsettings.jsv** souboru, aby se přidaly informace o připojení.  

    ```json
    {
      "SearchServiceUri": "<YourSearchServiceURL>",
      "SearchServiceAdminApiKey": "<YourSearchServiceAdminApiKey>",
      "BlobStorageAccountName": "<YourBlobStorageAccountName>",
      "BlobStorageConnectionString": "<YourBlobStorageConnectionString>",
      "CosmosDBConnectionString": "<YourCosmosDBConnectionString>",
      "CosmosDBDatabaseName": "hotel-rooms-db"
    }
    ```

První dvě položky jsou adresa URL a klíče Správce služby vyhledávání. Použijte úplný koncový bod, například: `https://mydemo.search.windows.net` .

Další položky určují názvy účtů a připojovací řetězec pro Azure Blob Storage a zdroje dat Azure Cosmos DB.

## <a name="3---map-key-fields"></a>3 – klíčová pole pro mapování

Sloučení obsahu vyžaduje, aby byly oba datové proudy cíleny na stejné dokumenty v indexu vyhledávání. 

V Azure Kognitivní hledání pole Key jednoznačně identifikuje každý dokument. Každý index vyhledávání musí mít přesně jedno pole klíče typu `Edm.String` . Toto pole klíče musí být k dispozici pro každý dokument ve zdroji dat, který je přidán do indexu. (Ve skutečnosti je to jediné povinné pole.)

Při indexování dat z více zdrojů dat se ujistěte, že každý příchozí řádek nebo dokument obsahuje společný klíč dokumentu pro sloučení dat ze dvou fyzicky odlišných zdrojových dokumentů do nového dokumentu vyhledávání v kombinovaném indexu. 

Často vyžaduje některé předem plánované plánování pro identifikaci smysluplného klíče dokumentu pro váš index a zajistěte, aby existovaly v obou zdrojích dat. V této ukázce `HotelId` se klíč pro každý Hotel v Cosmos DB nachází také v objektech blob JSON pro místnosti v úložišti objektů BLOB.

Indexery Azure Kognitivní hledání můžou použít mapování polí k přejmenování a dokonce formátování datových polí během procesu indexování, aby se zdrojová data mohla směrovat do správného pole indexu. Například v Cosmos DB je volán identifikátor hotelu **`HotelId`** . Ale v souborech objektů BLOB JSON pro hotelové místnosti se identifikátor hotelu jmenuje **`Id`** . Program to pořídí mapováním **`Id`** pole z objektů blob na **`HotelId`** pole klíče v indexeru.

> [!NOTE]
> Ve většině případů automaticky generované klíče dokumentů, například ty, které byly vytvořeny ve výchozím nastavení některými indexery, nedělají pro kombinované indexy vhodné klíče dokumentů. Obecně budete chtít použít smysluplnou, jedinečnou hodnotu klíče, která již existuje v, nebo může být snadno přidána do zdrojů dat.

## <a name="4---explore-the-code"></a>4. Prozkoumejte kód

Jakmile jsou nastavená data a nastavení konfigurace, měl by být vzorový program v **/V11/AzureSearchMultipleDataSources.sln** připravený k sestavování a spouštění.

Tato jednoduchá aplikace konzoly C#/.NET provádí následující úlohy:

* Vytvoří nový index na základě struktury dat třídy hotelu v jazyce C# (která také odkazuje na třídy adres a místností).
* Vytvoří nový zdroj dat a indexer, který mapuje Azure Cosmos DB data na pole indexu. Jedná se o oba objekty v Azure Kognitivní hledání.
* Spustí indexer, aby se načetla data hotelu z Cosmos DB.
* Vytvoří druhý zdroj dat a indexer, který mapuje data objektu BLOB JSON na indexová pole.
* Spustí druhý indexer, který načte data místností z úložiště objektů BLOB.

 Před spuštěním programu si prostudujte kód a definice indexu a indexeru pro tuto ukázku. Důležitý kód je ve dvou souborech:

  + **Hotel.cs** obsahuje schéma, které definuje index.
  + **Program.cs** obsahuje funkce, které vytvářejí index služby Azure kognitivní hledání, zdroje dat a indexery a načítají kombinované výsledky do indexu.

### <a name="create-an-index"></a>Vytvoření indexu

Tento ukázkový program používá [CreateIndexAsync](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindexasync) k definování a vytvoření indexu služby Azure kognitivní hledání. Využívá třídu [FieldBuilder](/dotnet/api/azure.search.documents.indexes.fieldbuilder) k vygenerování struktury indexu z třídy datového modelu jazyka C#.

Datový model je definován třídou hotelu, která také obsahuje odkazy na třídy Address a Room. FieldBuilder projde k podrobnostem v různých definicích tříd a vygeneruje složitou strukturu dat pro index. Značky metadat slouží k definování atributů každého pole, jako je například, zda je možné prohledávatelné nebo seřaditelné.

Program odstraní všechny existující indexy stejného názvu před vytvořením nového, pro případ, že chcete spustit tento příklad více než jednou.

Následující fragmenty kódu ze souboru **Hotel.cs** zobrazují jednotlivá pole, za kterými následuje odkaz na jinou třídu datového modelu, místnost [], která je v souboru **Room.cs** definována (není zobrazená).

```csharp
. . .
[SimpleField(IsFilterable = true, IsKey = true)]
public string HotelId { get; set; }

[SearchableField(IsFilterable = true, IsSortable = true)]
public string HotelName { get; set; }
. . .
public Room[] Rooms { get; set; }
. . .
```

V souboru **program.cs** je [SearchIndex](/dotnet/api/azure.search.documents.indexes.models.searchindex) definován s názvem a kolekcí pole generovanou `FieldBuilder.Build` metodou a pak vytvořen takto:

```csharp
private static async Task CreateIndexAsync(string indexName, SearchIndexClient indexClient)
{
    // Create a new search index structure that matches the properties of the Hotel class.
    // The Address and Room classes are referenced from the Hotel class. The FieldBuilder
    // will enumerate these to create a complex data structure for the index.
    FieldBuilder builder = new FieldBuilder();
    var definition = new SearchIndex(indexName, builder.Build(typeof(Hotel)));

    await indexClient.CreateIndexAsync(definition);
}
```

### <a name="create-azure-cosmos-db-data-source-and-indexer"></a>Vytvoření zdroje dat Azure Cosmos DB a indexeru

Další hlavní program obsahuje logiku pro vytvoření zdroje dat Azure Cosmos DB pro data hotelů.

Nejprve zřetězí název Azure Cosmos DB databáze do připojovacího řetězce. Pak definuje objekt [SearchIndexerDataSourceConnection](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) .

```csharp
private static async Task CreateAndRunCosmosDbIndexerAsync(string indexName, SearchIndexerClient indexerClient)
{
    // Append the database name to the connection string
    string cosmosConnectString =
        configuration["CosmosDBConnectionString"]
        + ";Database="
        + configuration["CosmosDBDatabaseName"];

    SearchIndexerDataSourceConnection cosmosDbDataSource = new SearchIndexerDataSourceConnection(
        name: configuration["CosmosDBDatabaseName"],
        type: SearchIndexerDataSourceType.CosmosDb,
        connectionString: cosmosConnectString,
        container: new SearchIndexerDataContainer("hotels"));

    // The Cosmos DB data source does not need to be deleted if it already exists,
    // but the connection string might need to be updated if it has changed.
    await indexerClient.CreateOrUpdateDataSourceConnectionAsync(cosmosDbDataSource);
```

Po vytvoření zdroje dat program nastaví Azure Cosmos DB indexer s názvem **Hotel-místnosti-Cosmos-indexer**.

Program aktualizuje všechny existující indexery se stejným názvem, přepíše stávající indexer obsahem výše uvedeného kódu. Zahrnuje také akce resetování a spouštění pro případ, že chcete spustit tento příklad více než jednou.

Následující příklad definuje plán pro indexer, aby se spouštěl jednou za den. Vlastnost Schedule můžete z tohoto volání odebrat, pokud nechcete, aby indexer znovu automaticky spouštěl v budoucnu.

```csharp
SearchIndexer cosmosDbIndexer = new SearchIndexer(
    name: "hotel-rooms-cosmos-indexer",
    dataSourceName: cosmosDbDataSource.Name,
    targetIndexName: indexName)
{
    Schedule = new IndexingSchedule(TimeSpan.FromDays(1))
};

// Indexers keep metadata about how much they have already indexed.
// If we already ran the indexer, it "remembers" and does not run again.
// To avoid this, reset the indexer if it exists.
try
{
    await indexerClient.GetIndexerAsync(cosmosDbIndexer.Name);
    // Reset the indexer if it exists.
    await indexerClient.ResetIndexerAsync(cosmosDbIndexer.Name);
}
catch (RequestFailedException ex) when (ex.Status == 404)
{
    // If the indexer does not exist, 404 will be thrown.
}

await indexerClient.CreateOrUpdateIndexerAsync(cosmosDbIndexer);

Console.WriteLine("Running Cosmos DB indexer...\n");

try
{
    // Run the indexer.
    await indexerClient.RunIndexerAsync(cosmosDbIndexer.Name);
}
catch (RequestFailedException ex) when (ex.Status == 429)
{
    Console.WriteLine("Failed to run indexer: {0}", ex.Message);
}
```

Tento příklad obsahuje jednoduchý blok try-catch, který oznamuje všechny chyby, ke kterým může dojít během provádění.

Po spuštění indexeru Azure Cosmos DB bude index vyhledávání obsahovat úplnou sadu ukázkových dokumentů hotelového typu. Pole místností pro každý Hotel však bude prázdné pole, protože Azure Cosmos DB zdroj dat vynechává podrobnosti místnosti. V dalším kroku se program vyžádá z úložiště objektů BLOB pro načtení a sloučení dat místnosti.

### <a name="create-blob-storage-data-source-and-indexer"></a>Vytvoření zdroje dat služby Blob Storage a indexeru

Chcete-li získat podrobnosti místnosti, program nejprve nastaví zdroj dat úložiště objektů BLOB tak, aby odkazoval na sadu individuálních souborů objektů BLOB JSON.

```csharp
private static async Task CreateAndRunBlobIndexerAsync(string indexName, SearchIndexerClient indexerClient)
{
    SearchIndexerDataSourceConnection blobDataSource = new SearchIndexerDataSourceConnection(
        name: configuration["BlobStorageAccountName"],
        type: SearchIndexerDataSourceType.AzureBlob,
        connectionString: configuration["BlobStorageConnectionString"],
        container: new SearchIndexerDataContainer("hotel-rooms"));

    // The blob data source does not need to be deleted if it already exists,
    // but the connection string might need to be updated if it has changed.
    await indexerClient.CreateOrUpdateDataSourceConnectionAsync(blobDataSource);
```

Po vytvoření zdroje dat program nastaví indexer objektů BLOB s názvem **hotelové místnosti – objekt BLOB-indexer** , jak je znázorněno níže.

Objekty blob JSON obsahují klíčové pole s názvem **`Id`** , nikoli **`HotelId`** . Kód používá `FieldMapping` třídu k oznámení indexeru, aby se hodnota pole nasměrovala na **`Id`** **`HotelId`** klíč dokumentu v indexu.

Indexery služby Blob Storage můžou použít [IndexingParameters](/dotnet/api/azure.search.documents.indexes.models.indexingparameters) k určení režimu analýzy. V závislosti na tom, zda objekty blob reprezentují jeden dokument nebo více dokumentů v rámci stejného objektu blob, byste měli nastavit různé režimy analýzy. V tomto příkladu každý objekt BLOB představuje jeden dokument JSON, takže kód používá `json` režim analýzy. Další informace o parametrech analýzy indexeru pro objekty blob JSON najdete v tématu [indexování objektů BLOB JSON](search-howto-index-json-blobs.md).

Tento příklad definuje plán pro indexer, takže se spustí jednou denně. Vlastnost Schedule můžete z tohoto volání odebrat, pokud nechcete, aby indexer znovu automaticky spouštěl v budoucnu.

```csharp
// Map the Id field in the Room documents to the HotelId key field in the index
List<FieldMapping> map = new List<FieldMapping> {
    new FieldMapping("Id")
    {
        TargetFieldName =  "HotelId"
    }
};

IndexingParameters parameters = new IndexingParameters();
parameters.Configuration.Add("parsingMode", "json");

SearchIndexer blobIndexer = new SearchIndexer(
    name: "hotel-rooms-blob-indexer",
    dataSourceName: blobDataSource.Name,
    targetIndexName: indexName)
{
    Parameters = parameters,
    Schedule = new IndexingSchedule(TimeSpan.FromDays(1))
};

blobIndexer.FieldMappings.Add(new FieldMapping("Id") { TargetFieldName = "HotelId" });

// Reset the indexer if it already exists
try
{
    await indexerClient.GetIndexerAsync(blobIndexer.Name);
    await indexerClient.ResetIndexerAsync(blobIndexer.Name);
}
catch (RequestFailedException ex) when (ex.Status == 404) { }

await indexerClient.CreateOrUpdateIndexerAsync(blobIndexer);

try
{
    // Run the indexer.
    await searchService.Indexers.RunAsync(blobIndexer.Name);
}
catch (CloudException e) when (e.Response.StatusCode == (HttpStatusCode)429)
{
    Console.WriteLine("Failed to run indexer: {0}", e.Response.Content);
}
```

Vzhledem k tomu, že index již byl vyplněný daty z databáze Azure Cosmos DB, indexer objektů BLOB aktualizuje existující dokumenty v indexu a přidá podrobnosti místnosti.

> [!NOTE]
> Pokud máte stejná neklíčová pole v obou zdrojích dat a data v těchto polích se neshodují, bude index obsahovat hodnoty z libovolného indexeru, který byl naposledy spuštěn. V našem příkladu oba zdroje dat obsahují pole **hotely** . Pokud z nějakého důvodu jsou data v tomto poli odlišná, u dokumentů se stejnou klíčovou hodnotou bude data **hotelů** ze zdroje dat, který byl indexován v poslední době, hodnotou uloženou v indexu.

## <a name="5---search"></a>5 – hledání

Po spuštění programu můžete prozkoumat vyplněný index vyhledávání pomocí [**Průzkumníka vyhledávání**](search-explorer.md) na portálu.

V Azure Portal otevřete stránku **Přehled** služby Search a v seznamu **indexy** Najděte rejstřík **Hotel-místnosti – vzor** .

  :::image type="content" source="media/tutorial-multiple-data-sources/index-list.png" alt-text="Seznam indexů Kognitivní hledání Azure" border="false":::

V seznamu klikněte na rejstřík hotelových místností. Pro index se zobrazí rozhraní Průzkumníka služby Search. Zadejte dotaz pro termín, jako je například "luxus". Ve výsledcích by se měl zobrazit alespoň jeden dokument a tento dokument by měl v poli místností zobrazit seznam objektů místností.

## <a name="reset-and-rerun"></a>Resetování a opětovné spuštění

Ve fázích předčasného experimentu vývoje je nejužitečnějším přístupem k iteraci návrhu odstranění objektů z Azure Kognitivní hledání a umožnění kódu jejich opětovného sestavení. Názvy prostředků jsou jedinečné. Když se objekt odstraní, je možné ho znovu vytvořit se stejným názvem.

Ukázkový kód zkontroluje existující objekty a odstraní je nebo aktualizuje, aby bylo možné spustit program znovu.

Portál můžete také použít k odstranění indexů, indexerů a zdrojů dat.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud pracujete ve vlastním předplatném, je vhodné odebrat prostředky, které už nepotřebujete. Prostředky, které necháte běžet, vás stojí peníze. Můžete odstraňovat prostředky jednotlivě nebo odstraněním skupiny prostředků odstranit celou sadu prostředků najednou.

Prostředky můžete najít a spravovat na portálu pomocí odkazu všechny prostředky nebo skupiny prostředků v levém navigačním podokně.

## <a name="next-steps"></a>Další kroky

Teď, když jste obeznámeni s konceptem ingestování dat z více zdrojů, se podíváme na konfiguraci indexeru od Cosmos DB.

> [!div class="nextstepaction"]
> [Konfigurace Azure Cosmos DB indexeru](search-howto-index-cosmosdb.md)
