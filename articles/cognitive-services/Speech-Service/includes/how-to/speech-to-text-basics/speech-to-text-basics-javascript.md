---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/15/2020
ms.author: trbye
ms.openlocfilehash: b11194640c4d049c90f85974022908dce6b4fd79
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399800"
---
## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že máte účet Azure a předplatné služby Rozpoznávání řeči. Pokud nemáte účet a předplatné, [vyzkoušejte bezplatnou službu Řeč](../../../get-started.md).

## <a name="install-the-speech-sdk"></a>Instalace sady Speech SDK

Než budete moci něco udělat, budete muset nainstalovat <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">sadu <span class="docon docon-navigate-external x-hidden-focus"> </span>JavaScript Speech SDK </a>. V závislosti na platformě postupujte podle následujících pokynů:

- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Node.js<span 
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">Webový prohlížeč<span class="docon docon-navigate-external x-hidden-focus"></span></a>

Navíc v závislosti na cílovém prostředí použijte jednu z následujících možností:

# <a name="import"></a>[Import](#tab/import)

```javascript
import {
    AudioConfig,
    CancellationDetails,
    CancellationReason,
    PhraseListGrammar,
    ResultReason,
    SpeechConfig,
    SpeechRecognizer
} from "microsoft-cognitiveservices-speech-sdk";
```

Další informace `import`naleznete v tématu <a href="https://javascript.info/import-export" target="_blank">Export a import <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

# <a name="require"></a>[Vyžadují](#tab/require)

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

Další informace `require`o tématu naleznete v tématu <a href="https://nodejs.org/en/knowledge/getting-started/what-is-require/" target="_blank">Co je potřeba? <span class="docon docon-navigate-external x-hidden-focus"></span></a>.


# <a name="script"></a>[Skript](#tab/script)

Stáhněte a extrahujte sadu <a href="https://aka.ms/csspeech/jsbrowserpackage" target="_blank">JavaScript Speech <span class="docon docon-navigate-external x-hidden-focus"></span> SDK</a> *microsoft.cognitiveservices.speech.bundle.js* a umístěte ji do složky přístupné souboru HTML.

```html
<script src="microsoft.cognitiveservices.speech.bundle.js"></script>;
```

> [!TIP]
> Pokud cílíte na webový prohlížeč `<script>` a používáte značku; `sdk` předpona není potřeba. Předpona `sdk` je alias používaný `require` k pojmenování modulu.

---

## <a name="create-a-speech-configuration"></a>Vytvoření konfigurace řeči

Chcete-li volat službu Řeč pomocí sady Speech [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest)SDK, je třeba vytvořit . Tato třída obsahuje informace o vašem předplatném, jako je klíč a přidružená oblast, koncový bod, hostitel nebo autorizační token.

> [!NOTE]
> Bez ohledu na to, zda provádíte rozpoznávání řeči, syntézu řeči, překlad nebo záměr rozpoznávání, budete vždy vytvořit konfiguraci.

Existuje několik způsobů, jak můžete inicializovat [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest):

* S předplatným: předat klíč a přidružené oblasti.
* S koncovým bodem: předají v koncovém bodě služby Řeči. Klíč nebo autorizační token je volitelný.
* S hostitelem: předat adresu hostitele. Klíč nebo autorizační token je volitelný.
* S tokenem autorizace: předavte autorizační token a přidruženou oblast.

Podívejme se na to, [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest) jak se vytvoří pomocí klíče a oblasti. Na stránce [podpory oblasti](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) najdete identifikátor oblasti.

```javascript
const speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

## <a name="initialize-a-recognizer"></a>Inicializovat nástroj pro rozpoznávání

Po vytvoření [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest)je dalším krokem inicializaci . [`SpeechRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest) Když inicializujete [`SpeechRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest), budete ji muset `speechConfig`předat . To poskytuje pověření, která služba řeči vyžaduje k ověření vašeho požadavku.

Pokud rozpoznávání řeči rozpoznáte pomocí výchozího mikrofonu zařízení, [`SpeechRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest) mělo by vypadat takto:

```javascript
const recognizer = new SpeechRecognizer(speechConfig);
```

Pokud chcete zadat vstupní zvukové zařízení, budete muset vytvořit [`AudioConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?view=azure-node-latest) a `audioConfig` zadat parametr při [`SpeechRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest)inicializaci aplikace .

> [!TIP]
> [Přečtěte si, jak získat ID zařízení pro vstupní zvukové zařízení](../../../how-to-select-audio-input-devices.md).

Odkazujte `AudioConfig` na objekt následovně:

```javascript
const audioConfig = AudioConfig.fromDefaultMicrophoneInput();
const speechConfig = SpeechConfig.fromSubscription(speechConfig, audioConfig);
```

Pokud chcete místo mikrofonu zadat zvukový soubor, budete muset zadat `audioConfig`soubor . To však lze provést pouze při cílení **Node.js** [`AudioConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?view=azure-node-latest)a `fromDefaultMicrophoneInput`při vytváření , `fromWavFileOutput` namísto `filename` volání , budete volat a předat parametr.

```javascript
const audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
const speechConfig = SpeechConfig.fromSubscription(speechConfig, audioConfig);
```

## <a name="recognize-speech"></a>Rozpoznávání řeči

[Třída Rozpoznávání](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest) pro sadu Speech SDK pro C# zpřístupňuje několik metod, které můžete použít pro rozpoznávání řeči.

* Jednorázové rozpoznávání (asynchronní) - Provádí rozpoznávání v neblokujícím (asynchronním) režimu. Tím rozpoznáte jeden utterance. Konec jednoho utterance je určena naslouchání pro ticho na konci nebo až do maximálně 15 sekund zvuku je zpracována.
* Průběžné rozpoznávání (asynchronní) - Asynchronně iniciuje operaci průběžného rozpoznávání. Uživatel se zaregistruje na události a zpracovává různé stavy aplikace. Chcete-li zastavit asynchronní [`stopContinuousRecognitionAsync`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#stopcontinuousrecognitionasync)průběžné rozpoznávání, volejte .

> [!NOTE]
> Přečtěte si další informace o [tom,](../../../how-to-choose-recognition-mode.md)jak zvolit režim rozpoznávání řeči .

### <a name="single-shot-recognition"></a>Rozpoznávání jedním výstřelem

Tady je příklad asynchronního rozpoznávání jedním [`recognizeOnceAsync`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#recognizeonceasync)výstřelem pomocí :

```javascript
recognizer.recognizeOnceAsync(result => {
    // Interact with result
});
```

Budete muset napsat nějaký kód pro zpracování výsledku. Tento vzorek vyhodnocuje [`result.reason`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognitionresult?view=azure-node-latest#reason):

* Vytiskne výsledek rozpoznávání:`ResultReason.RecognizedSpeech`
* Pokud neexistuje žádná shoda rozpoznávání, informujte uživatele:`ResultReason.NoMatch`
* Pokud dojde k chybě, vytiskněte chybovou zprávu:`ResultReason.Canceled`

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
}
```

### <a name="continuous-recognition"></a>Průběžné rozpoznávání

Průběžné rozpoznávání je o něco více než jednorázové rozpoznávání. Vyžaduje, abyste se `Recognizing`přihlásili k odběru , `Recognized`a `Canceled` události získat výsledky rozpoznávání. Chcete-li zastavit rozpoznávání, musíte zavolat [`stopContinuousRecognitionAsync`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#stopcontinuousrecognitionasync). Zde je příklad toho, jak se provádí průběžné rozpoznávání zvukového vstupního souboru.

Začněme definováním vstupu a inicializací [`SpeechRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest):

```javascript
const recognizer = new SpeechRecognizer(speechConfig);
```

Přihlásíme se k odběru událostí [`SpeechRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest)odeslaných z aplikace .

* [`recognizing`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#recognizing): Signál pro události obsahující průběžné výsledky rozpoznávání.
* [`recognized`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#recognized): Signál pro události obsahující konečné výsledky rozpoznávání (označující úspěšný pokus o rozpoznání).
* [`sessionStopped`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#sessionstopped): Signál pro události označující konec relace rozpoznávání (operace).
* [`canceled`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#canceled): Signál pro události obsahující zrušené výsledky rozpoznávání (označující pokus o uznání, který byl zrušen v důsledku nebo požadavek na přímé zrušení nebo případně selhání přenosu nebo protokolu).

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

Se vším, co je [`stopContinuousRecognitionAsync`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#stopcontinuousrecognitionasync)nastaveno, můžeme zavolat .

```javascript
// Starts continuous recognition. Uses stopContinuousRecognitionAsync() to stop recognition.
recognizer.startContinuousRecognitionAsync();

// Something later can call, stops recognition.
// recognizer.StopContinuousRecognitionAsync();
```

### <a name="dictation-mode"></a>Režim diktování

Při použití průběžného rozpoznávání můžete povolit zpracování diktování pomocí odpovídající funkce "povolit diktování". Tento režim způsobí, že instance konfigurace řeči interpretuje popisy slov větných struktur, jako je interpunkce. Například utterance "Žijete ve městě otazník" by být interpretován jako text "Žijete ve městě?".

Chcete-li povolit režim [`enableDictation`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest#enabledictation--) diktování, použijte metodu na vašem [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest).

```javascript
speechConfig.enableDictation();
```

## <a name="change-source-language"></a>Změna zdrojového jazyka

Běžnou úlohou pro rozpoznávání řeči je určení vstupního (nebo zdrojového) jazyka. Podívejme se na to, jak byste změnili vstupní jazyk na italštinu. V kódu najděte [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest)svůj , pak přidejte tento řádek přímo pod ním.

```javascript
speechConfig.speechRecognitionLanguage = "it-IT";
```

Vlastnost [`speechRecognitionLanguage`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest#speechrecognitionlanguage) očekává řetězec formátu jazykového národního prostředí. Libovolnou hodnotu můžete zadat ve **sloupci Národní prostředí** v seznamu podporovaných [národních prostředí/jazyků](../../../language-support.md).

## <a name="improve-recognition-accuracy"></a>Zlepšete přesnost rozpoznávání

Existuje několik způsobů, jak zlepšit přesnost rozpoznávání s řeč Pojďme se podívat na seznamy frází. Seznamy frází se používají k identifikaci známých frází ve zvukových datech, jako je jméno osoby nebo konkrétní umístění. Do seznamu frází lze přidat jednotlivá slova nebo úplné fráze. Během rozpoznávání se používá položka v seznamu frází, pokud je do zvuku zahrnuta přesná shoda pro celou frázi. Pokud není nalezena přesná shoda s frází, rozpoznávání není podporováno.

> [!IMPORTANT]
> Funkce Seznam frází je k dispozici pouze v angličtině.

Chcete-li použít seznam frází, nejprve vytvořte [`PhraseListGrammar`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar?view=azure-node-latest) objekt [`addPhrase`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar?view=azure-node-latest#addphrase-string-)a pak přidejte určitá slova a fráze s .

Jakékoli změny, které se [`PhraseListGrammar`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar?view=azure-node-latest) projeví při dalším rozpoznávání nebo po opětovném připojení ke službě Speech.

```javascript
const phraseList = PhraseListGrammar.fromRecognizer(recognizer);
phraseList.addPhrase("Supercalifragilisticexpialidocious");
```

Pokud potřebujete vymazat seznam frází:

```javascript
phraseList.clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Další možnosti pro zlepšení přesnosti rozpoznávání

Seznamy frází jsou pouze jednou z možností, jak zlepšit přesnost rozpoznávání. Můžete také: 

* [Zlepšení přesnosti s využitím služby Custom Speech](../../../how-to-custom-speech.md)
* [Zlepšení přesnosti s využitím modelů tenantů](../../../tutorial-tenant-model.md)