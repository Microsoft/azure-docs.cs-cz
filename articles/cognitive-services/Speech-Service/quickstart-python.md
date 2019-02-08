---
title: 'Rychlý start: Rozpoznávání řeči, Python – hlasové služby'
titleSuffix: Azure Cognitive Services
description: Tento průvodce vám vytvořit speech to text konzolovou aplikaci, která používá řeč SDK pro Python. Po dokončení můžete použít mikrofon vašeho počítače a v reálném čase přepisovat řeč na text.
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 1/16/2019
ms.author: chlandsi
ms.openlocfilehash: 43ae1339b2b6880656c68957a3d4ed2d2f13b559
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55859536"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-python"></a>Rychlý start: Rozpoznávání řeči se sadou SDK pro řeč pro Python

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Tento článek ukazuje, jak použít službu rozpoznávání řeči pomocí sadou SDK pro řeč pro Python. To ukazuje, jak rozpoznávání řeči ze vstupu mikrofon.

## <a name="prerequisites"></a>Požadavky

* Klíč předplatného pro službu rozpoznávání řeči. [Získat zdarma](get-started.md).
* [Python 3.5 nebo novější](https://www.python.org/downloads/), 64 bitů.
* Balíček sadou SDK pro řeč Python je k dispozici pro tyto operační systémy: 
    * Windows: x64.
    * Mac: macOS X verze 10.12 nebo novější.
    * Linux: Ubuntu 16.04 nebo 18.04 na x64.
* V Ubuntu spusťte následující příkazy, které se nainstalovaly požadované balíčky:

  ```sh
  sudo apt-get update
  sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2 wget
  ```

* Na Windows, musíte také [Microsoft Visual C++ Redistributable pro Visual Studio 2017](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) pro vaši platformu.

## <a name="install-the-speech-sdk"></a>Instalace sady SDK pro řeč

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Tento příkaz nainstaluje balíček Pythonu z [PyPI](https://pypi.org/) pro sadou SDK pro řeč:

```sh
pip install azure-cognitiveservices-speech
```

## <a name="support-and-updates"></a>Podpora a aktualizace 

Aktualizace balíčku Python SDK řeči se distribuují prostřednictvím PyPI a jsme oznámili v [poznámky k verzi](./releasenotes.md).
Pokud je dostupná nová verze, můžete do něj aktualizovat pomocí příkazu `pip install --upgrade azure-cognitiveservices-speech`.
Zkontrolujte, že se podíváte aktuálně nainstalované verze `azure.cognitiveservices.speech.__version__` proměnné. 

Pokud máte potíže nebo máte chybějící funkce, přečtěte si téma [možnosti podporu a nápovědu](./support.md).

## <a name="create-a-python-application-that-uses-the-speech-sdk"></a>Vytvoření aplikace v Pythonu, který používá sadou SDK pro řeč

### <a name="run-the-sample"></a>Spuštění ukázky

Můžete zkopírovat [ukázkový kód](#sample-code) z tohoto rychlého startu do zdrojového souboru `quickstart.py` a ho spustit v prostředí (IDE) nebo v konzole:

```sh
python quickstart.py
```

Nebo si můžete stáhnout jako tento rychlý úvodní kurz [Jupyter](https://jupyter.org) Poznámkový blok z [sadou SDK pro řeč ukázkové úložiště](https://github.com/Azure-Samples/cognitive-services-speech-sdk/) a spusťte ho jako poznámkový blok. 

### <a name="sample-code"></a>Ukázka kódu

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/python/quickstart.py#code)]

### <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>Instalace a používání sadou SDK pro řeč s Visual Studio Code

1. Stáhnout a nainstalovat 64bitové verzi [Python](https://www.python.org/downloads/), 3.5 nebo novější, ve vašem počítači.
1. Stáhněte a nainstalujte [Visual Studio Code](https://code.visualstudio.com/Download).
1. Otevřít Visual Studio Code a nainstalovat rozšíření Python. Vyberte **souboru** > **Předvolby** > **rozšíření** z nabídky. Vyhledejte **Python**.

   ![Instalace rozšíření Pythonu](media/sdk/qs-python-vscode-python-extension.png)

1. Vytvořte složku pro uložení do projektu. Příkladem je pomocí Průzkumníka Windows.
1. Ve Visual Studio Code, vyberte **souboru** ikonu. Potom otevřete složku, kterou jste vytvořili.

   ![Otevřít složku](media/sdk/qs-python-vscode-python-open-folder.png)
   
1. Vytvořte nový zdrojový soubor Pythonu, `speechsdk.py`, tak, že vyberete ikonu nového souboru.

   ![Vytvoření souboru](media/sdk/qs-python-vscode-python-newfile.png)

1. Kopírovat, Vložit a uložit [kódu Pythonu](#sample-code) na nově vytvořený soubor.
1. Vložte informace o předplatném služby řeči.
1. Pokud vybraná, interpret Pythonu se zobrazí na levé straně stavového řádku v dolní části okna.
   V opačném případě otevřete seznam dostupných interpretů Pythonu. Otevřete paletu příkazů (Ctrl + Shift + P) a zadejte **Pythonu: Vyberte interpret**. Vyberte odpovídající ten.
1. Můžete nainstalovat balíček Python SDK řeči z v rámci Visual Studio Code. Udělat, pokud není nainstalovaná, ale pro interpret Pythonu jste vybrali.
   Chcete-li nainstalovat balíček sadou SDK pro řeč, otevřete terminál. Otevřete paletu příkazů znovu (Ctrl + Shift + P) a zadejte **terminálu: Vytvořit nové integrovaný terminál**.
   V terminálu, které se otevře, zadejte příkaz `python -m pip install azure-cognitiveservices-speech` nebo příslušný příkaz pro váš systém.
1. Spuštění ukázkového kódu, klikněte pravým tlačítkem někde v editoru. Vyberte **spustit soubor v jazyce Python, v terminálu**.
   Po zobrazení výzvy, mluvte pár slov. Přepisována text se zobrazí krátce potom.

   ![Spuštění ukázky](media/sdk/qs-python-vscode-python-run.png)

Pokud máte problémy, postupujte podle těchto pokynů, podívejte se na další rozsáhlé [Visual Studio kódu Pythonu pro tento kurz](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Prozkoumejte ukázky Pythonu na Githubu](https://aka.ms/csspeech/samples)
