---
title: SSML (Speech syntézy Language) – služba pro rozpoznávání řeči
titleSuffix: Azure Cognitive Services
description: Použití jazyka značek syntézy řeči k řízení výslovnosti a prosodyi v převodu textu na řeč.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: trbye
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: d5cf5cd4c2667af3064b74ab2b1c5cd5bd347446
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2021
ms.locfileid: "102434283"
---
# <a name="improve-synthesis-with-speech-synthesis-markup-language-ssml"></a>Vylepšení syntézy pomocí jazyka SSML (Speech syntézy)

SSML (Speech syntézy Language) je značkovací jazyk založený na jazyce XML, který umožňuje vývojářům určit, jakým způsobem se vstupní text převede na syntetizované rozpoznávání řeči pomocí služby převodu textu na řeč. V porovnání s prostým textem umožňuje SSML vývojářům doladit rozteč, výslovnost, míru řeči, objem a další výstup textu na řeč. Normální interpunkční znaménka, jako je například pozastavení po určité době, nebo použití správné nečinnosti, pokud je věta zakončena znakem otazníku, automaticky zpracována.

Implementace služby SSML pro rozpoznávání řeči je založená konsorcium World Wide Web na [jazyku XML pro rozpoznávání řeči verze 1,0](https://www.w3.org/TR/speech-synthesis).

> [!IMPORTANT]
> Čínské, japonské a korejské znaky se počítají jako dva znaky pro účely fakturace. Další informace najdete v tématu [ceny](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

## <a name="standard-neural-and-custom-voices"></a>Standardní, neuronové a vlastní hlasy

Vyberte si ze standardních nebo neuronové hlasů nebo si vytvořte vlastní hlas jedinečný pro svůj produkt nebo značku. 75 a standardní hlasy jsou k dispozici ve více než 45 jazycích a národních prostředích a 5 neuronové hlasy je k dispozici ve čtyřech jazycích a národních prostředích. Úplný seznam podporovaných jazyků, národních prostředí a hlasů (neuronové a Standard) najdete v tématu [Podpora jazyků](language-support.md).

Další informace o standardních, neuronové a vlastních hlasů najdete v tématu [Přehled převodu textu na řeč](text-to-speech.md).


> [!NOTE]
> Můžete slyšet hlasy v různých stylech a pomocí [stránky převod textu na řeč se](https://azure.microsoft.com/services/cognitive-services/text-to-speech/#features)podíváme na vzorový text.


## <a name="special-characters"></a>Speciální znaky

Při použití SSML Pamatujte na to, že speciální znaky, jako jsou uvozovky, apostrofy a závorky, musí být uvozeny řídicím znakem. Další informace najdete v tématu [jazyk XML (Extensible Markup Language) (XML) 1,0: příloha D](https://www.w3.org/TR/xml/#sec-entexpand).

## <a name="supported-ssml-elements"></a>Podporované elementy SSML

Každý dokument SSML je vytvořen pomocí SSML prvků (nebo značek). Tyto prvky slouží k úpravě roztečí, Prosody, objemu a dalších. Následující části podrobně popisují, jak se jednotlivé prvky používají, a když je prvek povinný nebo volitelný.  

> [!IMPORTANT]
> Nezapomeňte použít dvojité uvozovky kolem hodnot atributů. Standardy pro správný formát platná XML vyžadují, aby hodnoty atributu byly uzavřeny do dvojitých uvozovek. Například je ve `<prosody volume="90">` správném formátu platný prvek, ale není `<prosody volume=90>` . SSML nesmí rozpoznat hodnoty atributu, které nejsou v uvozovkách.

## <a name="create-an-ssml-document"></a>Vytvoření dokumentu SSML

`speak` je kořenový prvek a je **vyžadován** pro všechny dokumenty SSML. `speak`Element obsahuje důležité informace, jako je verze, jazyk a definice slovníku označení.

**Syntax**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="string"></speak>
```

**Atributy**

| Atribut | Popis | Požadováno/volitelné |
|-----------|-------------|---------------------|
| `version` | Určuje verzi specifikace SSML, která se používá k interpretaci značek dokumentu. Aktuální verze je 1,0. | Vyžadováno |
| `xml:lang` | Určuje jazyk kořenového dokumentu. Hodnota může obsahovat malé písmeno, kód jazyka se dvěma písmeny (například `en` ), kód jazyka a zemi/oblast (například `en-US` ). | Vyžadováno |
| `xmlns` | Určuje identifikátor URI dokumentu, který definuje slovník značek (typy prvků a názvy atributů) dokumentu SSML. Aktuální identifikátor URI je http://www.w3.org/2001/10/synthesis . | Vyžadováno |

## <a name="choose-a-voice-for-text-to-speech"></a>Volba hlasu pro převod textu na řeč

`voice`Element je povinný. Slouží k určení hlasu, který se používá pro převod textu na řeč.

**Syntax**

```xml
<voice name="string">
    This text will get converted into synthesized speech.
</voice>
```

**Atributy**

| Atribut | Popis | Požadováno/volitelné |
|-----------|-------------|---------------------|
| `name` | Identifikuje hlas používaný pro výstup textu na řeč. Úplný seznam podporovaných hlasů najdete v tématu [Podpora jazyků](language-support.md#text-to-speech). | Vyžadováno |

**Příklad**

> [!NOTE]
> V tomto příkladu se používá `en-US-AriaRUS` hlas. Úplný seznam podporovaných hlasů najdete v tématu [Podpora jazyků](language-support.md#text-to-speech).

```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        This is the text that is spoken.
    </voice>
</speak>
```

## <a name="use-multiple-voices"></a>Použít více hlasů

V rámci `speak` elementu můžete zadat více hlasů pro výstup textu na řeč. Tyto hlasy můžou být v různých jazycích. U každého hlasu musí být text zabalen do `voice` elementu. 

**Atributy**

| Atribut | Popis | Požadováno/volitelné |
|-----------|-------------|---------------------|
| `name` | Identifikuje hlas používaný pro výstup textu na řeč. Úplný seznam podporovaných hlasů najdete v tématu [Podpora jazyků](language-support.md#text-to-speech). | Vyžadováno |

> [!IMPORTANT]
> Více hlasů je nekompatibilních s funkcí hranice slova. Aby bylo možné použít více hlasů, je třeba zakázat funkci hranice slova.

### <a name="disable-word-boundary"></a>Zakázat hranici slova

V závislosti na jazyku sady Speech SDK nastavíte vlastnost na `"SpeechServiceResponse_Synthesis_WordBoundaryEnabled"` `false` instanci `SpeechConfig` objektu.

# <a name="c"></a>[C#](#tab/csharp)

Další informace najdete v tématu <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.setproperty" target="_blank"> `SetProperty` </a>.

```csharp
speechConfig.SetProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="c"></a>[C++](#tab/cpp)

Další informace najdete v tématu <a href="https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setproperty" target="_blank"> `SetProperty` </a>.

```cpp
speechConfig->SetProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="java"></a>[Java](#tab/java)

Další informace najdete v tématu <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setproperty#com_microsoft_cognitiveservices_speech_SpeechConfig_setProperty_String_String_" target="_blank"> `setProperty` </a>.

```java
speechConfig.setProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="python"></a>[Python](#tab/python)

Další informace najdete v tématu <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#set-property-by-name-property-name--str--value--str-" target="_blank"> `set_property_by_name` </a>.

```python
speech_config.set_property_by_name(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Další informace najdete v tématu <a href="https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig#setproperty-string--string-" target="_blank"> `setProperty` </a>.

```javascript
speechConfig.setProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="objective-c"></a>[Objective-C](#tab/objectivec)

Další informace najdete v tématu <a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechconfiguration#setpropertytobyname" target="_blank"> `setPropertyTo` </a>.

```objectivec
[speechConfig setPropertyTo:@"false" byName:@"SpeechServiceResponse_Synthesis_WordBoundaryEnabled"];
```

# <a name="swift"></a>[Swift](#tab/swift)

Další informace najdete v tématu <a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechconfiguration#setpropertytobyname" target="_blank"> `setPropertyTo` </a>.

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

## <a name="adjust-speaking-styles"></a>Upravit styly speaking

> [!IMPORTANT]
> Úpravy mluveného stylu budou fungovat jenom s neuronové hlasy.

Ve výchozím nastavení služba pro převod textu na řeč syntetizuje text pomocí neutrálního mluveného stylu pro hlasy Standard i neuronové. S neuronové hlasymi můžete upravit styl speaking tak, aby bylo možné vyjádřit různé emoce, jako je cheerfulness, soucit nebo Calm, nebo optimalizovat hlas pro různé scénáře, jako jsou služby zákazníkům, newscasting a hlasový asistent, a to pomocí `mstts:express-as` elementu. Toto je volitelný element jedinečný pro službu Speech Service.

V současné době jsou pro tyto hlasy neuronové podporovány úpravy stylu speaking:
* `en-US-AriaNeural`
* `en-US-JennyNeural`
* `en-US-GuyNeural`
* `pt-BR-FranciscaNeural`
* `zh-CN-XiaoxiaoNeural`
* `zh-CN-YunyangNeural`
* `zh-CN-YunyeNeural`
* `zh-CN-YunxiNeural` Tisk
* `zh-CN-XiaohanNeural` Tisk
* `zh-CN-XiaomoNeural` Tisk
* `zh-CN-XiaoxuanNeural` Tisk
* `zh-CN-XiaoruiNeural` Tisk

Intenzita stylu projevování může být dále změněna, aby lépe vyhovovala vašemu případu použití. Můžete zadat silnější nebo měkčí styl s `styledegree` cílem usnadnit tak vyjádření a subdued řeči. 

V současné době jsou pro tyto hlasy neuronové podporovány úpravy stylu speaking:
* `zh-CN-XiaoxiaoNeural`

Kromě úprav stylů a stylu mluveného projevu můžete také upravit `role` parametr tak, aby hlas byl jiný a pohlaví. Například samcský hlas může zvýšit rozteč a změnit rozmocninu k napodobenině ženského hlasu.

V současné době jsou úpravy role-přehrávání podporovány pro tyto hlasy neuronové:
* `zh-CN-XiaomoNeural`
* `zh-CN-XiaoxuanNeural`

Výše uvedené změny se aplikují na úrovni věty a styly a aktéry rolí se liší podle hlasu. Pokud styl nebo role-Play není podporován, služba vrátí rozpoznávání řeči ve výchozím nastavení neutrální speaking. Můžete si prohlédnout, jaké styly a role-Play jsou podporovány pro každý hlas prostřednictvím [rozhraní API pro seznam hlasu](rest-text-to-speech.md#get-a-list-of-voices) nebo prostřednictvím platformy pro [vytváření obsahu pro zvuk](https://aka.ms/audiocontentcreation) bez kódu.

**Syntax**

```xml
<mstts:express-as style="string"></mstts:express-as>
```
```xml
<mstts:express-as style="string" styledegree="value"></mstts:express-as>
```
```xml
<mstts:express-as role="string" style="string"></mstts:express-as>
```
> [!NOTE]
> V tuto chvíli `styledegree` podporuje jenom zh-CN-XiaoxiaoNeural. `role` podporuje pouze zh-CN-XiaomoNeural a zh-CN-XiaoxuanNeural.

**Atributy**

| Atribut | Popis | Požadováno/volitelné |
|-----------|-------------|---------------------|
| `style` | Určuje styl speaking. V současné době jsou styly mluvené řeči specifické pro hlas. | Vyžaduje se, když se upraví styl speakování pro neuronové hlas. Pokud používáte `mstts:express-as` , musí být zadán styl. Pokud je zadána neplatná hodnota, bude tento prvek ignorován. |
| `styledegree` | Určuje intenzitu stylu speaking. **Přijaté hodnoty**: 0,01 až 2 včetně. Výchozí hodnota je 1, což znamená, že předdefinovaná intenzita stylu. Minimální jednotka je 0,01, což má za následek trochu tendenci pro cílový styl. Hodnota 2 má za následek zdvojnásobení výchozí intenzity stylu.  | Volitelné (v současné době `styledegree` podporuje jenom zh-CN-XiaoxiaoNeural.)|
| `role` | Určuje speaking Role-Play. Hlas bude fungovat jako jiný věk a pohlaví.  | Volitelné (v současné době `role` podporuje jenom zh-CN-XiaomoNeural a zh-CN-XiaoxuanNeural.)|

Pomocí této tabulky můžete určit, které mluvené styly jsou pro každý neuronové hlas podporovány.

| Hlas                   | Styl                     | Popis                                                 |
|-------------------------|---------------------------|-------------------------------------------------------------|
| `en-US-AriaNeural`      | `style="newscast-formal"` | Vyjadřuje formální, jistý a autoritativní tón pro doručování zpráv. |
|                         | `style="newscast-casual"` | Vyjadřuje všestranný a příležitostný tón pro obecné doručování zpráv.        |
|                         | `style="customerservice"` | Vyjadřuje uživatelsky přívětivý a užitečný tón pro zákaznickou podporu.  |
|                         | `style="chat"`            | Vyjádření nepříležitostného a odlehčeného tónu                         |
|                         | `style="cheerful"`        | Vyjadřuje kladný a šťastný tón.                         |
|                         | `style="empathetic"`      | Vyjadřuje smysl caring a porozumění               |
| `en-US-JennyNeural`     | `style="customerservice"` | Vyjadřuje uživatelsky přívětivý a užitečný tón pro zákaznickou podporu.  |
|                         | `style="chat"`            | Vyjádření nepříležitostného a odlehčeného tónu                         |
|                         | `style="assistant"`       | Vyjadřuje teplý a odlehčený tón pro digitální asistenty    |
|                         | `style="newscast"`        | Vyjadřuje všestranný a příležitostný tón pro obecné doručování zpráv.   |
| `en-US-GuyNeural`       | `style="newscast"`        | Vyjadřuje formální a profesionální tón pro zprávy mluveného komentáře. |
| `pt-BR-FranciscaNeural` | `style="calm"`            | Vyjadřuje studenou, získanou a složenou polohu při mluvení. Tónů, rozteč a Prosody je v porovnání s jinými typy řeči mnohem jednotnější.                                |
| `zh-CN-XiaoxiaoNeural`  | `style="newscast"`        | Vyjadřuje formální a profesionální tón pro zprávy mluveného komentáře. |
|                         | `style="customerservice"` | Vyjadřuje uživatelsky přívětivý a užitečný tón pro zákaznickou podporu.  |
|                         | `style="assistant"`       | Vyjadřuje teplý a odlehčený tón pro digitální asistenty    |
|                         | `style="chat"`            | Vyjádření příležitostného a odlehčeného tónu pro CHITEST – chat           |
|                         | `style="calm"`            | Vyjadřuje studenou, získanou a složenou polohu při mluvení. Tónů, rozteč a Prosody je v porovnání s jinými typy řeči mnohem jednotnější.                                |
|                         | `style="cheerful"`        | Vyjadřuje dostáváme a tón s vyšší roztečí a r-spotřebou.                         |
|                         | `style="sad"`             | Vyjadřuje sorrowful tón s vyšší roztečí, nižší intenzitou a nižší spotřebou r. Běžné indikátory tohoto emoce by byly během řeči whimpers nebo Crying.            |
|                         | `style="angry"`           | Vyjadřuje Angry a nespokojeni tón s nižší roztečí, vyšší intenzitou a vyšší spotřebou r. Mluvčí je ve stavu, ve kterém se irate, je zastavený a poškozený.       |
|                         | `style="fearful"`         | Vyjadřuje děsili a nervový tón s vyšší roztečí, vyšší energií energie a vyšší rychlostí. Mluvčí je ve stavu tenseness a uneasiness.                          |
|                         | `style="disgruntled"`     | Vyjadřuje Disdainful a stížnost. Řeč tohoto emoce zobrazuje nerekreační a dočasné.              |
|                         | `style="serious"`         | Vyjadřuje striktní a příkazový tón. Mluvčí často zazní a mnohem méně odlehčená pomocí tempo.          |
|                         | `style="affectionate"`    | Vyjadřuje teplý a affectionate tón s vyšší roztečí a spotřebou r. Mluvčí je ve stavu, ve kterém se přilákat na pozornost naslouchacího procesu. Osobní preference mluvčího je často endearingá.          |     
|                         | `style="gentle"`          | Vyjádření mírného, zdvořiléého a příjemnýho tónu s nižší roztečí a r-energií         |   
|                         | `style="lyrical"`         | Vyjadřuje emoce v Melodic a Sentimental         |   
| `zh-CN-YunyangNeural`   | `style="customerservice"` | Vyjadřuje uživatelsky přívětivý a užitečný tón pro zákaznickou podporu.  | 
| `zh-CN-YunyeNeural`     | `style="calm"`            | Vyjadřuje studenou, získanou a složenou polohu při mluvení. Tónů, rozteč a Prosody je v porovnání s jinými typy řeči mnohem jednotnější.    | 
|                         | `style="cheerful"`        | Vyjadřuje dostáváme a tón s vyšší roztečí a r-spotřebou.                         |
|                         | `style="sad"`             | Vyjadřuje sorrowful tón s vyšší roztečí, nižší intenzitou a nižší spotřebou r. Běžné indikátory tohoto emoce by byly během řeči whimpers nebo Crying.            |
|                         | `style="angry"`           | Vyjadřuje Angry a nespokojeni tón s nižší roztečí, vyšší intenzitou a vyšší spotřebou r. Mluvčí je ve stavu, ve kterém se irate, je zastavený a poškozený.       |
|                         | `style="fearful"`         | Vyjadřuje děsili a nervový tón s vyšší roztečí, vyšší energií energie a vyšší rychlostí. Mluvčí je ve stavu tenseness a uneasiness.                          |
|                         | `style="disgruntled"`     | Vyjadřuje Disdainful a stížnost. Řeč tohoto emoce zobrazuje nerekreační a dočasné.              |
|                         | `style="serious"`         | Vyjadřuje striktní a příkazový tón. Mluvčí často zazní a mnohem méně odlehčená pomocí tempo.          |
| `zh-CN-YunxiNeural`     | `style="cheerful"`        | Vyjadřuje dostáváme a tón s vyšší roztečí a r-spotřebou.                         |
|                         | `style="sad"`             | Vyjadřuje sorrowful tón s vyšší roztečí, nižší intenzitou a nižší spotřebou r. Běžné indikátory tohoto emoce by byly během řeči whimpers nebo Crying.            |
|                         | `style="angry"`           | Vyjadřuje Angry a nespokojeni tón s nižší roztečí, vyšší intenzitou a vyšší spotřebou r. Mluvčí je ve stavu, ve kterém se irate, je zastavený a poškozený.       |
|                         | `style="fearful"`         | Vyjadřuje děsili a nervový tón s vyšší roztečí, vyšší energií energie a vyšší rychlostí. Mluvčí je ve stavu tenseness a uneasiness.                          |
|                         | `style="disgruntled"`     | Vyjadřuje Disdainful a stížnost. Řeč tohoto emoce zobrazuje nerekreační a dočasné.              |
|                         | `style="serious"`         | Vyjadřuje striktní a příkazový tón. Mluvčí často zazní a mnohem méně odlehčená pomocí tempo.    |
|                         | `style="depressed"`       | Vyjadřuje melancholic a despondent tón s nižší roztečí a energií.    |
|                         | `style="embarrassed"`     | Vyjadřuje nejistý a váhají tón, když je mluvčí Uncomfortable.   |
| `zh-CN-XiaohanNeural`   | `style="cheerful"`        | Vyjadřuje dostáváme a tón s vyšší roztečí a r-spotřebou.                         |
|                         | `style="sad"`             | Vyjadřuje sorrowful tón s vyšší roztečí, nižší intenzitou a nižší spotřebou r. Běžné indikátory tohoto emoce by byly během řeči whimpers nebo Crying.            |
|                         | `style="angry"`           | Vyjadřuje Angry a nespokojeni tón s nižší roztečí, vyšší intenzitou a vyšší spotřebou r. Mluvčí je ve stavu, ve kterém se irate, je zastavený a poškozený.       |
|                         | `style="fearful"`         | Vyjadřuje děsili a nervový tón s vyšší roztečí, vyšší energií energie a vyšší rychlostí. Mluvčí je ve stavu tenseness a uneasiness.                          |
|                         | `style="disgruntled"`     | Vyjadřuje Disdainful a stížnost. Řeč tohoto emoce zobrazuje nerekreační a dočasné.              |
|                         | `style="serious"`         | Vyjadřuje striktní a příkazový tón. Mluvčí často zazní a mnohem méně odlehčená pomocí tempo.    |
|                         | `style="embarrassed"`     | Vyjadřuje nejistý a váhají tón, když je mluvčí Uncomfortable.   |
|                         | `style="affectionate"`    | Vyjadřuje teplý a affectionate tón s vyšší roztečí a spotřebou r. Mluvčí je ve stavu, ve kterém se přilákat na pozornost naslouchacího procesu. Osobní preference mluvčího je často endearingá.          |     
|                         | `style="gentle"`          | Vyjádření mírného, zdvořiléého a příjemnýho tónu s nižší roztečí a r-energií         |   
| `zh-CN-XiaomoNeural`    | `style="cheerful"`        | Vyjadřuje dostáváme a tón s vyšší roztečí a r-spotřebou.                         |
|                         | `style="angry"`           | Vyjadřuje Angry a nespokojeni tón s nižší roztečí, vyšší intenzitou a vyšší spotřebou r. Mluvčí je ve stavu, ve kterém se irate, je zastavený a poškozený.       |
|                         | `style="fearful"`         | Vyjadřuje děsili a nervový tón s vyšší roztečí, vyšší energií energie a vyšší rychlostí. Mluvčí je ve stavu tenseness a uneasiness.                          |
|                         | `style="disgruntled"`     | Vyjadřuje Disdainful a stížnost. Řeč tohoto emoce zobrazuje nerekreační a dočasné.              |
|                         | `style="serious"`         | Vyjadřuje striktní a příkazový tón. Mluvčí často zazní a mnohem méně odlehčená pomocí tempo.    |
|                         | `style="depressed"`       | Vyjadřuje melancholic a despondent tón s nižší roztečí a energií.    |
|                         | `style="gentle"`          | Vyjádření mírného, zdvořiléého a příjemnýho tónu s nižší roztečí a r-energií         |  
| `zh-CN-XiaoxuanNeural`  | `style="cheerful"`        | Vyjadřuje dostáváme a tón s vyšší roztečí a r-spotřebou.                         |
|                         | `style="angry"`           | Vyjadřuje Angry a nespokojeni tón s nižší roztečí, vyšší intenzitou a vyšší spotřebou r. Mluvčí je ve stavu, ve kterém se irate, je zastavený a poškozený.       |
|                         | `style="fearful"`         | Vyjadřuje děsili a nervový tón s vyšší roztečí, vyšší energií energie a vyšší rychlostí. Mluvčí je ve stavu tenseness a uneasiness.                          |
|                         | `style="disgruntled"`     | Vyjadřuje Disdainful a stížnost. Řeč tohoto emoce zobrazuje nerekreační a dočasné.              |
|                         | `style="serious"`         | Vyjadřuje striktní a příkazový tón. Mluvčí často zazní a mnohem méně odlehčená pomocí tempo.    |
|                         | `style="depressed"`       | Vyjadřuje melancholic a despondent tón s nižší roztečí a energií.    |
|                         | `style="gentle"`          | Vyjádření mírného, zdvořiléého a příjemnýho tónu s nižší roztečí a r-energií         |   
| `zh-CN-XiaoruiNeural`    | `style="sad"`             | Vyjadřuje sorrowful tón s vyšší roztečí, nižší intenzitou a nižší spotřebou r. Běžné indikátory tohoto emoce by byly během řeči whimpers nebo Crying.            |
|                         | `style="angry"`           | Vyjadřuje Angry a nespokojeni tón s nižší roztečí, vyšší intenzitou a vyšší spotřebou r. Mluvčí je ve stavu, ve kterém se irate, je zastavený a poškozený.       |
|                         | `style="fearful"`         | Vyjadřuje děsili a nervový tón s vyšší roztečí, vyšší energií energie a vyšší rychlostí. Mluvčí je ve stavu tenseness a uneasiness.                          |

Pomocí této tabulky můžete určit, které role se u každého hlasu neuronové podporují.

| Hlas                   | Role                       | Popis                                                 |
|-------------------------|----------------------------|-------------------------------------------------------------|
| `zh-CN-XiaomoNeural`    | `role="YoungAdultFemale"`  | Hlasový hovor je napodoben od mladých dospělých samic.                 |
|                         | `role="OlderAdultMale"`    | Tento hlas je napodoben na starší dospělé samci.                   |
|                         | `role="Girl"`              | Hlasový vstup je napodoben na Girl.                               |
|                         | `role="Boy"`               | Hlasový vstup je napodoben na Boy.                                |
| `zh-CN-XiaoxuanNeural`  | `role="YoungAdultFemale"`  | Hlasový hovor je napodoben od mladých dospělých samic.                 |
|                         | `role="OlderAdultFemale"`  | Tento hlas je napodoben na starší dospělého ženského pohlaví.                 |
|                         | `role="OlderAdultMale"`    | Tento hlas je napodoben na starší dospělé samci.                   |

**Příklad**

Tento fragment SSML ukazuje, jak se `<mstts:express-as>` prvek používá ke změně stylu speakování na `cheerful` .

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

Tento fragment SSML ukazuje, jak se `styledegree` atribut používá ke změně intenzity stylu speaking pro XiaoxiaoNeural.
```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
       xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="zh-CN">
    <voice name="zh-CN-XiaoxiaoNeural">
        <mstts:express-as style="sad" styledegree="2">
            快走吧，路上一定要注意安全，早去早回。
        </mstts:express-as>
    </voice>
</speak>
```

Tento fragment SSML ukazuje, jak se `role` atribut používá ke změně Role-Play pro XiaomoNeural.
```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
       xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="zh-CN">
    <voice name="zh-CN-XiaomoNeural">
        女儿看见父亲走了进来，问道：
        <mstts:express-as role="YoungAdultFemale" style="calm">
            “您来的挺快的，怎么过来的？”
        </mstts:express-as>
        父亲放下手提包，说：
        <mstts:express-as role="OlderAdultMale" style="calm">
            “刚打车过来的，路上还挺顺畅。”
        </mstts:express-as>
    </voice>
</speak>
```

## <a name="add-or-remove-a-breakpause"></a>Přidat nebo odebrat přerušení/pozastavení

Pomocí `break` elementu vložte pauzy (nebo přerušit) mezi slova nebo Zabraňte automatickému přidání do služby převodu textu na řeč.

> [!NOTE]
> Pomocí tohoto prvku můžete přepsat výchozí chování převodu textu na řeč (TTS) pro slovo nebo frázi v případě, že syntetizované rozpoznávání řeči pro toto slovo nebo frázi nepřirozeně zvuk. Nastavte `strength` na `none` , aby nedocházelo k přerušení Prozodický předěl, které je automaticky vložené službou pro převod textu na řeč.

**Syntax**

```xml
<break strength="string" />
<break time="string" />
```

**Atributy**

| Atribut | Popis | Požadováno/volitelné |
|-----------|-------------|---------------------|
| `strength` | Určuje relativní dobu trvání pozastavení pomocí jedné z následujících hodnot:<ul><li>žádné</li><li>x – slabý</li><li>slabé</li><li>střední (výchozí)</li><li>silnější</li><li>x – silné</li></ul> | Volitelné |
| `time` | Určuje absolutní dobu trvání pauzy v sekundách nebo milisekundách, tato hodnota by měla být nastavena na hodnotu menší než 5000ms. Příklady platných hodnot jsou `2s` a. `500ms` | Volitelné |

| Obsahem                      | Popis |
|-------------------------------|-------------|
| Žádná, nebo pokud není zadána žádná hodnota | 0 MS        |
| x – slabý                        | 250 ms      |
| slabé                          | 500 ms      |
| střední                        | 750 ms      |
| silnější                        | 1000 MS     |
| x – silné                      | 1250 MS     |

**Příklad**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
    </voice>
</speak>
```
## <a name="add-silence"></a>Přidat tichou

Použijte `mstts:silence` element pro vložení pauz před nebo za text nebo mezi 2 sousedícími větami. 

> [!NOTE]
>Rozdíl mezi `mstts:silence` a `break` je, který `break` lze přidat na libovolné místo v textu, ale ticha funguje pouze na začátku nebo konci vstupního textu nebo na hranici 2 sousedících vět.  


**Syntax**

```xml
<mstts:silence  type="string"  value="string"/>
```

**Atributy**

| Atribut | Popis | Požadováno/volitelné |
|-----------|-------------|---------------------|
| `type` | Určuje umístění tichého navýšení: <ul><li>Přední – na začátku textu </li><li>Zakončení – na konci textu </li><li>Sentenceboundary – mezi sousedními větami </li></ul> | Vyžadováno |
| `Value` | Určuje absolutní dobu trvání pauzy v sekundách nebo milisekundách, tato hodnota by měla být nastavena na hodnotu menší než 5000ms. Příklady platných hodnot jsou `2s` a. `500ms` | Vyžadováno |

**Příklad** V tomto příkladu `mtts:silence` se používá k přidání 200 MS ticha mezi dvěma větami.
```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">  
<voice name="en-US-AriaNeural"> 
<mstts:silence  type="Sentenceboundary" value="200ms"/> 
If we’re home schooling, the best we can do is roll with what each day brings and try to have fun along the way. 
A good place to start is by trying out the slew of educational apps that are helping children stay happy and smash their schooling at the same time. 
</voice> 
</speak> 
```

## <a name="specify-paragraphs-and-sentences"></a>Zadat odstavce a věty

`p``s`prvky a se používají k označení odstavců a vět v uvedeném pořadí. V případě absence těchto prvků služba převod textu na řeč automaticky určuje strukturu dokumentu SSML.

`p`Element může obsahovat text a následující prvky: `audio` , `break` , `phoneme` , `prosody` , `say-as` ,, a `sub` `mstts:express-as` `s` .

`s`Element může obsahovat text a následující prvky: `audio` , `break` , `phoneme` , `prosody` , `say-as` , `mstts:express-as` a `sub` .

**Syntax**

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

## <a name="use-phonemes-to-improve-pronunciation"></a>Použití fonémy ke zlepšení výslovnosti

`ph`Prvek se používá pro fonetickou výslovnost v dokumentech SSML. `ph`Element může obsahovat pouze text, žádné jiné prvky. V případě záložního převodu vždy poskytněte humánní řeč čitelný.

Fonetické abecedy se skládají z telefonů, které jsou tvořeny písmeny, číslicemi nebo znaky, někdy v kombinaci. Každý telefon popisuje jedinečný zvuk řeči. To je na rozdíl od abecedy latinky, kde jakékoli písmeno může představovat více mluvených zvuků. Vezměte v úvahu různé výslovnosti písmena "c" ve slově "Candy" a "pozastaveno", nebo na rozdíl od kombinace písmen "th" v slovech "věc" a "ty".

> [!NOTE]
> Značka fonémy se pro tyto 5 hlasy nepodporuje (et-EE-AnuNeural, GA-IE-OrlaNeural, lt-LT-OnaNeural, LV-LV-EveritaNeural a MT-MT-GarceNeural).

**Syntax**

```XML
<phoneme alphabet="string" ph="string"></phoneme>
```

**Atributy**

| Atribut | Popis | Požadováno/volitelné |
|-----------|-------------|---------------------|
| `alphabet` | Určuje fonetickou abecedu, která se použije při syntetizování výslovnosti řetězce v `ph` atributu. Řetězec určující abecedu musí být zadán malými písmeny. Níže jsou uvedené možné abecedy, které můžete zadat.<ul><li>`ipa`&ndash; <a href="https://en.wikipedia.org/wiki/International_Phonetic_Alphabet" target="_blank">Mezinárodní fonetická abeceda</a></li><li>`sapi`&ndash; [Fonetická abeceda služby Speech](speech-ssml-phonetic-sets.md)</li><li>`ups`&ndash; <a href="https://documentation.help/Microsoft-Speech-Platform-SDK-11/17509a49-cae7-41f5-b61d-07beaae872ea.htm" target="_blank">Univerzální telefonní sada</a></li></ul><br>Abeceda se vztahuje pouze na `phoneme` prvek v prvku.. | Volitelné |
| `ph` | Řetězec obsahující telefony, které určují výslovnost slova v `phoneme` prvku. Pokud zadaný řetězec obsahuje nerozpoznané telefony, služba převod textu na mluvené slovo (TTS) odmítne celý dokument SSML a vytvoří žádný z výstupů řeči zadaného v dokumentu. | Vyžaduje se, pokud používáte fonémy. |

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

## <a name="use-custom-lexicon-to-improve-pronunciation"></a>Vylepšení výslovnosti pomocí vlastního lexikonu

Někdy může služba převod textu na řeč přesně vyslovit slovo. Například název společnosti nebo zdravotní podmínky. Vývojáři mohou definovat způsob, jakým jsou jednotlivé entity čteny v SSML pomocí `phoneme` `sub` značek a. Pokud však potřebujete definovat způsob čtení více entit, můžete vytvořit vlastní lexikon pomocí `lexicon` značky.

> [!NOTE]
> Vlastní lexikon aktuálně podporuje kódování UTF-8. 

> [!NOTE]
> Vlastní lexikon se pro tyto 5 hlasy nepodporuje (et-EE-AnuNeural, GA-IE-OrlaNeural, lt-LT-OnaNeural, LV-LV-EveritaNeural a MT-MT-GarceNeural).


**Syntax**

```XML
<lexicon uri="string"/>
```

**Atributy**

| Atribut | Popis                               | Požadováno/volitelné |
|-----------|-------------------------------------------|---------------------|
| `uri`     | Adresa externího dokumentu jiných pracovních prostorů | Povinná hodnota.           |

**Použití**

Chcete-li definovat způsob čtení více entit, můžete vytvořit vlastní lexikon, který je uložen jako soubor. XML nebo. jiných pracovních prostorů. Následuje ukázkový soubor. XML.

```xml
<?xml version="1.0" encoding="UTF-8"?>
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

`lexicon`Element obsahuje alespoň jeden `lexeme` element. Každý `lexeme` prvek obsahuje nejméně jeden `grapheme` element a jeden nebo více elementů `grapheme` , `alias` a `phoneme` . `grapheme`Element obsahuje text popisující <a href="https://www.w3.org/TR/pronunciation-lexicon/#term-Orthography" target="_blank">orthography </a>. `alias`Prvky slouží k označení výslovnosti zkratky nebo zkrácení podmínky. `phoneme`Element poskytuje text popisující způsob, jakým `lexeme` je vyslovení.

Je důležité si uvědomit, že nemůžete přímo nastavit výslovnost fráze pomocí vlastního slovníku. Pokud potřebujete nastavit výslovnost zkratky nebo zkrácení podmínky, nejprve zadejte `alias` a přidružte k `phoneme` ní `alias` . Například:

```xml
  <lexeme>
    <grapheme>Scotland MV</grapheme> 
    <alias>ScotlandMV</alias> 
  </lexeme>
  <lexeme>
    <grapheme>ScotlandMV</grapheme> 
    <phoneme>ˈskɒtlənd.ˈmiːdiəm.weɪv</phoneme>
  </lexeme>
```

Můžete také přímo poskytnout očekávanou `alias` zkratku nebo zkrácený termín. Například:
```xml
  <lexeme>
    <grapheme>Scotland MV</grapheme> 
    <alias>Scotland Media Wave</alias> 
  </lexeme>
```

> [!IMPORTANT]
> `phoneme`Element nemůže obsahovat prázdné znaky při použití IPA.

Další informace o souboru s vlastním souborem lexikonu naleznete v tématu [jiných pracovních prostorů (výslovnost lexikon Specification) verze 1,0](https://www.w3.org/TR/pronunciation-lexicon/).

Potom publikujte svůj vlastní soubor lexikonu. I když nemáme omezení, kde je možné tento soubor uložit, doporučujeme použít [Azure Blob Storage](../../storage/blobs/storage-quickstart-blobs-portal.md).

Po publikování vlastního slovníku ho můžete odkázat z SSML.

> [!NOTE]
> `lexicon`Element musí být uvnitř `voice` elementu.

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" 
          xmlns:mstts="http://www.w3.org/2001/mstts" 
          xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <lexicon uri="http://www.example.com/customlexicon.xml"/>
        BTW, we will be there probably at 8:00 tomorrow morning.
        Could you help leave a message to Robert Benigni for me?
    </voice>
</speak>
```

Při použití tohoto vlastního slovníku se "BTW" přečte jako "způsobem". "Neškodné" se budou číst pomocí poskytnutého IPAu "bɛ ˈ ni ː nji".  

**Omezení**
- Velikost souboru: maximální limit velikosti souboru lexikonu je 100 KB, pokud je tato velikost mimo tuto velikost, požadavek na Shrnutí se nezdaří.
- Aktualizace pro lexikonovou mezipaměť: vlastní lexikon bude při prvním načtení uložen do mezipaměti s identifikátorem URI jako klíč ve službě TTS. Lexikon se stejným identifikátorem URI nebude znovu načten do 15 minut, takže změna vlastního lexikonu musí počkat až o 15 minut, než se projeví.

**Fonetické sady pro hlasové služby**

V ukázce výše používáme mezinárodní fonetickou abecedu, která se označuje také jako IPA telefonická sada. Doporučujeme, aby vývojáři používali IPA, protože se jedná o mezinárodní standard. U některých IPA znaků mají při reprezentaci s kódováním Unicode ve verzi "složené" a "rozložené" verze. Vlastní lexikon podporuje pouze rozložené znakové sady Unicode.

Vzhledem k tomu, že se IPA nepamatuje, Služba rozpoznávání řeči definuje fonetický sadu pro sedm jazyků ( `en-US` , `fr-FR` , `de-DE` , `es-ES` , `ja-JP` , a `zh-CN` `zh-TW` ).

Můžete použít `sapi` jako hodnotu pro `alphabet` atribut s vlastními lexikony, jak je znázorněno níže:

```xml
<?xml version="1.0" encoding="UTF-8"?>
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

Další informace o fonetické abecedě hlasové služby pro rozpoznávání řeči najdete v části [fonetické sady služby Speech](speech-ssml-phonetic-sets.md).

## <a name="adjust-prosody"></a>Upravit Prosody

`prosody`Element se používá k určení změn sklonu, obrysu, rozsahu, míry, trvání a objemu pro výstup textu na řeč. `prosody`Element může obsahovat text a následující prvky: `audio` , `break` , `p` , `phoneme` , `prosody` ,, a `say-as` `sub` `s` .

Vzhledem k tomu, že se hodnoty atributů Prozodický předěl můžou v rámci široké škály lišit, překladač řeči interpretuje přiřazené hodnoty jako návrh toho, co by měly být aktuální hodnoty Prozodický předěl vybraného hlasu. Služba převod textu na řeč omezuje nebo nahrazuje hodnoty, které nejsou podporovány. Příklady nepodporovaných hodnot jsou výškou 1 MHz nebo 120.

**Syntax**

```XML
<prosody pitch="value" contour="value" range="value" rate="value" duration="value" volume="value"></prosody>
```

**Atributy**

| Atribut | Popis | Požadováno/volitelné |
|-----------|-------------|---------------------|
| `pitch` | Určuje rozteč účaří pro text. Rozteč můžete vyjádřit jako:<ul><li>Absolutní hodnota vyjádřená jako číslo následovaný "Hz" (Hz). Například, `<prosody pitch="600Hz">some text</prosody>`.</li><li>Relativní hodnota vyjádřená jako číslo před "+" nebo "-" a následována "Hz" nebo "St", která určuje velikost pro změnu rozteči. Například: `<prosody pitch="+80Hz">some text</prosody>` nebo `<prosody pitch="-2st">some text</prosody>` . "St" značí, že se jednotka změny semitone, což je polovina tónu (poloviční krok) na standardním diatonic škále.</li><li>Konstantní hodnota:<ul><li>x – nízká</li><li>slab</li><li>střední</li><li>high</li><li>x-vysoká</li><li>default</li></ul></li></ul> | Volitelné |
| `contour` |Obrys teď podporuje hlasy neuronové i Standard. Obrys znázorňuje změny v rozteči. Tyto změny jsou reprezentovány jako pole cílů v určených časových pozicích ve výstupu řeči. Každý cíl je definován sadami dvojic parametrů. Například: <br/><br/>`<prosody contour="(0%,+20Hz) (10%,-2st) (40%,+10Hz)">`<br/><br/>První hodnota v každé sadě parametrů určuje umístění změny sklonu v procentech doby trvání textu. Druhá hodnota určuje velikost, která má zvýšit nebo snížit rozteč, pomocí relativní hodnoty nebo hodnoty výčtu pro rozteč (viz `pitch` ). | Volitelné |
| `range` | Hodnota, která představuje rozsah roztečí textu. Můžete vyjádřit `range` použití stejných absolutních hodnot, relativních hodnot nebo hodnot výčtu používaných k popisu `pitch` . | Volitelné |
| `rate` | Určuje míru projevení textu. Můžete vyjádřit `rate` jako:<ul><li>Relativní hodnota vyjádřená jako číslo, které funguje jako násobitel výchozí hodnoty. Například hodnota *1* má za následek nezměněnou sazbu. Výsledkem hodnoty *0,5* je poloviční sazba. Hodnota *3* má za následek cestu k této sazbě.</li><li>Konstantní hodnota:<ul><li>x – pomalé</li><li>pomalé</li><li>střední</li><li>světl</li><li>x – Fast</li><li>default</li></ul></li></ul> | Volitelné |
| `duration` | Časový interval, který by měl uplynout, zatímco služba rozpoznávání řeči (TTS) čte text v sekundách nebo milisekundách. Například *2S* nebo *1800ms*. Doba trvání podporuje pouze standardní hlasy.| Volitelné |
| `volume` | Určuje úroveň hlasitosti mluveného hlasu. Svazek můžete vyjádřit jako:<ul><li>Absolutní hodnota vyjádřená jako číslo v rozsahu od 0,0 do 100,0, od *tichého* po *nahlasu*. Například 75. Výchozí hodnota je 100,0.</li><li>Relativní hodnota vyjádřená jako číslo začínající znakem "+" nebo "-", která určuje velikost pro změnu svazku. Například + 10 nebo-5,5.</li><li>Konstantní hodnota:<ul><li>tich</li><li>× – měkké</li><li>Pohyblivý</li><li>střední</li><li>rovnává</li><li>x-nahlas</li><li>default</li></ul></li></ul> | Volitelné |

### <a name="change-speaking-rate"></a>Změna míry projevení

Míru speakace lze použít na hlasy neuronové a standardní hlasy na úrovni slova nebo věty. 

**Příklad**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-GuyNeural">
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
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <prosody volume="+20.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-pitch"></a>Změnit rozteč

Změny v rozteči je možné použít u standardních hlasů na úrovni slova nebo věty. Vzhledem k tomu, že změny v sklonu se dají použít jenom na hlasy neuronové na úrovni věty.

**Příklad**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Guy24kRUS">
        Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
    </voice>
</speak>
```

### <a name="change-pitch-contour"></a>Změnit rozvrh sklonu

> [!IMPORTANT]
> U hlasů neuronové se teď podporují změny profilování sklonu.

**Příklad**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        <prosody contour="(60%,-60%) (100%,+80%)" >
            Were you the only person in the room? 
        </prosody>
    </voice>
</speak>
```
## <a name="say-as-element"></a>Říkám elementu

`say-as` je volitelný prvek, který určuje typ obsahu (například číslo nebo datum) textu elementu. V této části najdete pokyny k vyslovení textu v modulu Shrnutí řeči.

**Syntax**

```XML
<say-as interpret-as="string" format="digit string" detail="string"> <say-as>
```

**Atributy**

| Atribut | Popis | Požadováno/volitelné |
|-----------|-------------|---------------------|
| `interpret-as` | Určuje typ obsahu textu elementu. Seznam typů naleznete v následující tabulce. | Vyžadováno |
| `format` | Poskytuje další informace o přesném formátování textu elementu pro typy obsahu, které mohou mít dvojznačné formáty. SSML definuje formáty pro typy obsahu, které je používají (viz tabulka níže). | Volitelné |
| `detail` | Určuje úroveň podrobností, které se mají vymluvené. Tento atribut například může vyžadovat, aby se v modulu Shrnutí řeči vyhodnotily interpunkční znaménka. Nejsou definovány žádné standardní hodnoty pro `detail` . | Volitelné |

<!-- I don't understand the last sentence. Don't we know which one Cortana uses? -->

Níže jsou podporované typy obsahu pro `interpret-as` `format` atributy a. Atribut zahrňte pouze v případě, že `format` `interpret-as` je nastavena na hodnotu datum a čas.

| interpretovat jako | formát | Interpretace |
|--------------|--------|----------------|
| `address` | | Text se používá jako adresa. Vysloví se modul Shrnutí řeči:<br /><br />`I'm at <say-as interpret-as="address">150th CT NE, Redmond, WA</say-as>`<br /><br />Jako "jsem jsem na 150thm soudu sever – východ Redmond – Washington." |
| `cardinal`, `number` | | Text se hovoří jako číslo mohutnosti. Vysloví se modul Shrnutí řeči:<br /><br />`There are <say-as interpret-as="cardinal">3</say-as> alternatives`<br /><br />Stejně jako existují tři alternativy. |
| `characters`, `spell-out` | | Text se hovoří jako jednotlivá písmena (vypsaný). Vysloví se modul Shrnutí řeči:<br /><br />`<say-as interpret-as="characters">test</say-as>`<br /><br />Jako "T E S T". |
| `date` | DMY, mdy, YMD, není, YM, my, MD, DM, d, m, y | Text se hovoří jako datum. `format`Atribut určuje formát data (*d = den, m = měsíc a y = rok*). Vysloví se modul Shrnutí řeči:<br /><br />`Today is <say-as interpret-as="date" format="mdy">10-19-2016</say-as>`<br /><br />Jako "dnes je Nineteenth 2016. října" |
| `digits`, `number_digit` | | Text se hlasuje jako sekvence jednotlivých číslic. Vysloví se modul Shrnutí řeči:<br /><br />`<say-as interpret-as="number_digit">123456789</say-as>`<br /><br />Jako "1 2 3 4 5 6 7 8 9". |
| `fraction` | | Text se hlasuje jako desetinné číslo. Vysloví se modul Shrnutí řeči:<br /><br /> `<say-as interpret-as="fraction">3/8</say-as> of an inch`<br /><br />Jako tři osmá palce. |
| `ordinal` | | Text se hlasuje jako ordinální číslo. Vysloví se modul Shrnutí řeči:<br /><br />`Select the <say-as interpret-as="ordinal">3rd</say-as> option`<br /><br />Jako vyberte třetí možnost. |
| `telephone` | | Text se používá jako telefonní číslo. `format`Atribut může obsahovat číslice, které reprezentují kód země. Například "1" pro USA nebo "39" pro Itálii. Modul Shrnutí řeči může tyto informace využít k tomu, aby provedou svou výslovnost telefonního čísla. Telefonní číslo může zahrnovat i kód země, a pokud ano, má přednost před kódem země v `format` . Vysloví se modul Shrnutí řeči:<br /><br />`The number is <say-as interpret-as="telephone" format="1">(888) 555-1212</say-as>`<br /><br />"Moje číslo je směrové číslo oblasti 8 8 8 5 5 5 1 2 1 2." |
| `time` | hms12, hms24 | Text se používá jako čas. `format`Atribut určuje, zda je zadaný čas pomocí 12hodinových hodin (hms12) nebo 24hodinových hodin (hms24). Použijte dvojtečku k oddělení čísel reprezentujících hodiny, minuty a sekundy. Následují příklady platných časů: 12:35, 1:14:32, 08:15 a 02:50:45. Vysloví se modul Shrnutí řeči:<br /><br />`The train departs at <say-as interpret-as="time" format="hms12">4:00am</say-as>`<br /><br />"Vlak se odpodílí na čtyřech A M." |

**Použití**

`say-as`Element může obsahovat pouze text.

**Příklad**

Modul Shrnutí řeči připraví následující příklad jako "první požadavek byl v říjnu Nineteenth 20 10 s počátečním příchodem na 12 35 odp."
 
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

## <a name="add-recorded-audio"></a>Přidat zaznamenaný zvuk

`audio` je volitelný prvek, který umožňuje vložit zvuk MP3 do dokumentu SSML. Tělo zvukového prvku může obsahovat prostý text nebo SSML poznámky, které jsou mluvené, pokud je zvukový soubor nedostupný nebo nezobrazitelný. Kromě toho `audio` element může obsahovat text a následující prvky: `audio` , `break` , `p` , `s` , `phoneme` ,, a `prosody` `say-as` `sub` .

Libovolný zvuk zahrnutý v dokumentu SSML musí splňovat tyto požadavky:

* MP3 musí být hostovaný na koncovém bodu HTTPS, který je přístupný pro Internet. Je vyžadován protokol HTTPS a doména hostující soubor MP3 musí představovat platný důvěryhodný certifikát TLS/SSL.
* MP3 musí být platný soubor MP3 (MPEG v2).
* Přenosová rychlost musí být 48 KB/s.
* Vzorkovací frekvence musí být 16 000 Hz.
* Celková celková doba pro všechny textové a zvukové soubory v jedné odpovědi nesmí překročit 90 (90) sekund.
* MP3 nesmí obsahovat žádné informace specifické pro zákazníka nebo jiné citlivé informace.

**Syntax**

```xml
<audio src="string"/></audio>
```

**Atributy**

| Atribut | Popis                                   | Požadováno/volitelné                                        |
|-----------|-----------------------------------------------|------------------------------------------------------------|
| `src`     | Určuje umístění nebo adresu URL zvukového souboru. | Požadováno při použití prvku zvuk v dokumentu SSML. |

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

## <a name="add-background-audio"></a>Přidat zvuk na pozadí

`mstts:backgroundaudio`Element umožňuje přidat do dokumentů SSML zvuk na pozadí (nebo míchat zvukový soubor s převodem textu na řeč). Díky `mstts:backgroundaudio` tomu můžete na pozadí zacyklovat zvukový soubor, mizet na začátku převodu textu na řeč a zeslabit na konci převodu textu na řeč.

Pokud je zadaný zvuk na pozadí kratší než převod textu na řeč nebo zeslabit, smyčka se spustí. Pokud je delší než převod textu na řeč, zastaví se, až se rozzvolna dokončí.

V SSML dokumentu je povolen pouze jeden zvukový soubor na pozadí. Můžete však `audio` v rámci `voice` elementu doplnit značky přidáním dalšího zvuku do dokumentu SSML.

**Syntax**

```XML
<mstts:backgroundaudio src="string" volume="string" fadein="string" fadeout="string"/>
```

**Atributy**

| Atribut | Popis | Požadováno/volitelné |
|-----------|-------------|---------------------|
| `src` | Určuje umístění nebo adresu URL zvukového souboru na pozadí. | Vyžaduje se, pokud v dokumentu SSML používáte zvuk na pozadí. |
| `volume` | Určuje hlasitost zvukového souboru na pozadí. **Přijaté hodnoty**: `0` na `100` včetně Výchozí hodnota je `1`. | Volitelné |
| `fadein` | Určuje dobu, po kterou se bude zvuk na pozadí zobrazovat jako milisekundy. Výchozí hodnota je `0` , což je ekvivalent bez zmizení. **Přijaté hodnoty**: `0` na `10000` včetně  | Volitelné |
| `fadeout` | Určuje dobu, po kterou se má zvuk na pozadí rozmizet v milisekundách. Výchozí hodnota je `0` , což je ekvivalent bez zmizení. **Přijaté hodnoty**: `0` na `10000` včetně  | Volitelné |

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

* [Podpora jazyků: hlasy, národní prostředí, jazyky](language-support.md)
