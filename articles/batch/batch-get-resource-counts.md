---
title: Počet stavů úkolů a uzly – Azure Batch | Dokumentace Microsoftu
description: Počet stavu úkolů služby Azure Batch a výpočetních uzlů ke správě a monitorování řešení Batch.
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.topic: article
ms.date: 06/29/2018
ms.author: danlep
ms.openlocfilehash: f4bad3d7058e82a246afce9502d275c7d485cb88
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39009172"
---
# <a name="monitor-batch-solutions-by-counting-tasks-and-nodes-by-state"></a>Monitorování řešení Batch podle počtu úloh a uzel podle stavu

Ke sledování a správě velkých řešeních Azure Batch, potřebujete přesný počet prostředků v různých stavech. Azure Batch poskytuje efektivní operace k získání těchto počtů pro službu Batch *úlohy* a *výpočetních uzlů*. Pomocí těchto operací místo může trvat delší dobu volání rozhraní API vrací podrobné informace o rozsáhlých kolekcí úkolů nebo uzly.

* [Získání úloh se počítá] [ rest_get_task_counts] získá souhrnný počet, aktivní, běžících a dokončených úloh v rámci úlohy a úkolů, které bylo úspěšné nebo neúspěšné. 

  Podle počtu úloh v jednotlivých stavech, můžete snadněji zobrazit průběh úlohy na uživatele nebo detekci neočekávaným zpožděním nebo chyby, které může mít vliv na úlohy. Počty úloh GET je k dispozici od verze rozhraní API služby Batch verze 2017-06-01.5.1 a souvisejících sad SDK a nástrojů.

* [Seznam fondu uzel počítá] [ rest_get_node_counts] získá počet s nízkou prioritou a vyhrazených výpočetních uzlů ve každý fond, které jsou v různých stavech: vytváření, nečinné, offline, přerušeno, restartování, obnovování z Image, spouštění a další. 

  Podle počtu uzlů v jednotlivých stavech, můžete určit, kdy máte dostatečné výpočetní prostředky, které spouštíte úlohy a identifikovat možné problémy s fondech. Seznam fondu uzel počítá je k dispozici od verze rozhraní API služby Batch verze 2018-03-01.6.1 a souvisejících sad SDK a nástrojů.

Pokud používáte verzi služby, která nepodporuje operace úloh počet nebo uzel počet, místo toho použijte dotaz na seznam ke zjištění těchto prostředků. Dotaz na seznam můžete také použijte k získání informací o dalších prostředcích služby Batch, jako jsou aplikace, soubory a úlohy. Další informace o použití filtrů do seznamu dotazů najdete v tématu [vytvořit dotazy do seznamu prostředků Batch efektivně](batch-efficient-list-queries.md).

## <a name="task-state-counts"></a>Počty stavů úloh

Operaci získat počty úloh počty úloh podle následujících stavů:

- **Aktivní** -úlohu, která je ve frontě a moct spustit, ale není aktuálně přiřazená k výpočetnímu uzlu. Úloha je také `active` jde [závisí na nadřazenou úlohu](batch-task-dependencies.md) ještě nebyl dokončen. 
- **Spuštění** -úlohu, která se přiřadila do výpočetního uzlu, ale zatím není dokončený. Úlohu se počítá jako `running` při jeho stav je buď `preparing` nebo `running`, je určeno [získat informace o úkolu] [ rest_get_task] operace.
- **Dokončení** -úlohu, která už není vhodné spustit, protože buď bylo dokončeno úspěšně, nebo byl dokončen neúspěšně a také vyčerpali limit opakování. 
- **Úspěšné** – úkol, jehož výsledek spuštění úkolu je `success`. Batch Určuje, zda úkol úspěšném nebo neúspěšném kontrolou `TaskExecutionResult` vlastnost [executionInfo] [ rest_get_exec_info] vlastnost.
- **Nepovedlo** úkol, jehož výsledek spuštění úkolu je `failure`.

Následující vzorový kód .NET ukazuje, jak načíst počet úloh podle stavu: 

```csharp
var taskCounts = await batchClient.JobOperations.GetTaskCountsAsync("job-1");

Console.WriteLine("Task count in active state: {0}", taskCounts.Active);
Console.WriteLine("Task count in preparing or running state: {0}", taskCounts.Running);
Console.WriteLine("Task count in completed state: {0}", taskCounts.Completed);
Console.WriteLine("Succeeded task count: {0}", taskCounts.Succeeded);
Console.WriteLine("Failed task count: {0}", taskCounts.Failed);
Console.WriteLine("ValidationStatus: {0}", taskCounts.ValidationStatus);
```

Chcete-li získat počet úloh pro úlohu můžete použít podobný vzorec pro REST a ostatní podporované jazyky. 
 

### <a name="consistency-checking-for-task-counts"></a>Pro počet úloh kontroly konzistence

Batch poskytuje další ověřování pro počty stavu úlohy pomocí provádí kontroly konzistence s více komponent systému. V nepravděpodobném případě, že kontrola konzistence zjistí chyby opravuje Batch výsledek operace získání úloh se počítá na základě výsledků kontroly konzistence.

`validationStatus` Vlastnost v odpovědi označuje, jestli Batch provést kontrolu konzistence. V případě služby Batch není zaškrtnuté, stav počítat skutečná státy v systému, pak bude `validationStatus` je nastavena na `unvalidated`. Z důvodů výkonu Batch nelze provést kontrolu konzistence Pokud úloha obsahuje více než 200 000 úloh, proto `validationStatus` je nastavena na `unvalidated` v tomto případě. (Počet úkolů není nutně nesprávné v tomto případě je nepravděpodobné, že by i ke ztrátě omezenými daty.) 

Když úloha změní stav, zpracovává agregačního kanálu změn během několika sekund. Operaci získat počty úloh odráží počty aktualizovaná úloha během tohoto období. Ale pokud agregačního kanálu výpadky změnu stavu úkolu, tato změna není registrované až do další ověření pass. Během této doby počty úloh může být nepřesná z důvodu chybějící události, ale jsou opravené v průchodu další ověření.

## <a name="node-state-counts"></a>Počty stavů uzlu

Počet operací počítá uzel seznamu fond výpočetních uzlů pomocí následujících stavů v každém fondu. Samostatné celkové počty jsou k dispozici pro vyhrazené uzly a uzly s nízkou prioritou v každém fondu.

- **Vytváření** -přidělené Azure VM, která ještě nezačala k fondu.
- **Nečinné** – k dispozici výpočetním uzlu, který není aktuálně spuštěna úloha.
- **LeavingPool** -uzel, který opouští ve fondu, protože uživatel je výslovně odebrali nebo fondu je změna velikosti nebo automatické škálování nefunguje.
- **Offline** -uzel této služby Batch nelze použít k naplánování nových úloh.
- **Ke zrušení přidělením** -uzel s nízkou prioritou, který byl odebrán z fondu, protože Azure získat virtuální počítač. A `preempted` uzlu lze znovu inicializovat při nahrazení kapacitu virtuálních počítačů s nízkou prioritou je k dispozici.
- **Restartování** -uzel, který se restartuje.
- **Obnovování z image** -uzel, na kterém je přeinstalaci operačního systému.
- **Spuštění** -uzel, na kterém běží jeden nebo více úkolů (než je na nich spouštěcí úkol).
- **Spouští se** -uzel, na kterém se spouští služby Batch. 
- **StartTaskFailed** -uzel, na kterém [spouštěcí úkol] [ rest_start_task] se nezdařilo a k vyčerpání všech opakovaných pokusů, ve kterém `waitForSuccess` je nastaven pro spouštěcí úkol. Uzel se nedá použít pro spouštění úloh.
- **Neznámý** – uzel, který ztratilo kontakt se službou Batch a jejichž stav není znám.
- **Nepoužitelné** -uzel, který nelze použít pro provedení úlohy z důvodu chyby.
- **WaitingForStartTask** -uzel, na kterém spouštěcí úkol spuštěn, ale `waitForSuccess` je sada a začátkem úkol nebyl dokončen.

Následující jazyka C# fragment kódu ukazuje, jak seznamu uzlu počty pro všechny fondy v aktuální účet:

```csharp
foreach (var nodeCounts in batchClient.PoolOperations.ListPoolNodeCounts())
{
    Console.WriteLine("Pool Id: {0}", nodeCounts.PoolId);

    Console.WriteLine("Total dedicated node count: {0}", nodeCounts.Dedicated.Total);

    // Get dedicated node counts in Idle and Offline states; you can get additional states.
    Console.WriteLine("Dedicated node count in Idle state: {0}", nodeCounts.Dedicated.Idle);
    Console.WriteLine("Dedicated node count in Offline state: {0}", nodeCounts.Dedicated.Offline);

    Console.WriteLine("Total low priority node count: {0}", nodeCounts.LowPriority.Total);

    // Get low-priority node counts in Running and Preempted states; you can get additional states.
    Console.WriteLine("Low-priority node count in Running state: {0}", nodeCounts.LowPriority.Running);
    Console.WriteLine("Low-priority node count in Preempted state: {0}", nodeCounts.LowPriority.Preempted);
}
```
Následující jazyka C# fragment kódu ukazuje, jak seznamu počtu uzlů pro daný fond na aktuální účet.

```csharp
foreach (var nodeCounts in batchClient.PoolOperations.ListPoolNodeCounts(new ODATADetailLevel(filterClause: "poolId eq 'testpool'")))
{
    Console.WriteLine("Pool Id: {0}", nodeCounts.PoolId);

    Console.WriteLine("Total dedicated node count: {0}", nodeCounts.Dedicated.Total);

    // Get dedicated node counts in Idle and Offline states; you can get additional states.
    Console.WriteLine("Dedicated node count in Idle state: {0}", nodeCounts.Dedicated.Idle);
    Console.WriteLine("Dedicated node count in Offline state: {0}", nodeCounts.Dedicated.Offline);

    Console.WriteLine("Total low priority node count: {0}", nodeCounts.LowPriority.Total);

    // Get low-priority node counts in Running and Preempted states; you can get additional states.
    Console.WriteLine("Low-priority node count in Running state: {0}", nodeCounts.LowPriority.Running);
    Console.WriteLine("Low-priority node count in Preempted state: {0}", nodeCounts.LowPriority.Preempted);
}
```
Chcete-li získat uzel počty pro fondy můžete podobný vzorec pro REST a ostatní podporované jazyky.
 
## <a name="next-steps"></a>Další postup

* Další informace o koncepcích a funkcích služby Batch najdete v článku [Přehled funkcí Batch](batch-api-basics.md). Tento článek popisuje primární prostředky služby Batch, jako jsou fondy, výpočetní uzly, úlohy a úkoly a najdete zde přehled funkcí služby.

* Informace o použití filtrů na dotazy, které uvádějí prostředky služby Batch najdete v tématu [vytvořit dotazy do seznamu prostředků Batch efektivně](batch-efficient-list-queries.md).


[rest_get_task_counts]: /rest/api/batchservice/job/gettaskcounts
[rest_get_node_counts]: /rest/api/batchservice/account/listpoolnodecounts
[rest_get_task]: /rest/api/batchservice/task/get
[rest_list_tasks]: /rest/api/batchservice/task/list
[rest_get_exec_info]: /rest/api/batchservice/task/get#executionInfo
[rest_start_task]: /rest/api/batchservice/pool/add#starttask

