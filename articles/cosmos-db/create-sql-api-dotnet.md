---
title: Rychlý Start – vytvoření konzolové aplikace .NET pro správu Azure Cosmos DBch prostředků rozhraní SQL API
description: Naučte se vytvářet konzolovou aplikaci .NET pro správu Azure Cosmos DB prostředků účtů rozhraní SQL API v tomto rychlém startu.
author: anfeldma-ms
ms.author: anfeldma
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/07/2021
ms.custom: devx-track-dotnet, devx-track-azurecli
ms.openlocfilehash: 21b2459294b3465ba147cb454b93ac0da0081c5d
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107480881"
---
# <a name="quickstart-build-a-net-console-app-to-manage-azure-cosmos-db-sql-api-resources"></a>Rychlý Start: Vytvoření konzolové aplikace .NET pro správu Azure Cosmos DBch prostředků rozhraní SQL API
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [ROZHRANÍ .NET V4](create-sql-api-dotnet-V4.md)
> * [Sada Java SDK v4](create-sql-api-java.md)
> * [Spring Data v3](create-sql-api-spring-data.md)
> * [Konektor Spark V3](create-sql-api-spark.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Začínáme s klientskou knihovnou Azure Cosmos DB SQL API pro .NET Použijte postup v tomto dokumentu k instalaci balíčku .NET, sestavení aplikace a vyzkoušení ukázkového kódu pro základní operace CRUD na datech uložených v Azure Cosmos DB. 

Azure Cosmos DB je™ Microsoftâ NoSQL s rychlá databáze s otevřenými rozhraními API pro jakékoli škálování. Azure Cosmos DB můžete použít k rychlému vytvoření a dotazování databáze klíčů/hodnot, dokumentů a grafů. Použijte klientskou knihovnu Azure Cosmos DB SQL API pro .NET pro:

* Vytvoření databáze Azure Cosmos a kontejneru
* Přidání ukázkových dat do kontejneru
* Vytváření dotazů na data 
* Odstranění databáze

[Referenční dokumentace k](/dotnet/api/microsoft.azure.cosmos)  |  rozhraní API [Zdrojový kód knihovny](https://github.com/Azure/azure-cosmos-dotnet-v3)  |  [Balíček (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Cosmos)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) nebo si můžete [vyzkoušet Azure Cosmos DB zdarma](https://azure.microsoft.com/try/cosmosdb/) bez předplatného Azure, zdarma a závazků. 
* [.NET Core 2,1 SDK nebo novější](https://dotnet.microsoft.com/download/dotnet-core/2.1).

## <a name="setting-up"></a>Nastavení

V této části se seznámíte s vytvořením účtu Azure Cosmos a nastavením projektu, který používá Azure Cosmos DB klientské knihovně rozhraní SQL API pro .NET ke správě prostředků. Vzorový kód popsaný v tomto článku vytvoří `FamilyDatabase` databázi a rodinné příslušníky (každý rodinný člen je položka) v této databázi. Každý rodinný člen má vlastnosti, jako je například `Id, FamilyName, FirstName, LastName, Parents, Children, Address,` . `LastName`Vlastnost se používá jako klíč oddílu pro kontejner. 

### <a name="create-an-azure-cosmos-account"></a><a id="create-account"></a>Vytvoření účtu Azure Cosmos

Pokud k vytvoření účtu Azure Cosmos použijete možnost [vyzkoušet Azure Cosmos DB for Free](https://azure.microsoft.com/try/cosmosdb/) , je nutné vytvořit účet Azure Cosmos DB typu **SQL API**. Pro vás už je vytvořený testovací účet Azure Cosmos DB. Účet není nutné vytvářet explicitně, takže můžete tuto část přeskočit a přejít k další části.

Pokud máte vlastní předplatné Azure nebo jste předplatné vytvořili zdarma, měli byste účet Azure Cosmos vytvořit explicitně. Následující kód vytvoří účet Azure Cosmos s konzistencí relací. Účet je replikován v `South Central US` a `North Central US` .  

K vytvoření účtu Azure Cosmos můžete použít Azure Cloud Shell. Azure Cloud Shell je interaktivní prostředí pro správu prostředků Azure, které je po ověření dostupné z webového prohlížeče. Umožňuje flexibilně zvolit prostředí, které nejlépe vyhovuje vašemu stylu práce – Bash nebo PowerShell. Pro tento rychlý Start vyberte režim **bash** . Azure Cloud Shell také vyžaduje účet úložiště, můžete ho po zobrazení výzvy vytvořit.

Vyberte tlačítko **vyzkoušet** vedle následujícího kódu, zvolte režim **bash** , vyberte **vytvořit účet úložiště** a přihlaste se Cloud Shell. Další kopii a vložte následující kód, který Azure Cloud Shell a spustí. Název účtu Azure Cosmos musí být globálně jedinečný, před spuštěním příkazu se ujistěte, že jste aktualizovali `mysqlapicosmosdb` hodnotu.

```azurecli-interactive

# Set variables for the new SQL API account, database, and container
resourceGroupName='myResourceGroup'
location='southcentralus'

# The Azure Cosmos account name must be globally unique, make sure to update the `mysqlapicosmosdb` value before you run the command
accountName='mysqlapicosmosdb'

# Create a resource group
az group create \
    --name $resourceGroupName \
    --location $location

# Create a SQL API Cosmos DB account with session consistency and multi-region writes enabled
az cosmosdb create \
    --resource-group $resourceGroupName \
    --name $accountName \
    --kind GlobalDocumentDB \
    --locations regionName="South Central US" failoverPriority=0 --locations regionName="North Central US" failoverPriority=1 \
    --default-consistency-level "Session" \
    --enable-multiple-write-locations true

```

Vytvoření účtu Azure Cosmos trvá chvilku, jakmile bude operace úspěšná, můžete zobrazit výstup potvrzení. Po úspěšném dokončení příkazu se přihlaste k [Azure Portal](https://portal.azure.com/) a ověřte, že účet Azure Cosmos se zadaným názvem existuje. Po vytvoření prostředku můžete okno Azure Cloud Shell zavřít. 

### <a name="create-a-new-net-app"></a><a id="create-dotnet-core-app"></a>Vytvoření nové aplikace .NET

Vytvořte novou aplikaci .NET v upřednostňovaném editoru nebo integrovaném vývojovém prostředí (IDE). Otevřete příkazový řádek systému Windows nebo okno terminálu z místního počítače. Všechny příkazy v dalších částech budete spouštět z příkazového řádku nebo terminálu.  Spusťte následující příkaz dotnet New a vytvořte novou aplikaci s názvem `todo` . Parametr--langversion – nastaví vlastnost langversion – v souboru vytvořeného projektu.

```console
dotnet new console --langVersion 7.1 -n todo
```

Změňte adresář na nově vytvořenou složku aplikace. Aplikaci můžete vytvořit pomocí:

```console
cd todo
dotnet build
```

Očekávaný výstup sestavení by měl vypadat přibližně takto:

```console
  Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\todo\todo.csproj.
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp2.2\todo.dll
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp2.2\todo.Views.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:34.17
```

### <a name="install-the-azure-cosmos-db-package"></a><a id="install-package"></a>Instalace balíčku Azure Cosmos DB

Stále v adresáři aplikace nainstalujte Azure Cosmos DB klientskou knihovnu pro .NET Core pomocí příkazu dotnet Add Package.

```console
dotnet add package Microsoft.Azure.Cosmos
```

### <a name="copy-your-azure-cosmos-account-credentials-from-the-azure-portal"></a>Zkopírování přihlašovacích údajů účtu Azure Cosmos z Azure Portal

Ukázková aplikace se musí ověřit pro váš účet Azure Cosmos. K ověřování byste měli do aplikace předat přihlašovací údaje účtu Azure Cosmos. Pomocí následujících kroků Získejte přihlašovací údaje k účtu Azure Cosmos:

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).

1. Přejděte k účtu Azure Cosmos.

1. Otevřete podokno **klíče** a zkopírujte **identifikátor URI** a **primární klíč** svého účtu. V dalším kroku přidáte hodnoty identifikátoru URI a Keys do proměnné prostředí.

### <a name="set-the-environment-variables"></a>Nastavení proměnných prostředí

Po zkopírování **identifikátoru URI** a **primárního klíče** účtu ho uložte do nové proměnné prostředí v místním počítači, na kterém je spuštěná aplikace. Chcete-li nastavit proměnnou prostředí, otevřete okno konzoly a spusťte následující příkaz. Nezapomeňte nahradit `<Your_Azure_Cosmos_account_URI>` `<Your_Azure_Cosmos_account_PRIMARY_KEY>` hodnoty a.

**Windows**

```console
setx EndpointUrl "<Your_Azure_Cosmos_account_URI>"
setx PrimaryKey "<Your_Azure_Cosmos_account_PRIMARY_KEY>"
```

**Linux**

```bash
export EndpointUrl = "<Your_Azure_Cosmos_account_URI>"
export PrimaryKey = "<Your_Azure_Cosmos_account_PRIMARY_KEY>"
```

**macOS**

```bash
export EndpointUrl = "<Your_Azure_Cosmos_account_URI>"
export PrimaryKey = "<Your_Azure_Cosmos_account_PRIMARY_KEY>"
```

 ## <a name="object-model"></a><a id="object-model"></a>Objektový model

Než začnete sestavovat aplikaci, Podívejme se na hierarchii prostředků v Azure Cosmos DB a objektový model použitý k vytvoření a přístup k těmto prostředkům. Azure Cosmos DB vytvoří prostředky v následujícím pořadí:

* Účet Azure Cosmos 
* Databáze 
* Kontejnery 
* Položky

Další informace o hierarchii různých entit najdete v tématu [práce s databázemi, kontejnery a položkami v Azure Cosmos DB](account-databases-containers-items.md) článku. K interakci s těmito prostředky použijete následující třídy .NET:

* [CosmosClient](/dotnet/api/microsoft.azure.cosmos.cosmosclient) – Tato třída poskytuje logickou reprezentaci na straně klienta pro službu Azure Cosmos DB. Objekt klienta se používá ke konfiguraci a provádění požadavků na službu.

* [CreateDatabaseIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.cosmosclient.createdatabaseifnotexistsasync) – Tato metoda vytvoří (Pokud neexistuje) nebo získá (Pokud již existuje) databázový prostředek jako asynchronní operaci. 

* [CreateContainerIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync)– Tato metoda vytvoří (Pokud neexistuje) nebo získá (Pokud již existuje) kontejner jako asynchronní operace. Můžete zjistit stavový kód z odpovědi, abyste zjistili, zda byl kontejner nově vytvořen (201) nebo byl vrácen existující kontejner (200). 
* [CreateItemAsync](/dotnet/api/microsoft.azure.cosmos.container.createitemasync) – Tato metoda vytvoří položku v rámci kontejneru. 

* [UpsertItemAsync](/dotnet/api/microsoft.azure.cosmos.container.upsertitemasync) – Tato metoda vytvoří položku v rámci kontejneru, pokud ještě neexistuje, nebo ji nahradí, pokud již existuje. 

* [GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.GetItemQueryIterator) – Tato metoda vytvoří dotaz na položky v rámci kontejneru v databázi Azure Cosmos pomocí příkazu jazyka SQL s parametrizovanými hodnotami. 

* [DeleteAsync](/dotnet/api/microsoft.azure.cosmos.database.deleteasync) – odstraní zadanou databázi ze svého účtu Azure Cosmos. `DeleteAsync` Metoda odstraní pouze databázi. K `Cosmosclient` disDeleteDatabaseAndCleanupAsync instance by se mělo provádět samostatně (to dělá v metodě). 

 ## <a name="code-examples"></a><a id="code-examples"></a>Příklady kódu

Vzorový kód popsaný v tomto článku vytvoří v Azure Cosmos DB databázi rodiny. Databáze rodiny obsahuje podrobnosti o rodině, jako je jméno, adresa, umístění, přidružené nadřazené položky, děti a domácí. Před naplněním dat do účtu Azure Cosmos definujte vlastnosti rodiny položek. Vytvořte novou třídu s názvem `Family.cs` na kořenové úrovni ukázkové aplikace a přidejte do ní následující kód:

```csharp
using Newtonsoft.Json;

namespace todo
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
        // The ToString() method is used to format the output, it's used for demo purpose only. It's not required by Azure Cosmos DB
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

### <a name="add-the-using-directives--define-the-client-object"></a>Přidání direktivy using & definování objektu klienta

V adresáři projektu otevřete `Program.cs` soubor v editoru a v horní části aplikace přidejte následující direktivy using:

```csharp

using System;
using System.Threading.Tasks;
using System.Configuration;
using System.Collections.Generic;
using System.Net;
using Microsoft.Azure.Cosmos;
```

Do souboru **program. cs** přidejte kód pro čtení proměnných prostředí, které jste nastavili v předchozím kroku. Definujte `CosmosClient` objekty, `Database` a `Container` . Dále přidejte kód do metody Main, která volá `GetStartedDemoAsync` metodu, ve které budete spravovat prostředky účtu Azure Cosmos. 

```csharp
namespace todo
{
public class Program
{

    /// The Azure Cosmos DB endpoint for running this GetStarted sample.
    private string EndpointUrl = Environment.GetEnvironmentVariable("EndpointUrl");

    /// The primary key for the Azure DocumentDB account.
    private string PrimaryKey = Environment.GetEnvironmentVariable("PrimaryKey");

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
           Console.WriteLine("Beginning operations...\n");
           Program p = new Program();
           await p.GetStartedDemoAsync();

        }
        catch (CosmosException de)
        {
           Exception baseException = de.GetBaseException();
           Console.WriteLine("{0} error occurred: {1}", de.StatusCode, de);
        }
        catch (Exception e)
        {
            Console.WriteLine("Error: {0}", e);
        }
        finally
        {
            Console.WriteLine("End of demo, press any key to exit.");
            Console.ReadKey();
        }
    }
}
}
```

### <a name="create-a-database"></a>Vytvoření databáze 

Definujte `CreateDatabaseAsync` metodu v rámci `program.cs` třídy. Tato metoda vytvoří, `FamilyDatabase` Pokud ještě neexistuje.

```csharp
private async Task CreateDatabaseAsync()
{
    // Create a new database
    this.database = await this.cosmosClient.CreateDatabaseIfNotExistsAsync(databaseId);
    Console.WriteLine("Created Database: {0}\n", this.database.Id);
}
```

### <a name="create-a-container"></a>Vytvoření kontejneru

Definujte `CreateContainerAsync` metodu v rámci `program.cs` třídy. Tato metoda vytvoří, `FamilyContainer` Pokud ještě neexistuje. 

```csharp
/// Create the container if it does not exist. 
/// Specifiy "/LastName" as the partition key since we're storing family information, to ensure good distribution of requests and storage.
private async Task CreateContainerAsync()
{
    // Create a new container
    this.container = await this.database.CreateContainerIfNotExistsAsync(containerId, "/LastName");
    Console.WriteLine("Created Container: {0}\n", this.container.Id);
}
```

### <a name="create-an-item"></a>Vytvoření položky

Vytvořte položku rodiny přidáním `AddItemsToContainerAsync` metody s následujícím kódem. Pomocí `CreateItemAsync` metod nebo můžete `UpsertItemAsync` vytvořit položku:

```csharp
private async Task AddItemsToContainerAsync()
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
        IsRegistered = false
    };

    try
    {
        // Create an item in the container representing the Andersen family. Note we provide the value of the partition key for this item, which is "Andersen".
        ItemResponse<Family> andersenFamilyResponse = await this.container.CreateItemAsync<Family>(andersenFamily, new PartitionKey(andersenFamily.LastName));
        // Note that after creating the item, we can access the body of the item with the Resource property of the ItemResponse. We can also access the RequestCharge property to see the amount of RUs consumed on this request.
        Console.WriteLine("Created item in database with id: {0} Operation consumed {1} RUs.\n", andersenFamilyResponse.Resource.Id, andersenFamilyResponse.RequestCharge);
    }
    catch (CosmosException ex) when (ex.StatusCode == HttpStatusCode.Conflict)
    {
        Console.WriteLine("Item in database with id: {0} already exists\n", andersenFamily.Id);                
    }
}

```

### <a name="query-the-items"></a>Dotazování na položky

Po vložení položky můžete spustit dotaz a získat podrobnosti o řadě "Andersen". Následující kód ukazuje, jak spustit dotaz přímo pomocí dotazu SQL. Dotaz SQL pro získání podrobností o řadě "Anderson" je: `SELECT * FROM c WHERE c.LastName = 'Andersen'` . Definujte `QueryItemsAsync` metodu v rámci `program.cs` třídy a přidejte do ní následující kód:


```csharp
private async Task QueryItemsAsync()
{
    var sqlQueryText = "SELECT * FROM c WHERE c.LastName = 'Andersen'";

    Console.WriteLine("Running query: {0}\n", sqlQueryText);

    QueryDefinition queryDefinition = new QueryDefinition(sqlQueryText);
    FeedIterator<Family> queryResultSetIterator = this.container.GetItemQueryIterator<Family>(queryDefinition);

    List<Family> families = new List<Family>();

    while (queryResultSetIterator.HasMoreResults)
    {
        FeedResponse<Family> currentResultSet = await queryResultSetIterator.ReadNextAsync();
        foreach (Family family in currentResultSet)
        {
            families.Add(family);
            Console.WriteLine("\tRead {0}\n", family);
        }
    }
}

```

### <a name="delete-the-database"></a>Odstranění databáze 

Nakonec můžete odstranit databázi přidáním `DeleteDatabaseAndCleanupAsync` metody s následujícím kódem:

```csharp
private async Task DeleteDatabaseAndCleanupAsync()
{
   DatabaseResponse databaseResourceResponse = await this.database.DeleteAsync();
   // Also valid: await this.cosmosClient.Databases["FamilyDatabase"].DeleteAsync();

   Console.WriteLine("Deleted Database: {0}\n", this.databaseId);

   //Dispose of CosmosClient
    this.cosmosClient.Dispose();
}
```

### <a name="execute-the-crud-operations"></a>Provedení operací CRUD

Po definování všech požadovaných metod je proveďte v `GetStartedDemoAsync` metodě. `DeleteDatabaseAndCleanupAsync`Metoda odhlásila do tohoto kódu, protože při spuštění této metody se nezobrazí žádné prostředky. Po ověření, že se v Azure Portal vytvořily prostředky Azure Cosmos DB, je můžete odkomentovat. 

```csharp
public async Task GetStartedDemoAsync()
{
    // Create a new instance of the Cosmos Client
    this.cosmosClient = new CosmosClient(EndpointUrl, PrimaryKey);
    await this.CreateDatabaseAsync();
    await this.CreateContainerAsync();
    await this.AddItemsToContainerAsync();
    await this.QueryItemsAsync();
}
```

Až přidáte všechny požadované metody, `Program.cs` soubor uložte. 

## <a name="run-the-code"></a>Spuštění kódu

Další Sestavte a spusťte aplikaci k vytvoření prostředků Azure Cosmos DB. Ujistěte se, že otevřete nové okno příkazového řádku, nepoužívejte stejnou instanci, jakou jste použili k nastavení proměnných prostředí. Protože proměnné prostředí nejsou nastaveny v aktuálním otevřeném okně. Aby se aktualizace zobrazily, budete muset otevřít nový příkazový řádek. 

```console
dotnet build
```

```console
dotnet run
```

Při spuštění aplikace se vygeneruje následující výstup. Můžete se také přihlásit k Azure Portal a ověřit, zda jsou prostředky vytvořeny:

```console
Created Database: FamilyDatabase

Created Container: FamilyContainer

Created item in database with id: Andersen.1 Operation consumed 11.62 RUs.

Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'

        Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}

End of demo, press any key to exit.
```

Můžete ověřit, že se data vytvoří, když se přihlásíte k Azure Portal a zobrazíte požadované položky v účtu Azure Cosmos. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete k odebrání účtu Azure Cosmos a odpovídající skupiny prostředků použít Azure CLI nebo Azure PowerShell. Následující příkaz ukazuje, jak odstranit skupinu prostředků pomocí Azure CLI:

```azurecli
az group delete -g "myResourceGroup"
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se seznámili s postupem vytvoření účtu Azure Cosmos, vytvoření databáze a kontejneru pomocí aplikace .NET Core. Teď můžete do svého účtu Azure Cosmos importovat další data s pokyny v následujícím článku. 

> [!div class="nextstepaction"]
> [Importování dat do služby Azure Cosmos DB](import-data.md)
