---
title: Zprávy typu cloud zařízení s Azure IoT Hub (iOS) | Microsoft Docs
description: Postupy pro odesílání zpráv typu cloud zařízení pro zařízení ze služby Azure IoT hub pomocí sady Azure IoT SDK for iOS.
services: iot-hub
documentationcenter: ''
author: kgremban
manager: timlt
editor: ''
ms.assetid: ''
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2018
ms.author: kgremban
ms.openlocfilehash: 23dbd1f359f947b8e87ab4115887120dfd55907a
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="send-cloud-to-device-messages-with-iot-hub-ios"></a>Odesílání zpráv typu cloud zařízení službou IoT Hub (iOS)
[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]


Azure IoT Hub je plně spravovaná služba, která pomáhá povolit spolehlivou a zabezpečenou obousměrnou komunikaci mezi miliony zařízení a back-end řešení. [odeslání telemetrie ze zařízení do služby IoT hub] článek ukazuje, jak k vytvoření služby IoT hub, zřídit identitu zařízení v ní a kódu aplikaci ze simulovaného zařízení, která odesílá zprávy typu zařízení cloud.

Tento článek ukazuje, jak na:

* Z back-end vašeho řešení odesílání zpráv typu cloud zařízení na jedno zařízení prostřednictvím služby IoT Hub.
* Příjem zpráv typu cloud zařízení na zařízení.
* Z back-end vašeho řešení, žádosti o potvrzení o doručení (*zpětné vazby*) pro zprávy odeslané do zařízení ze služby IoT Hub.

Můžete najít další informace o zprávy typu cloud zařízení v [Příručka vývojáře pro službu IoT Hub][IoT Hub developer guide - C2D].

Na konci tohoto článku můžete spustit Swift dva projekty iOS:

* **Ukázka zařízení**, stejné aplikace vytvořená v [odeslání telemetrie ze zařízení do služby IoT hub], který se připojuje ke službě IoT hub a přijímá zprávy typu cloud zařízení.
* **Ukázka služby**, který odešle zprávu cloud zařízení na aplikaci simulovaného zařízení prostřednictvím služby IoT Hub a pak obdrží jeho potvrzení o doručení.

> [!NOTE]
> IoT Hub je podpora v sadě SDK pro mnoho zařízení platformy a jazyky (například C, Javy a JavaScriptu) prostřednictvím SDK pro zařízení Azure IoT. Podrobné pokyny o tom, jak připojit zařízení ke kódu v tomto kurzu a obecně do služby Azure IoT Hub, najdete v článku [Azure střediska pro vývojáře IoT].

Pro absolvování tohoto kurzu potřebujete:

- Aktivní účet Azure. (Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet][lnk-free-trial].)
- Aktivním centrem IoT v Azure. 
- Ukázka kódu z [ukázek Azure](https://github.com/Azure-Samples/azure-iot-samples-ios/archive/master.zip) .
- Nejnovější verze [XCode](https://developer.apple.com/xcode/) používající nejnovější verzi sady SDK pro iOS. Tento rychlý start byl testován s XCode 9.3 a iOS 11.3.
- Nejnovější verze [CocoaPods](https://guides.cocoapods.org/using/getting-started.html).


## <a name="simulate-an-iot-device"></a>Simulovat zařízení s IoT
V této části můžete simulovat zařízení se systémem iOS spuštěná Swift aplikace pro příjem zpráv typu cloud zařízení ze služby IoT hub. 

Toto je ukázkový ukázka zařízení, které vytvoříte v následujícím článku [odeslání telemetrie ze zařízení do služby IoT hub]. Pokud už máte tuto spuštěná, můžete tuto část přeskočit.

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

1. Načtení připojovacího řetězce pro vaše zařízení. Můžete zkopírovat tento řetězec z portálu Azure v okně podrobností zařízení nebo načíst pomocí rozhraní příkazového řádku následující příkaz: 

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id {YourDeviceID} --output table
    ```

1. Otevřete ukázkový pracovní prostor v XCode.

   ```sh
   open "MQTT Client Sample.xcworkspace"
   ```

2. Rozbalte **MQTT klienta ukázka** projekt a potom složku se stejným názvem.  
3. Otevřete soubor **ViewController.swift** pro úpravy v XCode. 
4. Vyhledejte **connectionString** proměnné a hodnotu s připojením zařízení aktualizujte řetězce, které jste zkopírovali v prvním kroku.
5. Uložte provedené změny. 
6. Spusťte projekt v emulátoru zařízení pomocí tlačítka **Build and run** (Sestavit a spustit) nebo kombinace kláves **command + r**. 

   ![Spuštění projektu](media/quickstart-send-telemetry-ios/run-sample.png)


## <a name="simulate-a-service-device"></a>Simulovat zařízení služby

V této části můžete simulovat druhé zařízení iOS s Swift aplikaci, která odesílá zprávy typu cloud zařízení prostřednictvím služby IoT hub. Tato konfigurace je užitečná pro scénáře IoT níž se nachází jeden iPhone nebo iPad funguje jako řadič pro ostatní zařízení se systémem iOS připojené do služby IoT hub. 

### <a name="install-cocoapods"></a>Instalace CocoaPods

CocoaPods spravují závislosti pro projekty iOS využívající knihovny třetích stran.

Přejděte do složky ukázek Azure IoT iOS, který jste si stáhli v požadavky. Potom přejděte na ukázkový projekt služby:

```sh
cd quickstart/sample-service
```

Ujistěte se, že je XCode zavřené, a pak spuštěním následujícího příkazu nainstalujte CocoaPods deklarované v souboru **podfile**:

```sh
pod install
```

Kromě instalace požadovaných podů pro váš projekt příkaz k instalaci vytvořil také soubor pracovního prostoru XCode, který je předem nakonfigurovaný tak, aby používal pody pro závislosti.

### <a name="run-the-sample-service-application"></a>Spuštění ukázkové aplikace služby

1. Načtěte připojovací řetězec služby pro službu IoT hub. Tento řetězec můžete zkopírovat z portálu Azure z **iothubowner** zásad v **zásady sdíleného přístupu** okně je nebo načtěte pomocí následujícího příkazu příkazového řádku:  

    ```azurecli-interactive
    az iot hub show-connection-string --hub-name {YourIoTHubName} --output table
    ```

2. Otevřete ukázkový pracovní prostor v XCode.

   ```sh
   open AzureIoTServiceSample.xcworkspace
   ```

3. Rozbalte **AzureIoTServiceSample** projekt a poté rozbalte složku se stejným názvem.  
4. Otevřete soubor **ViewController.swift** pro úpravy v XCode. 
5. Vyhledejte **connectionString** proměnnou a aktualizace hodnotu s služby připojovací řetězec, který jste zkopírovali dříve.
6. Uložte provedené změny. 
7. V Xcode změňte nastavení emulátoru na zařízení iOS jiný než můžete použít ke spuštění zařízení IoT. XCode nelze spustit více emulátorů stejného typu. 

   ![Změňte zařízení emulátoru](media/iot-hub-ios-swift-c2d/change-device.png)

8. Spusťte projekt v emulátoru zařízení pomocí **sestavit a spustit** tlačítko nebo klíče se seznamem **příkaz + r**. 

   ![Spuštění projektu](media/iot-hub-ios-swift-c2d/run-app.png)


## <a name="send-a-cloud-to-device-message"></a>Odeslání zprávy typu cloud zařízení
Nyní jste připraveni používat dvě aplikace posílat a přijímat zprávy typu cloud zařízení.

1. V **iOS ukázkové aplikace** aplikaci spuštěnou na simulované zařízení IoT, klikněte na tlačítko **spustit**. Aplikace spustí odesílání zpráv typu zařízení cloud, ale také začne naslouchat pro zprávy typu cloud zařízení. 

   ![Zobrazit ukázkovou aplikaci zařízení IoT](media/iot-hub-ios-swift-c2d/view-d2c.png)

2. V **ukázka klienta služby IoTHub** aplikaci spuštěnou na zařízení simulovaného služby, zadejte ID pro zařízení IoT, které chcete k odesílání zpráv. 
3. Zapsat zprávu ve formátu prostého textu a pak klikněte na **odeslat**. 

Několik akcí dojít při kliknutí na tlačítko Odeslat. Ukázka služby odešle zprávu do vaší IoT hub, který má aplikace přístup k kvůli připojení k službě řetězce, které zadaný. Služby IoT hub kontroluje ID zařízení, odešle zprávu do cílové zařízení a odešle oznámení o potvrzení zdrojového zařízení. Aplikaci spuštěnou na simulovaného zařízení IoT kontroluje zpráv ze služby IoT Hub a vytiskne text z nejaktuálnějšího na obrazovce.

Výstup by měl vypadat jako v následujícím příkladu:

   ![Zobrazení zpráv typu cloud zařízení](media/iot-hub-ios-swift-c2d/view-c2d.png)


## <a name="next-steps"></a>Další postup
V tomto kurzu jste zjistili, jak odesílat a přijímat zprávy typu cloud zařízení. 

Příklady dokončení začátku do konce řešení, které pomocí služby IoT Hub, najdete v sekci [akcelerátoru řešení Azure IoT vzdálené monitorování].

Další informace o vývoji řešení službou IoT Hub, najdete v článku [Příručka vývojáře pro službu IoT Hub].

<!-- Images -->
[img-simulated-device]: media/iot-hub-python-python-c2d/simulated-device.png
[img-send-command]:  media/iot-hub-python-python-c2d/send-command.png
[img-message-recieved]: media/iot-hub-python-python-c2d/message-recieved.png

<!-- Links -->
[odeslání telemetrie ze zařízení do služby IoT hub]: quickstart-send-telemetry-ios.md

[IoT Hub developer guide - C2D]: iot-hub-devguide-messaging.md
[Příručka vývojáře pro službu IoT Hub]: iot-hub-devguide.md
[Azure střediska pro vývojáře IoT]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Azure portal]: https://portal.azure.com
[akcelerátoru řešení Azure IoT vzdálené monitorování]: https://azure.microsoft.com/documentation/suites/iot-suite/
