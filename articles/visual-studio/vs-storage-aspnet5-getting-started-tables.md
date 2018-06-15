---
title: Jak začít pracovat s úložiště tabulek a Visual Studio připojené služby (ASP.NET Core) | Microsoft Docs
description: Jak začít pracovat s Azure Table storage v projektu ASP.NET Core v sadě Visual Studio po připojení k účtu úložiště pomocí sady Visual Studio připojené služby
services: storage
author: ghogen
manager: douge
ms.assetid: c3c451d1-71ff-4222-a348-c41c98a02b85
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ghogen
ms.openlocfilehash: e53e8ed27cfc048f24bda4ef92fcd2a50a85ed07
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
ms.locfileid: "31794112"
---
# <a name="how-to-get-started-with-azure-table-storage-and-visual-studio-connected-services"></a>Jak začít pracovat s Azure Table storage a Visual Studio připojené služby

[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

Tento článek popisuje, jak začít používat Azure Table storage v sadě Visual Studio, po vytvoření a odkazuje pomocí sady Visual Studio účet úložiště Azure v projektu ASP.NET Core **připojené služby** funkce. **Připojené služby** operaci nainstaluje příslušné balíčky NuGet pro přístup k úložišti Azure ve vašem projektu a přidá připojovací řetězec pro účet úložiště v konfiguračních souborech projektu. (Viz [úložiště dokumentace](https://azure.microsoft.com/documentation/services/storage/) obecné informace o službě Azure Storage.)

Služba úložiště Azure Table umožňuje ukládat velké množství strukturovaná data. Služba je úložiště dat typu NoSQL, která přijímá ověřených volání z uvnitř i vně cloudu Azure. Tabulky Azure jsou ideální pro ukládání strukturovaných, nerelačních dat. Další obecné informace o používání Azure Table storage najdete v tématu [Začínáme s Azure Table storage pomocí rozhraní .NET](../storage/storage-dotnet-how-to-use-tables.md).

Chcete-li začít, nejdřív vytvořte tabulku ve vašem účtu úložiště. Pak se tento článek ukazuje, jak vytvořit tabulku v jazyce C# a jak provádět základní tabulka operace, jako je přidání, úprava, čtení a odebrání položek tabulky.  Kód používá Klientská knihovna pro úložiště Azure pro .NET. Další informace o technologii ASP.NET najdete v tématu [ASP.NET](http://www.asp.net).

Některé z rozhraní API úložiště Azure jsou asynchronní a kód v tomto článku předpokládá, že asynchronní metody jsou používány. V tématu [asynchronní programování](https://docs.microsoft.com/dotnet/csharp/async) Další informace.

## <a name="access-tables-in-code"></a>Přístup k tabulky v kódu

Chcete-li získat přístup k tabulky v projektů ASP.NET Core, zahrnují následující položky pro všechny C# zdrojové soubory, které přístupu k Azure table storage.

1. Přidat nezbytné `using` příkazy:

    ```cs
    using Microsoft.Framework.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Table;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Framework.Logging.LogLevel;
    ```

1. Získání `CloudStorageAccount` objekt, který reprezentuje informace o účtu úložiště. Chcete-li získat připojovací řetězec úložiště a informace o účtu úložiště z konfigurace služby Azure, použijte následující kód:

    ```cs
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Získání `CloudTableClient` objekt, který má odkazovat na tabulku objekty v účtu úložiště:

    ```cs
    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Získání `CloudTable` odkaz na objekt, který chcete odkazovat na konkrétní tabulky a entity:

    ```cs
    // Get a reference to a table named "peopleTable"
    CloudTable peopleTable = tableClient.GetTableReference("peopleTable");
    ```

## <a name="create-a-table-in-code"></a>Vytvoří tabulku v kódu

Chcete-li vytvořit tabulky Azure, volejte '' CreateIfNotExistsAsync()':

```cs
// Create the CloudTable if it does not exist
await peopleTable.CreateIfNotExistsAsync();
```

## <a name="add-an-entity-to-a-table"></a>Přidání entity do tabulky

Chcete-li do tabulky přidat entitu, vytvořte třídu, která definuje vlastnosti vaší entity. Následující kód definuje třídu entity, která je volána `CustomerEntity` která používá jméno zákazníka jako klíč řádku a jeho příjmení jako klíč oddílu.

```cs
public class CustomerEntity : TableEntity
{
    public CustomerEntity(string lastName, string firstName)
    {
        this.PartitionKey = lastName;
        this.RowKey = firstName;
    }

    public CustomerEntity() { }

    public string Email { get; set; }

    public string PhoneNumber { get; set; }
}
```

Tabulka operací zahrnujících použijte entity `CloudTable` objektu jste vytvořili předtím v [přístup k tabulkám v kódu](#access-tables-in-code). `TableOperation` Objekt představuje operaci provést. Následující příklad kódu ukazuje, jak vytvořit `CloudTable` objektu a `CustomerEntity` objektu. Příprava operaci `TableOperation` se vytvoří pro vložení entity zákazníka do tabulky. Nakonec se operace provede voláním `CloudTable.ExecuteAsync`.

```cs
// Create a new customer entity.
CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
customer1.Email = "Walter@contoso.com";
customer1.PhoneNumber = "425-555-0101";

// Create the TableOperation that inserts the customer entity.
TableOperation insertOperation = TableOperation.Insert(customer1);

// Execute the insert operation.
await peopleTable.ExecuteAsync(insertOperation);
```

## <a name="insert-a-batch-of-entities"></a>Vložení dávky entit

Do tabulky v rámci jedné zápisu operace můžete vložit více entit. Následující příklad kódu vytvoří dva objekty entity ("Jeff Smith" a "Ben Smith"), se přidají do `TableBatchOperation` pomocí `Insert` metoda, a pak spustí operaci voláním `CloudTable.ExecuteBatchAsync`.

```cs
// Create the batch operation.
TableBatchOperation batchOperation = new TableBatchOperation();

// Create a customer entity and add it to the table.
CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
customer1.Email = "Jeff@contoso.com";
customer1.PhoneNumber = "425-555-0104";

// Create another customer entity and add it to the table.
CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
customer2.Email = "Ben@contoso.com";
customer2.PhoneNumber = "425-555-0102";

// Add both customer entities to the batch insert operation.
batchOperation.Insert(customer1);
batchOperation.Insert(customer2);

// Execute the batch operation.
await peopleTable.ExecuteBatchAsync(batchOperation);
```

## <a name="get-all-of-the-entities-in-a-partition"></a>Získání všech entit v oddílu

Dotaz na tabulku pro všechny entity v oddílu, použijte `TableQuery` objektu. Následující příklad kódu určuje filtr pro entity, kde Smith je klíč oddílu. Tento příklad zobrazí pole každé entity z výsledků dotazu z konzoly.

```cs
// Construct the query operation for all customer entities where PartitionKey="Smith".
TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

// Print the fields for each customer.
TableContinuationToken token = null;
do
{
    TableQuerySegment<CustomerEntity> resultSegment = await peopleTable.ExecuteQuerySegmentedAsync(query, token);
    token = resultSegment.ContinuationToken;

    foreach (CustomerEntity entity in resultSegment.Results)
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
    }
} while (token != null);
```

## <a name="get-a-single-entity"></a>Získat jedné entity

Můžete napsat dotaz pro získání jedné konkrétní entity. Následující kód používá `TableOperation` objekt, který chcete zadat zákazníka s názvem 'Ben Smith'. Metoda vrátí pouze jednu entitu, místo do kolekce a vrácenou hodnotou při `TableResult.Result` je `CustomerEntity` objektu. Určení klíče oddílu a řádku v dotazu je nejrychlejší způsob, jak načíst jednu entitu ze `Table` služby.

```cs
// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = await peopleTable.ExecuteAsync(retrieveOperation);

// Print the phone number of the result.
if (retrievedResult.Result != null)
   Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
else
   Console.WriteLine("The phone number could not be retrieved.");
```

## <a name="delete-an-entity"></a>Odstranění entity

Entitu můžete odstranit po ji najít. Následující kód vyhledává a odstraní entitu zákazníka s názvem "Ben Smith":

```cs
// Create a retrieve operation that expects a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the operation.
TableResult retrievedResult = peopleTable.Execute(retrieveOperation);

// Assign the result to a CustomerEntity object.
CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

// Create the Delete TableOperation and then execute it.
if (deleteEntity != null)
{
   TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

   // Execute the operation.
   await peopleTable.ExecuteAsync(deleteOperation);

   Console.WriteLine("Entity deleted.");
}

else
   Console.WriteLine("Couldn't delete the entity.");
```

## <a name="next-steps"></a>Další postup
[!INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]
