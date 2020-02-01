---
title: Streamování dat Azure Diagnostics do Event Hubs
description: Konfigurace Azure Diagnostics s využitím kompletních Event Hubs, včetně pokynů pro běžné scénáře.
ms.service: azure-monitor
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/13/2017
ms.openlocfilehash: 111fab880887b54b2415d433bda2368c951381bd
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2020
ms.locfileid: "76901217"
---
# <a name="streaming-azure-diagnostics-data-in-the-hot-path-by-using-event-hubs"></a>Streamování Azure Diagnostics dat v Hot Path pomocí Event Hubs
Azure Diagnostics poskytuje flexibilní způsoby shromažďování metrik a protokolů z virtuálních počítačů cloudových služeb a výsledků přenosu do Azure Storage. Počínaje časovým rámcem, který začíná 2016. března (SDK 2,9), můžete do vlastních zdrojů dat odeslat diagnostiku a přenést data za provozu za pár sekund pomocí [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/).

Mezi podporované datové typy patří:

* Události Trasování událostí pro Windows
* Čítače výkonu
* Protokoly událostí systému Windows, včetně protokolů aplikace v protokolu událostí systému Windows
* Protokolů infrastruktury Azure Diagnostics

V tomto článku se dozvíte, jak nakonfigurovat Azure Diagnostics pomocí Event Hubs od konce do konce. Doprovodné materiály jsou také k dispozici v následujících běžných scénářích:

* Jak přizpůsobit protokoly a metriky, které se odesílají do Event Hubs
* Změna konfigurací v jednotlivých prostředích
* Postup zobrazení dat Event Hubs streamu
* Řešení potíží s připojením  

## <a name="prerequisites"></a>Požadavky
Event Hubs Příjem dat z Azure Diagnostics podporuje v Cloud Services, virtuálních počítačích, Virtual Machine Scale Sets a Service Fabric od Azure SDK 2,9 a odpovídajících nástrojů Azure pro Visual Studio.

* Azure Diagnostics rozšíření 1,6 (ve výchozím nastavení se[Azure SDK pro .net 2,9 nebo novější](https://azure.microsoft.com/downloads/) zaměřuje)
* [Visual Studio 2013 nebo novější](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
* Existující konfigurace Azure Diagnostics v aplikaci pomocí souboru *. wadcfgx* a jedné z následujících metod:
  * Visual Studio: [Konfigurace diagnostiky pro Azure Cloud Services a Virtual Machines](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines)
  * Windows PowerShell: [Povolení diagnostiky ve službě Azure Cloud Services pomocí prostředí PowerShell](../../cloud-services/cloud-services-diagnostics-powershell.md)
* Event Hubs obor názvů zřízený podle článku [Začínáme s Event Hubs](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

## <a name="connect-azure-diagnostics-to-event-hubs-sink"></a>Připojení Azure Diagnostics k Event Hubs jímky
Ve výchozím nastavení Azure Diagnostics vždy odesílá protokoly a metriky na účet Azure Storage. Aplikace může také odesílat data do Event Hubs přidáním **nového / oddílu** **jímky** do prvku **WadCfg** v souboru *. wadcfgx* . V aplikaci Visual Studio je soubor *. wadcfgx* uložený v následující cestě: **projekt cloudové služby** > **role** >  **(roleName)**  > **Diagnostics. wadcfgx** .

```xml
<SinksConfig>
  <Sink name="HotPath">
    <EventHub Url="https://diags-mycompany-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" />
  </Sink>
</SinksConfig>
```
```JSON
"SinksConfig": {
    "Sink": [
        {
            "name": "HotPath",
            "EventHub": {
                "Url": "https://diags-mycompany-ns.servicebus.windows.net/diageventhub",
                "SharedAccessKeyName": "SendRule"
            }
        }
    ]
}
```

V tomto příkladu je adresa URL centra událostí nastavená na plně kvalifikovaný obor názvů centra událostí: Event Hubs obor názvů + "/" + název centra událostí.  

Adresa URL centra událostí se zobrazí v [Azure Portal](https://go.microsoft.com/fwlink/?LinkID=213885) na řídicím panelu Event Hubs.  

Název **jímky** lze nastavit na libovolný platný řetězec, pokud je stejná hodnota použita konzistentně v rámci konfiguračního souboru.

> [!NOTE]
> V této části můžou být nakonfigurované další jímky, jako je *applicationInsights* . Azure Diagnostics umožňuje definovat jednu nebo více umyvadel, pokud je každá jímka také deklarována v oddílu **PrivateConfig** .  
>
>

Jímka Event Hubs musí být také deklarována a definována v oddílu **PrivateConfig** konfiguračního souboru *. wadcfgx* .

```XML
<PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <StorageAccount name="{account name}" key="{account key}" endpoint="{optional storage endpoint}" />
  <EventHub Url="https://diags-mycompany-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="{base64 encoded key}" />
</PrivateConfig>
```
```JSON
{
    "storageAccountName": "{account name}",
    "storageAccountKey": "{account key}",
    "storageAccountEndPoint": "{optional storage endpoint}",
    "EventHub": {
        "Url": "https://diags-mycompany-ns.servicebus.windows.net/diageventhub",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "{base64 encoded key}"
    }
}
```

Hodnota `SharedAccessKeyName` musí odpovídat klíči sdíleného přístupového podpisu (SAS) a zásadě, která byla definována v oboru názvů **Event Hubs** . V [Azure Portal](https://portal.azure.com)přejděte na řídicí panel Event Hubs, klikněte na kartu **Konfigurovat** a nastavte pojmenovanou zásadu (například "SendRule"), která má oprávnění *Odeslat* . **StorageAccount** je také deklarován v **PrivateConfig**. Pokud fungují, nemusíte tady měnit hodnoty. V tomto příkladu ponecháme hodnoty prázdné, což je znaménko, že se pro podřízený Asset nastavují hodnoty. Například konfigurační soubor prostředí *ServiceConfiguration. Cloud. cscfg* nastaví názvy a klíče odpovídající prostředí.  

> [!WARNING]
> Event Hubs klíč SAS je uložený v souboru *. wadcfgx* do prostého textu. Tento klíč je často vrácen se změnami do správy zdrojového kódu nebo je k dispozici jako prostředek na serveru sestavení, takže byste ho měli chránit podle potřeby. Doporučujeme použít klíč SAS v tomto umístění s oprávněním *Odeslat jenom* , aby uživatel se zlými úmysly mohl zapisovat do centra událostí, ale neposlouchal ho ani nespravuje.
>
>

## <a name="configure-azure-diagnostics-to-send-logs-and-metrics-to-event-hubs"></a>Konfigurace Azure Diagnostics pro odesílání protokolů a metrik do Event Hubs
Jak už jsme uvedli, všechna výchozí a vlastní diagnostická data, která jsou metriky a protokoly, se automaticky odesílají do Azure Storage v nakonfigurovaných intervalech. Pomocí Event Hubs a jakýchkoliv dalších umyvadel můžete v hierarchii určit kterýkoli kořenový uzel nebo uzel na úrovni listu, který se odešle do centra událostí. To zahrnuje události ETW, čítače výkonu, protokoly událostí systému Windows a protokoly aplikací.   

Je důležité vzít v úvahu, kolik datových bodů by se mělo ve skutečnosti přenést do Event Hubs. Vývojáři obvykle přenášejí data s nízkou latencí v Hot-Path, která musí být spotřebována a interpretována rychle. Příklady jsou systémy, které sledují výstrahy nebo pravidla automatického škálování. Vývojář může také nakonfigurovat alternativní úložiště analýzy nebo úložiště pro hledání, například Azure Stream Analytics, Elasticsearch, vlastní systém monitorování nebo oblíbený monitorovací systém od ostatních.

Následuje několik ukázkových konfigurací.

```xml
<PerformanceCounters scheduledTransferPeriod="PT1M" sinks="HotPath">
  <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
</PerformanceCounters>
```
```JSON
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "sinks": "HotPath",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Memory\\Available MBytes",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
            "sampleRate": "PT3M"
        }
    ]
}
```

V předchozím příkladu se jímka aplikuje na nadřazený uzel **čítače výkonu** v hierarchii, což znamená, že všechny podřízené **čítače výkonu** se odešlou do Event Hubs.  

```xml
<PerformanceCounters scheduledTransferPeriod="PT1M">
  <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" sinks="HotPath" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" sinks="HotPath"/>
  <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" sinks="HotPath"/>
</PerformanceCounters>
```
```JSON
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT3M",
            "sinks": "HotPath"
        },
        {
            "counterSpecifier": "\\Memory\\Available MBytes",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\ASP.NET\\Requests Rejected",
            "sampleRate": "PT3M",
            "sinks": "HotPath"
        },
        {
            "counterSpecifier": "\\ASP.NET\\Requests Queued",
            "sampleRate": "PT3M",
            "sinks": "HotPath"
        }
    ]
}
```

V předchozím příkladu se jímka používá jenom pro tři čítače: **žádosti ve frontě**, **Zamítnuté požadavky**a **% času procesoru**.  

Následující příklad ukazuje, jak může vývojář omezit množství odeslaných dat na kritické metriky, které se používají pro stav této služby.  

```XML
<Logs scheduledTransferPeriod="PT1M" sinks="HotPath" scheduledTransferLogLevelFilter="Error" />
```
```JSON
"Logs": {
    "scheduledTransferPeriod": "PT1M",
    "scheduledTransferLogLevelFilter": "Error",
    "sinks": "HotPath"
}
```

V tomto příkladu se jímka aplikuje na protokoly a je filtrovaná jenom na trasování úrovně chyby.

## <a name="deploy-and-update-a-cloud-services-application-and-diagnostics-config"></a>Nasazení a aktualizace Cloud Services aplikace a konfigurace diagnostiky
Visual Studio poskytuje nejjednodušší cestu k nasazení aplikace a konfigurace jímky Event Hubs. Pokud chcete soubor zobrazit a upravit, otevřete soubor *. wadcfgx* v aplikaci Visual Studio, upravte ho a uložte. Cesta je **projekt cloudové služby** > **role** >  **(roleName)**  > **Diagnostics. wadcfgx**.  

V tomto okamžiku všechny akce aktualizace nasazení a nasazení v aplikaci Visual Studio, Visual Studio Team System a všechny příkazy nebo skripty, které jsou založené na MSBuild a používají `/t:publish` cíl, zahrnují soubor *. wadcfgx* v procesu balení. Nasazení a aktualizace navíc nasadí soubor do Azure pomocí vhodného rozšíření agenta Azure Diagnostics na vašich virtuálních počítačích.

Po nasazení aplikace a konfigurace Azure Diagnostics se na řídicím panelu centra událostí okamžitě zobrazí aktivita. To znamená, že jste připraveni přejít k zobrazení dat pro cestu k Hot-PATH v nástroji pro naslouchání nebo pro analýzu podle vašeho výběru.  

Na následujícím obrázku Event Hubs řídicí panel zobrazovat v pořádku odesílání diagnostických dat do centra událostí, a to od času po 11./odp. To je v případě, že aplikace byla nasazena s aktualizovaným souborem *. wadcfgx* a jímka byla správně nakonfigurována.

![][0]  

> [!NOTE]
> Když provedete aktualizace konfiguračního souboru Azure Diagnostics (. wadcfgx), doporučujeme, abyste tyto aktualizace navložili do celé aplikace i do konfigurace pomocí publikování sady Visual Studio nebo skriptu prostředí Windows PowerShell.  
>
>

## <a name="view-hot-path-data"></a>Zobrazení dat za horkou cestu
Jak bylo popsáno dříve, existuje mnoho případů použití pro příjem a zpracování dat Event Hubs. Jedním jednoduchým přístupem je vytvořit malou testovací konzolovou aplikaci, která bude naslouchat centru událostí a tisknout výstupní datový proud. 

#### <a name="net-sdk-latest-500-or-latertablatest"></a>[Nejnovější rozhraní .NET SDK (5.0.0 nebo novější)](#tab/latest)
Můžete umístit následující kód, který je podrobněji vysvětlen v tématu [Začínáme s Event Hubs](../../event-hubs/get-started-dotnet-standard-send-v2.md)), v konzolové aplikaci.

```csharp
using System;
using System.Text;
using System.Threading.Tasks;
using Azure.Storage.Blobs;
using Azure.Messaging.EventHubs;
using Azure.Messaging.EventHubs.Processor;
namespace Receiver1204
{
    class Program
    {
        private static readonly string ehubNamespaceConnectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";
        private static readonly string eventHubName = "EVENT HUB NAME";
        private static readonly string blobStorageConnectionString = "AZURE STORAGE CONNECTION STRING";
        private static readonly string blobContainerName = "BLOB CONTAINER NAME";

        static async Task Main()
        {
            // Read from the default consumer group: $Default
            string consumerGroup = EventHubConsumerClient.DefaultConsumerGroupName;

            // Create a blob container client that the event processor will use 
            BlobContainerClient storageClient = new BlobContainerClient(blobStorageConnectionString, blobContainerName);

            // Create an event processor client to process events in the event hub
            EventProcessorClientOptions options = new EventProcessorClientOptions { }
            EventProcessorClient processor = new EventProcessorClient(storageClient, consumerGroup, ehubNamespaceConnectionString, eventHubName);

            // Register handlers for processing events and handling errors
            processor.ProcessEventAsync += ProcessEventHandler;
            processor.ProcessErrorAsync += ProcessErrorHandler;

            // Start the processing
            await processor.StartProcessingAsync();

            // Wait for 10 seconds for the events to be processed
            await Task.Delay(TimeSpan.FromSeconds(10));

            // Stop the processing
            await processor.StopProcessingAsync();
        }

        static Task ProcessEventHandler(ProcessEventArgs eventArgs)
        {
            Console.WriteLine("\tRecevied event: {0}", Encoding.UTF8.GetString(eventArgs.Data.Body.ToArray()));
            return Task.CompletedTask;
        }

        static Task ProcessErrorHandler(ProcessErrorEventArgs eventArgs)
        {
            Console.WriteLine($"\tPartition '{ eventArgs.PartitionId}': an unhandled exception was encountered. This was not expected to happen.");
            Console.WriteLine(eventArgs.Exception.Message);
            return Task.CompletedTask;
        }
    }
}
```

#### <a name="net-sdk-legacy-410-or-earliertablegacy"></a>[.NET SDK starší verze (4.1.0 nebo starší)](#tab/legacy)

Můžete umístit následující kód, který je podrobněji vysvětlen v tématu [Začínáme s Event Hubs](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)), v konzolové aplikaci. Všimněte si, že Konzolová aplikace musí zahrnovat [balíček NuGet pro procesor událostí](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/). Nezapomeňte nahradit hodnoty v lomených závorkách **Hlavní** funkce hodnotami vašich prostředků.   

```csharp
//Console application code for EventHub test client
using System;
using System.Collections.Generic;
using System.Diagnostics;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using Microsoft.ServiceBus.Messaging;

namespace EventHubListener
{
    class SimpleEventProcessor : IEventProcessor
    {
        Stopwatch checkpointStopWatch;

        async Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
        {
            Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
            if (reason == CloseReason.Shutdown)
            {
                await context.CheckpointAsync();
            }
        }

        Task IEventProcessor.OpenAsync(PartitionContext context)
        {
            Console.WriteLine("SimpleEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);
            this.checkpointStopWatch = new Stopwatch();
            this.checkpointStopWatch.Start();
            return Task.FromResult<object>(null);
        }

        async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
        {
            foreach (EventData eventData in messages)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                    Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
                        context.Lease.PartitionId, data));

                foreach (var x in eventData.Properties)
                {
                    Console.WriteLine(string.Format("    {0} = {1}", x.Key, x.Value));
                }
            }

            //Call checkpoint every 5 minutes, so that worker can resume processing from 5 minutes back if it restarts.
            if (this.checkpointStopWatch.Elapsed > TimeSpan.FromMinutes(5))
            {
                await context.CheckpointAsync();
                this.checkpointStopWatch.Restart();
            }
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            string eventHubConnectionString = "Endpoint= <your connection string>";
            string eventHubName = "<Event hub name>";
            string storageAccountName = "<Storage account name>";
            string storageAccountKey = "<Storage account key>";
            string storageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", storageAccountName, storageAccountKey);

            string eventProcessorHostName = Guid.NewGuid().ToString();
            EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, eventHubName, EventHubConsumerGroup.DefaultGroupName, eventHubConnectionString, storageConnectionString);
            Console.WriteLine("Registering EventProcessor...");
            var options = new EventProcessorOptions();
            options.ExceptionReceived += (sender, e) => { Console.WriteLine(e.Exception); };
            eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>(options).Wait();

            Console.WriteLine("Receiving. Press enter key to stop worker.");
            Console.ReadLine();
            eventProcessorHost.UnregisterEventProcessorAsync().Wait();
        }
    }
}
```
---

## <a name="troubleshoot-event-hubs-sinks"></a>Řešení potíží s Event Hubsmi jímkami
* Centrum událostí nezobrazuje příchozí nebo odchozí aktivitu události podle očekávání.

    Ověřte, že se vaše centrum událostí úspěšně zřídilo. Všechny informace o připojení v oddílu **PrivateConfig** v souboru *. wadcfgx* se musí shodovat s hodnotami prostředku, jak je vidět na portálu. Ujistěte se, že máte definované zásady SAS (v příkladu "SendRule") na portálu a že je udělené oprávnění *Odeslat* .  
* Po aktualizaci přestane centrum událostí zobrazovat příchozí nebo odchozí aktivitu události.

    Nejdřív se ujistěte, že jsou informace o centru událostí a konfiguraci správné, jak je vysvětleno výše. V některých případech se **PrivateConfig** resetuje v aktualizaci nasazení. Doporučenou opravou je udělat v projektu všechny změny v *. wadcfgx* a pak odeslat úplnou aktualizaci aplikace. Pokud to není možné, ujistěte se, že aktualizace diagnostiky nahraje úplný **PrivateConfig** , který obsahuje klíč SAS.  
* Vyzkoušel jsem návrhy a centrum událostí stále nepracuje.

    Zkuste hledat v tabulce Azure Storage, která obsahuje protokoly a chyby pro Azure Diagnostics sebe sama: **WADDiagnosticInfrastructureLogsTable**. Jednou z možností je použít pro připojení k tomuto účtu úložiště nástroj, například [Průzkumník služby Azure Storage](https://www.storageexplorer.com) , zobrazit tuto tabulku a přidat dotaz pro časové razítko za posledních 24 hodin. Pomocí tohoto nástroje můžete exportovat soubor. csv a otevřít ho v aplikaci, jako je Microsoft Excel. Aplikace Excel usnadňuje hledání řetězců volacích karet, jako je například **EventHubs**, k zobrazení informace o tom, jaká chyba je hlášena.  

## <a name="next-steps"></a>Další kroky
• Další [informace o Event Hubs](https://azure.microsoft.com/services/event-hubs/)

## <a name="appendix-complete-azure-diagnostics-configuration-file-wadcfgx-example"></a>Příloha: příklad úplného konfiguračního souboru Azure Diagnostics (. wadcfgx)
```xml
<?xml version="1.0" encoding="utf-8"?>
<DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <WadCfg>
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="applicationInsights.errors">
        <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" />
        <Directories scheduledTransferPeriod="PT1M">
          <IISLogs containerName="wad-iis-logfiles" />
          <FailedRequestLogs containerName="wad-failedrequestlogs" />
        </Directories>
        <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="HotPath">
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />
        </PerformanceCounters>
        <WindowsEventLog scheduledTransferPeriod="PT1M">
          <DataSource name="Application!*" />
        </WindowsEventLog>
        <CrashDumps>
          <CrashDumpConfiguration processName="WaIISHost.exe" />
          <CrashDumpConfiguration processName="WaWorkerHost.exe" />
          <CrashDumpConfiguration processName="w3wp.exe" />
        </CrashDumps>
        <Logs scheduledTransferPeriod="PT1M" sinks="HotPath" scheduledTransferLogLevelFilter="Error" />
      </DiagnosticMonitorConfiguration>
      <SinksConfig>
        <Sink name="HotPath">
          <EventHub Url="https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py" SharedAccessKeyName="SendRule" />
        </Sink>
        <Sink name="applicationInsights">
          <ApplicationInsights />
          <Channels>
            <Channel logLevel="Error" name="errors" />
          </Channels>
        </Sink>
      </SinksConfig>
    </WadCfg>
    <StorageAccount>ACCOUNT_NAME</StorageAccount>
  </PublicConfig>
  <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <StorageAccount name="{account name}" key="{account key}" endpoint="{storage endpoint}" />
    <EventHub Url="https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py" SharedAccessKeyName="SendRule" SharedAccessKey="YOUR_KEY_HERE" />
  </PrivateConfig>
  <IsEnabled>true</IsEnabled>
</DiagnosticsConfiguration>
```

Doplňkový *ServiceConfiguration. Cloud. cscfg* pro tento příklad vypadá podobně jako následující.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceConfiguration serviceName="MyFixItCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="3" osVersion="*" schemaVersion="2015-04.2.6">
  <Role name="MyFixIt.WorkerRole">
    <Instances count="1" />
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="YOUR_CONNECTION_STRING" />
    </ConfigurationSettings>
  </Role>
</ServiceConfiguration>
```

Ekvivalentní nastavení JSON pro virtuální počítače jsou následující:

Veřejné nastavení:
```JSON
{
    "WadCfg": {
        "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": 4096,
            "sinks": "applicationInsights.errors",
            "DiagnosticInfrastructureLogs": {
                "scheduledTransferLogLevelFilter": "Error"
            },
            "Directories": {
                "scheduledTransferPeriod": "PT1M",
                "IISLogs": {
                    "containerName": "wad-iis-logfiles"
                },
                "FailedRequestLogs": {
                    "containerName": "wad-failedrequestlogs"
                }
            },
            "PerformanceCounters": {
                "scheduledTransferPeriod": "PT1M",
                "sinks": "HotPath",
                "PerformanceCounterConfiguration": [
                    {
                        "counterSpecifier": "\\Memory\\Available MBytes",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\Web Service(_Total)\\Bytes Total/Sec",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\ASP.NET Applications(__Total__)\\Requests/Sec",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\ASP.NET Applications(__Total__)\\Errors Total/Sec",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\ASP.NET\\Requests Queued",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\ASP.NET\\Requests Rejected",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                        "sampleRate": "PT3M"
                    }
                ]
            },
            "WindowsEventLog": {
                "scheduledTransferPeriod": "PT1M",
                "DataSource": [
                    {
                        "name": "Application!*"
                    }
                ]
            },
            "Logs": {
                "scheduledTransferPeriod": "PT1M",
                "scheduledTransferLogLevelFilter": "Error",
                "sinks": "HotPath"
            }
        },
        "SinksConfig": {
            "Sink": [
                {
                    "name": "HotPath",
                    "EventHub": {
                        "Url": "https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py",
                        "SharedAccessKeyName": "SendRule"
                    }
                },
                {
                    "name": "applicationInsights",
                    "ApplicationInsights": "",
                    "Channels": {
                        "Channel": [
                            {
                                "logLevel": "Error",
                                "name": "errors"
                            }
                        ]
                    }
                }
            ]
        }
    },
    "StorageAccount": "{account name}"
}

```

Chráněná nastavení:
```JSON
{
    "storageAccountName": "{account name}",
    "storageAccountKey": "{account key}",
    "storageAccountEndPoint": "{storage endpoint}",
    "EventHub": {
        "Url": "https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "YOUR_KEY_HERE"
    }
}
```

## <a name="next-steps"></a>Další kroky
Další informace o službě Event Hubs najdete na následujících odkazech:

* [Přehled služby Event Hubs](../../event-hubs/event-hubs-about.md)
* [Vytvoření centra událostí](../../event-hubs/event-hubs-create.md)
* [Nejčastější dotazy k Event Hubs](../../event-hubs/event-hubs-faq.md)

<!-- Images. -->
[0]: ../../event-hubs/media/event-hubs-streaming-azure-diags-data/dashboard.png

