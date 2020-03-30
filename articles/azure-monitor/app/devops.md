---
title: Monitorování výkonu webových aplikací – Přehledy aplikací Azure
description: Jak přehledy aplikací zapadají do cyklu devOps
ms.topic: conceptual
ms.date: 12/21/2018
ms.openlocfilehash: 24095aade80022d1e1ebb38357971512bfc873c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669688"
---
# <a name="deep-diagnostics-for-web-apps-and-services-with-application-insights"></a>Hloubková diagnostika webových aplikací a služeb pomocí Application Insights
## <a name="why-do-i-need-application-insights"></a>Proč potřebuji Přehledy aplikací?
Application Insights monitoruje spuštěnou webovou aplikaci. Informuje vás o selháních a problémech s výkonem a pomůže analyzovat, jak zákazníci používají vaši aplikaci. Funguje pro aplikace běžící na mnoha platformách (ASP.NET, Java EE, Node.js, ...) a hostuje se buď v cloudu, nebo místně. 

![Aspekty složitosti doručování webových aplikací](./media/devops/010.png)

Je nezbytné sledovat moderní aplikaci, když je spuštěna. A co je nejdůležitější, chcete zjistit selhání dříve, než většina vašich zákazníků dělat. Chcete také zjistit a opravit problémy s výkonem, které sice nejsou katastrofální, ale možná zpomalí nebo způsobí uživatelům určité nepříjemnosti. A když systém funguje k vaší spokojenosti, chcete vědět, co s ním uživatelé dělají: Používají nejnovější funkci? Daří se jim to?

Moderní webové aplikace jsou vyvíjeny v cyklu nepřetržitého doručování: vydání nové funkce nebo zlepšení; sledovat, jak dobře to funguje pro uživatele; další přírůstek rozvoje na základě těchto znalostí. Klíčovou součástí tohoto cyklu je fáze pozorování. Application Insights poskytuje nástroje pro sledování výkonu a využití webové aplikace.

Nejdůležitějším aspektem tohoto procesu je diagnostika a diagnostika. Pokud se aplikace nezdaří, dojde ke ztrátě obchodu. Hlavní úlohou monitorovacího rámce je proto spolehlivě detekovat selhání, okamžitě vás upozornit a předložit vám informace potřebné k diagnostice problému. To je přesně to, co Application Insights dělá.

### <a name="where-do-bugs-come-from"></a>Odkud pocházejí chyby?
Chyby ve webových systémech obvykle vznikají z problémů s konfigurací nebo chybných interakcí mezi jejich mnoha součástmi. Prvním úkolem při řešení incidentu živého webu je tedy identifikovat místo problému: která složka nebo vztah je příčinou?

Někteří z nás, ti se šedivými vlasy, si pamatují jednodušší éru, ve které počítačový program běžel v jednom počítači. Vývojáři by jej důkladně otestovali před odesláním; a po odeslání, by jen zřídka vidět nebo přemýšlet o tom znovu. Uživatelé by museli smířit s zbytkovými chybami po mnoho let. 

Věci jsou teď úplně jiné. Vaše aplikace má nepřeberné množství různých zařízení pro spuštění a může být obtížné zaručit přesně stejné chování na každé z nich. Hostování aplikací v cloudu znamená, že chyby mohou být rychle opraveny, ale také to znamená nepřetržitou konkurenci a očekávání nových funkcí v častých intervalech. 

Za těchto podmínek je jediným způsobem, jak udržet pevnou kontrolu nad počtem chyb, automatizované testování částí. Bylo by nemožné ručně znovu otestovat vše při každé dodávce. Testování částí je nyní běžnou součástí procesu sestavení. Nástroje, jako je například Xamarin Test Cloud pomoc tím, že poskytuje automatizované testování uživatelského prostředí na více verzích prohlížeče. Tyto testovací režimy nám umožňují doufat, že rychlost chyb nalezených v aplikaci může být omezena na minimum.

Typické webové aplikace mají mnoho živých komponent. Kromě klienta (v prohlížeči nebo aplikaci zařízení) a webového serveru je pravděpodobné, že bude existovat podstatné back-endové zpracování. Možná, že back-end je potrubí komponent, nebo volnější kolekce spolupracujících kusů. A mnoho z nich nebude pod vaší kontrolou - jsou to externí služby, na kterých jste závislí.

V konfiguracích, jako jsou tyto, může být obtížné a neekonomické testovat nebo předvídat každý možný režim selhání, s výjimkou samotného živého systému. 

### <a name="questions-"></a>Otázky...
Několik otázek se ptáme, když vyvíjíme webový systém:

* Padá moje aplikace? 
* Co přesně se stalo? - Pokud se nepodařilo žádost, chci vědět, jak se tam dostal. Potřebujeme stopu událostí...
* Je moje aplikace dostatečně rychlá? Jak dlouho trvá reagovat na typické požadavky?
* Zvládne server zatížení? Když se zvýší rychlost požadavků, trvá doba odezvy stabilní?
* Jak reagují moje závislosti – rozhraní REST API, databáze a další součásti, které moje aplikace volá. Zejména, pokud je systém pomalý, je to moje součást, nebo dostávám pomalé odpovědi od někoho jiného?
* Je moje aplikace nahoru nebo dolů? Je to vidět z celého světa? Dejte mi vědět, pokud se zastaví....
* Co je hlavní příčinou? Došlo k selhání součásti nebo závislosti? Je to problém komunikace?
* Kolik uživatelů je ovlivněno? Pokud mám více než jeden problém řešit, který je nejdůležitější?

## <a name="what-is-application-insights"></a>Co je Application Insights?
![Základní pracovní postup Application Insights](./media/devops/020.png)

1. Application Insights napodobuje vaši aplikaci a odesílá o ní telemetrická data, když je spuštěná. Buď můžete vytvořit Application Insights SDK do aplikace, nebo můžete použít instrumentace za běhu. První metoda je flexibilnější, protože můžete přidat vlastní telemetrii do běžných modulů.
2. Telemetrie se odesílá na portál Application Insights, kde je uložena a zpracována. (I když je Application Insights hostovaná v Microsoft Azure, může monitorovat všechny webové aplikace – nejen aplikace Azure.)
3. Telemetrie je vám prezentována ve formě grafů a tabulek událostí.

Existují dva hlavní typy telemetrie: agregované a nezpracované instance. 

* Data instance zahrnují například zprávu o požadavku, který vaše webová aplikace přijala. Podrobnosti o žádosti můžete najít a zkontrolovat pomocí nástroje Vyhledávání na portálu Application Insights. Instance by zahrnovala data, například jak dlouho trvalo, než vaše aplikace odpověděla na požadavek, stejně jako požadovaná adresa URL, přibližná poloha klienta a další data.
* Agregovaná data zahrnují počty událostí za jednotku času, takže můžete porovnat rychlost požadavků s dobou odezvy. Obsahuje také průměry metrik, jako jsou doby odezvy požadavku.

Hlavními kategoriemi údajů jsou:

* Požadavky na vaši aplikaci (obvykle požadavky HTTP), s daty na adrese URL, dobou odezvy a úspěchem nebo neúspěchem.
* Závislosti – volání REST a SQL uskutečněná vaší aplikací, také s identifikátorem URI, dobou odezvy a úspěchem
* Výjimky, včetně trasování zásobníku.
* Data zobrazení stránky, která pocházejí z prohlížečů uživatelů.
* Metriky, jako jsou čítače výkonu, stejně jako metriky, které napíšete sami. 
* Vlastní události, které můžete použít ke sledování obchodních událostí
* Trasování protokolu používané pro ladění.

## <a name="case-study-real-madrid-fc"></a>Případová studie: Real Madrid F.C.
Webová služba [fotbalového klubu Real Madrid](https://www.realmadrid.com/) slouží asi 450 milionům fanoušků po celém světě. Fanoušci k němu přistupují jak prostřednictvím webových prohlížečů, tak prostřednictvím mobilních aplikací klubu. Fanoušci si nemohou rezervovat pouze vstupenky, ale také přístup k informacím a videoklipům o výsledcích, hráčích a nadcházejících hrách. Mohou vyhledávat pomocí filtrů, jako je počet vstřelených gólů. K dispozici jsou také odkazy na sociální média. Uživatelské prostředí je vysoce personalizované a je navrženo jako obousměrná komunikace, která zaujme fanoušky.

Řešením [je systém služeb a aplikací v Microsoft Azure](https://www.microsoft.com/inculture/sports/real-madrid/). Škálovatelnost je klíčovým požadavkem: provoz je variabilní a může dosáhnout velmi vysokých objemů během a kolem zápasů.

Pro Real Madrid je důležité sledovat výkon systému. Azure Application Insights poskytuje komplexní přehled napříč systémem a zajišťuje spolehlivou a vysokou úroveň služeb. 

Klub také získává hluboké porozumění svým fanouškům: kde jsou (pouze 3% jsou ve Španělsku), jaký zájem mají o hráče, historické výsledky a nadcházející hry a jak reagují na výsledky zápasů.

Většina těchto telemetrických dat se automaticky shromažďuje bez přidaného kódu, což zjednodušilo řešení a snížilo provozní složitost.  Pro Real Madrid se Application Insights zabývá 3,8 miliardami telemetrických bodů každý měsíc.

Real Madrid používá modul Power BI k zobrazení jejich telemetrie.

![Zobrazení Power BI telemetrie Application Insights](./media/devops/080.png)

## <a name="smart-detection"></a>Inteligentní detekce
[Proaktivní diagnostika](../../azure-monitor/app/proactive-diagnostics.md) je nejnovější funkce. Bez jakékoli speciální konfigurace od vás, Application Insights automaticky detekuje a upozorní vás na neobvyklé zvýšení míry selhání ve vaší aplikaci. Je dost chytrý na to, aby ignoroval pozadí občasných selhání, a také stoupá, které jsou prostě úměrné nárůstu požadavků. Pokud například dojde k selhání v jedné ze služeb, na kterých jste závislí, nebo pokud nové sestavení, které jste právě nasadili, nefunguje tak dobře, budete o tom vědět, jakmile se podíváte na svůj e-mail. (A tam jsou webhooky, takže můžete aktivovat další aplikace.)

Dalším aspektem této funkce provádí denní hloubkovou analýzu telemetrie a hledá neobvyklé vzory výkonu, které je těžké zjistit. Může například najít pomalý výkon spojený s určitou zeměpisnou oblastí nebo s konkrétní verzí prohlížeče.

V obou případech výstraha nejen informuje o zjištěných příznacích, ale také poskytuje data, která potřebujete k diagnostice problému, například příslušné zprávy o výjimkách.

![E-mail z proaktivní diagnostiky](./media/devops/030.png)

Zákazník Samtec řekl: "Během nedávnéfunkce cutover, jsme našli pod-škálované databáze, která byla bít své limity prostředků a způsobuje časové limity. Proaktivní detekce výstrahy prošel doslova, jak jsme byli třídění problém, velmi blízko v reálném čase, jak inzeroval. Tato výstraha spojená s výstrahami platformy Azure nám pomohla problém vyřešit téměř okamžitě. Celkový prostoje < 10 minut."

## <a name="live-metrics-stream"></a>Live Metrics Stream
Nasazení nejnovější sestavení může být úzkostné prostředí. Pokud se objeví nějaké problémy, chcete o nich vědět hned, abyste mohli v případě potřeby vycouvat. Live Metrics Stream poskytuje klíčové metriky s latencí přibližně jednu sekundu.

![Živé metriky](./media/devops/0040.png)

A umožňuje okamžitě zkontrolovat vzorek všech selhání nebo výjimek.

![Živé události selhání](./media/devops/002-live-stream-failures.png)

## <a name="application-map"></a>Mapa aplikace
Mapa aplikací automaticky zjišťuje topologii aplikace a pokládá informace o výkonu nad ní, abyste snadno identifikovali kritická místa výkonu a problematické toky v distribuovaném prostředí. Umožňuje zjistit závislosti aplikací na Azure Services. Můžete tažit problém pochopením, pokud se týká kódu nebo závislosti související a z jednoho místa přejít k podrobnostem související diagnostiky zkušenosti. Například vaše aplikace může být selhání z důvodu snížení výkonu na úrovni SQL. Pomocí mapy aplikace můžete okamžitě zobrazit a přejít k podrobnostem o sql index poradce nebo dotaz u insights prostředí.

![Mapa aplikace](./media/devops/0050.png)

## <a name="application-insights-analytics"></a>Analýza přehledů aplikací
Pomocí [služby Analytics](../../azure-monitor/app/analytics.md)můžete psát libovolné dotazy v výkonném jazyce podobném SQL.  Diagnostika v celém zásobníku aplikací se stává snadnou, protože se připojí různé perspektivy a můžete položit správné otázky, které korelace výkonu služeb s obchodnímetriky a zákaznické zkušenosti. 

Můžete zadat dotaz na všechny instance telemetrie a metrická nezpracovaná data uložená na portálu. Jazyk zahrnuje filtr, spojení, agregaci a další operace. Můžete vypočítat pole a provést statistickou analýzu. K dispozici jsou jak tabulkové, tak grafické vizualizace.

![Graf dotazů a výsledků analytics](./media/devops/0025.png)

Například je snadné:

* Segmentujte údaje o výkonu požadavků aplikace podle úrovní zákazníků, abyste porozuměli jejich zkušenostem.
* Vyhledejte konkrétní kódy chyb nebo vlastní názvy událostí během živých šetření webu.
* Přejděte k podrobnostem o používání aplikací konkrétních zákazníků a zjistěte, jak jsou funkce získávány a přijímány.
* Sledujte relace a doby odezvy pro konkrétní uživatele, abyste umožnili týmům podpory a operacím poskytovat okamžitou zákaznickou podporu.
* Určete často používané funkce aplikace, abyste odpověděli na otázky týkající se stanovení priorit funkcí.

Zákazník DNN řekl: "Application Insights nám poskytl chybějící část rovnice pro možnost kombinovat, řadit, dotazovat a filtrovat data podle potřeby. Umožnit našemu týmu používat vlastní vynalézavost a zkušenosti k hledání dat s výkonným dotazovacím jazykem nám umožnilo najít poznatky a řešit problémy, o kterých jsme ani nevěděli, že je máme. Mnoho zajímavých odpovědí pochází z otázek *začínajících na 'Zajímalo by mě, jestli ...'.*"

## <a name="development-tools-integration"></a>Integrace vývojových nástrojů
### <a name="configuring-application-insights"></a>Konfigurace přehledů aplikací
Visual Studio a Eclipse mají nástroje pro konfiguraci správné balíčky sady SDK pro projekt, který vyvíjíte. K dispozici je příkaz nabídky pro přidání Application Insights.

Pokud používáte rozhraní protokolování trasování, jako je Log4N, NLog nebo System.Diagnostics.Trace, pak získáte možnost odeslat protokoly do Application Insights spolu s další telemetrií, takže můžete snadno korelovat trasování s požadavky, závislost volání a výjimky.

### <a name="search-telemetry-in-visual-studio"></a>Hledání telemetrie v sadě Visual Studio
Při vývoji a ladění funkce můžete zobrazit a prohledávat telemetrii přímo v sadě Visual Studio pomocí stejných vyhledávacích zařízení jako na webovém portálu.

A když Application Insights protokoly výjimku, můžete zobrazit datový bod v sadě Visual Studio a přejít přímo na příslušný kód.

![Hledání ve Visual Studiu](./media/devops/060.png)

Během ladění máte možnost zachovat telemetrická data ve vývojovém počítači, její zobrazení v sadě Visual Studio, ale bez odeslání na portál. Tato místní možnost zabraňuje míchání ladění s produkční telemetrií.

### <a name="work-items"></a>Pracovní položky
Když je vyvolána výstraha, Application Insights můžete automaticky vytvořit pracovní položku v systému sledování práce.

## <a name="but-what-about"></a>Ale co...?
* [Ochrana osobních údajů a úložiště](../../azure-monitor/app/data-retention-privacy.md) – vaše telemetrie se uchovává na zabezpečených serverech Azure.
* Výkon - dopad je velmi nízký. Telemetrie je dávkována.
* [Ceny](../../azure-monitor/app/pricing.md) – Můžete začít zdarma, a to pokračuje, když jste v nízkéhlasitosti.


## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Další kroky
Začít s Application Insights je snadné. Hlavní možnosti jsou:

* [Servery Služby IIS](../../azure-monitor/app/monitor-performance-live-website-now.md)a také pro [službu Azure App Service](../../azure-monitor/app/app-insights-overview.md).
* Instrumentujte svůj projekt během vývoje. Můžete to udělat pro [aplikace ASP.NET](../../azure-monitor/app/asp-net.md) nebo [Java,](../../azure-monitor/app/java-get-started.md) stejně jako [Node.js](../../azure-monitor/app/nodejs.md) a řadu [dalších typů](../../azure-monitor/app/platforms.md). 
* Instrumentujte [libovolnou webovou stránku](../../azure-monitor/app/javascript.md) přidáním krátkého fragmentu kódu.

