---
title: Návrh efektivní seznamové dotazy – Azure Batch | Dokumentace Microsoftu
description: Zvýšení výkonu pomocí filtrování dotazů při žádosti o prostředky služby Batch, jako jsou fondy, úlohy, úkoly a výpočetních uzlů.
services: batch
documentationcenter: .net
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: 031fefeb-248e-4d5a-9bc2-f07e46ddd30d
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 12/07/2018
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: fc873f68be3e7aad67980ec2e8ee0b2e473777ec
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53537897"
---
# <a name="create-queries-to-list-batch-resources-efficiently"></a>Efektivně vytvářet dotazy do seznamu prostředků služby Batch

Zde se dozvíte, jak zvýšit výkon vaší aplikace Azure Batch snížením množství dat, která je vrácena službou při dotazování úlohy, úkoly, výpočetních uzlů a jiných prostředků pomocí [Batch .NET] [ api_net] knihovny.

Téměř všechny aplikace Batch musí provádí nějaký typ monitorování nebo jiné operace, který se často dotazuje službu Batch v pravidelných intervalech. Pokud chcete zjistit, zda jsou všechny zařazených do fronty úloh zbývajících v rámci úlohy, například musí získat data pro každý úkol v úloze. Pokud chcete zjistit stav uzlů ve fondu, musíte získat data na všech uzlech ve fondu. Tento článek vysvětluje, jak k provádění těchto dotazů nejefektivnějším způsobem.

> [!NOTE]
> Služba Batch podporuje speciální rozhraní API pro běžné scénáře, počítací úkoly v úloze a počítání výpočetních uzlů ve fondu služby Batch. Namísto použití dotaz typu seznam na tyto, můžete volat [získat počty úloh] [ rest_get_task_counts] a [seznamu fond uzel počítá] [ rest_get_node_counts] operace. Tyto operace jsou efektivnější než dotaz na seznam, ale vrátit více omezené informace. Zobrazit [počet úloh a výpočetní uzel podle stavu](batch-get-resource-counts.md). 


## <a name="meet-the-detaillevel"></a>Splnění DetailLevel
V produkční aplikaci služby Batch můžete entity jako výpočetní uzly, úlohy a úkoly čísel v tisících. Pokud budete požadovat informace o těchto prostředků, potenciálně velkého množství dat musí "napříč přenosu" ze služby Batch do vaší aplikace při každém dotazu. Tím, že omezíte počet položek a typu informací, která je vrácena dotazem, můžete zvýšit rychlost vašich dotazů a proto výkon vaší aplikace.

To [Batch .NET] [ api_net] seznamy fragment kódu rozhraní API *každý* úkol, který je spojený s úlohou, spolu s *všechny* z vlastností pro každou úlohu:

```csharp
// Get a collection of all of the tasks and all of their properties for job-001
IPagedEnumerable<CloudTask> allTasks =
    batchClient.JobOperations.ListTasks("job-001");
```

Mnohem efektivnější seznam dotazů, můžete však provést použitím "úroveň podrobností" na váš dotaz. To provedete zadáním [ODATADetailLevel] [ odata] objektu [JobOperations.ListTasks] [ net_list_tasks] metody. Tento fragment kódu vrátí pouze ID, příkazový řádek a výpočetní uzel informace vlastnosti dokončené úkoly:

```csharp
// Configure an ODATADetailLevel specifying a subset of tasks and
// their properties to return
ODATADetailLevel detailLevel = new ODATADetailLevel();
detailLevel.FilterClause = "state eq 'completed'";
detailLevel.SelectClause = "id,commandLine,nodeInfo";

// Supply the ODATADetailLevel to the ListTasks method
IPagedEnumerable<CloudTask> completedTasks =
    batchClient.JobOperations.ListTasks("job-001", detailLevel);
```

V tomto ukázkovém scénáři, pokud existuje spousta úkolů v úloze, výsledky z druhého dotazu se obvykle vrátí mnohem rychlejší než první. Další informace o používání ODATADetailLevel při seznamu položky, které rozhraní API .NET služby Batch je součástí [níže](#efficient-querying-in-batch-net).

> [!IMPORTANT]
> Důrazně doporučujeme vám *vždy* zadat objekt ODATADetailLevel seznam volání rozhraní API .NET zajistit maximální účinnosti a výkonu vaší aplikace. Zadáním úroveň podrobností může pomoct snížit dobu odezvy služby Batch, zlepšit využití sítě a minimalizuje využití paměti u klientských aplikací.
> 
> 

## <a name="filter-select-and-expand"></a>Filtrovat, vyberte a rozbalte
[Batch .NET] [ api_net] a [Batch REST] [ api_rest] rozhraní API umožňují snížit počet položek, které jsou vráceny v seznamu, jakož i množství informací, které se vrací pro každý. To uděláte tak, že zadáte **filtr**, **vyberte**, a **rozbalte řetězce** při provádění dotazů seznamu.

### <a name="filter"></a>Filtr
Řetězec filtru je výraz, který omezuje počet položek, které jsou vráceny. Například pouze spuštěné úkoly pro úlohy nebo seznamu pouze výpočetní uzly, které jsou připravené ke spuštění úlohy.

* Řetězec filtru se skládá z jednoho nebo více výrazů s výrazem, který se skládá z názvu vlastnosti, operátor a hodnotu. Vlastnosti, které je možné zadat jsou specifické pro každý typ entity, která odešlete dotaz, jako jsou operátory, které jsou podporovány pro každou vlastnost.
* Více výrazů můžete kombinovat pomocí logické operátory `and` a `or`.
* V tomto příkladu filtrovat seznamy řetězec jen pro spuštění úlohy "vykreslení": `(state eq 'running') and startswith(id, 'renderTask')`.

### <a name="select"></a>Vyberte
Vyberte řetězec omezuje hodnoty vlastností, které pro každou položku jsou vráceny. Zadejte seznam názvů vlastností a položek ve výsledcích dotazu jsou vráceny pouze hodnoty těchto vlastností.

* Vyberte řetězec se skládá z čárkou oddělený seznam názvů vlastností. Můžete určit kterékoli z vlastnosti pro typ entity, který se dotazuje.
* Tento příklad vyberte řetězec Určuje, že má být vrácen pouze tři hodnoty vlastností pro každou úlohu: `id, state, stateTransitionTime`.

### <a name="expand"></a>Rozbalit
Řetězec rozbalení snižuje počet volání rozhraní API, které jsou nutné k získání určitých informací. Při použití jako řetězec rozbalení lze získat další informace o jednotlivých položkách pomocí jediného volání rozhraní API. Místo první získání seznamu entit, pak požaduje informace pro každou položku v seznamu, použijte řetězec rozbalení získat stejné informace v jednom volání rozhraní API. Méně volání rozhraní API znamená vyšší výkon.

* Podobně jako když vyberte řetězec, řetězec rozbalení řídí, jestli je určitá data součástí seznamu výsledků dotazu.
* Řetězec rozbalení se podporuje jenom při použití v zobrazení úlohy, plány úloh, úkolů a fondy. V současné době podporuje pouze statistické informace.
* Když jsou povinné všechny vlastnosti a není určen žádný vyberte řetězec, řetězec rozbalení *musí* použít k získání statistické informace. Pokud vyberte řetězec se používá k získání podmnožiny vlastností, pak `stats` lze upravit v řetězci, vyberte a není nutné zadat řetězec rozbalení.
* V tomto příkladu rozbalte položku řetězec Určuje, zda má být vrácen statistické informace pro každou položku v seznamu: `stats`.

> [!NOTE]
> Při vytváření jakýkoli z typů řetězec tři dotazu (filtrování, vyberte a rozbalte), musíte zajistit, že názvy vlastností a případ odpovídat jejich protějšky element rozhraní REST API. Například při práci s .NET [CloudTask](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask) třídy, je nutné zadat **stavu** místo **stavu**, i když je vlastnost .NET [ CloudTask.State](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.state). Najdete v následujících tabulkách pro mapování vlastností mezi .NET a rozhraní REST API.
> 
> 

### <a name="rules-for-filter-select-and-expand-strings"></a>Pravidla pro filtr, vyberte a rozbalte řetězce
* Názvy vlastností ve filtru, vyberte a rozbalte řetězce by se měla zobrazit jako ve [Batch REST] [ api_rest] rozhraní API – i v případě, že používáte [Batch .NET] [ api_net]nebo některých jiných sad SDK služby Batch.
* Všechny názvy vlastností jsou malá a velká písmena, ale hodnoty vlastností jsou malá a velká písmena.
* Datum a čas může být jedna z dva formáty řetězců a musí předcházet s `DateTime`.
  
  * Příklad formátu W3C DTF: `creationTime gt DateTime'2011-05-08T08:49:37Z'`
  * Příklad formátu RFC 1123: `creationTime gt DateTime'Sun, 08 May 2011 08:49:37 GMT'`
* Logická řetězce jsou buď `true` nebo `false`.
* Pokud je zadán neplatnou vlastnost nebo operátor, `400 (Bad Request)` způsobí chybu.

## <a name="efficient-querying-in-batch-net"></a>Efektivní dotazování v Batch .NET
V rámci [Batch .NET] [ api_net] rozhraní API, [ODATADetailLevel] [ odata] třída se používá pro poskytnutí filtr, vyberte a rozbalte řetězce do seznamu operace. ODataDetailLevel třída má tři vlastnosti veřejného řetězce, které lze zadaný v konstruktoru, nebo nastavit přímo na objekt. Pak předáte ODataDetailLevel objektu jako parametr do různých seznam operací, jako [ListPools][net_list_pools], [ListJobs][net_list_jobs], a [ListTasks][net_list_tasks].

* [ODATADetailLevel][odata].[ FilterClause][odata_filter]: Omezte počet položek, které jsou vráceny.
* [ODATADetailLevel][odata].[ SelectClause][odata_select]: Zadejte každou položku jsou vráceny hodnot vlastností.
* [ODATADetailLevel][odata].[ ExpandClause][odata_expand]: Načíst data pro všechny položky v jednom volání rozhraní API namísto samostatné volání pro každou položku.

Následující fragment kódu používá rozhraní Batch .NET API pro efektivní dotazy pro statistiku konkrétní sadu fondů služby Batch. V tomto scénáři má uživatel Batch testovacích i produkčních fondy. Test fondu identifikátorů mají předponu "test" a produkční fondu identifikátorů mají předponu "produkční". V tomto fragmentu kódu *myBatchClient* je správně inicializována instancí [BatchClient](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient) třídy.

```csharp
// First we need an ODATADetailLevel instance on which to set the filter, select,
// and expand clause strings
ODATADetailLevel detailLevel = new ODATADetailLevel();

// We want to pull only the "test" pools, so we limit the number of items returned
// by using a FilterClause and specifying that the pool IDs must start with "test"
detailLevel.FilterClause = "startswith(id, 'test')";

// To further limit the data that crosses the wire, configure the SelectClause to
// limit the properties that are returned on each CloudPool object to only
// CloudPool.Id and CloudPool.Statistics
detailLevel.SelectClause = "id, stats";

// Specify the ExpandClause so that the .NET API pulls the statistics for the
// CloudPools in a single underlying REST API call. Note that we use the pool's
// REST API element name "stats" here as opposed to "Statistics" as it appears in
// the .NET API (CloudPool.Statistics)
detailLevel.ExpandClause = "stats";

// Now get our collection of pools, minimizing the amount of data that is returned
// by specifying the detail level that we configured above
List<CloudPool> testPools =
    await myBatchClient.PoolOperations.ListPools(detailLevel).ToListAsync();
```

> [!TIP]
> Instance [ODATADetailLevel] [ odata] , který je nakonfigurovaný s vybranými a klauzule Expand může také být předán odpovídající metody Get, například [PoolOperations.GetPool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getpool.aspx), Chcete-li omezit množství dat, která je vrácena.
> 
> 

## <a name="batch-rest-to-net-api-mappings"></a>Batch REST k mapování rozhraní .NET API
Názvy vlastností ve filtru, vyberte a rozbalte řetězce *musí* odráží jejich protějšky rozhraní REST API, v názvu i v případě. Následující tabulka obsahuje mapování mezi protějšky .NET a rozhraní REST API.

### <a name="mappings-for-filter-strings"></a>Mapování pro řetězce filtru
* **Seznam metod rozhraní .NET**: Každá z metod rozhraní .NET API v tomto sloupci přijímá [ODATADetailLevel] [ odata] objektu jako parametr.
* **Požadavků na seznam REST**: Každá stránka rozhraní REST API propojené v tomto sloupci obsahuje tabulku, která určuje vlastnosti a operace, které jsou povoleny v *filtr* řetězce. Použijete tyto názvy vlastností a operací při vytváření [ODATADetailLevel.FilterClause] [ odata_filter] řetězec.

| Seznam metod rozhraní .NET | Požadavků na seznam REST |
| --- | --- |
| [CertificateOperations.ListCertificates][net_list_certs] |[Seznam certifikátů v rámci účtu][rest_list_certs] |
| [CloudTask.ListNodeFiles][net_list_task_files] |[Seznam souborů, které jsou přidružené k úkolu][rest_list_task_files] |
| [JobOperations.ListJobPreparationAndReleaseTaskStatus][net_list_jobprep_status] |[Vypíše stav úlohy přípravy a uvolnění úloh pro úlohu][rest_list_jobprep_status] |
| [JobOperations.ListJobs][net_list_jobs] |[Zobrazit seznam úloh v rámci účtu][rest_list_jobs] |
| [JobOperations.ListNodeFiles][net_list_nodefiles] |[Seznam souborů na uzlu][rest_list_nodefiles] |
| [JobOperations.ListTasks][net_list_tasks] |[Vypsat úkoly spojené s úlohou][rest_list_tasks] |
| [JobScheduleOperations.ListJobSchedules][net_list_job_schedules] |[Seznam plánů úloh v rámci účtu][rest_list_job_schedules] |
| [JobScheduleOperations.ListJobs][net_list_schedule_jobs] |[Zobrazit seznam úloh, které jsou přidružené k plánu úlohy][rest_list_schedule_jobs] |
| [PoolOperations.ListComputeNodes][net_list_compute_nodes] |[Seznam výpočetních uzlů ve fondu][rest_list_compute_nodes] |
| [PoolOperations.ListPools][net_list_pools] |[Seznam fondů v účtu služby][rest_list_pools] |

### <a name="mappings-for-select-strings"></a>Mapování pro výběr řetězce
* **Batch .NET typy**: Typy rozhraní .NET API služby batch.
* **Rozhraní REST API služby entity**: Každá stránka v tomto sloupci obsahuje jednu nebo více tabulek, které uvádějí názvy vlastností rozhraní REST API pro typ. Tyto názvy vlastností se používají při konstrukci *vyberte* řetězce. Tyto stejné názvy vlastností použijete při vytváření [ODATADetailLevel.SelectClause] [ odata_select] řetězec.

| Typy rozhraní .NET služby batch | Rozhraní REST API služby entity |
| --- | --- |
| [Certifikát][net_cert] |[Získejte informace o certifikátu][rest_get_cert] |
| [CloudJob][net_job] |[Získejte informace o úloze][rest_get_job] |
| [CloudJobSchedule][net_schedule] |[Získejte informace o plánu úlohy][rest_get_schedule] |
| [ComputeNode][net_node] |[Získejte informace o uzlu][rest_get_node] |
| [CloudPool][net_pool] |[Získejte informace o fondu][rest_get_pool] |
| [CloudTask][net_task] |[Získejte informace o úkolu][rest_get_task] |

## <a name="example-construct-a-filter-string"></a>Příklad: vytvoření řetězce filtru
Při sestavování řetězec filtru pro [ODATADetailLevel.FilterClause][odata_filter], najdete v tabulce výše v části "Mapování pro filtr řetězce" stránku dokumentace najít rozhraní REST API, která odpovídá operace list, který chcete provést. V první tabulce více řádků na této stránce najdete filterable vlastností a jejich podporovaných operátorů. Pokud chcete načíst všechny úlohy, jejichž ukončovací kód procesu je nenulovou hodnotu, například tento řádek na [seznamu úkoly spojené s úlohou] [ rest_list_tasks] Určuje řetězec příslušné vlastnosti a operátory povolená:

| Vlastnost | Přípustné operace | Typ |
|:--- |:--- |:--- |
| `executionInfo/exitCode` |`eq, ge, gt, le , lt` |`Int` |

Proto by byl řetězec filtru pro výpis všech úloh s nenulový ukončovací kód:

`(executionInfo/exitCode lt 0) or (executionInfo/exitCode gt 0)`

## <a name="example-construct-a-select-string"></a>Příklad: sestavit vyberte řetězec
K sestavení kompletních [ODATADetailLevel.SelectClause][odata_select], najdete v tabulce výše v části "Mapování pro řetězce, vyberte možnost" a přejděte na stránku rozhraní REST API, který odpovídá typu entity, která se při vytváření seznamu. V první tabulce více řádků na této stránce najdete volitelných vlastností a jejich podporovaných operátorů. Pokud chcete načíst ID a příkazový řádek pro každý úkol v seznamu, například najdete tyto řádky v tabulce vztahuje na [získat informace o úkolu][rest_get_task]:

| Vlastnost | Typ | Poznámky |
|:--- |:--- |:--- |
| `id` |`String` |`The ID of the task.` |
| `commandLine` |`String` |`The command line of the task.` |

Pak budou vyberte řetězec pro včetně ID a příkazového řádku pomocí jednotlivých uvedených úkolů:

`id, commandLine`

## <a name="code-samples"></a>Ukázky kódů
### <a name="efficient-list-queries-code-sample"></a>Ukázka kódu efektivní seznamové dotazy
Podívejte se [EfficientListQueries] [ efficient_query_sample] ukázkový projekt na GitHub, kde uvidíte, jak efektivní dotazování seznamu může ovlivnit výkon aplikace. Tato Konzolová aplikace jazyka C# vytvoří a přidá velký počet úkolů do úlohy. Potom provede několik volání [JobOperations.ListTasks] [ net_list_tasks] metoda a předá [ODATADetailLevel] [ odata] objekty, které jsou nakonfigurované hodnoty různých vlastností, které se liší množství dat, který se má vrátit. Vytvoří výstup podobný následujícímu:

```
Adding 5000 tasks to job jobEffQuery...
5000 tasks added in 00:00:47.3467587, hit ENTER to query tasks...

4943 tasks retrieved in 00:00:04.3408081 (ExpandClause:  | FilterClause: state eq 'active' | SelectClause: id,state)
0 tasks retrieved in 00:00:00.2662920 (ExpandClause:  | FilterClause: state eq 'running' | SelectClause: id,state)
59 tasks retrieved in 00:00:00.3337760 (ExpandClause:  | FilterClause: state eq 'completed' | SelectClause: id,state)
5000 tasks retrieved in 00:00:04.1429881 (ExpandClause:  | FilterClause:  | SelectClause: id,state)
5000 tasks retrieved in 00:00:15.1016127 (ExpandClause:  | FilterClause:  | SelectClause: id,state,environmentSettings)
5000 tasks retrieved in 00:00:17.0548145 (ExpandClause: stats | FilterClause:  | SelectClause: )

Sample complete, hit ENTER to continue...
```

Jak je znázorněno v uplynulého času, můžete výrazně snížit dobu odezvy na dotazy tím, že omezíte vlastnosti a počet položek, které jsou vráceny. Můžete vyhledat tato a další ukázkových projektů v [azure-batch-samples] [ github_samples] úložišti na Githubu.

### <a name="batchmetrics-library-and-code-sample"></a>BatchMetrics knihovny a ukázku kódu
Kromě EfficientListQueries ukázkovém kódu výše, můžete najít [BatchMetrics] [ batch_metrics] projekt [azure-batch-samples] [ github_samples]Úložiště GitHub. Ukázkový projekt BatchMetrics ukazuje, jak efektivně Sledujte průběh úloh služby Azure Batch pomocí rozhraní API služby Batch.

[BatchMetrics] [ batch_metrics] ukázka zahrnuje projekt knihovny tříd .NET, která můžete začlenit do vašich vlastních projektů a jednoduchý program v příkazovém řádku na výkon a demonstrují použití knihovny.

Ukázková aplikace v rámci projektu demonstruje následující operace:

1. Výběr konkrétní atributy, aby mohl stáhnout pouze vlastnosti, které potřebujete
2. Pokud chcete stáhnout pouze změny od poslední dotaz filtrování podle doby přechodu stavu

Následující metody se zobrazí v knihovně BatchMetrics. Vrátí ODATADetailLevel, který určuje, že pouze `id` a `state` vlastnosti by měla být získána pro entity, které se generuje dotaz. Také určuje, že pouze entity, jehož stav se změnil od zadaného `DateTime` parametru má být vrácen.

```csharp
internal static ODATADetailLevel OnlyChangedAfter(DateTime time)
{
    return new ODATADetailLevel(
        selectClause: "id, state",
        filterClause: string.Format("stateTransitionTime gt DateTime'{0:o}'", time)
    );
}
```

## <a name="next-steps"></a>Další postup
### <a name="parallel-node-tasks"></a>Uzel paralelní úlohy
[Maximalizujte využití prostředků výpočetní služby Azure Batch s souběžné úlohy uzlu](batch-parallel-node-tasks.md) jiného článku souvisí s výkonu aplikace Batch. Některé typy úloh může přinést spuštění paralelní úlohy na větší – menší – počet výpočetních uzlů, ale. Podívejte se [ukázkový scénář](batch-parallel-node-tasks.md#example-scenario) v článku o v takové situaci.


[api_net]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch?view=azure-dotnet
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: https://docs.microsoft.com/rest/api/batchservice/
[batch_metrics]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchMetrics
[efficient_query_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/EfficientListQueries
[github_samples]: https://github.com/Azure/azure-batch-samples
[odata]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx
[odata_ctor]: https://msdn.microsoft.com/library/azure/dn866178.aspx
[odata_expand]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.expandclause.aspx
[odata_filter]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.filterclause.aspx
[odata_select]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.selectclause.aspx

[net_list_certs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.listcertificates.aspx
[net_list_compute_nodes]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listcomputenodes.aspx
[net_list_job_schedules]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobschedules.aspx
[net_list_jobprep_status]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobpreparationandreleasetaskstatus.aspx
[net_list_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[net_list_nodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listnodefiles.aspx
[net_list_pools]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listpools.aspx
[net_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobs.aspx
[net_list_task_files]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[net_list_tasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listtasks.aspx

[rest_list_certs]: https://msdn.microsoft.com/library/azure/dn820154.aspx
[rest_list_compute_nodes]: https://msdn.microsoft.com/library/azure/dn820159.aspx
[rest_list_job_schedules]: https://msdn.microsoft.com/library/azure/mt282174.aspx
[rest_list_jobprep_status]: https://msdn.microsoft.com/library/azure/mt282170.aspx
[rest_list_jobs]: https://msdn.microsoft.com/library/azure/dn820117.aspx
[rest_list_nodefiles]: https://msdn.microsoft.com/library/azure/dn820151.aspx
[rest_list_pools]: https://msdn.microsoft.com/library/azure/dn820101.aspx
[rest_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/mt282169.aspx
[rest_list_task_files]: https://msdn.microsoft.com/library/azure/dn820142.aspx
[rest_list_tasks]: https://msdn.microsoft.com/library/azure/dn820187.aspx

[rest_get_cert]: https://msdn.microsoft.com/library/azure/dn820176.aspx
[rest_get_job]: https://msdn.microsoft.com/library/azure/dn820106.aspx
[rest_get_node]: https://msdn.microsoft.com/library/azure/dn820168.aspx
[rest_get_pool]: https://msdn.microsoft.com/library/azure/dn820165.aspx
[rest_get_schedule]: https://msdn.microsoft.com/library/azure/mt282171.aspx
[rest_get_task]: https://msdn.microsoft.com/library/azure/dn820133.aspx

[net_cert]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificate.aspx
[net_job]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_schedule]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjobschedule.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx

[rest_get_task_counts]: /rest/api/batchservice/get-the-task-counts-for-a-job
[rest_get_node_counts]: /rest/api/batchservice/account/listpoolnodecounts