---
title: Vývoj pro platformu Android věci pomocí sad SDK Azure IoT | Dokumentace Microsoftu
description: Pro vývojáře průvodce – Další informace o tom, jak vyvíjet na Android pomocí sady SDK služby Azure IoT Hub.
author: yzhong94
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 1/30/2019
ms.author: yizhon
ms.openlocfilehash: b213642b093c3b5f79e5993af91ae51517f09c70
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55747940"
---
# <a name="develop-for-mobile-devices-using-azure-iot-sdks"></a>Vývoj pro mobilní zařízení pomocí sad SDK Azure IoT
[Sady SDK služby Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) podporují první úroveň pro širokou řadu oblíbených platforem, včetně Windows, Linux, OSX, MBED a mobilní platformy, jako je Android a iOS.  V rámci naší snahy o povolení větší možnost volby a flexibilitu v nasazení IoT, sady Java SDK podporuje také [Android věci](https://developer.android.com/things/) platformy.  Vývojáři mohou využít výhod operačního systému Android věci na straně zařízení při používání [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/about-iot-hub) jako centrální zprávy centru s možností škálování na miliony současně připojených zařízení. 

Tento kurz popisuje postup pro vytvoření aplikace na straně zařízení na Android pomocí sady Java SDK Azure IoT.

## <a name="prerequisites"></a>Požadavky
* Android věci nepodporuje hardwaru s Androidem věci operační systém.  Můžete postupovat podle [dokumentaci Android věci](https://developer.android.com/things/get-started/kits#flash-at) o tom, jak flash Android věci.  Zajistěte, aby že vaše zařízení s Androidem věcí je připojený k Internetu pomocí základní periferních zařízení, jako je například klávesnici, zobrazení a myš, připojená.  Tento kurz používá Raspberry Pi 3.
* Nejnovější verzi [Android Studio](https://developer.android.com/studio/)
* Nejnovější verzi [Git](https://git-scm.com/)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrování zařízení

Zařízení musí být zaregistrované ve vašem centru IoT, aby se mohlo připojit. V tomto rychlém startu zaregistrujete simulované zařízení pomocí služby Azure Cloud Shell.

1. Ve službě Azure Cloud Shell spusťte následující příkazy pro přidání rozšíření rozhraní příkazového řádku IoT Hub a vytvoření identity zařízení. 

   **YourIoTHubName** : Nahraďte tento zástupný text pod názvem, který jste vybrali pro službu IoT hub.

   **MyAndroidThingsDevice** : Toto je název pro registrovaná zařízení. Použijte MyAndroidThingsDevice, jak je znázorněno. Pokud si zvolíte jiný název zařízení, budete ho muset používat v průběhu celého článku a aktualizovat název zařízení v ukázkových aplikacích, než je spustíte.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyAndroidThingsDevice
    ```

2. Spuštěním následujícího příkazu ve službě Azure Cloud Shell získejte _připojovací řetězec zařízení_ pro zařízení, které jste právě zaregistrovali:  **YourIoTHubName** : Nahraďte tento zástupný text pod názvem, který jste vybrali pro službu IoT hub.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyAndroidThingsDevice --output table
    ```

    Poznamenejte si připojovací řetězec zařízení, který vypadá nějak takto:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidThingsDevice;SharedAccessKey={YourSharedAccessKey}`

    Tuto hodnotu použijete později v tomto rychlém startu.

## <a name="building-an-android-things-application"></a>Vytvoření aplikace pro Android věcí
1.  Prvním krokem při sestavování aplikace Android věci se připojuje k zařízením s Androidem věci.  Připojení zařízení s Androidem věci k zobrazení a připojení k Internetu.  Zadejte Android věci [dokumentaci](https://developer.android.com/things/get-started/kits) na postup připojení k Wi-Fi.  Po připojení k Internetu, poznamenejte si IP adresy uvedené v části sítě.
2.  Použití [adb](https://developer.android.com/studio/command-line/adb) nástroj pro připojení k zařízení s Androidem věcí s IP adresou, jak je uvedeno nahoře.  Dvojitá Kontrola připojení pomocí tohoto příkazu z terminálu.  Měli byste vidět uvedené jako "připojeno" zařízení
    ```
    adb devices
    ```
3.  Stáhněte si naše ukázky pro takové věci, Android a Android z tohoto [úložiště](https://github.com/Azure-Samples/azure-iot-samples-java) použijte Git.
    ```
    git clone https://github.com/Azure-Samples/azure-iot-samples-java.git
    ```
4.  V nástroji Android Studio otevřete projekt pro Android v umístěný v "\azure-iot-samples-java\iot-hub\Samples\device\AndroidSample".
5.  Otevřete soubor gradle.properties a nahraďte "Device_connection_string" vaším připojovacím řetězcem zařízení jste si předtím poznamenali.
6.  Klikněte na tlačítko na spuštění – ladění a vyberte zařízení k nasazení tohoto kódu do zařízení s Androidem věci.
7.  Když je aplikace spuštěna úspěšně, uvidíte aplikaci spuštěnou na zařízení s Androidem věci.  Tato ukázková aplikace odešle náhodně generované teplotní hodnoty.

## <a name="read-the-telemetry-from-your-hub"></a>Čtení telemetrických dat z centra

Ukázková aplikace, kterou jste spustili v emulátoru XCode, ukazuje data o zprávách odeslaných ze zařízení. Můžete zobrazit také přijatá data procházející přes vaše centrum IoT. Rozšíření IoT Hub CLI se může ve vaší službě IoT Hub připojit ke koncovému bodu **Události** na straně služby. Toto rozšíření přijímá zprávy ze zařízení do cloudu odesílané z vašeho simulovaného zařízení. Back-endová aplikace služby IoT Hub se obvykle spouští v cloudu, aby mohla přijímat a zpracovávat zprávy typu zařízení-cloud.

Ve službě Azure Cloud Shell spusťte následující příkazy a položku `YourIoTHubName` nahraďte názvem centra IoT:

```
azurecli-interactive
az iot hub monitor-events --device-id MyAndroidThingsDevice --hub-name YourIoTHubName
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Další postup

* Další informace o [Správa připojení a spolehlivé zasílání zpráv](iot-hub-reliability-features-in-sdks.md) pomocí sady SDK služby IoT Hub.
* Další informace o tom, jak [vývoj pro mobilní platformy](iot-hub-how-to-develop-for-mobile-devices.md) jako je iOS a Android.
* [Podpora platformy Azure IoT SDK](iot-hub-device-sdk-platform-support.md)