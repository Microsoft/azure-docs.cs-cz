---
title: Počet stavů pro úlohy a uzly
description: Spočítejte stav úloh Azure Batch a výpočetních uzlů, které vám pomůžou se správou a monitorováním řešení Batch.
ms.date: 06/18/2020
ms.topic: how-to
ms.custom: seodec18
ms.openlocfilehash: 90f741b9ec5e17da4fd0cc95ef921e116b0c27dc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "85960584"
---
# <a name="monitor-batch-solutions-by-counting-tasks-and-nodes-by-state"></a>Monitorování řešení Batch pomocí počítání úkolů a uzlů podle stavu

Pokud chcete monitorovat a spravovat rozsáhlá Azure Batch řešení, možná budete muset určit počty prostředků v různých stavech. Azure Batch poskytuje efektivní operace pro získání počtu úloh služby Batch a výpočetních uzlů. Tyto operace můžete použít místo potenciálně časově náročného seznamu dotazů, které vracejí podrobné informace o velkých kolekcích úkolů nebo uzlů.

- Čítač [získat počet úloh](/rest/api/batchservice/job/gettaskcounts) získá agregovaný počet aktivních, spuštěných a dokončených úloh v úloze a úkolů, které byly úspěšné nebo neúspěšné. 

  Díky počítání úkolů v jednotlivých stavech můžete snáze zobrazit průběh úlohy uživateli nebo zjistit Neočekávaná zpoždění nebo chyby, které mohou ovlivnit úlohu. Pro rozhraní API služby Batch verze 2017 -06-01.5.1 a související sady SDK a nástroje jsou k dispozici počty úloh.

- [Počty uzlů seznamu](/rest/api/batchservice/account/listpoolnodecounts) vycházejí z počtu vyhrazených výpočetních uzlů s nízkou prioritou v každém fondu, které jsou v různých stavech: vytváření, nečinné, offline, přerušené, Restarting, obnovování imagí, spouštění a další.

  Napočítáním uzlů v jednotlivých stavech můžete určit, kdy máte adekvátní výpočetní prostředky pro spuštění úloh, a identifikovat potenciální problémy s vašimi fondy. Počty uzlů seznamu jsou k dispozici pro rozhraní API služby Batch verze 2018 -03-01.6.1 a související sady SDK a nástroje.

Všimněte si, že číslice vracené těmito operacemi nemusí být v aktuálním stavu. Pokud potřebujete mít jistotu, že je počet přesný, použijte k počítání těchto prostředků dotaz na seznam. Seznam dotazů vám také umožní získat informace o dalších prostředcích Batch, jako jsou aplikace. Další informace o použití filtrů pro výpis dotazů najdete v tématu [efektivní vytváření dotazů pro vypsání prostředků služby Batch](batch-efficient-list-queries.md).

## <a name="task-state-counts"></a>Počty stavů úloh

Operace získat počty úloh počítá úlohy podle následujících stavů:

- **Aktivní** – úkol, který je ve frontě a který je schopný spustit, ale není aktuálně přiřazený k výpočetnímu uzlu. Úkol je také `active` v případě, že je [závislý na nadřazené úloze](batch-task-dependencies.md) , která ještě nebyla dokončena. 
- **Running** – úkol, který byl přiřazen k výpočetnímu uzlu, ale ještě nebyl dokončen. Úkol se počítá jako v `running` případě, že je jeho stav buď `preparing` nebo `running` , jak je uvedeno v [informacích o operaci úlohy](/rest/api/batchservice/task/get) .
- **Dokončeno** – úkol, který již není způsobilý ke spuštění, protože buď byl dokončen úspěšně, nebo byl dokončen neúspěšně a také vyčerpal svůj limit opakování. 
- **Úspěch** – úkol, jehož výsledkem je provedení úkolu `success` , je. Služba Batch určí, zda byl úkol úspěšný nebo neúspěšný, kontrolou `TaskExecutionResult` vlastnosti vlastnosti [executionInfo](/rest/api/batchservice/task/get) .
- **Nepodařilo se** Úkol, jehož výsledkem je provedení úkolu `failure` .

Následující ukázka kódu .NET ukazuje, jak načíst počty úloh podle stavu:

```csharp
var taskCounts = await batchClient.JobOperations.GetJobTaskCountsAsync("job-1");

Console.WriteLine("Task count in active state: {0}", taskCounts.Active);
Console.WriteLine("Task count in preparing or running state: {0}", taskCounts.Running);
Console.WriteLine("Task count in completed state: {0}", taskCounts.Completed);
Console.WriteLine("Succeeded task count: {0}", taskCounts.Succeeded);
Console.WriteLine("Failed task count: {0}", taskCounts.Failed);
```

Podobný vzor můžete použít pro REST a jiné podporované jazyky a získat tak počty úloh pro úlohu. 

> [!NOTE]
> Verze rozhraní API služby Batch před 2018 -08-01.7.0 také vrátí `validationStatus` vlastnost v odpovědi získat počty úloh. Tato vlastnost určuje, zda se ve službě Batch kontroluje konzistence stavu s tím, jak stavy hlášené v rozhraní API pro úlohy seznamu. Hodnota `validated` znamená pouze to, že v dávce byla alespoň jednou kontrolována konzistence úlohy. Hodnota `validationStatus` vlastnosti neurčuje, zda jsou počty, které obdrží počty úkolů, aktuálně aktuální.

## <a name="node-state-counts"></a>Počty stavů uzlů

Operace počítání uzlů fondu vypočítává počet výpočetních uzlů podle následujících stavů v jednotlivých fondech. Pro vyhrazené uzly a uzly s nízkou prioritou v každém fondu jsou k dispozici samostatné agregované počty.

- **Vytváření** – virtuální počítač přidělený Azure, který se ještě nespustil pro připojení ke fondu.
- **Nečinné** – dostupný výpočetní uzel, který aktuálně nespouští úlohu.
- **LeavingPool** – uzel, který opouští fond, buď proto, že ho uživatel explicitně odebral, nebo protože se mění velikost fondu nebo automatické škálování.
- **Offline** – uzel, který dávka nemůže použít k naplánování nových úloh.
- **Přerušeno – uzel** s nízkou prioritou, který byl odebrán z fondu, protože Azure znovu VYVOLAL virtuální počítač. `preempted`Uzel lze znovu inicializovat, pokud je k dispozici náhradní kapacita virtuálního počítače s nízkou prioritou.
- **Restartování – uzel** , který se restartuje.
- Obnovování **imagí** – uzel, na kterém je operační systém přeinstalován.
- **Spuštěno** – uzel, na kterém je spuštěna jedna nebo více úloh (jiné než počáteční úkol).
- **Spuštění** – uzel, na kterém se spouští služba Batch. 
- **StartTaskFailed** – uzel, na kterém se nezdařil [spouštěcí úkol](/rest/api/batchservice/pool/add#starttask) a byl vyčerpán všechny opakované pokusy a který `waitForSuccess` je nastaven na počáteční úkol. Uzel nelze použít pro spuštěné úlohy.
- **Neznámý** – uzel, který ztratil kontakt se službou Batch a jehož stav není známý.
- **Nepoužitelný** – uzel, který se nedá použít k provedení úlohy z důvodu chyb.
- **WaitingForStartTask** – uzel, na kterém byl spuštěn spouštěcí úkol, ale `waitForSuccess` je nastaven a počáteční úkol nebyl dokončen.

Následující fragment kódu jazyka C# ukazuje, jak zobrazit seznam počtů uzlů pro všechny fondy v aktuálním účtu:

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

Následující fragment kódu jazyka C# ukazuje, jak vypsat počty uzlů pro daný fond v aktuálním účtu.

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

Podobný vzor můžete použít pro REST a jiné podporované jazyky a získat tak počty uzlů pro fondy.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [pracovních postupech služby Batch a primárních prostředcích](batch-service-workflow-features.md) , jako jsou fondy, uzly, úlohy a úkoly.
- Informace o použití filtrů na dotazy, které uvádějí prostředky Batch, najdete v tématu [efektivní vytváření dotazů pro vypsání prostředků Batch](batch-efficient-list-queries.md).
