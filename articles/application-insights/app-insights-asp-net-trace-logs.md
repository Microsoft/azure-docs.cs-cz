---
title: Prozkoumejte protokoly trasování .NET ve službě Application Insights
description: Vygenerovat pomocí trasování, NLog a Log4Net protokolů hledání.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0c2a084f-6e71-467b-a6aa-4ab222f17153
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/03/2017
ms.author: mbullwin
ms.openlocfilehash: 574b11f9ba38bda775610f2f9e90fbb2d2b05868
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="explore-net-trace-logs-in-application-insights"></a>Prozkoumejte protokoly trasování .NET ve službě Application Insights
Pokud používáte NLog log4Net nebo System.Diagnostics.Trace pro diagnostické trasování v aplikaci ASP.NET, může mít vaše protokoly posílá [Azure Application Insights][start], kde můžete prozkoumat a vyhledávání je. Protokoly bude sloučen s další telemetrií pocházející z vaší aplikace, tak, aby mohli identifikovat trasování přidružené údržby každý požadavek uživatele a jejich korelující s jinými události a sestavy výjimek.

> [!NOTE]
> Je nutné modul zachycení protokolu? Je užitečné adaptér pro protokolovacích nástrojů 3. stran, ale pokud už nepoužíváte NLog, log4Net nebo System.Diagnostics.Trace, zvažte právě volání [Application Insights TrackTrace()](app-insights-api-custom-events-metrics.md#tracktrace) přímo.
>
>

## <a name="install-logging-on-your-app"></a>Nainstalujte protokolování v aplikaci
Nainstalujte rozhraní framework vaši zvolenou protokolování ve vašem projektu. Výsledkem by mělo být položku v souboru app.config nebo web.config.

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
## <a name="configure-application-insights-to-collect-logs"></a>Konfigurovat Application Insights se mají shromažďovat protokoly
**[Do projektu přidejte Application Insights](app-insights-asp-net.md)**  Pokud jste to neudělali, ještě. Zobrazí se možnost zahrnout kolektoru protokolů.

Nebo **konfigurovat Application Insights** kliknutím pravým tlačítkem na projekt v Průzkumníku řešení. Vyberte možnost **konfigurace sběru trasovacích**.

*Žádná možnost Application Insights nabídky nebo protokolu kolekce?* Zkuste [řešení potíží s](#troubleshooting).

## <a name="manual-installation"></a>Ruční instalace
Tuto metodu použijte, pokud váš typ projektu není podporován Instalační služby Application Insights (například Windows desktop projektu).

1. Pokud máte v plánu používat log4Net nebo NLog, nainstalujte ho do projektu.
2. V Průzkumníku řešení klikněte pravým tlačítkem na projekt a zvolte **spravovat balíčky NuGet**.
3. Vyhledání Application Insights
4. Vyberte odpovídající balíček – jeden z:

   * Microsoft.ApplicationInsights.TraceListener (pro zachycení System.Diagnostics.Trace volání)
   * Microsoft.ApplicationInsights.EventSourceListener (k zachycení událostí EventSource)
   * Microsoft.ApplicationInsights.EtwListener (k zachycení událostí trasování událostí pro Windows)
   * Microsoft.ApplicationInsights.NLogTarget
   * Microsoft.ApplicationInsights.Log4NetAppender

Balíček NuGet nainstaluje potřebné sestavení a také upraví soubor web.config nebo app.config.

## <a name="insert-diagnostic-log-calls"></a>Vložit volání protokolů diagnostiky
Pokud používáte System.Diagnostics.Trace, typické volání by byl:

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

Pokud dáváte přednost log4net nebo NLog:

    logger.Warn("Slow response - database01");

## <a name="using-eventsource-events"></a>Pomocí událostí EventSource
Můžete nakonfigurovat [System.Diagnostics.Tracing.EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) odeslání do služby Application Insights jako trasování událostí. Nejdřív nainstalujte `Microsoft.ApplicationInsights.EventSourceListener` balíček NuGet. Upravte `TelemetryModules` části [souboru ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) souboru.

```xml
    <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
      <Sources>
        <Add Name="MyCompany" Level="Verbose" />
      </Sources>
    </Add>
```

Pro každý zdroj můžete nastavit následující parametry:
 * `Name` Určuje název EventSource ke shromažďování.
 * `Level` Určuje úroveň protokolování ke shromažďování. Může být jedna z `Critical`, `Error`, `Informational`, `LogAlways`, `Verbose`, `Warning`.
 * `Keywords` (Volitelné) Určuje celočíselnou hodnotu kombinace klíčových slov používat.

## <a name="using-diagnosticsource-events"></a>Pomocí DiagnosticSource události
Můžete nakonfigurovat [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) odeslání do služby Application Insights jako trasování událostí. Nejdřív nainstalujte [ `Microsoft.ApplicationInsights.DiagnosticSourceListener` ](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener) balíček NuGet. Upravte `TelemetryModules` části [souboru ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) souboru.

```xml
    <Add Type="Microsoft.ApplicationInsights.DiagnosticSourceListener.DiagnosticSourceTelemetryModule, Microsoft.ApplicationInsights.DiagnosticSourceListener">
      <Sources>
        <Add Name="MyDiagnosticSourceName" />
      </Sources>
    </Add>
```

Pro každý DiagnosticSource chcete trasovat, přidat položku s `Name` atributu nastavena na název vaší DiagnosticSource.

## <a name="using-etw-events"></a>Pomocí události trasování událostí pro Windows
Můžete nakonfigurovat události trasování událostí pro odeslání do Application Insights jako trasování. Nejdřív nainstalujte `Microsoft.ApplicationInsights.EtwCollector` balíček NuGet. Upravte `TelemetryModules` části [souboru ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) souboru.

> [!NOTE] 
> Události trasování událostí pro Windows se můžou shromažďovat pouze pokud proces hostování sady SDK je spuštěný pod identitou, která je členem skupiny "Uživatelé protokolu výkonu" nebo správci.

```xml
    <Add Type="Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule, Microsoft.ApplicationInsights.EtwCollector">
      <Sources>
        <Add ProviderName="MyCompanyEventSourceName" Level="Verbose" />
      </Sources>
    </Add>
```

Pro každý zdroj můžete nastavit následující parametry:
 * `ProviderName` je název zprostředkovatele trasování událostí pro Windows, který má shromažďovat.
 * `ProviderGuid` Určuje identifikátor GUID zprostředkovatele trasování událostí pro Windows ke shromažďování, můžete použít místo `ProviderName`.
 * `Level` Nastaví úroveň protokolování ke shromažďování. Může být jedna z `Critical`, `Error`, `Informational`, `LogAlways`, `Verbose`, `Warning`.
 * `Keywords` (Volitelné) nastaví celé číslo – klíčové slovo kombinace používat.

## <a name="using-the-trace-api-directly"></a>Přímo pomocí trasování rozhraní API
Trasování Application Insights API můžete volat přímo. Toto rozhraní API použijte adaptéry protokolování.

Příklad:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

Výhodou TrackTrace je, že můžete ukládat poměrně dlouho data ve zprávě. Například může zakódovat následných dat existuje.

Kromě toho můžete přidat úroveň závažnosti na zprávu. A, podobně jako ostatní telemetrických dat, můžete přidat hodnoty vlastností, které můžete použít k lepšímu filtru nebo vyhledávání pro různé skupiny trasování. Příklad:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });

To by vás, povolte v [vyhledávání][diagnostic], snadno filtrovat všechny zprávy konkrétní závažnosti úrovně týkající se konkrétní databázi.

## <a name="explore-your-logs"></a>Prozkoumejte protokoly
Spuštění aplikace, buď v režimu ladění, nebo ji nasadit za provozu.

V okně Přehled vaší aplikace v [portálu služby Application Insights][portal], zvolte [vyhledávání][diagnostic].

![Ve službě Application Insights zvolte vyhledávání](./media/app-insights-asp-net-trace-logs/020-diagnostic-search.png)

![Search](./media/app-insights-asp-net-trace-logs/10-diagnostics.png)

Je to možné, například:

* Filtrování v protokolu trasování nebo na položky s konkrétní vlastnosti
* Zkontrolujte konkrétní položku podrobně.
* Najít další telemetrií vztahující se ke stejnému požadavku uživatele (to znamená, se stejnou OperationId)
* Konfiguraci této stránky uložit jako oblíbenou položku

> [!NOTE]
> **Vzorkování.** Pokud vaše aplikace odešle velké množství dat a používáte Application Insights SDK pro verze technologie ASP.NET 2.0.0-beta3 nebo novější, může funkce adaptivního vzorkování pracovat a odesílat pouze procento vaší telemetrie. [Přečtěte si další informace o vzorkování.](app-insights-sampling.md)
>
>

## <a name="next-steps"></a>Další postup
[Diagnostikovat chyby a výjimky technologie ASP.NET][exceptions]

[Další informace o vyhledávání][diagnostic].

## <a name="troubleshooting"></a>Řešení potíží
### <a name="how-do-i-do-this-for-java"></a>Jak se to pro jazyk Java?
Použití [adaptéry protokolu Java](app-insights-java-trace-logs.md).

### <a name="theres-no-application-insights-option-on-the-project-context-menu"></a>Neexistuje žádná možnost Application Insights na místní nabídky projektu
* Kontrola instalace nástrojů Application Insights na tomto počítači pro vývoj. V nabídce Nástroje aplikace Visual Studio, rozšíření a aktualizace vyhledejte nástroje Application Insights. Pokud není na kartě nainstalovaná, otevřete kartu Online a nainstalujte ji.
* To může být typ projektu není podporováno nástrojů Application Insights. Použití [ruční instalace](#manual-installation).

### <a name="no-log-adapter-option-in-the-configuration-tool"></a>Žádná možnost pro adaptér protokolu v konfiguračním nástroji služby
* Musíte nejprve nainstalovat rozhraní protokolování.
* Pokud používáte System.Diagnostics.Trace, ověřte, že je [nakonfigurované v `web.config` ](https://msdn.microsoft.com/library/system.diagnostics.eventlogtracelistener.aspx).
* Mít jste získali nejnovější verzi služby Application Insights? V sadě Visual Studio **nástroje** nabídce zvolte **rozšíření a aktualizace**a otevřete **aktualizace** kartě. Pokud Developer Analytics tools existuje, klikněte na tlačítko Aktualizovat.

### <a name="emptykey"></a>Dojde k chybě "klíč instrumentace nemůže být prázdný"
Zdá se, zda jste nainstalovali balíček Nuget adaptér protokolování bez instalace služby Application Insights.

V Průzkumníku řešení klikněte pravým tlačítkem na `ApplicationInsights.config` a zvolte **aktualizace Application Insights**. Zobrazí dialogové okno se žádostí, můžete se přihlásit k Azure a buď vytvořte prostředek Application Insights, nebo znovu použijte stávající. Který by měl opravte ji.

### <a name="i-can-see-traces-in-diagnostic-search-but-not-the-other-events"></a>Zobrazují se trasování v diagnostické vyhledávání, ale není ostatní události
Někdy může trvat nějakou dobu všechny události a žádosti o získání prostřednictvím kanálu.

### <a name="limits"></a>Množství dat, které se uchovávají?
Několik různých faktorů vliv na množství dat, které jsou zachovány. Najdete v článku [omezení](app-insights-api-custom-events-metrics.md#limits) části stránky zákazníka události metriky pro další informace. 

### <a name="im-not-seeing-some-of-the-log-entries-that-i-expect"></a>Některé položky protokolu, které očekávat nejsou zobrazeny
Pokud vaše aplikace odešle velké množství dat a používáte Application Insights SDK pro verze technologie ASP.NET 2.0.0-beta3 nebo novější, může funkce adaptivního vzorkování pracovat a odesílat pouze procento vaší telemetrie. [Přečtěte si další informace o vzorkování.](app-insights-sampling.md)

## <a name="add"></a>Další kroky
* [Nastavení dostupnosti a odezvy testů][availability]
* [Řešení potíží][qna]

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[portal]: https://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[start]: app-insights-overview.md
