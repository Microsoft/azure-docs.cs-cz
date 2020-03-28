---
title: 'Kurz: Indexování dat z databází Azure SQL v C # '
titleSuffix: Azure Cognitive Search
description: V tomto kurzu C# se připojte k databázi Azure SQL, extrahujte prohledávatelná data a načtěte je do indexu Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/28/2020
ms.openlocfilehash: 7660c89032ea3ef8371655b94b75c1f60603ee32
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "78193964"
---
# <a name="tutorial-use-c-to-index-data-from-sql-databases-in-azure-cognitive-search"></a>Kurz: Použití jazyka C# k indexování dat z databází SQL v Azure Cognitive Search

Nakonfigurujte [indexer](search-indexer-overview.md) pro extrahování prohledávatelných dat z databáze Azure SQL a odesílat je do indexu vyhledávání v Azure Cognitive Search. 

Tento kurz používá c# a [.NET SDK](https://aka.ms/search-sdk) k provedení následujících úkolů:

> [!div class="checklist"]
> * Vytvoření zdroje dat, který se připojí k Azure SQL Database
> * Vytvoření indexeru
> * Spuštění indexeru pro načtení dat do indexu
> * Dotaz na index jako krok ověření

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

+ [Azure SQL Database](https://azure.microsoft.com/services/sql-database/)
+ [Visual Studio](https://visualstudio.microsoft.com/downloads/)
+ [Vytvoření](search-create-service-portal.md) nebo [vyhledání existující vyhledávací služby](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> Můžete použít bezplatnou službu pro tento kurz. Bezplatná vyhledávací služba vás omezí na tři indexy, tři indexery a tři zdroje dat. V tomto kurzu se vytváří od každého jeden. Než začnete, ujistěte se, že máte ve službě prostor pro přijetí nových zdrojů.

## <a name="download-files"></a>Stažení souborů

Zdrojový kód pro tento kurz je ve složce [DotNetHowToIndexer](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) v [úložišti GitHub Azure-Samples/search-dotnet-getting-started.](https://github.com/Azure-Samples/search-dotnet-getting-started)

## <a name="1---create-services"></a>1 - Vytváření služeb

Tento kurz používá Azure Cognitive Search pro indexování a dotazy a Azure SQL Database jako externí zdroj dat. Pokud je to možné, vytvořte služby ve stejné oblasti a skupinu prostředků pro blízkost a možnosti správy. V praxi azure SQL database může být v libovolné oblasti.

### <a name="start-with-azure-sql-database"></a>Začněte s Databází Azure SQL

V tomto kroku vytvořte externí zdroj dat v Azure SQL Database, který může procházet indexer. K vytvoření datové sady v Azure SQL Database můžete použít portál Azure a soubor *hotels.sql* z ukázkového stahování. Azure Cognitive Search spotřebovává slogenerované sady řádků, jako je například jedna generovaná ze zobrazení nebo dotazu. Soubor SQL v ukázkovém řešení vytvoří a naplní jednu tabulku.

Pokud máte existující prostředek Azure SQL Database, můžete do něj přidat tabulku hotelů, počínaje krokem 4.

1. [Přihlaste se k portálu Azure](https://portal.azure.com/).

1. Vyhledání nebo vytvoření **databáze SQL**. Můžete použít výchozí hodnoty a nejnižší cenovou úroveň. Jednou z výhod vytvoření serveru je, že můžete zadat uživatelské jméno a heslo správce, které jsou potřeba k vytvoření a načtení tabulek v pozdějším kroku.

   ![Stránka Nová databáze](./media/search-indexer-tutorial/indexer-new-sqldb.png "Stránka Nová databáze")

1. Kliknutím na **Zkontrolovat + vytvořit** nasadit nový server a databázi. Počkejte na nasazení serveru a databáze.

1. V navigačním podokně klikněte na **Editor dotazů (preview)** a zadejte uživatelské jméno a heslo správce serveru. 

   Pokud je přístup odepřen, zkopírujte ip adresu klienta z chybové zprávy a klepnutím na odkaz **Nastavit serverovou bránu brány přidejte** pravidlo, které umožňuje přístup z klientského počítače, pomocí IP adresy klienta pro rozsah. Může trvat několik minut, než se pravidlo projeví.

1. V editoru dotazů klikněte na **Otevřít dotaz** a přejděte do umístění souboru *hotels.sql* v místním počítači. 

1. Vyberte tento soubor a klikněte na **Otevřít**. Váš skript by měl vypadat jako na následujícím snímku obrazovky:

   ![Skript SQL](./media/search-indexer-tutorial/sql-script.png "Skript SQL")

1. Kliknutím na **Spustit** dotaz spusťte. V podokně výsledků by se na 3 řádcích měla zobrazit zpráva o úspěšném provedení dotazu.

1. Pokud chcete vrátit sadu řádků z této tabulky, můžete jako krok ověření spustit následující dotaz:

    ```sql
    SELECT * FROM Hotels
    ```

1. Zkopírujte připojovací řetězec ADO.NET pro databázi. V části **Nastavení** > **připojovacích řetězců**zkopírujte připojovací řetězec ADO.NET podobně jako v níže uvedeném příkladu.

    ```sql
    Server=tcp:{your_dbname}.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
    ```

Tento připojovací řetězec budete potřebovat v dalším cvičení, kterým se vaše prostředí.

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

Další komponentou je Azure Cognitive Search, kterou můžete [vytvořit na portálu](search-create-service-portal.md). K dokončení tohoto návodu můžete použít úroveň Free. 

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Získání klíče api správce a adresy URL pro Azure Cognitive Search

Volání rozhraní API vyžadují adresu URL služby a přístupový klíč. Vyhledávací služba se vytvoří s oběma, takže pokud jste do předplatného přidali Azure Cognitive Search, postupujte podle následujících kroků a získejte potřebné informace:

1. [Přihlaste se na portál Azure](https://portal.azure.com/)portal a na stránce **Přehled** vyhledávací služby získáte adresu URL. Příkladem koncového bodu může být `https://mydemo.search.windows.net`.

1. V **nastavení** > **klíče**, získat klíč správce pro úplná práva ke službě. Existují dva zaměnitelné klíče pro správu, které jsou k dispozici pro kontinuitu podnikání v případě, že potřebujete převrátit jeden. Primární nebo sekundární klíč můžete použít při požadavcích na přidávání, úpravy a odstranění objektů.

   ![Získání koncového bodu HTTP a přístupového klíče](media/search-get-started-postman/get-url-key.png "Získání koncového bodu HTTP a přístupového klíče")

## <a name="2---set-up-your-environment"></a>2 - Nastavení prostředí

1. Spusťte Visual Studio a otevřete **DotNetHowToIndexers.sln**.

1. V Průzkumníku řešení otevřete **soubor appsettings.json** a zadejte informace o připojení.

1. Pro `searchServiceName`, pokud jehttps://my-demo-service.search.windows.netúplná adresa URL " ", název služby, který má být poskytnut, je "my-demo-service".

1. Formát `AzureSqlConnectionString`řetězce je pro tento formát podobný tomuto:`"Server=tcp:{your_dbname}.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"`

    ```json
    {
      "SearchServiceName": "<placeholder-Azure-Search-service-name>",
      "SearchServiceAdminApiKey": "<placeholder-admin-key-for-Azure-Search>",
      "AzureSqlConnectionString": "<placeholder-ADO.NET-connection-string",
    }
    ```

1. V připojovacím řetězci se ujistěte, že připojovací řetězec obsahuje platné heslo. Zatímco databáze a uživatelská jména budou kopírována, heslo musí být zadáno ručně.

## <a name="3---create-the-pipeline"></a>3 - Vytvoření potrubí

Indexery vyžadují objekt zdroje dat a index. Příslušný kód je ve dvou souborech:

  + **hotel.cs**, obsahující schéma, které definuje index
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

Hlavní program obsahuje logiku pro vytvoření klienta, indexu, zdroje dat a indexeru. Kód předpokládá, že byste tento program mohli spustit několikrát, a proto kontroluje a odstraňuje existující prostředky se stejným názvem.

Objekt zdroje dat je nakonfigurovaný s nastavením, které je specifické pro prostředky databáze Azure SQL, včetně [částečného nebo přírůstkového indexování](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows) pro využití integrovaných [funkcí zjišťování změn](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) Azure SQL. Databáze ukázkových hotelů v Azure SQL má sloupec "obnovitelné odstranění" s názvem **IsDeleted**. Když je tento sloupec nastaven na hodnotu true v databázi, indexer odebere odpovídající dokument z indexu Azure Cognitive Search.

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

Objekt indexeru je nezávislá na platformě, kde konfigurace, plánování a vyvolání jsou stejné bez ohledu na zdroj. Tento příklad indexer obsahuje plán, možnost obnovení, která vymaže historii indexeru a volá metodu k vytvoření a spuštění indexeru okamžitě.

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

## <a name="4---build-the-solution"></a>4 - Sestavení řešení

Stisknutím klávesy F5 sestavujete a spouštějte řešení. Program se spustí v režimu ladění. V okně konzoly se bude hlásit stav jednotlivých operací.

   ![Výstup konzoly](./media/search-indexer-tutorial/console-output.png "Výstup konzoly")

Váš kód běží místně v Sadě Visual Studio, připojení k vyhledávací službě v Azure, který se zase připojí k Azure SQL Database a načte datovou sadu. S tolika operacemi existuje několik potenciálních bodů selhání. Pokud se zobrazí chyba, zkontrolujte nejprve následující podmínky:

+ Zadané informace o připojení k vyhledávací službě se omezují na název služby v tomto kurzu. Pokud jste zadali úplnou adresu URL, operace se zastaví při vytváření indexu s chybou značící selhání připojení.

+ Informace o připojení k databázi v souboru **appsettings.json**. Měl by to být připojovací řetězec ADO.NET získaný z portálu a upravený tak, aby obsahoval platné uživatelské jméno a heslo pro vaši databázi. Uživatelský účet musí mít oprávnění k načtení dat. Ip adresa místního klienta musí mít přístup.

+ Omezení prostředků. Připomeňme, že úroveň Free má limity 3 indexy, indexery a zdroje dat. Služba, která dosáhne maximálního omezení, nemůže vytvářet nové objekty.

## <a name="5---search"></a>5 - Hledání

Pomocí portálu Azure ověřte vytváření objektů a pak pomocí **Průzkumníka vyhledávání** zadejte dotaz na index.

1. [Přihlaste se k portálu Azure](https://portal.azure.com/)a na stránce **Přehled** vyhledávací služby otevřete každý seznam a ověřte, zda je objekt vytvořen. **Indexy**, **Indexery**a **zdroje dat** budou mít "hotely", "azure-sql-indexer" a "azure-sql".

   ![Dlaždice Indexery a Zdroje dat](./media/search-indexer-tutorial/tiles-portal.png)

1. Vyberte index hotelů. Na stránce hotely je **průzkumník vyhledávání** první záložkou. 

1. Chcete-li vydat prázdný dotaz, klepněte na tlačítko **Hledat.** 

   Vrátí se tři položky ve vašem indexu jako dokumenty JSON. Průzkumník služby Search vrací dokumenty ve formátu JSON, abyste mohli zobrazit celou jejich strukturu.

   ![Dotaz na index](./media/search-indexer-tutorial/portal-search.png "Dotaz na index")
   
1. Dále zadejte hledaný řetězec `search=river&$count=true`. 

   Tento dotaz vyvolá fulltextové vyhledávání výrazu `river` a výsledek bude obsahovat počet odpovídajících dokumentů. Vrácení počtu odpovídajících dokumentů je užitečné ve scénářích testování, když máte velký index s tisíci nebo miliony dokumentů. V tomto případě dotazu odpovídá pouze jeden dokument.

1. Nakonec zadejte hledaný řetězec, který omezí pole ve výstupu JSON pouze na ta, která vás zajímají: `search=river&$count=true&$select=hotelId, baseRate, description`. 

   Odpověď na dotaz je omezená na vybraná pole, což ve výsledku znamená stručnější výstup.

## <a name="reset-and-rerun"></a>Resetování a opětovné spuštění

V raných experimentálních fázích vývoje je nejpraktičtějším přístupem pro iteraci návrhu odstranění objektů z Azure Cognitive Search a povolení jejich opětovného sestavení kódu. Názvy prostředků jsou jedinečné. Když se objekt odstraní, je možné ho znovu vytvořit se stejným názvem.

Ukázkový kód pro tento kurz kontroluje existující objekty a odstraní je, takže můžete znovu spustit kód.

Portál můžete také použít k odstranění indexů, indexerů a zdrojů dat.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Při práci ve vlastním předplatném je na konci projektu vhodné odebrat prostředky, které už nepotřebujete. Prostředky, které necháte běžet, vás můžou stát peníze. Prostředky můžete odstraňovat jednotlivě nebo můžete odstranit skupinu prostředků, a odstranit tak celou sadu prostředků najednou.

Můžete najít a spravovat prostředky na portálu pomocí odkazu Všechny prostředky nebo skupiny prostředků v levém navigačním podokně.

## <a name="next-steps"></a>Další kroky

Teď, když jste obeznámeni se základy indexování databáze SQL, pojďme se blíže podívat na konfiguraci indexeru.

> [!div class="nextstepaction"]
> [Konfigurace indexátoru databáze Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)