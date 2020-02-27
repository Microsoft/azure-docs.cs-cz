---
title: 'Kurz: indexování dat C# z databází SQL Azure'
titleSuffix: Azure Cognitive Search
description: V tomto C# kurzu se připojíte ke službě Azure SQL Database, extrahujete hledaná data a nahrajete ji do indexu služby Azure kognitivní hledání.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/26/2020
ms.openlocfilehash: 978587b68e719b79db31ff25adaf2b38d2235095
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77650043"
---
# <a name="tutorial-index-azure-sql-data-in-c-using-azure-cognitive-search-indexers"></a>Kurz: indexování dat SQL Azure C# pomocí indexerů služby Azure kognitivní hledání

Pomocí C#nástroje nakonfigurujte [indexer](search-indexer-overview.md) , který extrahuje hledaná data ze služby Azure SQL Database a pošle je do indexu vyhledávání. Tento kurz používá [klientské knihovny Azure kognitivní hledání .NET](https://aka.ms/search-sdk) a konzolovou aplikaci .NET Core k provádění následujících úloh:

> [!div class="checklist"]
> * Vytvoření zdroje dat, který se připojuje k Azure SQL Database
> * Konfigurace indexeru
> * Spuštění indexeru pro načtení dat do indexu
> * Dotazování indexu jako ověřovacího kroku

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Předpoklady

+ [Azure SQL Database](https://azure.microsoft.com/services/sql-database/)
+ [Visual Studio](https://visualstudio.microsoft.com/downloads/)
+ [Vytvoření](search-create-service-portal.md) nebo [vyhledání existující vyhledávací služby](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> Pro tento kurz můžete použít bezplatnou službu. Bezplatná vyhledávací služba omezuje tři indexy, tři indexery a tři zdroje dat. V tomto kurzu se vytváří od každého jeden. Než začnete, ujistěte se, že máte ve své službě místo pro přijímání nových prostředků.

## <a name="download-source-code"></a>Stáhnout zdrojový kód

Zdrojový kód pro tento kurz je ve složce [DotNetHowToIndexer](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) v úložišti GitHub [Azure-Samples/Search-dotnet-getzačínáme-Started](https://github.com/Azure-Samples/search-dotnet-getting-started) .

## <a name="get-a-key-and-url"></a>Získat klíč a adresu URL

Volání rozhraní API vyžadují adresu URL služby a přístupový klíč. Vyhledávací služba se vytvoří s oběma, takže pokud jste do svého předplatného přidali Azure Kognitivní hledání, postupujte podle těchto kroků a získejte potřebné informace:

1. [Přihlaste se k Azure Portal](https://portal.azure.com/)a na stránce **Přehled** vyhledávací služby Získejte adresu URL. Příkladem koncového bodu může být `https://mydemo.search.windows.net`.

1. V části **nastavení** > **klíče**Získejte klíč správce s úplnými právy k této službě. Existují dva zaměnitelné klíče správce poskytované pro zajištění kontinuity podnikových služeb pro případ, že byste museli nějakou dobu navrátit. V žádostech o přidání, úpravu a odstranění objektů můžete použít primární nebo sekundární klíč.

   ![Získání koncového bodu HTTP a přístupového klíče](media/search-get-started-postman/get-url-key.png "Získání koncového bodu HTTP a přístupového klíče")

## <a name="set-up-connections"></a>Nastavení připojení

1. Spusťte Visual Studio a otevřete **DotNetHowToIndexers. sln**.

1. V Průzkumník řešení otevřete **appSettings. JSON** a nahraďte zástupné hodnoty informacemi o připojení k vyhledávací službě. Pokud je úplná adresa URL "https://my-demo-service.search.windows.net", název služby, který se má poskytnout, je "moje ukázková služba".

    ```json
    {
      "SearchServiceName": "Put your search service name here",
      "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
      "AzureSqlConnectionString": "Put your Azure SQL database connection string here",
    }
    ```

Poslední položka vyžaduje existující databázi. Vytvoříte ho v dalším kroku.

## <a name="prepare-sample-data"></a>Příprava ukázkových dat

V tomto kroku vytvoříte externí zdroj dat na Azure SQL Database, který může indexer Procházet. Datovou sadu ve službě Azure SQL Database můžete vytvořit pomocí webu Azure Portal a souboru *hotels.sql* z ukázky. Azure Kognitivní hledání spotřebovává sloučené sady řádků, jako je například jedna vygenerovaná ze zobrazení nebo dotazu. Soubor SQL v ukázkovém řešení vytvoří a naplní jednu tabulku.

Pokud máte existující prostředek Azure SQL Database, můžete do něj přidat tabulku hotelů, počínaje krokem 4.

1. [Přihlaste se k Azure Portal](https://portal.azure.com/).

1. Najděte nebo vytvořte **SQL Database**. Můžete použít výchozí hodnoty a nejnižší cenovou úroveň. Jednou z výhod vytvoření serveru je, že můžete zadat uživatelské jméno a heslo správce, které jsou potřeba k vytvoření a načtení tabulek v pozdějším kroku.

   ![Nová stránka databáze](./media/search-indexer-tutorial/indexer-new-sqldb.png "Stránka Nová databáze")

1. Kliknutím na tlačítko **zkontrolovat + vytvořit** nasaďte nový server a databázi. Počkejte na nasazení serveru a databáze.

1. V navigačním podokně klikněte na **Editor dotazů (Preview)** a zadejte uživatelské jméno a heslo správce serveru. 

   Pokud přístup odepřete, zkopírujte IP adresu klienta z chybové zprávy a potom klikněte na odkaz **nastavit bránu firewall serveru** a přidejte pravidlo, které umožní přístup z klientského počítače, a to pomocí klientské IP adresy pro daný rozsah. Než se pravidlo projeví, může to trvat několik minut.

1. V editoru dotazů klikněte na **Otevřít dotaz** a přejděte do umístění souboru *hotely. SQL* na místním počítači. 

1. Vyberte tento soubor a klikněte na **Otevřít**. Váš skript by měl vypadat jako na následujícím snímku obrazovky:

   ![Skript SQL](./media/search-indexer-tutorial/sql-script.png "Skript SQL")

1. Kliknutím na **Spustit** dotaz spusťte. V podokně výsledků by se na 3 řádcích měla zobrazit zpráva o úspěšném provedení dotazu.

1. Pokud chcete vrátit sadu řádků z této tabulky, můžete jako krok ověření spustit následující dotaz:

    ```sql
    SELECT * FROM Hotels
    ```

1. Zkopírujte připojovací řetězec ADO.NET pro databázi. V části **nastavení** > **připojovací řetězce**zkopírujte připojovací řetězec ADO.NET podobně jako v následujícím příkladu.

    ```sql
    Server=tcp:{your_dbname}.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
    ```

1. Vložte připojovací řetězec do AzureSqlConnectionString jako třetí položku v souboru **appsettings.json** v sadě Visual Studio.

    ```json
    {
      "SearchServiceName": "<placeholder-Azure-Search-service-name>",
      "SearchServiceAdminApiKey": "<placeholder-admin-key-for-Azure-Search>",
      "AzureSqlConnectionString": "Server=tcp:{your_dbname}.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;",
    }
    ```

1. Zadejte heslo do připojovacího řetězce v souboru **appSettings. JSON** . Databáze a uživatelská jména se zkopírují v připojovacím řetězci, ale heslo je potřeba zadat ručně.

## <a name="build-the-solution"></a>Sestavení řešení

Stisknutím klávesy F5 Sestavte řešení. Program se spustí v režimu ladění. V okně konzoly se bude hlásit stav jednotlivých operací.

   ![Výstup konzoly](./media/search-indexer-tutorial/console-output.png "Výstup konzoly")

Váš kód se spouští místně v sadě Visual Studio a připojuje se k vaší vyhledávací službě v Azure, která se zase připojuje k Azure SQL Database a načítá datovou sadu. U této řady operací existuje několik potenciálních bodů selhání. Pokud se zobrazí chyba, nejdřív ověřte následující podmínky:

+ Zadané informace o připojení k vyhledávací službě se omezují na název služby v tomto kurzu. Pokud jste zadali úplnou adresu URL, operace se zastaví při vytváření indexu s chybou značící selhání připojení.

+ Informace o připojení k databázi v souboru **appsettings.json**. Měl by to být připojovací řetězec ADO.NET získaný z portálu a upravený tak, aby obsahoval platné uživatelské jméno a heslo pro vaši databázi. Uživatelský účet musí mít oprávnění k načtení dat. IP adresa místního klienta musí mít povolený přístup.

+ Omezení prostředků. Vyvoláte si, že úroveň Free má omezení 3 indexy, indexerů a zdrojů dat. Služba, která dosáhne maximálního omezení, nemůže vytvářet nové objekty.

## <a name="check-results"></a>Výsledky kontroly

K ověření vytvoření objektu použijte Azure Portal a pak pomocí **Průzkumníka vyhledávání** Dotazujte index.

1. [Přihlaste se k Azure Portal](https://portal.azure.com/)a na stránce **Přehled** vyhledávací služby otevřete jednotlivé seznamy a ověřte, zda je objekt vytvořen. **Indexy**, **indexery**a **zdroje dat** budou mít "hotely", "Azure-SQL-indexer" a "Azure-SQL" v uvedeném pořadí.

   ![Dlaždice Indexery a Zdroje dat](./media/search-indexer-tutorial/tiles-portal.png)

1. Vyberte index hotelů. Na stránce hotely je jako první karta **Průzkumník vyhledávání** . 

1. Kliknutím na tlačítko **Hledat** vydejte prázdný dotaz. 

   Vrátí se tři položky ve vašem indexu jako dokumenty JSON. Průzkumník služby Search vrací dokumenty ve formátu JSON, abyste mohli zobrazit celou jejich strukturu.

   ![Dotazování indexu](./media/search-indexer-tutorial/portal-search.png "Dotazování indexu")
   
1. Dále zadejte hledaný řetězec `search=river&$count=true`. 

   Tento dotaz vyvolá fulltextové vyhledávání výrazu `river` a výsledek bude obsahovat počet odpovídajících dokumentů. Vrácení počtu odpovídajících dokumentů je užitečné ve scénářích testování, když máte velký index s tisíci nebo miliony dokumentů. V tomto případě dotazu odpovídá pouze jeden dokument.

1. Nakonec zadejte hledaný řetězec, který omezí pole ve výstupu JSON pouze na ta, která vás zajímají: `search=river&$count=true&$select=hotelId, baseRate, description`. 

   Odpověď na dotaz je omezená na vybraná pole, což ve výsledku znamená stručnější výstup.

## <a name="explore-the-code"></a>Prozkoumat kód

Teď, když rozumíte tomu, jak se vzorový kód vytvoří, se vrátíme do řešení, kde můžete kód zkontrolovat. Relevantní kód je ve dvou souborech:

  + **Hotel.cs**obsahující schéma, které definuje index
  + **Program.cs**, obsahující funkce pro vytváření a správu struktur ve vaší službě

### <a name="in-hotelcs"></a>Soubor hotel.cs

Schéma indexu definuje kolekci polí, včetně atributů určujících povolené operace, například jestli je pole fulltextově prohledávatelné, filtrovatelné nebo seřaditelné, jak je znázorněno v následující definici pole pro HotelName. 

```csharp
. . . 
[IsSearchable, IsFilterable, IsSortable]
public string HotelName { get; set; }
. . .
```

Schéma může obsahovat také další elementy, včetně profilů vyhodnocování pro zvýšení skóre vyhledávání, vlastních analyzátorů a dalších konstrukcí. Pro naše účely je však schéma definováno řídce a skládá se pouze z polí, která jsou v ukázkových datových sadách.

### <a name="in-programcs"></a>Soubor Program.cs

Hlavní program obsahuje logiku pro vytváření klienta, index, zdroj dat a indexer. Kód předpokládá, že byste tento program mohli spustit několikrát, a proto kontroluje a odstraňuje existující prostředky se stejným názvem.

Objekt zdroje dat je nakonfigurovaný s nastavením, která jsou specifická pro prostředky Azure SQL Database, včetně [částečného nebo přírůstkového indexování](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows) pro využití integrovaných [funkcí detekce změn](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) v Azure SQL. Ukázková databáze hotelů v Azure SQL má sloupec "obnovitelné odstranění" s názvem **IsDeleted**. Pokud je tento sloupec v databázi nastavený na hodnotu true, indexer odebere odpovídající dokument z indexu služby Azure Kognitivní hledání.

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

Objekt indexeru je Platform-nezávislá, kde konfigurace, plánování a volání jsou stejné bez ohledu na zdroj. Tento příklad indexeru zahrnuje plán, možnost resetu, která vymaže historii indexeru a volá metodu pro vytvoření a spuštění indexeru hned.

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

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud pracujete ve vlastním předplatném, je vhodné odebrat prostředky, které už nepotřebujete. Prostředky, které se na něm zbývá, můžou mít náklady na peníze. Prostředky můžete odstranit jednotlivě nebo odstranit skupinu prostředků, abyste odstranili celou sadu prostředků.

Prostředky můžete najít a spravovat na portálu pomocí odkazu všechny prostředky nebo skupiny prostředků v levém navigačním podokně.

Pokud používáte bezplatnou službu, pamatujte na to, že jste omezeni na tři indexy, indexery a zdroje dat. Jednotlivé položky na portálu můžete odstranit, aby zůstaly pod limitem.

## <a name="next-steps"></a>Další kroky

V Azure Kognitivní hledání jsou indexery k dispozici pro více zdrojů dat Azure. V dalším kroku Prozkoumejte indexer pro úložiště objektů BLOB v Azure.

> [!div class="nextstepaction"]
> [Indexování dokumentů ve službě Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)