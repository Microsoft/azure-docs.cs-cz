---
title: 'Kurz jazyka C#: Indexování více zdrojů dat'
titleSuffix: Azure Cognitive Search
description: Zjistěte, jak importovat data z více zdrojů dat do jednoho indexu Azure Cognitive Search pomocí indexerů. Tento kurz a ukázkový kód jsou v C#.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/28/2020
ms.openlocfilehash: 8e75d9de45c64813ac75de635371d2435fb9261f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "78271481"
---
# <a name="tutorial-index-data-from-multiple-data-sources-in-c"></a>Kurz: Indexování dat z více zdrojů dat v C #

Azure Cognitive Search můžete importovat, analyzovat a indexovat data z více zdrojů dat do jednoho konsolidovaného indexu vyhledávání. To podporuje situace, kdy jsou strukturovaná data agregována s méně strukturovanými nebo dokonce prostými textovými daty z jiných zdrojů, jako je text, HTML nebo dokumenty JSON.

Tento kurz popisuje, jak indexovat hotelová data ze zdroje dat Azure Cosmos DB a sloučit je s podrobnostmi o hotelovém pokoji, které jsou vykresleny z dokumentů úložiště objektů blob Azure. Výsledkem bude kombinovaný index vyhledávání hotelů obsahující komplexní datové typy.

Tento kurz používá c# a [.NET SDK](https://aka.ms/search-sdk). V tomto kurzu budete provádět následující úkoly:

> [!div class="checklist"]
> * Nahrání ukázkových dat a vytvoření zdrojů dat
> * Identifikace klíče dokumentu
> * Definování a vytvoření indexu
> * Indexování hotelových dat z Azure Cosmos DB
> * Sloučení dat hotelového pokoje z úložiště objektů blob

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

+ [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-cosmosdb-resources-portal)
+ [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
+ [Visual Studio 2019](https://visualstudio.microsoft.com/)
+ [Vytvoření](search-create-service-portal.md) nebo [vyhledání existující vyhledávací služby](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> Můžete použít bezplatnou službu pro tento kurz. Bezplatná vyhledávací služba vás omezí na tři indexy, tři indexery a tři zdroje dat. V tomto kurzu se vytváří od každého jeden. Než začnete, ujistěte se, že máte ve službě prostor pro přijetí nových zdrojů.

## <a name="download-files"></a>Stažení souborů

Zdrojový kód pro tento kurz je v úložišti [GitHub azure-search-dotnet-samples](https://github.com/Azure-Samples/azure-search-dotnet-samples) ve složce [s více zdroji dat.](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources)

## <a name="1---create-services"></a>1 - Vytváření služeb

Tento kurz používá Azure Cognitive Search pro indexování a dotazy, Azure Cosmos DB pro jednu sadu dat a úložiště objektů blob Azure pro druhou sadu dat. 

Pokud je to možné, vytvořte všechny služby ve stejné oblasti a skupině prostředků pro blízkost a správu. V praxi mohou být vaše služby v libovolnéoblasti.

Tato ukázka používá dvě malé sady dat, které popisují sedm fiktivních hotelů. Jedna sada popisuje samotné hotely a bude načtena do databáze Azure Cosmos DB. Druhá sada obsahuje podrobnosti o hotelovém pokoji a je k dispozici jako sedm samostatných souborů JSON, které se mají nahrát do úložiště objektů blob Azure.

### <a name="start-with-cosmos-db"></a>Začněte s Cosmos DB

1. Přihlaste se k [portálu Azure a](https://portal.azure.com)pak se projděte na stránce Přehled účtu Azure Cosmos DB.

1. Vyberte **Průzkumník dat** a potom vyberte **Nová databáze**.

   ![Vytvoření nové databáze](media/tutorial-multiple-data-sources/cosmos-newdb.png "Vytvoření nové databáze")

1. Zadejte název **hotel-rooms-db**. Přijměte výchozí hodnoty pro zbývající nastavení.

   ![Konfigurace databáze](media/tutorial-multiple-data-sources/cosmos-dbname.png "Konfigurace databáze")

1. Vytvořte nový kontejner. Použijte existující databázi, kterou jste právě vytvořili. Zadejte **hotely** pro název kontejneru a použijte **/HotelId** pro klíč oddílu.

   ![Přidání kontejneru](media/tutorial-multiple-data-sources/cosmos-add-container.png "Přidání kontejneru")

1. V yberte **Položky** v části **Hotely**a na panelu příkazů klikněte na **Nahrát položku.** Přejděte do složky projektu **cosmosdb/HotelsDataSubset_CosmosDb.json** a vyberte jej.

   ![Nahrání do kolekce Azure Cosmos DB](media/tutorial-multiple-data-sources/cosmos-upload.png "Nahrát do kolekce Cosmos DB")

1. Pomocí tlačítka Aktualizovat můžete aktualizovat zobrazení položek v kolekci hotelů. Měli byste vidět sedm nových databázových dokumentů uvedených.

### <a name="azure-blob-storage"></a>Azure Blob Storage

1. Přihlaste se na [portál Azure](https://portal.azure.com), přejděte na svůj účet úložiště Azure, klikněte na **Objekty blob**a potom klikněte na **+ Container**.

1. [Vytvořte kontejner blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) s názvem **hotelové pokoje** pro uložení ukázkových souborů JSON v hotelovém pokoji. Úroveň veřejného přístupu můžete nastavit na libovolnou z jejích platných hodnot.

   ![Vytvoření kontejneru objektů blob](media/tutorial-multiple-data-sources/blob-add-container.png "Vytvoření kontejneru objektů blob")

1. Po vytvoření kontejneru ho otevřete a na panelu příkazů vyberte **Nahrát.** Přejděte do složky obsahující ukázkové soubory. Vyberte všechny a klepněte na tlačítko **Nahrát**.

   ![Nahrání souborů](media/tutorial-multiple-data-sources/blob-upload.png "Nahrání souborů")

Po dokončení nahrávání by se soubory měly zobrazit v seznamu pro datový kontejner.

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

Třetí komponentou je Azure Cognitive Search, kterou můžete [vytvořit na portálu](search-create-service-portal.md). K dokončení tohoto návodu můžete použít úroveň Free. 

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Získání klíče api správce a adresy URL pro Azure Cognitive Search

K interakci se službou Azure Cognitive Search budete potřebovat adresu URL služby a přístupový klíč. Vyhledávací služba se vytvoří s oběma, takže pokud jste do předplatného přidali Azure Cognitive Search, postupujte podle následujících kroků a získejte potřebné informace:

1. [Přihlaste se na portál Azure](https://portal.azure.com/)portal a na stránce **Přehled** vyhledávací služby získáte adresu URL. Příkladem koncového bodu může být `https://mydemo.search.windows.net`.

1. V **nastavení** > **klíče**, získat klíč správce pro úplná práva ke službě. Existují dva zaměnitelné klíče pro správu, které jsou k dispozici pro kontinuitu podnikání v případě, že potřebujete převrátit jeden. Primární nebo sekundární klíč můžete použít při požadavcích na přidávání, úpravy a odstranění objektů.

   Získejte také klíč dotazu. Je osvědčeným postupem pro vydávání požadavků na dotazy s přístupem jen pro čtení.

   ![Získání názvu služby a klíčů pro správce a dotazy](media/search-get-started-nodejs/service-name-and-keys.png)

Platný klíč vytváří na základě žádosti vztah důvěryhodnosti mezi aplikací, která žádost odeslala, a službou, která ji zpracovává.

## <a name="2---set-up-your-environment"></a>2 - Nastavení prostředí

1. Spusťte Visual Studio 2019 a v nabídce **Nástroje** vyberte **NuGet Package Manager** a pak **spravovat balíčky NuGet pro řešení...**. 

1. Na kartě **Procházet** vyhledejte a nainstalujte **Microsoft.Azure.Search** (verze 9.0.1 nebo novější). Budete muset proklikat další dialogová okna k dokončení instalace.

    ![Přidání knihoven Azure pomocí NuGetu](./media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png)

1. Vyhledejte balíček **Microsoft.Extensions.Configuration.Json** NuGet a nainstalujte jej také.

1. Otevřete soubor řešení **AzureSearchMultipleDataSources.sln**.

1. V Průzkumníku řešení upravte soubor **appsettings.json** a přidejte informace o připojení.  

    ```json
    {
      "SearchServiceName": "Put your search service name here",
      "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
      "BlobStorageAccountName": "Put your Azure Storage account name here",
      "BlobStorageConnectionString": "Put your Azure Blob Storage connection string here",
      "CosmosDBConnectionString": "Put your Cosmos DB connection string here",
      "CosmosDBDatabaseName": "hotel-rooms-db"
    }
    ```

První dvě položky používají adresy URL a klíče správce pro vaši službu Azure Cognitive Search. Vzhledem k `https://mydemo.search.windows.net`tomu, koncový bod , například název služby poskytnout je `mydemo`.

Další položky určují názvy účtů a informace o připojovacím řetězci pro zdroje dat Azure Blob Storage a Azure Cosmos DB.

## <a name="3---map-key-fields"></a>3 - Mapa klíčových polí

Slučování obsahu vyžaduje, aby oba datové proudy cílily na stejné dokumenty v indexu vyhledávání. 

V Azure Cognitive Search pole klíče jednoznačně identifikuje každý dokument. Každý index vyhledávání musí mít přesně `Edm.String`jedno klíčové pole typu . Toto klíčové pole musí být k dispozici pro každý dokument ve zdroji dat, který je přidán do indexu. (Ve skutečnosti je to jediné povinné pole.)

Při indexování dat z více zdrojů dat se ujistěte, že každý příchozí řádek nebo dokument obsahuje společný klíč dokumentu pro sloučení dat ze dvou fyzicky odlišných zdrojových dokumentů do nového vyhledávacího dokumentu v kombinovaném indexu. 

Často vyžaduje některé počáteční plánování k identifikaci smysluplného klíče dokumentu pro váš index a ujistěte se, že existuje v obou zdrojích dat. V této ukázce je `HotelId` klíč pro každý hotel v Cosmos DB také k dispozici v místnostech, které jsou kbloby JSON ve skladu objektů Blob.

Indexery Azure Cognitive Search můžou během procesu indexování použít mapování polí k přejmenování a dokonce i k přeformátování datových polí, takže zdrojová data můžou být přesměrována na správné pole indexu. Například v Cosmos DB se identifikátor **`HotelId`** hotelu nazývá . Ale v souborech objektů Blob JSON pro hotelové **`Id`** pokoje je identifikátor hotelu pojmenován . Program to zvládá **`Id`** mapováním pole z objektů **`HotelId`** BLOB na klíčové pole v indexu.

> [!NOTE]
> Ve většině případů automaticky generované klíče dokumentu, například ty, které jsou vytvořeny ve výchozím nastavení některé indexery, nedělají dobré klíče dokumentu pro kombinované indexy. Obecně budete chtít použít smysluplnou, jedinečnou hodnotu klíče, která již existuje ve zdrojích dat nebo je lze snadno přidat.

## <a name="4---explore-the-code"></a>4 - Prozkoumejte kód

Jakmile jsou data a nastavení konfigurace na místě, ukázkový program v **AzureSearchMultipleDataSources.sln** by měl být připraven k sestavení a spuštění.

Tato jednoduchá konzolová aplikace C#/.NET provádí následující úkoly:

* Vytvoří nový index založený na datové struktuře třídy C# Hotel (která také odkazuje na třídu Adresa a Pokoj).
* Vytvoří nový zdroj dat a indexer, který mapuje data Azure Cosmos DB na indexová pole. Jedná se o oba objekty v Azure Cognitive Search.
* Spustí indexer pro načtení dat hotelu z Cosmos DB.
* Vytvoří druhý zdroj dat a indexer, který mapuje data objektu blob JSON na indexová pole.
* Spustí druhý indexer pro načtení dat místností z úložiště objektů Blob.

 Před spuštěním programu, trvat minutu studovat kód a index a indexer definice pro tuto ukázku. Důležitý kód je ve dvou souborech:

  + **Hotel.cs** obsahuje schéma, které definuje index
  + **Program.cs** obsahuje funkce, které vytvářejí index Azure Cognitive Search, zdroje dat a indexery a načítají kombinované výsledky do indexu.

### <a name="create-an-index"></a>Vytvoření indexu

Tento ukázkový program používá sdsad .NET k definování a vytvoření indexu Azure Cognitive Search. Využívá [fieldbuilder](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.fieldbuilder) třídy ke generování struktury indexu z třídy datového modelu Jazyka C#.

Datový model je definován třídou Hotel, která obsahuje také odkazy na třídy Adresa a Místnost. FieldBuilder procházet více definic tříd generovat komplexní strukturu dat pro index. Značky metadat se používají k definování atributů každého pole, například zda je prohledávatelné nebo seřaditelné.

Následující úryvky ze **souboru Hotel.cs** ukazují, jak lze zadat jedno pole a odkaz na jinou třídu datového modelu.

```csharp
. . . 
[IsSearchable, IsFilterable, IsSortable]
public string HotelName { get; set; }
. . .
public Room[] Rooms { get; set; }
. . .
```

V **souboru Program.cs** je index definován s názvem a kolekcí polí generovanou `FieldBuilder.BuildForType<Hotel>()` metodou a poté vytvořen následujícím způsobem:

```csharp
private static async Task CreateIndex(string indexName, SearchServiceClient searchService)
{
    // Create a new search index structure that matches the properties of the Hotel class.
    // The Address and Room classes are referenced from the Hotel class. The FieldBuilder
    // will enumerate these to create a complex data structure for the index.
    var definition = new Index()
    {
        Name = indexName,
        Fields = FieldBuilder.BuildForType<Hotel>()
    };
    await searchService.Indexes.CreateAsync(definition);
}
```

### <a name="create-azure-cosmos-db-data-source-and-indexer"></a>Vytvoření zdroje dat a indexeru Azure Cosmos DB

Další hlavní program obsahuje logiku k vytvoření zdroje dat Azure Cosmos DB pro data hotelů.

Nejprve zřetězí název databáze Azure Cosmos DB do připojovacího řetězce. Pak definuje objekt zdroje dat, včetně nastavení specifických pro zdroje Azure Cosmos DB, jako je například vlastnost [useChangeDetection].

  ```csharp
private static async Task CreateAndRunCosmosDbIndexer(string indexName, SearchServiceClient searchService)
{
    // Append the database name to the connection string
    string cosmosConnectString = 
        configuration["CosmosDBConnectionString"]
        + ";Database=" 
        + configuration["CosmosDBDatabaseName"];

    DataSource cosmosDbDataSource = DataSource.CosmosDb(
        name: configuration["CosmosDBDatabaseName"], 
        cosmosDbConnectionString: cosmosConnectString,
        collectionName: "hotels",
        useChangeDetection: true);

    // The Azure Cosmos DB data source does not need to be deleted if it already exists,
    // but the connection string might need to be updated if it has changed.
    await searchService.DataSources.CreateOrUpdateAsync(cosmosDbDataSource);
  ```

Po vytvoření zdroje dat program nastaví indexer Azure Cosmos DB s názvem **hotel-rooms-cosmos-indexer**.

```csharp
    Indexer cosmosDbIndexer = new Indexer(
        name: "hotel-rooms-cosmos-indexer",
        dataSourceName: cosmosDbDataSource.Name,
        targetIndexName: indexName,
        schedule: new IndexingSchedule(TimeSpan.FromDays(1)));
    
    // Indexers keep metadata about how much they have already indexed.
    // If we already ran this sample, the indexer will remember that it already
    // indexed the sample data and not run again.
    // To avoid this, reset the indexer if it exists.
    bool exists = await searchService.Indexers.ExistsAsync(cosmosDbIndexer.Name);
    if (exists)
    {
        await searchService.Indexers.ResetAsync(cosmosDbIndexer.Name);
    }
    await searchService.Indexers.CreateOrUpdateAsync(cosmosDbIndexer);
```
Program odstraní všechny existující indexery se stejným názvem před vytvořením nového, v případě, že chcete spustit tento příklad více než jednou.

Tento příklad definuje plán pro indexer, tak, aby byl spuštěn jednou denně. Pokud nechcete, aby se indexer v budoucnu automaticky spouštěl, můžete z tohoto hovoru odebrat vlastnost plánu.

### <a name="index-azure-cosmos-db-data"></a>Indexdat Azure Cosmos DB

Po vytvoření zdroje dat a indexeru je kód, který spouští indexer, stručný:

```csharp
    try
    {
        await searchService.Indexers.RunAsync(cosmosDbIndexer.Name);
    }
    catch (CloudException e) when (e.Response.StatusCode == (HttpStatusCode)429)
    {
        Console.WriteLine("Failed to run indexer: {0}", e.Response.Content);
    }
```

Tento příklad obsahuje jednoduchý blok try-catch pro hlášení chyb, ke kterým může dojít během provádění.

Po spuštění indexeru Azure Cosmos DB bude index hledání obsahovat úplnou sadu ukázkových hotelových dokumentů. Pole pokojů pro každý hotel však bude prázdné pole, protože zdroj dat Azure Cosmos DB neobsahoval žádné podrobnosti o místnosti. Dále bude program načítat z úložiště objektů Blob k načtení a sloučení dat místnosti.

### <a name="create-blob-storage-data-source-and-indexer"></a>Vytvoření zdroje dat úložiště objektů Blob a indexeru

Chcete-li získat podrobnosti o místnosti, program nejprve nastaví zdroj dat úložiště objektů Blob tak, aby odkazoval na sadu jednotlivých souborů objektů blob JSON.

```csharp
private static async Task CreateAndRunBlobIndexer(string indexName, SearchServiceClient searchService)
{
    DataSource blobDataSource = DataSource.AzureBlobStorage(
        name: configuration["BlobStorageAccountName"],
        storageConnectionString: configuration["BlobStorageConnectionString"],
        containerName: "hotel-rooms");

    // The blob data source does not need to be deleted if it already exists,
    // but the connection string might need to be updated if it has changed.
    await searchService.DataSources.CreateOrUpdateAsync(blobDataSource);
```

Po vytvoření zdroje dat program nastaví indexer objektů blob s názvem **hotel-rooms-blob-indexer**.

```csharp
    // Add a field mapping to match the Id field in the documents to 
    // the HotelId key field in the index
    List<FieldMapping> map = new List<FieldMapping> {
        new FieldMapping("Id", "HotelId")
    };

    Indexer blobIndexer = new Indexer(
        name: "hotel-rooms-blob-indexer",
        dataSourceName: blobDataSource.Name,
        targetIndexName: indexName,
        fieldMappings: map,
        parameters: new IndexingParameters().ParseJson(),
        schedule: new IndexingSchedule(TimeSpan.FromDays(1)));

    // Reset the indexer if it already exists
    bool exists = await searchService.Indexers.ExistsAsync(blobIndexer.Name);
    if (exists)
    {
        await searchService.Indexers.ResetAsync(blobIndexer.Name);
    }
    await searchService.Indexers.CreateOrUpdateAsync(blobIndexer);
```

Objekty BLOB JSON obsahují **`Id`** místo **`HotelId`**. Kód používá `FieldMapping` třídu sdělit indexeru **`Id`** nasměrovat **`HotelId`** hodnotu pole na klíč dokumentu v indexu.

Indexery úložiště objektů blob můžete použít parametry, které identifikují režim analýzy, který má být použit. Režim analýzy se liší pro objekty BLOB, které představují jeden dokument nebo více dokumentů v rámci stejného objektu blob. V tomto příkladu každý objekt blob představuje jeden `IndexingParameters.ParseJson()` dokument indexu, takže kód používá parametr.

Další informace o parametrech analýzy indexeru pro objekty BLOB JSON naleznete v [tématu Index JSON blobs](search-howto-index-json-blobs.md). Další informace o určení těchto parametrů pomocí sady .NET SDK naleznete v třídě [IndexerParametersExtension.](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexingparametersextensions)

Program odstraní všechny existující indexery se stejným názvem před vytvořením nového, v případě, že chcete spustit tento příklad více než jednou.

Tento příklad definuje plán pro indexer, tak, aby byl spuštěn jednou denně. Pokud nechcete, aby se indexer v budoucnu automaticky spouštěl, můžete z tohoto hovoru odebrat vlastnost plánu.

### <a name="index-blob-data"></a>Data objektu blob indexu

Po vytvoření zdroje dat úložiště objektů blob a indexeru je kód, který spouští indexer, jednoduchý:

```csharp
    try
    {
        await searchService.Indexers.RunAsync(cosmosDbIndexer.Name);
    }
    catch (CloudException e) when (e.Response.StatusCode == (HttpStatusCode)429)
    {
        Console.WriteLine("Failed to run indexer: {0}", e.Response.Content);
    }
```

Vzhledem k tomu, že index již byla naplněna daty hotelu z databáze Azure Cosmos DB, indexer objektů blob aktualizuje existující dokumenty v indexu a přidá podrobnosti o místnosti.

> [!NOTE]
> Pokud máte ve zdrojích dat stejná pole, která nejsou klíčem, a data v těchto polích se neshodují, bude index obsahovat hodnoty z toho, z čeho hodinek indexování bylo spuštěno naposledy. V našem příkladu obsahují oba zdroje dat pole **Název_** Pokud se z nějakého důvodu data v tomto poli liší, u dokumentů se stejnou hodnotou klíče budou data **HotelName** ze zdroje dat, který byl naposledy indexován, hodnotou uloženou v indexu.

## <a name="5---search"></a>5 - Hledání

Index vyplněného hledání můžete prozkoumat po spuštění programu pomocí [**průzkumníka vyhledávání**](search-explorer.md) na portálu.

Na webu Azure Portal otevřete stránku **Přehled** vyhledávací služby a v seznamu **Indexy** najděte ukázkový index **hotel-rooms.**

  ![Seznam indexů Azure Cognitive Search](media/tutorial-multiple-data-sources/index-list.png "Seznam indexů Azure Cognitive Search")

Klikněte na index vzorků hotel-pokoje v seznamu. Zobrazí se rozhraní Průzkumníka vyhledávání pro index. Zadejte dotaz na termín jako "Luxus". Ve výsledcích byste měli vidět alespoň jeden dokument a tento dokument by měl zobrazit seznam objektů místnosti v poli místností.

## <a name="reset-and-rerun"></a>Resetování a opětovné spuštění

V raných experimentálních fázích vývoje je nejpraktičtějším přístupem pro iteraci návrhu odstranění objektů z Azure Cognitive Search a povolení jejich opětovného sestavení kódu. Názvy prostředků jsou jedinečné. Když se objekt odstraní, je možné ho znovu vytvořit se stejným názvem.

Ukázkový kód pro tento kurz kontroluje existující objekty a odstraní je, takže můžete znovu spustit kód.

Portál můžete také použít k odstranění indexů, indexerů a zdrojů dat.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Při práci ve vlastním předplatném je na konci projektu vhodné odebrat prostředky, které už nepotřebujete. Prostředky, které necháte běžet, vás můžou stát peníze. Prostředky můžete odstraňovat jednotlivě nebo můžete odstranit skupinu prostředků, a odstranit tak celou sadu prostředků najednou.

Můžete najít a spravovat prostředky na portálu pomocí odkazu Všechny prostředky nebo skupiny prostředků v levém navigačním podokně.

## <a name="next-steps"></a>Další kroky

Teď, když jste obeznámeni s konceptem ingestování dat z více zdrojů, pojďme se blíže podívat na konfiguraci indexeru, počínaje Cosmos DB.

> [!div class="nextstepaction"]
> [Konfigurace indexeru Azure Cosmos DB](search-howto-index-cosmosdb.md)