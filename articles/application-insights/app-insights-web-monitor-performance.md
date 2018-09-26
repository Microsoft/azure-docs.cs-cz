---
title: Monitorování stavu vaší aplikace a využití pomocí Application Insights
description: Začínáme s Application Insights. Analýza využití, dostupnosti a výkonu v místním nebo aplikace Microsoft Azure.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 40650472-e860-4c1b-a589-9956245df307
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 05/10/2018
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: 7e6aad6f6a0664d7834e6ea889dba14c1cbcdf0a
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47095357"
---
# <a name="monitor-performance-in-web-applications"></a>Sledování výkonu webových aplikací


Ujistěte se, že vaše aplikace je dobře a rychle zjistit o případných chybách. [Application Insights] [ start] popište všechny problémy s výkonem a výjimkami a umožňují najít a diagnostikovat hlavní příčiny.

Application Insights můžete monitorovat webové aplikace Java a ASP.NET a služby, služby WCF. Může být hostovaná místně, na virtuálních počítačích, nebo jako Microsoft Azure websites. 

Na straně klienta Application Insights můžete využít telemetrii z webových stránek a širokou škálu zařízení včetně iOS, Android a Windows Store apps.

## <a name="setup"></a>Nastavení sledování výkonu
Pokud jste ještě nepřidali Application Insights do projektu (tj. Pokud soubor ApplicationInsights.config nemá), vyberte jednu z těchto způsobů, jak začít:

* [Webové aplikace ASP.NET](app-insights-asp-net.md)
  * [Přidat monitorování výjimky](app-insights-asp-net-exceptions.md)
  * [Přidat monitorování závislostí](app-insights-monitor-performance-live-website-now.md)
* [Webových aplikací J2EE](app-insights-java-get-started.md)
  * [Přidat monitorování závislostí](app-insights-java-agent.md)

## <a name="view"></a>Zkoumání metrik výkonu
V [na webu Azure portal](https://portal.azure.com), přejděte do prostředku Application Insights, které jste nastavili pro vaše aplikace. Okno přehledu zobrazí data základního výkonu:

Kliknutím na libovolný graf zobrazíte další podrobnosti a zobrazit výsledky po delší dobu. Například, klikněte na dlaždici požadavků a pak vyberte časový rozsah:

![Proklikejte se k většímu množství dat a vyberte časový rozsah](./media/app-insights-web-monitor-performance/appinsights-48metrics.png)

Klikněte na graf. Chcete-li zvolit, které metriky se zobrazí, nebo přidejte nový graf a vyberte jeho metriky:

![Klikněte na graf zvolit metriky](./media/app-insights-web-monitor-performance/appinsights-61perfchoices.png)

> [!NOTE]
> **Zrušte zaškrtnutí všech metrik** zobrazíte úplný výběr, který je k dispozici. Metriky spadají do skupiny. Při výběru člena skupiny se zobrazí pouze ostatní členové této skupiny.

## <a name="metrics"></a>Jaké jsou všechny průměr? Dlaždice výkonu a sestavy
Existují různé metriky výkonu, které můžete získat. Začněme s těmi, které se ve výchozím nastavení v okně aplikace.

### <a name="requests"></a>Požadavky
Počet požadavků HTTP přijaté v zadaném období. Porovnat s výsledky na jiných sestav zobrazit, jak se vaše aplikace chová při zatížení se liší.

Požadavky HTTP zahrnout všechny požadavky GET nebo POST pro stránky, data a obrázky.

Kliknutím na dlaždici zobrazíte počet pro konkrétní adresy URL.

### <a name="average-response-time"></a>Průměrná doba odezvy
Měří čas mezi webovou žádost zadání vaší aplikace a vracené odpovědi.

Body zobrazit klouzavý průměr. Pokud existuje velký počet požadavků, můžou nastat jiným se liší od průměrné bez zřejmé ve špičce nebo ponořit v grafu.

Vyhledejte neobvyklé špičky. Obecně platí očekávané doby odezvy roste s nárůst požadavků. Pokud je vzestupu nesoustředil příliš velký, může vaše aplikace dosažení limitu prostředků, jako je například využití procesoru nebo kapacity, které používá služba.

Kliknutím na dlaždici zobrazíte časy pro konkrétní adresy URL.

![](./media/app-insights-web-monitor-performance/appinsights-42reqs.png)

### <a name="slowest-requests"></a>Nejpomalejší žádosti
![](./media/app-insights-web-monitor-performance/appinsights-44slowest.png)

Ukazuje, jaké požadavky může být třeba ladění výkonu.

### <a name="failed-requests"></a>Neúspěšné požadavky
![](./media/app-insights-web-monitor-performance/appinsights-46failed.png)

Počet požadavků, které vyvolala nezachycených výjimek.

Kliknutím na dlaždici zobrazíte podrobnosti o specifických chybách a vybrat jednotlivý požadavek zobrazíte její podrobnosti. 

Pouze reprezentativní vzorek selhání je zachován z důvodu jednotlivých kontroly.

### <a name="other-metrics"></a>Jiné metriky
Abyste zjistili, co nastavit jiné metriky můžete zobrazit, klikněte na graf a potom zrušte zaškrtnutí možnosti všechny metriky, abyste viděli plně k dispozici. Chcete-li zobrazit definici jednotlivé metriky klikněte na tlačítko (i).

![Zruší výběr všech metrik zobrazíte celé sady](./media/app-insights-web-monitor-performance/appinsights-62allchoices.png)

Výběrem libovolné metriky zakáže ostatní, které se nesmí objevit na stejném grafu.

## <a name="set-alerts"></a>Nastavení upozornění
Pokud chcete dostat e-mailu z neobvyklé hodnoty jakékoliv metriky, přidáte upozornění. Můžete zvolit buď k odeslání e-mailu pro účet správce nebo na konkrétní e-mailové adresy.

![](./media/app-insights-web-monitor-performance/appinsights-413setMetricAlert.png)

Nastavení prostředku před dalšími vlastnostmi. Neklikejte na tlačítko prostředky webového testu. Pokud chcete nastavit upozornění na metriky výkonu a využití.

Věnujte jednotky, ve kterých budete vyzváni k zadání prahovou hodnotu.

*Nevidím tlačítko Přidat oznámení.* – Je to skupina účet, ke kterým mají přístup jen pro čtení? Obraťte se na správce účtu.

## <a name="diagnosis"></a>Diagnostika problémů
Tady je pár tipů pro vyhledání a Diagnostika problémů s výkonem:

* Nastavit [webové testy] [ availability] se mají generovat výstrahy, pokud vaše webová stránka padá nebo reaguje pomalu nebo nesprávně. 
* Porovnejte počet požadavků s ostatními metrikami a zjistěte, jestli selhání nebo pomalé odezvy se vztahují k načtení.
* [Vložit a hledání trasování příkazy] [ diagnostic] v kódu k pomoci přesně určit problémy.
* Monitorování webové aplikace v operaci s [Live Metrics Stream][livestream].
* Zaznamenání stavu aplikace .net s [Snapshot Debugger][snapshot].

## <a name="find-and-fix-performance-bottlenecks-with-performance-investigation-experience"></a>Najít a opravit kritická místa výkonu pomocí možnosti pro zkoumání výkonu

Možnosti pro zkoumání výkonu můžete použít ke kontrole pomalé provádění operací ve vaší webové aplikaci. Můžete rychle vybrat konkrétní pomalých operací a použít [Profiler](app-insights-profiler.md) do kořenového adresáře způsobit pomalých operací na kód. Pomocí rozdělení novou dobu trvání pro vybranou operaci zobrazit, že můžete rychle na první pohled vyhodnotit jak chybný výsledné prostředí funguje pro vaše zákazníky. Můžete zobrazit, kolik vaše interakce uživatele to mělo dopad na jednotlivých pomalých operací. V následujícím příkladu jsme jste se rozhodli podrobněji podíváme na prostředí pro operaci GET Customers/Details. V distribuci doby trvání vidíme, že existují tři špičky. Úplně vlevo zásobníku je přibližně 400 ms a představuje interaktivní prostředí. Střední zásobníku je kolem 1.2 s a představuje průměrné prostředí. Nakonec v 3.6 s máme jiný malé zásobníku, který představuje 99. percentilu prostředí, které by mohly způsobit, že naše zákazníky, ponechte nespokojen(a). Prostředí je desetkrát pomalejší než skvělé prostředí pro stejnou operaci. 

![GET Customers/Details tři doba trvání špičky](./media/app-insights-web-monitor-performance/PerformanceTriageViewZoomedDistribution.png)

Chcete-li získat lepší představu o uživatelské prostředí pro tuto operaci, můžeme vybrat větší časový rozsah. Můžeme pak také zúžit v čase na konkrétní časový interval, ve kterém bylo pomalé v sítích operaci. V následujícím příkladu jsme přešli jsme od výchozí 24 hodin na 7 dní časový rozsah časový rozsah a pak zvětšili 9:47 až 12:47 časový interval mezi Út 12. a středa 13. Distribuce doby trvání a počtu vzorku a profiler trasování se aktualizovaly na pravé straně.

![GET Customers/Details tři doba trvání špičky za 7 dní v rozsahu s časovým intervalem](./media/app-insights-web-monitor-performance/PerformanceTriageView7DaysZoomedTrend.png)

Chcete-li zúžit v pomalé zkušenosti, jsme dále přiblížit doby trvání, které spadají do rozsahu mezi 95 a 99. percentilu. Tyto představují % 4, které byly pomalé interakcí s uživateli.

![GET Customers/Details tři doba trvání špičky za 7 dní v rozsahu s časovým intervalem](./media/app-insights-web-monitor-performance/PerformanceTriageView7DaysZoomedTrendZoomed95th99th.png)

Můžeme teď buď podívat na ukázky, kliknutím na tlačítko ukázky nebo v trasování reprezentativní profileru po kliknutí na tlačítko trasování Profiler. V tomto příkladu jsou čtyři trasování, které byly shromážděny pro GET Customers/Details ve doba trvání okna a oblasti zájmu.

Někdy nebude mít problém ve vašem kódu, ale místo toho v závislosti kód volá. Můžete přepnout na kartu závislosti v zobrazení pro posouzení výkonu prozkoumat tyto závislosti pomalé. Ve výchozím nastavení zobrazení výkonu je populární průměry, ale co vlastně chcete podívat na 95. percentil (nebo 99th, v případě, že monitorujete až po zralé služby). V následujícím příkladu jsme se zaměřili na pomalá závislost objektů BLOB v Azure, kde voláme PUT fabrikamaccount. Dobré prostředí clusteru přibližně 40 ms, pomalá volání na stejný závislost jsou třikrát pomalejší, clustering přibližně 120 ms. Nepřijímá mnohé z těchto volání to nasčítá způsobit příslušné operace výrazně zpomalit. Můžete zobrazit další podrobnosti reprezentativní vzorky a trasování profileru, stejně jako s kartou operace.

![GET Customers/Details tři doba trvání špičky za 7 dní v rozsahu s časovým intervalem](./media/app-insights-web-monitor-performance/SlowDependencies95thTrend.png)

Možnosti pro zkoumání výkonu ukazuje sadu ukázek, které jste se rozhodli soustředit se na relevantní přehledy po straně. Přepnout na 30 dnů časový rozsah a pak vyberte celkové zobrazte analytická ve všech operacích za poslední měsíc je nejlepší způsob, jak zobrazit všechny dostupné informace.

![GET Customers/Details tři doba trvání špičky za 7 dní v rozsahu s časovým intervalem](./media/app-insights-web-monitor-performance/Performance30DayOveralllnsights.png)


## <a name="next"></a>Další kroky
[Webové testy] [ availability] -mít webové požadavky odeslané do vaší aplikace v pravidelných intervalech z celého světa.

[Zachycování a hledání trasování diagnostiky] [ diagnostic] – vložit volání trasování a probrat se výsledky pro identifikaci problémů.

[Sledování využití] [ usage] – zjistěte, jak ostatní používají vaše aplikace.

[Řešení potíží s] [ qna] – a Q & A



<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-asp-net.md
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md
[usage]: app-insights-web-track-usage.md
[livestream]: app-insights-live-stream.md
[snapshot]: app-insights-snapshot-debugger.md



