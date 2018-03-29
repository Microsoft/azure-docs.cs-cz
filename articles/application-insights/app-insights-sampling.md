---
title: Vzorkování telemetrii ve službě Azure Application Insights | Microsoft Docs
description: Jak zajistit, aby svazek telemetrie pod kontrolou.
services: application-insights
documentationcenter: windows
author: vgorbenko
manager: carmonm
ms.assetid: 015ab744-d514-42c0-8553-8410eef00368
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: mbullwin
ms.openlocfilehash: 300b9b7786c17972c5c48df7e5b6d28491adc095
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2018
---
# <a name="sampling-in-application-insights"></a>Vzorkování ve službě Application Insights


Vzorkování je funkce v [Azure Application Insights](app-insights-overview.md). Je doporučeným způsobem, jak snížit telemetrie provozu a úložiště, při zachování statisticky správné analýzy dat aplikací. Filtr vybere položky, které se vztahují, tak, aby můžete procházet mezi položkami při provádění diagnostických šetření.
Kdy metriky počty se zobrazí na portálu, jsou renormalized vzít v úvahu vzorkování, aby se minimalizoval vliv na statistiku.

Vzorkování snižuje náklady na provoz a data a umožňuje vyhnout se omezení.

## <a name="in-brief"></a>Stručný postup:
* Vzorkování uchovává 1 v *n* zaznamenává a zahodí zbytek. Například je může zachovat události 1 v 5, vzorkovací frekvenci 20 %. 
* Vzorkování se stane automaticky, když vaše aplikace odešle velké množství telemetrických dat, v aplikacích pro ASP.NET web server.
* Vzorkování ručně, můžete také nastavit buď na portálu v na stránce s cenami; nebo v sadě SDK technologie ASP.NET v souboru config; nebo v jazyce Java SDK v souboru ApplicationInsights.xml také omezit přenos v síti.
* Pokud protokolu vlastní události a chcete zajistit, že sadu událostí, které je buď uchovávají nebo zrušených společně, ujistěte se, že mají stejnou hodnotu OperationId.
* Vzorkování dělitel *n* údajně všechny záznamy ve vlastnosti `itemCount`, která v hledání se zobrazí pod popisným názvem "počtu žádostí o" nebo "počet událostí". Pokud vzorkování není v provozu se `itemCount==1`.
* Pokud píšete analytické dotazy, měli byste [vzít v úvahu vzorkování](app-insights-analytics-tour.md#counting-sampled-data). Konkrétně místo jednoduše počítání záznamy, měli byste použít `summarize sum(itemCount)`.

## <a name="types-of-sampling"></a>Typy vzorkování
Existují tři metody vzorkování alternativní:

* **Adaptivního vzorkování** automaticky přizpůsobí objem telemetrická data odesílaná ze sady SDK v aplikaci ASP.NET. Od verze sady SDK v 2.0.0-beta3 Toto je výchozí metoda vzorkování. Adaptivního vzorkování je aktuálně k dispozici pouze pro telemetrických dat na straně serveru technologie ASP.NET. 
* **Míry vzorkování** snižuje objem telemetrická data odesílaná ze svého technologie ASP.NET nebo Java serveru a z prohlížečů uživatelů. Můžete nastavit rychlost. Klient a server bude synchronizovat jejich vzorkování tak, že v hledání, mohou procházet mezi zobrazení související stránky a požadavky.
* **Přijímání vzorkování** funguje na portálu Azure. Zahodí některé telemetrická data přenášená z vaší aplikace na vzorkovací frekvenci, který nastavíte. Nedojde k omezení přenosy telemetrie z vaší aplikace, ale umožňuje udržovat v rámci měsíční kvóta. Hlavní výhodou přijímání vzorkování je, že nastavíte vzorkovací frekvenci bez opětovného nasazení aplikace a funguje jednotně pro všechny servery a klienty. 

Pokud Adaptivní nebo pevné míry vzorkování v operaci, přijímání vzorkování je zakázána.

## <a name="ingestion-sampling"></a>Přijímání vzorkování
Tato forma vzorkování funguje v okamžiku, kdy telemetrie z vaší webový server, prohlížečů a zařízení dosáhne koncového bodu služby Application Insights. I když nesnižuje přenosy telemetrie z vaší aplikace, snížení objemu zpracovaných a zachovává (a účtovat poplatek za) pomocí Application Insights.

Tento typ vzorkování použijte, pokud vaše aplikace často prochází přes jeho měsíční kvóta a nemáte možnost pomocí sady SDK na základě typů vzorkování. 

Nastavení míry vzorkování v kvóty a ceny okno:

![V okně Přehled aplikace klikněte na tlačítko Nastavení, kvóty a ukázky, pak vyberte vzorkovací frekvenci a kliknutím na tlačítko Aktualizovat.](./media/app-insights-sampling/04.png)

Podobně jako ostatní typy vzorkování zachová algoritmus telemetrii související položky. Například při jste kontrole telemetrii ve vyhledávání, budete moci najít žádosti související s konkrétní výjimka položku. Metrika počítá jako je například rychlost požadavků a rychlost výjimka správně zachovány.

Datové body, které jsou zrušených vzorkování nejsou k dispozici ve všech funkcí, Application Insights, jako [průběžné exportovat](app-insights-export-telemetry.md).

Přijímání vzorkování nepracuje při vzorkování Adaptivní nebo pevnou sazbou na základě sady SDK je v provozu. Poznámka: ve výchozím nastavení je povoleno adaptivního vzorkování, pokud je povolená sady SDK technologie ASP.NET v sadě Visual Studio nebo pomocí monitorování stavu a přijímání vzorkování je zakázána. Pokud míry vzorkování na sadu SDK je menší než 100 %, je ignorována vzorkovací frekvenci přijímání, který nastavíte.

> [!WARNING]
> Hodnota použitá na dlaždici označuje hodnotu, která nastavíte pro přijímání vzorkování. Pokud vzorkování SDK je v provozu nepředstavuje skutečný vzorkovací frekvenci.
> 
> 

## <a name="adaptive-sampling-at-your-web-server"></a>Adaptivního vzorkování na webovém serveru
Adaptivního vzorkování je k dispozici pro Application Insights SDK pro aplikace ASP.NET v 2.0.0-beta3 nebo novější a je ve výchozím nastavení povolené. 

Adaptivního vzorkování ovlivňuje objem telemetrická data odesílaná ze serveru webové aplikace do koncového bodu služby Application Insights. Svazek je automaticky upravována nedovoluje zadaný maximální rychlost přenosu.

Ho nepracuje na nízkou svazky telemetrie, takže aplikace při ladění nebo nebude mít vliv na web s nízkou využití.

K dosažení cílový svazek, některé telemetrická generovaným se zahodí. Ale stejně jako jiné typy vzorkování algoritmus zachová související telemetrii položky. Například při jste kontrole telemetrii ve vyhledávání, budete moci najít žádosti související s konkrétní výjimka položku. 

Metrika počítá jako je například rychlost požadavků a rychlost výjimka upraveny tak, aby kompenzovat vzorkovací frekvenci, aby zobrazovala přibližně správné hodnoty v Průzkumníku metrika.

### <a name="update-nuget-packages"></a>Aktualizovat balíčky NuGet ###

Aktualizace vašeho projektu balíčky NuGet na nejnovější *předběžné verze* verzi Application Insights. V sadě Visual Studio, klikněte pravým tlačítkem na projekt v Průzkumníku řešení, zvolte spravovat balíčky NuGet, zkontrolujte **zahrnout předběžné verze** a vyhledejte Microsoft.ApplicationInsights.Web. 

### <a name="configuring-adaptive-sampling"></a>Konfigurace adaptivního vzorkování ###

V [souboru ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md), můžete upravit několik parametrů v `AdaptiveSamplingTelemetryProcessor` uzlu. Následující obrázky, zobrazí se výchozí hodnoty:

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`
  
    Cíl rychlost, jakou adaptivní algoritmus cíle na **na každém hostiteli serveru**. Pokud vaše webová aplikace běží na mnoho hostitelů, snižte tuto hodnotu tak, aby se v rámci vaší cílový počet přenosů dat na portálu služby Application Insights.
* `<EvaluationInterval>00:00:15</EvaluationInterval>` 
  
    Interval, ve kterém se znovu zhodnotí aktuální rychlost telemetrie. Hodnocení je provést, protože klouzavého průměru. Můžete tak, aby zkrátil tento interval, pokud by mohl nečekané shluky telemetrie.
* `<SamplingPercentageDecreaseTimeout>00:02:00</SamplingPercentageDecreaseTimeout>`
  
    Když vzorkování procentuální hodnotu změny, jak brzy po jsme mohou snížit procento vzorkování znovu k zaznamenání dat o menší.
* `<SamplingPercentageIncreaseTimeout>00:15:00</SamplingPercentageIncreaseTimeout>`
  
    Když vzorkování procentuální hodnotu změny, jak od jsme mohou zvýšit procento vzorkování znovu k zaznamenání dat o další.
* `<MinSamplingPercentage>0.1</MinSamplingPercentage>`
  
    Protože vzorkování procento liší, co je minimální hodnota, kterou jsme máte oprávnění k nastavení.
* `<MaxSamplingPercentage>100.0</MaxSamplingPercentage>`
  
    Protože vzorkování procento liší, co je maximální hodnota, kterou jsme máte oprávnění k nastavení.
* `<MovingAverageRatio>0.25</MovingAverageRatio>` 
  
    Při výpočtu klouzavého průměru přiřadit váhu většina hodnot. Použijte hodnotu rovna nebo menší než 1. Menší hodnoty proveďte algoritmus méně reaktivní k nečekané změny.
* `<InitialSamplingPercentage>100</InitialSamplingPercentage>`
  
    Hodnotu přiřazenou když aplikaci právě bylo zahájeno. Není to snížit při ladění. 

* `<ExcludedTypes>Trace;Exception</ExcludedTypes>`
  
    Středníky oddělený seznam typů, které nechcete se odeberou. Rozpoznat typy jsou: závislost, události, výjimky, stránkové zobrazení, požadavku, trasování. Všechny instance určené typy přenášejí; typy, které nebyly zadány jsou odebírána data.

* `<IncludedTypes>Request;Dependency</IncludedTypes>`
  
    Středníky oddělený seznam typů, které chcete se odeberou. Rozpoznat typy jsou: závislost, události, výjimky, stránkové zobrazení, požadavku, trasování. Zadané typy jsou odebírána data; nepřenáší se vždy všechny instance ostatních typů.


**Chcete-li vypnout** adaptivního vzorkování, odebrat uzel AdaptiveSamplingTelemetryProcessor z applicationinsights-config.

### <a name="alternative-configure-adaptive-sampling-in-code"></a>Alternativní: Konfigurace adaptivního vzorkování v kódu
Místo v souboru config nastavení parametru vzorkování, můžete programově nastavit tyto hodnoty. To umožňuje zadat funkce zpětného volání, která je volána vždy, když se znovu zhodnotí míry vzorkování. Můžete to, například použít a zjistěte, jaké míry vzorkování je používán.

Odeberte `AdaptiveSamplingTelemetryProcessor` uzlu ze souboru config.

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

([Další informace o telemetrie procesory](app-insights-api-filtering-sampling.md#filtering).)

<a name="other-web-pages"></a>

## <a name="sampling-for-web-pages-with-javascript"></a>Vzorkování pro webové stránky v jazyce JavaScript
Můžete nakonfigurovat webové stránky pro – míra vzorkování z jakéhokoli serveru. 

Pokud jste [konfigurace webové stránky pro službu Application Insights](app-insights-javascript.md), upravit fragment kódu jazyka JavaScript, kterou můžete získat z portálu služby Application Insights. (V aplikacích ASP.NET fragmentu obvykle bude v _Layout.cshtml.)  Vložit řádek jako `samplingPercentage: 10,` před klíč instrumentace:

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

Procento vzorkování zvolte procentuální hodnotu, která je blízko 100/N, kde N je celé číslo.  Aktuálně vzorkování nepodporuje ostatní hodnoty.

Pokud povolíte také – míra vzorkování na serveru, klienty a server bude synchronizovat tak, aby toto, v hledání mezi můžete procházet zobrazení související stránky a požadavky.

## <a name="fixed-rate-sampling-for-aspnet-and-java-web-sites"></a>Míry vzorkování pro weby technologie ASP.NET a Java
Pevné míry vzorkování omezuje provoz odesílaný z webového serveru a webových prohlížečů. Na rozdíl od adaptivního vzorkování, snižuje telemetrická data s pevnou sazbou, které jste se rozhodli. Také synchronizuje klient a server vzorkování tak, aby se zachovají související položky – například když se podíváte na zobrazení stránky ve vyhledávání, můžete vyhledat související požadavku.

Algoritmus vzorkování zachová související položky. Pro každý požadavek HTTP událostí, žádost a její související události jsou buď zrušených nebo přenášených společně. 

V Průzkumníku metrik jsou sazby, jako je například požadavek a výjimky počty vynásobí faktorem kompenzovat vzorkovací frekvenci, aby byly přibližně správné.

### <a name="configuring-fixed-rate-sampling-in-aspnet"></a>Konfigurace technologie ASP.NET – míra vzorkování ###

1. **Aktualizovat balíčky NuGet projektu na** na nejnovější *předběžné verze* verzi Application Insights. V sadě Visual Studio, klikněte pravým tlačítkem na projekt v Průzkumníku řešení, zvolte spravovat balíčky NuGet, zkontrolujte **zahrnout předběžné verze** a vyhledejte Microsoft.ApplicationInsights.Web. 
2. **Zakázat adaptivního vzorkování**: V [souboru ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md), odeberte, nebo komentář `AdaptiveSamplingTelemetryProcessor` uzlu.
   
    ```xml
   
    <TelemetryProcessors>
   
    <!-- Disabled adaptive sampling:
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    -->

    ```

3. **Povolte modul – míra vzorkování.** Přidejte tento fragment k [souboru ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md):
   
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

2. **Povolit modul – míra vzorkování** přidáním následující fragment kódu do souboru ApplicationInsights.xml.

```XML
    <TelemetryProcessors>
        <BuiltInProcessors>
            <Processor type = "FixedRateSamplingTelemetryProcessor">
                <!-- Set a percentage close to 100/N where N is an integer. -->
                <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
                <Add name = "SamplingPercentage" value = "50" />
            </Processor>
        </BuilrInProcessors>
    <TelemetryProcessors/>
```

3. Můžete zahrnout nebo vyloučit konkrétní typy telemetrických dat z těchto značek uvnitř značky procesor "FixedRateSamplingTelemetryProcessor" pomocí vzorkování
```XML
    <ExcludedTypes>
        <ExcludedType>Request</ExcludedType>
    </ExcludedTypes>

    <IncludedTypes>
        <IncludedType>Exception</IncludedType>
    </IncludedTypes>
```
Typy telemetrie, které lze zahrnout nebo vyloučit z vzorkování: závislost, událostí, výjimky, stránkové zobrazení, žádost a trasování.

> [!NOTE]
> Procento vzorkování zvolte procentuální hodnotu, která je blízko 100/N, kde N je celé číslo.  Aktuálně vzorkování nepodporuje ostatní hodnoty.
> 
> 

### <a name="alternative-enable-fixed-rate-sampling-in-your-server-code"></a>Alternativní: Povolte-míry vzorkování v serverovém kódu
Místo v souboru config nastavení parametru vzorkování, můžete programově nastavit tyto hodnoty. 

*C#*

```csharp

    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    ...

    var builder = TelemetryConfiguration.Active.GetTelemetryProcessorChainBuilder();
    builder.UseSampling(10.0); // percentage

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

([Další informace o telemetrie procesory](app-insights-api-filtering-sampling.md#filtering).)

## <a name="when-to-use-sampling"></a>Kdy použít vzorkování?
Pokud používáte 2.0.0-beta3 verze sady SDK technologie ASP.NET je automaticky povolen adaptivního vzorkování nebo novější. Bez ohledu na to, kterou verzi sady SDK můžete použít můžete povolit přijímání vzorkování umožňující Application Insights zkusit shromážděná data.

Ve výchozím nastavení je žádné vzorkování povolený v jazyce Java SDK. V současné době podporuje pouze pevné míry vzorkování. V jazyce Java SDK nepodporuje adaptivního vzorkování.

Obecně platí pro většinu aplikací malé a střední velikosti nepotřebujete vzorkování. Shromažďování dat pro všechny aktivity uživatele je nejvhodnější diagnostické informace a nejpřesnější statistiky jsou získat. 

Hlavní výhody vzorkování jsou:

* Aplikace Statistika vyřazuje ("omezení") dat body služby když vaše aplikace odesílá velmi vysoká míra telemetrie v krátkém časový interval. 
* Chcete-li zachovat v rámci [kvóty](app-insights-pricing.md) datových bodů pro cenovou úroveň. 
* Z kolekce telemetrie omezit přenos v síti. 

### <a name="which-type-of-sampling-should-i-use"></a>Jaký typ vzorkování mám použít?
**Použijte přijímání vzorkování, pokud:**

* Často projít vaše měsíční kvóta telemetrie.
* Používáte verzi sady SDK, která nepodporuje vzorkování – například ASP.NET verze starší než 2.
* Vám mnoho telemetrie z webových prohlížečů uživatelů.

**Použijte – míra vzorkování, pokud:**

* Používáte Application Insights SDK pro ASP.NET web services verze 2.0.0 nebo novější nebo Java SDK v2.0.1 nebo novější, a
* Chcete, aby synchronizovaná vzorkování mezi klientem a serverem, takže když jste příčin události v [vyhledávání](app-insights-diagnostic-search.md), můžete procházet mezi souvisejícími událostmi na klientovi a serveru, například zobrazení stránky a požadavky http.
* Jste si jisti, procenta odpovídající vzorkování pro vaši aplikaci. Musí být dostatečně vysoká, aby získat přesných metrik, ale pod rychlost, který překračuje vaší cenovou kvóty a omezení omezení. 

**Použijte adaptivního vzorkování:**

Pokud se nevztahují podmínky použití jiných forem vzorkování, doporučujeme adaptivního vzorkování. Tato možnost je povolena ve výchozím nastavení v serveru technologie ASP.NET SDK verze 2.0.0-beta3 nebo novější. Nebude omezit přenos, dokud nebude dosaženo určité minimální rychlost, proto nebude mít vliv nízká použití lokalit.

## <a name="how-do-i-know-whether-sampling-is-in-operation"></a>Jak zjistím, zda je vzorkování v operaci?
Chcete-li zjistit skutečný vzorkovací frekvenci bez ohledu na to, kde byl použit, použijte [Analytics dotazu](app-insights-analytics.md) jako je tato:

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h) 
    | render areachart 

V každé uchovávají záznam, `itemCount` označuje počet původní záznamy, které představuje, rovna 1 + číslo předchozí zrušených záznamů. 

## <a name="how-does-sampling-work"></a>Jak funguje vzorkování?
Míry vzorkování funkce sady SDK v technologii ASP.NET verze z 2.0.0 a sady Java SDK verze 2.0.1 a a vyšší. Adaptivního vzorkování je funkce sady SDK v technologii ASP.NET verze z 2.0.0 a vyšší. Přijímání vzorkování je funkce služby Application Insights a může být v rámci operace, pokud sada SDK neprovádí vzorkování. 

Algoritmus vzorkování rozhodne, které telemetrie položky, chcete-li vyřadit a ty, které chcete zachovat (jestli je v sadě SDK nebo ve službě Application Insights). Rozhodnutí o vzorkování je založena na několik pravidel, která za cíl zachovat všechny body vzájemně souvisejících dat beze změn, udržování diagnostiky prostředí ve službě Application Insights, který je možné použít a spolehlivé i s menší datové sady. Například pokud vaše aplikace pro chybné žádosti odesílá další telemetrické položky (například výjimky a trasování, přihlášení z této žádosti), vzorkování nebude rozdělit tento požadavek a další telemetrií. Je buď udržuje nebo je zahodí všechny společně. Výsledkem je když se podíváte na podrobnosti požadavku ve službě Application Insights, vždy uvidíte žádost spolu s jeho položky přidružené telemetrie. 

Pro aplikace, které definují "user" (který je nejčastější webové aplikace), vzorkování rozhodnutí vycházet z hodnota hash id uživatele, což znamená, že všechny telemetrická pro konkrétní uživatele, je buď zachovaná, nebo vyřadit. Pro typy aplikací, které nejsou definovat uživatele (například webové služby) rozhodnutí vzorkování podle id operace požadavku. Telemetrie položek, které mají ani id uživatele ani operaci nastavit (pro položky telemetrie příklad nahlásila asynchronní vláken s žádný kontext http) je nakonec vzorkování jednoduše zaznamená procento položek telemetrie každého typu. 

Během zpět na zobrazení telemetrie, upraví službu Application Insights metriky stejnou vzorkování procentuální hodnotu, která byla použita v okamžiku kolekce, pro kompenzovat chybějící datové body. Proto při prohlížení telemetrii ve službě Application Insights, uživatelé se zobrazuje statisticky správné aproximace, které jsou velmi podobné reálná čísla.

Přesnost sblížení do značné míry závisí na nakonfigurovaných vzorkování procento. Navíc zvyšuje přesnost pro aplikace, které zpracovávají velké množství obecně podobné požadavky od velký počet uživatelů. Pro aplikace, které nefungují s výrazném zatížení, není na druhé straně vzorkování potřeba, protože tyto aplikace mohou zasílat obvykle jejich telemetrických dat při zachování v rámci kvóty, aniž by došlo ke ztrátě dat z omezení. 

> [!WARNING]
> Application Insights není ukázkové metriky a relací typy telemetrie. Snížení přesnost může nežádoucího vysoce pro tyto typy telemetrie.
> 

### <a name="adaptive-sampling"></a>Adaptivního vzorkování
Adaptivního vzorkování přidá komponenty, která monitoruje aktuální rychlost přenosu ze sady SDK a upraví procento vzorkování pokusí zůstat v cílové maximální rychlost přenosu. Úpravou jsou přepočítána v pravidelných intervalech a vychází z o pohyblivý průměr odchozí rychlost přenosu.

## <a name="sampling-and-the-javascript-sdk"></a>Vzorkování a JavaScript SDK
Na straně klienta (JavaScript) sady SDK se účastní – míra vzorkování ve spojení s SDK na straně serveru. Instrumentované stránky pouze odesílání telemetrických dat na straně klienta z stejným uživatelům, pro které provedené na straně serveru své rozhodnutí "ukázka." Tato logika je určen k udržení integrity uživatelské relace mezi tisk klienta a serveru stranách. V důsledku toho z libovolné položky konkrétní telemetrii ve službě Application Insights můžete najít všechny ostatní položky telemetrie pro tohoto uživatele nebo relace. 

*Moje klientské a serverové telemetrie nezobrazovat koordinované ukázky, jak se uvádí výše.*

* Povolte – míra vzorkování serveru a klienta.
* Ujistěte se, že je verze sady SDK 2.0 nebo novější.
* Zkontrolujte nastavení stejné procento vzorkování v klient i server.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy
*Proč není vzorkování jednoduchý "shromažďování X procento každého typu telemetrie"?*

* Při velmi vysokou přesnost v metriky aproximace by poskytnout tento přístup vzorkování, by rozdělit možnost koordinace diagnostických dat na uživatele, relace a požadavku, což je důležité pro diagnostiku. Proto vzorkování funguje lépe s "shromažďovat všechny telemetrie položky pro X procento uživatelů aplikace" nebo "shromažďování všech telemetrie X Procento požadavků aplikace" logiku. Pro položky telemetrie nejsou přidružené požadavků (jako je například asynchronní zpracování na pozadí), je zpátky na podzim "shromažďování X procento všechny položky pro každý typ telemetrie." 

*Časem změnit procento vzorkování?*

* Ano, adaptivního vzorkování postupně změní procento vzorkování, na základě objemu aktuálně zjištěnou telemetrie.

*Pokud se používá – míra vzorkování, jak lze zjistit, které vzorkování procento je nejvhodnější pro aplikace?*

* Jedním ze způsobů je začínat adaptivního vzorkování, podívejte se, co ohodnotit vyrovná na (viz výše otázku) a potom přepnout na pevnou sazbou vzorkování pomocí tohoto kurzu. 
  
    Máte tak snadno uhodnout. Analýza vašeho aktuálního využití telemetrie ve službě Application Insights, sledovat žádné omezení, která je a odhadnout objem shromážděných telemetrie. Tyto tři vstupy, společně s vybranou cenovou úroveň, navrhnout, kolik můžete chtít snížit objem shromážděných telemetrie. Však může zvýšit počet uživatelů nebo jiných posunutí ve svazku telemetrie neplatným odhad.

*Co se stane, když je možné nakonfigurovat vzorkování procento příliš nízko?*

* Procento příliš nízkou vzorkování (over-aggressive vzorkování) snižuje přesnost aproximace, když se pokusí odpovídajícím způsobem vizualizaci dat pro snížení objemu dat Application Insights. Navíc diagnostiky prostředí může negativně ovlivněn, některé žádosti zřídka chybě nebo pomalé může odebraných.

*Co se stane, pokud je možné nakonfigurovat vzorkování procento příliš vysoká.*

* Konfigurace vzorkování příliš vysoké procento (ne agresivní dostatečně) výsledkem na nedostatečná snížení objemu shromažďovaných telemetrie. Stále se můžete setkat ztráty dat telemetrická data týkající se omezení a náklady na používání Application Insights může být vyšší než můžete naplánovat z důvodu překročení limitu služby poplatky.

*Na platformách, které lze použít vzorkování?*

* Přijímání vzorkování může dojít automaticky pro všechny telemetrie výše určité svazku, pokud sada SDK neprovádí vzorkování. To bude fungovat, například pokud používáte starší verzi sady SDK technologie ASP.NET nebo předchozí verze Java SDK(1.0.10 or before).
* Pokud používáte sady SDK technologie ASP.NET verze 2.0.0 a vyšší (hostované v Azure nebo na vlastní server), můžete získat adaptivního vzorkování ve výchozím nastavení, ale můžete přepnout-rate, jak je popsáno výše. S pevnou sazbou vzorkování prohlížeče SDK automaticky synchronizuje zkusit související události. 
* Pokud používáte sady Java SDK verze 2.0.1 nebo vyšší můžete nakonfigurovat soubor ApplicationInsights.xml zapnutí pevné míry vzorkování. Ve výchozím nastavení vypnutý vzorkování. S pevnou sazbou vzorkování prohlížeče SDK automaticky synchronizuje zkusit související události.

*Existují určité výjimečných události, které vždy chcete vidět. Načtení je po modul vzorkování?*

* Inicializujte samostatnou instanci TelemetryClient s novou TelemetryConfiguration (není výchozí aktivní). Použijte k odesílání výjimečných událostí.

## <a name="next-steps"></a>Další postup
* [Filtrování](app-insights-api-filtering-sampling.md) můžete zadat další přísnou kontrolu co pošle váš SDK.

