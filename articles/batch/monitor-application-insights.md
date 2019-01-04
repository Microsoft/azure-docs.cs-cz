---
title: Monitorování služby Batch pomocí Azure Application Insights | Dokumentace Microsoftu
description: Zjistěte, jak instrumentovat aplikaci Azure Batch .NET pomocí knihovny Azure Application Insights.
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
ms.openlocfilehash: 4a77f73f4d87f7def7c8a841c7a59aed04b9bee2
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54020043"
---
# <a name="monitor-and-debug-an-azure-batch-net-application-with-application-insights"></a>Monitorování a ladění aplikace Azure Batch .NET pomocí nástroje Application Insights

[Application Insights](../application-insights/app-insights-overview.md) poskytuje elegantní a účinný způsob pro vývojáře k monitorování a ladění aplikace nasazené do služeb Azure. Pomocí Application Insights do monitorování čítače výkonu a výjimek, jakož i nástrojem váš kód vlastní metriky a trasování. Integrace Application Insights s vaší aplikací Azure Batch umožňuje získat podrobný přehled o chování a prozkoumat problémy v téměř reálném čase.

Tento článek popisuje postup přidání a konfigurace knihovny služby Application Insights do vašeho řešení Azure Batch .NET a nástroje, kód vaší aplikace. Také ukazuje, jak můžete monitorovat své aplikace prostřednictvím portálu Azure portal a vytvářet vlastní řídicí panely. Application Insights podpory v jiných jazycích, podívejte se na [jazyky, platformy a integrace dokumentaci](../azure-monitor/app/platforms.md).

Ukázky C# řešení s kódem pro tento článek je k dispozici na [Githubu](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ApplicationInsights). V tomto příkladu přidá kód instrumentace Application Insights [TopNWords](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords) příklad. Pokud nejste obeznámeni s ukázkou, zkuste sestavováním a spouštěním TopNWords nejprve. To vám pomůže porozumět základní pracovní postup služby Batch zpracování sadu vstupní objekty BLOB paralelně na několika výpočetních uzlech. 

> [!TIP]
> Jako alternativu konfigurace vašeho řešení Batch, chcete-li zobrazit data Application Insights, jako jsou čítače výkonu virtuálních počítačů v Průzkumníkovi služby Batch. [Batch Explorer](https://github.com/Azure/BatchExplorer) je zdarma, bohatými funkcemi, samostatný klientský nástroj umožňující vytvářet, ladit a monitorovat aplikace Azure Batch. Můžete si stáhnout [instalační balíček](https://azure.github.io/BatchExplorer/) pro Mac, Linux nebo Windows. Najdete v článku [batch insights úložiště](https://github.com/Azure/batch-insights) pro rychlé kroky, aby data Application Insights v Průzkumníkovi služby Batch. 
>

## <a name="prerequisites"></a>Požadavky
* [Visual Studio 2017](https://www.visualstudio.com/vs)

* [Účet batch a propojený účet úložiště](batch-account-create-portal.md)

* [Prostředek služby Application Insights](../application-insights/app-insights-create-new-resource.md)
  
   * Pomocí webu Azure portal k vytvoření Application Insights *prostředků*. Vyberte *Obecné* **typ aplikace**.

   * Kopírovat [Instrumentační klíč](../application-insights/app-insights-create-new-resource.md#copy-the-instrumentation-key) z portálu. Je vyžadováno později v tomto článku.
  
  > [!NOTE]
  > Může být [účtovat](https://azure.microsoft.com/pricing/details/application-insights/) pro data uložená ve službě Application Insights. To zahrnuje diagnostiky a monitorování dat popsané v tomto článku.
  > 

## <a name="add-application-insights-to-your-project"></a>Přidejte ke svému projektu Application Insights.

**Microsoft.ApplicationInsights.WindowsServer** balíčku NuGet a jeho závislosti jsou požadovány pro váš projekt. Přidání nebo obnovit je do projektu aplikace. Chcete-li nainstalovat balíček, použijte `Install-Package` příkaz nebo Správce balíčků NuGet.

```powershell
Install-Package Microsoft.ApplicationInsights.WindowsServer
```
Odkazovat pomocí Application Insights z aplikace .NET **Microsoft.ApplicationInsights** oboru názvů.

## <a name="instrument-your-code"></a>Instrumentace kódu

Instrumentace kódu, musí vaše řešení k vytvoření Application Insights [TelemetryClient](/dotnet/api/microsoft.applicationinsights.telemetryclient). V tomto příkladu TelemetryClient načte konfiguraci z [soubor ApplicationInsights.config](../azure-monitor/app/configuration-with-applicationinsights-config.md) souboru. Nezapomeňte aktualizovat soubor ApplicationInsights.config v následujících projektech s Instrumentační klíč Application Insights: Microsoft.Azure.Batch.Samples.TelemetryStartTask a TopNWordsSample.

```xml
<InstrumentationKey>YOUR-IKEY-GOES-HERE</InstrumentationKey>
```
Také přidáte Instrumentační klíč v souboru TopNWords.cs.

V příkladu v TopNWords.cs používá následující [instrumentace volání](../azure-monitor/app/api-custom-events-metrics.md) z rozhraní API Application Insights:
* `TrackMetric()` -Sleduje, jak dlouho a v průměru výpočetního uzlu přijímá ke stažení souboru požadovaný text.
* `TrackTrace()` -Přidá ladění volání do kódu.
* `TrackEvent()` -Sleduje zajímavé události k zachycení.

Tento příklad je záměrně vynechány zpracování výjimek. Místo toho Application Insights automaticky oznámí neošetřené výjimky, která výrazně zlepšuje funkce ladění. 

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

### <a name="azure-batch-telemetry-initializer-helper"></a>Pomocné rutiny inicializátor telemetrie Azure Batch
Při hlášení telemetrie pro daný server a instanci Application Insights využívá název Role virtuálních počítačů Azure a virtuálních počítačů pro výchozí hodnoty. V rámci služby Azure Batch příklad ukazuje způsob použití názvu fondu a místo toho compute název uzlu. Použití [inicializátor telemetrie](../azure-monitor/app/api-filtering-sampling.md#add-properties) přepsat výchozí hodnoty. 

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

Pokud chcete povolit inicializátor telemetrie, soubor ApplicationInsights.config v projektu TopNWordsSample zahrnuje následující položky:

```xml
<TelemetryInitializers>
    <Add Type="Microsoft.Azure.Batch.Samples.TelemetryInitializer.AzureBatchNodeTelemetryInitializer, Microsoft.Azure.Batch.Samples.TelemetryInitializer"/>
</TelemetryInitializers>
``` 

## <a name="update-the-job-and-tasks-to-include-application-insights-binaries"></a>Aktualizace úlohy a úkolů, které chcete zahrnout binární soubory služby Application Insights

V pořadí pro službu Application Insights mohla správně spouštět v výpočetních uzlů Ujistěte se, že jsou správně umístěny binární soubory. Přidejte požadované binární soubory do vašich úloh kolekci souborů prostředků tak, aby získat stáhnou v době, kdy úloha spustí. Následující fragmenty kódu se podobají kódu v Job.cs.

Nejprve vytvořte statický seznam souborů Application Insights k nahrání.

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

Dále vytvořte pracovní soubory, které jsou používány úkolu.
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

`FileToStage` Metoda je pomocná funkce v ukázkovém kódu, který vám umožní snadno nahrát soubor z místního disku do objektu blob Azure Storage. Každý soubor je později stažen do výpočetního uzlu a odkazuje na položku úkolu.

Nakonec přidejte úkoly do úlohy a zahrnout binární soubory nezbytné Application Insights.
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

## <a name="view-data-in-the-azure-portal"></a>Zobrazení dat na webu Azure Portal

Teď, když jste nakonfigurovali úlohy a úkolů, jak pomocí Application Insights, spusťte příklad úlohu ve vašem fondu. Přejděte na web Azure Portal a otevřete prostředek Application Insights, kterou jste zřídili. Po zřízení fondu, měli byste začít toku dat a získání protokolována. Zbývající část tohoto článku týká pouze několik funkcí Application Insights, ale Nebojte se prozkoumat úplná sada funkcí.

### <a name="view-live-stream-data"></a>Zobrazení živého streamování dat

Pokud chcete zobrazit protokoly trasování ve svém prostředku aplikace Insights, klikněte na tlačítko **Live Stream**. Následující snímek obrazovky ukazuje, jak zobrazit živá data přicházející z výpočetních uzlů ve fondu, třeba využití CPU na výpočetním uzlu.

![Živé streamování výpočetní uzel dat](./media/monitor-application-insights/applicationinsightslivestream.png)

### <a name="view-trace-logs"></a>Zobrazit protokoly trasování

Pokud chcete zobrazit protokoly trasování ve svém prostředku aplikace Insights, klikněte na tlačítko **hledání**. Toto zobrazení uvádí seznam diagnostických dat zachycených přes Application Insights včetně trasování, události a výjimky. 

Následující snímek obrazovky ukazuje, jak jedno trasování pro úlohu je zaznamenána a později dotazovat pro účely ladění.

![Obrázek protokoly trasování](./media/monitor-application-insights/tracelogsfortask.png)

### <a name="view-unhandled-exceptions"></a>Zobrazit nezpracované výjimky

Na následujících snímcích obrazovky ukazuje, jak Application Insights protokoly výjimky vyvolané z vaší aplikace. V takovém případě během několika sekund od aplikace, která vyvolává výjimku, můžete přejít k podrobnostem konkrétní výjimce a problém diagnostikovat.

![Neošetřené výjimky](./media/monitor-application-insights/exception.png)

### <a name="measure-blob-download-time"></a>Doba stahování objektu blob měr

Vlastní metriky jsou také cenným nástrojem na portálu. Můžete například zobrazit Průměrná doba trvání jednotlivých výpočetních uzlech se stáhnout soubor požadovaný text, který byl zpracování.

Pokud chcete vytvořit ukázkový graf:
1. V prostředku Application Insights, klikněte na tlačítko **Průzkumníka metrik** > **přidat graf**.
2. Klikněte na tlačítko **upravit** v grafu, která byla přidána.
2. Aktualizujte podrobnosti o grafu následujícím způsobem:
   * Nastavte **typ grafu** k **mřížky**.
   * Nastavte **agregace** k **průměrné**.
   * Nastavte **Seskupit podle** k **NodeId**.
   * V **metriky**vyberte **vlastní** > **stažení objektů Blob v řádu sekund**.
   * Upravit zobrazení **paletu barev** zadaným hodnotám. 

![Doba stahování objektu BLOB na jeden uzel](./media/monitor-application-insights/blobdownloadtime.png)


## <a name="monitor-compute-nodes-continuously"></a>Průběžné monitorování výpočetních uzlů

Mohli jste si všimnout, že všechny metriky, včetně čítačů výkonu, jsou pouze protokolu zapíše, když jsou spuštěné úkoly. Toto chování je užitečné, protože omezuje množství dat, které protokoly Application Insights. Existují však případy když chcete vždy monitorovat výpočetní uzly. Například běží práce na pozadí, který není naplánované prostřednictvím služby Batch. V takovém případě nastavte monitorování procesu ke spuštění po celou dobu životnosti výpočetním uzlu. 

Jedním ze způsobů dosažení tohoto chování je vytvořit podřízený proces, který načte Knihovna Application Insights a běží na pozadí. V příkladu se na nich spouštěcí úkol načte binární soubory na počítači a ponechá proces spuštěný po neomezenou dobu. Nakonfigurujte Application Insights konfiguračního souboru pro tento proces a vygenerovat další data, která vás zajímá, jako jsou čítače výkonu.

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
> Ke zvýšení možnosti správy vašeho řešení, můžete svázat sestavení v [balíčku aplikace](./batch-application-packages.md). Pak automaticky nasadit balíček aplikace do vašich fondů, přidejte odkaz na aplikaci balíčku pro konfiguraci fondu.
>

## <a name="throttle-and-sample-data"></a>Omezení a ukázková data 

Z důvodu ve velkém měřítku povaha služby Azure Batch aplikace běžící v produkčním prostředí můžete chtít omezit objem dat shromážděných službou Application Insights a spravujte náklady. Zobrazit [vzorkování ve službě Application Insights](../azure-monitor/app/sampling.md) pro některé mechanismy pro můžete toho dosáhnout.


## <a name="next-steps"></a>Další postup
* Další informace o [Application Insights](../application-insights/app-insights-overview.md).

* Application Insights podpory v jiných jazycích, podívejte se na [jazyky, platformy a integrace dokumentaci](../azure-monitor/app/platforms.md).


