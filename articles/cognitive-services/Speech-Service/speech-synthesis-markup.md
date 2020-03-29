---
title: Jazyk značek syntézy řeči (SSML) – služba řeči
titleSuffix: Azure Cognitive Services
description: Použití jazyka značek syntézy řeči k řízení výslovnosti a prosody v převodu textu na řeč.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: dapine
ms.openlocfilehash: 7d5dd79399b15ade90173a55aeb71dacbc61fa78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80365816"
---
# <a name="improve-synthesis-with-speech-synthesis-markup-language-ssml"></a>Zlepšete syntézu pomocí jazyka s poznámkami o syntéze řeči (SSML)

Jazyk značek syntézy řeči (SSML) je značkovací jazyk založený na jazyce XML, který umožňuje vývojářům určit, jak je vstupní text převeden na syntetizovaný projev pomocí služby převodu textu na řeč. Ve srovnání s prostým textem umožňuje SSML vývojářům doladit výšku, výslovnost, rychlost mluvení, hlasitost a další výstup převodu textu na řeč. Normální interpunkce, například pozastavení po určité době nebo použití správné intonace, když věta končí otazníkem, jsou automaticky zpracovány.

Implementace služby Speech služby SSML je založena na [jazyce pro poznámky pro rozpoznávání řeči](https://www.w3.org/TR/speech-synthesis)konsorcia World Wide Web Consortium verze 1.0 .

> [!IMPORTANT]
> Čínské, japonské a korejské znaky se při fakturaci počítají jako dva znaky. Další informace naleznete v [tématu Ceny](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

## <a name="standard-neural-and-custom-voices"></a>Standardní, neurální a vlastní hlasy

Vyberte si ze standardních a neurálních hlasů, nebo si vytvořte vlastní hlas jedinečný pro váš produkt nebo značku. Více než 75 standardních hlasů je k dispozici ve více než 45 jazycích a národních prostředích a 5 neurálních hlasů je k dispozici ve čtyřech jazycích a národních prostředích. Úplný seznam podporovaných jazyků, národních prostředí a hlasů (neurální a standardní) naleznete v [tématu podpora jazyka](language-support.md).

Další informace o standardních, neurálních a vlastních hlasech najdete v [tématu Přehled převodu textu na řeč](text-to-speech.md).

## <a name="special-characters"></a>Speciální znaky

Při používání SSML, mějte na paměti, že speciální znaky, jako jsou uvozovky, apostrofy a závorky musí být uvozeny. Další informace naleznete [v tématu Extensible Markup Language (XML) 1.0: Dodatek D](https://www.w3.org/TR/xml/#sec-entexpand).

## <a name="supported-ssml-elements"></a>Podporované prvky SSML

Každý dokument SSML je vytvořen pomocí prvků SSML (nebo tagů). Tyto prvky se používají k nastavení rozteče, prozody, objemu a dalších. V následujících částech jsou podrobně popsány informace o tom, jak se jednotlivé prvky používají a kdy je prvek povinný nebo volitelný.  

> [!IMPORTANT]
> Nezapomeňte použít dvojité uvozovky kolem hodnot atributů. Standardy pro dobře tvarované platné hodnoty XML vyžadují, aby byly hodnoty atributů uzavřeny v uvozovkách. Například `<prosody volume="90">` je dobře tvarovaný, platný `<prosody volume=90>` prvek, ale není. SSML nemusí rozpoznat hodnoty atributů, které nejsou v uvozovkách.

## <a name="create-an-ssml-document"></a>Vytvoření dokumentu SSML

`speak`je kořenový prvek a je **vyžadován** pro všechny dokumenty SSML. Prvek `speak` obsahuje důležité informace, jako je například verze, jazyk a definice slovníku značek.

**Syntaxe**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="string"></speak>
```

**Atributy**

| Atribut | Popis | Povinné / Nepovinné |
|-----------|-------------|---------------------|
| `version` | Označuje verzi specifikace SSML použitou k interpretaci značek dokumentu. Aktuální verze je 1.0. | Požaduje se |
| `xml:lang` | Určuje jazyk kořenového dokumentu. Hodnota může obsahovat malý dvoupísmenný kód jazyka `en`(například ) nebo kód jazyka a zemi/oblast velkých písmen (například `en-US`). | Požaduje se |
| `xmlns` | Určuje identifikátor URI dokumentu, který definuje slovník značek (typy prvků a názvy atributů) dokumentu SSML. Aktuální identifikátor http://www.w3.org/2001/10/synthesisURI je . | Požaduje se |

## <a name="choose-a-voice-for-text-to-speech"></a>Volba hlasu pro převod textu na řeč

Prvek `voice` je povinný. Používá se k určení hlasu, který se používá pro převod textu na řeč.

**Syntaxe**

```xml
<voice name="string">
    This text will get converted into synthesized speech.
</voice>
```

**Atributy**

| Atribut | Popis | Povinné / Nepovinné |
|-----------|-------------|---------------------|
| `name` | Identifikuje hlas používaný pro výstup převodu textu na řeč. Úplný seznam podporovaných hlasů naleznete v [tématu Language support](language-support.md#text-to-speech). | Požaduje se |

**Příklad**

> [!NOTE]
> Tento příklad `en-US-AriaRUS` používá hlas. Úplný seznam podporovaných hlasů naleznete v [tématu Language support](language-support.md#text-to-speech).

```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        This is the text that is spoken.
    </voice>
</speak>
```

## <a name="use-multiple-voices"></a>Použití více hlasů

V `speak` rámci prvku můžete určit více hlasů pro výstup převodu textu na řeč. Tyto hlasy mohou být v různých jazycích. Pro každý hlas musí být text `voice` zabalen do prvku. 

**Atributy**

| Atribut | Popis | Povinné / Nepovinné |
|-----------|-------------|---------------------|
| `name` | Identifikuje hlas používaný pro výstup převodu textu na řeč. Úplný seznam podporovaných hlasů naleznete v [tématu Language support](language-support.md#text-to-speech). | Požaduje se |

> [!IMPORTANT]
> Více hlasů je nekompatibilních s funkcí hranice slova. Funkce hranice slova musí být zakázána, aby bylo možné použít více hlasů.

### <a name="disable-word-boundary"></a>Zakázat hranici slova

V závislosti na jazyce Speech SDK `"SpeechServiceResponse_Synthesis_WordBoundaryEnabled"` nastavíte vlastnost `false` `SpeechConfig` na instanci objektu.

# <a name="c"></a>[C #](#tab/csharp)

Další informace naleznete <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.setproperty?view=azure-dotnet" target="_blank"> `SetProperty` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>v tématu .

```csharp
speechConfig.SetProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="c"></a>[C++](#tab/cpp)

Další informace naleznete <a href="https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setproperty" target="_blank"> `SetProperty` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>v tématu .

```cpp
speechConfig->SetProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="java"></a>[Java](#tab/java)

Další informace naleznete <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setproperty?view=azure-java-stable#com_microsoft_cognitiveservices_speech_SpeechConfig_setProperty_String_String_" target="_blank"> `setProperty` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>v tématu .

```java
speechConfig.setProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="python"></a>[Python](#tab/python)

Další informace naleznete <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#set-property-by-name-property-name--str--value--str-" target="_blank"> `set_property_by_name` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>v tématu .

```python
speech_config.set_property_by_name(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

Další informace naleznete <a href="https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest#setproperty-string--string-" target="_blank"> `setProperty` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>v tématu .

```javascript
speechConfig.setProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="objective-c"></a>[Objective-C](#tab/objectivec)

Další informace naleznete <a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechconfiguration#setpropertytobyname" target="_blank"> `setPropertyTo` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>v tématu .

```objectivec
[speechConfig setPropertyTo:@"false" byName:@"SpeechServiceResponse_Synthesis_WordBoundaryEnabled"];
```

# <a name="swift"></a>[Swift](#tab/swift)

Další informace naleznete <a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechconfiguration#setpropertytobyname" target="_blank"> `setPropertyTo` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>v tématu .

```swift
speechConfig!.setPropertyTo(
    "false", byName: "SpeechServiceResponse_Synthesis_WordBoundaryEnabled")
```

---

**Příklad**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        Good morning!
    </voice>
    <voice name="en-US-Guy24kRUS">
        Good morning to you too Aria!
    </voice>
</speak>
```

## <a name="adjust-speaking-styles"></a>Úprava stylů mluvení

> [!IMPORTANT]
> Úprava mluvených stylů bude fungovat pouze s neurálními hlasy.

Ve výchozím nastavení služba převodu textu na řeč syntetizuje text pomocí neutrálního stylu mluvení pro standardní i neurální hlasy. S neurální hlasy, můžete upravit styl mluvení vyjádřit veselí, empatie, nebo sentiment s elementem. `<mstts:express-as>` Toto je volitelný prvek jedinečný pro službu Speech.

V současné době jsou podporovány úpravy stylu mluvení pro tyto nervové hlasy:
* `en-US-AriaNeural`
* `zh-CN-XiaoxiaoNeural`
* `pt-BR-FranciscaNeural`

Změny se použijí na úrovni věty a styl se liší podle hlasu. Pokud styl není podporován, služba vrátí řeč ve výchozím neutrálním stylu mluvení.

**Syntaxe**

```xml
<mstts:express-as style="string"></mstts:express-as>
```

**Atributy**

| Atribut | Popis | Povinné / Nepovinné |
|-----------|-------------|---------------------|
| `style` | Určuje styl mluvení. V současné době jsou styly mluvení specifické pro hlas. | Povinné při úpravě stylu mluvení pro neurální hlas. Pokud `mstts:express-as`používáte , musí být k dispozici styl. Pokud je k dispozici neplatná hodnota, bude tento prvek ignorován. |

V této tabulce můžete určit, které styly mluvení jsou podporovány pro každý neurální hlas.

| Hlas | Styl | Popis |
|-------|------|-------------|
| `en-US-AriaNeural` | `style="newscast"` | Vyjadřuje formální a profesionální tón pro vyprávění novinek |
| | `style="customerservice"` | Vyjadřuje přátelský a užitečný tón pro zákaznickou podporu |
| | `style="chat"` | Vyjadřuje ležérní a uvolněný tón |
| | `style="cheerful"` | Vyjadřuje pozitivní a šťastný tón |
| | `style="empathetic"` | Vyjadřuje pocit péče a porozumění |
| `zh-CN-XiaoxiaoNeural` | `style="newscast"` | Vyjadřuje formální a profesionální tón pro vyprávění novinek |
| | `style="customerservice"` | Vyjadřuje přátelský a užitečný tón pro zákaznickou podporu |
| | `style="assistant"` | Vyjadřuje hřejivý a uvolněný tón pro digitální asistenty  |
| | `style="lyrical"` | Vyjadřuje emoce melodickým a sentimentálním způsobem |
| `pt-BR-FranciscaNeural` | `style="cheerful"` | Vyjadřuje pozitivní a šťastný tón |

**Příklad**

Tento úryvek SSML znázorňuje, `<mstts:express-as>` jak se prvek `cheerful`používá ke změně stylu mluvení na .

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
       xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        <mstts:express-as style="cheerful">
            That'd be just amazing!
        </mstts:express-as>
    </voice>
</speak>
```

## <a name="add-or-remove-a-breakpause"></a>Přidání nebo odebrání přestávky/pozastavení

Pomocí `break` prvku můžete mezi slova vložit pauzy (nebo přestávky) nebo zabránit pozastavení automaticky přidanéslužbou převodu textu na řeč.

> [!NOTE]
> Tento prvek slouží k přepsání výchozího chování převodu textu na řeč (TTS) pro slovo nebo frázi, pokud syntetizovaný projev pro toto slovo nebo frázi zní nepřirozeně. `strength` Nastavte, `none` chcete-li zabránit prozodické přerušení, který je automaticky vložen službou převodu textu na řeč.

**Syntaxe**

```xml
<break strength="string" />
<break time="string" />
```

**Atributy**

| Atribut | Popis | Povinné / Nepovinné |
|-----------|-------------|---------------------|
| `strength` | Určuje relativní trvání pauzy pomocí jedné z následujících hodnot:<ul><li>Žádná</li><li>x-slabý</li><li>Slabý</li><li>střední (výchozí)</li><li>Silné</li><li>x-silný</li></ul> | Nepovinné |
| `time` | Určuje absolutní dobu trvání pauzy v sekundách nebo milisekundách. Příklady platných `2s` hodnot jsou a`500` | Nepovinné |

| Sílu | Popis |
|----------|-------------|
| Žádná, nebo pokud není poskytnuta žádná hodnota | 0 ms |
| x-slabý | 250 ms |
| Slabý | 500 ms |
| střední | 750 ms |
| Silné | 1000 ms |
| x-silný | 1250 ms |


**Příklad**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
    </voice>
</speak>
```

## <a name="specify-paragraphs-and-sentences"></a>Určení odstavců a vět

`p`a `s` prvky se používají k označení odstavců a vět. V případě, že tyto prvky, služba převodu textu na řeč automaticky určuje strukturu dokumentu SSML.

Prvek `p` může obsahovat text a `audio` `break`následující `phoneme` `prosody`prvky: , , , `say-as`, `sub`, `mstts:express-as`, , a `s`.

Prvek `s` může obsahovat text a `audio` `break`následující `phoneme` `prosody`prvky: , , , `say-as`, `mstts:express-as`, , a `sub`.

**Syntaxe**

```XML
<p></p>
<s></s>
```

**Příklad**

```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
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

## <a name="use-phonemes-to-improve-pronunciation"></a>Použití fonýžů ke zlepšení výslovnosti

Prvek `ph` se používá pro fonetické výslovnosti v dokumentech SSML. Prvek `ph` může obsahovat pouze text, žádné další prvky. Vždy poskytují lidsky čitelné řeči jako záložní.

Fonetická abeceda se skládá z telefonů, které se skládají z písmen, čísel nebo znaků, někdy v kombinaci. Každý telefon popisuje jedinečný zvuk řeči. To je na rozdíl od latinské abecedy, kde každé písmeno může představovat více mluvených zvuků. Vezměme si různé výslovnosti písmene "c" ve slovech "cukroví" a "přestat", nebo různé výslovnosti kombinace písmen "th" ve slovech "věc" a "ty".

**Syntaxe**

```XML
<phoneme alphabet="string" ph="string"></phoneme>
```

**Atributy**

| Atribut | Popis | Povinné / Nepovinné |
|-----------|-------------|---------------------|
| `alphabet` | Určuje fonetickou abecedu, která se má použít při syntéze výslovnosti řetězce v atributu. `ph` Řetězec určující abecedu musí být určen velkými písmeny. Níže jsou uvedeny možné abecedy, které můžete zadat.<ul><li>`ipa`&ndash; <a href="https://en.wikipedia.org/wiki/International_Phonetic_Alphabet" target="_blank">Mezinárodní fonetická abeceda <span class="docon docon-navigate-external x-hidden-focus"></span> </a></li><li>`sapi`&ndash; [Fonetická abeceda služby pro rozpoznávání řeči](speech-ssml-phonetic-sets.md)</li><li>`ups`&ndash; Univerzální telefonní sada</li></ul><br>Abeceda se `phoneme` vztahuje pouze na v elementu.. | Nepovinné |
| `ph` | Řetězec obsahující telefony, které určují výslovnost slova `phoneme` v prvku. Pokud zadaný řetězec obsahuje nerozpoznané telefony, služba převodu textu na řeč (TTS) odmítne celý dokument SSML a nevytvoří žádný výstup řeči zadaný v dokumentu. | Povinné při použití fonýžů. |

**Příklady**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <phoneme alphabet="sapi" ph="iy eh n y uw eh s"> en-US </phoneme>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <s>His name is Mike <phoneme alphabet="ups" ph="JH AU"> Zhou </phoneme></s>
    </voice>
</speak>
```

## <a name="use-custom-lexicon-to-improve-pronunciation"></a>Použití vlastního lexikonu ke zlepšení výslovnosti

Někdy TTS nemůže přesně vyslovit slovo, například název společnosti nebo cizí jméno. Vývojáři mohou definovat čtení těchto entit `phoneme` v `sub` SSML pomocí a značky nebo definovat čtení více `lexicon` entit odkazem na vlastní soubor lexikon pomocí značky.

**Syntaxe**

```XML
<lexicon uri="string"/>
```

**Atributy**

| Atribut | Popis | Povinné / Nepovinné |
|-----------|-------------|---------------------|
| `uri` | Adresa externího dokumentu PLS. | Povinná hodnota. |

**Použití**

Krok 1: Definování vlastního lexikonu 

Čtení entit můžete definovat podle seznamu vlastních položek lexikonu, které jsou uloženy jako soubor XML nebo PLS.

**Příklad**

```xml
<?xml version="1.0" encoding="UTF-16"?>
<lexicon version="1.0" 
      xmlns="http://www.w3.org/2005/01/pronunciation-lexicon"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
      xsi:schemaLocation="http://www.w3.org/2005/01/pronunciation-lexicon 
        http://www.w3.org/TR/2007/CR-pronunciation-lexicon-20071212/pls.xsd"
      alphabet="ipa" xml:lang="en-US">
  <lexeme>
    <grapheme>BTW</grapheme> 
    <alias>By the way</alias> 
  </lexeme>
  <lexeme>
    <grapheme> Benigni </grapheme> 
    <phoneme> bɛˈniːnji</phoneme>
  </lexeme>
</lexicon>
```

Každý `lexeme` prvek je položka lexikon. `grapheme`obsahuje text popisující ortograf `lexeme`. Formulář pro odečtu `alias`lze poskytnout jako . Telefonní řetězec může být `phoneme` k dispozici v elementu.

Prvek `lexicon` obsahuje alespoň `lexeme` jeden prvek. Každý `lexeme` prvek obsahuje `grapheme` alespoň jeden prvek `grapheme` `alais`a `phoneme` jeden nebo více , a prvky. Prvek `grapheme` obsahuje text popisující <a href="https://www.w3.org/TR/pronunciation-lexicon/#term-Orthography" target="_blank">ortografii <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>. Prvky `alias` se používají k označení výslovnosti zkratky nebo zkráceného termínu. Prvek `phoneme` obsahuje text popisující, `lexeme` jak je vyslovováno.

Další informace o vlastním souboru lexikon, naleznete [v části Výslovnost Lexicon Specifikace (PLS) verze 1.0](https://www.w3.org/TR/pronunciation-lexicon/) na webu W3C.

Krok 2: Nahrajte vlastní lexikon soubor vytvořený v kroku 1 online, můžete ho uložit kdekoli a doporučujeme ho uložit do Microsoft Azure, například [Azure Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal).

Krok 3: Podívejte se na vlastní soubor lexikon v SSML

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" 
          xmlns:mstts="http://www.w3.org/2001/mstts" 
          xml:lang="en-US">
<lexicon uri="http://www.example.com/customlexicon.xml"/>
BTW, we will be there probably 8:00 tomorrow morning.
Could you help leave a message to Robert Benigni for me?
</speak>
```
"BTW" bude číst jako "Mimochodem". "Benigni" bude číst s poskytnutým IPA "b中ninji".  

**Omezení**
- Velikost souboru: maximální limit velikosti souboru lexikon je 100 kB, pokud přesahuje tuto velikost, požadavek na syntézu se nezdaří.
- Aktualizace mezipaměti Lexicon: vlastní lexikon bude uložen do mezipaměti s URI jako klíčem ve službě TTS při prvním načtení. Lexikon se stejným identifikátorem URI nebude znovu načten do 15 minut, takže změna vlastního lexikonu musí počkat maximálně 15 minut, než se projeví.

**Fonetické sady služby rozpoznávání řeči**

Ve výše uvedeném příkladu používáme mezinárodní fonetickou abecedu, známou také jako telefonní sada IPA. Doporučujeme vývojářům používat IPA, protože se jedná o mezinárodní standard. Vzhledem k tomu, že nPP není snadno zapamatovatelná, definuje`en-US`služba `fr-FR` `de-DE`Speech `es-ES` `ja-JP`fotickou sadu pro sedm jazyků ( , , , `zh-CN`, , a `zh-TW`).

Jako údolí `sapi` můžete použít jako `alphabet` vale pro atribut s vlastními lexikonami, jak je znázorněno níže:

```xml
<?xml version="1.0" encoding="UTF-16"?>
<lexicon version="1.0" 
      xmlns="http://www.w3.org/2005/01/pronunciation-lexicon"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://www.w3.org/2005/01/pronunciation-lexicon
        http://www.w3.org/TR/2007/CR-pronunciation-lexicon-20071212/pls.xsd"
      alphabet="sapi" xml:lang="en-US">
  <lexeme>
    <grapheme>BTW</grapheme>
    <alias> By the way </alias>
  </lexeme>
  <lexeme>
    <grapheme> Benigni </grapheme>
    <phoneme> b eh 1 - n iy - n y iy </phoneme>
  </lexeme>
</lexicon>
```

Další informace o podrobné foselé abecedy služby Speech service naleznete v [fosetech služby Speech](speech-ssml-phonetic-sets.md).

## <a name="adjust-prosody"></a>Upravit prosody

Prvek `prosody` se používá k určení změn rozteče, obrysu, rozsahu, rychlosti, doby trvání a objemu pro výstup převodu textu na řeč. Prvek `prosody` může obsahovat text a `audio` `break`následující `p` `phoneme`prvky: , , , `prosody`, `say-as`, `sub`, , a `s`.

Vzhledem k tomu, že hodnoty prozodických atributů se mohou v širokém rozsahu lišit, interpretuje nástroj pro rozpoznávání řeči přiřazené hodnoty jako návrh toho, jaké by měly být skutečné prozodické hodnoty vybraného hlasu. Služba převodu textu na řeč omezuje nebo nahrazuje hodnoty, které nejsou podporovány. Příklady nepodporovaných hodnot jsou rozteč 1 MHz nebo objem 120.

**Syntaxe**

```XML
<prosody pitch="value" contour="value" range="value" rate="value" duration="value" volume="value"></prosody>
```

**Atributy**

| Atribut | Popis | Povinné / Nepovinné |
|-----------|-------------|---------------------|
| `pitch` | Označuje rozteč účaří textu. Rozteč můžete vyjádřit takto:<ul><li>Absolutní hodnota vyjádřená jako číslo následovaná "Hz" (Hertz). Například 600 Hz.</li><li>Relativní hodnota, vyjádřená jako číslo předchází "+" nebo "-" a následuje "Hz" nebo "st", která určuje částku pro změnu výšky. Například: +80 Hz nebo -2st. "St" označuje, že jednotka změny je polotón, což je polovina tónu (půl kroku) na standardní diatonické stupnici.</li><li>Konstantní hodnota:<ul><li>x-nízká</li><li>Nízké</li><li>střední</li><li>high</li><li>x-vysoká</li><li>default</li></ul></li></ul>. | Nepovinné |
| `contour` | Contour není podporovánpro nervové hlasy. Obrys představuje změny rozteče. Tyto změny jsou reprezentovány jako pole cílů v určených časových pozicích ve výstupu řeči. Každý cíl je definován sadami dvojic parametrů. Například: <br/><br/>`<prosody contour="(0%,+20Hz) (10%,-2st) (40%,+10Hz)">`<br/><br/>První hodnota v každé sadě parametrů určuje umístění změny rozteče jako procento doby trvání textu. Druhá hodnota určuje částku pro zvýšení nebo snížení rozteče pomocí relativní hodnoty nebo `pitch`hodnoty výčtu pro rozteč (viz). | Nepovinné |
| `range` | Hodnota, která představuje rozsah rozteče pro text. Můžete vyjádřit `range` pomocí stejné absolutní hodnoty, relativní hodnoty nebo výčtu hodnoty používané k popisu `pitch`. | Nepovinné |
| `rate` | Označuje rychlost mluvení textu. Můžete vyjádřit `rate` jako:<ul><li>Relativní hodnota vyjádřená jako číslo, které funguje jako multiplikátor výchozí hodnoty. Například hodnota *1* má za následek žádnou změnu sazby. Hodnota *0,5* má za následek snížení sazby na polovinu. Hodnota *3* má za následek ztrojnásobení sazby.</li><li>Konstantní hodnota:<ul><li>x-pomalé</li><li>Pomalé</li><li>střední</li><li>Rychle</li><li>x-rychlé</li><li>default</li></ul></li></ul> | Nepovinné |
| `duration` | Doba, která by měla uplynout, zatímco služba syntézy řeči (TTS) čte text v sekundách nebo milisekundách. Například *2s* nebo *1800ms*. | Nepovinné |
| `volume` | Označuje úroveň hlasitosti mluvícího hlasu. Hlasitost můžete vyjádřit takto:<ul><li>Absolutní hodnota vyjádřená jako číslo v rozsahu 0,0 až 100,0, od *nejtišší* po *nejhlasitější*. Například 75. Výchozí hodnota je 100.0.</li><li>Relativní hodnota vyjádřená jako číslo předchází "+" nebo "-", která určuje částku pro změnu svazku. Například +10 nebo -5,5.</li><li>Konstantní hodnota:<ul><li>Tichý</li><li>x-soft</li><li>Měkké</li><li>střední</li><li>Hlasité</li><li>x-hlasitý</li><li>default</li></ul></li></ul> | Nepovinné |

### <a name="change-speaking-rate"></a>Změnit rychlost mluvení

Rychlost mluvení lze použít na standardní hlasy na úrovni slova nebo věty. Zatímco rychlost mluvení může být použita pouze na nervové hlasy na úrovni věty.

**Příklad**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Guy24kRUS">
        <prosody rate="+30.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-volume"></a>Změna hlasitosti

Změny hlasitosti lze použít u standardních hlasů na úrovni slova nebo věty. Zatímco změny hlasitosti lze použít pouze na nervové hlasy na úrovni věty.

**Příklad**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <prosody volume="+20.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-pitch"></a>Změna rozteče

Změny rozteče lze použít u standardních hlasů na úrovni slova nebo věty. Zatímco změny výšky lze aplikovat pouze na nervové hlasy na úrovni věty.

**Příklad**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Guy24kRUS">
        Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
    </voice>
</speak>
```

### <a name="change-pitch-contour"></a>Změna obrysu rozteče

> [!IMPORTANT]
> Změny obrysů stoupání nejsou podporovány neurálními hlasy.

**Příklad**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <prosody contour="(80%,+20%) (90%,+30%)" >
            Good morning.
        </prosody>
    </voice>
</speak>
```
## <a name="say-as-element"></a>říci jako prvek

`say-as`je volitelný prvek, který označuje typ obsahu (například číslo nebo datum) textu prvku. To poskytuje pokyny pro modul syntézy řeči o tom, jak vyslovit text.

**Syntaxe**

```XML
<say-as interpret-as="string" format="digit string" detail="string"> <say-as>
```

**Atributy**

| Atribut | Popis | Povinné / Nepovinné |
|-----------|-------------|---------------------|
| `interpret-as` | Označuje typ obsahu textu prvku. Seznam typů naleznete v následující tabulce. | Požaduje se |
| `format` | Obsahuje další informace o přesném formátování textu prvku pro typy obsahu, které mohou mít nejednoznačné formáty. SSML definuje formáty pro typy obsahu, které je používají (viz tabulka níže). | Nepovinné |
| `detail` | Označuje úroveň podrobností, které mají být vysloveny. Tento atribut může například požadovat, aby modul pro syntézu řeči vyslovoval interpunkční znaménka. Pro soubor nejsou definovány žádné standardní `detail`hodnoty. | Nepovinné |

<!-- I don't understand the last sentence. Don't we know which one Cortana uses? -->

Následují podporované typy obsahu pro `interpret-as` `format` atributy a. Atribut `format` zahrňte pouze v případě, že `interpret-as` je nastaven na datum a čas.

| interpretovat-jako | formát | Interpretace |
|--------------|--------|----------------|
| `address` | | Text je vyslovován jako adresa. Modul pro syntézu řeči vyslovuje:<br /><br />`I'm at <say-as interpret-as="address">150th CT NE, Redmond, WA</say-as>`<br /><br />Jako "Jsem na 150. |
| `cardinal`, `number` | | Text je mluvený jako kardinální číslo. Modul pro syntézu řeči vyslovuje:<br /><br />`There are <say-as interpret-as="cardinal">3</say-as> alternatives`<br /><br />Jako "Existují tři alternativy." |
| `characters`, `spell-out` | | Text je vysloven jako jednotlivá písmena (hláskovaná). Modul pro syntézu řeči vyslovuje:<br /><br />`<say-as interpret-as="characters">test</say-as>`<br /><br />Jako "T E S T." |
| `date` | dmy, mdy, ymd, ydm, ym, můj, md, dm, d, m, y | Text je mluvený jako datum. Atribut `format` určuje formát data (*d=day, m=month a y=year*). Modul pro syntézu řeči vyslovuje:<br /><br />`Today is <say-as interpret-as="date" format="mdy">10-19-2016</say-as>`<br /><br />Jako "Dnes je říjen devatenácté dva tisíce šestnáct." |
| `digits`, `number_digit` | | Text je vysloven jako posloupnost jednotlivých číslic. Modul pro syntézu řeči vyslovuje:<br /><br />`<say-as interpret-as="number_digit">123456789</say-as>`<br /><br />Jako "1 2 3 4 5 6 7 8 9." |
| `fraction` | | Text je vyslovován jako zlomkové číslo. Modul pro syntézu řeči vyslovuje:<br /><br /> `<say-as interpret-as="fraction">3/8</say-as> of an inch`<br /><br />Jako "tři osminy palce." |
| `ordinal` | | Text je vysloven jako řadové číslo. Modul pro syntézu řeči vyslovuje:<br /><br />`Select the <say-as interpret-as="ordinal">3rd</say-as> option`<br /><br />Jako "Vyberte třetí možnost". |
| `telephone` | | Text je vysloven jako telefonní číslo. Atribut `format` může obsahovat číslice, které představují kód země. Například "1" pro Spojené státy nebo "39" pro Itálii. Modul pro syntézu řeči může použít tyto informace k vedení jeho výslovnosti telefonního čísla. Telefonní číslo může také obsahovat kód země, a pokud ano, `format`má přednost před kódem země v oblasti . Modul pro syntézu řeči vyslovuje:<br /><br />`The number is <say-as interpret-as="telephone" format="1">(888) 555-1212</say-as>`<br /><br />Jako "Moje číslo je předčíslí osm osm osm pět pět pět jedna jedna dva." |
| `time` | hms12, hms24 | Text je mluvený jako čas. Atribut `format` určuje, zda je čas určen pomocí 12hodinových hodin (hms12) nebo 24hodinových hodin (hms24). Dvojtečkou použijte k oddělení čísel představujících hodiny, minuty a sekundy. Platné příklady času jsou: 12:35, 1:14:32, 08:15 a 02:50:45. Modul pro syntézu řeči vyslovuje:<br /><br />`The train departs at <say-as interpret-as="time" format="hms12">4:00am</say-as>`<br /><br />Jako "Vlak odjíždí ve čtyři ráno." |

**Použití**

Prvek `say-as` může obsahovat pouze text.

**Příklad**

Motor pro syntézu řeči hovoří následujícím příkladem jako "Vaše první žádost byla o jednu místnost v říjnu devatenácté dvacet deset s brzký příjezd na dvanáct třicet pět PM."
 
```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <p>
        Your <say-as interpret-as="ordinal"> 1st </say-as> request was for <say-as interpret-as="cardinal"> 1 </say-as> room
        on <say-as interpret-as="date" format="mdy"> 10/19/2010 </say-as>, with early arrival at <say-as interpret-as="time" format="hms12"> 12:35pm </say-as>.
        </p>
    </voice>
</speak>
```

## <a name="add-recorded-audio"></a>Přidání nahraného zvuku

`audio`je volitelný prvek, který umožňuje vložit zvuk MP3 do dokumentu SSML. Tělo zvukového prvku může obsahovat prostý text nebo značky SSML, které se vyslovují, pokud je zvukový soubor nedostupný nebo nehratelný. `audio` Kromě toho může prvek obsahovat text `audio`a `break` `p`následující `s` `phoneme`prvky: , , , , `prosody`, `say-as`, a `sub`.

Veškerý zvuk obsažený v dokumentu SSML musí splňovat tyto požadavky:

* MP3 musí být hostované na koncovém bodu HTTPS přístupném pro Internet. Je vyžadován protokol HTTPS a doména hostující soubor MP3 musí obsahovat platný důvěryhodný certifikát TLS/SSL.
* MP3 musí být platný soubor MP3 (MPEG v2).
* Přenosová rychlost musí být 48 kbps.
* Vzorkovací frekvence musí být 16 000 Hz.
* Celkový celkový čas pro všechny textové a zvukové soubory v jedné odpovědi nesmí přesáhnout devadesát (90) sekund.
* Mp3 nesmí obsahovat žádné informace specifické pro zákazníka nebo jiné citlivé informace.

**Syntaxe**

```xml
<audio src="string"/></audio>
```

**Atributy**

| Atribut | Popis | Povinné / Nepovinné |
|-----------|-------------|---------------------|
| `src` | Určuje umístění/adresu URL zvukového souboru. | Povinné při použití zvukového prvku v dokumentu SSML. |

**Příklad**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <p>
            <audio src="https://contoso.com/opinionprompt.wav"/>
            Thanks for offering your opinion. Please begin speaking after the beep.
            <audio src="https://contoso.com/beep.wav">
                Could not play the beep, please voice your opinion now.
            </audio>
        </p>
    </voice>
</speak>
```

## <a name="add-background-audio"></a>Přidání zvuku na pozadí

Prvek `mstts:backgroundaudio` umožňuje přidat zvuk na pozadí do dokumentů SSML (nebo kombinovat zvukový soubor s převodem textu na řeč). Pomocí `mstts:backgroundaudio` funkce můžete smyčku zvukového souboru na pozadí, zeslabit na začátku převodu textu na řeč a zeslabit na konci převodu textu na řeč.

Pokud je poskytnutý zvuk na pozadí kratší než převod textu na řeč nebo zeslabení, bude smyčkou. Pokud je delší než převod textu na řeč, zastaví se po ukončení vyblednutí.

Na jeden zvukový soubor na pozadí je povolen pouze jeden zvukový soubor na pozadí. Můžete však v `audio` `voice` rámci prvku proložit značky a přidat do dokumentu SSML další zvuk.

**Syntaxe**

```XML
<mstts:backgroundaudio src="string" volume="string" fadein="string" fadeout="string"/>
```

**Atributy**

| Atribut | Popis | Povinné / Nepovinné |
|-----------|-------------|---------------------|
| `src` | Určuje umístění/adresu URL zvukového souboru na pozadí. | Vyžadováno při použití zvuku na pozadí v dokumentu SSML. |
| `volume` | Určuje hlasitost zvukového souboru pozadí. **Přijaté**hodnoty `0` `100` : do včetně. Výchozí hodnota je `1`. | Nepovinné |
| `fadein` | Určuje dobu trvání zvuku na pozadí "fade in" jako milisekundy. Výchozí hodnota `0`je , což odpovídá žádnému zeslabení. **Přijaté**hodnoty `0` `10000` : do včetně.  | Nepovinné |
| `fadeout` | Určuje dobu trvání zeslabení zvuku na pozadí v milisekundách. Výchozí hodnota `0`je , což odpovídá žádnému zeslabení. **Přijaté**hodnoty `0` `10000` : do včetně.  | Nepovinné |

**Příklad**

```xml
<speak version="1.0" xml:lang="en-US" xmlns:mstts="http://www.w3.org/2001/mstts">
    <mstts:backgroundaudio src="https://contoso.com/sample.wav" volume="0.7" fadein="3000" fadeout="4000"/>
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)">
        The text provided in this document will be spoken over the background audio.
    </voice>
</speak>
```

## <a name="next-steps"></a>Další kroky

* [Jazyková podpora: hlasy, národní prostředí, jazyky](language-support.md)
