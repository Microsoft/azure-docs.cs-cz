---
title: Sledování stavu a využití aplikace pomocí application insights
description: Začínáme s Přehledy aplikací. Analyzujte využití, dostupnost a výkon místních aplikací nebo aplikací Microsoft Azure.
ms.topic: conceptual
ms.date: 05/10/2018
ms.reviewer: sdash
ms.openlocfilehash: 873fc41585c387246d83008a8f97d6c4d9a32c3b
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985061"
---
# <a name="monitor-performance-in-web-applications"></a>Sledování výkonu webových aplikací


Ujistěte se, že vaše aplikace funguje dobře, a rychle se dozvíte o všech selháních. [Application Insights][start] vám řekne o všech problémech s výkonem a výjimkách a pomůže vám najít a diagnostikovat hlavní příčiny.

Application Insights můžete sledovat java i ASP.NET webové aplikace a služby, WCF služby. Můžou být hostované místně, na virtuálních počítačích nebo jako weby Microsoft Azure. 

Na straně klienta application insights můžete trvat telemetrie z webových stránek a širokou škálu zařízení, včetně iOS, Android a Windows Store aplikace.

## <a name="set-up-performance-monitoring"></a><a name="setup"></a>Nastavení sledování výkonu
Pokud jste ještě do projektu nepřidali Application Insights (to znamená, že nemá ApplicationInsights.config), zvolte jeden z těchto způsobů, jak začít:

* [Webové aplikace v ASP.NET](../../azure-monitor/app/asp-net.md)
  * [Přidání monitorování výjimek](../../azure-monitor/app/asp-net-exceptions.md)
  * [Přidání monitorování závislostí](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Webové aplikace Java EE](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent)

## <a name="exploring-performance-metrics"></a><a name="view"></a>Zkoumání metrik výkonu
Na [webu Azure Portal](https://portal.azure.com)přejděte na prostředek Application Insights, který jste nastavili pro vaši aplikaci. Okno přehledu zobrazuje základní údaje o výkonu:

Kliknutím na libovolný graf zobrazíte další podrobnosti a zobrazíte výsledky po delší dobu. Klikněte například na dlaždici Požadavky a vyberte časový rozsah:

![Proklikejte se na další data a vyberte časový rozsah](./media/web-monitor-performance/appinsights-48metrics.png)

Kliknutím na graf zvolte, které metriky zobrazí, nebo přidejte nový graf a vyberte jeho metriky:

![Kliknutím na graf zvolte metriky.](./media/web-monitor-performance/appinsights-61perfchoices.png)

> [!NOTE]
> **Zrušením zaškrtnutí všech metrik** zobrazíte úplný výběr, který je k dispozici. Metriky spadají do skupin; Pokud je vybrán některý člen skupiny, zobrazí se pouze ostatní členové této skupiny.

## <a name="what-does-it-all-mean-performance-tiles-and-reports"></a><a name="metrics"></a>Co to všechno znamená? Dlaždice výkonu a sestavy
Existují různé metriky výkonu, které můžete získat. Začněme s těmi, které se ve výchozím nastavení zobrazují v okně aplikace.

### <a name="requests"></a>Žádosti
Počet požadavků HTTP přijatých v zadaném období. Porovnejte to s výsledky v jiných sestavách, abyste zjistili, jak se vaše aplikace chová, když se zatížení liší.

Požadavky HTTP zahrnují všechny požadavky GET nebo POST pro stránky, data a obrázky.

Kliknutím na dlaždici získáte počty konkrétních adres URL.

### <a name="average-response-time"></a>Průměrná doba odezvy
Měří dobu mezi webovým požadavkem, který vstupuje do aplikace, a vrácenou odpovědí.

Body ukazují klouzavý průměr. Pokud existuje mnoho požadavků, mohou existovat některé, které se odchylují od průměru bez zjevného vrcholu nebo ponoření do grafu.

Podívejte se na neobvyklé vrcholy. Obecně lze očekávat, že doba odezvy vzroste s nárůstem žádostí. Pokud je nárůst nepřiměřený, může vaše aplikace být bít limit prostředků, jako je procesor nebo kapacita služby, kterou používá.

Kliknutím na dlaždici získáte časy pro konkrétní adresy URL.

![](./media/web-monitor-performance/appinsights-42reqs.png)

### <a name="slowest-requests"></a>Nejpomalejší požadavky
![](./media/web-monitor-performance/appinsights-44slowest.png)

Zobrazuje, které požadavky mohou vyžadovat ladění výkonu.

### <a name="failed-requests"></a>Neúspěšné požadavky
![](./media/web-monitor-performance/appinsights-46failed.png)

Počet požadavků, které vyvolaly nezachycené výjimky.

Kliknutím na dlaždici zobrazíte podrobnosti o konkrétních selháních a vyberte jednotlivé požadavky, abyste viděli jeho podrobnosti. 

Pro individuální kontrolu je zachován pouze reprezentativní vzorek poruch.

### <a name="other-metrics"></a>Další metriky
Chcete-li zjistit, jaké další metriky můžete zobrazit, klikněte na graf a pak zrušte výběr všech metrik, abyste viděli úplnou dostupnou sadu. Kliknutím (i) zobrazíte definici jednotlivých metrik.

![Zrušením zaškrtnutí všech metrik zobrazíte celou sadu](./media/web-monitor-performance/appinsights-62allchoices.png)

Výběrem libovolné metriky zakážete ostatní, které se nemohou zobrazit ve stejném grafu.

## <a name="set-alerts"></a>Nastavení upozornění
Chcete-li být e-mailem upozorněni na neobvyklé hodnoty libovolné metriky, přidejte výstrahu. Můžete se rozhodnout, že e-mail odešlete správcům účtu, nebo na konkrétní e-mailové adresy.

![](./media/web-monitor-performance/appinsights-413setMetricAlert.png)

Nastavte prostředek před ostatními vlastnostmi. Nevybírejte prostředky webtest, pokud chcete nastavit výstrahy na metriky výkonu nebo využití.

Dávejte pozor, abyste si všimli jednotek, ve kterých jste vyzváni k zadání prahové hodnoty.

*Tlačítko Přidat upozornění se nezobrazí.* - Jedná se o skupinový účet, ke kterému máte přístup jen pro čtení? Obraťte se na správce účtu.

## <a name="diagnosing-issues"></a><a name="diagnosis"></a>Diagnostika problémů
Zde je několik tipů pro hledání a diagnostiku problémů s výkonem:

* Nastavte [webové testy,][availability] které budou upozorňovány, pokud váš web přejde dolů nebo reaguje nesprávně nebo pomalu. 
* Porovnejte počet požadavků s jinými metrikami a zjistěte, jestli selhání nebo pomalá odezva souvisejí s načtením.
* [Vložte a vyhledejte příkazy trasování][diagnostic] ve vašem kódu, které vám pomohou určit problémy.
* Sledujte svou webovou aplikaci v provozu pomocí [živého streamování metrik][livestream].
* Zachyťte stav aplikace .NET pomocí [ladicího programu snímků][snapshot].

## <a name="find-and-fix-performance-bottlenecks-with-performance-investigation-experience"></a>Vyhledání a oprava kritických bodů výkonu se zkušenostmi s vyšetřováním výkonu

Zkušenosti s vyšetřováním výkonu můžete použít ke kontrole operací s pomalým prováděním ve webové aplikaci. Můžete rychle vybrat konkrétní pomalé operace a pomocí [Profiler](../../azure-monitor/app/profiler.md) root způsobit pomalé operace dolů do kódu. Pomocí nové distribuce doby trvání zobrazené pro vybranou operaci můžete rychle na první pohled posoudit, jak špatné je prostředí pro vaše zákazníky. Můžete vidět, kolik interakcí uživatelů bylo ovlivněno pro každou pomalou operaci. V následujícím příkladu jsme se rozhodli podívat se blíže na prostředí pro operace GET Customers/Details. V rozložení doby trvání vidíme, že existují tři hroty. Špička nejvíce vlevo je kolem 400 ms a představuje skvělý zážitek z odezvy. Střední hrot je kolem 1,2 s a představuje průměrný zážitek. Konečně na 3,6 s máme další malý hrot, který představuje 99. Tato zkušenost je desetkrát pomalejší než skvělá zkušenost pro stejnou operaci. 

![GET Zákazníci/Podrobnosti tři doby trvání špičky](./media/web-monitor-performance/PerformanceTriageViewZoomedDistribution.png)

Chcete-li získat lepší představu o uživatelské prostředí pro tuto operaci, můžeme vybrat větší časový rozsah. Můžeme se pak také zúžit v čase na konkrétní časové okno, kde byla operace pomalá. V následujícím příkladu jsme přepnuli z výchozího časového rozsahu 24 hodin na časový rozsah 7 dnů a pak jsme se přiblížili do časového okna 9:47 do 12:47 mezi Út em 12. Rozdělení doby trvání a počet trasování vzorku a profileru byly aktualizovány na pravé straně.

![GET Zákazníci / Podrobnosti tři doba trvání špičky v rozsahu 7 dnů s časovým oknem](./media/web-monitor-performance/PerformanceTriageView7DaysZoomedTrend.png)

Chcete-li zúžit na pomalé zážitky, jsme další zoom do doby trvání, které spadají mezi 95th a 99th percentilu. Ty představují 4 % interakcí uživatelů, které byly pomalé.

![GET Zákazníci / Podrobnosti tři doba trvání špičky v rozsahu 7 dnů s časovým oknem](./media/web-monitor-performance/PerformanceTriageView7DaysZoomedTrendZoomed95th99th.png)

Nyní se můžeme podívat buď na reprezentativní vzorky, kliknutím na tlačítko Vzorky, nebo na reprezentativní profilovací stopy, kliknutím na tlačítko Profiler stopy. V tomto příkladu jsou čtyři trasování, které byly shromážděny pro get zákazníci/podrobnosti v časovém okně a rozsah trvání zájmu.

Někdy problém nebude ve vašem kódu, ale spíše v závislosti volání kódu. Můžete přepnout na kartu Závislosti v zobrazení třídění výkonu a prozkoumat tyto pomalé závislosti. Ve výchozím nastavení je zobrazení výkonu trendy průměry, ale to, co opravdu chcete podívat na je 95 percentil (nebo 99., v případě, že sledujete vyspělé služby). V následujícím příkladu jsme se zaměřili na pomalé závislost i blob Azure, kde voláme PUT fabrikamaccount. Dobré zkušenosti clusteru kolem 40 ms, zatímco pomalé volání na stejnou závislost jsou třikrát pomalejší, clustering kolem 120 ms. Netrvá mnoho z těchto volání přidat až způsobit příslušné operace výrazně zpomalit. Můžete přejít k podrobnostem reprezentativních vzorků a trasování profileru, stejně jako můžete pomocí karty Operace.

![GET Zákazníci / Podrobnosti tři doba trvání špičky v rozsahu 7 dnů s časovým oknem](./media/web-monitor-performance/SlowDependencies95thTrend.png)

Zkušenosti s vyšetřováním výkonu ukazují relevantní poznatky spolu se sadou vzorků, na kterou jste se rozhodli zaměřit. Nejlepší způsob, jak se podívat na všechny dostupné přehledy, je přepnout na časový rozsah 30 dnů a pak vybrat Celkově, chcete-li zobrazit přehledy napříč všemi operacemi za poslední měsíc.

![GET Zákazníci / Podrobnosti tři doba trvání špičky v rozsahu 7 dnů s časovým oknem](./media/web-monitor-performance/Performance30DayOveralllnsights.png)


## <a name="next-steps"></a><a name="next"></a>Další kroky
[Webové testy][availability] - Mají webové požadavky odeslané do vaší aplikace v pravidelných intervalech z celého světa.

[Zachytávání a vyhledávání diagnostických trasování][diagnostic] – vložte trasování volání a prosít výsledky určit problémy.

[Sledování využití][usage] – Zjistěte, jak lidé používají vaši aplikaci.

[Řešení problémů][qna] - a Q & A



<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[greenbrown]: ../../azure-monitor/app/asp-net.md
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[redfield]: ../../azure-monitor/app/monitor-performance-live-website-now.md
[start]: ../../azure-monitor/app/app-insights-overview.md
[usage]: usage-overview.md
[livestream]: ../../azure-monitor/app/live-stream.md
[snapshot]: ../../azure-monitor/app/snapshot-debugger.md



