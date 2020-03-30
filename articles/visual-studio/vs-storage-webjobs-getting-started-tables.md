---
title: Začínáme s úložištěm Azure pomocí Sady Visual Studio (projekty WebJob)
description: Jak začít používat azure table storage v projektu Azure WebJobs v Sadě Visual Studio po připojení k účtu úložiště pomocí připojených služeb Visual Studia
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
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: e4d8299c06bfa5b0f33bff8fa592a2fa549c695c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74707601"
---
# <a name="getting-started-with-azure-storage-azure-webjob-projects"></a>Začínáme s Azure Storage (Azure WebJob Projects)

[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Přehled
Tento článek obsahuje ukázky kódu jazyka C#, které ukazují, jak používat Azure WebJobs SDK verze 1.x se službou úložiště tabulek Azure. Ukázky kódu používají [webjobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki) verze 1.x.

Služba úložiště Azure Table umožňuje ukládat velké objemy strukturovaných dat. Služba je úložiště dat NoSQL, které přijímá ověřená volání z cloudu Azure i mimo něj. Tabulky Azure jsou ideální pro ukládání strukturovaných, nerelačních dat.  Další informace [najdete v tématu Začínáme s úložištěm Azure Table pomocí rozhraní .NET.](../cosmos-db/tutorial-develop-table-dotnet.md#create-a-table)

Některé fragmenty kódu zobrazují atribut **Table** používaný ve funkcích, které se nazývají ručně, to znamená, že nepoužívají jeden z atributů aktivační události.

## <a name="how-to-add-entities-to-a-table"></a>Přidání entit do tabulky

Chcete-li do tabulky přidat entity, použijte atribut **Table** s parametrem **\<ICollector T>** nebo **IAsyncCollector\<T>,** kde **T** určuje schéma entit, které chcete přidat. Konstruktor atributu přebírá parametr řetězce, který určuje název tabulky.

Následující ukázka kódu přidá entity **Person** do tabulky s názvem *Ingress*.

```csharp
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
```

Obvykle typ, který používáte s **ICollector** odvozuje od **TableEntity** nebo implementuje **ITableEntity**, ale nemá. Některá z následujících tříd **Person** pracuje s kódem zobrazeným v předchozí metodě **příchozího přenosu dat.**

```csharp
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
```

Pokud chcete pracovat přímo s rozhraním API úložiště Azure, můžete přidat parametr **CloudStorageAccount** k podpisu metody.

## <a name="real-time-monitoring"></a>Monitorování v reálném čase

Vzhledem k tomu, že funkce příchozího přenosu dat často zpracovávají velké objemy dat, řídicí panel WebJobs SDK poskytuje data monitorování v reálném čase. V části **Protokol vyvolání** se dozvíte, zda je funkce stále spuštěna.

![Spuštěná funkce příchozího přenosu dat](./media/vs-storage-webjobs-getting-started-tables/ingressrunning.png)

Stránka **Podrobnosti vyvolání** hlásí průběh funkce (počet zapsaných entit) při jejím spuštění a poskytuje možnost ji přerušit.

![Spuštěná funkce příchozího přenosu dat](./media/vs-storage-webjobs-getting-started-tables/ingressprogress.png)

Po dokončení funkce zobrazí stránka **Podrobnosti vyvolání** počet zapsaných řádků.

![Funkce příchozího přenosu dat byla dokončena.](./media/vs-storage-webjobs-getting-started-tables/ingresssuccess.png)

## <a name="how-to-read-multiple-entities-from-a-table"></a>Jak číst více entit z tabulky

Chcete-li číst tabulku, použijte atribut **Table** s parametrem **IQueryable\<T>,** kde typ **T** pochází z **TableEntity** nebo implementuje **ITableEntity**.

Následující ukázka kódu čte a zaznamenává všechny řádky z tabulky **Příchozí přenos dat:**

```csharp
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
```

### <a name="how-to-read-a-single-entity-from-a-table"></a>Jak číst jednu entitu z tabulky

Existuje konstruktor **atributu Table** se dvěma dalšími parametry, které umožňují zadat klíč oddílu a klíč řádku, pokud chcete vytvořit vazbu na jednu entitu tabulky.

Následující ukázka kódu přečte řádek tabulky pro entitu **Person** na základě hodnot klíče oddílu a klíče řádku přijatých ve zprávě fronty:

```csharp
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
```

**Person třídy** v tomto příkladu není k implementaci **ITableEntity**.

## <a name="how-to-use-the-net-storage-api-directly-to-work-with-a-table"></a>Použití rozhraní .NET Storage API přímo pro práci s tabulkou

Atribut **Table** s objektem **CloudTable** můžete také použít pro větší flexibilitu při práci s tabulkou.

Následující ukázka kódu používá objekt **CloudTable** k přidání jedné entity do tabulky *Příchozí přenos dat.*

```csharp
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
```

Další informace o použití objektu **CloudTable** najdete v tématu [Začínáme s úložištěm Azure Table pomocí rozhraní .NET](../storage/storage-dotnet-how-to-use-tables.md).

## <a name="related-topics-covered-by-the-queues-how-to-article"></a>Související témata, na která se vztahují fronty návod-na článek

Informace o tom, jak zpracovat zpracování tabulky spuštěné zprávou fronty nebo pro scénáře sady WebJobs SDK, které nejsou specifické pro zpracování tabulek, naleznete [v tématu Začínáme s úložištěm fronty Azure a připojenými službami Visual Studio (Projekty webových úloh).](../storage/vs-storage-webjobs-getting-started-queues.md)

## <a name="next-steps"></a>Další kroky

Tento článek poskytl ukázky kódu, které ukazují, jak zpracovat běžné scénáře pro práci s tabulkami Azure. Další informace o tom, jak používat Azure WebJobs a WebJobs SDK, najdete v [tématu Materiály pro dokumentaci Azure WebJobs](https://go.microsoft.com/fwlink/?linkid=390226).
