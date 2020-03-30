---
title: Prozkoumejte protokoly trasování rozhraní .NET v přehledech aplikací
description: Protokoly hledání generované trasování, NLog nebo Log4Net.
ms.topic: conceptual
ms.date: 05/08/2019
ms.openlocfilehash: 273d5a2f4e1155541e159332312bdaa68aa175d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276267"
---
# <a name="explore-netnet-core-and-python-trace-logs-in-application-insights"></a>Prozkoumejte protokoly trasování jádra a pythonu .NET/.NET v přehledech aplikací

Odešlete protokoly sledování diagnostiky pro vaši ASP.NET/ASP.NET základní aplikaci z ILogger, NLog, log4Net nebo System.Diagnostics.Trace do [Azure Application Insights][start]. Pro aplikace Pythonu odesílat protokoly diagnostiky trasování pomocí AzureLogHandler v OpenCensus Python pro Azure Monitor. Pak je můžete prozkoumat a prohledat. Tyto protokoly jsou sloučeny s ostatními soubory protokolu z vaší aplikace, takže můžete identifikovat trasování, které jsou přidruženy ke každému požadavku uživatele a korelovat je s jinými událostmi a sestavami výjimek.

> [!NOTE]
> Potřebujete modul pro zachytávání protokolů? Je to užitečný adaptér pro úhozy kláves třetích stran. Ale pokud ještě nepoužíváte NLog, log4Net nebo System.Diagnostics.Trace, zvažte pouze volání [**Application Insights TrackTrace()**](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) přímo.
>
>
## <a name="install-logging-on-your-app"></a>Instalace přihlášení do aplikace
Nainstalujte do projektu vybranou architekturu protokolování, což by mělo vést k zadání v souboru app.config nebo web.config.

```XML
 <configuration>
  <system.diagnostics>
    <trace>
      <listeners>
        <add name="myAppInsightsListener" type="Microsoft.ApplicationInsights.TraceListener.ApplicationInsightsTraceListener, Microsoft.ApplicationInsights.TraceListener" />
      </listeners>
    </trace>
  </system.diagnostics>
</configuration>
```

## <a name="configure-application-insights-to-collect-logs"></a>Konfigurace přehledů aplikací pro shromažďování protokolů
Pokud jste to ještě neudělali, [přidejte do projektu přehledy aplikací.](../../azure-monitor/app/asp-net.md) Zobrazí se možnost zahrnout sběrač protokolů.

Nebo klikněte pravým tlačítkem myši na projekt v Průzkumníku řešení **a nakonfigurujte přehledy aplikací**. Vyberte možnost **Konfigurovat shromažďování trasování.**

> [!NOTE]
> Žádná nabídka Application Insights nebo možnost sběrače protokolů? Zkuste [poradce při potížích](#troubleshooting).

## <a name="manual-installation"></a>Ruční instalace
Tuto metodu použijte, pokud váš typ projektu není podporován instalačním programem Application Insights (například projekt plochy systému Windows).

1. Pokud máte v plánu použít log4Net nebo NLog, nainstalujte jej do projektu.
2. V Průzkumníku řešení klikněte pravým tlačítkem myši na projekt a vyberte **Spravovat balíčky NuGet**.
3. Vyhledejte "Application Insights."
4. Vyberte jeden z následujících balíčků:

   - Pro ILogger: [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.Extensions.Logging.ApplicationInsights.svg)](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
   - Pro NLog: [Microsoft.ApplicationInsights.NlogTarget](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.NLogTarget.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
   - Pro Log4Net: [Microsoft.ApplicationInsights.Log4NetAppender](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.Log4NetAppender.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
   - Pro System.Diagnostics: [Microsoft.ApplicationInsights.TraceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.TraceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
   - [Microsoft.ApplicationInsights.DiagnosticSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.DiagnosticSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
   - [Microsoft.ApplicationInsights.EtwCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EtwCollector.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
   - [Microsoft.ApplicationInsights.EventSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EventSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)

Balíček NuGet nainstaluje potřebná sestavení a upraví web.config nebo app.config, pokud je to možné.

## <a name="ilogger"></a>ILogger

Příklady použití implementace Application Insights ILogger s konzolovými aplikacemi a ASP.NET core naleznete v tématu [ApplicationInsightsLoggerProvider for .NET Core ILogger logs](ilogger.md).

## <a name="insert-diagnostic-log-calls"></a>Vložení volání diagnostického protokolu
Pokud používáte System.Diagnostics.Trace, typické volání by bylo:

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

Pokud dáváte přednost log4net nebo NLog, použijte:

    logger.Warn("Slow response - database01");

## <a name="use-eventsource-events"></a>Použít události EventSource
Události [System.Diagnostics.Tracing.EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) můžete nakonfigurovat tak, aby byly odeslány do přehledů aplikací jako trasování. Nejprve nainstalujte balíček `Microsoft.ApplicationInsights.EventSourceListener` NuGet. Potom upravte `TelemetryModules` část souboru [ApplicationInsights.config.](../../azure-monitor/app/configuration-with-applicationinsights-config.md)

```xml
    <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
      <Sources>
        <Add Name="MyCompany" Level="Verbose" />
      </Sources>
    </Add>
```

Pro každý zdroj můžete nastavit následující parametry:
 * **Název** určuje název EventSource shromažďovat.
 * **Úroveň** určuje úroveň protokolování, která má být shromažďována: *Kritická*, *Chyba*, *Informační*, *LogAlways*, *Verbose*nebo *Upozornění*.
 * **Klíčová slova** (volitelné) určují celou hodnotu kombinací klíčových slov, které mají být používány.

## <a name="use-diagnosticsource-events"></a>Použití událostí DiagnosticSource
Události [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) můžete nakonfigurovat tak, aby byly odeslány do přehledů aplikací jako trasování. Nejprve nainstalujte balíček [`Microsoft.ApplicationInsights.DiagnosticSourceListener`](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener) NuGet. Potom upravte část "TelemetryModules" souboru [ApplicationInsights.config.](../../azure-monitor/app/configuration-with-applicationinsights-config.md)

```xml
    <Add Type="Microsoft.ApplicationInsights.DiagnosticSourceListener.DiagnosticSourceTelemetryModule, Microsoft.ApplicationInsights.DiagnosticSourceListener">
      <Sources>
        <Add Name="MyDiagnosticSourceName" />
      </Sources>
    </Add>
```

Pro každý DiagnosticSource, který chcete sledovat, přidejte položku s atributem **Name** nastaveným na název zdroje diagnostiky.

## <a name="use-etw-events"></a>Použití událostí ETW
Můžete nakonfigurovat události trasování událostí pro Windows (ETW), které mají být odeslány do Application Insights jako trasování. Nejprve nainstalujte balíček `Microsoft.ApplicationInsights.EtwCollector` NuGet. Potom upravte část "TelemetryModules" souboru [ApplicationInsights.config.](../../azure-monitor/app/configuration-with-applicationinsights-config.md)

> [!NOTE] 
> Události ETW lze shromažďovat pouze v případě, že proces, který je hostitelem sady SDK, běží pod identitou, která je členem uživatelů nebo správců protokolu výkonu.

```xml
    <Add Type="Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule, Microsoft.ApplicationInsights.EtwCollector">
      <Sources>
        <Add ProviderName="MyCompanyEventSourceName" Level="Verbose" />
      </Sources>
    </Add>
```

Pro každý zdroj můžete nastavit následující parametry:
 * **ProviderName** je název zprostředkovatele ETW shromažďovat.
 * **ProviderGuid** určuje identifikátor GUID poskytovatele ETW shromažďovat. Může být použit `ProviderName`místo .
 * **Úroveň** nastaví úroveň protokolování shromažďovat. Může být *kritická*, *Chyba*, *Informační*, *LogAlways*, *Verbose*nebo *Upozornění*.
 * **Klíčová slova** (volitelně) nastavují celou hodnotu kombinací klíčových slov, která se mají použít.

## <a name="use-the-trace-api-directly"></a>Přímé použití rozhraní Trace API
Rozhraní API trasování Application Insights můžete volat přímo. Adaptéry protokolování používají toto rozhraní API.

Například:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

Výhodou TrackTrace je, že můžete umístit relativně dlouhá data ve zprávě. Například můžete kódovat data POST tam.

Ke zprávě můžete také přidat úroveň závažnosti. A stejně jako ostatní telemetrie můžete přidat hodnoty vlastností, které vám pomohou filtrovat nebo vyhledávat různé sady trasování. Například:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });

To by vám umožní snadno odfiltrovat v [hledat][diagnostic] všechny zprávy určité úrovně závažnosti, které se vztahují k určité databázi.

## <a name="azureloghandler-for-opencensus-python"></a>AzureLogHandler pro OpenCensus Python
Obslužná rutina protokolu monitoru Azure umožňuje exportovat protokoly Pythonu do Azure Monitoru.

Instrumentujte svou aplikaci pomocí [sady OpenCensus Python SDK](../../azure-monitor/app/opencensus-python.md) pro Azure Monitor.

Tento příklad ukazuje, jak odeslat protokol úrovně upozornění na Azure Monitor.

```python
import logging

from opencensus.ext.azure.log_exporter import AzureLogHandler

logger = logging.getLogger(__name__)
logger.addHandler(AzureLogHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>'))
logger.warning('Hello, World!')
```

## <a name="explore-your-logs"></a>Prozkoumejte své protokoly
Spusťte aplikaci v režimu ladění nebo ji nasaďte živě.

V podokně přehledu aplikace [na portálu Přehledy aplikací][portal]vyberte [Hledat][diagnostic].

Můžete například:

* Filtrování trasování protokolu nebo položek se specifickými vlastnostmi.
* Zkontrolujte konkrétní položku podrobně.
* Najít další data protokolu systému, který se vztahuje ke stejnému požadavku uživatele (má stejné OperationId).
* Uložte konfiguraci stránky jako oblíbenou stránku.

> [!NOTE]
>Pokud vaše aplikace odešle velké množství dat a používáte application insights SDK pro ASP.NET verze 2.0.0-beta3 nebo novější, funkce *adaptivnívzorkování* může fungovat a odesílat pouze část telemetrie. [Další informace o vzorkování.](../../azure-monitor/app/sampling.md)
>

## <a name="troubleshooting"></a>Řešení potíží
### <a name="how-do-i-do-this-for-java"></a>Jak to mám udělat pro Javu?
Použijte [adaptéry protokolu Java](../../azure-monitor/app/java-trace-logs.md).

### <a name="theres-no-application-insights-option-on-the-project-context-menu"></a>V kontextové nabídce projektu není žádná možnost Application Insights.
* Ujistěte se, že vývojářské analytické nástroje jsou nainstalovány ve vývojovém počítači. V**rozšířeních a aktualizacích** **nástrojů** > sady Visual Studio vyhledejte nástroje **pro analýzu vývojářů**. Pokud není na kartě **Nainstalováno,** otevřete kartu **Online** a nainstalujte ji.
* Může se jedná o typ projektu, který devloper Analytics Tools nepodporuje. Použijte [ruční instalaci](#manual-installation).

### <a name="theres-no-log-adapter-option-in-the-configuration-tool"></a>V konfiguračním nástroji není žádná možnost adaptéru protokolu.
* Nejprve nainstalujte architekturu protokolování.
* Pokud používáte System.Diagnostics.Trace, ujistěte se, že jste [ji nakonfigurovali v *web.config*](https://msdn.microsoft.com/library/system.diagnostics.eventlogtracelistener.aspx).
* Ujistěte se, že máte nejnovější verzi Application Insights. V sadě Visual Studio přejděte na**rozšíření a aktualizace** **nástrojů** > a otevřete kartu **Aktualizace.** Pokud jsou k **dispozici nástroje pro analýzu vývojářů,** vyberte je a aktualizujte je.

### <a name="i-get-the-instrumentation-key-cannot-be-empty-error-message"></a><a name="emptykey"></a>Zobrazuje se chybová zpráva "Instrumentační klíč nemůže být prázdný"
Pravděpodobně jste nainstalovali protokolovací adaptér Nuget balíček bez instalace Application Insights. V Průzkumníku řešení klepněte pravým tlačítkem myši na *soubor ApplicationInsights.config*a vyberte **aktualizovat přehledy aplikací**. Budete vyzváni k přihlášení do Azure a vytvoření prostředku Application Insights nebo opětovné použití existujícího. To by mělo vyřešit problém.

### <a name="i-can-see-traces-but-not-other-events-in-diagnostic-search"></a>V diagnostickém vyhledávání vidím stopy, ale ne jiné události.
Může chvíli trvat, než se všechny události a požadavky dostanou přes kanál.

### <a name="how-much-data-is-retained"></a><a name="limits"></a>Kolik dat je uchováno?
Množství uchovávaných dat ovlivňuje několik faktorů. Další informace najdete v části [Limity](../../azure-monitor/app/api-custom-events-metrics.md#limits) na stránce metrik událostí zákazníka.

### <a name="i-dont-see-some-log-entries-that-i-expected"></a>Nezobrazují se některé položky protokolu, které jsem očekával(a)
Pokud vaše aplikace odesílá objemné množství dat a používáte application insights SDK pro ASP.NET verze 2.0.0-beta3 nebo novější, funkce adaptivního vzorkování může fungovat a odesílat pouze část telemetrie. [Další informace o vzorkování.](../../azure-monitor/app/sampling.md)

## <a name="next-steps"></a><a name="add"></a>Další kroky

* [Diagnostikovat selhání a výjimky v ASP.NET][exceptions]
* [Další informace o hledání][diagnostic]
* [Nastavení testů dostupnosti a odezvy][availability]
* [Řešení potíží][qna]

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[exceptions]: asp-net-exceptions.md
[portal]: https://portal.azure.com/
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[start]: ../../azure-monitor/app/app-insights-overview.md
