---
title: Shromažďování o čítačích výkonu ve službě Azure Cloud Services | Dokumentace Microsoftu
description: Zjistěte, jak zjistit, použití a vytvoření čítače výkonu v cloudových službách Azure Diagnostics a Application Insights.
services: cloud-services
documentationcenter: .net
author: jpconnock
manager: timlt
editor: ''
ms.assetid: ''
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/18
ms.author: jeconnoc
ms.openlocfilehash: bae1e7de3c24ff6fbf943ef93ad449d784feb0c8
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/27/2018
ms.locfileid: "53788692"
---
# <a name="collect-performance-counters-for-your-azure-cloud-service"></a>Shromažďování čítačů výkonu pro cloudové služby Azure

Čítače výkonu poskytují způsob, jak vám umožní sledovat, jak dobře fungují aplikace a hostitel. Systém Windows Server poskytuje mnoho různých čítačů výkonu související s hardwaru, aplikace, operačního systému a další. Díky shromažďování a odesílání čítače výkonu do Azure, můžete analyzovat tato informace, které pomůžou lépe rozhodovat. 

## <a name="discover-available-counters"></a>Zjistit dostupné čítače

Čítače výkonu, který se skládá ze dvou částí, název sady (označované také jako kategorie) a jeden nebo více čítačů. Chcete-li získat seznam dostupných čítačů výkonu můžete použít PowerShell:

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

`CounterSetName` Vlastnost představuje sadu (nebo kategorie) a je vhodný indikátor co jsou čítače výkonu související s. `Paths` Vlastnost představuje kolekci čítače pro sadu. Můžete také narazit `Description` vlastnost pro další informace o sadě čítačů.

Chcete-li získat všechny čítače pro sadu, použijte `CounterSetName` hodnotu a rozbalte `Paths` kolekce. Každá položka cesta je čítač, který můžete zadávat dotazy. Například, chcete-li získat dostupné čítače související s `Processor` nastavení, rozbalte `Paths` kolekce:

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

Tyto jednotlivé čítače, které cesty mohou být přidány do diagnostics framework používá cloudovou službu. Další informace o jak je vytvořená cesta čítače výkonu, naleznete v tématu [zadání cesty čítače](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85)).

## <a name="collect-a-performance-counter"></a>Shromažďovat čítač výkonu

Čítače výkonu, který můžete přidat do cloudové služby pro Azure Diagnostics a Application Insights.

### <a name="application-insights"></a>Application Insights

Azure Application Insights pro služby Cloud Services umožňuje že určit jaké čítače výkonu, které chcete shromažďovat. Poté co [přidat Application Insights do projektu](../azure-monitor/app/cloudservices.md#sdk), konfigurační soubor s názvem **soubor ApplicationInsights.config** se přidá do projektu sady Visual Studio. Tento konfigurační soubor definuje, jaký typ informace o Application Insights shromažďuje a odesílá do Azure.

Otevřít **soubor ApplicationInsights.config** souborů a vyhledejte **ApplicationInsights** > **telemetrymodules následující** elementu. Každý `<Add>` podřízený element definuje typ telemetrických dat chcete shromažďovat, společně s jeho konfigurace. Typ modulu telemetrická data čítače výkonu je `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector`. Pokud tento prvek je již definován, nepřidávejte jej znovu. Každý čítač výkonu ke shromažďování je definován v uzlu s názvem `<Counters>`. Tady je příklad, který shromažďuje čítače výkonu disku:

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

Každý čítač výkonu je vyjádřena jako `<Add>` element v rámci `<Counters>`. `PerformanceCounter` Atribut definuje, které čítače výkonu chcete shromažďovat. `ReportAs` Atribut je název zobrazit na webu Azure Portal pro čítač výkonu. Všechny čítače výkonu můžete shromažďovat přejde do kategorie s názvem **vlastní** na portálu. Na rozdíl od Azure Diagnostics nelze nastavit interval tyto čítače výkonu se shromažďují a odesílají do Azure. Pomocí nástroje Application Insights čítače výkonu shromážděné a posílat každou minutu. 

Application Insights automaticky shromažďuje následující čítače výkonu:

* \Process(??APP_WIN32_PROC??)\% Processor Time
* \Memory\Available Bytes
* \.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec
* \Process(??APP_WIN32_PROC??)\Private Bytes
* \Process(??APP_WIN32_PROC??)\IO Data Bytes/sec
* \Processor(_Total)\% Processor Time

Další informace najdete v tématu [čítače výkonu systému ve službě Application Insights](../application-insights/app-insights-performance-counters.md) a [Application Insights pro Azure Cloud Services](../azure-monitor/app/cloudservices.md#performance-counters).

### <a name="azure-diagnostics"></a>Diagnostika Azure

> [!IMPORTANT]
> Zatímco tato data se agregují do účtu úložiště, na portálu nepodporuje **není** nativní způsob, jak data grafu. Důrazně doporučujeme integraci jiné diagnostiky služby, jako jsou Application Insights do vaší aplikace.

Rozšíření Azure Diagnostics pro Cloud Services umožňuje že určit jaké čítače výkonu, které chcete shromažďovat. Nastavení diagnostiky Azure, najdete v článku [Přehled monitorování Cloud Service](cloud-services-how-to-monitor.md#setup-diagnostics-extension).

Čítače výkonu, které chcete shromažďovat jsou definovány v **diagnostics.wadcfgx** souboru. Otevřete tento soubor (je definována na roli) v sadě Visual Studio a najít **DiagnosticsConfiguration** > **PublicConfig** > **WadCfg**  >  **DiagnosticMonitorConfiguration** > **čítače výkonu** elementu. Přidat nový **PerformanceCounterConfiguration** jako podřízený element. Tento prvek obsahuje dva atributy: `counterSpecifier` a `sampleRate`. `counterSpecifier` Atribut definuje které výkon systému sad čítačů (uvedeno v předchozí části) ke shromažďování. `sampleRate` Hodnota označuje, jak často se dotázal tuto hodnotu. Jako celek, všechny čítače výkonu jsou přeneseny do Azure podle nadřazené `PerformanceCounters` elementu `scheduledTransferPeriod` hodnotu atributu.

Další informace o `PerformanceCounters` prvek schématu, najdete v článku [Azure Diagnostics Schema](../azure-monitor/platform/diagnostics-extension-schema-1dot3.md#performancecounters-element).

V období definovaném `sampleRate` používá atribut XML doba trvání datový typ označuje, jak často dotazování čítače výkonu. V následujícím příkladu rychlost přenosu nastavená na `PT3M`, což znamená, že `[P]eriod[T]ime[3][M]inutes`: každé 3 minuty.

Další informace o tom, jak `sampleRate` a `scheduledTransferPeriod` jsou definované, najdete v článku **doba trvání datový typ** tématu [W3 XML datum a čas datum typy](https://www.w3schools.com/XML/schema_dtypes_date.asp) kurzu.

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

## <a name="create-a-new-perf-counter"></a>Vytvořit nový čítač výkonu

Nový čítač výkonu můžete vytvořit a používat váš kód. Váš kód, který vytvoří nový čítač výkonu musí být spuštěný se zvýšenými oprávněními, že jinak dojde k selhání. Cloudové služby `OnStart` spouštěcí kód můžete vytvořit čítač výkonu, které vyžadují, abyste pro běh role v kontextu se zvýšenými oprávněními. Nebo můžete vytvořit úlohy po spuštění, který běží se zvýšenými oprávněními a vytváří čítač výkonu. Další informace o úkolech po spuštění najdete v tématu [jak nakonfigurovat a spustit úlohy po spuštění pro cloudovou službu](cloud-services-startup-tasks.md).

Chcete-li konfigurovat vaše role ke spuštění se zvýšenými oprávněními, přidejte `<Runtime>` elementu [.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef) souboru.

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

Můžete vytvořit a zaregistrovat nový čítač výkonu po zadání několika řádků kódu. Použití `System.Diagnostics.PerformanceCounterCategory.Create` přetížení metody, která vytvoří kategorie a čítače. Následující kód nejprve zkontroluje, zda existuje kategorie a pokud chybí, vytvoří kategorie a čítače.

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

Pokud chcete používat čítač, zavolejte `Increment` nebo `IncrementBy` metody.

```csharp
// Increase the counter by 1
counterServiceUsed.Increment();
```

Teď, když vaše aplikace používá vlastní čítače, musíte nakonfigurovat Azure Diagnostics a Application Insights sledovat čítače.


### <a name="application-insights"></a>Application Insights

Jak už jsme uvedli, čítače výkonu pro Application Insights jsou definovány v **soubor ApplicationInsights.config** souboru. Otevřít **soubor ApplicationInsights.config** a najít **ApplicationInsights** > **telemetrymodules následující** > **přidat**  >  **Čítače** elementu. Vytvoření `<Add>` podřízený element a nastavte `PerformanceCounter` atribut kategorie a název čítače výkonu, kterou jste vytvořili v kódu. Nastavte `ReportAs` atribut popisný název, který chcete zobrazit na portálu.

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

Jak je uvedeno výše, které chcete shromáždit čítače výkonu jsou definovány v **diagnostics.wadcfgx** souboru. Otevřete tento soubor (je definována na roli) v sadě Visual Studio a najít **DiagnosticsConfiguration** > **PublicConfig** > **WadCfg**  >  **DiagnosticMonitorConfiguration** > **čítače výkonu** elementu. Přidat nový **PerformanceCounterConfiguration** jako podřízený element. Nastavte `counterSpecifier` atribut kategorie a název čítače výkonu, kterou jste vytvořili v kódu. 

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
- [Čítače výkonu systému ve službě Application Insights](../application-insights/app-insights-performance-counters.md)
- [Zadání cesty čítače](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85))
- [Azure Diagnostics Schema - čítače výkonu](../azure-monitor/platform/diagnostics-extension-schema-1dot3.md#performancecounters-element)