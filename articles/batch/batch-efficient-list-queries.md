---
title: Návrh efektivních dotazů na seznam – Azure Batch | Dokumenty společnosti Microsoft
description: Zvyšte výkon filtrováním dotazů při požadování informací o dávkových prostředcích, jako jsou fondy, úlohy, úkoly a výpočetní uzly.
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 031fefeb-248e-4d5a-9bc2-f07e46ddd30d
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 12/07/2018
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: df923ac479ce5f5a3668c18c616b11348dc6c0b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022235"
---
# <a name="create-queries-to-list-batch-resources-efficiently"></a>Vytvoření dotazů pro efektivní seznam dávkových prostředků

Zde se dozvíte, jak zvýšit výkon aplikace Azure Batch snížením množství dat, která je vrácena službou při dotazování úloh, úloh, výpočetních uzlů a dalších prostředků pomocí [knihovny Batch .NET.][api_net]

Téměř všechny dávkové aplikace musí provádět nějaký typ monitorování nebo jiné operace, která se dotazuje služby Batch, často v pravidelných intervalech. Chcete-li například zjistit, zda v úloze zůstávají nějaké úkoly ve frontě, je nutné získat data o každém úkolu v úloze. Chcete-li zjistit stav uzlů ve fondu, musíte získat data na každém uzlu ve fondu. Tento článek vysvětluje, jak provést tyto dotazy v nejúčinnějším způsobem.

> [!NOTE]
> Služba Batch poskytuje speciální podporu rozhraní API pro běžné scénáře počítání úloh v úloze a počítání výpočetních uzlů ve fondu dávek. Místo použití dotazu seznamu pro tyto, můžete volat [získat počty úkolů][rest_get_task_counts] a seznam fondu počty [uzlů][rest_get_node_counts] operace. Tyto operace jsou efektivnější než dotaz seznamu, ale vrátit omezenější informace. Viz [Počet úloh a výpočetních uzlů podle stavu](batch-get-resource-counts.md). 


## <a name="meet-the-detaillevel"></a>Seznamte se s úrovní podrobností
Ve výrobě dávkové aplikace mohou být entity, jako jsou úlohy, úkoly a výpočetní uzly, číslovány v tisících. Při vyžádání informací o těchto prostředcích musí potenciálně velké množství dat "přejít drát" ze služby Batch do aplikace na každém dotazu. Omezením počtu položek a typu informací vrácených dotazem můžete zvýšit rychlost dotazů a tedy výkon aplikace.

Tento fragment kódu rozhraní API [dávky .NET][api_net] obsahuje seznam *všech* úloh, které jsou přidruženy k úloze, spolu se *všemi* vlastnostmi jednotlivých úloh:

```csharp
// Get a collection of all of the tasks and all of their properties for job-001
IPagedEnumerable<CloudTask> allTasks =
    batchClient.JobOperations.ListTasks("job-001");
```

Můžete však provést mnohem efektivnější dotaz seznamu použitím "úrovně podrobností" na dotaz. To provést zadáním ObjektU [ODATADetailLevel][odata] metodě [JobOperations.ListTasks.][net_list_tasks] Tento úryvek vrátí pouze vlastnosti informací o ID, příkazovém řádku a výpočetním uzlu dokončených úloh:

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

V tomto příkladu scénáře pokud jsou tisíce úkolů v úloze, výsledky z druhého dotazu bude obvykle vrácena mnohem rychleji než první. Další informace o použití ODATADetailLevel při zobrazení seznamu položek s rozhraním API Batch .NET je zahrnuto [níže](#efficient-querying-in-batch-net).

> [!IMPORTANT]
> Důrazně doporučujeme *vždy* zadat Objekt ODATADetailLevel do seznamu volání rozhraní .NET API k zajištění maximální efektivity a výkonu vaší aplikace. Zadáním úrovně podrobností můžete snížit dobu odezvy služby Batch, zlepšit využití sítě a minimalizovat využití paměti klientskými aplikacemi.
> 
> 

## <a name="filter-select-and-expand"></a>Filtrování, výběr a rozbalení
Rozhraní API [batch .NET][api_net] a [Batch REST][api_rest] poskytují možnost snížit počet položek, které jsou vráceny v seznamu, stejně jako množství informací, které jsou vráceny pro každou z nich. Provedete to zadáním **filtru**, **výběrem**a **rozbalením řetězců** při provádění dotazů na seznam.

### <a name="filter"></a>Filtr
Řetězec filtru je výraz, který snižuje počet položek, které jsou vráceny. Například seznam pouze spuštěné úlohy pro úlohu nebo seznam pouze výpočetní uzly, které jsou připraveny ke spuštění úlohy.

* Řetězec filtru se skládá z jednoho nebo více výrazů s výrazem, který se skládá z názvu vlastnosti, operátoru a hodnoty. Vlastnosti, které lze zadat, jsou specifické pro každý typ entity, který dotazujete, stejně jako operátory, které jsou podporovány pro každou vlastnost.
* Více výrazů lze kombinovat pomocí logických operátorů `and` a `or`.
* V tomto příkladu je řetězec filtru `(state eq 'running') and startswith(id, 'renderTask')`uveden pouze spuštěné úlohy vykreslení: .

### <a name="select"></a>Vyberte
Select řetězec omezuje hodnoty vlastností, které jsou vráceny pro každou položku. Zadáte seznam názvů vlastností a pro položky ve výsledcích dotazu budou vráceny pouze tyto hodnoty vlastností.

* Řetězec select se skládá ze seznamu názvů vlastností oddělených čárkami. Můžete zadat libovolnou vlastnost pro typ entity, na kterou se dotazujete.
* Tento příklad vybrat řetězec určuje, že by měly `id, state, stateTransitionTime`být vráceny pouze tři hodnoty vlastností pro každý úkol: .

### <a name="expand"></a>Rozbalit
Rozbalovací řetězec snižuje počet volání rozhraní API, které jsou nutné k získání určitých informací. Při použití rozbalovací řetězec další informace o každé položce lze získat pomocí jednoho volání rozhraní API. Místo prvního získání seznamu entit a následnévyžádáníní informací pro každou položku v seznamu použijete rozbalovací řetězec k získání stejných informací v jednom volání rozhraní API. Méně volání rozhraní API znamená lepší výkon.

* Podobně jako u řetězce select rozbalení řetězec určuje, zda jsou určitá data zahrnuta ve výsledcích dotazu seznamu.
* Rozbalovací řetězec je podporován pouze v případě, že se používá v seznamu úloh, plánů úloh, úkolů a fondů. V současné době podporuje pouze statistické informace.
* Pokud jsou požadovány všechny vlastnosti a není zadán žádný řetězec pro výběr, *rozbalovací* řetězec musí být použit k získání informací o statistice. Pokud se k získání podmnožiny vlastností `stats` použije vybraný řetězec, lze jej zadat ve výběrovém řetězci a rozbalovací řetězec nemusí být určen.
* Tento příklad rozbalovací řetězec určuje, že informace o statistice by měly být vráceny pro každou položku v seznamu: `stats`.

> [!NOTE]
> Při vytváření některého ze tří typů řetězce dotazu (filtr, výběr a rozbalte), musíte zajistit, aby názvy vlastností a případ odpovídaly jejich protějšky prvku rozhraní REST API. Například při práci s třídou .NET [CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask) je nutné zadat **stav** namísto **stavu**, i když je vlastnost .NET [CloudTask.State](/dotnet/api/microsoft.azure.batch.cloudtask.state#Microsoft_Azure_Batch_CloudTask_State). Mapování vlastností mezi rozhraními API .NET a REST naleznete v následujících tabulkách.
> 
> 

### <a name="rules-for-filter-select-and-expand-strings"></a>Pravidla pro filtrování, výběr a rozbalení řetězců
* Názvy vlastností ve filtru, výběru a rozbalení řetězců by se měly zobrazovat stejně jako v rozhraní [API části API dávky ,][api_rest] a to i v případě, že používáte [dávkovou .NET][api_net] nebo jednu z dalších sad SDK dávky.
* Všechny názvy vlastností rozlišují malá a velká písmena, ale hodnoty vlastností jsou malá a velká písmena.
* Řetězce data a času mohou být jedním ze dvou formátů a musí mu předcházet `DateTime`.
  
  * Příklad formátu W3C-DTF:`creationTime gt DateTime'2011-05-08T08:49:37Z'`
  * Příklad formátu RFC 1123:`creationTime gt DateTime'Sun, 08 May 2011 08:49:37 GMT'`
* Logické řetězce jsou `true` buď `false`nebo .
* Pokud je zadána neplatná `400 (Bad Request)` vlastnost nebo operátor, dojde k chybě.

## <a name="efficient-querying-in-batch-net"></a>Efektivní dotazování v aplikaci Batch .NET
V rámci [rozhraní Batch .NET][api_net] API se třída [ODATADetailLevel][odata] používá pro poskytování filtru, výběru a rozbalení řetězců do operací seznamu. Třída ODataDetailLevel má tři vlastnosti veřejného řetězce, které lze zadat v konstruktoru nebo nastavit přímo na objektu. Potom předáte objekt ODataDetailLevel jako parametr různým operacím seznamu, jako jsou [seznamové pooly][net_list_pools], [ListJobs][net_list_jobs]a [ListTasks][net_list_tasks].

* [ODATADetailLevel][odata]. [FilterClause][odata_filter]: Omezit počet položek, které jsou vráceny.
* [ODATADetailLevel][odata]. [SelectClause][odata_select]: Určete, které hodnoty vlastností jsou vráceny s každou položkou.
* [ODATADetailLevel][odata]. [ExpandClause][odata_expand]: Načíst data pro všechny položky v jednom volání rozhraní API namísto samostatných volání pro každou položku.

Následující fragment kódu používá rozhraní API batch .NET k efektivnímu dotazování služby Batch na statistiky určité sady fondů. V tomto scénáři batch uživatel má testovací i produkční fondy. ID testovacího fondu jsou předponou s "test" a ID fondu výroby jsou předponou s "prod". Ve fragmentu je *myBatchClient* správně inicializovanou inicializovanou instancí třídy [BatchClient.](/dotnet/api/microsoft.azure.batch.batchclient)

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
> Instance [ODATADetailLevel,][odata] která je nakonfigurována s Select a Expand klauzule mohou být také předány příslušné Get metody, například [PoolOperations.GetPool](/dotnet/api/microsoft.azure.batch.pooloperations.getpool#Microsoft_Azure_Batch_PoolOperations_GetPool_System_String_Microsoft_Azure_Batch_DetailLevel_System_Collections_Generic_IEnumerable_Microsoft_Azure_Batch_BatchClientBehavior__), omezit množství dat, která je vrácena.
> 
> 

## <a name="batch-rest-to-net-api-mappings"></a>Dávková rest na rozhraní API rozhraní .NET
Názvy vlastností ve filtru, vyberte a rozbalte řetězce *musí* odrážet jejich protějšky rozhraní REST API, a to jak v názvu, tak v případě. Níže uvedené tabulky poskytují mapování mezi protějšky rozhraní .NET a REST API.

### <a name="mappings-for-filter-strings"></a>Mapování pro řetězce filtrů
* **Metody seznamu .NET**: Každá metoda rozhraní .NET API v tomto sloupci přijímá jako parametr objekt [ODATADetailLevel.][odata]
* **Požadavky na seznam REST**: Každá stránka rozhraní REST API propojená v tomto sloupci obsahuje tabulku, která určuje vlastnosti a operace, které jsou povoleny v řetězcích *filtrů.* Tyto názvy vlastností a operace použijete při vytváření řetězce [ODATADetailLevel.FilterClause.][odata_filter]

| Metody seznamu .NET | Požadavky na seznam REST |
| --- | --- |
| [CertificateOperations.ListCertifikáty][net_list_certs] |[Seznam certifikátů v účtu][rest_list_certs] |
| [CloudTask.ListNodeFiles][net_list_task_files] |[Seznam souborů přidružených k úkolu][rest_list_task_files] |
| [JobOperations.ListJobPreparationandReleaseTaskStatus][net_list_jobprep_status] |[Uveďte stav úlohy přípravy a uvolnění úlohy pro úlohu][rest_list_jobprep_status] |
| [JobOperations.ListJobs][net_list_jobs] |[Seznam úloh v účtu][rest_list_jobs] |
| [JobOperations.ListNodeFiles][net_list_nodefiles] |[Seznam souborů v uzlu][rest_list_nodefiles] |
| [JobOperations.ListTasks][net_list_tasks] |[Seznam úkolů přidružených k úloze][rest_list_tasks] |
| [JobScheduleOperations.ListJobSchedules][net_list_job_schedules] |[Seznam plánů úloh v účtu][rest_list_job_schedules] |
| [JobScheduleOperations.ListJobs][net_list_schedule_jobs] |[Seznam úloh přidružených k plánu úloh][rest_list_schedule_jobs] |
| [PoolOperations.ListComputeNodes][net_list_compute_nodes] |[Seznam výpočetních uzlů ve fondu][rest_list_compute_nodes] |
| [PoolOperations.ListPools][net_list_pools] |[Seznam fondů v účtu][rest_list_pools] |

### <a name="mappings-for-select-strings"></a>Mapování pro vybrané řetězce
* **Dávkové typy .NET:** Dávkové typy rozhraní API .NET.
* **Rest API entity**: Každá stránka v tomto sloupci obsahuje jednu nebo více tabulek, které seznam názvů vlastností rozhraní REST API pro daný typ. Tyto názvy vlastností se používají při vytváření *řetězců výběru.* Tyto stejné názvy vlastností použijete při vytváření řetězce [ODATADetailLevel.SelectClause.][odata_select]

| Dávkové typy .NET | Entity ROZHRANÍ REST API |
| --- | --- |
| [Certifikát][net_cert] |[Získání informací o certifikátu][rest_get_cert] |
| [CloudJob][net_job] |[Získání informací o úloze][rest_get_job] |
| [CloudJobSchedule][net_schedule] |[Získání informací o plánu úloh][rest_get_schedule] |
| [ComputeNode][net_node] |[Získání informací o uzlu][rest_get_node] |
| [Cloudpool][net_pool] |[Získání informací o fondu][rest_get_pool] |
| [CloudTask][net_task] |[Získání informací o úkolu][rest_get_task] |

## <a name="example-construct-a-filter-string"></a>Příklad: vytvoření řetězce filtru
Při vytváření řetězce filtru pro [ODATADetailLevel.FilterClause][odata_filter], naleznete v tabulce výše v části "Mapování pro řetězce filtrů" najít stránku dokumentace rozhraní REST API, která odpovídá operaci seznamu, který chcete provést. Filtrovatelné vlastnosti a jejich podporované operátory najdete v první tabulce více řádků na této stránce. Pokud chcete načíst všechny úkoly, jejichž ukončovací kód byl nenulový, například tento řádek na [Seznamu úkolů přidružených k úloze][rest_list_tasks] určuje příslušný řetězec vlastností a povolené operátory:

| Vlastnost | Operace povoleny | Typ |
|:--- |:--- |:--- |
| `executionInfo/exitCode` |`eq, ge, gt, le , lt` |`Int` |

Proto filtrační řetězec pro výpis všech úkolů s nenulovým ukončovacím kódem by byl:

`(executionInfo/exitCode lt 0) or (executionInfo/exitCode gt 0)`

## <a name="example-construct-a-select-string"></a>Příklad: vytvoření výběrového řetězce
Chcete-li vytvořit [ODATADetailLevel.SelectClause][odata_select], podívejte se do výše uvedené tabulky v části "Mapování pro vybrané řetězce" a přejděte na stránku rozhraní REST API, která odpovídá typu entity, kterou uvádíte. Volitelné vlastnosti a jejich podporované operátory najdete v první tabulce více řádků na této stránce. Pokud chcete například načíst pouze ID a příkazový řádek pro každý úkol v seznamu, najdete tyto řádky v příslušné tabulce na [vzkvétat informace o úkolu][rest_get_task]:

| Vlastnost | Typ | Poznámky |
|:--- |:--- |:--- |
| `id` |`String` |`The ID of the task.` |
| `commandLine` |`String` |`The command line of the task.` |

Vybraný řetězec pro zahrnutí pouze ID a příkazového řádku s každou uvedenou úlohou by pak byl:

`id, commandLine`

## <a name="code-samples"></a>Ukázky kódů
### <a name="efficient-list-queries-code-sample"></a>Ukázka kódu efektivních dotazů na seznam
Podívejte se na ukázkový projekt [EfficientListQueries][efficient_query_sample] na GitHubu a zjistěte, jak efektivní dotazování seznamu může ovlivnit výkon v aplikaci. Tato konzolová aplikace jazyka C# vytvoří a přidá velký počet úloh do úlohy. Potom provede více volání [JobOperations.ListTasks][net_list_tasks] metoda a předá [Objekty ODATADetailLevel,][odata] které jsou nakonfigurovány s různými hodnotami vlastností měnit množství dat, která mají být vráceny. Vytváří výstup podobný následujícímu:

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

Jak je znázorněno v uplynulých časech, můžete výrazně snížit dobu odezvy na dotaz omezením vlastností a počet položek, které jsou vráceny. Můžete najít tento a další ukázkové projekty v [azure-batch ukázky][github_samples] úložiště na GitHubu.

### <a name="batchmetrics-library-and-code-sample"></a>Ukázka knihovny BatchMetrics a kódu
Kromě ukázky kódu EfficientListQueries výše najdete projekt [BatchMetrics][batch_metrics] v úložišti GitHub [s předělanými ukázkami azure.][github_samples] Ukázkový projekt BatchMetrics ukazuje, jak efektivně sledovat průběh úlohy Azure Batch pomocí dávkového rozhraní API.

Ukázka [BatchMetrics][batch_metrics] obsahuje projekt knihovny tříd .NET, který můžete začlenit do vlastních projektů, a jednoduchý program příkazového řádku pro cvičení a demonstraci použití knihovny.

Ukázková aplikace v rámci projektu demonstruje následující operace:

1. Výběr konkrétních atributů, aby bylo možné stáhnout pouze vlastnosti, které potřebujete
2. Filtrování časů přechodu stavu, aby bylo možné stáhnout pouze změny od posledního dotazu

Například následující metoda se zobrazí v knihovně BatchMetrics. Vrátí ODATADetailLevel, který určuje, `id` že `state` by měly být získány pouze vlastnosti a pro entity, které jsou dotazovány. Také určuje, že by měly být vráceny pouze entity, jejichž stav se změnil od zadaného `DateTime` parametru.

```csharp
internal static ODATADetailLevel OnlyChangedAfter(DateTime time)
{
    return new ODATADetailLevel(
        selectClause: "id, state",
        filterClause: string.Format("stateTransitionTime gt DateTime'{0:o}'", time)
    );
}
```

## <a name="next-steps"></a>Další kroky
### <a name="parallel-node-tasks"></a>Úlohy paralelních uzlů
[Maximalizace využití výpočetních prostředků Azure Batch pomocí souběžných úloh uzlů](batch-parallel-node-tasks.md) je další článek související s výkonem dávkové aplikace. Některé typy úloh mohou mít prospěch z provádění paralelních úloh na větších, ale méně výpočetních uzlech. Podívejte se na [příklad scénáře](batch-parallel-node-tasks.md#example-scenario) v článku podrobnosti o takovém scénáři.


[api_net]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch
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
