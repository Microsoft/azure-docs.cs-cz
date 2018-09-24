---
title: 'Azure Cosmos DB: Úvodní kurz k rozhraní SQL API | Microsoft Docs'
description: Kurz, v rámci kterého se vytvoří online databáze a konzolová aplikace v jazyce C# pomocí rozhraní SQL API.
keywords: kurz nosql, online databáze konzolová aplikace jazyka c#
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 08/16/2017
ms.author: sngun
ms.openlocfilehash: 40a80a049e6eb94390bab0b47e1f5ed49b4606b9
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46991194"
---
# <a name="azure-cosmos-db-sql-api-getting-started-tutorial"></a>Azure Cosmos DB: Úvodní kurz k rozhraní SQL API

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

Vítejte v úvodním kurzu k rozhraní Azure Cosmos DB SQL API! Až projdete tímto kurzem, budete mít konzolovou aplikaci, která vytváří prostředky Azure Cosmos DB a dotazuje se na ně.

Tento kurz zahrnuje:

* Vytvoření účtu služby Azure Cosmos DB a připojení k němu
* Konfigurace řešení v nástroji Visual Studio
* Vytvoření online databáze
* Vytvoření kolekce
* Vytvoření dokumentů JSON
* Dotazování na kolekci
* Nahrazení dokumentu
* Odstranění dokumentu
* Odstranění databáze

Nemáte čas? Nevadí! Úplné řešení je k dispozici na [GitHubu](https://github.com/Azure-Samples/documentdb-dotnet-getting-started). Pro rychlé pokyny přeskočte na [oddíl Získání úplného řešení kurzu NoSQL](#GetSolution).

Můžeme začít!

## <a name="prerequisites"></a>Požadavky

* Aktivní účet Azure. Pokud žádný nemáte, můžete si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Krok 1: Vytvoření účtu služby Azure Cosmos DB
Vytvořme účet služby Azure Cosmos DB. Pokud již máte účet, který chcete použít, můžete přeskočit na [Nastavení řešení v nástroji Visual Studio](#SetupVS). Pokud používáte emulátor služby Azure Cosmos DB, nastavte emulátor pomocí postupu v tématu [Emulátor služby Azure Cosmos DB](local-emulator.md) a přeskočte k části [Nastavení řešení v sadě Visual Studio](#SetupVS).

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupVS"></a>Krok 2: Nastavení řešení v sadě Visual Studio
1. Otevřete v počítači **Visual Studio 2017**.
2. V nabídce **Soubor** vyberte **Nový** a zvolte **Projekt**.
3. V dialogovém okně **Nový projekt** vyberte **Šablony** / **Visual C#** / **Konzolová aplikace**, pojmenujte svůj projekt a klikněte na **OK**.
   ![Snímek obrazovky okna Nový projekt](./media/sql-api-get-started/nosql-tutorial-new-project-2.png)
4. V **Průzkumníku řešení** klikněte pravým tlačítkem na novou konzolovou aplikaci v rámci řešení sady Visual Studio a pak klikněte na **Spravovat balíčky NuGet**.
    
    ![Snímek obrazovky místní nabídky projektu](./media/sql-api-get-started/nosql-tutorial-manage-nuget-pacakges.png)
5. Na kartě **NuGet** klikněte na **Procházet** a do vyhledávacího pole zadejte **azure documentdb**.
6. Najděte ve výsledcích **Microsoft.Azure.DocumentDB** a klikněte na **Nainstalovat**.
   ID balíčku s klientskou knihovnou rozhraní Azure Cosmos DB SQL API je [Microsoft Azure Cosmos DB Client Library](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/).
   ![Snímek obrazovky s nabídkou NuGet pro vyhledání klientské sady SDK služby Azure Cosmos DB](./media/sql-api-get-started/nosql-tutorial-manage-nuget-pacakges-2.png)

    Pokud se vám zobrazí zpráva týkající se kontroly změn řešení, klikněte na **OK**. Pokud se vám zobrazí zpráva týkající se přijetí licence, klikněte na **Souhlasím**.

Výborně! Teď když jsme dokončili nastavování, napišme nějaký kód. Úplný projekt s kódem pro tento kurz najdete na [GitHubu](https://github.com/Azure-Samples/documentdb-dotnet-getting-started/blob/master/src/Program.cs).

## <a id="Connect"></a>Krok 3: Připojení k účtu služby Azure Cosmos DB
Nejprve přidejte na začátek aplikace C# do souboru Program.cs tyto reference:

    using System;
    using System.Linq;
    using System.Threading.Tasks;

    // ADD THIS PART TO YOUR CODE
    using System.Net;
    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Newtonsoft.Json;

> [!IMPORTANT]
> Přidání výše uvedených závislostí je nezbytné pro dokončení tohoto kurzu.
> 
> 

Nyní přidejte tyto dvě konstanty a proměnnou *client* pod veřejnou třídu *Program*.

    public class Program
    {
        // ADD THIS PART TO YOUR CODE
        private const string EndpointUrl = "<your endpoint URL>";
        private const string PrimaryKey = "<your primary key>";
        private DocumentClient client;

Dále přejděte zpět na [Azure Portal](https://portal.azure.com) a získejte adresu URL koncového bodu a primární klíč. Adresa URL koncového bodu a primární klíč jsou potřeba k tomu, aby aplikace věděla, kam se má připojit, a aby služba Azure Cosmos DB důvěřovala připojení aplikace.

Na webu Azure Portal přejděte do účtu služby Azure Cosmos DB a klikněte na **Klíče**.

Zkopírujte identifikátor URI z portálu a vložte ho do `<your endpoint URL>` v souboru program.cs. Poté zkopírujte PRIMÁRNÍ KLÍČ z portálu a vložte ho do `<your primary key>`.

![Snímek obrazovky webu Azure Portal, který se v kurzu NoSQL používá k vytvoření konzolové aplikace v jazyce C#. Ukazuje účet služby Azure Cosmos DB se zvýrazněným aktivním centrem, zvýrazněným tlačítkem KLÍČE na stránce účtu služby Azure Cosmos DB a zvýrazněnými hodnotami URI, PRIMÁRNÍ KLÍČ a SEKUNDÁRNÍ KLÍČ na stránce Klíče.][keys]

Potom vytvořením nové instance **DocumentClient** spustíme aplikaci.

Pod metodu **Main** přidejte tento nový asynchronní úkol pojmenovaný **GetStartedDemo**, který vytvoří instanci našeho nového klienta **DocumentClient**.

    static void Main(string[] args)
    {
    }

    // ADD THIS PART TO YOUR CODE
    private async Task GetStartedDemo()
    {
        this.client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey);
    }

Přidejte následující kód, aby se asynchronní úkol spustil z metody **Main**. Metoda **Main** zachytí výjimky a vypíše je do konzoly.

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

Stisknutím klávesy **F5** spusťte aplikaci. Výstup okna konzoly zobrazuje zprávu `End of demo, press any key to exit.`, která potvrzuje vytvoření připojení.  Potom můžete okno konzoly zavřít. 

Blahopřejeme! Úspěšně jste se připojili k účtu služby Azure Cosmos DB. Nyní se podívejme, jak se pracuje s prostředky Azure Cosmos DB.  

## <a name="step-4-create-a-database"></a>Krok 4: Vytvoření databáze
Než přidáte kód pro vytvoření databáze, přidejte pomocnou metodu pro výpis do konzoly.

Zkopírujte a vložte metodu **WriteToConsoleAndPromptToContinue** za metodu **GetStartedDemo**.

    // ADD THIS PART TO YOUR CODE
    private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
    {
            Console.WriteLine(format, args);
            Console.WriteLine("Press any key to continue ...");
            Console.ReadKey();
    }

[Databázi](sql-api-resources.md#databases) Azure Cosmos DB je možné vytvořit pomocí metody [CreateDatabaseIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseifnotexistsasync.aspx) třídy **DocumentClient**. Databáze je logický kontejner úložiště dokumentů JSON rozděleného mezi kolekcemi.

Zkopírujte a vložte následující kód do metody **GetStartedDemo** za vytvoření klienta. Tím se vytvoří databáze s názvem *FamilyDB*.

    private async Task GetStartedDemo()
    {
        this.client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey);

        // ADD THIS PART TO YOUR CODE
        await this.client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB" });

Stisknutím klávesy **F5** spusťte aplikaci.

Blahopřejeme! Úspěšně jste vytvořili databázi Azure Cosmos DB.  

## <a id="CreateColl"></a>Krok 5: Vytvoření kolekce
> [!WARNING]
> **CreateDocumentCollectionIfNotExistsAsync** vytvoří novou kolekci s vyhrazenou propustností, za kterou se hradí poplatky. Další podrobnosti najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/cosmos-db/).
> 
> 

[Kolekci](sql-api-resources.md#collections) je možné vytvořit pomocí metody [CreateDocumentCollectionIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionifnotexistsasync.aspx) třídy **DocumentClient**. Kolekce je kontejner dokumentů JSON a přidružené logiky javascriptové aplikace.

Zkopírujte a vložte následující kód do metody **GetStartedDemo** za vytvoření databáze. Tím se vytvoří kolekce dokumentů s názvem *FamilyCollection*.

        this.client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey);

        await this.client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB" });

        // ADD THIS PART TO YOUR CODE
         await this.client.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("FamilyDB"), new DocumentCollection { Id = "FamilyCollection" });

Stisknutím klávesy **F5** spusťte aplikaci.

Blahopřejeme! Úspěšně jste vytvořili kolekci dokumentů Azure Cosmos DB.  

## <a id="CreateDoc"></a>Krok 6: Vytvoření dokumentů JSON
[Dokument](sql-api-resources.md#documents) je možné vytvořit pomocí metody [CreateDocumentAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx) třídy **DocumentClient**. Dokumenty představují uživatelem definovaný (libovolný) obsah JSON. Nyní můžete vložit jeden nebo více dokumentů. Pokud již máte data, která chcete uložit do databáze, můžete použít [nástroj pro migraci dat](import-data.md) služby Azure Cosmos DB a importovat tato data do databáze.

Nejprve musíme vytvořit třídu **Family**, která bude v této ukázce představovat objekty uložené ve službě Azure Cosmos DB. Kromě toho vytvoříme i podtřídy **Parent**, **Child**, **Pet** a **Address**, které se použijí v rámci **Family**. Povšimněte si, že dokumenty musí mít vlastnost **Id** serializovanou jako **id** ve formátu JSON. Vytvořte tyto třídy tak, že za metodu **GetStartedDemo** přidáte následující vnitřní podtřídy.

Zkopírujte a vložte třídy **Family**, **Parent**, **Child**, **Pet** a **Address** za metodu **WriteToConsoleAndPromptToContinue**.

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

Zkopírujte a vložte metodu **CreateFamilyDocumentIfNotExists** pod třídu **Address**.

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

Vložte dva dokumenty, jeden pro rodinu Andersenů a druhý pro rodinu Wakefieldů.

Zkopírujte a vložte následující kód do metody **GetStartedDemo** za vytvoření kolekce dokumentů.

    await this.client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB" });
    
    await this.client.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("FamilyDB"), new DocumentCollection { Id = "FamilyCollection" });


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

    await this.CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", andersenFamily);

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

    await this.CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", wakefieldFamily);

Stisknutím klávesy **F5** spusťte aplikaci.

Blahopřejeme! Úspěšně jste vytvořili dva dokumenty Azure Cosmos DB.  

![Diagram ilustrující hierarchický vztah mezi účtem, online databází, kolekcí a dokumenty používanými v kurzu NoSQL k vytvoření konzolové aplikace v jazyce C#](./media/sql-api-get-started/nosql-tutorial-account-database.png)

## <a id="Query"></a>Krok 7: Dotazování prostředků Azure Cosmos DB
Azure Cosmos DB podporuje bohaté [dotazy](sql-api-sql-query.md) na dokumenty JSON uložené v každé z kolekcí.  Následující ukázkový kód ukazuje různé dotazy – používající jak syntaxi SQL služby Azure Cosmos DB, tak LINQ – které můžeme spouštět na dokumenty vložené v předchozím kroku.

Zkopírujte a vložte metodu **ExecuteSimpleQuery** za metodu **CreateFamilyDocumentIfNotExists**.

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

Zkopírujte a vložte následující kód do metody **GetStartedDemo** za vytvoření druhého dokumentu.

    await this.CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", wakefieldFamily);

    // ADD THIS PART TO YOUR CODE
    this.ExecuteSimpleQuery("FamilyDB", "FamilyCollection");

Stisknutím klávesy **F5** spusťte aplikaci.

Blahopřejeme! Úspěšně jste provedli dotaz na kolekci Azure Cosmos DB.

Následující diagram ilustruje volání syntaxe příkazu jazyka SQL služby Azure Cosmos DB na kolekci, kterou jste vytvořili. Stejná logika platí také pro dotaz LINQ.

![Diagram ilustrující obor a význam dotazu použitého v kurzu NoSQL k vytvoření konzolové aplikace v jazyce C#](./media/sql-api-get-started/nosql-tutorial-collection-documents.png)

Klíčové slovo [FROM](sql-api-sql-query.md#FromClause) je v dotazu volitelné, protože dotazy Azure Cosmos DB již mají obor nastaven na jedinou kolekci. Proto je možné příkaz „FROM Families f“ vyměnit za „FROM root r“ nebo jakoukoli jinou proměnnou, kterou si zvolíte. Azure Cosmos DB standardně vyvodí, že Families, root nebo zvolený název proměnné odkazují na aktuální kolekci.

## <a id="ReplaceDocument"></a>Krok 8: Nahrazení dokumentu JSON
Azure Cosmos DB podporuje nahrazování dokumentů JSON.  

Zkopírujte a vložte metodu **ReplaceFamilyDocument** za metodu **ExecuteSimpleQuery**.

    // ADD THIS PART TO YOUR CODE
    private async Task ReplaceFamilyDocument(string databaseName, string collectionName, string familyName, Family updatedFamily)
    {
         await this.client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, familyName), updatedFamily);
         this.WriteToConsoleAndPromptToContinue("Replaced Family {0}", familyName);
    }

Zkopírujte a vložte následující kód do metody **GetStartedDemo** za spuštění dotazu na konec metody. Po nahrazení dokumentu tento kód spustí stejný dotaz znovu, aby se zobrazil změněný dokument.

    await this.CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", wakefieldFamily);

    this.ExecuteSimpleQuery("FamilyDB", "FamilyCollection");

    // ADD THIS PART TO YOUR CODE
    // Update the Grade of the Andersen Family child
    andersenFamily.Children[0].Grade = 6;

    await this.ReplaceFamilyDocument("FamilyDB", "FamilyCollection", "Andersen.1", andersenFamily);

    this.ExecuteSimpleQuery("FamilyDB", "FamilyCollection");

Stisknutím klávesy **F5** spusťte aplikaci.

Blahopřejeme! Úspěšně jste nahradili dokument Azure Cosmos DB.

## <a id="DeleteDocument"></a>Krok 9: Odstranění dokumentu JSON
Azure Cosmos DB podporuje odstraňování dokumentů JSON.  

Zkopírujte a vložte metodu **DeleteFamilyDocument** za metodu **ReplaceFamilyDocument**.

    // ADD THIS PART TO YOUR CODE
    private async Task DeleteFamilyDocument(string databaseName, string collectionName, string documentName)
    {
         await this.client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, documentName));
         Console.WriteLine("Deleted Family {0}", documentName);
    }

Zkopírujte a vložte následující kód do metody **GetStartedDemo** za spuštění druhého dotazu na konec metody.

    await this.ReplaceFamilyDocument("FamilyDB", "FamilyCollection", "Andersen.1", andersenFamily);
    
    this.ExecuteSimpleQuery("FamilyDB", "FamilyCollection");
    
    // ADD THIS PART TO CODE
    await this.DeleteFamilyDocument("FamilyDB", "FamilyCollection", "Andersen.1");

Stisknutím klávesy **F5** spusťte aplikaci.

Blahopřejeme! Úspěšně jste odstranili dokument Azure Cosmos DB.

## <a id="DeleteDatabase"></a>Krok 10: Odstranění databáze
Odstraněním vytvořené databáze dojde k odstranění databáze a všech jejích podřízených prostředků (kolekcí, dokumentů atd.).

Pokud chcete odstranit celou databázi a její podřízené prostředky, zkopírujte a vložte následující kód do metody **GetStartedDemo** za odstranění dokumentu.

    this.ExecuteSimpleQuery("FamilyDB", "FamilyCollection");

    await this.DeleteFamilyDocument("FamilyDB", "FamilyCollection", "Andersen.1");

    // ADD THIS PART TO CODE
    // Clean up/delete the database
    await this.client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri("FamilyDB"));

Stisknutím klávesy **F5** spusťte aplikaci.

Blahopřejeme! Úspěšně jste odstranili databázi Azure Cosmos DB.

## <a id="Run"></a>Krok 11: Spuštění celé konzolové aplikace jazyka C#
Stiskněte v nástroji Visual Studio klávesu F5 – aplikace se sestaví v režimu ladění.

V okně konzoly by se měl zobrazit výstup spuštěné aplikace. Výstup bude zobrazovat výsledky dotazů, které jsme přidali, a měl by odpovídat ukázkovému textu níže.

    Created FamilyDB
    Press any key to continue ...
    Created FamilyCollection
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

Blahopřejeme! Dokončili jste tento kurz a máte funkční konzolovou aplikaci jazyka C#!

## <a id="GetSolution"></a>Získání úplného řešení kurzu
Pokud jste neměli dostatek času k dokončení kroků v tomto kurzu nebo si jen chcete stáhnout ukázky kódu, můžete je získat z [GitHubu](https://github.com/Azure-Samples/documentdb-dotnet-getting-started). 

K vytvoření řešení GetStarted budete potřebovat toto:

* Aktivní účet Azure. Pokud žádný nemáte, můžete si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/).
* [Účet služby Azure Cosmos DB][cosmos-db-create-account].
* Řešení [GetStarted](https://github.com/Azure-Samples/documentdb-dotnet-getting-started) dostupné na GitHubu

Pokud chcete obnovit reference na sadu Azure Cosmos DB .NET SDK v nástroji Visual Studio, klikněte v Průzkumníkovi řešení pravým tlačítkem na řešení **GetStarted** a pak levým na **Povolit obnovení balíčků NuGet**. Dále v souboru App.config aktualizujte hodnoty EndpointUrl a AuthorizationKey tak, jak je popsáno v části [Připojení k účtu služby Azure Cosmos DB](#Connect).

A to je vše, stačí sestavit a máte hotovo.


## <a name="next-steps"></a>Další kroky
* Chcete komplexnější kurz pro ASP.NET MVC? Viz [Kurz k ASP.NET MVC: Vývoj webové aplikace s použitím služby Azure Cosmos DB](sql-api-dotnet-application.md).
* Chcete testovat škálování a výkon pomocí služby Azure Cosmos DB? Viz [Testování výkonu a škálování pomocí služby Azure Cosmos DB](performance-testing.md).
* Přečtěte si, jak [sledovat požadavky, využití a úložiště Azure Cosmos DB](monitor-accounts.md).
* Spouštějte dotazy proti ukázkovým datovým sadám v [Query Playground](https://www.documentdb.com/sql/demo).
* Další informace o Cosmos Azure DB najdete v tématu [Vítá vás Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction).

[keys]: media/sql-api-get-started/nosql-tutorial-keys.png
[cosmos-db-create-account]: create-sql-api-dotnet.md#create-account
