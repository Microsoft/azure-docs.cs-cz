---
title: Monitorování Azure Functions
description: Zjistěte, jak použít Azure Application Insights s Azure Functions pro sledování spuštění funkce.
services: functions
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: funkce azure, funkce, zpracování událostí, webhook, dynamické výpočty, architektura bez serverů
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/15/2017
ms.author: tdykstra
ms.openlocfilehash: cbdb4691bac01843a451c988e09d77dd10f97461
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="monitor-azure-functions"></a>Monitorování Azure Functions

## <a name="overview"></a>Přehled 

[Azure Functions](functions-overview.md) nabízí integraci s [Azure Application Insights](../application-insights/app-insights-overview.md) pro monitorování funkce. Tento článek ukazuje, jak nakonfigurovat funkce Neodesílat telemetrická data do služby Application Insights.

![Průzkumníku metrik Application Insights](media/functions-monitoring/metrics-explorer.png)

Funkce má také [integrované monitorování, které nepoužívá Application Insights](#monitoring-without-application-insights). Doporučujeme vám Application Insights, protože nabízí další data a lepší způsoby, jak analyzovat data.

## <a name="application-insights-pricing-and-limits"></a>Application Insights ceny a omezení

Můžete vyzkoušet Application Insights integrace s aplikacemi funkce zdarma. Však není denní limit množství dat, které můžete zadarmo zpracování a může dosáhl tento limit během testování. Azure poskytuje portál a e-mailová oznámení, pokud jste vyčerpali denního limitu.  Ale pokud byste zapomněli na tyto výstrahy a dosáhl limitu, nové protokoly se nezobrazí v dotazech Application Insights. Proto buďte vědomi tento limit, aby se zabránilo zbytečným čas řešení problémů. Další informace najdete v tématu [spravovat cen a datový svazek ve službě Application Insights](../application-insights/app-insights-pricing.md).

## <a name="enable-app-insights-integration"></a>Povolit integraci statistiky aplikace

Pro funkce aplikace k odesílání dat do služby Application Insights musí znát klíč instrumentace prostředek Application Insights. Klíč musí být k dispozici v nastavení aplikace s názvem APPINSIGHTS_INSTRUMENTATIONKEY.

Můžete nastavit toto připojení v [portál Azure](https://portal.azure.com):

* [Automaticky pro novou aplikaci funkce](#new-function-app)
* [Ručně připojit prostředek App Insights](#manually-connect-an-app-insights-resource)

### <a name="new-function-app"></a>Nová aplikace funkcí

1. Přejděte do aplikaci funkce **vytvořit** stránky.

1. Nastavte **Application Insights** přepínač **na**.

2. Vyberte **Application Insights umístění**.

   Vyberte oblast, který je nejblíže k aplikaci funkce oblast v [Azure geography](https://azure.microsoft.com/global-infrastructure/geographies/) kam chcete data uložit.

   ![Povolit Application Insights při vytváření aplikace – funkce](media/functions-monitoring/enable-ai-new-function-app.png)

3. Zadejte další požadované informace.

1. Vyberte **Vytvořit**.

Dalším krokem je [zakázat integrované protokolování](#disable-built-in-logging).

### <a name="manually-connect-an-app-insights-resource"></a>Ručně připojit prostředek App Insights 

1. Vytvořte prostředek Application Insights. Nastavte typ aplikace na **Obecné**.

   ![Vytvořte prostředek Application Insights, zadejte obecné](media/functions-monitoring/ai-general.png)

2. Zkopírovat klíč instrumentace z **Essentials** stránky prostředku Application Insights. Najeďte myší na konci zobrazená hodnota klíče získat **kliknutím zkopírujte** tlačítko.

   ![Zkopírovat klíč instrumentace Application Insights](media/functions-monitoring/copy-ai-key.png)

1. V aplikaci funkce **nastavení aplikace** stránky, [přidat nastavení aplikace](functions-how-to-use-azure-function-app-settings.md#settings) kliknutím **přidejte nové nastavení**. Název nové nastavení APPINSIGHTS_INSTRUMENTATIONKEY a vložte klíč zkopírovaný instrumentace.

   ![Přidejte klíč instrumentace nastavení aplikace](media/functions-monitoring/add-ai-key.png)

1. Klikněte na **Uložit**.

## <a name="disable-built-in-logging"></a>Zakázat integrované protokolování

Pokud povolíte Application Insights, doporučujeme zakázat [vestavěné protokolování, který používá úložiště Azure](#logging-to-storage). Integrované protokolování je užitečné pro testování s světla úlohy, ale není určen pro použití v provozním prostředí vysokým zatížením. Pro produkční monitorování, se doporučuje Application Insights. Pokud integrované protokolování se používá v produkčním prostředí, mohou být neúplné kvůli omezování na Azure Storage záznam protokolování.

Chcete-li zakázat vestavěné protokolování, odstraňte `AzureWebJobsDashboard` nastavení aplikace. Informace o tom, jak odstranit aplikaci nastavení na portálu Azure najdete v tématu **nastavení aplikace** části [jak spravovat aplikaci funkce](functions-how-to-use-azure-function-app-settings.md#settings). Před odstraněním nastavení aplikace, ujistěte se, že žádné existující funkce ve stejné aplikaci funkce jej použít pro Azure Storage aktivační události nebo vazby.

## <a name="view-telemetry-in-monitor-tab"></a>Zobrazení telemetrie na kartě monitorování

Poté, co jste nastavili Application Insights integrace jak je znázorněno v předchozích částech, můžete zobrazit telemetrická data v **monitorování** kartě.

1. Na stránce funkce aplikace, vyberte funkci, která byla spuštěna nejméně jednou po Application Insights byla nakonfigurovaná a pak vyberte **monitorování** kartě.

   ![Vyberte kartu Sledování](media/functions-monitoring/monitor-tab.png)

2. Vyberte **aktualizovat** pravidelně, dokud se zobrazí seznam volání funkce.

   To může trvat až 5 minut v seznamu se objeví vzhledem ke způsobu telemetrická data dávky klienta pro přenos do serveru. (Tato prodleva se nevztahuje na [živý datový proud metriky](../application-insights/app-insights-live-stream.md). Této službě se připojí k hostiteli funkce při načtení stránky, takže protokoly streamovaných přímo na stránku.)

   ![Seznam volání](media/functions-monitoring/monitor-tab-ai-invocations.png)

2. Pokud chcete zobrazit protokoly pro vyvolání konkrétní funkce, vyberte **datum** sloupec odkaz pro tohoto volání.

   ![Podrobnosti o volání propojení](media/functions-monitoring/invocation-details-link-ai.png)

   Protokolování výstupu pro tohoto volání se zobrazí v nová stránka.

   ![Podrobnosti volání](media/functions-monitoring/invocation-details-ai.png)

Obě stránky (vyvolání seznamu a podrobností) odkazu Application Insights Analytics dotaz, který načte data:

![Spustit ve službě Application Insights](media/functions-monitoring/run-in-ai.png)

![Seznam volání Analytics Statistika aplikací](media/functions-monitoring/ai-analytics-invocation-list.png)

Z těchto dotazů, uvidíte, že seznamu vyvolání je omezená na poslední 30 dnů delší než 20 řádků (`where timestamp > ago(30d) | take 20`) a seznam podrobnosti o volání je za posledních 30 dnů bez omezení.

Další informace najdete v tématu [dotaz telemetrická data](#query-telemetry-data) dále v tomto článku.

## <a name="view-telemetry-in-app-insights"></a>Zobrazení telemetrie ve službě App Insights

Otevřete Application Insights z funkce aplikace na portálu Azure, vyberte **Application Insights** na odkaz v **nakonfigurované funkce** části funkce aplikace **přehled** stránky.

![Application Insights odkaz na stránce Přehled](media/functions-monitoring/ai-link.png)


Informace o tom, jak použít Application Insights, najdete v článku [Application Insights dokumentaci](https://docs.microsoft.com/azure/application-insights/). Tato část uvádí některé příklady, jak chcete zobrazit data ve službě Application Insights. Pokud jste již obeznámeni s Application Insights, můžete přejít přímo na [části o konfiguraci a přizpůsobení telemetrická data](#configure-categories-and-log-levels).

V [Průzkumníku metrik](../application-insights/app-insights-metrics-explorer.md), můžete vytvořit grafy a výstrahy podle metriky, například jako počet volání funkce, čas spuštění a míra úspěšnosti.

![Průzkumník metrik](media/functions-monitoring/metrics-explorer.png)

Na [selhání](../application-insights/app-insights-asp-net-exceptions.md) kartě, můžete vytvořit grafy a výstrahy na základě selhání funkce a server výjimky. **Název operace** je název funkce. Selhání v závislosti nejsou zobrazeny. Pokud budete implementovat [vlastní telemetrii](#custom-telemetry-in-c-functions) závislosti.

![Selhání](media/functions-monitoring/failures.png)

Na [výkonu](../application-insights/app-insights-performance-counters.md) kartě, můžete analyzovat problémy s výkonem.

![Výkon](media/functions-monitoring/performance.png)

**Servery** kartě se zobrazují využití prostředků a propustnosti na server. Tato data mohou být užitečné pro ladění scénáře, kde jsou funkce bogging dolů příslušných prostředků. Servery jsou označovány jako **cloudu instance rolí**.

![Servery](media/functions-monitoring/servers.png)

[Živý datový proud metriky](../application-insights/app-insights-live-stream.md) kartě se zobrazují data metriky, jak je zadáno v reálném čase.

![Živý datový proud](media/functions-monitoring/live-stream.png)

## <a name="query-telemetry-data"></a>Dotaz na data telemetrie

[Analýza aplikace Statistika](../application-insights/app-insights-analytics.md) dává vám přístup ke všem telemetrická data ve formě tabulky v databázi. Analytics obsahuje dotazovací jazyk pro extrahování, manipulace a vizualizaci dat.

![Vyberte Analytics](media/functions-monitoring/select-analytics.png)

![Příklad Analytics](media/functions-monitoring/analytics-traces.png)

Tady je příklad dotazu. Tato znázorňuje rozdělení požadavků za pracovního procesu za posledních 30 minut.

```
requests
| where timestamp > ago(30m) 
| summarize count() by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```

Tabulky, které jsou k dispozici jsou uvedené v **schématu** kartě v levém podokně. Můžete najít data generována volání funkce v následujících tabulkách:

* **trasování** -protokoly vytvořené modulem runtime a kódem funkce.
* **Požadavky** – jeden pro každé vyvolání funkce.
* **výjimky** – jakékoli výjimky vyvolané modulem runtime.
* **customMetrics** -počet úspěšná a neúspěšná volání, míra úspěšnosti a doba trvání.
* **customEvents** -události sledují modulem runtime, například: požadavky HTTP, které aktivují funkci.
* **čítače výkonu** -informace o výkon serverů, které funkce jsou spuštěny na.

Jiné tabulky se pro testy dostupnosti a telemetrie nebo prohlížeče klienta. Můžete implementovat vlastní telemetrii můžete přidat do nich.

V každé tabulce některá data konkrétní funkce je v `customDimensions` pole.  Například následující dotaz načte všechny trasování, které mají úroveň protokolu `Error`.

```
traces 
| where customDimensions.LogLevel == "Error"
```

Modul runtime poskytuje `customDimensions.LogLevel` a `customDimensions.Category`. Můžete zadat další pole v protokolech, které můžete psát v kódu funkce. V tématu [strukturovaná protokolování](#structured-logging) dále v tomto článku.

## <a name="configure-categories-and-log-levels"></a>Konfigurace kategorií a úrovně protokolování

Application Insights můžete použít bez jakékoli vlastní konfigurace, ale může způsobit vysoké objemy dat. výchozí konfiguraci. Pokud používáte Visual Studio Azure předplatné, může dosáhl limitu vaše data pro službu Application Insights. Zbývající část tohoto článku ukazuje, jak nakonfigurovat a upravit data, která funkcí odesílání Application Insights.

### <a name="categories"></a>Kategorie

Zahrnuje protokolovacího nástroje Azure Functions *kategorie* pro každý protokol. Kategorie označuje, která část kód runtime nebo funkce kód napsali v protokolu. 

Modul runtime funkce vytvoří protokoly, které mají počínaje "Hostitel" kategorie. Například "funkce spuštěna," "funkce spuštěna" a "funkce byla dokončena" protokoly mají kategorii "Host.Executor". 

Pokud se zapisují protokoly do vašeho kódu funkce, je jejich kategorie "Funkce".

### <a name="log-levels"></a>Úrovně protokolu

Také zahrnuje protokolovacího nástroje Azure functions *úrovně protokolování* s každou protokolu. [LogLevel](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loglevel#Microsoft_Extensions_Logging_LogLevel) je výčet a kód celé číslo označuje relativní důležitost:

|LogLevel    |Kód|
|------------|---|
|Trasování       | 0 |
|Ladění       | 1 |
|Informace | 2 |
|Upozornění     | 3 |
|Chyba       | 4 |
|Kritická    | 5 |
|Žádný        | 6 |

Úrovně protokolování `None` je vysvětleno v následující části. 

### <a name="configure-logging-in-hostjson"></a>Konfigurovat protokolování na host.json

*Host.json* souboru nakonfiguruje kolik protokolování funkce aplikace odesílá do služby Application Insights. Pro každou kategorii znamenat úroveň minimální protokolu k odeslání. Tady je příklad:

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host.Results": "Error",
        "Function": "Error",
        "Host.Aggregator": "Information"
      }
    }
  }
}
```

Tento příklad nastaví následující pravidla:

1. Pro protokoly s kategorie "Host.Results" nebo "Funkce" odeslat pouze `Error` úroveň a vyšší Application insights. V protokolech `Warning` úrovni a níže se ignorují.
2. Protokoly s kategorie hostitele. Agregátoru, odeslání pouze `Information` úroveň a vyšší Application insights. V protokolech `Debug` úrovni a níže se ignorují.
3. Pro všechny ostatní protokoly odeslat pouze `Information` úroveň a vyšší Application insights.

Hodnota kategorie v *host.json* řídí protokolování pro všechny kategorie, které začínají stejnou hodnotu. Například "hostitel" v *host.json* řídí protokolování pro "Host.General", "Host.Executor", "Host.Results" a tak dále.

Pokud *host.json* zahrnuje více kategorií, které začínají stejnou řetězec delší ty, které se splní první. Předpokládejme například, že chcete, aby vše z modulu runtime s výjimkou "Host.Aggregator" přihlašovat na `Error` při "Host.Aggregator" protokoly na úrovni `Information` úroveň:

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

Chcete-li potlačit všechny protokoly pro kategorii, můžete použít úroveň protokolu `None`. Protokoly jsou zapsány s této kategorie a není žádná úroveň protokolu nad ním.

Následující části popisují hlavní kategorie protokoly, které vytvoří modulu runtime. 

### <a name="category-hostresults"></a>Kategorie Host.Results

Tyto protokoly se zobrazí jako "požadavky" ve službě Application Insights. Indikuje úspěch nebo neúspěch funkce.

![Požadavky grafu](media/functions-monitoring/requests-chart.png)

Všechny tyto protokoly jsou zapsány v `Information` úroveň, takže pokud filtrovat v `Warning` nebo vyšší, neuvidíte žádné tato data.

### <a name="category-hostaggregator"></a>Kategorie Host.Aggregator

Tyto protokoly poskytují počty a průměry volání funkce přes [konfigurovat](#configure-the-aggregator) období času. Výchozí doba je 30 sekund nebo 1 000 výsledky, nastane dříve. 

Protokoly jsou k dispozici v **customMetrics** tabulky ve službě Application Insights. Příklady jsou číslo spustí, míra úspěšnosti a doba trvání.

![customMetrics dotazu](media/functions-monitoring/custom-metrics-query.png)

Všechny tyto protokoly jsou zapsány v `Information` úroveň, takže pokud filtrovat v `Warning` nebo vyšší, neuvidíte žádné tato data.

### <a name="other-categories"></a>Ostatních kategorií

Všechny protokoly pro kategorií než těm, které jsou už jsou k dispozici v **trasování** tabulky ve službě Application Insights.

![trasování dotazů](media/functions-monitoring/analytics-traces.png)

Všechny protokoly s kategorií, které začínají "Hostitel" se zapisují modulem runtime funkce. "Funkce Spustit" a "Funkce dokončeno" protokoly mají kategorii "Host.Executor". Pro úspěšné běží tyto protokoly jsou `Information` úroveň; výjimky se protokolují v `Error` úroveň. Modul runtime také vytvoří `Warning` úrovně protokoly, například: fronty zpráv odeslaných do poškozených fronty.

Protokoly, které jsou zapsány pomocí funkce kódu mají kategorii "Funkce" a může být jakékoli úroveň protokolu.

## <a name="configure-the-aggregator"></a>Konfigurace agregátoru

Jak je uvedeno v předchozí části, modul runtime agreguje data o spuštění funkce přes v časovém intervalu. Výchozí doba je 30 sekund nebo 1 000 běží, co nastane dříve. Toto nastavení můžete nakonfigurovat *host.json* souboru.  Tady je příklad:

```json
{
    "aggregator": {
      "batchSize": 1000,
      "flushTimeout": "00:00:30"
    }
}
```

## <a name="configure-sampling"></a>Konfigurace vzorkování

Application Insights obsahuje [vzorkování](../application-insights/app-insights-sampling.md) funkce, která může chránit z generovala příliš mnoho telemetrická data v některých případech o zátěž ve špičce. Pokud počet položek telemetrie, které překročí na určenou míru, Application Insights se začne náhodně některé z příchozích položek ignorovat. Výchozí nastavení pro maximální počet položek za sekundu, které je 5. Můžete nakonfigurovat vzorkování v *host.json*.  Tady je příklad:

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

## <a name="write-logs-in-c-functions"></a>Zapisují protokoly do funkcí jazyka C#

Zapisují protokoly do vašeho funkce kód, který se zobrazí jako trasování ve službě Application Insights.

### <a name="ilogger"></a>Objektu ILogger

Použijte [objektu ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger) parametr funkce místo `TraceWriter` parametr. Protokoly vytvořily s použitím `TraceWriter` přejděte do služby Application Insights, ale `ILogger` vám to umožní [strukturovaná protokolování](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

S `ILogger` objektu zavoláte `Log<level>` [rozšiřující metody na objektu ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loggerextensions#Methods_) vytvořit protokoly. Například následující kód zápisy `Information` protokoly s kategorie "Funkce".

```cs
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger logger)
{
    logger.LogInformation("Request for item with key={itemKey}.", id);
```

### <a name="structured-logging"></a>Strukturované protokolování

Pořadí zástupných symbolů, není jejich názvy, určuje, které parametry se používají ve zprávě protokolu. Předpokládejme například, že máte následující kód:

```csharp
string partitionKey = "partitionKey";
string rowKey = "rowKey";
logger.LogInformation("partitionKey={partitionKey}, rowKey={rowKey}", partitionKey, rowKey);
```

Pokud zachovat stejné řetězec zprávy a pořadí parametrů, výsledná text zprávy by měla mít hodnoty nesprávných místech.

Zástupné symboly jsou zpracovávány tímto způsobem, takže můžete provést strukturovaných protokolování. Application Insights ukládá dvojice název hodnota parametru kromě řetězec zprávy. Výsledek, že je zpráva argumenty pole, které můžete zadat dotaz na.

Například pokud vaše volání metody protokolovacího nástroje vypadá jako předchozí příklad, můžete dotazovat pole `customDimensions.prop__rowKey`. `prop__` Předpona se přidá k zajištění, že neexistují žádné kolize mezi poli modulu runtime přidá a polí kódu funkce přidá.

Můžete taky zadat dotaz na původní řetězec zprávy pod položkou pole `customDimensions.prop__{OriginalFormat}`.  

Zde je ukázka reprezentaci JSON `customDimensions` dat:

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

### <a name="logging-custom-metrics"></a>Metriky vlastní protokolování  

V jazyce C# skript funkce, můžete použít `LogMetric` rozšiřující metody na `ILogger` vytvořit vlastní metriky ve službě Application Insights. Zde je ukázka volání metody:

```csharp
logger.LogMetric("TestMetric", 1234); 
```

Tento kód je alternativa k volání `TrackMetric` pomocí [Application Insights API pro .NET](#custom-telemetry-in-c-functions).

## <a name="write-logs-in-javascript-functions"></a>Zapisují protokoly do funkce jazyka JavaScript

Ve funkcích Node.js, použijte `context.log` zápis protokolů. Strukturované protokolování není povoleno.

```
context.log('JavaScript HTTP trigger function processed a request.' + context.invocationId);
```

### <a name="logging-custom-metrics"></a>Metriky vlastní protokolování  

Ve funkcích Node.js, můžete použít `context.log.metric` metodu pro vytvoření vlastní metriky ve službě Application Insights. Zde je ukázka volání metody:

```javascript
context.log.metric("TestMetric", 1234); 
```

Tento kód je alternativa k volání `trackMetric` pomocí [SDK Node.js pro službu Application Insights](#custom-telemetry-in-javascript-functions).

## <a name="custom-telemetry-in-c-functions"></a>Vlastní telemetrii funkcí jazyka C#

Můžete použít [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) balíček NuGet pro odesílání dat vlastní telemetrii Application insights.

Tady je příklad kódu C#, který používá [vlastní rozhraní API telemetrie](../application-insights/app-insights-api-custom-events-metrics.md). V příkladu je knihovna tříd rozhraní .NET, ale kód Application Insights je stejný pro C# skript.

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

            // parse query parameter
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
            
            return name == null
                ? req.CreateResponse(HttpStatusCode.BadRequest, 
                    "Please pass a name on the query string or in the request body")
                : req.CreateResponse(HttpStatusCode.OK, "Hello " + name);
        }
        
        // This correllates all telemetry with the current Function invocation
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

Nemůžete volat `TrackRequest` nebo `StartOperation<RequestTelemetry>`, protože uvidíte duplicitní požadavky pro volání funkce.  Modul runtime funkce automaticky sleduje žádosti.

Není nastavený `telemetryClient.Context.Operation.Id`. Toto je globální nastavení a způsobí, že nesprávné correllation při mnoho funkcí běží současně. Místo toho vytvořte novou instanci telemetrie (`DependencyTelemetry`, `EventTelemetry`) a upravte jeho `Context` vlastnost. Pak předejte instance telemetrie do odpovídajících `Track` metodu `TelemetryClient` (`TrackDependency()`, `TrackEvent()`). Tím se zajistí, že telemetrii má správný correllation podrobnosti aktuální volání funkce.

## <a name="custom-telemetry-in-javascript-functions"></a>Vlastní telemetrii v funkce jazyka JavaScript

[Application Insights Node.js SDK](https://www.npmjs.com/package/applicationinsights) je aktuálně ve verzi beta. Tady je některé ukázkový kód, který odesílá vlastní telemetrii Application Insights:

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

    if (req.query.name || (req.body && req.body.name)) {
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
    context.done();
};
```

`tagOverrides` Sady parametrů `operation_Id` ID funkce volání. Toto nastavení umožňuje korelovat všechny automaticky vygenerují a vlastní telemetrii pro vyvolání dané funkce.

## <a name="known-issues"></a>Známé problémy

### <a name="dependencies"></a>Závislosti

Závislosti, které funkce má k jiným službám Nezobrazovat automaticky, ale můžete napsat vlastní kód, který pro závislosti. Ukázkový kód v [C# vlastní telemetrii části](#custom-telemetry-in-c-functions) ukazuje, jak. Ukázkový kód vede *aplikace mapy* ve službě Application Insights, vypadá podobně jako tento:

![Mapa aplikace](media/functions-monitoring/app-map.png)

### <a name="report-issues"></a>Sestava problémy

Ohlásit problém s Application Insights integraci funkcí, nebo zajistěte, aby návrhu nebo požadavku, [vytvořit problém v Githubu](https://github.com/Azure/Azure-Functions/issues/new).

## <a name="monitoring-without-application-insights"></a>Monitorování bez Application Insights

Doporučujeme, abyste Application Insights pro monitorování funkce, protože nabízí další data a lepší způsoby, jak analyzovat data. Ale pokud dáváte přednost integrované protokolování systému, který používá Azure Storage, můžete použít.

### <a name="logging-to-storage"></a>Protokolování do úložiště

Integrované protokolování používá účet úložiště určeného připojovací řetězec `AzureWebJobsDashboard` nastavení aplikace. Na stránce funkce aplikace, vyberte funkci a pak vyberte **monitorování** kartě a zachovat v klasickém zobrazení.

![Umožňuje přepnout do klasického zobrazení](media/functions-monitoring/switch-to-classic-view.png)

 Zobrazí seznam spuštěních funkce. Vyberte funkce provádění ke kontrole doba trvání, vstupních dat, chyb a přidružené soubory protokolu.

Pokud jste povolili službu Application Insights dříve, ale nyní chcete přejít zpět do vestavěné protokolování, zakažte Application Insights ručně a pak vyberte **monitorování** kartě. Zakázat integrace Application Insights, odstraňte nastavení aplikace APPINSIGHTS_INSTRUMENTATIONKEY.

I když **monitorování** kartě se zobrazují data Application Insights, data protokolu můžete podívat v systému souborů, pokud jste to ještě [zakázáno integrované protokolování](#disable-built-in-logging). V prostředků úložiště, přejděte na soubory, vyberte soubor služby pro funkci a potom přejděte na `LogFiles > Application > Functions > Function > your_function` k naleznete v souboru protokolu.

### <a name="real-time-monitoring"></a>Sledování v reálném čase

Soubory protokolu a relaci příkazového řádku na místní pracovní stanici pomocí dá Streamovat [rozhraní příkazového řádku Azure (CLI) 2.0](/cli/azure/install-azure-cli) nebo [prostředí Azure PowerShell](/powershell/azure/overview).  

Pro 2.0 rozhraní příkazového řádku Azure použijte následující příkazy Pokud chcete přihlásit, zvolte vaše předplatné a soubory datového proudu protokolů:

```
az login
az account list
az account set <subscriptionNameOrId>
az appservice web log tail --resource-group <resource group name> --name <function app name>
```

Pro prostředí Azure PowerShell použijte k přidání účtu Azure, zvolte si předplatné a soubory protokolu datového proudu následující příkazy:

```
PS C:\> Add-AzureAccount
PS C:\> Get-AzureSubscription
PS C:\> Get-AzureSubscription -SubscriptionName "<subscription name>" | Select-AzureSubscription
PS C:\> Get-AzureWebSiteLog -Name <function app name> -Tail
```

Další informace najdete v tématu [postup stream protokoly](../app-service/web-sites-enable-diagnostic-log.md#streamlogs).

### <a name="viewing-log-files-locally"></a>Zobrazení protokolu soubory místně

[!INCLUDE [functions-local-logs-location](../../includes/functions-local-logs-location.md)]

## <a name="next-steps"></a>Další postup

Další informace najdete v následujících materiálech:

* [Application Insights](/azure/application-insights/)
* [ASP.NET Core protokolování](/aspnet/core/fundamentals/logging/)
