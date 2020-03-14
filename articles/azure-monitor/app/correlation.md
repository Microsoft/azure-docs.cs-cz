---
title: Korelace telemetrie Azure Application Insights | Microsoft Docs
description: Korelace telemetrie Application Insights
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 06/07/2019
ms.reviewer: sergkanz
ms.openlocfilehash: 06897fffda490cdfcbb2a9cf6f55c7945e8afda0
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79276124"
---
# <a name="telemetry-correlation-in-application-insights"></a>Korelace telemetrie v Application Insights

V celém světě mikroslužeb vyžaduje každá Logická operace provedení práce v různých součástech služby. Jednotlivé komponenty můžete monitorovat samostatně pomocí [Application Insights](../../azure-monitor/app/app-insights-overview.md). Application Insights podporuje korelaci distribuované telemetrie, kterou použijete k detekci, která komponenta zodpovídá za selhání nebo snížení výkonu.

Tento článek vysvětluje datový model používaný Application Insights ke korelaci telemetrie odesílaného více komponentami. Zahrnuje postupy a protokoly šíření kontextu. Zahrnuje také implementaci korelace taktiku v různých jazycích a platformách.

## <a name="data-model-for-telemetry-correlation"></a>Datový model pro korelaci telemetrie

Application Insights definuje [datový model](../../azure-monitor/app/data-model.md) pro korelaci distribuovaných telemetrie. Pro přidružení telemetrie k logické operaci má každá položka telemetrie kontextové pole s názvem `operation_Id`. Tento identifikátor je sdílen všemi položkami telemetrie v distribuovaném trasování. Takže i v případě, že dojde ke ztrátě telemetrie z jedné vrstvy, můžete stále přidružit telemetrii nahlášenou jinými komponentami.

Distribuovaná Logická operace se typicky skládá ze sady menších operací, které jsou požadavky zpracovávané jednou z komponent. Tyto operace jsou definovány [telemetrie požadavků](../../azure-monitor/app/data-model-request-telemetry.md). Každá položka telemetrie požadavků má vlastní `id`, která ji jednoznačně identifikuje a globálně. A všechny položky telemetrie (například trasování a výjimky), které jsou přidruženy k žádosti, by měly nastavit `operation_parentId` na hodnotu `id`žádosti.

Každá odchozí operace, jako je volání HTTP jiné součásti, je reprezentována [telemetrie závislosti](../../azure-monitor/app/data-model-dependency-telemetry.md). Telemetrie závislostí také definuje vlastní `id` globálně jedinečný. Požadavek telemetrie, iniciované tímto voláním závislosti, používá tento `id` jako jeho `operation_parentId`.

Můžete sestavit zobrazení distribuované logické operace pomocí `operation_Id`, `operation_parentId`a `request.id` s `dependency.id`. Tato pole také definují pořadí volání telemetrie.

V prostředí mikroslužeb můžou trasování z komponent přejít na jiné položky úložiště. Každá součást může mít vlastní klíč instrumentace v Application Insights. Pro získání telemetrie pro logickou operaci Application Insights dotazovat data z každé položky úložiště. Pokud je počet položek úložiště velký, budete potřebovat nápovědu, kde můžete hledat další. Datový model Application Insights definuje dvě pole pro vyřešení tohoto problému: `request.source` a `dependency.target`. První pole identifikuje komponentu, která iniciovala požadavek závislosti. Druhé pole určuje, která komponenta vrátila odpověď na volání závislostí.

## <a name="example"></a>Příklad

Pojďme se podívat na příklad. Aplikace s názvem ceny akcií zobrazuje aktuální cenu na trhu na populaci pomocí externího rozhraní API s názvem Stock. Aplikace burzovních cen má stránku s názvem skladová stránka, kterou klientský webový prohlížeč otevře pomocí `GET /Home/Stock`. Aplikace se dotazuje na skladové rozhraní API pomocí `GET /api/stock/value`volání HTTP.

Výslednou telemetrii můžete analyzovat spuštěním dotazu:

```kusto
(requests | union dependencies | union pageViews)
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

Ve výsledcích si všimněte, že všechny položky telemetrie sdílejí kořen `operation_Id`. Když je ze stránky provedeno volání AJAX, je k telemetrie závislostí přiřazeno nové jedinečné ID (`qJSXU`) a ID pageView se používá jako `operation_ParentId`. Požadavek serveru pak použije ID AJAX jako `operation_ParentId`.

| itemType   | jméno                      | ID           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| pageView   | Skladová stránka                |              | STYz               | STYz         |
| závislosti | ZÍSKAT/Home/Stock           | qJSXU        | STYz               | STYz         |
| request    | ZÍSKAT domácí/burzovní            | KqKwlrSt9PA= | qJSXU              | STYz         |
| závislosti | ZÍSKAT/API/Stock/Value      | bBrf2L7mm2g = | KqKwlrSt9PA=       | STYz         |

Když se `GET /api/stock/value` voláním do externí služby, musíte znát identitu tohoto serveru, abyste mohli `dependency.target` pole nastavit správně. Pokud externí služba nepodporuje monitorování, `target` je nastaveno na název hostitele služby (například `stock-prices-api.com`). Pokud však služba identifikuje sebe sama vrácením předdefinované hlavičky protokolu HTTP, `target` obsahuje identitu služby, která umožňuje Application Insights sestavení distribuovaného trasování pomocí dotazování telemetrie z této služby.

## <a name="correlation-headers"></a>Hlavičky korelace

Application Insights se převádí na [kontext trasování W3C](https://w3c.github.io/trace-context/), který definuje:

- `traceparent`: přenese globálně jedinečné ID operace a jedinečný identifikátor volání.
- `tracestate`: přenese kontext trasování specifický pro systém.

Nejnovější verze Application Insights SDK podporuje protokol kontextu trasování, ale může být nutné se k němu přihlásit. (Bude zachována zpětná kompatibilita s předchozím protokolem korelace, který podporuje sada SDK Application Insights.)

[Korelační protokol HTTP, označovaný také jako Request-ID](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md), se už nepoužívá. Tento protokol definuje dvě hlavičky:

- `Request-Id`: přenese globálně jedinečné ID volání.
- `Correlation-Context`: přenese kolekci dvojic název-hodnota vlastností distribuované vektorizace.

Application Insights také definuje [rozšíření](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) pro protokol HTTP korelace. Používá `Request-Context` páry název-hodnota ke šíření kolekce vlastností používaných přímým volajícím nebo volaným. Sada Application Insights SDK používá tuto hlavičku k nastavení polí `dependency.target` a `request.source`.

### <a name="enable-w3c-distributed-tracing-support-for-classic-aspnet-apps"></a>Povolení podpory distribuovaného trasování W3C pro klasické aplikace ASP.NET
 
  > [!NOTE]
  >  Počínaje `Microsoft.ApplicationInsights.Web` a `Microsoft.ApplicationInsights.DependencyCollector`není nutná žádná konfigurace.

Trasování W3C – podpora kontextu je implementována zpětně kompatibilním způsobem. Očekává se korelace pro práci s aplikacemi, které jsou instrumentované s předchozími verzemi sady SDK (bez podpory W3C).

Pokud chcete dál používat starší protokol `Request-Id`, můžete zakázat trasování kontextu pomocí této konfigurace:

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

Pokud používáte starší verzi sady SDK, doporučujeme, abyste ji aktualizovali nebo použili následující konfiguraci pro povolení trasování kontextu.
Tato funkce je k dispozici v balíčcích `Microsoft.ApplicationInsights.Web` a `Microsoft.ApplicationInsights.DependencyCollector`, počínaje verzí 2.8.0-Beta1.
Ve výchozím nastavení je zakázaný. Pokud ho chcete povolit, udělejte tyto změny `ApplicationInsights.config`:

- V části `RequestTrackingTelemetryModule`přidejte prvek `EnableW3CHeadersExtraction` a nastavte jeho hodnotu na `true`.
- V části `DependencyTrackingTelemetryModule`přidejte prvek `EnableW3CHeadersInjection` a nastavte jeho hodnotu na `true`.
- Do `TelemetryInitializers`přidejte `W3COperationCorrelationTelemetryInitializer`. Bude vypadat podobně jako v tomto příkladu:

```xml
<TelemetryInitializers>
  <Add Type="Microsoft.ApplicationInsights.Extensibility.W3C.W3COperationCorrelationTelemetryInitializer, Microsoft.ApplicationInsights"/>
   ...
</TelemetryInitializers>
```

### <a name="enable-w3c-distributed-tracing-support-for-aspnet-core-apps"></a>Povolit podporu distribuovaného trasování W3C pro aplikace ASP.NET Core

 > [!NOTE]
  > Počínaje verzí `Microsoft.ApplicationInsights.AspNetCore` 2.8.0 není nutná žádná konfigurace.
 
Trasování W3C – podpora kontextu je implementována zpětně kompatibilním způsobem. Očekává se korelace pro práci s aplikacemi, které jsou instrumentované s předchozími verzemi sady SDK (bez podpory W3C).

Pokud chcete dál používat starší protokol `Request-Id`, můžete zakázat trasování kontextu pomocí této konfigurace:

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

Pokud používáte starší verzi sady SDK, doporučujeme, abyste ji aktualizovali nebo použili následující konfiguraci pro povolení trasování kontextu.

Tato funkce je ve verzi `Microsoft.ApplicationInsights.AspNetCore` 2.5.0-Beta1 a v `Microsoft.ApplicationInsights.DependencyCollector` verze 2.8.0-Beta1.
Ve výchozím nastavení je zakázaný. Pokud ho chcete povolit, nastavte `ApplicationInsightsServiceOptions.RequestCollectionOptions.EnableW3CDistributedTracing` na `true`:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry(o => 
        o.RequestCollectionOptions.EnableW3CDistributedTracing = true );
    // ....
}
```

### <a name="enable-w3c-distributed-tracing-support-for-java-apps"></a>Povolit podporu distribuovaného trasování W3C pro aplikace Java

- **Příchozí konfigurace**

  - Pro aplikace v jazyce Java EE přidejte následující značku do značky `<TelemetryModules>` v souboru ApplicationInsights. XML:

    ```xml
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule>
       <Param name = "W3CEnabled" value ="true"/>
       <Param name ="enableW3CBackCompat" value = "true" />
    </Add>
    ```
    
  - V případě aplikací pro spouštění pružiny přidejte tyto vlastnosti:

    - `azure.application-insights.web.enable-W3C=true`
    - `azure.application-insights.web.enable-W3C-backcompat-mode=true`

- **Odchozí konfigurace**

  Do souboru AI-Agent. xml přidejte následující:

  ```xml
  <Instrumentation>
    <BuiltIn enabled="true">
      <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
    </BuiltIn>
  </Instrumentation>
  ```

  > [!NOTE]
  > Režim zpětné kompatibility je ve výchozím nastavení povolený a parametr `enableW3CBackCompat` je nepovinný. Používejte ji pouze v případě, že chcete vypnout zpětnou kompatibilitu.
  >
  > V ideálním případě byste tuto funkci vypnuli, až budou všechny vaše služby aktualizované na novější verze sad SDK, které podporují protokol W3C. Důrazně doporučujeme co nejdříve přesunout na tyto novější sady SDK.

> [!IMPORTANT]
> Ujistěte se, že jsou příchozí a odchozí konfigurace přesně stejné.

### <a name="enable-w3c-distributed-tracing-support-for-web-apps"></a>Povolit podporu distribuovaného trasování W3C pro webové aplikace

Tato funkce je v `Microsoft.ApplicationInsights.JavaScript`. Ve výchozím nastavení je zakázaný. Pokud ho chcete povolit, použijte `distributedTracingMode` config. AI_AND_W3C je k dispozici kvůli zpětné kompatibilitě se staršími službami, které instrumentují Application Insights.

- **nastavení NPM (ignorovat při použití nastavení fragmentu)**

  ```javascript
  import { ApplicationInsights, DistributedTracingModes } from '@microsoft/applicationinsights-web';

  const appInsights = new ApplicationInsights({ config: {
    instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
    distributedTracingMode: DistributedTracingModes.W3C
    /* ...other configuration options... */
  } });
  appInsights.loadAppInsights();
  ```
  
- **Nastavení fragmentu (ignorovat při použití instalačního programu npm)**

  ```
  <script type="text/javascript">
  var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){i[e]=function(){var n=arguments;i.queue.push(function(){i[e].apply(i,n)})}}var i={config:e};i.initialize=!0;var a=document,t=window;setTimeout(function(){var n=a.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",a.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{i.cookie=a.cookie}catch(e){}i.queue=[],i.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var o="Track"+r[0];if(n("start"+o),n("stop"+o),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var s=t[r];t[r]=function(e,n,a,t,o){var c=s&&s(e,n,a,t,o);return!0!==c&&i["_"+r]({message:e,url:n,lineNumber:a,columnNumber:t,error:o}),c},e.autoExceptionInstrumented=!0}return i}
  (
    {
      instrumentationKey:"INSTRUMENTATION_KEY",
      distributedTracingMode: 2 // DistributedTracingModes.W3C
      /* ...other configuration options... */
    }
  );
  window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
  </script>
  ```

## <a name="opentracing-and-application-insights"></a>OpenTracing a Application Insights

Datové modely [OpenTracing a specifikace datového modelu](https://opentracing.io/) pro Application Insights mapují následujícím způsobem:

| Application Insights                  | OpenTracing                                       |
|------------------------------------   |-------------------------------------------------  |
| `Request`, `PageView`                 | `Span` s `span.kind = server`                  |
| `Dependency`                          | `Span` s `span.kind = client`                  |
| `Id` `Request` a `Dependency`    | `SpanId`                                          |
| `Operation_Id`                        | `TraceId`                                         |
| `Operation_ParentId`                  | `Reference` typu `ChildOf` (nadřazený rozsah)   |

Další informace najdete v tématu [Application Insights datovém modelu telemetrie](../../azure-monitor/app/data-model.md).

Definice konceptů OpenTracing najdete v tématu [specifikace](https://github.com/opentracing/specification/blob/master/specification.md) OpenTracing a [sémantické konvence](https://github.com/opentracing/specification/blob/master/semantic_conventions.md).

## <a name="telemetry-correlation-in-opencensus-python"></a>Korelace telemetrie v OpenCensus Pythonu

OpenCensus Python sleduje specifikace datového modelu `OpenTracing` popsané výše. Podporuje také [kontext trasování W3C](https://w3c.github.io/trace-context/) bez nutnosti jakékoli konfigurace.

### <a name="incoming-request-correlation"></a>Korelace příchozích požadavků

OpenCensus Python koreluje hlavičky W3C Trace-Context z příchozích požadavků do rozsahů, které jsou generovány z požadavků samotných. OpenCensus to provede automaticky s integrací pro tyto oblíbené webové aplikace: baňky, Django a jehlany. Stačí pouze naplnit hlavičky kontextu trasování W3C pomocí [správného formátu](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format) a odeslat je do žádosti. Tady je ukázková aplikace, která demonstruje toto:

```python
from flask import Flask
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.ext.flask.flask_middleware import FlaskMiddleware
from opencensus.trace.samplers import ProbabilitySampler

app = Flask(__name__)
middleware = FlaskMiddleware(
    app,
    exporter=AzureExporter(),
    sampler=ProbabilitySampler(rate=1.0),
)

@app.route('/')
def hello():
    return 'Hello World!'

if __name__ == '__main__':
    app.run(host='localhost', port=8080, threaded=True)
```

Tento kód spustí ukázkovou aplikaci na vašem místním počítači, která naslouchá na portu `8080`. Pro korelaci kontextu trasování odešlete požadavek na koncový bod. V tomto příkladu můžete použít příkaz `curl`:
```
curl --header "traceparent: 00-4bf92f3577b34da6a3ce929d0e0e4736-00f067aa0ba902b7-01" localhost:8080
```
Když se podíváte na [Formát záhlaví kontextu trasování](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format), můžete odvodit následující informace:

`version`: `00`

`trace-id`: `4bf92f3577b34da6a3ce929d0e0e4736`

`parent-id/span-id`: `00f067aa0ba902b7`

`trace-flags`: `01`

Pokud se podíváte na položku žádosti, která byla odeslána do Azure Monitor, uvidíte pole naplněná pomocí informací v hlavičce trasování. Tato data najdete v části protokoly (Analytics) v prostředku Azure Monitor Application Insights.

![Vyžádat telemetrii v protokolech (analýza)](./media/opencensus-python/0011-correlation.png)

Pole `id` je ve formátu `<trace-id>.<span-id>`, kde se `trace-id` předává z hlavičky trasování, která byla předána v požadavku, a `span-id` je vygenerované pole s osmi bajty pro tento rozsah.

Pole `operation_ParentId` je ve formátu `<trace-id>.<parent-id>`, kde `trace-id` a `parent-id` jsou pořízeny z hlavičky trasování, která byla předána v žádosti.

### <a name="log-correlation"></a>Korelace protokolu

OpenCensus Python umožňuje korelovat protokoly přidáním ID trasování, identifikátoru ID a příznaku vzorkování pro zaznamenávání záznamů. Tyto atributy přidáte tak, že nainstalujete [integraci protokolování](https://pypi.org/project/opencensus-ext-logging/)OpenCensus. Do `LogRecord`ch objektů Pythonu se přidají následující atributy: `traceId`, `spanId`a `traceSampled`. Všimněte si, že se to projeví jenom u protokolovacích nástrojů, které se vytvoří po integraci.

Zde je ukázková aplikace, která demonstruje tuto:

```python
import logging

from opencensus.trace import config_integration
from opencensus.trace.samplers import AlwaysOnSampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['logging'])
logging.basicConfig(format='%(asctime)s traceId=%(traceId)s spanId=%(spanId)s %(message)s')
tracer = Tracer(sampler=AlwaysOnSampler())

logger = logging.getLogger(__name__)
logger.warning('Before the span')
with tracer.span(name='hello'):
    logger.warning('In the span')
logger.warning('After the span')
```
Po spuštění tohoto kódu se v konzole nástroje zobrazí následující:
```
2019-10-17 11:25:59,382 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 Before the span
2019-10-17 11:25:59,384 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=70da28f5a4831014 In the span
2019-10-17 11:25:59,385 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 After the span
```
Všimněte si, že je k dispozici `spanId` pro zprávu protokolu v rámci rozsahu. Jedná se o stejný `spanId`, který patří do rozpětí s názvem `hello`.

Data protokolu můžete exportovat pomocí `AzureLogHandler`. Další informace najdete v [tomto článku](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python#logs).

## <a name="telemetry-correlation-in-net"></a>Korelace telemetrie v .NET

V průběhu času .NET definovalo několik způsobů, jak sladit protokoly telemetrie a diagnostiky:

- `System.Diagnostics.CorrelationManager` umožňuje sledování [LogicalOperationStack a ActivityId](https://msdn.microsoft.com/library/system.diagnostics.correlationmanager.aspx).
- `System.Diagnostics.Tracing.EventSource` a trasování událostí pro Windows (ETW) definují metodu [SetCurrentThreadActivityId](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid.aspx) .
- `ILogger` používá [rozsahy protokolů](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes).
- Windows Communication Foundation (WCF) a kabel HTTP nahoru "aktuální" šíření kontextu.

Tyto metody ale nepovolily automatickou podporu distribuovaného trasování. `DiagnosticSource` podporuje automatickou korelaci mezi počítači. Knihovny .NET podporují `DiagnosticSource` a umožňují automatické šíření mezipočítačového kontextu prostřednictvím přenosu, jako je například HTTP.

[Příručka pro uživatele aktivity](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) v `DiagnosticSource` vysvětluje základy sledování aktivit.

ASP.NET Core 2,0 podporuje extrakci hlaviček protokolu HTTP a spuštění nových aktivit.

`System.Net.Http.HttpClient`počínaje verzí 4.1.0 podporuje automatické vkládání korelačních hlaviček protokolu HTTP a sledování volání HTTP jako aktivit.

Pro klasický ASP.NET je k dispozici nový modul HTTP, [Microsoft. ASPNET. TelemetryCorrelation](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/). Tento modul implementuje korelaci telemetrie pomocí `DiagnosticSource`. Spustí aktivitu na základě hlaviček příchozích požadavků. Také koreluje telemetrii z různých fází zpracování požadavků, a to i v případě, že je každá fáze zpracování služby Internetová informační služba (IIS) spuštěna v jiném spravovaném vlákně.

Sada Application Insights SDK počínaje verzí 2.4.0-Beta1 používá `DiagnosticSource` a `Activity` k shromažďování telemetrie a k přidružení k aktuální aktivitě.

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-the-java-sdk"></a>Korelace telemetrie v sadě Java SDK

[Sada Application Insights SDK pro jazyk Java](../../azure-monitor/app/java-get-started.md) verze 2.0.0 nebo novější podporuje automatickou korelaci telemetrie. Automaticky vyplní `operation_id` pro veškerou telemetrii (jako jsou trasování, výjimky a vlastní události) vydaná v rámci požadavku. Také šíří hlavičky korelace (popsané dříve) pro volání služby-služba prostřednictvím protokolu HTTP, pokud je nakonfigurován [Agent Java SDK](../../azure-monitor/app/java-agent.md) .

> [!NOTE]
> Pro funkci Correlation se podporují jenom volání prostřednictvím Apache HttpClient. Pružinové RestTemplate i Feign se dají v digestoři použít s Apache HttpClient.

V současné době není podporováno automatické šíření kontextu napříč technologiemi zasílání zpráv (například Kafka, RabbitMQ a Azure Service Bus). Takové scénáře je možné kódovat ručně pomocí `trackDependency` a `trackRequest`ch metod. V těchto metodách představuje telemetrie závislostí zprávu, která je zařazena do fronty producentem. Požadavek představuje zprávu, kterou zpracovává příjemce. V takovém případě by se měly do vlastností zprávy šířit jak `operation_id`, tak `operation_parentId`.

### <a name="telemetry-correlation-in-asynchronous-java-applications"></a>Korelace telemetrie v asynchronních aplikacích Java

Chcete-li zjistit, jak korelace telemetrie v asynchronní aplikaci pro spouštění pomocí pružiny, přečtěte si téma [distribuované trasování v asynchronních aplikacích Java](https://github.com/Microsoft/ApplicationInsights-Java/wiki/Distributed-Tracing-in-Asynchronous-Java-Applications). Tento článek poskytuje pokyny pro instrumentaci [ThreadPoolTaskExecutor](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskExecutor.html) a [ThreadPoolTaskScheduler](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskScheduler.html)pružiny.


<a name="java-role-name"></a>
## <a name="role-name"></a>Název role

Možná budete chtít přizpůsobit způsob, jakým se názvy komponent zobrazují v [mapě aplikace](../../azure-monitor/app/app-map.md). K tomu můžete ručně nastavit `cloud_RoleName` provedením jedné z následujících akcí:

- V Application Insights Java SDK 2.5.0 a novější, můžete `cloud_RoleName` zadat přidáním `<RoleName>` do souboru ApplicationInsights. XML:

  ```XML
  <?xml version="1.0" encoding="utf-8"?>
  <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">
     <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>
     <RoleName>** Your role name **</RoleName>
     ...
  </ApplicationInsights>
  ```

- Pokud používáte jarní spouštění s Application Insightsm Starter Boot Starter, stačí nastavit vlastní název aplikace v souboru Application. Properties:

  `spring.application.name=<name-of-app>`

  Jaře Boot Starter automaticky přiřadí `cloudRoleName` k hodnotě, kterou zadáte pro vlastnost `spring.application.name`.

## <a name="next-steps"></a>Další kroky

- Zápis [vlastní telemetrie](../../azure-monitor/app/api-custom-events-metrics.md).
- Pokročilé scénáře korelace v ASP.NET Core a ASP.NET najdete v tématu [sledování vlastních operací](custom-operations-tracking.md).
- Přečtěte si další informace o [nastavení cloud_RoleName](../../azure-monitor/app/app-map.md#set-cloud-role-name) pro jiné sady SDK.
- Připojte všechny komponenty mikroslužby na Application Insights. Podívejte se na [podporované platformy](../../azure-monitor/app/platforms.md).
- Seznamte se s [datovým modelem](../../azure-monitor/app/data-model.md) pro Application Insights typy.
- Naučte se, jak můžete [zvětšit a filtrovat telemetrii](../../azure-monitor/app/api-filtering-sampling.md).
- Přečtěte si [referenční informace o konfiguraci Application Insights](configuration-with-applicationinsights-config.md).
