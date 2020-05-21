---
title: Souběžné spouštění úloh pro optimalizaci výpočetních prostředků
description: Zvýšení efektivity a snížení nákladů pomocí menšího počtu výpočetních uzlů a spuštění souběžných úkolů na každém uzlu ve fondu Azure Batch
ms.topic: how-to
ms.date: 04/17/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8d38076396ea89eed9e1ef0c2e9ba14cddfd7cc6
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2020
ms.locfileid: "83724186"
---
# <a name="run-tasks-concurrently-to-maximize-usage-of-batch-compute-nodes"></a>Souběžné spouštění úloh pro maximalizaci využití výpočetních uzlů Batch 

Spuštěním více než jedné úlohy na každém výpočetním uzlu ve fondu Azure Batch můžete maximalizovat využití prostředků na menším počtu uzlů ve fondu. U některých úloh to může mít za následek kratší dobu úlohy a nižší náklady.

I když některé scénáře mají za úkol vyhradit všechny prostředky uzlu na jednu úlohu, několik situací je výhodné při povolování sdílení těchto prostředků několika úkoly:

* **Minimalizace přenosu dat** , když úlohy můžou sdílet data. V tomto scénáři můžete významně snížit poplatky za přenos dat tím, že zkopírujete sdílená data do menšího počtu uzlů a paralelně spustíte úlohy na každém uzlu. To platí hlavně v případě, že se data, která mají být kopírována do každého uzlu, musí přenášet mezi geografickými oblastmi.
* **Maximalizace využití paměti** v případě, kdy úkoly vyžadují velké množství paměti, ale pouze během krátkých časových úseků a při proměnlivých časech během provádění. Můžete využít méně, ale větší výpočetní uzel s větší pamětí, abyste mohli efektivně zvládnout takové špičky. Tyto uzly budou mít paralelně spuštěné více úloh na každém uzlu, ale každá úloha by mohla využít plentiful paměti uzlů v různou dobu.
* **Zmírnění omezení počtu uzlů** , pokud se v rámci fondu vyžaduje komunikace mezi uzly. V současné době jsou fondy nakonfigurované pro komunikaci mezi uzly omezeny na 50 výpočetních uzlů. Pokud každý uzel v takovém fondu může spouštět úlohy paralelně, může se spustit větší počet úkolů současně.
* **Replikace místního výpočetního clusteru**, například když nejprve přesunete výpočetní prostředí do Azure. Pokud vaše aktuální místní řešení spouští více úloh na výpočetní uzel, můžete zvýšit maximální počet úloh uzlu, aby se tato konfigurace podrobněji rozrážejí.

## <a name="example-scenario"></a>Ukázkový scénář
Jako příklad pro ilustraci výhod paralelního provádění úloh řekněme, že vaše aplikace úkolu má požadavky na procesor a paměť, což znamená, že [standardní uzly \_ D1](../cloud-services/cloud-services-sizes-specs.md) jsou dostatečné. Aby bylo možné dokončit úlohu v požadovaném čase, je 1 000 potřebných uzlů.

Místo používání standardních \_ uzlů D1 s 1 jádrem procesoru můžete použít [standardní uzly \_ D14](../cloud-services/cloud-services-sizes-specs.md) s 16 jádry každého a povolit paralelní provádění úkolů. Proto by bylo možné použít *maximálně 16 uzlů* – místo 1 000 uzlů by se vyžadoval pouze 63. Kromě toho, pokud jsou pro každý uzel požadovány velké soubory aplikace nebo referenční data, jsou znovu vylepšena doba trvání a účinnost úlohy, protože data jsou zkopírována pouze do 63 uzlů.

## <a name="enable-parallel-task-execution"></a>Povolit spuštění paralelní úlohy
Výpočetní uzly můžete nakonfigurovat pro provádění paralelních úkolů na úrovni fondu. Pomocí knihovny Batch .NET nastavte při vytváření fondu vlastnost [CloudPool. MaxTasksPerComputeNode][maxtasks_net] . Pokud používáte REST API dávky, nastavte v textu žádosti během vytváření fondu prvek [maxTasksPerNode][rest_addpool] .

Azure Batch umožňuje nastavit úlohy na jeden uzel až (4x) na počet základních uzlů. Například pokud je fond nakonfigurovaný s uzly o velikosti "velký" (čtyři jádra), `maxTasksPerNode` může se nastavit na 16. Bez ohledu na to, kolik jader má uzel, ale nemůžete mít více než 256 úkolů na jeden uzel. Podrobnosti o počtu jader pro jednotlivé velikosti uzlů najdete v tématu [velikosti pro Cloud Services](../cloud-services/cloud-services-sizes-specs.md). Další informace o omezeních služeb najdete v tématu [kvóty a omezení pro službu Azure Batch](batch-quota-limit.md).

> [!TIP]
> Nezapomeňte vzít v úvahu `maxTasksPerNode` hodnotu při vytváření [vzorce automatického škálování][enable_autoscaling] pro váš fond. Například vzorec, který vyhodnocuje, `$RunningTasks` může výrazně ovlivnit zvýšení počtu úkolů na uzel. Další informace najdete v tématu [Automatické škálování výpočetních uzlů ve fondu Azure Batch](batch-automatic-scaling.md) .
>
>

## <a name="distribution-of-tasks"></a>Distribuce úloh
Když výpočetní uzly ve fondu můžou úlohy spouštět souběžně, je důležité určit, jak se mají úlohy distribuovat mezi uzly ve fondu.

Pomocí vlastnosti [CloudPool. TaskSchedulingPolicy][task_schedule] můžete určit, že se mají úlohy přiřadit rovnoměrně mezi všechny uzly ve fondu ("rozprostření"). Nebo můžete určit, že k jednotlivým uzlům by se měly přiřadit tolik úkolů, než se úkoly přiřadí do jiného uzlu ve fondu ("balení").

Jako příklad toho, jak je tato funkce užitečná, vezměte v úvahu fond [standardních \_ D14](../cloud-services/cloud-services-sizes-specs.md) uzlů (v předchozím příkladu), který je nakonfigurovaný s hodnotou [CloudPool. MaxTasksPerComputeNode][maxtasks_net] 16. Pokud je [CloudPool. TaskSchedulingPolicy][task_schedule] nakonfigurovaný s [ComputeNodeFillType][fill_type] *packem*, může maximalizovat využití všech 16 jader každého uzlu a umožňuje [fondu automatického škálování](batch-automatic-scaling.md) vyřadit nepoužívané uzly z fondu (uzly bez přiřazených úkolů). Tím se minimalizuje využití prostředků a šetří peníze.

## <a name="batch-net-example"></a>Příklad dávky .NET
Tento fragment kódu pro [dávku .NET][api_net] API zobrazuje požadavek na vytvoření fondu, který obsahuje čtyři uzly s maximálně čtyřmi úkoly na uzel. Určuje zásadu plánování úkolů, která před přiřazením úkolů jinému uzlu ve fondu vyplní každý uzel úkoly. Další informace o přidávání fondů pomocí rozhraní API služby Batch najdete v tématu [BatchClient. PoolOperations. CreatePool][poolcreate_net].

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

## <a name="batch-rest-example"></a>Příklad dávky v dávce
Tento fragment kódu rozhraní [REST API dávky][api_rest] zobrazuje požadavek na vytvoření fondu, který obsahuje dva velké uzly s maximálně čtyřmi úkoly na uzel. Další informace o přidávání fondů pomocí REST API najdete v tématu [Přidání fondu k účtu][rest_addpool].

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
> `maxTasksPerNode`Vlastnost element a [MaxTasksPerComputeNode][maxtasks_net] lze nastavit pouze v okamžiku vytvoření fondu. Po vytvoření fondu již nelze tyto změny změnit.
>
>

## <a name="code-sample"></a>Ukázka kódu
Projekt [ParallelNodeTasks][parallel_tasks_sample] na GitHubu ilustruje použití vlastnosti [CloudPool. MaxTasksPerComputeNode][maxtasks_net] .

Tato Konzolová aplikace v jazyce C# používá knihovnu [Batch .NET][api_net] k vytvoření fondu s jedním nebo více výpočetními uzly. Spustí na těchto uzlech konfigurovatelný počet úloh pro simulaci zatížení proměnných. Výstup z aplikace určuje, které uzly provedly jednotlivé úlohy. Aplikace také poskytuje souhrn parametrů a doby trvání úloh. Souhrnná část výstupu ze dvou různých spuštění ukázkové aplikace se zobrazuje níže.

```
Nodes: 1
Node size: large
Max tasks per node: 1
Tasks: 32
Duration: 00:30:01.4638023
```

První spuštění ukázkové aplikace ukazuje, že s jedním uzlem ve fondu a s výchozím nastavením jednoho úkolu na uzel je doba trvání úlohy více než 30 minut.

```
Nodes: 1
Node size: large
Max tasks per node: 4
Tasks: 32
Duration: 00:08:48.2423500
```

Druhý běh ukázky ukazuje výrazné snížení doby trvání úlohy. Důvodem je to, že fond byl nakonfigurován se čtyřmi úkoly na uzel, což umožňuje paralelní spouštění úkolů dokončit úlohu téměř ve čtvrtletí času.

> [!NOTE]
> Doby trvání úloh v souhrnech výše nezahrnují čas vytvoření fondu. Každá z výše uvedených úloh byla odeslána do dříve vytvořených fondů, jejichž výpočetní uzly byly ve stavu *nečinnosti* v době odeslání.
>
>

## <a name="next-steps"></a>Další kroky
### <a name="batch-explorer-heat-map"></a>Batch Explorer Heat mapa
[Batch Explorer][batch_labs] je bezplatný a samostatný klientský nástroj s bohatými funkcemi, který vám umožní vytvářet, ladit a monitorovat Azure Batch aplikace. Batch Explorer obsahuje funkci *Heat mapy* , která poskytuje vizualizaci provádění úlohy. Když spouštíte ukázkovou aplikaci [ParallelTasks][parallel_tasks_sample] , můžete pomocí funkce heat mapa snadno vizualizovat spouštění paralelních úloh na jednotlivých uzlech.


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

