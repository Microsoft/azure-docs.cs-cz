---
title: Vytvoření konzolové aplikace .NET pro správu dat v účtu rozhraní SQL API služby Azure Cosmos DB
description: Zjistěte, jak vytvořit prostředky rozhraní SQL API služby Azure Cosmos DB pomocí C# konzolové aplikace.
author: kirankumarkolli
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 07/09/2019
ms.author: kirankk
ms.openlocfilehash: 6fd7efe38aeb1f1094d240cf1675d432f3766229
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "67985729"
---
# <a name="build-a-net-console-app-to-manage-data-in-azure-cosmos-db-sql-api-account"></a>Vytvoření konzolové aplikace .NET pro správu dat v účtu rozhraní SQL API služby Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
>

Vítá vás Azure Cosmos DB SQL API get kurzu Začínáme. Až projdete tímto kurzem, budete mít konzolovou aplikaci, která vytváří prostředky Azure Cosmos DB a dotazuje se na ně. Tento kurz používá [verze 3.0 a](https://www.nuget.org/packages/Microsoft.Azure.Cosmos) sady Azure Cosmos DB .NET SDK, které mohou být cílené na [rozhraní .NET Framework](https://dotnet.microsoft.com/download) nebo [.NET Core](https://dotnet.microsoft.com/download).

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
1. V **nový projekt** dialogového okna, vyberte **Visual C#**   /  **Konzolová aplikace (.NET Framework)** , pojmenujte svůj projekt a potom klikněte na tlačítko **OK** .
    ![Snímek obrazovky okna Nový projekt](./media/sql-api-get-started/dotnet-tutorial-visual-studio-new-project.png)

    > [!NOTE]
    > Pro .NET core v cíl, **nový projekt** dialogového okna, vyberte **Visual C#**   /  **Konzolová aplikace (.NET Core)** , pojmenujte svůj projekt a potom klikněte na tlačítko  **Ok**

1. V **Průzkumníku řešení** klikněte pravým tlačítkem na novou konzolovou aplikaci v rámci řešení sady Visual Studio a pak klikněte na **Spravovat balíčky NuGet**.

    ![Snímek obrazovky místní nabídky projektu](./media/sql-api-get-started/dotnet-tutorial-visual-studio-manage-nuget.png)
1. V **NuGet** klikněte na tlačítko **Procházet**a typ **Microsoft.Azure.Cosmos** do vyhledávacího pole.
1. Najděte ve výsledcích **Microsoft.Azure.Cosmos** a klikněte na tlačítko **nainstalovat**.
   ID balíčku s klientskou knihovnou rozhraní Azure Cosmos DB SQL API je [Microsoft Azure Cosmos DB Client Library](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/).
   ![Snímek obrazovky nabídky NuGet pro vyhledání Azure Cosmos DB Client SDK](./media/sql-api-get-started/dotnet-tutorial-visual-studio-manage-nuget-2.png)

    Pokud se vám zobrazí zpráva týkající se kontroly změn řešení, klikněte na **OK**. Pokud se vám zobrazí zpráva týkající se přijetí licence, klikněte na **Souhlasím**.

Výborně! Teď když jsme dokončili nastavování, napišme nějaký kód. Úplný projekt s kódem pro tento kurz najdete na [GitHubu](https://github.com/Azure-Samples/cosmos-dotnet-getting-started).

## <a id="Connect"></a>Krok 3: Připojte se k účtu služby Azure Cosmos DB
1. Nejprve nahraďte odkazy na začátku vaše C# aplikace v **Program.cs** soubor s tyto odkazy:

   ```csharp
   using System;
   using System.Threading.Tasks;
   using System.Configuration;
   using System.Collections.Generic;
   using System.Net;
   using Microsoft.Azure.Cosmos;
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
        private Database database;

        // The container we will create.
        private Container container;

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

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=Main)]

1. Vyberte **F5** ke spuštění aplikace. Výstup okna konzoly zobrazuje zprávu `End of demo, press any key to exit.` potvrzující, že bylo navázáno připojení ke službě Azure Cosmos DB. Potom můžete okno konzoly zavřít.

Blahopřejeme! Právě jste úspěšně propojili s účtem služby Azure Cosmos DB. 

## <a name="step-4-create-a-database"></a>Krok 4: Vytvoření databáze
Databázi lze vytvořit buď pomocí [ **CreateDatabaseIfNotExistsAsync** ](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclient) nebo [ **CreateDatabaseAsync** ](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclient) funkce ``CosmosClient`` třídy. Databáze je logický kontejner položek rozdělených napříč kontejnery.

1. Zkopírujte a vložte **CreateDatabaseAsync** metodu vaše **GetStartedDemoAsync** metody. **CreateDatabaseAsync** vytvoří novou databázi s ID ``FamilyDatabase`` Pokud již neexistuje, pomocí zadané z ID ``databaseId`` pole. 

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=CreateDatabaseAsync&highlight=7)]

1. Zkopírujte a vložte kód níže, kde můžete vytvořit instanci CosmosClient volat **CreateDatabaseAsync** metoda, kterou jste právě přidali.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);

        //ADD THIS PART TO YOUR CODE
        await this.CreateDatabaseAsync();
    }
    ```

    V tuto chvíli váš kód by měla vypadat jako to, se koncový bod a primární klíč vyplněna.

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

Vyberte **F5** ke spuštění aplikace.

Blahopřejeme! Úspěšně jste vytvořili databázi Azure Cosmos DB.  

## <a id="CreateColl"></a>Krok 5: Vytvoření kontejneru
> [!WARNING]
> Volání metody **CreateContainerIfNotExistsAsync** vytvoří nový kontejner, kterou se hradí. Další podrobnosti najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/cosmos-db/).
>
>

Kontejner můžete vytvořit buď pomocí [ **CreateContainerIfNotExistsAsync** ](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosdatabase) nebo [ **CreateContainerAsync** ](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosdatabase) pracovat **CosmosDatabase** třídy. Kontejner se skládá z položky (dokumenty JSON, pokud rozhraní SQL API) a přidružené logiky na straně serveru aplikace v jazyce JavaScript, například uložených procedur, uživatelsky definované funkce a aktivační události.

1. Zkopírujte a vložte **CreateContainerAsync** metodu vaše **CreateDatabaseAsync** metody. **CreateContainerAsync** vytvoří nový kontejner s ID ``FamilyContainer`` Pokud již neexistuje, s ID zadané ``containerId`` pole rozdělené podle ``LastName`` vlastnost.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=CreateContainerAsync&highlight=9)]

1. Zkopírujte a vložte kód níže, kde můžete vytvořit instanci CosmosClient volat **CreateContainer** metoda, kterou jste právě přidali.

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

   Vyberte **F5** ke spuštění aplikace.

Blahopřejeme! Úspěšně jste vytvořili kontejner Azure Cosmos DB.  

## <a id="CreateDoc"></a>Krok 6: Přidání položek do kontejneru
Položky můžete vytvořit pomocí [ **CreateItemAsync** ](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmoscontainer) funkce **CosmosContainer** třídy. Při používání rozhraní SQL API jsou položky dokumenty, které představují uživatelem definovaný (libovolný) obsah JSON. Nyní můžete vložit položku do kontejneru Azure Cosmos DB.

Nejprve vytvoříme **řady** třídu, která bude představovat objekty uložené ve službě Azure Cosmos DB v této ukázce. Kromě toho vytvoříme i podtřídy **Parent**, **Child**, **Pet** a **Address**, které se použijí v rámci **Family**. Poznámka: položka musí mít **Id** vlastnost serializovat jako **id** ve formátu JSON.

1. Vyberte **Ctrl + Shift + A** otevřít **přidat novou položku** dialogového okna. Přidejte novou třídu **Family.cs** do projektu.

    ![Snímek obrazovky přidání nové třídy Family.cs do projektu](./media/sql-api-get-started/dotnet-tutorial-visual-studio-add-family-class.png)

1. Zkopírujte a vložte **řady**, **nadřazené**, **podřízené**, **domácí mazlíček**, a **adresu** třídy do **Family.cs**.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Family.cs)]

1. Přejděte zpět do **Program.cs** a přidejte **AddItemsToContainerAsync** metody vaše **CreateContainerAsync** metoda.
Kód zkontroluje, ujistěte se, že položka se stejným ID před vytvořením ho už neexistuje. Vloží jsme dvě položky, jeden pro rodinu a rodinu Wakefieldů.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=AddItemsToContainerAsync)]

1. Přidejte volání do ``AddItemsToContainerAsync`` v ``GetStartedDemoAsync`` metody.

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

Vyberte **F5** ke spuštění aplikace.

Blahopřejeme! Úspěšně jste vytvořili dvě položky služby Azure Cosmos DB.  

## <a id="Query"></a>Krok 7: Dotazování prostředků Azure Cosmos DB
Azure Cosmos DB podporuje bohaté [dotazy](sql-api-sql-query.md) na dokumenty JSON uložené v každé z kolekcí. Následující ukázkový kód ukazuje, jak spustit dotaz pro položky, které jsme vložili v předchozím kroku.

1. Zkopírujte a vložte **QueryItemsAsync** metodu vaše **AddItemsToContainerAsync** metody.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=QueryItemsAsync&highlight=10-11,17-18)]

1. Přidejte volání do ``QueryItemsAsync`` v ``GetStartedDemoAsync`` metody.

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

Vyberte **F5** ke spuštění aplikace.

Blahopřejeme! Úspěšně jste provedli proti kontejneru Azure Cosmos DB.

## <a id="ReplaceItem"></a>Krok 8: Nahraďte položkou formátu JSON
Teď aktualizujeme položky ve službě Azure Cosmos DB.

1. Zkopírujte a vložte **ReplaceFamilyItemAsync** metodu vaše **QueryItemsAsync** metody. Poznámka: měníme ``IsRegistered`` vlastnost rodiny a ``Grade`` jedné z podřízených.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=ReplaceFamilyItemAsync&highlight=15)]

1. Přidejte volání do ``ReplaceFamilyItemAsync`` v ``GetStartedDemoAsync`` metody.

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

   Vyberte **F5** ke spuštění aplikace.

Blahopřejeme! Úspěšně jste nahradili položku Azure Cosmos DB.

## <a id="DeleteDocument"></a>Krok 9: Odstranit položku
Teď odstraníme položky ve službě Azure Cosmos DB.

1. Zkopírujte a vložte **DeleteFamilyItemAsync** metodu vaše **ReplaceFamilyItemAsync** metody.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=DeleteFamilyItemAsync&highlight=10)]

1. Přidejte volání do ``DeleteFamilyItemAsync`` v ``GetStartedDemoAsync`` metody.

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

Vyberte **F5** ke spuštění aplikace.

Blahopřejeme! Úspěšně jste odstranili položku Azure Cosmos DB.

## <a id="DeleteDatabase"></a>Krok 10: Odstranění databáze
Teď odstraníme naší databázi. Odstraněním vytvořené databáze dojde k odebrání databáze a všech jejích podřízených prostředků (kontejnery, položky a všechny uložené procedury, uživatelsky definované funkce a aktivační události). Budeme se také vyřadit **CosmosClient** instance.

1. Zkopírujte a vložte **DeleteDatabaseAndCleanupAsync** metodu vaše **DeleteFamilyItemAsync** metody.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=DeleteDatabaseAndCleanupAsync)]

1. Přidejte volání do ``DeleteDatabaseAndCleanupAsync`` v ``GetStartedDemoAsync`` metody.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=GetStartedDemoAsync&highlight=14)]

Vyberte **F5** ke spuštění aplikace.

Blahopřejeme! Úspěšně jste odstranili databázi Azure Cosmos DB.

## <a id="Run"></a>Krok 11: Spustit vaše C# všechno dohromady konzolové aplikace!
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
* [Účtu služby Azure Cosmos DB][cosmos-db-create-account].
* Řešení [GetStarted](https://github.com/Azure-Samples/cosmos-dotnet-getting-started) dostupné na GitHubu

Pokud chcete obnovit odkazy na Azure Cosmos DB .NET SDK v sadě Visual Studio, klikněte pravým tlačítkem **GetStarted** řešení v Průzkumníku řešení a pak klikněte na tlačítko **obnovit balíčky NuGet**. Dále v souboru App.config aktualizujte hodnoty EndPointUri a PrimaryKey, jak je popsáno v [připojit k účtu služby Azure Cosmos DB](#Connect).

To je vše, sestavení a máte hotovo!

## <a name="next-steps"></a>Další postup
* Chcete komplexnější kurz pro ASP.NET MVC? Zobrazit [kurz k ASP.NET MVC: Vývoj webových aplikací pomocí služby Azure Cosmos DB](sql-api-dotnet-application-preview.md).
* Chcete testovat škálování a výkon pomocí služby Azure Cosmos DB? Viz [Testování výkonu a škálování pomocí služby Azure Cosmos DB](performance-testing.md).
* Přečtěte si, jak [sledovat požadavky, využití a úložiště Azure Cosmos DB](monitor-accounts.md).
* Spouštějte dotazy proti ukázkovým datovým sadám v [Query Playground](https://www.documentdb.com/sql/demo).
* Další informace o Cosmos Azure DB najdete v tématu [Vítá vás Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction).

[cosmos-db-create-account]: create-sql-api-java.md#create-a-database-account
