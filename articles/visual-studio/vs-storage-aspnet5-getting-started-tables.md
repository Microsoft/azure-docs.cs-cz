---
title: Začínáme s tabulkovým úložištěm pomocí Sady Visual Studio (ASP.NET Core)
description: Jak začít s úložištěm Azure Table v projektu ASP.NET Core v sadě Visual Studio po připojení k účtu úložiště pomocí připojených služeb Visual Studia
services: storage
author: ghogen
manager: jillfra
ms.assetid: c3c451d1-71ff-4222-a348-c41c98a02b85
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: d209f8117b1e061877daf2f8d316bd01ed4f84cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72298814"
---
# <a name="how-to-get-started-with-azure-table-storage-and-visual-studio-connected-services"></a>Jak začít s úložištěm Azure Table a připojenými službami Visual Studia

[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

Tento článek popisuje, jak začít používat azure table storage ve Visual Studiu po vytvoření nebo odkazování na účet úložiště Azure v projektu ASP.NET Core pomocí funkce Visual Studio **Connected Services.** Operace **Připojené služby** nainstaluje příslušné balíčky NuGet pro přístup k úložišti Azure ve vašem projektu a přidá připojovací řetězec pro účet úložiště do konfiguračních souborů projektu. (Obecné informace o Azure Storage najdete v [dokumentaci k](https://azure.microsoft.com/documentation/services/storage/) úložišti.)

Služba úložiště Azure Table umožňuje ukládat velké objemy strukturovaných dat. Služba je úložiště dat NoSQL, které přijímá ověřená volání z cloudu Azure i mimo něj. Tabulky Azure jsou ideální pro ukládání strukturovaných, nerelačních dat. Další obecné informace o používání úložiště Azure Table [najdete v tématu Začínáme s úložištěm Azure Table pomocí rozhraní .NET](../storage/storage-dotnet-how-to-use-tables.md).

Chcete-li začít, nejprve vytvořte tabulku v účtu úložiště. Tento článek pak ukazuje, jak vytvořit tabulku v jazyce C# a jak provádět základní operace tabulky, jako je například přidávání, úpravy, čtení a odebrání položek tabulky.  Kód používá klientskou knihovnu úložiště Azure pro rozhraní .NET. Další informace o ASP.NET naleznete v [tématu ASP.NET](https://www.asp.net).

Některá api úložiště Azure jsou asynchronní a kód v tomto článku předpokládá, že se používají asynchronní metody. Další informace naleznete [v tématu Asynchronní programování.](https://docs.microsoft.com/dotnet/csharp/async)

## <a name="access-tables-in-code"></a>Přístup k tabulkám v kódu

Chcete-li získat přístup k tabulkám v ASP.NET projektů core, musíte zahrnout následující položky do všech zdrojových souborů C#, které přistupují k úložišti tabulek Azure.

1. Přidejte `using` potřebné příkazy:

    ```csharp
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Table;
    using System.Threading.Tasks;
    ```

1. Získejte `CloudStorageAccount` objekt, který představuje informace o účtu úložiště. Použijte následující kód pomocí názvu účtu úložiště a klíče účtu, který najdete v připojovacím řetězci úložiště v aplikaciSettings.json:

    ```csharp
        CloudStorageAccount storageAccount = new CloudStorageAccount(
            new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(
                "<name>", "<account-key>"), true);
    ```

1. Získejte `CloudTableClient` objekt, který bude odkazovat na objekty tabulky v účtu úložiště:

    ```csharp
    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Získejte `CloudTable` referenční objekt, který odkazuje na určitou tabulku a entity:

    ```csharp
    // Get a reference to a table named "peopleTable"
    CloudTable peopleTable = tableClient.GetTableReference("peopleTable");
    ```

## <a name="create-a-table-in-code"></a>Vytvoření tabulky v kódu

Chcete-li vytvořit tabulku Azure, vytvořte asynchronní metodu a v ní volejte `CreateIfNotExistsAsync()`:

```csharp
async void CreatePeopleTableAsync()
{
    // Create the CloudTable if it does not exist
    await peopleTable.CreateIfNotExistsAsync();
}
```
    
## <a name="add-an-entity-to-a-table"></a>Přidání entity do tabulky

Chcete-li do tabulky přidat entitu, vytvořte třídu, která definuje vlastnosti entity. Následující kód definuje třídu `CustomerEntity` entity s názvem, která používá křestní jméno zákazníka jako klíč řádku a příjmení jako klíč oddílu.

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

Operace tabulky zahrnující entity `CloudTable` používají objekt, který jste vytvořili dříve v [tabulkách aplikace Access v kódu](#access-tables-in-code). Objekt `TableOperation` představuje operaci, která má být provedena. Následující příklad kódu ukazuje, `CloudTable` jak vytvořit `CustomerEntity` objekt a objekt. Pro přípravu operace `TableOperation` je vytvořen a vložit entitu zákazníka do tabulky. Nakonec je operace provedena voláním `CloudTable.ExecuteAsync`.

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

Do tabulky můžete vložit více entit v rámci jedné operace zápisu. Následující příklad kódu vytvoří dva objekty entity ("Jeff Novák" `TableBatchOperation` a `Insert` "Ben Novák"), přidá `CloudTable.ExecuteBatchAsync`je k objektu pomocí metody a potom spustí operaci voláním .

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

Chcete-li zadat dotaz na tabulku pro všechny `TableQuery` entity v oddílu, použijte objekt. Následující příklad kódu určuje filtr pro entity, kde Smith je klíč oddílu. Tento příklad zobrazí pole každé entity z výsledků dotazu z konzoly.

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

## <a name="get-a-single-entity"></a>Získání jedné entity

Můžete napsat dotaz získat jednu konkrétní entitu. Následující kód používá `TableOperation` objekt k určení zákazníka s názvem "Ben Smith". Metoda vrátí pouze jednu entitu, nikoli kolekci a vrácená hodnota v `TableResult.Result` je `CustomerEntity` objekt. Zadání klíče oddílu a řádku v dotazu je nejrychlejší způsob, `Table` jak načíst jednu entitu ze služby.

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

Entitu můžete odstranit poté, co ji najdete. Následující kód vyhledá a odstraní entitu zákazníka s názvem "Ben Smith":

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

## <a name="next-steps"></a>Další kroky
[!INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]
