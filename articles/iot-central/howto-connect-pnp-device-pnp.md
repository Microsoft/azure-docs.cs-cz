---
title: Připojení zařízení IoT technologie Plug and Play k IoT Central | Microsoft Docs
description: Jako vývojář zařízení se dozvíte, jak pomocí Visual Studio Code vytvořit a otestovat zařízení technologie Plug and Play IoT, které se připojuje k IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 07/10/2019
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 630f92bc8cf5fb89626bfe4de4e4a575045c961a
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2019
ms.locfileid: "69880953"
---
# <a name="use-visual-studio-code-to-create-an-iot-plug-and-play-device-that-connects-to-iot-central"></a>Pomocí Visual Studio Code vytvořit zařízení IoT technologie Plug and Play, které se připojuje k IoT Central

V této příručce se dozvíte, jak:

* Jako operátor můžete přidat a nakonfigurovat reálné zařízení v aplikaci Azure IoT Central.

* Jako vývojář zařízení můžete použít Visual Studio Code k vytvoření zařízení [IoT technologie Plug and Play](https://aka.ms/iot-pnp-docs) , které se připojuje k vaší IoT Central aplikaci.

Pomocí [modelu schopností zařízení](./concepts-architecture-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) můžete definovat zařízení, které se připojuje k IoT Central. V tomto průvodci použijete model, který definuje zařízení snímače životního prostředí.

Vývojář zařízení pomocí modelu generuje kód klienta zařízení a tvůrce používá model k [Vytvoření šablony zařízení](./howto-set-up-template-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) v IoT Central. Model funguje jako kontrakt mezi vaším zařízením a vaší IoT Centralou aplikací.

V části v této příručce, která popisuje, jak sestavit generovaný kód, se předpokládá, že používáte systém Windows.

V této příručce se naučíte:

* Import modelu schopností zařízení do šablony zařízení v IoT Central
* Přidání řídicího panelu do šablony, která zobrazuje telemetrii zařízení
* Přidání reálného zařízení ze šablony
* Import modelu schopností zařízení do Visual Studio Code
* Generování klientské aplikace zařízení v jazyce C z modelu
* Sestavení klientské aplikace zařízení v jazyce C a jejich připojení k IoT Central

## <a name="prerequisites"></a>Požadavky

K otestování kódu zařízení v této příručce potřebujete aplikaci IoT Central vytvořenou z šablony aplikace ve **verzi Preview** . Pokud ještě nemáte aplikaci IoT Central k použití, dokončete rychlé zprovoznění a [vytvořte aplikaci Azure IoT Central (funkce verze Preview)](./quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json):

Pokud chcete pracovat s modelem schopností zařízení v tomto průvodci, budete potřebovat:

* [Visual Studio Code](https://code.visualstudio.com/download): Visual Studio Code je k dispozici pro více platforem.
* Rozšíření Azure IoT Device Workbench pro Visual Studio Code. Pomocí následujících kroků nainstalujete rozšíření Azure IoT Device Workbench do VS Code:

    1. V VS Code vyberte kartu **rozšíření** .
    1. Vyhledejte aplikaci **Workbench pro zařízení Azure IoT**.
    1. Vyberte **Install** (Nainstalovat).

    > [!NOTE]
    > Aktuální verze generátoru kódu v rozšíření nepodporuje typy geografických a **vektorových** schémat ani sémantické typy **zrychlení**, **rychlosti**a **umístění** . Tyto schéma a sémantické typy jsou podporovány nástrojem IoT Central.

    > [!NOTE]
    > Aby bylo možné pracovat s IoT Central, musí mít model schopností zařízení všechna rozhraní definovaná vloženým do stejného souboru.

Chcete-li vytvořit generovaný kód jazyka C v systému Windows v tomto průvodci, budete potřebovat:

* [Visual Studio (komunita, Professional nebo Enterprise)](https://visualstudio.microsoft.com/downloads/) – nezapomeňte při instalaci sady Visual Studio zahrnout komponentu **Správce balíčků NuGet** a **vývoj C++ desktopových** aplikací.
* [Git](https://git-scm.com/download)
* [Cmake](https://cmake.org/download/) – při instalaci **cmake**vyberte možnost **Přidat cmake do systémové cesty**.
* Místní kopie sady Azure IoT C SDK:

    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive -b public-preview
    ```

Pokud chcete postupovat podle pokynů pro zařízení na konci tohoto postupu, musíte taky nainstalovat:

* [Node.js](https://nodejs.org)
* [Nástroj DPS-keygen](https://www.npmjs.com/package/dps-keygen):

    ```cmd/sh
    npm i -g dps-keygen
    ```

## <a name="create-device-template"></a>Vytvořit šablonu zařízení

Vytvořte složku s názvem **pnp_app** ve složce **Azure-IoT-SDK-c** obsahující sadu Azure IoT c SDK, kterou jste naklonováni. Stáhněte si model schopností zařízení [EnvironmentalSensor. capabilitymodel. JSON](https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensorInline.capabilitymodel.json) z GitHubu a uložte ho do složky **pnp_app** . Nahraďte dvě instance `<YOUR_COMPANY_NAME_HERE>` názvem. Používejte pouze znaky a-z, A-Z, 0-9 a podtržítko. Poznamenejte si celou hodnotu `"@id"` pole, která jednoznačně identifikuje model schopností zařízení, budete ho potřebovat později. Tento model obsahuje dvě rozhraní:

* Společné rozhraní **DeviceManagement**
* vlastní rozhraní **EnvironmentalSensor**

Chcete-li vytvořit šablonu zařízení pro zařízení snímače životního prostředí v IoT Central:

1. Přejít na stránku **šablony zařízení** a vyberte **+ Nová**. Pak zvolte **vlastní**.

1. Jako název šablony zařízení zadejte **snímač pro životní prostředí** .

1. Vyberte možnost **importovat model schopností**. Pak vyhledejte **EnvironmentalSensor. capabilitymodel. JSON** , který jste vytvořili, a vyberte **otevřít**. Tato dvě rozhraní, **informace o zařízení** a **snímače životního prostředí**se zobrazují v **modelu schopností environmentálního senzoru**.

1. Vyberte **zobrazení** a pak **Vizualizujte zařízení**.

1. V seznamu polí, která můžete přidat na řídicí panel, vyberte dvě hodnoty **telemetrie** , **vlhkost** a **teplotu** a vyberte **kombinovat**. Potom zvolte **Save** (Uložit).

Teď máte šablonu zařízení, která používá model senzoru pro **životní prostředí** a který má jednoduchý řídicí panel pro zobrazení telemetrie ze zařízení.

## <a name="publish-the-template-and-add-a-real-device"></a>Publikování šablony a přidání reálného zařízení

Pokud chcete publikovat šablonu a přidat reálné zařízení, klikněte na stránce **šablony zařízení** a vyberte šablonu zařízení **snímače životního prostředí** . Vyberte **publikovat**, zkontrolujte informace a vyberte **publikovat**.

Před připojením klientské aplikace je potřeba přidat zařízení na stránce **zařízení** a získat informace o jeho připojení:

1. Přejít na stránku **zařízení** a vyberte **senzor prostředí**. Stránka **zařízení** vám umožňuje spravovat zařízení, která se můžou připojovat k vaší aplikaci.

1. Pokud chcete přidat reálné zařízení, zvolte **+ Nový**, změňte ID zařízení na **Sensor01**a vyberte **vytvořit**. Ujistěte se, že je **simulovaná** .

1. V seznamu zařízení vyberte zařízení **snímače životního prostředí** . Pak vyberte **připojit**.

1. Poznamenejte si **ID oboru**, **ID zařízení**a **primární klíč**. Pak vyberte **Zavřít**.

## <a name="generate-a-device-client-application"></a>Vygenerování klientské aplikace zařízení

K vygenerování kostry klientské aplikace zařízení z modelu schopností zařízení slouží Visual Studio Code:

1. Spusťte Visual Studio Code a otevřete složku **pnp_app** .

1. Stisknutím **kombinace kláves CTRL + SHIFT + P** otevřete paletu příkazů.

1. Zadejte **technologie Plug and Play** a pak vyberte vygenerovat zástupnou proceduru pro **kód zařízení**.

1. Vyberte soubor modelu schopností zařízení **EnvironmentalSensor. capabilitymodel. JSON** .

1. Jako název projektu zadejte **sensor_app** .

1. Jako jazyk vyberte **ANSI C** .

1. Jako cíl vyberte **projekt cmake** .

1. Jako metodu připojení vyberte **přes DPS (služba Device Provisioning Service) symetrický klíč** .

Visual Studio Code otevře nové okno s generovaným kódem jazyka C ve složce **sensor_app** .

## <a name="add-connection-details-to-the-device-client"></a>Přidání podrobností o připojení do klienta zařízení

Chcete-li přidat informace o připojení do klienta zařízení, otevřete **Main. c** ve složce, která obsahuje váš generovaný kód:

1. Nahraďte `[DPS Id Scope]` hodnotou **ID oboru** , kterou jste si poznamenali dříve.

1. Nahraďte `[DPS symmetric key]` hodnotou **primárního klíče** , kterou jste si poznamenali dříve.

1. Nahraďte `[device registration Id]` hodnotou **ID zařízení** , kterou jste si poznamenali dříve.

1. Uložte změny.

## <a name="build-and-run-the-client"></a>Sestavení a spuštění klienta

Chcete-li vytvořit a spustit klienta, je nutné přizpůsobit sestavení pro sadu Azure IoT C SDK:

1. Otevřete soubor **CMakeLists. txt** v kořenové složce složky **Azure-IoT-SDK-c** .

1. Přidejte následující řádek na konec tohoto souboru, aby se do sestavení zahrnula nová klientská aplikace:

    ```txt
    add_subdirectory(pnp_app/sensor_app)
    ```

1. Uložte změny.

1. Otevřete příkazový řádek a přejděte do složky **Azure-IoT-SDK-c** .

1. Chcete-li sestavit aplikaci, spusťte následující příkazy:

    ```cmd
    mkdir cmake
    cd cmake
    cmake .. -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -Dskip_samples:BOOL=ON
    cmake --build . -- /m /p:Configuration=Release
    ```

1. Chcete-li spustit aplikaci, spusťte následující příkaz ze stejného příkazového řádku:

    ```cmd
    pnp_app\sensor_app\Release\sensor_app.exe
    ```

## <a name="view-the-telemetry"></a>Zobrazit telemetrii

Po několika minutách uvidíte telemetrii ze zařízení na řídicím panelu zařízení v aplikaci IoT Central.

## <a name="connect-device-first"></a>Připojit zařízení – nejdřív

Zařízení IoT technologie Plug and Play můžete připojit prostřednictvím připojení zařízení, jak je popsáno v tématu [připojení](concepts-connectivity-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json). Proces zjišťování sleduje toto pořadí:

1. Přidruží se k šabloně zařízení, pokud je již publikována v aplikaci IoT Central.

1. Načte model schopností z [veřejného úložiště](https://aka.ms/ACFI) publikovaných a certifikovaných modelů schopností.

Pomocí Visual Studio Code a příkazu **Generovat zástupný kód zařízení** , který jste si poodkazovali výše, můžete podle těchto kroků připojit zařízení a automaticky přenést publikovaný model schopností zařízení z veřejného úložiště do IoT Central:

1. Použijte existující model schopností zařízení, který je publikovaný ve veřejném úložišti. Potřebujete úplný model schopností zařízení a poznamenejte si název URN tohoto modelu.

1. Pomocí výše uvedených kroků [vygenerujte klientskou aplikaci pro zařízení](#generate-a-device-client-application) , která bude používat Visual Studio Code a vygeneruje kód zařízení.

1. Z aplikace IoT Central otevřete kartu **Správa** a vyberte možnost **připojení zařízení** . Poznamenejte si **ID oboru** a hodnoty **primárního klíče** pro vaši aplikaci.

    > [!NOTE]
    > **Primární klíč** zobrazený na této stránce je sdílený přístupový podpis skupiny, který můžete použít ke generování více klíčů zařízení pro vaši aplikaci.

1. Pomocí nástroje [DPS keygen](https://www.npmjs.com/package/dps-keygen) vygenerujte klíč zařízení z primárního klíče, který jste si poznamenali dříve. Pokud chcete vygenerovat klíč zařízení, spusťte následující příkaz:

    ```cmd/sh
    dps-keygen -mk:<Primary_Key> -di:<device_id>
    ```

1. Podle postupu v předchozí části [přidejte do klienta zařízení podrobnosti o připojení](#add-connection-details-to-the-device-client) , abyste mohli přidat **ID oboru**, **primární klíč**a **ID zařízení**.

1. Postupujte podle kroků v předchozí části a sestavte [a spusťte klienta](#build-and-run-the-client).

1. Nyní se zobrazí zařízení připojit k aplikaci IoT Central a automaticky se přiřadí do modelu schopností zařízení z veřejného úložiště jako šablona zařízení.

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak připojit reálné zařízení k IoT Central, je navržený další krok, kde najdete další informace o šablonách zařízení v tématu [nastavení šablony zařízení](howto-set-up-template-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) .
