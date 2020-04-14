---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/04/2020
ms.author: dapine
ms.openlocfilehash: 34d9dc2bdf99737e6b4c17abe37a18f795c19df8
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81274943"
---
## <a name="prerequisites"></a>Požadavky

* Klíč předplatného Azure pro službu Řeč. [Získejte jeden zdarma](~/articles/cognitive-services/Speech-Service/get-started.md).
* [Python 3.5 až 3.8](https://www.python.org/downloads/).
* Balíček Python Speech SDK je k dispozici pro tyto operační systémy:
    * Windows: x64 a x86.
    * Mac: macOS X verze 10.12 nebo novější.
    * Linux: Ubuntu 16.04/18.04, Debian 9, RHEL 7/8, CentOS 7/8 na x64.
* V Linuxu spusťte tyto příkazy a nainstalujte požadované balíčky:

# <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2
```

# <a name="debian-9"></a>[Debian 9](#tab/debian)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.2 libasound2
```

# <a name="rhel--centos-8"></a>[RHEL / CentOS 8](#tab/rhel-centos)

```Bash
sudo yum update
sudo yum install alsa-lib openssl python3
```

> [!NOTE]
> Na RHEL/CentOS 7/8 postupujte podle [pokynů, jak nakonfigurovat OpenSSL pro Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

---

* V systému Windows potřebujete pro vaši platformu [redistribuovatelný microsoft visual c++ pro Visual Studio 2019.](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)

## <a name="install-the-speech-sdk"></a>Instalace sady Speech SDK

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Tento příkaz nainstaluje balíček Pythonu z [PyPI](https://pypi.org/) pro sadu Speech SDK:

```Bash
pip install azure-cognitiveservices-speech
```

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

````python
import azure.cognitiveservices.speech as speechsdk

# Replace with your own subscription key and region identifier from here: https://aka.ms/speech/sdkregion
speech_key, service_region = "YourSubscriptionKey", "YourServiceRegion"
speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)

# Creates an audio configuration that points to an audio file.
# Replace with your own audio filename.
audio_filename = "helloworld.wav"
audio_output = speechsdk.audio.AudioOutputConfig(filename=audio_filename)

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
   V opačném případě zobrazíte seznam dostupných interpretů Pythonu. Otevřete paletu příkazů (<kbd>Ctrl+Shift+P</kbd>) a zadejte **Python: Vyberte interpreta**. Vyberte si vhodnou.
1. Balíček Speech SDK Python můžete nainstalovat z kódu sady Visual Studio. Udělejte to, pokud ještě není nainstalován pro interpret Pythonu, který jste vybrali.
   Chcete-li nainstalovat balíček sady Speech SDK, otevřete terminál. Znovu vyzvěte paletu příkazů<kbd>(Ctrl+Shift+P)</kbd>a zadejte **Terminál: Vytvořit nový integrovaný terminál**.
   Do terminálu, který se `python -m pip install azure-cognitiveservices-speech` otevře, zadejte příkaz nebo příslušný příkaz pro váš systém.
1. Chcete-li spustit ukázkový kód, klepněte pravým tlačítkem myši někde uvnitř editoru. Vyberte **Spustit soubor Pythonu v terminálu**.
   Text se převede na řeč a uloží se do zadaných zvukových dat.

   ```console
   Speech synthesized to [helloworld.wav] for text [Hello world!]
   ```

Pokud máte problémy podle těchto pokynů, naleznete v [podrobnějším kurzu aplikace Visual Studio Code Python](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="next-steps"></a>Další kroky

[!INCLUDE [Speech synthesis basics](../../text-to-speech-next-steps.md)]

## <a name="see-also"></a>Viz také

- [Vytvoření vlastního hlasu](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Záznam vlastních hlasových ukázek](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
