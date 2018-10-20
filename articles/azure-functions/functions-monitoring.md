---
title: Monitorování Azure Functions
description: Zjistěte, jak pomocí Azure Application Insights s využitím Azure Functions pro monitorování provádění funkce.
services: functions
author: ggailey777
manager: jeconnoc
keywords: funkce azure, funkce, zpracování událostí, webhook, dynamické výpočty, architektura bez serverů
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/15/2017
ms.author: glenga
ms.openlocfilehash: e317a9c3cea800e05fbf3d2df73c124d2e7ffd23
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49457659"
---
# <a name="monitor-azure-functions"></a>Monitorování Azure Functions

## <a name="overview"></a>Přehled 

[Služba Azure Functions](functions-overview.md) nabízí integrovanou integraci s [Azure Application Insights](../application-insights/app-insights-overview.md) pro monitorovacích funkcí. Tento článek ukazuje postup při konfiguraci funkce Neodesílat telemetrická data do Application Insights.

![Průzkumník metrik Application Insights](media/functions-monitoring/metrics-explorer.png)

Funkce má také [integrované monitorování, která nepoužívá Application Insights](#monitoring-without-application-insights). Doporučujeme vám Application Insights, protože nabízí větší množství dat a lepší způsoby, jak analyzovat data.

## <a name="application-insights-pricing-and-limits"></a>Ceny Application Insights a omezení

Můžete vyzkoušet Application Insights integrace s aplikací Function App zdarma. Ale je denní limit objemu dat může zpracovat zdarma a může být dosažení tohoto limitu během testování. Azure poskytuje portál a e-mailové oznámení, pokud blíží se denního limitu.  Ale pokud byste tyto výstrahy a dosáhl limitu, nové protokoly se nezobrazí v dotazech Application Insights. Proto nezapomínejte limitu, aby se zabránilo zbytečným čas řešení problémů. Další informace najdete v tématu [Správa cen a objemů dat ve službě Application Insights](../application-insights/app-insights-pricing.md).

## <a name="enable-app-insights-integration"></a>Povolení integrace App Insights

Aplikace function App k odesílání dat do služby Application Insights je potřeba vědět Instrumentační klíč tohoto prostředku Application Insights. Klíč musí být k dispozici v nastavení aplikace s názvem APPINSIGHTS_INSTRUMENTATIONKEY.

Můžete nastavit toto připojení v [webu Azure portal](https://portal.azure.com):

* [Automaticky pro novou aplikaci function app](#new-function-app)
* [Ručně připojit prostředek App Insights](#manually-connect-an-app-insights-resource)

### <a name="new-function-app"></a>Nová aplikace funkcí

1. Přejděte do aplikace function app **vytvořit** stránky.

1. Nastavte **Application Insights** přepnout **na**.

2. Vyberte **umístění Application Insights**.

   Vyberte oblast co nejblíže oblasti vaší aplikace funkcí v [zeměpisná oblast Azure](https://azure.microsoft.com/global-infrastructure/geographies/) kam chcete data ukládat.

   ![Povolit Application Insights při vytváření aplikace funkcí](media/functions-monitoring/enable-ai-new-function-app.png)

3. Zadejte další požadované informace.

1. Vyberte **Vytvořit**.

Dalším krokem je [zakázat vestavěné protokolování](#disable-built-in-logging).

### <a name="manually-connect-an-app-insights-resource"></a>Ručně připojit prostředek App Insights 

1. Vytvořte prostředek Application Insights. Nastavte typ aplikace na **Obecné**.

   ![Vytvořte prostředek Application Insights typu Obecné](media/functions-monitoring/ai-general.png)

2. Zkopírování instrumentačního klíče ze **Essentials** stránky prostředku Application Insights. Najeďte myší konec zobrazená hodnota klíče zobrazíte **kopírování kliknutím** tlačítko.

   ![Zkopírujte Instrumentační klíč Application Insights](media/functions-monitoring/copy-ai-key.png)

1. V aplikaci function app **nastavení aplikace** stránce [přidat nastavení aplikace](functions-how-to-use-azure-function-app-settings.md#settings) kliknutím **přidat nové nastavení**. Zadejte název nové nastavení APPINSIGHTS_INSTRUMENTATIONKEY a vložte zkopírovaný Instrumentační klíč.

   ![Přidat Instrumentační klíč nastavení aplikace](media/functions-monitoring/add-ai-key.png)

1. Klikněte na **Uložit**.

## <a name="disable-built-in-logging"></a>Zakázat vestavěné protokolování

Pokud povolíte Application Insights, doporučujeme zakázat [vestavěné protokolování, která používá službu Azure storage](#logging-to-storage). Integrované protokolování se hodí při testování s malé úlohy, ale není určena pro použití v produkčním prostředí vysokým zatížením. Pro monitorování produkčního prostředí se doporučuje Application Insights. Pokud vestavěné protokolování je použít v produkčním prostředí, záznamu protokolování může být neúplné, protože omezení šířky pásma ve službě Azure Storage.

Chcete-li zakázat vestavěné protokolování, odstraňte `AzureWebJobsDashboard` nastavení aplikace. Informace o tom, jak odstranit nastavení aplikace na webu Azure Portal najdete v tématu **nastavení aplikace** část [Správa aplikace function app](functions-how-to-use-azure-function-app-settings.md#settings). Před odstraněním nastavení aplikace, ujistěte se, že žádné existující funkce ve stejné aplikaci function app je používat pro aktivační události služby Azure Storage nebo vazby.

## <a name="view-telemetry-in-monitor-tab"></a>Zobrazení telemetrických dat na kartě monitorování

Po nastavení integrace služby Application Insights jak je znázorněno v předchozích částech, můžete zobrazit telemetrická data v **monitorování** kartu.

1. Na stránce aplikace funkcí vyberte funkce, která má aspoň jednou spustili po Application Insights byla nakonfigurovaná a pak vyberte **monitorování** kartu.

   ![Vyberte kartu monitorování](media/functions-monitoring/monitor-tab.png)

2. Vyberte **aktualizovat** pravidelně, dokud se zobrazí seznam obsahující záznamy volání funkcí.

   Může trvat až 5 minut pro seznam zobrazit kvůli způsobu, jakým telemetrická data dávek klienta pro přenos do serveru. (Neplatí pro toto zpoždění [Live Metrics Stream](../application-insights/app-insights-live-stream.md). Tuto službu se připojí k hostiteli funkce načtení stránky, takže protokoly streamovaných přímo na stránku.)

   ![Vyvolání seznamu](media/functions-monitoring/monitor-tab-ai-invocations.png)

2. Pokud chcete zobrazit protokoly pro konkrétní funkci volání, vyberte **datum** odkaz sloupce pro tohoto volání.

   ![Podrobnosti volání propojení](media/functions-monitoring/invocation-details-link-ai.png)

   Na nové stránce se zobrazí výstup protokolování pro tohoto volání.

   ![Podrobnosti volání](media/functions-monitoring/invocation-details-ai.png)

Obě stránky (vyvolání seznamu a podrobností) odkazu na dotaz Application Insights Analytics, který načítá data:

![Spustit v Application Insights](media/functions-monitoring/run-in-ai.png)

![Application Insights Analytics vyvolání seznamu](media/functions-monitoring/ai-analytics-invocation-list.png)

Tyto dotazy, uvidíte, že seznamu vyvolání je omezená na poslední 30 dní, více než 20 řádků (`where timestamp > ago(30d) | take 20`) a se seznam podrobností vyvolání za posledních 30 dní bez omezení.

Další informace najdete v tématu [dotazování na telemetrická data](#query-telemetry-data) dále v tomto článku.

## <a name="view-telemetry-in-app-insights"></a>Zobrazení telemetrických dat ve službě App Insights

Otevřít Application Insights z aplikace function app na webu Azure Portal, vyberte **Application Insights** odkaz v **nakonfigurování funkcí** část aplikace function app **přehled** stránky.

![Application Insights odkazu na stránce s přehledem](media/functions-monitoring/ai-link.png)


Informace o tom, jak pomocí Application Insights, najdete v článku [dokumentace k Application Insights](https://docs.microsoft.com/azure/application-insights/). Tato část ukazuje několik příkladů toho, jak zobrazit data ve službě Application Insights. Pokud jste již obeznámeni s Application Insights, můžete přejít přímo na [části o konfiguraci a přizpůsobení telemetrická data](#configure-categories-and-log-levels).

V [Průzkumníka metrik](../application-insights/app-insights-metrics-explorer.md), můžete vytvořit grafy a výstrah na základě metrik, jako je počet volání funkce, doba spuštění a míra úspěšnosti.

![Průzkumník metrik](media/functions-monitoring/metrics-explorer.png)

Na [selhání](../application-insights/app-insights-asp-net-exceptions.md) kartu, můžete vytvořit grafy a výstrah na základě selhání funkce a server výjimky. **Název operace** je název funkce. Selhání v závislosti se nezobrazují, Pokud implementujete [vlastní telemetrii](#custom-telemetry-in-c-functions) pro závislosti.

![Počet selhání](media/functions-monitoring/failures.png)

Na [výkonu](../application-insights/app-insights-performance-counters.md) kartu, můžete analyzovat problémy s výkonem.

![Výkon](media/functions-monitoring/performance.png)

**Servery** karta zobrazuje využití prostředků a propustnost na jednom serveru. Tato data mohou být užitečné při ladění scénářů, ve kterém jsou funkce bogging dolů příslušných prostředků. Servery se označují jako **instance rolí cloudové**.

![Servery](media/functions-monitoring/servers.png)

[Live Metrics Stream](../application-insights/app-insights-live-stream.md) karta zobrazuje data metrik, jakmile je vytvořena v reálném čase.

![Živý datový proud](media/functions-monitoring/live-stream.png)

## <a name="query-telemetry-data"></a>Dotazování na telemetrická data

[Application Insights Analytics](../application-insights/app-insights-analytics.md) vám umožní přístup ke všem telemetrických dat ve formě tabulky v databázi. Analytics poskytuje dotazovací jazyk k extrakci, manipulaci a vizualizaci dat.

![Vyberte Analytics](media/functions-monitoring/select-analytics.png)

![Příklad analýz](media/functions-monitoring/analytics-traces.png)

Tady je příklad dotazu. Ten zobrazuje distribuci požadavků za pracovního procesu za posledních 30 minut.

```
requests
| where timestamp > ago(30m) 
| summarize count() by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```

Tabulky, které jsou k dispozici jsou uvedeny v **schématu** kartu v levém podokně. Můžete najít data generovaná volání funkce v následujících tabulkách:

* **trasování** – protokoly vytvořené modulem runtime a kód funkce.
* **Požadavky** – jeden pro každé vyvolání funkce.
* **výjimky** – všechny výjimky vyvolané modulem runtime.
* **customMetrics** – počet úspěšných a neúspěšných volání, míru úspěšnosti a doba trvání.
* **customEvents** -události sledován pomocí funkce modulu runtime, například: požadavky HTTP, které aktivují funkce.
* **čítače výkonu** – informace o výkonu, které funkce jsou spuštěny na servery.

Ostatní tabulky jsou pro testy dostupnosti a/prohlížeče klienta telemetrie. Můžete implementovat vlastní telemetrii můžete přidat do nich.

V každé tabulce některá data konkrétních funkcí je `customDimensions` pole.  Například následující dotaz načte všechna trasování, které mají úroveň protokolu `Error`.

```
traces 
| where customDimensions.LogLevel == "Error"
```

Modul runtime poskytuje `customDimensions.LogLevel` a `customDimensions.Category`. Můžete zadat další pole v protokolech, které napíšete kód vaší funkce. Zobrazit [strukturované protokolování](#structured-logging) dále v tomto článku.

## <a name="configure-categories-and-log-levels"></a>Konfigurace kategorií a úrovně protokolování

Application Insights můžete používat bez vlastní konfigurace, ale může způsobit velké objemy dat ve výchozí konfiguraci. Pokud používáte předplatné sady Visual Studio v Azure, může dosáhnete limitu vaše data Application Insights. Zbývající část tohoto článku ukazuje, jak nakonfigurovat a upravit data, která vaše funkce posílat do Application Insights.

### <a name="categories"></a>Kategorie

Zahrnuje protokolovacího nástroje Azure Functions *kategorie* pro každý protokol. Kategorie určuje, které části kódu modulu runtime nebo kód vaší funkce zapsáno do protokolu. 

Modul runtime služby Functions vytvářejí protokoly, které mají kategorii začíná "Hostitel". Například "funkci začít," "spuštění funkce" a "funkce dokončena" protokoly mají kategorii "Host.Executor". 

Pokud se zapisují protokoly do vašeho kódu funkce jejich kategorie je "Funkce".

### <a name="log-levels"></a>Úroveň protokolu

Zahrnuje také protokolovacího nástroje Azure functions *úrovně protokolování* s každou protokolu. [LogLevel](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loglevel#Microsoft_Extensions_Logging_LogLevel) je výčet a kód celé číslo označuje relativní význam:

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

### <a name="configure-logging-in-hostjson"></a>Konfigurovat protokolování ve host.json

*Host.json* soubor nastaví jaká míra protokolování aplikace function app se odešle do služby Application Insights. Pro každou kategorii určujete minimální úroveň protokolování k odeslání. Tady je příklad:

#### <a name="functions-version-1"></a>Funkce verze 1 
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

#### <a name="functions-version-2"></a>Funkce verze 2 
Funkce v2 teď používá [hierarchie filtru protokolování .NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering). 
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

V tomto příkladu nastaví následující pravidla:

1. Pro protokoly s kategorií "Host.Results" nebo "Funkce" odeslat pouze `Error` úroveň a vyšší do Application Insights. Protokoly pro `Warning` úrovně a níže jsou ignorovány.
2. Pro protokoly s kategorií Host.Aggregator odesílat všechny protokoly do služby Application Insights. `Trace` Úroveň protokolu je stejný jako co volání některých protokolovacích nástrojů `Verbose`, ale použít `Trace` v *host.json* souboru.
3. Pro všechny ostatní protokoly odeslat pouze `Information` úroveň a vyšší do Application Insights.

Hodnota kategorie v *host.json* řídí protokolování všech kategorií, které začínají stejnou hodnotu. Například "hostitel" na *host.json* řídí protokolování pro "Host.General", "Host.Executor", "Host.Results" a tak dále.

Pokud *host.json* zahrnuje více kategorií, které začínají stejný řetězec delší ty budou odpovídat nejprve. Předpokládejme například, že chcete, aby všechno z modulu runtime s výjimkou "Host.Aggregator" protokolu `Error` úroveň, ale chcete "Host.Aggregator" protokolu `Information` úroveň:

#### <a name="functions-version-1"></a>Funkce verze 1 
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

#### <a name="functions-version-2"></a>Funkce verze 2 
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

Chcete-li potlačit všechny protokoly pro kategorii, můžete použít úroveň protokolu `None`. Žádné protokoly se zapisují do této kategorie a neexistuje žádná úroveň protokolu nad ním.

Následující části popisují hlavní kategorie protokoly, které modul runtime vytvoří. 

### <a name="category-hostresults"></a>Kategorie Host.Results

Tyto protokoly se zobrazí jako "požadavky" v Application Insights. Indikuje úspěch nebo neúspěch funkce.

![Žádosti o grafu](media/functions-monitoring/requests-chart.png)

Všechny tyto protokoly se zapíšou ve `Information` úroveň, takže pokud filtr v `Warning` nebo vyšší, některé z těchto dat neuvidíte.

### <a name="category-hostaggregator"></a>Kategorie Host.Aggregator

Tyto protokoly poskytuje přes počty a průměry obsahující záznamy volání funkcí [konfigurovatelné](#configure-the-aggregator) období času. Výchozí doba je 30 sekund nebo 1 000 výsledky, podle toho, co nastane dřív. 

Protokoly jsou k dispozici v **customMetrics** tabulky ve službě Application Insights. Mezi příklady patří počet běhů, míru úspěšnosti a doba trvání.

![customMetrics dotazu](media/functions-monitoring/custom-metrics-query.png)

Všechny tyto protokoly se zapíšou ve `Information` úroveň, takže pokud filtr v `Warning` nebo vyšší, některé z těchto dat neuvidíte.

### <a name="other-categories"></a>Další kategorie

Všechny protokoly pro kategorie než těm, které jsou již jsou k dispozici v **trasy** tabulky ve službě Application Insights.

![trasování dotazů](media/functions-monitoring/analytics-traces.png)

Modul runtime Functions jsou zapsány všechny protokoly s kategorií, které začínají řetězcem "Hostitel". "Funkce Spustit" a "funkce Completed (dokončeno) protokoly mají kategorii"Host.Executor". U úspěšných spuštění tyto protokoly jsou `Information` úroveň; výjimky jsou protokolovány v `Error` úroveň. Modul runtime vytvoří také `Warning` úroveň protokoly, například: fronty zprávy odeslané do fronty poškozené.

Protokoly vytvořené kód vaší funkce mají kategorii "Funkce" a může být libovolná úroveň protokolu.

## <a name="configure-the-aggregator"></a>Konfigurace agregátoru

Jak je uvedeno v předchozí části, modul runtime agreguje data o provádění funkcí po určitou dobu. Výchozí doba je 30 sekund nebo 1 000 spuštění, co nastane dříve. Toto nastavení můžete nakonfigurovat *host.json* souboru.  Tady je příklad:

```json
{
    "aggregator": {
      "batchSize": 1000,
      "flushTimeout": "00:00:30"
    }
}
```

## <a name="configure-sampling"></a>Konfigurace odběru vzorků

Application Insights má [vzorkování](../application-insights/app-insights-sampling.md) funkce, která můžete chránit z vytváření příliš mnoho telemetrických dat v některých případech z zátěž ve špičce. Po Míra příchozích telemetrických dat překročí zadanou prahovou hodnotu, Application Insights začne náhodně některé položky, příchozí ignorovat. Výchozí nastavení pro maximální počet položek za sekundu, které je 5. Můžete nakonfigurovat vzorkování v *host.json*.  Tady je příklad:

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

Zapisují protokoly do kódu funkce, které se zobrazují jako trasování v Application Insights.

### <a name="ilogger"></a>ILogger

Použití [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger) parametr vašich funkcí místo `TraceWriter` parametru. Protokoly vytvořené s použitím `TraceWriter` přejít na Application Insights, ale `ILogger` umožňuje provádět [strukturované protokolování](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Pomocí `ILogger` objektu zavoláte `Log<level>` [rozšiřující metody na objektu ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.loggerextensions#methods) vytvořit protokoly. Například následující kód zápisy `Information` protokoly z kategorie "Funkce".

```cs
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger logger)
{
    logger.LogInformation("Request for item with key={itemKey}.", id);
```

### <a name="structured-logging"></a>Strukturované protokolování

Pořadí zástupné symboly, nikoli jejich názvy, určuje, jaké parametry se používají ve zprávě protokolu. Předpokládejme například, že máte následující kód:

```csharp
string partitionKey = "partitionKey";
string rowKey = "rowKey";
logger.LogInformation("partitionKey={partitionKey}, rowKey={rowKey}", partitionKey, rowKey);
```

Je-li zachovat stejný řetězec zprávy a pořadí parametrů, výsledný text zprávy by mít hodnoty nesprávných místech.

Zástupné symboly jsou zpracovány tímto způsobem tak, aby vám pomůžou strukturované protokolování. Application Insights ukládají dvojice název hodnota parametru kromě řetězec zprávy. Výsledek, že je zpráva argumentů pole, které můžete zadat dotaz na.

Například pokud vaše volání metody protokolovacího nástroje vypadá podobně jako v předchozím příkladu, můžete dotazovat na pole `customDimensions.prop__rowKey`. `prop__` Přidána předpona přidá k zajištění, že neexistují žádné kolize mezi poli modulu runtime přidá pole a kód vaší funkce.

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

### <a name="logging-custom-metrics"></a>Protokolování vlastních metrik  

Ve skriptu funkcí jazyka C#, můžete použít `LogMetric` rozšiřující metody na `ILogger` vytvořit vlastní metriky v Application Insights. Tady je ukázka volání metody:

```csharp
logger.LogMetric("TestMetric", 1234); 
```

Tento kód se o alternativu k volání `TrackMetric` pomocí [rozhraní API Application Insights pro .NET](#custom-telemetry-in-c-functions).

## <a name="write-logs-in-javascript-functions"></a>Zapisují protokoly do funkce jazyka JavaScript

Ve funkcích Node.js pomocí `context.log` zápis protokolů. Není povoleno strukturované protokolování.

```
context.log('JavaScript HTTP trigger function processed a request.' + context.invocationId);
```

### <a name="logging-custom-metrics"></a>Protokolování vlastních metrik  

Ve funkcích Node.js, můžete použít `context.log.metric` metodu pro vytvoření vlastní metriky v Application Insights. Tady je ukázka volání metody:

```javascript
context.log.metric("TestMetric", 1234); 
```

Tento kód se o alternativu k volání `trackMetric` pomocí [sady Node.js SDK pro Application Insights](#custom-telemetry-in-javascript-functions).

## <a name="custom-telemetry-in-c-functions"></a>Vlastní telemetrická data v funkcí jazyka C#

Můžete použít [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) balíčku NuGet k odesílání vlastních telemetrických dat do služby Application Insights.

Tady je příklad kódu jazyka C#, který používá [vlastní API telemetrie](../application-insights/app-insights-api-custom-events-metrics.md). V příkladu se pro knihovny tříd .NET, ale kódu Application Insights je stejný pro skript jazyka C#.

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

Nevolejte `TrackRequest` nebo `StartOperation<RequestTelemetry>`, protože uvidíte duplicitní žádosti pro volání funkce.  Modul runtime služby Functions automaticky sleduje žádosti.

Nenastavujte `telemetryClient.Context.Operation.Id`. Toto je globální nastavení a způsobí nesprávné correllation při mnoho funkcí spuštěných současně. Místo toho vytvořte novou instanci telemetrie (`DependencyTelemetry`, `EventTelemetry`) a upravte její `Context` vlastnost. Předat instanci telemetrická data do odpovídajících `Track` metoda `TelemetryClient` (`TrackDependency()`, `TrackEvent()`). Tím se zajistí, že telemetrická data má correllation správné údaje pro aktuální volání funkce.

## <a name="custom-telemetry-in-javascript-functions"></a>Vlastní telemetrická data do funkce jazyka JavaScript

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

`tagOverrides` Sady parametrů `operation_Id` ID funkce volání. Toto nastavení umožňuje korelovat všechny automaticky vygenerují a vlastní telemetrická data pro volání dané funkce.

## <a name="known-issues"></a>Známé problémy

### <a name="dependencies"></a>Závislosti

Závislosti, které funkce má k jiným službám automaticky nezobrazují, ale můžete napsat vlastní kód k zobrazení závislostí. Ukázkový kód v [jazyka C# vlastní telemetrii části](#custom-telemetry-in-c-functions) ukazuje, jak. Vzorový kód vede *Mapa aplikace* ve službě Application Insights, která vypadá takto:

![Mapa aplikace](media/functions-monitoring/app-map.png)

### <a name="report-issues"></a>Hlásit problémy

Hlášení problémů s integrací služby Application Insights ve funkcích, nebo aby návrh nebo žádost [vytvoření problému v Githubu](https://github.com/Azure/Azure-Functions/issues/new).

## <a name="monitoring-without-application-insights"></a>Monitorování bez Application Insights

Doporučujeme vám Application Insights pro monitorování funkcí, protože nabízí větší množství dat a lepší způsoby, jak analyzovat data. Ale pokud chcete systém vestavěné protokolování, který používá službu Azure Storage, můžete nadále používat.

### <a name="logging-to-storage"></a>Protokolování do úložiště

Integrované protokolování používá účet úložiště zadaný v připojovacím řetězcem `AzureWebJobsDashboard` nastavení aplikace. Na stránce funkce aplikace, vyberte funkci a pak vyberte **monitorování** kartu a zachovat v klasickém zobrazení.

![Přepnout do klasického zobrazení](media/functions-monitoring/switch-to-classic-view.png)

 Můžete získat seznam provádění funkcí. Vyberte funkce spuštění do doby trvání, vstupní data, chyby a přidružené soubory protokolu.

Pokud jste povolili službu Application Insights dříve, ale nyní chcete přejít zpět na vestavěné protokolování, zakažte Application Insights ručně a pak vyberte **monitorování** kartu. Pokud chcete zakázat integraci Application Insights, odstraňte nastavení aplikace, které APPINSIGHTS_INSTRUMENTATIONKEY.

I v případě, **monitorování** karta zobrazuje data Application Insights, můžete zobrazit data protokolu v systému souborů, pokud jste tak dosud [zakázané vestavěné protokolování](#disable-built-in-logging). V prostředku úložiště, přejděte k souborům, vyberte soubor službu pro funkci a potom přejděte ke `LogFiles > Application > Functions > Function > your_function` na najdete v souboru protokolu.

### <a name="real-time-monitoring"></a>Monitorování v reálném čase

Soubory protokolu pro relaci příkazového řádku na místní pracovní stanici pomocí můžete Streamovat [rozhraní příkazového řádku Azure (CLI)](/cli/azure/install-azure-cli) nebo [prostředí Azure PowerShell](/powershell/azure/overview).  

Azure CLI použijte následující příkazy přihlásit, zvolte předplatné a datový proud protokolů:

```
az login
az account list
az account set <subscriptionNameOrId>
az webapp log tail --resource-group <resource group name> --name <function app name>
```

Pro prostředí Azure PowerShell použijte následující příkazy pro přidání účtu Azure, zvolte předplatné a datový proud protokolů:

```
PS C:\> Add-AzureAccount
PS C:\> Get-AzureSubscription
PS C:\> Get-AzureSubscription -SubscriptionName "<subscription name>" | Select-AzureSubscription
PS C:\> Get-AzureWebSiteLog -Name <function app name> -Tail
```

Další informace najdete v tématu [jak streamování protokolů](../app-service/web-sites-enable-diagnostic-log.md#streamlogs).

### <a name="viewing-log-files-locally"></a>Zobrazení protokolů místně

[!INCLUDE [functions-local-logs-location](../../includes/functions-local-logs-location.md)]

## <a name="next-steps"></a>Další postup

Další informace najdete v následujících materiálech:

* [Application Insights](/azure/application-insights/)
* [ASP.NET Core protokolování](/aspnet/core/fundamentals/logging/)
