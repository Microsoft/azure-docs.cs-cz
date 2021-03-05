---
title: Rychlý Start – odeslání telemetrie do služby Azure IoT Hub rychlý Start (Android) | Microsoft Docs
description: V tomto rychlém startu spustíte ukázkovou aplikaci pro Android, která bude odesílat simulovanou telemetrii do služby IoT Hub a číst telemetrii ze služby IoT Hub ke zpracování v cloudu.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom:
- mvc
- mqtt
- devx-track-java
- devx-track-azurecli
ms.date: 03/15/2019
ms.author: wesmc
ms.openlocfilehash: e1e44f5aa4250d63c2addafd2661b09bae2b38e3
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102199913"
---
# <a name="quickstart-send-iot-telemetry-from-an-android-device"></a>Rychlý Start: odeslání telemetrie IoT ze zařízení s Androidem

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

V tomto rychlém startu odešlete telemetrii do Azure IoT Hub z aplikace pro Android běžící na fyzickém nebo simulovaném zařízení. IoT Hub je služba Azure, která umožňuje ingestovat velké objemy telemetrických dat ze zařízení IoT do cloudu pro účely uložení nebo zpracování. V tomto rychlém startu se k posílání telemetrie používá předem napsaná aplikace pro Android. Telemetrii se načte z IoT Hub pomocí Azure Cloud Shell. Před spuštěním aplikace vytvoříte centrum IoT a zaregistrujete zařízení do centra.

## <a name="prerequisites"></a>Požadavky

* Účet Azure s aktivním předplatným. [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Android Studio s Android SDK 27](https://developer.android.com/studio/). Další informace najdete v tématu [instalace pro Android](https://developer.android.com/studio/install). Ukázka v tomto článku se používá Android SDK 27.

* [Ukázková aplikace pro Android](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/device/AndroidSample) Tato ukázka je součástí úložiště [Azure-IoT-Samples-Java](https://github.com/Azure-Samples/azure-iot-samples-java) .

* Port 8883 otevřete v bráně firewall. Ukázka zařízení v tomto rychlém startu používá protokol MQTT, který komunikuje přes port 8883. Tento port může být blokovaný v některých podnikových a vzdělávacích prostředích sítě. Další informace a způsoby, jak tento problém obejít, najdete v tématu [připojení k IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrace zařízení

Zařízení musí být zaregistrované ve vašem centru IoT, aby se mohlo připojit. V tomto rychlém startu zaregistrujete simulované zařízení pomocí služby Azure Cloud Shell.

1. Spuštěním následujícího příkazu v Azure Cloud Shell vytvořte identitu zařízení.

   **YourIoTHubName**: níže uvedený zástupný symbol nahraďte názvem, který jste zvolili pro Centrum IoT.

   **MyAndroidDevice**: Toto je název zařízení, které registrujete. Doporučuje se používat **MyAndroidDevice** , jak je znázorněno na obrázku. Pokud pro vaše zařízení zvolíte jiný název, budete ho muset použít i v celém rámci tohoto článku a před jeho spuštěním aktualizovat název zařízení v ukázkových aplikacích.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyAndroidDevice
    ```

2. Spuštěním následujícího příkazu v Azure Cloud Shell Získejte _připojovací řetězec zařízení_ pro zařízení, které jste právě zaregistrovali:

    **YourIoTHubName**: níže uvedený zástupný symbol nahraďte názvem, který jste zvolili pro Centrum IoT.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyAndroidDevice --output table
    ```

    Poznamenejte si připojovací řetězec zařízení, který vypadá takto:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}`

    Tuto hodnotu použijete později v tomto rychlém startu k odeslání telemetrie.

## <a name="send-simulated-telemetry"></a>Odesílání simulovaných telemetrických dat

1. V Android Studio otevřete projekt GitHub Sample Android. Projekt je umístěný v následujícím adresáři naklonované nebo stažené kopie úložiště [Azure-IoT-Sample-Java](https://github.com/Azure-Samples/azure-iot-samples-java) : *\azure-IoT-Samples-java\iot-hub\Samples\device\AndroidSample*.

2. V Android Studio otevřete *Gradle. Properties* pro ukázkový projekt a nahraďte zástupný text **Device_Connection_String** pomocí připojovacího řetězce zařízení, který jste si poznamenali dříve.

    ```
    DeviceConnectionString=HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}
    ```

3. V Android Studio klikněte na   >  **projekt synchronizace souborů se soubory Gradle**. Ověřte, že se sestavení dokončilo.

   > [!NOTE]
   > Pokud synchronizace projektu neproběhne úspěšně, může to být z některého z následujících důvodů:
   >
   > * Verze modulu plug-in pro Android Gradle a Gradle, na které se odkazuje v projektu, jsou zastaralé pro vaši verzi Android Studio. Podle [těchto pokynů](https://developer.android.com/studio/releases/gradle-plugin) můžete odkazovat a instalovat správné verze modulu plug-in a Gradle pro vaši instalaci.
   > * Licenční smlouva pro Android SDK nebyla podepsána. Podle pokynů ve výstupu sestavení podepište licenční smlouvu a Stáhněte si sadu SDK.

4. Po dokončení sestavení klikněte na **Spustit**  >  **Spustit aplikaci**. Nakonfigurujte aplikaci tak, aby běžela na fyzickém zařízení s Androidem nebo v emulátoru Androidu. Další informace o spuštění aplikace pro Android na fyzickém zařízení nebo emulátoru najdete v tématu [spuštění aplikace](https://developer.android.com/training/basics/firstapp/running-app).

5. Po načtení aplikace kliknutím na tlačítko **Start** spustíte odesílání telemetrie do IoT Hub:

    ![Aplikace](media/quickstart-send-telemetry-android/sample-screenshot.png)


## <a name="read-the-telemetry-from-your-hub"></a>Čtení telemetrie z centra

V této části použijete Azure Cloud Shell s [rozšířením IoT](/cli/azure/ext/azure-iot/iot) , abyste mohli monitorovat zprávy zařízení odesílané zařízením s Androidem.

1. Pomocí služby Azure Cloud Shell spusťte následující příkaz, který provede připojení a čtení zpráv z centra IoT:

   **YourIoTHubName**: níže uvedený zástupný symbol nahraďte názvem, který jste zvolili pro Centrum IoT.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name {YourIoTHubName} --output table
    ```

    Následující snímek obrazovky ukazuje výstup, ve kterém služba IoT Hub přijímá telemetrii odeslanou zařízením s Androidem:

      ![Čtení zpráv zařízení pomocí Azure CLI](media/quickstart-send-telemetry-android/read-data.png)
## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu nastavíte centrum IoT, zaregistrovali zařízení, poslali simulovanou telemetrii do centra pomocí aplikace pro Android a načetli telemetrii z centra pomocí Azure Cloud Shell.

Informace o tom, jak řídit simulované zařízení z back-endové aplikace, najdete v dalším rychlém startu.

> [!div class="nextstepaction"]
> [Rychlý start: Řízení zařízení připojeného k centru IoT](quickstart-control-device-android.md)
