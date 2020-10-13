---
title: Sledování závislostí v Azure Application Insights | Microsoft Docs
description: Monitorování volání závislostí z vaší místní nebo Microsoft Azure webové aplikace s využitím Application Insights.
ms.topic: conceptual
ms.date: 08/26/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: df13042656aa077b30bf144aab0a47d9fc0a0662
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91263925"
---
# <a name="dependency-tracking-in-azure-application-insights"></a>Sledování závislostí v Azure Application Insights 

*Závislost* je komponenta, která je volána vaší aplikací. Obvykle se jedná o službu volanou pomocí HTTP, databázi nebo systém souborů. [Application Insights](./app-insights-overview.md) měří dobu trvání volání závislostí, bez ohledu na to, jestli se nedaří, nebo ne, spolu s dalšími informacemi, jako je název závislosti a tak dále. Můžete prozkoumat konkrétní volání závislosti a korelovat je s požadavky a výjimkami.

## <a name="automatically-tracked-dependencies"></a>Automaticky sledované závislosti

Application Insights sady SDK pro .NET a .NET Core dodávané s nástrojem `DependencyTrackingTelemetryModule` , což je modul telemetrie, který automaticky shromažďuje závislosti. Tato kolekce závislostí je automaticky povolená pro [ASP.NET](./asp-net.md) a [ASP.NET Core](./asp-net-core.md) aplikace, pokud je nakonfigurovaná podle propojených oficiálních dokumentů. `DependencyTrackingTelemetryModule` je dodávána jako [Tento](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) balíček NuGet a při použití některého z balíčků NuGet nebo je automaticky načítán `Microsoft.ApplicationInsights.Web` `Microsoft.ApplicationInsights.AspNetCore` .

 `DependencyTrackingTelemetryModule` v současné době sleduje následující závislosti automaticky:

|Závislosti |Podrobnosti|
|---------------|-------|
|Http/https | Místní nebo vzdálené volání http/https |
|Volání WCF| Automaticky sledováno pouze v případě, že jsou použity vazby založené na protokolu HTTP.|
|SQL | Volání s `SqlClient` . Podívejte se na [Tento](#advanced-sql-tracking-to-get-full-sql-query) záznam pro zachycení dotazu SQL.  |
|[Azure Storage (objekt blob, tabulka, fronta)](https://www.nuget.org/packages/WindowsAzure.Storage/) | Volání prováděná klientem Azure Storage. |
|[Klientská sada SDK pro EventHub](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | Verze 1.1.0 a vyšší. |
|[Klientská sada SDK pro ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| Verze 3.0.0 a vyšší. |
|Azure Cosmos DB | Automaticky sledováno pouze v případě, že se používá protokol HTTP/HTTPS. Režim TCP nebude zachycen Application Insights. |

Pokud nemůžete najít závislost nebo používáte jinou sadu SDK, ujistěte se, že je v seznamu [automaticky shromážděných závislostí](./auto-collect-dependencies.md). Pokud není závislost automaticky shromažďována, můžete ji stále sledovat ručně pomocí [volání sledování závislostí](./api-custom-events-metrics.md#trackdependency).

## <a name="setup-automatic-dependency-tracking-in-console-apps"></a>Nastavení automatického sledování závislosti v konzolových aplikacích

Chcete-li automaticky sledovat závislosti z konzolových aplikací .NET, nainstalujte balíček NuGet `Microsoft.ApplicationInsights.DependencyCollector` a proveďte inicializaci `DependencyTrackingTelemetryModule` následujícím způsobem:

```csharp
    DependencyTrackingTelemetryModule depModule = new DependencyTrackingTelemetryModule();
    depModule.Initialize(TelemetryConfiguration.Active);
```

Pro konzolové aplikace .NET Core TelemetryConfiguration. Active je zastaralá. Informace najdete v [dokumentaci ke službě pracovního procesu](./worker-service.md) a v [dokumentaci k monitorování ASP.NET Core](./asp-net-core.md) .

### <a name="how-automatic-dependency-monitoring-works"></a>Jak funguje automatické monitorování závislostí?

Závislosti se automaticky shromažďují pomocí jednoho z následujících postupů:

* Použití instrumentace bajtového kódu kolem metod výběru. (InstrumentationEngine buď z StatusMonitor nebo rozšíření webové aplikace Azure)
* Zpětná volání EventSource
* DiagnosticSource zpětná volání (v nejnovějších sadách SDK .NET/.NET Core)

## <a name="manually-tracking-dependencies"></a>Ruční sledování závislostí

Níže jsou uvedeny některé příklady závislostí, které nejsou shromažďovány automaticky, a proto vyžadují ruční sledování.

* Azure Cosmos DB se sleduje automaticky jenom v případě, že se používá [protokol HTTP/HTTPS](../../cosmos-db/performance-tips.md#networking) . Režim TCP nebude zachycen Application Insights.
* Redis

Pro tyto závislosti, které nejsou automaticky shromažďovány sadou SDK, je můžete sledovat ručně pomocí [rozhraní TrackDependency API](api-custom-events-metrics.md#trackdependency) , které jsou používány standardními moduly pro automatické shromažďování dat.

Například Pokud sestavíte kód se sestavením, které jste nenapsali sami, mohli byste při všech voláních na něj napřed zjistit, jaký příspěvek vzmá vaše doby odezvy. Chcete-li tato data zobrazit v grafech závislostí v Application Insights, odešlete je pomocí `TrackDependency` .

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

Alternativně `TelemetryClient` poskytuje metody rozšíření `StartOperation` , `StopOperation` které lze použít k ručnímu sledování závislostí, jak je znázorněno [zde](custom-operations-tracking.md#outgoing-dependencies-tracking) .

Pokud chcete přepnout na standardní modul sledování závislostí, odeberte odkaz na DependencyTrackingTelemetryModule v [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) pro aplikace ASP.NET. V případě aplikací ASP.NET Core postupujte podle [pokynů.](asp-net-core.md#configuring-or-removing-default-telemetrymodules)

## <a name="tracking-ajax-calls-from-web-pages"></a>Sledování volání AJAX z webových stránek

Pro webové stránky Application Insights JavaScript SDK automaticky shromáždí volání AJAX jako závislosti.

## <a name="advanced-sql-tracking-to-get-full-sql-query"></a>Rozšířené sledování SQL pro získání úplného dotazu SQL

V případě volání SQL se název serveru a databáze vždycky shromažďují a ukládají se jako název shromážděné služby `DependencyTelemetry` . K dispozici je další pole s názvem data, které může obsahovat úplný text dotazu SQL.

U ASP.NET Corech aplikací se teď vyžaduje, aby se ke kolekci textů SQL mohla vyjádřit pomocí
```csharp
services.ConfigureTelemetryModule<DependencyTrackingTelemetryModule>((module, o) => { module. EnableSqlCommandTextInstrumentation = true; });
```

V případě aplikací ASP.NET je úplný text dotazu SQL shromážděn pomocí instrumentace kódu, která vyžaduje použití modulu instrumentace nebo použití balíčku NuGet [Microsoft. data.](https://www.nuget.org/packages/Microsoft.Data.SqlClient) SqlClient namísto knihovny System. data. SqlClient. Kroky specifické pro platformu pro povolení úplné kolekce dotazů SQL jsou popsané níže:

| Platforma | Krok (y) potřebný k získání úplného dotazu SQL |
| --- | --- |
| Webová aplikace Azure |V ovládacím panelu webové aplikace otevřete okno [Application Insights](../../azure-monitor/app/azure-web-apps.md) a povolte příkazy SQL pod položkou .NET. |
| Server IIS (virtuální počítač Azure, on-Prem atd.) | Pomocí balíčku NuGet [Microsoft. data. SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient) nebo pomocí modulu monitorování stavu PowerShellu [nainstalujte modul instrumentace](../../azure-monitor/app/status-monitor-v2-api-reference.md#enable-instrumentationengine) a restartujte službu IIS. |
| Cloudová služba Azure | Přidat [úlohu po spuštění pro instalaci StatusMonitor](../../azure-monitor/app/cloudservices.md#set-up-status-monitor-to-collect-full-sql-queries-optional) <br> Vaše aplikace by se měla připojit k ApplicationInsights SDK v době sestavení instalací balíčků NuGet pro [ASP.NET](./asp-net.md) nebo [aplikace ASP.NET Core](./asp-net-core.md) . |
| IIS Express | Použijte balíček NuGet [Microsoft. data. SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient) .
| Webové úlohy Azure | Použijte balíček NuGet [Microsoft. data. SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient) .

Kromě výše uvedených kroků specifických pro platformu je **také nutné explicitně vyjádřit výslovný souhlas s povolením shromažďování příkazů SQL** úpravou souboru applicationInsights.config následujícím způsobem:

```xml
<Add Type="Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule, Microsoft.AI.DependencyCollector">
<EnableSqlCommandTextInstrumentation>true</EnableSqlCommandTextInstrumentation>
</Add>
```

Ve výše uvedených případech je správným způsobem, jak ověřit, že je modul instrumentace správně nainstalovaný, ověření, že je shromážděná verze sady SDK `DependencyTelemetry` "rddp". ' rdddsd ' nebo ' rddf ' označuje závislosti, které jsou shromažďovány prostřednictvím zpětného volání DiagnosticSource nebo EventSource, takže plný dotaz SQL nebude zachycen.

## <a name="where-to-find-dependency-data"></a>Kde najít data závislostí

* [Mapa aplikace](app-map.md) vizualizuje závislosti mezi vaší aplikací a sousedními komponentami.
* [Diagnostika transakcí](transaction-diagnostics.md) zobrazuje jednotná a korelujovaná data serveru.
* [Karta prohlížeče](javascript.md) zobrazuje volání AJAX z prohlížečů uživatelů.
* Kliknutím na z pomalých nebo neúspěšných žádostí zkontrolujete svá volání závislostí.
* [Analýza](#logs-analytics) se dá použít k dotazování na data závislostí.

## <a name="diagnose-slow-requests"></a><a name="diagnosis"></a> Diagnostika pomalých požadavků

Každá událost žádosti je přidružená k voláním závislosti, výjimkám a dalším událostem, které jsou sledovány během zpracování požadavku vaší aplikací. Takže pokud některé požadavky nefungují správně, můžete zjistit, zda se jedná z důvodu pomalé odezvy ze závislosti.

### <a name="tracing-from-requests-to-dependencies"></a>Trasování z požadavků na závislosti

Otevřete kartu **výkon** a v horní části vedle operací přejděte na kartu **závislosti** .

V části celkové klikněte na **Název závislosti** . Po výběru závislosti se zobrazí graf s distribucí trvání této závislosti na pravé straně.

![Na kartě výkon klikněte v horní části na kartu závislost a pak na název závislosti v grafu.](./media/asp-net-dependencies/2-perf-dependencies.png)

Kliknutím na tlačítko modré **ukázky** v pravém dolním rohu a pak na ukázce zobrazíte podrobnosti o koncových transakcích.

![Kliknutím na ukázku zobrazíte podrobnosti o kompletní transakci.](./media/asp-net-dependencies/3-end-to-end.png)

### <a name="profile-your-live-site"></a>Profilování živého webu

Neznáte, kde se čas nachází? [Application Insights Profiler](../../azure-monitor/app/profiler.md) sleduje volání http na svůj živý web a zobrazí funkce v kódu, které trvaly nejdelší čas.

## <a name="failed-requests"></a>Neúspěšné požadavky

Neúspěšné požadavky mohou být také přidruženy k neúspěšným voláním závislostí.

Na levé straně můžeme přejít na kartu **chyby** a potom kliknout na kartu **závislosti** v horní části.

![Klikněte na graf neúspěšné požadavky.](./media/asp-net-dependencies/4-fail.png)

Tady budete moct zobrazit počet neúspěšných závislostí. Pokud chcete získat další podrobnosti o neúspěšném výskytu, zkuste kliknout na název závislosti v dolní tabulce. Můžete kliknout na tlačítko modré **závislosti** v pravém dolním rohu a získat podrobnosti o koncových transakcích.

## <a name="logs-analytics"></a>Protokoly (analýza)

Závislosti můžete sledovat v [dotazovacím jazyku Kusto](/azure/kusto/query/). Tady je pár příkladů.

* Vyhledání všech neúspěšných volání závislostí:

``` Kusto

    dependencies | where success != "True" | take 10
```

* Hledání volání AJAX:

``` Kusto

    dependencies | where client_Type == "Browser" | take 10
```

* Vyhledat volání závislostí přidružená k požadavkům:

``` Kusto

    dependencies
    | where timestamp > ago(1d) and  client_Type != "Browser"
    | join (requests | where timestamp > ago(1d))
      on operation_Id  
```


* Najde volání AJAX přidružená k zobrazením stránky:

``` Kusto 

    dependencies
    | where timestamp > ago(1d) and  client_Type == "Browser"
    | join (browserTimings | where timestamp > ago(1d))
      on operation_Id
```

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="how-does-automatic-dependency-collector-report-failed-calls-to-dependencies"></a>*Jak automatické shromažďování závislostí hlásí neúspěšná volání závislostí?*

* Neúspěšná volání závislostí budou mít pole Success nastaveno na hodnotu false. `DependencyTrackingTelemetryModule` neoznamuje `ExceptionTelemetry` . Úplný datový model pro závislost je popsán [zde](data-model-dependency-telemetry.md).

### <a name="how-do-i-calculate-ingestion-latency-for-my-dependency-telemetry"></a>*Návody vypočítat latenci příjmu pro telemetrii mých závislostí?*

```kusto
dependencies
| extend E2EIngestionLatency = ingestion_time() - timestamp 
| extend TimeIngested = ingestion_time()
```

### <a name="how-do-i-determine-the-time-the-dependency-call-was-initiated"></a>*Návody určit čas, kdy bylo volání závislosti iniciováno?*

V zobrazení Log Analytics dotaz `timestamp` představuje okamžik, kdy bylo volání TrackDependency () zahájeno, k němuž dojde ihned po přijetí odpovědi na volání závislosti. Chcete-li vypočítat čas, kdy bylo volání závislostí zahájeno, poberete `timestamp` a odečtete zaznamenané `duration` volání závislosti.

## <a name="open-source-sdk"></a>Open-Source sada SDK
Stejně jako každý Application Insights SDK je modul pro shromažďování závislostí také open source. Přečtěte si kód, přispívat k němu nebo nahlaste problémy v [oficiálním úložišti GitHub](https://github.com/Microsoft/ApplicationInsights-dotnet-server).

## <a name="next-steps"></a>Další kroky

* [Výjimky](./asp-net-exceptions.md)
* [Data stránky uživatele &](./javascript.md)
* [Dostupnost](./monitor-web-app-availability.md)

