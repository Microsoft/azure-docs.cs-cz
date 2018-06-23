---
title: Koncepty | Microsoft Docs
description: Základní koncepty používané v Microsoft řeči Service.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/15/2017
ms.author: zhouwang
ms.openlocfilehash: bc23f4fb7dfc045a0f8cc87155c31875c4de8450
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342673"
---
# <a name="basic-concepts"></a>Základní koncepty

Tato stránka popisuje některé základní pojmy ve službě rozpoznávání řeči společnosti Microsoft. Doporučujeme vám umožní číst tuto stránku před použitím Microsoft rozpoznávání řeči rozhraní API ve vaší aplikaci.

## <a name="understanding-speech-recognition"></a>Principy rozpoznávání řeči

Pokud je to poprvé, kterou vytváříte, aplikace se rozpoznávání řeči, nebo pokud je prvním přidáváte řeči funkcí do existující aplikace, tato část vám pomůže začít pracovat. Pokud již máte nějaké zkušenosti s aplikacemi rozpoznávání řeči, můžete zvolit pouze zběžně v této části, nebo může přeskočit, zcela v případě, že jste straně starý na rozpoznávání řeči a chcete získat zprava podrobnosti protokolu.

### <a name="audio-streams"></a>Zvukové datové proudy

Je přední mezi základní koncepty řeči *zvukový datový proud*. Na rozdíl od stisknutí klávesy, která nastane v jediný bod v čase a obsahuje jediný informace, žádost o mluvené se šíří přes stovky milisekundách a obsahuje mnoho kilobajtů informací. Trvání mluvené utterances uvede některé problémy s vývojáři vyhledávání a poskytuje zjednodušenou a elegantní řeči prostředí pro svou aplikaci. Počítače a algoritmy dnešní provést před spuštěním akce řeči přepis v přibližně polovinu trvání utterance, tak utterance 2 sekundu může být přepsána v přibližně 1 sekundu, ale je jakékoli aplikace, která vyskytne 1 sekundu zpoždění při zpracování uživatele zjednodušená ani elegantní.

Naštěstí jsou způsoby "skrytí" čas přepis provedením přepis na jednu část utterance, když uživatel je hovořícího jiné části. Rozdělením utterance 1 sekundu do 10 bloků 100 milisekund a provedením přepis na každého bloku zase přes 450 celkový 500 milisekund požadované pro přepis může být "skrytá" tak, aby uživatel nebere v úvahu přepis je například během provádění, zatímco si je hovořícího. Při přemýšlení o tomto příkladu, mějte na paměti, že službu je provádění přepis na předchozí 100 milisekund zvuku, když uživatel je hovořícího další 100, takže když uživatel přestane platí, že služba pouze muset transcribe zhruba 100 zvuk k vytvoření výsledku v milisekundách.

K dosažení tuto činnost koncového uživatele, mluvené zvuk informace se shromažďují v bloky a přepisována jako mluví uživatele. Tyto zvuk bloky souhrnně z *zvukový datový proud*, a proces odesílání těchto zvuk bloky ke službě se nazývá *zvuk streamování.* Zvuk streamování, je důležitou součástí jakékoli aplikace, rozpoznávání řeči; velikost bloku optimalizace a optimalizace streamování implementace jsou některé z nejvíc zvládat způsobů, jak zlepšit činnost koncového uživatele vaší aplikace.

### <a name="microphones"></a>Mikrofon

Osoby zpracovat mluvené zvuk pomocí jejich uší, ale používá mikrofon inanimate hardwaru. Pokud chcete povolit řeči v jakékoli aplikaci, musíte integrovat mikrofon poskytování zvukový datový proud pro vaši aplikaci.

Rozhraní API pro mikrofon musí umožňují spustit a zastavit příjem zvuk bajtů z mikrofonu. Musíte se rozhodnout, co bude akce uživatelů aktivovat mikrofon zahájit naslouchání pro rozpoznávání řeči. Můžete mít stisknutí tlačítka aktivuje spuštění naslouchání, nebo můžete mít *klíčové slovo* nebo *probuzení word* spotter vždy naslouchání mikrofon a používat výstup tohoto modulu odesílání zvuk ke službě Microsoft řeči aktivační události.

### <a name="end-of-speech"></a>Konec řeči

Zjišťování *při* má lektorem *zastavena* hovořícího pro člověka zdá být dostatečně jednoduchá, ale spíš obtížné problém mimo laboratorních podmínek. Není dostatek jednoduše hledání čistý nečinnosti po utterance, protože je často spoustu vedlejším šumu a zkomplikovat věcí. Služba rozpoznávání řeči Microsoft nemá úlohu vynikající rychle detekci, když uživatel zastavil, platí, že služba může informovat vaše aplikace tuto skutečnost, ale toto uspořádání znamenat, že aplikace je poslední vědět, když uživatel zastavit čtení. Tato akce není vůbec jako jiné formy vstup kde vaše aplikace je *první* vědět, když uživatel spustí vstup *a* ukončí.

### <a name="asynchronous-service-responses"></a>Asynchronní služby odpovědí

Skutečnost, že aplikace musí být informováni o po dokončení uživatelský vstup není uložit všechny penalizacím výkonu nebo programovací problémy ve vaší aplikaci, ale nevyžaduje, pokud si myslíte o žádostech řeči jinak než vstupní požadavek / vzory odpovědi, s nimiž se seznámíte. Vzhledem k tomu, že vaše aplikace nebude vědět, když uživatel přestane hovořícího, aplikace musí pokračovat k vysílání datového proudu zvuku ve službě při současně a asynchronně čekání na odpověď ze služby. Tento vzor je na rozdíl od jiných protokolů webových požadavků a odpovědí jako HTTP. Tyto protokoly musíte provést žádost před příjmem všechny odpovědi; v protokolu služby Microsoft řeči přijetí odpovědí *při jsou stále datový proud zvuku pro požadavek*.

> [!NOTE]
> Tato funkce není podporována při použití řeči HTTP REST API.

### <a name="turns"></a>Změní

Rozpoznávání řeči je poskytovatel informací. Při mluvení pokoušíte nesou informace, které jsou ve vašem vlastnictví někomu, kdo naslouchá na tyto informace. Při předávání informací, obvykle trvat oplátku mluvení a naslouchá. Podobně rozpoznávání řeči aplikace komunikuje s uživateli případně naslouchání a reakcí, i když vaše aplikace obvykle nemá většinu naslouchání. Je volána mluvené vstup uživatele a služby odpovědi na tento vstup *zapnout*. A *zapnout* spustí, když uživatel hovoří a končí, když aplikace dokončí zpracování odpovědi řeči služby.

### <a name="telemetry"></a>Telemetrická data

Vytváření rozpoznávání řeči zařízení nebo aplikace může být náročné, i pro zkušené vývojáře. Na základě datového proudu protokoly často zdá se, že na první pohled složitý a důležité podrobnosti jako interval detekce může být úplně nové. Mnoho zprávy museli být úspěšně odeslané a přijaté k dokončení pár jeden požadavek a odpověď, je *velmi* důležité shromažďovat úplné a přesné údaje o těchto zpráv. Protokol Microsoft řeči Service poskytuje kolekci tato data. Měli byste si úsilí, aby co nejpřesněji; zadejte požadovaná data zadáním úplné a přesné data, můžete se mohou obrátit sami – byste někdy potřebovali pomoc od týmu služby řeči Microsoft při řešení potíží implementaci klienta, kvality telemetrická data, které jste shromáždili bude velmi důležitá pro problém analýza.

> [!NOTE]
> Tato funkce není podporována při použití funkce rozpoznávání řeči REST API.

### <a name="speech-application-states"></a>Rozpoznávání řeči aplikace stavy

Kroky, které je třeba provést povolit hlasový vstup v aplikaci jsou mírně liší od pokynů pro jiné formy vstup, například kliknutí myší nebo prstem klepne na položku. Musíte udržovat přehled o Pokud je vaše aplikace naslouchá na mikrofonu a odeslání dat službě rozpoznávání řeči, když se čeká na odpověď ze služby, a pokud je ve stavu nečinnosti. Vztah mezi tyto stavy je znázorněn v následujícím diagramu.

![Stav aplikace řeči Diagram](Images/speech-application-state-diagram.png)

Vzhledem k tomu, že služba rozpoznávání řeči Microsoft účastní některé stavy, protokol služby definuje zprávy, které pomáhají vaší aplikace přechod mezi stavy. Aplikace musí jak interpretovat a fungují v těchto zpráv protokolu ke sledování a správě stavy řeči aplikace.

## <a name="using-the-speech-recognition-service-from-your-apps"></a>Pomocí služby rozpoznávání řeči z vašich aplikací

Služba rozpoznávání řeči Microsoft nabízí dva způsoby pro vývojáře pro přidání řeči do své aplikace.

- [REST API](GetStarted/GetStartedREST.md): vývojáři mohou použít HTTP volání ze svých aplikací se službou pro rozpoznávání řeči.
- [Knihovny klienta](GetStarted/GetStartedClientLibraries.md): pokročilé funkce, vývojáři můžou stáhnout Microsoft Speech klientské knihovny a propojit do své aplikace.  Knihovny klienta jsou dostupné na různých platformách (Windows, Android, iOS) pomocí různých jazyků (C#, Java, JavaScript, ObjectiveC).

| Případy použití | [Rozhraní REST API](GetStarted/GetStartedREST.md) | [Knihovny klienta](GetStarted/GetStartedClientLibraries.md) |
|-----|-----|-----|
| Převést prostě mluvené zvuk, například příkazy (audio délka < 15 s) bez dočasné výsledky | Ano | Ano |
| Převést dlouho zvuk (s > 15) | Ne | Ano |
| Zvuk datového proudu s dočasné výsledky potřeby | Ne | Ano |
| Pochopení textu převést z zvuk pomocí LEOŠ | Ne | Ano |

 Pokud váš jazyk nebo platformu ještě nemá sadu SDK, můžete vytvořit vlastní implementaci na základě [protokolu dokumentaci](API-Reference-REST/websocketprotocol.md).

## <a name="recognition-modes"></a>Režimy rozpoznávání

Existují tři režimy rozpoznávání: `interactive`, `conversation`, a `dictation`. Režim rozpoznávání upraví rozpoznávání řeči na základě toho, jak se pravděpodobně řeči uživatele. Zvolte režim rozpoznávání vhodné pro vaši aplikaci.

> [!NOTE]
> Režimy rozpoznávání může mít různé chování [REST protokol](#rest-speech-recognition-api) než v [protokol WebSocket](#webSocket-speech-recognition-api). Například rozhraní REST API nepodporuje průběžné rozpoznávání i v režimu konverzace nebo diktování.
> [!NOTE]
> Tyto režimy se dají použít, když používáte přímo protokol REST nebo protokolu WebSocket. [Klientské knihovny](GetStarted/GetStartedClientLibraries.md) použít jiné parametry k určení režimu rozpoznávání. Další informace najdete v knihovně klienta podle vašeho výběru.

Službu Microsoft řeči vrátí pouze jeden výsledek frázi rozpoznávání pro všechny režimy rozpoznávání. Existuje omezení pro všechny jeden utterance 15 sekund.

### <a name="interactive-mode"></a>Interaktivní režim

V `interactive` režimu, může uživatel zadává krátké požadavků a očekává aplikaci k provedení akce v odpovědi.

Tyto vlastnosti jsou typické pro aplikace v interaktivním režimu:

- Uživatelé věděli, že jejich jsou mluvení k počítači, ne na jiné lidské.
- Aplikace uživatelé předem věděli, chtějí vyslovte, podle chtějí aplikace udělat.
- Utterances obvykle poslední o 2 – 3 sekund.

### <a name="conversation-mode"></a>Konverzace režimu

V `conversation` režim, uživatelé se zabývají lidské lidské konverzace.

Tyto vlastnosti jsou typické pro aplikace v režimu konverzace:

- Uživatelé věděli, že komunikují na jinou osobu.
- Rozpoznávání řeči rozšiřuje lidského konverzace tím, že jeden nebo oba účastníky zobrazíte mluvené text.
- Uživatelé vždy neplánujete chtějí. Tím vyjádříte.
- Uživatelé se často používají slang a dalších neformální řeči.

### <a name="dictation-mode"></a>Režim diktování

V `dictation` režim, uživatelé vyslovení delší utterances do aplikace pro další zpracování.

Tyto vlastnosti jsou typické pro aplikace v režimu diktování:

- Uživatelé věděli, že komunikují k počítači.
- Uživatelům se zobrazí výsledky text rozpoznávání řeči.
- Uživatelé často naplánujte co chtějí vyslovení a použít více formální jazyk.
- Uživatelé využívají úplné věty, posledních 5 až 8 sekund.

> [!NOTE]
> V režimu diktování a konverzace službu Microsoft řeči nevrací částečné výsledky. Místo toho službu vrátí výsledky stabilní frázi po nečinnosti hranice zvukový stream. Microsoft může zvýšit protokol řeči spuštění v těchto režimech průběžné rozpoznávání vylepšit.

## <a name="recognition-languages"></a>Rozpoznávání jazyky

*Jazyk rozpoznávání* Určuje jazyk, který mluví uživatelů vaší aplikace. Zadejte *jazyk rozpoznávání* s *jazyk* parametr dotazu adresy URL pro připojení. Hodnota *jazyk* dotazu parametr používá značky jazyka IETF [BCP 47](https://en.wikipedia.org/wiki/IETF_language_tag), a **musí** být jeden z jazyků, které podporuje rozpoznávání řeči rozhraní API. Úplný seznam jazyků podporovaných službou řeči naleznete na stránce [podporované jazyky](API-Reference-REST/supportedlanguages.md).

Službu Microsoft řeči odmítne požadavky na připojení neplatný zobrazením `HTTP 400 Bad Request` odpovědi. Neplatný požadavek je jedním který:

- Nezahrnuje *jazyk* hodnota parametru dotazu.
- Zahrnuje *jazyk* parametr, který je nesprávně naformátovaný dotazu.
- Zahrnuje *jazyk* parametr, který není jedním z jazyků podporu dotazu.

Můžete se rozhodnout pro vytvoření aplikace, který podporuje alespoň jeden všechny jazyky, které jsou podporovány službou.

### <a name="example"></a>Příklad:

V následujícím příkladu používá aplikace *konverzace* režimu rozpoznávání řeči pro angličtinu lektorem USA.

```HTTP
https://speech.platform.bing.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

## <a name="transcription-responses"></a>Přepis odpovědí

Přepis odpovědí z zvuk vrátit text převedený na klienty. Přepis odpověď obsahuje následující pole:

- `RecognitionStatus` Určuje stav rozpoznávání. Možné hodnoty jsou uvedeny v následující tabulce.

| Status | Popis |
| ------------- | ---------------- |
| Úspěch | Rozpoznávání byla úspěšná a ZobrazenýText pole je k dispozici |
| NoMatch | V zvukový datový proud byl zjištěn rozpoznávání řeči, ale měla shodná žádné slova cílový jazyk. [Status(#nomatch-recognition-status) rozpoznávání NoMatch součásti další podrobnosti najdete v části  |
| InitialSilenceTimeout | Začátek zvukový datový proud obsažené pouze nečinnosti a službu vypršel časový limit čekání na rozpoznávání řeči |
| BabbleTimeout | Začátek zvukový datový proud obsažené pouze šumu a službu vypršel časový limit čekání na rozpoznávání řeči |
| Chyba | Služba rozpoznávání došlo k vnitřní chybě a nelze pokračovat. |

- `DisplayText` Po použití velkých písmen, interpunkce a inverzní. text normalizaci byly použity a vulgárnost má byla maskována hvězdičkami, představuje rozpoznaný fráze. Pole ZobrazenýText nachází *pouze* Pokud `RecognitionStatus` pole má hodnotu `Success`.

- `Offset` Určuje posun (v jednotkách 100 nanosekund), kdy byl rozpoznán fráze, relativně ke spuštění zvuk datového proudu.

- `Duration`Určuje dobu (v jednotkách 100 nanosekund), tato fráze řeči.

Přepis odpověď se vrátí informace v případě potřeby. V tématu [výstupní formát](#output-format) jak vrátit podrobnější výstupy.

Služba rozpoznávání řeči Microsoft podporuje další přepis proces, který zahrnuje přidání malá a velká písmena nebo interpunkční znaménka, maskování vulgárnost a normalizace text, který se běžné formuláře. Například pokud uživatel mluví fráze reprezentována slova "připomenout koupit šesti Iphony", služby společnosti Microsoft řeči vrátí přepisována text "Připomeňte mi koupit 6 Iphony." Proces, který převádí slovo "šest" číslo "6" se nazývá *inverzní Text normalizaci* (*ITN* pro zkrácení).

### <a name="nomatch-recognition-status"></a>Stav rozpoznávání NoMatch

Vrátí odpověď přepis `NoMatch` v `RecognitionStatus` při rozpoznávání řeči službu Microsoft zjistí řeči v zvukový datový proud, ale nelze spárovat. Tento převod řeči na gramatika jazyk používá pro žádost. Například *NoMatch* situaci může dojít, pokud uživatel uveden něco v němčině při rozpoznávání očekává angličtinu jako mluvené jazyk. Vzor zvukového průběhu utterance by signalizovat přítomnost lidského hlasu, ale žádná z slov oznamována by odpovídat anglické slovníkový USA používány pro rozpoznávání.

Jiné *NoMatch* při rozpoznávání algoritmus se nepodařilo najít přesná shoda pro zvuků obsažené v zvukový datový proud dochází k. Pokud k tomuto stavu dochází, může vytvořit službu Microsoft řeči *speech.hypothesis* zprávy, které obsahují *hodnoty text* vytvoří, ale *speech.phrase*zpráv, ve kterém *RecognitionStatus* je *NoMatch*. Tato podmínka je normální; nesmí vytvořit žádný odhad o přesnost nebo věrnosti od textu ve *speech.hypothesis* zprávy. Kromě toho můžete nesmí předpokládat, že protože službu Microsoft řeči vytváří *speech.hypothesis* zprávy, které je možné vytvořit službu *speech.phrase* zpráv s  *RecognitionStatus* *úspěch*.

## <a name="output-format"></a>Výstupní formát

Microsoft řeči Service může vrátit různých formátech datové části v přepis odpovědi. Jsou všechny datové části JSON struktury.

Výsledný formát frázi můžete řídit tak, že zadáte `format` parametr dotazu adresy URL. Ve výchozím nastavení, služba vrátí `simple` výsledky.

| Formát | Popis |
|-----|-----|
| `simple` | Výsledek zjednodušené frázi obsahující stav rozpoznávání a rozpoznaný text v zobrazení formuláře. |
| `detailed` | Stav rozpoznávání a N nejvhodnější seznam výsledků frázi, kde každý výsledek frázi obsahuje všechny čtyři rozpoznávání formulářů a spolehlivosti skóre. |

`detailed` Formátu obsahuje [N nejvhodnější hodnoty](#n-best-values), kromě `RecognitionStatus`, `Offset`, a `duration`, v odpovědi.

### <a name="n-best-values"></a>N-nejvhodnější hodnoty

Naslouchací procesy, ať už lidského nebo počítače, nikdy nemůže mít hodnotu určité, že se dozvěděli *přesně* co byla oznamována. Můžete přiřadit naslouchací proces *pravděpodobnosti* jenom na konkrétní výklad utterance. 

Za normálních podmínek, když ostatní lidé, se kterými se často komunikovat lidé mají vysokou pravděpodobností rozpozná slova, která měla oznamována. Moduly pro naslouchání na základě počítače řeči snažit dosáhnout podobné úrovně přesnost a v pravém podmínkách [dosahují parita s člověka](https://blogs.microsoft.com/next/2016/10/18/historic-achievement-microsoft-researchers-reach-human-parity-conversational-speech-recognition/#sm.001ykosqs14zte8qyxj2k9o28oz5v).

Algoritmy, které se používají v rozpoznávání řeči prozkoumejte alternativní interpretace utterance jako součást normálním zpracování. Obvykle alternativy zahodí důkaz považuje jeden interpretace začne být čtenáře. V méně než optimálních podmínek ale pro rozpoznávání řeči dokončení seznam alternativní možné interpretace. Horní *N* alternativy v tomto seznamu se nazývají *seznamu N nejvhodnější*. Každý alternativou je přiřazena [spolehlivosti skóre](#confidence). Spolehlivosti skóre rozsahu od 0 do 1. Skóre 1 představuje nejvyšší úroveň spolehlivosti. Skóre 0 představuje nejnižší úroveň spolehlivosti.

> [!NOTE]
> Počet položek v seznamu N nejvhodnější lišit napříč více utterances. Počet položek se může lišit v několika uznání z *stejné* utterance. Případný rozdíl je fyzická a očekávaný výsledek pravděpodobnosti povahy algoritmu rozpoznávání řeči.

Každý záznam, vrátí se v seznamu N nejvhodnější obsahuje

- `Confidence`, které představuje [spolehlivosti skóre](#confidence) této položky.
- `Lexical`, který je [lexikální formuláře](#lexical-form) rozpoznaného textu.
- `ITN`, který je [ITN formuláře](#itn-form) rozpoznaného textu.
- `MaskedITN`, který je [maskovat ITN formuláře](#masked-itn-form) rozpoznaného textu.
- `Display`, který je [zobrazení formuláře](#display-form) rozpoznaného textu.

### Skóre spolehlivosti <a id="confidence"></a>

Skóre spolehlivosti jsou nedílnou součástí systémů rozpoznávání řeči. Službu Microsoft řeči získává spolehlivosti skóre z *spolehlivosti třídění*. Microsoft soupravy spolehlivosti třídění v rámci sady funkcí, které jsou navržené tak, lze uchovávat rozlišit rozpoznávání správná a že jsou nesprávné. K jednotlivých slov a celý utterances se vyhodnocují spolehlivosti skóre.

Pokud chcete použít spolehlivosti skóre, které se vrátí pomocí služby, mějte na paměti následující chování:

- Skóre důvěru lze porovnat pouze v rámci stejné režim rozpoznávání a jazyka. Nelze použít k porovnání výsledků mezi různé jazyky nebo rozpoznávání různé režimy. Například spolehlivosti skóre v režimu interaktivní rozpoznávání má *žádné* korelace spolehlivosti skóre v režimu diktování.
- Skóre spolehlivosti jsou nejvhodnější na sadu utterances s omezeným přístupem. Vysoký stupeň variabilita skóre pro velké sady utterances přirozeně neexistuje.

Pokud se rozhodnete použít hodnotu spolehlivosti skóre jako *prahová hodnota* na který aplikace funguje, rozpoznávání řeči použít k vytvoření Prahové hodnoty.

- Proveďte rozpoznávání řeči reprezentativní vzorek utterances pro vaši aplikaci.
- Shromažďujte spolehlivosti skóre pro každý rozpoznávání v sadě ukázka.
- V některých percentilu spolehlivosti pro tuto ukázku základní prahovou hodnotu.

Žádná hodnota jednoduchá prahová hodnota je vhodný pro všechny aplikace. Přijatelné spolehlivosti skóre pro jednu aplikaci může nepřijatelné pro jinou aplikaci.

### <a name="lexical-form"></a>Lexikální formuláře

Není rozpoznaný text lexikální formulář přesně jak ho došlo k chybě v utterance a bez interpunkce nebo malá a velká písmena. Například by být lexikální formu adresu "1020 Enterprise způsob" *deset dvacet enterprise způsob*, za předpokladu, že ho byl používaný tímto způsobem. Lexikální formuláře věty "připomenout koupit 5 tužky" je *připomenout koupit pět tužky*.

Lexikální formuláře je nejvhodnější pro aplikace, které potřebují k provedení normalizaci nestandardní text. Lexikální formuláře je také vhodné pro aplikace, které potřebují nezpracované rozpoznávání slova.

Vulgárnost se nikdy maskovat ve formuláři lexikální.

### <a name="itn-form"></a>ITN formuláře

Text normalizace je proces převodu textu z jednoho formátu do jiného "kanonický" formuláře. Například telefonní číslo "555-1212" může být převedena na kanonický tvar *pět pět pět jeden dva jeden dvě*. *Inverzní* text normalizaci (ITN) obrátí tento proces převodu slova "pět pět pět jeden dva jeden dva" k obráceným kanonický tvar *555-1212*. ITN formu výsledku rozpoznávání nezahrnuje interpunkční znaménka nebo velká písmena.

Formulář ITN je nejvhodnější pro aplikace, které fungují v rozpoznaný text. Například aplikace, která umožňuje uživatelům číst obsah hledaných termínů a pak používá tyto podmínky v dotazu webové by formulář ITN. Vulgárnost se nikdy maskovat ve formuláři ITN. K maskování vulgárnost, použijte *maskované ITN formuláře*.

### <a name="masked-itn-form"></a>Maskované ITN formuláře

Protože vulgárnost je samozřejmě mluvené jazyka, služba Microsoft řeči rozpozná taková slova a slovní spojení při uslyšíte. Vulgárnost může ale být vhodné pro všechny aplikace, zejména aplikace s cílové skupiny s omezeným přístupem, nezletilých uživatele.

Maskované formuláře ITN platí vulgárnost maskování formuláře normalizaci inverzní text. Maskování vulgárnost, nastavte hodnotu hodnotu parametru vulgárnost `masked`. Když je maskovat vulgárnost, jsou slova, která jsou rozpoznána jako součást tohoto jazyka vulgárnost slovníkový nahrazena hvězdičkami. Příklad: *připomenout koupit 5 *** tužky*. Maskované ITN formu výsledku rozpoznávání nezahrnuje interpunkční znaménka nebo velká písmena.

> [!NOTE]
> Pokud hodnota parametru dotazu vulgárnost nastavena na `raw`, maskované ITN formuláře je stejný jako ITN formuláře. Je vulgárnost *není* maskování.

### <a name="display-form"></a>Formulář pro zobrazení

Interpunkce a velkých písmen signál umístění zvýraznění, kde k pozastavení a tak dále, což usnadňuje pochopení textu. Zobrazit formulář přidá do rozpoznávání výsledky, což formuláři nejvhodnější pro aplikace, které zobrazit mluvené text interpunkce a velkých písmen.

Protože zobrazení formuláře rozšiřuje maskované ITN formulář, můžete nastavit hodnotu parametru vulgárnost `masked` nebo `raw`. Pokud je hodnota nastavená `raw`, rozpoznávání výsledky budou zahrnovat všechny vulgárnost oznamována uživatelem. Pokud je hodnota nastavená `masked`, rozpoznán jako součást tohoto jazyka vulgárnost slovníkový slova pomocí hvězdiček.

### <a name="sample-responses"></a>Ukázka odpovědí

Jsou všechny datové části JSON struktury.

Formát datové části `simple` fráze výsledek:

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

Formát datové části `detailed` fráze výsledek:

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

## <a name="profanity-handling-in-speech-recognition"></a>Vulgárnost zpracování v rozpoznávání řeči

Služba Microsoft řeči rozpozná všechny formy lidského hlasu, včetně slova a slovní spojení, které Spousta lidí by klasifikovat jako "klení." Můžete řídit, jak služba zpracovává vulgárnost pomocí *vulgárnost* parametr dotazu. Ve výchozím nastavení, služba zakrývá vulgárnost v *speech.phrase* výsledků a nevrací *speech.hypothesis* zprávy, které obsahují vulgárnost.

| *Vulgárnost* hodnota | Popis |
| - | - |
| `masked` | Masek Klení pomocí hvězdiček. Toto chování je výchozí. | 
| `removed` | Odebere všechny výsledky vulgárnost. |
| `raw` | Rozpozná a vrátí vulgárnost všechny výsledky. |

### <a name="profanity-value-masked"></a>Hodnota vulgárnost `Masked`

K maskování vulgárnost, nastavte *vulgárnost* parametr na hodnotu dotazu *maskovat*. Když *vulgárnost* parametr dotazu má tuto hodnotu nebo není zadaný pro žádost službu *masek* vulgárnost. Služba provede maskování nahrazením vulgárnost ve výsledcích rozpoznávání hvězdičky. Když zadáte vulgárnost maskování zpracování, služba nevrátí *speech.hypothesis* zprávy, které obsahují vulgárnost.

### <a name="profanity-value-removed"></a>Hodnota vulgárnost `Removed`

Když *vulgárnost* dotazu parametru má hodnotu *odebrat*, služba odstraní vulgárnost z obou *speech.phrase* a *speech.hypothesis* zprávy. Výsledky jsou stejné *jako v případě, že nebyly oznamována slova vulgárnost*.

#### <a name="profanity-only-utterances"></a>Pouze vulgárnost utterances

Uživatel může řeči *pouze* vulgárnost při aplikaci nakonfiguroval službu, kterou chcete odebrat vulgárnost. Pro tento scénář, pokud je režim rozpoznávání *diktování* nebo *konverzace*, službu nevrací *speech.result*. Pokud je režim rozpoznávání *interaktivní*, vrátí služby *speech.result* se stavovým kódem *NoMatch*. 

### <a name="profanity-value-raw"></a>Hodnota vulgárnost `Raw`

Když *vulgárnost* dotazu parametru má hodnotu *nezpracovaná*, služba odebrat nebo maskování vulgárnost buď *speech.phrase* nebo  *Speech.hypothesis* zprávy.
