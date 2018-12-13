---
title: Kurz indexování databází Azure SQL na webu Azure portal – Azure Search
description: V tomto kurzu projdete databázi Azure SQL, extrahujete prohledávatelná data a naplníte jimi index služby Azure Search.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: na
ms.topic: tutorial
ms.date: 07/10/2018
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 872871d2ab9a9c693ad81081f24c8de68457982d
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53312047"
---
# <a name="tutorial-crawl-an-azure-sql-database-using-azure-search-indexers"></a>Kurz: Procházejte databázi Azure SQL pomocí indexerů Azure Search

V tomto kurzu se dozvíte, jak nakonfigurovat indexer pro extrahování prohledávatelných dat z ukázkové databáze Azure SQL. [Indexery](search-indexer-overview.md) jsou součástí služby Azure Search, které procházejí externí zdroje dat a naplňují [index vyhledávání](search-what-is-an-index.md) obsahem. Ze všech indexerů je nejpoužívanější indexer pro databázi Azure SQL. 

Znalost konfigurace indexeru je užitečná, protože zjednodušuje množství kódu, který musíte psát a spravovat. Místo přípravy a nabízení datové sady JSON odpovídající schématu můžete ke zdroji dat připojit indexer a nechat ho extrahovat data a vložit je do indexu. Volitelně také můžete indexer spouštět podle plánu opakování, aby přebíral změny v základním zdroji.

V tomto kurzu pomocí [klientských knihoven Azure Search pro .NET](https://aka.ms/search-sdk) a konzolové aplikace .NET Core provedete následující úlohy:

> [!div class="checklist"]
> * Stažení a konfigurace řešení
> * Přidání informací o vyhledávací službě do nastavení aplikace
> * Příprava externích datových sad v databázi Azure SQL 
> * Kontrola definic indexu a indexeru ve vzorovém kódu
> * Spuštění kódu indexeru pro import dat
> * Prohledání indexu
> * Zobrazení konfigurace indexeru na portálu

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

* Služba Azure Search. Nápovědu k jejímu nastavení najdete v tématu [Vytvoření vyhledávací služby](search-create-service-portal.md).

* Databáze Azure SQL, která poskytuje externí zdroj dat používaný indexerem. Ukázkové řešení obsahuje datový soubor SQL pro vytvoření tabulky.

* Sada Visual Studio 2017. Můžete použít bezplatnou sadu [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). 

> [!Note]
> Pokud používáte bezplatnou službu Azure Search, platí pro vás omezení na tři indexy, tři indexery a tři zdroje dat. V tomto kurzu se vytváří od každého jeden. Ujistěte se, že ve své službě máte místo pro příjem nových prostředků.

## <a name="download-the-solution"></a>Stažení řešení

Řešení indexeru použité v tomto kurzu pochází z kolekce ukázek služby Azure Search doručované v rámci jediného stažení hlavní větve. Řešení použité pro účely tohoto kurzu je *DotNetHowToIndexers*.

1. V úložišti GitHub s ukázkami Azure přejděte do [**Azure-Samples/search-dotnet-getting-started**](https://github.com/Azure-Samples/search-dotnet-getting-started).

2. Klikněte na **Clone or Download** (Klonovat nebo stáhnout) > **Download ZIP** (Stáhnout ZIP). Ve výchozím nastavení se soubor stáhne do složky Stažené soubory.

3. Přejděte do **Průzkumníka souborů** > **Stažené soubory**, klikněte pravým tlačítkem na soubor a zvolte **Extrahovat vše**.

4. Vypněte oprávnění jen pro čtení. Klikněte pravým tlačítkem na název složky, vyberte **Vlastnosti** > **Obecné** a zrušte zaškrtnutí atributu **Jen pro čtení** pro aktuální složku, podsložky a soubory.

5. V sadě **Visual Studio 2017** otevřete řešení *DotNetHowToIndexers.sln*.

6. V **Průzkumníku řešení** klikněte pravým tlačítkem na nejvyšší nadřazený uzel Řešení a vyberte **Obnovit balíčky NuGet**.

## <a name="set-up-connections"></a>Nastavení připojení
Informace o připojení k požadovaným službám se zadává do souboru **appsettings.json** v řešení. 

V Průzkumníku řešení otevřete soubor **appsettings.json**, abyste mohli vyplnit všechna nastavení pomocí pokynů v tomto kurzu.  

```json
{
  "SearchServiceName": "Put your search service name here",
  "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
  "AzureSqlConnectionString": "Put your Azure SQL database connection string here",
}
```

### <a name="get-the-search-service-name-and-admin-api-key"></a>Získání názvu vyhledávací služby a klíče api-key správce

Koncový bod vyhledávací služby a klíč najdete na portálu. Klíč poskytuje přístup k operacím služby. Klíče správce povolují oprávnění k zápisu potřebné k vytváření a odstraňování objektů ve službě, jako jsou indexy a indexery.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) a vyhledejte [vyhledávací služby pro vaše předplatné](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices).

2. Otevřete stránku služby.

3. Na hlavní stránce vyhledejte v horní části název služby. Na následujícím snímku obrazovky to je *azs-tutorial*.

   ![Název služby](./media/search-indexer-tutorial/service-name.png)

4. Název zkopírujte a vložte jej jako první položku do souboru **appsettings.json** v sadě Visual Studio.

  > [!Note]
  > Název služby je součástí koncového bodu, který zahrnuje search.windows.net. Pokud jste zvědaví, v části **Základy** na stránce Přehled můžete zobrazit úplnou adresu URL. Adresa URL vypadá jako v tomto příkladu: https://your-service-name.search.windows.net

5. Na levé straně v části **Nastavení** > **Klíče** zkopírujte jeden z klíčů správce a vložte jej jako druhou položku do souboru **appsettings.json**. Klíče jsou alfanumerické řetězce vygenerované pro vaši službu během zřizování, které se vyžadují pro ověřený přístup k operacím služby. 

  Po přidání obou nastavení by měl váš soubor vypadat přibližně jako v tomto příkladu:

  ```json
  {
    "SearchServiceName": "azs-tutorial",
    "SearchServiceAdminApiKey": "A1B2C3D4E5F6G7H8I9J10K11L12M13N14",
    . . .
  }
  ```

## <a name="prepare-an-external-data-source"></a>Příprava externího zdroje dat

V tomto kroku vytvoříte externí zdroj dat, který může indexer procházet. Datový soubor pro tento kurz je *hotels.sql* ve složce řešení \DotNetHowToIndexers. 

### <a name="azure-sql-database"></a>Azure SQL Database

Datovou sadu ve službě Azure SQL Database můžete vytvořit pomocí webu Azure Portal a souboru *hotels.sql* z ukázky. Azure Search využívá ploché sady řádků, jako jsou například řádky generované zobrazením nebo dotazem. Soubor SQL v ukázkovém řešení vytvoří a naplní jednu tabulku.

Následující cvičení předpokládá, že neexistuje žádný server ani databáze, a v kroku 2 poskytuje pokyny k jejich vytvoření. Pokud máte existující prostředek, volitelně můžete tabulku hotels přidat do něj podle pokynů od kroku 4 dále.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/). 

2. Klikněte na **Vytvořit prostředek** > **SQL Database** a vytvořte databázi, server a skupinu prostředků. Můžete použít výchozí hodnoty a nejnižší cenovou úroveň. Jednou z výhod vytvoření serveru je, že můžete zadat uživatelské jméno a heslo správce, které jsou potřeba k vytvoření a načtení tabulek v pozdějším kroku.

   ![Stránka Nová databáze](./media/search-indexer-tutorial/indexer-new-sqldb.png)

3. Kliknutím na **Vytvořit** nasaďte nový server a databázi. Počkejte na nasazení serveru a databáze.

4. Otevřete stránku služby SQL Database pro vaši novou databázi, pokud ještě není otevřená. Název prostředku by měl být *Databáze SQL*, a ne *SQL Server*.

  ![Stránka databáze SQL](./media/search-indexer-tutorial/hotels-db.png)

4. Na panelu příkazů klikněte na **Nástroje** > **Editor dotazů**.

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
      "SearchServiceName": "azs-tutorial",
      "SearchServiceAdminApiKey": "A1B2C3D4E5F6G7H8I9J10K11L12M13N14",
      "AzureSqlConnectionString": "Server=tcp:hotels-db.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security  Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;",
    }
    ```

## <a name="understand-index-and-indexer-code"></a>Vysvětlení kódu indexu a indexeru

Váš kód je teď připravený k sestavení a spuštění. Než to provedete, věnujte chvíli prostudování definic indexu a indexeru pro tuto ukázku. Důležitý kód je ve dvou souborech:

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

V tomto kurzu indexer přetahuje data z jednoho zdroje dat. V praxi můžete ke stejnému indexu připojit několik indexerů a vytvořit tak společný prohledávatelný index z několika zdrojů dat a indexerů. V závislosti na tom, kde potřebujete flexibilitu, můžete použít stejný pár indexu a indexeru a měnit pouze zdroje dat nebo jeden index s různými kombinacemi indexeru a zdroje dat.

### <a name="in-programcs"></a>Soubor Program.cs

Hlavní program obsahuje funkce pro všechny tři reprezentativní zdroje dat. Když se zaměříme pouze na službu Azure SQL Database, vyniknou následující objekty:

  ```csharp
  private const string IndexName = "hotels";
  private const string AzureSqlHighWaterMarkColumnName = "RowVersion";
  private const string AzureSqlDataSourceName = "azure-sql";
  private const string AzureSqlIndexerName = "azure-sql-indexer";
  ```

Ve službě Azure Search mezi objekty, které můžete zobrazit, konfigurovat nebo odstranit, patří indexy, indexery a zdroje dat (*hotels*, *azure-sql-indexer*, *azure-sql* v uvedeném pořadí). 

Sloupec *AzureSqlHighWaterMarkColumnName* si zaslouží zvláštní pozornost, protože poskytuje informace o detekci změn, pomocí kterých indexer určuje, jestli se řádek od poslední úlohy indexování změnil. [Zásady detekce změn](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) se podporují pouze v indexerech a liší se podle zdroje dat. V případě služby Azure SQL Database si v závislosti na požadavcích databáze můžete vybrat mezi dvěma zásadami.

Následující kód ukazuje metody, které v souboru Program.cs slouží k vytvoření zdroje dat a indexeru. Kód předpokládá, že byste tento program mohli spustit několikrát, a proto kontroluje a odstraňuje existující prostředky se stejným názvem.

  ```csharp
  private static string SetupAzureSqlIndexer(SearchServiceClient serviceClient, IConfigurationRoot configuration)
  {
    Console.WriteLine("Deleting Azure SQL data source if it exists...");
    DeleteDataSourceIfExists(serviceClient, AzureSqlDataSourceName);

    Console.WriteLine("Creating Azure SQL data source...");
    DataSource azureSqlDataSource = CreateAzureSqlDataSource(serviceClient, configuration);

    Console.WriteLine("Deleting Azure SQL indexer if it exists...");
    DeleteIndexerIfExists(serviceClient, AzureSqlIndexerName);

    Console.WriteLine("Creating Azure SQL indexer...");
    Indexer azureSqlIndexer = CreateIndexer(serviceClient, AzureSqlDataSourceName, AzureSqlIndexerName);

    return azureSqlIndexer.Name;
  }
  ```

Všimněte si, že volání rozhraní API indexerem jsou nezávislá na platformě, kromě volání [DataSourceType](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet), které určuje typ prohledávacího modulu, který se má vyvolat.

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

1. Otevřete stránku Přehled vaší služby Azure Search.
2. Přejděte dolů a vyhledejte dlaždice **Indexery** a **Zdroje dat**.
3. Kliknutím na dlaždici otevřete seznam jednotlivých prostředků. Můžete vybrat jednotlivé indexery nebo zdroje dat a zobrazit nebo upravit nastavení konfigurace.

  ![Dlaždice Indexery a Zdroje dat](./media/search-indexer-tutorial/tiles-portal.png)


## <a name="clean-up-resources"></a>Vyčištění prostředků

Nejrychlejším způsobem, jak po kurzu všechno uklidit, je odstranit skupinu prostředků, která obsahuje službu Azure Search. Odstraněním skupiny prostředků teď můžete trvale odstranit všechno, co se v ní nachází. Název příslušné skupiny prostředků najdete na portálu na stránce Přehled služby Azure Search.

## <a name="next-steps"></a>Další postup

Algoritmy využívající AI je možné připojit ke kanálu indexování. Jako další krok pokračujte následujícím kurzem.

> [!div class="nextstepaction"]
> [Indexování dokumentů ve službě Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)