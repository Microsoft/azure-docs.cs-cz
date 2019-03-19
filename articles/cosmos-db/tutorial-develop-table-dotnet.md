---
title: Začínáme s Azure Cosmos DB Table API pomocí .NET Standard SDK
description: Store strukturovaných dat v cloudu s využitím rozhraní Azure Cosmos DB Table API.
author: wmengmsft
ms.author: wmeng
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: sample
ms.date: 03/11/2019
ms.openlocfilehash: 0f324d39db38b17d436583277d60d87b2878d131
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "57880785"
---
# <a name="get-started-with-azure-cosmos-db-table-api-and-azure-table-storage-using-the-net-sdk"></a>Začínáme s Azure Cosmos DB Table API a Azure Table storage pomocí .NET SDK

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]

[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

Azure Cosmos DB Table API nebo Azure Table storage můžete použít k ukládání strukturovaných NoSQL ukládat data v cloudu, a poskytují klíčů/atributů s pomocí schématu méně návrhu. Vzhledem k tomu Azure Cosmos DB Table API a Table storage schématu méně, je snadné data přizpůsobovat měnícím potřebám vaší aplikace. Azure Cosmos DB Table API nebo Table storage můžete použít k ukládání flexibilních datových sad, například uživatelských dat pro webové aplikace, adresářů, informací o zařízení nebo dalších typů metadat, které vaše služba vyžaduje. 

Tento kurz popisuje vzorku, který ukazuje způsob použití [Microsoft Azure Cosmos DB Table Library pro .NET](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) se scénáři Azure Cosmo DB Table API a Azure Table storage. Je nutné použít připojení, které jsou specifické pro službu Azure. Tyto scénáře jsou probírány pomocí C# příklady, které ukazují, jak vytvářet tabulky, vložení / aktualizaci dat, dotazování na data a odstranění tabulek.

## <a name="prerequisites"></a>Požadavky

Pro úspěšné dokončení této ukázky potřebujete následující položky:

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

* [Microsoft Azure cosmos DB Table Library pro .NET](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) -této knihovny je aktuálně dostupné pro .NET Standard a .NET framework. 

* [Účet Azure Cosmos DB Table API](create-table-dotnet.md#create-a-database-account).

## <a name="create-an-azure-cosmos-db-table-api-account"></a>Vytvoření účtu rozhraní Table API služby Azure Cosmos DB

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="create-a-net-console-project"></a>Vytvořit projekt konzoly .NET

V sadě Visual Studio vytvořte novou konzolovou aplikaci .NET. Následující kroky ukazují, jak vytvořit konzolovou aplikaci v sadě Visual Studio 2017. Kroky u ostatních verzí sady Visual Studio jsou podobné. Můžete použít knihovnu tabulky Azure Cosmos DB z libovolného typu aplikace .NET, včetně Azure cloud service nebo do webové aplikace a desktopové nebo mobilní aplikace. V této příručce použijeme konzolovou aplikaci kvůli zjednodušení.

1. Vyberte **Soubor** > **Nový** > **Projekt**.

1. Vyberte **nainstalované** > **Visual C#**   >  **aplikace (.NET Core) konzoly**.

1. V **název** pole, zadejte název pro vaši aplikaci, například **CosmosTableSamples** (můžete zadat jiný název podle potřeby).

1. Vyberte **OK**.

Všechny příklady kódu v této ukázce lze přidat do metody Main() vaší konzolové aplikace **Program.cs** souboru.

## <a name="install-the-required-nuget-package"></a>Nainstalujte požadovaný balíček NuGet

Pokud chcete získat balíček NuGet, postupujte takto:

1. Klikněte v **Průzkumníku řešení** pravým tlačítkem na projekt a vyberte **Spravovat balíčky NuGet**.

1. Online hledání `Microsoft.Azure.Cosmos.Table`, `Microsoft.Extensions.Configuration`, `Microsoft.Extensions.Configuration.Json`, `Microsoft.Extensions.Configuration.Binder` a vyberte **nainstalovat** k instalaci knihovny Microsoft Azure Cosmos DB Table.

## <a name="configure-your-storage-connection-string"></a>Konfigurace připojovacího řetězce úložiště

1. Z [webu Azure portal](https://portal.azure.com/), klikněte na tlačítko **připojovací řetězec**. Pomocí tlačítka pro kopírování na pravé straně okna zkopírujte **PRIMÁRNÍ PŘIPOJOVACÍ ŘETĚZEC**.

   ![Zobrazení a zkopírování PRIMÁRNÍHO PŘIPOJOVACÍHO ŘETĚZCE v podokně Připojovací řetězec](./media/create-table-dotnet/connection-string.png)
   
1. Pokud chcete konfigurovat připojovací řetězec, ze sady visual studio klikněte pravým tlačítkem na projekt **CosmosTableSamples**.

1. Vyberte **přidat** a potom **nová položka**. Vytvořte nový soubor **Settings.json** s typem souboru jako **TypeScript JSON konfigurace** souboru. 

1. Nahraďte kód v souboru Settings.json následujícím kódem a přiřaďte primární připojovací řetězec:

   ```csharp
   {
   "StorageConnectionString": <Primary connection string of your Azure Cosmos DB account>
   }
   ```

1. Klikněte pravým tlačítkem na projekt **CosmosTableSamples**. Vyberte **přidat**, **nová položka** a přidejte třídu pojmenovanou **AppSettings.cs**.

1. Přidejte následující kód do souboru AppSettings.cs. Tento soubor načte připojovací řetězec ze souboru Settings.json a přiřadí ji k konfigurační parametr:

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

## <a name="parse-and-validate-the-connection-details"></a>Provádění analýzy a ověření podrobností o připojení 

1. Klikněte pravým tlačítkem na projekt **CosmosTableSamples**. Vyberte **přidat**, **nová položka** a přidejte třídu pojmenovanou **Common.cs**. Budete psát kód pro ověření podrobností o připojení a vytvoření tabulky v rámci této třídy.

1. Definujte metodu `CreateStorageAccountFromConnectionString` jak je znázorněno níže. Tato metoda se analyzovat podrobnosti o připojovacím řetězci a ověřit, že název účtu a účet klíče podrobné informace uvedené v souboru "Settings.json" jsou platné. 

   ```csharp
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
   ```


## <a name="create-a-table"></a>Vytvoření tabulky 

Třída [CloudTableClient](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.table.cloudtableclient?redirectedfrom=MSDN&view=azure-dotnet) vám umožňuje načíst tabulky a entity, které jsou uložené ve službě Table Storage. Protože nemáme žádné tabulky v účtu Cosmos DB Table API, přidáme `CreateTableAsync` metodu **Common.cs** třídy za účelem vytvoření tabulky:

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

## <a name="define-the-entity"></a>Definování entity 

Entity se mapují na C# objekty pomocí vlastní třídy odvozené z [TableEntity](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableentity.aspx). Když budete chtít do tabulky přidat entitu, vytvořte třídu, která definuje vlastnosti vaší entity.

Klikněte pravým tlačítkem na projekt **CosmosTableSamples**. Vyberte **přidat**, **novou složku** a pojmenujte ji jako **modelu**. Ve složce modelu přidejte třídu pojmenovanou **CustimerEntity.cs** a přidejte do ní následující kód.

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

Tento kód definuje třídu entity, která používá jméno zákazníka jako klíč řádku a jeho příjmení jako klíč oddílu. V tabulce ji pak jednoznačně identifikuje kombinace klíče oddílu a řádku entity. Entity se stejným klíčem oddílu můžete dotazovat rychleji než entity mají různé klíče oddílů, ale používání různých klíčů oddílů umožňuje větší škálovatelnost paralelních operací. Entity, které se ukládají do tabulek musí být podporovaného typu, například odvozené ze [TableEntity](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.table.tableentity?redirectedfrom=MSDN&view=azure-dotnet) třídy. Vlastnosti entity, které chcete uložit do tabulky, musí být veřejné vlastnosti typu a musí podporovat získávání i nastavování hodnot. Typ entity navíc musí vystavit konstruktor bez parametrů.

## <a name="insert-or-merge-an-entity"></a>Vložit nebo sloučit entity

Následující příklad kódu vytvoří objekt entity a přidá ji do tabulky. Metoda InsertOrMerge v rámci [TableOperation](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.table.tableoperation?redirectedfrom=MSDN&view=azure-dotnet) třída slouží k vložení nebo sloučit entity. [CloudTable.ExecuteAsync](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.table.cloudtable.executeasync?view=azure-dotnet) metoda je volána k provedení operace. 

Klikněte pravým tlačítkem na projekt **CosmosTableSamples**. Vyberte **přidat**, **nová položka** a přidejte třídu pojmenovanou **SamplesUtils.cs**. Tato třída uchovává všechny je kód potřebný k provádění operací CRUD u entity. 

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

### <a name="get-an-entity-from-a-partition"></a>Načtení entity z oddílu

Entity můžete získat z oddílu pomocí metody načtení [TableOperation](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.table.tableoperation?redirectedfrom=MSDN&view=azure-dotnet) třídy. Následující příklad kódu získá klíč řádku klíče oddílu, e-mailu a telefonní číslo entity zákazník. Tento příklad také vytiskne jednotky žádostí spotřebované dotaz pro entitu. Chcete-li dotaz pro entitu, přidejte následující kód, který **SamplesUtils.cs** souboru: 

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

Entitu můžete po jejím načtení snadno odstranit, a to pomocí stejného vzoru zobrazovaného pro aktualizaci entity. Následující kód načte a odstraní entitu zákazníka. Chcete-li odstranit entitu, přidejte následující kód, který **SamplesUtils.cs** souboru: 

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

## <a name="execute-the-crud-operations-on-sample-data"></a>Provádění operací CRUD na ukázková data

Po definování metody vytvoření entity tabulky, vložení nebo sloučení, spusťte tyto metody na ukázková data. Uděláte to tak, klikněte pravým tlačítkem myši na projekt **CosmosTableSamples**. Vyberte **přidat**, **nová položka** a přidejte třídu pojmenovanou **soubor BasicSamples.cs** a přidejte do ní následující kód. Tento kód vytvoří tabulku, přidá entity do něj. Pokud chcete odstranit entitu a tabulky na konci projekt odstranit znaky komentářů z `table.DeleteIfExistsAsync()` a `SamplesUtils.DeleteEntityAsync(table, customer)` metody následující kód:

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

Předchozí kód vytvoří tabulku, která začíná textem "ukázka" a vytvářenému identifikátoru GUID je připojena k názvu tabulky. Potom přidá entita zákazník s křestní jméno a příjmení jako "Waltera Harpa" a později aktualizuje telefonní číslo uživatele. 

V tomto kurzu jste vytvořili kód k provádění základních operací CRUD s daty uloženými v účet Table API. Můžete také provádět pokročilé operace, jako – dávkové vkládání dat, dotaz všechna data v rámci oddílu, dotaz rozsahu dat v rámci oddílu, seznamy tabulek v účtu, jejichž názvy začínají se zadanou předponou. Můžete si stáhnout úplný ukázkový formulář [azure-cosmos-table-dotnet-core-getting-started](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started) úložiště GitHub. [AdvancedSamples.cs](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started/blob/master/CosmosTableSamples/AdvancedSamples.cs) třída má více operací, které můžete provést na datech.  

## <a name="run-the-project"></a>Spuštění projektu

Nyní sestavte řešení a stiskněte klávesu F5 a spusťte projekt. Při spuštění projektu se zobrazí následující výstup v příkazovém řádku:

![Výstup z příkazového řádku](./media/tutorial-develop-table-standard/output-from-sample.png)

Pokud se zobrazí chybová zpráva s upozorněním, že nebyl nalezen soubor Settings.JSON v nástroji při spuštění projektu, ho mohli vyřešit přidáním následujících položky XML do nastavení projektu. Klikněte pravým tlačítkem na CosmosTableSamples, vyberte Upravit CosmosTableSamples.csproj a přidejte následující element itemGroup: 

```csharp
  <ItemGroup>
    <None Update="Settings.json">
      <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    </None>
  </ItemGroup>
```
Nyní můžete přihlásit na webu Azure portal a ověřte, že data v tabulce existuje. 

![Výsledky v portálu](./media/tutorial-develop-table-standard/results-in-portal.png)

## <a name="next-steps"></a>Další postup

Teď můžete pokračovat k dalšímu kurzu a zjistěte, jak migrovat data do účtu Azure Cosmos DB Table API. 

> [!div class="nextstepaction"]
>[Jak provádět dotazy na data](../cosmos-db/table-import.md)
