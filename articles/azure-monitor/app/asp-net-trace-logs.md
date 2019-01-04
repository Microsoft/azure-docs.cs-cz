---
title: Prozkoumejte protokoly trasování .NET ve službě Application Insights
description: Hledání protokolů generovaných s trasování, NLog a Log4Net.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0c2a084f-6e71-467b-a6aa-4ab222f17153
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/03/2017
ms.author: mbullwin
ms.openlocfilehash: b5cc0004edf105d8875ff83240b659e210b18e41
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54019941"
---
# <a name="explore-net-trace-logs-in-application-insights"></a>Prozkoumejte protokoly trasování .NET ve službě Application Insights
Pokud chcete použít NLog, log4Net nebo System.Diagnostics.Trace pro diagnostické trasování v aplikaci ASP.NET, může mít vaše protokoly odeslané do [Azure Application Insights][start], kde můžete prozkoumat a hledat je. Protokoly se sloučí s další telemetrická data přicházející z vaší aplikace tak, aby identifikovat trasování přidružené k obsluze jednotlivých požadavků uživatele a korelovat je s dalšími události a sestavy výjimek.

> [!NOTE]
> Je potřeba zachycení modulu protokolu? Je užitečné adaptér protokolovacích nástrojů 3rd třetích stran, ale pokud ještě nepoužíváte NLog, log4Net nebo System.Diagnostics.Trace, vezměte v úvahu pouze volání [Application Insights použitím metod TrackTrace()](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) přímo.
>
>

## <a name="install-logging-on-your-app"></a>Nainstalujte protokolování v aplikaci
Instalaci zvolený protokolovacího rozhraní ve vašem projektu. Výsledkem by měl být záznam v souboru app.config nebo web.config.

Pokud používáte System.Diagnostics.Trace, budete muset přidat záznam do souboru web.config:

```XML

    <configuration>
     <system.diagnostics>
       <trace autoflush="false" indentsize="4">
         <listeners>
           <add name="myListener"
             type="System.Diagnostics.TextWriterTraceListener"
             initializeData="TextWriterOutput.log" />
           <remove name="Default" />
         </listeners>
       </trace>
     </system.diagnostics>
   </configuration>
```
## <a name="configure-application-insights-to-collect-logs"></a>Nakonfigurovat Application Insights na shromažďování protokolů
**[Přidejte Application Insights do projektu](../../azure-monitor/app/asp-net.md)**  Pokud jste tak ještě neučinili. Zobrazí se vám možnost zahrnout kolektoru protokolů.

Nebo **konfigurovat Application Insights** kliknutím pravým tlačítkem myši na projekt v Průzkumníku řešení. Vyberte možnost **konfiguraci kolekce trasování**.

*Žádná možnost Application Insights nabídky nebo protokolu kolekcí?* Zkuste [řešení potíží s](#troubleshooting).

## <a name="manual-installation"></a>Ruční instalace
Tuto metodu použijte, pokud typ projektu nepodporuje Instalační služby Application Insights (třeba Windows desktopové projekt).

1. Pokud máte v plánu používat log4Net nebo NLog, nainstalujte ji do vašeho projektu.
2. V Průzkumníku řešení klikněte pravým tlačítkem myši na projekt a zvolte **spravovat balíčky NuGet**.
3. Vyhledání Application Insights
4. Vyberte příslušný balíček – jeden z:

   * Microsoft.ApplicationInsights.TraceListener (pro zachycení volání System.Diagnostics.Trace)
   * Microsoft.ApplicationInsights.EventSourceListener (k zachycení událostí EventSource)
   * Microsoft.ApplicationInsights.EtwCollector (k zachycení událostí trasování událostí pro Windows)
   * Microsoft.ApplicationInsights.NLogTarget
   * Microsoft.ApplicationInsights.Log4NetAppender

Balíček NuGet nainstaluje potřebná sestavení a taky změní web.config nebo app.config.

## <a name="insert-diagnostic-log-calls"></a>Vložit diagnostický protokol volání
Pokud používáte System.Diagnostics.Trace, typické volání by byl:

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

Pokud dáváte přednost, log4net nebo NLog:

    logger.Warn("Slow response - database01");

## <a name="using-eventsource-events"></a>Použití událostí EventSource
Můžete nakonfigurovat [System.Diagnostics.Tracing.EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) události k odeslání do Application Insights jako trasování. Nejdřív nainstalujte `Microsoft.ApplicationInsights.EventSourceListener` balíček NuGet. Upravte `TelemetryModules` část [soubor ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) souboru.

```xml
    <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
      <Sources>
        <Add Name="MyCompany" Level="Verbose" />
      </Sources>
    </Add>
```

Pro každý zdroj můžete nastavit následující parametry:
 * `Name` Určuje název EventSource ke shromažďování.
 * `Level` Určuje úroveň protokolování pro shromažďování. Může být jedna z `Critical`, `Error`, `Informational`, `LogAlways`, `Verbose`, `Warning`.
 * `Keywords` (Volitelné) Určuje celočíselnou hodnotu kombinací klíčová slova používat.

## <a name="using-diagnosticsource-events"></a>Použití DiagnosticSource událostí
Můžete nakonfigurovat [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) události k odeslání do Application Insights jako trasování. Nejdřív nainstalujte [ `Microsoft.ApplicationInsights.DiagnosticSourceListener` ](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener) balíček NuGet. Upravte `TelemetryModules` část [soubor ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) souboru.

```xml
    <Add Type="Microsoft.ApplicationInsights.DiagnosticSourceListener.DiagnosticSourceTelemetryModule, Microsoft.ApplicationInsights.DiagnosticSourceListener">
      <Sources>
        <Add Name="MyDiagnosticSourceName" />
      </Sources>
    </Add>
```

Pro každý DiagnosticSource chcete trasovat, přidejte položku s `Name` atribut nastavte na název vaší DiagnosticSource.

## <a name="using-etw-events"></a>Pomocí trasování událostí pro Windows
Můžete nakonfigurovat události trasování událostí pro Windows k odeslání do Application Insights jako trasování. Nejdřív nainstalujte `Microsoft.ApplicationInsights.EtwCollector` balíček NuGet. Upravte `TelemetryModules` část [soubor ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) souboru.

> [!NOTE] 
> Události trasování událostí pro Windows můžou shromažďují, pouze pokud je proces hostování SDK spuštěn pod identitou, která je členem skupiny "Performance Log Users" nebo správci.

```xml
    <Add Type="Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule, Microsoft.ApplicationInsights.EtwCollector">
      <Sources>
        <Add ProviderName="MyCompanyEventSourceName" Level="Verbose" />
      </Sources>
    </Add>
```

Pro každý zdroj můžete nastavit následující parametry:
 * `ProviderName` je název zprostředkovatele trasování událostí pro Windows ke shromažďování.
 * `ProviderGuid` Určuje identifikátor GUID zprostředkovatele trasování událostí pro Windows ke shromažďování, je možné místo `ProviderName`.
 * `Level` Nastaví úroveň protokolování pro shromažďování. Může být jedna z `Critical`, `Error`, `Informational`, `LogAlways`, `Verbose`, `Warning`.
 * `Keywords` (Volitelné) nastaví celočíselnou hodnotu kombinací – klíčové slovo lze použít.

## <a name="using-the-trace-api-directly"></a>Přímo pomocí trasování rozhraní API
Trasování Application Insights API můžete volat přímo. Adaptéry protokolování pomocí tohoto rozhraní API.

Příklad:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

Výhodou TrackTrace je umístit relativně dlouho data ve zprávě. Například může kódování následných dat existuje.

Kromě toho můžete přidat úroveň závažnosti zprávě. A stejně jako další telemetrická data, můžete přidat hodnoty vlastností, které mohou používat ke filtr nebo hledaný pro různé skupiny trasování. Příklad:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });

To by umožnilo, v [hledání][diagnostic]můžete snadno vyfiltrovat všechny zprávy konkrétní závažnosti úrovně týkající se konkrétní databáze.

## <a name="explore-your-logs"></a>Prozkoumejte protokoly
Spouštění vaší aplikace, buď v režimu ladění, nebo můžete nasadit za provozu.

V okně Přehled vaší aplikace v [portálu služby Application Insights][portal], zvolte [hledání][diagnostic].

Je to možné, například:

* Filtrovat v protokolu trasování nebo na položky s určitými vlastnostmi
* Zkontrolujte konkrétní položky podrobně.
* Najít další telemetrická data týkající se stejný požadavek uživatele (to znamená s stejným OperationId)
* Konfiguraci této stránky uložit jako oblíbenou položku

> [!NOTE]
> **Vzorkování.** Pokud vaše aplikace odešle velké množství dat a používáte Application Insights SDK pro verze technologie ASP.NET 2.0.0-beta3 nebo novější, může funkce adaptivního vzorkování pracovat a odesílat pouze procento vaší telemetrie. [Přečtěte si další informace o vzorkování.](../../azure-monitor/app/sampling.md)
>
>

## <a name="next-steps"></a>Další postup
[Diagnóza chyb a výjimek v ASP.NET][exceptions]

[Další informace o vyhledávání][diagnostic].

## <a name="troubleshooting"></a>Řešení potíží
### <a name="how-do-i-do-this-for-java"></a>Jak to udělám to pro Javu?
Použití [adaptéry protokolu Java](../../azure-monitor/app/java-trace-logs.md).

### <a name="theres-no-application-insights-option-on-the-project-context-menu"></a>Neexistuje žádná možnost Application Insights v místní nabídce projektu
* Zkontrolujte, že jsou nainstalované nástroje služby Application Insights na tomto počítači vývoje. V nabídce nástrojů sady Visual Studio, rozšíření a aktualizace vyhledejte nástroje Application Insights. Pokud není nainstalováno kartě, otevřete kartu Online a nainstalujte ho.
* Může se jednat typ projektu nepodporuje nástroje Application Insights. Použití [ruční instalace](#manual-installation).

### <a name="no-log-adapter-option-in-the-configuration-tool"></a>Žádná možnost pro adaptér protokolu v nástroji pro konfiguraci
* Je nutné nejprve nainstalovat rozhraní protokolování.
* Pokud používáte System.Diagnostics.Trace, ujistěte se, že jste [nakonfigurované v `web.config` ](https://msdn.microsoft.com/library/system.diagnostics.eventlogtracelistener.aspx).
* Máte mít nejnovější verzi služby Application Insights? V sadě Visual Studio **nástroje** nabídce zvolte **rozšíření a aktualizace**a otevřete **aktualizace** kartu. Pokud nástroje Developer Analytics tools existují, klikněte na tlačítko ji aktualizovat.

### <a name="emptykey"></a>Dojde k chybě "Instrumentační klíč nemůže být prázdný"
Vypadá to, jste nainstalovali balíček Nuget adaptér protokolování bez instalace služby Application Insights.

V Průzkumníku řešení klikněte pravým tlačítkem na `ApplicationInsights.config` a zvolte **aktualizace Application Insights**. Zobrazí se dialogové okno, které vás zve k přihlášení k Azure a vytvořte prostředek Application Insights, nebo znovu použijte existující. Která by ho opravit.

### <a name="i-can-see-traces-in-diagnostic-search-but-not-the-other-events"></a>Zobrazí trasování v diagnostickém vyhledávání, ale ne jiných událostí
Někdy může trvat nějakou dobu všechny události a žádosti o získání prostřednictvím kanálu.

### <a name="limits"></a>Jaká data se uchovávají?
Několik faktorů, které ovlivňují objem data se uchovávají. Zobrazit [omezení](../../azure-monitor/app/api-custom-events-metrics.md#limits) část stránky zákazníka události metriky pro další informace. 

### <a name="im-not-seeing-some-of-the-log-entries-that-i-expect"></a>Mi nezobrazují některé položky protokolu, které můžu očekávat
Pokud vaše aplikace odešle velké množství dat a používáte Application Insights SDK pro verze technologie ASP.NET 2.0.0-beta3 nebo novější, může funkce adaptivního vzorkování pracovat a odesílat pouze procento vaší telemetrie. [Přečtěte si další informace o vzorkování.](../../azure-monitor/app/sampling.md)

## <a name="add"></a>Další kroky
* [Nastavení dostupnosti a rychlosti odezvy testy][availability]
* [Řešení potíží][qna]

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[exceptions]: asp-net-exceptions.md
[portal]: https://portal.azure.com/
[qna]: ../../application-insights/app-insights-troubleshoot-faq.md
[start]: ../../application-insights/app-insights-overview.md
