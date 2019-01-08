---
title: Analýzy používání pomocí Azure Application Insights | Dokumentace Microsoftu
description: Pochopíte uživatele a co dělají s vaší aplikací.
services: application-insights
documentationcenter: ''
author: NumberByColors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/10/2017
ms.pm_owner: daviste;NumberByColors
ms.reviewer: mbullwin
ms.author: daviste
ms.openlocfilehash: 6d8570c78fcf28a236374579a9cb85c7c27f6ec8
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54076973"
---
# <a name="usage-analysis-with-application-insights"></a>Analýzy používání pomocí nástroje Application Insights

Které funkce vašich webových nebo mobilních aplikací jsou nejoblíbenější? Vaši uživatelé dosáhli svých cílů s vaší aplikací? Odešli na konkrétní body a vrátit později?  [Azure Application Insights](../../application-insights/app-insights-overview.md) umožňuje okamžitý přehled o tom, jak ostatní využívají vaše aplikace. Při každé aktualizaci aplikace můžete vyhodnotit, jak dobře funguje pro uživatele. Tyto znalosti dokážete na základě rozhodnutí o další vývojové cykly dat.

## <a name="send-telemetry-from-your-app"></a>Odesílání telemetrie z vaší aplikace

Dosažení co nejlepších výsledků je získat nainstalováním služby Application Insights v kódu serveru vaší aplikace a na webových stránkách. Klientské a serverové komponenty vaší aplikace odesílat telemetrii zpět na webu Azure portal pro analýzu.

1. **Kód serveru:** Nainstalujte příslušný modul pro vaše [ASP.NET](../../azure-monitor/app/asp-net.md), [Azure](../../application-insights/app-insights-overview.md), [Java](../../azure-monitor/app/java-get-started.md), [Node.js](../../azure-monitor/app/nodejs.md), nebo [jiných](../../azure-monitor/app/platforms.md) aplikace.

    * *Nechcete, aby instalace serveru kód? Právě [vytvoří prostředek služby Azure Application Insights](../../azure-monitor/app/create-new-resource.md ).*

2. **Kódu webové stránky:** Přidejte následující skript do své webové stránce před uzavírací značku ``</head>``. Nahraďte klíč instrumentace má hodnotu vhodnou pro váš prostředek Application Insights:

   ```javascript
      <script type="text/javascript">
        var appInsights=window.appInsights||function(a){
            function b(a){c[a]=function(){var b=arguments;c.queue.push(function(){c[a].apply(c,b)})}}var c={config:a},d=document,e=window;setTimeout(function(){var b=d.createElement("script");b.src=a.url||"https://az416426.vo.msecnd.net/scripts/a/ai.0.js",d.getElementsByTagName("script")[0].parentNode.appendChild(b)});try{c.cookie=d.cookie}catch(a){}c.queue=[];for(var f=["Event","Exception","Metric","PageView","Trace","Dependency"];f.length;)b("track"+f.pop());if(b("setAuthenticatedUserContext"),b("clearAuthenticatedUserContext"),b("startTrackEvent"),b("stopTrackEvent"),b("startTrackPage"),b("stopTrackPage"),b("flush"),!a.disableExceptionTracking){f="onerror",b("_"+f);var g=e[f];e[f]=function(a,b,d,e,h){var i=g&&g(a,b,d,e,h);return!0!==i&&c["_"+f](a,b,d,e,h),i}}return c
        }({
            instrumentationKey: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx"
        });
        
        window.appInsights=appInsights,appInsights.queue&&0===appInsights.queue.length&&appInsights.trackPageView();
    </script>
    ```
    Pokud se chcete dozvědět o pokročilejších konfiguracích monitorování webů, přečtěte si [referenční informace k rozhraní API sady JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md).

3. **Kód mobilní aplikace:** Použití sady SDK centra aplikací shromažďovat události z vaší aplikace a pak posílat kopie těchto událostí do služby Application Insights pro analýzu, [podle těchto pokynů](../../azure-monitor/learn/mobile-center-quickstart.md).

4. **Získání telemetrie:** Spuštění projektu v režimu ladění na pár minut a zkuste najít výsledky v okně Přehled v Application Insights.

    Publikování aplikace pro monitorování výkonu vaší aplikace a zjistěte, jak uživatelé pracují s vaší aplikací.

## <a name="include-user-and-session-id-in-your-telemetry"></a>Zahrnout do vaší telemetrie ID uživatelů a relací
Sledování uživatelů v čase, Application Insights vyžaduje způsob, jak jejich identifikaci. Nástroj událostí je pouze použití nástroj, který nevyžaduje ID uživatele nebo ID relace.

Zahájit odesílání uživatele a aby se ID relací pomocí [tento proces](https://docs.microsoft.com/azure/application-insights/app-insights-usage-send-user-context).

## <a name="explore-usage-demographics-and-statistics"></a>Prozkoumejte demografických využití a statistiky
Přečtěte si, když uživatelé používají vaše aplikace, které stránky se nejvíce zajímají, kde se nachází vaši uživatelé, jaké prohlížeče a operační systémy používají. 

Sestavy uživatelů a relací filtrování dat podle stránky nebo vlastní události a segmentovat podle vlastnosti, jako je umístění, prostředí a stránky. Můžete také přidat vlastní filtry.

![Uživatelé](./media/usage-overview/users.png)  

Přehledy na pravé straně bodu zajímavých vzorců v sadě dat.  

* **Uživatelé** sestavy se počítá počet jedinečných uživatelů, kteří přístup ke stránkám v rámci zvolenou časová období. Pro web apps uživatelé se počítají pomocí souborů cookie. Pokud někdo získá přístup k webu s různými prohlížeči nebo klientské počítače nebo vymaže jejich souborů cookie, pak se bude počítat více než jednou.
* **Relace** sestava vrátí počet uživatelských relací, které přistupují k webu. Relace je období aktivity podle uživatele, ukončeny určité době nečinnosti více než půl hodiny.

[Informace o nástrojích uživatelé, relace a události](usage-segmentation.md)  

## <a name="retention---how-many-users-come-back"></a>Uchování – kolik uživatelů se vrátit?

Uchovávání dat pomáhá pochopit, jak často uživatelé vrací a používá své aplikace založené na kohorty uživatelů, které provedl některé obchodní během určité časovém intervalu. 

- Pochopit, jaké konkrétní funkce způsobit, že uživatelé přijít zpět více než jiné 
- Formulář hypotézy podle dat uživatelů 
- Určení, zda je uchovávání problému v produktu 

![Uchovávání](./media/usage-overview/retention.png) 

Uchování ovládacích prvků v horní části umožňují definovat konkrétní události a časový rozsah pro výpočet uchovávání informací. Grafu uprostřed nabízí vizuální znázornění do celkové procentní hodnoty uchovávání informací o zadaný časový rozsah. V dolní části grafu představuje jednotlivé uchování v daném časovém období. Tato úroveň podrobností můžete pochopit, co uživatelé dělají a co by mohly ovlivnit stávajícím uživatelům na podrobnější členitosti.  

[Informace o nástroji pro uchovávání dat](usage-retention.md)

## <a name="custom-business-events"></a>Vlastní obchodní události

Abyste pochopili, co uživatelé dělají s vaší aplikací je vhodné vložit řádky kódu pro záznam vlastních událostí. Tyto události můžete sledovat cokoli podrobné uživatelské akce, jako je kliknutí na konkrétní tlačítka, významnější obchodní události, například nákupem nebo vítězství. 

I když v některých případech může představovat zobrazení stránek užitečné události, není true obecně. Uživatele můžete otevřít stránku produktu bez nutnosti kupovat produktu. 

S konkrétní obchodní události můžete graf průběh vaši uživatelé svého webu. Můžete zjistit jejich předvoleb pro různé možnosti a tam, kde se vyřadit out nebo mají potíže. S tyto znalosti můžete provádět informovaná rozhodnutí o priority v backlog vývoj.

Z aplikace na straně klienta může být protokolovány události:

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

Hodnoty vlastností lze připojit k tyto události, takže můžete filtrovat nebo události, když si prohlédnout na portálu rozdělit. Kromě toho standardní sadu vlastností je připojen k každé události, jako je ID anonymního uživatele, který umožňuje trasování posloupnost aktivit jednotlivého uživatele.

Další informace o [vlastních událostí](../../azure-monitor/app/api-custom-events-metrics.md#trackevent) a [vlastnosti](../../azure-monitor/app/api-custom-events-metrics.md#properties).

### <a name="slice-and-dice-events"></a>Nařezání a rozčlenění události

V nástrojích uživatelé, relace a události můžete rozdělit a můžete detailně rozebrat vlastních událostí podle uživatele, název události a vlastnosti.
![Uživatelé](./media/usage-overview/users.png)  
  
## <a name="design-the-telemetry-with-the-app"></a>Návrh telemetrie v aplikaci

Při návrhu jednotlivé funkce vaší aplikace, zvažte, jak budete měřit její úspěšnost s uživateli. Rozhodněte, jaké obchodní události, je potřeba zaznamenat a kód pro sledování volání pro tyto události do vaší aplikace od začátku.

## <a name="a--b-testing"></a>A | Testování B
Pokud si nejste jisti, které varianta funkce bude úspěšnější, uvolněte jejich provádění jednotlivých přístupné pro jiné uživatele. Měření úspěchu jednotlivých a poté přesuňte do jednotné verze.

Pro tuto techniku připojíte hodnot různých vlastností veškerá telemetrická data, která odesílá každé verze vaší aplikace. Uděláte to tak, že definujete vlastnosti v aktivní TelemetryContext. Tyto výchozí vlastnosti se přidají do každé telemetrické zprávy, které aplikace odesílá – nejen vlastní zprávy, ale také standardní telemetrická data.

Na portálu Application Insights filtrovat a rozdělení dat na hodnoty vlastností, aby porovnejte různé verze.

K tomu [nastavit inicializátor telemetrie](../../azure-monitor/app/api-filtering-sampling.md##add-properties-itelemetryinitializer):

```csharp


    // Telemetry initializer class
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize (ITelemetry telemetry)
        {
            telemetry.Properties["AppVersion"] = "v2.1";
        }
    }
```

V inicializátoru webové aplikace jako je například Global.asax.cs:

```csharp

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
        .Add(new MyTelemetryInitializer());
    }
```

Všechny nové TelemetryClients automaticky přidat hodnotu, kterou zadáte. Jednotlivé telemetrické události můžete přepsat výchozí hodnoty.

## <a name="next-steps"></a>Další postup
   - [Uživatelé, relace, události](usage-segmentation.md)
   - [Trychtýře](usage-funnels.md)
   - [Uchování](usage-retention.md)
   - [Toky uživatele](usage-flows.md)
   - [Workbooks](../../azure-monitor/app/usage-workbooks.md)
   - [Přidat kontext uživatele](usage-send-user-context.md)
