---
title: Vytvoření závislostí úloh pro spouštění úloh
description: Vytvořte úkoly, které závisí na dokončení dalších úloh pro zpracování MapReduce stylu a podobných úloh velkých objemů dat v Azure Batch.
ms.topic: how-to
ms.date: 12/28/2020
ms.custom: H1Hack27Feb2017, devx-track-csharp
ms.openlocfilehash: ef05a98fffc3c0684ad0fa29f2f9f039b388f5ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97803931"
---
# <a name="create-task-dependencies-to-run-tasks-that-depend-on-other-tasks"></a>Vytváření závislostí úloh pro spouštění úloh, které jsou závislé na jiných úkolech

Pomocí závislostí úkolů služby Batch vytvoříte úlohy naplánované pro spouštění na výpočetních uzlech po dokončení jedné nebo více nadřazených úkolů. Můžete například vytvořit úlohu, která vykreslí každý snímek 3D videa s oddělenými paralelními úkoly. Finální úkol – "úloha sloučení" – sloučí vykreslené snímky do kompletního filmu až po úspěšném vykreslení všech rámců.

Mezi scénáře, kdy jsou užitečné závislosti úloh, patří:

- MapReduce úlohy ve stylu v cloudu.
- Úlohy, jejichž úlohy zpracování dat lze vyjádřit jako orientovaný acyklického graf (DAG).
- Procesy před vykreslením a následným vykreslením, kde každý úkol musí být dokončen před zahájením další úlohy.
- Jakákoli jiná úloha, ve které jsou podřízené úlohy závislé na výstupu nadřazených úloh.

Ve výchozím nastavení jsou závislé úlohy naplánované pro provádění pouze po úspěšném dokončení nadřazené úlohy. Volitelně můžete zadat [akci závislosti](#dependency-actions)  , která přepíše výchozí chování a spustí úlohy, když dojde k chybě nadřazené úlohy.

## <a name="task-dependencies-with-batch-net"></a>Závislosti úkolů pomocí batch .NET

V tomto článku se zabýváme konfigurací závislostí úloh pomocí knihovny [Batch .NET](/dotnet/api/microsoft.azure.batch) . Nejprve vám ukážeme, jak [Povolit závislosti úkolů](#enable-task-dependencies) na vašich úlohách, a pak Ukázat, jak [nakonfigurovat úlohu se závislostmi](#create-dependent-tasks). Také popíšeme, jak zadat akci závislosti pro spuštění závislých úloh, pokud nadřazený objekt neproběhne úspěšně. Nakonec se podíváme na [scénáře závislosti](#dependency-scenarios) , které tato dávková podpora podporuje.

## <a name="enable-task-dependencies"></a>Povolit závislosti úkolů

Chcete-li použít závislosti úkolů v aplikaci Batch, je nutné nejprve nakonfigurovat úlohu, aby používala závislosti úkolů. V rozhraní Batch .NET ho povolte na [vlastnosti cloudjob](/dotnet/api/microsoft.azure.batch.cloudjob) nastavením jeho vlastnosti [UsesTaskDependencies](/dotnet/api/microsoft.azure.batch.cloudjob.usestaskdependencies) na `true` :

```csharp
CloudJob unboundJob = batchClient.JobOperations.CreateJob( "job001",
    new PoolInformation { PoolId = "pool001" });

// IMPORTANT: This is REQUIRED for using task dependencies.
unboundJob.UsesTaskDependencies = true;
```

V předchozím fragmentu kódu je "batchClient" instancí třídy [batchClient](/dotnet/api/microsoft.azure.batch.batchclient) .

## <a name="create-dependent-tasks"></a>Vytváření závislých úloh

Chcete-li vytvořit úkol, který závisí na dokončení jedné nebo více nadřazených úloh, můžete určit, že úkol závisí na dalších úkolech. V aplikaci Batch .NET nakonfigurujte vlastnost [CloudTask. DependsOn](/dotnet/api/microsoft.azure.batch.cloudtask.dependson) s instancí třídy [TaskDependencies](/dotnet/api/microsoft.azure.batch.taskdependencies) :

```csharp
// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

Tento fragment kódu vytvoří závislý úkol s ID úlohy "květiny". Úloha "květiny" závisí na úkolech "deště" a "Sun". Úloha "květiny" bude naplánována na výpočetní uzel až po úspěšném dokončení úloh "deště" a "Sun".

> [!NOTE]
> Ve výchozím nastavení se úkol považuje za úspěšně dokončený, když je ve stavu dokončeno a jeho ukončovací kód je `0` . V dávce .NET to znamená, že hodnota vlastnosti [CloudTask. State](/dotnet/api/microsoft.azure.batch.cloudtask.state) je `Completed` a hodnota vlastnosti [TaskExecutionInformation. ExitCode](/dotnet/api/microsoft.azure.batch.taskexecutioninformation.exitcode) CloudTask je `0` . Informace o tom, jak to změnit, najdete v části [Akce závislosti](#dependency-actions) .

## <a name="dependency-scenarios"></a>Scénáře závislosti

Existují tři základní scénáře závislosti úloh, které můžete použít v Azure Batch: 1:1, 1:1 a rozsah ID úlohy. Tyto tři scénáře lze kombinovat, a poskytnout tak čtvrtý scénář: mnoho až mnoho.

| Případě&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Příklad | Obrázek |
|:---:| --- | --- |
|  [Relace jednoho k jednomu jinému](#one-to-one) |*úkolb* závisí na *úloze* <p/> *úkolb* nebude naplánováno na spuštění, dokud se *úloha* ' úspěšně nedokončila. |:::image type="content" source="media/batch-task-dependency/01_one_to_one.png" alt-text="Diagram znázorňující scénář závislosti mezi jednotlivými úlohami"::: |
|  [1: n](#one-to-many) |*úkolc* závisí na *úloze* a na *úkolb* <p/> *úkolc* nebude naplánováno na spuštění, dokud se úspěšně nedokončí *úlohy Task* a *úkolb* . |:::image type="content" source="media/batch-task-dependency/02_one_to_many.png" alt-text="Diagram znázorňující scénář závislosti úloh 1: n."::: |
|  [Rozsah ID úlohy](#task-id-range) |*úkoly* jsou závislé na rozsahu úkolů. <p/> *úlohy* nebudou naplánovány na provádění, dokud nebudou úspěšně dokončeny úlohy s ID *1* až *10* . |:::image type="content" source="media/batch-task-dependency/03_task_id_range.png" alt-text="Diagram znázorňující scénář závislosti úlohy rozsahu ID úlohy"::: |

> [!TIP]
> Můžete vytvořit relace **m:n** , například kde úkoly C, D, E a F jsou závislé na úkolech a a B. To je užitečné, například v paralelních scénářích předběžného zpracování, kde se vaše podřízené úlohy liší v závislosti na výstupu více nadřazených úloh.
> 
> V příkladech v této části se závislá úloha spustí až po úspěšném dokončení nadřazených úloh. Toto chování je výchozím chováním závislé úlohy. Závislý úkol můžete spustit po chybě nadřazené úlohy zadáním [Akce závislosti](#dependency-actions)  pro přepsání výchozího chování.

### <a name="one-to-one"></a>Relace jednoho k jednomu jinému

V relaci 1:1 závisí úkol na úspěšném dokončení jedné nadřazené úlohy. Chcete-li vytvořit závislost, zadejte pro statickou metodu [TaskDependencies. OnId](/dotnet/api/microsoft.azure.batch.taskdependencies.onid) jedno ID úlohy, když naplníte vlastnost [CloudTask. DependsOn](/dotnet/api/microsoft.azure.batch.cloudtask.dependson) .

```csharp
// Task 'taskA' doesn't depend on any other tasks
new CloudTask("taskA", "cmd.exe /c echo taskA"),

// Task 'taskB' depends on completion of task 'taskA'
new CloudTask("taskB", "cmd.exe /c echo taskB")
{
    DependsOn = TaskDependencies.OnId("taskA")
},
```

### <a name="one-to-many"></a>1: n

V relaci 1: n závisí úkol na dokončení více nadřazených úkolů. Chcete-li vytvořit závislost, poskytněte pro statickou metodu [TaskDependencies. OnIds](/dotnet/api/microsoft.azure.batch.taskdependencies.onids) kolekci ID úlohy, když naplníte vlastnost [CloudTask. DependsOn](/dotnet/api/microsoft.azure.batch.cloudtask.dependson) .

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

### <a name="task-id-range"></a>Rozsah ID úlohy

V závislosti na rozsahu nadřazených úkolů závisí úkol na dokončení úloh, jejichž ID leží v rámci určitého rozsahu.
Chcete-li vytvořit závislost, zadejte ID první a poslední úlohy v rozsahu pro statickou metodu [TaskDependencies](/dotnet/api/microsoft.azure.batch.taskdependencies.onidrange) , když naplníte vlastnost [CloudTask. DependsOn](/dotnet/api/microsoft.azure.batch.cloudtask.dependson) .

> [!IMPORTANT]
> Pokud pro závislosti použijete rozsah ID úkolu, budou rozsahem vybrány pouze úlohy s ID reprezentujícími celočíselné hodnoty. Rozsah například `1..10` vybere úkoly `3` a `7` , ale ne `5flamingoes` .
>
> Úvodní nuly nejsou významné při vyhodnocování závislostí rozsahu, takže úkoly s identifikátory řetězců `4` `04` a `004` budou všechny *v* rozsahu a všechny budou považovány za úlohu `4` , takže první z nich bude vyhovovat závislosti.
>
> Každý úkol v rozsahu musí splňovat závislosti, a to buď pomocí úspěšného dokončení, nebo dokončením selhání, které je namapováno na [akci závislosti](#dependency-actions) nastavenou na hodnotu **vyhovující**.

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

## <a name="dependency-actions"></a>Akce závislosti

Ve výchozím nastavení se závislý úkol nebo sada úkolů spustí až po úspěšném dokončení nadřazené úlohy. V některých scénářích můžete chtít spustit závislé úlohy i v případě, že dojde k chybě nadřazené úlohy. Výchozí chování můžete přepsat zadáním akce závislosti.

Akce závislosti určuje, jestli má být závislý úkol spuštěný, na základě úspěchu nebo neúspěchu nadřazené úlohy. Předpokládejme například, že závislý úkol čeká na data od dokončení nadřazeného úkolu. Pokud je nadřazený úkol neúspěšný, může být stále možné spustit závislý úkol s použitím starších dat. V takovém případě akce závislosti může určit, že je závislý úkol spuštěný bez ohledu na selhání nadřazené úlohy.

Akce závislosti vychází z podmínky ukončení pro nadřazenou úlohu. Akci závislosti můžete zadat pro některou z následujících podmínek ukončení:

- Když dojde k chybě předběžného zpracování.
- Když dojde k chybě nahrávání souboru. Pokud se úloha ukončí s ukončovacím kódem, který byl zadán prostřednictvím **exitCodes** nebo **exitCodeRanges**, a poté dojde k chybě odeslání souboru, má přednost akce určená ukončovacím kódem.
- Když se úloha ukončí s ukončovacím kódem definovaným vlastností **ExitCodes** .
- Když se úloha ukončí s ukončovacím kódem, který spadá do rozsahu zadaného vlastností **ExitCodeRanges** .
- Výchozí případ, pokud se úloha ukončí s ukončovacím kódem, který není definován pomocí **ExitCodes** nebo **ExitCodeRanges**, nebo pokud se úloha ukončí s chybou předběžného zpracování a vlastnost **PreProcessingError** není nastavena, nebo pokud úloha selhala s chybou při nahrávání souboru a vlastnost **FileUploadError** není nastavena. 

Pro .NET se další podrobnosti o těchto podmínkách najdete ve třídě [ExitConditions](/dotnet/api/microsoft.azure.batch.exitconditions) .

Chcete-li zadat akci závislosti v rozhraní .NET, nastavte vlastnost [ExitOptions. DependencyAction](/dotnet/api/microsoft.azure.batch.exitoptions.dependencyaction) pro podmínku Exit na jednu z následujících možností:

- **Vyhovující**: označuje, že závislé úlohy mají nárok na spuštění, Pokud nadřazená úloha skončí se zadanou chybou.
- **Block**: označuje, že závislé úlohy nemají nárok na spuštění.

Výchozí nastavení pro vlastnost **DependencyAction** je **vyhovující** pro ukončovací kód 0 a **blokuje** všechny ostatní podmínky ukončení.

Následující fragment kódu nastaví vlastnost **DependencyAction** pro nadřazenou úlohu. Pokud se nadřazený úkol ukončí s chybou předběžného zpracování nebo se zadanými kódy chyb, je závislý úkol zablokovaný. Pokud se nadřazená úloha ukončí s jakoukoli jinou nenulovou chybou, bude se na závislý úkol spouštět.

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

Vzorový projekt [TaskDependencies](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies) na GitHubu ukazuje:

- Jak povolit závislost úlohy na úloze.
- Jak vytvořit úkoly, které závisejí na jiných úkolech.
- Jak spouštět tyto úlohy ve fondu výpočetních uzlů.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o funkci [balíčků aplikací](batch-application-packages.md) služby Batch, která poskytuje snadný způsob nasazení a verze aplikací, které vaše úkoly spouštějí na výpočetních uzlech.
- Přečtěte si o [kontrole chyb pro úlohy a úlohy](batch-job-task-error-checking.md).
