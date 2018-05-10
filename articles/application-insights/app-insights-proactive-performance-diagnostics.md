---
title: Inteligentní detekce - anomálie výkonu | Microsoft Docs
description: Application Insights provede inteligentní analýzu aplikace telemetrie a zobrazí upozornění na potenciální problémy. Tato funkce vyžaduje žádné nastavení.
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: 6acd41b9-fbf0-45b8-b83b-117e19062dd2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: mbullwin; antonfr
ms.openlocfilehash: 59b88a940e83ed89e8638b62680a57ca1514f8b0
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="smart-detection---performance-anomalies"></a>Inteligentní detekce - anomálie výkonu

[Application Insights](app-insights-overview.md) automaticky analyzuje výkon vaší webové aplikace a může vás upozorní na potenciální problémy. Vám může být čtení to vzhledem k tomu, že jste dostali jeden z našich Inteligentní detekce oznámení.

Tato funkce vyžaduje žádné speciální nastavení, než konfigurace aplikace pro službu Application Insights (na [ASP.NET](app-insights-asp-net.md), [Java](app-insights-java-get-started.md), nebo [Node.js](app-insights-nodejs.md)a v [kódu webové stránky](app-insights-javascript.md)). Bude aktivní, pokud vaše aplikace generuje dost telemetrických dat.

## <a name="when-would-i-get-a-smart-detection-notification"></a>Při obdržení oznámení Inteligentní detekce

Application Insights zjistil, že má ke snížení výkonu aplikace v jednom z těchto způsobů:

* **Snížení času odezvy** -aplikace bylo zahájeno pomalejší, než kterou používal na reagovat na požadavky. Tato změna byla pravděpodobně rychlé, například kvůli Regrese v nejnovější nasazení. Nebo mohlo být postupné, může být způsobeno nevrácenou pamětí. 
* **Snížení doby trvání závislosti** -aplikace provádí volání rozhraní REST API, databáze nebo jinou závislost. Závislost je pomalejší, než kterou používal na reagovat.
* **Vzor nízký výkon** – aplikace se zobrazí tak, aby měl problém výkonu, který ovlivňuje jenom některé požadavky. Například stránek načítání pomaleji na jeden typ prohlížeče než jiné; nebo požadavků, jsou obsluhovány pomaleji z jednoho konkrétního serveru. V současné době naše algoritmy podívejte se na časů načtení stránky, doby odezvy požadavku a časy odezvy závislostí.  

Inteligentní detekce vyžaduje minimálně 8 dní telemetrii na svazek použitelnou stanovení základní úrovně výkonu na normální. Ano po spuštění vaší aplikace pro toto období, žádné významné problém výsledkem bude oznámení.


## <a name="does-my-app-definitely-have-a-problem"></a>Aplikace my máme věci má problém?

Ne, oznámení neznamená, že vaše aplikace výborný došlo k problému. Je jednoduše návrh o něco, že můžete chtít podívejte se na další úzce.

## <a name="how-do-i-fix-it"></a>Jakým způsobem ji lze upravit?

Oznámení zahrnují diagnostické informace. Tady je příklad:


![Tady je příklad zjišťování snížení času odezvy serveru](./media/app-insights-proactive-diagnostics/server_response_time_degradation.png)

1. **Třídění**. Oznámení se zobrazuje počet uživatelů, kteří nebo jsou ovlivněny kolik operace. Můžete přiřadit prioritu problém.
2. **Obor**. Problém ovlivňuje veškerý provoz, nebo jen některé stránky? Je omezen na konkrétní prohlížeče nebo umístění? Tyto informace můžete získat z oznámení.
3. **Diagnostika**. Diagnostické informace v oznámení často navrhne povaze problému. Například pokud doba odezvy zpomalí, když je vysoká rychlost požadavků, který naznačuje, že jsou přetížené serveru nebo v závislosti. 

    Jinak otevřete okno výkon ve službě Application Insights. Zde najdete [profileru](app-insights-profiler.md) data. Pokud jsou výjimky vyvolány, můžete také zkusit [ladicí program snímku](app-insights-snapshot-debugger.md).



## <a name="configure-email-notifications"></a>Konfigurace e-mailových oznámení

Inteligentní detekce oznámení jsou ve výchozím nastavení povolené a odeslat na ty, kteří mají [vlastníci, přispěvatelé a čtenáři přístup k danému prostředku Application Insights](app-insights-resources-roles-access-control.md). Chcete-li toto nastavení změnit, **konfigurace** v e-mailových oznámení nebo otevřít nastavení inteligentního detekce ve službě Application Insights. 
  
  ![Nastavení inteligentního detekce](./media/app-insights-proactive-diagnostics/smart_detection_configuration.png)
  
  * Můžete použít **odhlášení** odkaz v e-mailu Inteligentní detekce zastavit doručování e-mailová oznámení.

E-mailů o inteligentní detekce anomálií výkonu jsou omezeny na jednu e-mailovou za den za prostředek Application Insights. E-mailu, odešle jenom v případě, že existuje alespoň jeden nový problém, která byla zjištěna v daný den. Nezískáte opakuje žádné zprávy. 

## <a name="faq"></a>Nejčastější dotazy

* *Ano zaměstnanci Microsoftu podívejte se na svá data?*
  * Ne. Služba je plně automatická. Pouze zobrazí oznámení. Vaše data jsou [privátní](app-insights-data-retention-privacy.md).
* *Analyzovat všechna data shromážděná pomocí Application Insights?*
  * Není v současné době. V současné době budeme analyzovat požadavku, že doba načítání doba odezvy, závislosti doba odezvy a stránky. Analýza další metriky je na našem nevyřízených položek Těšíme se.

* Jaké typy aplikací to funguje pro?
  * Tyto degradations zjištění v jakékoli aplikace, která generuje odpovídající telemetrie. Pokud jste nenainstalovali Application Insights ve vaší webové aplikaci, pak požadavky a závislosti automaticky sledovány. Ale v back-end služby nebo jiné aplikace, pokud jste vložili volání [TrackRequest()](app-insights-api-custom-events-metrics.md#trackrequest) nebo [TrackDependency](app-insights-api-custom-events-metrics.md#trackdependency), pak Inteligentní detekce bude fungovat stejným způsobem.

* *Můžete vytvořit vlastní anomálií pravidla detekce nebo přizpůsobit existující pravidla?*

  * Ještě nebyla ale můžete:
    * [Nastavení výstrah](app-insights-alerts.md) který říct, kdy metriky protne prahovou hodnotu.
    * [Export telemetrie](app-insights-export-telemetry.md) k [databáze](app-insights-code-sample-export-sql-stream-analytics.md) nebo [do PowerBI](app-insights-export-power-bi.md), kde můžete analyzovat sami.
* *Jak často se provádí analýzu?*

  * Analýza spustíme denně na telemetrie z předchozí den (plný den v časovém pásmu UTC).
* *Proto nemá tato nahradit [metriky výstrahy](app-insights-alerts.md)?*
  * Ne.  Jsme nemáte potvrzení zjišťování každých chování, které můžete zvážit neobvyklé.


* *Pokud dělat nemáte nic v reakci na oznámení, bude získat připomenutí?*
  * Ne, dostanete zprávu o každém problému pouze jednou. Pokud potíže potrvají, bude aktualizována Inteligentní detekce kanálu okno.
* *Ztrátou e-mailu. Kde najdu oznámení na portálu*
  * V přehledu nástroje Application Insights vaší aplikace, klikněte **Inteligentní detekce** dlaždici. Existuje budete moci najít všechna oznámení až do 90 dnů zpět.

## <a name="how-can-i-improve-performance"></a>Jak může zlepšit výkon?
Pomalé a k selhání odpovědi jsou jeden z největších frustrations pro uživatele, webu, protože víte z vlastního prostředí. Ano je důležité pro řešení problémů.

### <a name="triage"></a>Třídění
Nejprve záleží? Pokud je vždy pomalé načítání stránky, ale pouze 1 % uživatelé vaší lokality někdy bude nutné ji, možná bude potřeba další důležité otázky, které vezměte v úvahu. Na druhé straně Pokud pouze 1 % uživatelé otevřít, ale pokaždé, když vyvolá výjimek, který může být vhodné příčin.

Příkaz dopad (ovlivnění uživatelé nebo % z provozu), použijte jako vodítko Obecné, ale uvědomte si, že to není celého textu. Shromažďování dalších důkaz k potvrzení.

Vezměte v úvahu parametry problému. Pokud je závislé na geography, nastavit [testy dostupnosti](app-insights-monitor-web-app-availability.md) včetně danou oblast: existuje může být jednoduše problémů se sítí v této oblasti.

### <a name="diagnose-slow-page-loads"></a>Diagnostika pomalé načítání stránky
Kde je problém? Je pomalé reagovat na server, je velmi náročná na stránku, nebo prohlížeče třeba provádět spoustu práce, můžete ho zobrazit?

Otevřete okno prohlížeče metriky. Segmentovaným zobrazení prohlížeče stránku zatížení čas ukazuje kam čas. 

* Pokud **odeslat doba požadavku** je vysoká, buď server bude reagovat pomalu nebo post s velkým množstvím dat se nachází daná žádost. Podívejte se na [metriky výkonu](app-insights-web-monitor-performance.md#metrics) prozkoumat doby odezvy.
* Nastavit [závislost sledování](app-insights-asp-net-dependencies.md) zda pomalost je z důvodu externích služeb nebo databáze.
* Pokud **přijetí odpovědi** převládá, jsou dlouhé stránku a jeho závislé součásti.-JavaScript, CSS, obrázků a tak dále (ale asynchronně načtená data). Nastavení [test dostupnosti](app-insights-monitor-web-app-availability.md)a nastavte možnost načíst závislé součásti. Pokud některé výsledky získáte, otevřete podrobností výsledků a rozšířit tak, aby najdete v části časů načtení z různých souborů.
* Vysoká **doba zpracování klienta** navrhuje skripty běží pomalu. Pokud z důvodu není zřejmé, zvažte přidání nějaký kód časování a odeslat časy v trackMetric volání.

### <a name="improve-slow-pages"></a>Zlepšení pomalé stránky
Není webové úplné poradenství zlepšení odpovědí serveru a časů načtení stránky, proto se nebudeme snažit se všechny zde opakovat. Zde je několik tipů, které pravděpodobně již víte o, stačí, abyste mohli myslím.

* Pomalé načítání kvůli velkých souborů: spouštění skriptů a dalšími částmi asynchronně. Použijte sdružování skriptů. Hlavní stránka rozdělte pomůcky, které samostatně načíst svá data. Neodesílat prostý staré HTML pro dlouho tabulky: požadavku dat jako ostatní compact formát JSON nebo pomocí skriptu a potom vyplnění tabulky v místě. Existují skvělé rozhraní usnadní to vše. (Také má za následek big skripty samozřejmě.)
* Pomalé serveru závislosti: Zvažte zeměpisné umístění komponent. Například pokud používáte Azure, ujistěte se, že webový server a databáze jsou ve stejné oblasti. Dotazy načítají více informací, než potřebují? By ukládání do mezipaměti nebo dávkování pomoc?
* Problémy s kapacity: Podívejte se na serveru metrik doby odezvy a počet požadavku. Pokud odezvy ve špičkách nepřiměřeně s vrcholů v žádosti, je pravděpodobné, že jsou vaše servery roztažená.


## <a name="server-response-time-degradation"></a>Snížení času odezvy serveru

Oznámení snížení času odezvy poskytuje:

* Doba odezvy ve srovnání s normální odezvu pro tuto operaci.
* Počet uživatelů, kteří jsou vliv.
* Průměrná doba odezvy a 90 percentilu doba odezvy pro tuto operaci na den detekce a 7 dní před. 
* Počet žádostí o tuto operaci na den detekce a 7 dní před.
* Korelace mezi snížení v této operaci a degradations v související závislosti. 
* Odkazy k diagnostice problému.
  * Trasování profileru k zobrazení, kde je operace čas strávený (odkaz je k dispozici, pokud profileru trasování příklady, které byly shromážděny pro tuto operaci během období detekce). 
  * Sestavy pro zvýšení výkonu v Průzkumníku metrika, kde můžete vyfiltrování a rozčlenění čas nebo filtry rozsahu pro tuto operaci.
  * Vyhledávání pro toto volání zobrazíte vlastnosti konkrétní volání.
  * Selhání sestavy - li počet > 1 to znamenat, že došlo k chybám při této operaci, která může přispět ke snížení výkonu.

## <a name="dependency-duration-degradation"></a>Snížení doby trvání závislosti

Moderní aplikace více přijmout malých služby návrhu přístup, což v mnoha případech vede k velkou spolehlivosti na externích služeb. Například pokud vaše aplikace je založena na některé datovou platformu a i když můžete vytvořit vlastní robota služby, které bude pravděpodobně předávání na některé zprostředkovatele kognitivní services povolit vaší robotů interakci více lidského způsoby a některé data store pro robota vyžádání odpovědi z.  

Příklad závislostí snížení oznámení:

![Tady je příklad zjišťování snížení doby trvání závislosti](./media/app-insights-proactive-diagnostics/dependency_duration_degradation.png)

Všimněte si, že se dozvíte:

* Doba trvání ve srovnání s normální odezvu pro tuto operaci
* Počet uživatelů, kteří se vztahuje.
* Průměrná doba trvání a 90 percentilu trvání pro tuto závislost na den detekce a 7 dní před
* Počet závislostí volání na den detekce a 7 dní před
* Odkazy k diagnostice problému
  * Sestavy pro zvýšení výkonu v Průzkumníku Metrika pro tuto závislost
  * Vyhledejte tuto závislost volá zobrazíte vlastnosti volání
  * Selhání sestav – Pokud počet > 1, to znamená, že došlo k selhání závislosti volá během zjišťování dobu, po kterou může přispět ke snížení doby trvání. 
  * Otevřete Analytics s dotazy, které vypočítat této doby trvání závislosti a počet  

## <a name="smart-detection-of-slow-performing-patterns"></a>Inteligentní detekce pomalé provádění schémat 

Application Insights vyhledá problémy s výkonem, které mohou ovlivňují jenom některé část vaši uživatelé, nebo které ovlivňují jenom uživatelé v některých případech. Například oznámení o zatížení stránky je pomalejší na jeden typ prohlížeče než na jiné typy prohlížečů, nebo pokud z určitého serveru pomaleji zpracovat požadavky. Problémy spojené s kombinací vlastností, může také vyhledat, jako je pomalé stránka načte v jedné zeměpisné oblasti pro klienty, kteří používají konkrétní operační systém.  

Anomálie takovéto jsou velmi obtížné zjistit právě zkontrolováním data, ale jsou častější, než si myslíte. Často se pouze upozornit, když stížnost vašich zákazníků. Do té doby, je příliš pozdní: ovlivnění uživatelé jsou již přepnutí na konkurence!

V současné době naše algoritmy podívejte se na časů načtení stránky, doby odezvy požadavku na serveru a časy odezvy závislostí.  

Nemáte žádné prahové hodnoty nastavit nebo konfigurovat pravidla. Machine learning a algoritmy dolování dat se používají ke zjištění neobvyklé vzory.

![Z e-mailové výstrahy klikněte na odkaz otevřete diagnostické zprávy v Azure](./media/app-insights-proactive-performance-diagnostics/03.png)

* **Když** zobrazuje čas byl zjištěn problém.
* **Co** popisuje:

  * Problém, který byl zjištěn;
  * Charakteristika sadu událostí, které jsme našli zobrazí chování problém.
* Tabulka porovnává nedostatečně výkonná sada s průměrnou chování všechny další události.

Kliknutím na odkazy otevřete Průzkumníka metrika a hledání na příslušné sestavy, filtrováno vlastnosti sady pomalým výkonem a čas.

Změnit časové rozmezí a filtry a prozkoumejte telemetrii.

## <a name="next-steps"></a>Další postup
Tyto diagnostické nástroje můžete zkontrolovat telemetrie z vaší aplikace:

* [Profiler](app-insights-profiler.md) 
* [Ladicí program snímku](app-insights-snapshot-debugger.md)
* [Analýzy](app-insights-analytics-tour.md)
* [Analýza inteligentní diagnostiky](app-insights-analytics-diagnostics.md)

Inteligentní detekce jsou zcela automatické. Ale možná chcete nastavit některé další výstrahy?

* [Ručně konfigurované metriky výstrahy](app-insights-alerts.md)
* [Testy dostupnosti webu](app-insights-monitor-web-app-availability.md)
