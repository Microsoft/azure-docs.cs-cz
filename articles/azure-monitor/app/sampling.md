---
title: Vzorkování telemetrických dat ve službě Azure Application Insights | Dokumentace Microsoftu
description: Tom, jak zajistit objem telemetrických dat pod kontrolou.
services: application-insights
documentationcenter: windows
author: cijothomas
manager: carmonm
ms.assetid: 015ab744-d514-42c0-8553-8410eef00368
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/14/2019
ms.reviewer: vitalyg
ms.author: cithomas
ms.openlocfilehash: 7a657f175307e019155e37538021c5aecf5bb068
ms.sourcegitcommit: e5dcf12763af358f24e73b9f89ff4088ac63c6cb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/14/2019
ms.locfileid: "67136898"
---
# <a name="sampling-in-application-insights"></a>Vzorkování ve službě Application Insights

Vzorkování je funkce [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). Je doporučený postup ke snížení provozu telemetrie a úložiště, zachováním statisticky správné analýzy dat aplikací. Položky, které se týkají, vybere filtr tak, aby můžete procházet mezi položkami při provádění diagnostiky šetření.
Když metriky počty jsou uvedené na portálu, jsou renormalized zohlednit účet vzorkování. Tím se minimalizuje žádný vliv na Statistika.

Vzorkování snižuje náklady na provoz a data a pomůže vám vyhnout se omezení.

## <a name="in-brief"></a>Přehled:

* Vzorkování uchovává 1 v *n* záznamy a zbytek zahodí. Například to může zachovat jeden z pěti události, vzorkovací frekvenci 20 %. 
* Adaptivní vzorkování je povolené ve výchozím nastavení v nejnovější verzi technologie ASP.NET a ASP.NET Core Software Development Kit (SDK).
* Můžete také nastavit ručně vzorkování. To lze konfigurovat na portálu pro *využití a odhadované náklady na stránce*, v sady SDK technologie ASP.NET v souboru ApplicationInsights.config v ASP.NET Core SDK prostřednictvím kódu nebo sady Java SDK v ApplicationInsights.xml souboru.
* Při protokolování vlastních událostí a potřeba zajistit, že je zachována nebo zrušených společně sadu událostí, musí všechny události mají stejnou hodnotu ID operace.
* Dělitel vzorkování *n* se použije v hlášení všechny záznamy ve vlastnosti `itemCount`, která v hledání se zobrazí pod popisným názvem "počet požadavků" nebo "počet událostí". `itemCount==1`při vzorkování se nenachází v operaci.
* Pokud jste psaní analytických dotazů, měli byste [vzít v úvahu vzorkování](../../azure-monitor/log-query/aggregations.md). Konkrétně se místo jednoduše počítání záznamy, měli byste použít `summarize sum(itemCount)`.

## <a name="types-of-sampling"></a>Typy vzorkování

Existují tři metody alternativní vzorkování:

* **Adaptivní vzorkování** automaticky přizpůsobí objemu telemetrická data odesílaná ze sady SDK v aplikaci ASP.NET/ASP.NET Core. Toto je výchozí vzorkování z verze 2.0.0-beta3 v ASP.NET Web SDK a sady SDK Microsoft.ApplicationInsights.AspNetCore v 2.2.0-beta1 a vyšší.  Adaptivní vzorkování je momentálně dostupný jenom pro telemetrii na straně serveru technologie ASP.NET.

* **Míra vzorkování** snižuje objem telemetrická data odesílaná z vašeho technologie ASP.NET nebo ASP.NET Core nebo Java serveru a z prohlížečů uživatelů. Můžete nastavit rychlost. Klient a server bude synchronizovat jejich vzorkování tak, v hledání, můžete procházet zobrazení související stránky a požadavků.

* **Vzorkování příjmu** funguje na webu Azure Portal. Zruší část telemetrická data přenášená z vaší aplikace na vzorkovací frekvenci, kterou jste nastavili. Snížení provozu telemetrie odesílané z vaší aplikace se nepodporuje, ale pomáhá udržet v rámci měsíční kvóty. Hlavní výhodou vzorkování příjmu je, že je nastavení míry vzorkování bez opětovného nasazení aplikace. Vzorkování příjmu rovnoměrně funguje pro všechny servery a klienty.

Pokud Adaptivní nebo pevná frekvence vzorkování se v operaci, je vzorkování příjmu zakázané.


## <a name="adaptive-sampling-in-your-aspnetaspnet-core-web-applications"></a>Adaptivní vzorkování ve vašich webových aplikacích ASP.NET/ASP.NET Core

Adaptivní vzorkování je k dispozici pro Application Insights SDK pro ASP.NET v 2.0.0-beta3 nebo novější, 2.2.0-beta1 v Microsoft.ApplicationInsights.AspNetCore SDK nebo novější a je ve výchozím nastavení povolené.

Adaptivní vzorkování ovlivňuje množství telemetrie odesílané z vaší aplikace webového serveru do koncového bodu služby Application Insights. Svazek se automaticky upraví zachovat v rámci zadané maximální rychlost přenosu provozu a je řízen pomocí nastavení `MaxTelemetryItemsPerSecond`. Pokud aplikace vytvoří malou telemetrických dat, například při ladění nebo z důvodu s nízkým využitím položky nebudou vyřadit pomocí vzorkování procesoru, dokud svazek je nižší než `MaxTelemetryItemsPerSecond`. Jako objem telemetrických dat zvyšuje vzorkovací frekvenci se upraví tak, aby dosáhnout cílového svazku.

Pokud chcete dosáhnout cílového svazku, některé generované telemetrie se zahodí. Ale stejně jako jiné typy vzorkování algoritmus zachová položky souvisejícími telemetrickými daty. Například když se kontrola telemetrických dat ve službě Search, budete mít k vyhledání žádosti související s konkrétní výjimce položku.

Metrika se počítá jako je frekvence požadavků a frekvence výjimek jsou upraveny pro kompenzování případných vzorkovací frekvenci tak, aby se zobrazovala přibližně správné hodnoty v Průzkumníku metrik.

## <a name="configuring-adaptive-sampling-for-aspnet-applications"></a>Konfigurace adaptivního vzorkování pro aplikace ASP.NET

[Přečtěte si](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications) týkající se konfigurace adaptivního vzorkování pro aplikace ASP.NET Core. 

V [soubor ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md), můžete upravit několik parametrů v `AdaptiveSamplingTelemetryProcessor` uzlu. Na obrázcích, zobrazí se výchozí hodnoty:

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`
  
    Rychlost cíl, který adaptivní algoritmus pro **na každém hostiteli serveru**. Pokud vaše webová aplikace spuštěna v mnoha hostitelích, zmenšete tuto hodnotu tak, aby zůstaly ve vaší cílové míru přenosu dat na portálu služby Application Insights.
* `<EvaluationInterval>00:00:15</EvaluationInterval>` 
  
    Interval, ve kterém je již aktuální rychlost telemetrická data znovu. Vyhodnocení se provádí jako klouzavý průměr. Můžete chtít zkrátit tento interval, při které by mohly vést k náhlým nárůstům vaší telemetrie.
* `<SamplingPercentageDecreaseTimeout>00:02:00</SamplingPercentageDecreaseTimeout>`
  
    Při vzorkování procentuální hodnota změny, jak brzy poté, co jsme mohou snížit procento vzorkování znovu k zaznamenání dat o méně.
* `<SamplingPercentageIncreaseTimeout>00:15:00</SamplingPercentageIncreaseTimeout>`
  
    Při vzorkování procentuální hodnota změny, jak brzy poté jsme mohou zvýšit procento vzorkování znovu k zaznamenání dat o další.
* `<MinSamplingPercentage>0.1</MinSamplingPercentage>`
  
    Jako procento vzorkování se liší, co je minimální hodnota, kterou jsme už moct nastavit.
* `<MaxSamplingPercentage>100.0</MaxSamplingPercentage>`
  
    Jako procento vzorkování se liší, co je maximální hodnota, kterou jsme už moct nastavit.
* `<MovingAverageRatio>0.25</MovingAverageRatio>` 
  
    Při výpočtu klouzavý průměr váhy přiřazené nejnovější hodnotu. Použijte hodnotu větší nebo menší než 1. Menší hodnoty provádět algoritmus méně reaktivní k náhlé změny.
* `<InitialSamplingPercentage>100</InitialSamplingPercentage>`
  
    Hodnota přiřazená jenom po spuštění aplikace. Hodnota není snížit, během ladění.

* `<ExcludedTypes>Trace;Exception</ExcludedTypes>`
  
    Středníkem oddělený seznam typů, které nechcete odeberou. Rozpoznané typy jsou: Závislost, události, výjimky, zobrazení stránky, požadavek, trasování. Všechny instance určené typy jsou přenášeny; typy, které nejsou zadané, jsou vzorkovány.

* `<IncludedTypes>Request;Dependency</IncludedTypes>`
  
    Středníkem oddělený seznam typů, které mají být prováděno vzorkování. Rozpoznané typy jsou: Závislost, události, výjimky, zobrazení stránky, požadavek, trasování. Zadané typy jsou vzorkovány; všechny instance ostatních typů budou vždy odeslány.


**Vypnout** adaptivní vzorkování, odeberte AdaptiveSamplingTelemetryProcessor uzly z konfigurace applicationinsights.

### <a name="alternative-configure-adaptive-sampling-in-code"></a>Ve zkratce: Konfigurace adaptivním vzorkování v kódu

Namísto nastavení vzorkování parametru v souboru .config, můžete prostřednictvím kódu programu nastavit tyto hodnoty.

1. Odebrat vše `AdaptiveSamplingTelemetryProcessor` uzlů ze souboru .config.
2. Pomocí následujícího fragmentu kódu můžete nakonfigurovat adaptivního vzorkování.

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

([Další informace o telemetrii procesory](../../azure-monitor/app/api-filtering-sampling.md#filtering).)

Můžete také upravit jednotlivě vzorkovací frekvenci pro každý typ telemetrických dat nebo dokonce z můžete vyloučit určité typy vzorkují vůbec. 

*C#*

```csharp
    // The following configures adaptive sampling with 5 items per second, and also excludes DependencyTelemetry from being subject to sampling.
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");
```

## <a name="configuring-adaptive-sampling-for-aspnet-core-applications"></a>Konfigurace adaptivního vzorkování pro aplikace ASP.NET Core.

Neexistuje žádná `ApplicationInsights.Config` pro aplikace ASP.NET Core, takže všechny konfigurace se provádí prostřednictvím kódu.
Adaptivní vzorkování je povoleno standardně pro všechny aplikace ASP.NET Core. Můžete zakázat nebo přizpůsobit chování vzorkování.

### <a name="turning-off-adaptive-sampling"></a>Vypnutí adaptivní vzorkování

Při přidávání služby Application Insights v metodě lze zakázat funkci výchozí vzorkování ```ConfigureServices```s použitím ```ApplicationInsightsServiceOptions``` v rámci `Startup.cs` souboru:

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

Výše uvedený kód zakážete funkci vzorkování. Podle následujících pokynů pro přidání vzorkování s další možnosti vlastního nastavení.

### <a name="configure-sampling-settings"></a>Konfigurace nastavení vzorkování

Použití rozšíření metod pro posunutí ```TelemetryProcessorChainBuilder``` jak je znázorněno níže můžete přizpůsobit chování vzorkování.

> [!IMPORTANT]
> Pokud použijete tuto metodu můžete nakonfigurovat vzorkování, ujistěte se prosím, že používáte aiOptions.EnableAdaptiveSampling = false;. nastavení s AddApplicationInsightsTelemetry().

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

**Pokud pomocí výše uvedené metody ke konfiguraci odběru vzorků, je nutné použít ```aiOptions.EnableAdaptiveSampling = false;``` nastavení s AddApplicationInsightsTelemetry().**

## <a name="fixed-rate-sampling-for-aspnet-aspnet-core-and-java-websites"></a>Míra vzorkování webových stránek ASP.NET, ASP.NET Core a Javy

Pevná sazba vzorkování omezuje provoz odeslaný z webového serveru a webových prohlížečů. Na rozdíl od adaptivního vzorkování snižuje telemetrie s pevnou sazbou rozhodla sami. Také synchronizuje klient a server vzorkování tak, aby se zachovají související položky – například při pohledu na zobrazení stránky v hledání můžete najít jeho související požadavek.

Stejně jako ostatní odběry vzorků to také zachovává související položky. Pro každý požadavek HTTP události, žádost a její související události jsou zahozeny nebo přenášet společně.

V Průzkumníku metrik kurzy, jako jsou počty požadavků a výjimek násobí faktor jako kompenzaci za vzorkovací frekvenci tak, aby byly přibližně správné.

### <a name="configuring-fixed-rate-sampling-in-aspnet"></a>Konfigurace – míra vzorkování v ASP.NET

1. **Zakázat adaptivního vzorkování**: V [soubor ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md), odstranit nebo okomentovat `AdaptiveSamplingTelemetryProcessor` uzlu.

    ```xml

    <TelemetryProcessors>

    <!-- Disabled adaptive sampling:
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    -->
    ```

2. **Povolte modul-frekvence vzorkování.** Přidejte tento fragment kódu pro [soubor ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md):
   
    ```XML
   
    <TelemetryProcessors>
     <Add  Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
   
      <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
      <SamplingPercentage>10</SamplingPercentage>
      </Add>
    </TelemetryProcessors>
   
    ```
   ### <a name="alternative-enable-fixed-rate-sampling-in-your-server-code"></a>Ve zkratce: Povolte – míra vzorkování v serverovém kódu
    
    Namísto nastavení vzorkování parametru v souboru .config, můžete prostřednictvím kódu programu nastavit tyto hodnoty. 

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
([Další informace o telemetrii procesory](../../azure-monitor/app/api-filtering-sampling.md#filtering).)

### <a name="configuring-fixed-rate-sampling-in-aspnet-core"></a>Konfigurace – míra vzorkování v ASP.NET Core

1. **Zakázat adaptivního vzorkování**:  Změny v metodě ```ConfigureServices```s použitím ```ApplicationInsightsServiceOptions```:

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

2. **Povolte modul-frekvence vzorkování.** Změny v metodě ```Configure``` jak je znázorněno v následující fragment kódu:

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

### <a name="configuring-fixed-rate-sampling-in-java"></a>Konfigurace – míra vzorkování v jazyce JAVA ###

1. Stáhnout a nakonfigurovat webovou aplikaci s nejnovější [application insights java SDK](../../azure-monitor/app/java-get-started.md)

2. **Povolit modul-frekvence vzorkování** tak, že přidáte následující fragment kódu do souboru ApplicationInsights.xml.

    ```XML
        <TelemetryProcessors>
            <BuiltInProcessors>
                <Processor type = "FixedRateSamplingTelemetryProcessor">
                    <!-- Set a percentage close to 100/N where N is an integer. -->
                    <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
                    <Add name = "SamplingPercentage" value = "50" />
                </Processor>
            </BuiltInProcessors>
        <TelemetryProcessors/>
    ```

3. Můžete zahrnout nebo vyloučit určité typy telemetrie z vzorkování, pomocí následujících značek uvnitř značky procesoru "FixedRateSamplingTelemetryProcessor"
    ```XML
        <ExcludedTypes>
            <ExcludedType>Request</ExcludedType>
        </ExcludedTypes>

        <IncludedTypes>
            <IncludedType>Exception</IncludedType>
        </IncludedTypes>
    ```

Typy telemetrie, které mohou být zahrnuty nebo vyloučeny ze vzorkování jsou: Závislost, události, výjimky, zobrazení stránky, požadavek a trasování.

> [!NOTE]
> Procento vzorkování zvolte procento blíží 100/N, kde N je celé číslo.  Aktuálně vzorkování není podporováno jiné hodnoty.
> 
> 

<a name="other-web-pages"></a>



## <a name="ingestion-sampling"></a>Vzorkování příjmu

Tato forma odběru vzorků funguje v místě, kde telemetrická data z vašeho webového serveru, prohlížeče a zařízení dosáhne koncového bodu služby Application Insights. I když to není snížení provozu telemetrie odesílané z vaší aplikace, snížit objem zpracovaných a zachovává (a účtují se poplatky za) pomocí Application Insights.

Tento typ vzorkování použijte, pokud vaše aplikace často překročí měsíční kvóty a nemáte možnost používat jeden z typů založené na sadě SDK odběru vzorků. 

Nastavení míry vzorkování využití a odhadované náklady na stránce:

![V okně přehledu aplikace klikněte na nastavení, kvóty, ukázky, vyberte vzorkovací frekvenci a kliknutím na tlačítko Aktualizovat.](./media/sampling/04.png)

Podobně jako ostatní typy vzorkování uchovává algoritmus položky souvisejícími telemetrickými daty. Například když se kontrola telemetrických dat ve službě Search, budete mít k vyhledání žádosti související s konkrétní výjimce položku. Metrika se počítá jako je frekvence požadavků a frekvence výjimek správně se zachovají.

Datové body, které jsou zahozena vzorkování nejsou k dispozici v jakékoli funkce Application Insights, jako [průběžný Export](../../azure-monitor/app/export-telemetry.md).

Vzorkování příjmu nepracuje během vzorkování Adaptivní nebo pevnou sazbou na základě sady SDK. Adaptivní vzorkování je standardně povolená, když ASP.NET/ASP.NET Core SDK je povolena v sadě Visual Studio nebo v rozšíření webové aplikace Azure nebo pomocí monitorování stavu a vzorkování příjmu je zakázaná. Pokud míry vzorkování v sadě SDK je menší než 100 % (např.) položky se vzorkují) a míry vzorkování příjmu, které jste nastavili se ignoruje.

> [!WARNING]
> Hodnota použitá na dlaždici označuje hodnotu, kterou jste nastavili pro vzorkování příjmu. Pokud sada SDK vzorkování je v provozu, nepředstavuje skutečný vzorkovací frekvenci.
>
>
## <a name="sampling-for-web-pages-with-javascript"></a>Vzorkování pro webové stránky s použitím jazyka JavaScript
Můžete nakonfigurovat webových stránek – frekvence vzorkování z jakéhokoli serveru. 

Když jste [konfigurace webové stránky pro službu Application Insights](../../azure-monitor/app/javascript.md), upravte fragment kódu jazyka JavaScript, který můžete získat z portálu služby Application Insights. (V aplikacích technologie ASP.NET, fragment obvykle přejde v _Layout.cshtml.)  Vložit řádek podobný `samplingPercentage: 10,` před Instrumentační klíč:

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

Procento vzorkování zvolte procento blíží 100/N, kde N je celé číslo.  Aktuálně vzorkování není podporováno jiné hodnoty.

Pokud povolíte také vzorkování pevnou sazbou na serveru, klienty a server bude synchronizovat tak, aby, v hledání, můžete procházet zobrazení související stránky a požadavků.

## <a name="when-to-use-sampling"></a>Kdy použít vzorkování?

Adaptivní vzorkování je automaticky povolen v nejnovější rozhraní .NET a .NET Core SDK. Bez ohledu na to, kterou verzi sady SDK můžete použít můžete povolit vzorkování příjmu povolit Application Insights pro vzorkování se shromážděná data.

Ve výchozím nastavení je povoleno žádné vzorkování v sadě Java SDK. V současné době podporuje pouze dlouhodobý frekvence vzorkování. Adaptivní vzorkování není podporováno v sadě Java SDK.

Obecně platí pro většinu aplikací malé a střední nepotřebujete vzorkování. Nejužitečnější diagnostické informace a co nejvíce zpřesnili statistiky jsou získány pomocí shromažďování dat na všech aktivit uživatelů. 

Hlavní výhody vzorkování jsou:

* Application Insights service drops ("omezení") datových bodů když vaše aplikace odesílá velmi vysoká míra telemetrie v krátkých časový interval. 
* Aby se v rámci [kvóty](../../azure-monitor/app/pricing.md) datových bodů pro svou cenovou úroveň. 
* Omezit přenos v síti z kolekce telemetrie. 

### <a name="which-type-of-sampling-should-i-use"></a>Jaký typ vzorkování mám použít?

**Pomocí vzorkování příjmu, pokud:**

* Často projít kvóta měsíčního počtu telemetrická data.
* Používáte verzi sady SDK, která nepodporuje vzorkování – například technologie ASP.NET verze starší než 2.
* Získáváte příliš mnoho telemetrických dat z webových prohlížečů uživatelů.

**Použijte pevnou sazbou vzorkování, pokud:**

* Používáte sadu SDK Application Insights pro ASP.NET web services verze 2.0.0 nebo novější nebo v2.0.1 sady Java SDK nebo novější, a
* Chcete, aby synchronizovaná vzorkování mezi klientem a serverem, tak, že když zkoumáte události v [hledání](../../azure-monitor/app/diagnostic-search.md), můžete procházet mezi souvisejícími událostmi na klientovi a serveru, jako je například zobrazení stránky a požadavky http.
* Jste si jisti procenta vzorkování vhodné pro vaši aplikaci. Měla by být dostatečně vysoká, aby získání přesných metrik, ale pod sazbu, která překračuje cenovou kvóty a omezení. 

**Použijte adaptivní vzorkování:**

Pokud podmínky použití jiné formy vzorkování se nedá použít, doporučujeme, abyste adaptivního vzorkování. Toto nastavení je povolené ve výchozím nastavení v serveru ASP.NET/ASP.NET Core SDK. Nesníží provozu, dokud nebude dosaženo určitou minimální míru, proto lokalit s nízkou použijte tyto zásady neovlivní.

## <a name="how-do-i-know-whether-sampling-is-in-operation"></a>Jak zjistím, zda vzorkování je v provozu?

Chcete-li zjistit skutečné vzorkovací frekvenci bez ohledu na to, kde byl použit, použijte [dotazu Analytics](../../azure-monitor/app/analytics.md) takovou situaci:

```
union requests,dependencies,pageViews,browserTimings,exceptions,traces
| where timestamp > ago(1d)
| summarize RetainedPercentage = 100/avg(itemCount) by bin(timestamp, 1h), itemType
```

Pokud tuto položku se vzorkují RetainedPercentage pro jakýkoli typ je menší než 100.

**Application Insights není ukázková relace, metriky a čítače výkonu typy telemetrie v jakékoli vzorkování technik popsaných výše. Tyto typy jsou vždycky vyloučení z vzorkování jako snížení přesnost může být vysoce nežádoucí pro tyto typy telemetrie**

## <a name="how-does-sampling-work"></a>Jak funguje vzorkování?

Míra vzorkování funkce sady SDK v technologii ASP.NET verze 2.0.0 a sady Java SDK verze 2.0.1 a a vyšší. Adaptivní vzorkování je funkce sady SDK technologie ASP.NET verze 2.0.0 a vyšší. Vzorkování příjmu je funkce služby Application Insights a může být v operaci, pokud sada SDK není provádění vzorkování.

Algoritmus vzorkování rozhodne telemetrie položky, které chcete vyřadit a ty, které chcete zachovat (ať už je v sadě SDK nebo ve službě Application Insights). Rozhodnutí vzorkování je založeno na několik pravidel, která se mají zachovat všechny vzájemně propojené datové body beze změn, udržování diagnostického prostředí ve službě Application Insights, který je užitečné a spolehlivé i s menší datové sady. Například pokud vaše aplikace odesílá pro chybné žádosti položky další telemetrické údaje (například výjimky a trasování, přihlášení z této žádosti), vzorkování nebude rozdělit tuto žádost a další telemetrie. To zajišťuje nebo jejich zahodí všechno dohromady. V důsledku toho při pohledu na podrobnosti o žádosti ve službě Application Insights se vždy zobrazí požadavek spolu s položkami jeho přidružená telemetrická data. 

Rozhodnutí vzorkování je založeno na ID operace požadavku, což znamená, že všech položkách telemetrie, které patří do určité operace je zachována nebo vyřadit. Pro položky telemetrie, které nemají operace ID sady (pro Příklady položek telemetrie hlášená z asynchronní vlákna s žádný kontext http) vzorkování jednoduše zaznamená procento položky telemetrie každého typu. Před 2.5.0-beta2 sady .NET SDK a 2.2.0-beta3 ASP.NET Core SDK, vzorkování rozhodnutí bylo založeno na hodnotu hash ID uživatele pro aplikace, které definují "user" (to znamená, obvykle webové aplikace). Typy aplikací, které nebyly definovat uživatele (například webové služby) byl rozhodnutí vzorkování podle ID operace požadavku.

Při zobrazení telemetrie vrátit k tomu, služba Application Insights upraví metriky o stejné procento vzorkování, který byl použit v době kolekce, jako kompenzaci za chybějící datové body. Proto při prohlížení telemetrie v Application Insights, uživatelé se zobrazuje statisticky správné rovin útoku, které jsou velmi podobné reálná čísla.

Přesnost sblížení do značné míry závisí na procento nakonfigurované vzorkování. Navíc zvýší přesnost pro aplikace, které zpracovávají velké objemy obecně podobné žádosti z mnoha uživatelů. Na druhé straně aplikací, které nefungují s významné zatížení, vzorkování není v případě těchto aplikací můžete obvykle posílat všechny jejich telemetrie v mezích kvótu, aniž by došlo ke ztrátě dat z omezení. 

> [!WARNING]
> Application Insights není ukázkový typy telemetrie metrik a relací. Snížení přesnost může být vysoce nežádoucí pro tyto typy telemetrie.
> 

### <a name="adaptive-sampling"></a>Adaptivní vzorkování

Adaptivní vzorkování přidá komponentu, která sleduje aktuální rychlost přenosu ze sady SDK a upraví procento vzorkování se pokouší zůstat v rámci cílového maximální rychlost přenosu. Úprava přepočítána v pravidelných intervalech a je založena na klouzavý průměr sazby za odchozí přenos.

## <a name="sampling-and-the-javascript-sdk"></a>Vzorkování a JavaScript SDK

Straně klienta (JavaScript) SDK se účastní-frekvence vzorkování ve spojení s SDK na straně serveru. Instrumentovaná stránky pouze odesílání telemetrie na straně klienta z stejné uživatele, pro které provedené na straně serveru své rozhodnutí "Ukázkový." Tuto logiku je navržená k udržení integrity relace uživatele napříč tisk klienta a serveru strana. V důsledku toho z libovolné položky konkrétní telemetrie v Application Insights můžete najít všechny ostatní položky telemetrie pro tohoto uživatele nebo relace. 

*Moje klienta a telemetrii na straně serveru nezobrazovat koordinované ukázek, jak se uvádí výše.*

* Ověřte, že jste povolili vzorkování pevnou sazbou na serveru a klienta.
* Ujistěte se, že je sada SDK verze 2.0 nebo novější.
* Zkontrolujte nastavení vzorkování procentní v klientem a serverem.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

*Co je výchozí chování vzorkování v technologii ASP.NET a ASP.NET Core SDK?*

* Pokud použijete jeden z nejnovější verze sady SDK výše, adaptivního vzorkování je povolena ve výchozím nastavení se pět položek telemetrických dat za sekundu.
  Existují 2 AdaptiveSamplingTelemetryProcessors ve výchozím nastavení a obsahuje jeden typ události v vzorkování a druhý typ události vyloučí z vzorkování. Tato konfigurace znamená, že sada SDK se pokusí omezit položky telemetrie na pět položky telemetrie typů událostí a pět položek telemetrie všech ostatních typů v kombinaci, čímž se zajistí, že události jsou odebírána data odděleně od ostatních typů Telemetrie. Události se obvykle používají pro obchodní telemetrická data a pravděpodobně by neměla být ovlivněna telemetrická data diagnostiky svazky.
  
  Následuje ukázka vygeneruje soubor ApplicationInsights.Config výchozí. Jak je popsáno, existují dva samostatné uzly AdaptiveSamplingTelemetryProcessor přidali, s výjimkou typů událostí a další včetně ho. V ASP.NET Core přesně stejnou výchozí chování je povolena v kódu. Chcete-li změnit toto výchozí chování pomocí příklady v dřívější části dokumentu.

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

*Můžete telemetrie odeberou více než jednou?*

* Ne. SamplingTelemetryProcessors ignorovat položky z vzorkování aspekty, pokud položka je již vzorkována. Totéž platí pro vzorkování příjmu jako, který se nevztahuje vzorkování na tyto položky již vzorkovány v samotné sadě SDK. "

*Proč není vzorkování jednoduché "shromažďování X procento každý typ telemetrických dat"?*

* Sice by tento přístup vzorkování s vysokou úrovní přesnosti v metriky rovin útoku, by narušil možnosti pro korelaci dat diagnostiky za uživatele, relace a požadavku, což je velmi důležité pro diagnostiku. Proto se lépe funguje vzorkování s "shromažďování všech položkách telemetrie pro X procento uživatelů aplikace" nebo "shromažďovat veškerá telemetrie pro X procento žádostí aplikace" logiku. Pro položky telemetrie nejsou přidružené k požadavků (jako je asynchronní zpracování na pozadí), je použití náhrady "shromažďování X procento všechny položky pro každý typ telemetrie." 

*Procento vzorkování změnit v čase?*

* Ano, adaptivního vzorkování postupně změní procento vzorkování, na základě aktuálně známé objemu telemetrie.

*Když budu používat pevné frekvence vzorkování, jak poznám, že které vzorkování procento je nejvhodnější pro moji aplikaci?*

* Jedním ze způsobů je začít s adaptivní vzorkování, podívejte se, co hodnotit vyrovná na (viz předchozí otázce) a pak přepnout na pevnou sazbou vzorkování pomocí tohoto kurzu. 
  
    V opačném případě budete muset uhodnout. Analýza vaší aktuální využití telemetrie v Application Insights, sledovat žádné omezení využití sítě, ke kterému dochází a odhadnout objem shromážděných telemetrická data. Tyto tři vstupy, společně s vybrané cenové úrovně, navrhnout, kolik budete chtít snížit objem shromážděných telemetrická data. Zvýšení počtu uživatelů nebo jiné shift objemu telemetrických dat však může zneplatnit odhadu.

*Co se stane, když nakonfigurovat procento vzorkování příliš nízko?*

* Procento vzorkování příliš nízká (příliš agresivní vzorkování) snižuje přesnost rovin útoku, když se pokusí kompenzace vizualizaci dat pro snížení objemu dat Application Insights. Navíc diagnostického prostředí může být negativně ovlivněn, jako některé z požadavků zřídka selhání nebo pomalé vzorkovat lze navýšení kapacity.

*Co se stane, když nakonfigurovat příliš vysoké procento vzorkování?*

* Konfigurace procento vzorkování s příliš vysokou (ne agresivní dostatečně) způsobí nedostatek snížení objemu shromážděných telemetrická data. Může stále dojít související s omezení ztráty dat telemetrie a nákladů na použití služby Application Insights může být vyšší než na kolik máte plánované kvůli poplatky za Nadlimitní využití.

*Na jaké platformy je možné použít vzorkování?*

* Vzorkování příjmu může dojít automaticky pro žádnou telemetrii nad určitý objem, pokud sada SDK není vzorkování. Tato konfigurace bude fungovat, například pokud používáte starší verzi sady SDK technologie ASP.NET nebo předchozí verze Java SDK(1.0.10 or before).
* Pokud používáte sady SDK technologie ASP.NET verze 2.0.0 a vyšší nebo ASP.NET CORE SDK verze 2.2.0 a vyšší (hostované v Azure nebo na vlastním serveru), získáte adaptivní vzorkování ve výchozím nastavení, ale můžete přepnout na pevnou sazbou, jak je popsáno výše. S pevnou sazbou vzorkování prohlížeče sady SDK automaticky synchronizuje pro vzorkování souvisejících událostí. 
* Při použití sady Java SDK verze 2.0.1 nebo vyšší, můžete nakonfigurovat soubor ApplicationInsights.xml zapnout pevné frekvence vzorkování. Vzorkování je vypnuto ve výchozím nastavení. S pevnou sazbou vzorkování prohlížeče sady SDK automaticky synchronizuje pro vzorkování souvisejících událostí.

*Existují určité výjimečných událostí, které chci vždy zobrazit. Jak získám je poslední vzorkování modulu?*

* Nejlepší způsob, jak toho dosáhnout, je napsat vlastní [TelemetryProcessor](../../azure-monitor/app/api-filtering-sampling.md#filtering), který nastaví `SamplingPercentage` na 100 na položky telemetrie má zachované, jak je znázorněno níže. Tím se zajistí, že všechny odběry vzorků bude ignorovat tuto položku ze všechny důležité informace o vzorkování.

```csharp
    if(somecondition)
    {
        ((ISupportSampling)item).SamplingPercentage = 100;
    }
```

## <a name="next-steps"></a>Další postup

* [Filtrování](../../azure-monitor/app/api-filtering-sampling.md) může poskytovat další striktní kontrolu nad co vaše sada SDK odesílá.
* Přečtěte si článek Developer Network [optimalizovat Telemetrie pomocí Application Insights](https://msdn.microsoft.com/magazine/mt808502.aspx).
