---
title: Analýzy používání pomocí Azure Application Insights | Dokumentace Microsoftu
description: Pochopíte uživatele a co dělají s vaší aplikací.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: conceptual
ms.date: 10/10/2017
ms.author: mbullwin
ms.openlocfilehash: 1217083980355c69fa51d7a6983c71295684f5b1
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35643529"
---
# <a name="usage-analysis-with-application-insights"></a>Analýzy používání pomocí nástroje Application Insights

Které funkce vašich webových nebo mobilních aplikací jsou nejoblíbenější? Vaši uživatelé dosáhli svých cílů s vaší aplikací? Odešli na konkrétní body a vrátit později?  [Azure Application Insights](app-insights-overview.md) umožňuje okamžitý přehled o tom, jak ostatní využívají vaše aplikace. Při každé aktualizaci aplikace můžete vyhodnotit, jak dobře funguje pro uživatele. Tyto znalosti dokážete na základě rozhodnutí o další vývojové cykly dat.

## <a name="send-telemetry-from-your-app"></a>Odesílání telemetrie z vaší aplikace

Dosažení co nejlepších výsledků je získat nainstalováním služby Application Insights v kódu serveru vaší aplikace a na webových stránkách. Klientské a serverové komponenty vaší aplikace odesílat telemetrii zpět na webu Azure portal pro analýzu.

1. **Kód serveru:** nainstalovat modul vhodné pro vaše [ASP.NET](app-insights-asp-net.md), [Azure](app-insights-azure.md), [Java](app-insights-java-get-started.md), [Node.js](app-insights-nodejs.md), nebo [jiných](app-insights-platforms.md) aplikace.

    * *Nechcete, aby instalace serveru kód? Právě [vytvoří prostředek služby Azure Application Insights](app-insights-create-new-resource.md).*

2. **Kódu webové stránky:** otevřít [webu Azure portal](https://portal.azure.com), otevřete prostředek Application Insights pro vaši aplikaci a pak otevřete **Začínáme > monitorování a Diagnostika Client-Side**. 

    ![Zkopírujte skript do hlavní webové stránky předlohy.](./media/app-insights-usage-overview/02-monitor-web-page.png)

3. **Kód mobilní aplikace:** App Center SDK umožňuje shromažďovat události z vaší aplikace a pak posílat kopie těchto událostí do služby Application Insights pro analýzu, [podle těchto pokynů](app-insights-mobile-center-quickstart.md).

4. **Získání telemetrie:** spuštění projektu v režimu ladění na pár minut a zkuste najít výsledky v okně Přehled v Application Insights.

    Publikování aplikace pro monitorování výkonu vaší aplikace a zjistěte, jak uživatelé pracují s vaší aplikací.

## <a name="include-user-and-session-id-in-your-telemetry"></a>Zahrnout do vaší telemetrie ID uživatelů a relací
Sledování uživatelů v čase, Application Insights vyžaduje způsob, jak jejich identifikaci. Nástroj událostí je pouze použití nástroj, který nevyžaduje ID uživatele nebo ID relace.

Zahájit odesílání uživatele a aby se ID relací pomocí [tento proces](https://docs.microsoft.com/azure/application-insights/app-insights-usage-send-user-context).

## <a name="explore-usage-demographics-and-statistics"></a>Prozkoumejte demografických využití a statistiky
Přečtěte si, když uživatelé používají vaše aplikace, které stránky se nejvíce zajímají, kde se nachází vaši uživatelé, jaké prohlížeče a operační systémy používají. 

Sestavy uživatelů a relací filtrování dat podle stránky nebo vlastní události a segmentovat podle vlastnosti, jako je umístění, prostředí a stránky. Můžete také přidat vlastní filtry.

![Uživatelé](./media/app-insights-usage-overview/users.png)  

Přehledy na pravé straně bodu zajímavých vzorců v sadě dat.  

* **Uživatelé** sestavy se počítá počet jedinečných uživatelů, kteří přístup ke stránkám v rámci zvolenou časová období. Pro web apps uživatelé se počítají pomocí souborů cookie. Pokud někdo získá přístup k webu s různými prohlížeči nebo klientské počítače nebo vymaže jejich souborů cookie, pak se bude počítat více než jednou.
* **Relace** sestava vrátí počet uživatelských relací, které přistupují k webu. Relace je období aktivity podle uživatele, ukončeny určité době nečinnosti více než půl hodiny.

[Informace o nástrojích uživatelé, relace a události](app-insights-usage-segmentation.md)  

## <a name="page-views"></a>Zobrazení stránek

V okně využití proklikejte dlaždici zobrazení stránek a získat rozpis nejoblíbenějších stránek:

![V okně Přehled klikněte na graf zobrazení stránky](./media/app-insights-usage-overview/05-games.png)

Výše uvedený příklad je z webu hry. Z grafů okamžitě vidět:

* V minulém týdnu ještě lepší využití. Možná by přistupujeme k optimalizace pro vyhledávací weby?
* Tenis je nejoblíbenější her. Zaměřme se na další vylepšení na tuto stránku.
* Uživatelé v průměru, najdete na stránce tenis přibližně tři časy za týden. (Existují další informace o třikrát relace než uživatelé).
* Většina uživatelů přejděte na web v průběhu pracovního týdne USA a v pracovní době. Možná by měl poskytujeme "rychlé skrýt" tlačítko na webové stránce.
* [Poznámky](app-insights-annotations.md) v grafu zobrazit, když se nasadily nových verzí na webu. Žádná z poslední nasazení měli znatelný vliv na využití.

Co když chcete prozkoumat podrobněji, jako je rozdělení vlastní vlastnost, která odešle svůj web v jeho telemetrie zobrazení stránky provoz?

1. Otevřít **události** nástroj v nabídce prostředků Application Insights. Tento nástroj umožňuje analyzovat počet zobrazení stránek a vlastní události, které byly odeslány z vaší aplikace založené na širokou škálu možností filtrování, cohorting a segmentace.
2. V rozevíracím seznamu "Kdo used" Vyberte "Any zobrazení stránky".
3. V rozevíracím seznamu "Rozděleno podle" Vyberte vlastnost, podle kterého chcete rozdělit vaší telemetrie zobrazení stránky.

## <a name="retention---how-many-users-come-back"></a>Uchování – kolik uživatelů se vrátit?

Uchovávání dat pomáhá pochopit, jak často uživatelé vrací a používá své aplikace založené na kohorty uživatelů, které provedl některé obchodní během určité časovém intervalu. 

- Pochopit, jaké konkrétní funkce způsobit, že uživatelé přijít zpět více než jiné 
- Formulář hypotézy podle dat uživatelů 
- Určení, zda je uchovávání problému v produktu 

![Uchovávání](./media/app-insights-usage-overview/retention.png) 

Uchování ovládacích prvků v horní části umožňují definovat konkrétní události a časový rozsah pro výpočet uchovávání informací. Grafu uprostřed nabízí vizuální znázornění do celkové procentní hodnoty uchovávání informací o zadaný časový rozsah. V dolní části grafu představuje jednotlivé uchování v daném časovém období. Tato úroveň podrobností můžete pochopit, co uživatelé dělají a co by mohly ovlivnit stávajícím uživatelům na podrobnější členitosti.  

[Informace o nástroji pro uchovávání dat](app-insights-usage-retention.md)

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

Další informace o [vlastních událostí](app-insights-api-custom-events-metrics.md#trackevent) a [vlastnosti](app-insights-api-custom-events-metrics.md#properties).

### <a name="slice-and-dice-events"></a>Nařezání a rozčlenění události

V nástrojích uživatelé, relace a události můžete rozdělit a můžete detailně rozebrat vlastních událostí podle uživatele, název události a vlastnosti.
![Uživatelé](./media/app-insights-usage-overview/users.png)  
  
## <a name="design-the-telemetry-with-the-app"></a>Návrh telemetrie v aplikaci

Při návrhu jednotlivé funkce vaší aplikace, zvažte, jak budete měřit její úspěšnost s uživateli. Rozhodněte, jaké obchodní události, je potřeba zaznamenat a kód pro sledování volání pro tyto události do vaší aplikace od začátku.

## <a name="a--b-testing"></a>A | Testování B
Pokud si nejste jisti, které varianta funkce bude úspěšnější, uvolněte jejich provádění jednotlivých přístupné pro jiné uživatele. Měření úspěchu jednotlivých a poté přesuňte do jednotné verze.

Pro tuto techniku připojíte hodnot různých vlastností veškerá telemetrická data, která odesílá každé verze vaší aplikace. Uděláte to tak, že definujete vlastnosti v aktivní TelemetryContext. Tyto výchozí vlastnosti se přidají do každé telemetrické zprávy, které aplikace odesílá – nejen vlastní zprávy, ale také standardní telemetrická data.

Na portálu Application Insights filtrovat a rozdělení dat na hodnoty vlastností, aby porovnejte různé verze.

K tomu [nastavit inicializátor telemetrie](app-insights-api-filtering-sampling.md##add-properties-itelemetryinitializer):

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
   - [Uživatelé, relace, události](app-insights-usage-segmentation.md)
   - [Trychtýře](usage-funnels.md)
   - [Uchování](app-insights-usage-retention.md)
   - [Toky uživatele](app-insights-usage-flows.md)
   - [Workbooks](app-insights-usage-workbooks.md)
   - [Přidat kontext uživatele](app-insights-usage-send-user-context.md)
