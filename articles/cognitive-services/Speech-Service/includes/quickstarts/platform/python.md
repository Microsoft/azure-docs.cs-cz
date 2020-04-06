---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/04/2020
ms.author: dapine
ms.openlocfilehash: 327000173b4c5c378f60c76eb1a24bd155901671
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2020
ms.locfileid: "80666920"
---
Tato příručka ukazuje, jak nainstalovat [sadu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) pro Python. Pokud chcete, aby název balíčku začal sám, `pip install azure-cognitiveservices-speech`spusťte aplikaci .

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="supported-operating-systems"></a>Podporované operační systémy

- Balíček Python Speech SDK je k dispozici pro tyto operační systémy:
  - Windows: x64 a x86
  - Mac: macOS X verze 10.12 nebo novější
  - Linux: Ubuntu 16.04/18.04, Debian 9, RHEL 7/8, CentOS 7/8 na x64

## <a name="prerequisites"></a>Požadavky

- Podporované platformy Linux budou vyžadovat`libssl` instalaci určitých knihoven (pro podporu sadezových vrstev a `libasound2` pro podporu zvuku). Níže naleznete níže uvedené informace o níže uvedených příkazech potřebných k instalaci správných verzí těchto knihoven.

  - V Ubuntu spusťte následující příkazy pro instalaci požadovaných balíčků:

        ```sh
        sudo apt-get update
        sudo apt-get install build-essential libssl1.0.0 libasound2
        ```

  - V Debianu 9 spusťte následující příkazy pro instalaci požadovaných balíčků:

        ```sh
        sudo apt-get update
        sudo apt-get install build-essential libssl1.0.2 libasound2
        ```

  - Na RHEL/CentOS 8, spusťte následující příkazy k instalaci požadovaných balíčků:

        ```sh
        sudo yum update
        sudo yum install alsa-lib openssl python3
        ```

> [!NOTE]
> Na RHEL/CentOS 8 postupujte podle [pokynů, jak nakonfigurovat OpenSSL pro Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

- V systému Windows potřebujete pro vaši platformu [redistribuovatelný microsoft visual c++ pro Visual Studio 2019.](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) Všimněte si, že instalace poprvé může vyžadovat restartování systému Windows před pokračováním v této příručce.
- A nakonec budete potřebovat [Python 3.5 až 3.8](https://www.python.org/downloads/). Chcete-li zkontrolovat instalaci, otevřete příkazový řádek a zadejte příkaz `python --version` a zkontrolujte výsledek. Pokud je správně nainstalován, dostanete odpověď "Python 3.5.1" nebo podobné.

## <a name="install-the-speech-sdk-from-pypi"></a>Instalace sady Speech SDK z pyPI

Pokud používáte vlastní prostředí nebo vytvářet nástroje, spusťte následující příkaz k instalaci sady Speech SDK z [pyPI](https://pypi.org/). Pro uživatele visual studio kód, přejděte na další dílčí část pro instalaci s průvodcem.

```sh
pip install azure-cognitiveservices-speech
```

Pokud jste na macOS, možná budete muset spustit `pip` následující příkaz, aby výše uvedený příkaz fungoval:

```sh
python3 -m pip install --upgrade pip
```

Po úspěšné instalaci `pip` `azure-cognitiveservices-speech`můžete použít sadu Speech SDK importováním oboru názvů do projektů Pythonu.

```py
import azure.cognitiveservices.speech as speechsdk
```

## <a name="install-the-speech-sdk-using-visual-studio-code"></a>Instalace sady Speech SDK pomocí kódu sady Visual Studio

1. Stáhněte a nainstalujte nejnovější podporovanou verzi [Pythonu](https://www.python.org/downloads/) pro vaši platformu, 3.5 až 3.8.
   - Uživatelé systému Windows nezapomeňte během procesu instalace vybrat možnost "Přidat Python do cesty".
1. Stáhněte a nainstalujte [kód sady Visual Studio](https://code.visualstudio.com/Download).
1. Otevřete visual studio kód a nainstalujte rozšíření Pythonu. V nabídce vyberte**Přípony** **předvoleb** >  **souborů.** >  Vyhledejte **Python** a klepněte na tlačítko **Nainstalovat**.

   ![Instalace rozšíření Pythonu](~/articles/cognitive-services/speech-service/media/sdk/qs-python-vscode-python-extension.png)

1. Také z v rámci kódu sady Visual Studio nainstalujte balíček Speech SDK Python z integrovaného příkazového řádku:
   1. Otevření terminálu (z rozbalovacích **nabídek, Terminál** > **Nový terminál)**
   1. V terminálu, který se otevře, zadejte příkaz`python -m pip install azure-cognitiveservices-speech`

Pokud s visual studio kód začínáte, naleznete v podrobnější [dokumentaci kódu sady Visual Studio](https://code.visualstudio.com/docs). Další informace o kódu sady Visual Studio a Pythonu naleznete v [kurzu visual studio code python .](https://code.visualstudio.com/docs/python/python-tutorial)

## <a name="support-and-updates"></a>Podpora a aktualizace

Aktualizace balíčku Speech SDK Python jsou distribuovány prostřednictvím PyPI a oznámeny v [poznámkách k verzi](~/articles/cognitive-services/speech-service/releasenotes.md).
Pokud je k dispozici nová verze, můžete `pip install --upgrade azure-cognitiveservices-speech`ji aktualizovat pomocí příkazu .
Zkontrolujte, která verze je aktuálně `azure.cognitiveservices.speech.__version__` nainstalována kontrolou proměnné.

Pokud máte problém nebo vám chybí funkce, přečtěte si článek [Možnosti podpory a nápovědy](~/articles/cognitive-services/speech-service/support.md).

## <a name="next-steps"></a>Další kroky

[!INCLUDE [windows](../quickstart-list.md)]
