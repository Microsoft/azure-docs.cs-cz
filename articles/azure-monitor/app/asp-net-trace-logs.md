---
title: Prozkoumejte protokoly trasování .NET v Application Insights
description: Hledání v protokolech vygenerovaných trasováním, NLog nebo Log4Net.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 05/08/2019
ms.openlocfilehash: ab3b12bf0401c4060823c6ed1d20dd6385cc397f
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90973836"
---
# <a name="explore-netnet-core-and-python-trace-logs-in-application-insights"></a>Prozkoumejte protokoly trasování .NET/.NET Core a Python v Application Insights

Odešle protokoly diagnostiky trasování pro vaši aplikaci ASP.NET/ASP.NET Core z ILogger, NLog, log4Net nebo System. Diagnostics. Trace do [Azure Application Insights][start]. V případě aplikací Pythonu odešlete protokoly diagnostiky trasování pomocí AzureLogHandler v OpenCensus Pythonu pro Azure Monitor. Pak je můžete prozkoumat a vyhledat. Tyto protokoly jsou sloučeny s dalšími soubory protokolu z vaší aplikace, takže můžete identifikovat trasování, které jsou spojeny s jednotlivými požadavky uživatelů a korelovat je s jinými událostmi a sestavami výjimek.

> [!NOTE]
> Potřebujete modul pro zachycení protokolů? Je to užitečný adaptér pro protokolovací nástroje třetích stran. Pokud však již nepoužíváte NLog, log4Net nebo System. Diagnostics. Trace, zvažte, zda přímo zavoláte [**Application Insights TrackTrace ()**](./api-custom-events-metrics.md#tracktrace) .
>
>
## <a name="install-logging-on-your-app"></a>Instalace protokolování do aplikace
Do projektu nainstalujte zvolené protokolovací rozhraní, které by mělo mít za následek zadání app.config nebo web.config.

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

## <a name="configure-application-insights-to-collect-logs"></a>Konfigurace Application Insights pro shromažďování protokolů
Pokud jste to ještě neudělali, [přidejte do projektu Application Insights](./asp-net.md) . Zobrazí se možnost zahrnutí kolektoru protokolů.

Nebo klikněte pravým tlačítkem na projekt v Průzkumník řešení ke **konfiguraci Application Insights**. Vyberte možnost **Konfigurace kolekce trasování** .

> [!NOTE]
> Žádná nabídka Application Insights ani možnost kolektoru protokolů? Zkuste [řešení potíží](#troubleshooting).

## <a name="manual-installation"></a>Ruční instalace
Tuto metodu použijte, pokud váš typ projektu není podporován instalačním programem Application Insights (například desktopový projekt systému Windows).

1. Pokud máte v úmyslu používat log4Net nebo NLog, nainstalujte ho do svého projektu.
2. V Průzkumník řešení klikněte pravým tlačítkem myši na projekt a vyberte možnost **Spravovat balíčky NuGet**.
3. Vyhledejte "Application Insights".
4. Vyberte jeden z následujících balíčků:

   - ILogger: [Microsoft. Extensions. Logging. ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/) 
 [ ![ NuGet ILogger banner](https://img.shields.io/nuget/vpre/Microsoft.Extensions.Logging.ApplicationInsights.svg)](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
   - NLOG: [Microsoft. ApplicationInsights. NLogTarget](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/) 
 [ ![ NuGet nLOG banner](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.NLogTarget.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
   - Log4Net: [Microsoft. ApplicationInsights. Log4NetAppender](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/) 
 [ ![ NuGet Log4Net banner](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.Log4NetAppender.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
   - Pro System. Diagnostics: Banner [Microsoft. ApplicationInsights. TraceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/) 
 [ ![ NuGet System. Diagnostics](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.TraceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
   - [Microsoft. ApplicationInsights. DiagnosticSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/) 
 [ ![ Banner pro naslouchací proces zdroje diagnostiky NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.DiagnosticSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
   - [Microsoft. ApplicationInsights. EtwCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/) 
 [ ![ Banner sběrače ETW pro NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EtwCollector.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
   - [Microsoft. ApplicationInsights. EventSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/) 
 [ ![ Informační zpráva naslouchacího procesu zdroje událostí NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EventSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)

Balíček NuGet nainstaluje nezbytná sestavení a upraví web.config nebo app.config, pokud je to možné.

## <a name="ilogger"></a>ILogger

Příklady použití implementace Application Insights ILogger s konzolovou aplikací a ASP.NET Core najdete v tématu [protokoly ApplicationInsightsLoggerProvider pro .NET Core ILogger](ilogger.md).

## <a name="insert-diagnostic-log-calls"></a>Vložit volání diagnostického protokolu
Použijete-li System. Diagnostics. Trace, bude typické volání:

```csharp
System.Diagnostics.Trace.TraceWarning("Slow response - database01");
```

Pokud dáváte přednost log4net nebo NLog, použijte:

```csharp
    logger.Warn("Slow response - database01");
```

## <a name="use-eventsource-events"></a>Použití událostí EventSource
Můžete nakonfigurovat události [System. Diagnostics. Tracing. EventSource](/dotnet/api/system.diagnostics.tracing.eventsource?view=netcore-3.1) pro odeslání do Application Insights jako trasování. Nejdřív nainstalujte `Microsoft.ApplicationInsights.EventSourceListener` balíček NuGet. Pak upravte `TelemetryModules` část souboru [ApplicationInsights.config](./configuration-with-applicationinsights-config.md) .

```xml
    <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
      <Sources>
        <Add Name="MyCompany" Level="Verbose" />
      </Sources>
    </Add>
```

Pro každý zdroj můžete nastavit následující parametry:
 * **Název** Určuje název EventSource, který se má shromáždit.
 * **Úroveň** určuje úroveň protokolování, která se má shromáždit: *kritická*, *Chyba*, *informativní*, *LogAlways*, *verbose*nebo *Warning*.
 * **Klíčová slova** (volitelné) zadejte celočíselnou hodnotu kombinací klíčového slova, které chcete použít.

## <a name="use-diagnosticsource-events"></a>Použití událostí DiagnosticSource
Můžete nakonfigurovat události [System. Diagnostics. DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) , které budou odeslány do Application Insights jako trasování. Nejdřív nainstalujte [`Microsoft.ApplicationInsights.DiagnosticSourceListener`](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener) balíček NuGet. Pak upravte část "TelemetryModules" souboru [ApplicationInsights.config](./configuration-with-applicationinsights-config.md) .

```xml
    <Add Type="Microsoft.ApplicationInsights.DiagnosticSourceListener.DiagnosticSourceTelemetryModule, Microsoft.ApplicationInsights.DiagnosticSourceListener">
      <Sources>
        <Add Name="MyDiagnosticSourceName" />
      </Sources>
    </Add>
```

Pro každý DiagnosticSource, který chcete trasovat, přidejte položku s atributem **Name** nastaveným na název vaší DiagnosticSource.

## <a name="use-etw-events"></a>Použít události ETW
Můžete nakonfigurovat události trasování událostí pro Windows (ETW), které se mají odeslat Application Insights jako trasování. Nejdřív nainstalujte `Microsoft.ApplicationInsights.EtwCollector` balíček NuGet. Pak upravte část "TelemetryModules" souboru [ApplicationInsights.config](./configuration-with-applicationinsights-config.md) .

> [!NOTE] 
> Události ETW se můžou shromažďovat jenom v případě, že se proces, který hostuje sadu SDK, spouští pod identitou, která je členem skupiny Performance Log Users nebo Administrators.

```xml
    <Add Type="Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule, Microsoft.ApplicationInsights.EtwCollector">
      <Sources>
        <Add ProviderName="MyCompanyEventSourceName" Level="Verbose" />
      </Sources>
    </Add>
```

Pro každý zdroj můžete nastavit následující parametry:
 * **ProviderName** je název zprostředkovatele ETW, který se má shromáždit.
 * **ProviderGuid** Určuje identifikátor GUID zprostředkovatele ETW, který se má shromáždit. Dá se použít místo `ProviderName` .
 * **Level** nastaví úroveň protokolování na shromažďovat. Může to být *kritická*, *Chyba*, *informativní*, *LogAlways*, *verbose*nebo *Warning*.
 * **Klíčová slova** (volitelné) nastavte celočíselnou hodnotu kombinací klíčových slov, která se má použít.

## <a name="use-the-trace-api-directly"></a>Přímé použití rozhraní API trasování
Rozhraní API pro trasování Application Insights můžete volat přímo. Toto rozhraní API používají adaptéry protokolování.

Příklad:

```csharp
var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
telemetry.TrackTrace("Slow response - database01");
```

Výhodou TrackTrace je, že do zprávy můžete ukládat poměrně dlouhá data. Můžete například zakódovat data POST.

Do zprávy můžete také přidat úroveň závažnosti. A podobně jako u jiné telemetrie můžete přidat hodnoty vlastností, které vám pomohou filtrovat nebo vyhledat různé sady trasování. Příklad:

  ```csharp
  var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
  telemetry.TrackTrace("Slow database response",
                 SeverityLevel.Warning,
                 new Dictionary<string,string> { {"database", db.ID} });
  ```

To vám umožní snadno odfiltrovat ve [vyhledávání][diagnostic] všechny zprávy určité úrovně závažnosti, které se vztahují k určité databázi.

## <a name="azureloghandler-for-opencensus-python"></a>AzureLogHandler pro OpenCensus Python
Obslužná rutina protokolu Azure Monitor umožňuje exportovat protokoly Pythonu pro Azure Monitor.

Instrumentujte svoji aplikaci pomocí [sady OpenCensus Python SDK](./opencensus-python.md) pro Azure monitor.

V tomto příkladu se dozvíte, jak odeslat Azure Monitor protokolu úrovně upozornění.

```python
import logging

from opencensus.ext.azure.log_exporter import AzureLogHandler

logger = logging.getLogger(__name__)
logger.addHandler(AzureLogHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>'))
logger.warning('Hello, World!')
```

## <a name="explore-your-logs"></a>Prozkoumat protokoly
Spusťte aplikaci v režimu ladění nebo ji nasaďte živě.

V podokně Přehled vaší aplikace na [portálu Application Insights][portal]vyberte [Hledat][diagnostic].

Můžete například:

* Filtrování trasování protokolů nebo položek s konkrétními vlastnostmi
* Podrobně zkontrolujte konkrétní položku.
* Najde další data systémového protokolu, která se vztahují ke stejnému požadavku uživatele (má stejné OperationId).
* Uložte konfiguraci stránky jako oblíbenou.

> [!NOTE]
>Pokud vaše aplikace odesílá spoustu dat a používáte sadu SDK Application Insights pro ASP.NET verze 2.0.0-beta3 nebo novější, může funkce *adaptivního vzorkování* fungovat a odesílat jenom část telemetrie. [Další informace o vzorkování.](./sampling.md)
>

## <a name="troubleshooting"></a>Řešení potíží
### <a name="how-do-i-do-this-for-java"></a>Návody to udělat pro Java?
V případě instrumentace bez kódu Java (doporučeno) protokoly se shromažďují mimo pole, použijte [agenta Java 3,0](./java-in-process-agent.md).

Pokud používáte sadu Java SDK, použijte [adaptéry protokolu Java](./java-trace-logs.md).

### <a name="theres-no-application-insights-option-on-the-project-context-menu"></a>V místní nabídce projektu není žádná možnost Application Insights
* Ujistěte se, že je ve vývojovém počítači nainstalován Developer Analytics Tools. V **Tools**  >  **rozšířeních a aktualizacích**nástrojů sady Visual Studio vyhledejte **Developer Analytics Tools**. Pokud není na kartě **nainstalované** , otevřete **online** kartu a nainstalujte ji.
* Může to být typ projektu, který Developer Analytics Tools nepodporuje. Použijte [ruční instalaci](#manual-installation).

### <a name="theres-no-log-adapter-option-in-the-configuration-tool"></a>V konfiguračním nástroji není žádná možnost adaptéru protokolu.
* Nejdřív nainstalujte rozhraní protokolování.
* Pokud používáte System. Diagnostics. Trace, ujistěte se, že je [v *web.config*nakonfigurovaný ](/dotnet/api/system.diagnostics.eventlogtracelistener?view=dotnet-plat-ext-3.1).
* Ujistěte se, že máte nejnovější verzi Application Insights. V aplikaci Visual Studio, přejít na **nástroje**  >  **rozšíření a aktualizace**a otevřete kartu **aktualizace** . Pokud tam **Developer Analytics Tools** , vyberte ho a aktualizujte ho.

### <a name="i-get-the-instrumentation-key-cannot-be-empty-error-message"></a><a name="emptykey"></a>Zobrazí se chybová zpráva "klíč instrumentace nemůže být prázdný"
Pravděpodobně jste nainstalovali balíček NuGet pro protokolovací adaptér bez instalace Application Insights. V Průzkumník řešení klikněte pravým tlačítkem na *ApplicationInsights.config*a vyberte **aktualizovat Application Insights**. Zobrazí se výzva, abyste se přihlásili k Azure a vytvořili prostředek Application Insights nebo znovu použijete existující. To by mělo problém vyřešit.

### <a name="i-can-see-traces-but-not-other-events-in-diagnostic-search"></a>Můžu zobrazit trasování, ale ne jiné události v diagnostickém vyhledávání
Může chvíli trvat, než se všechny události a požadavky dostanou přes kanál.

### <a name="how-much-data-is-retained"></a><a name="limits"></a>Kolik dat se zachová?
Množství dat, která jsou zachována, má vliv na několik faktorů. Další informace najdete v části [omezení](./api-custom-events-metrics.md#limits) na stránce metriky událostí zákazníka.

### <a name="i-dont-see-some-log-entries-that-i-expected"></a>Nevidím některé položky protokolu, které jsem očekával
Pokud vaše aplikace odesílá voluminous množství dat a používáte sadu SDK Application Insights pro ASP.NET verze 2.0.0-beta3 nebo novější, může funkce adaptivního vzorkování fungovat a odesílat pouze část telemetrie. [Další informace o vzorkování.](./sampling.md)

## <a name="next-steps"></a><a name="add"></a>Další kroky

* [Diagnostika selhání a výjimek v ASP.NET][exceptions]
* [Další informace o hledání][diagnostic]
* [Nastavení testů dostupnosti a odezvy][availability]
* [Řešení potíží][qna]

<!--Link references-->

[availability]: ./monitor-web-app-availability.md
[diagnostic]: ./diagnostic-search.md
[exceptions]: asp-net-exceptions.md
[portal]: https://portal.azure.com/
[qna]: ../faq.md
[start]: ./app-insights-overview.md

