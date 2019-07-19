---
title: Vytvoření konzolové aplikace .NET pro správu Azure Cosmos DB prostředků rozhraní SQL API
description: Naučte se vytvářet konzolovou aplikaci .NET pro správu Azure Cosmos DB prostředků účtů rozhraní SQL API.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/12/2019
ms.openlocfilehash: ad66e2b2cab9d5e70e8c403781514ba9c5434e10
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2019
ms.locfileid: "68309460"
---
# <a name="quickstart-build-a-net-console-app-to-manage-azure-cosmos-db-sql-api-resources"></a>Rychlý start: Vytvoření konzolové aplikace .NET pro správu Azure Cosmos DB prostředků rozhraní SQL API

> [!div class="op_single_selector"]
> * [.NET](create-sql-api-dotnet.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)
>  

Začínáme s klientskou knihovnou Azure Cosmos DB SQL API pro .NET Použijte postup v tomto dokumentu k instalaci balíčku .NET, sestavení aplikace a vyzkoušení ukázkového kódu pro základní operace CRUD na datech uložených v Azure Cosmos DB. 

Azure Cosmos DB je globálně distribuovaná databázová služba Microsoftu pro více modelů. Azure Cosmos DB můžete použít k rychlému vytvoření a dotazování databáze klíčů/hodnot, dokumentů a grafů. Použijte klientskou knihovnu Azure Cosmos DB SQL API pro .NET pro:

* Vytvoření databáze Azure Cosmos a kontejneru
* Přidání ukázkových dat do kontejneru
* Vytváření dotazů na data 
* Odstranění databáze

[Dokumentace k referenční dokumentaci](/dotnet/api/microsoft.azure.cosmos?view=azure-dotnet) | rozhraní API balíček[zdrojového kódu](https://github.com/Azure/azure-cosmos-dotnet-v3) | knihovny[(NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Cosmos)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) nebo si můžete [vyzkoušet Azure Cosmos DB zdarma](https://azure.microsoft.com/try/cosmosdb/) bez předplatného Azure, zdarma a závazků. 
* [.NET Core 2,1 SDK nebo novější](https://dotnet.microsoft.com/download/dotnet-core/2.1).
* [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="setting-up"></a>Nastavení

V této části se seznámíte s vytvořením účtu Azure Cosmos a nastavením projektu, který používá Azure Cosmos DB klientské knihovně rozhraní SQL API pro .NET ke správě prostředků. Vzorový kód popsaný v tomto článku vytvoří `FamilyDatabase` databázi a rodinné příslušníky (každý rodinný člen je položka) v této databázi. Každý rodinný člen má vlastnosti, jako `Id, FamilyName, FirstName, LastName, Parents, Children, Address,`je například. `LastName` Vlastnost se používá jako klíč oddílu pro kontejner. 

### <a id="create-account"></a>Vytvoření účtu Azure Cosmos

Následující kód vytvoří účet Azure Cosmos s konzistencí relací. Účet je replikován v `South Central US` a. `North Central US` Vyberte tlačítko **vyzkoušet** a vložte kód pro spuštění ve službě Azure Cloud Shell. 

```azurecli-interactive

# Set variables for the new SQL API account, database, and container
resourceGroupName='myResourceGroup'
location='southcentralus'
accountName='mysqlapicosmosdb' 
databaseName='FamilyDatabase'
containerName='FamilyContainer'

# Create a resource group
az group create \
    --name $resourceGroupName \
    --location $location

# Create a SQL API Cosmos DB account with session consistency and multi-master enabled
az cosmosdb create \
    --resource-group $resourceGroupName \
    --name $accountName \
    --kind GlobalDocumentDB \
    --locations regionName="South Central US" failoverPriority=0 --locations regionName="North Central US" failoverPriority=1 \
    --default-consistency-level "Session" \
    --enable-multiple-write-locations true

```

### <a id="create-dotnet-core-app"></a>Vytvoření nové aplikace .NET

Vytvořte novou aplikaci .NET v upřednostňovaném editoru nebo integrovaném vývojovém prostředí (IDE). V okně konzoly spusťte následující příkaz dotnet New a vytvořte novou aplikaci s názvem `todo`.

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

### <a id="install-package"></a>Instalace balíčku Azure Cosmos DB

Stále v adresáři aplikace nainstalujte Azure Cosmos DB klientskou knihovnu pro .NET Core pomocí příkazu dotnet Add Package.

```console
dotnet add package Microsoft.Azure.Cosmos
```

### <a name="copy-your-azure-cosmos-account-credentials-from-the-azure-portal"></a>Zkopírování přihlašovacích údajů účtu Azure Cosmos z Azure Portal

Ukázková aplikace se musí ověřit pro váš účet Azure Cosmos. K ověřování byste měli do aplikace předat přihlašovací údaje účtu Azure Cosmos. Pomocí následujících kroků Získejte přihlašovací údaje k účtu Azure Cosmos:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. Přejděte k účtu Azure Cosmos. 

1. Otevřete podokno **klíče** a zkopírujte **identifikátor URI** a **primární klíč** svého účtu. V dalším kroku přidáte hodnoty identifikátoru URI a Keys do proměnné prostředí.

### <a name="set-the-environment-variables"></a>Nastavení proměnných prostředí

Po zkopírování identifikátoru **URI** a **primárního klíče** účtu ho uložte do nové proměnné prostředí v místním počítači, na kterém je spuštěná aplikace. Chcete-li nastavit proměnnou prostředí, otevřete okno konzoly a spusťte následující příkaz. Nezapomeňte nahradit `<Your_Azure_Cosmos_account_URI>` hodnoty a `<Your_Azure_Cosmos_account_PRIMARY_KEY>` .

```console
setx EndpointUrl <Your_Azure_Cosmos_account_URI>
setx PrimaryKey <Your_Azure_Cosmos_account_PRIMARY_KEY>
```

 ## <a id="object-model"></a>Objektový model

Než začnete sestavovat aplikaci, Podívejme se na hierarchii prostředků v Azure Cosmos DB a objektový model použitý k vytvoření a přístup k těmto prostředkům. Azure Cosmos DB vytvoří prostředky v následujícím pořadí:

* Účet Azure Cosmos 
* Databáze 
* Containers 
* Položky

Další informace o hierarchii různých entit najdete v tématu [práce s databázemi, kontejnery a položkami v Azure Cosmos DB](databases-containers-items.md) článku. K interakci s těmito prostředky použijete následující třídy .NET:

* [CosmosClient](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.cosmos.cosmosclient?view=azure-dotnet) – Tato třída poskytuje logickou reprezentaci na straně klienta pro službu Azure Cosmos DB. Objekt klienta se používá ke konfiguraci a provádění požadavků na službu.

* [CreateDatabaseIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.cosmosclient.createdatabaseifnotexistsasync?view=azure-dotnet) – Tato metoda vytvoří (Pokud neexistuje) nebo získá (Pokud již existuje) databázový prostředek jako asynchronní operaci. 

* [CreateContainerIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync?view=azure-dotnet)– Tato metoda vytvoří (Pokud neexistuje) nebo získá (Pokud již existuje) kontejner jako asynchronní operace. Můžete zjistit stavový kód z odpovědi, abyste zjistili, zda byl kontejner nově vytvořen (201) nebo byl vrácen existující kontejner (200). 
* [CreateItemAsync](/dotnet/api/microsoft.azure.cosmos.container.createitemasync?view=azure-dotnet) – Tato metoda vytvoří položku v rámci kontejneru. 

* [GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.GetItemQueryIterator?view=azure-dotnet
) – Tato metoda vytvoří dotaz na položky v rámci kontejneru v databázi Azure Cosmos pomocí příkazu jazyka SQL s parametrizovanými hodnotami. 

* [DeleteAsync](/dotnet/api/microsoft.azure.cosmos.database.deleteasync?view=azure-dotnet) – odstraní zadanou databázi ze svého účtu Azure Cosmos. `DeleteAsync`Metoda odstraní pouze databázi. K disDeleteDatabaseAndCleanupAsync `Cosmosclient` instance by se mělo provádět samostatně (to dělá v metodě). 

 ## <a id="code-examples"></a>Příklady kódu

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

`program.cs file`Do přidejte kód pro čtení proměnných prostředí, které jste nastavili v předchozím kroku. Definujte objekty, `Database`a. `CosmosClient` `Container` Dále přidejte kód do metody Main, která volá `GetStartedDemoAsync` metodu, ve které budete spravovat prostředky účtu Azure Cosmos. 

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

`CreateDatabaseAsync` Definujte metodu `program.cs` v rámci třídy. Tato metoda vytvoří, `FamilyDatabase` Pokud ještě neexistuje. 

```csharp
private async Task CreateDatabaseAsync()
{
    // Create a new database
    this.database = await this.cosmosClient.CreateDatabaseIfNotExistsAsync(databaseId);
    Console.WriteLine("Created Database: {0}\n", this.database.Id);
}
```

### <a name="create-a-container"></a>Vytvoření kontejneru

`CreateContainerAsync` Definujte metodu `program.cs` v rámci třídy. Tato metoda vytvoří, `FamilyContainer` Pokud ještě neexistuje. 

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

### <a name="create-an-item"></a>Vytvořit položku

Vytvořte položku rodiny přidáním `AddItemsToContainerAsync` metody s následujícím kódem:

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
    // Read the item to see if it exists. ReadItemAsync will throw an exception if the item does not exist and return status code 404 (Not found).
    ItemResponse<Family> andersenFamilyResponse = await this.container.ReadItemAsync<Family>(andersenFamily.Id, new PartitionKey(andersenFamily.LastName));
    Console.WriteLine("Item in database with id: {0} already exists\n", andersenFamilyResponse.Resource.Id);
}
catch(CosmosException ex) when (ex.StatusCode == HttpStatusCode.NotFound)
{
    // Create an item in the container representing the Andersen family. Note we provide the value of the partition key for this item, which is "Andersen"
    ItemResponse<Family> andersenFamilyResponse = await this.container.CreateItemAsync<Family>(andersenFamily, new PartitionKey(andersenFamily.LastName));

    // Note that after creating the item, we can access the body of the item with the Resource property off the ItemResponse. We can also access the RequestCharge property to see the amount of RUs consumed on this request.
    Console.WriteLine("Created item in database with id: {0} Operation consumed {1} RUs.\n", andersenFamilyResponse.Resource.Id, andersenFamilyResponse.RequestCharge);
}

```

### <a name="query-the-items"></a>Dotazování na položky

Po vložení položky můžete spustit dotaz a získat podrobnosti o řadě "Andersen". Následující kód ukazuje, jak spustit dotaz přímo pomocí dotazu SQL. Dotaz SQL pro získání podrobností o řadě "Anderson" je: `SELECT * FROM c WHERE c.LastName = 'Andersen'`. `QueryItemsAsync` Definujte metodu `program.cs` v rámci třídy a přidejte do ní následující kód:


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

Po definování všech požadovaných metod je proveďte v `GetStartedDemoAsync` metodě. `DeleteDatabaseAndCleanupAsync` Metoda odhlásila do tohoto kódu, protože při spuštění této metody se nezobrazí žádné prostředky. Po ověření, že se v Azure Portal vytvořily prostředky Azure Cosmos DB, je můžete odkomentovat. 

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
    await this.DeleteFamilyItemAsync();
    //await this.DeleteDatabaseAndCleanupAsync();
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
az group delete -g "myResourceGroup" -l "southcentralus"
```

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste se seznámili s postupem vytvoření účtu Azure Cosmos, vytvoření databáze a kontejneru pomocí aplikace .NET Core. Teď můžete do svého účtu Azure Cosmos importovat další data pomocí pokynů v následujícím článku. 

> [!div class="nextstepaction"]
> [Importování dat do služby Azure Cosmos DB](import-data.md)
