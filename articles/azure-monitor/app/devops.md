---
title: Sledování výkonu webové aplikace – Azure Application Insights
description: Jak Application Insights zapadá do cyklu devOps
ms.topic: conceptual
ms.date: 12/21/2018
ms.openlocfilehash: bc897cc0259894964e0b6164219b1e5459e824b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100579558"
---
# <a name="deep-diagnostics-for-web-apps-and-services-with-application-insights"></a>Hloubková diagnostika webových aplikací a služeb pomocí Application Insights
## <a name="why-do-i-need-application-insights"></a>Proč potřebuji Application Insights?
Application Insights monitoruje spuštěnou webovou aplikaci. Dozvíte se o chybách a problémech s výkonem a pomůže vám analyzovat, jak zákazníci používají vaši aplikaci. Funguje pro aplikace běžící na mnoha platformách (ASP.NET, Java EE, Node.js,...) a jsou hostované v cloudu i v místním prostředí. 

![Aspekty složitosti doručování webových aplikací](./media/devops/010.png)

Je důležité monitorovat moderní aplikace, když je spuštěná. Nejdůležitější je, že chcete detekovat chyby před většinou svých zákazníků. Také budete chtít zjistit a opravit problémy s výkonem, které sice nejsou závažné, možná zpomalit nebo způsobit určité obtíže uživatelům. A když systém provádí Vaše spokojenost, budete chtít zjistit, co s tím uživatelé dělají, když používají nejnovější funkci? Je s ní Tato operace úspěšná?

Moderní webové aplikace se vyvíjí v cyklu průběžného doručování: vydání nové funkce nebo zlepšení; Sledujte, jak dobře funguje pro uživatele. Naplánujte další přírůstek vývoje na základě tohoto vědomí. Klíčovou součástí tohoto cyklu je pozorovací fáze. Application Insights poskytuje nástroje pro monitorování výkonu a využití webové aplikace.

Nejdůležitější aspekt tohoto procesu je diagnostika a diagnostika. Pokud dojde k chybě aplikace, bude společnost sledována. Hlavní rolí rozhraní pro monitorování je proto, aby bylo možné spolehlivě detekovat chyby, upozorňovat okamžitě a prezentovat informace potřebné k diagnostice problému. To je přesně to, co Application Insights.

### <a name="where-do-bugs-come-from"></a>Odkud přicházejí nějaké chyby?
Selhání webových systémů obvykle vznikají při potížích s konfigurací nebo špatné interakce mezi jejich mnoha komponentami. První úkol při řešení incidentu živého webu je proto identifikovat Locus problému: kterou součást nebo vztah způsobují?

Některé z nich s šedým vlasy si můžou pamatovat na jednodušší období, kdy počítačový program běžel v jednom počítači. Vývojáři ji před expedicí důkladně otestovali. a když ho dostanou, bude se ho na něj znovu zobrazovat nebo se na něj považovat. Uživatelé by museli v mnoha letech vydávat zbývající chyby. 

Věci se teď velmi liší. Vaše aplikace má spoustu různých zařízení, na kterých běží, a může být obtížné zaručit pro každé z nich přesné stejné chování. Hostování aplikací v cloudu znamená, že chyby je možné rychle opravit, ale také to znamená nepřetržitou soutěž a očekáváme nové funkce v častých intervalech. 

V těchto podmínkách je jediným způsobem, jak udržet pevně řídící počet chyb, automatizované testování částí. Pro každé doručení by nebylo možné ručně znovu otestovat vše. Test jednotek je nyní maloobchodech součástí procesu sestavení. Nástroje, jako je například Xamarin Test Cloud nápovědě, poskytují automatizované testování uživatelského rozhraní ve více verzích prohlížeče. Tyto testovací režimy umožňují, aby se doufáme, že četnost chyb nalezených v aplikaci může být zachována minimálně.

Typické webové aplikace mají spoustu živých komponent. Kromě klienta (v prohlížeči nebo v aplikaci zařízení) a na webovém serveru je pravděpodobně zásadní zpracování back-endu. Je možné, že back-end je kanálem součástí nebo volně se sbírka spolupracujících částí. A mnohé z nich nebudou v ovládacím prvku – jedná se o externí služby, na kterých závisíte.

V těchto konfiguracích může být obtížné a neekonomicky testovat nebo předpokládat všechny možné režimy selhání, jiné než v samotném živém systému. 

### <a name="questions-"></a>Dotazy...
Některé otázky vyžádáme, když vyvíjíme webový systém:

* Je moje chyba aplikace? 
* Co se přesně stalo? – Pokud se žádost nezdařila, chci vědět, jak tam byla. Potřebujeme trasovat události...
* Je moje aplikace dostatečně rychlá? Jak dlouho trvá reakce na typické požadavky?
* Může server zpracovat zatížení? V případě zvýšení rychlosti požadavků se doba odezvy drží konstantní?
* Jak reagují jsou moje závislosti – rozhraní REST API, databáze a další komponenty, které moje aplikace volá. Zejména v případě, že je systém pomalý, je moje součást nebo když Získávám pomalé odpovědi od někoho jiného?
* Je moje aplikace nahoru nebo dolů? Můžu se z celého světa zobrazit? Dejte mi informace, pokud se zastaví...
* Jakou hlavní příčinu máte? Došlo k chybě v mé součásti nebo závislosti? Jedná se o problém s komunikací?
* Kolik uživatelů má vliv? Pokud mám k boji více než jeden problém, který je nejdůležitější?

## <a name="what-is-application-insights"></a>Co je Application Insights?
![Základní pracovní postup pro Application Insights](./media/devops/020.png)

1. Application Insights instrumentuje vaši aplikaci a při spuštění aplikace pošle telemetrie. Můžete buď sestavit sadu Application Insights SDK do aplikace, nebo můžete použít instrumentaci za běhu. Předchozí metoda je flexibilnější, protože můžete přidat vlastní telemetrii k běžným modulům.
2. Telemetrii se pošle na portál Application Insights, kde se ukládá a zpracovává. (I když je Application Insights hostovaná v Microsoft Azure, může monitorovat všechny webové aplikace – nejen aplikace Azure.)
3. Telemetrii se zobrazí ve formě grafů a tabulek událostí.

Existují dva hlavní typy telemetrie: agregované a nezpracované instance. 

* Data instance obsahují například sestavu žádosti, kterou přijala vaše webová aplikace. Podrobnosti o žádosti můžete najít a zkontrolovat na portálu Application Insights pomocí nástroje pro hledání. Instance by obsahovala data, například jak dlouho aplikace trvala na žádost reagovat, a také požadovanou adresu URL, přibližné umístění klienta a další data.
* Agregovaná data zahrnují počty událostí na čas jednotky, abyste mohli porovnat rychlost požadavků s dobami odezvy. Obsahuje také průměrnou metriku, jako je například doba odezvy na žádosti.

Hlavní kategorie dat:

* Požadavky na vaši aplikaci (obvykle požadavky HTTP), s daty na adrese URL, dobou odezvy a úspěchem nebo selháním.
* Závislosti a volání SQL provedené vaší aplikací, a to i s identifikátorem URI, dobou odezvy a úspěchem
* Výjimky, včetně trasování zásobníku.
* Data zobrazení stránky, která pocházejí z prohlížečů uživatelů.
* Metriky, jako jsou čítače výkonu, a také metriky, které zapisujete sami. 
* Vlastní události, které můžete použít ke sledování obchodních událostí
* Trasování protokolu používané pro ladění.

## <a name="case-study-real-madrid-fc"></a>Případová studie: Real Madrid F.C.
Webová služba [skutečného madridského fotbalového klubu](https://www.realmadrid.com/) zajišťuje přibližně 450 000 000 ventilátorů po celém světě. Ventilátory mají přístup přes webové prohlížeče i mobilní aplikace pro klub. Ventilátory nemůžou mít jenom lístky, ale také mají přístup k informacím a videoklipům na výsledcích, přehrávačích a nadcházejících hrách. Můžou hledat pomocí filtrů, jako je počet skóre výsledků. K dispozici jsou také odkazy na sociální média. Činnost koncového uživatele je vysoce přizpůsobená a je navržena jako obousměrná komunikace pro zapojení ventilátorů.

Řešení [je systém služeb a aplikací na Microsoft Azure](https://www.microsoft.com/inculture/sports/real-madrid/). Škálovatelnost je klíčovým požadavkem: provoz je proměnlivý a může dosáhnout velmi velkých svazků během a kolem shody.

Pro reálný Madrid je důležité monitorovat výkon systému. Azure Application Insights poskytuje komplexní zobrazení v celém systému a zajišťuje spolehlivou a vysokou úroveň služby. 

Klub taky získá podrobné porozumění jeho ventilátorům: kde jsou (jenom 3% jsou ve Španělsku), jaký je jejich zájem v přehrávačích, historických výsledcích a nadcházejících hrách a jak reagují na výsledek.

Většina těchto dat telemetrie se automaticky shromáždí bez přidávaného kódu, což zjednodušuje řešení a snižuje provozní složitost.  Pro reálný Madrid Application Insights se každý měsíc zabývá 3 800 000 000 bodů telemetrie.

Real Madrid používá modul Power BI k zobrazení telemetrie.

![Power BI zobrazení telemetrie Application Insights](./media/devops/080.png)

## <a name="smart-detection"></a>Inteligentní zjišťování
[Proaktivní Diagnostika](./proactive-diagnostics.md) je nedávná funkce. Bez jakékoli speciální konfigurace Application Insights automaticky detekuje a upozorňuje na neobvyklé zvýšení sazeb za selhání vaší aplikace. Je dostatečně inteligentní, aby bylo možné ignorovat obtíže s příležitostnými chybami, a také hry, které jsou v rámci požadavků jednoduše přiměřené. Pokud například dojde k selhání v jedné ze služeb, na které jste zapracovali, nebo pokud nové sestavení, které jste právě nasadili, nefunguje dobře, budete o tom informovat, jakmile se podíváte na e-mail. (A k dispozici jsou Webhooky, abyste mohli aktivovat jiné aplikace.)

Další aspekt této funkce provádí každodenní analýzu telemetrie a hledá neobvyklé vzorce výkonu, které je obtížné zjistit. Například může najít pomalý výkon přidružený k určité geografické oblasti nebo konkrétní verzi prohlížeče.

V obou případech výstraha nejen upozorní na to, co se zjistilo, ale také poskytuje data, která potřebujete k tomu, abyste mohli nastavovat problémy, jako jsou třeba relevantní sestavy výjimek.

![E-mail z proaktivní diagnostiky](./media/devops/030.png)

Zákazník Samtec: "během nedávné přímou migraci funkce jsme zjistili, že databáze byla v horizontálním měřítku, která byla v souladu s omezeními prostředků a způsobila vypršení časových limitů. Výstrahy proaktivní detekce byly dodány doslova, protože jsme tento problém rozložili, a to téměř v reálném čase jako inzerované. Tato výstraha se společně s výstrahami platformy Azure pomohla téměř okamžitě vyřešit problém. Celkový výpadek < 10 minut. "

## <a name="live-metrics-stream"></a>Live Metrics Stream
Nasazení nejnovějšího sestavení může být prostředí nervózním. Pokud dojde k nějakým potížím, měli byste o nich mít informace hned, abyste mohli v případě potřeby zálohovat. Live Metrics Stream poskytuje klíčové metriky s latencí přibližně jedna sekunda.

![Živé metriky](./media/devops/0040.png)

A umožňuje okamžitě zkontrolovat ukázku všech selhání nebo výjimek.

![Události živého selhání](./media/devops/002-live-stream-failures.png)

## <a name="application-map"></a>Mapa aplikace
Mapa aplikace automaticky zjišťuje topologii vaší aplikace, vyhledává informace o výkonu a umožňuje snadno identifikovat problémová místa výkonu a problematické toky napříč distribuovaným prostředím. Umožňuje zjistit závislosti aplikací ve službách Azure. Můžete určit jejich třídění tím, že se seznámíte s tím, že se jedná o související s kódem nebo závislosti a z jednoho místa k zobrazení související diagnostické prostředí. Například vaše aplikace může selhat z důvodu snížení výkonu v úrovni SQL. S mapou aplikace ji můžete okamžitě zobrazit a přejít k podrobnostem o prostředí SQL Index Advisor nebo dotazům na přehled.

![Mapa aplikace](./media/devops/0050.png)

## <a name="application-insights-analytics"></a>Analýzy Application Insights
Pomocí [analýzy](../logs/log-query-overview.md)můžete psát libovolné dotazy v výkonném jazyce podobném jazyku SQL.  Diagnostikování celého zásobníku aplikací je jednoduché, protože se připojí různé perspektivy a můžete klást správné otázky ke korelaci výkonu služby s obchodními metrikami a zákaznickým prostředím. 

Můžete zadat dotaz na veškerou instanci telemetrie a nezpracovaná data metrik uložená na portálu. Tento jazyk zahrnuje filtry, spojení, agregace a další operace. Můžete vypočítat pole a provádět statistickou analýzu. K dispozici jsou tabulkové i grafické vizualizace.

![Graf analytického dotazu a výsledků](./media/devops/0025.png)

Například je snadné:

* Segmentujte údaje o výkonu své aplikace na úrovni zákazníka, abyste porozuměli jejich zkušenostem.
* Při vyšetřování živých lokalit můžete hledat konkrétní kódy chyb nebo názvy vlastních událostí.
* Procházejte k podrobnostem o využití aplikace konkrétními zákazníky, abyste pochopili, jak se funkce získávají a přijímají.
* Sledujte relace a doby odezvy pro konkrétní uživatele a umožněte tak podpoře a provozním týmům poskytovat okamžitou zákaznickou podporu.
* Určete často používané funkce aplikací pro zodpovězení otázek stanovení priorit funkcí.

Zákazník DNN říká: "Application Insights nám poskytla chybějící část rovnice pro možnost kombinovat, řadit, dotazovat a filtrovat data podle potřeby. Umožnění našeho týmu používat vlastní ingenuity a prostředí k hledání dat s výkonným dotazovacím jazykem nám umožnilo najít přehledy a řešit problémy, které jsme ještě nevěděli. Spousta zajímavých odpovědí přichází z otázek, které začínají na *"I Wonder", pokud...*

## <a name="development-tools-integration"></a>Integrace nástrojů pro vývoj
### <a name="configuring-application-insights"></a>Konfigurace Application Insights
Sada Visual Studio a zatmění mají nástroje pro konfiguraci správných balíčků sady SDK pro projekt, který vyvíjíte. K přidání Application Insights je k dispozici příkaz nabídky.

Pokud se rozhodnete použít protokolovací rozhraní trasování, jako je Log4N, NLog nebo System. Diagnostics. Trace, získáte možnost Odeslat protokoly do Application Insights společně s další telemetrie, abyste mohli snadno sladit trasování s požadavky, voláními závislostí a výjimkami.

### <a name="search-telemetry-in-visual-studio"></a>Hledání telemetrie v aplikaci Visual Studio
Při vývoji a ladění funkce můžete telemetrie zobrazit a vyhledat přímo v aplikaci Visual Studio pomocí stejných vyhledávacích funkcí jako na webovém portálu.

A když Application Insights zaznamená výjimku, můžete zobrazit datový bod v aplikaci Visual Studio a přejít přímo na příslušný kód.

![Hledání v aplikaci Visual Studio](./media/devops/060.png)

Během ladění máte možnost zachovat telemetrii ve vývojovém počítači, zobrazit ji v aplikaci Visual Studio, ale bez odeslání na portál. Tato místní možnost zabraňuje smíchání ladění s produkční telemetrie.

### <a name="work-items"></a>Pracovní položky
Když je vyvolána výstraha, Application Insights může automaticky vytvořit pracovní položku v systému pro sledování práce.

## <a name="but-what-about"></a>Ale co...?
* [Ochrana osobních údajů a úložiště](./data-retention-privacy.md) – vaše telemetrie se uchovávají na zabezpečených serverech Azure.
* Výkon – dopad je velmi nízký. Telemetrii je dávkovaná.
* [Ceny](./pricing.md) – můžete začít zdarma a pokračovat, i když jste v nedostatku objemu.


## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Další kroky
Začínáme s Application Insights je snadné. Hlavní možnosti jsou:

* [Servery služby IIS](./monitor-performance-live-website-now.md)a také pro [Azure App Service](./app-insights-overview.md).
* Instrumentujte svůj projekt během vývoje. To můžete provést pro aplikace [ASP.NET](./asp-net.md) nebo [Java](./java-get-started.md) a také [Node.js](./nodejs.md) a hostitele [jiných typů](./platforms.md). 
* Vyinstrumentujte [jakoukoli webovou stránku](./javascript.md) přidáním krátkého fragmentu kódu.

