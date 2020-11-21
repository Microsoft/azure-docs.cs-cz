---
title: Záznam vlastních hlasových ukázek – služba pro rozpoznávání řeči
titleSuffix: Azure Cognitive Services
description: Přizpůsobte si vlastní hlas produkčního prostředí, a to tak, že připravujete robustní skript, zajistíte dobrý hlasový talentů a budete nahrávat profesionálně.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: erhopf
ms.openlocfilehash: dae7b8e0485c1a2456b85e0910f60b2164d4e41c
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026314"
---
# <a name="record-voice-samples-to-create-a-custom-voice"></a>Záznam ukázek hlasu pro vytvoření vlastního hlasu

Vytváření vysoce kvalitního vlastního hlasu od začátku není žádný neobvyklý podnik. Centrální součástí vlastního hlasu je velká sbírka zvukových ukázek lidského řeči. Je důležité, aby byly tyto zvukové nahrávky vysoce kvalitní. Vyberte si hlasový talentů, který má zkušenosti s tímto druhem nahrávek, a požádejte ho pomocí příslušného inženýra pro zaznamenávání pomocí profesionálního vybavení.

Než budete moct tyto nahrávky vytvořit, budete potřebovat skript: slova, která budou hlasem talentů mluveného záznamu k vytvoření ukázek zvuku. Pro dosažení nejlepších výsledků musí mít váš skript dobré pokrytí foneticky a dostatečné množství pro výuku vlastního hlasového modelu.

Mnoho malých, ale důležitých podrobností se dohlíží k vytvoření profesionálního záznamu hlasu. Tato příručka je plánem procesu, který vám pomůže získat dobré a konzistentní výsledky.

> [!TIP]
> Pro nejvyšší výsledky kvality zvažte, že společnost Microsoft bude pomáhat s vývojem vlastního hlasu. Microsoft má rozsáhlé zkušenosti s vytvářením vysoce kvalitních hlasů pro vlastní produkty, včetně Cortany a kanceláře.

## <a name="voice-recording-roles"></a>Role záznamu hlasu

Existují čtyři základní role v rámci vlastního projektu záznamu hlasu:

Role|Účel
-|-
Talentů hlasu        |Hlas této osoby bude tvořit základ vlastního hlasu.
Inženýr pro zaznamenávání  |Dohlíží na technické aspekty záznamu a pracuje se záznamovým zařízením.
Adresářů            |Připraví skript a naautokaruje výkon talentů hlasu.
Editor              |Dokončí zvukové soubory a připraví je na odeslání na vlastní hlasový portál.

Jednotlivec může vyplnit více než jednu roli. V tomto průvodci se předpokládá, že budete primárně vyplňovat roli ředitele a zajímáte talentů hlasu i inženýra pro zaznamenávání. Pokud chcete záznamy vytvořit sami, Tento článek obsahuje některé informace o roli inženýra nahrávání. Role editoru není potřebná, dokud ji neproběhne po relaci, takže ji může provést ředitel nebo technik pro zaznamenávání.

## <a name="choose-your-voice-talent"></a>Volba hlasového talentůu

Actors s využitím VoiceOver nebo hlasových znaků fungují dobře vlastní hlasová talentů. Můžete také často najít vhodné talentů mezi oznamujícími a čtenáři.

Vyberte hlasový talentů, na kterém se má přirozený hlas. Je možné vytvořit jedinečné hlasy "postavy", ale je mnohem těžší, aby se většina talentů prováděla konzistentně a úsilí může způsobovat hlasový kmen.

> [!TIP]
> Obecně se vyhnete použití rozpoznatelných hlasů k vytvoření vlastního hlasu, dokud samozřejmě nebudete vaším cílem vytvořit celebrit hlas. Méně známé hlasy jsou obvykle méně rušivé pro uživatele.

Jedním z nejdůležitějších faktorů pro výběr možnosti Voice talentů je konzistence. Vaše nahrávky by měly mít zvuk podobný stejnému, jako by byly provedeny ve stejném dnu ve stejné místnosti. K tomuto ideálnímu přístupu můžete využít postupy pro zaznamenávání a inženýry.

Vaše hlasová talentů je druhá polovina rovnice. Musí být schopni mluvit s konzistentní frekvencí, úrovní hlasitosti, roztečí a tónů. Clear Diction je nutné. Talentů také musí být schopné přesně řídit jejich kolísání sklonu, emocionálních ovlivnit a mannerisms řeči.

Nahrávání vlastních ukázek hlasu může být víc fatiguing než jiné druhy hlasového díla. Většina hlasových talentů může nahrávat dva nebo tři hodiny denně. Omezení relací na tři nebo čtyři týdny s denním dnem (Pokud je to možné).

Záznamy vytvořené pro hlasový model by měly být dětem neutrální. To znamená, že JSD utterance by neměl být čten v rámci JSD. Náladu můžete přidat do syntetizované řeči později prostřednictvím ovládacích prvků Prosody. Spolupracujte se svým hlasovým talentůem, abyste mohli vyvíjet "" "osoby, které definují celkový zvuk a emocionálních tón vlastního hlasu. V tomto procesu určíte, jaké jsou "neutrální" zvuky jako pro tyto osoby.

Osoba může mít například přirozený osobní charakter. Takže hlas "jejich" může poznamenat optimism i v případě, že budou mluvit neutrálně. Tyto vlastnosti osobnosti by však měly být drobné a konzistentní. Získejte představu o tom, co si stávající hlasy poslechnout.

> [!TIP]
> Obvykle budete chtít vlastnit hlasové nahrávky, které provedete. Váš hlasový talentů by měl být snadněji k kontraktu pro práci v rámci projektu.

## <a name="create-a-script"></a>Vytvoření skriptu

Výchozím bodem jakékoli vlastní relace záznamu hlasu je skript, který obsahuje projevy, který má být hlasem talentů. (Pojem "projevy" zahrnuje jak úplné věty, tak kratší fráze.)

Projevy ve skriptu může pocházet odkudkoli: fiktivní, nefiktivní, přepisy vedoucích představitelů, sestav zpráv a cokoli jiného, co je k dispozici v tištěné podobě. Pokud chcete mít jistotu, že váš hlas dobře vyhovuje konkrétním druhům slov (například lékařské terminologie nebo programování žargonu), můžete zahrnout věty ze dokumentů odborný nebo technických dokumentů. Stručné informace o možných právních problémech najdete v části ["legálnosti"](#legalities) . Můžete také napsat vlastní text.

Vaše projevy nemusí pocházet ze stejného zdroje nebo stejného druhu zdroje. Nemusejí dokonce nic dělat. Pokud však použijete nastavení fráze (například "jste úspěšně přihlášení") ve vaší aplikaci pro rozpoznávání řeči, nezapomeňte je zahrnout do skriptu. Díky tomu bude váš vlastní hlas lepší pravděpodobností pronouncing tyto fráze dobře. A pokud se rozhodnete použít záznam místo syntetizované řeči, už ho máte ve stejném hlasu.

I když je konzistence klíč při výběru možnosti Voice talentů, je odrůda Hallmark dobré skriptu. Váš skript by měl obsahovat mnoho různých slov a vět s různými délkami věty, strukturami a náladami. Každý zvuk v jazyce by měl být reprezentován víckrát a v mnoha kontextech (tzv. *fonetické pokrytí*).

Kromě toho by měl text obsahovat všechny způsoby, jak může určitý zvuk představovat při psaní, a umístit jednotlivé zvuky na různé místo ve větách. Tyto deklarativní věty a otázky by měly být zahrnuté a musí se číst pomocí vhodného rozhraní výbuchu.

Je obtížné napsat skript, který poskytuje *jenom dostatek* dat, aby mohl portál Custom Speech vytvořit dobrý hlas. Nejjednodušší způsob, jak vytvořit skript, který dosahuje robustního fonetického pokrytí, je zahrnout velký počet vzorků. Standardní hlasy, které poskytuje společnost Microsoft, byly sestaveny z desítky tisíců projevy. Aby bylo možné vytvořit vlastní hlas produkčního prostředí, je potřeba, abyste si poznamenali několik tisíc projevy.

Zkontrolujte pečlivě skript a vyhledejte chyby. Pokud je to možné, nechat někoho jiného, aby ho zkontrolovali. Při spuštění skriptu s vaším talentů pravděpodobně budete chtít zachytit několik dalších chyb.

### <a name="script-format"></a>Formát skriptu

Skript můžete napsat v Microsoft Wordu. Skript slouží k použití během relace nahrávání, takže ho můžete nastavit libovolným způsobem, se kterým se snadno pracujete. Vytvořte textový soubor, který je vyžadován vlastním hlasovým portálem samostatně.

Základní formát skriptu obsahuje tři sloupce:

* Číslo utterance, od 1. Možnost číslování usnadňuje všem členům v studiu, aby odkazovali na konkrétní utterance ("Pojďme vyzkoušet číslo 356 znovu"). Můžete použít funkci číslování odstavců aplikace Word k automatickému číslování řádků tabulky.
* Prázdný sloupec, do kterého zapíšete kód přijetí nebo času každého utteranceu, který vám usnadní jeho vyhledání v dokončeném záznamu.
* Text samotného utterance

![Ukázkový skript](media/custom-voice/script.png)

> [!NOTE]
> Většina záznamů studia v krátkých segmentech známých jako *přijímá*. Každé z nich obvykle obsahuje 10 až 24 projevy. Stačí, abyste si poznamenali, že je toto číslo dostatečné pro hledání utterance. Pokud se zaznamenáte do studia, které preferuje vytváření záznamů, budete místo toho chtít poznamenat kód času. V studiu bude zobrazen výrazný čas.

Ponechte dostatek místa za každým řádkem k zápisu poznámek. Ujistěte se, že mezi stránkami není rozdělen žádný utterance. Číslování stránek a tisk skriptu na jednu stranu dokumentu.

Vytiskněte tři kopie skriptu: jednu pro talentů, jednu pro inženýra a jednu pro generálního ředitele (vy). Použití papírového klipu místo svorek: zkušený Interpret hlasu oddělí stránky, aby nedocházelo ke hluku, když jsou stránky zapnuté.

### <a name="legalities"></a>Právní předpisy

V rámci autorského zákona může být čtení textu s copyrightem objektu actor výkon, pro který by měl být autor práce kompenzován. Tento výkon nebude možné rozpoznat v konečném produktu, vlastním hlasem. Bez ohledu na to, že zákonnost používání autorského práva pro tento účel není dobře navázána. Společnost Microsoft na tento problém nemůže poskytnout právní poradenství. obraťte se na svého vlastního poradce.

Naštěstí je možné tyto problémy zcela vyhnout. Existuje mnoho zdrojů textu, které můžete použít bez oprávnění nebo licence.

|Zdroj textu|Popis|
|-|-|
|[CMU Arctic corpus](http://festvox.org/cmu_arctic/)|Přibližně 1100 vět vybraných z ochrany autorského práva fungují speciálně pro použití v projektech syntézy řeči. Vynikající počáteční bod.|
|Už funguje<br>v části Copyright|Obvykle funguje publikace před 1923. V anglickém jazyce [Gutenberg Project](https://www.gutenberg.org/) nabízí desítky tisíců takových děl. Možná se budete chtít soustředit na novější práci, protože jazyk bude přiblížný k moderní angličtině.|
|Vláda &nbsp; funguje|Práce vytvořená USA vládou nejsou v USA s copyrightem, i když státní správa může nárokovat na Copyright v jiných zemích nebo oblastech.|
|Veřejná doména|Funguje, pro která je autorská práva explicitně nenárokovaná nebo která byla vyhrazená pro veřejnou doménu. V některých předpisech nemusí být možné zcela upustit od autorského práva.|
|Opravňující – licencovaná práce|Funguje v rámci licence, jako je například Creative, nebo licence na bezplatnou dokumentaci GNU (GFDL). Wikipedii používá GFDL. Některé licence však mohou znamenat omezení výkonu licencovaného obsahu, který může mít vliv na vytvoření vlastního hlasového modelu, takže si důkladně přečtěte licenci.|

## <a name="recording-your-script"></a>Záznam skriptu

Zaznamenejte svůj skript v profesionálním studiu, který se specializuje na hlasovou práci. Budou mít záznam do kabiny, správné vybavení a správné lidi k jeho fungování. Vyplatí, že se skimp při záznamu.

Prodiskutujte svůj projekt s technikou pro zaznamenávání studia a naslouchat jim rady. Záznam by měl mít malou nebo žádnou komprimaci dynamického rozsahu (maximálně 4:1). Je důležité, aby zvuk měl konzistentní poměr svazků a vysoký poměr signál k hluku, a přitom neuvolňuje nechtěné zvuky.

### <a name="do-it-yourself"></a>Udělejte to sami

Pokud chcete záznam vytvořit sami, nemusíte jít do studia pro nahrávání, tady je krátký úvod. Díky nárůstu nahrávání a podcastu na domácím webu je snazší než dřív najít dobré poradenství a materiály pro záznam v online režimu.

Váš "záznamový kabina" by měla být malá místnost bez znatelné ozvěny nebo "tónové místnosti". Mělo by být co nejvíce tiché a soundproof. Drapes na stěnách se dá použít ke snížení výsledků a neutralizovat nebo "deaden" zvuku místnosti.

Použijte vysoce kvalitní mikrofon ("MIC"), který je určený pro záznam hlasu. Sennheiser, AKG a ještě novější přiblížení MICS mohou přinést dobré výsledky. Můžete koupit mikrofon nebo si ho pronajímat od místního audiovizuálního podniku. Vyhledejte ho s rozhraním USB. Tento typ MIC umožňuje snadnou kombinaci prvku Microphone, Preamp a analogového a digitálního konvertoru do jednoho balíčku, což zjednodušuje propojení.

Můžete použít také analogový mikrofon. Řada nájemních telefonů nabízí "roční" mikrotelefony uznávaného pro svůj hlasový znak. Mějte na paměti, že profesionální analogové využívání používá vyrovnávání konektory XLR místo konektoru 1/4-palce, který se používá v zákaznických zařízeních. Pokud přecházíte na analogová, budete také potřebovat Preamp a zvukové rozhraní počítače s těmito konektory.

Nainstalujte mikrofon na stojan nebo bác a před mikrofon nainstalujte filtr pop, abyste vyloučili šum z "plosive" souhlásky, jako je "p" a "b". Některá mikrotelefony se dodávají s připojením pro pozastavení, které je izoluje od vibrací v rámci stojanu, což je užitečné.

Hlasový talentů musí zůstat v konzistentní vzdálenosti od mikrofonu. Použijte pásku na podlaze k označení místa, kde se mají stát. Pokud talentů upřednostňuje, přidávejte zvláštní péči, abyste mohli monitorovat vzdálenost mikrofonu a vyhnout se hluku křesla.

Pro uchování skriptu použijte stojan. Vyhněte se angling stojanu, aby mohl odrážet zvuk směrem k mikrofonu.

Osoba, která je schopná nahrávat zařízení – inženýr – by měla být v odděleném prostoru od talentů, a to nějakým způsobem, aby se talentů v záznamovém stánku ( *okruh TalkBack).*

Záznam by měl obsahovat co nejmenší šum s cílem poměru mezi 80-dB nebo lepším způsobem.

Nahlaste se těsně na záznam ticha ve svém "stánku", kde se dozvíte z nějakého hluku a odstraňte příčinu. Běžnými zdroji hluku jsou vzduchové větrací, přípravné zátěže, provoz na přilehlých cestách a ventilátory zařízení (dokonce i přenosné počítače můžou mít ventilátory). Mikrofony a kabely můžou vyzradit elektrický hluk z blízkých kabeláží střídavého napětí, obvykle Hum nebo žhavých novinek. Žhavých novinek může být také způsobena *podlahovou smyčkou*, což je způsobeno tím, že zařízení je zapojeno do více než jednoho elektrického okruhu.

> [!TIP]
> V některých případech můžete použít modul plug-in pro snížení nebo snížení hluku softwaru, abyste mohli odstranit šum v záznamech, i když je vždy nejlepší ho zastavit na jeho zdroji.

Nastavte úrovně tak, aby většina dostupného dynamického rozsahu digitálního záznamu byla používána bez přeřízení. To znamená, že nahlas nastaví zvuk, ale ne tak hlasitě, že se přestaly rušit. Příklad zvukového signálu dobré nahrávky je znázorněn na následujícím obrázku:

![Dobrý záznam Wave](media/custom-voice/good-recording.png)

Zde se používá většina rozsahu (výška), ale nejvyšší vrcholy signálu nedosáhnou horní nebo dolní části okna. Můžete také zjistit, že tichá nahrávka v záznamu je přibližně tenká vodorovná čára, což značí nízkou hlučnost. Tento záznam má přijatelný poměr dynamického rozsahu a signálu na šum.

Zaznamenejte přímo do počítače přes kvalitní zvukové rozhraní nebo port USB v závislosti na použitém mikrofonu. V případě analogového záznamu Udržujte zvukový řetězec jednoduché: mic, Preamp, audio interface, Computer. Licence na [nástroje Avid pro](https://www.avid.com/en/pro-tools) a [Adobe Audition](https://www.adobe.com/products/audition.html) můžete licencovat s přiměřenými náklady. Pokud je váš rozpočet extrémně těsný, vyzkoušejte bezplatné [Audacity](https://www.audacityteam.org/).

Záznam v 44,1 kHz 16 bitů monophonic (Kvalita CD) nebo lepší. Aktuální stav-na obrázku je 48 kHz 24 bitů, pokud ho vaše zařízení podporuje. Před odesláním na vlastní hlasový portál budete mít zvuk v ukázce na 16 kHz 16 bitů. I nadále platí, že při úpravách událostí je potřeba mít vysoce kvalitní originální záznam.

V ideálním případě mají různí lidé v rolích ředitele, inženýr a talentů. Nepokoušejte se to udělat sami. V gesto roztažení prstů může být jedním z uživatelů ředitel i inženýr.

### <a name="before-the-session"></a>Před relací

Abyste se vyhnuli zaznamenání času studia, spusťte skript s hlasovým talentů před relaci nahrávání. I když se hlasový talentů bude dobře rozumět textu, může objasnit výslovnost všech neznámých slov.

> [!NOTE]
> Většina záznamů studia nabízí elektronický zobrazení skriptů v záznamovém kabině. V takovém případě zadejte poznámky ke spuštění přímo do dokumentu skriptu. Pořád budete chtít v průběhu relace pořizovat poznámky k papírové kopii, i když. Většina techniků bude chtít také pevně nakopírovat. A pořád budete potřebovat třetí tištěnou kopii jako zálohu pro talentů pro případ, že je počítač mimo provoz.

Vaše hlasová talentů může požádat o to, které slovo chcete zdůraznit v utterance ("rozhodné slovo"). Řekněte jim, že chcete přirozeným čtením bez zvláštního zdůraznění. Při syntetizování řeči je možné přidat zdůraznění. neměl by být součástí původního záznamu.

Nasměrujte talentů tak, aby vysloví odlišně. Každé slovo skriptu by mělo být vyslovované jako zapsané. Zvuky by se neměly vynechat ani se musí nacházet společně, protože jsou běžné při příležitostném rozpoznávání řeči, *Pokud je ve skriptu nevytvořila možnost*.

|Psaný text|Nežádoucí neformální výslovnost|
|-|-|
|nikdy nebudete mít|nikdy nebudete mít|
|Existují čtyři indikátory|Existují čtyři indikátory|
|Jak je dnes počasí|Jak je to v dnešní době počasí|
|Řekněte mi trochu příteli|Řekněme, že Hello na My Lil "Friend|

Talentů by neměl *Přidat* odlišná pozastavení mezi slovy. Tato věta by pořád probíhala přirozeně, i když je málo formální. Toto jemné rozlišení může být v praxi.

### <a name="the-recording-session"></a>Relace nahrávání

Vytvořte záznam odkazu nebo porovnávací *soubor* na začátku relace na začátku utterance. Požádejte talentů o opakování této řádky na každou stránku. Pokaždé Porovnejte nový záznam s odkazem. Tento postup pomůže talentů zůstat v souladu se svazkem, tempo, roztečí a výbuchem. Mezitím může inženýr použít soubor shody jako referenci pro úrovně a celkovou konzistenci zvuku.

Soubor shody je obzvláště důležitý při pokračování záznamu po přerušení nebo v jiném dni. Budete ho chtít několikrát Přehrát pro talentů a nechat si ho pokaždé zopakovat, dokud nebudou správně spárovány.

Zavagonujte své talentů, abyste si převzali hloubkové Breath a zastavili chvíli před každým utterance. Zaznamenejte několik sekund tichosti mezi projevy. Slova by měla být vyslovovaná stejně jako při každém zobrazení, s ohledem na kontext. Například položka "Record" jako příkaz je vyslovně odlišná od "Record" jako podstatného jména.

Zaznamenejte si před prvním záznamem ještě pět sekund tichého záznamu k zachycení "tónového tónu". Tento postup pomáhá vlastním hlasovým portálům kompenzovat veškerý zbývající šum v záznamech.

> [!TIP]
> Vše, co opravdu potřebujete zachytit, je hlasový talentů, takže můžete vytvořit záznam monophonic (v jednom kanálu) pouze pro jejich řádky. Pokud však zaznamenáte v stereofonním programu, můžete k nahrání chatu v řídicí místnosti použít druhý kanál a zachytit diskuzi o konkrétních řádcích nebo pořídit. Odeberte tuto stopu z verze, která je nahraná na vlastní hlasový portál.

Využijte sluchátka k výkonu hlasového talentůu a používejte sluchátka. Hledáte dobrý, ale přirozený Diction, správnou výslovnost a nedostatek nežádoucích zvuků. Neváhajíte se na talentů, abyste si poznamenali, jak znovu nahrávat utterance, které tyto standardy nesplňuje.

> [!TIP]
> Pokud používáte velký počet projevy, nemusí mít jeden utterance znatelný dopad na výsledný vlastní hlas. Je možné, že si jednoduše poznamenejte všechny projevy s problémy, vyloučíte je z datové sady a zjistíte, jak se vlastní hlas zapíná. Kdykoli se můžete vrátit k studiu a zaznamenat zmeškané vzorky později.

Všimněte si, že se ve vašem skriptu převezme kód pro každý utterance. Požádejte inženýra o označení jednotlivých utterance v metadatech nahrávky nebo na listu hromádky.

Využijte pravidelných konců a poskytněte nápoj, který vašemu hlasu talentů ponechá svůj hlas v dobrém tvaru.

### <a name="after-the-session"></a>Po relaci

Moderní záznam studia běží na počítačích. Na konci relace obdržíte jeden nebo více zvukových souborů, nikoli pásku. Tyto soubory budou pravděpodobně ve formátu WAV nebo AIFF ve kvalitě CD (44,1 kHz 16 bitů) nebo vyšší. 48 kHz 24 bitů je běžné a žádoucí. Vyšší vzorkovací frekvence, například 96 kHz, nejsou obecně potřeba.

Vlastní hlasový portál vyžaduje, aby každý poskytnutý utterance byl ve vlastním souboru. Jednotlivé zvukové soubory dodávané nástrojem Studio obsahují více projevy. Primární úkol po výrobě proto slouží k rozdělení záznamů a jejich přípravě na odeslání. Inženýr pro zaznamenávání může umístit značky do souboru (nebo poskytnout samostatný návodový list), který indikuje, kde se jednotlivé utterancey spustí.

Pomocí svých poznámek můžete najít přesný postup a pak použít nástroj pro úpravy zvuku, jako jsou [nástroje Avid pro](https://www.avid.com/en/pro-tools), [Adobe Audition](https://www.adobe.com/products/audition.html)nebo [Audacity](https://www.audacityteam.org/), ke zkopírování každého utterance do nového souboru.

Na začátku a konci každého klipu nechte jenom asi 0,2 sekund ticha, s výjimkou prvního. Tento soubor by měl začínat celým pěti sekundami ticha. Nepoužívejte zvukový editor pro tiché části souboru "Zero out". Zahrnutím "tónového tónu" pomůžete vlastní algoritmy hlasu kompenzovat veškerý zbytkový hluk na pozadí.

Pečlivě naslouchat každému souboru. V této fázi můžete upravit malé nechtěné zvuky, které jste během nahrávání nenalezli, jako je například lehká sada LIP Smack před řádkem, ale pozor, abyste neodebrali žádný skutečný hlas. Pokud soubor nemůžete opravit, odeberte ho z datové sady a Všimněte si, že jste tak učinili.

Převeďte každý soubor na 16 bitů a vzorkovací frekvenci o 16 kHz před uložením a pokud jste si poznamenali, že jste nahráli chat Studio, odeberte druhý kanál. Uložte každý soubor ve formátu WAV a pojmenujte soubory s utterance číslem ze svého skriptu.

Nakonec vytvořte *přepis* , který přidruží jednotlivé soubory WAV k textové verzi odpovídající utterance. [Vytváření vlastních hlasových písem](./how-to-custom-voice-create-voice.md) obsahuje podrobnosti o požadovaném formátu. Text můžete zkopírovat přímo z vašeho skriptu. Pak vytvořte soubor zip se soubory WAV a přepis textu.

Archivujte původní nahrávky na bezpečném místě pro případ, že je budete potřebovat později. Zachovejte i svůj skript a poznámky.

## <a name="next-steps"></a>Další kroky

Jste připraveni nahrát vaše nahrávky a vytvořit vlastní hlas.

> [!div class="nextstepaction"]
> [Vytváření vlastních hlasových písem](./how-to-custom-voice-create-voice.md)