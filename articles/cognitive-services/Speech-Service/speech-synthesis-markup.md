---
title: SSML (Speech syntézy Language) – hlasové služby
titleSuffix: Azure Cognitive Services
description: K řízení výslovnost a prosody v převodu textu na řeč pomocí Markup Language syntézu řeči.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: e2b1e02a622dfe4ae488e372e44c8440f20d7034
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2019
ms.locfileid: "68501151"
---
# <a name="speech-synthesis-markup-language-ssml"></a>Jazyk SSML (Speech Synthesis Markup Language)

SSML (Speech syntézy Language) je značkovací jazyk založený na jazyce XML, který umožňuje vývojářům určit, jakým způsobem se vstupní text převede na syntetizované rozpoznávání řeči pomocí služby převodu textu na řeč. V porovnání s prostým textem umožňuje SSML vývojářům doladit rozteč, výslovnost, míru řeči, objem a další výstup textu na řeč. Normální interpunkční znaménka, jako je například pozastavení po určité době, nebo použití správné nečinnosti, pokud je věta zakončena znakem otazníku, automaticky zpracována.

Implementace služby SSML pro rozpoznávání řeči je založená konsorcium World Wide Web na [jazyce XML pro rozpoznávání řeči verze 1,0](https://www.w3.org/TR/speech-synthesis).

> [!IMPORTANT]
> Čínské, japonské a korejské znaky se počítají jako dva znaky pro účely fakturace. Další informace najdete v tématu [ceny](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

## <a name="standard-neural-and-custom-voices"></a>Standardní, neuronové a vlastní hlasy

Vyberte si ze standardních nebo neuronové hlasů nebo si vytvořte vlastní hlas jedinečný pro svůj produkt nebo značku. 75 a standardní hlasy jsou k dispozici ve více než 45 jazycích a národních prostředích a 5 neuronové hlasy jsou k dispozici ve 4 jazycích a národních prostředích. Úplný seznam podporovaných jazyků, národních prostředí a hlasů (neuronové a Standard) najdete v tématu [Podpora jazyků](language-support.md).

Další informace o standardních, neuronové a vlastních hlasů najdete v tématu [Přehled převodu textu na řeč](text-to-speech.md).

## <a name="supported-ssml-elements"></a>Podporované elementy SSML

Každý dokument SSML je vytvořen pomocí SSML prvků (nebo značek). Tyto prvky slouží k úpravě roztečí, Prosody, objemu a dalších. Následující části podrobně popisují, jak se jednotlivé prvky používají, a když je prvek povinný nebo volitelný.  

> [!IMPORTANT]
> Nezapomeňte použít dvojité uvozovky kolem hodnot atributů. Standardy pro správný formát platná XML vyžadují, aby hodnoty atributu byly uzavřeny do dvojitých uvozovek. Například `<prosody volume="90">` je ve správném formátu platný prvek, ale `<prosody volume=90>` není. SSML nesmí rozpoznat hodnoty atributu, které nejsou v uvozovkách.

## <a name="create-an-ssml-document"></a>Vytvoření dokumentu SSML

`speak`je kořenový prvek a je **vyžadován** pro všechny dokumenty SSML. `speak` Element obsahuje důležité informace, jako je verze, jazyk a definice slovníku označení.

**Syntaxe**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="string"></speak>
```

**Atributy**

| Atribut | Popis | Povinné / volitelné |
|-----------|-------------|---------------------|
| version | Určuje verzi specifikace SSML, která se používá k interpretaci značek dokumentu. Aktuální verze je 1,0. | Požadováno |
| xml:lang | Určuje jazyk kořenového dokumentu. Hodnota může obsahovat malé písmeno, kód jazyka se dvěma písmeny (například **EN**), kód jazyka a zemi/oblast (například **en-US**). | Požadováno |
| xmlns | Určuje identifikátor URI dokumentu, který definuje slovník značek (typy prvků a názvy atributů) dokumentu SSML. Aktuální identifikátor URI je https://www.w3.org/2001/10/synthesis. | Požadováno |

## <a name="choose-a-voice-for-text-to-speech"></a>Volba hlasu pro převod textu na řeč

`voice` Element je povinný. Slouží k určení hlasu, který se používá pro převod textu na řeč.

**Syntaxe**

```xml
<voice name="string">
    This text will get converted into synthesized speech.
</voice>
```

**Atributy**

| Atribut | Popis | Povinné / volitelné |
|-----------|-------------|---------------------|
| name | Identifikuje hlas používaný pro výstup textu na řeč. Úplný seznam podporovaných hlasů najdete v tématu [Podpora jazyků](language-support.md#text-to-speech). | Požadováno |

**Příklad**

> [!NOTE]
> V tomto příkladu se `en-US-Jessa24kRUS` používá hlas. Úplný seznam podporovaných hlasů najdete v tématu [Podpora jazyků](language-support.md#text-to-speech).

```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        This is the text that is spoken.
    </voice>
</speak>
```

## <a name="use-multiple-voices"></a>Použití více hlasů

V rámci `speak` elementu můžete zadat více hlasů pro výstup textu na řeč. Tyto hlasy můžou být v různých jazycích. U každého hlasu musí být text zabalen do `voice` elementu.

**Atributy**

| Atribut | Popis | Povinné / volitelné |
|-----------|-------------|---------------------|
| name | Identifikuje hlas používaný pro výstup textu na řeč. Úplný seznam podporovaných hlasů najdete v tématu [Podpora jazyků](language-support.md#text-to-speech). | Požadováno |

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

## <a name="adjust-speaking-styles"></a>Upravit styly speaking

> [!IMPORTANT]
> Tato funkce bude fungovat jenom s neuronové hlasy.

Ve výchozím nastavení služba pro převod textu na řeč syntetizuje text pomocí neutrálního mluveného stylu pro hlasy Standard i neuronové. Pomocí hlasů neuronové můžete upravit styl speakování na Express cheerfulness, soucit nebo mínění s `<mstts:express-as>` prvkem. Toto je volitelný element jedinečný pro služby Azure Speech Services.

V současné době jsou pro tyto hlasy neuronové podporovány úpravy stylu speaking:
* `en-US-JessaNeural`
* `zh-CN-XiaoxiaoNeural`

Změny se aplikují na úrovni věty a styl se liší podle hlasu. Pokud styl není podporován, služba vrátí řeč ve výchozím stylu neutrálního mluveného slova.

**Syntaxe**

```xml
<mstts:express-as type="string"></mstts:express-as>
```

**Atributy**

| Atribut | Popis | Povinné / volitelné |
|-----------|-------------|---------------------|
| type | Určuje styl speaking. V současné době jsou styly mluvené řeči specifické pro konkrétní hlas. | Vyžaduje se, když se upraví styl speakování pro neuronové hlas. Pokud používáte `mstts:express-as`, musí být zadaný typ. Pokud je zadána neplatná hodnota, bude tento prvek ignorován. |

Pomocí této tabulky můžete určit, které mluvené styly jsou pro každý neuronové hlas podporovány.

| Hlas | type | Popis |
|-------|------|-------------|
| `en-US-JessaNeural` | typ =`cheerful` | Vyjadřuje, že emoce jsou pozitivní a veselé |
| | typ =`empathy` | Vyjadřuje smysl caring a porozumění |
| `zh-CN-XiaoxiaoNeural` | typ =`newscast` | Vyjadřuje formální tón, podobně jako zprávy všesměrového vysílání. |
| | typ =`sentiment` | Předává zprávu o doteku nebo příběh. |

**Příklad**

Tento fragment SSML ukazuje, `<mstts:express-as>` jak se prvek používá ke změně stylu speakování na. `cheerful`

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
    <voice name="en-US-JessaNeural">
        <mstts:express-as type="cheerful">
            That'd be just amazing!
        </mstts:express-as>
    </voice>
</speak>
```

## <a name="add-or-remove-a-breakpause"></a>Přidat nebo odebrat přerušení/pozastavení

`break` Pomocí elementu vložte pauzy (nebo přerušit) mezi slova nebo Zabraňte automatickému přidání do služby převodu textu na řeč.

> [!NOTE]
> Pomocí tohoto prvku můžete přepsat výchozí chování převodu textu na řeč (TTS) pro slovo nebo frázi v případě, že syntetizované rozpoznávání řeči pro toto slovo nebo frázi nepřirozeně zvuk. Nastavte `strength`na , abynedocházelokpřerušeníProzodickýpředěl,kteréjeautomatickyvloženéslužboupropřevodtextunařeč.`none`

**Syntaxe**

```xml
<break strength="string" />
<break time="string" />
```

**Atributy**

| Atribut | Popis | Povinné / volitelné |
|-----------|-------------|---------------------|
| obsahem | Určuje relativní dobu trvání pozastavení pomocí jedné z následujících hodnot:<ul><li>žádný</li><li>x – slabý</li><li>slabé</li><li>střední (výchozí)</li><li>silné</li><li>x – silné</li></ul> | volitelná, |
| time | Určuje absolutní dobu trvání pauzy v sekundách nebo milisekundách. Příklady platných hodnot jsou 2S a 500. | volitelná, |

| Obsahem | Popis |
|----------|-------------|
| Žádná, nebo pokud není zadána žádná hodnota | 0 ms |
| x – slabý | 250 ms |
| slabé | 500 ms |
| středně silné | 750 ms |
| silné | 1000 MS |
| x – silné | 1250 ms |


**Příklad**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
    </voice>
</speak>
```

## <a name="specify-paragraphs-and-sentences"></a>Zadat odstavce a věty

`p`prvky `s` a se používají k označení odstavců a vět v uvedeném pořadí. V případě absence těchto prvků služba převod textu na řeč automaticky určuje strukturu dokumentu SSML.

`prosody` `phoneme` `break` `audio` `sub` Elementmůže`s`obsahovat text a následující prvky:,,, `say-as`,, ,`mstts:express-as`a. `p`

`phoneme` `break` `audio` `prosody` `say-as` `mstts:express-as`Element může obsahovat text a následující prvky:,,,,, a`sub`. `s`

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

## <a name="use-phonemes-to-improve-pronunciation"></a>Použití fonémy ke zlepšení výslovnosti

`ph` Prvek se používá pro fonetickou výslovnost v dokumentech SSML. `ph` Element může obsahovat pouze text, žádné jiné prvky. V případě záložního převodu vždy poskytněte humánní řeč čitelný.

Fonetické abecedy se skládají z telefonů, které jsou tvořeny písmeny, číslicemi nebo znaky, někdy v kombinaci. Každý telefon popisuje jedinečný zvuk řeči. To je na rozdíl od abecedy latinky, kde jakékoli písmeno může představovat více mluvených zvuků. Vezměte v úvahu různé výslovnosti písmena "c" ve slově "Candy" a "pozastaveno", nebo na rozdíl od kombinace písmen "th" v slovech "věc" a "ty".

**Syntaxe**

```XML
<phoneme alphabet="string" ph="string"></phoneme>
```

**Atributy**

| Atribut | Popis | Povinné / volitelné |
|-----------|-------------|---------------------|
| abecedy | Určuje fonetickou abecedu, která se použije při syntetizování výslovnosti řetězce v `ph` atributu. Řetězec určující abecedu musí být zadán malými písmeny. Níže jsou uvedené možné abecedy, které můžete zadat.<ul><li>Mezinárodní &ndash; fonetická abeceda IPA</li><li>telefonní &ndash; sada rozhraní API pro rozpoznávání řeči SAPI</li><li>Univerzální &ndash; sada telefonů UPS</li></ul>Abeceda se vztahuje pouze na foném v elementu. Další informace najdete v referenčních informacích o [fonetické abecedě](https://msdn.microsoft.com/library/hh362879(v=office.14).aspx). | volitelná, |
| 6,0 | Řetězec obsahující telefony, které určují výslovnost slova v `phoneme` prvku. Pokud zadaný řetězec obsahuje nerozpoznané telefony, služba převod textu na mluvené slovo (TTS) odmítne celý dokument SSML a vytvoří žádný z výstupů řeči zadaného v dokumentu. | Vyžaduje se, pokud používáte fonémy. |

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

## <a name="adjust-prosody"></a>Upravit Prosody

`prosody` Element se používá k zadání změn sklonu, Countour, rozsahu, míry, trvání a objemu pro výstup textu na řeč. `phoneme` `p` `break` `audio` `say-as` Elementmůže`s`obsahovat text a následující prvky:,,, `prosody`,, ,`sub`a. `prosody`

Vzhledem k tomu, že se hodnoty atributů Prozodický předěl můžou v rámci široké škály lišit, překladač řeči interpretuje přiřazené hodnoty jako návrh toho, co by měly být aktuální hodnoty Prozodický předěl vybraného hlasu. Služba převod textu na řeč omezuje nebo nahrazuje hodnoty, které nejsou podporovány. Příklady nepodporovaných hodnot jsou výškou 1 MHz nebo 120.

**Syntaxe**

```XML
<prosody pitch="value" contour="value" range="value" rate="value" duration="value" volume="value"></prosody>
```

**Atributy**

| Atribut | Popis | Povinné / volitelné |
|-----------|-------------|---------------------|
| teče | Určuje rozteč účaří pro text. Rozteč můžete vyjádřit jako:<ul><li>Absolutní hodnota vyjádřená jako číslo následovaný "Hz" (Hz). Například 600Hz.</li><li>Relativní hodnota vyjádřená jako číslo před "+" nebo "-" a následována "Hz" nebo "St", která určuje velikost pro změnu rozteči. Například: + 80Hz nebo-2st. "St" značí, že se jednotka změny semitone, což je polovina tónu (poloviční krok) na standardním diatonic škále.</li><li>Konstantní hodnota:<ul><li>x – nízká</li><li>nízká</li><li>středně silné</li><li>maximální</li><li>x-vysoká</li><li>default</li></ul></li></ul>. | volitelná, |
| nesmí | Pro hlasy neuronové se nepodporuje obrys. Obrys představuje změny v rozteči pro obsah mluveného slova jako pole cílů v zadaných časových pozicích ve výstupu řeči. Každý cíl je definován sadami dvojic parametrů. Příklad: <br/><br/>`<prosody contour="(0%,+20Hz) (10%,-2st) (40%,+10Hz)">`<br/><br/>První hodnota v každé sadě parametrů určuje umístění změny sklonu v procentech doby trvání textu. Druhá hodnota určuje velikost, která má zvýšit nebo snížit rozteč, pomocí relativní hodnoty nebo hodnoty výčtu pro rozteč (viz `pitch`). | volitelná, |
| oblasti  | Hodnota, která představuje rozsah roztečí textu. Můžete vyjádřit `range` použití stejných absolutních hodnot, relativních hodnot nebo hodnot výčtu používaných k popisu `pitch`. | volitelná, |
| kmitočt  | Určuje míru projevení textu. Můžete vyjádřit `rate` jako:<ul><li>Relativní hodnota vyjádřená jako číslo, které funguje jako násobitel výchozí hodnoty. Například hodnota *1* má za následek nezměněnou sazbu. Hodnota *0,5* vede k poloviční míře. Hodnota *3* má za následek cestu k této sazbě.</li><li>Konstantní hodnota:<ul><li>x – pomalé</li><li>pomalé</li><li>středně silné</li><li>světl</li><li>x – Fast</li><li>default</li></ul></li></ul> | volitelná, |
| duration  | Časový interval, který by měl uplynout, zatímco služba rozpoznávání řeči (TTS) čte text v sekundách nebo milisekundách. Například *2S* nebo *1800ms*. | volitelná, |
| svazek  | Určuje úroveň hlasitosti mluveného hlasu. Svazek můžete vyjádřit jako:<ul><li>Absolutní hodnota vyjádřená jako číslo v rozsahu od 0,0 do 100,0, od Tichého po  nahlasu . Například 75. Výchozí hodnota je 100,0.</li><li>Relativní hodnota vyjádřená jako číslo začínající znakem "+" nebo "-", která určuje velikost pro změnu svazku. Například + 10 nebo-5,5.</li><li>Konstantní hodnota:<ul><li>tich</li><li>× – měkké</li><li>Pohyblivý</li><li>středně silné</li><li>rovnává</li><li>x-nahlas</li><li>default</li></ul></li></ul> | volitelná, |

### <a name="change-speaking-rate"></a>Mluvy frekvence změny

Míru speakace lze použít na standardní hlasy na úrovni slova nebo věty. Vzhledem k tomu, že je možné použít pouze hlasy neuronové na úrovni věty.

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

Změny svazku lze použít na standardní hlasy na úrovni slova nebo na úrovni věty. Změny svazku se dají použít jenom na hlasy neuronové na úrovni věty.

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

Změny v rozteči je možné použít u standardních hlasů na úrovni slova nebo věty. Vzhledem k tomu, že změny v sklonu se dají použít jenom na hlasy neuronové na úrovni věty.

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
> Změny rozvrhu stoupání nejsou podporované neuronové hlasy.

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

## <a name="add-recorded-audio"></a>Přidat zaznamenaný zvuk

`audio`je volitelný prvek, který umožňuje vložit zvuk MP3 do dokumentu SSML. Tělo zvukového prvku může obsahovat prostý text nebo SSML poznámky, které jsou mluvené, pokud je zvukový soubor nedostupný nebo nezobrazitelný. `audio` Kromě toho element může obsahovat text a následující prvky: `phoneme` `p` `audio`, `break`,, `s`,, `prosody` `say-as` ,`sub`a.

Libovolný zvuk zahrnutý v dokumentu SSML musí splňovat tyto požadavky:

* MP3 musí být hostovaný na koncovém bodu HTTPS, který je přístupný pro Internet. Je vyžadován protokol HTTPS a doména hostující soubor MP3 musí představovat platný důvěryhodný certifikát SSL.
* MP3 musí být platný soubor MP3 (MPEG v2).
* Přenosová rychlost musí být 48 KB/s.
* Vzorkovací frekvence musí být 16000 Hz.
* Celková celková doba pro všechny textové a zvukové soubory v jedné odpovědi nesmí překročit 90 (90) sekund.
* MP3 nesmí obsahovat žádné informace specifické pro zákazníka nebo jiné citlivé informace.

**Syntaxe**

```xml
<audio src="string"/></audio>
```

**Atributy**

| Atribut | Popis | Povinné / volitelné |
|-----------|-------------|---------------------|
| src | Určuje umístění nebo adresu URL zvukového souboru. | Požadováno při použití prvku zvuk v dokumentu SSML. |

**Příklad**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <p>
        <audio src="https://contoso.com/opinionprompt.wav"/>
        Thanks for offering your opinion. Please begin speaking after the beep.
        <audio src="https://contoso.com/beep.wav">
        Could not play the beep, please voice your opinion now. </audio>
    </p>
</speak>
```

## <a name="add-background-audio"></a>Přidat zvuk na pozadí

`mstts:backgroundaudio` Element umožňuje přidat do dokumentů SSML zvuk na pozadí (nebo míchat zvukový soubor s převodem textu na řeč). Díky `mstts:backgroundaudio` tomu můžete na pozadí zacyklovat zvukový soubor, mizet na začátku převodu textu na řeč a zeslabit na konci převodu textu na řeč.

Pokud je zadaný zvuk na pozadí kratší než převod textu na řeč nebo zeslabit, smyčka se spustí. Pokud je delší než převod textu na řeč, zastaví se, až se rozzvolna dokončí.

V SSML dokumentu je povolen pouze jeden zvukový soubor na pozadí. Můžete `audio` však`voice` v rámci elementu doplnit značky přidáním dalšího zvuku do dokumentu SSML.

**Syntaxe**

```XML
<mstts:backgroundaudio src="string" volume="string" fadein="string" fadeout="string"/>
```

**Atributy**

| Atribut | Popis | Povinné / volitelné |
|-----------|-------------|---------------------|
| src | Určuje umístění nebo adresu URL zvukového souboru na pozadí. | Vyžaduje se, pokud v dokumentu SSML používáte zvuk na pozadí. |
| svazek | Určuje hlasitost zvukového souboru na pozadí. **Přijaté hodnoty**: `0` na `100` včetně Výchozí hodnota je `1`. | volitelná, |
| fadein | Určuje dobu, po kterou se bude objevovat zvuk na pozadí. **Přijaté hodnoty**: `0` na `10000` včetně  | volitelná, |
| zeslabení | Určuje dobu, po kterou se má zvuk na pozadí rozmizet. **Přijaté hodnoty**: `0` na `10000` včetně  | volitelná, |

**Příklad**

```xml
<speak version="1.0" xml:lang="en-US" xmlns:mstts="http://www.w3.org/2001/mstts">
    <mstts:backgroundaudio src="https://contoso.com/sample.wav" volume="0.7" fadein="3000" fadeout="4000"/>
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)">
        The text provided in this document will be spoken over the background audio.
    </voice>
</speak>
```

## <a name="next-steps"></a>Další postup

* [Podpora jazyků: hlasy, národní prostředí, jazyky](language-support.md)
