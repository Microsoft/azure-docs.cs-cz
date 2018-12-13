---
title: Inteligentní zjišťování – anomálie výkonu | Dokumentace Microsoftu
description: Application Insights provádí inteligentní analýzy telemetrie vaší aplikace a upozorní vás na potenciální problémy. Tato funkce vyžaduje žádné nastavení.
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: 6acd41b9-fbf0-45b8-b83b-117e19062dd2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/04/2017
ms.reviewer: antonfr
ms.author: mbullwin
ms.openlocfilehash: b9428e4451ebef921907809b1250238bf084706d
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52864957"
---
# <a name="smart-detection---performance-anomalies"></a>Inteligentní zjišťování – anomálie výkonu

[Application Insights](app-insights-overview.md) automaticky analyzuje výkon svojí webové aplikace a může vás upozorní na potenciální problémy. Vám může být čtení to vzhledem k tomu, že jste nedostali některou z našich upozornění inteligentního zjišťování.

Tato funkce vyžaduje žádné speciální instalační program, než je konfigurace aplikace pro službu Application Insights (na [ASP.NET](app-insights-asp-net.md), [Java](app-insights-java-get-started.md), nebo [Node.js](app-insights-nodejs.md)a v [webové stránky kód](app-insights-javascript.md)). Je aktivní, když vaše aplikace generuje dost telemetrických dat.

## <a name="when-would-i-get-a-smart-detection-notification"></a>Při obdržení oznámení inteligentního zjišťování

Application Insights zjistil, že výkon vaší aplikace má snížený výkon v jednom z těchto způsobů:

* **Zhoršení doby odezvy** -aplikace byla spuštěna reagování na žádosti pomaleji než dřív. Tato změna byla pravděpodobně rychlé, například protože Regrese ve vašem nasazení nejnovější. Nebo by mohlo být postupné, možná způsobené nevracení paměti. 
* **Snížení doby trvání závislosti** – vaše aplikace provádí volání rozhraní REST API, databáze nebo další závislosti. Závislost odpovídá pomaleji než dřív.
* **Nízký výkon vzor** – aplikace se zobrazí mít problému s výkonem, který ovlivňuje pouze některé požadavky. Například se stránky načítají pomaleji na jeden typ prohlížeče než jiné; nebo požadavky jsou obsluhovány pomaleji z jednoho konkrétního serveru. V současné době naše algoritmy podívejte se na dobu načítání stránek, dobu odezvy požadavku a doby odezvy závislostí.  

Inteligentní zjišťování vyžaduje minimálně 8 dní telemetrických dat na svazku funkční stanovení základní úrovně běžným výkonem. Ano za běhu aplikace pro toto období jakýkoli významný problém způsobí oznámení.


## <a name="does-my-app-definitely-have-a-problem"></a>Moje aplikace jednoznačně má problém?

Ne, oznámení neznamená, že vaše aplikace jednoznačně došlo k problému. Je jednoduše návrh o něco, že kdy je vhodné podívat se na informace pečlivě.

## <a name="how-do-i-fix-it"></a>Jak ho mám opravit?

Oznámení zahrnují diagnostické informace. Tady je příklad:


![Tady je příklad detekce zhoršení doby odezvy serveru](media/app-insights-proactive-performance-diagnostics/server_response_time_degradation.png)

1. **Třídění**. Oznámení se dozvíte, jak mnoho operací se vliv na jeden nebo kolik uživatelů. Můžete přiřadit prioritu problému.
2. **Obor**. Tento problém ovlivňuje veškerý provoz, nebo jen některé stránky? Je omezen na konkrétní prohlížeče nebo umístění? Tyto informace můžete získat z oznámení.
3. **Diagnostika**. Diagnostické informace v oznámení často, bude navrhovat povaze problému. Například pokud doba odezvy zpomalí, když je vysoká frekvence požadavků, který naznačuje, že server nebo závislosti jsou přetížené. 

    V opačném případě otevřete okno výkon ve službě Application Insights. Tam najdete [Profiler](app-insights-profiler.md) data. Pokud jsou výjimky vyvolány, můžete také zkusit [snapshot debuggeru](app-insights-snapshot-debugger.md).



## <a name="configure-email-notifications"></a>Konfigurace e-mailových oznámení

Inteligentní detekce oznámení jsou ve výchozím nastavení povolené a odeslat na ty, kteří mají [vlastníci, přispěvatelé a čtenáři přístup k prostředku Application Insights](app-insights-resources-roles-access-control.md). Chcete-li toto nastavení změnit, **konfigurovat** v e-mailové oznámení, nebo otevřete nastavení inteligentního zjišťování ve službě Application Insights. 
  
  ![Nastavení inteligentního zjišťování](media/app-insights-proactive-performance-diagnostics/smart_detection_configuration.png)
  
  * Můžete použít **zrušit odběr** odkaz v e-mailu inteligentního zjišťování k ukončení přijímání oznámení e-mailu.

E-mailů o inteligentní detekce anomálie výkonu jsou omezené na jeden e-mailu za den pro každý prostředek Application Insights. E-mail bude odeslán, pouze v případě, že existuje alespoň jeden nový problém, který byl zjištěn v daný den. Nezískáte opakování žádné zprávy. 

## <a name="faq"></a>Nejčastější dotazy

* *Ano zaměstnanci Microsoftu podívejte se na moje data?*
  * Ne. Tato služba je plně automatická. Pouze dostanete oznámení. Vaše data jsou [privátní](app-insights-data-retention-privacy.md).
* *Analýza všech dat shromážděných službou Application Insights?*
  * Není v současné době. V současné době analyzujeme požadavku, že doba odezvy, závislosti, doby odezvy a o načítání stránek čas. Analýza další metriky je v backlogu a Těšíme se.

* Jaké typy aplikací to funguje?
  * Zjišťují se tyto snížení výkonnosti v jakékoli aplikaci, která generuje telemetrická data odpovídající. Pokud jste nenainstalovali Application Insights ve webové aplikaci, pak požadavky a závislosti jsou automaticky sleduje. Ale v back-endovým službám nebo jiných aplikací, je-li vložit volání [TrackRequest()](app-insights-api-custom-events-metrics.md#trackrequest) nebo [TrackDependency](app-insights-api-custom-events-metrics.md#trackdependency), inteligentní zjišťování bude fungovat stejným způsobem.

* *Můžete vytvořit vlastní anomálií pravidla zjišťování nebo upravit stávající pravidla?*

  * Dosud ale můžete:
    * [Nastavení výstrah](app-insights-alerts.md) , který říct, pokud metrika překročí mezní hodnotu.
    * [Export telemetrie](app-insights-export-telemetry.md) k [databáze](app-insights-code-sample-export-sql-stream-analytics.md) nebo [do PowerBI](app-insights-export-power-bi.md), kde je můžete analyzovat sami.
* *Jak často se provádí analýzu?*

  * Zajišťuje každodenní provoz analýzy každý den na telemetrická data z předchozího dne (celý den v časovém pásmu UTC).
* *Postupy zločinců se nahradit tuto [upozornění na metriku](app-insights-alerts.md)?*
  * Ne.  Jsme není potvrzení se zjišťováním každý chování, které můžete zvážit neobvyklé.


* *Pokud se mi nic nedělají v reakci na oznámení, mi poskytne připomenutí?*
  * Ne, dostanete zprávu o jednotlivých problémů pouze jednou. Pokud se problém nevyřeší, budou aktualizovány v inteligentní zjišťování kanálu okno.
* *Mohu ztratit e-mailu. Kde najdu oznámení na portálu?*
  * V přehledu nástroje Application Insights vaší aplikace, klikněte na tlačítko **inteligentního zjišťování** dlaždici. Existuje budete moct najít všechna oznámení až 90 dnů zpět.

## <a name="how-can-i-improve-performance"></a>Jak můžu vylepšit výkon?
Pomalé a neúspěšných odpovědí jsou jedním z největších frustrations pro webovou stránku uživatele, jak už víte, své vlastní prostředí. Je tedy důležité pro řešení problémů.

### <a name="triage"></a>Třídění
Nejprve je důležité? Pokud je vždy pomalé načítání stránky, ale pouze 1 % uživatelů daného webu třeba podívat na to, možná bude nutné důležité věci v něm můžete vnímat. Na druhé straně Pokud pouze 1 % uživatelů, otevřete ho ale pokaždé, když se vyvolá výjimky, která může být vhodné prošetřit.

Jako obecné vodítko použít příkaz dopad (ovlivnění uživatelé nebo % provoz), ale mějte na paměti, že není kompletní představu. Další důkazy pro potvrzení.

Vezměte v úvahu parametry problém. Pokud je závislé na zeměpisné oblasti, nastavit [testy dostupnosti](app-insights-monitor-web-app-availability.md) včetně tuto oblast: existuje může být jednoduše problémy se sítí v této oblasti.

### <a name="diagnose-slow-page-loads"></a>Diagnostika pomalé načítání stránek
V čem je problém? Server je pomalý, je velmi dlouhá stránky nebo udělat spoustu práce zobrazíte ho nemá v prohlížeči?

Otevřete okno prohlížeče metriky. Segmentované zobrazení prohlížeče stránka zatížení čas ukazuje, kam čas. 

* Pokud **odeslat doba požadavku** je vysoké, buď server reaguje pomalu nebo žádosti je post s velkým množstvím dat. Podívejte se na [metriky výkonu](app-insights-web-monitor-performance.md#metrics) k prozkoumání doby odezvy.
* Nastavit [sledování závislostí](app-insights-asp-net-dependencies.md) Přesvědčte se, zda pomalost je z důvodu externích služeb nebo databáze.
* Pokud **přijetí odpovědi** převládá, stránce a jeho závislé součásti – JavaScript, CSS, obrázky a tak dále (ale ne asynchronně načtená data) jsou dlouhé. Nastavení [test dostupnosti](app-insights-monitor-web-app-availability.md)a nezapomeňte nastavit možnost načíst závislé součásti. Pokud jste získali nějaké výsledky, Otevřít podrobnosti výsledku a rozbalte ho a podívejte se dobu načítání různých souborů.
* Vysoká **doba zpracování klienta** navrhuje skripty běží pomalu. Pokud z důvodu není zřejmé, zvažte přidání některých časování kód a odeslat časy v trackMetric volání.

### <a name="improve-slow-pages"></a>Zlepšení pomalé stránky
Existuje webového plné poradit se správným určením zlepšení odezvy serveru a časy načtení stránek, proto jsme se nebude pokoušet zopakovat to všechno tady. Tady je několik tipů, které pravděpodobně už víte o, stačí, abyste mohli uvažujete:

* Pomalé načítání kvůli velké soubory: načtení skriptů a jiných částí asynchronní. Pomocí sdružování skriptů. Hlavní stránka přerušit pomůcky, který se načítá svá data samostatně. Neposílat prostý starý kód HTML pro dlouhé tabulky: použít skript, který žádost o data jako JSON nebo jiných kompaktním formátu a vyplnění tabulky na místě. Nejsou k dispozici skvělý platformy, které usnadní to vše. (Také má za následek velký objem skripty, samozřejmě.)
* Zpomalit závislosti na serveru: Zvažte geografické umístění součásti. Například pokud používáte Azure, ujistěte se, že webový server a databáze jsou ve stejné oblasti. Dotazy načítají více informací, než budou potřebovat? By ukládání do mezipaměti nebo dávkování nápovědu?
* Kapacity problémy: Podívejte se na server metrik doby odezvy a počty žádostí. Pokud doby odezvy nepřiměřeně špičky poptávek po prostředcích počty žádostí, je pravděpodobné, že jsou vaše servery roztažená.


## <a name="server-response-time-degradation"></a>Zhoršení doby odezvy serveru

Oznámení snížení času odezvy dozvíte:

* Doba odezvy ve srovnání s normální odezvu pro tuto operaci.
* Kolik uživatelů se TOP týká.
* Průměrná doba odezvy a 90. percentil doba odezvy pro tuto operaci zjišťování a 7 dní před dnem. 
* Počet žádostí této operace den detekce a 7 dní před.
* Korelace mezi pokles tuto operaci a snížení výkonnosti v související závislosti. 
* Obsahuje odkazy na při diagnostice problému.
  * Profiler trasování můžete zobrazit, kde byl stráven čas operace (odkaz je k dispozici, pokud Profiler trasování příklady, které byly shromážděny pro tuto operaci v období zjišťování). 
  * Sestavy o výkonu v Průzkumníku metrik, kde je můžete vyfiltrování a rozčlenění čas/filtry rozsahu pro tuto operaci.
  * Hledání pro toto volání, chcete-li zobrazit vlastnosti konkrétního volání.
  * Chyba sestavy – Pokud count > 1 to znamenat, že došlo k selhání v této operaci, která může přispět ke snížení výkonu.

## <a name="dependency-duration-degradation"></a>Snížení doby trvání závislosti

Moderní aplikace čím dál tím víc přijmout přístup návrh mikroslužby, což v mnoha případech vede k velkým spolehlivosti u externích služeb. Například pokud vaše aplikace závisí na některé datové platformy nebo i v případě vytvoříte vlastní bot service, které bude pravděpodobně přenášet na některé poskytovatele služeb cognitive services umožníte robotům, aby interagovaly víc jako lidé a některé služby úložiště dat pro robota na vyžádání z odpovědi m.  

Příklad oznámení snížení závislost:

![Tady je příklad detekce snížení doby trvání závislosti](media/app-insights-proactive-performance-diagnostics/dependency_duration_degradation.png)

Všimněte si, že musíte:

* Doba trvání v porovnání s normální odezvu pro tuto operaci
* Kolik uživatelů se TOP týká
* Průměrná doba trvání a 90. percentil doby trvání pro tuto závislost na den detekce a 7 dní před
* Počet závislostí volání na den detekce a 7 dní před
* Odkazy na při diagnostice problému
  * Sestavy o výkonu v Průzkumníku metrik pro tuto závislost
  * Vyhledejte a zobrazte vlastnosti volání volá tuto závislost
  * Chyba sestavy – Pokud count > 1 to znamená, které bylo neúspěšné závislosti volání během období zjišťování, která může přispět ke zhoršení doby trvání. 
  * Otevřete Analytics s dotazy, které vypočítá tato doba trvání závislosti a počet  

## <a name="smart-detection-of-slow-performing-patterns"></a>Inteligentní zjišťování pomalé provádění vzory 

Application Insights vyhledá problémy s výkonem, které mohou ovlivňují jenom část z vašich uživatelů, nebo mají vliv jenom uživatelé v některých případech. Například oznámení o načítání stránek je pomalejší na jeden typ prohlížeče než u jiných typů prohlížečů, nebo pokud požadavky se obsluhují pomaleji z určitého serveru. Problémy související s kombinací vlastnosti, může také vyhledat, jako je pomalé stránka načte v jedné zeměpisné oblasti pro klienty, kteří používají konkrétní operační systém.  

Anomálie obdobné jsou velmi obtížné zjistit jen, že se podíváte data, ale jsou mnohem běžnější, než si možná myslíte. Často jsou pouze docházet, pokud si vaši zákazníci začnou stěžovat. Během této doby bude příliš pozdě: ovlivnění uživatelé jsou již přepnutí na konkurencí!

V současné době naše algoritmy podívejte se na dobu načítání stránek, dobu odezvy požadavku na serveru a doby odezvy závislostí.  

Nemáte žádné prahové hodnoty nebo nakonfigurovat pravidla. Machine learning a algoritmy dolování dat se používají pro detekci abnormálních vzorů.

![Z e-mailové upozornění klikněte na odkaz k otevření diagnostickou zprávu v Azure](./media/app-insights-proactive-performance-diagnostics/03.png)

* **Když** zobrazuje čas zjištění problému.
* **Co** popisuje:

  * Problém, který byl zjištěn;
  * Zobrazí vlastnosti sadu událostí, která jsme našli chování problém.
* Tabulka porovnává nedostatečně výkonným sady s průměrnou chování všechny další události.

Kliknutím na odkazy na relevantní sestavy, filtruje se podle času a vlastnosti pomalé provádění sady otevřete Průzkumník metrik a vyhledávání.

Upravte časový rozsah a filtry a prozkoumejte telemetrická data.

## <a name="next-steps"></a>Další postup
Tyto diagnostické nástroje umožňují kontrolovat telemetrie z vaší aplikace:

* [Profiler](app-insights-profiler.md) 
* [Ladicí program snímků](app-insights-snapshot-debugger.md)
* [Analýzy](../azure-monitor/log-query/get-started-portal.md)
* [Inteligentní Diagnostika Analytics](app-insights-analytics.md)

Inteligentní detekce je úplně automatický. Ale možná chcete nastavit některé další oznámení?

* [Ručně konfigurované metriky výstrahy](app-insights-alerts.md)
* [Testy dostupnosti webu](app-insights-monitor-web-app-availability.md)
