---
title: Paralelnímu spouštění úloh - efektivně používat výpočetní prostředky služby Azure Batch | Dokumentace Microsoftu
description: Zvýšení efektivity a snížení nákladů s použitím menšího počtu výpočetních uzlů a spuštění souběžných úloh na každém uzlu ve fondu služby Azure Batch
services: batch
documentationcenter: .net
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: 538a067c-1f6e-44eb-a92b-8d51c33d3e1a
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 05/22/2017
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6c4fd63213eaaf402bd72b1754ba50a9ea35e965
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55475427"
---
# <a name="run-tasks-concurrently-to-maximize-usage-of-batch-compute-nodes"></a>Spuštění úloh souběžně, což umožňuje maximalizovat využití služby Batch výpočetních uzlů 

Spuštěním několika dílčích úloh souběžně na jednotlivých výpočetních uzlech ve fondu Azure Batch, můžete maximalizovat využití prostředků na menší počet uzlů ve fondu. Pro některé úlohy to může způsobit kratší časy úloh a snížení nákladů.

Zatímco některé scénáře využívat všechny prostředky uzlu vyhradíte pro jednu úlohu, situacích těžit z povolení více úkolů ke sdílení těchto prostředků:

* **Minimalizace přenos dat** úkoly, které mají moct sdílet data. V tomto scénáři můžete výrazně snížit poplatky za přenos dat zkopírováním sdílených dat na menší počet uzlů a provádění úloh paralelně na jednotlivých uzlech. To platí hlavně pokud mezi geografických oblastí. musí být přenášena data, která mají být zkopírovány do každého uzlu.
* **Maximalizace využití paměti** při úlohy vyžadují velké množství paměti, ale pouze během krátkou dobu a v proměnné časech během provádění. Můžete použít míň, ale větší, výpočetní uzly s více paměti pro efektivní zpracování těchto provozní špičky. Tyto uzly by mít více úloh paralelně na jednotlivých uzlech spuštěné, ale každý úkol by mohli využívat bohaté paměti na uzly v různých časech.
* **Snížení rizik souvisejících s uzel číselné limity** při komunikaci mezi uzly vyžádáním v rámci fondu. V současné době nakonfigurován pro komunikaci mezi uzly fondy jsou omezené na 50 výpočetních uzlů. Pokud všech uzlů v těchto fondu je možné spustit paralelně úloh, větší počet úkolů, mohou být provedeny souběžně.
* **Replikace místní výpočetní cluster**, jako je například při prvním přesunu výpočetních prostředí do Azure. Pokud vaše aktuální místní řešení spustí více úkolů na výpočetním uzlu, můžete zvýšit maximální počet úkolů uzel lépe zrcadlící tuto konfiguraci.

## <a name="example-scenario"></a>Příklad scénáře
Jako příklad pro ilustraci výhody paralelní zpracování úkolů, Řekněme, že má vaše aplikace úkolů požadavky na CPU a paměti tak, aby [standardní\_D1](../cloud-services/cloud-services-sizes-specs.md) uzly jsou dostačující. Ale, abyste mohli dokončit úlohy v požadovaném čase, jsou potřeba 1 000 z těchto uzlů.

Namísto použití standardní\_uzly D1, které mají 1 jádra procesoru, které můžete využít [standardní\_D14](../cloud-services/cloud-services-sizes-specs.md) uzlů, které s 16 jádry a povolit paralelní zpracování úkolů. Proto *16krát méně uzlů* může být použit – místo 1000 uzlů, pouze 63 by bylo zapotřebí. Kromě toho pokud se vyžaduje pro každý uzel rozsáhlé aplikace soubory nebo referenční data, doba trvání úlohy a efektivitu znovu lepší vzhledem k tomu, že se data kopírují do 63 pouze uzly.

## <a name="enable-parallel-task-execution"></a>Povolit paralelní zpracování úkolů
Konfigurace výpočetních uzlů pro paralelní zpracování úkolů na úrovni fondu. Pomocí knihovny Batch .NET, nastavte [CloudPool.MaxTasksPerComputeNode] [ maxtasks_net] při vytváření fondu. Pokud používáte rozhraní REST API služby Batch, nastavte [maxTasksPerNode] [ rest_addpool] element v textu požadavku během vytváření fondu.

Služba Azure Batch umožňuje nastavit maximální úkolů na uzel až čtyřikrát (4 x) počet jader na uzel. Například pokud je nakonfigurovaný fond s uzly velikosti "Velký" (4 jádra), pak `maxTasksPerNode` může být nastaven na hodnotu 16. Podrobnosti na počet jader pro jednotlivé velikosti uzlů najdete v tématu [velikosti pro Cloud Services](../cloud-services/cloud-services-sizes-specs.md). Další informace o omezení služby najdete v tématu [kvóty a omezení pro službu Azure Batch](batch-quota-limit.md).

> [!TIP]
> Je potřeba vzít v úvahu `maxTasksPerNode` hodnotu při vytváření [vzorec automatického škálování] [ enable_autoscaling] pro váš fond. Například vzorec, který se vyhodnotí `$RunningTasks` může výrazně ovlivňovat prodloužením úkolů na uzlu. Zobrazit [automatické škálování výpočetních uzlů ve fondu služby Azure Batch](batch-automatic-scaling.md) Další informace.
>
>

## <a name="distribution-of-tasks"></a>Rozdělení úkolů
Když výpočetních uzlů ve fondu, můžete souběžně spouštět úlohy, je důležité určit, jak má úkoly, které mají být distribuovány napříč uzly ve fondu.

S použitím [CloudPool.TaskSchedulingPolicy] [ task_schedule] vlastností, můžete určit, že úlohy by se měla přiřadit rovnoměrně mezi všechny uzly ve fondu ("rozšíření"). Nebo můžete určit, co nejvíce úkolů by měla být přiřazena každý uzel před úkoly jsou přiřazeny do jiného uzlu ve fondu ("komprimace").

Jako příklad toho, jak tato funkce je užitečná, vezměte v úvahu fondu [standardní\_D14](../cloud-services/cloud-services-sizes-specs.md) uzly (v předchozím příkladu), které je nakonfigurované [CloudPool.MaxTasksPerComputeNode] [ maxtasks_net] hodnotu 16. Pokud [CloudPool.TaskSchedulingPolicy] [ task_schedule] má nakonfigurovanou [ComputeNodeFillType] [ fill_type] z *Pack*, by maximalizovat využití všech 16 jader každého uzlu a povolit [automatické škálování fondu](batch-automatic-scaling.md) Chcete-li vyřadit nevyužité uzly z fondu (uzly bez přiřazeny žádné úkoly). Tím se minimalizuje využití prostředků a šetří peníze.

## <a name="batch-net-example"></a>Příklad rozhraní .NET služby batch
To [Batch .NET] [ api_net] API fragment kódu ukazuje požadavek na vytvoření fondu, který obsahuje čtyři uzly s délkou maximálně čtyři úkolů na uzlu. Určuje úlohu plánování zásadu, která bude každý uzel vyplnit úkolů před přiřazením úkolů do jiného uzlu ve fondu. Další informace o přidání fondů pomocí rozhraní API .NET služby Batch najdete v tématu [BatchClient.PoolOperations.CreatePool][poolcreate_net].

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

## <a name="batch-rest-example"></a>Příklad REST služby batch
To [Batch REST] [ api_rest] API fragment kódu ukazuje požadavek na vytvoření fondu, který obsahuje dva uzly velké s délkou maximálně čtyři úkolů na uzlu. Další informace o přidávání fondů pomocí rozhraní REST API najdete v tématu [přidání fondu k účtu][rest_addpool].

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
> Můžete nastavit `maxTasksPerNode` elementu a [MaxTasksPerComputeNode] [ maxtasks_net] vlastnost pouze při vytváření fondu. Nelze změnit poté, co byl vytvořen fond.
>
>

## <a name="code-sample"></a>Ukázka kódu
[ParallelNodeTasks] [ parallel_tasks_sample] projektu na Githubu ilustruje použití objektu [CloudPool.MaxTasksPerComputeNode] [ maxtasks_net] vlastnost.

Tato Konzolová aplikace jazyka C# používá [Batch .NET] [ api_net] knihovny k vytvoření fondu pomocí jednoho nebo víc výpočetních uzlů. Konfigurovat řadu úkolů se provede na těchto uzlech pro simulaci zatížení. Výstup z aplikace určuje uzly, na kterých spuštění každé úlohy. Aplikace také poskytuje přehled parametry úlohy a dobu trvání. Souhrnné části výstupu ze dvou různých spuštění ukázkové aplikace se zobrazí pod.

```
Nodes: 1
Node size: large
Max tasks per node: 1
Tasks: 32
Duration: 00:30:01.4638023
```

Dojde k prvnímu spuštění ukázkové aplikace ukazuje, že se jeden uzel ve fondu a ve výchozím nastavení jeden úkol na uzlu, že doba trvání úlohy je více než 30 minut.

```
Nodes: 1
Node size: large
Max tasks per node: 4
Tasks: 32
Duration: 00:08:48.2423500
```

Druhý ukazuje ukázkové spuštění výrazného poklesu doba trvání úlohy. Je to proto, že fond byl nakonfigurován s čtyři úkolů na uzlu, který umožňuje paralelní zpracování úkolů na dokončení úlohy v téměř čtvrtletí času.

> [!NOTE]
> Úloha doby trvání v souhrny výše nezahrnují čas vytvoření fondu. Každý z výše uvedené úlohy byla odeslána do dříve vytvořené fondy, výpočetní uzly byly v *nečinný* stavu při odesílání.
>
>

## <a name="next-steps"></a>Další postup
### <a name="batch-explorer-heat-map"></a>Batch Explorer Heat mapa
[Batch Explorer] [ batch_labs] je zdarma, bohatými funkcemi, samostatný klientský nástroj umožňující vytvářet, ladit a monitorovat aplikace Azure Batch. Obsahuje Průzkumníka služby batch *Heat mapa* funkce, která poskytuje vizualizaci provádění úkolu. Když provedete [ParallelTasks] [ parallel_tasks_sample] ukázkovou aplikaci, můžete použít funkci Heat mapa snadno vizualizovat spuštění paralelní úlohy na každém uzlu.


[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_labs]: https://azure.github.io/BatchExplorer/
[cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[enable_autoscaling]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[fill_type]: https://msdn.microsoft.com/library/microsoft.azure.batch.common.computenodefilltype.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[maxtasks_net]: http://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx
[rest_addpool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[parallel_tasks_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks
[poolcreate_net]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[task_schedule]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudpool.taskschedulingpolicy.aspx

