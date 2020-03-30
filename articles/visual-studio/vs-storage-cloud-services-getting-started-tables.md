---
title: Začínáme s tabulkovým úložištěm pomocí Sady Visual Studio (cloudové služby)
description: Jak začít používat azure table storage v projektu cloudové služby v sadě Visual Studio po připojení k účtu úložiště pomocí připojených služeb Visual Studia
services: storage
author: ghogen
manager: jillfra
ms.assetid: a3a11ed8-ba7f-4193-912b-e555f5b72184
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 5c42d65b5e2c46fcdbe1b0725f2ebce881722db3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72299985"
---
# <a name="getting-started-with-azure-table-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Začínáme s Azure Table Storage a připojenými službami sady Visual Studio (projekty cloudových služeb)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Přehled
Tento článek popisuje, jak začít používat úložiště tabulek Azure ve Visual Studiu po vytvoření nebo odkazování na účet úložiště Azure v projektu cloudových služeb pomocí dialogového okna **Přidat připojené služby** Visual Studia. Operace **Přidat připojené služby** nainstaluje příslušné balíčky NuGet pro přístup k úložišti Azure ve vašem projektu a přidá připojovací řetězec pro účet úložiště do konfiguračních souborů projektu.

Služba úložiště Azure Table umožňuje ukládat velké objemy strukturovaných dat. Služba je úložiště dat NoSQL, které přijímá ověřená volání z cloudu Azure i mimo něj. Tabulky Azure jsou ideální pro ukládání strukturovaných, nerelačních dat.

Chcete-li začít, musíte nejprve vytvořit tabulku v účtu úložiště. Ukážeme vám, jak vytvořit tabulku Azure v kódu a také jak provádět základní operace tabulky a entity, jako je přidávání, úpravy, čtení a čtení entit tabulky. Ukázky jsou zapsány v kódu C\# a používají [klientskou knihovnu Microsoft Azure Storage pro .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

**POZNÁMKA:** Některá úložiště API, která provádějí volání úložiště Azure, jsou asynchronní. Další informace naleznete [v tématu Asynchronní programování s Async a Await.](https://msdn.microsoft.com/library/hh191443.aspx) Níže uvedený kód předpokládá asynchronní programovací metody jsou používány.

* Další informace o programové manipulaci s tabulkami najdete v tématu [Začínáme s úložištěm Azure Table pomocí rozhraní .NET.](../storage/storage-dotnet-how-to-use-tables.md)
* Obecné informace o Azure Storage najdete v [dokumentaci](https://azure.microsoft.com/documentation/services/storage/) k úložišti.
* Obecné informace o cloudových službách Azure najdete v dokumentaci ke [cloudovým službám.](https://azure.microsoft.com/documentation/services/cloud-services/)
* Další informace o programování aplikací ASP.NET naleznete [v ASP.NET.](https://www.asp.net)

## <a name="access-tables-in-code"></a>Přístup k tabulkám v kódu
Chcete-li získat přístup k tabulkám v projektech cloudových služeb, musíte zahrnout následující položky do všech zdrojových souborů Jazyka C#, které přistupují k úložišti tabulek Azure.

1. Ujistěte se, že deklarace oboru názvů v horní části souboru C# obsahují tyto **příkazy pomocí.**
   
        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Table;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Framework.Logging.LogLevel;
2. Získejte objekt **CloudStorageAccount,** který představuje informace o vašem účtu úložiště. Pomocí následujícího kódu získáte informace o připojovacím řetězci úložiště a účtu úložiště z konfigurace služby Azure.
   
         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage account name>
         _AzureStorageConnectionString"));
   > [!NOTE]
   > Použijte všechny výše uvedené kód před kódem v následujících ukázkách.
   > 
   > 
3. Získejte objekt **CloudTableClient,** který bude odkazovat na objekty tabulky v účtu úložiště.
   
         // Create the table client.
         CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
4. Získejte referenční objekt **CloudTable,** který odkazuje na konkrétní tabulku a entity.
   
        // Get a reference to a table named "peopleTable".
        CloudTable peopleTable = tableClient.GetTableReference("peopleTable");

## <a name="create-a-table-in-code"></a>Vytvoření tabulky v kódu
Chcete-li vytvořit tabulku Azure, stačí přidat volání **CreateIfNotExistsAsync** po získání **objektu CloudTable,** jak je popsáno v části "Přístup tabulky v kódu".

    // Create the CloudTable if it does not exist.
    await peopleTable.CreateIfNotExistsAsync();

## <a name="add-an-entity-to-a-table"></a>Přidání entity do tabulky
Když budete chtít do tabulky přidat entitu, vytvořte třídu, která definuje vlastnosti vaší entity. Následující kód definuje třídu entity nazvanou **CustomerEntity,** která používá křestní jméno zákazníka jako klíč řádku a příjmení jako klíč oddílu.

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

Operace tabulky zahrnující entity se provádějí pomocí objektu **CloudTable,** který jste vytvořili dříve v části Přístupové tabulky v kódu. Objekt **TableOperation** představuje operaci, která má být provedena. Následující příklad kódu ukazuje, jak vytvořit objekt **CloudTable** a objekt **CustomerEntity.** Pro přípravu operace je vytvořena **operace TableOperation** pro vložení entity zákazníka do tabulky. Nakonec je operace provedena voláním **CloudTable.ExecuteAsync**.

    // Create a new customer entity.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    customer1.PhoneNumber = "425-555-0101";

    // Create the TableOperation that inserts the customer entity.
    TableOperation insertOperation = TableOperation.Insert(customer1);

    // Execute the insert operation.
    await peopleTable.ExecuteAsync(insertOperation);


## <a name="insert-a-batch-of-entities"></a>Vložení dávky entit
Do tabulky můžete vložit více entit v rámci jedné operace zápisu. Následující příklad kódu vytvoří dva objekty entity ("Jeff Smith" a "Ben Smith"), přidá je do objektu **TableBatchOperation** pomocí metody Insert a poté spustí operaci voláním **CloudTable.ExecuteBatchAsync**.

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
Chcete-li zadat dotaz na tabulku pro všechny entity v oddílu, použijte objekt **TableQuery.** Následující příklad kódu určuje filtr pro entity, kde Smith je klíč oddílu. Tento příklad zobrazí pole každé entity z výsledků dotazu z konzoly.

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


## <a name="get-a-single-entity"></a>Získání jedné entity
Můžete napsat dotaz získat jednu konkrétní entitu. Následující kód používá **Objekt TableOperation** k určení zákazníka s názvem "Ben Smith". Tato metoda vrátí pouze jednu entitu, nikoli kolekci a vrácená hodnota v **TableResult.Result** je **Objekt CustomerEntity.** Zadání klíče oddílu a řádku v dotazu je nejrychlejší způsob, jak načíst jednu entitu ze **služby Table.**

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
Entitu můžete odstranit poté, co ji najdete. Následující kód vyhledá entitu zákazníka s názvem "Ben Novák", a pokud ji najde, odstraní ji.

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

## <a name="next-steps"></a>Další kroky
[!INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]

