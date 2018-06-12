---
title: Webové funkce application performance monitoring - Azure Application Insights | Microsoft Docs
description: Jak Application Insights zapadá do devOps cyklu
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 479522a9-ff5c-471e-a405-b8fa221aedb3
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: conceptual
ms.date: 03/14/2017
ms.author: mbullwin
ms.openlocfilehash: a9a6e513d95df5dafba82556f74ec209529ff58d
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294907"
---
# <a name="deep-diagnostics-for-web-apps-and-services-with-application-insights"></a>Hloubková diagnostika webových aplikací a služeb pomocí Application Insights
## <a name="why-do-i-need-application-insights"></a>Proč musím Application Insights?
Application Insights monitoruje funkční webovou aplikaci. Informace o selhání a problémy s výkonem a pomáhá analyzovat, jak zákazníci používají vaši aplikaci. Ho funguje pro aplikace běžící na spoustě platforem (ASP.NET, J2EE, Node.js,...) a je hostovaná v cloudu nebo místně. 

![Aspekty složitosti doručování webové aplikace](./media/app-insights-devops/010.png)

Je nezbytné pro monitorování moderní aplikace, když je spuštěná. Co je nejdůležitější který chcete detekovat selhání před většina vašich zákazníků. Také chtít zjistit a opravit problémy s výkonem, že při nejsou závažné, případně zpomalují počítač nebo způsobit, že některé komplikace pro vaše uživatele. A pokud systému provádí vašich potřeb, budete chtít vědět, co uživatelé dělají s ním: jsou pomocí nejnovější funkce? Jsou s ním úspěšné jejich?

Moderní webové aplikace jsou vyvinuté v cyklus nastavené průběžné doručování: uvolnění nové funkce nebo zlepšování; Sledujte, jak dobře funguje pro uživatele; Naplánujte další přírůstek vývoj podle dané znalosti. Klíčovou součástí tohoto cyklu je fázi pozorování. Application Insights poskytuje nástroje pro monitorování webové aplikace pro využití a výkonu.

Nejdůležitějšími aspekty tohoto procesu je diagnostiky a diagnostiku. V případě selhání aplikace je právě ztratil firmy. Primárním úkolem monitorování prostředí je proto spolehlivě rozpoznala chyby, upozornění můžete okamžitě a prezentovat informace potřebné k diagnostikovat problém. Toto je přesně co dělají Application Insights.

### <a name="where-do-bugs-come-from"></a>Odkud pocházejí chyby?
Selhání v systémech webové obvykle způsobit problémy s konfigurací nebo chybných interakce mezi jejich celá řada komponent. První úlohou při řešení incidentu živý web je proto k identifikaci místo problému: které komponenta nebo relace je příčinou?

Některé z nás, ty s šedé kříž můžete mějte na paměti jednodušší letopočtu, ve kterém byl počítačový program spuštěn v jednom počítači. Vývojáři by otestovat důkladně před přenosů. a nutnosti dodaný, by zřídka najdete v článku nebo myslíte o ho znovu. Uživatelé by musel balení s zbytkové chyby pro mnoho let. 

Co jsou nyní tak liší. Aplikace má nadbytku ke spuštění na různých zařízeních, a může být obtížné zaručit přesně stejné chování na každé z nich. Hostování aplikace v cloudu znamená můžete rychle opraveno chyb, ale také znamená průběžné soutěže a očekávání nových funkcí v pravidelných intervalech. 

V těchto podmínkách je jediným způsobem, jak udržovat závazné ovládací prvek v počtu chyb automatizované testování částí. Je možné ručně znovu proveďte kompletní testování na každý doručení. Testování částí je nyní běžné součástí procesu sestavení. Nápověda k nástrojům například Xamarin Test Cloud tím, že poskytuje automatizované uživatelského rozhraní testování na více verzí prohlížeče. Tyto režimy testování umožněte nám Doufáme, že počet chyb nalezena uvnitř aplikace můžete omezit na minimum.

Typické webových aplikací mít celou řadu součástí za provozu. Kromě klienta (v aplikaci prohlížeč nebo zařízení) a webový server je pravděpodobné, že se zpracování významné back-end. Back-end je možná kanálu součástí nebo čím větší kolekci spolupráce částí. A kolika z nich nebudou v vlastního ovládacího prvku – jsou externích služeb, na kterých závisí.

V konfiguracích takovéto může být obtížné a uneconomical podmínky nebo předvídáte, všechny možné selhání režimu, jiné než v systému za provozu, sám sebe. 

### <a name="questions-"></a>Dotazy...
Některé dotazy, můžeme požádat, když jsme se vývojem webového systému:

* Selhává Moje aplikace 
* Co přesně se stalo? – Pokud se nezdařilo žádost, chci vědět, jak získali existuje. Potřebujeme trasování událostí...
* Moje aplikace je dostatečně rychle? Jak dlouho trvá reagovat na požadavky typické?
* Server může zpracovat zatížení? Jakmile se o počet žádostí o roste, doba odezvy stiskněte konstantní?
* Jak přizpůsobivý jsou moje závislosti - rozhraní REST API, databáze a další součásti, které volá mé aplikace. Konkrétní systém je pomalý, je můj součásti nebo dochází k pomalé odezvy od jiného uživatele?
* Moje aplikace je nahoru nebo dolů? Můžete ji zobrazit z po celém světě? Chci vědět, pokud přestane...
* Co je hlavní příčinou? Chyba v mé součásti nebo závislost? Je problém komunikace?
* Dopad na tom, kolik uživatelů? Pokud budu potřebovat více než jedno vydání tohoto problému, který je nejdůležitější?

## <a name="what-is-application-insights"></a>Co je Application Insights?
![Základní pracovní postup Application Insights](./media/app-insights-devops/020.png)

1. Application Insights instruments vaší aplikace a odesílá telemetrii týkající se ho, když aplikace běží. Buď můžete vytvořit Application Insights SDK do aplikace, nebo můžete použít instrumentace za běhu. Metoda je flexibilnější, jak můžete přidat vlastní telemetrii na regulární moduly.
2. Telemetrie je odeslána na portál Application Insights, kde je uložena a zpracována. (I když Application Insights je hostován v Microsoft Azure, může monitorovat všechny webové aplikace – aplikace právě Azure.)
3. Telemetrie je zobrazují ve formuláři grafů a tabulek událostí.

Existují dva hlavní typy telemetrických dat: agregovaná a raw instancí. 

* Instance data obsahuje například sestavu žádosti, která byla přijata ve vaší webové aplikace. Můžete najít a zkontrolujte podrobnosti o žádost o pomocí nástroje vyhledávání v portálu služby Application Insights. Instance bude zahrnovat data, jako je například jak dlouho trvalo aplikaci reagovat na žádosti, stejně jako požadovanou adresu URL, Přibližná umístění klienta a další data.
* Agregovaná data zahrnuje počet událostí za jednotku času, takže můžete porovnat míra požadavků s doby odezvy. Zahrnuje také průměry metrik, jako je například dobu odezvy požadavku.

Hlavní kategorie dat jsou:

* Požadavky na aplikace (obvykle požadavků protokolu HTTP), s daty na adrese URL, doby odezvy a úspěch nebo selhání.
* Závislosti - volání REST a SQL provedené vaší aplikace, také pomocí URI, doby odezvy a úspěch
* Výjimky, včetně trasování zásobníku.
* Stránka zobrazení data, která pochází z prohlížečů uživatelů.
* Metriky například čítače výkonu, jakož i metriky, které můžete psát sami. 
* Vlastní události, které můžete použít ke sledování obchodní události
* Slouží k ladění protokolu trasování.

## <a name="case-study-real-madrid-fc"></a>Případová studie: Skutečné Madridu F.C.
Webovou službu z [skutečné křížovou kartou Madridu fotbalové](http://www.realmadrid.com/) slouží o 450 milionů ventilátory po celém světě. Ventilátory přístup přes webových prohlížečů a mobilních aplikací křížovou kartou. Ventilátory můžete nejen sešit lístků, ale také přístup k informace a video klipů na výsledky, přehrávače a nadcházející hry. Vyhledávání můžete s filtry, jako je počet cílů skóre pro magnitudu. Existují také odkazy na sociálních sítích. Činnost koncového uživatele je vysoce přizpůsobené a slouží jako obousměrné komunikace ventilátory, vykonávat.

Řešení [je systém služeb a aplikací v Microsoft Azure](https://www.microsoft.com/en-us/enterprise/microsoftcloud/realmadrid.aspx). Škálovatelnost je klíčovým požadavkem: provoz je proměnná a mohou dosáhnout velké svazky, během a kolem odpovídá.

Skutečných Madrid, je životně důležité sledovat výkon systému. Azure Application Insights poskytuje komplexní pohled v rámci systému, zajištění spolehlivé a vysokou úroveň služeb. 

Křížovou kartou také získá podrobný přehled o jeho ventilátory: tam, kde jsou (pouze % 3 jsou v Španělsko), které vás zajímají mají v přehrávače, historické výsledky a nadcházející hry a jak se reagují tak, aby odpovídaly výstupy.

Většina této telemetrická data jsou shromažďována automaticky žádné přidané kód, který zjednodušené řešení a snižuje provozní složitost.  Skutečných Madrid, Application Insights se zabývá body telemetrie 3.8 miliardy každý měsíc.

Skutečné Madridu používá modul Power BI zobrazíte jejich telemetrie.

![Power BI zobrazení telemetrie Application Insights](./media/app-insights-devops/080.png)

## <a name="smart-detection"></a>Inteligentní detekce
[Proaktivní diagnostiky](app-insights-proactive-diagnostics.md) je nejnovější funkce. Bez jakékoli speciální konfigurace, které jste Application Insights automaticky zjišťuje a upozorní na neobvyklé přírůstky. selhání měr ve vaší aplikaci. Je dostatečně inteligentní Ignorovat pozadí příležitostné selhání, a také složitost, které jsou jednoduše přiměřené zvýšení požadavky. Tak například pokud dojde k selhání v jednom ze služby, které závisí na, nebo pokud jste právě nasadili nové sestavení nefunguje, tak i pak poznáte o tom, co nejrychleji se podíváte na e-mailu. (A k dispozici jsou webhooky můžete aktivovat jiné aplikace.)

Další aspekt této funkce provede denní podrobné analýzy vaší telemetrie, hledá neobvyklou vzory výkonu, které je obtížné zjistit. Například najdete nízký výkon, které jsou spojené s konkrétní zeměpisná oblast nebo s verzí určitého prohlížeče.

V obou případech výstrahy nejen zjistíte příznaky není zjištěna, ale také nabízí data, budete muset usnadnění diagnostiky, jako je například relevantní výjimka sestavy.

![E-mailu z proaktivní diagnostiky](./media/app-insights-devops/030.png)

Zákazník Samtec uvedená: "během posledních funkci cutover jsme našli databázi škálovat, který byl nedosáhli limitů jeho prostředků a způsobuje vypršení časových limitů. Výstrahy proaktivní zjišťování byla přijata oznámena jako inzerované jsme byly triaging problém velmi v reálném čase. Tato výstraha doplněná s výstrahami platformy Azure pomohl nám prakticky okamžitě vyřešte problém. Celkové prostoje < 10 minut."

## <a name="live-metrics-stream"></a>Live Metrics Stream
Nasazení na nejnovější verzi může být usilujíce prostředí. Pokud se vyskytnou potíže, budete chtít vědět o nich hned, takže můžete zálohovat v případě potřeby. Živý datový proud metriky vám dává klíčové metriky s latencí přibližně jednu sekundu.

![Metriky za provozu](./media/app-insights-devops/040.png)

A umožňuje okamžitě zkontrolujte ukázka chyby a výjimky.

![Události chyb za provozu](./media/app-insights-devops/live-stream-failures.png)

## <a name="application-map"></a>Mapa aplikace
Mapa aplikace automaticky vyhledá topologie vaší aplikace, kterým se informace o výkonu nad ji, abyste mohli snadno identifikovat kritická místa výkonu a problematické toků v distribuovaném prostředí. Umožňuje zjistit závislosti aplikací na služby Azure. Umožňuje rychlou kontrolu problému porozumíte Pokud je související s kódem nebo závislosti související a z jednom místě přejít k podrobnostem související diagnostiky prostředí. Například aplikace může být neúspěšné kvůli snížení výkonu ve vrstvě SQL. S aplikací mapy můžete ji zobrazit okamžitě a přejít k podrobnostem Poradce pro Index SQL nebo mít přehled o dotazu.

![Mapa aplikace](./media/app-insights-devops/050.png)

## <a name="application-insights-analytics"></a>Analýza statistiky aplikace
S [Analytics](app-insights-analytics.md), můžete napsat libovolný dotazy v jazyce, výkonné jako SQL.  Diagnostikování napříč zásobníku celá aplikace se stane snadno spojit různých perspektiv a můžete položit správné otázky ke korelaci výkon služby s obchodní metriky a zkušeností zákazníků. 

Všechny instance telemetrie a metriky nezpracovaná data uložená na portálu se můžete dotazovat. Jazyk zahrnuje filtru, spojení, agregace a další operace. Můžete vypočítat pole a provádět statistickou analýzu. Existují tabulkový a grafické vizualizace.

![Graf dotazu a výsledky analýzy](./media/app-insights-devops/025.png)

Například je snadno:

* Rozdělit data výkonu požadavku vaší aplikace pomocí vrstev zákazníka pochopit jejich prostředí.
* Vyhledat konkrétní chybové kódy a názvy vlastních událostí během šetření živý web.
* Přejděte do používání aplikace konkrétní zákazníků pochopit, jak jsou funkce získat a přijmout.
* Sledování relací a dobu odezvy pro konkrétní uživatele pro povolení podpory a operace týmy zajistit rychlých zákaznickou podporu.
* Určení funkcí často používaných aplikací k odpovědi na otázky funkce stanovení priorit.

Uvedená zákazníka DNN: "Application Insights poskytl nám s chybějící součást rovnice se moct do kombinovaného útvaru, řazení, dotazů a filtrování dat podle potřeby. Povolení náš tým používat vlastní vynalézavosti a prostředí a zjistit, že data s účinný dotazovací jazyk má povolené nám najít přehledy a řešení potíží nepomohly i víme, že jsme měli. Mnoho zajímavé odpovědi pocházet z otázek počínaje *' I wonder if...'.*"

## <a name="development-tools-integration"></a>Integrace nástroje pro vývoj
### <a name="configuring-application-insights"></a>Konfigurace služby Application Insights
Visual Studio a Eclipse mít nástroje pro konfiguraci správné SDK balíčků pro projekt, které vyvíjíte. Příkaz nabídky přidat Application Insights není k dispozici.

Pokud jste mají být pomocí trasování a protokolování framework například Log4N, NLog nebo System.Diagnostics.Trace, pak získáte možnost odeslat protokoly Application insights spolu s další telemetrií tak, aby trasování mohou snadno korelovat s požadavky, volání závislostí a výjimek.

### <a name="search-telemetry-in-visual-studio"></a>Telemetrie vyhledávání v sadě Visual Studio
Při vývoji a ladění funkce, můžete zobrazit a hledání telemetrii přímo v sadě Visual Studio, pomocí stejné vyhledávání prostředků jako na webový portál.

A když Application Insights protokoluje výjimku, můžete zobrazit datového bodu v sadě Visual Studio a přejít přímo na příslušný kód.

![Visual Studio vyhledávání](./media/app-insights-devops/060.png)

Během ladění, máte možnost zachovat telemetrii ve vývojovém počítači, jeho zobrazení v sadě Visual Studio, ale bez odeslání na portál. Tato možnost místní zabraňuje kombinování ladění pomocí telemetrie produkční.

### <a name="build-annotations"></a>Sestavení poznámky
Pokud používáte Visual Studio Team Services k vytvoření a nasazení aplikace, nasazení poznámky zobrazí v grafech na portálu. Nejnovější verze měli nijak neprojeví na metriku, je zřejmé.

![Sestavení poznámky](./media/app-insights-devops/070.png)

### <a name="work-items"></a>Pracovní položky
Pokud je vyvolána výstraha, Application Insights do systému pro sledování práce automaticky vytvořit pracovní položku.

## <a name="but-what-about"></a>Ale co o...?
* [Ochrana osobních údajů a úložiště](app-insights-data-retention-privacy.md) -telemetrie se ukládají na Azure zabezpečení serverů.
* Dopad na výkon - je velmi nízkou. Telemetrie je zpracovat v dávce.
* [Ceny](app-insights-pricing.md) – můžete začít používat zdarma a který pokračuje v nízkou svazku.


## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Další postup
Začínáme s Application Insights je snadné. Hlavní možnosti jsou:

* Instrumentace webové aplikace už spuštěná. To vám dává všechny předdefinované výkonu telemetrie. Je k dispozici pro [Java](app-insights-java-live.md) a [servery služby IIS](app-insights-monitor-performance-live-website-now.md)a také pro [webové aplikace Azure](app-insights-azure.md).
* Instrumentace projektu během vývoje. Můžete to udělat pro [ASP.NET](app-insights-asp-net.md) nebo [Java](app-insights-java-get-started.md) aplikace, a také [Node.js](app-insights-nodejs.md) a hostitele z [jiné typy](app-insights-platforms.md). 
* Nástroj [žádné webové stránce](app-insights-javascript.md) přidáním fragmentu kódu krátké.

