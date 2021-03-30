---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/04/2021
ms.author: trbye
ms.custom: devx-track-js
ms.openlocfilehash: 5a8065daca11e5b79f02510f82ab622c8fb1af2d
ms.sourcegitcommit: 91361cbe8fff7c866ddc4835251dcbbe2621c055
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "105729827"
---
Jednou z klíčových funkcí služby Speech je možnost rozpoznávat a přepisovat lidské řeči (často se označuje jako převod řeči na text). V tomto rychlém startu se naučíte používat sadu Speech SDK ve vašich aplikacích a produktech k provádění vysoce kvalitních převodů řeči na text.

## <a name="skip-to-samples-on-github"></a>Přeskočit na ukázky na GitHubu

Pokud chcete přeskočit přímý na vzorový kód, přečtěte si [ukázky rychlý Start JavaScriptu](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node) na GitHubu.

Případně si přečtěte [ukázku reakce](https://github.com/Azure-Samples/AzureSpeechReactSample) a Naučte se používat sadu Speech SDK v prostředí založeném na prohlížeči.

## <a name="prerequisites"></a>Požadavky

V tomto článku se předpokládá, že máte účet Azure a předplatné služby Speech. Pokud účet a předplatné nemáte, [Vyzkoušejte službu Speech Service zdarma](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Instalace sady Speech SDK

Předtím, než můžete cokoli udělat, musíte nainstalovat sadu Speech SDK pro Node.js. Pokud chcete jenom nainstalovat název balíčku, spusťte příkaz `npm install microsoft-cognitiveservices-speech-sdk` . Pokyny k instalaci najdete v článku [Začínáme](../../../quickstarts/setup-platform.md?pivots=programming-language-javascript&tabs=dotnet%2clinux%2cjre%2cnodejs) .

`require`K importu sady SDK použijte následující příkaz.

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

Další informace o naleznete v `require` [dokumentaci vyžadovat](https://nodejs.org/en/knowledge/getting-started/what-is-require/).

## <a name="create-a-speech-configuration"></a>Vytvoření konfigurace řeči

Chcete-li volat službu Speech pomocí sady Speech SDK, je třeba vytvořit [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig) . Tato třída obsahuje informace o vašem předplatném, jako je klíč a přidružená oblast, koncový bod, hostitel nebo autorizační token. Vytvořte [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig) pomocí klíče a oblasti. Podívejte se na stránku [najít klíče a oblast](../../../overview.md#find-keys-and-region) a vyhledejte pár klíč-oblast.

```javascript
const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
```

Existuje několik dalších způsobů, jak můžete inicializovat [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig) :

* S koncovým bodem: předejte koncový bod služby řeči. Klíč nebo autorizační token jsou volitelné.
* S hostitelem: předejte adresu hostitele. Klíč nebo autorizační token jsou volitelné.
* Pomocí autorizačního tokenu: předejte autorizační token a přidruženou oblast.

> [!NOTE]
> Bez ohledu na to, jestli provádíte rozpoznávání řeči, syntézu řeči, překlad nebo rozpoznávání záměrů, vždy vytvoříte konfiguraci.

## <a name="recognize-from-microphone-browser-only"></a>Rozpoznat z mikrofonu (jenom prohlížeč)

Rozpoznávání řeči pomocí mikrofonu není **v Node.jspodporováno** a je podporováno pouze v prostředí JavaScript založeném na prohlížeči. Pokud chcete zobrazit [Převod řeči na text z implementace mikrofonu](https://github.com/Azure-Samples/AzureSpeechReactSample/blob/main/src/App.js#L29), podívejte se na [ukázku reakce](https://github.com/Azure-Samples/AzureSpeechReactSample) na GitHubu.

> [!NOTE]
> Pokud chcete použít *konkrétní* vstupní zvukové zařízení, je nutné zadat ID zařízení v `AudioConfig` . Přečtěte si, [Jak získat ID zařízení](../../../how-to-select-audio-input-devices.md) pro vstupní zvukové zařízení.

## <a name="recognize-from-file"></a>Rozpoznat ze souboru 

Pro rozpoznávání řeči ze zvukového souboru vytvořte pomocí, `AudioConfig` `fromWavFileInput()` který přijímá `Buffer` objekt. Pak inicializujte [`SpeechRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer) a, předejte své `audioConfig` a `speechConfig` .

```javascript
const fs = require('fs');
const sdk = require("microsoft-cognitiveservices-speech-sdk");
const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-subscription-key>", "<paste-your-region>");

function fromFile() {
    let audioConfig = sdk.AudioConfig.fromWavFileInput(fs.readFileSync("YourAudioFile.wav"));
    let recognizer = new sdk.SpeechRecognizer(speechConfig, audioConfig);

    recognizer.recognizeOnceAsync(result => {
        console.log(`RECOGNIZED: Text=${result.text}`);
        recognizer.close();
    });
}
fromFile();
```

## <a name="recognize-from-in-memory-stream"></a>Rozpoznávání z datového proudu v paměti

V mnoha případech použití je pravděpodobnější, že zvuková data přicházejí z úložiště objektů BLOB nebo jinak jsou v paměti jako [`ArrayBuffer`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) nebo podobná struktura nezpracovaných dat. Následující kód:

* Vytvoří Stream push pomocí `createPushStream()` .
* Přečte `.wav` soubor pomocí `fs.createReadStream` demonstračních účelů, ale pokud již máte zvuková data v `ArrayBuffer` , můžete přeskočit přímo k zápisu obsahu do vstupního streamu.
* Vytvoří konfiguraci zvuku pomocí datového proudu push.

```javascript
const fs = require('fs');
const sdk = require("microsoft-cognitiveservices-speech-sdk");
const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-subscription-key>", "<paste-your-region>");

function fromStream() {
    let pushStream = sdk.AudioInputStream.createPushStream();

    fs.createReadStream("YourAudioFile.wav").on('data', function(arrayBuffer) {
        pushStream.write(arrayBuffer.slice());
    }).on('end', function() {
        pushStream.close();
    });
 
    let audioConfig = sdk.AudioConfig.fromStreamInput(pushStream);
    let recognizer = new sdk.SpeechRecognizer(speechConfig, audioConfig);
    recognizer.recognizeOnceAsync(result => {
        console.log(`RECOGNIZED: Text=${result.text}`);
        recognizer.close();
    });
}
fromStream();
```

Použití datového proudu push jako vstupu předpokládá, že zvuková data jsou nezpracované PCM, například přeskočení hlaviček.
Rozhraní API bude v některých případech fungovat i v případě, že hlavička nebyla vynechána, ale pro dosažení nejlepších výsledků zvažte implementaci logiky, aby bylo možné číst hlavičky, takže `fs` začíná na *začátku zvukového data*.

## <a name="error-handling"></a>Zpracování chyb

Předchozí příklady jednoduše získají rozpoznaný text z `result.text` , ale pro zpracování chyb a dalších odpovědí, budete muset napsat kód pro zpracování výsledku. Následující kód vyhodnocuje [`result.reason`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognitionresult#reason) vlastnost a:

* Vytiskne výsledek rozpoznávání: `ResultReason.RecognizedSpeech`
* Pokud se neshodují žádné rozpoznávání, informujte uživatele: `ResultReason.NoMatch`
* Pokud dojde k chybě, vytiskněte chybovou zprávu: `ResultReason.Canceled`

```javascript
switch (result.reason) {
    case ResultReason.RecognizedSpeech:
        console.log(`RECOGNIZED: Text=${result.text}`);
        break;
    case ResultReason.NoMatch:
        console.log("NOMATCH: Speech could not be recognized.");
        break;
    case ResultReason.Canceled:
        const cancellation = CancellationDetails.fromResult(result);
        console.log(`CANCELED: Reason=${cancellation.reason}`);

        if (cancellation.reason == CancellationReason.Error) {
            console.log(`CANCELED: ErrorCode=${cancellation.ErrorCode}`);
            console.log(`CANCELED: ErrorDetails=${cancellation.errorDetails}`);
            console.log("CANCELED: Did you update the subscription info?");
        }
        break;
    }
```

## <a name="continuous-recognition"></a>Průběžné rozpoznávání

V předchozích příkladech se používá rozpoznávání pomocí jediného snímku, které rozpoznává jeden utterance. Konec jednoho utterance se určuje tak, že naslouchá tichému ukončení na konci nebo dokud se nezpracovává po dobu 15 sekund zvuku.

Naopak se průběžné rozpoznávání používá, pokud chcete **určit** , kdy se má zastavit rozpoznávání. Pro získání výsledků rozpoznávání se vyžaduje přihlášení k odběru `Recognizing` `Recognized` událostí, a `Canceled` . Chcete-li zastavit rozpoznávání, je nutné zavolat [`stopContinuousRecognitionAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#stopcontinuousrecognitionasync) . Tady je příklad toho, jak se provádí nepřetržité rozpoznávání na vstupním souboru zvuku.

Začněte definováním vstupu a inicializací [`SpeechRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer) :

```javascript
const recognizer = new sdk.SpeechRecognizer(speechConfig, audioConfig);
```

Potom se přihlaste k odběru událostí odeslaných z [`SpeechRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer) .

* [`recognizing`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#recognizing): Signál pro události obsahující mezilehlé výsledky rozpoznávání.
* [`recognized`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#recognized): Signál pro události obsahující konečné výsledky rozpoznávání (indikující úspěšný pokus o uznání).
* [`sessionStopped`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#sessionstopped): Signál pro události indikující konec relace rozpoznávání (operace).
* [`canceled`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#canceled): Signál pro události obsahující zrušené výsledky rozpoznávání (indikující pokus o deaktivaci, který byl zrušen jako výsledek nebo přímý požadavek na zrušení nebo případně i přenos nebo selhání protokolu).

```javascript
recognizer.recognizing = (s, e) => {
    console.log(`RECOGNIZING: Text=${e.result.text}`);
};

recognizer.recognized = (s, e) => {
    if (e.result.reason == ResultReason.RecognizedSpeech) {
        console.log(`RECOGNIZED: Text=${e.result.text}`);
    }
    else if (e.result.reason == ResultReason.NoMatch) {
        console.log("NOMATCH: Speech could not be recognized.");
    }
};

recognizer.canceled = (s, e) => {
    console.log(`CANCELED: Reason=${e.reason}`);

    if (e.reason == CancellationReason.Error) {
        console.log(`"CANCELED: ErrorCode=${e.errorCode}`);
        console.log(`"CANCELED: ErrorDetails=${e.errorDetails}`);
        console.log("CANCELED: Did you update the subscription info?");
    }

    recognizer.stopContinuousRecognitionAsync();
};

recognizer.sessionStopped = (s, e) => {
    console.log("\n    Session stopped event.");
    recognizer.stopContinuousRecognitionAsync();
};
```

Když se všechno nastaví, zavolejte [`startContinuousRecognitionAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#startcontinuousrecognitionasync) na zahájit rozpoznávání.

```javascript
recognizer.startContinuousRecognitionAsync();

// make the following call at some point to stop recognition.
// recognizer.StopContinuousRecognitionAsync();
```

### <a name="dictation-mode"></a>Režim diktování

Při použití průběžného rozpoznávání můžete povolit zpracování diktování pomocí odpovídající funkce "Povolit diktování". V tomto režimu bude instance konfigurace řeči interpretovat popisy slov ve strukturách vět, jako je například interpunkční znaménko. Například utterance "provedete to živě ve městě otazník", který se interpretuje jako text "žijete ve městě?".

Chcete-li povolit režim diktování, použijte [`enableDictation`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig#enabledictation--) metodu na [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig) .

```javascript
speechConfig.enableDictation();
```

## <a name="change-source-language"></a>Změnit jazyk zdroje

Běžným úkolem pro rozpoznávání řeči je zadání vstupu (nebo zdrojového) jazyka. Pojďme se podívat, jak byste změnili vstupní jazyk na italštinu. V kódu Najděte [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig) a pak přidejte tento řádek přímo pod ním.

```javascript
speechConfig.speechRecognitionLanguage = "it-IT";
```

[`speechRecognitionLanguage`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig#speechrecognitionlanguage)Vlastnost očekává řetězec formátu národního prostředí jazyka. Můžete zadat libovolnou hodnotu ve sloupci **locale (národní prostředí** ) v seznamu podporovaných [národních prostředí a jazyků](../../../language-support.md).

## <a name="improve-recognition-accuracy"></a>Zlepšení přesnosti rozpoznávání

Seznamy frází slouží k identifikaci známých frází ve zvukových datech, jako je jméno osoby nebo konkrétní umístění. Zadáním seznamu frází Vylepšete přesnost rozpoznávání řeči.

Příklad: Pokud máte příkaz "přesunout do" a možné místo cíle "", které je možné přehlasovat, můžete přidat položku "přesunout do" dál ". Přidáním fráze se zvýší pravděpodobnost, že při rozpoznání zvuku bude místo možnosti přesunout směrem nahoru rozpoznána možnost přesunout na vyšší.

Do seznamu frází lze přidat jednotlivá slova nebo kompletní fráze. Při rozpoznávání se položka v seznamu frází používá ke zvýšení rozpoznávání slov a frází v seznamu i v případě, že se položky objeví uprostřed utterance. 

> [!IMPORTANT]
> Funkce seznamu frází je dostupná v těchto jazycích: en-US, de-DE, EN-AU, en-CA, en-GB, ES-ES, ES-MX, fr-CA, fr-FR, IT-IT, ja-JP, ko-KR, pt-BR, zh-CN

Chcete-li použít seznam frází, nejprve vytvořte [`PhraseListGrammar`](/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar) objekt a pak přidejte konkrétní slova a fráze pomocí [`addPhrase`](/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar#addphrase-string-) .

Všechny změny se projeví [`PhraseListGrammar`](/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar) při příštím rozpoznávání nebo po opětovném připojení ke službě Speech.

```javascript
const phraseList = sdk.PhraseListGrammar.fromRecognizer(recognizer);
phraseList.addPhrase("Supercalifragilisticexpialidocious");
```

Pokud potřebujete vymazat seznam frází:

```javascript
phraseList.clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Další možnosti pro zlepšení přesnosti rozpoznávání

Seznamy frází jsou pouze jedním z možností, jak zlepšit přesnost rozpoznávání. Můžete také: 

* [Zlepšení přesnosti s využitím služby Custom Speech](../../../custom-speech-overview.md)
* [Zlepšení přesnosti s využitím modelů tenantů](../../../tutorial-tenant-model.md)