---
title: Spuštění úlohy paralelní efektivně - používat výpočetní prostředky Azure Batch | Microsoft Docs
description: Zvýšení efektivity a snížení nákladů pomocí méně výpočetních uzlů a spuštění souběžných úkolů na každém uzlu ve fondu Azure Batch
services: batch
documentationcenter: .net
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: 538a067c-1f6e-44eb-a92b-8d51c33d3e1a
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 05/22/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5106bbbb073908af7e7e8f045fa6fb60e8a306f4
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
ms.locfileid: "30316908"
---
# <a name="run-tasks-concurrently-to-maximize-usage-of-batch-compute-nodes"></a>Spuštění úloh souběžně chcete maximalizovat využití služby Batch výpočetních uzlů 

Při spuštění více než jeden úkol současně na každém výpočetním uzlu ve fondu Azure Batch, můžete zvýšit využití prostředků na menší počet uzlů ve fondu. Pro některé úlohy to může způsobit kratší časy úloh a nižší náklady.

Při některých scénářích využívat všechny prostředky uzlu vyhradit pro jeden úkol, několika situacích těžit z povolení více úkolů ke sdílení těchto prostředků:

* **Minimalizace přenos dat** úkoly, které mají moci sdílet data. V tomto scénáři můžete výrazně snížit poplatky za přenos dat tak, že sdílená data na menší počet uzlů a provádění úloh paralelně na každém uzlu. To platí hlavně pokud data, která mají být zkopírovány do každého uzlu musí být přenesena mezi zeměpisné oblasti.
* **Tím se maximalizuje využití paměti** při úlohy vyžadovat velké množství paměti, ale pouze během krátkodobě dobu a v proměnné dobu během provádění. Můžete použít méně, ale větší, výpočetní uzly s vyšším rozsahem paměti pro efektivní zpracování takové špičky. Tyto uzly by mít více úloh s paralelně na jednotlivých uzlech spuštěné, ale každý úkol by využívat bohaté paměti uzlů v různých časech.
* **Zmírnění číslo omezení uzlu** při komunikaci mezi uzly vyžádáním v rámci fondu. Fondy, které jsou nakonfigurované pro komunikaci mezi uzly v současné době jsou omezeny na 50 výpočetních uzlů. Pokud každý uzel v takové fondu je provést v paralelní úlohy, větší počet úloh mohou být provedeny souběžně.
* **Replikace místní výpočetního clusteru**, například když je nejprve přesunout výpočetním prostředí do Azure. Pokud vaše aktuální řešení místní provede několik úkolů na výpočetním uzlu, můžete zvýšit maximální počet úkolů uzel pro přesněji zrcadlení pro tuto konfiguraci.

## <a name="example-scenario"></a>Příklad scénáře
Jako příklad k objasnění výhod provedení paralelní úlohy Řekněme, že aplikace úkolů má požadavky na procesor a paměť tak, aby [standardní\_D1](../cloud-services/cloud-services-sizes-specs.md) postačí uzly. Ale, aby bylo možné dokončit úlohy v požadované době, jsou potřeba 1000 tyto uzly.

Místo použití standardní\_D1 uzly, které mají 1 jádro procesoru, můžete použít [standardní\_D14](../cloud-services/cloud-services-sizes-specs.md) uzlů, které mají 16 jader a povolit spouštění paralelních úloh. Proto *16 časy méně uzly* může – místo 1000 uzly jen 63 by bylo zapotřebí. Kromě toho pokud aplikace velké soubory nebo referenční data jsou vyžadovány pro každý uzel, dobu trvání úlohy a efektivitu znovu lepší vzhledem k tomu, že data budou zkopírována do pouze 63 uzlů.

## <a name="enable-parallel-task-execution"></a>Povolit provedení paralelní úlohy
Výpočetní uzly pro provedení paralelní úlohy nakonfigurujete na úrovni fondu. Pomocí knihovny Batch .NET, nastavte [CloudPool.MaxTasksPerComputeNode] [ maxtasks_net] vlastnost při vytváření fondu. Pokud používáte rozhraní REST API služby Batch, nastavte [maxTasksPerNode] [ rest_addpool] element v těle žádosti během vytváření fondu.

Azure Batch umožňuje nastavit maximální úkolů na uzel až čtyřikrát (4 x) počet jader na uzel. Například pokud fondu je nakonfigurovaný s uzly velikost "Velké" (4 jádra), pak `maxTasksPerNode` může být nastaven na hodnotu 16. Podrobnosti na počtu jader pro každý uzel velikosti najdete v tématu [velikosti pro cloudové služby](../cloud-services/cloud-services-sizes-specs.md). Další informace o omezení služby najdete v tématu [kvóty a omezení pro službu Azure Batch](batch-quota-limit.md).

> [!TIP]
> Je nutné vzít v úvahu `maxTasksPerNode` hodnota při vytváření [vzorec škálování] [ enable_autoscaling] fondu. Například vzorec, jehož výsledkem `$RunningTasks` může mít vliv výrazně zvýšit úkolů na uzlu. V tématu [automatické škálování výpočetních uzlů ve fondu Azure Batch](batch-automatic-scaling.md) Další informace.
>
>

## <a name="distribution-of-tasks"></a>Rozdělení úkolů
Když výpočetní uzly ve fondu, můžete současně provést úlohy, je důležité určit, jak má úkoly, které mají být rozdělené mezi uzly ve fondu.

Pomocí [CloudPool.TaskSchedulingPolicy] [ task_schedule] vlastnost, můžete určit, že úlohy by se měla přiřadit rovnoměrně mezi všechny uzly ve fondu ("rozšíření"). Nebo můžete zadat, aby co nejvíce úkolů by měla být přiřazená každý uzel před úlohy jsou přiřazeny do jiného uzlu ve fondu ("okolních").

Jako příklad, jak tato funkce se hodí v situaci, zvažte fondu [standardní\_D14](../cloud-services/cloud-services-sizes-specs.md) uzly (v předchozím příkladu), které je nakonfigurované [CloudPool.MaxTasksPerComputeNode] [ maxtasks_net] hodnotu 16. Pokud [CloudPool.TaskSchedulingPolicy] [ task_schedule] nastavena [ComputeNodeFillType] [ fill_type] z *Pack*, by maximalizovat využití všech 16 jader každého uzlu a povolit [automatické škálování fondu](batch-automatic-scaling.md) Chcete-li vyřadit nepoužívá uzly z fondu (uzlů bez přiřazeny žádné úkoly). To snižuje využití prostředků a úsporu nákladů.

## <a name="batch-net-example"></a>Příklad batch .NET
To [Batch .NET] [ api_net] rozhraní API fragment kódu ukazuje požadavek na vytvoření fondu, který obsahuje čtyři uzly velké s maximálně čtyřmi úkolů na uzlu. Určuje úlohu plánování zásad, který naplní každý uzel úkolech před přiřazením úkolů do jiného uzlu ve fondu. Další informace o přidání fondů pomocí rozhraní Batch .NET API najdete v tématu [BatchClient.PoolOperations.CreatePool][poolcreate_net].

```csharp
CloudPool pool =
    batchClient.PoolOperations.CreatePool(
        poolId: "mypool",
        targetDedicatedComputeNodes: 4
        virtualMachineSize: "large",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));

pool.MaxTasksPerComputeNode = 4;
pool.TaskSchedulingPolicy = new TaskSchedulingPolicy(ComputeNodeFillType.Pack);
pool.Commit();
```

## <a name="batch-rest-example"></a>Příklad batch REST
To [Batch REST] [ api_rest] rozhraní API fragment kódu ukazuje požadavek na vytvoření fondu, který obsahuje dva uzly velké s maximálně čtyřmi úkolů na uzlu. Další informace o přidání fondů pomocí rozhraní REST API najdete v tématu [přidat fond na účet][rest_addpool].

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
> Můžete nastavit `maxTasksPerNode` elementu a [MaxTasksPerComputeNode] [ maxtasks_net] vlastnost pouze při vytváření fondu. Nemůže být upraven po fond již byly vytvořeny.
>
>

## <a name="code-sample"></a>Ukázka kódu
[ParallelNodeTasks] [ parallel_tasks_sample] projektu na Githubu ukazuje použití [CloudPool.MaxTasksPerComputeNode] [ maxtasks_net] vlastnost.

Tato Konzolová aplikace C# používá [Batch .NET] [ api_net] knihovny a vytvoření fondu pomocí jednoho nebo více výpočetních uzlů. Konfigurovat řadu úloh se provede na těchto uzlech k simulaci proměnné zatížení. Výstup z aplikace určuje, které uzly provést každý úkol. Aplikace také poskytuje souhrn parametry úlohy a doba trvání. Souhrn část výstup ze dvou různých spuštění ukázkové aplikace se zobrazí pod.

```
Nodes: 1
Node size: large
Max tasks per node: 1
Tasks: 32
Duration: 00:30:01.4638023
```

První spuštění ukázkové aplikace ukazuje, že se jeden uzel ve fondu a výchozí nastavení pro jeden úkol na uzlu, že dobu trvání úlohy je více než 30 minut.

```
Nodes: 1
Node size: large
Max tasks per node: 4
Tasks: 32
Duration: 00:08:48.2423500
```

Druhý spuštění ukázkové ukazuje výrazného poklesu dobu trvání úlohy. Je to proto, že fond byl nakonfigurován s čtyři úkolů na uzlu, který umožňuje provádění paralelních úkolů dokončete úlohu v téměř čtvrtletí času.

> [!NOTE]
> Doby trvání úlohy ve výše uvedené souhrny nezahrnují čas vytvoření fondu. Každá z výše uvedených úloh byla odeslána na dříve vytvořenou fondů, jejichž výpočetní uzly se *nečinnosti* během odesílání stavu.
>
>

## <a name="next-steps"></a>Další postup
### <a name="batchlabs-heat-map"></a>BatchLabs Heat mapa
[BatchLabs][batch_labs] je bezplatný a samostatný klientský nástroj s bohatými funkcemi, který pomáhá vytvářet, ladit a monitorovat aplikace Azure Batch. Obsahuje BatchLabs *Heat mapa* funkce, která poskytuje vizualizaci provedení úlohy. Pokud jste provádění [ParallelTasks] [ parallel_tasks_sample] ukázkovou aplikaci, můžete použít funkci Heat mapa můžete snadno vizualizovat provádění paralelních úloh na každém uzlu.


[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_labs]: https://azure.github.io/BatchLabs/
[cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[enable_autoscaling]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[fill_type]: https://msdn.microsoft.com/library/microsoft.azure.batch.common.computenodefilltype.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[maxtasks_net]: http://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx
[rest_addpool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[parallel_tasks_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks
[poolcreate_net]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[task_schedule]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudpool.taskschedulingpolicy.aspx

