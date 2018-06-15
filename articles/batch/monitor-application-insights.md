---
title: Monitorování Batch pomocí služby Azure Application Insights | Microsoft Docs
description: Zjistěte, jak k instrumentaci aplikace Azure Batch .NET pomocí knihovny Azure Application Insights.
services: batch
author: dlepow
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: .NET
ms.topic: article
ms.workload: na
ms.date: 04/05/2018
ms.author: danlep
ms.openlocfilehash: 9f989ada01a2ffced509b42df9e46aa001386ab6
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2018
ms.locfileid: "34077390"
---
# <a name="monitor-and-debug-an-azure-batch-net-application-with-application-insights"></a>Sledování a ladění aplikace Azure Batch .NET s Application Insights

[Application Insights](../application-insights/app-insights-overview.md) poskytuje elegantní a výkonný nástroj pro vývojáře k monitorování a ladění aplikace nasazené do služby Azure. Pomocí Application Insights do monitorování čítače výkonu a výjimek, jakož i nástrojem kódu vlastní metriky a trasování. Integrace služby Application Insights s vaší aplikací Azure Batch umožňuje přímé proniknout do chování a prozkoumat problémy v skoro v reálném čase.

Tento článek ukazuje, jak přidávat a konfigurovat knihovnu Application Insights do řešení Azure Batch .NET a instrumentace kódu aplikace. Také ukazuje, jak monitorovat aplikace prostřednictvím portálu Azure a sestavit vlastní řídicí panely. Pro podporu Application Insights v jiných jazycích, podívejte se na [jazyky, platformy a integrace dokumentaci](../application-insights/app-insights-platforms.md).

Ukázka C# řešení s kódem, který může doprovázet Tento článek je k dispozici na [Githubu](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ApplicationInsights). Tento příklad přidá kód instrumentace Application Insights do [TopNWords](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords) příklad. Pokud si nejste obeznámeni s například, zkuste vytváření a spouštění TopNWords nejdřív. To vám pomůže porozumět základní pracovní postup dávkové zpracování sady objektů BLOB vstupní paralelně v několika výpočetních uzlech. 

## <a name="prerequisites"></a>Požadavky
* [Visual Studio IDE](https://www.visualstudio.com/vs) (Visual Studio 2015 nebo novější verzi)

* [Účet batch a propojený účet úložiště](batch-account-create-portal.md)

* [Prostředek Application Insights](../application-insights/app-insights-create-new-resource.md)
  
   * Pomocí portálu Azure k vytvoření Application Insights *prostředků*. Vyberte *Obecné* **typ aplikace**.

   * Kopírování [klíč instrumentace](../application-insights/app-insights-create-new-resource.md#copy-the-instrumentation-key) z portálu. Je třeba později v tomto článku.
  
  > [!NOTE]
  > Může být [účtovat](https://azure.microsoft.com/pricing/details/application-insights/) pro data uložená ve službě Application Insights. To zahrnuje diagnostiky a sledování dat popsané v tomto článku.
  > 

## <a name="add-application-insights-to-your-project"></a>Přidejte ke svému projektu Application Insights.

**Microsoft.ApplicationInsights.WindowsServer** balíček NuGet a jeho závislosti jsou požadovány pro váš projekt. Přidat nebo obnovit je do projektu vaší aplikace. Chcete-li nainstalovat balíček, použijte `Install-Package` příkaz nebo Správce balíčků NuGet.

```powershell
Install-Package Microsoft.ApplicationInsights.WindowsServer
```
Odkazování Application Insights z aplikace .NET pomocí **Microsoft.ApplicationInsights** oboru názvů.

## <a name="instrument-your-code"></a>Instrumentace kódu

K instrumentaci kódu, musí vaše řešení vytvořit Application Insights [TelemetryClient](/dotnet/api/microsoft.applicationinsights.telemetryclient). V příkladu TelemetryClient načte konfiguraci z [souboru ApplicationInsights.config](../application-insights/app-insights-configuration-with-applicationinsights-config.md) souboru. Nezapomeňte aktualizovat soubor ApplicationInsights.config v následujících projektech váš klíč instrumentace Application Insights: Microsoft.Azure.Batch.Samples.TelemetryStartTask a TopNWordsSample.

```xml
<InstrumentationKey>YOUR-IKEY-GOES-HERE</InstrumentationKey>
```
Klíč instrumentace můžete také přidáte v souboru TopNWords.cs.

V příkladu v TopNWords.cs používá následující [instrumentace volání](../application-insights/app-insights-api-custom-events-metrics.md) z Application Insights API:
* `TrackMetric()` -Sleduje, jak dlouho, v průměru výpočetního uzlu trvá stáhnout požadované textového souboru.
* `TrackTrace()` -Přidá ladění volání do kódu.
* `TrackEvent()` -Sleduje události pro zachycení zajímavé.

Tento příklad účelově vynechány výjimek. Místo toho Application Insights automaticky hlásí neošetřených výjimek, které výrazně zlepšuje funkce ladění. 

Následující fragment kódu ukazuje, jak používat tyto metody.

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

### <a name="azure-batch-telemetry-initializer-helper"></a>Azure Batch telemetrie inicializátoru pomocné rutiny
Při hlášení telemetrie pro daný server a instance, Application Insights používá název Role virtuálních počítačů Azure a virtuálních počítačů pro výchozí hodnoty. V rámci Azure Batch příklad ukazuje, jak používat název fondu a místo toho výpočetní název uzlu. Použití [telemetrie inicializátoru](../application-insights/app-insights-api-filtering-sampling.md#add-properties) přepsat výchozí hodnoty. 

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

Povolit inicializátoru telemetrická data, soubor ApplicationInsights.config v projektu TopNWordsSample zahrnuje následující položky:

```xml
<TelemetryInitializers>
    <Add Type="Microsoft.Azure.Batch.Samples.TelemetryInitializer.AzureBatchNodeTelemetryInitializer, Microsoft.Azure.Batch.Samples.TelemetryInitializer"/>
</TelemetryInitializers>
``` 

## <a name="update-the-job-and-tasks-to-include-application-insights-binaries"></a>Aktualizace úlohy a úlohy, které chcete zahrnout binárních souborů Application Insights

V pořadí pro službu Application Insights správné fungování na výpočetních uzlech Ujistěte se, že binární soubory jsou umístěny správně. Přidejte požadované binární soubory do kolekce souborů prostředků vaším úkolem tak, aby získat stáhnou v době, kdy provede úlohu. Následující fragmenty kódu jsou podobné kód v Job.cs.

Nejprve vytvořte statický seznam souborů Application Insights k odeslání.

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

Dále vytvořte pracovní soubory, které jsou používány úlohu.
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

`FileToStage` Metoda je pomocné funkce v ukázce kódu, který vám umožní snadno nahrát soubor z místního disku pro objekt blob úložiště Azure. Každý soubor je později stažen do výpočetního uzlu a odkazuje úlohu.

Nakonec do úlohy přidat úkoly a zahrnují binární soubory nutné Application Insights.
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

## <a name="view-data-in-the-azure-portal"></a>Zobrazení dat na portálu Azure

Nyní, když jste nakonfigurovali, úlohy a úlohy použít Application Insights, spusťte úlohu příklad ve vašem fondu. Přejděte na portál Azure a otevřete prostředek Application Insights, kterou jste zřídili. Po zřízení fondu, měli byste začít toku dat a získávání protokolována. Zbývající část tohoto článku dotykem na několik funkcí Application Insights, ale Nebojte se úplná sada funkcí zkoumat.

### <a name="view-live-stream-data"></a>Živý datový proud dat zobrazení

Chcete-li zobrazit protokoly trasování v prostředku statistiky aplikace, klikněte na tlačítko **živý datový proud**. Následující snímek obrazovky ukazuje, jak zobrazit za provozu dat pocházejících z výpočetních uzlů ve fondu, například využití procesoru na výpočetním uzlu.

![Dynamická data uzlu výpočetní datového proudu](./media/monitor-application-insights/applicationinsightslivestream.png)

### <a name="view-trace-logs"></a>Zobrazit protokoly trasování

Chcete-li zobrazit protokoly trasování v prostředku statistiky aplikace, klikněte na tlačítko **vyhledávání**. Toto zobrazení uvádí seznam diagnostická data zaznamenaná pomocí Application Insights, včetně trasování událostí a výjimky. 

Následující snímek obrazovky ukazuje, jak je jediné trasování pro úlohu protokolují a později dotaz pro účely ladění.

![Obrázek protokoly trasování](./media/monitor-application-insights/tracelogsfortask.png)

### <a name="view-unhandled-exceptions"></a>Zobrazit nezpracované výjimky

Na následujících snímcích obrazovky ukazuje, jak Application Insights protokoly výjimek vyvolaných z vaší aplikace. V takovém případě můžete během několika sekund aplikace způsobující výjimku, přejdete do určité výjimky a diagnostikovat problém.

![Nezpracované výjimky](./media/monitor-application-insights/exception.png)

### <a name="measure-blob-download-time"></a>Doba stahování měr objektů blob

Vlastní metriky jsou také cenným nástrojem na portálu. Můžete například zobrazit Průměrná doba, kterou trvalo každý výpočetní uzel stáhnout požadované textového souboru, který byl zpracování.

Vytvoření ukázkové grafu:
1. V prostředku Application Insights, klikněte na tlačítko **Průzkumníku metrik** > **přidat graf**.
2. Klikněte na tlačítko **upravit** v grafu, která byla přidána.
2. Aktualizujte podrobnosti grafu následujícím způsobem:
   * Nastavit **typ grafu** k **mřížky**.
   * Nastavit **agregace** k **průměrná**.
   * Nastavit **Seskupit podle** k **NodeId**.
   * V **metriky**, vyberte **vlastní** > **Blob stahování v sekundách**.
   * Upravit zobrazení **paletu barev** do svého výběru. 

![Doba stahování objektů blob na uzel](./media/monitor-application-insights/blobdownloadtime.png)


## <a name="monitor-compute-nodes-continuously"></a>Nepřetržitě monitorování výpočetních uzlů

Jste si všimli, že všechny metriky, včetně čítače výkonu se pouze protokolují, když jsou spuštěné úlohy. Toto chování je užitečné, protože se omezuje množství dat, který zaznamenává Application Insights. Ale existují případy Pokud byste chtěli vždy monitorovat výpočetní uzly. Například se může být spuštěn práce na pozadí, které není naplánován prostřednictvím služby Batch. V takovém případě nastavte monitorování procesu spuštění po dobu trvání výpočetního uzlu. 

Jeden způsob, jak dosáhnout toto chování je spawn proces, který načte knihovně Application Insights a běží na pozadí. V příkladu spouštěcí úkol načte binárních souborů v počítači a udržuje proces, který běží bez omezení. Konfigurace služby Application Insights konfiguračního souboru pro tento proces pro vydávání další data, která vás zajímá, jako je například čítače výkonu.

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
    virtualMachineSize: "small",
    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));
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
> Pokud chcete zvýšit spravovatelnosti vašeho řešení, můžete svázat sestavení v [balíčku aplikace](./batch-application-packages.md). Potom k nasazení balíčku aplikace automaticky na fondech, přidáte odkaz na aplikaci balíčku pro konfiguraci fondu.
>

## <a name="throttle-and-sample-data"></a>Omezení a ukázková data 

Z důvodu rozsáhlé povaha Azure Batch aplikace běžící v produkčním prostředí můžete chtít omezit množství dat shromažďovaných pomocí Application Insights ke správě náklady. V tématu [vzorkování ve službě Application Insights](../application-insights/app-insights-sampling.md) pro některé mechanismy dosáhnout.


## <a name="next-steps"></a>Další postup
* Další informace o [Application Insights](../application-insights/app-insights-overview.md).

* Pro podporu Application Insights v jiných jazycích, podívejte se na [jazyky, platformy a integrace dokumentaci](../application-insights/app-insights-platforms.md).


