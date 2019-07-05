---
title: Monitorování Azure Functions
description: Zjistěte, jak pomocí Azure Application Insights s využitím Azure Functions můžete sledovat provádění funkce.
services: functions
author: ggailey777
manager: jeconnoc
keywords: funkce azure, funkce, zpracování událostí, webhook, dynamické výpočty, architektura bez serverů
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: glenga
ms.openlocfilehash: 581b7cc09089b5f48938bc9677eca6b9dc3731d3
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442297"
---
# <a name="monitor-azure-functions"></a>Monitorování Azure Functions

[Služba Azure Functions](functions-overview.md) nabízí integrovanou integraci s [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) monitorování funkce. Tento článek ukazuje postup při konfiguraci Azure Functions k odeslání systémem generovaných protokolů do služby Application Insights.

Doporučujeme používat Application Insights, protože shromažďuje protokolu, výkonu a údaje o chybě. Automaticky detekuje anomálie výkonu a obsahuje výkonné analytické nástroje, které vám pomohou diagnostikovat problémy a pochopit, jak se používají funkce. Je navržena tak, aby pomáhala průběžně vylepšovat výkon a možnosti využití. Application Insights můžete použít i během vývoje projektu lokální funkce aplikace. Další informace najdete v tématu [co je Application Insights?](../azure-monitor/app/app-insights-overview.md).

Požadované instrumentace Application Insights je součástí Azure Functions, vše, co potřebujete je platný Instrumentační klíč pro připojení k prostředku služby Application Insights aplikaci function app.

## <a name="application-insights-pricing-and-limits"></a>Ceny Application Insights a omezení

Můžete vyzkoušet Application Insights integrace s aplikací Function App zdarma. Je denní limit objemu dat může zpracovat zdarma. Může být dosažení tohoto limitu během testování. Azure poskytuje portál a e-mailové oznámení, pokud blíží se denního limitu. Pokud byste tyto výstrahy a dosáhl limitu, nové protokoly se nezobrazí v dotazech Application Insights. Mějte na paměti limitu, aby se zabránilo zbytečným čas řešení problémů. Další informace najdete v tématu [Správa cen a objemů dat ve službě Application Insights](../azure-monitor/app/pricing.md).

## <a name="enable-application-insights-integration"></a>Povolit integraci modulu Application Insights

Aplikace function App k odesílání dat do služby Application Insights je potřeba vědět Instrumentační klíč tohoto prostředku Application Insights. Klíč musí být v aplikaci nastavení s názvem **APPINSIGHTS_INSTRUMENTATIONKEY**.

### <a name="new-function-app-in-the-portal"></a>Novou aplikaci function app na portálu

Pokud jste [vytvořit aplikaci function app na webu Azure Portal](functions-create-first-azure-function.md), integrace Application Insights je ve výchozím nastavení povolené. Prostředek Application Insights má stejný název jako vaše aplikace function app a vytvoří se ve stejné oblasti nebo v nejbližší oblasti.

Kontrola probíhá vytváření prostředku Application Insights, vyberte ji a rozbalte **Application Insights** okna. Můžete změnit **nový název prostředku** nebo zvolte jinou **umístění** v [zeměpisná oblast Azure](https://azure.microsoft.com/global-infrastructure/geographies/) kam chcete data ukládat.

![Povolit Application Insights při vytváření aplikace funkcí](media/functions-monitoring/enable-ai-new-function-app.png)

Při výběru **vytvořit**, se vytvoří prostředek Application Insights se aplikace function app, který má `APPINSIGHTS_INSTRUMENTATIONKEY` v aplikaci nastavení. Všechno je připravené.

<a id="manually-connect-an-app-insights-resource"></a>
### <a name="add-to-an-existing-function-app"></a>Přidat do existující aplikaci function app 

Když vytvoříte aplikaci funkcí s použitím [rozhraní příkazového řádku Azure](functions-create-first-azure-function-azure-cli.md), [sady Visual Studio](functions-create-your-first-function-visual-studio.md), nebo [Visual Studio Code](functions-create-first-function-vs-code.md), je potřeba vytvořit prostředek Application Insights. Potom přidáte Instrumentační klíč z tohoto prostředku jako nastavení aplikace ve vaší aplikaci funkcí.

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

Starší verze funkce používá integrované monitorování, které už se nedoporučuje. Při povolení integrace služby Application Insights pro aplikaci funkcí, je nutné také [zakázat vestavěné protokolování](#disable-built-in-logging).  

## <a name="view-telemetry-in-monitor-tab"></a>Zobrazení telemetrických dat na kartě monitorování

S [povolenou integrací služby Application Insights](#enable-application-insights-integration), můžete zobrazit telemetrická data v **monitorování** kartu.

1. V stránky aplikace funkcí vyberte funkci, která má aspoň jednou spustili po Application Insights byla nakonfigurována. Vyberte **monitorování** kartu.

   ![Vyberte kartu monitorování](media/functions-monitoring/monitor-tab.png)

1. Vyberte **aktualizovat** pravidelně, dokud se nezobrazí seznam obsahující záznamy volání funkcí.

   Může trvat až pět minut, než seznamu se zobrazí, když klient telemetrie dávek dat pro přenos do serveru. (Neplatí pro zpoždění [Live Metrics Stream](../azure-monitor/app/live-stream.md). Tuto službu se připojí k hostiteli funkce načtení stránky, takže protokoly streamovaných přímo na stránku.)

   ![Vyvolání seznamu](media/functions-monitoring/monitor-tab-ai-invocations.png)

1. Pokud chcete zobrazit protokoly pro konkrétní funkci volání, vyberte **datum** odkaz sloupce pro tohoto volání.

   ![Podrobnosti volání propojení](media/functions-monitoring/invocation-details-link-ai.png)

   Na nové stránce se zobrazí výstup protokolování pro tohoto volání.

   ![Podrobnosti volání](media/functions-monitoring/invocation-details-ai.png)

Uvidíte, že obě stránky obsahují **spustit ve službě Application Insights** odkaz pro dotaz Application Insights Analytics, který načítá data.

![Spustit ve službě Application Insights](media/functions-monitoring/run-in-ai.png)

Zobrazí se následující dotaz. Vidíte, že seznamu vyvolání je omezená na poslední 30 dnů. V seznamu se zobrazí více než 20 řádků (`where timestamp > ago(30d) | take 20`). Seznam podrobností volání je za posledních 30 dní bez omezení.

![Application Insights Analytics vyvolání seznamu](media/functions-monitoring/ai-analytics-invocation-list.png)

Další informace najdete v tématu [dotazování na telemetrická data](#query-telemetry-data) dále v tomto článku.

## <a name="view-telemetry-in-application-insights"></a>Zobrazení telemetrie v Application Insights

Otevřete službu Application Insights z aplikace function app na webu Azure Portal, přejděte na aplikace function app **přehled** stránky. V části **nakonfigurování funkcí**vyberte **Application Insights**.

![Otevřete službu Application Insights na stránce Přehled aplikace – funkce](media/functions-monitoring/ai-link.png)

Informace o tom, jak pomocí Application Insights, najdete v článku [dokumentace k Application Insights](https://docs.microsoft.com/azure/application-insights/). Tato část ukazuje několik příkladů toho, jak zobrazit data ve službě Application Insights. Pokud jste již obeznámeni s Application Insights, můžete přejít přímo na [oddíly o tom, jak konfigurace a přizpůsobení telemetrická data](#configure-categories-and-log-levels).

![Karta Insights přehled aplikace](media/functions-monitoring/metrics-explorer.png)

Následující oblasti služby Application Insights může být užitečné, když se určuje chování, výkon nebo chyby ve vašich funkcí:

| Tabulátor | Popis |
| ---- | ----------- |
| **[selhání](../azure-monitor/app/asp-net-exceptions.md)** |  Vytvoření grafů a výstrah na základě funkce chyby a výjimky serveru. **Název operace** je název funkce. Selhání v závislosti nezobrazí, Pokud implementujete vlastní telemetrii pro závislosti. |
| **[Výkon](../azure-monitor/app/performance-counters.md)** | Analýza problémů s výkonem. |
| **Servery** | Zobrazení využití prostředků a propustnost na jednom serveru. Tato data mohou být užitečné při ladění scénářů, ve kterém jsou funkce bogging dolů příslušných prostředků. Servery se označují jako **instance rolí cloudové**. |
| **[Metriky](../azure-monitor/app/metrics-explorer.md)** | Vytvoření výstrahy, které jsou založeny na metriky a grafy. Metriky zahrnují počet volání funkce, doba provádění a úspěšnosti. |
| **[Live Metrics Stream](../azure-monitor/app/live-stream.md)** | Zobrazení dat metrik, jakmile je vytvořena v reálném čase. |

## <a name="query-telemetry-data"></a>Dotazování na telemetrická data

[Application Insights Analytics](../azure-monitor/app/analytics.md) vám umožní přístup ke všem datům telemetrických dat ve formě tabulky v databázi. Analytics poskytuje dotazovací jazyk k extrakci, manipulaci a vizualizaci dat.

![Vyberte Analytics](media/functions-monitoring/select-analytics.png)

![Příklad analýz](media/functions-monitoring/analytics-traces.png)

Tady je příklad dotazu, který zobrazuje distribuci požadavků za pracovního procesu za posledních 30 minut.

```
requests
| where timestamp > ago(30m) 
| summarize count() by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```

Tabulky, které jsou k dispozici jsou uvedeny v **schématu** karty na levé straně. Můžete najít data generovaná volání funkce v následujících tabulkách:

| Table | Popis |
| ----- | ----------- |
| **trasování** | Protokoly vytvořené modulem runtime a kód funkce. |
| **Požadavky** | Jeden požadavek při každém vyvolání funkce. |
| **Výjimky** | Jakékoli výjimky vyvolané modulem runtime. |
| **customMetrics** | Počet úspěšných a neúspěšných volání, míru úspěšnosti a doba trvání. |
| **customEvents** | Události sledován pomocí funkce modulu runtime, například: Požadavky HTTP, které aktivují funkce. |
| **performanceCounters** | Informace o výkonu, které funkce jsou spuštěny na servery. |

Ostatní tabulky jsou pro testy dostupnosti a telemetrii klienta a prohlížeče. Můžete implementovat vlastní telemetrii můžete přidat do nich.

V každé tabulce některá data konkrétních funkcí je `customDimensions` pole.  Například následující dotaz načte všechna trasování, které mají úroveň protokolu `Error`.

```
traces 
| where customDimensions.LogLevel == "Error"
```

Modul runtime poskytuje `customDimensions.LogLevel` a `customDimensions.Category` pole. Můžete zadat další pole v protokolech, které napíšete kód vaší funkce. Zobrazit [strukturované protokolování](#structured-logging) dále v tomto článku.

## <a name="configure-categories-and-log-levels"></a>Konfigurace kategorií a úrovně protokolování

Application Insights můžete používat bez vlastní konfigurace. Výchozí konfigurace může způsobit velké objemy dat. Pokud používáte předplatné sady Visual Studio v Azure, může dosáhnete limitu vaše data Application Insights. Dále v tomto článku se dozvíte, jak nakonfigurovat a upravit data, která vaše funkce posílat do Application Insights. Aplikace function App, protokolování je nastaveno v [host.json] souboru.

### <a name="categories"></a>Categories

Zahrnuje protokolovacího nástroje Azure Functions *kategorie* pro každý protokol. Kategorie určuje, které části kódu modulu runtime nebo kód vaší funkce zapsáno do protokolu. 

Modul runtime služby Functions vytvoří protokoly s kategorií, které začínají řetězcem "Hostitel". "Funkce spuštění," "spustí funkce" a "funkce dokončena" protokoly mají kategorii "Host.Executor." 

Pokud se zapisují protokoly do vašeho kódu funkce jejich kategorie je "Funkce".

### <a name="log-levels"></a>Úroveň protokolu

Zahrnuje také protokolovacího nástroje Azure Functions *úrovně protokolování* s každou protokolu. [LogLevel](/dotnet/api/microsoft.extensions.logging.loglevel) je výčet a kód celé číslo označuje relativní význam:

|LogLevel    |Kód|
|------------|---|
|Trasování       | 0 |
|Ladění       | 1 |
|Informace | 2 |
|Upozornění     | 3 |
|Chyba       | 4 |
|Kritická    | 5 |
|Žádný        | 6 |

Úrovně protokolování `None` je vysvětleno v další části. 

### <a name="log-configuration-in-hostjson"></a>Konfigurace protokolu v host.json

[Host.json] soubor nastaví jaká míra protokolování aplikace function app se odešle do služby Application Insights. Pro každou kategorii určujete minimální úroveň protokolování k odeslání. Existují dva příklady: první příklad cílí [modul runtime verze 2.x funkce](functions-versions.md#version-2x) (.NET Core) a druhý příklad je určený pro modul runtime verze 1.x.

### <a name="version-2x"></a>Verze 2.x

Modul runtime v2.x používá [hierarchie filtru protokolování .NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering). 

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host.Results": "Error",
      "Function": "Error",
      "Host.Aggregator": "Trace"
    }
  }
}
```

### <a name="version-1x"></a>Verzi 1.x

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host.Results": "Error",
        "Function": "Error",
        "Host.Aggregator": "Trace"
      }
    }
  }
}
```

V tomto příkladu nastaví následující pravidla:

* Pro protokoly s kategorií `Host.Results` nebo `Function`, odeslat pouze `Error` úroveň a vyšší do Application Insights. Protokoly pro `Warning` úrovně a níže jsou ignorovány.
* Pro protokoly s kategorií `Host.Aggregator`, všechny protokoly posílat do Application Insights. `Trace` Úroveň protokolu je stejný jako co volání některých protokolovacích nástrojů `Verbose`, ale použít `Trace` v [host.json] souboru.
* Pro všechny ostatní protokoly odeslat pouze `Information` úroveň a vyšší do Application Insights.

Hodnota kategorie v [host.json] řídí protokolování všech kategorií, které začínají stejnou hodnotu. `Host` v [host.json] protokolování pro ovládací prvky `Host.General`, `Host.Executor`, `Host.Results`, a tak dále.

Pokud [host.json] zahrnuje více kategorií, které začínají stejný řetězec delší ty budou odpovídat nejprve. Předpokládejme, že chcete, aby všechno z modulu runtime s výjimkou `Host.Aggregator` protokolu `Error` úroveň, ale chcete mít `Host.Aggregator` protokolu `Information` úroveň:

### <a name="version-2x"></a>Verze 2.x 

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host": "Error",
      "Function": "Error",
      "Host.Aggregator": "Information"
    }
  }
}
```

### <a name="version-1x"></a>Verzi 1.x 

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host": "Error",
        "Function": "Error",
        "Host.Aggregator": "Information"
      }
    }
  }
}
```

Chcete-li potlačit všechny protokoly pro kategorii, můžete použít úroveň protokolu `None`. Žádné protokoly se zapisují do této kategorie a neexistuje žádná úroveň protokolu nad ním.

Následující části popisují hlavní kategorie protokoly, které modul runtime vytvoří. 

### <a name="category-hostresults"></a>Kategorie Host.Results

Tyto protokoly se zobrazí jako "požadavky" v Application Insights. Indikuje úspěch nebo neúspěch funkce.

![Žádosti o grafu](media/functions-monitoring/requests-chart.png)

Všechny tyto protokoly se zapíšou ve `Information` úroveň. Pokud filtr v `Warning` nebo vyšší, některé z těchto dat neuvidíte.

### <a name="category-hostaggregator"></a>Kategorie Host.Aggregator

Tyto protokoly poskytuje přes počty a průměry obsahující záznamy volání funkcí [konfigurovatelné](#configure-the-aggregator) období času. Výchozí doba je 30 sekund nebo 1 000 výsledky, podle toho, co nastane dřív. 

Protokoly jsou k dispozici v **customMetrics** tabulky ve službě Application Insights. Příklady jsou počet běhů, míru úspěšnosti a doba trvání.

![customMetrics dotazu](media/functions-monitoring/custom-metrics-query.png)

Všechny tyto protokoly se zapíšou ve `Information` úroveň. Pokud filtr v `Warning` nebo vyšší, některé z těchto dat neuvidíte.

### <a name="other-categories"></a>Další kategorie

Všechny protokoly pro kategorie než těm, které jsou již jsou k dispozici v **trasy** tabulky ve službě Application Insights.

![trasování dotazů](media/functions-monitoring/analytics-traces.png)

Všechny protokoly s kategorií, které začínají `Host` se zapisují modul runtime Functions. "Funkce Spustit" a "Funkce dokončena" protokoly mají kategorii `Host.Executor`. U úspěšných spuštění tyto protokoly jsou `Information` úroveň. Výjimky jsou protokolovány v `Error` úroveň. Modul runtime vytvoří také `Warning` úroveň protokoly, například: fronty zprávy odeslané do fronty poškozené.

Protokoly vytvořené kód vaší funkce mají kategorii `Function` a může být libovolná úroveň protokolu.

## <a name="configure-the-aggregator"></a>Konfigurace agregátoru

Jak je uvedeno v předchozí části, modul runtime agreguje data o provádění funkcí po určitou dobu. Výchozí doba je 30 sekund nebo 1 000 spuštění, co nastane dříve. Toto nastavení můžete nakonfigurovat [host.json] souboru.  Tady je příklad:

```json
{
    "aggregator": {
      "batchSize": 1000,
      "flushTimeout": "00:00:30"
    }
}
```

## <a name="configure-sampling"></a>Konfigurace odběru vzorků

Application Insights má [vzorkování](../azure-monitor/app/sampling.md) funkce, která můžete chránit z vytváření příliš mnoho telemetrických dat na dokončení prováděných v některých případech zátěž ve špičce. Po Míra příchozích spuštění překročí zadanou prahovou hodnotu, Application Insights začne náhodně ignorovat některé příchozí spuštění. Výchozí nastavení pro maximální počet spuštění za sekundu je 20 (pět ve verzi 1.x). Můžete nakonfigurovat vzorkování v [host.json].  Tady je příklad:

### <a name="version-2x"></a>Verze 2.x 

```json
{
  "logging": {
    "applicationInsights": {
      "samplingSettings": {
        "isEnabled": true,
        "maxTelemetryItemsPerSecond" : 20
      }
    }
  }
}
```

### <a name="version-1x"></a>Verzi 1.x 

```json
{
  "applicationInsights": {
    "sampling": {
      "isEnabled": true,
      "maxTelemetryItemsPerSecond" : 5
    }
  }
}
```

> [!NOTE]
> [Vzorkování](../azure-monitor/app/sampling.md) je ve výchozím nastavení povolené. Vypadá to, že chybí data, můžete potřebovat upravit nastavení vzorkování podle vaší konkrétní scénáře monitorování.

## <a name="write-logs-in-c-functions"></a>Zapisují protokoly do funkcí jazyka C#

Zapisují protokoly do kódu funkce, které se zobrazují jako trasování v Application Insights.

### <a name="ilogger"></a>ILogger

Použití [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger) parametr vašich funkcí místo `TraceWriter` parametru. Protokoly vytvořené s použitím `TraceWriter` přejděte do služby Application Insights, ale `ILogger` umožňuje provádět [strukturované protokolování](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Pomocí `ILogger` objektu, můžete volat `Log<level>` [rozšiřující metody na objektu ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.loggerextensions#methods) vytvořit protokoly. Následující kód zápisy `Information` protokoly z kategorie "Funkce".

```cs
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger logger)
{
    logger.LogInformation("Request for item with key={itemKey}.", id);
```

### <a name="structured-logging"></a>Strukturované protokolování

Pořadí zástupné symboly, nikoli jejich názvy, určuje, jaké parametry se používají ve zprávě protokolu. Předpokládejme, že máte následující kód:

```csharp
string partitionKey = "partitionKey";
string rowKey = "rowKey";
logger.LogInformation("partitionKey={partitionKey}, rowKey={rowKey}", partitionKey, rowKey);
```

Je-li zachovat stejný řetězec zprávy a pořadí parametrů, výsledný text zprávy by mít hodnoty nesprávných místech.

Zástupné symboly jsou zpracovány tímto způsobem tak, aby vám pomůžou strukturované protokolování. Application Insights ukládají dvojice název hodnota parametrů a řetězec zprávy. Výsledek, že je zpráva argumentů pole, které můžete zadat dotaz na.

Pokud vaše volání metody protokolovacího nástroje vypadá podobně jako v předchozím příkladu, můžete dát dotaz na pole `customDimensions.prop__rowKey`. `prop__` Je přidána předpona, abyste zajistili zde nejsou žádné kolize mezi poli modulu runtime přidá pole a kód vaší funkce přidá.

Můžete také zadávat dotazy na původní řetězec zprávy pomocí odkazu na pole `customDimensions.prop__{OriginalFormat}`.  

Tady je ukázka JSON s reprezentací provedených `customDimensions` dat:

```json
{
  customDimensions: {
    "prop__{OriginalFormat}":"C# Queue trigger function processed: {message}",
    "Category":"Function",
    "LogLevel":"Information",
    "prop__message":"c9519cbf-b1e6-4b9b-bf24-cb7d10b1bb89"
  }
}
```

### <a name="custom-metrics-logging"></a>Protokolování vlastních metrik

Ve skriptu funkcí jazyka C#, můžete použít `LogMetric` rozšiřující metody na `ILogger` vytvořit vlastní metriky v Application Insights. Tady je ukázka volání metody:

```csharp
logger.LogMetric("TestMetric", 1234);
```

Tento kód se o alternativu k volání `TrackMetric` pomocí rozhraní API Application Insights pro .NET.

## <a name="write-logs-in-javascript-functions"></a>Zapisují protokoly do funkce jazyka JavaScript

Ve funkcích Node.js pomocí `context.log` zápis protokolů. Není povoleno strukturované protokolování.

```
context.log('JavaScript HTTP trigger function processed a request.' + context.invocationId);
```

### <a name="custom-metrics-logging"></a>Protokolování vlastních metrik

Při spouštění [verzi 1.x](functions-versions.md#creating-1x-apps) modul runtime služby Functions, pomocí funkce Node.js `context.log.metric` metodu pro vytvoření vlastní metriky v Application Insights. Tato metoda není podporována aktuálně ve verzi 2.x. Tady je ukázka volání metody:

```javascript
context.log.metric("TestMetric", 1234);
```

Tento kód se o alternativu k volání `trackMetric` pomocí sady Node.js SDK pro Application Insights.

## <a name="log-custom-telemetry-in-c-functions"></a>Přihlaste se vlastní telemetrická data C# funkce

Můžete použít [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) balíčku NuGet k odesílání vlastních telemetrických dat do služby Application Insights. Následující C# příklad používá [vlastní telemetrii API](../azure-monitor/app/api-custom-events-metrics.md). V příkladu se pro knihovny tříd .NET, ale kódu Application Insights je stejný pro skript jazyka C#.

### <a name="version-2x"></a>Verze 2.x

Modul runtime verze 2.x využívá novější funkce ve službě Application Insights k automaticky propojte telemetrické aktuální operace. Není nutné ručně nastavit operaci `Id`, `ParentId`, nebo `Name` pole.

```cs
using System;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;

namespace functionapp0915
{
    public class HttpTrigger2
    {
        private readonly TelemetryClient telemetryClient;

        /// Using dependency injection will guarantee that you use the same configuration for telemetry collected automatically and manually.
        public HttpTrigger2(TelemetryConfiguration telemetryConfiguration)
        {
            this.telemetryClient = new TelemetryClient(telemetryConfiguration);
        }

        [FunctionName("HttpTrigger2")]
        public Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", Route = null)]
            HttpRequest req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // Parse query parameter
            string name = req.Query
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Track an Event
            var evt = new EventTelemetry("Function called");
            evt.Context.User.Id = name;
            this.telemetryClient.TrackEvent(evt);

            // Track a Metric
            var metric = new MetricTelemetry("Test Metric", DateTime.Now.Millisecond);
            metric.Context.User.Id = name;
            this.telemetryClient.TrackMetric(metric);

            // Track a Dependency
            var dependency = new DependencyTelemetry
            {
                Name = "GET api/planets/1/",
                Target = "swapi.co",
                Data = "https://swapi.co/api/planets/1/",
                Timestamp = start,
                Duration = DateTime.UtcNow - start,
                Success = true
            };
            dependency.Context.User.Id = name;
            this.telemetryClient.TrackDependency(dependency);

            return Task.FromResult<IActionResult>(new OkResult());
        }
    }
}
```

### <a name="version-1x"></a>Verzi 1.x

```cs
using System;
using System.Net;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.Azure.WebJobs;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;
using System.Linq;

namespace functionapp0915
{
    public static class HttpTrigger2
    {
        private static string key = TelemetryConfiguration.Active.InstrumentationKey = 
            System.Environment.GetEnvironmentVariable(
                "APPINSIGHTS_INSTRUMENTATIONKEY", EnvironmentVariableTarget.Process);

        private static TelemetryClient telemetryClient = 
            new TelemetryClient() { InstrumentationKey = key };

        [FunctionName("HttpTrigger2")]
        public static async Task<HttpResponseMessage> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
            HttpRequestMessage req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // Parse query parameter
            string name = req.GetQueryNameValuePairs()
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Get request body
            dynamic data = await req.Content.ReadAsAsync<object>();

            // Set name to query string or body data
            name = name ?? data?.name;
         
            // Track an Event
            var evt = new EventTelemetry("Function called");
            UpdateTelemetryContext(evt.Context, context, name);
            telemetryClient.TrackEvent(evt);
            
            // Track a Metric
            var metric = new MetricTelemetry("Test Metric", DateTime.Now.Millisecond);
            UpdateTelemetryContext(metric.Context, context, name);
            telemetryClient.TrackMetric(metric);
            
            // Track a Dependency
            var dependency = new DependencyTelemetry
                {
                    Name = "GET api/planets/1/",
                    Target = "swapi.co",
                    Data = "https://swapi.co/api/planets/1/",
                    Timestamp = start,
                    Duration = DateTime.UtcNow - start,
                    Success = true
                };
            UpdateTelemetryContext(dependency.Context, context, name);
            telemetryClient.TrackDependency(dependency);
        }
        
        // Correlate all telemetry with the current Function invocation
        private static void UpdateTelemetryContext(TelemetryContext context, ExecutionContext functionContext, string userName)
        {
            context.Operation.Id = functionContext.InvocationId.ToString();
            context.Operation.ParentId = functionContext.InvocationId.ToString();
            context.Operation.Name = functionContext.FunctionName;
            context.User.Id = userName;
        }
    }    
}
```

Nevolejte `TrackRequest` nebo `StartOperation<RequestTelemetry>` vzhledem k tomu, že uvidíte duplicitní žádosti pro volání funkce.  Modul runtime služby Functions automaticky sleduje žádosti.

Nenastavujte `telemetryClient.Context.Operation.Id`. Tato globální nastavení způsobí, že nesprávná korelační při mnoho funkcí spuštěných současně. Místo toho vytvořte novou instanci telemetrie (`DependencyTelemetry`, `EventTelemetry`) a upravte její `Context` vlastnost. Předat instanci telemetrická data do odpovídajících `Track` metoda `TelemetryClient` (`TrackDependency()`, `TrackEvent()`). Tato metoda zajišťuje, že telemetrická data obsahuje podrobnosti správné korelace pro aktuální volání funkce.

## <a name="log-custom-telemetry-in-javascript-functions"></a>Vlastní telemetrická data protokolu v funkce jazyka JavaScript

[Application Insights Node.js SDK](https://www.npmjs.com/package/applicationinsights) je aktuálně ve verzi beta. Tady je ukázkový kód, který odesílá vlastní telemetrii do Application Insights:

```javascript
const appInsights = require("applicationinsights");
appInsights.setup();
const client = appInsights.defaultClient;

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    client.trackEvent({name: "my custom event", tagOverrides:{"ai.operation.id": context.invocationId}, properties: {customProperty2: "custom property value"}});
    client.trackException({exception: new Error("handled exceptions can be logged with this method"), tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackMetric({name: "custom metric", value: 3, tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackTrace({message: "trace message", tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL", tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true, tagOverrides:{"ai.operation.id": context.invocationId}});

    context.done();
};
```

`tagOverrides` Sady parametrů `operation_Id` ID funkce volání. Toto nastavení umožňuje korelovat všechny automaticky generované a vlastní telemetrická data pro volání dané funkce.

## <a name="dependencies"></a>Závislosti

Funkce v2 automaticky shromažďuje závislosti pro požadavky HTTP, služby Service Bus a SQL.

Můžete napsat vlastní kód k zobrazení závislostí. Příklady najdete v tématu ukázkový kód v [ C# vlastní telemetrii části](#log-custom-telemetry-in-c-functions). Vzorový kód vede *Mapa aplikace* ve službě Application Insights, vypadá podobně jako na následujícím obrázku:

![Mapa aplikace](./media/functions-monitoring/app-map.png)

## <a name="report-issues"></a>Hlásit problémy

Hlášení problémů s integrací služby Application Insights ve funkcích, nebo aby návrh nebo žádost [vytvoření problému v Githubu](https://github.com/Azure/Azure-Functions/issues/new).

## <a name="streaming-logs"></a>Datový proud protokolů

Při vývoji aplikace, je často užitečné prohlédnout si informace o protokolování v téměř reálném čase. Můžete zobrazit datový proud protokolů generovaných vašich funkcí na webu Azure Portal nebo v relaci příkazového řádku v místním počítači.

Jedná se o ekvivalent k výstupu viděli při ladění funkcí během [místního vývojového](functions-develop-local.md). Další informace najdete v tématu [jak streamování protokolů](../app-service/troubleshoot-diagnostic-logs.md#streamlogs).

> [!NOTE]
> Protokoly streamování podporují pouze jedna instance funkce hostitele. Když vaše funkce je škálování na více instancí, data z jiných instancí se nezobrazují v datovém proudu protokolu. [Live Metrics Stream](../azure-monitor/app/live-stream.md) ve službě Application Insights nepodporuje více instancí. Zatímco také v téměř reálném čase, streamovou analýzu jsou také založeny na [vzorků dat](#configure-sampling).

### <a name="portal"></a>Portál

Chcete-li zobrazit datový proud protokolů na portálu, vyberte **funkce platformy** kartě v aplikaci function app. Potom v části **monitorování**, zvolte **streamování protokolů**.

![Povolení streamování protokolů na portálu](./media/functions-monitoring/enable-streaming-logs-portal.png)

To propojuje vaši aplikaci do protokolu, služba pro streamování a aplikační protokoly jsou zobrazeny v okně. Můžete přepínat mezi **protokoly aplikací** a **webovou protokolech serveru**.  

![Zobrazit protokoly datových proudů na portálu](./media/functions-monitoring/streaming-logs-window.png)

### <a name="visual-studio-code"></a>Visual Studio Code

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

### <a name="azure-cli"></a>Azure CLI

Protokoly streamování můžete povolit pomocí [rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli). Přihlásit, zvolte předplatné a datový proud protokolů použijte následující příkazy:

```azurecli
az login
az account list
az account set --subscription <subscriptionNameOrId>
az webapp log tail --resource-group <RESOURCE_GROUP_NAME> --name <FUNCTION_APP_NAME>
```

### <a name="azure-powershell"></a>Azure PowerShell

Protokoly streamování můžete povolit pomocí [prostředí Azure PowerShell](/powershell/azure/overview). Pro prostředí PowerShell použijte následující příkazy pro přidání účtu Azure, zvolte předplatné a datový proud protokolů:

```powershell
Add-AzAccount
Get-AzSubscription
Get-AzSubscription -SubscriptionName "<subscription name>" | Select-AzSubscription
Get-AzWebSiteLog -Name <FUNCTION_APP_NAME> -Tail
```

## <a name="disable-built-in-logging"></a>Zakázat vestavěné protokolování

Když povolíte Application Insights, zakažte vestavěné protokolování, která používá službu Azure Storage. Integrované protokolování se hodí při testování s malé úlohy, ale není určena pro použití v produkčním prostředí vysokým zatížením. Pro monitorování produkčního prostředí, doporučujeme vám Application Insights. Pokud vestavěné protokolování je použít v produkčním prostředí, může být protokolování záznamů nedokončené kvůli omezování u služby Azure Storage.

Chcete-li zakázat vestavěné protokolování, odstraňte `AzureWebJobsDashboard` nastavení aplikace. Informace o tom, jak odstranit nastavení aplikace na webu Azure Portal najdete v tématu **nastavení aplikace** část [Správa aplikace function app](functions-how-to-use-azure-function-app-settings.md#settings). Před odstraněním nastavení aplikace, které Ujistěte se, že žádné existující funkce ve stejné aplikaci function app používat nastavení pro aktivační události služby Azure Storage nebo vazby.

## <a name="next-steps"></a>Další postup

Další informace najdete v následujících materiálech:

* [Application Insights](/azure/application-insights/)
* [ASP.NET Core protokolování](/aspnet/core/fundamentals/logging/)

[host.json]: functions-host-json.md
