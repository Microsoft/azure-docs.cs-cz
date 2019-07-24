---
title: Začínáme s Azure Cosmos DB rozhraní API pro tabulky pomocí sady .NET Standard SDK
description: Ukládejte strukturovaná data v cloudu pomocí rozhraní API pro tabulky Azure Cosmos DB.
author: wmengmsft
ms.author: wmeng
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: sample
ms.date: 05/20/2019
ms.openlocfilehash: 75f1554f7522723d71666633a03761d07e797e33
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443507"
---
# <a name="get-started-with-azure-cosmos-db-table-api-and-azure-table-storage-using-the-net-sdk"></a>Začínáme s Azure Cosmos DB rozhraní API pro tabulky a Azure Table Storage pomocí sady .NET SDK

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]

[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

Pomocí Azure Cosmos DB rozhraní API pro tabulky nebo úložiště tabulek Azure můžete ukládat strukturovaná data NoSQL do cloudu a poskytnout tak úložiště klíčů a atributů s návrhem bez schématu. Vzhledem k tomu, že Azure Cosmos DB rozhraní API pro tabulky a úložiště tabulek jsou míň schématu, je snadné přizpůsobit data, jak se vyvíjí vaše aplikace. K ukládání flexibilních datových sad, například uživatelských dat pro webové aplikace, adresářů, informací o zařízení nebo jiných typů metadat, které vaše služba vyžaduje, můžete použít Azure Cosmos DB rozhraní API pro tabulky nebo úložiště tabulek. 

V tomto kurzu se dozvíte, jak používat [Microsoft Azure Cosmos DB knihovny tabulek pro .NET](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) se službou Azure Cosmo DB rozhraní API pro tabulky a scénáři úložiště tabulek Azure. Je nutné použít připojení specifické pro službu Azure. Tyto scénáře jsou prozkoumány pomocí C# příkladů, které ilustrují vytváření tabulek, vkládání, aktualizaci dat, dotazování dat a odstraňování tabulek.

## <a name="prerequisites"></a>Požadavky

Pro úspěšné dokončení této ukázky potřebujete následující položky:

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

* [Microsoft Azure knihovna tabulek CosmosDB pro .NET](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) – Tato knihovna je aktuálně dostupná pro .NET Standard a rozhraní .NET Framework. 

* [Účet Azure Cosmos DB rozhraní API pro tabulky](create-table-dotnet.md#create-a-database-account).

## <a name="create-an-azure-cosmos-db-table-api-account"></a>Vytvoření účtu rozhraní Table API služby Azure Cosmos DB

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="create-a-net-console-project"></a>Vytvoření projektu konzoly .NET

V aplikaci Visual Studio vytvořte novou konzolovou aplikaci .NET. Následující kroky ukazují, jak vytvořit konzolovou aplikaci v aplikaci Visual Studio 2019. Můžete použít Azure Cosmos DB knihovny tabulek v jakémkoli typu aplikace .NET, včetně cloudové služby Azure nebo webové aplikace a desktopových a mobilních aplikací. V této příručce použijeme konzolovou aplikaci kvůli zjednodušení.

1. Vyberte **Soubor** > **Nový** > **Projekt**.

1. Zvolte **Konzolová aplikace (.NET Core)** a pak vyberte **Další**.

1. Do pole **název projektu** zadejte název vaší aplikace, například **CosmosTableSamples**. (V případě potřeby můžete zadat jiný název.)

1. Vyberte **Vytvořit**.

Všechny příklady kódu v této ukázce lze přidat do metody Main () souboru **program.cs** vaší konzolové aplikace.

## <a name="install-the-required-nuget-package"></a>Instalace požadovaného balíčku NuGet

K získání balíčku NuGet použijte tento postup:

1. Klikněte v **Průzkumníku řešení** pravým tlačítkem na projekt a vyberte **Spravovat balíčky NuGet**.

1. Vyhledejte v online `Microsoft.Azure.Cosmos.Table`režimu `Microsoft.Extensions.Configuration`, `Microsoft.Extensions.Configuration.Json` ,`Microsoft.Extensions.Configuration.Binder` a vyberte **nainstalovat** a nainstalujte Microsoft Azure Cosmos DB knihovnu tabulek.

## <a name="configure-your-storage-connection-string"></a>Konfigurace připojovacího řetězce úložiště

1. V [Azure Portal](https://portal.azure.com/)přejděte k účtu Azure Cosmos nebo k účtu Table Storage. 

1. Otevřete okno **připojovací řetězec** nebo **přístupové klávesy** . Pomocí tlačítka pro kopírování na pravé straně okna zkopírujte **PRIMÁRNÍ PŘIPOJOVACÍ ŘETĚZEC**.

   ![Zobrazení a zkopírování PRIMÁRNÍHO PŘIPOJOVACÍHO ŘETĚZCE v podokně Připojovací řetězec](./media/create-table-dotnet/connection-string.png)
   
1. Pokud chcete nakonfigurovat připojovací řetězec, klikněte pravým tlačítkem ze sady Visual Studio na projekt **CosmosTableSamples**.

1. Vyberte **Přidat** a poté **Nová položka**. Vytvoří nový soubor **Settings. JSON** s typem souboru jako **konfigurační soubor TypeScript JSON** . 

1. Nahraďte kód v souboru Settings. JSON následujícím kódem a přiřaďte k primárnímu připojovacímu řetězci:

   ```csharp
   {
   "StorageConnectionString": <Primary connection string of your Azure Cosmos DB account>
   }
   ```

1. Klikněte pravým tlačítkem na projekt **CosmosTableSamples**. Vyberte **Přidat**, **Nová položka** a přidejte třídu s názvem **appSettings.cs**.

1. Do souboru AppSettings.cs přidejte následující kód. Tento soubor přečte připojovací řetězec ze souboru Settings. JSON a přiřadí ho ke konfiguračnímu parametru:

   ```csharp
   namespace CosmosTableSamples
   {
    using Microsoft.Extensions.Configuration;
    public class AppSettings
    {
        public string StorageConnectionString { get; set; }
        public static AppSettings LoadAppSettings()
        {
            IConfigurationRoot configRoot = new ConfigurationBuilder()
                .AddJsonFile("Settings.json")
                .Build();
            AppSettings appSettings = configRoot.Get<AppSettings>();
            return appSettings;
        }
    }
   }
   ```

## <a name="parse-and-validate-the-connection-details"></a>Analyzovat a ověřit podrobnosti připojení 

1. Klikněte pravým tlačítkem na projekt **CosmosTableSamples**. Vyberte **Přidat**, **Nová položka** a přidejte třídu s názvem **Common.cs**. Budete psát kód, který ověří podrobnosti připojení a vytvoří tabulku v rámci této třídy.

1. Definujte metodu `CreateStorageAccountFromConnectionString` , jak je znázorněno níže. Tato metoda analyzuje podrobnosti připojovacího řetězce a ověří, zda jsou platné podrobnosti o názvu účtu a klíči účtu uvedené v souboru Settings. JSON. 

 ```csharp
using System;

namespace CosmosTableSamples
{
    using System.Threading.Tasks;
    using Microsoft.Azure.Cosmos.Table;
    using Microsoft.Azure.Documents;

    public class Common
    {
        public static CloudStorageAccount CreateStorageAccountFromConnectionString(string storageConnectionString)
        {
            CloudStorageAccount storageAccount;
            try
            {
                storageAccount = CloudStorageAccount.Parse(storageConnectionString);
            }
            catch (FormatException)
            {
                Console.WriteLine("Invalid storage account information provided. Please confirm the AccountName and AccountKey are valid in the app.config file - then restart the application.");
                throw;
            }
            catch (ArgumentException)
            {
                Console.WriteLine("Invalid storage account information provided. Please confirm the AccountName and AccountKey are valid in the app.config file - then restart the sample.");
                Console.ReadLine();
                throw;
            }

            return storageAccount;
        }
    }
}
   ```


## <a name="create-a-table"></a>Vytvoření tabulky 

Třída [CloudTableClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.cloudtableclient) vám umožňuje načíst tabulky a entity, které jsou uložené ve službě Table Storage. Vzhledem k tomu, že v účtu Cosmos DB rozhraní API pro tabulky nejsou žádné tabulky, přidejte `CreateTableAsync` metodu do třídy **Common.cs** a vytvořte tabulku:

```csharp
public static async Task<CloudTable> CreateTableAsync(string tableName)
  {
    string storageConnectionString = AppSettings.LoadAppSettings().StorageConnectionString;

    // Retrieve storage account information from connection string.
    CloudStorageAccount storageAccount = CreateStorageAccountFromConnectionString(storageConnectionString);

    // Create a table client for interacting with the table service
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient(new TableClientConfiguration());

    Console.WriteLine("Create a Table for the demo");

    // Create a table client for interacting with the table service 
    CloudTable table = tableClient.GetTableReference(tableName);
    if (await table.CreateIfNotExistsAsync())
    {
      Console.WriteLine("Created Table named: {0}", tableName);
    }
    else
    {
      Console.WriteLine("Table {0} already exists", tableName);
    }

    Console.WriteLine();
    return table;
}
```

## <a name="define-the-entity"></a>Definovat entitu 

Entity se mapují C# na objekty pomocí vlastní třídy odvozené z [TableEntity](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableentity). Když budete chtít do tabulky přidat entitu, vytvořte třídu, která definuje vlastnosti vaší entity.

Klikněte pravým tlačítkem na projekt **CosmosTableSamples**. Vyberte **Přidat**, **Nová složka** a pojmenujte ji jako **model**. Do složky modelu přidejte třídu s názvem **CustomerEntity.cs** a přidejte do ní následující kód.

```csharp
namespace CosmosTableSamples.Model
{
    using Microsoft.Azure.Cosmos.Table;
    public class CustomerEntity : TableEntity
    {
        public CustomerEntity()
        {
        }

        public CustomerEntity(string lastName, string firstName)
        {
            PartitionKey = lastName;
            RowKey = firstName;
        }

        public string Email { get; set; }
        public string PhoneNumber { get; set; }
    }
}
```

Tento kód definuje třídu entity, která používá křestní jméno zákazníka jako klíč řádku a příjmení jako klíč oddílu. V tabulce ji pak jednoznačně identifikuje kombinace klíče oddílu a řádku entity. Na entity se stejným klíčem oddílu je možné zadávat dotazy rychleji než entity s různými klíči oddílů, ale pomocí různých klíčů oddílu můžete dosáhnout větší škálovatelnosti paralelních operací. Entity, které mají být uloženy v tabulkách, musí být podporovaného typu, například odvozeného od třídy [TableEntity](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableentity) . Vlastnosti entity, které chcete uložit do tabulky, musí být veřejné vlastnosti typu a musí podporovat získávání i nastavování hodnot. Typ entity také musí vystavit konstruktor bez parametrů.

## <a name="insert-or-merge-an-entity"></a>Vložení nebo sloučení entity

Následující příklad kódu vytvoří objekt entity a přidá jej do tabulky. Metoda InsertOrMerge v rámci třídy [TableOperation](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableoperation) se používá k vložení nebo sloučení entity. K provedení operace je volána metoda [Cloud. metody ExecuteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.cloudtable.executeasync?view=azure-dotnet) . 

Klikněte pravým tlačítkem na projekt **CosmosTableSamples**. Vyberte **Přidat**, **Nová položka** a přidejte třídu s názvem **SamplesUtils.cs**. Tato třída ukládá veškerý kód potřebný k provádění operací CRUD u entit. 

```csharp
public static async Task<CustomerEntity> InsertOrMergeEntityAsync(CloudTable table, CustomerEntity entity)
    {
      if (entity == null)
    {
       throw new ArgumentNullException("entity");
    }
    try
    {
       // Create the InsertOrReplace table operation
       TableOperation insertOrMergeOperation = TableOperation.InsertOrMerge(entity);

       // Execute the operation.
       TableResult result = await table.ExecuteAsync(insertOrMergeOperation);
       CustomerEntity insertedCustomer = result.Result as CustomerEntity;
        
        // Get the request units consumed by the current operation. RequestCharge of a TableResult is only applied to Azure CosmoS DB 
        if (result.RequestCharge.HasValue)
          {
            Console.WriteLine("Request Charge of InsertOrMerge Operation: " + result.RequestCharge);
          }

        return insertedCustomer;
        }
        catch (StorageException e)
        {
          Console.WriteLine(e.Message);
          Console.ReadLine();
          throw;
        }
    }
```

### <a name="get-an-entity-from-a-partition"></a>Získat entitu z oddílu

Entitu můžete získat z oddílu pomocí metody načíst pod třídou [TableOperation](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableoperation) . Následující příklad kódu získá klíč řádku klíč oddílu, e-mail a telefonní číslo entity zákazníka. Tento příklad také vytiskne jednotky žádosti spotřebované pro dotazování na entitu. Chcete-li zadat dotaz na entitu, přidejte následující kód do souboru **SamplesUtils.cs** : 

```csharp
public static async Task<CustomerEntity> RetrieveEntityUsingPointQueryAsync(CloudTable table, string partitionKey, string rowKey)
    {
      try
      {
        TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>(partitionKey, rowKey);
        TableResult result = await table.ExecuteAsync(retrieveOperation);
        CustomerEntity customer = result.Result as CustomerEntity;
        if (customer != null)
        {
          Console.WriteLine("\t{0}\t{1}\t{2}\t{3}", customer.PartitionKey, customer.RowKey, customer.Email, customer.PhoneNumber);
        }

        // Get the request units consumed by the current operation. RequestCharge of a TableResult is only applied to Azure CosmoS DB 
        if (result.RequestCharge.HasValue)
        {
           Console.WriteLine("Request Charge of Retrieve Operation: " + result.RequestCharge);
        }

        return customer;
        }
        catch (StorageException e)
        {
           Console.WriteLine(e.Message);
           Console.ReadLine();
           throw;
        }
    }
```

## <a name="delete-an-entity"></a>Odstranění entity

Entitu můžete po jejím načtení snadno odstranit, a to pomocí stejného vzoru zobrazovaného pro aktualizaci entity. Následující kód načte a odstraní entitu zákazníka. Pokud chcete entitu odstranit, přidejte do souboru **SamplesUtils.cs** následující kód: 

```csharp
public static async Task DeleteEntityAsync(CloudTable table, CustomerEntity deleteEntity)
   {
     try
     {
        if (deleteEntity == null)
     {
        throw new ArgumentNullException("deleteEntity");
     }

    TableOperation deleteOperation = TableOperation.Delete(deleteEntity);
    TableResult result = await table.ExecuteAsync(deleteOperation);

    // Get the request units consumed by the current operation. RequestCharge of a TableResult is only applied to Azure CosmoS DB 
    if (result.RequestCharge.HasValue)
    {
       Console.WriteLine("Request Charge of Delete Operation: " + result.RequestCharge);
    }

    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

## <a name="execute-the-crud-operations-on-sample-data"></a>Provádění operací CRUD pro ukázková data

Po definování metod pro vytvoření tabulky, vložení nebo sloučení entit spusťte tyto metody pro ukázková data. Provedete to tak, že kliknete pravým tlačítkem na projekt **CosmosTableSamples**. Vyberte **Přidat**, **Nová položka** a přidejte třídu s názvem **BasicSamples.cs** a přidejte do ní následující kód. Tento kód vytvoří tabulku a přidá do ní entity. Pokud chcete odstranit entitu a tabulku na konci projektu, odstraňte komentáře z `table.DeleteIfExistsAsync()` a `SamplesUtils.DeleteEntityAsync(table, customer)` metody z následujícího kódu:

```csharp
using System;
namespace CosmosTableSamples
{
    using System.Threading.Tasks;
    using Microsoft.Azure.Cosmos.Table;
    using Model;

    class BasicSamples
    {
        public async Task RunSamples()
        {
            Console.WriteLine("Azure Cosmos DB Table - Basic Samples\n");
            Console.WriteLine();

            string tableName = "demo" + Guid.NewGuid().ToString().Substring(0, 5);

            // Create or reference an existing table
            CloudTable table = await Common.CreateTableAsync(tableName);

            try
            {
                // Demonstrate basic CRUD functionality 
                await BasicDataOperationsAsync(table);
            }
            finally
            {
                // Delete the table
                // await table.DeleteIfExistsAsync();
            }
        }

        private static async Task BasicDataOperationsAsync(CloudTable table)
        {
            // Create an instance of a customer entity. See the Model\CustomerEntity.cs for a description of the entity.
            CustomerEntity customer = new CustomerEntity("Harp", "Walter")
            {
                Email = "Walter@contoso.com",
                PhoneNumber = "425-555-0101"
            };

            // Demonstrate how to insert the entity
            Console.WriteLine("Insert an Entity.");
            customer = await SamplesUtils.InsertOrMergeEntityAsync(table, customer);

            // Demonstrate how to Update the entity by changing the phone number
            Console.WriteLine("Update an existing Entity using the InsertOrMerge Upsert Operation.");
            customer.PhoneNumber = "425-555-0105";
            await SamplesUtils.InsertOrMergeEntityAsync(table, customer);
            Console.WriteLine();

            // Demonstrate how to Read the updated entity using a point query 
            Console.WriteLine("Reading the updated Entity.");
            customer = await SamplesUtils.RetrieveEntityUsingPointQueryAsync(table, "Harp", "Walter");
            Console.WriteLine();

            // Demonstrate how to Delete an entity
            //Console.WriteLine("Delete the entity. ");
            //await SamplesUtils.DeleteEntityAsync(table, customer);
            //Console.WriteLine();
        }
    }
}
```

Předchozí kód vytvoří tabulku, která začíná "demo" a vygenerovaný identifikátor GUID se připojí k názvu tabulky. Pak přidá entitu Customer s křestním jménem a příjmení jako "Harpa Waltera" a později aktualizuje telefonní číslo tohoto uživatele. 

V tomto kurzu jste vytvořili kód, který provádí základní operace CRUD s daty uloženými v rozhraní API pro tabulkym účtu. Můžete také provádět pokročilé operace, jako je například dávkové vkládání dat, dotazování všech dat v rámci oddílu, dotazování na rozsah dat v rámci oddílu, seznam tabulek v účtu, jejichž názvy začínají zadanou předponou. Můžete si stáhnout kompletní ukázkový formulář [Azure-Cosmos-Table-dotnet-Core-Začínáme](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started) s úložištěm GitHub. Třída [AdvancedSamples.cs](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started/blob/master/CosmosTableSamples/AdvancedSamples.cs) má více operací, které můžete s daty provádět.  

## <a name="run-the-project"></a>Spuštění projektu

Z projektu **CosmosTableSamples**. Otevřete třídu s názvem **program.cs** a přidejte do ní následující kód pro volání BasicSamples při spuštění projektu.

```csharp
using System;

namespace CosmosTableSamples
{
    class Program
    {
        public static void Main(string[] args)
        {
            Console.WriteLine("Azure Cosmos Table Samples");
            BasicSamples basicSamples = new BasicSamples();
            basicSamples.RunSamples().Wait();
           
            Console.WriteLine();
            Console.WriteLine("Press any key to exit");
            Console.Read();
        }
    }
}
```

Nyní Sestavte řešení a stisknutím klávesy F5 spusťte projekt. Při spuštění projektu se zobrazí následující výstup na příkazovém řádku:

![Výstup z příkazového řádku](./media/tutorial-develop-table-standard/output-from-sample.png)

Pokud se zobrazí chyba, která říká, že soubor. JSON nelze najít při spuštění projektu, můžete ho vyřešit přidáním následující položky XML do nastavení projektu. Klikněte pravým tlačítkem na CosmosTableSamples, vyberte Upravit CosmosTableSamples. csproj a přidejte následující položku: 

```csharp
  <ItemGroup>
    <None Update="Settings.json">
      <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    </None>
  </ItemGroup>
```
Nyní se můžete přihlásit k Azure Portal a ověřit, zda data v tabulce existují. 

![Výsledky na portálu](./media/tutorial-develop-table-standard/results-in-portal.png)

## <a name="next-steps"></a>Další postup

Teď můžete přejít k dalšímu kurzu a Naučte se migrovat data na účet Azure Cosmos DB rozhraní API pro tabulky. 

> [!div class="nextstepaction"]
>[Dotazování na data](../cosmos-db/table-import.md)
