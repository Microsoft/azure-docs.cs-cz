---
title: Jak začít pracovat s table storage a Visual Studio připojené služby (ASP.NET Core) | Dokumentace Microsoftu
description: Jak začít s Azure Table storage v projektu aplikace ASP.NET Core v sadě Visual Studio po připojení k účtu úložiště pomocí sady Visual Studio připojené služby
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
ms.openlocfilehash: 0cb2e04d788bce2d3a5f6bc46632b9ae18b6467f
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2018
ms.locfileid: "39112912"
---
# <a name="how-to-get-started-with-azure-table-storage-and-visual-studio-connected-services"></a>Jak začít s Azure Table storage a Visual Studio připojené služby

[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

Tento článek popisuje, jak začít používat Azure Table storage v sadě Visual Studio po odkazovat účtu služby Azure storage v projektu aplikace ASP.NET Core pomocí sady Visual Studio nebo vytvořili **připojené služby** funkce. **Připojené služby** operace nainstaluje příslušné balíčky NuGet pro přístup k Azure storage ve vašem projektu a přidá připojovací řetězec pro účet úložiště pro konfigurační soubory projektu. (Viz [dokumentace ke službě Storage](https://azure.microsoft.com/documentation/services/storage/) obecné informace o službě Azure Storage.)

Služba Azure Table storage umožňuje ukládat velké objemy strukturovaných dat. Tato služba je úložiště dat typu NoSQL, která přijímá ověřených volání z uvnitř i mimo Azure cloud. Tabulky Azure jsou ideální pro ukládání strukturovaných, nerelačních dat. Další obecné informace o používání služby Azure Table storage najdete v tématu [Začínáme s Azure Table storage pomocí .NET](../storage/storage-dotnet-how-to-use-tables.md).

Abyste mohli začít, nejprve vytvořte tabulku v účtu úložiště. Tento článek popisuje pak v tom, jak vytvořit tabulku v jazyce C# a jak provádět základní tabulka operace, jako jsou přidání, úprava, čtení a odebrání položky tabulky.  Tento kód použije klientskou knihovnu pro úložiště Azure pro .NET. Další informace o ASP.NET najdete v tématu [ASP.NET](http://www.asp.net).

Některé z rozhraní API služby Azure Storage jsou asynchronní a kódu v tomto článku se předpokládá, že se používají asynchronní metody. Zobrazit [asynchronní programování](https://docs.microsoft.com/dotnet/csharp/async) Další informace.

## <a name="access-tables-in-code"></a>Tabulky přístup v kódu

Chcete-li k tabulkám v projektech ASP.NET Core, zahrnují následující položky, které chcete žádné C# zdrojové soubory, které přístup k úložišti tabulek v Azure.

1. Přidat nezbytné `using` příkazy:

    ```csharp
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Table;
    using System.Threading.Tasks;
    ```

1. Získání `CloudStorageAccount` objekt reprezentující informace o vašem účtu úložiště. Použijte následující kód, pomocí názvu účtu úložiště a klíč účtu, který najdete v souboru appSettings.json připojovacího řetězce úložiště:

    ```csharp
        CloudStorageAccount storageAccount = new CloudStorageAccount(
            new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(
                "<name>", "<account-key>"), true);
    ```

1. Získání `CloudTableClient` objekt neodkazovali na objekty Tabulka ve vašem účtu úložiště:

    ```csharp
    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Získání `CloudTable` referenční objekt odkazování na určité tabulky a entity:

    ```csharp
    // Get a reference to a table named "peopleTable"
    CloudTable peopleTable = tableClient.GetTableReference("peopleTable");
    ```

## <a name="create-a-table-in-code"></a>Vytvoření tabulky v kódu

Vytvoření tabulek v Azure, vytvořte asynchronní metody a v něm, volejte `CreateIfNotExistsAsync()`:

```csharp
async void CreatePeopleTableAsync()
{
    // Create the CloudTable if it does not exist
    await peopleTable.CreateIfNotExistsAsync();
}
```
    
## <a name="add-an-entity-to-a-table"></a>Přidání entity do tabulky

Přidání entity do tabulky, vytvořte třídu, která definuje vlastnosti vaší entity. Následující kód definuje třídu entity, která je volána `CustomerEntity` , která používá jméno zákazníka jako klíč řádku a jeho příjmení jako klíč oddílu.

```csharp
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

Operace týkající se použití entit tabulek `CloudTable` objekt jste vytvořili dříve v [přístup k tabulkám v kódu](#access-tables-in-code). `TableOperation` Objekt představuje operaci provést. Následující příklad kódu ukazuje, jak vytvořit `CloudTable` objektu a `CustomerEntity` objektu. Přípravy na operaci `TableOperation` se vytvoří pro vložení entity zákazníka do tabulky. Nakonec se operace provede voláním `CloudTable.ExecuteAsync`.

```csharp
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

Do tabulky v rámci jedné zápisu operace můžete vložit více entit. Následující příklad kódu vytvoří dva objekty entity ("Jan Macek" a "Ben Smith"), se přidají do `TableBatchOperation` pomocí `Insert` metoda, a pak spustí operaci voláním `CloudTable.ExecuteBatchAsync`.

```csharp
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

Chcete-li dotaz na tabulku pro všechny entity v oddílu, použijte `TableQuery` objektu. Následující příklad kódu určuje filtr pro entity, kde Smith je klíč oddílu. Tento příklad zobrazí pole každé entity z výsledků dotazu z konzoly.

```csharp
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

## <a name="get-a-single-entity"></a>Získat jednu entitu

Můžete napsat dotaz pro získání jedné konkrétní entity. Následující kód používá `TableOperation` objektu k určení zákazníka s názvem "Ben Smith". Metoda vrátí pouze jednu entitu, nikoli kolekci a vrácenou hodnotou při `TableResult.Result` je `CustomerEntity` objektu. Zadání klíče oddílu a řádku v dotazu je nejrychlejší způsob, jak načíst jednu entitu ze `Table` služby.

```csharp
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

Entitu můžete po vás odstranit. Následující kód vyhledá a odstraní entitu zákazníka s názvem "Ben Smith":

```csharp
// Create a retrieve operation that expects a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the operation.
TableResult retrievedResult = await peopleTable.ExecuteAsync(retrieveOperation);

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
