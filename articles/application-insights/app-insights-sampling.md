---
title: Vzorkování telemetrických dat ve službě Azure Application Insights | Dokumentace Microsoftu
description: Tom, jak zajistit objem telemetrických dat pod kontrolou.
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: 015ab744-d514-42c0-8553-8410eef00368
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/02/2018
ms.reviewer: vitalyg
ms.author: mbullwin
ms.openlocfilehash: d9dfedd89e362f12f97d0106f3e56d465a89bb76
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2018
ms.locfileid: "52723839"
---
# <a name="sampling-in-application-insights"></a>Vzorkování ve službě Application Insights


Vzorkování je funkce [Azure Application Insights](app-insights-overview.md). Je doporučený postup ke snížení provozu telemetrie a úložiště, zachováním statisticky správné analýzy dat aplikací. Položky, které se týkají, vybere filtr tak, aby můžete procházet mezi položkami při provádění diagnostiky šetření.
Když metriky počty se zobrazují na portálu, jsou renormalized vzít v úvahu vzorkování, chcete-li minimalizovat žádný vliv na Statistika.

Vzorkování snižuje náklady na provoz a data a pomůže vám vyhnout se omezení.

## <a name="in-brief"></a>Přehled:
* Vzorkování uchovává 1 v *n* záznamy a zbytek zahodí. Například to může být uchování událostí 1 do 5, vzorkovací frekvenci 20 %. 
* Vzorkování se stane automaticky, když vaše aplikace odešle velké množství telemetrických dat, v serveru pro webové aplikace ASP.NET.
* Můžete také nastavit ručně vzorkování, buď v portálu využití a odhadované náklady na stránku. nebo v sadě SDK technologie ASP.NET v souboru .config. nebo v jazyce Java SDK v souboru ApplicationInsights.xml také omezit přenos v síti.
* Pokud záznam vlastních událostí a chcete, abyste měli jistotu, že sadu událostí je buď uchovávají nebo zrušených společně, ujistěte se, že mají stejnou hodnotu ID operace.
* Dělitel vzorkování *n* se použije v hlášení všechny záznamy ve vlastnosti `itemCount`, která v hledání se zobrazí pod popisným názvem "počet požadavků" nebo "počet událostí". Při vzorkování se nenachází v operaci, `itemCount==1`.
* Pokud jste psaní analytických dotazů, měli byste [vzít v úvahu vzorkování](../log-analytics/query-language/aggregations.md). Konkrétně se místo jednoduše počítání záznamy, měli byste použít `summarize sum(itemCount)`.

## <a name="types-of-sampling"></a>Typy vzorkování
Existují tři metody alternativní vzorkování:

* **Adaptivní vzorkování** automaticky přizpůsobí objemu telemetrická data odesílaná ze sady SDK v aplikaci ASP.NET. Od verze sady SDK v 2.0.0-beta3 Toto je výchozí metoda vzorkování. Adaptivní vzorkování je momentálně dostupný jenom pro telemetrii na straně serveru technologie ASP.NET. Pro aplikace Asp.NET Core, které cílí na úplné rozhraní Framework je k dispozici od verze 1.0.0 Microsoft.ApplicationInsights.AspNetCore SDK adaptivního vzorkování. Pro aplikace Asp.NET Core cílení NetCore adaptivního vzorkování je k dispozici 2.2.0-beta1 Microsoft.ApplicationInsights.AspNetCore SDK.

* **Míra vzorkování** snižuje objem telemetrická data odesílaná z vašeho technologie ASP.NET nebo Java serveru a z prohlížečů uživatelů. Můžete nastavit rychlost. Klient a server bude synchronizovat jejich vzorkování tak, v hledání, můžete procházet zobrazení související stránky a požadavků.
* **Vzorkování příjmu** funguje na webu Azure Portal. Zruší část telemetrická data přenášená z vaší aplikace na vzorkovací frekvenci, kterou jste nastavili. Snížení provozu telemetrie odesílané z vaší aplikace se nepodporuje, ale pomáhá udržet v rámci měsíční kvóty. Hlavní výhodou vzorkování příjmu je, že nastavíte vzorkovací frekvenci bez opětovného nasazení aplikace a rovnoměrně funguje pro všechny servery a klienty. 

Pokud Adaptivní nebo pevná frekvence vzorkování se v operaci, je vzorkování příjmu zakázané.

## <a name="ingestion-sampling"></a>Vzorkování příjmu
Tato forma odběru vzorků funguje v místě, kde telemetrická data z vašeho webového serveru, prohlížeče a zařízení dosáhne koncového bodu služby Application Insights. I když to není snížení provozu telemetrie odesílané z vaší aplikace, snížit objem zpracovaných a zachovává (a účtují se poplatky za) pomocí Application Insights.

Tento typ vzorkování použijte, pokud vaše aplikace často překročí měsíční kvóty a nemáte možnost používat jeden z typů založené na sadě SDK odběru vzorků. 

Nastavení míry vzorkování využití a odhadované náklady na stránce:

![V okně přehledu aplikace klikněte na nastavení, kvóty, ukázky, vyberte vzorkovací frekvenci a kliknutím na tlačítko Aktualizovat.](./media/app-insights-sampling/04.png)

Podobně jako ostatní typy vzorkování uchovává algoritmus položky souvisejícími telemetrickými daty. Například když se kontrola telemetrických dat ve službě Search, budete mít k vyhledání žádosti související s konkrétní výjimce položku. Metrika se počítá jako je frekvence požadavků a frekvence výjimek správně se zachovají.

Datové body, které jsou zahozena vzorkování nejsou k dispozici v jakékoli funkce Application Insights, jako [průběžný Export](app-insights-export-telemetry.md).

Vzorkování příjmu nepracuje během vzorkování Adaptivní nebo pevnou sazbou na základě sady SDK. Všimněte si, že adaptivního vzorkování je povolená ve výchozím nastavení, pokud je povoleno sady SDK technologie ASP.NET v sadě Visual Studio nebo pomocí monitorování stavu a vzorkování příjmu je zakázaná. Pokud míry vzorkování v sadě SDK je menší než 100 %, je ignorován míry vzorkování příjmu, které jste nastavili.

> [!WARNING]
> Hodnota použitá na dlaždici označuje hodnotu, kterou jste nastavili pro vzorkování příjmu. Pokud sada SDK vzorkování je v provozu, nepředstavuje skutečný vzorkovací frekvenci.
> 
> 

## <a name="adaptive-sampling-at-your-web-server"></a>Adaptivní vzorkování na webovém serveru
Adaptivní vzorkování je k dispozici pro Application Insights SDK pro ASP.NET v 2.0.0-beta3 nebo novější a je ve výchozím nastavení povolené. 

Adaptivní vzorkování ovlivňuje množství telemetrie odesílané z vaší aplikace webového serveru do koncového bodu služby Application Insights. Svazek se automaticky upraví zachovat v rámci zadané maximální rychlost přenosu.

Ho nepracuje na nízkou objemy telemetrických dat, takže aplikace v ladění nebo nebude mít vliv na web s s nízkým využitím.

Pokud chcete dosáhnout cílového svazku, některé generované telemetrie se zahodí. Ale stejně jako jiné typy vzorkování algoritmus zachová položky souvisejícími telemetrickými daty. Například když se kontrola telemetrických dat ve službě Search, budete mít k vyhledání žádosti související s konkrétní výjimce položku. 

Metrika se počítá jako je frekvence požadavků a frekvence výjimek jsou upraveny pro kompenzování případných vzorkovací frekvenci tak, aby se zobrazovala přibližně správné hodnoty v Průzkumníku metrik.

### <a name="update-nuget-packages"></a>Aktualizace balíčků NuGet ###

Aktualizovat balíčky NuGet projektu na nejnovější verzi *předběžné verze* verze služby Application Insights. V sadě Visual Studio, klikněte pravým tlačítkem na projekt v Průzkumníku řešení, vyberte spravovat balíčky NuGet, zkontrolujte **zahrnout předběžné verze** a vyhledejte Microsoft.ApplicationInsights.Web. 

### <a name="configuring-adaptive-sampling"></a>Konfigurace adaptivní vzorkování ###

V [soubor ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md), můžete upravit několik parametrů v `AdaptiveSamplingTelemetryProcessor` uzlu. Na obrázcích, zobrazí se výchozí hodnoty:

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`
  
    Rychlost cíl, který adaptivní algoritmus pro **na každém hostiteli serveru**. Pokud vaše webová aplikace spuštěna v mnoha hostitelích, zmenšete tuto hodnotu tak, aby zůstaly ve vaší cílové míru přenosu dat na portálu služby Application Insights.
* `<EvaluationInterval>00:00:15</EvaluationInterval>` 
  
    Interval, ve kterém se znovu zhodnotí aktuální míra telemetrie. Vyhodnocení se provádí jako klouzavý průměr. Můžete chtít zkrátit tento interval, při které by mohly vést k náhlým nárůstům vaší telemetrie.
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
  
    Hodnota přiřazená jenom po spuštění aplikace. Není to snížit při ladění. 

* `<ExcludedTypes>Trace;Exception</ExcludedTypes>`
  
    Středníkem oddělený seznam typů, které nechcete odeberou. Rozpoznat typy jsou: závislost, události, výjimky, zobrazení stránky, požadavek, trasování. Všechny instance určené typy jsou přenášeny; typy, které nejsou zadané, jsou vzorkovány.

* `<IncludedTypes>Request;Dependency</IncludedTypes>`
  
    Středníkem oddělený seznam typů, které mají být prováděno vzorkování. Rozpoznat typy jsou: závislost, události, výjimky, zobrazení stránky, požadavek, trasování. Zadané typy jsou vzorkovány; všechny instance ostatních typů budou vždy odeslány.


**Vypnout** adaptivní vzorkování, uzlu AdaptiveSamplingTelemetryProcessor odebrání applicationinsights-config.

### <a name="alternative-configure-adaptive-sampling-in-code"></a>Ve zkratce: Konfigurace adaptivním vzorkování v kódu
Namísto nastavení vzorkování parametru v souboru .config, můžete prostřednictvím kódu programu nastavit tyto hodnoty. To umožňuje určit funkce zpětného volání, které je voláno pokaždé, když se znovu zhodnotí míra vzorkování. Můžete to, například použít a zjistěte, jaké míry vzorkování se používá.

Odeberte `AdaptiveSamplingTelemetryProcessor` ze souboru .config.

*C#*

```csharp

    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.Channel.Implementation;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    ...

    var adaptiveSamplingSettings = new SamplingPercentageEstimatorSettings();

    // Optional: here you can adjust the settings from their defaults.

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;

    builder.UseAdaptiveSampling(
         adaptiveSamplingSettings,

        // Callback on rate re-evaluation:
        (double afterSamplingTelemetryItemRatePerSecond,
         double currentSamplingPercentage,
         double newSamplingPercentage,
         bool isSamplingPercentageChanged,
         SamplingPercentageEstimatorSettings s
        ) =>
        {
          if (isSamplingPercentageChanged)
          {
             // Report the sampling rate.
             telemetryClient.TrackMetric("samplingPercentage", newSamplingPercentage);
          }
      });

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

([Další informace o telemetrii procesory](app-insights-api-filtering-sampling.md#filtering).)

<a name="other-web-pages"></a>

## <a name="sampling-for-web-pages-with-javascript"></a>Vzorkování pro webové stránky s použitím jazyka JavaScript
Můžete nakonfigurovat webových stránek – frekvence vzorkování z jakéhokoli serveru. 

Když jste [konfigurace webové stránky pro službu Application Insights](app-insights-javascript.md), upravte fragment kódu jazyka JavaScript, který můžete získat z portálu služby Application Insights. (V aplikacích technologie ASP.NET, fragment obvykle přejde v _Layout.cshtml.)  Vložit řádek podobný `samplingPercentage: 10,` před Instrumentační klíč:

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

## <a name="fixed-rate-sampling-for-aspnet-and-java-web-sites"></a>Míra vzorkování pro weby technologie ASP.NET a jazyka Java
Pevná sazba vzorkování omezuje provoz odeslaný z webového serveru a webových prohlížečů. Na rozdíl od adaptivního vzorkování snižuje telemetrie s pevnou sazbou rozhodla sami. Také synchronizuje klient a server vzorkování tak, aby se zachovají související položky – například při pohledu na zobrazení stránky v hledání můžete najít jeho související požadavek.

Algoritmus vzorkování uchovává související položky. Pro každý požadavek HTTP události, žádost a její související události jsou zahozeny nebo přenášet společně. 

V Průzkumníku metrik kurzy, jako jsou počty požadavků a výjimek násobí faktor jako kompenzaci za vzorkovací frekvenci tak, aby byly přibližně správné.

### <a name="configuring-fixed-rate-sampling-in-aspnet"></a>Konfigurace – míra vzorkování v ASP.NET ###

1. **Aktualizovat balíčky NuGet projektu** na nejnovější verzi *předběžné verze* verze služby Application Insights. V sadě Visual Studio, klikněte pravým tlačítkem na projekt v Průzkumníku řešení, vyberte spravovat balíčky NuGet, zkontrolujte **zahrnout předběžné verze** a vyhledejte Microsoft.ApplicationInsights.Web. 
2. **Zakázat adaptivního vzorkování**: V [soubor ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md), odstranit nebo okomentovat `AdaptiveSamplingTelemetryProcessor` uzlu.
   
    ```xml
   
    <TelemetryProcessors>
   
    <!-- Disabled adaptive sampling:
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    -->

    ```

3. **Povolte modul-frekvence vzorkování.** Přidejte tento fragment kódu pro [soubor ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md):
   
    ```XML
   
    <TelemetryProcessors>
     <Add  Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
   
      <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
      <SamplingPercentage>10</SamplingPercentage>
      </Add>
    </TelemetryProcessors>
   
    ```

### <a name="configuring-fixed-rate-sampling-in-java"></a>Konfigurace – míra vzorkování v jazyce JAVA ###

1. Stáhnout a nakonfigurovat webovou aplikaci s nejnovější [application insights java SDK](app-insights-java-get-started.md)

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
Jsou typy telemetrie, které mohou být zahrnuty nebo vyloučeny ze vzorkování: závislost, události, výjimky, zobrazení stránky, požadavek a trasování.

> [!NOTE]
> Procento vzorkování zvolte procento blíží 100/N, kde N je celé číslo.  Aktuálně vzorkování není podporováno jiné hodnoty.
> 
> 

### <a name="alternative-enable-fixed-rate-sampling-in-your-server-code"></a>Ve zkratce: Povolte – míra vzorkování v serverovém kódu
Namísto nastavení vzorkování parametru v souboru .config, můžete prostřednictvím kódu programu nastavit tyto hodnoty. 

*C#*

```csharp

    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    ...

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;
    builder.UseSampling(10.0); // percentage

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

([Další informace o telemetrii procesory](app-insights-api-filtering-sampling.md#filtering).)

## <a name="when-to-use-sampling"></a>Kdy použít vzorkování?
Pokud používáte 2.0.0-beta3 verze sady SDK technologie ASP.NET je automaticky povolené adaptivní vzorkování nebo novější. Bez ohledu na to, kterou verzi sady SDK můžete použít můžete povolit vzorkování příjmu povolit Application Insights pro vzorkování se shromážděná data.

Ve výchozím nastavení je povoleno žádné vzorkování v sadě Java SDK. V současné době podporuje pouze dlouhodobý frekvence vzorkování. Adaptivní vzorkování není podporováno v sadě Java SDK.

Obecně platí pro většinu aplikací malé a střední nepotřebujete vzorkování. Nejužitečnější diagnostické informace a co nejvíce zpřesnili statistiky jsou získány pomocí shromažďování dat na všech aktivit uživatelů. 

Hlavní výhody vzorkování jsou:

* Application Insights service drops ("omezení") datových bodů když vaše aplikace odesílá velmi vysoká míra telemetrie v krátkých časový interval. 
* Aby se v rámci [kvóty](app-insights-pricing.md) datových bodů pro svou cenovou úroveň. 
* Omezit přenos v síti z kolekce telemetrie. 

### <a name="which-type-of-sampling-should-i-use"></a>Jaký typ vzorkování mám použít?
**Pomocí vzorkování příjmu, pokud:**

* Často projít kvóta měsíčního počtu telemetrická data.
* Používáte verzi sady SDK, která nepodporuje vzorkování – například technologie ASP.NET verze starší než 2.
* Získáváte mnoho telemetrických dat z webových prohlížečů uživatelů.

**Použijte pevnou sazbou vzorkování, pokud:**

* Používáte sadu SDK Application Insights pro ASP.NET web services verze 2.0.0 nebo novější nebo v2.0.1 sady Java SDK nebo novější, a
* Chcete, aby synchronizovaná vzorkování mezi klientem a serverem, tak, že když zkoumáte události v [hledání](app-insights-diagnostic-search.md), můžete procházet mezi souvisejícími událostmi na klientovi a serveru, jako je například zobrazení stránky a požadavky http.
* Jste si jisti procenta vzorkování vhodné pro vaši aplikaci. Měla by být dostatečně vysoká, aby získání přesných metrik, ale pod sazbu, která překračuje cenovou kvóty a omezení. 

**Použijte adaptivní vzorkování:**

Pokud podmínky použití jiné formy vzorkování se nedá použít, doporučujeme, abyste adaptivního vzorkování. Tato možnost je povolena ve výchozím nastavení na serveru ASP.NET sadu SDK verze 2.0.0-beta3 nebo novější. Nesníží provozu, dokud nebude dosaženo určitou minimální míru, proto lokalit s nízkou použijte tyto zásady neovlivní.

## <a name="how-do-i-know-whether-sampling-is-in-operation"></a>Jak zjistím, zda vzorkování je v provozu?
Chcete-li zjistit skutečné vzorkovací frekvenci bez ohledu na to, kde byl použit, použijte [dotazu Analytics](app-insights-analytics.md) takovou situaci:

```
union * 
| where timestamp > ago(1d)
| summarize 100/avg(itemCount) by bin(timestamp, 1h), itemType
| render timechart 
```

V každém uchovávají záznam, `itemCount` označuje počet původní záznamy, které představuje, rovno 1 + Počet zrušených předchozí záznamů. 

## <a name="how-does-sampling-work"></a>Jak funguje vzorkování?
Míra vzorkování funkce sady SDK v technologii ASP.NET verze 2.0.0 a sady Java SDK verze 2.0.1 a a vyšší. Adaptivní vzorkování je funkce sady SDK technologie ASP.NET verze 2.0.0 a vyšší. Vzorkování příjmu je funkce služby Application Insights a může být v operaci, pokud sada SDK není provádění vzorkování. 

Algoritmus vzorkování rozhodne telemetrie položky, které chcete vyřadit a ty, které chcete zachovat (ať už je v sadě SDK nebo ve službě Application Insights). Rozhodnutí vzorkování je založeno na několik pravidel, která se mají zachovat všechny vzájemně propojené datové body beze změn, udržování diagnostického prostředí ve službě Application Insights, který je užitečné a spolehlivé i s menší datové sady. Například pokud vaše aplikace odesílá pro chybné žádosti položky další telemetrické údaje (například výjimky a trasování, přihlášení z této žádosti), vzorkování nebude rozdělit tuto žádost a další telemetrie. To zajišťuje nebo jejich zahodí všechno dohromady. V důsledku toho při pohledu na podrobnosti o žádosti ve službě Application Insights se vždy zobrazí požadavek spolu s položkami jeho přidružená telemetrická data. 

Rozhodnutí vzorkování je založeno na id operace požadavku, což znamená, že všech položkách telemetrie, které patří do určité operace je zachována nebo vyřadit. Pro položky telemetrie, které nemají operace id sady (pro Příklady položek telemetrie hlášená z asynchronní vlákna s žádný kontext http) vzorkování jednoduše zaznamená procento položky telemetrie každého typu. Před 2.5.0-beta2 sady .NET SDK a 2.2.0-beta3 ASP.NET Core SDK, vzorkování rozhodnutí bylo založeno na hodnotu hash id uživatele pro aplikace, které definují "user" (to znamená, obvykle webové aplikace). Typy aplikací, které nebyly definovat uživatele (například webové služby) byl rozhodnutí vzorkování podle id operace požadavku.

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
*Proč není vzorkování jednoduché "shromažďování X procento každý typ telemetrických dat"?*

* Tento přístup vzorkování by nabízejí s velmi vysokou přesností v metriky rovin útoku, by narušil možnosti pro korelaci dat diagnostiky za uživatele, relace a požadavku, což je velmi důležité pro diagnostiku. Proto se lépe funguje vzorkování s "shromažďování všech položkách telemetrie pro X procento uživatelů aplikace" nebo "shromažďovat veškerá telemetrie pro X procento žádostí aplikace" logiku. Pro položky telemetrie nejsou přidružené k požadavků (jako je asynchronní zpracování na pozadí), je zpátky na podzim "shromažďování X procento všechny položky pro každý typ telemetrie." 

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

* Vzorkování příjmu může dojít automaticky pro žádnou telemetrii nad určitý objem, pokud sada SDK není vzorkování. To by fungovalo, například pokud používáte starší verzi sady SDK technologie ASP.NET nebo předchozí verze Java SDK(1.0.10 or before).
* Pokud používáte sady SDK technologie ASP.NET verze 2.0.0 a vyšší (hostované v Azure nebo na vlastním serveru), získáte adaptivní vzorkování ve výchozím nastavení, ale můžete přepnout na pevnou sazbou, jak je popsáno výše. S pevnou sazbou vzorkování prohlížeče sady SDK automaticky synchronizuje pro vzorkování souvisejících událostí. 
* Pokud používáte sadu Java SDK verze 2.0.1 nebo vyšší můžete nakonfigurovat soubor ApplicationInsights.xml zapnout pevné frekvence vzorkování. Vzorkování je vypnuto ve výchozím nastavení. S pevnou sazbou vzorkování prohlížeče sady SDK automaticky synchronizuje pro vzorkování souvisejících událostí.

*Existují určité výjimečných událostí, které chci vždy zobrazit. Jak získám je poslední vzorkování modulu?*

* Inicializujte samostatnou instanci TelemetryClient s novou TelemetryConfiguration (ne výchozí aktivní). Použijte k odesílání výjimečných událostí.

## <a name="next-steps"></a>Další postup
* [Filtrování](app-insights-api-filtering-sampling.md) může poskytovat další striktní kontrolu nad co vaše sada SDK odesílá.

