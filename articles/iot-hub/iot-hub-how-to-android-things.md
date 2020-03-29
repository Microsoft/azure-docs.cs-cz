---
title: Vývoj platformy Pro Android Things pomocí sad Azure IoT SDK | Dokumenty společnosti Microsoft
description: Průvodce pro vývojáře – Přečtěte si, jak vyvíjet vývoj v androidových sadách Azure IoT Hub SDK.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: robinsh
ms.openlocfilehash: a06583e9aab4b082517d47c1022f7bec5184b9bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673383"
---
# <a name="develop-for-android-things-platform-using-azure-iot-sdks"></a>Vývoj platformy Pro Android Things pomocí sad Azure IoT SDK

[Sady Azure IoT Hub SDK](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) poskytují podporu první úrovně pro oblíbené platformy, jako jsou Windows, Linux, OSX, MBED a mobilní platformy, jako je Android a iOS.  Java SDK podporuje také platformu [Android Things](https://developer.android.com/things/) v rámci našeho závazku umožnit větší výběr a flexibilitu v nasazení IoT.  Vývojáři můžou využívat výhody operačního systému Android Things na straně zařízení a zároveň používat [Azure IoT Hub](about-iot-hub.md) jako centrální centrum zpráv, které se škáluje na miliony současně připojených zařízení.

Tento kurz popisuje kroky k vytvoření aplikace na straně zařízení na Android věci pomocí Azure IoT Java SDK.

## <a name="prerequisites"></a>Požadavky

* Android Věci podporované hardware s Operačním systémem Android Things běží.  Můžete sledovat [Dokumentaci Android Things](https://developer.android.com/things/get-started/kits#flash-at) o tom, jak blikat Android Things OS.  Ujistěte se, že je vaše zařízení Android Things připojené k internetu se základními periferními zařízeními, jako je klávesnice, displej a myš.  Tento výukový program používá Raspberry Pi 3.

* Nejnovější verze [Android Studia](https://developer.android.com/studio/)

* Nejnovější verze [Gitu](https://git-scm.com/)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrování zařízení

Zařízení musí být zaregistrované ve vašem centru IoT, aby se mohlo připojit. V tomto rychlém startu zaregistrujete simulované zařízení pomocí služby Azure Cloud Shell.

1. Ve službě Azure Cloud Shell spusťte následující příkazy pro přidání rozšíření rozhraní příkazového řádku IoT Hub a vytvoření identity zařízení.

   **YourIoTHubName:** Tento zástupný text nahraďte názvem, který si zvolíte pro své centrum IoT.

   **MyAndroidThingsDevice** : Toto je název pro registrované zařízení. Použijte MyAndroidThingsDevice, jak je znázorněno. Pokud si zvolíte jiný název zařízení, budete ho muset používat v průběhu celého článku a aktualizovat název zařízení v ukázkových aplikacích, než je spustíte.

    ```azurecli-interactive
    az extension add --name azure-iot
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyAndroidThingsDevice
    ```

2. Spusťte následující příkazy v Azure Cloud Shell získat *připojovací řetězec zařízení* pro zařízení, které jste právě zaregistrovali. Nahraďte `YourIoTHubName` níže název, který zvolíte pro centrum IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyAndroidThingsDevice --output table
    ```

    Poznamenejte si připojovací řetězec zařízení, který vypadá nějak takto:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidThingsDevice;SharedAccessKey={YourSharedAccessKey}`

    Tuto hodnotu použijete později v tomto rychlém startu.

## <a name="building-an-android-things-application"></a>Vytváření aplikace Android Things

1. Prvním krokem k vytvoření aplikace Android Things je připojení k zařízením Android Things. Připojte zařízení Android Things k displeji a připojte ho k internetu. Android Věci poskytují [dokumentaci](https://developer.android.com/things/get-started/kits) o tom, jak se připojit k WiFi. Po připojení k internetu si poznamenejte ip adresu uvedenou v části Sítě.

2. Pomocí nástroje [adb](https://developer.android.com/studio/command-line/adb) se připojte k zařízení Android Things s výše uvedenou IP adresou. Zkontrolujte připojení pomocí tohoto příkazu z terminálu. Měla by se vám zobrazit zařízení uvedená jako "připojená".

   ```
   adb devices
   ```

3. Stáhněte si naši ukázku pro Android/Android Věci z tohoto [úložiště](https://github.com/Azure-Samples/azure-iot-samples-java) nebo použijte Git.

   ```
   git clone https://github.com/Azure-Samples/azure-iot-samples-java.git
   ```

4. Ve Studiu pro Android otevřete Projekt Androidu v části "\azure-iot-samples-java\iot-hub\Samples\device\AndroidSample".

5. Otevřete soubor gradle.properties a nahraďte "Device_connection_string" připojovacím řetězcem zařízení, který byl zaznamenán dříve.
 
6. Klikněte na Spustit - Ladění a vyberte zařízení pro nasazení tohoto kódu do zařízení Android Things.

7. Po úspěšném spuštění aplikace se zobrazí aplikace spuštěná na zařízení Android Things. Tato ukázková aplikace odesílá náhodně generované hodnoty teploty.

## <a name="read-the-telemetry-from-your-hub"></a>Čtení telemetrických dat z centra

Data můžete zobrazit prostřednictvím centra IoT, jak je přijata. Rozšíření IoT Hub CLI se může ve vaší službě IoT Hub připojit ke koncovému bodu **Události** na straně služby. Toto rozšíření přijímá zprávy ze zařízení do cloudu odesílané z vašeho simulovaného zařízení. Back-endová aplikace služby IoT Hub se obvykle spouští v cloudu, aby mohla přijímat a zpracovávat zprávy typu zařízení-cloud.

Ve službě Azure Cloud Shell spusťte následující příkazy a položku `YourIoTHubName` nahraďte názvem centra IoT:

```azurecli-interactive
az iot hub monitor-events --device-id MyAndroidThingsDevice --hub-name YourIoTHubName
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Další kroky

* Přečtěte [si, jak spravovat připojení a spolehlivé zasílání zpráv](iot-hub-reliability-features-in-sdks.md) pomocí sad SDK služby IoT Hub.
* Přečtěte si, jak [se vyvíjet pro mobilní platformy,](iot-hub-how-to-develop-for-mobile-devices.md) jako je iOS a Android.
* [Podpora platformy Azure IoT SDK](iot-hub-device-sdk-platform-support.md)
