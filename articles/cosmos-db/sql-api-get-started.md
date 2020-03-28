---
title: 'Kurz: Vytvoření aplikace konzoly .NET pro správu dat v účtu SQL API Azure Cosmos DB'
description: 'Kurz: Naučte se, jak vytvořit prostředky Azure Cosmos DB SQL API pomocí aplikace konzoly C#.'
author: kirankumarkolli
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: kirankk
ms.openlocfilehash: 2681b2199f321f695bc621ed5580319a5e907b34
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "78274015"
---
# <a name="tutorial-build-a-net-console-app-to-manage-data-in-azure-cosmos-db-sql-api-account"></a>Kurz: Vytvoření aplikace konzoly .NET pro správu dat v účtu SQL API Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
>

Vítá vás kurz azure cosmos DB SQL API začíná. Až projdete tímto kurzem, budete mít konzolovou aplikaci, která vytváří prostředky Azure Cosmos DB a dotazuje se na ně.

Tento kurz používá verzi 3.0 nebo novější sady [Azure Cosmos DB .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos). Můžete pracovat s [rozhraním .NET Framework nebo .NET Core](https://dotnet.microsoft.com/download).

Tento kurz zahrnuje:

> [!div class="checklist"]
>
> * Vytváření a připojování k účtu Azure Cosmos
> * Konfigurace projektu v sadě Visual Studio
> * Vytvoření databáze a kontejneru
> * Přidání položek do kontejneru
> * Dotazování kontejneru
> * Provádění operací vytváření, čtení, aktualizace a odstraňování (CRUD) u položky
> * Odstranění databáze

Nemáte čas? Nevadí! Úplné řešení je k dispozici na [GitHubu](https://github.com/Azure-Samples/cosmos-dotnet-getting-started). Přejděte do [části Získat kompletní výukové řešení](#GetSolution) pro rychlé pokyny.

Můžeme začít!

## <a name="prerequisites"></a>Požadavky

* Aktivní účet Azure. Pokud žádný nemáte, můžete si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/).

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Krok 1: Vytvoření účtu služby Azure Cosmos DB

Vytvořme účet služby Azure Cosmos DB. Pokud již máte účet, který chcete použít, tuto část přeskočte. Chcete-li použít emulátor Azure Cosmos DB, nastavte emulátor podle kroků v [emulátoru Azure Cosmos DB.](local-emulator.md) Potom přeskočíte na [krok 2: Nastavte projekt sady Visual Studio](#SetupVS).

[!INCLUDE [create-dbaccount-preview](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="step-2-set-up-your-visual-studio-project"></a><a id="SetupVS"></a>Krok 2: Nastavení projektu sady Visual Studio

1. Otevřete Visual Studio a vyberte **Vytvořit nový projekt**.
1. V **okně Vytvořit nový projekt**zvolte Console App **(.NET Framework)** pro C#a pak vyberte **Další**.
1. Pojmenujte projekt *CosmosGettingStartedTutorial*a pak vyberte **vytvořit**.

    ![Konfigurace projektu](./media/sql-api-get-started/configure-cosmos-getting-started-2019.png)

1. V **Průzkumníku řešení**klepněte pravým tlačítkem myši na novou konzolovou aplikaci, která je pod řešením sady Visual Studio, a vyberte **příkaz Spravovat balíčky NuGet**.
1. Ve **Správci balíčků NuGet**vyberte **Procházet** a vyhledejte *Microsoft.Azure.Cosmos*. Zvolte **Microsoft.Azure.Cosmos** a vyberte **Instalovat**.

   ![Instalace sady NuGet pro sdk klienta Azure Cosmos DB](./media/sql-api-get-started/cosmos-getting-started-manage-nuget-2019.png)

   ID balíčku s klientskou knihovnou rozhraní Azure Cosmos DB SQL API je [Microsoft Azure Cosmos DB Client Library](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/).

Výborně! Teď když jsme dokončili nastavování, napišme nějaký kód. Dokončený projekt tohoto kurzu najdete [v tématu Vývoj konzolové aplikace .NET pomocí Azure Cosmos DB](https://github.com/Azure-Samples/cosmos-dotnet-getting-started).

## <a name="step-3-connect-to-an-azure-cosmos-db-account"></a><a id="Connect"></a>Krok 3: Připojení k účtu služby Azure Cosmos DB

1. Nahraďte odkazy na začátku aplikace Jazyka C# v souboru *Program.cs* těmito odkazy:

   ```csharp
   using System;
   using System.Threading.Tasks;
   using System.Configuration;
   using System.Collections.Generic;
   using System.Net;
   using Microsoft.Azure.Cosmos;
   ```

1. Přidejte tyto konstanty a `Program` proměnné do třídy.

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
   > Pokud jste obeznámeni s předchozí verzí sady .NET SDK, můžete být obeznámeni s *kolekce* termínů a *dokumentu*. Vzhledem k tomu, že Azure Cosmos DB podporuje více modelů rozhraní API, verze 3.0 sady .NET SDK používá obecný termíny *kontejner* a *položku*. *Kontejner* může být kolekce, graf nebo tabulka. *Položka* může být dokument, hrana/vrchol nebo řádek a je obsah uvnitř kontejneru. Další informace najdete [v tématu Práce s databázemi, kontejnery a položkami v Azure Cosmos DB](databases-containers-items.md).

1. Otevřete [portál Azure](https://portal.azure.com). Najděte svůj účet Azure Cosmos DB a pak vyberte **Klíče**.

   ![Získání klíčů Azure Cosmos DB z portálu Azure](./media/sql-api-get-started/cosmos-getting-started-portal-keys.png)

1. V *Program.cs* `<your endpoint URL>` nahraďte hodnotou **identifikátoru URI**. Nahraďte `<your primary key>` hodnotou **PRIMÁRNÍ KLÍČ**.

1. Pod **Hlavní** metoda přidejte novou asynchronní úlohu nazvanou **GetStartedDemoAsync** `CosmosClient`, která konkretizuje naše nové .

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

    Používáme **GetStartedDemoAsync** jako vstupní bod, který volá metody, které pracují na prostředcích Azure Cosmos DB.

1. Přidejte následující kód pro spuštění asynchronní úlohy **GetStartedDemoAsync** z **hlavní** metody. Metoda **Main** zachycuje výjimky a vypisuje je do konzoly.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=Main)]

1. Chcete-li aplikaci spustit, vyberte možnost F5.

    Konzole zobrazí zprávu: **Konec ukázky, stisknutím libovolné klávesy ukončete.** Tato zpráva potvrzuje, že vaše aplikace navázala připojení k Azure Cosmos DB. Potom můžete okno konzoly zavřít.

Blahopřejeme! Úspěšně jste se připojili k účtu Azure Cosmos DB.

## <a name="step-4-create-a-database"></a>Krok 4: Vytvoření databáze

Databáze je logický kontejner položek rozdělených napříč kontejnery. Buď `CreateDatabaseIfNotExistsAsync` nebo `CreateDatabaseAsync` metoda [CosmosClient třídy](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclient) můžete vytvořit databázi.

1. Zkopírujte a `CreateDatabaseAsync` vložte `GetStartedDemoAsync` metodu pod metodu.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=CreateDatabaseAsync&highlight=7)]

    `CreateDatabaseAsync`vytvoří novou databázi `FamilyDatabase` s ID, pokud ještě neexistuje, která má `databaseId` ID zadané z pole.

1. Zkopírujte a vložte níže uvedený kód, kde můžete vytvořit konkretizovat CosmosClient volat **CreateDatabaseAsync** metoda, kterou jste právě přidali.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);

        //ADD THIS PART TO YOUR CODE
        await this.CreateDatabaseAsync();
    }
    ```

    Vaše *Program.cs* by teď měla vypadat takto, když je vyplněn koncový bod a primární klíč.

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

1. Chcete-li aplikaci spustit, vyberte možnost F5.

   > [!NOTE]
   > Pokud se zobrazí chyba "Výjimka není k dispozici služby 503", je možné, že požadované [porty](performance-tips.md#networking) pro režim přímého připojení jsou blokovány bránou firewall. Chcete-li tento problém vyřešit, otevřete požadované porty nebo použijte připojení režimu brány, jak je znázorněno v následujícím kódu:
   ```csharp
     // Create a new instance of the Cosmos Client in Gateway mode
     this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey, new CosmosClientOptions()
            {
                ConnectionMode = ConnectionMode.Gateway
            });
   ```

Blahopřejeme! Úspěšně jste vytvořili databázi Azure Cosmos.  

## <a name="step-5-create-a-container"></a><a id="CreateColl"></a>Krok 5: Vytvoření kontejneru

> [!WARNING]
> Metoda `CreateContainerIfNotExistsAsync` vytvoří nový kontejner, který má cenové důsledky. Pro více informací navštivte naši [stránku s cenami](https://azure.microsoft.com/pricing/details/cosmos-db/).
>
>

Kontejner lze vytvořit pomocí [**createcontainerIfNotExistsAsync**](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync?view=azure-dotnet#Microsoft_Azure_Cosmos_Database_CreateContainerIfNotExistsAsync_Microsoft_Azure_Cosmos_ContainerProperties_System_Nullable_System_Int32__Microsoft_Azure_Cosmos_RequestOptions_System_Threading_CancellationToken_) nebo [**CreateContainerAsync**](/dotnet/api/microsoft.azure.cosmos.database.createcontainerasync?view=azure-dotnet#Microsoft_Azure_Cosmos_Database_CreateContainerAsync_Microsoft_Azure_Cosmos_ContainerProperties_System_Nullable_System_Int32__Microsoft_Azure_Cosmos_RequestOptions_System_Threading_CancellationToken_) metoda `CosmosDatabase` ve třídě. Kontejner se skládá z položek (Dokumenty JSON, pokud SQL API) a přidružené aplikační logiky na straně serveru v Jazyce JavaScript, například uložené procedury, uživatelem definované funkce a aktivační události.

1. Zkopírujte a `CreateContainerAsync` vložte `CreateDatabaseAsync` metodu pod metodu. `CreateContainerAsync`vytvoří nový kontejner s `FamilyContainer` ID, pokud ještě neexistuje, pomocí ID `containerId` zadané ho `LastName` z pole rozdělené podle vlastnosti.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=CreateContainerAsync&highlight=9)]

1. Zkopírujte a vložte níže uvedený kód, kde jste vytvořili konkretizovat CosmosClient volat **CreateContainer** metoda, kterou jste právě přidali.

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

1. Chcete-li aplikaci spustit, vyberte možnost F5.

Blahopřejeme! Úspěšně jste vytvořili kontejner Azure Cosmos.  

## <a name="step-6-add-items-to-the-container"></a><a id="CreateDoc"></a>Krok 6: Přidání položek do kontejneru

[**CreateItemAsync**](/dotnet/api/microsoft.azure.cosmos.container.createitemasync?view=azure-dotnet#Microsoft_Azure_Cosmos_Container_CreateItemAsync__1___0_System_Nullable_Microsoft_Azure_Cosmos_PartitionKey__Microsoft_Azure_Cosmos_ItemRequestOptions_System_Threading_CancellationToken_) metoda třídy `CosmosContainer` můžete vytvořit položku. Při použití rozhraní SQL API jsou položky promítány jako dokumenty, které jsou uživatelem definované libovolný obsah JSON. Teď můžete vložit položku do kontejneru Azure Cosmos.

Nejprve pojďme vytvořit `Family` třídu, která představuje objekty uložené v Azure Cosmos DB v této ukázce. Vytvoříme také `Parent`podtřídy , `Child` `Pet`které `Address` se `Family`používají v rámci . Položka musí mít `Id` vlastnost serializovanou jako `id` v JSON.

1. Chcete-li otevřít možnost **Přidat novou položku**, vyberte Ctrl+Shift+A . Přidejte do `Family.cs` projektu novou třídu.

    ![Snímek obrazovky s přidáním nové třídy Family.cs do projektu](./media/sql-api-get-started/cosmos-getting-started-add-family-class-2019.png)

1. Zkopírujte a `Family` `Parent`vložte třídu , , `Child` `Pet`, a `Address` do . `Family.cs`

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Family.cs)]


1. Zpět v *Program.cs* `AddItemsToContainerAsync` , přidejte metodu za metodu. `CreateContainerAsync`

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=AddItemsToContainerAsync)]


    Kód zkontroluje, zda položka se stejným ID ještě neexistuje. Vložíme dvě položky, po jedné pro *rodinu Andersena* a *rodinu Wakefieldů*.

1. Přidejte volání `AddItemsToContainerAsync` do `GetStartedDemoAsync` metody.

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

1. Chcete-li aplikaci spustit, vyberte možnost F5.

Blahopřejeme! Úspěšně jste vytvořili dvě položky Azure Cosmos.  

## <a name="step-7-query-azure-cosmos-db-resources"></a><a id="Query"></a>Krok 7: Dotazování prostředků Azure Cosmos DB

Azure Cosmos DB podporuje bohaté dotazy na dokumenty JSON uložené v jednotlivých kontejnerech. Další informace naleznete [v tématu Začínáme s dotazy SQL](sql-api-sql-query.md). Následující ukázkový kód ukazuje, jak spustit dotaz proti položkám, které jsme vložili v předchozím kroku.

1. Zkopírujte a `QueryItemsAsync` vložte `AddItemsToContainerAsync` metodu za metodu.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=QueryItemsAsync&highlight=10-11,17-18)]

1. Přidejte volání ``QueryItemsAsync`` do ``GetStartedDemoAsync`` metody.

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

1. Chcete-li aplikaci spustit, vyberte možnost F5.

Blahopřejeme! Úspěšně jste se dotazovali kontejneru Azure Cosmos.

## <a name="step-8-replace-a-json-item"></a><a id="ReplaceItem"></a>Krok 8: Nahrazení položky JSON

Teď budeme aktualizovat položku v Azure Cosmos DB. Změníme `IsRegistered` majetek `Family` jednoho `Grade` z dětí.

1. Zkopírujte a `ReplaceFamilyItemAsync` vložte `QueryItemsAsync` metodu za metodu.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=ReplaceFamilyItemAsync&highlight=15)]

1. Přidejte volání `ReplaceFamilyItemAsync` do `GetStartedDemoAsync` metody.

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

1. Chcete-li aplikaci spustit, vyberte možnost F5.

Blahopřejeme! Úspěšně jste nahradili položku Azure Cosmos.

## <a name="step-9-delete-item"></a><a id="DeleteDocument"></a>Krok 9: Odstranit položku

Teď odstraníme položku v Azure Cosmos DB.

1. Zkopírujte a `DeleteFamilyItemAsync` vložte `ReplaceFamilyItemAsync` metodu za metodu.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=DeleteFamilyItemAsync&highlight=10)]

1. Přidejte volání `DeleteFamilyItemAsync` do `GetStartedDemoAsync` metody.

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

1. Chcete-li aplikaci spustit, vyberte možnost F5.

Blahopřejeme! Úspěšně jste odstranili položku Azure Cosmos.

## <a name="step-10-delete-the-database"></a><a id="DeleteDatabase"></a>Krok 10: Odstranění databáze

Teď smažeme naši databázi. Odstranění vytvořené databáze odebere databázi a všechny podřízené prostředky. Prostředky zahrnují kontejnery, položky a všechny uložené procedury, uživatelem definované funkce a aktivační události. Také disponujeme `CosmosClient` instance.

1. Zkopírujte a `DeleteDatabaseAndCleanupAsync` vložte `DeleteFamilyItemAsync` metodu za metodu.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=DeleteDatabaseAndCleanupAsync)]

1. Přidejte volání ``DeleteDatabaseAndCleanupAsync`` do ``GetStartedDemoAsync`` metody.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=GetStartedDemoAsync&highlight=14)]

1. Chcete-li aplikaci spustit, vyberte možnost F5.

Blahopřejeme! Úspěšně jste odstranili databázi Azure Cosmos.

## <a name="step-11-run-your-c-console-application-all-together"></a><a id="Run"></a>Krok 11: Spuštění celé konzolové aplikace jazyka C#!

Vyberte F5 v sadě Visual Studio, chcete-li vytvořit a spustit aplikaci v režimu ladění.

Výstup celé aplikace byste měli vidět v okně konzoly. Výstup zobrazuje výsledky dotazů, které jsme přidali. Měl by odpovídat níže uvedenému příkladu textu.

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

Pokud jste neměli čas na dokončení kroků v tomto kurzu, nebo jen chcete stáhnout ukázky kódu, můžete si jej stáhnout.

Chcete-li `GetStarted` vytvořit řešení, potřebujete následující požadavky:

* Aktivní účet Azure. Pokud žádný nemáte, můžete si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/).
* [Účet služby Azure Cosmos DB][cosmos-db-create-account].
* Řešení [GetStarted](https://github.com/Azure-Samples/cosmos-dotnet-getting-started) dostupné na GitHubu

Chcete-li obnovit odkazy na azure cosmos DB .NET SDK v sadě Visual Studio, klepněte pravým tlačítkem myši na řešení v **Průzkumníku řešení**a potom vyberte **obnovit balíčky NuGet**. Dále v souboru *App.config* `EndPointUri` aktualizujte hodnoty a, `PrimaryKey` jak je popsáno v [kroku 3: Připojení k účtu Azure Cosmos DB](#Connect).

To je ono, postav to a jsi na cestě!

## <a name="next-steps"></a>Další kroky

* Chcete komplexnější kurz pro ASP.NET MVC? Viz [kurz: Vývoj webové aplikace ASP.NET Core MVC s Azure Cosmos DB pomocí .NET SDK](sql-api-dotnet-application.md).
* Chcete provést škálování a testování výkonu pomocí Azure Cosmos DB? Viz [Testování výkonu a škálování pomocí Azure Cosmos DB](performance-testing.md).
* Informace o tom, jak monitorovat požadavky, využití a úložiště služby Azure Cosmos DB, najdete v tématu [Monitorování metrik výkonu a úložiště v Azure Cosmos DB](monitor-accounts.md).
* Chcete-li spustit dotazy proti naší ukázkové datové sady, naleznete [na hřišti dotazu](https://www.documentdb.com/sql/demo).
* Další informace o Cosmos Azure DB najdete v tématu [Vítá vás Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction).

[cosmos-db-create-account]: create-sql-api-java.md#create-a-database-account
