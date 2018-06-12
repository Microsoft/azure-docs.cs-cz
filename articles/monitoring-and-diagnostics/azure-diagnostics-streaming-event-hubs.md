---
title: Datový proud Azure diagnostická data do centra událostí
description: Konfigurace Azure Diagnostics službou Event Hubs začátku do konce, včetně pokyny pro běžné scénáře.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 07/13/2017
ms.author: robb
ms.component: diagnostic-extension
ms.openlocfilehash: 98e788d87b0ce03eece35868391aadd5233217b0
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35267709"
---
# <a name="streaming-azure-diagnostics-data-in-the-hot-path-by-using-event-hubs"></a>Streamování dat diagnostiky Azure v aktivní trase pomocí služby Event Hubs
Azure Diagnostics poskytuje flexibilní způsoby, jak shromažďovat metriky a protokoly z cloudové služby virtuálních počítačů (VM) a přenos výsledků do služby Azure Storage. Spouštění v časovém intervalu. března 2016 (SDK 2.9), můžete odeslání diagnostiky do vlastní zdroje dat a přenos dat aktivní trase v sekundách pomocí [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/).

Podporované datové typy patří:

* Události Trasování událostí pro Windows
* Čítače výkonu
* Protokoly událostí Windows
* Protokoly aplikací
* Protokolů infrastruktury Azure Diagnostics

Tento článek ukazuje, jak nakonfigurovat Azure Diagnostics službou Event Hubs a provést tak kompletní. Příručka je taky pokyny pro následující běžné scénáře:

* Postup přizpůsobení protokoly a metriky, které získat odeslaných do centra událostí
* Postup změny konfigurace v každé prostředí
* Postup zobrazení dat datového proudu Event Hubs
* Řešení potíží s připojení  

## <a name="prerequisites"></a>Požadavky
Event Hubs receieving dat z Azure Diagnostics je podporována v cloudové služby, virtuální počítače, sady škálování virtuálního počítače a Service Fabric od Azure SDK 2.9 a odpovídající nástroje Azure pro sadu Visual Studio.

* Rozšíření diagnostiky Azure 1.6 ([Azure SDK pro .NET 2.9 nebo novější](https://azure.microsoft.com/downloads/) cílem to ve výchozím nastavení)
* [Visual Studio 2013 nebo novější](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
* Stávající konfigurace Azure Diagnostics v aplikaci pomocí *.wadcfgx* souboru a jeden z následujících metod:
  * Visual Studio: [konfigurace diagnostiky pro cloudové služby Azure a virtuální počítače](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md)
  * Prostředí Windows PowerShell: [zapněte diagnostiku ve službě Azure Cloud Services pomocí prostředí PowerShell](../cloud-services/cloud-services-diagnostics-powershell.md)
* Obor názvů centra událostí zřízený za v článku [Začínáme se službou Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

## <a name="connect-azure-diagnostics-to-event-hubs-sink"></a>Připojení Azure Diagnostics k podřízený Event Hubs
Ve výchozím nastavení Azure Diagnostics vždycky odešle protokoly a metriky k účtu úložiště Azure. Aplikace může přidáním nové také odesílat data do centra událostí **jímky** oddílu pod **PublicConfig** / **WadCfg** element *. wadcfgx* souboru. V sadě Visual Studio *.wadcfgx* soubor je uložen v následující cestě: **projekt cloudové služby** > **role** > **() RoleName)** > **diagnostics.wadcfgx** souboru.

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

V tomto příkladu nastavení adresy URL centra událostí do centra událostí, plně kvalifikovaný oboru názvů: obor názvů služby Event Hubs + "/" + název centra událostí.  

Adresa URL se zobrazí v centra událostí [portál Azure](http://go.microsoft.com/fwlink/?LinkID=213885) na řídicím panelu služby Event Hubs.  

**Jímky** název můžete nastavit na libovolný platný řetězec, tak dlouho, dokud se stejnou hodnotou se používá konzistentně napříč do konfiguračního souboru.

> [!NOTE]
> Mohou existovat další jímky, jako například *applicationInsights* nakonfigurované v této části. Azure Diagnostics umožňuje jeden nebo více jímky k být definována, pokud každý podřízený také deklarovaného v souboru **PrivateConfig** části.  
>
>

Event Hubs podřízený musí být také deklarovaný a definované v **PrivateConfig** části *.wadcfgx* konfiguračního souboru.

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

`SharedAccessKeyName` Hodnota musí odpovídat klíč sdíleného přístupového podpisu (SAS) a zásad, která byla definována v **Event Hubs** oboru názvů. Přejděte do centra událostí řídicího panelu [portál Azure](https://portal.azure.com), klikněte na tlačítko **konfigurace** kartě a nastavte s názvem zásady (například "SendRule"), který má *odeslat* oprávnění. **StorageAccount** také deklarovaného v **PrivateConfig**. Není nutné ke změně hodnot v tomto poli, pokud pracují. V tomto příkladu jsme ponechte hodnoty prázdná, což je přihlašovací, že podřízené asset bude nastavit hodnoty. Například *ServiceConfiguration.Cloud.cscfg* prostředí konfigurační soubor nastaví příslušné prostředí názvů a klíče.  

> [!WARNING]
> Event Hubs SAS klíč, který je uložený ve formátu prostého textu v *.wadcfgx* souboru. Často tento klíč se změnami do správy zdrojového kódu, nebo není k dispozici jako prostředek na vašem serveru sestavení, takže byste měli chránit podle potřeby. Doporučujeme používat klíč SAS se zde *odesílat pouze* oprávnění tak, aby uživatel se zlými úmysly nelze zapisovat do centra událostí, ale naslouchání k němu nebo k její správě.
>
>

## <a name="configure-azure-diagnostics-to-send-logs-and-metrics-to-event-hubs"></a>Konfigurace Azure Diagnostics odeslat protokoly a metriky do centra událostí
Jak je popsáno dříve, všechny výchozí a vlastní diagnostická data, to znamená, metriky a protokoly, je automaticky odeslán do služby Azure Storage v nakonfigurovaných intervalech. Event Hubs a všechny další jímka můžete zadat libovolný uzel kořenovou nebo listu v hierarchii k odeslání do centra událostí. To zahrnuje události trasování událostí, čítače výkonu, protokoly událostí systému Windows a protokolů aplikace.   

Je důležité vzít v úvahu, kolik datových bodů by ve skutečnosti se měly převést do centra událostí. Vývojáři obvykle přenášet data za provozu cesty s nízkou latencí, která musí využívat a rychle interpretovat. Systémy, které monitorují výstrahy nebo pravidel automatického škálování jsou příklady. Vývojář může také nakonfigurovat úložišti alternativní analýzy nebo hledání úložiště – například Azure Stream Analytics, Elasticsearch, vlastní monitorování systému nebo oblíbených monitorování systému od jiných uživatelů.

Následují některé příklad konfigurace.

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

Ve výše uvedeném příkladu jímky použije s nadřazeným **čítače výkonu** uzlu v hierarchii, což znamená, všechny podřízené **čítače výkonu** odešle do centra událostí.  

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

V předchozím příkladu jímky použije jenom tři čítače: **požadavky ve frontě**, **požadavky zamítnuty**, a **% času procesoru**.  

Následující příklad ukazuje, jak může vývojář omezit množství odeslaných dat jako kritické metriky, které se používají pro tuto službu stavu.  

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

V tomto příkladu jímky se použije pro protokoly a vyfiltrovaná jenom na úrovni trasování chyby.

## <a name="deploy-and-update-a-cloud-services-application-and-diagnostics-config"></a>Nasazení a aktualizace konfigurace aplikace a Diagnostika cloudové služby
Visual Studio poskytuje nejjednodušší cesta k nasazení aplikace a služby Event Hubs podřízený konfigurace. Chcete-li zobrazit a upravit soubor, otevřete *.wadcfgx* souborů v sadě Visual Studio, upravovat a uložte ho. Cesta je **projekt cloudové služby** > **role** > **(RoleName)** > **diagnostics.wadcfgx**.  

V tomto okamžiku všechny nasazení a nasazení aktualizací akcí v sadě Visual Studio, Visual Studio Team System a všechny příkazy nebo skripty, které jsou založeny na MSBuild a použít **/t: publikování** zahrnout cíl *.wadcfgx* v procesu vytváření balíčků. Kromě toho nasazení a aktualizace nasazení souboru do Azure pomocí příslušné rozšíření agenta Azure Diagnostics na virtuální počítače.

Po nasazení aplikace a konfigurace Azure Diagnostics, zobrazí se okamžitě aktivity na řídicím panelu Centra událostí. To znamená, že jste připravení přejít k zobrazení dat za běhu cestu v nástroji klienta nebo analysis naslouchací proces podle svého výběru.  

Na následujícím obrázku Event Hubs řídicího panelu ukazuje pořádku odesílání diagnostická data do centra událostí spouštění zopakovat po 23: 00. Pokud je aplikace nasazená s aktualizované *.wadcfgx* soubor a jímky byla nakonfigurována správně.

![][0]  

> [!NOTE]
> Pokud provedete aktualizace konfiguračního souboru Azure Diagnostics (.wadcfgx), se doporučuje push aktualizace bude celá aplikace a také konfigurace pomocí sady Visual Studio publikování nebo skript prostředí Windows PowerShell.  
>
>

## <a name="view-hot-path-data"></a>Data za provozu cesty zobrazení
Jak je popsáno dříve, existuje mnoho případy použití pro příjem a zpracování dat služby Event Hubs.

Jeden jednoduchý přístup je vytvoření konzolové aplikace testu malých k naslouchání do centra událostí a tisk do výstupního datového proudu. Můžete umístit následující kód, který je vysvětlené podrobněji v [Začínáme se službou Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)), v konzolové aplikaci.  

Všimněte si, že musí obsahovat konzolové aplikace [balíček NuGet hostitele procesor událostí](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/).  

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

## <a name="troubleshoot-event-hubs-sinks"></a>Řešení potíží s jímky služby Event Hubs
* Centra událostí nezobrazuje aktivity příchozích nebo odchozích události podle očekávání.

    Zkontrolujte, že je úspěšně zřízený Centrum událostí. Všechny informace o připojení ve **PrivateConfig** části *.wadcfgx* musí shodovat s hodnotami prostředku, jak je vidět na portálu. Ujistěte se, že máte SAS zásady definované ("SendRule" v příkladu) v portálu a které *odeslat* je povoleno.  
* Po aktualizaci už centra událostí zobrazuje aktivity příchozích nebo odchozích události.

    Nejprve se ujistěte, zda je správný, jak je popsáno dříve rozbočovače a konfigurační informace o události. Někdy **PrivateConfig** v aktualizaci nasazení se resetuje. Doporučené opravy je to, aby všechny změny *.wadcfgx* v projektu a pak nabízené aktualizaci dokončení aplikace. Pokud to není možné, ujistěte se, že aktualizace diagnostiky nabízených oznámení úplná **PrivateConfig** který obsahuje klíč SAS.  
* Byl proveden o návrhy a centra událostí, ale stále nefunguje.

    Podívejte se do v Azure Storage tabulka, která obsahuje chyby a protokolování Azure Diagnostics samotné: **WADDiagnosticInfrastructureLogsTable**. Jednou z možností je použít nástroj, jako například [Azure Storage Explorer](http://www.storageexplorer.com) se pokud chcete připojit k tomuto účtu úložiště, zobrazit tuto tabulku a přidat dotaz pro časové razítko za posledních 24 hodin. Nástroj můžete exportovat soubor .csv a otevře ji v aplikaci, jako je například aplikace Microsoft Excel. Excel usnadňuje hledat řetězce volací karty, například **EventHubs**, pokud chcete zobrazit, jaké se chybová zpráva.  

## <a name="next-steps"></a>Další postup
• [Další informace o službě Event Hubs](https://azure.microsoft.com/services/event-hubs/)

## <a name="appendix-complete-azure-diagnostics-configuration-file-wadcfgx-example"></a>Dodatek: Dokončení příkladu Azure Diagnostics konfigurační soubor (.wadcfgx)
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

Doplňkové *ServiceConfiguration.Cloud.cscfg* pro tento příklad vypadá jako následující.

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

Ekvivalentní Json na základě nastavení pro virtuální počítače je následující:
```JSON
"settings": {
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


"protectedSettings": {
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
