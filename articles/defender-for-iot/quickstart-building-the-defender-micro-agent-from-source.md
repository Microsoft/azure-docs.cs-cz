---
title: 'Rychlý Start: sestavení agenta Defender Micro ze zdrojového kódu (Preview)'
description: V tomto rychlém startu se dozvíte o mikroagentovi, který obsahuje infrastrukturu, kterou je možné použít k přizpůsobení distribuce.
ms.date: 1/18/2021
ms.topic: quickstart
ms.openlocfilehash: a3a8f82989d6abbaf2657e4b45638c77b3b2f704
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384593"
---
# <a name="quickstart-build-the-defender-micro-agent-from-source-code-preview"></a>Rychlý Start: sestavení agenta Defender Micro ze zdrojového kódu (Preview)

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

1. Volitelné Nainstalujte [rozšíření C/C++](https://code.visualstudio.com/docs/languages/cpp ) pro VSCode.-None

## <a name="baseline-configuration-signing"></a>Podepisování konfigurace standardních hodnot 

Agent ověřuje pravost konfiguračních souborů, které jsou umístěny na disku, a ve výchozím nastavení snižuje riziko manipulace.

Tento proces můžete zastavit definováním příznaku preprocesoru `ASC_BASELINE_CONF_SIGN_CHECK_DISABLE` .

Nedoporučujeme vypínat kontrolu podpisů pro produkční prostředí. 

Pokud pro produkční scénáře potřebujete jinou konfiguraci, obraťte se na tým Defender for IoT. 

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
