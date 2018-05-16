---
title: Monitorovat stav a využití pomocí Application Insights vaší aplikace
description: Začínáme s Application Insights. Analýza využití, dostupnosti a výkonu místního nebo aplikace Microsoft Azure.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 40650472-e860-4c1b-a589-9956245df307
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: sdash
ms.openlocfilehash: 02421492528e44ed6a913443a7793235170d4881
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
---
# <a name="monitor-performance-in-web-applications"></a>Sledování výkonu webových aplikací


Ujistěte se, že aplikace je výkon na dobré a rychle zjistěte informace o případných selhání. [Application Insights] [ start] bude vás informovat o žádné problémy s výkonem a výjimkami a můžete najít a diagnostikovat základní příčiny.

Application Insights můžete monitorovat webové aplikace Java a ASP.NET a služby, služby WCF. Mohou být hostovaná místně, virtuálními počítači, nebo jako weby Microsoft Azure. 

Na straně klienta může trvat Application Insights telemetrie z webových stránek a širokou škálu zařízení se systémy iOS, Android a aplikací pro Windows Store.

## <a name="setup"></a>Nastavení monitorování výkonu
Pokud jste ještě nepřidali Application Insights do projektu (to znamená, pokud nemá soubor ApplicationInsights.config), vyberte jednu z těchto způsobů, jak začít:

* [Webové aplikace ASP.NET](app-insights-asp-net.md)
  * [Přidat monitorování výjimek](app-insights-asp-net-exceptions.md)
  * [Přidat monitorování závislostí](app-insights-monitor-performance-live-website-now.md)
* [Webových aplikací J2EE](app-insights-java-get-started.md)
  * [Přidat monitorování závislostí](app-insights-java-agent.md)

## <a name="view"></a>Zkoumání metriky výkonu
V [portálu Azure](https://portal.azure.com), procházejte do zdroje Application Insights, které jste nastavili pro vaši aplikaci. V okně Přehled zobrazuje data výkonu základní:

Klikněte na libovolný graf zobrazíte další podrobnosti a zobrazit výsledky delší dobu. Například klikněte na dlaždici požadavků a pak vyberte časové rozmezí:

![Proklikejte se k více dat a vyberte časový rozsah](./media/app-insights-web-monitor-performance/appinsights-48metrics.png)

Klikněte na graf vybrat metriky, které se zobrazí, nebo přidejte nový graf a vyberte jeho metriky:

![Klikněte na graf vybrat metriky](./media/app-insights-web-monitor-performance/appinsights-61perfchoices.png)

> [!NOTE]
> **Zrušte zaškrtnutí políčka všechny metriky** zobrazíte úplné výběru, která je k dispozici. Metriky lze rozdělit do skupin; Pokud je vybraná kteréhokoli člena skupiny, zobrazí se pouze ostatní členové této skupiny.

## <a name="metrics"></a>Jaké jsou všechny střední? Dlaždice výkonu a sestavy
Existují různé metriky výkonu, které můžete získat. Začněme s těmi, které se ve výchozím nastavení v okně aplikace.

### <a name="requests"></a>Požadavky
Počet požadavků HTTP přijaté v zadaném období. Výsledky porovnejte s výsledky v jiných sestavách zobrazíte chování vaší aplikace jako zatížení se liší.

Požadavky HTTP zahrnují všechny požadavky GET nebo POST pro stránky, data a bitové kopie.

Klikněte na dlaždici získat počty pro konkrétní adresy URL.

### <a name="average-response-time"></a>Průměrná doba odezvy
Měří čas mezi zadání vaší aplikace a odpovědi nevrátila webového požadavku.

Body zobrazit klouzavý průměr. Pokud existují mnoho požadavků, mohou existovat některé, který odchylují od průměr bez zřejmé ve špičce nebo ponořit v grafu.

Podívejte se na neobvyklé vrcholů. Obecně platí očekávané doby odezvy roste s nárůstem požadavky. Pokud zvýšení nesoustředil příliš velký, může aplikace nedosáhli limitu prostředků, například CPU, kapacitu služby, kterou používá.

Klikněte na dlaždici získat časy pro konkrétní adresy URL.

![](./media/app-insights-web-monitor-performance/appinsights-42reqs.png)

### <a name="slowest-requests"></a>Nejpomalejší žádosti
![](./media/app-insights-web-monitor-performance/appinsights-44slowest.png)

Zobrazuje, které požadavky může být nutné optimalizace výkonu.

### <a name="failed-requests"></a>Neúspěšné požadavky
![](./media/app-insights-web-monitor-performance/appinsights-46failed.png)

Počet požadavků, které vyvolala nezachycená výjimek.

Klikněte na dlaždici pro podrobné informace o specifických chybách a vyberte individuální žádosti zobrazíte její podrobnosti. 

Pro jednotlivé kontroly se uchovávají pouze reprezentativní vzorek chyb.

### <a name="other-metrics"></a>Další metriky
Pokud chcete zjistit, co nastavit další metriky můžete zobrazit, klikněte na graf a poté zrušte všechny metriky zobrazíte kompletní k dispozici. Viz definice jednotlivé metriky kliknutím (i).

![Zrušte výběr všechny metriky zobrazíte celé sady](./media/app-insights-web-monitor-performance/appinsights-62allchoices.png)

Výběr všech metrika zakáže ostatní, které se nesmí objevit na stejném grafu.

## <a name="set-alerts"></a>Nastavení upozornění
Mají být informování tímto e-mailu neobvyklou hodnot všechny metriky, přidáte oznámení. Můžete buď k odeslání e-mailu pro účet správce, nebo na konkrétní e-mailové adresy.

![](./media/app-insights-web-monitor-performance/appinsights-413setMetricAlert.png)

Nastavte prostředků než ostatní vlastnosti. Nemáte zvolte zdroje, testu webu, pokud chcete nastavit výstrahy na metriky výkonu a využití.

Pečlivě si uvědomit a jednotky, ve kterých budete vyzváni k zadání prahovou hodnotu.

*Tlačítko Přidat oznámení se nezobrazí.* -Je tato skupina účet, ke které máte oprávnění jen pro čtení? Obraťte se na správce účtu.

## <a name="diagnosis"></a>Diagnostika problémů
Zde je několik tipů pro hledání a diagnostice problémů s výkonem.

* Nastavit [webové testy] [ availability] výstrahy, pokud váš web z umístění ocitne mimo provoz nebo reaguje pomalu nebo nesprávně. 
* Porovnejte počtu žádostí o s další metriky pro případ selhání nebo pomalé odezvy se vztahují k načtení.
* [Vložení a vyhledávací příkazy trasování] [ diagnostic] ve svém kódu pro pomoci přesně určit problémy.
* Monitorování webové aplikace v operaci s [živý datový proud metriky][livestream].
* Zaznamenat stav aplikace .net s [ladicí program snímku][snapshot].

## <a name="find-and-fix-performance-bottlenecks-with-performance-investigation-experience"></a>Najít a opravit kritická místa výkonu s výkon šetření

Výkon šetření můžete použít ke kontrole pomalé provádění operací ve vaší webové aplikaci. Můžete rychle vybrat konkrétní pomalé operaci a použijte [profileru](app-insights-profiler.md) kořenovou způsobit pomalé operace dolů kódu. Pomocí rozdělení nové trvání zobrazuje pro vybrané operaci, že můžete rychle na první pohled vyhodnotit jak chybný možnosti je pro vaše zákazníky. Můžete zobrazit, kolik vaše uživatelské interakce měla dopad pro každou operaci pomalé. V následujícím příkladu jsme jste se rozhodli trvat bližší pohled na prostředí pro operaci získat zákazníků nebo podrobnosti. V distribuci trvání můžete vidíte, že existují tři špičky. Krajní levé Špička je přibližně 400 ms a představuje skvělý přizpůsobivý prostředí. Střední Špička je kolem 1.2 s a představuje průměrné prostředí. Nakonec v 3.6 s máme jiné malé Špička představující 99th percentilu prostředí, které by mohly způsobit, že naše zákazníky, ponechte spokojeni. Tuto činnost je desetkrát nižší než optimálního uživatelského prostředí pro stejnou operaci. 

![GET zákazníků nebo podrobnosti tři trvání špičky](./media/app-insights-web-monitor-performance/PerformanceTriageViewZoomedDistribution.png)

Chcete-li získat lepší představu o činnosti koncových uživatelů pro tuto operaci, jsme vyberte větší časový rozsah. Jsme můžete pak také zúžit v čase na konkrétní časové okno, kde bylo pomalé operaci. V následujícím příkladu jsme jste přešli z výchozího 24 hodin časový rozsah a za 7 dnů čas rozsah a pak do 9:47 až 12:47 časový interval mezi Út 12 a st 13. možnosti. Doba trvání distribuce i počet ukázka a profileru trasování byly aktualizovány na pravé straně.

![ZÍSKÁTE zákazníků nebo podrobnosti tři špičky doba trvání za 7 dní v rozsahu s časovým oknem](./media/app-insights-web-monitor-performance/PerformanceTriageView7DaysZoomedTrend.png)

Chcete-li zúžit v pomalé zkušenosti, jsme vedle zvětšení doby trvání, které spadají mezi 95th a 99th percentil. Tyto představují % 4 interakce uživatele, které byly pomalé.

![ZÍSKÁTE zákazníků nebo podrobnosti tři špičky doba trvání za 7 dní v rozsahu s časovým oknem](./media/app-insights-web-monitor-performance/PerformanceTriageView7DaysZoomedTrendZoomed95th99th.png)

Můžeme nyní buď podívejte se na ukázky, kliknutím na tlačítko ukázky, nebo na reprezentativní profileru trasování, kliknutím na tlačítko profileru trasování. V tomto příkladu jsou čtyři trasování, které byly shromážděny pro získání zákazníků nebo podrobnosti v doba trvání okna a rozsah zájmu.

Někdy se tento problém nebude v kódu, ale místo v závislost volá vašeho kódu. Můžete přepnout na kartu závislosti v zobrazení výkonu třídění k prozkoumání takové pomalé závislosti. Ve výchozím nastavení je zobrazení výkonu trendů průměry, ale co chcete prohlédnout si je 95. percentil (nebo 99th, v případě, že monitorujete vyspělá služby). V následujícím příkladu jsme se zaměřili na pomalé závislost objektů BLOB v Azure, kde voláme PUT fabrikamaccount. Dobré vyskytne clusteru přibližně 40 ms, pomalého volání stejné závislost jsou třikrát pomalejší, clustering okolo 120 ms. Neberou mnohé z těchto volání dohromady způsobit, že se příslušné operace výrazně zpomalit. Můžete rozbalit reprezentativní ukázky a profileru trasování, podobně jako s kartu operace.

![ZÍSKÁTE zákazníků nebo podrobnosti tři špičky doba trvání za 7 dní v rozsahu s časovým oknem](./media/app-insights-web-monitor-performance/SlowDependencies95thTrend.png)

Výkon šetření zobrazuje důležité statistiky na straně ukázka sadu, do které jste se rozhodli zaměřit se na. Nejlepší způsob, jak zobrazit všechny dostupné statistiky je přepnout časové rozmezí 30 dnů a pak vyberte celkovým zobrazíte přehled mezi všechny operace pro poslední měsíc.

![ZÍSKÁTE zákazníků nebo podrobnosti tři špičky doba trvání za 7 dní v rozsahu s časovým oknem](./media/app-insights-web-monitor-performance/Performance30DayOveralllnsights.png)


## <a name="next"></a>Další kroky
[Webové testy] [ availability] -mít webové požadavky odeslané do vaší aplikace v pravidelných intervalech z po celém světě.

[Zaznamenání a hledání diagnostické trasování] [ diagnostic] – vložení trasovacího volání a analyzovat výsledky ke kotvícímu bodu problémy.

[Sledování využití] [ usage] – zjistěte, jak lidé používat vaši aplikaci.

[Řešení potíží s] [ qna] - a otázky A odpovědi



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



