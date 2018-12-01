---
title: Web application performance monitoring - Azure Application Insights | Dokumentace Microsoftu
description: Jak Application Insights zapadá do cyklu devOps
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 479522a9-ff5c-471e-a405-b8fa221aedb3
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/14/2017
ms.author: mbullwin
ms.openlocfilehash: 57e201403374ac72ef79bb4335294e1e937c8757
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2018
ms.locfileid: "52720300"
---
# <a name="deep-diagnostics-for-web-apps-and-services-with-application-insights"></a>Hloubková diagnostika webových aplikací a služeb pomocí Application Insights
## <a name="why-do-i-need-application-insights"></a>Proč potřebuji Application Insights?
Application Insights monitoruje webové aplikace spuštěné. Vás informuje o selhání a problémy s výkonem a pomáhá analyzovat, jak zákazníci využívají vaše aplikace. Funguje pro aplikace běžící na spoustě platforem (ASP.NET, J2EE, Node.js,...) a je hostovaná v cloudu nebo místně. 

![Aspekty složitost doručování webových aplikací](./media/app-insights-devops/010.png)

Je nezbytné pro monitorování moderní aplikace během jejího běhu. Nejdůležitější ale je který chcete detekovat selhání před většina vašich zákazníků. Také chcete vyhledat a opravit problémy s výkonem, zatímco není katastrofickými, možná zpomalit zpracování nebo způsobit nějaké potíže vašim uživatelům. A když systém provádí vašich potřeb, budete chtít vědět, co uživatelé dělají s ním: jsou pomocí nejnovější funkce? Jsou úspěšná s ním?

Moderní webové aplikace, které jsou vyvíjeny v cyklu průběžného doručování: vydání nové funkce nebo vylepšení; Sledujte, jak dobře funguje pro uživatele; Naplánujte další přírůstek podle dané znalosti vývoje. Klíčovou součástí tohoto cyklu je fázi zjišťování. Application Insights poskytuje nástroje pro monitorování výkonu a využití webové aplikace.

Nejdůležitější aspekty tohoto procesu je diagnostiky a Diagnostika. Pokud aplikace selže, je právě ztráty firmy. Primární role monitorování framework je proto spolehlivé zjišťování chyb, můžete okamžitě informovat a znamenat informace potřebné k diagnostice problému. To je přesně čemu Application Insights.

### <a name="where-do-bugs-come-from"></a>Odkud pocházejí chyb?
Chyby ve webových systémů obvykle vzniknout problémy s konfigurací nebo chybný interakce mezi jejich řada komponent. Proto je první úkol při použití na incident živého webu místo problém identifikovat: které komponenta nebo vztah je příčinou?

Některé z nás, těmi, které mají šedou vlasů zapamatujete jednodušší období, ve kterém byl počítačový program spuštěn v jednom počítači. Vývojáři by se důkladně otestujte před dodáním. a s dodán, by zřídka najdete v článku nebo Představte si to znovu. Uživatelé by musel vyvěste s zbývající chyby po mnoho let. 

Co jsou nyní tak velmi liší. Vaše aplikace obsahuje množství spouštět na různých zařízeních a může být obtížné zajistit přesně stejné chování na každé z nich. Hostování aplikací v cloudu znamená, že můžete rychle opravit chyby, ale také to znamená průběžné soutěž a že budou nové funkce v pravidelných intervalech. 

Za těchto podmínek je jediný způsob, jak zachovat pevnou ovládací prvek na počet chyb automatizované testování částí. Bylo by možné ručně znovu testovat vše, co každý doručení. Testování částí je teď běžnou součástí procesu sestavení. Nástroje, jako je Xamarin Test Cloud pomoct tím, že poskytuje automatizovaných testů ve více verzích prohlížeče UI. Tyto režimy testování umožňují nám Doufáme, počet chyb v aplikaci, můžete omezit na minimum.

Běžné webové aplikace mají mnoho součástí za provozu. Kromě klienta (v aplikaci prohlížeč nebo zařízení) a webový server je pravděpodobné, že se zpracování podstatné back-endem. Možná je back-endu kanálu komponent nebo volnější kolekcí spolupracujících údaje. A mnoho z nich nebudou existovat v rámci ovládacího prvku – jsou to externích služeb, na kterých závisí.

V konfiguracích obdobné může být obtížné a uneconomical test pro a předvídat, všech možných režimů selhání, jiné než v systému za provozu, samotného. 

### <a name="questions-"></a>Dotazy...
Některé dotazy požádáme při vyvíjíme webový systém:

* Dochází k chybám aplikace? 
* Co přesně se stalo? – Pokud došlo k selhání požadavku, chci vědět, jak máte existuje. Potřebujeme trasování událostí...
* Je Moje aplikace dostatečně rychle? Jak dlouho trvá reagovat na typické požadavky?
* Server může zpracovat zatížení? Když se zvyšuje počet požadavků, doba odezvy podržte konstantní?
* Jak responzivní jsou moje závislosti – rozhraní REST API, databáze a další součásti, které Moje aplikace volá operaci. Zejména při pomalém systému je Moje komponenty, nebo se zobrazuje pomalé odezvy od někoho jiného?
* Navýšení nebo snížení kapacity je Moje aplikace? Lze ji zobrazit z po celém světě? Dejte nám vědět, pokud se zastaví...
* Co je hlavní příčinou? Chyba v mé součásti nebo závislosti? Je to problém komunikaci?
* Kolik uživatelů se to týká? Pokud mám více než jeden problém řešit, což je nejdůležitější?

## <a name="what-is-application-insights"></a>Co je Application Insights?
![Základní pracovní postup služby Application Insights](./media/app-insights-devops/020.png)

1. Application Insights využívá vaši aplikaci a odesílá telemetrická data o tom, když aplikace běží. Můžete vytvořit Application Insights SDK do aplikace, nebo můžete provádět instrumentaci za běhu. První metoda je flexibilnější, při přidávání vlastní telemetrii do modulů, které pravidelně.
2. Na portálu služby Application Insights, kde je uložena a zpracována přijde telemetrická data. (I když Application Insights je hostován v Microsoft Azure, může monitorovat všechny webové aplikace – aplikace právě Azure.)
3. Telemetrická data se zobrazí ve formě grafů a tabulek událostí.

Existují dva hlavní typy telemetrických dat: agregovaná a raw instancí. 

* Instance data zahrnují například zprávu žádosti, která byla přijata ve vaší webové aplikace. Můžete vyhledat a prozkoumejte podrobnosti o žádosti o pomocí nástroje hledání na portálu Application Insights. Instance bude zahrnovat data, třeba jak dlouho trvalo aplikace reagovat na žádosti, stejně jako požadovanou adresu URL, jak umístění klienta a další data.
* Agregovaná data obsahuje počet událostí za jednotku času, takže můžete porovnat míra požadavků s dobou odezvy. Zahrnuje také průměry metriky, například zaznamenávání doby odezvy požadavku.

Hlavní kategorie dat jsou:

* Požadavky na aplikace (obvykle požadavků protokolu HTTP), s daty na adresu URL, doby odezvy a úspěchu nebo selhání.
* Závislosti – volání REST a SQL provedené při vaší aplikace, také s identifikátorem URI, doby odezvy a úspěch
* Výjimky, včetně trasování zásobníku.
* Data o zobrazení stránek, které pocházejí z prohlížečů uživatelů.
* Metriky, jako jsou čítače výkonu, jakož i metriky, které si sami napíšete. 
* Vlastní události, které můžete použít ke sledování obchodních událostí
* Trasování protokolů použít pro ladění.

## <a name="case-study-real-madrid-fc"></a>Případová studie: Real Madrid F.C.
Webové služby, kterou [Real Madrid Football Club](http://www.realmadrid.com/) slouží asi 450 milionům fanoušků z celého světa. K němu přes webových prohlížečů a mobilních aplikací Club fanoušky přístup. Fanoušky lze pouze rezervuje lístky, ale také přístup k klipy informace a video na výsledky, hráče a nadcházející hry. Vyhledávání můžete s filtry, jako je počet cílů skóre. Existují také odkazy na sociálních sítích. Činnost koncového uživatele je vysoce přizpůsobené a je navržena jako obousměrná komunikace provozovat fanoušků.

Řešení [je systém služeb a aplikací v Microsoft Azure](https://www.microsoft.com/inculture/sports/real-madrid/). Škálovatelnost je klíčovým požadavkem: provoz je proměnná a mohou dosáhnout velmi velké objemy během a po shody.

Pro Real Madrid, je důležité monitorovat výkon systému. Azure Application Insights nabízí komplexní pohled na systém, zajištění spolehlivého a vysokou úroveň služeb. 

Klub také získá hlouběji porozuměli jeho fanoušky: tam, kde jsou (pouze % 3 jsou ve Španělsku), které vás zajímají mají v přehrávačů, historické výsledky a nadcházející hry a jak reagují tak, aby odpovídaly výsledků.

Většina těchto dat telemetrie se automaticky shromažďují bez přidání kód, který zjednodušené řešení a snížit provozní složitost.  Pro Real Madrid, Application Insights se zabývá bodům telemetrie 3.8 miliard každý měsíc.

Real Madrid používá modulu Power BI k zobrazení jejich telemetrická data.

![Power BI zobrazení telemetrie Application Insights](./media/app-insights-devops/080.png)

## <a name="smart-detection"></a>Inteligentní zjišťování
[Proaktivní Diagnostika](app-insights-proactive-diagnostics.md) je nejnovější funkce. Bez žádnou zvláštní konfiguraci vámi Application Insights automaticky zjistí a upozorní vás neobvyklé nárůsty chybovost ve vaší aplikaci. Je dostatečně inteligentní, aby Ignorovat pozadí občasnému selhání, a také složitost, které jsou jednoduše úměrné nárůst požadavků. Tak například pokud dojde k selhání v jednom ze služeb, které závisí na, nebo pokud nové sestavení do fronty jste právě nasadili nepracuje tak dobře, pak budete vědět o něm poté, co lze najít v e-mailu. (A webhooky, takže můžete aktivovat další aplikace.)

Dalším aspektem tato funkce provádí denní potřebují podrobně analyzovat telemetrii, hledá neobvyklé vzory výkonu, které se těžko zjistit. Například najdete nízký výkon, které jsou spojené s konkrétní zeměpisné oblasti nebo s verzí určitého prohlížeče.

V obou případech výstraha nejen zjistíte příznaků je zjištěna, ale také poskytuje data, která potřebujete pomoc s diagnostikou problému, například sestavy příslušná výjimka.

![E-mailu ze proaktivní Diagnostika](./media/app-insights-devops/030.png)

Zákazníka Samtec říká: "během nedávné funkce přímou migraci, zjistili databázi v rámci škálování, který jste narazili jeho omezení prostředků a způsobí vypršení časového limitu. Proaktivní detekce upozornění pochází z doslova jako ohlášené jsme byly třídění problém, velmi téměř reálném čase. Tato výstraha s výstrahami platformy Azure s velkou provázaností rozjezd téměř okamžité vyřešení problému. Total downtime < 10 minut."

## <a name="live-metrics-stream"></a>Live Metrics Stream
Nasazení nejnovější sestavení může být nervózním prostředí. Pokud máte potíže, budete chtít informovat o hned, tak, aby můžete zálohovat v případě potřeby. Live Metrics Stream poskytuje klíčové metriky s latencí přibližně jedné sekundy.

![Živé metriky](./media/app-insights-devops/040.png)

A umožňuje okamžitě zkontrolujte vzorek případné chyby nebo výjimky.

![Selhání živé události](./media/app-insights-devops/live-stream-failures.png)

## <a name="application-map"></a>Mapa aplikace
Mapa aplikace automaticky zjistí topologii vaší aplikace, kterým se na informace o výkonu na to, abyste mohli snadno identifikovat kritické body výkonu a problematické toky v distribuovaném prostředí. Umožňuje zjistit závislosti aplikací na služby Azure. Problém můžete posoudit podle principy, je-li týkající se kód nebo závislosti související a z jednotné místo, kde přejít na související diagnostiky prostředí. Například vaše aplikace se ji nedaří kvůli snížení výkonu ve vrstvě SQL. Pomocí mapy aplikace můžete podívat, jak to hned a přejít k podrobnostem SQL Index Advisor nebo prostředí přehledy dotazů.

![Mapa aplikace](./media/app-insights-devops/050.png)

## <a name="application-insights-analytics"></a>Application Insights Analytics
S [Analytics](app-insights-analytics.md), můžete napsat libovolné dotazy v jazyce podobném SQL výkonné.  Diagnostika napříč celým zásobníkem celé aplikace budete moct snadno spojit různých perspektiv a můžete pokládat správné otázky ke korelaci výkon služby s obchodní metriky a vylepšit uživatelské prostředí. 

Všechny instance telemetrie a metriky nezpracovaných dat uložených na portálu se můžete dotazovat. Jazyk zahrnuje filtr, spojení, agregace a další operace. Můžete vypočítat polí a provádění statistických analýz. Existují tabulkovým i grafických vizualizací.

![Graf dotazu a výsledky analýzy](./media/app-insights-devops/025.png)

Například velmi snadno:

* Údaje o výkonu vaší aplikace požadavek segmentovat podle úrovně zákazníků o své zkušenosti.
* Hledání konkrétních kódech chyb nebo názvy vlastních událostí během šetření živého webu.
* K podrobnostem o využití aplikace z konkrétní zákazníky umožnila porozumět, jak se funkce získat a přijmout.
* Sledování relací a doby odezvy pro konkrétního uživatele pro povolení podpory a provozní týmy k zajištění podpory rychlých zákazníka.
* Určení často používaných aplikací funkcí odpovědi na otázky stanovení priorit funkce.

Říká, že zákazník DNN: "Application Insights poskytuje nám s chybějící součást rovnice se možnost kombinovat, řazení, dotazu a filtrování dat podle potřeby. Náš tým používat vlastní vynalézavosti a prostředí a zjistit, že nám umožňuje získat přehledy a řešit problémy má povoleno data s výkonný dotazovací jazyk umožňující neměli i víme, že jsme měli. Spoustu zajímavých odpovědi pocházejí z dotazy počínaje *"můžu wonder if...".*"

## <a name="development-tools-integration"></a>Integrace nástroje pro vývoj
### <a name="configuring-application-insights"></a>Konfigurace Application Insights
Visual Studio a Eclipse mají nástroje pro konfiguraci správné sady SDK balíčky pro projekt, který vyvíjíte. Existuje příkaz pro přidání Application Insights.

Pokud chcete používat rozhraní protokolování trasování například Log4N, NLog nebo System.Diagnostics.Trace, získáte možnost odeslat protokoly do služby Application Insights společně s další telemetrická data, tak, aby mohli snadno porovnat trasování s požadavky, závislosti volání a výjimky.

### <a name="search-telemetry-in-visual-studio"></a>Hledat telemetrii v sadě Visual Studio
Při vývoji a ladění funkcí, může zobrazit a prohledávat telemetrická data přímo ve Visual Studiu, pomocí stejných prostředků vyhledávání jako webový portál.

A když Application Insights protokoluje výjimku, můžete zobrazit datový bod v sadě Visual Studio a přejít přímo na příslušný kód.

![Hledání Visual Studio](./media/app-insights-devops/060.png)

Během ladění, máte možnost zachovat telemetrická data ve vývojovém počítači, jeho zobrazení v sadě Visual Studio, ale bez odeslání na portál. Tato možnost místní se vyhnete kombinování ladění pomocí telemetrie produkčního prostředí.

### <a name="build-annotations"></a>Vytváření poznámek
Pokud používáte Azure DevOps pro sestavení a nasazení vaší aplikace, poznámky k nasazení zobrazí na grafy na portálu. Nejnovější vydaná verze má vliv na metriky, stane se takový zřejmý.

![Vytváření poznámek](./media/app-insights-devops/070.png)

### <a name="work-items"></a>Pracovní položky
Když je vyvolána výstraha, Application Insights automaticky vytvořit pracovní položku v systému pro sledování práce.

## <a name="but-what-about"></a>Ale co...?
* [Ochrana osobních údajů a úložiště](app-insights-data-retention-privacy.md) – vaše telemetrická data se ukládají na Azure zabezpečení serverů.
* Výkon – dopad je velmi nízký. Telemetrie v dávce.
* [Ceny](app-insights-pricing.md) – můžete začít zdarma, a to platí i když jste v malé množství.


## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Další postup
Začínáme s Application Insights je snadné. Jsou hlavní možnosti:

* Instrumentace webové aplikace už běží. To vám veškerá telemetrická data integrovaných výkonu. Je k dispozici pro [Java](app-insights-java-live.md) a [servery služby IIS](app-insights-monitor-performance-live-website-now.md)a také pro [webových aplikací Azure web apps](app-insights-overview.md).
* Instrumentujte projektu během vývoje. Můžete to provést [ASP.NET](app-insights-asp-net.md) nebo [Java](app-insights-java-get-started.md) aplikace, stejně jako [Node.js](app-insights-nodejs.md) a celou řadu [jiné typy](app-insights-platforms.md). 
* Nástroj [libovolná webová stránka](app-insights-javascript.md) přidáním krátkého kódu.

