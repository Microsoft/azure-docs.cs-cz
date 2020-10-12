---
title: Zprávy z cloudu do zařízení s Azure IoT Hub (iOS) | Microsoft Docs
description: Postup posílání zpráv z cloudu na zařízení ze služby Azure IoT Hub pomocí sad SDK Azure IoT pro iOS.
author: kgremban
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/19/2018
ms.author: kgremban
ms.custom: mqtt
ms.openlocfilehash: d8552391e8e8c389a44174595305b8f28224a833
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "81732539"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-ios"></a>Posílání zpráv z cloudu na zařízení pomocí IoT Hub (iOS)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub je plně spravovaná služba, která pomáhá zajistit spolehlivou a zabezpečenou obousměrnou komunikaci mezi miliony zařízení a back-endu řešení. V rychlém startu [pro odeslání ze zařízení do služby IoT Hub](quickstart-send-telemetry-ios.md) se dozvíte, jak vytvořit centrum IoT, zřídit v něm identitu zařízení a zakódovat aplikaci simulovaného zařízení, která odesílá zprávy typu zařízení-Cloud.

V tomto kurzu získáte informace o následujících postupech:

* Z back-endu vašeho řešení odesílají zprávy typu cloud-zařízení do jediného zařízení prostřednictvím IoT Hub.

* Příjem zpráv typu cloud-zařízení na zařízení.

* Z back-endu vašeho řešení požadavek na doručení zpráv*feedback*odesílaných do zařízení z IoT Hub.

Další informace o zprávách typu cloud-zařízení najdete v [části zasílání zpráv v IoT Hub příručce pro vývojáře](iot-hub-devguide-messaging.md).

Na konci tohoto článku spustíte dva projekty SWIFT pro iOS:

* **ukázková zařízení –** stejná aplikace vytvořená v [rámci odesílání telemetrie ze zařízení do služby IoT Hub](quickstart-send-telemetry-ios.md), která se připojuje ke službě IoT Hub a přijímá zprávy typu cloud-zařízení.

* **ukázková služba**, která pošle zprávu typu cloud-zařízení do aplikace simulovaného zařízení prostřednictvím IoT Hub a potom obdrží potvrzení o doručení.

> [!NOTE]
> IoT Hub podporuje sadu SDK pro mnoho platforem a jazyků zařízení (včetně C, Java, Pythonu a JavaScriptu) prostřednictvím sad SDK pro zařízení Azure IoT. Podrobné pokyny, jak připojit zařízení k kódu tohoto kurzu a obecně k Azure IoT Hub, najdete v [centru pro vývojáře Azure IoT](https://www.azure.com/develop/iot).

## <a name="prerequisites"></a>Požadavky

* Aktivní účet Azure. (Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) .)

* Aktivní centrum IoT v Azure.

* Ukázka kódu z [ukázek Azure](https://github.com/Azure-Samples/azure-iot-samples-ios/archive/master.zip).

* Nejnovější verze [XCode](https://developer.apple.com/xcode/) používající nejnovější verzi sady SDK pro iOS. Tento rychlý start byl testován s XCode 9.3 a iOS 11.3.

* Nejnovější verze [CocoaPods](https://guides.cocoapods.org/using/getting-started.html).

* Ujistěte se, že je v bráně firewall otevřený port 8883. Ukázka zařízení v tomto článku používá protokol MQTT, který komunikuje přes port 8883. Tento port může být blokovaný v některých podnikových a vzdělávacích prostředích sítě. Další informace a způsoby, jak tento problém obejít, najdete v tématu [připojení k IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="simulate-an-iot-device"></a>Simulace zařízení IoT

V této části simulujete zařízení s iOS, na kterém běží aplikace SWIFT, pro příjem zpráv z cloudu na zařízení ze služby IoT Hub. 

Toto je ukázkové zařízení, které vytvoříte v článku [odeslání telemetrie ze zařízení do služby IoT Hub](quickstart-send-telemetry-ios.md). Pokud již máte spuštěnou službu, můžete tuto část přeskočit.

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

### <a name="run-the-sample-device-application"></a>Spustit ukázkovou aplikaci zařízení

1. Načtěte připojovací řetězec pro vaše zařízení. Tento řetězec můžete zkopírovat z [Azure Portal](https://portal.azure.com) v okně Podrobnosti o zařízení, nebo ho načíst pomocí následujícího příkazu CLI:

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id {YourDeviceID} --output table
    ```

1. Otevřete ukázkový pracovní prostor v XCode.

   ```sh
   open "MQTT Client Sample.xcworkspace"
   ```

2. Rozbalte projekt **ukázkového klienta MQTT** a pak složku se stejným názvem.  

3. Otevřete soubor **ViewController.swift** pro úpravy v XCode. 

4. Vyhledejte proměnnou **ConnectionString** a aktualizujte hodnotu pomocí připojovacího řetězce zařízení, který jste zkopírovali v prvním kroku.

5. Uložte provedené změny. 

6. Spusťte projekt v emulátoru zařízení pomocí tlačítka **Build and run** (Sestavit a spustit) nebo kombinace kláves **command + r**.

   ![Spuštění projektu](media/iot-hub-ios-swift-c2d/run-sample.png)

## <a name="get-the-iot-hub-connection-string"></a>Získání připojovacího řetězce centra IoT Hub

V tomto článku vytvoříte back-end službu pro posílání zpráv z cloudu na zařízení prostřednictvím služby IoT Hub, kterou jste vytvořili v tématu [odeslání telemetrie ze zařízení do služby IoT Hub](quickstart-send-telemetry-ios.md). K posílání zpráv z cloudu na zařízení potřebuje služba oprávnění **připojit** se. Ve výchozím nastavení se všechny IoT Hub vytvoří pomocí zásad sdíleného přístupu s názvem **Služba** , která toto oprávnění udělí.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="simulate-a-service-device"></a>Simulace zařízení služby

V této části simulujete druhé zařízení s iOS pomocí aplikace SWIFT, která posílá zprávy typu cloud-zařízení prostřednictvím centra IoT. Tato konfigurace je užitečná pro scénáře IoT, kde existuje jedno zařízení iPhone nebo iPad jako kontroler pro jiná zařízení s iOS připojená ke IoT Hub.

### <a name="install-cocoapods"></a>Instalace CocoaPods

CocoaPods spravují závislosti pro projekty iOS využívající knihovny třetích stran.

Přejděte do složky ukázek Azure IoT pro iOS, kterou jste si stáhli v části požadavky. Pak přejděte do projektu ukázkové služby:

```sh
cd quickstart/sample-service
```

Ujistěte se, že je XCode zavřené, a pak spuštěním následujícího příkazu nainstalujte CocoaPods deklarované v souboru **podfile**:

```sh
pod install
```

Kromě instalace požadovaných podů pro váš projekt příkaz k instalaci vytvořil také soubor pracovního prostoru XCode, který je předem nakonfigurovaný tak, aby používal pody pro závislosti.

### <a name="run-the-sample-service-application"></a>Spuštění ukázkové aplikace služby

1. Otevřete ukázkový pracovní prostor v XCode.

   ```sh
   open AzureIoTServiceSample.xcworkspace
   ```

2. Rozbalte projekt **AzureIoTServiceSample** a pak rozbalte složku se stejným názvem.  

3. Otevřete soubor **ViewController.swift** pro úpravy v XCode. 

4. Vyhledejte proměnnou **ConnectionString** a aktualizujte hodnotu pomocí připojovacího řetězce služby, který jste si dříve zkopírovali v [části získání připojovacího řetězce služby IoT Hub](#get-the-iot-hub-connection-string).

5. Uložte provedené změny.

6. V Xcode změňte nastavení emulátoru na jiné zařízení iOS, než jste použili ke spuštění zařízení IoT. XCode nemůže spustit více emulátorů stejného typu.

   ![Změna zařízení emulátoru](media/iot-hub-ios-swift-c2d/change-device.png)

7. Spusťte projekt v emulátoru zařízení pomocí tlačítka **Sestavit a spustit** nebo pomocí příkazu se seznamem kláves **+ r**.

   ![Spuštění projektu](media/iot-hub-ios-swift-c2d/run-app.png)

## <a name="send-a-cloud-to-device-message"></a>Odeslání zprávy typu cloud-zařízení

Nyní jste připraveni používat tyto dvě aplikace k posílání a přijímání zpráv z cloudu na zařízení.

1. V **ukázkové aplikaci aplikace pro iOS** běžící na simulovaném zařízení IoT klikněte na **Spustit**. Aplikace spouští odesílání zpráv ze zařízení do cloudu, ale také zahájí naslouchání zpráv z cloudu na zařízení.

   ![Zobrazit ukázkovou aplikaci IoT Device](media/iot-hub-ios-swift-c2d/view-d2c.png)

2. V **ukázkové aplikaci klienta služby IoTHub** spuštěné na simulovaném zařízení služby zadejte ID zařízení IoT, na které chcete poslat zprávu. 

3. Napište zprávu ve formátu prostého textu a pak klikněte na **Odeslat**.

    Po kliknutí na Odeslat dojde k několika akcím. Ukázka služby odešle zprávu do služby IoT Hub, ke které má aplikace přístup, protože jste zadali připojovací řetězec služby, který jste zadali. Vaše centrum IoT zkontroluje ID zařízení, odešle zprávu na cílové zařízení a pošle potvrzení potvrzení na zdrojové zařízení. Aplikace spuštěná na simulovaném zařízení IoT kontroluje zprávy od IoT Hub a tiskne text z posledního z nich na obrazovce.

    Výstup by měl vypadat jako v následujícím příkladu:

   ![Zobrazení zpráv z cloudu na zařízení](media/iot-hub-ios-swift-c2d/view-c2d.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak odesílat a přijímat zprávy z cloudu do zařízení.

Příklady kompletních řešení, která používají IoT Hub, najdete v dokumentaci k [akcelerátorům řešení Azure IoT](https://azure.microsoft.com/documentation/suites/iot-suite/) .

Další informace o vývoji řešení pomocí IoT Hub najdete v příručce pro [vývojáře IoT Hub](iot-hub-devguide.md).
