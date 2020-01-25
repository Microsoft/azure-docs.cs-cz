---
title: Sestavení zařízení IoT technologie Plug and Play ve verzi Preview, které je připravené k certifikaci | Microsoft Docs
description: Jako vývojář zařízení se dozvíte, jak můžete vytvořit zařízení IoT technologie Plug and Play ve verzi Preview, které je připravené k certifikaci.
author: tbhagwat3
ms.author: tanmayb
ms.date: 12/28/2019
ms.topic: tutorial
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: ce7d3ee8a0d05d837bc0049cba688cffe14d8a8c
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721638"
---
# <a name="build-an-iot-plug-and-play-preview-device-thats-ready-for-certification"></a>Sestavení zařízení IoT technologie Plug and Play ve verzi Preview, které je připravené k certifikaci

V tomto kurzu se dozvíte, jak jako vývojář zařízení můžete vytvořit zařízení IoT technologie Plug and Play ve verzi Preview, které je připravené k certifikaci.

Ověřovací testy kontrolují tyto podmínky:

- Váš kód zařízení technologie Plug and Play IoT se nainstaluje do vašeho zařízení.
- Váš kód zařízení technologie Plug and Play IoT je sestavený pomocí sady Azure IoT SDK.
- Váš kód zařízení podporuje [IoT Hub Device Provisioning Service Azure](../iot-dps/about-iot-dps.md).
- Váš kód zařízení implementuje rozhraní informací o zařízení.
- Model schopností a kód zařízení fungují s IoT Central.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

- [Visual Studio Code](https://code.visualstudio.com/download)
- Sady [nástrojů Azure IoT pro rozšíření vs Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) Extension Pack

K vytvoření rychlého startu zařízení pro Windows je také potřeba dokončit [model použití schopností zařízení](quickstart-create-pnp-device-windows.md) . V tomto rychlém startu se dozvíte, jak nastavit vývojové prostředí pomocí Vcpkg a vytvořit ukázkový projekt.

## <a name="store-a-capability-model-and-interfaces"></a>Uložení modelu a rozhraní schopností

Pro zařízení IoT technologie Plug and Play musíte vytvořit model schopností a rozhraní, které definují možnosti zařízení jako soubory JSON.

Tyto soubory JSON můžete uložit ve třech různých umístěních:

- Úložiště veřejného modelu.
- Úložiště podnikového modelu.
- Na vašem zařízení.

V současné době, aby se vaše zařízení osvědčilo, soubory musí být uložené buď v úložišti podnikového úložiště, nebo ve veřejném úložišti modelu.

## <a name="include-the-required-interfaces"></a>Zahrnutí požadovaných rozhraní

K předání procesu certifikace musíte do svého modelu schopností zahrnout a implementovat rozhraní **informací o zařízení** . Toto rozhraní má následující identifikaci:

```json
"@id": "urn:azureiot:DeviceManagement:DeviceInformation:1"
```

> [!NOTE]
> Pokud jste dokončili [rychlý Start: k vytvoření zařízení použít model schopností zařízení](quickstart-create-pnp-device-windows.md), už jste do svého modelu zahrnuli rozhraní **informací o zařízení** .

Chcete-li do modelu zařízení zahrnout **informace o rozhraní informací o zařízení** , přidejte ID rozhraní do vlastnosti `implements` modelu schopností:

```json
{
  "@id": "urn:yourcompanyname:sample:ThermostatDevice:1",
  "@type": "CapabilityModel",
  "displayName": "Thermostat T-1000",
  "implements": [
    "urn:yourcompanyname:sample:Thermostat:1",
    "urn:azureiot:DeviceManagement:DeviceInformation:1"
  ],
  "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
}
```

Postup zobrazení rozhraní **informací o zařízení** v vs Code:

1. Stisknutím **kombinace kláves CTRL + SHIFT + P** otevřete paletu příkazů.

1. Zadejte **technologie Plug and Play** a pak vyberte příkaz **IoT technologie Plug and Play Open model úložiště** . Vyberte **Otevřít úložiště veřejného modelu**. Úložiště veřejného modelu se otevře v VS Code.

1. V úložišti veřejného modelu vyberte kartu **rozhraní** , vyberte ikonu filtru a do pole Filtr zadejte **informace o zařízení** .

1. Pokud chcete vytvořit místní kopii rozhraní **informací o zařízení** , vyberte ji v seznamu filtrovaných a pak vyberte **Stáhnout**. VS Code zobrazí soubor rozhraní.

Postup zobrazení rozhraní **informací o zařízení** pomocí rozhraní příkazového řádku Azure:

1. [Nainstalujte rozšíření Azure IoT CLI](howto-install-pnp-cli.md).

1. Pomocí následujícího příkazu rozhraní příkazového řádku Azure zobrazíte rozhraní s ID rozhraní informací o zařízení:

    ```cmd/sh
    az iot pnp interface show --interface urn:azureiot:DeviceManagement:DeviceInformation:1
    ```

Další informace najdete v tématu [instalace a použití rozšíření Azure IoT pro Azure CLI](howto-install-pnp-cli.md).

## <a name="update-device-code"></a>Aktualizovat kód zařízení

### <a name="enable-device-provisioning-through-the-azure-iot-device-provisioning-service-dps"></a>Povolení zřizování zařízení prostřednictvím služby Azure IoT Device Provisioning (DPS)

Aby zařízení bylo možné certifikovat, musí povolit zřizování prostřednictvím [služby Azure IoT Device Provisioning (DPS)](https://docs.microsoft.com/azure/iot-dps/about-iot-dps). Chcete-li přidat možnost použití DPS, můžete vygenerovat zástupnou proceduru kódu C v kódu VS. Postupujte následovně:

1. Otevřete složku se souborem DCM v VS Code, pomocí **kombinace kláves CTRL + SHIFT + P** otevřete paletu příkazů, zadejte **IoT technologie Plug and Play**a vyberte **vygenerovat zástupnou proceduru pro kód zařízení**.

1. Vyberte soubor DCM, který chcete použít k vygenerování zástupné procedury kódu zařízení.

1. Zadejte název projektu, například **sample_device**. Toto je název aplikace zařízení.

1. Jako jazyk vyberte **ANSI C** .

1. Jako metodu připojení vyberte možnost **přes DPS (služba Device Provisioning Service)** .

1. Jako šablonu projektu vyberte možnost **projekt cmake v systému Windows** .

1. Vyberte **prostřednictvím Vcpkg** jako způsob, jak zahrnout sadu SDK pro zařízení.

1. VS Code otevře nové okno s generovanými zástupnými soubory kódu zařízení.

## <a name="build-and-run-the-code"></a>Sestavit a spustit kód

Pomocí balíčku Vcpkg vytvoříte zástupný kód vygenerovaných kódů zařízení. Vytvořená aplikace simuluje zařízení, které se připojuje ke službě IoT Hub. Aplikace odesílá telemetrie a vlastnosti a přijímá příkazy.

1. Vytvořte podadresář `cmake` ve složce `sample_device` a přejděte do této složky:

    ```cmd
    mkdir cmake
    cd cmake
    ```

1. Spuštěním následujících příkazů Sestavte zástupnou proceduru vygenerovaného kódu (zástupný symbol nahraďte adresářem úložiště Vcpkg):

    ```cmd
    cmake .. -G "Visual Studio 16 2019" -A Win32 -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"

    cmake --build .
    ```
    
    > [!NOTE]
    > Pokud používáte sadu Visual Studio 2017 nebo 2015, je nutné určit generátor CMake na základě nástrojů pro sestavení, které používáte:
    >```cmd
    ># Either
    >cmake .. -G "Visual Studio 15 2017" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}\scripts\buildsystems\vcpkg.cmake"
    ># or
    >cmake .. -G "Visual Studio 14 2015" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}\scripts\buildsystems\vcpkg.cmake"
    >```

    > [!NOTE]
    > Pokud cmake nenajde váš C++ kompilátor, při spuštění předchozího příkazu se zobrazí chyby sestavení. Pokud k tomu dojde, zkuste spustit tento příkaz na příkazovém [řádku sady Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs).

1. Po úspěšném dokončení sestavení zadejte přihlašovací údaje k DPS (**Rozsah ID DPS**, **symetrický klíč DPS**, **ID zařízení**) jako parametry pro aplikaci. Pokud chcete získat přihlašovací údaje z certifikačního portálu, přečtěte si téma [připojení a testování zařízení IoT technologie Plug and Play](tutorial-certification-test.md#connect-and-discover-interfaces).

    ```cmd\sh
    .\Debug\sample_device.exe [Device ID] [DPS ID Scope] [DPS symmetric key]
    ```

### <a name="implement-standard-interfaces"></a>Implementovat standardní rozhraní

#### <a name="implement-the-model-information-and-sdk-information-interfaces"></a>Implementace informací o modelu a informačních rozhraní SDK

Sada SDK pro zařízení Azure IoT implementuje rozhraní informace o modelu a informace o sadě SDK. Pokud použijete funkci generování kódu v VS Code, váš kód zařízení používá sadu SDK pro zařízení IoT technologie Plug and Play.

Pokud se rozhodnete nepoužívat sadu SDK pro zařízení Azure IoT, můžete použít zdrojový kód sady SDK jako referenci pro vlastní implementaci.

#### <a name="implement-the-device-information-interface"></a>Implementace rozhraní informací o zařízení

Implementujte na svém zařízení rozhraní **informací o zařízení** a poskytněte v zařízení v době běhu informace specifické pro zařízení.

Jako referenční informace můžete použít ukázkovou implementaci rozhraní **informací o zařízení** pro [Linux](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) .

### <a name="implement-all-the-capabilities-defined-in-your-model"></a>Implementace všech schopností definovaných ve vašem modelu

Během certifikace se vaše zařízení testuje programově, aby se zajistilo, že implementuje funkce definované v jeho rozhraních. Pokud vaše zařízení neimplementuje požadavky na vlastnost pro čtení a zápis, použijte kód stavu HTTP 501.

## <a name="next-steps"></a>Další kroky

Teď, když jste vytvořili zařízení IoT technologie Plug and Play připravené k certifikaci, je navržený další krok:

> [!div class="nextstepaction"]
> [Naučte se, jak zařízení certifikovat.](tutorial-certification-test.md)
