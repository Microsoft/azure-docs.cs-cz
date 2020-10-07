---
title: 'Kurz: Vytvoření aplikace konzoly .NET pro správu dat v Azure Cosmos DB účtu rozhraní SQL API'
description: 'Kurz: Naučte se vytvářet Azure Cosmos DB prostředky rozhraní SQL API pomocí konzolové aplikace v jazyce C#.'
author: kirankumarkolli
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: kirankk
ms.custom: devx-track-csharp
ms.openlocfilehash: 912b4966312d8925f70deeed99042d2701641f49
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2020
ms.locfileid: "91801507"
---
# <a name="tutorial-build-a-net-console-app-to-manage-data-in-azure-cosmos-db-sql-api-account"></a>Kurz: Vytvoření aplikace konzoly .NET pro správu dat v Azure Cosmos DB účtu rozhraní SQL API

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
>

Vítejte v úvodním kurzu Azure Cosmos DB SQL API. Až projdete tímto kurzem, budete mít konzolovou aplikaci, která vytváří prostředky Azure Cosmos DB a dotazuje se na ně.

V tomto kurzu se používá verze 3,0 nebo novější [Azure Cosmos DB .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos). Můžete pracovat s [.NET Framework nebo .NET Core](https://dotnet.microsoft.com/download).

Tento kurz zahrnuje:

> [!div class="checklist"]
>
> * Vytvoření účtu Azure Cosmos a připojení k němu
> * Konfigurace projektu v aplikaci Visual Studio
> * Vytvoření databáze a kontejneru
> * Přidání položek do kontejneru
> * Dotazování kontejneru
> * Provádění operací vytvoření, čtení, aktualizace a odstranění (CRUD) u položky
> * Odstranění databáze

Nemáte čas? Ale žádný strach. Úplné řešení je k dispozici na [GitHubu](https://github.com/Azure-Samples/cosmos-dotnet-getting-started). Přejděte do [části získání kompletního řešení kurzu](#GetSolution) , kde najdete rychlé pokyny.

Můžeme začít!

## <a name="prerequisites"></a>Předpoklady

* Aktivní účet Azure. Pokud žádný nemáte, můžete si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/).

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Krok 1: Vytvoření účtu služby Azure Cosmos DB

Vytvořme účet služby Azure Cosmos DB. Pokud již máte účet, který chcete použít, přeskočte tuto část. Chcete-li použít emulátor Azure Cosmos DB, postupujte podle pokynů v [Azure Cosmos DB emulátoru](local-emulator.md) a nastavte emulátor. Potom přejděte ke [kroku 2: nastavení projektu sady Visual Studio](#SetupVS).

[!INCLUDE [create-dbaccount-preview](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="step-2-set-up-your-visual-studio-project"></a><a id="SetupVS"></a>Krok 2: nastavení projektu sady Visual Studio

1. Otevřete Visual Studio a vyberte **vytvořit nový projekt**.
1. V možnosti **vytvořit nový projekt**zvolte **Konzolová aplikace (.NET Framework)** pro C# a pak vyberte **Další**.
1. Pojmenujte projekt *CosmosGettingStartedTutorial*a pak vyberte **vytvořit**.

    :::image type="content" source="./media/sql-api-get-started/configure-cosmos-getting-started-2019.png" alt-text="Konfigurace projektu":::

1. V **Průzkumník řešení**klikněte pravým tlačítkem myši na novou konzolovou aplikaci, která je v rámci řešení sady Visual Studio, a vyberte možnost **Spravovat balíčky NuGet**.
1. V okně **Správce balíčků NuGet**vyberte **Procházet** a vyhledejte *Microsoft. Azure. Cosmos*. Zvolte **Microsoft. Azure. Cosmos** a vyberte **nainstalovat**.

   :::image type="content" source="./media/sql-api-get-started/cosmos-getting-started-manage-nuget-2019.png" alt-text="Konfigurace projektu":::

   ID balíčku s klientskou knihovnou rozhraní Azure Cosmos DB SQL API je [Microsoft Azure Cosmos DB Client Library](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/).

Výborně! Teď když jsme dokončili nastavování, napišme nějaký kód. Dokončený projekt tohoto kurzu najdete v tématu [vývoj aplikace konzoly .NET pomocí Azure Cosmos DB](https://github.com/Azure-Samples/cosmos-dotnet-getting-started).

## <a name="step-3-connect-to-an-azure-cosmos-db-account"></a><a id="Connect"></a>Krok 3: Připojení k účtu služby Azure Cosmos DB

1. Nahraďte odkazy na začátku aplikace v C# v souboru *program.cs* pomocí těchto odkazů:

   ```csharp
   using System;
   using System.Threading.Tasks;
   using System.Configuration;
   using System.Collections.Generic;
   using System.Net;
   using Microsoft.Azure.Cosmos;
   ```

1. Do své třídy přidejte tyto konstanty a proměnné `Program` .

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

   > [!NOTE]
   > Pokud jste obeznámeni s předchozí verzí sady .NET SDK, můžete být obeznámeni se *shromažďováním* a *dokumentem*s podmínkami. Vzhledem k tomu, že Azure Cosmos DB podporuje více modelů rozhraní API, verze 3,0 sady .NET SDK používá obecný pojem *kontejner* a *položka*. *Kontejner* může být kolekce, graf nebo tabulka. *Položka* může být dokument, okraj, vrchol nebo řádek a je obsahem uvnitř kontejneru. Další informace najdete v tématu [práce s databázemi, kontejnery a položkami v Azure Cosmos DB](databases-containers-items.md).

1. Otevřete web [Azure Portal](https://portal.azure.com). Vyhledejte účet Azure Cosmos DB a pak vyberte **klíče**.

   :::image type="content" source="./media/sql-api-get-started/cosmos-getting-started-portal-keys.png" alt-text="Konfigurace projektu":::

1. V *program.cs*nahraďte `<your endpoint URL>` hodnotou **identifikátoru URI**. Nahraďte `<your primary key>` hodnotou **primárního klíče**.

1. Pod metodou **Main** přidejte novou asynchronní úlohu s názvem **GetStartedDemoAsync**, která vytvoří instanci našeho nového `CosmosClient` .

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

    **GetStartedDemoAsync** používáme jako vstupní bod, který volá metody, které pracují s Azure Cosmos DB prostředky.

1. Přidejte následující kód pro spuštění asynchronní úlohy **GetStartedDemoAsync** z metody **Main** . Metoda **Main** zachycuje výjimky a vypisuje je do konzoly.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=Main)]

1. Zvolte F5 pro spuštění aplikace.

    Konzola zobrazí zprávu: **konec ukázky, stisknutím libovolné klávesy ukončete.** Tato zpráva potvrdí, že vaše aplikace vytvořila připojení k Azure Cosmos DB. Potom můžete okno konzoly zavřít.

Blahopřejeme! Úspěšně jste se připojili k účtu Azure Cosmos DB.

## <a name="step-4-create-a-database"></a>Krok 4: Vytvoření databáze

Databáze je logický kontejner položek rozdělených napříč kontejnery. Pomocí `CreateDatabaseIfNotExistsAsync` metody nebo `CreateDatabaseAsync` třídy [CosmosClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclient) lze vytvořit databázi.

1. Zkopírujte a vložte `CreateDatabaseAsync` metodu pod vaší `GetStartedDemoAsync` metodou.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=CreateDatabaseAsync&highlight=7)]

    `CreateDatabaseAsync` Vytvoří novou databázi s ID `FamilyDatabase` , pokud ještě neexistuje, která má ID zadané v `databaseId` poli.

1. Zkopírujte a vložte kód níže, kde vytváříte instanci CosmosClient k volání metody **metody createdatabaseasync** , kterou jste právě přidali.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);

        //ADD THIS PART TO YOUR CODE
        await this.CreateDatabaseAsync();
    }
    ```

    Váš *program.cs* by teď měl vypadat jako to s vyplněným koncovým bodem a primárním klíčem.

    ```csharp
    using System;
    using System.Threading.Tasks;
    using System.Configuration;
    using System.Collections.Generic;
    using System.Net;
    using Microsoft.Azure.Cosmos;

    namespace CosmosGettingStartedTutorial
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

1. Zvolte F5 pro spuštění aplikace.

   > [!NOTE]
   > Pokud se zobrazí chyba "nedostupná výjimka služby 503", je možné, že brána firewall zablokuje požadované [porty](performance-tips.md#networking) pro režim přímého připojení. Pokud chcete tento problém vyřešit, otevřete požadované porty nebo použijte připojení režimu brány, jak je znázorněno v následujícím kódu:
   ```csharp
     // Create a new instance of the Cosmos Client in Gateway mode
     this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey, new CosmosClientOptions()
            {
                ConnectionMode = ConnectionMode.Gateway
            });
   ```

Blahopřejeme! Úspěšně jste vytvořili databázi Azure Cosmos.  

## <a name="step-5-create-a-container"></a><a id="CreateColl"></a>Krok 5: vytvoření kontejneru

> [!WARNING]
> Metoda `CreateContainerIfNotExistsAsync` vytvoří nový kontejner, který má vliv na ceny. Další podrobnosti najdete na naší stránce s [cenami](https://azure.microsoft.com/pricing/details/cosmos-db/).
>
>

Kontejner lze vytvořit pomocí metody [**CreateContainerIfNotExistsAsync**](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync?view=azure-dotnet&preserve-view=true#Microsoft_Azure_Cosmos_Database_CreateContainerIfNotExistsAsync_Microsoft_Azure_Cosmos_ContainerProperties_System_Nullable_System_Int32__Microsoft_Azure_Cosmos_RequestOptions_System_Threading_CancellationToken_) nebo [**CreateContainerAsync**](/dotnet/api/microsoft.azure.cosmos.database.createcontainerasync?view=azure-dotnet&preserve-view=true#Microsoft_Azure_Cosmos_Database_CreateContainerAsync_Microsoft_Azure_Cosmos_ContainerProperties_System_Nullable_System_Int32__Microsoft_Azure_Cosmos_RequestOptions_System_Threading_CancellationToken_) ve `CosmosDatabase` třídě. Kontejner se skládá z položek (dokumenty JSON, pokud je SQL API) a přidružená aplikační logika na straně serveru v JavaScriptu, například uložené procedury, uživatelsky definované funkce a triggery.

1. Zkopírujte a vložte `CreateContainerAsync` metodu pod vaší `CreateDatabaseAsync` metodou. `CreateContainerAsync`  Vytvoří nový kontejner s ID `FamilyContainer` , pokud ještě neexistuje, pomocí ID zadaného v `containerId` poli děleného `LastName` vlastností.

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

1. Zvolte F5 pro spuštění aplikace.

Blahopřejeme! Úspěšně jste vytvořili kontejner Azure Cosmos.  

## <a name="step-6-add-items-to-the-container"></a><a id="CreateDoc"></a>Krok 6: Přidání položek do kontejneru

Metoda [**CreateItemAsync**](/dotnet/api/microsoft.azure.cosmos.container.createitemasync?view=azure-dotnet&preserve-view=true#Microsoft_Azure_Cosmos_Container_CreateItemAsync__1___0_System_Nullable_Microsoft_Azure_Cosmos_PartitionKey__Microsoft_Azure_Cosmos_ItemRequestOptions_System_Threading_CancellationToken_) `CosmosContainer` třídy může vytvořit položku. Při použití rozhraní SQL API se položky procházejí jako dokumenty, což je uživatelsky definovaný libovolný obsah JSON. Nyní můžete vložit položku do kontejneru Azure Cosmos.

Nejprve vytvoříme `Family` třídu, která představuje objekty uložené v rámci Azure Cosmos DB v této ukázce. Vytvoříme také `Parent` `Child` `Pet` `Address` podtřídy, které jsou použity v rámci `Family` . Položka musí mít `Id` serializovanou vlastnost jako `id` ve formátu JSON.

1. Kliknutím na Ctrl + Shift + A otevřete **Přidat novou položku**. Přidejte `Family.cs` do projektu novou třídu.

    :::image type="content" source="./media/sql-api-get-started/cosmos-getting-started-add-family-class-2019.png" alt-text="Konfigurace projektu":::

1. Zkopírujte a vložte `Family` třídu, `Parent` ,, a `Child` `Pet` `Address` do `Family.cs` .

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Family.cs)]


1. Zpět v *program.cs*přidejte `AddItemsToContainerAsync` metodu za `CreateContainerAsync` metodu.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=AddItemsToContainerAsync)]


    Kód zkontroluje, že položka se stejným ID ještě neexistuje. Vložíme dvě položky, jednu z nich pro *rodinu Andersen* a *rodinu wakefieldů*.

1. Do metody přidejte volání `AddItemsToContainerAsync` `GetStartedDemoAsync` .

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

1. Zvolte F5 pro spuštění aplikace.

Blahopřejeme! Úspěšně jste vytvořili dvě položky Azure Cosmos.  

## <a name="step-7-query-azure-cosmos-db-resources"></a><a id="Query"></a>Krok 7: Dotazování prostředků Azure Cosmos DB

Azure Cosmos DB podporuje bohaté dotazy na dokumenty JSON uložené v jednotlivých kontejnerech. Další informace najdete v tématu [Začínáme s dotazy SQL](sql-api-sql-query.md). Následující vzorový kód ukazuje, jak spustit dotaz proti položkám, které jsme vložili v předchozím kroku.

1. Zkopírujte a vložte `QueryItemsAsync` metodu po své `AddItemsToContainerAsync` metodě.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=QueryItemsAsync&highlight=10-11,17-18)]

1. Do metody přidejte volání ``QueryItemsAsync`` ``GetStartedDemoAsync`` .

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

1. Zvolte F5 pro spuštění aplikace.

Blahopřejeme! Úspěšně jste dotazováni na kontejner Azure Cosmos.

## <a name="step-8-replace-a-json-item"></a><a id="ReplaceItem"></a>Krok 8: nahrazení položky JSON

Teď aktualizujeme položku v Azure Cosmos DB. Změníme `IsRegistered` vlastnost `Family` a na `Grade` jednu z podřízených objektů.

1. Zkopírujte a vložte `ReplaceFamilyItemAsync` metodu po své `QueryItemsAsync` metodě.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=ReplaceFamilyItemAsync&highlight=15)]

1. Do metody přidejte volání `ReplaceFamilyItemAsync` `GetStartedDemoAsync` .

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

1. Zvolte F5 pro spuštění aplikace.

Blahopřejeme! Úspěšně jste nahradili položku Azure Cosmos.

## <a name="step-9-delete-item"></a><a id="DeleteDocument"></a>Krok 9: odstranění položky

Nyní odstraníme položku v Azure Cosmos DB.

1. Zkopírujte a vložte `DeleteFamilyItemAsync` metodu po své `ReplaceFamilyItemAsync` metodě.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=DeleteFamilyItemAsync&highlight=10)]

1. Do metody přidejte volání `DeleteFamilyItemAsync` `GetStartedDemoAsync` .

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

1. Zvolte F5 pro spuštění aplikace.

Blahopřejeme! Úspěšně jste odstranili položku Azure Cosmos.

## <a name="step-10-delete-the-database"></a><a id="DeleteDatabase"></a>Krok 10: Odstranění databáze

Nyní odstraníme naši databázi. Odstraněním vytvořené databáze dojde k odebrání databáze a všech podřízených prostředků. Mezi prostředky patří kontejnery, položky a jakékoli uložené procedury, uživatelsky definované funkce a triggery. Odstraníme také `CosmosClient` instanci.

1. Zkopírujte a vložte `DeleteDatabaseAndCleanupAsync` metodu po své `DeleteFamilyItemAsync` metodě.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=DeleteDatabaseAndCleanupAsync)]

1. Do metody přidejte volání ``DeleteDatabaseAndCleanupAsync`` ``GetStartedDemoAsync`` .

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=GetStartedDemoAsync&highlight=14)]

1. Zvolte F5 pro spuštění aplikace.

Blahopřejeme! Úspěšně jste odstranili databázi Azure Cosmos.

## <a name="step-11-run-your-c-console-application-all-together"></a><a id="Run"></a>Krok 11: Spuštění celé konzolové aplikace jazyka C#!

V aplikaci Visual Studio vyberte F5 a sestavte a spusťte aplikaci v režimu ladění.

V okně konzoly byste měli vidět výstup celé aplikace. Výstup zobrazuje výsledky dotazů, které jsme přidali. Měl by odpovídat následujícímu ukázkovému textu.

```cmd
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

## <a name="get-the-complete-tutorial-solution"></a><a id="GetSolution"></a>Získání úplného řešení kurzu

Pokud jste neměli dostatek času k dokončení kroků v tomto kurzu, nebo chcete pouze stáhnout ukázky kódu, můžete si ho stáhnout.

K sestavení `GetStarted` řešení potřebujete následující požadavky:

* Aktivní účet Azure. Pokud žádný nemáte, můžete si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/).
* [Účet služby Azure Cosmos DB][cosmos-db-create-account].
* Řešení [GetStarted](https://github.com/Azure-Samples/cosmos-dotnet-getting-started) dostupné na GitHubu

Chcete-li obnovit odkazy na sadu Azure Cosmos DB .NET SDK v aplikaci Visual Studio, klikněte pravým tlačítkem na řešení v **Průzkumník řešení**a potom vyberte možnost **obnovit balíčky NuGet**. Dále v souboru *App.config* aktualizujte `EndPointUri` hodnoty a, jak je `PrimaryKey` popsáno v [kroku 3: připojení k účtu Azure Cosmos DB](#Connect).

To je to, jak ho sestavit a vy budete vy.

## <a name="next-steps"></a>Další kroky

* Chcete komplexnější kurz pro ASP.NET MVC? Viz [kurz: vývoj webové aplikace ASP.NET Core MVC pomocí Azure Cosmos DB pomocí sady .NET SDK](sql-api-dotnet-application.md).
* Chcete testovat škálování a výkon pomocí Azure Cosmos DB? Projděte si [testování výkonu a škálování pomocí Azure Cosmos DB](performance-testing.md).
* Informace o tom, jak monitorovat žádosti o Azure Cosmos DB, využití a úložiště, najdete [v tématu monitorování výkonu a metrik úložiště v Azure Cosmos DB](monitor-accounts.md).
* Chcete-li spustit dotazy pro ukázkovou datovou sadu, přečtěte si [testovací prostředí dotazů](https://www.documentdb.com/sql/demo).
* Další informace o Cosmos Azure DB najdete v tématu [Vítá vás Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction).

[cosmos-db-create-account]: create-sql-api-java.md#create-a-database-account
