---
title: Závislost sledování ve službě Azure Application Insights | Dokumentace Microsoftu
description: Monitorování volání závislostí z vašich místních nebo webová aplikace Microsoft Azure s využitím Application Insights.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: d15c4ca8-4c1a-47ab-a03d-c322b4bb2a9e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: mbullwin
ms.openlocfilehash: d8ba5b19ad5d8f03203e9a028fbc5aec84e5ec06
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565374"
---
# <a name="dependency-tracking-in-azure-application-insights"></a>Ve službě Azure Application Insights pro sledování závislostí 

A *závislost* je externí komponenta, která volá vaši aplikaci. Obvykle se jedná o službu volána pomocí protokolu HTTP, nebo databázi nebo systému souborů. [Application Insights](../../azure-monitor/app/app-insights-overview.md) měří dobu trvání volání závislostí, zda jeho selhání nebo Ne, společně s dalšími informacemi, jako je název závislosti a tak dále. Můžete prozkoumat konkrétní závislost volání a korelovat je k žádosti a výjimky.

## <a name="automatically-tracked-dependencies"></a>Automaticky sledovaný závislosti

Sady Application Insights SDK pro .NET a .NET Core se dodává s `DependencyTrackingTelemetryModule` což je modul telemetrická data, která automaticky shromažďuje závislosti. Tuto kolekci závislostí se povolí automaticky pro [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) a [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) aplikace, když je nakonfigurovaný podle propojené oficiální dokumentace. `DependencyTrackingTelemetryModule` je příslušný [to](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) balíčku NuGet a nepřipojí automaticky při použití některý z balíčků NuGet `Microsoft.ApplicationInsights.Web` nebo `Microsoft.ApplicationInsights.AspNetCore`.

 `DependencyTrackingTelemetryModule` aktuálně automaticky sleduje následující závislosti:

|Závislosti |Podrobnosti|
|---------------|-------|
|Http/Https | Místní nebo vzdálené volání http/https |
|Volání WCF| Pokud se používají vazby založené na protokolu Http, pouze sledovat automaticky.|
|SQL | Volání s `SqlClient`. Zobrazit [to](##advanced-sql-tracking-to-get-full-sql-query) pro zachycení SQL dotazu.  |
|[Azure storage (Blob, tabulky, fronty)](https://www.nuget.org/packages/WindowsAzure.Storage/) | Volání s klienta úložiště Azure. |
|[EventHub Client SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | Verze 1.1.0 a vyšší. |
|[Klientská sada SDK pro služby Service Bus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| Verze 3.0.0 a vyšší. |
|Azure Cosmos DB | Pokud se používá HTTP/HTTPS, pouze sledována automaticky. Režim TCP nebude zachycena Application Insights. |


## <a name="setup-automatic-dependency-tracking-in-console-apps"></a>Nastavení automatické sledování v konzolových aplikacích závislostí

Automaticky sledovat závislosti z.NET/.NET základní konzolové aplikace, nainstalujte balíček Nuget `Microsoft.ApplicationInsights.DependencyCollector`a také inicializovat `DependencyTrackingTelemetryModule` následujícím způsobem:

```csharp
    DependencyTrackingTelemetryModule depModule = new DependencyTrackingTelemetryModule();
    depModule.Initialize(TelemetryConfiguration.Active);
```

### <a name="how-automatic-dependency-monitoring-works"></a>Funkce Automatické závislost monitorování funguje?

Závislosti se automaticky shromažďují pomocí jedné z následujících postupů:

* Pomocí instrumentace kódu byte po výběru metody. (InstrumentationEngine z StatusMonitor nebo rozšíření Azure webové aplikace)
* Zpětná volání EventSource
* Zpětná volání DiagnosticSource (v nejnovější.NET/.NET Core SDK)

## <a name="manually-tracking-dependencies"></a>Ručně sledování závislostí

Následují příklady závislosti, které nejsou automaticky shromažďovat a proto vyžadují ruční sledování.

* Azure Cosmos DB se automaticky sleduje pouze v případě [HTTP/HTTPS](../../cosmos-db/performance-tips.md#networking) se používá. Režim TCP nebude zachycena Application Insights.
* Redis

Pro tyto závislosti automaticky shromážděná sadou SDK, můžete sledovat jejich používání ručně [TrackDependency API](api-custom-events-metrics.md#trackdependency) , který se používá standardní automatické shromažďování moduly.

Například pokud vytváření kódu pomocí sestavení, které jste sami nenapsali může čas všechna volání, a zjistěte, jaké příspěvek, odešle vaše doby odezvy. Pokud chcete, aby tato data zobrazí v grafech závislostí ve službě Application Insights, odeslat ho pomocí `TrackDependency`.

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

Alternativně `TelemetryClient` poskytuje rozšiřující metody `StartOperation` a `StopOperation` kterých jde můžete manuálně sledovat závislosti, jak je znázorněno [zde](custom-operations-tracking.md#outgoing-dependencies-tracking)

Pokud chcete vypnout sledování modulu standardních závislostí, odeberte odkaz na DependencyTrackingTelemetryModule v [soubor ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) pro aplikace ASP.NET. Pro aplikace ASP.NET Core, postupujte podle pokynů [tady](asp-net-core.md#configuring-or-removing-default-telemetrymodules).

## <a name="tracking-ajax-calls-from-web-pages"></a>Sledování volání AJAX z webových stránek

Pro webové stránky Application Insights JavaScript SDK automaticky shromažďuje volání AJAX jako závislosti podle popisu [tady](javascript.md#ajax-performance). Tento dokument se zaměřuje na závislosti ze součásti serveru.

## <a name="advanced-sql-tracking-to-get-full-sql-query"></a>Pokročilé sledování zobrazíte celý dotaz SQL SQL

Pro volání SQL, název serveru a databáze se vždy shromažďují a ukládají jako název shromážděných `DependencyTelemetry`. Existuje další pole s názvem "data", který může obsahovat fulltextové dotaz SQL.

Pro aplikace ASP.NET Core neexistuje žádné další kroky nutné získat celý dotaz SQL.

Pro aplikace ASP.NET se shromažďují celý dotaz SQL s nápovědou bajtů kód instrumentace, která vyžaduje modul instrumentace. Specifické pro platformu, jak je popsáno níže, jsou nutné další kroky.

| Platforma | Kroky potřebné k získání celý dotaz SQL |
| --- | --- |
| Webové aplikace Azure |V váš řídicí panel webové aplikace [otevřete okno Application Insights](../../azure-monitor/app/azure-web-apps.md) a povolit SQL příkazy v rámci .NET |
| Server služby IIS (virtuální počítač Azure, v místním prostředí a atd.) | [Nainstalujte monitorování stavu na vašem serveru, na kterém je aplikace spuštěna](../../azure-monitor/app/monitor-performance-live-website-now.md) a restartujte službu IIS.
| Cloudové služby Azure | Přidat [úloha po spuštění instalace StatusMonitor](../../azure-monitor/app/cloudservices.md#set-up-status-monitor-to-collect-full-sql-queries-optional) <br> Vaše aplikace by měla být připojení k sadě SDK ApplicationInsights v okamžiku sestavení podle instalace balíčků NuGet pro [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) nebo [aplikací ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) |
| Služba IIS Express | Nepodporuje se

Ve výše uvedených případech správný způsob ověřování tento motor instrumentace je správně nainstalována je tím, že ověří, že verze sady SDK shromážděných `DependencyTelemetry` je "rddp". 'rdddsd' nebo "rddf" označuje závislosti jsou shromažďovány prostřednictvím DiagnosticSource nebo EventSource zpětná volání, a proto nebude možné zaznamenat celý dotaz v jazyce SQL.

## <a name="where-to-find-dependency-data"></a>Kde najít data závislostí

* [Mapa aplikace](app-map.md) vizualizuje závislosti mezi vaší aplikací a sousední komponenty.
* [Diagnostika transakcí](transaction-diagnostics.md) ukazuje unified, korelovaných dat serveru.
* [Kartu prohlížeče](javascript.md#ajax-performance) ukazuje volání AJAX z prohlížečů uživatelů.
* Klikněte na z pomalý nebo neúspěšné žádosti ke kontrole jejich voláním závislostí.
* [Analytics](#logs-analytics) slouží k dotazování na data závislostí.

## <a name="diagnosis"></a> Diagnostika pomalé žádosti

Každý požadavek událost je přidružena volání závislostí, výjimek a dalších událostí, které jsou sledovány během zpracování požadavku aplikace. Proto pokud některé požadavky dělají chybně, najdete si, jestli je z důvodu zpomalení odezvy ze závislostí.

### <a name="tracing-from-requests-to-dependencies"></a>Trasování požadavků závislostí

Otevřít **výkonu** kartu a přejít na **závislosti** kartě v horní části stránky vedle operace.

Klikněte na **název závislosti** podle celkové. Po výběru závislost graf dané závislosti rozložení dob trvání se zobrazí na pravé straně.

![Výkonu kartě klikněte na kartu závislosti v horní části stránky klikněte název závislosti v grafu](./media/asp-net-dependencies/2-perf-dependencies.png)

Klikněte na modrý **ukázky** tlačítko v pravé dolní části a pak na ukázky a podrobnosti transakce začátku do konce.

![Klikněte na ukázky a podrobnosti transakce začátku do konce](./media/asp-net-dependencies/3-end-to-end.png)

### <a name="profile-your-live-site"></a>Profil živého webu

Představu kde čas prochází? [Application Insights profiler](../../azure-monitor/app/profiler.md) trasy HTTP zavolá na váš živý web a obsahuje funkce ve vašem kódu, který přijal nejvíce času.

## <a name="failed-requests"></a>Neúspěšné požadavky

Neúspěšné požadavky může být také přidružen neúspěšných volání závislostí.

Můžeme si do **selhání** kartu na levé straně a potom klikněte na **závislosti** kartě v horní části.

![Klikněte na graf neúspěšných žádostí](./media/asp-net-dependencies/4-fail.png)

Tady budete moci zobrazit počet neúspěšných závislostí. Chcete-li získat další podrobnosti o neúspěšných výskytů při kliknutí na název závislosti v tabulce dole. Můžete kliknout na modrý **závislosti** tlačítko v pravé dolní části zobrazíte podrobnosti o transakci začátku do konce.

## <a name="logs-analytics"></a>Protokoly (analýza)

Můžete sledovat v závislosti [Kusto dotazovací jazyk](/azure/kusto/query/). Zde je několik příkladů:

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

### <a name="how-does-automatic-dependency-collector-report-failed-calls-to-dependencies"></a>*Jak nepodporuje automatické závislostí kolekce sestavy se nezdařilo volání závislostí?*

* Neúspěšná volání bude mít pole 'success' nastavena na hodnotu False. `DependencyTrackingTelemetryModule` nevytváří sestavu `ExceptionTelemetry`. Úplný datový model pro závislost je popsán [tady](data-model-dependency-telemetry.md).

## <a name="open-source-sdk"></a>Open source sad SDK
Stejně jako každý sadu SDK Application Insights závislostí kolekce modul je také open source. Čtení a přispívání ke kódu nebo hlášení problémů v [oficiální úložiště GitHub se vzorovými](https://github.com/Microsoft/ApplicationInsights-dotnet-server).

## <a name="next-steps"></a>Další postup

* [Výjimky](../../azure-monitor/app/asp-net-exceptions.md)
* [Data uživatele a stránky](../../azure-monitor/app/javascript.md)
* [Dostupnost](../../azure-monitor/app/monitor-web-app-availability.md)
