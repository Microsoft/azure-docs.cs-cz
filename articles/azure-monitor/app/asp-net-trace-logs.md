---
title: Prozkoumejte protokoly trasování .NET ve službě Application Insights
description: Hledání protokolů generovaných trasování, NLog a Log4Net.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0c2a084f-6e71-467b-a6aa-4ab222f17153
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: mbullwin
ms.openlocfilehash: 74cb1b3ec4e0570aa4316e6f45e99719f36815d1
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65150703"
---
# <a name="explore-netnet-core-trace-logs-in-application-insights"></a>Prozkoumejte základní.NET/.NET protokoly trasování v Application Insights

Odeslání protokolů diagnostické trasování pro aplikaci ASP.NET/ASP.NET Core z objektu ILogger, NLog, log4Net nebo System.Diagnostics.Trace k [Azure Application Insights][start]. Potom můžete prozkoumat a prohledávat. Tyto protokoly jsou sloučeny s další soubory protokolu z vašich aplikací, abyste mohli identifikovat trasování, které jsou spojené s každou žádostí uživatele a proveďte jejich korelaci s jinými události a sestavy výjimek.

> [!NOTE]
> Je třeba modulu protokolu capture? To je užitečné adaptér protokolovacích nástrojů třetích stran. Pokud už nepoužíváte NLog, log4Net nebo System.Diagnostics.Trace, vezměte v úvahu pouze volání, ale [ **Application Insights použitím metod TrackTrace()** ](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) přímo.
>
>
## <a name="install-logging-on-your-app"></a>Nainstalujte protokolování v aplikaci
Instalaci zvolený protokolovacího rozhraní ve vašem projektu, který by měl mít za následek záznam v souboru app.config nebo web.config.

```XML
    <configuration>
      <system.diagnostics>
    <trace autoflush="true" indentsize="0">
      <listeners>
        <add name="myAppInsightsListener" type="Microsoft.ApplicationInsights.TraceListener.ApplicationInsightsTraceListener, Microsoft.ApplicationInsights.TraceListener" />
      </listeners>
    </trace>
  </system.diagnostics>
   </configuration>
```

## <a name="configure-application-insights-to-collect-logs"></a>Nakonfigurovat Application Insights na shromažďování protokolů
[Přidejte Application Insights do projektu](../../azure-monitor/app/asp-net.md) Pokud jste tak ještě neučinili. Zobrazí se vám možnost zahrnout kolektoru protokolů.

Nebo klikněte pravým tlačítkem na projekt v Průzkumníku řešení **konfigurovat Application Insights**. Vyberte **konfiguraci kolekce trasování** možnost.

> [!NOTE]
> Žádná možnost Application Insights nabídky nebo protokolu kolekcí? Zkuste [řešení potíží s](#troubleshooting).

## <a name="manual-installation"></a>Ruční instalace
Tuto metodu použijte, pokud typ projektu nepodporuje Instalační služby Application Insights (třeba Windows desktopové projekt).

1. Pokud máte v plánu používat log4Net nebo NLog, nainstalujte ji do vašeho projektu.
2. V Průzkumníku řešení klikněte pravým tlačítkem myši na projekt a vyberte **spravovat balíčky NuGet**.
3. Vyhledejte spojení "Application Insights."
4. Vyberte jednu z následujících balíčků:

   - Pro ILogger: [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.Extensions.Logging.ApplicationInsights.svg)](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
   - Pro NLog: [Microsoft.ApplicationInsights.NLogTarget](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
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

Balíček NuGet nainstaluje potřebná sestavení a je-li vyžadován změní web.config nebo app.config.

## <a name="ilogger"></a>ILogger

Příklady implementace objektu ILogger Application Insights pomocí konzolové aplikace a ASP.NET Core, najdete v článku [protokoly ApplicationInsightsLoggerProvider pro .NET Core ILogger](ilogger.md).

## <a name="insert-diagnostic-log-calls"></a>Vložit diagnostický protokol volání
Pokud používáte System.Diagnostics.Trace, typické volání by byl:

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

Pokud dáváte přednost, log4net nebo NLog, použijte:

    logger.Warn("Slow response - database01");

## <a name="use-eventsource-events"></a>Použití událostí EventSource
Můžete nakonfigurovat [System.Diagnostics.Tracing.EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) události k odeslání do Application Insights jako trasování. Nejdřív nainstalujte `Microsoft.ApplicationInsights.EventSourceListener` balíček NuGet. Upravte `TelemetryModules` část [soubor ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) souboru.

```xml
    <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
      <Sources>
        <Add Name="MyCompany" Level="Verbose" />
      </Sources>
    </Add>
```

Pro každý zdroj můžete nastavit následující parametry:
 * **Název** Určuje název EventSource ke shromažďování.
 * **Úroveň** určuje úroveň protokolování shromažďovat: *Kritické*, *chyba*, *informační*, *LogAlways*, *podrobné*, nebo *upozornění*.
 * **Klíčová slova** (volitelné) zadejte celočíselnou hodnotu kombinací – klíčové slovo lze použít.

## <a name="use-diagnosticsource-events"></a>Použití DiagnosticSource událostí
Můžete nakonfigurovat [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) události k odeslání do Application Insights jako trasování. Nejdřív nainstalujte [ `Microsoft.ApplicationInsights.DiagnosticSourceListener` ](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener) balíček NuGet. Potom upravte část "Telemetrymodules následující" [soubor ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) souboru.

```xml
    <Add Type="Microsoft.ApplicationInsights.DiagnosticSourceListener.DiagnosticSourceTelemetryModule, Microsoft.ApplicationInsights.DiagnosticSourceListener">
      <Sources>
        <Add Name="MyDiagnosticSourceName" />
      </Sources>
    </Add>
```

Pro každý DiagnosticSource chcete trasovat, přidejte položku s **název** atribut nastavte na název vaší DiagnosticSource.

## <a name="use-etw-events"></a>Použití událostí trasování událostí pro Windows
Můžete nakonfigurovat události trasování událostí pro Windows (ETW) k odeslání do Application Insights jako trasování. Nejdřív nainstalujte `Microsoft.ApplicationInsights.EtwCollector` balíček NuGet. Potom upravte část "Telemetrymodules následující" [soubor ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) souboru.

> [!NOTE] 
> Události trasování událostí pro Windows můžou shromažďují, pouze pokud spustí proces, který je hostitelem sadu SDK s identitou, která je členem skupiny Performance Log Users nebo Administrators.

```xml
    <Add Type="Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule, Microsoft.ApplicationInsights.EtwCollector">
      <Sources>
        <Add ProviderName="MyCompanyEventSourceName" Level="Verbose" />
      </Sources>
    </Add>
```

Pro každý zdroj můžete nastavit následující parametry:
 * **ProviderName** je název zprostředkovatele trasování událostí pro Windows ke shromažďování.
 * **GuidZprostředkovatele** Určuje identifikátor GUID zprostředkovatele trasování událostí pro Windows ke shromažďování. Je možné místo `ProviderName`.
 * **Úroveň** nastavuje úroveň protokolování pro shromažďování. Může to být *kritický*, *chyba*, *informativní*, *LogAlways*, *Verbose*, nebo *Upozornění*.
 * **Klíčová slova** (volitelné) nastavte hodnotu celého čísla kombinací – klíčové slovo lze použít.

## <a name="use-the-trace-api-directly"></a>Trasování API přímo použít
Trasování Application Insights API můžete volat přímo. Adaptéry protokolování pomocí tohoto rozhraní API.

Příklad:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

Výhodou TrackTrace je umístit relativně dlouho data ve zprávě. Můžete například kódovat následných dat existuje.

Můžete také přidat úroveň závažnosti zprávě. A stejně jako další telemetrická data, můžete přidat hodnoty vlastností, které pomůžou filtr nebo hledaný pro různé skupiny trasování. Příklad:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });

To umožňuje snadno vyfiltrovat v [hledání] [ diagnostic] všechny zprávy konkrétní závažnosti úrovně, které se vztahují ke konkrétní databázi.

## <a name="explore-your-logs"></a>Prozkoumejte protokoly
Spusťte aplikaci v režimu ladění nebo můžete nasadit za provozu.

V podokně s přehledem vaší aplikace v [portálu služby Application Insights][portal]vyberte [hledání][diagnostic].

Je to možné, například:

* Filtrovat v protokolu trasování nebo na položky s určitými vlastnostmi.
* Zkontrolujte konkrétní položky podrobně.
* Najít další data protokolu systému, které se týkají stejný požadavek uživatele (má stejné ID operace).
* Konfigurace stránky uložte jako oblíbenou položku.

> [!NOTE]
>Pokud vaše aplikace odešle velké množství dat a používáte Application Insights SDK pro verze technologie ASP.NET 2.0.0-beta3 nebo novější, *adaptivního vzorkování* funkce může pracovat a odesílat pouze část vaší telemetrie. [Přečtěte si další informace o vzorkování.](../../azure-monitor/app/sampling.md)
>

## <a name="troubleshooting"></a>Řešení potíží
### <a name="how-do-i-do-this-for-java"></a>Jak to udělám to pro Javu?
Použití [adaptéry protokolu Java](../../azure-monitor/app/java-trace-logs.md).

### <a name="theres-no-application-insights-option-on-the-project-context-menu"></a>Neexistuje žádná možnost Application Insights v místní nabídce projektu
* Ujistěte se, že nainstalované nástroje služby Application Insights na vývojovém počítači. V sadě Visual Studio **nástroje** > **rozšíření a aktualizace**, vyhledejte **nástroje Application Insights**. Pokud se nejedná o **nainstalováno** otevřenou kartou **Online** kartu a nainstalujte ho.
* To může být typ projektu, který nepodporuje nástroje Application Insights. Použití [ruční instalace](#manual-installation).

### <a name="theres-no-log-adapter-option-in-the-configuration-tool"></a>Neexistuje žádná možnost adaptér protokolu v nástroji pro konfiguraci
* Nejprve nainstalujte rozhraní protokolování.
* Pokud používáte System.Diagnostics.Trace, ujistěte se, že používáte [gurovaný *web.config*](https://msdn.microsoft.com/library/system.diagnostics.eventlogtracelistener.aspx).
* Ujistěte se, že máte nejnovější verzi služby Application Insights. V sadě Visual Studio, přejděte na **nástroje** > **rozšíření a aktualizace**a otevřete **aktualizace** kartu. Pokud **Developer Analytics Tools** je, vyberte ji a aktualizujte ji.

### <a name="emptykey"></a>Zobrazí se chybová zpráva "Instrumentační klíč nemůže být prázdný"
Pravděpodobně jste nainstalovali balíček Nuget adaptér protokolování bez instalace služby Application Insights. V Průzkumníku řešení klikněte pravým tlačítkem na *soubor ApplicationInsights.config*a vyberte **aktualizace Application Insights**. Budete vyzváni k přihlášení k Azure a vytvořte prostředek Application Insights nebo znovu použít existující. Který by měla tento problém vyřešit.

### <a name="i-can-see-traces-but-not-other-events-in-diagnostic-search"></a>Zobrazí trasování, ale ne další události v diagnostickém vyhledávání
Může trvat nějakou dobu všechny události a žádosti o získání prostřednictvím kanálu.

### <a name="limits"></a>Jaká data se uchovávají?
Několik faktorů vliv na množství dat, která se uchovávají. Další informace najdete v tématu [omezení](../../azure-monitor/app/api-custom-events-metrics.md#limits) na stránce metriky event zákazníka.

### <a name="i-dont-see-some-log-entries-that-i-expected"></a>Nevidím některé položky protokolu, které můžu očekávat
Pokud vaše aplikace odešle rozsáhlých množství dat a používáte Application Insights SDK pro verze technologie ASP.NET 2.0.0-beta3 nebo novější, může funkce adaptivního vzorkování pracovat a odesílat pouze část vaší telemetrie. [Přečtěte si další informace o vzorkování.](../../azure-monitor/app/sampling.md)

## <a name="add"></a>Další kroky

* [Diagnóza chyb a výjimek v ASP.NET][exceptions]
* [Další informace o vyhledávání][diagnostic]
* [Nastavení dostupnosti a rychlosti odezvy testy][availability]
* [Řešení potíží][qna]

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[exceptions]: asp-net-exceptions.md
[portal]: https://portal.azure.com/
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[start]: ../../azure-monitor/app/app-insights-overview.md