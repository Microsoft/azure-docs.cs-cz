---
title: Rychlý start k řízení zařízení ze služby Azure IoT Hub (Python) | Microsoft Docs
description: V tomto rychlém startu spustíte dvě ukázkové aplikace Python. První aplikace je back-endová aplikace, která může vzdáleně řídit zařízení připojená k vašemu centru. Druhá aplikace simuluje zařízení připojené k vašemu centru, které je možné řídit vzdáleně.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/26/2019
ms.openlocfilehash: 74146242ef4084e5990027117689bb1371c3f272
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2019
ms.locfileid: "65832196"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-python"></a>Rychlý start: Řízení zařízení připojená ke službě IoT hub (Python)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

IoT Hub je služba Azure, která umožňuje ingestovat velké objemy telemetrických dat ze zařízení IoT do cloudu a spravovat zařízení z cloudu. V tomto rychlém startu použijete *přímou metodu* k řízení simulovaného zařízení připojeného k centru IoT. Přímé metody můžete použít k provádění vzdálených změn chování zařízení připojeného k centru IoT.

Rychlý start používá dvě předem napsané aplikace Python:

* Aplikaci simulovaného zařízení, která odpovídá na přímé metody volané z back-endové aplikace. Aby bylo možné přijímat volání přímé metody, připojí se tato aplikace ke koncovému bodu centra IoT pro konkrétní zařízení.

* Back-endovou aplikaci, která na simulovaném zařízení volá přímé metody. Aby na zařízení bylo možné volat přímou metodu, připojí se tato aplikace ke koncovému bodu vašeho centra IoT na straně služby.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Dvě ukázkové aplikace, které spustíte v tomto rychlém startu, jsou napsány pomocí Pythonu. Microsoft Azure IoT SDK pro Python v současné době podporují pouze konkrétní verze jazyka Python pro každou platformu. Další informace najdete v tématu [Python SDK – soubor Readme](https://github.com/Azure/azure-iot-sdk-python#important-installation-notes---dealing-with-importerror-issues).

V tomto rychlém startu se předpokládá, že používáte vývojový počítač s Windows. Pro systémy Windows, pouze [Python 3.6.x](https://www.python.org/downloads/release/python-368/) je podporována. To, který instalační program Pythonu zvolíte, by mělo vycházet z architektury systému, ve kterém pracujete. Pokud je váš systém architekturu procesoru, 32 bitů, potom instalační program stáhnout x86; pro 64bitovou architekturu stáhněte si instalační program x86 – x 64. Dále ověřte, jestli [Microsoft Visual C++ Redistributable pro Visual Studio 2015 – 2017 a 2019](https://support.microsoft.com/en-us/help/2977003/the-latest-supported-visual-c-downloads) pro architektury (x86 nebo x64) je nainstalována.

Python si můžete stáhnout pro jiné platformy z [Python.org](https://www.python.org/downloads/).

Aktuální verzi Pythonu na počítači používaném pro vývoj můžete ověřit pomocí jednoho z následujících příkazů:

```python
python --version
```

```python
python3 --version
```

Spusťte následující příkaz pro přidání rozšíření Microsoft Azure IoT pro Azure CLI do instance služby Cloud Shell. Rozšíření IOT přidá služby IoT Hub, IoT Edge a IoT zařízení zřizování služby (DPS) konkrétní příkazy rozhraní příkazového řádku Azure.

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Pokud jste to ještě neudělali, stáhněte si ukázkový projekt Python z webu https://github.com/Azure-Samples/azure-iot-samples-python/archive/master.zip a extrahujte archiv ZIP.

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

Pokud jste dokončili předchozí [rychlý start: Odesílání telemetrických dat ze zařízení do služby IoT hub](quickstart-send-telemetry-python.md), můžete tento krok přeskočit.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrování zařízení

Pokud jste dokončili předchozí [rychlý start: Odesílání telemetrických dat ze zařízení do služby IoT hub](quickstart-send-telemetry-python.md), můžete tento krok přeskočit.

Zařízení musí být zaregistrované ve vašem centru IoT, aby se mohlo připojit. V tomto rychlém startu zaregistrujete simulované zařízení pomocí služby Azure Cloud Shell.

1. Spusťte následující příkaz v Azure Cloud Shell vytvořte identitu zařízení.

    **YourIoTHubName** : Nahraďte tento zástupný text pod názvem, který jste zvolili pro službu IoT hub.

    **MyPythonDevice** : Toto je název pro registrovaná zařízení. Použijte uvedený název MyPythonDevice. Pokud si zvolíte jiný název zařízení, budete ho muset používat v průběhu celého článku a aktualizovat název zařízení v ukázkových aplikacích, než je spustíte.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyPythonDevice
    ```

2. Spuštěním následujícího příkazu ve službě Azure Cloud Shell získejte _připojovací řetězec zařízení_ pro zařízení, které jste právě zaregistrovali:

    **YourIoTHubName** : Nahraďte tento zástupný text pod názvem, který jste zvolili pro službu IoT hub.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyPythonDevice --output table
    ```

    Poznamenejte si připojovací řetězec zařízení, který vypadá nějak takto:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Tuto hodnotu použijete později v tomto rychlém startu.

3. Potřebujete také _připojovací řetězec služby_, který back-endové aplikaci umožní připojení k vašemu centru IoT a načtení zpráv. Následující příkaz načte připojovací řetězec služby pro vaše centrum IoT:

    **YourIoTHubName** : Nahraďte tento zástupný text pod názvem, který jste vybrali pro službu IoT hub.

    ```azurecli-interactive
    az iot hub show-connection-string \
      --name YourIoTHubName \
      --output table
    ```

    Poznamenejte si připojovací řetězec služby, který vypadá nějak takto:

   `HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={YourSharedAccessKey}`

    Tuto hodnotu použijete později v tomto rychlém startu. Připojovací řetězec služby se liší od připojovacího řetězce zařízení.

## <a name="listen-for-direct-method-calls"></a>Naslouchání voláním přímé metody

Aplikace simulovaného zařízení se připojí ke koncovému bodu v centru IoT pro konkrétní zařízení, odešle simulovaná telemetrická data a z vašeho centra naslouchá voláním přímé metody. Volání přímé metody z centra v tomto rychlém startu nařídí zařízení, aby změnilo interval, ve kterém se odesílají telemetrická data. Simulované zařízení odešle po spuštění přímé metody zpět do centra potvrzení.

1. V okně místního terminálu přejděte do kořenové složky ukázkového projektu Python. Potom přejděte do složky **iot-hub\Quickstarts\simulated-device-2**.

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

    ![Spuštění simulovaného zařízení](./media/quickstart-control-device-python/SimulatedDevice-1.png)

## <a name="call-the-direct-method"></a>Volání přímé metody

Back-endová aplikace se připojí ke koncovému bodu vašeho centra IoT na straně služby. Aplikace provádí volání přímé metody na zařízení prostřednictvím centra IoT a čeká na potvrzení. Back-endová aplikace služby IoT Hub se obvykle spouští v cloudu.

1. V jiném okně místního terminálu přejděte do kořenové složky ukázkového projektu Python. Potom přejděte do složky **iot-hub\Quickstarts\back-end-application**.

1. V libovolném textovém editoru otevřete soubor **BackEndApplication.py**.

    Hodnotu proměnné `CONNECTION_STRING` nahraďte připojovacím řetězcem služby, který jste si předtím poznamenali. Změny pak uložte do souboru **BackEndApplication.py**.

1. V okně místního terminálu pomocí následujících příkazů nainstalujte požadované knihovny pro aplikaci simulovaného zařízení:

    ```cmd/sh
    pip install azure-iothub-service-client future
    ```

1. Spuštěním následujících příkazů v okně místního terminálu spusťte back-endovou aplikaci:

    ```cmd/sh
    python BackEndApplication.py
    ```

    Následující snímek obrazovky ukazuje výstup, zatímco aplikace provádí volání přímé metody na zařízení a obdrží potvrzení:

    ![Spuštění back-endové aplikace](./media/quickstart-control-device-python/BackEndApplication.png)

    Po spuštění back-endové aplikace se v okně konzoly se simulovaným zařízením zobrazí zpráva a rychlost odesílání zpráv se změní:

    ![Změna simulovaného klienta](./media/quickstart-control-device-python/SimulatedDevice-2.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste volali přímou metodu na zařízení z back-endové aplikace a odpovídali na volání přímé metody v aplikaci simulovaného zařízení.

Informace o tom, jak směrovat zprávy typu zařízení-cloud do různých cílů v cloudu, najdete v dalším kurzu.

> [!div class="nextstepaction"]
> [Kurz: Telemetrická data trasy pro různé koncové body pro zpracování](tutorial-routing.md)