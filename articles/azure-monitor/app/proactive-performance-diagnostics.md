---
title: Inteligentní detekce - anomálie výkonu | Dokumenty společnosti Microsoft
description: Application Insights provádí inteligentní analýzu telemetrie aplikace a varuje vás před potenciálními problémy. Tato funkce nepotřebuje žádné nastavení.
ms.topic: conceptual
ms.date: 05/04/2017
ms.reviewer: antonfr
ms.openlocfilehash: 3d8de08605d3dd693eb74a84a29c2efa6cad669a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671728"
---
# <a name="smart-detection---performance-anomalies"></a>Inteligentní detekce – anomálie výkonu

[Application Insights](../../azure-monitor/app/app-insights-overview.md) automaticky analyzuje výkon vaší webové aplikace a může vás varovat před možnými problémy. Možná si to čtete, protože jste obdrželi jedno z našich inteligentních detekčních oznámení.

Tato funkce nevyžaduje žádné speciální nastavení, kromě konfigurace aplikace pro Application Insights (na [ASP.NET](../../azure-monitor/app/asp-net.md), [Java](../../azure-monitor/app/java-get-started.md)nebo [Node.js](../../azure-monitor/app/nodejs.md)a v [kódu webové stránky).](../../azure-monitor/app/javascript.md) Je aktivní, když vaše aplikace generuje dostatek telemetrie.

## <a name="when-would-i-get-a-smart-detection-notification"></a>Kdy dostanu oznámení o inteligentní detekci?

Application Insights zjistila, že výkon vaší aplikace se snížil jedním z těchto způsobů:

* **Zhoršení doby odezvy** – aplikace začala reagovat na požadavky pomaleji než dříve. Změna může být rychlé, například proto, že došlo k regresi v posledním nasazení. Nebo to mohlo být postupné, možná způsobeno únikem paměti. 
* **Snížení doby trvání závislostí** – aplikace provádí volání rozhraní REST API, databáze nebo jiné závislosti. Závislost reaguje pomaleji než dříve.
* **Pomalý výkon vzor** – vaše aplikace se zdá mít problém s výkonem, který ovlivňuje pouze některé požadavky. Například stránky se načítají pomaleji v jednom typu prohlížeče než jiné; nebo požadavky jsou obsluhovány pomaleji z jednoho konkrétního serveru. V současné době naše algoritmy se dívají na časy načítání stránky, doby odezvy požadavku a doby odezvy závislostí.  

Inteligentní detekce vyžaduje alespoň 8 dní telemetrie na funkční svazek za účelem stanovení základní hodnoty normálního výkonu. Takže poté, co vaše aplikace byla spuštěna pro toto období, bude mít jakýkoli významný problém za následek oznámení.


## <a name="does-my-app-definitely-have-a-problem"></a>Má moje aplikace určitě nějaký problém?

Ne, oznámení neznamená, že vaše aplikace má určitě problém. Je to je jednoduše návrh ohledně něčeho, co byste měli blíže prozkoumat.

## <a name="how-do-i-fix-it"></a>Jak ho mám opravit?

Oznámení obsahují diagnostické informace. Tady je příklad:


![Zde je příklad detekce degradace doby odezvy serveru](media/proactive-performance-diagnostics/server_response_time_degradation.png)

1. **Třídění**. Oznámení ukazuje, kolik uživatelů nebo kolik operací jsou ovlivněny. To vám může pomoci přiřadit prioritu problému.
2. **Obor**. Je problém ovlivňuje veškerý provoz, nebo jen některé stránky? Je omezena na konkrétní prohlížeče nebo umístění? Tyto informace lze získat z oznámení.
3. **Diagnostika:** Často diagnostické informace v oznámení naznačují povahu problému. Například pokud doba odezvy zpomaluje při požadavku rychlost je vysoká, která naznačuje, že váš server nebo závislosti jsou přetíženy. 

    V opačném případě otevřete okno Výkon v Application Insights. Zde najdete data [Profileru.](profiler.md) Pokud jsou vyvolány výjimky, můžete také zkusit [ladicí program snímek](../../azure-monitor/app/snapshot-debugger.md).



## <a name="configure-email-notifications"></a>Konfigurace e-mailových oznámení

Inteligentní zjišťování oznámení jsou povoleny ve výchozím nastavení a odesílány těm, kteří mají [sledování reader](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) a [monitorování přispěvatele](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) přístup k předplatnému, ve kterém se nachází prostředek Application Insights. Pokud to chcete změnit, klikněte buď na **Konfigurovat** v e-mailovém oznámení, nebo otevřete nastavení inteligentní detekce v Application Insights. 
  
  ![Nastavení inteligentní detekce](media/proactive-performance-diagnostics/smart_detection_configuration.png)
  
  * Pomocí odkazu **pro odhlášení** v e-mailu inteligentní detekce můžete přestat dostávat e-mailová oznámení.

E-maily o anomáliích výkonu inteligentních zjišťování jsou omezeny na jeden e-mail za den na prostředek Application Insights. E-mail bude odeslán pouze v případě, že je alespoň jeden nový problém, který byl zjištěn v ten den. Nebudete mít opakování žádné zprávy. 

## <a name="faq"></a>Nejčastější dotazy

* *Takže zaměstnanci společnosti Microsoft se dívají na moje data?*
  * Ne. Služba je zcela automatická. Oznámení dostáváte jen vy. Vaše data jsou [soukromá](../../azure-monitor/app/data-retention-privacy.md).
* *Analyzujete všechna data shromážděná application insights?*
  * V současné době ne. V současné době analyzujeme dobu odezvy požadavku, dobu odezvy závislostí a dobu načítání stránky. Analýza dalších metrik je na našich nevyřízených položkách, které se těšíme.

* Pro jaké typy aplikací tato aplikace funguje?
  * Tato degradace jsou zjištěny v libovolné aplikaci, která generuje příslušnou telemetrii. Pokud jste do webové aplikace nainstalovali Application Insights, budou požadavky a závislosti automaticky sledovány. Ale v back-endové služby nebo jiné aplikace, pokud jste vložili volání [TrackRequest()](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest) nebo [TrackDependency](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency), pak inteligentní detekce bude fungovat stejným způsobem.

* *Je možné vytvořit vlastní pravidla detekce anomálií nebo přizpůsobit stávající pravidla?*

  * Ještě ne, ale můžete:
    * [Nastavte výstrahy,](../../azure-monitor/app/alerts.md) které informují o tom, kdy metrika překročí prahovou hodnotu.
    * [Exportujte telemetrii](../../azure-monitor/app/export-telemetry.md) do [databáze](../../azure-monitor/app/code-sample-export-sql-stream-analytics.md) nebo [do PowerBI](../../azure-monitor/app/export-power-bi.md ), kde ji můžete analyzovat sami.
* *Jak často se analýza provádí?*

  * Denně spouštěme analýzu telemetrie z předchozího dne (celý den v časovém pásmu UTC).
* *Nahrazuje to tedy [upozornění na metriky?](../../azure-monitor/app/alerts.md)*
  * Ne.  Nezavazujeme se k detekci každého chování, které byste mohli považovat za abnormální.


* *Pokud v reakci na oznámení nic neudělám, dostanu připomenutí?*
  * Ne, o každém problému se zobrazí zpráva pouze jednou. Pokud problém přetrvává, bude aktualizován v posuvu inteligentní detekce.
* *Ztratil jsem e-mail. Kde najdu oznámení na portálu?*
  * V přehledu aplikací ve vaší aplikaci klikněte na dlaždici **Inteligentní detekce.** Tam budete moci najít všechna oznámení až do 90 dnů zpět.

## <a name="how-can-i-improve-performance"></a>Jak mohu zlepšit výkon?
Pomalé a neúspěšné odpovědi jsou jednou z největších frustrací pro uživatele webových stránek, jak víte z vlastní zkušenosti. Takže je důležité řešit problémy.

### <a name="triage"></a>Třídění
Za prvé, záleží na tom? Pokud se stránka načítá vždy pomalu, ale pouze 1% uživatelů vašeho webu se na to někdy musí podívat, možná máte důležitější věci, o kterých byste měli přemýšlet. Na druhou stranu, pokud pouze 1% uživatelů otevřít, ale to vyvolává výjimky pokaždé, že by mohlo být vhodné prozkoumat.

Použijte příkaz dopadu (ovlivněné uživatele nebo % provozu) jako obecné vodítko, ale uvědomte si, že to není celý příběh. Shromážděte další důkazy, abyste to potvrdili.

Zvažte parametry problému. Pokud je závislá na zeměpisné poloze, nastavte [testy dostupnosti](../../azure-monitor/app/monitor-web-app-availability.md) včetně této oblasti: v této oblasti mohou být jednoduše problémy se sítí.

### <a name="diagnose-slow-page-loads"></a>Diagnostika pomalého načítání stránek
Kde je problém? Je server pomalu reagovat, je stránka velmi dlouhá, nebo má prohlížeč muset udělat hodně práce, aby ji zobrazit?

Otevřete okno metriky Prohlížeče. Segmentované zobrazení času načítání stránky prohlížeče ukazuje, kam čas probíhá. 

* Pokud **je doba odeslání požadavku** vysoká, server reaguje pomalu nebo je požadavek příspěvkem s velkým množstvím dat. Podívejte se na metriky výkonu prozkoumat doby [odezvy.](../../azure-monitor/app/web-monitor-performance.md#metrics)
* Nastavte [sledování závislostí, abyste zjistili,](../../azure-monitor/app/asp-net-dependencies.md) zda je pomalost způsobena externími službami nebo databází.
* Pokud **převládá příjem odpovědi,** vaše stránka a její závislé části - JavaScript, CSS, obrázky a tak dále (ale ne asynchronně načtená data) jsou dlouhé. Nastavte [test dostupnosti](../../azure-monitor/app/monitor-web-app-availability.md)a nezapomeňte nastavit možnost načtení závislých dílů. Když získáte nějaké výsledky, otevřete podrobnosti o výsledku a rozbalte jej, abyste viděli časy načítání různých souborů.
* Vysoká **doba zpracování klienta** naznačuje, že skripty běží pomalu. Pokud důvod není zřejmý, zvažte přidání nějakého časového kódu a odešlete časy v trackMetric volání.

### <a name="improve-slow-pages"></a>Zlepšení pomalých stránek
K dispozici je web plný rad, jak zlepšit odpovědi na server a časy načítání stránek, takže se nebudeme snažit opakovat to všechno zde. Zde je několik tipů, které jste pravděpodobně již víte o tom, jen aby si myslíte:

* Pomalé načítání z důvodu velkých souborů: Načtěte skripty a další části asynchronně. Použijte sdružování skriptů. Rozdělte hlavní stránku na widgety, které načítají svá data samostatně. Neposílejte prostý starý HTML pro dlouhé tabulky: použijte skript požádat data jako JSON nebo jiný kompaktní formát, pak vyplňte tabulku na místě. Existují skvělé rámce, které vám s tím vším pomohou. (Oni také zahrnují velké skripty, samozřejmě.)
* Pomalé závislosti serveru: Zvažte zeměpisná umístění součástí. Například pokud používáte Azure, ujistěte se, že webový server a databáze jsou ve stejné oblasti. Načítají dotazy více informací, než potřebují? By ukládání do mezipaměti nebo dávkování pomoci?
* Problémy s kapacitou: Podívejte se na metriky odezvy serveru a počty požadavků. Pokud doba odezvy dosáhne neúměrného vrcholu s vrcholy v požadavcích, je pravděpodobné, že vaše servery jsou roztažené.


## <a name="server-response-time-degradation"></a>Snížení doby odezvy serveru

Oznámení o snížení doby odezvy vám řekne:

* Doba odezvy ve srovnání s normální dobou odezvy pro tuto operaci.
* Počet uživatelů jsou ovlivněny.
* Průměrná doba odezvy a 90. 
* Počet požadavků na tuto operaci v den zjištění a 7 dní předtím.
* Korelace mezi degradací v této operaci a degradacemi v souvisejících závislostech. 
* Odkazy, které vám pomohou diagnostikovat problém.
  * Trasování profileru, které vám pomohou zobrazit, kde je čas operace strávený (propojení je k dispozici, pokud byly pro tuto operaci během doby zjišťování shromážděny příklady trasování profileru). 
  * Sestavy výkonu v Průzkumníku metrik, kde můžete pro tuto operaci rozdělit a odkreslovat časový rozsah nebo filtry.
  * Vyhledejte toto volání a zobrazte konkrétní vlastnosti volání.
  * Zprávy o selhání – pokud počet > 1 to znamená, že v této operaci došlo k chybám, které mohly přispět ke snížení výkonu.

## <a name="dependency-duration-degradation"></a>Degradace doby trvání závislosti

Moderní aplikace stále více a více přijmout mikro služby design přístup, který v mnoha případech vede k vysoké spolehlivosti na externí služby. Například, pokud vaše aplikace závisí na některé datové platformy, nebo i když si vytvořit vlastní bot služby budete pravděpodobně relé na některé kognitivní služby poskytovatele, aby vaše roboty komunikovat v lidštější způsoby a některé služby úložiště dat pro robota vytáhnout odpovědi Z.  

Příklad oznámení o snížení závislosti:

![Zde je příklad detekce degradace doby trvání závislostí.](media/proactive-performance-diagnostics/dependency_duration_degradation.png)

Všimněte si, že vám říká:

* Doba trvání ve srovnání s normální dobou odezvy pro tuto operaci
* Počet uživatelů je ovlivněn
* Průměrná doba trvání a 90% trvání této závislosti v den zjištění a 7 dní před
* Počet volání závislosti v den zjištění a 7 dní před
* Odkazy, které vám pomohou diagnostikovat problém
  * Sestavy výkonu v Průzkumníku metrik pro tuto závislost
  * Hledání těchto volání závislostí pro zobrazení vlastností volání
  * Zprávy o selhání – pokud počet > 1 to znamená, že došlo k selhání volání závislostí během období zjišťování, které by mohly přispět k snížení doby trvání. 
  * Otevřete Analytics s dotazy, které počítají tuto dobu trvání závislosti a počet  

## <a name="smart-detection-of-slow-performing-patterns"></a>Inteligentní detekce pomalu fungujících vzorů 

Application Insights vyhledá problémy s výkonem, které mohou ovlivnit jenom část uživatelů nebo v některých případech ovlivní jenom uživatele. Například oznámení o načtení stránek je pomalejší na jednom typu prohlížeče než u jiných typů prohlížečů, nebo pokud jsou požadavky obsluhovány pomaleji z určitého serveru. Může také zjistit problémy spojené s kombinacemi vlastností, jako je například pomalé načítání stránek v jedné zeměpisné oblasti pro klienty používající konkrétní operační systém.  

Anomálie, jako jsou tyto, jsou velmi těžké odhalit pouhým zkontrolováním dat, ale jsou častější, než si myslíte. Často se objevují pouze tehdy, když si vaši zákazníci stěžují. Do té doby je příliš pozdě: postižení uživatelé již přecházejí na vaše konkurenty!

V současné době naše algoritmy se dívají na časy načítání stránky, doby odezvy požadavku na serveru a doby odezvy závislostí.  

Nemusíte nastavovat žádné prahové hodnoty ani konfigurovat pravidla. Algoritmy strojového učení a dolování dat se používají k detekci abnormálních vzorů.

![V e-mailové výstraze klikněte na odkaz a otevřete diagnostickou sestavu v Azure.](./media/proactive-performance-diagnostics/03.png)

* **Když** ukazuje čas byl zjištěn problém.
* **Co** popisuje:

  * Problém, který byl zjištěn;
  * Charakteristiky sady událostí, které jsme našli, zobrazují problémové chování.
* Tabulka porovnává sadu s nevýkonným výkonem s průměrným chováním všech ostatních událostí.

Kliknutím na odkazy otevřete Průzkumník metriky a Vyhledejte v příslušných sestavách filtrovaných podle času a vlastností sady s pomalým prováděním.

Upravte časový rozsah a filtry a prozkoumejte telemetrii.

## <a name="next-steps"></a>Další kroky
Tyto diagnostické nástroje vám pomohou zkontrolovat telemetrii z vaší aplikace:

* [Profiler](profiler.md) 
* [Ladicí program snímků](../../azure-monitor/app/snapshot-debugger.md)
* [Analýza](../../azure-monitor/log-query/get-started-portal.md)
* [Analytics inteligentní diagnostika](../../azure-monitor/app/analytics.md)

Inteligentní detekce jsou zcela automatické. Ale možná byste chtěli nastavit nějaké další upozornění?

* [Ručně nakonfigurovaná upozornění na metriky](../../azure-monitor/app/alerts.md)
* [Testy dostupnosti webu](../../azure-monitor/app/monitor-web-app-availability.md)
