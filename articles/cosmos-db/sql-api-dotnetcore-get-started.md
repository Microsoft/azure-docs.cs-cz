---
title: 'Azure Cosmos DB: Úvodní kurz k .NET Core pro rozhraní SQL API | Microsoft Docs'
description: Kurz, v rámci kterého se vytvoří online databáze a konzolová aplikace v jazyce C# pomocí sady Azure Cosmos DB SQL API .NET Core SDK.
services: cosmos-db
author: SnehaGunda
manager: kfile
editor: ''
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 03/12/2018
ms.author: sngun
ms.custom: devcenter
ms.openlocfilehash: 2cbfa3662f6db7dc44d142d5fa13275aed359be7
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2018
ms.locfileid: "50740395"
---
# <a name="tutorial-build-a-net-core-app-to-manage-azure-cosmos-db-sql-api-data"></a>Kurz: Vytvoření aplikace .Net Core pro správu dat rozhraní SQL API služby Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

V tomto kurzu se dozvíte, jak vytvořit aplikaci .Net Core pro vytváření a dotazování dat rozhraní SQL API služby Azure Cosmos DB. 

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Vytvoření účtu služby Azure Cosmos DB a připojení k němu
> * Konfigurace řešení v nástroji Visual Studio
> * Vytvoření online databáze
> * Vytvoření kolekce
> * Vytvoření dokumentů JSON
> * Provádění operací CRUD s položkami, kontejnerem a databází

Nemáte čas aplikaci vytvářet? Nevadí! Úplné řešení je k dispozici na [GitHubu](https://github.com/Azure-Samples/documentdb-dotnet-core-getting-started). Pro rychlé pokyny přeskočte na [oddíl Získání úplného řešení](#GetSolution).

Chcete sestavit aplikaci v Xamarinu pro iOS, Android nebo Forms pomocí rozhraní SQL API a sady .NET Core SDK? Přečtěte si [vytváření mobilních aplikací s Xamarinem a Azure Cosmos DB](mobile-apps-with-xamarin.md).

## <a name="prerequisites"></a>Požadavky

* Aktivní účet Azure. Pokud žádný nemáte, můžete si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* Pokud ještě nemáte nainstalovanou sadu Visual Studio 2017, můžete stáhnout a použít bezplatnou verzi [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Pokud vyvíjíte aplikace pro práci s univerzální platformou Windows (UPW), měli byste použít **Visual Studio 2017 s verzí 15.4** nebo vyšší. Nezapomeňte při instalaci sady Visual Studio povolit možnost **Azure Development**.

    * Pokud pracujete v systému MacOS nebo Linux, můžete vyvíjet aplikace .NET Core z příkazového řádku instalací sady [.NET Core SDK](https://www.microsoft.com/net/core#macos) pro vámi zvolenou platformu. 

    * Pokud pracujete v systému Windows, můžete vyvíjet aplikace .NET Core z příkazového řádku instalací sady [.NET Core SDK](https://www.microsoft.com/net/core#windows). 

    * Můžete použít vlastní editor nebo si bezplatně stáhnout editor [Visual Studio Code](https://code.visualstudio.com/), který funguje na systémech Windows, Linux a MacOS. 

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Krok 1: Vytvoření účtu služby Azure Cosmos DB

Vytvořme účet služby Azure Cosmos DB. Pokud již máte účet, který chcete použít, můžete přeskočit na [Nastavení řešení v nástroji Visual Studio](#SetupVS). Pokud používáte emulátor služby Azure Cosmos DB, nastavte emulátor pomocí postupu v tématu [Emulátor služby Azure Cosmos DB](local-emulator.md) a přeskočte k části [Nastavení řešení v sadě Visual Studio](#SetupVS).

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupVS"></a>Krok 2: Nastavení řešení v sadě Visual Studio

1. Otevřete v počítači **Visual Studio 2017**.

2. V nabídce **Soubor** vyberte **Nový** a zvolte **Projekt**.

3. V dialogovém okně **Nový projekt** vyberte **Šablony** / **Visual C#** / **.NET Core**/**Konzolová aplikace (.NET Core)**, pojmenujte projekt **DocumentDBGettingStarted** a pak vyberte **OK**.

   ![Snímek obrazovky s oknem Nový projekt](./media/sql-api-dotnetcore-get-started/nosql-tutorial-new-project-2.png)

4. V **Průzkumníku řešení** klikněte pravým tlačítkem myši na **DocumentDBGettingStarted**.

5. Ve stejné nabídce vyberte **Spravovat balíčky NuGet**.

   ![Snímek obrazovky místní nabídky projektu](./media/sql-api-dotnetcore-get-started/nosql-tutorial-manage-nuget-pacakges.png)

6. Na kartě **NuGet** vyberte v horní části okna **Procházet** a do vyhledávacího pole zadejte **azure documentdb**.

7. Ve výsledcích vyhledejte **Microsoft.Azure.DocumentDB.Core** a vyberte **Nainstalovat**.

   ID balíčku knihovny je [Microsoft.Azure.DocumentDB.Core](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core). Pokud cílíte na verzi rozhraní .NET Framework (třeba net461), kterou tento balíček .NET Core NuGet nepodporuje, použijte [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB) podporující všechny verze rozhraní .NET Framework počínaje verzí .NET Framework 4.5.

8. Po zobrazení výzvy přijměte instalaci balíčku NuGet a licenční smlouvu.

Výborně! Teď, když je instalace dokončená, napíšeme nějaký kód. Úplný projekt s kódem pro tento kurz najdete na [GitHubu](https://github.com/Azure-Samples/documentdb-dotnet-core-getting-started).

## <a id="Connect"></a>Krok 3: Připojení k účtu služby Azure Cosmos DB

Importujte požadované závislosti přidáním následujícího kódu na začátek aplikace jazyka C# do souboru Program.cs:

```csharp
using System;

// ADD THIS PART TO YOUR CODE
using System.Linq;
using System.Threading.Tasks;
using System.Net;
using Microsoft.Azure.Documents;
using Microsoft.Azure.Documents.Client;
using Newtonsoft.Json;
```

Nyní přidejte tyto dvě konstanty a proměnnou *client* pod veřejnou třídu *Program*.

```csharp
class Program
{
    // ADD THIS PART TO YOUR CODE
    private const string EndpointUri = "<your endpoint URI>";
    private const string PrimaryKey = "<your key>";
    private DocumentClient client;
```

Dále přejděte na web [Azure Portal](https://portal.azure.com) a získejte identifikátor URI a primární klíč. Identifikátor URI a primární klíč služby Azure Cosmos DB jsou potřeba k tomu, aby aplikace věděla, kam se má připojit, a aby služba Azure Cosmos DB důvěřovala připojení aplikace.

Na webu Azure Portal přejděte ke svému účtu služby Azure Cosmos DB a vyberte **Klíče**.

Zkopírujte identifikátor URI z portálu a vložte ho do `<your endpoint URI>` v souboru program.cs. Poté zkopírujte PRIMÁRNÍ KLÍČ z portálu a vložte ho do `<your key>`. Pokud používáte emulátor služby Azure Cosmos DB, použijte jako koncový bod `https://localhost:8081` a dobře definovaný autorizační klíč z tématu [Postup vývoje pomocí emulátoru služby Azure Cosmos DB](local-emulator.md). Nezapomeňte odstranit znaky < a >, ale ponechte uvozovky kolem vašeho koncového bodu a klíče.

![Získání klíčů z webu Azure Portal][keys]

Vytvořením nové instance **DocumentClient** spustíme počáteční aplikaci.

Pod metodu **Main** přidejte tento nový asynchronní úkol **GetStartedDemo**, který vytvoří instanci nového klienta **DocumentClient**.

```csharp
static void Main(string[] args)
{
}

// ADD THIS PART TO YOUR CODE
private async Task GetStartedDemo()
{
    this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);
}
```

Přidejte následující kód, aby se asynchronní úkol spustil z metody **Main**. Metoda **Main** zachycuje výjimky a vypisuje je do konzoly.

```csharp
static void Main(string[] args)
{
        // ADD THIS PART TO YOUR CODE
        try
        {
                Program p = new Program();
                p.GetStartedDemo().Wait();
        }
        catch (DocumentClientException de)
        {
                Exception baseException = de.GetBaseException();
                Console.WriteLine("{0} error occurred: {1}, Message: {2}", de.StatusCode, de.Message, baseException.Message);
        }
        catch (Exception e)
        {
                Exception baseException = e.GetBaseException();
                Console.WriteLine("Error: {0}, Message: {1}", e.Message, baseException.Message);
        }
        finally
        {
                Console.WriteLine("End of demo, press any key to exit.");
                Console.ReadKey();
        }
```

Výběrem tlačítka **DocumentDBGettingStarted** sestavte a spusťte aplikaci.

Blahopřejeme! Úspěšně jste se připojili k účtu služby Azure Cosmos DB. Nyní se podívejme, jak se pracuje s prostředky Azure Cosmos DB.  

## <a name="step-4-create-a-database"></a>Krok 4: Vytvoření databáze

Než přidáte kód pro vytvoření databáze, přidejte pomocnou metodu pro výpis do konzoly.

Zkopírujte a vložte metodu **WriteToConsoleAndPromptToContinue** pod metodu **GetStartedDemo**.

```csharp
// ADD THIS PART TO YOUR CODE
private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
{
        Console.WriteLine(format, args);
        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
}
```

[Databázi](sql-api-resources.md#databases) Azure Cosmos DB je možné vytvořit pomocí metody [CreateDatabaseAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) třídy **DocumentClient**. Databáze je logický kontejner úložiště dokumentů JSON rozděleného mezi kolekcemi.

Zkopírujte a vložte následující kód do metody **GetStartedDemo** pod vytvoření klienta. Tím se vytvoří databáze s názvem *FamilyDB*.

```csharp
private async Task GetStartedDemo()
{
    this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);

    // ADD THIS PART TO YOUR CODE
    await this.client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB_oa" });
```

Výběrem tlačítka **DocumentDBGettingStarted** spusťte aplikaci.

Blahopřejeme! Úspěšně jste vytvořili databázi Azure Cosmos DB.  

## <a id="CreateColl"></a>Krok 5: Vytvoření kolekce

> [!WARNING]
> **CreateDocumentCollectionAsync** vytvoří novou kolekci s vyhrazenou propustností, za kterou se hradí poplatky. Další podrobnosti najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/cosmos-db/).

[Kolekci](sql-api-resources.md#collections) je možné vytvořit pomocí metody [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx) třídy **DocumentClient**. Kolekce je kontejner dokumentů JSON a přidružené logiky javascriptové aplikace.

Zkopírujte a vložte následující kód do metody **GetStartedDemo** pod vytvoření databáze. Tento kód vytvoří kolekci dokumentů s názvem *FamilyCollection_oa*.

```csharp
    this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);

    await this.client.CreateDatabaseIfNotExistsAsync("FamilyDB_oa");

    // ADD THIS PART TO YOUR CODE
    await this.client.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("FamilyDB_oa"), new DocumentCollection { Id = "FamilyCollection_oa" });
```

Výběrem tlačítka **DocumentDBGettingStarted** spusťte aplikaci.

Blahopřejeme! Úspěšně jste vytvořili kolekci dokumentů Azure Cosmos DB.  

## <a id="CreateDoc"></a>Krok 6: Vytvoření dokumentů JSON

[Dokument](sql-api-resources.md#documents) je možné vytvořit pomocí metody [CreateDocumentAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx) třídy **DocumentClient**. Dokumenty představují uživatelem definovaný (libovolný) obsah JSON. Nyní můžete vložit jeden nebo více dokumentů. Pokud již máte data, která chcete uložit do databáze, můžete použít [nástroj pro migraci dat](import-data.md) Azure Cosmos DB.

Nejprve vytvoříte třídu **Family**, která představuje objekty uložené ve službě Azure Cosmos DB. Kromě toho vytvoříte i podtřídy **Parent**, **Child**, **Pet** a **Address**, které se použijí v rámci **Family**. Dokumenty musí mít vlastnost **Id** serializovanou jako **id** ve formátu JSON. Vytvořte tyto třídy tak, že za metodu **GetStartedDemo** přidáte následující vnitřní podtřídy.

Zkopírujte a vložte třídy **Family**, **Parent**, **Child**, **Pet** a **Address** pod metodu **WriteToConsoleAndPromptToContinue**.

```csharp
private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
{
    Console.WriteLine(format, args);
    Console.WriteLine("Press any key to continue ...");
    Console.ReadKey();
}

// ADD THIS PART TO YOUR CODE
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

Zkopírujte a vložte metodu **CreateFamilyDocumentIfNotExists** pod metodu **CreateDocumentCollectionIfNotExists**.

```csharp
// ADD THIS PART TO YOUR CODE
private async Task CreateFamilyDocumentIfNotExists(string databaseName, string collectionName, Family family)
{
    try
    {
        await this.client.ReadDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, family.Id));
        this.WriteToConsoleAndPromptToContinue("Found {0}", family.Id);
    }
    catch (DocumentClientException de)
    {
        if (de.StatusCode == HttpStatusCode.NotFound)
        {
            await this.client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), family);
            this.WriteToConsoleAndPromptToContinue("Created Family {0}", family.Id);
        }
        else
        {
            throw;
        }
    }
}
```

Vložte dva dokumenty, jeden pro rodinu Andersenů a druhý pro rodinu Wakefieldů.

Zkopírujte a vložte kód následující za `// ADD THIS PART TO YOUR CODE` do vaší metody **GetStartedDemo** pod vytvoření kolekce dokumentů.

```csharp
await this.CreateDatabaseIfNotExistsAsync("FamilyDB_oa");

await this.CreateDocumentCollectionIfNotExists("FamilyDB_oa", "FamilyCollection_oa");

// ADD THIS PART TO YOUR CODE
Family andersenFamily = new Family
{
        Id = "Andersen.1",
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

await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", andersenFamily);

Family wakefieldFamily = new Family
{
        Id = "Wakefield.7",
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

await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", wakefieldFamily);
```

Výběrem tlačítka **DocumentDBGettingStarted** spusťte aplikaci.

Blahopřejeme! Úspěšně jste vytvořili dva dokumenty Azure Cosmos DB.  

![Hierarchický vztah mezi účtem, online databází a kolekcí](./media/sql-api-dotnetcore-get-started/nosql-tutorial-account-database.png)

## <a id="Query"></a>Krok 7: Dotazování prostředků Azure Cosmos DB

Azure Cosmos DB podporuje bohaté [dotazy](sql-api-sql-query.md) na dokumenty JSON uložené v každé z kolekcí. Následující ukázkový kód ukazuje různé dotazy – používající jak syntaxi SQL služby Azure Cosmos DB, tak LINQ – které můžeme spouštět na dokumenty vložené v předchozím kroku.

Zkopírujte a vložte metodu **ExecuteSimpleQuery** pod metodu **CreateFamilyDocumentIfNotExists**.

```csharp
// ADD THIS PART TO YOUR CODE
private void ExecuteSimpleQuery(string databaseName, string collectionName)
{
    // Set some common query options
    FeedOptions queryOptions = new FeedOptions { MaxItemCount = -1 };

        // Here we find the Andersen family via its LastName
        IQueryable<Family> familyQuery = this.client.CreateDocumentQuery<Family>(
                UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), queryOptions)
                .Where(f => f.LastName == "Andersen");

        // The query is executed synchronously here, but can also be executed asynchronously via the IDocumentQuery<T> interface
        Console.WriteLine("Running LINQ query...");
        foreach (Family family in familyQuery)
        {
            Console.WriteLine("\tRead {0}", family);
        }

        // Now execute the same query via direct SQL
        IQueryable<Family> familyQueryInSql = this.client.CreateDocumentQuery<Family>(
                UriFactory.CreateDocumentCollectionUri(databaseName, collectionName),
                "SELECT * FROM Family WHERE Family.LastName = 'Andersen'",
                queryOptions);

        Console.WriteLine("Running direct SQL query...");
        foreach (Family family in familyQueryInSql)
        {
                Console.WriteLine("\tRead {0}", family);
        }

        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
}
```

Zkopírujte a vložte následující kód do metody **GetStartedDemo** pod vytvoření druhého dokumentu.

```csharp
await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", wakefieldFamily);

// ADD THIS PART TO YOUR CODE
this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");
```

Výběrem tlačítka **DocumentDBGettingStarted** spusťte aplikaci.

Blahopřejeme! Úspěšně jste provedli dotaz na kolekci Azure Cosmos DB.

Následující diagram ilustruje volání syntaxe příkazu jazyka SQL služby Azure Cosmos DB na kolekci, kterou jste vytvořili. Stejná logika platí také pro dotaz LINQ.

![Diagram ilustrující obor a význam dotazu použitého v kurzu NoSQL k vytvoření konzolové aplikace v jazyce C#](./media/sql-api-dotnetcore-get-started/nosql-tutorial-collection-documents.png)

Klíčové slovo [FROM](sql-api-sql-query.md#FromClause) je v dotazu volitelné, protože dotazy Azure Cosmos DB již mají obor nastaven na jedinou kolekci. Proto je možné příkaz „FROM Families f“ vyměnit za „FROM root r“ nebo jakoukoli jinou proměnnou, kterou si zvolíte. Azure Cosmos DB standardně vyvodí, že Families, root nebo zvolený název proměnné odkazují na aktuální kolekci.

## <a id="ReplaceDocument"></a>Krok 8: Nahrazení dokumentu JSON

Azure Cosmos DB podporuje nahrazování dokumentů JSON.  

Zkopírujte a vložte metodu **ReplaceFamilyDocument** pod metodu **ExecuteSimpleQuery**.

```csharp
// ADD THIS PART TO YOUR CODE
private async Task ReplaceFamilyDocument(string databaseName, string collectionName, string familyName, Family updatedFamily)
{
    await this.client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, familyName), updatedFamily);
    this.WriteToConsoleAndPromptToContinue("Replaced Family {0}", familyName);
}
```

Zkopírujte a vložte následující kód do metody **GetStartedDemo** pod spuštění dotazu. Po nahrazení dokumentu tento kód spustí stejný dotaz znovu, aby se zobrazil změněný dokument.

```csharp
await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", wakefieldFamily);

this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

// ADD THIS PART TO YOUR CODE
// Update the Grade of the Andersen Family child
andersenFamily.Children[0].Grade = 6;

await this.ReplaceFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1", andersenFamily);

this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");
```

Výběrem tlačítka **DocumentDBGettingStarted** spusťte aplikaci.

Blahopřejeme! Úspěšně jste nahradili dokument Azure Cosmos DB.

## <a id="DeleteDocument"></a>Krok 9: Odstranění dokumentu JSON

Azure Cosmos DB podporuje odstraňování dokumentů JSON.  

Zkopírujte a vložte metodu **DeleteFamilyDocument** pod metodu **ReplaceFamilyDocument**.

```csharp
// ADD THIS PART TO YOUR CODE
private async Task DeleteFamilyDocument(string databaseName, string collectionName, string documentName)
{
    await this.client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, documentName));
    Console.WriteLine("Deleted Family {0}", documentName);
}
```

Zkopírujte a vložte následující kód do metody **GetStartedDemo** pod spuštění druhého dotazu.

```csharp
await this.ReplaceFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1", andersenFamily);

this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

// ADD THIS PART TO CODE
await this.DeleteFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1");
```

Výběrem tlačítka **DocumentDBGettingStarted** spusťte aplikaci.

Blahopřejeme! Úspěšně jste odstranili dokument Azure Cosmos DB.

## <a id="DeleteDatabase"></a>Krok 10: Odstranění databáze

Odstraněním vytvořené databáze dojde k odstranění databáze a všech jejích podřízených prostředků (kolekcí, dokumentů atd.).

Pokud chcete odstranit celou databázi a její podřízené prostředky, zkopírujte a vložte následující kód do metody **GetStartedDemo** pod odstranění dokumentu.

```csharp
this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

await this.DeleteFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1");

// ADD THIS PART TO CODE
// Clean up/delete the database
await this.client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri("FamilyDB_oa"));
```

Výběrem tlačítka **DocumentDBGettingStarted** spusťte aplikaci.

Blahopřejeme! Úspěšně jste odstranili databázi Azure Cosmos DB.

## <a id="Run"></a>Krok 11: Spuštění konzolové aplikace jazyka C#

Výběrem tlačítka **DocumentDBGettingStarted** v sadě Visual Studio sestavte aplikaci v režimu ladění.

V okně konzoly by se měl zobrazit výstup spuštěné aplikace. Výstup bude zobrazovat výsledky dotazů, které jsme přidali, a měl by odpovídat ukázkovému textu níže.

```
Created FamilyDB_oa
Press any key to continue ...
Created FamilyCollection_oa
Press any key to continue ...
Created Family Andersen.1
Press any key to continue ...
Created Family Wakefield.7
Press any key to continue ...
Running LINQ query...
    Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Running direct SQL query...
    Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Replaced Family Andersen.1
Press any key to continue ...
Running LINQ query...
    Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Running direct SQL query...
    Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Deleted Family Andersen.1
End of demo, press any key to exit.
```

Blahopřejeme! Dokončili jste tento kurz a máte funkční konzolovou aplikaci jazyka C#!

## <a id="GetSolution"></a>Získání úplného řešení kurzu

Abyste mohli sestavit řešení GetStarted, které obsahuje všechny ukázky tohoto článku, budete potřebovat následující:

* Aktivní účet Azure. Pokud žádný nemáte, můžete si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/).
* [Účet služby Azure Cosmos DB][create-sql-api-dotnet.md#create-account].
* Řešení [GetStarted](https://github.com/Azure-Samples/documentdb-dotnet-core-getting-started) dostupné na GitHubu

Pokud chcete obnovit reference na rozhraní SQL API pro sadu Azure Cosmos DB .NET Core SDK v sadě Visual Studio, klikněte v Průzkumníku řešení pravým tlačítkem na řešení **GetStarted** a vyberte **Povolit obnovení balíčků NuGet**. Dále v souboru Program.cs aktualizujte hodnoty EndpointUrl a AuthorizationKey tak, jak je popsáno v části [Připojení k účtu služby Azure Cosmos DB](#Connect).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak vytvořit aplikaci .Net Core pro správu dat rozhraní SQL API služby Azure Cosmos DB. Teď můžete přejít k dalšímu článku:

> [!div class="nextstepaction"]
> [Vytvoření konzolové aplikace v Javě s využitím účtu rozhraní SQL API služby Azure Cosmos DB](sql-api-java-get-started.md)

[create-sql-api-dotnet.md#create-account]: create-sql-api-dotnet.md#create-account
[keys]: media/sql-api-dotnetcore-get-started/nosql-tutorial-keys.png
