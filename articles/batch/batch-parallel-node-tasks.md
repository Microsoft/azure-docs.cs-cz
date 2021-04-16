---
title: Souběžné spouštění úloh pro maximalizaci využití výpočetních uzlů Batch
description: Zvýšení efektivity a snížení nákladů pomocí menšího počtu výpočetních uzlů a souběžného spouštění úkolů na každém uzlu ve fondu Azure Batch
ms.topic: how-to
ms.date: 04/13/2021
ms.custom: H1Hack27Feb2017, devx-track-csharp
ms.openlocfilehash: 81648f30a7a02f702dcb189aa7df27e5a82e2b07
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389294"
---
# <a name="run-tasks-concurrently-to-maximize-usage-of-batch-compute-nodes"></a>Souběžné spouštění úloh pro maximalizaci využití výpočetních uzlů Batch

Využití prostředků na menším počtu výpočetních uzlů ve fondu můžete maximalizovat tak, že na každém uzlu spustíte více než jeden úkol současně.

I když některé scénáře fungují nejlépe se všemi prostředky uzlu vyhrazenými pro jeden úkol, můžou některé úlohy zobrazovat kratší dobu úloh a snížit náklady, když se tyto prostředky sdílejí s více úlohami. Zvažte následující scénáře:

- **Minimalizujte přenos dat** pro úlohy, které můžou sdílet data. Poplatky za přenos dat můžete výrazně snížit zkopírováním sdílených dat do menšího počtu uzlů a následným spouštěním úloh paralelně na jednotlivých uzlech. To platí hlavně v případě, že se data, která mají být kopírována do každého uzlu, musí přenášet mezi geografickými oblastmi.
- **Maximalizujte využití paměti** pro úlohy, které vyžadují velké množství paměti, ale jenom během krátkých časových období a v určitém časovém intervalu při provádění. Můžete využít méně, ale větší výpočetní uzel s větší pamětí, abyste mohli efektivně zvládnout takové špičky. Tyto uzly budou mít paralelně spuštěné více úloh na každém uzlu, ale každý úkol může využít výhod plentiful paměti uzlů v různých časech.
- Omezte **omezení počtu uzlů** , pokud se v rámci fondu vyžaduje komunikace mezi uzly. V současné době jsou fondy nakonfigurované pro komunikaci mezi uzly omezeny na 50 výpočetních uzlů. Pokud každý uzel v takovém fondu může spouštět úlohy paralelně, může se spustit větší počet úkolů současně.
- **Replikujte místní výpočetní cluster**, například když nejprve přesunete výpočetní prostředí do Azure. Pokud vaše aktuální místní řešení spouští více úloh na výpočetní uzel, můžete zvýšit maximální počet úloh uzlu, aby se tato konfigurace podrobněji rozrážejí.

## <a name="example-scenario"></a>Ukázkový scénář

Představte si například aplikaci úkolu s požadavky na procesor a paměť, které jsou dostatečné pro [standardní uzly \_ D1](../cloud-services/cloud-services-sizes-specs.md#d-series) . Aby se ale dokončila úloha v požadované době, 1 000 těchto uzlů je potřeba.

Místo používání standardních \_ uzlů D1 s 1 jádrem procesoru můžete použít [standardní uzly \_ D14](../cloud-services/cloud-services-sizes-specs.md#d-series) s 16 jádry každého a povolit paralelní provádění úkolů. To znamená, že se dá použít méně uzlů, než je počet uzlů 1 000, ale bude potřeba jenom 63. Pokud jsou pro každý uzel požadovány rozsáhlé soubory aplikace nebo referenční data, jsou vylepšena doba trvání úlohy a efektivita, protože data jsou zkopírována pouze do 63 uzlů.

## <a name="enable-parallel-task-execution"></a>Povolit spuštění paralelní úlohy

Výpočetní uzly můžete nakonfigurovat pro provádění paralelních úkolů na úrovni fondu. Pomocí knihovny Batch .NET nastavte při vytváření fondu vlastnost [CloudPool. TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool.taskslotspernode) . Pokud používáte REST API dávky, nastavte v textu žádosti během vytváření fondu prvek [taskSlotsPerNode](/rest/api/batchservice/pool/add) .

> [!NOTE]
> `taskSlotsPerNode`Vlastnost element a [TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool) lze nastavit pouze v okamžiku vytvoření fondu. Po vytvoření fondu již nelze tyto změny změnit.

Azure Batch umožňuje nastavit sloty úloh na jeden uzel až (4x) na počet jader uzlů. Například pokud je fond nakonfigurovaný s uzly o velikosti "velký" (čtyři jádra), `taskSlotsPerNode` může se nastavit na 16. Bez ohledu na to, kolik jader má uzel, ale nemůžete mít více než 256 slotů úloh na jeden uzel. Podrobnosti o počtu jader pro jednotlivé velikosti uzlů najdete v tématu [velikosti pro Cloud Services](../cloud-services/cloud-services-sizes-specs.md). Další informace o omezeních služeb najdete v tématu [kvóty a omezení pro službu Azure Batch](batch-quota-limit.md).

> [!TIP]
> Nezapomeňte vzít v úvahu `taskSlotsPerNode` hodnotu při vytváření [vzorce automatického škálování](/rest/api/batchservice/pool/enableautoscale) pro váš fond. Například vzorec, který vyhodnocuje, `$RunningTasks` může výrazně ovlivnit zvýšení počtu úkolů na uzel. Další informace najdete v tématu [Automatické škálování výpočetních uzlů ve fondu Azure Batch](batch-automatic-scaling.md).

## <a name="specify-task-distribution"></a>Zadat distribuci úkolu

Při povolování souběžných úloh je důležité určit, jak se mají úlohy distribuovat mezi uzly ve fondu.

Pomocí vlastnosti [CloudPool. TaskSchedulingPolicy](/dotnet/api/microsoft.azure.batch.cloudpool.taskschedulingpolicy) můžete určit, že se mají úlohy přiřadit rovnoměrně mezi všechny uzly ve fondu ("rozprostření"). Nebo můžete určit, že k jednotlivým uzlům by se měly přiřadit tolik úkolů, než se úkoly přiřadí do jiného uzlu ve fondu ("balení").

Můžete například zvážit fond [standardních \_ D14](../cloud-services/cloud-services-sizes-specs.md#d-series) uzlů (v předchozím příkladu), který je nakonfigurovaný s hodnotou [CloudPool. TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool.taskslotspernode) 16. Pokud je [CloudPool. TaskSchedulingPolicy](/dotnet/api/microsoft.azure.batch.cloudpool.taskschedulingpolicy) nakonfigurovaný s [ComputeNodeFillType](/dotnet/api/microsoft.azure.batch.common.computenodefilltype) *packem*, může maximalizovat využití všech 16 jader každého uzlu a umožňuje [fondu automatického škálování](batch-automatic-scaling.md) odebrat nepoužívané uzly (uzly bez přiřazených úkolů) z fondu. Tím se minimalizuje využití prostředků a šetří peníze.

## <a name="define-variable-slots-per-task"></a>Definovat proměnné sloty na úlohu

Úkol lze definovat pomocí vlastnosti [CloudTask. RequiredSlots](/dotnet/api/microsoft.azure.batch.cloudtask.requiredslots) , která určuje, kolik slotů vyžaduje spuštění na výpočetním uzlu. Výchozí hodnota je 1. Pokud mají vaše úkoly různé váhy týkající se využití prostředků na výpočetním uzlu, můžete nastavit proměnné. To umožňuje každému výpočetnímu uzlu přiměřený počet souběžně spuštěných úloh bez zahlcení systémových prostředků, jako je CPU nebo paměť.

Například u fondu s vlastností `taskSlotsPerNode = 8` můžete odesílat úlohy s více jádry náročné na procesor s `requiredSlots = 8` , zatímco jiné úlohy lze nastavit na `requiredSlots = 1` . Po naplánování této smíšené úlohy se úlohy náročné na procesor spustí výhradně na jejich výpočetních uzlech, zatímco jiné úlohy můžou běžet souběžně (až osm úloh najednou) na jiných uzlech. Díky tomu můžete vyrovnávat zatížení napříč výpočetními uzly a zlepšit efektivitu využití prostředků.

Ujistěte se, že neurčíte, `requiredSlots` aby úkol byl větší než ve fondu `taskSlotsPerNode` . Výsledkem bude, že se úloha nebude nikdy moct spustit. Služba Batch aktuálně neověřuje tento konflikt, když odesíláte úkoly, protože v době odeslání se nemusí fond svázat nebo ho můžete změnit na jiný fond tím, že zakážete nebo znovu povolíte.

> [!TIP]
> Při použití proměnných úloh proměnné je možné, že velké úlohy s dalšími požadovanými Sloty mohou být dočasně neúspěšné, protože na žádném výpočetním uzlu nejsou k dispozici dostatek slotů, a to ani v případě, že jsou na některých uzlech stále nečinné sloty. Můžete zvýšit prioritu úlohy pro tyto úlohy a zvýšit tak jejich šanci na soutěž na dostupné sloty na uzlech.
>
> Služba Batch generuje [TaskScheduleFailEvent](batch-task-schedule-fail-event.md) , když se nepovede naplánovat spuštění úlohy a pokračuje v plánování až do chvíle, kdy budou k dispozici požadované sloty. Můžete naslouchat této události a zjistit potenciální problémy s plánováním úloh a zmírnit je.

## <a name="batch-net-example"></a>Příklad dávky .NET

Následující fragmenty kódu v rozhraní [Batch .NET](/dotnet/api/microsoft.azure.batch) API ukazují, jak vytvořit fond s více sloty úloh na jeden uzel a jak odeslat úkol s požadovanými sloty.

### <a name="create-a-pool-with-multiple-task-slots-per-node"></a>Vytvoření fondu s více sloty úloh na jeden uzel

Tento fragment kódu ukazuje požadavek na vytvoření fondu, který obsahuje čtyři uzly se čtyřmi sloty úloh povolenými pro každý uzel. Určuje zásadu plánování úkolů, která před přiřazením úkolů jinému uzlu ve fondu vyplní každý uzel úkoly.

Další informace o přidávání fondů pomocí rozhraní API služby Batch najdete v tématu [BatchClient. PoolOperations. CreatePool](/dotnet/api/microsoft.azure.batch.pooloperations.createpool).

```csharp
CloudPool pool =
    batchClient.PoolOperations.CreatePool(
        poolId: "mypool",
        targetDedicatedComputeNodes: 4
        virtualMachineSize: "standard_d1_v2",
        VirtualMachineConfiguration: new VirtualMachineConfiguration(
            imageReference: new ImageReference(
                                publisher: "MicrosoftWindowsServer",
                                offer: "WindowsServer",
                                sku: "2019-datacenter-core",
                                version: "latest"),
            nodeAgentSkuId: "batch.node.windows amd64");

pool.TaskSlotsPerNode = 4;
pool.TaskSchedulingPolicy = new TaskSchedulingPolicy(ComputeNodeFillType.Pack);
pool.Commit();
```

### <a name="create-a-task-with-required-slots"></a>Vytvoření úlohy s požadovanými sloty

Tento fragment kódu vytvoří úlohu s jiným než výchozím `requiredSlots` . Tato úloha se spustí pouze v případě, že je na výpočetním uzlu k dispozici dostatek volných slotů.

```csharp
CloudTask task = new CloudTask(taskId, taskCommandLine)
{
    RequiredSlots = 2
};
```

### <a name="list-compute-nodes-with-counts-for-running-tasks-and-slots"></a>Vypsání výpočetních uzlů s počty pro spuštěné úlohy a sloty

Tento fragment kódu obsahuje seznam všech výpočetních uzlů ve fondu a tiskne počty pro spuštěné úlohy a sloty úloh na uzel.

```csharp
ODATADetailLevel nodeDetail = new ODATADetailLevel(selectClause: "id,runningTasksCount,runningTaskSlotsCount");
IPagedEnumerable<ComputeNode> nodes = batchClient.PoolOperations.ListComputeNodes(poolId, nodeDetail);

await nodes.ForEachAsync(node =>
{
    Console.WriteLine(node.Id + " :");
    Console.WriteLine($"RunningTasks = {node.RunningTasksCount}, RunningTaskSlots = {node.RunningTaskSlotsCount}");

}).ConfigureAwait(continueOnCapturedContext: false);
```

### <a name="list-task-counts-for-the-job"></a>Vypsat počty úloh pro úlohu

Tento fragment kódu získá počty úloh pro úlohu, která zahrnuje úlohy a počet slotů úloh na stav úkolu.

```csharp
TaskCountsResult result = await batchClient.JobOperations.GetJobTaskCountsAsync(jobId);

Console.WriteLine("\t\tActive\tRunning\tCompleted");
Console.WriteLine($"TaskCounts:\t{result.TaskCounts.Active}\t{result.TaskCounts.Running}\t{result.TaskCounts.Completed}");
Console.WriteLine($"TaskSlotCounts:\t{result.TaskSlotCounts.Active}\t{result.TaskSlotCounts.Running}\t{result.TaskSlotCounts.Completed}");
```

## <a name="batch-rest-example"></a>Příklad dávky v dávce

Následující fragmenty kódu rozhraní REST API pro [Batch](/rest/api/batchservice/) ukazují, jak vytvořit fond s více sloty úloh na jeden uzel a jak odeslat úkol s požadovanými sloty.

### <a name="create-a-pool-with-multiple-task-slots-per-node"></a>Vytvoření fondu s více sloty úloh na jeden uzel

Tento fragment kódu ukazuje požadavek na vytvoření fondu, který obsahuje dva velké uzly s maximálně čtyřmi úkoly na uzel.

Další informace o přidávání fondů pomocí REST API najdete v tématu [Přidání fondu k účtu](/rest/api/batchservice/pool/add).

```json
{
  "odata.metadata":"https://myaccount.myregion.batch.azure.com/$metadata#pools/@Element",
  "id":"mypool",
  "vmSize":"large",
  "virtualMachineConfiguration": {
    "imageReference": {
      "publisher": "canonical",
      "offer": "ubuntuserver",
      "sku": "18.04-lts"
    },
    "nodeAgentSKUId": "batch.node.ubuntu 16.04"
  },
  "targetDedicatedComputeNodes":2,
  "taskSlotsPerNode":4,
  "enableInterNodeCommunication":true,
}
```

### <a name="create-a-task-with-required-slots"></a>Vytvoření úlohy s požadovanými sloty

Tento fragment kódu ukazuje požadavek na přidání úlohy s jiným než výchozím nastavením `requiredSlots` . Tato úloha se spustí pouze v případě, že je na výpočetním uzlu k dispozici dostatek volných slotů.

```json
{
  "id": "taskId",
  "commandLine": "bash -c 'echo hello'",
  "userIdentity": {
    "autoUser": {
      "scope": "task",
      "elevationLevel": "nonadmin"
    }
  },
  "requiredSLots": 2
}
```

## <a name="code-sample-on-github"></a>Ukázka kódu na GitHubu

Projekt [ParallelTasks](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks) na GitHubu ilustruje použití vlastnosti [CloudPool. TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool.taskslotspernode) .

Tato Konzolová aplikace v jazyce C# používá knihovnu [Batch .NET](/dotnet/api/microsoft.azure.batch) k vytvoření fondu s jedním nebo více výpočetními uzly. Spustí na těchto uzlech konfigurovatelný počet úloh pro simulaci zatížení proměnné. Výstup z aplikace zobrazuje, které uzly provedly jednotlivé úlohy. Aplikace také poskytuje souhrn parametrů a doby trvání úloh.

Příklad: níže je souhrnná část výstupu ze dvou různých spuštění ukázkové aplikace ParallelTasks. Trvání úloh, které jsou zde uvedeny, neobsahují čas vytvoření fondu, protože každá úloha byla odeslána do dříve vytvořeného fondu, jehož výpočetní uzly byly v době odeslání ve stavu *nečinnosti* .

První spuštění ukázkové aplikace ukazuje, že s jedním uzlem ve fondu a s výchozím nastavením jednoho úkolu na uzel je doba trvání úlohy více než 30 minut.

```
Nodes: 1
Node size: large
Task slots per node: 1
Max slots per task: 1
Tasks: 32
Duration: 00:30:01.4638023
```

Druhý běh ukázky ukazuje výrazné snížení doby trvání úlohy. Důvodem je to, že fond byl nakonfigurován se čtyřmi úkoly na jeden uzel, což umožňuje paralelní spouštění úkolů dokončit úlohu téměř ve čtvrtletí času.

```
Nodes: 1
Node size: large
Task slots per node: 4
Max slots per task: 1
Tasks: 32
Duration: 00:08:48.2423500
```

## <a name="next-steps"></a>Další kroky

- Vyzkoušejte [Batch Explorer](https://azure.github.io/BatchExplorer/) Heat mapy. Batch Explorer je bezplatný a samostatný klientský nástroj s bohatými funkcemi, který vám umožní vytvářet, ladit a monitorovat Azure Batch aplikace. Když spouštíte ukázkovou aplikaci [ParallelTasks](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks) , funkce Batch Explorer tepelný map vám umožní snadno vizualizovat spouštění paralelních úloh na jednotlivých uzlech.
- Prozkoumejte [Azure Batch ukázky na GitHubu](https://github.com/Azure/azure-batch-samples).
- Přečtěte si další informace o [závislostech úloh Batch](batch-task-dependencies.md).
