---
title: Vzorkování telemetrie v Přehledech aplikací Azure | Dokumenty společnosti Microsoft
description: Jak udržet objem telemetrie pod kontrolou.
ms.topic: conceptual
ms.date: 01/17/2020
ms.reviewer: vitalyg
ms.custom: fasttrack-edit
ms.openlocfilehash: fc9db23f7733f97ca207e834d4543fbdb1b9db5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275825"
---
# <a name="sampling-in-application-insights"></a>Vzorkování ve službě Application Insights

Vzorkování je funkce v [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). Je to doporučený způsob, jak snížit telemetrický provoz, náklady na data a náklady na úložiště při zachování statisticky správné analýzy dat aplikace. Vzorkování také pomáhá vyhnout se application insights omezení telemetrie. Filtr vzorkování vybere položky, které spolu souvisejí, takže můžete procházet mezi položkami při diagnostických šetřeních.

Když jsou na portálu prezentovány počty metrik, jsou znovu normalizovány, aby se zohlednilo vzorkování. Tím se minimalizuje jakýkoli vliv na statistiky.

## <a name="brief-summary"></a>Stručné shrnutí

* Existují tři různé typy odběru vzorků: adaptivní odběr vzorků, odběr vzorků s pevnou rychlostí a odběr vzorků při požití.
* Adaptivní vzorkování je ve výchozím nastavení povoleno ve všech nejnovějších verzích sad Application Insights ASP.NET a ASP.NET core software Development Kits (SDK). Používá se také azure [funkce](https://docs.microsoft.com/azure/azure-functions/functions-overview).
* Vzorkování s pevnou rychlostí je k dispozici v nejnovějších verzích sad SDK Application Insights pro ASP.NET, ASP.NET Jádra, Javy a Pythonu.
* Vzorkování ingestování funguje na koncovém bodu služby Application Insights. Použije se pouze v případě, že není v platnosti žádný jiný odběr vzorků. Pokud sada SDK vzorky telemetrie, vzorkování ingestování je zakázáno.
* Pro webové aplikace, pokud protokolovat vlastní události a je třeba zajistit, že sada událostí je `OperationId` zachována nebo zahozena společně, události musí mít stejnou hodnotu.
* Pokud píšete dotazy Analytics, měli byste [vzít v úvahu vzorkování](../../azure-monitor/log-query/aggregations.md). Zejména místo pouhého počítání záznamů byste `summarize sum(itemCount)`měli použít .
* Některé typy telemetrie, včetně metrik výkonu a vlastní metriky, jsou vždy uchovávány bez ohledu na to, zda vzorkování je povolena nebo ne.

Následující tabulka shrnuje typy vzorkování, které jsou k dispozici pro každou sadu SDK, a typ aplikace:

| Aplikace Přehledy SDK | Adaptivní vzorkování podporováno | Odběr vzorků s pevnou frekvencí podporován | Odběr vzorků z požití podporován |
|-|-|-|-|
| ASP.NET | [Ano (zapnuto ve výchozím nastavení)](#configuring-adaptive-sampling-for-aspnet-applications) | [Ano](#configuring-fixed-rate-sampling-for-aspnet-applications) | Pouze v případě, že nedochází k jinému odběru vzorků |
| ASP.NET Core | [Ano (zapnuto ve výchozím nastavení)](#configuring-adaptive-sampling-for-aspnet-core-applications) | [Ano](#configuring-fixed-rate-sampling-for-aspnet-core-applications) | Pouze v případě, že nedochází k jinému odběru vzorků |
| Azure Functions | [Ano (zapnuto ve výchozím nastavení)](#configuring-adaptive-sampling-for-azure-functions) | Ne | Pouze v případě, že nedochází k jinému odběru vzorků |
| Java | Ne | [Ano](#configuring-fixed-rate-sampling-for-java-applications) | Pouze v případě, že nedochází k jinému odběru vzorků |
| Python | Ne | [Ano](#configuring-fixed-rate-sampling-for-opencensus-python-applications) | Pouze v případě, že nedochází k jinému odběru vzorků |
| Všechny ostatní | Ne | Ne | [Ano](#ingestion-sampling) |

> [!NOTE]
> Informace na většině této stránky se vztahují na aktuální verze sad SDK Application Insights. Informace o starších verzích sad SDK [naleznete v části níže](#older-sdk-versions).

## <a name="types-of-sampling"></a>Typy odběru vzorků

Existují tři různé metody odběru vzorků:

* **Adaptivní vzorkování** automaticky upraví objem telemetrie odeslané z sady SDK ve vaší aplikaci ASP.NET/ASP.NET Core a z funkce Azure. Toto je výchozí vzorkování při použití sady ASP.NET nebo ASP.NET Core SDK. Adaptivní vzorkování je aktuálně k dispozici pouze pro ASP.NET telemetrii na straně serveru a pro funkce Azure.

* **Vzorkování s pevnou rychlostí** snižuje objem telemetrie odeslané jak z ASP.NET nebo ASP.NET serveru Core nebo Java, tak z prohlížečů uživatelů. Nastavíte sazbu. Klient a server synchronizují vzorkování, takže ve vyhledávání můžete procházet mezi souvisejícími zobrazeními stránek a požadavky.

* **Vzorkování ingestuce** probíhá v koncovém bodě služby Application Insights. Zahodí některé telemetrie, která přichází z vaší aplikace, při vzorkovací rychlosti, kterou nastavíte. Nesnižuje telemetrický provoz odeslaný z vaší aplikace, ale pomáhá vám udržovat v rámci vaší měsíční kvóty. Hlavní výhodou vzorkování ingestování je, že můžete nastavit vzorkovací frekvenci bez opětovného nasazení aplikace. Vzorkování ingestování funguje jednotně pro všechny servery a klienty, ale neplatí, pokud jsou v provozu jiné typy vzorkování.

> [!IMPORTANT]
> Pokud jsou v provozu metody odběru vzorků s adaptivní nebo pevnou rychlostí, je odběr vzorků z ingestování zakázán.

## <a name="adaptive-sampling"></a>Adaptivní odběr vzorků

Adaptivní vzorkování ovlivňuje objem telemetrie odeslané z aplikace webového serveru do koncového bodu služby Application Insights.

> [!TIP]
> Adaptivní vzorkování je ve výchozím nastavení povoleno při použití sady ASP.NET SDK nebo ASP.NET core sdk a je také povoleno ve výchozím nastavení pro funkce Azure.

Hlasitost je nastavena automaticky tak, aby se udržela v rámci `MaxTelemetryItemsPerSecond`zadané maximální rychlosti provozu, a je řízena nastavením . Pokud aplikace vytváří nízké množství telemetrie, například při ladění nebo z důvodu nízké využití, položky nebudou vynechány `MaxTelemetryItemsPerSecond`vzorkovací procesor, pokud je svazek nižší . S nárůstem objemu telemetrie se vzorkovací frekvence upraví tak, aby bylo dosaženo cílového objemu. Nastavení je přepočítáno v pravidelných intervalech a je založeno na klouzavém průměru odchozí přenosové rychlosti.

K dosažení cílového objemu je některé generované telemetrie zahozeny. Ale stejně jako jiné typy vzorkování algoritmus zachová související telemetrické položky. Například při kontrole telemetrie ve vyhledávání, budete moci najít požadavek vztahující se k určité výjimky.

Počty metrik, jako je například míra požadavků a míra výjimek, jsou upraveny tak, aby kompenzovaly vzorkovací frekvenci tak, aby v Průzkumníku metrik zobrazovaly přibližně správné hodnoty.

### <a name="configuring-adaptive-sampling-for-aspnet-applications"></a>Konfigurace adaptivního vzorkování pro aplikace ASP.NET

> [!NOTE]
> Tato část se vztahuje na ASP.NET aplikace, nikoli pro ASP.NET základní aplikace. [Další informace o konfiguraci adaptivního vzorkování pro aplikace ASP.NET Core dále v tomto dokumentu.](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications)

V [`ApplicationInsights.config`](../../azure-monitor/app/configuration-with-applicationinsights-config.md)oblasti můžete upravit několik `AdaptiveSamplingTelemetryProcessor` parametrů v uzlu. Zobrazené obrázky jsou výchozí hodnoty:

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`
  
    Cílová rychlost, kterou adaptivní algoritmus cílí **na každého hostitele serveru**. Pokud vaše webová aplikace běží na mnoha hostitelích, snižte tuto hodnotu tak, aby na portálu Application Insights zůstala v rámci cílové míry provozu.

* `<EvaluationInterval>00:00:15</EvaluationInterval>` 
  
    Interval, ve kterém je přehodnocena aktuální rychlost telemetrie. Vyhodnocení se provádí jako klouzavý průměr. Tento interval můžete zkrátit, pokud je telemetrie náchylná k náhlým výbuchům.

* `<SamplingPercentageDecreaseTimeout>00:02:00</SamplingPercentageDecreaseTimeout>`
  
    Když se změní procentuální hodnota vzorkování, za jak dlouho můžeme znovu snížit procento vzorkování, abychom zachytili méně dat?

* `<SamplingPercentageIncreaseTimeout>00:15:00</SamplingPercentageIncreaseTimeout>`
  
    Když se změní procentuální hodnota vzorkování, za jak dlouho můžeme znovu zvýšit procento vzorkování, abychom získali více dat?

* `<MinSamplingPercentage>0.1</MinSamplingPercentage>`
  
    Jak se procento vzorkování liší, jaká je minimální hodnota, kterou můžeme nastavit?

* `<MaxSamplingPercentage>100.0</MaxSamplingPercentage>`
  
    Jak se procento vzorkování liší, jaká je maximální hodnota, kterou můžeme nastavit?

* `<MovingAverageRatio>0.25</MovingAverageRatio>` 
  
    Při výpočtu klouzavého průměru se určuje hmotnost, která by měla být přiřazena k poslední hodnotě. Použijte hodnotu rovnou nebo menší než 1. Menší hodnoty, aby algoritmus méně reaktivní na náhlé změny.

* `<InitialSamplingPercentage>100</InitialSamplingPercentage>`
  
    Množství telemetrie na ukázku při právě spuštění aplikace. Nesnižovat tuto hodnotu při ladění.

* `<ExcludedTypes>Trace;Exception</ExcludedTypes>`
  
    Středník oddělený seznam typů, které nechcete být předmětem vzorkování. Rozpoznané `Dependency`typy `Event` `Exception`jsou: , , `PageView`, `Request`, . `Trace` Všechny telemetrie zadaných typů je přenášena; typy, které nejsou zadány, budou odebrány vzorky.

* `<IncludedTypes>Request;Dependency</IncludedTypes>`
  
    Středník oddělený seznam typů, které chcete podrobit vzorkování. Rozpoznané `Dependency`typy `Event` `Exception`jsou: , , `PageView`, `Request`, . `Trace` Vzorky budou odebrány zadaným typům. všechny telemetrie ostatních typů budou vždy přenášeny.

**Chcete-li adaptivní** vzorkování `AdaptiveSamplingTelemetryProcessor` vypnout, odeberte z aplikace `ApplicationInsights.config`.

#### <a name="alternative-configure-adaptive-sampling-in-code"></a>Alternativa: Konfigurace adaptivního vzorkování v kódu

Namísto nastavení parametru `.config` vzorkování v souboru můžete tyto hodnoty programově nastavit.

1. Odeberte `AdaptiveSamplingTelemetryProcessor` ze souboru `.config` všechny uzla.
2. Ke konfiguraci adaptivního vzorkování použijte následující úryvek:

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

    ([Další informace o telemetrických procesorech](../../azure-monitor/app/api-filtering-sampling.md#filtering).)

Můžete také upravit vzorkovací frekvenci pro každý typ telemetrie jednotlivě nebo můžete dokonce vyloučit určité typy z odběru vzorků vůbec:

```csharp
// The following configures adaptive sampling with 5 items per second, and also excludes Dependency telemetry from being subjected to sampling.
builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");
```

### <a name="configuring-adaptive-sampling-for-aspnet-core-applications"></a>Konfigurace adaptivního vzorkování pro aplikace ASP.NET Core

Neexistuje žádný `ApplicationInsights.config` pro ASP.NET základní aplikace, takže veškerá konfigurace se provádí pomocí kódu.
Adaptivní vzorkování je ve výchozím nastavení povoleno pro všechny aplikace ASP.NET Core. Můžete zakázat nebo přizpůsobit vzorkování chování.

#### <a name="turning-off-adaptive-sampling"></a>Vypnutí adaptivního vzorkování

Výchozí funkci vzorkování lze zakázat při přidávání `ConfigureServices`služby Application Insights v metodě pomocí `ApplicationInsightsServiceOptions` souboru: `Startup.cs`

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

Výše uvedený kód zakáže adaptivní vzorkování. Podle následujících kroků přidejte vzorkování s dalšími možnostmi přizpůsobení.

#### <a name="configure-sampling-settings"></a>Konfigurace nastavení vzorkování

Pomocí rozšiřujících `TelemetryProcessorChainBuilder` metod, jak je znázorněno níže přizpůsobit vzorkování chování.

> [!IMPORTANT]
> Pokud použijete tuto metodu ke konfiguraci `aiOptions.EnableAdaptiveSampling` vzorkování, ujistěte se, že nastavit vlastnost `false` při volání `AddApplicationInsightsTelemetry()`.

```csharp
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

### <a name="configuring-adaptive-sampling-for-azure-functions"></a>Konfigurace adaptivního vzorkování pro funkce Azure

Podle pokynů z [této stránky](https://docs.microsoft.com/azure/azure-functions/functions-monitoring#configure-sampling) nakonfigurujte adaptivní vzorkování pro aplikace spuštěné v Azure Functions.

## <a name="fixed-rate-sampling"></a>Odběr vzorků s pevnou frekvencí

Vzorkování s pevnou rychlostí snižuje provoz odeslaný z webového serveru a webových prohlížečů. Na rozdíl od adaptivního vzorkování snižuje telemetrii pevnou sazbou, o které jste rozhodli. Vzorkování s pevnou rychlostí je k dispozici pro aplikace ASP.NET, ASP.NET Core, Java a Python.

Stejně jako ostatní techniky odběru vzorků, to také zachovává související položky. Také synchronizuje vzorkování klienta a serveru tak, aby byly zachovány související položky - například při pohledu na zobrazení stránky ve vyhledávání můžete najít jeho související požadavky na server. 

V Průzkumníku metrik se sazby, jako je počet požadavků a výjimek, násobí koeficientem, který kompenzuje vzorkovací frekvenci, takže jsou přibližně správné.

### <a name="configuring-fixed-rate-sampling-for-aspnet-applications"></a>Konfigurace vzorkování s pevnou rychlostí pro ASP.NET aplikace

1. **Zakázat adaptivní vzorkování**: [`ApplicationInsights.config`](../../azure-monitor/app/configuration-with-applicationinsights-config.md)V `AdaptiveSamplingTelemetryProcessor` , odebrat nebo komentovat uzel.

    ```xml
    <TelemetryProcessors>
        <!-- Disabled adaptive sampling:
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
        </Add>
        -->
    ```

2. **Povolte modul vzorkování s pevnou frekvencí.** Přidejte tento úryvek do : [`ApplicationInsights.config`](../../azure-monitor/app/configuration-with-applicationinsights-config.md)
   
    ```XML
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <!-- Set a percentage close to 100/N where N is an integer. -->
            <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
            <SamplingPercentage>10</SamplingPercentage>
        </Add>
    </TelemetryProcessors>
    ```

      Případně místo nastavení parametru vzorkování `ApplicationInsights.config` v souboru můžete programově nastavit tyto hodnoty:

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

    ([Další informace o telemetrických procesorech](../../azure-monitor/app/api-filtering-sampling.md#filtering).)

### <a name="configuring-fixed-rate-sampling-for-aspnet-core-applications"></a>Konfigurace vzorkování s pevnou rychlostí pro aplikace ASP.NET Core

1. **Zakázat adaptivní vzorkování**: `ConfigureServices` Změny lze `ApplicationInsightsServiceOptions`provést v metodě pomocí :

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

2. **Povolte modul vzorkování s pevnou frekvencí.** Změny lze provést `Configure` v metodě, jak je znázorněno na níže uvedeném fragmentu:

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        var configuration = app.ApplicationServices.GetService<TelemetryConfiguration>();

        var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
        // For older versions of the Application Insights SDK, use the following line instead:
        // var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;

        // Using fixed rate sampling   
        double fixedSamplingPercentage = 10;
        builder.UseSampling(fixedSamplingPercentage);

        builder.Build();

        // ...
    }
    ```

### <a name="configuring-fixed-rate-sampling-for-java-applications"></a>Konfigurace vzorkování s pevnou rychlostí pro aplikace java

Ve výchozím nastavení není v sadě Java SDK povoleno žádné vzorkování. V současné době podporuje pouze vzorkování s pevnou rychlostí. Adaptivní vzorkování není v sadě Java SDK podporováno.

1. Stáhněte a nakonfigurujte webovou aplikaci pomocí nejnovější [sady Application Insights Java SDK](../../azure-monitor/app/java-get-started.md).

2. **Povolte modul vzorkování s pevnou frekvencí** přidáním následujícího `ApplicationInsights.xml` fragmentu do souboru:

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

3. Můžete zahrnout nebo vyloučit určité typy telemetrie z `Processor` vzorkování `FixedRateSamplingTelemetryProcessor`pomocí následujících značek uvnitř značky :
   
    ```XML
    <ExcludedTypes>
        <ExcludedType>Request</ExcludedType>
    </ExcludedTypes>

    <IncludedTypes>
        <IncludedType>Exception</IncludedType>
    </IncludedTypes>
    ```

Typy telemetrie, které mohou být zahrnuty `Dependency` `Event`nebo `Exception` `PageView`vyloučeny z odběru vzorků, jsou: , , , `Request`, , a `Trace`.

> [!NOTE]
> Pro procento odběru vzorků zvolte procento, které se blíží 100/N, kde N je celé číslo.  V současné době vzorkování nepodporuje jiné hodnoty.

### <a name="configuring-fixed-rate-sampling-for-opencensus-python-applications"></a>Konfigurace vzorkování s pevnou frekvencí pro aplikace OpenCensus Python

Instrumentujte svou aplikaci s nejnovějšími [exportéry OpenCensus Azure Monitor](../../azure-monitor/app/opencensus-python.md).

> [!NOTE]
> Pro vývozce metrik není k dispozici výběr ový odběr vzorků s pevnou sazbou. To znamená, že vlastní metriky jsou jedinými typy telemetrie, kde nelze nakonfigurovat vzorkování. Vývozce metrik odešle všechny telemetrie, které sleduje.

#### <a name="fixed-rate-sampling-for-tracing"></a>Odběr vzorků s pevnou mírou pro trasování ####
V rámci konfigurace `Tracer` můžete zadat vzorkovník `sampler`. Pokud není k dispozici žádný `ProbabilitySampler` explicitní sampler, bude použit ve výchozím nastavení. Ve `ProbabilitySampler` výchozím nastavení by se používala rychlost 1/10000, což znamená, že jeden z každých 10000 požadavků bude odeslán do Application Insights. Informace o zadání vzorkovací frekvence najdete níže.

Chcete-li určit vzorkovací `Tracer` frekvenci, ujistěte se, že určuje vzorkovač s vzorkovací frekvencí mezi 0,0 a 1,0 včetně. Vzorkovací frekvence 1,0 představuje 100 %, což znamená, že všechny vaše požadavky budou odeslány jako telemetrie do Application Insights.

```python
tracer = Tracer(
    exporter=AzureExporter(
        instrumentation_key='00000000-0000-0000-0000-000000000000',
    ),
    sampler=ProbabilitySampler(1.0),
)
```

#### <a name="fixed-rate-sampling-for-logs"></a>Odběr vzorků s pevnou rychlostí pro protokoly ####
Vzorkování s pevnou `AzureLogHandler` rychlostí můžete `logging_sampling_rate` nakonfigurovat úpravou volitelného argumentu. Pokud není zadán žádný argument, bude použita vzorkovací frekvence 1,0. Vzorkovací frekvence 1,0 představuje 100 %, což znamená, že všechny vaše požadavky budou odeslány jako telemetrie do Application Insights.

```python
exporter = metrics_exporter.new_metrics_exporter(
    instrumentation_key='00000000-0000-0000-0000-000000000000',
    logging_sampling_rate=0.5,
)
```

### <a name="configuring-fixed-rate-sampling-for-web-pages-with-javascript"></a>Konfigurace vzorkování s pevnou frekvencí pro webové stránky pomocí JavaScriptu

Webové stránky založené na JavaScriptu lze nakonfigurovat tak, aby používaly Application Insights. Telemetrie je odesílána z klientské aplikace spuštěné v prohlížeči uživatele a stránky mohou být hostovány z libovolného serveru.

Při [konfiguraci webových stránek založených na JavaScriptu pro Application Insights](javascript.md)upravte fragment JavaScriptu, který získáte z portálu Application Insights.

> [!TIP]
> V ASP.NET aplikací s javascriptem v ceně, `_Layout.cshtml`výstřižek obvykle jde v .

Vložte řádek `samplingPercentage: 10,` jako před klávesu instrumentace:

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

Pro procento odběru vzorků zvolte procento, které se blíží 100/N, kde N je celé číslo. V současné době vzorkování nepodporuje jiné hodnoty.

#### <a name="coordinating-server-side-and-client-side-sampling"></a>Koordinace vzorkování na straně serveru a klienta

Sada JavaScript SDK na straně klienta se účastní vzorkování s pevnou rychlostí ve spojení se sadou SDK na straně serveru. Instrumentované stránky budou odesílat pouze telemetrii na straně klienta od stejného uživatele, pro kterého se sada SDK na straně serveru rozhodla zahrnout do vzorkování. Tato logika je navržena tak, aby udržovala integritu uživatelských relací napříč aplikacemi na straně klienta a serveru. V důsledku toho z jakékoli konkrétní položky telemetrie v Application Insights můžete najít všechny ostatní položky telemetrie pro tohoto uživatele nebo relace a ve vyhledávání, můžete přejít mezi související zobrazení stránek a požadavky.

Pokud telemetrie klienta a serveru nezobrazuje koordinované ukázky:

* Ověřte, zda jste povolili vzorkování na serveru i v klientovi.
* Zkontrolujte, zda jste nastavili stejné procento vzorkování v klientovi i na serveru.
* Ujistěte se, že verze sady SDK je 2.0 nebo vyšší.

## <a name="ingestion-sampling"></a>Odběr vzorků z požití

Vzorkování ingestování funguje v místě, kde telemetrie z webového serveru, prohlížečů a zařízení dosáhne koncového bodu služby Application Insights. I když nesnižuje přenosy telemetrie odeslané z vaší aplikace, snižuje množství zpracované a uchované (a účtované) application insights.

Tento typ vzorkování použijte, pokud vaše aplikace často překračuje svou měsíční kvótu a nemáte možnost použít některý z typů vzorkování založených na sdk. 

Nastavte vzorkovací frekvenci na stránce Využití a odhadované náklady:

![V okně Přehled aplikace klikněte na Nastavení, Kvóta, Ukázky, vyberte vzorkovací frekvenci a klikněte na Aktualizovat.](./media/sampling/data-sampling.png)

Stejně jako jiné typy vzorkování algoritmus zachovává související položky telemetrie. Například při kontrole telemetrie ve vyhledávání, budete moci najít požadavek vztahující se k určité výjimky. Počty metrik, jako je například míra požadavků a míra výjimek, jsou správně zachovány.

Datové body, které jsou zahozeny vzorkováním, nejsou k dispozici v žádné funkci Přehledy aplikací, například [nepřetržitý export](../../azure-monitor/app/export-telemetry.md).

Vzorkování požití nefunguje, když je v provozu adaptivní nebo stabilní vzorkování. Adaptivní vzorkování je ve výchozím nastavení povoleno, když se používá ASP.NET SDK nebo ASP.NET základní sady SDK nebo když je application insights povolená ve [službě Azure App Service](azure-web-apps.md) nebo pomocí sledování stavu. Když je telemetrie přijata koncovým bodem služby Application Insights, zkontroluje telemetrii a pokud je vzorkovací frekvence hlášena menší než 100 % (což znamená, že se vzorkuje telemetrie), je rychlost vzorkování při přijetí, kterou nastavíte, ignorována.

> [!WARNING]
> Hodnota zobrazená na dlaždici portálu označuje hodnotu, kterou jste nastavili pro vzorkování při ingestování. Nepředstavuje skutečnou vzorkovací frekvenci, pokud je v provozu jakýkoli druh vzorkování Sady SDK (adaptivní nebo vzorkování s pevnou frekvencí).

## <a name="when-to-use-sampling"></a>Kdy použít vzorkování

Obecně platí, že pro většinu malých a středních aplikací nepotřebujete vzorkování. Nejužitečnější diagnostické informace a nejpřesnější statistiky jsou získávány shromažďováním údajů o všech vašich uživatelských aktivitách. 

Hlavní výhody odběru vzorků jsou:

* Služba Application Insights klesne ("omezení") datových bodů, když vaše aplikace odešle velmi vysokou rychlost telemetrie v krátkém časovém intervalu. Vzorkování snižuje pravděpodobnost, že aplikace bude vidět omezení dojít.
* Chcete-li zachovat v rámci [kvóty](pricing.md) datových bodů pro cenovou úroveň. 
* Chcete-li snížit zatížení sítě z kolekce telemetrie. 

### <a name="which-type-of-sampling-should-i-use"></a>Jaký typ odběru vzorků mám použít?

**Odběr vzorků z požití použijte, pokud:**

* Často používáte měsíční kvótu telemetrie.
* Z webových prohlížečů uživatelů získáváte příliš mnoho telemetrie.
* Používáte verzi sady SDK, která nepodporuje vzorkování – například ASP.NET verze starší než 2.

**Odběr vzorků s pevnou rychlostí se použije, pokud:**

* Chcete synchronizované vzorkování mezi klientem a serverem tak, aby při zkoumání událostí ve [vyhledávání](../../azure-monitor/app/diagnostic-search.md), můžete přejít mezi související události na straně klienta a serveru, jako jsou zobrazení stránek a požadavky HTTP.
* Jste si jisti, že příslušné procento vzorkování pro vaši aplikaci. Měla by být dostatečně vysoká, aby získala přesné metriky, ale pod sazbou, která překračuje vaši cenovou kvótu a omezení omezení.

**Používejte adaptivní vzorkování:**

Pokud podmínky pro použití jiných forem odběru vzorků neplatí, doporučujeme adaptivní odběr vzorků. Toto nastavení je ve výchozím nastavení povoleno v ASP.NET/ASP.NET sady Core SDK. Nesníží provoz, dokud nebude dosaženo určité minimální sazby, a proto místa s nízkým využitím pravděpodobně nebudou vzorkována vůbec.

## <a name="knowing-whether-sampling-is-in-operation"></a>Vědět, zda je v provozu vzorkování

Chcete-li zjistit skutečnou vzorkovací frekvenci bez ohledu na to, kde byla použita, použijte [dotaz Analytics,](../../azure-monitor/app/analytics.md) například tento:

```kusto
union requests,dependencies,pageViews,browserTimings,exceptions,traces
| where timestamp > ago(1d)
| summarize RetainedPercentage = 100/avg(itemCount) by bin(timestamp, 1h), itemType
```

Pokud zjistíte, že `RetainedPercentage` pro jakýkoli typ je menší než 100, pak tento typ telemetrie je vzorkován.

> [!IMPORTANT]
> Application Insights nevzorku relace, metriky (včetně vlastní metriky) nebo výkon čítač telemetrie typy v některé z technik vzorkování. Tyto typy jsou vždy vyloučeny z vzorkování jako snížení přesnosti může být velmi nežádoucí pro tyto typy telemetrie.

## <a name="how-sampling-works"></a>Jak odběr vzorků funguje

Algoritmus vzorkování určuje, které položky telemetrie k přetažení a které z nich zachovat. To platí bez ohledu na to, zda vzorkování provádí sada SDK nebo služba Application Insights. Rozhodnutí o odběru vzorků je založeno na několika pravidlech, jejichž cílem je zachovat všechny vzájemně propojené datové body beze změny, a udržovat diagnostické prostředí v Application Insights, které je žalovatelné a spolehlivé i při omezené množině dat. Například pokud vaše aplikace má neúspěšný požadavek zahrnuty v ukázce, další položky telemetrie (například výjimky a trasování protokolované pro tento požadavek) budou zachovány. Vzorkování buď udržuje nebo klesá je všechny dohromady. V důsledku toho se při pohledu na podrobnosti požadavku v Application Insights, můžete vždy zobrazit požadavek spolu s jeho přidružené položky telemetrie.

Rozhodnutí o odběru vzorků je založeno na ID operace požadavku, což znamená, že všechny položky telemetrie patřící do určité operace jsou buď zachovány, nebo vynechány. Pro položky telemetrie, které nemají sadu ID operace (například položky telemetrie hlášené z asynchronních vláken bez kontextu PROTOKOLU HTTP) vzorkování jednoduše zachytí procento položek telemetrie každého typu.

Při prezentaci telemetrie zpět k vám, Application Insights služba upraví metriky podle stejné procento vzorkování, který byl použit v době kolekce, kompenzovat chybějící datové body. Proto při pohledu na telemetrii v Application Insights, uživatelé vidí statisticky správné aproximace, které jsou velmi blízko k reálným číslům.

Přesnost aproximace do značné míry závisí na nakonfigurovaném procentu odběru vzorků. Také se zvyšuje přesnost pro aplikace, které zpracovávají velký objem obecně podobných požadavků od mnoha uživatelů. Na druhou stranu pro aplikace, které nepracují s významným zatížením, vzorkování není potřeba, protože tyto aplikace mohou obvykle odesílat všechny své telemetrie při zachování kvóty, aniž by došlo ke ztrátě dat z omezení. 

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

*Jaké je výchozí vzorkování chování v ASP.NET a ASP.NET jádra sady SDK?*

* Pokud používáte jednu z nejnovějších verzí výše uvedené sady SDK, adaptivní vzorkování je ve výchozím nastavení povoleno s pěti položkami telemetrie za sekundu.
  Ve výchozím `AdaptiveSamplingTelemetryProcessor` nastavení jsou přidány dva uzly a jeden zahrnuje `Event` typ `Event` vzorkování, zatímco druhý vylučuje typ z odběru vzorků. Tato konfigurace znamená, že sada SDK se pokusí omezit `Event` položky telemetrie na pět položek telemetrie typů `Events` a pět položek telemetrie všech ostatních typů v kombinaci, čímž zajistí, že jsou vzorkovány odděleně od jiných typů telemetrie. Události se obvykle používají pro obchodní telemetrie a s největší pravděpodobností by neměly být ovlivněny diagnostické telemetrické svazky.
  
  Následující obrázek `ApplicationInsights.config` ukazuje výchozí soubor vygenerovaný. V ASP.NET Core je v kódu povoleno stejné výchozí chování. Pomocí [příkladů v předchozí části této stránky](#configuring-adaptive-sampling-for-aspnet-core-applications) změňte toto výchozí chování.

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

*Může být telemetrie vzorkována více než jednou?*

* Ne. SamplingTelemetryProcessors ignorují položky z aspekty vzorkování, pokud je položka již vzorkována. Totéž platí i pro vzorkování při požití, které nepoužije vzorkování na položky, které již byly odebrány v samotné sadě SDK.

*Proč není vzorkování jednoduché "sbírat X procent každého typu telemetrie"?*

* Zatímco tento přístup vzorkování by poskytnout vysokou úroveň přesnosti v aproximace metriky, by to přerušit schopnost korelovat diagnostická data na uživatele, relace a požadavku, což je důležité pro diagnostiku. Vzorkování proto funguje lépe se zásadami, jako je "shromažďovat všechny položky telemetrie pro X procent uživatelů aplikace", nebo "shromažďovat všechny telemetrie pro X procent žádostí o aplikace". Pro položky telemetrie, které nejsou přidruženy k požadavkům (například na pozadí asynchronní zpracování), záložní je "shromažďovat x procent všech položek pro každý typ telemetrie." 

*Může se procento vzorkování v průběhu času měnit?*

* Ano, adaptivní vzorkování postupně mění procento vzorkování na základě aktuálně pozorovaného objemu telemetrie.

*Pokud používám vzorkování s pevnou rychlostí, jak zjistím, které procento vzorkování bude pro mou aplikaci nejlepší?*

* Jedním ze způsobů je začít s adaptivním vzorkováním, zjistit, na jaké rychlosti se usadí (viz výše uvedená otázka) a pak přepnout na odběr vzorků s pevnou frekvencí pomocí této rychlosti. 
  
    Jinak musíš hádat. Analyzujte aktuální využití telemetrie v Application Insights, sledujte jakékoli omezení, ke kterému dochází, a odhadněte objem shromážděné telemetrie. Tyto tři vstupy spolu s vybranou cenovou úrovní naznačují, kolik můžete chtít snížit objem shromážděné telemetrie. Zvýšení počtu uživatelů nebo jiný posun ve objemu telemetrie však může zneplatnit váš odhad.

*Co se stane, když nakonfiguruji procento vzorkování tak, aby bylo příliš nízké?*

* Příliš nízké procento vzorkování způsobuje příliš agresivní vzorkování a snižuje přesnost aproximací při pokusu application insights kompenzovat vizualizaci dat pro snížení objemu dat. Také vaše diagnostické zkušenosti mohou být negativně ovlivněny, protože některé zřídka selhávající nebo pomalé požadavky mohou být odebrány.

*Co se stane, když nakonfiguruji procento vzorkování tak, aby bylo příliš vysoké?*

* Konfigurace příliš vysoké procento vzorkování (není dostatečně agresivní) má za následek nedostatečné snížení objemu shromážděné telemetrie. Stále může dojít ke ztrátě telemetrie dat související s omezením a náklady na používání Application Insights může být vyšší, než jste plánovali z důvodu poplatků za nadbytečné.

*Na jakých platformách mohu vzorkování používat?*

* Vzorkování při požití může dojít automaticky pro všechny telemetrie nad určitý svazek, pokud sada SDK neprovádí vzorkování. Tato konfigurace by fungovala například v případě, že používáte starší verzi sady ASP.NET Sady SDK nebo sady Java SDK.
* Pokud používáte aktuální ASP.NET nebo ASP.NET sady Core SDK (hostované buď v Azure nebo na vlastním serveru), získáte adaptivní vzorkování ve výchozím nastavení, ale můžete přepnout na pevnou sazbu, jak je popsáno výše. Při vzorkování s pevnou rychlostí se sada SDK prohlížeče automaticky synchronizuje s ukázkovými událostmi. 
* Pokud používáte aktuální sadu Java SDK, můžete nakonfigurovat `ApplicationInsights.xml` zapnutí vzorkování s pevnou frekvencí. Vzorkování je ve výchozím nastavení vypnuto. Při vzorkování s pevnou rychlostí se sada SDK prohlížeče a server automaticky synchronizují s ukázkovými událostmi.

*Jsou tu vzácné události, které chci vždycky vidět. Jak je mohu dostat přes vzorkovací modul?*

* Nejlepší způsob, jak toho dosáhnout, je napsat vlastní [TelemetryInitializer](../../azure-monitor/app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer), který nastaví `SamplingPercentage` na 100 na položku telemetrie, kterou chcete zachovat, jak je znázorněno níže. Jako inicializátory jsou zaručeně spustit před telemetrická procesory (včetně vzorkování), to zajišťuje, že všechny vzorkovací techniky bude ignorovat tuto položku ze všech úvah vzorkování. Vlastní inicializátory telemetrie jsou k dispozici v ASP.NET SDK, ASP.NET Core SDK, JavaScript SDK a Java SDK. Můžete například nakonfigurovat inicializátor telemetrie pomocí sady ASP.NET SDK:

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

Adaptivní vzorkování je k dispozici pro sadu Application Insights SDK pro ASP.NET v2.0.0-beta3 a novější, Microsoft.ApplicationInsights.AspNetCore SDK v2.2.0-beta1 a novější a je ve výchozím nastavení povolena.

Vzorkování s pevnou rychlostí je součástí sady SDK v ASP.NET verzích od 2.0.0 a Java SDK verze 2.0.1 a dále.

Před v2.5.0-beta2 ASP.NET SDK a v2.2.0-beta3 ASP.NET Core SDK bylo rozhodnutí o vzorkování založeno na algoritmu hash ID uživatele pro aplikace, které definují "uživatel" (to znamená nejtypičtější webové aplikace). Pro typy aplikací, které nedefinovaly uživatele (například webové služby), bylo rozhodnutí o vzorkování založeno na ID operace požadavku. Poslední verze sad ASP.NET a ASP.NET sad Core SDK používají id operace pro rozhodnutí o odběru vzorků.

## <a name="next-steps"></a>Další kroky

* [Filtrování](../../azure-monitor/app/api-filtering-sampling.md) může poskytnout přísnější kontrolu nad tím, co vaše sada SDK odesílá.
* Přečtěte si článek Síť vývojářů [Optimalizace telemetrie pomocí application insights](https://msdn.microsoft.com/magazine/mt808502.aspx).
