---
title: 'Rychlý start: Rozpoznávání řeči v Pythonu pomocí sady SDK služby řeči'
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak rozpoznávat řeč v Pythonu pomocí sady SDK služby řeči
services: cognitive-services
author: chlandsi
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 12/18/2018
ms.author: chlandsi
ms.openlocfilehash: 7610b12b351b2652df7ade603711d4d92e587292
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53723905"
---
# <a name="quickstart-using-the-speech-service-from-python"></a>Rychlý start: Pomocí služby řeči z Pythonu

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Tento článek ukazuje, jak použít službu rozpoznávání řeči pomocí sadou SDK pro řeč pro Python. To ukazuje, jak rozpoznávání řeči ze vstupu mikrofon.

## <a name="prerequisites"></a>Požadavky

Než začnete, tady je seznam požadovaných součástí:

* Klíč předplatného pro službu rozpoznávání řeči. [Získat zdarma](get-started.md).
* [Python 3.5 (64-bit)](https://www.python.org/downloads/) nebo novější.
* Balíček sadou SDK pro řeč Python je k dispozici pro Windows (x64) Mac (verze macOS X 10.12 nebo novější) a Linux (Ubuntu 16.04 nebo 18.04 ve x64).
* Ubuntu spusťte následující příkazy pro instalaci požadované balíčky:

  ```sh
  sudo apt-get update
  sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2 wget
  ```

* Na Windows, musíte také [Microsoft Visual C++ Redistributable pro Visual Studio 2017](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) pro vaši platformu.

## <a name="get-the-speech-sdk-python-package"></a>Získat balíček Python SDK řeči

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Balíček Cognitive Services řeči SDK Python si můžete nainstalovat pomocí [PyPI](https://pypi.org/) pomocí tohoto příkazu na příkazovém řádku:

```sh
pip install azure-cognitiveservices-speech
```

Aktuální verze sady Cognitive Services Speech SDK je `1.2.0`.

## <a name="support-and-updates"></a>Podpora a aktualizace

Aktualizace balíčku Python SDK řeči se distribuují prostřednictvím PyPI a zveřejněných na [zpráva k vydání verze](./releasenotes.md) stránky.
Pokud je dostupná nová verze, můžete do něj aktualizovat pomocí příkazu `pip install --upgrade azure-cognitiveservices-speech`.
Můžete zkontrolovat, že se podíváte aktuálně nainstalované verze `azure.cognitiveservices.speech.__version__` proměnné.

Pokud máte problém nebo funkce chybí, podíváme se na naše [stránku podpory](./support.md).

## <a name="create-a-python-application-using-the-speech-sdk"></a>Vytvoření aplikace v Pythonu pomocí sadou SDK pro řeč

### <a name="running-the-sample-in-a-terminal"></a>V terminálu spuštěním ukázky

Můžete kopírovat [kód](#quickstart-code) z tohoto rychlého startu do zdrojového souboru `quickstart.py` a ho spustit v prostředí (IDE) nebo v konzole

```sh
python quickstart.py
```

nebo si můžete stáhnout tento rychlý úvodní kurz jako [Jupyter](https://jupyter.org) Poznámkový blok z [úložiště ukázek Cognitive Services řeči](https://github.com/Azure-Samples/cognitive-services-speech-sdk/) a spusťte ho jako poznámkový blok.

### <a name="quickstart-code"></a>Kód tohoto rychlého startu

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/python/quickstart.py#code)]

### <a name="installing-the-speech-sdk-python-package-and-running-the-sample-in-visual-studio-code"></a>Instaluje se balíček Python SDK řeči a spuštění ukázky ve Visual Studio Code

1. [Stáhněte si](https://www.python.org/downloads/) a nainstalujte verzi 64-bit (3.5 nebo novější) aplikace Pythonu ve vašem počítači.
1. [Stáhněte si](https://code.visualstudio.com/Download) a instalace Visual Studio Code.
1. Otevřít Visual Studio Code a rozšíření Python nainstalovat tak, že vyberete **souboru** > **Předvolby** > **rozšíření** z nabídky a hledání "Python".
   ![Instalace rozšíření Pythonu](media/sdk/qs-python-vscode-python-extension.png)
1. Vytvořte složku pro uložení projektu, například pomocí Průzkumníka Windows.
1. Ve Visual Studio Code, klikněte na **souboru** ikonu a potom otevřete složku, které jste vytvořili.
   ![Otevřít složku](media/sdk/qs-python-vscode-python-open-folder.png)
1. Vytvoření nového zdrojového souboru Python `speechsdk.py`, kliknutím na ikonu nový soubor.
   ![Vytvoření souboru](media/sdk/qs-python-vscode-python-newfile.png)
1. Kopírovat, Vložit a uložit [kódu Pythonu](#quickstart-code) na nově vytvořený soubor.
1. Vložte informace o předplatném Speech Service.
1. Pokud už je zvolená interpret Pythonu, zobrazí se na levé straně stavového řádku v dolní části okna.
   V opačném případě můžete klást svoje seznam dostupných interpretů Pythonu tak, že otevřete **paletu příkazů** (`Ctrl+Shift+P`) a zadáním příkazu **Pythonu: Vyberte interpret**a vyberte odpovídající.
1. Pokud dosud není nainstalován balíček Python SDK řeči pro interpret Pythonu, který jste vybrali, to snadno provést z v rámci Visual Studio Code.
   Chcete-li nainstalovat balíček sadou SDK pro řeč, otevřete terminál vyvoláním paletu příkazů znovu (`Ctrl+Shift+P`) a zadáním příkazu **terminálu: Vytvořit nové integrovaný terminál**.
   V terminálu, které se otevře, zadejte příkaz `python -m pip install azure-cognitiveservices-speech`, nebo příslušný příkaz pro váš systém.
1. Spuštění vzorového kódu, klikněte pravým tlačítkem někde v editoru a vyberte **spustit soubor Pythonu, v terminálu**.
   Řekněme, že po zobrazení výzvy pár slov a přepisována text by měl být zobrazí po chvíli později.
   ![Spuštění ukázky](media/sdk/qs-python-vscode-python-run.png)

Pokud existují problémy těchto pokynů, podívejte se na více rozsáhlé [Visual Studio kódu Pythonu pro tento kurz](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Prozkoumejte ukázky Pythonu na Githubu](https://aka.ms/csspeech/samples)
