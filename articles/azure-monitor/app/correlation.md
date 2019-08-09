---
title: Korelace telemetrie Azure Application Insights | Microsoft Docs
description: Korelace telemetrie Application Insights
services: application-insights
documentationcenter: .net
author: lgayhardt
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/07/2019
ms.reviewer: sergkanz
ms.author: lagayhar
ms.openlocfilehash: 1c6a0ce3e4e8d098d2bc048a331b0ae0cb5c6b13
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881394"
---
# <a name="telemetry-correlation-in-application-insights"></a>Korelace telemetrie v Application Insights

V celém světě mikroslužeb vyžaduje každá Logická operace provedení práce v různých součástech služby. Každou z těchto součástí je možné monitorovat samostatně pomocí [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). Komponenta webové aplikace komunikuje s komponentou poskytovatele ověřování a ověří přihlašovací údaje uživatele a pomocí komponenty rozhraní API načte data pro vizualizaci. Komponenta rozhraní API se může dotazovat na data z jiných služeb a používat komponenty zprostředkovatele mezipaměti k oznamování fakturační komponenty s tímto voláním. Application Insights podporuje korelaci distribuované telemetrie, kterou použijete k detekci, která komponenta zodpovídá za selhání nebo snížení výkonu.

Tento článek vysvětluje datový model používaný Application Insights ke korelaci telemetrie odesílaného více komponentami. Zahrnuje postupy a protokoly šíření kontextu. Zahrnuje také implementaci principů korelace v různých jazycích a platformách.

## <a name="data-model-for-telemetry-correlation"></a>Datový model pro korelaci telemetrie

Application Insights definuje [datový model](../../azure-monitor/app/data-model.md) pro korelaci distribuovaných telemetrie. Pro přidružení telemetrie k logické operaci má každá položka telemetrie kontextové pole s názvem `operation_Id`. Tento identifikátor je sdílen všemi položkami telemetrie v distribuovaném trasování. Takže i v případě ztráty telemetrie z jedné vrstvy můžete stále přidružit telemetrii nahlášenou jinými komponentami.

Distribuovaná Logická operace se typicky skládá ze sady menších operací, které jsou požadavky zpracovávané jednou z komponent. Tyto operace jsou definovány [telemetrie požadavků](../../azure-monitor/app/data-model-request-telemetry.md). Každá telemetrie požadavků má svou vlastní `id` identifikaci jedinečnou a globálně. A všechny položky telemetrie (například trasování a výjimky), které jsou přidruženy k této žádosti, by `operation_parentId` měly být nastaveny na hodnotu požadavku `id`.

Každá odchozí operace, jako je volání HTTP jiné součásti, je reprezentována [telemetrie závislosti](../../azure-monitor/app/data-model-dependency-telemetry.md). Telemetrie závislostí definuje také vlastní `id` globálně jedinečný. Požadavek telemetrie, iniciované tímto voláním závislosti, používá `id` jako svůj `operation_parentId`.

Můžete sestavit zobrazení distribuované logické operace `operation_Id`pomocí, `operation_parentId` `request.id` a s `dependency.id`. Tato pole také definují pořadí volání telemetrie.

V prostředí mikroslužeb můžou trasování z komponent přejít na jiné položky úložiště. Každá součást může mít vlastní klíč instrumentace v Application Insights. Pro získání telemetrie pro logickou operaci Application Insights UX dotazuje data z každé položky úložiště. Pokud je počet položek úložiště obrovský, budete potřebovat nápovědu, kde můžete hledat další. Datový model Application Insights definuje dvě pole pro vyřešení tohoto problému: `request.source` a. `dependency.target` První pole identifikuje komponentu, která iniciovala požadavek závislosti, a druhý identifikuje, která komponenta vrátila odpověď na volání závislostí.

## <a name="example"></a>Příklad

Podíváme se na příklad aplikace s názvem ceny akcií, která zobrazuje aktuální cenu na trhu na populaci pomocí externího rozhraní API s názvem `Stock`. Aplikace burzovních cen má stránku s názvem `Stock page` , kterou klientský webový prohlížeč otevře pomocí nástroje `GET /Home/Stock`. Aplikace se dotazuje `Stock` rozhraní API pomocí volání `GET /api/stock/value`http.

Výslednou telemetrii můžete analyzovat spuštěním dotazu:

```kusto
(requests | union dependencies | union pageViews)
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

Ve výsledcích si všimněte, že všechny položky telemetrie sdílejí kořen `operation_Id`. Když je ze stránky provedeno volání AJAX, je k telemetrie závislostí přiřazeno`qJSXU`nové jedinečné ID () a ID pageView se používá jako. `operation_ParentId` Požadavek serveru pak použije ID AJAX jako `operation_ParentId`.

| itemType   | name                      | id           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| pageView   | Skladová stránka                |              | STYz               | STYz         |
| závislost | ZÍSKAT/Home/Stock           | qJSXU        | STYz               | STYz         |
| request    | ZÍSKAT domácí/burzovní            | KqKwlrSt9PA= | qJSXU              | STYz         |
| závislost | ZÍSKAT/API/Stock/Value      | bBrf2L7mm2g = | KqKwlrSt9PA=       | STYz         |

Při volání `GET /api/stock/value` externí služby potřebujete znát identitu tohoto serveru, abyste mohli odpovídajícím způsobem `dependency.target` nastavit pole. Pokud externí služba nepodporuje monitorování, `target` je nastavena na název hostitele služby ( `stock-prices-api.com`například). Pokud však služba identifikuje sebe sama vrácením předdefinované hlavičky HTTP, `target` obsahuje identitu služby, která umožňuje Application Insights sestavit distribuované trasování pomocí dotazování telemetrie z této služby.

## <a name="correlation-headers"></a>Hlavičky korelace

Pracujeme na návrhu RFC pro [korelační protokol HTTP](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md). Tento návrh definuje dvě hlavičky:

- `Request-Id`: Provede globálně jedinečné ID volání.
- `Correlation-Context`: Přenese kolekce dvojic název-hodnota vlastností distribuované vektorizace.

Standard také definuje dvě schémata pro `Request-Id` generování: ploché a hierarchické. U plochého schématu `Id` je `Correlation-Context` pro kolekci definován známý klíč.

Application Insights definuje [rozšíření](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) protokolu HTTP korelace. Používá `Request-Context` páry název-hodnota ke šíření kolekce vlastností používaných přímým volajícím nebo volaným. Sada Application Insights SDK používá tuto hlavičku k nastavení `dependency.target` a `request.source` polí.

### <a name="w3c-distributed-tracing"></a>Distribuované trasování W3C

Převádíme na [Formát distribuované vektorizace W3C](https://w3c.github.io/trace-context/). Definuje:

- `traceparent`: Provede globálně jedinečné ID operace a jedinečný identifikátor volání.
- `tracestate`: Přináší trasování kontextu specifického pro systém.

#### <a name="enable-w3c-distributed-tracing-support-for-classic-aspnet-apps"></a>Povolení podpory distribuovaného trasování W3C pro klasické aplikace ASP.NET

Tato funkce je k dispozici `Microsoft.ApplicationInsights.DependencyCollector` v `Microsoft.ApplicationInsights.Web` a balíčky počínaje verzí 2.8.0-Beta1.
Ve výchozím nastavení je zakázaný. Pokud ho chcete povolit, `ApplicationInsights.config`změňte:

- V `RequestTrackingTelemetryModule`části `EnableW3CHeadersExtraction` přidejte element s hodnotou nastavenou na `true`.
- V `DependencyTrackingTelemetryModule`části `EnableW3CHeadersInjection` přidejte element s hodnotou nastavenou na `true`.
- `W3COperationCorrelationTelemetryInitializer` Přidat`TelemetryInitializers` pod podobný 

```xml
<TelemetryInitializers>
  <Add Type="Microsoft.ApplicationInsights.Extensibility.W3C.W3COperationCorrelationTelemetryInitializer, Microsoft.ApplicationInsights"/>
   ...
</TelemetryInitializers> 
```

#### <a name="enable-w3c-distributed-tracing-support-for-aspnet-core-apps"></a>Povolit podporu distribuovaného trasování W3C pro aplikace ASP.NET Core

Tato funkce je ve `Microsoft.ApplicationInsights.AspNetCore` verzi 2.5.0-Beta1 a ve `Microsoft.ApplicationInsights.DependencyCollector` verzi 2.8.0-Beta1.
Ve výchozím nastavení je zakázaný. Pokud ho chcete povolit, `ApplicationInsightsServiceOptions.RequestCollectionOptions.EnableW3CDistributedTracing` nastavte `true`na:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry(o => 
        o.RequestCollectionOptions.EnableW3CDistributedTracing = true );
    // ....
}
```

#### <a name="enable-w3c-distributed-tracing-support-for-java-apps"></a>Povolit podporu distribuovaného trasování W3C pro aplikace Java

- **Příchozí konfigurace**

  - Pro aplikace v jazyce Java EE přidejte do `<TelemetryModules>` tagu ApplicationInsights. XML následující značky:

    ```xml
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule>
       <Param name = "W3CEnabled" value ="true"/>
       <Param name ="enableW3CBackCompat" value = "true" />
    </Add>
    ```
  - V případě aplikací pro spouštění pružiny přidejte následující vlastnosti:

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
  > Režim zpětné kompatibility je ve výchozím nastavení povolený a `enableW3CBackCompat` parametr je nepovinný. Používejte ji pouze v případě, že chcete vypnout zpětnou kompatibilitu.
  >
  > V ideálním případě byste tuto funkci vypnuli, až budou všechny vaše služby aktualizované na novější verze sad SDK, které podporují protokol W3C. Důrazně doporučujeme co nejdříve přesunout na tyto novější sady SDK.

> [!IMPORTANT]
> Zajistěte, aby příchozí i odchozí konfigurace byly přesně stejné.

#### <a name="enable-w3c-distributed-tracing-support-for-web-apps"></a>Povolit podporu distribuovaného trasování W3C pro webové aplikace

Tato funkce je v `Microsoft.ApplicationInsights.JavaScript`systému. Ve výchozím nastavení je zakázaný. Pokud ho chcete povolit, `distributedTracingMode` použijte config. AI_AND_W3C je k dispozici pro zajištění zpětné kompatibility se staršími Application Insights instrumentované služby:

- **Nastavení NPM (ignorovat při použití nastavení fragmentu)**

  ```javascript
  import { ApplicationInsights, DistributedTracingModes } from '@microsoft/applicationinsights-web';

  const appInsights = new ApplicationInsights({ config: {
    instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
    distributedTracingMode: DistributedTracingModes.W3C
    /* ...Other Configuration Options... */
  } });
  appInsights.loadAppInsights();
  ```
  
- **Nastavení fragmentu (ignorovat při použití instalačního programu NPM)**

  ```
  <script type="text/javascript">
  var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){i[e]=function(){var n=arguments;i.queue.push(function(){i[e].apply(i,n)})}}var i={config:e};i.initialize=!0;var a=document,t=window;setTimeout(function(){var n=a.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",a.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{i.cookie=a.cookie}catch(e){}i.queue=[],i.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var o="Track"+r[0];if(n("start"+o),n("stop"+o),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var s=t[r];t[r]=function(e,n,a,t,o){var c=s&&s(e,n,a,t,o);return!0!==c&&i["_"+r]({message:e,url:n,lineNumber:a,columnNumber:t,error:o}),c},e.autoExceptionInstrumented=!0}return i}
  (
    {
      instrumentationKey:"INSTRUMENTATION_KEY",
      distributedTracingMode: 2 // DistributedTracingModes.W3C
      /* ...Other Configuration Options... */
    }
  );
  window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
  </script>
  ```

## <a name="opentracing-and-application-insights"></a>OpenTracing a Application Insights

Datové modely [OpenTracing a specifikace datového modelu](https://opentracing.io/) pro Application Insights mapují následujícím způsobem:

| Application Insights                  | OpenTracing                                       |
|------------------------------------   |-------------------------------------------------  |
| `Request`, `PageView`                 | `Span`řetězce`span.kind = server`                  |
| `Dependency`                          | `Span`řetězce`span.kind = client`                  |
| `Id`z `Request` a`Dependency`    | `SpanId`                                          |
| `Operation_Id`                        | `TraceId`                                         |
| `Operation_ParentId`                  | `Reference`typu `ChildOf` (nadřazený rozsah)   |

Další informace najdete v tématu [Application Insights datovém modelu telemetrie](../../azure-monitor/app/data-model.md). 

Definice konceptů OpenTracing najdete v článku [specifikace](https://github.com/opentracing/specification/blob/master/specification.md) OpenTracing a [semantic_conventions](https://github.com/opentracing/specification/blob/master/semantic_conventions.md).

## <a name="telemetry-correlation-in-net"></a>Korelace telemetrie v .NET

V průběhu času .NET definovalo několik způsobů, jak sladit protokoly telemetrie a diagnostiky:

- `System.Diagnostics.CorrelationManager`umožňuje sledování [LogicalOperationStack a ActivityId](https://msdn.microsoft.com/library/system.diagnostics.correlationmanager.aspx). 
- `System.Diagnostics.Tracing.EventSource`a trasování událostí pro Windows (ETW) definuje metodu [SetCurrentThreadActivityId](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid.aspx) .
- `ILogger`používá [rozsahy protokolů](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes). 
- Windows Communication Foundation (WCF) a kabel HTTP nahoru "aktuální" šíření kontextu.

Tyto metody ale nepovolily automatickou podporu distribuovaného trasování. `DiagnosticSource`je způsob, jak podporovat automatickou korelaci mezi počítači. Knihovny .NET podporují DiagnosticSource a umožňují automatické šíření mezipočítačového kontextu korelace prostřednictvím přenosu, jako je například HTTP.

[Příručka k činnostem](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) v `DiagnosticSource` tématu vysvětluje základy sledování aktivit.

ASP.NET Core 2,0 podporuje extrakci hlaviček protokolu HTTP a spuštění nové aktivity.

`System.Net.HttpClient`Počínaje verzí 4.1.0 podporuje automatické vkládání hlaviček protokolu HTTP korelace a sledování volání HTTP jako aktivity.

Pro klasický ASP.NET je k dispozici nový modul HTTP, [Microsoft. ASPNET. TelemetryCorrelation](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/). Tento modul implementuje korelaci telemetrie pomocí `DiagnosticSource`. Spustí aktivitu na základě hlaviček příchozích požadavků. Také koreluje telemetrie z různých fází zpracování požadavků, a to i v případě, že se každá fáze zpracování Internetová informační služba (IIS) spouští v jiném spravovaném vlákně.

Sada Application Insights SDK, počínaje verzí 2.4.0-Beta1, používá `DiagnosticSource` a `Activity` ke shromáždění telemetrie a jejím přidružení k aktuální aktivitě.

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-the-java-sdk"></a>Korelace telemetrie v sadě Java SDK

[Sada Application Insights SDK pro jazyk Java](../../azure-monitor/app/java-get-started.md) podporuje automatickou korelaci telemetrie počínaje verzí 2.0.0. Automaticky se naplní `operation_id` pro veškerou telemetrii (například trasování, výjimky a vlastní události) vydaná v rámci rozsahu žádosti. I když je [Agent Java SDK](../../azure-monitor/app/java-agent.md) nakonfigurovaný, postará se také o šíření hlaviček korelace (popsaných dříve) pro volání typu služba-služba prostřednictvím protokolu HTTP.

> [!NOTE]
> Pro funkci Correlation se podporují jenom volání prostřednictvím Apache HTTPClient. Pokud používáte jarní RestTemplate nebo Feign, můžete je v digestoři použít s Apache HTTPClient.

V současné době není podporováno automatické šíření kontextu napříč technologiemi zasílání zpráv (například Kafka, RabbitMQ nebo Azure Service Bus). Takové scénáře je ale možné kódovat ručně pomocí `trackDependency` rozhraní API a. `trackRequest` V těchto rozhraních API představuje telemetrie závislost zpráva, kterou zařazování provádí producent, a požadavek představuje zprávu, kterou zpracovává příjemce. V takovém případě by `operation_id` měly `operation_parentId` být obě i šířeny ve vlastnostech zprávy.

### <a name="telemetry-correlation-in-asynchronous-java-application"></a>Korelace telemetrie v asynchronní aplikaci Java

Pro korelaci telemetrie v aplikaci asynchronního spouštění pomocí pružiny použijte [Tento](https://github.com/Microsoft/ApplicationInsights-Java/wiki/Distributed-Tracing-in-Asynchronous-Java-Applications) podrobný článek. Poskytuje pokyny k instrumentaci [ThreadPoolTaskExecutor](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskExecutor.html) pružiny a také k [ThreadPoolTaskScheduler](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskScheduler.html). 


<a name="java-role-name"></a>
## <a name="role-name"></a>Název role

V některých případech můžete chtít přizpůsobit způsob, jakým se názvy komponent zobrazují v [mapě aplikace](../../azure-monitor/app/app-map.md). Provedete to tak, že ručně nastavíte `cloud_RoleName` jednu z následujících možností:

- Pokud používáte jarní spouštění s Application Insightsm Starter Boot Starter, jediná požadovaná změna je nastavení vlastního názvu aplikace v souboru Application. Properties.

  `spring.application.name=<name-of-app>`

  Jaře Boot Starter automaticky přiřadí `cloudRoleName` hodnotu, kterou zadáte `spring.application.name` pro vlastnost.

- Pokud používáte `WebRequestTrackingFilter`, název aplikace se `WebAppNameContextInitializer` nastaví automaticky. Do konfiguračního souboru (ApplicationInsights. XML) přidejte následující:

  ```XML
  <ContextInitializers>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebAppNameContextInitializer" />
  </ContextInitializers>
  ```

- Pokud používáte třídu kontextu cloudu:

  ```Java
  telemetryClient.getContext().getCloud().setRole("My Component Name");
  ```

## <a name="next-steps"></a>Další postup

- Zápis [vlastní telemetrie](../../azure-monitor/app/api-custom-events-metrics.md).
- Přečtěte si další informace o [Nastavení cloud_RoleName](../../azure-monitor/app/app-map.md#set-cloud-role-name) pro jiné sady SDK.
- Připojte všechny komponenty mikroslužby na Application Insights. Podívejte se na [podporované platformy](../../azure-monitor/app/platforms.md).
- Seznamte [](../../azure-monitor/app/data-model.md) se s datovým modelem pro Application Insights typy.
- Naučte se, jak můžete [zvětšit a filtrovat telemetrii](../../azure-monitor/app/api-filtering-sampling.md).
- Přečtěte si [referenční informace o konfiguraci Application Insights](configuration-with-applicationinsights-config.md).
