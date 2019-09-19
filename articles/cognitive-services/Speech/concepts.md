---
title: Pojmy Zpracování řeči Bingu | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Základní pojmy používané ve službě Microsoft Speech Service.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: fba1bbdeaf68bdd45524b336011627a27cd024da
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965717"
---
# <a name="basic-concepts"></a>Základní koncepty

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Tato stránka popisuje několik základních konceptů ve službě rozpoznávání řeči od společnosti Microsoft. Před použitím rozhraní API pro rozpoznávání řeči společnosti Microsoft ve vaší aplikaci doporučujeme tuto stránku přečíst.

## <a name="understanding-speech-recognition"></a>Porozumění rozpoznávání řeči

Při prvním vytváření aplikace s podporou rozpoznávání řeči nebo při prvním přidání funkcí pro rozpoznávání řeči do existující aplikace vám tato část pomůže začít. Pokud už máte nějaké zkušenosti s aplikacemi s podporou řeči, můžete se rozhodnout jenom skim této části, nebo ji můžete přeskočit úplně, pokud jste při rozpoznávání řeči starí a chcete získat právo k podrobnostem protokolu.

### <a name="audio-streams"></a>Zvukové streamy

*Zvukovým datovým proudem*je základní koncepce řeči. Na rozdíl od klávesových zkratek, ke kterým dochází v jednom bodě v čase a obsahuje jednu část informací, mluvený požadavek se rozprostře po stovkách milisekund a obsahuje mnoho kilobajtů informací. Doba trvání mluveného projevyu předkládá vývojářům možnost využít pro své aplikace zjednodušené a elegantní prostředí pro rozpoznávání řeči. Dnešní počítače a algoritmy provádějí rozpoznávání řeči v přibližně polovině doby trvání utterance, takže 2 sekundová utterance může být přepisu přibližně 1 sekundu, ale každá aplikace, která je v průběhu 1 sekundové prodlevy při zpracování uživatele, je. není jednodušší ani elegantní.

Naštěstí existuje několik způsobů, jak "skrývat" dobu přepisu, a to tak, že provedete přepis jednoho dílu utterance a uživatel mluví o jiné části. Například rozdělením utterance o 1 sekundu na 10 bloků po 100 milisekund a provedením přepisu na každém bloku v důsledku toho může být více než 450 celkové 500 milisekund potřebných pro přepis "skryté", aby uživatel nevědom přepisu byl "skrytý". provádí se při mluvení. Při zvažování tohoto příkladu Pamatujte na to, že služba provádí přepis předchozích 100 milisekund zvuků, zatímco uživatel mluví o další 100, takže když uživatel přestane mluvit, služba bude muset přepisovat zhruba 100. milisekundy zvukového výsledku, které vytvoří výsledek.

Pro dosažení tohoto uživatelského prostředí se mluvené zvukové informace shromažďují v blocích a přepisu, když uživatel mluví. Tyto zvukové bloky se souhrnně ze *zvukového datového proudu*shromažďují a proces odesílání těchto zvukových bloků do služby se nazývá *streamování zvuku.* Streamování zvuku je důležitou součástí jakékoli aplikace s podporou rozpoznávání řeči. vyladění velikosti bloku dat a optimalizace implementace streamování jsou některé z nejvýznamnějších způsobů, jak zlepšit uživatelské prostředí vaší aplikace.

### <a name="microphones"></a>Mikrofonů

Lidé zpracovávají mluvený zvuk pomocí jejich EARS, ale inanimace hardwaru používá mikrofony. Pokud chcete povolit rozpoznávání řeči v libovolné aplikaci, je třeba se zapojit do mikrofonu, který poskytuje zvukový stream pro vaši aplikaci.

Rozhraní API pro váš mikrofon musí umožňovat spuštění a zastavení příjmu zvukových bajtů z mikrofonu. Musíte rozhodnout, jaké akce uživatele aktivují mikrofon, aby bylo možné začít naslouchat rozpoznávání řeči. Můžete zvolit, aby stisknutí tlačítka aktivovalo spuštění naslouchání, nebo můžete zvolit *klíčové slovo* nebo *probuzení slovem* spotter, které vždycky naslouchá mikrofonu a využívalo výstup tohoto modulu, aby se mohl aktivovat odesílání zvuků na řeč Microsoftu. Službám.

### <a name="end-of-speech"></a>Konec řeči

Zjištění *, že se* mluvčí *přestane* mluvit, je pro člověka dostatečně snadné, ale jedná se o obtížně problém mimo laboratorní podmínky. Po utterance nestačí jednoduše najít čistý ticho, protože je často hodně okolního hluku, které by bylo možné zkomplikovat. Služba Microsoft Speech Service nabízí skvělou úlohu rychlého zjištění, kdy uživatel přestala mluvit, a služba může tuto skutečnost informovat. Toto uspořádání ale znamená, že vaše aplikace je poslední, aby znala, že uživatel přestane mluvit. To se vůbec nelíbí jako u jiných forem vstupu, kde je vaše aplikace *první* , co je třeba znát, když se vstup uživatele spustí *a* skončí.

### <a name="asynchronous-service-responses"></a>Asynchronní odpovědi služby

Skutečnost, že vaše aplikace musí být informována o dokončení vstupu uživatele, nevede ke snížení výkonu ani programování problémů aplikace, ale vyžaduje, abyste si myslíte, že se požadavky na řeč liší od vstupních požadavků/ vzory odpovědí, se kterými jste obeznámeni. Vzhledem k tomu, že vaše aplikace nebude znát, kdy uživatel přestane mluvit, musí aplikace dál streamovat zvuk do služby a současně i asynchronně čekat na odpověď služby. Tento model se na rozdíl od jiných webových protokolů požadavků a odpovědí, jako je HTTP, nelíbí. V těchto protokolech je nutné před přijetím odpovědi dokončit žádost. v protokolu služby Microsoft Speech Service obdržíte odpovědi, *dokud budete pro požadavek dál streamovat zvuk*.

> [!NOTE]
> Tato funkce není podporována při použití REST API řeči HTTP.

### <a name="turns"></a>Zčerná

Řeč je dopravcem informací. Při mluvení se snažíte předat informace, které jsou ve vašem vlastnictví, pro někoho, kdo na tyto informace naslouchá. Při předávání informací obvykle nebudete mluvit a naslouchat. Stejně tak vaše aplikace podporující rozpoznávání řeči komunikuje s uživateli tím, že střídavě naslouchá a reaguje, i když vaše aplikace většinou provádí většinu poslechu. Mluvený vstup uživatele a odpověď služby na tento vstup se označuje jako *zapínání*. Spustí *se, když* uživatel mluví a končí, když aplikace dokončí zpracování odpovědi služby Speech.

### <a name="telemetry"></a>Telemetrie

Vytvoření zařízení nebo aplikace s podporou rozpoznávání řeči může být náročné i u zkušených vývojářů. Protokoly založené na streamech se často zdají těžké na první pohled a důležité podrobnosti, jako je detekce tichého navýšení, můžou být zcela nové. Aby bylo možné úspěšně odeslat a přijmout mnoho zpráv, aby bylo možné dokončit jednu dvojici požadavků a odpovědí, je *velmi* důležité shromáždit pro tyto zprávy úplná a přesná data. Pro shromažďování těchto dat poskytuje protokol služby Microsoft Speech Service. Měli byste zajistit, aby co nejlépe poskytovala požadovaná data. poskytnutím kompletních a přesných dat vám pomůžeme – měli byste při řešení potíží s implementací klientů potřebovat pomoc tým služby Microsoft Speech Service, protože kvalita dat telemetrie, kterou jste shromáždili, bude pro problém zásadní. výsledcích.

> [!NOTE]
> Tato funkce není podporována při použití REST API rozpoznávání řeči.

### <a name="speech-application-states"></a>Stavy aplikace Speech

Kroky, které provedete při povolování vstupu řeči ve vaší aplikaci, se trochu liší od kroků pro jiné formy vstupu, jako je kliknutí myší nebo klepnutí na prst. Je nutné sledovat, kdy aplikace naslouchá mikrofonu a odesílá data do služby pro rozpoznávání řeči, když čeká na odpověď služby a když je ve stavu nečinnosti. Vztah mezi těmito stavy je zobrazený v diagramu níže.

![Diagram stavu aplikace Speech](Images/speech-application-state-diagram.png)

Vzhledem k tomu, že je služba Microsoft Speech Service zapojena do některých stavů, protokol služby definuje zprávy, které pomůžou přechod aplikace mezi stavy. Vaše aplikace musí interpretovat a reagovat na tyto zprávy protokolu pro sledování a správu stavů aplikace pro rozpoznávání řeči.

## <a name="using-the-speech-recognition-service-from-your-apps"></a>Používání služby rozpoznávání řeči z vašich aplikací

Služba rozpoznávání řeči od Microsoftu nabízí dva způsoby, jak vývojářům přidávat do svých aplikací řeč.

- [Rozhraní REST API](GetStarted/GetStartedREST.md): Vývojáři můžou použít volání HTTP ze svých aplikací do služby pro rozpoznávání řeči.
- [Klientské knihovny](GetStarted/GetStartedClientLibraries.md): V případě pokročilých funkcí můžou vývojáři stahovat klientské knihovny Microsoft Speech a propojit je s aplikacemi.  Klientské knihovny jsou k dispozici na různých platformách (Windows, Android, iOS) pomocí různýchC#jazyků (, Java, JavaScript, ObjectiveC).

| Případy použití | [Rozhraní REST API](GetStarted/GetStartedREST.md) | [Klientské knihovny](GetStarted/GetStartedClientLibraries.md) |
|-----|-----|-----|
| Převeďte krátký mluvený zvuk, například příkazy (velikost zvuku < 15 s) bez dočasných výsledků. | Ano | Ano |
| Převod dlouhého zvuku (> 15 s) | Ne | Ano |
| Streamování zvuku s požadovanými průběžnými výsledky | Ne | Ano |
| Pochopení textu převedeného ze zvuku pomocí LUIS | Ne | Ano |

 Pokud váš jazyk nebo platforma ještě nemá sadu SDK, můžete vytvořit vlastní implementaci založenou na [dokumentaci k protokolu](API-Reference-REST/websocketprotocol.md).

## <a name="recognition-modes"></a>Režimy rozpoznávání

Existují tři režimy rozpoznávání: `interactive`, `conversation` `dictation`a. Režim rozpoznávání upraví rozpoznávání řeči na základě toho, jak se uživatelé budou muset mluvit. Vyberte pro svou aplikaci vhodný režim rozpoznávání.

> [!NOTE]
> Režimy rozpoznávání můžou mít v protokolu REST jiné chování než v protokolu WebSocket. REST API například nepodporuje nepřetržité rozpoznávání, a to ani v režimu konverzace nebo diktování.
> [!NOTE]
> Tyto režimy se použijí, pokud přímo používáte protokol REST nebo WebSocket. [Klientské knihovny](GetStarted/GetStartedClientLibraries.md) používají jiné parametry pro určení režimu rozpoznávání. Další informace najdete v klientské knihovně podle vašeho výběru.

Služba Microsoft Speech vrátí pro všechny režimy rozpoznávání pouze jeden výsledek fráze pro rozpoznávání. Pro všechny jednotlivé utterance se omezuje 15 sekund.

### <a name="interactive-mode"></a>Interaktivní režim

V `interactive` režimu provádí uživatel krátké požadavky a očekává, že aplikace provede akci v reakci.

Následující vlastnosti jsou typické pro aplikace v interaktivním režimu:

- Uživatelé ví, že mluví na počítači, a ne na jiný člověk.
- Uživatelé aplikace znají čas, co chtějí, na základě toho, co chtějí aplikace dělat.
- Projevy obvykle poslední asi 2-3 sekund.

### <a name="conversation-mode"></a>Režim konverzace

V `conversation` režimu se uživatelé účastní konverzace mezi lidmi.

Následující vlastnosti jsou typické pro aplikace v režimu konverzace:

- Uživatelé ví, že mluví s jinou osobou.
- Rozpoznávání řeči vylepšuje konverzace pro člověka tím, že umožňuje jednomu nebo oběma účastníkům zobrazit mluvený text.
- Uživatelé neplánují vždycky, co chtějí vyslovit.
- Uživatelé často používají slangem a další neformální řeč.

### <a name="dictation-mode"></a>Režim diktování

V `dictation` režimu uživatelé přebírají po delší dobu projevy do aplikace pro další zpracování.

Následující vlastnosti jsou typické pro aplikace v režimu diktování:

- Uživatelé ví, že mluví k počítači.
- Uživatelům se zobrazí výsledky textu rozpoznávání řeči.
- Uživatelé často plánují, co chtějí a používají další formální jazyk.
- Uživatelé používají úplné věty s poslední 5-8 sekundami.

> [!NOTE]
> V režimech diktování a konverzace služba Microsoft Speech nevrací částečné výsledky. Místo toho vrátí služba výsledky stabilní fráze po hranicích ticha ve zvukovém streamu. Společnost Microsoft může vylepšit protokol řeči pro zlepšení uživatelského prostředí v těchto režimech nepřetržitého rozpoznávání.

## <a name="recognition-languages"></a>Jazyky rozpoznávání

*Jazyk rozpoznávání* určuje jazyk, ve kterém uživatel aplikace mluví. Zadejte *jazyk rozpoznávání* s parametrem dotazu adresa URL *jazyka* v připojení. Hodnota parametru dotazu *jazyka* používá značku jazyka IETF [BCP 47](https://en.wikipedia.org/wiki/IETF_language_tag)a **musí** být jedním z jazyků podporovaných rozhraním API pro rozpoznávání řeči. Úplný seznam jazyků podporovaných službou Speech Service najdete na stránce [podporované jazyky](API-Reference-REST/supportedlanguages.md).

Služba Microsoft Speech Service odmítne neplatné požadavky na připojení zobrazením `HTTP 400 Bad Request` odpovědi. Neplatná žádost je jedna z těchto akcí:

- Nezahrnuje hodnotu parametru dotazu *jazyka* .
- Obsahuje parametr dotazu *jazyka* , který je nesprávně naformátován.
- Obsahuje parametr dotazu *jazyka* , který není jedním z jazyků podpory.

Můžete se rozhodnout sestavit aplikaci, která podporuje jeden nebo všechny jazyky, které služba podporuje.

### <a name="example"></a>Příklad

V následujícím příkladu aplikace používá režim rozpoznávání řeči v *konverzaci* pro anglickou verzi mluvčího.

```HTTP
https://speech.platform.bing.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

## <a name="transcription-responses"></a>Přepisující odpovědi

Přepisující odpovědi vrátí převedený text ze zvuk do klientů. Odpověď přepisu obsahuje následující pole:

- `RecognitionStatus`Určuje stav rozpoznávání. Možné hodnoty jsou uvedeny v následující tabulce.

| Stav | Popis |
| ------------- | ---------------- |
| Úspěch | Rozpoznávání bylo úspěšné a pole zobrazenýtext je k dispozici. |
| Neshoda | V zvukový datový proud byl zjištěn řeči, ale žádná slova v cílovém jazyce se shoda našla. Další podrobnosti najdete v článku [stav rozpoznávání neshody (#nomatch-Recognition-status).  |
| InitialSilenceTimeout | Začátek zvukového datového proudu obsahoval pouze tiché a při čekání na řeč vypršel časový limit služby. |
| BabbleTimeout | Začátek zvukového streamu obsahoval jenom šum a služba vypršel časový limit při čekání na řeč. |
| Chyba | Služba pro rozpoznávání zjistila vnitřní chybu a nemůže pokračovat. |

- `DisplayText`představuje rozpoznanou frázi, která je použita pro normalizaci, interpunkci a inverzní text – byly aplikovány a vulgární znaky byly maskovány hvězdičkami. Pole zobrazenýtext je k dispozici *pouze* v `RecognitionStatus` případě, že pole `Success`má hodnotu.

- `Offset`Určuje posun (v jednotkách 100 – nanosekund), na kterých byla fráze rozpoznaná, vzhledem k začátku zvukového datového proudu.

- `Duration`Určuje dobu trvání (v jednotkách 100 – nanosekund) této fráze řeči.

V případě potřeby vrátí odpověď přepisu více informací. Podrobnosti o tom, jak vracet podrobnější výstupy, najdete v tématu [výstupní formát](#output-format) .

Služba Microsoft Speech Service podporuje další přepisný proces, který zahrnuje přidávání velkých a malých písmen, vkládání vulgárních výrazů a normalizaci textu do běžných forem. Pokud uživatel například vystupuje frázi, která se připomínat k nákupu šesti iPhone, služba Microsoft Speech Services vrátí přepisu text s upozorněním na nákup 6 iPhone. Proces, který převede slovo "6" na číslo "6", se nazývá *normalizace textu s inverzními texty* (*vytvořené* pro krátké).

### <a name="nomatch-recognition-status"></a>Nerozlišovat stav rozpoznávání

Odpověď přepisu se `NoMatch` vrátí `RecognitionStatus` v případě, že služba Microsoft Speech detekuje rozpoznávání řeči ve zvukovém datovém proudu, ale není schopna porovnat tento hlas s jazykovou gramatikou použitou pro danou žádost. Například podmínka *neshody* může nastat, pokud uživatel uvede něco v němčině, když nástroj pro rozpoznávání očekává angličtinu jako mluvený jazyk. Vzorek signálu utterance by znamenal přítomnost lidského řeči, ale žádné z mluvených slov by se neshodovalo s anglickým lexikonem používaným nástrojem pro rozpoznávání.

Pokud algoritmus rozpoznávání nedokáže najít přesnou shodu zvuků obsažených ve zvukovém datovém proudu, dojde k dalšímu stavu *neshody* . Pokud k tomuto stavu dojde, může služba Microsoft Speech Service vytvořit *hlasové zprávy. hypotézy* , které obsahují *hypotetické text* , ale vytvoří zprávu *Speech. phrase* , na které se *RecognitionStatus* *neshoduje.* . Tato podmínka je normální. nemusíte dělat žádné předpoklady týkající se přesnosti nebo věrnosti textu ve zprávě *Speech. hypotéz* . Kromě toho nesmíte předpokládat, že protože služba Microsoft Speech vytvoří zprávy *Speech. hypotéz* , které služba dokáže vytvořit *řeč.* zpráva s *RecognitionStatus* *úspěchem*.

## <a name="output-format"></a>Výstupní formát

Služba Microsoft Speech Service může vracet nejrůznější formáty datových částí v rámci odpovědí v přepisu. Všechny datové části jsou struktury JSON.

Můžete řídit formát výsledku fráze zadáním `format` parametru dotazu URL. Ve výchozím nastavení vrátí `simple` služba výsledky.

| Formát | Popis |
|-----|-----|
| `simple` | Zjednodušený výsledek fráze obsahující stav rozpoznávání a rozpoznaný text ve formě zobrazení |
| `detailed` | Stav rozpoznávání a N-nejlepších výsledků fráze, kde každý výsledek fráze obsahuje všechny čtyři formuláře pro rozpoznávání a hodnocení spolehlivosti. |

`Offset` `duration` [](#n-best-values) `RecognitionStatus`Formát obsahuje N-nejlepších hodnot kromě, a v odpovědi. `detailed`

### <a name="n-best-values"></a>N-nejlepší hodnoty

Naslouchací procesy, ať už jde o člověka nebo počítač, nikdy nemusejí mít jistotu, že se jim *přesně* dozvěděly, co se hovoří. Naslouchací proces může přiřadit *pravděpodobnost* pouze konkrétnímu výkladu utterance.

V běžných podmínkách platí, že při komunikaci s dalšími uživateli, se kterými často pracují, mají lidé vysokou pravděpodobnost rozpoznat slova, která byla mluvené řeči. Procesy rozpoznávání řeči založené na počítačích se snaží dosáhnout podobných úrovní přesnosti a za správných podmínek [dosahují parity pomocí člověka](https://blogs.microsoft.com/next/2016/10/18/historic-achievement-microsoft-researchers-reach-human-parity-conversational-speech-recognition/#sm.001ykosqs14zte8qyxj2k9o28oz5v).

Algoritmy používané v rozpoznávání řeči prozkoumat alternativní interpretace utterance jako součást normálního zpracování. Obvykle se tyto alternativy zahodí, protože legitimace, která je ve prospěch jediného výkladu, se stává zahlcením. V méně než optimálních podmínkách se ale nástroj pro rozpoznávání řeči dokončí se seznamem alternativních možných interpretací. Horních *n* alternativních možností v tomto seznamu se nazývají *N-nejlepší seznam*. Každé Alternative je přiřazeno [skóre spolehlivosti](#confidence). Výsledky spolehlivosti jsou v rozsahu od 0 do 1. Skóre 1 představuje nejvyšší úroveň důvěry. Skóre 0 představuje nejnižší úroveň důvěry.

> [!NOTE]
> Počet položek v seznamu N-nejlepších se liší v několika projevy. Počet položek se může v různých rozlišeních *stejného* utterance lišit. Tato variace je přirozeným a očekávaným výsledkem pravděpodobnostní povahy algoritmu pro rozpoznávání řeči.

Každá položka vrácená v seznamu N-nejlepších obsahuje

- `Confidence`, který představuje [skóre spolehlivosti](#confidence) této položky.
- `Lexical`, což je [lexikální forma](#lexical-form) rozpoznaného textu.
- `ITN`, což je [vytvořené forma](#itn-form) rozpoznaného textu.
- `MaskedITN`, což je [MASKOVANÝ vytvořené tvar](#masked-itn-form) rozpoznaného textu.
- `Display`, což je [formulář pro zobrazení](#display-form) rozpoznaného textu.

### Hodnocení spolehlivosti<a id="confidence"></a>

Hodnocení spolehlivosti je nedílnou součástí systémů rozpoznávání řeči. Služba Microsoft Speech získává hodnocení spolehlivosti od *klasifikátoru spolehlivosti*. Microsoft napomáhá klasifikátoru spolehlivosti pro sadu funkcí, které jsou určené k maximálnímu rozlišení mezi správným a nesprávným rozpoznáváním. Hodnocení spolehlivosti se vyhodnocuje pro jednotlivá slova a celá projevya.

Pokud se rozhodnete použít hodnocení spolehlivosti, která jsou vrácena službou, pamatujte na následující chování:

- Hodnocení spolehlivosti lze porovnat pouze v rámci stejného režimu a jazyka rozpoznávání. Neporovnávání výsledků mezi různými jazyky nebo různými režimy rozpoznávání. Například hodnocení spolehlivosti v interaktivním režimu *rozpoznávání nemá korelaci k* skóre spolehlivosti v režimu diktování.
- Hodnocení spolehlivosti se nejlépe využije u omezené sady projevy. V hodnoceních pro velkou sadu projevy je přirozeně vynikající stupeň proměnlivosti.

Pokud se rozhodnete použít hodnotu skóre spolehlivosti jako *prahovou* hodnotu, na které vaše aplikace funguje, vytvořte prahové hodnoty pomocí rozpoznávání řeči.

- Provede rozpoznávání řeči na reprezentativní vzorek projevy pro vaši aplikaci.
- Shromážděte hodnocení spolehlivosti pro každé rozpoznávání v sadě ukázek.
- Základ prahové hodnoty pro určitý percentil v určité ukázce.

Žádná jedna prahová hodnota není vhodná pro všechny aplikace. Přijatelné hodnocení spolehlivosti pro jednu aplikaci může být pro jinou aplikaci nepřijatelné.

### <a name="lexical-form"></a>lexikální formulář

Lexikální formulář je rozpoznaný text, přesně jak k němu došlo v utterance a bez interpunkce nebo kapitalizace. Například lexikální tvar adresy "1020 Enterprise Way" by byl *10 20 podnikového způsobu*za předpokladu, že byl tímto způsobem vymluvené. Lexikální forma věty "připomenout mě k nákupu 5 tužky" *vám umožní koupit si pět tužk*.

Lexikální formulář je nejvhodnější pro aplikace, které potřebují provádět normalizaci nestandardních textů. Lexikální formulář je také vhodný pro aplikace, které potřebují nezpracované výrazy rozpoznávání.

Vulgární výrazy nejsou nikdy maskovány v lexikálním formuláři.

### <a name="itn-form"></a>Formulář vytvořené

Normalizace textu je proces převodu textu z jednoho formuláře do jiného kanonického tvaru. Například telefonní číslo "555-1212" může být převedeno na kanonický tvar *5 5 5 1 2 1 2*. Normalizace *inverzního* textu (vytvořené) obrátí tento proces a převede slova "5 5 5 1 2 1 2" na obrácený kanonický tvar *555-1212*. VYTVOŘENÉ forma výsledku rozpoznávání nezahrnuje velká a malá písmena.

Formulář vytvořené je nejvhodnější pro aplikace, které fungují na rozpoznaném textu. Například aplikace, která umožňuje uživateli mluvit se vyhledávanými výrazy a pak tyto podmínky používá ve webovém dotazu, by používala formu vytvořené. Vulgární výrazy nejsou ve formuláři vytvořené nikdy maskovány. K maskování vulgárních výrazů použijte *vytvořené formulář s maskou*.

### <a name="masked-itn-form"></a>Maskovaný vytvořené formulář

Vzhledem k tomu, že vulgární fráze je přirozeně součástí mluveného jazyka, služba Microsoft Speech při jejich mluveném rozpoznání tato slova a fráze rozpozná. Vulgární výrazy ale nemusí být vhodné pro všechny aplikace, zejména pro aplikace s omezenou, nedospělý cílovou skupinou uživatelů.

VYTVOŘENÉový formulář používá maskování vulgárních výrazů u formy normalizace inverzního textu. Chcete-li maskovat vulgární výrazy, nastavte hodnotu parametru vulgárních výrazů na `masked`. Při maskování vulgárních výrazů se slova rozpoznaná jako součást slovníku vulgárního jazyka nahrazují hvězdičkami. Například: *připomenout mi k nákupu 5* tužky. Maskovaný vytvořené tvar výsledku rozpoznávání nezahrnuje velká a malá písmena.

> [!NOTE]
> Pokud je hodnota parametru dotazu vulgárních výrazů nastavená `raw`na, je vytvořené tvar s maskou stejný jako formulář vytvořené. Vulgární výrazy *nejsou* maskovány.

### <a name="display-form"></a>Formulář pro zobrazení

Interpunkční znaménka a velká písmena, kde se má zdůraznit zdůraznění, kde se mají porušit a tak dále, což usnadňuje pochopení textu. Formulář pro zobrazení přidá interpunkci a kapitalizace do výsledků rozpoznávání, takže je nejvhodnějším formulářem pro aplikace, které zobrazují mluvený text.

Vzhledem k tomu, že formulář pro zobrazení rozšiřuje maskovaný formulář vytvořené, můžete nastavit hodnotu parametru vulgárních `masked` výrazů `raw`na nebo. Pokud je hodnota nastavena na `raw`, výsledky rozpoznávání budou zahrnovat jakékoli vulgární výrazy, které uživatel používá. Pokud je hodnota nastavená na `masked`, nahradí se slova rozpoznaná jako součást slovníku vulgárních výrazů v podobě hvězdiček.

### <a name="sample-responses"></a>Ukázkové odpovědi

Všechny datové části jsou struktury JSON.

Formát datové části výsledku `simple` fráze:

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

Formát datové části výsledku `detailed` fráze:

```json
{
  "RecognitionStatus": "Success",
  "Offset": "1236645672289",
  "Duration": "1236645672289",
  "NBest": [
      {
        "Confidence" : "0.87",
        "Lexical" : "remind me to buy five pencils",
        "ITN" : "remind me to buy 5 pencils",
        "MaskedITN" : "remind me to buy 5 pencils",
        "Display" : "Remind me to buy 5 pencils.",
      },
      {
        "Confidence" : "0.54",
        "Lexical" : "rewind me to buy five pencils",
        "ITN" : "rewind me to buy 5 pencils",
        "MaskedITN" : "rewind me to buy 5 pencils",
        "Display" : "Rewind me to buy 5 pencils.",
      }
  ]
}
```

## <a name="profanity-handling-in-speech-recognition"></a>Rozpoznávání řeči – zpracování vulgárních výrazů

Služba Microsoft Speech Service rozpoznává všechny formy lidského hlasu, včetně slov a frází, které by řada lidí mohla klasifikovat jako "vulgárních výrazů". Pomocí parametru dotazu *vulgárních výrazů* můžete řídit, jak služba zpracovává vulgární výrazy. Ve výchozím nastavení neexistují vulgární výrazy v *rozpoznávání řeči. výsledky fráze* a nevrací *Speech. hypotézové* zprávy, které obsahují vulgární výrazy.

| Hodnota *vulgárních výrazů* | Popis |
| - | - |
| `masked` | Vulgární výrazy masek hvězdičkami. Toto chování je výchozí. |
| `removed` | Odstraní vulgární výrazy ze všech výsledků. |
| `raw` | Rozpoznává a vrátí vulgární výrazy ve všech výsledcích. |

### <a name="profanity-value-masked"></a>Hodnota vulgárních výrazů`Masked`

Pro maskování vulgárních výrazů nastavte parametr dotazu *vulgárních výrazů* na hodnotu *maskovaná*. Pokud parametr dotazu *vulgárních výrazů* má tuto hodnotu nebo není pro požadavek zadán, je vulgárních výrazů *Maska* služby. Služba provádí maskování nahrazením vulgárních výrazů ve výsledcích rozpoznávání hvězdičkami. Když zadáváte práci s maskou, služba nevrací *Speech. hypotézové* zprávy, které obsahují vulgární výrazy.

### <a name="profanity-value-removed"></a>Hodnota vulgárních výrazů`Removed`

Pokud parametr dotazu na *vulgární* text má *odstraněnou*hodnotu, služba odstraňuje vulgární výrazy z *hlasu.* zprávy s *mluveným* slovem. Výsledky jsou stejné *, jako by slova vulgárních slov nebyla mluvené*.

#### <a name="profanity-only-utterances"></a>Projevy jenom pro vulgární výrazy

Uživatel může hovořit *jenom* o vulgárních událostech, když aplikace nakonfigurovala tuto službu, aby odstranila vulgární výrazy. V případě tohoto scénáře, pokud je režim rozpoznávání *Diktování* nebo *konverzace*, služba nevrátí *řeč. výsledek*. Pokud je režim rozpoznávání *interaktivní*, služba vrátí *řeč. výsledek* se stavovým kódem se *neshoduje*.

### <a name="profanity-value-raw"></a>Hodnota vulgárních výrazů`Raw`

Pokud parametr dotazu *vulgárních výrazů* má hodnotu *raw*, služba neodebere ani nemaskuje vulgární výrazy buď ve zprávách *Speech. frázi* , nebo *Speech. hypotéz* .
