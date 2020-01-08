---
title: 'Rychlý Start: syntetizace řeči do zvukového souboru, Python-Speech Service'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 07/05/2019
ms.author: chlandsi
ms.openlocfilehash: d82b2904f16cc9816a22caadba598a7a44bfa007
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75467621"
---
## <a name="prerequisites"></a>Požadavky

* Klíč předplatného Azure pro službu Speech Service. [Získejte je zdarma](~/articles/cognitive-services/Speech-Service/get-started.md).
* [Python 3,5 nebo novější](https://www.python.org/downloads/).
* Balíček python Speech SDK je k dispozici pro tyto operační systémy:
    * Windows: x64 a x86.
    * Mac: macOS X verze 10,12 nebo novější.
    * Linux: Ubuntu 16,04, Ubuntu 18,04, Debian 9 na platformě x64.
* V systému Linux spuštěním těchto příkazů nainstalujte požadované balíčky:

  * V Ubuntu:

    ```sh
    sudo apt-get update
    sudo apt-get install build-essential libssl1.0.0 libasound2
    ```

  * V Debian 9:

    ```sh
    sudo apt-get update
    sudo apt-get install build-essential libssl1.0.2 libasound2
    ```

* V systému Windows budete potřebovat [Microsoft Visual C++ Redistributable pro Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) pro vaši platformu.

## <a name="install-the-speech-sdk"></a>Instalace sady Speech SDK

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Tento příkaz nainstaluje balíček Pythonu z [PyPI](https://pypi.org/) pro sadu Speech SDK:

```sh
pip install azure-cognitiveservices-speech
```

## <a name="support-and-updates"></a>Podpora a aktualizace

Aktualizace balíčku Python sady Speech SDK jsou distribuovány prostřednictvím PyPI a oznámeny v [poznámkách k verzi](~/articles/cognitive-services/Speech-Service/releasenotes.md).
Pokud je k dispozici nová verze, můžete na ni pomocí příkazu `pip install --upgrade azure-cognitiveservices-speech`aktualizovat.
Kontrolou proměnné `azure.cognitiveservices.speech.__version__` zkontrolujte, která verze je aktuálně nainstalovaná.

Pokud máte problém nebo nemáte funkci, přečtěte si téma [Podpora a možnosti pomoci](~/articles/cognitive-services/Speech-Service/support.md).

## <a name="create-a-python-application-that-uses-the-speech-sdk"></a>Vytvoření aplikace v Pythonu, která používá sadu Speech SDK

### <a name="run-the-sample"></a>Spuštění ukázky

[Vzorový kód](#sample-code) z tohoto rychlého startu můžete zkopírovat do zdrojového souboru `quickstart.py` a spustit ho v integrovaném vývojovém prostředí nebo v konzole:

```sh
python quickstart.py
```

Nebo si můžete tento kurz rychlý Start stáhnout jako [Jupyter](https://jupyter.org) notebook z [ukázkového úložiště sady Speech SDK](https://github.com/Azure-Samples/cognitive-services-speech-sdk/) a spustit ho jako Poznámkový blok.

### <a name="sample-code"></a>Ukázka kódu

````Python

import azure.cognitiveservices.speech as speechsdk

# Creates an instance of a speech config with specified subscription key and service region.
# Replace with your own subscription key and service region (e.g., "westus").
speech_key, service_region = "YourSubscriptionKey", "YourServiceRegion"
speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)

# Creates an audio configuration that points to an audio file.
# Replace with your own audio filename.
audio_filename = "helloworld.wav"
audio_output = speechsdk.AudioOutputConfig(filename=audio_filename)

# Creates a synthesizer with the given settings
speech_synthesizer = speechsdk.SpeechSynthesizer(speech_config=speech_config, audio_config=audio_output)

# Synthesizes the text to speech.
# Replace with your own text.
text = "Hello world!"
result = speech_synthesizer.speak_text_async(text).get()

# Checks result.
if result.reason == speechsdk.ResultReason.SynthesizingAudioCompleted:
    print("Speech synthesized to [{}] for text [{}]".format(audio_filename, text))
elif result.reason == speechsdk.ResultReason.Canceled:
    cancellation_details = result.cancellation_details
    print("Speech synthesis canceled: {}".format(cancellation_details.reason))
    if cancellation_details.reason == speechsdk.CancellationReason.Error:
        if cancellation_details.error_details:
            print("Error details: {}".format(cancellation_details.error_details))
    print("Did you update the subscription info?")
````

### <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>Instalace a použití sady Speech SDK s Visual Studio Code

1. V počítači stáhněte a nainstalujte 64 verze [Pythonu](https://www.python.org/downloads/), 3,5 nebo novější.
1. Stáhněte a nainstalujte [Visual Studio Code](https://code.visualstudio.com/Download).
1. Otevřete Visual Studio Code a nainstalujte rozšíření Python. Z nabídky vyberte **soubor** > **Předvolby** > **rozšíření** . Vyhledejte **Python**.

   ![Instalace rozšíření Python](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-extension.png)

1. Vytvořte složku, do které se má projekt uložit. Příkladem je použití Průzkumníka Windows.
1. V Visual Studio Code vyberte ikonu **souboru** . Pak otevřete složku, kterou jste vytvořili.

   ![Otevření složky](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-open-folder.png)

1. Vytvořte nový zdrojový soubor Pythonu `speechsdk.py`, a to tak, že vyberete ikonu Nový soubor.

   ![Vytvoření souboru](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-newfile.png)

1. Zkopírujte, vložte a uložte [kód Pythonu](#sample-code) do nově vytvořeného souboru.
1. Vložte informace o předplatném služby Speech.
1. Pokud je tato možnost vybrána, na levé straně stavového řádku v dolní části okna se zobrazí interpret Pythonu.
   V opačném případě uveďte seznam dostupných překladačů Pythonu. Otevřete paletu příkazů (CTRL + SHIFT + P) a zadejte **Python: vyberte Interpret**. Vyberte vhodný.
1. Balíček python sady Speech SDK můžete nainstalovat v rámci Visual Studio Code. Pokud není ještě nainstalována pro překladač Pythonu, který jste vybrali.
   Chcete-li nainstalovat balíček sady Speech SDK, otevřete terminál. Znovu vyvolejte paletu příkazů (CTRL + SHIFT + P) a zadejte **terminál: vytvořit nový integrovaný terminál**.
   V terminálu, který se otevře, zadejte příkaz `python -m pip install azure-cognitiveservices-speech` nebo příslušný příkaz pro váš systém.
1. Chcete-li spustit vzorový kód, klikněte pravým tlačítkem někam do editoru. Vyberte možnost **Spustit soubor Pythonu v terminálu**.
   Váš text se převede na řeč a uloží se do zadaného zvukového data.

   ```text
   Speech synthesized to [helloworld.wav] for text [Hello world!]
   ```

Pokud máte problémy podle těchto pokynů, přečtěte si podrobný [kurz Visual Studio Code Pythonu](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="next-steps"></a>Další kroky

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Další informace najdete v tématech

- [Vytvoření vlastního hlasu](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Zaznamenat vlastní ukázky hlasu](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
