---
title: Koncepty zpracování řeči Bingu | Dokumentace Microsoftu
titlesuffix: Azure Cognitive Services
description: Základní koncepty používané v Microsoft Speech Service.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ms.openlocfilehash: 6089f053472faaa7fa8c957904f73c7061cb2d3f
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2018
ms.locfileid: "49344921"
---
# <a name="basic-concepts"></a>Základní koncepty

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Tato stránka popisuje některé základní pojmy v Microsoft službu rozpoznávání řeči. Doporučujeme vám přečíst si téma na této stránce než začnete používat rozhraní API pro rozpoznávání řeči Microsoft ve vaší aplikaci.

## <a name="understanding-speech-recognition"></a>Principy rozpoznávání řeči

Pokud je to poprvé, vytváříte aplikaci podporou řeči nebo pokud je prvním přidáváte možnosti zpracování řeči do stávající aplikace, tato část vám pomůže začít pracovat. Pokud už máte nějaké zkušenosti s podporou hlasových aplikace, můžete se rozhodnout k popisům pouze této části nebo může přeskočit, úplně, pokud jste staré úplného zpracování řeči a chcete získat právo na podrobnosti protokolu.

### <a name="audio-streams"></a>Zvukové datové proudy

Přední mezi základními koncepty řeči je *zvukový datový proud*. Na rozdíl od stisknutí klávesy, která nastane v jednom okamžiku v čase a obsahuje informace o každé, žádost o mluvené slovo je rozdělena do stovek milisekund a obsahuje mnoho kilobajtů informace. Doba trvání mluvené slovo projevy představuje určité potíže vývojářům, kteří chtějí poskytovat jednoduchý a elegantní řeči pro svou aplikaci. Dnešní počítače a algoritmy provést před spuštěním akce určené k transkripci řeči na přibližně polovinu doby trvání utterance, tak 2sekundové utterance může být přepsána v přibližně 1 sekundu, ale všechny aplikace, ke které dojde 1 sekundu zpoždění při zpracování uživatele zjednodušené ani elegantní.

Naštěstí způsoby "skrytí" určené k transkripci času prováděním určené k transkripci v jedné části utterance zatímco uživatel to mluví jiné části. Například rozdělením utterance 1 sekundu do 10 bloků 100 milisekund a provedením určené k transkripci na každý blok zase více než 450 celkem 500 milisekund vyžadované pro přepis může být "hidden" tak, aby uživatel neví, určené k transkripci je provádí během učitelského to mluví. Při přemýšlet o tom, v tomto příkladu mějte na paměti, že služba provádí určené k transkripci na předchozí 100 milisekund zvuk zatímco uživatel to mluví další 100, proto když uživatel zastaví vzato službu pouze muset přepisy zhruba 100 milisekundy zvuk k vygenerování výsledku.

K dosažení této uživatelské prostředí, je mluvené slovo zvuku informace shromážděné v blocích a přepisována jako uživatel mluví. Tyto zvukové bloky souhrnně z *zvukový datový proud*, a proces odesílání tyto zvukové bloků dat ve službě se nazývá *zvukový datový proud.* Zvukový datový proud je důležitou součástí jakékoli aplikace s podporou řeči; velikost deduplikačního bloku dat ladění a optimalizaci implementace streamování jsou některé z jejich činnost nejvíc ohrožují způsoby komfort uživatelů vaší aplikace.

### <a name="microphones"></a>Mikrofony

Lidé proces mluvené pomocí jejich uší ale inanimate hardware používá mikrofon. Povolit rozpoznávání řeči do jakékoli aplikace, budete muset integraci s mikrofon poskytuje zvukový datový proud pro vaši aplikaci.

Rozhraní API pro mikrofonu musí umožňují spustit a zastavit příjem zvuku bajtů mikrofon. Musíte se rozhodnout, co bude akce uživatele aktivovat mikrofon zahájit naslouchání pro rozpoznávání řeči. Může zvolit, aby stisknutí tlačítka aktivovat začátku naslouchání, nebo můžete mít *klíčové slovo* nebo *probuzení slovo* spotter vždy naslouchání mikrofon a používají výstup tohoto modulu aktivační událost posílání zvuku ke službě Microsoft řeči.

### <a name="end-of-speech"></a>Konec řeči

Zjišťování *při* mluvčího má *zastavena* mluvený pro člověka zdá být dostatečně jednoduchá, ale spíše obtížné problému mimo laboratorních podmínek. Nestačí jednoduše hledat čistě nečinnosti po utterance, protože často dochází k mnoha hluk k zkomplikovat věci. Microsoft Speech Service provádí úlohu vynikající rychle zjistit, když uživatel zastavil, mluvit a služba může informovat aplikaci o této skutečnosti, ale toto uspořádání znamená, že vaše aplikace je poslední vědět, kdy uživatel přestat předčítat. Tato akce není vůbec stejně jako jiné formy vstup ve kterém je vaše aplikace *první* vědět, kdy se uživatelovo zadání spustí *a* skončí.

### <a name="asynchronous-service-responses"></a>Odpovědi na asynchronní služby

Skutečnost, že vaše aplikace potřebuje být informováni o dokončení uživatelského vstupu neurčuje žádné snížení výkonu nebo programovací vyřešit potíže se na svou aplikaci, ale vyžadují, pokud si myslíte o hlasové požadavky odlišně od vstupní požadavek / odpověď vzorce, se kterými jste se seznámili. Vzhledem k tomu, že vaše aplikace nebude vědět, když uživatel zastaví čtení, musí aplikace dál datového proudu zvuku ve službě při současně a asynchronně čeká na odpověď ze služby. Tento model je na rozdíl od dalších žádostí a odpovědí webových protokolů jako jsou HTTP. V těchto protokolech musíte dokončit žádost o před přijetím odpovědi; v protokolu Microsoft Speech Service obdržíte odpovědi *zatímco jsou stále vysílání datového proudu zvuku pro požadavek*.

> [!NOTE]
> Tato funkce není podporována při použití rozhraní HTTP REST API řeči.

### <a name="turns"></a>Zapne

Rozpoznávání řeči je dopravce informací. Když hovoříte, se snažíte sdělit informace, které jsou ve vašem vlastnictví někomu, kdo naslouchá tuto informaci. Při předávání informací obvykle trvá zapne mluvený a naslouchá. Podobně vaše aplikace s podporou řeči komunikuje s uživateli střídavě naslouchání a reakcí, i když vaše aplikace obvykle probíhá většina naslouchání. Mluvené slovo vstup uživatele a služby odpověď na tento vstup je volán *zapnout*. A *zapnout* spustí, když uživatel mluví a končí, když aplikace dokončí zpracování řeči odezvy služby.

### <a name="telemetry"></a>Telemetrická data

Vytváření podporou hlasových zařízení nebo aplikace může být náročné, dokonce i pro zkušené vývojáře. Protokoly založené na Stream často vypadá to, že na první pohled složitý a důležité podrobnosti, jako je detekce nečinnosti může být úplně nové. S tolika zprávy museli být úspěšně odeslané a přijaté dokončete dvojici jednu žádost odpověď, je *velmi* důležité shromažďovat úplné a přesné údaje o těchto zprávách. Protokol Microsoft Speech Service poskytuje pro kolekci tato data. Ujistěte se, veškeré úsilí, aby co nejpřesněji; zadejte požadovaná data zadáním úplných a přesných dat je bude možné pomáhá sami – byste někdy potřebovali pomoc od týmu služby řeči Microsoft při řešení potíží s vaší implementace klienta, kvalitu telemetrických dat, které jste shromáždili bude velmi důležité pro problém analýzy.

> [!NOTE]
> Tato funkce není podporována při použití rozhraní REST API pro rozpoznávání řeči.

### <a name="speech-application-states"></a>Stavy aplikace řeči

Postup, jak povolit hlasový vstup v aplikaci se trochu liší od pokynů pro jiné formy vstup například kliknutí myší nebo klepnutí prstem. Je nutné sledovat, jestli aplikace naslouchá na mikrofon a odesílání dat do služby řeči při čekání odpověď od služby a až bude ve stavu nečinnosti. Vztah mezi těmito stavy je znázorněn v následujícím diagramu.

![Diagram stavu aplikace řeči](Images/speech-application-state-diagram.png)

Protože služba Speech podílí na některé stavy, protokol služby definuje zprávy, které pomůžou vaše aplikace přechod mezi stavy. Vaše aplikace potřebuje jak interpretovat a zpracovat tyto zprávy protokolu pro sledování a správu stavů aplikace řeči.

## <a name="using-the-speech-recognition-service-from-your-apps"></a>Pomocí služby rozpoznávání řeči z vašich aplikací

Službu rozpoznávání řeči Microsoft poskytuje dva způsoby, jak vývojáři přidat rozpoznávání řeči do svých aplikací.

- [REST API](GetStarted/GetStartedREST.md): vývojáři mohou pomocí protokolu HTTP volání ze svých aplikací do služby pro rozpoznávání řeči.
- [Klientské knihovny](GetStarted/GetStartedClientLibraries.md): pro pokročilé funkce, vývojáři mohli stáhnout Microsoft Speech klientských knihoven a propojit do svých aplikací.  Klientské knihovny jsou dostupné na různých platformách (Windows, Android, iOS) používající různé jazyky (C#, Java, JavaScript, ObjectiveC).

| Případy použití | [Rozhraní REST API](GetStarted/GetStartedREST.md) | [Klientské knihovny](GetStarted/GetStartedClientLibraries.md) |
|-----|-----|-----|
| Převést krátké mluvené, například příkazy (s zvuku délka < 15) bez prozatímní výsledky | Ano | Ano |
| Převod dlouhé zvuku (> 15 s) | Ne | Ano |
| Zvukový Stream s prozatímní požadované výsledky | Ne | Ano |
| Vysvětlení text převést z zvuk pomocí služby LUIS | Ne | Ano |

 Pokud váš jazyk nebo platformu ještě nemá žádné sady SDK, můžete vytvořit vlastní implementaci na základě [protokol dokumentaci](API-Reference-REST/websocketprotocol.md).

## <a name="recognition-modes"></a>Rozpoznávání režimy

Existují tři režimy rozpoznávání: `interactive`, `conversation`, a `dictation`. Režim rozpoznávání upraví na základě toho, jak se uživatelé mohou mluvit rozpoznávání řeči. Zvolte odpovídající rozpoznávání režim pro vaši aplikaci.

> [!NOTE]
> Rozpoznávání režimy může mít různé chování [protokolu REST](#rest-speech-recognition-api) než [protokol WebSocket](#webSocket-speech-recognition-api). Například rozhraní REST API nepodporuje průběžné rozpoznávání i v režimu takovou konverzaci či diktování.
> [!NOTE]
> Tyto režimy platí při přímo použít protokol REST nebo pomocí protokolu WebSocket. [Klientské knihovny](GetStarted/GetStartedClientLibraries.md) použít jiné parametry pro určení režimu rozpoznávání. Další informace najdete v klientské knihovně podle vašeho výběru.

Microsoft Speech Service vrátí pouze jeden výsledek rozpoznání fráze ve všech režimech rozpoznávání. Platí limit 15 sekund pro jakékoli jedné utterance.

### <a name="interactive-mode"></a>Interaktivní režim

V `interactive` režimu, uživatel provede krátký požadavků a očekává, že aplikace provádět akce v reakci.

Následující vlastnosti jsou běžně aplikací interaktivní režim:

- Uživatelé věděli, že jsou mluvený k počítači a ne do jiného lidské.
- Uživatelům aplikace vědět předem chce říct, založené na to, co chtějí aplikace provést.
- Projevy obvykle o poslední 2-3 sekundy.

### <a name="conversation-mode"></a>Režim konverzace

V `conversation` režim, uživatelé se zabývají lidských lidských konverzace.

Následující vlastnosti jsou typické pro aplikace v režimu konverzace:

- Uživatelé vědí, jsou někým jiným osobně.
- Rozpoznávání řeči rozšiřuje tím, že jedno nebo obě účastníci mluvené slovo text zobrazí lidské konverzace.
- Uživatelé vždy nezamýšlíte chce říct.
- Uživatelé často používají slang a dalších neformální řeči.

### <a name="dictation-mode"></a>Diktování

V `dictation` režim, uživatelé vyslovení projevy delší do aplikace pro další zpracování.

Následující vlastnosti jsou běžně diktování režim aplikací:

- Uživatelé vědí, že jsou kdekoliv na počítač.
- Uživatelům se zobrazí textové výsledky rozpoznávání řeči.
- Uživatelé často plánování, co chtějí Řekněme, že a použít formálnější jazyk.
- Uživatelé využívají úplné věty, posledních 5 až 8 sekund.

> [!NOTE]
> V režimech diktování a konverzace Microsoft Speech Service vracet částečné výsledky. Místo toho službu vrátí výsledky stabilní frázi po nečinnosti hranice v zvukový datový proud. Microsoft může vylepšovat protokolu řeči vylepšit uživatelské prostředí v těchto režimech průběžné rozpoznávání.

## <a name="recognition-languages"></a>Rozpoznávání jazyků

*Rozpoznávání jazyka* Určuje jazyk, který přednáší uživatel vaší aplikace. Zadejte *rozpoznávání jazyka* s *jazyk* parametr dotazu adresy URL pro připojení. Hodnota *jazyk* dotazu parametr používá značky jazyka IETF [BCP 47](https://en.wikipedia.org/wiki/IETF_language_tag), a **musí** být jeden z těchto jazyků, které podporují rozhraní API pro rozpoznávání řeči. Úplný seznam jazyků podporovaných službou Speech najdete na stránce [podporované jazyky](API-Reference-REST/supportedlanguages.md).

Microsoft Speech Service odmítá požadavky neplatné připojení zobrazením `HTTP 400 Bad Request` odpovědi. Neplatný požadavek je jeden, který:

- Nezahrnuje *jazyk* hodnotu parametru.
- Zahrnuje *jazyk* parametr, který je nesprávně naformátováno dotazu.
- Zahrnuje *jazyk* parametr, který není jeden z těchto jazyků podporu dotazu.

Můžete vytvářet aplikace, která podporuje jednu nebo všechny jazyky, které jsou podporovány službou.

### <a name="example"></a>Příklad:

V následujícím příkladu se aplikace používá *konverzace* režimu rozpoznávání řeči pro anglickou mluvčího USA.

```HTTP
https://speech.platform.bing.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

## <a name="transcription-responses"></a>Přepis odpovědi

Odpovědi na určené k transkripci vrátí text převedený od zvuk klientům. Přepis odpovědí obsahuje následující pole:

- `RecognitionStatus` Určuje stav uznání. Možné hodnoty jsou uvedeny v následující tabulce.

| Status | Popis |
| ------------- | ---------------- |
| Úspěch | Rozpoznávání byla úspěšná a ZobrazenýText pole je k dispozici |
| NoMatch | V zvukový datový proud byl zjištěn řeči, ale žádná slova v cílovém jazyce se shoda našla. [Status(#nomatch-recognition-status) rozpoznávání NoMatch součásti pro další podrobnosti naleznete v tématu  |
| InitialSilenceTimeout | Začátek zvukový datový proud obsahovala pouze nečinnosti a službu vypršel časový limit čekání na řeč |
| BabbleTimeout | Začátek zvukový datový proud obsažené pouze šum a službu vypršel časový limit čekání na řeč |
| Chyba | Rozpoznávání služby došlo k interní chybě a nemůže pokračovat |

- `DisplayText` představuje rozpoznanou frázi poté, co se použily malá a velká písmena, interpunkce a inverzní. text normalizace a vulgárních výrazů má byla maskována hvězdičkami. Je k dispozici pole ZobrazenýText *pouze* Pokud `RecognitionStatus` pole má hodnotu `Success`.

- `Offset` Určuje posun (v jednotkách 100 nanosekund), ve kterém byl rozpoznán frázi, vzhledem k začátku zvukový datový proud.

- `Duration`Určuje dobu (v jednotkách 100 nanosekund) Tato fráze řeči.

V případě potřeby, vrátí odpověď určené k transkripci Další informace. Zobrazit [výstupní formát](#output-format) jak vrátit podrobnější výstupy.

Microsoft Speech Service podporuje další určené k transkripci proces, který zahrnuje přidání malá a velká písmena a interpunkční znaménka, maskování vulgárních výrazů a normalizace text, který se běžné formuláře. Například pokud uživatel mluví slovní spojení reprezentována slova "připomenout si chcete koupit šest Iphony", hlasových služeb od Microsoftu se vrátí přepisována text "Připomenout si chcete koupit 6 Iphony." Proces, který převádí slovo "šest" číslo "6" se nazývá *inverzní normalizace Text* (*není* zkráceně).

### <a name="nomatch-recognition-status"></a>Stav NoMatch rozpoznávání

Vrátí odpověď určené k transkripci `NoMatch` v `RecognitionStatus` při Microsoft Speech Service zjistí řeči v zvukový datový proud, ale není schopen odpovídat tento převod řeči na jazyk gramatiky se používají pro žádost. Například *NoMatch* situaci může dojít, pokud uživatel řekne něco v němčině, když rozlišovač očekává Americkou angličtinu jako mluvený jazyk. Vzor zvukového průběhu utterance by být indikována přítomnost lidské řeči, ale žádná slova, kterým se mluví by neodpovídá anglické lexikonu USA používá nástroj pro rozpoznávání.

Jiné *NoMatch* podmínka nastane, pokud je algoritmus rozpoznávání nebyla nalezena přesná shoda pro zvuky součástí zvukový datový proud. Pokud dojde k tomuto stavu může vést Microsoft Speech Service *speech.hypothesis* zprávy, které obsahují *hodnoty text* vytvoří, ale *speech.phrase*zprávu, ve kterém *RecognitionStatus* je *NoMatch*. Tato podmínka je normální; provedete nesmí nevyvozujte předpoklady o přesnost nebo věrnost textu *speech.hypothesis* zprávy. Kromě toho můžete nesmí se předpokládá, že vzhledem k tomu, že Microsoft Speech Service vytvoří *speech.hypothesis* zprávy, které služba je schopná vytvořit *speech.phrase* zprávy s  *RecognitionStatus* *úspěch*.

## <a name="output-format"></a>Výstupní formát

Microsoft Speech Service může vrátit různé formáty datových částí v určené k transkripci odpovědi. Struktury JSON jsou všechny datové části.

Výsledný formát frázi můžete řídit tak, že zadáte `format` parametr dotazu adresy URL. Ve výchozím nastavení, tato služba vrátí `simple` výsledky.

| Formát | Popis |
|-----|-----|
| `simple` | Výsledkem zjednodušené frázi obsahující stav rozpoznávání a rozpoznaný text v zobrazení formuláře. |
| `detailed` | Stav rozpoznávání a N-best seznam výsledků frázi, kde každou frázi výsledek obsahuje všechny čtyři způsoby rozpoznávání a skóre spolehlivosti. |

`detailed` Formát obsahuje [hodnoty N-best](#n-best-values), kromě `RecognitionStatus`, `Offset`, a `duration`, v odpovědi.

### <a name="n-best-values"></a>N-best hodnoty

Naslouchacích procesů, zda překladatelem nebo strojově, mohl nikdy být jistý, že jsou vyslyšeni *přesně* co je slyšet. Můžete přiřadit naslouchací proces *pravděpodobnost* jenom na konkrétní výklad utterance. 

Za běžných podmínek, když mluvíme ostatním uživatelům se kterými často interagují mají uživatelé vysoká pravděpodobnost rozpoznávání slova, která se používá. Naslouchacích procesů založených na počítačích řeči snažit dosáhnout podobné úrovně přesnosti a správné podmínek [jejich dosažení parity s člověka](https://blogs.microsoft.com/next/2016/10/18/historic-achievement-microsoft-researchers-reach-human-parity-conversational-speech-recognition/#sm.001ykosqs14zte8qyxj2k9o28oz5v).

Algoritmy, které se používají v rozpoznávání řeči prozkoumat alternativní interpretace utterance při normálním zpracování. Obvykle tyto možnosti se zahodí, jak se stává legitimaci a místo toho použití jedné interpretace náročný. V méně než optimální podmínky ale modulu pro rozpoznávání řeči skončí se seznam možných alternativních interpretace. Horní *N* alternativy v tomto seznamu se nazývají *seznamu N-best*. Každý alternativu je přiřazen [skóre spolehlivosti](#confidence). Spolehlivosti skóre rozsahu od 0 do 1. Skóre 1 představuje nejvyšší úroveň spolehlivosti. Skóre 0 představuje nejnižší úroveň spolehlivosti.

> [!NOTE]
> Počet položek v seznamu N-best lišit napříč více projevy. Počet položek, které se může lišit napříč více rozpoznávání z *stejné* utterance. Tato varianta je fyzická a očekávaný výsledek pravděpodobnostní povahy algoritmus rozpoznávání řeči.

Obsahuje každou položku v seznamu N-best

- `Confidence`, která představuje [skóre spolehlivosti](#confidence) této položky.
- `Lexical`, což je [lexikální formuláře](#lexical-form) rozpoznaný textu.
- `ITN`, což je [není formuláře](#itn-form) rozpoznaný textu.
- `MaskedITN`, což je [maskované není formuláře](#masked-itn-form) rozpoznaný textu.
- `Display`, což je [formulář pro zobrazení](#display-form) rozpoznaný textu.

### Skóre spolehlivosti <a id="confidence"></a>

Skóre spolehlivosti jsou nedílnou součástí systémů rozpoznávání řeči. Microsoft Speech Service obdrží hodnocení spolehlivosti od *spolehlivosti třídění*. Microsoft trénovat třídění důvěru v rámci sady funkcí, které jsou určeny k lze uchovávat rozlišit správné a nesprávné rozpoznávání. Skóre spolehlivosti jsou vyhodnocovány pro jednotlivá slova a celý projevy.

Pokud se rozhodnete použít skóre spolehlivosti, které jsou vrácené službou, mějte na paměti následující chování:

- Skóre spolehlivosti lze porovnat pouze v rámci stejné režim rozpoznávání a jazyk. Není výsledkem porovnání skóre mezi různé jazyky nebo rozpoznávání různé režimy. Skóre spolehlivosti v režimu interaktivního rozpoznávání má například *žádné* spojitost s míněním skóre spolehlivosti v režimu diktování.
- Skóre spolehlivosti jsou nejvhodnější na omezenou sadu projevy. Je přirozeně vysoký stupeň proměnlivé skóre pro velké sady projevy.

Pokud se rozhodnete použít hodnotu skóre spolehlivosti jako *prahová hodnota* na který vaše aplikace funguje, používat k navázání prahové hodnoty rozpoznávání řeči.

- Spusťte rozpoznávání řeči na reprezentativní vzorek projevy pro vaši aplikaci.
- Shromážděte skóre spolehlivosti pro každý rozpoznávání v ukázkové sadě.
- Základní prahovou hodnotu na některé percentilu výrazné zvýšení sebedůvěry pro tuto ukázku.

Žádná hodnota jednoduchá prahová hodnota není vhodná pro všechny aplikace. Přijatelné jistoty pro jednu aplikaci může nepřijatelné pro jinou aplikaci.

### <a name="lexical-form"></a>Lexikální formuláře

Lexikální formuláře je rozpoznaný text, přesně jak k němu utterance a bez interpunkce nebo malá a velká písmena. Například by být lexikální formulář na adresu "1020 Enterprise způsob" *deset dvacet enterprise způsob*, za předpokladu, že to byla používaný tímto způsobem. Lexikální formu větu "připomenout si chcete koupit 5 tužky" je *připomenout si chcete koupit pět tužky*.

Lexikální formuláře je nejvhodnější pro aplikace, které potřebují k provedení normalizace nestandardní text. Lexikální formulář je také vhodné pro aplikace, které potřebují nezpracované rozpoznávání slova.

Ve formuláři lexikální nikdy maskovaná vulgárních výrazů.

### <a name="itn-form"></a>NENÍ formuláře

Normalizace text je proces převodu textu z jednoho formuláře do jiného "canonical" formuláře. Například telefonní číslo "555-1212" může být převedena na kanonický tvar *pět pěti pět jeden dvě jeden dvě*. *Inverzní* normalizace text (není) vrátí tento proces převodu slova "pět pěti pět jeden dvě jeden dvě" pro obrácenou kanonický tvar *555-1212*. NENÍ formu výsledek rozpoznání nezahrnuje malá a velká písmena nebo interpunkční znaménka.

Formulář není je nejvhodnější pro aplikace, které působí na rozpoznaný text. Například aplikace, která umožňuje uživateli mluvit hledané termíny a pak používá tyto podmínky v dotazu webové by formulář není. Ve formuláři není nikdy maskovaná vulgárních výrazů. Maskovat vulgárních výrazů, použijte *maskované není formuláře*.

### <a name="masked-itn-form"></a>Maskované není formuláře

Protože vulgárních výrazů je přirozeně součástí mluvený jazyk, Microsoft Speech Service rozpozná taková slova a slovní spojení, se kterým se mluví. Vulgárních výrazů může však být vhodný pro všechny aplikace, zejména aplikací s cílovou skupinou uživatelů s omezeným přístupem, dospělá osoba.

Maskované formulář není platí vulgárních výrazů maskování na formulář normalizace inverzní textu. Maskovat vulgárních výrazů, nastavte hodnotu vlastnosti hodnotu parametru vulgárních výrazů `masked`. Když maskované vulgární slova, které jsou rozpoznány jako součást slovníku vulgárních výrazů jazyce jsou nahrazeny hvězdičkami. Příklad: *připomenout si chcete koupit 5 *** tužky*. Maskované není formu výsledek rozpoznání nezahrnuje malá a velká písmena nebo interpunkční znaménka.

> [!NOTE]
> Pokud hodnota parametru dotazu vulgárních výrazů nastavená na `raw`, maskované není formuláře je stejný jako formulář není. Vulgárních výrazů je *není* zakryté hvězdičkami.

### <a name="display-form"></a>Formulář pro zobrazení

Interpunkce a malá a velká písmena signál, kam chcete umístit důraz, pokud chcete pozastavit a tak dále, což usnadňuje pochopení textu. Formulář pro zobrazení přidá do výsledky rozpoznávání, takže formuláři nejvhodnější pro aplikace, které se zobrazí text mluvené slovo interpunkce a malá a velká písmena.

Protože formulář pro zobrazení rozšiřuje maskované formulář není, můžete nastavit hodnotu parametru vulgárních výrazů `masked` nebo `raw`. Pokud je hodnota nastavena na `raw`, rozpoznávání výsledky budou zahrnovat všechny vulgárních výrazů používaný tímto uživatelem. Pokud je hodnota nastavena na `masked`, slova rozpoznán jako část slovníku vulgárních výrazů jazyce jsou nahrazeny hvězdičkami.

### <a name="sample-responses"></a>Ukázkové odpovědi

Struktury JSON jsou všechny datové části.

Formát datové části `simple` frázi výsledek:

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

Formát datové části `detailed` frázi výsledek:

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

## <a name="profanity-handling-in-speech-recognition"></a>Zpracování vulgárních výrazů v rozpoznávání řeči

Microsoft Speech Service rozpozná všechny formy lidské řeči, včetně slova a slovní spojení, které by mnoho lidí klasifikovat jako "neuctivosti." Můžete řídit, jak služba zpracovává pomocí vulgárních výrazů *vulgárních výrazů* parametr dotazu. Ve výchozím nastavení, služby zakrývá vulgárních výrazů v *speech.phrase* výsledky a nevrací *speech.hypothesis* zprávy, které obsahují vulgárních výrazů.

| *Vulgárních výrazů* hodnota | Popis |
| - | - |
| `masked` | Masky vulgárních výrazů hvězdičkami. Toto chování je výchozí nastavení. | 
| `removed` | Zruší všechny výsledky vulgárních výrazů. |
| `raw` | Rozpozná a vrátí vulgárních výrazů v všechny výsledky. |

### <a name="profanity-value-masked"></a>Hodnota vulgárních výrazů `Masked`

Maskovat vulgárních výrazů, nastavte *vulgárních výrazů* parametr na hodnotu dotazu *maskované*. Když *vulgárních výrazů* parametr dotazu má tuto hodnotu, nebo není zadaná pro žádost službu *masky* vulgárních výrazů. Služba provádí maskování nahrazením vulgárních výrazů v výsledky rozpoznávání hvězdičkami. Při zadávání vulgárních výrazů maskování zpracování služby nevrací *speech.hypothesis* zprávy, které obsahují vulgárních výrazů.

### <a name="profanity-value-removed"></a>Hodnota vulgárních výrazů `Removed`

Když *vulgárních výrazů* dotazu parametr má hodnotu *odebrat*, služba vulgárních výrazů odebere z obou *speech.phrase* a *speech.hypothesis* zprávy. Výsledky jsou stejné *jakoby nebyly slyšet vulgární slova*.

#### <a name="profanity-only-utterances"></a>Projevy pouze vulgárních výrazů

Uživatel může přečíst *pouze* vulgárních výrazů, když aplikace má nakonfigurovanou službu vulgárních výrazů. Pro tento scénář, pokud je režim rozpoznávání *diktování* nebo *konverzace*, nesmí vracet služby *speech.result*. Pokud je režim rozpoznávání *interaktivní*, služba vrátí hodnotu *speech.result* se stavovým kódem *NoMatch*. 

### <a name="profanity-value-raw"></a>Hodnota vulgárních výrazů `Raw`

Při *vulgárních výrazů* dotazu parametr má hodnotu *nezpracovaná*, služba neodebere ani nezablokuje maskování vulgárních výrazů v buď *speech.phrase* nebo  *Speech.hypothesis* zprávy.
