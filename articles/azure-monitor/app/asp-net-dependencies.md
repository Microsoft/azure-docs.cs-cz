---
title: Sledování závislostí v přehledech aplikací Azure | Dokumenty společnosti Microsoft
description: Sledujte volání závislostí z místní webové aplikace nebo webové aplikace Microsoft Azure pomocí application insights.
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: 1e30d8036c1fc624d39f027f38e314c6c57360f6
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731496"
---
# <a name="dependency-tracking-in-azure-application-insights"></a>Sledování závislostí v přehledech aplikací Azure 

*Závislost* je externí součást, která je volána vaší aplikací. Obvykle se jedná o službu volanou pomocí protokolu HTTP, databáze nebo systému souborů. [Application Insights](../../azure-monitor/app/app-insights-overview.md) měří dobu trvání volání závislostí, ať už se jedná o selhání nebo ne, spolu s dalšími informacemi, jako je název závislosti a tak dále. Můžete prozkoumat konkrétní volání závislostí a korelovat je s požadavky a výjimky.

## <a name="automatically-tracked-dependencies"></a>Automaticky sledované závislosti

Sady SDK Application Insights pro .NET `DependencyTrackingTelemetryModule` a .NET Core jsou součástí modulu telemetrie, který automaticky shromažďuje závislosti. Tato kolekce závislostí je povolena automaticky pro [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) a [ASP.NET základní](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) aplikace, pokud jsou konfigurovány podle propojených oficiálních dokumentů. `DependencyTrackingTelemetryModule` je dodáván jako [tento](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) balíček NuGet a je automaticky přenesen `Microsoft.ApplicationInsights.Web` `Microsoft.ApplicationInsights.AspNetCore`při použití balíčků NuGet nebo .

 `DependencyTrackingTelemetryModule`v současné době sleduje následující závislosti automaticky:

|Závislosti |Podrobnosti|
|---------------|-------|
|Http/Https | Místní nebo vzdálené volání http/https |
|Volání WCF| Sledovány pouze automaticky, pokud jsou použity vazby založené na protokolu Http.|
|SQL | Hovory uskutečněné s `SqlClient`. Podívejte se na [toto](#advanced-sql-tracking-to-get-full-sql-query) pro zachycení dotazu SQL.  |
|[Úložiště Azure (objekt blob, tabulka, fronta)](https://www.nuget.org/packages/WindowsAzure.Storage/) | Volání uskutečněná s klientem úložiště Azure. |
|[EventHub Client SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | Verze 1.1.0 a vyšší. |
|[Sada ServiceBus klienta SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| Verze 3.0.0 a vyšší. |
|Azure Cosmos DB | Sledováno automaticky pouze v případě, že je použit protokol HTTP/HTTPS. Režim TCP nebude zachycen application insights. |

Pokud vám chybí závislost nebo používáte jinou sadu SDK, ujistěte se, že je v seznamu [automaticky shromážděných závislostí](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies). Pokud závislost není automaticky shromažďována, můžete ji stále sledovat ručně pomocí [volání závislosti sledování](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency).

## <a name="setup-automatic-dependency-tracking-in-console-apps"></a>Nastavení automatického sledování závislostí v konzolových aplikacích

Chcete-li automaticky sledovat závislosti z konzolových aplikací `Microsoft.ApplicationInsights.DependencyCollector`rozhraní .NET, nainstalujte balíček Nuget a inicializujte `DependencyTrackingTelemetryModule` následujícím způsobem:

```csharp
    DependencyTrackingTelemetryModule depModule = new DependencyTrackingTelemetryModule();
    depModule.Initialize(TelemetryConfiguration.Active);
```

Pro aplikace konzoly .NET Core je TelemetryConfiguration.Active zastaralý. Podívejte se na pokyny v [dokumentaci k pracovníslužbě](https://docs.microsoft.com/azure/azure-monitor/app/worker-service) a [ASP.NET základní monitorovací dokumentaci](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)

### <a name="how-automatic-dependency-monitoring-works"></a>Jak funguje automatické monitorování závislostí?

Závislosti jsou automaticky shromažďovány pomocí jedné z následujících technik:

* Použití instrumentace bajtového kódu kolem vybraných metod. (InstrumentationEngine buď z StatusMonitor nebo Rozšíření Azure Web App)
* Zpětná volání EventSource
* DiagnosticSource Zpětná volání (v nejnovějších sadách SDK jádra .NET/.NET)

## <a name="manually-tracking-dependencies"></a>Ruční sledování závislostí

Následují některé příklady závislostí, které nejsou automaticky shromažďovány a proto vyžadují ruční sledování.

* Azure Cosmos DB se automaticky sleduje, jenom když se používá [HTTP/HTTPS.](../../cosmos-db/performance-tips.md#networking) Režim TCP nebude zachycen application insights.
* Redis

Pro tyto závislosti, které nejsou automaticky shromažďovány sadou SDK, můžete je sledovat ručně pomocí [rozhraní TRACKDependency API,](api-custom-events-metrics.md#trackdependency) které se používá standardní moduly automatické kolekce.

Například pokud vytvoříte kód s sestavením, které jste nenapsali sami, můžete čas všechny volání na něj zjistit, jaký příspěvek to dělá pro vaše doby odezvy. Chcete-li, aby se tato data zobrazovala v `TrackDependency`grafech závislostí v application insights, odešlete je pomocí .

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

Alternativně `TelemetryClient` poskytuje metody `StartOperation` `StopOperation` rozšíření a které lze použít k ručnímu sledování závislostí, jak je znázorněno [zde](custom-operations-tracking.md#outgoing-dependencies-tracking)

Pokud chcete vypnout modul sledování standardní závislosti, odeberte odkaz na DependencyTrackingTelemetryModule v [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) pro ASP.NET aplikace. Pro ASP.NET základní aplikace postupujte podle pokynů [zde](asp-net-core.md#configuring-or-removing-default-telemetrymodules).

## <a name="tracking-ajax-calls-from-web-pages"></a>Sledování volání AJAX z webových stránek

Pro webové stránky JavaScript SDK Application Insights automaticky shromažďuje volání AJAX jako závislosti.

## <a name="advanced-sql-tracking-to-get-full-sql-query"></a>Rozšířené sledování SQL pro získání úplného dotazu SQL

U volání SQL je název serveru a databáze vždy shromažďován a `DependencyTelemetry`uložen jako název shromážděných . Existuje další pole s názvem "data", které může obsahovat úplný text dotazu SQL.

Pro ASP.NET základní aplikace, neexistuje žádný další krok potřebný k získání úplného dotazu SQL.

Pro ASP.NET aplikace je úplný dotaz SQL shromažďován pomocí instrumentace bajtového kódu, který vyžaduje instrumentaci motoru. Další kroky specifické pro platformu, jak je popsáno níže, jsou povinné.

| Platforma | Krok (y) Potřebné k získání úplného dotazu SQL |
| --- | --- |
| Webová aplikace Azure |Na ovládacím panelu webové aplikace [otevřete okno Application Insights](../../azure-monitor/app/azure-web-apps.md) a povolte příkazy SQL v části .NET. |
| Server Služby IIS (virtuální počítač Azure, on-prem a tak dále.) | Pomocí modulu PowerShell monitoru stavu [nainstalujte modul Instrumentation Engine](../../azure-monitor/app/status-monitor-v2-api-reference.md) a restartujte službu IIS. |
| Cloudová služba Azure | Přidání [úlohy při spuštění k instalaci služby StatusMonitor](../../azure-monitor/app/cloudservices.md#set-up-status-monitor-to-collect-full-sql-queries-optional) <br> Vaše aplikace by měla být připojena k ApplicationInsights SDK v době sestavení instalací balíčků NuGet pro [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) nebo [ASP.NET základní aplikace](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) |
| IIS Express | Nepodporuje se

Ve výše uvedených případech je správný způsob ověření správné instalace přístrojového stroje ověřením, `DependencyTelemetry` že verze sdk shromážděných je "RDDP". 'rdddsd' nebo 'rddf' označuje, že závislosti jsou shromažďovány prostřednictvím DiagnosticSource nebo EventSource zpětná volání, a proto nebude zachycen úplný dotaz SQL.

## <a name="where-to-find-dependency-data"></a>Kde najít data závislostí

* [Mapa aplikací](app-map.md) vizualizuje závislosti mezi aplikací a sousedními součástmi.
* [Diagnostika transakcí](transaction-diagnostics.md) zobrazuje jednotná, korelační serverová data.
* [Karta Prohlížeče](javascript.md) zobrazuje volání AJAX z prohlížečů vašich uživatelů.
* Proklikejte se z pomalých nebo neúspěšných požadavků a zkontrolujte jejich volání závislostí.
* [Analýzu](#logs-analytics) lze použít k dotazování na data závislostí.

## <a name="diagnose-slow-requests"></a><a name="diagnosis"></a>Diagnostika pomalých požadavků

Každá událost požadavku je přidružena k volání závislostí, výjimkám a dalším událostem, které jsou sledovány při zpracování požadavku vaší aplikací. Takže pokud některé požadavky dělají špatně, můžete zjistit, zda je to z důvodu pomalé odpovědi ze závislosti.

### <a name="tracing-from-requests-to-dependencies"></a>Trasování z požadavků na závislosti

Otevřete kartu **Výkon** a přejděte na kartu **Závislosti** v horní části vedle operací.

Klikněte na **název závislosti** v části celkově. Po výběru závislosti se vpravo zobrazí graf rozdělení doby trvání této závislosti.

![Na kartě výkon klikněte na kartu Závislost v horní části a potom na název závislosti v grafu](./media/asp-net-dependencies/2-perf-dependencies.png)

Klikněte na modré tlačítko **Vzorky** v pravém dolním rohu a pak na vzorku zobrazíte podrobnosti o transakci od konce.

![Kliknutím na ukázku zobrazíte podrobnosti o transakcích od konce.](./media/asp-net-dependencies/3-end-to-end.png)

### <a name="profile-your-live-site"></a>Profilujte svůj živý web

Netušíte, kam ten čas sem jde? Profiler [Application Insights](../../azure-monitor/app/profiler.md) sleduje volání HTTP na váš živý web a zobrazuje funkce v kódu, které trvaly nejdelší dobu.

## <a name="failed-requests"></a>Neúspěšné požadavky

Neúspěšné požadavky mohou být také přidruženy k neúspěšným voláním závislostí.

Můžeme přejít na kartu **Selhání** vlevo a kliknout na kartu **závislosti** v horní části.

![Klikněte na graf neúspěšných požadavků.](./media/asp-net-dependencies/4-fail.png)

Zde budete moci zobrazit počet neúspěšných závislostí. Chcete-li získat další podrobnosti o neúspěšném výskytu, pokusem o kliknutí na název závislosti v dolní tabulce. Kliknutím na modré tlačítko **Závislosti** v pravém dolním rohu získáte podrobnosti o transakcích od konce.

## <a name="logs-analytics"></a>Protokoly (Analytics)

Závislosti můžete sledovat v [dotazovacím jazyce Kusto](/azure/kusto/query/). Zde je několik příkladů:

* Vyhledání všech neúspěšných volání závislostí:

``` Kusto

    dependencies | where success != "True" | take 10
```

* Najít VOLÁNÍ AJAX:

``` Kusto

    dependencies | where client_Type == "Browser" | take 10
```

* Hledání volání závislostí přidružených k požadavkům:

``` Kusto

    dependencies
    | where timestamp > ago(1d) and  client_Type != "Browser"
    | join (requests | where timestamp > ago(1d))
      on operation_Id  
```


* Najít VOLÁNÍ AJAX přidružené k zobrazení stránky:

``` Kusto 

    dependencies
    | where timestamp > ago(1d) and  client_Type == "Browser"
    | join (browserTimings | where timestamp > ago(1d))
      on operation_Id
```

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="how-does-automatic-dependency-collector-report-failed-calls-to-dependencies"></a>*Jak se selhání sestavy automatického kolektoru závislostí na závislostech?*

* Neúspěšná volání závislostí budou mít pole "úspěch" nastaveno na hodnotu False. `DependencyTrackingTelemetryModule`nehlásí `ExceptionTelemetry`. Úplný datový model pro závislost je popsán [zde](data-model-dependency-telemetry.md).

## <a name="open-source-sdk"></a>Sada SDK s otevřeným zdrojovým kódem
Stejně jako každý Application Insights SDK, modul kolekce závislostí je také open source. Přečtěte si kód a přispějte k něm nebo nahlaste problémy na [oficiálním úložišti GitHub](https://github.com/Microsoft/ApplicationInsights-dotnet-server).

## <a name="next-steps"></a>Další kroky

* [Výjimky](../../azure-monitor/app/asp-net-exceptions.md)
* [Uživatelská & stránková data](../../azure-monitor/app/javascript.md)
* [Dostupnost](../../azure-monitor/app/monitor-web-app-availability.md)
