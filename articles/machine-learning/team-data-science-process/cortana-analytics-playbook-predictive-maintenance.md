---
title: Azure AI Příručka pro řešení prediktivní údržby | Microsoft Docs
description: Komplexní popis vědecké zpracování dat, která pohání řešení prediktivní údržby v několika svislé odvětví.
services: machine-learning
documentationcenter: ''
author: fboylu
manager: jhubbard
editor: cgronlun
ms.assetid: 2e8b66db-91eb-432b-b305-6abccca25620
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2018
ms.author: fboylu
ms.openlocfilehash: e069a7d16fa56f2c7590edbda8339182835ef367
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34837255"
---
# <a name="azure-ai-guide-for-predictive-maintenance-solutions"></a>Azure AI Příručka pro řešení prediktivní údržby

## <a name="summary"></a>Souhrn

Prediktivní údržby (**PdM**) je Oblíbené aplikace prediktivní analýzy, která pomáhá podnikům v několika odvětví dosáhnout vysoké asset využití a úspory provozních nákladů na. Tato příručka spojuje obchodní a analytické pokyny a osvědčené postupy úspěšně vývoji a nasazení řešení PdM pomocí [platformy Microsoft Azure AI](https://azure.microsoft.com/overview/ai-platform) technologie.

Tato příručka pro začátek představuje průmyslové obchodní scénáře a proces opravňujících tyto scénáře pro PdM. Požadavky na data a modelování techniky, jak sestavit řešení PdM jsou také uvedené. Hlavní obsah v průvodci je v procesu vědecké účely dat – včetně kroky přípravy dat, funkce analýzy, vytvoření modelu a modelu operationalization. Aby doplňovala tyto klíčové koncepty, tento průvodce popisuje sadu urychlit vývoj aplikací PdM šablony řešení. Průvodce také ukazuje na zdrojů pro specialisty na další informace o AI za vědecké zpracování dat užitečné školení. 

### <a name="data-science-guide-overview-and-target-audience"></a>Data vědecké účely Průvodce přehled a cíle cílové skupiny
První polovinu Tato příručka popisuje typické obchodních problémů, výhod implementace PdM k řešení těchto problémů a uvádí některé běžné případy použití. Pracovníci s rozhodovací pravomocí (BDMs) bude těžit z tohoto obsahu. Druhé polovině vysvětluje vědecké zpracování dat za PdM a poskytuje seznam PdM řešení vytvořená s využitím zásad uvedených v této příručce. Poskytuje taky kurzů a odkazy na školicích materiálů. Technické rozhodují (TDMs) užitečné tento obsah.

| Začněte s... | Pokud jste... |
|:---------------|:---------------|
| [Obchodní případ prediktivní údržby](#Business-case-for-predictive-maintenance) |s rozhodovací pravomocí (BDM) chtějí omezit výpadky a provozní náklady a zvýšit využití zařízení |
| [Vědecké zpracování dat pro prediktivní údržby](#Data-Science-for-predictive-maintenance) |technické s rozhodovací pravomocí (Pracovník) vyhodnocení PdM technologie pochopit jedinečný zpracování dat a AI požadavky pro prediktivní údržby |
| [Řešení šablon pro prediktivní údržby](#Solution-templates-for-predictive-maintenance)|architekti softwaru nebo vyhledávání rychle stát ukázku nebo testování konceptu vývojáře AI |
| [Školení prostředky pro prediktivní údržby](#Training-resources-for-predictive-maintenance) | některé nebo všechny výše uvedené a chcete se dozvědět základní koncepty za vědecké zpracování dat, nástroje a techniky.

### <a name="prerequisite-knowledge"></a>Požadovaný znalostní báze
Obsah BDM neočekává čtečky mít žádnou znalost vědecké účely předchozí data. Pro obsah Pracovník je užitečné základní znalosti o vědecké účely statistiky a data. Doporučuje se znalostní báze dat Azure a služby AI, Python, R, XML a JSON. Techniky AI jsou implementované v Pythonu a R balíčky. Šablony řešení jsou implementovány pomocí služby Azure, nástroje pro vývoj a sady SDK.

## <a name="business-case-for-predictive-maintenance"></a>Obchodní případ prediktivní údržby

Podniky vyžadují kritické zařízení, aby byl spuštěn v efektivitu ve špičce a využití si uvědomí, jejich návratu na kapitálové investice. Tyto prostředky může v rozsahu od letecké motory turbín, výtahy nebo průmyslových dochlazovače – které miliony - dolů každý den zařízení jako kopírky, kávy počítače nebo horních chladiče náklady.
- Ve výchozím nastavení, většina podniků spoléhá na _opravné údržby_, kde jsou části Nahradit jako a když se nezdaří. Opravné Údržba zaručuje částí se používají úplně (proto není plýtvání součást životnost), ale stojí firmy v výpadek, práce a neplánovanou údržbu požadavky (mimo dobu, nebo nepohodlná umístění).
- Na další úroveň, firmách postupem _preventivní údržbě_, kde určit užitečné životnost pro část a udržovat a nahraďte ji před selhání. Preventivní údržbě zabraňuje neplánovanou a závažné chyby. Ale zůstanou nedostatečné využití komponenty před jeho úplnou životnost používají a stále celkové náklady plánované výpadky na vysokou.
- Cílem _prediktivní údržby_ je za účelem optimalizace rovnováhu mezi opravné a preventivní údržby, povolením _jenom na dobu_ nahrazení součásti. Tento přístup pouze nahradí tyto součásti, pokud se blíží k chybě. Tím, že rozšíří součást lifespans (ve srovnání s preventivní údržby) a snížení neplánovanou údržbu a práci, (přes opravné údržby), firmách získají úsporu nákladů a konkurenční výhody.

## <a name="business-problems-in-pdm"></a>Obchodní problémy v PdM
Podnikům čelí vysoce rizikové provozní z důvodu neočekávané chyby a mají omezenou vhled do hlavní příčinu problémů v systémech komplexní. Jsou některé klíčové obchodních otázek:

- Zjišťovat anomálie v zařízení nebo systém výkonu nebo funkce.
- Předvídání, zda prostředek může selhat v blízké budoucnosti.
- Odhad zbývající dobu životnosti prostředek.
- Určení hlavních příčin selhání prostředek.
- Určete, jaké akce údržby je potřeba provést, když, na prostředek.

Typické cílem příkazy z PdM jsou:

- Snížit provozní riziko zvláště důležité vybavení.
- Zvýšit návratovou hodnotu na prostředky predikci selhání, než k nim dojde.
- Řídit náklady na údržbu povolením operací údržby za běhu.
- Nižší otěr zákazníka, zlepšení image značky a ke ztrátě prodeje.
- Nižší poplatky za inventáře snížením úrovně inventáře Odhadnutím toho, jaká bod.
- Zjistit vzorů, které jsou připojené k různým problémy při údržbě.
- Zadejte klíčové ukazatele výkonu (klíčových ukazatelů výkonu) jako je například stav skóre pro asset podmínky.
- Odhad zbývající životnost prostředků.
- Doporučujeme včas údržby aktivity.
- Povolte jenom v inventáři čas odhad pořadí data určena k nahrazení částí.

Tyto příkazy cílem jsou výchozím bodem pro:

- _datových vědců_ analyzovat a řešit konkrétní problémy prediktivní.
- _cloudové architekty a vývojáře_ dávat dohromady komplexní řešení.

## <a name="qualifying-problems-for-predictive-maintenance"></a>Kvalifikující problémy prediktivní údržby
Je nutné zdůraznit, že všechny případy použití nebo lze PdM efektivně vyřešit obchodní problémy. Existují tři důležité opravňující kritéria, která je potřeba zvážit při výběru problému:

- Problém má být prediktivní ve své podstatě; To znamená, měla by existovat na cíl nebo výstupem předpovědět. Tento problém by měly mít také zrušte cestu zabraňte selhání při jejich zjištění.
- Tento problém by měl mít záznam provozní historie zařízení, která obsahuje _dobrý i chybný výstupy_. Sadu akcí, aby byla zmírněna chybný výstupy navíc by měl mít k dispozici jako součást tyto záznamy. Zprávy o chybách, údržby protokoly snížení výkonu, opravte a nahraďte protokoly jsou také důležité. Kromě toho jsou opraví zavázala zlepšení a nahrazení zaznamenává taky užitečné.
- Zaznamenaná historie by měl promítnuta _relevantní_ data, která je _dostatečná_ dostatek kvality pro podporu případ použití. Další informace o data relevance a dostatečné pokrytí najdete v tématu [požadavky dat pro prediktivní údržby](#Data-requirements-for-predictive-maintenance).
- Nakonec firmy by měl mít odborníky domény, kteří mají jasné problému. Měly by být vědomi interní procesy a postupy, abyste mohli pomoct analytika pochopit a interpretovat data. Musí být také možnost provádět potřebné změny existující podnikové procesy pomohou shromažďovat správná data pro problémy, v případě potřeby.

## <a name="sample-pdm-use-cases"></a>Případy použití PdM vzorku
Tato část se zaměřuje na kolekci případy použití PdM z několika odvětví, jako je například letecký, nástrojů a Transport. Každá část začíná obchodního problému a vás seznámí s výhodami PdM příslušných dat, které obaluje obchodního problému a nakonec výhody PdM řešení.

| Obchodního problému | Výhody z PdM |
|:-----------------|-------------------|
|**Letecké**      |                   |
|_Letu zpoždění a zrušení_ kvůli problémům s mechanických. Chyby, které nelze opravit v čase může způsobit lety ke zrušení a přerušit operace a plánování. |PdM řešení můžete předvídání pravděpodobnost letadla se odložené nebo zrušena z důvodu mechanických selhání.|
|_Letadla modul částí selhání_: leteckého motoru část náhrady patří mezi nejběžnější úlohy údržby v rámci odvětví letecká společnost. Řešení údržby vyžadují pečlivě správu uložených dostupnost součásti, doručení a plánování|Schopnost shromažďovat, že intelligence na spolehlivost součást vede k podstatné snížení na investice náklady.|
|**Finance** |                         |
|_Selhání ATM_ problém je běžný, v rámci odvětví bankovnictví. Problém tady je pravděpodobnost, že získá ATM peněžních odvolání transakce přerušena z důvodu selhání dokumentu papír nebo část v enumenátor peněžních sestavy. Podle předpovědi selhání transakce, peněžními automaty zpracováním proaktivně k zabránění výskytu chyby.| Místo povolit počítač selhání se v polovině prostřednictvím transakci, žádoucí alternativou je program počítače tak, aby odepřel služby podle předpovědi.|
|**Energie** |                          |
|_Větru turbína selhání_: větru turbín zdrojem hlavní energie v k životnímu prostředí zodpovědná zemích a zahrnují vysoké kapitálové náklady. Klíčovou součástí větru turbín je generátor motoru. nezdařeného vykreslí turbíně neúčinná. Je také vysoce náročná opravit.|Predikci klíčových ukazatelů výkonu, jako je například MTTF (střední čas selhání) může pomoct energetické společnosti zabránit chybám turbína a zajistit minimální dobou výpadku. Selhání pravděpodobnostech bude informovat o tom technici k monitorování turbín, které by mohly brzy dojde k selhání a naplánovat režimů založené na čase údržby. Prediktivní modely poskytují přehled o různých faktorů, které přispívají k chybě, která pomáhá technici lépe pochopit, že že hlavní příčiny potíží.|
|_Selhání jistič_: distribuce elektřiny domácnostech a společnostem vyžaduje power řádky do provozu za všech okolností zaručit energie doručení. Moduly okruh dělení pomůžou omezit nebo vyloučit škody power řádků při přetížení nebo nežádoucí informace o počasí podmínky. Obchodního problému je k předvídání jistič selhání.| Řešení PdM pomáhají omezit náklady opravit a zvýšit životnost vybavení, jako jsou moduly okruh dělení. Pomáhají zlepšit kvalitu sítě napájení snížením neočekávaných selhání a přerušení poskytování služeb.|
|**Transport a logistiky** |    |
|_Hodnocení dveře selhání_: velké hodnocení společnosti poskytují služby úplné zásobníku pro miliony funkční výtahy po celém světě. Hodnocení zabezpečení, spolehlivost a provozu jsou hlavní otázky zákazníkům. Těmto společnostem sledovat tyto a různé další atributy prostřednictvím senzorů, aby jim pomohl s opravné a preventivní údržby. V hodnocení nepracuje správně nejvýraznější problém zákazníka dveře hodnocení. V takovém případě je poskytnout znalostní báze knowledge base prediktivní aplikace, který bude předpovídat potenciálně způsobuje selhání dveře obchodního problému.| Výtahy jsou kapitálové investice pro potenciálně životnost 20-30 roku. Proto může být každý potenciální prodej vysoce konkurenčním prostředí; Proto jsou vysoké očekávání pro služeb a podpory. Prediktivní údržby můžete těmto společnostem poskytnout několik výhod, přes jejich konkurenci ve svých produktech a nabídek služeb.|
|_Hodinových selhání_: hodinových selhání účet polovina všech cvičení bezpečnosti proti vykolejení a náklady až miliardy na globální liště odvětví. Selhání Wheel také způsobit zhoršení, které někdy způsobuje liště přerušíte předčasně. Zalomení liště vést k závažné události, jako je například bezpečnosti proti vykolejení. Abyste se vyhnuli takové instancí, dráhy sledovat výkon souborů Wheel a nahradíte je preventivní způsobem. Obchodního problému je předpovědi wheel selhání.| Prediktivní údržby souborů Wheel vám pomůže s za běhu nahrazení souborů Wheel |
|_Selhání dveře train podzemní dráha_: selhání dveře train automobilů je hlavní důvod zpoždění při podzemní dráha operace. Obchodního problému je k předvídání train dveře selhání.|Časná povědomí o selhání dveře nebo počet dní, dokud selhání dveře pomůžou optimalizovat obchodní cvičení dveře plány údržby.|

V další části získá na podrobné informace o tom, jak výhody PdM výše popsané.

## <a name="data-science-for-predictive-maintenance"></a>Vědecké zpracování dat pro prediktivní údržby

Tato část obsahuje obecná pravidla zásad vědecké účely dat a postup pro PdM. Je určena k pomoci Pracovník, architekt řešení nebo vývojář pochopit požadavky a proces pro vytváření aplikací AI začátku do konce pro PdM. Si můžete přečíst v této části společně s kontrolu ukázky a testování konceptu šablon uvedené v [šablony řešení pro prediktivní údržby](#Solution-templates-for-predictive-maintenance). Pak můžete tyto zásady a osvědčené postupy pro implementaci řešení PdM v Azure.

> [!NOTE]
> Tato příručka určená není kterých čtečky vědecké zpracování dat. Několik užitečné zdroje jsou k dispozici pro další čtení v části pro [školení prostředky pro prediktivní údržby](#Training-resources-for-predictive-maintenance). [Šablony řešení](#Solution-templates-for-predictive-maintenance) uvedených v Průvodci ukazují některé z těchto postupů AI pro konkrétní PdM problémy.

## <a name="data-requirements-for-predictive-maintenance"></a>Požadavky na data pro prediktivní údržby

Úspěch žádné learning závisí na (a) kvality její cíl a (b) možnost student. Prediktivní modely další vzory z historických dat a předpovídat budoucí výsledky s určitá pravděpodobnost na základě těchto zjištěnou způsobů. Modelu prediktivní přesnost závisí na důležitosti, dostatečné pokrytí a kvalitu učení a testovací data. Nová data, která 'skóre pro magnitudu' pomocí tohoto modelu musí mít stejné funkce a schéma jako školení a testovací data. Funkce vlastností (typ, hustotu, distribuce a tak dále) nová data by měla odpovídat hodnotě učení a testovací datových sad. Na tyto požadavky dat je zaměřená v této části.

### <a name="relevant-data"></a>Relevantní data

První, data musí být _relevantní pro problém_. Vezměte v úvahu _hodinových selhání_ popsané případ použití vyšší - Cvičná data by měl obsahovat funkce související se wheel operací. Pokud tento problém byl k předvídání selhání _trakčním systému_, jsou Cvičná data musí zahrnovat všechny různé součásti nástroje trakčním systému. Prvním případě cílí pro konkrétní součást, zatímco druhém případě Cílem selhání větší subsystému. Obecné doporučení je návrh předpovědi systémy o konkrétní součásti, nikoli větší subsystémy, vzhledem k tomu, že k tomu bude mít více rozmístěnými data. Expert domény (viz [kvalifikující problémy prediktivní údržby](#Qualifying-problems-for-predictive-maintenance)) by měly pomoci při výběru nejdůležitější podmnožiny dat pro analýzu. Příslušné zdroje dat jsou popsány podrobněji na [Příprava dat pro prediktivní údržby](#Data-preparation-for-predictive-maintenance).

### <a name="sufficient-data"></a>Dostatečného množství dat
Dva dotazy jsou často kladené s ohledem na data historie selhání: (1) "počet událostí selhání jsou vyžadována k natrénování modelu?" (2) "počet záznamů se považuje za"dostatek"?" Neexistují žádné spolehlivý odpovědi, ale pouze zásady. Pro (1) lepší více počet událostí selhání modelu. Pro [2] a přesný počet událostí selhání závisí na data a kontext problém se vyřeší. Ale na straně překlopit, pokud se počítači nezdaří příliš často pak firmy dojde k nahrazení, což sníží selhání instance. Zde znovu, pokyny z domény odborné je důležité. Existují však metody zvládnout problému s _výjimečných události_. Jsou popsané v části [zpracování imbalanced dat](#Handling-imbalanced-data).

### <a name="quality-data"></a>Kvality dat
Kvalita dat je důležité – musí být hodnota atributu každý předpověď _přesné_ ve spojení s hodnotou Cílová proměnná. Kvality dat je dobře sledované oblasti v rámci správy statistiky a data a proto mimo obor této příručce.

> [!NOTE]
> Existuje několik prostředků a produkty enterprise k doručování dat kvality. Ukázka odkazy najdete níže:
> - Dasu, T, Janíček, dolování dat t. Toto, nahodilého a Data čištění, Wiley, 2003.
> - [Analýza dat nahodilého, Wikipedia](https://en.wikipedia.org/wiki/Exploratory_data_analysis)
> - [Kvantitativní Data Hellerstein, J, čištění pro velké databáze](http://db.cs.berkeley.edu/jmh/papers/cleaning-unece.pdf)
> - [de Jonge, E, van der vyhledat, M Úvod do dat čištění s R](https://cran.r-project.org/doc/contrib/de_Jonge+van_der_Loo-Introduction_to_data_cleaning_with_R.pdf)

## <a name="data-preparation-for-predictive-maintenance"></a>Příprava dat pro prediktivní údržby

### <a name="data-sources"></a>Zdroje dat

Zdroje dat relevantní pro prediktivní údržby zahrnovat, ale nejsou omezeny na:
- Selhání historie
- Historie údržby nebo opravit
- Počítač provozních podmínek
- Metadata zařízení

#### <a name="failure-history"></a>Selhání historie
Události chyb vyskytují jen vzácně v aplikacích PdM. Ale při vytváření modelů předpovědi, algoritmus potřebuje další informace o součásti normální provozní vzor, stejně jako jeho selhání vzorky. Proto jsou Cvičná data by měla obsahovat dostatečný počet příklady z obou kategorií. Historie údržby záznamů a částí nahrazení jsou dobrou zdroje a vyhledejte události selhání. Pomocí některé domény znalosti anomálie v Cvičná data určeny také jako selhání.

#### <a name="maintenancerepair-history"></a>Historie údržby nebo opravit
Historie údržby majetku obsahuje podrobnosti o součásti nahrazen, opravy aktivity prováděné atd. Tyto události zaznamenat snížení vzory. Bez těchto informací zásadní v datech školení může vést k zavádějící výsledky modelu. Historie selhání naleznete také v rámci údržby historie jako speciální chybové kódy nebo pořadí data pro částí. Další datové zdroje, které ovlivňují vzory selhání by měly prozkoumat a poskytuje odborníky domény.

#### <a name="machine-operating-conditions"></a>Počítač provozních podmínek
Streamování dat snímačů na základě (nebo jiných) zařízení v operaci je důležité datovým zdrojem. Klíče předpokladů v PdM je, že stav počítače a snižuje časem během jeho běžné operace. Očekává se, že data obsahují čas různých funkcí, které zaznamenat tento vzor stárnutí a všechny anomálií, které vede ke snížení výkonu. Dočasné aspekt dat je vyžadována pro algoritmus Další selhání a vzory bez selhání v čase. Podle toho, tyto datové body, algoritmus zjišťuje odhadnout, kolik další jednotky času na počítači můžete pokračovat v práci předtím, než se nezdaří.

#### <a name="static-feature-data"></a>Data statické funkce
Statické funkce jsou metadata o zařízení. Příklady jsou výrobce vybavení, modelu, vyrobenou datum, spusťte datum služby, umístění systému a jiných technických specifikací.

Příklady relevantních dat pro [případy použití ukázkové PdM](#Sample-PdM-use-cases) jsou uvedeny v následující tabulce:

| Případ použití | Příklady relevantní data |
|:---------|---------------------------|
|_Zpoždění letu a zrušení_ | Informace o postupu letu ve formě letu nožičky a protokoly stránky. Data pohybující se větev zahrnují směrování podrobnosti, například odeslání nebo přijetí datum, čas, letiště, layovers atd. Stránka protokolu obsahuje řadu kódy údržby a chyb zaznamenaných pracovníky údržby základů.|
|_Selhání částí motor letadla_ | Data se shromažďují ze senzorů v letadle, které poskytují informace o stavu různých částí. Zaznamenává údržby pomoci při identifikaci při došlo k selhání komponent a při jejich byly nahrazeny.|
|_Selhání ATM_ | Odečty snímačů pro každou transakci (uloží peněžních nebo kontrola) a výdej peněžních. Informace na měření mezera mezi poznámky, Všimněte si, tloušťka, Všimněte si příchodem vzdálenost, zkontrolujte atributy atd. Záznamy údržby, které poskytují kódy chyb, informace o opravě, čas posledního enumenátor peněžních byl opakovaného plnění.|
|_Selhání turbína větru_ | Snímače sledování turbína podmínek jako teploty, směr větru, napájení generované, generátor rychlost atd. Shromáždění dat z více turbín větru z farmy větru umístěné v různých oblastech. Každý turbína bude obvykle mají více odečty snímačů předávání měření na pevném časovém intervalu.|
|_Selhání jistič_ | Protokoly údržby, které zahrnují opravné, preventivní a systematické akce. Provozní data, která zahrnuje automatickou a ruční příkazy odeslané okruh funkce, jako rozdělování pro akce Otevřít a zavřít. Metadata zařízení, jako je například datum výroby, umístění, modelu, atd. Specifikace jistič například napětí úrovně, informace o zeměpisné poloze, podmínky okolí.|
|_Selhání dveře hodnocení_| Hodnocení metadata, jako je například typ hodnocení, vyrobenou datum, frekvence údržby, typ sestavení a tak dále. Provozní informace, jako je počet cyklů dveří, průměrná dveře zavřít čas. Selhání historie s příčiny.|
|_Selhání Wheel_ | Data snímače, míry hodinových akcelerace, brzdového instancí, podporovat jeho vzdálenost, rychlosti atd. Informace o statických na souborů Wheel jako výrobce, pocházejí datum. Data selhání odvodit z databáze pořadí část sledování pořadí data a počty.|
|_Podzemní dráha train dveře selhání_ | Otevírání dveří a uzavírací dobu, ostatní provozní data, jako jsou aktuální stav train dveří. Statických dat bude zahrnovat identifikátor prostředku, čas a podmínky hodnoty sloupců.|

### <a name="data-types"></a>Typy dat
Zadaný výše uvedených zdrojů dat, se dvě hlavní datové typy zjištěnými v PdM domény:

- _Dočasná data_: provozní telemetrie, počítač podmínky, typy pořadí pracovních, priority kódy, které budou mít časová razítka v době záznam. Časová razítka přidružená každé události bude mít i chyby, údržby nebo opravit a historie využití.
- _Statických dat_: funkce počítačů a operátor funkce jsou obecně statické vzhledem k tomu, že popisují technických specifikací počítače nebo operátor atributy. Pokud tyto funkce může postupně měnit, by měly mít také časová razítka s nimi spojených.

Předpověď a cíle proměnné by měla být zpracované nebo transformovat na [číselné, kategorií a jiné datové typy](https://www.statsdirect.com/help/basics/measurement_scales.htm) v závislosti na algoritmus používá.

### <a name="data-preprocessing"></a>Data předběžného zpracování
Aby bylo _konstruování_, Příprava dat z různých datových proudů k vytváření schématu, ze kterého je snadné sestavení funkce. Vizualizace dat nejprve jako tabulky záznamů. Každý řádek v tabulce představuje instanci školení a sloupce, které představují _předpověď_ funkcí (také nazývané nezávislé atributy nebo proměnné). Uspořádání dat tak, aby byla posledního sloupce _cíl_ (závislé proměnné). Pro každou instanci školení přiřadit _popisek_ jako hodnotu v tomto sloupci.

Pro dočasná data rozdělení do jednotky doby trvání data snímačů. Každý záznam by měly patřit do časovou jednotku pro určitý prostředek _a měli nabízejí jedinečné informace_. Jednotky doby jsou definovány podle obchodních potřeb v násobcích sekund, minut, hodin, dnů, měsíce, a tak dále. Časová jednotka _nemusí být stejný jako frekvence shromažďování dat_. Pokud frekvence vysoká, data nemusí zobrazit žádné významné rozdíly z jedné jednotky na druhý. Předpokládejme například, že o teplotě nebyla shromážděna každých 10 sekund. Použití tom samém intervalu pro Cvičná data pouze zvýšení kapacity počet příklady bez zadání jakýchkoli dalších informací. Pro tento případ může být strategie lepší použití průměr na obchodního oprávnění na základě dat více než 10 minut, nebo jednu hodinu.

Pro statických dat
- _Zaznamenává údržby_: data nezpracovaná údržby má identifikátor asset a časové razítko s informacemi o údržby aktivity, které prováděly k danému bodu v čase. Transformace aktivity údržby do _kategorií_ sloupců, kde každé kategorie popisovač jednoznačně mapuje konkrétní údržby akce. Schéma pro záznamy údržby by mělo zahrnovat identifikátor prostředku, čas a akce údržby.

- _Selhání záznamy_: selhání nebo z důvodů chyby lze zaznamenat jako jednotlivých kódů chyb nebo selhání událostí podle konkrétních podnikových podmínky. V případech, kdy zařízení obsahuje více kódy chyb by měly domény odborné pomoci, identifikujte ty, které jsou relevantní pro Cílová proměnná. Použití zbývající kódy chyb nebo podmínky pro vytvoření _předpověď_ funkce, které korelovat s tyto chyby. Schéma pro záznamy selhání by mělo zahrnovat identifikátor prostředku, čas, selhání nebo důvod selhání – Pokud je k dispozici.

- _Počítač a operátor metadata_: sloučení dat počítače a operátor do jedno schéma pro přidružení prostředek jeho operátor spolu s jejich příslušné atributy. Schéma pro počítač podmínky by zahrnout identifikátor prostředku, funkce asset, operátor funkce a operátor identifikátor.

Další data předběžného zpracování kroky obsahovat _zpracování chybějící hodnoty_ a _normalizaci_ hodnot atributu. Podrobnou diskuzi je nad rámec této příručky – viz další část pro některé důležité údaje.

Pomocí výše uvedeného zpracované zdroje dat v místě, poslední transformaci před inženýrství funkce pro připojení na základě identifikátoru asset a časové razítko tabulek uvedených výše. Výsledná tabulka by obsahovat hodnoty null pro sloupec selhání, pokud je počítač v běžném provozu. Tyto hodnoty null lze nebude splněn podle indikátor pro běžné operace. Tento sloupec selhání slouží k vytvoření _popisky pro prediktivní model_. Další informace najdete v části na [modelování techniky pro prediktivní údržby](#Modeling-techniques-for-predictive-maintenance).

## <a name="feature-engineering"></a>Návrh funkcí
Funkce inženýrství jde o první krok před modelování data. Jeho role v procesu vědecké účely dat [je zde popsán](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/create-features). A _funkce_ je prediktivní atribut pro model – například teploty, přetížení, vibrace a tak dále. Pro PdM zahrnuje funkce inženýrství poskytuje abstrakci stavu počítače a přes historická data shromážděná prostřednictvím značné množství doba trvání. V tomto smysl se liší od jeho partnerské uzly například vzdálené monitorování, detekce anomálií a detekce chyb. 

### <a name="time-windows"></a>Čas windows
Vzdálené monitorování zahrnují reporting události, které dojít od _body v čase_. Modely detekce anomálií vyhodnotit příchozí datové proudy (skóre) dat do příznak anomálií od body v čase. Detekce chyb klasifikuje jako konkrétní typy jako body se vyskytují v době selhání. Naproti tomu PdM zahrnuje predikci selhání přes _budoucí časová období_, podle funkce, které představují počítač chování přes _historických časové období_. Pro PdM jsou příliš aktivní být prediktivní funkce dat z jednotlivých bodů času. Proto musí být data pro každou funkci _smoothened_ agregací datových bodů přes časových oken.

### <a name="lag-features"></a>Funkce Lag funkce
Obchodní požadavky definovat, jak daleko model má k předvídání do budoucna. Pak tato doba trvání pomáhá definovat, jak daleko zpět modelu má hledat' Chcete-li tyto předpovědi. Tento "vyhledávání zpět' období je volána _prodleva_, a funkce, které jsou analyzovány tohoto období prodleva, se nazývají _funkce lag funkce_. Tato část popisuje funkce lag funkce, které lze sestavit ze zdroje dat se časová razítka a vytvoření funkce z statických dat zdrojů. Funkce opoždění jsou obvykle _číselné_ ve své podstatě.

> [!IMPORTANT]
> Velikost okna je určen prostřednictvím experimentování a by měl dokončit za pomoci odborné domény. Přímý přístup do stejné paměti obsahuje pro výběr a definice funkcí prodleva, jejich agregací a typ systému windows.

#### <a name="rolling-aggregates"></a>Vrácení agregace
Pro každý záznam prostředek okno postupného velikosti "W" je se rozhodli jako počet jednotek času výpočetní agregace. Funkce opoždění jsou pak vypočítány pomocí tečky W _před datem_ daného záznamu. Na obrázku 1 blue řádky zobrazit hodnoty čidel zaznamenány pro určitý prostředek pro každou jednotku času. Kumulativní průměr hodnot funkce se označují přes okno velikosti W = 3. Kumulativní průměr se vypočítává přes všechny záznamy časová razítka v rozsahu t<sub>1</sub> (v oranžová) k t<sub>2</sub> (zeleně). Hodnota pro W je obvykle v minutách nebo hodin v závislosti na povaze data. Ale pro některé problémy, výběr velké W (například dobu 12 měsíců) nabízejí celou historii prostředek až do okamžiku záznamu.

![Obrázek 1. Vrácení agregační funkce](./media/cortana-analytics-playbook-predictive-maintenance/rolling-aggregate-features.png) obrázek 1. Vrácení agregační funkce

Příklady převrácení časový interval agregace jsou počet, průměr, míry CUMESUM (kumulativní součet), minimální nebo maximální hodnoty. Kromě toho odchylky, směrodatná odchylka a počet extrémních nad rámec standardních odchylek N se často používají. Příklady agregací, které mohou být použity pro [případy použití](#Sample-PdM-use-cases) v této příručce jsou uvedeny níže. 
- _Letu zpoždění_: počet kódů chyb přes poslední den za týden.
- _Letadla modul část selhání_: vrácení znamená, směrodatná odchylka a součet přes poslední den, týden atd. Tato metrika je třeba určit společně s odborné obchodní domény.
- _Selhání ATM_: vrácení znamená, medián, rozsah, standardních odchylek, počet extrémních nad rámec tři standardních odchylek, horní a dolní CUMESUM.
- _Selhání dveře train podzemní dráha_: počet událostí za posledních den, týden, dva týdny atd.
- _Selhání jistič_: selhání počty přes minulého týdne roku, tři roky atd.

Další užitečné v PdM je zachycení trend změny, špičky a úrovně změny pomocí algoritmů, které zjišťovat anomálie v datech.

#### <a name="tumbling-aggregates"></a>Přeskakující agregace
Pro každý s názvem bez přípony záznam prostředek okno velikosti _W -<sub>tisíc</sub>_  je definován, kde _tisíc_ je počet windows velikosti _W_. Agregace jsou poté jste vytvořili přes _tisíc_ _přeskakující windows_ _W-k, W -<sub>(k-1)</sub>,..., W -<sub>2</sub>, W -<sub>1</sub>_  pro období před časové razítko záznam. _k_ může být malé množství k zachycení krátkodobé důsledky nebo velký počet pro zachycení dlouhodobé snížení vzory. (viz obrázek 2).

![Obrázek 2. Agregační funkce přeskakující](./media/cortana-analytics-playbook-predictive-maintenance/tumbling-aggregate-features.png) na obrázku 2. Přeskakujícího agregační funkce

Například funkce lag funkce pro případ použití turbín větru mohou být vytvořeny s W = 1 a tisíc = 3. Jejich implikují prodleva pro každou z posledních tří měsíců pomocí horní a dolní odlehlé hodnoty.

### <a name="static-features"></a>Statické funkce

Technických specifikací vybavení, jako je například datum výroby, číslo modelu, umístění, jsou některé příklady statické funkce. Jsou považovány za _kategorií_ proměnných pro modelování. Příklady pro případ použití jistič jsou napětí, aktuální, kapacita napájení, typ transformer a zdroji napájení. K selhání wheel typ můžete zadat souborů Wheel (legované vs oceli) je příklad.

Úsilí přípravu dat popsané, pokud by měla vést k dat probíhá uspořádané, jak je uvedeno níže. Školení, testování a ověření dat musí mít toto logické schéma (Tento příklad zobrazuje čas v jednotkách dní).

| ID assetu | Čas | <Feature Columns> | Štítek |
| ---- | ---- | --- | --- |
| A123 |1 den | . . . | . |
| A123 |Den 2 | . . . | . |
| ...  |...   | . . . | . |
| B234 |1 den | . . . | . |
| B234 |Den 2 | . . . | . |
| ...  |...   | . . . | . |

Posledním krokem v inženýrství funkce je **označování** z Cílová proměnná. Tento proces je závislý na technika modelování. Pak modelování techniku, závisí na obchodního problému a povaha všechna dostupná data. Označování je popsané v další části.

> [!IMPORTANT]
> Příprava dat a funkce technici jsou důležité jako modelování techniky k přicházejí na úspěšné PdM řešení. Expert domény a specialisty by měl investovat déle správné funkce a dat pro model. Malé ukázkové z mnoha knih na funkci technici jsou uvedeny níže:
> - Pyle, Příprava D. dat pro dolování (Nováková Kaufmann řady v systémy správy dat), 1999 dat.
> - Casari Zheng A., technici A. funkce pro Machine Learning: Principy a techniky pro datových vědců O'Reilly, 2018.
> - Dong, G. Liu H. (Editory), funkce, technici pro Machine Learning a analýzy dat (Chapmanova & Hall/CRC dolování dat a řady zjišťování znalostní báze), stiskněte CRC 2018.

## <a name="modeling-techniques-for-predictive-maintenance"></a>Modelování techniky pro prediktivní údržby

Tato část popisuje hlavní modelování techniky pro PdM problémy, spolu s jejich metody vytváření konkrétní štítek. Všimněte si, že jeden modelování postup lze použít napříč různými odvětvími. Modelování technika je spárován do problém vědecké účely dat, nikoli kontextu dat po ruce.

> [!IMPORTANT]
> Volba štítky pro případy selhání a označování strategie  
> je třeba určovat konzultaci s doménou odborné.

### <a name="binary-classification"></a>binární klasifikace
Binární klasifikace se používá ke _předpovědi pravděpodobnost, že zařízení, selže a v budoucích časovém intervalu_ – zavolat _budoucí horizon období X_. X je určen podle obchodního problému a data, ve spolupráci s odborné domény. Mezi příklady patří:
- _minimální doba realizace_ potřeby nahradit součásti nasazení údržby prostředků, provedení údržby, aby nedošlo k problému, který může dojít v tomto období.
- _minimální počet událostí_ , může dojít předtím, než dojde k potížím.

V této technice jsou identifikovány dva typy školení příklady. V příkladu kladné _označující selhání_, s popiskem = 1. Záporné příklad, který označuje normální provozní podmínky, s popiskem = 0. Cílová proměnná, a proto jsou hodnoty popisků _kategorií_. Model měli identifikovat každý nový příklad jako pravděpodobně selhat nebo pracovat normálně přes další X jednotky doby.

#### <a name="label-construction-for-binary-classification"></a>Popisek konstrukce pro binární klasifikaci
Je zde na otázku: "co je pravděpodobnost, že asset selže v dalším X jednotkami času?" K odpovědi na tuto otázku, popisek X záznamů před selháním prostředek jako "o na selhání" (popisek = 1) a označovat všechny záznamy, že je "normální" (Popisek = 0). (viz obrázek 3).

![Obrázek 3. Označování pro binární klasifikaci](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-binary-classification.png) obrázku 3. Označování pro binární klasifikaci

Níže jsou uvedeny příklady označování strategie pro některé případy použití.
- _Letu zpoždění_: X může být zvolen jako 1 den, k předvídání zpoždění v dalších 24 hodin. Potom všechny lety, které jsou v rámci 24 hodin, než selhání jsou označeny jako 1.
- _Selhání obejít peněžních ATM bez_: Cílem může být k určení pravděpodobnosti selhání transakce za další jednu hodinu. V takovém případě jsou všechny transakce, které došlo během poslední hodiny selhání označeny jako 1. K předvídání pravděpodobnost selhání přes další měna N poznámky distribuován, všechny poznámky distribuován v rámci poslední poznámky N selhání jsou označeny jako 1.
- _Selhání jistič_: Cílem může být k další chybě příkazu jistič předpovědi. V takovém případě je jako jeden příkaz budoucí vybrali X.
- _Cvičení dveře selhání_: X může být zvolen jako dva dny.
- _Větru turbína selhání_: X může být zvolen jako dvou měsíců.

### <a name="regression-for-predictive-maintenance"></a>Regrese pro prediktivní údržby
Modely regrese se používá ke _výpočetní zbývající dobu životnosti (RUL) prostředek_. RUL je definován jako množství času, který prostředek je funkční, než dojde k další chybě. Každý příkladem školení je záznam, který patří do časovou jednotku _nY_ pro určitý prostředek, kde _n_ je násobek. Model byste měli vypočítat zbývající životnost jednotlivých nové příkladu jako _průběžné číslo_. Toto číslo označuje dobu, po zbývající před selháním.

#### <a name="label-construction-for-regression"></a>Popisek konstrukce pro regresní
Je zde na otázku: "Co je zbývající užitečné životnosti (RUL) zařízení?" Pro každý záznam před selháním vypočítejte štítek, který chcete být počet jednotek zbývající před selháním další čas. Tato metoda popisky jsou průběžné proměnné. (Viz obrázek 4)

![Obrázek 4. Označování pro regresní](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-regression.png) obrázek 4. Označování pro regresní

Pro regresní označování provádí nese odkaz na bod selhání. Výpočet jeho není možné bez znalosti, jak dlouho asset má zůstal naživu před selhání. Proto na rozdíl od binární klasifikace prostředky bez jakýchkoliv potíží v datech nelze používat pro modelování. Tento problém je nejlépe používala jiné statistické techniku zvanou [základními Analysis](https://en.wikipedia.org/wiki/Survival_analysis). Ale potenciální komplikace mohou nastat při použití Tato technika PdM případy použití, které se týkají různých čas dat pomocí pravidelných intervalech. Další informace o základními analýzy, najdete v části [tomto jeden pager](https://www.cscu.cornell.edu/news/statnews/stnews78.pdf).

### <a name="multi-class-classification-for-predictive-maintenance"></a>Klasifikace více tříd pro prediktivní údržby
Techniky klasifikace více třídy lze použít v PdM řešení pro dva scénáře:
- Předpověď _dva budoucí výsledky_: první výsledek je _časového údaje k selhání_ pro určitý prostředek. Asset se přiřadí k jednomu z několika možných období. Druhý výsledek je pravděpodobnost selhání v budoucí období kvůli _jedním z několika kořenové způsobí, že_. Tato předpovědi umožňuje tým údržby si chcete přehrát příznaky a plán údržby plánů.
- Předpověď _nejpravděpodobnější příčiny_ dané selhání. Tento výsledek doporučuje správnou sadu akce údržby vyřešit selhání. Seřazený seznam hlavní příčiny a doporučené opravy může pomoci technici prioritu jejich oprava akce po selhání.

#### <a name="label-construction-for-multi-class-classification"></a>Popisek konstrukce pro klasifikaci s více – třída
Je zde na otázku: "co je pravděpodobnost, že prostředek selže v dalším _nZ_ jednotkami času kde _n_ je počet období?" Na tuto otázku odpovědět, označení nZ záznamy před selháním prostředek pomocí sad času (3Z, 2Z, Z). Popisek všechny ostatní zaznamenává "normální" (Popisek = 0). Tato metoda obsahuje cílová proměnná _kategorií_ hodnoty. (Viz obrázek 5).

![Obrázek 5. Označování pro více třídami klasifikaci pro předpověď časové selhání](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-multiclass-classification-for-failure-time-prediction.png) obrázek 5. Označování pro klasifikaci s více tříd pro předpověď časové selhání

Je zde na otázku: "co je pravděpodobnost, že asset selže v dalším X jednotkami času z důvodu příčina nebo problém nevyřeší _P<sub>i</sub>_?" kde _i_ je počet možných příčinách. K odpovědi na tuto otázku, popisek X záznamů před selháním prostředek jako "o nezdaří z důvodu příčiny _P<sub>i</sub>_" (popisek = _P<sub>i</sub>_). Označení všechny záznamy, že je "normální" (Popisek = 0). Tato metoda také popisky jsou kategorií (viz obrázek 6).

![Obrázek 6. Označování pro více třídami klasifikaci pro kořenové příčina předpovědi](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-multiclass-classification-for-root-cause-prediction.png) obrázek 6. Označování pro klasifikaci více tříd pro kořenové příčina předpovědi

Model přiřadí pravděpodobnost selhání kvůli každý _P<sub>i</sub>_  i pravděpodobnost bez chyby. Tyto pravděpodobnostech lze provést řazení podle rozsahem umožňující předpovědi problémů, které jsou v budoucnu dochází nejčastěji.

Je zde na otázku: "jaké akce údržby Doporučujete po selhání?" Na tuto otázku odpovědět označování _nevyžaduje budoucí horizon má být vybráno_, protože model není v budoucnu predikci selhání. Ho je právě predikci s největší pravděpodobností příčiny _po selhání bylo provedeno_.

## <a name="training-validation-and-testing-methods-for-predictive-maintenance"></a>Školení, ověřování a testování metody pro prediktivní údržby
[Proces vědecké účely dat Team](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview) poskytuje úplné vysvětlení cyklu train test ověření modelu. Tato část popisuje aspekty, které jsou jedinečné pro PdM.

### <a name="cross-validation"></a>Křížové ověření
Cílem [křížové ověření](https://en.wikipedia.org/wiki/Cross-validation_(statistics)) je k definování sady dat na "test" modelu ve fázi školení. Tato datová sada je volána _ověření sadu_. Tato technika pomáhá limit problémy jako _overfitting_ a dává přehledu na tom, jak bude modelu generalize pro nezávislé datové sady. To znamená neznámé dat nastavit, které by mohly být od skutečné problém. Rutiny pro PdM školení a testování musí vzít v úvahu dobu různých aspektů lépe generalize neviditelný budoucí data.

Mnoho algoritmy strojového učení závisí na několika hyperparameters, který může významně změní výkon modelu. Optimální hodnoty těchto hyperparameters nejsou automaticky vypočítávají při tréninku modelu. Musí být určena vědecký pracovník data. Nalezení správné hodnoty hyperparameters několika způsoby.

Nejběžnější jeden je _tisíc skládání křížové ověření_ , rozdělí příklady náhodně do _tisíc_ složení. Pro každou sadu hodnot hyperparameters spustit algoritmus učení _tisíc_ časy. Při každé iteraci použijte v příkladech v aktuální násobek jako sada ověření a zbytek v příkladech jako sada školení. Učení algoritmu přes příklady školení a výpočetním metriky výkonu přes příklady ověření. Na konci této smyčky, výpočetní průměr _tisíc_ metrik výkonu. Pro každou sadu hodnot hyperparameter vyberte ty, které se průměrná nejlepší výkon. Výběr hyperparameters je často experimentální ve své podstatě.

V PdM problémy se data zaznamená jako časové řady událostí, které pocházejí z několika zdrojů dat. Tyto záznamy může provést řazení podle času zobrazení popisků. Proto pokud datová sada je rozdělená _náhodně_ do sady školení a ověření _některé příklady školení může být později v čase než některé příklady ověření_. Budoucí výkonu hyperparameter hodnot bude dá odhadnout na základě některé dat, které byly přijaty _před_ byl cvičení modelu. Tyto odhady může být příliš optimistickou metodu, zejména v případě, že časové řady není stojící a vyvíjí v průběhu času. V důsledku toho mohou být hodnoty zvolené hyperparameter zhoršené.

Doporučený způsob je příklady rozdělením školení a nastavit ověření _závislá na čase_ způsobem, kde jsou všechny příklady ověření v čase pozdější, než všechny příklady školení. Pro každou sadu hodnot hyperparameter učení algoritmu přes trénovací datové sady. Měření výkonu modelu přes stejnou sadu ověření. Zvolte hyperparameter hodnoty, které se zobrazí nejlepší výkon. Hodnoty Hyperparameter zvolené train a ověření rozdělení vyústí v lépe budoucí modelu výkon než s hodnotami náhodně vybere podle křížové ověření.

Poslední modelu může být generována cvičení algoritmu učení přes celou Cvičná data pomocí nejlepší hyperparameter hodnot.

### <a name="testing-for-model-performance"></a>Testování výkonu modelu
Jakmile se model sestavuje, je vyžadován odhad jeho budoucí výkon na nová data. Dobrou odhadnout, je metrika výkonu hodnot hyperparameter vypočítán na sadu ověření nebo metriku výkonu průměrná vypočítaný z křížové ověření. Tyto odhady jsou často příliš optimistickou metodu. Firmy často může mít některé další pokyny o tom, jak se mají otestování modelu.

Doporučený způsob pro PdM je rozdělením školení, ověření, příklady a testovací datových sad _závislá na čase_ způsobem. Všechny příklady test by měl následovat v čase všechny příklady školení a ověření. Po rozdělení generování modelu a měření jeho výkonu, jak je popsáno výše.

Když časové řady místě a snadno předpovědět, náhodné i závislá na čase přístupy generovat podobné Odhady budoucí výkonu. Ale když časové řady-stojícího vozidla nebo snadno předpovědět, bude přístup závislá na čase generovat realističtější odhad budoucích výkonu.

### <a name="time-dependent-split"></a>Rozdělení závislá na čase
Tato část popisuje osvědčené postupy pro implementaci rozdělení závislá na čase. Rozdělení obousměrný závislá na čase mezi výukových a testovacích sad je popsáno níže.

Předpokládejme označen časovým razítkem události například měření z různých snímače datového proudu. Definování funkcí a popisky školení a příklady testovací přes časových intervalů, které obsahují více událostí. Pro binární klasifikaci, například vytvoření funkce na základě posledních událostí a vytvořit štítky na základě budoucí událostí v rámci "X" jednotky čas v budoucnosti (najdete v částech na [konstruování](#Feature-engineering) a [modelování techniky](#Modeling-techniques-applied-to-PdM-use-cases)). Proto označování časový rámec příkladu obsahuje později než časový rámec jeho funkcí.

Pro rozdělení závislá na čase, vyberte _cvičení čas konečného T<sub>c</sub>_  , kdy má být trénování modelu, s hyperparameters přizpůsobená pomocí historických dat až T<sub>c</sub>. Aby se zabránilo úniku budoucí popisky, které jsou nad rámec T<sub>c</sub> do Cvičná data, zvolte nejnovější doba popisek školení příklady jako X jednotky před T<sub>c</sub>. V příkladu na obrázku 7 představuje každý čtvereček záznam v datové sadě, kde funkce a popisky se vypočítávají jak bylo popsáno výše. Obrázek zobrazuje záznamy, které patří do trénování a testování sad pro X = 2 a W = 3:

![Obrázek 7. Závislá na čase rozdělení pro binární klasifikaci](./media/cortana-analytics-playbook-predictive-maintenance/time-dependent-split-for-binary-classification.png) na obrázku 7. Závislá na čase rozdělení pro binární klasifikaci

Zelená čtverce představují záznamy, které patří do časové jednotky, které lze použít pro školení. Každý příklad školení je generován zvažování posledních tří období pro funkci generování a dvě tečky budoucí pro označování před T<sub>c</sub>. Pokud je libovolná součást dvě tečky budoucí nad rámec T<sub>c</sub>, vyloučit například z trénovací datové sady, protože žádné viditelnost se předpokládá, že nad rámec T<sub>c</sub>.

Černé čtverce představují záznamy konečné s popiskem datovou sadou, která se nesmí použít v trénovací datové sady, přiřazeno výše uvedené omezení. Tyto záznamy nebudou se používat i v testování dat, protože jsou před T<sub>c</sub>. Kromě toho jejich označování časových intervalů částečně závisí na školení časového rámce, což není ideální. Učení a testovací data by měl mít samostatné označování časových intervalů zabránit úniku informací popisku.

Umožňuje technik popsaných dosavadní překryv mezi trénování a testování příklady, které mají časová razítka téměř T<sub>c</sub>. K dosažení vyšší oddělení je vyloučit příklady, které jsou v rámci jednotky doby W t<sub>c</sub> z testu nastavit. Ale agresivní rozdělení závisí na dostupnosti dostatečným data.

Problém úniku více vážně týká regrese modely použité pro predikci RUL. Metodou split náhodných vede k extrémně přečerpání přizpůsobování. V případě problémů regrese by měla být rozdělení tak, aby záznamy, které patří k prostředkům s chybami před T<sub>c</sub> přejděte do trénovací sady. Záznamy o prostředcích, které po úroveň oříznutí s chybami, přejděte do testovací sada.

Další osvědčených postupů pro rozdělení dat pro trénování a testování je použití rozdělení podle ID prostředku. Rozdělení by měla být tak, aby žádné prostředky, které používá sada školení se používají při testování výkonu modelu. Tento přístup má model zvýší možnost poskytování realističtější výsledky nové prostředky.

### <a name="handling-imbalanced-data"></a>Zpracování imbalanced dat
V klasifikaci problémy, pokud existují další příklady jednu třídu než ostatní, datová sada se říká, že _imbalanced_. V ideálním případě by se povolit rozdíl mezi různými třídami upřednostňovaná dostatek zástupců každá třída v Cvičná data. Pokud jedna třída je menší než 10 % dat, data se považuje imbalanced. Underrepresented třídy se nazývá _menšinového třída_.

Mnoho problémů PdM čelí takové imbalanced datové sady, kde je jedna třída vážně underrepresented ve srovnání s další třídu nebo třídy. V některých situacích může představovat třídě menšinových pouze 0,001 % celkový počet datových bodů. Třída nevyváženosti není jedinečný pro PdM. Jiných domén, kdy jsou chyby a anomálie výjimečných výskytů čelí podobný problém, příklady, odhalování podvodů a narušení sítě. Tyto chyby tvoří příklady menšinových tříd.

Pomocí třídy nevyváženosti v datech výkon většinu standardních algoritmů učení v ohrožení, vzhledem k tomu, že jejich cílem minimalizovat míra celkové chyb. Pro datovou sadu s 99 % záporné a kladné příklady 1 % lze zobrazit modelu tak, aby měl 99 % přesnost pomocí označování všechny instance jako záporné. Ale modelu bude nemá klasifikovat všechny kladné příklady; takže i v případě jeho přesnost je vysoká, algoritmus není užitečné. V důsledku toho konvenční vyhodnocení metriky jako _celkové přesnost na míra chyb_ není k dispozici dostatek imbalanced informací. Pokud potýkají s imbalanced datové sady, se použijí jiné metriky pro vyhodnocení modelu:
- přesnost
- Svolat
- F1 skóre
- Náklady na upravena ROC (provozní vlastnosti příjemce)

Další informace o tyto metriky najdete v tématu [modelu vyhodnocení](#Model-evaluation).

Existují však některé metody, které pomáhají nápravě třída nevyváženosti problém. Dva hlavní ty, které jsou _vzorkování techniky_ a _náklady citlivé learning_.

#### <a name="sampling-methods"></a>Metody vzorkování
Imbalanced learning zahrnuje použití vzorkování metody k úpravě školení datovou sadu s vyrovnáváním datovou sadou. Metody vzorkování není má být použita pro testovací sada. I když existuje několik postupů vzorkování, většina splněny následující vlastnosti jsou _náhodných oversampling_ a _pod vzorkování_.

_Náhodné oversampling_ zahrnuje výběru náhodného vzorku ze třídy menšinových, replikace tyto příklady a jejich přidáním do trénovací datové sady. V důsledku toho počet příklady v třídě menšinových je vyšší a nakonec vyvážit počet příklady různých tříd. Nevýhodou oversampling je, že více instancí některé příklady může způsobit třídění k příliš konkrétní, což přečerpání přizpůsobování. Model může zobrazovat školení vysokou přesnost, ale jeho výkon na neviditelný testovací data mohou být zhoršené.

Naopak _náhodných pod vzorkování_ je vyberete z náhodného vzorku od třídy, většinou a odebrání těchto příkladů z trénovací datové sady. Odebrání příklady z třídy většina může způsobit třídění přijít o důležité koncepty týkající se většinou třídy. _Hybridní vzorkování_ kde menšinových třída je povolená jen Vzorkovaná a většina třída je v části vzorkovat ve stejné čas je další přijatelná přístup.

Existuje mnoho sofistikovaných odběry vzorků. Postup vybrali záviset na vlastnosti dat a výsledky iterativní experimenty podle vědecký pracovník data.

#### <a name="cost-sensitive-learning"></a>Náklady citlivé učení
V PdM chyb, které tvoří třídě menšinových jsou důležitější sledovat než normální příklady. Proto je zaměřená především na výkon, že algoritmus o selhání. Nesprávně predikci třídu kladné jako záporné třída může více než naopak náklady. Tato situace se obvykle označuje jako nerovné ztráta nebo asymetrický náklady nemá klasifikace elementů různých tříd. Ideální třídění by měl poskytovat přesnost předpovědi vysoké třídy menšinových bez kompromisů v přesnost pro většinu třídu.

K dosažení této vyrovnávání několika způsoby. Pro zmírnění problém nerovné dojít ke ztrátě, přiřadit chybné klasifikace menšinových třídy vysoké náklady a snažit minimalizovat celkové náklady na. Algoritmy jako _SVMs (Support Vector počítače)_ přijmout tato metoda ze své podstaty, tím, že náklady na kladné a záporné příklady zadat během cvičení. Podobně, jako například zvyšovat skóre metody _boosted decision trees_ obvykle zobrazit dobrý výkon imbalanced daty.

## <a name="model-evaluation"></a>Vyhodnocení modelu
Chybné klasifikace je závažný problém pro PdM scénáře, kde je vysoké náklady na falešné výstrahy pro jejich podnikání. Například můžete rozhodnutí pro pozadí letadel podle nesprávné předpovědi poruchám motorů přerušit plány a cestovní plány. Přepnutím počítače do offline režimu z řádku sestavení může vést ke ztrátě příjmů. Proto je důležité vyhodnocení modelu s metriky správné výkonu pro nová data testu.

Metriky typické výkonu používá k vyhodnocení PdM modely jsou popsané dále:

- [Přesnost](https://en.wikipedia.org/wiki/Accuracy_and_precision) je nejoblíbenější metrika používá k popisu třídění výkonu. Ale přesnost je citlivá na distribuce dat a je neúčinná opatření pro scénáře s imbalanced datových sad. Místo toho se používají jiné metriky. Nástroje, jako [nedorozuměním matice](https://en.wikipedia.org/wiki/Confusion_matrix) se používat k výpočtům a důvodu o přesnosti modelu.
- [Přesnost](https://en.wikipedia.org/wiki/Precision_and_recall) z PdM modely se týkají počet falešných poplachů. Nižší přesnost modelu obecně odpovídá vyšší počet falešných poplachů.
- [Odvolat](https://en.wikipedia.org/wiki/Precision_and_recall) míra označuje počet selhání v testovací sadě byly identifikovány správně modelem. Vyšší rychlosti pro vyvolání to znamenat, že je model úspěšné při identifikaci true selhání.
- [F1 skóre](https://en.wikipedia.org/wiki/F1_score) je harmonické průměr přesnost a odvolání s hodnotou rozmezí od 0 (nejhorší) na hodnotu 1 (nejlepší).

Pro binární klasifikaci
- [Příjemce operační křivek (ROC)](https://en.wikipedia.org/wiki/Receiver_operating_characteristic) je také oblíbených metriky. V křivek ROC interpretována podle jednoho pevné operační bodu ROC výkonu modelu.
- Ale v případě problémů PdM _decile tabulky_ a _navýšení grafy_ jsou informativnější. Soustředit se jenom na kladné – třída (počet selhání) a zadejte složitější přehled o výkonu algoritmus než křivek ROC.
  - _Tabulky decile_ jsou vytvořené pomocí testu příklady v sestupném pořadí pravděpodobnostech selhání. Seřazené ukázky pak seskupeny do deciles (10 % vzorků s nejvyšší pravděpodobnost, pak 20 %, 30 % a tak dále). /(Random baseline) poměr (true kladné rychlost) pro každý decile pomáhá odhadnout výkon algoritmus za každou decile. Náhodné směrného plánu přebírá hodnoty 0.1, 0.2 a tak dále.
  - [Navýšení grafy](http://www2.cs.uregina.ca/~dbd/cs831/notes/lift_chart/lift_chart.html) vykreslení true kladné rychlost decile versus náhodných true kladné rychlost pro všechny deciles. První deciles jsou obvykle fokus výsledky, protože zobrazují největší zvýšení. První deciles najdete i jako reprezentativní pro "na riziko", pokud se používá pro PdM.

## <a name="model-operationalization-for-predictive-maintenance"></a>Model operationalization pro prediktivní údržby

Výhodou cvičení vědecké účely dat je dosaženo, pouze pokud pro cvičný model přišla provozní. To znamená model se musí nasadit do obchodní systémy, které chcete provádět na základě dat o nové, dříve neviditelný předpovědi.  Nová data musí přesně odpovídat _modelu podpis_ vyškolení modelu dvěma způsoby:
- všechny funkce musí být v každé logické instanci (například řádek v tabulce) nová data.
- nová data musí předběžně zpracovat a každou funkci analyzovány přesně stejně jako jsou Cvičná data.

Proces výše uvedená v mnoha směrech v academic a odvětví dokumentace. Ale tyto příkazy mají stejný význam:
- _Skóre pro nová data_ pomocí modelu
- _Použití modelu_ na nová data
- _Zprovoznit_ modelu
- _Nasazení_ modelu
- _Spustit model_ pro nová data

Jak jsme uvedli dříve, operationalization modelu pro PdM se liší od jeho partnerské uzly. Implementovat scénáře zahrnující detekce anomálií a detekce chyb obvykle _online vyhodnocování_ (také nazývané _vyhodnocování v reálném čase_). Tady, modelu _skóre_ každý příchozí záznam a vrátí předpovědi. Pro zjišťování anomálií předpovědi je to znamenat, že došlo k chybě anomálií (Příklad: jedna třída SVM). Pro zjišťování selhání je typu nebo třídy selhání.

Naproti tomu zahrnuje PdM _dávkového vyhodnocování_. Tak, aby odpovídala modelu podpis, musí být analyzovány funkcí v nová data stejným způsobem, jako jsou Cvičná data. Pro velké datové sady, které je typické pro nová data jsou funkce agregován v časových oken a skóre pro magnitudu v dávce. Dávkové vyhodnocování se obvykle provádí v distribuovaných systémů, jako je [Spark](http://spark.apache.org/) nebo [Azure Batch](https://docs.microsoft.com/azure/batch/batch-api-basics). Existuje několik alternativních - i zhoršené:
- Streamování dat stroje podporují agregace přes windows v paměti. Proto může být uvedl, že podporují online vyhodnocování. Ale tyto systémy jsou vhodné pro hustých data v úzké windows čas nebo zhuštěných elementy přes širší windows. Se nemusí škálovat dobře hustých data přes širší časových oken, jak je vidět v PdM scénáře.
- Pokud dávkového vyhodnocování není k dispozici, je řešení přizpůsobit online vyhodnocování zpracování nová data v dávkách malé najednou.

## <a name="solution-templates-for-predictive-maintenance"></a>Řešení šablon pro prediktivní údržby

V poslední části této příručky obsahuje seznam šablon řešení PdM, kurzy a experimenty implementované v Azure. Tyto aplikace PdM lze nasadit do předplatného Azure v rámci minut v některých případech. Použitím jako testování konceptu ukázky, izolovaných prostorů a experimentovat s alternativy nebo akcelerátorů pro skutečné produkční implementace. Tyto šablony jsou umístěny [Azure AI Galerie](http://gallery.azure.ai) nebo [Azure Githubu](https://github.com/Azure). Tyto ukázky různých budou vráceny do této šablony řešení v čase.

| # | Titul | Popis |
|--:|:------|-------------|
| 1 | [Prediktivní údržby Azure Machine Learning ukázka](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance) |Ukázka PdM k předvídání selhání přes další jednotky doby N. Tato ukázka je zapsána jako projekt Azure ML Workbench a je ideální pro začátečníky k PdM. [Další dokumentaci](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/scenario-predictive-maintenance) související této ukázce.|
| 2 | [Šablona řešení prediktivní údržby Azure](https://github.com/Azure/AI-PredictiveMaintenance) | Představuje rozhraní a provést tak kompletní k předvedení více PdM scénářů. Tato šablona představuje dva scénáře: první je nový případ použití klasifikace podmínky selhání v reálném čase. Druhý scénář je jednoduše integrační řešení [1] na tuto šablonu řešení. Ukazuje, jak znovu použít stejnou nasazené infrastrukturu pro přidání dalších nových nebo existujících scénářů.|
| 3 | [Přímý učení pro prediktivní údržby](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance) | Azure poznámkového bloku v rámci řešení pro ukázkové použití sítě LSTM (dlouho krátkodobé paměti) (třída opakující Neuronové sítě) pro prediktivní údržby, s [příspěvku na blogu v této ukázce](https://azure.microsoft.com/blog/deep-learning-for-predictive-maintenance).|
| 4 | [Průvodce modelováním prediktivní údržby v R](https://gallery.azure.ai/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1) | Průvodce modelováním PdM pomocí skriptů v jazyce R.|
| 5 | [Prediktivní údržby Azure pro letecký](https://gallery.azure.ai/Solution/Predictive-Maintenance-for-Aerospace-1) | Jeden z první šablony řešení PdM založené na Azure ML verze 1.0 pro letadla údržby. Tato příručka pochází z tohoto projektu. |
| 6 | [Azure AI nástrojů pro IoT Edge](https://github.com/Azure/ai-toolkit-iot-edge) | AI v edge IoT pomocí TensorFlow; Sada nástrojů balíčky hloubkové učení modely v kontejnerech Docker kompatibilní se službou Azure IoT okraj a vystavit tyto modely jako rozhraní REST API.
| 7 | [Prediktivní údržby Azure IoT](https://github.com/Azure/azure-iot-predictive-maintenance) | Azure IoT Suite počítačů - předkonfigurované řešení. Šablona PdM letadla údržby pomocí sady IoT Suite. [Další dokument](https://docs.microsoft.com/azure/iot-suite/iot-suite-predictive-overview) a [návod](https://docs.microsoft.com/azure/iot-suite/iot-suite-predictive-walkthrough) související s stejného projektu. |
| 8 | [Šablona prediktivní údržby pomocí služby SQL Server R Services](https://gallery.azure.ai/Tutorial/Predictive-Maintenance-Template-with-SQL-Server-R-Services-1) | Ukázku podle R zbývající dobu životnosti scénáře. |
| 9 | [Průvodce modelováním prediktivní údržby](https://gallery.azure.ai/Collection/Predictive-Maintenance-Modelling-Guide-1) | Letadla údržby datovou sadu funkce analýzy využití R s [experimenty](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Modelling-Guide-Experiment-1) a [datové sady](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Modelling-Guide-Data-Sets-1) a [Azure poznámkového bloku](https://gallery.azure.ai/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1) a [experimenty](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Step-1-of-3-data-preparation-and-feature-engineering-2)v AzureML verze 1.0|

## <a name="training-resources-for-predictive-maintenance"></a>Školení prostředky pro prediktivní údržby

[Azure AI studijní prediktivní údržby](https://github.com/Azure/AI-PredictiveMaintenance/blob/master/docs/azure-ai-learning-path-for-predictive-maintenance.md) poskytuje školicí materiály pro lepší představu o konceptech a matematické za algoritmy a postupy, které jsou používány PdM problémy. 

Microsoft Azure nabízí bezplatné obsah a školení na obecné koncepty AI a postup.

| Školení prostředků  | Dostupnost |
|:-------------------|--------------|
| [AI vývojáře v Azure](http://azure.microsoft.com/training/learning-paths/azure-ai-developer) | Public |
| [Školní Microsoft AI](http://aischool.microsoft.com/learning-paths) | Public |
| [Learning Azure AI z Githubu](http://azure.github.io/learnanalytics/public) | Public |
| [LinkedIn Learning](http://www.linkedin.com/learning) | Public |
| [Microsoft AI Youtube Webináře](https://www.youtube.com/watch?v=NvrH7_KKzoM&t=4s) | Public |
| [Zobrazit Microsoft AI](http://channel9.msdn.com/Shows/AI-Show) | Public |
| [LearnAI@MS](http://learnanalytics.microsoft.com) | Pro partnery společnosti Microsoft |
| [Microsoft Partner Network](http://learningportal.microsoft.com) | Pro partnery společnosti Microsoft |

Kromě toho jsou volné MOOCS (masivní otevřené online kurzy) na AI online nabízený academic instituce jako Stanford a MIT a dalších výukových společnosti.