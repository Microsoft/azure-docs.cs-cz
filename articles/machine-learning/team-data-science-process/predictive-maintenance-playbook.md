---
title: Průvodce Azure AI pro prediktivní údržbu – proces týmu pro vědecké zpracování dat
description: Komplexní popis datové vědy, který využívá řešení prediktivní údržby ve více vertikálních odvětvích.
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
ms.openlocfilehash: 1661f0f6cf024fde48d3706a1f8e47bf65f0d46f
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93321975"
---
# <a name="azure-ai-guide-for-predictive-maintenance-solutions"></a>Průvodce Azure AI pro prediktivní správu řešení

## <a name="summary"></a>Souhrn

Prediktivní Údržba ( **PDM** ) je oblíbená aplikace prediktivní analýzy, která může pomáhat podnikům v několika odvětvích dosahovat vysokého využití prostředků a úspory za provozní náklady. Tato příručka spojuje obchodní a analytické pokyny a osvědčené postupy pro úspěšné vývoj a nasazování řešení PdM s využitím technologie [platformy Microsoft Azure AI](https://azure.microsoft.com/overview/ai-platform) .

V případě starts Tato příručka zavádí konkrétní firemní scénáře a proces, kterým se tyto scénáře opravňují pro PdM. K dispozici jsou také požadavky na data a techniky modelování pro sestavování řešení PdM. Hlavním obsahem příručky je proces vědeckého zpracování dat, včetně kroků pro přípravu dat, vytváření funkcí, vytváření modelů a provoz modelů. Tato příručka obsahuje sadu šablon řešení, které pomáhají zrychlit vývoj aplikací PdM. Tato příručka také ukazuje na užitečné školicí materiály pro odborníka na Další informace o AI za datovou vědy. 

### <a name="data-science-guide-overview-and-target-audience"></a>Přehled příručky pro datové vědy a cílová skupina
První polovina této příručky popisuje běžné obchodní problémy, výhody implementace PdM k řešení těchto problémů a uvádí některé běžné případy použití. K tomuto obsahu budou mít výhody tvůrci obchodních rozhodnutí (BDMs). Druhá polovina vysvětluje data vědy na základě PdM a poskytuje seznam řešení PdM vytvořených pomocí zásad uvedených v této příručce. Poskytuje také cesty pro učení a ukazatele na školicí materiály. Technickým rozhodnutím pro pracovníky (TDMs) se tento obsah hodí.

| Začněte s... | Pokud jste... |
|:---------------|:---------------|
| [Obchodní případ pro prediktivní údržbu](#business-case-for-predictive-maintenance) |pracovník s rozhodovací pravomocí (BDM), který se pokouší snížit prostoje a provozní náklady a zlepšit využití zařízení |
| [Datové vědy pro prediktivní údržbu](#data-science-for-predictive-maintenance) |technický rozhodovací program (TDM), který vyhodnocuje technologie PdM pro pochopení jedinečného zpracování dat a požadavků AI na prediktivní údržbu |
| [Šablony řešení pro prediktivní údržbu](#solution-templates-for-predictive-maintenance)|softwarový architekt nebo nástroj pro vývojáře AI, který hledá rychlou ukázku nebo zkušební účely konceptu |
| [Školicí materiály pro prediktivní údržbu](#training-resources-for-predictive-maintenance) | některé nebo všechny výše uvedené a chtějí se naučit základní koncepty za rámec datových vědy, nástrojů a technik.

### <a name="prerequisite-knowledge"></a>Předpokládané znalosti
Obsah BDM neočekává, že čtenář bude mít žádné předchozí znalosti pro datové vědy. V případě obsahu TDM je užitečné základní znalosti statistik a vědeckého zpracování dat. Doporučujeme, abyste si vydoporučili znalosti služeb Azure data a AI, Python, R, XML a JSON. Techniky AI jsou implementované v balíčcích Python a R. Šablony řešení se implementují pomocí služeb Azure, vývojářských nástrojů a sad SDK.

## <a name="business-case-for-predictive-maintenance"></a>Obchodní případ pro prediktivní údržbu

Firmy vyžadují pro provozování svých návratnosti velkých investic důležité zařízení, která mají provozní efektivitu a využití. Tyto prostředky by mohly být v rozsahu od leteckých motorů, turbínů, výtahů nebo průmyslových chladicích přístrojů – tyto náklady se vybírají až do každodenních zařízení, jako jsou kopírky, kavárny nebo vodní chladič.
- Ve výchozím nastavení většina firem spoléhá na _opravnou údržbu_ , kde se součásti nahrazují jako a při selhání. Opravná údržba zajišťuje, že se části zcela používají (takže neodstraňuje životní cyklus součástí), ale náklady na obchod za výpadky, práci a neplánované požadavky na údržbu (mimo špičky nebo nepohodlné umístění).
- Na další úrovni podniky postupuje při  _preventivní údržbě_ , kde určují vhodný životnost pro součást a udržují nebo nahrazují před selháním. Preventivní údržba zabraňuje neplánovaným a závažným chybám. Ale vysoké náklady na plánované výpadky, využití komponenty během své užitečné životnosti, a stále ještě zůstává práce.
- Cílem _prediktivní údržby_ je optimalizovat rovnováhu mezi opravnou a preventivní údržbou tím, že _v čase_ zapnete náhradní součásti. Tento přístup nahrazuje tyto součásti pouze v případě, že se blíží selhání. Díky rozšíření životností komponent (v porovnání s preventivní údržbou) a omezením neplánovaných nákladů na údržbu a práce (v rámci opravné údržby) můžou podniky získat úspory nákladů a konkurenční výhody.

## <a name="business-problems-in-pdm"></a>Obchodní problémy v PdM
Podniky čelí vysokému provoznímu riziku kvůli neočekávaným chybám a mají omezený přehled o hlavní příčině problémů ve složitých systémech. Některé z klíčových obchodních otázek:

- Detekuje anomálie v zařízeních nebo výkonu systému nebo funkcích.
- Předpovědět, jestli se v blízké budoucnosti může vycházet selhání prostředku
- Odhadnout zbývající životnost assetu.
- Identifikujte hlavní příčiny selhání prostředku.
- Určete, jaké akce údržby je potřeba provést, když se aktivuje v prostředku.

Typickými příkazy cílů z PdM jsou:

- Snižte provozní riziko důležitých zařízení.
- Zvyšte počet vrácených prostředků tím, že předpovídáte chyby, než k nim dojde.
- Řízení nákladů na údržbu tím, že povolíte operace údržby za běhu.
- Snižte Attrition zákazníka, Vylepšete obrázek značky a ztratíte prodej.
- Snižte náklady na inventář snížením úrovně inventáře tím, že předpovídáte bod přiobjednání.
- Objevte vzory připojené k různým problémům s údržbou.
- Poskytněte klíčové ukazatele výkonu (klíčové ukazatele výkonu), jako jsou například skóre stavu pro podmínky assetu.
- Odhad zbývající životnosti prostředků
- Doporučte si včasné aktivity údržby.
- Umožňuje v inventáři přesně vyhodnotit data objednávek k nahrazení částí.

Tyto příkazy cílů jsou počátečními body pro:

- _vědečtí data_ , která umožňují analyzovat a řešit konkrétní prediktivní problémy.
- _cloudové architekty a vývojáři, kteří_ dohromady dovedou k koncovému řešení.

## <a name="qualifying-problems-for-predictive-maintenance"></a>Kvalifikační problémy pro prediktivní údržbu
Je důležité zdůraznit, že ne všechny případy použití nebo obchodní problémy může PdM efektivně vyřešit. Existují tři důležitá kvalifikační kritéria, která je potřeba vzít v úvahu při výběru problému:

- Problém musí být předpověďně v podstatě. To znamená, že by měl být cíl nebo výsledek pro předpověď. Problém by měl být také jasným umístěním akce, aby se předešlo chybám při jejich zjištění.
- Problém by měl obsahovat záznam o provozní historii zařízení, které obsahuje _dobrý i špatný výsledek_. V rámci těchto záznamů by měla být taky dostupná sada akcí podniknutých za účelem zmírnění špatných výsledků. Zprávy o chybách, protokoly údržby pro snížení výkonu, opravit a nahradit protokoly jsou také důležité. Kromě toho opravy byly provedeny pro zlepšení a jsou užitečné i náhradní záznamy.
- Zaznamenaná historie by se měla projevit v _relevantních_ údajích, které _jsou dostatečně kvalitní_ , aby podporovaly případ použití. Další informace o relevanci dat a dostatečnosti najdete v tématu [požadavky na data pro prediktivní údržbu](#data-requirements-for-predictive-maintenance).
- Firma by nakonec měla mít odborníky na doménu s jasným porozuměním problému. Měli by znát interní postupy a postupy, které vám pomůžou analytikovi pochopit a interpretovat data. Měly by být také schopné provádět potřebné změny stávajících obchodních procesů, aby při potřeby mohli shromažďovat správná data týkající se problémů.

## <a name="sample-pdm-use-cases"></a>Ukázkové případy použití PdM
Tato část se zaměřuje na kolekci případů použití PdM z několika oborů, jako je například Aerospace, nástroje a přeprava. Každá část začíná obchodním problémem a zabývá se výhodami PdM, relevantními daty obklopujícími obchodní problém a konečně výhodami řešení PdM.

| Obchodní potíže | Výhody z PdM |
|:-----------------|-------------------|
|**Bezpečnostních**      |                   |
|_Zpoždění letu a zrušení_ v důsledku mechanických problémů. Chyby, které nejde opravit v čase, můžou způsobit zrušení letů a narušovat plánování a provoz. |Řešení PdM mohou předpovědět pravděpodobnost, že letadlo je zpožděné nebo zrušené z důvodu mechanického selhání.|
|_Selhání částí motoru_ letadla: náhrady součástí leteckého motoru patří mezi nejběžnější úlohy údržby v rámci leteckého průmyslu. Řešení údržby vyžadují pečlivou správu dostupnosti, doručování a plánování komponent.|Schopnost shromažďovat informace o spolehlivosti součástí vede k výraznému snížení nákladů na investice.|
|**Finance** |                         |
|_Selhání ATM_ je běžný problém v rámci bankovního odvětví. Problémem je, aby se nahlásila pravděpodobnost, že transakce pro stažení hotovosti ATM je přerušená kvůli zaseknutí nebo selhání části v platebním nástroji. V závislosti na předpovědi selhání transakce se ATMs dá aktivně obsluhovat, aby nedocházelo k chybám.| Místo toho, aby se počítač v průběhu transakce nezdařil, je žádoucí alternativou naprogramovat počítač k odepření služby na základě předpovědi.|
|**Energetický sektor** |                          |
|_Chyby větrné turbíny_ : větrné turbíny jsou hlavním zdrojem energie v rámci životně zodpovědných zemí nebo oblastí v životním prostředí a zahrnují velké náklady. Klíčovou komponentou v větrných turbínách je generátor motoru, jehož selhání vykresluje turbínu neúčinným. Je také velmi nákladné opravit.|Předpovídání klíčových ukazatelů výkonu, jako je MTTF (Průměrná doba selhání), může napomáhat energetickým firmám zabránit chybám turbín a zajistit minimální prostoje. Pravděpodobnost selhání bude informovat techniky o monitorování turbín, kteří brzy selžou, a plánovat režimy údržby založené na čase. Prediktivní modely poskytují přehled o různých faktorech, které přispívají k selhání, což pomáhá technikům lépe pochopit hlavní příčiny problémů.|
|_Selhání přepínacího okruhu_ : distribuce elektřiny na domy a firmy vyžaduje, aby elektrické napájení byly na všech místech v provozu, aby bylo zaručeno poskytování energie. Přerušení okruhů pomůžou omezit nebo zabránit poškození linek napájení během přetížení nebo nepříznivých povětrnostních podmínek. Tady je problém s firmou, abyste předpovídat selhání přerušení okruhů.| Řešení PdM vám pomůžou snižovat náklady na opravu a zvyšovat životnost zařízení, jako jsou například přerušení okruhů. Můžou zlepšit kvalitu napájení v síti tím, že snižují nečekaná selhání a přerušení služeb.|
|**Přeprava a logistika** |    |
|_Selhání dvířek pro výtah_ : velké společnosti pro výtah poskytují úplnou službu Stack pro miliony funkčních výtahů po celém světě. Zabezpečení, spolehlivost a provozuschopnost výtahu jsou hlavními aspekty svých zákazníků. Tyto společnosti sledují tyto a různé další atributy přes senzory, které jim pomůžou s opravou a preventivní údržbou. U výtahu se u nejzávažnějších problémů zákazníků nepracuje s dveřmi na výtahech. Obchodní problém v tomto případě je poskytnout prediktivní aplikaci znalostní báze, která předpovídá potenciální příčiny selhání dvířek.| Výtahy jsou kapitálové investice do životnosti po dobu až 20-30 let. Takže každý potenciální prodej může být vysoce konkurenční; Proto jsou očekávání služby a podpory vysoké. Prediktivní údržba může těmto společnostem poskytnout výhodu ve svých konkurentech v rámci svých produktů a nabídek služeb.|
|_Selhání kolečka_ : účet selhání kolečka pro polovinu všech vykolejí vlaku a nákladových miliard do celosvětového železničního odvětví. Selhání kolečka také způsobují zhoršení kolejnicí, někdy ale způsobují, že se kolejnice může předčasně rozdělit. Přerušení železnic vedou k závažným událostem, jako jsou například vypadnutí. Aby se tyto instance nezobrazovaly, železnice monitorují výkon kol a nahradí je preventivním způsobem. Tady je problém s obchodním problémem, který je předpovědi selhání kolečka.| Prediktivní údržba kol pomůže s náhradou kol za běhu. |
|_Selhání dveří Subway vlaku_ : zásadním důvodem pro prodlevy při operacích Subway jsou chyby dveří vlakových automobilů. Tady je příklad obchodního problému, který vám umožní předpovědět selhání dveří.|Díky včasnému povědomí o selhání dvířek nebo počtu dní, ke kterým dojde v případě výpadku dveří, pomůže podnik optimalizovat plány obsluhy dveří.|

V další části najdete podrobné informace o tom, jak realizovat PdM výhody popsané výše.

## <a name="data-science-for-predictive-maintenance"></a>Datové vědy pro prediktivní údržbu

V této části najdete obecné pokyny k principům pro datové vědy a praxi pro PdM. Je určena k tomu, aby pomohly TDM, architektům řešení nebo vývojářům pochopit požadavky a procesy pro vytváření koncových aplikací AI pro PdM. Tuto část si můžete přečíst spolu s přehledem ukázek a šablon pro podávání konceptů, které jsou uvedené v [šablonách řešení pro prediktivní údržbu](#solution-templates-for-predictive-maintenance). Pak můžete použít tyto zásady a osvědčené postupy k implementaci řešení PdM v Azure.

> [!NOTE]
> Tato příručka není určená k učení s datovou vědy pro čtenáře. K dispozici je několik užitečných zdrojů pro další čtení v části pro [školení prostředků pro prediktivní údržbu](#training-resources-for-predictive-maintenance). [Šablony řešení](#solution-templates-for-predictive-maintenance) uvedené v průvodci ukazují některé z těchto postupů AI na konkrétní PDM problémy.

## <a name="data-requirements-for-predictive-maintenance"></a>Požadavky na data pro prediktivní údržbu

Úspěch jakéhokoli učení závisí na (a) kvalitě, co je vyvíjena, a (b) na možnosti, kterou si učí. Prediktivní modely Naučte se vzory z historických dat a předpovídat budoucí výsledky s určitou pravděpodobností založenou na těchto pozorovaných vzorcích. Prediktivní přesnost modelu závisí na relevanci, dostatečnosti a kvalitě dat školení a testování. Nová data, která jsou v tomto modelu typu skore, by měla mít stejné funkce a schéma jako data školení a testování. Charakteristiky funkcí (typ, hustota, distribuce atd.) by měly odpovídat údajům z školicích a testovacích sad dat. Tato část se zaměřuje na požadavky na data.

### <a name="relevant-data"></a>Relevantní data

Nejprve musí být data _relevantní pro daný problém_. Vezměte v úvahu případ použití _při selhání kolečka_ popsané výše – školicí data by měla obsahovat funkce týkající se operací kolečka. Pokud by byl problém předpovídat selhání  _trakčního systému_ , musí školicí data zahrnovat všechny různé komponenty trakčního systému. První případ cílí na konkrétní komponentu, zatímco druhý případ cílí na selhání většího subsystému. Obecně doporučujeme, abyste navrhli systémy předpovědi pro konkrétní součásti, a ne větší subsystémy, protože ta by měla větší rozptýlená data. Odborník na doménu (viz [opravňující problémy pro prediktivní údržbu](#qualifying-problems-for-predictive-maintenance)) by měl pomáhat při výběru nejdůležitějších podmnožin dat pro analýzu. Relevantní zdroje dat jsou podrobněji popsány v článku [Příprava dat pro prediktivní údržbu](#data-preparation-for-predictive-maintenance).

### <a name="sufficient-data"></a>Dostatečná data
Mezi časté otázky týkající se dat v historii selhání patří dvě otázky: (1) "kolik událostí selhání je potřeba k vytvoření výukového modelu?" (2) "kolik záznamů je považováno za" dostačující "? Neexistují žádné definitivní odpovědi, ale pouze pravidla pro palec. Pro (1), větší počet událostí selhání, lepší model. Pro (2) a přesný počet událostí selhání závisí na datech a kontextu problému, který je vyřešen. Ale na překlopení dojde při selhání počítače příliš často, ale společnost ho nahradí, čímž se sníží instance selhání. Tady je důležité pokyny od odborníka na doménu. Existují však metody, které je možné vypořádat s problémem _vzácných událostí_. Jsou popsány v oddílu [zpracování nevyvážených dat](#handling-imbalanced-data).

### <a name="quality-data"></a>Data kvality
Kvalita dat je Kritická – každá hodnota atributu prediktivního atributu musí být ve spojení s hodnotou cílové proměnné _přesné_ . Kvalita dat je dobře posuzovaná oblast v oblasti statistik a správa dat, a proto je mimo rozsah tohoto průvodce.

> [!NOTE]
> K dispozici je několik zdrojů a podnikových produktů k doručování dat o kvalitě. Ukázka odkazů je uvedená níže:
> - Dasu, T, Johnsonem, T, průzkumné dolování dat a čištění dat, Wiley, 2003.
> - [Analýza průzkumnéch dat, Wikipedii](https://en.wikipedia.org/wiki/Exploratory_data_analysis)
> - [Hellerstein, a kvantitativní čištění dat pro velké databáze](http://db.cs.berkeley.edu/jmh/papers/cleaning-unece.pdf)
> - [de Jonge, E, Van der Loo, M, Úvod k čištění dat pomocí jazyka R](https://cran.r-project.org/doc/contrib/de_Jonge+van_der_Loo-Introduction_to_data_cleaning_with_R.pdf)

## <a name="data-preparation-for-predictive-maintenance"></a>Příprava dat pro prediktivní údržbu

### <a name="data-sources"></a>Zdroje dat

Mezi relevantní zdroje dat pro prediktivní údržbu patří mimo jiné:
- Historie selhání
- Historie údržby a oprav
- Provozní podmínky počítače
- Metadata zařízení

#### <a name="failure-history"></a>Historie selhání
Události selhání jsou v aplikacích PdM vzácné. Při sestavování předpovědí modelů však musí algoritmus získat informace o běžném provozním vzoru komponenty a také o jeho vzorech selhání. Takže školicí data by měla obsahovat dostatečný počet příkladů z obou kategorií. Záznamy údržby a historie nahrazení částí jsou dobrými zdroji pro nalezení událostí selhání. S využitím některých znalostí v doméně je také možné definovat anomálie v školicích datech jako selhání.

#### <a name="maintenancerepair-history"></a>Historie údržby a oprav
Historie údržby assetu obsahuje podrobnosti o nahrazených součástech, provedených opravných aktivitách atd. Tyto události zaznamenávají vzory snížení úrovně. Absence těchto důležitých informací v školicích datech může vést k zavádějícím výsledkům modelu. Historii selhání můžete také najít v historii údržby jako speciální kódy chyb nebo data objednávky pro části. Další zdroje dat, které mají vliv na vzorce selhání, by měly být prověřené a poskytované odborníky na domény.

#### <a name="machine-operating-conditions"></a>Provozní podmínky počítače
Datové proudy založené na senzoru (nebo jiné) streamovaná data v provozu jsou důležitým zdrojem dat. Klíčovým předpokladem v PdM je, že stav počítače se během běžné operace degraduje v průběhu času. Očekává se, že data budou obsahovat časově proměnlivé funkce, které zachycují tento vzor stárnutí, a všechny anomálie, které vedou ke snížení úrovně. K obznámení selhání a vzorců, které nejsou v průběhu času, se vyžaduje dočasný aspekt dat. Na základě těchto datových bodů se algoritmus učí a odhadne, kolik dalších jednotek času může počítač dál fungovat, než se povede k jeho neúspěchu.

#### <a name="static-feature-data"></a>Data statických funkcí
Statické funkce jsou metadata o zařízení. Příkladem může být vybavení, model, datum výroby, počáteční datum provozu, umístění systému a další technické specifikace.

Příklady relevantních dat pro [případy použití Sample PDM](#sample-pdm-use-cases) jsou následující:

| Případ použití | Příklady relevantních dat |
|:---------|---------------------------|
|_Zpoždění letů a zrušení_ | Informace o letových trasách ve formě ramen a protokolů stránek v letadle. Data o vzjezdové nožkě zahrnují podrobnosti o směrování, jako je datum odchodu, čas, letiště, layovers atd. Protokol stránky obsahuje řadu chybových kódů a kódů údržby zaznamenaných pracovníky údržby provozu.|
|_Selhání částí motoru letadla_ | Data shromážděná ze senzorů v letadle, která poskytují informace o podmínce různých částí. Záznamy údržby vám pomohou identifikovat, kdy došlo k selhání komponenty a kdy byly nahrazeny.|
|_Chyba ATM_ | Čtení senzorů pro každou transakci (Vkladová a kontrolní platba) a obvracení hotovosti. Informace o měření mezer mezi poznámkami, tloušťkou poznámky, vzdáleností doručení, kontrolu atributů atd. Záznamy údržby, které poskytují kódy chyb, informace o opravě, čas posledního vyplnění platebního držáku.|
|_Selhání větrné turbíny_ | Senzory sledují podmínky turbín, jako je teplota, směr větru, vygenerovaná energie, rychlost generátoru atd. Data se shromažďují z několika větrných turbín z větrných farem v různých oblastech. Každé turbínové prostředí má obvykle více čtení snímačů, které v určitém časovém intervalu přenáší měření.|
|_Selhání přerušení okruhů_ | Protokoly údržby, které zahrnují opravné, preventivní a systematické akce. Provozní data, která obsahují automatické a ruční příkazy odesílané přepínacím modulům okruhů, například pro akce otevřít a zavřít. Metadata zařízení, jako je datum výroby, umístění, model atd. Specifikace pro přerušení okruhů, jako jsou úrovně napětí, geografická poloha, okolní podmínky.|
|_Selhání dvířek pro výtahy_| Metadata výtahu, jako je typ výtahu, datum výroby, frekvence údržby, typ budovy atd. Provozní informace, jako je počet cyklů dvířek, Průměrná doba zavírání dveří Historie selhání s příčinami.|
|_Selhání kolečka_ | Data senzorů, která měří akceleraci kolečka, brzdové instance, hnací dráhy, rychlosti atd. Statické informace o kolech, jako je výrobce, datum výroby. Data o selhání odvozená z databáze pořadí částí, která sledují data a množství objednávek.|
|_Selhání dveří Subway vlaku_ | Doba otevírání a zavírání dveří, další provozní data, jako je aktuální podmínka vlakových dveří. Statická data by zahrnovala sloupce hodnota identifikátoru prostředku, čas a podmínka.|

### <a name="data-types"></a>Typy dat
S ohledem na výše uvedené zdroje dat jsou dva hlavní datové typy zjištěné v doméně PdM:

- _Dočasná data_ : provozní telemetrie, podmínky počítače, typy pracovních objednávek, kódy priorit, které budou mít časová razítka v době nahrávání. Chyba, Údržba/Oprava a historie využití budou mít také časová razítka přidružená k jednotlivým událostem.
- _Statická data_ : funkce počítačů a funkce operátoru jsou obecně statické, protože popisují technické specifikace počítačů nebo atributů operátorů. Pokud se tyto funkce můžou v průběhu času měnit, měly by k nim také být přidružená časová razítka.

Prediktivní a cílové proměnné by se měly předzpracovat/transformovat na [číselné, kategorií a další datové typy](https://www.statsdirect.com/help/basics/measurement_scales.htm) v závislosti na použitém algoritmu.

### <a name="data-preprocessing"></a>Předběžné zpracování dat
Jako součást pro _strojírenství funkcí_ Připravte data z různých datových proudů, abyste mohli vytvořit schéma, ze kterého je možné snadno vytvářet funkce. Nejprve Vizualizujte data jako tabulku záznamů. Každý řádek v tabulce představuje instanci školení a sloupce představují funkce _prediktivních_ funkcí (označované také jako nezávislé atributy nebo proměnné). Uspořádejte data tak, aby poslední sloupce byly _cílem_ (závislá proměnná). Pro každou instanci školení přiřaďte _popisek_ jako hodnotu tohoto sloupce.

Pro dočasná data rozdělte dobu trvání dat ze senzorů na časové jednotky. Každý záznam by měl patřit do jednotky času pro určitý prostředek _a měl by nabízet odlišné informace_. Časové jednotky se definují na základě obchodních potřeb v násobcích sekund, minut, hodin, dnů, měsíců a tak dále. Časová jednotka nemusí _být stejná jako frekvence shromažďování dat_. Pokud je frekvence vysoká, data nemusí zobrazovat žádný významný rozdíl mezi jednou jednotkou. Předpokládejme například, že se teplota okolí shromáždila každých 10 sekund. Použití stejného intervalu pro školení dat pouze vyrovná počet příkladů bez zadání dalších informací. V takovém případě by lepší strategií bylo použít průměrnou hodnotu dat za 10 minut nebo hodinu na základě obchodního odůvodnění.

Pro statická data
- _Záznamy údržby_ : nezpracovaná data údržby obsahují identifikátor assetu a časové razítko s informacemi o aktivitách údržby, které byly provedeny v daném časovém okamžiku. Transformuje aktivity údržby do _kategorií_ sloupců, kde každý popisovač kategorie jednoznačně mapuje na určitou akci údržby. Schéma pro záznamy údržby by zahrnovalo identifikátor prostředku, čas a akci údržby.

- _Záznamy o selhání_ : chyby nebo příčiny selhání lze zaznamenat jako konkrétní chybové kódy nebo události selhání definované konkrétními obchodními podmínkami. V případech, kdy má vybavení více chybových kódů, by měl odborník na doménu pomáhat určit ty, které se vztahují k cílové proměnné. Zbývající kódy chyb nebo podmínky použijte k vytvoření _prediktivních_ funkcí, které korelují s těmito chybami. Schéma pro záznamy o selhání by zahrnovalo identifikátor prostředku, čas, selhání nebo důvod selhání – Pokud je k dispozici.

- _Metadata počítače a operátora_ : sloučí data počítače a operátoru do jednoho schématu a přidružte tak Asset k operátorovi spolu s příslušnými atributy. Schéma pro podmínky počítače by zahrnovalo identifikátor prostředku, funkce assetu, identifikátor operátora a funkce operátora.

Mezi další kroky předzpracování dat patří _zpracování chybějících hodnot_ a _normalizace_ hodnot atributů. Podrobná diskuze je nad rámec tohoto průvodce – další užitečné odkazy najdete v další části.

U výše uvedených předzpracovaných zdrojů dat je finální transformace před technikou, která se připojí k výše uvedeným tabulkám na základě identifikátoru assetu a časového razítka. Výsledná tabulka by měla mít hodnoty null ve sloupci selhání, pokud je počítač v normálním provozu. Tyto hodnoty null mohou být přizpůsobeny indikátorem pro normální operaci. Pomocí tohoto sloupce selhání můžete vytvořit _popisky pro prediktivní model_. Další informace najdete v části [techniky modelování pro prediktivní údržbu](#modeling-techniques-for-predictive-maintenance).

## <a name="feature-engineering"></a>Příprava atributů
Technologie funkcí je prvním krokem před modelováním dat. Její role v rámci vědeckého zpracování dat [je popsána zde](./create-features.md). _Funkce_ je prediktivní atribut pro model – například teplota, tlak, vibrace a tak dále. Pro PdM zahrnuje inženýr funkcí abstrakci stavu počítače nad historickými daty shromážděnými během určité doby trvání. V takovém smyslu se liší od jeho partnerských uzlů, jako je vzdálené monitorování, detekce anomálií a detekce selhání. 

### <a name="time-windows"></a>Časová okna
Vzdálené monitorování zahrnuje hlášení událostí, ke kterým dochází _v čase_. Modely detekce anomálií vyhodnocují (skóre) příchozí proudy dat, aby bylo možné označit anomálie k bodům v čase. Detekce selhání klasifikuje selhání podle konkrétních typů, ke kterým dochází v čase. Na rozdíl od toho PdM zahrnuje předpověď chyb v _budoucím časovém období_ , a to na základě funkcí, které reprezentují chování počítače v _historických časových obdobích_. V případě PdM jsou data funkcí z jednotlivých časových bodů příliš nepřesná, takže je nemusíte předpovídat. Data pro jednotlivé funkce proto musí být _smoothened_ agregací datových bodů v čase okna.

### <a name="lag-features"></a>Funkce lag
Obchodní požadavky definují, jak daleko má model předpovídat do budoucna. Tato doba trvání pomáhá definovat, jak daleko se má model Hledat, aby bylo možné tyto předpovědi. Toto období "vyhledávání zpět" se nazývá _Prodleva_ a funkce, které se v průběhu tohoto období zpoždění procházejí, se nazývají _funkce pro prodlevu_. V této části jsou popsány funkce pro prodlevy, které lze vytvořit ze zdrojů dat s časovými razítky, a vytváření funkcí ze statických zdrojů dat. Funkce pro prodlevu jsou obvykle _číselné_ povahy.

> [!IMPORTANT]
> Velikost okna je určena pomocí experimentu a měla by být dokončena pomocí odborníka na doménu. Stejné omezení se uchovává pro výběr a definování funkcí lag, jejich agregací a typ Windows.

#### <a name="rolling-aggregates"></a>Kumulované agregace
Pro každý záznam prostředku je jako počet jednotek času pro výpočet agregovaných hodnot zvoleno posuvné okno s velikostí "W". Funkce lag se pak vypočítávají s použitím období W _před datem_ záznamu. Na obrázku 1 modré čáry ukazují hodnoty senzorů zaznamenané pro daný prostředek pro každou jednotku času. Označují Kumulovaný průměr hodnot funkcí v okně o velikosti W = 3. Klouzavý průměr se vypočítává na všech záznamech s časovými razítky v rozsahu t<sub>1</sub> (oranžová) na t<sub>2</sub> (zeleně). Hodnota pro W je obvykle v řádu minut nebo hodin v závislosti na povaze dat. Ale u určitých problémů může po vyzvednutí velkých písmen W (řekněme 12 měsíců) poskytnout celou historii assetu až do doby, než se zaznamená.

![Obrázek 1: Souhrnné funkce pro Shrnutí](./media/predictive-maintenance-playbook/rolling-aggregate-features.png)

Obrázek 1: Souhrnné funkce pro Shrnutí

Příklady kumulovaných agregací v časovém intervalu jsou míry, průměr, CUMESUM (kumulativní součet), minimální/maximální hodnota. Kromě toho se často používají variance, směrodatná odchylka a počet podseznamů nad N směrodatnými odchylkami. Příklady agregací, které mohou být použity pro [případy použití](#sample-pdm-use-cases) v tomto průvodci, jsou uvedeny níže. 
- _Zpoždění letu_ : počet chybových kódů za poslední den/týden.
- _Selhání součásti leteckého motoru_ : válcování znamená, směrodatná odchylka a součet za poslední den, týden atd. Tato metrika by měla být určena společně s odborníkem na obchodní doménu.
- _Chyby ATM_ : valení, medián, rozsah, směrodatné odchylky, počet podrozsahů mimo tři směrodatné odchylky, horní a dolní CUMESUM.
- _Selhání dveří Subway vlaku_ : počet událostí za poslední den, týden, dva týdny atd.
- _Selhání dělení na okruhy_ : počet selhání za minulý týden, rok, tři roky atd.

Další užitečnou technikou v PdM je zachytit změny trendů, špičky a změny úrovně pomocí algoritmů, které zjišťují anomálie v datech.

#### <a name="tumbling-aggregates"></a>Agregáty bubnu
U každého označeného záznamu prostředku je definováno okno velikosti _w- <sub>k</sub>_ , kde _k_ je počet oken velikosti _w_. Agregace se pak vytvoří přes _k_ _bubnu Windows_ _w-k, w- <sub>(n-1)</sub>,..., w- <sub>2</sub>, w- <sub>1</sub>_ pro období před časovým razítkem záznamu. _k_ může být malé číslo pro zachycení krátkodobých účinků nebo velké číslo pro zachycení dlouhodobých vzorů snížení úrovně. (viz obrázek 2).

![Obrázek 2. Nabubnování agregovaných funkcí](./media/predictive-maintenance-playbook/tumbling-aggregate-features.png)

Obrázek 2. Nabubnování agregovaných funkcí

Například funkce lag pro případ použití větrné turbíny může být vytvořena s W = 1 a k = 3. Představují prodlevu za každý poslední 3 měsíc pomocí horních a dolních odlehlého množství.

### <a name="static-features"></a>Statické funkce

Mezi příklady statických funkcí patří technické specifikace zařízení, jako je datum výroby, číslo modelu a umístění. Jsou považovány za proměnné _kategorií_ pro modelování. Mezi příklady pro případ použití přerušení okruhů patří napětí, aktuální, kapacita napájení, typ transformátoru a zdroj napájení. V případě selhání kolo je příkladem typ Tire kol (slitina vs – ocel).

Přípravné úsilí na přípravu dat by mělo vést k sestavování dat, jak vidíte níže. Data o školeních, testováních a ověřováních by měla mít toto logické schéma (v tomto příkladu se zobrazuje čas v jednotkách dnů).

| ID assetu | Čas | \<Feature Columns> | Popisek |
| ---- | ---- | --- | --- |
| A123 |Den 1 | . . . | . |
| A123 |Den 2 | . . . | . |
| ...  |...   | . . . | . |
| B234 |Den 1 | . . . | . |
| B234 |Den 2 | . . . | . |
| ...  |...   | . . . | . |

Posledním krokem při strojírenství funkcí je **označení** cílové proměnné. Tento proces závisí na technikě modelování. Technika modelování pak závisí na obchodním problému a charakteru dostupných dat. Popisování je popsáno v další části.

> [!IMPORTANT]
> Příprava dat a strojírenství funkcí jsou důležité, protože techniky modelování dorazí na úspěšná PdM řešení. Odborník na doménu a odborník by měli investovat do správné funkce a dat pro model o významné době. Malá ukázka z mnoha knih pro strojírenství funkcí je uvedená níže:
> - Pyle, D. Příprava dat pro dolování dat (Morgan Kaufmann Series v systémech Správa dat), 1999
> - Zheng, A., Casari, A. funkce pro Machine Learning: principy a techniky pro odborníky přes data, O'Reilly, 2018.
> - Dong, G. Liu, H. (editory), strojírenství funkcí pro Machine Learning a analýzu dat (Chapman & data dolování/CRC a řada zjišťování znalostí), CRC Press, 2018.

## <a name="modeling-techniques-for-predictive-maintenance"></a>Techniky modelování pro prediktivní údržbu

Tato část popisuje hlavní techniky modelování pro problémy PdM a jejich konkrétní metody konstrukce labelů. Všimněte si, že jedinou technikou modelování lze použít v různých oborech. Technika modelování je spárována s problémem s datovou vědy, nikoli s kontextem dat.

> [!IMPORTANT]
> Výběr popisků pro případy selhání a strategii označování  
> by měla být určena při konzultacích s odborníkem na doménu.

### <a name="binary-classification"></a>Binární klasifikace
Binární klasifikace se používá k _předpovědi pravděpodobnosti, že se část zařízení v budoucím časovém období, které_ se označuje jako _budoucí období horizontu X_ , nezdařila. X se určuje podle obchodního problému a dat při konzultacích s odborníkem na doménu. Příklady:
- _Minimální doba realizace_ nutná k nahrazení komponent, nasazení prostředků údržby, provedení údržby, aby se předešlo problému, který se může v daném období vyskytovat.
- _minimální počet událostí_ , ke kterým může dojít, než dojde k problému.

V tomto postupu jsou identifikovány dva typy výukových příkladů. Kladný příklad, _který označuje selhání_ s popiskem = 1. Negativní příklad, který označuje normální operace s jmenovkou = 0. Cílová proměnná, a proto hodnoty popisku, jsou _kategorií_. Model by měl identifikovat každý nový příklad, který bude pravděpodobně neúspěšný nebo fungovat normálně za dalších X časových jednotek.

#### <a name="label-construction-for-binary-classification"></a>Konstrukce Label pro binární klasifikaci
Otázka je tady: "Jaká je pravděpodobnost, že se Asset za dalších X jednotek času nezdařil?" Pro zodpovězení této otázky je třeba označit záznamy X před selháním assetu jako "o selhání" (popisek = 1) a označit všechny ostatní záznamy jako "normální" (popisek = 0). (viz obrázek 3).

![Obrázek 3: Označování pro binární klasifikaci](./media/predictive-maintenance-playbook/labelling-for-binary-classification.png)

Obrázek 3: Označování pro binární klasifikaci

Příklady strategie označování pro některé případy použití jsou uvedené níže.
- _Zpoždění letu_ : X se dá zvolit jako jeden den, aby se předpovídá zpoždění v příštích 24 hodinách. Všechny lety, které jsou v průběhu 24 hodin před selháním, jsou označeny jako 1.
- _Selhání platebních plateb ATM_ : cílem může být určení pravděpodobnosti selhání transakce v následující jedné hodině. V takovém případě jsou všechny transakce, ke kterým došlo během poslední hodiny selhání, označeny jako 1. Pro předpověď pravděpodobnosti selhání na dalších neočekávaných poznámkách v měně se všechny poznámky, které jsou v posledních N poznámkách o selhání, označené jako 1.
- _Selhání dělení na okruhy_ : cílem může být předpovědět další selhání příkazu pro přerušení okruhu. V takovém případě se X vybere jako jeden z budoucích příkazů.
- _Selhání dveří vlaků_ : X se dá zvolit jako dva dny.
- _Chyby větrné turbíny_ : X se dá zvolit jako dva měsíce.

### <a name="regression-for-predictive-maintenance"></a>Regrese pro prediktivní údržbu
Regresní modely slouží k _výpočtu zbývající doby životnosti (RUL) assetu_. RUL se definuje jako doba, po kterou je Asset funkční, než dojde k dalšímu selhání. Každý školicí příklad je záznam, který patří do časové jednotky _nY_ pro určitý prostředek, kde _n_ je násobek. Model by měl vypočítat RUL každého nového příkladu jako _průběžné číslo_. Toto číslo označuje dobu zbývající v době před selháním.

#### <a name="label-construction-for-regression"></a>Konstrukce jmenovky pro regresi
Otázka je tady: "Co je zbývající doba životnosti (RUL) vybavení?" U každého záznamu před selháním Vypočítejte popisek tak, aby byl počet jednotek času zbývajících před dalším selháním. V této metodě jsou popisky souvislé proměnné. (Viz obrázek 4)

![Obrázek 4. Popis regrese](./media/predictive-maintenance-playbook/labelling-for-regression.png)

Obrázek 4. Popis regrese

Pro regresi je označení provedeno s odkazem na bod selhání. Jeho výpočet není možný bez vědomí, jak dlouho se Asset před chybou zadržel. Proto na rozdíl od binární klasifikace nelze prostředky bez jakýchkoli chyb v datech použít k modelování. Tento problém se nejlépe řeší jinou statistickou technikou, která se nazývá [analýza přežití](https://en.wikipedia.org/wiki/Survival_analysis). Ale případné komplikace mohou nastat při použití této techniky, aby PdM případy použití, které obsahují časově proměnlivá data s častými intervaly. Další informace o tom, jak analyzovat, najdete na [této](https://www.cscu.cornell.edu/news/news.php/stnews78.pdf)straně.

### <a name="multi-class-classification-for-predictive-maintenance"></a>Klasifikace více tříd pro prediktivní údržbu
Techniky klasifikace s více třídami lze použít v řešeních PdM pro dva scénáře:
- Předpověď _dvou budoucích výsledků_ : první výsledek je _časový rozsah pro selhání_ assetu. Asset se přiřadí jednomu z několika možných časových období. Druhým výsledkem je pravděpodobnost selhání v budoucím období kvůli _jedné z několika hlavních příčin_. Tato předpověď umožňuje pracovním postupům údržby sledovat příznaky a plánovat plány údržby.
- Předpovědět _nejpravděpodobnější hlavní příčinu_ daného selhání. Tento výsledek doporučuje správnou sadu akcí údržby pro vyřešení chyby. Seřazený seznam hlavních příčin a doporučených oprav může technikům pomáhat určit prioritu jejich opravných akcí po selhání.

#### <a name="label-construction-for-multi-class-classification"></a>Konstrukce popisků pro klasifikaci více tříd
Otázka je tady: "Jaká je pravděpodobnost, že se Asset nezdařil v dalších jednotkách _NZ_ , kde _n_ je počet období?" K zodpovězení této otázky popište záznamy nZ před selháním assetu pomocí časových intervalů (3Z, 2Z, Z). Označte všechny ostatní záznamy jako "normální" (popisek = 0). V této metodě obsahuje cílová proměnná hodnoty _kategorií_ . (Viz obrázek 5).

![Obrázek 5. Popisky předpovědi doby selhání pro klasifikaci s více třídami](./media/predictive-maintenance-playbook/labelling-for-multiclass-classification-for-failure-time-prediction.png)

Obrázek 5. Označování pro klasifikaci více tříd pro předpověď doby selhání

Otázka je tady: "Jaká je pravděpodobnost, že se Asset v dalších X časových jednotkách nezdařil z důvodu hlavní příčiny/problému _P <sub>i</sub>_ ?" kde _je_ počet možných hlavních příčin. Chcete-li odpovědět na tuto otázku, označit záznamy X před selháním assetu jako "o to neúspěšné z důvodu hlavní příčiny _P <sub>i</sub>_ " (Label = _P <sub>i</sub>_ ). Označí všechny ostatní záznamy jako "normální" (popisek = 0). V této metodě jsou také popisky kategorií (viz obrázek 6).

![Obrázek 6. Popisky předpovědi hlavní příčiny pro klasifikaci s více třídami](./media/predictive-maintenance-playbook/labelling-for-multiclass-classification-for-root-cause-prediction.png)

Obrázek 6. Označování pro klasifikaci více tříd pro předpověď hlavní příčiny

Model přiřadí pravděpodobnost selhání z důvodu každé _P <sub>i</sub>_ pravděpodobnosti, že nedošlo k chybě. Tyto pravděpodobnosti je možné seřadit podle velikosti, aby bylo možné předpovědi problémů, které jsou v budoucnosti pravděpodobně nastat.

Otázka je tady: "Jaké akce při údržbě doporučujeme po selhání?" K zodpovězení této otázky nevyžaduje označení _pro vyzvednutí budoucího horizontu_ , protože model není v budoucnu předpověď. Je pouze předpověď nejpravděpodobnější hlavní příčiny, _Jakmile k chybě_ došlo.

## <a name="training-validation-and-testing-methods-for-predictive-maintenance"></a>Metody školení, ověřování a testování pro prediktivní údržbu
[Vědecké zpracování týmových dat](./overview.md) poskytuje úplný přehled modelu testování testovacího cyklu. Tato část popisuje aspekty, které jsou jedinečné pro PdM.

### <a name="cross-validation"></a>Vzájemné ověřování
Cílem [vzájemného ověřování](https://en.wikipedia.org/wiki/Cross-validation_(statistics)) je definovat datovou sadu s cílem "testovat" model ve fázi školení. Tato datová sada se nazývá _sada ověření_. Tento postup pomáhá omezit problémy jako _přeložení_ a poskytuje přehled o tom, jak se model generalizuje na nezávislou datovou sadu. To znamená neznámou datovou sadu, která může být ze skutečného problému. Rutina školení a testování pro PdM potřebuje vzít v úvahu čas proměnlivé aspekty, aby se lépe zobecnit při nezměněných budoucích datech.

Mnoho algoritmů strojového učení závisí na mnoha parametrech, které můžou významně měnit výkon modelu. Optimální hodnoty těchto parametrů nejsou vypočítány automaticky při výuce modelu. Měly by být určeny vědeckým vědeckým daty. Existuje několik způsobů, jak najít správné hodnoty parametrů.

Nejběžnější je _k skládání křížového ověřování_ , které rozdělí příklady náhodně do skládání _k_ . Pro každou sadu hodnot parametrů s parametry můžete spustit algoritmus učení _k_ času. V každé iteraci použijte příklady v aktuálním skládání jako sadu ověřování a zbytek příkladů jako sadu školení. Vyškolujte algoritmus přes výukové příklady a vypočítají metriky výkonu v příkladech ověření. Na konci této smyčky vypočítá průměr z _k_ metrikám výkonu. Pro každou sadu hodnot parametrů, vyberte ty, které mají nejlepší průměrnou výkonnost. Úkol volby parametrů je často experimentální v podstatě.

V případě problémů PdM se data zaznamenávají jako časová řada událostí, které pocházejí z několika zdrojů dat. Tyto záznamy lze seřadit podle času označování. Proto pokud je datová sada _náhodně_ rozdělena do školicích a ověřovacích sad, _mohou být některé příklady školení později v čase než některé příklady ověření_. Budoucí výkon hodnot parametrů bude odhadnut na základě dat, která byla doručena _před_ vyškolením modelu. Tyto odhady můžou být příliš optimistické, zejména pokud časová řada není stacionární a vyvíjí se v průběhu času. V důsledku toho mohou být zvolené hodnoty parametrů neoptimální.

Doporučeným způsobem je rozdělit příklady do školicích a ověřovacích sad v _závislosti na čase_ , kde všechny příklady ověření jsou později v čase, než všechny příklady školení. Pro každou sadu hodnot parametrů můžete vyškolit algoritmus přes školicí datovou sadu. Změřte výkon modelu v rámci stejné sady ověření. Vyberte hodnoty parametrů, které zobrazují nejlepší výkon. Hodnoty parametrů, které jsou zvoleny výsledkem rozdělení vlaku/ověření, jsou vyšší než u hodnot, které jsou vybrány náhodně při křížovém ověření.

Konečný model je možné vygenerovat školením výukového algoritmu přes celá školení za použití nejlepších hodnot parametrů.

### <a name="testing-for-model-performance"></a>Testování výkonu modelu
Po sestavení modelu se vyžaduje odhad jeho budoucího výkonu pro nová data. Dobrý odhad je metrika výkonu hodnot parametrů vypočítaných v rámci sady ověření nebo průměrné metriky výkonu vypočítané z křížového ověření. Tyto odhady jsou často optimistické. Firma často může mít několik dalších pokynů, jak by chtěli model otestovat.

Doporučeným způsobem pro PdM je rozdělit příklady do školicích, ověřovacích a testovacích datových sad v _závislosti na čase_ . Všechny příklady testů by měly být později v čase, než jsou uvedeny všechny příklady školení a ověření. Po rozdělení vytvořte model a změřte jeho výkon, jak je popsáno výše.

Když je časová řada stacionární a snadno se předpovídá, vygenerují náhodné i časově závislé přístupy podobné odhady budoucího výkonu. Pokud ale časová řada není pevná, nebo je obtížné předpovědět, bude přístup závislý na čase generovat realističtější odhad budoucích výkonů.

### <a name="time-dependent-split"></a>Rozdělení závislé na čase
Tato část popisuje osvědčené postupy pro implementaci rozdělení závislého na čase. Obousměrné rozdělení závislé na čase mezi školicími a testovacími sadami je popsáno níže.

Předpokládejme datový proud událostí s časovým razítkem, například měření z různých senzorů. Definujte funkce a popisy příkladů školení a testování v časových snímcích, které obsahují více událostí. Například pro binární klasifikaci, vytváření funkcí založených na minulých událostech a vytváření popisků na základě budoucích událostí v rámci "X" jednotek času v budoucnosti (podívejte se na části techniky a modelování [funkcí](#feature-engineering) ). Proto se časový rámec popisku v příkladu nachází později než časový rámec jeho funkcí.

Pro rozdělení závislé na čase vyberte _dobu trvání školení T <sub>c</sub>_ , na které se má naučit model, s použitím parametrů, které jsou vyladěny pomocí historických dat až do T <sub>c</sub>. Chcete-li zabránit úniku budoucích popisků, které jsou mimo T<sub>c</sub> , do školicích dat, vyberte možnost Poslední čas pro popisek výukových příkladů na X jednotek před T<sub>c</sub>. V příkladu uvedeném na obrázku 7 představuje každý čtverec záznam v sadě dat, kde jsou vypočítány funkce a popisky, jak je popsáno výše. Obrázek zobrazuje záznamy, které by měly přejít do školicích a testovacích sad pro X = 2 a W = 3:

![Obrázek 7. Rozdělení závislé na čase pro binární klasifikaci](./media/predictive-maintenance-playbook/time-dependent-split-for-binary-classification.png)

Obrázek 7. Rozdělení závislé na čase pro binární klasifikaci

Zelené čtverce reprezentují záznamy patřící k časovým jednotkám, které lze použít pro školení. Každý školicí příklad je vygenerován tím, že zvažuje poslední tři tečky pro generaci funkcí a dvě budoucí období pro označování před T<sub>c</sub>. Pokud je libovolná část dvou budoucích období než T<sub>c</sub>, vylučte tento příklad ze sady školicích dat, protože se nepředpokládá žádná viditelnost mimo t<sub>c</sub>.

Černé čtverce reprezentují záznamy finální sady dat s popisky, které by se neměly používat v sadě školicích dat, podle výše uvedeného omezení. Tyto záznamy se také nepoužijí v datech testování, protože jsou před T<sub>c</sub>. Kromě toho časové rámce označení jsou částečně závislé na časovém rámci školení, což není ideální. Data o školeních a testováních by měla obsahovat samostatné časové rámce popisků, aby se zabránilo úniku informací popisku.

Výše popsaná technika umožňuje překrývat mezi příklady školení a testování, které mají časová razítka v blízkosti T<sub>c</sub>. Řešením pro dosažení většího rozdělení je vyloučit příklady, které jsou v jednotkách W v čase T<sub>c</sub> ze sady testů. Takové agresivní rozdělení ale závisí na dostatečné dostupnosti dat.

Regresní modely používané pro předpověď RUL jsou mnohem vážně ovlivněné problémem s únikem. Použití metody náhodného rozdělení vede k extrémnímu umísťování. V případě regresních problémů by rozdělení mělo být takové, aby záznamy patřící k assetům s chybami před T<sub>c</sub> přešly do sady školení. Záznamy prostředků, které mají chyby po přerušení, se přestanou do sady testů.

Dalším osvědčeným postupem při rozdělování dat pro školení a testování je použití rozdělení podle ID assetu. Rozdělení by mělo být takové, že žádný z prostředků použitých ve školicí sadě se nepoužívá při testování výkonu modelu. S využitím tohoto přístupu model nabízí lepší pravděpodobnost poskytnutí realističtějších výsledků novými prostředky.

### <a name="handling-imbalanced-data"></a>Manipulace s nevyváženými daty
V případě problémů s klasifikací, pokud existuje více příkladů jedné třídy než ostatní, je tato datová sada označována jako _nevyvážená_. V ideálním případě jsou upřednostňovány dostatečné zástupce každé třídy v školicích datech, aby bylo možné rozlišovat mezi různými třídami. Pokud je jedna třída méně než 10% dat, považují se za nevyvážená data. Podreprezentovaná třída se nazývá _menšinová třída_.

Mnohé PdM problémy čelí takovým nevyváženým datovým sadám, kde jedna třída je v porovnání s jinou třídou nebo třídami vážně neuvedena. V některých situacích může menšinová třída představovat pouze 0,001% z celkového počtu datových bodů. Nerovnováha třídy není jedinečná pro PdM. Další domény, ve kterých selhání a anomálie mají vzácnou situaci, čelí podobnému problému, například k detekci podvodů a vniknutí k síti. Tyto chyby tvoří příklady minoritní třídy.

Díky nerovnováhě tříd v datech je výkon většiny standardních výukových algoritmů ohrožen, protože se zaměřuje na minimalizaci celkové míry chyb. Pro datovou sadu s 99% negativním a 1% pozitivním příkladem může být model zobrazený tak, aby měl 99% přesnost označením všech instancí jako negativní. Model ale nebude zařadit všechny pozitivní příklady; Takže i když je jeho přesnost vysoká, algoritmus není vhodný. V důsledku toho se konvenční hodnocení metrik, jako je _Celková přesnost u chybových přenosů_ , nedostatečné pro získání nevyváženého učení. Při vystavení s nevyváženými datovými sadami se pro vyhodnocení modelu používají jiné metriky:
- Přesnost
- Recall
- Skóre F1
- Náklady upravené na ROC (provozní charakteristiky přijímače)

Další informace o těchto metrikách naleznete v tématu [vyhodnocení modelu](#model-evaluation).

Existují však některé metody, které vám pomohou vyřešit problém nerovnováhy třídy. Existují dva hlavní _způsoby vzorkování_ a učení s _důvěrnými náklady_.

#### <a name="sampling-methods"></a>Metody vzorkování
Nevyrovnané vzdělávání zahrnuje použití metod vzorkování pro úpravu školicích dat nastavených na vyváženou datovou sadu. Metody vzorkování nelze pro sadu testů použít. I když je k dispozici několik technik vzorkování, je většina přímých dopředně _náhodným vzorkováním_ a _v rámci vzorkování_.

_Náhodné převzorkování_ zahrnuje výběr náhodného vzorku z minoritní třídy, replikaci těchto příkladů a jejich přidání do školicí sady dat. V důsledku toho se zvyšuje počet příkladů v minoritní třídě a nakonec se vyrovnávají počet příkladů různých tříd. Nevýhodou převzorkování je, že více instancí určitých příkladů může způsobit, že se třídění bude příliš specifické, což vede k převzetí služeb při selhání. Model může zobrazit vysokou přesnost školení, ale jeho výkon u nezpracovaných testovacích dat může být optimální.

Naopak _náhodně v rámci vzorkování_ je výběr náhodného vzorku ze většiny třídy a odebrání těchto příkladů ze sady dat školení. Odebrání příkladů z třídy většina může ale způsobit, že klasifikátor nevede k důležitým koncepcím, které se týkají většiny třídy. _Hybridní vzorkování_ , kde je nadlimitní využití menšinových tříd, a většina třídy je v současné době ve vzorku, je další životaschopný přístup.

Existuje mnoho propracovaných metod vzorkování. Zvolená technika závisí na vlastnostech dat a výsledcích iterativních experimentů podle odborníka na data.

#### <a name="cost-sensitive-learning"></a>Učení s důvěrnými náklady
V PdM chyby, které tvoří minoritní třídu, mají větší význam než běžné příklady. Proto se fokus zaměřuje hlavně na výkon algoritmu při selhání. Nesprávná předpověď pozitivní třídy jako záporné třídy může být větší než naopak. Tato situace se běžně označuje jako nerovnost nebo asymetrické náklady na chybné zařazení prvků do různých tříd. Ideální klasifikátor by měl zajistit přesnost vysoké předpovědi pro třídu menšin, aniž by to ohrozilo přesnost třídy většina.

Existuje několik způsobů, jak dosáhnout tohoto zůstatku. Chcete-li zmírnit problém nestejné ztráty, přiřaďte vysoké náklady k chybné klasifikaci třídy menšin a pokuste se minimalizovat celkové náklady. Algoritmy jako _SVMs (podporují vektorové počítače)_ tuto metodu přinese z vlastního vlastnictví tím, že se během školení budou zadávat náklady na pozitivní a negativní příklady. Podobně metody zvyšování úrovně, jako jsou například posílené _rozhodovací stromy_ , obvykle znázorňují dobrý výkon s nevyváženými daty.

## <a name="model-evaluation"></a>Vyhodnocení modelu
Nestálá klasifikace je významným problémem pro PdM scénáře, kdy náklady na falešně náročné výstrahy na firmu jsou vysoké. Například rozhodnutí o založení letadlech na základě nesprávné předpovědi selhání motoru může rušit plány a cestovní plány. Přepnutí počítače do režimu offline ze spojnice sestavení může způsobit ztrátu výnosů. Proto je kritické hodnocení modelu se správnou metrikou výkonu pro nová testovací data.

Typické metriky výkonu pro vyhodnocení modelů PdM jsou popsané níže:

- [Přesnost](https://en.wikipedia.org/wiki/Accuracy_and_precision) je nejoblíbenější metrika, která se používá k popisu výkonu třídění. Ale přesnost je citlivá na distribuci dat a jedná se o neúčinné měření pro scénáře s nevyváženými datovými sadami. Místo toho se používají jiné metriky. K výpočtům a důvodům přesnosti modelu se používají nástroje, jako je [nejasná matice](https://en.wikipedia.org/wiki/Confusion_matrix) .
- [Přesnost](https://en.wikipedia.org/wiki/Precision_and_recall) PDM modelů se vztahuje k míře falešných poplachů. Nižší přesnost modelu obvykle odpovídá větší míře falešných poplachů.
- Míra [odvolání](https://en.wikipedia.org/wiki/Precision_and_recall) označuje, kolik chyb v sadě testů bylo správně identifikováno modelem. Vyšší míra odvolání znamená, že je model úspěšný při identifikaci skutečných chyb.
- [Známkou F1](https://en.wikipedia.org/wiki/F1_score) je harmonický průměr přesnosti a odvolání s jeho hodnotou v rozsahu od 0 (nejhorší) do 1 (nejlepší).

Pro binární klasifikaci
- [Pracovní křivky přijímače (Roc)](https://en.wikipedia.org/wiki/Receiver_operating_characteristic) jsou také oblíbenou metrikou. V případě více než ROC je výkon modelu interpretován na základě jednoho pevného operačního bodu na ROC.
- Ale pro problémy s PdM jsou _tabulky decile_ a _výtahové grafy_ více informativní. Zaměřuje se jenom na kladnou třídu (selhání) a poskytují komplexnější přehled o výkonu algoritmu než ROC křivky.
  - _Tabulky Decile_ jsou vytvářeny pomocí příkladů testů v sestupném pořadí pravděpodobnosti selhání. Seřazené vzorky se pak seskupí do deciles (10% vzorků s nejvyšší pravděpodobností, 20%, 30% atd.). Poměr (skutečná kladná míra)/(náhodný základ) pro každý decile pomáhá odhadnout výkon algoritmu u každého decile. Náhodné standardní hodnoty jsou převzaty z hodnot 0,1, 0,2 a tak dále.
  - Nastrojit [grafy](http://www2.cs.uregina.ca/~dbd/cs831/notes/lift_chart/lift_chart.html) vykreslí decile skutečnou míru a náhodnou kladnou sazbu pro všechny deciles. První deciles jsou obvykle soustředěné z výsledků, protože ukazují největší nárůst. První deciles se může také zobrazit jako zástupce pro "ohrožené", pokud se používá pro PdM.

## <a name="model-operationalization-for-predictive-maintenance"></a>Provoz modelu pro prediktivní údržbu

Výhody, které jsou cvičením v oblasti datové vědy, jsou realizované pouze v případě, že je vyškolený model vytvořen. To znamená, že model musí být nasazený v obchodních systémech, aby předpovědi na základě nových, dříve nezobrazených dat.  Nová data se musí přesně shodovat s _signaturou modelu_ poučeného modelu dvěma způsoby:
- všechny funkce musí být přítomné v každé logické instanci (řekněme řádek v tabulce) nových dat.
- Nová data musí být předem zpracovaná a každá z těchto funkcí byla navržena přesně stejným způsobem jako školicí data.

Výše uvedený proces je uveden v mnoha ohledech v oblasti akademické a průmyslové literatury. Ale všechny následující příkazy znamenají stejnou věc:
- Určení _skóre nových dat_ pomocí modelu
- _Použití modelu_ na nová data
- _Zprovoznění_ modelu
- _Nasazení_ modelu
- _Spustit model pro_ nová data

Jak bylo uvedeno dříve, provoz modelů pro PdM se liší od jeho partnerských uzlů. Scénáře zahrnující detekci anomálií a detekci selhání obvykle implementují _online hodnocení_ (označuje se také jako _bodování v reálném čase_ ). Zde _model_ vyhodnotí každý příchozí záznam a vrátí předpověď. Pro detekci anomálií je předpověď označením, že došlo k anomálii (příklad: jedna třída SVM). Pro detekci selhání by to byl typ nebo třída selhání.

Oproti tomu PdM zahrnuje _dávkové vyhodnocování_. Aby bylo možné v souladu s signaturou modelu, musí být funkce v nových datech zkonstruovány stejným způsobem jako školicí data. Pro velké datové sady, které jsou typické pro nová data, se funkce agreguje v časových oknech a v dávce se vyhodnotí jako skóre. Dávkové vyhodnocování se obvykle provádí v distribuovaných systémech, jako je [Spark](https://spark.apache.org/) nebo [Azure Batch](../../batch/batch-service-workflow-features.md). K dispozici je několik alternativ – jak optimální:
- Streamování datových strojů podporuje agregaci přes Windows v paměti. Proto by mohlo být namítáno, že podporují online hodnocení. Tyto systémy jsou ale vhodné pro zhuštěná data v úzkých oknech času nebo zhuštěných prvků v širších oknech. Nemusí se dobře škálovat na hustá data nad širším časovým obdobím, jak je vidět v PdM scénářích.
- Pokud není k dispozici dávkové bodování, řešení je přizpůsobování online bodování pro zpracování nových dat v malých dávkách najednou.

## <a name="solution-templates-for-predictive-maintenance"></a>Šablony řešení pro prediktivní údržbu

Poslední část tohoto průvodce poskytuje seznam šablon řešení PdM, kurzů a experimentů implementovaných v Azure. Tyto aplikace PdM můžou být v některých případech nasazené do předplatného Azure během několika minut. Dají se použít jako ukázky pro vyzkoušení konceptu, izolované prostory pro experimentování s alternativami nebo akcelerátory pro vlastní výrobní implementace. Tyto šablony se nacházejí v [Azure AI Gallery](https://gallery.azure.ai) nebo na [GitHubu Azure](https://github.com/Azure). Tyto různé ukázky budou v průběhu času zahrnuty do této šablony řešení.

| # | Nadpis | Popis |
|--:|:------|-------------|
| 2 | [Šablona řešení prediktivní údržby Azure](https://github.com/Azure/AI-PredictiveMaintenance) | Open Source šablona řešení, která předvádí modelování Azure ML a kompletní infrastrukturu Azure s podporou scénářů prediktivní údržby v kontextu vzdáleného monitorování IoT. |
| 3 | [Hloubkové učení pro prediktivní údržbu](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance) | Poznámkový blok Azure s ukázkovým řešením použití LSTM (Long Short-Term paměti) (třída opakujících se sítí neuronové) pro prediktivní údržbu s [blogovým příspěvkem k této ukázce](https://azure.microsoft.com/blog/deep-learning-for-predictive-maintenance).|
| 4 | [Prediktivní údržba Azure pro Aerospace](https://gallery.azure.ai/Solution/Predictive-Maintenance-for-Aerospace-1) | Jedna z prvních šablon řešení PdM založená na Azure ML v 1.0 pro údržbu letadel. Tato příručka pochází z tohoto projektu. |
| 5 | [Sada nástrojů Azure AI pro IoT Edge](https://github.com/Azure/ai-toolkit-iot-edge) | AI v IoT Edge pomocí TensorFlow; sada Toolkit balí modely hloubkového učení v kontejnerech Docker kompatibilních s Azure IoT Edge a zpřístupňuje tyto modely jako rozhraní REST API.
| 6 | [Prediktivní údržba Azure IoT](https://github.com/Azure/azure-iot-predictive-maintenance) | Azure IoT Suite počítače – předkonfigurované řešení. PdM šablona pro údržbu letadla pomocí IoT Suite. [Jiný dokument](../../iot-accelerators/quickstart-predictive-maintenance-deploy.md) a [návod](../../iot-accelerators/iot-accelerators-predictive-walkthrough.md) vztahující se ke stejnému projektu. |
| 7 | [Šablona prediktivní údržby pomocí SQL Server R Services](https://gallery.azure.ai/Tutorial/Predictive-Maintenance-Template-with-SQL-Server-R-Services-1) | Ukázka zbývajícího scénáře životního cyklu na základě služeb jazyka R. |
| 8 | [Průvodce modelováním prediktivní údržby](https://gallery.azure.ai/Collection/Predictive-Maintenance-Modelling-Guide-1) | Funkce sady datová sada pro správu letadel, která je navržená pomocí jazyka R s [experimenty](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Modelling-Guide-Experiment-1) a [datovými sadami](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Modelling-Guide-Data-Sets-1) a notebookem a [experimenty](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Step-1-of-3-data-preparation-and-feature-engineering-2) Azure v AzureML|

## <a name="training-resources-for-predictive-maintenance"></a>Školicí materiály pro prediktivní údržbu

Microsoft Azure nabízí výukové postupy pro základní koncepty za techniky PdM, kromě obsahu a školení o obecných konceptech a zvyklostech AI.

| Školicí prostředek  | Dostupnost |
|:-------------------|--------------|
| [Výuková cesta pro PdM s využitím stromů a náhodné doménové struktury](https://aischool.microsoft.com/learning-paths/1H5vH5wAYcAy88CoQWQcA8) | Veřejná | 
| [Výuková cesta pro PdM s využitím hloubkového učení](https://aischool.microsoft.com/learning-paths/FSIXxYkOGcauo0eUO8qAS) | Veřejná |
| [Pro vývojáře AI v Azure](https://azure.microsoft.com/training/learning-paths/azure-ai-developer) | Veřejná |
| [Microsoft AI School](https://aischool.microsoft.com/learning-paths) | Veřejná |
| [Učení Azure AI z GitHubu](https://github.com/Azure/connectthedots/blob/master/readme.md) | Veřejná |
| [LinkedIn Learning](https://www.linkedin.com/learning) | Veřejná |
| [Microsoft AI YouTube webináře](https://www.youtube.com/watch?v=NvrH7_KKzoM&t=4s) | Veřejná |
| [Microsoft AI – zobrazení](https://channel9.msdn.com/Shows/AI-Show) | Veřejná |
| [LearnAI@MS](https://learnanalytics.microsoft.com) | Partneři |
| [Microsoft Partner Network](https://partner.microsoft.com/training/training-center) | Partneři |

Kromě toho bezplatné Course (rozsáhlé otevřené online kurzy) v AI jsou nabízeny online pomocí akademických institucí, jako je Stanfordu a MIT, a dalších vzdělávacích společností.