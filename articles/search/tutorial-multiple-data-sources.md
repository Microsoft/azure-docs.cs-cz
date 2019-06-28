---
title: 'Kurz: Index více zdrojů dat – Azure Search'
description: Zjistěte, jak importovat data z různých zdrojů dat do jednoho indexu Azure Search.
author: RobDixon22
manager: HeidiSteen
services: search
ms.service: search
ms.topic: tutorial
ms.date: 06/21/2019
ms.author: v-rodixo
ms.custom: seodec2018
ms.openlocfilehash: 4186c422836771de4f8a283616d77214b91bfc02
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67462699"
---
# <a name="c-tutorial-combine-data-from-multiple-data-sources-in-one-azure-search-index"></a>C#Kurz: Kombinovat data z různých zdrojů dat v jednom indexu Azure Search

Služba Azure Search můžete importovat, analyzovat a indexaci dat z různých zdrojů dat do jedné kombinované vyhledávací index. Tento atribut podporuje situacích, kde je agregován strukturovaná data s daty bez strukturovaná nebo dokonce prostý text z jiných zdrojů, jako je text, HTML, nebo dokumenty JSON.

Tento kurz popisuje, jak indexovat hotelu data ze zdroje dat služby Azure Cosmos DB a sloučení, která s podrobnostmi o místnosti hotelu z dokumentů Azure Blob Storage. Výsledkem bude kombinované hotelu vyhledávací index obsahující komplexních datových typů.

Tento kurz používá C#, sady .NET SDK pro Azure Search a webu Azure portal a proveďte následující úlohy:

> [!div class="checklist"]
> * Nahrání ukázkových dat a vytvoření zdrojů dat
> * Identifikujte klíč dokumentu
> * Definování a vytvoření indexu
> * Index hotelu data ze služby cosmos DB
> * Sloučit hotelu místnosti data z úložiště objektů blob

## <a name="prerequisites"></a>Požadavky

Následující služby, nástroje a data se používají v tomto rychlém startu. 

- [Vytvoření služby Azure Search](search-create-service-portal.md) nebo [najít existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) pod vaším aktuálním předplatným. Můžete použít bezplatnou službu pro účely tohoto kurzu.

- [Vytvoření účtu služby Azure Cosmos DB](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) pro uložení ukázkových dat hotelu.

- [Vytvoření účtu služby Azure storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) pro ukládání ukázkovému kódu JSON data v objektech blob.

- [Instalace sady Visual Studio](https://visualstudio.microsoft.com/) pro použití jako rozhraní IDE.

### <a name="install-the-project-from-github"></a>Instalace projektu z Githubu

1. Vyhledejte ukázkové úložiště na Githubu: [azure-search-dotnet-samples](https://github.com/Azure-Samples/azure-search-dotnet-samples).
1. Vyberte **klonovat nebo stáhnout** a vaší privátní místní kopie úložiště.
1. Otevřít Visual Studio a nainstalovat balíček Microsoft Azure Search NuGet, pokud ještě není nainstalovaný. V **nástroje** nabídce vyberte možnost **Správce balíčků NuGet** a potom **spravovat balíčky NuGet pro řešení...** . Vyberte **Procházet** kartě pak do vyhledávacího pole zadejte "Azure Search". Nainstalujte **Microsoft.Azure.Search** až se zobrazí v seznamu (verze 9.0.1, nebo novější). Budete muset klikněte na tlačítko prostřednictvím zobrazení dalších dialogových oken pro dokončení instalace.

    ![Pomocí nástroje NuGet pro přidání knihoven Azure](./media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png)

1. Pomocí sady Visual Studio, přejděte do svého místního úložiště a otevřete soubor řešení **AzureSearchMultipleDataSources.sln**.

## <a name="get-a-key-and-url"></a>Získejte klíč a adresy URL

K interakci s vaší službou Azure Search, budete potřebovat adresu URL služby a přístupový klíč. Vyhledávací služba se vytvoří s oběma, takže pokud jste do svého předplatného přidali službu Azure Search, získejte potřebné informace pomocí následujícího postupu:

1. [Přihlaste se k webu Azure portal](https://portal.azure.com/)a ve vyhledávací službě **přehled** stránce, získat adresu URL. Příkladem koncového bodu může být `https://mydemo.search.windows.net`.

1. V **nastavení** > **klíče**, získat klíč pro úplná práva správce na službu. Existují dva klíče zaměnitelné správce, v případě, že budete potřebovat k výměně jeden k dispozici zajišťuje nepřetržitý chod podniků. U požadavků můžete použít buď primární nebo sekundární klíč pro přidání, úpravy a odstraňování objektů.

![Získejte koncový bod a přístupový klíč rozhraní HTTP](media/search-fiddler/get-url-key.png "získat HTTP koncový bod a přístupový klíč")

Všechny požadavky vyžaduje klíč rozhraní api na každou požadavku odeslaného do vaší služby. Platný klíč vytváří vztah důvěryhodnosti, na základě požadavku na mezi aplikace odešle požadavek a službu, která ji zpracovává.

## <a name="prepare-sample-azure-cosmos-db-data"></a>Příprava ukázková data služby Azure Cosmos DB

Tato ukázka používá dvě malé sady dat, které popisují sedm fiktivní hotels. Jedna sada popisuje hotels sami a se načtou do databáze Azure Cosmos DB. Druhá sada obsahuje podrobnosti o místnosti hotelu a je k dispozici jako sedm samostatné soubory JSON k odeslání do úložiště objektů Blob v Azure.

1. [Přihlaste se k webu Azure portal](https://portal.azure.com)a potom přejděte na stránku přehled účtu Azure Cosmos DB.

1. Na panelu nabídek klikněte na tlačítko přidat kontejner. Zadejte "Vytvořit novou databázi" a použijte název **hotelu. místnosti db**. Zadejte **hotelu místnosti** pro název kolekce a **/HotelId** pro klíč oddílu. Klikněte na tlačítko **OK** k vytvoření databáze a kontejneru.

   ![Přidat kontejner Azure Cosmos DB](media/tutorial-multiple-data-sources/cosmos-add-container.png "přidat kontejner služby Azure Cosmos DB")

1. Přejděte do Průzkumníku dat Cosmos DB a vyberte **položky** element v rámci **hotels** kontejneru **hotelu. místnosti db** databáze. Pak klikněte na tlačítko **nahrát položky** na panelu příkazů.

   ![Nahrát do služby Azure Cosmos DB kolekce](media/tutorial-multiple-data-sources/cosmos-upload.png "nahrát do kolekce Cosmos DB")

1. Na panelu nahrávání, klikněte na tlačítko složky a potom přejděte k souboru **cosmosdb/HotelsDataSubset_CosmosDb.json** ve složce projektu. Klikněte na tlačítko **OK** spustit nahrávání.

   ![Vybrat soubor k nahrání](media/tutorial-multiple-data-sources/cosmos-upload2.png "vybrat soubor k nahrání")

1. Pomocí tlačítka Aktualizovat aktualizujte zobrazení položek v kolekci hotels. Měli byste vidět sedm nových databáze uvedené dokumenty.

## <a name="prepare-sample-blob-data"></a>Příprava ukázková data objektů blob

1. [Přihlaste se k webu Azure portal](https://portal.azure.com), přejděte do svého účtu úložiště Azure, klikněte na tlačítko **objekty BLOB**a potom klikněte na tlačítko **+ kontejner**.

1. [Vytvořte kontejner objektů blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) s názvem **hotelu místnosti** k uložení ukázkové soubory JSON místnosti hotelu. Můžete nastavit úroveň veřejného přístupu k některému z jeho platných hodnot.

   ![Vytvořte kontejner objektů blob](media/tutorial-multiple-data-sources/blob-add-container.png "vytvořte kontejner objektů blob")

1. Po vytvoření kontejneru ho otevřete a vyberte **nahrát** na panelu příkazů.

   ![Nahrát na panelu příkazů](media/search-semi-structured-data/upload-command-bar.png "nahrát na panelu příkazů")

1. Přejděte do složky obsahující ukázkové soubory. Vyberte všechny z nich a pak klikněte na tlačítko **nahrát**.

   ![Nahrání souborů](media/tutorial-multiple-data-sources/blob-upload.png "nahrání souborů")

Po dokončení nahrávání soubory měly zobrazit v seznamu pro datový kontejner.

## <a name="set-up-connections"></a>Nastavení připojení

Informace o připojení pro vyhledávací služby a zdroje dat je zadán v **appsettings.json** souboru v řešení. 

1. V sadě Visual Studio, otevřete **AzureSearchMultipleDataSources.sln** souboru.

1. V Průzkumníku řešení, upravit **appsettings.json** souboru.  

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

První dvě položky pomocí kláves adresy URL a správce pro vaši službu Azure Search. Zadaný koncový bod `https://mydemo.search.windows.net`, například je název služby k poskytování `mydemo`.

Další položky zadejte názvy účtu a informace o připojovacím řetězci pro úložiště objektů Blob v Azure a zdroje dat služby Azure Cosmos DB.

### <a name="identify-the-document-key"></a>Identifikujte klíč dokumentu

Ve službě Azure Search pole klíče jednoznačně identifikuje každý dokument v indexu. Každý index vyhledávání musí mít přesně jeden klíčové pole typu `Edm.String`. Toto pole klíče musí být k dispozici pro každý dokument ve zdroji dat, který je přidán do indexu. (Ve skutečnosti je jediné povinné pole.)

Názvy při indexování dat z různých zdrojů dat, každá hodnota klíčové zdroje dat musí být namapovaný na stejné pole klíče v kombinované indexu. Často vyžaduje některé počáteční plánování k identifikaci smysluplné dokumentu klíče pro index a ujistěte se, že existuje ve službě jednotlivé datové zdroje.

Indexerů Azure Search můžete použít mapování polí pro přejmenování a dokonce i během procesu indexování u vydavatelských datová pole tak, aby zdrojová data mohou být přesměrováni na správné indexu pole.

Například v cosmos DB ukázková data, se nazývá identifikátor hotelu **HotelId**. Ale v souborech JSON blob hotelu místnosti, má název hotelu identifikátor **Id**. Tento program zpracovává pomocí mapování **Id** pole z přes bloby až po **HotelId** klíčové pole v indexu.

> [!NOTE]
> Ve většině případů klíče pro automaticky generované dokumentů, jako jsou ty několik indexerů vytvořených ve výchozím nastavení Nedovolte, aby byly klíče dobré dokumentů pro kombinované indexy. Obecně použijte smysluplné, jedinečnou hodnotu klíče, který již existuje v nebo můžete snadno přidat ke zdrojům dat.

## <a name="understand-the-code"></a>Vysvětlení kódu

Po nastavení a konfigurace dat jsou na místě, ukázkový program v **AzureSearchMultipleDataSources.sln** měli být připravení sestavit a spustit.

Tento jednoduchý C#/konzolové aplikace .NET provádí následující úlohy:
* Vytvoří nový index Azure Search na základě struktury dat C# hotelu třídy (který také odkazuje na adresu a místnosti třídy).
* Vytvoří zdroj dat služby Azure Cosmos DB a indexer, který se mapuje na pole indexu dat Azure Cosmos DB.
* Spuštění indexeru služby cosmos DB k načtení dat hotelu.
* Vytvoří z úložiště objektů Blob v Azure datového zdroje a indexer, který mapuje data objektů JSOn Blob do indexu pole.
* Spuštění indexeru úložiště objektů blob v Azure k načtení dat místnosti.

 Před spuštěním programu, využijte prostudování kódu a definic indexu a indexeru pro tuto ukázku. Důležitý kód je ve dvou souborech:

  + **Hotel.cs** obsahuje schéma definující index
  + **Soubor program.cs** obsahuje funkce, které vytvoření indexu Azure Search, zdroje dat a indexerů a načte kombinované výsledky do indexu.

### <a name="define-the-index"></a>Definování indexu

Tento ukázkový program používá k definování a vytvoření indexu Azure Search .NET SDK. Využívá [FieldBuilder](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.fieldbuilder) k vygenerování struktury indexu z C# třída dat modelu.

Datový model je definován třídou hotelu, která taky obsahuje odkazy na třídy adresu a místa. FieldBuilder při procházení k podrobnostem přes několik definic třídy k vytvoření komplexní datové struktury pro index. Značky metadat se používají k definování atributy každého pole, například jestli je prohledávatelná nebo řazení.

Následující fragmenty kódu z **Hotel.cs** soubor zobrazit, jak lze zadat jedno pole a odkaz na jiné třídy modelu data.

```csharp
. . . 
[IsSearchable, IsFilterable, IsSortable]
public string HotelName { get; set; }
. . .
public Room[] Rooms { get; set; }
. . .
```

V **Program.cs** soubor indexu je definována názvem a kolekci polí generovaných `FieldBuilder.BuildForType<Hotel>()` metoda a pak vytvořit následujícím způsobem:

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

### <a name="create-azure-cosmos-db-data-source-and-indexer"></a>Vytvoření zdroje dat služby Azure Cosmos DB a indexeru

Další hlavní program obsahuje logiku pro vytvoření zdroje dat služby Azure Cosmos DB pro hotels data.

Nejprve zřetězí název databáze Azure Cosmos DB na připojovací řetězec. Potom definuje objekt zdroje dat, včetně nastavení specifické pro službu Azure Cosmos DB zdroje, jako je například vlastnost [useChangeDetection].

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

Po vytvoření zdroje dat program nastaví služby Azure Cosmos DB indexer s názvem **hotelu místnosti cosmos indexeru**.

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
Program se odstraní všechny existující indexery se stejným názvem, před vytvořením nového, v případě, že chcete spustit tento příklad více než jednou.

Tento příklad definuje plán indexeru, tak, aby se spustí jednou denně. Vlastnost schedule můžete odebrat z tohoto volání, pokud už nechcete indexeru pro automatické spouštění v budoucnu.

### <a name="index-azure-cosmos-db-data"></a>Data indexu služby Azure Cosmos DB

Po vytvoření zdroje dat a indexeru je kód, který spouští indexeru (BRIEF):

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

Tento příklad zahrnuje bloku try-catch – jednoduché podávat zprávy o chybách, které mohou nastat během provádění.

Po spuštění indexeru služby Azure Cosmos DB bude obsahovat index vyhledávání úplnou sadu ukázkové dokumenty hotelu. Pole místnosti pro každý hotelu ale bude prázdné pole, protože zdroj dat služby Azure Cosmos DB obsažené žádné podrobnosti o místnosti. V dalším kroku program bude o přijetí změn z úložiště objektů Blob k načítání a slučování dat místnosti.

### <a name="create-blob-storage-data-source-and-indexer"></a>Vytvořit zdroj objektu Blob úložiště dat a indexeru

Pro získání podrobností o místnosti program nejprve nastaví zdroj dat objektu Blob úložiště tak, aby odkazují na sadu jednotlivé soubory objektů blob JSON.

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

Po vytvoření zdroje dat program nastaví indexeru blob s názvem **hotelu místnosti blob-indexer**.

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

Objekty BLOB JSON obsahují klíčové pole s názvem **Id** místo **HotelId**. Tento kód použije `FieldMapping` třídy říct indexeru pro přesměrování **Id** hodnota pro pole **HotelId** klíč dokumentu v indexu.

Indexery úložiště objektů BLOB můžete použít parametry, které identifikují analýzy režimu, který se má použít. Režim parsování se liší pro objekty BLOB, které představují jeden dokument, nebo více dokumentů do stejného objektu blob. V tomto příkladu každý objekt blob představuje jeden index dokumentu, takže tento kód použije `IndexingParameters.ParseJson()` parametru.

Další informace o analýze parametrů pro objekty BLOB JSON indexeru najdete v tématu [objektů BLOB Index JSON](search-howto-index-json-blobs.md). Další informace o zadávání těchto parametrů pomocí sady .NET SDK najdete v tématu [IndexerParametersExtension](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexingparametersextensions) třídy.

Program se odstraní všechny existující indexery se stejným názvem, před vytvořením nového, v případě, že chcete spustit tento příklad více než jednou.

Tento příklad definuje plán indexeru, tak, aby se spustí jednou denně. Vlastnost schedule můžete odebrat z tohoto volání, pokud už nechcete indexeru pro automatické spouštění v budoucnu.

### <a name="index-blob-data"></a>Index dat objektů blob

Po vytvoření zdroje dat úložiště objektů Blob a indexeru kód spuštění indexeru je jednoduchý:

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

Protože index je již vyplněný hotelu data z databáze Azure Cosmos DB, indexeru blob aktualizuje existující dokumenty v indexu a přidá podrobnosti místnosti.

> [!NOTE]
> Pokud mají stejné pole neklíčovým ve zdrojích dat i dat v rámci těchto polí se neshoduje, index bude obsahovat hodnoty z toho indexer posledního spuštění. V našem příkladu obsahovat oba zdroje dat **HotelName** pole. Pokud z nějakého důvodu data v toto pole je jiný pro dokumenty se stejnou hodnotou klíče, pak bude **HotelName** data ze zdroje dat, které se indexovat jako poslední budou hodnotu uloženou v indexu.

## <a name="search-your-json-files"></a>Prohledávání souborů JSON

Můžete prozkoumat mají údaj vyplněný vyhledávací index po spuštění programu pomocí [ **Průzkumníka služby Search** ](search-explorer.md) na portálu.

Na webu Azure portal otevřete službu search **přehled** stránky a najděte **hotelu místnosti sample** indexu v **indexy** seznamu.

  ![Seznam indexů Azure Search](media/tutorial-multiple-data-sources/index-list.png "indexů seznamu Azure Search")

Klikněte na hotelu místnosti ukázkového indexu v seznamu. Zobrazí se Průzkumník služby Search rozhraní pro index. Zadejte dotaz pro období jako "Luxusní". Měli byste vidět nejméně jeden dokument ve výsledcích a tohoto dokumentu by se zobrazit seznam objektů místnosti v jeho poli místnosti.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Nejrychlejším způsobem, jak po kurzu všechno uklidit, je odstranit skupinu prostředků, která obsahuje službu Azure Search. Odstraněním skupiny prostředků teď můžete trvale odstranit všechno, co se v ní nachází. Na portálu název skupiny prostředků je na stránce Přehled služby Azure Search.

## <a name="next-steps"></a>Další postup

Existuje několik přístupů a více možností pro indexování objektů BLOB JSON. Pokud vaše zdrojová data obsahuje obsah JSON, můžete zkontrolovat tyto varianty, abyste viděli, jak fungují nejlépe pro váš scénář.

> [!div class="nextstepaction"]
> [Jak indexovat objektů BLOB JSON pomocí indexeru Azure Search Blob](search-howto-index-json-blobs.md)

Můžete chtít rozšířit strukturovaných index data z jednoho zdroje dat cognitively bohatších možností daty z nestrukturovaných objektů BLOB nebo fulltextově obsah. V následujícím kurzu ukazuje, jak pomocí služeb Cognitive Services společně s Azure Search pomocí .NET SDK.

> [!div class="nextstepaction"]
> [Volání rozhraní API služeb Cognitive Services v Azure Search indexování kanálu](cognitive-search-tutorial-blob-dotnet.md)
