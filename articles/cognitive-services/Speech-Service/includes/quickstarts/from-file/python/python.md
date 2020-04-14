---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/20/2020
ms.author: dapine
ms.openlocfilehash: a6fa385faeb757b86b9a59b3a3c8197cccc5ac83
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81274748"
---
## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:

> [!div class="checklist"]
> * [Vytvoření prostředku řeči Azure](../../../../get-started.md)
> * [Vytvoření aplikace LUIS a získání klíče koncového bodu](../../../../quickstarts/create-luis.md)
> * [Nastavení vývojového prostředí a vytvoření prázdného projektu](../../../../quickstarts/setup-platform.md?pivots=programming-language-python)

[!INCLUDE [Audio input format](~/articles/cognitive-services/speech-service/includes/audio-input-format-chart.md)]

## <a name="support-and-updates"></a>Podpora a aktualizace

Aktualizace balíčku Speech SDK Python jsou distribuovány prostřednictvím PyPI a oznámeny v [poznámkách k verzi](~/articles/cognitive-services/Speech-Service/releasenotes.md).
Pokud je k dispozici nová verze, můžete `pip install --upgrade azure-cognitiveservices-speech`ji aktualizovat pomocí příkazu .
Zkontrolujte, která verze je aktuálně `azure.cognitiveservices.speech.__version__` nainstalována kontrolou proměnné.

Pokud máte problém nebo vám chybí funkce, přečtěte si článek [Možnosti podpory a nápovědy](~/articles/cognitive-services/Speech-Service/support.md).

## <a name="create-a-python-application-that-uses-the-speech-sdk"></a>Vytvoření aplikace Pythonu, která používá sadu Speech SDK

### <a name="run-the-sample"></a>Spuštění ukázky

[Ukázkový kód](#sample-code) z tohoto rychlého startu `quickstart.py` můžete zkopírovat do zdrojového souboru a spustit jej v prostředí IDE nebo v konzole:

```Bash
python quickstart.py
```

Nebo si můžete stáhnout tento úvodní kurz jako poznámkový blok [Jupyter](https://jupyter.org) z [ukázkového úložiště sady Speech SDK](https://github.com/Azure-Samples/cognitive-services-speech-sdk/) a spustit jej jako poznámkový blok.

### <a name="sample-code"></a>Ukázka kódu

> [!NOTE]
> Sada Speech SDK bude ve výchozím nastavení rozpoznána použití jazyka en-us, viz [Určení zdrojového jazyka pro řeč na text,](../../../../how-to-specify-source-language.md) kde naleznete informace o výběru zdrojového jazyka.

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

### <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>Instalace a použití sady Speech SDK s kódem sady Visual Studio

1. Stáhněte a nainstalujte do počítače 64bitovou verzi [Pythonu](https://www.python.org/downloads/)3,5 až 3,8.
1. Stáhněte a nainstalujte [kód sady Visual Studio](https://code.visualstudio.com/Download).
1. Otevřete visual studio kód a nainstalujte rozšíření Pythonu. V nabídce vyberte**Přípony** **předvoleb** >  **souborů.** >  Vyhledejte **Python**.

   ![Instalace rozšíření Pythonu](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-extension.png)

1. Vytvořte složku pro uložení projektu. Příkladem je použití Průzkumníka Windows.
1. V kódu Visual Studia vyberte ikonu **Soubor.** Pak otevřete složku, kterou jste vytvořili.

   ![Otevření složky](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-open-folder.png)

1. Vytvořte nový zdrojový `speechsdk.py`soubor Pythonu výběrem ikony nového souboru.

   ![Vytvoření souboru](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-newfile.png)

1. Zkopírujte, vložte a uložte [kód Pythonu](#sample-code) do nově vytvořeného souboru.
1. Vložte informace o předplatném služby Speech.
1. Pokud je tato možnost vybrána, zobrazí se na levé straně stavového řádku v dolní části okna interpret Pythonu.
   V opačném případě zobrazíte seznam dostupných interpretů Pythonu. Otevřete paletu příkazů <kbd>Ctrl+Shift+P</kbd> a zadejte **Python: Vyberte interpreta**. Vyberte si vhodnou.
1. Balíček Speech SDK Python můžete nainstalovat z kódu sady Visual Studio. Udělejte to, pokud ještě není nainstalován pro interpret Pythonu, který jste vybrali.
   Chcete-li nainstalovat balíček sady Speech SDK, otevřete terminál. Znovu vyzvěte paletu příkazů <kbd>Ctrl+Shift+P</kbd> a zadejte **Terminál: Vytvořit nový integrovaný terminál**.
   Do terminálu, který se `python -m pip install azure-cognitiveservices-speech` otevře, zadejte příkaz nebo příslušný příkaz pro váš systém.
1. Chcete-li spustit ukázkový kód, klepněte pravým tlačítkem myši někde uvnitř editoru. Vyberte **Spustit soubor Pythonu v terminálu**.
   Prvních 15 sekund hlasového vstupu ze zvukového souboru bude rozpoznáno a zaznamenáno v okně konzoly.

   ```console
   Recognizing first result...
   We recognized: What's the weather like?
   ```

Pokud máte problémy podle těchto pokynů, naleznete v [podrobnějším kurzu aplikace Visual Studio Code Python](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="next-steps"></a>Další kroky

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]