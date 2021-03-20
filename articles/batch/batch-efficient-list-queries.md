---
title: Návrh efektivních dotazů na seznam
description: Zvyšte výkon filtrováním dotazů při žádosti o informace o prostředcích služby Batch, jako jsou fondy, úlohy, úlohy a výpočetní uzly.
ms.topic: how-to
ms.date: 06/18/2020
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 3a767cc8ae3c8c48e1e40e0735c33fa807ba0015
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "88933510"
---
# <a name="create-queries-to-list-batch-resources-efficiently"></a>Efektivní vytváření dotazů k vypsání prostředků Batch

Téměř všechny aplikace Batch musí provádět určitý typ monitorování nebo jiné operace, která se dotazuje na službu Batch, často v pravidelných intervalech. Například chcete-li zjistit, zda v úloze zbývá úlohy ve frontě, je nutné získat data pro každý úkol v rámci úlohy. Chcete-li zjistit stav uzlů ve fondu, je třeba získat data na každém uzlu ve fondu. Tento článek vysvětluje, jak provádět takové dotazy nejúčinnějším způsobem.

Můžete zvýšit výkon aplikace Azure Batch tím, že omezíte množství dat, která služba vrátí, při dotazování úloh, úloh, výpočetních uzlů a dalších prostředků pomocí knihovny [Batch .NET](/dotnet/api/microsoft.azure.batch) .

> [!NOTE]
> Služba Batch poskytuje podporu rozhraní API pro běžné scénáře počítání úloh v úloze a počítání výpočetních uzlů ve fondu služby Batch. Místo použití dotazu na seznam pro tyto účely můžete zavolat operace [získat počty úloh](/rest/api/batchservice/job/gettaskcounts) a [počet uzlů výpisu fondu](/rest/api/batchservice/account/listpoolnodecounts) . Tyto operace jsou efektivnější než dotaz na seznam, ale vracejí více omezené informace, které nemusí být vždy aktuální. Další informace najdete v tématu [počítání úloh a výpočetních uzlů podle stavu](batch-get-resource-counts.md).

## <a name="specify-a-detail-level"></a>Zadat úroveň podrobností

V rámci výrobní dávkové aplikace můžou entity jako úlohy, úlohy a výpočetní uzly očíslovat v tisících. Když si vyžádáte informace o těchto prostředcích, může se stát, že se z služby Batch od služby Batch do vaší aplikace na každém dotazu navzájem potenciálně velké množství dat. Omezením počtu položek a typu informací, které je vráceno dotazem, můžete zvýšit rychlost dotazů, a tedy i výkon vaší aplikace.

Tento fragment kódu pro [dávku .NET](/dotnet/api/microsoft.azure.batch) *API obsahuje* všechny úlohy, které jsou přidružené k úloze, spolu se *všemi* vlastnostmi každé úlohy:

```csharp
// Get a collection of all of the tasks and all of their properties for job-001
IPagedEnumerable<CloudTask> allTasks =
    batchClient.JobOperations.ListTasks("job-001");
```

Můžete použít mnohem efektivnější dotaz na seznam, ale použitím "úrovně podrobností" na dotaz. Provedete to tak, že zadáte objekt [ODATADetailLevel](/dotnet/api/microsoft.azure.batch.odatadetaillevel) do metody [JobOperations. ListTasks](/dotnet/api/microsoft.azure.batch.joboperations) . Tento fragment kódu vrátí pouze vlastnosti informací o dokončených úkolech ID, příkazového řádku a výpočetního uzlu:

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

V tomto ukázkovém scénáři platí, že pokud je v úloze tisíce úkolů, výsledky z druhého dotazu se většinou vrátí mnohem rychleji než první. Další informace o použití ODATADetailLevel při výpisu položek pomocí rozhraní Batch .NET API je obsaženo [níže](#efficient-querying-in-batch-net).

> [!IMPORTANT]
> Důrazně doporučujeme, abyste vždy zadali objekt ODATADetailLevel do vašeho seznamu volání rozhraní .NET API, abyste zajistili maximální efektivitu a výkon vaší aplikace. Zadáním úrovně podrobností můžete pomoci snížit dobu odezvy služby Batch, zlepšit využití sítě a minimalizovat využití paměti klientskými aplikacemi.

## <a name="filter-select-and-expand"></a>Filtrovat, vybrat a rozbalit

Rozhraní REST API služby [Batch .NET](/dotnet/api/microsoft.azure.batch) a [Batch](/rest/api/batchservice/) poskytují možnost omezit počet položek, které jsou vráceny v seznamu, a také množství informací, které jsou pro každý z nich vráceny. Provedete to tak, že zadáte **Filter**, **Select** a **rozbalíte řetězce** při provádění dotazů seznamu.

### <a name="filter"></a>Filtrovat

Řetězec filtru je výraz, který snižuje počet vrácených položek. Můžete například vypsat pouze spuštěné úlohy pro úlohu nebo zobrazit seznam pouze výpočetních uzlů, které jsou připraveny ke spouštění úkolů.

Řetězec filtru se skládá z jednoho nebo více výrazů s výrazem, který se skládá z názvu vlastnosti, operátoru a hodnoty. Vlastnosti, které lze zadat, jsou specifické pro každý typ entity, který se dotazuje, stejně jako operátory podporované pro jednotlivé vlastnosti.  Více výrazů lze kombinovat pomocí logických operátorů `and` a `or` .

V tomto příkladu řetězce filtru se zobrazí pouze spuštěné úlohy vykreslení: `(state eq 'running') and startswith(id, 'renderTask')` .

### <a name="select"></a>Vyberte

Řetězec Select omezuje hodnoty vlastností, které jsou vráceny pro každou položku. Zadejte seznam názvů vlastností oddělených čárkami a pro položky ve výsledcích dotazu jsou vráceny pouze tyto hodnoty vlastností. Můžete zadat libovolnou vlastnost pro typ entity, který se dotazuje.

Tento příklad výběru řetězce určuje, že pro každý úkol by měly být vráceny pouze tři hodnoty vlastností: `id, state, stateTransitionTime` .

### <a name="expand"></a>Rozbalit

Řetězec expand omezuje počet volání rozhraní API potřebných k získání určitých informací. Když použijete expandující řetězec, můžete získat další informace o jednotlivých položkách s jedním voláním rozhraní API. Místo toho, abyste získali seznam entit a požadovali informace pro každou položku v seznamu, můžete použít rozbalení řetězce a získat stejné informace v jediném volání rozhraní API, což pomáhá zvýšit výkon snížením počtu volání rozhraní API.

Podobně jako u řetězce příkazu SELECT určuje řetězec expand, zda jsou určitá data součástí výsledků dotazu seznamu. Pokud jsou vyžadovány všechny vlastnosti a není zadán žádný řetězec SELECT, je *nutné* k získání informací o statistice použít řetězec pro rozbalení. Je-li pro získání podmnožiny vlastností použit řetězec SELECT, `stats` lze jej zadat v řetězci Select a řetězec rozbalení není nutné zadávat.

Řetězec rozbalení se podporuje jenom v případě, že se používá při výpisu úloh, plánů úloh, úloh a fondů. V současné době podporuje jenom informace o statistice.

Tento příklad rozbalení řetězce určuje, že se mají pro každou položku v seznamu vracet informace o statistice: `stats` .

> [!NOTE]
> Při sestavování kteréhokoliv ze tří typů řetězce dotazu (Filter, Select a expand) je nutné zajistit, aby názvy vlastností a velikost písmen odpovídaly jejich REST APIm prvkům. Například při práci s třídou .NET [CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask) je nutné zadat **stav** místo **stavu**, i když vlastnost .NET je [CloudTask. State](/dotnet/api/microsoft.azure.batch.cloudtask.state#Microsoft_Azure_Batch_CloudTask_State). V následujících tabulkách najdete mapování vlastností rozhraní .NET a rozhraní REST API.

### <a name="rules-for-filter-select-and-expand-strings"></a>Pravidla pro filtrování, výběr a rozbalení řetězců

- Vlastnosti v části filtrovat, vybrat a rozšířit řetězce by se měly zobrazovat stejně jako v rozhraní [REST](/rest/api/batchservice/) API pro Batch, a to i v případě, že používáte [Batch .NET](/dotnet/api/microsoft.azure.batch) nebo jednu z dalších sad SDK pro sadu Batch.
- U všech názvů vlastností se rozlišují velká a malá písmena, ale v hodnotách vlastností se nerozlišují malá a velká písmena.
- Řetězce data a času mohou být v jednom ze dvou formátů a musí předcházet `DateTime` .
  
  - Příklad formátu W3C-DTF: `creationTime gt DateTime'2011-05-08T08:49:37Z'`
  - Příklad formátu RFC 1123: `creationTime gt DateTime'Sun, 08 May 2011 08:49:37 GMT'`
- Logické řetězce jsou buď `true` nebo `false` .
- Pokud je zadána neplatná vlastnost nebo operátor, `400 (Bad Request)` bude výsledkem chyba.

## <a name="efficient-querying-in-batch-net"></a>Efektivní dotazování v dávce .NET

V rozhraní [Batch .NET](/dotnet/api/microsoft.azure.batch) API se třída [ODATADetailLevel](/dotnet/api/microsoft.azure.batch.odatadetaillevel) používá k poskytnutí filtru, výběru a rozbalení řetězců k vypsání operací. Třída ODataDetailLevel má tři vlastnosti veřejných řetězců, které lze zadat v konstruktoru nebo nastavit přímo na objekt. Pak předáte objekt ODataDetailLevel jako parametr do různých operací seznamu, jako je například [ListPools](/dotnet/api/microsoft.azure.batch.pooloperations), [ListJobs](/dotnet/api/microsoft.azure.batch.joboperations)a [ListTasks](/dotnet/api/microsoft.azure.batch.joboperations).

- [ODATADetailLevel. FilterClause](/dotnet/api/microsoft.azure.batch.odatadetaillevel.filterclause): omezí počet vrácených položek.
- [ODATADetailLevel. SelectClause](/dotnet/api/microsoft.azure.batch.odatadetaillevel.selectclause): Určete, které hodnoty vlastností se vrátí s každou položkou.
- [ODATADetailLevel. ExpandClause](/dotnet/api/microsoft.azure.batch.odatadetaillevel.expandclause): načítají data pro všechny položky v jednom volání rozhraní API namísto samostatných volání pro každou položku.

Následující fragment kódu používá rozhraní Batch .NET API k efektivnímu dotazování služby Batch na statistiky konkrétní sady fondů. V tomto scénáři má uživatel dávky testovací i výrobní fondy. ID fondů testů s předponou "test" a ID fondu výroby mají předponu "prod". Ve fragmentu kódu je *myBatchClient* správně inicializovaná instance třídy [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient) .

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
> Instance [ODATADetailLevel](/dotnet/api/microsoft.azure.batch.odatadetaillevel) , která je nakonfigurována pomocí klauzulí Select a expand, lze také předat příslušným metodám Get, jako je například [PoolOperations. getpool](/dotnet/api/microsoft.azure.batch.pooloperations.getpool#Microsoft_Azure_Batch_PoolOperations_GetPool_System_String_Microsoft_Azure_Batch_DetailLevel_System_Collections_Generic_IEnumerable_Microsoft_Azure_Batch_BatchClientBehavior__), pro omezení množství vrácených dat.

## <a name="batch-rest-to-net-api-mappings"></a>Mapování dávek z dávky do rozhraní .NET API

Názvy vlastností ve filtru, Select a rozbalte řetězce musí odrážet jejich REST API protějšky v názvu i v případě. Níže uvedené tabulky poskytují mapování mezi partnery .NET a REST API.

### <a name="mappings-for-filter-strings"></a>Mapování pro řetězce filtru

- **Metody seznamu .NET**: každá z metod rozhraní .NET API v tomto sloupci přijímá objekt [ODATADetailLevel](/dotnet/api/microsoft.azure.batch.odatadetaillevel) jako parametr.
- **Požadavky na seznam REST**: každá REST APIová stránka propojená s v tomto sloupci obsahuje tabulku, která určuje vlastnosti a operace, které jsou povoleny v řetězcích *filtrů* . Tyto názvy vlastností a operace použijete při vytváření řetězce [ODATADetailLevel. FilterClause](/dotnet/api/microsoft.azure.batch.odatadetaillevel.filterclause) .

| Metody seznamu .NET | Žádosti seznamu REST |
| --- | --- |
| [Metodu certificateoperations. ListCertificates](/dotnet/api/microsoft.azure.batch.certificateoperations) |[Výpis certifikátů v účtu](/rest/api/batchservice/certificate/list) |
| [CloudTask. ListNodeFiles](/dotnet/api/microsoft.azure.batch.cloudtask) |[Seznam souborů přidružených k úloze](/rest/api/batchservice/file/listfromtask) |
| [JobOperations. ListJobPreparationAndReleaseTaskStatus](/dotnet/api/microsoft.azure.batch.joboperations) |[Seznam stavů úloh přípravy úlohy a úkolů uvolnění úloh pro úlohu](/rest/api/batchservice/job/listpreparationandreleasetaskstatus) |
| [JobOperations. ListJobs](/dotnet/api/microsoft.azure.batch.joboperations) |[Výpis úloh v účtu](/rest/api/batchservice/job/list) |
| [JobOperations. ListNodeFiles](/dotnet/api/microsoft.azure.batch.joboperations) |[Výpis souborů na uzlu](/rest/api/batchservice/file/listfromcomputenode) |
| [JobOperations. ListTasks](/dotnet/api/microsoft.azure.batch.joboperations) |[Seznam úloh přidružených k úloze](/rest/api/batchservice/task/list) |
| [JobScheduleOperations.ListJobSchedules](/dotnet/api/microsoft.azure.batch.jobscheduleoperations) |[Výpis plánů úloh v účtu](/rest/api/batchservice/jobschedule/list) |
| [JobScheduleOperations.ListJobs](/dotnet/api/microsoft.azure.batch.jobscheduleoperations) |[Seznam úloh přidružených k plánu úlohy](/rest/api/batchservice/job/listfromjobschedule) |
| [PoolOperations. ListComputeNodes](/dotnet/api/microsoft.azure.batch.pooloperations) |[Výpis výpočetních uzlů ve fondu](/rest/api/batchservice/computenode/list) |
| [PoolOperations. ListPools](/dotnet/api/microsoft.azure.batch.pooloperations) |[Výpis fondů v účtu](/rest/api/batchservice/pool/list) |

### <a name="mappings-for-select-strings"></a>Mapování pro vybrané řetězce

- **Typy Batch .NET**: typy rozhraní API pro Batch .NET.
- **REST API entit**: každá stránka v tomto sloupci obsahuje jednu nebo více tabulek, které obsahují seznam názvů vlastností REST API pro daný typ. Tyto názvy vlastností se používají při vytváření řetězců *Select* . Tyto stejné názvy vlastností můžete použít při vytváření řetězce [ODATADetailLevel. SelectClause](/dotnet/api/microsoft.azure.batch.odatadetaillevel.selectclause) .

| Typy Batch .NET | REST API entit |
| --- | --- |
| [Certifikát](/dotnet/api/microsoft.azure.batch.certificate) |[Získání informací o certifikátu](/rest/api/batchservice/certificate/get) |
| [Vlastnosti cloudjob](/dotnet/api/microsoft.azure.batch.cloudjob) |[Získání informací o úloze](/rest/api/batchservice/job/get) |
| [CloudJobSchedule](/dotnet/api/microsoft.azure.batch.cloudjobschedule) |[Získat informace o plánu úlohy](/rest/api/batchservice/jobschedule/get) |
| [Metodu computenode](/dotnet/api/microsoft.azure.batch.computenode) |[Získání informací o uzlu](/rest/api/batchservice/computenode/get) |
| [CloudPool](/dotnet/api/microsoft.azure.batch.cloudpool) |[Získat informace o fondu](/rest/api/batchservice/pool/get) |
| [CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask) |[Získání informací o úkolu](/rest/api/batchservice/task/get) |

## <a name="example-construct-a-filter-string"></a>Příklad: vytvoření řetězce filtru

Při vytváření řetězce filtru pro [ODATADetailLevel. FilterClause](/dotnet/api/microsoft.azure.batch.odatadetaillevel.filterclause)si v tabulce výše v části "mapování pro řetězce filtru" vyhledejte stránku dokumentace REST API, která odpovídá operaci seznamu, kterou chcete provést. Vlastnosti Filtered a jejich podporované operátory najdete na této stránce v první tabulce násobení. Pokud chcete načíst všechny úlohy, jejichž ukončovací kód byl nenulový, například tento řádek v [seznamu úkolů přidružených k úloze](/rest/api/batchservice/task/list) určuje příslušný řetězec vlastnosti a přípustné operátory:

| Vlastnost | Povolené operace | Typ |
|:--- |:--- |:--- |
| `executionInfo/exitCode` |`eq, ge, gt, le , lt` |`Int` |

Proto řetězec filtru pro výpis všech úkolů s nenulovým ukončovacím kódem by byl:

`(executionInfo/exitCode lt 0) or (executionInfo/exitCode gt 0)`

## <a name="example-construct-a-select-string"></a>Příklad: vytvoření řetězce výběru

Chcete-li vytvořit [ODATADetailLevel. SelectClause](/dotnet/api/microsoft.azure.batch.odatadetaillevel.selectclause), přečtěte si výše v tabulce "mapování pro vybrané řetězce" a přejděte na stránku REST API, která odpovídá typu entity, kterou zadáváte. V první tabulce násobení na této stránce najdete vlastnosti umožňující výběr a jejich podporované operátory. Pokud chcete načíst pouze ID a příkazový řádek pro každý úkol v seznamu, najdete například tyto řádky v příslušné tabulce pro [získání informací o úkolu](/rest/api/batchservice/task/get):

| Vlastnost | Typ | Poznámky |
|:--- |:--- |:--- |
| `id` |`String` |`The ID of the task.` |
| `commandLine` |`String` |`The command line of the task.` |

Řetězec SELECT pro zahrnutí pouze ID a příkazového řádku s každým uvedeným úkolem by pak byl:

`id, commandLine`

## <a name="code-samples"></a>Ukázky kódů

### <a name="efficient-list-queries-code-sample"></a>Ukázka kódu efektivních dotazů na seznam

Vzorový projekt [EfficientListQueries](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/ArticleProjects/EfficientListQueries) na GitHubu ukazuje, jak efektivní dotazování na seznam může ovlivnit výkon v aplikaci. Tato Konzolová aplikace v jazyce C# vytvoří a přidá do úlohy velký počet úkolů. Pak provede více volání metody [JobOperations. ListTasks](/dotnet/api/microsoft.azure.batch.joboperations) a předá objekty [ODATADetailLevel](/dotnet/api/microsoft.azure.batch.odatadetaillevel) , které jsou nakonfigurovány s různými hodnotami vlastností, aby se lišilo množství dat, která mají být vrácena. Vytváří výstup podobný následujícímu:

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

Jak je znázorněno v uplynulých časech, můžete výrazně snížit dobu odezvy na dotazy omezením vlastností a počtu vrácených položek. Tento a další ukázkové projekty najdete v úložišti [Azure-Batch-Samples](https://github.com/Azure-Samples/azure-batch-samples) na GitHubu.

### <a name="batchmetrics-library-and-code-sample"></a>Ukázka knihovny a kódu BatchMetrics

Kromě výše uvedeného příkladu kódu EfficientListQueries ukázkový projekt [BatchMetrics](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/BatchMetrics) ukazuje, jak efektivně monitorovat průběh úloh Azure Batch pomocí rozhraní API pro Batch.

Ukázka [BatchMetrics](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/BatchMetrics) zahrnuje projekt knihovny tříd .NET, který můžete začlenit do svých vlastních projektů, a jednoduchý program příkazového řádku pro cvičení a demonstraci použití knihovny.

Ukázková aplikace v rámci projektu ukazuje následující operace:

1. Výběr konkrétních atributů, aby se stáhly jenom vlastnosti, které potřebujete
2. Filtrování časů přechodu stavu za účelem stažení pouze změn od posledního dotazu

Například následující metoda se zobrazí v knihovně BatchMetrics. Vrátí ODATADetailLevel, který určuje, že se `id` `state` mají získat pouze vlastnosti a pro entity, které jsou dotazovány. Také určuje, že by měly být vráceny pouze entity, jejichž stav byl změněn, protože `DateTime` by měl být vrácen zadaný parametr.

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

- Přečtěte si, jak [maximalizovat Azure Batch využití výpočetních prostředků pomocí souběžných úloh uzlů](batch-parallel-node-tasks.md). Některé typy úloh můžou těžit ze spouštění paralelních úloh na větších (ale méně) výpočetních uzlech. Podrobnosti o takovém scénáři najdete v [ukázkovém scénáři](batch-parallel-node-tasks.md#example-scenario) v článku.
- Naučte se [monitorovat řešení Batch pomocí počítání úkolů a uzlů podle jejich stavu](batch-get-resource-counts.md) .


[api_net]: /dotnet/api/microsoft.azure.batch
[api_net_listjobs]: /dotnet/api/microsoft.azure.batch.joboperations
[api_rest]: /rest/api/batchservice/
[batch_metrics]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchMetrics
[efficient_query_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/EfficientListQueries
[github_samples]: https://github.com/Azure/azure-batch-samples
[odata]: /dotnet/api/microsoft.azure.batch.odatadetaillevel
[odata_ctor]: /dotnet/api/microsoft.azure.batch.odatadetaillevel
[odata_expand]: /dotnet/api/microsoft.azure.batch.odatadetaillevel
[odata_filter]: /dotnet/api/microsoft.azure.batch.odatadetaillevel
[odata_select]: /dotnet/api/microsoft.azure.batch.odatadetaillevel

[net_list_certs]: /dotnet/api/microsoft.azure.batch.certificateoperations
[net_list_compute_nodes]: /dotnet/api/microsoft.azure.batch.pooloperations
[net_list_job_schedules]: /dotnet/api/microsoft.azure.batch.jobscheduleoperations
[net_list_jobprep_status]: /dotnet/api/microsoft.azure.batch.joboperations
[net_list_jobs]: /dotnet/api/microsoft.azure.batch.joboperations
[net_list_nodefiles]: /dotnet/api/microsoft.azure.batch.joboperations
[net_list_pools]: /dotnet/api/microsoft.azure.batch.pooloperations
[net_list_schedule_jobs]: /dotnet/api/microsoft.azure.batch.jobscheduleoperations
[net_list_task_files]: /dotnet/api/microsoft.azure.batch.cloudtask
[net_list_tasks]: /dotnet/api/microsoft.azure.batch.joboperations

[rest_list_certs]: /rest/api/batchservice/certificate/list
[rest_list_compute_nodes]: /rest/api/batchservice/computenode/list
[rest_list_job_schedules]: /rest/api/batchservice/jobschedule/list
[rest_list_jobprep_status]: /rest/api/batchservice/job/listpreparationandreleasetaskstatus
[rest_list_jobs]: /rest/api/batchservice/job/list
[rest_list_nodefiles]: /rest/api/batchservice/file/listfromcomputenode
[rest_list_pools]: /rest/api/batchservice/pool/list
[rest_list_schedule_jobs]: /rest/api/batchservice/job/listfromjobschedule
[rest_list_task_files]: /rest/api/batchservice/file/listfromtask
[rest_list_tasks]: /rest/api/batchservice/task/list

[rest_get_cert]: /rest/api/batchservice/certificate/get
[rest_get_job]: /rest/api/batchservice/job/get
[rest_get_node]: /rest/api/batchservice/computenode/get
[rest_get_pool]: /rest/api/batchservice/pool/get
[rest_get_schedule]: /rest/api/batchservice/jobschedule/get
[rest_get_task]: /rest/api/batchservice/task/get

[net_cert]: /dotnet/api/microsoft.azure.batch.certificate
[net_job]: /dotnet/api/microsoft.azure.batch.cloudjob
[net_node]: /dotnet/api/microsoft.azure.batch.computenode
[net_pool]: /dotnet/api/microsoft.azure.batch.cloudpool
[net_schedule]: /dotnet/api/microsoft.azure.batch.cloudjobschedule
[net_task]: /dotnet/api/microsoft.azure.batch.cloudtask

[rest_get_task_counts]: /rest/api/batchservice/get-the-task-counts-for-a-job
[rest_get_node_counts]: /rest/api/batchservice/account/listpoolnodecounts
