---
title: Spouštění úloh paralelně pro optimalizaci výpočetních prostředků – Azure Batch
description: Zvyšte efektivitu a snižte náklady pomocí menšího počtu výpočetních uzlů a spouštěním souběžných úloh na každém uzlu ve fondu Azure Batch
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 538a067c-1f6e-44eb-a92b-8d51c33d3e1a
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/17/2019
ms.author: labrenne
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5465771cb97ef9d8d5c451a6bafc61c4621d3c4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023629"
---
# <a name="run-tasks-concurrently-to-maximize-usage-of-batch-compute-nodes"></a>Souběžné spouštění úloh za účelem maximalizace využití dávkových výpočetních uzlů 

Spuštěním více než jednu úlohu současně na každém výpočetním uzlu ve fondu Azure Batch, můžete maximalizovat využití prostředků na menší počet uzlů ve fondu. U některých úloh to může mít za následek kratší dobu úlohy a nižší náklady.

Zatímco některé scénáře mají prospěch z vyčlenění všech prostředků uzlu na jeden úkol, několik situací těží z povolení více úkolů ke sdílení těchto prostředků:

* **Minimalizace přenosu dat,** když jsou úkoly schopny sdílet data. V tomto scénáři můžete výrazně snížit poplatky za přenos dat zkopírováním sdílených dat do menšího počtu uzlů a provádění úloh paralelně na každém uzlu. To platí zejména v případě, že data, která mají být zkopírována do každého uzlu, musí být přenesena mezi geografickými oblastmi.
* **Maximalizace využití paměti,** pokud úkoly vyžadují velké množství paměti, ale pouze v krátkých časových obdobích a v proměnných časech během provádění. Můžete použít méně, ale větší výpočetní uzly s více paměti efektivně zpracovat takové špičky. Tyto uzly by mít více úloh spuštěných paralelně na každém uzlu, ale každý úkol by využít hojné paměti uzlů v různých časech.
* **Zmírnění omezení počtu uzlů** při komunikaci mezi uznami je vyžadována v rámci fondu. V současné době fondy nakonfigurované pro komunikaci mezi uzly jsou omezeny na 50 výpočetních uzlů. Pokud každý uzel v takovém fondu je schopen provádět úlohy paralelně, větší počet úloh lze provést současně.
* **Replikace místního výpočetního clusteru**, například při prvním přesunutí výpočetního prostředí do Azure. Pokud vaše aktuální místní řešení provádí více úloh na výpočetní uzel, můžete zvýšit maximální počet úloh uzlu, aby lépe zrcadlit tuto konfiguraci.

## <a name="example-scenario"></a>Příklad scénáře
Jako příklad pro ilustraci výhody paralelní ho spuštění úloh, řekněme, že aplikace úlohy má požadavky na procesor a paměť tak, aby [\_standardní d1](../cloud-services/cloud-services-sizes-specs.md) uzly jsou dostatečné. Ale aby bylo možné dokončit úlohu v požadovaném čase, je zapotřebí 1 000 těchto uzlů.

Namísto použití\_standardních uzlů D1, které mají 1 jádro procesoru, můžete použít standardní [\_](../cloud-services/cloud-services-sizes-specs.md) uzly D14, které mají každý 16 jader, a povolit paralelní provádění úloh. Proto lze použít *16 krát méně uzlů* – namísto 1 000 uzlů by bylo vyžadováno pouze 63 uzlů. Navíc pokud jsou pro každý uzel požadovány velké soubory aplikací nebo referenční data, doba trvání úlohy a efektivita se opět zlepší, protože data jsou zkopírována do pouze 63 uzlů.

## <a name="enable-parallel-task-execution"></a>Povolit provádění paralelních úloh
Konfigurace výpočetních uzlů pro paralelní provádění úloh na úrovni fondu. V knihovně Batch .NET nastavte vlastnost [CloudPool.MaxTasksPerComputeNode][maxtasks_net] při vytváření fondu. Pokud používáte rozhraní API dávky REST, nastavte prvek [maxTasksPerNode][rest_addpool] v těle požadavku během vytváření fondu.

Azure Batch umožňuje nastavit úkoly na uzel až (4x) počet základních uzlů. Například pokud je fond nakonfigurován s uzly velikosti "Velké" (čtyři jádra), pak `maxTasksPerNode` může být nastavena na 16. Však bez ohledu na to, kolik jader uzel má, nemůžete mít více než 256 úkolů na uzel. Podrobnosti o počtu jader pro každou velikost uzlu najdete v tématu [Velikosti pro cloudové služby](../cloud-services/cloud-services-sizes-specs.md). Další informace o omezeních služeb najdete [v tématu Kvóty a limity pro službu Azure Batch](batch-quota-limit.md).

> [!TIP]
> Nezapomeňte vzít v úvahu `maxTasksPerNode` hodnotu při vytváření [vzorce automatického škálování][enable_autoscaling] pro váš fond. Například vzorec, který `$RunningTasks` vyhodnocuje, může být dramaticky ovlivněn nárůstem úkolů na uzel. Další informace najdete [v tématu Automatické škálování výpočetních uzlů ve fondu Azure Batch.](batch-automatic-scaling.md)
>
>

## <a name="distribution-of-tasks"></a>Rozdělení úkolů
Když výpočetní uzly ve fondu můžete provádět úkoly souběžně, je důležité určit, jak chcete, aby úkoly distribuovány mezi uzly ve fondu.

Pomocí [vlastnosti CloudPool.TaskSchedulingPolicy][task_schedule] můžete určit, že úkoly by měly být přiřazeny rovnoměrně ve všech uzlech ve fondu ("rozprostření"). Nebo můžete určit, že každému uzlu má být přiřazeno co nejvíce úkolů, než budou úkoly přiřazeny jinému uzlu ve fondu ("balení").

Jako příklad, jak je tato funkce cenná, zvažte fond [uzlů\_Standard D14](../cloud-services/cloud-services-sizes-specs.md) (v příkladu výše), který je nakonfigurován s hodnotou [CloudPool.MaxTasksPerComputeNode][maxtasks_net] 16. Pokud [CloudPool.TaskSchedulingPolicy][task_schedule] je nakonfigurován s [ComputeNodeFillType][fill_type] *pack*, by maximalizovat využití všech 16 jader každého uzlu a umožnit [automatické škálování fondu](batch-automatic-scaling.md) prořezávat nepoužívané uzly z fondu (uzly bez přiřazených úkolů). Tím se minimalizuje využití prostředků a šetří peníze.

## <a name="batch-net-example"></a>Příklad dávky .NET
Tento fragment kódu rozhraní API [rozhraní Batch služby Batch][api_net] zobrazuje požadavek na vytvoření fondu, který obsahuje čtyři uzly s maximálně čtyřmi úkoly na uzel. Určuje zásadu plánování úkolů, která vyplní každý uzel úkoly před přiřazením úkolů jinému uzlu ve fondu. Další informace o přidávání fondů pomocí rozhraní Batch .NET API naleznete v tématu [BatchClient.PoolOperations.CreatePool][poolcreate_net].

```csharp
CloudPool pool =
    batchClient.PoolOperations.CreatePool(
        poolId: "mypool",
        targetDedicatedComputeNodes: 4
        virtualMachineSize: "standard_d1_v2",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));

pool.MaxTasksPerComputeNode = 4;
pool.TaskSchedulingPolicy = new TaskSchedulingPolicy(ComputeNodeFillType.Pack);
pool.Commit();
```

## <a name="batch-rest-example"></a>Příklad dávky REST
Tento fragment rozhraní API [dávky][api_rest] zobrazuje požadavek na vytvoření fondu, který obsahuje dva velké uzly s maximálně čtyřmi úkoly na uzel. Další informace o přidávání fondů pomocí rozhraní REST API najdete v [tématu Přidání fondu k účtu][rest_addpool].

```json
{
  "odata.metadata":"https://myaccount.myregion.batch.azure.com/$metadata#pools/@Element",
  "id":"mypool",
  "vmSize":"large",
  "cloudServiceConfiguration": {
    "osFamily":"4",
    "targetOSVersion":"*",
  }
  "targetDedicatedComputeNodes":2,
  "maxTasksPerNode":4,
  "enableInterNodeCommunication":true,
}
```

> [!NOTE]
> Element a `maxTasksPerNode` Vlastnost [MaxTasksPerComputeNode][maxtasks_net] můžete nastavit pouze v době vytvoření fondu. Po vytvoření fondu je nelze změnit.
>
>

## <a name="code-sample"></a>Ukázka kódu
Projekt [ParallelNodeTasks][parallel_tasks_sample] na GitHubu ilustruje použití vlastnosti [CloudPool.MaxTasksPerComputeNode.][maxtasks_net]

Tato konzolová aplikace jazyka C# používá knihovnu [Batch .NET][api_net] k vytvoření fondu s jedním nebo více výpočetními uzly. Provádí konfigurovatelný počet úloh na těchto uzlech pro simulaci proměnnézatížení. Výstup z aplikace určuje, které uzly byly provedeny jednotlivými úlohami. Aplikace také poskytuje souhrn parametrů a doby trvání úlohy. Souhrnná část výstupu ze dvou různých spuštění ukázkové aplikace se zobrazí níže.

```
Nodes: 1
Node size: large
Max tasks per node: 1
Tasks: 32
Duration: 00:30:01.4638023
```

První spuštění ukázkové aplikace ukazuje, že s jedním uzlem ve fondu a výchozí nastavení jednoho úkolu na uzel, doba trvání úlohy je více než 30 minut.

```
Nodes: 1
Node size: large
Max tasks per node: 4
Tasks: 32
Duration: 00:08:48.2423500
```

Druhé spuštění vzorku ukazuje významné snížení doby trvání úlohy. Důvodem je, že fond byl nakonfigurován se čtyřmi úkoly na uzel, což umožňuje paralelní provádění úloh k dokončení úlohy v téměř čtvrtinu času.

> [!NOTE]
> Doby trvání úlohy ve výše uvedených souhrnech nezahrnují čas vytvoření fondu. Každá z výše uvedených úloh byla odeslána do dříve vytvořených fondů, jejichž výpočetní uzly byly v době odeslání ve stavu *Nečinnosti.*
>
>

## <a name="next-steps"></a>Další kroky
### <a name="batch-explorer-heat-map"></a>Mapa tepla aplikace Batch Explorer
[Batch Explorer][batch_labs] je bezplatný, bohatý, samostatný klientský nástroj, který pomáhá vytvářet, ladit a monitorovat aplikace Azure Batch. Batch Explorer obsahuje funkci *Heat Map,* která poskytuje vizualizaci provádění úloh. Při provádění [ParallelTasks][parallel_tasks_sample] ukázkové aplikace, můžete použít funkci Heat Map snadno vizualizovat provádění paralelních úloh na každém uzlu.


[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_labs]: https://azure.github.io/BatchExplorer/
[cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[enable_autoscaling]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[fill_type]: https://msdn.microsoft.com/library/microsoft.azure.batch.common.computenodefilltype.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[maxtasks_net]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx
[rest_addpool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[parallel_tasks_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks
[poolcreate_net]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[task_schedule]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudpool.taskschedulingpolicy.aspx

