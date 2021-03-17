---
title: Přehled instalačního programu sady nástrojů pro vývoj pro Azure Percept
description: Další informace o použití instalačního programu sady nástrojů pro vývojáře k urychlení pokročilého vývoje pomocí Azure Percept
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 66498fabadc0784a4a4ab1c3762daaaa9a5738c4
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102503211"
---
# <a name="dev-tools-pack-installer-overview"></a>Přehled instalačního programu sady nástrojů pro vývojáře

Instalační program sady nástrojů pro vývojáře je řešení s jedním zastavením, které nainstaluje a nakonfiguruje všechny nástroje, které jsou potřeba pro vývoj inteligentního řešení Edge. Pokud jste již nainstalovali kterýkoli ze softwarových balíčků uvedených níže, instalační program sady nástrojů pro vývojáře tyto balíčky znovu nainstaluje, aby byly nástroje konzistentní s verzemi softwaru instalačního programu.

## <a name="mandatory-tools-installed"></a>Nainstalované povinné nástroje

* [Visual Studio Code](https://code.visualstudio.com/)
* [Python 3,6 (Windows) nebo 3,5 (Linux)](https://www.python.org/)
* [Docker 19,03](https://www.docker.com/)
* [PIP3](https://pip.pypa.io/en/stable/user_guide/)
* [TensorFlow 1,13](https://www.tensorflow.org/)
* [Azure Machine Learning SDK 1,1](https://docs.microsoft.com/python/api/overview/azure/ml/)

## <a name="optional-tools-available-for-installation"></a>Volitelné nástroje dostupné pro instalaci

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

- Nepovinná instalace Caffe může selhat, pokud Docker v systému správně neběží. Pokud chcete nainstalovat Caffe, před pokusem o instalaci Caffe prostřednictvím instalačního programu sady nástrojů pro vývojáře se ujistěte, že je Docker nainstalovaný a spuštěný. 

- Volitelná instalace CUDA se v nekompatibilních systémech nezdařila. Než se pokusíte nainstalovat [sadu CUDA Toolkit 10.0.130](https://developer.nvidia.com/cuda-toolkit) prostřednictvím instalačního programu sady nástrojů pro vývojáře, ověřte kompatibilitu systému.

## <a name="minimum-requirements"></a>Minimální požadavky

* Minimální požadavky Docker:

    * Windows:
        * https://docs.docker.com/docker-for-windows/install/#system-requirements

        - Windows 10 64-bit: pro, Enterprise nebo školství (Build 16299 nebo novější).

             Informace o Windows 10 Home najdete v tématu Instalace Docker desktopu na Windows Home.
           - Je nutné povolit funkce systému Windows Hyper-V a kontejnery.
           - Aby bylo možné úspěšně spustit klienta Hyper-V ve Windows 10, musí být splněny následující požadavky na hardware:

              - 64 bitový procesor s [překladem adres druhé úrovně (SLAT)](https://en.wikipedia.org/wiki/Second_Level_Address_Translation)
              - 4 GB systémové paměti RAM
              - V nastavení systému BIOS musí být povolena podpora virtualizace hardwaru na úrovni systému BIOS. Další informace najdete v tématu virtualizace.

        > [!NOTE]
        > Docker podporuje Docker Desktop ve Windows v závislosti na životním cyklu podpory Microsoftu pro operační systém Windows 10. Další informace najdete v [listu faktu pro životní cyklus systému Windows](https://support.microsoft.com/help/13853/windows-lifecycle-fact-sheet).

    * Mac:
        * https://docs.docker.com/docker-for-mac/install/#system-requirements
       
        Aby bylo možné úspěšně nainstalovat Docker Desktop, musí váš počítač Mac splňovat následující požadavky:
         
         - **Hardware počítače Mac musí být 2010 nebo novější model s procesorem Intel** s podporou hardwaru Intel pro MMU (Memory Management Unit), včetně rozšířených tabulek stránek (EPT) a neomezeného režimu. Spuštěním následujícího příkazu v terminálu můžete zjistit, jestli váš počítač tuto podporu obsahuje: ```sysctl kern.hv_support```

        Pokud Váš počítač Mac podporuje architekturu hypervisoru, příkaz vytiskne ```kern.hv_support: 1``` .

         - **MacOS musí být verze 10,14 nebo novější**. To znamená, Mojave, Catalina nebo Big Sur. Doporučujeme upgradovat na nejnovější verzi macOS.

        Pokud dojde k jakýmkoli problémům po upgradu macOS na verzi 10,15, je nutné nainstalovat nejnovější verzi Docker desktopu, aby byla kompatibilní s touto verzí nástroje macOS.

        - Minimálně 4 GB RAM
        - VirtualBox před verzí 4.3.30 se nesmí nainstalovat, protože není kompatibilní s Docker desktopem.

        > [!NOTE]
        > Docker podporuje Docker Desktop na nejnovějších verzích macOS. To znamená aktuální vydání macOS a předchozích dvou verzí. Vzhledem k tomu, že jsou obecně dostupné nové hlavní verze macOS, Docker přestane podporovat nejstarší verzi a podporuje nejnovější verzi macOS (kromě předchozích dvou vydání). Docker Desktop aktuálně podporuje macOS Mojave, macOS Catalina a macOS Big Sur.
        > 
        - Instalační program není podporován v Apple M1.

## <a name="instructions"></a>Pokyny

1. Stáhněte si instalační program sady nástrojů pro vývojáře pro [Windows](https://go.microsoft.com/fwlink/?linkid=2132187), [Linux](https://go.microsoft.com/fwlink/?linkid=2132186)a [Mac](https://go.microsoft.com/fwlink/?linkid=2132296).

1. V závislosti na vaší platformě dojde k nějakým rozdílům při spouštění instalačního programu.

    1. Ve Windows:
    
        1. Kliknutím na **instalační program pro vývoj-nástroje-Pack** spusťte Průvodce instalací.
        
    1. Pro Mac:
    
        1. Po stažení přesuňte soubor Dev-Tools-Pack-Installer. app do složky Apps (aplikace).
        
        1. Kliknutím na **dev-Tools-Pack-Installer. app** otevřete Průvodce instalací nástroje.
        
        1. Pokud se zobrazí dialogové okno zabezpečení neidentifikovaný vývojář:
        
            1. Přejděte na Předvolby systému – > zabezpečení & ochrany osobních údajů – > obecné a klikněte na tlačítko "otevřít ještě" vedle možnosti "Dev-Tools-Pack-Installer. app".
        
            1. Znovu klikněte na ikonu elektronového zástupce v Docku.
        
            1. V dialogovém okně zabezpečení klikněte na tlačítko otevřít.
    
    1. V Linuxu:
    
        1. Po zobrazení výzvy v prohlížeči kliknutím na Uložit dokončete stažení instalačního programu.
        
        1. Přidejte oprávnění ke spuštění souboru **. appimage** , metoda 1 (příkazový řádek):
            
            1. Otevření terminálu pro Linux
            
            1. Do složky soubory ke stažení zadejte následující text v terminálu.
            
                1. CD ~/downloads/
                
            1. Zadáním následujícího textu v terminálu zpřístupníte spustitelný soubor AppImage.
            
                1. chmod + x **dev-Tools-Pack – instalační program. AppImage**
                
            1. Do terminálu zadejte následující příkaz pro spuštění instalačního programu
            
                1. ./Dev-Tools-Pack-Installer.AppImage
        
        1. Přidejte oprávnění ke spuštění do souboru **. appimage** metody 2 (UI):
        
            1. Klikněte pravým tlačítkem na soubor. appimage a vyberte vlastnosti.
            
            1. Otevřít kartu oprávnění
            
            1. Zaškrtněte políčko ' Allow vykonávajícího souboru jako program '
            
            1. Zavřete vlastnosti a otevřete soubor. appimage.

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

   1. Windows:
   
      1. Rozbalte položku skryté ikony hlavního panelu systému:
      
         1. Rozbalte ikonu skrytého hlavního panelu systému, pokud je skrytá:

            :::image type="content" source="./media/dev-tools-installer/system-tray.png" alt-text="Hlavní panel systému.":::
         
         1. Ověřte, že ikona Docker desktopu zobrazuje "Docker Desktop běží":

            :::image type="content" source="./media/dev-tools-installer/docker-status-running.png" alt-text="Stav Docker.":::
         
         1. Pokud se na hlavním panelu systému nezobrazuje ikona uvedená výše, spusťte z nabídky Start Docker Desktop.
         
         1. Pokud Docker vyzve k restartování, je možné, že instalační program uzavřete a znovu spustíte po dokončení restartování a Docker je ve spuštěném stavu. Všechny úspěšně nainstalované aplikace třetích stran by měly být zjištěné a nebudou automaticky přeinstalovány.

## <a name="next-steps"></a>Další kroky

Projděte si [Pokročilé vývojové úložiště Azure Percept](https://github.com/microsoft/azure-percept-advanced-development) a začněte s pokročilým vývojem pro Azure Percept DK.