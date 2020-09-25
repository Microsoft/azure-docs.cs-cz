---
title: Připojovací řetězce v Azure Application Insights | Microsoft Docs
description: Jak používat připojovací řetězce.
ms.topic: conceptual
author: timothymothra
ms.author: tilee
ms.date: 01/17/2020
ms.custom: devx-track-js, devx-track-csharp
ms.reviewer: mbullwin
ms.openlocfilehash: f4227c28329233c7f414c6c45e4a3c1420bf47be
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91335156"
---
# <a name="connection-strings"></a>Připojovací řetězce

## <a name="overview"></a>Přehled

Připojovací řetězce poskytují uživatelům přehledné aplikace s jediným nastavením konfigurace, což eliminuje nutnost více nastavení proxy serveru. Vysoce užitečné pro intranetové webové servery, svrchované nebo hybridní cloudové prostředí, které se pokouší odeslat data do monitorovací služby.

Páry klíč-hodnota poskytují uživatelům snadný způsob, jak definovat kombinaci přípony předpony pro každou službu a produkt Application Insights (AI).

> [!IMPORTANT]
> Nedoporučujeme nastavovat připojovací řetězec a klíč instrumentace. V případě, že uživatel nastavení nastavil, podle toho, co byl nastaven jako poslední, bude mít přednost. 


## <a name="scenario-overview"></a>Přehled scénáře 

Scénáře zákazníků, kde vizualizuji, že to má největší dopad:

- Výjimky brány firewall nebo přesměrování proxy 

    V případech, kdy je vyžadováno monitorování pro intranetový webový server, jsme naši starší řešení požádali o přidání jednotlivých koncových bodů služby do vaší konfigurace. Další informace najdete [tady](../faq.md#can-i-monitor-an-intranet-web-server). 
    Připojovací řetězce nabízejí lepší alternativu tím, že se toto úsilí omezuje na jedno nastavení. Jednoduchá předpona, změna přípony umožňuje automatické plnění a přesměrování všech koncových bodů do správných služeb. 

- Svrchovaná nebo hybridní cloudová prostředí

    Uživatelé mohou odesílat data do definované [oblasti Azure Government](../../azure-government/compare-azure-government-global-azure.md#application-insights).
    Připojovací řetězce umožňují definovat nastavení koncových bodů pro intranetové servery nebo nastavení hybridního cloudu. 

## <a name="getting-started"></a>Začínáme

### <a name="finding-my-connection-string"></a>Hledáte připojovací řetězec?

Váš připojovací řetězec se zobrazí v okně Přehled prostředku Application Insights.

![připojovací řetězec pro okno s přehledem](media/overview-dashboard/overview-connection-string.png)

### <a name="schema"></a>Schéma

#### <a name="max-length"></a>Maximální délka

Připojení má maximální podporovanou délku 4096 znaků.

#### <a name="key-value-pairs"></a>Páry klíč-hodnota

Připojovací řetězec se skládá ze seznamu nastavení reprezentovaného jako páry klíč-hodnota oddělených středníkem: `key1=value1;key2=value2;key3=value3`

#### <a name="syntax"></a>Syntax

- `InstrumentationKey` (např.: 00000000-0000-0000-0000-000000000000)  Připojovací řetězec je **povinné** pole.
- `Authorization` (např.: ikey) (Toto nastavení je volitelné, protože dnes podporujeme jenom autorizaci ikey.)
- `EndpointSuffix` (např.: applicationinsights.azure.cn) Nastavením přípony koncového bodu budete dát pokyn k sadě SDK, ke které se cloud Azure připojuje. Sada SDK bude sestavovat zbytek koncového bodu pro jednotlivé služby.
- Explicitní koncové body.
  Jakoukoli službu lze explicitně přepsat v připojovacím řetězci.
   - `IngestionEndpoint` (např. `https://dc.applicationinsights.azure.com` )
   - `LiveEndpoint` (např. `https://live.applicationinsights.azure.com` )
   - `ProfilerEndpoint` (např. `https://profiler.applicationinsights.azure.com` )
   - `SnapshotEndpoint` (např. `https://snapshot.applicationinsights.azure.com` )

#### <a name="endpoint-schema"></a>Schéma koncového bodu

`<prefix>.<suffix>`
- Prefix: definuje službu. 
- Přípona: definuje běžný název domény.

##### <a name="valid-suffixes"></a>Platné přípony

Tady je seznam platných přípon. 
- applicationinsights.azure.cn
- applicationinsights.us


Viz také: https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification


##### <a name="valid-prefixes"></a>Platné předpony

- [Přijímání telemetrie](./app-insights-overview.md): `dc`
- [Živé metriky](./live-stream.md): `live`
- [Profiler](./profiler-overview.md): `profiler`
- [Snímek](./snapshot-debugger.md): `snapshot`



## <a name="connection-string-examples"></a>Příklady připojovacího řetězce


### <a name="minimal-valid-connection-string"></a>Minimální platný připojovací řetězec

`InstrumentationKey=00000000-0000-0000-0000-000000000000;`

V tomto příkladu se nastavil jenom klíč instrumentace.

- Ve výchozím nastavení se schéma autorizace používá jako "ikey". 
- Klíč instrumentace: 00000000-0000-0000-0000-000000000000
- Identifikátory URI regionálních služeb jsou založené na [výchozích hodnotách sady SDK](https://github.com/microsoft/ApplicationInsights-dotnet/blob/e50d569cebf485e72e98f4a08a0bc0e30cdf42bc/BASE/src/Microsoft.ApplicationInsights/Extensibility/Implementation/Endpoints/Constants.cs#L6) a připojovat se k veřejnému globálnímu Azure:
   - Ingestování `https://dc.services.visualstudio.com/`
   - Živé metriky: `https://rt.services.visualstudio.com/`
   - Modulu `https://agent.azureserviceprofiler.net/`
   - Ladění `https://agent.azureserviceprofiler.net/`



### <a name="connection-string-with-endpoint-suffix"></a>Připojovací řetězec s příponou koncového bodu

`InstrumentationKey=00000000-0000-0000-0000-000000000000;EndpointSuffix=ai.contoso.com;`

V tomto příkladu tento připojovací řetězec Určuje příponu koncového bodu a sada SDK vytvoří koncové body služby.

- Ve výchozím nastavení se schéma autorizace používá jako "ikey". 
- Klíč instrumentace: 00000000-0000-0000-0000-000000000000
- Identifikátory URI regionálních služeb jsou založené na zadané příponě koncového bodu: 
   - Ingestování `https://dc.ai.contoso.com`
   - Živé metriky: `https://live.ai.contoso.com`
   - Modulu `https://profiler.ai.contoso.com`
   - Ladění `https://snapshot.ai.contoso.com`  



### <a name="connection-string-with-explicit-endpoint-overrides"></a>Připojovací řetězec s explicitním přepsáním koncového bodu 

`InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://custom.com:111/;LiveEndpoint=https://custom.com:222/;ProfilerEndpoint=https://custom.com:333/;SnapshotEndpoint=https://custom.com:444/;`

V tomto příkladu tento připojovací řetězec Určuje Explicitní přepsání pro každou službu. Sada SDK bude používat přesné koncové body, které jsou poskytovány beze změny.

- Ve výchozím nastavení se schéma autorizace používá jako "ikey". 
- Klíč instrumentace: 00000000-0000-0000-0000-000000000000
- Identifikátory URI regionálních služeb jsou založené na explicitních hodnotách přepsání: 
   - Ingestování `https://custom.com:111/`
   - Živé metriky: `https://custom.com:222/`
   - Modulu `https://custom.com:333/`
   - Ladění `https://custom.com:444/`  


## <a name="how-to-set-a-connection-string"></a>Nastavení připojovacího řetězce

Připojovací řetězce jsou podporovány v následujících verzích sady SDK:
- .NET a .NET Core v 2.12.0
- Java v 2.5.1 a Java 3,0
- JavaScript v 2.3.0
- NodeJS v 1.5.0
- Python v 1.0.0

Připojovací řetězec může být nastaven buď v kódu, v proměnné prostředí, nebo v konfiguračním souboru.



### <a name="environment-variable"></a>Proměnná prostředí

- Připojovací řetězec: `APPLICATIONINSIGHTS_CONNECTION_STRING`

# <a name="netnetcore"></a>[.NET/. NetCore](#tab/net)

TelemetryConfiguration. ConnectionString: https://github.com/microsoft/ApplicationInsights-dotnet/blob/add45ceed35a817dc7202ec07d3df1672d1f610d/BASE/src/Microsoft.ApplicationInsights/Extensibility/TelemetryConfiguration.cs#L271-L274

Explicitně nastavené rozhraní .NET:
```csharp
var configuration = new TelemetryConfiguration
{
    ConnectionString = "InstrumentationKey=00000000-0000-0000-0000-000000000000;"
};
```

Konfigurační soubor .NET:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
    <ConnectionString>InstrumentationKey=00000000-0000-0000-0000-000000000000</ConnectionString>
</ApplicationInsights>
```

Explicitně nastavené NetCore:
```csharp
public void ConfigureServices(IServiceCollection services)
{
    var options = new ApplicationInsightsServiceOptions { ConnectionString = "InstrumentationKey=00000000-0000-0000-0000-000000000000;" };
    services.AddApplicationInsightsTelemetry(options: options);
}
```

NetCore config.js: 

```json
{
  "ApplicationInsights": {
    "ConnectionString" : "InstrumentationKey=00000000-0000-0000-0000-000000000000;"
    }
  }
```


# <a name="java"></a>[Java](#tab/java)


Explicitně nastavené Java (v 2,5. x):
```java
TelemetryConfiguration.getActive().setConnectionString("InstrumentationKey=00000000-0000-0000-0000-000000000000");
```

ApplicationInsights.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
    <ConnectionString>InstrumentationKey=00000000-0000-0000-0000-000000000000;</ConnectionString>
</ApplicationInsights>
```

# <a name="javascript"></a>[JavaScript](#tab/js)

Důležité: jazyk JavaScript nepodporuje použití proměnných prostředí.

Použití fragmentu:

```javascript
<script type="text/javascript">
var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("addTelemetryInitializer"),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),t.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4},!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
{
  connectionString:"InstrumentationKey=00000000-0000-0000-0000-000000000000;"
}
);window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```


Ruční instalace:
```javascript
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  connectionString: 'InstrumentationKey=00000000-0000-0000-0000-000000000000;'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
appInsights.trackPageView();
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("InstrumentationKey=00000000-0000-0000-0000-000000000000;");
appInsights.start();
```

# <a name="python"></a>[Python](#tab/python)

Doporučujeme, aby uživatelé nastavili proměnnou prostředí.

Postup explicitního nastavení připojovacího řetězce:

```python
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tracer = Tracer(exporter=AzureExporter(connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'), sampler=ProbabilitySampler(1.0))
```


## <a name="next-steps"></a>Další kroky

Začínáme při spuštění:

* [Virtuální počítače Azure a škálování sady virtuálních počítačů Azure – aplikace hostované službou IIS](./azure-vm-vmss-apps.md)
* [Server IIS](./monitor-performance-live-website-now.md)
* [Azure Web Apps](./azure-web-apps.md)

Začínáme při vývoji:

* [ASP.NET](./asp-net.md)
* [ASP.NET Core](./asp-net-core.md)
* [Java](./java-get-started.md)
* [Node.js](./nodejs.md)
* [Python](./opencensus-python.md)

