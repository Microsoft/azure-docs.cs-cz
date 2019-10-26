---
title: Vzorkování telemetrie v Azure Application Insights | Microsoft Docs
description: Jak udržet množství telemetrie v rámci řízení.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: cijothomas
ms.author: cithomas
ms.date: 03/14/2019
ms.reviewer: vitalyg
ms.openlocfilehash: 82c0855e3ea3b6a89c1b20569971b0dc6b3d449c
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899859"
---
# <a name="sampling-in-application-insights"></a>Vzorkování ve službě Application Insights

Vzorkování je funkce v [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). Je doporučený způsob, jak omezit provoz telemetrie a úložiště, a přitom zachovává statisticky správnou analýzu dat aplikací. Filtr vybere položky, které jsou v relaci, takže můžete přecházet mezi položkami při provádění diagnostických šetření.
Když se počty metrik zobrazují na portálu, jsou znovu normalizovány, aby se braly v úvahu vzorkování. Tím se minimalizují všechny vlivy na statistiky.

Vzorkování snižuje náklady na provoz a data a pomáhá vyhnout se omezování.

## <a name="in-brief"></a>V krátkém případě:

* Vzorkování zachovává 1 v *n* záznamech a zahodí zbytek. Například může uchovávat jednu z pěti událostí, vzorkovací frekvenci 20%. 
* Adaptivní vzorkování je ve výchozím nastavení povolené ve všech nejnovějších verzích ASP.NET ASP.NET Core a sadách SDK (Software Development Kit).
* Vzorkování můžete také nastavit ručně. To se dá nakonfigurovat na portálu na *stránce využití a odhadované náklady*v sadě ASP.NET SDK v souboru ApplicationInsights. config v sadě sdk pro ASP.NET Core pomocí kódu nebo v sadě Java SDK v souboru ApplicationInsights. XML.
* Pokud protokolovat vlastní události a potřebujete zajistit, aby byla sada událostí zachována nebo zahozena společně, musí mít události stejnou hodnotu OperationId.
* U každého záznamu ve vlastnosti `itemCount` je hlášený dělitel vzorků *n* , který se v hledání zobrazí pod popisným názvem "počet požadavků" nebo "počet událostí". `itemCount==1`, pokud vzorkování není v operaci.
* Pokud píšete analytické dotazy, měli byste [vzít v úvahu vzorkování](../../azure-monitor/log-query/aggregations.md). Konkrétně místo pouhého počítání záznamů byste měli použít `summarize sum(itemCount)`.

## <a name="types-of-sampling"></a>Typy vzorkování

Existují tři alternativní metody vzorkování:

* **Adaptivní vzorkování** automaticky upravuje objem telemetrie odeslaných ze sady SDK ve vaší aplikaci ASP.NET/ASP.NET Core. Toto je výchozí vzorkování z ASP.NET Web SDK v 2.0.0-beta3 a vyšší a Microsoft. ApplicationInsights. AspNetCore SDK v 2.2.0-Beta1 a vyšší.  Adaptivní vzorkování je aktuálně dostupné jenom pro ASP.NET telemetrie na straně serveru.

* **Vzorkování s pevnou sazbou** snižuje objem telemetrie odeslaných z ASP.NET ASP.NET Core nebo z vašeho serveru Java i z prohlížečů uživatelů. Nastavte sazbu. Klient a Server budou synchronizovat své vzorkování, takže v hledání můžete procházet mezi souvisejícími zobrazeními stránky a požadavky.

* **Vzorkování přijímání** Funguje v Azure Portal. Zahodí některé telemetrie, které dorazí z vaší aplikace, při vzorkovací frekvenci, kterou jste nastavili. Neomezuje provoz telemetrie odeslaný z vaší aplikace, ale pomáhá udržet se v rámci měsíční kvóty. Hlavní výhodou pro vzorkování ingestování je, že můžete nastavit vzorkovací frekvenci bez opětovného nasazení aplikace. Vzorkování ingestování funguje jednotně pro všechny servery a klienty.

Pokud je v provozu vzorkování s adaptivní nebo pevnou sazbou, vzorkování ingestování je zakázané.


## <a name="adaptive-sampling-in-your-aspnetaspnet-core-web-applications"></a>Adaptivní vzorkování ve webových aplikacích ASP.NET/ASP.NET Core

Adaptivní vzorkování je k dispozici pro sadu Application Insights SDK pro ASP.NET v 2.0.0-beta3 a novější, Microsoft. ApplicationInsights. AspNetCore SDK v 2.2.0-Beta1 a novější a je ve výchozím nastavení povolená.

Adaptivní vzorkování ovlivňuje objem telemetrie odeslaných z aplikace webového serveru do koncového bodu služby Application Insights. Svazek se automaticky upraví tak, aby udržoval v rámci zadané maximální míry provozu a byl řízen pomocí nastavení `MaxTelemetryItemsPerSecond`. Pokud aplikace vytvoří nízké množství telemetrie, například při ladění nebo z důvodu nízkého využití, nebudou položky pomocí procesoru vzorkování vyhozeny, pokud je svazek pod `MaxTelemetryItemsPerSecond`. Po zvýšení objemu telemetrie se vzorkovací frekvence upraví tak, aby se dosáhlo cílového svazku.

Aby bylo možné dosáhnout cílového svazku, některá z vygenerované telemetrie jsou zahozena. Ale podobně jako jiné typy vzorkování, algoritmus zachovává související položky telemetrie. Například při kontrole telemetrie ve službě Search budete moci najít požadavek související s konkrétní výjimkou.

Počty metrik, jako je míra požadavků a četnosti výjimek, se upravují tak, aby vyrovnaly vzorkovací frekvenci, aby v Průzkumníkovi metrik zobrazovaly přibližně správné hodnoty.

## <a name="configuring-adaptive-sampling-for-aspnet-applications"></a>Konfigurace adaptivního vzorkování pro aplikace ASP.NET

[Přečtěte si](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications) o konfiguraci adaptivního vzorkování pro aplikace ASP.NET Core. 

V [ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md)můžete upravit několik parametrů v uzlu `AdaptiveSamplingTelemetryProcessor`. Zobrazené hodnoty jsou výchozí hodnoty:

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`
  
    Cílová frekvence, pro kterou se adaptivní algoritmus zaměřuje **na každého hostitele serveru**. Pokud vaše webová aplikace běží na mnoha hostitelích, snižte tuto hodnotu tak, aby zůstala v rámci cílové míry provozu na portálu Application Insights.
* `<EvaluationInterval>00:00:15</EvaluationInterval>` 
  
    Interval, ve kterém je aktuální frekvence telemetrie znovu vyhodnocena. Hodnocení se provádí jako klouzavý průměr. Pokud je vaše telemetrie příčinou náhlého nárůstu, možná budete chtít tento interval zkrátit.
* `<SamplingPercentageDecreaseTimeout>00:02:00</SamplingPercentageDecreaseTimeout>`
  
    Když se změní procentuální hodnota vzorkování, jak brzy bude povoleno snížit procento vzorkování znovu a zachytit tak méně dat.
* `<SamplingPercentageIncreaseTimeout>00:15:00</SamplingPercentageIncreaseTimeout>`
  
    Když se změní procentuální hodnota vzorkování, jak brzy bude povoleno zvýšit procento vzorkování, aby se zachytí více dat.
* `<MinSamplingPercentage>0.1</MinSamplingPercentage>`
  
    Jelikož procento vzorkování se liší, jaká je minimální hodnota, kterou je možné nastavit.
* `<MaxSamplingPercentage>100.0</MaxSamplingPercentage>`
  
    Jelikož procento vzorkování se liší, jaká je maximální hodnota, kterou je možné nastavit.
* `<MovingAverageRatio>0.25</MovingAverageRatio>` 
  
    Při výpočtu klouzavého průměru je váha přiřazená k nejnovější hodnotě. Použijte hodnotu rovnou nebo menší než 1. Menší hodnoty nastaví algoritmus méně aktivní na náhlé změny.
* `<InitialSamplingPercentage>100</InitialSamplingPercentage>`
  
    Hodnota přiřazená při právě spuštění aplikace Při ladění nezmenšujte hodnotu.

* `<ExcludedTypes>Trace;Exception</ExcludedTypes>`
  
    Středníkem oddělený seznam typů, které nechcete vzorkovat. Rozpoznané typy jsou: závislost, událost, výjimka, PageView, požadavek, trasování. Jsou přenášeny všechny instance zadaných typů; typy, které nejsou zadány, jsou vzorkované.

* `<IncludedTypes>Request;Dependency</IncludedTypes>`
  
    Středníkem oddělený seznam typů, které chcete vzorkovat. Rozpoznané typy jsou: závislost, událost, výjimka, PageView, požadavek, trasování. Zadané typy jsou vzorkovat; všechny instance ostatních typů budou vždy přeneseny.


**Chcete-li přepnout** adaptivní vzorkování, odeberte uzly AdaptiveSamplingTelemetryProcessor z ApplicationInsights-config.

### <a name="alternative-configure-adaptive-sampling-in-code"></a>Alternativa: Konfigurace adaptivního vzorkování v kódu

Místo nastavení parametru vzorkování v souboru. config můžete tyto hodnoty programově nastavit.

1. Odeberte všechny uzly `AdaptiveSamplingTelemetryProcessor` ze souboru. config.
2. Pro konfiguraci adaptivního vzorkování použijte následující fragment kódu.

*C#*

```csharp

    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.Channel.Implementation;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    ...

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;
    // If you are on ApplicationInsights SDK v 2.8.0-beta2 or higher, use the following line instead
    // var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;

    // Enable AdaptiveSampling so as to keep overall telemetry volume to 5 items per second.
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5);

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

([Další informace o procesorech telemetrie](../../azure-monitor/app/api-filtering-sampling.md#filtering).)

Můžete také upravit vzorkovací frekvenci pro každý typ telemetrie jednotlivě nebo můžete dokonce vyloučit určité typy ze vzorků. 

*C#*

```csharp
    // The following configures adaptive sampling with 5 items per second, and also excludes DependencyTelemetry from being subject to sampling.
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");
```

## <a name="configuring-adaptive-sampling-for-aspnet-core-applications"></a>Konfigurace adaptivního vzorkování pro aplikace ASP.NET Core.

Pro ASP.NET Core aplikace neexistuje žádná `ApplicationInsights.Config`, takže každá konfigurace se provádí prostřednictvím kódu.
Adaptivní vzorkování je ve výchozím nastavení povolené pro všechny aplikace ASP.NET Core. Můžete zakázat nebo upravit chování vzorkování.

### <a name="turning-off-adaptive-sampling"></a>Vypnutí adaptivního vzorkování

Při přidávání Application Insights služby je možné zakázat výchozí funkci vzorkování, v metodě ```ConfigureServices``` pomocí ```ApplicationInsightsServiceOptions``` v rámci souboru `Startup.cs`:

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

Výše uvedený kód zakáže funkci vzorkování. Pomocí následujících kroků přidejte vzorkování s dalšími možnostmi přizpůsobení.

### <a name="configure-sampling-settings"></a>Konfigurovat nastavení vzorkování

K přizpůsobení chování vzorkování použijte metody rozšíření ```TelemetryProcessorChainBuilder```, jak je znázorněno níže.

> [!IMPORTANT]
> Pokud tuto metodu použijete ke konfiguraci vzorkování, ujistěte se prosím, že používáte aiOptions. EnableAdaptiveSampling = false; nastavení pomocí AddApplicationInsightsTelemetry ().

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
{
    var builder = configuration.TelemetryProcessorChainBuilder;
    // version 2.5.0-beta2 and above should use the following line instead of above. (https://github.com/Microsoft/ApplicationInsights-aspnetcore/blob/develop/CHANGELOG.md#version-250-beta2)
    // var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;

    // Using adaptive sampling
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5);

    // Alternately, the following configures adaptive sampling with 5 items per second, and also excludes DependencyTelemetry from being subject to sampling.
    // builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");

    builder.Build();

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));
    // ...
}

```

**Pokud ke konfiguraci vzorkování použijete výše uvedenou metodu, nezapomeňte použít nastavení ```aiOptions.EnableAdaptiveSampling = false;``` s AddApplicationInsightsTelemetry ().**

## <a name="fixed-rate-sampling-for-aspnet-aspnet-core-java-websites-and-python-applications"></a>Vzorkování s pevnou sazbou pro ASP.NET, ASP.NET Core, weby Java a aplikace Python

Vzorkování pevné míry omezuje provoz odeslaný z webového serveru a webových prohlížečů. Na rozdíl od adaptivního vzorkování redukuje telemetrie za pevnou sazbu, kterou jste určili. Také synchronizuje vzorkování klienta a serveru, aby se související položky zachovaly – například při zobrazení stránky v hledání můžete najít související žádost.

Podobně jako u jiných technik vzorkování to také uchovává související položky. U každé události požadavku HTTP se žádost a její související události buď zahodí, nebo přenášejí společně.

V Průzkumník metrik se tarify, jako je počet požadavků a výjimek, vynásobí faktorem, který kompenzuje vzorkovací frekvenci, aby byly přibližně správné.

### <a name="configuring-fixed-rate-sampling-in-aspnet"></a>Konfigurace vzorkování s pevnou sazbou v ASP.NET

1. **Disable Adaptive vzorkování**: v [souboru ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md)odstraňte nebo odkomentujte uzel `AdaptiveSamplingTelemetryProcessor`.

    ```xml

    <TelemetryProcessors>

    <!-- Disabled adaptive sampling:
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    -->
    ```

2. **Povolte modul vzorkování s pevnými rychlostmi.** Přidejte tento fragment kódu do [souboru ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md):
   
    ```XML
   
    <TelemetryProcessors>
     <Add  Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
   
      <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
      <SamplingPercentage>10</SamplingPercentage>
      </Add>
    </TelemetryProcessors>
   
    ```
   ### <a name="alternative-enable-fixed-rate-sampling-in-your-server-code"></a>Alternativa: povolení vzorkování s pevnou sazbou v kódu serveru
    
    Místo nastavení parametru vzorkování v souboru. config můžete tyto hodnoty programově nastavit. 

*C#*

```csharp

    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    ...

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;
    // If you are on ApplicationInsights SDK v 2.8.0-beta2 or higher, use the following line instead
    // var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;

    builder.UseSampling(10.0); // percentage

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```
([Další informace o procesorech telemetrie](../../azure-monitor/app/api-filtering-sampling.md#filtering).)

### <a name="configuring-fixed-rate-sampling-in-aspnet-core"></a>Konfigurace vzorkování s pevnou sazbou v ASP.NET Core

1. **Zakázat adaptivní vzorkování**: v metodě ```ConfigureServices``` lze provést změny pomocí ```ApplicationInsightsServiceOptions```:

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

2. **Povolte modul vzorkování s pevnými rychlostmi.** Změny lze provést v metodě ```Configure```, jak je znázorněno v následujícím fragmentu kódu:

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        var configuration = app.ApplicationServices.GetService<TelemetryConfiguration>();

        var builder = configuration.TelemetryProcessorChainBuilder;
        // version 2.5.0-beta2 and above should use the following line instead of above. (https://github.com/Microsoft/ApplicationInsights-aspnetcore/blob/develop/CHANGELOG.md#version-250-beta2)
        // var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;

        // Using fixed rate sampling   
        double fixedSamplingPercentage = 10;
        builder.UseSampling(fixedSamplingPercentage);

        builder.Build();

        // ...
    }

    ```

### <a name="configuring-fixed-rate-sampling-in-java"></a>Konfigurace vzorkování s pevnou sazbou v jazyce JAVA ###

1. Stažení a konfigurace webové aplikace pomocí nejnovější [sady SDK pro Application Insights Java](../../azure-monitor/app/java-get-started.md)

2. **Povolte modul vzorkování s pevnými rychlostmi** přidáním následujícího fragmentu do souboru ApplicationInsights. XML.

    ```XML
        <TelemetryProcessors>
            <BuiltInProcessors>
                <Processor type = "FixedRateSamplingTelemetryProcessor">
                    <!-- Set a percentage close to 100/N where N is an integer. -->
                    <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
                    <Add name = "SamplingPercentage" value = "50" />
                </Processor>
            </BuiltInProcessors>
        </TelemetryProcessors>
    ```

3. Můžete zahrnout nebo vyloučit konkrétní typy telemetrie z vzorkování pomocí následujících značek v rámci značky procesoru "FixedRateSamplingTelemetryProcessor".
    ```XML
        <ExcludedTypes>
            <ExcludedType>Request</ExcludedType>
        </ExcludedTypes>

        <IncludedTypes>
            <IncludedType>Exception</IncludedType>
        </IncludedTypes>
    ```

Typy telemetrie, které lze zahrnout nebo vyloučit z vzorkování, jsou: závislost, událost, výjimka, PageView, požadavek a trasování.

> [!NOTE]
> Pro procento vzorkování vyberte procento, které je blízko 100/N, kde N je celé číslo.  V současné době vzorkování nepodporují jiné hodnoty.
> 
> 

<a name="other-web-pages"></a>

### <a name="configuring-fixed-rate-sampling-in-opencensus-python"></a>Konfigurace vzorkování s pevnou sazbou v OpenCensus Pythonu ###

1. Instrumentujte svoji aplikaci pomocí nejnovějšího [OpenCensus Azure monitor vývozců](../../azure-monitor/app/opencensus-python.md).

> [!NOTE]
> Vzorkování s pevnou sazbou je k dispozici pouze pomocí exportéra sledování. To znamená, že příchozí a odchozí požadavky jsou jediné typy telemetrie, ve kterých je možné nakonfigurovat vzorkování.
> 
> 

2. V rámci konfigurace `Tracer` můžete zadat vzorkovník `sampler`. Pokud není zadaný žádný explicitní vzorkovník, použije se ve výchozím nastavení ProbabilitySampler. ProbabilitySampler by ve výchozím nastavení použila sazbu 1/10000, což znamená, že se do Application Insights pošle jeden z každých 10000 požadavků. Informace o zadání vzorkovací frekvence najdete níže.

3. Při zadávání vzorkovníku se ujistěte, že hodnota `Tracer` určuje vzorkovník se vzorkovací frekvencí od 0.0 do 1.0 (včetně). Vzorkovací frekvence 1,0 představuje 100%, což znamená, že všechny vaše požadavky budou odeslány jako telemetrie do Application Insights.

    ```python
    tracer = Tracer(
        exporter=AzureExporter(
            instrumentation_key='00000000-0000-0000-0000-000000000000',
        ),
        sampler=ProbabilitySampler(1.0),
    )
    ```

## <a name="ingestion-sampling"></a>Vzorkování přijímání

Tato forma vzorkování funguje v okamžiku, kdy telemetrie z webového serveru, prohlížeče a zařízení dosáhne koncového bodu služby Application Insights. I když se tím nesnižuje provoz telemetrie odeslaný z vaší aplikace, snižuje se množství zpracovaných a uchovávaných (a účtuje se za) Application Insights.

Tento typ vzorkování použijte, pokud vaše aplikace často prochází za měsíční kvótu a nemáte možnost použít některý z typů vzorkování založených na sadě SDK. 

Nastavte tempo vzorkování na stránce využití a odhadované náklady:

![V okně Přehled aplikace klikněte na nastavení, kvóta, ukázky, vyberte vzorkovací frekvenci a klikněte na aktualizovat.](./media/sampling/04.png)

Podobně jako jiné typy vzorkování, algoritmus zachovává související položky telemetrie. Například při kontrole telemetrie ve službě Search budete moci najít požadavek související s konkrétní výjimkou. Počty metrik, jako je rychlost požadavků a četnost výjimek, se uchovávají správně.

Datové body, které jsou zahozeny vzorkováním, nejsou k dispozici v žádné Application Insights funkci, jako je například [průběžný export](../../azure-monitor/app/export-telemetry.md).

Vzorkování ingestování nefunguje, pokud je operace vzorkování na základě sady SDK nebo fixních sazeb. Adaptivní vzorkování je ve výchozím nastavení povolené, když je v sadě Visual Studio povolená sada ASP.NET/ASP.NET Core SDK nebo když je povolená v rozšířeních webových aplikací Azure nebo pomocí Monitorování stavu a vzorkování ingestování je zakázané. Pokud je vzorkovací frekvence v sadě SDK menší než 100% (tj. jsou odebírány vzorky položek), nastavené vzorkovací frekvence příjmu se ignorují.

> [!WARNING]
> Hodnota zobrazená na dlaždici označuje hodnotu, kterou jste nastavili pro vzorkování přijímání. Nepředstavuje skutečnou vzorkovací frekvenci, pokud je v provozu sada SDK vzorkování.
>
>
## <a name="sampling-for-web-pages-with-javascript"></a>Vzorkování pro webové stránky pomocí JavaScriptu
Webové stránky pro vzorkování s pevnou sazbou můžete nakonfigurovat z libovolného serveru. 

Když [nakonfigurujete webové stránky pro Application Insights](../../azure-monitor/app/javascript.md), upravte fragment JavaScriptu, který získáte z portálu Application Insights. (V aplikacích ASP.NET je fragment kódu typicky v _Layout. cshtml.)  Vložit řádek jako `samplingPercentage: 10,` před klíč instrumentace:

    <script>
    var appInsights= ... 
    }({ 


    // Value must be 100/N where N is an integer.
    // Valid examples: 50, 25, 20, 10, 5, 1, 0.1, ...
    samplingPercentage: 10, 

    instrumentationKey:...
    }); 

    window.appInsights=appInsights; 
    appInsights.trackPageView(); 
    </script> 

Pro procento vzorkování vyberte procento, které je blízko 100/N, kde N je celé číslo.  V současné době vzorkování nepodporují jiné hodnoty.

Pokud na serveru povolíte vzorkování s pevnou sazbou, budou se klienti a servery synchronizovat, takže v hledání můžete přecházet mezi souvisejícími zobrazeními stránky a požadavky.

## <a name="when-to-use-sampling"></a>Kdy použít vzorkování?

Adaptivní vzorkování se automaticky povolí v nejnovějších sadách SDK pro .NET a .NET Core. Bez ohledu na to, jakou verzi sady SDK použijete, můžete povolit vzorkování ingestování, aby Application Insights mohl vzorkovat shromážděná data.

Ve výchozím nastavení není v sadě Java SDK povolen žádný odběr vzorkování. V současné době podporuje jenom vzorkování pevné míry. Adaptivní vzorkování není v sadě Java SDK podporováno.

Obecně platí pro většinu malých a středně velkých aplikací, které nepotřebujete vzorkování. Nejužitečnější diagnostické informace a nejpřesnější statistiky se získávají shromažďováním dat ze všech vašich uživatelských aktivit. 

Hlavní výhody vzorkování:

* Pokud vaše aplikace odesílá velmi vysokou míru telemetrie v krátkém časovém intervalu, Application Insights služba vychází z datových bodů omezení. 
* Aby se zachovala [kvóta](../../azure-monitor/app/pricing.md) datových bodů pro vaši cenovou úroveň. 
* Pro snížení síťového provozu z kolekce telemetrie. 

### <a name="which-type-of-sampling-should-i-use"></a>Jaký typ vzorkování mám použít?

**Vzorkování ingestování použijte v těchto případech:**

* Často procházíte měsíční kvótou telemetrie.
* Používáte verzi sady SDK, která nepodporuje vzorkování – například verze ASP.NET starší než 2.
* Máte příliš mnoho telemetrie z webových prohlížečů vašich uživatelů.

**Vzorkování s pevnou sazbou použijte v těchto případech:**

* Používáte sadu Application Insights SDK for ASP.NET Web Services verze 2.0.0 nebo novější nebo Java SDK v 2.0.1 nebo novější a
* Chcete synchronizovat vzorkování mezi klientem a serverem, takže při zkoumání událostí ve [vyhledávání](../../azure-monitor/app/diagnostic-search.md)můžete procházet mezi souvisejícími událostmi klienta a serveru, jako jsou například zobrazení stránky a požadavky HTTP.
* Jste si jistí vhodné procento vzorkování pro vaši aplikaci. Měl by být dostatečně vysoký, aby dosáhl přesné metriky, ale překročil sazbu, která překračuje vaši kvótu a omezení omezení. 

**Použít adaptivní vzorkování:**

Pokud se podmínky použití ostatních forem vzorkování nepoužijí, doporučujeme adaptivní vzorkování. Toto nastavení je ve výchozím nastavení povoleno v sadě SDK serveru ASP.NET/ASP.NET Core. Nebude snižovat provoz, dokud nedosáhnete určité minimální míry, proto nebudou ovlivněné weby s nízkým použitím.

## <a name="how-do-i-know-whether-sampling-is-in-operation"></a>Návody víte, zda je vzorkování v provozu?

Pokud chcete zjistit skutečnou vzorkovací frekvenci bez ohledu na to, kde byla použita, použijte [dotaz Analytics](../../azure-monitor/app/analytics.md) , jako je například:

```
union requests,dependencies,pageViews,browserTimings,exceptions,traces
| where timestamp > ago(1d)
| summarize RetainedPercentage = 100/avg(itemCount) by bin(timestamp, 1h), itemType
```

Je-li RetainedPercentage pro libovolný typ menší než 100, bude tato položka vyvzorkovaná.

**Application Insights není vzorová relace, metriky a čítače výkonu v metodách vzorkování, které jsou popsané výše. Tyto typy jsou vždycky vyloučené z vzorkování, protože snížení přesnosti může být pro tyto typy telemetrie vysoce nežádoucí.**

## <a name="how-does-sampling-work"></a>Jak funguje vzorkování?

Funkce vzorkování s pevnou sazbou sady SDK ve verzích ASP.NET od 2.0.0 a Java SDK verze 2.0.1 a vyšší. Adaptivní vzorkování je funkce sady SDK ve verzích ASP.NET z 2.0.0 a vyšší. Vzorkování ingestování je funkce služby Application Insights a může být v provozu, pokud sada SDK neprovádí vzorkování.

Algoritmus vzorkování určuje, které položky telemetrie se mají odpojit, a které se mají zachovat (ať už jsou v sadě SDK nebo ve službě Application Insights). Rozhodnutí o vzorkování vychází z několika pravidel, která se zaměřují na zachování všech vzájemně se nedotčených datových bodů, což zachovává diagnostické prostředí v Application Insights, které je možné a spolehlivé i s omezenou datovou sadou. Například pokud u neúspěšného požadavku vaše aplikace odesílá další položky telemetrie (například výjimku a trasování zaznamenané z této žádosti), vzorkování nebude tento požadavek a další telemetrie rozdělit. Udržuje je nebo je pohromadě pokaždé. Výsledkem je, že když se podíváte na podrobnosti o požadavku v Application Insights, můžete vždy zobrazit žádost spolu s jejími přidruženými položkami telemetrie. 

Rozhodnutí o vzorkování vychází z ID operace požadavku. to znamená, že všechny položky telemetrie patřící do konkrétní operace jsou buď zachované, nebo vyřazené. Pro položky telemetrie, které nemají nastavené ID operace (například položky telemetrie hlášené z asynchronních vláken bez kontextu http), se vzorkováním jednoduše zachytí procento položek telemetrie každého typu. V rámci 2.5.0-beta2 sady .NET SDK a 2.2.0-beta3 ASP.NET Core SDK bylo rozhodnutí o vzorkování založeno na hodnotě hash ID uživatele u aplikací, které definují "User" (tj. ve většině typických webových aplikací). Pro typy aplikací, které nedefinovaly uživatele (například webové služby), bylo rozhodnutí o vzorkování založeno na ID operace žádosti.

Když prezentujete telemetrii zpátky, služba Application Insights upravuje metriky podle stejného procenta vzorkování, které bylo použito v době shromažďování, k náhradě chybějících datových bodů. Proto při prohlížení telemetrie v Application Insights se uživatelům zobrazuje statisticky správné aproximace, které se velmi blíží skutečným číslům.

Přesnost aproximace je převážně závislá na nakonfigurované procentuální hodnotě vzorkování. Také se zvyšuje přesnost aplikací, které zpracovávají velký objem obecně podobných požadavků od mnoha uživatelů. Na druhé straně pro aplikace, které nefungují s významným zatížením, není vzorkování nutné, protože tyto aplikace mohou obvykle posílat veškerou telemetrii při zachování kvóty, aniž by došlo ke ztrátě dat z omezení. 

> [!WARNING]
> Application Insights nezahrnuje ukázkové metriky a typy telemetrie relací. Snížení přesnosti může být pro tyto typy telemetrie vysoce nežádoucí.
> 

### <a name="adaptive-sampling"></a>Adaptivní vzorkování

Adaptivní vzorkování přidá komponentu, která monitoruje aktuální rychlost přenosu ze sady SDK, a upraví procento vzorkování, aby se pokusilo zůstat v rámci maximálního počtu cílových hodnot. Úprava se přepočítá v pravidelných intervalech a je založena na klouzavý průměr odchozí přenosové rychlosti.

## <a name="sampling-and-the-javascript-sdk"></a>Vzorkování a sada JavaScript SDK

Sada SDK na straně klienta (JavaScript) se účastní vzorkování s pevnou sazbou ve spojení se sadou SDK na straně serveru. Instrumentované stránky odesílají telemetrii jenom na straně klienta od stejných uživatelů, u kterých na straně serveru provedla rozhodnutí "vzorkovat". Tato logika je navržená tak, aby udržovala integritu uživatelské relace napříč klienty a na straně serveru. V důsledku toho můžete z jakékoliv konkrétní položky telemetrie v Application Insights najít všechny další položky telemetrie pro tohoto uživatele nebo relaci. 

*Moje telemetrie na straně klienta a serveru nezobrazuje koordinované ukázky, jak je popsáno výše.*

* Ověřte, že jste povolili vzorkování s pevnou sazbou na serveru i v klientovi.
* Ujistěte se, že verze sady SDK je 2,0 nebo vyšší.
* Ověřte, že jste v klientovi i na serveru nastavili stejné procento vzorkování.

### <a name="sampling-in-azure-functions"></a>Vzorkování v Azure Functions

Podle pokynů v [tomto](https://docs.microsoft.com/azure/azure-functions/functions-monitoring#configure-sampling) tématu nakonfigurujte vzorkování pro aplikace běžící v Azure Functions.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

*Jaké je výchozí chování při vzorkování v ASP.NET a sadě SDK pro ASP.NET Core?*

* Pokud používáte některou z nejnovějších verzí výše uvedené sady SDK, adaptivní vzorkování je ve výchozím nastavení povolené s pěti položkami telemetrie za sekundu.
  Ve výchozím nastavení je přidáno 2 AdaptiveSamplingTelemetryProcessors a v vzorkování zahrnuje typ události a druhý vyloučí typ události z vzorkování. Tato konfigurace znamená, že se sada SDK pokusí omezit položky telemetrie na pět položek telemetrie typů událostí a pět položek telemetrie všech ostatních typů. tím zajistíte, že se události vzorkují odděleně od jiných typů telemetrie. Události se obvykle používají pro obchodní telemetrie a pravděpodobně by na ně neměly mít vliv žádné diagnostické svazky telemetrie.
  
  Následující příklad ukazuje výchozí vygenerovaný soubor ApplicationInsights. config. Jak je popsáno, jsou přidány dva samostatné uzly AdaptiveSamplingTelemetryProcessor, jeden s výjimkou typů událostí a další včetně. V ASP.NET Core je v kódu povolené přesné stejné výchozí chování. Chcete-li změnit toto výchozí chování, použijte příklady v předchozí části dokumentu.

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

* Ne. SamplingTelemetryProcessors ignorovat položky z hlediska vzorkování, pokud je položka již vzorkovat. Totéž platí také pro vzorkování ingestování, které nepoužijí vzorkování na ty položky, které jsou již v sadě SDK navzorkované. "

*Proč není vzorkování jednoduché "shromáždit X procento každého typu telemetrie"?*

* I když tento přístup k odběru vzorků poskytne vysokou úroveň přesnosti v rámci aproximace metriky, může přerušit schopnost korelovat diagnostická data podle uživatele, relace a žádosti, což je pro diagnostiku klíčové. Proto vzorkování funguje lépe s "shromažďovat všechny položky telemetrie pro X% uživatelů aplikací" nebo "shromažďovat veškerou telemetrii pro X procent požadavků aplikací". Pro položky telemetrie, které nejsou přidruženy k žádostem (jako je například asynchronní zpracování na pozadí), by záloha měla být shromážděna X procent všech položek pro každý typ telemetrie. 

*Může se v průběhu času změnit procentuální hodnota vzorkování?*

* Ano, adaptivní vzorkování postupně mění procento vzorkování na základě aktuálně zjištěného objemu telemetrie.

*Jak mám při použití vzorkování s pevnou sazbou vědět, jaké procento vzorkování bude fungovat nejlépe pro moji aplikaci?*

* Jedním ze způsobů, jak začít s adaptivním vzorkováním, zjistíte, ke které sazbě se připravuje (viz výše uvedený dotaz), a pak se pomocí této sazby přepne na vzorkování s pevnou sazbou. 
  
    V opačném případě je nutné odhadnout. Analyzujte své aktuální využití telemetrie v Application Insights, Sledujte jakékoliv omezení, ke kterému dochází, a odhad objemu shromážděné telemetrie. Tyto tři vstupy společně s vaší vybranou cenovou úrovní navrhují, kolik můžete chtít snížit objem shromážděné telemetrie. Zvýšení počtu uživatelů nebo nějakého jiného posunu v rámci telemetrie může ale způsobit zrušení platnosti vašeho odhadu.

*Co se stane, když nakonfigurujem procento vzorkování příliš nízké?*

* Příliš nízké procento vzorkování (nadměrné vzorkování) snižuje přesnost aproximací, když se Application Insights pokusí kompenzovat vizualizaci dat pro snížení objemu dat. Kromě toho může být diagnostické prostředí negativně ovlivněno, protože některé z častých neúspěšných nebo pomalých požadavků mohou být vyodebírány.

*Co se stane, když nakonfigurujem procento vzorkování příliš vysoké?*

* Konfigurace příliš vysokého procenta vzorkování (není dostatečně agresivní) vede k nedostatečnému snížení objemu shromážděné telemetrie. Může se stát, že dojde ke ztrátě dat telemetrie v souvislosti s omezováním a náklady na použití Application Insights můžou být vyšší, než jste naplánovali z důvodu poplatků za nadlimitní využití.

*Na kterých platformách je možné vzorkování použít?*

* Vzorkování příjmu se může vyskytnout automaticky pro všechny telemetrie nad určitým svazkem, pokud sada SDK neprovádí vzorkování. Tato konfigurace by mohla fungovat například v případě, že používáte starší verzi sady ASP.NET SDK nebo předchozí verzi sady Java SDK (1.0.10 nebo před).
* Pokud používáte sadu SDK ASP.NET verze 2.0.0 a vyšší nebo ASP.NET CORE SDK verze 2.2.0 a vyšší (hostované v Azure nebo na vašem vlastním serveru), získáte ve výchozím nastavení adaptivní vzorkování, ale můžete přepnout na pevný kurz, jak je popsáno výše. Při vzorkování s pevnou sazbou se sada SDK pro prohlížeč automaticky synchronizuje s ukázkovými událostmi, které se týkají. 
* Pokud používáte sadu Java SDK verze 2.0.1 nebo novější, můžete ApplicationInsights. XML nakonfigurovat, aby bylo možné zapnout vzorkování pevné míry. Vzorkování je ve výchozím nastavení vypnuté. Při vzorkování s pevnou sazbou se sada SDK pro prohlížeč automaticky synchronizuje s ukázkovými událostmi, které se týkají.

*Existují určité vzácné události, které vždycky chcete vidět. Jak se dají dostat za modul vzorkování?*

* Nejlepším způsobem, jak toho dosáhnout, je napsat vlastní [TelemetryInitializer](../../azure-monitor/app/api-filtering-sampling.md#add-properties-itelemetryinitializer), který nastaví `SamplingPercentage` na 100 u položky telemetrie, kterou chcete uchovat, jak je znázorněno níže. Vzhledem k tomu, že je zaručeno spouštění inicializátorů před procesory telemetrie (včetně vzorkování), zajistí to, že všechny techniky vzorkování budou tuto položku ignorovat z jakéhokoli hlediska vzorkování.

```csharp
     public class MyTelemetryInitializer : ITelemetryInitializer
      {
         public void Initialize(ITelemetry telemetry)
        {
            if(somecondition)
            {
                ((ISupportSampling)item).SamplingPercentage = 100;
            }
        }
      }
```

## <a name="next-steps"></a>Další kroky

* [Filtrování](../../azure-monitor/app/api-filtering-sampling.md) může poskytovat přísnější kontrolu nad tím, co vaše sada SDK posílá.
* Přečtěte si článek o vývojářské síti [optimalizace telemetrie pomocí Application Insights](https://msdn.microsoft.com/magazine/mt808502.aspx).
