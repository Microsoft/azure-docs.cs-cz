---
title: Monitorování Azure Functions
description: Zjistěte, jak pomocí Azure Application Insights s funkcemi Azure monitorovat spouštění funkcí.
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.topic: conceptual
ms.date: 04/04/2019
ms.openlocfilehash: 08da17f1ef023676aa0c499cf4e7e1bb9687f1c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80257849"
---
# <a name="monitor-azure-functions"></a>Monitorování Azure Functions

[Funkce Azure](functions-overview.md) nabízejí integrovanou integraci s [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) pro monitorování funkcí. Tento článek ukazuje, jak nakonfigurovat funkce Azure pro odesílání souborů protokolu generovaných systémem do Application Insights.

Doporučujeme používat Application Insights, protože shromažďuje data protokolu, výkonu a chyb. Automaticky detekuje anomálie výkonu a obsahuje výkonné analytické nástroje, které vám pomohou diagnostikovat problémy a pochopit, jak se vaše funkce používají. Je navržena tak, aby pomáhala průběžně vylepšovat výkon a možnosti využití. Můžete dokonce použít Application Insights během vývoje projektu aplikace místní funkce. Další informace naleznete v tématu [Co je Application Insights?](../azure-monitor/app/app-insights-overview.md).

Vzhledem k tomu, že požadovaná instrumentace Application Insights je integrovaná do funkcí Azure, vše, co potřebujete, je platný klíč instrumentace pro připojení vaší funkční aplikace k prostředku Application Insights. Klíč instrumentace by měl být přidán do nastavení aplikace při vytvoření prostředku aplikace funkce v Azure. Pokud vaše aplikace funkcí ještě tento klíč nemá, můžete [ho nastavit ručně](#enable-application-insights-integration).  

## <a name="application-insights-pricing-and-limits"></a>Ceny a limity Přehledy aplikací

Integraci Application Insights s aplikacemi Function Apps si můžete vyzkoušet zdarma. K dispozici je denní limit na to, kolik dat lze zdarma zpracovat. Tento limit můžete během testování narazit. Azure poskytuje portálová a e-mailová oznámení, když se blížíte k dennímu limitu. Pokud tyto výstrahy zmeškáte a dosáhnete limitu, nové protokoly se v dotazech Application Insights nezobrazí. Uvědomte si limit, abyste se vyhnuli zbytečnému řešení času. Další informace naleznete [v tématu Správa cen a objemu dat v application insights](../azure-monitor/app/pricing.md).

Úplný seznam funkcí Application Insights, které jsou k dispozici pro vaši funkční aplikaci, je podrobně popsán v [podporovaných funkcích Application Insights for Azure Functions](../azure-monitor/app/azure-functions-supported-features.md).

## <a name="view-telemetry-in-monitor-tab"></a>Zobrazení telemetrie na kartě Monitor

Pokud [je povolena integrace Application Insights](#enable-application-insights-integration), můžete zobrazit telemetrická data na kartě **Monitor.**

1. Na stránce aplikace funkce vyberte funkci, která byla spuštěna alespoň jednou po nakonfigurování Application Insights. Potom vyberte kartu **Monitor.** Pravidelně vyberte **Aktualizovat,** dokud se nezobrazí seznam vyvolání funkce.

   ![Seznam vyvolání](media/functions-monitoring/monitor-tab-ai-invocations.png)

    > [!NOTE]
    > Může trvat až pět minut, než se seznam zobrazí, zatímco klient telemetrie dávky dat pro přenos na server. Zpoždění se nevztahuje na [stream živých metrik](../azure-monitor/app/live-stream.md). Tato služba se připojí k hostiteli Functions při načtení stránky, takže protokoly jsou přenášeny přímo na stránku.

1. Chcete-li zobrazit protokoly pro vyvolání určité funkce, vyberte odkaz sloupec **Datum (UTC)** pro toto vyvolání. Výstup protokolování pro toto vyvolání se zobrazí na nové stránce.

   ![Podrobnosti o vyvolání](media/functions-monitoring/invocation-details-ai.png)

1. Zvolte **spustit v application insights** odkaz zobrazíte zdroj dotazu, který načte data protokolu Azure Monitor v protokolu Azure Log Pokud je to poprvé pomocí Azure Log Analytics ve vašem předplatném, budete vyzváni k povolení.

1. Když zvolíte tento odkaz a zvolíte povolení Log Analytic. zobrazí se následující dotaz. Můžete vidět, že výsledky dotazu jsou omezeny`where timestamp > ago(30d)`na posledních 30 dnů ( ). Kromě toho výsledky ukazují ne více než`take 20`20 řádků ( ). Naproti tomu seznam podrobností vyvolání pro vaši funkci je za posledních 30 dní bez omezení.

   ![Seznam vyvolání analýzy přehledů aplikací](media/functions-monitoring/ai-analytics-invocation-list.png)

Další informace naleznete v [tématu Dotaz na telemetrická data](#query-telemetry-data) dále v tomto článku.

## <a name="view-telemetry-in-application-insights"></a>Zobrazení telemetrie v přehledech aplikací

Pokud chcete otevřít Application Insights z aplikace funkcí na webu Azure Portal, přejděte na stránku **Přehled** aplikace pro funkce. V části **Nakonfigurované funkce**vyberte **Přehledy aplikací**.

![Otevření přehledů aplikací ze stránky Přehled aplikace funkcí](media/functions-monitoring/ai-link.png)

Informace o použití application insights naleznete v [dokumentaci k přehledům aplikací](https://docs.microsoft.com/azure/application-insights/). Tato část ukazuje některé příklady zobrazení dat v Application Insights. Pokud už znáte Application Insights, můžete přejít přímo do [oddílů o konfiguraci a přizpůsobení telemetrických dat](#configure-categories-and-log-levels).

![Karta Přehledy aplikací](media/functions-monitoring/metrics-explorer.png)

Následující oblasti Application Insights může být užitečné při hodnocení chování, výkonu a chyb y ve vašich funkcích:

| Prověřování | Popis |
| ---- | ----------- |
| **[Selhání](../azure-monitor/app/asp-net-exceptions.md)** |  Vytvářejte grafy a výstrahy na základě selhání funkcí a výjimek serveru. **Název operace** je název funkce. Chyby v závislostech nejsou zobrazeny, pokud implementovat vlastní telemetrie pro závislosti. |
| **[Výkon](../azure-monitor/app/performance-counters.md)** | Analyzujte problémy s výkonem zobrazením využití prostředků a propustnosti podle **instancí rolí cloudu**. Tato data mohou být užitečná pro ladění scénáře, kde funkce jsou zatěžování základní prostředky. |
| **[Metriky](../azure-monitor/app/metrics-explorer.md)** | Vytvářejte grafy a výstrahy založené na metrikách. Metriky zahrnují počet vyvolání funkce, čas spuštění a úspěšnost. |
| **[Živé metriky](../azure-monitor/app/live-stream.md)** | Zobrazte data metrik tak, jak jsou vytvořena téměř v reálném čase. |

## <a name="query-telemetry-data"></a>Dotaz na telemetrická data

[Application Insights Analytics](../azure-monitor/app/analytics.md) umožňuje přístup ke všem telemetrickým datům ve formě tabulek v databázi. Služba Analytics poskytuje dotazovací jazyk pro extrahování, manipulaci a vizualizaci dat. 

Zvolte **Protokoly** prozkoumat nebo dotaz na protokolované události.

![Příklad analýzy](media/functions-monitoring/analytics-traces.png)

Zde je příklad dotazu, který zobrazuje rozdělení požadavků na pracovníka za posledních 30 minut.

<pre>
requests
| where timestamp > ago(30m) 
| summarize count() by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
</pre>

Tabulky, které jsou k dispozici, jsou zobrazeny na kartě **Schéma** vlevo. Data generovaná vyvoláními funkcí naleznete v následujících tabulkách:

| Table | Popis |
| ----- | ----------- |
| **Stopy** | Protokoly vytvořené za běhu a kódem funkce. |
| **požadavky** | Jeden požadavek pro každou vyvolání funkce. |
| **Výjimky** | Všechny výjimky vyvoláné za běhu. |
| **vlastnímetriky** | Počet úspěšných a neúspěšných vyvolání, úspěšnost a trvání. |
| **customEvents** | Události sledované za běhu, například: HTTP požadavky, které aktivují funkci. |
| **performanceCounters** | Informace o výkonu serverů, na kterých jsou spuštěny funkce. |

Ostatní tabulky jsou pro testy dostupnosti a telemetrie klienta a prohlížeče. Můžete implementovat vlastní telemetrie přidat data k nim.

V každé tabulce jsou některá data specifická pro funkce v `customDimensions` poli.  Například následující dotaz načte všechny trasování, `Error`které mají úroveň protokolu .

<pre>
traces 
| where customDimensions.LogLevel == "Error"
</pre>

Runtime poskytuje `customDimensions.LogLevel` pole `customDimensions.Category` a. Další pole můžete zadat v protokolech, které zapíšete do kódu funkce. Viz [Strukturované protokolování](#structured-logging) dále v tomto článku.

## <a name="configure-categories-and-log-levels"></a>Konfigurace kategorií a úrovní protokolů

Application Insights můžete použít bez vlastní konfigurace. Výchozí konfigurace může mít za následek velké objemy dat. Pokud používáte předplatné Visual Studio Azure, můžete stisknout limit dat pro Application Insights. Dále v tomto článku se dozvíte, jak nakonfigurovat a přizpůsobit data, která vaše funkce odesílají do Application Insights. Pro aplikaci funkce je protokolování nakonfigurováno v souboru [host.json.]

### <a name="categories"></a>Kategorie

Protokolování funkce Azure obsahuje *kategorii* pro každý protokol. Kategorie označuje, která část kódu runtime nebo kód funkce napsal protokol. Následující graf popisuje hlavní kategorie protokolů, které vytvoří za běhu. 

| Kategorie | Popis |
| ----- | ----- | 
| Host.Results | Tyto protokoly se zobrazují jako **požadavky** v Application Insights. Označují úspěch nebo neúspěch funkce. Všechny tyto protokoly jsou `Information` zapsány na úrovni. Pokud filtrujete podle `Warning` nebo vyšší, žádná z těchto dat se nezobrazí. |
| Hostitel.Agregátor | Tyto protokoly poskytují počty a průměry vyvolání funkce za [konfigurovatelné](#configure-the-aggregator) časové období. Výchozí doba je 30 sekund nebo 1 000 výsledků podle toho, co nastane dříve. Protokoly jsou k dispozici v tabulce **customMetrics** v Application Insights. Příkladem je počet spuštění, úspěšnost a doba trvání. Všechny tyto protokoly jsou `Information` zapsány na úrovni. Pokud filtrujete podle `Warning` nebo vyšší, žádná z těchto dat se nezobrazí. |

Všechny protokoly pro kategorie než tyto jsou k dispozici v tabulce **trasování** v Application Insights.

Všechny protokoly s kategoriemi, které začínají `Host` jsou zapsány funkce runtime. Protokoly **Funkce spuštěno** a **dokončená funkce** mají kategorii `Host.Executor`. Pro úspěšné spuštění jsou `Information` tyto protokoly na úrovni. Výjimky jsou protokolovány na `Error` úrovni. Runtime také `Warning` vytvoří protokoly úrovně, například: zprávy fronty odeslané do fronty jedovatých.

Functions runtime vytvoří protokoly s kategorií, která začíná "Host". Ve verzi 1.x `function started` `function executed`mají `function completed` kategorie `Host.Executor`, a protokoly . Počínaje verzí 2.x mají tyto protokoly `Function.<YOUR_FUNCTION_NAME>`kategorii .

Pokud zapisujete protokoly do kódu `Function.<YOUR_FUNCTION_NAME>.User` funkce, kategorie je a může být libovolná úroveň protokolu. Ve verzi 1.x functions runtime je `Function`kategorie .

### <a name="log-levels"></a>Úrovně protokolování

Protokolování funkce Azure také obsahuje *úroveň protokolu* s každým protokolem. [LogLevel](/dotnet/api/microsoft.extensions.logging.loglevel) je výčet a etegerový kód označuje relativní důležitost:

|LogLevel    |kód|
|------------|---|
|Trasování       | 0 |
|Ladění       | 1 |
|Informace | 2 |
|Upozornění     | 3 |
|Chyba       | 4 |
|Kritická    | 5 |
|Žádný        | 6 |

Úroveň `None` protokolu je vysvětlena v další části. 

### <a name="log-configuration-in-hostjson"></a>Konfigurace protokolu v souboru host.json

Soubor [host.json] konfiguruje, kolik protokolování aplikace funkce odešle do Application Insights. Pro každou kategorii označujete minimální úroveň protokolu, kterou chcete odeslat. Existují dva příklady: první příklad cíle [verze 2.x a novější](functions-versions.md#version-2x) funkce runtime (s .NET Core) a druhý příklad je pro verzi 1.x runtime.

### <a name="version-2x-and-higher"></a>Verze 2.x a vyšší

Verze v2.x a novější verze za běhu Functions používají [hierarchii filtrů protokolování .NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering). 

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

### <a name="version-1x"></a>Verze 1.x

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

Tento příklad nastaví následující pravidla:

* Pro protokoly s `Host.Results` `Function`kategorie nebo `Error` , odeslat pouze úroveň a výše application insights. Protokoly `Warning` pro úroveň a níže jsou ignorovány.
* Pro protokoly s `Host.Aggregator`kategorií , odeslat všechny protokoly Application Insights. Úroveň `Trace` protokolu je stejná jako to, co nazývají `Verbose`některé úhozy kláves , ale používá se `Trace` v souboru [host.json.]
* Pro všechny ostatní protokoly `Information` odesílejte pouze úroveň a výše na Application Insights.

Hodnota kategorie v [souboru host.json] řídí protokolování pro všechny kategorie, které začínají stejnou hodnotou. `Host`v [souboru host.json] řídí protokolování pro `Host.General`, `Host.Executor`, `Host.Results`a tak dále.

Pokud [host.json] obsahuje více kategorií, které začínají stejným řetězcem, ty delší jsou porovnány jako první. Předpokládejme, že chcete vše `Host.Aggregator` od `Error` runtime s `Host.Aggregator` výjimkou protokolu `Information` na úrovni, ale chcete se přihlásit na úrovni:

### <a name="version-2x-and-later"></a>Verze 2.x a novější

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

### <a name="version-1x"></a>Verze 1.x 

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

Chcete-li potlačit všechny protokoly pro kategorii, `None`můžete použít úroveň protokolu . Žádné protokoly jsou zapsány s danou kategorií a není žádná úroveň protokolu nad ním.

## <a name="configure-the-aggregator"></a>Konfigurace agregátoru

Jak je uvedeno v předchozí části, runtime agreguje data o spuštění funkce za určité časové období. Výchozí doba je 30 sekund nebo 1 000 spuštění, podle toho, co nastane dříve. Toto nastavení můžete nakonfigurovat v souboru [host.json.]  Tady je příklad:

```json
{
    "aggregator": {
      "batchSize": 1000,
      "flushTimeout": "00:00:30"
    }
}
```

## <a name="configure-sampling"></a>Konfigurace vzorkování

Application Insights má [funkci vzorkování,](../azure-monitor/app/sampling.md) která vás může chránit před vytvářením příliš velkého množství telemetrických dat o dokončených spuštěních v době špičkového zatížení. Pokud rychlost příchozích spuštění překročí zadanou prahovou hodnotu, Application Insights začne náhodně ignorovat některé příchozí spuštění. Výchozí nastavení maximálního počtu spuštění za sekundu je 20 (pět ve verzi 1.x). Vzorkování můžete nakonfigurovat v [souboru host.json].  Tady je příklad:

### <a name="version-2x-and-later"></a>Verze 2.x a novější

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

### <a name="version-1x"></a>Verze 1.x 

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
> [Vzorkování](../azure-monitor/app/sampling.md) je ve výchozím nastavení povoleno. Pokud se zdá, že chybí data, bude pravděpodobně nutné upravit nastavení vzorkování tak, aby vyhovovalo vašemu konkrétnímu scénáři monitorování.

## <a name="write-logs-in-c-functions"></a>Zápis protokolů ve funkcích jazyka C#

Můžete zapsat protokoly v kódu funkce, které se zobrazí jako trasování v Application Insights.

### <a name="ilogger"></a>ILogger

Místo `TraceWriter` parametru použijte parametr [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger) ve svých funkcích. Protokoly vytvořené `TraceWriter` pomocí aplikace Insights, `ILogger` ale umožňuje provést [strukturované protokolování](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

S `ILogger` objektem volání `Log<level>` [metody rozšíření na ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.loggerextensions#methods) k vytvoření protokolů. Následující kód `Information` zapisuje protokoly s kategorií "Function.<YOUR_FUNCTION_NAME>. Uživateli."

```cs
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger logger)
{
    logger.LogInformation("Request for item with key={itemKey}.", id);
```

### <a name="structured-logging"></a>Strukturované protokolování

Pořadí zástupných symbolů, nikoli jejich názvy, určuje, které parametry se používají ve zprávě protokolu. Předpokládejme, že máte následující kód:

```csharp
string partitionKey = "partitionKey";
string rowKey = "rowKey";
logger.LogInformation("partitionKey={partitionKey}, rowKey={rowKey}", partitionKey, rowKey);
```

Pokud zachováte stejný řetězec zprávy a obrátíte pořadí parametrů, výsledný text zprávy bude mít hodnoty na nesprávných místech.

Zástupné symboly jsou zpracovány tímto způsobem, takže můžete provést strukturované protokolování. Application Insights ukládá dvojice název-hodnota parametru a řetězec zprávy. Výsledkem je, že argumenty zprávy se stanou poli, na které se můžete dotazovat.

Pokud volání metody logger vypadá jako v předchozím `customDimensions.prop__rowKey`příkladu, můžete zadat dotaz na pole . Předpona `prop__` je přidána, aby se zajistilo, že nedojde ke kolizím mezi poli, která runtime přidá, a poli, která přidá kód funkce.

Můžete také dotaz na původní řetězec zprávy `customDimensions.prop__{OriginalFormat}`odkazem na pole .  

Zde je ukázka JSON `customDimensions` reprezentace dat:

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

Ve skriptových funkcích jazyka `LogMetric` C# `ILogger` můžete použít metodu rozšíření k vytvoření vlastních metrik v Application Insights. Zde je ukázka volání metody:

```csharp
logger.LogMetric("TestMetric", 1234);
```

Tento kód je alternativou k volání `TrackMetric` pomocí rozhraní API Application Insights pro rozhraní .NET.

## <a name="write-logs-in-javascript-functions"></a>Zápis protokolů ve funkcích JavaScriptu

Ve funkcích Node.js použijte `context.log` k zápisu protokolů. Strukturované protokolování není povoleno.

```
context.log('JavaScript HTTP trigger function processed a request.' + context.invocationId);
```

### <a name="custom-metrics-logging"></a>Protokolování vlastních metrik

Když běžíte na [verzi 1.x](functions-versions.md#creating-1x-apps) funkce runtime, Node.js `context.log.metric` funkce můžete použít metodu k vytvoření vlastní metriky v Application Insights. Tato metoda není aktuálně podporována ve verzi 2.x a novější. Zde je ukázka volání metody:

```javascript
context.log.metric("TestMetric", 1234);
```

Tento kód je alternativou k volání `trackMetric` pomocí sady SDK Node.js pro přehledy aplikací.

## <a name="log-custom-telemetry-in-c-functions"></a>Protokolování vlastní telemetrie ve funkcích jazyka C#

K dispozici je verze sady Application Insights SDK specifická pro funkce, kterou můžete použít k odeslání vlastních telemetrických dat z vašich funkcí do application insights: [Microsoft.Azure.WebJobs.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Logging.ApplicationInsights). K instalaci tohoto balíčku použijte následující příkaz z příkazového řádku:

# <a name="command"></a>[Příkaz](#tab/cmd)

```cmd
dotnet add package Microsoft.Azure.WebJobs.Logging.ApplicationInsights --version <VERSION>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -Version <VERSION>
```

---

V tomto příkazu nahraďte `<VERSION>` verzí tohoto balíčku, která podporuje nainstalovanou verzi [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs/). 

Následující příklady jazyka C# používají [vlastní rozhraní API pro telemetrii](../azure-monitor/app/api-custom-events-metrics.md). Příklad je pro knihovnu tříd .NET, ale kód Application Insights je stejný pro skript Jazyka C#.

### <a name="version-2x-and-later"></a>Verze 2.x a novější

Verze 2.x a novější verze runtime používat novější funkce v Application Insights automaticky korelovat telemetrie s aktuální operací. Není nutné ručně nastavit operaci `Id` `ParentId`nebo `Name` pole.

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

[GetMetric](../azure-monitor/app/api-custom-events-metrics.md#getmetric) je aktuálně doporučené rozhraní API pro vytvoření metriky.

### <a name="version-1x"></a>Verze 1.x

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

Nevolejte `TrackRequest` nebo `StartOperation<RequestTelemetry>` protože se zobrazí duplicitní požadavky na vyvolání funkce.  Funkce runtime automaticky sleduje požadavky.

Nenastavuj `telemetryClient.Context.Operation.Id`. Toto globální nastavení způsobí nesprávnou korelaci, když je současně spuštěno mnoho funkcí. Místo toho vytvořte novou`DependencyTelemetry`telemetrickou instanci ( , `EventTelemetry`) a upravte její `Context` vlastnost. Potom předaj instanci `Track` telemetrie odpovídající metodě `TelemetryClient` na`TrackDependency()`( , `TrackEvent()`, `TrackMetric()`). Tato metoda zajišťuje, že telemetrie má správné podrobnosti korelace pro vyvolání aktuální funkce.

## <a name="log-custom-telemetry-in-javascript-functions"></a>Protokolování vlastní telemetrie ve funkcích JavaScriptu

Tady je ukázkový fragment kódu, který odesílá vlastní telemetrii pomocí [sady SDK application Insights Node.js](https://github.com/microsoft/applicationinsights-node.js):

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

Parametr `tagOverrides` nastaví `operation_Id` id vyvolání funkce. Toto nastavení umožňuje korelovat všechny automaticky generované a vlastní telemetrie pro dané vyvolání funkce.

## <a name="dependencies"></a>Závislosti

Funkce v2 automaticky shromažďuje závislosti pro požadavky HTTP, ServiceBus, EventHub a SQL.

Můžete napsat vlastní kód pro zobrazení závislostí. Příklady naleznete ukázkový kód v [oddílu vlastní telemetrie jazyka C#](#log-custom-telemetry-in-c-functions). Ukázkový kód má za následek *mapování aplikace* v Application Insights, který vypadá jako následující obrázek:

![Mapa aplikace](./media/functions-monitoring/app-map.png)

## <a name="enable-application-insights-integration"></a>Povolení integrace Application Insights

Aby aplikace funkce odesílat data do Application Insights, potřebuje znát instrumentace klíč prostředku Application Insights. Klíč musí být v nastavení aplikace s názvem **APPINSIGHTS_INSTRUMENTATIONKEY**.

Když vytvoříte aplikaci funkcí [na webu Azure Portal](functions-create-first-azure-function.md), z příkazového řádku pomocí nástrojů Azure Functions Core [Tools](functions-create-first-azure-function-azure-cli.md)nebo pomocí kódu [Visual Studia](functions-create-first-function-vs-code.md), je ve výchozím nastavení povolena integrace Application Insights. Prostředek Application Insights má stejný název jako aplikace funkce a je vytvořen ve stejné oblasti nebo v nejbližší oblasti.

### <a name="new-function-app-in-the-portal"></a>Nová aplikace funkcí na portálu

Chcete-li zkontrolovat vytvářený prostředek Application Insights, vyberte ho, chcete-li rozbalit okno **Přehledy aplikací.** Můžete změnit **název nového prostředku** nebo zvolit jiné **umístění** v [azure geografii,](https://azure.microsoft.com/global-infrastructure/geographies/) kde chcete ukládat data.

![Povolení přehledů aplikací při vytváření aplikace funkcí](media/functions-monitoring/enable-ai-new-function-app.png)

Když zvolíte **Vytvořit**, vytvoří se prostředek Application Insights `APPINSIGHTS_INSTRUMENTATIONKEY` pomocí aplikace funkce, která má nastavenou hodnotu v nastavení aplikace. Všechno je připraveno.

<a id="manually-connect-an-app-insights-resource"></a>
### <a name="add-to-an-existing-function-app"></a>Přidání do existující aplikace pro funkce 

Při vytváření aplikace funkce pomocí [sady Visual Studio](functions-create-your-first-function-visual-studio.md)je nutné vytvořit prostředek Application Insights. Potom můžete přidat klíč instrumentace z tohoto prostředku jako nastavení aplikace v aplikaci funkce.

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

Rané verze funkcí používá integrované monitorování, které se již nedoporučuje. Při povolení integrace Application Insights pro takovou aplikaci funkce musíte také [zakázat integrované protokolování](#disable-built-in-logging).  

## <a name="report-issues"></a>Nahlášení potíží

Chcete-li nahlásit problém s integrací Application Insights ve funkcích nebo vytvořit návrh nebo žádost, [vytvořte problém na GitHubu](https://github.com/Azure/Azure-Functions/issues/new).

## <a name="streaming-logs"></a>Protokoly datových proudů

Při vývoji aplikace často chcete zobrazit, co se zapisuje do protokolů v téměř v reálném čase při spuštění v Azure.

Existují dva způsoby, jak zobrazit datový proud souborů protokolu generovaných spuštění funkce.

* **Integrované streamování protokolu**: Platforma služby App Service umožňuje zobrazit datový proud souborů protokolu aplikace. To je ekvivalentní výstup vidět při ladění funkce během [místního vývoje](functions-develop-local.md) a při použití **test** kartu na portálu. Zobrazí se všechny informace založené na protokolu. Další informace naleznete v tématu [Stream logs](../app-service/troubleshoot-diagnostic-logs.md#stream-logs). Tato metoda streamování podporuje pouze jednu instanci a nelze ji použít s aplikací spuštěnou na Linuxu v plánu Spotřeba.

* **Živé metriky Stream:** když je vaše aplikace funkce [připojena k Application Insights](#enable-application-insights-integration), můžete zobrazit data protokolu a další metriky téměř v reálném čase na webu Azure Portal pomocí [živého streamování metrik](../azure-monitor/app/live-stream.md). Tuto metodu použijte při monitorování funkcí spuštěných na více instancích nebo na Linuxu v plánu spotřeby. Tato metoda používá [vzorkovaná data](#configure-sampling).

Datové proudy protokolů lze zobrazit na portálu i ve většině prostředí místního rozvoje. 

### <a name="portal"></a>Portál

Můžete zobrazit oba typy datových proudů protokolu na portálu.

#### <a name="built-in-log-streaming"></a>Integrované streamování protokolů

Chcete-li zobrazit protokoly datových proudů na portálu, vyberte kartu **Funkce platformy** v aplikaci funkce. Potom v části **Sledování**zvolte **Log streaming**.

![Povolení protokolů datových proudů na portálu](./media/functions-monitoring/enable-streaming-logs-portal.png)

Tím se aplikace připojí ke službě streamování protokolu a v okně se zobrazí protokoly aplikací. Můžete přepínat mezi **protokoly aplikací** a **protokoly webového serveru**.  

![Zobrazení protokolů datových proudů na portálu](./media/functions-monitoring/streaming-logs-window.png)

#### <a name="live-metrics-stream"></a>Live Metrics Stream

Pokud chcete zobrazit Stream živých metrik pro vaši aplikaci, vyberte kartu **Přehled** aplikace funkce. Pokud máte application insights umožňuje, zobrazí se odkaz **Přehledy aplikací** v části **Nakonfigurované funkce**. Tento odkaz vás přenese na stránku Přehledy aplikací pro vaši aplikaci.

V application insights vyberte **Stream živých metrik**. [Vzorkované položky protokolu](#configure-sampling) jsou zobrazeny v části **Ukázková telemetrie**.

![Zobrazení živého streamování metrik na portálu](./media/functions-monitoring/live-metrics-stream.png) 

### <a name="visual-studio-code"></a>Visual Studio Code

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

### <a name="core-tools"></a>Základní nástroje

[!INCLUDE [functions-streaming-logs-core-tools](../../includes/functions-streaming-logs-core-tools.md)]

### <a name="azure-cli"></a>Azure CLI

Protokoly datových proudů můžete povolit pomocí [příkazového příkazu k webu Azure](/cli/azure/install-azure-cli). Pomocí následujících příkazů se přihlaste, zvolte předplatné a streamujte soubory protokolu:

```azurecli
az login
az account list
az account set --subscription <subscriptionNameOrId>
az webapp log tail --resource-group <RESOURCE_GROUP_NAME> --name <FUNCTION_APP_NAME>
```

### <a name="azure-powershell"></a>Azure PowerShell

Protokoly streamování můžete povolit pomocí [Azure PowerShellu](/powershell/azure/overview). V PowerShellu přidejte svůj účet Azure pomocí následujících příkazů, zvolte předplatné a streamujte soubory protokolu:

```powershell
Add-AzAccount
Get-AzSubscription
Get-AzSubscription -SubscriptionName "<subscription name>" | Select-AzSubscription
Get-AzWebSiteLog -Name <FUNCTION_APP_NAME> -Tail
```

## <a name="disable-built-in-logging"></a>Zakázání integrovaného protokolování

Když povolíte Application Insights, zakažte integrované protokolování, které používá Azure Storage. Integrované protokolování je užitečné pro testování s lehkými úlohami, ale není určeno pro použití v produkčním prostředí s vysokým zatížením. Pro monitorování výroby doporučujeme Application Insights. Pokud integrované protokolování se používá v produkčním prostředí, záznam protokolování může být neúplné z důvodu omezení na Azure Storage.

Chcete-li zakázat integrované `AzureWebJobsDashboard` protokolování, odstraňte nastavení aplikace. Informace o tom, jak odstranit nastavení aplikací na webu Azure Portal, najdete v části **Nastavení aplikace** [v tématu Jak spravovat aplikaci funkcí](functions-how-to-use-azure-function-app-settings.md#settings). Než odstraníte nastavení aplikace, ujistěte se, že žádné existující funkce ve stejné aplikaci funkce použít nastavení pro Azure Storage aktivační události nebo vazby.

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících materiálech:

* [Application Insights](/azure/application-insights/)
* [ASP.NET jádra protokolování](/aspnet/core/fundamentals/logging/)

[hostitel.json]: functions-host-json.md
