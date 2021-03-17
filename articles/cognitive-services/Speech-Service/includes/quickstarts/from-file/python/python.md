---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/20/2020
ms.author: trbye
ms.openlocfilehash: 9365b4bbf6e6fb37156b6e9f24b923591945ea52
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91376411"
---
## <a name="prerequisites"></a>Požadavky

Než začnete, nezapomeňte:

> [!div class="checklist"]
> * [Vytvoření prostředku Azure Speech](../../../../overview.md#try-the-speech-service-for-free)
> * [Vytvoření aplikace LUIS a získání klíče koncového bodu](../../../../quickstarts/create-luis.md)
> * [Nastavení vývojového prostředí a vytvoření prázdného projektu](../../../../quickstarts/setup-platform.md?pivots=programming-language-python)

[!INCLUDE [Audio input format](~/articles/cognitive-services/speech-service/includes/audio-input-format-chart.md)]

## <a name="support-and-updates"></a>Podpora a aktualizace

Aktualizace balíčku Python sady Speech SDK jsou distribuovány prostřednictvím PyPI a oznámeny v [poznámkách k verzi](~/articles/cognitive-services/Speech-Service/releasenotes.md).
Pokud je k dispozici nová verze, můžete na ni pomocí příkazu aktualizovat `pip install --upgrade azure-cognitiveservices-speech` .
Zkontrolováním proměnné zkontrolujte, kterou verzi aktuálně máte nainstalovanou `azure.cognitiveservices.speech.__version__` .

Pokud máte problém nebo nemáte funkci, přečtěte si téma [Podpora a možnosti pomoci](~/articles/cognitive-services/Speech-Service/support.md).

## <a name="create-a-python-application-that-uses-the-speech-sdk"></a>Vytvoření aplikace v Pythonu, která používá sadu Speech SDK

### <a name="run-the-sample"></a>Spuštění ukázky

[Vzorový kód](#sample-code) můžete zkopírovat z tohoto rychlého startu do zdrojového souboru `quickstart.py` a spustit ho v integrovaném vývojovém prostředí nebo v konzole:

```Bash
python quickstart.py
```

Nebo si můžete tento kurz rychlý Start stáhnout jako [Jupyter](https://jupyter.org) notebook z [ukázkového úložiště sady Speech SDK](https://github.com/Azure-Samples/cognitive-services-speech-sdk/) a spustit ho jako Poznámkový blok.

### <a name="sample-code"></a>Ukázka kódu

> [!NOTE]
> Sada Speech SDK bude standardně rozpoznána pomocí en-US pro daný jazyk. informace o výběru zdrojového jazyka najdete v tématu [určení zdrojového jazyka pro převod řeči na text](../../../../how-to-specify-source-language.md) .

```python
import azure.cognitiveservices.speech as speechsdk

# Creates an instance of a speech config with specified subscription key and service region.
# Replace with your own subscription key and region identifier from here: https://aka.ms/speech/sdkregion
speech_key, service_region = "YourSubscriptionKey", "YourServiceRegion"
speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)

# Creates an audio configuration that points to an audio file.
# Replace with your own audio filename.
audio_filename = "whatstheweatherlike.wav"
audio_input = speechsdk.audio.AudioConfig(filename=audio_filename)

# Creates a recognizer with the given settings
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_input)

print("Recognizing first result...")

# Starts speech recognition, and returns after a single utterance is recognized. The end of a
# single utterance is determined by listening for silence at the end or until a maximum of 15
# seconds of audio is processed.  The task returns the recognition text as result. 
# Note: Since recognize_once() returns only a single utterance, it is suitable only for single
# shot recognition like command or query. 
# For long-running multi-utterance recognition, use start_continuous_recognition() instead.
result = speech_recognizer.recognize_once()

# Checks result.
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

### <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>Instalace a použití sady Speech SDK s Visual Studio Code

1. V počítači stáhněte a nainstalujte 64 verze [Pythonu](https://www.python.org/downloads/), 3,5 až 3,8.
1. Stáhněte a nainstalujte si [Visual Studio Code](https://code.visualstudio.com/Download).
1. Otevřete Visual Studio Code a nainstalujte rozšíření Python. **File**  >  **Preferences**  >  V nabídce vyberte**rozšíření** předvoleb souborů. Vyhledejte **Python**.

   ![Instalace rozšíření Python](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-extension.png)

1. Vytvořte složku, do které se má projekt uložit. Příkladem je použití Průzkumníka Windows.
1. V Visual Studio Code vyberte ikonu **souboru** . Pak otevřete složku, kterou jste vytvořili.

   ![Otevření složky](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-open-folder.png)

1. Vytvořte nový zdrojový soubor Pythonu `speechsdk.py` tak, že vyberete ikonu Nový soubor.

   ![Vytvoření souboru](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-newfile.png)

1. Zkopírujte, vložte a uložte [kód Pythonu](#sample-code) do nově vytvořeného souboru.
1. Vložte informace o předplatném služby Speech.
1. Pokud je tato možnost vybrána, na levé straně stavového řádku v dolní části okna se zobrazí interpret Pythonu.
   V opačném případě uveďte seznam dostupných překladačů Pythonu. Otevřete paletu příkazů <kbd>CTRL + SHIFT + P</kbd> a zadejte **Python: vyberte Interpret**. Vyberte vhodný.
1. Balíček python sady Speech SDK můžete nainstalovat v rámci Visual Studio Code. Pokud není ještě nainstalována pro překladač Pythonu, který jste vybrali.
   Chcete-li nainstalovat balíček sady Speech SDK, otevřete terminál. Spusťte znovu paletu příkazů <kbd>CTRL + SHIFT + P</kbd> a zadejte **terminál: vytvořit nový integrovaný terminál**.
   V terminálu, který se otevře, zadejte příkaz `python -m pip install azure-cognitiveservices-speech` nebo příslušný příkaz pro váš systém.
1. Chcete-li spustit vzorový kód, klikněte pravým tlačítkem někam do editoru. Vyberte možnost **Spustit soubor Pythonu v terminálu**.
   V okně konzoly bude rozpoznáno a zaznamenáno prvních 15 sekund vstupu řeči ze zvukového souboru.

   ```console
   Recognizing first result...
   We recognized: What's the weather like?
   ```

Pokud máte problémy podle těchto pokynů, přečtěte si podrobný [kurz Visual Studio Code Pythonu](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="next-steps"></a>Další kroky

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]