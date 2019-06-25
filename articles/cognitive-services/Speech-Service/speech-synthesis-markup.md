---
title: Jazyk využívající značky syntézu řeči (SSML) – hlasové služby
titleSuffix: Azure Cognitive Services
description: K řízení výslovnost a prosody v převodu textu na řeč pomocí Markup Language syntézu řeči.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 488cbf1874e023cf3a665bc9f2087900b30b3d3d
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204914"
---
# <a name="speech-synthesis-markup-language-ssml"></a>Jazyk SSML (Speech Synthesis Markup Language)

Rozpoznávání řeči syntézu Markup Language (SSML) je jazyka kód založený na formátu XML, který umožňuje vývojářům zadat jak vstupní text, který je převeden na řečového pomocí převod textu na řeč služby. Porovnání na prostý text, SSML umožňuje vývojářům pro vyladění od výslovnost, mluvený rychlost, svazek a více převod textu na řeč výstupu. Normální interpunkce, jako je například pozastavení po určité době nebo pomocí správné intonací větu s otazníkem končí jsou automaticky zpracovány.

Implementace hlasové služby SSML je založená na World Wide Web Consortium [řeči syntézu Markup Language verze 1.0](https://www.w3.org/TR/speech-synthesis).

> [!IMPORTANT]
> Znaky čínštiny, japonštiny a korejštiny počítány jako dva znaky pro fakturaci. Další informace najdete v tématu [ceny](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

## <a name="standard-neural-and-custom-voices"></a>Standardní, neuronových sítí a vlastní hlasů

Zvolte standardní a neuronových sítí hlasů nebo vytvořte vlastní vlastní hlasové jedinečné produkty nebo značky. 75 standardní hlasy jsou k dispozici ve více než 45 jazyků a národních prostředí a 5 neuronových sítí hlasy jsou k dispozici v 4 jazyky a národní prostředí. Úplný seznam podporovaných jazyků, národní prostředí a hlasů (neuronových sítí nebo standardním), najdete v části [jazykovou podporu](language-support.md).

Další informace o standard, neuronových sítí a vlastní hlasů, naleznete v tématu [přehled převodu textu na řeč](text-to-speech.md).

## <a name="supported-ssml-elements"></a>Podporované elementy SSML

Každý dokument SSML je vytvořen s prvky SSML (nebo značky). Tyto prvky se používají k upravit výšku, prosody, svazek a další. Následující části podrobně popisují použití každý prvek a prvek je povinná nebo volitelná.  

> [!IMPORTANT]
> Nezapomeňte použít dvojité uvozovky kolem hodnot atributů. Standardy pro XML ve správném formátu, platný vyžaduje hodnoty atributů bude uzavřen do dvojitých uvozovek. Například `<prosody volume="90">` je ve správném formátu, platný element, ale `<prosody volume=90>` není. SSML nemusí rozpoznat hodnoty atributů, které nejsou v uvozovkách.

## <a name="create-an-ssml-document"></a>Vytvořit dokument SSML

`speak` je kořenovým elementem a **požadované** pro všechny dokumenty SSML. `speak` Prvek obsahuje důležité informace, jako je verze, jazyka a definice slovník značek.

**Syntaxe**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="string"></speak>
```

**Atributy**

| Atribut | Popis | Povinné / volitelné |
|-----------|-------------|---------------------|
| version | Určuje verzi specifikace SSML použité k interpretaci značce dokumentu. Aktuální verze je 1.0. | Požaduje se |
| xml:lang | Určuje jazyk kořenového dokumentu. Hodnota může obsahovat kód jazyka dvě písmena, malá písmena (například **en**), nebo kód jazyka a země/oblast velká písmena (například **en US**). | Požaduje se |
| xmlns | Určuje identifikátor URI dokumentu, který definuje značky slovník dokumentu SSML (typy prvků a názvy atributů). Aktuální identifikátor URI je https://www.w3.org/2001/10/synthesis. | Požaduje se |

## <a name="choose-a-voice-for-text-to-speech"></a>Zvolte hlasový vstup pro převod textu na řeč

`voice` Je vyžadován element. Používá se k určení, který se používá pro převod textu na řeč hlasu.

**Syntaxe**

```xml
<voice name="string">
    This text will get converted into synthesized speech.
</voice>
```

**Atributy**

| Atribut | Popis | Povinné / volitelné |
|-----------|-------------|---------------------|
| name | Identifikuje hlasu použít pro převod textu na řeč výstup. Úplný seznam podporovaných hlasů, naleznete v tématu [jazykovou podporu](language-support.md#text-to-speech). | Požaduje se |

**Příklad**

> [!NOTE]
> V tomto příkladu `en-US-Jessa24kRUS` hlasu. Úplný seznam podporovaných hlasů, naleznete v tématu [jazykovou podporu](language-support.md#text-to-speech).

```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        This is the text that is spoken.
    </voice>
</speak>
```

## <a name="use-multiple-voices"></a>Použití více hlasů

V rámci `speak` element, můžete zadat více hlasů pro převod textu na řeč výstup. Tyto hlasy může být v různých jazycích. Každý hlas, musí být zabalené v textu `voice` elementu.

**Atributy**

| Atribut | Popis | Povinné / volitelné |
|-----------|-------------|---------------------|
| name | Identifikuje hlasu použít pro převod textu na řeč výstup. Úplný seznam podporovaných hlasů, naleznete v tématu [jazykovou podporu](language-support.md#text-to-speech). | Požaduje se |

**Příklad**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        Good morning!
    </voice>
    <voice  name="en-US-Guy24kRUS">
        Good morning to you too Jessa!
    </voice>
</speak>
```

## <a name="adjust-speaking-styles"></a>Upravit styly mluvící

> [!IMPORTANT]
> Tato funkce bude fungovat jenom s hlasy neuronových sítí.

Ve výchozím nastavení převod textu na řeč služby syntetizuje textu s použitím neutrální styl mluvy pro standardní i neuronových sítí hlasy. S neuronových sítí hlasů, můžete upravit styl mluvy vyjádřit cheerfulness empatické a subjektivního hodnocení s `<mstts:express-as>` elementu. Toto je volitelný prvek pro hlasové služby Azure jedinečný.

Pro tyto neuronových sítí hlasy v současné době jsou podporovány mluvy úpravy stylu:
* `en-US-JessaNeural`
* `zh-CN-XiaoxiaoNeural`

Změny se použijí na úrovni vět a styl se liší podle hlasu. Pokud není podporovaná styl, služba vrátí řeči ve výchozím neutrální mluvený styl.

**Syntaxe**

```xml
<mstts:express-as type="string"></mstts:express-as>
```

**Atributy**

| Atribut | Popis | Povinné / volitelné |
|-----------|-------------|---------------------|
| type | Určuje styl mluvy. V současné době mluvy styly jsou konkrétní hlasu. | Vyžaduje-li upravit styl mluvy pro Neurální hlasový vstup. Pokud používáte `mstts:express-as`, pak typ musí být zadaná. Pokud je zadaná neplatná hodnota, tento element se bude ignorovat. |

Tato tabulka slouží k určení mluvy styly, které jsou podporovány pro každý hlas neuronových sítí.

| Hlas | Type | Popis |
|-------|------|-------------|
| `en-US-JessaNeural` | type=`cheerful` | Vyjadřuje pro rozpoznávání emocí, který je kladné a šťastnější při |
| | type=`empathy` | Vyjadřuje vnímání caring a pochopení |
| `zh-CN-XiaoxiaoNeural` | type=`newscast` | Vyjadřuje formální tón podobný vysílání zpráv |
| | type=`sentiment` | Přenáší touching zprávy nebo příběh |

**Příklad**

Tento fragment SSML ukazuje, jak `<mstts:express-as>` element umožňuje změnit styl mluvy k `cheerful`.

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
    <voice name="en-US-JessaNeural">
        <mstts:express-as type="cheerful">
            That'd be just amazing!
        </mstts:express-as>
    </voice>
</speak>
```

## <a name="add-or-remove-a-breakpause"></a>Přidání nebo odebrání přerušení/pozastavení

Použití `break` prvek, který chcete vložit pozastavení (nebo konce) mezi slovy, nebo zabránit pozastaví automaticky přidá převod textu na řeč služby.

> [!NOTE]
> Tento element slouží k přepsání výchozího chování převod textu na ŘEČ pro slovo nebo frázi, pokud zvuky nepřirozené řečového pro toto slovo nebo fráze. Nastavte `strength` k `none` zabránit prozodický přerušení, která je automaticky vložen službou tex řeči.

**Syntaxe**

```xml
<break strength="string" />
<break time="string" />
```

**Atributy**

| Atribut | Popis | Povinné / volitelné |
|-----------|-------------|---------------------|
| síla | Určuje relativní doba trvání pozastavení pomocí jedné z následujících hodnot:<ul><li>None</li><li>slabé x</li><li>slabé</li><li>Střední (výchozí)</li><li>Silné</li><li>x-strong</li></ul> | Nepovinné |
| time | Určuje absolutní dobu pozastavit v řádu sekund a milisekund. Příkladem platné hodnoty jsou 2s a 500 | Nepovinné |

| Síla | Popis |
|----------|-------------|
| Žádné, nebo pokud neexistuje žádná hodnota | 0 ms |
| slabé x | 250 ms |
| slabé | 500 ms |
| Střední | 750 ms |
| Silné | 1000 ms |
| x-strong | 1250 ms |


**Příklad**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
    </voice>
</speak>
```

## <a name="specify-paragraphs-and-sentences"></a>Zadejte odstavců a vět

`p` a `s` elementy se používají k označení odstavců a vět, v uvedeném pořadí. Převod textu na řeč služby chybí tyto prvky, automaticky určí strukturu dokumentu SSML.

`p` Element může obsahovat text a následující prvky: `audio`, `break`, `phoneme`, `prosody`, `say-as`, `sub`, `mstts:express-as`, a `s`.

`s` Element může obsahovat text a následující prvky: `audio`, `break`, `phoneme`, `prosody`, `say-as`, `mstts:express-as`, a `sub`.

**Syntaxe**

```XML
<p></p>
<s></s>
```

**Příklad**

```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        <p>
            <s>Introducing the sentence element.</s>
            <s>Used to mark individual sentences.</s>
        </p>
        <p>
            Another simple paragraph.
            Sentence structure in this paragraph is not explicitly marked.
        </p>
    </voice>
</speak>
```

## <a name="use-phonemes-to-improve-pronunciation"></a>Použít ke zlepšení výslovnost fonémy

`ph` Element slouží k fonetická výslovnost v dokumentech SSML. `ph` Element může obsahovat pouze text, žádné elementy. Vždy poskytovat čitelné řeči jako záložní.

Fonetické abecedy se skládají z telefonů, které se skládají z písmen, čísel nebo znaků, někdy v kombinaci. Každý phone popisuje jedinečný zvuk řeči. To se liší od latinské abecedy, kde žádné písmeno může představovat více mluvené slovo zvuku. Vezměte v úvahu různých výslovnosti písmenem "c" slova "kandys" a "ukončí pokusy" nebo jiný výslovnosti kombinace písmen "th" slova "věc" a "".

**Syntaxe**

```XML
<phoneme alphabet="string" ph="string"></phoneme>
```

**Atributy**

| Atribut | Popis | Povinné / volitelné |
|-----------|-------------|---------------------|
| alphabet | Určuje fonetická abeceda při syntetizační výslovnost řetězec `ph` atribut. Řetězec určující abecedy. je třeba zadat malými písmeny. Tady jsou možné písmena abecedy, které můžete zadat.<ul><li>soubor IPA &ndash; mezinárodní fonetická abeceda</li><li>SAPI &ndash; Speech API Phone nastavit</li><li>UPS &ndash; univerzální nastavení telefonu</li></ul>Abecedy se vztahuje pouze na foném v elementu. Další informace najdete v tématu [fonetická abeceda odkaz](https://msdn.microsoft.com/library/hh362879(v=office.14).aspx). | Nepovinné |
| pH | Řetězec obsahující telefonů, které určují výslovnost slova `phoneme` elementu. Pokud zadaný řetězec obsahuje nerozpoznaný telefony, převod textu na řeč služby (převod textu na ŘEČ) odmítne celý dokument SSML a vytváří žádný řečovém výstupu zadaný v dokumentu. | Povinná, pokud pomocí fonémy. |

**Příklady**

```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        <s>His name is Mike <phoneme alphabet="ups" ph="JH AU"> Zhou </phoneme></s>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme>
    </voice>
</speak>
```

## <a name="adjust-prosody"></a>Upravit prosody

`prosody` Element slouží k určení změn od, countour, rozsah, míry, doba trvání a svazku pro převod textu na řeč výstup. `prosody` Element může obsahovat text a následující prvky: `audio`, `break`, `p`, `phoneme`, `prosody`, `say-as`, `sub`, a `s`.

Vzhledem k tomu, že hodnoty stanovují prozodickou atributů se může lišit v široké modulu pro rozpoznávání řeči interpretuje jako návrhy toho, co by měl být skutečné hodnoty stanovují prozodickou vybraný hlasový přiřazené hodnoty. Převod textu na řeč služby kapacitního limitu nebo nahradí hodnoty, které nejsou podporovány. Příkladem nepodporované hodnoty jsou od 1 MHz nebo do svazku 120.

**Syntaxe**

```XML
<prosody pitch="value" contour="value" range="value" rate="value" duration="value" volume="value"></prosody>
```

**Atributy**

| Atribut | Popis | Povinné / volitelné |
|-----------|-------------|---------------------|
| Výška | Určuje rozteč směrný plán pro text. Mohou vyjádřit prvotního jako:<ul><li>Absolutní hodnota, vyjádřené jako čísla následované "Hz" (Hz). Například 600Hz.</li><li>Relativní hodnotu, vyjádřené jako číslo předcházet párový příkaz "+" nebo "-" a následuje "Hz" nebo "st", který určuje dobu, jakou chcete-li změnit výšku. Příklad: +80 Hz nebo - 2st. "st" znamená, že změna jednotek je semitone, což je polovinu tón (krok polovině) ve standardním měřítku diatonic.</li><li>Konstantní hodnota:<ul><li>x nízká</li><li>Nízká</li><li>Střední</li><li>Vysoká</li><li>x vysoká</li><li>default</li></ul></li></ul>. | Nepovinné |
| contour | Rozvrh není podporována pro Neurální hlasy. Rozvrh představuje změny v výška mluveného obsahu jako pole cílů v zadaném časovém pozic v řečovém výstupu. Každý cíl je definovaná pomocí sad párů parametru. Příklad: <br/><br/>`<prosody contour="(0%,+20Hz) (10%,-2st) (40%,+10Hz)">`<br/><br/>První hodnota v každou sadu parametrů určuje umístění změnit výšku jako procentuální hodnotu doby trvání textu. Druhá hodnota určuje dobu, chcete-li zvýšit nebo snížit prvotního pomocí relativní hodnotu nebo hodnoty výčtu pro rozteč (viz `pitch`). | Nepovinné |
| rozsah  | Hodnota, která představuje řadu výška textu. Může express `range` pomocí stejné hodnoty absolutní, relativní hodnoty nebo hodnoty výčtu používají k popisu `pitch`. | Nepovinné |
| Frekvence  | Informuje o frekvenci mluvy textu. Může express `rate` jako:<ul><li>Relativní hodnotu vyjádřenou jako číslo, které funguje jako násobitel výchozí. Například hodnota *1* má za následek žádná změna míry. Hodnota *.5* vede halving rychlost. Hodnota *3* vede tripling rychlost.</li><li>Konstantní hodnota:<ul><li>x-slow</li><li>Pomalé</li><li>Střední</li><li>Rychlé</li><li>x-fast</li><li>default</li></ul></li></ul> | Nepovinné |
| Doba trvání  | Doba, která má uplynout při řeč syntézu (převod textu na ŘEČ) služby přečte text, v sekundách a milisekundách. Například *2s* nebo *1800ms*. | Nepovinné |
| svazek  | Informuje o úrovni svazku hlasem. Mohou vyjádřit svazku:<ul><li>Absolutní hodnota, vyjádřené jako číslo v rozsahu od 0,0 do 100.0 z *nejtišších* k *omezit*. Například 75. Výchozí hodnota je 100.0.</li><li>Relativní hodnotu, vyjádřené jako číslo předcházet párový příkaz "+" nebo "-", který určuje velikost chcete změnit svazek. Například + 10 nebo-5.5.</li><li>Konstantní hodnota:<ul><li>Tiché</li><li>x-soft</li><li>obnovitelné</li><li>Střední</li><li>loud</li><li>x-loud</li><li>default</li></ul></li></ul> | Nepovinné |

### <a name="change-speaking-rate"></a>Mluvy frekvence změny

Rychlost čtení lze použít pro standardní hlasy v aplikaci word nebo úrovni vět. Zatímco mluvený míra může používat jedině pro Neurální hlasy na úrovni vět.

**Příklad**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Guy24kRUS">
        <prosody rate="+30.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-volume"></a>Změnit svazek

Svazek změny mohou být použity na standardní hlasy v aplikaci word nebo úrovni vět. Zatímco změny hlasitosti dá používat jedině pro Neurální hlasy na úrovni vět.

**Příklad**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        <prosody volume="+20.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-pitch"></a>Změnit výšku

Rozteč změny mohou být použity na standardní hlasy v aplikaci word nebo úrovni vět. Vzhledem k tomu od změny dá používat jedině pro Neurální hlasy na úrovni vět.

**Příklad**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Guy24kRUS">
        Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
    </voice>
</speak>
```

### <a name="change-pitch-contour"></a>Změnit výšku obrysu

> [!IMPORTANT]
> Rozteč tvarování změny nejsou podporovány s hlasy neuronových sítí.

**Příklad**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        <prosody contour="(80%,+20%) (90%,+30%)" >
            Good morning.
        </prosody>
    </voice>
</speak>
```

## <a name="next-steps"></a>Další postup

* [Podpora jazyků: hlasů, národní prostředí, jazyků](language-support.md)
