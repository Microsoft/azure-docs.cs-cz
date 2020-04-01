---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: dapine
ms.openlocfilehash: c2b474c9d6485bdba31412435f4edbdd2383b3c0
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80501820"
---
## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že máte účet Azure a předplatné služby Rozpoznávání řeči. Pokud nemáte účet a předplatné, [vyzkoušejte bezplatnou službu Řeč](../../../get-started.md).

## <a name="install-the-speech-sdk"></a>Instalace sady Speech SDK

Než budete moci něco udělat, budete muset nainstalovat sadu Speech SDK. V závislosti na platformě postupujte podle následujících pokynů:

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=jre&pivots=programming-language-java" target="_blank">Java Runtime<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=android&pivots=programming-language-java" target="_blank">Android<span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="create-a-speech-configuration"></a>Vytvoření konfigurace řeči

Chcete-li volat službu Řeč pomocí sady Speech [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable)SDK, je třeba vytvořit . Tato třída obsahuje informace o vašem předplatném, jako je klíč a přidružená oblast, koncový bod, hostitel nebo autorizační token.

> [!NOTE]
> Bez ohledu na to, zda provádíte rozpoznávání řeči, syntézu řeči, překlad nebo záměr rozpoznávání, budete vždy vytvořit konfiguraci.

Existuje několik způsobů, jak můžete inicializovat [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable):

* S předplatným: předat klíč a přidružené oblasti.
* S koncovým bodem: předají v koncovém bodě služby Řeči. Klíč nebo autorizační token je volitelný.
* S hostitelem: předat adresu hostitele. Klíč nebo autorizační token je volitelný.
* S tokenem autorizace: předavte autorizační token a přidruženou oblast.

Podívejme se na to, [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable) jak se vytvoří pomocí klíče a oblasti. Na stránce [podpory oblasti](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) najdete identifikátor oblasti.

```java
SpeechConfig config = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

## <a name="initialize-a-recognizer"></a>Inicializovat nástroj pro rozpoznávání

Po vytvoření [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable)je dalším krokem inicializaci . [`SpeechRecognizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable) Když inicializujete [`SpeechRecognizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable), budete ji muset `config`předat . To poskytuje pověření, která služba řeči vyžaduje k ověření vašeho požadavku.

Pokud rozpoznávání řeči rozpoznáte pomocí výchozího mikrofonu zařízení, [`SpeechRecognizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable) mělo by vypadat takto:

```java
SpeechRecognizer recognizer = new SpeechRecognizer(config);
```

Pokud chcete zadat vstupní zvukové zařízení, budete muset vytvořit [`AudioConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.audio.audioconfig?view=azure-java-stable) a `audioConfig` zadat parametr při [`SpeechRecognizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable)inicializaci aplikace .

> [!TIP]
> [Přečtěte si, jak získat ID zařízení pro vstupní zvukové zařízení](../../../how-to-select-audio-input-devices.md).

Nejprve přidejte `import` následující příkazy.

```java
import java.util.concurrent.Future;
import com.microsoft.cognitiveservices.speech.*;
```

Dále budete moci odkazovat na `AudioConfig` objekt takto:

```java
AudioConfig audioConfig = AudioConfig.fromDefaultMicrophoneInput();
SpeechRecognizer recognizer = new SpeechRecognizer(config, audioConfig);
```

Pokud chcete místo mikrofonu zadat zvukový soubor, budete muset zadat `audioConfig`soubor . Však při vytváření [`AudioConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.audio.audioconfig?view=azure-java-stable), namísto volání `fromDefaultMicrophoneInput`, `fromWavFileOutput` budete `filename` volat a předat parametr.

```java
AudioConfig audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
SpeechRecognizer recognizer = new SpeechRecognizer(config, audioConfig);
```

## <a name="recognize-speech"></a>Rozpoznávání řeči

[Třída Rozpoznávání](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable) pro sadu Speech SDK pro jazyk Java zveřejňuje několik metod, které můžete použít pro rozpoznávání řeči.

* Jednorázové rozpoznávání (asynchronní) - Provádí rozpoznávání v neblokujícím (asynchronním) režimu. Tím rozpoznáte jeden utterance. Konec jednoho utterance je určena naslouchání pro ticho na konci nebo až do maximálně 15 sekund zvuku je zpracována.
* Průběžné rozpoznávání (asynchronní) - Asynchronně iniciuje operaci průběžného rozpoznávání. Pokud chcete místo mikrofonu zadat zvukový soubor, budete muset zadat. Chcete-li zastavit asynchronní průběžné rozpoznávání, volejte [stopContinuousRecognitionAsync](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.stopcontinuousrecognitionasync).

> [!NOTE]
> Přečtěte si další informace o [tom,](../../../how-to-choose-recognition-mode.md)jak zvolit režim rozpoznávání řeči .

### <a name="single-shot-recognition"></a>Rozpoznávání jedním výstřelem

Tady je příklad asynchronního rozpoznávání jedním [`recognizeOnceAsync`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.recognizeonceasync?view=azure-java-stable)výstřelem pomocí :

```java
Future<SpeechRecognitionResult> task = recognizer.recognizeOnceAsync();
SpeechRecognitionResult result = task.get();
```

Budete muset napsat nějaký kód pro zpracování výsledku. Tento vzorek vyhodnocuje [`result.getReason()`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.resultreason?view=azure-java-stable):

* Vytiskne výsledek rozpoznávání:`ResultReason.RecognizedSpeech`
* Pokud neexistuje žádná shoda rozpoznávání, informujte uživatele:`ResultReason.NoMatch`
* Pokud dojde k chybě, vytiskněte chybovou zprávu:`ResultReason.Canceled`

```java
switch (result.getReason()) {
    case ResultReason.RecognizedSpeech:
        System.out.println("We recognized: " + result.getText());
        exitCode = 0;
        break;
    case ResultReason.NoMatch:
        System.out.println("NOMATCH: Speech could not be recognized.");
        break;
    case ResultReason.Canceled: {
            CancellationDetails cancellation = CancellationDetails.fromResult(result);
            System.out.println("CANCELED: Reason=" + cancellation.getReason());

            if (cancellation.getReason() == CancellationReason.Error) {
                System.out.println("CANCELED: ErrorCode=" + cancellation.getErrorCode());
                System.out.println("CANCELED: ErrorDetails=" + cancellation.getErrorDetails());
                System.out.println("CANCELED: Did you update the subscription info?");
            }
        }
        break;
}
```

### <a name="continuous-recognition"></a>Průběžné rozpoznávání

Průběžné rozpoznávání je o něco více než jednorázové rozpoznávání. Vyžaduje, abyste se `recognizing`přihlásili k odběru , `recognized`a `canceled` události získat výsledky rozpoznávání. Chcete-li zastavit rozpoznávání, musíte zavolat [`stopContinuousRecognitionAsync`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.stopcontinuousrecognitionasync). Zde je příklad toho, jak se provádí průběžné rozpoznávání zvukového vstupního souboru.

Začněme definováním vstupu a inicializací [`SpeechRecognizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable):

```java
AudioConfig audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
SpeechRecognizer recognizer = new SpeechRecognizer(config, audioConfig);
```

Dále vytvoříme proměnnou pro správu stavu rozpoznávání řeči. Chcete-li začít, budeme `Semaphore` deklarovat na oboru třídy.

```java
private static Semaphore stopTranslationWithFileSemaphore;
```

Přihlásíme se k odběru událostí [`SpeechRecognizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable)odeslaných z aplikace .

* [`recognizing`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.recognizing?view=azure-java-stable): Signál pro události obsahující průběžné výsledky rozpoznávání.
* [`recognized`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.recognized?view=azure-java-stable): Signál pro události obsahující konečné výsledky rozpoznávání (označující úspěšný pokus o rozpoznání).
* [`sessionStopped`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.recognizer.sessionstopped?view=azure-java-stable): Signál pro události označující konec relace rozpoznávání (operace).
* [`canceled`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.canceled?view=azure-java-stable): Signál pro události obsahující zrušené výsledky rozpoznávání (označující pokus o uznání, který byl zrušen v důsledku nebo požadavek na přímé zrušení nebo případně selhání přenosu nebo protokolu).

```java
// First initialize the semaphore.
stopTranslationWithFileSemaphore = new Semaphore(0);

recognizer.recognizing.addEventListener((s, e) -> {
    System.out.println("RECOGNIZING: Text=" + e.getResult().getText());
});

recognizer.recognized.addEventListener((s, e) -> {
    if (e.getResult().getReason() == ResultReason.RecognizedSpeech) {
        System.out.println("RECOGNIZED: Text=" + e.getResult().getText());
    }
    else if (e.getResult().getReason() == ResultReason.NoMatch) {
        System.out.println("NOMATCH: Speech could not be recognized.");
    }
});

recognizer.canceled.addEventListener((s, e) -> {
    System.out.println("CANCELED: Reason=" + e.getReason());

    if (e.getReason() == CancellationReason.Error) {
        System.out.println("CANCELED: ErrorCode=" + e.getErrorCode());
        System.out.println("CANCELED: ErrorDetails=" + e.getErrorDetails());
        System.out.println("CANCELED: Did you update the subscription info?");
    }

    stopTranslationWithFileSemaphore.release();
});

recognizer.sessionStopped.addEventListener((s, e) -> {
    System.out.println("\n    Session stopped event.");
    stopTranslationWithFileSemaphore.release();
});
```

Se vším, co je [`stopContinuousRecognitionAsync`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.stopcontinuousrecognitionasync)nastaveno, můžeme zavolat .

```java
// Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
recognizer.startContinuousRecognitionAsync().get();

// Waits for completion.
stopTranslationWithFileSemaphore.acquire();

// Stops recognition.
recognizer.stopContinuousRecognitionAsync().get();
```

### <a name="dictation-mode"></a>Režim diktování

Při použití průběžného rozpoznávání můžete povolit zpracování diktování pomocí odpovídající funkce "povolit diktování". Tento režim způsobí, že instance konfigurace řeči interpretuje popisy slov větných struktur, jako je interpunkce. Například utterance "Žijete ve městě otazník" by být interpretován jako text "Žijete ve městě?".

Chcete-li povolit režim [`enableDictation`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.enabledictation?view=azure-java-stable) diktování, použijte metodu na vašem [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable).

```java
config.enableDictation();
```

## <a name="change-source-language"></a>Změna zdrojového jazyka

Běžnou úlohou pro rozpoznávání řeči je určení vstupního (nebo zdrojového) jazyka. Podívejme se na to, jak byste změnili vstupní jazyk na francouzštinu. V kódu najděte [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable)svůj , pak přidejte tento řádek přímo pod ním.

```java
config.setSpeechRecognitionLanguage("fr-FR");
```

[`setSpeechRecognitionLanguage`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setspeechrecognitionlanguage?view=azure-java-stable)je parametr, který bere řetězec jako argument. V seznamu podporovaných [národních prostředí/jazyků](../../../language-support.md)můžete zadat libovolnou hodnotu .

## <a name="improve-recognition-accuracy"></a>Zlepšete přesnost rozpoznávání

Existuje několik způsobů, jak zlepšit přesnost rozpoznávání pomocí sady Speech SDK. Podívejme se na seznamy frází. Seznamy frází se používají k identifikaci známých frází ve zvukových datech, jako je jméno osoby nebo konkrétní umístění. Do seznamu frází lze přidat jednotlivá slova nebo úplné fráze. Během rozpoznávání se používá položka v seznamu frází, pokud je do zvuku zahrnuta přesná shoda pro celou frázi. Pokud není nalezena přesná shoda s frází, rozpoznávání není podporováno.

> [!IMPORTANT]
> Funkce Seznam frází je k dispozici pouze v angličtině.

Chcete-li použít seznam frází, nejprve vytvořte [`PhraseListGrammar`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.phraselistgrammar?view=azure-java-stable) objekt [`AddPhrase`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.phraselistgrammar.addphrase?view=azure-java-stable#com_microsoft_cognitiveservices_speech_PhraseListGrammar_addPhrase_String_)a pak přidejte určitá slova a fráze s .

Jakékoli změny, které se [`PhraseListGrammar`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.phraselistgrammar?view=azure-java-stable) projeví při dalším rozpoznávání nebo po opětovném připojení ke službě Speech.

```java
PhraseListGrammar phraseList = PhraseListGrammar.fromRecognizer(recognizer);
phraseList.addPhrase("Supercalifragilisticexpialidocious");
```

Pokud potřebujete vymazat seznam frází: 

```java
phraseList.clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Další možnosti pro zlepšení přesnosti rozpoznávání

Seznamy frází jsou pouze jednou z možností, jak zlepšit přesnost rozpoznávání. Můžete také: 

* [Zlepšení přesnosti s využitím služby Custom Speech](../../../how-to-custom-speech.md)
* [Zlepšení přesnosti s využitím modelů tenantů](../../../tutorial-tenant-model.md)