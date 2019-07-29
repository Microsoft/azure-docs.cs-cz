---
title: Vytvoření konzolové aplikace .NET pro správu dat v Azure Cosmos DB účtu rozhraní SQL API
description: Naučte se vytvářet Azure Cosmos DB prostředky rozhraní SQL API pomocí C# konzolové aplikace.
author: kirankumarkolli
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 07/23/2019
ms.author: kirankk
ms.openlocfilehash: 37b6a9947d3cabe1f566f842e321229efe9d03b6
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68598528"
---
# <a name="build-a-net-console-app-to-manage-data-in-azure-cosmos-db-sql-api-account"></a>Vytvoření konzolové aplikace .NET pro správu dat v Azure Cosmos DB účtu rozhraní SQL API

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
>

Vítejte v úvodním kurzu Azure Cosmos DB SQL API. Až projdete tímto kurzem, budete mít konzolovou aplikaci, která vytváří prostředky Azure Cosmos DB a dotazuje se na ně. V tomto kurzu se používá [verze 3.0](https://www.nuget.org/packages/Microsoft.Azure.Cosmos) sady Azure Cosmos DB .NET SDK, na kterou se dá cílit [.NET Framework](https://dotnet.microsoft.com/download) nebo [.NET Core](https://dotnet.microsoft.com/download).

Tento kurz zahrnuje:

> [!div class="checklist"]
> * Vytvoření a připojení k účtu Azure Cosmos
> * Konfigurace projektu v aplikaci Visual Studio
> * Vytvoření databáze a kontejneru
> * Přidání položek do kontejneru
> * Dotazování kontejneru
> * Operace CRUD u položky
> * Odstranění databáze

Nemáte čas? Nevadí! Úplné řešení je k dispozici na [GitHubu](https://github.com/Azure-Samples/cosmos-dotnet-getting-started). Přejděte do [části získání kompletního řešení kurzu](#GetSolution) , kde najdete rychlé pokyny.

Můžeme začít!

## <a name="prerequisites"></a>Požadavky

* Aktivní účet Azure. Pokud žádný nemáte, můžete si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/).

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Krok 1: Vytvoření účtu služby Azure Cosmos DB
Vytvořme účet služby Azure Cosmos DB. Pokud již máte účet, který chcete použít, můžete přeskočit na [Nastavení řešení v nástroji Visual Studio](#SetupVS). Pokud používáte emulátor Azure Cosmos DB, nastavte emulátor pomocí postupu v [Azure Cosmos DB emulátoru](local-emulator.md) a přeskočte dopředu na [nastavení projektu sady Visual Studio](#SetupVS).

[!INCLUDE [create-dbaccount-preview](../../includes/cosmos-db-create-dbaccount-preview.md)]

## <a id="SetupVS"></a>Krok 2: Nastavení projektu sady Visual Studio
1. Otevřete v počítači **Visual Studio 2017**.
1. V nabídce **Soubor** vyberte **Nový** a zvolte **Projekt**.
1. V dialogovém okně  /  **Nový projekt** vyberte **Visual C#**  **Console App (.NET Framework)** , pojmenujte projekt a klikněte na **OK**.
    ![Snímek obrazovky okna Nový projekt](./media/sql-api-get-started/dotnet-tutorial-visual-studio-new-project.png)

    > [!NOTE]
    > Pro  / cíl .NET Core v dialogovém okně **Nový projekt** vyberte **Visual C#**  **Console App (.NET Core)** , pojmenujte svůj projekt a pak klikněte na **OK** .

1. V **Průzkumníku řešení** klikněte pravým tlačítkem na novou konzolovou aplikaci v rámci řešení sady Visual Studio a pak klikněte na **Spravovat balíčky NuGet**.

    ![Snímek obrazovky místní nabídky projektu](./media/sql-api-get-started/dotnet-tutorial-visual-studio-manage-nuget.png)
1. V **NuGet** klikněte na tlačítko **Procházet**a typ **Microsoft.Azure.Cosmos** do vyhledávacího pole.
1. Najděte ve výsledcích **Microsoft.Azure.Cosmos** a klikněte na tlačítko **nainstalovat**.
   ID balíčku s klientskou knihovnou rozhraní Azure Cosmos DB SQL API je [Microsoft Azure Cosmos DB Client Library](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/).
   ![Snímek obrazovky s nabídkou NuGet pro hledání Azure Cosmos DB SDK klienta](./media/sql-api-get-started/dotnet-tutorial-visual-studio-manage-nuget-2.png)

    Pokud se vám zobrazí zpráva týkající se kontroly změn řešení, klikněte na **OK**. Pokud se vám zobrazí zpráva týkající se přijetí licence, klikněte na **Souhlasím**.

Výborně! Teď když jsme dokončili nastavování, napišme nějaký kód. Úplný projekt s kódem pro tento kurz najdete na [GitHubu](https://github.com/Azure-Samples/cosmos-dotnet-getting-started).

## <a id="Connect"></a>Krok 3: Připojení k účtu Azure Cosmos DB
1. Nejprve nahraďte odkazy na začátku vaší C# aplikace v souboru **program.cs** pomocí těchto odkazů:

   ```csharp
   using System;
   using System.Threading.Tasks;
   using System.Configuration;
   using System.Collections.Generic;
   using System.Net;
   using Microsoft.Azure.Cosmos;
   ```

1. Nyní přidejte tyto konstanty a proměnné do vaší veřejné třídy ``Program``.

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
        private Database database;

        // The container we will create.
        private Container container;

        // The name of the database and container we will create
        private string databaseId = "FamilyDatabase";
        private string containerId = "FamilyContainer";
    }
    ```

    Mějte na paměti, pokud jste se seznámili s předchozí verzí sady .NET SDK, můžou sloužit k zobrazení podmínek kolekce a dokumentu. Protože Azure Cosmos DB podporuje několik modelů rozhraní API, verze 3.0 a sady .NET SDK používá obecné podmínky "kontejner" a "položka". Kontejner může být kolekce, graf nebo tabulka. Položka představuje obsah uvnitř kontejneru a může to být dokument, pár hrany a vrcholu nebo řádek. [Další informace o databází, kontejnerů a položek.](databases-containers-items.md)

1. Načte adresu URL koncového bodu a primární klíč z [Azure Portal](https://portal.azure.com).

    Na webu Azure Portal přejděte do účtu služby Azure Cosmos DB a klikněte na **Klíče**.

    Zkopírujte identifikátor URI z portálu a vložte ho do `<your endpoint URL>` ```Program.cs``` souboru. Zkopírujte primární klíč z portálu a vložte ho do `<your primary key>`.

   ![Snímek obrazovky, ze kterého se mají získat Azure Cosmos DB klíče Azure Portal](./media/sql-api-get-started/dotnet-tutorial-portal-keys.png)

1. V dalším kroku vytvoříme novou instanci ```CosmosClient``` a nastavíme pro náš program nějaké generování uživatelského rozhraní.

    Pod metodou **Main** přidejte novou asynchronní úlohu nazvanou **GetStartedDemoAsync**, která vytvoří instanci našeho nového ```CosmosClient```. **GetStartedDemoAsync** použijeme jako vstupní bod, který volá metody, které pracují s Azure Cosmos DB prostředky.

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

1. Přidejte následující kód pro spuštění asynchronní úlohy **GetStartedDemoAsync** z metody **Main** . Metoda **Main** zachytí výjimky a vypíše je do konzoly.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=Main)]

1. Zvolte **F5** pro spuštění aplikace. Výstup okna konzoly zobrazí zprávu `End of demo, press any key to exit.` potvrzující, že připojení k Azure Cosmos DB bylo provedeno. Potom můžete okno konzoly zavřít.

Blahopřejeme! Úspěšně jste se připojili k účtu Azure Cosmos DB. 

## <a name="step-4-create-a-database"></a>Krok 4: Vytvoření databáze
Databázi lze vytvořit buď pomocí ``CosmosClient`` funkce [**CreateDatabaseIfNotExistsAsync**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclient) nebo [**metody createdatabaseasync**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclient) třídy. Databáze je logický kontejner položek rozdělených napříč kontejnery.

1. Zkopírujte a vložte metodu **metody createdatabaseasync** pod vaší metodou **GetStartedDemoAsync** . **Metody createdatabaseasync** vytvoří novou databázi s ID ``FamilyDatabase`` , pokud ještě neexistuje, s ``databaseId`` ID, které je zadáno v poli. 

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=CreateDatabaseAsync&highlight=7)]

1. Zkopírujte a vložte kód níže, kde jste vytvořili instanci CosmosClient k volání metody **metody createdatabaseasync** , kterou jste právě přidali.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);

        //ADD THIS PART TO YOUR CODE
        await this.CreateDatabaseAsync();
    }
    ```

    V tomto okamžiku by váš kód měl teď vypadat jako to s vyplněným koncovým bodem a primárním klíčem.

    ```csharp
    using System;
    using System.Threading.Tasks;
    using System.Configuration;
    using System.Collections.Generic;
    using System.Net;
    using Microsoft.Azure.Cosmos;

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
            private Database database;

            // The container we will create.
            private Container container;

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

            /// <summary>
            /// Entry point to call methods that operate on Azure Cosmos DB resources in this sample
            /// </summary>
            public async Task GetStartedDemoAsync()
            {
                // Create a new instance of the Cosmos Client
                this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
                await this.CreateDatabaseAsync();
            }

            /// <summary>
            /// Create the database if it does not exist
            /// </summary>
            private async Task CreateDatabaseAsync()
            {
                // Create a new database
                this.database = await this.cosmosClient.CreateDatabaseIfNotExistsAsync(databaseId);
                Console.WriteLine("Created Database: {0}\n", this.database.Id);
            }
        }
    }
    ```

Zvolte **F5** pro spuštění aplikace.

Blahopřejeme! Úspěšně jste vytvořili databázi Azure Cosmos DB.  

## <a id="CreateColl"></a>Krok 5: Vytvoření kontejneru
> [!WARNING]
> Volání metody **CreateContainerIfNotExistsAsync** vytvoří nový kontejner, který má vliv na cenu. Další podrobnosti najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/cosmos-db/).
>
>

Kontejner lze vytvořit pomocí funkce [**CreateContainerIfNotExistsAsync**](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync?view=azure-dotnet#Microsoft_Azure_Cosmos_Database_CreateContainerIfNotExistsAsync_Microsoft_Azure_Cosmos_ContainerProperties_System_Nullable_System_Int32__Microsoft_Azure_Cosmos_RequestOptions_System_Threading_CancellationToken_) nebo [**CreateContainerAsync**](/dotnet/api/microsoft.azure.cosmos.database.createcontainerasync?view=azure-dotnet#Microsoft_Azure_Cosmos_Database_CreateContainerAsync_Microsoft_Azure_Cosmos_ContainerProperties_System_Nullable_System_Int32__Microsoft_Azure_Cosmos_RequestOptions_System_Threading_CancellationToken_) ve třídě **CosmosDatabase** . Kontejner se skládá z položek (dokumenty JSON, pokud je SQL API) a přidružená aplikační logika na straně serveru v JavaScriptu, například uložené procedury, uživatelsky definované funkce a triggery.

1. Zkopírujte a vložte metodu **CreateContainerAsync** pod vaší metodou **metody createdatabaseasync** . **CreateContainerAsync** vytvoří nový kontejner s ID ``FamilyContainer`` , pokud ještě neexistuje, s ``containerId`` identifikátorem zadaným z pole, které je rozdělené podle ``LastName`` vlastnosti.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=CreateContainerAsync&highlight=9)]

1. Zkopírujte a vložte kód níže, kde jste vytvořili instanci CosmosClient k volání metody **CreateContainer** , kterou jste právě přidali.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();

        //ADD THIS PART TO YOUR CODE
        await this.CreateContainerAsync();
    }
    ```

   Zvolte **F5** pro spuštění aplikace.

Blahopřejeme! Úspěšně jste vytvořili kontejner Azure Cosmos DB.  

## <a id="CreateDoc"></a>Krok 6: Přidat položky do kontejneru
Položku lze vytvořit pomocí funkce [**CreateItemAsync**](/dotnet/api/microsoft.azure.cosmos.container.createitemasync?view=azure-dotnet#Microsoft_Azure_Cosmos_Container_CreateItemAsync__1___0_System_Nullable_Microsoft_Azure_Cosmos_PartitionKey__Microsoft_Azure_Cosmos_ItemRequestOptions_System_Threading_CancellationToken_) třídy **CosmosContainer** . Při používání rozhraní SQL API jsou položky dokumenty, které představují uživatelem definovaný (libovolný) obsah JSON. Nyní můžete vložit položku do kontejneru Azure Cosmos DB.

Nejprve vytvoříme třídu **Family** , která bude představovat objekty uložené v rámci Azure Cosmos DB v této ukázce. Kromě toho vytvoříme i podtřídy **Parent**, **Child**, **Pet** a **Address**, které se použijí v rámci **Family**. Položka poznámky musí mít vlastnost **ID** serializovanou jako **ID** ve formátu JSON.

1. Vyberte **CTRL + SHIFT + a** a otevřete tak dialogové okno **Přidat novou položku** . Přidejte do projektu novou třídu **Family.cs** .

    ![Snímek obrazovky s přidáním nové třídy Family.cs do projektu](./media/sql-api-get-started/dotnet-tutorial-visual-studio-add-family-class.png)

1. Zkopírujte a vložte třídu **rodin**, nadřazených , podřízených, **PET**a **Address** do **Family.cs**.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Family.cs)]

1. Přejděte zpátky na **program.cs** a přidejte do své metody **CreateContainerAsync** metodu **AddItemsToContainerAsync** .
Kód zkontroluje, zda ještě před vytvořením položky se stejným ID neexistuje. Vložíme dvě položky, jednu z nich pro rodinu Andersen a rodinu Wakefieldů.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=AddItemsToContainerAsync)]

1. Do ``GetStartedDemoAsync`` metody přidejte volání ``AddItemsToContainerAsync`` .

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();

        //ADD THIS PART TO YOUR CODE
        await this.AddItemsToContainerAsync();
    }
    ```

Zvolte **F5** pro spuštění aplikace.

Blahopřejeme! Úspěšně jste vytvořili dvě položky Azure Cosmos DB.  

## <a id="Query"></a>Krok 7: Dotazy Azure Cosmos DB prostředky
Azure Cosmos DB podporuje bohaté [dotazy](sql-api-sql-query.md) na dokumenty JSON uložené v každé z kolekcí. Následující vzorový kód ukazuje, jak spustit dotaz proti položkám, které jsme vložili v předchozím kroku.

1. Zkopírujte a vložte metodu **QueryItemsAsync** pod vaší metodou **AddItemsToContainerAsync** .

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=QueryItemsAsync&highlight=10-11,17-18)]

1. Do ``GetStartedDemoAsync`` metody přidejte volání ``QueryItemsAsync`` .

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();
        await this.AddItemsToContainerAsync();

        //ADD THIS PART TO YOUR CODE
        await this.QueryItemsAsync();
    }
    ```

Zvolte **F5** pro spuštění aplikace.

Blahopřejeme! Úspěšně jste dotazováni na kontejner Azure Cosmos DB.

## <a id="ReplaceItem"></a>Krok 8: Nahradit položku JSON
Nyní budeme aktualizovat položku v Azure Cosmos DB.

1. Zkopírujte a vložte metodu **ReplaceFamilyItemAsync** pod vaší metodou **QueryItemsAsync** . Poznámka: měníme ``IsRegistered`` vlastnost rodiny ``Grade`` a jednu z podřízených objektů.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=ReplaceFamilyItemAsync&highlight=15)]

1. Do ``GetStartedDemoAsync`` metody přidejte volání ``ReplaceFamilyItemAsync`` .

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();
        await this.AddItemsToContainerAsync();
        await this.QueryItemsAsync();

        //ADD THIS PART TO YOUR CODE
        await this.ReplaceFamilyItemAsync();
    }
    ```

   Zvolte **F5** pro spuštění aplikace.

Blahopřejeme! Úspěšně jste nahradili položku Azure Cosmos DB.

## <a id="DeleteDocument"></a>Krok 9: Odstranit položku
Nyní odstraníme položku v Azure Cosmos DB.

1. Zkopírujte a vložte metodu **DeleteFamilyItemAsync** pod vaší metodou **ReplaceFamilyItemAsync** .

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=DeleteFamilyItemAsync&highlight=10)]

1. Do ``GetStartedDemoAsync`` metody přidejte volání ``DeleteFamilyItemAsync`` .

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();
        await this.AddItemsToContainerAsync();
        await this.QueryItemsAsync();
        await this.ReplaceFamilyItemAsync();

        //ADD THIS PART TO YOUR CODE
        await this.DeleteFamilyItemAsync();
    }
    ```

Zvolte **F5** pro spuštění aplikace.

Blahopřejeme! Úspěšně jste odstranili položku Azure Cosmos DB.

## <a id="DeleteDatabase"></a>Krok 10: Odstranění databáze
Nyní odstraníme naši databázi. Odstraněním vytvořené databáze dojde k odebrání databáze a všech podřízených prostředků (kontejnerů, položek a všech uložených procedur, uživatelsky definovaných funkcí a triggerů). Odstraníme také instanci **CosmosClient** .

1. Zkopírujte a vložte metodu **DeleteDatabaseAndCleanupAsync** pod vaší metodou **DeleteFamilyItemAsync** .

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=DeleteDatabaseAndCleanupAsync)]

1. Do ``GetStartedDemoAsync`` metody přidejte volání ``DeleteDatabaseAndCleanupAsync`` .

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=GetStartedDemoAsync&highlight=14)]

Zvolte **F5** pro spuštění aplikace.

Blahopřejeme! Úspěšně jste odstranili databázi Azure Cosmos DB.

## <a id="Run"></a>Krok 11: Spustit C# konzolovou aplikaci dohromady!
V aplikaci Visual Studio vyberte F5 a sestavte a spusťte aplikaci v režimu ladění.

V okně konzoly byste měli vidět výstup celé aplikace. Výstup bude zobrazovat výsledky dotazů, které jsme přidali, a měl by odpovídat ukázkovému textu níže.

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
* [Účet Azure Cosmos DB][cosmos-db-create-account].
* Řešení [GetStarted](https://github.com/Azure-Samples/cosmos-dotnet-getting-started) dostupné na GitHubu

Chcete-li obnovit odkazy na sadu Azure Cosmos DB .NET SDK v aplikaci Visual Studio, klikněte pravým tlačítkem na řešení getstarted v Průzkumník řešení a pak klikněte na možnost **obnovit balíčky NuGet**. Dále v souboru App. config aktualizujte hodnoty hodnot endpointuri a PrimaryKey, jak je popsáno v tématu [připojení k Azure Cosmos DBmu účtu](#Connect).

To je to, jak ho sestavit a vy budete vy.

## <a name="next-steps"></a>Další postup
* Chcete komplexnější kurz pro ASP.NET MVC? Viz [kurz k ASP.NET MVC: Vývoj webových aplikací pomocí Azure Cosmos DB](sql-api-dotnet-application-preview.md).
* Chcete testovat škálování a výkon pomocí služby Azure Cosmos DB? Viz [Testování výkonu a škálování pomocí služby Azure Cosmos DB](performance-testing.md).
* Přečtěte si, jak [sledovat požadavky, využití a úložiště Azure Cosmos DB](monitor-accounts.md).
* Spouštějte dotazy proti ukázkovým datovým sadám v [Query Playground](https://www.documentdb.com/sql/demo).
* Další informace o Cosmos Azure DB najdete v tématu [Vítá vás Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction).

[cosmos-db-create-account]: create-sql-api-java.md#create-a-database-account
