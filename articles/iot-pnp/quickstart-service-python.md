---
title: Interakce se zařízením IoT technologie Plug and Play připojeným k řešení Azure IoT (Python) | Microsoft Docs
description: Pomocí Pythonu se můžete připojit k zařízení IoT technologie Plug and Play, které je připojené k řešení Azure IoT, a pracovat s nimi.
author: elhorton
ms.author: elhorton
ms.date: 7/13/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: be5ff3e863752dfc187bd91257425af5e8de85c4
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91574958"
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

1. Ve složce Samples jsou čtyři ukázkové soubory, které demonstrují operace s digitálním podtřídou správce: *get_digital_twin_sample. py, update_digitial_twin_sample. py, invoke_command_sample. py a invoke_component_command_sample-. py*.  Tyto ukázky ukazují, jak používat jednotlivá rozhraní API pro komunikaci se zařízeními IoT technologie Plug and Play:

### <a name="get-digital-twin"></a>Získání digitálního vlákna

V [části nastavení prostředí pro iot technologie Plug and Play rychlé starty a kurzy](set-up-environment.md) , které jste vytvořili dvě proměnné prostředí pro konfiguraci ukázky pro připojení ke službě IoT Hub a zařízení:

* **IOTHUB_CONNECTION_STRING**: připojovací řetězec ke službě IoT Hub jste si poznamenali dříve.
* **IOTHUB_DEVICE_ID**: `"my-pnp-device"` .

Pomocí následujícího příkazu v terminálu **služby** spusťte tuto ukázku:

```cmd/sh
python get_digital_twin_sample.py
```

Výstup ukazuje digitální vyzdvojení zařízení a vytiskne jeho ID modelu:

```cmd/sh
{'$dtId': 'mySimpleThermostat', '$metadata': {'$model': 'dtmi:com:example:Thermostat;1'}}
Model Id: dtmi:com:example:Thermostat;1
```

Následující fragment kódu ukazuje vzorový kód z *get_digital_twin_sample. py*:

```python
    # Get digital twin and retrieve the modelId from it
    digital_twin = iothub_digital_twin_manager.get_digital_twin(device_id)
    if digital_twin:
        print(digital_twin)
        print("Model Id: " + digital_twin["$metadata"]["$model"])
    else:
        print("No digital_twin found")
```

### <a name="update-a-digital-twin"></a>Aktualizace digitálního vlákna

V této ukázce se dozvíte, jak pomocí *opravy* aktualizovat vlastnosti pomocí digitálního vlákna vašeho zařízení. Následující fragment kódu z *update_digital_twin_sample. py* ukazuje, jak sestavit opravu:

```python
# If you already have a component thermostat1:
# patch = [{"op": "replace", "path": "/thermostat1/targetTemperature", "value": 42}]
patch = [{"op": "add", "path": "/targetTemperature", "value": 42}]
iothub_digital_twin_manager.update_digital_twin(device_id, patch)
print("Patch has been succesfully applied")
```

Pomocí následujícího příkazu v terminálu **služby** spusťte tuto ukázku:

```cmd/sh
python update_digital_twin_sample.py
```

Můžete ověřit, že se aktualizace aplikuje v terminálu **zařízení** , kde se zobrazí následující výstup:

```cmd/sh
the data in the desired properties patch was: {'targetTemperature': 42, '$version': 2}
previous values
42
```

Terminál **služby** potvrzuje, že byla oprava úspěšná:

```cmd/sh
Patch has been successfully applied
```

### <a name="invoke-a-command"></a>Vyvolání příkazu

Chcete-li vyvolat příkaz, spusťte ukázku *invoke_command_sample. py* . Tento příklad ukazuje, jak vyvolat příkaz v jednoduchém zařízení termostatu. Před spuštěním této ukázky nastavte `IOTHUB_COMMAND_NAME` `IOTHUB_COMMAND_PAYLOAD` proměnné prostředí a v terminálu **služby** :

```cmd/sh
set IOTHUB_COMMAND_NAME="getMaxMinReport" # this is the relevant command for the thermostat sample
set IOTHUB_COMMAND_PAYLOAD="hello world" # this payload doesn't matter for this sample
```

V terminálu **služby** použijte následující příkaz ke spuštění ukázky:
  
```cmd/sh
python invoke_command_sample.py
```

Terminál **služby** Zobrazuje potvrzovací zprávu od zařízení:

```cmd/sh
{"tempReport": {"avgTemp": 34.5, "endTime": "13/07/2020 16:03:38", "maxTemp": 49, "minTemp": 11, "startTime": "13/07/2020 16:02:18"}}
```

V terminálu **zařízení** se zobrazí, že zařízení přijímá příkaz:

```cmd/sh
Command request received with payload
hello world
Will return the max, min and average temperature from the specified time hello to the current time
Done generating
{"tempReport": {"avgTemp": 34.2, "endTime": "09/07/2020 09:58:11", "maxTemp": 49, "minTemp": 10, "startTime": "09/07/2020 09:56:51"}}
Sent message
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak připojit zařízení IoT technologie Plug and Play k řešení IoT. Další informace o modelech zařízení IoT technologie Plug and Play najdete v tématech:

> [!div class="nextstepaction"]
> [Příručka pro vývojáře IoT technologie Plug and Play Modeling](concepts-developer-guide-device-csharp.md)
