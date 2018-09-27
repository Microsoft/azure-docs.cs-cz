---
title: Zprávy typu cloud zařízení pomocí služby Azure IoT Hub (iOS) | Dokumentace Microsoftu
description: Postup odesílání zpráv typu cloud zařízení na zařízení ze služby Azure IoT hub pomocí sad Azure IoT SDK pro iOS.
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/19/2018
ms.author: kgremban
ms.openlocfilehash: 005136b6da841376daad27fa439949927f098882
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2018
ms.locfileid: "47223743"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-ios"></a>Odesílání zpráv typu cloud zařízení pomocí služby IoT Hub (iOS)
[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]


Azure IoT Hub je plně spravovaná služba, která pomáhá povolit spolehlivou a zabezpečenou obousměrnou komunikaci mezi miliony zařízení a back-endu řešení. [Odesílání telemetrických dat ze zařízení do služby IoT hub] článek popisuje, jak k vytvoření služby IoT hub, zřídit identitu zařízení v něm a kódu aplikace simulovaného zařízení, která odesílá zprávy typu zařízení cloud.

Tento článek vám ukáže, jak do:

* Z back-end vašeho řešení odesílat zprávy typu cloud zařízení na jediné zařízení prostřednictvím služby IoT Hub.
* Příjem zpráv typu cloud zařízení na zařízení.
* Z back-end vašeho řešení, požádat o doručení potvrzení (*zpětnou vazbu*) pro zprávy odeslané do zařízení ze služby IoT Hub.

Můžete najít další informace o zprávy typu cloud zařízení v [Příručka vývojáře pro IoT Hub][IoT Hub developer guide - C2D].

Na konci tohoto článku spustíte dvě Swift projekty iOS:

* **Ukázka zařízení**, stejnou aplikaci vytvořenou v [odesílání telemetrických dat ze zařízení do služby IoT hub], který se připojí ke službě IoT hub a přijímá zprávy typu cloud zařízení.
* **Ukázka služby**, která odesílá zprávy typu cloud zařízení do aplikace simulovaného zařízení prostřednictvím služby IoT Hub a potom přijímá jeho doručení potvrzení.

> [!NOTE]
> IoT Hub má sady SDK podporují mnoho platforem zařízení a jazyky (včetně C, Javy a JavaScriptu) prostřednictvím sady SDK pro zařízení Azure IoT. Podrobné pokyny o tom, jak připojit zařízení ke kódu v tomto kurzu a obecně pro službu Azure IoT Hub, najdete v článku [centrum pro vývojáře Azure IoT].

Pro absolvování tohoto kurzu potřebujete:

- Aktivní účet Azure. (Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet][lnk-free-trial].)
- Aktivním centrem IoT v Azure. 
- Ukázkový kód z [ukázky v Azure](https://github.com/Azure-Samples/azure-iot-samples-ios/archive/master.zip) .
- Nejnovější verze [XCode](https://developer.apple.com/xcode/) používající nejnovější verzi sady SDK pro iOS. Tento rychlý start byl testován s XCode 9.3 a iOS 11.3.
- Nejnovější verze [CocoaPods](https://guides.cocoapods.org/using/getting-started.html).


## <a name="simulate-an-iot-device"></a>Simulace zařízení IoT
V této části simulovat zařízení se systémem iOS Swift aplikaci pro příjem zpráv typu cloud zařízení ze služby IoT hub. 

Toto je ukázková zařízení, který vytvoříte v následujícím článku [odesílání telemetrických dat ze zařízení do služby IoT hub]. Pokud už máte, systém, můžete tuto část přeskočit.

### <a name="install-cocoapods"></a>Instalace CocoaPods

CocoaPods spravují závislosti pro projekty iOS využívající knihovny třetích stran.

V okně terminálu přejděte do složky Azure-IoT-Samples-iOS, kterou jste stáhli v rámci požadavků. Pak přejděte do ukázkového projektu:

```sh
cd quickstart/sample-device
```

Ujistěte se, že je XCode zavřené, a pak spuštěním následujícího příkazu nainstalujte CocoaPods deklarované v souboru **podfile**:

```sh
pod install
```

Kromě instalace požadovaných podů pro váš projekt příkaz k instalaci vytvořil také soubor pracovního prostoru XCode, který je předem nakonfigurovaný tak, aby používal pody pro závislosti. 

### <a name="run-the-sample-device-application"></a>Spuštění ukázkové aplikace zařízení 

1. Načtení připojovacího řetězce pro vaše zařízení. Můžete zkopírovat tento řetězec z portálu Azure portal v okně podrobností o zařízení, nebo načíst pomocí následujícího příkazu rozhraní příkazového řádku: 

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id {YourDeviceID} --output table
    ```

1. Otevřete ukázkový pracovní prostor v XCode.

   ```sh
   open "MQTT Client Sample.xcworkspace"
   ```

2. Rozbalte **MQTT Client Sample** projekt a potom na složku se stejným názvem.  
3. Otevřete soubor **ViewController.swift** pro úpravy v XCode. 
4. Hledat **connectionString** proměnné a hodnotu s připojením zařízení aktualizujte řetězce, které jste zkopírovali v prvním kroku.
5. Uložte provedené změny. 
6. Spusťte projekt v emulátoru zařízení pomocí tlačítka **Build and run** (Sestavit a spustit) nebo kombinace kláves **command + r**. 

   ![Spuštění projektu](media/quickstart-send-telemetry-ios/run-sample.png)


## <a name="simulate-a-service-device"></a>Simulace zařízení služby

V této části můžete simulovat druhé zařízení s iOS s využitím aplikace Swift, která odesílá zprávy typu cloud zařízení prostřednictvím služby IoT hub. Tato konfigurace je užitečná pro scénáře IoT, ve kterých je jeden Iphonu nebo Ipadu fungovat jako řadič pro ostatní zařízení se systémem iOS připojené do služby IoT hub. 

### <a name="install-cocoapods"></a>Instalace CocoaPods

CocoaPods spravují závislosti pro projekty iOS využívající knihovny třetích stran.

Přejděte do složky Ukázky iOS Azure IoT, který jste stáhli v rámci požadavků. Pak přejděte do ukázkového projektu služby:

```sh
cd quickstart/sample-service
```

Ujistěte se, že je XCode zavřené, a pak spuštěním následujícího příkazu nainstalujte CocoaPods deklarované v souboru **podfile**:

```sh
pod install
```

Kromě instalace požadovaných podů pro váš projekt příkaz k instalaci vytvořil také soubor pracovního prostoru XCode, který je předem nakonfigurovaný tak, aby používal pody pro závislosti.

### <a name="run-the-sample-service-application"></a>Spuštění ukázkové aplikace služby

1. Načtení připojovacího řetězce služby pro službu IoT hub. Tento řetězec můžete zkopírovat z portálu Azure portal ze **iothubowner** zásad **zásady sdíleného přístupu** okně je nebo načtěte pomocí následujícího příkazu rozhraní příkazového řádku:  

    ```azurecli-interactive
    az iot hub show-connection-string --hub-name {YourIoTHubName} --output table
    ```

2. Otevřete ukázkový pracovní prostor v XCode.

   ```sh
   open AzureIoTServiceSample.xcworkspace
   ```

3. Rozbalte **AzureIoTServiceSample** projektu a pak rozbalte složku se stejným názvem.  
4. Otevřete soubor **ViewController.swift** pro úpravy v XCode. 
5. Hledat **connectionString** proměnné a aktualizujte její hodnotu připojovacím řetězcem služby, který jste zkopírovali dříve.
6. Uložte provedené změny. 
7. V Xcode změňte nastavení emulátoru na různých iOS zařízení než na kolik máte použitý ke spuštění zařízení IoT. XCode nelze spustit více emulátory stejného typu. 

   ![Změnit zařízení emulátoru](media/iot-hub-ios-swift-c2d/change-device.png)

8. Spusťte projekt v emulátoru zařízení pomocí **sestavíte a spustíte** tlačítko nebo kombinace kláves **Command + r**. 

   ![Spuštění projektu](media/iot-hub-ios-swift-c2d/run-app.png)


## <a name="send-a-cloud-to-device-message"></a>Odeslání zprávy typu cloud zařízení
Nyní jste připraveni používat dvě aplikace odesílat a přijímat zprávy typu cloud zařízení.

1. V **Ukázka aplikace pro iOS** aplikaci spuštěnou v simulovaném zařízení IoT, klikněte na tlačítko **Start**. Aplikace spustí, odesílání zpráv typu zařízení cloud, ale také začne naslouchat pro zprávy typu cloud zařízení. 

   ![Zobrazit ukázkovou aplikaci v zařízení IoT](media/iot-hub-ios-swift-c2d/view-d2c.png)

2. V **ukázka klienta služby IOT hub** aplikaci spuštěnou v service s Simulovaná zařízení, zadejte ID zařízení IoT, které chcete, aby odeslat zprávu do. 
3. Zapsat zprávu ve formátu prostého textu a pak klikněte na **odeslat**. 

Několik akcí dojít také klikněte na tlačítko Odeslat. Ukázka služby odešle zprávu do vašeho IoT hub, který má aplikace přístup k kvůli připojení k službě řetězec, který jste za předpokladu. Služby IoT hub kontroluje ID zařízení, odešle zprávu do cílového zařízení a odešle oznámení o potvrzení na zdrojové zařízení. Aplikaci spuštěnou na zařízení IoT s Simulovaná kontroluje pro zprávy ze služby IoT Hub a vytiskne text z nejaktuálnějšího na obrazovce.

Váš výstup by měl vypadat jako v následujícím příkladu:

   ![Zobrazit zprávy typu cloud zařízení](media/iot-hub-ios-swift-c2d/view-c2d.png)


## <a name="next-steps"></a>Další postup
V tomto kurzu jste zjistili, jak posílat a přijímat zprávy typu cloud zařízení. 

Příklady kompletní řešení začátku do konce, které používají služby IoT Hub najdete v tématu [Akcelerátor řešení Azure IoT vzdálené monitorování].

Další informace o vývoji řešení s využitím služby IoT Hub, najdete v článku [Příručka vývojáře pro IoT Hub].

<!-- Images -->
[img-simulated-device]: media/iot-hub-python-python-c2d/simulated-device.png
[img-send-command]:  media/iot-hub-python-python-c2d/send-command.png
[img-message-recieved]: media/iot-hub-python-python-c2d/message-recieved.png

<!-- Links -->
[Odesílání telemetrických dat ze zařízení do služby IoT hub]: quickstart-send-telemetry-ios.md

[IoT Hub developer guide - C2D]: iot-hub-devguide-messaging.md
[Příručka vývojáře pro IoT Hub]: iot-hub-devguide.md
[Centrum pro vývojáře Azure IoT]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[Azure portal]: https://portal.azure.com
[Akcelerátor řešení Azure IoT vzdálené monitorování]: https://azure.microsoft.com/documentation/suites/iot-suite/
