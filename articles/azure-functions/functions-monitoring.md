---
title: Monitorování Azure Functions
description: Naučte se používat Azure Application Insights s Azure Functions k monitorování provádění funkcí.
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.topic: conceptual
ms.date: 04/04/2019
ms.custom: devx-track-csharp, fasttrack-edit
ms.openlocfilehash: 239d1da028a06d4272ed9b22b624413394aa142f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88212990"
---
# <a name="monitor-azure-functions"></a>Monitorování Azure Functions

[Azure Functions](functions-overview.md) nabízí integrovanou integraci s [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) k monitorování funkcí. V tomto článku se dozvíte, jak nakonfigurovat Azure Functions k odeslání souborů protokolu generovaných systémem do Application Insights.

Doporučujeme použít Application Insights, protože shromažďuje data o protokolech, výkonu a chybách. Automaticky detekuje anomálie ve výkonu a zahrnuje výkonné analytické nástroje, které vám pomůžou diagnostikovat problémy a pochopit, jak se vaše funkce používají. Je navržena tak, aby pomáhala průběžně vylepšovat výkon a možnosti využití. Můžete dokonce použít Application Insights během vývoje projektu místní funkce aplikace Function App. Další informace najdete v tématu [co je Application Insights?](../azure-monitor/app/app-insights-overview.md).

Jelikož je požadovaná instrumentace Application Insights integrovaná do Azure Functions, potřebujete k připojení aplikace Function App k Application Insights prostředku platný Instrumentation Key. Klíč instrumentace by se měl přidat do nastavení vaší aplikace, když se v Azure vytvoří prostředek Function App. Pokud vaše aplikace Function ještě tento klíč nemá, můžete [ji nastavit ručně](#enable-application-insights-integration).  

## <a name="application-insights-pricing-and-limits"></a>Application Insights ceny a omezení

Můžete si vyzkoušet Application Insights integraci s Azure Functions zdarma. K dispozici je denní limit pro množství dat, která je možné zpracovat zdarma. Tento limit se může vyrazit během testování. Azure poskytuje přístup k portálu a e-mailovým oznámením při přístupu k dennímu limitu. Pokud jste tyto výstrahy nepřišli a dosáhli jste limitu, nové protokoly se v Application Insightsch dotazech nezobrazují. Nezapomeňte omezit omezení, aby nedocházelo k zbytečnému času řešení potíží. Další informace najdete v tématu [Správa cen a objemu dat v Application Insights](../azure-monitor/app/pricing.md).

> [!IMPORTANT]
> Application Insights má funkci [vzorkování](../azure-monitor/app/sampling.md) , která vám může chránit při vytváření příliš velkého množství dat telemetrie při dokončeném provádění v době špičky zatížení. Vzorkování je ve výchozím nastavení povolené. Pokud se zdá, že chybí data, možná budete muset upravit nastavení vzorkování tak, aby vyhovovalo vašemu konkrétnímu scénáři monitorování. Další informace najdete v tématu [Konfigurace vzorkování](#configure-sampling).

Úplný seznam funkcí Application Insights, které jsou k dispozici pro aplikaci Function App, je podrobně popsán v [Application Insights pro Azure Functions podporované funkce](../azure-monitor/app/azure-functions-supported-features.md).

## <a name="view-telemetry-in-monitor-tab"></a>Zobrazit telemetrii na kartě monitorování

S [povolenou integrací Application Insights](#enable-application-insights-integration)můžete zobrazit data telemetrie na kartě **monitorování** .

1. Na stránce Function App vyberte po nakonfigurování Application Insights funkci, která byla alespoň jednou spuštěna. Pak v levém podokně vyberte **monitor** . Vyberte možnost **aktualizovat** pravidelně, dokud se nezobrazí seznam vyvolání funkce.

   ![Seznam volání](media/functions-monitoring/monitor-tab-ai-invocations.png)

    > [!NOTE]
    > Může trvat až pět minut, než se seznam objeví, zatímco klient telemetrie data pro přenos na server vystaví. Zpoždění se nevztahuje na [Live Metrics Stream](../azure-monitor/app/live-stream.md). Tato služba se při načtení stránky připojí k hostiteli Functions, takže protokoly se streamují přímo na stránku.

1. Chcete-li zobrazit protokoly pro konkrétní vyvolání funkce, vyberte pro toto vyvolání odkaz na sloupec **Datum (UTC)** . Výstup protokolování pro toto vyvolání se zobrazí na nové stránce.

   ![Podrobnosti vyvolání](media/functions-monitoring/invocation-details-ai.png)

1. Zvolením možnosti **Spustit v Application Insights** zobrazíte zdroj dotazu, který načte data protokolu Azure monitor v protokolu Azure. Pokud Azure Log Analytics ve vašem předplatném používáte poprvé, budete vyzváni k jeho povolení.

1. Po povolení Log Analytics se zobrazí následující dotaz. Můžete vidět, že výsledky dotazu jsou omezené na posledních 30 dní ( `where timestamp > ago(30d)` ). Kromě toho výsledky zobrazují maximálně 20 řádků ( `take 20` ). Naopak seznam podrobností o vyvolání pro vaši funkci je za posledních 30 dnů bez omezení.

   ![Seznam volání analýzy Application Insights](media/functions-monitoring/ai-analytics-invocation-list.png)

Další informace najdete v části [dotazování na data telemetrie](#query-telemetry-data) dále v tomto článku.

## <a name="view-telemetry-in-application-insights"></a>Zobrazit telemetrii v Application Insights

Pokud chcete Application Insights otevřít z aplikace Function App v Azure Portal, vyberte **Application Insights** v části **Nastavení** na levé stránce. Pokud Application Insights u vašeho předplatného používáte poprvé, zobrazí se vám výzva k jeho povolení: vyberte **zapnout Application Insights**a pak na další stránce vyberte **použít** .

![Otevření Application Insights na stránce s přehledem aplikace Function App](media/functions-monitoring/ai-link.png)

Informace o tom, jak používat Application Insights, najdete v [dokumentaci k Application Insights](/azure/application-insights/). V této části jsou uvedeny některé příklady, jak zobrazit data v Application Insights. Pokud už jste obeznámeni s Application Insights, můžete přejít přímo k [oddílům, jak nakonfigurovat a přizpůsobit data telemetrie](#configure-categories-and-log-levels).

![Karta Přehled Application Insights](media/functions-monitoring/metrics-explorer.png)

Následující oblasti Application Insights mohou být užitečné při vyhodnocování chování, výkonu a chyb ve vašich funkcích:

| Prověřování | Description |
| ---- | ----------- |
| **[Selhání](../azure-monitor/app/asp-net-exceptions.md)** |  Vytvářejte grafy a výstrahy na základě selhání funkcí a výjimek serveru. **Název operace** je název funkce. Pokud neimplementujete vlastní telemetrie pro závislosti, neobjeví se chyby v závislostech. |
| **[Výkon](../azure-monitor/app/performance-counters.md)** | Analyzujte problémy s výkonem zobrazením využití prostředků a propustnosti na **instance rolí cloudu**. Tato data můžou být užitečná pro scénáře ladění, kde funkce bogging své základní prostředky. |
| **[Metriky](../azure-monitor/platform/metrics-charts.md)** | Vytváření grafů a výstrah založených na metrikách. Metriky zahrnují počet vyvolání funkcí, dobu provádění a míru úspěšnosti. |
| **[Živé metriky    ](../azure-monitor/app/live-stream.md)** | Umožňuje zobrazit data metrik, která se vytvářejí téměř v reálném čase. |

## <a name="query-telemetry-data"></a>Dotazování dat telemetrie

[Application Insights Analytics](../azure-monitor/log-query/log-query-overview.md) vám umožní přístup ke všem datům telemetrie ve formě tabulek v databázi. Analýza poskytuje dotazovací jazyk pro extrakci, manipulaci a vizualizaci dat. 

Vyberte **protokoly** pro prozkoumání protokolovaných událostí nebo dotazování na ně.

![Příklad analýzy](media/functions-monitoring/analytics-traces.png)

Tady je příklad dotazu, který zobrazuje distribuci požadavků za pracovní proces za posledních 30 minut.

<pre>
requests
| where timestamp > ago(30m) 
| summarize count() by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
</pre>

Tabulky, které jsou k dispozici, jsou zobrazeny na kartě **schéma** na levé straně. Data generovaná pomocí volání funkcí najdete v následujících tabulkách:

| Tabulka | Popis |
| ----- | ----------- |
| **trasování** | Protokoly vytvořené modulem runtime a kódem funkce. |
| **požadavky** | Jedna žádost pro každé vyvolání funkce. |
| **výjimek** | Jakékoli výjimky vyvolané modulem runtime. |
| **customMetrics** | Počet úspěšných a neúspěšných vyvolání, míra úspěšnosti a trvání. |
| **customEvents** | Události sledované modulem runtime, například: požadavky HTTP, které aktivují funkci. |
| **Čítače výkonu** | Informace o výkonu serverů, na kterých jsou spuštěny funkce. |

Ostatní tabulky jsou k dispozici pro testy dostupnosti a telemetrie klientů a prohlížečů. Můžete implementovat vlastní telemetrii a přidat do nich data.

V každé tabulce jsou některá data specifická pro konkrétní funkce v `customDimensions` poli.  Například následující dotaz načte všechna trasování, která mají úroveň protokolu `Error` .

<pre>
traces 
| where customDimensions.LogLevel == "Error"
</pre>

Modul runtime poskytuje `customDimensions.LogLevel` pole a `customDimensions.Category` . V protokolech můžete zadat další pole, která zapíšete do kódu funkce. Viz [strukturované protokolování](#structured-logging) dále v tomto článku.

## <a name="configure-categories-and-log-levels"></a>Konfigurovat kategorie a úrovně protokolu

Application Insights můžete použít bez vlastní konfigurace. Výchozí konfigurace může mít za následek velké objemy dat. Pokud používáte předplatné sady Visual Studio Azure, můžete pro Application Insights použít svůj limit dat. Později v tomto článku se dozvíte, jak nakonfigurovat a přizpůsobit data, která vaše funkce odesílají do Application Insights. V případě aplikace Function App se protokolování nakonfiguruje v [host.jsv] souboru.

### <a name="categories"></a>Kategorie

Protokolovací nástroj Azure Functions zahrnuje *kategorii* pro každý protokol. Kategorie označuje, která část běhového kódu nebo kód vaší funkce zapsaly protokol. Následující graf popisuje hlavní kategorie protokolů, které modul runtime vytvoří. 

| Kategorie | Popis |
| ----- | ----- | 
| Host.Results | Tyto protokoly se zobrazují jako **požadavky** v Application Insights. Označují úspěch nebo neúspěch funkce. Všechny tyto protokoly jsou zapisovány na `Information` úrovni. Pokud filtrujete `Warning` nad nebo výše, nezobrazí se žádná z těchto dat. |
| Host. agregátor | Tyto protokoly poskytují počty a průměry volání funkcí v [konfigurovatelném](#configure-the-aggregator) časovém intervalu. Výchozí doba je 30 sekund nebo 1 000 výsledků, podle toho, co nastane dřív. Protokoly jsou k dispozici v tabulce **customMetrics** v Application Insights. Jedná se o počet spuštění, úspěšnost a dobu trvání. Všechny tyto protokoly jsou zapisovány na `Information` úrovni. Pokud filtrujete `Warning` nad nebo výše, nezobrazí se žádná z těchto dat. |

Všechny protokoly pro jiné kategorie, než jsou, jsou k dispozici v tabulce **trasování** v Application Insights.

Všechny protokoly s kategoriemi, které začínají, `Host` jsou zapsány modulem runtime Functions. **Funkce začala** a **funkce dokončených** protokolů měla kategorii `Host.Executor` . V případě úspěšného spuštění jsou tyto protokoly na `Information` úrovni. Výjimky jsou protokolovány na `Error` úrovni. Modul runtime také vytváří `Warning` protokoly úrovně, například: fronty odeslané do fronty nepoškozených zpráv.

Modul runtime Functions vytvoří protokoly s kategorií, která začíná na "host". V protokolech verze 1. x, `function started` , `function executed` a `function completed` mají kategorii `Host.Executor` . Od verze 2. x mají tyto protokoly kategorii `Function.<YOUR_FUNCTION_NAME>` .

Pokud zapisujete protokoly do kódu funkce, kategorie je `Function.<YOUR_FUNCTION_NAME>.User` a může být libovolná úroveň protokolu. Ve verzi 1. x modulu runtime Functions je kategorie `Function` .

### <a name="log-levels"></a>Úrovně protokolování

Protokolovací nástroj Azure Functions zahrnuje i *úroveň protokolu* s každým protokolem. [LogLevel](/dotnet/api/microsoft.extensions.logging.loglevel) je výčet a celočíselný kód označuje relativní důležitost:

|LogLevel    |Kód|
|------------|---|
|Trasování       | 0 |
|Ladit       | 1 |
|Informace | 2 |
|Upozornění     | 3 |
|Chyba       | 4 |
|Kritické    | 5 |
|Žádné        | 6 |

Úroveň protokolu `None` je vysvětleno v další části. 

### <a name="log-configuration-in-hostjson"></a>Protokolovat konfiguraci v host.jsna

[host.jsv] souboru nakonfiguruje, kolik protokolování aplikace Function App odesílá do Application Insights. Pro každou kategorii označíte minimální úroveň protokolu k odeslání. Existují dva příklady: první příklad cílí na [verzi 2. x a novější](functions-versions.md#version-2x) z modulu runtime Functions (s .NET Core) a druhým příkladem pro modul runtime verze 1. x.

### <a name="version-2x-and-higher"></a>Verze 2. x a vyšší

Verze v2. x a novější verze modulu runtime Functions používají [hierarchii filtrů protokolování .NET Core](/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering). 

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

### <a name="version-1x"></a>Verze 1. x

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

Tento příklad nastavuje následující pravidla:

* Pro protokoly s kategorií `Host.Results` nebo můžete `Function` Odeslat `Error` Application Insights jenom úroveň a vyšší. Protokoly pro `Warning` úroveň a níže jsou ignorovány.
* Pro protokoly s kategorií `Host.Aggregator` odešlete všechny protokoly do Application Insights. `Trace`Úroveň protokolu je stejná jako u některých volání protokolovacích `Verbose` nástrojů, ale používá se `Trace` v [host.jsv] souboru.
* Pro všechny ostatní protokoly odešlete `Information` Application Insights jenom úroveň a vyšší.

Hodnota kategorie v [host.js] řídí protokolování pro všechny kategorie, které začínají stejnou hodnotou. `Host` v [host.js] ovládací prvky pro protokolování `Host.General` , `Host.Executor` , `Host.Results` a tak dále.

Pokud [host.jsna] zahrnuje více kategorií, které začínají stejným řetězcem, budou se nejprve shodovat. Předpokládejme, že chcete, aby vše z modulu runtime s výjimkou `Host.Aggregator` protokolování na `Error` úrovni, ale chcete `Host.Aggregator` se přihlásit na `Information` úrovni:

### <a name="version-2x-and-later"></a>Verze 2. x a novější

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

### <a name="version-1x"></a>Verze 1. x 

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

Chcete-li potlačit všechny protokoly pro kategorii, můžete použít úroveň protokolu `None` . S touto kategorií nejsou zapsány žádné protokoly a nad ní není žádná úroveň protokolu.

## <a name="configure-the-aggregator"></a>Konfigurace Agregátoru

Jak je uvedeno v předchozí části, modul runtime agreguje data o spouštěních funkcí v časovém intervalu. Výchozí doba je 30 sekund nebo 1 000 spuštění, podle toho, co nastane dřív. Toto nastavení můžete nakonfigurovat v [host.js] souboru.  Tady je příklad:

```json
{
    "aggregator": {
      "batchSize": 1000,
      "flushTimeout": "00:00:30"
    }
}
```

## <a name="configure-sampling"></a>Konfigurace vzorkování

Application Insights má funkci [vzorkování](../azure-monitor/app/sampling.md) , která vám může chránit při vytváření příliš velkého množství dat telemetrie při dokončeném provádění v době špičky zatížení. Když frekvence příchozího provádění překročí zadanou prahovou hodnotu, Application Insights začne náhodně ignorovat některé příchozí spuštění. Výchozí nastavení maximálního počtu spuštění za sekundu je 20 (pět ve verzi 1. x). Vzorkování můžete nakonfigurovat v [host.jszapnuto](./functions-host-json.md#applicationinsights).  Tady je příklad:

### <a name="version-2x-and-later"></a>Verze 2. x a novější

```json
{
  "logging": {
    "applicationInsights": {
      "samplingSettings": {
        "isEnabled": true,
        "maxTelemetryItemsPerSecond" : 20,
        "excludedTypes": "Request"
      }
    }
  }
}
```

Ve verzi 2. x můžete vyloučit určité typy telemetrie z vzorkování. V předchozím příkladu jsou data typu `Request` vyloučena z vzorkování. Tím se zajistí, že se zaprotokolují *všechny* provádění funkcí (požadavky), zatímco jiné typy telemetrie nadále podléhají vzorkování.

### <a name="version-1x"></a>Verze 1. x 

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

## <a name="write-logs-in-c-functions"></a>Zápis protokolů ve funkcích jazyka C#

Protokoly můžete zapsat v kódu funkce, který se zobrazí jako trasování v Application Insights.

### <a name="ilogger"></a>ILogger

Místo parametru použijte ve svých funkcích parametr [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger) `TraceWriter` . Protokoly vytvořené pomocí možnosti `TraceWriter` Přejít na Application Insights, ale `ILogger` umožňují [strukturované protokolování](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

S `ILogger` objektem zavoláte `Log<level>` [metody rozšíření v ILogger](/dotnet/api/microsoft.extensions.logging.loggerextensions#methods) k vytváření protokolů. Následující kód zapisuje `Information` protokoly do kategorie Functions. <YOUR_FUNCTION_NAME>. Uživatel. "

```cs
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger logger)
{
    logger.LogInformation("Request for item with key={itemKey}.", id);
```

### <a name="structured-logging"></a>Strukturované protokolování

Pořadí zástupných symbolů, nikoli jejich názvů, určuje, které parametry se použijí ve zprávě protokolu. Předpokládejme, že máte následující kód:

```csharp
string partitionKey = "partitionKey";
string rowKey = "rowKey";
logger.LogInformation("partitionKey={partitionKey}, rowKey={rowKey}", partitionKey, rowKey);
```

Pokud zachováte stejný řetězec zprávy a obrátíte pořadí parametrů, výsledný text zprávy by měl mít hodnoty na nesprávných místech.

Zástupné symboly jsou zpracovávány tímto způsobem, aby bylo možné provádět strukturované protokolování. Application Insights ukládá páry parametr název-hodnota a řetězec zprávy. Výsledkem je, že se argumenty zprávy stanou poli, se kterými se můžete dotazovat.

Pokud vaše volání metody protokolovacího nástroje vypadá jako v předchozím příkladu, můžete zadat dotaz na pole `customDimensions.prop__rowKey` . `prop__`Je přidána předpona, aby se zajistilo, že mezi poli, které modul runtime přidává, nedochází k žádné kolizi, které přidávají kód funkce.

Můžete také zadat dotaz na původní řetězec zprávy odkazem na pole `customDimensions.prop__{OriginalFormat}` .  

Tady je ukázková reprezentace dat ve formátu JSON `customDimensions` :

```json
{
  "customDimensions": {
    "prop__{OriginalFormat}":"C# Queue trigger function processed: {message}",
    "Category":"Function",
    "LogLevel":"Information",
    "prop__message":"c9519cbf-b1e6-4b9b-bf24-cb7d10b1bb89"
  }
}
```

### <a name="custom-metrics-logging"></a>Protokolování vlastních metrik

Ve skriptových funkcích jazyka C# lze pomocí `LogMetric` metody rozšíření pro `ILogger` vytvořit vlastní metriky v nástroji Application Insights. Zde je příklad volání metody:

```csharp
logger.LogMetric("TestMetric", 1234);
```

Tento kód je alternativou pro volání `TrackMetric` pomocí rozhraní Application Insights API pro rozhraní .NET.

## <a name="write-logs-in-javascript-functions"></a>Zápis protokolů ve funkcích JavaScriptu

V Node.js funkce použijte `context.log` k zápisu protokolů. Strukturované protokolování není povoleno.

```
context.log('JavaScript HTTP trigger function processed a request.' + context.invocationId);
```

### <a name="custom-metrics-logging"></a>Protokolování vlastních metrik

Pokud používáte [verzi 1. x](functions-versions.md#creating-1x-apps) modulu runtime Functions, Node.js funkce mohou použít `context.log.metric` metodu k vytváření vlastních metrik v Application Insights. Tato metoda není aktuálně podporovaná ve verzi 2. x a novější. Zde je příklad volání metody:

```javascript
context.log.metric("TestMetric", 1234);
```

Tento kód je alternativou pro volání `trackMetric` pomocí Node.js SDK pro Application Insights.

## <a name="log-custom-telemetry-in-c-functions"></a>Protokolování vlastní telemetrie ve funkcích jazyka C#

Verze Application Insights SDK specifická pro konkrétní funkce, kterou můžete použít k posílání vlastních dat telemetrie z vašich funkcí do Application Insights: [Microsoft. Azure. WebJobs. Logging. ApplicationInsights](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Logging.ApplicationInsights). K instalaci tohoto balíčku použijte následující příkaz z příkazového řádku:

# <a name="command"></a>[Příkaz](#tab/cmd)

```cmd
dotnet add package Microsoft.Azure.WebJobs.Logging.ApplicationInsights --version <VERSION>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -Version <VERSION>
```

---

V tomto příkazu nahraďte `<VERSION>` verzi tohoto balíčku, která podporuje vaši nainstalovanou verzi [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs/). 

Následující příklady jazyka C# používají [vlastní rozhraní API telemetrie](../azure-monitor/app/api-custom-events-metrics.md). Příklad je pro knihovnu tříd .NET, ale kód Application Insights je stejný pro skript jazyka C#.

### <a name="version-2x-and-later"></a>Verze 2. x a novější

Verze 2. x a novější verze modulu runtime používají v Application Insights novějších funkcí k automatickému korelaci telemetrie s aktuální operací. Nemusíte ručně nastavit operaci `Id` , `ParentId` nebo `Name` pole.

```cs
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;

using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
using System.Linq;

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

[Getmetric](../azure-monitor/app/api-custom-events-metrics.md#getmetric) je aktuálně Doporučené rozhraní API pro vytvoření metriky.

### <a name="version-1x"></a>Verze 1. x

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

Nevolejte `TrackRequest` nebo `StartOperation<RequestTelemetry>` , protože se zobrazí duplicitní požadavky na vyvolání funkce.  Modul runtime Functions automaticky sleduje požadavky.

Nenastaveno `telemetryClient.Context.Operation.Id` . Toto globální nastavení způsobuje nesprávnou korelaci, pokud mnoho funkcí běží současně. Místo toho vytvořte novou instanci telemetrie ( `DependencyTelemetry` , `EventTelemetry` ) a upravte její `Context` vlastnost. Pak předejte instanci telemetrie do odpovídající `Track` metody v `TelemetryClient` ( `TrackDependency()` , `TrackEvent()` , `TrackMetric()` ). Tato metoda zajišťuje, že telemetrie má správné korelační údaje pro aktuální vyvolání funkce.

## <a name="log-custom-telemetry-in-javascript-functions"></a>Protokolování vlastní telemetrie ve funkcích JavaScriptu

Tady jsou ukázkové fragmenty kódu, které odesílají vlastní telemetrii pomocí [Application Insights Node.js SDK](https://github.com/microsoft/applicationinsights-node.js):

### <a name="version-2x-and-later"></a>Verze 2. x a novější

```javascript
const appInsights = require("applicationinsights");
appInsights.setup();
const client = appInsights.defaultClient;

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    // Use this with 'tagOverrides' to correlate custom telemetry to the parent function invocation.
    var operationIdOverride = {"ai.operation.id":context.traceContext.traceparent};

    client.trackEvent({name: "my custom event", tagOverrides:operationIdOverride, properties: {customProperty2: "custom property value"}});
    client.trackException({exception: new Error("handled exceptions can be logged with this method"), tagOverrides:operationIdOverride});
    client.trackMetric({name: "custom metric", value: 3, tagOverrides:operationIdOverride});
    client.trackTrace({message: "trace message", tagOverrides:operationIdOverride});
    client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL", tagOverrides:operationIdOverride});
    client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true, tagOverrides:operationIdOverride});

    context.done();
};
```

### <a name="version-1x"></a>Verze 1. x

```javascript
const appInsights = require("applicationinsights");
appInsights.setup();
const client = appInsights.defaultClient;

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    // Use this with 'tagOverrides' to correlate custom telemetry to the parent function invocation.
    var operationIdOverride = {"ai.operation.id":context.operationId};

    client.trackEvent({name: "my custom event", tagOverrides:operationIdOverride, properties: {customProperty2: "custom property value"}});
    client.trackException({exception: new Error("handled exceptions can be logged with this method"), tagOverrides:operationIdOverride});
    client.trackMetric({name: "custom metric", value: 3, tagOverrides:operationIdOverride});
    client.trackTrace({message: "trace message", tagOverrides:operationIdOverride});
    client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL", tagOverrides:operationIdOverride});
    client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true, tagOverrides:operationIdOverride});

    context.done();
};
```

`tagOverrides`Parametr nastaví `operation_Id` ID vyvolání funkce. Toto nastavení umožňuje korelovat všechny automaticky vygenerované a vlastní telemetrie pro dané volání funkce.

## <a name="dependencies"></a>Závislosti

Funkce v2 automaticky shromažďuje závislosti pro požadavky HTTP, ServiceBus, EventHub a SQL.

Můžete napsat vlastní kód, který zobrazí závislosti. Příklady najdete v tématu vzorový kód v [části vlastní telemetrie v C#](#log-custom-telemetry-in-c-functions). Vzorový kód vede *mapu aplikace* v Application Insights, který vypadá jako na následujícím obrázku:

![Mapa aplikace](./media/functions-monitoring/app-map.png)

> [!NOTE]
> Závislosti se zapisují na úrovni informací. Pokud filtrujete v upozornění nebo výše, nezobrazí se žádná z těchto dat. Automatická shromažďování závislostí se také provádí v oboru bez uživatele. Proto se ujistěte, že úroveň je nastavena alespoň na **informace** mimo rozsah uživatele v host.js(tj. mimo funkci <YOUR_FUNCTION_NAME>. Klíč uživatele), pokud chcete, aby byly tyto závislosti zachyceny.

## <a name="enable-application-insights-integration"></a>Povolení integrace Application Insights

Aby aplikace Function App odesílala data Application Insights, musí znát klíč instrumentace prostředku Application Insights. Klíč musí být v nastavení aplikace s názvem **APPINSIGHTS_INSTRUMENTATIONKEY**.

Když vytvoříte aplikaci Function App [v Azure Portal](functions-create-first-azure-function.md), z příkazového řádku pomocí [Azure Functions Core Tools](functions-create-first-azure-function-azure-cli.md)nebo pomocí [Visual Studio Code](functions-create-first-function-vs-code.md)je ve výchozím nastavení povolená integrace Application Insights. Prostředek Application Insights má stejný název jako aplikace Function App a je vytvořený buď ve stejné oblasti, nebo v nejbližší oblasti.

### <a name="new-function-app-in-the-portal"></a>Nová aplikace Function App na portálu

Chcete-li zkontrolovat vytvářené prostředky Application Insights, vyberte ji a rozbalte okno **Application Insights** . Můžete změnit **název nového prostředku** nebo zvolit jiné **umístění** v [geografických oblastech Azure](https://azure.microsoft.com/global-infrastructure/geographies/) , kam chcete data uložit.

![Povolit Application Insights při vytváření aplikace Function App](media/functions-monitoring/enable-ai-new-function-app.png)

Když zvolíte **vytvořit**, vytvoří se prostředek Application Insights s aplikací Function App, která má `APPINSIGHTS_INSTRUMENTATIONKEY` nastavenou hodnotu v nastavení aplikace. Všechno je připravené k přechodu.

<a id="manually-connect-an-app-insights-resource"></a>
### <a name="add-to-an-existing-function-app"></a>Přidat do existující aplikace Function App 

Při vytváření aplikace Function App pomocí sady [Visual Studio](functions-create-your-first-function-visual-studio.md)je nutné vytvořit prostředek Application Insights. Potom můžete do aplikace Function app přidat klíč instrumentace z tohoto prostředku jako [nastavení aplikace](functions-how-to-use-azure-function-app-settings.md#settings) .

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

Dřívější verze funkcí používaly integrované monitorování, které se už nedoporučuje. Když povolíte integraci Application Insights pro takovou aplikaci Function App, musíte taky [zakázat integrované protokolování](#disable-built-in-logging).  

## <a name="report-issues"></a>Nahlášení potíží

Pokud chcete ohlásit problém s Application Insights integrací v rámci funkcí nebo udělat návrh nebo žádost, [vytvořte problém na GitHubu](https://github.com/Azure/Azure-Functions/issues/new).

## <a name="streaming-logs"></a>Protokoly streamování

Při vývoji aplikace často chcete zjistit, co se do protokolů zapisuje téměř v reálném čase, když běží v Azure.

Existují dva způsoby, jak zobrazit datový proud souborů protokolu generovaných spuštěním vaší funkce.

* **Integrované streamování protokolů**: platforma App Service umožňuje zobrazit datový proud souborů protokolu aplikace. Jedná se o ekvivalent výstupu zobrazeného při ladění funkcí během [místního vývoje](functions-develop-local.md) a při použití karty **test** na portálu. Zobrazí se všechny informace založené na protokolu. Další informace najdete v tématu [protokoly streamování](../app-service/troubleshoot-diagnostic-logs.md#stream-logs). Tato metoda streamování podporuje pouze jednu instanci a nelze ji použít s aplikací spuštěnou v systému Linux v plánu spotřeby.

* **Live Metrics Stream**: když je aplikace funkcí [připojená k Application Insights](#enable-application-insights-integration), můžete zobrazit data protokolu a další metriky téměř v reálném čase v Azure Portal pomocí [Live Metrics Stream](../azure-monitor/app/live-stream.md). Tuto metodu použijte, když chcete monitorovat funkce běžící na více instancích nebo v systému Linux v plánu spotřeby. Tato metoda používá [ukázková data](#configure-sampling).

Streamy protokolů je možné zobrazit na portálu i ve většině místních vývojových prostředí. 

### <a name="portal"></a>Portál

Oba typy datových proudů protokolů můžete zobrazit na portálu.

#### <a name="built-in-log-streaming"></a>Integrované streamování protokolů

Pokud chcete zobrazit protokoly streamování na portálu, vyberte v aplikaci Function App kartu **funkce platformy** . Pak v části **monitorování**zvolte **streamování protokolů**.

![Povolit streamování protokolů na portálu](./media/functions-monitoring/enable-streaming-logs-portal.png)

Tím se vaše aplikace připojí ke službě streamování protokolů a protokoly aplikací se zobrazí v okně. Můžete přepínat mezi **protokoly aplikací** a **protokoly webového serveru**.  

![Zobrazení protokolů streamování na portálu](./media/functions-monitoring/streaming-logs-window.png)

#### <a name="live-metrics-stream"></a>Live Metrics Stream

Pokud chcete zobrazit Live Metrics Stream pro vaši aplikaci, vyberte kartu **Přehled** aplikace Function App. Když Application Insights povolíte, zobrazí se v části **nakonfigurované funkce**odkaz **Application Insights** . Tento odkaz vás přesměruje na stránku Application Insights vaší aplikace.

V Application Insights vyberte **Live Metrics Stream**. [Ukázkové položky protokolu](#configure-sampling) se zobrazují v části **ukázková telemetrie**.

![Zobrazit Live Metrics Stream na portálu](./media/functions-monitoring/live-metrics-stream.png) 

### <a name="visual-studio-code"></a>Visual Studio Code

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

### <a name="core-tools"></a>Základní nástroje

[!INCLUDE [functions-streaming-logs-core-tools](../../includes/functions-streaming-logs-core-tools.md)]

### <a name="azure-cli"></a>Azure CLI

Protokoly streamování můžete povolit pomocí [Azure CLI](/cli/azure/install-azure-cli). Pomocí následujících příkazů se přihlaste, vyberte své předplatné a soubory protokolu streamu:

```azurecli
az login
az account list
az account set --subscription <subscriptionNameOrId>
az webapp log tail --resource-group <RESOURCE_GROUP_NAME> --name <FUNCTION_APP_NAME>
```

### <a name="azure-powershell"></a>Azure PowerShell

Protokoly streamování můžete povolit pomocí [Azure PowerShell](/powershell/azure/). Pro prostředí PowerShell pomocí příkazu [set-AzWebApp](/powershell/module/az.websites/set-azwebapp) Povolte protokolování aplikace Function App, jak je znázorněno v následujícím fragmentu kódu: 

:::code language="powershell" source="~/powershell_scripts/app-service/monitor-with-logs/monitor-with-logs.ps1" range="19-20":::

Další informace naleznete v tématu [úplný příklad kódu](../app-service/scripts/powershell-monitor.md#sample-script). 

## <a name="scale-controller-logs-preview"></a>Škálování protokolů řadiče (Preview)

Tato funkce je ve verzi Preview. 

[Kontroler škálování Azure Functions](./functions-scale.md#runtime-scaling) sleduje instance Azure Functionsho hostitele, na kterém vaše aplikace běží. Tento kontroler provádí rozhodnutí o tom, kdy se instance na základě aktuálního výkonu přidávají nebo odebírají. Řadič škálování můžete mít k dispozici protokoly pro Application Insights nebo do úložiště objektů blob, abyste lépe pochopili rozhodnutí, které kontroler škálování vytváří pro vaši aplikaci Function App.

Chcete-li povolit tuto funkci, přidejte nové nastavení aplikace s názvem `SCALE_CONTROLLER_LOGGING_ENABLED` . Hodnota tohoto nastavení musí být ve formátu `<DESTINATION>:<VERBOSITY>` , a to na základě následujícího:

[!INCLUDE [functions-scale-controller-logging](../../includes/functions-scale-controller-logging.md)]

Například následující příkaz Azure CLI zapne podrobné protokolování z kontroleru škálování na Application Insights:

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings SCALE_CONTROLLER_LOGGING_ENABLED=AppInsights:Verbose
```

V tomto příkladu nahraďte `<FUNCTION_APP_NAME>` a `<RESOURCE_GROUP_NAME>` názvem vaší aplikace Function App a názvem skupiny prostředků v uvedeném pořadí. 

Následující příkaz rozhraní příkazového řádku Azure zakáže protokolování nastavením podrobností na `None` :

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings SCALE_CONTROLLER_LOGGING_ENABLED=AppInsights:None
```

Protokolování můžete také zakázat odebráním `SCALE_CONTROLLER_LOGGING_ENABLED` nastavení pomocí následujícího příkazu rozhraní příkazového řádku Azure:

```azurecli-interactive
az functionapp config appsettings delete --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--setting-names SCALE_CONTROLLER_LOGGING_ENABLED
```

## <a name="disable-built-in-logging"></a>Zákaz integrovaného protokolování

Pokud povolíte Application Insights, zakažte integrované protokolování, které používá Azure Storage. Integrované protokolování je užitečné pro testování s využitím lehkých úloh, ale není určené pro použití v produkčním prostředí s vysokou zátěží. Pro produkční monitorování doporučujeme Application Insights. Pokud se v produkčním prostředí používá integrované protokolování, může být záznam protokolování neúplný z důvodu omezování Azure Storage.

Pokud chcete zakázat integrované protokolování, odstraňte `AzureWebJobsDashboard` nastavení aplikace. Informace o tom, jak odstranit nastavení aplikace v Azure Portal, najdete v části **nastavení aplikace** v tématu [Správa aplikace Function App](functions-how-to-use-azure-function-app-settings.md#settings). Před odstraněním nastavení aplikace se ujistěte, že žádné existující funkce ve stejné aplikaci Function App nepoužívají nastavení pro Azure Storage triggery nebo vazby.

## <a name="next-steps"></a>Další kroky

Další informace naleznete v následujících zdrojích:

* [Application Insights](/azure/application-insights/)
* [Protokolování ASP.NET Core](/aspnet/core/fundamentals/logging/)

[host.jsna]: functions-host-json.md
