---
title: Pomocí závislosti úkolů ke spuštění úlohy, které jsou založeny na dokončení jiné úlohy – Azure Batch | Dokumentace Microsoftu
description: Vytvoření úlohy, které závisí na dokončení dalších úkolů ke zpracování MapReduce style a podobných velkých objemů dat úloh ve službě Azure Batch.
services: batch
documentationcenter: .net
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: b8d12db5-ca30-4c7d-993a-a05af9257210
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 05/22/2017
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5fe8cbbaa16e4a3de0744f98ff9394d198805cca
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55475597"
---
# <a name="create-task-dependencies-to-run-tasks-that-depend-on-other-tasks"></a>Vytvoření závislosti úkolů ke spuštění úlohy, které závisí na jiné úkoly

Můžete definovat závislosti úkolů ke spuštění úlohy nebo úlohy, až po dokončení nadřazené úlohy. Některé scénáře, ve kterém jsou užitečné závislostí patří:

* Úlohy MapReduce – vizuální styl v cloudu.
* Úlohy, jejichž úlohy související se zpracováním dat může být vyjádřený jako orientovaného acyklického grafu (DAG).
* Vykreslování před a po vykreslení procesy, kde každá úloha musí dokončit předtím, než můžete začít dalším úkolem.
* Všechny ostatní úlohy ve kterém následných úloh závisí na výstupu nadřazeného úlohy.

Pomocí závislosti úkolů služby Batch můžete vytvořit úlohy, které jsou plánovány pro spuštění ve výpočetních uzlech po dokončení jedné nebo několika úloh nadřazené. Můžete například vytvořit úlohu, která vykreslí každý snímek 3D film s samostatné, paralelní úlohy. Poslední úlohy--"sloučení úkol"--sloučení vykreslené snímky do dokončení videa až poté, co všechny rámce bylo úspěšně vykresleno.

Ve výchozím nastavení závislé úlohy jsou naplánovány k provedení až po nadřazená úloha byla úspěšně dokončena. Můžete určit závislosti akce potlačit výchozí chování a spouštění úloh, pokud nadřazená úloha se nezdaří. Zobrazit [závislostí akcí](#dependency-actions) podrobné informace.  

Můžete vytvořit úlohy, které jsou závislé na jiných úkolech v relaci 1: 1 nebo 1 n. Můžete také vytvořit rozsah závislostí, kde úkol závisí na dokončení skupiny úkolů v zadaném rozsahu ID úlohy. Můžete kombinovat tyto tři základní scénáře vytvářet vztahy m: m.

## <a name="task-dependencies-with-batch-net"></a>Závislosti úkolů pomocí rozhraní Batch .NET
V tomto článku si popíšeme, jak nakonfigurovat pomocí závislosti úkolů [Batch .NET] [ net_msdn] knihovny. Nejprve ukážeme, jak k [povolit závislosti úkolů](#enable-task-dependencies) na úlohy a potom ukazují, jak [nakonfigurovat úlohu se závislostmi](#create-dependent-tasks). Také zjistíte, jak určit závislosti akce ke spuštění závislých úloh selže nadřazené. Nakonec se podíváme [závislostí situacích](#dependency-scenarios) , služba Batch podporuje.

## <a name="enable-task-dependencies"></a>Povolí závislosti úkolů
Použít závislosti úkolů v aplikaci služby Batch, musíte nejprve nakonfigurovat úlohy pomocí závislosti úkolů. V Batch .NET, povolte ho na vaše [CloudJob] [ net_cloudjob] nastavením jeho [UsesTaskDependencies] [ net_usestaskdependencies] vlastnost `true`:

```csharp
CloudJob unboundJob = batchClient.JobOperations.CreateJob( "job001",
    new PoolInformation { PoolId = "pool001" });

// IMPORTANT: This is REQUIRED for using task dependencies.
unboundJob.UsesTaskDependencies = true;
```

V předchozím fragmentu kódu "batchClient" je instance [BatchClient] [ net_batchclient] třídy.

## <a name="create-dependent-tasks"></a>Vytvořit závislé úlohy
Pokud chcete vytvořit úlohu, která závisí na dokončení jedné nebo několika úloh nadřazené, můžete zadat, že úkol "závisí na" Další úkoly. V Batch .NET, nakonfigurujte [CloudTask][net_cloudtask].[ DependsOn] [ net_dependson] vlastnost s instancí [TaskDependencies] [ net_taskdependencies] třídy:

```csharp
// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

Tento fragment kódu vytvoří závislé úlohu s ID úlohy "Květin". Úkol "Květin" závisí na úkoly "Déšť" nebo "Ne". Úloha "květin" bude naplánována ke spuštění na výpočetních uzlech až po úlohy, které byly úspěšně dokončeny "Déšť" a "Ne".

> [!NOTE]
> Ve výchozím nastavení, úloha se považuje za úspěšně dokončit, až se **Dokončit** stavu a jeho **ukončovací kód** je `0`. V Batch .NET, to znamená, že [CloudTask][net_cloudtask].[ Stav] [ net_taskstate] hodnotou vlastnosti `Completed` a CloudTask [TaskExecutionInformation][net_taskexecutioninformation].[ Ukončovací kód] [ net_exitcode] hodnota vlastnosti je `0`. Pro toto nastavení změnit, naleznete v tématu [závislostí akcí](#dependency-actions) oddílu.
> 
> 

## <a name="dependency-scenarios"></a>Závislost scénáře
Existují tři scénáře závislost základní úlohy, které můžete použít ve službě Azure Batch: 1: 1, 1 n a ID úlohy rozsah závislostí. Ty mohou být kombinovány pro zadejte čtvrtou scénáři many-to-many.

| Scénář&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Příklad: |  |
|:---:| --- | --- |
|  [One-to-one](#one-to-one) |*Úkolb* závisí na *úkolu a* <p/> *Úkolb* pro provádění, dokud nebude naplánováno *úkolu a* byla úspěšně dokončena |![Diagram: 1: 1 závislostí][1] |
|  [Jeden mnoho](#one-to-many) |*ÚkolC* závisí na *úkoluA* a *úkoluB* <p/> *Úkolc* nebude nutné naplánovat provedení do obou *úkolu a* a *Úkolb* byly úspěšně dokončeny |![Diagram: závislost na více úkolů][2] |
|  [Rozsah ID úkolu](#task-id-range) |*Úkold* závisí na mnoha různých úkolů <p/> *Úkold* nebude nutné naplánovat provedení až do úlohy s ID *1* prostřednictvím *10* byly úspěšně dokončeny |![Diagram: Rozsah id závislostí][3] |

> [!TIP]
> Můžete vytvořit **many-to-many** vztahů, například pokud úkoly C, D, E a F každý závisí na úlohy A a B. To je užitečné, například v paralelizované předběžného zpracování scénářů, kde podřízené úlohy závisí na výstupu několik úkolů nadřazený.
> 
> V příkladech v této části závislé úkol spustí, až poté, co nadřazené úlohy úspěšně dokončit. Toto chování je výchozí chování pro úlohu závislé. Závislé úlohu lze spustit, jakmile se nepovede nadřazenou úlohu tak, že zadáte akce závislost potlačit výchozí chování. Zobrazit [závislostí akcí](#dependency-actions) podrobné informace.

### <a name="one-to-one"></a>1: 1
V relaci úkol závisí na úspěšném dokončení nadřazených úloh. Vytvoření závislosti, a zadat ID jeden úkol [TaskDependencies][net_taskdependencies].[ OnId] [ net_onid] statickou metodu při naplňování [DependsOn] [ net_dependson] vlastnost [CloudTask] [ net_cloudtask].

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
Ve vztahu jednoho k několika úkol závisí na dokončení více nadřízené úlohy. K vytvoření závislost, zadejte kolekci úloh ID [TaskDependencies][net_taskdependencies].[ OnIds] [ net_onids] statickou metodu při naplňování [DependsOn] [ net_dependson] vlastnost [CloudTask] [ net_cloudtask].

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
V závislosti na mnoha různých nadřízeným úkolům úkol závisí na dokončení úkolů, jejichž ID ležet v rozsahu.
K vytvoření závislost, zadejte první a poslední úlohy ID v rozsahu, který chcete [TaskDependencies][net_taskdependencies].[ OnIdRange] [ net_onidrange] statickou metodu při naplňování [DependsOn] [ net_dependson] vlastnost [CloudTask] [ net_cloudtask].

> [!IMPORTANT]
> Při použití úkolů ID rozsahy pro závislosti pouze úlohy s ID představující celočíselné hodnoty nebude zvolen podle oblasti. Takže rozsahu `1..10` vybere úlohy `3` a `7`, ale ne `5flamingoes`. 
> 
> Úvodní nuly. nejsou důležité při vyhodnocování závislostí rozsah, tak úkoly s řetězec identifikátory `4`, `04` a `004` budou všechny *v rámci* rozsahu a budou všechny považovány za úkol `4`, takže vyhoví první z nich k dokončení závislost.
> 
> Každý úkol v rozsahu musí splňovat závislost, úspěšně dokončit nebo s chybou, který je namapovaný na závislost akcí nastavenou na dokončení **Satisfy**. Zobrazit [závislostí akcí](#dependency-actions) podrobné informace.
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

Ve výchozím nastavení spustí závislé úlohy nebo sadě úkolů až po nadřazená úloha byla úspěšně dokončena. V některých případech můžete chtít spustit závislé úlohy i v případě, že nadřazená úloha se nezdaří. Výchozí chování můžete přepsat zadáním akce závislostí. Akce závislostí Určuje, zda závislé úlohy oprávnění ke spuštění, na základě úspěchu nebo selhání nadřazeného úkolu. 

Předpokládejme například, že závislé úloh čeká na data z dokončování nadřazeného úkolu. Závislá úloha může být možné spouštět pomocí starší data Pokud se nadřazený úkol nezdaří. Akce závislostí v takovém případě můžete zadat, že závislé úkol je vhodné spustit bez ohledu na selhání nadřazeného úkolu.

Akce závislostí podle ukončovací podmínky pro nadřízenou úlohou. Můžete zadat akce závislostí pro některý z následujících podmínek ukončení; .NET, najdete v článku [ExitConditions] [ net_exitconditions] třídy podrobnosti:

- Pokud nastane chyba předběžného zpracování.
- Když dojde k chybě při odesílání souboru. Pokud se úlohy ukončí s ukončovacím kódem, který byl zadán prostřednictvím **exitCodes** nebo **exitCodeRanges**, a potom zjistí chyba, akce určená ukončovací kód procesu nahrávání souboru má přednost.
- Při ukončení úlohy s ukončovacím kódem určené **ExitCodes** vlastnost.
- Když se úlohy ukončí s ukončovacím kódem, který spadá do rozsahu určeném **ExitCodeRanges** vlastnost.
- Výchozí případ, pokud se úlohy ukončí s ukončovacím kódem nejsou definovány parametrem **ExitCodes** nebo **ExitCodeRanges**, nebo pokud se úlohy ukončí s chyba předběžného zpracování a **PreProcessingError** vlastnost není nastavená, nebo pokud se úloha nezdaří se souborem Chyba odesílání a **FileUploadError** není nastavena vlastnost. 

Určení závislostí akce v .NET, nastavte [ExitOptions][net_exitoptions].[ DependencyAction] [ net_dependencyaction] vlastnost ukončovací podmínky. **DependencyAction** vlastnost má jednu ze dvou hodnot:

- Nastavení **DependencyAction** vlastnost **Satisfy** označuje, že jsou oprávnění ke spouštění, pokud nadřazená úloha se ukončí kvůli zadané chybě závislé úlohy.
- Nastavení **DependencyAction** vlastnost **bloku** značí, že závislé úlohy nejsou vhodné ke spuštění.

Ve výchozím nastavení **DependencyAction** vlastnost je **Satisfy** pro ukončovací kód 0, a **bloku** pro všechny ostatní ukončovací podmínky.

Následující kód fragment kódu nastaví **DependencyAction** vlastnost s nadřazeným úkolem. Pokud nadřazená úloha ukončení chyba předběžného zpracování, nebo zadané kódy chyb, závislé úlohy se zablokuje. Pokud nadřazená úloha ukončí s nenulovou chybě, vás opravňuje závislé úkolů ke spuštění.

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

- Jak povolit závislosti úkolů pro úlohu
- Vytvoření úlohy, které závisí na jiné úkoly
- Jak provádět tyto úlohy ve fondu výpočetních uzlů.

## <a name="next-steps"></a>Další postup
### <a name="application-deployment"></a>Nasazení aplikace
[Balíčky aplikací](batch-application-packages.md) funkcí služby Batch poskytuje snadný způsob, jak nasadit a verze aplikace, které vaše úkoly spouští na výpočetních uzlech.

### <a name="installing-applications-and-staging-data"></a>Instalace aplikací a přípravu dat
Zobrazit [instalaci aplikací a přípravu dat v listu výpočetních uzlů] [ forum_post] ve fóru služby Azure Batch pro přehled metod k přípravě uzlů pro spouštění úloh. Tento příspěvek napsané pomocí některého z členů týmu služby Azure Batch, je dobré hrubý přehled o různých způsobech zkopírujte aplikací, úloh vstupní data a další soubory do výpočetních uzlů.

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

[1]: ./media/batch-task-dependency/01_one_to_one.png "Diagram: 1: 1 závislostí"
[2]: ./media/batch-task-dependency/02_one_to_many.png "Diagram: závislost jeden mnoho"
[3]: ./media/batch-task-dependency/03_task_id_range.png "Diagramu: závislost rozsah id"
