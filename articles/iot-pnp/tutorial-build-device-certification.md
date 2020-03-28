---
title: Sestavte si zařízení IoT Plug and Play Preview, které je připravené k certifikaci | Dokumenty společnosti Microsoft
description: Jako vývojář zařízení se dozvíte, jak můžete vytvořit zařízení IoT Plug and Play Preview, které je připravené k certifikaci.
author: tbhagwat3
ms.author: tanmayb
ms.date: 12/28/2019
ms.topic: tutorial
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: e97aa07d2a43a03805fd881c674157ee676c37b4
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2020
ms.locfileid: "80239910"
---
# <a name="build-an-iot-plug-and-play-preview-device-thats-ready-for-certification"></a>Sestavte si zařízení IoT Plug and Play Preview, které je připravené k certifikaci

Tento kurz popisuje, jak jako vývojář zařízení můžete vytvořit zařízení IoT Plug and Play Preview, které je připravené k certifikaci.

Certifikační testy zkontrolují, zda:

- Kód zařízení IoT Plug and Play je na vašem zařízení nainstalovaný.
- Kód zařízení IoT Plug and Play je vytvořený pomocí sady Azure IoT SDK.
- Kód vašeho zařízení podporuje [službu Azure IoT Hub Device Provisioning Service](../iot-dps/about-iot-dps.md).
- Kód zařízení implementuje rozhraní Informace o zařízení.
- Model schopností a kód zařízení pracují s IoT Central.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

- [Kód visual studia](https://code.visualstudio.com/download)
- Rozšíření rozšíření [Azure IoT Tools for VS Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

K vytvoření rychlého spuštění zařízení pro Windows je také nutné dokončit [model funkce zařízení.](quickstart-create-pnp-device-windows.md) Rychlý start ukazuje, jak nastavit vývojové prostředí pomocí Vcpkg a vytvořit ukázkový projekt.

## <a name="store-a-capability-model-and-interfaces"></a>Uložení modelu schopností a rozhraní

Pro zařízení IoT Plug and Play musíte vytvořit model schopností a rozhraní, která definují možnosti zařízení jako soubory JSON.

Tyto soubory JSON můžete uložit do tří různých umístění:

- Úložiště veřejného modelu.
- Úložiště modelu společnosti.
- Na vašem zařízení.

V současné době musí být soubory, aby bylo vaše zařízení certifikováno, uloženy buď v úložišti firemního modelu, nebo v úložišti veřejného modelu.

## <a name="include-the-required-interfaces"></a>Zahrnout požadovaná rozhraní

Chcete-li předat proces certifikace, musíte zahrnout a implementovat **rozhraní Informace o zařízení** v modelu schopností. Toto rozhraní má následující identifikaci:

```json
"@id": "urn:azureiot:DeviceManagement:DeviceInformation:1"
```

> [!NOTE]
> Pokud jste dokončili [úvodní příručku: K vytvoření zařízení použijte model schopností zařízení](quickstart-create-pnp-device-windows.md), už jste do modelu zahrnuli rozhraní Informace o **zařízení.**

Chcete-li zahrnout **rozhraní Informace o zařízení** do modelu `implements` zařízení, přidejte ID rozhraní do vlastnosti modelu schopností:

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

Zobrazení **informačního** rozhraní zařízení v kódu VS:

1. K otevření palety příkazů použijte **kombinaci kláves Ctrl+Shift+P.**

1. Zadejte **Plug and Play** a pak vyberte příkaz **IoT Plug and Play Open Model Repository** . Zvolte **Otevřít úložiště veřejných modelů**. Úložiště veřejného modelu se otevře v kódu VS.

1. V úložišti veřejného modelu vyberte kartu **Rozhraní,** vyberte ikonu filtru a do pole filtru zadejte **Informace o zařízení.**

1. Chcete-li vytvořit místní kopii **informačního rozhraní zařízení,** vyberte ji ve filtrovaném seznamu a pak vyberte **stáhnout**. Kód VS zobrazí soubor rozhraní.

Zobrazení **rozhraní Informace o zařízení** pomocí rozhraní příkazového řádku Azure:

1. [Nainstalujte rozšíření Azure IoT CLI](howto-install-pnp-cli.md).

1. Pomocí následujícího příkazu Azure CLI zobrazte rozhraní s ID rozhraní Informace o zařízení:

    ```azurecli
    az iot pnp interface show --interface urn:azureiot:DeviceManagement:DeviceInformation:1
    ```

Další informace najdete [v tématu Instalace a použití rozšíření Azure IoT pro Azure CLI](howto-install-pnp-cli.md).

## <a name="update-device-code"></a>Aktualizovat kód zařízení

### <a name="enable-device-provisioning-through-the-azure-iot-device-provisioning-service-dps"></a>Povolení zřizování zařízení prostřednictvím služby Azure IoT Device Provisioning Service (DPS)

Chcete-li certifikovat zařízení, musí povolit zřizování prostřednictvím [služby Azure IoT Device Provisioning Service (DPS).](https://docs.microsoft.com/azure/iot-dps/about-iot-dps) Chcete-li přidat možnost používat DPS, můžete vygenerovat kód C se zakázaným inzerováním v kódu VS. Postupujte následovně:

1. Otevřete složku se souborem DCM ve VS Code, pomocí **Ctrl+Shift+P** otevřete paletu příkazů, zadejte **IoT Plug and Play**a vyberte **Generovat kód zařízení .**

1. Zvolte soubor DCM, který chcete použít ke generování kódu zařízení se zakázaným inzerováním.

1. Zadejte název projektu, například **sample_device**. Toto je název aplikace zařízení.

1. Jako jazyk zvolte **ANSI C.**

1. Jako metodu připojení zvolte **Symetrický klíč Via DPS (Device Provisioning Service).**

1. Jako šablonu projektu zvolte **CMake Project ve Windows.**

1. Jako způsob zahrnutí sady SDK zařízení zvolte **Via Vcpkg.**

1. VS Code otevře nové okno s generovanými soubory se zakázaným inzerováním kódu zařízení.

## <a name="build-and-run-the-code"></a>Sestavení a spuštění kódu

Balíček Vcpkg slouží k vytvoření kódu generovaného zařízení se zakázaným inzerováním. Aplikace, kterou vytvoříte, simuluje zařízení, které se připojuje k centru IoT. Aplikace odesílá telemetrii a vlastnosti a přijímá příkazy.

1. Vytvořte `cmake` podadresář `sample_device` ve složce a přejděte do této složky:

    ```cmd
    mkdir cmake
    cd cmake
    ```

1. Spusťte následující příkazy k vytvoření protokolu se zakázaným inzerováním generovaného kódu (nahrazení zástupného symbolu adresářem úložiště Vcpkg):

    ```cmd
    cmake .. -G "Visual Studio 16 2019" -A Win32 -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"

    cmake --build .
    ```
    
    > [!NOTE]
    > Pokud používáte Visual Studio 2017 nebo 2015, je třeba zadat generátor CMake na základě nástrojů sestavení, které používáte:
    >```cmd
    ># Either
    >cmake .. -G "Visual Studio 15 2017" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}\scripts\buildsystems\vcpkg.cmake"
    ># or
    >cmake .. -G "Visual Studio 14 2015" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}\scripts\buildsystems\vcpkg.cmake"
    >```

    > [!NOTE]
    > Pokud cmake nemůže najít kompilátor Jazyka C++, zobrazí se chyby sestavení při spuštění předchozího příkazu. Pokud k tomu dojde, zkuste spustit tento příkaz na [příkazovém řádku sady Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs).

1. Po úspěšném dokončení sestavení zadejte pověření DPS (**DPS ID Obor**, **Symetrický klíč DPS**, **ID zařízení**) jako parametry pro aplikaci. Pokud chcete získat přihlašovací údaje z certifikačního portálu, [přečtěte si informace o připojení a otestování zařízení IoT Plug and Play](tutorial-certification-test.md#connect-and-discover-interfaces).

    ```cmd\sh
    .\Debug\sample_device.exe [Device ID] [DPS ID Scope] [DPS symmetric key]
    ```

### <a name="implement-standard-interfaces"></a>Implementace standardních rozhraní

#### <a name="implement-the-model-information-and-sdk-information-interfaces"></a>Implementace rozhraní Informace o modelu a Informační sada SDK

Sada Azure IoT device SDK implementuje rozhraní Informace o modelu a Informační rozhraní sady SDK. Pokud používáte funkci generování kódu v kódu VS, kód vašeho zařízení používá sdk zařízení IoT Plug and Play.

Pokud jste se rozhodli nepoužívat Azure IoT zařízení SDK, můžete použít zdrojový kód Sady SDK jako odkaz pro vlastní implementaci.

#### <a name="implement-the-device-information-interface"></a>Implementace informačního rozhraní o zařízení

Implementujte rozhraní **Informace o zařízení** v zařízení a za běhu poskytněte informace specifické pro zařízení ze zařízení.

Jako referenci můžete použít příklad implementace **informačního** rozhraní o zařízení pro [Linux.](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview)

### <a name="implement-all-the-capabilities-defined-in-your-model"></a>Implementace všech funkcí definovaných ve vašem modelu

Během certifikace je vaše zařízení testováno programově, aby bylo zajištěno, že implementuje funkce definované v jeho rozhraních. Pomocí stavového kódu HTTP 501 můžete reagovat na požadavky na vlastnosti a příkazy pro čtení a zápis, pokud je vaše zařízení neimplementuje.

## <a name="next-steps"></a>Další kroky

Teď, když jste si vytvořili zařízení IoT Plug and Play připravené k certifikaci, je navrhovaným dalším krokem:

> [!div class="nextstepaction"]
> [Přečtěte si, jak certifikovat zařízení](tutorial-certification-test.md)
