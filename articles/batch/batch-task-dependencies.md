---
title: Vytvoření závislostí úkolů pro spouštění úloh – Azure Batch
description: Vytvořte úkoly, které závisí na dokončení dalších úkolů pro zpracování stylu MapReduce a podobných úloh velkých objemů dat v Azure Batch.
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: b8d12db5-ca30-4c7d-993a-a05af9257210
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 05/22/2017
ms.author: labrenne
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ca771117e889afc8e143c4ca4626ab2d3bb4da2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022898"
---
# <a name="create-task-dependencies-to-run-tasks-that-depend-on-other-tasks"></a>Vytvoření závislostí mezi úkoly pro spouštění úloh závislých na jiných úkolech

Závislosti mezi úkoly můžete definovat a spustit úlohu nebo sadu úkolů až po dokončení nadřazeného úkolu. Některé scénáře, kde jsou užitečné závislosti mezi úkoly patří:

* MapReduce-styl úlohy v cloudu.
* Úlohy, jejichž úlohy zpracování dat mohou být vyjádřeny jako řízený acyklický graf (DAG).
* Procesy předběžného vykreslování a následného vykreslování, kde musí být každá úloha dokončena před zahájením další úlohy.
* Všechny ostatní úlohy, ve kterých navazující úkoly závisí na výstupu upstream úkoly.

Pomocí závislostí dávkových úkolů můžete vytvořit úkoly, které jsou naplánovány pro spuštění na výpočetních uzlech po dokončení jednoho nebo více nadřazených úloh. Můžete například vytvořit úlohu, která vykreslí každý snímek 3D filmu se samostatnými paralelními úkoly. Poslední úkol – "úloha sloučení" – sloučí vykreslené snímky do celého filmu až po úspěšném vykreslení všech snímků.

Ve výchozím nastavení jsou závislé úlohy naplánovány k provedení až po úspěšném dokončení nadřazené úlohy. Můžete určit akci závislosti, která přepíše výchozí chování a spustí úlohy, když se nadřazená úloha nezdaří. Podrobnosti najdete v části [Akce závislostí.](#dependency-actions)  

Můžete vytvořit úkoly, které závisí na jiných úkolech ve vztahu 1:1 nebo 1:N. Můžete také vytvořit závislost rozsahu, kde úkol závisí na dokončení skupiny úkolů v rámci zadaného rozsahu ID úkolů. Tyto tři základní scénáře můžete zkombinovat a vytvořit tak relace N:N.

## <a name="task-dependencies-with-batch-net"></a>Závislosti úloh s dávkou .NET
V tomto článku popisujeme, jak nakonfigurovat závislosti mezi úkoly pomocí knihovny [Batch .NET.][net_msdn] Nejprve vám ukážeme, jak [povolit závislost na úlohách](#enable-task-dependencies) a potom ukážeme, jak [nakonfigurovat úkol se závislostmi](#create-dependent-tasks). Popisujeme také, jak určit akci závislosti pro spuštění závislých úloh, pokud se nadřazený systém nezdaří. Nakonec budeme diskutovat o [scénářích závislostí,](#dependency-scenarios) které podporuje Batch.

## <a name="enable-task-dependencies"></a>Povolení závislostí mezi úkoly
Chcete-li použít závislosti úloh v aplikaci Batch, musíte nejprve nakonfigurovat úlohu tak, aby používala závislosti mezi úkoly. V aplikaci Batch .NET povolte v [cloudové práci][net_cloudjob] `true`nastavením [vlastnosti UsesTaskDependencies][net_usestaskdependencies] na:

```csharp
CloudJob unboundJob = batchClient.JobOperations.CreateJob( "job001",
    new PoolInformation { PoolId = "pool001" });

// IMPORTANT: This is REQUIRED for using task dependencies.
unboundJob.UsesTaskDependencies = true;
```

V předchozím fragmentu kódu je "batchClient" instancí třídy [BatchClient.][net_batchclient]

## <a name="create-dependent-tasks"></a>Vytvoření závislých úkolů
Chcete-li vytvořit úkol, který závisí na dokončení jednoho nebo více nadřazených úkolů, můžete určit, že úkol "závisí na" ostatní úkoly. V části Batch .NET nakonfigurujte [cloudtask][net_cloudtask]. [DependsOn][net_dependson] vlastnost s instancí [TaskDependencies třídy:][net_taskdependencies]

```csharp
// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

Tento fragment kódu vytvoří závislý úkol s ID úkolu "Květiny". Úkol "Květiny" závisí na úkolech "Déšť" a "Slunce". Úloha "Květiny" bude naplánováno spuštění na výpočetní uzel pouze po úkoly "Déšť" a "Slunce" byly úspěšně dokončeny.

> [!NOTE]
> Ve výchozím nastavení je úloha považována za úspěšně dokončenou, pokud je ve **stavu dokončení** a jeho **ukončovací kód** je `0`. V batch .NET to znamená [CloudTask][net_cloudtask]. [Hodnota][net_taskstate] vlastnosti `Completed` State a [CloudTask TaskExecutionInformation][net_taskexecutioninformation]. [Hodnota][net_exitcode] vlastnosti `0`ExitCode je . Postup, jak to změnit, naleznete v části [Akce závislostí.](#dependency-actions)
> 
> 

## <a name="dependency-scenarios"></a>Scénáře závislostí
Existují tři základní scénáře závislostí úkolů, které můžete použít v Azure Batch: one-to-one, one-to-one a task ID závislost. Ty lze kombinovat poskytnout čtvrtý scénář, N:N.

| Scénář&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Příklad |  |
|:---:| --- | --- |
|  [Relace jednoho k jednomu jinému](#one-to-one) |*taskB* závisí na *taskA* <p/> *taskB* nebude naplánováno k provedení, dokud *taskA* úspěšně nedokončí |![Diagram: závislost mezi úkoly 1:1][1] |
|  [1:N](#one-to-many) |*taskC* závisí jak na *taskA,* tak na *taskB* <p/> *taskC* nebude naplánováno k provedení, dokud *taskA* a *taskB* úspěšně dokončeny |![Diagram: závislost mezi úkoly 1:N][2] |
|  [Rozsah ID id úkolu](#task-id-range) |*taskD* závisí na řadě úkolů <p/> *taskD* nebude naplánováno k provedení, dokud nebudou úspěšně dokončeny úkoly s ID *1* až *10.* |![Diagram: Závislost rozsahu id úkolu][3] |

> [!TIP]
> Můžete vytvořit relace **N:N,** například kde úkoly C, D, E a F závisí na úkolech A a B. To je užitečné, například v paralelizovaných scénářích předběžného zpracování, kde vaše podřízené úkoly závisí na výstupu více upstream úloh.
> 
> V příkladech v této části se závislá úloha spustí až po úspěšném dokončení nadřazených úloh. Toto chování je výchozí chování pro závislé úlohy. Závislý úkol můžete spustit po selhání nadřazené úlohy zadáním akce závislosti, která přepíše výchozí chování. Podrobnosti najdete v části [Akce závislostí.](#dependency-actions)

### <a name="one-to-one"></a>Relace jednoho k jednomu jinému
Ve vztahu 1:1 závisí úkol na úspěšném dokončení jednoho nadřazeného úkolu. Chcete-li vytvořit závislost, zadejte jedno ID úkolu [taskdependencies][net_taskdependencies]. [OnId][net_onid] statickou metodu při naplnění [DependsOn][net_dependson] vlastnost [CloudTask][net_cloudtask].

```csharp
// Task 'taskA' doesn't depend on any other tasks
new CloudTask("taskA", "cmd.exe /c echo taskA"),

// Task 'taskB' depends on completion of task 'taskA'
new CloudTask("taskB", "cmd.exe /c echo taskB")
{
    DependsOn = TaskDependencies.OnId("taskA")
},
```

### <a name="one-to-many"></a>1:N
Ve vztahu 1:N závisí úkol na dokončení více nadřazených úkolů. Chcete-li vytvořit závislost, zadejte kolekci ID úloh [taskdependencies][net_taskdependencies]. [OnIds][net_onids] statickou metodu při naplnění [DependsOn][net_dependson] vlastnost [CloudTask][net_cloudtask].

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

### <a name="task-id-range"></a>Rozsah ID id úkolu
V závislosti na řadě nadřazených úkolů závisí úkol na dokončení úkolů, jejichž ID leží v rozsahu.
Chcete-li vytvořit závislost, zadejte ID prvního a posledního úkolu v rozsahu [taskdependencies][net_taskdependencies]. [OnIdRange][net_onidrange] statickou metodu při naplnění [DependsOn][net_dependson] vlastnost [CloudTask][net_cloudtask].

> [!IMPORTANT]
> Při použití rozsahů ID úkolů pro vaše závislosti budou rozsahem vybrány pouze úkoly s ID představujícími celé hodnoty. Takže rozsah `1..10` vybere `3` úkoly `7`a `5flamingoes`, ale ne . 
> 
> Počáteční nuly nejsou významné při vyhodnocování závislostí rozsahu, takže úkoly s identifikátory `4`řetězce a `04` `004` všechny budou *v* rozsahu a všechny budou považovány za úkol `4`, takže první, který dokončí, uspokojí závislost.
> 
> Každý úkol v rozsahu musí splňovat závislost, buď úspěšné dokončení nebo dokončení s chybou, která je mapována na akci závislosti **nastavenou**na Satisfy . Podrobnosti najdete v části [Akce závislostí.](#dependency-actions)
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

Ve výchozím nastavení se závislý úkol nebo sada úkolů spustí až po úspěšném dokončení nadřazeného úkolu. V některých případech můžete chtít spustit závislé úlohy i v případě, že nadřazený úkol selže. Výchozí chování můžete přepsat zadáním akce závislosti. Akce závislosti určuje, zda je závislý úkol způsobilý ke spuštění na základě úspěchu nebo neúspěchu nadřazené úlohy. 

Předpokládejme například, že závislý úkol čeká na data z dokončení upstream úkolu. Pokud se úloha upstream nezdaří, může být závislá úloha stále možné spustit pomocí starších dat. V takovém případě může akce závislosti určit, že závislý úkol je způsobilý ke spuštění i přes selhání nadřazené úlohy.

Akce závislosti je založena na výstupní podmínce nadřazeného úkolu. Můžete určit akci závislosti pro některou z následujících podmínek ukončení. pro .NET, naleznete [ExitConditions][net_exitconditions] třídy podrobnosti:

- Dojde-li k chybě předběžného zpracování.
- Dojde-li k chybě odeslání souboru. Pokud se úloha ukončí s ukončovacím kódem, který byl zadán pomocí **exitCodes** nebo **exitCodeRanges**, a pak narazí na chybu odeslání souboru, má přednost akce určená ukončovacím kódem.
- Když se úloha ukončí s ukončovacím kódem definovaným vlastností **ExitCodes.**
- Když se úloha ukončí s ukončovacím kódem, který spadá do rozsahu určeného vlastností **ExitCodeRanges.**
- Výchozí případ, pokud se úloha ukončí s ukončovacím kódem, který není definován **programy ExitCodes** nebo **ExitCodeRanges**, nebo pokud je úloha ukončena s chybou předběžného zpracování a vlastnost **ÍProcessingError** není nastavena nebo pokud úloha selže s chybou odeslání souboru a vlastnost **FileUploadError** není nastavena. 

Chcete-li v rozhraní .NET určit akci závislosti, nastavte [možnost IZP][net_exitoptions]. [Vlastnost DependencyAction][net_dependencyaction] pro podmínku ukončení. Vlastnost **DependencyAction** přebírá jednu ze dvou hodnot:

- Nastavení **vlastnosti DependencyAction** na **Satisfy** označuje, že závislé úkoly jsou způsobilé ke spuštění, pokud se nadřazený úkol ukončí se zadanou chybou.
- Nastavení **Vlastnost DependencyAction** na **Block** označuje, že závislé úkoly nejsou způsobilé ke spuštění.

Výchozí nastavení **vlastnosti DependencyAction** je **Satisfy** for exit code 0 a **Block** for all other exit conditions.

Následující fragment kódu nastaví vlastnost **DependencyAction** pro nadřazený úkol. Pokud se nadřazená úloha ukončí s chybou předběžného zpracování nebo se zadanými kódy chyb, je závislý úkol blokován. Pokud nadřazený úkol ukončí s jinou nenulovou chybu, závislý úkol je možné spustit.

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
Ukázkový projekt [TaskDependencies][github_taskdependencies] je jedním z [ukázek kódu Azure Batch][github_samples] na GitHubu. Toto řešení sady Visual Studio ukazuje:

- Jak povolit závislost na úkolu
- Jak vytvořit úkoly, které závisí na jiných úkolech
- Jak provést tyto úlohy ve fondu výpočetních uzlů.

## <a name="next-steps"></a>Další kroky
### <a name="application-deployment"></a>Nasazení aplikací
Funkce [balíčků aplikací](batch-application-packages.md) Batch poskytuje snadný způsob nasazení i verze aplikací, které vaše úlohy provádějí na výpočetních uzlech.

### <a name="installing-applications-and-staging-data"></a>Instalace aplikací a přípravná data
Přehled metod pro přípravu uzlů ke spuštění úloh najdete v článku [Instalace aplikací a přípravných dat na výpočetních uzlech Batch][forum_post] ve fóru Azure Batch. Tento příspěvek, napsaný jedním z členů týmu Azure Batch, je dobrým základním nátěrem různých způsobů kopírování aplikací, vstupních dat úloh a dalších souborů do výpočetních uzlů.

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
[net_dependencyaction]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.exitoptions
[net_msdn]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_onid]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onid.aspx
[net_onids]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onids.aspx
[net_onidrange]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onidrange.aspx
[net_taskexecutioninformation]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskexecutioninformation.aspx
[net_taskstate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.taskstate.aspx
[net_usestaskdependencies]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.usestaskdependencies.aspx
[net_taskdependencies]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskdependencies.aspx

[1]: ./media/batch-task-dependency/01_one_to_one.png "Diagram: závislost 1:1"
[2]: ./media/batch-task-dependency/02_one_to_many.png "Diagram: závislost 1:N"
[3]: ./media/batch-task-dependency/03_task_id_range.png "Diagram: závislost na rozsahu id úkolu"
