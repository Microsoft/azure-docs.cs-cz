---
title: Monitorování Azure Functions
description: Naučte se používat Azure Application Insights s Azure Functions k monitorování provádění funkcí.
services: functions
author: ggailey777
manager: jeconnoc
keywords: funkce azure, funkce, zpracování událostí, webhook, dynamické výpočty, architektura bez serverů
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.service: azure-functions
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: glenga
ms.openlocfilehash: 7220c72625697490d9c42dab7e18ca4bf64d3f43
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70085612"
---
# <a name="monitor-azure-functions"></a>Monitorování Azure Functions

[Azure Functions](functions-overview.md) nabízí integrovanou integraci s [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) k monitorování funkcí. V tomto článku se dozvíte, jak nakonfigurovat Azure Functions k odeslání souborů protokolu generovaných systémem do Application Insights.

Doporučujeme použít Application Insights, protože shromažďuje data o protokolech, výkonu a chybách. Automaticky detekuje anomálie ve výkonu a zahrnuje výkonné analytické nástroje, které vám pomůžou diagnostikovat problémy a pochopit, jak se vaše funkce používají. Je navržena tak, aby pomáhala průběžně vylepšovat výkon a možnosti využití. Můžete dokonce použít Application Insights během vývoje projektu místní funkce aplikace Function App. Další informace najdete v tématu [co je Application Insights?](../azure-monitor/app/app-insights-overview.md).

Jelikož je požadovaná instrumentace Application Insights integrovaná do Azure Functions, potřebujete k připojení aplikace Function App k Application Insights prostředku platný Instrumentation Key.

## <a name="application-insights-pricing-and-limits"></a>Application Insights ceny a omezení

Můžete si vyzkoušet Application Insights integraci s aplikacemi Function App zdarma. K dispozici je denní limit pro množství dat, která je možné zpracovat zdarma. Tento limit se může vyrazit během testování. Azure poskytuje přístup k portálu a e-mailovým oznámením při přístupu k dennímu limitu. Pokud jste tyto výstrahy nepřišli a dosáhli jste limitu, nové protokoly se v Application Insightsch dotazech nezobrazují. Nezapomeňte omezit omezení, aby nedocházelo k zbytečnému času řešení potíží. Další informace najdete v tématu [Správa cen a objemu dat v Application Insights](../azure-monitor/app/pricing.md).

## <a name="enable-application-insights-integration"></a>Povolit integraci Application Insights

Aby aplikace Function App odesílala data Application Insights, musí znát klíč instrumentace prostředku Application Insights. Klíč musí být v nastavení aplikace s názvem **APPINSIGHTS_INSTRUMENTATIONKEY**.

### <a name="new-function-app-in-the-portal"></a>Nová aplikace Function App na portálu

Při [vytváření aplikace Function App v Azure Portal](functions-create-first-azure-function.md)je ve výchozím nastavení povolena integrace Application Insights. Prostředek Application Insights má stejný název jako aplikace Function App a je vytvořený buď ve stejné oblasti, nebo v nejbližší oblasti.

Chcete-li zkontrolovat vytvářené prostředky Application Insights, vyberte ji a rozbalte okno **Application Insights** . Můžete změnit **název nového prostředku** nebo zvolit jiné **umístění** v geografických oblastech [Azure](https://azure.microsoft.com/global-infrastructure/geographies/) , kam chcete data uložit.

![Povolit Application Insights při vytváření aplikace Function App](media/functions-monitoring/enable-ai-new-function-app.png)

Když zvolíte **vytvořit**, vytvoří se prostředek Application Insights s aplikací Function App, která má `APPINSIGHTS_INSTRUMENTATIONKEY` nastavenou hodnotu v nastavení aplikace. Všechno je připravené k přechodu.

<a id="manually-connect-an-app-insights-resource"></a>
### <a name="add-to-an-existing-function-app"></a>Přidat do existující aplikace Function App 

Když vytvoříte aplikaci funkcí pomocí [Azure CLI](functions-create-first-azure-function-azure-cli.md), sady [Visual Studio](functions-create-your-first-function-visual-studio.md)nebo [Visual Studio Code](functions-create-first-function-vs-code.md), musíte vytvořit prostředek Application Insights. Potom můžete do aplikace Function app přidat klíč instrumentace z tohoto prostředku jako nastavení aplikace.

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

Dřívější verze funkcí používaly integrované monitorování, které se už nedoporučuje. Když povolíte integraci Application Insights pro takovou aplikaci Function App, musíte taky [zakázat integrované protokolování](#disable-built-in-logging).  

## <a name="view-telemetry-in-monitor-tab"></a>Zobrazit telemetrii na kartě monitorování

S [povolenou integrací Application Insights](#enable-application-insights-integration)můžete zobrazit data telemetrie na kartě **monitorování** .

1. Na stránce Function App vyberte po nakonfigurování Application Insights funkci, která byla alespoň jednou spuštěna. Pak vyberte kartu **monitorování** .

   ![Vybrat kartu monitorování](media/functions-monitoring/monitor-tab.png)

1. Vyberte možnost **aktualizovat** pravidelně, dokud se nezobrazí seznam vyvolání funkce.

   Může trvat až pět minut, než se seznam objeví, zatímco klient telemetrie data pro přenos na server vystaví. (Zpoždění se nevztahuje na [Live Metrics Stream](../azure-monitor/app/live-stream.md). Tato služba se při načtení stránky připojí k hostiteli Functions, takže protokoly se streamují přímo na stránku.)

   ![Seznam volání](media/functions-monitoring/monitor-tab-ai-invocations.png)

1. Chcete-li zobrazit protokoly pro konkrétní vyvolání funkce, vyberte odkaz na sloupec **data** pro toto vyvolání.

   ![Odkaz na podrobnosti vyvolání](media/functions-monitoring/invocation-details-link-ai.png)

   Výstup protokolování pro toto vyvolání se zobrazí na nové stránce.

   ![Podrobnosti vyvolání](media/functions-monitoring/invocation-details-ai.png)

Vidíte, že obě stránky mají **Spustit v Application Insights** odkaz na dotaz Application Insights Analytics, který načte data.

![Spustit v Application Insights](media/functions-monitoring/run-in-ai.png)

Zobrazí se následující dotaz. Můžete vidět, že seznam volání je omezený na posledních 30 dní. V seznamu se zobrazí maximálně 20 řádků (`where timestamp > ago(30d) | take 20`). Seznam podrobností o vyvolání je za posledních 30 dnů bez omezení.

![Seznam volání analýzy Application Insights](media/functions-monitoring/ai-analytics-invocation-list.png)

Další informace najdete v části [dotazování na data telemetrie](#query-telemetry-data) dále v tomto článku.

## <a name="view-telemetry-in-application-insights"></a>Zobrazit telemetrii v Application Insights

Pokud chcete Application Insights otevřít z aplikace Function App v Azure Portal, přečtěte si stránku **Přehled** aplikace Function App. V části **nakonfigurované funkce**vyberte **Application Insights**.

![Otevření Application Insights na stránce s přehledem aplikace Function App](media/functions-monitoring/ai-link.png)

Informace o tom, jak používat Application Insights, najdete v [dokumentaci k Application Insights](https://docs.microsoft.com/azure/application-insights/). V této části jsou uvedeny některé příklady, jak zobrazit data v Application Insights. Pokud už jste obeznámeni s Application Insights, můžete přejít přímo k oddílům, [jak nakonfigurovat a přizpůsobit data telemetrie](#configure-categories-and-log-levels).

![Karta Přehled Application Insights](media/functions-monitoring/metrics-explorer.png)

Následující oblasti Application Insights mohou být užitečné při vyhodnocování chování, výkonu a chyb ve vašich funkcích:

| Tabulátor | Popis |
| ---- | ----------- |
| **[Úspěšně](../azure-monitor/app/asp-net-exceptions.md)** |  Vytvářejte grafy a výstrahy na základě selhání funkcí a výjimek serveru. **Název operace** je název funkce. Pokud neimplementujete vlastní telemetrie pro závislosti, neobjeví se chyby v závislostech. |
| **[Předepsané](../azure-monitor/app/performance-counters.md)** | Analyzujte problémy s výkonem. |
| **Servery** | Zobrazení využití prostředků a propustnosti na server. Tato data můžou být užitečná pro scénáře ladění, kde funkce bogging své základní prostředky. Servery se označují jako **instance cloudových rolí**. |
| **[Metriky](../azure-monitor/app/metrics-explorer.md)** | Vytváření grafů a výstrah založených na metrikách. Metriky zahrnují počet vyvolání funkcí, dobu provádění a míru úspěšnosti. |
| **[Live Metrics Stream](../azure-monitor/app/live-stream.md)** | Zobrazit data metrik, jak jsou vytvořena v reálném čase. |

## <a name="query-telemetry-data"></a>Dotazování dat telemetrie

[Application Insights Analytics](../azure-monitor/app/analytics.md) vám umožní přístup ke všem datům telemetrie ve formě tabulek v databázi. Analýza poskytuje dotazovací jazyk pro extrakci, manipulaci a vizualizaci dat.

![Výběr analýzy](media/functions-monitoring/select-analytics.png)

![Příklad analýzy](media/functions-monitoring/analytics-traces.png)

Tady je příklad dotazu, který zobrazuje distribuci požadavků za pracovní proces za posledních 30 minut.

```
requests
| where timestamp > ago(30m) 
| summarize count() by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```

Tabulky, které jsou k dispozici, jsou zobrazeny na kartě **schéma** na levé straně. Data generovaná pomocí volání funkcí najdete v následujících tabulkách:

| Table | Popis |
| ----- | ----------- |
| **trasování** | Protokoly vytvořené modulem runtime a kódem funkce. |
| **požádal** | Jedna žádost pro každé vyvolání funkce. |
| **výjimek** | Jakékoli výjimky vyvolané modulem runtime. |
| **customMetrics** | Počet úspěšných a neúspěšných vyvolání, míra úspěšnosti a trvání. |
| **customEvents** | Události sledované modulem runtime, například: Požadavky HTTP, které aktivují funkci |
| **performanceCounters** | Informace o výkonu serverů, na kterých jsou spuštěny funkce. |

Ostatní tabulky jsou k dispozici pro testy dostupnosti a telemetrie klientů a prohlížečů. Můžete implementovat vlastní telemetrii a přidat do nich data.

V každé tabulce jsou některá data specifická pro konkrétní funkce v `customDimensions` poli.  Například následující dotaz načte všechna trasování, která mají úroveň `Error`protokolu.

```
traces 
| where customDimensions.LogLevel == "Error"
```

Modul runtime poskytuje `customDimensions.LogLevel` pole a `customDimensions.Category` . V protokolech můžete zadat další pole, která zapíšete do kódu funkce. Viz [strukturované protokolování](#structured-logging) dále v tomto článku.

## <a name="configure-categories-and-log-levels"></a>Konfigurovat kategorie a úrovně protokolu

Application Insights můžete použít bez vlastní konfigurace. Výchozí konfigurace může mít za následek velké objemy dat. Pokud používáte předplatné sady Visual Studio Azure, můžete pro Application Insights použít svůj limit dat. Později v tomto článku se dozvíte, jak nakonfigurovat a přizpůsobit data, která vaše funkce odesílají do Application Insights. V případě aplikace Function App se protokolování nakonfiguruje v souboru [Host. JSON] .

### <a name="categories"></a>Categories

Protokolovací nástroj Azure Functions zahrnuje *kategorii* pro každý protokol. Kategorie označuje, která část běhového kódu nebo kód vaší funkce zapsaly protokol. 

Modul runtime Functions vytvoří protokoly s kategorií, která začíná na "host". V protokolech verze 1. x `function started`, `function executed`, a `function completed` mají kategorii `Host.Executor`. Od verze 2. x mají tyto protokoly kategorii `Function.<YOUR_FUNCTION_NAME>`.

Pokud zapíšete protokoly do kódu funkce, kategorie je `Function` ve verzi 1. x modulu runtime Functions. Ve verzi 2. x je `Function.<YOUR_FUNCTION_NAME>.User`kategorie.

### <a name="log-levels"></a>Úrovně protokolu

Protokolovací nástroj Azure Functions zahrnuje i *úroveň protokolu* s každým protokolem. [LogLevel](/dotnet/api/microsoft.extensions.logging.loglevel) je výčet a celočíselný kód označuje relativní důležitost:

|LogLevel    |Kód|
|------------|---|
|Trasování       | 0 |
|Ladění       | 1 |
|Information | 2 |
|Upozornění     | 3 |
|Chyba       | 4 |
|Kritická    | 5 |
|Žádné        | 6 |

Úroveň `None` protokolu je vysvětleno v další části. 

### <a name="log-configuration-in-hostjson"></a>Protokolovat konfiguraci v Host. JSON

Soubor [Host. JSON] nakonfiguruje, kolik protokolování funkcí aplikace odesílá do Application Insights. Pro každou kategorii označíte minimální úroveň protokolu k odeslání. Existují dva příklady: první příklad cílí na [funkce Functions verze 2. x runtime](functions-versions.md#version-2x) (.NET Core) a druhý příklad pro modul runtime verze 1. x.

### <a name="version-2x"></a>Verze 2. x

Modul runtime v2. x používá [hierarchii filtrů protokolování .NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering). 

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

* Pro protokoly s kategorií `Host.Results` nebo `Function`můžete odeslat Application Insights `Error` jenom úroveň a vyšší. Protokoly pro `Warning` úroveň a níže jsou ignorovány.
* Pro protokoly s kategorií `Host.Aggregator`odešlete všechny protokoly do Application Insights. Úroveň protokolu je stejná jako u některých volání `Verbose`protokolovacích nástrojů, ale používá `Trace` se v souboru [Host. JSON] . `Trace`
* Pro všechny ostatní protokoly odešlete Application Insights `Information` jenom úroveň a vyšší.

Hodnota kategorie v prvku [Host. JSON] určuje protokolování pro všechny kategorie, které začínají stejnou hodnotou. `Host`v ovládacích prvcích [Host. JSON] se `Host.General`přihlašuje `Host.Results`pro, `Host.Executor`, a tak dále.

Pokud [Host. JSON] obsahuje několik kategorií, které začínají stejným řetězcem, budou se nejdřív shodovat. Předpokládejme, že chcete, aby vše z `Host.Aggregator` modulu runtime s `Error` výjimkou protokolování na úrovni `Host.Aggregator` , ale `Information` Chcete se přihlásit na úrovni:

### <a name="version-2x"></a>Verze 2. x 

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

Chcete-li potlačit všechny protokoly pro kategorii, můžete použít úroveň `None`protokolu. S touto kategorií nejsou zapsány žádné protokoly a nad ní není žádná úroveň protokolu.

V následujících částech jsou popsány hlavní kategorie protokolů, které modul runtime vytvoří. 

### <a name="category-hostresults"></a>Hostitel kategorie. výsledky

Tyto protokoly ukazují jako "žádosti" v Application Insights. Označují úspěch nebo neúspěch funkce.

![Graf požadavků](media/functions-monitoring/requests-chart.png)

Všechny tyto protokoly jsou zapisovány na `Information` úrovni. Pokud filtrujete `Warning` nad nebo výše, nezobrazí se žádná z těchto dat.

### <a name="category-hostaggregator"></a>Kategorie Host. agregátor

Tyto protokoly poskytují počty a průměry volání funkcí v konfigurovatelném časovém [](#configure-the-aggregator) intervalu. Výchozí doba je 30 sekund nebo 1 000 výsledků, podle toho, co nastane dřív. 

Protokoly jsou k dispozici v tabulce **customMetrics** v Application Insights. Jedná se o počet spuštění, úspěšnost a dobu trvání.

![dotaz customMetrics](media/functions-monitoring/custom-metrics-query.png)

Všechny tyto protokoly jsou zapisovány na `Information` úrovni. Pokud filtrujete `Warning` nad nebo výše, nezobrazí se žádná z těchto dat.

### <a name="other-categories"></a>Jiné kategorie

Všechny protokoly pro jiné kategorie než ty, které jsou již uvedeny, jsou k dispozici v tabulce **trasování** v Application Insights.

![dotaz Traces](media/functions-monitoring/analytics-traces.png)

Všechny protokoly s kategoriemi, které `Host` začínají, jsou zapsány modulem runtime Functions. V protokolech "spuštěná funkce" a "dokončená funkce `Host.Executor`" jsou kategorie. V případě úspěšného spuštění jsou `Information` tyto protokoly na úrovni. Výjimky jsou protokolovány `Error` na úrovni. Modul runtime také vytváří `Warning` protokoly úrovně, například: fronty odeslané do fronty nepoškozených zpráv.

Protokoly zapsané vaším kódem funkce mají kategorii `Function` a můžou být libovolné úrovně protokolu.

## <a name="configure-the-aggregator"></a>Konfigurace Agregátoru

Jak je uvedeno v předchozí části, modul runtime agreguje data o spouštěních funkcí v časovém intervalu. Výchozí doba je 30 sekund nebo 1 000 spuštění, podle toho, co nastane dřív. Toto nastavení můžete nakonfigurovat v souboru [Host. JSON] .  Tady je příklad:

```json
{
    "aggregator": {
      "batchSize": 1000,
      "flushTimeout": "00:00:30"
    }
}
```

## <a name="configure-sampling"></a>Konfigurace vzorkování

Application Insights má funkci [vzorkování](../azure-monitor/app/sampling.md) , která vám může chránit při vytváření příliš velkého množství dat telemetrie při dokončeném provádění v době špičky zatížení. Když frekvence příchozího provádění překročí zadanou prahovou hodnotu, Application Insights začne náhodně ignorovat některé příchozí spuštění. Výchozí nastavení maximálního počtu spuštění za sekundu je 20 (pět ve verzi 1. x). Vzorkování můžete nakonfigurovat v [Host. JSON].  Tady je příklad:

### <a name="version-2x"></a>Verze 2. x 

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

> [!NOTE]
> [Vzorkování](../azure-monitor/app/sampling.md) je ve výchozím nastavení povolené. Pokud se zdá, že chybí data, možná budete muset upravit nastavení vzorkování tak, aby vyhovovalo vašemu konkrétnímu scénáři monitorování.

## <a name="write-logs-in-c-functions"></a>Zápis protokolů ve C# funkcích

Protokoly můžete zapsat v kódu funkce, který se zobrazí jako trasování v Application Insights.

### <a name="ilogger"></a>ILogger

Místo`TraceWriter` parametru použijte ve svých funkcích parametr [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger) . Protokoly vytvořené pomocí `TraceWriter` možnosti přejít na Application Insights, ale `ILogger` umožňují [strukturované protokolování](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

S objektem zavoláte `Log<level>` [metody rozšíření v ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.loggerextensions#methods) k vytváření protokolů. `ILogger` Následující kód zapisuje `Information` protokoly do kategorie "Function".

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

Pokud vaše volání metody protokolovacího nástroje vypadá jako v předchozím příkladu, můžete zadat dotaz `customDimensions.prop__rowKey`na pole. Je `prop__` přidána předpona, aby se zajistilo, že mezi poli, které modul runtime přidává, nedochází k žádné kolizi, které přidávají kód funkce.

Můžete také zadat dotaz na původní řetězec zprávy odkazem na pole `customDimensions.prop__{OriginalFormat}`.  

Tady je ukázková reprezentace dat ve `customDimensions` formátu JSON:

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

Ve C# skriptových funkcích můžete použít `LogMetric` metodu `ILogger` rozšíření pro k vytvoření vlastních metrik v Application Insights. Zde je příklad volání metody:

```csharp
logger.LogMetric("TestMetric", 1234);
```

Tento kód je alternativou pro volání `TrackMetric` pomocí rozhraní Application Insights API pro rozhraní .NET.

## <a name="write-logs-in-javascript-functions"></a>Zápis protokolů ve funkcích JavaScriptu

V funkcích Node. js použijte `context.log` k zápisu protokolů. Strukturované protokolování není povoleno.

```
context.log('JavaScript HTTP trigger function processed a request.' + context.invocationId);
```

### <a name="custom-metrics-logging"></a>Protokolování vlastních metrik

Pokud používáte [verzi 1. x](functions-versions.md#creating-1x-apps) funkcí runtime Functions, Node. js, může pomocí `context.log.metric` metody vytvořit vlastní metriky v Application Insights. Tato metoda není aktuálně podporovaná ve verzi 2. x. Zde je příklad volání metody:

```javascript
context.log.metric("TestMetric", 1234);
```

Tento kód je alternativou pro volání `trackMetric` pomocí sady Node. js SDK pro Application Insights.

## <a name="log-custom-telemetry-in-c-functions"></a>Protokolovat vlastní telemetrii C# ve funkcích

Pomocí balíčku NuGet [Microsoft. ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) můžete odesílat vlastní data telemetrie do Application Insights. Následující C# příklad používá [vlastní rozhraní API telemetrie](../azure-monitor/app/api-custom-events-metrics.md). Příklad je pro knihovnu tříd .NET, ale kód Application Insights je stejný pro C# skript.

### <a name="version-2x"></a>Verze 2. x

Modul runtime verze 2. x používá k automatickému sladění telemetrie s aktuální operací novější funkce v Application Insights. Nemusíte ručně nastavit operaci `Id`, `ParentId`nebo `Name` pole.

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

Nenastaveno `telemetryClient.Context.Operation.Id`. Toto globální nastavení způsobuje nesprávnou korelaci, pokud mnoho funkcí běží současně. Místo toho vytvořte novou instanci telemetrie (`DependencyTelemetry`, `EventTelemetry`) a upravte její `Context` vlastnost. `Track` Pak předejte instanci telemetrie do odpovídající metody on `TelemetryClient` (`TrackDependency()`, `TrackEvent()`). Tato metoda zajišťuje, že telemetrie má správné korelační údaje pro aktuální vyvolání funkce.

## <a name="log-custom-telemetry-in-javascript-functions"></a>Protokolování vlastní telemetrie ve funkcích JavaScriptu

Tady je ukázkový fragment kódu, který odesílá vlastní telemetrii pomocí [Application Insights Node. js SDK](https://github.com/microsoft/applicationinsights-node.js):

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

`tagOverrides` Parametr`operation_Id` nastaví ID vyvolání funkce. Toto nastavení umožňuje korelovat všechny automaticky vygenerované a vlastní telemetrie pro dané volání funkce.

## <a name="dependencies"></a>Závislosti

Funkce v2 automaticky shromažďuje závislosti pro požadavky HTTP, ServiceBus a SQL.

Můžete napsat vlastní kód, který zobrazí závislosti. Příklady najdete v tématu vzorový kód v [ C# části vlastní telemetrie](#log-custom-telemetry-in-c-functions). Vzorový kód vede *mapu aplikace* v Application Insights, který vypadá jako na následujícím obrázku:

![Mapa aplikace](./media/functions-monitoring/app-map.png)

## <a name="report-issues"></a>Nahlásit problémy

Pokud chcete ohlásit problém s Application Insights integrací v rámci funkcí nebo udělat návrh nebo žádost, [vytvořte problém na GitHubu](https://github.com/Azure/Azure-Functions/issues/new).

## <a name="streaming-logs"></a>Protokoly streamování

Při vývoji aplikace často budete chtít, aby se při spuštění v Azure v reálném čase psaly do protokolů téměř v reálném čase.

Existují dva způsoby, jak zobrazit datový proud souborů protokolu generovaných spuštěním vaší funkce.

* **Integrované streamování protokolů**: platforma App Service umožňuje zobrazit datový proud souborů protokolu aplikace. Jedná se o ekvivalent výstupu zobrazeného při ladění funkcí během [místního vývoje](functions-develop-local.md) a při použití karty **test** na portálu. Zobrazí se všechny informace založené na protokolu. Další informace najdete v tématu [postup streamování protokolů](../app-service/troubleshoot-diagnostic-logs.md#streamlogs). Tato metoda streamování podporuje pouze jednu instanci a nelze ji použít s aplikací spuštěnou v systému Linux v plánu spotřeby.

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

Protokoly streamování můžete povolit pomocí [Azure PowerShell](/powershell/azure/overview). Pro prostředí PowerShell pomocí následujících příkazů přidejte svůj účet Azure, vyberte své předplatné a soubory protokolu streamu:

```powershell
Add-AzAccount
Get-AzSubscription
Get-AzSubscription -SubscriptionName "<subscription name>" | Select-AzSubscription
Get-AzWebSiteLog -Name <FUNCTION_APP_NAME> -Tail
```

## <a name="disable-built-in-logging"></a>Zakázat integrované protokolování

Pokud povolíte Application Insights, zakažte integrované protokolování, které používá Azure Storage. Integrované protokolování je užitečné pro testování s využitím lehkých úloh, ale není určené pro použití v produkčním prostředí s vysokou zátěží. Pro produkční monitorování doporučujeme Application Insights. Pokud se v produkčním prostředí používá integrované protokolování, může být záznam protokolování neúplný z důvodu omezování Azure Storage.

Pokud chcete zakázat integrované protokolování, odstraňte `AzureWebJobsDashboard` nastavení aplikace. Informace o tom, jak odstranit nastavení aplikace v Azure Portal, najdete v části **nastavení aplikace** v tématu [Správa aplikace Function App](functions-how-to-use-azure-function-app-settings.md#settings). Před odstraněním nastavení aplikace se ujistěte, že žádné existující funkce ve stejné aplikaci Function App nepoužívají nastavení pro Azure Storage triggery nebo vazby.

## <a name="next-steps"></a>Další postup

Další informace naleznete v následujících materiálech:

* [Application Insights](/azure/application-insights/)
* [Protokolování ASP.NET Core](/aspnet/core/fundamentals/logging/)

[Host. JSON]: functions-host-json.md
