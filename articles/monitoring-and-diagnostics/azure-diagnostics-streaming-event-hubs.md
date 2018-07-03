---
title: Stream dat diagnostiky Azure do služby Event Hubs
description: Konfigurace diagnostiky Azure pomocí služby Event Hubs komplexní pokyny pro běžné scénáře včetně.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 07/13/2017
ms.author: robb
ms.component: diagnostic-extension
ms.openlocfilehash: c87a4acb8ca333af73643a38ae1338c9c8769d13
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2018
ms.locfileid: "37341226"
---
# <a name="streaming-azure-diagnostics-data-in-the-hot-path-by-using-event-hubs"></a>Streamování dat diagnostiky Azure do horké cesty s využitím služby Event Hubs
Diagnostika Azure nabízí flexibilní možnosti, jak shromažďovat metriky a protokoly z cloudové služby virtuálních počítačů (VM) a přenést výsledky do služby Azure Storage. Od března 2016 (SDK 2.9) časový rámec, můžete odeslání diagnostiky do vlastní zdroje dat a přenos dat kritickou cestu během několika sekund pomocí [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/).

Podporované datové typy patří:

* Události Trasování událostí pro Windows
* Čítače výkonu
* Protokoly událostí Windows
* Protokoly aplikací
* Protokolů infrastruktury Azure Diagnostics

Tento článek ukazuje, jak konfigurovat diagnostiku Azure pomocí služby Event Hubs od začátku do konce. Poskytujeme také pokyny pro následující běžné scénáře:

* Jak přizpůsobit protokoly a metriky, které odesílání do služby Event Hubs
* Postup změny konfigurace v jednotlivých prostředích
* Jak zobrazit data datového proudu Event Hubs
* Řešení potíží s připojení  

## <a name="prerequisites"></a>Požadavky
Event Hubs receieving data z Azure Diagnostics se podporuje v cloudových služeb, virtuálních počítačů, Škálovací sady virtuálních počítačů a spuštění v Azure SDK 2.9 a odpovídající nástroje Azure pro sadu Visual Studio Service Fabric.

* Rozšíření Azure Diagnostics 1.6 ([sady Azure SDK pro .NET 2.9 nebo novější](https://azure.microsoft.com/downloads/) cílí to ve výchozím nastavení)
* [Visual Studio 2013 nebo novější](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
* Existující konfigurace diagnostiky Azure v aplikaci s využitím *.wadcfgx* soubor a jeden z následujících metod:
  * Visual Studio: [konfigurace diagnostiky pro Azure Cloud Services a Virtual Machines](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md)
  * Prostředí Windows PowerShell: [povolení diagnostiky v Azure Cloud Services pomocí Powershellu](../cloud-services/cloud-services-diagnostics-powershell.md)
* Zřízeno na článek, obor názvů služby Event Hubs [Začínáme se službou Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

## <a name="connect-azure-diagnostics-to-event-hubs-sink"></a>Připojení diagnostiky Azure do služby Event Hubs jímky
Ve výchozím nastavení Azure Diagnostics vždy posílá protokoly a metriky pro účet služby Azure Storage. Aplikace může také posílat do služby Event Hubs tak, že přidáte nový **jímky** části **PublicConfig** / **WadCfg** elementu *. wadcfgx* souboru. V sadě Visual Studio *.wadcfgx* soubor je uložený v následujícím umístění: **projekt cloudové služby** > **role** > **() RoleName)** > **diagnostics.wadcfgx** souboru.

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

V tomto příkladu URL centra událostí je nastavena na plně kvalifikovaný obor názvů centra událostí: obor názvů Event Hubs + "/" + název centra událostí.  

Adresa URL se zobrazí v Centru událostí [webu Azure portal](http://go.microsoft.com/fwlink/?LinkID=213885) na řídicím panelu služby Event Hubs.  

**Jímky** název lze nastavit na libovolný platný řetězec, tak dlouho, dokud se stejnou hodnotu konzistentně používá v konfiguračním souboru.

> [!NOTE]
> Můžou existovat další jímky, jako například *applicationInsights* nakonfigurované v této části. Diagnostika Azure umožňuje jeden nebo více jímky definováno, pokud každý jímky je také deklarován **PrivateConfig** oddílu.  
>
>

Jímce služby Event Hubs musí také být deklarovány a definovány v **PrivateConfig** část *.wadcfgx* konfiguračního souboru.

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

`SharedAccessKeyName` Hodnota musí odpovídat klíče sdíleného přístupového podpisu (SAS) a zásadu, která je definována v **Event Hubs** oboru názvů. Přejděte na řídicí panel služby Event Hubs [webu Azure portal](https://portal.azure.com), klikněte na tlačítko **konfigurovat** kartu a nastavení s názvem zásady (například "SendRule"), který má *odeslat* oprávnění. **StorageAccount** je také deklarován **PrivateConfig**. Není nutné měnit hodnoty sem, pokud používají. V tomto příkladu jsme ponechte hodnoty prázdná, což je znak, že bude podřízený prostředek nastavte hodnoty. Například *ServiceConfiguration.Cloud.cscfg* prostředí konfiguračního souboru nastaví prostředí odpovídající názvy a klíče.  

> [!WARNING]
> Klíč SAS centra událostí je uložený ve formátu prostého textu v *.wadcfgx* souboru. Často tento klíč se změnami do správy zdrojového kódu nebo je k dispozici jako prostředek v serveru sestavení, měli byste ho chránit podle potřeby. Doporučujeme používat klíč SAS se tady *odeslat pouze* oprávnění tak, aby uživatel se zlými úmysly nelze zapisovat do centra událostí, ale naslouchání na ni nebo ho spravovat.
>
>

## <a name="configure-azure-diagnostics-to-send-logs-and-metrics-to-event-hubs"></a>Konfigurace diagnostiky Azure do služby Event Hubs odesílat protokoly a metriky
Jak je popsáno výše, všechny výchozí a vlastní diagnostická data, to znamená, metriky a protokoly, je automaticky odeslána do služby Azure Storage v nakonfigurovaných intervalech. Pomocí služby Event Hubs a jakékoli další jímky můžete zadat libovolný kořenové nebo listový uzel v hierarchii k odeslání do centra událostí. To zahrnuje události trasování událostí pro Windows, čítače výkonu, protokoly událostí Windows a protokoly aplikací.   

Je důležité vzít v úvahu, kolik datových bodů by ve skutečnosti se měly převést do služby Event Hubs. Vývojáři obvykle přenos horká cesta data s nízkou latencí, která musí být spotřebovány a interpretovat rychle. Příklady jsou systémy, které monitorují výstrahy nebo pravidla automatického škálování. Vývojáři můžou také nakonfigurujte úložiště alternativní analýzy nebo hledání úložiště – například Azure Stream Analytics, Elasticsearch, vlastní monitorovacího systému nebo oblíbený systém monitorování od ostatních.

Tady jsou některé ukázkové konfigurace.

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

Ve výše uvedeném příkladu se použije jímka pro nadřazenou **čítače výkonu** uzel v hierarchii, což znamená, že všechny podřízené **čítače výkonu** se odešlou do služby Event Hubs.  

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

V předchozím příkladu jímka platí pro pouze tři čítače: **požadavky ve frontě**, **požadavků odmítnutých**, a **% času procesoru**.  

Následující příklad ukazuje, jak může vývojář omezit množství odesílaných dat bude důležité metriky, které se používají pro tuto službu stavu.  

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

Jímka v tomto příkladu se použije pro protokoly a filtrovat jenom pro úroveň trasování chyba.

## <a name="deploy-and-update-a-cloud-services-application-and-diagnostics-config"></a>Nasazení a aktualizace konfigurace aplikace a Diagnostika cloudových služeb
Visual Studio nabízí nejsnadnější způsob, jak nasadit aplikace a služby Event Hubs jímky konfigurace. Chcete-li zobrazit a upravit soubor, otevřete *.wadcfgx* souboru v sadě Visual Studio, upravte ho a uložte ho. Cesta je **projekt cloudové služby** > **role** > **(RoleName)** > **diagnostics.wadcfgx**.  

V tomto okamžiku všechny nasazení a nasazení aktualizací akcí ve Visual Studio, Visual Studio Team System a všechny příkazy nebo skripty, které jsou založené na MSBuild a použití **/t: publikování** zahrnout cíl *.wadcfgx* v procesu vytváření balíčků. Kromě toho nasazení a aktualizace nasazení souboru do Azure pomocí příslušné rozšíření agenta diagnostiky Azure na virtuálních počítačích.

Po nasazení aplikace a konfigurace diagnostiky Azure, okamžitě uvidíte aktivity na řídicím panelu Centra událostí. To znamená, že jste připraveni přejít k zobrazení dat horká cesta v naslouchací proces klienta nebo analýzy nástroje podle vašeho výběru.  

Na následujícím obrázku zobrazuje řídicí panel služby Event Hubs v dobrém stavu odesílání diagnostická data do centra událostí spuštění nějakou dobu po 23: 00. Právě to byla aplikace nasazená s aktualizovanou *.wadcfgx* byl správně nakonfigurován soubor a jímky.

![][0]  

> [!NOTE]
> Když provádíte aktualizace do konfiguračního souboru Azure Diagnostics (.wadcfgx), se doporučuje nahrání aktualizace do celé aplikace, jakož i konfigurace s použitím sady Visual Studio publikování nebo skriptu prostředí Windows PowerShell.  
>
>

## <a name="view-hot-path-data"></a>Data zobrazení horké cesty
Jak je popsáno výše, existuje mnoho případy použití pro příjem a zpracování dat služby Event Hubs.

Jeden jednoduchý přístupem je vytvoření konzolové aplikace malý test naslouchat službě eventhub a tisknout do výstupního datového proudu. Můžete provádět následující kód, který je podrobněji vysvětleno [Začínáme se službou Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)), v konzolové aplikaci.  

Všimněte si, že musí obsahovat konzolovou aplikaci [balíček NuGet hostitel procesoru událostí](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/).  

Nezapomeňte nahradit hodnoty v lomených závorkách v **hlavní** funkce s hodnotami pro vaše prostředky.   

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
            string eventHubConnectionString = "Endpoint= <your connection string>”
            string eventHubName = "<Event hub name>";
            string storageAccountName = "<Storage account name>";
            string storageAccountKey = "<Storage account key>”;
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

## <a name="troubleshoot-event-hubs-sinks"></a>Řešení potíží s Event Hubs jímky
* Centrum událostí se nezobrazují příchozí nebo odchozí událost činnost dle očekávání.

    Zkontrolujte, že je vaše Centrum událostí úspěšně zřízený. Všechny informace o připojení v **PrivateConfig** část *.wadcfgx* musí odpovídat hodnotám prostředku, jak je vidět na portálu. Ujistěte se, že máte SAS zásady definované ("SendRule" v příkladu) na portálu a který *odeslat* je povolení uděleno.  
* Po aktualizaci centra událostí už nebude zobrazovat aktivity události příchozí nebo odchozí.

    Nejprve se ujistěte, že události rozbočovače a konfigurační informace, které je správný, jak bylo popsáno dříve. Někdy **PrivateConfig** obnovit v nasazení aktualizace. Doporučené opravy je, aby všechny změny *.wadcfgx* v projektu a nabízených oznámení aplikace dokončena a aktualizace. Pokud to není možné, ujistěte se, že aktualizace diagnostiky nabízených oznámení dokončení **PrivateConfig** , který obsahuje klíč SAS.  
* Byl proveden o návrhy a centra událostí není funkční.

    Zkuste se podívat v tabulce Azure Storage, který obsahuje chyby a protokolování Azure Diagnostics, vlastní: **WADDiagnosticInfrastructureLogsTable**. Jednou z možností je použít nástroj, jako například [Průzkumníka služby Azure Storage](http://www.storageexplorer.com) pro připojení k tomuto účtu úložiště, zobrazit tuto tabulku a přidat dotaz pro časové razítko za posledních 24 hodin. Nástroj můžete exportovat soubor CSV a otevřít ji v aplikaci jako je třeba aplikace Microsoft Excel. Excelu umožňuje snadno hledat řetězce volání karty, jako například **EventHubs**, abyste si zobrazili, jaká chyba se nahlásí.  

## <a name="next-steps"></a>Další postup
• [Další informace o službě Event Hubs](https://azure.microsoft.com/services/event-hubs/)

## <a name="appendix-complete-azure-diagnostics-configuration-file-wadcfgx-example"></a>Dodatek: Kompletní příklad souboru (.wadcfgx) konfigurace diagnostiky Azure
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

Doplňková *ServiceConfiguration.Cloud.cscfg* pro tento příklad bude vypadat jako následující.

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

Je ekvivalentní nastavení JSON pro virtuální počítače takto:

Nastavení veřejné:
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

Chráněná nastavení pro:
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

## <a name="next-steps"></a>Další postup
Další informace o službě Event Hubs najdete na následujících odkazech:

* [Přehled služby Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md)
* [Vytvoření centra událostí](../event-hubs/event-hubs-create.md)
* [Nejčastější dotazy k Event Hubs](../event-hubs/event-hubs-faq.md)

<!-- Images. -->
[0]: ../event-hubs/media/event-hubs-streaming-azure-diags-data/dashboard.png
