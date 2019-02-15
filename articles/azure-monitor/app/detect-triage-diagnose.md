---
title: Přehled Azure Application Insights pro DevOps | Dokumentace Microsoftu
description: Zjistěte, jak používat Application Insights v prostředí DevOps.
author: mrbullwinkle
services: application-insights
documentationcenter: ''
manager: carmonm
ms.assetid: 6ccab5d4-34c4-4303-9d3b-a0f1b11e6651
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.custom: mvc
ms.topic: overview
ms.date: 09/06/2018
ms.author: mbullwin
ms.openlocfilehash: 91814579675882ba969f5903ebeddcebbcbd6616
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2019
ms.locfileid: "56301532"
---
# <a name="overview-of-application-insights-for-devops"></a>Přehled Application Insights pro DevOps

Pomocí [Application Insights](../../azure-monitor/app/app-insights-overview.md) můžete rychle zjistit, jak vaše aplikace funguje a jak se používá, když je v provozu. Pokud dojde k problému, upozorní vás na něj a pomůže vám posoudit dopad a určit příčinu.

Tady je zpráva od týmu, který vyvíjí webové aplikace:

* *„Před pár dny jsme nasadili menší opravu hotfix. Neprovedli jsme rozsáhlý test průchodu, ale bohužel se s datovou částí sloučila nějaká neočekávaná změna, která způsobila nekompatibilitu mezi front-endem a back-endem. Okamžitě došlo k nárůstu počtu výjimek serveru, aktivovalo se naše upozornění, a my jsme se dozvěděli o nastalé situaci. Stačilo několik kliknutí na portálu Application Insights a získali jsme dostatek informací ze zásobníků volání výjimek, abychom problém mohli zúžit. Okamžitě jsme vrátili změny a omezili škody. Díky Application Insights byla tato část cyklu DevOps velmi snadná a efektivní.“*

V tomto článku sledujeme tým v bance Fabrikam, který vyvíjí online bankovní systém (OBS), abychom si ukázali, jak pomocí Application Insights rychle reagují na zákazníky a provádějí aktualizace.  

Tým pracuje v cyklu DevOps znázorněném na následujícím obrázku:

![Cyklus DevOps](./media/detect-triage-diagnose/00-devcycle.png)

Požadavky proudí do vývojového backlogu (seznam úkolů). Pracují v krátkých sprintech, ve kterých se často dokončí fungující software – obvykle v podobě vylepšení a rozšíření existující aplikace. Živá aplikace se často aktualizuje o nové funkce. Když je aplikace v provozu, tým pomocí Application Insights monitoruje její výkon a využití. Tato data APM proudí zpět do vývojového backlogu.

Tým pomocí Application Insights monitoruje v živé webové aplikaci zejména:

* Výkon. Chtějí zjistit, jak se s počtem požadavků mění doba odezvy, kolik procesorových, síťových, diskových a dalších prostředků se využívá, jaký kód aplikace snižuje výkon a kde jsou kritické body.
* Selhání. Pokud dojde k výjimkám nebo neúspěšným požadavkům, nebo pokud je čítač výkonu mimo komfortní rozsah, tým to potřebuje rychle vědět, aby mohl reagovat.
* Využití. Při každém vydání nové funkce chce tým vědět, do jaké míry se využívá, a jestli s ní uživatelé nemají nějaké problémy.

Zaměřme se na část cyklu se zpětnou vazbou:

![Zjišťování – určení priorit podle dostupnosti zdrojů – diagnostika](./media/detect-triage-diagnose/01-pipe1.png)

## <a name="detect-poor-availability"></a>Zjišťování špatné dostupnosti
Marcela Markova je vedoucí vývojář v týmu OBS a vede monitorování online výkonu. Nastaví několik [testů dostupnosti](../../azure-monitor/app/monitor-web-app-availability.md):

* Test jediné adresy URL hlavní vstupní stránky aplikace http://fabrikambank.com/onlinebanking/. Marcela nastaví kritéria na kód HTTP 200 a text „Welcome!“. Pokud tento test selže, existuje nějaký závažný problém se sítí nebo servery, případně se může jednat o problém s nasazením. (Nebo někdo na stránce změnil zprávu Welcome!, aniž by ji o tom informoval.)
* Hlubší test s více kroky, který se přihlásí, získá výpis aktuálního účtu a na každé stránce zkontroluje několik klíčových podrobností. Tento test ověřuje fungování odkazu na databázi účtů. Marcela používá fiktivní ID zákazníka – několik se jich udržuje pro účely testování.

Díky těmto nastaveným testům si Marcela může být jistá, že se tým rychle dozví o jakémkoli výpadku.  

Selhání se zobrazují jako červené tečky v grafu webového testu:

![Zobrazení webových testů, které proběhly v předchozím období](./media/detect-triage-diagnose/04-webtests.png)

Ale co je důležitější, upozornění na jakékoli selhání se odešle e-mailem vývojovému týmu. Tímto způsobem se o tom dozví dříve, než téměř všichni zákazníci.

## <a name="monitor-performance"></a>Monitorování výkonu
Na stránce Přehled v Application Insights je graf zobrazující různé [klíčové metriky](../../azure-monitor/app/web-monitor-performance.md).

![Snímek obrazovky s přehledem grafů vývoje klíčových ukazatelů výkonu](./media/detect-triage-diagnose/overview-graphs.png)

Čas načtení stránky v prohlížeči se odvozuje z telemetrických dat odesílaných přímo z webových stránek. Dobra odezvy serveru, počet požadavků serveru a počet neúspěšných požadavků se měří na webovém serveru a odtud se odesílají do Application Insights.

Marcela je mírně znepokojená grafem odezvy serveru. Tento graf ukazuje průměrnou dobu mezi přijetím požadavku HTTP z prohlížeče uživatele na serveru a vrácením odpovědi. Není neobvyklé, že tento graf kolísá, protože zatížení systému se mění. V tomto případě se však zdá, že existuje korelace mezi malými nárůsty počtu požadavků a velkými nárůsty doby odezvy. To by mohlo znamenat, že systém funguje na hranici svých omezení.

Marcela otevře grafy serverů:

![Různé metriky](./media/detect-triage-diagnose/002-servers.png)

Tady se nezdá, že by něco naznačovalo omezení prostředků, takže možná jsou hrboly v grafech odezvy serveru pouze náhodné.

## <a name="set-alerts-to-meet-goals"></a>Nastavení upozornění pro splnění cílů
Marcela by nicméně stále chtěla dohlížet na dobu odezvy. Pokud dojde k příliš velkému navýšení, chce se o tom okamžitě dozvědět.

Proto nastaví [upozornění](../../azure-monitor/app/metrics-explorer.md) na vyšší dobu odezvy, než je obvyklá prahová hodnota. Díky tomu se může spolehnout, že pokud bude doba odezvy vysoká, dozví se o tom.

![Okno přidání upozornění](./media/detect-triage-diagnose/07-alerts.png)

Upozornění je možné nastavit na celou řadu dalších metrik. Můžete například dostávat e-maily v případě nárůstu počtu výjimek, snížení velikosti dostupné paměti nebo když požadavky klientů dosáhnou špičky.

## <a name="stay-informed-with-smart-detection-alerts"></a>Udržování přehledu pomocí upozornění inteligentního zjišťování
Další den skutečně přijde e-mail z Application Insights. Když ho ale Marcela otevře, zjistí, že se nejedná o upozornění na dobu odezvy, které nastavila. Místo toho e-mail obsahuje sdělení o náhlém nárůstu počtu neúspěšných požadavků – to znamená požadavků, které vrátily kód selhání 500 nebo vyšší.

Při neúspěšných požadavcích se uživatelům zobrazí chyba – ta obvykle následuje po výjimce vyvolané v kódu. Může se jim zobrazit zpráva „Sorry we couldn't update your details right now“ (Omlouváme se, právě teď nemůžete aktualizovat vaše podrobnosti). Nebo, a to je absolutně nejhorší, webový server na obrazovce uživatele zobrazí výpis zásobníku.

Toto upozornění je překvapením, protože když se Marcela koukala naposledy, počet neúspěšných požadavků byl povzbudivě nízký. Na zaneprázdněném serveru se dá očekávat malý počet selhání.

Trochu překvapením pro ni také bylo, že toto upozornění nemusela konfigurovat. Application Insights zahrnuje inteligentní zjišťování. To se automaticky přizpůsobuje obvyklému vzoru selhání vaší aplikace a „zvyká si“ na selhání na konkrétní stránce, v případě velkého zatížení nebo v souvislosti s dalšími metrikami. Upozorňuje pouze na nárůst nad rámec očekávaných hodnot.

![e-mail proaktivní diagnostiky](./media/detect-triage-diagnose/21.png)

Tento e-mail je velmi užitečný. Není to pouhé upozornění. Provádí také určení priorit podle dostupnosti zdrojů a hodně diagnostické práce.

Ukazuje, kolika uživatelů a kterých webových stránek a operací se problém týká. Marcela se může rozhodnout, jestli potřebuje, aby na tom začal pracovat celý tým v rámci cvičení, nebo jestli se to dá ignorovat až do příštího týdne.

E-mail také ukazuje, že došlo ke konkrétní výjimce a, co je ještě zajímavější, že selhání souvisí s neúspěšnými voláními konkrétní databáze. Tím se vysvětluje, proč náhle došlo k selhání, přestože Marcelin tým v nedávné době nenasazoval žádné aktualizace.

Marcela na základě tohoto e-mailu zavolá vedoucímu databázového týmu. Zjistí, že během poslední půlhodiny vydali opravu hotfix a pravděpodobně došlo k menší změně schématu.

Takže řešení problému už je na cestě, a to dokonce před zkoumáním protokolů a během 15 minut od jeho vzniku. Nicméně Marcela klikne na odkaz a otevře Application Insights. Otevře se přímo se zobrazením neúspěšného požadavku a Marcela vidí neúspěšné volání databáze v přidruženém seznamu volání závislostí.

![neúspěšný požadavek](./media/detect-triage-diagnose/23.png)

## <a name="detect-exceptions"></a>Zjišťování výjimek
Stačí nepatrné nastavení a [výjimky](../../azure-monitor/app/asp-net-exceptions.md) se hlásí do Application Insights automaticky. Můžete je zachycovat také explicitně vložením volání metody [TrackException()](../../azure-monitor/app/api-custom-events-metrics.md#trackexception) do kódu:  

    var telemetry = new TelemetryClient();
    ...
    try
    { ...
    }
    catch (Exception ex)
    {
       // Set up some properties:
       var properties = new Dictionary <string, string>
         {{"Game", currentGame.Name}};

       var measurements = new Dictionary <string, double>
         {{"Users", currentGame.Users.Count}};

       // Send the exception telemetry:
       telemetry.TrackException(ex, properties, measurements);
    }


Tým banky Fabrikam vyvinul postup spočívající v tom, že se telemetrie vždy odesílá jako výjimka, pokud nejde o zřejmé obnovení.  

Jejich strategie je ve skutečnosti ještě širší: Odesílají telemetrii ve všech případech, kde je zákazník frustrovaní něčím, co chcete provést, ať už to odpovídá výjimce v kódu nebo ne. Můžou sledovat i události, kdy například externí systém mezibankovních přenosů vrátí zprávu o nemožnosti dokončení transakce z nějakých provozních důvodů (bez chyby na straně zákazníka).

    var successCode = AttemptTransfer(transferAmount, ...);
    if (successCode < 0)
    {
       var properties = new Dictionary <string, string>
            {{ "Code", returnCode, ... }};
       var measurements = new Dictionary <string, double>
         {{"Value", transferAmount}};
       telemetry.TrackEvent("transfer failed", properties, measurements);
    }

K hlášení výjimek se používá TrackException, protože odesílá kopii zásobníku. K hlášení ostatních událostí se používá TrackEvent. Můžete zachytávat jakékoli vlastnosti, které můžou být užitečně pro účely diagnostiky.

Výjimky a události se zobrazují v okně [Diagnostické vyhledávání](diagnostic-search.md). Můžete je blíže prozkoumat a zobrazit další vlastnosti a trasování zásobníku.

![Použití filtrů v diagnostickém vyhledávání k zobrazení konkrétních typů dat](./media/detect-triage-diagnose/appinsights-333facets.png)


## <a name="monitor-proactively"></a>Proaktivní monitorování
Marcela jen tak nesedí a nečeká na upozornění. Krátce po každém nasazení si prohlédne [dobu odezvy](../../azure-monitor/app/web-monitor-performance.md), a to jak celkové číslo a tabulku nejpomalejších požadavků, tak i počty výjimek.  

![Graf doby odezvy a mřížka doby odezvy serveru](./media/detect-triage-diagnose/response-time.png)

Marcela může posoudit vliv každého nasazení na výkon, obvykle porovnáním s předchozím týdnem. Pokud dojde k náhlému zhoršení, upozorní na to příslušné vývojáře.

## <a name="triage-issues"></a>Problémy s určováním priorit podle dostupnosti zdrojů
Určení priorit podle dostupnosti zdrojů, tedy posouzení závažnosti a rozsahu problému, je prvním krokem po zjištění. Měli bychom tým svolat o půlnoci? Nebo s tím můžeme počkat na další příhodnou mezeru v backlogu? S určováním priorit podle dostupnosti zdrojů souvisí několik klíčových otázek.

Jak často se to děje? Z grafů v okně Přehled můžete získat určitou představu o problému. Například aplikace Fabrikam jednu noc vygenerovala čtyři upozornění webových testů. Když se tým na graf podíval ráno, skutečně obsahoval několik červených teček, přestože většina testů stále byla zobrazena zeleně. Při bližším pohledu na graf dostupnosti bylo zřejmé, že všechny tyto přerušované problémy pocházely z jednoho testovacího umístění. Toto byl samozřejmě problém sítě, který ovlivnil pouze jednu trasu, a pravděpodobně by se sám opravil.  

Naopak výrazný a stabilní růst v grafu počtu výjimek nebo doby odezvy je samozřejmě důvodem k panice.

Užitečnou taktikou určení priorit podle dostupnosti zdrojů je: zkuste si to sami. Pokud narazíte na stejný problém, víte, že je skutečný.

Jak velká část uživatelů je ovlivněná? Přibližnou odpověď získáte, když vydělíte míru selhání počtem relací.

Pokud existují pomalé odezvy, porovnejte na každé stránce tabulku požadavků s nejpomalejší odezvou s frekvencí použití.

Jak důležitý je blokovaný scénář? Jedná se o funkční problém blokující konkrétní uživatelský scénář a záleží na tom hodně? Pokud zákazníci nemůžou platit své účty, je to vážné; pokud si nemůžou změnit předvolby barvy obrazovky, pravděpodobně to může počkat. Pomocí podrobností o události nebo výjimce nebo identity pomalé stránky zjistíte, kde mají zákazníci potíže.

## <a name="diagnose-issues"></a>Diagnostika problémů
Diagnostika není to samé jako ladění. Než začnete s trasováním kódem, měli byste mít přibližnou představu o tom, proč, kde a kdy k problému dochází.

**Kdy k problému dochází?** Historické zobrazení, které poskytují grafy událostí a metrik, usnadňuje korelaci důsledků s možnými příčinami. Pokud jsou v době odezvy nebo frekvenci výjimek přerušované vrcholy, podívejte se na počet požadavků: pokud dosahuje vrcholů ve stejnou dobu, vypadá to na problém s prostředky. Je potřeba přiřadit další procesor nebo paměť? Nebo zátěž nezvládá nějaká závislost?

**Je problém u nás?**  Pokud dochází k náhlému poklesu výkonu u určitého typu požadavku, například když uživatel chce výpis z účtu, pak je možná problém v externím subsystému, a ne ve vaší webové aplikaci. V Průzkumníku metrik vyberte míru chyb závislostí a míry doby trvání a porovnejte jejich historie za několik posledních hodin nebo dnů se zjištěným problémem. Pokud zjistíte korelaci změn, pak je problém pravděpodobně v externím subsystému.  


![Grafy chyb závislostí a doby trvání volání závislostí](./media/detect-triage-diagnose/11-dependencies.png)

Některé problémy s pomalými závislostmi jsou problémy geografické polohy. Banka Fabrikam používá virtuální počítače Azure a zjistilo se, že nedopatřením umístila webový server a server účtů do jiných zemí. Migrací jednoho z nich došlo k výraznému zlepšení.

**Co jsme udělali?** Pokud se nezdá, že by problém byl v závislosti, a pokud závislost neexistovala vždy, problém je pravděpodobně způsobený nedávnou změnou. Historická perspektiva, kterou poskytují grafy metrik a událostí, usnadňuje korelaci jakýchkoli náhlých změn s nasazeními. Tím se může zúžit oblast hledání problému. Pokud chcete identifikovat, které řádky v kódu aplikace zpomalily výkon, povolte Application Insights Profiler. Další informace najdete v tématu [Profilace živých webových aplikací Azure pomocí Application Insights](./../../azure-monitor/app/profiler.md). Po povolení profileru se zobrazí trasování podobné následujícímu. V tomto příkladu si snadno můžete všimnout, že problém způsobila metoda *GetStorageTableData*.  

![Trasování nástroje Application Insights Profiler](./media/detect-triage-diagnose/AppInsightsProfiler.png)

**Co se děje?** K některým problémům dochází jenom zřídka a může být složité je vysledovat při offline testování. Vše, co můžeme udělat, je pokusit se zachytit chybu, když k ní dojde za provozu. Můžete si prohlédnout výpisy zásobníku v sestavách výjimek. Kromě toho můžete psát volání trasování pomocí svého oblíbeného protokolovacího rozhraní nebo s použitím metod TrackTrace() nebo TrackEvent().  

Ve společnosti Fabrikam přerušovaně docházelo k problému s přenosy mezi účty, ale pouze u určitých typů účtů. Za účelem lepšího pochopení, co se děje, vložili do kódu volání TrackTrace() jako klíčové body a ke každému volání připojili jako vlastnost typ účtu. Tím umožnil snadné vyfiltrování pouze těchto trasování v diagnostickém vyhledávání. K voláním trasování připojili také hodnoty parametrů jako vlastnosti a míry.

## <a name="respond-to-discovered-issues"></a>Reakce na zjištěné problémy
Jakmile problém diagnostikujete, můžete vytvořit plán k jeho opravení. Možná budete muset vrátit nedávnou změnu nebo možná bude stačit ji prostě opravit. Po dokončení opravy vám Application Insights sdělí, jestli jste byli úspěšní.  

Vývojový tým banky Fabrikam přistupuje k měření výkonu strukturovaněji než dříve, kdy nepoužíval Application Insights.

* Nastavují cíle výkonu z hlediska konkrétních měřítek na stránce Přehled služby Application Insights.
* Od samého začátku zahrnují do návrhu aplikace i měřítka výkonu, například metriky měřící postup uživatelů trychtýři.  

## <a name="monitor-user-activity"></a>Monitorování aktivity uživatelů
Když je doba odezvy konzistentně dobrá a s malým množstvím výjimek, může se vývojový tým přesunout k použitelnosti. Můžou se zamyslet nad tím, jak vylepšit uživatelské prostředí a jak umožnit více uživatelům dosáhnout požadovaných cílů.

Application Insights je také možné použít ke zjištění, co uživatelé s aplikací dělají. Jakmile aplikace plynule běží, tým by chtěl zjistit, které funkce jsou nejoblíbenější, co mají uživatelé rádi nebo s čím mají potíže, a jak často se vracejí. To jim pomůže určit prioritu další práce. Zároveň můžou plánovat měření úspěchu jednotlivých funkcí jako součást vývojového cyklu.

Například cesta typického uživatele webem má jasnou podobu trychtýře. Mnoho zákazníků si prohlédne sazby různých typů půjček. Méně jich vyplní poptávkový formulář. Z těch, kteří získají nabídku, si jich jenom pár vezme půjčku.

![Počty zobrazení stránek](./media/detect-triage-diagnose/funnel.png)

Na základě informací o tom, kde předčasně odchází největší počet zákazníků, můžou podniky vypracovat plán, aby trychtýřem propadlo více uživatelů. V některých případech může být chyba v uživatelském rozhraní (UX) – například je obtížné najít tlačítko Další nebo jsou nejasné pokyny. Pro předčasný odchod však pravděpodobně existují významnější obchodní důvody – možná jsou úrokové sazby příliš vysoké.

Ať jsou důvody jakékoli, data pomáhají týmu porozumět chování uživatelů. Je možné vložit další sledovací volání a získat tak podrobnější přehled. Metodu TrackEvent() je možné použít k počítání jakýchkoli akcí uživatelů, od úplných podrobností o kliknutích na jednotlivá tlačítka až po důležité úspěchy, jako je splacení půjčky.

Tým si postupně zvyká na to, že má k dispozici informace o aktivitách uživatelů. V současné době vždy, když navrhují novou funkci, vypracují plán na získání zpětné vazby o jejím využití. Od samého začátku zahrnují do návrhu funkce i sledovací volání. Zpětnou vazbu využívají v každém vývojovém cyklu ke zlepšení funkce.

[Další informace o sledování využití](../../azure-monitor/app/usage-overview.md).

## <a name="apply-the-devops-cycle"></a>Použití cyklu DevOps
Toto byla ukázka jednoho týmu, který pomocí Application Insights nejen řeší jednotlivé problémy, ale také zlepšuje životní cyklus vývoje. Doufám, že jste získali představu o tom, jak vám Application Insights může pomoct se správou výkonu vašich vlastních aplikací.

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Další postup
V závislosti na vlastnostech vaší aplikace můžete začít několika způsoby. Vyberte si, co vám nejvíce vyhovuje:

* [Webová aplikace ASP.NET](../../azure-monitor/app/asp-net.md)
* [Webová aplikace Java](../../azure-monitor/app/java-get-started.md)
* [Webová aplikace Node.js](../../azure-monitor/app/nodejs.md)
* Již nasazené aplikace hostované na [IIS](../../azure-monitor/app/monitor-web-app-availability.md), [platformě Java EE určený](../../azure-monitor/app/java-live.md), nebo [Azure](../../azure-monitor/app/app-insights-overview.md).
* [Webové stránky](../../azure-monitor/app/javascript.md) – Samostatná webová stránka nebo obyčejná webová stránka – tuto možnost můžete použít samotnou nebo společně s jakoukoli z možností serveru.
* [Testy dostupnosti](../../azure-monitor/app/monitor-web-app-availability.md) pro účely testování aplikace z veřejného internetu.
