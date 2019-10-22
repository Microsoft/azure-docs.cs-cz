---
title: Filtrování a předzpracování v sadě Azure Application Insights SDK | Microsoft Docs
description: Zapište procesory telemetrie a Inicializátory telemetrie pro sadu SDK, aby bylo možné filtrovat nebo přidat vlastnosti dat před odesláním telemetrie na Application Insights portál.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 11/23/2016
ms.openlocfilehash: 1e02e227180bb0082dd87ab8f5d2fe64e19b60f2
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2019
ms.locfileid: "72677809"
---
# <a name="filtering-and-preprocessing-telemetry-in-the-application-insights-sdk"></a>Filtrování a předzpracování telemetrie v sadě Application Insights SDK

Můžete napsat a nakonfigurovat moduly plug-in pro sadu Application Insights SDK, abyste mohli přizpůsobit, jak může být telemetrie obohacena a zpracována před odesláním do služby Application Insights.

* [Vzorkování](sampling.md) snižuje objem telemetrie, aniž by to ovlivnilo vaše statistiky. Udržuje spolu se souvisejícími datovými body, takže můžete mezi nimi přecházet při diagnostice problému. V portálu se celkový počet vynásobí, aby se vzorkování vyrovnalo.
* Filtrování pomocí procesorů telemetrie vám umožňuje vyfiltrovat telemetrie v sadě SDK předtím, než se pošle na server. Můžete například snížit objem telemetrie vyloučením požadavků z robotů. Filtrování je obecnější přístup k omezení provozu než vzorkování. Umožňuje vám lepší kontrolu nad tím, co se přenáší, ale je nutné mít na paměti, že má vliv na vaše statistiky – například pokud filtrujete všechny úspěšné požadavky.
* [Inicializátory telemetrie přidávají nebo upravují vlastnosti](#add-properties) jakékoli telemetrie odeslané z vaší aplikace, včetně telemetrie ze standardních modulů. Můžete například přidat počítané hodnoty; nebo čísla verzí, podle kterých se mají data filtrovat na portálu.
* [Rozhraní API sady SDK](../../azure-monitor/app/api-custom-events-metrics.md) slouží k posílání vlastních událostí a metrik.

Než začnete, potřebujete:

* Nainstalujte příslušnou sadu SDK pro vaši aplikaci: [ASP.NET](asp-net.md), [ASP.NET Core](asp-net-core.md), [non http/Worker pro .NET/.NET Core](worker-service.md)nebo [Java](../../azure-monitor/app/java-get-started.md).

<a name="filtering"></a>

## <a name="filtering-itelemetryprocessor"></a>Filtrování: ITelemetryProcessor

Tento postup vám poskytne přímou kontrolu nad tím, co je zahrnuto nebo vyloučeno z datového proudu telemetrie. Filtrování lze použít k vyřazení položek telemetrie z odeslání do Application Insights. Můžete ji použít ve spojení s vzorkováním nebo samostatně.

Pokud chcete vyfiltrovat telemetrii, napíšete procesor telemetrie a zaregistrujete ho pomocí `TelemetryConfiguration`. Veškerá telemetrie projde procesorem a Vy se můžete rozhodnout, že ho z datového proudu vyřadíte nebo ho přiřadíte k dalšímu procesoru v řetězu. To zahrnuje telemetrii ze standardních modulů, jako jsou kolekce požadavků HTTP a kolektor závislostí, a telemetrii, kterou jste si sami sledovali. Můžete například odfiltrovat telemetrii o požadavcích z robotů nebo úspěšných volání závislostí.

> [!WARNING]
> Filtrování telemetrie odesílané ze sady SDK pomocí procesorů může zkosit statistiky, které vidíte na portálu, a obtížně sledovat související položky.
>
> Místo toho zvažte použití [vzorkování](../../azure-monitor/app/sampling.md).
>
>

### <a name="create-a-telemetry-processor-c"></a>Vytvoření procesoru telemetrie (C#)

1. Chcete-li vytvořit filtr, implementujte `ITelemetryProcessor`.

    Všimněte si, že procesory telemetrie vytvoří řetězec zpracování. Při vytváření instance procesoru telemetrie budete mít odkaz na další procesor v řetězu. Když je do metody procesu předán datový bod telemetrie, provede svoji práci a potom volá (nebo nevolá) další procesor telemetrie v řetězu.

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

**Aplikace ASP.NET** Vložit tento fragment kódu do souboru ApplicationInsights. config:

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
> Pečlivě se ujistěte, že název typu a všechny názvy vlastností v souboru. config se shodují s názvy tříd a vlastností v kódu. Pokud soubor. config odkazuje na neexistující typ nebo vlastnost, může dojít k tiché chybě při odesílání jakékoli telemetrie.
>

**Alternativně** můžete inicializovat filtr v kódu. Do vhodné inicializační třídy – například AppStart v `Global.asax.cs` – vložte procesor do řetězce:

```csharp
var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
builder.Use((next) => new SuccessfulDependencyFilter(next));

// If you have more processors:
builder.Use((next) => new AnotherProcessor(next));

builder.Build();
```

TelemetryClients vytvořené po tomto okamžiku budou používat vaše procesory.

**Aplikace ASP.NET Core/pracovní služby**

> [!NOTE]
> Přidání procesoru pomocí `ApplicationInsights.config` nebo použití `TelemetryConfiguration.Active` není platné pro ASP.NET Core aplikace nebo pokud používáte sadu Microsoft. ApplicationInsights. WorkerService SDK.

V případě aplikací napsaných pomocí [ASP.NET Core](asp-net-core.md#adding-telemetry-processors) nebo [WorkerService](worker-service.md#adding-telemetry-processors)je přidání nového `TelemetryProcessor` provedeno pomocí metody rozšíření `AddApplicationInsightsTelemetryProcessor` v `IServiceCollection`, jak je znázorněno níže. Tato metoda se volá v metodě `ConfigureServices` vaší třídy `Startup.cs`.

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

Pokud chcete jenom diagnostikovat volání, která jsou pomalá, vyfiltrujte je rychleji.

> [!NOTE]
> Tato akce zkosí statistiky, které vidíte na portálu.
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

#### <a name="diagnose-dependency-issues"></a>Diagnóza problémů se závislostí

[Tento blog](https://azure.microsoft.com/blog/implement-an-application-insights-telemetry-processor/) popisuje projekt pro diagnostiku problémů se závislostmi tím, že automaticky posílá standardní příkazy pro odesílání do závislostí.

<a name="add-properties"></a>

## <a name="add-properties-itelemetryinitializer"></a>Přidat vlastnosti: ITelemetryInitializer

Použijte Inicializátory telemetrie k obohacení telemetrie o další informace a/nebo k přepsání vlastností telemetrie nastavených standardními moduly telemetrie.

Například Application Insights pro webový balíček shromažďuje telemetrii o požadavcích HTTP. Ve výchozím nastavení se označí jako neúspěšné všechny žádosti s kódem odpovědi > = 400. Pokud ale chcete považovat 400 za úspěch, můžete poskytnout inicializátor telemetrie, který nastaví vlastnost success.

Pokud zadáte inicializátor telemetrie, je volána při každém volání jakékoli metody Track * (). To zahrnuje metody `Track()` volané standardními moduly telemetrie. Podle konvence tyto moduly nenastaví žádnou vlastnost, která již byla nastavena inicializátorem. Inicializátory telemetrie se volají před voláním procesorů telemetrie. Takže jakákoli rozšíření prováděná Inicializátory jsou viditelná pro procesory.

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

V souboru ApplicationInsights. config:

```xml
<ApplicationInsights>
  <TelemetryInitializers>
    <!-- Fully qualified type name, assembly name: -->
    <Add Type="MvcWebRole.Telemetry.MyTelemetryInitializer, MvcWebRole"/>
    ...
  </TelemetryInitializers>
</ApplicationInsights>
```

*Alternativně* můžete vytvořit instanci inicializátoru v kódu, například v Global.aspx.cs:

```csharp
protected void Application_Start()
{
    // ...
    TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
}
```

[Další informace najdete v této ukázce.](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole)

**ASP.NET Core/aplikace služby pracovního procesu: načíst inicializátor**

> [!NOTE]
> Přidání inicializátoru pomocí `ApplicationInsights.config` nebo pomocí `TelemetryConfiguration.Active` není platné pro ASP.NET Core aplikace nebo pokud používáte sadu Microsoft. ApplicationInsights. WorkerService SDK.

U aplikací napsaných pomocí [ASP.NET Core](asp-net-core.md#adding-telemetryinitializers) nebo [WorkerService](worker-service.md#adding-telemetryinitializers)se přidání nového `TelemetryInitializer` provede přidáním do kontejneru vkládání závislostí, jak je znázorněno níže. To se provádí v metodě `Startup.ConfigureServices`.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

### <a name="java-telemetry-initializers"></a>Inicializátory telemetrie Java

[Dokumentace k sadě Java SDK](https://docs.microsoft.com/java/api/com.microsoft.applicationinsights.extensibility.telemetryinitializer?view=azure-java-stable)

```Java
public interface TelemetryInitializer
{ /** Initializes properties of the specified object. * @param telemetry The {@link com.microsoft.applicationinsights.telemetry.Telemetry} to initialize. */

void initialize(Telemetry telemetry); }
```

Pak zaregistrujte vlastní inicializátor v souboru ApplicationInsights. XML.

```xml
<Add type="mypackage.MyConfigurableContextInitializer">
    <Param name="some_config_property" value="some_value" />
</Add>
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

Shrnutí nevlastních vlastností dostupných v telemetryItem najdete v tématu [Export datového modelu Application Insights](../../azure-monitor/app/export-data-model.md).

Můžete přidat tolik inicializátorů, kolik chcete, a jsou volány v pořadí, ve kterém jsou přidány.

### <a name="example-telemetryinitializers"></a>Příklad TelemetryInitializers

#### <a name="add-custom-property"></a>Přidat vlastní vlastnost

Následující ukázkový inicializátor přidá do každé sledované telemetrie vlastní vlastnost.

```csharp
public void Initialize(ITelemetry item)
{
  var itemProperties = item as ISupportProperties;
  if(itemProperties != null && !itemProperties.ContainsKey("customProp"))
    {
        itemProperties.Properties["customProp"] = "customValue";
    }
}
```

#### <a name="add-cloud-role-name"></a>Přidat název cloudové role

Následující vzorový inicializátor nastaví název cloudové role na každou sledovanou telemetrii.

```csharp
public void Initialize(ITelemetry telemetry)
{
    if(string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
    {
        telemetry.Context.Cloud.RoleName = "MyCloudRoleName";
    }
}
```

## <a name="itelemetryprocessor-and-itelemetryinitializer"></a>ITelemetryProcessor a ITelemetryInitializer

Jaký je rozdíl mezi procesory telemetrie a Inicializátory telemetrie?

* Existuje několik překrytí v tom, co můžete s nimi dělat: obě lze použít k přidání nebo úpravě vlastností telemetrie, i když se pro tento účel doporučuje použít inicializátory.
* TelemetryInitializers vždy spustit před TelemetryProcessors.
* TelemetryInitializers může být volána více než jednou. Podle konvence nenastaví žádnou vlastnost, která již byla nastavena.
* TelemetryProcessors umožňuje úplně nahradit nebo zrušit položku telemetrie.
* Všem registrovaným TelemetryInitializers je zaručeno, že budou volány pro každou položku telemetrie. Pro procesory telemetrie sada SDK garantuje volání velmi prvního procesoru telemetrie. Bez ohledu na to, zda jsou ostatní procesory volány nebo nikoli, je určeno předchozími procesory telemetrie.
* Pomocí TelemetryInitializers můžete obohacení telemetrie s dalšími vlastnostmi nebo přepsat existující. Vyfiltrujte telemetrii pomocí TelemetryProcessor.

## <a name="troubleshooting-applicationinsightsconfig"></a>Řešení potíží s ApplicationInsights. config

* Potvrďte, že plně kvalifikovaný název typu a název sestavení jsou správné.
* Zkontrolujte, zda je soubor ApplicationInsights. config ve výstupním adresáři a zda obsahuje nedávné změny.

## <a name="reference-docs"></a>Referenční dokumenty

* [Přehled rozhraní API](../../azure-monitor/app/api-custom-events-metrics.md)
* [Odkaz na ASP.NET](https://msdn.microsoft.com/library/dn817570.aspx)

## <a name="sdk-code"></a>Kód sady SDK

* [Sada ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [SADA ASP.NET SDK](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)

## <a name="next"></a>Další kroky
* [Hledat události a protokoly](../../azure-monitor/app/diagnostic-search.md)
* [Vzorkování](../../azure-monitor/app/sampling.md)
* [Řešení potíží](../../azure-monitor/app/troubleshoot-faq.md)
