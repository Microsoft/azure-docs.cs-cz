---
title: Filtrování a předběžného zpracování v Azure Application Insights SDK | Microsoft Docs
description: Zápis procesorů Telemetrie a inicializátory telemetrická data sady SDK, filtrovat nebo přidání vlastnosti do data před odesláním telemetrie na portál Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 38a9e454-43d5-4dba-a0f0-bd7cd75fb97b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 11/23/2016
ms.author: mbullwin; borooji
ms.openlocfilehash: 06f116fc5096fe4bda9c8433c3a33726acb67eea
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="filtering-and-preprocessing-telemetry-in-the-application-insights-sdk"></a>Filtrování a předběžného zpracování telemetrie Application Insights SDK


Můžete napsat a konfigurovat moduly plug-in pro Application Insights SDK pro přizpůsobení jak telemetrie bude zachycen a před odesláním do služby Application Insights.

* [Vzorkování](app-insights-sampling.md) snižuje telemetrie, aniž by to ovlivnilo vaše statistiky. Udržuje společně související datových bodů tak, aby můžete procházet mezi nimi při diagnostikování problému. Celkové počty jsou na portálu, násobí odpovídajícím způsobem pro odběr.
* Filtrování s procesory Telemetrie [pro technologii ASP.NET](#filtering) nebo [Java](app-insights-java-filter-telemetry.md) umožňuje vybrat nebo upravit telemetrie sady SDK před odesláním na server. Například může snížit objem telemetrie tak, že vyloučíte z robotů požadavky. Ale filtrování se více základní postup pro omezení provozu než vzorkování. Umožňuje větší kontrolu nad co se přenášejí, ale máte potřeba mít na paměti, že ovlivňuje statistice – například pokud můžete filtrovat všechny úspěšné požadavky.
* [Inicializátory telemetrie přidávat vlastnosti](#add-properties) pro všechny telemetrická data odesílaná z vaší aplikace, včetně telemetrie z standardní moduly. Například můžete přidat vypočítané hodnoty; nebo čísla verzí, podle kterého chcete filtrovat data v portálu.
* [Rozhraní API sady SDK](app-insights-api-custom-events-metrics.md) se používá k odeslání vlastní události a metriky.

Než začnete, potřebujete:

* Nainstalujte službu Application Insights [SDK pro technologii ASP.NET](app-insights-asp-net.md) nebo [SDK pro jazyk Java](app-insights-java-get-started.md) ve vaší aplikaci.

<a name="filtering"></a>

## <a name="filtering-itelemetryprocessor"></a>Filtrování: ITelemetryProcessor
Tento postup vám dává větší kontrolu nad co je zahrnout nebo vyloučit z datový proud telemetrie. Můžete jej použít ve spojení s vzorkování, nebo samostatně.

Pro filtrování telemetrie, zápis procesor telemetrie a zaregistrovat ji pomocí sady SDK. Všechny telemetrická prochází procesor a můžete ho vyřadit z datového proudu, nebo přidejte vlastnosti. To zahrnuje telemetrie z standardní moduly, například kolekce požadavku HTTP a collector závislost, a také telemetrických dat, který jste napsali sami. Můžete například odfiltrovat telemetrická data týkající se požadavků z robotů nebo volání úspěšné závislostí.

> [!WARNING]
> Filtrování telemetrická data odesílaná ze sady SDK pomocí procesorů můžete zkreslit statistiky, které se zobrazí na portálu a je obtížné sledovat související položky.
>
> Místo toho zvažte použití [vzorkování](app-insights-sampling.md).
>
>

### <a name="create-a-telemetry-processor-c"></a>Vytvoření telemetrie procesoru (C#)
1. Ověřte, zda je ve vašem projektu Application Insights SDK verze 2.0.0 nebo novější. Klikněte pravým tlačítkem na projekt v Průzkumníku řešení Visual Studio a zvolte spravovat balíčky NuGet. V Správce balíčků NuGet zkontrolujte Microsoft.ApplicationInsights.Web.
2. Chcete-li vytvořit filtr, implementujte ITelemetryProcessor. Toto je jiný bod rozšiřitelnost jako modul telemetrie, inicializátoru telemetrie a telemetrie kanál.

    Všimněte si, že Telemetrie procesory vytvořit řetěz zpracování. Instanci můžete vytvořit procesor telemetrie, předáte odkaz na další procesoru v řetězu. Když je bod data telemetrie předaný metodě proces, nemá svou práci a pak zavolá další procesor Telemetrie v řetězu.

    ``` C#

    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.Extensibility;

    public class SuccessfulDependencyFilter : ITelemetryProcessor
      {

        private ITelemetryProcessor Next { get; set; }

        // You can pass values from .config
        public string MyParamFromConfigFile { get; set; }

        // Link processors to each other in a chain.
        public SuccessfulDependencyFilter(ITelemetryProcessor next)
        {
            this.Next = next;
        }
        public void Process(ITelemetry item)
        {
            // To filter out an item, just return
            if (!OKtoSend(item)) { return; }
            // Modify the item if required
            ModifyItem(item);

            this.Next.Process(item);
        }

        // Example: replace with your own criteria.
        private bool OKtoSend (ITelemetry item)
        {
            var dependency = item as DependencyTelemetry;
            if (dependency == null) return true;

            return dependency.Success != true;
        }

        // Example: replace with your own modifiers.
        private void ModifyItem (ITelemetry item)
        {
            item.Context.Properties.Add("app-version", "1." + MyParamFromConfigFile);
        }
    }

    ```
1. Vložte tuto položku v souboru ApplicationInsights.config:

```XML

    <TelemetryProcessors>
      <Add Type="WebApplication9.SuccessfulDependencyFilter, WebApplication9">
         <!-- Set public property -->
         <MyParamFromConfigFile>2-beta</MyParamFromConfigFile>
      </Add>
    </TelemetryProcessors>

```

(To je do stejné části, kde inicializovat vzorkování filtr.)

Můžete předat hodnoty řetězce ze souboru .config tím, že poskytuje veřejné s názvem vlastnosti v třídě.

> [!WARNING]
> Vezměte v potaz tak, aby odpovídaly název typu a všechny názvy vlastností v souboru config názvy třídy a vlastnosti v kódu. Pokud soubor .config odkazuje na neexistující typ nebo vlastnost, sada SDK může bez upozornění nepodaří odesílat všechny telemetrická data.
>
>

**Alternativně** můžete inicializovat filtru v kódu. Ve třídě vhodný inicializace – například AppStart v Global.asax.cs - vložte do řetězu procesor:

```csharp

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;
    builder.Use((next) => new SuccessfulDependencyFilter(next));

    // If you have more processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

TelemetryClients vytvořené po tomto bodu použije vaše procesory.

Následující kód ukazuje, jak přidat inicializátoru telemetrie do ASP.NET Core.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    var initializer = new SuccessfulDependencyFilter();
    var configuration = app.ApplicationServices.GetService<TelemetryConfiguration>();
    configuration.TelemetryInitializers.Add(initializer);
}
```

### <a name="example-filters"></a>Příklad filtrů
#### <a name="synthetic-requests"></a>Syntetické požadavků
Filtrovat robotů a webové testy. I když Průzkumníku metrik poskytuje možnost filtrovat syntetické zdroje, tato možnost snižuje zatížení filtrování je v sadě SDK.

``` C#

    public void Process(ITelemetry item)
    {
      if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource)) {return;}

      // Send everything else:
      this.Next.Process(item);
    }

```

#### <a name="failed-authentication"></a>Ověřování se nezdařilo
Filtrování požadavků s odpovědi "401".

```csharp

public void Process(ITelemetry item)
{
    var request = item as RequestTelemetry;

    if (request != null &&
    request.ResponseCode.Equals("401", StringComparison.OrdinalIgnoreCase))
    {
        // To filter out an item, just terminate the chain:
        return;
    }
    // Send everything else:
    this.Next.Process(item);
}

```

#### <a name="filter-out-fast-remote-dependency-calls"></a>Odfiltrovat rychlé vzdálené závislostí volání
Pokud chcete diagnostikovat volání, které jsou pomalé, odfiltrovat ty fast.

> [!NOTE]
> To bude zkreslit statistiky, které vidíte na portálu. Graf závislostí bude vypadat jako, pokud jsou všechny chyby při volání závislostí.
>
>

``` C#

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
[Tomto blogu](https://azure.microsoft.com/blog/implement-an-application-insights-telemetry-processor/) popisuje projekt k vyřešení problémů se závislostí automaticky odesláním regulární příkazy ping závislosti.


<a name="add-properties"></a>

## <a name="add-properties-itelemetryinitializer"></a>Přidat vlastnosti: ITelemetryInitializer
Inicializátory telemetrická data použít k definování globální vlastnosti, které se odesílají s všechny telemetrická; a přepsání vybrané chování modulů standardní telemetrie.

Například Application Insights pro webové balíček shromažďuje telemetrická data o požadavcích HTTP. Ve výchozím nastavení, označuje jako neúspěšný každá žádost s kódem odpovědi > = 400. Ale pokud budete chtít 400 považovat za úspěšné, můžete zadat inicializátoru telemetrická data, která nastaví vlastnost úspěch.

Pokud zadáte inicializátoru telemetrie, nazývá se vždy, když se nazývají metod Track*(). To zahrnuje metody, které jsou volány moduly standardní telemetrie. Podle konvence nenastavujte tyto moduly jakákoli vlastnost, která již byla nastavena pomocí inicializátoru.

**Zadejte vaše inicializátoru**

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
                requestTelemetry.Context.Properties["Overridden400s"] = "true";
            }
            // else leave the SDK to set the Success property      
        }
      }
    }
```

**Načíst vaše inicializátoru**

In ApplicationInsights.config:

    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="MvcWebRole.Telemetry.MyTelemetryInitializer, MvcWebRole"/>
        ...
      </TelemetryInitializers>
    </ApplicationInsights>

*Alternativně* můžete vytvořit instanci inicializátoru v kódu, například v souboru Global.aspx.cs:

```csharp
    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
        .Add(new MyTelemetryInitializer());
    }
```


[Další informace naleznete v této ukázky.](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole)

<a name="js-initializer"></a>

### <a name="javascript-telemetry-initializers"></a>Inicializátory telemetrie JavaScript
*JavaScript*

Vložte inicializátoru telemetrie hned po inicializaci kód, který jste získali z portálu:

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

                // To check the telemetry item’s type - for example PageView:
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

Souhrn k dispozici na telemetryItem bez vlastní vlastnosti, najdete v části [Application Insights Exportovat datový Model](app-insights-export-data-model.md).

Můžete přidat libovolný počet inicializátory, jak se vám líbí.

## <a name="itelemetryprocessor-and-itelemetryinitializer"></a>ITelemetryProcessor a ITelemetryInitializer
Jaký je rozdíl mezi procesory telemetrie a inicializátory telemetrie?

* Existují některé překrytí v co můžete dělat s nimi: jak lze použít k přidání vlastností do telemetrie.
* Vždy spustit před TelemetryProcessors TelemetryInitializers.
* TelemetryProcessors umožňují zcela nahradit nebo zahodit položku telemetrie.
* TelemetryProcessors nemáte zpracovat telemetrická data čítače výkonu.

## <a name="troubleshooting-applicationinsightsconfig"></a>Řešení potíží s souboru ApplicationInsights.config
* Potvrďte správnost plně kvalifikovaný název typu a název sestavení.
* Potvrďte, že soubor applicationinsights.config je ve vašem adresáři výstup a obsahuje všechny poslední změny.

## <a name="reference-docs"></a>Referenční dokumenty
* [Přehled rozhraní API](app-insights-api-custom-events-metrics.md)
* [Rozhraní ASP.NET – reference](https://msdn.microsoft.com/library/dn817570.aspx)

## <a name="sdk-code"></a>Kód SDK
* [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [SADY SDK TECHNOLOGIE ASP.NET](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)

## <a name="next"></a>Další kroky
* [Hledání událostí a protokolů](app-insights-diagnostic-search.md)
* [Vzorkování](app-insights-sampling.md)
* [Řešení potíží](app-insights-troubleshoot-faq.md)
