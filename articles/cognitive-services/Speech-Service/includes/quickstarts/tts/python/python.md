---
title: 'Úvodní příručka: Syntetizační řeč, Python - Služba řeči'
titleSuffix: Azure Cognitive Services
description: Naučte se syntetizovat řeč v Pythonu pomocí sady Speech SDK
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 19/14/2020
ms.author: dapine
ms.openlocfilehash: 58f2b3693b924197dda1e396ae71192776ca276e
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "78924906"
---
## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:

> [!div class="checklist"]
> * [Vytvoření řečového prostředku Azure](../../../../get-started.md)
> * [Nastavení vývojového prostředí a vytvoření prázdného projektu](../../../../quickstarts/setup-platform.md)

## <a name="support-and-updates"></a>Podpora a aktualizace

Aktualizace balíčku Speech SDK Python jsou distribuovány prostřednictvím PyPI a oznámeny v [poznámkách k verzi](~/articles/cognitive-services/Speech-Service/releasenotes.md).
Pokud je k dispozici nová verze, můžete `pip install --upgrade azure-cognitiveservices-speech`ji aktualizovat pomocí příkazu .
Zkontrolujte, která verze je aktuálně `azure.cognitiveservices.speech.__version__` nainstalována kontrolou proměnné.

Pokud máte problém nebo vám chybí funkce, přečtěte si článek [Možnosti podpory a nápovědy](~/articles/cognitive-services/Speech-Service/support.md).

## <a name="create-a-python-application-that-uses-the-speech-sdk"></a>Vytvoření aplikace Pythonu, která používá sadu Speech SDK

### <a name="run-the-sample"></a>Spuštění ukázky

[Ukázkový kód](#sample-code) z tohoto rychlého startu `quickstart.py` můžete zkopírovat do zdrojového souboru a spustit jej v prostředí IDE nebo v konzole:

```sh
python quickstart.py
```

Nebo si můžete stáhnout tento úvodní kurz jako poznámkový blok [Jupyter](https://jupyter.org) z [ukázkového úložiště sady Speech SDK](https://aka.ms/csspeech/samples) a spustit jej jako poznámkový blok.

### <a name="sample-code"></a>Ukázka kódu

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/python/text-to-speech/quickstart.py#code)]

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
   V opačném případě zobrazíte seznam dostupných interpretů Pythonu. Otevřete paletu příkazů (Ctrl+Shift+P) a zadejte **Python: Vyberte interpreta**. Vyberte si vhodnou.
1. Balíček Speech SDK Python můžete nainstalovat z kódu sady Visual Studio. Udělejte to, pokud ještě není nainstalován pro interpret Pythonu, který jste vybrali.
   Chcete-li nainstalovat balíček sady Speech SDK, otevřete terminál. Znovu vyzvěte paletu příkazů (Ctrl+Shift+P) a zadejte **Terminál: Vytvořit nový integrovaný terminál**.
   Do terminálu, který se `python -m pip install azure-cognitiveservices-speech` otevře, zadejte příkaz nebo příslušný příkaz pro váš systém.
1. Chcete-li spustit ukázkový kód, klepněte pravým tlačítkem myši někde uvnitř editoru. Vyberte **Spustit soubor Pythonu v terminálu**.
   Po zobrazení výzvy zadejte text. Syntetizovaný zvuk se přehraje krátce poté.

   ![Spuštění ukázky](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-run-tts.png)

Pokud máte problémy podle těchto pokynů, naleznete v [podrobnějším kurzu aplikace Visual Studio Code Python](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="next-steps"></a>Další kroky

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Viz také

- [Vytvoření vlastního hlasu](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Záznam vlastních hlasových ukázek](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
