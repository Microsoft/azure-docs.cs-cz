---
title: Rychlý start k odesílání telemetrických dat do služby Azure IoT Hub (Java) | Microsoft Docs
description: V tomto rychlém startu spustíte dvě ukázkové aplikace Java, které odesílají simulovaná telemetrická data do centra IoT a čtou z centra IoT telemetrická data pro účely zpracování v cloudu.
services: iot-hub
author: dominicbetts
manager: timlt
editor: ''
ms.service: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: ns
ms.date: 04/30/2018
ms.author: dobett
ms.openlocfilehash: 36005988611e7ec3f16146919e3ab3f04755e7e5
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-the-telemetry-from-the-hub-with-a-back-end-application-java"></a>Rychlý start: Odesílání telemetrických dat ze zařízení do centra IoT a čtení telemetrických dat z centra pomocí back-endové aplikace (Java)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub je služba Azure, která umožňuje ingestovat velké objemy telemetrických dat ze zařízení IoT do cloudu pro účely uložení nebo zpracování. V tomto rychlém startu odešlete telemetrická data z aplikace simulovaného zařízení prostřednictvím služby IoT Hub do back-endové aplikace za účelem zpracování.

V tomto rychlém startu se používají dvě předem napsané aplikace Java, jedna k odesílání telemetrických dat a jedna ke čtení telemetrických dat z centra. Před spuštěním těchto dvou aplikací vytvoříte centrum IoT a zaregistrujete v tomto centru zařízení.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Dvě ukázkové aplikace, které spustíte v tomto rychlém startu, jsou napsány pomocí Javy. Na vývojovém počítači musíte mít Java SE 8 nebo novější.

Javu pro různé platformy si můžete stáhnout z webu [Oracle](http://www.oracle.com/technetwork/java/javase/downloads/index.html).

Aktuální verzi Javy na vývojovém počítači můžete ověřit pomocí následujícího příkazu:

```cmd/sh
java --version
```

K sestavení ukázek potřebujete nainstalovat Maven 3. Maven pro různé platformy si můžete stáhnout z webu [Apache Maven](https://maven.apache.org/download.cgi).

Aktuální verzi Mavenu na vývojovém počítači můžete ověřit pomocí následujícího příkazu:

```cmd/sh
mvn --version
```

Stáhněte si ukázkový projekt Java z webu https://github.com/Azure-Samples/azure-iot-samples-java/archive/master.zip a extrahujte archiv ZIP.

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-quickstarts-create-hub](../../includes/iot-hub-quickstarts-create-hub.md)]

## <a name="register-a-device"></a>Registrování zařízení

Zařízení musí být zaregistrované ve vašem centru IoT, aby se mohlo připojit. V tomto rychlém startu pomocí Azure CLI zaregistrujete simulované zařízení.

1. Přidejte rozšíření rozhraní příkazového řádku IoT Hub a vytvořte identitu zařízení. `{YourIoTHubName}` nahraďte názvem, který jste vybrali pro centrum IoT:

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyJavaDevice
    ```

    Pokud si zvolíte jiný název zařízení, změňte ho také v ukázkových aplikacích, než je spustíte.

1. Spuštěním následujícího příkazu získejte _připojovací řetězec zařízení_ pro zařízení, které jste právě zaregistrovali:

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyJavaDevice --output table
    ```

    Poznamenejte si připojovací řetězec zařízení, který vypadá nějak takto: `Hostname=...=`. Tuto hodnotu použijete později v tomto rychlém startu.

1. Dále potřebujete _koncový bod kompatibilní s Event Hubs_, _cestu kompatibilní s Event Hubs_ a _primární klíč vlastníka centra IoT_ z centra IoT, abyste umožnili back-endové aplikaci připojit se k centru IoT a načíst zprávy. Následující příkazy načtou tyto hodnoty pro centrum IoT:

    ```azurecli-interactive
    az iot hub show --query properties.eventHubEndpoints.events.endpoint --name {YourIoTHubName}

    az iot hub show --query properties.eventHubEndpoints.events.path --name {YourIoTHubName}

    az iot hub policy show --name iothubowner --query primaryKey --hub-name {your IoT Hub name}
    ```

    Tyto tři hodnoty si poznamenejte, použijete je později v rychlém startu.

## <a name="send-simulated-telemetry"></a>Odesílání simulovaných telemetrických dat

Aplikace simulovaného zařízení se připojí ke koncovému bodu vašeho centra IoT pro konkrétní zařízení a odesílá simulovaná telemetrická data o teplotě a vlhkosti vzduchu.

1. V okně terminálu přejděte do kořenové složky ukázkového projektu Java. Pak přejděte do složky **Quickstarts\simulated-device**.

1. V libovolném textovém editoru otevřete soubor **src/main/java/com/microsoft/docs/iothub/samples/SimulatedDevice.java**.

    Hodnotu proměnné `connString` nahraďte připojovacím řetězcem zařízení, který jste si předtím poznamenali. Změny pak uložte do souboru **SimulatedDevice.java**.

1. V okně terminálu spusťte následující příkazy pro instalaci požadovaných knihoven a sestavení aplikace simulovaného zařízení:

    ```cmd/sh
    mvn clean package
    ```

1. V okně terminálu spusťte následující příkazy pro spuštění aplikace simulovaného zařízení:

    ```cmd/sh
    java -jar target/simulated-device-1.0.0-with-deps.jar
    ```

    Následující snímek obrazovky ukazuje výstup, zatímco aplikace simulovaného zařízení odesílá telemetrická data do vašeho centra IoT:

    ![Spuštění simulovaného zařízení](media/quickstart-send-telemetry-java/SimulatedDevice.png)

## <a name="read-the-telemetry-from-your-hub"></a>Čtení telemetrických dat z centra

Back-endová aplikace se připojí ke koncovému bodu **Events** na straně služby ve vašem centru IoT. Aplikace přijímá zprávy typu zařízení-cloud odeslané ze simulovaného zařízení. Back-endová aplikace služby IoT Hub se obvykle spouští v cloudu, aby mohla přijímat a zpracovávat zprávy typu zařízení-cloud.

1. V jiném okně terminálu přejděte do kořenové složky ukázkového projektu Java. Potom přejděte do složky **Quickstarts\read-d2c-messages**.

1. V libovolném textovém editoru otevřete soubor **src/main/java/com/microsoft/docs/iothub/samples/ReadDeviceToCloudMessages.java**.

    Hodnotu proměnné `eventHubsCompatibleEndpoint` nahraďte koncovým bodem kompatibilním s Event Hubs, který jste si předtím poznamenali.

    Hodnotu proměnné `eventHubsCompatiblePath` nahraďte cestou kompatibilní s Event Hubs, kterou jste si předtím poznamenali.

    Hodnotu proměnné `iotHubSasKey` nahraďte primárním klíčem vlastníka centra IoT, který jste si předtím poznamenali. Změny potom uložte do souboru **ReadDeviceToCloudMessages.java**.

1. V okně terminálu spusťte následující příkazy pro instalaci požadovaných knihoven a sestavení back-endové aplikace:

    ```cmd/sh
    mvn clean package
    ```

1. V okně terminálu spusťte následující příkazy pro spuštění back-endové aplikace:

    ```cmd/sh
    java -jar target/read-d2c-messages-1.0.0-with-deps.jar
    ```

    Následující snímek obrazovky ukazuje výstup, zatímco back-endová aplikace přijímá telemetrická data odeslaná simulovaným zařízením do centra:

    ![Spuštění back-endové aplikace](media/quickstart-send-telemetry-java/ReadDeviceToCloud.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud si chcete projít další rychlý start, zachovejte skupinu prostředků a centrum IoT pro pozdější použití.

Pokud už centrum IoT nepotřebujete, odstraňte ho společně se skupinou prostředků na portálu. Provedete to výběrem skupiny prostředků **qs-iot-hub-rg**, která obsahuje vaše centrum IoT, a kliknutím na **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nastavili centrum IoT, zaregistrovali zařízení, odeslali simulovaná telemetrická data do centra pomocí aplikace Java a přečetli telemetrická data z centra pomocí jednoduché back-endové aplikace.

Informace o tom, jak řídit simulované zařízení z back-endové aplikace, najdete v dalším rychlém startu.

> [!div class="nextstepaction"]
> [Rychlý start: Řízení zařízení připojeného k centru IoT](quickstart-control-device-java.md)