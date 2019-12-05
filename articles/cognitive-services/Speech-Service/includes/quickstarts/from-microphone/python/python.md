---
title: 'Rychlý Start: rozpoznávání řeči na mikrofonu, Python-Speech Service'
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
ms.openlocfilehash: a417c2a269ce66c291eda3c4c76f8d48f0ebad95
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2019
ms.locfileid: "74818970"
---
## <a name="prerequisites"></a>Předpoklady

Než začnete:

> [!div class="checklist"]
> * [Vytvoření prostředku Azure Speech](../../../../get-started.md)
> * [Nastavení vývojového prostředí](../../../../quickstarts/setup-platform.md)
> * [Vytvořit prázdný ukázkový projekt](../../../../quickstarts/create-project.md)
> * Ujistěte se, že máte přístup k mikrofonu pro záznam zvuku

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

> [!NOTE]
> Sada Speech SDK bude standardně rozpoznána pomocí en-US pro daný jazyk. informace o výběru zdrojového jazyka najdete v tématu [určení zdrojového jazyka pro převod řeči na text](../../../../how-to-specify-source-language.md) .

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/python/from-microphone/quickstart.py#code)]

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
   Až budete vyzváni, mluvte několik slov. Text přepisu se za chvíli zobrazuje.

   ![Spustit ukázku](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-run.png)

Pokud máte problémy podle těchto pokynů, přečtěte si podrobný [kurz Visual Studio Code Pythonu](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="next-steps"></a>Další kroky

[!INCLUDE [footer](./footer.md)]
