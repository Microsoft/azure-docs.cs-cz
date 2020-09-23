---
title: Monitorujte stav a využití vaší aplikace pomocí Application Insights
description: Začínáme s Application Insights. Analyzujte využití, dostupnost a výkon vašich místních nebo Microsoft Azurech aplikací.
ms.topic: conceptual
ms.date: 05/10/2018
ms.reviewer: sdash
ms.openlocfilehash: f84e1065f93fc2cca950f12d5df537b931faac8d
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90987150"
---
# <a name="monitor-performance-in-web-applications"></a>Sledování výkonu webových aplikací


Ujistěte se, že aplikace správně funguje, a zjistěte, co se nepovede. [Application Insights][start] vás seznámí s případnými problémy s výkonem a výjimkami a pomůže vám najít a diagnostikovat hlavní příčiny.

Application Insights mohou monitorovat webové aplikace a služby v jazyce Java i ASP.NET, služby WCF. Můžou být hostované místně, na virtuálních počítačích nebo jako Microsoft Azure websites. 

Na straně klienta Application Insights může převzít telemetrii z webových stránek a širokou škálu zařízení, včetně aplikací pro iOS, Android a Windows Store.

## <a name="set-up-performance-monitoring"></a><a name="setup"></a>Nastavení sledování výkonu
Pokud jste ještě nepřidali Application Insights do projektu (tj. Pokud nemá ApplicationInsights.config), vyberte jeden z následujících způsobů, jak začít:

* [Webové aplikace v ASP.NET](./asp-net.md)
  * [Přidat monitorování výjimek](./asp-net-exceptions.md)
  * [Přidat monitorování závislostí](./monitor-performance-live-website-now.md)
* [Webové aplikace Java EE](./java-in-process-agent.md)

## <a name="exploring-performance-metrics"></a><a name="view"></a>Prozkoumávání metrik výkonu
V [Azure Portal](https://portal.azure.com)přejděte k prostředku Application Insights, který jste nastavili pro vaši aplikaci. Okno Přehled obsahuje základní údaje o výkonu:

Kliknutím na libovolný graf zobrazíte více podrobností a zobrazíte výsledky po delší dobu. Klikněte například na dlaždici požadavky a pak vyberte časový rozsah:

![Klikněte na více dat a vyberte časový rozsah.](./media/web-monitor-performance/appinsights-48metrics.png)

Klikněte na graf pro výběr metrik, které se zobrazí, nebo přidejte nový graf a vyberte jeho metriky:

![Kliknutím na graf vyberte metriky.](./media/web-monitor-performance/appinsights-61perfchoices.png)

> [!NOTE]
> **Zrušte výběr všech metrik** , abyste viděli kompletní výběr, který je k dispozici. Metriky spadají do skupin; Když je vybraný libovolný člen skupiny, zobrazí se jenom ostatní členové této skupiny.

## <a name="what-does-it-all-mean-performance-tiles-and-reports"></a><a name="metrics"></a>Co to znamená? Dlaždice výkonu a sestavy
Můžete získat různé metriky výkonu. Pojďme začít s uživateli, kteří se ve výchozím nastavení zobrazují v okně aplikace.

### <a name="requests"></a>Žádosti
Počet požadavků HTTP přijatých v zadaném období. Porovnejte je s výsledky na jiných sestavách a podívejte se, jak se vaše aplikace chová, protože se liší zatížení.

Požadavky HTTP zahrnují všechny požadavky GET nebo POST pro stránky, data a obrázky.

Kliknutím na dlaždici získáte počty pro konkrétní adresy URL.

### <a name="average-response-time"></a>Průměrná doba odezvy
Měří čas mezi webovým požadavkem, který vstupuje do vaší aplikace, a vrácenou odpovědí.

Body ukazují klouzavý průměr. Pokud existuje mnoho požadavků, může dojít k určitým odchylám od průměru bez zjevné špičky nebo DIP v grafu.

Hledejte neobvyklé špičky. Obecně je možné očekávat dobu odezvy při nárůstu požadavků. Pokud je nárůst neúměrný, může být vaše aplikace v rámci omezení prostředků, jako je například CPU nebo kapacita služby, kterou používá.

Kliknutím na dlaždici získáte časy pro konkrétní adresy URL.

![Snímek obrazovky s podoknem stavu aplikace, ve kterém se zobrazují spojnicové grafy v čase přesunutí průměrných požadavků a doby odezvy.](./media/web-monitor-performance/appinsights-42reqs.png)

### <a name="slowest-requests"></a>Nejpomalejší požadavky
![Snímek obrazovky se seznamem nejpomalejších požadavků a jejich dob odezvy.](./media/web-monitor-performance/appinsights-44slowest.png)

Zobrazuje, které požadavky můžou potřebovat vyladění výkonu.

### <a name="failed-requests"></a>Neúspěšné požadavky
![Snímek obrazovky se spojnicovým grafem v čase s počtem neúspěšných požadavků; To znamená, že požadavky, které vygenerovaly nezachycené výjimky.](./media/web-monitor-performance/appinsights-46failed.png)

Počet požadavků, které vygenerovaly nezachycené výjimky.

Kliknutím na dlaždici zobrazíte podrobnosti o konkrétních selháních a výběrem jednotlivé žádosti zobrazíte její podrobnosti. 

Pouze reprezentativní vzorek selhání je uchován pro individuální kontrolu.

### <a name="other-metrics"></a>Jiné metriky
Pokud chcete zjistit, jaké další metriky můžete zobrazit, klikněte na graf a potom zrušte výběr všech metrik, aby se zobrazila úplná dostupná sada. Kliknutím (i) zobrazíte definici každé metriky.

![Zrušit výběr všech metrik pro zobrazení celé sady](./media/web-monitor-performance/appinsights-62allchoices.png)

Výběrem jakékoli metriky zakážete ostatním uživatelům, kteří se nemůžou objevit ve stejném grafu.

## <a name="set-alerts"></a>Nastavení upozornění
Pokud chcete dostávat oznámení e-mailem o neobvyklých hodnotách jakékoli metriky, přidejte upozornění. Můžete zvolit odeslání e-mailu správcům účtu nebo určitým e-mailovým adresám.

![Snímek obrazovky dialogového okna Přidat pravidlo výstrahy spolu s snímky obrazovky připojenými šipkami, které ukazují, jak se dostat k tomuto dialogovému oknu z Průzkumník metrik.](./media/web-monitor-performance/appinsights-413setMetricAlert.png)

Nastavte prostředek před ostatními vlastnostmi. Nevybírejte prostředky webového testu, pokud chcete nastavit výstrahy týkající se výkonu nebo metrik využití.

Nezapomeňte si uvědomit jednotky, ve kterých jste vyzváni k zadání prahové hodnoty.

*Nevidím tlačítko Přidat výstrahu.* – Jedná se o účet skupiny, ke kterému máte přístup jen pro čtení? Obraťte se na správce účtu.

## <a name="diagnosing-issues"></a><a name="diagnosis"></a>Diagnostikování problémů
Tady je několik tipů pro hledání a diagnostiku problémů s výkonem:

* Nastavte, aby byly [webové testy][availability] upozorňovány, pokud váš web přestane reagovat nebo je nereaguje nesprávně nebo pomalu. 
* Porovnejte počet požadavků s ostatními metrikami, abyste viděli, jestli chyby nebo pomalé odezvy souvisejí s načtením.
* [Vkládat a hledat příkazy trasování][diagnostic] v kódu, které vám pomůžou identifikovat problémy.
* Monitorujte svou webovou aplikaci v provozu pomocí [Live Metrics Stream][livestream].
* Zachyťte stav aplikace .NET pomocí [Snapshot Debugger][snapshot].

## <a name="find-and-fix-performance-bottlenecks-with-performance-investigation-experience"></a>Hledání a oprava slabých míst výkonu pomocí prostředí pro zkoumání výkonu

Prostředí pro šetření výkonu můžete použít ke kontrole pomalých operací ve vaší webové aplikaci. Můžete rychle vybrat konkrétní pomalou operaci a použít [Profiler](./profiler.md) k hlavní příčině pomalé operace v kódu. Pomocí nové distribuce trvání zobrazené pro vybranou operaci můžete rychle na první pohled vyhodnotit, jak špatné je prostředí pro vaše zákazníky. Můžete zjistit, kolik vašich uživatelských interakcí bylo ovlivněno při každé pomalé operaci. V následujícím příkladu jsme se rozhodli, že se podíváme na možnosti získání zákazníků a podrobností. V rozdělení trvání vidíte, že existují tři špičky. Špička úplně vlevo je okolo 400 MS a představuje skvělé možnosti reakce. Střední špička je okolo 1,2 s a představuje mediocreé prostředí. Nakonec v 3,6 s máme další malý špičku, který představuje prostředí 99 percentilu, což pravděpodobně způsobí, že naši zákazníci odejdou nespokojenost. Toto prostředí je po deseti případech pomalejší než Skvělé prostředí pro stejnou operaci. 

![ZÍSKAT zákazníky/podrobnosti tři špičky trvání](./media/web-monitor-performance/PerformanceTriageViewZoomedDistribution.png)

Pro lepší představu o zkušenostech uživatelů pro tuto operaci můžeme vybrat větší časový rozsah. V určitém časovém intervalu se pak můžeme v čase prodloužit. V následujícím příkladu jsme přešli z výchozího časového rozmezí na 7 dní a pak se přiblížili k časovému intervalu 9:47 až 12:47 mezi Út 12. a nějak změnily 13. Napravo se aktualizovala jak rozdělení trvání, tak počet ukázek a trasování profileru.

![Snímek obrazovky zobrazuje výkon (Preview) se třemi špičkami trvání v rozmezí 7 dní s časovým intervalem](./media/web-monitor-performance/PerformanceTriageView7DaysZoomedTrend.png)

Abychom se mohli zúžit na pomalé prostředí, příště přiblížíme dobu trvání, která se nachází mezi 95. a 99 percentilem. To představuje 4% interakcí uživatelů, které byly pomalé.

![Snímek obrazovky s časovým intervalem vám ukáže, jak získat zákazníky/podrobnosti tři špičky trvání v rozmezí 7 dnů.](./media/web-monitor-performance/PerformanceTriageView7DaysZoomedTrendZoomed95th99th.png)

Teď se můžeme podívat na zástupce ukázek, a to kliknutím na tlačítko ukázky nebo na základě zástupce trasování v profileru kliknutím na tlačítko trasování profileru. V tomto příkladu jsou k dispozici čtyři trasování, která jsou shromážděna pro získání zákazníků/podrobností v časovém intervalu a trvání rozsahu zájmu.

V některých případech problém nebude ve vašem kódu, ale spíše v závislosti, kterou váš kód volá. Můžete přejít na kartu závislosti v zobrazení třídění výkonu a prozkoumat takové pomalé závislosti. Ve výchozím nastavení je zobrazení výkonu v trendech trendů, ale co opravdu chcete zobrazit, je 95. percentil (nebo 99 pro případ, že sledujete vyspělou službu). V následujícím příkladu jsme se zaměřili na pomalou závislost Azure BLOB, kde zavoláme PUT fabrikamaccount. Cluster s dobrým prostředím v rozmezí 40 MS, zatímco pomalé volání stejné závislosti je třikrát pomalejší, clustering kolem 120 MS. Nepřijímá mnoho z těchto volání k tomu, aby mohla příslušná operace výrazně zpomalit. Můžete přejít na zástupce ukázek a trasování profileru, stejně jako na kartě operace.

![ZÍSKAT zákazníky/podrobnosti tři špičky trvání v rozmezí 7 dní s časovým intervalem](./media/web-monitor-performance/SlowDependencies95thTrend.png)

Prostředí pro zkoumání výkonu zobrazuje relevantní přehledy na straně ukázkové sady, na které jste se rozhodli zaměřit. Nejlepším způsobem, jak si prohlédnout všechny dostupné přehledy, je přepnout na 30 dní časového rozsahu a pak vybrat celkově a zobrazit si přehledy napříč všemi operacemi za minulý měsíc.

![Snímek obrazovky zobrazuje výkon (Preview) s vybraným celkovým otevřeným a přehledem okna.](./media/web-monitor-performance/Performance30DayOveralllnsights.png)


## <a name="next-steps"></a><a name="next"></a>Další kroky
[Webové testy][availability] – webové požadavky se odesílají do vaší aplikace v pravidelných intervalech z celého světa.

[Zachytávání a hledání diagnostických trasování][diagnostic] – vkládání trasování a prosévání prostřednictvím výsledků k určení problémů.

[Sledování využití][usage] – Zjistěte, jak uživatelé používají vaši aplikaci.

[Řešení potíží][qna] – a Q & a



<!--Link references-->

[availability]: ./monitor-web-app-availability.md
[diagnostic]: ./diagnostic-search.md
[greenbrown]: ./asp-net.md
[qna]: ../faq.md
[redfield]: ./monitor-performance-live-website-now.md
[start]: ./app-insights-overview.md
[usage]: usage-overview.md
[livestream]: ./live-stream.md
[snapshot]: ./snapshot-debugger.md

