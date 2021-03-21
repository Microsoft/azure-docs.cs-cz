---
title: Připojovací řetězce v Azure Application Insights | Microsoft Docs
description: Jak používat připojovací řetězce.
ms.topic: conceptual
author: timothymothra
ms.author: tilee
ms.date: 01/17/2020
ms.custom: devx-track-js, devx-track-csharp
ms.reviewer: mbullwin
ms.openlocfilehash: df87b060423aeff9fa5f83f21634395fe30e0bbb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102486280"
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
   - `ProfilerEndpoint` (např. `https://profiler.monitor.azure.com` )
   - `SnapshotEndpoint` (např. `https://snapshot.monitor.azure.com` )

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
- Identifikátory URI regionálních služeb jsou založené na [výchozích hodnotách sady SDK](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/BASE/src/Microsoft.ApplicationInsights/Extensibility/Implementation/Endpoints/Constants.cs) a připojovat se k veřejnému globálnímu Azure:
   - Ingestování `https://dc.services.visualstudio.com/`
   - Živé metriky: `https://rt.services.visualstudio.com/`
   - Modulu `https://profiler.monitor.azure.com/`
   - Ladění `https://snapshot.monitor.azure.com/`



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

### <a name="code-samples"></a>Ukázky kódů

# <a name="netnetcore"></a>[.NET/. NetCore](#tab/net)

Nastavte vlastnost [TelemetryConfiguration. ConnectionString](https://github.com/microsoft/ApplicationInsights-dotnet/blob/add45ceed35a817dc7202ec07d3df1672d1f610d/BASE/src/Microsoft.ApplicationInsights/Extensibility/TelemetryConfiguration.cs#L271-L274) nebo [ApplicationInsightsServiceOptions. ConnectionString.](https://github.com/microsoft/ApplicationInsights-dotnet/blob/81288f26921df1e8e713d31e7e9c2187ac9e6590/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs#L66-L69)

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

Aktuální fragment (uvedený níže) je verze "5", verze je ve fragmentu kódování zakódována jako sv: "#" a [aktuální verze je také k dispozici na GitHubu](https://go.microsoft.com/fwlink/?linkid=2156318).

```html
<script type="text/javascript">
!function(T,l,y){var S=T.location,k="script",D="instrumentationKey",C="ingestionendpoint",I="disableExceptionTracking",E="ai.device.",b="toLowerCase",w="crossOrigin",N="POST",e="appInsightsSDK",t=y.name||"appInsights";(y.name||T[e])&&(T[e]=t);var n=T[t]||function(d){var g=!1,f=!1,m={initialize:!0,queue:[],sv:"5",version:2,config:d};function v(e,t){var n={},a="Browser";return n[E+"id"]=a[b](),n[E+"type"]=a,n["ai.operation.name"]=S&&S.pathname||"_unknown_",n["ai.internal.sdkVersion"]="javascript:snippet_"+(m.sv||m.version),{time:function(){var e=new Date;function t(e){var t=""+e;return 1===t.length&&(t="0"+t),t}return e.getUTCFullYear()+"-"+t(1+e.getUTCMonth())+"-"+t(e.getUTCDate())+"T"+t(e.getUTCHours())+":"+t(e.getUTCMinutes())+":"+t(e.getUTCSeconds())+"."+((e.getUTCMilliseconds()/1e3).toFixed(3)+"").slice(2,5)+"Z"}(),iKey:e,name:"Microsoft.ApplicationInsights."+e.replace(/-/g,"")+"."+t,sampleRate:100,tags:n,data:{baseData:{ver:2}}}}var h=d.url||y.src;if(h){function a(e){var t,n,a,i,r,o,s,c,u,p,l;g=!0,m.queue=[],f||(f=!0,t=h,s=function(){var e={},t=d.connectionString;if(t)for(var n=t.split(";"),a=0;a<n.length;a++){var i=n[a].split("=");2===i.length&&(e[i[0][b]()]=i[1])}if(!e[C]){var r=e.endpointsuffix,o=r?e.location:null;e[C]="https://"+(o?o+".":"")+"dc."+(r||"services.visualstudio.com")}return e}(),c=s[D]||d[D]||"",u=s[C],p=u?u+"/v2/track":d.endpointUrl,(l=[]).push((n="SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details)",a=t,i=p,(o=(r=v(c,"Exception")).data).baseType="ExceptionData",o.baseData.exceptions=[{typeName:"SDKLoadFailed",message:n.replace(/\./g,"-"),hasFullStack:!1,stack:n+"\nSnippet failed to load ["+a+"] -- Telemetry is disabled\nHelp Link: https://go.microsoft.com/fwlink/?linkid=2128109\nHost: "+(S&&S.pathname||"_unknown_")+"\nEndpoint: "+i,parsedStack:[]}],r)),l.push(function(e,t,n,a){var i=v(c,"Message"),r=i.data;r.baseType="MessageData";var o=r.baseData;return o.message='AI (Internal): 99 message:"'+("SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details) ("+n+")").replace(/\"/g,"")+'"',o.properties={endpoint:a},i}(0,0,t,p)),function(e,t){if(JSON){var n=T.fetch;if(n&&!y.useXhr)n(t,{method:N,body:JSON.stringify(e),mode:"cors"});else if(XMLHttpRequest){var a=new XMLHttpRequest;a.open(N,t),a.setRequestHeader("Content-type","application/json"),a.send(JSON.stringify(e))}}}(l,p))}function i(e,t){f||setTimeout(function(){!t&&m.core||a()},500)}var e=function(){var n=l.createElement(k);n.src=h;var e=y[w];return!e&&""!==e||"undefined"==n[w]||(n[w]=e),n.onload=i,n.onerror=a,n.onreadystatechange=function(e,t){"loaded"!==n.readyState&&"complete"!==n.readyState||i(0,t)},n}();y.ld<0?l.getElementsByTagName("head")[0].appendChild(e):setTimeout(function(){l.getElementsByTagName(k)[0].parentNode.appendChild(e)},y.ld||0)}try{m.cookie=l.cookie}catch(p){}function t(e){for(;e.length;)!function(t){m[t]=function(){var e=arguments;g||m.queue.push(function(){m[t].apply(m,e)})}}(e.pop())}var n="track",r="TrackPage",o="TrackEvent";t([n+"Event",n+"PageView",n+"Exception",n+"Trace",n+"DependencyData",n+"Metric",n+"PageViewPerformance","start"+r,"stop"+r,"start"+o,"stop"+o,"addTelemetryInitializer","setAuthenticatedUserContext","clearAuthenticatedUserContext","flush"]),m.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4};var s=(d.extensionConfig||{}).ApplicationInsightsAnalytics||{};if(!0!==d[I]&&!0!==s[I]){var c="onerror";t(["_"+c]);var u=T[c];T[c]=function(e,t,n,a,i){var r=u&&u(e,t,n,a,i);return!0!==r&&m["_"+c]({message:e,url:t,lineNumber:n,columnNumber:a,error:i}),r},d.autoExceptionInstrumented=!0}return m}(y.cfg);function a(){y.onInit&&y.onInit(n)}(T[t]=n).queue&&0===n.queue.length?(n.queue.push(a),n.trackPageView({})):a()}(window,document,{
src: "https://js.monitor.azure.com/scripts/b/ai.2.min.js", // The SDK URL Source
// name: "appInsights", // Global SDK Instance name defaults to "appInsights" when not supplied
// ld: 0, // Defines the load delay (in ms) before attempting to load the sdk. -1 = block page load and add to head. (default) = 0ms load after timeout,
// useXhr: 1, // Use XHR instead of fetch to report failures (if available),
crossOrigin: "anonymous", // When supplied this will add the provided value as the cross origin attribute on the script tag
// onInit: null, // Once the application insights instance has loaded and initialized this callback function will be called with 1 argument -- the sdk instance (DO NOT ADD anything to the sdk.queue -- As they won't get called)
cfg: { // Application Insights Configuration
  connectionString:"InstrumentationKey=00000000-0000-0000-0000-000000000000;"
}});
</script>
```

> [!NOTE]
> Z důvodu čitelnosti a snížení možných chyb JavaScriptu jsou všechny možné možnosti konfigurace uvedeny na novém řádku ve výše uvedeném kódu fragmentu, pokud nechcete změnit hodnotu řádku s komentářem, který je možné odebrat.

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

---

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

