---
title: Monitorování služby Batch pomocí Application Insights Azure
description: Naučte se, jak instrumentovat aplikaci Azure Batch .NET pomocí knihovny Azure Application Insights.
ms.topic: how-to
ms.custom: devx-track-csharp
ms.date: 04/05/2018
ms.openlocfilehash: 9decb99c3de798df43dedc2441208066d18e3a13
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104605779"
---
# <a name="monitor-and-debug-an-azure-batch-net-application-with-application-insights"></a>Monitorování a ladění aplikace Azure Batch .NET pomocí Application Insights

[Application Insights](../azure-monitor/app/app-insights-overview.md) poskytuje vývojářům elegantní a výkonný způsob, jak monitorovat a ladit aplikace nasazené ve službách Azure. Pomocí Application Insights můžete monitorovat čítače a výjimky výkonu a také instrumentovat kód s využitím vlastních metrik a trasování. Integrace Application Insights s vaší aplikací Azure Batch vám umožní získat podrobné přehledy o chování a prozkoumat problémy téměř v reálném čase.

Tento článek ukazuje, jak přidat a nakonfigurovat knihovnu Application Insights do řešení .NET Azure Batch a instrumentovat kód aplikace. Také ukazuje způsoby monitorování aplikace pomocí Azure Portal a vytváření vlastních řídicích panelů. V případě podpory Application Insights v jiných jazycích si prohlédněte [dokumentaci jazyky, platformy a integrace](../azure-monitor/app/platforms.md).

Ukázkové řešení jazyka C# s kódem, který se doprovází do tohoto článku, je k dispozici na [GitHubu](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ApplicationInsights). Tento příklad přidá do [ukázce topnwords](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords) příkladu kód instrumentace Application Insights. Pokud s tímto příkladem nejste obeznámeni, zkuste nejprve sestavit a spustit ukázce topnwords. Pomůže vám to pochopit základní dávkový pracovní postup zpracování sady vstupních objektů BLOB paralelně na několika výpočetních uzlech. 

> [!TIP]
> Jako alternativu můžete nakonfigurovat řešení Batch tak, aby zobrazovalo Application Insightsá data, jako jsou čítače výkonu virtuálních počítačů v Batch Explorer. [Batch Explorer](https://github.com/Azure/BatchExplorer) je bezplatný a samostatný klientský nástroj s bohatými funkcemi, který vám umožní vytvářet, ladit a monitorovat Azure Batch aplikace. Můžete si stáhnout [instalační balíček](https://azure.github.io/BatchExplorer/) pro Mac, Linux nebo Windows. Rychlé kroky pro povolení Application Insights dat v Batch Explorer najdete v části [úložiště Batch-Insights](https://github.com/Azure/batch-insights) . 
>

## <a name="prerequisites"></a>Předpoklady
* [Visual Studio 2017 nebo novější](https://www.visualstudio.com/vs)

* [Účet Batch a propojený účet úložiště](batch-account-create-portal.md)

* [Prostředek Application Insights](../azure-monitor/app/create-new-resource.md )
  
   * Pomocí Azure Portal vytvořte *prostředek* Application Insights. Vyberte typ *Obecné* **aplikace**.

   * Zkopírujte [klíč instrumentace](../azure-monitor/app/create-new-resource.md#copy-the-instrumentation-key) z portálu. Je potřeba ho později v tomto článku.
  
  > [!NOTE]
  > Můžete se vám [účtovat](https://azure.microsoft.com/pricing/details/application-insights/) data uložená v Application Insights. To zahrnuje diagnostická data a monitorování, která jsou popsána v tomto článku.
  > 

## <a name="add-application-insights-to-your-project"></a>Přidání Application Insights do projektu

Pro váš projekt se vyžaduje balíček NuGet **Microsoft. ApplicationInsights. windowsserver** a jeho závislosti. Přidejte nebo obnovte do projektu aplikace. K instalaci balíčku použijte `Install-Package` příkaz nebo správce balíčků NuGet.

```powershell
Install-Package Microsoft.ApplicationInsights.WindowsServer
```
Odkaz Application Insights z vaší aplikace .NET pomocí oboru názvů **Microsoft. ApplicationInsights** .

## <a name="instrument-your-code"></a>Instrumentace kódu

Chcete-li instrumentovat svůj kód, vaše řešení musí vytvořit Application Insights [TelemetryClient](/dotnet/api/microsoft.applicationinsights.telemetryclient). V tomto příkladu TelemetryClient načte svou konfiguraci z [ApplicationInsights.config](../azure-monitor/app/configuration-with-applicationinsights-config.md) souboru. Nezapomeňte aktualizovat ApplicationInsights.config v následujících projektech s vaším klíčem Application Insights instrumentace: Microsoft.Azure.Batch. Samples. TelemetryStartTask a TopNWordsSample.

```xml
<InstrumentationKey>YOUR-IKEY-GOES-HERE</InstrumentationKey>
```
Do souboru ukázce topnwords. cs přidejte taky klíč instrumentace.

Příklad v ukázce topnwords. cs používá následující [volání instrumentace](../azure-monitor/app/api-custom-events-metrics.md) z rozhraní Application Insights API:
* `TrackMetric()` -Sleduje, jak dlouho potřebuje výpočetní uzel stáhnout požadovaný textový soubor.
* `TrackTrace()` – Přidá volání ladění do kódu.
* `TrackEvent()` – Sleduje zajímavé události k zachycení.

Tento příklad záměrně opustí zpracování výjimek. Místo toho Application Insights automaticky hlásí neošetřené výjimky, což významně vylepšuje možnosti ladění. 

Následující fragment kódu ukazuje, jak tyto metody použít.

```csharp
public void CountWords(string blobName, int numTopN, string storageAccountName, string storageAccountKey)
{
    // simulate exception for some set of tasks
    Random rand = new Random();
    if (rand.Next(0, 10) % 10 == 0)
    {
        blobName += ".badUrl";
    }

    // log the url we are downloading the file from
    insightsClient.TrackTrace(new TraceTelemetry(string.Format("Task {0}: Download file from: {1}", this.taskId, blobName), SeverityLevel.Verbose));

    // open the cloud blob that contains the book
    var storageCred = new StorageCredentials(storageAccountName, storageAccountKey);
    CloudBlockBlob blob = new CloudBlockBlob(new Uri(blobName), storageCred);
    using (Stream memoryStream = new MemoryStream())
    {
        // calculate blob download time
        DateTime start = DateTime.Now;
        blob.DownloadToStream(memoryStream);
        TimeSpan downloadTime = DateTime.Now.Subtract(start);

        // track how long the blob takes to download on this node
        // this will help debug timing issues or identify poorly performing nodes
        insightsClient.TrackMetric("Blob download in seconds", downloadTime.TotalSeconds, this.CommonProperties);

        memoryStream.Position = 0; //Reset the stream
        var sr = new StreamReader(memoryStream);
        var myStr = sr.ReadToEnd();
        string[] words = myStr.Split(' ');

        // log how many words were found in the text file
        insightsClient.TrackTrace(new TraceTelemetry(string.Format("Task {0}: Found {1} words", this.taskId, words.Length), SeverityLevel.Verbose));
        var topNWords =
            words.
                Where(word => word.Length > 0).
                GroupBy(word => word, (key, group) => new KeyValuePair<String, long>(key, group.LongCount())).
                OrderByDescending(x => x.Value).
                Take(numTopN).
                ToList();
        foreach (var pair in topNWords)
        {
            Console.WriteLine("{0} {1}", pair.Key, pair.Value);
        }

        // emit an event to track the completion of the task
        insightsClient.TrackEvent("Done counting words");
    }
}
```

### <a name="azure-batch-telemetry-initializer-helper"></a>Pomocná rutina inicializátoru telemetrie Azure Batch
Při vytváření sestav telemetrie pro daný server a instanci Application Insights pro výchozí hodnoty používá roli virtuálních počítačů Azure a název virtuálního počítače. V souvislosti s Azure Batch příklad ukazuje, jak místo toho použít název fondu a výpočetní uzel. K přepsání výchozích hodnot použijte [inicializátor telemetrie](../azure-monitor/app/api-filtering-sampling.md#add-properties) . 

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;
using System;
using System.Threading;

namespace Microsoft.Azure.Batch.Samples.TelemetryInitializer
{
    public class AzureBatchNodeTelemetryInitializer : ITelemetryInitializer
    {
        // Azure Batch environment variables
        private const string PoolIdEnvironmentVariable = "AZ_BATCH_POOL_ID";
        private const string NodeIdEnvironmentVariable = "AZ_BATCH_NODE_ID";

        private string roleInstanceName;
        private string roleName;

        public void Initialize(ITelemetry telemetry)
        {
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
            {
                // override the role name with the Azure Batch Pool name
                string name = LazyInitializer.EnsureInitialized(ref this.roleName, this.GetPoolName);
                telemetry.Context.Cloud.RoleName = name;
            }

            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleInstance))
            {
                // override the role instance with the Azure Batch Compute Node name
                string name = LazyInitializer.EnsureInitialized(ref this.roleInstanceName, this.GetNodeName);
                telemetry.Context.Cloud.RoleInstance = name;
            }
        }

        private string GetPoolName()
        {
            return Environment.GetEnvironmentVariable(PoolIdEnvironmentVariable) ?? string.Empty;
        }

        private string GetNodeName()
        {
            return Environment.GetEnvironmentVariable(NodeIdEnvironmentVariable) ?? string.Empty;
        }
    }
}
```

Chcete-li povolit inicializátor telemetrie, soubor ApplicationInsights.config v projektu TopNWordsSample zahrnuje následující:

```xml
<TelemetryInitializers>
    <Add Type="Microsoft.Azure.Batch.Samples.TelemetryInitializer.AzureBatchNodeTelemetryInitializer, Microsoft.Azure.Batch.Samples.TelemetryInitializer"/>
</TelemetryInitializers>
``` 

## <a name="update-the-job-and-tasks-to-include-application-insights-binaries"></a>Aktualizace úlohy a úloh pro zahrnutí Application Insights binárních souborů

Aby Application Insights správně běžela na výpočetních uzlech, ujistěte se, že jsou binární soubory správně umístěny. Přidejte požadované binární soubory do kolekce souborů prostředků úlohy tak, aby se stáhly v době, kdy se úloha spustí. Následující fragmenty kódu jsou podobné kódu v úloze Job. cs.

Nejdřív vytvořte statický seznam Application Insights souborů k nahrání.

```csharp
private static readonly List<string> AIFilesToUpload = new List<string>()
{
    // Application Insights config and assemblies
    "ApplicationInsights.config",
    "Microsoft.ApplicationInsights.dll",
    "Microsoft.AI.Agent.Intercept.dll",
    "Microsoft.AI.DependencyCollector.dll",
    "Microsoft.AI.PerfCounterCollector.dll",
    "Microsoft.AI.ServerTelemetryChannel.dll",
    "Microsoft.AI.WindowsServer.dll",
    
    // custom telemetry initializer assemblies
    "Microsoft.Azure.Batch.Samples.TelemetryInitializer.dll",
 };
...
```

Dále vytvořte pracovní soubory, které úloha používá.
```csharp
...
// create file staging objects that represent the executable and its dependent assembly to run as the task.
// These files are copied to every node before the corresponding task is scheduled to run on that node.
FileToStage topNWordExe = new FileToStage(TopNWordsExeName, stagingStorageAccount);
FileToStage storageDll = new FileToStage(StorageClientDllName, stagingStorageAccount);

// Upload Application Insights assemblies
List<FileToStage> aiStagedFiles = new List<FileToStage>();
foreach (string aiFile in AIFilesToUpload)
{
    aiStagedFiles.Add(new FileToStage(aiFile, stagingStorageAccount));
}
...
```

`FileToStage`Metoda je pomocná funkce v ukázce kódu, která umožňuje snadno nahrát soubor z místního disku do objektu blob Azure Storage. Každý soubor je později stažen do výpočetního uzlu a na něj odkazuje úkol.

Nakonec přidejte úlohy do úlohy a zahrňte nezbytné Application Insights binární soubory.
```csharp
...
// initialize a collection to hold the tasks that will be submitted in their entirety
List<CloudTask> tasksToRun = new List<CloudTask>(topNWordsConfiguration.NumberOfTasks);
for (int i = 1; i <= topNWordsConfiguration.NumberOfTasks; i++)
{
    CloudTask task = new CloudTask("task_no_" + i, String.Format("{0} --Task {1} {2} {3} {4}",
        TopNWordsExeName,
        string.Format("https://{0}.blob.core.windows.net/{1}",
            accountSettings.StorageAccountName,
            documents[i]),
        topNWordsConfiguration.TopWordCount,
        accountSettings.StorageAccountName,
        accountSettings.StorageAccountKey));

    //This is the list of files to stage to a container -- for each job, one container is created and 
    //files all resolve to Azure Blobs by their name (so two tasks with the same named file will create just 1 blob in
    //the container).
    task.FilesToStage = new List<IFileStagingProvider>
                        {
                            // required application binaries
                            topNWordExe,
                            storageDll,
                        };
    foreach (FileToStage stagedFile in aiStagedFiles)
   {
        task.FilesToStage.Add(stagedFile);
   }    
    task.RunElevated = false;
    tasksToRun.Add(task);
}
```

## <a name="view-data-in-the-azure-portal"></a>Zobrazit data v Azure Portal

Teď, když jste nakonfigurovali úlohu a úkoly, které se mají použít Application Insights, spusťte ukázkovou úlohu ve vašem fondu. Přejděte do Azure Portal a otevřete prostředek Application Insights, který jste zřídili. Po zřízení fondu byste měli začít sledovat tok dat a protokolování. Zbývající část tohoto článku se dotýká jenom několika Application Insightsch funkcí, ale můžete si klidně prozkoumat celou sadu funkcí.

### <a name="view-live-stream-data"></a>Zobrazení dat živého streamu

Pokud chcete zobrazit protokoly trasování v prostředku Application Insights, klikněte na **Live Stream**. Následující snímek obrazovky ukazuje, jak zobrazit živá data přicházející z výpočetních uzlů ve fondu, například využití CPU na výpočetní uzel.

![Data výpočetního uzlu živého streamu](./media/monitor-application-insights/applicationinsightslivestream.png)

### <a name="view-trace-logs"></a>Zobrazit protokoly trasování

Pokud chcete zobrazit protokoly trasování v prostředku Application Insights, klikněte na **Hledat**. Toto zobrazení ukazuje seznam diagnostických dat zachycených Application Insights včetně trasování, událostí a výjimek. 

Následující snímek obrazovky ukazuje, jak se protokoluje jedno trasování pro úlohu a později se dotazuje na účely ladění.

![Obrázek trasovacích protokolů](./media/monitor-application-insights/tracelogsfortask.png)

### <a name="view-unhandled-exceptions"></a>Zobrazit neošetřené výjimky

Následující snímky obrazovky ukazují, jak Application Insights protokoluje výjimky vyvolané z vaší aplikace. V tomto případě, v průběhu sekund od aplikace vyvolala výjimku, můžete přejít na konkrétní výjimku a diagnostikovat problém.

![Neošetřené výjimky](./media/monitor-application-insights/exception.png)

### <a name="measure-blob-download-time"></a>Čas stažení objektu BLOB míry

Vlastní metriky jsou také cenným nástrojem na portálu. Můžete například zobrazit průměrnou dobu trvání každého výpočetního uzlu ke stažení požadovaného textového souboru, který zpracovává.

Vytvoření ukázkového grafu:
1. V prostředku Application Insights klikněte na **Průzkumník metrik**  >  **přidat graf**.
2. V přidaném grafu klikněte na **Upravit** .
2. Aktualizujte podrobnosti grafu následujícím způsobem:
   * Nastavte **typ grafu** na **Grid**.
   * Nastavte **agregaci** na **průměr**.
   * Nastavte **Seskupit podle** na **NodeId**.
   * V **metrikách** vyberte **vlastní**  >  **stahování objektů BLOB během několika sekund**.
   * Upravte **paletu barev** zobrazení podle vašeho výběru. 

![Doba stahování objektu BLOB na uzel](./media/monitor-application-insights/blobdownloadtime.png)


## <a name="monitor-compute-nodes-continuously"></a>Průběžné monitorování výpočetních uzlů

Možná jste si všimli, že všechny metriky, včetně čítačů výkonu, jsou protokolovány pouze v případě, že jsou úlohy spuštěny. Toto chování je užitečné, protože omezuje množství dat, která Application Insights protokoly. Existují však případy, kdy byste měli vždy monitorovat výpočetní uzly. Například mohou používat práci na pozadí, která není naplánována prostřednictvím služby Batch. V takovém případě nastavte proces monitorování tak, aby běžel po dobu životnosti výpočetního uzlu. 

Jedním ze způsobů, jak toto chování dosáhnout, je vytvořit proces, který načte knihovnu Application Insights a spustí se na pozadí. V tomto příkladu spouštěcí úkol načte binární soubory na počítači a udržuje proces běžící po neomezenou dobu. Nakonfigurujte konfigurační soubor Application Insights, aby tento proces vygeneroval další data, která vás zajímají, jako jsou čítače výkonu.

```csharp
...
 // Batch start task telemetry runner
private const string BatchStartTaskFolderName = "StartTask";
private const string BatchStartTaskTelemetryRunnerName = "Microsoft.Azure.Batch.Samples.TelemetryStartTask.exe";
private const string BatchStartTaskTelemetryRunnerAIConfig = "ApplicationInsights.config";
...
CloudPool pool = client.PoolOperations.CreatePool(
    topNWordsConfiguration.PoolId,
    targetDedicated: topNWordsConfiguration.PoolNodeCount,
    virtualMachineSize: "standard_d1_v2",
    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));
...

// Create a start task which will run a dummy exe in background that simply emits performance
// counter data as defined in the relevant ApplicationInsights.config.
// Note that the waitForSuccess on the start task was not set so the Compute Node will be
// available immediately after this command is run.
pool.StartTask = new StartTask()
{
    CommandLine = string.Format("cmd /c {0}", BatchStartTaskTelemetryRunnerName),
    ResourceFiles = resourceFiles
};
...
```

> [!TIP]
> Chcete-li zvýšit spravovatelnost svého řešení, můžete sestavení seskupit do [balíčku aplikace](./batch-application-packages.md). Pak pro nasazení balíčku aplikace do fondů automaticky přidejte odkaz na balíček aplikace do konfigurace fondu.
>

## <a name="throttle-and-sample-data"></a>Omezení a ukázková data 

Z důvodu velké škály Azure Batch aplikací běžících v produkčním prostředí můžete chtít omezit množství dat shromažďovaných Application Insightsmi za účelem správy nákladů. Pokud chcete dosáhnout některých mechanismů, přečtěte si téma [vzorkování v Application Insights](../azure-monitor/app/sampling.md) .


## <a name="next-steps"></a>Další kroky
* Přečtěte si další informace o [Application Insights](../azure-monitor/app/app-insights-overview.md).

* V případě podpory Application Insights v jiných jazycích si prohlédněte [dokumentaci jazyky, platformy a integrace](../azure-monitor/app/platforms.md).


