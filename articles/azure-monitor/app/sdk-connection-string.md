---
title: Připojovací řetězce v Přehledech aplikací Azure | Dokumenty společnosti Microsoft
description: Použití připojovacích řetězců.
ms.topic: conceptual
author: timothymothra
ms.author: tilee
ms.date: 01/17/2020
ms.reviewer: mbullwin
ms.openlocfilehash: 7b049c04913d3415074f46b9d90ec34be874a2da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136702"
---
# <a name="connection-strings"></a>Připojovací řetězce

## <a name="overview"></a>Přehled

Připojovací řetězce poskytují uživatelům Application Insight jedno nastavení konfigurace, což eliminuje potřebu více nastavení proxy serveru. Vysoce užitečné pro intranetové webové servery, suverénní nebo hybridní cloudová prostředí, která chtějí odesílat data monitorovací službě.

Dvojice hodnot klíče poskytují uživatelům snadný způsob, jak definovat kombinaci přípony předpony pro každou službu / produkt Application Insights (AI).

> [!IMPORTANT]
> Nedoporučujeme nastavení připojovacího řetězce a instrumentace. V případě, že uživatel nastaví obojí, podle toho, co bylo nastaveno jako poslední, bude mít přednost. 


## <a name="scenario-overview"></a>Přehled scénáře 

Scénáře zákazníků, kde to vizualizujeme s největším dopadem:

- Výjimky brány firewall nebo přesměrování serveru proxy 

    V případech, kdy je vyžadováno monitorování pro intranetový webový server, naše dřívější řešení požádalo zákazníky o přidání jednotlivých koncových bodů služby do vaší konfigurace. Další informace naleznete [zde](https://docs.microsoft.com/azure/azure-monitor/app/troubleshoot-faq#can-i-monitor-an-intranet-web-server). 
    Připojovací řetězce nabízejí lepší alternativu snížením tohoto úsilí na jedno nastavení. Jednoduchá předpona, přípona změna umožňuje automatické plnění a přesměrování všech koncových bodů na správné služby. 

- Suverénní nebo hybridní cloudová prostředí

    Uživatelé mohou odesílat data do definované [oblasti Azure Government .](https://docs.microsoft.com/azure/azure-government/documentation-government-services-monitoringandmanagement#application-insights)
    Připojovací řetězce umožňují definovat nastavení koncového bodu pro intranetové servery nebo nastavení hybridního cloudu. 

## <a name="getting-started"></a>Začínáme

### <a name="finding-my-connection-string"></a>Hledáte můj připojovací řetězec?

Připojovací řetězec se zobrazí v okně Přehled prostředku Application Insights.

![připojovací řetězec na přehledovém noži](media/overview-dashboard/overview-connection-string.png)

### <a name="schema"></a>Schéma

#### <a name="max-length"></a>Maximální délka

Připojení má maximální podporovanou délku 4096 znaků.

#### <a name="key-value-pairs"></a>Páry klíč-hodnota

Připojovací řetězec se skládá ze seznamu nastavení reprezentovaných jako dvojice klíč-hodnota oddělené středníkem:`key1=value1;key2=value2;key3=value3`

#### <a name="syntax"></a>Syntaxe

- `InstrumentationKey`(ex: 00000000-0000-0000-0000-000000000000000000)  Připojovací řetězec je **povinné** pole.
- `Authorization`(například: ikey) (Toto nastavení je volitelné, protože dnes podporujeme pouze autorizaci ikey.)
- `EndpointSuffix`(například: applicationinsights.azure.cn) Nastavení přípony koncového bodu dá suflávu SDK, ke kterému se má Azure připojit. Sada SDK sestaví zbytek koncového bodu pro jednotlivé služby.
- Explicitní koncové body.
  Jakákoli služba může být explicitně přepsána v připojovacím řetězci.
   - `IngestionEndpoint`(například:https://dc.applicationinsights.azure.com)
   - `LiveEndpoint`(například:https://live.applicationinsights.azure.com)
   - `ProfilerEndpoint`(například:https://profiler.applicationinsights.azure.com)
   - `SnapshotEndpoint`(například:https://snapshot.applicationinsights.azure.com)

#### <a name="endpoint-schema"></a>Schéma koncového bodu

`<prefix>.<suffix>`
- Předpona: Definuje službu. 
- Přípona: Definuje společný název domény.

##### <a name="valid-suffixes"></a>Platné přípony

Zde je seznam platných přípon 
- applicationinsights.azure.cn
- applicationinsights.us


Viz také:https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification


##### <a name="valid-prefixes"></a>Platné předpony

- [Telemetrické požití](./app-insights-overview.md):`dc`
- [Živé metriky](./live-stream.md):`live`
- [Profiler](./profiler-overview.md):`profiler`
- [Snímek](./snapshot-debugger.md):`snapshot`



## <a name="connection-string-examples"></a>Příklady připojovacího řetězce


### <a name="minimal-valid-connection-string"></a>Minimální platný připojovací řetězec

`InstrumentationKey=00000000-0000-0000-0000-000000000000;`

V tomto příkladu byl nastaven pouze klíč instrumentace.

- Autorizační schéma výchozí na "ikey" 
- Instrumentační klíč: 00000000-0000-0000-0000-000000000000000000000000000000
- Identifikátory URI místní služby jsou založeny na [výchozích hodnotách sady SDK](https://github.com/microsoft/ApplicationInsights-dotnet/blob/e50d569cebf485e72e98f4a08a0bc0e30cdf42bc/BASE/src/Microsoft.ApplicationInsights/Extensibility/Implementation/Endpoints/Constants.cs#L6) a připojí se k veřejnému globálnímu Azure:
   - Požití:https://dc.services.visualstudio.com/
   - Živé metriky:https://rt.services.visualstudio.com/
   - Profiler:https://agent.azureserviceprofiler.net/
   - Ladicí program:https://agent.azureserviceprofiler.net/  



### <a name="connection-string-with-endpoint-suffix"></a>Připojovací řetězec s příponou koncového bodu

`InstrumentationKey=00000000-0000-0000-0000-000000000000;EndpointSuffix=ai.contoso.com;`

V tomto příkladu tento připojovací řetězec určuje příponu koncového bodu a sada SDK vytvoří koncové body služby.

- Autorizační schéma výchozí na "ikey" 
- Instrumentační klíč: 00000000-0000-0000-0000-000000000000000000000000000000
- Místní identifikátory URI služby jsou založeny na poskytnuté příponě koncového bodu: 
   - Požití:https://dc.ai.contoso.com
   - Živé metriky:https://live.ai.contoso.com
   - Profiler:https://profiler.ai.contoso.com 
   - Ladicí program:https://snapshot.ai.contoso.com   



### <a name="connection-string-with-explicit-endpoint-overrides"></a>Připojovací řetězec s explicitními přepsáními koncového bodu 

`InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://custom.com:111/;LiveEndpoint=https://custom.com:222/;ProfilerEndpoint=https://custom.com:333/;SnapshotEndpoint=https://custom.com:444/;`

V tomto příkladu tento připojovací řetězec určuje explicitní lokální změny pro každou službu. Sada SDK bude používat přesné koncové body, které jsou k dispozici bez epoých.

- Autorizační schéma výchozí na "ikey" 
- Instrumentační klíč: 00000000-0000-0000-0000-000000000000000000000000000000
- Identifikátory URI místní služby jsou založeny na explicitních hodnotách přepsání: 
   - Požití: https:\//custom.com:111/
   - Živé metriky:\/https: /custom.com:222/
   - Profiler: https:\//custom.com:333/ 
   - Debugger: https:\//custom.com:444/   


## <a name="how-to-set-a-connection-string"></a>Jak nastavit připojovací řetězec

Připojovací řetězce jsou podporovány v následujících verzích sady SDK:
- .NET a .NET Core v2.12.0
- Java v2.5.1
- Javascript v2.3.0
- NodeJS v1.5.0
- Python v1.0.0

Připojovací řetězec lze nastavit buď v kódu, proměnné prostředí nebo konfiguračním souboru.



### <a name="environment-variable"></a>Proměnná prostředí

- Připojovací řetězec:`APPLICATIONINSIGHTS_CONNECTION_STRING`

### <a name="net-sdk-example"></a>Příklad sady .Net SDK

TelemetryConfiguration.ConnectionString:https://github.com/microsoft/ApplicationInsights-dotnet/blob/add45ceed35a817dc7202ec07d3df1672d1f610d/BASE/src/Microsoft.ApplicationInsights/Extensibility/TelemetryConfiguration.cs#L271-L274

.Net Explicitně nastavit:
```csharp
var configuration = new TelemetryConfiguration
{
    ConnectionString = "InstrumentationKey=00000000-0000-0000-0000-000000000000;"
};
```

Konfigurační soubor .Net:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
    <ConnectionString>InstrumentationKey=00000000-0000-0000-0000-000000000000</ConnectionString>
</ApplicationInsights>
```


NetCore config.json: 

```json
{
  "ApplicationInsights": {
    "ConnectionString" : "InstrumentationKey=00000000-0000-0000-0000-000000000000;"
    }
  }
```


### <a name="java-sdk-example"></a>Příklad sady Java SDK


Java explicitně nastavit:
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

### <a name="javascript-sdk-example"></a>Příklad sady Javascript SDK

Důležité: Javascript nepodporuje použití proměnných prostředí.

Použití úryvku:

```javascript
<script type="text/javascript">
var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("addTelemetryInitializer"),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),t.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4},!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
{
  connectionString:"InstrumentationKey=00000000-0000-0000-0000-000000000000;"
}
);window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```


Ruční nastavení:
```javascript
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  connectionString: 'InstrumentationKey=00000000-0000-0000-0000-000000000000;'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
appInsights.trackPageView();
```

### <a name="node-sdk-example"></a>Příklad sady SDK uzlu

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("InstrumentationKey=00000000-0000-0000-0000-000000000000;");
appInsights.start();
```

### <a name="python-sdk-example"></a>Příklad sady Python SDK

Doporučujeme uživatelům nastavit proměnnou prostředí.

Explicitní nastavení připojovacího řetězce:

```python
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tracer = Tracer(exporter=AzureExporter(connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'), sampler=ProbabilitySampler(1.0))
```


## <a name="next-steps"></a>Další kroky

Začínáme při spuštění:

* [Škálovací sady azure virtuálních počítačů a škálovací sady virtuálních strojů Azure hostované v aplikacích hostovaných ve službě IIS](../../azure-monitor/app/azure-vm-vmss-apps.md)
* [Server IIS](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Azure Web Apps](../../azure-monitor/app/azure-web-apps.md)

Začínáme při vývoji:

* [ASP.NET](../../azure-monitor/app/asp-net.md)
* [ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [Node.js](../../azure-monitor/app/nodejs.md)
* [Python](../../azure-monitor/app/opencensus-python.md)
