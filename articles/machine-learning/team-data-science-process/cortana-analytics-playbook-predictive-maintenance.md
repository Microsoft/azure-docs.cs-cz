---
title: Průvodce Azure AI pro řešení prediktivní údržby | Dokumentace Microsoftu
description: Úplný popis pro datové vědy, která je základem řešení prediktivní údržby v několika vertikální obory.
services: machine-learning
author: fboylu
manager: cgronlun
editor: ''
ms.assetid: 2e8b66db-91eb-432b-b305-6abccca25620
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2018
ms.author: fboylu
ms.openlocfilehash: 7802aa8ea9798b18d5424c2342ba63c01406b25a
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2018
ms.locfileid: "47221702"
---
# <a name="azure-ai-guide-for-predictive-maintenance-solutions"></a>Průvodce Azure AI pro řešení prediktivní údržby

## <a name="summary"></a>Souhrn

Prediktivní Údržba (**PdM**) je oblíbených aplikací prediktivní analýzu, která můžou pomoct firmám v několika oborech, dosáhnout vysoké asset využití a úspory provozních nákladů. Tato příručka spojuje firmy a analytické pokyny a osvědčené postupy úspěšně vyvíjet a nasazovat řešení PdM pomocí [platformy Microsoft Azure AI](https://azure.microsoft.com/overview/ai-platform) technologie.

Tato příručka představuje začínáte, specifických pro dané odvětví obchodních scénářů a proces kvalifikace scénářů pro PdM. Požadavky na data a modelovacích postupů, které umožňují vytvářet řešení PdM jsou také k dispozici. Hlavním obsahem tohoto průvodce je na vědecké zpracování dat – včetně kroky pro přípravu dat, vytváření funkcí, vytvoření modelu a operacionalizace modelu. Doplnit tyto klíčové koncepty, tento průvodce popisuje sadu šablon řešení vám pomohou zrychlit vývoj aplikací PdM. Průvodce také odkazuje na užitečné studijní materiály pro profesionální získat další informace o AI za nástrojem pro datové vědy. 

### <a name="data-science-guide-overview-and-target-audience"></a>Data Science Průvodce přehled a cíl cílové skupiny
V první polovině roku Tato příručka popisuje typické obchodních problémů, přínosech implementace PdM k řešení těchto problémů a uvádí některé běžné případy použití. Pracovníci s rozhodovací pravomocí (BDMs) budou těžit z tohoto obsahu. V druhé polovině vysvětluje pro datovou vědu za PdM a poskytuje seznam PdM řešení sestavená pomocí zásad popsané v tomto průvodci. Obsahuje také postupy výuky a odkazy na školicí materiály. Technický pracovník s rozhodovací pravomocí (TDMs) bude užitečné tohoto obsahu.

| Začněte s... | Pokud jste... |
|:---------------|:---------------|
| [Obchodní případ pro prediktivní údržbu](#Business-case-for-predictive-maintenance) |Organizační pracovník s rozhodovací pravomocí (BDM) snížit prostoje a provozních nákladů a zvýšení využití zařízení |
| [Vědecké zpracování dat pro prediktivní údržbu](#Data-Science-for-predictive-maintenance) |technický pracovník s rozhodovací pravomocí (Pracovník) vyhodnocuje nové technologie PdM vám pomohou pochopit jedinečný zpracování dat a AI požadavky pro prediktivní údržbu |
| [Šablony řešení prediktivní údržby](#Solution-templates-for-predictive-maintenance)|Vývojář AI chtějí rychle zprovozněte ukázku nebo testování konceptu a softwarový architekt |
| [Studijní materiály pro prediktivní údržbu](#Training-resources-for-predictive-maintenance) | některé nebo všechny z výše uvedeného a chcete se dozvědět, základní koncepty za nástrojem pro datové vědy, nástroje a techniky.

### <a name="prerequisite-knowledge"></a>Předpokládané znalosti
Obsah BDM neočekává čtečka, která má mít žádnou znalost předchozí datové vědy. Pracovník obsah je užitečné základní znalosti o statistiky a datové vědy. Doporučuje se znalostí dat do Azure a služeb AI, Python, R, XML a JSON. Techniky AI jsou implementovány v Pythonu a r. balíčky. Šablony řešení jsou implementovány pomocí služby Azure, vývojové nástroje a sady SDK.

## <a name="business-case-for-predictive-maintenance"></a>Obchodní případ pro prediktivní údržbu

Podniky vyžadují důležité zařízení běží na efektivitu ve špičce a využití, jak začít využívat jejich návratu na kapitálových investic. Tyto prostředky může sahat od leteckých motorů, turbíny, výtahů nebo průmyslové dochlazovače – které nákladů milionů – na každý den zařízení jako kopírkách, kávy počítače nebo chladiče vody.
- Ve výchozím nastavení, Spolehněte se na většinu firem _opravné údržby_, kde nahrazuje částí a při jejich selhání. Nápravné Údržba zaručuje části se používají zcela (tedy ne plýtvání komponenty životnost), ale náklady obchodní výpadky, práci a neplánovanou údržbu požadavky (mimo hodin nebo nevhodné umístění).
- Na další úrovni, postupem firmám _preventivní údržby_, kde určit užitečné životnosti pro určitou část a udržovat nebo nahradit před selhání. Preventivní údržba zabraňuje neplánovaným a katastrofální chyby. Ale vysokým nákladům spojeným s plánovaný výpadek, snížení využití součásti před úplnou dobu života použití a práci stále zůstanou.
- Cílem _prediktivní údržby_ je k optimalizaci rovnováhu mezi opravné a preventivní údržby, povolením _právě včas_ nahrazení komponent. Tento přístup pouze nahradí tyto součásti, když se nachází blízko k chybě. Tím, že rozšíří lifespans součásti (ve srovnání se preventivní údržba) a snížení neplánovanou údržbu a náklady na práci (přes opravné údržby), firmy získají úspory nákladů a konkurenční výhody.

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

- _Odborníci přes data_ analyzovat a řešit konkrétní problémy prediktivní.
- _cloudovým architektům a vývojářům_ sestavit ucelené řešení.

## <a name="qualifying-problems-for-predictive-maintenance"></a>Kvalifikace problémy související s prediktivní údržby
Je důležité zdůraznit, že všechny případy použití nebo možné PdM efektivně vyřešit obchodní problémy. Existují tři důležité kvalifikační kritéria, které je třeba zvážit při výběru problém:

- Problém je potřeba prediktivní ze své podstaty; To znamená, měla by existovat cíl nebo jako výsledek předpovědět. Problém by měl mít také jasný akce, aby se zabránilo chybám při jejich zjištění.
- Problém by měl mít záznam o historii provozní zařízení, která obsahuje _dobré a špatné výsledků_. Sadu akcí provedených ke zmírnění chybné výsledky by měla být k dispozici jako součást tyto záznamy. Zprávy o chybách, údržba protokoly snížení výkonu, opravit a nahradit protokoly jsou také důležité. Kromě toho jsou opravy prováděnou ke zlepšení jejich nahrazení záznamy a také užitečné.
- Zaznamenané historii by měl být projeví v _relevantní_ data, která je _dostatečná_ dostatek kvalitních pro podporu případu použití. Další informace o důležitosti dat a dostatečné pokrytí, naleznete v tématu [požadavky na Data pro účely prediktivní údržby](#Data-requirements-for-predictive-maintenance).
- Nakonec firmy by měl mít odborníci, kteří mají pochopili, že problém. Jejich by měl být vědomi interních procesů a postupů, které nelze vyřešit analytik, pochopit a interpretovat data. Musí být také možnost provádět potřebné změny existujících firemních procesů do pomůže shromažďovat ta správná data pro problémy, v případě potřeby.

## <a name="sample-pdm-use-cases"></a>Vzorové případy použití PdM
Tato část se zaměřuje na kolekci PdM případy použití v několika oborech, třeba leteckém průmyslu, nástroje a doprava. Každá část začíná obchodních problémů a vás seznámí s výhodami PdM, relevantní data okolo obchodním problému a nakonec výhody PdM řešení.

| Obchodního problému | Výhody PdM |
|:-----------------|-------------------|
|**Letectví**      |                   |
|_Testovat zpoždění a zrušení_ z důvodu potíží mechanických. Chyby, které nelze opravit v čase může způsobit, že lety budou zrušeny a narušit plánování a provoz. |PdM řešení můžete předvídá pravděpodobnost letadla se zpozdila nebo zrušená kvůli mechanickým závadám.|
|_Části selhání modulu letadla_: nahrazení část motoru letadla jsou mezi většiny běžných úloh údržby v leteckém průmyslu. Údržba řešení vyžadují pečlivé správu uložených dostupnost součásti, doručování a plánování|Schopnost shromáždění informací o spolehlivosti komponenty vede k podstatné omezení objemu na investiční náklady.|
|**Finance** |                         |
|_Selhání ATM_ problém je běžný v rámci oboru bankovnictví. Problémem je pravděpodobnost, že transakci ATM hotovosti stažení dojde k přerušení kvůli chybě dokumentu zaseknutý nebo její část v hotovosti řadič sestavy. Podle predikcí selhání transakce, Network lze udržovat proaktivně zabránit výskytu chyby.| Místo umožňují počítači nezdaří polovině transakce, žádoucí alternativou je program počítače k odepření služby podle do predikce.|
|**Energie** |                          |
|_Větru turbíny selhání_: větrné turbíny jsou zdrojem hlavní energie v dbá zemích a zahrnují vysokou kapitálových nákladů. Klíčovou součástí větrné turbíny je generátor motoru. nezdařeného vykreslí turbíny neefektivní. Je také velmi nákladné opravit.|Předpověď klíčové ukazatele výkonu, jako je například MTTF (průměrný čas potřebný k selhání) může pomoct energetickým společnostem turbíny selhání a zajistit minimálními prostoji. Pravděpodobnosti selhání bude informovat rozvržení služeb techniků pro monitorování turbíny, které můžou brzy dojde k selhání a naplánovat režimy založeného na čase údržby. Prediktivní modely poskytují přehled o různých faktorů, které přispívají k selhání, která pomáhá techniků lépe pochopili původní příčiny problémů.|
|_Jistič selhání_: distribuce elektřiny domovů a podniků vyžaduje power řádky za všech okolností zaručit doručování energie byla funkční. Jističe pomůžou omezit nebo vyloučit poškození power řádků při přetížení nebo negativní weather podmínky. Obchodní problémem je Předvídejte selhání jističe.| Řešení PdM pomoct snížit náklady na opravu a zvýšit životnost vybavení, jako jsou jističe. Pomáhají zlepšovat kvalitu výkon sítě snížením neočekávaných chyb a přerušení služeb.|
|**Dopravy a logistiky** |    |
|_Selhání dveře elevator_: hodnocení velkých společností poskytování služeb plnohodnotných miliony funkční výtahy po celém světě. Výtah bezpečnost, spolehlivost a dostupnost jsou hlavních cílů pro své zákazníky. Těmto společnostem sledovat tyto a různých dalších atributů prostřednictvím snímačů, abychom jim opravné i preventivní údržby. V hodnocení nejvýraznější potíže zákazníka nefunguje dveře hodnocení. V tomto případě je poskytnout prediktivní aplikace znalostní báze, který bude předpovídat, že způsobí, že riziko chyb dveře obchodního problému.| Výtahy jsou investiční pro potenciálně životnost 20 – 30 rok. Proto může být vysoce konkurenčním prostředí; každý potenciální prodej Proto mají vysoká očekávání pro servis a podporu. Prediktivní údržby může poskytnout tyto společnosti výhodu oproti konkurenci ve svých produktech a nabídek služeb.|
|_Kolem selhání_: kolem selhání účet polovina všech trénování bezpečnosti proti vykolejení a miliardy rozesílaných zpráv v odvětví představuje špičku globální lišty nákladů. Selhání kolečko také způsobit rails zhoršovat, což občas způsobí lišty přerušení předčasně ukončen. Zalomení lišty vést k katastrofickými událostmi, jako je například bezpečnosti proti vykolejení. Aby tyto instance železniční sledování výkonu kola a nahradíte je preventivní způsobem. Obchodní problémem je predikce selhání kolečka.| Prediktivní Údržba kol vám pomůže s just-in-time nahrazení souborů Wheel |
|_Selhání dveře trénování subway_: je hlavní důvod zpoždění v operacích subway dveře selhání automobilů trénování. Obchodní problémem je Předvídejte selhání dveře trénování.|Časná povědomí o selhání dveře nebo počet dní do selhání dveře vám pomůže optimalizovat obchodní trénování dveře plány údržby.|

V další části se dostane do podrobnosti o tom, jak začít využívat výhody PdM bylo uvedeno výše.

## <a name="data-science-for-predictive-maintenance"></a>Vědecké zpracování dat pro prediktivní údržbu

Tato část obsahuje obecné pokyny pro datové vědy zásady a postupy pro PdM. Účelem je pomoct Pracovník, architekt řešení, nebo vývojář pochopit požadavky a proces tvorby aplikace AI začátku do konce pro PdM. Si můžete přečíst v této části spolu s přezkoumání ukázky a šablony pro testování konceptu uvedené v [šablony řešení prediktivní údržby](#Solution-templates-for-predictive-maintenance). Pak můžete tyto zásady a osvědčené postupy pro implementaci PdM řešení v Azure.

> [!NOTE]
> Tato příručka určená není naučit čtečky pro datové vědy. Jsou k dispozici několik užitečných zdrojů pro další informace v části věnované [studijní materiály pro prediktivní údržbu](#Training-resources-for-predictive-maintenance). [Šablony řešení](#Solution-templates-for-predictive-maintenance) uvedených v Průvodci ukazují některé z následujících postupů AI pro specifické problémy PdM.

## <a name="data-requirements-for-predictive-maintenance"></a>Požadavky na data pro účely prediktivní údržby

Úspěch libovolného závisí na (a) kvalitu co museli a (b) možnost learner. Prediktivní modely další vzory z historických dat a předpovídá budoucí výsledky s určitou pravděpodobností na základě těchto zjištěnou způsobů. Do modelu prediktivní přesnost závisí na relevanci, dostatečné pokrytí a kvalitu dat trénování a testování. Nová data, která je "skóre: pomocí tohoto modelu by měl mít stejné funkce a schéma jako data trénování a testování. Funkce vlastností (typ, hustota, distribuce a tak dále) nová data by měla odpovídat hodnotě datové sady pro trénování a testování. Sada v této části je zaměřena na tyto požadavky na data.

### <a name="relevant-data"></a>Související data

Nejprve, data musí být _týkající se problému_. Vezměte v úvahu _kolem selhání_ případu popsané výše - trénovací data by měla obsahovat funkce týkající se operací kolečka. Pokud se problém předpovídat selhání _systému výrazně nabývá na dynamice_, musí zahrnovat různé součásti systému výrazně nabývá na dynamice trénovací data. Vzhledem k tomu, zaměřuje na druhý případ selhání podsystému větší, zaměřuje prvním případě konkrétní součást. Obecné doporučení je k navrhování systémů předpovědi o konkrétní součásti, nikoli větší subsystémy, protože ten bude mít více jsou daleko od sebe data. Expert domény (naleznete v tématu [kvalifikaci problémy související s prediktivní údržby](#Qualifying-problems-for-predictive-maintenance)) by měly pomoci při výběru nejrelevantnější podmnožiny dat pro analýzu. Příslušné zdroje dat jsou popsány podrobněji v [přípravy dat pro prediktivní údržbu](#Data-preparation-for-predictive-maintenance).

### <a name="sufficient-data"></a>Dostatečným množstvím dat
S ohledem na data historie selhání jsou nejčastější dvě otázky: (1) "počet událostí selhání je potřeba trénování modelu?" (2) "kolik záznamů se považuje"dostatek"?" Nejsou žádné konečné odpovědi, ale pouze hrubé odhady. (1) větší počet událostí selhání, lepší modelu. (2) a přesný počet událostí selhání závisí na data a kontext problém je vyřešen. Ale na druhou stranu, pokud se počítači nezdaří příliš často podniku se nahradit ho, což sníží selhání instance. Sem znovu pokynů od expertní domény je důležité. Existují však metody počítat s problémem _výjimečných událostí_. Jsou popsány v části [zpracování dat imbalanced](#Handling-imbalanced-data).

### <a name="quality-data"></a>Kvalita dat
Kvalita dat je důležité – musí být každá hodnota atributu prediktivní _přesné_ ve spojení s hodnotou Cílová proměnná. Kvalita dat je dobře sledované oblast v statistiky a data správy a proto out oboru této příručce.

> [!NOTE]
> Existuje několik prostředků a produkty enterprise k zajištění kvality dat. Ukázka odkazy jsou uvedeny níže:
> - Dasu, T, Johnsonem, dolování dat T., průzkumné a čištění dat, Wiley, 2003.
> - [Analýza dat průzkumného testování, Wikipedia](https://en.wikipedia.org/wiki/Exploratory_data_analysis)
> - [Kvantitativní Data Hellerstein, J, čištění u velkých databází](http://db.cs.berkeley.edu/jmh/papers/cleaning-unece.pdf)
> - [de Jonge, E, van der vyhledávání, M, úvod k čištění dat s jazykem R](https://cran.r-project.org/doc/contrib/de_Jonge+van_der_Loo-Introduction_to_data_cleaning_with_R.pdf)

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
Historie údržby prostředku obsahuje podrobnosti o součásti nahradit, opravy aktivity prováděné atd. Tyto události zaznamenat snížení vzory. Neexistence tato zásadní informace v datech školení může vést k zavádějící výsledky modelu. Historie chyb také dostupné v rámci údržby historie jako speciální chybové kódy, nebo data objednávky pro části. Další zdroje dat, které ovlivňují vzory selhání by měl prozkoumat a poskytované odborníky na domény.

#### <a name="machine-operating-conditions"></a>Počítač provozních podmínek
Streamování dat snímačů závislosti (nebo jiné) zařízení v operaci je důležitá data source. Klíčových předpokladů v PdM je, že stav počítače s zhoršení v čase během jeho běžné operace. Očekává se, že data obsahovat časově proměnlivých funkce, které zaznamenávají tento model stárnoucích a anomálie, které vede ke snížení. Dočasné aspekt dat se vyžaduje pro algoritmus další chyby a selhání vzory v čase. Podle těchto datových bodů, algoritmus učí předpovědět, kolik jednotek více času na počítači můžete pokračovat v práci předtím, než selže.

#### <a name="static-feature-data"></a>Data statické funkce
Statické funkce jsou metadata o zařízení. Mezi příklady patří zařízení značka, model, manufactured datum, start date služby, umístění v systému a dalších technických specifikací.

Příklady relevantních dat pro [případy použití ukázkové PdM](#Sample-PdM-use-cases) jsou uvedeny v následující tabulce:

| Případ použití | Příklady související data |
|:---------|---------------------------|
|_Zpoždění letů a zrušení_ | Informace o postupu let ve formě ramena letu a stránky protokolů. Větev spojeného letů zahrnuje směrování podrobnosti, jako je odeslání/přijetí datum, čas, letiště, layovers atd. Stránky protokolů obsahuje řadu kódy chyb a údržbě zaznamenaných pracovníky údržby základu.|
|_Selhání částí motoru letadla_ | Shromažďovat data ze senzorů v letadlech obsahující informace o stavu různých částí. Údržba záznamy pomoct identifikovat, kdy došlo k selhání součásti, a pokud byly nahrazeny.|
|_Selhání ATM_ | Údajů snímačů přes pro každou transakci (uložení hotovosti/vrácení) a o předepisování hotovosti. Informace na měření mezery mezi poznámky, mějte na paměti tloušťka, mějte na paměti doručení vzdálenost, zkontrolujte atributy atd. Údržba záznamy, které poskytují kódy chyb, informace o opravě, čas poslední řadič hotovosti byla opakovaného plnění.|
|_Větrné turbíny selhání_ | Senzorů sledovat turbíny podmínky, jako je například teploty, směr větru, vygeneruje power, generátor rychlost atd. Shromáždění dat z více větrné turbíny z větrné farmy umístěných v různých oblastech. Každý turbíny se obvykle mít více údajů snímačů přes předávání měření v daném časovém intervalu.|
|_Jistič selhání_ | Údržba protokoly, které zahrnují akce opravné, preventivní a systematicky. Provozní data, která zahrnuje automatické a ruční příkazy, odeslané do jističe, jako pro otevření a zavření akce. Metadata zařízení, jako je například datum výroby, umístění, modelu atd. Jistič specifikace například napětí úrovně, informace o zeměpisné poloze, podmínky okolí.|
|_Selhání dveře elevator_| Výtah metadat – například typu hodnocení, manufactured datum, frekvence údržby, typ sestavení a tak dále. Provozní informace, jako je počet cyklů dveří, dveře průměrný čas uzavření. Historie chyb s příčiny.|
|_Selhání kolečka_ | Data ze senzorů, kolo opatření akcelerace, brzdění instancí, řízení vzdálenost, rychlost atd. Statické informace na kolech například výrobce vyrobenými datum. Data selhání odvodit z část pořadí databáze, které sledují data objednávky a množství.|
|_Selhání dveře subway trénování_ | Otevření dveří a pravou časy, ostatní provozní data, jako jsou aktuální podmínky dveře trénování. Statická data bude zahrnovat identifikátor prostředku, čas a sloupce s hodnotami podmínky.|

### <a name="data-types"></a>Typy dat
S ohledem výše uvedené zdroje dat, jsou dvě hlavní datové typy dodržovat v doméně PdM:

- _Dočasná data_: provozní telemetrii, počítač podmínky, typy pracovních pořadí, priority kódy, které se mají časové razítko v době záznam. Selhání, Údržba a opravy a historie využití bude také mít časová razítka spojené s každou událost.
- _Statická data_: funkce počítačů a funkce operátoru obecně jsou statické od popisují technických specifikací počítače nebo operátor atributů. Pokud tyto funkce může v průběhu času měnit, by měly mít časové razítko k nim má přiřazené.

Prediktivní a cíl proměnné by měl být předzpracovaná/transformuje na [číselná, kategorií a další datové typy](https://www.statsdirect.com/help/basics/measurement_scales.htm) podle algoritmu používá.

### <a name="data-preprocessing"></a>Předzpracování dat
Předpokladem pro _konstruování_, připravit data z různých datových proudů k vytvoření schématu, ze kterého se jednoduše vytvářet funkce. Nejprve Vizualizujte data jako tabulku záznamů. Každý řádek v tabulce představuje instanci školení a sloupce, které představují _prediktivní_ funkcí (také nazývané nezávislé atributy nebo proměnné). Uspořádání dat tak, že je posledního sloupce _cílové_ (závislé proměnné). Pro každou instanci školení, přiřadit _popisek_ jako hodnotu tohoto sloupce.

Pro dočasná data rozdělte duration s daty ze snímačů časové jednotky. Každý záznam by měl patřit k časovou jednotku pro určitý prostředek _a by měl nabídnout různé informace_. Časové jednotky jsou definovány v závislosti na obchodních potřebách v násobcích sekund, minut, hodin, dnů, měsíců, a tak dále. Časová jednotka _nemusí být stejná jako četnost shromažďování dat_. Pokud je vysoká frekvence, data nemusí zobrazit všechny značný rozdíl z jedné jednotky na druhý. Předpokládejme například, že teplota shromáždění každých 10 sekund. Pomocí tom samém intervalu pro trénovací data jenom tento počet příklady bez zadání jakýchkoli dalších informací. V takovém případě může být lepší strategie použití průměr dat více než 10 minut nebo hodiny podle obchodní odůvodnění.

Pro statická data,
- _Údržba záznamy_: údržby nezpracovaných dat má identifikátor prostředku a časové razítko s informacemi o činnosti údržby, které byly provedeny v daném bodě v čase. Transformace aktivity údržby do _zařazené do kategorií_ sloupce, kde každá kategorie popisovač jednoznačně mapuje na akce zvláštní údržby. Schéma pro záznamy údržby by obsahovat identifikátor prostředku, čas a akce údržby.

- _Selhání záznamy_: selhání nebo důvody selhání můžete zaznamenávat jako konkrétních kódech chyb nebo selhání událostí podle konkrétní obchodní podmínky. V případech, kdy zařízení má více kódů chyb, odborné domény by měl vystihovat těch, které se vztahují k Cílová proměnná. Použít zbývající kódy chyb nebo podmínky k vytvoření _prediktivní_ funkce, které je možné korelovat s tyto chyby. Schéma pro selhání záznamů bude zahrnovat identifikátor prostředku, selhání a čas nebo důvod selhání – Pokud je k dispozici.

- _Počítače a operátor metadat_: sloučení dat počítače a operátor do jedno schéma, které chcete přidružit k její operátor spolu s jejich příslušných atributů prostředku. Schéma pro počítač podmínky by být identifikátor prostředku, funkce asset, operátor identifikátoru a funkce operátoru.

Další data předběžného zpracování kroky zahrnují _zpracování chybějící hodnoty_ a _normalizace_ hodnot atributu. Podrobný rozbor je mimo rámec této příručky - naleznete v části Další odkazy na některé užitečné.

Pomocí výše uvedeného předzpracovaná zdroje dat na místě, finální transformace před vytváření funkcí je k výše uvedeným tabulkám založené na identifikátor prostředku a časové razítko. Výsledná tabulka by obsahovat hodnoty null ve sloupci selhání, když je počítač v běžném provozu. Tyto hodnoty null lze přisoudit podle indikátor pro běžné operace. Tento sloupec selhání slouží k vytvoření _popisky pro prediktivní model_. Další informace najdete v části na [modelování techniky pro prediktivní údržbu](#Modeling-techniques-for-predictive-maintenance).

## <a name="feature-engineering"></a>Návrh funkcí
Vytváření funkcí je první krok před modelování data. Jejich rolí v vědecké zpracování dat [je zde popsán](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/create-features). A _funkce_ je atribut prediktivního modelu – například teploty, přetížení, pronikavost a tak dále. U PdM vytváření funkcí zahrnuje abstrahovat počítače, do stavu přes historická data shromážděná prostřednictvím proměnlivou velikostí dobu trvání. V tomto smyslu se liší od jeho partnerů, jako je vzdálené monitorování, detekce anomálií a detekce selhání. 

### <a name="time-windows"></a>Čas windows
Vzdálené monitorování zahrnují události, ke kterým dochází k vytváření sestav _body v čase_. Modely detekce anomálií vyhodnotit příchozí datové proudy (skóre) dat do příznak anomálie v době body v čase. Detekce chyb klasifikuje selhání jako konkrétní typy při jejich výskytu body v čase. Naproti tomu PdM zahrnuje predikce selhání přes _budoucí období_založená na funkce, které představují počítač chování přes _historických časové období_. Pro PdM jsou příliš Rušený. Chcete-li být prediktivní funkce údaje z jednotlivých bodů čas. Abyste data pro jednotlivé funkce musí být _smoothened_ na základě agregace přes časová okna datových bodů.

### <a name="lag-features"></a>Funkce Lag
Obchodní požadavky definují, jak daleko modelu má k předvídání budoucí. Pak tato doba pomáhá definovat "jak daleko zpět model se vás pod rouškou" aby tyto předpovědi. Tato "hledání zpět" období je volána _prodleva_, a funkce, které jsou analyzovány během tohoto období prodleva, se nazývají _prodleva funkce_. Tato část popisuje funkce lag, které lze zkonstruovat z datových zdrojů s časová razítka a také vytváří funkci ze statických datových zdrojů. Funkce Lag jsou obvykle _číselné_ ze své podstaty.

> [!IMPORTANT]
> Velikost okna je určen pomocí služby experimentování ve službě a musí dokončit díky expertní domény. Pro výběr a definice funkce lag, jejich agregace a typ systému windows obsahuje stejné výstrahou.

#### <a name="rolling-aggregates"></a>Agregace se zajištěním provozu
Pro každý záznam prostředku postupné okno velikosti "W" vybrána jako počet časových jednotek pro výpočet agregací. Funkce Lag jsou pak vypočítán s použitím období W _před datem_ daného záznamu. Na obrázku 1 modré čáry zobrazit hodnoty čidel zaznamenaných pro určitý prostředek pro každou jednotku času. Souhrnný průměr hodnot funkce, označení za období velikost W = 3. Je souhrnný průměr vypočítaný přes všechny záznamy s časovými razítky v rozsahu t<sub>1</sub> (zvýrazněných oranžovou barvou) na t<sub>2</sub> (zeleně). Hodnota W je obvykle v minut nebo hodin v závislosti na povaze data. Ale pro některé problémy, výběr velké W (třeba 12 měsíců) poskytují celou historii prostředek až do okamžiku záznamu.

![Obrázek 1. Agregační funkce se zajištěním provozu](./media/cortana-analytics-playbook-predictive-maintenance/rolling-aggregate-features.png) obrázek 1. Agregační funkce se zajištěním provozu

Příklady v časovém intervalu se zajištěním provozu agregace jsou počet, průměr, míry CUMESUM (kumulativní součet), minimální/maximální hodnoty. Kromě toho odchylky, směrodatná odchylka a počet odlehlé hodnoty nad rámec standardních odchylek N se často používají. Příklady agregace, které mohou být použity pro [případy použití](#Sample-PdM-use-cases) v této příručce jsou uvedeny níže. 
- _Zpoždění letu_: počet kódů chyb za poslední den/týden.
- _Část selhání modulu letadla_: vrácení prostředky, směrodatná odchylka a součet za poslední den týdnu atd. Tato metrika byste měli určit spolu s odborné obchodní domény.
- _Selhání ATM_: prostředky, střední, rozsah, standardních odchylek, počet odlehlé hodnoty nad rámec tři standardních odchylek, horní a dolní CUMESUM se zajištěním provozu.
- _Selhání dveře trénování subway_: počet událostí za posledních den, týden, dvou týdnech atd.
- _Jistič selhání_: selhání se počítá za poslední týden roku, tři roky atd.

Další užitečné technikou v PdM je zaznamenat změny trendů, provozní špičky a změny na úrovni pomocí algoritmů, které zjišťovat anomálie v datech.

#### <a name="tumbling-aggregates"></a>Aktivační událost pro přeskakující agregace
Pro každý popisek záznam o prostředku, okna velikost _W -<sub>k</sub>_  je definován, kde _k_ je počet oken velikosti _W_. Agregace se pak vytvoří přes _k_ _přeskakující okna_ _W-k, W -<sub>(k-1)</sub>,..., W -<sub>2</sub>, W -<sub>1</sub>_  pro období před časovým razítkem záznamu. _k_ může být malý počet zachycení krátkodobé účinky nebo velký počet zachycení dlouhodobé vzorce snížení. (viz obrázek 2).

![Obrázek 2. Agregační funkce aktivační událost pro přeskakující](./media/cortana-analytics-playbook-predictive-maintenance/tumbling-aggregate-features.png) na obrázku 2. Přeskakujícího agregační funkce

Například prodleva funkce pro případ použití větrné turbíny může být vytvořena pomocí W = 1 a k = 3. Tyto možnosti implikují prodleva pro každou z posledních tří měsíců používání horní a dolní odlehlé hodnoty.

### <a name="static-features"></a>Statické funkce

Technické specifikace zařízení, jako je například datum výroby, číslo modelu, umístění, je několik příkladů statická funkce. Jsou považovány za _zařazené do kategorií_ proměnné pro modelování. Několik příkladů, pro případ použití jističe jsou napětí, aktuální, kapacita napájení, typu transformátoru a zdroji napájení. Selhání kolečko typu zadat souborů Wheel (legované vs oceli) je příklad.

Úsilí přípravy dat popsáno, pokud by měla vést k data jsou uspořádané, jak je znázorněno níže. Trénování, testování a ověřování dat by měly mít tento logický schéma (Tento příklad ukazuje čas v jednotkách, které dnů).

| ID assetu | Čas | <Feature Columns> | Štítek |
| ---- | ---- | --- | --- |
| A123 |1 den | . . . | . |
| A123 |2. den | . . . | . |
| ...  |...   | . . . | . |
| B234 |1 den | . . . | . |
| B234 |2. den | . . . | . |
| ...  |...   | . . . | . |

Posledním krokem při vytváření funkcí je **označování** z Cílová proměnná. Tento proces je závislý na techniku modelování. Zase techniku modelování závisí na obchodní problém a povaze dostupná data. Popisky jsou popsány v následující části.

> [!IMPORTANT]
> Příprava dat a vytváření funkcí jsou důležité jako modelování techniky můžete přejít na úspěšné PdM řešení. Expert domény a profesionální investovat spoustu času správné funkce a data modelu. Malým vzorkem z mnoho knih na vytváření funkcí jsou uvedeny níže:
> - Pyle, Příprava D. dat pro dolování (Nováková Kaufmann řady ve systémů pro správu dat), 1999 dat.
> - Casari Zheng A., vytváření funkcí a pro Machine Learning: zásady a postupy pro odborníky přes Data, O'Reilly, 2018.
> - Dong, např. Liu H. (Editory), funkce, technickými pracemi při Machine Learning a analýzy dat (Chapmanova & Hall/CRC dolování dat a řady Knowledge Discovery), stiskněte CRC 2018.

## <a name="modeling-techniques-for-predictive-maintenance"></a>Modelování techniky pro prediktivní údržbu

Tato část popisuje hlavní modelovacích postupů PdM problémů, spolu s jejich metod vytváření konkrétního popisku. Všimněte si, že jeden modelování postup lze použít v různých oborech. Metoda modelování je spárovaná problém datové vědy, spíše než kontextu dat po ruce.

> [!IMPORTANT]
> Volba popisky pro tyto případy selhání a používání popisků strategie  
> byste měli určit ve spolupráci s odborné domény.

### <a name="binary-classification"></a>Binární klasifikace
Binární klasifikace se používá k _odhadnout pravděpodobnost, že zařízení se nezdaří v budoucích období_ – voláno _budoucí časový horizont období X_. X se určuje podle obchodního problému a data vždy po ruce, ve spolupráci s odborné domény. Mezi příklady patří:
- _minimální doba_ muset nahradit komponenty, nasazení prostředků údržby, provedení údržby tak, aby nedošlo k potížím, která by mohla nastat v daném období.
- _minimální počet událostí_ , který může dojít předtím, než dojde k potížím.

Při použití této techniky dva typy školení příklady jsou označeny. Pozitivní příklad _označující selhání_, s popiskem = 1. Záporná příkladu, který označuje normální provoz, s popiskem = 0. Cílovou proměnnou, a proto jsou hodnoty popisků _zařazené do kategorií_. Model byste identifikovat každý nový příklad se pravděpodobně nezdaří, nebo pracovat normálně příštích X časové jednotky.

#### <a name="label-construction-for-binary-classification"></a>Popisek konstrukce pro binární klasifikaci
Je tady na otázku: "co je pravděpodobnost, že prostředek se nezdaří v příštích X časových jednotkách?" Na tuto otázku, popisek X záznamů před selháním prostředek jako "přibližně na selhání" (label = 1) a označovat pomocí popisků všechny záznamy, jako je "normální" (Popisek = 0). (viz obrázek 3).

![Obrázek 3. Popisky pro binární klasifikaci](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-binary-classification.png) obr. 3. Popisky pro binární klasifikaci

Níže jsou uvedeny příklady vytváření popisků strategie pro některé případy použití.
- _Zpoždění letu_: X může být zvolen jako 1 den, k předpovědi zpoždění v příštích 24 hodin. Potom všechny lety, které jsou během 24 hodin před selháním jsou označeny jako 1.
- _Platební ATM štítků selhání_: Cílem může být určit pravděpodobnost selhání transakce za další jednu hodinu. V takovém případě všechny transakce, ke kterým došlo během poslední hodiny selhání jsou označeny jako 1. Předvídá pravděpodobnost selhání za další měny N poznámky distribuován, všechny poznámky distribuován v rámci poslední N poznámky selhání jsou označeny jako 1.
- _Jistič selhání_: Cílem může být k další chybě příkazu jistič předpovědi. V takovém případě X je vybrán jako budoucí jeden příkaz.
- _Trénování dveře selhání_: X může být zvolen jako dva dny.
- _Větru turbíny selhání_: X může být zvolen jako po dobu dvou měsíců.

### <a name="regression-for-predictive-maintenance"></a>Regrese pro prediktivní údržbu
Regresní modely, které se používají pro _vypočítat zbývající životnosti (RUL) prostředek_. Zbývající doby životnosti je definován jako množství času, který prostředek je funkční, než dojde k následující chybě. Každý příklad školení je záznam, který patří do časovou jednotku _nY_ pro určitý prostředek, ve kterém _n_ je násobek. Model byste měli vypočítat zbývající životnost jednotlivých nový příklad jako _průběžné číslo_. Toto číslo označuje dobu, po zbývající před selháním.

#### <a name="label-construction-for-regression"></a>Popisek konstrukce pro regresní
Je tady na otázku: "Jak se zbývající životnosti (RUL) zařízení?" Pro každý záznam před selháním vypočítejte tento popisek se počet jednotek času, než budou další selhání. V této metodě jsou popisky průběžné proměnné. (Viz obrázek 4)

![Obrázek 4. Vytváření popisků pro regresní](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-regression.png) obr. 4. Vytváření popisků pro regresní

Pro regresní označování popisky se provádí s odkazem na bod selhání. Výpočet není možné nainstalovat bez mého, jak dlouho má vydrželi prostředku před selhání. Proto oproti binární klasifikace prostředky bez jakýchkoliv dat nelze použít pro modelování. Tento problém je nejlepší řešený další statistické techniky označované jako [analýz přežití](https://en.wikipedia.org/wiki/Survival_analysis). Ale možných komplikací může nastat při použití této techniky PdM případy použití, které se týkají časově proměnlivých dat pomocí pravidelných intervalech. Další informace o analýz přežití, naleznete v tématu [tomto jeden stránkování](https://www.cscu.cornell.edu/news/statnews/stnews78.pdf).

### <a name="multi-class-classification-for-predictive-maintenance"></a>Klasifikace víc tříd pro prediktivní údržbu
Roc klasifikačních technik lze použít v řešeních PdM pro dva scénáře:
- Předpověď _dvě budoucích výsledků_: první výsledek je _rozsah doby mezi poruchami_ pro určitý prostředek. Asset se přiřadí k jednomu z několika možných časová období. Druhý výsledek je pravděpodobnost selhání budoucích období kvůli _způsobí, že jeden z několika kořenové_. Tato predikce umožňuje posádky údržby a sledujte příznaky a plánů plánu údržby.
- Předpověď _nejvíce pravděpodobně hlavní příčinu_ daného selhání. Tento výsledek doporučuje správnou sadu údržby akce k vyřešení selhání. Seřazený seznam hlavní příčiny a doporučené opravy může pomoct technikům upřednostnit jejich opravy akce po selhání.

#### <a name="label-construction-for-multi-class-classification"></a>Konstrukce popisek klasifikace víc tříd
Je tady na otázku: "co je pravděpodobnost, že prostředek se nezdaří v dalším _nZ_ časových jednotkách kde _n_ je počet období?" Na tuto otázku odpovědět, popisek nZ záznamů před selháním prostředku pomocí intervalů doby (3Z 2Z Z). Popisek všechny ostatní zaznamenává "normální" (label = 0). V této metodě Cílová proměnná drží _zařazené do kategorií_ hodnoty. (Viz obrázek 5).

![Obrázek 5. Vytváření popisků pro klasifikace víc tříd pro předpověď časové selhání](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-multiclass-classification-for-failure-time-prediction.png) obr. 5. Popisky pro klasifikaci roc pro předpověď časové selhání

Otázky zde: "co je pravděpodobnost, že prostředek se nezdaří v příštích X jednotkami času z důvodu hlavní příčinu/problém _P<sub>můžu</sub>_?" kde _můžu_ je počet možné hlavní příčiny. Na tuto otázku, popisek X záznamů před selháním prostředek jako "o selhat z důvodu hlavní příčinu _P<sub>můžu</sub>_" (popisek = _P<sub>můžu</sub>_). Označte všechny záznamy, jako je "normální" (label = 0). V této metodě také popisky jsou zařazené do kategorií (viz obrázek 6).

![Obrázek 6. Vytváření popisků pro klasifikace víc tříd pro kořenové příčiny předpovědi](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-multiclass-classification-for-root-cause-prediction.png) obrázek 6. Popisky pro klasifikaci roc pro kořenové příčiny predikcí

Model přiřadí pravděpodobnost selhání kvůli každý _P<sub>můžu</sub>_  a také pravděpodobnost bez chyby. Tyto pravděpodobnosti lze provést řazení podle velikosti umožňuje předpovědi problémy, které bývají nejčastějším dojít v budoucnosti.

Je tady na otázku: "jaké akce údržby Doporučujete po selhání?" Na tuto otázku odpovědět označování _nevyžaduje budoucí časový horizont k výběru_, protože model není v budoucnu predikce selhání. To je právě předpověď nejvíce pravděpodobně hlavní příčinu _Jakmile již došlo k selhání_.

## <a name="training-validation-and-testing-methods-for-predictive-maintenance"></a>Školení, ověřování a testovacích metod pro prediktivní údržbu
[Vědecké zpracování týmových dat](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview) poskytuje úplné pokrytí cyklu trénování test ověření modelu. Tato část popisuje aspekty, které jsou jedinečné pro PdM.

### <a name="cross-validation"></a>Křížové ověření
Cílem [křížové ověření](https://en.wikipedia.org/wiki/Cross-validation_(statistics)) je definování datové sady na "test" modelu ve fázi školení. Tato datová sada je volána _ověření sady_. Tato technika pomáhá limit problémy jako _overfitting_ a poskytuje přehled o jak bude generalize model k nezávislé datové sady. To znamená Neznámý datový nastavení, které by mohly být z skutečný problém. Trénovací a testovací rutina pro PdM je potřeba vzít v úvahu lépe generalize na neviditelný dat různé aspekty čas.

Mnoho algoritmů strojového učení závisí na celé řadě hyperparameters, která může výrazně měnit výkonu modelu. Optimální hodnoty těchto hyperparameters nejsou vypočítané automaticky při cvičení modelu. Měly by být zadané ve mezi odborníky přes data. Nalezení správné hodnoty hyperparameters několika způsoby.

Nejběžnější je _k přeložení křížového ověření_ , která rozdělí příklady náhodně do _k_ složení. Pro každou sadu hodnot hyperparameters spustit algoritmus učení _k_ časy. V každé iteraci pomocí příklady v aktuální fold jako sada ověření a zbývajících příkladech jako trénovací sady. Učení algoritmu přes příklady školení a výpočetní metriky výkonu za ověření příklady. Na konci této smyčky vypočítat průměr _k_ metrik výkonu. Pro každou sadu hodnot hyperparameter vyberte ty, které mají průměrný výkon. Často je úloha výběru hyperparameters experimentální ze své podstaty.

PdM problémy údaje jsou zaznamenány jako časových řad pro události přicházející z několika zdrojů dat. Tyto záznamy může seřazené podle času zobrazení popisků. Proto pokud rozdělit datovou sadu _náhodně_ do sady pro trénování a ověření, _některé z příkladů školení může následovat v čase některé příklady ověření_. Budoucí výkonu hyperparameter hodnot se dá odhadnout na základě některých dat, které byly přijaty _před_ model se trénuje. Tyto odhady může být příliš optimistické, zejména v případě, že časové řady není bez pohybu a průběhu času vyvíjí. V důsledku toho mohou být hodnoty zvolený hyperparameter neoptimální.

Doporučený postup je rozdělit na školení a ověření nastavení v příkladech _závislá na čase_ způsobem, kde jsou všechny příklady ověření později v čase než všechny příklady školení. Pro každou sadu hodnot hyperparameter učení algoritmu přes trénovací datové sady. Měření výkonu modelu v stejné sady ověřování. Vyberte hyperparameter hodnoty, které zobrazit nejlepší výkon. Hodnoty Hyperparameter zvolí trénování a ověření výsledku rozdělení lépe budoucí modelu výkonu než s hodnotami náhodně zvolí křížového ověření.

Cvičení algoritmu učení přes celý trénovacích dat pomocí nejlepší hyperparameter hodnoty mohou být generovány finálního modelu.

### <a name="testing-for-model-performance"></a>Testování výkonu modelu
Jakmile se model sestavuje, vyžaduje se odhad budoucí výkon na nová data. Vhodná odhad je metrika výkonu hyperparameter hodnot vypočítaný přes sadu ověření nebo metriku průměrný výkon vypočítaný z křížové ověření. Tyto odhady jsou často příliš optimistického řízení. Obchodní pravděpodobně často některé další pokyny o tom, jak by chtěli otestování modelu.

Doporučený postup pro PdM je rozdělit na školení, ověření, příklady a test datových sad _závislá na čase_ způsobem. Všechny příklady test by měl následovat v čase všechny příklady školení a ověřování. Po rozdělení generování modelu a měřila svou výkonnost, jak je popsáno výše.

Když časových řad bez pohybu a snadno předvídatelné, generovat přístupy k náhodné a závislé na čase podobné odhad budoucí výkonu. Ale pokud časových řad-stojícího vozidla a/nebo nedá snadno předpovědět, závislé na čase přístup bude generovat víc odpovídají realitě odhad budoucí výkonu.

### <a name="time-dependent-split"></a>Rozdělení závislá na čase
Tato část popisuje osvědčené postupy pro implementaci rozdělení závislá na čase. Níže je popsána závislá na čase obousměrný rozdělení mezi trénovací a testovací sady.

Předpokládejme datový proud časovým razítkem události, například měření z různých senzory. Definování funkcí a označení trénovací a testovací příklady časových intervalů, které obsahují více událostí. Pro binární klasifikaci, například vytvoření funkce na základě posledních událostí a vytváření popisků na základě budoucích událostí v rámci "jednotky čas v budoucnosti X" (na najdete v částech [konstruování](#Feature-engineering) a [modelování techniky](#Modeling-techniques-applied-to-PdM-use-cases)). Popisování časový rámec příklad proto proběhne později než časový rámec jeho funkcí.

Pro rozdělení závislá na čase, vyberte _školení času přerušení T<sub>c</sub>_  jakou pro trénování modelu, s hyperparameters, která je vyladěná pomocí historických dat až po T<sub>c</sub>. Aby se zabránilo úniku budoucí popisky, které jsou nad rámec T<sub>c</sub> do trénovací data, zvolte nejnovější čas příklady školení popisek bude X jednotky před T<sub>c</sub>. V příkladu je vidět na obrázku 7 představuje každý čtvereček záznam v datové sadě, ve kterém funkce a popisky se vypočítávají jak je popsáno výše. Obrázek zobrazuje záznamy, které by měly patřit do trénování a testování sad pro X = 2 a W = 3:

![Obrázek 7. Závislá na čase rozdělení pro binární klasifikaci](./media/cortana-analytics-playbook-predictive-maintenance/time-dependent-split-for-binary-classification.png) obrázek 7. Závislá na čase rozdělení pro binární klasifikaci

Zelená čtverec představují záznamy, které patří do časové jednotky, které lze použít k trénování. Každý příklad školení je generován vzhledem k tomu, posledních tří období pro generování funkcí a dvou budoucích obdobích pro označování popisky před T<sub>c</sub>. Při libovolné části dvě budoucí období nad rámec T<sub>c</sub>, vyloučit tento příklad z trénovací datové sady, protože žádné viditelnost se předpokládá, že nad rámec T<sub>c</sub>.

Černé čtverec představují záznamy konečné s popiskem datová sada, která by neměla být používána trénovací datové sady uvedené výše uvedené omezení. Tyto záznamy nebudou také použít při testování data, protože jsou před T<sub>c</sub>. Kromě toho časové rámce jejich používání popisků částečně závisí na školení časový rámec, což není ideální. Učení a testovací data by měl mít samostatný popisování časových intervalů pro ochranu před únikem informací popisek.

Umožňuje techniku popsanou zatím překrývají trénování a testování příklady, které mají časové razítko v T<sub>c</sub>. Je řešení pro dosažení větší oddělení vyloučit příklady, které jsou v rámci časové jednotky W, t<sub>c</sub> od testovací sady. Ale agresivní rozdělení závisí na dostupnosti klad data.

Regresní modely použité pro predikci zbývající doby životnosti více obtěžuje problém úniku. Metodou split náhodné vede k typu over-pass-the extreme přizpůsobování. Regrese problémů by měl být rozdělení tak, aby záznamy, které patří k výpočetním prostředkům s chybami před T<sub>c</sub> přejděte do trénovací sady. Záznamy prostředků, ke které došlo k selhání po uzavření přejít do testovací sady.

Pro rozdělení dat pro trénování a testování jiný osvědčeným postupem je použití rozdělit podle ID prostředku. Rozdělení by měl být žádný z prostředky používané v trénovací sady se používají při testování výkonu modelu. Tento přístup má model větší šanci poskytující víc odpovídají realitě výsledky pomocí nových prostředků.

### <a name="handling-imbalanced-data"></a>Zpracování imbalanced dat
V zařazení problémy, pokud existují další příklady jednu třídu než ostatní, datové sady se říká, že _imbalanced_. V ideálním případě jsou upřednostňovány dostatek zástupci každá třída v trénovacích dat umožňující rozlišení mezi různými třídami. Pokud jedna třída je menší než 10 % data, data se považuje imbalanced. Znevýhodněným třída se nazývá _minority třídy_.

Mnoho problémů PdM čelí těchto imbalanced datových sad, kde jedné třídy je přísně znevýhodněným ve srovnání s další třídu nebo třídy. V některých případech může představovat minority třídy pouze 0,001 % celkový počet datových bodů. Třída nevyváženosti není jedinečný pro PdM. Další domény, kde jsou chyby a anomálie výjimečných výskyty čelí podobný problém, příklady, zjišťování možných podvodů a napadení sítě. Tyto chyby tvoří minority příklady tříd.

Pomocí třídy nevyrovnanosti data výkonu většinu standardních algoritmů učení dojde k ohrožení, protože jejich cílem je minimalizovat celkové chybovost. Pro datovou sadu s 99 % negativní a pozitivní příklady 1 % mohou být zobrazeny modelu pomocí označování všechny instance jako záporné mít 99 % přesností. Ale model bude chybně klasifikovat všechny pozitivní příklady; takže i když je vysoká. její přesnost, algoritmus není užitečné. V důsledku toho konvenční metrik, jako _celkové přesnost na míra chyb_ nejsou dostatečná k imbalanced učení. Při imbalanced datové sady, ani jiné metriky se používají pro vyhodnocení modelu:
- Přesnost
- Svolat
- Skóre F1
- Náklady na Upravit ROC (receiver provozní vlastnosti)

Další informace o těchto metrikách naleznete v tématu [model hodnocení](#Model-evaluation).

Existují však některé metody, které pomáhají nápravu tříd imbalance problém. Jsou dvě hlavní ty _vzorkování techniky_ a _nákladů citlivé learning_.

#### <a name="sampling-methods"></a>Metody vzorkování
Imbalanced učení zahrnuje použití metody k úpravě trénovací datové sady s vyrovnáváním sadu dat vzorkování. Metody vzorkování se použijí pro testovací sadu. I když existuje několik postupů vzorkování, většina přímo přesměrování z nich jsou _náhodné oversampling_ a _pod vzorkování_.

_Náhodné oversampling_ zahrnuje výběru náhodného vzorku ze třídy minority, tyto příklady replikace a jejich přidání na trénovací datové sady. Řadu příkladů v třídě minority v důsledku toho je vyšší a nakonec vyvážení počtu příklady různých tříd. Nevýhodou oversampling je, že některé příklady více instancí může způsobit třídění stane příliš konkrétní, což vede k navýšení přizpůsobování. Model se může zobrazit školení vysokou přesnost, ale jeho výkon na neviditelný testovací data může být neoptimální.

Naopak _náhodné pod vzorkování_ je výběru náhodného vzorku ze třídy většinou a odebírání těchto příkladů z trénovací datové sady. Odebrání příklady z většiny tříd však může způsobit třídění přijít o důležitých pojmů týkajících se většinou třídy. _Hybridní vzorkování_ kde minority třída je nadměrně vzorky a většinou třída je v části vzorkovány ve stejnou dobu je další způsob, přijatelné.

Existuje mnoho sofistikovaných odběry vzorků. Technika zvolili závisí na vlastnosti dat a výsledky opakované pokusy mezi odborníky přes data.

#### <a name="cost-sensitive-learning"></a>Náklady citlivé učení
V PdM chyby, které tvoří třídu minority jsou důležitější než běžné příklady. Proto se zaměřuje především na tento algoritmus výkonu na selhání. Nesprávně předpověď pozitivní třídy jako záporné třída vakcíny stojí více než a naopak. Tato situace se obvykle označuje jako nerovné ztráta nebo asymetrického náklady chybné klasifikace prvků různých tříd. Ideální třídění by měl poskytovat přesnost předpovědi vysoké minority třídy bez negativního vlivu na přesnost pro většinu tříd.

Pomáhají této rovnováhy dosáhnout několika způsoby. Pro zmírnění těchto potíží nerovnost ztráty, přiřadit chybné klasifikace třídy minority vysoké náklady a minimalizovat celkové náklady. Algoritmy, jako jsou _SVMs (podpora vektoru počítače)_ přijmout této metody ze své podstaty, tím, že náklady na kladné a záporné příklady zadat během cvičení. Podobně, jako například zvýšení skóre metody _boosted decision trees_ obvykle zobrazit dobrý výkon imbalanced daty.

## <a name="model-evaluation"></a>Vyhodnocení modelu
Chybné klasifikace je závažný problém pro PdM scénáře, kde je vysoké náklady na falešných poplachů firmám. Rozhodnutí o musí letadlo zůstat podle nesprávné predikcí selhání modulu, můžete narušit plány a cestovní plány. Přepnutím počítače do režimu offline z montážní linky může vést ke ztrátě příjmů. Proto modelu s správné výkonové metriky pro nová data testu se vyžaduje vyhodnocení za kritické.

Metriky typické výkonu používá k vyhodnocení modelů PdM pojednáváme níže:

- [Přesnost](https://en.wikipedia.org/wiki/Accuracy_and_precision) je nejoblíbenější metrika použít pro popisující třídění výkonu. Ale přesnost je citlivé na rozdělení dat a je neefektivní opatření pro scénáře s imbalanced datovými sadami. Místo toho se používají další metriky. Nástroje, jako je [chybovou matici](https://en.wikipedia.org/wiki/Confusion_matrix) slouží k výpočtu a odůvodnitelný přesnost modelu.
- [Přesnost](https://en.wikipedia.org/wiki/Precision_and_recall) z PdM modely se týkají počet falešných poplachů. Nižší přesnost modelu odpovídá obecně vyšší počet falešných poplachů.
- [Odvolat](https://en.wikipedia.org/wiki/Precision_and_recall) míra označuje, kolik chyb v sadě testů byly správně identifikovali modelem. Vyšší míra odvolání bude modelu úspěšná při identifikaci skutečná selhání.
- [Skóre F1](https://en.wikipedia.org/wiki/F1_score) je harmonické průměrnou hodnotu hodnot precision a Vzpomínáte se jeho hodnota v rozsahu od 0 (nejhorší) do 1 (doporučené).

Pro binární klasifikaci
- [Příjemce provozní křivky (roc s více TŘÍDAMI)](https://en.wikipedia.org/wiki/Receiver_operating_characteristic) je také oblíbené metriku. V křivky roc s více TŘÍDAMI je interpretován výkon modelů podle jedné pevné provozní bodu roc s více TŘÍDAMI.
- Ale pro problémy PdM _decile tabulky_ a _lift grafy_ jsou dál. Se zaměříte jenom na kladné třídy (selhání) a poskytnout komplexnější přehled o výkonu algoritmu než křivky roc s více TŘÍDAMI.
  - _Tabulky decile_ jsou vytvořeny testovací příklady použití sestupně podle pravděpodobnosti selhání. Seřazený ukázky jsou potom seskupeny do deciles (10 % vzorků s nejvyšší pravděpodobností, pak 20 %, 30 % a tak dále). /(Random baseline) poměr (míru pozitivních výsledků true) pro každý decile pomáhá odhadnout algoritmus výkon v každé decile. Náhodná základní hodnoty převezme, 0.1, 0.2 a tak dále.
  - [Zvedněte grafy](http://www2.cs.uregina.ca/~dbd/cs831/notes/lift_chart/lift_chart.html) vykreslení true kladné míra decile oproti náhodné true kladné rychlost pro všechny deciles. První deciles jsou obvykle fokus výsledky, protože zobrazí největší zisky. První deciles také se dají považovat za reprezentativní rizikoví"na", když se použije pro PdM.

## <a name="model-operationalization-for-predictive-maintenance"></a>Operacionalizace modelu pro prediktivní údržbu

Výhoda na konci cvičení data science realizované při trénovaného modelu je provedena pouze provozní. To znamená model se musí nasadit do obchodních systémů k následné predikci na základě dat nový, dříve nezobrazený.  Nová data musí přesně odpovídat _modelu podpis_ trénovaného modelu dvěma způsoby:
- všechny funkce musí být k dispozici v každé logické instanci (Dejme tomu, že řádek v tabulce) nová data.
- musí být předem zpracují nová data, a každé z těchto funkcí zpětná analýza, přesně stejným způsobem jako trénovací data.

Procesu výše je uvedeno v mnoho způsobech akademické a oborové dokumentace. Ale všechny tyto příkazy mají stejný význam:
- _Stanovení skóre nových dat_ pomocí modelu
- _Použití modelu_ na nová data
- _Zprovoznění_ modelu
- _Nasazení_ modelu
- _Spustit model_ pro nová data

Jak bylo uvedeno dříve, se liší od jeho partnerské uzly operacionalizace modelu pro PdM. Scénáře zahrnující detekce anomálií a detekce chyb obvykle implementují _online vyhodnocování_ (také nazývané _vyhodnocování v reálném čase_). Tady, model _skóre_ záznamech příchozí a vrací předpověď. Pro detekci anomálií do predikce. slouží jako ukazatel toho, že došlo k anomálií (Příklad: jedna třída SVM). Pro zjištění selhání bude tento typ nebo třída selhání.

Naproti tomu PdM zahrnuje _dávkové bodování_. Tak, aby odpovídal na podpis, modelu, musí být analyzovány funkce v nových datech stejným způsobem jako trénovací data. Pro velké datové sady, která je typická pro nová data funkce se agregují přes časová okna a zohlednit ve službě batch. Dávkové vyhodnocování se obvykle provádí v distribuovaných systémech, jako je [Spark](http://spark.apache.org/) nebo [Azure Batch](https://docs.microsoft.com/azure/batch/batch-api-basics). Máte několik možností – obě neoptimální:
- Streamování datových modulů podporuje agregaci přes okna v paměti. Proto může být uvedl, že podporují online vyhodnocování. Ale tyto systémy jsou vhodné pro kompaktní dat v úzkém windows čas nebo sadu zhuštěných elementů přes širší windows. Jejich nemusí škálování i pro kompaktní data přes širší časová okna, jak je vidět ve scénářích PdM.
- Pokud dávkové vyhodnocování není k dispozici, řešení je přizpůsobit, online vyhodnocování pro zpracování nových dat. malých dávkách najednou.

## <a name="solution-templates-for-predictive-maintenance"></a>Šablony řešení prediktivní údržby

Poslední části této příručky obsahuje seznam šablon řešení PdM, kurzy a experimenty, které jsou implementované v Azure. Tyto aplikace PdM je možné nasadit do předplatného Azure během několika minut v některých případech. Se může sloužit jako testování konceptu ukázky, sandboxy můžete experimentovat s alternativami nebo akcelerátory pro skutečné produkční implementace. Tyto šablony jsou umístěny [galerii Azure AI](http://gallery.azure.ai) nebo [Azure na Githubu](https://github.com/Azure). Tyto různé ukázky budou vráceny do Tato šablona řešení v čase.

| # | Titul | Popis |
|--:|:------|-------------|
| 1 | [Ukázka prediktivní údržby Azure Machine Learning](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance) |Ukázka PdM předpovídat selhání za další časové jednotky N. Tato ukázka je zapsán jako projekt aplikace Azure ML Workbench a je ideální pro začátečníky k PdM. [Další dokumentaci](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/scenario-predictive-maintenance) týkající se této ukázce.|
| 2 | [Šablona řešení prediktivní údržby Azure](https://github.com/Azure/AI-PredictiveMaintenance) | Šablonu řešení open source, která ukazuje ML modelování a kompletní infrastrukturou Azure dokáže v souvislosti s vzdáleného sledování IoT podporuje scénáře prediktivní údržby. |
| 3 | [Hloubkové učení pro prediktivní údržbu](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance) | Azure Poznámkový blok s řešením ukázku použití sítí LSTM (Long krátkodobé paměti) (třída Rekurentní Neuronové sítě) pro prediktivní údržbu se [blogovém příspěvku s tímto příkladem](https://azure.microsoft.com/blog/deep-learning-for-predictive-maintenance).|
| 4 | [Průvodce modelováním prediktivní údržby v jazyce R](https://gallery.azure.ai/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1) | Průvodce modelováním PdM pomocí skriptů v jazyce R.|
| 5 | [Azure prediktivní Údržba pro letectví](https://gallery.azure.ai/Solution/Predictive-Maintenance-for-Aerospace-1) | Jeden z první šablony řešení PdM založené na Azure ML v1.0 kvůli údržbě letadla. Tento průvodce, vytvoří se z tohoto projektu. |
| 6 | [Azure AI Toolkit pro IoT Edge](https://github.com/Azure/ai-toolkit-iot-edge) | AI v IoT edge používající TensorFlow; Sada nástrojů balíčky hloubkového učení modely v kontejnerech Dockeru kompatibilní se službou Azure IoT Edge a vystavit tyto modely jako rozhraní REST API.
| 7 | [Prediktivní údržby Azure IoT](https://github.com/Azure/azure-iot-predictive-maintenance) | Počítače s - předkonfigurovaného řešení Azure IoT Suite. Šablona PdM letadla údržby pomocí sady IoT Suite. [Další dokument](https://docs.microsoft.com/azure/iot-suite/iot-suite-predictive-overview) a [návod](https://docs.microsoft.com/azure/iot-suite/iot-suite-predictive-walkthrough) související do stejného projektu. |
| 8 | [Šablona prediktivní údržby pomocí SQL Server R Services](https://gallery.azure.ai/Tutorial/Predictive-Maintenance-Template-with-SQL-Server-R-Services-1) | Ukázka zbývající dobu životnosti scénáře podle R services. |
| 9 | [Průvodce modelováním prediktivní údržby](https://gallery.azure.ai/Collection/Predictive-Maintenance-Modelling-Guide-1) | Funkce datovou sadu údržby letadla analyzovány pomocí jazyka R s [experimenty](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Modelling-Guide-Experiment-1) a [datových sad](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Modelling-Guide-Data-Sets-1) a [Azure Notebooks](https://gallery.azure.ai/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1) a [experimenty](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Step-1-of-3-data-preparation-and-feature-engineering-2)v Azure ml v1.0|

## <a name="training-resources-for-predictive-maintenance"></a>Studijní materiály pro prediktivní údržbu

Microsoft Azure nabízí postupy výuky pro základní principy PdM techniky, kromě obsah a školení na obecné koncepty AI a cvičení.

| Školení prostředků  | Dostupnost |
|:-------------------|--------------|
| [Postup výuky pro PdM pomocí stromy a doménové struktury Random](https://aischool.microsoft.com/learning-paths/1H5vH5wAYcAy88CoQWQcA8) | Public | 
| [Postup výuky pro PdM využívající hloubkové učení](https://aischool.microsoft.com/learning-paths/FSIXxYkOGcauo0eUO8qAS) | Public |
| [Vývojář AI v Azure](http://azure.microsoft.com/training/learning-paths/azure-ai-developer) | Public |
| [Školní Microsoft AI](http://aischool.microsoft.com/learning-paths) | Public |
| [Azure AI učení z Githubu](https://github.com/Azure/connectthedots/blob/master/readme.md) | Public |
| [LinkedIn Learning](http://www.linkedin.com/learning) | Public |
| [Webináře Microsoft AI Youtube](https://www.youtube.com/watch?v=NvrH7_KKzoM&t=4s) | Public |
| [Zobrazit Microsoft AI](http://channel9.msdn.com/Shows/AI-Show) | Public |
| [LearnAI@MS](http://learnanalytics.microsoft.com) | Partneři |
| [Microsoft Partner Network](http://learningportal.microsoft.com) | Partneři |

Navíc jsou zdarma MOOCS (massive open online kurzy) na AI online nabízené, akademické instituce jako informačních technologií a MIT a další vzdělávací společnosti.
