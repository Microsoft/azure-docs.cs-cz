---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/06/2020
ms.author: dapine
ms.openlocfilehash: 8392e03f272c6db2bb384c8031cb975bca53d530
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2020
ms.locfileid: "79372799"
---
## <a name="prerequisites"></a>Předpoklady

V tomto článku se předpokládá, že máte účet Azure a předplatné služby Speech. Pokud účet a předplatné nemáte, [Vyzkoušejte službu Speech Service zdarma](../../../get-started.md).

## <a name="install-the-speech-sdk"></a>Instalace sady Speech SDK

Předtím, než můžete cokoli udělat, musíte nainstalovat sadu Speech SDK. V závislosti na vaší platformě postupujte podle následujících pokynů:

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=linux&pivots=programming-language-cpp" target="_blank">Linux<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=macos&pivots=programming-language-cpp" target="_blank">macOS<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=windows&pivots=programming-language-cpp" target="_blank">Systému<span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="create-a-speech-configuration"></a>Vytvoření konfigurace řeči

Chcete-li volat službu Speech pomocí sady Speech SDK, je třeba vytvořit [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig). Tato třída obsahuje informace o vašem předplatném, jako je klíč a přidružená oblast, koncový bod, hostitel nebo autorizační token.

> [!NOTE]
> Bez ohledu na to, jestli provádíte rozpoznávání řeči, syntézu řeči, překlad nebo rozpoznávání záměrů, vždy vytvoříte konfiguraci.

Existuje několik způsobů, jak můžete inicializovat [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig):

* S předplatným: předejte klíč a přidruženou oblast.
* S koncovým bodem: předejte koncový bod služby řeči. Klíč nebo autorizační token jsou volitelné.
* S hostitelem: předejte adresu hostitele. Klíč nebo autorizační token jsou volitelné.
* Pomocí autorizačního tokenu: předejte autorizační token a přidruženou oblast.

Pojďme se podívat, jak se vytvoří [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig) pomocí klíče a oblasti.

```cpp
auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

## <a name="initialize-a-recognizer"></a>Inicializovat Nástroj pro rozpoznávání

Po vytvoření [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig)je dalším krokem inicializace [`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer). Když inicializujete [`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer), budete ji muset předat `speech_config`. To poskytuje přihlašovací údaje, které služba Speech vyžaduje k ověření vaší žádosti.

Pokud rozpoznávání řeči rozpoznáte pomocí výchozího mikrofonu vašeho zařízení, [`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer) by měl vypadat takto:

```cpp
auto recognizer = SpeechRecognizer::FromConfig(config);
```

Pokud chcete zadat vstupní zvukové zařízení, budete muset vytvořit [`AudioConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/audio-audioconfig) a zadat `audioConfig` parametr při inicializaci [`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer).

> [!TIP]
> Přečtěte si, [Jak získat ID zařízení pro vstupní zvukové zařízení](../../../how-to-select-audio-input-devices.md).

Nejprve za definice `#include` přidejte následující příkaz `using namespace`.

```cpp
using namespace Microsoft::CognitiveServices::Speech::Audio;
```

Dále budete moci odkazovat na objekt `AudioConfig` následujícím způsobem:

```cpp
auto audioConfig = AudioConfig::FromDefaultMicrophoneInput();
auto recognizer = SpeechRecognizer::FromConfig(config, audioConfig);
```

Pokud chcete místo používání mikrofonu zadat zvukový soubor, budete ještě muset zadat `audioConfig`. Nicméně při vytváření [`AudioConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/audio-audioconfig)namísto volání `FromDefaultMicrophoneInput`zavoláte `FromWavFileOutput` a předáte parametr `filename`.

```cpp
auto audioInput = AudioConfig::FromWavFileInput("YourAudioFile.wav");
auto recognizer = SpeechRecognizer::FromConfig(config, audioInput);
```

## <a name="recognize-speech"></a>Rozpoznávání řeči

[Třída pro rozpoznávání](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer) pro sadu Speech SDK pro C++ poskytuje několik metod, které můžete použít pro rozpoznávání řeči.

* Rozpoznávání bez přípony (Async) – provádí rozpoznávání v neblokujícím (asynchronním) režimu. Tím se rozpozná jeden utterance. Konec jednoho utterance se určuje tak, že naslouchá tichému ukončení na konci nebo dokud se nezpracovává po dobu 15 sekund zvuku.
* Průběžné rozpoznávání (asynchronní) – asynchronně iniciuje operaci průběžného rozpoznávání. Uživatel se musí připojit, aby zpracoval událost pro příjem výsledků rozpoznávání. Chcete-li zastavit asynchronní průběžné rozpoznávání, zavolejte [`StopContinuousRecognitionAsync`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer#stopcontinuousrecognitionasync).

> [!NOTE]
> Přečtěte si další informace o tom, jak [zvolit režim rozpoznávání řeči](../../../how-to-choose-recognition-mode.md).

### <a name="single-shot-recognition"></a>Rozpoznávání s jedním záběrem

Tady je příklad asynchronního samostatného rozpoznávání pomocí [`RecognizeOnceAsync`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer#recognizeonceasync):

```cpp
auto result = recognizer->RecognizeOnceAsync().get();
```

Pro zpracování výsledku budete muset napsat nějaký kód. Tato ukázka vyhodnocuje [`result->Reason`](https://docs.microsoft.com/cpp/cognitive-services/speech/recognitionresult#reason):

* Vytiskne výsledek rozpoznávání: `ResultReason::RecognizedSpeech`
* Pokud se neshoduje žádné rozpoznávání, informujte uživatele: `ResultReason::NoMatch`
* Pokud dojde k chybě, vytiskněte chybovou zprávu: `ResultReason::Canceled`

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

Průběžné rozpoznávání je trochu větší než rozpoznávání s jedním prostřelou. Vyžaduje, abyste se přihlásili k odběru událostí `Recognizing`, `Recognized`a `Canceled`, abyste získali výsledky rozpoznávání. Chcete-li zastavit rozpoznávání, je nutné volat [StopContinuousRecognitionAsync](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer#stopcontinuousrecognitionasync). Tady je příklad toho, jak se provádí nepřetržité rozpoznávání na vstupním souboru zvuku.

Pojďme začít definováním vstupu a inicializací [`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer):

```cpp
auto audioInput = AudioConfig::FromWavFileInput("YourAudioFile.wav");
auto recognizer = SpeechRecognizer::FromConfig(config, audioInput);
```

Nyní vytvoříme proměnnou, která bude spravovat stav rozpoznávání řeči. Pokud chcete začít, deklarujeme `promise<void>`, protože na začátku rozpoznávání můžeme bezpečně předpokládat, že není dokončený.

```cpp
promise<void> recognitionEnd;
```

Přihlásíme se k odběru událostí odeslaných z [`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer).

* [`Recognizing`](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#recognizing): signál pro události obsahující mezilehlé výsledky rozpoznávání.
* [`Recognized`](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#recognized): signál pro události obsahující konečné výsledky rozpoznávání (což znamená úspěšné pokusy o rerozpoznávání).
* [`SessionStopped`](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#sessionstopped): signál pro události indikující konec relace rozpoznávání (operace).
* [`Canceled`](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#canceled): signál pro události obsahující zrušené výsledky rozpoznávání (indikující pokus o deaktivaci, který byl zrušen v důsledku výsledku nebo přímý požadavek na zrušení nebo případně i přenos nebo selhání protokolu).

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

S nastavením všeho můžeme volat [`StopContinuousRecognitionAsync`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer#startcontinuousrecognitionasync).

```cpp
// Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
recognizer->StartContinuousRecognitionAsync().get();

// Waits for recognition end.
recognitionEnd.get_future().get();

// Stops recognition.
recognizer->StopContinuousRecognitionAsync().get();
```

### <a name="dictation-mode"></a>Režim diktování

Při použití průběžného rozpoznávání můžete povolit zpracování diktování pomocí odpovídající funkce "Povolit diktování". V tomto režimu bude instance konfigurace řeči interpretovat popisy slov ve strukturách vět, jako je například interpunkční znaménko. Například utterance "provedete to živě ve městě otazník", který se interpretuje jako text "žijete ve městě?".

Chcete-li povolit režim diktování, použijte metodu [`EnableDictation`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#enabledictation) v [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig).

```cpp
config->EnableDictation();
```

## <a name="change-source-language"></a>Změnit jazyk zdroje

Běžným úkolem pro rozpoznávání řeči je zadání vstupu (nebo zdrojového) jazyka. Pojďme se podívat, jak byste změnili vstupní jazyk na německý. V kódu Najděte [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig)a pak přidejte tento řádek přímo pod ním.

```cpp
config->SetSpeechRecognitionLanguage("fr-FR");
```

[`SetSpeechRecognitionLanguage`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setspeechrecognitionlanguage) je parametr, který jako argument přijímá řetězec. Můžete zadat libovolnou hodnotu v seznamu podporovaných [národních prostředí a jazyků](../../../language-support.md).

## <a name="improve-recognition-accuracy"></a>Zlepšení přesnosti rozpoznávání

Existuje několik způsobů, jak vylepšit přesnost rozpoznávání pomocí sady Speech SDK. Pojďme se podívat na seznamy frází. Seznamy frází slouží k identifikaci známých frází ve zvukových datech, jako je jméno osoby nebo konkrétní umístění. Do seznamu frází lze přidat jednotlivá slova nebo kompletní fráze. Při rozpoznávání se používá záznam v seznamu frází, pokud je do zvukového umístění uvedena přesná shoda celé fráze. Pokud se nenalezne přesná shoda se slovem, rozpoznávání vám nepomáhá.

> [!IMPORTANT]
> Funkce seznamu frází je k dispozici pouze v angličtině.

Chcete-li použít seznam frází, nejprve vytvořte objekt [`PhraseListGrammar`](https://docs.microsoft.com/cpp/cognitive-services/speech/phraselistgrammar) a pak přidejte určitá slova a fráze pomocí [`AddPhrase`](https://docs.microsoft.com/cpp/cognitive-services/speech/phraselistgrammar#addphrase).

Změny [`PhraseListGrammar`](https://docs.microsoft.com/cpp/cognitive-services/speech/phraselistgrammar) se projeví při příštím rozpoznávání nebo po opětovném připojení ke službě Speech.

```cpp
auto phraseListGrammar = PhraseListGrammar::FromRecognizer(recognizer);
phraseListGrammar->AddPhrase("Supercalifragilisticexpialidocious");
```

Pokud potřebujete vymazat seznam frází: 

```cpp
phraseListGrammar->Clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Další možnosti pro zlepšení přesnosti rozpoznávání

Seznamy frází jsou pouze jedním z možností, jak zlepšit přesnost rozpoznávání. Můžete také: 

* [Zlepšení přesnosti pomocí Custom Speech](../../../how-to-custom-speech.md)
* [Zlepšení přesnosti pomocí modelů klientů](../../../tutorial-tenant-model.md)