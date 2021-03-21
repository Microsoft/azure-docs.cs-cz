---
title: Začínáme s tabulkovým úložištěm pomocí sady Visual Studio (cloudové služby)
description: Jak začít používat službu Azure Table Storage v projektu cloudové služby v aplikaci Visual Studio po připojení k účtu úložiště pomocí připojených služeb sady Visual Studio
services: storage
author: ghogen
manager: jillfra
ms.assetid: a3a11ed8-ba7f-4193-912b-e555f5b72184
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure, devx-track-csharp
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: e5e687b172b49fec5f77615e332d0a2204162c43
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "95545771"
---
# <a name="getting-started-with-azure-table-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Začínáme s Azure Table Storage a připojenými službami sady Visual Studio (projekty cloudových služeb)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Přehled
Tento článek popisuje, jak začít používat službu Azure Table Storage v aplikaci Visual Studio po vytvoření nebo odkazování účtu úložiště Azure v projektu cloudové služby pomocí dialogového okna **Přidat připojené služby** sady Visual Studio. Operace **Přidat připojené služby** nainstaluje příslušné balíčky NuGet pro přístup k úložišti Azure v projektu a přidá připojovací řetězec pro účet úložiště do konfiguračních souborů projektu.

Služba Azure Table Storage umožňuje ukládat velké objemy strukturovaných dat. Služba je úložiště dat NoSQL, které přijímá ověřená volání zevnitř i mimo cloud Azure. Tabulky Azure jsou ideální pro ukládání strukturovaných, nerelačních dat.

Abyste mohli začít, musíte nejdřív vytvořit tabulku v účtu úložiště. Ukážeme vám, jak vytvořit tabulku Azure v kódu a také jak provádět základní operace s tabulkami a entitami, jako je přidání, úprava, čtení a čtení entit tabulky. Ukázky jsou napsány v \# kódu jazyka C a používají [Microsoft Azure Storage klientské knihovny pro .NET](/previous-versions/azure/dn261237(v=azure.100)).

**Poznámka:** Některá rozhraní API, která provádějí volání do služby Azure Storage, jsou asynchronní. Další informace najdete v tématu [asynchronní programování pomocí Async a operátoru await](/previous-versions/hh191443(v=vs.140)) . Níže uvedený kód předpokládá použití asynchronních programovacích metod.

* Další informace o programové manipulaci s tabulkami najdete v tématu Začínáme [s Azure Table Storage pomocí rozhraní .NET](../cosmos-db/tutorial-develop-table-dotnet.md) .
* Obecné informace o Azure Storage najdete v [dokumentaci k úložišti](https://azure.microsoft.com/documentation/services/storage/) .
* Obecné informace o cloudových službách Azure najdete v [dokumentaci k Cloud Services](https://azure.microsoft.com/documentation/services/cloud-services/) .
* Další informace o programování aplikací ASP.NET naleznete v tématu [ASP.NET](https://www.asp.net) .

## <a name="access-tables-in-code"></a>Přístup k tabulkám v kódu
Chcete-li získat přístup k tabulkám v projektech cloudové služby, je třeba zahrnout následující položky do všech zdrojových souborů C#, které přistupují k úložišti tabulek Azure.

1. Ujistěte se, že deklarace oboru názvů v horní části souboru jazyka C# obsahují tyto příkazy **using** .
   
    ```csharp
    using Microsoft.Framework.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Table;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Framework.Logging.LogLevel;
    ```
2. Získejte objekt **CloudStorageAccount** , který představuje informace o vašem účtu úložiště. K získání připojovacího řetězce a informací o účtu úložiště z konfigurace služby Azure použijte následující kód.
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("<storage account name>
    _AzureStorageConnectionString"));
    ```
   > [!NOTE]
   > Použijte veškerý výše uvedený kód před kódem v následujících ukázkách.
   > 
   > 
3. Získejte objekt **cloudtableclient vám** , který odkazuje na objekty tabulky v účtu úložiště.
   
    ```csharp
    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```
4. Získá objekt odkazu na **Cloud** , který odkazuje na konkrétní tabulku a entity.
   
    ```csharp
    // Get a reference to a table named "peopleTable".
    CloudTable peopleTable = tableClient.GetTableReference("peopleTable");
    ```

## <a name="create-a-table-in-code"></a>Vytvoření tabulky v kódu
Chcete-li vytvořit tabulku Azure, stačí přidat volání **CreateIfNotExistsAsync** do až po získání objektu **cloudu** , jak je popsáno v části "tabulky pro přístup v kódu".

```csharp
// Create the CloudTable if it does not exist.
await peopleTable.CreateIfNotExistsAsync();
```

## <a name="add-an-entity-to-a-table"></a>Přidání entity do tabulky
Když budete chtít do tabulky přidat entitu, vytvořte třídu, která definuje vlastnosti vaší entity. Následující kód definuje třídu entity s názvem **CustomerEntity** , která používá křestní jméno zákazníka jako klíč řádku a příjmení jako klíč oddílu.

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

Operace s tabulkou zahrnující entity se provádí pomocí objektu **cloudu** , který jste vytvořili dříve v části "přístup k tabulkám v kódu". Objekt **TableOperation** představuje operaci, kterou chcete provést. Následující příklad kódu ukazuje, jak vytvořit objekt **cloudu** a objekt **CustomerEntity** . Pro přípravu operace je vytvořen **TableOperation** pro vložení entity zákazníka do tabulky. Nakonec se operace provede voláním **CloudTable.ExecuteAsync**.

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
Do tabulky můžete vložit více entit v rámci jedné operace zápisu. Následující příklad kódu vytvoří dva objekty entity ("Jan Novák" a "Ben Smith"), přidá je do objektu **TableBatchOperation** pomocí metody Insert a poté spustí operaci voláním **CloudTable.ExecuteBatchAsync**.

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
Chcete-li zadat dotaz na tabulku pro všechny entity v oddílu, použijte objekt **TableQuery** . Následující příklad kódu určuje filtr pro entity, kde Smith je klíč oddílu. Tento příklad zobrazí pole každé entity z výsledků dotazu z konzoly.

```csharp
// Construct the query operation for all customer entities where PartitionKey="Smith".
TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>()
    .Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

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

return View();
```


## <a name="get-a-single-entity"></a>Získat jednu entitu
Můžete napsat dotaz, který získá jednu konkrétní entitu. Následující kód používá objekt **TableOperation** k určení zákazníka s názvem "Ben Smith". Tato metoda vrátí pouze jednu entitu, nikoli kolekci, a vrácenou hodnotu v **při metody tableresult. Result** je objekt **CustomerEntity** . Určení obou klíčů oddílů a řádků v dotazu je nejrychlejší způsob, jak načíst jednu entitu ze služby **Table** Service.

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
Entitu můžete po nalezení odstranit. Následující kód vyhledá entitu zákazníka s názvem "Robert Smith", a pokud ji najde, odstraní ji.

```csharp
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

## <a name="next-steps"></a>Další kroky
[!INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]