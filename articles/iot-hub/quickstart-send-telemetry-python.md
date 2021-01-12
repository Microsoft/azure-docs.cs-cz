---
title: Rychlý start k odesílání telemetrických dat do služby Azure IoT Hub (Python) | Microsoft Docs
description: V tomto rychlém startu spustíte ukázkovou aplikaci Python, která odesílá simulovaná telemetrická data do centra IoT a pomocí nástroje čte telemetrické údaje z tohoto centra IoT.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: quickstart
ms.custom:
- mvc
- mqtt
- devx-track-python
- 'Role: Cloud Development'
- devx-track-azurecli
ms.date: 06/16/2020
ms.openlocfilehash: 71d91ceba48158d2abc688af572dabfc258a2e94
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2021
ms.locfileid: "98121428"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-python"></a>Rychlý Start: odeslání telemetrie ze zařízení do služby IoT Hub a její čtení pomocí back-endové aplikace (Python)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

V tomto rychlém startu odešlete telemetrii z aplikace simulovaného zařízení prostřednictvím Azure IoT Hub do back-endové aplikace ke zpracování. IoT Hub je služba Azure, která umožňuje ingestovat velké objemy telemetrických dat ze zařízení IoT do cloudu pro účely uložení nebo zpracování. Tento rychlý Start používá dvě předem napsané aplikace Pythonu: jeden pro odeslání telemetrie a jednu pro přečtení telemetrie z centra. Před spuštěním těchto dvou aplikací vytvoříte centrum IoT a zaregistrujete v tomto centru zařízení.

## <a name="prerequisites"></a>Požadavky

* Účet Azure s aktivním předplatným. [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Python 3.7 +](https://www.python.org/downloads/). Další podporované verze Pythonu najdete v tématu [funkce zařízení Azure IoT](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device#azure-iot-device-features).

* [Vzorový projekt v Pythonu](https://github.com/Azure-Samples/azure-iot-samples-python/archive/master.zip)

* Port 8883 otevřete v bráně firewall. Ukázka zařízení v tomto rychlém startu používá protokol MQTT, který komunikuje přes port 8883. Tento port může být blokovaný v některých podnikových a vzdělávacích prostředích sítě. Další informace a způsoby, jak tento problém obejít, najdete v tématu [připojení k IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrace zařízení

Zařízení musí být zaregistrované ve vašem centru IoT, aby se mohlo připojit. V tomto rychlém startu zaregistrujete simulované zařízení pomocí služby Azure Cloud Shell.

1. Spuštěním následujícího příkazu v Azure Cloud Shell vytvořte identitu zařízení.

    **YourIoTHubName**: níže uvedený zástupný symbol nahraďte názvem, který jste zvolili pro Centrum IoT.

    **MyPythonDevice**: Toto je název zařízení, které registrujete. Doporučuje se používat **MyPythonDevice** , jak je znázorněno na obrázku. Pokud pro vaše zařízení zvolíte jiný název, budete ho muset použít i v celém rámci tohoto článku a před jeho spuštěním aktualizovat název zařízení v ukázkových aplikacích.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyPythonDevice
    ```

1. Spuštěním následujícího příkazu v Azure Cloud Shell Získejte _připojovací řetězec zařízení_ pro zařízení, které jste zaregistrovali:

    **YourIoTHubName**: níže uvedený zástupný symbol nahraďte názvem, který jste zvolili pro Centrum IoT.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyPythonDevice --output table
    ```

    Poznamenejte si připojovací řetězec zařízení, který vypadá takto:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyPythonDevice;SharedAccessKey={YourSharedAccessKey}`

    Tuto hodnotu použijete později v rychlém startu.

1. K povolení back-endové aplikace pro připojení ke službě IoT Hub a k načtení zpráv budete potřebovat taky _koncový bod kompatibilní s Event Hubs_, _cestu kompatibilní s Event Hubs_ a _primární klíč služby_ z vašeho centra IoT. Následující příkazy načtou tyto hodnoty pro centrum IoT:

   **YourIoTHubName**: níže uvedený zástupný symbol nahraďte názvem, který zvolíte pro Centrum IoT.

    ```azurecli-interactive
    az iot hub show --query properties.eventHubEndpoints.events.endpoint --name {YourIoTHubName}

    az iot hub show --query properties.eventHubEndpoints.events.path --name {YourIoTHubName}

    az iot hub policy show --name service --query primaryKey --hub-name {YourIoTHubName}
    ```

    Poznamenejte si tyto tři hodnoty, které použijete později v rychlém startu.

## <a name="send-simulated-telemetry"></a>Odesílání simulovaných telemetrických dat

Aplikace simulovaného zařízení se připojuje ke koncovému bodu vašeho centra IoT pro konkrétní zařízení a odesílá simulovaná telemetrická data o teplotě a vlhkosti vzduchu.

1. V okně místního terminálu přejděte do kořenové složky ukázkového projektu Python. Pak přejděte do složky **iot-hub\Quickstarts\simulated-device**.

1. V libovolném textovém editoru otevřete soubor **SimulatedDevice.py**.

    Nahraďte hodnotu `CONNECTION_STRING` proměnné připojovacím řetězcem zařízení, který jste si poznamenali dříve. Pak změny uložte do **SimulatedDevice.py**.

1. V okně místního terminálu pomocí následujících příkazů nainstalujte požadované knihovny pro aplikaci simulovaného zařízení:

    ```cmd/sh
    pip install azure-iot-device
    ```

1. Spuštěním následujících příkazů v okně místního terminálu spusťte aplikaci simulovaného zařízení:

    ```cmd/sh
    python SimulatedDevice.py
    ```

    Následující snímek obrazovky ukazuje výstup, zatímco aplikace simulovaného zařízení odesílá telemetrická data do vašeho centra IoT:

    ![Spuštění simulovaného zařízení](media/quickstart-send-telemetry-python/simulated-device.png)

## <a name="read-the-telemetry-from-your-hub"></a>Čtení telemetrie z centra

Back-endová aplikace se připojí ke koncovému bodu **Events** na straně služby ve vašem centru IoT. Aplikace přijímá zprávy typu zařízení-cloud odeslané ze simulovaného zařízení. Back-endová aplikace služby IoT Hub se obvykle spouští v cloudu, aby mohla přijímat a zpracovávat zprávy typu zařízení-cloud.

> [!NOTE]
> Následující kroky používají synchronní ukázku **read_device_to_cloud_messages_sync. py**. Můžete provést stejný postup s asynchronní ukázkou, **read_device_to_cloud_messages_async. py**.

1. V jiném okně místního terminálu přejděte do kořenové složky ukázkového projektu Python. Potom přejděte ke složce **iot-hub\Quickstarts\read-d2c-messages**.

2. Otevřete soubor **read_device_to_cloud_messages_sync. py** v textovém editoru dle vašeho výběru. Aktualizujte následující proměnné a uložte provedené změny souboru.

    | Proměnná | Hodnota |
    | -------- | ----------- |
    | `EVENTHUB_COMPATIBLE_ENDPOINT` | Nahraďte hodnotu proměnné pomocí koncového bodu kompatibilního s Event Hubs, který jste si poznamenali dříve. |
    | `EVENTHUB_COMPATIBLE_PATH`     | Nahraďte hodnotu proměnné cestou kompatibilní s Event Hubs, kterou jste si poznamenali dříve. |
    | `IOTHUB_SAS_KEY`                | Nahraďte hodnotu proměnné primárním klíčem služby, který jste si poznamenali dříve. |

3. V okně místního terminálu pomocí následujících příkazů nainstalujte požadované knihovny pro back-endovou aplikaci:

    ```cmd/sh
    pip install azure-eventhub
    ```

4. Spuštěním následujících příkazů v okně místního terminálu sestavte a spusťte back-endovou aplikaci:

    ```cmd/sh
    python read_device_to_cloud_messages_sync.py
    ```

    Následující snímek obrazovky ukazuje výstup, zatímco back-endová aplikace přijímá telemetrická data odeslaná simulovaným zařízením do centra:

    ![Spuštění back-endové aplikace](media/quickstart-send-telemetry-python/read-device-to-cloud.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu nastavíte centrum IoT, zaregistrovali zařízení, poslali simulovanou telemetrii do centra pomocí aplikace v Pythonu a načetli telemetrii z centra pomocí jednoduché back-endové aplikace.

Informace o tom, jak řídit simulované zařízení z back-endové aplikace, najdete v dalším rychlém startu.

> [!div class="nextstepaction"]
> [Rychlý start: Řízení zařízení připojeného k centru IoT](quickstart-control-device-python.md)
