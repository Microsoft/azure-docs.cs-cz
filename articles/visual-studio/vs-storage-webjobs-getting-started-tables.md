---
title: Začínáme s Azure storage a Visual Studio připojené služby (webové úlohy projektů)
description: Jak začít používat Azure Table storage v Azure WebJobs projektu v sadě Visual Studio po připojení k účtu úložiště pomocí sady Visual Studio připojené služby
services: storage
author: ghogen
manager: douge
ms.assetid: 061a6c46-0592-4e5d-aced-ab7498481cde
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: a10d354cf819ebaa7e2199d9de0566b3e6bfd1bf
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/18/2018
ms.locfileid: "42057518"
---
# <a name="getting-started-with-azure-storage-azure-webjob-projects"></a>Začínáme s Azure Storage (webová úloha Azure projektů)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Přehled
Tento článek obsahuje ukázky kódu C#, které ukazují, jak pomocí sady Azure WebJobs SDK verze 1.x pomocí služby Azure table storage. Ukázky kódu použijte [sada WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki) verzi 1.x.

Služba Azure Table storage umožňuje ukládat velké objemy strukturovaných dat. Tato služba je úložiště dat typu NoSQL, která přijímá ověřených volání z uvnitř i mimo Azure cloud. Tabulky Azure jsou ideální pro ukládání strukturovaných, nerelačních dat.  Zobrazit [Začínáme s Azure Table storage pomocí .NET](../cosmos-db/table-storage-how-to-use-dotnet.md#create-a-table) Další informace.

Některé z kódu fragmenty kódu ukazují **tabulky** atributu použitého v funkcí, které jsou volány ručně, to znamená, nikoli pomocí jeden z atributů aktivační události.

## <a name="how-to-add-entities-to-a-table"></a>Přidání entity do tabulky
Pro přidání entity do tabulky, použijte **tabulky** atributem **ICollector<T>**  nebo **IAsyncCollector<T>**  parametr kde **T** určuje schématu entity, které chcete přidat. Konstruktor atributu má řetězcový parametr, který určuje název tabulky.

Následující příklad kódu přidá **osoba** entity do tabulky s názvem *příchozího přenosu dat*.

        [NoAutomaticTrigger]
        public static void IngressDemo(
            [Table("Ingress")] ICollector<Person> tableBinding)
        {
            for (int i = 0; i < 100000; i++)
            {
                tableBinding.Add(
                    new Person() {
                        PartitionKey = "Test",
                        RowKey = i.ToString(),
                        Name = "Name" }
                    );
            }
        }

Obvykle typ pomocí **ICollector** je odvozena z **TableEntity** nebo implementuje **ITableEntity**, ale nemusí to. Jednu z následujících **osoba** třídy práce s kódem zobrazeným v předchozím **příchozího přenosu dat** metody.

        public class Person : TableEntity
        {
            public string Name { get; set; }
        }

        public class Person
        {
            public string PartitionKey { get; set; }
            public string RowKey { get; set; }
            public string Name { get; set; }
        }

Pokud budete chtít pracovat přímo s úložištěm Azure API, můžete přidat **CloudStorageAccount** parametr do podpisu metody.

## <a name="real-time-monitoring"></a>Monitorování v reálném čase
Protože funkce příchozího přenosu dat často zpracovávají velké objemy dat, sada WebJobs SDK řídicí panel poskytuje monitorování dat v reálném čase. **Protokol volání** části zjistíte, zda funkce stále běží.

![Spuštění funkce příchozího přenosu dat](./media/vs-storage-webjobs-getting-started-tables/ingressrunning.png)

**Podrobnosti volání** stránka sestavy průběh zavináčem (počet entit, které jsou napsané) spuštěn a vám dává možnost ho přerušit.

![Spuštění funkce příchozího přenosu dat](./media/vs-storage-webjobs-getting-started-tables/ingressprogress.png)

Po dokončení funkce **podrobnosti volání** stránka sestavy počet zapsaných řádků.

![Dokončené funkce příchozího přenosu dat](./media/vs-storage-webjobs-getting-started-tables/ingresssuccess.png)

## <a name="how-to-read-multiple-entities-from-a-table"></a>Jak číst více entity z tabulky
Další tabulky, použijte **tabulky** atributem **IQueryable<T>**  parametr typem **T** je odvozena z **TableEntity**nebo implementuje **ITableEntity**.

Následující vzorový kód přečte a protokoluje všechny řádky z **příchozího přenosu dat** tabulky:

        public static void ReadTable(
            [Table("Ingress")] IQueryable<Person> tableBinding,
            TextWriter logger)
        {
            var query = from p in tableBinding select p;
            foreach (Person person in query)
            {
                logger.WriteLine("PK:{0}, RK:{1}, Name:{2}",
                    person.PartitionKey, person.RowKey, person.Name);
            }
        }

### <a name="how-to-read-a-single-entity-from-a-table"></a>Informace o načtení jedné entity z tabulky
Je **tabulky** konstruktor atributu se dvěma další parametry, které vám umožní zadat klíč oddílu a klíčem řádku, pokud chcete vytvořit vazbu na jedné tabulky entitu.

Následující vzorový kód přečte řádek tabulky pro **osoba** entity na základě oddílu klíč a řádek hodnot klíče doručení zpráv fronty:  

        public static void ReadTableEntity(
            [QueueTrigger("inputqueue")] Person personInQueue,
            [Table("persontable","{PartitionKey}", "{RowKey}")] Person personInTable,
            TextWriter logger)
        {
            if (personInTable == null)
            {
                logger.WriteLine("Person not found: PK:{0}, RK:{1}",
                        personInQueue.PartitionKey, personInQueue.RowKey);
            }
            else
            {
                logger.WriteLine("Person found: PK:{0}, RK:{1}, Name:{2}",
                        personInTable.PartitionKey, personInTable.RowKey, personInTable.Name);
            }
        }


**Osoba** třídy v tomto příkladu není nutné implementovat **ITableEntity**.

## <a name="how-to-use-the-net-storage-api-directly-to-work-with-a-table"></a>Jak používat rozhraní API úložiště .NET přímo pro práci s tabulkou
Můžete také použít **tabulky** atributem **CloudTable** objekt pro větší flexibilitu při práci s tabulkou.

Následující kód používá ukázkový **CloudTable** objektu, který chcete přidat do jedné entity *příchozího přenosu dat* tabulky.

        public static void UseStorageAPI(
            [Table("Ingress")] CloudTable tableBinding,
            TextWriter logger)
        {
            var person = new Person()
                {
                    PartitionKey = "Test",
                    RowKey = "100",
                    Name = "Name"
                };
            TableOperation insertOperation = TableOperation.Insert(person);
            tableBinding.Execute(insertOperation);
        }

Další informace o tom, jak používat **CloudTable** objektu, najdete v článku [Začínáme s Azure Table storage pomocí .NET](../storage/storage-dotnet-how-to-use-tables.md).

## <a name="related-topics-covered-by-the-queues-how-to-article"></a>Související témata s postupy článku fronty
Informace o tom, jak zpracovat zpracování tabulky aktivované zpráv fronty, nebo sada WebJobs SDK scénáře nejsou specifické pro zpracování tabulky, projděte si téma [Začínáme se službou Azure Queue storage a Visual Studio připojené služby (webové úlohy projektů) ](../storage/vs-storage-webjobs-getting-started-queues.md).

## <a name="next-steps"></a>Další postup
Tento článek poskytuje ukázek kódu, které ukazují, jak zvládnout běžné scénáře pro práci s tabulky Azure. Další informace o tom, jak používat Azure WebJobs a sada WebJobs SDK najdete v tématu [prostředků dokumentace Azure WebJobs](http://go.microsoft.com/fwlink/?linkid=390226).

