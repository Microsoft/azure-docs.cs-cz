---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/06/2020
ms.author: dapine
ms.openlocfilehash: 0c0fa3e07e0cdae6ce28fcd1521b63cb2575767f
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80501685"
---
## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že máte účet Azure a předplatné služby Rozpoznávání řeči. Pokud nemáte účet a předplatné, [vyzkoušejte bezplatnou službu Řeč](../../../get-started.md).

## <a name="install-the-speech-sdk"></a>Instalace sady Speech SDK

Než budete moci něco udělat, budete muset nainstalovat sadu Speech SDK. V závislosti na platformě postupujte podle následujících pokynů:

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=linux&pivots=programming-language-cpp" target="_blank">Linux<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=macos&pivots=programming-language-cpp" target="_blank">Macos<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=windows&pivots=programming-language-cpp" target="_blank">Windows<span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="create-a-speech-configuration"></a>Vytvoření konfigurace řeči

Chcete-li volat službu Řeč pomocí sady Speech [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig)SDK, je třeba vytvořit . Tato třída obsahuje informace o vašem předplatném, jako je klíč a přidružená oblast, koncový bod, hostitel nebo autorizační token.

> [!NOTE]
> Bez ohledu na to, zda provádíte rozpoznávání řeči, syntézu řeči, překlad nebo záměr rozpoznávání, budete vždy vytvořit konfiguraci.

Existuje několik způsobů, jak můžete inicializovat [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig):

* S předplatným: předat klíč a přidružené oblasti.
* S koncovým bodem: předají v koncovém bodě služby Řeči. Klíč nebo autorizační token je volitelný.
* S hostitelem: předat adresu hostitele. Klíč nebo autorizační token je volitelný.
* S tokenem autorizace: předavte autorizační token a přidruženou oblast.

Podívejme se na to, [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig) jak se vytvoří pomocí klíče a oblasti. Na stránce [podpory oblasti](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) najdete identifikátor oblasti.

```cpp
auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

## <a name="initialize-a-recognizer"></a>Inicializovat nástroj pro rozpoznávání

Po vytvoření [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig)je dalším krokem inicializaci . [`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer) Když inicializujete [`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer), budete ji muset `speech_config`předat . To poskytuje pověření, která služba řeči vyžaduje k ověření vašeho požadavku.

Pokud rozpoznávání řeči rozpoznáte pomocí výchozího mikrofonu zařízení, [`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer) mělo by vypadat takto:

```cpp
auto recognizer = SpeechRecognizer::FromConfig(config);
```

Pokud chcete zadat vstupní zvukové zařízení, budete muset vytvořit [`AudioConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/audio-audioconfig) a `audioConfig` zadat parametr při [`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer)inicializaci aplikace .

> [!TIP]
> [Přečtěte si, jak získat ID zařízení pro vstupní zvukové zařízení](../../../how-to-select-audio-input-devices.md).

Nejprve přidejte `using namespace` následující `#include` příkaz za definice.

```cpp
using namespace Microsoft::CognitiveServices::Speech::Audio;
```

Dále budete moci odkazovat na `AudioConfig` objekt takto:

```cpp
auto audioConfig = AudioConfig::FromDefaultMicrophoneInput();
auto recognizer = SpeechRecognizer::FromConfig(config, audioConfig);
```

Pokud chcete místo mikrofonu zadat zvukový soubor, budete muset zadat `audioConfig`soubor . Však při vytváření [`AudioConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/audio-audioconfig), namísto volání `FromDefaultMicrophoneInput`, `FromWavFileOutput` budete `filename` volat a předat parametr.

```cpp
auto audioInput = AudioConfig::FromWavFileInput("YourAudioFile.wav");
auto recognizer = SpeechRecognizer::FromConfig(config, audioInput);
```

## <a name="recognize-speech"></a>Rozpoznávání řeči

[Třída Rozpoznávání](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer) pro sadu Speech SDK pro C++ zveřejňuje několik metod, které můžete použít pro rozpoznávání řeči.

* Jednorázové rozpoznávání (asynchronní) - Provádí rozpoznávání v neblokujícím (asynchronním) režimu. Tím rozpoznáte jeden utterance. Konec jednoho utterance je určena naslouchání pro ticho na konci nebo až do maximálně 15 sekund zvuku je zpracována.
* Průběžné rozpoznávání (asynchronní) - Asynchronně iniciuje operaci průběžného rozpoznávání. Uživatel se musí připojit ke zpracování události pro příjem výsledků rozpoznávání. Chcete-li zastavit asynchronní [`StopContinuousRecognitionAsync`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer#stopcontinuousrecognitionasync)průběžné rozpoznávání, volejte .

> [!NOTE]
> Přečtěte si další informace o [tom,](../../../how-to-choose-recognition-mode.md)jak zvolit režim rozpoznávání řeči .

### <a name="single-shot-recognition"></a>Rozpoznávání jedním výstřelem

Tady je příklad asynchronního rozpoznávání jedním [`RecognizeOnceAsync`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer#recognizeonceasync)výstřelem pomocí :

```cpp
auto result = recognizer->RecognizeOnceAsync().get();
```

Budete muset napsat nějaký kód pro zpracování výsledku. Tento vzorek vyhodnocuje [`result->Reason`](https://docs.microsoft.com/cpp/cognitive-services/speech/recognitionresult#reason):

* Vytiskne výsledek rozpoznávání:`ResultReason::RecognizedSpeech`
* Pokud neexistuje žádná shoda rozpoznávání, informujte uživatele:`ResultReason::NoMatch`
* Pokud dojde k chybě, vytiskněte chybovou zprávu:`ResultReason::Canceled`

```cpp
switch (result->Reason)
{
    case ResultReason::RecognizedSpeech:
        cout << "We recognized: " << result->Text << std::endl;
        break;
    case ResultReason::NoMatch:
        cout << "NOMATCH: Speech could not be recognized." << std::endl;
        break;
    case ResultReason::Canceled:
        {
            auto cancellation = CancellationDetails::FromResult(result);
            cout << "CANCELED: Reason=" << (int)cancellation->Reason << std::endl;
    
            if (cancellation->Reason == CancellationReason::Error) {
                cout << "CANCELED: ErrorCode= " << (int)cancellation->ErrorCode << std::endl;
                cout << "CANCELED: ErrorDetails=" << cancellation->ErrorDetails << std::endl;
                cout << "CANCELED: Did you update the subscription info?" << std::endl;
            }
        }
        break;
    default:
        break;
}
```

### <a name="continuous-recognition"></a>Průběžné rozpoznávání

Průběžné rozpoznávání je o něco více než jednorázové rozpoznávání. Vyžaduje, abyste se `Recognizing`přihlásili k odběru , `Recognized`a `Canceled` události získat výsledky rozpoznávání. Chcete-li zastavit rozpoznávání, musíte volat [StopContinuousRecognitionAsync](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer#stopcontinuousrecognitionasync). Zde je příklad toho, jak se provádí průběžné rozpoznávání zvukového vstupního souboru.

Začněme definováním vstupu a inicializací [`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer):

```cpp
auto audioInput = AudioConfig::FromWavFileInput("YourAudioFile.wav");
auto recognizer = SpeechRecognizer::FromConfig(config, audioInput);
```

Dále vytvoříme proměnnou pro správu stavu rozpoznávání řeči. Chcete-li začít, budeme `promise<void>`deklarovat , protože na začátku uznání můžeme bezpečně předpokládat, že to není hotové.

```cpp
promise<void> recognitionEnd;
```

Přihlásíme se k odběru událostí [`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer)odeslaných z aplikace .

* [`Recognizing`](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#recognizing): Signál pro události obsahující průběžné výsledky rozpoznávání.
* [`Recognized`](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#recognized): Signál pro události obsahující konečné výsledky rozpoznávání (označující úspěšný pokus o rozpoznání).
* [`SessionStopped`](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#sessionstopped): Signál pro události označující konec relace rozpoznávání (operace).
* [`Canceled`](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#canceled): Signál pro události obsahující zrušené výsledky rozpoznávání (označující pokus o uznání, který byl zrušen v důsledku nebo požadavek na přímé zrušení nebo případně selhání přenosu nebo protokolu).

```cpp
recognizer->Recognizing.Connect([](const SpeechRecognitionEventArgs& e)
    {
        cout << "Recognizing:" << e.Result->Text << std::endl;
    });

recognizer->Recognized.Connect([](const SpeechRecognitionEventArgs& e)
    {
        if (e.Result->Reason == ResultReason::RecognizedSpeech)
        {
            cout << "RECOGNIZED: Text=" << e.Result->Text 
                 << " (text could not be translated)" << std::endl;
        }
        else if (e.Result->Reason == ResultReason::NoMatch)
        {
            cout << "NOMATCH: Speech could not be recognized." << std::endl;
        }
    });

recognizer->Canceled.Connect([&recognitionEnd](const SpeechRecognitionCanceledEventArgs& e)
    {
        cout << "CANCELED: Reason=" << (int)e.Reason << std::endl;
        if (e.Reason == CancellationReason::Error)
        {
            cout << "CANCELED: ErrorCode=" << (int)e.ErrorCode << "\n"
                 << "CANCELED: ErrorDetails=" << e.ErrorDetails << "\n"
                 << "CANCELED: Did you update the subscription info?" << std::endl;

            recognitionEnd.set_value(); // Notify to stop recognition.
        }
    });

recognizer->SessionStopped.Connect([&recognitionEnd](const SessionEventArgs& e)
    {
        cout << "Session stopped.";
        recognitionEnd.set_value(); // Notify to stop recognition.
    });
```

Se vším, co je [`StopContinuousRecognitionAsync`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer#startcontinuousrecognitionasync)nastaveno, můžeme zavolat .

```cpp
// Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
recognizer->StartContinuousRecognitionAsync().get();

// Waits for recognition end.
recognitionEnd.get_future().get();

// Stops recognition.
recognizer->StopContinuousRecognitionAsync().get();
```

### <a name="dictation-mode"></a>Režim diktování

Při použití průběžného rozpoznávání můžete povolit zpracování diktování pomocí odpovídající funkce "povolit diktování". Tento režim způsobí, že instance konfigurace řeči interpretuje popisy slov větných struktur, jako je interpunkce. Například utterance "Žijete ve městě otazník" by být interpretován jako text "Žijete ve městě?".

Chcete-li povolit režim [`EnableDictation`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#enabledictation) diktování, použijte metodu na vašem [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig).

```cpp
config->EnableDictation();
```

## <a name="change-source-language"></a>Změna zdrojového jazyka

Běžnou úlohou pro rozpoznávání řeči je určení vstupního (nebo zdrojového) jazyka. Podívejme se na to, jak byste změnili vstupní jazyk na němčinu. V kódu najděte [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig)svůj , pak přidejte tento řádek přímo pod ním.

```cpp
config->SetSpeechRecognitionLanguage("fr-FR");
```

[`SetSpeechRecognitionLanguage`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setspeechrecognitionlanguage)je parametr, který bere řetězec jako argument. V seznamu podporovaných [národních prostředí/jazyků](../../../language-support.md)můžete zadat libovolnou hodnotu .

## <a name="improve-recognition-accuracy"></a>Zlepšete přesnost rozpoznávání

Existuje několik způsobů, jak zlepšit přesnost rozpoznávání pomocí sady Speech SDK. Podívejme se na seznamy frází. Seznamy frází se používají k identifikaci známých frází ve zvukových datech, jako je jméno osoby nebo konkrétní umístění. Do seznamu frází lze přidat jednotlivá slova nebo úplné fráze. Během rozpoznávání se používá položka v seznamu frází, pokud je do zvuku zahrnuta přesná shoda pro celou frázi. Pokud není nalezena přesná shoda s frází, rozpoznávání není podporováno.

> [!IMPORTANT]
> Funkce Seznam frází je k dispozici pouze v angličtině.

Chcete-li použít seznam frází, nejprve vytvořte [`PhraseListGrammar`](https://docs.microsoft.com/cpp/cognitive-services/speech/phraselistgrammar) objekt [`AddPhrase`](https://docs.microsoft.com/cpp/cognitive-services/speech/phraselistgrammar#addphrase)a pak přidejte určitá slova a fráze s .

Jakékoli změny, které se [`PhraseListGrammar`](https://docs.microsoft.com/cpp/cognitive-services/speech/phraselistgrammar) projeví při dalším rozpoznávání nebo po opětovném připojení ke službě Speech.

```cpp
auto phraseListGrammar = PhraseListGrammar::FromRecognizer(recognizer);
phraseListGrammar->AddPhrase("Supercalifragilisticexpialidocious");
```

Pokud potřebujete vymazat seznam frází: 

```cpp
phraseListGrammar->Clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Další možnosti pro zlepšení přesnosti rozpoznávání

Seznamy frází jsou pouze jednou z možností, jak zlepšit přesnost rozpoznávání. Můžete také: 

* [Zlepšení přesnosti s využitím služby Custom Speech](../../../how-to-custom-speech.md)
* [Zlepšení přesnosti s využitím modelů tenantů](../../../tutorial-tenant-model.md)