---
title: Vytvoření webové aplikace .NET pomocí služby Azure Cosmos DB pomocí rozhraní SQL API
description: V tomto rychlém startu pomocí webu Azure portal a webové aplikace .NET k vytváření a správě prostředků účtu rozhraní SQL API ve službě Azure Cosmos DB.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 04/05/2019
ms.openlocfilehash: 7ecb2269243ae96b629a20a26956e6220a2e616c
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2019
ms.locfileid: "59280838"
---
# <a name="quickstart-build-a-net-web-app-using-sql-api-account-in-azure-cosmos-db"></a>Rychlý start: Vytvoření webové aplikace .NET pomocí rozhraní SQL API účtu ve službě Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](create-sql-api-dotnet.md)
> * [.NET (Preview)](create-sql-api-dotnet-preview.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)
>  
> 

Azure Cosmos DB je globálně distribuovaná databázová služba Microsoftu pro více modelů. Azure Cosmos DB můžete rychle vytvořit a dotazovat databáze klíč/hodnota, dokumentové databáze a databáze grafů, které můžou využívat výhody globální distribuce a možností horizontálního škálování v jádru služby Azure Cosmos DB. 

Tento rychlý start ukazuje, jak pomocí webu Azure portal k vytvoření služby Azure Cosmos DB [rozhraní SQL API](sql-api-introduction.md) účtu, vytvořit databázi dokumentů a kolekce a přidejte data do kolekce. Pak použijete [SQL SDK pro .NET](sql-api-sdk-dotnet.md) webové aplikace k přidání dalších dat do kolekce. 

V tomto rychlém startu použijete Průzkumníka dat na webu Azure Portal k vytvoření databáze a kolekce. Můžete také vytvořit databázi a kolekci pomocí vzorový kód .NET. Další informace najdete v tématu [revize kódu .NET](#review-the-net-code). 

## <a name="prerequisites"></a>Požadavky

Visual Studio 2017 s pracovním postupem vývoj pro Azure, nainstalované
- Můžete stáhnout a použít **bezplatné** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Nezapomeňte při instalaci sady Visual Studio povolit možnost **Azure Development**. 

Předplatného Azure nebo Bezplatný zkušební účet Azure Cosmos DB
- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

<a id="create-account"></a>
## <a name="create-an-azure-cosmos-db-account"></a>Vytvoření účtu služby Azure Cosmos DB

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

<a id="create-collection-database"></a>
## <a name="add-a-database-and-a-collection"></a>Přidejte databáze a kolekce 

Průzkumník dat na webu Azure Portal slouží k vytvoření databáze a kolekce. 

1.  Vyberte **Průzkumník dat** z levého navigačního panelu ve službě Azure Cosmos DB účtu stránky a pak vyberte **novou kolekci**. 
    
    Budete muset posunout doprava najdete v článku **přidat kolekci** oblasti.
    
    ![Na webu Azure portal Průzkumník dat, podokno přidat kolekci](./media/create-sql-api-dotnet/azure-cosmosdb-data-explorer-dotnet.png)
    
1.  Na stránce **Přidat kolekci** zadejte nastavení pro novou kolekci.
    
    |Nastavení|Navrhovaná hodnota|Popis
    |---|---|---|
    |**ID databáze**|ToDoList|Zadejte *ToDoList* jako název nové databáze. Názvy databází musí mít délku 1 až 255 znaků a nesmí obsahovat `/, \\, #, ?`, ani koncové mezery.|
    |**ID kolekce**|Items|Jako název nové kolekce zadejte *Items*. ID kolekcí mají stejné požadavky na znaky jako názvy databází.|
    |**Klíč oddílu**| /kategorie| Ukázku popsanou v tomto článku používá */category* jako klíč oddílu.|
    |**Propustnost**|400|Ponechte propustnost na 400 jednotek žádostí za sekundu (RU/s). Pokud budete chtít snížit latenci, můžete propustnost později navýšit.| 
    
    Nepřidávejte **jedinečné klíče** pro účely tohoto příkladu. Jedinečné klíče umožňují přidat do databáze vrstvu integrity dat. Tím zajistíte jedinečnost jedné nebo více hodnot pro klíč oddílu. Další informace najdete v tématu [jedinečné klíče ve službě Azure Cosmos DB](unique-keys.md).
    
1.  Vyberte **OK**. 
    Průzkumník dat zobrazí novou databázi a kolekci.
    
    ![Průzkumník dat na webu Azure Portal zobrazující novou databázi a tabulku](./media/create-sql-api-dotnet/azure-cosmos-db-new-collection.png)

## <a name="add-data-to-your-database"></a>Přidat data do databáze

Přidání dat do nové databáze pomocí Průzkumníku dat.

1. V **Průzkumník dat**, nová databáze zobrazí v **kolekce** podokně. Rozbalte **ToDoList** databáze, rozbalte **položky** kolekce, vyberte **dokumenty**a pak vyberte **nový dokument**. 
   
   ![Vytváření nových dokumentů v Průzkumníku dat na portálu Azure Portal](./media/create-sql-api-dotnet/azure-cosmosdb-new-document.png)
   
1. Přidejte následující strukturu dokumentu na pravé straně **dokumenty** podokna:

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries.",
         "isComplete": false
     }
     ```

1. Vyberte **Uložit**.
   
   ![Zkopírujte json data a vyberte Uložit v Průzkumníku dat na webu Azure Portal](./media/create-sql-api-dotnet/azure-cosmosdb-save-document.png)
   
1. Vyberte **nový dokument** znovu a vytvořte a uložte jiný dokument s jedinečným `id`a všechny ostatní vlastnosti a hodnoty požadujete. Dokumenty můžou mít jakoukoli strukturu, protože Azure Cosmos DB neuplatňuje žádné schéma s vašimi daty.

## <a name="query-your-data"></a>Dotazování dat

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="use-the-net-web-app-to-manage-data"></a>Použijte webové aplikace .NET pro správu dat

Pokud chcete zobrazit, jak snadno se pracuje s daty služby Azure Cosmos DB prostřednictvím kódu programu, naklonujte ukázkovou webovou aplikaci SQL API .NET z Githubu, aktualizovat připojovací řetězec a spusťte aplikaci aktualizovat vaše data. 

Můžete také vytvořit databázi a kolekci pomocí ukázkového kódu .NET. Další informace najdete v tématu [revize kódu .NET](#review-the-net-code).

### <a name="clone-the-sample-app"></a>Klonování ukázkové aplikace

Nejprve naklonujte C# [aplikace SQL API](https://github.com/Azure-Samples/documentdb-dotnet-todo-app) z Githubu. 

1. Otevřete okno terminálu git, jako je třeba Git Bash, vytvořte nový adresář s názvem *git-samples*a změňte ho na: 
   
   ```bash
   mkdir /c/git-samples/
   cd /c/git-samples/
   ```
   
1. Spusťte následující příkaz a naklonujte ukázkové úložiště vytvořte kopii ukázkové aplikace ve vašem počítači:
   
   ```bash
   git clone https://github.com/Azure-Samples/documentdb-dotnet-todo-app.git
   ```

### <a name="update-the-connection-string"></a>Aktualizujte připojovací řetězec 

1. Přejděte na a otevřete *todo.sln* souboru naklonované aplikace v sadě Visual Studio. 

1. V sadě Visual Studio **Průzkumníka řešení**, otevřete *web.config* souboru. 

1. Přejděte zpět na portálu Azure ke zkopírování vašich informací o připojovacím řetězci a vložte do *web.config*.
   
   1. Ve vaší službě Azure Cosmos DB účet navigačním panelu vlevo vyberte **klíče**.
      
      ![Zobrazení a zkopírování přístupového klíče na webu Azure Portal v okně Klíče](./media/create-sql-api-dotnet/keys.png)
      
   1. V části **klíče pro čtení i zápis**, kopie **URI** hodnotu pomocí tlačítka pro kopírování na pravé straně a vložte ho do `endpoint` klíče v *web.config*. Příklad: 
      
      `<add key="endpoint" value="https://mysqlapicosmosdb.documents.azure.com:443/" />`
      
   1. Kopírovat **primární klíč** hodnotu a vložte ho do `authKey` klíče v *web.config*. Příklad:
      
      `<add key="authKey" value="19ZDNJAiYL26tmnRvoez6hmtIfBGwjun50PWRjNYMC2ig8Ob9hYk7Fq1RYSv8FcIYnh1TdBISvCh7s6yyb0000==" />`

       
1. Ujistěte se, že databáze a kolekce hodnot v *web.config* shodovat s názvy, které jste vytvořili dříve. 

   ```csharp
   <add key="database" value="ToDoList"/>
   <add key="collection" value="Items"/>
   ```
 
1. Uložit *souboru web.config.* Aktualizovali jste vaši aplikaci se všemi informacemi, které potřebuje ke komunikaci s Azure Cosmos DB.

### <a name="run-the-web-app"></a>Spuštění webové aplikace

1. V sadě Visual Studio, klikněte pravým tlačítkem **todo** projekt **Průzkumníku řešení**a pak vyberte **spravovat balíčky NuGet**. 

1. Do pole **Procházet** v NuGetu zadejte *DocumentDB*.

1. Z výsledků nainstalujte **Microsoft.Azure.DocumentDB** knihovny, pokud ještě není nainstalovaný. Tím se nainstaluje [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) balíček a všechny závislosti.
   
   Pokud správce balíčků NuGet se zobrazí zpráva, že se z řešení chybí některé balíčky, vyberte **obnovení** instalace z interní zdroje. 

1. Vyberte **Ctrl**+**F5** a spusťte tak aplikaci v prohlížeči. 

1. Vyberte **vytvořit nový** v aplikaci seznamu úkolů a vytvořit několik nových úloh.

   ![Aplikace seznamu úkolů s ukázkovými daty](./media/create-sql-api-dotnet/azure-comosdb-todo-app-list.png)

Můžete přejít zpět do Průzkumníku dat na webu Azure Portal najdete v článku, dotazy, upravit a pracovat s novými daty. 

## <a name="review-the-net-code"></a>Projděte si kód v .NET

Tento krok je volitelný. V tomto rychlém startu jste vytvořili databázi a kolekci na webu Azure Portal a přidání ukázkových dat pomocí ukázku .NET. Ale můžete také vytvoříte databázi a kolekci pomocí ukázku .NET. Pokud vás zajímá vytvoření databázových prostředků v kódu, projděte si následující fragmenty kódu. Fragmenty kódu pocházejí ze *DocumentDBRepository.cs* soubor **todo** projektu.

* Tento kód inicializuje `DocumentClient`: 

    ```csharp
    client = new DocumentClient(new Uri(ConfigurationManager.AppSettings["endpoint"]), ConfigurationManager.AppSettings["authKey"]);
    ```

* Tento kód vytvoří novou databázi s použitím `CreateDatabaseAsync` metody:

    ```csharp
    await client.CreateDatabaseAsync(new Database { Id = DatabaseId });
    ```

* Následující kód vytvoří novou kolekci s použitím `CreateDocumentCollectionAsync` metody:

    ```csharp
    private static async Task CreateCollectionIfNotExistsAsync()
    {
        try
        {
           await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(DatabaseId, CollectionId));
        }
        catch (DocumentClientException e)
        {
           if (e.StatusCode == System.Net.HttpStatusCode.NotFound)
           {
              await client.CreateDocumentCollectionAsync(
              UriFactory.CreateDatabaseUri(DatabaseId),
              new DocumentCollection
              {
                  Id = CollectionId
              },
              new RequestOptions { OfferThroughput = 400 });
           }
           else
           {
             throw;
           }
        }
    }
    ```

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste zjistili, jak vytvořit účet služby Azure Cosmos DB, vytvoření databáze a kolekce pomocí Průzkumníku dat a spuštění webové aplikace .NET a aktualizujte data. Teď můžete do svého účtu služby Azure Cosmos DB importovat další data. 

> [!div class="nextstepaction"]
> [Import dat do služby Azure Cosmos DB](import-data.md)

