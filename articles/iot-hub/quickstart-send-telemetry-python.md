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
ms.custom: mvc
ms.date: 02/28/2019
ms.openlocfilehash: a08719d322f044bbf1ced8103af5e4e23ed948c9
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2019
ms.locfileid: "70998489"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-python"></a>Rychlý start: Odeslání telemetrie ze zařízení do služby IoT Hub a jejich čtení pomocí back-endové aplikace (Python)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub je služba Azure, která umožňuje ingestovat velké objemy telemetrických dat ze zařízení IoT do cloudu pro účely uložení nebo zpracování. V tomto rychlém startu odešlete telemetrická data z aplikace simulovaného zařízení prostřednictvím služby IoT Hub do back-endové aplikace za účelem zpracování.

V tomto rychlém startu se k odesílání telemetrických dat používá předem napsaná aplikace Python a ke čtení telemetrických dat z centra se používá nástroj rozhraní příkazového řádku. Před spuštěním těchto dvou aplikací vytvoříte centrum IoT a zaregistrujete v tomto centru zařízení.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Spuštěním následujícího příkazu přidejte do instance služby Cloud Shell Microsoft Azure rozšíření IoT pro rozhraní příkazového řádku Azure. Rozšíření IOT přidá do Azure CLI příkazy určené pro služby IoT Hub, IoT Edge a IoT Device Provisioning Service (DPS).

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Stáhněte si ukázkový projekt Python z https://github.com/Azure-Samples/azure-iot-samples-python/archive/master.zip a extrahujte archiv ZIP.

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrování zařízení

Zařízení musí být zaregistrované ve vašem centru IoT, aby se mohlo připojit. V tomto rychlém startu zaregistrujete simulované zařízení pomocí služby Azure Cloud Shell.

1. Spuštěním následujícího příkazu v Azure Cloud Shell vytvořte identitu zařízení.

    **YourIoTHubName**: Nahraďte tento zástupný symbol níže názvem, který zvolíte pro Centrum IoT.

    **MyPythonDevice**: Toto je název zadaný pro registrované zařízení. Použijte uvedený název MyPythonDevice. Pokud si zvolíte jiný název zařízení, budete ho muset používat v průběhu celého článku a aktualizovat název zařízení v ukázkových aplikacích, než je spustíte.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyPythonDevice
    ```

1. Spuštěním následujících příkazů v Azure Cloud Shell Získejte _připojovací řetězec zařízení_ pro zařízení, které jste zaregistrovali:

    **YourIoTHubName**: Nahraďte tento zástupný symbol níže názvem, který zvolíte pro Centrum IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyPythonDevice --output table
    ```

    Poznamenejte si připojovací řetězec zařízení, který vypadá nějak takto:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Tuto hodnotu použijete později v tomto rychlém startu.

## <a name="send-simulated-telemetry"></a>Odesílání simulovaných telemetrických dat

Aplikace simulovaného zařízení se připojí ke koncovému bodu vašeho centra IoT pro konkrétní zařízení a odesílá simulovaná telemetrická data o teplotě a vlhkosti vzduchu.

1. V okně místního terminálu přejděte do kořenové složky ukázkového projektu Python. Pak přejděte do složky **iot-hub\Quickstarts\simulated-device**.

1. V libovolném textovém editoru otevřete soubor **SimulatedDevice.py**.

    Hodnotu proměnné `CONNECTION_STRING` nahraďte připojovacím řetězcem zařízení, který jste si předtím poznamenali. Změny pak uložte do souboru **SimulatedDevice.py**.

1. V okně místního terminálu pomocí následujících příkazů nainstalujte požadované knihovny pro aplikaci simulovaného zařízení:

    ```cmd/sh
    pip install azure-iot-device
    ```

1. Spuštěním následujících příkazů v okně místního terminálu spusťte aplikaci simulovaného zařízení:

    ```cmd/sh
    python SimulatedDevice.py
    ```

    Následující snímek obrazovky ukazuje výstup, zatímco aplikace simulovaného zařízení odesílá telemetrická data do vašeho centra IoT:

    ![Spuštění simulovaného zařízení](media/quickstart-send-telemetry-python/SimulatedDevice.png)


## <a name="read-the-telemetry-from-your-hub"></a>Čtení telemetrických dat z centra

Rozšíření IoT Hub CLI se může ve vaší službě IoT Hub připojit ke koncovému bodu **Události** na straně služby. Toto rozšíření přijímá zprávy ze zařízení do cloudu odesílané z vašeho simulovaného zařízení. Back-endová aplikace služby IoT Hub se obvykle spouští v cloudu, aby mohla přijímat a zpracovávat zprávy typu zařízení-cloud.

Ve službě Azure Cloud Shell spusťte následující příkazy a položku `YourIoTHubName` nahraďte názvem centra IoT:

```azurecli-interactive
az iot hub monitor-events --hub-name YourIoTHubName --device-id MyPythonDevice 
```

Následující snímek obrazovky ukazuje výstup, když rozšíření přijímá telemetrická data odesílaná simulovaným zařízením do centra:

![Spuštění back-endové aplikace](media/quickstart-send-telemetry-python/ReadDeviceToCloud.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste nastavili centrum IoT, zaregistrovali zařízení, odeslali simulovaná telemetrická data do centra pomocí aplikace Python a přečetli telemetrická data z centra pomocí jednoduché back-endové aplikace.

Informace o tom, jak řídit simulované zařízení z back-endové aplikace, najdete v dalším rychlém startu.

> [!div class="nextstepaction"]
> [Rychlé zprovoznění: Řízení zařízení připojeného k centru IoT](quickstart-control-device-python.md)
