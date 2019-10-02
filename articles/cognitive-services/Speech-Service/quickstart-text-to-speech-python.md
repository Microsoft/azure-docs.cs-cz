---
title: 'Rychlý Start: syntetizace řeči, Python-Speech Service'
titleSuffix: Azure Cognitive Services
description: Naučte se, jak pomocí sady Speech SDK syntetizovat řeč v Pythonu.
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/14/2019
ms.author: yulili
ms.openlocfilehash: fed0dea1a562f5ddea05516125a38c690069f446
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803961"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-python"></a>Rychlý Start: syntetizace řeči pomocí sady Speech SDK for Python

K dispozici jsou také rychlé starty pro [rozpoznávání řeči](quickstart-python.md).

Tento článek ukazuje, jak používat hlasové služby prostřednictvím sady Speech SDK pro Python. Ukazuje, jak syntetizovat řeč z textu a přehrát ho s výchozím zvukovým výstupem.

## <a name="prerequisites"></a>Předpoklady

* Klíč předplatného Azure pro hlasové služby. [Získejte je zdarma](get-started.md).
* [Python 3,5 nebo novější](https://www.python.org/downloads/).
* Balíček python Speech SDK je k dispozici pro tyto operační systémy:
    * Windows 10: x64 a x86.
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

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Všimněte si, že tento kurz nebude fungovat s verzí sady SDK starší než 1.7.0.

Tento příkaz nainstaluje balíček Pythonu z [PyPI](https://pypi.org/) pro sadu Speech SDK:

```sh
pip install azure-cognitiveservices-speech
```

## <a name="support-and-updates"></a>Podpora a aktualizace

Aktualizace balíčku Python sady Speech SDK jsou distribuovány prostřednictvím PyPI a oznámeny v [poznámkách k verzi](./releasenotes.md).
Pokud je k dispozici nová verze, můžete na ni aktualizovat příkazem `pip install --upgrade azure-cognitiveservices-speech`.
Kontrolou proměnné `azure.cognitiveservices.speech.__version__` Zkontrolujte, kterou verzi aktuálně máte nainstalovanou.

Pokud máte problém nebo nemáte funkci, přečtěte si téma [Podpora a možnosti pomoci](./support.md).

## <a name="create-a-python-application-that-uses-the-speech-sdk"></a>Vytvoření aplikace v Pythonu, která používá sadu Speech SDK

### <a name="run-the-sample"></a>Spuštění ukázky

[Vzorový kód](#sample-code) z tohoto rychlého startu můžete zkopírovat do zdrojového souboru `quickstart.py` a spustit ho v integrovaném vývojovém prostředí (IDE) nebo v konzole:

```sh
python quickstart.py
```

Nebo si můžete tento kurz rychlý Start stáhnout jako [Jupyter](https://jupyter.org) notebook z [ukázkového úložiště sady Speech SDK](https://aka.ms/csspeech/samples) a spustit ho jako Poznámkový blok.

### <a name="sample-code"></a>Ukázka kódu

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/python/quickstart.py#code)]

### <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>Instalace a použití sady Speech SDK s Visual Studio Code

1. V počítači stáhněte a nainstalujte 64 verze [Pythonu](https://www.python.org/downloads/), 3,5 nebo novější.
1. Stáhněte a nainstalujte [Visual Studio Code](https://code.visualstudio.com/Download).
1. Otevřete Visual Studio Code a nainstalujte rozšíření Python. Z nabídky vyberte **soubor** > **Předvolby** > **rozšíření** . Vyhledejte **Python**.

   ![Instalace rozšíření Python](media/sdk/qs-python-vscode-python-extension.png)

1. Vytvořte složku, do které se má projekt uložit. Příkladem je použití Průzkumníka Windows.
1. V Visual Studio Code vyberte ikonu **souboru** . Pak otevřete složku, kterou jste vytvořili.

   ![Otevření složky](media/sdk/qs-python-vscode-python-open-folder.png)

1. Vytvořte nový zdrojový soubor Pythonu `speechsdk.py`, a to tak, že vyberete ikonu Nový soubor.

   ![Vytvoření souboru](media/sdk/qs-python-vscode-python-newfile.png)

1. Zkopírujte, vložte a uložte [kód Pythonu](#sample-code) do nově vytvořeného souboru.
1. Vložte informace o předplatném služby Speech Services.
1. Pokud je tato možnost vybrána, na levé straně stavového řádku v dolní části okna se zobrazí interpret Pythonu.
   V opačném případě uveďte seznam dostupných překladačů Pythonu. Otevřete paletu příkazů (CTRL + SHIFT + P) a zadejte **Python: vyberte Interpret**. Vyberte vhodný.
1. Balíček python sady Speech SDK můžete nainstalovat v rámci Visual Studio Code. Pokud není ještě nainstalována pro překladač Pythonu, který jste vybrali.
   Chcete-li nainstalovat balíček sady Speech SDK, otevřete terminál. Znovu vyvolejte paletu příkazů (CTRL + SHIFT + P) a zadejte **terminál: vytvořit nový integrovaný terminál**.
   V terminálu, který se otevře, zadejte příkaz `python -m pip install azure-cognitiveservices-speech` nebo příslušný příkaz pro váš systém.
1. Chcete-li spustit vzorový kód, klikněte pravým tlačítkem někam do editoru. Vyberte možnost **Spustit soubor Pythonu v terminálu**.
   Až budete vyzváni, zadejte nějaký text. Syntetizované zvuky se přehrávají krátce po.

   ![Spustit ukázku](media/sdk/qs-python-vscode-python-run-tts.png)

Pokud máte problémy podle těchto pokynů, přečtěte si podrobný [kurz Visual Studio Code Pythonu](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Prozkoumejte ukázky Pythonu na GitHubu](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Další informace najdete v tématech

- [Přizpůsobení hlasových písem](how-to-customize-voice-font.md)
- [Záznam ukázek hlasu](record-custom-voice-samples.md)
