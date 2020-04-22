---
title: Průvodce UI Azure pro řešení prediktivní údržby – proces vědecké analýzy týmových dat
description: Komplexní popis datové vědy, který pohání řešení prediktivní údržby v několika vertikálních odvětvích.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: previous-author=fboylu, previous-ms.author=fboylu
ms.openlocfilehash: 301e2be0c8b971a0236de6a8b5c8bd5f278c3aee
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686758"
---
# <a name="azure-ai-guide-for-predictive-maintenance-solutions"></a>Průvodce UI Azure pro řešení prediktivní údržby

## <a name="summary"></a>Souhrn

Prediktivní údržba **(PdM)** je populární aplikace prediktivní analýzy, která může pomoci podnikům v několika průmyslových odvětvích dosáhnout vysokého využití aktiv a úspor provozních nákladů. Tato příručka sdružuje obchodní a analytické pokyny a osvědčené postupy pro úspěšný vývoj a nasazování řešení PdM pomocí technologie [platformy Microsoft Azure AI.](https://azure.microsoft.com/overview/ai-platform)

Pro začátek tato příručka představuje obchodní scénáře specifické pro dané odvětví a proces kvalifikace těchto scénářů pro PdM. K dispozici jsou také požadavky na data a techniky modelování pro vytváření řešení PdM. Hlavní obsah příručky je o procesu datové vědy - včetně kroků přípravy dat, konstrukce funkcí, vytváření modelů a modelové operativizace. Chcete-li tyto klíčové koncepty doplnit, tato příručka obsahuje sadu šablon řešení, které pomáhají urychlit vývoj aplikací PdM. Průvodce také poukazuje na užitečné školicí zdroje pro odborníka, aby se dozvěděli více o AI za datovou vědou. 

### <a name="data-science-guide-overview-and-target-audience"></a>Přehled průvodce datovou vědou a cílová skupina
První polovina této příručky popisuje typické obchodní problémy, výhody implementace PdM k řešení těchto problémů a uvádí některé běžné případy použití. Z tohoto obsahu budou mít prospěch osoby s rozhodovací pravomocí (BDM). Druhá polovina vysvětluje datové vědy za PdM a poskytuje seznam pdm řešení postavených pomocí zásad uvedených v této příručce. Poskytuje také studijní cesty a odkazy na školicí materiály. Pro technické činitele s rozhodovací pravomocí (TDM) bude tento obsah užitečný.

| Začněte s ... | Pokud jste ... |
|:---------------|:---------------|
| [Obchodní argumenty pro prediktivní údržbu](#business-case-for-predictive-maintenance) |podnikatel s rozhodovací pravomocí (BDM), který chce snížit prostoje a provozní náklady a zlepšit využití zařízení |
| [Datová věda pro prediktivní údržbu](#data-science-for-predictive-maintenance) |technická osoby s rozhodovací pravomocí (TDM) vyhodnocující technologie PdM, aby porozuměli jedinečným požadavkům na zpracování dat a ai pro prediktivní údržbu |
| [Šablony řešení pro prediktivní údržbu](#solution-templates-for-predictive-maintenance)|softwarový architekt nebo vývojář ai, kteří chtějí rychle postavit demo nebo proof-of-concept |
| [Zdroje školení pro prediktivní údržbu](#training-resources-for-predictive-maintenance) | některé nebo všechny výše uvedené, a chcete se naučit základní koncepty za datové vědy, nástroje a techniky.

### <a name="prerequisite-knowledge"></a>Předpokládané znalosti
Obsah BDM neočekává, že čtenář bude mít žádné předchozí znalosti datové vědy. Pro obsah TDM jsou užitečné základní znalosti statistiky a datové vědy. Doporučuje se znalost dat a ai služeb Azure, Pythonu, R, XML a JSON. Techniky AI jsou implementovány v pythonových a R balíčcích. Šablony řešení se implementují pomocí služeb Azure, vývojových nástrojů a sad SDK.

## <a name="business-case-for-predictive-maintenance"></a>Obchodní argumenty pro prediktivní údržbu

Podniky vyžadují, aby kritické vybavení běželo s maximální účinností a využitím, aby si uvědomily návratnost kapitálových investic. Tato aktiva by se mohla pohybovat od leteckých motorů, turbín, výtahů nebo průmyslových chladičů - které stojí miliony - až po každodenní spotřebiče, jako jsou kopírky, kávovary nebo chladiče vody.
- Ve výchozím nastavení se většina podniků spoléhá na _nápravnou údržbu_, kde jsou díly vyměňovány, když se nezdaří. Nápravná údržba zajišťuje, že díly jsou používány zcela (tedy neplýtvají životností součásti), ale stojí podnik v prostojcích, práci a neplánovaných požadavcích na údržbu (mimo pracovní dobu nebo nevhodná místa).
- Na další úrovni podniky praktikují _preventivní údržbu_, kde určují životnost dílu a udržují ji nebo ji nahrazují před selháním. Preventivní údržba zabraňuje neplánovaným a katastrofickým selháním. Vysoké náklady na plánované prostoje, nedostatečné využití součásti během její životnosti a práce však stále přetrvávají.
- Cílem _prediktivní údržby_ je optimalizovat rovnováhu mezi nápravnou a preventivní údržbou tím, že se umožní výměna součástí _právě včas._ Tento přístup nahradí pouze tyto součásti, pokud jsou blízko k selhání. Prodloužením životnosti součástí (ve srovnání s preventivní údržbou) a snížením neplánovaných nákladů na údržbu a práci (nad nápravnou údržbou) mohou podniky získat úspory nákladů a konkurenční výhody.

## <a name="business-problems-in-pdm"></a>Obchodní problémy v PdM
Podniky čelí vysokému provoznímu riziku v důsledku neočekávaných poruch a mají omezený přehled o příčině problémů ve složitých systémech. Některé z klíčových obchodních otázek jsou:

- Detekujte anomálie ve výkonu nebo funkčnosti zařízení nebo systému.
- Předpovědět, zda aktivum může selhat v blízké budoucnosti.
- Odhadněte zbývající životnost aktiva.
- Identifikujte hlavní příčiny selhání aktiva.
- Určete, jaké akce údržby je třeba provést, do kdy, u majetku.

Typické příkazy cíle z PdM jsou:

- Snižte operační riziko kritického vybavení mise.
- Zvyšte míru návratnosti aktiv předvídáním selhání dříve, než k nim dojde.
- Kontrolujte náklady na údržbu povolením údržby za dobu.
- Nižší úbytk zákazníků, zlepšení image značky a ztráta prodeje.
- Snižte náklady na zásoby snížením úrovně zásob předvídáním bodu přiobjednání.
- Objevte vzory spojené s různými problémy s údržbou.
- Poskytněte klíčové ukazatele výkonu (klíčové ukazatele výkonu), jako je například skóre stavu podmínek aktiv.
- Odhad zbývající životnosti aktiv.
- Doporučte včasné činnosti údržby.
- Povolte zásoby právě včas odhadem dat objednávek pro výměnu dílů.

Tyto příkazy k cíli jsou výchozím bodem pro:

- _data vědců_ k analýze a řešení konkrétních prediktivních problémů.
- _cloudarchitekti a vývojáři_ dát dohromady end-to-end řešení.

## <a name="qualifying-problems-for-predictive-maintenance"></a>Kvalifikační problémy pro prediktivní údržbu
Je důležité zdůraznit, že ne všechny případy použití nebo obchodní problémy mohou být účinně vyřešeny PdM. Existují tři důležitá kvalifikační kritéria, která je třeba vzít v úvahu při výběru problému:

- Problém musí být prediktivní povahy; to znamená, že by měl být cíl nebo výsledek předpovědět. Problém by měl mít také jasnou cestu akce, aby se zabránilo selhání, když jsou zjištěny.
- Problém by měl mít záznam o provozní historii zařízení, které obsahuje _dobré i špatné výsledky_. Soubor opatření přijatých ke zmírnění špatných výsledků by měl být k dispozici také jako součást těchto záznamů. Důležité jsou také zprávy o chybách, protokoly údržby snížení výkonu, opravy a výměna protokolů. Kromě toho jsou užitečné opravy provedené za účelem jejich zlepšení a náhradní záznamy.
- Zaznamenaná historie by se měla odrazit v _příslušných_ údajích, které jsou _dostatečně_ kvalitní na podporu případu použití. Další informace o relevanci a dostatečnosti dat naleznete v [tématu Požadavky na údaje pro prediktivní údržbu](#data-requirements-for-predictive-maintenance).
- A konečně, podnikání by mělo mít odborníky domény, kteří mají jasnou představu o problému. Měli by si být vědomi interních procesů a postupů, aby mohli pomoci analytikovi pochopit a interpretovat data. Měly by být také schopny provést nezbytné změny stávajících obchodních procesů, aby v případě potřeby pomohly shromažďovat správná data pro tyto problémy.

## <a name="sample-pdm-use-cases"></a>Ukázkové případy použití PdM
Tato část se zaměřuje na kolekci případů použití PdM z několika průmyslových odvětví, jako je letectví, utility a doprava. Každá sekce začíná obchodním problémem a pojednává o výhodách PdM, příslušných datech obklopujících obchodní problém a nakonec o výhodách řešení PdM.

| Obchodní problém | Výhody z PdM |
|:-----------------|-------------------|
|**Letectví**      |                   |
|_Zpoždění letu a zrušení_ z důvodu mechanických problémů. Chyby, které nelze opravit včas, mohou způsobit zrušení letů a narušit plánování a provoz. |PdM řešení mohou předpovědět pravděpodobnost zpoždění nebo zrušení letadla z důvodu mechanických poruch.|
|_Selhání letadlových motorů_: Výměna dílů leteckých motorů patří mezi nejběžnější úkoly údržby v leteckém průmyslu. Řešení údržby vyžadují pečlivou správu dostupnosti zásob součástí, dodávky a plánování|Možnost získat informace o spolehlivosti komponent vede k podstatnému snížení investičních nákladů.|
|**Finance** |                         |
|_Selhání atm_ je běžným problémem v bankovním sektoru. Problémem je nahlásit pravděpodobnost, že transakce výběru hotovosti z bankomatu bude přerušena kvůli zablokování papíru nebo selhání části v bankomatu. Na základě předpovědi selhání transakcí bankomaty lze proaktivně obsluhovat, aby se zabránilo selhání z výskytu.| Spíše než aby počítač selhání v polovině transakce, žádoucí alternativou je naprogramovat počítač odepřít službu na základě předpovědi.|
|**Energetický sektor** |                          |
|_Selhání větrných turbín_: Větrné turbíny jsou hlavním zdrojem energie v ekologicky odpovědných zemích/regionech a zahrnují vysoké kapitálové náklady. Klíčovou součástí větrných turbín je generátorový motor, jehož porucha činí turbínu neúčinnou. Je také velmi drahé opravit.|Předvídání kru, jako je MTTF (střední doba do selhání), může energetickým společnostem pomoci předcházet poruchám turbín a zajistit minimální prostoje. Pravděpodobnostselhání bude informovat techniky, aby monitorovali turbíny, u kterých je pravděpodobné, že brzy selžou, a naplánují režimy údržby podle času. Prediktivní modely poskytují přehled o různých faktorech, které přispívají k selhání, což pomáhá technikům lépe pochopit základní příčiny problémů.|
|_Poruchy jističů_: Distribuce elektřiny do domácností a podniků vyžaduje, aby elektrické vedení bylo vždy v provozu, aby byla zaručena dodávka energie. Jističe pomáhají omezit nebo zabránit poškození elektrického vedení během přetížení nebo nepříznivých povětrnostních podmínek. Obchodní problém je zde předpovědět selhání jističe.| Řešení PdM pomáhají snížit náklady na opravy a prodloužit životnost zařízení, jako jsou jističe. Pomáhají zlepšovat kvalitu elektrické sítě snížením neočekávaných poruch a přerušení služeb.|
|**Doprava a logistika** |    |
|_Selhání dveří výtahu:_ Velké výtahové společnosti poskytují kompletní stohový servis pro miliony funkčních výtahů po celém světě. Bezpečnost výtahu, spolehlivost a provozuna jsou hlavními obavami jejich zákazníků. Tyto společnosti sledují tyto a různé další atributy prostřednictvím senzorů, aby jim pomohly s nápravnou a preventivní údržbou. Ve výtahu je nejvýznamnějším problémem zákazníka nefunkční dveře výtahu. Obchodní problém v tomto případě je poskytnout znalostní báze prediktivní aplikace, která předpovídá potenciální příčiny selhání dveří.| Výtahy jsou kapitálové investice pro potenciálně životnost 20-30 let. Takže každý potenciální prodej může být vysoce konkurenční; proto jsou očekávání služeb a podpory vysoká. Prediktivní údržba může těmto společnostem poskytnout výhodu oproti jejich konkurentům v jejich nabídce produktů a služeb.|
|_Selhání kol_: Poruchy kol představují polovinu všech vykolejení vlaků a stojí miliardy pro globální železniční průmysl. Poruchy kol také způsobují zhoršení kolejnic, což někdy způsobuje předčasné prolomení kolejnice. Železniční přestávky vedou ke katastrofickým událostem, jako je vykolejení. Aby se těmto případům vyhnuly, sledují železnice výkon kol a preventivně je vyměňují. Obchodní problém je zde předpověď selhání kola.| Prediktivní údržba kol vám pomůže s výměnou kol just-in-time |
|_Selhání dveří metra_: Hlavním důvodem zpoždění v provozu metra je selhání dveří vlakových vozů. Obchodní problém je zde předpovědět selhání dveří vlaku.|Včasné povědomí o selhání dveří, nebo počet dní do selhání dveří, pomůže podniku optimalizovat plány údržby dveří vlaku.|

V další části se zabýváme podrobnostmi o tom, jak realizovat výhody PdM popsané výše.

## <a name="data-science-for-predictive-maintenance"></a>Datová věda pro prediktivní údržbu

Tato část obsahuje obecné pokyny pro principy datové vědy a praxe pro PdM. Je určen k pomoci TDM, architekt řešení, nebo developer pochopit předpoklady a proces pro vytváření end-to-end AI aplikací pro PdM. Můžete si přečíst tuto část spolu s přehledem ukázky a proof-of-concept šablony uvedené v [šablonách řešení pro prediktivní údržbu](#solution-templates-for-predictive-maintenance). Tyto zásady a osvědčené postupy pak můžete použít k implementaci řešení PdM v Azure.

> [!NOTE]
> Tato příručka není určena k výuce čtenáře datové vědy. Několik užitečných zdrojů je k dispozici pro další čtení v sekci pro [školení zdrojů pro prediktivní údržbu](#training-resources-for-predictive-maintenance). [Šablony řešení](#solution-templates-for-predictive-maintenance) uvedené v příručce ukazují některé z těchto technik AI pro konkrétní problémy PdM.

## <a name="data-requirements-for-predictive-maintenance"></a>Požadavky na údaje pro prediktivní údržbu

Úspěch každého učení závisí na (a) kvalitě toho, co se učí, a (b) schopnosti žáka. Prediktivní modely se učí vzory z historických dat a předpovídají budoucí výsledky s určitou pravděpodobností na základě těchto pozorovaných vzorců. Prediktivní přesnost modelu závisí na relevanci, dostatečnosti a kvalitě trénovacích a testovacích dat. Nová data, která je "skóre" pomocí tohoto modelu by měl mít stejné funkce a schéma jako data školení/testování. Charakteristiky funkce (typ, hustota, distribuce a tak dále) nových dat by měly odpovídat vlastnostem trénovacích a testovacích datových sad. Tato část se zaměřuje na tyto požadavky na údaje.

### <a name="relevant-data"></a>Relevantní údaje

Za prvé, údaje musí být _relevantní pro tento problém_. Zvažte případ _použití selhání kola_ popsaný výše - trénovací data by měla obsahovat prvky související s operacemi s kolečky. Pokud bylo problémem předpovídat poruchu _trakčního systému_, musí údaje o tréninku zahrnovat všechny různé součásti trakčního systému. První případ se zaměřuje na konkrétní součást, zatímco druhý případ se zaměřuje na selhání většího subsystému. Obecným doporučením je navrhnout predikční systémy o konkrétních součástech spíše než o větších subsystémech, protože ty budou mít více rozptýlených dat. Odborník na doménu (viz [Kvalifikační problémy pro prediktivní údržbu](#qualifying-problems-for-predictive-maintenance)) by měl pomoci při výběru nejrelevantnějších podmnožiny dat pro analýzu. Příslušné zdroje dat jsou podrobněji popsány v [rámci přípravy dat na prediktivní údržbu](#data-preparation-for-predictive-maintenance).

### <a name="sufficient-data"></a>Dostatečné údaje
S ohledem na data historie selhání jsou běžně kladeny dvě otázky: (1) "Kolik událostí selhání je nutné k trénování modelu?" (2) "Kolik záznamů je považováno za "dost"?" Neexistují žádné definitivní odpovědi, ale pouze pravidla. Pro (1), více počet událostí selhání, lepší model. Pro (2) a přesný počet událostí selhání závisí na data a kontext problému, který je vyřešen. Ale na druhou stranu, pokud stroj selže příliš často, pak ho firma nahradí, což sníží výskyty selhání. Zde opět pokyny od odborníka domény je důležité. Existují však metody, jak se vypořádat s otázkou _vzácných událostí_. Jsou popsány v části [Zpracování nevyvážených dat](#handling-imbalanced-data).

### <a name="quality-data"></a>Údaje o kvalitě
Kvalita dat je kritická - každá hodnota atributu prediktoru musí být _přesná_ ve spojení s hodnotou cílové proměnné. Kvalita dat je dobře prozkoumanou oblastí ve statistice a správě dat, a proto není pro tuto příručku v yspadá z působnosti.

> [!NOTE]
> Existuje několik zdrojů a podnikových produktů pro poskytování kvalitních dat. Níže je uveden vzorek odkazů:
> - Dasu, T, Johnson, T., Průzkumné dolování dat a čištění dat, Wiley, 2003.
> - [Průzkumná analýza dat, Wikipedie](https://en.wikipedia.org/wiki/Exploratory_data_analysis)
> - [Hellerstein, J, Kvantitativní čištění dat pro velké databáze](http://db.cs.berkeley.edu/jmh/papers/cleaning-unece.pdf)
> - [de Jonge, E, van der loo, M, Úvod do čištění dat s R](https://cran.r-project.org/doc/contrib/de_Jonge+van_der_Loo-Introduction_to_data_cleaning_with_R.pdf)

## <a name="data-preparation-for-predictive-maintenance"></a>Příprava dat pro prediktivní údržbu

### <a name="data-sources"></a>Zdroje dat

Mezi příslušné zdroje údajů pro prediktivní údržbu patří mimo jiné:
- Historie selhání
- Historie údržby/oprav
- Provozní podmínky stroje
- Metadata zařízení

#### <a name="failure-history"></a>Historie selhání
Události selhání jsou vzácné v aplikacích PdM. Však při vytváření modely předpověď algoritmus potřebuje se učit o normální provozní vzor komponenty, jakož i její vzory selhání. Takže údaje školení by měly obsahovat dostatečný počet příkladů z obou kategorií. Záznamy o údržbě a historie výměny dílů jsou dobrým zdrojem pro nalezení událostí selhání. S pomocí některých znalostí domény mohou být anomálie v trénovacích datech také definovány jako selhání.

#### <a name="maintenancerepair-history"></a>Historie údržby/oprav
Historie údržby majetku obsahuje podrobnosti o vyměněných součástech, provedených opravách atd. Tyto události zaznamenávají vzorce degradace. Absence těchto zásadních informací v údajích o školení může vést k zavádějícím výsledkům modelu. Historie selhání lze také nalézt v historii údržby jako zvláštní kódy chyb nebo data objednávek pro díly. Další zdroje dat, které ovlivňují vzorce selhání, by měly být zkoumány a poskytovány odborníky na domény.

#### <a name="machine-operating-conditions"></a>Provozní podmínky stroje
Důležitým zdrojem dat jsou data založená na senzorech (nebo jiných) streamovaných datech zařízení v provozu. Klíčovým předpokladem v PdM je, že stav počítače degraduje v průběhu času během jeho rutinní provoz. Očekává se, že data budou obsahovat funkce, které mění čas, které zachycují tento vzor stárnutí, a všechny anomálie, které vedou k degradaci. Časový aspekt dat je vyžadován pro algoritmus naučit selhání a non-selhání vzory v průběhu času. Na základě těchto datových bodů algoritmus se naučí předpovědět, kolik dalších jednotek času může počítač pokračovat v práci, než selže.

#### <a name="static-feature-data"></a>Statická data funkcí
Statické funkce jsou metadata o zařízení. Příkladem jsou výrobce zařízení, model, datum výroby, datum zahájení provozu, umístění systému a další technické specifikace.

Příklady relevantních údajů pro [ukázkové případy použití PdM](#sample-pdm-use-cases) jsou uvedeny v tabulce níže:

| Použít případ | Příklady relevantních údajů |
|:---------|---------------------------|
|_Zpoždění a zrušení letu_ | Informace o letové trase ve formě letových nohou a protokolů stránek. Údaje o letovém úseku zahrnují údaje o směrování, jako je datum odletu/příletu, čas, letiště, mezipřistání atd. Protokol stránek obsahuje řadu chybových kódů a kódů údržby zaznamenaných pracovníky údržby terénu.|
|_Porucha letadlových motorů_ | Údaje shromážděné ze senzorů v letadle, které poskytují informace o stavu různých částí. Záznamy o údržbě pomáhají určit, kdy došlo k chybám součástí a kdy byly vyměněny.|
|_Selhání bankomatu_ | Údaje ze senzorů pro každou transakci (uložení hotovosti/šeku) a výdej hotovosti. Informace o měření mezery mezi poznámkami, tloušťkou poznámky, vzdáleností příjezdu poznámky, atributy kontroly atd. Záznamy o údržbě, které poskytují kódy chyb, informace o opravách, kdy byl pokladní dávkovač naposledy znovu vyplněn.|
|_Porucha větrné turbíny_ | Senzory monitorují stavturbíny, jako je teplota, směr větru, generovaný výkon, otáčky generátoru atd. Data jsou shromažďována z několika větrných turbín z větrných farem umístěných v různých regionech. Typicky, každá turbína bude mít více senzorických měření předávání měření v pevném časovém intervalu.|
|_Poruchy jističe_ | Protokoly údržby, které zahrnují nápravná, preventivní a systematická opatření. Provozní data, která zahrnují automatické a ruční příkazy odeslané jističům, například pro akce otevření a zavření. Metadata zařízení, jako je datum výroby, umístění, model atd. Specifikace jističe, jako jsou úrovně napětí, geolokace, okolní podmínky.|
|_Poruchy dveří výtahu_| Metadata výtahu, jako je typ výtahu, datum výroby, frekvence údržby, typ budovy a tak dále. Provozní informace, jako je počet cyklů dveří, průměrná doba zavírání dveří. Historie selhání s příčinami.|
|_Poruchy kol_ | Data ze senzorů, která měří zrychlení kola, případy brzdění, vzdálenost jízdy, rychlost atd. Statické informace o kolech, jako je výrobce, datum výroby. Data selhání odvozená z databáze dílčích objednávek, která sledují data a množství objednávek.|
|_Selhání dveří metra_ | Otevírací a zavírací doba dveří, další provozní údaje, jako je aktuální stav dveří vlaku. Statická data by zahrnovala sloupce identifikátorů, času a hodnoty podmínky.|

### <a name="data-types"></a>Typy dat
Vzhledem k výše uvedeným zdrojům dat jsou dva hlavní datové typy pozorované v doméně PdM:

- _Časová data_: Provozní telemetrie, podmínky stroje, typy pracovních řádů, kódy priorit, které budou mít v době záznamu časová razítka. S každou událostí budou také přidružena časová razítka, dojde k selhání, údržbě/opravě a historii použití.
- _Statická data_: Vlastnosti stroje a prvky obsluhy jsou obecně statické, protože popisují technické specifikace strojů nebo atributů obsluhy. Pokud se tyto funkce mohou v průběhu času měnit, měly by k nim být přidružena také časová razítka.

Prediktor a cílové proměnné by měly být předem zpracovány/transformovány do [číselných, kategorických a dalších datových typů](https://www.statsdirect.com/help/basics/measurement_scales.htm) v závislosti na použitém algoritmu.

### <a name="data-preprocessing"></a>Předběžné zpracování dat
Jako předpoklad _pro funkci inženýrství_, připravit data z různých datových proudů sestavit schéma, ze kterého je snadné vytvářet funkce. Vizualizujte data jako první jako tabulku záznamů. Každý řádek v tabulce představuje instanci školení a sloupce představují funkce _prediktoru_ (nazývané také nezávislé atributy nebo proměnné). Uspořádejte data tak, aby poslední sloupec (sloupce) byl _cíl_ (závislá proměnná). Pro každou instanci školení přiřaďte _popisek_ jako hodnotu tohoto sloupce.

U časových dat rozdělte dobu trvání dat ze senzorů na časové jednotky. Každý záznam by měl patřit časové jednotce pro majetek _a měl by nabízet odlišné informace_. Časové jednotky jsou definovány na základě obchodních potřeb v násobcích sekund, minut, hodin, dnů, měsíců atd. Časová jednotka _nemusí být stejná jako četnost sběru dat_. Pokud je frekvence vysoká, data nemusí vykazovat žádný významný rozdíl mezi jednotlivými jednotkami. Předpokládejme například, že okolní teplota byla shromažďována každých 10 sekund. Použití stejného intervalu pro trénovací data pouze nafoukne počet příkladů bez poskytnutí dalších informací. V tomto případě by lepší strategií bylo použít průměrná data za 10 minut nebo hodinu na základě obchodního odůvodnění.

U statických dat
- _Záznamy o údržbě_: Nezpracovaná data údržby mají identifikátor majetku a časové razítko s informacemi o činnostech údržby, které byly provedeny v daném okamžiku. Transformujte aktivity údržby do _kategorických_ sloupců, kde se každý popisovač kategorie jednoznačně mapuje na konkrétní akci údržby. Schéma pro záznamy údržby by zahrnovalo identifikátor majetku, čas a akci údržby.

- _Záznamy o selhání_: Chyby nebo důvody selhání lze zaznamenat jako specifické kódy chyb nebo události selhání definované konkrétními obchodními podmínkami. V případech, kdy zařízení obsahuje více kódů chyb, by měl odborník na doménu pomoci identifikovat ty, které se relevantní pro cílovou proměnnou. Pomocí zbývajících kódů chyb nebo podmínek vytvořte prvky _prediktoru,_ které korelují s těmito chybami. Schéma pro záznamy selhání by zahrnovalo identifikátor majetku, čas, selhání nebo důvod selhání - pokud je k dispozici.

- _Metadata počítače a operátora_: Sloučit data počítače a operátora do jednoho schématu a přidružit majetek k jeho operátorovi spolu s jejich příslušnými atributy. Schéma pro podmínky počítače by zahrnovalo identifikátor majetku, funkce datového zdroje, identifikátor operátora a funkce operátora.

Mezi další kroky předběžného zpracování dat patří _zpracování chybějících hodnot_ a _normalizace_ hodnot atributů. Podrobná diskuse je nad rámec této příručky - viz další část pro některé užitečné odkazy.

S výše uvedenými předem zpracovanými zdroji dat na místě je konečnou transformací před inženýrstvím prvků spojení výše uvedených tabulek na základě identifikátoru majetku a časového razítka. Výsledná tabulka by měla hodnoty null pro sloupec selhání, pokud je počítač v normálním provozu. Tyto hodnoty null lze imputovat indikátorem pro normální provoz. Tento sloupec selhání slouží k vytvoření _popisků pro prediktivní model_. Další informace naleznete v části [o technikách modelování pro prediktivní údržbu](#modeling-techniques-for-predictive-maintenance).

## <a name="feature-engineering"></a>Návrh funkcí
Funkce inženýrství je prvním krokem před modelováním dat. Jeho role v procesu datové vědy [je popsána zde](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/create-features). _Funkce_ je prediktivní atribut modelu – například teplota, tlak, vibrace a tak dále. Pro PdM, funkce inženýrství zahrnuje abstrahování zdraví stroje přes historická data shromážděná v průběhu značné doby trvání. V tomto smyslu se liší od svých vrstevníků, jako je vzdálené monitorování, detekce anomálií a detekce selhání. 

### <a name="time-windows"></a>Časová okna
Vzdálené monitorování zahrnuje hlášení událostí, ke kterým dochází k _časem_. Modely detekce anomálií vyhodnocují (skóre) příchozí datové proudy dat k označení anomálií jako bodů v čase. Zjišťování selhání klasifikuje selhání, které mají být určité typy, jak se vyskytují body v čase. Naproti tomu PdM zahrnuje předpovídání selhání v _budoucím časovém období_, na základě funkcí, které představují chování stroje v _historickém časovém období_. Pro PdM jsou data funkcí z jednotlivých časových bodů příliš hlučná na to, aby byla prediktivní. Takže data pro každou funkci musí být _vyhlazena_ agregací datových bodů v časových oknech.

### <a name="lag-features"></a>Funkce Lag
Obchodní požadavky definují, jak daleko musí model předpovědět do budoucna. Na druhé straně toto trvání pomáhá definovat ', jak daleko zpět model musí vypadat' aby tyto předpovědi. Toto "ohlédnutí" období se nazývá _zpoždění_, a funkce inženýrství v průběhu tohoto období zpoždění se nazývají _funkce zpoždění_. Tato část popisuje funkce zpoždění, které lze vytvořit ze zdrojů dat s časovými razítky, a vytváření funkcí ze statických zdrojů dat. Funkce lagu jsou obvykle _číselné_ povahy.

> [!IMPORTANT]
> Velikost okna je určena experimentem a měla by být dokončena pomocí odborníka na doménu. Stejné upozornění platí pro výběr a definici funkce zpoždění, jejich agregace a typ oken.

#### <a name="rolling-aggregates"></a>Průběžné agregáty
Pro každý záznam datového zdroje je jako počet jednotek výpočtu agregací vybráno postupné okno o velikosti "W". Funkce lagu jsou pak vypočítány pomocí období W _před datem_ tohoto záznamu. Na obrázku 1 jsou modré čáry zobrazeny hodnoty snímačů zaznamenané pro aktivum pro každou jednotku času. Označují klouzavý průměr hodnot prvků v okně o velikosti W=3. Klouzavý průměr se vypočítá ve všech záznamech s časovými razítky v rozsahu t<sub>1</sub> (oranžově) až t<sub>2</sub> (zeleně). Hodnota pro W je obvykle v minutách nebo hodinách v závislosti na povaze dat. Ale pro určité problémy, výběr velké W (řekněme 12 měsíců) může poskytnout celou historii aktiva až do doby záznamu.

![Obrázek 1: Postupné agregační funkce](./media/predictive-maintenance-playbook/rolling-aggregate-features.png)

Obrázek 1: Postupné agregační funkce

Příklady klouzavých agregací v časovém okně jsou míry počet, průměr, CUMESUM (kumulativní součet), min/max hodnoty. Kromě toho se často používají odchylky, směrodatná odchylka a počet odlehlých hodnot nad směrodatné odchylky N. Příklady agregátů, které mohou být použity pro [případy použití](#sample-pdm-use-cases) v této příručce, jsou uvedeny níže. 
- _Zpoždění letu_: počet chybových kódů za poslední den/týden.
- _Porucha součásti motoru letadla:_ valivé prostředky, směrodatná odchylka a součet za poslední den, týden atd. Tato metrika by měla být určena společně s odborníkem na obchodní doménu.
- _Poruchy ATM:_ valivé prostředky, střední, rozsah, směrodatné odchylky, počet odlehlých hodnot nad tři směrodatné odchylky, horní a dolní CUMESUM.
- _Selhání dveří metra:_ Počet událostí za poslední den, týden, dva týdny atd.
- _Poruchy jističe_: Porucha se počítá za minulý týden, rok, tři roky atd.

Další užitečnou technikou v PdM je zachytit změny trendu, špičky a změny úrovně pomocí algoritmů, které detekují anomálie v datech.

#### <a name="tumbling-aggregates"></a>Omílání agregáty
Pro každý označený záznam datového zdroje je definováno okno o velikosti _W-<sub>k,</sub> _ kde _k_ je počet oken velikosti _W_. Agregáty jsou pak vytvořeny přes _k_ _omílání oken_ _W-k, W-<sub>(k-1)</sub>, ..., W-<sub>2</sub>, W-<sub>1</sub> _ pro období před časovým razítkem záznamu. _k_ může být malý počet zachytit krátkodobé účinky, nebo velký počet zachytit dlouhodobé degradační vzory. (viz obrázek 2).

![Obrázek 2. Omílání agregační funkce](./media/predictive-maintenance-playbook/tumbling-aggregate-features.png)

Obrázek 2. Omílání agregační funkce

Například funkce zpoždění pro případ použití větrných turbín mohou být vytvořeny s W = 1 a k = 3. Znamenají zpoždění za každý z posledních tří měsíců pomocí horní a dolní odlehlé hodnoty.

### <a name="static-features"></a>Statické prvky

Technické specifikace zařízení, jako je datum výroby, číslo modelu, umístění, jsou některé příklady statických prvků. Jsou považovány za _kategorické_ proměnné pro modelování. Některé příklady pro případ použití jističe jsou napětí, proud, kapacita napájení, typ transformátoru a zdroj napájení. Pro poruchy kol je příkladem typ kol pneumatik (slitina vs ocel).

Dosavadní úsilí o přípravu dat by mělo vést k tomu, že data budou uspořádána, jak je uvedeno níže. Trénovací, testovací a ověřovací data by měla mít toto logické schéma (tento příklad ukazuje čas v jednotkách dnů).

| ID majetku | Time | \<> sloupce funkcí | Popisek |
| ---- | ---- | --- | --- |
| A123 |1. den | . . . | . |
| A123 |2. den | . . . | . |
| ...  |...   | . . . | . |
| B234 |1. den | . . . | . |
| B234 |2. den | . . . | . |
| ...  |...   | . . . | . |

Posledním krokem v návrhu funkce je **označení** cílové proměnné. Tento proces je závislý na technice modelování. Technika modelování zase závisí na obchodním problému a povaze dostupných dat. Označení je popsáno v další části.

> [!IMPORTANT]
> Příprava dat a technické znalosti jsou stejně důležité jako techniky modelování pro dosažení úspěšných řešení PdM. Odborník na doménu a odborník by měli investovat značný čas do dosažení správných funkcí a dat pro model. Malý vzorek z mnoha knih o funkci inženýrství jsou uvedeny níže:
> - Pyle, D. Příprava dat pro dolování dat (řada Morgan Kaufmann v systémech pro správu dat), 1999
> - Zheng, A., Casari, A. Funkce inženýrství pro strojové učení: Principy a techniky pro datové vědce, O'Reilly, 2018.
> - Dong, G. Liu, H. (Redakce), Funkce inženýrství pro strojové učení a analýzu dat (Chapman & Hall / CRC Data Mining and Knowledge Discovery Series), CRC Press, 2018.

## <a name="modeling-techniques-for-predictive-maintenance"></a>Modelovací techniky pro prediktivní údržbu

Tato část pojednává o hlavních modelovacích technikách pro problémy PdM spolu s jejich specifickými metodami konstrukce štítků. Všimněte si, že jeden modelování technika může být použita v různých odvětvích. Modelování technika je spárována s problémem datové vědy, spíše než kontext dat po ruce.

> [!IMPORTANT]
> Volba štítků pro případy selhání a strategie označování  
> by měla být stanovena po konzultaci s odborníkem na doménu.

### <a name="binary-classification"></a>Binární klasifikace
Binární klasifikace se používá k _předvídání pravděpodobnosti, že část zařízení selže v budoucím časovém období_ - _nazývaném budoucí horizontové období X_. X je určen obchodním problémem a daty po ruce, po konzultaci s odborníkem na doménu. Můžete například:
- _minimální doba realizace_ potřebná k výměně součástí, nasazení prostředků údržby, provádění údržby, aby se zabránilo problému, ke kterému pravděpodobně dojde v tomto období.
- _minimální počet událostí,_ ke kterým může dojít před výskytem problému.

V této technice jsou identifikovány dva typy příkladů školení. Kladný příklad, _který označuje selhání_s popiskem = 1. Negativní příklad, který označuje normální operace s popiskem = 0. Cílová proměnná a tedy hodnoty popisků jsou _kategorické_. Model by měl identifikovat každý nový příklad jako pravděpodobné selhání nebo pracovat normálně v průběhu příštích jednotek X času.

#### <a name="label-construction-for-binary-classification"></a>Konstrukce štítků pro binární klasifikaci
Otázka zní: "Jaká je pravděpodobnost, že aktivum selže v příštích X jednotkách času?" Chcete-li odpovědět na tuto otázku, popisek X záznamy před selháním majetku jako "o selhání" (popisek = 1), a popisek všechny ostatní záznamy jako "normální" (popisek = 0). (viz obrázek 3).

![Obrázek 3: Označení pro binární klasifikaci](./media/predictive-maintenance-playbook/labelling-for-binary-classification.png)

Obrázek 3: Označení pro binární klasifikaci

Příklady strategie označování pro některé případy použití jsou uvedeny níže.
- _Zpoždění letu_: X může být vybrán jako jeden den, předpovědět zpoždění v příštích 24 hodinách. Pak jsou všechny lety, které jsou do 24 hodin před poruchami, označeny jako 1.
- _Selhání výdeje hotovosti v bankomatu_: Cílem může být určení pravděpodobnosti selhání transakce v následující hodině. V takovém případě jsou všechny transakce, ke kterým došlo během poslední hodiny selhání, označeny jako 1. Chcete-li předpovědět pravděpodobnost selhání v průběhu dalších n bankovek, které byly vydány, jsou všechny poznámky vydané v posledních N bankovek selhání označeny jako 1.
- _Selhání jističe_: Cílem může být předvídání dalšího selhání příkazu jističe. V takovém případě x je vybrán jako jeden budoucí příkaz.
- _Poruchy dveří vlaku_: X lze zvolit jako dva dny.
- _Poruchy větrných turbín_: X lze zvolit jako dva měsíce.

### <a name="regression-for-predictive-maintenance"></a>Regrese pro prediktivní údržbu
Regresní modely se používají k _výpočtu zbývající životnosti (RUL) aktiva_. RUL je definována jako doba, po kterou je aktivum v provozu, než dojde k dalšímu selhání. Každý příklad školení je záznam, který patří do časové jednotky _nY_ pro datový zdroj, kde _n_ je násobek. Model by měl vypočítat rul každého nového příkladu jako _průběžné číslo_. Toto číslo označuje dobu zbývající před selháním.

#### <a name="label-construction-for-regression"></a>Konstrukce štítků pro regresi
Otázka zní: "Jaká je zbývající životnost (RUL) zařízení?" Pro každý záznam před selháním vypočítejte popisek jako počet jednotek času zbývajícího před další poruchou. V této metodě popisky jsou spojité proměnné. (Viz obrázek 4)

![Obrázek 4. Označení pro regresi](./media/predictive-maintenance-playbook/labelling-for-regression.png)

Obrázek 4. Označení pro regresi

Pro regresi se popisování provádí s odkazem na bod selhání. Jeho výpočet není možný, aniž by věděl, jak dlouho se aktivum přežilo před selháním. Takže na rozdíl od binární klasifikace, prostředky bez selhání v datech nelze použít pro modelování. Tento problém je nejlépe řešit jinou statistickou technikou nazvanou [Analýza přežití](https://en.wikipedia.org/wiki/Survival_analysis). Ale potenciální komplikace mohou nastat při použití této techniky na případy použití PdM, které zahrnují časově proměnlivá data s častými intervaly. Další informace o analýze přežití naleznete v [tomto jednostránkovém zařízení](https://www.cscu.cornell.edu/news/news.php/stnews78.pdf).

### <a name="multi-class-classification-for-predictive-maintenance"></a>Klasifikace více tříd pro prediktivní údržbu
Vícetřídní klasifikační techniky lze použít v řešeních PdM pro dva scénáře:
- Předpovědět _dva budoucí výsledky_: Prvním výsledkem je rozsah času do _selhání_ pro aktivum. Majetek je přiřazen k jednomu z více možných časových období. Druhým výsledkem je pravděpodobnost selhání v budoucím období v důsledku _jedné z více hlavních příčin_. Tato předpověď umožňuje údržbě posádky sledovat příznaky a plánovat plány údržby.
- Předpovědět _nejpravděpodobnější příčinu_ daného selhání. Tento výsledek doporučuje správnou sadu akcí údržby k opravě selhání. Seřazený seznam hlavních příčin a doporučených oprav může technikům pomoci určit prioritu jejich oprav po selhání.

#### <a name="label-construction-for-multi-class-classification"></a>Konstrukce štítků pro klasifikaci více tříd
Otázka zní: "Jaká je pravděpodobnost, že aktivum selže v příštích _jednotkách nZ,_ kde _n_ je počet období?" Chcete-li odpovědět na tuto otázku, label nZ záznamy před selháním majetku pomocí kbelíky času (3Z, 2Z, Z). Označte všechny ostatní záznamy jako "normální" (popisek = 0). V této metodě obsahuje cílová proměnná _kategorické_ hodnoty. (Viz obrázek 5).

![Obrázek 5. Popisky předpovědi doby selhání pro klasifikaci více tříd](./media/predictive-maintenance-playbook/labelling-for-multiclass-classification-for-failure-time-prediction.png)

Obrázek 5. Označení pro klasifikaci více tříd pro předpověď doby selhání

Otázka zní: "Jaká je pravděpodobnost, že aktivum selže v příštích X jednotek času kvůli hlavní příčině / problému _P<sub>i?"</sub>_ kde _i_ je počet možných příčin. Chcete-li odpovědět na tuto otázku, popisek X záznamy před selháním aktiva jako "o selhání z důvodu hlavní příčiny _P<sub>i</sub>_" (label = _P<sub>i).</sub>_ Označte všechny ostatní záznamy jako "normální" (popisek = 0). V této metodě jsou také popisky kategorické (viz obrázek 6).

![Obrázek 6. Popisky předpovědi hlavní příčiny pro klasifikaci více tříd](./media/predictive-maintenance-playbook/labelling-for-multiclass-classification-for-root-cause-prediction.png)

Obrázek 6. Označení pro klasifikaci více tříd pro předpověď hlavní příčiny

Model přiřadí pravděpodobnost selhání z důvodu každého _P<sub>i,</sub> _ stejně jako pravděpodobnost žádné poruchy. Tyto pravděpodobnosti lze seřadit podle velikosti, aby predikce problémů, které jsou s největší pravděpodobností dojít v budoucnu.

Otázka zní: "Jaké údržbové akce doporučujete po selhání?" Chcete-li odpovědět na tuto otázku, označování _nevyžaduje budoucí horizont, který má být vybrán_, protože model není předpovídá selhání v budoucnu. Je to jen předpovídání nejpravděpodobnější _příčiny, jakmile selhání již došlo_.

## <a name="training-validation-and-testing-methods-for-predictive-maintenance"></a>Metody školení, validace a testování prediktivní údržby
[Proces vědecké ho procesu týmových dat](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview) poskytuje úplné pokrytí cyklu test-test modelu. Tato část popisuje aspekty jedinečné pdm.

### <a name="cross-validation"></a>Křížové ověření
Cílem [křížového ověření](https://en.wikipedia.org/wiki/Cross-validation_(statistics)) je definovat datovou sadu pro "testování" modelu ve fázi trénování. Tato sada dat se nazývá _ověřovací sada_. Tato technika pomáhá omezit problémy, jako _je nadměrné vybavení_ a poskytuje přehled o tom, jak bude model generalizovat na nezávislou sadu dat. To znamená neznámou datovou sadu, která by mohla být z reálného problému. Školení a testování rutiny pro PdM musí brát v úvahu čas různé aspekty, aby lépe zobecnit na neviditelné budoucí data.

Mnoho algoritmů strojového učení závisí na počtu hyperparametrů, které mohou výrazně změnit výkon modelu. Optimální hodnoty těchto hyperparameters nejsou vypočítány automaticky při trénování modelu. Měly by být specifikovány datovým vědcem. Existuje několik způsobů, jak najít dobré hodnoty hyperparametrů.

Nejběžnější z nich je _k-fold křížové ověření,_ které rozděluje příklady náhodně do _k_ záhybů. Pro každou sadu hodnot hyperparameters spusťte algoritmus učení _k_ časy. Při každé iteraci použijte příklady v aktuálním záhybu jako sadu ověření a ostatní příklady jako trénovací sadu. Trénování algoritmu přes příklady školení a vypočítat metriky výkonu přes příklady ověření. Na konci této smyčky vypočítat průměr metriky výkonu _k._ Pro každou sadu hodnot hyperparametrů zvolte ty, které mají nejlepší průměrný výkon. Úkol emitování hyperparametrů je často experimentální povahy.

V pdm problémy data jsou zaznamenány jako časová řada událostí, které pocházejí z několika zdrojů dat. Tyto záznamy mohou být objednány podle doby označení. Proto pokud je datová sada _rozdělena náhodně_ do trénovací a ověřovací sady, _některé příklady školení může být později v čase než některé příklady ověření_. Budoucí výkon hodnot hyperparametrů bude odhadnut na základě některých dat, která byla doručena _před_ trénovaným modelem. Tyto odhady mohou být příliš optimistické, zejména pokud časové řady nejsou stacionární a v průběhu času se vyvíjejí. V důsledku toho mohou být vybrané hodnoty hyperparametrů neoptimální.

Doporučeným způsobem je rozdělit příklady do nastavení školení a ověřování nastaveným způsobem _závislým na čase,_ kde jsou všechny příklady ověření později v čase než všechny příklady školení. Pro každou sadu hodnot hyperparameter, trénování algoritmu přes trénovací datové sady. Změřte výkon modelu ve stejné sadě ověření. Zvolte hodnoty hyperparametrů, které vykazují nejlepší výkon. Hodnoty hyperparametrů zvolené rozdělením train/validation vedou k lepšímu výkonu budoucího modelu než u hodnot náhodně vybraných křížovým ověřením.

Konečný model lze generovat trénování algoritmu učení přes celá trénovací data pomocí nejlepší hodnoty hyperparameter.

### <a name="testing-for-model-performance"></a>Testování výkonu modelu
Po vytvoření modelu je vyžadován odhad jeho budoucího výkonu na nová data. Dobrý odhad je metrika výkonu hodnot hyperparametrů vypočítaných v průběhu sady ověření nebo metriky průměrného výkonu vypočítané z křížového ověření. Tyto odhady jsou často příliš optimistické. Firma může mít často některé další pokyny o tom, jak by chtěli otestovat model.

Doporučeným způsobem pro PdM je rozdělit příklady do trénovacích, ověřovacích a testovacích datových sad způsobem závislým na _čase._ Všechny příklady testu by měly být později než všechny příklady školení a ověření. Po rozdělení vygenerujte model a změřte jeho výkon, jak je popsáno výše.

Když časové řady jsou stacionární a snadno předvídatelné, náhodné i časově závislé přístupy generovat podobné odhady budoucí ho výkonu. Když jsou však časové řady nestacionární a/nebo těžko předvídatelné, časově závislý přístup vygeneruje realističtější odhady budoucí výkonnosti.

### <a name="time-dependent-split"></a>Rozdělení závislé na čase
Tato část popisuje osvědčené postupy pro implementaci rozdělení závislé ho času. Časově závislé obousměrné rozdělení mezi trénovací a testovací sady je popsáno níže.

Předpokládejme proud událostí s časovým razítkem, jako jsou měření z různých senzorů. Definujte funkce a popisky školení a testovací chod v časových rámcích, které obsahují více událostí. Například pro binární klasifikaci, vytvořit funkce založené na minulých událostech a vytvořit popisky založené na budoucích událostech v rámci jednotek "X" času v budoucnu (viz části o [funkce inženýrství](#feature-engineering) a modelování techniky). Časový rámec popisování příkladu tedy přichází později než časový rámec jeho funkcí.

Pro rozdělení závislé na čase vyberte _tréninkovou dobu přerušení T<sub>c,</sub> _ při které chcete trénovat model, s hyperparametry naladěnými pomocí historických dat až do T<sub>c</sub>. Chcete-li zabránit úniku budoucích štítků, které jsou mimo T<sub>c</sub> do trénovacích dat, zvolte nejnovější čas, abyste označili příklady školení jako jednotky X před T<sub>c</sub>. V příkladu znázorněném na obrázku 7 představuje každý čtverec záznam v sadě dat, kde jsou vypočteny prvky a popisky, jak je popsáno výše. Obrázek znázorňuje záznamy, které by měly jít do školení a testování sady pro X = 2 a W = 3:

![Obrázek 7. Rozdělení závislé na čase pro binární klasifikaci](./media/predictive-maintenance-playbook/time-dependent-split-for-binary-classification.png)

Obrázek 7. Rozdělení závislé na čase pro binární klasifikaci

Zelené čtverce představují záznamy patřící k časovým jednotkám, které lze použít pro školení. Každý příklad školení je generován a vezmeme-li v úvahu poslední tři období pro generování prvků a dvě budoucí období pro označování před T<sub>c</sub>. Pokud je kterákoli část dvou budoucích období nad rámec T<sub>c</sub>, vylučte tento příklad ze sady dat školení, protože se nepředpokládá žádná viditelnost nad T<sub>c</sub>.

Černé čtverečky představují záznamy konečné popisky datové sady, které by neměly být použity v sadě dat školení, vzhledem k výše uvedené omezení. Tyto záznamy také nebudou použity v testovacích datech, protože jsou před T<sub>c</sub>. Kromě toho jejich časové rámce pro označování částečně závisí na časovém rámci tréninku, což není ideální. Údaje o školení a zkouškách by měly mít samostatné časové rámce pro označování, aby se zabránilo úniku informací o popiscích.

Technika, o které se dosud diskutovalo, umožňuje překrývání mezi tréninkem a testovacími příklady, které mají časová razítka v blízkosti T<sub>c</sub>. Řešením pro dosažení většího oddělení je vyloučit příklady, které jsou v rámci W časových jednotek T<sub>c</sub> ze zkušební sady. Takové agresivní rozdělení však závisí na dostatečné dostupnosti dat.

Regresní modely používané pro předpovídání RUL jsou závažněji ovlivněny problémem úniku. Použití metody náhodného rozdělení vede k extrémnímu nadměrnému přizpůsobení. Pro regresní problémy by rozdělení mělo být takové, aby záznamy patřící do prostředků s poruchami před T<sub>c</sub> přejít do sady školení. Záznamy o datových prostředcích, které mají chyby po přerušení přejít do testovací sady.

Dalším osvědčeným postupem pro rozdělení dat pro školení a testování je použití rozdělení podle ID prostředku. Rozdělení by mělo být takové, aby žádný z prostředků použitých v trénovací sadě se nepoužíval při testování výkonu modelu. Pomocí tohoto přístupu má model větší šanci poskytnout realističtější výsledky s novými aktivy.

### <a name="handling-imbalanced-data"></a>Zpracování nevyvážených dat
V klasifikaci problémy, pokud existuje více příkladů jedné třídy než ostatní, soubor dat se říká, že _je nevyvážený_. V ideálním případě dostatek zástupců každé třídy v trénovací data jsou upřednostňovány povolit rozlišení mezi různými třídami. Pokud jedna třída je menší než 10 % dat, data se považuje za nevyvážená. Nedostatečně zastoupená třída se nazývá _menšinová třída_.

Mnoho problémů PdM čelí takové nevyvážené datové sady, kde jedna třída je vážně nedostatečně zastoupeny ve srovnání s jinou třídou nebo třídy. V některých situacích může menšinová třída představovat pouze 0,001 % z celkového počtu datových bodů. Třídní nerovnováha není jedinečná pro PdM. Jiné domény, kde selhání a anomálie jsou vzácné výskyty čelí podobnému problému, například detekce podvodů a narušení sítě. Tyto chyby tvoří příklady menšinové třídy.

S nerovnováhou třídy v datech je ohrožen výkon většiny standardních algoritmů učení, protože jejich cílem je minimalizovat celkovou míru chyb. U datové sady s 99 % negativními a 1 % pozitivními příklady lze prokázat, že model má 99% přesnost označením všech instancí jako negativní. Model však špatně klasifikuje všechny pozitivní příklady; takže i když je jeho přesnost vysoká, algoritmus není užitečný. V důsledku toho jsou konvenční hodnotící metriky, jako je _celková přesnost míry chyb,_ pro nevyvážené učení nedostatečné. Při řešení nevyvážených datových sad se pro vyhodnocení modelu používají další metriky:
- Přesnost
- Úplnost
- Skóre F1
- Roc očištěný o náklady (provozní charakteristiky přijímače)

Další informace o těchto metrikách naleznete v [tématu vyhodnocení modelu](#model-evaluation).

Existují však některé metody, které pomáhají napravit problém nerovnováhy třídy. Dva hlavní z nich jsou _vzorkovací techniky_ a _náklady citlivé učení_.

#### <a name="sampling-methods"></a>Metody odběru vzorků
Nevyvážené učení zahrnuje použití metod vzorkování k úpravě trénovací datové sady na vyváženou sadu dat. Metody odběru vzorků se na zkušební množinu nepoužijí. Ačkoli existuje několik technik odběru vzorků, většina přímočarých jsou _náhodné převzorkování_ a _pod odběrem vzorků_.

_Náhodné převzorkování_ zahrnuje výběr náhodného vzorku z menšinové třídy, replikaci těchto příkladů a jejich přidání do trénovací datové sady. V důsledku toho se zvýší počet příkladů v menšinové třídě a nakonec vyvažte počet příkladů různých tříd. Nevýhodou převzorkování je, že více instancí určitých příkladů může způsobit, že třídění bude příliš specifické, což vede k přemýmontáži. Model může vykazovat vysokou přesnost trénování, ale jeho výkon na neviditelné testovací data může být neoptimální.

Naopak _náhodné pod vzorkování_ je výběr náhodného vzorku z většinové třídy a odstranění těchto příkladů ze sady dat školení. Odebrání příklady z většinové třídy však může způsobit třídění chybět důležité koncepty týkající se většinové třídy. _Hybridní odběr vzorků,_ u nichž je menšinová třída převzorkována a většinová třída je současně podvzorkována, je jiný životaschopný přístup.

Existuje mnoho sofistikovaných technik vzorkování. Zvolená technika závisí na vlastnostech dat a výsledcích iterativních experimentů datového vědce.

#### <a name="cost-sensitive-learning"></a>Nákladově citlivé učení
V PdM jsou selhání, která tvoří menšinovou třídu, zajímavější než běžné příklady. Takže důraz je kladen především na výkon algoritmu na selhání. Nesprávné předpovídání kladné třídy jako záporné třídy může stát více než naopak. Tato situace se běžně označuje jako nerovné ztráty nebo asymetrické náklady chybné klasifikace prvků do různých tříd. Ideální třídění by měl poskytovat vysokou přesnost predikce přes menšinové třídy, bez kompromisů na přesnost pro většinovou třídu.

Existuje několik způsobů, jak dosáhnout této rovnováhy. Chcete-li zmírnit problém nerovné ztráty, přiřadit vysoké náklady na mis-klasifikace menšinové třídy a pokusit se minimalizovat celkové náklady. Algoritmy jako _SVMs (Support Vector Machines)_ přijmout tuto metodu ze své podstaty tím, že náklady na pozitivní a negativní příklady, které mají být zadány během školení. Podobně, posílení metody, jako je _například posílené rozhodnutí stromy_ obvykle vykazují dobrý výkon s nevyváženými daty.

## <a name="model-evaluation"></a>Vyhodnocení modelu
Mis-klasifikace je významný problém pro scénáře PdM, kde náklady na falešné poplachy pro podnikání je vysoká. Například rozhodnutí o přistání letadla na základě nesprávné předpovědi poruchy motoru může narušit plány a cestovní plány. Přepneme stroj do ústraní z montážní linky a může vést ke ztrátě výnosů. Hodnocení modelu se správnými metrikami výkonu oproti novým testovacím datům je tedy velmi důležité.

Typické metriky výkonu používané k vyhodnocení modelů PdM jsou popsány níže:

- [Přesnost](https://en.wikipedia.org/wiki/Accuracy_and_precision) je nejoblíbenější metrika používaná k popisu výkonu třídění. Ale přesnost je citlivá na rozdělení dat a je neefektivní opatření pro scénáře s nevyváženou datové sady. Místo toho se používají jiné metriky. Nástroje, jako [je matice záměny](https://en.wikipedia.org/wiki/Confusion_matrix) se používají k výpočtu a důvod o přesnosti modelu.
- [Přesnost](https://en.wikipedia.org/wiki/Precision_and_recall) modelů PdM se vztahuje k rychlosti falešných poplachů. Nižší přesnost modelu obecně odpovídá vyšší míře falešných poplachů.
- [Rychlost odvolání](https://en.wikipedia.org/wiki/Precision_and_recall) označuje, kolik chyb v testovací sadě bylo modelem správně identifikováno. Vyšší míra odvolání znamená, že model je úspěšný při identifikaci skutečných selhání.
- [F1 skóre](https://en.wikipedia.org/wiki/F1_score) je harmonický průměr přesnosti a odvolání, s jeho hodnota v rozmezí od 0 (nejhorší) do 1 (nejlepší).

Pro binární klasifikaci
- [Provozní křivky přijímače (ROC)](https://en.wikipedia.org/wiki/Receiver_operating_characteristic) je také populární metrika. V křivkách ROC je výkon modelu interpretován na základě jednoho pevného provozního bodu na ROC.
- Ale pro problémy PdM, _decile tabulky_ a _výtah grafy_ jsou více informativní. Zaměřují se pouze na pozitivní třídy (selhání) a poskytují složitější obraz výkonu algoritmu než křivky ROC.
  - _Decile tabulky_ jsou vytvořeny pomocí testovacích příkladů v sestupném pořadí pravděpodobností selhání. Objednané vzorky jsou pak seskupeny do decily (10% vzorků s nejvyšší pravděpodobností, pak 20%, 30% a tak dále). Poměr (true pozitivní míra) / (náhodný směrný plán) pro každý decile pomáhá odhadnout výkon algoritmu při každém decilu. Náhodný směrný plán přebírá hodnoty 0,1, 0,2 a tak dále.
  - [Grafy výtahů](http://www2.cs.uregina.ca/~dbd/cs831/notes/lift_chart/lift_chart.html) vykreslují decilovou skutečnou kladnou míru versus náhodnou skutečnou kladnou míru pro všechny decily. První decily jsou obvykle středem výsledků, protože vykazují největší zisky. První decily lze také považovat za reprezentativní pro "ohrožené", při použití pro PdM.

## <a name="model-operationalization-for-predictive-maintenance"></a>Modeloperationalization pro prediktivní údržbu

Výhoda cvičení datové vědy je realizována pouze v případě, že je trénovaný model zprovozněn. To znamená, že model musí být nasazendo obchodních systémů, aby se předpovědi založené na nových, dříve neviditelné, data.  Nová data musí přesně odpovídat _podpisu modelu_ trénovaného modelu dvěma způsoby:
- všechny funkce musí být k dispozici v každé logické instanci (řekněme řádek v tabulce) nových dat.
- nová data musí být předem zpracována a každá z funkcí musí být navržena přesně stejným způsobem jako trénovací data.

Výše uvedený proces je uveden v mnoha ohledech v akademické a průmyslové literatuře. Ale všechny následující výroky znamenají totéž:
- _Skóre nových dat_ pomocí modelu
- _Použití modelu u_ nových dat
- _Zprovoznit_ model
- _Nasazení_ modelu
- _Spuštění modelu s novými_ daty

Jak již bylo uvedeno dříve, model operationalization pro PdM se liší od jeho vrstevníků. Scénáře zahrnující detekci anomálií a detekci selhání obvykle implementují _online bodování_ (nazývané také _vyhodnocování v reálném čase)._ Zde model _skóre_ každý příchozí záznam a vrátí předpověď. Pro detekci anomálií předpověď je indikace, že došlo k anomálii (Příklad: Jednotřídní SVM). Pro detekci selhání by se jedná o typ nebo třídu selhání.

Naproti tomu PdM zahrnuje _dávkové vyhodnocování_. Aby byly funkce v nových datech přizpůsobeny podpisu modelu, musí být navrženy stejným způsobem jako trénovací data. Pro velké datové sady, které jsou typické pro nová data, jsou funkce agregovány v průběhu času a hodnoceny v dávce. Dávkové vyhodnocování se obvykle provádí v distribuovaných systémech, jako [je Spark](https://spark.apache.org/) nebo [Azure Batch](https://docs.microsoft.com/azure/batch/batch-api-basics). Existuje několik alternativ - oba suboptimální:
- Moduly datových proudů podporují agregaci přes okna v paměti. Takže by to mohlo být argumentoval, že podporují on-line bodování. Ale tyto systémy jsou vhodné pro hustá data v úzkých oknech času, nebo řídké prvky přes širší okna. Nemusí škálovat dobře pro hustá data v širších časových oknech, jak je vidět ve scénářích PdM.
- Pokud dávkové vyhodnocování není k dispozici, řešením je přizpůsobit online vyhodnocování pro zpracování nových dat v malých dávkách najednou.

## <a name="solution-templates-for-predictive-maintenance"></a>Šablony řešení pro prediktivní údržbu

Poslední část této příručky obsahuje seznam šablon řešení PdM, kurzů a experimentů implementovaných v Azure. Tyto pdm aplikace lze nasadit do předplatného Azure během několika minut v některých případech. Mohou být použity jako ukázky proof-of-concept, karantény zabezpečení pro experimentování s alternativami nebo akcelerátory pro skutečné produkční implementace. Tyto šablony jsou umístěné v [Galerii Azure AI](https://gallery.azure.ai) nebo [Azure GitHub](https://github.com/Azure). Tyto různé ukázky budou vráceny do této šablony řešení v průběhu času.

| # | Nadpis | Popis |
|--:|:------|-------------|
| 2 | [Šablona řešení prediktivní údržby Azure](https://github.com/Azure/AI-PredictiveMaintenance) | Šablona řešení s otevřeným zdrojovým kódem, která demonstruje modelování Azure ML a kompletní infrastrukturu Azure schopnou podporovat scénáře prediktivní údržby v kontextu vzdáleného monitorování IoT. |
| 3 | [Hloubkové učení pro prediktivní údržbu](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance) | Poznámkový blok Azure s ukázkovým řešením využívajícím sítě LSTM (dlouhá krátkodobá paměť) (třída rekurentních neuronových sítí) pro prediktivní údržbu s [příspěvkem blogu v této ukázce](https://azure.microsoft.com/blog/deep-learning-for-predictive-maintenance).|
| 4 | [Průvodce modelováním prediktivní údržby v R](https://gallery.azure.ai/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1) | PdM modelování průvodce se skripty v R.|
| 5 | [Prediktivní údržba Azure pro letectví a kosmonautiku](https://gallery.azure.ai/Solution/Predictive-Maintenance-for-Aerospace-1) | Jedna z prvních šablon řešení PdM založená na Azure ML v1.0 pro údržbu letadel. Tato příručka pochází z tohoto projektu. |
| 6 | [Sada nástrojů Azure AI pro IoT Edge](https://github.com/Azure/ai-toolkit-iot-edge) | AI v IoT Edge pomocí TensorFlow; sada nástrojů balíčky hluboké učení modely v kontejnerech Docker kompatibilní s Azure IoT Edge a vystavit tyto modely jako REST API.
| 7 | [Prediktivní údržba Azure IoT](https://github.com/Azure/azure-iot-predictive-maintenance) | Azure IoT Suite PCS – předkonfigurované řešení. Šablona PdM údržby letadel s IoT Suite. [Jiný dokument](https://docs.microsoft.com/azure/iot-suite/iot-suite-predictive-overview) a [návod](https://docs.microsoft.com/azure/iot-suite/iot-suite-predictive-walkthrough) týkající se stejného projektu. |
| 8 | [Šablona prediktivní údržby pomocí služby SQL Server R Services](https://gallery.azure.ai/Tutorial/Predictive-Maintenance-Template-with-SQL-Server-R-Services-1) | Ukázka scénáře životnosti zbývající hodna na základě služeb R. |
| 9 | [Průvodce modelováním prediktivní údržby](https://gallery.azure.ai/Collection/Predictive-Maintenance-Modelling-Guide-1) | Funkce datové sady údržby letadel navržená pomocí R s [experimenty](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Modelling-Guide-Experiment-1) a [datovými sadami](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Modelling-Guide-Data-Sets-1) a [poznámkovými bloky Azure](https://gallery.azure.ai/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1) a [experimenty](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Step-1-of-3-data-preparation-and-feature-engineering-2) ve službě AzureML v1.0|

## <a name="training-resources-for-predictive-maintenance"></a>Zdroje školení pro prediktivní údržbu

Microsoft Azure nabízí výukové cesty pro základní koncepty za pdm techniky, kromě obsahu a školení o obecných konceptech a praxi ai.

| Školicí zdroj  | Dostupnost |
|:-------------------|--------------|
| [Studijní program pro PdM pomocí stromů a náhodné doménové struktury](https://aischool.microsoft.com/learning-paths/1H5vH5wAYcAy88CoQWQcA8) | Public | 
| [Studijní program pro PdM pomocí hloubkového učení](https://aischool.microsoft.com/learning-paths/FSIXxYkOGcauo0eUO8qAS) | Public |
| [Vývojář umělá já v Azure](https://azure.microsoft.com/training/learning-paths/azure-ai-developer) | Public |
| [Microsoft AI škola](https://aischool.microsoft.com/learning-paths) | Public |
| [Učení u mělžíte k Azure z GitHubu](https://github.com/Azure/connectthedots/blob/master/readme.md) | Public |
| [LinkedIn Learning](https://www.linkedin.com/learning) | Public |
| [Webináře YouTube služby Microsoft AI](https://www.youtube.com/watch?v=NvrH7_KKzoM&t=4s) | Public |
| [Microsoft AI Show](https://channel9.msdn.com/Shows/AI-Show) | Public |
| [LearnAI@MS](https://learnanalytics.microsoft.com) | Partneři |
| [Microsoft Partner Network](https://partner.microsoft.com/training/training-center) | Partneři |

Kromě toho, zdarma MOOCS (masivní otevřené on-line kurzy) na AI jsou nabízeny on-line akademických institucí, jako je Stanford a MIT, a dalšívzdělávací společnosti.
