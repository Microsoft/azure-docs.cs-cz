---
title: 'Kurz: Vytvoření aplikace .NET Core pro správu dat uložených v účtu rozhraní SQL API pro službu Azure Cosmos DB'
description: V tomto kurzu se vytvoří online databáze a C# konzolové aplikace pomocí sady SQL rozhraní API .NET Core SDK pro službu Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 03/12/2018
ms.author: sngun
Customer intent: As a developer, I want to build a .NET Core application to access and manage Azure Cosmos DB resources so that customers can utilize the global distribution, elastic scaling, multi-master, and other capabilities that Azure Cosmos DB offers.
ms.openlocfilehash: 1aad68a3248561c86e195b55c1d509ab7a15964e
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54035674"
---
# <a name="tutorial-build-a-net-core-app-to-manage-data-stored-in-a-sql-api-account"></a>Kurz: Vytvoření aplikace .NET Core pro správu dat uložených v účtu rozhraní SQL API

> [!div class="op_single_selector"]
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [.NET core (Preview)](sql-api-dotnet-core-get-started-preview.md)
> * [.NET](sql-api-get-started.md)
> * [.NET (preview)](sql-api-dotnet-get-started-preview.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

Jako vývojář můžete mít aplikace, které používají dat dokumentů typu NoSQL. Účet rozhraní SQL API ve službě Azure Cosmos DB můžete použít k ukládání a přístup k těmto datům dokumentu. V tomto kurzu se dozvíte, jak vytvořit aplikaci .NET Core k vytvoření a dotazování dat uložených v účtu rozhraní SQL API služby Azure Cosmos DB. 

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Vytvoření a připojení k účtu Azure Cosmos
> * Konfigurace řešení sady Visual Studio
> * Vytvoření online databáze
> * Vytvoření kolekce
> * Vytvoření dokumentů JSON
> * Provádění operací CRUD s položkami, kontejnerem a databází

Nemáte čas aplikaci vytvářet? Úplné řešení je k dispozici na [GitHubu](https://github.com/Azure-Samples/documentdb-dotnet-core-getting-started). 

## <a name="prerequisites"></a>Požadavky

* Aktivní účet Azure. Pokud žádný nemáte, můžete si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* Stáhnout a použít bezplatnou [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Pokud vyvíjíte aplikace pro práci s univerzální platformou Windows (UPW), měli byste použít **Visual Studio 2017 s verzí 15.4** nebo vyšší. Nezapomeňte při instalaci sady Visual Studio povolit možnost **Azure Development**.

    * Pro MacOS nebo Linux, můžete vyvíjet aplikace .NET Core z příkazového řádku instalací [.NET Core SDK](https://www.microsoft.com/net/core#macos) pro platformu podle vašeho výběru. 

    * Pro Windows, můžete vyvíjet aplikace .NET Core z příkazového řádku instalací [.NET Core SDK](https://www.microsoft.com/net/core#windows). 

## <a name="create-an-azure-cosmos-account"></a>Vytvoření účtu služby Azure Cosmos

Následujícím postupem vytvoření účtu služby Azure Cosmos:

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupVS"></a>Nastavení řešení v sadě Visual Studio

1. Otevřete v počítači **Visual Studio 2017**.

2. V nabídce **Soubor** vyberte **Nový** a zvolte **Projekt**.

3. V dialogovém okně **Nový projekt** vyberte **Šablony** > **Visual C#** > **.NET Core** > **Konzolová aplikace (.NET Core)**, pojmenujte projekt **DocumentDBGettingStarted** a pak vyberte **OK**.

   ![Snímek obrazovky s oknem Nový projekt](./media/sql-api-dotnetcore-get-started/nosql-tutorial-new-project-2.png)

4. V **Průzkumníka řešení**, klikněte pravým tlačítkem na **DocumentDBGettingStarted**.

5. Ve stejné nabídce vyberte **spravovat balíčky NuGet**.

   ![Snímek obrazovky místní nabídky projektu](./media/sql-api-dotnetcore-get-started/nosql-tutorial-manage-nuget-pacakges.png)

6. Na **NuGet** kartu, vyberte možnost **Procházet** v horní části okna a typ **azure documentdb** do vyhledávacího pole.

7. Ve výsledcích hledání **Microsoft.Azure.DocumentDB.Core** a vyberte **nainstalovat**.

   ID balíčku knihovny je [Microsoft.Azure.DocumentDB.Core](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core). Pokud cílíte na verzi rozhraní .NET Framework (třeba net461), kterou tento balíček .NET Core NuGet nepodporuje, použijte [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB), která podporuje všechny verze rozhraní .NET Framework počínaje .NET Framework 4.5.

8. Po zobrazení výzvy přijměte instalaci balíčku NuGet a licenční smlouvu.

Teď, když je instalace dokončená, napíšeme nějaký kód. Dokončený kód v projektu v tomto kurzu lze najít na [Githubu](https://github.com/Azure-Samples/documentdb-dotnet-core-getting-started).

## <a id="Connect"></a>Připojení k účtu Azure Cosmos

Připojení k účtu Azure Cosmos pomocí importu požadované závislosti. Chcete-li importovat závislosti, přidejte následující kód na začátek souboru Program.cs:

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

V dalším kroku přidejte tyto dvě konstanty a *klienta* proměnné pod veřejnou třídu *Program*.

```csharp
class Program
{
    // ADD THIS PART TO YOUR CODE
    private const string EndpointUri = "<your endpoint URI>";
    private const string PrimaryKey = "<your key>";
    private DocumentClient client;
```

Dále přejděte na web [Azure Portal](https://portal.azure.com) a získejte identifikátor URI a primární klíč. Identifikátor URI a primární klíč služby Azure Cosmos DB jsou potřeba k tomu, aby aplikace věděla, kam se má připojit, a aby služba Azure Cosmos DB důvěřovala připojení aplikace.

Na webu Azure Portal, přejděte k vašemu účtu Azure Cosmos a pak vyberte **klíče**.

Zkopírujte identifikátor URI z portálu a vložte ho do `<your endpoint URI>` v souboru program.cs. Poté zkopírujte PRIMÁRNÍ KLÍČ z portálu a vložte ho do `<your key>`. Nezapomeňte odstranit znaky < a >, ale ponechte uvozovky kolem vašeho koncového bodu a klíče.

![Získání klíčů z webu Azure Portal][keys]

Vytvořením nové instance **DocumentClient** spustíme počáteční aplikaci.

Níže **hlavní** metodu, přidejte nový asynchronní úkol pojmenovaný **GetStartedDemo**, který vytvoří instanci nové **DocumentClient**.

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

## <a id="CreateDatabase"></a>Vytvoření databáze

Než přidáte kód pro vytvoření databáze, přidejte pomocnou metodu pro výpis do konzoly. Zkopírujte a vložte metodu **WriteToConsoleAndPromptToContinue** pod metodu **GetStartedDemo**.

```csharp
// ADD THIS PART TO YOUR CODE
private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
{
        Console.WriteLine(format, args);
        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
}
```

Vytvoření databáze Azure Cosmos DB s použitím `CreateDatabaseAsync` metodu **DocumentClient** třídy. Databáze je logický kontejner úložiště dokumentů JSON rozděleného mezi kolekcemi. Zkopírujte a vložte následující kód do metody **GetStartedDemo** pod vytvoření klienta. Tím se vytvoří databáze s názvem *FamilyDB*.

```csharp
private async Task GetStartedDemo()
{
    this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);

    // ADD THIS PART TO YOUR CODE
    await this.client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB_oa" });
```

Výběrem tlačítka **DocumentDBGettingStarted** spusťte aplikaci.

## <a id="CreateColl"></a>Vytvoření kolekce

Vytvoření kolekce pomocí `CreateDocumentCollectionAsync` metodu **DocumentClient** třídy. Kolekce je kontejner dokumentů JSON a přidružené logiky javascriptové aplikace.

> [!WARNING]
> **CreateDocumentCollectionAsync** vytvoří novou kolekci s vyhrazenou propustností, za kterou se hradí poplatky. Další podrobnosti najdete v části [stránce s cenami](https://azure.microsoft.com/pricing/details/cosmos-db/).

Zkopírujte a vložte následující kód do metody **GetStartedDemo** pod vytvoření databáze. Tento kód vytvoří kolekci dokumentů s názvem *FamilyCollection_oa*.

```csharp
    this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);

    await this.client.CreateDatabaseIfNotExistsAsync("FamilyDB_oa");

    // ADD THIS PART TO YOUR CODE
    await this.client.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("FamilyDB_oa"), new DocumentCollection { Id = "FamilyCollection_oa" });
```

Výběrem tlačítka **DocumentDBGettingStarted** spusťte aplikaci.

## <a id="CreateDoc"></a>Vytvoření dokumentů JSON

Vytvoření dokumentu pomocí `CreateDocumentAsync` metodu **DocumentClient** třídy. Dokumenty představují uživatelem definovaný (libovolný) obsah JSON. Nyní můžete vložit jeden nebo více dokumentů. 

Nejprve je potřeba vytvořit **řady** třídu, která představuje objekty uložené ve službě Azure Cosmos DB. Můžete také vytvořit **nadřazené**, **podřízené**, **domácí mazlíček**, a **adresu** podtřídami, které se používají v rámci **řady**. Dokumenty musí mít vlastnost **Id** serializovanou jako **id** ve formátu JSON. Vytvořte tyto třídy tak, že za metodu **GetStartedDemo** přidáte následující vnitřní podtřídy. Zkopírujte a vložte třídy **Family**, **Parent**, **Child**, **Pet** a **Address** pod metodu **WriteToConsoleAndPromptToContinue**.

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

Vložte dva dokumenty, jeden pro rodinu Andersenů a druhý pro rodinu Wakefieldů. Zkopírujte a vložte kód následující za `// ADD THIS PART TO YOUR CODE` do vaší metody **GetStartedDemo** pod vytvoření kolekce dokumentů.

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

![Hierarchický vztah mezi účtem, online databází a kolekcí](./media/sql-api-dotnetcore-get-started/nosql-tutorial-account-database.png)

## <a id="Query"></a>Dotazování prostředků Azure Cosmos DB

Azure Cosmos DB podporuje bohaté dotazy na dokumenty JSON uložené v každé z kolekcí. Následující ukázkový kód ukazuje různé dotazy – používající jak syntaxi SQL služby Azure Cosmos DB a LINQ –, kterou můžete spouštět oproti dokumentům, které jsme vložili v předchozím kroku.

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

Následující diagram ilustruje, jak se volá syntaxe dotazu Azure Cosmos DB SQL proti kolekci, kterou jste vytvořili. Stejná logika platí pro dotaz LINQ.

![Diagram ilustrující obor a význam dotazu použitého v kurzu NoSQL k vytvoření konzolové aplikace v jazyce C#](./media/sql-api-dotnetcore-get-started/nosql-tutorial-collection-documents.png)

`FROM` – Klíčové slovo je v dotazu volitelné, protože dotazy služby Azure Cosmos DB již mají obor nastaven na jedinou kolekci. Proto je možné příkaz „FROM Families f“ vyměnit za „FROM root r“ nebo jakoukoli jinou proměnnou, kterou si zvolíte. Azure Cosmos DB odvodí, že rodiny, root nebo název proměnné, kterou jste zvolili odkazuje na aktuální kolekci ve výchozím nastavení.

## <a id="ReplaceDocument"></a>Nahrazení dokumentu JSON

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

## <a id="DeleteDocument"></a>Odstranění dokumentu JSON

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

## <a id="DeleteDatabase"></a>Odstranění databáze

Odstraněním vytvořené databáze dojde k odebrání databáze a všech jejích podřízených prostředků (kolekcí, dokumentů atd.). Zkopírujte a vložte následující kód do vašeho **GetStartedDemo** pod dokument, který chcete odstranit celou databázi a všechny podřízené prostředky.

```csharp
this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

await this.DeleteFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1");

// ADD THIS PART TO CODE
// Clean up/delete the database
await this.client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri("FamilyDB_oa"));
```

Výběrem tlačítka **DocumentDBGettingStarted** spusťte aplikaci.

## <a id="Run"></a>Spustit vaše C# konzolové aplikace

Výběrem tlačítka **DocumentDBGettingStarted** v sadě Visual Studio sestavte aplikaci v režimu ladění. V okně konzoly by se měl zobrazit výstup spuštěné aplikace. Výstup bude zobrazovat výsledky dotazů, které jsme přidali, a měl by odpovídat ukázkovému textu níže.

```bash
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

Právě jste dokončili kurz a máte funkční C# konzolové aplikace.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud jste už nepotřebujete, můžete odstranit skupinu prostředků, účet Azure Cosmos a všechny související prostředky. Uděláte to tak, vyberte skupinu prostředků pro virtuální počítač, vyberte **odstranit**a potom ověřte název skupiny prostředků pro odstranění.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak můžete vytvořit aplikaci .NET Core, abyste mohli spravovat data uložená v účtu rozhraní SQL API služby Azure Cosmos DB. Teď můžete přejít k dalšímu článku:

> [!div class="nextstepaction"]
> [Sestavení aplikace konzoly v jazyce Java s účtu rozhraní SQL API služby Azure Cosmos DB](sql-api-java-get-started.md)

[create-sql-api-dotnet.md#create-account]: create-sql-api-dotnet.md#create-account
[keys]: media/sql-api-dotnetcore-get-started/nosql-tutorial-keys.png
