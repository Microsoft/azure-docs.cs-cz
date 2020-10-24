---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/15/2020
ms.author: trbye
ms.custom: devx-track-js
ms.openlocfilehash: afe6562357f1d5558fdb2ac8cb0eb53cd3422d48
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92499200"
---
Jednou z klíčových funkcí služby Speech je možnost rozpoznávat a přepisovat lidské řeči (často se označuje jako převod řeči na text). V tomto rychlém startu se naučíte používat sadu Speech SDK ve vašich aplikacích a produktech k provádění vysoce kvalitních převodů řeči na text.

## <a name="skip-to-samples-on-github"></a>Přeskočit na ukázky na GitHubu

Pokud chcete přeskočit přímý na vzorový kód, přečtěte si [ukázky rychlý Start JavaScriptu](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node) na GitHubu.

## <a name="prerequisites"></a>Předpoklady

V tomto článku se předpokládá, že máte účet Azure a předplatné služby Speech. Pokud účet a předplatné nemáte, [Vyzkoušejte službu Speech Service zdarma](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Instalace sady Speech SDK

Předtím, než můžete cokoli udělat, musíte nainstalovat <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">sadu Speech SDK pro JavaScript <span class="docon docon-navigate-external x-hidden-focus"></span> </a>. V závislosti na vaší platformě postupujte podle následujících pokynů:

- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Node.js <span 
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">Webový prohlížeč <span class="docon docon-navigate-external x-hidden-focus"></span></a>

V závislosti na cílovém prostředí navíc použijte jednu z následujících možností:

# <a name="script"></a>[pravidel](#tab/script)

Stáhněte a extrahujte <a href="https://aka.ms/csspeech/jsbrowserpackage" target="_blank">sadu Speech SDK pro <span class="docon docon-navigate-external x-hidden-focus"></span> JavaScript</a> *microsoft.cognitiveservices.speech.sdk.bundle.js* soubor a umístěte ji do složky přístupné pro váš soubor HTML.

```html
<script src="microsoft.cognitiveservices.speech.sdk.bundle.js"></script>;
```

> [!TIP]
> Pokud cílíte na webový prohlížeč a použijete `<script>` značku; `sdk` Předpona není nutná. `sdk`Předpona je alias použitý k pojmenování `require` modulu.

# <a name="import"></a>[importovat](#tab/import)

```javascript
import * from "microsoft-cognitiveservices-speech-sdk";
```

Další informace o najdete v `import` tématu <a href="https://javascript.info/import-export" target="_blank">Export a import <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.

# <a name="require"></a>[žádá](#tab/require)

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

Další informace o najdete v `require` tématu <a href="https://nodejs.org/en/knowledge/getting-started/what-is-require/" target="_blank">co je potřeba? <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.

---

## <a name="create-a-speech-configuration"></a>Vytvoření konfigurace řeči

Chcete-li volat službu Speech pomocí sady Speech SDK, je třeba vytvořit [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest&preserve-view=true) . Tato třída obsahuje informace o vašem předplatném, jako je klíč a přidružená oblast, koncový bod, hostitel nebo autorizační token. Vytvořte [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest&preserve-view=true) pomocí klíče a oblasti. Identifikátor vaší oblasti najdete na stránce [podpory oblasti](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) .

```javascript
const speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

Existuje několik dalších způsobů, jak můžete inicializovat [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest&preserve-view=true) :

* S koncovým bodem: předejte koncový bod služby řeči. Klíč nebo autorizační token jsou volitelné.
* S hostitelem: předejte adresu hostitele. Klíč nebo autorizační token jsou volitelné.
* Pomocí autorizačního tokenu: předejte autorizační token a přidruženou oblast.

> [!NOTE]
> Bez ohledu na to, jestli provádíte rozpoznávání řeči, syntézu řeči, překlad nebo rozpoznávání záměrů, vždy vytvoříte konfiguraci.

## <a name="recognize-from-microphone"></a>Rozpoznávání z mikrofonu

Pokud chcete rozpoznávat řeč pomocí mikrofonu zařízení, vytvořte `AudioConfig` pomocí `fromDefaultMicrophoneInput()` . Pak inicializujte [`SpeechRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest&preserve-view=true) a, předejte své `speechConfig` a `audioConfig` .

```javascript
const audioConfig = AudioConfig.fromDefaultMicrophoneInput();
const recognizer = new SpeechRecognizer(speechConfig, audioConfig);

console.log('Speak into your microphone.');
recognizer.recognizeOnceAsync(result => {
    console.log(`RECOGNIZED: Text=${result.text}`);
});
```

Pokud chcete použít *konkrétní* vstupní zvukové zařízení, je nutné zadat ID zařízení v `AudioConfig` . Přečtěte si, [Jak získat ID zařízení](../../../how-to-select-audio-input-devices.md) pro vstupní zvukové zařízení.

## <a name="recognize-from-file-nodejs-only"></a>Rozpoznat ze souboru (jenom Node.js)

Pokud chcete rozpoznávat řeč ze zvukového souboru místo pomocí mikrofonu, musíte zadat `AudioConfig` . Pokud však vytvoříte [`AudioConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?view=azure-node-latest&preserve-view=true) místo volání, `fromDefaultMicrophoneInput()` zavoláte `fromWavFileInput()` a předáte cestu k souboru.

```javascript
const audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
const recognizer = new SpeechRecognizer(speechConfig, audioConfig);

recognizer.recognizeOnceAsync(result => {
    console.log(`RECOGNIZED: Text=${result.text}`);
});
```

## <a name="recognize-speech"></a>Rozpoznávání řeči

[Třída pro rozpoznávání](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest&preserve-view=true) pro sadu Speech SDK pro JavaScript zpřístupňuje několik metod, které můžete použít pro rozpoznávání řeči.

### <a name="single-shot-recognition"></a>Rozpoznávání s jedním záběrem

Samostatné rozpoznávání asynchronně rozpoznává jeden utterance. Konec jednoho utterance se určuje tak, že naslouchá tichému ukončení na konci nebo dokud se nezpracovává po dobu 15 sekund zvuku. Tady je příklad asynchronního samostatného rozpoznávání pomocí [`recognizeOnceAsync`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest&preserve-view=true#recognizeonceasync) :

```javascript
recognizer.recognizeOnceAsync(result => {
    // Interact with result
});
```

Pro zpracování výsledku budete muset napsat nějaký kód. Tato ukázka vyhodnocuje [`result.reason`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognitionresult?view=azure-node-latest&preserve-view=true#reason) :

* Vytiskne výsledek rozpoznávání: `ResultReason.RecognizedSpeech`
* Pokud se neshodují žádné rozpoznávání, informujte uživatele: `ResultReason.NoMatch`
* Pokud dojde k chybě, vytiskněte chybovou zprávu: `ResultReason.Canceled`

```javascript
switch (result.reason) {
    case ResultReason.RecognizedSpeech:
        console.log(`RECOGNIZED: Text=${result.text}`);
        console.log("    Intent not recognized.");
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

### <a name="continuous-recognition"></a>Průběžné rozpoznávání

Průběžné rozpoznávání je trochu větší než rozpoznávání s jedním prostřelou. Pro získání výsledků rozpoznávání se vyžaduje přihlášení k odběru `Recognizing` `Recognized` událostí, a `Canceled` . Chcete-li zastavit rozpoznávání, je nutné zavolat [`stopContinuousRecognitionAsync`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest&preserve-view=true#stopcontinuousrecognitionasync) . Tady je příklad toho, jak se provádí nepřetržité rozpoznávání na vstupním souboru zvuku.

Pojďme začít definováním vstupu a inicializací [`SpeechRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest&preserve-view=true) :

```javascript
const recognizer = new SpeechRecognizer(speechConfig);
```

Přihlásíme se k odběru událostí odeslaných z [`SpeechRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest&preserve-view=true) .

* [`recognizing`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest&preserve-view=true#recognizing): Signál pro události obsahující mezilehlé výsledky rozpoznávání.
* [`recognized`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest&preserve-view=true#recognized): Signál pro události obsahující konečné výsledky rozpoznávání (indikující úspěšný pokus o uznání).
* [`sessionStopped`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest&preserve-view=true#sessionstopped): Signál pro události indikující konec relace rozpoznávání (operace).
* [`canceled`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest&preserve-view=true#canceled): Signál pro události obsahující zrušené výsledky rozpoznávání (indikující pokus o deaktivaci, který byl zrušen jako výsledek nebo přímý požadavek na zrušení nebo případně i přenos nebo selhání protokolu).

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

S nastavením všeho můžeme zavolat [`startContinuousRecognitionAsync`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest&preserve-view=true#startcontinuousrecognitionasync) .

```javascript
// Starts continuous recognition. Uses stopContinuousRecognitionAsync() to stop recognition.
recognizer.startContinuousRecognitionAsync();

// Something later can call, stops recognition.
// recognizer.StopContinuousRecognitionAsync();
```

### <a name="dictation-mode"></a>Režim diktování

Při použití průběžného rozpoznávání můžete povolit zpracování diktování pomocí odpovídající funkce "Povolit diktování". V tomto režimu bude instance konfigurace řeči interpretovat popisy slov ve strukturách vět, jako je například interpunkční znaménko. Například utterance "provedete to živě ve městě otazník", který se interpretuje jako text "žijete ve městě?".

Chcete-li povolit režim diktování, použijte [`enableDictation`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest&preserve-view=true#enabledictation--) metodu na [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest&preserve-view=true) .

```javascript
speechConfig.enableDictation();
```

## <a name="change-source-language"></a>Změnit jazyk zdroje

Běžným úkolem pro rozpoznávání řeči je zadání vstupu (nebo zdrojového) jazyka. Pojďme se podívat, jak byste změnili vstupní jazyk na italštinu. V kódu Najděte [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest&preserve-view=true) a pak přidejte tento řádek přímo pod ním.

```javascript
speechConfig.speechRecognitionLanguage = "it-IT";
```

[`speechRecognitionLanguage`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest&preserve-view=true#speechrecognitionlanguage)Vlastnost očekává řetězec formátu národního prostředí jazyka. Můžete zadat libovolnou hodnotu ve sloupci **locale (národní prostředí** ) v seznamu podporovaných [národních prostředí a jazyků](../../../language-support.md).

## <a name="improve-recognition-accuracy"></a>Zlepšení přesnosti rozpoznávání

Existuje několik způsobů, jak vylepšit přesnost rozpoznávání pomocí rozpoznávání řeči Pojďme pohlížet na seznamy frází. Seznamy frází slouží k identifikaci známých frází ve zvukových datech, jako je jméno osoby nebo konkrétní umístění. Do seznamu frází lze přidat jednotlivá slova nebo kompletní fráze. Při rozpoznávání se používá záznam v seznamu frází, pokud je do zvukového umístění uvedena přesná shoda celé fráze. Pokud se nenalezne přesná shoda se slovem, rozpoznávání vám nepomáhá.

> [!IMPORTANT]
> Funkce seznamu frází je k dispozici pouze v angličtině.

Chcete-li použít seznam frází, nejprve vytvořte [`PhraseListGrammar`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar?view=azure-node-latest&preserve-view=true) objekt a pak přidejte konkrétní slova a fráze pomocí [`addPhrase`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar?view=azure-node-latest&preserve-view=true#addphrase-string-) .

Všechny změny se projeví [`PhraseListGrammar`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar?view=azure-node-latest&preserve-view=true) při příštím rozpoznávání nebo po opětovném připojení ke službě Speech.

```javascript
const phraseList = PhraseListGrammar.fromRecognizer(recognizer);
phraseList.addPhrase("Supercalifragilisticexpialidocious");
```

Pokud potřebujete vymazat seznam frází:

```javascript
phraseList.clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Další možnosti pro zlepšení přesnosti rozpoznávání

Seznamy frází jsou pouze jedním z možností, jak zlepšit přesnost rozpoznávání. Můžete také: 

* [Zlepšení přesnosti s využitím služby Custom Speech](../../../how-to-custom-speech.md)
* [Zlepšení přesnosti s využitím modelů tenantů](../../../tutorial-tenant-model.md)
