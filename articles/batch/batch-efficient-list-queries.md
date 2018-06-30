---
title: Návrh efektivní seznamu dotazy – Azure Batch | Microsoft Docs
description: Zvyšuje výkon filtrování vašich dotazů při žádosti o Batch prostředkům, jako jsou fondy, úlohy, úlohy a výpočetní uzly.
services: batch
documentationcenter: .net
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: 031fefeb-248e-4d5a-9bc2-f07e46ddd30d
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 08/02/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 950e422b3076e5abd5db6dd0ac452fa1c2d500d0
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37129264"
---
# <a name="create-queries-to-list-batch-resources-efficiently"></a>Efektivně vytvářet dotazy ke prostředky Batch seznamu

Zde se dozvíte, jak zvýšit výkon aplikace Azure Batch snižuje množství dat, která je vrácena službou při dotazování úlohy a úlohy a výpočetní uzly se [Batch .NET] [ api_net]knihovny.

Téměř všechny aplikace Batch je potřeba provést nějaký typ monitorování nebo jiné operace, který se dotazuje služby Batch, často v pravidelných intervalech. Pokud chcete zjistit, zda jsou všechny ve frontě úloh zbývající v rámci úlohy, například musí získat data na každý úkol v úloze. Pokud chcete zjistit stav uzly ve fondu, musíte získat data na každý uzel ve fondu. Tento článek vysvětluje, jak provést takové dotazy nejefektivnějším způsobem.

> [!NOTE]
> Služba Batch poskytuje speciální podporu rozhraní API pro běžný scénář inventur úkoly v úloze. Místo použití seznamu dotazu pro tyto, můžete zavolat [získat počty úloh] [ rest_get_task_counts] operaci. Počet úloh Get Určuje, kolik úlohy čekají na vyřízení, spuštění nebo dokončení, a mít kolik úlohy byla úspěšná nebo neúspěšná. Spočítá počet úloh GET je efektivnější než seznamu dotazu. Další informace najdete v tématu [počet úloh pro úlohu podle stavu (Preview)](batch-get-task-counts.md). 
>
> Operace získání úkolů počty starší než 2017-06-01.5.1 není k dispozici ve verzích služby Batch. Pokud používáte starší verzi služby, potom pomocí seznamu dotazu místo počet úkoly v úloze.
>
> 

## <a name="meet-the-detaillevel"></a>Splňovat DetailLevel
V produkční aplikaci služby Batch můžete v tisíců čísel entity jako úlohy, úlohy a výpočetní uzly. Pokud budete požadovat informace o těchto prostředků, potenciálně velkého množství dat musí "křížová sítě" ze služby Batch do vaší aplikace na každý dotaz. Tím, že omezí počet položek a typu informací, které je vrácených dotazem, můžete zvýšit rychlost své dotazy a proto výkon vaší aplikace.

To [Batch .NET] [ api_net] seznamy fragmentu kódu rozhraní API *každých* úlohu, která je spojená s úlohou, spolu s *všechny* vlastností jednotlivých úloh:

```csharp
// Get a collection of all of the tasks and all of their properties for job-001
IPagedEnumerable<CloudTask> allTasks =
    batchClient.JobOperations.ListTasks("job-001");
```

Mnohem efektivnější dotaz, můžete však provést použitím "úroveň podrobností" do dotazu. To provedete zadáním [ODATADetailLevel] [ odata] do objektu [JobOperations.ListTasks] [ net_list_tasks] metoda. Tento fragment kódu vrátí pouze ID, příkazový řádek a výpočetní uzel informace vlastnosti dokončených úloh:

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

V tomto ukázkovém scénáři, pokud existují tisíce úkolů do úlohy výsledky z druhého dotazu bude obvykle vrácen mnohem rychlejší než první. Další informace o používání ODATADetailLevel při seznamu položek s Batch .NET API je součástí [pod](#efficient-querying-in-batch-net).

> [!IMPORTANT]
> Důrazně doporučujeme, aby vám *vždy* zadat objekt ODATADetailLevel tak, aby voláními rozhraní API .NET seznamu zajistit maximální efektivity a výkonu vaší aplikace. Zadáním úroveň podrobností můžete pomoct snížit dobu odezvy služby Batch, zvýšit využití sítě a minimalizovat využití paměti v klientských aplikacích.
> 
> 

## <a name="filter-select-and-expand"></a>Filtrovat, vyberte a rozbalte
[Batch .NET] [ api_net] a [Batch REST] [ api_rest] rozhraní API umožňují snížit počet položek, které jsou vráceny v seznamu, a také objem informací, které se vrátí pro každou. To uděláte tak, že zadáte **filtru**, **vyberte**, a **rozbalte řetězce** při provádění dotazů seznamu.

### <a name="filter"></a>Filtr
Řetězec filtru je výraz, který snižuje počet položek, které jsou vráceny. Například seznam pouze spuštěné úlohy pro úlohu, nebo seznam pouze výpočetní uzly, které jsou připravené ke spuštění úlohy.

* Řetězec filtru se skládá z jednoho nebo více výrazů s výrazem, který obsahuje název vlastnosti, operátor a hodnotu. Vlastnosti, které lze zadat jsou specifické pro každý typ entity, které můžete zadat dotaz, jako jsou operátory, které jsou podporovány pro každou vlastnost.
* Více výrazů lze spojovat pomocí logických operátorů `and` a `or`.
* Tento příklad filtrování seznamů řetězec jen spuštění úlohy "vykreslení": `(state eq 'running') and startswith(id, 'renderTask')`.

### <a name="select"></a>Vyberte
Vyberte řetězec omezení hodnoty vlastností, které se vrátí pro každou položku. Zadejte seznam názvů vlastností a pouze hodnoty těchto vlastností jsou vráceny pro položky v výsledky dotazu.

* Vyberte řetězec tvořený seznam názvů vlastností oddělených čárkami. Můžete určit všechny vlastnosti pro typ entity, který se dotazuje.
* Tento příklad vyberte řetězec Určuje, že má být vrácen pouze tři hodnoty vlastností pro každou úlohu: `id, state, stateTransitionTime`.

### <a name="expand"></a>Rozbalit
Rozbalte řetězec snižuje počet volání rozhraní API, které jsou nutné k získání určité informace. Použijete-li řetězec rozbalte, nelze získat další informace o jednotlivých položkách na základě jednoho volání rozhraní API. Místo první získání seznamu entity, pak vyžadování informací o pro každou položku v seznamu, použijte řetězec rozbalte získat stejné informace v jediném volání rozhraní API. Menší volání rozhraní API znamená vyšší výkon.

* Podobně jako u vyberte řetězec, řetězec rozbalte Určuje, jestli některá data je zahrnuta v seznamu výsledků dotazu.
* Rozbalte řetězec je podporována pouze v případě se používá v seznam úloh, plány úloh, úlohy a fondy. V současné době podporuje pouze statistické informace.
* Pokud se všechny vlastnosti požadované a není zadán žádný vyberte řetězec, řetězec rozbalte *musí* použít k získání statistické informace. Pokud vyberte řetězec se používá k získání podmnožiny vlastností, pak `stats` lze zadat v řetězci vyberte a rozbalte řetězec není potřeba zadat.
* Tento příklad rozbalte položku řetězec Určuje, zda má být vrácen statistické informace pro každou položku v seznamu: `stats`.

> [!NOTE]
> Při vytváření jakýkoli z typů řetězec dotazu tři (filtrování, vyberte a rozbalte možnost), ujistěte se, že názvy vlastností a případ shodovat s jejich protějšky element REST API. Například při práci s .NET [CloudTask](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask) třídy, je nutné zadat **stavu** místo **stavu**, i když je vlastnost .NET [ CloudTask.State](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.state). Podívejte se na tabulky pod pro mapování vlastností mezi .NET a REST API.
> 
> 

### <a name="rules-for-filter-select-and-expand-strings"></a>Pravidla pro filtr, vyberte a rozbalte řetězce
* Názvy vlastností ve filtru, vyberte a rozbalte řetězce by se měla zobrazit jako ve [Batch REST] [ api_rest] rozhraní API – i v případě, že používáte [Batch .NET] [ api_net]nebo jeden z ostatních Batch sad SDK.
* Všechny názvy vlastností malá a velká písmena, ale hodnoty vlastností se malá a velká písmena.
* Datum a čas řetězců může mít jednu ze dvou formátů a musí předcházet `DateTime`.
  
  * Příklad formátu W3C DTF: `creationTime gt DateTime'2011-05-08T08:49:37Z'`
  * Příklad formátu RFC 1123: `creationTime gt DateTime'Sun, 08 May 2011 08:49:37 GMT'`
* Logická hodnota řetězce jsou buď `true` nebo `false`.
* Pokud je zadána neplatná vlastnost nebo operátor, `400 (Bad Request)` bude výsledkem chyba.

## <a name="efficient-querying-in-batch-net"></a>Efektivní dotazování v Batch .NET
V rámci [Batch .NET] [ api_net] rozhraní API, [ODATADetailLevel] [ odata] třída se používá pro zadávání filtru, vyberte a rozbalte seznam řetězců operace. Třída ODataDetailLevel má tři řetězec veřejné vlastnosti, které lze zadaným v konstruktoru, nebo nastavit přímo na objekt. Můžete poté předat objekt ODataDetailLevel jako parametr různé operace výpisu, jako [ListPools][net_list_pools], [ListJobs][net_list_jobs], a [ListTasks][net_list_tasks].

* [ODATADetailLevel][odata].[ FilterClause][odata_filter]: omezit počet položek, které jsou vráceny.
* [ODATADetailLevel][odata].[ SelectClause][odata_select]: Zadejte každou položku jsou vráceny hodnot vlastností.
* [ODATADetailLevel][odata].[ ExpandClause][odata_expand]: načtení dat pro všechny položky v jediného rozhraní API volat místo samostatné volání pro každou položku.

Následující fragment kódu používá rozhraní Batch .NET API k efektivní dotazování na službu Batch pro statistiku konkrétní sadu fondů. V tomto scénáři má uživatel Batch testovací a produkční fondů. Test fondu ID mají předponu "test" a fondu produkční ID mají předponu "produkčnímu". V tomto fragmentu kódu *myBatchClient* je správně inicializována instanci [BatchClient](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient) třídy.

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
> Instance [ODATADetailLevel] [ odata] nakonfigurovaný s vyberte a rozbalte klauzule lze také předat příslušné metody Get, jako například [PoolOperations.GetPool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getpool.aspx), Chcete-li omezit množství dat, která je vrácena.
> 
> 

## <a name="batch-rest-to-net-api-mappings"></a>Batch REST pro mapování rozhraní API .NET
Názvy vlastností ve filtru, vyberte a rozbalte řetězce *musí* odráží jejich protějšky REST API, v názvu i případu. Následující tabulky poskytují mapování mezi svými protějšky .NET a REST API.

### <a name="mappings-for-filter-strings"></a>Mapování pro filtr řetězce
* **Seznam metod rozhraní .NET**: každá z metod rozhraní API .NET v tomto sloupci přijímá [ODATADetailLevel] [ odata] objekt jako parametr.
* **REST seznamu požadavků**: stránka každý REST API propojené v tomto sloupci obsahuje tabulku, která určuje vlastnosti a operace, které jsou povoleny v *filtru* řetězce. Když vytvoříte budou používat tyto operace a názvy vlastností [ODATADetailLevel.FilterClause] [ odata_filter] řetězec.

| Seznam metod rozhraní .NET | REST seznamu požadavků |
| --- | --- |
| [CertificateOperations.ListCertificates][net_list_certs] |[Seznam certifikátů na účtu][rest_list_certs] |
| [CloudTask.ListNodeFiles][net_list_task_files] |[Seznam soubory spojené s úlohami][rest_list_task_files] |
| [JobOperations.ListJobPreparationAndReleaseTaskStatus][net_list_jobprep_status] |[Seznam stav úlohy přípravy a uvolnění úloh pro úlohu][rest_list_jobprep_status] |
| [JobOperations.ListJobs][net_list_jobs] |[Seznam úloh na účtu][rest_list_jobs] |
| [JobOperations.ListNodeFiles][net_list_nodefiles] |[Seznam souborů v uzlu][rest_list_nodefiles] |
| [JobOperations.ListTasks][net_list_tasks] |[Seznam úkoly spojené s úlohou][rest_list_tasks] |
| [JobScheduleOperations.ListJobSchedules][net_list_job_schedules] |[Seznam plánů úloh na účtu][rest_list_job_schedules] |
| [JobScheduleOperations.ListJobs][net_list_schedule_jobs] |[Seznam úloh, které jsou přidružené k plánu úlohy][rest_list_schedule_jobs] |
| [PoolOperations.ListComputeNodes][net_list_compute_nodes] |[Seznam výpočetní uzly ve fondu][rest_list_compute_nodes] |
| [PoolOperations.ListPools][net_list_pools] |[Zobrazí seznam fondů na účtu][rest_list_pools] |

### <a name="mappings-for-select-strings"></a>Mapování pro vyberte řetězce
* **Batch .NET typy**: typy Batch .NET API.
* **Rozhraní REST API entity**: obsahuje jednu nebo více tabulek, které seznam názvů vlastností rozhraní REST API pro typ každé stránce v tomto sloupci. Tyto názvy vlastností se používají při vytváření *vyberte* řetězce. Tyto stejné názvy vlastností použijete při vytváření [ODATADetailLevel.SelectClause] [ odata_select] řetězec.

| Typy batch .NET | Rozhraní REST API entity |
| --- | --- |
| [Certifikát][net_cert] |[Získání informací o certifikát][rest_get_cert] |
| [CloudJob][net_job] |[Získání informací o úlohy][rest_get_job] |
| [CloudJobSchedule][net_schedule] |[Získat informace o plánu úlohy][rest_get_schedule] |
| [ComputeNode][net_node] |[Získání informací o uzlu][rest_get_node] |
| [CloudPool][net_pool] |[Získat informace o fondu][rest_get_pool] |
| [CloudTask][net_task] |[Získat informace o úkolu][rest_get_task] |

## <a name="example-construct-a-filter-string"></a>Příklad: vytvoření řetězec filtru
Když vytvoříte řetězec filtru pro [ODATADetailLevel.FilterClause][odata_filter], najdete v tabulce výše v části "Mapování pro filtr řetězce" na stránce dokumentace najít rozhraní REST API, která odpovídá operace seznamu, který chcete provést. V první tabulce více řádků na této stránce najdete filtrování vlastností a jejich podporované operátory. Pokud chcete načíst všechny úlohy, jejichž ukončovací kód procesu je nenulové hodnoty, například tento řádek na [seznamu úkoly spojené s úlohou] [ rest_list_tasks] určuje povolené operátory a použít vlastnost řetězec:

| Vlastnost | Povolené operace | Typ |
|:--- |:--- |:--- |
| `executionInfo/exitCode` |`eq, ge, gt, le , lt` |`Int` |

Proto by byl řetězec filtru pro výpis všech úloh s nenulový ukončovací kód:

`(executionInfo/exitCode lt 0) or (executionInfo/exitCode gt 0)`

## <a name="example-construct-a-select-string"></a>Příklad: sestavit vyberte řetězec
K vytvoření [ODATADetailLevel.SelectClause][odata_select], projděte si v tabulce výše v části "Mapování pro vyberte řetězce" a přejděte na stránku REST API, která odpovídá typ entity, která jsou výpis. V první tabulce více řádků na této stránce najdete volitelný vlastnostmi a jejich podporované operátory. Pokud chcete načíst pouze ID a příkazový řádek pro každý úkol v seznamu, například zjistíte, tyto řádky v tabulce použít na [získat informace o úkolu][rest_get_task]:

| Vlastnost | Typ | Poznámky |
|:--- |:--- |:--- |
| `id` |`String` |`The ID of the task.` |
| `commandLine` |`String` |`The command line of the task.` |

Poté budou vyberte řetězec pro včetně pouze ID a příkazového řádku u jednotlivých uvedených úkolů:

`id, commandLine`

## <a name="code-samples"></a>Ukázky kódů
### <a name="efficient-list-queries-code-sample"></a>Ukázka kódu dotazy efektivní seznamu
Podívejte se [EfficientListQueries] [ efficient_query_sample] ukázkového projektu na Githubu, které chcete zobrazit, jak efektivní dotazování seznamu může ovlivnit výkon v aplikaci. Tato Konzolová aplikace C# vytvoří a přidá velkého počtu úkolů do úlohy. Pak umožňuje více volání [JobOperations.ListTasks] [ net_list_tasks] metoda a předává [ODATADetailLevel] [ odata] objekty, které jsou nakonfigurované hodnot různých vlastností k odlišení množství dat, který se má vrátit. Vyvolá výstup podobný následujícímu:

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

Jak je znázorněno v procentuálně dobu, můžete výrazně snížit dobu odezvy na dotazy omezením vlastnosti a počet položek, které jsou vráceny. Tato a Další ukázkové projekty v lze najít [azure-batch-samples] [ github_samples] úložišti na Githubu.

### <a name="batchmetrics-library-and-code-sample"></a>Ukázka BatchMetrics knihovny a kódu
Kromě EfficientListQueries kód ukázce výše můžete najít [BatchMetrics] [ batch_metrics] v projektu [azure-batch-samples] [ github_samples]Úložiště GitHub. Ukázkový projekt BatchMetrics ukazuje, jak efektivně sledovat průběh úlohy Azure Batch pomocí rozhraní API služby Batch.

[BatchMetrics] [ batch_metrics] ukázka zahrnuje projektu knihovny tříd rozhraní .NET, která můžete začlenit do vašich vlastních projektů a jednoduchý příkazového řádku programu prověření a Ukázka použití knihovny.

Ukázkovou aplikaci v rámci projektu ukazuje následující operace:

1. Chcete-li stáhnout pouze vlastnosti, které potřebujete výběr konkrétní atributy
2. Chcete-li stáhnout pouze změny od poslední dotaz s filtrování doby přechodu stavu

V knihovně BatchMetrics se například zobrazí následující metodu. Vrátí ODATADetailLevel, která určuje, že pouze `id` a `state` by měl získat vlastnosti pro entity, které jsou předmětem dotazování. Také určuje, že jenom entity, jejichž stav se změnil od zadaného `DateTime` parametr má být vrácen.

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
[Maximalizovat využití prostředků Azure Batch výpočetní uzel souběžných úloh](batch-parallel-node-tasks.md) je jiný článek související s výkonem aplikací Batch. Některé typy úloh využívat výhod spouštění paralelní úlohy na větší – ale méně – výpočetní uzly. Podívejte se [ukázkový scénář](batch-parallel-node-tasks.md#example-scenario) v článku podrobnosti o tento případ.


[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
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

[rest_get_task_counts]: https://docs.microsoft.com/rest/api/batchservice/get-the-task-counts-for-a-job