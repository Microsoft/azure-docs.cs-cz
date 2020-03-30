---
title: Analýza využití pomocí přehledů aplikací Azure | Dokumenty Microsoftu
description: Seznamte se s uživateli a s tím, co s vaší aplikací dělají.
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: e964b1b5b9d5500f2d9f24ed765299389e6dbbb9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283952"
---
# <a name="usage-analysis-with-application-insights"></a>Analýzy používání pomocí nástroje Application Insights

Které funkce vašeho webu nebo mobilní aplikace jsou nejoblíbenější? Dosahují vaši uživatelé pomocí aplikace svých cílů? Mají vypadnout na konkrétních místech, a oni se vrátí později?  [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) vám pomůže získat výkonné přehledy o tom, jak lidé používají vaši aplikaci. Pokaždé, když aktualizujete aplikaci, můžete posoudit, jak dobře to funguje pro uživatele. S těmito znalostmi můžete provádět rozhodnutí založená na datech o dalších vývojových cyklech.

## <a name="send-telemetry-from-your-app"></a>Odeslání telemetrie z aplikace

Nejlepší prostředí se získá instalací Application Insights do kódu aplikačního serveru i do webových stránek. Klientské a serverové součásti vaší aplikace odesílají telemetrická data zpět na portál Azure k analýze.

1. **Kód serveru:** Nainstalujte příslušný modul pro [ASP.NET](../../azure-monitor/app/asp-net.md), [Azure](../../azure-monitor/app/app-insights-overview.md), [Java](../../azure-monitor/app/java-get-started.md), [Node.js](../../azure-monitor/app/nodejs.md)nebo [jiné](../../azure-monitor/app/platforms.md) aplikace.

    * *Nechcete nainstalovat kód serveru? Stačí [vytvořit prostředek Azure Application Insights](../../azure-monitor/app/create-new-resource.md ).*

2. **Kód webové stránky:** Před uzávěrkou ``</head>``přidejte na webovou stránku následující skript . Nahraďte klíč instrumentace příslušnou hodnotou pro váš prostředek Application Insights:
    
    ```html
    <script type="text/javascript">
    var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
    {
      instrumentationKey:"INSTRUMENTATION_KEY"
    }
    );window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
    </script>
    ```

    Chcete-li se dozvědět pokročilejší konfigurace pro sledování webových stránek, podívejte se na [referenční článek sady JavaScript SDK](https://docs.microsoft.com/azure/azure-monitor/app/javascript).

3. **Kód mobilní aplikace:** Pomocí sady App Center SDK můžete shromažďovat události z vaší aplikace a pak odeslat kopie těchto událostí do Application Insights k analýze [podle tohoto průvodce](../../azure-monitor/learn/mobile-center-quickstart.md).

4. **Získejte telemetrii:** Spusťte projekt v režimu ladění po dobu několika minut a pak vyhledejte výsledky v okně Přehled v Application Insights.

    Publikujte aplikaci, abyste sledovali výkon aplikace a zjistili, co uživatelé s vaší aplikací dělají.

## <a name="include-user-and-session-id-in-your-telemetry"></a>Zahrnutí ID uživatele a relace do telemetrie
Ke sledování uživatelů v průběhu času, Application Insights vyžaduje způsob, jak je identifikovat. Nástroj Události je jediným nástrojem použití, který nevyžaduje ID uživatele nebo ID relace.

Začněte odesílat ID uživatelů a relací pomocí [tohoto procesu](https://docs.microsoft.com/azure/application-insights/app-insights-usage-send-user-context).

## <a name="explore-usage-demographics-and-statistics"></a>Prozkoumejte demografické údaje a statistiky využití
Zjistěte, kdy lidé používají vaši aplikaci, jaké stránky je nejvíce zajímají, kde se uživatelé nacházejí, jaké prohlížeče a operační systémy používají. 

Sestavy Uživatelé a relace filtrují data podle stránek nebo vlastních událostí a segmentují je podle vlastností, jako je umístění, prostředí a stránka. Můžete také přidat vlastní filtry.

![Uživatelé](./media/usage-overview/users.png)  

Přehledy na správné poukazují na zajímavé vzory v sadě dat.  

* Sestava **Uživatelé** počítá počet jedinečných uživatelů, kteří přistupují k vašim stránkám ve zvolených časových obdobích. U webových aplikací se uživatelé počítají pomocí souborů cookie. Pokud někdo přistupuje k vašemu webu pomocí různých prohlížečů nebo klientských počítačů nebo vymaže soubory cookie, bude započítán více než jednou.
* Sestava **Relace** spočítá počet uživatelských relací, které přistupují k vašemu webu. Relace je období aktivity uživatele, ukončené obdobím nečinnosti delší než půl hodiny.

[Další informace o nástrojích Uživatelé, Relace a Události](usage-segmentation.md)  

## <a name="retention---how-many-users-come-back"></a>Retence - kolik uživatelů se vrátí?

Uchovávání informací vám pomůže pochopit, jak často se uživatelé vracejí k používání své aplikace na základě kohort uživatelů, kteří během určitého časového intervalu provedli nějakou obchodní akci. 

- Zjistěte, jaké konkrétní funkce způsobují, že se uživatelé mohou vrátit více než ostatní 
- Formuláře hypotéz y založené na skutečných uživatelských datech 
- Zjištění, zda je uchovávání informací v produktu problémem 

![Uchovávání](./media/usage-overview/retention.png) 

Ovládací prvky uchovávání informací v horní části umožňují definovat konkrétní události a časový rozsah pro výpočet uchovávání informací. Graf uprostřed poskytuje vizuální znázornění celkového procenta uchování podle zadaného časového rozsahu. Graf v dolní části představuje individuální retenci v daném časovém období. Tato úroveň podrobností umožňuje pochopit, co vaši uživatelé dělají a co může ovlivnit vracející se uživatelé na podrobnější rozlišovací schopnost.  

[Další informace o nástroji pro uchovávání informací](usage-retention.md)

## <a name="custom-business-events"></a>Vlastní obchodní události

Chcete-li získat jasnou představu o tom, co uživatelé dělají s vaší aplikací, je užitečné vložit řádky kódu pro protokolování vlastních událostí. Tyto události mohou sledovat cokoli od podrobných uživatelských akcí, jako je kliknutí na konkrétní tlačítka, až po významnější obchodní události, jako je nákup nebo výhra ve hře. 

Ačkoli v některých případech mohou zobrazení stránek představovat užitečné události, obecně se to neplatí. Uživatel může otevřít stránku produktu bez zakoupení produktu. 

Pomocí konkrétních obchodních událostí můžete na webu zmapovat postup uživatelů. Můžete zjistit jejich preference pro různé možnosti, a kde vypadnou nebo mají potíže. S těmito znalostmi můžete činit informovaná rozhodnutí o prioritách ve vašem nevyřízeném položkách vývoje.

Události lze protokolovat z klientské strany aplikace:

```JavaScript

    appInsights.trackEvent("ExpandDetailTab", {DetailTab: tabName});
```

Nebo ze strany serveru:

```csharp
    var tc = new Microsoft.ApplicationInsights.TelemetryClient();
    tc.TrackEvent("CreatedAccount", new Dictionary<string,string> {"AccountType":account.Type}, null);
    ...
    tc.TrackEvent("AddedItemToCart", new Dictionary<string,string> {"Item":item.Name}, null);
    ...
    tc.TrackEvent("CompletedPurchase");
```

K těmto událostem můžete připojit hodnoty vlastností, abyste je mohli filtrovat nebo rozdělit při jejich kontrole na portálu. Kromě toho je ke každé události připojena standardní sada vlastností, například anonymní ID uživatele, které umožňuje sledovat posloupnost aktivit jednotlivých uživatelů.

Další informace o [vlastních událostech](../../azure-monitor/app/api-custom-events-metrics.md#trackevent) a [vlastnostech](../../azure-monitor/app/api-custom-events-metrics.md#properties).

### <a name="slice-and-dice-events"></a>Události řezu a kostky

V nástrojích Uživatelé, Relace a Události můžete vlastní události řezat a odřezávat podle uživatele, názvu události a vlastností.
![Uživatelé](./media/usage-overview/users.png)  
  
## <a name="design-the-telemetry-with-the-app"></a>Návrh telemetrie pomocí aplikace

Při navrhování jednotlivých funkcí aplikace zvažte, jak budete měřit její úspěch s uživateli. Rozhodněte se, jaké obchodní události potřebujete zaznamenat, a od začátku zakódujte sledování těchto událostí do aplikace.

## <a name="a--b-testing"></a>A | B Testování
Pokud nevíte, která varianta funkce bude úspěšnější, uvolněte obě, zpřístupníte je různým uživatelům. Změřte úspěch každého z nich a pak přejděte na jednotnou verzi.

Pro tuto techniku připojit odlišné hodnoty vlastností pro všechny telemetrie, která je odeslána každou verzi aplikace. Můžete to udělat definováním vlastností v aktivní mašitce TelemetryContext. Tyto výchozí vlastnosti jsou přidány do každé telemetrické zprávy, které aplikace odešle – nejen vlastní zprávy, ale také standardní telemetrie.

Na portálu Application Insights můžete data filtrovat a rozdělit na hodnoty vlastností, aby bylo účelem porovnání různých verzí.

Chcete-li to provést, [nastavte inicializátor telemetrie](../../azure-monitor/app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer):

**Aplikace v ASP.NET**

```csharp
    // Telemetry initializer class
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry item)
            {
                var itemProperties = item as ISupportProperties;
                if (itemProperties != null && !itemProperties.Properties.ContainsKey("AppVersion"))
                {
                    itemProperties.Properties["AppVersion"] = "v2.1";
                }
            }
    }
```

Ve inicializátoru webové aplikace, například Global.asax.cs:

```csharp

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
         .Add(new MyTelemetryInitializer());
    }
```

**Aplikace ASP.NET Core**

> [!NOTE]
> Přidání inicializéru pomocí `ApplicationInsights.config` nebo použití `TelemetryConfiguration.Active` není platné pro ASP.NET základní aplikace. 

Pro [ASP.NET základní](asp-net-core.md#adding-telemetryinitializers) aplikace, `TelemetryInitializer` přidání nového se provádí přidáním do kontejneru vkládání závislostí, jak je znázorněno níže. To se `ConfigureServices` provádí metodou `Startup.cs` vaší třídy.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

Všechny nové TelemetryClients automaticky přidat hodnotu vlastnosti, kterou zadáte. Jednotlivé události telemetrie můžete přepsat výchozí hodnoty.

## <a name="next-steps"></a>Další kroky
   - [Uživatelé, relace, události](usage-segmentation.md)
   - [Trychtýře](usage-funnels.md)
   - [Uchovávání](usage-retention.md)
   - [Toky uživatele](usage-flows.md)
   - [Workbooks](../../azure-monitor/app/usage-workbooks.md)
   - [Přidání kontextu uživatele](usage-send-user-context.md)
