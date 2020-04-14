---
title: Záznam vlastních hlasových ukázek – služba Řeči
titleSuffix: Azure Cognitive Services
description: Vytvořte vlastní hlas v produkční kvalitě tím, že připravíte robustní skript, najměte dobrý hlasový talent a profesionálně nahráváte.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: erhopf
ms.openlocfilehash: 2897fe2e0cc8aeb929b0a33f5cdaba9c60f2a244
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261580"
---
# <a name="record-voice-samples-to-create-a-custom-voice"></a>Záznam hlasových vzorků pro vytvoření vlastního hlasu

Vytvoření vysoce kvalitní produkce vlastní hlas od nuly není příležitostný podnik. Ústřední složkou vlastního hlasu je velká sbírka zvukových vzorků lidské řeči. Je důležité, aby tyto zvukové nahrávky byly vysoce kvalitní. Vyberte si hlasový talent, který má zkušenosti s vytvářením těchto druhů nahrávek, a nechte je nahrávat příslušným nahrávacím technikem pomocí profesionálního vybavení.

Než budete moci tyto nahrávky, i když budete potřebovat skript: slova, která bude mluvený váš hlas talent k vytvoření zvukových vzorků. Pro dosažení nejlepších výsledků musí mít skript dobré fonetické pokrytí a dostatečnou rozmanitost pro trénování vlastního hlasového modelu.

Mnoho malých, ale důležitých detailů jde do vytvoření profesionálního hlasového záznamu. Tato příručka je plán pro proces, který vám pomůže získat dobré, konzistentní výsledky.

> [!TIP]
> Chcete-li dosáhnout výsledků nejvyšší kvality, zvažte zapojení společnosti Microsoft, která vám pomůže rozvíjet vlastní hlas. Společnost Microsoft má rozsáhlé zkušenosti s vytvářením vysoce kvalitních hlasů pro své vlastní produkty, včetně Cortany a Office.

## <a name="voice-recording-roles"></a>Role nahrávání hlasu

V projektu vlastního záznamu hlasu jsou čtyři základní role:

Role|Účel
-|-
Hlasový talent        |Hlas této osoby bude tvořit základ vlastního hlasu.
Záznamový inženýr  |Dohlíží na technické aspekty záznamu a provozuje záznamové zařízení.
Ředitel            |Připravuje scénář a trénuje výkon hlasového talentu.
Editor              |Dokončí zvukové soubory a připraví je k nahrání na portál Vlastní hlas.

Jednotlivec může plnit více než jednu roli. Tato příručka předpokládá, že budete primárně plnit roli režiséra a najímat jak hlasový talent, tak nahrávacího inženýra. Pokud chcete, aby se nahrávky sami, tento článek obsahuje některé informace o roli záznam inženýra. Role editoru je potřeba až po zasedání, takže může být provedena režisérem nebo nahrávacím inženýrem.

## <a name="choose-your-voice-talent"></a>Vyberte si svůj hlasový talent

Herci se zkušenostmi v dabingu nebo hlasové charakter práce, aby dobré vlastní hlas talent. Můžete také často najít vhodný talent mezi hlasatele a newsreaders.

Vyberte si hlasový talent, jehož přirozený hlas se vám líbí. Je možné vytvořit jedinečné "charakter" hlasy, ale je to mnohem těžší pro většinu talentů, aby je důsledně, a úsilí může způsobit hlasové napětí.

> [!TIP]
> Obecně platí, že se vyhněte používání rozpoznatelných hlasů k vytvoření vlastního hlasu - pokud samozřejmě není vaším cílem vytvořit hlas celebrit. Méně známé hlasy jsou obvykle méně rušivé pro uživatele.

Jedním z nejdůležitějších faktorů pro výběr hlasových talentů je konzistence. Vaše nahrávky by měly znít, jako by byly pořízeny ve stejný den ve stejné místnosti. K tomuto ideálu můžete přistupovat prostřednictvím osvědčených postupů nahrávání a inženýrství.

Tvůj hlasový talent je druhá polovina rovnice. Musí být schopni mluvit s konzistentní rychlostí, úrovní hlasitosti, roztečí a tónem. Jasná dikce je nutností. Talent také musí být schopen přísně kontrolovat jejich hřiště variace, emocionální vliv, a řeči manýry.

Nahrávání vlastních hlasových vzorků může být více fatiguing než jiné druhy hlasové práce. Většina hlasových talentů může nahrávat dvě nebo tři hodiny denně. Omezte relace na tři nebo čtyři týdně, pokud je to možné, můžete si mezi nimi odpojit.

Nahrávky pro hlasový model by měly být emocionálně neutrální. To znamená, že smutný výrok by neměl být chápán smutným způsobem. Nálada může být přidána do syntetizované řeči později prostřednictvím prozody ovládacích prvků. Spolupracujte se svým hlasovým talentem na vytvoření "persony", která definuje celkový zvuk a emocionální tón vlastního hlasu. V tomto procesu, budete určit, co "neutrální" zní jako pro tuto osobnost.

Osobnost může mít například přirozeně pozitivní osobnost. Takže "jejich" hlas může nést tón optimismu, i když mluví neutrálně. Nicméně, takový osobnostní rys by měl být jemný a konzistentní. Poslechněte si čtení podle stávajících hlasů, abyste získali představu o tom, o co usilujete.

> [!TIP]
> Obvykle budete chtít vlastnit hlasové nahrávky, které poděláte. Váš hlasový talent by měl být přístupný pracovní smlouvě pro projekt.

## <a name="create-a-script"></a>Vytvoření skriptu

Výchozím bodem každé vlastní relace nahrávání hlasu je skript, který obsahuje projevy, které mají být mluveny váš hlas talent. (Termín "projevy" zahrnuje jak úplné věty, tak kratší fráze.)

Projevy ve vašem scénáři mohou pocházet odkudkoliv: fikce, non-fiction, přepisy projevů, zpravodajství, a cokoli v tištěné podobě. Chcete-li se ujistit, že váš hlas dělá dobře na konkrétní druhy slov (jako je lékařská terminologie nebo programovací žargon), možná budete chtít zahrnout věty z vědeckých prací nebo technických dokumentů. Stručnou diskusi o možných právních otázkách naleznete v části ["Zákonnost".](#legalities) Můžete také napsat vlastní text.

Vaše projevy nemusí pocházet ze stejného zdroje nebo stejného druhu zdroje. Ani spolu nepotřebují mít nic společného. Pokud však budete používat nastavené fráze (například "Úspěšně jste se přihlásili") v aplikaci pro rozpoznávání řeči, nezapomeňte je zahrnout do skriptu. To dá váš vlastní hlas větší šanci vyslovovat tyto fráze dobře. A pokud byste se měli rozhodnout použít nahrávku místo syntetizované řeči, budete ji již mít stejným hlasem.

Zatímco konzistence je klíčem při výběru hlasového talentu, rozmanitost je charakteristickým znakem dobrého scénáře. Skript by měl obsahovat mnoho různých slov a vět s různými délkami vět, struktur a nálad. Každý zvuk v jazyce by měl být reprezentován vícekrát a v mnoha kontextech *(tzv. fonetický pokrytí).*

Kromě toho by měl text obsahovat všechny způsoby, jak může být určitý zvuk reprezentován písemně, a umístit každý zvuk na různá místa ve větách. Obě deklarativní věty a otázky by měly být zahrnuty a přečteny s příslušnou intonací.

Je obtížné napsat skript, který poskytuje *pouze dostatek* dat, aby portál vlastní řeči vytvořit dobrý hlas. V praxi je nejjednodušším způsobem, jak vytvořit skript, který dosahuje robustnífonetické pokrytí, zahrnout velké množství vzorků. Standardní hlasy, které poskytuje Microsoft byly vytvořeny z desítek tisíc projevy. Měli byste být připraveni zaznamenat několik až několik tisíc projevy na minimum k vybudování produkční kvality vlastní hlas.

Pečlivě zkontrolujte, zda skript nezasílá chyby. Pokud je to možné, aby to zkontroloval i někdo jiný. Když si projdete scénář se svým talentem, pravděpodobně chytíte ještě pár chyb.

### <a name="script-format"></a>Formát skriptu

Skript můžete napsat v aplikaci Microsoft Word. Skript je určen pro použití během nahrávání relace, takže si můžete nastavit tak, jak zjistíte, snadno pracovat. Vytvořte textový soubor, který je vyžadován portálem Vlastní hlas samostatně.

Základní formát skriptu obsahuje tři sloupce:

* Číslo utterance, počínaje 1. Číslování usnadňuje všem ve studiu odkazovat na konkrétní utterance ("zkusme číslo 356 znovu"). Pomocí funkce číslování odstavců aplikace Word můžete automaticky číslovat řádky tabulky.
* Prázdný sloupec, ve kterém napíšete číslo nebo časový kód každého utterance, který vám pomůže najít v dokončeném záznamu.
* Text utterance sám.

![Ukázkový skript](media/custom-voice/script.png)

> [!NOTE]
> Většina studií záznam v krátkých segmentech známý jako *bere*. Každý trvat obvykle obsahuje 10 až 24 projevy. Jen zaznamenáme take číslo je dostačující najít utterance později. Pokud nahráváte ve studiu, které dává přednost delším nahráváním, budete místo toho chtít zaznamenat časový kód. Studio bude mít prominentní zobrazení času.

Po každém řádku ponechte dostatek místa pro psaní poznámek. Ujistěte se, že žádné utterance je rozdělena mezi stránkami. Očíslujte stránky a vytiskněte skript na jedné straně papíru.

Vytisknout tři kopie skriptu: jeden pro talent, jeden pro inženýra, a jeden pro režiséra (vy). Místo svorek použijte kancelářskou sponku: zkušený hlasový umělec oddělí stránky, aby při otočných stránkách nedělal šum.

### <a name="legalities"></a>Zákonná

Podle autorského práva může být hercovo čtení textu chráněného autorskými právy představením, za které by měl být autor díla odškodněn. Tento výkon nebude rozpoznatelný v konečném produktu, vlastní hlas. Přesto zákonnost používání díla chráněného autorskými právy k tomuto účelu není dobře zavedena. Společnost Microsoft nemůže v této věci poskytovat právní poradenství. poraďte se se svým právním zástupcem.

Naštěstí je možné se těmto problémům zcela vyhnout. Existuje mnoho zdrojů textu, které můžete použít bez povolení nebo licence.

|Zdroj textu|Popis|
|-|-|
|[CMU Arktický korpus](http://festvox.org/cmu_arctic/)|Asi 1100 vět vybraných z děl mimo autorská práva speciálně pro použití v projektech syntézy řeči. Vynikající výchozí bod.|
|Již nefunguje<br>pod autorským právem|Typicky díla publikovaná před rokem 1923. Pro angličtinu nabízí [projekt Gutenberg](https://www.gutenberg.org/) desítky tisíc takových děl. Možná se budete chtít zaměřit na novější díla, protože jazyk bude blíže k moderní angličtině.|
|Vládní&nbsp;práce|Díla vytvořená vládou Spojených států nejsou ve Spojených státech chráněna autorskými právy, ačkoli vláda může požadovat autorská práva v jiných zemích nebo oblastech.|
|Veřejná doména|Díla, pro která byla autorská práva výslovně zneříkána nebo která byla věnována public domain. V některých jurisdikcích nemusí být možné zcela upustit od autorských práv.|
|Tolerantně licencovaná díla|Díla distribuovaná pod licencí, jako je Creative Commons nebo GNU Free Documentation License (GFDL). Wikipedie používá GFDL. Některé licence však mohou ukládat omezení výkonu licencovaného obsahu, která mohou mít vliv na vytvoření vlastního hlasového modelu, proto si licenci pečlivě přečtěte.|

## <a name="recording-your-script"></a>Nahrávání skriptu

Nahrajte svůj skript v profesionálním nahrávacím studiu, které se specializuje na hlasovou práci. Budou mít nahrávací kabinu, správné vybavení a ty správné lidi, kteří ji budou obsluhovat. Vyplatí se nešetřit na nahrávání.

Diskutujte o svém projektu s nahrávacím technikem studia a poslechněte si jejich rady. Záznam by měl mít malou nebo žádnou kompresi dynamického rozsahu (maximálně 4:1). Je důležité, aby zvuk měl konzistentní hlasitost a vysoký poměr signálu k šumu, aniž by byl nežádoucí.

### <a name="do-it-yourself"></a>Udělej to sám

Pokud chcete, aby se nahrávka sami, spíše než jít do nahrávacího studia, zde je krátký primer. Díky vzestupu domácí nahrávání a podcasting, je to jednodušší než kdy jindy najít dobré nahrávání poradenství a zdroje on-line.

Vaše "nahrávací kabina" by měla být malá místnost bez nápadné ozvěny nebo "tónmístnosti". Mělo by to být co nejtišší a zvukotěsné. Závěsy na stěnách mohou být použity ke snížení ozvěny a neutralizovat nebo "umrtvit" zvuk místnosti.

Používejte vysoce kvalitní studiový kondenzátorový mikrofon (zkráceně "mic") určený pro záznam hlasu. Sennheiser, AKG, a ještě novější Zoom mikrofony mohou přinést dobré výsledky. Můžete si koupit mikrofon, nebo si ho pronajmout v místní audio-vizuální půjčovně. Podívejte se na jeden s rozhraním USB. Tento typ mikrofonu pohodlně kombinuje mikrofonní prvek, předzesilovač a analogově digitální převodník do jednoho balíčku, což zjednodušuje připojení.

Můžete také použít analogový mikrofon. Mnoho nájemních domů nabízí "vintage" mikrofony proslulé svým hlasovým charakterem. Všimněte si, že profesionální analogové zařízení používá vyvážené XLR konektory, spíše než 1/4-palcový konektor, který se používá ve spotřebním zařízení. Pokud půjdete analogové, budete také potřebovat předzesilovač a počítačové audio rozhraní s těmito konektory.

Nainstalujte mikrofon na stojan nebo výložník a nainstalujte pop filtr před mikrofonem, abyste eliminovali hluk z "plosive" souhlásky jako "p" a "b". Některé mikrofony jsou dodávány s držákem odpružení, který je izoluje od vibrací na stojanu, což je užitečné.

Hlasový talent musí zůstat v konzistentní vzdálenosti od mikrofonu. Pomocí pásky na podlaze označte, kde by měly stát. Pokud talent preferuje sedět, věnujte zvláštní pozornost sledování vzdálenosti mikrofonu a vyhnout se hluku židle.

Použijte stojan držet skript. Vyhněte se nasávání stojanu tak, aby odrážel zvuk směrem k mikrofonu.

Osoba provozující záznamové zařízení - inženýr - by měla být v oddělené místnosti od talentu, s nějakým způsobem, jak mluvit s talentem v nahrávací kabině *(okruh talkback).*

Záznam by měl obsahovat co nejméně šumu, s cílem 80-db poměr signálu k šumu nebo lepší.

Pozorně poslouchejte nahrávku ticha ve svém "stánku", zjistěte, odkud přichází nějaký hluk, a odstraňte příčinu. Běžnými zdroji hluku jsou větrací otvory, předřadníky zářivky, provoz na okolních silnicích a ventilátory zařízení (dokonce i notebooky mohou mít ventilátory). Mikrofony a kabely mohou zachytit elektrický šum z nedalekého střídavého vedení, obvykle hukot nebo bzučení. Bzučení může být také způsobeno *zemní smyčkou*, která je způsobena tím, že zařízení je zapojeno do více než jednoho elektrického obvodu.

> [!TIP]
> V některých případech můžete použít ekvalizér nebo modul plug-in softwaru pro snížení šumu, který pomáhá odstranit šum z nahrávek, i když je vždy nejlepší jej zastavit u zdroje.

Nastavte úrovně tak, aby většina dostupného dynamického rozsahu digitálního záznamu byla použita bez přejíždění. To znamená, že zvuk nastavíte nahlas, ale ne tak hlasitě, aby se zkreslil. Příklad křivky dobrého záznamu je zobrazen na následujícím obrázku:

![Dobrý záznam průběh](media/custom-voice/good-recording.png)

Zde se používá většina rozsahu (výška), ale nejvyšší vrcholy signálu nedosahují horní nebo dolní části okna. Můžete také vidět, že ticho v záznamu se blíží tenké vodorovné čáře, což naznačuje nízkou hlučnost podlahy. Tento záznam má přijatelný dynamický rozsah a poměr signálu k šumu.

V závislosti na mikrofonu, který používáte, můžete nahrávat přímo do počítače prostřednictvím vysoce kvalitního zvukového rozhraní nebo portu USB. Pro analogový, aby audio řetězec jednoduchý: mic, předzesilovač, audio rozhraní, počítač. Můžete licencovat [avid Pro Tools](https://www.avid.com/en/pro-tools) a [Adobe Audition](https://www.adobe.com/products/audition.html) měsíčně za rozumnou cenu. Pokud je váš rozpočet extrémně těsný, vyzkoušejte zdarma [Audacity](https://www.audacityteam.org/).

Záznam na 44,1 kHz 16 bit monofonní (CD kvalita) nebo lepší. Aktuální stav je 48 kHz 24-bit, pokud vaše zařízení podporuje. Před odesláním na portál Vlastní hlas snížíte odběr vzorku zvuku na 16 kHz 16bit. Přesto se vyplatí mít vysoce kvalitní originální záznam v případě, že jsou potřebné úpravy.

V ideálním případě mají různí lidé slouží v rolích režiséra, inženýra a talentu. Nesnaž se to všechno udělat sám. V nouzi, jedna osoba může být jak režisér a inženýr.

### <a name="before-the-session"></a>Před zasedáním

Chcete-li se vyhnout plýtvání časem studia, projděte si skript s hlasovým talentem před nahráváním. Zatímco hlasový talent se seznámí s textem, mohou objasnit výslovnost všech neznámých slov.

> [!NOTE]
> Většina nahrávacích studií nabízí elektronické zobrazení skriptů v nahrávací kabině. V takovém případě zadejte run-through poznámky přímo do dokumentu skriptu. Stále však budete chtít, aby si během relace dělala poznámky. Většina inženýrů bude chtít také tištěnou kopii. A stále budete chtít třetí tištěnou kopii jako zálohu pro talenty v případě, že počítač nese.

Váš hlasový talent se může zeptat, které slovo chcete zdůraznit v utterance ("operativní slovo"). Řekněte jim, že chcete přirozené čtení bez zvláštního důrazu. Důraz lze přidat při syntetizování řeči; neměla by být součástí původní nahrávky.

Nasměrujte talent, aby vyslovoval slova zřetelně. Každé slovo skriptu by mělo být vyslovováno tak, jak je napsáno. Zvuky by neměly být vynechány nebo nezřetelné dohromady, jak je běžné v ležérní řeči, *pokud byly napsány tímto způsobem ve scénáři*.

|Psaný text|Nechtěná příležitostná výslovnost|
|-|-|
|nikdy se tě nevzdám.|nikdy se tě nevzdám.|
|k dispozici jsou čtyři světla|Jsou tu čtyři světla|
|Jaké je dnes počasí|Jaké je dnešní počasí|
|pozdravmého malého přítele|pozdravmého přítele lil'|

Talent by *neměl* přidávat zřetelné pauzy mezi slovy. Věta by měla stále proudit přirozeně, i když zní trochu formálně. Tento jemný rozdíl může trvat praxi, aby si právo.

### <a name="the-recording-session"></a>Relace nahrávání

Vytvořte referenční záznam nebo *soubor shody* typické utterance na začátku relace. Požádejte talent, aby tento řádek zopakoval na každé stránce nebo tak nějak. Pokaždé porovnejte novou nahrávku s odkazem. Tato praxe pomáhá talent zůstat konzistentní v objemu, tempo, hřiště, a intonace. Mezitím může inženýr použít soubor shody jako referenci pro úrovně a celkovou konzistenci zvuku.

Soubor shody je obzvláště důležitý, když pokračujete v nahrávání po přestávce nebo v jiný den. Budete chtít hrát několikrát pro talent a nechat je opakovat pokaždé, dokud nejsou odpovídající dobře.

Trénuj svůj talent, aby se zhluboka nadechl a na chvíli se zastavil před každým promluvou. Zaznamenejte pár vteřin ticha mezi projevy. Slova by měla být vyslovována stejným způsobem pokaždé, když se objeví, s ohledem na kontext. Například "záznam" jako sloveso je vyslovováno odlišně od "record" jako nosné slovo.

Zaznamenejte dobrých pět sekund ticha před prvním záznamem, abyste zachytili "tón místnosti". Tento postup pomáhá portálu Vlastní hlas kompenzovat zbývající šum v nahrávkách.

> [!TIP]
> Vše, co opravdu potřebujete zachytit, je hlasový talent, takže můžete vytvořit monofonní (jednokanálový) záznam pouze jejich linek. Pokud však nahráváte ve stereu, můžete použít druhý kanál k zaznamenání tlachání v kontrolní místnosti, abyste zachytili diskusi o konkrétních řádcích nebo záběrech. Odeberte tuto stopu z verze, která je odeslána na portál Vlastní hlas.

Poslouchejte pozorně, pomocí sluchátek, na výkon hlasového talentu. Hledáte dobrou, ale přirozenou dikci, správnou výslovnost a nedostatek nežádoucích zvuků. Neváhejte požádat svůj talent, aby znovu nahrál výrok, který nesplňuje tyto standardy.

> [!TIP]
> Pokud používáte velký počet projevy, jeden utterance nemusí mít znatelný vliv na výsledný vlastní hlas. Může být vhodnější jednoduše zaznamenat všechny projevy s problémy, vyloučit je z datové sady a zjistit, jak se váš vlastní hlas ukáže. Vždy se můžete vrátit do studia a později nahrát zmeškané vzorky.

Všimněte si, trvat číslo nebo časový kód na skriptu pro každý utterance. Požádejte inženýra, aby označil každý utterance v metadatech nebo tágo záznamu.

Udělejte si pravidelné přestávky a poskytněte nápoj, který pomůže vašemu hlasovému talentu udržet jejich hlas v dobré kondici.

### <a name="after-the-session"></a>Po zasedání

Moderní nahrávací studia běží na počítačích. Na konci relace obdržíte jeden nebo více zvukových souborů, nikoli pásku. Tyto soubory budou pravděpodobně WAV nebo AIFF formátu v kvalitě CD (44,1 kHz 16-bit) nebo lepší. 48 kHz 24-bit je běžné a žádoucí. Vyšší vzorkovací frekvence, například 96 kHz, nejsou obecně potřeba.

Vlastní hlasový portál vyžaduje, aby každý zadaný utterance byl ve svém vlastním souboru. Každý zvukový soubor dodaný studiem obsahuje více promluv. Takže primárním postprodukčním úkolem je rozdělit nahrávky a připravit je k odeslání. Záznamový inženýr mohl umístit značky do souboru (nebo za předpokladu, že samostatný startovací list) k označení, kde každý utterance začíná.

Pomocí poznámek vyhledejte přesně požadované poznámky a potom použijte nástroj pro úpravu zvuku, například [Avid Pro Tools](https://www.avid.com/en/pro-tools), [Adobe Audition](https://www.adobe.com/products/audition.html)nebo bezplatnou [audacity](https://www.audacityteam.org/), ke zkopírování každého utterance do nového souboru.

Na začátku a na konci každého klipu ponechte jen asi 0,2 sekundy ticha, s výjimkou prvního. Ta složka by měla začínat pěti vteřinami ticha. Nepoužívejte zvukový editor k "nule" tiché části souboru. Zahrnutí "tónu místnosti" pomůže algoritmům Custom Voice kompenzovat jakýkoli zbytkový šum na pozadí.

Poslechněte si pozorně každý soubor. V této fázi můžete upravit malé nežádoucí zvuky, které jste vynechali během nahrávání, jako je mírný ret před čárou, ale dávejte pozor, abyste neodstranili žádnou skutečnou řeč. Pokud soubor nemůžete opravit, odeberte ho z datové sady a všimněte si, že jste tak učinili.

Převeďte každý soubor na 16 bitů a vzorkovací frekvenci 16 kHz před uložením a pokud jste zaznamenali studiotklá řeč, odstraňte druhý kanál. Uložte každý soubor ve formátu WAV a pojmenujte soubory číslem utterance ze skriptu.

Nakonec vytvořte *přepis,* který přidruží každý soubor WAV s textovou verzí odpovídající utterance. [Vytvoření vlastních hlasových písem](how-to-customize-voice-font.md) obsahuje podrobnosti o požadovaném formátu. Text můžete zkopírovat přímo ze skriptu. Pak vytvořte soubor ZIP souborů WAV a přepis textu.

Archivujte původní nahrávky na bezpečném místě pro případ, že byste je později potřebovali. Zachovat skript a poznámky, taky.

## <a name="next-steps"></a>Další kroky

Jste připraveni nahrát své nahrávky a vytvořit si vlastní hlas.

> [!div class="nextstepaction"]
> [Vytvoření vlastních hlasových písem](how-to-customize-voice-font.md)
