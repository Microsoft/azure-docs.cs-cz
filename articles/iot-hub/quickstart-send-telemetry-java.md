---
title: 'Rychlý Start: odeslání telemetrie do Azure IoT Hub pomocí Java'
description: V tomto rychlém startu spustíte dvě ukázkové aplikace Java, které odesílají simulovaná telemetrická data do centra IoT a čtou z centra IoT telemetrická data pro účely zpracování v cloudu.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom:
- mvc
- seo-java-august2019
- seo-java-september2019
- mqtt
- devx-track-java
- devx-track-azurecli
ms.date: 05/26/2020
ms.openlocfilehash: 76326d0b68a3ff71bf95c09147d003e769e103db
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844638"
---
# <a name="quickstart-send-telemetry-to-an-azure-iot-hub-and-read-it-with-a-java-application"></a>Rychlý Start: odeslání telemetrie do služby Azure IoT Hub a její čtení pomocí aplikace Java

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

V tomto rychlém startu odešlete telemetrii do Azure IoT Hub a přečtete ji pomocí aplikace Java. IoT Hub je služba Azure, která umožňuje ingestovat velké objemy telemetrických dat ze zařízení IoT do cloudu pro účely uložení nebo zpracování. Tento rychlý Start používá dvě předem napsané aplikace Java: jeden pro odeslání telemetrie a jednu pro přečtení telemetrie z centra. Před spuštěním těchto dvou aplikací vytvoříte centrum IoT a zaregistrujete v tomto centru zařízení.

## <a name="prerequisites"></a>Předpoklady

* Účet Azure s aktivním předplatným. [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* Java SE Development Kit 8. V [dlouhodobé podpoře jazyka Java pro Azure a Azure Stack](/java/azure/jdk/?view=azure-java-stable)v části **Dlouhodobá podpora** vyberte **Java 8**.

    Aktuální verzi Javy na vývojovém počítači můžete ověřit pomocí následujícího příkazu:

    ```cmd/sh
    java -version
    ```

* [Apache Maven 3](https://maven.apache.org/download.cgi).

    Aktuální verzi Mavenu na vývojovém počítači můžete ověřit pomocí následujícího příkazu:

    ```cmd/sh
    mvn --version
    ```

* [Vzorový projekt Java](https://github.com/Azure-Samples/azure-iot-samples-java/archive/master.zip).

* Port 8883 otevřete v bráně firewall. Ukázka zařízení v tomto rychlém startu používá protokol MQTT, který komunikuje přes port 8883. Tento port může být blokovaný v některých podnikových a vzdělávacích prostředích sítě. Další informace a způsoby, jak tento problém obejít, najdete v tématu [připojení k IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrace zařízení

Zařízení musí být zaregistrované ve vašem centru IoT, aby se mohlo připojit. V tomto rychlém startu zaregistrujete simulované zařízení pomocí služby Azure Cloud Shell.

1. Spuštěním následujícího příkazu v Azure Cloud Shell vytvořte identitu zařízení.

   **YourIoTHubName**: níže uvedený zástupný symbol nahraďte názvem, který jste zvolili pro Centrum IoT.

   **MyJavaDevice**: Toto je název zařízení, které registrujete. Doporučuje se používat **MyJavaDevice** , jak je znázorněno na obrázku. Pokud pro vaše zařízení zvolíte jiný název, budete ho muset použít i v celém rámci tohoto článku a před jeho spuštěním aktualizovat název zařízení v ukázkových aplikacích.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyJavaDevice
    ```

2. Spuštěním následujícího příkazu v Azure Cloud Shell Získejte _připojovací řetězec zařízení_ pro zařízení, které jste právě zaregistrovali:

    **YourIoTHubName**: níže uvedený zástupný symbol nahraďte názvem, který jste zvolili pro Centrum IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyJavaDevice --output table
    ```

    Poznamenejte si připojovací řetězec zařízení, který vypadá takto:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyJavaDevice;SharedAccessKey={YourSharedAccessKey}`

    Tuto hodnotu použijete později v rychlém startu.

3. K povolení back-endové aplikace pro připojení ke službě IoT Hub a k načtení zpráv budete potřebovat taky _koncový bod kompatibilní s Event Hubs_, _cestu kompatibilní s Event Hubs_ a _primární klíč služby_ z vašeho centra IoT. Následující příkazy načtou tyto hodnoty pro centrum IoT:

     **YourIoTHubName**: níže uvedený zástupný symbol nahraďte názvem, který jste zvolili pro Centrum IoT.

    ```azurecli-interactive
    az iot hub show --query properties.eventHubEndpoints.events.endpoint --name {YourIoTHubName}

    az iot hub show --query properties.eventHubEndpoints.events.path --name {YourIoTHubName}

    az iot hub policy show --name service --query primaryKey --hub-name {YourIoTHubName}
    ```

    Poznamenejte si tyto tři hodnoty, které použijete později v rychlém startu.

## <a name="send-simulated-telemetry"></a>Odesílání simulovaných telemetrických dat

Aplikace simulovaného zařízení se připojuje ke koncovému bodu vašeho centra IoT pro konkrétní zařízení a odesílá simulovaná telemetrická data o teplotě a vlhkosti vzduchu.

1. V okně místního terminálu přejděte do kořenové složky ukázkového projektu Java. Pak přejděte do složky **iot-hub\Quickstarts\simulated-device**.

2. V libovolném textovém editoru otevřete soubor **src/main/java/com/microsoft/docs/iothub/samples/SimulatedDevice.java**.

    Nahraďte hodnotu `connString` proměnné připojovacím řetězcem zařízení, který jste si poznamenali dříve. Pak změny uložte do **SimulatedDevice. Java**.

3. V okně místního terminálu pomocí následujících příkazů nainstalujte požadované knihovny a sestavte aplikaci simulovaného zařízení:

    ```cmd/sh
    mvn clean package
    ```

4. Spuštěním následujících příkazů v okně místního terminálu spusťte aplikaci simulovaného zařízení:

    ```cmd/sh
    java -jar target/simulated-device-1.0.0-with-deps.jar
    ```

    Následující snímek obrazovky ukazuje výstup, zatímco aplikace simulovaného zařízení odesílá telemetrická data do vašeho centra IoT:

    ![Výstup ze telemetrie odesílané zařízením do služby IoT Hub](media/quickstart-send-telemetry-java/simulated-device.png)

## <a name="read-the-telemetry-from-your-hub"></a>Čtení telemetrie z centra

Back-endová aplikace se připojí ke koncovému bodu **Events** na straně služby ve vašem centru IoT. Aplikace přijímá zprávy typu zařízení-cloud odeslané ze simulovaného zařízení. Back-endová aplikace služby IoT Hub se obvykle spouští v cloudu, aby mohla přijímat a zpracovávat zprávy typu zařízení-cloud.

1. V jiném okně místního terminálu přejděte do kořenové složky ukázkového projektu Java. Potom přejděte ke složce **iot-hub\Quickstarts\read-d2c-messages**.

2. V libovolném textovém editoru otevřete soubor **src/main/java/com/microsoft/docs/iothub/samples/ReadDeviceToCloudMessages.java**. Aktualizujte následující proměnné a uložte provedené změny souboru.

    | Proměnná | Hodnota |
    | -------- | ----------- |
    | `EVENT_HUBS_COMPATIBLE_ENDPOINT` | Nahraďte hodnotu proměnné pomocí koncového bodu kompatibilního s Event Hubs, který jste si poznamenali dříve. |
    | `EVENT_HUBS_COMPATIBLE_PATH`     | Nahraďte hodnotu proměnné cestou kompatibilní s Event Hubs, kterou jste si poznamenali dříve. |
    | `IOT_HUB_SAS_KEY`                | Nahraďte hodnotu proměnné primárním klíčem služby, který jste si poznamenali dříve. |

3. V okně místního terminálu pomocí následujících příkazů nainstalujte požadované knihovny a sestavte back-endovou aplikaci:

    ```cmd/sh
    mvn clean package
    ```

4. Spuštěním následujících příkazů v okně místního terminálu spusťte back-endovou aplikaci:

    ```cmd/sh
    java -jar target/read-d2c-messages-1.0.0-with-deps.jar
    ```

    Následující snímek obrazovky ukazuje výstup, zatímco back-endová aplikace přijímá telemetrická data odeslaná simulovaným zařízením do centra:

    ![Výstupem do back-endové aplikace je příjem telemetrie odeslaných do služby IoT Hub.](media/quickstart-send-telemetry-java/read-device-to-cloud.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu nastavíte centrum IoT, zaregistrovali zařízení, poslali simulovanou telemetrii do centra pomocí aplikace Java a načetli telemetrii z centra pomocí jednoduché back-endové aplikace.

Informace o tom, jak řídit simulované zařízení z back-endové aplikace, najdete v dalším rychlém startu.

> [!div class="nextstepaction"]
> [Rychlý start: Řízení zařízení připojeného k centru IoT](quickstart-control-device-java.md)
