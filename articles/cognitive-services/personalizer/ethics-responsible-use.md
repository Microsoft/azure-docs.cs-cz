---
title: Etika a odpovědné používání - Personalizátář
titleSuffix: Azure Cognitive Services
description: Tyto pokyny jsou zaměřeny na pomoc při implementaci personalizace způsobem, který vám pomůže vybudovat důvěru ve vaši společnost a služby. Ujistěte se, že pauza na výzkum, učit se a uvažovat o dopadu personalizace na životy lidí. Pokud jste na pochybách, vyhledejte vedení.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: diberry
ms.openlocfilehash: e422284b871214dbeca31b5dd17b9177a18ad3c8
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478105"
---
# <a name="guidelines-for-responsible-implementation-of-personalizer"></a>Pokyny pro odpovědnou implementaci personalisty

Aby lidé a společnost mohli plně realizovat potenciál umělou ainii, musí být implementace navrženy tak, aby získaly důvěru těch, kteří do svých aplikací přidávají umělou ai a uživatelů aplikací vytvořených pomocí umělou ai. Tyto pokyny jsou zaměřeny na pomoc při implementaci personalistu způsobem, který vám pomůže vybudovat důvěru ve vaši společnost a služby. Ujistěte se, že pauza na výzkum, učit se a uvažovat o dopadu personalizace na životy lidí. Pokud jste na pochybách, vyhledejte vedení.

Tyto pokyny nejsou určeny jako právní poradenství a měli byste se samostatně ujistit, že vaše žádost je v souladu s rychlým vývojem v oblasti práva v této oblasti a ve vašem odvětví.

Při navrhování aplikace pomocí personalizátoru byste také měli zvážit širokou škálu povinností, které máte při vývoji jakéhokoli systému AI zaměřeného na data, včetně etiky, ochrany osobních údajů, zabezpečení, bezpečnosti, začlenění, transparentnosti a odpovědnosti. Další informace si o nich můžete přečíst v části [Doporučené čtení.](#recommended-reading)

Následující obsah můžete použít jako počáteční kontrolní seznam a přizpůsobit a upřesnit podle vašeho scénáře. Tento dokument má dvě hlavní části: První je věnována zvýraznění aspekty zodpovědného použití při výběru scénářů, funkcí a odměn pro personalistu. Druhý vzít sadu hodnot Microsoft věří, že by měly být považovány za při vytváření systémů AI a poskytuje žalovatelné návrhy a rizika o tom, jak vaše použití Personalizace ovlivňuje je.


## <a name="your-responsibility"></a>Vaše odpovědnost

Všechny pokyny pro zodpovědné provádění vycházejí ze základu, že vývojáři a firmy využívající personalizát a jsou zodpovědní za účinky používání těchto algoritmů ve společnosti. Pokud vyvíjíte aplikaci, kterou bude vaše organizace nasazovat, měli byste si uvědomit svou roli a odpovědnost za její provoz a jak ovlivňuje lidi. Pokud navrhujete aplikaci, která má být nasazena třetí stranou, dospět ke společnému porozumění s nimi, kdo je nakonec zodpovědný za chování aplikace a dokument, který porozumění.

Důvěra je postavena na pojmu splněných závazků - zvažte své uživatele, společnost a právní rámec, ve které vaše aplikace pracují, k identifikaci explicitních a implicitních závazků, které mohou mít.

Společnost Microsoft neustále vkládá úsilí do svých nástrojů a dokumentů, které vám pomohou tyto povinnosti plnit. Pokud se domníváte, že další nástroje, funkce produktu a dokumenty vám pomohou implementovat tyto pokyny pro používání personalizátoru, poskytněte společnosti [Microsoft zpětnou vazbu.](mailto:cogsvcs-RL-feedback@microsoft.com?subject%3DPersonalizer%20Responsible%20Use%20Feedback&body%3D%5BPlease%20share%20any%20question%2C%20idea%20or%20concern%5D)


## <a name="factors-for-responsibly-implementing-personalizer"></a>Faktory pro zodpovědné implementaci personalisty

Implementace personalisty může mít pro vaše uživatele a vaši firmu velkou hodnotu. Chcete-li implementovat Personalizátor zodpovědně, začněte tím, že zvažuje následující pokyny, když:

* Výběr emisi případů použití individuálního nastavení.
* Budování [funkcí odměny](concept-rewards.md).
* Výběr [funkcí](concepts-features.md) týkající se kontextu a možných akcí, které budete používat pro přizpůsobení.


## <a name="choosing-use-cases-for-personalizer"></a>Volba případů použití pro personalizátor

Použití služby, která se učí přizpůsobit obsah a uživatelská rozhraní, je užitečné. To může být také nesprávně, pokud způsob, jakým personalizace vytváří negativní vedlejší účinky v reálném světě, včetně v případě, že uživatelé nejsou vědomi obsahu personalizace.

Příklady použití Personalista se zvýšeným potenciálem pro negativní vedlejší účinky nebo nedostatek transparentnosti zahrnují scénáře, kde "odměna" závisí na mnoha dlouhodobých složitých faktorech, které při nadměrném zjednodušeném do okamžité odměny mohou mít nepříznivé výsledky pro jednotlivce. Ty mají tendenci být považovány za "následné" volby, nebo volby, které zahrnují riziko újmy. Například:


* **Finance**: Personalizace nabídek na půjčky, finanční a pojistné produkty, kde jsou rizikové faktory založeny na údajích, o kterých jednotlivci nevědí, nemohou získat nebo nemohou zpochybnit.
* **Vzdělávání**: Personalizace hodností pro školní kurzy a vzdělávací instituce, kde doporučení mohou šířit předsudky a snižovat povědomí uživatelů o dalších možnostech.
* **Demokracie a občanská participace**: Personalizace obsahu pro uživatele s cílem ovlivňovat názory je následná a manipulativní.
* **Hodnocení odměn třetí strany**: Personalizace položek, kde je odměna založena na hodnocení uživatele třetí strany, namísto odměny generované vlastním chováním uživatele.
* **Nesnášenlivost k průzkumu:** Jakákoli situace, kdy průzkum chování Personalista může způsobit škodu.

Při výběru případů použití pro personalizátor:

* Spusťte proces návrhu s ohledem na to, jak přizpůsobení pomáhá uživatelům.
* Zvažte negativní důsledky v reálném světě, pokud některé položky nejsou hodnoceny pro uživatele kvůli personalizaci vzory nebo průzkumu.
* Zvažte, zda váš případ použití představuje automatizované zpracování, které významně ovlivňuje subjekty údajů, které jsou regulovány podle článku 22 [gdpr](https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX:32016R0679) nebo jiných zákonů.
* Zvažte sebenaplňující se smyčky proroctví. K tomu může dojít, pokud odměna za přizpůsobení trénuje model, takže může následně dále vyloučit demografickou skupinu z přístupu k relevantnímu obsahu. Například většina lidí v nízkopříjmové čtvrti nezíská nabídku pojistného na pojištění a pomalu nikdo v okolí nemá tendenci vidět nabídku vůbec, pokud není dostatek průzkumu.
* Uložit kopie modelů a zásad učení v případě, že je nutné reprodukovat Personalizák v budoucnu. Můžete to provést pravidelně nebo každé období aktualizace modelu.
* Vezměme si úroveň průzkumu adekvátní pro prostor a jak jej použít jako nástroj ke zmírnění "echo komory" účinky.


## <a name="selecting-features-for-personalizer"></a>Výběr funkcí pro personalizátor

Přizpůsobení obsahu závisí na tom, že máte užitečné informace o obsahu a uživateli. Mějte na paměti, že u některých aplikací a odvětví mohou být některé uživatelské funkce přímo nebo nepřímo považovány za diskriminační a potenciálně nezákonné.

Zvažte účinek těchto funkcí:

* **Demografické údaje uživatelů**: Funkce týkající se pohlaví, pohlaví, věku, rasy, náboženství: Tyto funkce nemusí být v některých aplikacích povoleny z regulačních důvodů a nemusí být etické personalizovat je, protože personalizace by množí generalizace a zaujatost. Příkladem tohoto šíření předpojatosti je pracovní vysílání pro inženýrské inženýrství, které není zobrazeno starším nebo genderově založeným publikům.
* **Informace o lokalitě**: Na mnoha místech světa mohou informace o poloze (například PSČ, PSČ nebo název sousedství) vysoce korelovat s příjmy, rasou a náboženstvím.
* **Vnímání spravedlnosti uživatelem**: I v případech, kdy vaše aplikace činí správná rozhodnutí, zvažte účinek uživatelů, kteří vnímají, že se obsah zobrazený ve vaší aplikaci mění způsobem, který se zdá být v korelaci s funkcemi, které by byly diskriminační.
* **Neúmyslné zkreslení ve funkcích**: Existují typy zkreslení, které mohou být zavedeny pomocí funkcí, které ovlivňují pouze podmnožinu základního souboru. To vyžaduje zvláštní pozornost, pokud jsou funkce generovány algoritmicky, například při použití analýzy obrázků k extrahování položek v analýze obrázků nebo textu za účelem zjištění entit v textu. Uvědomte si charakteristiky služeb, které používáte k vytvoření těchto funkcí.

Při výběru funkcí, které se mají odesílat v kontextech a akcích personalistovi, použijte následující postupy:

* Zvažte legálnost a etiku používání určitých funkcí pro některé aplikace a zda mohou být nevinně vypadající funkce proxy pro ostatní, kterým chcete nebo byste se měli vyhnout,
* Buďte transparentní pro uživatele, že algoritmy a analýza dat se používají k přizpůsobení možností, které vidí.
* Zeptejte se sami sebe: Zajímalo by se to mým uživatelům a byli byste rádi, kdybych tyto informace použil k přizpůsobení obsahu pro ně? Cítil bych se pohodlně ukazovat jim, jak bylo rozhodnuto zvýraznit nebo skrýt některé položky?
* Používejte spíše údaje o chování než klasifikaci nebo segmentaci na základě jiných charakteristik. Demografické informace byly tradičně používány maloobchodníky z historických důvodů - demografické atributy se zdály být jednoduché shromažďovat a jednat před digitální érou - ale otázka, jak relevantní demografické informace jsou, když máte skutečnou interakci, kontextové a historické údaje, které se více vztahují k preferencím a identitě uživatelů.
* Zvažte, jak zabránit tomu, aby funkce byly "zfalšovány" uživateli se zlými úmysly, což může vést k výcviku personalisty zavádějícími způsoby, jak záměrně narušit, ztrapnit a obtěžovat určité třídy uživatelů.
* Pokud je to vhodné a proveditelné, navrhněte aplikaci tak, aby se uživatelé mohli přihlásit nebo se odhlásit z používání určitých osobních funkcí. Ty mohou být seskupeny, například "Informace o poloze", "Informace o zařízení", "Historie minulých nákupů" atd.


## <a name="computing-rewards-for-personalizer"></a>Výpočetní odměny pro personalistu

Personalizátor se snaží zlepšit výběr, kterou akci odměnit na základě skóre odměny poskytované ho obchodní logiky aplikace.

Dobře postavené skóre odměny bude sloužit jako krátkodobý proxy k obchodnímu cíli, který je vázán na poslání organizace.

Například odměňování za kliknutí způsobí, že služba personalisty vyhledá kliknutí na úkor všeho ostatního, i když to, na co kliknete, je rušivé nebo není vázáno na obchodní výsledek.

Jako kontrastní příklad může zpravodajský web chtít nastavit odměny vázané na něco smysluplnějšího než kliknutí, například "Strávil uživatel dostatek času číst obsah?" "Klikli na příslušné články nebo reference?". S personalizátem je snadné úzce spojit metriky s odměnami. Ale dávejte pozor, abyste nezahanbili krátkodobé zapojení uživatelů s dobrými výsledky.

### <a name="unintended-consequences-from-reward-scores"></a>Nezamýšlené důsledky z bodů odměny
Skóre odměn může být vytvořeno s nejlepšími úmysly, ale stále může vést k neočekávaným důsledkům nebo nezamýšleným výsledkům ohledně toho, jak personalizátor hodnotí obsah.

Představte si následující příklady:

* Odměňování personalizace videoobsahu na procento sledované délky videa bude mít pravděpodobně tendenci řadit kratší videa.
* Odměňování akcií sociálních médií, bez analýzy sentimentu o tom, jak je sdíleno nebo samotný obsah, může vést k hodnocení urážlivého, nemoderovaného nebo pobuřujícího obsahu, který má tendenci podněcovat hodně "angažovanosti", ale přidává malou hodnotu.
* Odměňování akce na prvky uživatelského rozhraní, které uživatelé neočekávají ke změně, může narušit použitelnost a předvídatelnost uživatelského rozhraní, kde tlačítka překvapivě mění polohu nebo účel bez varování, což ztěžuje určitým skupinám uživatelů zůstat produktivní.

Implementujte tyto osvědčené postupy:

* Spusťte offline experimenty se systémem pomocí různých přístupů odměny pochopit dopad a vedlejší účinky.
* Vyhodnoťte své funkce odměny a zeptejte se sami sebe, jak by extrémně naivní člověk ohýbal svou interpretaci a dosáhl s ním nežádoucích výsledků.


## <a name="responsible-design-considerations"></a>Aspekty zodpovědného návrhu

Níže jsou uvedeny oblasti návrhu pro odpovědné implementace AI. Další informace přiléhá k tomuto rámci v [The Future Computed](https://news.microsoft.com/futurecomputed/).

![Hodnoty AI z budoucích výpočtů](media/ethics-and-responsible-use/ai-values-future-computed.png)

### <a name="accountability"></a>Odpovědnost
*Lidé, kteří navrhují a nasazují systémy ai, musí být zodpovědní za to, jak jejich systémy fungují*.

* Vytvořte interní pokyny, jak implementovat personalizátor, dokumentovat je a sdělit je svému týmu, vedoucím pracovníkům a dodavatelům.
* Provádět pravidelné kontroly toho, jak se vypočítávají skóre odměn, provádět offline hodnocení, abyste zjistili, jaké funkce ovlivňují personalistu, a použijte výsledky k odstranění nepotřebných a zbytečných funkcí.
* Sdělte uživatelům jasně, jak se personalizace používá, k jakému účelu a s jakými daty.
* Archivovat informace a datové zdroje – například modely, zásady učení a další data – které personalizátor používá k fungování, aby bylo možné reprodukovat výsledky.

### <a name="transparency"></a>Průhlednost
*Systémy umělá umělá vi by měla být srozumitelná*. S personalizátorem:

* *Podejte uživatelům informace o tom, jak byl obsah personalizován.* Můžete například uživatelům zobrazit tlačítko s `Why These Suggestions?` označením, které znázorňují, které hlavní funkce uživatele a akce hrály roli ve výsledcích personalisty.
* Ujistěte se, že vaše podmínky použití uvádějí, že budete používat informace o uživatelích a jejich chování k přizpůsobení prostředí.

### <a name="fairness"></a>Spravedlnosti
*AI Systémy by měly zacházet se všemi lidmi spravedlivě*.

* Nepoužívejte personalista pro případy použití, kdy jsou výsledky dlouhodobé, následné nebo zahrnují skutečnou škodu.
* Nepoužívejte funkce, které nejsou vhodné k přizpůsobení obsahu nebo které mohou pomoci šířit nežádoucí předsudky. Například každý, kdo má podobné finanční okolnosti, by měl vidět stejná personalizovaná doporučení pro finanční produkty.
* Pochopit předpojatost, které mohou existovat v funkcích, které jsou získávány z editorů, algoritmické nástroje nebo uživatelé sami.

### <a name="reliability-and-safety"></a>Spolehlivost a bezpečnost
*Systémy AI by měly fungovat spolehlivě a bezpečně*. Pro personalisty:

* *Neposkytovat akce personalista, které by neměly být vybrány*. Například nevhodně filmy by měly být odfiltrovány z akcí přizpůsobit, pokud dělat doporučení pro anonymní nebo nezletilý uživatel.
* *Spravujte model personalisty jako obchodní zdroj*.  Zvažte, jak často můžete ukládat a zálohovat modelové a výukové zásady za smyčkou personalizátorů a jinak s ní zacházet jako s důležitým obchodním aktivem. Reprodukce minulých výsledků je důležitá pro vlastní audit a zlepšení měření.
* *Poskytněte kanály pro získání přímé zpětné vazby od uživatelů*. Kromě kontroly bezpečnosti kódování, aby se ujistil, pouze správné publikum vidět správný obsah, poskytnout mechanismus zpětné vazby pro uživatele hlásit obsah, který může být překvapivé nebo znepokojující. Zejména pokud váš obsah pochází od uživatelů nebo třetích stran, zvažte použití programu Microsoft Content Moderator nebo dalších nástrojů ke kontrole a ověření obsahu.
* *Provádět časté offline hodnocení*. To vám pomůže sledovat trendy a ujistěte se, že účinnost je známa.
* *Vytvořte proces pro detekci a jednat na škodlivé manipulace*. Existují aktéři, kteří využijí schopnosti strojového učení a systémů umělou a iniciála učit se ze svého prostředí a posunout výsledek směrem k jejich cílům. Pokud je vaše používání personalistu schopno ovlivnit důležité volby, ujistěte se, že máte vhodné prostředky k detekci a zmírnění těchto tříd útoků, včetně lidské kontroly za vhodných okolností.

### <a name="security-and-privacy"></a>Zabezpečení a soukromí
*Systémy ai by měly být bezpečné a respektovat soukromí*. Při použití personalizace:

* *Informujte uživatele předem o shromažďovaných datech a způsobu jejich použití a předem získejte jejich souhlas*v návaznosti na místní a oborové předpisy.
* *Poskytněte uživatelské ovládací prvky chránící ochranu osobních údajů.* U aplikací, které ukládají osobní údaje, zvažte poskytnutí snadno dostupného tlačítka pro funkce, jako jsou:
   * `Show me all you know about me`
   * `Forget my last interaction`
   * `Delete all you know about me`

V některých případech mohou být tyto zákonem vyžadovány. Zvažte kompromisy v rekvalifikačních modelech pravidelně, aby neobsahovaly stopy odstraněných dat.

### <a name="inclusiveness"></a>Inkluzivita
*Řešit širokou škálu lidských potřeb a zkušeností*.
* *Poskytněte přizpůsobené prostředí pro rozhraní s podporou přístupnosti.* Účinnost, která pochází z dobré personalizace - použít ke snížení množství úsilí, pohybu a zbytečné opakování v interakcích- může být zvláště prospěšné pro osoby se zdravotním postižením.
* *Upravte chování aplikace na kontext*. Personalizace můžete použít k rozmísťování mezi záměry v chatu bot, například jako správný výklad může být kontextové a jedna velikost nemusí vyhovovat všem.


## <a name="proactive-readiness-for-increased-data-protection-and-governance"></a>Proaktivní připravenost na zvýšenou ochranu a správu údajů

Je těžké předvídat konkrétní změny v regulačních souvislostech, ale obecně by bylo moudré jít nad rámec minimálního právního rámce při zajišťování uctivého využívání osobních údajů a poskytovat transparentnost a volbu související s algoritmickým rozhodováním.


* Zvažte plánování dopředu do situace, kdy mohou existovat nová omezení údajů shromážděných od jednotlivců, a je třeba ukázat, jak byly použity k rozhodování.
* Zvažte mimořádnou připravenost, kdy uživatelé mohou zahrnovat marginalizované zranitelné skupiny obyvatel, děti, uživatele v ekonomické zranitelnosti nebo uživatele, kteří jsou jinak náchylní k vlivu algoritmické manipulace.
* Zamysleme se nad všeobecnou nespokojeností s tím, jak se programy a algoritmy pro cílení na publikum a ovlivňování publika hrají a jak se vyhnout prokázaným strategickým chybám.


## <a name="proactive-assessments-during-your-project-lifecycle"></a>Proaktivní hodnocení během životního cyklu projektu

Zvažte vytvoření metod pro členy týmu, uživatele a vlastníky firem, které by hlásily obavy týkající se zodpovědného používání, a vytvoření procesu, který upřednostňuje jejich řešení a zabraňuje odvetným opatřením.

Každá osoba, přemýšlet o vedlejší účinky použití jakékoli technologie je omezena jejich perspektivy a životní zkušenosti. Rozšiřte rozsah dostupných názorů tím, že do svých týmů, uživatelů nebo poradních sborů zavedete rozmanitější hlasy; tak, aby bylo možné a povzbuzováno k tomu, aby se ozývají. Zvažte školicí a výukové materiály k dalšímu rozšíření znalostí týmu v této doméně a k přidání možností diskutovat o složitých a citlivých tématech.

Zvažte použití úkolů týkajících se zodpovědného použití stejně jako jiné úlohy příčně se stavby v životním cyklu aplikace, jako jsou úkoly související s uživatelským prostředím, zabezpečením nebo devops. Tyto úkoly a jejich požadavky nemohou být nápad. Zodpovědné používání by mělo být diskutováno a ověřováno po celou dobu životního cyklu aplikace.

## <a name="questions-and-feedback"></a>Otázky a zpětná vazba

Společnost Microsoft neustále vkládá úsilí do nástrojů a dokumentů, které vám pomohou tyto povinnosti plnit. Náš tým vás zve k [poskytnutí zpětné vazby společnosti Microsoft,](mailto:cogsvcs-RL-feedback@microsoft.com?subject%3DPersonalizer%20Responsible%20Use%20Feedback&body%3D%5BPlease%20share%20any%20question%2C%20idea%20or%20concern%5D) pokud se domníváte, že další nástroje, funkce produktu a dokumenty vám pomohou implementovat tyto pokyny pro používání personalistu.

## <a name="recommended-reading"></a>Doporučená četba

* Podívejte se na šest zásad společnosti Microsoft pro zodpovědný vývoj ai publikovaný v knize [The Future Computed](https://news.microsoft.com/futurecomputed/) z ledna 2018
* [Kdo vlastní budoucnost?](https://www.goodreads.com/book/show/15802693-who-owns-the-future) od Jaron Lanier.
* [Zbraně matematický zničení](https://www.goodreads.com/book/show/28186015-weapons-of-math-destruction) - Cathy O'Neil
* [Etika a datová věda](https://www.oreilly.com/library/view/ethics-and-data/9781492043898/) DJ Patil, Hilary Mason, Mike Loukides.
* [Etický kodex ACM](https://www.acm.org/code-of-ethics)
* [Zákon o genetické nediskriminaci - GINA](https://en.wikipedia.org/wiki/Genetic_Information_Nondiscrimination_Act)
* [Fatml principy pro odpovědné algoritmy](https://www.fatml.org/resources/principles-for-accountable-algorithms)


## <a name="next-steps"></a>Další kroky

[Nabízeno: akce a kontext](concepts-features.md).
