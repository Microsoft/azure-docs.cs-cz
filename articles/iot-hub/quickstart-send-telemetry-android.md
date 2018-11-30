---
title: Odesílání telemetrie k rychlému startu pro Azure IoT Hub (Android) | Dokumentace Microsoftu
description: V tomto rychlém startu spustíte ukázku s Androidem aplikace odesílá Simulovaná telemetrická data do služby IoT hub a čtení telemetrických dat ze služby IoT hub pro zpracování v cloudu.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom: mvc
ms.date: 11/05/2018
ms.author: wesmc
ms.openlocfilehash: 66c1380070c9f9732369cb0d209e428525d53ce8
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2018
ms.locfileid: "52427903"
---
# <a name="quickstart-send-iot-telemetry-from-an-android-device"></a>Rychlý start: Odeslání IoT telemetrická data ze zařízení s Androidem

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub je služba Azure, která umožňuje ingestovat velké objemy telemetrických dat ze zařízení IoT do cloudu pro účely uložení nebo zpracování. V tomto rychlém startu můžete odesílat telemetrická data do služby IoT Hub z aplikace pro Android běží na fyzických nebo simulovaných zařízení.

Rychlý start používá předem napsaná aplikace systému Android odesílat telemetrická data. Telemetrická data budou číst ze služby IoT Hub pomocí Azure Cloud Shell. Před spuštěním aplikace vytvoříte službu IoT hub a registraci zařízení do centra.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

* Android studio z https://developer.android.com/studio/. Další informace týkající se instalace sady Android Studio najdete v tématu [android instalace](https://developer.android.com/studio/install). 

* 27. Android SDK se používá v rámci ukázky v tomto článku. 

* [Ukázkové aplikace pro Android](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/device/AndroidSample) spustíte v tomto rychlém startu je součástí sady azure-iot-samples-java úložišti na Githubu. Stáhněte nebo naklonujte [azure-iot-samples-java](https://github.com/Azure-Samples/azure-iot-samples-java) úložiště.



## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrování zařízení

Zařízení musí být zaregistrované ve vašem centru IoT, aby se mohlo připojit. V tomto rychlém startu zaregistrujete simulované zařízení pomocí služby Azure Cloud Shell.

1. Ve službě Azure Cloud Shell spusťte následující příkazy pro přidání rozšíření rozhraní příkazového řádku IoT Hub a vytvoření identity zařízení. 

   **YourIoTHubName**: Tento zástupný text pod nahraďte název, který zvolíte pro službu IoT hub.

   **MyAndroidDevice**: MyAndroidDevice je název pro registrovaná zařízení. Použijte MyAndroidDevice, jak je znázorněno. Pokud si zvolíte jiný název zařízení, budete ho muset používat v průběhu celého článku a aktualizovat název zařízení v ukázkových aplikacích, než je spustíte.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyAndroidDevice
    ```

2. Spuštěním následujícího příkazu ve službě Azure Cloud Shell získejte _připojovací řetězec zařízení_ pro zařízení, které jste právě zaregistrovali:

    **YourIoTHubName**: Tento zástupný text pod nahraďte název, který zvolíte pro službu IoT hub.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyAndroidDevice --output table
    ```

    Poznamenejte si připojovací řetězec zařízení, který vypadá nějak takto:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}`

    Tuto hodnotu použijete později v tomto rychlém startu k odesílání telemetrie.

## <a name="send-telemetry"></a>Odeslání telemetrie

1. Otevřete ukázkový projekt Android githubu v nástroji Android Studio. Projekt se nachází v následujícím adresáři klonované nebo stažených kopie [azure-iot ukázka java](https://github.com/Azure-Samples/azure-iot-samples-java) úložiště.

        \azure-iot-samples-java\iot-hub\Samples\device\AndroidSample

2. V nástroji Android Studio, otevřete *gradle.properties* pro ukázkového projektu a nahraďte **Device_Connection_String** zástupného symbolu připojovacím řetězcem zařízení jste si předtím poznamenali.

    ```
    DeviceConnectionString=HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}
    ```

3. V nástroji Android Studio, klikněte na tlačítko **souboru** > **synchronizovat projekt se soubory Gradle**. Ověřte, že dokončení sestavení.

4. Po dokončení sestavení kliknutím na **spustit** > **spuštění "aplikace"**. Konfigurace aplikace pro spuštění na fyzické zařízení s Androidem nebo emulátoru Androidu. Další informace o spouštění aplikace pro Android na fyzické zařízení nebo emulátoru najdete v tématu [spuštění aplikace](https://developer.android.com/training/basics/firstapp/running-app).

5. Jakmile aplikace načte, klikněte na tlačítko **Start** tlačítko Zahájit odesílání telemetrických dat do služby IoT Hub:

    ![Aplikace](media/quickstart-send-telemetry-android/sample-screenshot.png)


## <a name="read-the-telemetry-from-your-hub"></a>Čtení telemetrických dat z centra

V této části budete používat Azure Cloud Shell s [rozšíření IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) k monitorování zařízení zprávy, které jsou odesílány zařízení s Androidem.

1. Pomocí služby Azure Cloud Shell spusťte následující příkaz, který provede připojení a čtení zpráv z centra IoT:

   **YourIoTHubName**: Tento zástupný text pod nahraďte název, který zvolíte pro službu IoT hub.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name YourIoTHubName --output table
    ```
    Na následujícím snímku obrazovky se zobrazí výstup jako IoT hub přijímá telemetrická data odesílaná zařízení s Androidem:

      ![Čtení zpráv zařízení pomocí Azure CLI](media/quickstart-send-telemetry-android/read-data.png)


## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste nastavení IoT hub, zaregistrované zařízení, odeslali Simulovaná telemetrická data do centra pomocí aplikace pro Android a čtení telemetrických dat z centra pomocí Azure Cloud Shell.

Informace o tom, jak řídit simulované zařízení z back-endové aplikace, najdete v dalším rychlém startu.

> [!div class="nextstepaction"]
> [Rychlý start: Řízení zařízení připojeného k centru IoT](quickstart-control-device-android.md)

