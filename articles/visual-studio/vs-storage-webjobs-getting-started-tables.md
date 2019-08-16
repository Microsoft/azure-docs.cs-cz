---
title: Začínáme se službou Azure Storage a připojenými službami sady Visual Studio (projekty WebJob)
description: Jak začít používat službu Azure Table Storage v Azure WebJobs projektu v aplikaci Visual Studio po připojení k účtu úložiště pomocí připojených služeb sady Visual Studio
services: storage
author: ghogen
manager: jillfra
ms.assetid: 061a6c46-0592-4e5d-aced-ab7498481cde
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: 8875f680c8bb83c2375d6fe767f376cbb35d5a0a
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2019
ms.locfileid: "69510668"
---
# <a name="getting-started-with-azure-storage-azure-webjob-projects"></a>Začínáme s Azure Storage (projekty WebJob Azure)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Přehled
Tento článek obsahuje C# ukázky kódu, které ukazují, jak používat sadu Azure WebJobs SDK verze 1. x se službou Azure Table Storage. Ukázky kódu používají [sadu WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki) verze 1. x.

Služba Azure Table Storage umožňuje ukládat velké objemy strukturovaných dat. Tato služba je úložiště dat typu NoSQL, která přijímá ověřených volání z uvnitř i mimo Azure cloud. Jsou ideální pro ukládání strukturovaných, nerelačních dat tabulky Azure.  Další informace najdete v tématu Začínáme [s Azure Table Storage pomocí rozhraní .NET](../cosmos-db/tutorial-develop-table-dotnet.md#create-a-table) .

Některé fragmenty kódu ukazují atribut **tabulky** používané ve funkcích, které jsou volány ručně, to znamená, že nepoužívají jeden z atributů triggeru.

## <a name="how-to-add-entities-to-a-table"></a>Postup přidání entit do tabulky
Chcete-li přidat entity do tabulky, použijte atribut **tabulky** s parametrem **ICollector\<t >** nebo **IAsyncCollector\<t >** , kde **T** Určuje schéma entit, které chcete přidat. Konstruktor atributu přebírá řetězcový parametr, který určuje název tabulky.

Následující ukázka kódu přidá entity **osob** do tabulky s názvem příchozí příchozí *přenosy*.

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

Typ, který používáte s **ICollector** , je obvykle odvozen z **TableEntity** nebo implementuje **ITableEntity**, ale není nutné. Jedna z následujících tříd **Person** pracuje s kódem zobrazeným v předchozí metodě příchozího přenosu dat.

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

Pokud chcete pracovat přímo s rozhraním API služby Azure Storage, můžete do signatury metody přidat parametr **CloudStorageAccount** .

## <a name="real-time-monitoring"></a>Sledování v reálném čase
Vzhledem k tomu, že funkce příchozího přenosu dat často zpracovávají velké objemy dat, řídicí panel sady WebJobs SDK poskytuje data monitorování v reálném čase. V části **protokol vyvolání** se dozvíte, jestli je funkce pořád spuštěná.

![Spuštěná funkce příchozího přenosu dat](./media/vs-storage-webjobs-getting-started-tables/ingressrunning.png)

Na stránce **Podrobnosti o vyvolání** se zobrazí průběh funkce (počet zapsaných entit), který je spuštěný, a poskytuje příležitost k jejímu přerušení.

![Spuštěná funkce příchozího přenosu dat](./media/vs-storage-webjobs-getting-started-tables/ingressprogress.png)

Po dokončení funkce se na stránce **Podrobnosti o vyvolání** zobrazí počet zapsaných řádků.

![Funkce příchozího přenosu byla dokončena](./media/vs-storage-webjobs-getting-started-tables/ingresssuccess.png)

## <a name="how-to-read-multiple-entities-from-a-table"></a>Čtení více entit z tabulky
Chcete-li číst tabulku, použijte **atribut tabulky** s parametrem **IQueryable\<T >** , kde type **T** je odvozen z **TableEntity** nebo implementuje **ITableEntity**.

Následující ukázka kódu čte a zapisuje všechny řádky z tabulky příchozího přenosu dat:

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

### <a name="how-to-read-a-single-entity-from-a-table"></a>Jak načíst jednu entitu z tabulky
K dispozici je konstruktor atributu **tabulky** se dvěma dalšími parametry, které umožňují určit klíč oddílu a klíč řádku, pokud chcete vytvořit propojení s jednou tabulkovou entitou.

Následující ukázka kódu přečte řádek tabulky pro entitu **Person** na základě hodnot klíče oddílu a klíče řádku přijaté ve zprávě fronty:  

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


Třída **Person** v tomto příkladu nemusí implementovat **ITableEntity**.

## <a name="how-to-use-the-net-storage-api-directly-to-work-with-a-table"></a>Jak používat rozhraní API pro úložiště .NET přímo pro práci s tabulkou
Můžete také použít atribut **Table** s objektem **cloudu** pro větší flexibilitu při práci s tabulkou.

Následující ukázka kódu pomocí objektu **cloudu** přidá jednu entitu do tabulky příchozího přenosu dat.

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

Další informace o používání objektu cloudu najdete v tématu Začínáme [s úložištěm Azure Table pomocí rozhraní .NET](../storage/storage-dotnet-how-to-use-tables.md).

## <a name="related-topics-covered-by-the-queues-how-to-article"></a>Související témata, která jsou popsaná v článku s postupy pro fronty
Informace o tom, jak zpracovat zpracování tabulky aktivované zprávami ve frontě nebo ve scénářích služby WebJobs SDK, které nejsou specifické pro zpracování tabulek, najdete v tématu [Začínáme s Azure Queue Storage a připojenými službami sady Visual Studio (projekty WebJob)](../storage/vs-storage-webjobs-getting-started-queues.md).

## <a name="next-steps"></a>Další postup
V tomto článku jsou uvedené ukázky kódu, které ukazují, jak zpracovávat běžné scénáře pro práci s tabulkami Azure. Další informace o tom, jak používat Azure WebJobs a sadu WebJobs SDK, najdete v [dokumentaci k prostředkům Azure WebJobs](https://go.microsoft.com/fwlink/?linkid=390226).

