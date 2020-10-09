---
title: Kurz C# – indexování více zdrojů dat Azure
titleSuffix: Azure Cognitive Search
description: Naučte se importovat data z více zdrojů dat do jednoho indexu služby Azure Kognitivní hledání pomocí indexerů. Tento kurz a vzorový kód jsou v jazyce C#.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 10/07/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 06b80b5fe14a7a913d8ad8454c6568b04fe01c2f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91819792"
---
# <a name="tutorial-index-from-multiple-data-sources-using-the-net-sdk"></a>Kurz: indexování z více zdrojů dat pomocí sady .NET SDK

Azure Kognitivní hledání může importovat, analyzovat a indexovat data z několika zdrojů dat do jednoho indexu vyhledávání konsolidace. To podporuje situace, kdy jsou strukturovaná data agregována s méně strukturovanými nebo i prostými textovými daty z jiných zdrojů, jako jsou text, HTML nebo dokumenty JSON.

V tomto kurzu se dozvíte, jak indexovat data hotelu z Azure Cosmos DB zdroje dat a sloučit je s podrobnostmi o hotelu z Azure Blob Storage dokumentů. Výsledkem bude kombinovaný index vyhledávání hotelu obsahující komplexní datové typy.

V tomto kurzu se používá C# a [.NET SDK](/dotnet/api/overview/azure/search). V tomto kurzu provedete následující úlohy:

> [!div class="checklist"]
> * Nahrání ukázkových dat a vytváření zdrojů dat
> * Identifikujte klíč dokumentu
> * Definování a vytvoření indexu
> * Indexovat data hotelu z Azure Cosmos DB
> * Sloučení dat z hotelových místností z úložiště objektů BLOB

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Předpoklady

+ [Azure Cosmos DB](../cosmos-db/create-cosmosdb-resources-portal.md)
+ [Azure Storage](../storage/common/storage-account-create.md)
+ [Visual Studio 2019](https://visualstudio.microsoft.com/)
+ [Vytvoření](search-create-service-portal.md) nebo [vyhledání existující vyhledávací služby](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> Pro tento kurz můžete použít bezplatnou službu. Bezplatná vyhledávací služba omezuje tři indexy, tři indexery a tři zdroje dat. V tomto kurzu se vytváří od každého jeden. Než začnete, ujistěte se, že máte ve své službě místo pro přijímání nových prostředků.

## <a name="download-files"></a>Stažení souborů

Zdrojový kód pro tento kurz najdete v úložišti GitHub [Azure-Search-dotnet-Samples](https://github.com/Azure-Samples/azure-search-dotnet-samples) ve složce s [více datovými zdroji](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources) .

## <a name="1---create-services"></a>1. vytvoření služeb

V tomto kurzu se používá Azure Kognitivní hledání pro indexování a dotazy, Azure Cosmos DB pro jednu sadu dat a Azure Blob Storage pro druhou datovou sadu. 

Pokud je to možné, vytvořte všechny služby ve stejné oblasti a skupině prostředků pro možnost blízkost a spravovatelnost. V praxi se vaše služby můžou nacházet v jakékoli oblasti.

Tato ukázka používá dvě malé sady dat, které popisují sedm fiktivních hotelů. Jedna sada popisuje hotely samotné a načte se do databáze Azure Cosmos DB. Druhá sada obsahuje podrobnosti o hotelu místností a poskytuje se jako sedm samostatných souborů JSON, které se mají nahrát do Azure Blob Storage.

### <a name="start-with-cosmos-db"></a>Začínáme s Cosmos DB

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a potom přejděte na stránku přehled účtu Azure Cosmos DB.

1. Vyberte **Průzkumník dat** a pak vyberte **Nová databáze**.

   :::image type="content" source="media/tutorial-multiple-data-sources/cosmos-newdb.png" alt-text="Vytvoření nové databáze" border="false":::

1. Zadejte název **hotelu-místnosti-DB**. Přijměte výchozí hodnoty pro zbývající nastavení.

   :::image type="content" source="media/tutorial-multiple-data-sources/cosmos-dbname.png" alt-text="Vytvoření nové databáze" border="false":::

1. Vytvořte nový kontejner. Použijte existující databázi, kterou jste právě vytvořili. Zadejte **hotely** pro název kontejneru a použijte **/HotelId** pro klíč oddílu.

   :::image type="content" source="media/tutorial-multiple-data-sources/cosmos-add-container.png" alt-text="Vytvoření nové databáze" border="false":::

1. Vyberte **položky** v části **hotely**a pak klikněte na tlačítko **nahrát položku** na panelu příkazů. Přejděte na a potom vyberte soubor **cosmosdb/HotelsDataSubset_CosmosDb.jsve** složce projektu.

   :::image type="content" source="media/tutorial-multiple-data-sources/cosmos-upload.png" alt-text="Vytvoření nové databáze" border="false":::

1. Pomocí tlačítka Aktualizovat aktualizujte zobrazení položek v kolekci hotelů. Měli byste vidět sedm nových databázových dokumentů uvedených v seznamu.

### <a name="azure-blob-storage"></a>Azure Blob Storage

1. Přihlaste se k [Azure Portal](https://portal.azure.com), přejděte k účtu úložiště Azure, klikněte na **objekty blob**a pak klikněte na **+ kontejner**.

1. [Vytvořte kontejner objektů BLOB](../storage/blobs/storage-quickstart-blobs-portal.md) s názvem **hotelové místnosti** pro uložení ukázkových souborů JSON pro hotelovou místnost. Úroveň veřejného přístupu můžete nastavit na libovolnou z jeho platných hodnot.

   :::image type="content" source="media/tutorial-multiple-data-sources/blob-add-container.png" alt-text="Vytvoření nové databáze" border="false":::

1. Po vytvoření kontejneru ho otevřete a na panelu příkazů vyberte **nahrát** . Přejděte do složky, která obsahuje ukázkové soubory. Vyberte všechny z nich a pak klikněte na **nahrát**.

   :::image type="content" source="media/tutorial-multiple-data-sources/blob-upload.png" alt-text="Vytvoření nové databáze" border="false":::

Až se nahrávání dokončí, soubory by se měly zobrazit v seznamu datového kontejneru.

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

Třetí součástí je Azure Kognitivní hledání, kterou můžete vytvořit na [portálu](search-create-service-portal.md). K dokončení tohoto Názorného postupu můžete použít bezplatnou úroveň. 

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Získání klíčového rozhraní API pro správu a adresy URL pro Azure Kognitivní hledání

Abyste mohli komunikovat se službou Azure Kognitivní hledání, budete potřebovat adresu URL služby a přístupový klíč. Vyhledávací služba se vytvoří s oběma, takže pokud jste do svého předplatného přidali Azure Kognitivní hledání, postupujte podle těchto kroků a získejte potřebné informace:

1. [Přihlaste se k Azure Portal](https://portal.azure.com/)a na stránce **Přehled** vyhledávací služby Získejte adresu URL. Příkladem koncového bodu může být `https://mydemo.search.windows.net`.

1. V části **Nastavení**  >  **klíče**Získejte klíč správce s úplnými právy k této službě. Existují dva zaměnitelné klíče správce poskytované pro zajištění kontinuity podnikových služeb pro případ, že byste museli nějakou dobu navrátit. V žádostech o přidání, úpravu a odstranění objektů můžete použít primární nebo sekundární klíč.

   Získejte taky klíč dotazu. Osvědčeným postupem je vystavovat požadavky na dotazy s přístupem jen pro čtení.

   :::image type="content" source="media/search-get-started-nodejs/service-name-and-keys.png" alt-text="Vytvoření nové databáze" border="false":::

Platný klíč vytváří na základě žádosti vztah důvěryhodnosti mezi aplikací, která žádost odeslala, a službou, která ji zpracovává.

## <a name="2---set-up-your-environment"></a>2. nastavení prostředí

1. Spusťte sadu Visual Studio 2019 a v nabídce **nástroje** vyberte **Správce balíčků NuGet** a pak **spravujte balíčky NuGet pro řešení...**. 

1. Na kartě **Procházet** vyhledejte a pak nainstalujte **Microsoft. Azure. Search** (verze 9.0.1 nebo novější). K dokončení instalace budete muset kliknout na další dialogová okna.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png" alt-text="Vytvoření nové databáze" border="false":::

1. Vyhledejte **Microsoft.Extensions.Configuration.Jsna** balíčku NuGet a nainstalujte ho také.

1. Otevřete soubor řešení **AzureSearchMultipleDataSources. sln**.

1. V Průzkumník řešení upravte **appsettings.jsv** souboru, aby se přidaly informace o připojení.  

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

První dvě položky používají adresu URL a klíče pro správu služby Azure Kognitivní hledání. Byl zadán koncový bod `https://mydemo.search.windows.net` , například název služby, který má být poskytnut `mydemo` .

Další položky určují názvy účtů a připojovací řetězec pro Azure Blob Storage a zdroje dat Azure Cosmos DB.

## <a name="3---map-key-fields"></a>3 – klíčová pole pro mapování

Sloučení obsahu vyžaduje, aby byly oba datové proudy cíleny na stejné dokumenty v indexu vyhledávání. 

V Azure Kognitivní hledání pole Key jednoznačně identifikuje každý dokument. Každý index vyhledávání musí mít přesně jedno pole klíče typu `Edm.String` . Toto pole klíče musí být k dispozici pro každý dokument ve zdroji dat, který je přidán do indexu. (Ve skutečnosti je to jediné povinné pole.)

Při indexování dat z více zdrojů dat se ujistěte, že každý příchozí řádek nebo dokument obsahuje společný klíč dokumentu pro sloučení dat ze dvou fyzicky odlišných zdrojových dokumentů do nového dokumentu vyhledávání v kombinovaném indexu. 

Často vyžaduje některé předem plánované plánování pro identifikaci smysluplného klíče dokumentu pro váš index a zajistěte, aby existovaly v obou zdrojích dat. V této ukázce `HotelId` se klíč pro každý Hotel v Cosmos DB nachází také v objektech blob JSON pro místnosti v úložišti objektů BLOB.

Indexery Azure Kognitivní hledání můžou použít mapování polí k přejmenování a dokonce formátování datových polí během procesu indexování, aby se zdrojová data mohla směrovat do správného pole indexu. Například v Cosmos DB je volán identifikátor hotelu **`HotelId`** . Ale v souborech objektů BLOB JSON pro hotelové místnosti se identifikátor hotelu jmenuje **`Id`** . Program to pořídí mapováním **`Id`** pole z objektů blob na **`HotelId`** pole klíče v indexu.

> [!NOTE]
> Ve většině případů automaticky generované klíče dokumentů, například ty, které byly vytvořeny ve výchozím nastavení některými indexery, nedělají pro kombinované indexy vhodné klíče dokumentů. Obecně budete chtít použít smysluplnou, jedinečnou hodnotu klíče, která již existuje v, nebo může být snadno přidána do zdrojů dat.

## <a name="4---explore-the-code"></a>4. Prozkoumejte kód

Jakmile jsou data a nastavení konfigurace na místě, vzorový program v **AzureSearchMultipleDataSources. sln** by měl být připravený k sestavování a spouštění.

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

Tento ukázkový program používá sadu .NET SDK k definování a vytvoření indexu služby Azure Kognitivní hledání. Využívá třídu [FieldBuilder](/dotnet/api/microsoft.azure.search.fieldbuilder) k vygenerování struktury indexu z třídy datového modelu jazyka C#.

Datový model je definován třídou hotelu, která také obsahuje odkazy na třídy Address a Room. FieldBuilder projde k podrobnostem v různých definicích tříd a vygeneruje složitou strukturu dat pro index. Značky metadat slouží k definování atributů každého pole, jako je například, zda je možné prohledávatelné nebo seřaditelné.

Následující fragmenty kódu ze souboru **Hotel.cs** ukazují, jak lze určit jedno pole a odkaz na jinou třídu datového modelu.

```csharp
. . . 
[IsSearchable, IsFilterable, IsSortable]
public string HotelName { get; set; }
. . .
public Room[] Rooms { get; set; }
. . .
```

V souboru **program.cs** je index definován s názvem a kolekcí polí generovaných `FieldBuilder.BuildForType<Hotel>()` metodou a pak vytvořen takto:

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

### <a name="create-azure-cosmos-db-data-source-and-indexer"></a>Vytvoření zdroje dat Azure Cosmos DB a indexeru

Další hlavní program obsahuje logiku pro vytvoření zdroje dat Azure Cosmos DB pro data hotelů.

Nejprve zřetězí název Azure Cosmos DB databáze do připojovacího řetězce. Pak definuje objekt zdroje dat, včetně nastavení specifických pro Azure Cosmos DB zdroje, jako je například vlastnost [useChangeDetection].

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

Po vytvoření zdroje dat program nastaví Azure Cosmos DB indexer s názvem **Hotel-místnosti-Cosmos-indexer**.

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
Program odstraní všechny existující indexery se stejným názvem před vytvořením nového, pro případ, že chcete spustit tento příklad více než jednou.

Tento příklad definuje plán pro indexer, takže se spustí jednou denně. Vlastnost Schedule můžete z tohoto volání odebrat, pokud nechcete, aby indexer znovu automaticky spouštěl v budoucnu.

### <a name="index-azure-cosmos-db-data"></a>Data Azure Cosmos DB indexu

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

Tento příklad obsahuje jednoduchý blok try-catch, který oznamuje všechny chyby, ke kterým může dojít během provádění.

Po spuštění indexeru Azure Cosmos DB bude index vyhledávání obsahovat úplnou sadu ukázkových dokumentů hotelového typu. Pole místností pro každý Hotel však bude prázdné pole, protože zdroj dat Azure Cosmos DB neobsahuje žádné podrobnosti o místnostech. V dalším kroku se program vyžádá z úložiště objektů BLOB pro načtení a sloučení dat místnosti.

### <a name="create-blob-storage-data-source-and-indexer"></a>Vytvoření zdroje dat služby Blob Storage a indexeru

Chcete-li získat podrobnosti o místnosti, program nejprve nastaví zdroj dat úložiště objektů BLOB tak, aby odkazoval na sadu individuálních souborů objektů BLOB JSON.

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

Po vytvoření zdroje dat program nastaví indexer objektů BLOB s názvem **hotelové místnosti – objekt BLOB-indexer**.

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

Objekty blob JSON obsahují klíčové pole s názvem **`Id`** , nikoli **`HotelId`** . Kód používá `FieldMapping` třídu k oznámení indexeru, aby se hodnota pole nasměrovala na **`Id`** **`HotelId`** klíč dokumentu v indexu.

Indexery BLOB Storage můžou používat parametry, které identifikují režim analýzy, který se má použít. Režim analýzy se liší u objektů blob, které reprezentují jediný dokument, nebo více dokumentů v rámci stejného objektu BLOB. V tomto příkladu každý objekt BLOB představuje jeden indexový dokument, takže kód používá `IndexingParameters.ParseJson()` parametr.

Další informace o parametrech analýzy indexeru pro objekty blob JSON najdete v tématu [indexování objektů BLOB JSON](search-howto-index-json-blobs.md). Další informace o zadání těchto parametrů pomocí sady .NET SDK naleznete v tématu Třída [IndexerParametersExtension](/dotnet/api/microsoft.azure.search.models.indexingparametersextensions) .

Program odstraní všechny existující indexery se stejným názvem před vytvořením nového, pro případ, že chcete spustit tento příklad více než jednou.

Tento příklad definuje plán pro indexer, takže se spustí jednou denně. Vlastnost Schedule můžete z tohoto volání odebrat, pokud nechcete, aby indexer znovu automaticky spouštěl v budoucnu.

### <a name="index-blob-data"></a>Indexovat data objektů BLOB

Jakmile se vytvoří zdroj dat úložiště objektů BLOB a indexer, kód, který spouští indexer, je jednoduchý:

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

Vzhledem k tomu, že index již byl vyplněný daty z databáze Azure Cosmos DB, indexer objektů BLOB aktualizuje existující dokumenty v indexu a přidá podrobnosti místnosti.

> [!NOTE]
> Pokud máte stejná neklíčová pole v obou zdrojích dat a data v těchto polích se neshodují, bude index obsahovat hodnoty z libovolného indexeru, který byl naposledy spuštěn. V našem příkladu oba zdroje dat obsahují pole **hotely** . Pokud z nějakého důvodu jsou data v tomto poli odlišná, u dokumentů se stejnou klíčovou hodnotou bude data **hotelů** ze zdroje dat, který byl indexován v poslední době, hodnotou uloženou v indexu.

## <a name="5---search"></a>5 – hledání

Po spuštění programu můžete prozkoumat vyplněný index vyhledávání pomocí [**Průzkumníka vyhledávání**](search-explorer.md) na portálu.

V Azure Portal otevřete stránku **Přehled** služby Search a v seznamu **indexy** Najděte rejstřík **Hotel-místnosti – vzor** .

  :::image type="content" source="media/tutorial-multiple-data-sources/index-list.png" alt-text="Vytvoření nové databáze" border="false":::

V seznamu klikněte na rejstřík hotelových místností. Pro index se zobrazí rozhraní Průzkumníka služby Search. Zadejte dotaz pro termín, jako je například "luxus". Ve výsledcích by se měl zobrazit alespoň jeden dokument a tento dokument by měl v poli místností zobrazit seznam objektů místností.

## <a name="reset-and-rerun"></a>Resetování a opětovné spuštění

Ve fázích předčasného experimentu vývoje je nejužitečnějším přístupem k iteraci návrhu odstranění objektů z Azure Kognitivní hledání a umožnění kódu jejich opětovného sestavení. Názvy prostředků jsou jedinečné. Když se objekt odstraní, je možné ho znovu vytvořit se stejným názvem.

Vzorový kód pro tento kurz kontroluje existující objekty a odstraní je, abyste mohli znovu spustit kód.

Portál můžete také použít k odstranění indexů, indexerů a zdrojů dat.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud pracujete ve vlastním předplatném, je vhodné odebrat prostředky, které už nepotřebujete. Prostředky, které necháte běžet, vás stojí peníze. Můžete odstraňovat prostředky jednotlivě nebo odstraněním skupiny prostředků odstranit celou sadu prostředků najednou.

Prostředky můžete najít a spravovat na portálu pomocí odkazu všechny prostředky nebo skupiny prostředků v levém navigačním podokně.

## <a name="next-steps"></a>Další kroky

Teď, když jste obeznámeni s konceptem ingestování dat z více zdrojů, se podíváme na konfiguraci indexeru od Cosmos DB.

> [!div class="nextstepaction"]
> [Konfigurace Azure Cosmos DB indexeru](search-howto-index-cosmosdb.md)