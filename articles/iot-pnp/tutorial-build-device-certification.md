---
title: Sestavte si zařízení Azure IoT technologie Plug and Play ve verzi Preview, které je připravené k certifikaci | Microsoft Docs
description: Jako vývojář zařízení se dozvíte, jak můžete vytvořit zařízení IoT technologie Plug and Play ve verzi Preview, které je připravené k certifikaci.
author: tbhagwat3
ms.author: tanmayb
ms.date: 06/28/2019
ms.topic: tutorial
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: bfa611eba8e7a990626fbace8b930962615e0594
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878093"
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
- [Rozšíření Azure IoT Workbench pro VS Code](https://github.com/Azure/Azure-IoT-PnP-Preview/blob/master/VSCode/README.md#installation)

Také budete potřebovat zařízení IoT technologie Plug and Play, které vytvoříte v [rychlém startu: Použijte model schopností zařízení k vytvoření zařízení](quickstart-create-pnp-device.md).

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
> Pokud jste dokončili [rychlý Start: Použijte model schopností zařízení k vytvoření zařízení](quickstart-create-pnp-device.md). v modelu už jste zahrnuli rozhraní **informací o zařízení** .

Chcete-li do modelu zařízení zahrnout **informace o rozhraní informací o zařízení** , přidejte ID rozhraní `implements` do vlastnosti modelu schopností:

```json
{
  "@id": "urn:yourcompanyname:sample:ThermostatDevice:1",
  "@type": "CapabilityModel",
  "displayName": "Thermostat T-1000",
  "implements": [
    "urn:yourcompanyname:sample:Thermostat:1",
    "urn:azureiot:DeviceManagement:DeviceInformation:1"
  ],
  "@context": "http://azureiot.com/v1/contexts/CapabilityModel.json"
}
```

Postup zobrazení rozhraní **informací o zařízení** v vs Code:

1. Stisknutím **kombinace kláves CTRL + SHIFT + P** otevřete paletu příkazů.

1. Zadejte **technologie Plug and Play** a pak vyberte příkaz **IoT technologie Plug and Play Open model úložiště** . Vyberte **Otevřít úložiště veřejného modelu**. Úložiště veřejného modelu se otevře v VS Code.

1. V úložišti veřejného modelu vyberte kartu **rozhraní** , vyberte ikonu filtru a do pole Filtr zadejte **informace o zařízení** .

1. Pokud chcete vytvořit místní kopii rozhraní **informací o zařízení** , vyberte ji v seznamu filtrovaných a pak vyberte **Stáhnout**. VS Code zobrazí soubor rozhraní.

## <a name="update-device-code"></a>Aktualizovat kód zařízení

### <a name="enable-device-provisioning-through-the-azure-iot-device-provisioning-service-dps"></a>Povolení zřizování zařízení prostřednictvím služby Azure IoT Device Provisioning (DPS)

Aby zařízení bylo možné certifikovat, musí povolit zřizování prostřednictvím [služby Azure IoT Device Provisioning (DPS)](https://docs.microsoft.com/azure/iot-dps/about-iot-dps). Chcete-li přidat možnost použití DPS, můžete vygenerovat zástupnou proceduru kódu C v kódu VS. Postupujte následovně:

1. Otevřete složku se souborem DCM v VS Code, pomocí **kombinace kláves CTRL + SHIFT + P** otevřete paletu příkazů, zadejte **IoT technologie Plug and Play**a vyberte vygenerovat zástupnou proceduru pro **kód zařízení**.

1. Vyberte soubor DCM, který chcete použít k vygenerování zástupné procedury kódu zařízení.

1. Zadejte název projektu, jedná se o název aplikace vašeho zařízení.

1. Jako jazyk vyberte **ANSI C** .

1. Jako typ projektu vyberte **cmake projekt** .

1. Jako metodu připojení vyberte možnost **přes DPS (služba Device Provisioning Service)** .

1. VS Code otevře nové okno s generovanými zástupnými soubory kódu zařízení.

1. Otevřete `main.c`, vyplňte **dpsIdScope**, **sasKey**a **registrationId** , které jste připravili. Tyto informace můžete získat z certifikačního portálu. Další informace najdete v tématu [připojení a testování zařízení IoT technologie Plug and Play](tutorial-certification-test.md#connect-and-discover-interfaces).

    ```c
    // TODO: Specify DPS scope ID if you intend on using DPS / IoT Central.
    static const char *dpsIdScope = "[DPS Id Scope]";
    
    // TODO: Specify symmetric keys if you intend on using DPS / IoT Central and symmetric key based auth.
    static const char *sasKey = "[DPS symmetric key]";
    
    // TODO: specify your device registration ID
    static const char *registrationId = "[device registration Id]";
    ```

1. Uložte soubor.

### <a name="implement-standard-interfaces"></a>Implementovat standardní rozhraní

#### <a name="implement-the-model-information-and-sdk-information-interfaces"></a>Implementace informací o modelu a informačních rozhraní SDK

Sada SDK pro zařízení Azure IoT implementuje rozhraní informace o modelu a informace o sadě SDK. Pokud použijete funkci generování kódu v VS Code, váš kód zařízení používá sadu SDK pro zařízení Azure IoT technologie Plug and Play.

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
