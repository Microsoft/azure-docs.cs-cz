---
title: Začínáme s Azure storage a Visual Studio připojené služeb (webové úlohy projekty)
description: Jak začít používat Azure Table storage v Azure WebJobs projektu v sadě Visual Studio po připojení k účtu úložiště pomocí sady Visual Studio připojené služby
services: storage
documentationcenter: ''
author: ghogen
manager: douge
editor: ''
ms.assetid: 061a6c46-0592-4e5d-aced-ab7498481cde
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: 75ca4914fe60ec6f5c50e4942ad6d5cc7a6fe98b
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="getting-started-with-azure-storage-azure-webjob-projects"></a>Začínáme s Azure Storage (webová úloha Azure projekty)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Přehled
Tento článek obsahuje C# ukázek kódu, které ukazují ukazují, jak používat Azure WebJobs SDK verze 1.x s služby úložiště Azure table. Kód – ukázky použití [WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki) verze 1.x.

Služba úložiště Azure Table umožňuje ukládat velké množství strukturovaná data. Služba je úložištěm dat typu NoSQL, která přijímá ověřených volání z uvnitř i vně cloudu Azure. Tabulky Azure jsou ideální pro ukládání strukturovaných, nerelačních dat.  V tématu [Začínáme s Azure Table storage pomocí rozhraní .NET](../cosmos-db/table-storage-how-to-use-dotnet.md#create-a-table) Další informace.

Některé zobrazit fragmenty kódu **tabulky** atribut používaných ve funkcích, které se nazývají ručně, tedy ne prostřednictvím jeden z atributů aktivační události.

## <a name="how-to-add-entities-to-a-table"></a>Postup přidání entity do tabulky
K přidání entity do tabulky, použijte **tabulky** atribut s **ICollector<T>**  nebo **IAsyncCollector<T>**  parametr kde **T** Určuje schéma entity, které chcete přidat. Konstruktoru atributu přijímá řetězcový parametr, který určuje název tabulky.

Následující ukázka kódu přidá **osoba** entity do tabulky s názvem *příjem příchozích dat*.

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

Obvykle typ můžete používat s **ICollector** je odvozena z **TableEntity** nebo implementuje **ITableEntity**, ale nemusí to. Z následujících **osoba** třídy práce s kódem zobrazeným v předchozím **příjem příchozích dat** metoda.

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

Pokud chcete pracovat přímo s úložištěm Azure API, můžete přidat **CloudStorageAccount** parametru podpis metody.

## <a name="real-time-monitoring"></a>Sledování v reálném čase
Protože funkce příchozí přenos dat často zpracování velkých objemů dat, řídicím panelu WebJobs SDK poskytuje data monitorování v reálném čase. **Volání protokolu** části oznamuje, zda je stále spuštěna funkce.

![Příjem příchozích dat funkci spouštění](./media/vs-storage-webjobs-getting-started-tables/ingressrunning.png)

**Volání podrobnosti** stránky sestavy průběhu funkce (počet entit, které jsou zapsány) je spuštěn a vám dává příležitost k přerušení ho.

![Příjem příchozích dat funkci spouštění](./media/vs-storage-webjobs-getting-started-tables/ingressprogress.png)

Po dokončení funkce **volání podrobnosti** stránky sestavy počet řádků, které jsou zapsány.

![Funkce pro příjem příchozích dat bylo dokončeno](./media/vs-storage-webjobs-getting-started-tables/ingresssuccess.png)

## <a name="how-to-read-multiple-entities-from-a-table"></a>Jak si více entit z tabulky.
Čtení tabulky, použijte **tabulky** atribut s **IQueryable<T>**  parametr kde zadejte **T** je odvozena z **TableEntity** nebo implementuje **ITableEntity**.

Následující ukázka kódu čte a zaznamená všechny řádky z **příjem příchozích dat** tabulky:

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

### <a name="how-to-read-a-single-entity-from-a-table"></a>Informace o načtení jedné entity z tabulky.
Je **tabulky** konstruktoru atributu s dva další parametry, které umožňují zadat klíč oddílu a klíč řádku, pokud chcete vytvořit vazbu k jedné tabulky entity.

Následující ukázka kódu čte pro řádek tabulky **osoba** entit na základě oddílu klíč a řádek hodnot klíče dostali zprávu fronty:  

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

## <a name="how-to-use-the-net-storage-api-directly-to-work-with-a-table"></a>Jak používat rozhraní API .NET úložiště přímo do tabulky
Můžete také **tabulky** atribut s **CloudTable** objekt pro větší flexibilitu při práci s tabulkou.

Následující kód používá ukázka **CloudTable** objekt, který chcete přidat do jedné entity *příjem příchozích dat* tabulky.

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

Další informace o tom, jak používat **CloudTable** objektu, najdete v části [Začínáme s Azure Table storage pomocí rozhraní .NET](../storage/storage-dotnet-how-to-use-tables.md).

## <a name="related-topics-covered-by-the-queues-how-to-article"></a>Související témata předmětem článek s postupy fronty
Informace o způsobu zpracování zpracování tabulky aktivovány zprávu fronty, nebo pro scénáře WebJobs SDK, které nejsou specifické pro zpracování tabulky, najdete v části [Začínáme s Azure Queue storage a Visual Studio připojené služeb (webové úlohy projekty)](../storage/vs-storage-webjobs-getting-started-queues.md).

## <a name="next-steps"></a>Další postup
Tento článek poskytl ukázek kódu, které ukazují, jak zpracovat běžné scénáře pro práci s tabulek Azure. Další informace o tom, jak používat Azure WebJobs a WebJobs SDK najdete v tématu [zdrojů dokumentace Azure WebJobs](http://go.microsoft.com/fwlink/?linkid=390226).

