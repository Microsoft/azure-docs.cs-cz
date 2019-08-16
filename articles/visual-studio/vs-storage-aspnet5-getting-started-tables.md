---
title: Jak začít s tabulkovým úložištěm a připojenými službami sady Visual Studio (ASP.NET Core) | Microsoft Docs
description: Jak začít s úložištěm Azure Table v ASP.NET Core projektu v aplikaci Visual Studio po připojení k účtu úložiště pomocí připojených služeb sady Visual Studio
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
ms.openlocfilehash: 7ac610e96d84568b7973f288623730ea1677ceac
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2019
ms.locfileid: "69510916"
---
# <a name="how-to-get-started-with-azure-table-storage-and-visual-studio-connected-services"></a>Jak začít s Azure Table Storage a připojenými službami sady Visual Studio

[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

Tento článek popisuje, jak začít používat úložiště Azure Table v aplikaci Visual Studio po vytvoření nebo odkazu na účet služby Azure Storage v ASP.NET Core projektu pomocí funkce **připojené služby** sady Visual Studio. Operace **připojené služby** nainstaluje příslušné balíčky NuGet pro přístup k úložišti Azure v projektu a přidá připojovací řetězec pro účet úložiště do konfiguračních souborů projektu. (Obecné informace o Azure Storage najdete v [dokumentaci k úložišti](https://azure.microsoft.com/documentation/services/storage/) .)

Služba Azure Table Storage umožňuje ukládat velké objemy strukturovaných dat. Služba je úložiště dat NoSQL, které přijímá ověřená volání zevnitř i mimo cloud Azure. Jsou ideální pro ukládání strukturovaných, nerelačních dat tabulky Azure. Obecnější informace o používání služby Azure Table Storage najdete v tématu Začínáme [s úložištěm Azure Table pomocí rozhraní .NET](../storage/storage-dotnet-how-to-use-tables.md).

Začněte tím, že nejprve vytvoříte tabulku v účtu úložiště. Tento článek ukazuje, jak vytvořit tabulku v C# nástroji a jak provádět základní operace s tabulkami, jako je například přidávání, úpravy, čtení a odebírání položek tabulky.  Kód používá klientskou knihovnu Azure Storage pro .NET. Další informace o ASP.NET najdete v tématu [ASP.NET](https://www.asp.net).

Některá rozhraní API Azure Storage jsou asynchronní a kód v tomto článku předpokládá použití asynchronních metod. Další informace najdete v tématu [asynchronní programování](https://docs.microsoft.com/dotnet/csharp/async) .

## <a name="access-tables-in-code"></a>Přístup k tabulkám v kódu

Chcete-li získat přístup k tabulkám v ASP.NET Core projektech, je třeba zahrnout následující C# položky do všech zdrojových souborů, které přistupují k úložišti tabulek Azure.

1. Přidejte potřebné `using` příkazy:

    ```csharp
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Table;
    using System.Threading.Tasks;
    ```

1. `CloudStorageAccount` Získejte objekt, který představuje informace o vašem účtu úložiště. Použijte následující kód s použitím názvu účtu úložiště a klíče účtu, který najdete v připojovacím řetězci úložiště v souboru appSettings. JSON:

    ```csharp
        CloudStorageAccount storageAccount = new CloudStorageAccount(
            new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(
                "<name>", "<account-key>"), true);
    ```

1. `CloudTableClient` Získat objekt pro odkazování na objekty tabulky v účtu úložiště:

    ```csharp
    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. `CloudTable` Získat referenční objekt pro odkaz na konkrétní tabulku a entity:

    ```csharp
    // Get a reference to a table named "peopleTable"
    CloudTable peopleTable = tableClient.GetTableReference("peopleTable");
    ```

## <a name="create-a-table-in-code"></a>Vytvoření tabulky v kódu

Tabulku Azure vytvoříte tak, že vytvoříte asynchronní metodu a v ní zavoláte `CreateIfNotExistsAsync()`:

```csharp
async void CreatePeopleTableAsync()
{
    // Create the CloudTable if it does not exist
    await peopleTable.CreateIfNotExistsAsync();
}
```
    
## <a name="add-an-entity-to-a-table"></a>Přidání entity do tabulky

Chcete-li přidat entitu do tabulky, vytvořte třídu, která definuje vlastnosti vaší entity. Následující kód definuje třídu entity s názvem `CustomerEntity` , která používá křestní jméno zákazníka jako klíč řádku a příjmení jako klíč oddílu.

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

Operace s tabulkami zahrnující entity `CloudTable` používají objekt, který jste vytvořili dříve v [tabulkách přístupu v kódu](#access-tables-in-code). `TableOperation` Objekt představuje operaci, kterou chcete provést. Následující příklad kódu ukazuje, jak vytvořit `CloudTable` objekt `CustomerEntity` a objekt. Pro přípravu operace se vytvoří a `TableOperation` vloží entitu Customer do tabulky. Nakonec se operace provede voláním `CloudTable.ExecuteAsync`.

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

Do tabulky můžete vložit více entit v rámci jedné operace zápisu. Následující příklad kódu vytvoří dva objekty entity ("Jan Novák" a "Ben Smith"), přidá je do `TableBatchOperation` objektu `Insert` pomocí metody a poté spustí operaci voláním `CloudTable.ExecuteBatchAsync`.

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

Chcete-li zadat dotaz na tabulku pro všechny entity v oddílu, použijte `TableQuery` objekt. Následující příklad kódu určuje filtr pro entity, kde Smith je klíč oddílu. Tento příklad zobrazí pole každé entity z výsledků dotazu z konzoly.

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

Můžete napsat dotaz, který získá jednu konkrétní entitu. Následující kód používá `TableOperation` objekt k určení zákazníka s názvem "Ben Smith". Metoda vrátí pouze jednu entitu, nikoli kolekci, a vrácená hodnota v `TableResult.Result` `CustomerEntity` je objekt. Zadání obou klíčů oddílů i řádků v dotazu představuje nejrychlejší způsob, jak z `Table` služby načíst jednu entitu.

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

Entitu můžete po nalezení odstranit. Následující kód vyhledá a odstraní entitu zákazníka s názvem "Ben Smith":

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
