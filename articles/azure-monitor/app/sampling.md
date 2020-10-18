---
title: Vzorkování telemetrie v Azure Application Insights | Microsoft Docs
description: Jak udržet množství telemetrie v rámci řízení.
ms.topic: conceptual
ms.date: 01/17/2020
ms.reviewer: vitalyg
ms.custom: fasttrack-edit
ms.openlocfilehash: e4c5000adb2339d3fd0f828781a60f75c75894b5
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2020
ms.locfileid: "92168592"
---
# <a name="sampling-in-application-insights"></a>Vzorkování ve službě Application Insights

Vzorkování je funkce v [Azure Application Insights](./app-insights-overview.md). Je doporučený způsob, jak omezit provoz telemetrie, náklady na data a náklady na úložiště a zároveň zachovat statisticky správnou analýzu dat aplikací. Vzorkování také pomáhá vyhnout se Application Insights omezování telemetrie. Filtr vzorkování vybere položky, které jsou v relaci, takže můžete přecházet mezi položkami při provádění diagnostických šetření.

Když se počty metrik zobrazují na portálu, jsou znovu normalizovány, aby se braly v úvahu vzorkování. Tím se minimalizují všechny vlivy na statistiky.

## <a name="brief-summary"></a>Stručný souhrn

* Existují tři různé typy vzorkování: adaptivní vzorkování, vzorkování s pevnou sazbou a vzorkování přijímání.
* Adaptivní vzorkování je ve výchozím nastavení povolené ve všech nejnovějších verzích Application Insights ASP.NET a ASP.NET Core SDK (Software Development Kit). Používá ho i [Azure Functions](../../azure-functions/functions-overview.md).
* Vzorkování s pevnou sazbou je dostupné v posledních verzích sady Application Insights SDK pro ASP.NET, ASP.NET Core, Java (agent i SDK) a Python.
* Vzorkování ingestování funguje na koncovém bodu služby Application Insights. Platí jenom v případě, že se neplatí žádné jiné vzorkování. Pokud sada SDK vzorkuje vaši telemetrii, vzorkování ingestování je zakázané.
* Pokud se pro webové aplikace protokolují vlastní události a potřebujete zajistit, aby byla sada událostí zachována nebo zahozena společně, musí mít události stejnou `OperationId` hodnotu.
* Pokud píšete analytické dotazy, měli byste [vzít v úvahu vzorkování](../log-query/aggregations.md). Konkrétně místo pouhého počítání záznamů byste měli použít `summarize sum(itemCount)` .
* Některé typy telemetrie, včetně metrik výkonu a vlastních metrik, se vždycky uchovávají bez ohledu na to, jestli je povolený vzorkování, nebo ne.

Následující tabulka shrnuje typy vzorkování dostupné pro každou sadu SDK a typ aplikace:

| Sada Application Insights SDK | Adaptivní vzorkování se podporuje. | Vzorkování s pevnou sazbou je podporováno. | Podporuje se vzorkování přijímání. |
|-|-|-|-|
| ASP.NET | [Ano (ve výchozím nastavení zapnuto)](#configuring-adaptive-sampling-for-aspnet-applications) | [Ano](#configuring-fixed-rate-sampling-for-aspnet-applications) | Jenom v případě, že se neplatí žádné jiné vzorkování |
| ASP.NET Core | [Ano (ve výchozím nastavení zapnuto)](#configuring-adaptive-sampling-for-aspnet-core-applications) | [Ano](#configuring-fixed-rate-sampling-for-aspnet-core-applications) | Jenom v případě, že se neplatí žádné jiné vzorkování |
| Azure Functions | [Ano (ve výchozím nastavení zapnuto)](#configuring-adaptive-sampling-for-azure-functions) | Ne | Jenom v případě, že se neplatí žádné jiné vzorkování |
| Java | Ne | [Ano](#configuring-fixed-rate-sampling-for-java-applications) | Jenom v případě, že se neplatí žádné jiné vzorkování |
| Node.JS | Ne | [Ano](./nodejs.md#sampling) | Jenom v případě, že se neplatí žádné jiné vzorkování
| Python | Ne | [Ano](#configuring-fixed-rate-sampling-for-opencensus-python-applications) | Jenom v případě, že se neplatí žádné jiné vzorkování |
| Všichni ostatní | Ne | Ne | [Ano](#ingestion-sampling) |

> [!NOTE]
> Informace na většině této stránky se vztahují na aktuální verze sad Application Insights SDK. Informace o starších verzích sad SDK [najdete v části níže](#older-sdk-versions).

## <a name="types-of-sampling"></a>Typy vzorkování

Existují tři různé metody vzorkování:

* **Adaptivní vzorkování** automaticky upravuje objem telemetrie odeslaných ze sady SDK ve vaší aplikaci ASP.NET/ASP.NET Core a z Azure Functions. Toto je výchozí vzorkování při použití sady ASP.NET nebo ASP.NET Core SDK. Adaptivní vzorkování je aktuálně dostupné jenom pro ASP.NET telemetrie na straně serveru a pro Azure Functions.

* **Vzorkování s pevnou sazbou** snižuje objem telemetrie odeslaných z ASP.NET ASP.NET Core nebo z vašeho serveru Java i z prohlížečů uživatelů. Nastavte sazbu. Klient a Server budou synchronizovat své vzorkování, takže v hledání můžete procházet mezi souvisejícími zobrazeními stránky a požadavky.

* **Vzorkování** ingestování proběhne na koncovém bodu služby Application Insights. Zahodí některé telemetrie, které dorazí z vaší aplikace, při vzorkovací frekvenci, kterou jste nastavili. Neomezuje provoz telemetrie odeslaný z vaší aplikace, ale pomáhá udržet se v rámci měsíční kvóty. Hlavní výhodou pro vzorkování ingestování je, že můžete nastavit vzorkovací frekvenci bez opětovného nasazení aplikace. Vzorkování ingestování funguje jednotně pro všechny servery a klienty, ale nevztahuje se na to, kdy se v provozu nacházejí jiné typy vzorkování.

> [!IMPORTANT]
> Pokud se v provozu používají metody vzorkování s adaptivní nebo pevnou sazbou, je vzorkování ingest vypnuto.

## <a name="adaptive-sampling"></a>Adaptivní vzorkování

Adaptivní vzorkování ovlivňuje objem telemetrie odeslaných z aplikace webového serveru do koncového bodu služby Application Insights.

> [!TIP]
> Adaptivní vzorkování je ve výchozím nastavení povolené, když použijete sadu ASP.NET SDK nebo sadu ASP.NET Core SDK a ve výchozím nastavení je dostupná taky pro Azure Functions.

Svazek se automaticky upraví tak, aby udržoval v rámci zadané maximální míry provozu a byl řízen pomocí nastavení `MaxTelemetryItemsPerSecond` . Pokud aplikace vytvoří nízké množství telemetrie, například při ladění nebo z důvodu nízkého využití, položky se nebudou vyřadit pomocí procesoru vzorkování, pokud je svazek níže `MaxTelemetryItemsPerSecond` . Po zvýšení objemu telemetrie se vzorkovací frekvence upraví tak, aby se dosáhlo cílového svazku. Úprava se přepočítá v pravidelných intervalech a je založena na klouzavý průměr odchozí přenosové rychlosti.

Aby bylo možné dosáhnout cílového svazku, některá z vygenerované telemetrie jsou zahozena. Ale podobně jako jiné typy vzorkování, algoritmus zachovává související položky telemetrie. Například při kontrole telemetrie ve službě Search budete moci najít požadavek související s konkrétní výjimkou.

Počty metrik, jako je míra požadavků a četnosti výjimek, se upravují tak, aby vyrovnaly vzorkovací frekvenci, aby v Průzkumníkovi metrik zobrazovaly přibližně správné hodnoty.

### <a name="configuring-adaptive-sampling-for-aspnet-applications"></a>Konfigurace adaptivního vzorkování pro aplikace ASP.NET

> [!NOTE]
> Tato část se vztahuje na ASP.NET aplikace, nikoli na ASP.NET Core aplikace. [Přečtěte si o konfiguraci adaptivního vzorkování pro ASP.NET Core aplikací dále v tomto dokumentu.](#configuring-adaptive-sampling-for-aspnet-core-applications)

V nástroji [`ApplicationInsights.config`](./configuration-with-applicationinsights-config.md) můžete upravit několik parametrů v `AdaptiveSamplingTelemetryProcessor` uzlu. Zobrazené hodnoty jsou výchozí hodnoty:

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`
  
    Cílová frekvence [logických operací](./correlation.md#data-model-for-telemetry-correlation) , které adaptivní algoritmus cílí na shromažďování **na každém hostiteli serveru**. Pokud vaše webová aplikace běží na mnoha hostitelích, snižte tuto hodnotu tak, aby zůstala v rámci cílové míry provozu na portálu Application Insights.

* `<EvaluationInterval>00:00:15</EvaluationInterval>` 
  
    Interval, ve kterém je aktuální frekvence telemetrie znovu vyhodnocena. Hodnocení se provádí jako klouzavý průměr. Pokud je vaše telemetrie příčinou náhlého nárůstu, možná budete chtít tento interval zkrátit.

* `<SamplingPercentageDecreaseTimeout>00:02:00</SamplingPercentageDecreaseTimeout>`
  
    Když se změní procentuální hodnota vzorkování, jak brzy bude povoleno snížit procento vzorkování znovu a zachytit tak méně dat?

* `<SamplingPercentageIncreaseTimeout>00:15:00</SamplingPercentageIncreaseTimeout>`
  
    Když se změní procentuální hodnota vzorkování, jak brzy bude povoleno zvýšit procento vzorkování, aby se zachytí více dat?

* `<MinSamplingPercentage>0.1</MinSamplingPercentage>`
  
    Když se procento vzorkování liší, jaká je minimální hodnota, kterou je možné nastavit?

* `<MaxSamplingPercentage>100.0</MaxSamplingPercentage>`
  
    Když se procento vzorkování liší, jaká je maximální hodnota, kterou je možné nastavit?

* `<MovingAverageRatio>0.25</MovingAverageRatio>` 
  
    Při výpočtu klouzavého průměru určuje tato hodnota váhu, která by se měla přiřadit k nejnovější hodnotě. Použijte hodnotu rovnou nebo menší než 1. Menší hodnoty nastaví algoritmus méně aktivní na náhlé změny.

* `<InitialSamplingPercentage>100</InitialSamplingPercentage>`
  
    Množství telemetrie, které se má vzorkovat při právě spuštění aplikace Při ladění tuto hodnotu nezmenšujte.

* `<ExcludedTypes>Trace;Exception</ExcludedTypes>`
  
    Středníkem oddělený seznam typů, u kterých nechcete, aby se vzorkování nevztahovalo. Rozpoznané typy jsou: `Dependency` , `Event` , `Exception` , `PageView` , `Request` , `Trace` . Přenáší se veškerá telemetrie určených typů; typy, které nejsou zadány, budou vzorkovat.

* `<IncludedTypes>Request;Dependency</IncludedTypes>`
  
    Středníkem oddělený seznam typů, u kterých chcete provést vzorkování. Rozpoznané typy jsou: `Dependency` , `Event` , `Exception` , `PageView` , `Request` , `Trace` . Zadané typy budou vzorkovat; všechny telemetrie ostatních typů budou vždy přenášeny.

**Chcete-li přepnout** adaptivní vzorkování, odeberte `AdaptiveSamplingTelemetryProcessor` uzly z `ApplicationInsights.config` .

#### <a name="alternative-configure-adaptive-sampling-in-code"></a>Alternativa: Konfigurace adaptivního vzorkování v kódu

Místo nastavení parametru vzorkování v `.config` souboru můžete tyto hodnoty programově nastavit.

1. Odebere všechny `AdaptiveSamplingTelemetryProcessor` uzly ze `.config` souboru.
2. Pro konfiguraci adaptivního vzorkování použijte následující fragment kódu:

    ```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.Channel.Implementation;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    
    // ...

    var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
    // For older versions of the Application Insights SDK, use the following line instead:
    // var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;

    // Enable AdaptiveSampling so as to keep overall telemetry volume to 5 items per second.
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5);

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();
    ```

    ([Další informace o procesorech telemetrie](./api-filtering-sampling.md#filtering).)

Vzorkovací frekvenci můžete také upravit pro každý typ telemetrie jednotlivě, nebo můžete dokonce vyloučit, že některé z nich jsou odebírány na všech typech:

```csharp
// The following configures adaptive sampling with 5 items per second, and also excludes Dependency telemetry from being subjected to sampling.
builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");
```

### <a name="configuring-adaptive-sampling-for-aspnet-core-applications"></a>Konfigurace adaptivního vzorkování pro aplikace ASP.NET Core

Pro aplikace ASP.NET Core není k dispozici `ApplicationInsights.config` , takže se veškerá konfigurace provádí prostřednictvím kódu.
Adaptivní vzorkování je ve výchozím nastavení povolené pro všechny aplikace ASP.NET Core. Můžete zakázat nebo upravit chování vzorkování.

#### <a name="turning-off-adaptive-sampling"></a>Vypnutí adaptivního vzorkování

Výchozí funkci vzorkování lze zakázat při přidávání služby Application Insights v `ConfigureServices` rámci metody pomocí `ApplicationInsightsServiceOptions` `Startup.cs` souboru:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ...

    var aiOptions = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
    aiOptions.EnableAdaptiveSampling = false;
    services.AddApplicationInsightsTelemetry(aiOptions);

    //...
}
```

Výše uvedený kód zakáže adaptivní vzorkování. Pomocí následujících kroků přidejte vzorkování s dalšími možnostmi přizpůsobení.

#### <a name="configure-sampling-settings"></a>Konfigurovat nastavení vzorkování

`TelemetryProcessorChainBuilder`K přizpůsobení chování vzorkování použijte metody rozšíření, jak je znázorněno níže.

> [!IMPORTANT]
> Použijete-li tuto metodu ke konfiguraci vzorkování, nezapomeňte nastavit `aiOptions.EnableAdaptiveSampling` vlastnost na hodnotu `false` při volání `AddApplicationInsightsTelemetry()` .

```csharp
using Microsoft.ApplicationInsights.Extensibility

public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
{
    var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
    // For older versions of the Application Insights SDK, use the following line instead:
    // var builder = configuration.TelemetryProcessorChainBuilder;

    // Using adaptive sampling
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5);

    // Alternately, the following configures adaptive sampling with 5 items per second, and also excludes DependencyTelemetry from being subject to sampling.
    // builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));
    
    builder.Build();

    // ...
}
```

### <a name="configuring-adaptive-sampling-for-azure-functions"></a>Konfigurace adaptivního vzorkování pro Azure Functions

Podle pokynů na [této stránce](../../azure-functions/configure-monitoring.md#configure-sampling) můžete nakonfigurovat adaptivní vzorkování pro aplikace běžící v Azure Functions.

## <a name="fixed-rate-sampling"></a>Vzorkování s pevnou sazbou

Vzorkování s pevnou sazbou omezuje provoz odeslaný z webového serveru a webových prohlížečů. Na rozdíl od adaptivního vzorkování redukuje telemetrie za pevnou sazbu, kterou jste určili. Vzorkování s pevnou sazbou je k dispozici pro aplikace ASP.NET, ASP.NET Core, Java a Python.

Podobně jako u jiných technik vzorkování to také uchovává související položky. Také synchronizuje vzorkování klienta a serveru, aby se související položky zachovaly – například při zobrazení stránky v hledání můžete najít související požadavky serveru. 

V Průzkumník metrik se tarify, jako je počet požadavků a výjimek, vynásobí faktorem, který kompenzuje vzorkovací frekvenci, aby byly přibližně správné.

### <a name="configuring-fixed-rate-sampling-for-aspnet-applications"></a>Konfigurace vzorkování s pevnou sazbou pro aplikace ASP.NET

1. **Zakázat adaptivní vzorkování**: v [`ApplicationInsights.config`](./configuration-with-applicationinsights-config.md) , odeberte nebo zakomentujte `AdaptiveSamplingTelemetryProcessor` uzel.

    ```xml
    <TelemetryProcessors>
        <!-- Disabled adaptive sampling:
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
        </Add>
        -->
    ```

2. **Povolte modul vzorkování s pevnými rychlostmi.** Přidat tento fragment kódu do [`ApplicationInsights.config`](./configuration-with-applicationinsights-config.md) :
   
    ```XML
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <!-- Set a percentage close to 100/N where N is an integer. -->
            <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
            <SamplingPercentage>10</SamplingPercentage>
        </Add>
    </TelemetryProcessors>
    ```

      Případně místo nastavení parametru vzorkování v `ApplicationInsights.config` souboru můžete tyto hodnoty programově nastavit:

    ```csharp
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

    // ...

    var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
    // For older versions of the Application Insights SDK, use the following line instead:
    // var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;

    builder.UseSampling(10.0); // percentage

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();
    ```

    ([Další informace o procesorech telemetrie](./api-filtering-sampling.md#filtering).)

### <a name="configuring-fixed-rate-sampling-for-aspnet-core-applications"></a>Konfigurace vzorkování s pevnou sazbou pro ASP.NET Coreé aplikace

1. **Zakázat adaptivní vzorkování**: v metodě lze provádět změny `ConfigureServices` pomocí `ApplicationInsightsServiceOptions` :

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // ...

        var aiOptions = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
        aiOptions.EnableAdaptiveSampling = false;
        services.AddApplicationInsightsTelemetry(aiOptions);

        //...
    }
    ```

2. **Povolte modul vzorkování s pevnými rychlostmi.** Změny lze provést v `Configure` metodě, jak je znázorněno v následujícím fragmentu kódu:

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        var configuration = app.ApplicationServices.GetService<TelemetryConfiguration>();

        var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
        // For older versions of the Application Insights SDK, use the following line instead:
        // var builder = configuration.TelemetryProcessorChainBuilder;

        // Using fixed rate sampling
        double fixedSamplingPercentage = 10;
        builder.UseSampling(fixedSamplingPercentage);

        builder.Build();

        // ...
    }
    ```

### <a name="configuring-fixed-rate-sampling-for-java-applications"></a>Konfigurace vzorkování s pevnou sazbou pro aplikace v jazyce Java

Ve výchozím nastavení nejsou v agentech Java a v sadě SDK povoleny žádné vzorkování. V současné době podporuje jenom vzorkování pevné míry. Adaptivní vzorkování není v jazyce Java podporováno.

#### <a name="configuring-java-agent"></a>Konfigurace agenta Java

1. Stáhnout [ApplicationInsights-agent-3.0.0-Preview. 5. jar](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.0.0-PREVIEW.5/applicationinsights-agent-3.0.0-PREVIEW.5.jar)

1. Pokud chcete vzorkování povolit, přidejte do `ApplicationInsights.json` souboru následující:

```json
{
  "instrumentationSettings": {
    "preview": {
      "sampling": {
        "fixedRate": {
          "percentage": 10 //this is just an example that shows you how to enable only only 10% of transaction 
        }
      }
    }
  }
}
```

#### <a name="configuring-java-sdk"></a>Konfigurace sady Java SDK

1. Stáhněte a nakonfigurujte svou webovou aplikaci pomocí nejnovější [sady SDK pro Application Insights Java](./java-get-started.md).

2. **Povolit modul vzorkování s pevnými rychlostmi** přidáním následujícího fragmentu do `ApplicationInsights.xml` souboru:

    ```XML
    <TelemetryProcessors>
        <BuiltInProcessors>
            <Processor type="FixedRateSamplingTelemetryProcessor">
                <!-- Set a percentage close to 100/N where N is an integer. -->
                <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
                <Add name="SamplingPercentage" value="50" />
            </Processor>
        </BuiltInProcessors>
    </TelemetryProcessors>
    ```

3. Můžete zahrnout nebo vyloučit konkrétní typy telemetrie z vzorkování pomocí následujících značek uvnitř `Processor` značky `FixedRateSamplingTelemetryProcessor` :
   
    ```XML
    <ExcludedTypes>
        <ExcludedType>Request</ExcludedType>
    </ExcludedTypes>

    <IncludedTypes>
        <IncludedType>Exception</IncludedType>
    </IncludedTypes>
    ```

Typy telemetrie, které lze zahrnout nebo vyloučit z vzorkování, jsou: `Dependency` , `Event` , `Exception` , `PageView` , a `Request` `Trace` .

> [!NOTE]
> Pro procento vzorkování vyberte procento, které je blízko 100/N, kde N je celé číslo.  V současné době vzorkování nepodporují jiné hodnoty.

### <a name="configuring-fixed-rate-sampling-for-opencensus-python-applications"></a>Konfigurace vzorkování s pevnou sazbou pro aplikace OpenCensus v Pythonu

Instrumentujte svoji aplikaci pomocí nejnovějšího [OpenCensus Azure monitor vývozců](./opencensus-python.md).

> [!NOTE]
> Vzorkování s pevnou sazbou není pro exportéra metrik k dispozici. To znamená, že vlastní metriky jsou jediné typy telemetrie, ve kterých nelze nakonfigurovat vzorkování. Exportér metrik pošle veškerou telemetrii, kterou sleduje.

#### <a name="fixed-rate-sampling-for-tracing"></a>Vzorkování s pevnou sazbou pro trasování ####
V rámci konfigurace `Tracer` můžete zadat vzorkovník `sampler`. Pokud není zadaný žádný explicitní vzorkovník, použije se `ProbabilitySampler` ve výchozím nastavení. Ve `ProbabilitySampler` výchozím nastavení by se použila sazba 1/10000, což znamená, že se do Application Insights pošle jeden z každých 10000 požadavků. Informace o zadání vzorkovací frekvence najdete níže.

Chcete-li určit vzorkovací frekvenci, ujistěte se, že vaše hodnota `Tracer` Určuje vzorkovník s vzorkovací frekvencí mezi 0,0 a 1,0 včetně. Vzorkovací frekvence 1,0 představuje 100%, což znamená, že všechny vaše požadavky budou odeslány jako telemetrie do Application Insights.

```python
tracer = Tracer(
    exporter=AzureExporter(
        instrumentation_key='00000000-0000-0000-0000-000000000000',
    ),
    sampler=ProbabilitySampler(1.0),
)
```

#### <a name="fixed-rate-sampling-for-logs"></a>Vzorkování s pevnou sazbou pro protokoly ####
Vzorkování s pevnou sazbou můžete nakonfigurovat `AzureLogHandler` změnou `logging_sampling_rate` volitelného argumentu. Pokud není zadán žádný argument, bude použita vzorkovací frekvence 1,0. Vzorkovací frekvence 1,0 představuje 100%, což znamená, že všechny vaše požadavky budou odeslány jako telemetrie do Application Insights.

```python
handler = AzureLogHandler(
    instrumentation_key='00000000-0000-0000-0000-000000000000',
    logging_sampling_rate=0.5,
)
```

### <a name="configuring-fixed-rate-sampling-for-web-pages-with-javascript"></a>Konfigurace vzorkování s pevnou sazbou pro webové stránky pomocí JavaScriptu

Webové stránky založené na jazyce JavaScript je možné nakonfigurovat tak, aby používaly Application Insights. Telemetrie se odesílá z klientské aplikace spuštěné v prohlížeči uživatele a stránky můžou být hostované z libovolného serveru.

Když [nakonfigurujete webové stránky založené na JavaScriptu pro Application Insights](javascript.md), upravte fragment JavaScriptu, který získáte z portálu Application Insights.

> [!TIP]
> V aplikacích ASP.NET s integrovaným jazykem JavaScript se fragment kódu obvykle nachází v `_Layout.cshtml` .

Vložte řádek jako `samplingPercentage: 10,` před klíč instrumentace:

```xml
<script>
    var appInsights = // ... 
    ({ 
      // Value must be 100/N where N is an integer.
      // Valid examples: 50, 25, 20, 10, 5, 1, 0.1, ...
      samplingPercentage: 10, 

      instrumentationKey: ...
    }); 

    window.appInsights = appInsights; 
    appInsights.trackPageView(); 
</script>
```

Pro procento vzorkování vyberte procento, které je blízko 100/N, kde N je celé číslo. V současné době vzorkování nepodporují jiné hodnoty.

#### <a name="coordinating-server-side-and-client-side-sampling"></a>Koordinace vzorkování na straně serveru a na straně klienta

Sada JavaScript SDK na straně klienta se účastní vzorkování s pevnou sazbou ve spojení se sadou SDK na straně serveru. Instrumentované stránky odesílají telemetrie na straně klienta pouze ze stejného uživatele, pro který sada SDK na straně serveru provedla rozhodnutí, aby byla zahrnuta do vzorkování. Tato logika je navržená tak, aby udržovala integritu uživatelských relací napříč klientskými a serverovými aplikacemi. V důsledku toho můžete z jakékoliv konkrétní položky telemetrie v Application Insights najít všechny další položky telemetrie pro tohoto uživatele nebo relaci a v hledání můžete procházet mezi souvisejícími zobrazeními stránky a požadavky.

Pokud se vaše telemetrie na straně klienta a serveru nezobrazují koordinované ukázky:

* Ověřte, že jste povolili vzorkování na serveru i v klientovi.
* Ověřte, že jste v klientovi i na serveru nastavili stejné procento vzorkování.
* Ujistěte se, že verze sady SDK je 2,0 nebo vyšší.

## <a name="ingestion-sampling"></a>Vzorkování přijímání

Vzorkování ingestování funguje v okamžiku, kdy telemetrie z webového serveru, prohlížeče a zařízení dosáhne koncového bodu služby Application Insights. I když se tím nesnižuje provoz telemetrie odeslaný z vaší aplikace, snižuje se množství zpracovaných a uchovávaných (a účtuje se za) Application Insights.

Tento typ vzorkování použijte, pokud vaše aplikace často prochází za měsíční kvótu a nemáte možnost použít některý z typů vzorkování založených na sadě SDK. 

Nastavte tempo vzorkování na stránce využití a odhadované náklady:

![V okně Přehled aplikace klikněte na nastavení, kvóta, ukázky, pak vyberte vzorkovací frekvenci a klikněte na aktualizovat.](./media/sampling/data-sampling.png)

Podobně jako jiné typy vzorkování, algoritmus zachovává související položky telemetrie. Například při kontrole telemetrie ve službě Search budete moci najít požadavek související s konkrétní výjimkou. Počty metrik, jako je rychlost požadavků a četnost výjimek, se uchovávají správně.

Datové body, které jsou zahozeny vzorkováním, nejsou k dispozici v žádné Application Insights funkci, jako je například [průběžný export](./export-telemetry.md).

Vzorkování ingestování nefunguje v průběhu operace vzorkování adaptivního nebo fixního přenosu. Adaptivní vzorkování je ve výchozím nastavení povolené, když se používá sada ASP.NET SDK nebo sada ASP.NET Core SDK nebo když je Application Insights povolený v [Azure App Service ](azure-web-apps.md) nebo pomocí monitorování stavu. Když koncový bod služby Application Insights obdrží telemetrii, prověřuje telemetrii a pokud je frekvence vzorkování nahlášena jako méně než 100% (což indikuje, že se telemetrie odebírá), bude vzorkovací frekvence ingestování, kterou jste nastavili, ignorována.

> [!WARNING]
> Hodnota zobrazená na dlaždici portálu označuje hodnotu, kterou jste nastavili pro vzorkování přijímání. Nepředstavuje skutečný vzorkovací kmitočet, pokud je v provozu nějaký druh vzorkování sady SDK (adaptivní nebo fixní frekvence).

## <a name="when-to-use-sampling"></a>Kdy použít vzorkování

Obecně platí pro většinu malých a středně velkých aplikací, které nepotřebujete vzorkování. Nejužitečnější diagnostické informace a nejpřesnější statistiky se získávají shromažďováním dat ze všech vašich uživatelských aktivit. 

Hlavní výhody vzorkování:

* Služba Application Insights vychází z datových bodů, když vaše aplikace pošle velmi vysokou míru telemetrie v krátkém časovém intervalu. Vzorkování snižuje pravděpodobnost, že dojde k omezování, které vaše aplikace uvidí.
* Aby se zachovala [kvóta](pricing.md) datových bodů pro vaši cenovou úroveň. 
* Pro snížení síťového provozu z kolekce telemetrie. 

### <a name="which-type-of-sampling-should-i-use"></a>Jaký typ vzorkování mám použít?

**Vzorkování ingestování použijte v těchto případech:**

* Často používáte měsíční kvótu telemetrie.
* Máte příliš mnoho telemetrie z webových prohlížečů vašich uživatelů.
* Používáte verzi sady SDK, která nepodporuje vzorkování – například verze ASP.NET starší než 2.

**Vzorkování s pevnou sazbou použijte v těchto případech:**

* Chcete synchronizovat vzorkování mezi klientem a serverem, takže při zkoumání událostí ve [vyhledávání](./diagnostic-search.md)můžete procházet mezi souvisejícími událostmi klienta a serveru, jako jsou například zobrazení stránky a požadavky HTTP.
* Jste si jistí vhodné procento vzorkování pro vaši aplikaci. Měl by být dostatečně vysoký, aby dosáhl přesné metriky, ale překročil sazbu, která překračuje vaši kvótu a omezení omezení.

**Použít adaptivní vzorkování:**

Pokud se podmínky použití ostatních forem vzorkování nepoužijí, doporučujeme adaptivní vzorkování. Toto nastavení je ve výchozím nastavení povoleno v sadě ASP.NET/ASP.NET Core SDK. Nebude snižovat provoz, dokud nedosáhnete určité minimální míry, takže weby s nízkým použitím nebudou pravděpodobně vzorkovat vůbec.

## <a name="knowing-whether-sampling-is-in-operation"></a>Určení, jestli probíhá vzorkování

Pokud chcete zjistit skutečnou vzorkovací frekvenci bez ohledu na to, kde byla použita, použijte [dotaz Analytics](../log-query/log-query-overview.md) , jako je například:

```kusto
union requests,dependencies,pageViews,browserTimings,exceptions,traces
| where timestamp > ago(1d)
| summarize RetainedPercentage = 100/avg(itemCount) by bin(timestamp, 1h), itemType
```

Pokud vidíte, že `RetainedPercentage` pro libovolný typ je menší než 100, tento typ telemetrie se vzorkuje.

> [!IMPORTANT]
> Application Insights nezahrnuje ukázkovou relaci, metriky (včetně vlastních metrik) nebo typy telemetrie čítače výkonu v jakémkoli z metod vzorkování. Tyto typy jsou vždycky vyloučené z vzorkování, protože snížení přesnosti může být pro tyto typy telemetrie vysoce nežádoucí.

## <a name="how-sampling-works"></a>Jak vzorkování funguje

Algoritmus vzorkování určuje, které položky telemetrie se mají odpojit a které se mají zachovat. To platí bez ohledu na to, jestli se vzorkování provádí v sadě SDK nebo ve službě Application Insights. Rozhodnutí o vzorkování vychází z několika pravidel, která se zaměřují na zachování všech vzájemně se nedotčených datových bodů, což zachovává diagnostické prostředí v Application Insights, které je možné a spolehlivé i s omezenou datovou sadou. Pokud například vaše aplikace obsahuje neúspěšný požadavek obsažený v ukázce, zachová se další položky telemetrie (například výjimka a trasování pro tuto žádost). Vzorkování buď udržuje, nebo je ponechá dohromady. Výsledkem je, že když se podíváte na podrobnosti o požadavku v Application Insights, můžete vždy zobrazit žádost spolu s jejími přidruženými položkami telemetrie.

Rozhodnutí o vzorkování vychází z ID operace požadavku. to znamená, že všechny položky telemetrie patřící do konkrétní operace jsou buď zachované, nebo vyřazené. Pro položky telemetrie, které nemají nastaven ID operace (například položky telemetrie hlášené z asynchronních vláken bez kontextu HTTP), zachytí jenom procento položek telemetrie každého typu.

Když prezentujete telemetrii zpátky, služba Application Insights upravuje metriky podle stejného procenta vzorkování, které bylo použito v době shromažďování, k náhradě chybějících datových bodů. Proto při prohlížení telemetrie v Application Insights se uživatelům zobrazuje statisticky správné aproximace, které se velmi blíží skutečným číslům.

Přesnost aproximace je převážně závislá na nakonfigurované procentuální hodnotě vzorkování. Také se zvyšuje přesnost aplikací, které zpracovávají velký objem obecně podobných požadavků od mnoha uživatelů. Na druhé straně pro aplikace, které nefungují s významným zatížením, není vzorkování nutné, protože tyto aplikace mohou obvykle posílat veškerou telemetrii při zachování kvóty, aniž by došlo ke ztrátě dat z omezení. 

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

*Jaké jsou výchozí chování vzorkování v sadách ASP.NET a ASP.NET Core SDK?*

* Pokud používáte některou z nejnovějších verzí výše uvedené sady SDK, adaptivní vzorkování je ve výchozím nastavení povolené s pěti položkami telemetrie za sekundu.
  `AdaptiveSamplingTelemetryProcessor`Ve výchozím nastavení jsou přidány dva uzly a jeden zahrnuje `Event` typ při vzorkování, zatímco druhý vylučuje `Event` typ z vzorkování. Tato konfigurace znamená, že se sada SDK pokusí omezit položky telemetrie na pět položek telemetrie `Event` typů a pět položek telemetrie pro všechny ostatní typy, a tím zajistit, že `Events` jsou vzorky odděleně od jiných typů telemetrie. Události se obvykle používají pro obchodní telemetrie a pravděpodobně by na ně neměly mít vliv žádné diagnostické svazky telemetrie.
  
  Následuje ukázka `ApplicationInsights.config` vygenerovaného výchozího souboru. V ASP.NET Core je v kódu povolené stejné výchozí chování. Chcete-li změnit toto výchozí chování, použijte [Příklady v předchozí části této stránky](#configuring-adaptive-sampling-for-aspnet-core-applications) .

    ```xml
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
            <ExcludedTypes>Event</ExcludedTypes>
        </Add>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
            <IncludedTypes>Event</IncludedTypes>
        </Add>
    </TelemetryProcessors>
    ```

*Je možné telemetrii vzorkovat více než jednou?*

* Ne. SamplingTelemetryProcessors ignorovat položky z hlediska vzorkování, pokud je položka již vzorkovat. Totéž platí také pro vzorkování ingestování, které nepoužijí vzorkování na ty položky, které jsou již v samotné sadě SDK navzorkované.

*Proč není vzorkování jednoduché "shromáždit X procento každého typu telemetrie"?*

* I když tento přístup k odběru vzorků poskytne vysokou úroveň přesnosti v rámci aproximace metriky, mohl by poškodit možnost korelace diagnostických dat podle uživatele, relace a žádosti, což je pro diagnostiku velmi důležité. Proto vzorkování funguje lépe se zásadami, jako je "shromáždění všech položek telemetrie pro X procent uživatelů aplikace" nebo "shromáždění všech telemetrie pro X procent žádostí o aplikace". Pro položky telemetrie, které nejsou přidruženy k žádostem (jako je například asynchronní zpracování na pozadí), by záloha měla být shromážděna X procent všech položek pro každý typ telemetrie. 

*Může se v průběhu času změnit procentuální hodnota vzorkování?*

* Ano, adaptivní vzorkování postupně mění procento vzorkování na základě aktuálně zjištěného objemu telemetrie.

*Jak mám při použití vzorkování s pevnou sazbou vědět, jaké procento vzorkování bude fungovat nejlépe pro moji aplikaci?*

* Jedním ze způsobů, jak začít s adaptivním vzorkováním, zjistíte, ke které sazbě se připravuje (viz výše uvedený dotaz), a pak se pomocí této sazby přepne na vzorkování s pevnou sazbou. 
  
    V opačném případě je nutné odhadnout. Analyzujte své aktuální využití telemetrie v Application Insights, Sledujte jakékoliv omezení, ke kterému dochází, a odhad objemu shromážděné telemetrie. Tyto tři vstupy společně s vaší vybranou cenovou úrovní navrhují, kolik můžete chtít snížit objem shromážděné telemetrie. Zvýšení počtu uživatelů nebo nějakého jiného posunu v rámci telemetrie může ale způsobit zrušení platnosti vašeho odhadu.

*Co se stane, když nakonfigurujem procento vzorkování jako příliš nízké?*

* Příliš nízké procenta vzorkování způsobují nadměrné vzorkování a při Application Insights pokusech o kompenzaci vizualizace dat pro snížení objemu dat zmenšují přesnost sblížení. I vaše diagnostické prostředí může mít negativní vliv na to, že některé z častých neúspěšných nebo pomalých žádostí můžou být ukázkové.

*Co se stane, když nakonfigurujem procento vzorkování jako příliš vysoké?*

* Konfigurace příliš vysokého procenta vzorkování (není dostatečně agresivní) vede k nedostatečnému snížení objemu shromážděné telemetrie. Může se stát, že dojde ke ztrátě dat telemetrie v souvislosti s omezováním a náklady na použití Application Insights můžou být vyšší, než jste naplánovali z důvodu poplatků za nadlimitní využití.

*Na kterých platformách je možné vzorkování použít?*

* Vzorkování příjmu se může vyskytnout automaticky pro všechny telemetrie nad určitým svazkem, pokud sada SDK neprovádí vzorkování. Tato konfigurace by mohla fungovat například v případě, že používáte starší verzi sady ASP.NET SDK nebo Java SDK.
* Pokud používáte aktuální ASP.NET nebo ASP.NET Core sady SDK (hostované buď v Azure, nebo na vašem vlastním serveru), můžete ve výchozím nastavení získat adaptivní vzorkování, ale můžete přejít na pevný kurz, jak je popsáno výše. Při vzorkování s pevnou sazbou se sada SDK pro prohlížeč automaticky synchronizuje s ukázkovými událostmi, které se týkají. 
* Pokud používáte aktuálního agenta Java, můžete pro `ApplicationInsights.json` vzorkování s pevnou sazbou nakonfigurovat (pro sadu Java SDK, nakonfigurovat `ApplicationInsights.xml` ). Vzorkování je ve výchozím nastavení vypnuté. Při vzorkování s pevnou sazbou se sada SDK pro prohlížeč a server automaticky synchronizuje s ukázkovými událostmi, které jsou v relaci.

*Existují určité vzácné události, které vždycky chcete vidět. Jak se dají dostat za modul vzorkování?*

* Nejlepším způsobem, jak toho dosáhnout, je napsat vlastní [TelemetryInitializer](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer), který nastaví `SamplingPercentage` 100 na položku telemetrie, kterou chcete uchovat, jak je znázorněno níže. Vzhledem k tomu, že je zaručeno spouštění inicializátorů před procesory telemetrie (včetně vzorkování), zajistí to, že všechny techniky vzorkování budou tuto položku ignorovat z jakéhokoli hlediska vzorkování. Vlastní Inicializátory telemetrie jsou k dispozici v sadě ASP.NET SDK, sadě ASP.NET Core SDK, sadě JavaScript SDK a sadě Java SDK. Můžete například nakonfigurovat inicializátor telemetrie pomocí sady ASP.NET SDK:

    ```csharp
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if(somecondition)
            {
                ((ISupportSampling)telemetry).SamplingPercentage = 100;
            }
        }
    }
    ```

## <a name="older-sdk-versions"></a>Starší verze sady SDK

Adaptivní vzorkování je k dispozici pro sadu Application Insights SDK pro ASP.NET v 2.0.0-beta3 a novější, Microsoft. ApplicationInsights. AspNetCore SDK v 2.2.0-Beta1 a novější a je ve výchozím nastavení povolená.

Vzorkování s pevnou sazbou je funkce sady SDK ve verzích ASP.NET od 2.0.0 a Java SDK verze 2.0.1 a vyšší.

V rámci 2.5.0 SDK a v 2.2.0-beta3 sady ASP.NET Core SDK byla rozhodnutí o vzorkování založena na hodnotě hash ID uživatele pro aplikace definující "uživatele" (tj. nejčastěji používané webové aplikace). Pro typy aplikací, které nedefinovaly uživatele (například webové služby), bylo rozhodnutí o vzorkování založeno na ID operace žádosti. Poslední verze sad SDK ASP.NET a ASP.NET Core používají ID operace pro rozhodnutí o vzorkování.

## <a name="next-steps"></a>Další kroky

* [Filtrování](./api-filtering-sampling.md) může poskytovat přísnější kontrolu nad tím, co vaše sada SDK posílá.
* Přečtěte si článek o vývojářské síti [optimalizace telemetrie pomocí Application Insights](/archive/msdn-magazine/2017/may/devops-optimize-telemetry-with-application-insights).

