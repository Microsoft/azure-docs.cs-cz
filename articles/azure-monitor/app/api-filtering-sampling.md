---
title: Filtrování a předběžné zpracování v Azure Application Insights SDK | Dokumentace Microsoftu
description: Zápis Telemetrie procesory a inicializátory telemetrická data sady SDK filtrovat nebo přidání vlastnosti do data před odesláním telemetrie na portál Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 38a9e454-43d5-4dba-a0f0-bd7cd75fb97b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 11/23/2016
ms.author: mbullwin
ms.openlocfilehash: 1b55a2b053b86d3260fdca201357445d2556c444
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2019
ms.locfileid: "54199030"
---
# <a name="filtering-and-preprocessing-telemetry-in-the-application-insights-sdk"></a>Filtrování a předběžné zpracování telemetrie v Application Insights SDK


Můžete napsat a nakonfigurovat moduly plug-in pro Application Insights SDK pro přizpůsobení fungování telemetrická data zachycená a zpracovat před odesláním do služby Application Insights.

* [Vzorkování](../../azure-monitor/app/sampling.md) snižuje objem telemetrických dat bez ovlivnění vašich statistik. Uchová společně souvisejících datových bodů tak, aby můžete procházet mezi nimi při diagnostikování problému. Na portálu jsou celkového počtu vynásobené jako kompenzaci za vzorkování.
* Filtrování Telemetrie procesorů [pro technologii ASP.NET](#filtering) nebo [Java](../../azure-monitor/app/java-filter-telemetry.md) umožňuje vybrat nebo změnit telemetrie v sadě SDK, před odesláním do serveru. Například může snížit objem telemetrických dat vyloučením požadavky od robotů. Ale filtrování je jednodušší přístup k snižování zatížení než vzorkování. Umožňuje větší kontrolu nad co se přenášejí, ale budete muset mějte na paměti, že má vliv statistik – například pokud odfiltrovat všechny úspěšné požadavky.
* [Inicializátory telemetrie přidat vlastnosti](#add-properties) na všechny telemetrická data odesílaná z vaší aplikace, včetně telemetrie z standardní moduly. Například můžete přidat počítané hodnoty. nebo čísla verzí, podle kterého chcete filtrovat data na portálu.
* [Rozhraní API sady SDK](../../azure-monitor/app/api-custom-events-metrics.md) se používá k odesílání vlastních událostí a metrik.

Než začnete, potřebujete:

* Nainstalujte službu Application Insights [sady SDK pro ASP.NET](../../azure-monitor/app/asp-net.md) nebo [sadu SDK pro Javu](../../azure-monitor/app/java-get-started.md) ve vaší aplikaci.

<a name="filtering"></a>

## <a name="filtering-itelemetryprocessor"></a>Filtrování: ITelemetryProcessor
Tato technika umožňuje více přímou kontrolu nad co je zahrnuty nebo vyloučeny ze datový proud telemetrie. Můžete ho použít ve spojení s povoleným vzorkováním, nebo samostatně.

Filtrovat telemetrii, zápisu telemetrických dat procesoru a zaregistrujte ho pomocí sady SDK. Veškerá telemetrie prochází procesor a můžete ho vyřadit z datového proudu, nebo přidejte vlastnosti. To zahrnuje telemetrie z standardní moduly, například kolekcí požadavku HTTP a závislosti kolekcí a také telemetrie, které jste sami napsali. Můžete například odfiltrovat telemetrická data týkající se požadavků z roboti nebo volání úspěšné závislostí.

> [!WARNING]
> Filtrování telemetrická data odesílaná ze sady SDK pomocí procesorů můžete zkosení statistické údaje, které se zobrazí na portálu a je obtížné sledovat související položky.
>
> Místo toho zvažte použití [vzorkování](../../azure-monitor/app/sampling.md).
>
>

### <a name="create-a-telemetry-processor-c"></a>Vytvoření telemetrie procesoru (C#)
1. Ověřte, zda Application Insights SDK do projektu je verze 2.0.0 nebo novější. Klikněte pravým tlačítkem na projekt v Průzkumníku řešení sady Visual Studio a zvolte spravovat balíčky NuGet. Ve správci balíčků NuGet zkontrolujte Microsoft.ApplicationInsights.Web.
2. Chcete-li vytvořit filtr, implementujte ITelemetryProcessor. Toto je jiný bod rozšíření jako modul telemetrie inicializátor telemetrie a telemetrická data kanálu.

    Všimněte si, že Telemetrie procesory sestavit řetězec zpracování. Při vytváření instance procesoru telemetrie, předáte odkaz do další procesor v řetězci. Když bod telemetrická data se předá metodě procesu, provede svou práci a pak zavolá další procesor Telemetrie v řetězci.

    ```csharp

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
3. V souboru ApplicationInsights.config, vložte toto:

```xml

    <TelemetryProcessors>
      <Add Type="WebApplication9.SuccessfulDependencyFilter, WebApplication9">
         <!-- Set public property -->
         <MyParamFromConfigFile>2-beta</MyParamFromConfigFile>
      </Add>
    </TelemetryProcessors>

```

(To je stejné části, kde je inicializovat filtr vzorkování.)

Řetězcové hodnoty ze souboru .config můžete předat tím, že poskytuje veřejné pojmenované vlastnosti ve své třídě.

> [!WARNING]
> Aby se postaral tak, aby odpovídaly název typu a všechny názvy vlastností v souboru .config na názvy tříd a vlastností v kódu. Pokud soubor .config odkazuje na neexistující typu nebo vlastnost, sada SDK může bez upozornění nepodaří odeslat žádnou telemetrii.
>
>

**Alternativně** můžete inicializovat filtru v kódu. Ve třídě vhodný inicializace – například AppStart v Global.asax.cs – vložte do řetězce procesoru:

```csharp

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;
    builder.Use((next) => new SuccessfulDependencyFilter(next));

    // If you have more processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

TelemetryClients vytvořili od této chvíle budou používat procesorů.

### <a name="example-filters"></a>Příklad filtrů
#### <a name="synthetic-requests"></a>Umělé požadavky
Vyfiltrování roboty a webové testy. Ačkoli Průzkumník metrik dává možnost filtrování syntetické zdrojů, tato možnost snižuje zatížení filtrování v SDK.

```csharp

    public void Process(ITelemetry item)
    {
      if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource)) {return;}

      // Send everything else:
      this.Next.Process(item);
    }

```

#### <a name="failed-authentication"></a>Neúspěšné ověření
Filtrujte požadavky "401" odpovědí.

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

#### <a name="filter-out-fast-remote-dependency-calls"></a>Odfiltrovat rychlé vzdálené závislosti volání
Pokud chcete diagnostikovat volání, která jsou pomalé, odfiltrovat rychlé ty.

> [!NOTE]
> To bude zkosení statistiky, které se zobrazí na portálu. Graf závislosti bude vypadat, jako by volání závislostí jsou všechny chyby.
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
[Tento blog](https://azure.microsoft.com/blog/implement-an-application-insights-telemetry-processor/) popisuje projekt, který diagnostikovat problémy s závislostí tak, že automatické odesílání pravidelných příkazy ping pro zjištění závislostí.


<a name="add-properties"></a>

## <a name="add-properties-itelemetryinitializer"></a>Přidání vlastnosti: ITelemetryInitializer
Slouží k definování globálních vlastností, které se odesílají s veškerou telemetrii; inicializátory telemetrie a přepsat vybrané chování modulů standardní telemetrická data.

Například Application Insights pro webový balíček shromažďuje telemetrická data týkající se požadavků HTTP. Ve výchozím nastavení, označí jako neúspěšný jakékoli žádosti s kódem odpovědi > = 400. Ale pokud budete chtít 400 považovat za úspěšné, můžete zadat inicializátor telemetrie, která nastaví vlastnost úspěch.

Pokud zadáte inicializátoru telemetrie, nazývá se pokaždé, když se některou z metod Track*() jsou volány. To zahrnuje metody volané moduly standardní telemetrická data. Podle konvence nenastavujte tyto moduly jakákoli vlastnost, která již byla nastavena inicializátor.

**Definujte vaši inicializátor**

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

**Načíst vaše inicializátor**

In ApplicationInsights.config:

```xml
    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="MvcWebRole.Telemetry.MyTelemetryInitializer, MvcWebRole"/>
        ...
      </TelemetryInitializers>
    </ApplicationInsights>
```

*Alternativně* lze vytvořit instanci v kódu, například v souboru Global.aspx.cs inicializátoru:

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

### <a name="java-telemetry-initializers"></a>Inicializátory telemetrie Java

[Dokumentace k sadě Java SDK](https://docs.microsoft.com/java/api/com.microsoft.applicationinsights.extensibility.telemetryinitializer?view=azure-java-stable)

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

### <a name="javascript-telemetry-initializers"></a>Inicializátory telemetrických dat jazyka JavaScript
*JavaScript*

Vložení inicializátoru telemetrie bezprostředně za inicializační kód, který jste získali z portálu:

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

                // To check the telemetry item�s type - for example PageView:
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

Souhrn k dispozici na telemetryItem bez vlastní vlastnosti, naleznete v tématu [Application Insights exportovat datového modelu](../../azure-monitor/app/export-data-model.md).

Můžete přidat tolik inicializátory, jak potřebujete.

## <a name="itelemetryprocessor-and-itelemetryinitializer"></a>ITelemetryProcessor a ITelemetryInitializer
Jaký je rozdíl mezi procesory telemetrická data a telemetrii inicializátory?

* Existují některé překrytí v co s nimi můžete dělat: oba slouží k přidání vlastnosti do telemetrická data.
* Vždy spustit před TelemetryProcessors TelemetryInitializers.
* TelemetryProcessors umožňují k úplnému nahrazení nebo zahodit položky telemetrie.
* TelemetryProcessors Nezpracovávat telemetrická data čítače výkonu.

## <a name="troubleshooting-applicationinsightsconfig"></a>Řešení potíží s soubor ApplicationInsights.config
* Potvrďte správnost plně kvalifikovaný název typu a název sestavení.
* Zkontrolujte, jestli soubor applicationinsights.config v adresáři výstupu a obsahuje všechny změny.

## <a name="reference-docs"></a>Referenční dokumenty
* [Přehled rozhraní API](../../azure-monitor/app/api-custom-events-metrics.md)
* [Referenční dokumentace technologie ASP.NET](https://msdn.microsoft.com/library/dn817570.aspx)

## <a name="sdk-code"></a>Kód SDK
* [Sada ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [SADY SDK TECHNOLOGIE ASP.NET](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)

## <a name="next"></a>Další kroky
* [Hledat události a protokoly](../../azure-monitor/app/diagnostic-search.md)
* [Vzorkování](../../azure-monitor/app/sampling.md)
* [Řešení potíží](../../azure-monitor/app/troubleshoot-faq.md)
