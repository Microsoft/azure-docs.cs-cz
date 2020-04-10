---
title: Korelace telemetrie Azure Application Insights | Dokumenty společnosti Microsoft
description: Korelace telemetrie Application Insights
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 06/07/2019
ms.reviewer: sergkanz
ms.openlocfilehash: 6ceace1ee93fab8c0a46ed4a67850fc87a5cdad2
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991224"
---
# <a name="telemetry-correlation-in-application-insights"></a>Korelace telemetrie v application insights

Ve světě mikroslužeb vyžaduje každá logická operace práci v různých součástech služby. Každou z těchto součástí můžete sledovat samostatně pomocí [application insights](../../azure-monitor/app/app-insights-overview.md). Application Insights podporuje distribuovanou telemetricko korelaci, kterou použijete ke zjištění, která komponenta je zodpovědná za selhání nebo snížení výkonu.

Tento článek vysvětluje datový model používaný Application Insights ke korelaci telemetrie odeslané více součástmi. Zahrnuje techniky a protokoly šíření kontextu. Zahrnuje také provádění korelační taktiky na různých jazycích a platformách.

## <a name="data-model-for-telemetry-correlation"></a>Datový model pro korelaci telemetrie

Application Insights definuje [datový model](../../azure-monitor/app/data-model.md) pro distribuovanou korelaci telemetrie. Chcete-li přidružit telemetrii k logické `operation_Id`operaci, každá položka telemetrie má kontextové pole s názvem . Tento identifikátor je sdílena každou položkou telemetrie v distribuovanétrasování. Takže i v případě, že ztratíte telemetrická data z jedné vrstvy, stále můžete přidružit telemetrie hlášené jinými součástmi.

Distribuovaná logická operace se obvykle skládá ze sady menších operací, které jsou požadavky zpracované jednou z komponent. Tyto operace jsou definovány [telemetrií požadavku](../../azure-monitor/app/data-model-request-telemetry.md). Každá položka telemetrie `id` požadavku má své vlastní, které ji identifikují jednoznačně a globálně. A všechny položky telemetrie (například trasování a výjimky), `operation_parentId` které jsou přidruženy k požadavku by měl nastavit na hodnotu požadavku `id`.

Každá odchozí operace, například volání HTTP do jiné součásti, je [reprezentována telemetrií závislostí](../../azure-monitor/app/data-model-dependency-telemetry.md). Telemetrie závislostí také `id` definuje vlastní, která je globálně jedinečná. Požadavek telemetrie, iniciované toto volání závislostí, používá to jako `id` jeho `operation_parentId`.

Zobrazení distribuované logické operace můžete `operation_Id` `operation_parentId`vytvořit `request.id` pomocí `dependency.id`aplikace , a s . Tato pole také definovat pořadí příčinné souvislosti volání telemetrie.

V prostředí mikroslužeb trasování z komponent může přejít na různé položky úložiště. Každá komponenta může mít svůj vlastní klíč instrumentace v Application Insights. Chcete-li získat telemetrická data pro logickou operaci, Application Insights dotazuje data z každé položky úložiště. Když je počet položek úložiště velký, budete potřebovat nápovědu, kde hledat dál. Datový model Application Insights definuje dvě pole `request.source` k `dependency.target`vyřešení tohoto problému: a . První pole identifikuje součást, která iniciovala požadavek na závislost. Druhé pole určuje, která komponenta vrátila odpověď na volání závislosti.

## <a name="example"></a>Příklad

Pojďme se podívat na příklad. Aplikace s názvem Ceny akcií zobrazuje aktuální tržní cenu akcie pomocí externího rozhraní API s názvem Sklad. Aplikace Ceny akcií má stránku s názvem Stránka Akcie, kterou klientský webový prohlížeč otevře pomocí `GET /Home/Stock`aplikace . Aplikace dotazuje rozhraní STOCK API `GET /api/stock/value`pomocí volání HTTP .

Výslednou telemetrii můžete analyzovat spuštěním dotazu:

```kusto
(requests | union dependencies | union pageViews)
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

Ve výsledcích si všimněte, že `operation_Id`všechny položky telemetrie sdílet kořen . Při volání Ajax ze stránky, nové jedinečné ID (`qJSXU`) je přiřazena k telemetrii závislostí `operation_ParentId`a ID pageView se používá jako . Požadavek serveru pak používá Ajax `operation_ParentId`ID jako .

| Itemtype   | jméno                      | ID           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| pageView   | Stránka zásob                |              | STYz (STYZ)               | STYz (STYZ)         |
| Závislost | GET /Úvod/Skladem           | qJSXU        | STYz (STYZ)               | STYz (STYZ)         |
| Požadavek    | DOMŮ/Skladem            | KqKwlrSt9PA= | qJSXU              | STYz (STYZ)         |
| Závislost | GET /api/stock/value      | bBrf2L7mm2g= | KqKwlrSt9PA=       | STYz (STYZ)         |

Při volání `GET /api/stock/value` externí služby, musíte znát identitu tohoto serveru, takže můžete `dependency.target` správně nastavit pole. Pokud externí služba nepodporuje monitorování, `target` je nastavena na název hostitele `stock-prices-api.com`služby (například). Ale pokud služba identifikuje tím, že vrátí předdefinované hlavičky HTTP, obsahuje identitu služby, `target` která umožňuje Application Insights k vytvoření distribuované trasování dotazem telemetrie z této služby.

## <a name="correlation-headers"></a>Záhlaví korelace

Application Insights přechází na [w3c trasovací kontext](https://w3c.github.io/trace-context/), který definuje:

- `traceparent`: Nese globálně jedinečné ID operace a jedinečný identifikátor volání.
- `tracestate`: Nese kontext trasování specifické pro systém.

Nejnovější verze sady Application Insights SDK podporuje protokol Trace-Context, ale možná budete muset přihlásit. (Zpětná kompatibilita s předchozím korelačním protokolem podporovaným sadou Application Insights SDK bude zachována.)

[Korelace protokolu HTTP, také volal Request-Id](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md), je zastaralá. Tento protokol definuje dvě záhlaví:

- `Request-Id`: Nese globálně jedinečné ID volání.
- `Correlation-Context`: Nese kolekci dvojic název-hodnota distribuované trasování.

Application Insights také definuje [rozšíření](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) pro korelaci protokolu HTTP. Používá `Request-Context` dvojice název-hodnota k šíření kolekce vlastností používaných volajícího nebo volaného. Sada Application Insights SDK používá `dependency.target` tuto `request.source` hlavičku k nastavení polí a.

### <a name="enable-w3c-distributed-tracing-support-for-classic-aspnet-apps"></a>Povolte podporu distribuovaného trasování W3C pro klasické ASP.NET aplikace
 
  > [!NOTE]
  >  Počínaje `Microsoft.ApplicationInsights.Web` a `Microsoft.ApplicationInsights.DependencyCollector`, není nutná žádná konfigurace.

W3C Trace-Context podpora je implementována zpětně kompatibilním způsobem. Očekává se, že korelace bude fungovat s aplikacemi, které jsou instrumentovány s předchozími verzemi sady SDK (bez podpory W3C).

Pokud chcete nadále používat `Request-Id` starší protokol, můžete zakázat trasování kontextu pomocí této konfigurace:

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

Pokud spustíte starší verzi sady SDK, doporučujeme ji aktualizovat nebo použít následující konfiguraci pro povolení kontextu trasování.
Tato funkce je `Microsoft.ApplicationInsights.Web` k `Microsoft.ApplicationInsights.DependencyCollector` dispozici v balíčcích a, počínaje verzí 2.8.0-beta1.
Ve výchozím nastavení je zakázán. Chcete-li jej povolit, proveďte tyto změny v `ApplicationInsights.config`:

- V `RequestTrackingTelemetryModule`části `EnableW3CHeadersExtraction` přidejte prvek a `true`nastavte jeho hodnotu na .
- V `DependencyTrackingTelemetryModule`části `EnableW3CHeadersInjection` přidejte prvek a `true`nastavte jeho hodnotu na .
- Přidat `W3COperationCorrelationTelemetryInitializer` `TelemetryInitializers`pod . Bude vypadat podobně jako v tomto příkladu:

```xml
<TelemetryInitializers>
  <Add Type="Microsoft.ApplicationInsights.Extensibility.W3C.W3COperationCorrelationTelemetryInitializer, Microsoft.ApplicationInsights"/>
   ...
</TelemetryInitializers>
```

### <a name="enable-w3c-distributed-tracing-support-for-aspnet-core-apps"></a>Povolení podpory distribuovaného trasování W3C pro aplikace ASP.NET Core

 > [!NOTE]
  > Počínaje `Microsoft.ApplicationInsights.AspNetCore` verzí 2.8.0 není nutná žádná konfigurace.
 
W3C Trace-Context podpora je implementována zpětně kompatibilním způsobem. Očekává se, že korelace bude fungovat s aplikacemi, které jsou instrumentovány s předchozími verzemi sady SDK (bez podpory W3C).

Pokud chcete nadále používat `Request-Id` starší protokol, můžete zakázat trasování kontextu pomocí této konfigurace:

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

Pokud spustíte starší verzi sady SDK, doporučujeme ji aktualizovat nebo použít následující konfiguraci pro povolení kontextu trasování.

Tato funkce `Microsoft.ApplicationInsights.AspNetCore` je ve verzi 2.5.0-beta1 a ve `Microsoft.ApplicationInsights.DependencyCollector` verzi 2.8.0-beta1.
Ve výchozím nastavení je zakázán. Chcete-li jej `ApplicationInsightsServiceOptions.RequestCollectionOptions.EnableW3CDistributedTracing` `true`povolit, nastavte na :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry(o => 
        o.RequestCollectionOptions.EnableW3CDistributedTracing = true );
    // ....
}
```

### <a name="enable-w3c-distributed-tracing-support-for-java-apps"></a>Povolení podpory distribuovaného trasování W3C pro aplikace Java

#### <a name="java-30-agent"></a>Java 3.0 agent

  Java 3.0 agent podporuje W3C po vybalení z krabice a není nutná žádná další konfigurace. 

#### <a name="java-sdk"></a>Java SDK
- **Příchozí konfigurace**

  - Pro aplikace Java EE přidejte `<TelemetryModules>` do tagu v ApplicationInsights.xml následující:

    ```xml
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule>
       <Param name = "W3CEnabled" value ="true"/>
       <Param name ="enableW3CBackCompat" value = "true" />
    </Add>
    ```
    
  - Pro aplikace jarního spuštění přidejte tyto vlastnosti:

    - `azure.application-insights.web.enable-W3C=true`
    - `azure.application-insights.web.enable-W3C-backcompat-mode=true`

- **Odchozí konfigurace**

  Do souboru AI-Agent.xml přidejte následující:

  ```xml
  <Instrumentation>
    <BuiltIn enabled="true">
      <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
    </BuiltIn>
  </Instrumentation>
  ```

  > [!NOTE]
  > Režim zpětné kompatibility je ve `enableW3CBackCompat` výchozím nastavení povolen a parametr je volitelný. Použijte ji pouze v případě, že chcete vypnout zpětnou kompatibilitu.
  >
  > V ideálním případě byste to vypnout, když všechny vaše služby byly aktualizovány na novější verze sad SDK, které podporují protokol W3C. Důrazně doporučujeme, abyste se co nejdříve přesunuli na tyto novější sady SDK.

> [!IMPORTANT]
> Ujistěte se, že příchozí a odchozí konfigurace jsou přesně stejné.

### <a name="enable-w3c-distributed-tracing-support-for-web-apps"></a>Povolení podpory distribuovaného trasování W3C pro webové aplikace

Tato funkce `Microsoft.ApplicationInsights.JavaScript`je v . Ve výchozím nastavení je zakázán. Chcete-li jej `distributedTracingMode` povolit, použijte config. AI_AND_W3C je poskytována zpětná kompatibilita s všechny starší služby instrumentované Application Insights.

- **npm nastavení (ignorovat, pokud používáte nastavení úryvku)**

  ```javascript
  import { ApplicationInsights, DistributedTracingModes } from '@microsoft/applicationinsights-web';

  const appInsights = new ApplicationInsights({ config: {
    instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
    distributedTracingMode: DistributedTracingModes.W3C
    /* ...other configuration options... */
  } });
  appInsights.loadAppInsights();
  ```
  
- **Nastavení úryvku (ignorovat, pokud používáte nastavení npm)**

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

## <a name="opentracing-and-application-insights"></a>OpenTracing a přehledy aplikací

[Specifikace datového modelu OpenTracing](https://opentracing.io/) a datové modely Application Insights mapují následujícím způsobem:

| Application Insights                   | OpenTracing                                        |
|------------------------------------    |-------------------------------------------------    |
| `Request`, `PageView`                  | `Span`S`span.kind = server`                    |
| `Dependency`                           | `Span`S`span.kind = client`                    |
| `Id`a `Request``Dependency`     | `SpanId`                                            |
| `Operation_Id`                         | `TraceId`                                           |
| `Operation_ParentId`                   | `Reference`typu `ChildOf` (nadřazené rozpětí)     |

Další informace naleznete v [tématu Application Insights telemetrie datový model](../../azure-monitor/app/data-model.md).

Definice konceptů OpenTracing naleznete ve [specifikaci](https://github.com/opentracing/specification/blob/master/specification.md) OpenTracing a [sémantické konvence](https://github.com/opentracing/specification/blob/master/semantic_conventions.md).

## <a name="telemetry-correlation-in-opencensus-python"></a>Korelace telemetrie v OpenCensus Python

OpenCensus Python sleduje `OpenTracing` specifikace datového modelu popsané dříve. Podporuje také [W3C Trace-Context](https://w3c.github.io/trace-context/) bez nutnosti jakékoli konfigurace.

### <a name="incoming-request-correlation"></a>Korelace příchozích požadavků

OpenCensus Python koreluje hlavičky W3C Trace-Context z příchozích požadavků na rozsahy, které jsou generovány z samotných požadavků. OpenCensus to udělá automaticky s integrací pro tyto populární webové aplikace architektury: Flask, Django a Pyramid. Stačí vyplnit W3C Trace-Context záhlaví se [správným formátem](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format) a odeslat je s požadavkem. Zde je ukázka aplikace Flask, která ukazuje toto:

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

Tento kód spustí ukázkovou aplikaci Flask na `8080`místním počítači a naslouchá portu . Chcete-li korelovat kontextu trasování, odešlete požadavek do koncového bodu. V tomto příkladu `curl` můžete použít příkaz:
```
curl --header "traceparent: 00-4bf92f3577b34da6a3ce929d0e0e4736-00f067aa0ba902b7-01" localhost:8080
```
Při pohledu na [formát záhlaví trace-context](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format)můžete odvodit následující informace:

`version`: `00`

`trace-id`: `4bf92f3577b34da6a3ce929d0e0e4736`

`parent-id/span-id`: `00f067aa0ba902b7`

`trace-flags`: `01`

Pokud se podíváte na položku požadavku, která byla odeslána do Azure Monitoru, můžete zobrazit pole naplněná informacemi záhlaví trasování. Tato data najdete v části Protokoly (Analytics) v prostředku Azure Monitor Application Insights.

![Požadavek na telemetrii v protokolech (Analytics)](./media/opencensus-python/0011-correlation.png)

Pole `id` je ve `<trace-id>.<span-id>`formátu , `trace-id` kde je převzata ze záhlaví trasování, která byla předána v požadavku a `span-id` je generováno 8bajtové pole pro toto rozpětí.

Pole `operation_ParentId` je ve `<trace-id>.<parent-id>`formátu , `trace-id` kde `parent-id` jsou převzaty z hlavičky trasování, která byla předána v požadavku.

### <a name="log-correlation"></a>Korelace protokolů

OpenCensus Python umožňuje korelovat protokoly přidáním ID trasování, ID rozsahu a příznak vzorkování do protokolu záznamů. Tyto atributy přidáte instalací [integrace protokolování](https://pypi.org/project/opencensus-ext-logging/)OpenCensus . K objektům `LogRecord` Pythonu budou přidány `traceId` `spanId`následující `traceSampled`atributy: , a . Všimněte si, že to se projeví pouze pro úhozy kláves, které jsou vytvořeny po integraci.

Zde je ukázková aplikace, která demonstruje toto:

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
Při spuštění tohoto kódu se v ytiskne v konzole následující:
```
2019-10-17 11:25:59,382 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 Before the span
2019-10-17 11:25:59,384 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=70da28f5a4831014 In the span
2019-10-17 11:25:59,385 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 After the span
```
Všimněte si, `spanId` že je k dispozici dárek pro zprávu protokolu, který je v rozpětí. Toto je `spanId` stejné, které patří `hello`do rozsahu s názvem .

Data protokolu můžete exportovat `AzureLogHandler`pomocí aplikace . Další informace najdete v [tomto článku](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python#logs).

## <a name="telemetry-correlation-in-net"></a>Korelace telemetrie v rozhraní .NET

V průběhu času rozhraní .NET definovalo několik způsobů korelace telemetrických a diagnostických protokolů:

- `System.Diagnostics.CorrelationManager`umožňuje sledování [LogicalOperationStack a ActivityId](https://msdn.microsoft.com/library/system.diagnostics.correlationmanager.aspx).
- `System.Diagnostics.Tracing.EventSource`a trasování událostí pro Windows (ETW) definují metodu [SetCurrentThreadActivityId.](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid.aspx)
- `ILogger`používá [protokolobory](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes).
- Windows Communication Foundation (WCF) a HTTP drát y "aktuální" šíření kontextu.

Tyto metody však neumožnily podporu automatického distribuovaného trasování. `DiagnosticSource`podporuje automatickou korelaci mezi stroji. Knihovny .NET `DiagnosticSource` podporují a umožňují automatické šíření kontextu korelace pomocí přenosu, například HTTP.

[Uživatelská příručka k aktivitám](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) v `DiagnosticSource` příručce vysvětluje základy sledování aktivit.

ASP.NET Core 2.0 podporuje extrakci http hlaviček a spouštění nových aktivit.

`System.Net.Http.HttpClient`, počínaje verzí 4.1.0, podporuje automatické vkládání korelačních hlaviček HTTP a sledování volání HTTP jako aktivit.

Je nový modul HTTP, [Microsoft.AspNet.TelemetryCorrelation](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/), pro klasické ASP.NET. Tento modul implementuje korelaci telemetrie pomocí `DiagnosticSource`. Spustí aktivitu na základě hlavičky příchozích požadavků. Také koreluje telemetrická data z různých fází zpracování požadavků, i když každá fáze zpracování Internetové informační služby (IIS) běží v jiném spravovaném vlákně.

Sada Application Insights SDK, počínaje verzí 2.4.0-beta1, používá `DiagnosticSource` a `Activity` shromažďuje telemetrii a přidružuje ji k aktuální aktivitě.

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-java"></a>Korelace telemetrie v Jazyce Java

[Java agent](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent) a [java sdk](../../azure-monitor/app/java-get-started.md) verze 2.0.0 nebo novější podporuje automatickou korelaci telemetrie. Automaticky naplní `operation_id` všechny telemetrie (jako trasování, výjimky a vlastní události) vydané v rámci požadavku. Také šíří záhlaví korelace (popsané výše) pro volání služby služby prostřednictvím protokolu HTTP, pokud je nakonfigurován [agent Java SDK.](../../azure-monitor/app/java-agent.md)

> [!NOTE]
> Application Insights Java agent automaticky shromažďuje požadavky a závislosti pro JMS, Kafka, Netty/Webflux a další. Pro Java SDK jsou podporovány pouze volání prostřednictvím Apache HttpClient jsou podporovány pro funkci korelace. Automatické šíření kontextu napříč technologiemi zasílání zpráv (jako jsou Kafka, RabbitMQ a Azure Service Bus) není v sdk podporováno. 

> [!NOTE]
> Chcete-li sbírat vlastní telemetrii, musíte instrumentovat aplikaci pomocí sady Java 2.6 SDK. 

### <a name="role-names"></a>Názvy rolí

Můžete chtít přizpůsobit způsob zobrazení názvů součástí v [mapě aplikace](../../azure-monitor/app/app-map.md). Chcete-li tak učinit, můžete `cloud_RoleName` ručně nastavit provedením jedné z následujících akcí:

- Pro Application Insights Java agent 3.0 nastavte název role cloudu takto:

    ```json
    {
      "instrumentationSettings": {
        "preview": {
          "roleName": "my cloud role name"
        }
      }
    }
    ```
    Můžete také nastavit název role cloudu `APPLICATIONINSIGHTS_ROLE_NAME`pomocí proměnné prostředí .

- Pomocí sady Application Insights java sdcharty 2.5.0 `<RoleName>` a novějších můžete zadat přidání souboru `cloud_RoleName` ApplicationInsights.xml:

  ```XML
  <?xml version="1.0" encoding="utf-8"?>
  <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">
     <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>
     <RoleName>** Your role name **</RoleName>
     ...
  </ApplicationInsights>
  ```

- Pokud používáte jarní spuštění se startérem jarního startu Application Insights, stačí nastavit vlastní název aplikace v souboru application.properties:

  `spring.application.name=<name-of-app>`

  Startér jarního startu `cloudRoleName` automaticky přiřadí hodnotu, kterou zadáte pro `spring.application.name` vlastnost.

## <a name="next-steps"></a>Další kroky

- Napište [vlastní telemetrii](../../azure-monitor/app/api-custom-events-metrics.md).
- Pokročilé scénáře korelace v ASP.NET jádra a ASP.NET naleznete v [tématu Sledování vlastních operací](custom-operations-tracking.md).
- Přečtěte si další informace o [nastavení cloud_RoleName](../../azure-monitor/app/app-map.md#set-cloud-role-name) pro ostatní sady SDK.
- Zadejte všechny součásti mikroslužeb na Application Insights. Podívejte se na [podporované platformy](../../azure-monitor/app/platforms.md).
- Podívejte se na [datový model](../../azure-monitor/app/data-model.md) pro typy Application Insights.
- Přečtěte si, jak [rozšířit a filtrovat telemetrii](../../azure-monitor/app/api-filtering-sampling.md).
- Zkontrolujte [odkaz na konfiguraci Application Insights](configuration-with-applicationinsights-config.md).
