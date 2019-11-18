---
title: 'C#Kurz: indexování více zdrojů dat'
titleSuffix: Azure Cognitive Search
description: Naučte se importovat data z více zdrojů dat do jednoho indexu služby Azure Kognitivní hledání pomocí indexerů. Tento kurz a vzorový kód jsou v C#.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: fbe3b9ada556f26bd559f040bf2ba5b22367abd0
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112220"
---
# <a name="c-tutorial-combine-data-from-multiple-data-sources-in-one-azure-cognitive-search-index"></a>C#Kurz: kombinování dat z více zdrojů dat v jednom indexu Azure Kognitivní hledání

Azure Kognitivní hledání může importovat, analyzovat a indexovat data z více zdrojů dat do jednoho kombinovaného indexu vyhledávání. To podporuje situace, kdy jsou strukturovaná data agregována s méně strukturovanými nebo i prostými textovými daty z jiných zdrojů, jako jsou text, HTML nebo dokumenty JSON.

V tomto kurzu se dozvíte, jak indexovat data hotelu z Azure Cosmos DB zdroje dat a sloučit je s podrobnostmi o hotelu z Azure Blob Storage dokumentů. Výsledkem bude kombinovaný index vyhledávání hotelu obsahující komplexní datové typy.

V tomto kurzu C#se používá sada .NET SDK pro Azure Kognitivní hledání a Azure Portal k provádění následujících úloh:

> [!div class="checklist"]
> * Nahrání ukázkových dat a vytváření zdrojů dat
> * Identifikujte klíč dokumentu
> * Definování a vytvoření indexu
> * Indexovat data hotelu z Azure Cosmos DB
> * Sloučení dat z hotelových místností z úložiště objektů BLOB

## <a name="prerequisites"></a>Požadavky

V tomto rychlém startu se používají následující služby, nástroje a data. 

- [Vytvořte službu Azure kognitivní hledání](search-create-service-portal.md) nebo [Najděte existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) v rámci aktuálního předplatného. Pro tento kurz můžete použít bezplatnou službu.

- [Vytvořte účet Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-cosmosdb-resources-portal) pro uložení ukázkových údajů o hotelu.

- [Vytvořte účet úložiště Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) pro ukládání ukázkových dat BLOB JSON.

- [Nainstalujte Visual Studio](https://visualstudio.microsoft.com/) pro použití jako rozhraní IDE.

### <a name="install-the-project-from-github"></a>Instalace projektu z GitHubu

1. Vyhledejte ukázkové úložiště na GitHubu: [Azure-Search-dotnet-Samples](https://github.com/Azure-Samples/azure-search-dotnet-samples).
1. Vyberte **klonovat nebo stáhnout** a udělejte si soukromou místní kopii úložiště.
1. Otevřete Visual Studio a nainstalujte balíček NuGet Microsoft Azure Kognitivní hledání, pokud ještě není nainstalovaný. V nabídce **nástroje** vyberte **Správce balíčků NuGet** a pak **spravujte balíčky NuGet pro řešení...** . Na kartě **Procházet** vyhledejte a pak nainstalujte **Microsoft. Azure. Search** (verze 9.0.1 nebo novější). K dokončení instalace budete muset kliknout na další dialogová okna.

    ![Přidání knihoven Azure pomocí NuGet](./media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png)

1. Pomocí sady Visual Studio přejděte do místního úložiště a otevřete soubor řešení **AzureSearchMultipleDataSources. sln**.

## <a name="get-a-key-and-url"></a>Získat klíč a adresu URL

Abyste mohli komunikovat se službou Azure Kognitivní hledání, budete potřebovat adresu URL služby a přístupový klíč. Vyhledávací služba se vytvoří s oběma, takže pokud jste do svého předplatného přidali Azure Kognitivní hledání, postupujte podle těchto kroků a získejte potřebné informace:

1. [Přihlaste se k Azure Portal](https://portal.azure.com/)a na stránce **Přehled** vyhledávací služby Získejte adresu URL. Příkladem koncového bodu může být `https://mydemo.search.windows.net`.

1. V části **nastavení** > **klíče**Získejte klíč správce s úplnými právy k této službě. Existují dva zaměnitelné klíče správce poskytované pro zajištění kontinuity podnikových služeb pro případ, že byste museli nějakou dobu navrátit. V žádostech o přidání, úpravu a odstranění objektů můžete použít primární nebo sekundární klíč.

![Získání koncového bodu HTTP a přístupového klíče](media/search-get-started-postman/get-url-key.png "Získání koncového bodu HTTP a přístupového klíče")

Všechny požadavky vyžadují klíč rozhraní API na všech žádostech odeslaných službě. Platný klíč vytváří vztah důvěryhodnosti na základě jednotlivých požadavků mezi aplikací odesílající požadavek a službu, která ho zpracovává.

## <a name="prepare-sample-azure-cosmos-db-data"></a>Příprava ukázkových Azure Cosmos DB dat

Tato ukázka používá dvě malé sady dat, které popisují sedm fiktivních hotelů. Jedna sada popisuje hotely samotné a načte se do databáze Azure Cosmos DB. Druhá sada obsahuje podrobnosti o hotelu místností a poskytuje se jako sedm samostatných souborů JSON, které se mají nahrát do Azure Blob Storage.

1. [Přihlaste se k Azure Portal](https://portal.azure.com)a potom přejděte na stránku přehled účtu Azure Cosmos DB.

1. V řádku nabídek klikněte na přidat kontejner. Zadejte "vytvořit novou databázi" a použijte název **Hotel-místnosti – DB**. Zadejte **hotely** pro název kolekce a **/HotelId** pro klíč oddílu. Kliknutím na tlačítko **OK** vytvořte databázi a kontejner.

   ![Přidat Azure Cosmos DB kontejner](media/tutorial-multiple-data-sources/cosmos-add-container.png "Přidání kontejneru Azure Cosmos DB")

1. Přejít na Cosmos DB Průzkumník dat a vyberte element **Items** v kontejneru **hotely** v rámci databáze **hotelových místností** . Pak na panelu příkazů klikněte na **nahrát položku** .

   ![Nahrát do kolekce Azure Cosmos DB](media/tutorial-multiple-data-sources/cosmos-upload.png "Nahrát do kolekce Cosmos DB")

1. Na panelu nahrání klikněte na tlačítko Složka a pak ve složce projektu přejděte do souboru **cosmosdb/HotelsDataSubset_CosmosDb. JSON** . Kliknutím na **OK** zahajte nahrávání.

   ![Vyberte soubor, který se má nahrát.](media/tutorial-multiple-data-sources/cosmos-upload2.png "Vyberte soubor, který se má nahrát.")

1. Pomocí tlačítka Aktualizovat aktualizujte zobrazení položek v kolekci hotelů. Měli byste vidět sedm nových databázových dokumentů uvedených v seznamu.

## <a name="prepare-sample-blob-data"></a>Příprava ukázkových dat objektů BLOB

1. [Přihlaste se k Azure Portal](https://portal.azure.com), přejděte k účtu úložiště Azure, klikněte na **objekty blob**a pak klikněte na **+ kontejner**.

1. [Vytvořte kontejner objektů BLOB](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) s názvem **hotelové místnosti** pro uložení ukázkových souborů JSON pro hotelovou místnost. Úroveň veřejného přístupu můžete nastavit na libovolnou z jeho platných hodnot.

   ![Vytvoření kontejneru objektů BLOB](media/tutorial-multiple-data-sources/blob-add-container.png "Vytvoření kontejneru objektů blob")

1. Po vytvoření kontejneru ho otevřete a na panelu příkazů vyberte **nahrát** .

   ![Nahrát na panel příkazů](media/search-semi-structured-data/upload-command-bar.png "Nahrát na panel příkazů")

1. Přejděte do složky, která obsahuje ukázkové soubory. Vyberte všechny z nich a pak klikněte na **nahrát**.

   ![Nahrání souborů](media/tutorial-multiple-data-sources/blob-upload.png "Nahrání souborů")

Až se nahrávání dokončí, soubory by se měly zobrazit v seznamu datového kontejneru.

## <a name="set-up-connections"></a>Nastavení připojení

Informace o připojení pro vyhledávací službu a zdroje dat jsou zadány v souboru **appSettings. JSON** v řešení. 

1. V aplikaci Visual Studio otevřete soubor **AzureSearchMultipleDataSources. sln** .

1. V Průzkumník řešení upravte soubor **appSettings. JSON** .  

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

První dvě položky používají adresu URL a klíče pro správu služby Azure Kognitivní hledání. Byl zadán koncový bod `https://mydemo.search.windows.net`, například název služby, který se má poskytnout `mydemo`.

Další položky určují názvy účtů a připojovací řetězec pro Azure Blob Storage a zdroje dat Azure Cosmos DB.

### <a name="identify-the-document-key"></a>Identifikujte klíč dokumentu

V Azure Kognitivní hledání pole klíč jednoznačně identifikuje každý dokument v indexu. Každý index vyhledávání musí mít přesně jedno pole Key typu `Edm.String`. Toto pole klíče musí být k dispozici pro každý dokument ve zdroji dat, který je přidán do indexu. (Ve skutečnosti je to jediné povinné pole.)

Při indexování dat z více zdrojů dat musí být každá hodnota klíče zdroje dat namapována na stejné klíčové pole v kombinovaném indexu. Často vyžaduje některé předem plánované plánování, které identifikují smysluplný klíč dokumentu pro váš index, a ujistěte se, že existuje v každém zdroji dat.

Indexery Azure Kognitivní hledání můžou použít mapování polí k přejmenování a dokonce formátování datových polí během procesu indexování, aby se zdrojová data mohla směrovat do správného pole indexu.

Například v našem příkladu Azure Cosmos DB data se identifikátor hotelu nazývá **HotelId**. V souborech objektů BLOB JSON se ale pro hotelové místnosti identifikátor hotelu nazývá **ID**. Program to pořídí mapováním pole **ID** z objektů blob na pole klíče **HotelId** v indexu.

> [!NOTE]
> Ve většině případů automaticky generované klíče dokumentů, například ty, které byly vytvořeny ve výchozím nastavení některými indexery, nevytvářejte vhodné klíče dokumentů pro kombinované indexy. Obecně budete chtít použít smysluplnou, jedinečnou hodnotu klíče, která již existuje v, nebo může být snadno přidána do zdrojů dat.

## <a name="understand-the-code"></a>Vysvětlení kódu

Jakmile jsou data a nastavení konfigurace na místě, vzorový program v **AzureSearchMultipleDataSources. sln** by měl být připravený k sestavování a spouštění.

Tato jednoduchá C#Konzolová aplikace/.NET provádí následující úlohy:
* Vytvoří nový index služby Azure Kognitivní hledání na základě struktury dat třídy C# hotelu (která také odkazuje na třídy adres a místností).
* Vytvoří zdroj dat Azure Cosmos DB a indexer, který mapuje Azure Cosmos DB data na pole indexu.
* Spustí indexer Azure Cosmos DB, který načte data hotelu.
* Vytvoří zdroj dat služby Azure Blob Storage a indexer, který mapuje data objektů BLOB JSON na pole indexu.
* Spustí indexer služby Azure Blob Storage, který načte data místností.

 Před spuštěním programu si prostudujte kód a definice indexu a indexeru pro tuto ukázku. Důležitý kód je ve dvou souborech:

  + **Hotel.cs** obsahuje schéma, které definuje index.
  + **Program.cs** obsahuje funkce, které vytvářejí index služby Azure kognitivní hledání, zdroje dat a indexery a načítají kombinované výsledky do indexu.

### <a name="define-the-index"></a>Definice indexu

Tento ukázkový program používá sadu .NET SDK k definování a vytvoření indexu služby Azure Kognitivní hledání. Využívá třídu [FieldBuilder](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.fieldbuilder) k vygenerování struktury indexu z třídy C# datového modelu.

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

V souboru **program.cs** je index definován s názvem a kolekcí polí generovaných metodou `FieldBuilder.BuildForType<Hotel>()` a pak vytvořen takto:

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

Objekty blob JSON obsahují klíčové pole s názvem **ID** , nikoli **HotelId**. Kód používá třídu `FieldMapping` k oznámení indexeru k nasměrování hodnoty pole **ID** na klíč dokumentu **HotelId** v indexu.

Indexery BLOB Storage můžou používat parametry, které identifikují režim analýzy, který se má použít. Režim analýzy se liší u objektů blob, které reprezentují jediný dokument, nebo více dokumentů v rámci stejného objektu BLOB. V tomto příkladu každý objekt BLOB představuje jeden indexový dokument, takže kód používá parametr `IndexingParameters.ParseJson()`.

Další informace o parametrech analýzy indexeru pro objekty blob JSON najdete v tématu [indexování objektů BLOB JSON](search-howto-index-json-blobs.md). Další informace o zadání těchto parametrů pomocí sady .NET SDK naleznete v tématu Třída [IndexerParametersExtension](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexingparametersextensions) .

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

## <a name="search-your-json-files"></a>Prohledávání souborů JSON

Po spuštění programu můžete prozkoumat vyplněný index vyhledávání pomocí [**Průzkumníka vyhledávání**](search-explorer.md) na portálu.

V Azure Portal otevřete stránku **Přehled** služby Search a v seznamu **indexy** Najděte rejstřík **Hotel-místnosti – vzor** .

  ![Seznam indexů Kognitivní hledání Azure](media/tutorial-multiple-data-sources/index-list.png "Seznam indexů Kognitivní hledání Azure")

V seznamu klikněte na rejstřík hotelových místností. Pro index se zobrazí rozhraní Průzkumníka služby Search. Zadejte dotaz pro termín, jako je například "luxus". Ve výsledcích by se měl zobrazit alespoň jeden dokument a tento dokument by měl v poli místností zobrazit seznam objektů místností.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Nejrychlejší způsob vyčištění po kurzu odstraněním skupiny prostředků, která obsahuje službu Azure Kognitivní hledání. Odstraněním skupiny prostředků teď můžete trvale odstranit všechno, co se v ní nachází. Na portálu je název skupiny prostředků na stránce Přehled služby Azure Kognitivní hledání.

## <a name="next-steps"></a>Další kroky

Existuje několik přístupů a několik možností indexování objektů BLOB JSON. Pokud zdrojová data obsahují obsah JSON, můžete si projít tyto možnosti, abyste viděli, co nejlépe vyhovuje vašemu scénáři.

> [!div class="nextstepaction"]
> [Indexování objektů BLOB JSON pomocí indexeru Azure Kognitivní hledání BLOB](search-howto-index-json-blobs.md)

Je možné, že budete chtít rozšířit strukturovaná data indexu z jednoho zdroje dat s obohacenými daty z nestrukturovaných objektů BLOB nebo fulltextového obsahu. V následujícím kurzu se dozvíte, jak používat Cognitive Services společně s Azure Kognitivní hledání pomocí sady .NET SDK.

> [!div class="nextstepaction"]
> [Volání rozhraní API služeb Cognitive Services v kanálu indexování Kognitivní hledání Azure](cognitive-search-tutorial-blob-dotnet.md)
