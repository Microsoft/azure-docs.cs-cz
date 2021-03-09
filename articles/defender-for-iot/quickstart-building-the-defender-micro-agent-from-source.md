---
title: Sestavení agenta Defender Micro ze zdrojového kódu (Preview)
titleSuffix: Azure Defender for IoT
description: Mikroagenti zahrnuje infrastrukturu, která se dá použít k přizpůsobení distribuce.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/18/2021
ms.topic: quickstart
ms.service: azure
ms.openlocfilehash: 29cac544c858b030cff8e9101737b30e96b863bf
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102506595"
---
# <a name="build-the-defender-micro-agent-from-source-code-preview"></a>Sestavení agenta Defender Micro ze zdrojového kódu (Preview)

Mikroagenti zahrnuje infrastrukturu, která se dá použít k přizpůsobení distribuce. Chcete-li zobrazit seznam dostupných parametrů konfigurace, Prohlédněte si `configs/LINUX_BASE.conf` soubor.

V případě jedné distribuce upravte základní `.conf` soubor. 

Pokud požadujete více než jednu distribuci, můžete dědit ze základní konfigurace a přepsat její hodnoty. 

Přepsání hodnot:

1. Vytvoří nový `.dist` soubor.

1. Přidejte `CONF_DEFINE_BASE(${g_plat_config_path} LINUX_BASE.conf)` do horní části.
 
1. Definujte nové hodnoty podle toho, co požadujete, například: 

    `set(ASC_LOW_PRIORITY_INTERVAL 60*60*24)` 

1. Dejte `.dist` souboru odkaz na referenci při sestavování. Třeba 

    `cmake -DCMAKE_BUILD_TYPE=Debug -Dlog_level=DEBUG -Dlog_level_cmdline:BOOL=ON -DIOT_SECURITY_MODULE_DIST_TARGET=UBUNTU1804 ..` 

## <a name="baseline-configuration-signing"></a>Podepisování konfigurace standardních hodnot 

Agent ověřuje pravost konfiguračních souborů, které jsou umístěny na disku, a ve výchozím nastavení snižuje riziko manipulace.

Tento proces můžete zastavit definováním příznaku preprocesoru `ASC_BASELINE_CONF_SIGN_CHECK_DISABLE` .

Nedoporučujeme vypínat kontrolu podpisů pro produkční prostředí. 

Pokud pro produkční scénáře potřebujete jinou konfiguraci, obraťte se na tým Defender for IoT. 

## <a name="prerequisites"></a>Požadavky 

1. Obraťte se na svého správce účtu, aby požádal o přístup k programu Defender pro zdrojový kód IoT.
 
1. Klonovat nebo extrahovat zdrojový kód do složky na disku.

1. Přejděte do tohoto adresáře.

1. Vyžádejte si dílčí moduly pomocí následujícího kódu:

    ```bash
    git submodule update --init
    ```
    
1. Dál načetli dílčí moduly pro sadu Azure IoT SDK s následujícím kódem: 

    ```bash
    git -C deps/azure-iot-sdk-c/ submodule update –init
    ```
 

1. Přidejte oprávnění ke spuštění a spusťte skript pro nastavení vývojářského prostředí:

    ```bash
    chmod +x scripts/install_development_environment.sh && ./scripts/install_development_environment.sh 
    ```

1. Vytvořte adresář pro výstupy sestavení: 

    ```bash
    mkdir cmake 
    ```

1. Volitelné Stažení a instalace [VSCode](https://code.visualstudio.com/download ) 

1. Volitelné Nainstalujte [rozšíření C/C++](https://code.visualstudio.com/docs/languages/cpp ) pro VSCode.

## <a name="building-the-defender-iot-micro-agent"></a>Sestavení agenta programu Defender IoT Micro 

1. Otevřete adresář pomocí VSCode 

1. Přejděte do `cmake` adresáře. 

1. Spusťte následující příkaz: 

    ```bash
    cmake -DCMAKE_BUILD_TYPE=Debug -Dlog_level=DEBUG -Dlog_level_cmdline:BOOL=ON -DIOT_SECURITY_MODULE_DIST_TARGET<the appropriate distro configuration file name> .. 
    
    cmake --build . -- -j${env:NPROC}
    ```

    Například: 

    ```bash
    cmake -DCMAKE_BUILD_TYPE=Debug -Dlog_level=DEBUG -Dlog_level_cmdline:BOOL=ON -DIOT_SECURITY_MODULE_DIST_TARGETUBUNTU1804 ..
    
    cmake --build . -- -j${env:NPROC}
    ```

## <a name="next-steps"></a>Další kroky

[Nakonfigurujte řešení Azure Defender pro IoT](quickstart-configure-your-solution.md).
