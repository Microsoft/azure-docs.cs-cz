---
title: 'Tutorial: Index data in C# from Azure SQL databases'
titleSuffix: Azure Cognitive Search
description: In this C# tutorial, connect to Azure SQL database, extract searchable data, and load it into an Azure Cognitive Search index.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 36215403f99cc86ab4fb111ce95a6b3190063d7b
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406716"
---
# <a name="tutorial-import-azure-sql-database-in-c-using-azure-cognitive-search-indexers"></a>Tutorial: Import Azure SQL database in C# using Azure Cognitive Search indexers

Learn how to configure an indexer for extracting searchable data from a sample Azure SQL database. [Indexers](search-indexer-overview.md) are a component of Azure Cognitive Search that crawl external data sources, populating a [search index](search-what-is-an-index.md) with content. Of all indexers, the indexer for Azure SQL Database is the most widely used. 

Znalost konfigurace indexeru je užitečná, protože zjednodušuje množství kódu, který musíte psát a spravovat. Místo přípravy a nabízení datové sady JSON odpovídající schématu můžete ke zdroji dat připojit indexer a nechat ho extrahovat data a vložit je do indexu. Volitelně také můžete indexer spouštět podle plánu opakování, aby přebíral změny v základním zdroji.

In this tutorial, use the [Azure Cognitive Search .NET client libraries](https://aka.ms/search-sdk) and a .NET Core console application to perform the following tasks:

> [!div class="checklist"]
> * Přidání informací o vyhledávací službě do nastavení aplikace
> * Příprava externích datových sad v databázi Azure SQL 
> * Kontrola definic indexu a indexeru ve vzorovém kódu
> * Spuštění kódu indexeru pro import dat
> * Prohledání indexu
> * Zobrazení konfigurace indexeru na portálu

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Předpoklady

The following services, tools, and data are used in this quickstart. 

[Create an Azure Cognitive Search service](search-create-service-portal.md) or [find an existing service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) under your current subscription. You can use a free service for this tutorial.

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) stores the external data source used by an indexer. Ukázkové řešení obsahuje datový soubor SQL pro vytvoření tabulky. Steps for creating the service and database are provided in this tutorial.

[Visual Studio 2017](https://visualstudio.microsoft.com/downloads/), any edition, can be used to run the sample solution. Sample code and instructions were tested on the free Community edition.

[Azure-Samples/search-dotnet-getting-started](https://github.com/Azure-Samples/search-dotnet-getting-started) provides the sample solution, located in the Azure samples GitHub repository. Download and extract the solution. By default, solutions are read-only. Right-click the solution and clear the read-only attribute so that you can modify files.

> [!Note]
> If you are using the free Azure Cognitive Search service, you are limited to three indexes, three indexers, and three data sources. V tomto kurzu se vytváří od každého jeden. Ujistěte se, že ve své službě máte místo pro příjem nových prostředků.

## <a name="get-a-key-and-url"></a>Get a key and URL

Volání REST vyžadují pro každý požadavek adresu URL služby a přístupový klíč. A search service is created with both, so if you added Azure Cognitive Search to your subscription, follow these steps to get the necessary information:

1. [Sign in to the Azure portal](https://portal.azure.com/), and in your search service **Overview** page, get the URL. Příkladem koncového bodu může být `https://mydemo.search.windows.net`.

1. In **Settings** > **Keys**, get an admin key for full rights on the service. There are two interchangeable admin keys, provided for business continuity in case you need to roll one over. You can use either the primary or secondary key on requests for adding, modifying, and deleting objects.

![Get an HTTP endpoint and access key](media/search-get-started-postman/get-url-key.png "Get an HTTP endpoint and access key")

All requests require an api-key on every request sent to your service. Platný klíč vytváří na základě žádosti vztah důvěryhodnosti mezi aplikací, která žádost odeslala, a službou, která ji zpracovává.

## <a name="set-up-connections"></a>Nastavení připojení
Informace o připojení k požadovaným službám se zadává do souboru **appsettings.json** v řešení. 

1. In Visual Studio, open the **DotNetHowToIndexers.sln** file.

1. In Solution Explorer, open **appsettings.json** so that you can populate each setting.  

The first two entries you can fill in right now, using the URL and admin keys for your Azure Cognitive Search service. Given an endpoint of `https://mydemo.search.windows.net`, the service name to provide is `mydemo`.

```json
{
  "SearchServiceName": "Put your search service name here",
  "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
  "AzureSqlConnectionString": "Put your Azure SQL database connection string here",
}
```

The last entry requires an existing database. You'll create it in the next step.

## <a name="prepare-sample-data"></a>Prepare sample data

V tomto kroku vytvoříte externí zdroj dat, který může indexer procházet. Datovou sadu ve službě Azure SQL Database můžete vytvořit pomocí webu Azure Portal a souboru *hotels.sql* z ukázky. Azure Cognitive Search consumes flattened rowsets, such as one generated from a view or query. Soubor SQL v ukázkovém řešení vytvoří a naplní jednu tabulku.

Následující cvičení předpokládá, že neexistuje žádný server ani databáze, a v kroku 2 poskytuje pokyny k jejich vytvoření. Pokud máte existující prostředek, volitelně můžete tabulku hotels přidat do něj podle pokynů od kroku 4 dále.

1. [Sign in to the Azure portal](https://portal.azure.com/). 

2. Find or create an **Azure SQL Database** to create a database, server, and resource group. Můžete použít výchozí hodnoty a nejnižší cenovou úroveň. Jednou z výhod vytvoření serveru je, že můžete zadat uživatelské jméno a heslo správce, které jsou potřeba k vytvoření a načtení tabulek v pozdějším kroku.

   ![Stránka Nová databáze](./media/search-indexer-tutorial/indexer-new-sqldb.png)

3. Kliknutím na **Vytvořit** nasaďte nový server a databázi. Počkejte na nasazení serveru a databáze.

4. Otevřete stránku služby SQL Database pro vaši novou databázi, pokud ještě není otevřená. Název prostředku by měl být *Databáze SQL*, a ne *SQL Server*.

   ![Stránka databáze SQL](./media/search-indexer-tutorial/hotels-db.png)

4. On the navigation pane, click **Query editor (preview)** .

5. Klikněte na **Přihlášení** a zadejte uživatelské jméno a heslo správce serveru.

6. Klikněte na **Otevřít dotaz** a přejděte do umístění souboru *hotels.sql*. 

7. Vyberte tento soubor a klikněte na **Otevřít**. Váš skript by měl vypadat jako na následujícím snímku obrazovky:

   ![Skript SQL](./media/search-indexer-tutorial/sql-script.png)

8. Kliknutím na **Spustit** dotaz spusťte. V podokně výsledků by se na 3 řádcích měla zobrazit zpráva o úspěšném provedení dotazu.

9. Pokud chcete vrátit sadu řádků z této tabulky, můžete jako krok ověření spustit následující dotaz:

    ```sql
    SELECT HotelId, HotelName, Tags FROM Hotels
    ```
    Typický dotaz `SELECT * FROM Hotels` v editoru dotazů nefunguje. Ukázková data obsahují v poli Location (Umístění) zeměpisné souřadnice, které editor v tuto chvíli nezpracovává. Seznam dalších sloupců, které můžete dotazovat, získáte spuštěním tohoto příkazu: `SELECT * FROM sys.columns WHERE object_id = OBJECT_ID('dbo.Hotels')`

10. Když teď máte externí datovou sadu, zkopírujte připojovací řetězec ADO.NET pro databázi. Na stránce služby SQL Database pro vaši databázi přejděte do **Nastavení** > **Připojovací řetězce** a zkopírujte připojovací řetězec ADO.NET.
 
    Připojovací řetězec ADO.NET vypadá podobně jako v následujícím příkladu, ale je upravený, aby používal platný název databáze a platné uživatelské jméno a heslo.

    ```sql
    Server=tcp:hotels-db.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
    ```
11. Vložte připojovací řetězec do AzureSqlConnectionString jako třetí položku v souboru **appsettings.json** v sadě Visual Studio.

    ```json
    {
      "SearchServiceName": "<placeholder-Azure-Search-service-name>",
      "SearchServiceAdminApiKey": "<placeholder-admin-key-for-Azure-Search>",
      "AzureSqlConnectionString": "Server=tcp:hotels-db.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security  Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;",
    }
    ```

## <a name="understand-the-code"></a>Vysvětlení kódu

Once the data and configuration settings are in place, the sample program in **DotNetHowToIndexers.sln** is ready to build and run. Než to provedete, věnujte chvíli prostudování definic indexu a indexeru pro tuto ukázku. Důležitý kód je ve dvou souborech:

  + **hotel.cs**, který obsahuje schéma definující index
  + **Program.cs**, který obsahuje funkce pro vytváření a správu struktur ve vaší službě

### <a name="in-hotelcs"></a>Soubor hotel.cs

Schéma indexu definuje kolekci polí, včetně atributů určujících povolené operace, například jestli je pole fulltextově prohledávatelné, filtrovatelné nebo seřaditelné, jak je znázorněno v následující definici pole pro HotelName. 

```csharp
. . . 
[IsSearchable, IsFilterable, IsSortable]
public string HotelName { get; set; }
. . .
```

Schéma může obsahovat také další elementy, včetně profilů vyhodnocování pro zvýšení skóre vyhledávání, vlastních analyzátorů a dalších konstrukcí. Pro naše účely je však schéma definováno řídce a skládá se pouze z polí, která jsou v ukázkových datových sadách.

V tomto kurzu indexer přetahuje data z jednoho zdroje dat. In practice, you can attach multiple indexers to the same index, creating a consolidated searchable index from multiple data sources. V závislosti na tom, kde potřebujete flexibilitu, můžete použít stejný pár indexu a indexeru a měnit pouze zdroje dat nebo jeden index s různými kombinacemi indexeru a zdroje dat.

### <a name="in-programcs"></a>Soubor Program.cs

The main program includes logic for creating a client, an index, a data source, and an indexer. Kód předpokládá, že byste tento program mohli spustit několikrát, a proto kontroluje a odstraňuje existující prostředky se stejným názvem.

The data source object is configured with settings that are specific to Azure SQL database resources, including [incremental indexing](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows) for leveraging the built-in [change detection features](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) of Azure SQL. The demo hotels database in Azure SQL has a "soft delete" column named **IsDeleted**. When this column is set to true in the database, the indexer removes the corresponding document from the Azure Cognitive Search index.

  ```csharp
  Console.WriteLine("Creating data source...");

  DataSource dataSource = DataSource.AzureSql(
      name: "azure-sql",
      sqlConnectionString: configuration["AzureSQLConnectionString"],
      tableOrViewName: "hotels",
      deletionDetectionPolicy: new SoftDeleteColumnDeletionDetectionPolicy(
          softDeleteColumnName: "IsDeleted",
          softDeleteMarkerValue: "true"));
  dataSource.DataChangeDetectionPolicy = new SqlIntegratedChangeTrackingPolicy();

  searchService.DataSources.CreateOrUpdateAsync(dataSource).Wait();
  ```

An indexer object is platform-agnostic, where  configuration, scheduling, and invocation are the same regardless of the source. This example indexer includes a schedule, a reset option that clears indexer history, and calls a method to create and run the indexer immediately.

  ```csharp
  Console.WriteLine("Creating Azure SQL indexer...");
  Indexer indexer = new Indexer(
      name: "azure-sql-indexer",
      dataSourceName: dataSource.Name,
      targetIndexName: index.Name,
      schedule: new IndexingSchedule(TimeSpan.FromDays(1)));
  // Indexers contain metadata about how much they have already indexed
  // If we already ran the sample, the indexer will remember that it already
  // indexed the sample data and not run again
  // To avoid this, reset the indexer if it exists
  exists = await searchService.Indexers.ExistsAsync(indexer.Name);
  if (exists)
  {
      await searchService.Indexers.ResetAsync(indexer.Name);
  }

  await searchService.Indexers.CreateOrUpdateAsync(indexer);

  // We created the indexer with a schedule, but we also
  // want to run it immediately
  Console.WriteLine("Running Azure SQL indexer...");

  try
  {
      await searchService.Indexers.RunAsync(indexer.Name);
  }
  catch (CloudException e) when (e.Response.StatusCode == (HttpStatusCode)429)
  {
      Console.WriteLine("Failed to run indexer: {0}", e.Response.Content);
  }
  ```



## <a name="run-the-indexer"></a>Spuštění indexeru

V tomto kroku program zkompilujete a spustíte. 

1. V Průzkumníku řešení klikněte pravým tlačítkem na **DotNetHowToIndexers** a vyberte **Sestavit**.
2. Znovu klikněte pravým tlačítkem na **DotNetHowToIndexers** a pak vyberte **Ladění** > **Zahájit novou instanci**.

Program se spustí v režimu ladění. V okně konzoly se bude hlásit stav jednotlivých operací.

  ![Skript SQL](./media/search-indexer-tutorial/console-output.png)

Váš kód je spuštěný místně v sadě Visual Studio a připojuje se k vaší vyhledávací službě v Azure, která se pak pomocí připojovacího řetězce připojí ke službě Azure SQL Database a načte datovou sadu. Kvůli takovému množství operací existuje několik potenciálních bodů selhání, ale pokud obdržíte chybu, zkontrolujte nejprve následující podmínky:

+ Zadané informace o připojení k vyhledávací službě se omezují na název služby v tomto kurzu. Pokud jste zadali úplnou adresu URL, operace se zastaví při vytváření indexu s chybou značící selhání připojení.

+ Informace o připojení k databázi v souboru **appsettings.json**. Měl by to být připojovací řetězec ADO.NET získaný z portálu a upravený tak, aby obsahoval platné uživatelské jméno a heslo pro vaši databázi. Uživatelský účet musí mít oprávnění k načtení dat.

+ Omezení prostředků. Recall that the Free tier has limits of 3 indexes, indexers, and data sources. Služba, která dosáhne maximálního omezení, nemůže vytvářet nové objekty.

## <a name="search-the-index"></a>Prohledání indexu 

Na webu Azure Portal na stránce Přehled vyhledávací služby klikněte v horní části na **Průzkumník služby Search** a odešlete několik dotazů na nový index.

1. V horní části klikněte na **Změnit index** a vyberte index *hotels*.

2. Kliknutím na tlačítko **Hledat** proveďte prázdné vyhledávání. 

   Vrátí se tři položky ve vašem indexu jako dokumenty JSON. Průzkumník služby Search vrací dokumenty ve formátu JSON, abyste mohli zobrazit celou jejich strukturu.

3. Dále zadejte hledaný řetězec `search=river&$count=true`. 

   Tento dotaz vyvolá fulltextové vyhledávání výrazu `river` a výsledek bude obsahovat počet odpovídajících dokumentů. Vrácení počtu odpovídajících dokumentů je užitečné ve scénářích testování, když máte velký index s tisíci nebo miliony dokumentů. V tomto případě dotazu odpovídá pouze jeden dokument.

4. Nakonec zadejte hledaný řetězec, který omezí pole ve výstupu JSON pouze na ta, která vás zajímají: `search=river&$count=true&$select=hotelId, baseRate, description`. 

   Odpověď na dotaz je omezená na vybraná pole, což ve výsledku znamená stručnější výstup.

## <a name="view-indexer-configuration"></a>Zobrazení konfigurace indexeru

Všechny indexery, včetně toho, který jste právě vytvořili prostřednictvím kódu programu, jsou uvedené na portálu. Můžete otevřít definici indexeru a zobrazit jeho zdroj dat nebo nakonfigurovat plán aktualizace pro přebírání nových a změněných řádků.

1. [Sign in to the Azure portal](https://portal.azure.com/), and in your search service **Overview** page, click the links for **Indexes**, **Indexers**, and **Data Sources**.
3. Select individual objects to view or modify configuration settings.

   ![Dlaždice Indexery a Zdroje dat](./media/search-indexer-tutorial/tiles-portal.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

The fastest way to clean up after a tutorial is by deleting the resource group containing the Azure Cognitive Search service. Odstraněním skupiny prostředků teď můžete trvale odstranit všechno, co se v ní nachází. In the portal, the resource group name is on the Overview page of Azure Cognitive Search service.

## <a name="next-steps"></a>Další kroky

You can attach AI enrichment algorithms to an indexer pipeline. Jako další krok pokračujte následujícím kurzem.

> [!div class="nextstepaction"]
> [Indexování dokumentů ve službě Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)