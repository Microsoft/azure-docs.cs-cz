---
title: Cortana Intelligence řešení šablony Playbook pro prognózování poptávky po energii
description: Šablona řešení s Microsoft Cortana Intelligence, která pomáhá prognózy poptávky pro firmu energii utility.
services: machine-learning
author: ilanr9
manager: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/24/2016
ms.author: garye
ms.openlocfilehash: 40bff35c1136d55e968a287d259f2304252f5248
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2019
ms.locfileid: "55078776"
---
# <a name="cortana-intelligence-solution-template-playbook-for-demand-forecasting-of-energy"></a>Cortana Intelligence řešení šablony Playbook pro prognózování poptávky po energii
## <a name="executive-summary"></a>Shrnutí
V posledních několika let Internet of Things (IoT), alternativních zdrojů energie a velké objemy dat mají sloučeny pro tvorbu obrovské příležitosti v doméně nástroj a energie. Ve stejnou dobu nástroj a celý energetickém sektoru viděli spotřeby sloučení navýšení kapacity s vysokými nároky na lepší způsoby, jak řídit využití energie příjemci. Proto nástroj a inteligentní mřížky společností jsou v skvělé potřeba inovovat a obnovení sami. Navíc mnoho napájení a nástroj mřížky se stávají zastaralé a udržovat a spravovat velmi nákladné. Během posledního roku tým pracuje na celé řadě engagements v rámci domény energie. Během těchto výstav a došlo k mnoha případech, ve kterých byly hledání nezávislí výrobci softwaru (nezávislým výrobcům softwaru) nebo nástroje do Prognózování pro budoucí energii. Tyto předpovědi hraje důležitou roli v jejich současným i budoucím obchodním a staly základ pro různé případy použití. Patří mezi ně předpověď zatížení krátkodobé a dlouhodobé napájení, obchodování, Vyrovnávání zatížení, mřížky optimalizace atd. Velké objemy dat a pokročilé analýzy (AA) metody, jako je Machine Learning (ML) jsou klíčových předpokladů pro vytvoření spolehlivé a přesné prognózy.

V tomto playbook jsme připravili firmy a analytické pokyny potřebné pro úspěšný vývoj a nasazení poptávku po energii prognózy řešení. Tyto navržené pokyny vám mohou pomoci nástroje, odborníci přes data a datovými architekty při vytváření řešení plně zprovozněné založené na cloudu, Prognózování poptávky. Pro společnosti, kteří zrovna začínáte jejich velké objemy dat a pokročilé analýzy cesty toto řešení může představovat počátečních v jejich dlouhodobou strategii inteligentní mřížky.

> [!TIP]
> Stáhněte si diagram, který obsahuje základní informace o architektuře této šablony, najdete v článku [architekturu šablony řešení Cortana Intelligence pro prognózování poptávky po energii](cortana-analytics-architecture-demand-forecasting-energy.md).
>
>

## <a name="overview"></a>Přehled
Tento dokument popisuje firmy, data a technické aspekty využívající Cortana Intelligence a v konkrétní Azure Machine Learning (AML) pro implementaci a nasazení řešení Prognózování energie. Dokument se skládá ze tří hlavních částí:

1. Principy podniku
2. Pochopení dat
3. Technická implementace

**Obchodní vysvětlení** popisuje část aspekty business jeden je potřeba pochopit a vzít v úvahu před rozhodování o investici. Vysvětluje, jak zařadit obchodní problém aktuální a ověřte, machine learning a prediktivní analýzy jsou skutečně efektivní a použitelný. Další dokument vysvětluje základy strojového učení a jak se používá k řešení problémů Prognózování energie. Poskytuje přehled o požadavcích a kritéria kvalifikace případu použití. Některé ukázky použít případy a obchodní případ scénáře jsou také k dispozici.

Data jsou hlavní složkou pro jakékoli řešení strojového učení. **Pochopení dat** část tohoto dokumentu popisuje některé důležité aspekty data. Poskytuje přehled o druh dat, která je potřebná pro vytváření prognóz energie, požadavky na kvalitu dat a k jakým zdrojům dat obvykle existují. Také vysvětlují, jak se nezpracovaná data slouží k přípravě dat funkce, které skutečně jednotka části modelování.

Třetí části dokumentu se vztahuje **technickou implementaci** aspekt řešení. Vytváření funkcí a modelování jsou základem vědecké zpracování dat a jsou proto jsou podrobně popsány v některé. Popisuje pojem webové služby, které jsou důležité prostředkem pro nasazení řešení prediktivní analýzy v cloudu. Můžeme také popisují typickou architekturu řešení zprovozněné začátku do konce.

Dokument navíc obsahuje referenční materiál, který vám umožní získat další znalosti domény a technologie.

Je důležité si uvědomit, že neplánujeme zahrnují v tomto dokumentu hlubší vědecké zpracování dat, jejich matematické a technické aspekty. Tyto podrobnosti můžete najít v [dokumentace ke službě Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) a [blogy](https://blogs.microsoft.com/blog/tag/azure-machine-learning/).

### <a name="target-audience"></a>Cílová skupina
Cílová skupina pro tento dokument se obchodní a technické pracovníky, kteří by chtěli získat znalosti, a porozumění strojového učení na základě řešeními a jak jsou použity konkrétně v rámci domény Prognózování energie.

Odborníci přes data mohou také těžit z čtení tento dokument, abyste lépe porozuměli základní proces, který řídí nasazení řešení Prognózování energie. V tomto kontextu se můžete také použije k navázání dobrá směrný plán a výchozí bod pro další podrobné a rozšířené materiálu.

### <a name="industry-trends"></a>Oborové trendy
V posledních několika let mají velké objemy dat, IoT a alternativních zdrojů energie sloučeny pro tvorbu obrovské příležitosti v prostoru nástroje a energie. Ve stejnou dobu nástroj a sektory celý energie viděli spotřeby sloučení navýšení kapacity s vysokými nároky na lepší způsoby, jak řídit využití energie příjemci.

Mnoho nástrojů a inteligentní energetickým společnostem byla průkopnický mít [inteligentní mřížky](https://en.wikipedia.org/wiki/Smart_grid) nasazením počet použití případů, které usnadňují používání dat vygenerovaných vašimi mřížky. Mnoho případy použití točí kolem základní vlastnosti výroby elektrické energie: nemůže být sbírají ani jste si poznamenali uložené jako inventáře. To co je vytvořen musí být použita. Nástroje, které chcete se stát efektivnější potřebovat k předvídání spotřeby energie jednoduše vzhledem k tomu, který vám poskytne větší možnost **vyvážit nabídka a poptávka**, zamezuje tak energie ztráty **snížit skleníkových plynu emise**a řízení nákladů.

Když mluvíme o nákladech, je další důležitý aspekt, což je cena. Nové možnosti pro využití výkonu mezi nástroje v skvělých věcí a potřebovali si dali **předpovídat budoucí poptávku a budoucí ceny elektřiny**. To může pomoct určit jejich svazky výrobní společnosti.

Pokud použijeme slovo "inteligentní", ve skutečnosti označujeme mřížky, můžete další informace a pak proveďte předpovědi. To předvídat sezónními změnami v spotřeby stejně jako **přijetím dočasné přetížení a automaticky pro něj nastavit**. Ve vzdálené řízení spotřeby (s pomocí těchto inteligentních měřičů), může být zpracována lokalizované přetížení. **Nejprve předpověď a pak funguje**, mřížky umožňuje efektivněji v čase.

Pro zbývající část tohoto dokumentu se zaměříme na konkrétní řady případů použití, které se týkají předvídání budoucí krátkodobé a dlouhodobé poptávku po energii. Byla práce v těchto oblastech na několik měsíců jsme získali nějaké znalosti a dovednosti, díky kterým můžou můžeme vytvářet výsledky na podnikové úrovni odvětví. Dalších případů použití se bude vztahovat i v dokumentu v blízké budoucnosti.

## <a name="business-understanding"></a>Obchodní vysvětlení
### <a name="business-goals"></a>Obchodní cíle
**Energie ukázka** cílem je předvést typické prediktivní analýzy a machine learningu řešení, které je možné nasadit v krátkém časovém rámci. Konkrétně je naše aktuální aktivní tak, aby jeho obchodní hodnotu můžete rychle realizovat a využít při povolování řešení prognózy poptávky energie. Informace v tomto playbook může pomoci zákazníka provádění následujících cílů:

* Krátkého formátu času na hodnotu strojového učení na základě řešení
* Schopnost rozšiřovat pilotní nasazení mít velikost dalších případů použití nebo širším rozsahem podle svých obchodních potřeb
* Rychle získat znalostní bázi produktů Cortana Intelligence Suite

S těmito cíli v úvahu zaměřuje playbook v poskytování obchodních a technických znalostí, které vám pomůže při dosažení těchto cílů.

### <a name="power-load-and-demand-forecasting"></a>Napájecí zátěž a Prognózování poptávky
V energetickém sektoru může být mnoho způsobů, které poptávky Prognózování pomáhá při řešení kritických obchodních problémů. Ve skutečnosti Prognózování poptávky po lze považovat za základem pro mnoho základní případy použití v tomto odvětví. Obecně platí, budeme přemýšlet o dva druhy prognózy poptávky energie: krátkodobé a dlouhodobé. Každý z nich může sloužit k jinému účelu a využívat jiný přístup. Hlavní rozdíl mezi nimi je prognóz horizontu, to znamená časové rozmezí, do budoucna, u kterého jsme by prognózy.

#### <a name="short-term-load-forecasting"></a>Krátká období zatížení Prognózování
V rámci kontextu poptávku po energii krátká období načíst Prognózování (STLF) je definován jako agregované zatížení, která je naplánované v blízké budoucnosti na různé části mřížce (nebo jako celek). V tomto kontextu je krátkodobá definován jako časový horizont v rozsahu 1 hodina 24 hodin. V některých případech také je možné horizont 48 hodin Proto je velmi běžné v případě provozních pomocí mřížky STLF. Tady je několik příkladů STLF řízené případy použití:

* Nabídka a poptávka vyrovnávání
* Podpora Power obchodování
* Provádění trh (nastavení napájení cena)
* Optimalizace provozní mřížky
* [Poptávka reaguje](https://en.wikipedia.org/wiki/Demand_response)
* Ve špičce poptávky
* Správa na straně poptávky
* Vyrovnávání zatížení a přetížení ochrany před únikem informací
* Dlouhodobé Prognózování zatížení
* Selhání a detekce anomálií
* Ve špičce curtailment/vyrovnání

STLF model jsou většinou založený na téměř minulosti (posledního dne nebo týdne) data o spotřebě a použití naplánované teploty jako důležité údaje. Získání přesných teploty prognózy příští hodinu a až 24 hodin se mění na méně náročné nyní dnů. Tyto modely jsou méně citlivé na sezónní vzorů nebo dlouhodobé trendy spotřeby energie.

SLTF řešení můžou také generovat velkému počtu prediktivních volání (žádostí o službu) vzhledem k tomu, že jsou vyvolání po hodinách a v některých případech i s vyšší frekvencí. Je také velmi běžné zobrazíte implantaci, kde každé jednotlivé transformovny nebo transformer je vyjádřena jako samostatný model a proto jsou ještě větší objem požadavků předpovědi.

#### <a name="long-term-load-forecasting"></a>Dlouhodobé Prognózování zatížení
Cílem z dlouhé termín zatížení Prognózování (LTLF) je předpovídá poptávku power s časový horizont od 1 týden na několik měsíců (a v některých případech pro řadu let). Tento rozsah horizontu je ve většině případů platí pro plánování a případy použití investice.

Pro dlouhodobou scénáře je důležité mít vysoce kvalitní data, které pokrývá rozsah za několik let (minimální 3 roky). Tyto modely se obvykle extrahovat sezónnosti vzorů z historických dat a použití externí predicators například jako počasí a klimatických vzory.

Je důležité, aby se vyjasnilo, že čím delší je prognóz chystané, tím méně přesné prognózy může být. Je proto potřeba vytvořit některé intervalu spolehlivosti spolu s skutečné předpovědí, která by umožňovala lidí se faktorovat možné změnu na proces plánování.

Protože scénáře spotřeby LTLF je většinou plánování, můžete Očekáváme, že mnohem nižší předpovědi svazky (porovnání s STLF). By obvykle najdete v těchto předpovědí součástí vizualizačních nástrojů, jako je Excel nebo Power BI jsme ji vyvolat ručně uživatelem.

### <a name="short-term-vs-long-term-prediction"></a>Krátká období vs. Dlouhodobé Predikcí
Následující tabulka porovnává STLF a LTLF z hlediska vašich nejdůležitějších atributy:

| Atribut | Zatížení krátkodobou předpověď | Dlouhodobé předpověď zatížení |
| --- | --- | --- |
| Forecast Horizon |Od 1 hodiny do 48 hodin |Od 1 do 6 měsíců |
| Členitost dat |Každou hodinu |Každou hodinu nebo každý den |
| Typické případy použití |<ul><li>/ Poptávky vyrovnávání</li><li>Vyberte hodinu Prognózování</li><li>Poptávka reaguje</li></ul> |<ul><li>Dlouhodobé plánování</li><li>Plánování prostředků mřížky</li><li>Plánování prostředků</li></ul> |
| Typické prediktory |<ul><li>Dne nebo týdne</li><li>hodiny dne</li><li>Hodinové teploty</li></ul> |<ul><li>Měsíc roku</li><li>Den v měsíci</li><li>Dlouhodobé teploty a klimatu</li></ul> |
| Rozsah historických dat |Data za dvě až tři roky |Za 5 až 10 let dat. |
| Typické přesnost |MAPE * 5 % nebo nižším |MAPE * 25 % nebo nižším |
| Prognózy frekvence |Vytváří každou hodinu nebo každých 24 hodin |Vytváří jednou měsíčně, čtvrtletně nebo ročně |

\*[MAPE](https://en.wikipedia.org/wiki/Mean_absolute_percentage_error) – znamenat průměrné procento chyb

Jak je vidět z této tabulky, je velmi důležité rozlišovat mezi krátkodobém a dlouhodobém horizontu Prognózování scénáře, jako ty představují potřebám různých firem a mohou mít různé nasazení a jejich vzorce spotřeby.

### <a name="example-use-case-1-esmart-systems--overload-optimization"></a>Příklad použití případ 1: eSmart systémy – optimalizace přetížení
Důležité role [inteligentní mřížky](https://en.wikipedia.org/wiki/Smart_grid) je dynamicky a průběžně optimalizace a optimalizace pro změnu vzorce spotřeby. Spotřeba energie může mít dopad na krátkodobou změnou hlavně způsobených fluktuace teploty (*třeba*, vyšší výkon se používá pro podmínku air nebo vytápění). Ve stejnou dobu spotřebu energie ovlivňuje také dlouhodobé trendy. Ty mohou obsahovat sezónnosti efekty, státních svátků v USA, dlouhodobé růstu využití a dokonce ekonomické faktorů, jako je index příjemce, cena ropy a HDP.

V tomto případě se [eSmart](https://www.esmartsystems.com/) kterých chcete nasadit cloudové řešení, která umožňuje předpověď tendence situace přetížení na jakékoli dané transformovny mřížky. Zejména eSmart chtěli identifikovat Rozvodny, které jsou pravděpodobně přetížení během následující hodiny, takže lze provést okamžitou akci k zamezení nebo řešení této situace.

Přesný a rychlé provádění předpovědi vyžaduje implementaci ze tří prediktivní modelů:

* Dlouhá období model, který umožňuje předvídání spotřeby energie v každé transformovny během další několik týdnů či měsíců
* Krátkodobá model, který umožňuje předpovědi přetížení situace na daný transformovny během příští hodina
* Model teploty, který poskytuje předpovědi budoucích teploty přes různé scénáře

Cíl dlouhodobé modelu má pořadí Rozvodny podle jejich tendence k přetížení (vzhledem k jejich přenos kapacita napájení) během další týden nebo měsíc. To umožňuje vytváření krátký seznam Rozvodny, které bude sloužit jako vstup pro krátkodobou předpověď. Teplota je důležité údaje pro dlouhodobé model, je potřeba neustále vytvářejí prognózy teplota několika scénáři a jejich vstupu jako vstup do dlouhodobého modelu. Potom je volána krátkodobou předpověď předpovědět, které transformovny je pravděpodobně přetížení přes do příští hodiny.

Modely krátkodobé a dlouhodobé se nasazují samostatně za každý transformovny. Proto praktické provádění těchto modelů vyžaduje rozsáhlou Orchestrace. Získat vyšší přesnost předpovědi v krátkodobém horizontu podrobnější modelu je vyhrazen pro každou hodinu dne. Tyto modely jsou spouštěny každou hodinu a dokončí provádění během několika minut, aby bylo dost času reagovat a přijmout preventivní opatření v případě potřeby. Tato kolekce modelů je pořád aktuální pomocí pravidelné retraining nejnovější data.

Můžete najít další informace o tento případ použití [tady](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18945).

#### <a name="use-case-qualification-criteria--prerequisites"></a>Použít kritéria případu kvalifikace – požadavky
Hlavní sílu Cortana Intelligence je v jeho výkonné schopnost nasadit a škálovat na střed řešení strojového učení. Je navržen pro podporu tisíc predikcí, které jsou spuštěny souběžně. Může automaticky škálovat podle měnící vzor spotřeby. Zaměření řešení, tedy na přesnost a výpočetní výkon. Například je nástroj společnosti zájem o vytváření poptávky po energii přesné prognózy do příští hodiny a pro každou hodinu dne. Na druhé straně nás zajímají méně odpovídání na otázku, proč poptávky očekává se, že je potřeba jako ji (samotného modelu se postará o který).

Proto je důležité si uvědomit, že všechny případy použití a obchodní problémy můžete efektivně vyřešit pomocí služby machine learning.

Cortana Intelligence a machine learning může být velmi efektivní při řešení daného obchodních problémů, když se splní následující kritéria:

* Je obchodního problému v dolním **prediktivní** ze své podstaty. Případu příklad prediktivní použití je společnost nástroj, který chcete předpovědět napájecí zátěž na daný transformovny během následující hodiny. Na druhé straně, analýze a hodnocení ovladače historických vyžádání by **popisný** ze své podstaty a proto méně použitelné.
* Je zřejmé **cestu akce** mají být provedeny, když do predikce. je k dispozici. Například předpověď přetížení na transformovny během následující hodiny můžete aktivovat proaktivní akce snížení zatížení, který je přidružený k této transformovny a potenciálně zamezuje tak přetížení.
* Představuje případu použití **typický typ problému** tak, aby při vyřešit ho pave způsob, jakým případy použití pro jiné podobné řešení.
* Můžete nastavit zákazník **kvantitativní a kvalitativní cíle** k předvedení implementací úspěšné řešení. Například by dobré kvantitativní cíl pro prognózu poptávky energie prahová hodnota požadovaná přesnost (*např*, je povolená až 5 % došlo k chybě) nebo pokud předpověď transformovny přetížení pak přesnosti (počet pravdivě pozitivní) a odvolání (rozsah pravdivě pozitivní) by měla být vyšší než dané prahové hodnoty. Tyto cíle by měl být odvozen od zákazníka obchodních cílů.
* Je zřejmé **scénáře integrace** s pracovní postup společnosti vaší společnosti. Předpověď zatížení transformovny například je možné integrovat do control centeru mřížky umožňující přetížení aktivity ochrany před únikem informací.
* Zákazník má připraven k použití **data s dostatečnou kvality** pro podporu případ použití (zobrazit více v další části **kvality dat**, z playbook).
* Architektura poskytuje výkonný zaměřenou na data v cloudu zákazníka nebo **založené na cloudu strojového učení**, včetně Azure ML a dalších součástí Cortana Intelligence Suite.
* Je zákazník chce vytvořit **tok, který koncového data** tohoto zařízení doručování dat do cloudu průběžně a je ochotná **zprovoznění** řešení.
* Zákazník je připraven k **vyhradit prostředky** kdo bude mít aktivně zabývám během počáteční implementace pilotního nasazení tak, aby znalostní báze a vlastnictví řešení lze přenést do zákazníků po úspěšném dokončení.
* By měl být prostředku zákazníka **zkušený data professional**, pokud možno mezi odborníky přes data.

Kvalifikace případu použití podle výše uvedených kritérií můžete výrazně zlepšit míru úspěšných případu použití a vytvořit funkční útok typu beachhead pro implementaci případy budoucí použití.

### <a name="cloud-based-solutions"></a>Cloudová řešení
Cortana Intelligence Suite v Azure je integrované prostředí, které se nacházejí v cloudu. Nasazení řešení pokročilých analýz v cloudovém prostředí obsahuje značné výhody pro firmy a v době, může to znamenat velkou změnu pro společnosti, že stále použít místní IT řešení. V energetickém sektoru je jasné trend postupné migrace operací do cloudu. Tento trend přejde ruku v ruce spolu s vývoj inteligentních mřížky jak je popsáno výše, v **trendů odvětví**. Jak tento playbook se zaměřuje na cloudové řešení v doméně energie, je potřeba popisují výhody a další důležité informace o nasazení řešení založené na cloudu.

Možná největší výhodou cloudové řešení je náklady. Jako řešení využívá cloud nasazuje komponenty neexistuje žádné pořizovací náklady ani COGS (cena prodaného zboží) komponenta náklady spojené s ním. To znamená, že není nutné investovat do hardwaru, softwaru a údržbu IT, a proto není podstatné omezení objemu obchodní riziko.

Další důležitou výhodou je strukturu nákladů s průběžnými platbami cloudových řešení. Servery založené na cloudu pro výpočty a úložiště je možné nasadit a škálovat na základě právě podle potřeby. Reprezentuje efektivitu nákladů výhod cloudové řešení.

A konečně není nutné pro investic do IT údržby nebo budoucí infrastruktury, vývoje, jak to vše je součástí nabídky založené na cloudu. Rozsahu Cortana Intelligence Suite zahrnuje nejlepší služby třídy a udržuje svůj Znovunačtení vyvíjejí. Nové funkce, komponenty a funkce jsou neustále zavlečení a vyvíjejí.

Pro společnosti, který právě spouští jeho přechodu do cloudu vám důrazně doporučujeme provést postupného díky implementaci cloudových migrace přehled toho. Věříme, že nástroje a společností ve službě domény energie, případy použití, které jsou popsané v tomto playbook představují vynikající příležitost pro pilotní nasazení řešení prediktivní analýzy v cloudu.

#### <a name="business-case-justification-considerations"></a>Důležité informace o obchodních případů odůvodnění
V mnoha případech může být zákazník zájem obchodní odůvodnění pro danou použití případ, ve kterém cloudové řešení a Machine Learning jsou důležitou součástí. Na rozdíl od místního řešení, v případě cloudové řešení komponenta počátečních nákladů je minimální a většinu prvků nákladů jsou spojeny s aktuálním využití. Při rozhodování o nasazení řešení v sadě Cortana Intelligence Suite Prognózování energie, několik služeb, které je možné integrovat s jednu společnou strukturu nákladů. Například databáze (*třeba*, SQL Azure) slouží k ukládání nezpracovaných dat a pak pro skutečný předpovědi s Azure ML se používá k hostování prognóz služeb. V tomto příkladu může obsahovat struktura náklady na úložiště a transakčních komponent.

Na druhé straně jedna by měl mít dostatečné povědomí o obchodní hodnoty jazyka provoz poptávku po energii Prognózování (krátkodobého nebo dlouhodobého hlediska). Ve skutečnosti je důležité si uvědomit obchodní hodnotu každé prognózy operace. Například přesné Prognózování napájecí zátěž dobu následujících 24 hodin může zabránit nadbytečné produkce nebo může pomoct zabránit přetížení mřížky a to může být vyjadřuje z hlediska finanční úspora každý den.

Základní vzorce pro výpočet finanční výhody vyžádání prognózy řešením může být: ![Základní vzorce pro výpočet finanční výhody vyžádání prognózy řešení](media/cortana-analytics-playbook-demand-forecasting-energy/financial-benefit-formula.png)

Protože Cortana Intelligence Suite poskytuje cenového modelu s průběžnými platbami, není nutné pro něj hradit fixní částku součásti na tento vzorec. Tento vzorec může vypočítat každý den, měsíční nebo roční.

Aktuální Cortana Intelligence Suite a Azure ML cenových plánech najdete [tady](https://azure.microsoft.com/pricing/details/machine-learning/).

### <a name="solution-development-process"></a>Proces vývoje řešení
Vývojový cyklus poptávku po energii prognóz řešení obvykle zahrnuje 4 fází, které bychom použít cloudové technologie a služby v sadě Cortana Intelligence Suite.

To je znázorněno v následujícím diagramu:

![Cyklus inteligentní mřížky](media/cortana-analytics-playbook-demand-forecasting-energy/smart-grid-cycle.png)

Následující odstavce popisuje tento proces krok 4:

1. **Shromažďování dat** – všechny pokročilé řešení pro analýzu na základě závisí na datech (naleznete v tématu **pochopení dat**). Konkrétně pokud jde o prediktivní analýzy a prognózy, spoléháme na probíhající, dynamické tok dat. V případě energie Prognózování poptávky, tato data můžete použít jako zdroj přímo z inteligentních měřičů, nebo již agregovat v databázi v místním prostředí. Spoléháme se také na dalších externích zdrojů dat, třeba o počasí a teploty. Tento probíhající tok dat nutné orchestrované, naplánovat a uložit. [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) (ADF) je náš hlavní centrem k provedení této úlohy.
2. **Modelování** – pro prognózy přesným a spolehlivým energie, jeden musí vývoj (regrese) a skvělé modelu, který umožňuje použít historických dat a extrahuje smysluplné a prediktivní vzory v datech udržovat. V oblasti nástroje Machine Learning (ML) se rychle rozrůstá pokročilé algoritmy pravidelně vyvíjených. Azure ML Studio poskytuje skvělé uživatelské prostředí, které pomáhá využívat nejpokročilejší algoritmů ML v úplný pracovní postup. Tento pracovní postup je znázorněn v intuitivním vývojový diagram a zahrnuje přípravy dat, funkce extrakce, modelování a vyhodnocení modelu. Uživatel pracovat stovky různých modelů, které jsou zahrnuty v tomto prostředí. Na konci této fáze mezi odborníky přes data budou mít funkční modelu, který plně Vyhodnocená a připravený k nasazení.

   Následující diagram není moc ilustraci obvyklý pracovní postup:

   ![Modelování pracovního postupu](media/cortana-analytics-playbook-demand-forecasting-energy/modeling-workflow.png)
3. **Nasazení** – podpoříte modelem funkční spolupráce, dalším krokem je nasazení. Tady modelu je převést na webovou službu, která zveřejňuje rozhraní RESTful API, který lze vyvolat souběžně přes Internet z různých klientů spotřeby. Služba Azure ML poskytuje jednoduchý způsob nasazení modelu přímo z Azure ML Studio jediným kliknutím na tlačítko. Celý proces nasazení se stane pod pokličkou. Toto řešení může automaticky škálovat podle požadované využití.
4. **Spotřeba** – v této fázi se ve skutečnosti Usnadňujeme modelu prognózy lze použít k vytvoření predikcí. Využití můžete řídit z aplikace v jazyce uživatele (*třeba*, řídicího panelu) nebo přímo z operačního systému, jako/poptávky vyrovnávání systému nebo řešení pro optimalizaci mřížky. Mohou být řízeny více případů použití, z jednoho modelu.

## <a name="data-understanding"></a>Pochopení dat
Po pokrývající důležité obchodní informace (viz **obchodní vysvětlení**) řešení Prognózování poptávky energie, jsme připraveni teď můžete projednávat část dat. Libovolné řešení prediktivní analýzy závisí na datech spolehlivé. Pro energie Prognózování poptávky, spoléháme na historické spotřeby dat pomocí různých úrovní členitosti. Historická data se používá jako suroviny. Projdou pečlivé analýzy, ve kterém bude mezi odborníky přes data identifikovat prediktory (označované také jako funkce), které můžou být přepnuté do modelu, který bude nakonec Generovat požadované prognózy.

Ve zbytku této části článků popíšeme různé krocích a důležité informace pro pochopení dat a jak přenést do použitelného formátu.

### <a name="the-model-development-cycle"></a>Vývojový cyklus modelu
Vytváření dobré Prognózování modely vyžaduje určitou přípravu opatrní a plánování. Výsledek celý proces může výrazně zlepšit rozdělení do několika kroků procesu modelování a zaměřuje se na krok v čase.

Následující diagram znázorňuje, jak může proces modelování rozdělené do několika kroků:

![Model vývojového cyklu](media/cortana-analytics-playbook-demand-forecasting-energy/model-development-cycle.png)

Jak je vidět, že cyklus se skládá z šesti kroků:

* Formulaci problému
* Příjem dat a zkoumání dat
* Příprava dat a vytváření funkcí
* Modelování
* Vyhodnocení modelu
* Vývoj

Ve zbytku této části jsme se popisují jednotlivé kroky a aspekty ke zvážení v každém kroku.

### <a name="problem-formulation"></a>Formulaci problému
Považujeme formulaci problému jako nejdůležitější krok jeden se musí provést ještě před jejich implementací jakékoli řešení prediktivní analýzy. Zde by transformujeme obchodního problému a rozložit na konkrétní prvky, které lze vyřešit pomocí data a modelování techniky. Je vhodné formuluje problém jako sadu otázek, na které jsme chtěli odpověď. Tady jsou některé možné dotazy, které mohou být použitelné v rámci oboru Prognózování poptávky po energii:

* Co je očekávané zatížení na jednotlivých transformovny další hodinu nebo den?
* V které denní doby dojde Moje mřížky poptávku ve špičce?
* Jak pravděpodobné je Moje mřížky pro udržení zatížení očekávané maximální vytížení?
* Kolik power má stanice power generovat během každou hodinu dne?

Formulování tyto otázky vám umožňuje soustředit na získávání ta správná data a implementaci řešení, která je plně v souladu s obchodní problém aktuální. Kromě toho jsme pak můžete nastavit některé klíčové metriky, které umožňují nám k vyhodnocení výkonu modelu. Například jak přesné prognózy by a jaký je rozsah chybu, která bude stále přijatelné podnikání?

### <a name="data-sources"></a>Zdroje dat
Moderní inteligentní mřížky shromažďuje data z různých částí a součástí do mřížky. Tato data představuje různé aspekty operace a využití power mřížky. V rámci oboru prognózy poptávky pro energiích jsme se omezení diskuse o datových zdrojů, které odrážejí využití skutečné poptávce. Důležitým zdrojem spotřeby energie jsou inteligentních měřičů. Nástroje na celém světě jsou rychlé nasazení inteligentních měřičů pro zákazníky. Inteligentní měřiče zaznamenávat skutečné spotřeby a neustále předávání těchto dat zpět do nástroje společnosti. Data se shromažďují a odeslána zpět v pevně zadaném intervalu od každých 5 minut na 1 hodinu. Pokročilejší inteligentních měřičů, mohou být naprogramovány vzdáleně řídit a vyvážit skutečného využití, které v rámci domácnost. Inteligentní měřiče dat je poměrně spolehlivé a obsahuje časové razítko. Díky tomu se důležité složka pro prognózy poptávky. Měření dat se dají agregovat (sečtené) na různých úrovních v rámci topologie mřížky: transformer, transformovny, oblast, *atd*. My potom si vyberte úroveň požadovaná agregace k sestavení modelu prognózy lze pro něj. Například pokud společnost nástroje k předvídání budoucí zatížení na všech jeho Rozvodny mřížky pak všechny měřiče dat může být zobrazují se pro každé jednotlivé transformovny a používá jako vstup pro model Prognózování. Označujeme jako zdroj dat interní inteligentních měřičů.

Prognóza poptávky spolehlivé energii také spoléhat na jiných externích datových zdrojů. Důležitým faktorem, který má vliv na spotřebu energie je počasí, nebo přesněji teploty. Historická data zobrazují silnou korelaci mezi mimo teploty a spotřebu energie. Během výměně léto dnů spotřebitelům pomocí jejich klimatizace a během zimní zapnutí vytápění. Klíč je proto o spolehlivý zdroj historických teploty v umístění mřížky. Kromě toho jsme také využívají přesná předpověď teploty jako prediktorem spotřebu energie.

Další externím zdrojům dat může také pomoct s vytvářením modelů prognózy poptávky energie. Ty můžou zahrnovat dlouhodobém horizontu změny klimatu, ekonomické indexy (*třeba*, HDP) a další. V tomto dokumentu jsme nebude obsahovat tyto další zdroje dat.

### <a name="data-structure"></a>Struktura dat
Poté, co identifikujete zdroje požadovaná data, rádi bychom Ujistěte se, že nezpracovaná data, která byla shromážděna zahrnuje funkce správná data. Pokud chcete sestavit model prognózy poptávky spolehlivé, by potřebujeme Ujistěte se, že zahrnuje data shromážděná datové prvky, které vám mohou pomoci předvídání budoucí poptávky. Tady jsou některé základní požadavky týkající se dat strukturu (schéma) nezpracovaná data.

Nezpracovaná data se skládá z řádků a sloupců. Míry je vyjádřena jako jeden řádek dat. Každý řádek dat obsahuje více sloupců (označované také jako funkce nebo pole).

1. **Časové razítko** – pole časového razítka představuje skutečný čas, kdy se přihlášení měření. Ji by měl v souladu s jedním z běžných formátů data a času. Datum a čas částí by měly být zahrnuty. Ve většině případů není nutné dobu mají být zaznamenány do druhé úrovni členitosti. Je důležité zadat časové pásmo, ve které je zaznamenaná data.
2. **ID měřiče** – toto pole označuje měřič nebo měření zařízení. Proměnná zařazené do kategorií a může být kombinací číslic a znaků.
3. **Hodnota spotřeby** – to je skutečného využití, které v daném data a času. Spotřeba se měří v kWh (kilowatt-hour) nebo jakýkoli jiný preferovaný jednotky. Je důležité si uvědomit, že Měrná jednotka musí být konzistentní vzhledem k aplikacím napříč všechny hodnoty v datech. V některých případech může být spotřeby zadán více než 3 fáze napájení. V takovém případě by potřebujeme ke shromažďování všech fázích nezávislé spotřeby.
4. **Teplota** – teplota se obvykle shromažďují z nezávislé zdroje. Nicméně by měl být kompatibilní s daty využití. Měl by obsahovat časové razítko výše popsaným způsobem, který vám umožní, aby mohla být synchronizována s daty skutečného využití, které. Teplotní hodnota mohou být zadány ve stupních Celsia nebo Fahrenheita však by mělo zůstat konzistentní napříč všechny měření.
5. **Umístění –** pole umístění obvykle souvisí s místem, kde jsou shromážděná data o teplotě. Může být reprezentována jako poštovní směrovací číslo nebo ve formátu (lat/dlouhé) zeměpisné šířky a délky.

V následujících tabulkách jsou uvedeny příklady dobré spotřeby a teploty formát dat:

| **Datum** | **čas** | **ID měřiče** | **Fáze 1** | **Fáze 2** | **Fáze 3** |
| --- | --- | --- | --- | --- | --- |
| 7/1/2015 |10:00:00 |ABC1234 |7.0 |2.1 |5.3 |
| 7/1/2015 |10:00:01 |ABC1234 |7.1 |2.2 |4.3 |
| 7/1/2015 |10:00:02 |ABC1234 |6.0 |2.1 |4.0 |

| **Datum** | **čas** | **Umístění** | **teplota** |
| --- | --- | --- | --- |
| 7/1/2015 |10:00:00 |11242 |24.4 |
| 7/1/2015 |10:00:01 |11242 |24.4 |
| 7/1/2015 |10:00:02 |11242 |24.5 |

Jak je vidět výše, tento příklad zahrnuje 3 různé hodnoty pro spotřebu spojené s 3 fáze napájení. Všimněte si také, že datum a čas, pole se oddělují, ale mohou také být kombinovány do jednoho sloupce. V tomto případě představuje sloupci umístění ve formátu 5 číslic PSČ a teploty ve formátu stupně Celsia.

### <a name="data-format"></a>Formát dat
Cortana Intelligence Suite může podporovat nejběžnějších formátů dat jako CSV, TSV, JSON, *atd*. Je důležité, že formát dat zůstane konzistentní pro celý životní cyklus projektu.

### <a name="data-ingestion"></a>Přijímání dat
Protože energie prognóza poptávky je neustále a často předpovědět, jsme musíte zajistit, že nezpracovaná data se přenášejí prostřednictvím procesu ingestování dat solid a spolehlivé. Zpracování příjmu musí zaručit, že nezpracovaná data jsou k dispozici pro proces Prognózování v požadované době. To znamená, že frekvence ingestování dat musí být větší než frekvence prognóz.

Příklad: Pokud naše řešení Prognózování poptávky vygeneruje nový předpovědi v 8:00 hodin denně jsme muset ujistit, že všechna data, která byla shromážděna během posledních 24 hodin, byl plně přijatých až do tohoto bodu a musí obsahovat i poslední hodina data.

Aby bylo možné dosáhnout, Cortana Intelligence Suite nabízí různé způsoby, jak proces ingestování spolehlivé data. To dál probereme v **nasazení** část tohoto dokumentu.

### <a name="data-quality"></a>Kvalita dat
Zdroj nezpracovaných dat, která je požadována pro provádění Prognózování poptávky spolehlivé a přesné, musí splňovat kritéria kvality některé základní data. Pokročilé statistické metody lze jako kompenzaci za některý problém kvality dat, ale stále potřebujeme zajistit, že jsme jsou při ingestování nových dat překročení prahové hodnoty některých základních dat kvality. Tady je několik důležitých informací týkající se kvality nezpracovaná data:

* **Chybí hodnota** – to se vztahuje na situaci po konkrétní měření nebyl shromážděn. Základním požadavkem je, že chybí hodnota frekvence nesmí být větší než 10 % pro jakékoli dané časové období. V případě, že jedna hodnota je chybějící by měl být označeny pomocí předdefinované hodnoty (například: "9999"), nikoli "0", který může být platná hodnota.
* **Přesnost měření** – skutečnou hodnotu spotřeby nebo teploty by měly být zaznamenány přesně. Nesprávné rozměry způsobí nepřesné prognózy. Chyba měření obvykle by mělo být menší než 1 % vzhledem k hodnotu true.
* **Čas měření** – je vyžadován, časové razítko data shromážděna nesmí lišit o více než 10 sekund vzhledem k true čas skutečné měření.
* **Synchronizace** – když jsou používány více zdrojů dat (*třeba*, spotřebě a teploty) jsme musíte zajistit, že neexistují žádné synchronizaci času problémy mezi nimi. To znamená, že časový rozdíl mezi shromážděné časové razítko z jakékoli dva nezávislé datové zdroje nesmí být delší než více než 10 sekund.
* **Latence** – jak je popsáno výše, v **Ingestování**, jsme jsou závislé na spolehlivý datový tok a příjmu procesu. Pro ovládací prvek, který jsme musíte zajistit, že latence dat kontrolou. Tento parametr je zadán jako časový rozdíl mezi časem, který byla pořízen skutečné měření a čas, kdy byla načtena do úložiště Cortana Intelligence Suite a je připravená k použití. Pro krátkodobé načtení Prognózování celkovou latenci nesmí být delší než 30 minut. Pro dlouhodobé zatížení Prognózování celkovou latenci nesmí být větší než 1 den.

### <a name="data-preparation-and-feature-engineering"></a>Příprava dat a vytváření funkcí
Jakmile se ingestují nezpracovaná data (naleznete v tématu **Ingestování**) a jestli se bezpečně uložena, je připraven ke zpracování. Fáze přípravy dat je v podstatě trvá nezpracovaných dat a převodu (transformaci, provést explicitní přeformování) ji do formuláře pro fázi modelování. Který může obsahovat jednoduché operace, jako je třeba použití sloupci nezpracovaná data jsou s jeho skutečné naměřenou hodnotu, standardizované hodnoty, složitějších operací, jako [čas obložení](https://en.wikipedia.org/wiki/Lag_operator)a další. Nově vytvořené datové sloupce se označují jako funkce data a tyto proces se označuje jako vytváření funkcí. Na konci tohoto procesu měli jsme nové datové sady, který byl získán z nezpracovaných dat a slouží pro modelování. Kromě toho je potřeba fáze Příprava dat postará o chybějících hodnot (naleznete v tématu **kvality dat**) a jako kompenzaci za ně. V některých případech by také potřebujeme k normalizaci dat k zajištění, že všechny hodnoty jsou reprezentovány ve stejném měřítku.

V této části uvádíme některé běžné funkce data, které jsou součástí energie prognózy poptávky modely.

**Čas řízené funkce:** Tyto funkce jsou odvozeny z data datum a časové razítko. Ty se extrahují a převést do kategorií funkcí, jako jsou:

* Čas denní – to je hodina dne, který má hodnoty od 0 do 23
* Den v týdnu – to představuje den v týdnu a má hodnoty od 1 (neděle) do 7 (sobota)
* Den měsíce – to představuje aktuální datum a může přijmout hodnoty od 1 do 31.
* Měsíc roku – to představuje měsíc a má hodnoty od 1 (leden) do 12 (prosinec)
* Víkendu – Toto je binární hodnotu funkce, která přebírá hodnoty 0 pro víkendové dny v týdnu nebo 1
* Sváteční – Toto je binární hodnotu funkce, která přebírá hodnotu 0 pro pravidelné den nebo 1 pro svátek
* Podmínky Fourierova – Fourierova podmínky jsou váhy, které jsou odvozeny z časové razítko a slouží k zaznamenávání sezónnosti (cykly) v datech. Protože jsme v našich datech pravděpodobně více sezóny potřebujeme Fourierova podmínek. Roční, týdenní a denní období/cykly následkem toho 3 Fourierova podmínek může mít třeba, hodnoty poptávky.

**Nezávislé měření funkce:** Nezávislé funkce zahrnují všechny datové prvky, které jsme chtěli použít jako prediktory v náš model. Tady jsme vyloučit závislé funkce, která by potřebujeme k předpovědi.

* Funkce Lag – jedná se o dobu posunuta hodnoty skutečné poptávce. Funkce lag 1 například bude obsahovat hodnota požadavku do předchozí hodiny (za předpokladu, že data po hodinách) vzhledem k aktuální časové razítko. Podobně, jsme můžete přidat prodleva 2, 3, prodleva *atd*. Vyhodnocení výsledků modelu stanovuje během fáze modelování skutečné kombinací prodleva funkce, které se používají.
* Dlouhodobé populární – tato funkce představuje lineární nárůstu poptávky mezi roky.

**Závislé funkce:** Závislé funkce je datový sloupec, který rádi bychom náš model k predikci. S [pod dohledem strojového učení](https://en.wikipedia.org/wiki/Supervised_learning), musíme nejprve trénování modelu s použitím závislé součásti (která se také označuje jako popisky). Díky tomu model další vzory v datech přidružené závislé funkce. V odhadovat poptávku po energii obvykle chcete předpovídat skutečné poptávce a proto jsme by použít jako závislé funkce.

**Manipulace s chybějící hodnoty:** Během fáze přípravy dat můžeme nutné určit nejlepší strategii pro zpracování chybějící hodnoty. To se většinou provádí pomocí různých statistických [metody imputace dat](https://en.wikipedia.org/wiki/Imputation_\(statistics\)). V případě energie Prognózování poptávky, jsme obvykle dává chybějící hodnoty pomocí klouzavý průměr z předchozí dostupných datových bodů.

**Normalizace dat:** Normalizace dat je jiný typ transformace, který se používá k zajištění všech číselná data, jako je prognóza poptávky podobné škálování. To obvykle pomáhá zlepšit přesnost modelu a přesnosti. By obvykle děláme to vydělí rozsah dat skutečnou hodnotu.
To se snížit na původní hodnotu do má menší rozsah, obvykle mezi -1 a 1.

## <a name="modeling"></a>Modelování
Fáze modelování je, kde probíhá převod data do modelu. V jádru služby existuje tento proces pokročilé algoritmy, které kontrola historická data (trénovacích dat), extrakce vzory a sestavení modelu. Tento model je možné později k předpovědi na nová data, která nebyla použita k sestavení modelu.

Jakmile budeme mít funkční spolehlivé modelu jsme ho pak může použít ke stanovení skóre pro nová data, která je strukturována tak, aby zahrnout požadované součásti (X). Použít trvalý modelu (objekt z fáze školení) a předvídání Cílová proměnná, která označuje symbolem Ŷ způsobí, že proces hodnocení.

### <a name="demand-forecasting-modeling-techniques"></a>Modelování techniky Prognózování poptávky
V případě uděláme Prognózování poptávky pomocí historických dat, který je seřazen podle času. Obecně označujeme data, která obsahuje časovou dimenzi jako [časové řady](https://en.wikipedia.org/wiki/Time_series). Cílem v čase řady modelování je nalezení čas související trendy, sezónnost, automatické korelace (korelace v čase) a dává do modelu.

V posledních letech pokročilých algoritmů byly vyvinuty tak, aby vyhovovaly prognózy časových řad a zvyšte přesnost předpovědi. Stručně několik z nich tady probereme.

> [!NOTE]
> Tento oddíl není určena pro použití jako strojové učení a Prognózování přehled, ale spíše jako krátký dotazník modelování techniky, které se obvykle používají pro prognózování poptávky. Další informace a vzdělávací materiály o prognózy časových řad, důrazně doporučujeme online knihy [Prognózování: zásady a přístup k](https://www.otexts.org/).
>
>

#### <a name="ma-moving-average"></a>**MA (klouzavý průměr)**
Klouzavý průměr je jedním z první analytické techniky, které již byly použity pro prognózy časových řad a je to stále jedním z nejčastěji používaných technik k dnešnímu dni. Je také základem pro pokročilejší Prognózování techniky. S klouzavý průměr jsme se Prognózování další datový bod zprůměrováním přes K nejnovější body, ve kterém K označuje pořadí klouzavý průměr.

Klouzavý průměr postup má za následek vyhlazování prognózy a nemusí proto zpracovat i velké volatility v datech.

#### <a name="ets-exponential-smoothing"></a>**ETS (exponenciální vyhlazování)**
Exponenciální vyhlazování (ETS) je řada různých metod, které používají vážený průměr poslední datových bodů, aby bylo možné předpovědět další datový bod. Cílem je přiřaďte vyšší váhu na novější hodnoty a postupně klesat tento váha pro starší měřené hodnoty. Existuje několik různých metod s Tato řada, některé z nich data, jako jsou sezónní metoda Holt-Winters zahrňte zpracování sezónnosti.

Některé z těchto metod také zvážit sezónnosti data.

#### <a name="arima-auto-regression-integrated-moving-average"></a>**ARIMA (regrese automaticky integrovaný pohyblivý průměr)**
Automatické regrese integrované přesun průměr (ARIMA) je jiné řady metod, které se běžně používá pro předpovědi časové řady. Prakticky kombinuje metody Automatické regrese s klouzavý průměr. Auto-regression metody používají regresních modelů provedením předchozích časových řad hodnoty k výpočtu dalšího bodu datum. Metody ARIMA platí také rozdílové metody, které zahrnují výpočet rozdílu mezi datovými body a jejich namísto původní naměřenou hodnotu použití. Nakonec ARIMA také využívá technik klouzavý průměr, které jsou popsané výše. Kombinace všechny tyto metody v různých způsobů, jak je, co vytvoří rodinu metody ARIMA.

ETS a ARIMA se často používají ještě dnes k Prognózování poptávky po energii a mnoho dalších problémů prognóz. V mnoha případech jsou zkombinované dohromady a poskytovat velmi přesné výsledky.

**Obecné vícenásobná regrese** regresních modelů může být nejdůležitější přístup k modelování v rámci domény strojového učení a statistické údaje. V souvislosti s časovými řadami použijeme regresní předpovídat budoucí hodnoty (*třeba*, poptávky). V regrese jsme trvat lineární kombinaci prediktory a další váhu těchto prediktory (také označované jako koeficienty) v průběhu školení. Cílem je vytvořit řádek regrese, který bude předpovídat naše předpovězené hodnoty. Regrese metody jsou vhodné, když Cílová proměnná jsou číselná a proto také odpovídá prognózy časových řad. Široké škály metod regrese včetně velmi jednoduché regresních modelů, jako je [lineární regrese](https://en.wikipedia.org/wiki/Linear_regression) a další pokročilé těch, jako je například rozhodovacích stromů [doménových struktur Random](https://en.wikipedia.org/wiki/Random_forest), [Neural Sítě](https://en.wikipedia.org/wiki/Artificial_neural_network)a vylepšené rozhodovací stromy.

Vytváření poptávku po energii Prognózování jako regresní problém nám poskytuje značnou flexibilitu při výběru naší datové funkce, které mohou být kombinovány z dat časových řad skutečné poptávce a vnějším faktorům, jako je například teploty. Další informace o vybrané funkce jsou popsány v technické funkce (naleznete v tématu **přípravy dat a funkce Engineering**) části playbook.

Z našich zkušeností v implementaci a nasazení pilotní prognózy poptávky energie zjistili jsme, že nejlepší výsledky mají tendenci pokročilé regresní modely, které jsou k dispozici v Azure ML a Usnadňujeme jejich použití.

## <a name="model-evaluation"></a>Vyhodnocení modelu
Vyhodnocení modelu mají důležitou roli v rámci **modelu vývojový cyklus**. V tomto kroku podíváme do ověření modelu a jeho výkon s daty v reálném životě. Během kroku modelování používáme část dostupných dat pro trénování modelu. Během zkušební fáze pořídíme zbývající data pro otestování modelu. Prakticky, znamená to, že jsme se tak nová data modelu, která byla změnili a obsahuje stejné funkce jako trénovací datové sady. Ale během procesu ověření používáme model pro předpověď Cílová proměnná, spíše než poskytují k dispozici Cílová proměnná. Často označujeme jako model bodování tento proces. Potom jsme použije true cílové hodnoty a jejich porovnání s predikované výsledky. Cílem je minimalizovat chyba předpovědi, to znamená rozdíl mezi předpovědí a hodnotu true a měření. Kvantifikace měření chyb je klíč, protože jsme chtěli vyladění modelu a ověření, jestli se ve skutečnosti snižuje chyby. Dolaďování modelu můžete udělat tak, že upravíte parametry modelu, které řídí studijní postup nebo přidáním nebo odebráním funkcí dat (označované jako [úklidu parametrů](https://channel9.msdn.com/Blogs/Azure/Data-Science-Series-Building-an-Optimal-Model-With-Parameter-Sweep)). Prakticky, která znamená, že jsme může mezi vytváření funkcí, modelování, iterovat a model fáze hodnocení více než jednou, dokud nedojde ke snížení chyby na požadované úrovni.

Je důležité, že chyba predikcí se nikdy nesmí být nulové důraz je nikdy model, který může předpovídat naprosto každý výsledek. Je však závažnost chyby, která je akceptovatelná podle podnikání. Během procesu ověření bychom rádi zajistěte, aby byla naše chyba předpovědi modelu na úrovni nebo lepší než úroveň tolerance firmy. Proto je důležité nastavit úroveň přípustná chyba na začátku cyklu během **formulaci problému** fáze.

### <a name="typical-evaluation-techniques"></a>Techniky hodnocení za typické
Existují různé způsoby, které predikce chyba měří a kvantifikovat. V této části se zaměříme diskuse o techniky hodnocení za relevantní na časové řady a specifické pro odhadovat poptávku po energii.

#### <a name="mapehttpsenwikipediaorgwikimeanabsolutepercentageerror"></a>[**MAPE**](https://en.wikipedia.org/wiki/Mean_absolute_percentage_error)
MAPE jsou zahrnovaného znamenat absolutní chyba procento. S MAPE jsme se výpočetní rozdíl mezi každé naplánované bodu a skutečnou hodnotu tohoto bodu. Potom jsme vyčíslení chyba na bod výpočtem podíl rozdíl vzhledem k aktuální hodnotu. V posledním kroku jsme průměr těchto hodnot. Matematické vzorce pro MAPE je následující:

![Vzorec MAPE](media/cortana-analytics-playbook-demand-forecasting-energy/mape-formula.png)
*kde A<sub>t</sub> je skutečná hodnota F<sub>t</sub> předpovězenou a n je prognózy obzoru.*

## <a name="deployment"></a>Nasazení
Jakmile jsme nailed dolů fáze modelování a ověření výkonu modelu jsme připraveni přejít do fáze nasazení. V tomto kontextu znamená nasazení umožňuje zákazníkům využívat model spuštěním skutečné předpovědí na něm ve velkém měřítku. Koncept nasazení je klíč v Azure ML, protože naším hlavním cílem je neustále vyvolat předpovědí na rozdíl od jenom z dat získat podrobnější informace o. Fáze nasazení je ta část, kde jsme povolili modelu, který se má využít ve velkém měřítku.

V rámci kontextu prognóza poptávky energie naše cílem je vyvolat nepřetržitý a pravidelných předpovědí přitom zajistit, že je k dispozici pro model čerstvá data a že předpokládaných data se odesílají zpět do klienta náročné.

### <a name="web-services-deployment"></a>Nasazení webové služby
Hlavní nasaditelný stavebního bloku v Azure ML je webová služba. Toto je nejúčinnější způsob, jak povolit spotřebu prediktivního modelu v cloudu. Webová služba zapouzdřuje modelu a zabalí jej s [RESTful](https://www.restapitutorial.com/) rozhraní API (Application Programming Interface). Rozhraní API můžete použít jako součást jakýkoli kód klienta, jak je znázorněno v následujícím diagramu.

![Jsme nasazení služby a využití](media/cortana-analytics-playbook-demand-forecasting-energy/web-service-deployment-and-consumption.png)

Jak je vidět, webové služby je nasazený v Cortana Intelligence Suite cloudu a mohou být vyvolány pomocí vystavených koncový bod REST API. Různých typů klientů napříč různými doménami, můžete současně vyvolat službu prostřednictvím webového rozhraní API. Webovou službu můžete taky škálovat, aby podporoval tisíce souběžných volání.

### <a name="a-typical-solution-architecture"></a>Architektura typického řešení
Při nasazování řešení Prognózování poptávky po energii nás zajímají nasazení, který jde nad rámec webovou službu pro předpovědi a zajišťuje kompletní tok dat. kompletní řešení. V době jsme vyvolání nové prognózy by potřebujeme Ujistěte se, že se model zobrazí s funkcemi aktuální data. Která znamená, že nově shromážděných nezpracovaných dat je neustále ingestuje, zpracování a transformuje na požadované funkce nastavit v modelu byla vytvořena. Ve stejnou dobu rádi bychom zpřístupnění dat prognózy end využívání klientů. Například data cyklu tok (nebo datový kanál) je znázorněn v následujícím diagramu:

![Kompletní datový tok odhadovat poptávku po energii](media/cortana-analytics-playbook-demand-forecasting-energy/energy-demand-forecase-end-data-flow.png)

Toto jsou kroky, které se provedou v rámci cyklu prognózy poptávky energie:

1. Miliony měřiče nasazených dat neustále generují data o spotřebě energie v reálném čase.
2. Tato data se shromažďují a uloženy v cloudovém úložišti (*třeba*, objektů Blob v Azure).
3. Před zpracováním, nezpracovaných dat je agregován transformovny nebo oblasti definované podnikání.
4. Zpracování funkce (naleznete v tématu **přípravy dat a zpracování funkce**) pak probíhá a vytvoří modelovat data, která se vyžaduje pro trénování a vyhodnocování – jsou funkce set data uložená v databázi (*třeba*, SQL Azure).
5. Je vyvolána znovu školicí služby pro znovu trénování modelu prognózy lze – je trvalé tuto aktualizovanou verzi modelu, takže mohou využívat hodnoticí webové služby.
6. Hodnoticí webové služby se vyvolá podle plánu, která odpovídá požadovanou četnost prognózy.
7. Předpokládané data uložená v databázi, který je přístupný koncový spotřeby klienta.
8. Využití klienta načte předpovědí, použije ji zpět do mřížky a využívá v souladu s případ vyžaduje použití.

Je důležité si uvědomit, že tento celý cyklus je plně automatizovaná a spouští podle plánu. Celý Orchestrace tento cyklus dat můžete udělat pomocí nástrojů, jako [Azure Data Factory](https://azure.microsoft.com/services/data-factory/).

### <a name="end-to-end-deployment-architecture"></a>Architektura komplexní nasazení
Aby bylo možné prakticky nasadit řešení prognózy poptávky energie v Cortana Intelligence, musíme Ujistěte se, že požadované součásti jsou vytvářena a správně nakonfigurovaný.

Následující diagram znázorňuje typickou architekturu Cortana Intelligence na základě, který implementuje a orchestruje cyklu toku dat, který je popsán výše:

![Architektura komplexní nasazení](media/cortana-analytics-playbook-demand-forecasting-energy/architecture.png)

Další informace o jednotlivých komponent a celkovou architekturu najdete šablonu řešení energie.

