---
title: Rychlý Start – interakce se zařízením IoT technologie Plug and Play připojeným k řešení Azure IoT (Python) | Microsoft Docs
description: Rychlý Start – pomocí Pythonu se můžete připojit k zařízení IoT technologie Plug and Play, které je připojené k řešení Azure IoT, a pracovat s nimi.
author: elhorton
ms.author: elhorton
ms.date: 10/05/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 681568c3aee88483c7f9c813529f0eb500a59e93
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421494"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-device-thats-connected-to-your-solution-python"></a>Rychlý Start: interakce se zařízením IoT technologie Plug and Play připojeným k vašemu řešení (Python)

[!INCLUDE [iot-pnp-quickstarts-service-selector.md](../../includes/iot-pnp-quickstarts-service-selector.md)]

IoT technologie Plug and Play zjednodušuje IoT tím, že vám umožní pracovat s modelem zařízení bez znalosti základní implementace zařízení. V tomto rychlém startu se dozvíte, jak pomocí Pythonu připojit a řídit zařízení IoT technologie Plug and Play, které je připojené k vašemu řešení.

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

K dokončení tohoto rychlého startu potřebujete Python 3,7 na vašem vývojovém počítači. Nejnovější doporučenou verzi si můžete stáhnout z [Python.org](https://www.python.org/)na víc platforem. Verzi Pythonu si můžete ověřit pomocí následujícího příkazu:  

```cmd/sh
python --version
```

Balíček **Azure-IoT-Device** se zveřejňuje jako PIP.

V místním prostředí Python nainstalujte balíček následujícím způsobem:

```cmd/sh
pip install azure-iot-device
```

Nainstalujte balíček **Azure-IoT-Hub** spuštěním následujícího příkazu:

```cmd/sh
pip install azure-iot-hub
```

## <a name="run-the-sample-device"></a>Spuštění ukázkového zařízení

[!INCLUDE [iot-pnp-environment](../../includes/iot-pnp-environment.md)]

Další informace o ukázkové konfiguraci najdete v [ukázkovém souboru Readme](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/pnp/README.md).

V tomto rychlém startu použijete vzorový termostat zařízení napsané v Pythonu jako zařízení IoT technologie Plug and Play. Spuštění ukázkového zařízení:

1. Otevřete okno terminálu ve složce podle vašeho výběru. Spuštěním následujícího příkazu naklonujte úložiště GitHub [sady SDK služby Azure IoT](https://github.com/Azure/azure-iot-sdk-python) do tohoto umístění:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```

1. Toto okno terminálu slouží jako terminál **zařízení** . Přejděte do složky naklonovaného úložiště a přejděte do složky */Azure-IoT-SDK-Python/Azure-IoT-Device/Samples/PNP* .

1. Spusťte vzorový termostat zařízení pomocí následujícího příkazu:

    ```cmd/sh
    python simple_thermostat.py
    ```

1. Zobrazí se zpráva oznamující, že zařízení odeslalo nějaké informace a nahlásilo je jako online. Tyto zprávy signalizují, že zařízení začalo posílat data telemetrie do centra a teď je připravené přijímat příkazy a aktualizace vlastností. Nezavírejte Tento terminál, budete ho potřebovat k potvrzení, že ukázka služby funguje.

## <a name="run-the-sample-solution"></a>Spuštění ukázkového řešení

V tomto rychlém startu použijete v Pythonu ukázkové řešení IoT, které vám umožní pracovat s ukázkovým zařízením, které jste právě nastavili.

1. Otevřete další okno terminálu pro použití jako terminálu **služby** .

1. Přejděte do složky */Azure-IoT-SDK-Python/Azure-IoT-Hub/Samples* naklonovaného úložiště Python SDK.

1. Otevřete soubor *registry_manager_pnp_sample. py* a Prohlédněte si kód. V této ukázce se dozvíte, jak používat třídu **IoTHubRegistryManager** k interakci se zařízením IoT technologie Plug and Play.

> [!NOTE]
> Tyto ukázky služeb používají třídu **IoTHubRegistryManager** z **klienta služby IoT Hub**. Další informace o rozhraních API, včetně rozhraní Digital props API, najdete v [příručce pro vývojáře služby](concepts-developer-guide-service.md).

### <a name="get-the-device-twin"></a>Získat zdvojené zařízení

V [části nastavení prostředí pro iot technologie Plug and Play rychlé starty a kurzy](set-up-environment.md) , které jste vytvořili dvě proměnné prostředí pro konfiguraci ukázky pro připojení ke službě IoT Hub a zařízení:

* **IOTHUB_CONNECTION_STRING** : připojovací řetězec ke službě IoT Hub jste si poznamenali dříve.
* **IOTHUB_DEVICE_ID** : `"my-pnp-device"` .

Pomocí následujícího příkazu v terminálu **služby** spusťte tuto ukázku:

```cmd/sh
set IOTHUB_METHOD_NAME="getMaxMinReport"
set IOTHUB_METHOD_PAYLOAD="hello world"
python registry_manager_pnp_sample.py
```

> [!NOTE]
> Pokud tuto ukázku spouštíte v systému Linux, použijte `export` místo `set` .

Výstup zobrazuje nevlákenné zařízení a vytiskne jeho ID modelu:

```cmd/sh
The Model ID for this device is:
dtmi:com:example:Thermostat;1
```

Následující fragment kódu ukazuje vzorový kód z *registry_manager_pnp_sample. py* :

```python
    # Create IoTHubRegistryManager
    iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

    # Get device twin
    twin = iothub_registry_manager.get_twin(device_id)
    print("The device twin is: ")
    print("")
    print(twin)
    print("")

    # Print the device's model ID
    additional_props = twin.additional_properties
    if "modelId" in additional_props:
        print("The Model ID for this device is:")
        print(additional_props["modelId"])
        print("")
```

### <a name="update-a-device-twin"></a>Aktualizace vlákna v zařízení

V této ukázce se dozvíte, jak aktualizovat `targetTemperature` vlastnost s možností zápisu v zařízení:

```python
    # Update twin
    twin_patch = Twin()
    twin_patch.properties = TwinProperties(
        desired={"targetTemperature": 42}
    )  # this is relevant for the thermostat device sample
    updated_twin = iothub_registry_manager.update_twin(device_id, twin_patch, twin.etag)
    print("The twin patch has been successfully applied")
    print("")
```

Můžete ověřit, že se aktualizace aplikuje v terminálu **zařízení** , kde se zobrazí následující výstup:

```cmd/sh
the data in the desired properties patch was: {'targetTemperature': 42, '$version': 2}
```

Terminál **služby** potvrzuje, že byla oprava úspěšná:

```cmd/sh
The twin patch has been successfully applied
```

### <a name="invoke-a-command"></a>Vyvolání příkazu

Ukázka potom vyvolá příkaz:

Terminál **služby** Zobrazuje potvrzovací zprávu od zařízení:

```cmd/sh
The device method has been successfully invoked
```

V terminálu **zařízení** se zobrazí, že zařízení přijímá příkaz:

```cmd/sh
Command request received with payload
hello world
Will return the max, min and average temperature from the specified time hello to the current time
Done generating
{"tempReport": {"avgTemp": 34.2, "endTime": "09/07/2020 09:58:11", "maxTemp": 49, "minTemp": 10, "startTime": "09/07/2020 09:56:51"}}
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak připojit zařízení IoT technologie Plug and Play k řešení IoT. Další informace o modelech zařízení IoT technologie Plug and Play najdete v tématech:

> [!div class="nextstepaction"]
> [Příručka pro vývojáře IoT technologie Plug and Play Modeling](concepts-developer-guide-device-csharp.md)
