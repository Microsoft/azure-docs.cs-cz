---
title: 'Kurz: Indexování dat z databáze Azure SQL C# ukázkový kód – Azure Search'
description: A C# příklad kódu ukazuje, jak se připojit ke službě Azure SQL database, extrahujte prohledávatelná data a jejich načtení do indexu Azure Search.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: na
ms.topic: tutorial
ms.date: 04/09/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 8550e220a2c87823fc337154ea33dd3c4ec81ed0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60322152"
---
# <a name="c-tutorial-crawl-an-azure-sql-database-using-azure-search-indexers"></a>C#Kurz: Procházejte databázi Azure SQL pomocí indexerů Azure Search

Zjistěte, jak nakonfigurovat indexer pro extrahování prohledávatelných dat z ukázkové databáze Azure SQL. [Indexery](search-indexer-overview.md) jsou součástí služby Azure Search, které procházejí externí zdroje dat a naplňují [index vyhledávání](search-what-is-an-index.md) obsahem. Ze všech indexerů je nejpoužívanější indexer pro Azure SQL Database. 

Znalost konfigurace indexeru je užitečná, protože zjednodušuje množství kódu, který musíte psát a spravovat. Místo přípravy a nabízení datové sady JSON odpovídající schématu můžete ke zdroji dat připojit indexer a nechat ho extrahovat data a vložit je do indexu. Volitelně také můžete indexer spouštět podle plánu opakování, aby přebíral změny v základním zdroji.

V tomto kurzu, používá [klientských knihoven Azure Search pro .NET](https://aka.ms/search-sdk) a konzolovou aplikaci .NET Core pro provádět následující úlohy:

> [!div class="checklist"]
> * Přidání informací o vyhledávací službě do nastavení aplikace
> * Příprava externích datových sad v databázi Azure SQL 
> * Kontrola definic indexu a indexeru ve vzorovém kódu
> * Spuštění kódu indexeru pro import dat
> * Prohledání indexu
> * Zobrazení konfigurace indexeru na portálu

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Následující služby, nástroje a data se používají v tomto rychlém startu. 

[Vytvoření služby Azure Search](search-create-service-portal.md) nebo [najít existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) pod vaším aktuálním předplatným. Můžete použít bezplatnou službu pro účely tohoto kurzu.

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) ukládá externí zdroj dat používaný indexerem. Ukázkové řešení obsahuje datový soubor SQL pro vytvoření tabulky. Postup vytvoření služby a databáze jsou k dispozici v tomto kurzu.

[Visual Studio 2017](https://visualstudio.microsoft.com/downloads/), všechny edice, lze použít ke spuštění ukázkové řešení. Ukázky kódu a instrukce byly testovány v bezplatná edice Community.

[Azure – ukázky/search-dotnet-getting-started](https://github.com/Azure-Samples/search-dotnet-getting-started) poskytuje ukázkové řešení nachází v úložišti Githubu ukázky v Azure. Stažení a extrakci řešení. Ve výchozím nastavení řešení jsou jen pro čtení. Klikněte pravým tlačítkem na řešení a vymažte atribut jen pro čtení tak, že můžete upravovat soubory.

> [!Note]
> Pokud používáte bezplatnou službu Azure Search, platí pro vás omezení na tři indexy, tři indexery a tři zdroje dat. V tomto kurzu se vytváří od každého jeden. Ujistěte se, že ve své službě máte místo pro příjem nových prostředků.

## <a name="get-a-key-and-url"></a>Získejte klíč a adresy URL

Volání REST vyžadují pro každý požadavek adresu URL služby a přístupový klíč. Vyhledávací služba se vytvoří s oběma, takže pokud jste do svého předplatného přidali službu Azure Search, získejte potřebné informace pomocí následujícího postupu:

1. [Přihlaste se k webu Azure portal](https://portal.azure.com/)a ve vyhledávací službě **přehled** stránce, získat adresu URL. Příkladem koncového bodu může být `https://mydemo.search.windows.net`.

1. V **nastavení** > **klíče**, získat klíč pro úplná práva správce na službu. Existují dva klíče zaměnitelné správce, v případě, že budete potřebovat k výměně jeden k dispozici zajišťuje nepřetržitý chod podniků. U požadavků můžete použít buď primární nebo sekundární klíč pro přidání, úpravy a odstraňování objektů.

![Získejte koncový bod a přístupový klíč rozhraní HTTP](media/search-fiddler/get-url-key.png "získat HTTP koncový bod a přístupový klíč")

Všechny požadavky vyžaduje klíč rozhraní api na každou požadavku odeslaného do vaší služby. Platný klíč vytváří na základě žádosti vztah důvěryhodnosti mezi aplikací, která žádost odeslala, a službou, která ji zpracovává.

## <a name="set-up-connections"></a>Nastavení připojení
Informace o připojení k požadovaným službám se zadává do souboru **appsettings.json** v řešení. 

1. V sadě Visual Studio, otevřete **DotNetHowToIndexers.sln** souboru.

1. V Průzkumníku řešení otevřete **appsettings.json** tak, aby mohli vyplnit všechna nastavení.  

První dvě položky můžete přejít k vyplnění právě teď pomocí adresy URL a Správce klíčů pro službu Azure Search. Zadaný koncový bod `https://mydemo.search.windows.net`, je název služby k poskytování `mydemo`.

```json
{
  "SearchServiceName": "Put your search service name here",
  "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
  "AzureSqlConnectionString": "Put your Azure SQL database connection string here",
}
```

Poslední položka vyžaduje existující databáze. Vytvoříte ho v dalším kroku.

## <a name="prepare-sample-data"></a>Příprava ukázkových dat

V tomto kroku vytvoříte externí zdroj dat, který může indexer procházet. Datovou sadu ve službě Azure SQL Database můžete vytvořit pomocí webu Azure Portal a souboru *hotels.sql* z ukázky. Azure Search využívá ploché sady řádků, jako jsou například řádky generované zobrazením nebo dotazem. Soubor SQL v ukázkovém řešení vytvoří a naplní jednu tabulku.

Následující cvičení předpokládá, že neexistuje žádný server ani databáze, a v kroku 2 poskytuje pokyny k jejich vytvoření. Pokud máte existující prostředek, volitelně můžete tabulku hotels přidat do něj podle pokynů od kroku 4 dále.

1. [Přihlaste se k webu Azure portal](https://portal.azure.com/). 

2. Vyhledat nebo vytvořit **Azure SQL Database** vytvořit databázi, server a skupinu prostředků. Můžete použít výchozí hodnoty a nejnižší cenovou úroveň. Jednou z výhod vytvoření serveru je, že můžete zadat uživatelské jméno a heslo správce, které jsou potřeba k vytvoření a načtení tabulek v pozdějším kroku.

   ![Stránka Nová databáze](./media/search-indexer-tutorial/indexer-new-sqldb.png)

3. Kliknutím na **Vytvořit** nasaďte nový server a databázi. Počkejte na nasazení serveru a databáze.

4. Otevřete stránku služby SQL Database pro vaši novou databázi, pokud ještě není otevřená. Název prostředku by měl být *Databáze SQL*, a ne *SQL Server*.

   ![Stránka databáze SQL](./media/search-indexer-tutorial/hotels-db.png)

4. V navigačním podokně klikněte na tlačítko **editor dotazů (preview)**.

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

Po nastavení a konfigurace dat jsou na místě, ukázkový program v **DotNetHowToIndexers.sln** je připraven k sestavení a spuštění. Než to provedete, věnujte chvíli prostudování definic indexu a indexeru pro tuto ukázku. Důležitý kód je ve dvou souborech:

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

V tomto kurzu indexer přetahuje data z jednoho zdroje dat. V praxi můžete připojit několik indexerů ke stejnému indexu, vytváření tak společný prohledávatelný index z různých zdrojů dat. V závislosti na tom, kde potřebujete flexibilitu, můžete použít stejný pár indexu a indexeru a měnit pouze zdroje dat nebo jeden index s různými kombinacemi indexeru a zdroje dat.

### <a name="in-programcs"></a>Soubor Program.cs

Hlavní program obsahuje logiku pro vytvoření klienta, index, zdroj dat a indexeru. Kód předpokládá, že byste tento program mohli spustit několikrát, a proto kontroluje a odstraňuje existující prostředky se stejným názvem.

Objekt zdroje dat je nakonfigurován s nastavením, které jsou specifické pro prostředky databáze Azure SQL, včetně [přírůstkové indexování](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows) pro využití integrovaných [změníte některé funkce detekce](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) Azure SQL. Ukázková databáze hotely v Azure SQL má sloupec s názvem "obnovitelné odstranění" **IsDeleted**. Když tento sloupec je nastaven na hodnotu true v databázi, indexer, odebere odpovídající dokumentu z indexu Azure Search.

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

Objekt indexeru je nezávislý na platformě, kde stejný bez ohledu na zdroji jsou konfigurace, plánování a vyvolání. Tento příklad indexer zahrnuje plán, má možnost resetování, který vymaže historii indexeru a volá metodu pro vytvoření a spuštění indexeru okamžitě.

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

+ Omezení prostředků. Vzpomeňte si, že pro sdílenou (bezplatnou) službu platí omezení na 3 indexy, indexery a zdroje dat. Služba, která dosáhne maximálního omezení, nemůže vytvářet nové objekty.

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

1. [Přihlaste se k webu Azure portal](https://portal.azure.com/)a ve vyhledávací službě **přehled** stránce, kliknutí na příslušné odkazy pro **indexy**, **indexery**, a **dat Zdroje**.
3. Vyberte jednotlivé objekty lze zobrazit nebo upravit nastavení konfigurace.

   ![Dlaždice Indexery a Zdroje dat](./media/search-indexer-tutorial/tiles-portal.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Nejrychlejším způsobem, jak po kurzu všechno uklidit, je odstranit skupinu prostředků, která obsahuje službu Azure Search. Odstraněním skupiny prostředků teď můžete trvale odstranit všechno, co se v ní nachází. Název příslušné skupiny prostředků najdete na portálu na stránce Přehled služby Azure Search.

## <a name="next-steps"></a>Další postup

Algoritmy využívající AI je možné připojit ke kanálu indexování. Jako další krok pokračujte následujícím kurzem.

> [!div class="nextstepaction"]
> [Indexování dokumentů ve službě Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)