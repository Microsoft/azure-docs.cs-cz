---
title: Začínáme s table storage a Visual Studio připojené služby (cloudové služby) | Dokumentace Microsoftu
description: Jak začít používat Azure Table storage v projektu cloudové služby v sadě Visual Studio po připojení k účtu úložiště pomocí sady Visual Studio připojené služby
services: storage
author: ghogen
manager: douge
ms.assetid: a3a11ed8-ba7f-4193-912b-e555f5b72184
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: f1e663463ff91da887a4afaebde4b2257347d4f4
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/18/2018
ms.locfileid: "42055049"
---
# <a name="getting-started-with-azure-table-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Začínáme s Azure table storage a Visual Studio připojené služby (projekty cloudových služeb)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Přehled
Tento článek popisuje, jak začít používat úložiště tabulek v Azure v sadě Visual Studio po odkazovat účtu služby Azure storage v projektu cloudové služby pomocí sady Visual Studio nebo vytvořili **přidání připojené služby** dialogového okna. **Přidání připojené služby** operace nainstaluje příslušné balíčky NuGet pro přístup k Azure storage ve vašem projektu a přidá připojovací řetězec pro účet úložiště pro konfigurační soubory projektu.

Služba Azure Table storage umožňuje ukládat velké objemy strukturovaných dat. Tato služba je úložiště dat typu NoSQL, která přijímá ověřených volání z uvnitř i mimo Azure cloud. Tabulky Azure jsou ideální pro ukládání strukturovaných, nerelačních dat.

Abyste mohli začít, musíte nejprve vytvořit tabulku v účtu úložiště. Vám ukážeme, jak vytvořit tabulku Azure v kódu a jak provádět základní tabulky a entity operace, jako jsou přidání, úprava, čtení a čtení tabulkové entity. Ukázky jsou napsané v jazyce C\# kód a použít [Klientská knihovna pro Microsoft Azure Storage pro .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

**Poznámka:** některá rozhraní API, provádět volání si do služby Azure storage jsou asynchronní. Zobrazit [asynchronní programování s Async a Await](http://msdn.microsoft.com/library/hh191443.aspx) Další informace. Následující kód předpokládá, že se používají asynchronní programovací metody.

* Zobrazit [Začínáme s Azure Table storage pomocí .NET](../storage/storage-dotnet-how-to-use-tables.md) Další informace o programově práci s tabulkami.
* Zobrazit [dokumentace ke službě Storage](https://azure.microsoft.com/documentation/services/storage/) obecné informace o službě Azure Storage.
* Zobrazit [dokumentace ke službě Cloud Services](https://azure.microsoft.com/documentation/services/cloud-services/) obecné informace o cloudových službách Azure.
* Zobrazit [ASP.NET](http://www.asp.net) Další informace o programování aplikací ASP.NET.

## <a name="access-tables-in-code"></a>Tabulky přístup v kódu
Chcete-li k tabulkám v projekty cloudových služeb, zahrnují následující položky, které chcete žádné C# zdrojové soubory, které přístup k úložišti tabulek v Azure.

1. Ujistěte se, že ty pak uvádějte deklarace oboru názvů v horní části souboru jazyka C# **pomocí** příkazy.
   
        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Table;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Framework.Logging.LogLevel;
2. Získání **CloudStorageAccount** objekt reprezentující informace o vašem účtu úložiště. Použijte následující kód k získání připojovacího řetězce k úložišti a informace o účtu úložiště z konfigurace služby Azure.
   
         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage account name>
         _AzureStorageConnectionString"));
   > [!NOTE]
   > Použijte všechny výše uvedený kód před kód v následujících ukázkách.
   > 
   > 
3. Získání **CloudTableClient** objekt neodkazovali na objekty Tabulka ve vašem účtu úložiště.
   
         // Create the table client.
         CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
4. Získání **CloudTable** referenční objekt odkazování na určité tabulky a entity.
   
        // Get a reference to a table named "peopleTable".
        CloudTable peopleTable = tableClient.GetTableReference("peopleTable");

## <a name="create-a-table-in-code"></a>Vytvoření tabulky v kódu
Vytvoření tabulek v Azure, stačí přidat volání **CreateIfNotExistsAsync** k po získání **CloudTable** objektu podle popisu v části "Přístup k tabulkám v kódu".

    // Create the CloudTable if it does not exist.
    await peopleTable.CreateIfNotExistsAsync();

## <a name="add-an-entity-to-a-table"></a>Přidání entity do tabulky
Když budete chtít do tabulky přidat entitu, vytvořte třídu, která definuje vlastnosti vaší entity. Následující kód definuje třídu entity, která je volána **CustomerEntity** , která používá jméno zákazníka jako klíč řádku a příjmení jako klíč oddílu.

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

Operace s tabulkou zahrnující entity se provádí pomocí **CloudTable** objekt, který jste vytvořili dříve v "Přístup tabulky v kódu." **TableOperation** objekt představuje operaci provést. Následující příklad kódu ukazuje, jak vytvořit **CloudTable** objektu a **CustomerEntity** objektu. Přípravy na operaci **TableOperation** se vytvoří pro vložení entity zákazníka do tabulky. Nakonec se operace provede voláním **CloudTable.ExecuteAsync**.

    // Create a new customer entity.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    customer1.PhoneNumber = "425-555-0101";

    // Create the TableOperation that inserts the customer entity.
    TableOperation insertOperation = TableOperation.Insert(customer1);

    // Execute the insert operation.
    await peopleTable.ExecuteAsync(insertOperation);


## <a name="insert-a-batch-of-entities"></a>Vložení dávky entit
Do tabulky v rámci jedné zápisu operace můžete vložit více entit. Následující příklad kódu vytvoří dva objekty entity ("Jan Macek" a "Ben Smith"), se přidají do **TableBatchOperation** pomocí metody Insert a potom spustí operaci voláním  **CloudTable.ExecuteBatchAsync**.

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
Chcete-li dotaz na tabulku pro všechny entity v oddílu, použijte **TableQuery** objektu. Následující příklad kódu určuje filtr pro entity, kde Smith je klíč oddílu. Tento příklad zobrazí pole každé entity z výsledků dotazu z konzoly.

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


## <a name="get-a-single-entity"></a>Získat jednu entitu
Můžete napsat dotaz pro získání jedné konkrétní entity. Následující kód používá **TableOperation** objektu k určení zákazníka s názvem "Ben Smith". Tato metoda vrátí pouze jednu entitu, nikoli kolekci a vrácenou hodnotou při **TableResult.Result** je **CustomerEntity** objektu. Zadání klíče oddílu a řádku v dotazu je nejrychlejší způsob, jak načíst jednu entitu ze **tabulky** služby.

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
Entitu můžete po vás odstranit. Následující kód hledá entita zákazník s názvem "Ben Smith", a pokud ho najde, odstraní jej.

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

