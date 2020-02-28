---
title: Závislost sledování ve službě Azure Application Insights | Dokumentace Microsoftu
description: Monitorování volání závislostí z vaší místní nebo Microsoft Azure webové aplikace s využitím Application Insights.
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: 8fb1550a3f1d4b3336384139b049b60e23e648d7
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77666237"
---
# <a name="dependency-tracking-in-azure-application-insights"></a>Sledování závislostí v Azure Application Insights 

*Závislost* je externí komponenta, která je volána vaší aplikací. Obvykle se jedná o službu volána pomocí protokolu HTTP, nebo databázi nebo systému souborů. [Application Insights](../../azure-monitor/app/app-insights-overview.md) měří dobu trvání volání závislostí, bez ohledu na to, jestli se nedaří, nebo ne, spolu s dalšími informacemi, jako je název závislosti a tak dále. Můžete prozkoumat konkrétní volání závislosti a korelovat je s požadavky a výjimkami.

## <a name="automatically-tracked-dependencies"></a>Automaticky sledované závislosti

Application Insights sady SDK pro .NET a .NET Core dodávané s `DependencyTrackingTelemetryModule` je modul telemetrie, který automaticky shromažďuje závislosti. Tato kolekce závislostí je automaticky povolená pro [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) a [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) aplikace, pokud je nakonfigurovaná podle propojených oficiálních dokumentů. `DependencyTrackingTelemetryModule` se dodává jako [Tento](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) balíček NuGet a automaticky se připravuje při použití některého z balíčků NuGet `Microsoft.ApplicationInsights.Web` nebo `Microsoft.ApplicationInsights.AspNetCore`.

 `DependencyTrackingTelemetryModule` aktuálně sleduje následující závislosti automaticky:

|Závislosti |Podrobnosti|
|---------------|-------|
|Http/https | Místní nebo vzdálené volání http/https |
|Volání WCF| Automaticky sledováno pouze v případě, že jsou použity vazby založené na protokolu HTTP.|
|SQL | Volání prováděná pomocí `SqlClient`. Podívejte se na [Tento](#advanced-sql-tracking-to-get-full-sql-query) záznam pro zachycení dotazu SQL.  |
|[Azure Storage (objekt blob, tabulka, fronta)](https://www.nuget.org/packages/WindowsAzure.Storage/) | Volání prováděná klientem Azure Storage. |
|[Klientská sada SDK pro EventHub](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | Verze 1.1.0 a vyšší. |
|[Klientská sada SDK pro ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| Verze 3.0.0 a vyšší. |
|Azure Cosmos DB | Automaticky sledováno pouze v případě, že se používá protokol HTTP/HTTPS. Režim TCP nebude zachycena Application Insights. |

Pokud nemůžete najít závislost nebo používáte jinou sadu SDK, ujistěte se, že je v seznamu [automaticky shromážděných závislostí](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies). Pokud není závislost automaticky shromažďována, můžete ji stále sledovat ručně pomocí [volání sledování závislostí](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency).

## <a name="setup-automatic-dependency-tracking-in-console-apps"></a>Nastavení automatického sledování závislosti v konzolových aplikacích

Pokud chcete automaticky sledovat závislosti z konzolových aplikací .NET/.NET Core, nainstalujte balíček NuGet `Microsoft.ApplicationInsights.DependencyCollector`a inicializujte `DependencyTrackingTelemetryModule` následujícím způsobem:

```csharp
    DependencyTrackingTelemetryModule depModule = new DependencyTrackingTelemetryModule();
    depModule.Initialize(TelemetryConfiguration.Active);
```

### <a name="how-automatic-dependency-monitoring-works"></a>Jak funguje automatické monitorování závislostí?

Závislosti se automaticky shromažďují pomocí jednoho z následujících postupů:

* Použití instrumentace bajtového kódu kolem metod výběru. (InstrumentationEngine buď z StatusMonitor nebo rozšíření webové aplikace Azure)
* Zpětná volání EventSource
* DiagnosticSource zpětná volání (v nejnovějších sadách SDK .NET/.NET Core)

## <a name="manually-tracking-dependencies"></a>Ruční sledování závislostí

Níže jsou uvedeny některé příklady závislostí, které nejsou shromažďovány automaticky, a proto vyžadují ruční sledování.

* Azure Cosmos DB se sleduje automaticky jenom v případě, že se používá [protokol HTTP/HTTPS](../../cosmos-db/performance-tips.md#networking) . Režim TCP nebude zachycena Application Insights.
* Redis

Pro tyto závislosti, které nejsou automaticky shromažďovány sadou SDK, je můžete sledovat ručně pomocí [rozhraní TrackDependency API](api-custom-events-metrics.md#trackdependency) , které jsou používány standardními moduly pro automatické shromažďování dat.

Například pokud vytváření kódu pomocí sestavení, které jste sami nenapsali může čas všechna volání, a zjistěte, jaké příspěvek, odešle vaše doby odezvy. Chcete-li tato data zobrazit v grafech závislostí v Application Insights, odešlete je pomocí `TrackDependency`.

```csharp

    var startTime = DateTime.UtcNow;
    var timer = System.Diagnostics.Stopwatch.StartNew();
    try
    {
        // making dependency call
        success = dependency.Call();
    }
    finally
    {
        timer.Stop();
        telemetryClient.TrackDependency("myDependencyType", "myDependencyCall", "myDependencyData",  startTime, timer.Elapsed, success);
    }
```

Alternativně `TelemetryClient` poskytuje rozšiřující metody `StartOperation` a `StopOperation`, které se dají použít k ručnímu sledování závislostí, jak je vidět [tady](custom-operations-tracking.md#outgoing-dependencies-tracking) .

Pokud chcete přepnout na standardní modul sledování závislostí, odeberte odkaz na DependencyTrackingTelemetryModule v [souboru ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) pro aplikace ASP.NET. V případě aplikací ASP.NET Core postupujte podle [pokynů.](asp-net-core.md#configuring-or-removing-default-telemetrymodules)

## <a name="tracking-ajax-calls-from-web-pages"></a>Sledování volání AJAX z webových stránek

Pro webové stránky Application Insights JavaScript SDK automaticky shromáždí volání AJAX jako závislosti.

## <a name="advanced-sql-tracking-to-get-full-sql-query"></a>Rozšířené sledování SQL pro získání úplného dotazu SQL

V případě volání SQL se název serveru a databáze vždycky shromažďují a ukládají jako název shromážděných `DependencyTelemetry`. K dispozici je další pole s názvem data, které může obsahovat úplný text dotazu SQL.

Pro ASP.NET Core aplikace není k dispozici žádný další krok potřebný k získání úplného dotazu SQL.

V případě aplikací ASP.NET je úplný dotaz SQL shromážděn s využitím instrumentace kódu, který vyžaduje instrumentaci jádra. Další kroky specifické pro platformu, jak je popsáno níže, jsou povinné.

| Platforma | Krok (y) potřebný k získání úplného dotazu SQL |
| --- | --- |
| Azure Web App |V ovládacím panelu webové aplikace otevřete okno [Application Insights](../../azure-monitor/app/azure-web-apps.md) a povolte příkazy SQL pod položkou .NET. |
| Server IIS (virtuální počítač Azure, on-Prem atd.) | Pomocí modulu Monitorování stavu PowerShellu [nainstalujte modul instrumentace](../../azure-monitor/app/status-monitor-v2-api-enable-instrumentation-engine.md) a restartujte službu IIS. |
| Cloudová služba Azure | Přidat [úlohu po spuštění pro instalaci StatusMonitor](../../azure-monitor/app/cloudservices.md#set-up-status-monitor-to-collect-full-sql-queries-optional) <br> Vaše aplikace by se měla připojit k ApplicationInsights SDK v době sestavení instalací balíčků NuGet pro [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) nebo [aplikace ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) . |
| IIS Express | Nepodporuje se

Ve výše uvedených případech je správným způsobem, jak ověřit, že je modul instrumentace správně nainstalován, ověření, že verze sady SDK shromážděná `DependencyTelemetry` je "rddp". ' rdddsd ' nebo ' rddf ' označuje závislosti, které jsou shromažďovány prostřednictvím zpětného volání DiagnosticSource nebo EventSource, takže plný dotaz SQL nebude zachycen.

## <a name="where-to-find-dependency-data"></a>Kde najít data závislostí

* [Mapa aplikace](app-map.md) vizualizuje závislosti mezi vaší aplikací a sousedními komponentami.
* [Diagnostika transakcí](transaction-diagnostics.md) zobrazuje jednotná a korelujovaná data serveru.
* [Karta prohlížeče](javascript.md) zobrazuje volání AJAX z prohlížečů uživatelů.
* Kliknutím na z pomalých nebo neúspěšných žádostí zkontrolujete svá volání závislostí.
* [Analýza](#logs-analytics) se dá použít k dotazování na data závislostí.

## <a name="diagnosis"></a>Diagnostika pomalých požadavků

Každá událost žádosti je přidružená k voláním závislosti, výjimkám a dalším událostem, které jsou sledovány během zpracování požadavku vaší aplikací. Takže pokud některé požadavky nefungují správně, můžete zjistit, zda se jedná z důvodu pomalé odezvy ze závislosti.

### <a name="tracing-from-requests-to-dependencies"></a>Trasování požadavků závislostí

Otevřete kartu **výkon** a v horní části vedle operací přejděte na kartu **závislosti** .

V části celkové klikněte na **Název závislosti** . Po výběru závislosti se zobrazí graf s distribucí trvání této závislosti na pravé straně.

![Na kartě výkon klikněte v horní části na kartu závislost a pak na název závislosti v grafu.](./media/asp-net-dependencies/2-perf-dependencies.png)

Kliknutím na tlačítko modré **ukázky** v pravém dolním rohu a pak na ukázce zobrazíte podrobnosti o koncových transakcích.

![Kliknutím na ukázku zobrazíte podrobnosti o kompletní transakci.](./media/asp-net-dependencies/3-end-to-end.png)

### <a name="profile-your-live-site"></a>Profil živého webu

Představu kde čas prochází? [Application Insights Profiler](../../azure-monitor/app/profiler.md) sleduje volání http na svůj živý web a zobrazí funkce v kódu, které trvaly nejdelší čas.

## <a name="failed-requests"></a>Neúspěšné žádosti

Neúspěšné požadavky může být také přidružen neúspěšných volání závislostí.

Na levé straně můžeme přejít na kartu **chyby** a potom kliknout na kartu **závislosti** v horní části.

![Klikněte na graf neúspěšných žádostí](./media/asp-net-dependencies/4-fail.png)

Tady budete moct zobrazit počet neúspěšných závislostí. Pokud chcete získat další podrobnosti o neúspěšném výskytu, zkuste kliknout na název závislosti v dolní tabulce. Můžete kliknout na tlačítko modré **závislosti** v pravém dolním rohu a získat podrobnosti o koncových transakcích.

## <a name="logs-analytics"></a>Protokoly (analýza)

Závislosti můžete sledovat v [dotazovacím jazyku Kusto](/azure/kusto/query/). Zde je několik příkladů:

* Najdete všechna neúspěšná volání:

``` Kusto

    dependencies | where success != "True" | take 10
```

* Najděte volání AJAX:

``` Kusto

    dependencies | where client_Type == "Browser" | take 10
```

* Vyhledejte žádosti spotřebují volání závislostí:

``` Kusto

    dependencies
    | where timestamp > ago(1d) and  client_Type != "Browser"
    | join (requests | where timestamp > ago(1d))
      on operation_Id  
```


* Najít volání AJAX přidružený k zobrazení stránek:

``` Kusto 

    dependencies
    | where timestamp > ago(1d) and  client_Type == "Browser"
    | join (browserTimings | where timestamp > ago(1d))
      on operation_Id
```

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="how-does-automatic-dependency-collector-report-failed-calls-to-dependencies"></a>*Jak automatické shromažďování závislostí hlásí neúspěšná volání závislostí?*

* Neúspěšná volání závislostí budou mít pole Success nastaveno na hodnotu false. `DependencyTrackingTelemetryModule` nehlásí `ExceptionTelemetry`. Úplný datový model pro závislost je popsán [zde](data-model-dependency-telemetry.md).

## <a name="open-source-sdk"></a>Open-Source sada SDK
Stejně jako každý Application Insights SDK je modul pro shromažďování závislostí také open source. Přečtěte si kód, přispívat k němu nebo nahlaste problémy v [oficiálním úložišti GitHub](https://github.com/Microsoft/ApplicationInsights-dotnet-server).

## <a name="next-steps"></a>Další kroky

* [Výjimky](../../azure-monitor/app/asp-net-exceptions.md)
* [Data stránky uživatele &](../../azure-monitor/app/javascript.md)
* [Dostupnost](../../azure-monitor/app/monitor-web-app-availability.md)
