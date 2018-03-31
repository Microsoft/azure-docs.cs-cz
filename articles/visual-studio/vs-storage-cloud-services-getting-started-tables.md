---
title: Začínáme s úložiště tabulek a Visual Studio připojené služby (cloudové služby) | Microsoft Docs
description: Jak začít používat Azure Table storage v projektu cloudové služby v sadě Visual Studio po připojení k účtu úložiště pomocí sady Visual Studio připojené služby
services: storage
documentationcenter: ''
author: ghogen
manager: douge
editor: ''
ms.assetid: a3a11ed8-ba7f-4193-912b-e555f5b72184
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: 68ce9630c5224bd7d9bc4b093ce54dd94140f9e7
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="getting-started-with-azure-table-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Začínáme s Azure table storage a Visual Studio připojené služby (projekty cloudových služeb)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Přehled
Tento článek popisuje, jak začít používat úložiště tabulek Azure v sadě Visual Studio, po vytvoření a účet úložiště Azure v projektu cloudové služby odkazuje pomocí sady Visual Studio **přidat připojení služby** dialogové okno. **Přidat připojení služby** operaci nainstaluje příslušné balíčky NuGet pro přístup k úložišti Azure ve vašem projektu a přidá připojovací řetězec pro účet úložiště v konfiguračních souborech projektu.

Služba úložiště Azure Table umožňuje ukládat velké množství strukturovaná data. Služba je úložištěm dat typu NoSQL, která přijímá ověřených volání z uvnitř i vně cloudu Azure. Tabulky Azure jsou ideální pro ukládání strukturovaných, nerelačních dat.

Abyste mohli začít, musíte nejprve vytvořit tabulku ve vašem účtu úložiště. Ukážeme vám postup vytvoření Azure tabulky v kódu a jak provádět základní tabulky a entity operace, jako je přidání, úprava, čtení a čtení entity tabulky. Ukázky jsou napsané v jazyce C\# kód a použít [Klientská knihovna pro úložiště Microsoft Azure pro .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

**Poznámka:** některé z rozhraní API, které provádějí volání se do úložiště Azure jsou asynchronní. V tématu [asynchronní programování s Async a Await](http://msdn.microsoft.com/library/hh191443.aspx) Další informace. Následující kód předpokládá, že asynchronní programování metody jsou používány.

* V tématu [Začínáme s Azure Table storage pomocí rozhraní .NET](../storage/storage-dotnet-how-to-use-tables.md) Další informace o programu manipulace s tabulky.
* V tématu [úložiště dokumentace](https://azure.microsoft.com/documentation/services/storage/) obecné informace o službě Azure Storage.
* V tématu [cloudové služby dokumentaci](https://azure.microsoft.com/documentation/services/cloud-services/) obecné informace o cloudových služeb Azure.
* V tématu [ASP.NET](http://www.asp.net) Další informace o programování aplikací ASP.NET.

## <a name="access-tables-in-code"></a>Přístup k tabulky v kódu
Chcete-li získat přístup k tabulky v projekty cloudových služeb, zahrnují následující položky pro všechny C# zdrojové soubory, které přístupu k Azure table storage.

1. Zajistěte, aby deklarace oboru názvů v horní části souboru C# zahrnují tyto **pomocí** příkazy.
   
        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Table;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Framework.Logging.LogLevel;
2. Získání **CloudStorageAccount** objekt, který reprezentuje informace o účtu úložiště. Použijte následující kód k získání připojovacího řetězce úložiště a informace o účtu úložiště z konfigurace služby Azure.
   
         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage account name>
         _AzureStorageConnectionString"));
   > [!NOTE]
   > V následující ukázky použijte všechny výše uvedené kódu před kód.
   > 
   > 
3. Získání **CloudTableClient** objekt, který má odkazovat na tabulku objekty v účtu úložiště.
   
         // Create the table client.
         CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
4. Získání **CloudTable** odkaz na objekt, který chcete odkazovat na konkrétní tabulky a entity.
   
        // Get a reference to a table named "peopleTable".
        CloudTable peopleTable = tableClient.GetTableReference("peopleTable");

## <a name="create-a-table-in-code"></a>Vytvoří tabulku v kódu
K vytvoření tabulky Azure, stačí přidat volání **CreateIfNotExistsAsync** do můžete po načtení **CloudTable** objektu, jak je popsáno v části "Přístup k tabulkám v kódu".

    // Create the CloudTable if it does not exist.
    await peopleTable.CreateIfNotExistsAsync();

## <a name="add-an-entity-to-a-table"></a>Přidání entity do tabulky
Když budete chtít do tabulky přidat entitu, vytvořte třídu, která definuje vlastnosti vaší entity. Následující kód definuje třídu entity, která je volána **CustomerEntity** která používá jméno zákazníka jako klíč řádku a příjmení jako klíč oddílu.

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

Operace s tabulkou zahrnující entity se provádějí pomocí **CloudTable** objekt, který jste vytvořili dříve v "Přístup tabulky v kódu". **TableOperation** objekt představuje operaci provést. Následující příklad kódu ukazuje, jak vytvořit **CloudTable** objektu a **CustomerEntity** objektu. Příprava operaci **TableOperation** se vytvoří pro vložení entity zákazníka do tabulky. Nakonec se operace provede voláním **CloudTable.ExecuteAsync**.

    // Create a new customer entity.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    customer1.PhoneNumber = "425-555-0101";

    // Create the TableOperation that inserts the customer entity.
    TableOperation insertOperation = TableOperation.Insert(customer1);

    // Execute the insert operation.
    await peopleTable.ExecuteAsync(insertOperation);


## <a name="insert-a-batch-of-entities"></a>Vložení dávky entit
Do tabulky v rámci jedné zápisu operace můžete vložit více entit. Následující příklad kódu vytvoří dva objekty entity ("Jeff Smith" a "Ben Smith"), se přidají do **TableBatchOperation** metodou vložení objektu, a pak spustí operaci voláním **CloudTable.ExecuteBatchAsync**.

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

## <a name="get-all-of-the-entities-in-a-partition"></a>Získání všech entit v oddílu
Dotaz na tabulku pro všechny entity v oddílu, použijte **TableQuery** objektu. Následující příklad kódu určuje filtr pro entity, kde Smith je klíč oddílu. Tento příklad zobrazí pole každé entity z výsledků dotazu z konzoly.

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


## <a name="get-a-single-entity"></a>Získat jedné entity
Můžete napsat dotaz pro získání jedné konkrétní entity. Následující kód používá **TableOperation** objekt, který chcete zadat zákazníka s názvem 'Ben Smith'. Tato metoda vrátí pouze jednu entitu, místo do kolekce a vrácenou hodnotou při **TableResult.Result** je **CustomerEntity** objektu. Určení klíče oddílu a řádku v dotazu je nejrychlejší způsob, jak načíst jednu entitu ze **tabulky** služby.

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the retrieve operation.
    TableResult retrievedResult = await peopleTable.ExecuteAsync(retrieveOperation);

    // Print the phone number of the result.
    if (retrievedResult.Result != null)
       Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
    else
       Console.WriteLine("The phone number could not be retrieved.");

## <a name="delete-an-entity"></a>Odstranění entity
Entitu můžete odstranit po ji najít. Následující kód vyhledá entitu zákazníka s názvem "Ben Smith", a pokud ho najde, se odstraní.

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

## <a name="next-steps"></a>Další postup
[!INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]

