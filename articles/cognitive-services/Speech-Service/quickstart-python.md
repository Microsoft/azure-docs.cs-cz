---
title: 'Rychlý start: Rozpoznávání řeči, Python-Speech Service'
titleSuffix: Azure Cognitive Services
description: Pomocí tohoto průvodce můžete vytvořit konzolovou aplikaci v jazyce Speech, která používá sadu Speech SDK for Python. Po dokončení můžete použít mikrofon vašeho počítače a v reálném čase přepisovat řeč na text.
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: chlandsi
ms.openlocfilehash: 6cbe583ca59cb98ba233e58dc665c7e18c2a1f7f
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559288"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-python"></a>Rychlý start: Rozpoznávání řeči pomocí sady Speech SDK for Python

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Tento článek ukazuje, jak používat hlasové služby prostřednictvím sady Speech SDK pro Python. Ukazuje, jak rozpoznávat řeč ze vstupu přes mikrofon.

## <a name="prerequisites"></a>Požadavky

* Klíč předplatného Azure pro hlasové služby. [Získejte je zdarma](get-started.md).
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

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Tento příkaz nainstaluje balíček Pythonu z [PyPI](https://pypi.org/) pro sadu Speech SDK:

```sh
pip install azure-cognitiveservices-speech
```

## <a name="support-and-updates"></a>Podpora a aktualizace

Aktualizace balíčku Python sady Speech SDK jsou distribuovány prostřednictvím PyPI a oznámeny v poznámkách k [verzi](./releasenotes.md).
Pokud je k dispozici nová verze, můžete na ni pomocí příkazu `pip install --upgrade azure-cognitiveservices-speech`aktualizovat.
Zkontrolováním `azure.cognitiveservices.speech.__version__` proměnné zkontrolujte, kterou verzi aktuálně máte nainstalovanou.

Pokud máte problém nebo nemáte funkci, přečtěte si téma [Podpora a možnosti pomoci](./support.md).

## <a name="create-a-python-application-that-uses-the-speech-sdk"></a>Vytvoření aplikace v Pythonu, která používá sadu Speech SDK

### <a name="run-the-sample"></a>Spuštění ukázky

[Vzorový kód](#sample-code) můžete zkopírovat z tohoto rychlého startu do zdrojového souboru `quickstart.py` a spustit ho v integrovaném vývojovém prostředí nebo v konzole:

```sh
python quickstart.py
```

Nebo si můžete tento kurz rychlý Start stáhnout jako [Jupyter](https://jupyter.org) notebook z [ukázkového úložiště sady Speech SDK](https://github.com/Azure-Samples/cognitive-services-speech-sdk/) a spustit ho jako Poznámkový blok.

### <a name="sample-code"></a>Ukázka kódu

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/python/quickstart.py#code)]

### <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>Instalace a použití sady Speech SDK s Visual Studio Code

1. V počítači stáhněte a nainstalujte 64 verze Pythonu [](https://www.python.org/downloads/), 3,5 nebo novější.
1. Stáhněte a nainstalujte [Visual Studio Code](https://code.visualstudio.com/Download).
1. Otevřete Visual Studio Code a nainstalujte rozšíření Python. V nabídce vyberte**rozšíření** **předvoleb** >  **souborů** > . Vyhledejte **Python**.

   ![Instalace rozšíření Python](media/sdk/qs-python-vscode-python-extension.png)

1. Vytvořte složku, do které se má projekt uložit. Příkladem je použití Průzkumníka Windows.
1. V Visual Studio Code vyberte ikonu **souboru** . Pak otevřete složku, kterou jste vytvořili.

   ![Otevření složky](media/sdk/qs-python-vscode-python-open-folder.png)

1. Vytvořte nový zdrojový soubor `speechsdk.py`Pythonu tak, že vyberete ikonu Nový soubor.

   ![Vytvoření souboru](media/sdk/qs-python-vscode-python-newfile.png)

1. Zkopírujte, vložte a uložte [kód Pythonu](#sample-code) do nově vytvořeného souboru.
1. Vložte informace o předplatném služby Speech Services.
1. Pokud je tato možnost vybrána, na levé straně stavového řádku v dolní části okna se zobrazí interpret Pythonu.
   V opačném případě uveďte seznam dostupných překladačů Pythonu. Otevřete paletu příkazů (CTRL + SHIFT + P) a zadejte **Python: Vyberte Interpret**. Vyberte vhodný.
1. Balíček python sady Speech SDK můžete nainstalovat v rámci Visual Studio Code. Pokud není ještě nainstalována pro překladač Pythonu, který jste vybrali.
   Chcete-li nainstalovat balíček sady Speech SDK, otevřete terminál. Znovu zaveďte paletu příkazů (CTRL + SHIFT + P) a zadejte **terminál: Vytvořte nový integrovaný terminál**.
   V terminálu, který se otevře, zadejte příkaz `python -m pip install azure-cognitiveservices-speech` nebo příslušný příkaz pro váš systém.
1. Chcete-li spustit vzorový kód, klikněte pravým tlačítkem někam do editoru. Vyberte možnost **Spustit soubor Pythonu v terminálu**.
   Až budete vyzváni, mluvte několik slov. Text přepisu se za chvíli zobrazuje.

   ![Spustit ukázku](media/sdk/qs-python-vscode-python-run.png)

Pokud máte problémy podle těchto pokynů, přečtěte si podrobný [kurz Visual Studio Code Pythonu](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Prozkoumejte ukázky Pythonu na GitHubu](https://aka.ms/csspeech/samples)
