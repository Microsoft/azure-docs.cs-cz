---
title: Monitorování dávky pomocí přehledů aplikací Azure | Dokumenty společnosti Microsoft
description: Zjistěte, jak instrumentovat aplikaci Azure Batch .NET pomocí knihovny Azure Application Insights.
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.devlang: .NET
ms.topic: article
ms.workload: na
ms.date: 04/05/2018
ms.author: labrenne
ms.openlocfilehash: b1f4fb0207d4f659861dbd3fdfd1b2d502409935
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022456"
---
# <a name="monitor-and-debug-an-azure-batch-net-application-with-application-insights"></a>Monitorování a ladění aplikace Azure Batch .NET pomocí application insights

[Application Insights](../azure-monitor/app/app-insights-overview.md) poskytuje vývojářům elegantní a výkonný způsob monitorování a ladění aplikací nasazených ve službách Azure. Pomocí application insights můžete monitorovat čítače výkonu a výjimky a také instrumentovat váš kód pomocí vlastních metrik a trasování. Integrace Application Insights s vaší aplikací Azure Batch vám umožní získat podrobný přehled o chování a prozkoumat problémy téměř v reálném čase.

Tento článek ukazuje, jak přidat a nakonfigurovat knihovnu Application Insights do vašeho řešení Azure Batch .NET a instrumentovat kód aplikace. Také ukazuje způsoby, jak sledovat vaši aplikaci prostřednictvím portálu Azure a vytvářet vlastní řídicí panely. Podpora Application Insights v jiných jazycích najdete v dokumentaci k [jazykům, platformám a integraci](../azure-monitor/app/platforms.md).

Ukázkové řešení Jazyka C# s kódem, který je přiložen k tomuto článku, je k dispozici na [GitHubu](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ApplicationInsights). Tento příklad přidá kód instrumentace Application Insights do příkladu [TopNWords.](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords) Pokud tento příklad neznáte, zkuste nejprve vytvářet a spouštět TopNWords. To vám pomůže pochopit základní dávkový pracovní postup zpracování sady vstupních objektů BLOB paralelně na více výpočetních uzlech. 

> [!TIP]
> Jako alternativu nakonfigurujte dávkové řešení tak, aby zobrazovalo data Application Insights, jako jsou čítače výkonu virtuálních her v Průzkumníku dávek. [Batch Explorer](https://github.com/Azure/BatchExplorer) je bezplatný, bohatý, samostatný klientský nástroj, který pomáhá vytvářet, ladit a monitorovat aplikace Azure Batch. Můžete si stáhnout [instalační balíček](https://azure.github.io/BatchExplorer/) pro Mac, Linux nebo Windows. V rychlém provedení nástrojů pro povolení dat Application Insights v Průzkumníkovi dávek najdete v povolte úložiště [přehledů dávek.](https://github.com/Azure/batch-insights) 
>

## <a name="prerequisites"></a>Požadavky
* [Visual Studio 2017 nebo novější](https://www.visualstudio.com/vs)

* [Dávkový účet a propojený účet úložiště](batch-account-create-portal.md)

* [Prostředek Application Insights](../azure-monitor/app/create-new-resource.md )
  
   * Pomocí portálu Azure vytvořte *prostředek*Application Insights . Vyberte typ *obecné* **aplikace**.

   * Zkopírujte [klíč instrumentace](../azure-monitor/app/create-new-resource.md #copy-the-instrumentation-key) z portálu. Je vyžadováno dále v tomto článku.
  
  > [!NOTE]
  > Za data uložená v Application Insights vám mohou být [účtovány](https://azure.microsoft.com/pricing/details/application-insights/) poplatky. To zahrnuje diagnostická a monitorovací data popsané v tomto článku.
  > 

## <a name="add-application-insights-to-your-project"></a>Přidání přehledů aplikací do projektu

Balíček **Microsoft.ApplicationInsights.WindowsServer** NuGet a jeho závislosti jsou vyžadovány pro váš projekt. Přidejte nebo obnovte je do projektu aplikace. Chcete-li nainstalovat balíček, použijte `Install-Package` příkaz nebo NuGet Správce balíčků.

```powershell
Install-Package Microsoft.ApplicationInsights.WindowsServer
```
Reference Application Insights z aplikace .NET pomocí oboru názvů **Microsoft.ApplicationInsights.**

## <a name="instrument-your-code"></a>Nástroj váš kód

Chcete-li instrumentovat váš kód, vaše řešení potřebuje vytvořit Application Insights [TelemetryClient](/dotnet/api/microsoft.applicationinsights.telemetryclient). V tomto příkladu telemetrieClient načte svou konfiguraci ze souboru [ApplicationInsights.config.](../azure-monitor/app/configuration-with-applicationinsights-config.md) Nezapomeňte aktualizovat soubor ApplicationInsights.config v následujících projektech pomocí klíče instrumentace Application Insights: Microsoft.Azure.Batch.Samples.TelemetryStartTask a TopNWordsSample.

```xml
<InstrumentationKey>YOUR-IKEY-GOES-HERE</InstrumentationKey>
```
Přidejte také klíč instrumentace do TopNWords.cs souboru.

Příklad v TopNWords.cs používá následující [volání instrumentace](../azure-monitor/app/api-custom-events-metrics.md) z rozhraní API Application Insights:
* `TrackMetric()`- Sleduje, jak dlouho v průměru výpočetní uzel trvá ke stažení požadovaného textového souboru.
* `TrackTrace()`- Přidá ladění volání do kódu.
* `TrackEvent()`- Sleduje zajímavé události zachytit.

Tento příklad záměrně vynechává zpracování výjimek. Místo toho Application Insights automaticky hlásí neošetřené výjimky, což výrazně zlepšuje ladění. 

Následující úryvek ukazuje, jak používat tyto metody.

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

### <a name="azure-batch-telemetry-initializer-helper"></a>Pomocník pro iniciačátor telemetrie Azure Batch
Při vytváření sestav telemetrie pro daný server a instanci, Application Insights používá role virtuálního počítače Azure a název virtuálního počítače pro výchozí hodnoty. V kontextu Azure Batch příklad ukazuje, jak použít název fondu a název výpočetního uzlu místo. Použijte [inicializátor telemetrie](../azure-monitor/app/api-filtering-sampling.md#add-properties) k přepsání výchozích hodnot. 

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

Chcete-li povolit inicializátor telemetrie, soubor ApplicationInsights.config v projektu TopNWordsSample obsahuje následující:

```xml
<TelemetryInitializers>
    <Add Type="Microsoft.Azure.Batch.Samples.TelemetryInitializer.AzureBatchNodeTelemetryInitializer, Microsoft.Azure.Batch.Samples.TelemetryInitializer"/>
</TelemetryInitializers>
``` 

## <a name="update-the-job-and-tasks-to-include-application-insights-binaries"></a>Aktualizace úlohy a úkolů tak, aby zahrnovaly binární soubory Application Insights

Aby se přehledy aplikací správně spouštěly na výpočetních uzlech, ujistěte se, že jsou správně umístěny binární soubory. Přidejte požadované binární soubory do kolekce souborů prostředků úlohy, aby se stáhly v době, kdy se úloha spustí. Následující úryvky jsou podobné kódu v Job.cs.

Nejprve vytvořte statický seznam souborů Application Insights, které chcete nahrát.

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

Dále vytvořte pracovní soubory, které jsou používány úlohou.
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

Metoda `FileToStage` je pomocná funkce v ukázce kódu, která umožňuje snadno nahrát soubor z místního disku do objektu blob úložiště Azure. Každý soubor je později stažen do výpočetního uzlu a odkazován úlohou.

Nakonec přidejte úkoly do úlohy a zahrňte potřebné binární soubory Application Insights.
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

Teď, když jste nakonfigurovali úlohu a úkoly tak, aby používaly Application Insights, spusťte ukázkovou úlohu ve vašem fondu. Přejděte na portál Azure a otevřete prostředek Application Insights, který jste zřídit. Po zřízení fondu, měli byste začít vidět data toku a získávání protokolování. Zbytek tohoto článku se dotýká pouze několika funkcí Application Insights, ale neváhejte a prozkoumejte celou sadu funkcí.

### <a name="view-live-stream-data"></a>Zobrazení dat živého datového proudu

Chcete-li zobrazit protokoly trasování v prostředku Application Insights, klepněte na tlačítko **Živý přenos**. Následující snímek obrazovky ukazuje, jak zobrazit živá data pocházející z výpočetních uzlů ve fondu, například využití procesoru na výpočetní uzel.

![Data výpočetních uzlů živého datového proudu](./media/monitor-application-insights/applicationinsightslivestream.png)

### <a name="view-trace-logs"></a>Zobrazit protokoly trasování

Chcete-li zobrazit protokoly trasování v prostředku Application Insights, klepněte na tlačítko **Hledat**. Toto zobrazení zobrazuje seznam diagnostických dat zachycených application insights včetně trasování, událostí a výjimek. 

Následující snímek obrazovky ukazuje, jak je zaznamenáno jedno trasování pro úlohu a později dotazován o účely ladění.

![Obrázek trasovacích protokolů](./media/monitor-application-insights/tracelogsfortask.png)

### <a name="view-unhandled-exceptions"></a>Zobrazit neošetřené výjimky

Následující snímky obrazovky ukazují, jak Application Insights protokoluje výjimky vyvolány z vaší aplikace. V tomto případě během několika sekund aplikace vyvolání výjimky můžete přejít k podrobnostem konkrétní výjimku a diagnostikovat problém.

![Neošetřené výjimky](./media/monitor-application-insights/exception.png)

### <a name="measure-blob-download-time"></a>Měření doby stahování objektu blob

Vlastní metriky jsou také cenným nástrojem na portálu. Můžete například zobrazit průměrnou dobu, po kterou každý výpočetní uzel stáhl požadovaný textový soubor, který zpracovával.

Vytvoření ukázkového grafu:
1. V prostředku Application Insights klikněte na **Metrics Explorer** > **Add chart**.
2. V grafu, který byl přidán, klikněte na **Upravit.**
2. Podrobnosti grafu aktualizujte takto:
   * Nastavte **typ grafu** na **mřížku**.
   * Nastavte **agregaci** na **průměr**.
   * Nastavit **skupinu podle** **na NodeId**.
   * V **metrikách**vyberte **vlastní** > **stahování objektů Blob během několika sekund**.
   * Upravte **paletu barev** zobrazení podle svého výběru. 

![Doba stahování objektů Blob na uzel](./media/monitor-application-insights/blobdownloadtime.png)


## <a name="monitor-compute-nodes-continuously"></a>Průběžně monitorujte výpočetní uzly

Možná jste si všimli, že všechny metriky, včetně čítačů výkonu, jsou zaznamenány pouze v případě, že jsou spuštěny úlohy. Toto chování je užitečné, protože omezuje množství dat, které protokoly Application Insights. Existují však případy, kdy byste vždy chtěli sledovat výpočetní uzly. Mohou například spouštět práce na pozadí, které nejsou naplánovány prostřednictvím služby Batch. V takovém případě nastavte proces monitorování tak, aby běžel po dobu životnosti výpočetního uzlu. 

Jedním ze způsobů, jak dosáhnout tohoto chování je vytvořit proces, který načte knihovnu Application Insights a spustí na pozadí. V příkladu počáteční úloha načte binární soubory v počítači a udržuje proces spuštěný po neomezenou dobu. Nakonfigurujte konfigurační soubor Application Insights pro tento proces tak, aby vydával další data, která vás zajímají, například čítače výkonu.

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
> Chcete-li zvýšit možnosti správy vašeho řešení, můžete svázat sestavení v [balíčku aplikace](./batch-application-packages.md). Potom k automatickému nasazení balíčku aplikace do fondů přidejte odkaz na balíček aplikace na konfiguraci fondu.
>

## <a name="throttle-and-sample-data"></a>Údaje o škrticí klapky a ukázková data 

Vzhledem k rozsáhlé povaze aplikací Azure Batch spuštěných v produkčním prostředí můžete chtít omezit množství dat shromážděných application insights pro správu nákladů. Viz [vzorkování v Application Insights](../azure-monitor/app/sampling.md) pro některé mechanismy k dosažení tohoto cíle.


## <a name="next-steps"></a>Další kroky
* Další informace o [application insights](../azure-monitor/app/app-insights-overview.md).

* Podpora Application Insights v jiných jazycích najdete v dokumentaci k [jazykům, platformám a integraci](../azure-monitor/app/platforms.md).


