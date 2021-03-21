---
title: Shromažďování informací o čítačích výkonu v Azure Cloud Services (Classic) | Microsoft Docs
description: Naučte se zjišťovat, používat a vytvářet čítače výkonu v Cloud Services s Azure Diagnostics a Application Insights.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: fa5dd61c0764be45cdba68b73a4f55745ee5e55a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100585504"
---
# <a name="collect-performance-counters-for-your-azure-cloud-service-classic"></a>Shromažďování čítačů výkonu pro cloudovou službu Azure (Classic)

> [!IMPORTANT]
> [Azure Cloud Services (Rozšířená podpora)](../cloud-services-extended-support/overview.md) je nový model nasazení založený na Azure Resource Manager pro produkt Azure Cloud Services.V důsledku této změny se Azure Cloud Services běžící na modelu nasazení založeném na Azure Service Manager přejmenovala jako Cloud Services (Classic) a všechna nová nasazení by měla používat [Cloud Services (Rozšířená podpora)](../cloud-services-extended-support/overview.md).

Čítače výkonu poskytují způsob, jak sledovat, jak dobře aplikace a hostitel provádí. Windows Server poskytuje mnoho různých čítačů výkonu souvisejících s hardwarem, aplikacemi, operačním systémem a dalšími. Shromažďováním a odesíláním čítačů výkonu do Azure můžete analyzovat tyto informace, které vám pomohou lépe rozhodovat. 

## <a name="discover-available-counters"></a>Zjistit dostupné čítače

Čítač výkonu se skládá ze dvou částí, názvu sady (označovaného také jako kategorie) a jednoho nebo více čítačů. K získání seznamu dostupných čítačů výkonu můžete použít PowerShell.

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

`CounterSetName`Vlastnost představuje sadu (nebo kategorii) a je dobrým indikátorem toho, s jakými čítači výkonu souvisejí. `Paths`Vlastnost představuje kolekci čítačů pro sadu. Můžete také získat `Description` vlastnost pro další informace o sadě čítačů.

Chcete-li získat všechny čítače pro sadu, použijte `CounterSetName` hodnotu a rozbalte `Paths` kolekci. Každá položka cesty je čítač, který můžete dotazovat. Pokud například chcete získat dostupné čítače související se `Processor` sadou, rozbalte `Paths` kolekci:

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

Tyto cesty k jednotlivým čítačům lze přidat do diagnostického rozhraní používaného vaší cloudovou službou. Další informace o tom, jak je vytvořena cesta k čítači výkonu, najdete v tématu [Určení cesty čítače](/windows/win32/perfctrs/specifying-a-counter-path).

## <a name="collect-a-performance-counter"></a>Shromáždění čítače výkonu

Čítač výkonu může být přidán do vaší cloudové služby, a to buď Azure Diagnostics, nebo Application Insights.

### <a name="application-insights"></a>Application Insights

Azure Application Insights pro Cloud Services umožňuje určit čítače výkonu, které chcete shromáždit. Po [přidání Application Insights do projektu](../azure-monitor/app/cloudservices.md#sdk)se do projektu aplikace Visual Studio přidá konfigurační soubor s názvem **ApplicationInsights.config** . Tento konfigurační soubor definuje, jaký typ informací Application Insights shromažďuje a odesílá do Azure.

Otevřete soubor **ApplicationInsights.config** a vyhledejte prvek TelemetryModules **ApplicationInsights**  >   . Každý `<Add>` podřízený prvek definuje typ telemetrie ke shromáždění spolu se svou konfigurací. Typ modulu telemetrie čítače výkonu je `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector` . Pokud je tento prvek již definován, nepřidávejte jej podruhé. Každý čítač výkonu, který se má shromáždit, je definovaný v uzlu s názvem `<Counters>` . Tady je příklad, který shromažďuje čítače výkonu jednotky:

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

Každý čítač výkonu je reprezentován jako `<Add>` element v `<Counters>` . `PerformanceCounter`Atribut definuje, který čítač výkonu se má shromáždit. `ReportAs`Atribut je název, který se zobrazí v Azure Portal pro čítač výkonu. Libovolný čítač výkonu, který shromáždíte, je umístěn do kategorie s názvem **Custom** na portálu. Na rozdíl od Azure Diagnostics nemůžete nastavit interval, po který se tyto čítače výkonu shromažďují a odesílají do Azure. Při Application Insights se čítače výkonu shromažďují a odesílají každou minutu. 

Application Insights automaticky shromažďuje následující čítače výkonu:

* \Process(?? APP_WIN32_PROC??) \% Čas procesoru
* \Memory\Available Bytes
* \.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec
* \Process(??APP_WIN32_PROC??)\Private Bytes
* \Process(??APP_WIN32_PROC??)\IO Data Bytes/sec
* \Processor(_Total)\% Processor Time

Další informace najdete v tématu [čítače výkonu systému v Application Insights](../azure-monitor/app/performance-counters.md) a [Application Insights pro Azure Cloud Services](../azure-monitor/app/cloudservices.md#performance-counters).

### <a name="azure-diagnostics"></a>Azure Diagnostics

> [!IMPORTANT]
> I když jsou všechna tato data agregována do účtu úložiště, portál **neposkytuje nativní** způsob, jak data graficky vytvořit. Důrazně doporučujeme integrovat do své aplikace další diagnostické služby, například Application Insights.

Azure Diagnostics rozšíření pro Cloud Services umožňuje určit čítače výkonu, které chcete shromáždit. Pokud chcete nastavit Azure Diagnostics, přečtěte si téma [Přehled monitorování cloudové služby](cloud-services-how-to-monitor.md#setup-diagnostics-extension).

Čítače výkonu, které chcete shromáždit, jsou definovány v souboru **Diagnostics. wadcfgx** . Otevřete tento soubor (je definován pro jednu roli) v aplikaci Visual Studio a vyhledejte prvek **DiagnosticsConfiguration**  >  **PublicConfig**  >  **WadCfg**  >  **DiagnosticMonitorConfiguration**  >  **čítače výkonu** . Přidejte nový element **PerformanceCounterConfiguration** jako podřízenou položku. Tento element má dva atributy: `counterSpecifier` a `sampleRate` . `counterSpecifier`Atribut definuje sadu čítačů výkonu systému (popsaný v předchozí části), která má být shromažďována. `sampleRate`Hodnota určuje, jak často se tato hodnota dotazuje. Jako celek se všechny čítače výkonu přenesou do Azure podle `PerformanceCounters` `scheduledTransferPeriod` hodnoty atributu nadřazeného elementu.

Další informace o `PerformanceCounters` elementu schématu naleznete v tématu [Azure Diagnostics Schema](../azure-monitor/agents/diagnostics-extension-schema-windows.md#performancecounters-element).

Období definované `sampleRate` atributem používá datový typ doba trvání XML k určení, jak často se má čítač výkonu dotazovat. V následujícím příkladu je sazba nastavená na `PT3M` , což znamená, že `[P]eriod[T]ime[3][M]inutes` každé tři minuty je:

Další informace o tom, jak `sampleRate` `scheduledTransferPeriod` jsou definovány a, naleznete v tomto kurzu v části **datový typ doby trvání** v [w3 XML data a času](https://www.w3schools.com/XML/schema_dtypes_date.asp) .

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

## <a name="create-a-new-perf-counter"></a>Vytvoření nového čítače výkonu

Můžete vytvořit nový čítač výkonu a použít ho v kódu. Váš kód, který vytváří nový čítač výkonu, musí běžet se zvýšenými oprávněními, jinak selže. Spouštěcí kód vaší cloudové služby `OnStart` může vytvořit čítač výkonu, který vyžaduje spuštění role v kontextu se zvýšenými oprávněními. Případně můžete vytvořit úlohu po spuštění se zvýšenými oprávněními a vytvořit čítač výkonu. Další informace o úlohách po spuštění najdete v tématu [Konfigurace a spouštění úloh po spuštění pro cloudovou službu](cloud-services-startup-tasks.md).

Chcete-li nakonfigurovat, aby vaše role běžela se zvýšenými oprávněními, přidejte do `<Runtime>` souboru [. csdef](cloud-services-model-and-package.md#servicedefinitioncsdef) element.

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

Můžete vytvořit a zaregistrovat nový čítač výkonu s několika řádky kódu. Použijte `System.Diagnostics.PerformanceCounterCategory.Create` přetížení metody, které vytvoří kategorii i čítač. Následující kód nejprve zkontroluje, zda kategorie existuje, a pokud chybí, vytvoří kategorii i čítač.

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

Pokud chcete použít čítač, zavolejte `Increment` `IncrementBy` metodu nebo.

```csharp
// Increase the counter by 1
counterServiceUsed.Increment();
```

Teď, když vaše aplikace používá vlastní počítadlo, je potřeba nakonfigurovat Azure Diagnostics nebo Application Insights a sledovat čítač.


### <a name="application-insights"></a>Application Insights

Jak je uvedeno výše, čítače výkonu pro Application Insights jsou definovány v souboru **ApplicationInsights.config** . Otevřete **ApplicationInsights.config** a vyhledejte prvek **ApplicationInsights**  >  **TelemetryModules**  >  **Přidat**  >  **čítače** . Vytvořte `<Add>` podřízený element a nastavte `PerformanceCounter` atribut na kategorii a název čítače výkonu, který jste vytvořili v kódu. Nastavte `ReportAs` atribut na popisný název, který chcete zobrazit na portálu.

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

Jak je uvedeno výše, čítače výkonu, které chcete shromáždit, jsou definovány v souboru **Diagnostics. wadcfgx** . Otevřete tento soubor (je definován pro jednu roli) v aplikaci Visual Studio a vyhledejte prvek **DiagnosticsConfiguration**  >  **PublicConfig**  >  **WadCfg**  >  **DiagnosticMonitorConfiguration**  >  **čítače výkonu** . Přidejte nový element **PerformanceCounterConfiguration** jako podřízenou položku. Nastavte `counterSpecifier` atribut na kategorii a název čítače výkonu, který jste vytvořili v kódu. 

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
- [Čítače výkonu systému v Application Insights](../azure-monitor/app/performance-counters.md)
- [Zadání cesty k čítači](/windows/win32/perfctrs/specifying-a-counter-path)
- [Azure Diagnostics schématu – čítače výkonu](../azure-monitor/agents/diagnostics-extension-schema-windows.md#performancecounters-element)