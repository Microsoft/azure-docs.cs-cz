---
title: Filtrování a předzpracování v sadě Application Insights SDK | Microsoft Docs
description: Zapište procesory telemetrie a Inicializátory telemetrie pro sadu SDK, aby bylo možné filtrovat nebo přidat vlastnosti dat před odesláním telemetrie na Application Insights portál.
ms.topic: conceptual
ms.date: 11/23/2016
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: cb9159b98b219c6fb04beb7bbbaade64fc72a30b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98927758"
---
# <a name="filter-and-preprocess-telemetry-in-the-application-insights-sdk"></a>Filtrování a předzpracování telemetrie v sadě Application Insights SDK

Můžete napsat a nakonfigurovat moduly plug-in pro sadu Application Insights SDK, abyste mohli přizpůsobit, jak může být telemetrie obohacena a zpracována před odesláním do služby Application Insights.

* [Vzorkování](sampling.md) snižuje objem telemetrie, aniž by to ovlivnilo vaše statistiky. Udržuje spolu se souvisejícími datovými body, takže můžete mezi nimi přecházet při diagnostice problému. V portálu se celkový počet vynásobí, aby se vzorkování vyrovnalo.
* Filtrování pomocí procesorů telemetrie vám umožňuje vyfiltrovat telemetrie v sadě SDK předtím, než se pošle na server. Můžete například snížit objem telemetrie vyloučením požadavků z robotů. Filtrování je obecnější přístup k omezení provozu než vzorkování. Umožňuje vám lepší kontrolu nad tím, co se přenáší, ale má vliv na vaše statistiky. Můžete například odfiltrovat všechny úspěšné požadavky.
* [Inicializátory telemetrie přidávají nebo upravují vlastnosti](#add-properties) jakékoli telemetrie odeslané z vaší aplikace, včetně telemetrie ze standardních modulů. Můžete například přidat počítané hodnoty nebo čísla verzí, podle kterých se budou data filtrovat na portálu.
* [Rozhraní API sady SDK](./api-custom-events-metrics.md) slouží k posílání vlastních událostí a metrik.

Než začnete, potřebujete:

* Nainstalujte příslušnou sadu SDK pro vaši aplikaci: [ASP.NET](asp-net.md), [ASP.NET Core](asp-net-core.md), [non http/Worker pro .NET/.NET Core](worker-service.md)nebo [JavaScript](javascript.md).

<a name="filtering"></a>

## <a name="filtering"></a>Filtrování

Tento postup vám poskytne přímou kontrolu nad tím, co je zahrnuto nebo vyloučeno z datového proudu telemetrie. Filtrování lze použít k vyřazení položek telemetrie z odeslání do Application Insights. Filtrování lze použít ve spojení s vzorkováním nebo samostatně.

Pokud chcete vyfiltrovat telemetrii, napíšete procesor telemetrie a zaregistrujete ho v `TelemetryConfiguration` . Veškerá telemetrie projde procesorem. Můžete ho z datového proudu odpojit nebo ho předat dalšímu procesoru v řetězu. Je zahrnutá telemetrie ze standardních modulů, jako je například kolektor požadavků HTTP a kolektor závislostí, a telemetrie, kterou jste si sami sledovali. Můžete například vyfiltrovat telemetrie o požadavcích z robotů nebo úspěšných volání závislostí.

> [!WARNING]
> Filtrování telemetrie odesílané ze sady SDK pomocí procesorů může zkosit statistiky, které vidíte na portálu, a usnadnit tak sledování souvisejících položek.
>
> Místo toho zvažte použití [vzorkování](./sampling.md).
>
>

### <a name="create-a-telemetry-processor-c"></a>Vytvoření procesoru telemetrie (C#)

1. Chcete-li vytvořit filtr, implementujte `ITelemetryProcessor` .

    Procesory telemetrie vytvoří řetězec zpracování. Při vytváření instance procesoru telemetrie budete mít odkaz na další procesor v řetězu. Když je do metody procesu předán datový bod telemetrie, provede svoji práci a potom volá (nebo nevolá) další procesor telemetrie v řetězu.

    ```csharp
    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.Extensibility;

    public class SuccessfulDependencyFilter : ITelemetryProcessor
    {
        private ITelemetryProcessor Next { get; set; }

        // next will point to the next TelemetryProcessor in the chain.
        public SuccessfulDependencyFilter(ITelemetryProcessor next)
        {
            this.Next = next;
        }

        public void Process(ITelemetry item)
        {
            // To filter out an item, return without calling the next processor.
            if (!OKtoSend(item)) { return; }

            this.Next.Process(item);
        }

        // Example: replace with your own criteria.
        private bool OKtoSend (ITelemetry item)
        {
            var dependency = item as DependencyTelemetry;
            if (dependency == null) return true;

            return dependency.Success != true;
        }
    }
    ```

2. Přidejte procesor.

**Aplikace** ASP.NET

Vložit tento fragment kódu do ApplicationInsights.config:

```xml
<TelemetryProcessors>
  <Add Type="WebApplication9.SuccessfulDependencyFilter, WebApplication9">
     <!-- Set public property -->
     <MyParamFromConfigFile>2-beta</MyParamFromConfigFile>
  </Add>
</TelemetryProcessors>
```

Můžete předat řetězcové hodnoty ze souboru. config tím, že zadáte veřejné pojmenované vlastnosti ve třídě.

> [!WARNING]
> Pečlivě se ujistěte, že název typu a všechny názvy vlastností v souboru. config se shodují s názvy tříd a vlastností v kódu. Pokud soubor. config odkazuje na neexistující typ nebo vlastnost, může se stát, že sada SDK bez jakýchkoli telemetrie nebude moci odeslat žádnou telemetrii.
>

Alternativně můžete inicializovat filtr v kódu. Do vhodné inicializační třídy, například AppStart v `Global.asax.cs` , vložte procesor do řetězce:

```csharp
var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
builder.Use((next) => new SuccessfulDependencyFilter(next));

// If you have more processors:
builder.Use((next) => new AnotherProcessor(next));

builder.Build();
```

Klienti telemetrie vytvořené po tomto okamžiku budou používat vaše procesory.

ASP.NET **jádro/aplikace služby pracovního procesu**

> [!NOTE]
> Přidání procesoru pomocí `ApplicationInsights.config` nebo `TelemetryConfiguration.Active` není platné pro ASP.NET Core aplikace nebo pokud používáte sadu Microsoft. ApplicationInsights. WorkerService SDK.

V případě aplikací napsaných pomocí [ASP.NET Core](asp-net-core.md#adding-telemetry-processors) nebo [WorkerService](worker-service.md#adding-telemetry-processors)je přidání nového procesoru telemetrie provedeno pomocí `AddApplicationInsightsTelemetryProcessor` metody rozšíření na `IServiceCollection` , jak je znázorněno na obrázku. Tato metoda je volána v `ConfigureServices` metodě vaší `Startup.cs` třídy.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // ...
        services.AddApplicationInsightsTelemetry();
        services.AddApplicationInsightsTelemetryProcessor<SuccessfulDependencyFilter>();

        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<AnotherProcessor>();
    }
```

### <a name="example-filters"></a>Ukázkové filtry

#### <a name="synthetic-requests"></a>Syntetické požadavky

Vyfiltrujte roboty a webové testy. I když vám Průzkumník metrik dává možnost vyfiltrovat syntetické zdroje, tato možnost zmenší přenos a velikost příjmu filtrováním v samotné sadě SDK.

```csharp
public void Process(ITelemetry item)
{
  if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource)) {return;}

  // Send everything else:
  this.Next.Process(item);
}
```

#### <a name="failed-authentication"></a>Neúspěšné ověření

Odfiltrování požadavků pomocí odpovědi "401".

```csharp
public void Process(ITelemetry item)
{
    var request = item as RequestTelemetry;

    if (request != null &&
    request.ResponseCode.Equals("401", StringComparison.OrdinalIgnoreCase))
    {
        // To filter out an item, return without calling the next processor.
        return;
    }

    // Send everything else
    this.Next.Process(item);
}
```

#### <a name="filter-out-fast-remote-dependency-calls"></a>Vyfiltrujte rychlá volání vzdálených závislostí.

Pokud chcete diagnostikovat jenom volání, která jsou pomalá, vyfiltrujte je rychleji.

> [!NOTE]
> Toto filtrování zkosí statistiku, kterou vidíte na portálu.
>
>

```csharp
public void Process(ITelemetry item)
{
    var request = item as DependencyTelemetry;

    if (request != null && request.Duration.TotalMilliseconds < 100)
    {
        return;
    }
    this.Next.Process(item);
}
```

#### <a name="diagnose-dependency-issues"></a>Diagnostika problémů se závislostmi

[Tento blog](https://azure.microsoft.com/blog/implement-an-application-insights-telemetry-processor/) popisuje projekt pro diagnostiku problémů se závislostmi tím, že automaticky posílá standardní příkazy pro odesílání do závislostí.

<a name="add-properties"></a>

### <a name="javascript-web-applications"></a>Webové aplikace v jazyce JavaScript

**Filtrování pomocí ITelemetryInitializer**

1. Vytvoří funkci zpětného volání inicializátoru telemetrie. Funkce zpětného volání přebírá `ITelemetryItem` jako parametr, což je událost, která je zpracovávána. `false`Výsledkem návratu z tohoto zpětného volání je položka telemetrie, která se má odfiltrovat.

   ```JS
   var filteringFunction = (envelope) => {
     if (envelope.data.someField === 'tobefilteredout') {
         return false;
     }
  
     return true;
   };
   ```

2. Přidejte zpětné volání inicializátoru telemetrie:

   ```JS
   appInsights.addTelemetryInitializer(filteringFunction);
   ```

## <a name="addmodify-properties-itelemetryinitializer"></a>Přidat nebo upravit vlastnosti: ITelemetryInitializer

Použijte Inicializátory telemetrie k obohacení telemetrie o další informace nebo přepište vlastnosti telemetrie nastavené standardními moduly telemetrie.

Například Application Insights pro webový balíček shromažďuje telemetrii o požadavcích HTTP. Ve výchozím nastavení se označí jako neúspěšné všechny žádosti s kódem odpovědi >= 400. Pokud ale chcete považovat 400 za úspěch, můžete poskytnout inicializátor telemetrie, který nastaví vlastnost success.

Pokud zadáte inicializátor telemetrie, je volána při každém volání jakékoli metody Track * (). To zahrnuje `Track()` metody, které jsou volány standardními moduly telemetrie. Podle konvence tyto moduly nenastaví žádnou vlastnost, která již byla nastavena inicializátorem. Inicializátory telemetrie se volají před voláním procesorů telemetrie. Takže jakákoli rozšíření prováděná Inicializátory jsou viditelná pro procesory.

**Definovat inicializátor**

*C#*

```csharp
using System;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;

namespace MvcWebRole.Telemetry
{
  /*
   * Custom TelemetryInitializer that overrides the default SDK
   * behavior of treating response codes >= 400 as failed requests
   *
   */
  public class MyTelemetryInitializer : ITelemetryInitializer
  {
    public void Initialize(ITelemetry telemetry)
    {
        var requestTelemetry = telemetry as RequestTelemetry;
        // Is this a TrackRequest() ?
        if (requestTelemetry == null) return;
        int code;
        bool parsed = Int32.TryParse(requestTelemetry.ResponseCode, out code);
        if (!parsed) return;
        if (code >= 400 && code < 500)
        {
            // If we set the Success property, the SDK won't change it:
            requestTelemetry.Success = true;

            // Allow us to filter these requests in the portal:
            requestTelemetry.Properties["Overridden400s"] = "true";
        }
        // else leave the SDK to set the Success property
    }
  }
}
```

**Aplikace ASP.NET: načíst inicializátor**

V ApplicationInsights.config:

```xml
<ApplicationInsights>
  <TelemetryInitializers>
    <!-- Fully qualified type name, assembly name: -->
    <Add Type="MvcWebRole.Telemetry.MyTelemetryInitializer, MvcWebRole"/>
    ...
  </TelemetryInitializers>
</ApplicationInsights>
```

Alternativně můžete vytvořit instanci inicializátoru v kódu, například v Global. aspx. cs:

```csharp
protected void Application_Start()
{
    // ...
    TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
}
```

Další informace najdete v [této ukázce](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole).

ASP.NET **Core/aplikace pracovní služby: načíst inicializátor**

> [!NOTE]
> Přidání inicializátoru pomocí `ApplicationInsights.config` nebo `TelemetryConfiguration.Active` není platné pro ASP.NET Core aplikace nebo pokud používáte sadu Microsoft. ApplicationInsights. WorkerService SDK.

V případě aplikací napsaných pomocí [ASP.NET Core](asp-net-core.md#adding-telemetryinitializers) nebo [WorkerService](worker-service.md#adding-telemetryinitializers)je přidání nového inicializátoru telemetrie provedeno jeho přidáním do kontejneru pro vkládání závislostí, jak je znázorněno na obrázku. To se provádí v `Startup.ConfigureServices` metodě.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```
### <a name="javascript-telemetry-initializers"></a>Inicializátory telemetrie JavaScript
*JavaScript*

Vložte inicializátor telemetrie hned po inicializačním kódu, který jste získali z portálu:

```JS
<script type="text/javascript">
    // ... initialization code
    ...({
        instrumentationKey: "your instrumentation key"
    });
    window.appInsights = appInsights;


    // Adding telemetry initializer.
    // This is called whenever a new telemetry item
    // is created.

    appInsights.addTelemetryInitializer(function (envelope) {
        var telemetryItem = envelope.data.baseData;

        // To check the telemetry items type - for example PageView:
        if (envelope.name == Microsoft.ApplicationInsights.Telemetry.PageView.envelopeType) {
            // this statement removes url from all page view documents
            telemetryItem.url = "URL CENSORED";
        }

        // To set custom properties:
        telemetryItem.properties = telemetryItem.properties || {};
        telemetryItem.properties["globalProperty"] = "boo";
        
        // To set cloud role name / instance
        envelope.tags["ai.cloud.role"] = "your role name";
        envelope.tags["ai.cloud.roleInstance"] = "your role instance";
    });

    // End of inserted code.

    appInsights.trackPageView();
</script>
```

Souhrn nevlastních vlastností dostupných pro položku telemetrie najdete v tématu [Application Insights Export datového modelu](./export-data-model.md).

Můžete přidat tolik inicializátorů, kolik chcete. Jsou volány v pořadí, v jakém byly přidány.

### <a name="opencensus-python-telemetry-processors"></a>Procesory telemetrie OpenCensus Python

Procesory telemetrie v OpenCensus Pythonu jsou funkce zpětného volání volané pro zpracování telemetrie před jejich exportem. Funkce zpětného volání musí jako svůj parametr přijmout datový typ [obálky](https://github.com/census-instrumentation/opencensus-python/blob/master/contrib/opencensus-ext-azure/opencensus/ext/azure/common/protocol.py#L86) . Pokud chcete vyfiltrovat telemetrie z exportu, ujistěte se, že funkce zpětného volání vrátí hodnotu `False` . V obálkách [na GitHubu](https://github.com/census-instrumentation/opencensus-python/blob/master/contrib/opencensus-ext-azure/opencensus/ext/azure/common/protocol.py)můžete zobrazit schéma pro Azure monitor datových typů.

> [!NOTE]
> Můžete upravit `cloud_RoleName` změnou `ai.cloud.role` atributu v `tags` poli.

```python
def callback_function(envelope):
    envelope.tags['ai.cloud.role'] = 'new_role_name'
```

```python
# Example for log exporter
import logging

from opencensus.ext.azure.log_exporter import AzureLogHandler

logger = logging.getLogger(__name__)

# Callback function to append '_hello' to each log message telemetry
def callback_function(envelope):
    envelope.data.baseData.message += '_hello'
    return True

handler = AzureLogHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>')
handler.add_telemetry_processor(callback_function)
logger.addHandler(handler)
logger.warning('Hello, World!')
```
```python
# Example for trace exporter
import requests

from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace import config_integration
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['requests'])

# Callback function to add os_type: linux to span properties
def callback_function(envelope):
    envelope.data.baseData.properties['os_type'] = 'linux'
    return True

exporter = AzureExporter(
    connection_string='InstrumentationKey=<your-instrumentation-key-here>'
)
exporter.add_telemetry_processor(callback_function)
tracer = Tracer(exporter=exporter, sampler=ProbabilitySampler(1.0))
with tracer.span(name='parent'):
response = requests.get(url='https://www.wikipedia.org/wiki/Rabbit')
```

```python
# Example for metrics exporter
import time

from opencensus.ext.azure import metrics_exporter
from opencensus.stats import aggregation as aggregation_module
from opencensus.stats import measure as measure_module
from opencensus.stats import stats as stats_module
from opencensus.stats import view as view_module
from opencensus.tags import tag_map as tag_map_module

stats = stats_module.stats
view_manager = stats.view_manager
stats_recorder = stats.stats_recorder

CARROTS_MEASURE = measure_module.MeasureInt("carrots",
                                            "number of carrots",
                                            "carrots")
CARROTS_VIEW = view_module.View("carrots_view",
                                "number of carrots",
                                [],
                                CARROTS_MEASURE,
                                aggregation_module.CountAggregation())

# Callback function to only export the metric if value is greater than 0
def callback_function(envelope):
    return envelope.data.baseData.metrics[0].value > 0

def main():
    # Enable metrics
    # Set the interval in seconds in which you want to send metrics
    exporter = metrics_exporter.new_metrics_exporter(connection_string='InstrumentationKey=<your-instrumentation-key-here>')
    exporter.add_telemetry_processor(callback_function)
    view_manager.register_exporter(exporter)

    view_manager.register_view(CARROTS_VIEW)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    mmap.measure_int_put(CARROTS_MEASURE, 1000)
    mmap.record(tmap)
    # Default export interval is every 15.0s
    # Your application should run for at least this amount
    # of time so the exporter will meet this interval
    # Sleep can fulfill this
    time.sleep(60)

    print("Done recording metrics")

if __name__ == "__main__":
    main()
```
Můžete přidat tolik procesorů, kolik chcete. Jsou volány v pořadí, v jakém byly přidány. Pokud jeden z procesorů vyvolá výjimku, nemá vliv na následující procesory.

### <a name="example-telemetryinitializers"></a>Příklad TelemetryInitializers

#### <a name="add-a-custom-property"></a>Přidat vlastní vlastnost

Následující ukázkový inicializátor přidá do každé sledované telemetrie vlastní vlastnost.

```csharp
public void Initialize(ITelemetry item)
{
  var itemProperties = item as ISupportProperties;
  if(itemProperties != null && !itemProperties.Properties.ContainsKey("customProp"))
    {
        itemProperties.Properties["customProp"] = "customValue";
    }
}
```

#### <a name="add-a-cloud-role-name"></a>Přidat název cloudové role

Následující ukázkový inicializátor nastaví název cloudové role na každou sledovanou telemetrii.

```csharp
public void Initialize(ITelemetry telemetry)
{
    if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
    {
        telemetry.Context.Cloud.RoleName = "MyCloudRoleName";
    }
}
```

#### <a name="add-information-from-httpcontext"></a>Přidat informace z HttpContext

Následující ukázkový inicializátor načte data z [`HttpContext`](/aspnet/core/fundamentals/http-context) a připojí je k `RequestTelemetry` instanci. `IHttpContextAccessor`Je automaticky zajištěn vložením závislosti konstruktoru.

```csharp
public class HttpContextRequestTelemetryInitializer : ITelemetryInitializer
{
    private readonly IHttpContextAccessor httpContextAccessor;

    public HttpContextRequestTelemetryInitializer(IHttpContextAccessor httpContextAccessor)
    {
        this.httpContextAccessor =
            httpContextAccessor ??
            throw new ArgumentNullException(nameof(httpContextAccessor));
    }

    public void Initialize(ITelemetry telemetry)
    {
        var requestTelemetry = telemetry as RequestTelemetry;
        if (requestTelemetry == null) return;

        var claims = this.httpContextAccessor.HttpContext.User.Claims;
        Claim oidClaim = claims.FirstOrDefault(claim => claim.Type == "oid");
        requestTelemetry.Properties.Add("UserOid", oidClaim?.Value);
    }
}
```

## <a name="itelemetryprocessor-and-itelemetryinitializer"></a>ITelemetryProcessor a ITelemetryInitializer

Jaký je rozdíl mezi procesory telemetrie a Inicializátory telemetrie?

* Existuje několik překrývajících se v tom, co můžete s nimi dělat. Obojí lze použít k přidání nebo úpravě vlastností telemetrie, i když doporučujeme pro tento účel použít inicializátory.
* Inicializátory telemetrie se vždycky spouštějí před procesory telemetrie.
* Inicializátory telemetrie mohou být volány více než jednou. Podle konvence nenastaví žádnou vlastnost, která již byla nastavena.
* Procesory telemetrie umožňují zcela nahradit nebo zrušit položku telemetrie.
* Všechny registrované Inicializátory telemetrie jsou zaručené pro každou položku telemetrie. Pro procesory telemetrie sada SDK garantuje volání prvního procesoru telemetrie. Určuje, zda jsou ostatní procesory volány nebo nikoli, o základě předchozích procesorů telemetrie.
* Použijte Inicializátory telemetrie k obohacení telemetrie o další vlastnosti nebo přepište existující. K odfiltrování telemetrie použijte procesor telemetrie.

## <a name="troubleshoot-applicationinsightsconfig"></a>Řešení potíží s ApplicationInsights.config

* Potvrďte, že plně kvalifikovaný název typu a název sestavení jsou správné.
* Potvrďte, že je soubor applicationinsights.config ve výstupním adresáři a obsahuje nedávné změny.

## <a name="reference-docs"></a>Referenční dokumenty

* [Přehled rozhraní API](./api-custom-events-metrics.md)
* [Odkaz na ASP.NET](/previous-versions/azure/dn817570(v=azure.100))

## <a name="sdk-code"></a>Kód sady SDK

* [Sada ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [SADA ASP.NET SDK](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)

## <a name="next-steps"></a><a name="next"></a>Další kroky
* [Hledat události a protokoly](./diagnostic-search.md)
* [kontrol](./sampling.md)
* [Řešení potíží](../faq.md)

