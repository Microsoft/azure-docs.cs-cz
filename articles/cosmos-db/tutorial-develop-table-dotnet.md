---
title: Rozhraní API tabulky Db Azure Cosmos pomocí sady .NET Standard SDK
description: Zjistěte, jak ukládat a dotazovat se na strukturovaná data v účtu rozhraní API tabulky Azure Cosmos DB
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: sample
ms.date: 12/03/2019
ms.openlocfilehash: f4d6e1bb0d5db0dbfc30e14abc58321bce8d0baf
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79238451"
---
# <a name="get-started-with-azure-cosmos-db-table-api-and-azure-table-storage-using-the-net-sdk"></a>Začínáme s rozhraním Azure Cosmos DB API pro tabulky a Azure Table Storage s využitím .NET SDK

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]

[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

Rozhraní Azure Cosmos DB Table API nebo Azure Table storage můžete použít k ukládání strukturovaných dat NoSQL v cloudu a poskytovat úložiště klíčů a atributů s menším návrhem schématu. Vzhledem k tomu, že rozhraní Azure Cosmos DB Table API a table storage jsou schémata méně, je snadné přizpůsobit data podle potřeb vaší aplikace vyvíjet. Rozhraní Azure Cosmos DB Table API nebo table storage můžete použít k ukládání flexibilních datových sad, jako jsou uživatelská data pro webové aplikace, adresáře, informace o zařízení nebo jiné typy metadat, které vaše služba vyžaduje. 

Tento kurz popisuje ukázku, která ukazuje, jak používat [knihovnu tabulek Microsoft Azure Cosmos DB pro .NET](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) se scénáři table api Azure Cosmos DB a scénáře úložiště Azure Table. Musíte použít připojení specifické pro službu Azure. Tyto scénáře jsou prozkoumány pomocí příkladů jazyka C#, které ilustrují, jak vytvářet tabulky, vkládat nebo aktualizovat data, data dotazů a odstraňovat tabulky.

## <a name="prerequisites"></a>Požadavky

Pro úspěšné dokončení této ukázky potřebujete následující položky:

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

* [Knihovna tabulek Microsoft Azure CosmosDB pro rozhraní .NET](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) – tato knihovna je aktuálně dostupná pro rozhraní .NET Standard a rozhraní .NET Framework. 

* [Účet rozhraní API tabulky DB Azure Cosmos](create-table-dotnet.md#create-a-database-account).

## <a name="create-an-azure-cosmos-db-table-api-account"></a>Vytvoření účtu rozhraní Table API služby Azure Cosmos DB

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="create-a-net-console-project"></a>Vytvoření projektu konzoly .NET

V sadě Visual Studio vytvořte novou aplikaci konzoly .NET. Následující kroky ukazují, jak vytvořit konzolovou aplikaci v Sadě Visual Studio 2019. Knihovnu tabulek Azure Cosmos DB můžete použít v libovolném typu aplikace .NET, včetně cloudové služby Azure nebo webové aplikace a desktopových a mobilních aplikací. V této příručce použijeme konzolovou aplikaci kvůli zjednodušení.

1. Vyberte **Soubor** > **nový** > **projekt**.

1. Zvolte **Console App (.NET Core)** a pak vyberte **Další**.

1. Do pole **Název projektu** zadejte název aplikace, například **CosmosTableSamples**. (Podle potřeby můžete zadat jiný název.)

1. Vyberte **Vytvořit**.

Všechny příklady kódu v této ukázce lze přidat do Main() metody **Program.cs** souboru aplikace konzoly.

## <a name="install-the-required-nuget-package"></a>Instalace požadovaného balíčku NuGet

Chcete-li získat balíček NuGet, postupujte takto:

1. Klikněte v **Průzkumníku řešení** pravým tlačítkem na projekt a vyberte **Spravovat balíčky NuGet**.

1. Vyhledejte [`Microsoft.Azure.Cosmos.Table`](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)online [`Microsoft.Extensions.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) [`Microsoft.Extensions.Configuration.Json`](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json)položku , a [`Microsoft.Extensions.Configuration.Binder`](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder) vyberte **nainstalovat,** chcete-li nainstalovat knihovnu tabulek Microsoft Azure Cosmos DB.

## <a name="configure-your-storage-connection-string"></a>Konfigurace připojovacího řetězce úložiště

1. Na [webu Azure Portal](https://portal.azure.com/)přejděte na svůj účet Azure Cosmos nebo účet Table Storage. 

1. Otevřete podokno **Připojovací řetězec** nebo **Přístupové klávesy.** Pomocí tlačítka pro kopírování na pravé straně okna zkopírujte **PRIMÁRNÍ PŘIPOJOVACÍ ŘETĚZEC**.

   ![Zobrazení a zkopírování PRIMÁRNÍHO PŘIPOJOVACÍHO ŘETĚZCE v podokně Připojovací řetězec](./media/create-table-dotnet/connection-string.png)
   
1. Chcete-li nakonfigurovat připojovací řetězec, klikněte z visual studia pravým tlačítkem myši na projekt **CosmosTableSamples**.

1. Vyberte **Přidat** a potom Přidat a potom **Přidat položku**. Vytvořte nový soubor **Settings.json** s typem souboru jako **konfigurační soubor TypeScript JSON.** 

1. Nahraďte kód v souboru Settings.json následujícím kódem a přiřaďte primární připojovací řetězec:

   ```csharp
   {
   "StorageConnectionString": <Primary connection string of your Azure Cosmos DB account>
   }
   ```

1. Klikněte pravým tlačítkem myši na váš projekt **CosmosTableSamples**. Vyberte **Přidat**, **Nová položka** a přidejte třídu s názvem **AppSettings.cs**.

1. Přidejte do souboru AppSettings.cs následující kód. Tento soubor přečte připojovací řetězec ze souboru Settings.json a přiřadí jej k parametru konfigurace:

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

## <a name="parse-and-validate-the-connection-details"></a>Analyzovat a ověřit podrobnosti o připojení 

1. Klikněte pravým tlačítkem myši na váš projekt **CosmosTableSamples**. Vyberte **Přidat**, **Nová položka** a přidejte třídu s názvem **Common.cs**. Napíšete kód pro ověření podrobností o připojení a vytvoříte tabulku v rámci této třídy.

1. Definujte `CreateStorageAccountFromConnectionString` metodu, jak je znázorněno níže. Tato metoda bude analyzovat podrobnosti připojovacího řetězce a ověřit, že název účtu a podrobnosti klíče účtu uvedené v souboru "Settings.json" jsou platné. 

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

Třída [CloudTableClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.cloudtableclient) vám umožňuje načítat tabulky a entity, které jsou uložené ve službě Table Storage. Protože nemáme žádné tabulky v účtu Cosmos DB Table API, `CreateTableAsync` přidáme metodu do **třídy Common.cs** a vytvoříme tabulku:

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

Pokud se zobrazí chyba "Výjimka není k dispozici služby 503", je možné, že požadované porty pro režim připojení jsou blokovány bránou firewall. Chcete-li tento problém vyřešit, otevřete požadované porty nebo použijte připojení režimu brány, jak je znázorněno v následujícím kódu:

```csharp
tableClient.TableClientConfiguration.UseRestExecutorForCosmosEndpoint = true;
```

## <a name="define-the-entity"></a>Definovat entitu 

Entity se mapují na objekty jazyka C# pomocí vlastní třídy odvozené z [TableEntity](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableentity). Když budete chtít do tabulky přidat entitu, vytvořte třídu, která definuje vlastnosti vaší entity.

Klikněte pravým tlačítkem myši na váš projekt **CosmosTableSamples**. Vyberte **Přidat**, Nová složka a **pojmenujte** ji jako **model**. Ve složce Model přidejte třídu s názvem **CustomerEntity.cs** a přidejte do ní následující kód.

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

Tento kód definuje třídu entity, která používá křestní jméno zákazníka jako klíč řádku a příjmení jako klíč oddílu. V tabulce ji pak jednoznačně identifikuje kombinace klíče oddílu a řádku entity. Entity se stejným klíčem oddílu mohou být dotazovány rychleji než entity s různými klíči oddílů, ale použití různých klíčů oddílů umožňuje větší škálovatelnost paralelních operací. Entity, které se mají ukládat do tabulek, musí být podporovaného typu, například odvozené ze třídy [TableEntity](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableentity). Vlastnosti entity, které chcete uložit do tabulky, musí být veřejné vlastnosti typu a musí podporovat získávání i nastavování hodnot. Typ entity navíc musí zveřejňovat konstruktor bez parametrů.

## <a name="insert-or-merge-an-entity"></a>Vložení nebo sloučení entity

Následující příklad kódu vytvoří objekt entity a přidá ho do tabulky. Metoda InsertOrMerge v rámci třídy [TableOperation](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableoperation) se používá k vložení nebo sloučení entity. Metoda [CloudTable.ExecuteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.cloudtable.executeasync?view=azure-dotnet) je volána ke spuštění operace. 

Klikněte pravým tlačítkem myši na váš projekt **CosmosTableSamples**. Vyberte **Přidat**, **Nová položka** a přidejte třídu s názvem **SamplesUtils.cs**. Tato třída ukládá veškerý kód potřebný k provádění operací CRUD na entitách. 

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

         // Get the request units consumed by the current operation. RequestCharge of a TableResult is only applied to Azure Cosmos DB
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

### <a name="get-an-entity-from-a-partition"></a>Získání entity z oddílu

Entitu můžete získat z oddílu pomocí metody Načíst v části [TableOperation.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableoperation) Následující příklad kódu získá klíč řádku oddílu, e-mail a telefonní číslo entity zákazníka. Tento příklad také vytiskne jednotky požadavku spotřebované pro dotaz na entitu. Chcete-li dotazovat na entitu, přidejte k **SamplesUtils.cs** souboru následující kód: 

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

Entitu můžete po jejím načtení snadno odstranit, a to pomocí stejného vzoru zobrazovaného pro aktualizaci entity. Následující kód načte a odstraní entitu zákazníka. Chcete-li odstranit entitu, připomtoujte k **SamplesUtils.cs** souboru následující kód: 

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

## <a name="execute-the-crud-operations-on-sample-data"></a>Provádění operací CRUD na ukázkových datech

Po definování metod pro vytvoření tabulek, vložení nebo sloučení entit spusťte tyto metody na ukázkových datech. Chcete-li tak učinit, klikněte pravým tlačítkem myši na váš projekt **CosmosTableSamples**. Vyberte **Přidat**, **Nová položka** a přidejte třídu s názvem **BasicSamples.cs** a přidejte do ní následující kód. Tento kód vytvoří tabulku, přidá do ní entity. Pokud chcete odstranit entitu a tabulku na konci projektu, `SamplesUtils.DeleteEntityAsync(table, customer)` odeberte komentáře a `table.DeleteIfExistsAsync()` metody z následujícího kódu:

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

Předchozí kód vytvoří tabulku, která začíná "demo" a generovaný identifikátor GUID je připojen k názvu tabulky. Potom přidá entitu zákazníka s křestním jménem a příjmením jako "Harp Walter" a později aktualizuje telefonní číslo tohoto uživatele. 

V tomto kurzu jste vytvořili kód pro provádění základních operací CRUD na datech uložených v účtu rozhraní TABLE API. Můžete také provádět pokročilé operace, jako je například – dávkové vkládání dat, dotazovat se na všechna data v rámci oddílu, dotazovat se na oblast dat v rámci oddílu, Vypořovat tabulky v účtu, jehož názvy začínají zadanou předponou. Můžete si stáhnout kompletní ukázkový formulář [azure-cosmos-table-dotnet-core-getting-started](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started) úložiště GitHub. Třída [AdvancedSamples.cs](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started/blob/master/CosmosTableSamples/AdvancedSamples.cs) má více operací, které můžete provádět s daty.  

## <a name="run-the-project"></a>Spuštění projektu

Z vašeho projektu **CosmosTableSamples**. Otevřete třídu s názvem **Program.cs** a přidejte do ní následující kód pro volání BasicSamples při spuštění projektu.

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

Nyní sestavte řešení a stisknutím klávesy F5 spusťte projekt. Po spuštění projektu se na příkazovém řádku zobrazí následující výstup:

![Výstup z příkazového řádku](./media/tutorial-develop-table-standard/output-from-sample.png)

Pokud se zobrazí chyba, která říká, že soubor Settings.json nelze při spuštění projektu najít, můžete jej vyřešit přidáním následující položky XML do nastavení projektu. Klikněte pravým tlačítkem myši na CosmosTableSamples, vyberte upravit cosmostablesamples.csproj a přidejte následující položkuSkupina: 

```csharp
  <ItemGroup>
    <None Update="Settings.json">
      <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    </None>
  </ItemGroup>
```
Teď se můžete přihlásit na portál Azure a ověřit, že data existují v tabulce. 

![Výsledky v portálu](./media/tutorial-develop-table-standard/results-in-portal.png)

## <a name="next-steps"></a>Další kroky

Teď můžete přejít k dalšímu kurzu a zjistit, jak migrovat data do účtu rozhraní API Azure Cosmos DB Table API. 

> [!div class="nextstepaction"]
>[Jak dotazovat data](../cosmos-db/table-import.md)
