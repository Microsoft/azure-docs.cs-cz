---
title: Vytvoření konzolové aplikace .NET pro správu dat v účtu rozhraní SQL API služby Azure Cosmos DB (sada SDK verze 3 ve verzi Preview)
description: Kurz, v rámci kterého se vytvoří online databáze a konzolová aplikace v jazyce C# pomocí rozhraní SQL API.
keywords: kurz nosql, online databáze konzolová aplikace jazyka c#
author: deborahc
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 12/01/2018
ms.author: dech
ms.openlocfilehash: 4ac770b58e4a4d9a547916997a8f9d181b2fa895
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52852926"
---
# <a name="build-a-net-console-app-to-manage-data-in-azure-cosmos-db-sql-api-account-sdk-version-3-preview"></a>Vytvoření konzolové aplikace .NET pro správu dat v účtu rozhraní SQL API služby Azure Cosmos DB (sada SDK verze 3 ve verzi Preview)

> [!div class="op_single_selector"]
> * [.NET (preview)](sql-api-dotnet-get-started-preview.md)
> * [.NET](sql-api-get-started.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [.NET core (Preview)](sql-api-dotnet-core-get-started-preview.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

Vítejte v úvodním kurzu k rozhraní Azure Cosmos DB SQL API! Až projdete tímto kurzem, budete mít konzolovou aplikaci, která vytváří prostředky Azure Cosmos DB a dotazuje se na ně. Tento kurz používá [verze 3.0 a](https://www.nuget.org/packages/Microsoft.Azure.Cosmos) verzi sady Azure Cosmos DB .NET SDK, cíle, které [.NET Standard 2.0.](https://docs.microsoft.com/dotnet/standard/net-standard)

Tento kurz zahrnuje:

> [!div class="checklist"]
> * Vytvoření a připojení k účtu Azure Cosmos
> * Konfigurace vašeho projektu v sadě Visual Studio
> * Vytváří se databáze a kontejner
> * Přidání položek do kontejneru
> * Dotazování kontejneru
> * Operace CRUD v položce
> * Odstranění databáze

Nemáte čas? Nevadí! Úplné řešení je k dispozici na [GitHubu](https://github.com/Azure-Samples/cosmos-dotnet-getting-started). Přejít [získání úplného řešení kurzu k části](#GetSolution) pro rychlé pokyny.

Můžeme začít!

## <a name="prerequisites"></a>Požadavky

* Aktivní účet Azure. Pokud žádný nemáte, můžete si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Krok 1: Vytvoření účtu služby Azure Cosmos DB
Vytvořme účet služby Azure Cosmos DB. Pokud již máte účet, který chcete použít, můžete přeskočit na [Nastavení řešení v nástroji Visual Studio](#SetupVS). Pokud používáte emulátor služby Azure Cosmos DB, postupujte podle kroků uvedených v [emulátor služby Azure Cosmos DB](local-emulator.md) nastavili emulátor, a přeskočte k části [nastavení projektu sady Visual Studio](#SetupVS).

[!INCLUDE [create-dbaccount-preview](../../includes/cosmos-db-create-dbaccount-preview.md)]

## <a id="SetupVS"></a>Krok 2: Nastavení projektu sady Visual Studio
1. Otevřete v počítači **Visual Studio 2017**.
1. V nabídce **Soubor** vyberte **Nový** a zvolte **Projekt**.
1. V **nový projekt** dialogového okna, vyberte **Visual C#**   /  **Konzolová aplikace (.NET Framework)**, pojmenujte svůj projekt a potom klikněte na tlačítko **OK** .
    ![Snímek obrazovky okna Nový projekt](./media/sql-api-get-started/dotnet-tutorial-visual-studio-new-project.png)
1. V **Průzkumníku řešení** klikněte pravým tlačítkem na novou konzolovou aplikaci v rámci řešení sady Visual Studio a pak klikněte na **Spravovat balíčky NuGet**.
    
    ![Snímek obrazovky místní nabídky projektu](./media/sql-api-get-started/dotnet-tutorial-visual-studio-manage-nuget.png)
1. V **NuGet** klikněte na tlačítko **Procházet**a typ **Microsoft.Azure.Cosmos** do vyhledávacího pole. Nezapomeňte se podívat *zahrnout prelease* najít verzi preview.
1. Najděte ve výsledcích **Microsoft.Azure.Cosmos** a klikněte na tlačítko **nainstalovat**.
   ID balíčku s klientskou knihovnou rozhraní Azure Cosmos DB SQL API je [Microsoft Azure Cosmos DB Client Library](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/).
   ![Snímek obrazovky s nabídkou NuGet pro vyhledání klientské sady SDK služby Azure Cosmos DB](./media/sql-api-get-started/dotnet-tutorial-visual-studio-manage-nuget-2.png)

    Pokud se vám zobrazí zpráva týkající se kontroly změn řešení, klikněte na **OK**. Pokud se vám zobrazí zpráva týkající se přijetí licence, klikněte na **Souhlasím**.

Výborně! Teď když jsme dokončili nastavování, napišme nějaký kód. Úplný projekt s kódem pro tento kurz najdete na [GitHubu](https://github.com/Azure-Samples/cosmos-dotnet-getting-started).

## <a id="Connect"></a>Krok 3: Připojení k účtu služby Azure Cosmos DB
1. Nejprve nahraďte odkazy na začátku vaše C# aplikace v **Program.cs** soubor s tyto odkazy:
    ```csharp
    using System;
    using System.Threading.Tasks;
    using System.Configuration;
    using Microsoft.Azure.Cosmos;
    using System.Collections.Generic;
    using System.Net;
    ```
1. Nyní přidejte tyto konstanty a proměnné do veřejné třídy ``Program``.
    ```csharp
    public class Program
    {
        // ADD THIS PART TO YOUR CODE

        // The Azure Cosmos DB endpoint for running this sample.
        private static readonly string EndpointUri = "<your endpoint here>";
        // The primary key for the Azure Cosmos account.
        private static readonly string PrimaryKey = "<your primary key>";

        // The Cosmos client instance
        private CosmosClient cosmosClient;

        // The database we will create
        private CosmosDatabase database;

        // The container we will create.
        private CosmosContainer container;

        // The name of the database and container we will create
        private string databaseId = "FamilyDatabase";
        private string containerId = "FamilyContainer";
    }
    ```
    Mějte na paměti, pokud jste se seznámili s předchozí verzí sady .NET SDK, můžou sloužit k zobrazení podmínek kolekce a dokumentu. Protože Azure Cosmos DB podporuje několik modelů rozhraní API, verze 3.0 a sady .NET SDK používá obecné podmínky "kontejner" a "položka". Kontejner může být kolekce, graf nebo tabulka. Položka představuje obsah uvnitř kontejneru a může to být dokument, pár hrany a vrcholu nebo řádek. [Další informace o databází, kontejnerů a položek.](databases-containers-items.md)

1. Získejte adresu URL koncového bodu a primární klíč z [webu Azure portal](https://portal.azure.com).

    Na webu Azure Portal přejděte do účtu služby Azure Cosmos DB a klikněte na **Klíče**.

    Zkopírujte identifikátor URI z portálu a vložte ho do `<your endpoint URL>` v ```Program.cs``` souboru. Zkopírujte primární klíč z portálu a vložte ho do `<your primary key>`.

   ![Snímek obrazovky se získat klíče služby Azure Cosmos DB na webu Azure portal](./media/sql-api-get-started/dotnet-tutorial-portal-keys.png)

1. V dalším kroku vytvoříme novou instanci třídy ```CosmosClient``` a nastavit některé generování uživatelského rozhraní pro naši aplikaci.

    Níže **hlavní** metodu, přidejte nový asynchronní úkol pojmenovaný **GetStartedDemoAsync**, který vytvoří instanci našeho nového ```CosmosClient```. Použijeme **GetStartedDemoAsync** jako vstupní bod, který volá metody, které pracují s prostředky Azure Cosmos DB.

    ```csharp
    public static async Task Main(string[] args)
    {
    }

    // ADD THIS PART TO YOUR CODE
    /*
        Entry point to call methods that operate on Azure Cosmos DB resources in this sample
    */   
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
    }
    ```

1. Přidejte následující kód pro spuštění **GetStartedDemoAsync** asynchronní úlohu z vaší **hlavní** metody. Metoda **Main** zachytí výjimky a vypíše je do konzoly.
    ```csharp
    public static async Task Main(string[] args)
    {
        // ADD THIS PART TO YOUR CODE
        try
        {
            Console.WriteLine("Beginning operations...\n");
            Program p = new Program();
            await p.GetStartedDemoAsync();
        }
        catch (CosmosException de)
        {
            Exception baseException = de.GetBaseException();
            Console.WriteLine("{0} error occurred: {1}\n", de.StatusCode, de);
        }
        catch (Exception e)
        {
            Console.WriteLine("Error: {0}\n", e);
        }
        finally
        {
            Console.WriteLine("End of demo, press any key to exit.");
            Console.ReadKey();
        }
    }
    ```

1. Vyberte **F5** ke spuštění aplikace. Výstup okna konzoly zobrazuje zprávu `End of demo, press any key to exit.` potvrzující, že bylo navázáno připojení ke službě Azure Cosmos DB. Potom můžete okno konzoly zavřít. 

Blahopřejeme! Právě jste úspěšně propojili s účtem služby Azure Cosmos DB. 

## <a name="step-4-create-a-database"></a>Krok 4: Vytvoření databáze
Databázi lze vytvořit buď pomocí [ **CreateDatabaseIfNotExistsAsync** ](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.cosmos.cosmosdatabases) nebo [ **CreateDatabaseAsync** ](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.cosmos.cosmosdatabases) funkce ``CosmosDatabases`` třídy. Databáze je logický kontejner položek rozdělených napříč kontejnery.
    
1. Zkopírujte a vložte **CreateDatabase** metodu vaše **GetStartedDemoAsync** metody. **CreateDatabase** vytvoří novou databázi s id ``FamilyDatabase`` Pokud již neexistuje, pomocí zadané z id ``databaseId`` pole. 

    ```csharp
    /*
        Create the database if it does not exist
    */    
    private async Task CreateDatabase()
    {
        // Create a new database
        this.database = await this.cosmosClient.Databases.CreateDatabaseIfNotExistsAsync(databaseId);
        Console.WriteLine("Created Database: {0}\n", this.database.Id);
    }
    ```

1. Zkopírujte a vložte kód níže, kde můžete vytvořit instanci CosmosClient volat **CreateDatabase** metoda, kterou jste právě přidali.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);

        //ADD THIS PART TO YOUR CODE
        await this.CreateDatabase(); 
    }
    ```

    V tuto chvíli váš kód by měla vypadat jako to, se koncový bod a primární klíč vyplněna.
    ```csharp
    using System;
    using System.Threading.Tasks;
    using System.Configuration;
    using Microsoft.Azure.Cosmos;
    using System.Collections.Generic;
    using System.Net;

    namespace CosmosGettingStarted
    {
        class Program
        {
            // The Azure Cosmos DB endpoint for running this sample.
            private static readonly string EndpointUri = "<your endpoint here>";
            // The primary key for the Azure Cosmos account.
            private static readonly string PrimaryKey = "<your primary key>";

            // The Cosmos client instance
            private CosmosClient cosmosClient;
        
            // The database we will create
            private CosmosDatabase database;

            // The container we will create.
            private CosmosContainer container;

            // The name of the database and container we will create
            private string databaseId = "FamilyDatabase";
            private string containerId = "FamilyContainer";

            public static async Task Main(string[] args)
            {
                try
                {
                    Console.WriteLine("Beginning operations...");
                    Program p = new Program();
                    await p.GetStartedDemoAsync();
                }
                catch (CosmosException de)
                {
                    Exception baseException = de.GetBaseException();
                    Console.WriteLine("{0} error occurred: {1}\n", de.StatusCode, de);
                }
                catch (Exception e)
                {
                    Console.WriteLine("Error: {0}\n", e);
                }
                finally
                {
                    Console.WriteLine("End of demo, press any key to exit.");
                    Console.ReadKey();
                }
            }

            /*
                Entry point to call methods that operate on Azure Cosmos DB resources in this sample
            */
            public async Task GetStartedDemoAsync()
            {
                // Create a new instance of the Cosmos Client
                this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
                await this.CreateDatabase();
            }

            /*
                Create the database if it does not exist
            */    
            private async Task CreateDatabase()
            {
                // Create a new database
                this.database = await this.cosmosClient.Databases.CreateDatabaseIfNotExistsAsync(databaseId);
                Console.WriteLine("Created Database: {0}\n", this.database.Id);
            }
        }
    }
    ```

Vyberte **F5** ke spuštění aplikace.

Blahopřejeme! Úspěšně jste vytvořili databázi Azure Cosmos DB.  

## <a id="CreateColl"></a>Krok 5: Vytvoření kontejneru
> [!WARNING]
> Volání metody **CreateContainerIfNotExistsAsync** vytvoří nový kontejner, kterou se hradí. Další podrobnosti najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/cosmos-db/).
> 
> 

Kontejner můžete vytvořit buď pomocí [ **CreateContainerIfNotExistsAsync** ](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.cosmos.cosmoscontainers) nebo [ **CreateContainerAsync** ](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.cosmos.cosmoscontainers) pracovat **CosmosContainers** třídy. Kontejner se skládá z položek (což je v případě rozhraní SQL API je dokumentů JSON) a přidružené logiky Javascriptové aplikace na straně serveru, třeba uložených procedur, uživatelsky definované funkce a aktivační události.

1. Zkopírujte a vložte **CreateContainer** metodu vaše **CreateDatabase** metody. **CreateContainer** vytvoří nový kontejner s id ``FamilyContainer`` Pokud již neexistuje, pomocí zadané z id ``containerId`` pole. 

    ```csharp
    /*
        Create the container if it does not exist. 
        Specifiy "/LastName" as the partition key since we're storing family information, to ensure good distribution of requests and storage.
    */
    private async Task CreateContainer()
    {
        // Create a new container
        this.container = await this.database.Containers.CreateContainerIfNotExistsAsync(containerId, "/LastName");
        Console.WriteLine("Created Container: {0}\n", this.container.Id);
    }
    ```

1. Zkopírujte a vložte kód níže, kde můžete vytvořit instanci CosmosClient volat **CreateContainer** metoda, kterou jste právě přidali.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabase(); 

        //ADD THIS PART TO YOUR CODE
        await this.CreateContainer();
    }

Select **F5** to run your application.

Congratulations! You have successfully created an Azure Cosmos DB container.  

## <a id="CreateDoc"></a>Step 6: Add items to the container
An item can be created by using the [**CreateItemAsync**](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.cosmos.cosmositems) function of the **CosmosItems** class. When using the SQL API, items are projected as documents, which are user-defined (arbitrary) JSON content. You can now insert an item into your Azure Cosmos DB container.

First, we need to create a **Family** class that will represent objects stored within Azure Cosmos DB in this sample. We will also create **Parent**, **Child**, **Pet**, **Address** subclasses that are used within **Family**. Note that documents must have an **Id** property serialized as **id** in JSON. 
1. Select **Ctrl+Shift+A** to open the **Add New Item** dialog. Add a new class **Family.cs** to your project. 

    ![Screen shot of adding a new Family.cs class into the project](./media/sql-api-get-started/dotnet-tutorial-visual-studio-add-family-class.png)

1. Copy and paste the **Family**, **Parent**, **Child**, **Pet**, and **Address** class into **Family.cs**. 
    ```csharp
    using Newtonsoft.Json;

    namespace CosmosGettingStarted
    {
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
    }
    ```
1. Přejděte zpět do **Program.cs** a přidejte **AddItemsToContainer** metody vaše **CreateContainer** metoda. Kód zkontroluje, ujistěte se, že položka se stejným id před vytvořením ho už neexistuje. Vloží jsme dvě položky, jeden pro rodinu a rodinu Wakefieldů.

    ```csharp
    /*
        Add Family items to the container
    */
    private async Task AddItemsToContainer()
    {
        // Create a family object for the Andersen family
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

        // Read the item to see if it exists. Note ReadItemAsync will not throw an exception if an item does not exist. Instead, we check the StatusCode property off the response object. 
        CosmosItemResponse<Family> andersenFamilyResponse = await this.container.Items.ReadItemAsync<Family>(andersenFamily.LastName, andersenFamily.Id);

        if (andersenFamilyResponse.StatusCode == HttpStatusCode.NotFound)
        {
            // Create an item in the container representing the Andersen family. Note we provide the value of the partition key for this item, which is "Andersen"
            andersenFamilyResponse = await this.container.Items.CreateItemAsync<Family>(andersenFamily.LastName, andersenFamily);

            // Note that after creating the item, we can access the body of the item with the Resource property off the CosmosItemResponse. 
            //We can also access the RequestCharge property to see the amount of RUs consumed on this request.
            Console.WriteLine("Created item in database with id: {0} Operation consumed {1} RUs.\n", andersenFamilyResponse.Resource.Id, andersenFamilyResponse.RequestCharge);
        }
        else
        {
            Console.WriteLine("Item in database with id: {0} already exists\n", andersenFamilyResponse.Resource.Id);
        }

        // Create a family object for the Wakefield family
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

        // Read the item to see if it exists
        CosmosItemResponse<Family> wakefieldFamilyResponse = await this.container.Items.ReadItemAsync<Family>(wakefieldFamily.LastName, wakefieldFamily.Id);

        if (wakefieldFamilyResponse.StatusCode == HttpStatusCode.NotFound)
        {
            // Create an item in the container representing the Wakefield family. Note we provide the value of the partition key for this item, which is "Wakefield"
            wakefieldFamilyResponse = await this.container.Items.CreateItemAsync<Family>(wakefieldFamily.LastName, wakefieldFamily);

            // Note that after creating the item, we can access the body of the item with the Resource property off the CosmosItemResponse. 
            //We can also access the RequestCharge property to see the amount of RUs consumed on this request.
            Console.WriteLine("Created item in database with id: {0} Operation consumed {1} RUs.\n", wakefieldFamilyResponse.Resource.Id, wakefieldFamilyResponse.RequestCharge);
        }
        else
        {
            Console.WriteLine("Item in database with id: {0} already exists\n", wakefieldFamilyResponse.Resource.Id);
        }
    }
    ```
1. Přidejte volání do ``AddItemsToContainer`` v ``GetStartedDemoAsync`` metody.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabase(); 
        await this.CreateContainer();

        //ADD THIS PART TO YOUR CODE
        await this.AddItemsToContainer();
    }
    ```

Vyberte **F5** ke spuštění aplikace.

Blahopřejeme! Úspěšně jste vytvořili dvě položky služby Azure Cosmos DB.  


## <a id="Query"></a>Krok 7: Dotazování prostředků Azure Cosmos DB
Azure Cosmos DB podporuje bohaté [dotazy](sql-api-sql-query.md) na dokumenty JSON uložené v každé z kolekcí. Následující ukázkový kód ukazuje, jak spustit dotaz pro položky, které jsme vložili v předchozím kroku.

1. Zkopírujte a vložte **RunQuery** metodu vaše **AddItemsToContainer** metody.

    ```csharp
    /*
        Run a query (using Azure Cosmos DB SQL syntax) against the container
    */
    private async Task RunQuery()
    {
        var sqlQueryText = "SELECT * FROM c WHERE c.LastName = 'Andersen'";
        var partitionKeyValue = "Andersen";

        Console.WriteLine("Running query: {0}\n", sqlQueryText);

        CosmosSqlQueryDefinition queryDefinition = new CosmosSqlQueryDefinition(sqlQueryText);
        CosmosResultSetIterator<Family> queryResultSetIterator = this.container.Items.CreateItemQuery<Family>(queryDefinition, partitionKeyValue);

        List<Family> families = new List<Family>();

        while (queryResultSetIterator.HasMoreResults)
        {
            CosmosQueryResponse<Family> currentResultSet = await queryResultSetIterator.FetchNextSetAsync();
            foreach (Family family in currentResultSet)
            {
                families.Add(family);
                Console.WriteLine("\tRead {0}\n", family);
            }
        }
    }
    ```
1. Přidejte volání do ``RunQuery`` v ``GetStartedDemoAsync`` metody.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabase(); 
        await this.CreateContainer();
        await this.AddItemsToContainer();

        //ADD THIS PART TO YOUR CODE
        await this.RunQuery();
    }
    ```

Vyberte **F5** ke spuštění aplikace.

Blahopřejeme! Úspěšně jste provedli proti kontejneru Azure Cosmos DB.

## <a id="ReplaceItem"></a>Krok 8: Nahrazení položky JSON
Teď aktualizujeme položky ve službě Azure Cosmos DB.

1. Zkopírujte a vložte **ReplaceFamilyItem** metodu vaše **RunQuery** metody. Poznámka: měníme ``IsRegistered`` vlastnost rodiny a ``Grade`` jedné z podřízených.
    ```csharp
    /*
    Update an item in the container
    */
    private async Task ReplaceFamilyItem()
    {
        CosmosItemResponse<Family> wakefieldFamilyResponse = await this.container.Items.ReadItemAsync<Family>("Wakefield", "Wakefield.7");
        var itemBody = wakefieldFamilyResponse.Resource;
        
        // update registration status from false to true
        itemBody.IsRegistered = true;
        // update grade of child
        itemBody.Children[0].Grade = 6;

        // replace the item with the updated content
        wakefieldFamilyResponse = await this.container.Items.ReplaceItemAsync<Family>(itemBody.LastName, itemBody.Id, itemBody);
        Console.WriteLine("Updated Family [{0},{1}]\n. Body is now: {2}\n", itemBody.LastName, itemBody.Id, wakefieldFamilyResponse.Resource);
    }
    ```
1. Přidejte volání do ``ReplaceFamilyItem`` v ``GetStartedDemo`` metody.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabase(); 
        await this.CreateContainer();
        await this.AddItemsToContainer();
        await this.RunQuery();

        //ADD THIS PART TO YOUR CODE
        await this.ReplaceFamilyItem();
    }

Select **F5** to run your application.

Congratulations! You have successfully replaced an Azure Cosmos DB item.

## <a id="DeleteDocument"></a>Step 9: Delete item
Now, we will delete an item in Azure Cosmos DB.

1. Copy and paste the **DeleteFamilyItem** method below your **ReplaceFamilyItem** method.
    ```csharp
    /*
    Delete an item in the container
    */
    private async Task DeleteFamilyItem()
    {
        var partitionKeyValue = "Wakefield";
        var familyId = "Wakefield.7";

        // Delete an item. Note we must provide the partition key value and id of the item to delete
        CosmosItemResponse<Family> wakefieldFamilyResponse = await this.container.Items.DeleteItemAsync<Family>(partitionKeyValue, familyId);
        Console.WriteLine("Deleted Family [{0},{1}]\n", partitionKeyValue, familyId);
    }
    ```
1. Přidejte volání do ``DeleteFamilyItem`` v ``GetStartedDemo`` metody.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabase(); 
        await this.CreateContainer();
        await this.AddItemsToContainer();
        await this.RunQuery();
        await this.ReplaceFamilyItem();

        //ADD THIS PART TO YOUR CODE
        await this.DeleteFamilyItem();
    }
    ```

Vyberte **F5** ke spuštění aplikace.

Blahopřejeme! Úspěšně jste odstranili položku Azure Cosmos DB.

## <a id="DeleteDatabase"></a>Krok 10: Odstranění databáze
Teď odstraníme naší databázi. Odstraněním vytvořené databáze dojde k odebrání databáze a všech jejích podřízených prostředků (kontejnery, položky a všechny uložené procedury, uživatelsky definované funkce a aktivační události). Budeme se také vyřadit **CosmosClient** instance.

1. Zkopírujte a vložte **DeleteDatabaseAndCleanup** metodu vaše **DeleteFamilyItem** metody.
    ```csharp
    /*
    Delete the database and dispose of the Cosmos Client instance
    */
    private async Task DeleteDatabaseAndCleanup()
    {
        CosmosDatabaseResponse databaseResourceResponse = await this.database.DeleteAsync();
        // Also valid: await this.cosmosClient.Databases["FamilyDatabase"].DeleteAsync();

        Console.WriteLine("Deleted Database: {0}\n", this.databaseId);

        //Dispose of CosmosClient
        this.cosmosClient.Dispose();
    }
    ```
1. Přidejte volání do ``DeleteDatabaseAndCleanup`` v ``GetStartedDemo`` metody.
    
    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabase(); 
        await this.CreateContainer();
        await this.AddItemsToContainer();
        await this.RunQuery();
        await this.ReplaceFamilyItem();
        await this.DeleteFamilyItem();

        //ADD THIS PART TO YOUR CODE        
        await this.DeleteDatabaseAndCleanup();
    }
    ```

Vyberte **F5** ke spuštění aplikace.

Blahopřejeme! Úspěšně jste odstranili databázi Azure Cosmos DB.

## <a id="Run"></a>Krok 11: Spuštění celé konzolové aplikace jazyka C#
Stisknutím klávesy F5 v sadě Visual Studio k vytvoření a spuštění aplikace v režimu ladění.

Měli byste vidět výstupní celou aplikaci v okně konzoly. Výstup bude zobrazovat výsledky dotazů, které jsme přidali, a měl by odpovídat ukázkovému textu níže.

```
Beginning operations...

Created Database: FamilyDatabase

Created Container: FamilyContainer

Created item in database with id: Andersen.1 Operation consumed 11.43 RUs.

Created item in database with id: Wakefield.7 Operation consumed 14.29 RUs.

Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'

        Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}

Updated Family [Wakefield,Wakefield.7].
        Body is now: {"id":"Wakefield.7","LastName":"Wakefield","Parents":[{"FamilyName":"Wakefield","FirstName":"Robin"},{"FamilyName":"Miller","FirstName":"Ben"}],"Children":[{"FamilyName":"Merriam","FirstName":"Jesse","Gender":"female","Grade":6,"Pets":[{"GivenName":"Goofy"},{"GivenName":"Shadow"}]},{"FamilyName":"Miller","FirstName":"Lisa","Gender":"female","Grade":1,"Pets":null}],"Address":{"State":"NY","County":"Manhattan","City":"NY"},"IsRegistered":true}

Deleted Family [Wakefield,Wakefield.7]

Deleted Database: FamilyDatabase

End of demo, press any key to exit.
```

Blahopřejeme! Dokončili jste tento kurz a máte funkční konzolovou aplikaci jazyka C#!

## <a id="GetSolution"></a>Získání úplného řešení kurzu
Pokud jste neměli dostatek času k dokončení kroků v tomto kurzu nebo si jen chcete stáhnout ukázky kódu, můžete je získat z [GitHubu](https://github.com/Azure-Samples/cosmos-dotnet-getting-started). 

K vytvoření řešení GetStarted budete potřebovat toto:

* Aktivní účet Azure. Pokud žádný nemáte, můžete si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/).
* [Účet služby Azure Cosmos DB][cosmos-db-create-account].
* Řešení [GetStarted](https://github.com/Azure-Samples/cosmos-dotnet-getting-started) dostupné na GitHubu

Pokud chcete obnovit odkazy na Azure Cosmos DB .NET SDK v sadě Visual Studio, klikněte pravým tlačítkem **GetStarted** řešení v Průzkumníku řešení a pak klikněte na tlačítko **obnovit balíčky NuGet**. Dále v souboru App.config aktualizujte hodnoty EndPointUri a PrimaryKey, jak je popsáno v [připojit k účtu služby Azure Cosmos DB](#Connect).

To je vše, sestavení a máte hotovo!


## <a name="next-steps"></a>Další postup
* Chcete komplexnější kurz pro ASP.NET MVC? Viz [Kurz k ASP.NET MVC: Vývoj webové aplikace s použitím služby Azure Cosmos DB](sql-api-dotnet-application-preview.md).
* Chcete testovat škálování a výkon pomocí služby Azure Cosmos DB? Viz [Testování výkonu a škálování pomocí služby Azure Cosmos DB](performance-testing.md).
* Přečtěte si, jak [sledovat požadavky, využití a úložiště Azure Cosmos DB](monitor-accounts.md).
* Spouštějte dotazy proti ukázkovým datovým sadám v [Query Playground](https://www.documentdb.com/sql/demo).
* Další informace o Cosmos Azure DB najdete v tématu [Vítá vás Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction).

[cosmos-db-create-account]: create-sql-api-dotnet-preview.md#create-account
