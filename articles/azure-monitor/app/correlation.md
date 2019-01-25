---
title: Korelace telemetrie Azure Application Insights | Dokumentace Microsoftu
description: Korelace telemetrie Application Insights
services: application-insights
documentationcenter: .net
author: lgayhardt
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/10/2019
ms.reviewer: sergkanz
ms.author: lagayhar
ms.openlocfilehash: 065319987b6ec8d1dd5cbbb10198e5a4e470543e
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54886816"
---
# <a name="telemetry-correlation-in-application-insights"></a>Korelace telemetrie v Application Insights

V řadě mikroslužeb vyžaduje každé logické operace práce v různých součástí služby. Každá z těchto komponent se dá sledovat samostatně pomocí [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). Součást webové aplikace komunikuje s součást zprostředkovatele ověřování k ověření přihlašovacích údajů uživatele a s komponentou rozhraní API k získání dat pro vizualizaci. Komponentu rozhraní API můžete dotazovat data z jiných služeb a používat komponenty poskytovatele mezipaměti upozornění fakturace komponenty o tomto volání. Application Insights podporuje distribuované telemetrie korelace, které můžete použít k detekci jaká součást je zodpovědná za selhání nebo snížení výkonu.

Tento článek vysvětluje používané službou Application Insights k harmonizaci telemetrická data odesílaná více komponent datového modelu. Zabývá se kontext šíření techniky a protokoly. Věnuje se také provádění koncepty korelace v různých jazycích a platformách.

## <a name="data-model-for-telemetry-correlation"></a>Datový model pro korelace telemetrie

Definuje Application Insights [datový model](../../azure-monitor/app/data-model.md) pro korelaci distribuované telemetrie. Pokud chcete přidružit telemetrie logické operace, každé položce telemetrie má kontext pole s názvem `operation_Id`. Tento identifikátor je sdílet každé položce telemetrie v distribuované trasování. I přes ztrátu telemetrie z jedné vrstvy, můžete tedy stále přidružit hlášené součástmi další telemetrická data.

Distribuované logické operace se obvykle skládá z sadu menších operace, které jsou žádostí zpracovaných rozhraním jednu součást. Tyto operace jsou definovány [telemetrie žádostí](../../azure-monitor/app/data-model-request-telemetry.md). Každý požadavek telemetrie má svůj vlastní `id` , který identifikuje jedinečné a globálně. A všech položkách telemetrie (například trasování a výjimky), které jsou spojené s touto žádostí, nastavte `operation_parentId` hodnotě požadavku `id`.

Všechny odchozí operace, jako je například HTTP volání pro jiné komponenty, je reprezentována [telemetrických závislostí](../../azure-monitor/app/data-model-dependency-telemetry.md). Telemetrie závislostí také definuje vlastní `id` , který je globálně jedinečný. Žádost o telemetrii, inicializuje toto volání závislosti používá toto `id` jako jeho `operation_parentId`.

Zobrazení distribuovaných logické operace můžete vytvářet pomocí `operation_Id`, `operation_parentId`, a `request.id` s `dependency.id`. Tato pole také definovat pořadí příčinnou souvislost volání telemetrická data.

V prostředí mikroslužeb trasování z komponenty přejít na položky jiného úložiště. Každá komponenta může mít svůj vlastní Instrumentační klíč ve službě Application Insights. K získání telemetrických informací o logické operace, musíte zobrazit dotaz data z každé položce úložiště. Po velký počet položek úložiště, které budete potřebovat nápovědu o tom, kde hledat další. Datový model Application Insights definuje dvě pole pro vyřešení tohoto problému: `request.source` a `dependency.target`. První pole určuje komponentu, závislost žádost iniciovala a druhý určuje, jaká součást vrátil odpověď volání závislostí.

## <a name="example"></a>Příklad:

Vezměme si příklad aplikace volá ceny akcie, který zobrazuje aktuální trhu ceny akcie pomocí externího rozhraní API volá `Stock`. Ceny akcie aplikace obsahuje stránku s názvem `Stock page` , které se otevře webový prohlížeč klienta s použitím `GET /Home/Stock`. Použití dotazů `Stock` rozhraní API prostřednictvím volání protokolu HTTP `GET /api/stock/value`.

Spuštěním dotazu lze analyzovat výsledná telemetrická data:

```kusto
(requests | union dependencies | union pageViews)
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

Ve výsledcích, mějte na paměti, že všechny položky telemetrie sdílet kořenovou `operation_Id`. Při volání Ajax proběhne na stránce nové jedinečné ID (`qJSXU`) je přiřazen k telemetrii závislostí a ID zobrazení stránky se používá jako `operation_ParentId`. Požadavek serveru použije ID Ajax jako `operation_ParentId`.

| itemType   | jméno                      | ID           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| pageView   | Základní stránka                |              | STYz               | STYz         |
| závislost | / GET Home/Stock           | qJSXU        | STYz               | STYz         |
| žádost    | GET Home/Stock            | KqKwlrSt9PA= | qJSXU              | STYz         |
| závislost | ZÍSKAT /api/stock/value      | bBrf2L7mm2g = | KqKwlrSt9PA=       | STYz         |

Při volání `GET /api/stock/value` se provádí na externí služby, chcete znát identitu serveru, abyste mohli nastavit `dependency.target` pole odpovídajícím způsobem. Když nepodporuje externí služby monitorování, `target` je nastavena na název hostitele služby (například `stock-prices-api.com`). Nicméně, pokud služba identifikuje vrácením předdefinované záhlaví HTTP `target` obsahuje identitu služby, která umožňuje vytvářet distribuované trasování pomocí dotazu na telemetrická data ze služby Application Insights.

## <a name="correlation-headers"></a>Korelace záhlaví

Pracujeme na návrh RFC [korelační protokol HTTP](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md). Tento návrh definuje dvě záhlaví:

- `Request-Id`: Představuje globálně jedinečné ID volání.
- `Correlation-Context`: Představuje kolekci dvojic název hodnota vlastnosti distribuované trasování.

Standardní definuje také dvě schémata pro `Request-Id` generování: bez stromové struktury a hierarchické. Plochý schématu, dobře známé `Id` klíč je definován pro `Correlation-Context` kolekce.

Definuje Application Insights [rozšíření](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) pro korelaci protokolu HTTP. Používá `Request-Context` dvojice název hodnota šíření kolekci vlastností používané okamžité volající nebo volaný. Application Insights SDK používá této hlavičky k nastavení `dependency.target` a `request.source` pole.

### <a name="w3c-distributed-tracing"></a>Distribuované trasování W3C

Můžeme se převádějí na [formátu W3C distribuované trasování](https://w3c.github.io/trace-context/). Definuje:

- `traceparent`: Provede operaci globálně jedinečné ID a jedinečný identifikátor hovoru.
- `tracestate`: Trasování kontextu konkrétní systém má u sebe.

#### <a name="enable-w3c-distributed-tracing-support-for-classic-aspnet-apps"></a>Povolit podporu W3C distribuované trasování pro klasické aplikace v ASP.NET

Tato funkce je dostupná v `Microsoft.ApplicationInsights.Web` a `Microsoft.ApplicationInsights.DependencyCollector` počínaje 2.8.0-beta1 verze balíčků.
Ve výchozím nastavení je zakázána. Chcete-li ji povolit, změňte `ApplicationInsights.config`:

- V části `RequestTrackingTelemetryModule`, přidejte `EnableW3CHeadersExtraction` element s hodnotou nastavenou `true`.
- V části `DependencyTrackingTelemetryModule`, přidejte `EnableW3CHeadersInjection` element s hodnotou nastavenou `true`.

#### <a name="enable-w3c-distributed-tracing-support-for-aspnet-core-apps"></a>Povolit podporu W3C distribuované trasování pro aplikace ASP.NET Core

Tato funkce je ve `Microsoft.ApplicationInsights.AspNetCore` 2.5.0-beta1 verzi a v `Microsoft.ApplicationInsights.DependencyCollector` 2.8.0-beta1 verze.
Ve výchozím nastavení je zakázána. Chcete-li ji povolit, nastavte `ApplicationInsightsServiceOptions.RequestCollectionOptions.EnableW3CDistributedTracing` k `true`:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry(o => 
        o.RequestCollectionOptions.EnableW3CDistributedTracing = true );
    // ....
}
```

#### <a name="enable-w3c-distributed-tracing-support-for-java-apps"></a>Povolit podporu W3C distribuované trasování pro aplikace v Javě

- **Konfigurace příchozí**

  - Pro aplikace J2EE, přidejte následující text do `<TelemetryModules>` značky uvnitř ApplicationInsights.xml:

    ```xml
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule>
       <Param name = "W3CEnabled" value ="true"/>
       <Param name ="enableW3CBackCompat" value = "true" />
    </Add>
    ```
  - Pro aplikace Spring Boot přidejte následující vlastnosti:

    - `azure.application-insights.web.enable-W3C=true`
    - `azure.application-insights.web.enable-W3C-backcompat-mode=true`

- **Odchozí konfigurace**

  Přidejte následující AI – Agent.xml:

  ```xml
  <Instrumentation>
    <BuiltIn enabled="true">
      <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
    </BuiltIn>
  </Instrumentation>
  ```

  > [!NOTE]
  > Ve výchozím nastavení, je povolen režim zpětná kompatibilita a `enableW3CBackCompat` parametr je nepovinný. Používejte pouze v případě, že chcete vypnout zpětné kompatibility.
  >
  > V ideálním případě by vypnete to při všechny vaše služby byly aktualizovány na novější verzi sady SDK, které podporují protokol W3C. Důrazně doporučujeme, že přejdete na tyto novější sady SDK co nejdříve.

> [!IMPORTANT]
> Ujistěte se, že příchozí a odchozí konfigurace jsou stejné.

## <a name="opentracing-and-application-insights"></a>OpenTracing a Application Insights

[OpenTracing datového modelu specifikace](https://opentracing.io/) a Application Insights datové modely mapování následujícím způsobem:

| Application Insights                  | OpenTracing                                       |
|------------------------------------   |-------------------------------------------------  |
| `Request`, `PageView`                 | `Span` s `span.kind = server`                  |
| `Dependency`                          | `Span` s `span.kind = client`                  |
| `Id` z `Request` a `Dependency`    | `SpanId`                                          |
| `Operation_Id`                        | `TraceId`                                         |
| `Operation_ParentId`                  | `Reference` typ `ChildOf` (nadřazený rozsah)   |

Další informace najdete v tématu [modelu dat telemetrie Application Insights](../../azure-monitor/app/data-model.md). 

Definice OpenTracing koncepty, najdete v článku OpenTracing [specifikace](https://github.com/opentracing/specification/blob/master/specification.md) a [semantic_conventions](https://github.com/opentracing/specification/blob/master/semantic_conventions.md).

## <a name="telemetry-correlation-in-net"></a>Korelace telemetrie v .NET

V průběhu času .NET definovány několik způsobů, jak korelovat telemetrie a Diagnostika protokolů:

- `System.Diagnostics.CorrelationManager` umožňuje sledování [LogicalOperationStack a ID aktivity](https://msdn.microsoft.com/library/system.diagnostics.correlationmanager.aspx). 
- `System.Diagnostics.Tracing.EventSource` a definovat trasování událostí pro Windows (ETW) [SetCurrentThreadActivityId](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid.aspx) metody.
- `ILogger` používá [protokolu obory](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes). 
- Windows Communication Foundation (WCF) a protokolu HTTP při přenosu nahoru "aktuální" šíření kontextu.

Tyto metody však nepovolili automatické distribuované trasování podpory. `DiagnosticSource` je způsob, jak podporovat automatické korelace mezi počítači. Knihovny pro .NET podporuje "DiagnosticSource" a povolit automatické šíření mezi počítači kontextu korelace prostřednictvím přenosu, jako je například HTTP.

[Průvodce aktivity](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) v `DiagnosticSource` vysvětluje základy tohoto sledování aktivit.

ASP.NET Core 2.0 podporuje extrakce hlavičky protokolu HTTP a spouští se nová aktivita.

`System.Net.HttpClient`, od verze 4.1.0, podporuje automatické vkládání záhlaví korelace HTTP a sledování, volání HTTP jako aktivita.

Je nový modul HTTP [Microsoft.AspNet.TelemetryCorrelation](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/), klasické technologii ASP.NET. Tento modul implementuje korelace telemetrie pomocí `DiagnosticSource`. Spustí aktivitu podle příchozí hlavičky žádosti. Také koreluje telemetrická data z různých fází zpracování požadavku, dokonce i pro případech, kdy jednotlivé fáze zpracování v Internetové informační služby (IIS) běží na jiném vlákně spravované.

Používá sadu Application Insights SDK, počínaje verzí 2.4.0-beta1 `DiagnosticSource` a `Activity` shromažďovat telemetrii a přidružte jej k aktuální aktivitu.

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-the-java-sdk"></a>Korelace telemetrie v sadě Java SDK

[Application Insights SDK pro Javu](../../azure-monitor/app/java-get-started.md) podporuje automatické korelace telemetrie od verze 2.0.0. Automaticky naplní `operation_id` všechny telemetrie (například trasování, výjimky a vlastní události), vydáno v rámci oboru požadavku. Je také se postará o šíření korelace hlavičky (popsanou výš) pro volání služba služba pomocí protokolu HTTP, pokud [agenta Java SDK](../../azure-monitor/app/java-agent.md) je nakonfigurovaný.

> [!NOTE]
> Pro funkci korelace se podporují pouze volání provedená prostřednictvím Apache HTTPClient. Pokud používáte Spring RestTemplate nebo Feign, jak jde použít s Apache HTTPClient pod pokličkou.

V současné době se nepodporuje automatické kontextu šíření přes zasílání zpráv technologie (takové Kafka, RabbitMQ nebo Azure Service Bus). Je však možné kódu scénářů ručně pomocí `trackDependency` a `trackRequest` rozhraní API. V těchto rozhraní API telemetrie závislostí představuje zprávu se zařazených do fronty podle výrobce a požadavek představuje zprávu zpracovává příjemce. V takovém případě obě `operation_id` a `operation_parentId` by mělo být předáno ve vlastnostech zprávy.

<a name="java-role-name"></a>
## <a name="role-name"></a>Název role

V některých případech může být vhodné přizpůsobit způsob názvů součástí se zobrazuje v [Mapa aplikace](../../azure-monitor/app/app-map.md). Uděláte to tak, můžete ručně nastavit `cloud_RoleName` pomocí jedné z následujících akcí:

- Pokud používáte Spring Boot s Application Insights Spring Boot starter, je pouze požadovaná změna nastavit váš vlastní název aplikace v souboru application.properties souboru.

  `spring.application.name=<name-of-app>`

  Spring Boot starter automaticky přiřadí `cloudRoleName` na hodnotu, kterou zadáte pro `spring.application.name` vlastnost.

- Pokud používáte `WebRequestTrackingFilter`, `WebAppNameContextInitializer` automaticky nastaví název aplikace. Přidejte následující konfigurační soubor (soubor ApplicationInsights.xml):

  ```XML
  <ContextInitializers>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebAppNameContextInitializer" />
  </ContextInitializers>
  ```

- Při použití třídy kontextu cloud:

  ```Java
  telemetryClient.getContext().getCloud().setRole("My Component Name");
  ```

## <a name="next-steps"></a>Další postup

- Zápis [vlastní telemetrii](../../azure-monitor/app/api-custom-events-metrics.md).
- Další informace o [nastavení cloud_RoleName](../../azure-monitor/app/app-map.md#set-cloudrolename) pro dalších sad SDK.
- Připojení všech součástí vašeho mikroslužby na Application Insights. Podívejte se [podporované platformy](../../azure-monitor/app/platforms.md).
- Zobrazit [datový model](../../azure-monitor/app/data-model.md) pro typy Application Insights.
- Zjistěte, jak [rozšířit a filtrování telemetrie](../../azure-monitor/app/api-filtering-sampling.md).
- Zkontrolujte [odkaz Konfigurace Application Insights](configuration-with-applicationinsights-config.md).
