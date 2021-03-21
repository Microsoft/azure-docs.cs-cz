---
title: Vývoj pro platformu Android Platforms pomocí sad SDK Azure IoT | Microsoft Docs
description: Příručka pro vývojáře – Přečtěte si informace o vývoji Androidu s využitím sad Azure IoT Hub SDK.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: robinsh
ms.openlocfilehash: 6008ca1549629bcbb582f38de2ab1b453cd73a99
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94843753"
---
# <a name="develop-for-android-things-platform-using-azure-iot-sdks"></a>Vývoj pro platformu Android Platforms pomocí sad SDK Azure IoT

[Azure IoT Hub SDK](./iot-hub-devguide-sdks.md) poskytují podporu první úrovně pro oblíbené platformy, jako jsou Windows, Linux, OSX, MBED a mobilní platformy, jako je Android a iOS.  V rámci našeho závazku o zajištění větší možnosti a flexibility v nasazeních IoT podporuje sada Java SDK také platformu [Androidu](https://developer.android.com/things/) .  Vývojáři můžou využít výhody operačního systému Androidu na straně zařízení a při použití [Azure IoT Hub](about-iot-hub.md) jako centrálního centra zpráv, které se škálují na miliony současně připojených zařízení.

Tento kurz popisuje kroky pro vytvoření aplikace na straně zařízení v Androidu pomocí sady Azure IoT Java SDK.

## <a name="prerequisites"></a>Předpoklady

* Android podporuje hardware s operačním systémem Androidu.  [Dokumentaci k Androidu](https://developer.android.com/things/get-started/kits#flash-at) můžete sledovat v tématu Postup pro Flash Androidu.  Ujistěte se, že zařízení s Androidem je připojené k Internetu se základními periferními zařízeními, jako jsou klávesnice, displeje a připojené myší.  V tomto kurzu se používá Malina Pi 3.

* Nejnovější verze [Android Studio](https://developer.android.com/studio/)

* Nejnovější verzi [Gitu](https://git-scm.com/)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrace zařízení

Zařízení musí být zaregistrované ve vašem centru IoT, aby se mohlo připojit. V tomto rychlém startu zaregistrujete simulované zařízení pomocí služby Azure Cloud Shell.

1. Ve službě Azure Cloud Shell spusťte následující příkazy pro přidání rozšíření rozhraní příkazového řádku IoT Hub a vytvoření identity zařízení.

   **YourIoTHubName:** Tento zástupný text nahraďte názvem, který si zvolíte pro své centrum IoT.

   **MyAndroidThingsDevice** : Toto je název zadaný pro registrované zařízení. Použijte MyAndroidThingsDevice, jak je znázorněno na obrázku. Pokud si zvolíte jiný název zařízení, budete ho muset používat v průběhu celého článku a aktualizovat název zařízení v ukázkových aplikacích, než je spustíte.

    ```azurecli-interactive
    az extension add --name azure-iot
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyAndroidThingsDevice
    ```

2. Spuštěním následujících příkazů v Azure Cloud Shell Získejte *připojovací řetězec zařízení* pro zařízení, které jste právě zaregistrovali. Nahraďte `YourIoTHubName` níže názvem, který zvolíte pro Centrum IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyAndroidThingsDevice --output table
    ```

    Poznamenejte si připojovací řetězec zařízení, který vypadá takto:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidThingsDevice;SharedAccessKey={YourSharedAccessKey}`

    Tuto hodnotu použijete později v tomto rychlém startu.

## <a name="building-an-android-things-application"></a>Sestavování aplikace pro Android

1. Prvním krokem při sestavování aplikace pro Android se připojuje k zařízením s Androidem. Připojte zařízení s Androidem k zobrazení a připojte ho k Internetu. Android nabízí [dokumentaci](https://developer.android.com/things/get-started/kits) , jak se připojit k Wi-Fi. Po připojení k Internetu si poznamenejte IP adresu uvedenou v části sítě.

2. Pomocí nástroje [ADB](https://developer.android.com/studio/command-line/adb) se připojte ke svému zařízení s Androidem s použitím IP adresy uvedené výše. Pomocí tohoto příkazu v terminálu dvakrát Projděte připojení. Měla by se zobrazit vaše zařízení uvedená jako "připojená".

   ```
   adb devices
   ```

3. Stáhněte si náš příklad pro Android/Android z tohoto [úložiště](https://github.com/Azure-Samples/azure-iot-samples-java) nebo použijte Git.

   ```
   git clone https://github.com/Azure-Samples/azure-iot-samples-java.git
   ```

4. V Android Studio otevřete projekt pro Android v umístění v části "\azure-iot-samples-java\iot-hub\Samples\device\AndroidSample".

5. Otevřete soubor Gradle. Properties a nahraďte "Device_connection_string" vaším připojovacím řetězcem zařízení, který jste si poznamenali dříve.
 
6. Klikněte na spustit – ladění a vyberte své zařízení, abyste mohli tento kód nasadit na zařízení s Androidem.

7. Po úspěšném spuštění aplikace uvidíte aplikaci spuštěnou na zařízení s Androidem. Tato ukázková aplikace posílá náhodně generované čtení teploty.

## <a name="read-the-telemetry-from-your-hub"></a>Čtení telemetrie z centra

Data můžete v rámci služby IoT Hub zobrazit při jejich přijetí. Rozšíření IoT Hub CLI se může ve vaší službě IoT Hub připojit ke koncovému bodu **Události** na straně služby. Toto rozšíření přijímá zprávy ze zařízení do cloudu odesílané z vašeho simulovaného zařízení. Back-endová aplikace služby IoT Hub se obvykle spouští v cloudu, aby mohla přijímat a zpracovávat zprávy typu zařízení-cloud.

Ve službě Azure Cloud Shell spusťte následující příkazy a položku `YourIoTHubName` nahraďte názvem centra IoT:

```azurecli-interactive
az iot hub monitor-events --device-id MyAndroidThingsDevice --hub-name YourIoTHubName
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Další kroky

* Přečtěte si, [jak spravovat připojení a spolehlivé zasílání zpráv](iot-hub-reliability-features-in-sdks.md) pomocí sad IoT Hub SDK.
* Přečtěte si, jak [vyvíjet na mobilních platformách](iot-hub-how-to-develop-for-mobile-devices.md) , jako jsou iOS a Android.
* [Podpora platformy Azure IoT SDK](iot-hub-device-sdk-platform-support.md)