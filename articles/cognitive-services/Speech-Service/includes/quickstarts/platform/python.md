---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/04/2020
ms.author: trbye
ms.openlocfilehash: 924532318fc6638059bbe9eef0065349463523ae
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81400884"
---
V této příručce se dozvíte, jak nainstalovat [sadu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) pro Python. Pokud chcete, aby název balíčku začínal vlastním, spusťte `pip install azure-cognitiveservices-speech`příkaz.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="supported-operating-systems"></a>Podporované operační systémy

- Balíček python Speech SDK je k dispozici pro tyto operační systémy:
  - Windows: x64 a x86
  - Mac: macOS X verze 10,12 nebo novější
  - Linux: Ubuntu 16.04/18.04, Debian 9, RHEL 7/8, CentOS 7/8 na platformě x64

## <a name="prerequisites"></a>Požadavky

- Podporované platformy Linux budou vyžadovat nainstalovaná konkrétní knihovny`libssl` (pro podporu SSL (Secure `libasound2` Sockets Layer) a pro podporu zvuku. Pro příkazy potřebné k instalaci správných verzí těchto knihoven použijte níže uvedenou distribuci.

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

  - Na RHEL/CentOS 8 spusťte následující příkazy, abyste nainstalovali požadované balíčky:

        ```sh
        sudo yum update
        sudo yum install alsa-lib openssl python3
        ```

> [!NOTE]
> V RHEL/CentOS 8 postupujte podle pokynů, [jak nakonfigurovat OpenSSL pro Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

- V systému Windows potřebujete [Microsoft Visual C++ distribuovatelné součásti pro Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) pro vaši platformu. Mějte na paměti, že pokud tuto instalaci nainstalujete poprvé, může před pokračováním v této příručce vyžadovat restartování systému Windows.
- A nakonec budete potřebovat [Python 3,5 až 3,8](https://www.python.org/downloads/). Chcete-li ověřit instalaci, otevřete příkazový řádek a zadejte příkaz `python --version` a podívejte se na výsledek. Pokud je správně nainstalovaná, dostanete odpověď "Python 3.5.1" nebo podobnou.

## <a name="install-the-speech-sdk-from-pypi"></a>Instalace sady Speech SDK z PyPI

Pokud používáte vlastní prostředí nebo nástroje pro tvorbu, spusťte následující příkaz, který nainstaluje sadu Speech SDK z [PyPI](https://pypi.org/). Pro uživatele Visual Studio Code přejděte k další dílčí části instalace s asistencí.

```sh
pip install azure-cognitiveservices-speech
```

Pokud používáte macOS, možná budete muset spustit následující příkaz, abyste mohli pracovat výše uvedeným `pip` příkazem:

```sh
python3 -m pip install --upgrade pip
```

Po úspěšném použití `pip` k instalaci `azure-cognitiveservices-speech`můžete sadu Speech SDK použít tak, že do svých projektů Python naimportujete obor názvů.

```py
import azure.cognitiveservices.speech as speechsdk
```

## <a name="install-the-speech-sdk-using-visual-studio-code"></a>Instalace sady Speech SDK pomocí Visual Studio Code

1. Stáhněte a nainstalujte nejnovější podporovanou verzi [Pythonu](https://www.python.org/downloads/) pro vaši platformu, 3,5 až 3,8.
   - Uživatelé systému Windows při instalaci nezapomeňte v procesu instalace vybrat přidat Python do vaší cesty.
1. Stáhněte a nainstalujte [Visual Studio Code](https://code.visualstudio.com/Download).
1. Otevřete Visual Studio Code a nainstalujte rozšíření Python. V nabídce vyberte**rozšíření** **předvoleb** >  **souborů** > . Vyhledejte **Python** a klikněte na **nainstalovat**.

   ![Instalace rozšíření Python](~/articles/cognitive-services/speech-service/media/sdk/qs-python-vscode-python-extension.png)

1. V rámci Visual Studio Code také nainstalujte sadu Speech SDK Python Package z integrovaného příkazového řádku:
   1. Otevření terminálu (z rozevíracích nabídek, **terminálového** > **nového terminálu**)
   1. V terminálu, který se otevře, zadejte příkaz.`python -m pip install azure-cognitiveservices-speech`

Pokud s Visual Studio Code začínáte, přečtěte si rozsáhlejší [Visual Studio Code dokumentaci](https://code.visualstudio.com/docs). Další informace o Visual Studio Code a Pythonu najdete v [kurzu Visual Studio Code Pythonu](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="support-and-updates"></a>Podpora a aktualizace

Aktualizace balíčku Python sady Speech SDK jsou distribuovány prostřednictvím PyPI a oznámeny v [poznámkách k verzi](~/articles/cognitive-services/speech-service/releasenotes.md).
Pokud je k dispozici nová verze, můžete na ni pomocí příkazu `pip install --upgrade azure-cognitiveservices-speech`aktualizovat.
Zkontrolováním `azure.cognitiveservices.speech.__version__` proměnné zkontrolujte, kterou verzi aktuálně máte nainstalovanou.

Pokud máte problém nebo nemáte funkci, přečtěte si téma [Podpora a možnosti pomoci](~/articles/cognitive-services/speech-service/support.md).

## <a name="next-steps"></a>Další kroky

[!INCLUDE [windows](../quickstart-list.md)]
