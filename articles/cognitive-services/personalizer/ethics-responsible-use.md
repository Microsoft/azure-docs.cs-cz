---
title: Etickými a odpovídá pomocí přepínače-Personalizer
titleSuffix: Azure Cognitive Services
description: Tyto pokyny jsou zaměřené na pomáhá implementovat přizpůsobení způsobem, který vám pomůže vytvořit vztah důvěryhodnosti ve vaší společnosti a služby. Je nutné pozastavit výzkumu, přečtěte si a úmyslné na vliv přizpůsobení na životy lidí. V případě pochybností si vyžádat pokyny.
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: 437dc1fba2502602109483aa9d6f25b4265af26f
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2019
ms.locfileid: "66239896"
---
# <a name="guidelines-for-responsible-implementation-of-personalizer"></a>Pokyny pro zodpovědná provádění Personalizer

Uživatelé a společnosti využijte celý potenciál AI implementace musí být navržené tak, že nezískají vztah důvěryhodnosti z nich do svých aplikací přidat funkce AI a uživatelům aplikace vytvořené s využitím AI. Tyto pokyny jsou zaměřené na pomáhá implementovat Personalizer způsobem, který vám pomůže vytvořit vztah důvěryhodnosti ve vaší společnosti a služby. Je nutné pozastavit výzkumu, přečtěte si a úmyslné na vliv přizpůsobení na životy lidí. V případě pochybností si vyžádat pokyny.

Tyto pokyny nepředstavují právní Rady a je třeba samostatně zajistit, že vaše aplikace v souladu s agilním vývojem práva v této oblasti a vaše odvětví.

Také v návrhu vaší aplikace pomocí Personalizer, měli byste zvážit široké škály odpovědnosti, které máte při vývoji všechny datově orientovaných AI systému, včetně etickými, ochrany osobních údajů, zabezpečení, zabezpečení, zahrnutí, transparentnost a odpovědnost. Další informace o těchto v [doporučené čtení](#recommended-reading) oddílu.

Můžete použít následující obsah jako kontrolní seznam starter a přizpůsobit a vyladit je pro váš scénář. Tento dokument má dvě hlavní části: První je vyhrazen pro zvýraznění odpovídá použití aspekty při výběru scénáře, funkce a odměny pro Personalizer. Druhý vzít sadu hodnot, které společnost Microsoft věří třeba zvážit při vytváření AI systémů a poskytuje užitečná doporučení a rizika na způsob jejich používání Personalizer ovlivňuje. 


## <a name="your-responsibility"></a>Vaše odpovědnosti

Všechny pokyny pro implementaci zodpovědná sestavení na základech, vývojáři a firmy používající Personalizer jsou zodpovědné a zodpovídají za účinky pomocí těchto algoritmů ve společnosti. Pokud vyvíjíte aplikaci, která bude vaše organizace nasazovat, by měl rozpoznávat role a odpovědnosti za tento jeho provoz a o jejím dopadu lidí. Pokud navrhujete aplikaci k nasazení třetí strana, přejděte do sdílené Principy s nimi, který je nakonec odpovědné za chování aplikace a dokumentu tento princip.

Vztah důvěryhodnosti je postavená na pojem splněny závazky – zvažte vaši uživatelé společnosti a právní rámec vaše aplikace funguje, k identifikaci explicitní a implicitní závazky, které mohou mít.

Microsoft průběžně nastavuje úsilí do jeho nástroje a dokumenty, které umožňují pracovat s těmito úkoly. [Poskytnout zpětnou vazbu společnosti Microsoft](mailto:cogsvcs-RL-feedback@microsoft.com?subject%3DPersonalizer%20Responsible%20Use%20Feedback&body%3D%5BPlease%20share%20any%20question%2C%20idea%20or%20concern%5D) Pokud si myslíte, že další nástroje, funkce produktu a dokumenty by vám pomoct implementovat tyto pokyny k používání Personalizer.


## <a name="factors-for-responsibly-implementing-personalizer"></a>Faktory pro implementaci zodpovědně Personalizer

Implementace Personalizer může být skvělé hodnoty vašim uživatelům a firmě. K implementaci Personalizer zodpovědně, začněte tím, že vzhledem k tomu následující pokyny případě:

* Výběr případy použití použít individuálního nastavení.
* Vytváření [odměna v rámci funkce](https://github.com/Azure/personalization-rl/blob/master/docs/concepts-rewards.md).
* Výběr, který [funkce](https://github.com/Azure/personalization-rl/blob/master/docs/concepts-features.md) o kontextu a možných akcí budete používat pro přizpůsobení.


## <a name="choosing-use-cases-for-personalizer"></a>Výběr případy použití pro Personalizer

Pomocí služby, které se učí můžete přizpůsobit obsah a uživatelské rozhraní je užitečné. Může být také misapplied, pokud způsob přizpůsobení vytvoří záporný vedlejší účinky v reálném světě, včetně, pokud jsou uživatelé vědět o přizpůsobení obsahu. 

Příklady použití Personalizer se zvýšenou potenciál pro záporné vedlejší účinky nebo nedostatek průhlednosti patří scénáře, kde "účet" závisí na mnoha dlouhodobé komplexní faktory, když over-pass-the zjednodušené do okamžité reward může mít nepříznivý výsledky pro jednotlivce. Tyto jsou často považuje za "následné" možností nebo voleb, které se týkají riziko poškození. Příklad: 


* **Finance**: Přizpůsobení nabídky na půjček, finanční a pojišťovací produkty, kde rizikové faktory založené na datech, jednotlivcům jak, nelze získat nebo nelze sporu. 
* **Vzdělávání**: Přizpůsobení pořadí kurzy školy a vzdělávací instituce, kde může doporučení šíření chyb a snížit sledování uživatelů z dalších možností.
* **Demokracie a Civic účast**: Přizpůsobení obsahu pro uživatele s cílem vliv názory je následné a manipulující.
* **Vyhodnocení třetích stran reward**: Přizpůsobení položky, které potřebu vychází z druhé 3. stran vyhodnocení uživatele, namísto nutnosti reward generovaných chování vlastněných uživateli.
* **Nesnášenlivosti pro zkoumání**: Každé situaci, kdy může poškodit chování zkoumání Personalizer.

Při výběru případy použití pro Personalizer:

* Zahájíte proces návrhu vzhledem k tomu, jak přizpůsobení pomáhá uživatelům.
* Pokud nejsou některé položky seřazeny pro uživatele kvůli přizpůsobení vzorů nebo průzkumu vezměte v úvahu negativní důsledky v reálném světě.
* Vezměte v úvahu svým uspokojivou prophecy smyčky. To může nastat pokud reward přizpůsobení trénovat modelu, tak může následně další vyloučit demografické skupiny přístup k souvisejícího obsahu. Například většina lidí v s nízkými příjmy detekovaná sousední není získat pojištění nabídky úrovně premium a pomalu nikdo v čtvrti tendenci zobrazíte nabídku vůbec.
* Ukládejte si kopie modely a učení zásady v případě, že je nutné pro reprodukci Personalizer v budoucnu. Můžete to provést pravidelně nebo každý interval aktualizace modelu.
* Vezměte v úvahu úrovně zkoumání adekvátní prostor a jak ji používat jako nástroj pro "echo komory" důsledky.


## <a name="selecting-features-for-personalizer"></a>Výběr funkce pro Personalizer

Přizpůsobování obsahu díky závisí na existenci užitečné informace o obsahu a uživatele. Mějte na paměti, u některých aplikací a odvětví, některé funkce lze přímo nebo nepřímo považovat za diskriminační a potenciálně neplatnou uživatele.

Zvažte, jaký vliv tyto funkce:

* **Uživatel demografických**: Funkce o pohlaví, pohlaví, věk, závodu, vyznání: Tyto funkce nemusí být povolený v některých aplikacích regulačních důvodů a nemusí být etické můžete přizpůsobit kolem sebe, protože přizpůsobení by podporovala Generalizace a posun. Příklad posunu omezení šíření je úloh účtování engineering není uvedené starší nebo podle pohlaví cílovým skupinám na místě.
* **Informace o národním prostředí**: Na mnoha místech světa informace o poloze (například PSČ, PSČ nebo jméno čtvrti) můžete vysoce korelují s výnosy, závod a vyznání.
* **Uživatel vnímání rovnost**: Dokonce i v případech, kdy vaše aplikace provádí při rozhodování zvažte, jaký vliv vnímání tento obsah zobrazit v aplikaci změny tak, aby se zdá být korelována funkcí, které byste měli diskriminační uživatelů.
* **Nežádoucí posun ve funkcích**:  Existují typy chyb, které mohou být zavedena pomocí funkcí, které mají vliv jenom podmnožinu základního souboru. To vyžaduje zvláštní pozornost tématům, pokud se funkce generuje algorithmically, jako je například při použití image analýzy k extrahování položky v obrázek nebo text analytics ke zjišťování entit v textu. Ujistěte se, sami vědět charakteristik služeb, které použijete k vytvoření těchto funkcí.

Následující postupy platí při výběru funkce, které chcete odesílat Personalizer v kontextech a akce:

* Vezměte v úvahu legálnosti a etickými použití určitých funkcí u některých aplikací a zda nevinnosti hledání funkce mohou být třídy proxy pro ostatní mají nebo měli vyhnout,
* Být pro uživatele transparentní, algoritmy a analýzy dat byly použity k přizpůsobení možností, zobrazí se jim.
* Položte si otázku: Moji uživatelé by pro vás a vám rádi, pokud mám použít tyto informace můžete přizpůsobit obsah pro ně? By I s klidem znázorňující, jak společnost se rozhodla zvýrazněte nebo skrýt některé položky?
* Použijte místo data klasifikace nebo segmentace podle jiné vlastnosti chování. Demografické údaje tradičně použil prodejců z historických důvodů – demografické atributy vypadal jako snadno shromažďovat a použít před digitální období – dotaz, ale jak relevantní demografické údaje je, když máte skutečné interakce Kontextová a historická data, která lépe odpovídá předvolby a identity uživatelů.
* Zvažte, jak zabránit funkce se "maskování' uživateli se zlými úmysly, což v případě zneužití ve velké počty může vést k trénování Personalizer v zavádějící způsoby, jak záměrně přerušit, embarrass a obtěžování určité třídy uživatelů. 
* Při vhodné a proveditelné, navrhněte svou aplikaci, která umožní uživatelům přihlášení nebo vyjádřit výslovný nesouhlas s některými osobní funkcemi používanými. Ty by mohly být seskupené, jako je například "Informace o poloze", "Informace o zařízení", "Minulé historie" atd.


## <a name="computing-rewards-for-personalizer"></a>Odměny pro Personalizer computingu

Personalizer se snaží zlepšovat volba jakou akci chcete potřebu na základě skóre reward poskytuje obchodní logiky aplikace.

Dobře sestavený reward skóre bude fungovat jako krátkodobé proxy server a obchodní cíle, které se vázalo na cíle vaší organizace.

Například odměny v kliknutí způsobí, že kliknutí seek Personalizer služby za cenu všechno ostatní, i v případě, co dojde ke kliknutí na na je rušivé a není vázaná na obchodního výsledku.

Kontrastní třeba web příspěvky chtít nastavit odměny vázané na něco smysluplnějšího než klikne, jako například "Nebyl uživatel věnovat dostatek času ke čtení obsahu?" "Kliknou na související články nebo odkazy?". S Personalizer je snadné a jejich zapojení metrika úzce k odměny. Ale dejte pozor, abyste zmařit krátkodobé zapojení uživatelů pomocí dobré výsledky.

### <a name="unintended-consequences-from-reward-scores"></a>Nežádoucí důsledky z reward skóre
Reward skóre, které se můžou vytvářet s využitím nejlepší záměry, ale můžete stále vytvořit neočekávaným důsledkům nebo neočekávaným výsledkům na způsobu Personalizer řazení obsahu. 

Vezměte v úvahu následující příklady:

* Odměny pro přizpůsobení video obsahu na procento délky videa, sledovali vysílání televizní pravděpodobně bodech řadit kratší videa.
* Odměny pro sdílené složky sociálních médií, bez analýzu subjektivního hodnocení ve způsob sdílení nebo obsahu, může vést k hodnocení urážlivé, unmoderated nebo zánětlivé obsah, který se obvykle podněcování spoustu "engagement", ale přidá nízké hodnoty.
* Odměny pro akce pro prvky uživatelského rozhraní, které uživatelé Neočekáváme, že chcete-li změnit může kolidovat s použitelnost a předvídatelnost uživatelského rozhraní, kde tlačítka jsou překvapivě Změna umístění nebo bez upozornění, kvůli tomu je těžší pro určité účely skupiny uživatelů, aby mohli pracovat.

Implementace těchto osvědčených postupů:

* Spouštět experimenty v režimu offline ve vašem systému pomocí různých reward přístupů k pochopení vlivu a vedlejší účinky.
* Vyhodnocení funkce reward a položte si otázku, jak by s velmi naivní osoby ohybem svoji interpretaci a Oslovte nežádoucí výsledky s ním.


## <a name="responsible-design-considerations"></a>Aspekty návrhu odpovědnost

Následují oblasti návrhu pro zodpovědná implementace AI. Další informace o toto rozhraní v [počítaný budoucnosti](https://news.microsoft.com/futurecomputed/).

![AI hodnoty z budoucích vypočítat](media/ethics-and-responsible-use/ai-values-future-computed.png)

### <a name="accountability"></a>Odpovědnosti
*Uživatelů, kteří navrhnout a nasadit systémy AI musí být zodpovídají za jak systémy provoz*. 

* Vytvoření interní pokyny o tom, jak implementovat Personalizer, dokument a sdělovat je týmu, vedoucí pracovníci a dodavatelů.
* Provádět pravidelné kontroly jak se počítají reward skóre, proveďte offline hodnocení, pokud chcete zobrazit, jaké funkce jsou by to ovlivnilo Personalizer a použít výsledky pro odstranění nepotřebných a zbytečné funkce.
* Komunikaci jasně uživatelům Personalizer použití, pro jaké účely a jaká data.
* Archivovat informace a prostředky – například modely, učení zásady a další data - používající Personalizer fungovat, abyste mohli reprodukovat výsledky.

### <a name="transparency"></a>Transparentnost
*AI systémy musí být Understandable*. S Personalizer:

* *Poskytněte uživatelům informace o jak byl individuální obsah.* Například můžete zobrazit vaši uživatelé v tlačítko s popiskem `Why These Suggestions?` zobrazení, které hlavní funkce uživatele a akce hraje roli ve výsledcích Personalizer.
* Ujistěte se, že vaše podmínky použití zkontrolujte uvést, že použijete informace týkající se uživatelů a jejich chování k přizpůsobení prostředí.

### <a name="fairness"></a>Rovnost
*Všichni uživatelé s AI systémy by měly zpracovávat poměrně*.

* Nepoužívejte Personalizer pro případy použití, kdy výsledky nejsou dlouhodobé, následné, nebo zahrnují skutečné poškození.
* Nepoužívejte funkcí, které nejsou vhodné přizpůsobit obsah s nebo, které můžou pomoct rozšířit nežádoucí tendence. Například každý, kdo má podobné finanční situaci byste vidět stejný individuální doporučení pro finanční produkty.
* Vysvětlení chyb, které mohou existovat v funkce, které pocházejí z editorů, vylepšením nástrojů nebo samotných uživatelů.

### <a name="reliability-and-safety"></a>Spolehlivost a zabezpečení
*Systémy AI by měl provádět spolehlivě a bezpečně*. Pro Personalizer:

* *Neposkytují akce k Personalizer, který by neměl být vybrána*. Například neoprávněně videa se mají filtrovat mimo akce můžete přizpůsobit, pokud doporučení pro anonymní nebo snížení věk uživatele.
* *Správa modelu Personalizer jako majetek firmy*.  Zvažte, jak často se má uložit a zálohujte modelu a učení zásady za Personalizer smyčku a jinak ji považovat za důležité obchodním prostředkem. Je důležité pro vlastní audit a měření zlepšení reprodukce minulé výsledky.
* *Zadejte kanály můžete získat přímé zpětné vazby od uživatelů*. Kromě kódování bezpečnostní kontroly, aby se zajistilo pouze správné cílové skupiny, najdete v článku správný obsah, poskytují mechanismus zpětné vazby uživatelů k obsahu sestav, které může být překvapivé nebo narušení. Zejména v případě, že váš obsah pochází z uživatele nebo 3. stran, zvažte použití Microsoft Content Moderator nebo další nástroje ke kontrole a ověřování obsahu.
* *Provádění častých offline hodnocení*. To vám pomůže sledovat trendy a ujistěte se, že se označuje efektivitu.
* *Vytvoření procesu detekovat a reagovat na zlými úmysly manipulaci s*. Existují objekty actor, které se využívají strojové učení a AI systémy možnost získat z jejich prostředí a posunutí výsledek na svých cílů. Pokud vaše užívání Personalizer je schopen ovlivňují důležité možnosti, ujistěte se, že máte příslušné prostředky k detekování a zmírnění těchto tříd útoky, včetně recenze prováděné lidmi v příslušných podmínek.

### <a name="security-and-privacy"></a>Zabezpečení a soukromí
*Systémy AI by měl být bezpečné, respektujeme soukromí*. Při použití Personalizer:

* *Informování uživatelů před jeho zahájením o datech, která se shromažďují a jak se používají a získat jejich souhlas předem*, následující místní verzi a oborové předpisy.
* *Poskytuje ochranu soukromí uživatelské ovládací prvky.* Pro aplikace, které obsahují osobní údaje zvažte poskytnutí na snadno najít tlačítko pro funkcí, jako jsou: 
   * `Show me all you know about me`    
   * `Forget my last interaction` 
   * `Delete all you know about me`

V některých případech to může vyžadovat právně. Zvažte kompromis v pravidelně přetrénování modelů, neobsahují trasování odstraněným datům.

### <a name="inclusiveness"></a>Inclusiveness
*Adresa širokou škálu lidské potřebám a prostředími*.
* *Zadejte individuálních prostředí pro rozhraní s podporou přístupnosti.* Účinnost, který přichází z dobré přizpůsobení - použít ke snížení množství úsilí, přesouvání a zbytečnému opakování v interakcí, může být zvláště užitečné pro osoby s postižením.
* *Upravit chování aplikace do kontextu*. Personalizer můžete použít k rozlišení mezi záměry v chatovací robot, například jako správné vyhodnocení může být kontextové a velikosti nemusí přizpůsobit všem. 


## <a name="proactive-readiness-for-increased-data-protection-and-governance"></a>Proaktivní připravenosti pro zvýšení ochrany a zásad správného řízení

Je těžké předpovědět určité změny v kontextech dodržování legislativních, ale obecně by se dostat nad rámec minimální právní rámec v zajištění respektujte použití osobních údajů a zajištění transparentnosti a související s vylepšením rozhodování a možnost výběru z pohledu.


* Vezměte v úvahu budete plánovat předem na situaci, kdy může být nová omezení na data shromážděná z jednotlivce, a je potřeba k zobrazení, jak byl použit pro rozhodování.
* Vezměte v úvahu další připravenosti, ve kterém uživatelé mohou zahrnovat marginalized zranitelné populace, podřízené položky, uživatelé v ekonomické ohrožení zabezpečení nebo uživatelé jinak náchylný k ovlivnění z manipulaci s vylepšením.
* Vezměte v úvahu široké nespokojenost se shromažďováním dat jak cílí na cílovou skupinu a vliv na cílovou skupinu, programy a algoritmy hrají navýšení kapacity a jak se vyhnout prověřené strategické chyby.


## <a name="proactive-assessments-during-your-project-lifecycle"></a>Proaktivní posouzení během životní cyklus vašeho projektu

Zvažte vytvoření metody pro členy týmu, uživatelům a vlastníkům obchodních sestavy otázky ohledně využívání a vytvoření procesu, který upřednostňuje jejich rozlišení a brání Odveta.

Jakákoli osoba uvažujete o vedlejší účinky použití jakékoli technologie je omezená jejich perspektivy a životní prostředí. Rozbalte oblast názory, které jsou k dispozici díky možnostem různorodější hlasů do týmy, uživatele nebo advisory desky; takový, že je možné a doporučuje pro ně nahlas promluví. Vezměte v úvahu školicí a výukové materiály dále rozšířit znalostí týmu v této doméně a přidat funkci diskutovat o komplexní a citlivých tématech.

Vezměte v úvahu zpracování úlohy, odpovědnost použití stejně jako ostatní úlohy průřezové v životního cyklu aplikací, jako je úlohy týkající se uživatelského prostředí, zabezpečení a devops. Tyto úlohy a požadavky na nemůže být opomíjet. Odpovídá použití by měl popsané a ověřit v průběhu životního cyklu aplikací.
 
## <a name="questions-and-feedback"></a>Otázky a zpětná vazba

Microsoft průběžně nastavuje úsilí do nástroje a dokumenty, které umožňují pracovat s těmito úkoly. Náš tým vás zve k [zasílat připomínky do Microsoftu](mailto:cogsvcs-RL-feedback@microsoft.com?subject%3DPersonalizer%20Responsible%20Use%20Feedback&body%3D%5BPlease%20share%20any%20question%2C%20idea%20or%20concern%5D) Pokud si myslíte, že další nástroje, funkce produktu a dokumenty by vám pomůže implementovat tyto pokyny k používání Personalizer.

## <a name="recommended-reading"></a>Doporučené zdroje

* Zobrazit šest zásady společnosti Microsoft pro zodpovědná vývoj AI publikované v knize. ledna 2018 [počítaný budoucnosti](https://news.microsoft.com/futurecomputed/)
* [Kdo je vlastníkem do budoucna? ](https://www.goodreads.com/book/show/15802693-who-owns-the-future) podle Jaron Lanier.
* [Zbraní matematické ničení](https://www.goodreads.com/book/show/28186015-weapons-of-math-destruction) parametrem - Cathy O'Neil
* [Etickými a pro datové vědy](https://www.oreilly.com/library/view/ethics-and-data/9781492043898/) DJ Patil, Hilary Mason, Mike Loukides.
* [ACM kód Ethics](https://www.acm.org/code-of-ethics)
* [Genetická Information Act Nondiscrimination - GINA](https://en.wikipedia.org/wiki/Genetic_Information_Nondiscrimination_Act)
* [Principy FATML Accountable algoritmů](http://www.fatml.org/resources/principles-for-accountable-algorithms)


## <a name="next-steps"></a>Další postup

[Funkce: akce a kontextu](concepts-features.md).
