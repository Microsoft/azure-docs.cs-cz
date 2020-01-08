---
title: 'Rychlý Start: sada Speech SDK pro Python – nastavení platformy – Speech Service'
titleSuffix: Azure Cognitive Services
description: Pomocí tohoto průvodce můžete nastavit platformu pro používání Pythonu se sadou Speech Service SDK.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/09/2019
ms.author: erhopf
ms.openlocfilehash: 37b0deba6531c8e714b8786f8c815cf323c310ba
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75466945"
---
V této příručce se dozvíte, jak nainstalovat [sadu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) pro Python.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="supported-operating-systems"></a>Podporované operační systémy

- Balíček python Speech SDK je k dispozici pro tyto operační systémy:
  - Windows: x64 a x86
  - Mac: macOS X verze 10,12 nebo novější
  - Linux: Ubuntu 16,04, Ubuntu 18,04, Debian 9 na platformě x64

## <a name="prerequisites"></a>Požadavky

- Podporované platformy Linux budou vyžadovat, aby byly nainstalovány určité knihovny (`libssl` pro podporu technologie Secure Sockets Layer a `libasound2` pro podporu zvuku). Pro příkazy potřebné k instalaci správných verzí těchto knihoven použijte níže uvedenou distribuci.

  - V Ubuntu spusťte následující příkazy, abyste nainstalovali požadované balíčky:

        ```sh
        sudo apt-get update
        sudo apt-get install build-essential libssl1.0.0 libasound2
        ```

  - V Debian 9 spusťte následující příkazy, abyste nainstalovali požadované balíčky:

        ```sh
        sudo apt-get update
        sudo apt-get install build-essential libssl1.0.2 libasound2
        ```

- V systému Windows budete potřebovat [Microsoft Visual C++ Redistributable pro Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) pro vaši platformu. Mějte na paměti, že pokud tuto instalaci nainstalujete poprvé, může před pokračováním v této příručce vyžadovat restartování systému Windows.
- A nakonec budete potřebovat [Python 3,5 nebo novější](https://www.python.org/downloads/). Chcete-li ověřit instalaci, otevřete příkazový řádek a zadejte příkaz `python --version` a podívejte se na výsledek. Pokud je správně nainstalovaná, dostanete odpověď "Python 3.5.1" nebo podobnou.

## <a name="install-the-speech-sdk-using-visual-studio-code"></a>Instalace sady Speech SDK pomocí Visual Studio Code

1. Stáhněte a nainstalujte nejnovější podporovanou verzi [Pythonu](https://www.python.org/downloads/) pro vaši platformu, 3,5 nebo novější.
   - Uživatelé systému Windows při instalaci nezapomeňte v procesu instalace vybrat přidat Python do vaší cesty.
1. Stáhněte a nainstalujte [Visual Studio Code](https://code.visualstudio.com/Download).
1. Otevřete Visual Studio Code a nainstalujte rozšíření Python. Z nabídky vyberte **soubor** > **Předvolby** > **rozšíření** . Vyhledejte **Python** a klikněte na **nainstalovat**.

   ![Instalace rozšíření Python](~/articles/cognitive-services/speech-service/media/sdk/qs-python-vscode-python-extension.png)

1. V rámci Visual Studio Code také nainstalujte sadu Speech SDK Python Package z integrovaného příkazového řádku:
   1. Otevření terminálu (z rozevíracích nabídek, **terminálu** > **nového terminálu**)
   1. V terminálu, který se otevře, zadejte příkaz `python -m pip install azure-cognitiveservices-speech`

To je to, že jste připravení začít kódovat do sady Speech SDK v Pythonu a můžete přejít na [Další kroky](#next-steps) níže. Pokud s Visual Studio Code začínáte, přečtěte si rozsáhlejší [Visual Studio Code dokumentaci](https://code.visualstudio.com/docs). Další informace o Visual Studio Code a Pythonu najdete v [kurzu Visual Studio Code Pythonu](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="install-the-speech-sdk-using-the-command-line"></a>Instalace sady Speech SDK pomocí příkazového řádku

Pokud nepoužíváte Visual Studio Code, následující příkaz nainstaluje balíček Pythonu z [PyPI](https://pypi.org/) pro sadu Speech SDK. Pro uživatele Visual Studio Code přejděte na další dílčí část.

```sh
pip install azure-cognitiveservices-speech
```

Pokud používáte macOS, možná budete muset spustit následující příkaz, abyste mohli použít příkaz `pip` výše:

```sh
python3 -m pip install --upgrade pip
```

Po úspěšném použití `pip` k instalaci `azure-cognitiveservices-speech`můžete použít sadu Speech SDK tak, že obor názvů naimportujete do projektů Pythonu. Příklad:

```py
import azure.cognitiveservices.speech as speechsdk
```

Tento postup je podrobněji zobrazen v příkladech kódu uvedených v [následujících krocích](#next-steps) níže.

## <a name="support-and-updates"></a>Podpora a aktualizace

Aktualizace balíčku Python sady Speech SDK jsou distribuovány prostřednictvím PyPI a oznámeny v [poznámkách k verzi](~/articles/cognitive-services/speech-service/releasenotes.md).
Pokud je k dispozici nová verze, můžete na ni pomocí příkazu `pip install --upgrade azure-cognitiveservices-speech`aktualizovat.
Kontrolou proměnné `azure.cognitiveservices.speech.__version__` zkontrolujte, která verze je aktuálně nainstalovaná.

Pokud máte problém nebo nemáte funkci, přečtěte si téma [Podpora a možnosti pomoci](~/articles/cognitive-services/speech-service/support.md).

## <a name="next-steps"></a>Další kroky

[!INCLUDE [windows](../quickstart-list.md)]