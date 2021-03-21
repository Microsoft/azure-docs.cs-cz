---
title: Korelace telemetrie Azure Application Insights | Microsoft Docs
description: Korelace telemetrie Application Insights
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 06/07/2019
ms.reviewer: sergkanz
ms.custom: devx-track-python, devx-track-csharp
ms.openlocfilehash: beaeb0131a2c9b326d663f6fcbb8273a9b52b412
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102100963"
---
# <a name="telemetry-correlation-in-application-insights"></a>Korelace telemetrie v Application Insights

V celém světě mikroslužeb vyžaduje každá Logická operace provedení práce v různých součástech služby. Jednotlivé komponenty můžete monitorovat samostatně pomocí [Application Insights](../../azure-monitor/app/app-insights-overview.md). Application Insights podporuje korelaci distribuované telemetrie, kterou použijete k detekci, která komponenta zodpovídá za selhání nebo snížení výkonu.

Tento článek vysvětluje datový model používaný Application Insights ke korelaci telemetrie odesílaného více komponentami. Zahrnuje postupy a protokoly šíření kontextu. Zahrnuje také implementaci korelace taktiku v různých jazycích a platformách.

## <a name="data-model-for-telemetry-correlation"></a>Datový model pro korelaci telemetrie

Application Insights definuje [datový model](../../azure-monitor/app/data-model.md) pro korelaci distribuovaných telemetrie. Pro přidružení telemetrie k logické operaci má každá položka telemetrie kontextové pole s názvem `operation_Id` . Tento identifikátor je sdílen všemi položkami telemetrie v distribuovaném trasování. Takže i v případě, že dojde ke ztrátě telemetrie z jedné vrstvy, můžete stále přidružit telemetrii nahlášenou jinými komponentami.

Distribuovaná Logická operace se typicky skládá ze sady menších operací, které jsou požadavky zpracovávané jednou z komponent. Tyto operace jsou definovány [telemetrie požadavků](../../azure-monitor/app/data-model-request-telemetry.md). Každá položka telemetrie požadavků má svou vlastní `id` identifikaci jedinečnou a globálně. A všechny položky telemetrie (například trasování a výjimky), které jsou přidruženy k žádosti, by měly být nastaveny na `operation_parentId` hodnotu požadavku `id` .

Každá odchozí operace, jako je volání HTTP jiné součásti, je reprezentována [telemetrie závislosti](../../azure-monitor/app/data-model-dependency-telemetry.md). Telemetrie závislostí definuje také vlastní `id` globálně jedinečný. Požadavek telemetrie, iniciované tímto voláním závislosti, používá `id` jako svůj `operation_parentId` .

Můžete sestavit zobrazení distribuované logické operace pomocí `operation_Id` , a `operation_parentId` `request.id` s `dependency.id` . Tato pole také definují pořadí volání telemetrie.

V prostředí mikroslužeb můžou trasování z komponent přejít na jiné položky úložiště. Každá součást může mít vlastní klíč instrumentace v Application Insights. Pro získání telemetrie pro logickou operaci Application Insights dotazovat data z každé položky úložiště. Pokud je počet položek úložiště velký, budete potřebovat nápovědu, kde můžete hledat další. Datový model Application Insights definuje dvě pole pro vyřešení tohoto problému: `request.source` a `dependency.target` . První pole identifikuje komponentu, která iniciovala požadavek závislosti. Druhé pole určuje, která komponenta vrátila odpověď na volání závislostí.

## <a name="example"></a>Příklad

Podívejme se na příklad. Aplikace s názvem ceny akcií zobrazuje aktuální cenu na trhu na populaci pomocí externího rozhraní API s názvem Stock. Aplikace burzovních cen má stránku s názvem skladová stránka, kterou klientský webový prohlížeč otevře pomocí `GET /Home/Stock` . Aplikace se dotazuje na skladové rozhraní API pomocí volání HTTP `GET /api/stock/value` .

Výslednou telemetrii můžete analyzovat spuštěním dotazu:

```kusto
(requests | union dependencies | union pageViews)
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

Ve výsledcích si všimněte, že všechny položky telemetrie sdílejí kořen `operation_Id` . Když je ze stránky provedeno volání AJAX, `qJSXU` je k telemetrie závislostí přiřazeno nové jedinečné ID () a ID pageView se používá jako `operation_ParentId` . Požadavek serveru pak použije ID AJAX jako `operation_ParentId` .

| itemType   | name                      | ID           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| pageView   | Skladová stránka                | STYz         |                    | STYz         |
| závislosti | ZÍSKAT/Home/Stock           | qJSXU        | STYz               | STYz         |
| Request    | ZÍSKAT domácí/burzovní            | KqKwlrSt9PA = | qJSXU              | STYz         |
| závislosti | ZÍSKAT/API/Stock/Value      | bBrf2L7mm2g = | KqKwlrSt9PA =       | STYz         |

Při volání `GET /api/stock/value` externí služby potřebujete znát identitu tohoto serveru, abyste mohli `dependency.target` odpovídajícím způsobem nastavit pole. Pokud externí služba nepodporuje monitorování, `target` je nastavena na název hostitele služby (například `stock-prices-api.com` ). Pokud však služba identifikuje sebe sama vrácením předdefinované hlavičky HTTP, `target` obsahuje identitu služby, která umožňuje Application Insights sestavit distribuované trasování pomocí dotazování telemetrie z této služby.

## <a name="correlation-headers-using-w3c-tracecontext"></a>Korelační hlavičky pomocí formátu W3C TraceContext

Application Insights se převádí na [kontext trasování W3C](https://w3c.github.io/trace-context/), který definuje:

- `traceparent`: Provede globálně jedinečné ID operace a jedinečný identifikátor volání.
- `tracestate`: Přenese kontext trasování specifický pro systém.

Nejnovější verze sady Application Insights SDK podporuje protokol Trace-Context, ale možná se k ní budete muset přihlásit. (Bude zachována zpětná kompatibilita s předchozím protokolem korelace, který podporuje sada SDK Application Insights.)

[Korelační protokol HTTP, označovaný také jako Request-ID](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md), se už nepoužívá. Tento protokol definuje dvě hlavičky:

- `Request-Id`: Provede globálně jedinečné ID volání.
- `Correlation-Context`: Přenese kolekci dvojic název-hodnota pro distribuované vlastnosti trasování.

Application Insights také definuje [rozšíření](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) pro protokol HTTP korelace. Používá `Request-Context` páry název-hodnota ke šíření kolekce vlastností používaných přímým volajícím nebo volaným. Sada Application Insights SDK používá tuto hlavičku k nastavení `dependency.target` polí a `request.source` .

Mapování datových modelů v [kontextu W3C Trace-Context](https://w3c.github.io/trace-context/) a Application Insights následujícím způsobem:

| Application Insights                   | W3C TraceContext                                      |
|------------------------------------    |-------------------------------------------------|
| `Id` z `Request` a `Dependency`     | [ID nadřazeného prvku](https://w3c.github.io/trace-context/#parent-id)                                     |
| `Operation_Id`                         | [ID trasování](https://w3c.github.io/trace-context/#trace-id)                                           |
| `Operation_ParentId`                   | [nadřazený identifikátor](https://w3c.github.io/trace-context/#parent-id) nadřazeného nadřazeného rozsahu tohoto rozsahu Pokud se jedná o kořenový rozsah, musí být toto pole prázdné.     |

Další informace najdete v tématu [Application Insights datovém modelu telemetrie](../../azure-monitor/app/data-model.md).

### <a name="enable-w3c-distributed-tracing-support-for-net-apps"></a>Povolení podpory distribuovaného trasování W3C pro aplikace .NET

Distribuované trasování založené na formátu W3C je ve výchozím nastavení povolené v všech nejnovějších .NET Framework/. NET Core SDK a v případě zpětné kompatibility se starším protokolem Request-Id.

### <a name="enable-w3c-distributed-tracing-support-for-java-apps"></a>Povolit podporu distribuovaného trasování W3C pro aplikace Java

#### <a name="java-30-agent"></a>Agent Java 3,0

  Agent Java 3,0 podporuje W3C mimo pole a není nutná žádná další konfigurace. 

#### <a name="java-sdk"></a>Java SDK
- **Příchozí konfigurace**

  - Pro aplikace v jazyce Java EE přidejte do `<TelemetryModules>` značky v ApplicationInsights.xml následující:

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

  Přidejte následující AI-Agent.xml:

  ```xml
  <Instrumentation>
    <BuiltIn enabled="true">
      <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
    </BuiltIn>
  </Instrumentation>
  ```

  > [!NOTE]
  > Režim zpětné kompatibility je ve výchozím nastavení povolený a `enableW3CBackCompat` parametr je nepovinný. Používejte ji pouze v případě, že chcete vypnout zpětnou kompatibilitu.
  >
  > V ideálním případě byste tuto funkci vypnuli, až budou všechny vaše služby aktualizované na novější verze sad SDK, které podporují protokol W3C. Důrazně doporučujeme co nejdříve přesunout na tyto novější sady SDK.

> [!IMPORTANT]
> Ujistěte se, že jsou příchozí a odchozí konfigurace přesně stejné.

### <a name="enable-w3c-distributed-tracing-support-for-web-apps"></a>Povolit podporu distribuovaného trasování W3C pro webové aplikace

Tato funkce je v systému `Microsoft.ApplicationInsights.JavaScript` . Ve výchozím nastavení je zakázaný. Pokud ho chcete povolit, použijte `distributedTracingMode` config. AI_AND_W3C je k dispozici kvůli zpětné kompatibilitě se staršími službami, které instrumentují Application Insights.

- **[nastavení založené na npm](./javascript.md#npm-based-setup)**

Přidejte následující konfiguraci:
  ```JavaScript
    distributedTracingMode: DistributedTracingModes.W3C
  ```

- **[Nastavení na základě fragmentů kódu](./javascript.md#snippet-based-setup)**

Přidejte následující konfiguraci:
  ```
      distributedTracingMode: 2 // DistributedTracingModes.W3C
  ```
> [!IMPORTANT] 
> Pokud chcete zobrazit všechny konfigurace potřebné k povolení korelace, přečtěte si [dokumentaci k korelaci JavaScriptu](./javascript.md#enable-correlation).

## <a name="telemetry-correlation-in-opencensus-python"></a>Korelace telemetrie v OpenCensus Pythonu

OpenCensus Python podporuje [kontext trasování W3C](https://w3c.github.io/trace-context/) bez nutnosti další konfigurace.

Jako referenci se dá datový model OpenCensus najít [tady](https://github.com/census-instrumentation/opencensus-specs/tree/master/trace).

### <a name="incoming-request-correlation"></a>Korelace příchozích požadavků

OpenCensus Python koreluje hlavičky W3C Trace-Context z příchozích požadavků do rozsahů, které jsou generovány z požadavků samotných. OpenCensus to provede automaticky s integrací pro tyto oblíbené webové aplikace: baňky, Django a jehlany. Stačí, když naplníte hlavičky Trace-Context W3C [správným formátem](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format) a odešlete je do žádosti. Tady je ukázková aplikace, která demonstruje toto:

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

Tento kód spustí ukázkovou aplikaci na vašem místním počítači, která naslouchá na portu `8080` . Pro korelaci kontextu trasování odešlete požadavek na koncový bod. V tomto příkladu můžete použít `curl` příkaz:
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

`id`Pole je ve formátu `<trace-id>.<span-id>` , kde `trace-id` je získán z hlavičky trasování, která byla předána v požadavku a `span-id` je vygenerováno pole s osmi bajty pro tento rozsah.

`operation_ParentId`Pole je ve formátu `<trace-id>.<parent-id>` , kde `trace-id` a, `parent-id` z hlavičky trasování, která byla předána v žádosti.

### <a name="log-correlation"></a>Korelace protokolů

OpenCensus Python umožňuje korelovat protokoly přidáním ID trasování, identifikátoru ID a příznaku vzorkování pro zaznamenávání záznamů. Tyto atributy přidáte tak, že nainstalujete [integraci protokolování](https://pypi.org/project/opencensus-ext-logging/)OpenCensus. Následující atributy budou přidány do `LogRecord` objektů Python: `traceId` , `spanId` a `traceSampled` . Všimněte si, že se to projeví jenom u protokolovacích nástrojů, které se vytvoří po integraci.

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
Všimněte si, že je k `spanId` dispozici pro zprávu protokolu v rámci rozsahu. To je totéž `spanId` , co patří do rozpětí s názvem `hello` .

Data protokolu můžete exportovat pomocí `AzureLogHandler` . Další informace najdete v [tomto článku](./opencensus-python.md#logs).

Pro správnou korelaci můžeme také předat informace o trasování z jedné součásti do jiné. Představte si například scénář, ve kterém jsou dvě komponenty `module1` a `module2` . Module1 volá funkce v Module2 a získá protokoly z `module1` a `module2` v jediném trasování můžeme použít následující postup:

```python
# module1.py
import logging

from opencensus.trace import config_integration
from opencensus.trace.samplers import AlwaysOnSampler
from opencensus.trace.tracer import Tracer
from module2 import function_1

config_integration.trace_integrations(['logging'])
logging.basicConfig(format='%(asctime)s traceId=%(traceId)s spanId=%(spanId)s %(message)s')
tracer = Tracer(sampler=AlwaysOnSampler())

logger = logging.getLogger(__name__)
logger.warning('Before the span')
with tracer.span(name='hello'):
   logger.warning('In the span')
   function_1(tracer)
logger.warning('After the span')


# module2.py

import logging

from opencensus.trace import config_integration
from opencensus.trace.samplers import AlwaysOnSampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['logging'])
logging.basicConfig(format='%(asctime)s traceId=%(traceId)s spanId=%(spanId)s %(message)s')
tracer = Tracer(sampler=AlwaysOnSampler())

def function_1(parent_tracer=None):
    if parent_tracer is not None:
        tracer = Tracer(
                    span_context=parent_tracer.span_context,
                    sampler=AlwaysOnSampler(),
                )
    else:
        tracer = Tracer(sampler=AlwaysOnSampler())

    with tracer.span("function_1"):
        logger.info("In function_1")
```

## <a name="telemetry-correlation-in-net"></a>Korelace telemetrie v .NET

.NET runtime podporuje distribuované s podporou [aktivity](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) a [DiagnosticSource](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md)

Sada Application Insights .NET SDK používá `DiagnosticSource` a `Activity` ke shromažďování a korelaci telemetrie.

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-java"></a>Korelace telemetrie v jazyce Java

[Agent Java](./java-in-process-agent.md) a [Java SDK](../../azure-monitor/app/java-get-started.md) verze 2.0.0 nebo novější podporují automatickou korelaci telemetrie. Automaticky se naplní `operation_id` pro veškerou telemetrii (jako jsou trasování, výjimky a vlastní události) vydaná v rámci požadavku. Také šíří hlavičky korelace (popsané dříve) pro volání služby-služba prostřednictvím protokolu HTTP, pokud je nakonfigurován [Agent Java SDK](../../azure-monitor/app/java-agent.md) .

> [!NOTE]
> Agent Application Insights Java automaticky shromažďuje požadavky a závislosti pro JMS, Kafka, síťovinu/webtoků a další. V případě sady Java SDK jsou pro funkci korelace podporovány pouze volání prostřednictvím Apache HttpClient. Automatické šíření kontextu napříč technologiemi zasílání zpráv (například Kafka, RabbitMQ a Azure Service Bus) není v sadě SDK podporováno. 

> [!NOTE]
> Pro shromáždění vlastní telemetrie, kterou potřebujete k instrumentaci aplikace pomocí sady Java 2,6 SDK. 

### <a name="role-names"></a>Názvy rolí

Možná budete chtít přizpůsobit způsob, jakým se názvy komponent zobrazují v [mapě aplikace](../../azure-monitor/app/app-map.md). K tomu můžete ručně nastavit `cloud_RoleName` pomocí jedné z následujících akcí:

- V případě Application Insightsho agenta Java 3,0 nastavte název cloudové role následujícím způsobem:

    ```json
    {
      "role": {
        "name": "my cloud role name"
      }
    }
    ```
    Název cloudové role můžete také nastavit pomocí proměnné prostředí `APPLICATIONINSIGHTS_ROLE_NAME` .

- S Application Insights Java SDK 2.5.0 a novější můžete zadat `cloud_RoleName` přidáním `<RoleName>` do ApplicationInsights.xml souboru:

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

  Jaře Boot Starter automaticky přiřadí `cloudRoleName` hodnotu, kterou zadáte pro `spring.application.name` vlastnost.

## <a name="next-steps"></a>Další kroky

- Zápis [vlastní telemetrie](../../azure-monitor/app/api-custom-events-metrics.md).
- Pokročilé scénáře korelace v ASP.NET Core a ASP.NET najdete v tématu [sledování vlastních operací](custom-operations-tracking.md).
- Přečtěte si další informace o [nastavení cloud_RoleName](./app-map.md#set-or-override-cloud-role-name) pro jiné sady SDK.
- Připojte všechny komponenty mikroslužby na Application Insights. Podívejte se na [podporované platformy](./platforms.md).
- Seznamte se s [datovým modelem](./data-model.md) pro Application Insights typy.
- Naučte se, jak můžete [zvětšit a filtrovat telemetrii](./api-filtering-sampling.md).
- Přečtěte si [referenční informace o konfiguraci Application Insights](configuration-with-applicationinsights-config.md).