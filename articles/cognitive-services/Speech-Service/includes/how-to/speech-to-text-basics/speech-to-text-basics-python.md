---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: dapine
ms.openlocfilehash: c8354a67d26210035355f4947cb5f17cee934508
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "79372820"
---
## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá:

* Máte předplatné účtu Azure a služby Rozpoznávání řeči. Pokud nemáte účet a předplatné -- [Vyzkoušejte službu Řeč zdarma](../../../get-started.md).

## <a name="install-and-import-the-speech-sdk"></a>Instalace a import sady Speech SDK

Než budete moci něco udělat, budete muset nainstalovat sadu Speech SDK.

```Python
pip install azure-cognitiveservices-speech
```

Pokud jste na macOS a spustit do problémů s instalací, možná budete muset nejprve spustit tento příkaz.

```Python
python3 -m pip install --upgrade pip
```

Po instalaci sady Speech SDK importujte do projektu Pythonu pomocí tohoto příkazu.

```Python
import azure.cognitiveservices.speech as speechsdk
```

## <a name="create-a-speech-configuration"></a>Vytvoření konfigurace řeči

Chcete-li volat službu Řeč pomocí sady Speech [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python)SDK, je třeba vytvořit . Tato třída obsahuje informace o vašem předplatném, jako je klíč a přidružená oblast, koncový bod, hostitel nebo autorizační token.

> [!NOTE]
> Bez ohledu na to, zda provádíte rozpoznávání řeči, syntézu řeči, překlad nebo záměr rozpoznávání, budete vždy vytvořit konfiguraci.

Existuje několik způsobů, jak můžete inicializovat [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python):

* S předplatným: předat klíč a přidružené oblasti.
* S koncovým bodem: předají v koncovém bodě služby Řeči. Klíč nebo autorizační token je volitelný.
* S hostitelem: předat adresu hostitele. Klíč nebo autorizační token je volitelný.
* S tokenem autorizace: předavte autorizační token a přidruženou oblast.

Podívejme se na to, [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python) jak se vytvoří pomocí klíče a oblasti.

```Python
speech_key, service_region = "YourSubscriptionKey", "YourServiceRegion"
speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)
```

## <a name="initialize-a-recognizer"></a>Inicializovat nástroj pro rozpoznávání

Po vytvoření [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python)je dalším krokem inicializaci . [`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python) Když inicializujete [`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python), budete ji muset `speech_config`předat . To poskytuje pověření, která služba řeči vyžaduje k ověření vašeho požadavku.

Pokud rozpoznávání řeči rozpoznáte pomocí výchozího mikrofonu zařízení, [`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python) mělo by vypadat takto:

```Python
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config)
```

Pokud chcete zadat vstupní zvukové zařízení, budete muset vytvořit [`AudioConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audioconfig?view=azure-python) a `audio_config` zadat parametr při [`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python)inicializaci aplikace .

> [!TIP]
> [Přečtěte si, jak získat ID zařízení pro vstupní zvukové zařízení](../../../how-to-select-audio-input-devices.md).

```Python
audio_config = AudioConfig(device_name="<device id>");
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_config)
```

Pokud chcete místo mikrofonu zadat zvukový soubor, budete muset zadat `audio_config`soubor . Však při vytváření [`AudioConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audioconfig?view=azure-python), namísto `device_name`poskytnutí , budete `filename` používat parametr.

```Python
audio_filename = "whatstheweatherlike.wav"
audio_input = speechsdk.AudioConfig(filename=audio_filename)
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_input)
```

## <a name="recognize-speech"></a>Rozpoznávání řeči

[Třída Rozpoznávání](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python) pro sadu Speech SDK pro Python zveřejňuje několik metod, které můžete použít pro rozpoznávání řeči.

* Jednorázové rozpoznávání (synchronizace) – provádí rozpoznávání v režimu blokování (synchronní). Vrátí po jeden utterance je rozpoznán. Konec jednoho utterance je určena naslouchání pro ticho na konci nebo až do maximálně 15 sekund zvuku je zpracována. Úkol vrátí text rozpoznávání jako výsledek.
* Jednorázové rozpoznávání (asynchronní) - Provádí rozpoznávání v neblokujícím (asynchronním) režimu. Tím rozpoznáte jeden utterance. Konec jednoho utterance je určena naslouchání pro ticho na konci nebo až do maximálně 15 sekund zvuku je zpracována.
* Průběžné rozpoznávání (synchronizace) – synchronně iniciuje průběžné rozpoznávání. Klient se musí `EventSignal` připojit k získání výsledků rozpoznávání. Chcete-li zastavit rozpoznávání, volejte [stop_continuous_recognition()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition--).
* Průběžné rozpoznávání (asynchronní) - Asynchronně iniciuje operaci průběžného rozpoznávání. Uživatel se musí připojit k EventSignal získat výsledky rozpoznávání. Chcete-li zastavit asynchronní průběžné rozpoznávání, volejte [stop_continuous_recognition()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition-async--).

> [!NOTE]
> Přečtěte si další informace o [tom,](../../../how-to-choose-recognition-mode.md)jak zvolit režim rozpoznávání řeči .

### <a name="single-shot-recognition"></a>Rozpoznávání jedním výstřelem

Tady je příklad synchronního rozpoznávání jedním [`recognize_once()`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#recognize-once)výstřelem pomocí :

```Python
result = speech_recognizer.recognize_once()
```

Tady je příklad synchronního rozpoznávání jedním [`recognize_once_async()`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#recognize-once-async------azure-cognitiveservices-speech-resultfuture)výstřelem pomocí :

```Python
result = speech_recognizer.recognize_once_async()
```

Bez ohledu na to, zda jste použili synchronní nebo asynchronní metodu, budete muset napsat nějaký kód k iterátu prostřednictvím výsledku. Tento vzorek vyhodnocuje [`result.reason`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.resultreason?view=azure-python):

* Vytiskne výsledek rozpoznávání:`speechsdk.ResultReason.RecognizedSpeech`
* Pokud neexistuje žádná shoda rozpoznávání, informujte uživatele:`speechsdk.ResultReason.NoMatch `
* Pokud dojde k chybě, vytiskněte chybovou zprávu:`speechsdk.ResultReason.Canceled`

```Python
if result.reason == speechsdk.ResultReason.RecognizedSpeech:
    print("Recognized: {}".format(result.text))
elif result.reason == speechsdk.ResultReason.NoMatch:
    print("No speech could be recognized: {}".format(result.no_match_details))
elif result.reason == speechsdk.ResultReason.Canceled:
    cancellation_details = result.cancellation_details
    print("Speech Recognition canceled: {}".format(cancellation_details.reason))
    if cancellation_details.reason == speechsdk.CancellationReason.Error:
        print("Error details: {}".format(cancellation_details.error_details))
```

### <a name="continuous-recognition"></a>Průběžné rozpoznávání

Průběžné rozpoznávání je o něco více než jednorázové rozpoznávání. Vyžaduje, abyste se `EventSignal` připojili k the k získání výsledků rozpoznávání a chcete-li zastavit rozpoznávání, musíte volat [stop_continuous_recognition()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition--) nebo [stop_continuous_recognition().](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition-async--) Zde je příklad toho, jak se provádí průběžné rozpoznávání zvukového vstupního souboru.

Začněme definováním vstupu a inicializací [`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python):

```Python
audio_config = speechsdk.audio.AudioConfig(filename=weatherfilename)
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_config)
```

Dále vytvoříme proměnnou pro správu stavu rozpoznávání řeči. Chcete-li začít, nastavíme to na `False`, protože na začátku uznání můžeme bezpečně předpokládat, že to není hotové.

```Python
done = False
```

Nyní vytvoříme zpětné volání, které zastaví nepřetržité `evt` rozpoznávání při přijetí. Je tu pár věcí, které je třeba mít na paměti.

* Po `evt` přijetí je `evt` zpráva vytištěna.
* Po `evt` přijetí je volána [stop_continuous_recognition()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition--) k zastavení rozpoznávání.
* Stav rozpoznávání se `True`změní na .

```Python
def stop_cb(evt):
    print('CLOSING on {}'.format(evt))
    speech_recognizer.stop_continuous_recognition()
    nonlocal done
    done = True
```

Tato ukázka kódu ukazuje, jak připojit zpětná volání k událostem odeslaných [`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#start-continuous-recognition--)z .

* [`recognizing`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#recognizing): Signál pro události obsahující průběžné výsledky rozpoznávání.
* [`recognized`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#recognized): Signál pro události obsahující konečné výsledky rozpoznávání (označující úspěšný pokus o rozpoznání).
* [`session_started`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#session-started): Signál pro události označující zahájení relace rozpoznávání (operace).
* [`session_stopped`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#session-stopped): Signál pro události označující konec relace rozpoznávání (operace).
* [`canceled`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#canceled): Signál pro události obsahující zrušené výsledky rozpoznávání (označující pokus o uznání, který byl zrušen v důsledku nebo požadavek na přímé zrušení nebo případně selhání přenosu nebo protokolu).

```Python
speech_recognizer.recognizing.connect(lambda evt: print('RECOGNIZING: {}'.format(evt)))
speech_recognizer.recognized.connect(lambda evt: print('RECOGNIZED: {}'.format(evt)))
speech_recognizer.session_started.connect(lambda evt: print('SESSION STARTED: {}'.format(evt)))
speech_recognizer.session_stopped.connect(lambda evt: print('SESSION STOPPED {}'.format(evt)))
speech_recognizer.canceled.connect(lambda evt: print('CANCELED {}'.format(evt)))

speech_recognizer.session_stopped.connect(stop_cb)
speech_recognizer.canceled.connect(stop_cb)
```

Se vším, co je nastaveno, můžeme volat [start_continuous_recognition()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#session-stopped).

```Python
speech_recognizer.start_continuous_recognition()
while not done:
    time.sleep(.5)
```

### <a name="dictation-mode"></a>Režim diktování

Při použití průběžného rozpoznávání můžete povolit zpracování diktování pomocí odpovídající funkce "povolit diktování". Tento režim způsobí, že instance konfigurace řeči interpretuje popisy slov větných struktur, jako je interpunkce. Například utterance "Žijete ve městě otazník" by být interpretován jako text "Žijete ve městě?".

Chcete-li povolit režim [`enable_dictation()`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#enable-dictation--) diktování, použijte metodu na vašem [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python).

```Python 
SpeechConfig.enable_dictation()
```

## <a name="change-source-language"></a>Změna zdrojového jazyka

Běžnou úlohou pro rozpoznávání řeči je určení vstupního (nebo zdrojového) jazyka. Podívejme se na to, jak byste změnili vstupní jazyk na němčinu. V kódu najděte speechconfig a přidejte tento řádek přímo pod něj.

```Python
speech_config.speech_recognition_language="de-DE"
```

[`speech_recognition_language`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#speech-recognition-language)je parametr, který bere řetězec jako argument. V seznamu podporovaných [národních prostředí/jazyků](../../../language-support.md)můžete zadat libovolnou hodnotu .

## <a name="improve-recognition-accuracy"></a>Zlepšete přesnost rozpoznávání

Existuje několik způsobů, jak zlepšit přesnost rozpoznávání pomocí sady Speech SDK. Podívejme se na seznamy frází. Seznamy frází se používají k identifikaci známých frází ve zvukových datech, jako je jméno osoby nebo konkrétní umístění. Do seznamu frází lze přidat jednotlivá slova nebo úplné fráze. Během rozpoznávání se používá položka v seznamu frází, pokud je do zvuku zahrnuta přesná shoda pro celou frázi. Pokud není nalezena přesná shoda s frází, rozpoznávání není podporováno.

> [!IMPORTANT]
> Funkce Seznam frází je k dispozici pouze v angličtině.

Chcete-li použít seznam frází, nejprve vytvořte [`PhraseListGrammar`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.phraselistgrammar?view=azure-python) objekt [`addPhrase`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.phraselistgrammar?view=azure-python#addphrase-phrase--str-)a pak přidejte určitá slova a fráze s .

Jakékoli změny, které se [`PhraseListGrammar`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.phraselistgrammar?view=azure-python) projeví při dalším rozpoznávání nebo po opětovném připojení ke službě Speech.

```Python
phrase_list_grammar = speechsdk.PhraseListGrammar.from_recognizer(reco)
phrase_list_grammar.addPhrase("Supercalifragilisticexpialidocious")
```

Pokud potřebujete vymazat seznam frází: 

```Python
phrase_list_grammar.clear()
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Další možnosti pro zlepšení přesnosti rozpoznávání

Seznamy frází jsou pouze jednou z možností, jak zlepšit přesnost rozpoznávání. Můžete také: 

* [Zlepšení přesnosti s využitím služby Custom Speech](../../../how-to-custom-speech.md)
* [Zlepšení přesnosti s využitím modelů tenantů](../../../tutorial-tenant-model.md)