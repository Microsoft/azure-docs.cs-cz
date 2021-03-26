---
title: Přehled instalačního programu sady nástrojů pro vývoj pro Azure Percept
description: Další informace o použití instalačního programu sady nástrojů pro vývojáře k urychlení pokročilého vývoje pomocí Azure Percept
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: template-how-to
ms.openlocfilehash: f81f7922431f85cfc2a98261a128ba66d23a984f
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105608591"
---
# <a name="dev-tools-pack-installer-overview"></a>Přehled instalačního programu sady nástrojů pro vývojáře

Instalační program sady nástrojů pro vývojáře je řešení s jedním zastavením, které nainstaluje a nakonfiguruje všechny nástroje, které jsou potřeba k vývoji pokročilého řešení inteligentního Edge.

## <a name="mandatory-tools"></a>Povinné nástroje

* [Visual Studio Code](https://code.visualstudio.com/)
* [Python 3,6 nebo novější](https://www.python.org/)
* [Docker 19,03](https://www.docker.com/)
* [PIP3](https://pip.pypa.io/en/stable/user_guide/)
* [TensorFlow 1,13](https://www.tensorflow.org/)
* [Azure Machine Learning SDK 1,1](/python/api/overview/azure/ml/)

## <a name="optional-tools"></a>Volitelné nástroje

* [NVIDIA DEEPSTREAM SDK 5](https://developer.nvidia.com/deepstream-sdk) (Toolkit pro vývoj řešení pro akcelerátory NVIDIA)
* [Intel OpenVino Toolkit 2020,2](https://docs.openvinotoolkit.org/) (sada nástrojů pro vývoj řešení pro akcelerátory Intel)
* [Lobe.ai](https://lobe.ai/)  
* [Streamlit](https://www.streamlit.io/)
* [Pytorch 1.4.0 (Windows) nebo 1.2.0 (Linux)](https://pytorch.org/)
* [Miniconda3](https://docs.conda.io/en/latest/miniconda.html)
* [Chainer 5,2](https://chainer.org/)
* [Caffe](https://caffe.berkeleyvision.org/)
* [CUDA Toolkit 10.0.130](https://developer.nvidia.com/cuda-toolkit)
* [Microsoft Cognitive Toolkit 2.5.1](https://www.microsoft.com/research/product/cognitive-toolkit/?lang=fr_ca)

## <a name="known-issues"></a>Známé problémy

- Nepovinná instalace Caffe může selhat, pokud Docker nefunguje správně. Pokud chcete nainstalovat Caffe, před pokusem o instalaci Caffe prostřednictvím instalačního programu sady nástrojů pro vývojáře se ujistěte, že je Docker nainstalovaný a spuštěný.

- Volitelná instalace CUDA se v nekompatibilních systémech nezdařila. Než se pokusíte nainstalovat [sadu CUDA Toolkit 10.0.130](https://developer.nvidia.com/cuda-toolkit) prostřednictvím instalačního programu sady nástrojů pro vývojáře, ověřte kompatibilitu systému.

## <a name="docker-minimum-requirements"></a>Minimální požadavky Docker

### <a name="windows"></a>Windows

- Windows 10 64-bit: pro, Enterprise nebo školství (Build 16299 nebo novější).

- Je nutné povolit funkce systému Windows Hyper-V a kontejnery. K úspěšnému spuštění technologie Hyper-V ve Windows 10 se vyžadují tyto požadavky na hardware:

    - 64 bitový procesor s [překladem adres druhé úrovně (SLAT)](https://en.wikipedia.org/wiki/Second_Level_Address_Translation)
    - 4 GB systémová paměť RAM
    - V nastavení systému BIOS musí být povolena podpora virtualizace hardwaru na úrovni systému BIOS. Další informace najdete v tématu virtualizace.

> [!NOTE]
> Docker podporuje Docker Desktop ve Windows v závislosti na životním cyklu podpory Microsoftu pro operační systém Windows 10. Další informace najdete v [listu faktu pro životní cyklus systému Windows](https://support.microsoft.com/help/13853/windows-lifecycle-fact-sheet).

Přečtěte si další informace o [instalaci Docker desktopu ve Windows](https://docs.docker.com/docker-for-windows/install/#install-docker-desktop-on-windows).

### <a name="mac"></a>Mac

- Mac musí být 2010 nebo novější model s následujícími atributy:
    - Procesor Intel
    - Virtualizace hardwaru (MMU) od společnosti Intel, včetně rozšířených tabulek stránek (EPT) a neomezeného režimu. Spuštěním následujícího příkazu v terminálu můžete zjistit, jestli váš počítač tuto podporu obsahuje: ```sysctl kern.hv_support``` . Pokud Váš počítač Mac podporuje architekturu hypervisoru, příkaz vytiskne ```kern.hv_support: 1``` .

- macOS verze 10,14 nebo novější (Mojave, Catalina nebo Big Sur). Doporučujeme upgradovat na nejnovější verzi macOS. Pokud dojde k jakýmkoli problémům po upgradu macOS na verzi 10,15, je nutné nainstalovat nejnovější verzi Docker desktopu, aby byla kompatibilní s touto verzí nástroje macOS.

- Minimálně 4 GB RAM

- Neinstalujte VirtualBox před verzí 4.3.30 – není kompatibilní s Docker desktopem.

- Instalační program není podporován v Apple M1.

Přečtěte si další informace o [instalaci Docker desktopu na Macu](https://docs.docker.com/docker-for-mac/install/#system-requirements).

## <a name="launch-the-installer"></a>Spusťte instalační program.

Stáhněte si instalační program sady nástrojů pro vývojáře pro [Windows](https://go.microsoft.com/fwlink/?linkid=2132187), [Linux](https://go.microsoft.com/fwlink/?linkid=2132186)nebo [Mac](https://go.microsoft.com/fwlink/?linkid=2132296). Spusťte instalační program podle vaší platformy, jak je popsáno níže.

### <a name="windows"></a>Windows

1. Kliknutím na **vývoj-Tools-Pack-Installer** otevřete Průvodce instalací nástroje.

### <a name="mac"></a>Mac

1. Po stažení přesuňte soubor **dev-Tools-Pack-Installer.app** do složky **aplikace** .

1. Kliknutím na **dev-Tools-Pack-Installer.app** otevřete Průvodce instalací nástroje.

1. Pokud se zobrazí dialogové okno zabezpečení neidentifikovaný vývojář:

    1. Přejděte na **Předvolby systému**  ->  **zabezpečení & ochraně osobních údajů**  ->  **všeobecně** a klikněte na **otevřít, přesto** vedle **dev-Tools-Pack-Installer.app**.
    1. Klikněte na ikonu elektron.
    1. V dialogovém okně zabezpečení klikněte na **otevřít** .

### <a name="linux"></a>Linux

1. Po zobrazení výzvy v prohlížeči kliknutím na **Uložit** dokončete stahování instalačního programu.

1. Přidejte do souboru **. appimage** oprávnění ke spuštění:

    1. Otevřete terminál pro Linux.

    1. Do složky **soubory ke stažení** přejděte v terminálu a zadejte následující:

        ```bash
        cd ~/Downloads/
        ```

    1. Nastavte spustitelný soubor AppImage:

        ```bash
        chmod +x Dev-Tools-Pack-Installer.AppImage
        ```

    1. Spusťte instalační program:

        ```bash
        ./Dev-Tools-Pack-Installer.AppImage
        ```

1. Přidejte do souboru **. appimage** oprávnění ke spuštění:

    1. Klikněte pravým tlačítkem na soubor. appimage a vyberte **vlastnosti**.
    1. Otevřete kartu **oprávnění** .
    1. Zaškrtněte políčko vedle políčka **povolí spouštění souboru jako programu**.
    1. Zavřete **vlastnosti** a otevřete soubor **. appimage** .

## <a name="run-the-installer"></a>Spuštění instalačního programu

1. Na stránce **instalace instalačního programu sady nástrojů pro vývojáře** klikněte na **Zobrazit licenci** . zobrazí se licenční smlouvy každého softwarového balíčku, který je součástí instalačního programu. Pokud souhlasíte s podmínkami licenční smlouvy, zaškrtněte políčko a klikněte na **Další**.

    :::image type="content" source="./media/dev-tools-installer/dev-tools-license-agreements.png" alt-text="Obrazovka licenční smlouva v instalačním programu.":::

1. Kliknutím na **prohlášení o zásadách ochrany osobních** údajů zkontrolujte prohlášení Microsoftu o zásadách ochrany osobních údajů. Pokud souhlasíte s podmínkami prohlášení o zásadách ochrany osobních údajů a chcete odeslat diagnostická data společnosti Microsoft, vyberte možnost **Ano** a klikněte na tlačítko **Další**. V opačném případě vyberte možnost **ne** a klikněte na tlačítko **Další**.

    :::image type="content" source="./media/dev-tools-installer/dev-tools-privacy-statement.png" alt-text="V instalačním programu se obrazovka smlouvy prohlášení o zásadách ochrany osobních údajů.":::

1. Na stránce **Konfigurovat součásti** vyberte volitelné nástroje, které chcete nainstalovat (ve výchozím nastavení se budou instalovat povinné nástroje).

    1. Pokud pracujete se službou Azure Percept audio SoM, která je součástí Azure Percept DK, nezapomeňte nainstalovat Intel OpenVino Toolkit a Miniconda3.

    1. Pokud chcete pokračovat v instalaci, klikněte na **nainstalovat** .

    :::image type="content" source="./media/dev-tools-installer/dev-tools-configure-components.png" alt-text="Obrazovka instalačního programu zobrazující dostupné softwarové balíčky.":::

1. Po úspěšné instalaci všech vybraných součástí Průvodce pokračuje na stránce **dokončení Průvodce instalací** . Kliknutím na tlačítko **Dokončit** ukončíte instalační program.

    :::image type="content" source="./media/dev-tools-installer/dev-tools-finish.png" alt-text="Obrazovka pro dokončení instalačního programu.":::

## <a name="docker-status-check"></a>Kontroler stavu Docker

Pokud se instalační program upozorní na ověření, že je dokovací plocha v dobrém stavu, přečtěte si následující postup:

### <a name="windows"></a>Windows

1. Rozbalte položku skryté ikony na hlavním panelu systému.

    :::image type="content" source="./media/dev-tools-installer/system-tray.png" alt-text="Hlavní panel systému.":::

1. Ověřte, že ikona Docker desktopu zobrazuje **Docker Desktop spuštěný**.

    :::image type="content" source="./media/dev-tools-installer/docker-status-running.png" alt-text="Stav Docker.":::

1. Pokud se na hlavním panelu systému nezobrazuje ikona uvedená výše, spusťte z nabídky Start Docker Desktop.

1. Pokud Docker vyzve k restartování, je možné, že instalační program uzavřete a znovu spustíte po dokončení restartování a Docker je ve spuštěném stavu. Všechny úspěšně nainstalované aplikace třetích stran by měly být zjištěné a nebudou automaticky přeinstalovány.

## <a name="next-steps"></a>Další kroky

Projděte si [Pokročilé vývojové úložiště Azure Percept](https://github.com/microsoft/azure-percept-advanced-development) a začněte s pokročilým vývojem pro Azure Percept DK.
