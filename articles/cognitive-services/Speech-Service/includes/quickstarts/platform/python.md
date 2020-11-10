---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: trbye
ms.openlocfilehash: 0e2324f7e524567b07f0228e046605b781bc4916
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2020
ms.locfileid: "94425502"
---
V této příručce se dozvíte, jak nainstalovat [sadu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) pro Python. Pokud chcete, aby název balíčku začínal vlastním, spusťte příkaz `pip install azure-cognitiveservices-speech` .

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="supported-operating-systems"></a>Podporované operační systémy

- Balíček python Speech SDK je k dispozici pro tyto operační systémy:
  - Windows: x64 a x86
  - Mac: macOS X verze 10,12 nebo novější
  - Linux Podívejte se na seznam [podporovaných distribucí a cílových architektur systému Linux](~/articles/cognitive-services/speech-service/speech-sdk.md).

## <a name="prerequisites"></a>Předpoklady

- Podporované platformy Linux budou vyžadovat nainstalovaná konkrétní knihovny ( `libssl` pro podporu SSL (Secure Sockets Layer `libasound2` ) a pro podporu zvuku. Pro příkazy potřebné k instalaci správných verzí těchto knihoven použijte níže uvedenou distribuci.

  - V Ubuntu/Debian spuštěním následujících příkazů nainstalujte požadované balíčky:

    ```sh
    sudo apt-get update
    sudo apt-get install build-essential libssl1.0.0 libasound2
    ```

    Pokud libssl 1.0.0 není k dispozici, nainstalujte libssl 1.0. x (kde x je větší než 0) nebo místo toho libssl 1.1.

  - V RHEL/CentOS spuštěním následujících příkazů nainstalujte požadované balíčky:

    ```sh
    sudo yum update
    sudo yum install alsa-lib openssl python3
    ```

> [!NOTE]
> - V RHEL/CentOS 7 postupujte podle pokynů, [jak nakonfigurovat RHEL/CentOS 7 pro sadu Speech SDK](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md).
> - V RHEL/CentOS 8 postupujte podle pokynů, [jak nakonfigurovat OpenSSL pro Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

- V systému Windows potřebujete [Microsoft Visual C++ distribuovatelné součásti pro Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) pro vaši platformu. Mějte na paměti, že pokud tuto instalaci nainstalujete poprvé, může před pokračováním v této příručce vyžadovat restartování systému Windows.
- A nakonec budete potřebovat [Python 3,5 až 3,8](https://www.python.org/downloads/). Chcete-li ověřit instalaci, otevřete příkazový řádek a zadejte příkaz `python --version` a podívejte se na výsledek. Pokud je správně nainstalovaná, dostanete odpověď "Python 3.5.1" nebo podobnou.

## <a name="install-the-speech-sdk-from-pypi"></a>Instalace sady Speech SDK z PyPI

Pokud používáte vlastní prostředí nebo nástroje pro tvorbu, spusťte následující příkaz, který nainstaluje sadu Speech SDK z [PyPI](https://pypi.org/). Pro uživatele Visual Studio Code přejděte k další dílčí části instalace s asistencí.

```sh
pip install azure-cognitiveservices-speech
```

Pokud používáte macOS, možná budete muset spustit následující příkaz, abyste mohli `pip` pracovat výše uvedeným příkazem:

```sh
python3 -m pip install --upgrade pip
```

Po úspěšném použití `pip` k instalaci `azure-cognitiveservices-speech` můžete sadu Speech SDK použít tak, že do svých projektů Python naimportujete obor názvů.

```py
import azure.cognitiveservices.speech as speechsdk
```

## <a name="install-the-speech-sdk-using-visual-studio-code"></a>Instalace sady Speech SDK pomocí Visual Studio Code

1. Stáhněte a nainstalujte nejnovější podporovanou verzi [Pythonu](https://www.python.org/downloads/) pro vaši platformu, 3,5 až 3,8.
   - Uživatelé systému Windows při instalaci nezapomeňte v procesu instalace vybrat přidat Python do vaší cesty.
1. Stáhněte a nainstalujte si [Visual Studio Code](https://code.visualstudio.com/Download).
1. Otevřete Visual Studio Code a nainstalujte rozšíření Python. **File**  >  **Preferences**  >  V nabídce vyberte **rozšíření** předvoleb souborů. Vyhledejte **Python** a klikněte na **nainstalovat**.

   ![Instalace rozšíření Pythonu](~/articles/cognitive-services/speech-service/media/sdk/qs-python-vscode-python-extension.png)

1. V rámci Visual Studio Code také nainstalujte sadu Speech SDK Python Package z integrovaného příkazového řádku:
   1. Otevření terminálu (z rozevíracích nabídek, **terminálového**  >  **nového terminálu** )
   1. V terminálu, který se otevře, zadejte příkaz. `python -m pip install azure-cognitiveservices-speech`

Pokud s Visual Studio Code začínáte, přečtěte si rozsáhlejší [Visual Studio Code dokumentaci](https://code.visualstudio.com/docs). Další informace o Visual Studio Code a Pythonu najdete v [kurzu Visual Studio Code Pythonu](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="support-and-updates"></a>Podpora a aktualizace

Aktualizace balíčku Python sady Speech SDK jsou distribuovány prostřednictvím PyPI a oznámeny v [poznámkách k verzi](~/articles/cognitive-services/speech-service/releasenotes.md).
Pokud je k dispozici nová verze, můžete na ni pomocí příkazu aktualizovat `pip install --upgrade azure-cognitiveservices-speech` .
Zkontrolováním proměnné zkontrolujte, kterou verzi aktuálně máte nainstalovanou `azure.cognitiveservices.speech.__version__` .

Pokud máte problém nebo nemáte funkci, přečtěte si téma [Podpora a možnosti pomoci](../../../../cognitive-services-support-options.md?context=%252fazure%252fcognitive-services%252fspeech-service%252fcontext%252fcontext%253fcontext%253d%252fazure%252fcognitive-services%252fspeech-service%252fcontext%252fcontext).

## <a name="next-steps"></a>Další kroky

[!INCLUDE [windows](../quickstart-list.md)]