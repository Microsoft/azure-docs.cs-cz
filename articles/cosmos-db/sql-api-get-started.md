---
title: Vytvoření konzolové aplikace .NET pro správu dat v účtu rozhraní SQL API služby Azure Cosmos DB
description: Kurz, který se vytvoří online databáze a C# konzolovou aplikaci pomocí rozhraní SQL API.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: sngun
ms.openlocfilehash: a8d144b2cb8ee18c69dc4c4768b09422d44bade2
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2019
ms.locfileid: "59617314"
---
# <a name="build-a-net-console-app-to-manage-data-in-azure-cosmos-db-sql-api-account"></a>Vytvoření konzolové aplikace .NET pro správu dat v účtu rozhraní SQL API služby Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET (preview)](sql-api-dotnet-get-started-preview.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [.NET core (Preview)](sql-api-dotnet-core-get-started-preview.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

Vítá vás Azure Cosmos DB SQL API get kurzu Začínáme. Po dokončení tohoto kurzu budete mít konzolovou aplikaci, která vytváří prostředky a dotazuje se služby Azure Cosmos DB.

V tomto kurzu získáte informace o následujících postupech:

> [!div class="checklist"]
>
> - Vytvoření účtu služby Azure Cosmos DB a připojte se k němu
> - Konfigurace řešení sady Visual Studio
> - Vytvoření databáze
> - Vytvoření kolekce
> - Vytvoření dokumentů JSON
> - Dotaz na kolekci
> - Aktualizace dokumentu JSON
> - Odstranění dokumentu
> - Odstranění databáze

## <a name="prerequisites"></a>Požadavky

Visual Studio 2017 s pracovním postupem vývoj pro Azure nainstalovaný:
- Můžete stáhnout a použít **bezplatné** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Nezapomeňte při instalaci sady Visual Studio povolit možnost **Azure Development**. 

Předplatné Azure nebo Bezplatný zkušební účet Cosmos DB:
- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
  
- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  
  
- Pokud používáte emulátor služby Azure Cosmos DB, postupujte podle kroků uvedených v [emulátor služby Azure Cosmos DB](local-emulator.md) nastavení emulátoru. Potom zahájit kurz na [nastavení řešení v sadě Visual Studio](#SetupVS).
  
## <a name="get-the-completed-solution"></a>Získání dokončeného řešení

Pokud nemáte čas k dokončení tohoto kurzu, nebo chcete jenom ukázky kódu, si můžete stáhnout kompletní řešení z [Githubu](https://github.com/Azure-Samples/documentdb-dotnet-getting-started). 

Ke spuštění staženého kompletní řešení: 

1. Ujistěte se, že máte [požadavky](#prerequisites) nainstalované. 
1. Otevřete na stažený *GetStarted.sln* soubor řešení v sadě Visual Studio.
1. V **Průzkumníka řešení**, klikněte pravým tlačítkem myši **GetStarted** projektu a vyberte **spravovat balíčky NuGet**.
1. Na **NuGet** kartu, vyberte možnost **obnovení** a obnovit odkazy na .NET SDK služby Azure Cosmos DB.
1. V *App.config* soubor, aktualizovat `EndpointUrl` a `PrimaryKey` hodnoty, jak je popsáno v [připojte se k účtu Azure Cosmos DB](#Connect) oddílu.
1. Vyberte **ladění** > **spustit bez ladění** nebo stiskněte klávesu **Ctrl**+**F5** sestavíte a spustíte aplikaci.

## <a name="create-an-azure-cosmos-db-account"></a>Vytvoření účtu služby Azure Cosmos DB

Postupujte podle těchto pokynů a vytvořte účet služby Azure Cosmos DB na webu Azure Portal. Pokud již máte účet služby Azure Cosmos DB používat, přeskočte k části [nastavení řešení v sadě Visual Studio](#SetupVS). 

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupVS"></a>Nastavení řešení v sadě Visual Studio

1. V sadě Visual Studio 2017, vyberte **souboru** > **nový** > **projektu**.
   
1. V **nový projekt** dialogového okna, vyberte **Visual C#**   >  **Konzolová aplikace (.NET Framework)**, pojmenujte svůj projekt *AzureCosmosDBApp* a pak vyberte **OK**.
   
   ![Snímek obrazovky s oknem Nový projekt](./media/sql-api-get-started/nosql-tutorial-new-project-2.png)
   
1. V **Průzkumníka řešení**, klikněte pravým tlačítkem myši **AzureCosmosDBApp** projektu a vyberte **spravovat balíčky NuGet**.
   
   ![Kontextové nabídky projektu](./media/sql-api-get-started/nosql-tutorial-manage-nuget-pacakges.png)
   
1. Na **NuGet** kartu, vyberte možnost **Procházet**a zadejte *azure documentdb* do vyhledávacího pole.
   
1. Vyhledejte a vyberte **Microsoft.Azure.DocumentDB**a vyberte **nainstalovat** Pokud ještě není nainstalovaná.
   
   ID balíčku s klientskou knihovnou rozhraní Azure Cosmos DB SQL API je [Microsoft Azure Cosmos DB Client Library](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/).
   
   ![Snímek obrazovky nabídky NuGet pro vyhledání Azure Cosmos DB Client SDK](./media/sql-api-get-started/nosql-tutorial-manage-nuget-pacakges-2.png)
   
   Pokud se zobrazí zpráva o zobrazení náhledu změn řešení, vyberte **OK**. Pokud se zobrazí zpráva týkající se přijetí licence, vyberte **lze nastavit přijímání**.

## <a id="Connect"></a>Připojte se k účtu Azure Cosmos DB

Nyní začněte psát nějaký kód. Kompletní *Project.cs* soubor pro tento kurz je určen v [Githubu](https://github.com/Azure-Samples/documentdb-dotnet-getting-started/blob/master/src/Program.cs).

1. V **Průzkumníka řešení**vyberte *Program.cs*a v editoru kódu přidejte následující odkazy na začátek souboru:
   
   ```csharp
   using System.Net;
   using Microsoft.Azure.Documents;
   using Microsoft.Azure.Documents.Client;
   using Newtonsoft.Json;
   ```
   
1. V dalším kroku přidejte následující dvě konstanty a `client` proměnnou `public class Program`.
   
   ```csharp
   
   public class Program
   {
      private const string EndpointUrl = "<your endpoint URL>";
      private const string PrimaryKey = "<your primary key>";
      private DocumentClient client;
   ```
   
1. Adresa URL koncového bodu a primární klíč povolit pro připojení k účtu služby Azure Cosmos DB a účet služby Azure Cosmos DB důvěřovala připojení aplikace. Zkopírování klíče z [webu Azure portal](https://portal.azure.com)a vložte je do vašeho kódu. 

   
   1. Ve vaší službě Azure Cosmos DB účet navigačním panelu vlevo vyberte **klíče**.
      
      ![Zobrazení a zkopírování přístupových klíčů na webu Azure Portal](./media/sql-api-get-started/nosql-tutorial-keys.png)
      
   1. V části **klíče pro čtení i zápis**, kopie **URI** hodnotu pomocí tlačítka pro kopírování na pravé straně a vložte ho do `<your endpoint URL>` v *Program.cs*. Příklad: 
      
      `private const string EndpointUrl = "https://mysqlapicosmosdb.documents.azure.com:443/";`
      
   1. Kopírovat **primární klíč** hodnotu a vložte ho do `<your primary key>` v *Program.cs*. Příklad: 
      
      `private const string PrimaryKey = "19ZDNJAiYL26tmnRvoez6hmtIfBGwjun50PWRjNYMC2ig8Ob9hYk7Fq1RYSv8FcIYnh1TdBISvCh7s6yyb0000==";`
   
1. Po `Main` metodu, přidejte nový asynchronní úkol pojmenovaný `GetStartedDemo`, který vytvoří nový `DocumentClient` volá `client`.
   
   ```csharp
      private async Task GetStartedDemo()
      {
        client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey);
      }
   ```
   
1. Přidejte následující kód, který `Main` způsob spuštění `GetStartedDemo` úloh. `Main` Metoda zachytí výjimky a zapisuje je do konzole.
   
   ```csharp
      static void Main(string[] args)
      {
        try
        {
           Program p = new Program();
           p.GetStartedDemo().Wait();
        }
        catch (DocumentClientException de)
        {
           Exception baseException = de.GetBaseException();
           Console.WriteLine($"{de.StatusCode} error occurred: {de.Message}, Message: {baseException.Message}");
        }
        catch (Exception e)
        {
           Exception baseException = e.GetBaseException();
           Console.WriteLine($"Error: {e.Message}, Message: {baseException.Message}");
        }
        finally
        {
           Console.WriteLine("End of demo, press any key to exit.");
           Console.ReadKey();
        }
      }
   ```
   
1. Stisknutím klávesy **F5** ke spouštění vaší aplikace. 
   
1. Když se zobrazí zpráva **koncové ukázky, stisknutím libovolné klávesy ukončete** v okně konzoly, to znamená, že připojení bylo úspěšné. Stisknutím jakékoli klávesy zavřete okno konzoly. 

Úspěšně jste se připojili k účtu služby Azure Cosmos DB. Teď pracovat s některými prostředky služby Azure Cosmos DB.  

## <a name="create-a-database"></a>Vytvoření databáze

Azure Cosmos DB [databáze](databases-containers-items.md#azure-cosmos-databases) je logický kontejner úložiště dokumentů JSON rozděleného mezi kolekcemi. Vytvořit databázi s použitím [CreateDatabaseIfNotExistsAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdatabaseifnotexistsasync) metodu `DocumentClient` třídy. 

1. Než přidáte kód pro vytvoření databáze, přidejte pomocnou metodu pro výpis do konzoly. Zkopírujte a vložte následující `WriteToConsoleAndPromptToContinue` za `GetStartedDemo` metodě v kódu.
   
   ```csharp
   private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
   {
      Console.WriteLine(format, args);
      Console.WriteLine("Press any key to continue...");
      Console.ReadKey();
   }
   ```
   
1. Zkopírujte a vložte následující řádek, který vaše `GetStartedDemo` metoda, poté, co `client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey);` řádku. Tento kód vytvoří databázi s názvem `FamilyDB`.
   
   ```csharp
      await client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB" });
   ```
   
1. Stisknutím klávesy **F5** ke spouštění vaší aplikace.

Úspěšně jste vytvořili databázi Azure Cosmos DB. Zobrazí se v databázi [webu Azure portal](https://portal.azure.com) tak, že vyberete **Průzkumník dat** v levém navigačním vaší služby Azure Cosmos DB účtu. 

## <a id="CreateColl"></a>Vytvoření kolekce

Kolekce je kontejner dokumentů JSON a přidružené logiky javascriptové aplikace. Vytvoření kolekce pomocí [CreateDocumentCollectionIfNotExistsAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentcollectionifnotexistsasync#overloads) metodu `DocumentClient` třídy. 

> [!IMPORTANT]
> **CreateDocumentCollectionIfNotExistsAsync** vytvoří novou kolekci s vyhrazenou propustností, kterou se hradí. Další informace najdete [stránce s cenami](https://azure.microsoft.com/pricing/details/cosmos-db/).
> 

1. Zkopírujte a vložte následující kód do vašeho `GetStartedDemo` za `await client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB" });` řádku. Tento kód vytvoří kolekce dokumentů s názvem `FamilyCollection`.
   
   ```csharp
      await client.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("FamilyDB"), new DocumentCollection { Id = "FamilyCollection" });
   ```
   
1. Stisknutím klávesy **F5** ke spouštění vaší aplikace.

Úspěšně jste vytvořili kolekci dokumentů Azure Cosmos DB. Zobrazí se kolekce v části vašeho **FamilyDB** databáze v **Průzkumník dat** na webu Azure Portal.  

## <a id="CreateDoc"></a>Vytvoření dokumentů JSON

Dokumenty představují uživatelem definovaný, libovolný obsah JSON. Dokumenty musí mít vlastnost ID serializován jako `id` ve formátu JSON. Vytváření dokumentů s použitím [CreateDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentasync#overloads) metodu `DocumentClient` třídy. 

> [!TIP]
> Pokud již máte data, která chcete uložit do databáze, můžete použít rozhraní Azure Cosmos DB [nástroj pro migraci dat](import-data.md) importujte ho.
>

Následující kód vytvoří a vloží dva dokumenty v databázové kolekci. Nejprve je potřeba vytvořit `Family` třídy, a `Parent`, `Child`, `Pet`, a `Address` podtřídy pro použití v rámci `Family`. Pak vytvoříte `CreateFamilyDocumentIfNotExists` metoda a pak vytvořit a vložte dva dokumenty. 

1. Zkopírujte a vložte následující `Family`, `Parent`, `Child`, `Pet`, a `Address` třídy po `WriteToConsoleAndPromptToContinue` metodě v kódu.
   
   ```csharp
    public class Family
    {
        [JsonProperty(PropertyName = "id")]
        public string Id { get; set; }
        public string LastName { get; set; }
        public Parent[] Parents { get; set; }
        public Child[] Children { get; set; }
        public Address Address { get; set; }
        public bool IsRegistered { get; set; }
        public override string ToString()
        {
            return JsonConvert.SerializeObject(this);
        }
    }

    public class Parent
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
    }

    public class Child
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
        public string Gender { get; set; }
        public int Grade { get; set; }
        public Pet[] Pets { get; set; }
    }

    public class Pet
    {
        public string GivenName { get; set; }
    }

    public class Address
    {
        public string State { get; set; }
        public string County { get; set; }
        public string City { get; set; }
    }
   ```
   
1. Zkopírujte a vložte následující `CreateFamilyDocumentIfNotExists` za `Address` třídy, kterou jste právě přidali.
   
   ```csharp
    private async Task CreateFamilyDocumentIfNotExists(string databaseName, string collectionName, Family family)
    {
        try
        {
            await client.ReadDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, family.Id));
            WriteToConsoleAndPromptToContinue($"Found {family.Id}");
        }
        catch (DocumentClientException de)
        {
            if (de.StatusCode == HttpStatusCode.NotFound)
            {
                await client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), family);
                WriteToConsoleAndPromptToContinue($"Created Family {family.Id}");
            }
            else
            {
                throw;
            }
        }
    }
   ```
   
1. Zkopírujte a vložte následující kód na konci vaší `GetStartedDemo` metoda, poté, co `await client.CreateDocumentCollectionIfNotExistsAsync` řádku. Tento kód vytvoří a vloží dva dokumenty, jeden pro rodinu a Wakefieldů rodiny.
   
   ```csharp
    Family andersenFamily = new Family
    {
        Id = "AndersenFamily",
        LastName = "Andersen",
        Parents = new Parent[]
        {
            new Parent { FirstName = "Thomas" },
            new Parent { FirstName = "Mary Kay" }
        },
        Children = new Child[]
        {
            new Child
            {
                FirstName = "Henriette Thaulow",
                Gender = "female",
                Grade = 5,
                Pets = new Pet[]
                {
                    new Pet { GivenName = "Fluffy" }
                }
            }
        },
        Address = new Address { State = "WA", County = "King", City = "Seattle" },
        IsRegistered = true
    };

    await CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", andersenFamily);

    Family wakefieldFamily = new Family
    {
        Id = "WakefieldFamily",
        LastName = "Wakefield",
        Parents = new Parent[]
        {
            new Parent { FamilyName = "Wakefield", FirstName = "Robin" },
            new Parent { FamilyName = "Miller", FirstName = "Ben" }
        },
        Children = new Child[]
        {
            new Child
            {
                FamilyName = "Merriam",
                FirstName = "Jesse",
                Gender = "female",
                Grade = 8,
                Pets = new Pet[]
                {
                    new Pet { GivenName = "Goofy" },
                    new Pet { GivenName = "Shadow" }
                }
            },
            new Child
            {
                FamilyName = "Miller",
                FirstName = "Lisa",
                Gender = "female",
                Grade = 1
            }
        },
        Address = new Address { State = "NY", County = "Manhattan", City = "NY" },
        IsRegistered = false
    };

    await CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", wakefieldFamily);
   ```
   
1. Stisknutím klávesy **F5** ke spouštění vaší aplikace.

Úspěšně jste vytvořili dva dokumenty Azure Cosmos DB. Můžete zobrazit dokumenty v rámci vaší **FamilyDB** databáze a **FamilyCollection** kolekce v **Průzkumník dat** na webu Azure Portal.   

![Diagram ilustrující hierarchický vztah mezi účet, online databáze, kolekce a dokumenty](./media/sql-api-get-started/nosql-tutorial-account-database.png)

## <a id="Query"></a>Dotazování prostředků Azure Cosmos DB

Azure Cosmos DB podporuje bohaté [dotazy](how-to-sql-query.md) na dokumenty JSON uložené v kolekcích. Následující vzorový kód používá syntaxi LINQ a Azure Cosmos DB SQL spustit dotaz pro ukázkové dokumenty.

1. Zkopírujte a vložte následující `ExecuteSimpleQuery` za `CreateFamilyDocumentIfNotExists` metodě v kódu.
   
   ```csharp
    private void ExecuteSimpleQuery(string databaseName, string collectionName)
    {
        // Set some common query options.
        FeedOptions queryOptions = new FeedOptions { MaxItemCount = -1 };

        // Find the Andersen family by its LastName.
        IQueryable<Family> familyQuery = client.CreateDocumentQuery<Family>(
            UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), queryOptions)
            .Where(f => f.LastName == "Andersen");

        // Execute the query synchronously. 
        // You could also execute it asynchronously using the IDocumentQuery<T> interface.
        Console.WriteLine("Running LINQ query...");
        foreach (Family family in familyQuery)
        {
            Console.WriteLine($"\tRead {family}");
        }

        // Now execute the same query using direct SQL.
        IQueryable<Family> familyQueryInSql = client.CreateDocumentQuery<Family>(
            UriFactory.CreateDocumentCollectionUri(databaseName, collectionName),
            "SELECT * FROM Family WHERE Family.LastName = 'Andersen'",
            queryOptions);

        Console.WriteLine("Running direct SQL query...");
        foreach (Family family in familyQueryInSql)
        {
            Console.WriteLine($"\tRead {family}");
        }

        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
    }
   ```
   
1. Zkopírujte a vložte následující kód na konci vaší `GetStartedDemo` metoda, poté, co `await CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", wakefieldFamily);` řádku.
   
   ```csharp
      ExecuteSimpleQuery("FamilyDB", "FamilyCollection");
   ```
   
1. Stisknutím klávesy **F5** ke spouštění vaší aplikace.

Předchozí dotaz vrátí celou položku pro rodinu. Jste úspěšně dotázán proti kolekci Azure Cosmos DB.

Následující diagram znázorňuje, jakým způsobem volá syntaxe dotazu Azure Cosmos DB SQL proti kolekci. Stejná logika platí pro dotaz LINQ.

![Diagram ilustrující obor a význam dotazu použitého v kurzu NoSQL k vytvoření konzolové aplikace v jazyce C#](./media/sql-api-get-started/nosql-tutorial-collection-documents.png)

[FROM](how-to-sql-query.md#FromClause) – klíčové slovo v dotazu SQL je volitelný, protože dotazy služby Azure Cosmos DB již mají obor nastaven na jedinou kolekci. Můžete zaměnit `FROM Families f` s `FROM root r`, nebo jiné proměnné název, který zvolíte. Azure Cosmos DB, která odvodí `Families`, `root`, nebo název proměnné, zvolíte odkazuje na aktuální kolekci.

## <a id="ReplaceDocument"></a>Aktualizace dokumentu JSON

SQL API služby Azure Cosmos DB podporuje aktualizaci a nahrazování dokumentů JSON.  

1. Zkopírujte a vložte následující `ReplaceFamilyDocument` za `ExecuteSimpleQuery` metodě v kódu.
   
   ```csharp
    private async Task ReplaceFamilyDocument(string databaseName, string collectionName, string familyName, Family updatedFamily)
    {
       await client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, familyName), updatedFamily);
       WriteToConsoleAndPromptToContinue($"Replaced Family {familyName}");
    }
   ```
   
1. Zkopírujte a vložte následující kód na konci vaší `GetStartedDemo` metoda, poté, co `ExecuteSimpleQuery("FamilyDB", "FamilyCollection");` řádku. Kód aktualizuje data v jednom z dokumentů a pak spustí dotaz znovu zobrazíte změněný dokument.
   
   ```csharp
   // Update the Grade of the Andersen Family child
   andersenFamily.Children[0].Grade = 6;
   await ReplaceFamilyDocument("FamilyDB", "FamilyCollection", "AndersenFamily", andersenFamily);
   ExecuteSimpleQuery("FamilyDB", "FamilyCollection");
   ```
   
1. Stisknutím klávesy **F5** ke spouštění vaší aplikace.

Výstup dotazu, který ukazuje `Grade` pro dítě řady Andersen aktualizováno z hodnoty `5` k `6`. Úspěšně jste aktualizován a nahradí dokument Azure Cosmos DB. 

## <a id="DeleteDocument"></a>Odstranění dokumentu JSON

SQL API služby Azure Cosmos DB podporuje odstraňování dokumentů JSON.  

1. Zkopírujte a vložte následující `DeleteFamilyDocument` za `ReplaceFamilyDocument` metody.
   
   ```csharp
    private async Task DeleteFamilyDocument(string databaseName, string collectionName, string documentName)
    {
        await client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, documentName));
        Console.WriteLine($"Deleted Family {documentName}");
    }
   ```
   
1. Zkopírujte a vložte následující kód na konci vaší `GetStartedDemo` metoda po druhé `ExecuteSimpleQuery("FamilyDB", "FamilyCollection");` řádku.
   
   ```csharp
   await DeleteFamilyDocument("FamilyDB", "FamilyCollection", "AndersenFamily");
   ```
   
1. Stisknutím klávesy **F5** ke spouštění vaší aplikace.

Úspěšně jste odstranili dokument Azure Cosmos DB. 

## <a id="DeleteDatabase"></a>Odstranění databáze

Odstraňte databázi, kterou jste vytvořili, odeberte ji a všechny její podřízené prostředky, včetně kolekce a dokumenty. 

1. Zkopírujte a vložte následující kód na konci vaší `GetStartedDemo` metoda, poté, co `await DeleteFamilyDocument("FamilyDB", "FamilyCollection", "AndersenFamily");` řádku. 
   
   ```csharp
   // Clean up - delete the database
   await client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri("FamilyDB"));
   ```
   
1. Stisknutím klávesy **F5** ke spouštění vaší aplikace.

Úspěšně jste odstranili databázi Azure Cosmos DB. Je vidět **Průzkumník dat** pro váš účet Azure Cosmos DB, databáze FamilyDB se odstraní. 

## <a id="Run"></a>Spustit celou C# Konzolová aplikace

Stisknutím klávesy **F5** v sadě Visual Studio sestavte a spusťte kompletní C# konzolovou aplikaci v režimu ladění. Byste měli vidět následující výstup v okně konzoly:

```bash
Created Family AndersenFamily
Press any key to continue ...
 Created Family WakefieldFamily
Press any key to continue ...
 Running LINQ query...
        Read {"id":"AndersenFamily","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Running direct SQL query...
        Read {"id":"AndersenFamily","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Press any key to continue ...
 Replaced Family AndersenFamily
Press any key to continue ...
 Running LINQ query...
        Read {"id":"AndersenFamily","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Running direct SQL query...
        Read {"id":"AndersenFamily","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Press any key to continue ...
 Deleted Family AndersenFamily
End of demo, press any key to exit.
```

Blahopřejeme! Dokončili jste kurz a máte funkční C# konzolovou aplikaci, která vytvoří, dotazy, aktualizace a odstraní prostředky Azure Cosmos DB.  

## <a name="next-steps"></a>Další postup
* Další informace o Cosmos Azure DB najdete v tématu [Vítá vás Azure Cosmos DB](introduction.md).
* Pro složitější kurz k ASP.NET MVC naleznete v tématu [kurz ASP.NET MVC: Vývoj webových aplikací pomocí služby Azure Cosmos DB](sql-api-dotnet-application.md).
* Pokud chcete testovat škálování a výkon pomocí služby Azure Cosmos DB, přečtěte si téma [testování pomocí služby Azure Cosmos DB výkonu a škálování](performance-testing.md).
* Další informace o monitorování požadavků, využití a úložiště Azure Cosmos DB, najdete v článku [monitorování účtů](monitor-accounts.md).
* Spouštějte dotazy proti ukázkovým datovým sadám v [Query Playground](https://www.documentdb.com/sql/demo).

