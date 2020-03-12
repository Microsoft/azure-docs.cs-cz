---
title: Průvodce Azure AI pro řešení prediktivní údržby – vědecké zpracování týmových dat
description: Úplný popis pro datové vědy, která je základem řešení prediktivní údržby v několika vertikální obory.
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
ms.openlocfilehash: 5cf3f02284777a54a9d26cad8a7f3b5b4fa6b335
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2020
ms.locfileid: "79087767"
---
# <a name="azure-ai-guide-for-predictive-maintenance-solutions"></a>Průvodce Azure AI pro řešení prediktivní údržby

## <a name="summary"></a>Souhrn

Prediktivní Údržba (**PDM**) je oblíbená aplikace prediktivní analýzy, která může pomáhat podnikům v několika odvětvích dosahovat vysokého využití prostředků a úspory za provozní náklady. Tato příručka spojuje obchodní a analytické pokyny a osvědčené postupy pro úspěšné vývoj a nasazování řešení PdM s využitím technologie [platformy Microsoft Azure AI](https://azure.microsoft.com/overview/ai-platform) .

Tato příručka představuje začínáte, specifických pro dané odvětví obchodních scénářů a proces kvalifikace scénářů pro PdM. Požadavky na data a modelovacích postupů, které umožňují vytvářet řešení PdM jsou také k dispozici. Hlavním obsahem tohoto průvodce je na vědecké zpracování dat – včetně kroky pro přípravu dat, vytváření funkcí, vytvoření modelu a operacionalizace modelu. Doplnit tyto klíčové koncepty, tento průvodce popisuje sadu šablon řešení vám pomohou zrychlit vývoj aplikací PdM. Průvodce také odkazuje na užitečné studijní materiály pro profesionální získat další informace o AI za nástrojem pro datové vědy. 

### <a name="data-science-guide-overview-and-target-audience"></a>Data Science Průvodce přehled a cíl cílové skupiny
V první polovině roku Tato příručka popisuje typické obchodních problémů, přínosech implementace PdM k řešení těchto problémů a uvádí některé běžné případy použití. Pracovníci s rozhodovací pravomocí (BDMs) budou těžit z tohoto obsahu. V druhé polovině vysvětluje pro datovou vědu za PdM a poskytuje seznam PdM řešení sestavená pomocí zásad popsané v tomto průvodci. Obsahuje také postupy výuky a odkazy na školicí materiály. Technický pracovník s rozhodovací pravomocí (TDMs) bude užitečné tohoto obsahu.

| Začněte s... | Pokud jste... |
|:---------------|:---------------|
| [Obchodní případ pro prediktivní údržbu](#business-case-for-predictive-maintenance) |Organizační pracovník s rozhodovací pravomocí (BDM) snížit prostoje a provozních nákladů a zvýšení využití zařízení |
| [Datové vědy pro prediktivní údržbu](#data-science-for-predictive-maintenance) |technický pracovník s rozhodovací pravomocí (Pracovník) vyhodnocuje nové technologie PdM vám pomohou pochopit jedinečný zpracování dat a AI požadavky pro prediktivní údržbu |
| [Šablony řešení pro prediktivní údržbu](#solution-templates-for-predictive-maintenance)|Vývojář AI chtějí rychle zprovozněte ukázku nebo testování konceptu a softwarový architekt |
| [Školicí materiály pro prediktivní údržbu](#training-resources-for-predictive-maintenance) | některé nebo všechny z výše uvedeného a chcete se dozvědět, základní koncepty za nástrojem pro datové vědy, nástroje a techniky.

### <a name="prerequisite-knowledge"></a>Předpokládané znalosti
Obsah BDM neočekává čtečka, která má mít žádnou znalost předchozí datové vědy. Pracovník obsah je užitečné základní znalosti o statistiky a datové vědy. Doporučuje se znalostí dat do Azure a služeb AI, Python, R, XML a JSON. Techniky AI jsou implementovány v Pythonu a r. balíčky. Šablony řešení jsou implementovány pomocí služby Azure, vývojové nástroje a sady SDK.

## <a name="business-case-for-predictive-maintenance"></a>Obchodní případ pro prediktivní údržbu

Podniky vyžadují důležité zařízení běží na efektivitu ve špičce a využití, jak začít využívat jejich návratu na kapitálových investic. Tyto prostředky může sahat od leteckých motorů, turbíny, výtahů nebo průmyslové dochlazovače – které nákladů milionů – na každý den zařízení jako kopírkách, kávy počítače nebo chladiče vody.
- Ve výchozím nastavení většina firem spoléhá na _opravnou údržbu_, kde se součásti nahrazují jako a při selhání. Nápravné Údržba zaručuje části se používají zcela (tedy ne plýtvání komponenty životnost), ale náklady obchodní výpadky, práci a neplánovanou údržbu požadavky (mimo hodin nebo nevhodné umístění).
- Na další úrovni podniky postupuje při _preventivní údržbě_, kde určují vhodný životnost pro součást a udržují nebo nahrazují před selháním. Preventivní údržba zabraňuje neplánovaným a katastrofální chyby. Ale vysoké náklady na plánované výpadky, využití komponenty během své užitečné životnosti, a stále ještě zůstává práce.
- Cílem _prediktivní údržby_ je optimalizovat rovnováhu mezi opravnou a preventivní údržbou tím, že _v čase_ zapnete náhradní součásti. Tento přístup pouze nahradí tyto součásti, když se nachází blízko k chybě. Tím, že rozšíří lifespans součásti (ve srovnání se preventivní údržba) a snížení neplánovanou údržbu a náklady na práci (přes opravné údržby), firmy získají úspory nákladů a konkurenční výhody.

## <a name="business-problems-in-pdm"></a>Obchodní problémy PdM
Firmám čelí vysokou provozní rizika z důvodu neočekávaných chyb a mají omezené pohled na původní příčinu možných problémů v komplexních systémů. Zde jsou některé na klíčové otázky firmy:

- Zjišťovat anomálie v zařízení nebo systém výkonu nebo funkce.
- Předpověď, jestli určitý prostředek může selhat v blízké budoucnosti.
- Odhad zbývající životnost prostředku.
- Identifikujte hlavní příčiny selhání prostředku.
- Určete, jaké akce údržby je potřeba udělat, pokud se na prostředek.

Typické cílem příkazy z PdM jsou:

- Snižte provozní riziko zásadně důležité zařízení.
- Predikce selhání dříve, než nastanou zvýšit míru výnosnosti na prostředky.
- Tím, že operace údržby just-in-time řídit náklady na údržbu.
- Snížit otěr zákazníků, zvyšují image značky a ke ztrátě prodeje.
- Nižší náklady na inventář díky snížení zásob predikce bod pro změnu uspořádání.
- Zjistěte vzorky, které jsou připojené k různým problémům údržby.
- Zadejte klíčové ukazatele výkonu (klíčových ukazatelů výkonu) jako je například stav skóre asset podmínky.
- Odhad zbývající životnost prostředků.
- Doporučujeme, abyste včas údržby.
- Povolte jenom v inventáři čas odhadem data objednávky pro náhradní díly.

Tyto příkazy cíle jsou počáteční body pro:

- _vědečtí data_ , která umožňují analyzovat a řešit konkrétní prediktivní problémy.
- _cloudové architekty a vývojáři, kteří_ dohromady dovedou k koncovému řešení.

## <a name="qualifying-problems-for-predictive-maintenance"></a>Kvalifikace problémy související s prediktivní údržby
Je důležité zdůraznit, že všechny případy použití nebo možné PdM efektivně vyřešit obchodní problémy. Existují tři důležité kvalifikační kritéria, které je třeba zvážit při výběru problém:

- Problém je potřeba prediktivní ze své podstaty; To znamená, měla by existovat cíl nebo jako výsledek předpovědět. Problém by měl mít také jasný akce, aby se zabránilo chybám při jejich zjištění.
- Problém by měl obsahovat záznam o provozní historii zařízení, které obsahuje _dobrý i špatný výsledek_. Sadu akcí provedených ke zmírnění chybné výsledky by měla být k dispozici jako součást tyto záznamy. Zprávy o chybách, údržba protokoly snížení výkonu, opravit a nahradit protokoly jsou také důležité. Kromě toho jsou opravy prováděnou ke zlepšení jejich nahrazení záznamy a také užitečné.
- Zaznamenaná historie by se měla projevit v _relevantních_ údajích, které _jsou dostatečně kvalitní_ , aby podporovaly případ použití. Další informace o relevanci dat a dostatečnosti najdete v tématu [požadavky na data pro prediktivní údržbu](#data-requirements-for-predictive-maintenance).
- Nakonec firmy by měl mít odborníci, kteří mají pochopili, že problém. Jejich by měl být vědomi interních procesů a postupů, které nelze vyřešit analytik, pochopit a interpretovat data. Musí být také možnost provádět potřebné změny existujících firemních procesů do pomůže shromažďovat ta správná data pro problémy, v případě potřeby.

## <a name="sample-pdm-use-cases"></a>Vzorové případy použití PdM
Tato část se zaměřuje na kolekci PdM případy použití v několika oborech, třeba leteckém průmyslu, nástroje a doprava. Každá část začíná obchodních problémů a vás seznámí s výhodami PdM, relevantní data okolo obchodním problému a nakonec výhody PdM řešení.

| Obchodního problému | Výhody PdM |
|:-----------------|-------------------|
|**Bezpečnostních**      |                   |
|_Zpoždění letu a zrušení_ v důsledku mechanických problémů. Chyby, které nelze opravit v čase může způsobit, že lety budou zrušeny a narušit plánování a provoz. |PdM řešení můžete předvídá pravděpodobnost letadla se zpozdila nebo zrušená kvůli mechanickým závadám.|
|_Selhání částí motoru_letadla: náhrady součástí leteckého motoru patří mezi nejběžnější úlohy údržby v rámci leteckého průmyslu. Údržba řešení vyžadují pečlivé správu uložených dostupnost součásti, doručování a plánování|Schopnost shromáždění informací o spolehlivosti komponenty vede k podstatné omezení objemu na investiční náklady.|
|**Financování** |                         |
|_Selhání ATM_ je běžný problém v rámci bankovního odvětví. Problémem je pravděpodobnost, že transakci ATM hotovosti stažení dojde k přerušení kvůli chybě dokumentu zaseknutý nebo její část v hotovosti řadič sestavy. Podle predikcí selhání transakce, Network lze udržovat proaktivně zabránit výskytu chyby.| Místo umožňují počítači nezdaří polovině transakce, žádoucí alternativou je program počítače k odepření služby podle do predikce.|
|**Konfiguruje** |                          |
|_Chyby větrné turbíny_: větrné turbíny jsou hlavním zdrojem energie v rámci životně zodpovědných zemí nebo oblastí v životním prostředí a zahrnují velké náklady. Klíčovou komponentou v větrných turbínách je generátor motoru, jehož selhání vykresluje turbínu neúčinným. Je také velmi nákladné opravit.|Předpověď klíčové ukazatele výkonu, jako je například MTTF (průměrný čas potřebný k selhání) může pomoct energetickým společnostem turbíny selhání a zajistit minimálními prostoji. Pravděpodobnosti selhání bude informovat rozvržení služeb techniků pro monitorování turbíny, které můžou brzy dojde k selhání a naplánovat režimy založeného na čase údržby. Prediktivní modely poskytují přehled o různých faktorů, které přispívají k selhání, která pomáhá techniků lépe pochopili původní příčiny problémů.|
|_Selhání přepínacího okruhu_: distribuce elektřiny na domy a firmy vyžaduje, aby elektrické napájení byly na všech místech v provozu, aby bylo zaručeno poskytování energie. Jističe pomůžou omezit nebo vyloučit poškození power řádků při přetížení nebo negativní weather podmínky. Obchodní problémem je Předvídejte selhání jističe.| Řešení PdM pomoct snížit náklady na opravu a zvýšit životnost vybavení, jako jsou jističe. Pomáhají zlepšovat kvalitu výkon sítě snížením neočekávaných chyb a přerušení služeb.|
|**Přeprava a logistika** |    |
|_Selhání dvířek pro výtah_: velké společnosti pro výtah poskytují úplnou službu Stack pro miliony funkčních výtahů po celém světě. Výtah bezpečnost, spolehlivost a dostupnost jsou hlavních cílů pro své zákazníky. Těmto společnostem sledovat tyto a různých dalších atributů prostřednictvím snímačů, abychom jim opravné i preventivní údržby. V hodnocení nejvýraznější potíže zákazníka nefunguje dveře hodnocení. V tomto případě je poskytnout prediktivní aplikace znalostní báze, který bude předpovídat, že způsobí, že riziko chyb dveře obchodního problému.| Výtahy jsou investiční pro potenciálně životnost 20 – 30 rok. Proto může být vysoce konkurenčním prostředí; každý potenciální prodej Proto mají vysoká očekávání pro servis a podporu. Prediktivní údržby může poskytnout tyto společnosti výhodu oproti konkurenci ve svých produktech a nabídek služeb.|
|_Selhání kolečka_: účet selhání kolečka pro polovinu všech vykolejí vlaku a nákladových miliard do celosvětového železničního odvětví. Selhání kolečko také způsobit rails zhoršovat, což občas způsobí lišty přerušení předčasně ukončen. Zalomení lišty vést k katastrofickými událostmi, jako je například bezpečnosti proti vykolejení. Aby tyto instance železniční sledování výkonu kola a nahradíte je preventivní způsobem. Obchodní problémem je predikce selhání kolečka.| Prediktivní Údržba kol vám pomůže s just-in-time nahrazení souborů Wheel |
|_Selhání dveří Subway vlaku_: zásadním důvodem pro prodlevy při operacích Subway jsou chyby dveří vlakových automobilů. Obchodní problémem je Předvídejte selhání dveře trénování.|Časná povědomí o selhání dveře nebo počet dní do selhání dveře vám pomůže optimalizovat obchodní trénování dveře plány údržby.|

V další části se dostane do podrobnosti o tom, jak začít využívat výhody PdM bylo uvedeno výše.

## <a name="data-science-for-predictive-maintenance"></a>Vědecké zpracování dat pro prediktivní údržbu

Tato část obsahuje obecné pokyny pro datové vědy zásady a postupy pro PdM. Účelem je pomoct Pracovník, architekt řešení, nebo vývojář pochopit požadavky a proces tvorby aplikace AI začátku do konce pro PdM. Tuto část si můžete přečíst spolu s přehledem ukázek a šablon pro podávání konceptů, které jsou uvedené v [šablonách řešení pro prediktivní údržbu](#solution-templates-for-predictive-maintenance). Pak můžete tyto zásady a osvědčené postupy pro implementaci PdM řešení v Azure.

> [!NOTE]
> Tato příručka určená není naučit čtečky pro datové vědy. K dispozici je několik užitečných zdrojů pro další čtení v části pro [školení prostředků pro prediktivní údržbu](#training-resources-for-predictive-maintenance). [Šablony řešení](#solution-templates-for-predictive-maintenance) uvedené v průvodci ukazují některé z těchto postupů AI na konkrétní PDM problémy.

## <a name="data-requirements-for-predictive-maintenance"></a>Požadavky na data pro účely prediktivní údržby

Úspěch libovolného závisí na (a) kvalitu co museli a (b) možnost learner. Prediktivní modely další vzory z historických dat a předpovídá budoucí výsledky s určitou pravděpodobností na základě těchto zjištěnou způsobů. Do modelu prediktivní přesnost závisí na relevanci, dostatečné pokrytí a kvalitu dat trénování a testování. Nová data, která je "skóre: pomocí tohoto modelu by měl mít stejné funkce a schéma jako data trénování a testování. Funkce vlastností (typ, hustota, distribuce a tak dále) nová data by měla odpovídat hodnotě datové sady pro trénování a testování. Sada v této části je zaměřena na tyto požadavky na data.

### <a name="relevant-data"></a>Související data

Nejprve musí být data _relevantní pro daný problém_. Vezměte v úvahu případ použití _při selhání kolečka_ popsané výše – školicí data by měla obsahovat funkce týkající se operací kolečka. Pokud by byl problém předpovídat selhání _trakčního systému_, musí školicí data zahrnovat všechny různé komponenty trakčního systému. Vzhledem k tomu, zaměřuje na druhý případ selhání podsystému větší, zaměřuje prvním případě konkrétní součást. Obecné doporučení je k navrhování systémů předpovědi o konkrétní součásti, nikoli větší subsystémy, protože ten bude mít více jsou daleko od sebe data. Odborník na doménu (viz [opravňující problémy pro prediktivní údržbu](#qualifying-problems-for-predictive-maintenance)) by měl pomáhat při výběru nejdůležitějších podmnožin dat pro analýzu. Relevantní zdroje dat jsou podrobněji popsány v článku [Příprava dat pro prediktivní údržbu](#data-preparation-for-predictive-maintenance).

### <a name="sufficient-data"></a>Dostatečným množstvím dat
S ohledem na data historie selhání jsou nejčastější dvě otázky: (1) "počet událostí selhání je potřeba trénování modelu?" (2) "kolik záznamů je považováno za" dostačující "? Neexistují žádné definitivní odpovědi, ale pouze pravidla pro palec. (1) větší počet událostí selhání, lepší modelu. (2) a přesný počet událostí selhání závisí na data a kontext problém je vyřešen. Ale na druhou stranu, pokud se počítači nezdaří příliš často podniku se nahradit ho, což sníží selhání instance. Sem znovu pokynů od expertní domény je důležité. Existují však metody, které je možné vypořádat s problémem _vzácných událostí_. Jsou popsány v oddílu [zpracování nevyvážených dat](#handling-imbalanced-data).

### <a name="quality-data"></a>Kvalita dat
Kvalita dat je Kritická – každá hodnota atributu prediktivního atributu musí být ve spojení s hodnotou cílové proměnné _přesné_ . Kvalita dat je dobře sledované oblast v statistiky a data správy a proto out oboru této příručce.

> [!NOTE]
> Existuje několik prostředků a produkty enterprise k zajištění kvality dat. Ukázka odkazy jsou uvedeny níže:
> - Dasu, T, Johnsonem, dolování dat T., průzkumné a čištění dat, Wiley, 2003.
> - [Analýza průzkumnéch dat, Wikipedii](https://en.wikipedia.org/wiki/Exploratory_data_analysis)
> - [Hellerstein, a kvantitativní čištění dat pro velké databáze](http://db.cs.berkeley.edu/jmh/papers/cleaning-unece.pdf)
> - [de Jonge, E, Van der Loo, M, Úvod k čištění dat pomocí jazyka R](https://cran.r-project.org/doc/contrib/de_Jonge+van_der_Loo-Introduction_to_data_cleaning_with_R.pdf)

## <a name="data-preparation-for-predictive-maintenance"></a>Příprava dat pro prediktivní údržbu

### <a name="data-sources"></a>Zdroje dat

Příslušné zdroje dat pro prediktivní údržbu patří, ale nejsou omezené na:
- Historie chyb
- Údržba a opravy historie
- Počítač provozních podmínek
- Metadata zařízení

#### <a name="failure-history"></a>Historie chyb
Události chyb se vyskytují jen vzácně v aplikacích PdM. Ale při vytváření prediktivní modely, algoritmus potřebuje další informace o normální provozní model komponenty a vzory jeho selhání. Takže trénovacích dat by měl obsahovat dostatečný počet příklady z obou kategoriích. Historie nahrazení záznamy a části údržby jsou dobré zdroje k vyhledání události chyb. Díky pomoci určitá znalost domény je anomálie v datech školení také definovat jako selhání.

#### <a name="maintenancerepair-history"></a>Údržba a opravy historie
Historie údržby assetu obsahuje podrobnosti o nahrazených součástech, provedených opravných aktivitách atd. Tyto události zaznamenávají vzory snížení úrovně. Neexistence tato zásadní informace v datech školení může vést k zavádějící výsledky modelu. Historie chyb také dostupné v rámci údržby historie jako speciální chybové kódy, nebo data objednávky pro části. Další zdroje dat, které ovlivňují vzory selhání by měl prozkoumat a poskytované odborníky na domény.

#### <a name="machine-operating-conditions"></a>Počítač provozních podmínek
Streamování dat snímačů závislosti (nebo jiné) zařízení v operaci je důležitá data source. Klíčovým předpokladem v PdM je, že stav počítače se během běžné operace degraduje v průběhu času. Očekává se, že data obsahovat časově proměnlivých funkce, které zaznamenávají tento model stárnoucích a anomálie, které vede ke snížení. Dočasné aspekt dat se vyžaduje pro algoritmus další chyby a selhání vzory v čase. Podle těchto datových bodů, algoritmus učí předpovědět, kolik jednotek více času na počítači můžete pokračovat v práci předtím, než selže.

#### <a name="static-feature-data"></a>Data statické funkce
Statické funkce jsou metadata o zařízení. Mezi příklady patří zařízení značka, model, manufactured datum, start date služby, umístění v systému a dalších technických specifikací.

Příklady relevantních dat pro [případy použití Sample PDM](#sample-pdm-use-cases) jsou následující:

| Případ použití | Příklady související data |
|:---------|---------------------------|
|_Zpoždění letů a zrušení_ | Informace o postupu let ve formě ramena letu a stránky protokolů. Data o vzjezdové nožkě zahrnují podrobnosti o směrování, jako je datum odchodu, čas, letiště, layovers atd. Protokol stránky obsahuje řadu chybových kódů a kódů údržby zaznamenaných pracovníky údržby provozu.|
|_Selhání částí motoru letadla_ | Shromažďovat data ze senzorů v letadlech obsahující informace o stavu různých částí. Údržba záznamy pomoct identifikovat, kdy došlo k selhání součásti, a pokud byly nahrazeny.|
|_Chyba ATM_ | Údajů snímačů přes pro každou transakci (uložení hotovosti/vrácení) a o předepisování hotovosti. Informace o měření mezer mezi poznámkami, tloušťkou poznámky, vzdáleností doručení, kontrolu atributů atd. Záznamy údržby, které poskytují kódy chyb, informace o opravě, čas posledního vyplnění platebního držáku.|
|_Selhání větrné turbíny_ | Senzory sledují podmínky turbín, jako je teplota, směr větru, vygenerovaná energie, rychlost generátoru atd. Data se shromažďují z několika větrných turbín z větrných farem v různých oblastech. Každý turbíny se obvykle mít více údajů snímačů přes předávání měření v daném časovém intervalu.|
|_Selhání přerušení okruhů_ | Údržba protokoly, které zahrnují akce opravné, preventivní a systematicky. Provozní data, která zahrnuje automatické a ruční příkazy, odeslané do jističe, jako pro otevření a zavření akce. Metadata zařízení, jako je datum výroby, umístění, model atd. Specifikace pro přerušení okruhů, jako jsou úrovně napětí, geografická poloha, okolní podmínky.|
|_Selhání dvířek pro výtahy_| Výtah metadat – například typu hodnocení, manufactured datum, frekvence údržby, typ sestavení a tak dále. Provozní informace, jako je počet cyklů dveří, dveře průměrný čas uzavření. Historie chyb s příčiny.|
|_Selhání kolečka_ | Data senzorů, která měří akceleraci kolečka, brzdové instance, hnací dráhy, rychlosti atd. Statické informace o kolech, jako je výrobce, datum výroby. Data selhání odvodit z část pořadí databáze, které sledují data objednávky a množství.|
|_Selhání dveří Subway vlaku_ | Otevření dveří a pravou časy, ostatní provozní data, jako jsou aktuální podmínky dveře trénování. Statická data bude zahrnovat identifikátor prostředku, čas a sloupce s hodnotami podmínky.|

### <a name="data-types"></a>Typy dat
S ohledem výše uvedené zdroje dat, jsou dvě hlavní datové typy dodržovat v doméně PdM:

- _Dočasná data_: provozní telemetrie, podmínky počítače, typy pracovních objednávek, kódy priorit, které budou mít časová razítka v době nahrávání. Selhání, Údržba a opravy a historie využití bude také mít časová razítka spojené s každou událost.
- _Statická data_: funkce počítačů a funkce operátoru jsou obecně statické, protože popisují technické specifikace počítačů nebo atributů operátorů. Pokud tyto funkce může v průběhu času měnit, by měly mít časové razítko k nim má přiřazené.

Prediktivní a cílové proměnné by se měly předzpracovat/transformovat na [číselné, kategorií a další datové typy](https://www.statsdirect.com/help/basics/measurement_scales.htm) v závislosti na použitém algoritmu.

### <a name="data-preprocessing"></a>Předzpracování dat
Jako součást pro _strojírenství funkcí_Připravte data z různých datových proudů, abyste mohli vytvořit schéma, ze kterého je možné snadno vytvářet funkce. Nejprve Vizualizujte data jako tabulku záznamů. Každý řádek v tabulce představuje instanci školení a sloupce představují funkce _prediktivních_ funkcí (označované také jako nezávislé atributy nebo proměnné). Uspořádejte data tak, aby poslední sloupce byly _cílem_ (závislá proměnná). Pro každou instanci školení přiřaďte _popisek_ jako hodnotu tohoto sloupce.

Pro dočasná data rozdělte duration s daty ze snímačů časové jednotky. Každý záznam by měl patřit do jednotky času pro určitý prostředek _a měl by nabízet odlišné informace_. Časové jednotky jsou definovány v závislosti na obchodních potřebách v násobcích sekund, minut, hodin, dnů, měsíců, a tak dále. Časová jednotka nemusí _být stejná jako frekvence shromažďování dat_. Pokud je vysoká frekvence, data nemusí zobrazit všechny značný rozdíl z jedné jednotky na druhý. Předpokládejme například, že teplota shromáždění každých 10 sekund. Pomocí tom samém intervalu pro trénovací data jenom tento počet příklady bez zadání jakýchkoli dalších informací. V takovém případě může být lepší strategie použití průměr dat více než 10 minut nebo hodiny podle obchodní odůvodnění.

Pro statická data,
- _Záznamy údržby_: nezpracovaná data údržby obsahují identifikátor assetu a časové razítko s informacemi o aktivitách údržby, které byly provedeny v daném časovém okamžiku. Transformuje aktivity údržby do _kategorií_ sloupců, kde každý popisovač kategorie jednoznačně mapuje na určitou akci údržby. Schéma pro záznamy údržby by obsahovat identifikátor prostředku, čas a akce údržby.

- _Záznamy o selhání_: chyby nebo příčiny selhání lze zaznamenat jako konkrétní chybové kódy nebo události selhání definované konkrétními obchodními podmínkami. V případech, kdy zařízení má více kódů chyb, odborné domény by měl vystihovat těch, které se vztahují k Cílová proměnná. Zbývající kódy chyb nebo podmínky použijte k vytvoření _prediktivních_ funkcí, které korelují s těmito chybami. Schéma pro selhání záznamů bude zahrnovat identifikátor prostředku, selhání a čas nebo důvod selhání – Pokud je k dispozici.

- _Metadata počítače a operátora_: sloučí data počítače a operátoru do jednoho schématu a přidružte tak Asset k operátorovi spolu s příslušnými atributy. Schéma pro počítač podmínky by být identifikátor prostředku, funkce asset, operátor identifikátoru a funkce operátoru.

Mezi další kroky předzpracování dat patří _zpracování chybějících hodnot_ a _normalizace_ hodnot atributů. Podrobný rozbor je mimo rámec této příručky - naleznete v části Další odkazy na některé užitečné.

Pomocí výše uvedeného předzpracovaná zdroje dat na místě, finální transformace před vytváření funkcí je k výše uvedeným tabulkám založené na identifikátor prostředku a časové razítko. Výsledná tabulka by obsahovat hodnoty null ve sloupci selhání, když je počítač v běžném provozu. Tyto hodnoty null lze přisoudit podle indikátor pro běžné operace. Pomocí tohoto sloupce selhání můžete vytvořit _popisky pro prediktivní model_. Další informace najdete v části [techniky modelování pro prediktivní údržbu](#modeling-techniques-for-predictive-maintenance).

## <a name="feature-engineering"></a>Návrh funkcí
Vytváření funkcí je první krok před modelování data. Její role v rámci vědeckého zpracování dat [je popsána zde](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/create-features). _Funkce_ je prediktivní atribut pro model – například teplota, tlak, vibrace a tak dále. Pro PdM zahrnuje inženýr funkcí abstrakci stavu počítače nad historickými daty shromážděnými během určité doby trvání. V tomto smyslu se liší od jeho partnerů, jako je vzdálené monitorování, detekce anomálií a detekce selhání. 

### <a name="time-windows"></a>Čas windows
Vzdálené monitorování zahrnuje hlášení událostí, ke kterým dochází _v čase_. Modely detekce anomálií vyhodnotit příchozí datové proudy (skóre) dat do příznak anomálie v době body v čase. Detekce chyb klasifikuje selhání jako konkrétní typy při jejich výskytu body v čase. Na rozdíl od toho PdM zahrnuje předpověď chyb v _budoucím časovém období_, a to na základě funkcí, které reprezentují chování počítače v _historických časových obdobích_. Pro PdM jsou příliš Rušený. Chcete-li být prediktivní funkce údaje z jednotlivých bodů čas. Data pro jednotlivé funkce proto musí být _smoothened_ agregací datových bodů v čase okna.

### <a name="lag-features"></a>Funkce Lag
Obchodní požadavky definují, jak daleko modelu má k předvídání budoucí. Pak tato doba pomáhá definovat "jak daleko zpět model se vás pod rouškou" aby tyto předpovědi. Toto období "vyhledávání zpět" se nazývá _Prodleva_a funkce, které se v průběhu tohoto období zpoždění procházejí, se nazývají _funkce pro prodlevu_. Tato část popisuje funkce lag, které lze zkonstruovat z datových zdrojů s časová razítka a také vytváří funkci ze statických datových zdrojů. Funkce pro prodlevu jsou obvykle _číselné_ povahy.

> [!IMPORTANT]
> Velikost okna je určen pomocí služby experimentování ve službě a musí dokončit díky expertní domény. Pro výběr a definice funkce lag, jejich agregace a typ systému windows obsahuje stejné výstrahou.

#### <a name="rolling-aggregates"></a>Agregace se zajištěním provozu
Pro každý záznam prostředku postupné okno velikosti "W" vybrána jako počet časových jednotek pro výpočet agregací. Funkce lag se pak vypočítávají s použitím období W _před datem_ záznamu. Na obrázku 1 modré čáry zobrazit hodnoty čidel zaznamenaných pro určitý prostředek pro každou jednotku času. Souhrnný průměr hodnot funkce, označení za období velikost W = 3. Klouzavý průměr se vypočítává na všech záznamech s časovými razítky v rozsahu t<sub>1</sub> (oranžová) na t<sub>2</sub> (zeleně). Hodnota W je obvykle v minut nebo hodin v závislosti na povaze data. Ale pro některé problémy, výběr velké W (třeba 12 měsíců) poskytují celou historii prostředek až do okamžiku záznamu.

![Obrázek 1. Agregační funkce se zajištěním provozu](./media/predictive-maintenance-playbook/rolling-aggregate-features.png)

Obrázek 1. Agregační funkce se zajištěním provozu

Příklady v časovém intervalu se zajištěním provozu agregace jsou počet, průměr, míry CUMESUM (kumulativní součet), minimální/maximální hodnoty. Kromě toho odchylky, směrodatná odchylka a počet odlehlé hodnoty nad rámec standardních odchylek N se často používají. Příklady agregací, které mohou být použity pro [případy použití](#sample-pdm-use-cases) v tomto průvodci, jsou uvedeny níže. 
- _Zpoždění letu_: počet chybových kódů za poslední den/týden.
- _Selhání součásti leteckého motoru_: válcování znamená, směrodatná odchylka a součet za poslední den, týden atd. Tato metrika by měla být určena společně s odborníkem na obchodní doménu.
- _Chyby ATM_: valení, medián, rozsah, směrodatné odchylky, počet podrozsahů mimo tři směrodatné odchylky, horní a dolní CUMESUM.
- _Selhání dveří Subway vlaku_: počet událostí za poslední den, týden, dva týdny atd.
- _Selhání dělení na okruhy_: počet selhání za minulý týden, rok, tři roky atd.

Další užitečné technikou v PdM je zaznamenat změny trendů, provozní špičky a změny na úrovni pomocí algoritmů, které zjišťovat anomálie v datech.

#### <a name="tumbling-aggregates"></a>Aktivační událost pro přeskakující agregace
U každého označeného záznamu prostředku je definováno okno velikosti _w-<sub>k</sub>_  , kde _k_ je počet oken velikosti _w_. Agregace se pak vytvoří přes _k_ _bubnu Windows_ _w-k, w-<sub>(n-1)</sub>,..., w-<sub>2</sub>, w-<sub>1</sub>_  pro období před časovým razítkem záznamu. _k_ může být malé číslo pro zachycení krátkodobých účinků nebo velké číslo pro zachycení dlouhodobých vzorů snížení úrovně. (viz obrázek 2).

![Obrázek 2. Přeskakujícího agregační funkce](./media/predictive-maintenance-playbook/tumbling-aggregate-features.png)

Obrázek 2. Přeskakujícího agregační funkce

Například prodleva funkce pro případ použití větrné turbíny může být vytvořena pomocí W = 1 a k = 3. Tyto možnosti implikují prodleva pro každou z posledních tří měsíců používání horní a dolní odlehlé hodnoty.

### <a name="static-features"></a>Statické funkce

Technické specifikace zařízení, jako je například datum výroby, číslo modelu, umístění, je několik příkladů statická funkce. Jsou považovány za proměnné _kategorií_ pro modelování. Několik příkladů, pro případ použití jističe jsou napětí, aktuální, kapacita napájení, typu transformátoru a zdroji napájení. Selhání kolečko typu zadat souborů Wheel (legované vs oceli) je příklad.

Úsilí přípravy dat popsáno, pokud by měla vést k data jsou uspořádané, jak je znázorněno níže. Trénování, testování a ověřování dat by měly mít tento logický schéma (Tento příklad ukazuje čas v jednotkách, které dnů).

| ID assetu | Čas | Sloupce funkce \<> | Popisek |
| ---- | ---- | --- | --- |
| A123 |1 den | . . . | . |
| A123 |2\. den | . . . | . |
| Tlačítka ...  |Tlačítka ...   | . . . | . |
| B234 |1 den | . . . | . |
| B234 |2\. den | . . . | . |
| Tlačítka ...  |Tlačítka ...   | . . . | . |

Posledním krokem při strojírenství funkcí je **označení** cílové proměnné. Tento proces je závislý na techniku modelování. Zase techniku modelování závisí na obchodní problém a povaze dostupná data. Popisky jsou popsány v následující části.

> [!IMPORTANT]
> Příprava dat a vytváření funkcí jsou důležité jako modelování techniky můžete přejít na úspěšné PdM řešení. Expert domény a profesionální investovat spoustu času správné funkce a data modelu. Malým vzorkem z mnoho knih na vytváření funkcí jsou uvedeny níže:
> - Pyle, Příprava D. dat pro dolování (Nováková Kaufmann řady ve systémů pro správu dat), 1999 dat.
> - Casari Zheng A., vytváření funkcí a pro Machine Learning: zásady a postupy pro odborníky přes Data, O'Reilly, 2018.
> - Dong, G. Liu, H. (editory), strojírenství funkcí pro Machine Learning a analýzu dat (Chapman & data dolování/CRC a řada zjišťování znalostí), CRC Press, 2018.

## <a name="modeling-techniques-for-predictive-maintenance"></a>Modelování techniky pro prediktivní údržbu

Tato část popisuje hlavní modelovacích postupů PdM problémů, spolu s jejich metod vytváření konkrétního popisku. Všimněte si, že jeden modelování postup lze použít v různých oborech. Metoda modelování je spárovaná problém datové vědy, spíše než kontextu dat po ruce.

> [!IMPORTANT]
> Volba popisky pro tyto případy selhání a používání popisků strategie  
> byste měli určit ve spolupráci s odborné domény.

### <a name="binary-classification"></a>Binární klasifikace
Binární klasifikace se používá k _předpovědi pravděpodobnosti, že se část zařízení v budoucím časovém období, které_ se označuje jako _budoucí období horizontu X_, nezdařila. X se určuje podle obchodního problému a dat při konzultacích s odborníkem na doménu. Můžete například:
- _Minimální doba realizace_ nutná k nahrazení komponent, nasazení prostředků údržby, provedení údržby, aby se předešlo problému, který se může v daném období vyskytovat.
- _minimální počet událostí_ , ke kterým může dojít, než dojde k problému.

Při použití této techniky dva typy školení příklady jsou označeny. Kladný příklad, _který označuje selhání_s popiskem = 1. Záporná příkladu, který označuje normální provoz, s popiskem = 0. Cílová proměnná, a proto hodnoty popisku, jsou _kategorií_. Model byste identifikovat každý nový příklad se pravděpodobně nezdaří, nebo pracovat normálně příštích X časové jednotky.

#### <a name="label-construction-for-binary-classification"></a>Popisek konstrukce pro binární klasifikaci
Je tady na otázku: "co je pravděpodobnost, že prostředek se nezdaří v příštích X časových jednotkách?" Na tuto otázku, popisek X záznamů před selháním prostředek jako "přibližně na selhání" (label = 1) a označovat pomocí popisků všechny záznamy, jako je "normální" (Popisek = 0). (viz obrázek 3).

![Obrázek 3. Popisky pro binární klasifikaci](./media/predictive-maintenance-playbook/labelling-for-binary-classification.png)

Obrázek 3. Popisky pro binární klasifikaci

Níže jsou uvedeny příklady vytváření popisků strategie pro některé případy použití.
- _Zpoždění letu_: X se dá zvolit jako jeden den, aby se předpovídá zpoždění v příštích 24 hodinách. Potom všechny lety, které jsou během 24 hodin před selháním jsou označeny jako 1.
- _Selhání platebních plateb ATM_: cílem může být určení pravděpodobnosti selhání transakce v následující jedné hodině. V takovém případě všechny transakce, ke kterým došlo během poslední hodiny selhání jsou označeny jako 1. Předvídá pravděpodobnost selhání za další měny N poznámky distribuován, všechny poznámky distribuován v rámci poslední N poznámky selhání jsou označeny jako 1.
- _Selhání dělení na okruhy_: cílem může být předpovědět další selhání příkazu pro přerušení okruhu. V takovém případě X je vybrán jako budoucí jeden příkaz.
- _Selhání dveří vlaků_: X se dá zvolit jako dva dny.
- _Chyby větrné turbíny_: X se dá zvolit jako dva měsíce.

### <a name="regression-for-predictive-maintenance"></a>Regrese pro prediktivní údržbu
Regresní modely slouží k _výpočtu zbývající doby životnosti (RUL) assetu_. Zbývající doby životnosti je definován jako množství času, který prostředek je funkční, než dojde k následující chybě. Každý školicí příklad je záznam, který patří do časové jednotky _nY_ pro určitý prostředek, kde _n_ je násobek. Model by měl vypočítat RUL každého nového příkladu jako _průběžné číslo_. Toto číslo označuje dobu, po zbývající před selháním.

#### <a name="label-construction-for-regression"></a>Popisek konstrukce pro regresní
Je tady na otázku: "Jak se zbývající životnosti (RUL) zařízení?" Pro každý záznam před selháním vypočítejte tento popisek se počet jednotek času, než budou další selhání. V této metodě jsou popisky průběžné proměnné. (Viz obrázek 4)

![Obrázek 4. Vytváření popisků pro regresní](./media/predictive-maintenance-playbook/labelling-for-regression.png)

Obrázek 4. Vytváření popisků pro regresní

Pro regresní označování popisky se provádí s odkazem na bod selhání. Výpočet není možné nainstalovat bez mého, jak dlouho má vydrželi prostředku před selhání. Proto oproti binární klasifikace prostředky bez jakýchkoliv dat nelze použít pro modelování. Tento problém se nejlépe řeší jinou statistickou technikou, která se nazývá [analýza přežití](https://en.wikipedia.org/wiki/Survival_analysis). Ale možných komplikací může nastat při použití této techniky PdM případy použití, které se týkají časově proměnlivých dat pomocí pravidelných intervalech. Další informace o tom, jak analyzovat, najdete na [této](https://www.cscu.cornell.edu/news/news.php/stnews78.pdf)straně.

### <a name="multi-class-classification-for-predictive-maintenance"></a>Klasifikace víc tříd pro prediktivní údržbu
Roc klasifikačních technik lze použít v řešeních PdM pro dva scénáře:
- Předpověď _dvou budoucích výsledků_: první výsledek je _časový rozsah pro selhání_ assetu. Asset se přiřadí k jednomu z několika možných časová období. Druhým výsledkem je pravděpodobnost selhání v budoucím období kvůli _jedné z několika hlavních příčin_. Tato predikce umožňuje posádky údržby a sledujte příznaky a plánů plánu údržby.
- Předpovědět _nejpravděpodobnější hlavní příčinu_ daného selhání. Tento výsledek doporučuje správnou sadu údržby akce k vyřešení selhání. Seřazený seznam hlavní příčiny a doporučené opravy může pomoct technikům upřednostnit jejich opravy akce po selhání.

#### <a name="label-construction-for-multi-class-classification"></a>Konstrukce popisek klasifikace víc tříd
Otázka je tady: "Jaká je pravděpodobnost, že se Asset nezdařil v dalších jednotkách _NZ_ , kde _n_ je počet období?" Na tuto otázku odpovědět, popisek nZ záznamů před selháním prostředku pomocí intervalů doby (3Z 2Z Z). Popisek všechny ostatní zaznamenává "normální" (label = 0). V této metodě obsahuje cílová proměnná hodnoty _kategorií_ . (Viz obrázek 5).

![Obrázek 5. Popisky předpovědi doby selhání pro klasifikaci s více třídami](./media/predictive-maintenance-playbook/labelling-for-multiclass-classification-for-failure-time-prediction.png)

Obrázek 5. Popisky pro klasifikaci roc pro předpověď časové selhání

Otázka je tady: "Jaká je pravděpodobnost, že se Asset v dalších X časových jednotkách nezdařil z důvodu hlavní příčiny/problému _P<sub>i</sub>_ ?" kde _je_ počet možných hlavních příčin. Chcete-li odpovědět na tuto otázku, označit záznamy X před selháním assetu jako "o to neúspěšné z důvodu hlavní příčiny _P<sub>i</sub>_ " (Label = _P<sub>i</sub>_ ). Označte všechny záznamy, jako je "normální" (label = 0). V této metodě také popisky jsou zařazené do kategorií (viz obrázek 6).

![Obrázek 6. Popisky předpovědi hlavní příčiny pro klasifikaci s více třídami](./media/predictive-maintenance-playbook/labelling-for-multiclass-classification-for-root-cause-prediction.png)

Obrázek 6. Popisky pro klasifikaci roc pro kořenové příčiny predikcí

Model přiřadí pravděpodobnost selhání z důvodu každé _P<sub>i</sub>_  pravděpodobnosti, že nedošlo k chybě. Tyto pravděpodobnosti lze provést řazení podle velikosti umožňuje předpovědi problémy, které bývají nejčastějším dojít v budoucnosti.

Je tady na otázku: "jaké akce údržby Doporučujete po selhání?" K zodpovězení této otázky nevyžaduje označení _pro vyzvednutí budoucího horizontu_, protože model není v budoucnu předpověď. Je pouze předpověď nejpravděpodobnější hlavní příčiny, _Jakmile k chybě_došlo.

## <a name="training-validation-and-testing-methods-for-predictive-maintenance"></a>Školení, ověřování a testovacích metod pro prediktivní údržbu
[Vědecké zpracování týmových dat](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview) poskytuje úplný přehled modelu testování testovacího cyklu. Tato část popisuje aspekty, které jsou jedinečné pro PdM.

### <a name="cross-validation"></a>Křížové ověření
Cílem [vzájemného ověřování](https://en.wikipedia.org/wiki/Cross-validation_(statistics)) je definovat datovou sadu s cílem "testovat" model ve fázi školení. Tato datová sada se nazývá _sada ověření_. Tento postup pomáhá omezit problémy jako _přeložení_ a poskytuje přehled o tom, jak se model generalizuje na nezávislou datovou sadu. To znamená Neznámý datový nastavení, které by mohly být z skutečný problém. Trénovací a testovací rutina pro PdM je potřeba vzít v úvahu lépe generalize na neviditelný dat různé aspekty čas.

Mnoho algoritmů strojového učení závisí na celé řadě hyperparameters, která může výrazně měnit výkonu modelu. Optimální hodnoty těchto hyperparameters nejsou vypočítané automaticky při cvičení modelu. Měly by být zadané ve mezi odborníky přes data. Nalezení správné hodnoty hyperparameters několika způsoby.

Nejběžnější je _k skládání křížového ověřování_ , které rozdělí příklady náhodně do skládání _k_ . Pro každou sadu hodnot parametrů s parametry můžete spustit algoritmus učení _k_ času. V každé iteraci pomocí příklady v aktuální fold jako sada ověření a zbývajících příkladech jako trénovací sady. Učení algoritmu přes příklady školení a výpočetní metriky výkonu za ověření příklady. Na konci této smyčky vypočítá průměr z _k_ metrikám výkonu. Pro každou sadu hodnot hyperparameter vyberte ty, které mají průměrný výkon. Často je úloha výběru hyperparameters experimentální ze své podstaty.

PdM problémy údaje jsou zaznamenány jako časových řad pro události přicházející z několika zdrojů dat. Tyto záznamy může seřazené podle času zobrazení popisků. Proto pokud je datová sada _náhodně_ rozdělena do školicích a ověřovacích sad, _mohou být některé příklady školení později v čase než některé příklady ověření_. Budoucí výkon hodnot parametrů bude odhadnut na základě dat, která byla doručena _před_ vyškolením modelu. Tyto odhady může být příliš optimistické, zejména v případě, že časové řady není bez pohybu a průběhu času vyvíjí. V důsledku toho mohou být hodnoty zvolený hyperparameter neoptimální.

Doporučeným způsobem je rozdělit příklady do školicích a ověřovacích sad v _závislosti na čase_ , kde všechny příklady ověření jsou později v čase, než všechny příklady školení. Pro každou sadu hodnot hyperparameter učení algoritmu přes trénovací datové sady. Změřte výkon modelu v rámci stejné sady ověření. Vyberte hyperparameter hodnoty, které zobrazit nejlepší výkon. Hodnoty Hyperparameter zvolí trénování a ověření výsledku rozdělení lépe budoucí modelu výkonu než s hodnotami náhodně zvolí křížového ověření.

Cvičení algoritmu učení přes celý trénovacích dat pomocí nejlepší hyperparameter hodnoty mohou být generovány finálního modelu.

### <a name="testing-for-model-performance"></a>Testování výkonu modelu
Jakmile se model sestavuje, vyžaduje se odhad budoucí výkon na nová data. Vhodná odhad je metrika výkonu hyperparameter hodnot vypočítaný přes sadu ověření nebo metriku průměrný výkon vypočítaný z křížové ověření. Tyto odhady jsou často příliš optimistického řízení. Obchodní pravděpodobně často některé další pokyny o tom, jak by chtěli otestování modelu.

Doporučeným způsobem pro PdM je rozdělit příklady do školicích, ověřovacích a testovacích datových sad v _závislosti na čase_ . Všechny příklady test by měl následovat v čase všechny příklady školení a ověřování. Po rozdělení generování modelu a měřila svou výkonnost, jak je popsáno výše.

Když časových řad bez pohybu a snadno předvídatelné, generovat přístupy k náhodné a závislé na čase podobné odhad budoucí výkonu. Ale pokud časových řad-stojícího vozidla a/nebo nedá snadno předpovědět, závislé na čase přístup bude generovat víc odpovídají realitě odhad budoucí výkonu.

### <a name="time-dependent-split"></a>Rozdělení závislá na čase
Tato část popisuje osvědčené postupy pro implementaci rozdělení závislá na čase. Níže je popsána závislá na čase obousměrný rozdělení mezi trénovací a testovací sady.

Předpokládejme datový proud časovým razítkem události, například měření z různých senzory. Definování funkcí a označení trénovací a testovací příklady časových intervalů, které obsahují více událostí. Například pro binární klasifikaci, vytváření funkcí založených na minulých událostech a vytváření popisků na základě budoucích událostí v rámci "X" jednotek času v budoucnosti (podívejte se na části techniky a modelování [funkcí](#feature-engineering) ). Popisování časový rámec příklad proto proběhne později než časový rámec jeho funkcí.

Pro rozdělení závislé na čase vyberte _dobu trvání školení T<sub>c</sub>_  , na které se má naučit model, s použitím parametrů, které jsou vyladěny pomocí historických dat až do T<sub>c</sub>. Chcete-li zabránit úniku budoucích popisků, které jsou mimo T<sub>c</sub> , do školicích dat, vyberte možnost Poslední čas pro popisek výukových příkladů na X jednotek před T<sub>c</sub>. V příkladu je vidět na obrázku 7 představuje každý čtvereček záznam v datové sadě, ve kterém funkce a popisky se vypočítávají jak je popsáno výše. Obrázek zobrazuje záznamy, které by měly patřit do trénování a testování sad pro X = 2 a W = 3:

![Obrázek 7. Závislá na čase rozdělení pro binární klasifikaci](./media/predictive-maintenance-playbook/time-dependent-split-for-binary-classification.png)

Obrázek 7. Závislá na čase rozdělení pro binární klasifikaci

Zelená čtverec představují záznamy, které patří do časové jednotky, které lze použít k trénování. Každý školicí příklad je vygenerován tím, že zvažuje poslední tři tečky pro generaci funkcí a dvě budoucí období pro označování před T<sub>c</sub>. Pokud je libovolná část dvou budoucích období než T<sub>c</sub>, vylučte tento příklad ze sady školicích dat, protože se nepředpokládá žádná viditelnost mimo t<sub>c</sub>.

Černé čtverec představují záznamy konečné s popiskem datová sada, která by neměla být používána trénovací datové sady uvedené výše uvedené omezení. Tyto záznamy se také nepoužijí v datech testování, protože jsou před T<sub>c</sub>. Kromě toho časové rámce jejich používání popisků částečně závisí na školení časový rámec, což není ideální. Učení a testovací data by měl mít samostatný popisování časových intervalů pro ochranu před únikem informací popisek.

Výše popsaná technika umožňuje překrývat mezi příklady školení a testování, které mají časová razítka v blízkosti T<sub>c</sub>. Řešením pro dosažení většího rozdělení je vyloučit příklady, které jsou v jednotkách W v čase T<sub>c</sub> ze sady testů. Ale agresivní rozdělení závisí na dostupnosti klad data.

Regresní modely použité pro predikci zbývající doby životnosti více obtěžuje problém úniku. Metodou split náhodné vede k typu over-pass-the extreme přizpůsobování. V případě regresních problémů by rozdělení mělo být takové, aby záznamy patřící k assetům s chybami před T<sub>c</sub> přešly do sady školení. Záznamy prostředků, ke které došlo k selhání po uzavření přejít do testovací sady.

Pro rozdělení dat pro trénování a testování jiný osvědčeným postupem je použití rozdělit podle ID prostředku. Rozdělení by měl být žádný z prostředky používané v trénovací sady se používají při testování výkonu modelu. Tento přístup má model větší šanci poskytující víc odpovídají realitě výsledky pomocí nových prostředků.

### <a name="handling-imbalanced-data"></a>Zpracování imbalanced dat
V případě problémů s klasifikací, pokud existuje více příkladů jedné třídy než ostatní, je tato datová sada označována jako _nevyvážená_. V ideálním případě jsou upřednostňovány dostatek zástupci každá třída v trénovacích dat umožňující rozlišení mezi různými třídami. Pokud jedna třída je menší než 10 % data, data se považuje imbalanced. Podreprezentovaná třída se nazývá _menšinová třída_.

Mnoho problémů PdM čelí těchto imbalanced datových sad, kde jedné třídy je přísně znevýhodněným ve srovnání s další třídu nebo třídy. V některých případech může představovat minority třídy pouze 0,001 % celkový počet datových bodů. Třída nevyváženosti není jedinečný pro PdM. Další domény, kde jsou chyby a anomálie výjimečných výskyty čelí podobný problém, příklady, zjišťování možných podvodů a napadení sítě. Tyto chyby tvoří minority příklady tříd.

Pomocí třídy nevyrovnanosti data výkonu většinu standardních algoritmů učení dojde k ohrožení, protože jejich cílem je minimalizovat celkové chybovost. Pro datovou sadu s 99 % negativní a pozitivní příklady 1 % mohou být zobrazeny modelu pomocí označování všechny instance jako záporné mít 99 % přesností. Ale model bude chybně klasifikovat všechny pozitivní příklady; takže i když je vysoká. její přesnost, algoritmus není užitečné. V důsledku toho se konvenční hodnocení metrik, jako je _Celková přesnost u chybových přenosů_ , nedostatečné pro získání nevyváženého učení. Při imbalanced datové sady, ani jiné metriky se používají pro vyhodnocení modelu:
- Přesnost
- Odvolat
- Skóre F1
- Náklady na Upravit ROC (receiver provozní vlastnosti)

Další informace o těchto metrikách naleznete v tématu [vyhodnocení modelu](#model-evaluation).

Existují však některé metody, které pomáhají nápravu tříd imbalance problém. Existují dva hlavní _způsoby vzorkování_ a učení s _důvěrnými náklady_.

#### <a name="sampling-methods"></a>Metody vzorkování
Imbalanced učení zahrnuje použití metody k úpravě trénovací datové sady s vyrovnáváním sadu dat vzorkování. Metody vzorkování se použijí pro testovací sadu. I když je k dispozici několik technik vzorkování, je většina přímých dopředně _náhodným vzorkováním_ a _v rámci vzorkování_.

_Náhodné převzorkování_ zahrnuje výběr náhodného vzorku z minoritní třídy, replikaci těchto příkladů a jejich přidání do školicí sady dat. Řadu příkladů v třídě minority v důsledku toho je vyšší a nakonec vyvážení počtu příklady různých tříd. Nevýhodou oversampling je, že některé příklady více instancí může způsobit třídění stane příliš konkrétní, což vede k navýšení přizpůsobování. Model se může zobrazit školení vysokou přesnost, ale jeho výkon na neviditelný testovací data může být neoptimální.

Naopak _náhodně v rámci vzorkování_ je výběr náhodného vzorku ze většiny třídy a odebrání těchto příkladů ze sady dat školení. Odebrání příklady z většiny tříd však může způsobit třídění přijít o důležitých pojmů týkajících se většinou třídy. _Hybridní vzorkování_ , kde je nadlimitní využití menšinových tříd, a většina třídy je v současné době ve vzorku, je další životaschopný přístup.

Existuje mnoho sofistikovaných odběry vzorků. Technika zvolili závisí na vlastnosti dat a výsledky opakované pokusy mezi odborníky přes data.

#### <a name="cost-sensitive-learning"></a>Náklady citlivé učení
V PdM chyby, které tvoří třídu minority jsou důležitější než běžné příklady. Proto se zaměřuje především na tento algoritmus výkonu na selhání. Nesprávně předpověď pozitivní třídy jako záporné třída vakcíny stojí více než a naopak. Tato situace se obvykle označuje jako nerovné ztráta nebo asymetrického náklady chybné klasifikace prvků různých tříd. Ideální třídění by měl poskytovat přesnost předpovědi vysoké minority třídy bez negativního vlivu na přesnost pro většinu tříd.

Pomáhají této rovnováhy dosáhnout několika způsoby. Pro zmírnění těchto potíží nerovnost ztráty, přiřadit chybné klasifikace třídy minority vysoké náklady a minimalizovat celkové náklady. Algoritmy jako _SVMs (podporují vektorové počítače)_ tuto metodu přinese z vlastního vlastnictví tím, že se během školení budou zadávat náklady na pozitivní a negativní příklady. Podobně metody zvyšování úrovně, jako jsou například posílené _rozhodovací stromy_ , obvykle znázorňují dobrý výkon s nevyváženými daty.

## <a name="model-evaluation"></a>Vyhodnocení modelu
Chybné klasifikace je závažný problém pro PdM scénáře, kde je vysoké náklady na falešných poplachů firmám. Rozhodnutí o musí letadlo zůstat podle nesprávné predikcí selhání modulu, můžete narušit plány a cestovní plány. Přepnutím počítače do režimu offline z montážní linky může vést ke ztrátě příjmů. Proto modelu s správné výkonové metriky pro nová data testu se vyžaduje vyhodnocení za kritické.

Metriky typické výkonu používá k vyhodnocení modelů PdM pojednáváme níže:

- [Přesnost](https://en.wikipedia.org/wiki/Accuracy_and_precision) je nejoblíbenější metrika, která se používá k popisu výkonu třídění. Ale přesnost je citlivé na rozdělení dat a je neefektivní opatření pro scénáře s imbalanced datovými sadami. Místo toho se používají další metriky. K výpočtům a důvodům přesnosti modelu se používají nástroje, jako je [nejasná matice](https://en.wikipedia.org/wiki/Confusion_matrix) .
- [Přesnost](https://en.wikipedia.org/wiki/Precision_and_recall) PDM modelů se vztahuje k míře falešných poplachů. Nižší přesnost modelu odpovídá obecně vyšší počet falešných poplachů.
- Míra [odvolání](https://en.wikipedia.org/wiki/Precision_and_recall) označuje, kolik chyb v sadě testů bylo správně identifikováno modelem. Vyšší míra odvolání bude modelu úspěšná při identifikaci skutečná selhání.
- [Známkou F1](https://en.wikipedia.org/wiki/F1_score) je harmonický průměr přesnosti a odvolání s jeho hodnotou v rozsahu od 0 (nejhorší) do 1 (nejlepší).

Pro binární klasifikaci
- [Pracovní křivky přijímače (Roc)](https://en.wikipedia.org/wiki/Receiver_operating_characteristic) jsou také oblíbenou metrikou. V křivky roc s více TŘÍDAMI je interpretován výkon modelů podle jedné pevné provozní bodu roc s více TŘÍDAMI.
- Ale pro problémy s PdM jsou _tabulky decile_ a _výtahové grafy_ více informativní. Se zaměříte jenom na kladné třídy (selhání) a poskytnout komplexnější přehled o výkonu algoritmu než křivky roc s více TŘÍDAMI.
  - _Tabulky Decile_ jsou vytvářeny pomocí příkladů testů v sestupném pořadí pravděpodobnosti selhání. Seřazený ukázky jsou potom seskupeny do deciles (10 % vzorků s nejvyšší pravděpodobností, pak 20 %, 30 % a tak dále). /(Random baseline) poměr (míru pozitivních výsledků true) pro každý decile pomáhá odhadnout algoritmus výkon v každé decile. Náhodná základní hodnoty převezme, 0.1, 0.2 a tak dále.
  - Nastrojit [grafy](http://www2.cs.uregina.ca/~dbd/cs831/notes/lift_chart/lift_chart.html) vykreslí decile skutečnou míru a náhodnou kladnou sazbu pro všechny deciles. První deciles jsou obvykle fokus výsledky, protože zobrazí největší zisky. První deciles také se dají považovat za reprezentativní rizikoví"na", když se použije pro PdM.

## <a name="model-operationalization-for-predictive-maintenance"></a>Operacionalizace modelu pro prediktivní údržbu

Výhoda na konci cvičení data science realizované při trénovaného modelu je provedena pouze provozní. To znamená model se musí nasadit do obchodních systémů k následné predikci na základě dat nový, dříve nezobrazený.  Nová data se musí přesně shodovat s _signaturou modelu_ poučeného modelu dvěma způsoby:
- všechny funkce musí být k dispozici v každé logické instanci (Dejme tomu, že řádek v tabulce) nová data.
- musí být předem zpracují nová data, a každé z těchto funkcí zpětná analýza, přesně stejným způsobem jako trénovací data.

Procesu výše je uvedeno v mnoho způsobech akademické a oborové dokumentace. Ale všechny tyto příkazy mají stejný význam:
- Určení _skóre nových dat_ pomocí modelu
- _Použití modelu_ na nová data
- _Zprovoznění_ modelu
- _Nasazení_ modelu
- _Spustit model pro_ nová data

Jak bylo uvedeno dříve, se liší od jeho partnerské uzly operacionalizace modelu pro PdM. Scénáře zahrnující detekci anomálií a detekci selhání obvykle implementují _online hodnocení_ (označuje se také jako _bodování v reálném čase_). Zde _model_ vyhodnotí každý příchozí záznam a vrátí předpověď. Pro detekci anomálií do predikce. slouží jako ukazatel toho, že došlo k anomálií (Příklad: jedna třída SVM). Pro zjištění selhání bude tento typ nebo třída selhání.

Oproti tomu PdM zahrnuje _dávkové vyhodnocování_. Tak, aby odpovídal na podpis, modelu, musí být analyzovány funkce v nových datech stejným způsobem jako trénovací data. Pro velké datové sady, která je typická pro nová data funkce se agregují přes časová okna a zohlednit ve službě batch. Dávkové vyhodnocování se obvykle provádí v distribuovaných systémech, jako je [Spark](https://spark.apache.org/) nebo [Azure Batch](https://docs.microsoft.com/azure/batch/batch-api-basics). Máte několik možností – obě neoptimální:
- Streamování datových modulů podporuje agregaci přes okna v paměti. Proto může být uvedl, že podporují online vyhodnocování. Ale tyto systémy jsou vhodné pro kompaktní dat v úzkém windows čas nebo sadu zhuštěných elementů přes širší windows. Jejich nemusí škálování i pro kompaktní data přes širší časová okna, jak je vidět ve scénářích PdM.
- Pokud dávkové vyhodnocování není k dispozici, řešení je přizpůsobit, online vyhodnocování pro zpracování nových dat. malých dávkách najednou.

## <a name="solution-templates-for-predictive-maintenance"></a>Šablony řešení prediktivní údržby

Poslední části této příručky obsahuje seznam šablon řešení PdM, kurzy a experimenty, které jsou implementované v Azure. Tyto aplikace PdM je možné nasadit do předplatného Azure během několika minut v některých případech. Se může sloužit jako testování konceptu ukázky, sandboxy můžete experimentovat s alternativami nebo akcelerátory pro skutečné produkční implementace. Tyto šablony se nacházejí v [Azure AI Gallery](https://gallery.azure.ai) nebo na [GitHubu Azure](https://github.com/Azure). Tyto různé ukázky budou vráceny do Tato šablona řešení v čase.

| # | Název | Popis |
|--:|:------|-------------|
| 2 | [Šablona řešení prediktivní údržby Azure](https://github.com/Azure/AI-PredictiveMaintenance) | Open Source šablona řešení, která předvádí modelování Azure ML a kompletní infrastrukturu Azure s podporou scénářů prediktivní údržby v kontextu vzdáleného monitorování IoT. |
| 3 | [Obsáhlý Learning pro prediktivní údržbu](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance) | Poznámkový blok Azure s ukázkovým řešením použití LSTM (dlouhodobě krátkodobé paměti) (třída opakujících se sítí neuronové) pro prediktivní údržbu s [blogovým příspěvkem k této ukázce](https://azure.microsoft.com/blog/deep-learning-for-predictive-maintenance).|
| 4 | [Průvodce modelováním prediktivní údržby v jazyce R](https://gallery.azure.ai/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1) | Průvodce modelováním PdM pomocí skriptů v jazyce R.|
| 5 | [Prediktivní údržba Azure pro Aerospace](https://gallery.azure.ai/Solution/Predictive-Maintenance-for-Aerospace-1) | Jeden z první šablony řešení PdM založené na Azure ML v1.0 kvůli údržbě letadla. Tento průvodce, vytvoří se z tohoto projektu. |
| 6 | [Sada nástrojů Azure AI pro IoT Edge](https://github.com/Azure/ai-toolkit-iot-edge) | AI v IoT Edge pomocí TensorFlow; sada Toolkit balí modely hloubkového učení v kontejnerech Docker kompatibilních s Azure IoT Edge a zpřístupňuje tyto modely jako rozhraní REST API.
| 7 | [Prediktivní údržba Azure IoT](https://github.com/Azure/azure-iot-predictive-maintenance) | Počítače s - předkonfigurovaného řešení Azure IoT Suite. Šablona PdM letadla údržby pomocí sady IoT Suite. [Jiný dokument](https://docs.microsoft.com/azure/iot-suite/iot-suite-predictive-overview) a [návod](https://docs.microsoft.com/azure/iot-suite/iot-suite-predictive-walkthrough) vztahující se ke stejnému projektu. |
| 8 | [Šablona prediktivní údržby pomocí SQL Server R Services](https://gallery.azure.ai/Tutorial/Predictive-Maintenance-Template-with-SQL-Server-R-Services-1) | Ukázka zbývající dobu životnosti scénáře podle R services. |
| 9 | [Průvodce modelováním prediktivní údržby](https://gallery.azure.ai/Collection/Predictive-Maintenance-Modelling-Guide-1) | Funkce sady datová sada pro správu letadel, která je navržená pomocí jazyka R s [experimenty](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Modelling-Guide-Experiment-1) a [datovými sadami](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Modelling-Guide-Data-Sets-1) a [notebookem](https://gallery.azure.ai/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1) a [experimenty](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Step-1-of-3-data-preparation-and-feature-engineering-2) Azure v AzureML|

## <a name="training-resources-for-predictive-maintenance"></a>Studijní materiály pro prediktivní údržbu

Microsoft Azure nabízí postupy výuky pro základní principy PdM techniky, kromě obsah a školení na obecné koncepty AI a cvičení.

| Školení prostředků  | Dostupnost |
|:-------------------|--------------|
| [Výuková cesta pro PdM s využitím stromů a náhodné doménové struktury](https://aischool.microsoft.com/learning-paths/1H5vH5wAYcAy88CoQWQcA8) | Public | 
| [Výuková cesta pro PdM s využitím hloubkového učení](https://aischool.microsoft.com/learning-paths/FSIXxYkOGcauo0eUO8qAS) | Public |
| [Pro vývojáře AI v Azure](https://azure.microsoft.com/training/learning-paths/azure-ai-developer) | Public |
| [Microsoft AI School](https://aischool.microsoft.com/learning-paths) | Public |
| [Učení Azure AI z GitHubu](https://github.com/Azure/connectthedots/blob/master/readme.md) | Public |
| [Učení LinkedInu](https://www.linkedin.com/learning) | Public |
| [Microsoft AI YouTube webináře](https://www.youtube.com/watch?v=NvrH7_KKzoM&t=4s) | Public |
| [Microsoft AI – zobrazení](https://channel9.msdn.com/Shows/AI-Show) | Public |
| [LearnAI@MS](https://learnanalytics.microsoft.com) | Partneři |
| [Microsoft Partner Network](https://learningportal.microsoft.com) | Partneři |

Navíc jsou zdarma MOOCS (massive open online kurzy) na AI online nabízené, akademické instituce jako informačních technologií a MIT a další vzdělávací společnosti.
