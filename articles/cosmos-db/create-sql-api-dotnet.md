---
title: Úvodní příručka – vytvoření aplikace konzoly .NET pro správu prostředků sql api Azure Cosmos DB
description: Naučte se, jak vytvořit aplikaci konzoly .NET pro správu prostředků účtu Azure Cosmos DB SQL API v tomto rychlém startu.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/12/2019
ms.openlocfilehash: 0981ed30c6bcd9d4246ce1eb047aa66168e3884a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240412"
---
# <a name="quickstart-build-a-net-console-app-to-manage-azure-cosmos-db-sql-api-resources"></a>Úvodní příručka: Vytvoření aplikace konzoly .NET pro správu prostředků SQL API Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Začínáme s klientskou knihovnou Rozhraní SQL API Azure Cosmos DB pro rozhraní .NET. Podle pokynů v tomto dokumentu nainstalujte balíček .NET, vytvořte aplikaci a vyzkoušejte ukázkový kód pro základní operace CRUD na datech uložených v Azure Cosmos DB. 

Databáze Azure Cosmos je databázová služba Microsoftu s více modely použitelná v celosvětovém měřítku. Azure Cosmos DB můžete použít k rychlému vytvoření a dotazování na klíčové nebo hodnotové, dokumentové a grafové databáze. Pomocí klientské knihovny rozhraní SQL API Azure Cosmos DB pro rozhraní .NET:

* Vytvoření databáze Azure Cosmos a kontejneru
* Přidání ukázkových dat do kontejneru
* Vytváření dotazů na data 
* Odstranění databáze

[Referenční dokumentace](/dotnet/api/microsoft.azure.cosmos?view=azure-dotnet) | rozhraní[API Balíček zdrojového kódu](https://github.com/Azure/azure-cosmos-dotnet-v3) | [knihovny (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Cosmos)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [vytvořte si jedno zdarma](https://azure.microsoft.com/free/) nebo můžete [bezplatně vyzkoušet Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) bez předplatného Azure, zdarma a závazků. 
* Sada [.NET Core 2.1 SDK nebo novější](https://dotnet.microsoft.com/download/dotnet-core/2.1).

## <a name="setting-up"></a>Nastavení

Tato část vás provede vytvořením účtu Azure Cosmos a nastavením projektu, který používá klientskou knihovnu Azure Cosmos DB SQL API pro .NET ke správě prostředků. Ukázkový kód popsaný v tomto `FamilyDatabase` článku vytvoří databázi a členy rodiny (každý člen rodiny je položka) v rámci této databáze. Každý člen rodiny `Id, FamilyName, FirstName, LastName, Parents, Children, Address,`má vlastnosti, například . Vlastnost `LastName` se používá jako klíč oddílu pro kontejner. 

### <a name="create-an-azure-cosmos-account"></a><a id="create-account"></a>Vytvoření účtu Azure Cosmos

Pokud k vytvoření účtu Azure Cosmos použijete bezplatnou možnost [Try Azure Cosmos DB,](https://azure.microsoft.com/try/cosmosdb/) musíte vytvořit účet Azure Cosmos DB typu **SQL API**. Testovací účet Azure Cosmos DB už je vytvořen pro vás. Účet nemusíte explicitně vytvářet, takže můžete tuto část přeskočit a přejít na další část.

Pokud máte vlastní předplatné Azure nebo jste zdarma vytvořili předplatné, měli byste explicitně vytvořit účet Azure Cosmos. Následující kód vytvoří účet Azure Cosmos s konzistencí relace. Účet je replikován v `South Central US` a . `North Central US`  

Azure Cloud Shell můžete použít k vytvoření účtu Azure Cosmos. Azure Cloud Shell je interaktivní prostředí pro správu prostředků Azure, které je po ověření dostupné z webového prohlížeče. Umožňuje flexibilně zvolit prostředí, které nejlépe vyhovuje vašemu stylu práce – Bash nebo PowerShell. Pro tento rychlý start zvolte **Režim Bash.** Azure Cloud Shell také vyžaduje účet úložiště, můžete vytvořit jeden po zobrazení výzvy.

Vyberte tlačítko **Try It** vedle následujícího kódu, zvolte **Režim Bash** vyberte vytvořit **účet úložiště** a přihlásit se do Cloud Shell. Dále zkopírujte a vložte následující kód do cloudshellu Azure a spusťte jej. Název účtu Azure Cosmos musí být globálně jedinečný, `mysqlapicosmosdb` nezapomeňte aktualizovat hodnotu před spuštěním příkazu.

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

# Create a SQL API Cosmos DB account with session consistency and multi-master enabled
az cosmosdb create \
    --resource-group $resourceGroupName \
    --name $accountName \
    --kind GlobalDocumentDB \
    --locations regionName="South Central US" failoverPriority=0 --locations regionName="North Central US" failoverPriority=1 \
    --default-consistency-level "Session" \
    --enable-multiple-write-locations true

```

Vytvoření účtu Azure Cosmos chvíli trvá, jakmile je operace úspěšná, můžete zobrazit výstup potvrzení. Po úspěšném dokončení příkazu se přihlaste na [portál Azure](https://portal.azure.com/) a ověřte, zda existuje účet Azure Cosmos se zadaným názvem. Okno Azure Cloud Shell můžete po vytvoření prostředku zavřít. 

### <a name="create-a-new-net-app"></a><a id="create-dotnet-core-app"></a>Vytvoření nové aplikace .NET

Vytvořte novou aplikaci .NET v upřednostňovaném editoru nebo rozhraní IDE. Otevřete příkazový řádek systému Windows nebo okno terminálu z místního počítače. Všechny příkazy v následujících částech spustíte z příkazového řádku nebo terminálu.  Spusťte následující dotnet nový příkaz k `todo`vytvoření nové aplikace s názvem . Parametr --langVersion nastaví vlastnost LangVersion v vytvořeném souboru projektu.

```console
dotnet new console --langVersion 7.1 -n todo
```

Změňte adresář do nově vytvořené složky aplikace. Aplikaci můžete sestavit pomocí:

```console
cd todo
dotnet build
```

Očekávaný výstup z sestavení by měl vypadat nějak takto:

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

Zatímco ještě v adresáři aplikace, nainstalujte klientské knihovny Azure Cosmos DB pro .NET Core pomocí příkazu dotnet add package.

```console
dotnet add package Microsoft.Azure.Cosmos
```

### <a name="copy-your-azure-cosmos-account-credentials-from-the-azure-portal"></a>Kopírování přihlašovacích údajů k účtu Azure Cosmos z webu Azure Portal

Ukázková aplikace potřebuje k ověření vašeho účtu Azure Cosmos. K ověření, měli byste předat přihlašovací údaje účtu Azure Cosmos do aplikace. Získejte přihlašovací údaje k účtu Azure Cosmos takto:

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

1. Přejděte na svůj účet Azure Cosmos.

1. Otevřete podokno **Klíče** a zkopírujte **identifikátor URI** a **primární klíč** svého účtu. Hodnoty URI a klíče přidáte do proměnné prostředí v dalším kroku.

### <a name="set-the-environment-variables"></a>Nastavení proměnných prostředí

Po zkopírování **identifikátoru URI** a **primárního klíče** účtu je uložte do nové proměnné prostředí v místním počítači se spuštěnou aplikací. Chcete-li nastavit proměnnou prostředí, otevřete okno konzoly a spusťte následující příkaz. Ujistěte se, že nahradit `<Your_Azure_Cosmos_account_URI>` a `<Your_Azure_Cosmos_account_PRIMARY_KEY>` hodnoty.

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

**Macos**

```bash
export EndpointUrl = "<Your_Azure_Cosmos_account_URI>"
export PrimaryKey = "<Your_Azure_Cosmos_account_PRIMARY_KEY>"
```

 ## <a name="object-model"></a><a id="object-model"></a>Objektový model

Než začnete vytvářet aplikaci, podívejme se na hierarchii prostředků v Azure Cosmos DB a objektový model, který se používá k vytvoření a přístupu k těmto prostředkům. Azure Cosmos DB vytváří prostředky v následujícím pořadí:

* Účet Azure Cosmos 
* Databáze 
* Kontejnery 
* Items

Další informace o hierarchii různých entit najdete v [článku práce s databázemi, kontejnery a položkami.](databases-containers-items.md) K interakci s těmito prostředky budete používat následující třídy .NET:

* [CosmosClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclient?view=azure-dotnet) – tato třída poskytuje logické znázornění služby Azure Cosmos DB na straně klienta. Objekt klienta se používá ke konfiguraci a spuštění požadavků proti službě.

* [CreateDatabaseIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.cosmosclient.createdatabaseifnotexistsasync?view=azure-dotnet) - Tato metoda vytvoří (pokud neexistuje) nebo získá (pokud již existuje) databázový prostředek jako asynchronní operace. 

* [CreateContainerIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync?view=azure-dotnet)- - Tato metoda vytvoří (pokud neexistuje) nebo získá (pokud již existuje) kontejner jako asynchronní operace. Můžete zkontrolovat stavový kód z odpovědi k určení, zda byl kontejner nově vytvořen (201) nebo byl vrácen existující kontejner (200). 
* [CreateItemAsync](/dotnet/api/microsoft.azure.cosmos.container.createitemasync?view=azure-dotnet) - Tato metoda vytvoří položku v rámci kontejneru. 

* [UpsertItemAsync](/dotnet/api/microsoft.azure.cosmos.container.upsertitemasync?view=azure-dotnet) - Tato metoda vytvoří položku v kontejneru, pokud již neexistuje nebo nahradí položku, pokud již existuje. 

* [GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.GetItemQueryIterator?view=azure-dotnet
) - Tato metoda vytvoří dotaz na položky v rámci kontejneru v databázi Azure Cosmos pomocí příkazu SQL s parametrizovanými hodnotami. 

* [DeleteAsync](/dotnet/api/microsoft.azure.cosmos.database.deleteasync?view=azure-dotnet) – odstraní zadanou databázi z vašeho účtu Azure Cosmos. `DeleteAsync`metoda pouze odstraní databázi. Likvidace `Cosmosclient` instance by mělo dojít samostatně (což se provádí v DeleteDatabaseAndCleanupAsync metoda. 

 ## <a name="code-examples"></a><a id="code-examples"></a>Příklady kódu

Ukázkový kód popsaný v tomto článku vytvoří rodinnou databázi v Azure Cosmos DB. Rodinná databáze obsahuje rodinné údaje, jako je jméno, adresa, umístění, přidružené rodiče, děti a domácí zvířata. Před vyplněním dat do účtu Azure Cosmos definujte vlastnosti položky rodiny. Vytvořte novou `Family.cs` třídu pojmenovanou na kořenové úrovni ukázkové aplikace a přidejte do ní následující kód:

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

### <a name="add-the-using-directives--define-the-client-object"></a>Přidání direktiv using & definování objektu klienta

V adresáři projektu `Program.cs` otevřete soubor v editoru a přidejte následující pomocí direktiv v horní části aplikace:

```csharp

using System;
using System.Threading.Tasks;
using System.Configuration;
using System.Collections.Generic;
using System.Net;
using Microsoft.Azure.Cosmos;
```

Do **souboru Program.cs** přidejte kód pro čtení proměnných prostředí, které jste nastavili v předchozím kroku. Definujte `CosmosClient` `Database`, a `Container` objekty. Další přidat kód k hlavní `GetStartedDemoAsync` metodě, která volá metodu, kde spravujete prostředky účtu Azure Cosmos. 

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

Definujte `CreateDatabaseAsync` metodu `program.cs` v rámci třídy. Tato metoda `FamilyDatabase` vytvoří, pokud ještě neexistuje.

```csharp
private async Task CreateDatabaseAsync()
{
    // Create a new database
    this.database = await this.cosmosClient.CreateDatabaseIfNotExistsAsync(databaseId);
    Console.WriteLine("Created Database: {0}\n", this.database.Id);
}
```

### <a name="create-a-container"></a>Vytvoření kontejneru

Definujte `CreateContainerAsync` metodu `program.cs` v rámci třídy. Tato metoda `FamilyContainer` vytvoří, pokud ještě neexistuje. 

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

Vytvořte položku rodiny přidáním `AddItemsToContainerAsync` metody s následujícím kódem. K vytvoření `CreateItemAsync` položky můžete použít metody nebo: `UpsertItemAsync`

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

### <a name="query-the-items"></a>Dotaz na položky

Po vložení položky můžete spustit dotaz a získat podrobnosti o rodině "Andersen". Následující kód ukazuje, jak spustit dotaz pomocí dotazu SQL přímo. Dotaz SQL získat podrobnosti rodiny "Anderson" je: `SELECT * FROM c WHERE c.LastName = 'Andersen'`. Definujte `QueryItemsAsync` metodu `program.cs` v rámci třídy a přidejte do ní následující kód:


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

Nakonec můžete odstranit databázi `DeleteDatabaseAndCleanupAsync` přidání metody s následujícím kódem:

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

### <a name="execute-the-crud-operations"></a>Provádění operací CRUD

Poté, co jste definovali všechny požadované `GetStartedDemoAsync` metody, proveďte je v metodě. Metoda `DeleteDatabaseAndCleanupAsync` komentoval v tomto kódu, protože neuvidíte žádné prostředky, pokud je tato metoda spuštěna. Můžete odkomentovat po ověření, že vaše prostředky Azure Cosmos DB byly vytvořeny na webu Azure Portal. 

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

Po přidání všech požadovaných metod `Program.cs` soubor uložte. 

## <a name="run-the-code"></a>Spuštění kódu

Další sestavení a spuštění aplikace k vytvoření prostředků Azure Cosmos DB. Ujistěte se, že otevřete nové okno příkazového řádku, nepoužívejte stejnou instanci, kterou jste použili k nastavení proměnných prostředí. Protože proměnné prostředí nejsou nastaveny v aktuálním otevřeném okně. Chcete-li zobrazit aktualizace, budete muset otevřít nový příkazový řádek. 

```console
dotnet build
```

```console
dotnet run
```

Následující výstup je generován při spuštění aplikace. Můžete se taky přihlásit k portálu Azure a ověřit, že se prostředky vytvoří:

```console
Created Database: FamilyDatabase

Created Container: FamilyContainer

Created item in database with id: Andersen.1 Operation consumed 11.62 RUs.

Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'

        Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}

End of demo, press any key to exit.
```

Můžete ověřit, že data se vytvoří po přihlášení k portálu Azure a zobrazí požadované položky ve vašem účtu Azure Cosmos. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Když už nepotřebujete, můžete použít Azure CLI nebo Azure PowerShell k odebrání účtu Azure Cosmos a odpovídající skupiny prostředků. Následující příkaz ukazuje, jak odstranit skupinu prostředků pomocí azure cli:

```azurecli
az group delete -g "myResourceGroup"
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili, jak vytvořit účet Azure Cosmos, vytvořit databázi a kontejner pomocí aplikace .NET Core. Teď můžete importovat další data do svého účtu Azure Cosmos s pokyny v následujícím článku. 

> [!div class="nextstepaction"]
> [Importování dat do služby Azure Cosmos DB](import-data.md)
