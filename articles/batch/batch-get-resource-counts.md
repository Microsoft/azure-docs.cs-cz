---
title: Počet stavů pro úkoly a uzly – Azure Batch | Dokumenty společnosti Microsoft
description: Spočítejte si stav úloh Azure Batch a výpočetních uzlů, které vám pomůžou spravovat a monitorovat dávková řešení.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: article
ms.date: 09/07/2018
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: a7b58e96918d26851812aa96c18043121c081e94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023918"
---
# <a name="monitor-batch-solutions-by-counting-tasks-and-nodes-by-state"></a>Monitorování dávkových řešení počítáním úloh a uzlů podle stavu

Chcete-li monitorovat a spravovat rozsáhlá řešení Azure Batch, potřebujete přesné počty prostředků v různých stavech. Azure Batch poskytuje efektivní operace pro získání těchto počtů pro dávkové *úlohy* a *výpočetní uzly*. Tyto operace použijte namísto potenciálně časově náročných dotazů seznamu, které vracejí podrobné informace o rozsáhlých kolekcích úkolů nebo uzlů.

* [Získat počet úloh][rest_get_task_counts] získá souhrnný počet aktivních, spuštěných a dokončených úloh v úloze a úkolů, které byly úspěšné nebo neúspěšné. 

  Počítáním úkolů v každém stavu můžete snadněji zobrazit průběh úlohy uživateli nebo zjistit neočekávaná zpoždění nebo selhání, která mohou úlohu ovlivnit. Počet úloh get je k dispozici od dávkových služeb API verze 2017-06-01.5.1 a související sady SDK a nástroje.

* [Seznam počty uzlů fondu][rest_get_node_counts] získá počet vyhrazených a s nízkou prioritou výpočetníu uzlů v každém fondu, které jsou v různých stavech: vytváření, nečinnosti, offline, preempted, restartování, reimaging, spuštění a další. 

  Počítáním uzlů v každém stavu můžete určit, kdy máte odpovídající výpočetní prostředky pro spuštění úloh a identifikaci potenciálních problémů s fondy. Počty uzlů fondu seznamu jsou k dispozici od dávkových služeb API verze 2018-03-01.6.1 a souvisejících sad ADK a nástrojů.

Pokud používáte verzi služby, která nepodporuje operace počtu úloh nebo počtu uzlů, použijte místo toho dotaz na seznam k počítání těchto prostředků. Pomocí dotazu na seznam můžete také získat informace o dalších dávkových prostředcích, jako jsou aplikace, soubory a úlohy. Další informace o použití filtrů v dotazech seznamu naleznete v [tématu Vytváření dotazů pro efektivní seznam dávkových prostředků](batch-efficient-list-queries.md).

## <a name="task-state-counts"></a>Počítá se stav úkolu

Operace Získat počet úkolů počítá úkoly podle následujících stavů:

- **Aktivní** – úloha, která je zařazena do fronty a může být spuštěna, ale není aktuálně přiřazena výpočetnímu uzlu. Úkol je `active` také, pokud je [závislá na nadřazený úkol,](batch-task-dependencies.md) který ještě nebyl dokončen. 
- **Spuštěno** – úloha, která byla přiřazena výpočetnímu uzlu, ale ještě nebyla dokončena. Úloha se počítá `running` jako při `preparing` jeho `running`stavu nebo , jak je uvedeno v získání informací o operaci [úkolu.][rest_get_task]
- **Dokončeno** – úkol, který již není způsobilý ke spuštění, protože byl úspěšně dokončen nebo dokončen neúspěšně a také vyčerpal limit opakování. 
- **Úspěšné** - Úkol, jehož výsledkem provádění úlohje `success`. Batch určuje, zda úkol proběhl úspěšně nebo `TaskExecutionResult` se nezdařilkontrolou vlastnosti [executionInfo.][rest_get_exec_info]
- **Nezdařilo se** Úloha, jejíž výsledkem provádění úloh je `failure`.

Následující ukázka kódu .NET ukazuje, jak načíst počty úkolů podle stavu: 

```csharp
var taskCounts = await batchClient.JobOperations.GetJobTaskCountsAsync("job-1");

Console.WriteLine("Task count in active state: {0}", taskCounts.Active);
Console.WriteLine("Task count in preparing or running state: {0}", taskCounts.Running);
Console.WriteLine("Task count in completed state: {0}", taskCounts.Completed);
Console.WriteLine("Succeeded task count: {0}", taskCounts.Succeeded);
Console.WriteLine("Failed task count: {0}", taskCounts.Failed);
```

Podobný vzor můžete použít pro REST a další podporované jazyky, abyste získali počty úkolů pro úlohu. 

> [!NOTE]
> Verze rozhraní API dávkové služby před 2018-08-01.7.0 také vrátit `validationStatus` vlastnost v get počet úloh odpověď. Tato vlastnost označuje, zda Batch zkontrolovat stav počítá pro konzistenci se stavy hlášené v rozhraní API seznam úkolů. Hodnota `validated` označuje pouze to, že Batch zkontroloval konzistenci alespoň jednou pro úlohu. Hodnota vlastnosti `validationStatus` neoznačuje, zda počty, které získat počet úkolů vrátí jsou aktuálně aktuální.
>

## <a name="node-state-counts"></a>Počítá se stav uzlu

Operace Počty uzlů fondu seznamu počítá výpočetní uzly podle následujících stavů v každém fondu. Samostatné agregační počty jsou k dispozici pro vyhrazené uzly a uzly s nízkou prioritou v každém fondu.

- **Vytváření** – virtuální počítač přidělený Azure, který ještě nezačal připojit fond.
- **Nečinnosti** - dostupný výpočetní uzel, který není aktuálně spuštěna úloha.
- **LeavingPool** - uzel, který opouští fond, buď proto, že uživatel explicitně odebral, nebo protože fond mění velikost nebo automaticky škálování dolů.
- **Offline** – Uzel, který batch nemůže použít k plánování nových úkolů.
- **Preempted** – uzel s nízkou prioritou, který byl odebrán z fondu, protože Azure kultivovaný virtuální počítač. Uzel `preempted` lze znovu inicializovat, pokud je k dispozici náhradní kapacita virtuálního virtuálního provozu s nízkou prioritou.
- **Restartování** - uzel, který se restartuje.
- **Reimaging** - Uzel, na kterém je přeinstalován operační systém.
- **Spuštěno** – uzel, na který běží jeden nebo více úloh (jiných než počáteční úloha).
- **Spuštění** - Uzel, na kterém je spouštěna služba Batch. 
- **StartTaskFailed** - Uzel, na kterém se nepodařilo [spustit úlohu][rest_start_task] a vyčerpal všechny opakování a na kterém `waitForSuccess` je nastavena na počáteční úlohu. Uzel není použitelný pro spouštění úloh.
- **Neznámý** – uzel, který ztratil kontakt se službou Batch a jehož stav není znám.
- **Nepoužitelný** - uzel, který nelze použít pro spuštění úlohy z důvodu chyb.
- **WaitingForStartTask** - Uzel, na kterém byla spuštěna počáteční úloha, ale `waitForSuccess` je nastavena a počáteční úloha nebyla dokončena.

Následující fragment jazyka C# ukazuje, jak vypsat počty uzlů pro všechny fondy v běžném účtu:

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
Následující úryvek jazyka C# ukazuje, jak vypsat počty uzlů pro daný fond v běžném účtu.

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
Podobný vzor můžete použít pro REST a další podporované jazyky získat počty uzlů pro fondy.
 
## <a name="next-steps"></a>Další kroky

* Další informace o koncepcích a funkcích služby Batch najdete v článku [Přehled funkcí Batch](batch-api-basics.md). Článek popisuje primární dávkové prostředky, jako jsou fondy, výpočetní uzly, úlohy a úkoly, a poskytuje přehled funkcí služby.

* Informace o použití filtrů na dotazy, které uvádějí dávkové prostředky, naleznete v [tématu Vytvoření dotazů pro efektivní seznam prostředků dávky](batch-efficient-list-queries.md).


[rest_get_task_counts]: /rest/api/batchservice/job/gettaskcounts
[rest_get_node_counts]: /rest/api/batchservice/account/listpoolnodecounts
[rest_get_task]: /rest/api/batchservice/task/get
[rest_list_tasks]: /rest/api/batchservice/task/list
[rest_get_exec_info]: /rest/api/batchservice/task/get
[rest_start_task]: /rest/api/batchservice/pool/add#starttask

