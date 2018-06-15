---
title: Cortana Intelligence řešení šablony scénářem pro vyžádání prognózy energie | Microsoft Docs
description: Šablona řešení s Microsoft Cortana Intelligence, který pomáhá prognózy vyžádání pro firmu nástroj energie.
services: cortana-analytics
documentationcenter: ''
author: ilanr9
manager: ilanr9
editor: yijichen
ms.assetid: 8855dbb9-8543-45b9-b4c6-aa743a04d547
ms.service: cortana-analytics
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2016
ms.author: ilanr9;yijichen;garye
ms.openlocfilehash: 275e387878900154660d044b26ff5ac03a17a65a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23846427"
---
# <a name="cortana-intelligence-solution-template-playbook-for-demand-forecasting-of-energy"></a>Cortana Intelligence řešení šablony scénářem pro vyžádání prognózy energie
## <a name="executive-summary"></a>Shrnutí
V posledních několika letech mít sloučené Internet věcí (IoT), alternativních zdrojů energie a velké objemy dat k vytvoření velká možností v doméně, nástroje a energie. Ve stejnou dobu nástroje a celý energie sektoru viděli spotřeba vyrovnání se spotřebiteli náročných lepší způsoby řízení jejich použití energie. Proto nástroj a inteligentní mřížky společností jsou v velmi potřebné inovacemi. Zajistěte a obnovení sami. Kromě toho mnoho power a nástroj mřížky se stávají zastaralé a velmi nákladný k zajištění údržby a spravovat. Během posledního roku tým pracuje na několika oznámeních podporujících zapojení uživatelů v rámci domény energie. Během těchto oznámeních podporujících zapojení uživatelů jsme narazili mnoha případech, ve kterých byla vyhledávání nezávislí dodavatelé softwaru (nezávislí výrobci softwaru) nebo nástroje do prognózy pro budoucí spotřeby energie. Tyto prognózy hraje důležitou roli v jejich aktuálním a budoucím obchodním a staly základ pro různé případy použití. Mezi ně patří krátkodobých a dlouhodobých power zatížení prognózy, obchodování, Vyrovnávání zatížení, optimalizace mřížky atd. Velké objemy dat a pokročilé analýzy (AA) metody například Machine Learning (ML) jsou klíče předpokladů pro vytvoření přesné a spolehlivé prognózy.  

V této scénářem sestavili jsme firmy a analytické pokyny potřebné pro úspěšné vývoj a nasazení spotřeby energie prognózy řešení. Tyto navrhované pokyny vám mohou pomoci nástroje, datových vědců a techniky data v zřízení plně operationalized založené na cloudu, prognózy vyžádání řešení. Pro společnosti, kteří jsou právě spouští jejich velkých objemů dat a cesty pokročilou analýzu můžete toto řešení představují počáteční počáteční hodnoty v jejich dlouhodobou strategii inteligentní mřížky.

> [!TIP]
> Si můžete stáhnout diagram, který poskytuje přehled architektury této šablony [Cortana Intelligence řešení šablony architektura pro vytváření prognóz vyžádání energie](cortana-analytics-architecture-demand-forecasting-energy.md).  
> 
> 

## <a name="overview"></a>Přehled
Tento dokument popisuje business, data a technické aspekty pomocí Cortana Intelligence a v konkrétní Azure Machine Learning (AML) pro provádění a nasazení řešení prognózy energie. Dokument se skládá ze tří hlavních částí:  

1. Principy podniku  
2. Pochopení dat  
3. Technická implementace

**Obchodní principy** část popisuje aspekt obchodní jeden je potřeba pochopit a vezměte v úvahu před uskutečněním rozhodnutí o investice. Vysvětluje, jak kvalifikaci obchodního problému po ruce zajistit prediktivní analýzy a strojové učení se skutečně efektivní a použít. Další dokument vysvětluje základy strojové učení a jak se používají k řešení problémů prognózy energie. Ho popisuje požadavky a kritéria kvalifikace případu použití. Některé ukázkové použijte případy a případ obchodní scénáře jsou také uvedeny.

Data jsou hlavní složkou pro všechny strojového učení řešení. **Pochopení dat** tento dokument popisuje některé důležité aspekty data. Popisuje ho druh data, která je potřebná pro prognózy energie, požadavky na kvalitu dat a jaké zdroje dat je obvykle neexistuje. Také popisují, jak nezpracovaných dat slouží k přípravě funkce dat, které ve skutečnosti jednotky pro modelování část.

Třetí části dokumentu se vztahuje **technickou implementaci** aspekt řešení. Funkce inženýrství a modelování jsou jádrem procesu vědecké účely dat a jsou proto se podrobněji některé. Pokrývá koncept webové služby, které jsou důležité vehicle pro nasazení cloudu řešení prediktivní analýzy. Můžeme také popisují typický Architektura řešení operationalized začátku do konce.

Kromě toho dokument obsahuje referenční materiál, který můžete použít k získání dalších pochopení domény a technologie.

Je důležité si uvědomit, že jsme nemáte v úmyslu tak, aby pokrývalo v tomto dokumentu hlubší procesu vědecké účely dat, jejich matematické a technické aspekty. Tyto podrobnosti naleznete v [dokumentace Azure ML](http://azure.microsoft.com/services/machine-learning/) a [blogy](http://blogs.microsoft.com/blog/tag/azure-machine-learning/).

### <a name="target-audience"></a>Cílové skupiny
Cílovou skupinu tohoto dokumentu je obchodních a technických pracovníky, kteří by chtěli získají informace a řešení a jak jsou použity konkrétně v rámci domény prognózy energie na základě Principy Machine Learning.

Datových vědců mohou také těžit z čtení tohoto dokumentu lépe porozuměli nejvyšší úrovni procesu, který řídí nasazení energie prognózy řešení. V tomto kontextu je lze také funkční stanovíte a výchozí bod pro další podrobné a rozšířené materiálů.

### <a name="industry-trends"></a>Oborových trendů
V posledních několika letech mít sloučené IoT, alternativních zdrojů energie a velké objemy dat k vytvoření velká možností v prostoru nástroj a energie. Ve stejnou dobu nástroje a sektory celý energie viděli spotřeba vyrovnání se spotřebiteli náročných lepší způsoby řízení jejich použití energie.

Mnoho nástroj a inteligentní energetické společnosti mají byla průkopnické [inteligentní mřížky](https://en.wikipedia.org/wiki/Smart_grid) nasazení počet použití případů, které pomocí dat vygenerovaných sadami mřížky. Mnoho případy použití základem vlastností vlastní výroby elektřiny: nemůže být nashromáždila, ani z produkce uložené jako inventáře. Ano co se vytvářejí se musí použít. Nástroje, které chcete ke zvýšení účinnosti muset prognózy spotřebu jednoduše vzhledem k tomu, který vám poskytne větší schopnost **vyvážit nabídce a poptávce**, proto prevence ztráty energie **snížit skleníkového plynu emisí**a řídit náklady.

Při posuzování nákladů, je dalším důležitým aspektem, který je cena. Nové schopnosti obchodu power mezi nástroje začnou ve velmi potřebné **předpovídat budoucí vyžádání a budoucí ceny elektřiny**. To může pomoct určit jejich svazky výrobní společnosti.

Používáme je slovo 'inteligentní', jsme naleznete ve skutečnosti mřížky, které můžete informace a pak proveďte předpovědi. Ho můžete odhadnout sezónní změny energie a také **předvídáte dočasného přetížení situacích a automaticky ji upravit**. Ve vzdáleně regulační spotřeba (pomocí tato inteligentní měřidla), lze provádět lokalizované přetížení situacích. **Nejprve predikci a pak funguje**, mřížky umožňuje efektivněji v čase.

Pro zbytek tohoto dokumentu se zaměříme na konkrétní řadu případy použití, které se týkají prognózy z budoucí krátkodobé i dlouhodobé na energii na vyžádání. Jsme několik měsíců pracovaly v těchto oblastech a dostalo některé znalosti a dovednosti, která umožňují nám nepřineslo výsledky úrovni oboru. Jiné případy použití se budeme také v dokumentu v blízké budoucnosti.

## <a name="business-understanding"></a>Obchodní vysvětlení
### <a name="business-goals"></a>Obchodních cílů
**Energie ukázku** cílem je ukázka typické prediktivní analýzy a strojového učení řešení, které můžete nasadit ve velmi krátkého časového rámce. Konkrétně je naše aktuální aktivní, aby jeho obchodní hodnotu mohli rychle uvědomili si a využít při povolení energie vyžádání prognózy řešení. Informace v této playbook může pomoct zákaznické splníte následující cíle:

* Krátkého času na hodnotu machine learning na základě řešení
* Umožňuje rozšířit pilotní nasazení použít případ na jiné případy použití nebo širší obor podle jejich obchodních potřeb
* Rychle získat Cortana Intelligence Suite znalostní báze produktů

Pomocí těchto cílů na paměti Toto playbook cílem je doručení obchodních a technických znalostí, která vám pomůže při dosažení těchto cílů.

### <a name="power-load-and-demand-forecasting"></a>Napájení zatížení a vyžádání prognózy
V rámci odvětví energie může být mnoha způsoby, které poptávky prognózy lze kritické obchodní problémy vyřešit. Ve skutečnosti vyžádání prognózy lze považovat za základem pro mnoho případy použití jader v odvětví. Obecně platí, jsme zvážit dva typy předpovědi energetické poptávky: krátkodobé i dlouhodobé. Každé z nich může sloužit k jinému účelu a využívat jiný přístup. Hlavní rozdíl mezi nimi je prognózy horizontu, znamená časové rozmezí do budoucna, pro kterou jsme by prognózy.

#### <a name="short-term-load-forecasting"></a>Krátkodobých termín zatížení prognózy
V kontextu spotřeby energie krátké termín načíst prognózy (STLF) je definován jako agregované zatížení, která je naplánované v blízké budoucnosti na různých částí mřížky (nebo mřížku jako celek). V tomto kontextu krátkodobou je definován časový horizont v rozsahu 1 hodina na 24 hodin. V některých případech je také horizon 48 hodin možné. Proto STLF je velmi běžné provozní použití případ mřížky. Tady jsou některé příklady STLF řízené případů použití:

* Nabídce a poptávce vyrovnávání
* Podpora obchodním napájení
* Provádění trhu (nastavení napájení cena)
* Provozní optimalizace mřížky
* [Odpověď na vyžádání](https://en.wikipedia.org/wiki/Demand_response)
* Ve špičkách vyžádání prognózy
* Vyžádání straně správy
* Vyrovnávání zatížení a přetížení prevence
* Dlouhodobé zatížení prognózy
* Selhání a anomálií detekce
* Curtailment/vyrovnávání ve špičce 

STLF model jsou většinou založené na nejbližší minulosti (posledního dne nebo týdne) datům o spotřebě a používání naplánované teploty jako důležité předpověď. Získávání přesných teploty prognózy pro příští hodiny a až na 24 hodin se stává stále menší výzvu nyní dnů. Tyto modely jsou méně citlivou sezónní vzory nebo dlouhodobé trendy spotřeby.

SLTF řešení jsou také k vygenerování velkému počtu volání předpovědi (žádosti o službu) pravděpodobně vzhledem k tomu, že jsou právě vyvolány hodinu a v některých případech i s vyšší frekvence. Také je velmi běžné zobrazíte implantaci, kde každé jednotlivé transformovny nebo transformer je reprezentována jako samostatné model a proto se ještě větší objem požadavků předpovědi.

#### <a name="long-term-load-forecasting"></a>Dlouhodobé zatížení prognózy
Cílem z dlouho termín zatížení prognózy (LTLF) je prognózy power vyžádání s časový horizont od 1 týden do více měsíců (a v některých případech pro počet roků). Tento rozsah horizon je ve většině případů platí pro plánování a investice případy použití.

Pro dlouhodobou scénáře je důležité mít vysoké kvality dat, které pokrývá rozpětí několik let (minimální 3 roky). Tyto modely obvykle extrahovat sezónnosti vzory z historických dat a nutné používat externí predicators například jako počasí a klimatem vzory.

Je důležité, aby se vyjasnilo, že čím delší je prognózy horizon, tím méně přesná Prognóza může být. Je proto důležité vytvořit některé intervaly spolehlivosti společně s skutečné prognózu, která by umožnilo člověka zohlednit možné variace do proces plánování.

Vzhledem k tomu, že tento scénář spotřeby pro LTLF je většinou plánování, očekáváme mnohem nižší předpovědi svazky (ve srovnání se STLF). Jsme by obvykle najdete v těchto předpovědi vkládat do vizualizace nástroje, například aplikace Excel nebo PowerBI a volání ručně uživatelem.

### <a name="short-term-vs-long-term-prediction"></a>Krátký termín vs. Dlouhodobé předpovědi
Následující tabulka porovnává STLF a LTLF v ohledem na nejdůležitější atributy:

| Atribut | Krátkodobá zatížení prognózy | Dlouhodobé zatížení prognózy |
| --- | --- | --- |
| Prognózy Horizon |Z 1 hodinu 48 hodin |Od 1 do 6 měsíců nebo více |
| Členitost dat |Každou hodinu |Hodinové nebo denní |
| Typické scénáře použití |<ul><li>/ Poptávky vyrovnávání</li><li>Vyberte hodinu prognózy</li><li>Odpověď na vyžádání</li></ul> |<ul><li>Dlouhodobé plánování</li><li>Plánování prostředky mřížky</li><li>Plánování prostředků</li></ul> |
| Typické prognostické |<ul><li>Dne nebo týdne</li><li>Hodiny dne</li><li>Hodinové teploty</li></ul> |<ul><li>Měsíc roku</li><li>Den v měsíci</li><li>Dlouhodobé teploty a klimatem</li></ul> |
| Rozsah historických dat |Data za dvě až tři roky |Data za pět až 10 let |
| Typické přesnost |MAPE * 5 % nebo nižší |MAPE * 25 % nebo nižší |
| Prognózy frekvence |Vytváří každou hodinu nebo každých 24 hodin |Vytváří jednou měsíčně, čtvrtletně nebo ročně |

\*[MAPE](https://en.wikipedia.org/wiki/Mean_absolute_percentage_error) – znamenat průměrnou procentuální chyby

Jak je vidět z této tabulky, je velmi důležité k rozlišení mezi krátkodobém a dlouhodobém prognózy scénáře, protože se jedná představují různých obchodních potřeb a může mít jiné nasazení a vzorce používání.

### <a name="example-use-case-1-esmart-systems--overload-optimization"></a>Příklad použití případ 1: eSmart systémy – přetížení optimalizace
Důležité role [inteligentní mřížky](https://en.wikipedia.org/wiki/Smart_grid) je dynamicky a neustále optimalizovat a upravit změna vzorce používání. Spotřebu může být ovlivněno krátkodobé změny, které jsou způsobeny hlavně kolísání teploty (*například*, další power se používá pro podmínku letecké nebo vytápění). Ve stejnou dobu spotřebu je ovlivněny také dlouhodobých trendů. Ty mohou obsahovat sezónnosti účinky, státní svátky, dlouhodobé růstu spotřeby a i hospodářského faktorech, například příjemce index, cena těžba ropy a HDP.

V takovém případě použijte [eSmart](http://www.esmartsystems.com/) chtěli nasadit cloudové řešení, která umožňuje predikci tendenci situace přetížení na jakékoli dané transformovny mřížky. Konkrétně eSmart chtěli identifikovat trakčních, které jsou pravděpodobně přetížení během následující hodiny, takže okamžitý zásah, může přesměrováni na vyhnout nebo je tato situace vyřešit.

Přesný a rychlé provádění předpovědi vyžaduje implementaci tři prediktivní modely:

* Dlouho model termín, který umožňuje prognózy spotřeby energie na každý transformovny během další několik týdnů či měsíců
* Krátkodobá model, který umožňuje předpovědi přetížení situace na danou transformovny během příští hodiny
* Model teploty, který poskytuje prognózy z teploty budoucí přes více scénářů

Cílem dlouhodobé modelu je pořadí trakčních podle jejich tendenci přetížení (vzhledem ke své přenosu kapacita napájení) během další týden nebo měsíc. To umožňuje vytvoření krátké seznam trakčních, které by slouží jako vstup pro krátkodobou předpověď. Teplotní je důležité předpověď pro dlouhodobé model, je zapotřebí neustále vytvářet prognózy více scénář teploty a kanálu je jako vstup do dlouhodobé modelu. Krátkodobá prognózy je pak volána k předpovědi, které transformovny je pravděpodobně přetížení přes do příští hodiny.

Modely krátkodobé a dlouhodobé nasazených jednotlivě na každém transformovny. Proto praktické spuštění těchto modelů vyžaduje rozsáhlé orchestration. K získání vyšší přesnost předpovědi v krátkodobém horizontu, podrobnější modelu jsou vyhrazené pro každou hodinu dne. Všechny tyto modely jsou vykonány každou hodinu a dokončí provádění během několika minut, aby bylo dost času reagovat a přijmout preventivní opatření, v případě potřeby. Tato kolekce modelů je pořád aktuální pomocí pravidelné retraining nejnovější data.

Další informace o tento případ použití naleznete [zde](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18945).

#### <a name="use-case-qualification-criteria--prerequisites"></a>Použít kritéria případu kvalifikace – požadavky
Hlavní sílu Cortana Intelligence je výkonné možnosti zavádět a škálovat machine learning zaměřená na řešení v oblasti. Je navržen pro podporu tisíc předpovědi, které jsou spouštěny souběžně. Může automaticky škálovat podle měnících vzor spotřeby. Proto je fokus na řešení na přesnost a výpočetní výkon. Například nástroj společnosti má zájem generovala přesné energie vyžádání prognózy do příští hodiny a pro každou hodinu dne. Na druhé straně jsme zájem o méně odpovídání na otázku, proč je vyžádání předpovědět, které ho je (přímo pro model se postará o,).

Proto je důležité si uvědomit, že všechny případy použití a obchodní efektivně vyřešení problémů pomocí machine learning.

Cortana Intelligence a machine learningu může být velice efektivní v řešení daného obchodního problému, když se splní následující kritéria:

* Je obchodního problému v dolním **prediktivní** ve své podstatě. V příkladu případu použití prediktivní je nástroj společnost, která se má odhadnout power zatížení daného transformovny během příští hodiny. Analýza a řazení ovladače historických poptávky na druhé straně by **popisný** ve své podstatě a proto méně použitelné.
* Je zřejmé **cestu akce** mají být provedeny, jakmile je k dispozici předpovědi. Například predikci přetížení na transformovny během příští hodiny můžete aktivovat proaktivní akce snižuje zatížení, který je přidružený tento transformovny a proto potenciálně brání přetížení.
* Představuje případ použití **typický typ problému** tak, že když vyřešeny ho můžete připravit přechod případy použití pro jiné podobné řešení.
* Můžete nastavit zákazník **kvantitativní a kvalitativní cíle** k předvedení implementace úspěšné řešení. Dobrý kvantitativní cíl pro energie vyžádání prognózy by být například prahovou hodnotu požadovanou přesnost (*například*, je povoleno až 5 % došlo k chybě) nebo pokud predikci transformovny přetížení pak přesnost (počet pozitivních true) a pro vyvolání (rozsah true pozitivních) by měla být s danou prahovou hodnotu. Těchto cílů by měl být odvozen od zákazníka obchodních cílů.
* Je zřejmé **scénáře integrace** s pracovním postupem obchodní společnosti. Například Prognóza zatížení transformovny lze integrovat do Centrum ovládacího prvku mřížky tak, aby přetížení prevence aktivity.
* Zákazník má připraven k použití **dat s dostatečnou kvality** pro podporu případ použití (v další části, další informace naleznete v **Data Quality**, z této playbook).
* Architektura vyhoví zaměřená na data v cloudu zákazníka nebo **cloudové machine learning**, včetně Azure ML a další součásti Cortana Intelligence Suite.
* Zákazník je ochotná navázat **tok dat koncová** tohoto pracoviště doručování dat do cloudu průběžně a je ochotná **zprovoznit** řešení.
* Zákazník je připraven k **vyhradit prostředky** kdo bude mít aktivně zapojení během počáteční pilotní implementace tak, aby znalosti a vlastnictví řešení lze přesunout do zákazníka po úspěšném dokončení.
* Zákazník zdroj by měl být **zkušený data professional**, pokud možno vědecký pracovník data.

Kvalifikace případu použití podle výše uvedená kritéria může výrazně zlepšit úspěšnost případu použití a vytvořit funkční beachhead pro implementaci případy budoucí použití.

### <a name="cloud-based-solutions"></a>Řešení založená na cloudu
Cortana Intelligence Suite v Azure je integrované prostředí, který se nachází v cloudu. Nasazení řešení pokročilou analýzu v cloudovém prostředí obsahuje, že významné výhody pro firmy a ve stejnou dobu může to znamenat velkou změnu pro společnosti, že stále použít místní IT řešeními. V rámci odvětví energie není jasný trend postupné migrace operací do cloudu. Tento trend souvisí společně s vývojem inteligentní mřížky jak je popsáno výše, v **odvětví trendy**. Jak tato scénářem se zaměřuje na cloudové řešení v doméně energie, je důležité popisují výhody a další důležité informace o nasazení řešení založená na cloudu.

Možná největších výhod cloudové řešení jsou náklady. Jako řešení využívá součástí nasazení cloudu, neexistuje žádný předem náklady a náklady na spotřebu (náklady z zboží prodané) komponenty s ním spojená. To znamená, že není nutné investovat do hardwaru, softwaru a údržby IT, a proto je podstatně snížit riziko firmy.

Další důležité výhodou je strukturu průběžnými platbami náklady cloudové řešení. Servery založené na cloudu pro computing nebo úložiště můžete nasadit a škálovat na základě právě podle potřeby. To představuje výhod efektivitu nákladů na cloudové řešení.

Nakonec je pro Investujete do IT údržby nebo vývoj budoucí infrastruktury, jako to vše je součástí Cloudová nabídka není nutné. Rozsahu Cortana Intelligence Suite zahrnuje nejvhodnější třída služby a jeho silniční mapu udržuje vyvíjející se. Nové funkce, součásti a možnosti jsou neustále zavedené a momentální.

Pro společnosti, který je právě spouští jeho přechodu do cloudu důrazně doporučujeme provést postupný přístup implementací mapě cloudu migrace. Věříme, že pro nástroje a společnosti v doméně energie, případy použití, které jsou popsané v této playbook reprezentuje příležitost vynikající pro pilotní nasazení řešení prediktivní analýzy v cloudu.

#### <a name="business-case-justification-considerations"></a>Aspekty obchodní případu při zarovnání do bloku
V mnoha případech může být zákazník zájem o provedení obchodního oprávnění pro danou použití případ, ve kterém jsou cloudové řešení a Machine Learning důležité součásti. Na rozdíl od v případě místních řešení, v případě cloudové řešení je minimální komponentu předem náklady a většina elementů náklady jsou přidruženy skutečném využití. Pokud jde o nasazení energie prognózy řešení na webu Cortana Intelligence Suite, více služeb lze integrovat s struktura single běžné náklady. Například databáze (*například*, SQL Azure) lze použít k uložení nezpracovaná data a pak pro samotný předpovídá Azure ML se používá k hostování služby prognózy. V tomto příkladu můžou zahrnovat strukturu náklady na úložiště a transakčních komponent.

Na druhé straně jeden měli rozumět obchodní hodnotu operačního vyžádání energie prognózy (krátkodobého nebo dlouhodobého hlediska). Ve skutečnosti je důležité si uvědomit obchodní hodnotu každé prognózy operace. Například přesně prognózy power zatížení dobu následujících 24 hodin můžete zabránit nadbytečné produkce nebo může zabránit přetížení v mřížce a to je možné kvantifikovat z hlediska finanční úspor na každý den.

Základní vzorec pro výpočet finanční výhodou vyžádání prognózy řešení by: ![základní vzorec pro výpočet finanční výhodou vyžádání prognózy řešení](media/cortana-analytics-playbook-demand-forecasting-energy/financial-benefit-formula.png)

Vzhledem k tomu, že Cortana Intelligence Suite poskytuje průběžnými platbami cenový model, není nutné pro komponentu pevné náklady na tento vzorec by docházelo. Tento vzorec, lze vypočítat na základě denně, měsíční nebo roční.

Aktuální Cortana Intelligence Suite a Azure ML cenových plánů najdete [zde](http://azure.microsoft.com/pricing/details/machine-learning/).

### <a name="solution-development-process"></a>Proces vývoj řešení
Vývoj cyklus vyžádání energie prognózy, řešení obvykle zahrnuje 4 fází, všechny z nich uděláme použít cloudové technologie a služby v rámci Cortana Intelligence Suite.

To je znázorněno v následujícím diagramu:

![Cyklus inteligentní mřížky](media/cortana-analytics-playbook-demand-forecasting-energy/smart-grid-cycle.png)

Následující odstavce popisuje tento proces krok 4:

1. **Shromažďování dat** – všechny pokročilé analýzy na základě řešení využívá data (najdete v části **pochopení dat**). Konkrétně pokud jde o prediktivní analýzy a vytváření prognóz, spoléháme na probíhající, dynamické tok dat. V případě energie vyžádání prognózy, tato data můžete použít jako zdroj přímo z inteligentní měřidla nebo již agregovat na místní databázi. Také spoléháme na jiné externí zdroje dat, jako je například počasí a teploty. Tento probíhající tok dat je nutné orchestrovat, naplánovat a uložit. [Azure Data Factory](http://azure.microsoft.com/services/data-factory/) (ADF) je naše hlavní centrem k provedení této úlohy.
2. **Modelování** – pro přesné a spolehlivé energie prognózy jeden musí vyvíjet (train) a udržovat skvělé model, díky použijete historických dat a extrahuje smysluplný a prediktivní vzorů v datech. Oblasti Learning počítače (ML) má byl narůstá s více pokročilé algoritmy pravidelně vyvíjených. Azure ML Studio nabízí skvělý uživatelské prostředí, která pomáhá využívat nejpokročilejší algoritmy ML v rámci dokončení pracovní postup. Tento pracovní postup je znázorněna v intuitivní vývojový diagram a zahrnuje data přípravy, funkce extrakce, modelování a vyhodnocení modelu. Uživatel může pro vyžádání obsahu v stovky různých modely, které jsou zahrnuté v tomto prostředí. Na konci této fáze vědecký pracovník dat bude mít pracovní model, který je plně vyhodnotí a připravena k nasazení.
   
   Následující obrázek je obrázek typické pracovního postupu:
   
   ![Modelování pracovního postupu](media/cortana-analytics-playbook-demand-forecasting-energy/modeling-workflow.png)
3. **Nasazení** – s modelem pracovní ručně, dalším krokem je nasazení. Zde je model převeden na webová služba, která vystavuje rozhraní RESTful API, která se může souběžně vyvolat z různých spotřeba klientů na Internetu. Azure ML poskytuje jednoduchý způsob nasazení model přímo z nástroje Azure ML Studio jediným kliknutím na tlačítko. Pod pokličkou se stane celý proces nasazení. Toto řešení může automaticky škálovat podle požadované spotřeby.
4. **Spotřeba** – v této fázi se ve skutečnosti uděláme modelu prognózy lze použít k vytvoření předpovědi. Spotřeby můžete vycházejí z aplikace uživatele (*například*, řídicí panel) nebo přímo z operačního systému, jako/poptávky vyrovnávání systému nebo řešení optimalizace mřížky. Několik případů použití může vycházejí z jeden model.

## <a name="data-understanding"></a>Pochopení dat
Po pokrývajících aspekty obchodní (najdete v části **obchodní principy**) vytváření prognóz řešení poptávky energie, jsme jste připraveni popisují část dat. Řešení prediktivní analýzy spoléhá na spolehlivé data. U prognózy vyžádání energie, spoléháme na historické spotřeby data pomocí různých úrovní členitosti. Historických dat se používá jako suroviny. Určitým pečlivě analýzy, ve kterém se data vědecký pracovník identifikovat prognostické (také označované jako funkce), které můžou být přepnuté do modelu, který nakonec vygeneruje požadované prognózy.

Ve zbývající části této části jsme se popisují různé postupy a požadavky pro pochopení dat a zajištění do použitelného formátu.

### <a name="the-model-development-cycle"></a>Cyklu vývoje modelu
Vytváření dobrý prognózy modely vyžaduje některé pečlivě přípravu a plánování. Rozdělení procesu modelování do více kroků a zaměřené na jednom kroku současně může výrazně zlepšit výsledek celý proces.

Následující diagram znázorňuje, jak může proces modelování rozdělit do více kroků:

![Cyklu vývoje modelu](media/cortana-analytics-playbook-demand-forecasting-energy/model-development-cycle.png)

Jak je vidět, že tento cyklus se skládá z šesti kroků:

* Formulování problém
* Přijímání dat a zkoumání dat
* Příprava dat a funkce inženýrství
* Modelování
* Vyhodnocení modelu
* Vývoj

Ve zbývající části této části jsme se popisují jednotlivé kroky a položky, které je třeba zvážit při každém kroku.

### <a name="problem-formulation"></a>Formulování problém
Zvážit jsme problém formulování jako nejdůležitější krok, který jeden nemusí provádět před jejich implementací řešení prediktivní analýzy. Zde jsme by transformace obchodního problému a rozložit na konkrétní prvky, které lze vyřešit pomocí data a modelování techniky. Je dobrým zvykem formulovali problém jako sada otázek, na které má odpovědět. Zde jsou některé možné otázky, které nejsou k dispozici v rámci oboru prognózy na energii na vyžádání:

* Co je očekávané zatížení na jednotlivých transformovny v další hodinu nebo den?
* V průběhu dne všimnete Moje mřížky poptávky ve špičce?
* Jak pravděpodobně je můj mřížky pro udržení zatížení ve špičce očekávané?
* Kolik energie měl stanice power generovat během každou hodinu dne?

Formulování tyto otázky umožňuje zaměřit se na získávání správná data a implementace řešení, které je plně v souladu s obchodního problému po ruce. Kromě toho jsme pak můžete nastavit některé klíčové metriky, které nám k vyhodnocení výkonu modelu umožňují. Například jak přesná Prognóza měli a jaký je rozsah chybu, která se bude stále přijatelné podle firmy?

### <a name="data-sources"></a>Zdroje dat
Moderní inteligentní mřížky shromažďuje data z různých částí a komponent mřížky. Tato data představuje různé aspekty operaci a využití power mřížky. V rámci oboru vyžádání energie prognózy jsme jsou omezení diskuzi na zdroje dat, které odráží skutečný vyžádání spotřeby. Jeden zdroj důležité spotřeby energie, jsou inteligentní měřidla. Nástroje po celém světě rychle nasazujete inteligentní měřidla pro jejich příjemce. Inteligentní měřidla zaznamenávat skutečné spotřebu energie a neustále předávání tato data zpět do nástroje společnosti. Data se shromažďuje a odesílá zpět v pevných intervalech, od každých 5 minut až 1 hodina. Pokročilejší inteligentní měřidla lze naprogramovat vzdálené řízení a vyvážit skutečné spotřebě v rámci domácnost. Inteligentní měření dat je poměrně spolehlivé a obsahuje časové razítko. Tím je důležité složky pro vyžádání prognózy. Měření dat, se dají agregovat (sečtené) na různých úrovních v rámci topologie mřížky: transformer, transformovny, oblast, *atd*. Jsme pak můžete vybrat úroveň požadované agregace pro něj vytvořit model prognózy. Například pokud společnost nástroj chtěli předpovídat budoucí zatížení na jednotlivých jeho mřížky trakčních pak všechny měřidla data může být agregován pro každé jednotlivé transformovny a použít jako vstup pro model prognózy. Označujeme jako zdroj interních datových inteligentní měřidla.

Vyžádání prognózy spolehlivé energie bude také závisí na jiných externích zdrojů dat. Jeden důležitý faktor, který má vliv na spotřebu energie je počasí, nebo přesněji teploty. Historická data zobrazují silné korelace mezi mimo teploty a spotřebu energie. Během aktivního letní dnů spotřebitelům při použití jejich klimatizace a během jedná o zimní zapnutí systémů vytápění. Klíč je proto spolehlivý zdroj historických teplot v umístění v mřížce. Kromě toho také spoléháme na přesná prognóza z teploty jako nástroj pro odhad spotřeby energie.

Další externích zdrojů dat. může také pomoci při vytváření modelů prognózy na energii na vyžádání. Ty mohou obsahovat dlouhodobé klimatem změny, ekonomické indexy (*například*, HDP) a další. V tomto dokumentu jsme nebude obsahovat tyto ostatních zdrojů.

### <a name="data-structure"></a>Datové struktury
Po identifikaci požadované datových zdrojů, bychom rádi Ujistěte se, že nezpracovaných dat, které se shromáždily zahrnuje funkce správná data. K sestavení modelu prognózy spolehlivé vyžádání, by potřebujeme zajistit, že data shromážděná obsahuje datové prvky, které může pomoci předpovídat budoucí poptávky. Tady jsou některé základní požadavky týkající se dat strukturu (schéma) nezpracovaná data.

Nezpracovaná data se skládá z řádků a sloupců. Každé měření je reprezentován jako jednoho řádku dat. Každý řádek dat obsahuje více sloupců (také označované jako funkce nebo polí).

1. **Časové razítko** – pole časového razítka představuje skutečný čas, kdy byla zaznamenána měření. Ho by měly splňovat jeden z běžných formátů data a času. Datum a čas částí by měly být zahrnuty. Ve většině případů není třeba dobu, aby se zaznamenávaly do druhé úrovně podrobností. Je důležité určete časové pásmo, ve kterém se zaznamená data.
2. **Měřicí ID** – v tomto poli identifikuje měřidla nebo měření zařízení. Je kategorií proměnné a může být kombinací číslic a znaků.
3. **Hodnota spotřeby** – jedná se o skutečné spotřebě v dané datum a čas. Spotřeby můžete měřená v kWh (kilowatt-hour) nebo jakékoliv preferované jednotky. Je důležité si uvědomit, že Měrná jednotka musí zůstat konzistentní napříč všech měření v datech. V některých případech může být spotřeba dodán více než 3 power fáze. V takovém případě by potřebujeme ke shromažďování všech fázích nezávislé spotřeby.
4. **Teplotní** – teplota je obvykle shromážděné z nezávislé zdroje. Ale musí být kompatibilní s datům o spotřebě. Měl by obsahovat časovým razítkem jak bylo popsáno výše, který vám umožní, aby mohla být synchronizována s daty o skutečné spotřebě. Hodnota teploty lze zadat v stupňů c nebo Fahrenheita ale musí zůstat konzistentní napříč všech měření.
5. **Umístění –** pole umístění obvykle souvisí s na místě, kde jsou shromážděná data teploty. Může být reprezentován jako poštovní směrovací číslo nebo ve formátu zeměpisnou šířku a délku (lat nebo long).

V následujících tabulkách jsou uvedeny příklady dobrý využívání a teploty formát dat:

| **Datum** | **Čas** | **ID měření** | **Fáze 1** | **Fáze 2** | **Fáze 3** |
| --- | --- | --- | --- | --- | --- |
| 7/1/2015 |10:00:00 |ABC1234 |7.0 |2.1 |5.3 |
| 7/1/2015 |10:00:01 |ABC1234 |7.1 |2.2 |4.3 |
| 7/1/2015 |10:00:02 |ABC1234 |6.0 |2.1 |4.0 |

| **Datum** | **Čas** | **Umístění** | **Teplotní** |
| --- | --- | --- | --- |
| 7/1/2015 |10:00:00 |11242 |24.4 |
| 7/1/2015 |10:00:01 |11242 |24.4 |
| 7/1/2015 |10:00:02 |11242 |24.5 |

Jak je vidět výše, tento příklad obsahuje 3 různé hodnoty pro používání přidružené fáze 3 napájení. Všimněte si také, zda jsou odděleny pole data a času, ale mohou také být sdruženy do jednoho sloupce. V takovém případě je sloupec umístění reprezentované ve formátu zip kódu 5 číslic a teploty ve formátu stupeň c.

### <a name="data-format"></a>Formát dat
Cortana Intelligence Suite může podporovat nejběžnější formáty dat sdíleného svazku clusteru, TSV, formát JSON, *atd*. Je důležité, že formát dat zůstává konzistentní pro celý životní cyklus projektu.

### <a name="data-ingestion"></a>Přijímání dat
Vzhledem k tomu, že na energii na vyžádání prognózy je neustále a často předpovědět, jsme musíte zajistit, že je nezpracovaných dat předávaných prostřednictvím procesu přijímání dat plnou a spolehlivé. Proces přijímání musí zaručit, že nezpracovaných dat je k dispozici pro proces prognózy v požadované době. Která znamená, že četnost přijímání dat musí být větší než četnost prognózy.

Například: Pokud naše vyžádání prognózy řešení by vygeneroval novou prognózu v 8:00 AM každý den, pak je potřeba zajistit, že všechny data, která jsou shromážděná během posledních 24 hodin byla plně požity do tohoto bodu a má i obsahují poslední hodiny  data.

Aby bylo možné dosáhnout, Cortana Intelligence Suite nabízí různé způsoby, jak podporují procesu přijímání spolehlivé data. To v popsané dál **nasazení** část tohoto dokumentu.

### <a name="data-quality"></a>Kvality dat.
Nezpracovaná datovém zdroji, které jsou nutné k provádění spolehlivé a přesné vyžádání prognózy musí splňovat kritéria kvality některé základní data. Pokročilé statistické metody lze kompenzovat některé možné data quality problém, ale potřebujeme ještě Ujistěte se, že jsme se při překročení prahové hodnoty některé základní data quality při příjem nová data. Zde je několik aspekty týkající se kvality nezpracovaná data:

* **Chybí hodnota** – vztahuje situaci při konkrétní měření nebyla shromážděna. Základní požadavek je, že chybí hodnota rychlost, jakou by neměla být větší než 10 % pro jakékoli dané časové období. V případě, že jedna hodnota je chybějící by měl být indikován pomocí předem definované hodnoty (například: '9999') a zda není '0', který může být hodnota je neplatná.
* **Přesnost měření** – se skutečnou hodnotou spotřeby nebo teploty by měl být přesně zaznamenán. Nesprávné měření způsobí nepřesné prognózy. Chyba měření obvykle by mělo být menší než 1 % relativní vůči hodnotě true.
* **Čas měření** – je potřeba, časové razítko skutečné dat shromážděných nesmí lišit o více než 10 sekund vzhledem k true době vlastní měření.
* **Synchronizace** – Pokud se používají více zdrojů dat (*například*, využívání a teploty) jsme Ujistěte se, že neexistují žádné synchronizaci času problémy mezi nimi. To znamená, že je časový rozdíl mezi shromážděné časové razítko ze všech dva nezávislé datových zdrojů by nemělo přesáhnout více než 10 sekund.
* **Latence** – jak je popsáno výše, v **přijímání dat**, jsme jsou závislé na spolehlivé datového toku a přijímání procesu. K řízení, které jsme musí zajistit, aby jsme kontrolu nad latence data. To je zadán jako časový rozdíl mezi časem, která byla provedena vlastní měření a čas, kdy byla načtena do úložiště Cortana Intelligence Suite a je připravený k použití. Pro krátkodobou zatížení prognózy nízkou celkovou latenci nesmí být větší než 30 minut. Pro dlouhodobé zatížení prognózy nízkou celkovou latenci nesmí být větší než 1 den.

### <a name="data-preparation-and-feature-engineering"></a>Příprava dat a funkce inženýrství
Po nezpracovaná data byla požity. (viz **přijímání dat**) a byla bezpečně uložené, je připravena k provedení. Fázi přípravy dat je v podstatě trvá nezpracovaná data a převádění (transformace, změna tvaru) do formuláře pro fázi modelování. Jednoduché operace, například pomocí sloupci nezpracovaná data, jako je s jeho skutečná hodnota měřená, standardizované hodnoty, složitějších operací, jako který může zahrnovat [čas obložení](https://en.wikipedia.org/wiki/Lag_operator)a další. Nově vytvořené datové sloupce, které se označují jako funkce data a proces generování to se označuje jako funkce inženýrství. Na konci tohoto procesu nám nové sady dat, který byl získán z nezpracovaná data a lze použít pro modelování. Kromě toho musí fázi přípravy dat postará o chybějící hodnoty (viz **Data Quality**) a kompenzovat je. V některých případech by také musíme normalizaci data a ujistěte se, že všechny hodnoty jsou zobrazeny ve stejné měřítko.

V této části jsme některé běžné funkce dat, které jsou součástí energii seznamu prognózy vyžádání modelů.

**Čas řízené funkce:** tyto funkce jsou odvozeny od data datum/časové razítko. Tyto jsou extrahovat a převést do kategorií funkcí, jako je:

* Čas dne – to je hodina dne, který přebírá hodnoty od 0 do 23
* Den v týdnu – to představuje den v týdnu a přijímá hodnoty od 1 (neděle) na 7 (sobota)
* Den v měsíci – to představuje skutečný datum a může obsahovat hodnoty od 1 do 31
* Měsíc roku – to představuje měsíc a přijímá hodnoty od 1 (leden) do 12 (prosinec)
* Víkendu – Toto je funkce binární hodnotu, která přijímá hodnoty 0 pro dny v týdnu nebo 1 pro víkendu
* Svátek - Toto je funkce binární hodnotu, která přijímá hodnoty 0 pro regulární den nebo 1 pro svátek
* Podmínky Fourierova – Fourierova podmínky jsou váhu, které jsou odvozeny od časové razítko a slouží k zaznamenání sezónnosti (cykly) v datech. Vzhledem k tomu, že jsme může mít několik ročních období v našich dat potřebujeme více Fourierova podmínky. Vyžádání hodnoty může mít například roční, týdenní a denní období nebo cykly které bude mít za následek 3 Fourierova podmínky.

**Funkce nezávislé měření:** nezávislé funkce zahrnují všechny datové prvky, které jsme chtěli používat jako prognostické v našem modelu. Tady jsou vyloučeny závislé funkci, která by musíme předpovědi.

* Funkce opoždění – jedná se o čas zapuštěno hodnoty aktuální. Funkce opoždění 1 například bude obsahovat hodnotu vyžádání do předchozí hodiny (za předpokladu, že po hodinách data) relativně k aktuální časové razítko. Jsme podobně přidat prodleva 2, 3, funkce lag *atd*. Skutečné kombinace prodleva funkce, které se používají určuje během fáze modelování hodnocení výsledků modelu.
* Dlouhodobé trendů – tato funkce představuje lineární růst v vyžádání mezi let.

**Závislé funkce:** závislé funkce je sloupec dat, který rádi bychom znali našeho modelu předpovědi. S [počítač učení se supervizí](https://en.wikipedia.org/wiki/Supervised_learning), musíme nejprve cvičení modelu s použitím funkce závislé (která je také označována jako popisky). To umožňuje modelu další vzory v data související s funkci závislé. Požadavků na energii prognózy obvykle chcete předpovídat aktuální, a proto jsme proč ji používat jako závislé funkce.

**Zpracování chybějící hodnoty:** fázi přípravy dat, musíme určit nejlepší strategii pro zpracování chybějící hodnoty. To se většinou provádí pomocí různé statistické [data imputace metody](https://en.wikipedia.org/wiki/Imputation_\(statistics\)). V případě energie vyžádání prognózy jsme obvykle dává chybějící hodnoty pomocí klouzavého průměru z předchozí dostupné datových bodů.

**Data normalizaci:** normalizace dat je jiný typ transformace, který se používá k zajištění všech číselná data, jako je například vyžádání prognózy podobné škálování. To obvykle pomáhá zlepšit přesnost a přesnosti modelu. Jsme by obvykle k tomu vydělením skutečná hodnota rozsahu data.
Původní hodnotu to bude škálovat do menší oblast, obvykle mezi -1 a 1.

## <a name="modeling"></a>Modelování
Fáze modelování je, kde probíhá převod dat do modelu. V základní tohoto procesu existuje pokročilé algoritmy, které kontrola historických dat (Cvičná data), rozbalte vzory a sestavení modelu. Tento model můžete později použít k předvídání na nová data, který nebyl použit k vytvoření modelu.

Jakmile model spolehlivé práci jsme ho pak může použít k skóre pro nová data, která je strukturovaná zahrnout požadované funkce (X). Proces vyhodnocování budou používat trvalé modelu (objekt z fázi školení) a předvídání Cílová proměnná, který je označený jako Ŷ.

### <a name="demand-forecasting-modeling-techniques"></a>Vyžádání prognózy modelování techniky
V případě vyžádání prognózy, provedeme pomocí historických dat, který je seřazené podle času. Obecně označujeme data, která zahrnuje časové dimenze jako [časové řady](https://en.wikipedia.org/wiki/Time_series). Cílem v časové řady modelování je nalezení čas související trendy, sezónnosti, auto korelace (korelace v čase) a formulovali v modelu.

V posledních letech pokročilé algoritmy bylo vyvinuto zohlednit časové řady prognózy a zvyšte tak přesnost předpovědi. Stručně probereme několik z nich zde.

> [!NOTE]
> V této části není určena pro použití jako machine learning a předpovídat přehled, ale spíše jako krátký přehled modelování techniky, které se běžně používají pro vyžádání prognózy. Další informace a vzdělávací materiály o časové řady prognózy, důrazně doporučujeme příručce online [vytváření prognóz: Principy a postup](https://www.otexts.org/book/fpp).
> 
> 

#### <a name="ma-moving-averagehttpswwwotextsorgfpp62"></a>[**MA (klouzavého průměru)**](https://www.otexts.org/fpp/6/2)
Klouzavý průměr je jedním z první analytické techniky, které již byly použity pro předpověď časové řady a je stále jedním z nejčastěji nejčastěji používané techniky k dnešnímu dni. Je také základem pro pokročilejší prognózy techniky. S klouzavý průměr jsme jsou prognózy další datového bodu jako průměr prostřednictvím tisíc nejnovější bodů, kde K označuje pořadí klouzavého průměru.

Přesunutí průměrná technika má za následek vyhlazování prognózy a nemusí proto zpracovat dobře velké volatility v datech.

#### <a name="ets-exponential-smoothinghttpswwwotextsorgfpp75"></a>[**ETS (exponenciální vyrovnání)**](https://www.otexts.org/fpp/7/5)
Exponenciální vyhlazování (ETS) je rodina různé metody, které používají váženým průměrem poslední datové body k předvídání další datového bodu. Cílem je, a přiřadit vyšší váhou novější hodnoty postupně snížit tento váha pro starší měřené hodnoty. Existuje několik různých metod s této rodině, některé z nich zahrnout zpracování sezónnosti v datech, jako [Holt Winters sezónní metoda](https://www.otexts.org/fpp/7/5).

Některé z těchto metod také zvážit sezónnosti data.

#### <a name="arima-auto-regression-integrated-moving-averagehttpswwwotextsorgfpp8"></a>[**ARIMA (automatické regrese integrovaný klouzavý průměr)**](https://www.otexts.org/fpp/8)
Automatické regrese integrované přesunutí průměrná (ARIMA) je jiné rodiny metod, které se běžně používá pro předpověď časové řady. Auto-regression metody prakticky kombinuje s klouzavý průměr. Chcete-li výpočetní další bod datum metody Automatické regrese použijte regrese modely provedením předchozích časových řad hodnoty. Metody ARIMA platí také rozdílové metody, které obsahují výpočet rozdílu mezi datovými body a jejich namísto původní hodnoty měřených použití. Nakonec ARIMA také využívá přesunutí průměrná techniky, které jsou popsané výše. Kombinace všechny tyto metody v různé způsoby, kterými je co vytvoří řadu metody ARIMA.

ETS a ARIMA se často používá dnes pro prognózy vyžádání energie a mnoho dalších prognózy problémů. V mnoha případech se zkombinují společně k poskytování velmi přesné výsledky.

**Obecné vícenásobná regrese** modely regrese může být nejdůležitější modelování přístup v rámci domény strojového učení a statistiky. V kontextu časové řady použijeme regresní předpovídat budoucí hodnoty (*například*, poptávky). V regrese jsme trvat lineární kombinaci prognostické a další váhu těchto prognostické (také označované koeficientem) během procesu školení. Cílem je vytvořit regresní přímky, který bude prognózy naše předpovězené hodnoty. Regrese metody jsou vhodné, když Cílová proměnná je číselné a proto také vyhovuje prognózy časové řady. Je širokou škálu regrese metody včetně velmi jednoduché regrese modely, jako například [lineární regrese](https://en.wikipedia.org/wiki/Linear_regression) a další pokročilé těch, které jsou například rozhodovací stromy, [doménových struktur náhodných](https://en.wikipedia.org/wiki/Random_forest), [Neural Sítě](https://en.wikipedia.org/wiki/Artificial_neural_network)a Boosted Decision Trees.

Vytváření energie vyžádání prognózy jako problém regrese nám poskytuje značnou flexibilitu při výběru naší datové funkce, které mohou být kombinovány z aktuální požadavek časových řad dat a vnějším faktorům, jako je například teploty. Další informace o vybrané funkce, které jsou popsané v Engineering funkce (najdete v části **Příprava dat a funkce Engineering**) části této scénářem.

Z našich zkušeností s provádění a nasazení na energii na vyžádání prognózy pilotního zjistili jsme, že mohou vyvolávat nejlepších výsledků dosáhnete pokročilých regrese modely, které jsou k dispozici v Azure ML a jsme použít z nich.

## <a name="model-evaluation"></a>Vyhodnocení modelu
Vyhodnocení modelu má zásadní roli v rámci **cyklu vývoje modelu**. V tomto kroku podíváme do ověřování modelu a jeho výkon s skutečných dat. Během kroku modelování použijeme pro trénování modelu součástí všechna dostupná data. Během fáze hodnocení jsme trvat zbytek dat pro otestování modelu. Prakticky znamená, že jsme se napájení modelu nová data, která má se změnili a obsahuje stejné funkce jako školení datovou sadu. Však během procesu ověření používáme modelu předpovědi Cílová proměnná, nikoli poskytují dostupné Cílová proměnná. Často označujeme jako model vyhodnocování tohoto procesu. Pak jsme použije true cílové hodnoty a porovnejte je s předpokládaných těm, které jsou. Cílem je měřit a minimalizovat chyba předpovědi, znamená rozdíl mezi jsou předpovědi a hodnota true. Kvantifikace měření chyb je klíč, protože jsme chtěli doladit modelu a ověření, zda je ve skutečnosti snížení chyba. Doladění modelu lze provést úpravou modelu parametry, které řídí proces learning nebo přidáním nebo odebráním data funkce (označuje jako [parametry oblouku](https://channel9.msdn.com/Blogs/Azure/Data-Science-Series-Building-an-Optimal-Model-With-Parameter-Sweep)). Prakticky to znamená, že může potřebujeme k iteraci mezi funkce technikům, modelování a modelu zkušební fáze několikrát, dokud jsme snižte chyba na požadované úrovni.

Je důležité zvýraznění, že chyba předpovědi nebude nikdy nulové se nikdy model, který lze přesně odhadnout každý výsledek. Je však určitého rozsahu chyby, která je akceptovatelná podle firmy. Během procesu ověření bychom rádi zajistěte, aby naše chyba předpovědi modelu na úrovni nebo lepší než úroveň tolerance firmy. Proto je důležité nastavit úroveň přípustné chyby na začátku cyklu během **problém formulování** fáze.

### <a name="typical-evaluation-techniques"></a>Techniky typické vyhodnocení
Existují různé způsoby, ve které předpovědi chyba měří a kvantifikovány. V této části se zaměříme diskuzi na vyhodnocení techniky, které jsou relevantní pro časovou řadu a specifické pro vyžádání energie prognózy.

#### <a name="mapehttpsenwikipediaorgwikimeanabsolutepercentageerror"></a>[**MAPE**](https://en.wikipedia.org/wiki/Mean_absolute_percentage_error)
MAPE znamená znamenat absolutní chyba procento. S MAPE jsme jsou computing rozdíl mezi každou naplánované bodu a skutečnou hodnotou tohoto bodu. Potom jsme vyčíslení chyba jednotlivých bodů pomocí výpočtu poměru rozdíl vzhledem k aktuální hodnotu. V posledním kroku průměrná jsme tyto hodnoty. Matematický vzorec použitý pro MAPE je následující:

![Vzorec MAPE](media/cortana-analytics-playbook-demand-forecasting-energy/mape-formula.png)
*kde A<sub>t</sub> je skutečná hodnota F<sub>t</sub> je předpovězené hodnoty a n je prognózy horizont.*

## <a name="deployment"></a>Nasazení
Jakmile jsme nailed dolů fázi modelování a ověření výkonu modelu jsme připravení přejít do fáze nasazení. V tomto kontextu znamená nasazení, umožňující klientovi využívat modelu spuštěním skutečné předpovědi na něm ve velkém měřítku. Koncept nasazení je klíč v Azure ML, protože naše hlavním cílem je neustále vyvolání předpovědi oproti právě z dat získat podrobnější informace o. Fáze nasazení je část, kde jsme povolit model, který se má používat při velkém měřítku.

V kontextu prognózy vyžádání energie naše cílem je vyvolání nepřetržitý a pravidelné prognózy a zajistit, že je k dispozici pro model čerstvá data a že prognózy data se odesílají zpět do klienta náročná.

### <a name="web-services-deployment"></a>Nasazení webové služby
Hlavní nasadit stavební blok v Azure ML je webová služba. Toto je co nejúčinnější způsob, jak povolit spotřeba prediktivního modelu v cloudu. Webové služby zapouzdří modelu a zabalí s [dosáhl standardu RESTful](http://www.restapitutorial.com/) rozhraní API (Application Programming Interface). Rozhraní API slouží jako součást žádný kód klienta, jak je znázorněno v následujícím diagramu.

![Jsme nasazení služby a spotřeba](media/cortana-analytics-playbook-demand-forecasting-energy/web-service-deployment-and-consumption.png)

Jak je vidět, webové služby je nasazen v cloudu Cortana Intelligence Suite a mohou být vyvolány pomocí jeho zveřejněné koncový bod REST API. Jiný typ klientů v různých doménách, můžete současně vyvolat služby pomocí rozhraní Web API. Pro podporu tisíce souběžných volání můžete škálovat také webovou službu.

### <a name="a-typical-solution-architecture"></a>Architektura typické řešení
Při nasazování vyžádání energie prognózy řešení, jsme zajímá nasazení koncová řešení, které překročí předpovědi webové služby a usnadňuje celého datového toku. V době jsme vyvolání novou prognózu by potřebujeme zajistit, že je model dodáni s funkcemi aktuální data. Která znamená, že nově shromážděných nezpracovaná data je neustále požity, zpracování a transformovat na požadovaná sada ve model byl vytvořený funkcí. Ve stejnou dobu bychom rádi zpřístupnit pro element end, které využívají klienti dat prognózy. Příklad datového toku cyklus (nebo datovém kanálu) je znázorněno v následujícím diagramu:

![Tok dat koncová prognózy spotřeby energie](media/cortana-analytics-playbook-demand-forecasting-energy/energy-demand-forecase-end-data-flow.png)

Toto jsou kroky, které provést v rámci prognózy cyklu na energii na vyžádání:

1. Miliony měřidla nasazené data jsou neustále generování spotřebě energie v reálném čase.
2. Tato data se shromažďují a nahrál do úložiště v cloudu (*například*, objektů Blob v Azure).
3. Před zpracováním, nezpracovaná data se shromažďují na místní úrovni nebo transformovny podle definice firmy.
4. Funkce zpracování (viz **Příprava dat a zpracování funkce**) pak proběhne a vytváří data, která je vyžadována pro model cvičení nebo vyhodnocování – funkce sady dat je uložená v databázi (*např*, SQL Azure).
5. Je volána službu znovu školení pro znovu trénování modelu prognózy lze – je trvalé tuto aktualizovanou verzi modelu, takže lze vyhodnocování webovou službou.
6. Vyhodnocování webová služba je vyvolána podle plánu, který nejlépe odpovídá na požadovanou četnost prognózy.
7. Prognózy data jsou uložena v databázi, která byla přístupná pomocí klienta spotřeba end.
8. Spotřeba klient načte prognózy, použije ji zpět do mřížky a využívá v souladu s případ vyžaduje použití.

Je důležité si uvědomit, že tento celý cyklus je zcela automatizovat a spouští podle plánu. Celý orchestraci tento cyklus dat lze provést pomocí nástrojů, jako [Azure Data Factory](http://azure.microsoft.com/services/data-factory/).

### <a name="end-to-end-deployment-architecture"></a>Architektura a provést tak kompletní nasazení
Abyste mohli nasadit prakticky do energie vyžádání prognózy řešení na webu Cortana Intelligence, potřebujeme zajistit, že požadované součásti jsou vytvořeno a správně nakonfigurován.

Následující diagram znázorňuje typické Cortana Intelligence na základě architektury, který implementuje a orchestruje cyklus toku dat, které je popsáno výše:

![Architektura a provést tak kompletní nasazení](media/cortana-analytics-playbook-demand-forecasting-energy/architecture.png)

Další informace o jednotlivých komponent a celý architektura naleznete šablona řešení energie.

