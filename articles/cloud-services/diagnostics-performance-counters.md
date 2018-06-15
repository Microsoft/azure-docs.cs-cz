---
title: Shromažďování na čítače výkonu v cloudových služeb Azure | Microsoft Docs
description: Zjistěte, jak zjistit, použití a vytvoření čítače výkonu v cloudových služeb s Azure Diagnostics a Application Insights.
services: cloud-services
documentationcenter: .net
author: thraka
manager: timlt
editor: ''
ms.assetid: ''
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/18
ms.author: adegeo
ms.openlocfilehash: 3e0af48c172fa912f0ac9e05b7b761dd7eaad795
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2018
ms.locfileid: "29134348"
---
# <a name="collect-performance-counters-for-your-azure-cloud-service"></a>Shromažďování čítačů výkonu pro cloudové služby Azure

Čítače výkonu poskytují způsob, jak můžete sledovat, jak dobře fungují aplikace a hostitele. Windows Server poskytuje mnoho čítačů různých výkonu související s hardwarem, aplikace, operační systém a další. Díky shromažďování a odesílání čítače výkonu do Azure, můžete analyzovat tyto informace pomohou lépe rozhodnout. 

## <a name="discover-available-counters"></a>Zjišťování dostupných čítačů

Čítač výkonu, který se skládá ze dvou částí, název sady (také označované jako kategorie) a jeden nebo více čítačů. Prostředí PowerShell můžete použít k získání seznamu čítačů výkonu k dispozici:

```PowerShell
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

`CounterSetName` Vlastnost představuje sadu (nebo kategorie) a je vhodný indikátor co jsou čítače výkonu související s. `Paths` Vlastnost představuje kolekci čítačů pro sadu. Také může dojít, `Description` vlastnost pro další informace o sadě čítače.

Všechny čítače pro sadu, použijte `CounterSetName` hodnotu a rozbalte `Paths` kolekce. Každá položka cesta je čítače, které můžete zadat dotaz. Například pro získání k dispozici čítače související se `Processor` nastavení, rozbalte `Paths` kolekce:

```PowerShell
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

Tyto jednotlivé čítače, cesty, lze přidat diagnostics Framework používá cloudové služby. Další informace o tom, jak je vytvořený cestu k čítači výkonu najdete v tématu [zadání cesty čítače](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85)).

## <a name="collect-a-performance-counter"></a>Shromažďovat čítač výkonu

Čítač výkonu lze přidat do cloudové služby pro Azure Diagnostics nebo Application Insights.

### <a name="application-insights"></a>Application Insights

Pro cloudové služby Azure Application Insights umožňuje že zadat jaké čítače výkonu, které chcete shromažďovat. Po jste [do projektu přidejte Application Insights](../application-insights/app-insights-cloudservices.md#sdk), konfigurační soubor s názvem **souboru ApplicationInsights.config** je přidat do projektu sady Visual Studio. Tento konfigurační soubor definuje, jaký typ informace Application Insights shromažďuje a odesílá do Azure.

Otevřete **souboru ApplicationInsights.config** souboru a najděte **ApplicationInsights** > **TelemetryModules** element. Každý `<Add>` podřízený element definuje typ telemetrii ke shromažďování, společně s její konfiguraci. Typ modulu telemetrická data čítače výkonu je `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector`. Pokud tento element je již definován, nepřidávejte jej znovu. Jednotlivé čítače výkonu ke shromažďování je definována v rámci uzlu s názvem `<Counters>`. Tady je příklad, který shromažďuje jednotky čítače výkonu:

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

Jednotlivých čítačů výkonu je reprezentován jako `<Add>` prvek v rámci `<Counters>`. `PerformanceCounter` Atribut definuje které čítače výkonu ke shromažďování. `ReportAs` Atribut je na název zobrazit na portálu Azure pro čítač výkonu. Všechny čítače výkonu, které shromažďujete umístí do kategorie s názvem **vlastní** na portálu. Na rozdíl od Azure Diagnostics nelze nastavit interval tyto čítače výkonu se shromažďuje a odesílá do Azure. S nástrojem Application Insights čítače výkonu se shromažďují a odesílají každou minutu. 

Application Insights automaticky shromažďuje následující čítače výkonu:

* \Process(??APP_WIN32_PROC??)\% Processor Time
* \Memory\Available Bytes
* \.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec
* \Process(??APP_WIN32_PROC??)\Private Bytes
* \Process(??APP_WIN32_PROC??)\IO Data Bytes/sec
* \Processor(_Total)\% Processor Time

Další informace najdete v tématu [čítače výkonu systému ve službě Application Insights](../application-insights/app-insights-performance-counters.md) a [Application Insights pro Azure Cloud Services](../application-insights/app-insights-cloudservices.md#performance-counters).

### <a name="azure-diagnostics"></a>Diagnostika Azure

> [!IMPORTANT]
> Zatímco tato data je agregován do účtu úložiště, nemá portálu **není** umožňují nativní grafu data. Důrazně doporučujeme integraci jiné diagnostické služby, jako Application Insights do své aplikace.

Rozšíření Azure Diagnostics pro cloudové služby umožňuje že zadat jaké čítače výkonu, které chcete shromažďovat. Azure Diagnostics, naleznete v tématu [Přehled monitorování cloudové služby](cloud-services-how-to-monitor.md#setup-diagnostics-extension).

Chcete shromáždit čítače výkonu jsou definovány v **diagnostics.wadcfgx** souboru. Umožňuje otevřít tento soubor (je definována na roli) v sadě Visual Studio a najděte **DiagnosticsConfiguration** > **PublicConfig** > **WadCfg**  >  **DiagnosticMonitorConfiguration** > **čítače výkonu** elementu. Přidejte nový **PerformanceCounterConfiguration** jako podřízený element. Tento element má dva atributy: `counterSpecifier` a `sampleRate`. `counterSpecifier` Atribut definuje které výkon systému čítač nastavena na shromažďování (popsané v předchozí části). `sampleRate` Hodnota udává, jak často je dotazovaný tuto hodnotu. Jako celek, všechny čítače výkonu se přenesou do Azure podle nadřazené `PerformanceCounters` elementu `scheduledTransferPeriod` hodnota atributu.

Další informace o `PerformanceCounters` element schématu, najdete v článku [Azure Diagnostics schématu](../monitoring-and-diagnostics/azure-diagnostics-schema-1dot3-and-later.md#performancecounters-element).

Období definované `sampleRate` používá atribut XML trvání datový typ k označení, jak často je dotazování čítače výkonu. V následujícím příkladu je rychlost, jakou nastavený na `PT3M`, což znamená `[P]eriod[T]ime[3][M]inutes`: každé tři minuty.

Další informace o tom, jak `sampleRate` a `scheduledTransferPeriod` jsou definovány, najdete v části **doba trvání datový typ** tématu [W3 XML datum a čas datum typy](https://www.w3schools.com/XML/schema_dtypes_date.asp) kurzu.

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

## <a name="create-a-new-perf-counter"></a>Vytvoří nový čítač výkonu

Nový čítač výkonu můžete vytvořit a používat váš kód. Váš kód, který vytvoří nový čítač výkonu musí být spuštěna zvýšenými oprávněními, že jinak se nezdaří. Cloudové služby `OnStart` spuštění kódu můžete vytvořit čítač výkonu, vyžadující, abyste roli spustit v kontextu zvýšenými oprávněními. Nebo můžete vytvořit úloha spuštění, který spouští zvýšenými oprávněními a vytváří čítač výkonu. Další informace o spuštění úlohy najdete v tématu [postupy pro konfiguraci a spuštění úlohy spuštění pro cloudové služby](cloud-services-startup-tasks.md).

Chcete-li konfigurovat vaše role spouštět se zvýšenými oprávněními, přidejte `<Runtime>` elementu, který chcete [.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef) souboru.

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

Můžete vytvořit a zaregistrovat nový čítač výkonu po zadání několika řádků kódu. Použití `System.Diagnostics.PerformanceCounterCategory.Create` přetížení metody, která vytvoří kategorii a čítače. Následující kód nejprve ověří, zda kategorii existuje a pokud chybí, vytvoří kategorii a čítače.

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

            // Define the cateogry and counter names.
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

Pokud chcete používat čítač, volání `Increment` nebo `IncrementBy` metoda.

```csharp
// Increase the counter by 1
counterServiceUsed.Increment();
```

Teď, když vaše aplikace používá vaše vlastní čítače, budete muset nakonfigurovat Azure Diagnostics nebo Application Insights pro sledování čítač.


### <a name="application-insights"></a>Application Insights

Jak už jsme si říkali, čítače výkonu pro Application Insights jsou definovány v **souboru ApplicationInsights.config** souboru. Otevřete **souboru ApplicationInsights.config** a najděte **ApplicationInsights** > **TelemetryModules** > **přidat**  >  **Čítače** elementu. Vytvoření `<Add>` podřízený element a sadu `PerformanceCounter` atribut kategorie a název čítače výkonu, kterou jste vytvořili v kódu. Nastavte `ReportAs` atribut popisný název, který chcete zobrazit na portálu.

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

### <a name="azure-diagnostics"></a>Diagnostika Azure

Jak je uvedeno, který chcete shromáždit čítače výkonu jsou definovány v **diagnostics.wadcfgx** souboru. Umožňuje otevřít tento soubor (je definována na roli) v sadě Visual Studio a najděte **DiagnosticsConfiguration** > **PublicConfig** > **WadCfg**  >  **DiagnosticMonitorConfiguration** > **čítače výkonu** elementu. Přidejte nový **PerformanceCounterConfiguration** jako podřízený element. Nastavte `counterSpecifier` atribut kategorie a název čítače výkonu, kterou jste vytvořili v kódu. 

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

- [Application Insights pro cloudové služby Azure](../application-insights/app-insights-cloudservices.md#performance-counters)
- [Čítače výkonu systému ve službě Application Insights](../application-insights/app-insights-performance-counters.md)
- [Zadání cesty čítače](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85))
- [Azure Diagnostics schématu - čítače výkonu](../monitoring-and-diagnostics/azure-diagnostics-schema-1dot3-and-later.md#performancecounters-element)