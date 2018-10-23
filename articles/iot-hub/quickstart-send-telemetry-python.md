---
title: Rychlý start k odesílání telemetrických dat do služby Azure IoT Hub (Python) | Microsoft Docs
description: V tomto rychlém startu spustíte ukázkovou aplikaci Python, která odesílá simulovaná telemetrická data do centra IoT a pomocí nástroje čte telemetrické údaje z tohoto centra IoT.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/07/2018
ms.author: dobett
ms.openlocfilehash: 35b9f07b0aa8ee50b4f0f6500f86ea7c6eed4823
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49362037"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-the-telemetry-from-the-hub-with-a-back-end-application-python"></a>Rychlý start: Odesílání telemetrických dat ze zařízení do centra IoT a čtení telemetrických dat z centra pomocí back-endové aplikace (Python)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub je služba Azure, která umožňuje ingestovat velké objemy telemetrických dat ze zařízení IoT do cloudu pro účely uložení nebo zpracování. V tomto rychlém startu odešlete telemetrická data z aplikace simulovaného zařízení prostřednictvím služby IoT Hub do back-endové aplikace za účelem zpracování.

V tomto rychlém startu se k odesílání telemetrických dat používá předem napsaná aplikace Python a ke čtení telemetrických dat z centra se používá nástroj rozhraní příkazového řádku. Před spuštěním těchto dvou aplikací vytvoříte centrum IoT a zaregistrujete v tomto centru zařízení.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Dvě ukázkové aplikace, které spustíte v tomto rychlém startu, jsou napsány pomocí Pythonu. Na vývojovém počítači musíte mít Python 2.7.x nebo 3.5.x.

Python pro různé platformy si můžete stáhnout z webu [Python.org](https://www.python.org/downloads/). To, který instalační program Pythonu zvolíte, by mělo vycházet z architektury systému, ve kterém pracujete. Pokud má váš systém 32bitovou architekturu CPU, stáhněte verzi x86, což je výchozí instalační program na webu Python.org. V případě 64bitové architektury je potřeba stáhnout instalační program x86-64.

Aktuální verzi Pythonu na počítači používaném pro vývoj můžete ověřit pomocí jednoho z následujících příkazů:

```python
python --version
```

```python
python3 --version
```

Stáhněte si ukázkový projekt Python z https://github.com/Azure-Samples/azure-iot-samples-python/archive/master.zip a extrahujte archiv ZIP.

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-quickstarts-create-hub](../../includes/iot-hub-quickstarts-create-hub.md)]

## <a name="register-a-device"></a>Registrování zařízení

Zařízení musí být zaregistrované ve vašem centru IoT, aby se mohlo připojit. V tomto rychlém startu zaregistrujete simulované zařízení pomocí prostředí Azure Cloud Shell.

1. V prostředí Azure Cloud Shell spusťte následující příkazy pro přidání rozšíření IoT Hub CLI a vytvoření identity zařízení. 

    **YourIoTHubName:** Tento zástupný text nahraďte názvem, který si zvolíte pro své centrum IoT.

    **MyPythonDevice:** Toto je název přidělený zaregistrovanému zařízení. Použijte uvedený název MyPythonDevice. Pokud si zvolíte jiný název zařízení, budete ho muset používat v průběhu celého článku a aktualizovat název zařízení v ukázkových aplikacích, než je spustíte.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyPythonDevice
    ```

1. Spuštěním následujícího příkazu v Azure Cloud Shellu získejte _připojovací řetězec zařízení_ pro zařízení, které jste právě zaregistrovali:

    **YourIoTHubName:** Tento zástupný text nahraďte názvem, který si zvolíte pro své centrum IoT.

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
    pip install azure-iothub-device-client
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
az iot hub monitor-events --device-id MyPythonDevice --hub-name YourIoTHubName
```

Následující snímek obrazovky ukazuje výstup, když rozšíření přijímá telemetrická data odesílaná simulovaným zařízením do centra:

![Spuštění back-endové aplikace](media/quickstart-send-telemetry-python/ReadDeviceToCloud.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nastavili centrum IoT, zaregistrovali zařízení, odeslali simulovaná telemetrická data do centra pomocí aplikace Python a přečetli telemetrická data z centra pomocí jednoduché back-endové aplikace.

Informace o tom, jak řídit simulované zařízení z back-endové aplikace, najdete v dalším rychlém startu.

> [!div class="nextstepaction"]
> [Rychlý start: Řízení zařízení připojeného k centru IoT](quickstart-control-device-python.md)