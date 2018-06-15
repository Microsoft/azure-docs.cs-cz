---
title: Pomocí závislosti úkolů spouštět úlohy, které jsou založeny na dokončení jiné úlohy – Azure Batch | Microsoft Docs
description: Vytvoření úlohy, které jsou závislé na dokončení jiné úlohy pro zpracování prostředí MapReduce style a podobné velkých objemů dat úlohy v Azure Batch.
services: batch
documentationcenter: .net
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: b8d12db5-ca30-4c7d-993a-a05af9257210
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 05/22/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ba85e075c39251b0b3d7c4b8bc3f8d53a1afadf7
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
ms.locfileid: "30316813"
---
# <a name="create-task-dependencies-to-run-tasks-that-depend-on-other-tasks"></a>Vytvoření závislosti úkolů ke spouštění úloh, které závisí na jiné úlohy

Můžete definovat závislosti úkolů pro spuštění úloh nebo sadu úloh, až po dokončení úlohy nadřazené. Některé scénáře, kde jsou užitečné závislosti úkolů patří:

* Úlohy MapReduce-style v cloudu.
* Úlohy, jejichž úloh zpracování dat může být vyjádřený jako necyklicky (DAG).
* Zahájit vykreslování před a po vykreslení procesy, kde musí každý úkol dokončit před dalším úkolem.
* Žádné jiné úlohy, ve kterém podřízené úlohy závisí na výstup úlohy nadřazený.

Pomocí závislosti úkolů dávky můžete vytvořit úlohy, které jsou naplánovány pro spuštění na výpočetních uzlech po dokončení jedné nebo více úloh nadřazené. Můžete například vytvořit úlohu, která vykreslí každý snímek 3D film samostatný, paralelní úlohy. Konečné úloh – "sloučení úlohu"--sloučení vykreslené rámce do dokončení film až poté, co byl úspěšně vykreslen všechny snímky.

Ve výchozím nastavení závislé úlohy jsou naplánovány pro spuštění až po úspěšném dokončení úlohy nadřazené. Můžete zadat akce závislostí přepsat výchozí chování a spouštět úlohy, pokud úloha nadřazené selže. Najdete v článku [závislostí akcí](#dependency-actions) podrobnosti.  

Můžete vytvořit úlohy, které jsou závislé na dalších úloh v relaci 1: 1 nebo 1 n. Můžete také vytvořit rozsah závislostí, kde úkol závisí na dokončení úloh v zadaném rozsahu úlohy ID skupiny. Můžete kombinovat těchto tří základních scénářů pro vytvoření relace m: n.

## <a name="task-dependencies-with-batch-net"></a>Závislosti úkolů pomocí rozhraní Batch .NET
V tomto článku probereme, jak nakonfigurovat závislosti úkolů pomocí [Batch .NET] [ net_msdn] knihovny. Nám nejdřív ukazují, jak k [povolit závislosti úkolů](#enable-task-dependencies) na úlohách a pak ukazují, jak [úlohu nakonfigurovat závislosti](#create-dependent-tasks). Můžeme také popisují, jak určení závislostí akce ke spouštění závislé úlohy, pokud nadřazená selže. Nakonec probereme [scénáře závislostí](#dependency-scenarios) podporující Batch.

## <a name="enable-task-dependencies"></a>Povolit závislosti úkolů
Používat závislosti úkolů v aplikaci Batch, musíte nejdřív nakonfigurovat úlohy pomocí závislosti úkolů. V Batch .NET, povolte ji na vaše [CloudJob] [ net_cloudjob] nastavením jeho [UsesTaskDependencies] [ net_usestaskdependencies] vlastnost `true`:

```csharp
CloudJob unboundJob = batchClient.JobOperations.CreateJob( "job001",
    new PoolInformation { PoolId = "pool001" });

// IMPORTANT: This is REQUIRED for using task dependencies.
unboundJob.UsesTaskDependencies = true;
```

V předchozím fragmentu kódu je "batchClient" instance [BatchClient] [ net_batchclient] třídy.

## <a name="create-dependent-tasks"></a>Vytvoření závislé úlohy
Chcete-li vytvořit úlohu, která závisí na dokončení jedné nebo více úloh nadřazené, můžete zadat, že úloha "závisí na" Další úlohy. V Batch .NET, nakonfigurujte [CloudTask][net_cloudtask].[ DependsOn] [ net_dependson] vlastnost s instancí [TaskDependencies] [ net_taskdependencies] třídy:

```csharp
// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

Tento fragment kódu vytvoří závislé úlohy s ID úlohy "Květy". Úloha "Květy" závisí na úkoly "Deště" a "Sun". Úloha "květy" bude naplánovat na spuštění na výpočetním uzlu až po úlohy, které byly úspěšně dokončeny "Deště" a "Sun".

> [!NOTE]
> Úloha se považuje za úspěšně dokončit, pokud je v **Dokončit** stavu a jeho **ukončovací kód** je `0`. V Batch .NET, to znamená [CloudTask][net_cloudtask].[ Stav] [ net_taskstate] hodnota vlastnosti `Completed` a CloudTask [TaskExecutionInformation][net_taskexecutioninformation].[ ExitCode] [ net_exitcode] hodnota vlastnosti je `0`.
> 
> 

## <a name="dependency-scenarios"></a>Scénáře závislostí
Existují tři scénáře závislostí základní úlohy, které můžete použít ve službě Azure Batch: 1: 1, 1 n a ID úkolu rozsah závislostí. Ty mohou být kombinovány zajistit scénáři čtvrtý m: n.

| Scénář&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Příklad: |  |
|:---:| --- | --- |
|  [One-to-one](#one-to-one) |*Úkolb* závisí na *Úkolua* <p/> *Úkolb* nebude naplánovaných pro spuštění až *Úkolua* byla úspěšně dokončena |![Diagram: Úloha 1: 1 závislostí][1] |
|  [One-to-many](#one-to-many) |*ÚkolC* závisí na *úkoluA* a *úkoluB* <p/> *Úkolc* nebude naplánovaných pro spuštění, dokud *Úkolua* a *Úkolb* byly úspěšně dokončeny |![Diagram: závislost na více úkolů][2] |
|  [Rozsah ID úkolu](#task-id-range) |*Úkold* závisí na celou řadu úloh <p/> *Úkold* nebude naplánovaných pro spuštění až úlohy s ID *1* prostřednictvím *10* byly úspěšně dokončeny |![Diagram: Úloha id rozsah závislostí][3] |

> [!TIP]
> Můžete vytvořit **m: n** relace, například kde úlohy C, D, E a F každý závisí na úlohy A a B. To je užitečné, například v paralelizovaná málo předběžného zpracování scénáře kde podřízené úkoly závisí na výstupu několika nadřazeného úloh.
> 
> V příkladech v této části závislé úlohy spustí až po nadřazené úlohy úspěšně dokončit. Toto chování je výchozí chování pro úlohu závislé. Závislé úlohu lze spustit po nadřazené úlohy nepovede zadáním závislostí akce přepsat výchozí chování. Najdete v článku [závislostí akcí](#dependency-actions) podrobnosti.

### <a name="one-to-one"></a>1: 1
V relaci úkol závisí na úspěšné dokončení úlohy jednou nadřazenou položkou. Pokud chcete vytvořit závislost, uveďte ID jedné úlohy pro [TaskDependencies][net_taskdependencies].[ OnId] [ net_onid] statickou metodu při naplňování [DependsOn] [ net_dependson] vlastnost [CloudTask][net_cloudtask].

```csharp
// Task 'taskA' doesn't depend on any other tasks
new CloudTask("taskA", "cmd.exe /c echo taskA"),

// Task 'taskB' depends on completion of task 'taskA'
new CloudTask("taskB", "cmd.exe /c echo taskB")
{
    DependsOn = TaskDependencies.OnId("taskA")
},
```

### <a name="one-to-many"></a>Jeden mnoho
Ve vztahu k více úkol závisí na dokončení více úkolů nadřazené. Pokud chcete vytvořit závislost, shrnují ID úloh na [TaskDependencies][net_taskdependencies].[ OnIds] [ net_onids] statickou metodu při naplňování [DependsOn] [ net_dependson] vlastnost [CloudTask][net_cloudtask].

```csharp
// 'Rain' and 'Sun' don't depend on any other tasks
new CloudTask("Rain", "cmd.exe /c echo Rain"),
new CloudTask("Sun", "cmd.exe /c echo Sun"),

// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
``` 

### <a name="task-id-range"></a>Rozsah ID úkolu
V závislosti na škále nadřazené úlohy, úkol závisí dokončení úlohy, jejichž identifikátory ležet v rozsahu.
Pokud chcete vytvořit závislost, zadejte první a poslední ID úloh v rozsahu do [TaskDependencies][net_taskdependencies].[ OnIdRange] [ net_onidrange] statickou metodu při naplňování [DependsOn] [ net_dependson] vlastnost [CloudTask][net_cloudtask].

> [!IMPORTANT]
> Když použijete rozsahy ID úloh pro závislosti, ID úlohy v rozsahu *musí* být řetězcové vyjádření hodnot celé číslo.
> 
> Každý úkol v rozsahu musí splňovat závislost, nelze úspěšně dokončit nebo dokončení s chybou, který je namapovaný na závislostí akcí nastavenou na hodnotu **Satisfy**. Najdete v článku [závislostí akcí](#dependency-actions) podrobnosti.
>
>

```csharp
// Tasks 1, 2, and 3 don't depend on any other tasks. Because
// we will be using them for a task range dependency, we must
// specify string representations of integers as their ids.
new CloudTask("1", "cmd.exe /c echo 1"),
new CloudTask("2", "cmd.exe /c echo 2"),
new CloudTask("3", "cmd.exe /c echo 3"),

// Task 4 depends on a range of tasks, 1 through 3
new CloudTask("4", "cmd.exe /c echo 4")
{
    // To use a range of tasks, their ids must be integer values.
    // Note that we pass integers as parameters to TaskIdRange,
    // but their ids (above) are string representations of the ids.
    DependsOn = TaskDependencies.OnIdRange(1, 3)
},
```

## <a name="dependency-actions"></a>Akce závislostí

Ve výchozím nastavení spustí závislé úlohy nebo sadu úloh až po úspěšném dokončení úlohy nadřazené. V některých scénářích můžete spustit závislé úlohy i v případě, že úloha nadřazené nezdaří. Výchozí chování můžete přepsat zadáním závislostí akce. Akce závislostí Určuje, jestli závislé úlohy je vhodné spustit, na základě úspěch nebo selhání úlohy nadřazené. 

Předpokládejme například, že závislé úloha čeká na data z dokončení nadřazený úkol. Pokud nadřazený úkol selže, může být závislé úlohy stále moci spouštět pomocí starší data. Akce závislostí v tomto případě můžete zadat, že závislé úkol je vhodné spustit bez ohledu na selhání nadřazené úlohy.

Akce závislostí je založena na ukončovací podmínky pro nadřazené úlohy. Můžete zadat akce závislostí pro některý z následujících podmínek ukončení; pro platformu .NET, najdete v článku [ExitConditions] [ net_exitconditions] třída podrobnosti:

- Když dojde k chybě s předem zpracování.
- Když dojde k chybě při odesílání souboru. Pokud úloha ukončení s ukončovacím kódem, která byla zadána prostřednictvím **exitCodes** nebo **exitCodeRanges**, a pak mají přednost komunikaci soubor nahrát chyba akci určenou v něm ukončovací kód.
- Při ukončení úlohy s ukončovacím kódem definované **ExitCodes** vlastnost.
- Při ukončení úlohy s ukončovacím kódem, která spadá do rozsahu určeného **ExitCodeRanges** vlastnost.
- Výchozí Ano, pokud úlohu ukončí s ukončovacím kódem není definované **ExitCodes** nebo **ExitCodeRanges**, nebo pokud úlohu ukončí s předem zpracování chyb a **PreProcessingError** není nastavena vlastnost, nebo pokud se úloha nezdaří s soubor nahrát chyba a **FileUploadError** není nastavena vlastnost. 

Určení závislostí akce v rozhraní .NET, nastavte [ExitOptions][net_exitoptions].[ DependencyAction] [ net_dependencyaction] vlastnost ukončovací podmínky. **DependencyAction** vlastnost trvá jednu ze dvou hodnot:

- Nastavení **DependencyAction** vlastnost **Satisfy** označuje, že závislé úlohy jsou způsobilé ke spouštění, pokud nadřazená úloha ukončí kvůli zadané chybě.
- Nastavení **DependencyAction** vlastnost **bloku** označuje, že závislé úlohy nejsou způsobilé ke spuštění.

Výchozí nastavení **DependencyAction** vlastnost je **Satisfy** pro ukončovací kód 0, a **bloku** pro všechny ostatní podmínek ukončení.

Následující fragment kódu nastaví kód **DependencyAction** vlastnost pro úlohu nadřazené. Pokud úloha nadřazené ukončí předběžného zpracování chyby nebo s zadané chybové kódy, závislé úlohy je blokován. Pokud úloha nadřazené ukončí s nenulovou hodnotou chybě, závislé úlohy nemá oprávnění ke spuštění.

```csharp
// Task A is the parent task.
new CloudTask("A", "cmd.exe /c echo A")
{
    // Specify exit conditions for task A and their dependency actions.
    ExitConditions = new ExitConditions
    {
        // If task A exits with a pre-processing error, block any downstream tasks (in this example, task B).
        PreProcessingError = new ExitOptions
        {
            DependencyAction = DependencyAction.Block
        },
        // If task A exits with the specified error codes, block any downstream tasks (in this example, task B).
        ExitCodes = new List<ExitCodeMapping>
        {
            new ExitCodeMapping(10, new ExitOptions() { DependencyAction = DependencyAction.Block }),
            new ExitCodeMapping(20, new ExitOptions() { DependencyAction = DependencyAction.Block })
        },
        // If task A succeeds or fails with any other error, any downstream tasks become eligible to run 
        // (in this example, task B).
        Default = new ExitOptions
        {
            DependencyAction = DependencyAction.Satisfy
        }
    }
},
// Task B depends on task A. Whether it becomes eligible to run depends on how task A exits.
new CloudTask("B", "cmd.exe /c echo B")
{
    DependsOn = TaskDependencies.OnId("A")
},
```

## <a name="code-sample"></a>Ukázka kódu
[TaskDependencies] [ github_taskdependencies] ukázkový projekt je jedním z [ukázky kódu Azure Batch] [ github_samples] na Githubu. Toto řešení sady Visual Studio ukazuje:

- Postup povolení úloh závislost na úlohu
- Postup vytvoření úlohy, které závisí na jiné úlohy
- Jak provést tyto úlohy ve fondu výpočetních uzlů.

## <a name="next-steps"></a>Další postup
### <a name="application-deployment"></a>Nasazení aplikace
[Balíčky aplikací](batch-application-packages.md) funkce služby Batch poskytuje snadný způsob pro obě nasazení a verze aplikace, které vaše úkoly spouští na výpočetních uzlech.

### <a name="installing-applications-and-staging-data"></a>Instalace aplikací a pracovní data
V tématu [instalaci aplikací a dat na Batch pracovních výpočetní uzly] [ forum_post] ve fóru Azure Batch přehled při přípravě uzlů ke spouštění úloh. Tento příspěvek zapsána pomocí některého z členů týmu Azure Batch, je dobré Úvod do na různé způsoby, jak zkopírovat aplikace, úlohy vstupních dat a další soubory do výpočetních uzlů.

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_taskdependencies]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_dependson]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.dependson.aspx
[net_exitcode]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskexecutioninformation.exitcode.aspx
[net_exitconditions]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.exitconditions
[net_exitoptions]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.exitoptions
[net_dependencyaction]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.exitoptions#Microsoft_Azure_Batch_ExitOptions_DependencyAction
[net_msdn]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_onid]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onid.aspx
[net_onids]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onids.aspx
[net_onidrange]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onidrange.aspx
[net_taskexecutioninformation]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskexecutioninformation.aspx
[net_taskstate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.taskstate.aspx
[net_usestaskdependencies]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.usestaskdependencies.aspx
[net_taskdependencies]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskdependencies.aspx

[1]: ./media/batch-task-dependency/01_one_to_one.png "Diagram: závislost 1: 1"
[2]: ./media/batch-task-dependency/02_one_to_many.png "Diagram: závislost na více"
[3]: ./media/batch-task-dependency/03_task_id_range.png "Diagram: Úloha id rozsah závislostí"
