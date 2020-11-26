---
title: Inteligentní zjišťování – anomálie výkonu | Microsoft Docs
description: Application Insights provádí inteligentní analýzu telemetrie aplikací a upozorňuje na potenciální problémy. Tato funkce nepotřebuje žádné nastavení.
ms.topic: conceptual
ms.date: 05/04/2017
ms.reviewer: antonfr
ms.openlocfilehash: 28a32c714618a3b6b0c3126d8060295f134ddc7c
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186231"
---
# <a name="smart-detection---performance-anomalies"></a>Inteligentní zjišťování – anomálie výkonu

[Application Insights](./app-insights-overview.md) automaticky analyzuje výkon webové aplikace a může vás upozornit na potenciální problémy. Můžete si ho přečíst, protože jste dostali jednu z našich oznámení inteligentního zjišťování.

Tato funkce nevyžaduje žádné speciální nastavení, kromě konfigurace aplikace pro Application Insights pro váš [podporovaný jazyk](./platforms.md). Je aktivní, když vaše aplikace vygeneruje dostatek telemetrie.

## <a name="when-would-i-get-a-smart-detection-notification"></a>Kdy získám oznámení inteligentního zjišťování?

Application Insights zjistil, že výkon vaší aplikace se snížil jedním z následujících způsobů:

* **Snížení doby odezvy** – vaše aplikace začala reagovat na požadavky pomaleji než při použití. Tato změna může být rychlá, například v případě, že v nejnovějším nasazení došlo k regresi. Nebo to může být postupný, možná způsobené nevrácenou pamětí. 
* **Snížení doby trvání závislosti** – vaše aplikace volá REST API, databázi nebo jiné závislosti. Závislost reaguje pomaleji než při použití.
* **Vzorek pomalého výkonu** – vaše aplikace se zdá mít problémy s výkonem, které ovlivňují jenom některé požadavky. Například stránky se načítají pomaleji na jeden typ prohlížeče než jiné. nebo jsou požadavky obsluhovány pomaleji z jednoho konkrétního serveru. V současné době naše algoritmy hledají dobu načítání stránek, dobu odezvy na žádosti a doby odezvy závislostí.  

Inteligentní zjišťování vyžaduje alespoň 8 dní telemetrie na funkčním svazku, aby bylo možné vytvořit standardní hodnotu normálního výkonu. Takže po spuštění vaší aplikace v této době budou všechny významné problémy mít za následek oznámení.


## <a name="does-my-app-definitely-have-a-problem"></a>Má moje aplikace konečně nějaký problém?

Ne, oznámení neznamená, že vaše aplikace má jednoznačně problém. Je to je jednoduše návrh ohledně něčeho, co byste měli blíže prozkoumat.

## <a name="how-do-i-fix-it"></a>Jak ho mám opravit?

Oznámení obsahují diagnostické informace. Tady je příklad:


![Tady je příklad detekce degradace doby odezvy serveru.](media/proactive-performance-diagnostics/server_response_time_degradation.png)

1. **Třídění**. V oznámení se dozvíte, kolik uživatelů nebo kolik operací je ovlivněno. To vám může přispět k přiřazení priority k problému.
2. **Rozsah**. Má tento problém vliv na veškerý provoz, nebo jenom na nějaké stránky? Je omezená na konkrétní prohlížeče nebo umístění? Tyto informace lze získat z oznámení.
3. **Diagnostika:** Diagnostické informace v oznámení budou často navrhovat povahu problému. Například pokud se doba odezvy zpomaluje, když je rychlost požadavků vysoká, navrhne se, aby váš server nebo závislosti byly přetížené. 

    V opačném případě otevřete okno výkon v Application Insights. Tady najdete data [profileru](profiler.md) . Pokud jsou výjimky vyvolány, můžete také vyzkoušet [ladicí program snímku](./snapshot-debugger.md).



## <a name="configure-email-notifications"></a>Konfigurace e-mailových oznámení

Oznámení inteligentní detekce jsou ve výchozím nastavení povolená a odesílají se uživatelům, kteří mají [Čtenář monitorování](../../role-based-access-control/built-in-roles.md#monitoring-reader) a [monitorování](../../role-based-access-control/built-in-roles.md#monitoring-contributor) , přístup k předplatnému, ve kterém se Application Insights prostředek nachází. Pokud ho chcete změnit, buď v e-mailovém oznámení klikněte na **Konfigurovat** , nebo otevřete nastavení inteligentního zjišťování v Application Insights. 
  
  ![Nastavení inteligentního zjišťování](media/proactive-performance-diagnostics/smart_detection_configuration.png)
  
  * Pokud chcete zastavit příjem e-mailových oznámení, můžete použít odkaz zrušit **odběr** v e-mailu inteligentní detekce.

U e-mailů o inteligentních Detekcích se anomálie výkonu u každého Application Insights prostředku omezí na jeden e-mail za den. E-mail se pošle jenom v případě, že se v daném dni našel aspoň jeden nový problém. Žádná zpráva se neopakuje. 

## <a name="faq"></a>Časté otázky

* *Proto si zaměstnanci Microsoftu prohledají moje údaje?*
  * Ne. Služba je zcela automatická. Oznámení se zobrazí jenom vy. Vaše data jsou [soukromá](./data-retention-privacy.md).
* *Analyzujete všechna data shromažďovaná v Application Insights?*
  * V současné době není k dispozici. V současné době analyzujeme dobu odezvy požadavku, dobu odezvy závislosti a dobu načítání stránky. Analýza dalších metrik je na našich nevyřízených položkách, které jsou vyhledány.

* Jaké typy aplikací Tato práce funguje?
  * Tyto degradace se zjišťují v jakékoli aplikaci, která generuje příslušnou telemetrii. Pokud jste ve své webové aplikaci nainstalovali Application Insights, jsou požadavky a závislosti automaticky sledovány. I když jste do služby back-endu nebo jiných aplikací vložili volání do [TrackRequest ()](./api-custom-events-metrics.md#trackrequest) nebo [TrackDependency](./api-custom-events-metrics.md#trackdependency), inteligentní zjišťování bude fungovat stejným způsobem.

* *Můžu vytvořit vlastní pravidla detekce anomálií nebo přizpůsobit stávající pravidla?*

  * Ještě ne, ale můžete:
    * [Nastavte výstrahy](../platform/alerts-log.md) , které vás upozorní, když metrika přechází do prahové hodnoty.
    * [Exportujte telemetrii](./export-telemetry.md) do [databáze](./code-sample-export-sql-stream-analytics.md) nebo [Power BI](./export-power-bi.md), kde ji můžete analyzovat sami.
* *Jak často se provádí analýza?*

  * Analýzy provedeme denně na telemetrie z předchozího dne (celý den v časovém pásmu UTC).
* *To znamená, že tyto [výstrahy metriky](../platform/alerts-log.md)nahrazují?*
  * Ne.  Nepotvrzuji jsme zjišťování všech chování, která by se vám mohla považovat za neobvyklou.


* *Pokud v reakci na oznámení něco neudělám, zobrazí se vám připomenutí?*
  * Ne, zobrazí se zpráva o každém problému jenom jednou. Pokud potíže potrvají, bude aktualizováno v okně kanálu inteligentního zjišťování.
* *Ztratil jsem e-mail. Kde najdu oznámení na portálu?*
  * V přehledu Application Insights vaší aplikace klikněte na dlaždici **inteligentního zjišťování** . Budete moct najít všechna oznámení až do 90 dnů zpátky.

## <a name="how-can-i-improve-performance"></a>Jak můžu zvýšit výkon?
Pomalé a neúspěšné odpovědi jsou jedním z největších frustrations pro uživatele webů, jak znáte vlastní prostředí. Proto je důležité problémy vyřešit.

### <a name="triage"></a>Třídění
Jako první se to dělá? Pokud je stránka vždy pomalu načtena, ale pouze 1% uživatelů vaší lokality se někdy musí podívat na IT, možná máte důležitější věci, které je třeba zvážit. Na druhé straně, pokud je otevře pouze 1% uživatelů, ale vyvolá výjimky pokaždé, co by mohlo být prozkoumání.

Jako obecné vodítko použijte příkaz dopadu (ovlivněné uživatele nebo% provozu), ale mějte na paměti, že se nejedná o celý příběh. Shromážděte další legitimace k potvrzení.

Vezměte v úvahu parametry problému. Pokud se jedná o geograficky závislé, nastavte [testy dostupnosti](./monitor-web-app-availability.md) , včetně této oblasti: v této oblasti můžou být jenom problémy se sítí.

### <a name="diagnose-slow-page-loads"></a>Diagnostika pomalých načítání stránek
Kde se jedná o problém? Reaguje Server pomaleji, je stránka velmi dlouhá nebo je v něm nutné, aby si ji mohl zobrazit i prohlížeč hodně práce?

Otevřete okno metrika prohlížeče. Segmentované zobrazení času načtení stránky v prohlížeči ukazuje, kde se bude zobrazovat čas. 

* Pokud je **čas požadavku na odeslání** vysoký, znamená to, že server reaguje pomalu nebo se jedná o příspěvek s velkým množstvím dat. Podívejte se na [metriky výkonu](./web-monitor-performance.md#metrics) a prozkoumejte dobu odezvy.
* Nastavením [sledování závislostí](./asp-net-dependencies.md) zjistíte, jestli je zpomalení způsobeno externími službami nebo databází.
* Pokud je **příjem odpovědi** převládající, vaše stránka a její závislé součásti – JavaScript, CSS, obrázky atd. (ale ne asynchronně načtená data) jsou dlouhé. Nastavte [Test dostupnosti](./monitor-web-app-availability.md)a nezapomeňte nastavit možnost načítání závislých částí. Když dostanete nějaké výsledky, otevřete Podrobnosti výsledku a rozbalte ho, abyste viděli dobu načítání různých souborů.
* Vysoká **Doba zpracování klienta** navrhuje spouštění skriptů pomalu. Pokud důvod není zjevný, zvažte přidání nějakého kódu časování a odeslání času v voláních trackMetric.

### <a name="improve-slow-pages"></a>Zlepšení pomalých stránek
Na webu je dostatek Rady na vylepšení odezvy serveru a doby načítání stránek, proto se nebudeme opakovat tady. Tady je několik tipů, o kterých už jste se seznámili, stačí vám to, abyste se seznámili:

* Pomalé načítání z důvodu velkých souborů: asynchronní načtení skriptů a dalších částí. Použijte sdružování skriptů. Oddělte hlavní stránku na widgety, které data načítají samostatně. Neodesílat obyčejné staré HTML pro dlouhé tabulky: pomocí skriptu si vyžádejte data jako JSON nebo jiné kompaktní formátování a pak tabulku vyplňte. Existují Skvělé architektury, které vám pomůžou s tímto. (Zahrnuje také velké skripty samozřejmě.)
* Pomalé závislosti serveru: Zvažte zeměpisná umístění vašich komponent. Pokud například používáte Azure, ujistěte se, že webový server a databáze jsou ve stejné oblasti. Načítají dotazy Další informace, než potřebují? Mohlo by to být v ukládání do mezipaměti nebo v dávce?
* Problémy s kapacitou: Podívejte se na metriky serveru doby odezvy a počty požadavků. Pokud se špičky odezvy ve špičce v počtu požadavků neúměrně sníží, je pravděpodobný, že se vaše servery roztáhnou.


## <a name="server-response-time-degradation"></a>Snížení doby odezvy serveru

Oznámení o snížení úrovně doby odezvy vám oznamuje:

* Doba odezvy v porovnání s normální dobou odezvy pro tuto operaci.
* Kolik uživatelů je ovlivněno.
* Průměrná doba odezvy a doba odezvy 90 procent pro tuto operaci v den zjišťování a 7 dní před. 
* Počet žádostí o tuto operaci v den zjišťování a 7 dní před.
* Korelace mezi snížením v této operaci a snížením úrovně v souvisejících závislostech. 
* Odkazy, které vám pomůžou diagnostikovat problém.
  * Trasování profileru, která vám pomůžou zobrazit, kde se vybrala doba provozu (odkaz je dostupný, pokud se pro tuto operaci během období zjišťování shromáždily příklady trasování profileru). 
  * Sestavy výkonu v Průzkumníkovi metrik, kde můžete pro tuto operaci rozdělit časový rozsah nebo filtry na řezy a indexy.
  * Vyhledáním tohoto volání zobrazíte konkrétní vlastnosti volání.
  * Hlášení o selhání – Pokud je počet > 1, znamená to, že v této operaci došlo k chybám, které mohly přispět ke snížení výkonu.

## <a name="dependency-duration-degradation"></a>Snížení doby trvání závislosti

Moderní aplikace ještě více a více přijímají přístup k návrhu mikroslužeb, který v mnoha případech má za následek velkou spolehlivost externích služeb. Například pokud vaše aplikace spoléhá na určitou datovou platformu, nebo i když sestavíte vlastní službu bot, budete pravděpodobně přenášet na některého poskytovatele služeb rozpoznávání služeb, aby vaše roboty mohli pracovat efektivněji, a některé služby úložiště dat pro roboty, ze kterých si můžete stáhnout odpovědi.  

Příklad oznámení o degradování závislostí:

![Tady je příklad detekce snížení stupně trvání závislosti.](media/proactive-performance-diagnostics/dependency_duration_degradation.png)

Všimněte si, že vám oznamuje:

* Doba trvání v porovnání s normální dobou odezvy pro tuto operaci
* Kolik uživatelů je ovlivněno
* Průměrná doba trvání a 90 procent trvání této závislosti v den zjišťování a 7 dní před
* Počet volání závislosti v den detekce a 7 dní před
* Odkazy, které vám pomůžou diagnostikovat problém
  * Sestavy výkonu v Průzkumníkovi metrik pro tuto závislost
  * Vyhledat tato volání závislosti pro zobrazení vlastností volání
  * Hlášení o selhání – Pokud je počet > 1, znamená to, že během období zjišťování došlo k neúspěšným voláním závislostí, která mohla přispět ke snížení doby trvání. 
  * Otevřete analytiky s dotazy, které počítají tuto dobu trvání a počet závislostí.  

## <a name="smart-detection-of-slow-performing-patterns"></a>Inteligentní zjišťování pomalých způsobů provádění vzorců 

Application Insights vyhledá problémy s výkonem, které by mohly ovlivnit jenom část vašich uživatelů, nebo mají vliv jenom na uživatele v některých případech. Například oznámení o zatížení stránek je pomalejší na jednom typu prohlížeče než na jiných typech prohlížečů, nebo pokud jsou požadavky obsluhovány pomaleji z konkrétního serveru. Také může zjišťovat problémy spojené s kombinacemi vlastností, například pomalé načítání stránek v jedné geografické oblasti pro klienty používající konkrétní operační systém.  

Anomálie, jako jsou ty, jsou velmi obtížné detekovat pouhým zkontrolováním dat, ale jsou běžnější, než byste si museli představit. Často se jenom při stížnosti vašich zákazníků. V tuto chvíli je to moc pozdě: ovlivnění uživatelé už přecházejí na vaše konkurenty.

V současné době naše algoritmy hledají dobu načítání stránek, dobu odezvy na server a dobu odezvy na závislosti.  

Nemusíte nastavovat žádné prahové hodnoty ani konfigurovat pravidla. Algoritmy strojového učení a dolování dat slouží k detekci neobvyklých vzorů.

![Kliknutím na odkaz v e-mailové výstraze otevřete diagnostickou sestavu v Azure.](./media/proactive-performance-diagnostics/03.png)

* **Zobrazuje čas** zjištěného problému.
* **Co** popisuje:

  * Problém, který byl zjištěn;
  * Charakteristiky sady událostí, které jsme našli, se zobrazilo chování problému.
* Tabulka porovnává nesprávně prováděnou sadu s průměrnou vlastností všech ostatních událostí.

Kliknutím na odkazy otevřete Průzkumníka metrik a vyhledejte příslušné sestavy filtrované podle času a vlastností pomalého nastavování.

Upravte časový rozsah a filtry pro prozkoumání telemetrie.

## <a name="next-steps"></a>Další kroky
Tyto diagnostické nástroje vám pomůžou zkontrolovat telemetrii z vaší aplikace:

* [Profiler](profiler.md) 
* [Snapshot Debugger](./snapshot-debugger.md)
* [Analýzy](../log-query/log-analytics-tutorial.md)
* [Inteligentní Diagnostika Analytics](../log-query/log-query-overview.md)

Inteligentní detekce jsou zcela automatické. Možná byste ale chtěli nastavit ještě nějaké další výstrahy?

* [Ručně nakonfigurované výstrahy metriky](../platform/alerts-log.md)
* [Testy dostupnosti webu](./monitor-web-app-availability.md)