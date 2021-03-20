---
title: Etika a zodpovědné osoba – individuální použití
titleSuffix: Azure Cognitive Services
description: Tyto pokyny jsou zaměřené na to, abychom vám pomohli implementovat přizpůsobení způsobem, který vám pomůže vytvořit důvěryhodnost ve vaší společnosti a službě. Nezapomeňte pozastavit na výzkum, dozvědět se a úmyslně ovlivnit dopad přizpůsobení na život v lidech. V případě pochybností vyhledejte pokyny.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 06/12/2019
ms.openlocfilehash: 602da28f0c235fb0e797a493bc1160631c042a9d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96005975"
---
# <a name="guidelines-for-responsible-implementation-of-personalizer"></a>Pokyny pro odpovědnou implementaci přizpůsobeného přizpůsobování

Pro lidi a firmy, které využijí plný potenciál AI, se implementace musí navrhovat takovým způsobem, že získají vztah důvěryhodnosti těch, kteří přidávají do svých aplikací AI, a uživatele aplikací vytvořených pomocí AI. Tyto pokyny jsou zaměřené na pomoc při implementaci přizpůsobeného přizpůsobování způsobem, který vám pomůže vytvořit důvěryhodnost ve vaší společnosti a službě. Nezapomeňte pozastavit na výzkum, dozvědět se a úmyslně ovlivnit dopad přizpůsobení na život v lidech. V případě pochybností vyhledejte pokyny.

Tyto pokyny nejsou určené jako právní rady a měli byste samostatně zajistit, aby vaše aplikace splňovala rychlé požadavky na vývoj v této oblasti a ve vašem sektoru.

V návrhu aplikace pomocí přizpůsobení byste také měli vzít v úvahu širokou škálu odpovědností, které máte při vývoji jakéhokoli systému souborů AI zaměřeného na data, včetně etiky, ochrany osobních údajů, zabezpečení, bezpečnosti, zahrnutí, transparentnosti a zodpovědnosti. Další informace o těchto možnostech najdete v části [Doporučené čtení](#recommended-reading) .

Následující obsah můžete použít jako kontrolní seznam Starter a přizpůsobit ho a Upřesnit ve svém scénáři. Tento dokument má dvě hlavní části: první je vyhrazený pro zdůraznění zodpovědnosti za použití při výběru scénářů, funkcí a odměny pro přizpůsobování. Druhý při sestavování systémů AI by měl brát v úvahu sadu hodnot, které Microsoft považuje za vhodné a poskytuje užitečné návrhy a rizika týkající se toho, jak se jejich používání dá přizpůsobit.


## <a name="your-responsibility"></a>Vaše zodpovědnost

Všechny pokyny pro zodpovědnost v rámci zodpovědné implementace v základu, které vývojáři a firmy pomocí přizpůsobeného přizpůsobují, jsou zodpovědné a mají dopad na použití těchto algoritmů ve společnosti. Pokud vyvíjíte aplikaci, kterou bude vaše organizace nasazovat, měli byste pochopit svou roli a zodpovědnost za její provoz a vliv na uživatele. Pokud navrhujete aplikaci, kterou má nasadit třetí strana, přinesete se ke sdílenému porozumění s tím, kdo je nakonec zodpovědný za chování aplikace, a dokument, který se bude rozumět.

Vztah důvěryhodnosti je založený na pojmu splněné závazky – uvažujte uživatele, společnost a právní rámec, ve kterém vaše aplikace fungují, a Identifikujte tak explicitní a implicitní závazky, které mohou mít.

Microsoft průběžně zapisuje do svých nástrojů a dokumentů informace, které vám pomůžou s těmito odpovědnostmi působit. [Poskytněte Microsoftu zpětnou vazbu](mailto:cogsvcs-RL-feedback@microsoft.com?subject%3DPersonalizer%20Responsible%20Use%20Feedback&body%3D%5BPlease%20share%20any%20question%2C%20idea%20or%20concern%5D) , pokud si myslíte další nástroje, funkce produktu a dokumenty, které vám pomůžou při implementaci těchto pokynů k používání přizpůsobeného přizpůsobování.


## <a name="factors-for-responsibly-implementing-personalizer"></a>Faktory pro zodpovědnou implementující přizpůsobení

Implementací přizpůsobené aplikace může být pro uživatele i vaše podnikání skvělé. K implementaci přizpůsobeného zodpovědnou začněte zvážením následujících pokynů:

* Výběr případů použití pro použití přizpůsobení.
* Vytváření [funkcí odměňování](concept-rewards.md).
* Výběr [funkcí](concepts-features.md) kontextu a možných akcí, které budete používat pro přizpůsobení.


## <a name="choosing-use-cases-for-personalizer"></a>Výběr případů použití pro přizpůsobování

Používání služby, která se učí přizpůsobení obsahu a uživatelských rozhraní, je užitečné. Může být také nepoužit, pokud způsob přizpůsobení vytvoří negativní vedlejší účinky v reálném světě, včetně toho, pokud uživatelé nevědí o přizpůsobení obsahu.

Příklady použití přizpůsobeného přizpůsobeného doplňku s vyšším možným dopadem na negativní vedlejší účinky nebo nedostatek transparentnosti jsou scénáře, kdy "odměňování" závisí na mnoha dlouhodobých složitých faktorech, které při přestupnosti na okamžitou odměnu můžou mít neuspokojivé výsledky pro jednotlivce. Ty se obvykle považují za "následné" možnosti nebo volby, které zahrnují riziko poškození. Například:


* **Finance**: individuální nastavení nabízí úvěrové, finanční a pojišťovací produkty, kde jsou rizikové faktory založené na datech, o kterých jednotliví uživatelé neznají, nemůžou získat ani nesporit.
* **Vzdělávání**: přizpůsobení pořadí pro školní kurzy a vzdělávací instituce, kde doporučení můžou šířit bias a snižovat povědomí uživatelů o dalších možnostech.
* **Demokracie a Civic zapojení**: přizpůsobení obsahu pro uživatele s cílem vzájemně se ovlivňujících stanovisek je následné a manipulace.
* **Vyhodnocení odměňování na základě třetí strany**: přizpůsobení položek, kde je odměna založená na hodnocení uživatele třetí strany, namísto toho, aby se nemuselo vygenerovat vlastní chování uživatele.
* **Netolerance pro zkoumání**: jakákoli situace, kdy chování při průzkumu přizpůsobeného může způsobit škodu.

Při volbě možnosti použít případy pro přizpůsobování:

* Spusťte proces návrhu, který zvažuje způsob, jakým přizpůsobení pomáhá uživatelům.
* Zvažte negativní následky v reálném světě, pokud se některé položky neberou v úvahu pro uživatele z důvodu způsobů přizpůsobení nebo průzkumu.
* Zvažte, zda váš případ použití představuje automatizované zpracování, které významně ovlivňuje předměty údajů, které jsou regulovány v [GDPR](https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX:32016R0679) článku 22 nebo jinými zákony.
* Zvažte, jestli splňujete Prophecy smyčky. K tomu může dojít, pokud přizpůsobení neplatí pro určitý model, takže potom může další vyloučení demografické skupiny z přístupu k relevantnímu obsahu. Například většina lidí v okolních rozkladech nezískala prémiovou nabídku Premium a pomalu nikdo z okolního dobývá v případě, že není k dispozici dostatek průzkumu.
* Uložení kopií modelů a zásad učení v případě, že je potřeba reprodukování přizpůsobeného přizpůsobování v budoucnu. To můžete provést pravidelně nebo v každé době aktualizace modelu.
* Vezměte v úvahu úroveň průzkumu, která je vhodná pro prostor a jak ji používat jako nástroj ke zmírnění efektů "echo komory".


## <a name="selecting-features-for-personalizer"></a>Výběr funkcí pro přizpůsobování

Přizpůsobení obsahu závisí na tom, jaké jsou informace o obsahu a uživateli užitečné. Mějte na paměti, že pro některé aplikace a odvětví můžou být některé uživatelské funkce přímo nebo nepřímo považované za diskriminační a potenciálně nelegální.

Vezměte v úvahu účinek těchto funkcí:

* **Demografické údaje uživatele**: funkce týkající se pohlaví, pohlaví, stáří, rasy, náboženství: tyto funkce nemusí být v určitých aplikacích povoleny, a proto není možné je přizpůsobovat, protože přizpůsobení by rozšířila generalizace a posun. Příkladem tohoto rozšíření bias je odeslání úlohy pro inženýry, které se nezobrazují na starší skupiny nebo cílové skupiny na základě pohlaví.
* **Informace o národním prostředí**: na mnoha místech světa může být informace o poloze (například PSČ, poštovní směrovací číslo nebo název okolí) vysoce korelační s příjmy, rasy a náboženství.
* **Povědomí uživatelů o spravedlivosti**: dokonce i v případech, kdy vaše aplikace provádí zvuková rozhodnutí, je vhodné zvážit účinek uživatelů, kteří si vyrozumí, že obsah zobrazený v aplikaci se mění způsobem, který se zdá být koreluje s funkcemi, které by byly diskriminační.
* **Nezamýšlené posunutí funkcí**: existují typy posunů, které mohou být zavedeny pomocí funkcí, které ovlivňují pouze podmnožinu populace. To vyžaduje další pozornost při generování funkcí algorithmically, například při použití analýzy obrázků k extrakci položek v obrázku nebo v analýze textu pro zjišťování entit v textu. Zajistěte si informace o vlastnostech služeb, které používáte k vytváření těchto funkcí.

Při volbě funkcí pro odeslání v kontextech a akcích k přizpůsobování použijte následující postupy:

* Vezměte v úvahu právní a etické používání určitých funkcí u některých aplikací a to, jestli Innocent funkce můžou být proxy pro jiné, na které chcete nebo se chcete vyhnout,
* Být transparentní pro uživatele, kteří používají algoritmy a analýzu dat k přizpůsobení možností, které vidí.
* Zeptejte se sami: mají moji uživatelé starosti a budou spokojeni, pokud jste použili tyto informace k přizpůsobení obsahu pro ně? Jsem si spokojeni s tím, jak se rozhodnutí provedlo k zvýraznění nebo skrytí určitých položek?
* Použijte chování namísto klasifikace nebo dat segmentace na základě jiných vlastností. Demografické informace byly tradičně využívány prodejci z historických důvodů – demografické atributy se zdály být jednoduché ke shromáždění a jednání před digitálním obdobím. – ale otázky, které jsou relevantní demografické údaje, jsou v případě, že máte skutečná interakce, kontextová a historická data, která se podrobněji vztahují na preference a identitu uživatelů.
* Zvažte, jak zabránit tomu, aby se funkce zfalšovaným uživatelům, kteří by využili ve velkých číslech, mohly vyškolit přizpůsobeným způsobem, aby záměrně narušení, embarrass a obtěžování určitých tříd uživatelů.
* Pokud je to vhodné a proveditelné, navrhněte svou aplikaci tak, aby uživatelům umožnila výslovný souhlas nebo odsouhlasení se používáním určitých osobních funkcí. Ty by mohly být seskupené, například "informace o umístění", "informace o zařízení", "historie minulých nákupů" atd.


## <a name="computing-rewards-for-personalizer"></a>Výpočetní ceny pro přizpůsobeného přizpůsobování

Přizpůsobený modul se snaží zlepšit možnosti, které se mají použít, a to na základě skóre, které poskytuje vaše aplikační obchodní logika.

Dobře sestavené skóre odměňování bude fungovat jako krátkodobý proxy server pro obchodní cíl, který je svázán se posláním organizace.

Například odměna při kliknutí umožní službě přizpůsobeného vyhledávání kliknout na náklady na všechno ostatní, i když se na to, co se klikne, odvolává nebo není vázáno na obchodní výsledek.

V případě, že se jedná o podobný příklad, může chtít, aby se na webu příspěvky nastavily nevýznamy smysluplnější než kliknutí, například "došlo k tomu, že uživatel stráví dostatek času na čtení obsahu?" "Při kliknutí na relevantní články nebo odkazy?". S přidaným nástrojem je snadné spojit metriky s využitím nerovnosti. Ale buďte opatrní, abyste zmařit krátkodobou zapojení uživatelů s dobrými výsledky.

### <a name="unintended-consequences-from-reward-scores"></a>Nezamýšlené důsledky z výsledků záměna
Skóre odměňování může být sestavena s nejlepšími záměry, ale stále může vytvořit neočekávané následky nebo neočekávané výsledky, jak přizpůsobování přiřadí obsah.

Představte si následující příklady:

* Motivace přizpůsobení obsahu videa na základě procentuální hodnoty sledované délky videa bude pravděpodobně zařadit kratší videa.
* Odměňování sdílených složek sociálních médií, aniž by došlo k mínění analýze obsahu nebo samotného obsahu, může vést k tomu, že má za následek hodnocení urážlivého, nemoderovaného nebo inflammatoryého obsahu, který v úmyslu zamýšlí Incite spoustu "zapojení", ale přidává málo hodnot.
* Odměňování akce s prvky uživatelského rozhraní, které uživatelé neočekávají, můžou narušovat použitelnost a předvídatelnost uživatelského rozhraní, kde se tlačítka překvapivě mění umístění nebo účelu bez upozornění. díky tomu jsou pro určité skupiny uživatelů obtížnější, aby zůstaly produktivní.

Implementujte tyto osvědčené postupy:

* Spouštějte v systému offline experimenty s využitím různých přístupů k disměna, abyste pochopili dopad a vedlejší účinky.
* Vyhodnoťte své funkce odměňování a zeptejte se, jak by to znamenalo extrémně Naive osobu, která prostihla svůj výklad a dosáhla nežádoucích výsledků.


## <a name="responsible-design-considerations"></a>Zodpovědné požadavky na návrh

Níže jsou uvedené oblasti návrhu pro zodpovědné implementace AI. Další informace sousedit s tuto architekturu v [budoucím vypočítaném](https://news.microsoft.com/futurecomputed/).

![Hodnoty AI z budoucího vypočítaného](media/ethics-and-responsible-use/ai-values-future-computed.png)

### <a name="accountability"></a>Odpovědnost
*Lidé, kteří navrhují a nasazují systémy AI, musí být pro fungování svých systémů vhodné*.

* Vytvořte interní pokyny pro implementaci přizpůsobeného, dokumentování a předávání IT týmu, vedoucím pracovníkům a dodavatelům.
* Provádějte pravidelné kontroly toho, jak se počítají skóre odměňování, proveďte offline vyhodnocení a zjistěte, jaké funkce ovlivňují přizpůsobené možnosti, a výsledky použijte k odstranění nepotřebných a nepotřebných funkcí.
* Informujte uživatele, jak se přizpůsobuje použití, k jakým účelu a s jakými daty.
* Archivace informací a prostředků – například modelů, studijních zásad a dalších dat – tato přizpůsobená funkce používá funkci, aby mohla reprodukování výsledků reprodukuje.

### <a name="transparency"></a>Transparentnost
*Systémy AI by se měly porozumět*. Pomocí přizpůsobení:

* *Poskytněte uživatelům informace o tom, jak byl obsah přizpůsobován.* Uživatelům můžete například zobrazit tlačítko s popiskem `Why These Suggestions?` ukazující, které hlavní funkce uživatele a akce přehrály roli ve výsledcích přizpůsobeného modulu.
* Ujistěte se, že vám podmínky použití znamenají, že k přizpůsobení prostředí budete používat informace o uživatelích a jejich chování.

### <a name="fairness"></a>Nestrannost
*Systémy AI by měly považovat za všechny osoby poměrně*.

* Nepoužívejte přizpůsobený modul pro případy použití, kde jsou výsledky dlouhodobé, následné nebo zahrnuje reálné poškození.
* Nepoužívejte funkce, které nejsou vhodné k přizpůsobení obsahu, nebo které mohou přispět k rozšiřování neočekávaných posunů. Například kdokoli s podobnými finančními okolnostmi by měl vidět stejná individuální doporučení pro finanční produkty.
* Pochopení představů, které mohou existovat ve funkcích, které se nastavují pomocí editorů, nástrojů pro algoritmy nebo uživatelů samotných.

### <a name="reliability-and-safety"></a>Spolehlivost a zabezpečení
*Systémy AI by měly spolehlivě a bezpečně fungovat*. Pro přizpůsobení:

* *Neposkytněte akci přizpůsobené možnosti, která by neměla být zvolena*. Například nevhodné filmy by se měly vyfiltrovat mimo akce, které je třeba přizpůsobit, pokud je doporučení pro uživatele typu anonymní nebo mladší.
* Spravujte model přizpůsobeného přizpůsobeného *jako obchodní Asset*.  Vezměte v úvahu, jak často se mají ukládat a zálohovat zásady modelu a učení za vaší smyčkou přizpůsobeného, a jinak je považujte za důležitý obchodní majetek. Opětovné vyprodukování minulých výsledků je důležité pro zlepšení auditů a měření.
* *Poskytněte kanály pro získání přímé zpětné vazby od uživatelů*. Kromě toho, že se bezpečnostní kontroly pro kódování zajišťují, aby se zajistilo, že pouze správné cílové skupiny uvidí správný obsah, poskytněte mechanismus pro zpětnou vazbu uživatelům, kteří budou hlásit obsah, který může být překvapivé nebo narušen. Obzvláště pokud váš obsah pochází od uživatelů nebo třetích stran, zvažte použití nástroje Microsoft Content Moderator nebo dalších nástrojů pro kontrolu a ověření obsahu.
* *Provádějte časté online hodnocení*. To vám pomůže monitorovat trendy a zajistit, aby bylo známo, že je jejich efektivita známá.
* *Navažte proces pro detekci a fungování škodlivé manipulace*. Existují objekty actor, které budou využívat možnosti strojového učení a AI z hlediska jejich prostředí, aby se posunul výsledek k jejich cílům. Pokud je použití přizpůsobeného přizpůsobeného přizpůsobování důležité volby, ujistěte se, že máte vhodné prostředky k detekci a zmírnění těchto tříd útoků, včetně lidského přezkoumání v příslušných případech.

### <a name="security-and-privacy"></a>Zabezpečení a soukromí
*Systémy AI by měly být zabezpečené a respektovat soukromí*. Při použití přizpůsobeného přizpůsobování:

* *Informujte uživatele o shromažďovaných datech a způsobu jejich používání a předem si vyžádejte svůj souhlas, a* to podle místních a průmyslových předpisů.
* *Poskytněte ochranu osobních údajů a zabezpečte uživatelské ovládací prvky.* Pro aplikace, které ukládají osobní údaje, zvažte poskytnutí snadno najítelné tlačítko pro funkce, jako například:
   * `Show me all you know about me`
   * `Forget my last interaction`
   * `Delete all you know about me`

V některých případech to může být právně povinné. Zvažte možnost pravidelného přeškolení modelů, takže neobsahují trasování odstraněných dat.

### <a name="inclusiveness"></a>Inkluzivnost
*Vyřešte širokou škálu lidských potřeb a zkušeností*.
* *Poskytněte přizpůsobené prostředí pro rozhraní s podporou usnadnění přístupu.* Efektivita, která přichází z dobrého přizpůsobení – je využita k omezení množství úsilí, pohybu a nepotřebného opakování v interakcích – může být obzvláště užitečná pro osoby s postižením.
* *Upravte chování aplikace na kontext*. Pomocí přizpůsobeného nástroje můžete v aplikaci Chat Bot odstranit nejednoznačnost mezi záměry, například když je správný výklad kontextem a jedna velikost nemusí odpovídat všem.


## <a name="proactive-readiness-for-increased-data-protection-and-governance"></a>Proaktivní připravenost na zvýšení ochrany dat a zásady správného řízení

Je těžké předpovědět konkrétní změny v kontextech regulativních předpisů, ale obecně by se mohlo přesáhnout nad rámec minimálního právního rámce při zajištění respectful používání osobních údajů a zajištění transparentnosti a volby v souvislosti s vytvářením algoritmových rozhodnutí.


* Zvažte, jestli předem naplánujete situaci, kdy může dojít k novým omezením pro data shromážděná od jednotlivců, a je potřeba Ukázat, jak se použilo k rozhodování.
* Zvažte možnost nadbytečné připravenosti, kde mohou uživatelé zahrnout navýšení zranitelné populace, děti, uživatele v ekonomickém ohrožení zabezpečení nebo uživatele, kteří jsou jiným způsobem vnímaví, aby měli vliv na algoritmus manipulace.
* Zvažte rozšíření nespokojenosti s tím, jak se zachází na cílení na cílovou skupinu a jak se prohrály algoritmy a algoritmy pro shromažďování dat, a jak se vyhnout ověřeným strategickým chybám.


## <a name="proactive-assessments-during-your-project-lifecycle"></a>Proaktivní posouzení během životního cyklu projektu

Zvažte vytvoření metod pro členy týmu, uživatele a vlastníky, které budou hlásit obavy z zodpovědného použití, a vytvořit proces, který bude upřednostňovat jejich rozlišení a zabraňuje Retaliation.

Jakákoli osoba, která má na starosti vedlejší účinky použití jakékoli technologie, je omezená jejich perspektivou a životním prostředím. Rozšiřte dostupné množství stanovisek tím, že do svých týmů, uživatelů nebo poradních vývěsek přinesete různorodé hlasy. To je tak možné a doporučujeme, aby se mohli mluvit. Vezměte v úvahu školení a studijní materiály, abyste mohli dále rozšířit znalosti týmu v této doméně a přidat možnost diskutovat na složitá a citlivá témata.

Zvažte, jestli v životním cyklu aplikace využijete úlohy týkající se zodpovědného použití stejně jako jiné crosscutting úlohy, jako jsou úlohy týkající se uživatelského prostředí, zabezpečení nebo DevOps. Tyto úlohy a jejich požadavky nemůžou být dodatečně. Odpovědné použití by se mělo projednávat a ověřit v celém životním cyklu aplikace.

## <a name="questions-and-feedback"></a>Dotazy a zpětná vazba

Microsoft nepřetržitě zapisuje úsilí do nástrojů a dokumentů, které vám pomůžou s těmito odpovědnostmi působit. Náš tým vás vyzve k [poskytnutí zpětné vazby společnosti Microsoft](mailto:cogsvcs-RL-feedback@microsoft.com?subject%3DPersonalizer%20Responsible%20Use%20Feedback&body%3D%5BPlease%20share%20any%20question%2C%20idea%20or%20concern%5D)  , pokud se domníváte, že další nástroje, funkce produktu a dokumenty vám pomůžou implementovat tyto pokyny k používání přizpůsobeného doplňku.

## <a name="recommended-reading"></a>Doporučené čtení

* Podívejte se na šest principů Microsoftu pro zodpovědný vývoj AI, který je publikovaný v knize s lednem 2018, což [je budoucí vypočítané](https://news.microsoft.com/futurecomputed/) .
* [Kdo je v budoucnu vlastní?](https://www.goodreads.com/book/show/15802693-who-owns-the-future) od jaron Lanier.
* [Zbraně pro matematické zničení](https://www.goodreads.com/book/show/28186015-weapons-of-math-destruction) pomocí – Cathy O'Neil
* [Etika a datové vědy](https://www.oreilly.com/library/view/ethics-and-data/9781492043898/) podle DJ Patil, Hilary Mason, Mike Loukides.
* [ACMý kodex etiky](https://www.acm.org/code-of-ethics)
* [Genetické informace nediskriminace Act – GINA](https://en.wikipedia.org/wiki/Genetic_Information_Nondiscrimination_Act)
* [FATML principy pro algoritmy s ohledem na účty](https://www.fatml.org/resources/principles-for-accountable-algorithms)


## <a name="next-steps"></a>Další kroky

[Funkce: akce a kontext](concepts-features.md).
