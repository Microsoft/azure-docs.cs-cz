---
title: Interakce se zařízením IoT technologie Plug and Play ve verzi Preview připojeným k řešení Azure IoT (Python) | Microsoft Docs
description: Pomocí Pythonu se můžete připojit k zařízení IoT technologie Plug and Play Preview, které je připojené k řešení Azure IoT, a pracovat s nimi.
author: elhorton
ms.author: elhorton
ms.date: 7/13/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: e3f00bb601cce17721c5247941588be1c2de788d
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352925"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-python"></a>Rychlý Start: interakce se zařízením IoT technologie Plug and Play ve verzi Preview, které je připojené k vašemu řešení (Python)

[!INCLUDE [iot-pnp-quickstarts-service-selector.md](../../includes/iot-pnp-quickstarts-service-selector.md)]

IoT technologie Plug and Play Preview zjednodušuje IoT tím, že vám umožní pracovat s modelem zařízení bez znalosti základní implementace zařízení. V tomto rychlém startu se dozvíte, jak pomocí Pythonu připojit a řídit zařízení IoT technologie Plug and Play, které je připojené k vašemu řešení.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu potřebujete Python 3,7 na vašem vývojovém počítači. Nejnovější doporučenou verzi si můžete stáhnout z [Python.org](https://www.python.org/)na víc platforem. Verzi Pythonu si můžete ověřit pomocí následujícího příkazu:  

```cmd/sh
python --version
```

Spuštěním následujícího příkazu nainstalujte [balíček služby Python Service SDK Preview](https://pypi.org/project/azure-iot-hub/2.2.1rc0/) :

```cmd/sh
pip3 install azure-iot-hub==2.2.1rc0
```

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Spuštěním následujícího příkazu Získejte _připojovací řetězec služby IoT Hub_ pro vaše centrum. Poznamenejte si tento připojovací řetězec, budete ho používat později v tomto rychlém startu:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

Spuštěním následujícího příkazu Získejte _připojovací řetězec zařízení_ pro zařízení, které jste přidali do centra. Poznamenejte si tento připojovací řetězec, budete ho používat později v tomto rychlém startu:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output
```

## <a name="run-the-sample-device"></a>Spuštění ukázkového zařízení

V tomto rychlém startu použijete vzorový termostat zařízení napsané v Pythonu jako zařízení IoT technologie Plug and Play. Spuštění ukázkového zařízení:

1. Otevřete okno terminálu ve složce podle vašeho výběru. Spuštěním následujícího příkazu naklonujte úložiště GitHub [sady SDK služby Azure IoT](https://github.com/Azure/azure-iot-sdk-python) do tohoto umístění:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```

1. Toto okno terminálu slouží jako terminál **zařízení** . Přejděte do složky naklonovaného úložiště a přejděte do složky */Azure-IoT-SDK-Python/Azure-IoT-Device/Samples/PNP* .

1. Konfigurace _připojovacího řetězce zařízení_:

    ```cmd/sh
    set IOTHUB_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Spusťte vzorový termostat zařízení pomocí následujícího příkazu:

    ```cmd/sh
    python pnp_thermostat.py
    ```

1. Zobrazí se zpráva oznamující, že zařízení odeslalo nějaké informace a nahlásilo je jako online. Tyto zprávy signalizují, že zařízení začalo posílat data telemetrie do centra a teď je připravené přijímat příkazy a aktualizace vlastností. Nezavírejte Tento terminál, budete ho potřebovat k potvrzení, že ukázka služby funguje.

## <a name="run-the-sample-solution"></a>Spuštění ukázkového řešení

V tomto rychlém startu použijete v Pythonu ukázkové řešení IoT, které vám umožní pracovat s ukázkovým zařízením, které jste právě nastavili.

1. Otevřete další okno terminálu pro použití jako terminálu **služby** . Sada SDK služby je ve verzi Preview, takže je nutné klonovat ukázky z [větve verze Preview sady Python SDK](https://github.com/Azure/azure-iot-sdk-python/tree/pnp-preview-refresh):

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-python -b pnp-preview-refresh
    ```

1. Přejděte do složky této větve klonovaného úložiště a přejděte do složky */Azure-IoT-SDK-Python/Azure-IoT-Hub/Samples* .

1. Nakonfigurujte proměnné prostředí pro ID zařízení a _připojovací řetězec IoT Hub_:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIOTHubConnectionString>
    set IOTHUB_DEVICE_ID=<Your device ID>
    ```

1. Ve složce Samples jsou čtyři ukázkové soubory s `pnp` předponou. Tyto ukázky ukazují, jak používat jednotlivá rozhraní API pro komunikaci se zařízeními IoT technologie Plug and Play:

### <a name="get-digital-twin"></a>Získání digitálního vlákna

Pomocí následujícího příkazu v terminálu **služby** spusťte tuto ukázku:

```cmd/sh
python pnp_get_digital_twin_sample.py
```

Výstup ukazuje digitální vyzdvojení zařízení a vytiskne jeho ID modelu:

```cmd/sh
{'$dtId': 'mySimpleThermostat', '$metadata': {'$model': 'dtmi:com:example:Thermostat;1'}}
Model Id: dtmi:com:example:Thermostat;1
```

Následující fragment kódu ukazuje vzorový kód z *pnp_get_digital_twin_sample. py*:

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

V této ukázce se dozvíte, jak pomocí *opravy* aktualizovat vlastnosti pomocí digitálního vlákna vašeho zařízení. Následující fragment kódu z *pnp_update_digital_twin_sample. py* ukazuje, jak sestavit opravu:

```python
# If you already have a component thermostat1:
# patch = [{"op": "replace", "path": "/thermostat1/targetTemperature", "value": 42}]
patch = [{"op": "add", "path": "/targetTemperature", "value": 42}]
iothub_digital_twin_manager.update_digital_twin(device_id, patch)
print("Patch has been succesfully applied")
```

Pomocí následujícího příkazu v terminálu **služby** spusťte tuto ukázku:

```cmd/sh
python pnp_update_digital_twin_sample.py
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

Chcete-li vyvolat příkaz, spusťte ukázku *pnp_invoke_command_sample. py* . Tento příklad ukazuje, jak vyvolat příkaz v jednoduchém zařízení termostatu. Před spuštěním této ukázky nastavte `IOTHUB_COMMAND_NAME` `IOTHUB_COMMAND_PAYLOAD` proměnné prostředí a v terminálu **služby** :

```cmd/sh
set IOTHUB_COMMAND_NAME="getMaxMinReport" # this is the relevant command for the thermostat sample
set IOTHUB_COMMAND_PAYLOAD="hello world" # this payload doesn't matter for this sample
```

V terminálu **služby** použijte následující příkaz ke spuštění ukázky:
  
```cmd/sh
python pnp_invoke_command_sample.py
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

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak připojit zařízení IoT technologie Plug and Play k řešení IoT. Další informace o modelech zařízení IoT technologie Plug and Play najdete v tématech:

> [!div class="nextstepaction"]
> [Příručka pro vývojáře pro modelování IoT technologie Plug and Play Preview](concepts-developer-guide.md)
