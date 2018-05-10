---
title: Analýza využití pomocí služby Azure Application Insights | Microsoft docs
description: Porozumět uživatelům a co dělají s vaší aplikací.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 10/10/2017
ms.author: mbullwin
ms.openlocfilehash: 79f4f77f08653c40591137268b87dc2feeb0acf6
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="usage-analysis-with-application-insights"></a>Analýzy používání pomocí nástroje Application Insights

Funkce web nebo mobilní aplikace, které jsou nejčastěji používané? Vaši uživatelé dosáhli svých cílů s vaší aplikací? Jejich vyřadit na konkrétní body a vracejí později?  [Azure Application Insights](app-insights-overview.md) pomáhá získat efektivní přehled o tom, jak uživatelé používají vaši aplikaci. Při každé aktualizaci aplikace můžete vyhodnotit, jak dobře funguje pro uživatele. Replikace můžete provést rozhodnutí o další cyklech vývoj řízených daty.

## <a name="send-telemetry-from-your-app"></a>Odeslání telemetrie z vaší aplikace.

Dosažení co nejlepších výsledků je získat nainstalováním Application Insights v kódu aplikace serveru i na webových stránkách. Klientské a serverové součásti vaší aplikace odesílat telemetrii zpět na portál Azure pro analýzu.

1. **Serverový kód:** nainstalovat modul vhodné pro vaše [ASP.NET](app-insights-asp-net.md), [Azure](app-insights-azure.md), [Java](app-insights-java-get-started.md), [Node.js](app-insights-nodejs.md), nebo [jiné](app-insights-platforms.md) aplikace.

    * *Nechcete instalovat serverový kód? Právě [vytvořte prostředek Azure Application Insights](app-insights-create-new-resource.md).*

2. **Kód webové stránky:** otevřete [portál Azure](https://portal.azure.com), otevřete prostředek Application Insights pro vaši aplikaci a pak otevřete **Začínáme > sledování a diagnostikovat Client-Side**. 

    ![Zkopírujte skript do head vaší hlavní webové stránky.](./media/app-insights-usage-overview/02-monitor-web-page.png)

3. **Kód mobilní aplikace:** použijte sadu App SDK Center k shromažďování událostí z vaší aplikace a potom posílat kopie tyto události Application insights pro analýzu, [těchto pokynů](app-insights-mobile-center-quickstart.md).

4. **Získat telemetrie:** spusťte projekt v režimu ladění pro několik minut a potom vyhledejte výsledky v okně Přehled ve službě Application Insights.

    Publikování aplikace ke sledování výkonu vaší aplikace a zjistěte, co uživatelé dělají s vaší aplikací.

## <a name="include-user-and-session-id-in-your-telemetry"></a>Zahrnout ID uživatele a relace telemetrie
Sledování uživatelů v čase, Application Insights vyžaduje způsob, jak je identifikovat. Nástroj události je pouze využití nástroj, který nevyžaduje ID uživatele nebo ID relace.

Zahájit odesílání uživatele a pomocí ID relací [tento proces](https://docs.microsoft.com/azure/application-insights/app-insights-usage-send-user-context).

## <a name="explore-usage-demographics-and-statistics"></a>Prozkoumat využití demografické údaje a statistiky
Zjistí, pokud uživatelé používají vaši aplikaci, jaké stránky, budou se nejvíce zajímá, kde se nachází vaši uživatelé, jaké prohlížeče a operační systémy používají. 

Uživatele a relace sestavy filtr dat, stránky nebo vlastní události a je rozdělit pomocí vlastnosti, jako je například umístění prostředí a stránky. Můžete také přidat vlastní filtry.

![Uživatelé](./media/app-insights-usage-overview/users.png)  

Statistika na pravé straně bodu zajímavých vzorců v sadě dat.  

* **Uživatelé** sestava udává počet jedinečných uživatelů, kteří přístup k vaší stránky v rámci vašich vybrané časové období. Pro webové aplikace uživatelé počítají pomocí souborů cookie. Pokud někdo přistupuje k webu s jinou prohlížeče nebo klientské počítače nebo vymaže jejich souborů cookie, pak se bude počítat více než jednou.
* **Relací** sestava vrátí počet uživatelských relací, které přistupují k webu. Relace je období aktivity uživatelem, byl ukončen tečkou nečinnosti více než půl hodiny.

[Další informace o nástroje uživatelů, relací a události](app-insights-usage-segmentation.md)  

## <a name="page-views"></a>Zobrazení stránek

V okně využití klikněte na tlačítko prostřednictvím zobrazení stránky dlaždicí a získejte rozpis nejoblíbenější stránky:

![V okně Přehled klikněte na graf zobrazení stránky](./media/app-insights-usage-overview/05-games.png)

V předchozím příkladu je z webu hry. Z grafy okamžitě uvidíme:

* Využití nebyl vylepšené minulého týdne. Možná jsme měli zamyslet optimalizaci pro vyhledávací weby?
* Tenis je nejoblíbenější herní stránka. Umožňuje zaměřit se na další vylepšení na tuto stránku.
* Uživatelé v průměru, najdete na stránce tenis o třikrát za týden. (Existují další o třikrát relace než uživatelů.)
* Většina uživatelů přejděte na web během pracovní týden USA a v pracovní době. Možná jsme by měl poskytovat tlačítko "rychlý skrýt" na webové stránce.
* [Poznámky](app-insights-annotations.md) na graf zobrazit, pokud byly nasazeny nové verze webu. Žádná z poslední nasazení měl znatelný vliv na využití.

Co dělat, pokud chcete prozkoumat provoz na váš web podrobněji, jako je rozdělení pomocí vlastní vlastnosti, které váš web odesílá v jeho telemetrická zobrazení stránky?

1. Otevřete **události** nástroj v nabídce prostředku Application Insights. Tento nástroj umožňuje analyzovat, kolik zobrazení stránky a vlastních událostí, které byly odeslány z vaší aplikace na základě různých možností filtrování, cohorting a segmentace.
2. V rozevírací nabídce "Kdo používá" Vyberte "Any zobrazení stránky".
3. V rozevírací nabídce "Rozdělení podle" Vyberte vlastnosti, podle kterého rozdělit vaší telemetrická zobrazení stránky.

## <a name="retention---how-many-users-come-back"></a>Uchování – počet uživatelů, kteří se vrátit?

Uchování pomáhá pochopit, jak často se vaši uživatelé vrátit používat své aplikace založené na kohorty uživatele, kteří provést některé akce obchodní během určité časové období. 

- Pochopit, jaké konkrétní funkce způsobit, že uživatelům se zpět více než jiné 
- Hypotézy formulář na základě reálného uživatelských dat 
- Zjistěte, jestli uchování problém ve vašem produkt 

![Uchovávání](./media/app-insights-usage-overview/retention.png) 

Ovládací prvky uchovávání informací v horní části umožňují definovat konkrétních událostí a časový rozsah uchování vypočítat. Graf uprostřed nabízí vizuální znázornění uchování procentuální podle zadaný časový rozsah. Graf na dolní představuje jednotlivé uchování v daném časovém období. Tato úroveň podrobností můžete zjistit, co uživatelé dělají a co může mít vliv na stávajícím uživatelům na podrobnější členitosti.  

[Informace o nástroji pro uchování](app-insights-usage-retention.md)

## <a name="custom-business-events"></a>Vlastní obchodní události

Potřebujete vědět, co uživatelé dělají s vaší aplikací, je užitečné k vložení řádků kódu do protokolu vlastních událostí. Tyto události můžete sledovat nic akcím podrobné uživatel kliknutím na konkrétní tlačítka na větších obchodní události, jako je například nakupování nebo vítězství. 

I když v některých případech může představovat zobrazení stránky užitečné události, není true obecně. Uživatele můžete otevřít stránku produktu bez kdybyste kupovali produktu. 

S konkrétní obchodní události můžete grafu vaši uživatelé průběh svého webu. Můžete zjistit jejich předvoleb pro různé možnosti a tam, kde se vyřadit out nebo mít problémy. Replikace můžete provést informované rozhodnutí o priority v backlogu vývoj.

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

Hodnoty vlastností lze připojit k tyto události, takže můžete filtrovat nebo rozdělení událostí v případě, že si prohlédnout na portálu. Kromě toho je standardní sada vlastností připojený k každé události, jako je například ID anonymního uživatele, které umožňuje sledovat posloupnost aktivit jednotlivého uživatele.

Další informace o [vlastních událostí](app-insights-api-custom-events-metrics.md#trackevent) a [vlastnosti](app-insights-api-custom-events-metrics.md#properties).

### <a name="slice-and-dice-events"></a>Nařezání a rozčlenění události

V nástrojích pro uživatele, relace a události můžete řezu a rozčlenění vlastních událostí podle uživatele, název události a vlastnosti.
![Uživatelé](./media/app-insights-usage-overview/users.png)  
  
## <a name="design-the-telemetry-with-the-app"></a>Návrh telemetrie s aplikací

Při navrhování jednotlivých funkcí aplikace, zvažte, jak chcete měřit její úspěšnost s uživateli. Rozhodněte, jaké obchodní události, které je potřeba zaznamenat a kód sledování volání pro tyto události do vaší aplikace od začátku.

## <a name="a--b-testing"></a>A | Testování B
Pokud si nejste jisti, který variant funkce, která bude více úspěšné, uvolněte obou z nich, provádění jednotlivých přístupné pro různé uživatele. Měřit její úspěšnost. pro každou z a poté přesuňte jednotná verzi.

Pro tento postup můžete hodnot různých vlastností připojení k všechny telemetrická data, které odesílají každou verzi vaší aplikace. Můžete to udělat tak, že definují vlastnosti v active TelemetryContext. Tyto výchozí vlastnosti jsou přidány do každé telemetrie zprávu, která aplikace odešle – nejen vlastní zprávy, ale také standardní telemetrie.

V portálu služby Application Insights filtrovat a rozdělení dat na hodnoty vlastností, která porovnat různé verze.

K tomu, [nastavení inicializátoru telemetrie](app-insights-api-filtering-sampling.md##add-properties-itelemetryinitializer):

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

Ve webové aplikaci inicializátoru například Global.asax.cs:

```csharp

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
        .Add(new MyTelemetryInitializer());
    }
```

Všechny nové TelemetryClients automaticky přidat hodnotu vlastnosti, které zadáte. Jednotlivé telemetrické události můžete přepsat výchozí hodnoty.

## <a name="next-steps"></a>Další postup
   - [Uživatelé, relace, události](app-insights-usage-segmentation.md)
   - [Trychtýře](usage-funnels.md)
   - [Uchování](app-insights-usage-retention.md)
   - [Toky uživatele](app-insights-usage-flows.md)
   - [Workbooks](app-insights-usage-workbooks.md)
   - [Přidat uživatelský kontext](app-insights-usage-send-user-context.md)
