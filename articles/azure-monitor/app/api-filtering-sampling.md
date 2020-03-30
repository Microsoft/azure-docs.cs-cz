---
title: Filtrování a předběžné zpracování v sadě Azure Application Insights SDK | Dokumenty společnosti Microsoft
description: Zapsat telemetrické procesory a telemetrické inicializátory pro sdk filtrovat nebo přidat vlastnosti do dat před telemetrie je odeslána na portál Application Insights.
ms.topic: conceptual
ms.date: 11/23/2016
ms.openlocfilehash: 53b6ecc51961feba35d571eab3115c8e7ccf9964
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366296"
---
# <a name="filtering-and-preprocessing-telemetry-in-the-application-insights-sdk"></a>Filtrování a předzpracování telemetrie v application insights SDK

Můžete psát a konfigurovat moduly plug-in pro application insights SDK přizpůsobit, jak telemetrie může být obohacena a zpracována před odesláním do služby Application Insights.

* [Vzorkování](sampling.md) snižuje objem telemetrie bez ovlivnění statistiky. Udržuje pohromadě související datové body, takže můžete procházet mezi nimi při diagnostice problému. Na portálu se celkový počet násobí, aby se kompenzoval odběr vzorků.
* Filtrování pomocí telemetrických procesorů umožňuje odfiltrovat telemetrii v sadě SDK před odesláním na server. Můžete například snížit objem telemetrie vyloučením požadavků z robotů. Filtrování je základnější přístup ke snížení provozu než vzorkování. To vám umožní větší kontrolu nad tím, co se přenáší, ale musíte si být vědomi toho, že ovlivňuje vaše statistiky - například pokud odfiltrujete všechny úspěšné požadavky.
* [Telemetrické inicializátory přidávají nebo upravují vlastnosti](#add-properties) do jakékoli telemetrie odeslané z vaší aplikace, včetně telemetrie ze standardních modulů. Můžete například přidat vypočtené hodnoty; nebo čísla verzí, podle kterých můžete filtrovat data na portálu.
* [Rozhraní SDK API](../../azure-monitor/app/api-custom-events-metrics.md) se používá k odesílání vlastních událostí a metrik.

Než začnete, potřebujete:

* Nainstalujte příslušnou sadu SDK pro vaši aplikaci: [ASP.NET](asp-net.md), [ASP.NET Core](asp-net-core.md), [Non HTTP/Worker pro .NET/.NET Core](worker-service.md), [Java](../../azure-monitor/app/java-get-started.md) nebo [JavaScript](javascript.md)

<a name="filtering"></a>

## <a name="filtering"></a>Filtrování

Tato technika poskytuje přímou kontrolu nad co je zahrnuto nebo vyloučeno z telemetrického proudu. Filtrování lze přetáhnout položky telemetrie z odesílané do Application Insights. Můžete jej použít ve spojení s vzorkováním, nebo samostatně.

Chcete-li filtrovat telemetrii, napíšete `TelemetryConfiguration`telemetrický procesor a zaregistrujete jej pomocí . Všechny telemetrie prochází procesorem a můžete si vybrat, že ho přetáhnete z datového proudu nebo ji předáte dalšímu procesoru v řetězci. To zahrnuje telemetrická data ze standardních modulů, jako je například shromažďování požadavků HTTP a kolekcí závislostí a telemetrická data, které jste sami sledovali. Můžete například odfiltrovat telemetrii o požadavcích od robotů nebo úspěšná volání závislostí.

> [!WARNING]
> Filtrování telemetrie odeslané ze sady SDK pomocí procesorů může zkreslit statistiky, které se zobrazí na portálu a ztížit sledování souvisejících položek.
>
> Místo toho zvažte použití [vzorkování](../../azure-monitor/app/sampling.md).
>
>

### <a name="create-a-telemetry-processor-c"></a>Vytvoření telemetrického procesoru (C#)

1. Chcete-li vytvořit `ITelemetryProcessor`filtr, implementujte .

    Všimněte si, že telemetrické procesory vytvořit řetězec zpracování. Při vytváření instanci telemetrického procesoru, jsou uvedeny odkaz na další procesor v řetězci. Když je datový bod telemetrie předán metodě Process, provádí svou práci a pak volá (nebo nevolá) další procesor telemetrie v řetězci.

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

**ASP.NET aplikace** Vložte tento úryvek do souboru ApplicationInsights.config:

```xml
<TelemetryProcessors>
  <Add Type="WebApplication9.SuccessfulDependencyFilter, WebApplication9">
     <!-- Set public property -->
     <MyParamFromConfigFile>2-beta</MyParamFromConfigFile>
  </Add>
</TelemetryProcessors>
```

Hodnoty řetězců můžete předat ze souboru .config poskytnutím veřejných pojmenovaných vlastností ve vaší třídě.

> [!WARNING]
> Dbát na to, aby odpovídaly název typu a všechny názvy vlastností v souboru .config na třídy a názvy vlastností v kódu. Pokud soubor .config odkazuje na neexistující typ nebo vlastnost, může sada SDK bezobslužně neodeslat žádnou telemetrii.
>

**Případně** můžete inicializovat filtr v kódu. Ve vhodné inicializační třídě - `Global.asax.cs` například AppStart in - vložte procesor do řetězce:

```csharp
var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
builder.Use((next) => new SuccessfulDependencyFilter(next));

// If you have more processors:
builder.Use((next) => new AnotherProcessor(next));

builder.Build();
```

TelemetryKlienti vytvořené po tomto okamžiku budou používat procesory.

**ASP.NET aplikace Core/ Pracovní služby**

> [!NOTE]
> Přidání procesoru `ApplicationInsights.config` `TelemetryConfiguration.Active` pomocí nebo použití není platné pro ASP.NET základní aplikace nebo pokud používáte Microsoft.ApplicationInsights.WorkerService SDK.

Pro aplikace napsané pomocí [ASP.NET Core](asp-net-core.md#adding-telemetry-processors) `TelemetryProcessor` nebo [WorkerService](worker-service.md#adding-telemetry-processors), přidání nového se provádí pomocí `AddApplicationInsightsTelemetryProcessor` metody rozšíření na `IServiceCollection`, jak je znázorněno níže. Tato metoda je `ConfigureServices` volána `Startup.cs` v metodě vaší třídy.

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

Odfiltrujte roboty a webové testy. Přestože Průzkumník metrik poskytuje možnost odfiltrovat syntetické zdroje, tato možnost snižuje návštěvnost a velikost ingestování filtrováním v samotné sadě SDK.

```csharp
public void Process(ITelemetry item)
{
  if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource)) {return;}

  // Send everything else:
  this.Next.Process(item);
}
```

#### <a name="failed-authentication"></a>Neúspěšné ověření

Odfiltrovat požadavky s odpovědí "401".

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

#### <a name="filter-out-fast-remote-dependency-calls"></a>Odfiltrovat rychlá volání vzdálených závislostí

Pokud chcete diagnostikovat pouze pomalé hovory, odfiltrujte ty rychlé.

> [!NOTE]
> Tím se zkreslují statistiky, které vidíte na portálu.
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

#### <a name="diagnose-dependency-issues"></a>Diagnostikovat problémy se závislostmi

[Tento blog](https://azure.microsoft.com/blog/implement-an-application-insights-telemetry-processor/) popisuje projekt diagnostikovat problémy se závislostmi automatickým odesíláním pravidelných pingů závislostem.

<a name="add-properties"></a>

### <a name="javascript-web-applications"></a>Webové aplikace JavaScript

**Filtrování pomocí ITelemetryInitializer**

1. Vytvořte funkci zpětného volání telemetrické inicializátoru. Funkce zpětného `ITelemetryItem` volání trvá jako parametr, což je událost, která je zpracovávána. Vrácení `false` z tohoto zpětného volání má za následek položku telemetrie, která má být odfiltrována.  

   ```JS
   var filteringFunction = (envelope) => {
     if (envelope.data.someField === 'tobefilteredout') {
        return false;
     }
  
     return true;
   };
   ```

2. Přidejte zpětné volání inicializačního zařízení telemetrie:

   ```JS
   appInsights.addTelemetryInitializer(filteringFunction);
   ```

## <a name="addmodify-properties-itelemetryinitializer"></a>Přidat nebo upravit vlastnosti: ITelemetryInitializer


Pomocí inicializačních inicializátorů telemetrie obohaťte telemetrii o další informace nebo k přepsání vlastností telemetrie nastavených standardními moduly telemetrie.

Například application insights for Web balíček shromažďovat telemetrická data o požadavcích HTTP. Ve výchozím nastavení označí jako neúspěšný jakýkoli požadavek s kódem odpovědi >= 400. Ale pokud chcete považovat 400 jako úspěch, můžete poskytnout telemetrickou inicializátor, který nastaví Success vlastnost.

Pokud zadáte inicializátor telemetrie, je volána vždy, když jsou volány některé metody Track*(). To `Track()` zahrnuje metody volané standardní telemetrické moduly. Podle konvence tyto moduly nenastavují žádnou vlastnost, která již byla nastavena inicializátorem. Telemetrické inicializátory jsou volány před voláním telemetrických procesorů. Takže jakékoli obohacení provedené inicializátory jsou viditelné pro procesory.

**Definujte inicializátor**

*C #*

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

**ASP.NET aplikace: Načtěte inicializátor**

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

*Případně* můžete vytvořit inkalizační inicializátor v kódu, například v Global.aspx.cs:

```csharp
protected void Application_Start()
{
    // ...
    TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
}
```

[Podívejte se na další ukázku.](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole)

**ASP.NET aplikace Core/ Worker Service: Načtěte inicializátor**

> [!NOTE]
> Přidání inicializátoru pomocí `ApplicationInsights.config` nebo použití `TelemetryConfiguration.Active` není platné pro ASP.NET základní aplikace nebo pokud používáte Microsoft.ApplicationInsights.WorkerService SDK.

Pro aplikace napsané pomocí [ASP.NET Core](asp-net-core.md#adding-telemetryinitializers) `TelemetryInitializer` nebo [WorkerService](worker-service.md#adding-telemetryinitializers), přidání nového se provádí přidáním do kontejneru vkládání závislostí, jak je znázorněno níže. To se `Startup.ConfigureServices` provádí metodou.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

### <a name="java-telemetry-initializers"></a>Inicializátory telemetrie Java

[Dokumentace sady Java SDK](https://docs.microsoft.com/java/api/com.microsoft.applicationinsights.extensibility.telemetryinitializer?view=azure-java-stable)

```Java
public interface TelemetryInitializer
{ /** Initializes properties of the specified object. * @param telemetry The {@link com.microsoft.applicationinsights.telemetry.Telemetry} to initialize. */

void initialize(Telemetry telemetry); }
```

Potom zaregistrujte vlastní inicializátor v souboru applicationinsights.xml.

```xml
<Add type="mypackage.MyConfigurableContextInitializer">
    <Param name="some_config_property" value="some_value" />
</Add>
```

### <a name="javascript-telemetry-initializers"></a>JavaScript telemetrické inicializátory
*Javascript*

Vložte inicializátor telemetrie ihned po inicializačníkód, který jste získali z portálu:

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

    appInsights.queue.push(function () {
        appInsights.context.addTelemetryInitializer(function (envelope) {
            var telemetryItem = envelope.data.baseData;

            // To check the telemetry items type - for example PageView:
            if (envelope.name == Microsoft.ApplicationInsights.Telemetry.PageView.envelopeType) {
                // this statement removes url from all page view documents
                telemetryItem.url = "URL CENSORED";
            }

            // To set custom properties:
            telemetryItem.properties = telemetryItem.properties || {};
            telemetryItem.properties["globalProperty"] = "boo";

            // To set custom metrics:
            telemetryItem.measurements = telemetryItem.measurements || {};
            telemetryItem.measurements["globalMetric"] = 100;
        });
    });

    // End of inserted code.

    appInsights.trackPageView();
</script>
```

Souhrn nevlastních vlastností dostupných v telemetrické položce naleznete v tématu [Application Insights Export Data Model](../../azure-monitor/app/export-data-model.md).

Můžete přidat libovolný počet inicializátorů a jsou volány v pořadí, v jakém jsou přidány.

### <a name="opencensus-python-telemetry-processors"></a>OpenCensus Python telemetrické procesory

Telemetrické procesory v OpenCensus Python jsou jednoduše funkce zpětného volání volané ke zpracování telemetrie před jejich exportem. Funkce zpětného volání musí jako svůj parametr přijmout datový typ [obálky.](https://github.com/census-instrumentation/opencensus-python/blob/master/contrib/opencensus-ext-azure/opencensus/ext/azure/common/protocol.py#L86) Chcete-li odfiltrovat telemetrii z exportu, ujistěte se, že funkce zpětného volání vrátí `False`. Schéma pro datové typy Azure Monitoru najdete v obálkách [zde](https://github.com/census-instrumentation/opencensus-python/blob/master/contrib/opencensus-ext-azure/opencensus/ext/azure/common/protocol.py).

> [!NOTE]
> Můžete upravit `cloud_RoleName` změnou `ai.cloud.role` atributu `tags` v poli.

```python
def callback_function(envelope):
    envelope.tags['ai.cloud.role'] = 'new_role_name.py'
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
Můžete přidat libovolný počet procesorů a jsou volány v pořadí, v jakém jsou přidány. Pokud jeden procesor by měl vyvolat výjimku, nemá vliv na následující procesory.

### <a name="example-telemetryinitializers"></a>Příklad telemetriii inicializátorů

#### <a name="add-custom-property"></a>Přidat vlastní vlastnost

Následující ukázkový inicializátor přidá vlastní vlastnost ke každé sledované telemetrii.

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

#### <a name="add-cloud-role-name"></a>Přidání názvu role cloudu

Následující ukázkový inicializátor nastaví název role cloudu na každou sondou telemetrii.

```csharp
public void Initialize(ITelemetry telemetry)
{
    if(string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
    {
        telemetry.Context.Cloud.RoleName = "MyCloudRoleName";
    }
}
```

## <a name="itelemetryprocessor-and-itelemetryinitializer"></a>ITelemetryProcesor a ITelemetryInitializer

Jaký je rozdíl mezi telemetrickými procesory a inicializátory telemetrie?

* Existují některé překrývání v tom, co můžete dělat s nimi: oba lze přidat nebo upravit vlastnosti telemetrie, i když se doporučuje použít inicializátory pro tento účel.
* TelemetrieInitializers vždy spustit před TelemetryProcesors.
* TelemetrieInitializers může být volána více než jednou. Podle konvence nenastavují žádnou vlastnost, která již byla nastavena.
* TelemetryProcesory umožňují zcela nahradit nebo zahodit položku telemetrie.
* Všechny registrované telemetrieInitializers je zaručeno, že bude volána pro každou položku telemetrie. Pro telemetrické procesory Sada SDK zaručuje volání prvního telemetrického procesoru. Zda ostatní procesory jsou volány nebo ne, je rozhodnuto předchozí telemetrické procesory.
* Pomocí telemetrieInitializers obohatit telemetrie s další vlastnosti nebo přepsat existující jeden. Pomocí telemetrického procesoru odfiltrujte telemetrii.

## <a name="troubleshooting-applicationinsightsconfig"></a>Poradce při potížích s souborem ApplicationInsights.config

* Zkontrolujte, zda je úplný název typu a název sestavení správný.
* Zkontrolujte, zda je soubor applicationinsights.config ve výstupním adresáři a obsahuje všechny nedávné změny.

## <a name="reference-docs"></a>Referenční dokumenty

* [Přehled rozhraní API](../../azure-monitor/app/api-custom-events-metrics.md)
* [ASP.NET odkaz](https://msdn.microsoft.com/library/dn817570.aspx)

## <a name="sdk-code"></a>Kód sady SDK

* [Sada ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [ASP.NET SDK](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)

## <a name="next-steps"></a><a name="next"></a>Další kroky
* [Hledání událostí a protokolů](../../azure-monitor/app/diagnostic-search.md)
* [Vzorkování](../../azure-monitor/app/sampling.md)
* [Řešení potíží](../../azure-monitor/app/troubleshoot-faq.md)
