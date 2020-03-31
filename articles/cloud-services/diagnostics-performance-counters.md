---
title: Shromažďujte čítače výkonu ve cloudových službách Azure | Dokumenty společnosti Microsoft
description: Zjistěte, jak zjistit, používat a vytvářet čítače výkonu v cloudových službách pomocí Azure Diagnostics a Application Insights.
services: cloud-services
documentationcenter: .net
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 02/02/2018
ms.author: tagore
ms.openlocfilehash: 3b4028a09f69acd5d7a6579b4610785ed32e227d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77469523"
---
# <a name="collect-performance-counters-for-your-azure-cloud-service"></a>Shromážděte čítače výkonu pro vaši cloudovou službu Azure

Čítače výkonu poskytují způsob, jak můžete sledovat, jak dobře vaše aplikace a hostitele jsou výkon. Systém Windows Server poskytuje mnoho různých čítačů výkonu souvisejících s hardwarem, aplikacemi, operačním systémem a dalšími funkcemi. Shromažďováním a odesíláním čítačů výkonu do Azure můžete tyto informace analyzovat a lépe se rozhodovat. 

## <a name="discover-available-counters"></a>Objevte dostupné čítače

Čítač výkonu se skládá ze dvou částí, název sady (označovaný také jako kategorie) a jeden nebo více čítačů. Pomocí prostředí PowerShell můžete získat seznam dostupných čítačů výkonu:

```powershell
Get-Counter -ListSet * | Select-Object CounterSetName, Paths | Sort-Object CounterSetName

CounterSetName                                  Paths
--------------                                  -----
.NET CLR Data                                   {\.NET CLR Data(*)\SqlClient...
.NET CLR Exceptions                             {\.NET CLR Exceptions(*)\# o...
.NET CLR Interop                                {\.NET CLR Interop(*)\# of C...
.NET CLR Jit                                    {\.NET CLR Jit(*)\# of Metho...
.NET Data Provider for Oracle                   {\.NET Data Provider for Ora...
.NET Data Provider for SqlServer                {\.NET Data Provider for Sql...
.NET Memory Cache 4.0                           {\.NET Memory Cache 4.0(*)\C...
AppV Client Streamed Data Percentage            {\AppV Client Streamed Data ...
ASP.NET                                         {\ASP.NET\Application Restar...
ASP.NET Apps v4.0.30319                         {\ASP.NET Apps v4.0.30319(*)...
ASP.NET State Service                           {\ASP.NET State Service\Stat...
ASP.NET v2.0.50727                              {\ASP.NET v2.0.50727\Applica...
ASP.NET v4.0.30319                              {\ASP.NET v4.0.30319\Applica...
Authorization Manager Applications              {\Authorization Manager Appl...

#... results cut to save space ...
```

Vlastnost `CounterSetName` představuje sadu (nebo kategorie) a je dobrým ukazatelem toho, co čítače výkonu souvisejí. Vlastnost `Paths` představuje kolekci čítačů pro sadu. Můžete také získat `Description` vlastnost pro více informací o sadě čítačů.

Chcete-li získat všechny čítače `CounterSetName` pro sadu, `Paths` použijte hodnotu a rozbalte kolekci. Každá položka cesty je čítač, na který se můžete dotazovat. Chcete-li například získat dostupné čítače související se sadou, `Processor` rozbalte kolekci: `Paths`

```powershell
Get-Counter -ListSet * | Where-Object CounterSetName -eq "Processor" | Select -ExpandProperty Paths

\Processor(*)\% Processor Time
\Processor(*)\% User Time
\Processor(*)\% Privileged Time
\Processor(*)\Interrupts/sec
\Processor(*)\% DPC Time
\Processor(*)\% Interrupt Time
\Processor(*)\DPCs Queued/sec
\Processor(*)\DPC Rate
\Processor(*)\% Idle Time
\Processor(*)\% C1 Time
\Processor(*)\% C2 Time
\Processor(*)\% C3 Time
\Processor(*)\C1 Transitions/sec
\Processor(*)\C2 Transitions/sec
\Processor(*)\C3 Transitions/sec
```

Tyto jednotlivé cesty čítače lze přidat do architektury diagnostiky, kterou používá vaše cloudová služba. Další informace o tom, jak je vytvořena cesta čítače výkonu, naleznete [v tématu Určení cesty čítače](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85)).

## <a name="collect-a-performance-counter"></a>Shromáždění čítače výkonu

Čítač výkonu lze přidat do cloudové služby pro diagnostiku Azure nebo Application Insights.

### <a name="application-insights"></a>Application Insights

Azure Application Insights pro cloudové služby umožňuje určit, jaké čítače výkonu chcete shromažďovat. Po [přidání application insights do projektu](../azure-monitor/app/cloudservices.md#sdk)je do projektu přidán konfigurační soubor s názvem **ApplicationInsights.config.** Tento konfigurační soubor definuje, jaký typ informací Application Insights shromažďuje a odesílá do Azure.

Otevřete soubor **ApplicationInsights.config** a najděte element **ApplicationInsights** > **TelemetryModules.** Každý `<Add>` podřízený prvek definuje typ telemetrie shromažďovat, spolu s jeho konfigurací. Typ modulu telemetrie `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector`čítače výkonu je . Pokud je tento prvek již definován, nepřidávejte jej podruhé. Každý čítač výkonu, který `<Counters>`má být shromažďován, je definován pod názvem uzlu . Zde je příklad, který shromažďuje čítače výkonu jednotky:

```xml
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">

  <TelemetryModules>

    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
      <Counters>
        <Add PerformanceCounter="\LogicalDisk(C:)\Disk Write Bytes/sec" ReportAs="Disk write (C:)" />
        <Add PerformanceCounter="\LogicalDisk(C:)\Disk Read Bytes/sec" ReportAs="Disk read (C:)" />
      </Counters>
    </Add>

  </TelemetryModules>

<!-- ... cut to save space ... -->
```

Každý čítač `<Add>` výkonu `<Counters>`je reprezentován jako prvek v aplikaci . Atribut `PerformanceCounter` definuje, který čítač výkonu shromažďovat. Atribut `ReportAs` je název, který se má zobrazit na portálu Azure pro čítač výkonu. Všechny čítače výkonu, které shromažďujete, jsou zařazeny do kategorie s názvem **Vlastní** na portálu. Na rozdíl od Diagnostiky Azure nelze nastavit interval, kdy se tyto čítače výkonu shromažďují a odesílají do Azure. Pomocí application insights jsou čítače výkonu shromažďovány a odesílány každou minutu. 

Application Insights automaticky shromažďuje následující čítače výkonu:

* \Process(?? APP_WIN32_PROC?) \% Čas procesoru
* \Memory\Available Bytes
* \.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec
* \Process(??APP_WIN32_PROC??)\Private Bytes
* \Process(??APP_WIN32_PROC??)\IO Data Bytes/sec
* \Processor(_Total)\% Processor Time

Další informace najdete [v tématu Čítače výkonu systému v přehledech aplikací](../azure-monitor/app/performance-counters.md) a [přehledy aplikací pro cloudové služby Azure](../azure-monitor/app/cloudservices.md#performance-counters).

### <a name="azure-diagnostics"></a>Azure Diagnostics

> [!IMPORTANT]
> Zatímco všechna tato data jsou agregována do účtu úložiště, portál **neposkytuje** nativní způsob, jak data zmapovat. Důrazně doporučujeme integrovat do vaší aplikace jinou diagnostickou službu, jako je Application Insights.

Rozšíření Diagnostika Azure pro cloudové služby umožňuje určit, jaké čítače výkonu, které chcete shromažďovat. Pokud chcete nastavit diagnostiku Azure, přečtěte si informace [o přehledu monitorování cloudových služeb](cloud-services-how-to-monitor.md#setup-diagnostics-extension).

Čítače výkonu, které chcete shromažďovat, jsou definovány v souboru **diagnostics.wadcfgx.** Otevřete tento soubor (je definován pro roli) v sadě Visual Studio a vyhledejte prvek **DiagnosticsConfiguration** > **PublicConfig** > **WadCfg** > **DiagnosticMonitorConfiguration** > **PerformanceCounters.** Přidejte nový prvek **PerformanceCounterConfiguration** jako podřízený. Tento prvek má dva `counterSpecifier` `sampleRate`atributy: a . Atribut `counterSpecifier` definuje, kterou sadu čítačů výkonu systému (popsané v předchozí části) shromažďovat. Hodnota `sampleRate` označuje, jak často je tato hodnota dotazována. Jako celek jsou všechny čítače výkonu převedeny `PerformanceCounters` do `scheduledTransferPeriod` Azure podle hodnoty atributu nadřazeného prvku.

Další informace o `PerformanceCounters` elementu schématu najdete v tématu [schéma diagnostiky Azure](../azure-monitor/platform/diagnostics-extension-schema-windows.md#performancecounters-element).

Tečka definovaná `sampleRate` atributem používá datový typ doby trvání XML k označení, jak často je čítač výkonu dotazován. V níže uvedeném příkladu `PT3M`je rychlost `[P]eriod[T]ime[3][M]inutes`nastavena na hodnotu , což znamená : každé tři minuty.

Další informace o `sampleRate` tom, jak jsou definovány a `scheduledTransferPeriod` jsou definovány, naleznete v části **Typ dat doby trvání** v kurzu [W3 XML date and Time Date Types.](https://www.w3schools.com/XML/schema_dtypes_date.asp)

```xml
<?xml version="1.0" encoding="utf-8"?>
<DiagnosticsConfiguration  xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <PublicConfig>
    <WadCfg>
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096">

        <!-- ... cut to save space ... -->

        <PerformanceCounters scheduledTransferPeriod="PT1M">
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />

          <!-- This is a new perf counter which will track the C: disk read activity in bytes per second, every minute -->
          <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(C:)\Disk Read Bytes/sec" sampleRate="PT1M" />

        </PerformanceCounters>
      </DiagnosticMonitorConfiguration>
    </WadCfg>
    
    <!-- ... cut to save space ... -->

  </PublicConfig>
</DiagnosticsConfiguration>
```

## <a name="create-a-new-perf-counter"></a>Vytvoření nového čítače perf

Nový čítač výkonu lze vytvořit a použít váš kód. Váš kód, který vytvoří nový čítač výkonu musí být spuštěn se zvýšenými oprávněními, jinak se nezdaří. Váš spouštěcí `OnStart` kód cloudové služby může vytvořit čítač výkonu, který vyžaduje spuštění role ve zvýšeném kontextu. Nebo můžete vytvořit úlohu při spuštění, která běží se zvýšenými oprávněními a vytvoří čítač výkonu. Další informace o úlohách při spuštění naleznete v [tématu Konfigurace a spuštění úloh při spuštění pro cloudovou službu](cloud-services-startup-tasks.md).

Chcete-li nakonfigurovat úlohu pro `<Runtime>` spuštění se zvýšenými oprávněními, přidejte prvek do souboru [.csdef.](cloud-services-model-and-package.md#servicedefinitioncsdef)

```xml
<ServiceDefinition name="CloudServiceLoadTesting" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRoleWithSBQueue1" vmsize="Large">

    <!-- ... cut to save space ... -->

    <Runtime executionContext="elevated">
      
    </Runtime>

    <!-- ... cut to save space ... -->

  </WorkerRole>
</ServiceDefinition>
```

Můžete vytvořit a zaregistrovat nový čítač výkonu s několika řádky kódu. Použijte `System.Diagnostics.PerformanceCounterCategory.Create` přetížení metody, která vytvoří kategorii a čítač. Následující kód nejprve zkontroluje, zda kategorie existuje a pokud chybí, vytvoří kategorii i čítač.

```csharp
using System.Diagnostics;
using Microsoft.WindowsAzure;
using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRoleWithSBQueue1
{
    public class WorkerRole : RoleEntryPoint
    {
        // Perf counter variable representing times service was used.
        private PerformanceCounter counterServiceUsed;

        public override bool OnStart()
        {
            // ... Other startup code here ...

            // Define the category and counter names.
            string perfCounterCatName = "MyService";
            string perfCounterName = "Times Used";

            // Create the counter if needed. Our counter category only has a single counter.
            // Both the category and counter are created in the same method call.
            if (!PerformanceCounterCategory.Exists(perfCounterCatName))
            {
                PerformanceCounterCategory.Create(perfCounterCatName, "Collects information about the cloud service.", 
                                                  PerformanceCounterCategoryType.SingleInstance, 
                                                  perfCounterName, "How many times the cloud service was used.");
            }

            // Get reference to our counter
            counterServiceUsed = new PerformanceCounter(perfCounterCatName, perfCounterName);
            counterServiceUsed.ReadOnly = false;
            
            return base.OnStart();
        }

        // ... cut class code to save space
    }
}
```

Pokud chcete použít čítač, zavolejte metodu `Increment` or. `IncrementBy`

```csharp
// Increase the counter by 1
counterServiceUsed.Increment();
```

Teď, když vaše aplikace používá vlastní čítač, je potřeba nakonfigurovat Diagnostika Azure nebo Application Insights ke sledování čítače.


### <a name="application-insights"></a>Application Insights

Jak již bylo uvedeno, čítače výkonu pro Application Insights jsou definovány v souboru **ApplicationInsights.config.** Otevřete **applicationInsights.config** a najděte element **ApplicationInsights** > **TelemetryModules** > **Add** > **Counters.** Vytvořte `<Add>` podřízený prvek `PerformanceCounter` a nastavte atribut na kategorii a název čítače výkonu, který jste vytvořili v kódu. Nastavte `ReportAs` atribut na popisný název, který chcete zobrazit na portálu.

```xml
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">

  <TelemetryModules>

    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
      <Counters>
        <!-- ... cut other perf counters to save space ... -->

        <!-- This new perf counter matches the [category name]\[counter name] defined in your code -->
        <Add PerformanceCounter="\MyService\Times Used" ReportAs="Service used counter" />
      </Counters>
    </Add>

  </TelemetryModules>

<!-- ... cut to save space ... -->
```

### <a name="azure-diagnostics"></a>Azure Diagnostics

Jak již bylo uvedeno, čítače výkonu, které chcete shromažďovat, jsou definovány v souboru **diagnostics.wadcfgx.** Otevřete tento soubor (je definován pro roli) v sadě Visual Studio a vyhledejte prvek **DiagnosticsConfiguration** > **PublicConfig** > **WadCfg** > **DiagnosticMonitorConfiguration** > **PerformanceCounters.** Přidejte nový prvek **PerformanceCounterConfiguration** jako podřízený. Nastavte `counterSpecifier` atribut na kategorii a název čítače výkonu, který jste vytvořili v kódu. 

```xml
<?xml version="1.0" encoding="utf-8"?>
<DiagnosticsConfiguration  xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <PublicConfig>
    <WadCfg>
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096">

        <!-- ... cut to save space ... -->

        <PerformanceCounters scheduledTransferPeriod="PT1M">
          <!-- ... cut other perf counters to save space ... -->
          
          <!-- This new perf counter matches the [category name]\[counter name] defined in your code -->
          <PerformanceCounterConfiguration counterSpecifier="\MyService\Times Used" sampleRate="PT1M" />

        </PerformanceCounters>
      </DiagnosticMonitorConfiguration>
    </WadCfg>
    
    <!-- ... cut to save space ... -->

  </PublicConfig>
</DiagnosticsConfiguration>
```

## <a name="more-information"></a>Další informace

- [Application Insights pro Azure Cloud Services](../azure-monitor/app/cloudservices.md#performance-counters)
- [Čítače výkonu systému v application insights](../azure-monitor/app/performance-counters.md)
- [Určení cesty čítače](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85))
- [Schéma diagnostiky Azure – čítače výkonu](../azure-monitor/platform/diagnostics-extension-schema-windows.md#performancecounters-element)



