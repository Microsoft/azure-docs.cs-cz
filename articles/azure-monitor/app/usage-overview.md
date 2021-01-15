---
title: Analýza využití pomocí Azure Application Insights | Dokumentace Microsoftu
description: Pochopte uživatele a to, co dělají s vaší aplikací.
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: 633d35ec16f5eb9de664421c38cd4c824dc240cf
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2021
ms.locfileid: "98233844"
---
# <a name="usage-analysis-with-application-insights"></a>Analýzy používání pomocí nástroje Application Insights

Které funkce vaší webové nebo mobilní aplikace jsou nejoblíbenější? Dosáhnou vaši uživatelé své cíle s vaší aplikací? Vynechává se konkrétní body a později se vrátí?  [Azure Application Insights](./app-insights-overview.md) pomáhá získat výkonné přehledy o tom, jak uživatelé vaši aplikaci používají. Pokaždé, když aktualizujete aplikaci, můžete posoudit, jak dobře funguje pro uživatele. V tomto článku se seznámíte s tím, jak můžete provádět rozhodování o dalších cyklech vývoje na základě dat.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4Cijb]

## <a name="send-telemetry-from-your-app"></a>Poslat telemetrii z vaší aplikace

Nejlepší prostředí získáte instalací Application Insights jak v kódu aplikačního serveru, tak i na webových stránkách. Klientské a serverové součásti vaší aplikace odesílají telemetrii zpátky do Azure Portal k analýze.

1. **Serverový kód:** Nainstalujte příslušný modul pro [ASP.NET](./asp-net.md), [Azure](./app-insights-overview.md), [Java](./java-get-started.md), [Node.js](./nodejs.md)nebo [jinou](./platforms.md) aplikaci.

    * *Nechcete instalovat kód serveru? Stačí [vytvořit prostředek služby Azure Application Insights](./create-new-resource.md).*

2. **Kód webové stránky:** Před zavřením přidejte na svou webovou stránku následující skript ``</head>`` . Nahraďte klíč instrumentace příslušnou hodnotou pro váš Application Insights prostředek:
    
    ```html
    <script type="text/javascript">
    var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
    {
      instrumentationKey:"INSTRUMENTATION_KEY"
    }
    );window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
    </script>
    ```

    Další informace o pokročilejších konfiguracích pro monitorování webů najdete v [referenčním článku o sadě JavaScript SDK](./javascript.md).

3. **Kód mobilní aplikace:** Pomocí sady App Center SDK můžete shromažďovat události z vaší aplikace a pak tyto události poslat do Application Insights pro účely analýzy pomocí [této příručky](../learn/mobile-center-quickstart.md).

4. **Získat telemetrii:** Spusťte projekt v režimu ladění po dobu několika minut a potom vyhledejte výsledky v okně Přehled v Application Insights.

    Publikováním aplikace můžete monitorovat výkon vaší aplikace a zjistit, co uživatelé s vaší aplikací dělají.

## <a name="include-user-and-session-id-in-your-telemetry"></a>Zahrnutí ID uživatele a relace do telemetrie
Aby bylo možné sledovat uživatele v průběhu času, Application Insights vyžaduje způsob jejich identifikace. Jediným nástrojem pro použití, který nevyžaduje ID uživatele nebo ID relace, je nástroj Events.

Zahajte odesílání ID uživatelů a relací pomocí [tohoto procesu](./usage-send-user-context.md).

## <a name="explore-usage-demographics-and-statistics"></a>Prozkoumat demografické údaje o využití a statistiky
Zjistěte, kdy lidé používají vaši aplikaci, jaké stránky mají nejvíc zajímat, kde se nacházejí vaši uživatelé, jaké prohlížeče a operační systémy používají. 

Sestavy uživatelé a relace filtrují data podle stránek nebo vlastních událostí a segmentují je podle vlastností, jako je umístění, prostředí a stránka. Můžete také přidat vlastní filtry.

![Snímek obrazovky znázorňuje stránku s přehledem uživatelů pro fiktivní firmu.](./media/usage-overview/users.png)  

Přehledy o tom, jaké jsou zajímavé vzory v sadě dat, najdete na pravé straně.  

* Sestava **Uživatelé** vypočítává počet jedinečných uživatelů, kteří přistupují k vašim stránkám v rámci zvolených časových období. U webových aplikací se uživatelé počítají pomocí souborů cookie. Pokud někdo přistupuje k webu pomocí různých prohlížečů nebo klientských počítačů nebo vymaže své soubory cookie, bude se počítat více než jednou.
* Sestava **relace** spočítá počet uživatelských relací, které přistupují k vašemu webu. Relace je doba aktivity uživatele, která se ukončila po dobu nečinnosti delší než půl hodiny.

[Další informace o nástrojích uživatelé, relace a události](usage-segmentation.md)  

## <a name="retention---how-many-users-come-back"></a>Uchování – kolik uživatelů se vrátí?

Uchovávání vám pomůže pochopit, jak často se uživatelé budou vracet k používání své aplikace, na základě kohorty uživatelů, kteří provedli některé obchodní akce během určitého časového intervalu. 

- Informace o tom, jaké konkrétní funkce způsobí, že se uživatelé budou moci vrátit více než jiné 
- Forma hypotézy založená na datech reálného uživatele 
- Zjistěte, jestli je uchovávání problému v produktu. 

![Snímek obrazovky znázorňuje stránku s přehledem uchovávání informací, která zobrazuje informace o tom, jak často se uživatelé vracejí k používání jejich aplikace.](./media/usage-overview/retention.png) 

Ovládací prvky uchování v horní části umožňují definovat konkrétní události a časový rozsah pro výpočet uchovávání. Graf uprostřed poskytuje vizuální znázornění celkového procenta uchování v zadaném časovém rozsahu. Graf v dolní části představuje individuální uchování v daném časovém období. Tato úroveň podrobností vám umožní pochopit, co dělají vaši uživatelé a co může ovlivnit vracení uživatelů na podrobnější členitost.  

[Další informace o nástroji pro uchovávání informací](usage-retention.md)

## <a name="custom-business-events"></a>Vlastní obchodní události

Chcete-li získat jasné informace o tom, co uživatelé s vaší aplikací dělají, je vhodné vložit řádky kódu do protokolu pro vlastní události. Tyto události mohou sledovat cokoli od podrobných uživatelských akcí, jako je například kliknutí na určitá tlačítka, na důležitější obchodní události, jako je například vytvoření nákupu nebo získání hry.

K shromažďování vlastních událostí můžete také použít [modul plug-in pro automatické shromažďování a analýzu](javascript-click-analytics-plugin.md) .

I když v některých případech mohou zobrazení stránky představovat užitečné události, neplatí obecně. Uživatel může otevřít stránku produktu bez nákupu produktu. 

S konkrétními obchodními událostmi můžete pomocí webu sestavovat průběh vašich uživatelů. Můžete si najít své předvolby pro různé možnosti a místo jejich vyřazení nebo potíží. S tímto vědomím se můžete rozhodnout o prioritách v nevyřízených položkách vývoje.

Události se dají protokolovat ze strany klienta aplikace:

```JavaScript

    appInsights.trackEvent("ExpandDetailTab", {DetailTab: tabName});
```

Nebo na straně serveru:

```csharp
    var tc = new Microsoft.ApplicationInsights.TelemetryClient();
    tc.TrackEvent("CreatedAccount", new Dictionary<string,string> {"AccountType":account.Type}, null);
    ...
    tc.TrackEvent("AddedItemToCart", new Dictionary<string,string> {"Item":item.Name}, null);
    ...
    tc.TrackEvent("CompletedPurchase");
```

K těmto událostem můžete přiřadit hodnoty vlastností, abyste mohli události filtrovat nebo rozdělit při jejich kontrole na portálu. Kromě toho je ke každé události připojená standardní sada vlastností, jako je například anonymní ID uživatele, což vám umožňuje sledovat posloupnost aktivit jednotlivých uživatelů.

Přečtěte si další informace o [vlastních událostech](./api-custom-events-metrics.md#trackevent) a [vlastnostech](./api-custom-events-metrics.md#properties).

### <a name="slice-and-dice-events"></a>Události řezů a kostek

V nástrojích uživatelé, relace a události můžete rozřezat a indexovat vlastní události podle uživatele, názvu události a vlastností.
![Snímek obrazovky znázorňuje stránku s přehledem uživatelů pro fiktivní firmu.](./media/usage-overview/users.png)  
  
## <a name="design-the-telemetry-with-the-app"></a>Návrh telemetrie s aplikací

Když navrhujete jednotlivé funkce aplikace, zvažte, jak budete měřit úspěch s vašimi uživateli. Rozhodněte, jaké obchodní události potřebujete zaznamenat, a zaznamenejte volání sledování těchto událostí do vaší aplikace od začátku.

## <a name="a--b-testing"></a>A | B testování
Pokud si nejste jisti, kterou variantu funkce bude možné považovat za úspěšnou, vyžádejte si obě z nich, aby každý z nich měl přístup k různým uživatelům. Změřte úspěšnost jednotlivých a potom přejděte na sjednocenou verzi.

Pro tuto techniku připojíte jedinečné hodnoty vlastností ke všem telemetriem, které jsou odesílány každou verzí vaší aplikace. To lze provést definováním vlastností v aktivním TelemetryContext. Tyto výchozí vlastnosti jsou přidány do každé zprávy telemetrie, kterou aplikace odesílá – ne pouze vlastní zprávy, ale také standardní telemetrii.

Na portálu Application Insights vyfiltrujte a rozdělte data podle hodnot vlastností, aby bylo možné porovnat různé verze.

K tomu je potřeba [nastavit inicializátor telemetrie](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer):

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

V inicializátoru webové aplikace, jako je například Global.asax.cs:

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
> Přidání inicializátoru pomocí `ApplicationInsights.config` nebo použití `TelemetryConfiguration.Active` není pro ASP.NET Core aplikace platné. 

U [ASP.NET Corech](asp-net-core.md#adding-telemetryinitializers) aplikací je přidání nového `TelemetryInitializer` provedeno přidáním do kontejneru vkládání závislostí, jak je znázorněno níže. To se provádí v `ConfigureServices` metodě vaší `Startup.cs` třídy.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

Všechny nové TelemetryClients automaticky přidají hodnotu vlastnosti, kterou zadáte. Jednotlivé události telemetrie můžou přepsat výchozí hodnoty.

## <a name="next-steps"></a>Další kroky
   - [Uživatelé, relace, události](usage-segmentation.md)
   - [Trychtýře](usage-funnels.md)
   - [Uchovávání](usage-retention.md)
   - [Toky uživatelů](usage-flows.md)
   - [Workbooks](../platform/workbooks-overview.md)
   - [Přidat kontext uživatele](usage-send-user-context.md)

