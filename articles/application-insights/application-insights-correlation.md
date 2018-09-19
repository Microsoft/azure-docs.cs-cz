---
title: Korelace Telemetrie Azure Application Insights | Dokumentace Microsoftu
description: Korelace telemetrie Application Insights
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/09/2018
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: 696843363bc6617bb11c01cdccb9dbbb7b719a82
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46298196"
---
# <a name="telemetry-correlation-in-application-insights"></a>Korelace telemetrie v Application Insights

V řadě mikroslužby každé logické operace vyžaduje práci v různých součástí služby. Každá z těchto součástí samostatně lze pomocí [Application Insights](app-insights-overview.md). Součást webové aplikace komunikuje s součást zprostředkovatele ověřování k ověření přihlašovacích údajů uživatele a s komponentou rozhraní API k získání dat pro vizualizaci. Komponentu rozhraní API v jeho důsledku můžete dotazovat data z jiných služeb a používat komponenty zprostředkovatele mezipaměti a upozornění fakturace komponenty o tomto volání. Application Insights podporuje distribuované telemetrie korelace. Umožňuje zjistit, jaká součást je zodpovědná za selhání nebo snížení výkonu.

Tento článek vysvětluje používané službou Application Insights k harmonizaci telemetrická data odesílaná více komponent datového modelu. Zabývá se kontext šíření techniky a protokoly. Věnuje se také provádění koncepty korelace v různých jazycích a platformách.

## <a name="telemetry-correlation-data-model"></a>Datový model korelace telemetrie

Definuje Application Insights [datový model](application-insights-data-model.md) pro korelaci distribuované telemetrie. Pokud chcete přidružit telemetrie logické operace, každé položce telemetrie má kontext pole s názvem `operation_Id`. Tento identifikátor je sdílet každé položce telemetrie v distribuované trasování. Takže i přes ztrátu telemetrie z jedné vrstvy stále můžete přidružit hlášené součástmi další telemetrická data.

Distribuované logické operace se obvykle skládá z sadu menších operací - žádostí zpracovaných rozhraním jednu součást. Tyto operace jsou definovány [telemetrie žádostí](application-insights-data-model-request-telemetry.md). Každý požadavek telemetrie má svůj vlastní `id` , který globálně jedinečným způsobem identifikuje. A veškerá telemetrie – trasování, výjimky, atd. s touto žádostí, nastavte `operation_parentId` hodnotě požadavku `id`.

Všechny odchozí operace jako volání protokolu http na jinou komponentu reprezentována [telemetrických závislostí](application-insights-data-model-dependency-telemetry.md). Telemetrie závislostí také definuje vlastní `id` , který je globálně jedinečný. Telemetrie žádostí iniciovaných toto volání závislosti, používá ho jako `operation_parentId`.

Můžete vytvořit zobrazení pomocí distribuované logické operace `operation_Id`, `operation_parentId`, a `request.id` s `dependency.id`. Tato pole také definovat pořadí příčinnou souvislost volání telemetrická data.

Trasování z komponent může v prostředí mikroslužby, přejděte do různých úložných. Každá komponenta může mít svůj vlastní Instrumentační klíč ve službě Application Insights. K získání telemetrických informací o logické operace, budete muset dotaz na data z každé úložiště. Když je velký počet úložišť, musíte mít pomocného parametru, ve kterém můžete vyhledat další.

Application Insights data model definuje dvě pole pro vyřešení tohoto problému: `request.source` a `dependency.target`. První pole určuje komponentu, závislost žádost iniciovala a druhý určuje, jaká součást vrátil odpověď volání závislostí.


## <a name="example"></a>Příklad:

Vezměme si příklad ceny AKCIE aplikace zobrazuje aktuální trhu ceny akcie pomocí externího rozhraní API volat rozhraní API AKCIE. Na stránce má aplikace ceny AKCIE `Stock page` otevřít s použitím klienta webové prohlížeče `GET /Home/Stock`. Aplikace dotazuje prostřednictvím volání protokolu HTTP rozhraní API AKCIE `GET /api/stock/value`.

Můžete analyzovat výsledná telemetrických dat spuštěním dotazu:

```
(requests | union dependencies | union pageViews) 
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

V poznámce výsledek zobrazení, že všechny položky telemetrie sdílet kořenovou `operation_Id`. Při volání ajax udělali na stránce – nové jedinečné id `qJSXU` je přiřazena k telemetrii závislostí a id pro zobrazení stránky se používá jako `operation_ParentId`. Pak požadavek na serveru používá id jazyka ajax na jako `operation_ParentId`atd.

| itemType   | jméno                      | id           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| pageView   | Základní stránka                |              | STYz               | STYz         |
| závislost | / GET Home/Stock           | qJSXU        | STYz               | STYz         |
| požadavek    | GET Home/Stock            | KqKwlrSt9PA= | qJSXU              | STYz         |
| závislost | ZÍSKAT /api/stock/value      | bBrf2L7mm2g = | KqKwlrSt9PA=       | STYz         |

Teď při volání `GET /api/stock/value` provedené externí služby chcete znát identitu serveru. Abyste mohli nastavit `dependency.target` pole odpovídajícím způsobem. Když externí služba nepodporuje sledování - `target` je nastavena na název hostitele služby jako `stock-prices-api.com`. Nicméně pokud tuto službu identifikuje vrácením i předdefinovanou hlavičky protokolu HTTP - `target` obsahuje identitu služby, která umožňuje vytvářet distribuované trasování pomocí dotazu na telemetrická data ze služby Application Insights. 

## <a name="correlation-headers"></a>Korelace záhlaví

Pracujeme na RFC návrh [korelační protokol HTTP](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v1.md). Tento návrh definuje dvě záhlaví:

- `Request-Id` provádění globálně jedinečné id volání
- `Correlation-Context` -provádění kolekce dvojic hodnot název vlastnosti distribuované trasování

Standardní definuje také dvě schémata `Request-Id` generování - bez stromové struktury a hierarchické. Plochý schématu je dobře známé `Id` definovaný pro klíč `Correlation-Context` kolekce.

Definuje Application Insights [rozšíření](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) pro korelaci protokolu HTTP. Používá `Request-Context` název dvojice šíření kolekci vlastností používané okamžité volající nebo volaný. Application Insights SDK používá této hlavičky k nastavení `dependency.target` a `request.source` pole.

### <a name="w3c-distributed-tracing"></a>W3C distribuované trasování

Přeneseme do (formátu W3C distribuované trasování) [https://w3c.github.io/distributed-tracing/report-trace-context.html]. Definuje:
- `traceparent` -provádí operace globálně jedinečné id a jedinečný identifikátor hovoru
- `tracestate` -představuje konkrétní kontext trasování systému.

#### <a name="enable-w3c-distributed-tracing-support-for-aspnet-classic-apps"></a>Povolit podporu W3C distribuované trasování pro aplikace ASP.NET Classic

Tato funkce je dostupná v balíčcích Microsoft.ApplicationInsights.Web a Microsoft.ApplicationInsights.DependencyCollector počínaje 2.8.0-beta1 verze.
Je **vypnout** ve výchozím nastavení, chcete-li ji povolit, změňte `ApplicationInsights.config`:

* v části `RequestTrackingTelemetryModule` přidat `EnableW3CHeadersExtraction` element s hodnotou nastavenou `true`
* v části `DependencyTrackingTelemetryModule` přidat `EnableW3CHeadersInjection` element s hodnotou nastavenou `true`

#### <a name="enable-w3c-distributed-tracing-support-for-aspnet-core-apps"></a>Povolit podporu W3C distribuované trasování pro aplikace ASP.NET Core

Tato funkce je ve Microsoft.ApplicationInsights.AspNetCore 2.5.0-beta1 verze a verze 2.8.0-beta1 Microsoft.ApplicationInsights.DependencyCollector.
Je **vypnout** ve výchozím nastavení, aby je `ApplicationInsightsServiceOptions.RequestCollectionOptions.EnableW3CDistributedTracing` k `true`:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry(o => 
        o.RequestCollectionOptions.EnableW3CDistributedTracing = true );
    // ....
}
```

## <a name="open-tracing-and-application-insights"></a>Otevřít trasování a Application Insights

[Otevřít trasování](http://opentracing.io/) a vyhledá modely dat Application Insights 

- `request`, `pageView` mapuje **rozpětí** s `span.kind = server`
- `dependency` mapuje **rozpětí** s `span.kind = client`
- `id` nástroje `request` a `dependency` mapuje **Span.Id**
- `operation_Id` mapuje **TraceId**
- `operation_ParentId` mapuje **odkaz** typu `ChildOf`

Zobrazit [datový model](application-insights-data-model.md) pro typy a datový model Application Insights.

Zobrazit [specifikace](https://github.com/opentracing/specification/blob/master/specification.md) a [semantic_conventions](https://github.com/opentracing/specification/blob/master/semantic_conventions.md) definice otevřít trasování koncepty.


## <a name="telemetry-correlation-in-net"></a>Korelace telemetrie v .NET

V čase .NET definovány různými způsoby pro korelaci protokolů telemetrie a Diagnostika. Je `System.Diagnostics.CorrelationManager` umožňuje sledovat [LogicalOperationStack a ID aktivity](https://msdn.microsoft.com/library/system.diagnostics.correlationmanager.aspx). `System.Diagnostics.Tracing.EventSource` a trasování událostí pro Windows Windows definovat metodu [SetCurrentThreadActivityId](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid.aspx). `ILogger` používá [protokolu obory](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes). WCF a Http při přenosu nahoru "aktuální" šíření kontextu.

Ale tyto metody nepovolili automatické distribuované trasování podpory. `DiagnosticsSource` je způsob, jak podporovat automatické křížové korelace počítače. Knihovny .NET podporuje diagnostiku zdrojů a povolit automatické různé počítače šíření kontextu korelace prostřednictvím přenosu, jako je http.

[Průvodce aktivity](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) ve zdroji diagnostiky vysvětluje základy tohoto sledování aktivit. 

ASP.NET Core 2.0 podporuje extrakce hlavičky protokolu Http a spouští se nová aktivita. 

`System.Net.HttpClient` Počáteční verze `4.1.0` podporuje automatické vkládání korelace hlavičky protokolu Http a sledování volání http jako aktivita.

Nový modul Http [Microsoft.AspNet.TelemetryCorrelation](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/) v případě klasického modelu ASP.NET. Tento modul implementuje pomocí DiagnosticsSource korelace telemetrie. Spustí aktivitu podle příchozí hlavičky žádosti. Také koreluje telemetrická data z různých fází zpracování požadavku. I pro případech, kdy jednotlivé fáze zpracování služby IIS běží na různých spravovat vlákna.

Počáteční verze Application Insights SDK `2.4.0-beta1` používá DiagnosticsSource a aktivita shromažďovat telemetrii a přidružte jej k aktuální aktivitu. 

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-the-java-sdk"></a>Korelace telemetrie v sadě Java SDK
[Application Insights Java SDK](app-insights-java-get-started.md) podporuje automatické korelace telemetrie od verze `2.0.0`. Automaticky naplní `operation_id` pro veškerá telemetrie (trasování, výjimky, vlastní události atd.), vydáno v rámci oboru požadavku. Je také se postará o šíření korelace hlavičky (popsaných výše) pro volání mezi službami přes protokol HTTP-li [agenta Java SDK](app-insights-java-agent.md) je nakonfigurované. Poznámka: pro funkci korelace jsou podporovány pouze volání provedená prostřednictvím Apache HTTP klienta. Pokud používáte Spring Rest šablony nebo Feign, jak jde použít s klient Apache HTTP pod pokličkou.

V současné době se nepodporuje automatické kontextu šíření napříč technologie zasílání zpráv (například Kafka, RabbitMQ, Azure Service Bus). Je ale možné ručně kódu s použitím scénářů `trackDependency` a `trackRequest` rozhraní API, kterým telemetrických závislostí představuje zprávu se zařazených do fronty podle výrobce a požadavek představuje zprávu zpracovává příjemce. V takovém případě obě `operation_id` a `operation_parentId` by mělo být předáno ve vlastnostech zprávy.

<a name="java-role-name"></a>
### <a name="role-name"></a>Název role
V některých případech může být vhodné přizpůsobit způsob názvů součástí se zobrazuje v [Mapa aplikace](app-insights-app-map.md). Uděláte to tak, můžete ručně nastavit `cloud_roleName` pomocí jedné z následujících akcí:

Pomocí inicializátoru telemetrie (všech položkách telemetrie jsou označené)
```Java
public class CloudRoleNameInitializer extends WebTelemetryInitializerBase {

    @Override
    protected void onInitializeTelemetry(Telemetry telemetry) {
        telemetry.getContext().getTags().put(ContextTagKeys.getKeys().getDeviceRoleName(), "My Component Name");
    }
  }
```
Prostřednictvím [třídy kontextu zařízení](https://docs.microsoft.com/et-ee/java/api/com.microsoft.applicationinsights.extensibility.context._device_context) (pouze tuto položku telemetrie je označené)
```Java
telemetry.getContext().getDevice().setRoleName("My Component Name");
```

## <a name="next-steps"></a>Další postup

- [Psát vlastní telemetrii](app-insights-api-custom-events-metrics.md)
- Připojit všechny součásti micro služby Application insights. Podívejte se na [podporované platformy](app-insights-platforms.md).
- Zobrazit [datový model](application-insights-data-model.md) pro typy a datový model Application Insights.
- Zjistěte, jak [rozšířit a filtrování telemetrie](app-insights-api-filtering-sampling.md).
- [Referenční informace k confg Application Insights](app-insights-configuration-with-applicationinsights-config.md)

