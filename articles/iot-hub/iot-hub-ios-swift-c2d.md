---
title: Zprávy z cloudu na zařízení pomocí služby Azure IoT Hub (iOS) | Dokumenty společnosti Microsoft
description: Jak odesílat zprávy z cloudu na zařízení z centra Azure IoT pomocí sad Azure IoT SDK pro iOS.
author: kgremban
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/19/2018
ms.author: kgremban
ms.openlocfilehash: 544b1108a3c79da50969f7fdd33cfb9af373d854
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110865"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-ios"></a>Odesílání zpráv z cloudu na zařízení pomocí služby IoT Hub (iOS)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub je plně spravovaná služba, která pomáhá umožnit spolehlivou a zabezpečenou obousměrnou komunikaci mezi miliony zařízení a back-endem řešení. [Odeslat telemetrii ze zařízení do služby IoT hub](quickstart-send-telemetry-ios.md) rychlý start ukazuje, jak vytvořit službu IoT hub, zřízení identity zařízení v něm a kód simulované zařízení aplikace, která odesílá zprávy zařízení cloud.

V tomto kurzu získáte informace o následujících postupech:

* Z back-endu vašeho řešení můžete odesílat zprávy z cloudu na zařízení do jednoho zařízení prostřednictvím služby IoT Hub.

* Příjem zpráv z cloudu na zařízení na zařízení.

* Z back-endu vašeho řešení požádejte o potvrzení doručení *(zpětnou vazbu)* pro zprávy odeslané do zařízení ze služby IoT Hub.

Další informace o zprávách mezi cloudy najdete v [části zasílání zpráv v průvodci vývojáři služby IoT Hub](iot-hub-devguide-messaging.md).

Na konci tohoto článku spustíte dva projekty Swift iOS:

* **ukázkové zařízení**, stejná aplikace vytvořená v [aplikaci Send telemetrie ze zařízení do centra IoT hub](quickstart-send-telemetry-ios.md), které se připojuje k centru IoT hub a přijímá zprávy z cloudu na zařízení.

* **ukázková služba**, která odešle zprávu z cloudu na zařízení do aplikace simulovaných zařízení prostřednictvím služby IoT Hub a poté obdrží potvrzení o doručení.

> [!NOTE]
> IoT Hub má podporu sady SDK pro mnoho platforem a jazyků zařízení (včetně C, Java, Pythonu a Javascriptu) prostřednictvím sad SDK zařízení Azure IoT. Podrobné pokyny, jak připojit zařízení ke kódu tohoto kurzu a obecně k azure iot hubu, najdete v [tématu Azure IoT Developer Center](https://www.azure.com/develop/iot).

## <a name="prerequisites"></a>Požadavky

* Aktivní účet Azure. (Pokud nemáte účet, můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) během několika minut.)

* Aktivní centrum IoT v Azure.

* Ukázka kódu z [ukázek Azure](https://github.com/Azure-Samples/azure-iot-samples-ios/archive/master.zip).

* Nejnovější verze [XCode](https://developer.apple.com/xcode/) používající nejnovější verzi sady SDK pro iOS. Tento rychlý start byl testován s XCode 9.3 a iOS 11.3.

* Nejnovější verze [CocoaPods](https://guides.cocoapods.org/using/getting-started.html).

* Zkontrolujte, zda je v bráně firewall otevřený port 8883. Ukázka zařízení v tomto článku používá protokol MQTT, který komunikuje přes port 8883. Tento port může být blokován v některých prostředích podnikové a vzdělávací sítě. Další informace a způsoby, jak tento problém vyřešit, najdete [v tématu připojení k centru IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="simulate-an-iot-device"></a>Simulace zařízení IoT

V této části simulujete zařízení se systémem iOS s aplikací Swift pro příjem zpráv z cloudu zařízení z centra IoT Hub. 

Toto je ukázkové zařízení, které vytvoříte v článku [Odeslat telemetrii ze zařízení do služby IoT hub](quickstart-send-telemetry-ios.md). Pokud již tento běh máte, můžete tuto část přeskočit.

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

1. Načtěte připojovací řetězec pro vaše zařízení. Tento řetězec můžete zkopírovat z [portálu Azure](https://portal.azure.com) v okně podrobností o zařízení nebo jej načíst pomocí následujícího příkazu příkazu příkazu příkazu příkazu:

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id {YourDeviceID} --output table
    ```

1. Otevřete ukázkový pracovní prostor v XCode.

   ```sh
   open "MQTT Client Sample.xcworkspace"
   ```

2. Rozbalte projekt **Ukázkový klient MQTT** a potom složku se stejným názvem.  

3. Otevřete soubor **ViewController.swift** pro úpravy v XCode. 

4. Vyhledejte proměnnou **connectionString** a aktualizujte hodnotu připojovacím řetězcem zařízení, který jste zkopírovali v prvním kroku.

5. Uložte provedené změny. 

6. Spusťte projekt v emulátoru zařízení pomocí tlačítka **Build and run** (Sestavit a spustit) nebo kombinace kláves **command + r**.

   ![Spuštění projektu](media/iot-hub-ios-swift-c2d/run-sample.png)

## <a name="get-the-iot-hub-connection-string"></a>Získání připojovacího řetězce centra IoT hub

V tomto článku vytvoříte back-endovou službu pro odesílání zpráv mezi cloudy prostřednictvím služby IoT hub, kterou jste vytvořili v [aplikaci Send telemetrie ze zařízení do služby IoT hub](quickstart-send-telemetry-ios.md). K odesílání zpráv z cloudu na zařízení potřebuje vaše služba oprávnění **k připojení služby.** Ve výchozím nastavení je každé centrum IoT Hub vytvořeno pomocí zásady sdíleného přístupu s názvem **služba,** která uděluje toto oprávnění.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="simulate-a-service-device"></a>Simulace servisního zařízení

V této části simulujete druhé iOS zařízení pomocí aplikace Swift, která odesílá zprávy z cloudu na zařízení prostřednictvím služby IoT hub. Tato konfigurace je užitečná pro scénáře IoT, kde jeden iPhone nebo iPad funguje jako ovladač pro ostatní zařízení iOS připojená k centru IoT.

### <a name="install-cocoapods"></a>Instalace CocoaPods

CocoaPods spravují závislosti pro projekty iOS využívající knihovny třetích stran.

Přejděte do složky Ukázky Azure IoT iOS, kterou jste stáhli v požadavcích. Potom přejděte na ukázkový projekt služby:

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

2. Rozbalte projekt **AzureIoTServiceSample** a potom rozbalte složku se stejným názvem.  

3. Otevřete soubor **ViewController.swift** pro úpravy v XCode. 

4. Vyhledejte proměnnou **connectionString** a aktualizujte hodnotu připojovacím řetězcem služby, který jste dříve zkopírovali v [aplikaci Získat připojovací řetězec centra IoT](#get-the-iot-hub-connection-string).

5. Uložte provedené změny.

6. V Xcode změňte nastavení emulátoru na jiné zařízení iOS, než jste použili ke spuštění zařízení IoT. XCode nelze spustit více emulátorů stejného typu.

   ![Změna zařízení emulátoru](media/iot-hub-ios-swift-c2d/change-device.png)

7. Spusťte projekt v emulátoru zařízení pomocí tlačítka **Sestavení a spuštění** nebo příkazu key combo Command + **r**.

   ![Spuštění projektu](media/iot-hub-ios-swift-c2d/run-app.png)

## <a name="send-a-cloud-to-device-message"></a>Odeslání zprávy z cloudu na zařízení

Nyní jste připraveni použít dvě aplikace k odesílání a přijímání zpráv z cloudu na zařízení.

1. V aplikaci **Ukázka aplikace pro iOS** spuštěná na simulovaném zařízení IoT klikněte na **start**. Aplikace spustí odesílání zpráv mezi zařízeními, ale také začne naslouchat zprávám z cloudu na zařízení.

   ![Zobrazení ukázkové aplikace zařízení IoT](media/iot-hub-ios-swift-c2d/view-d2c.png)

2. V ukázkové aplikaci **klienta služby IoTHub** spuštěné na simulovaném servisním zařízení zadejte ID pro zařízení IoT, kterému chcete odeslat zprávu. 

3. Napište zprávu ve formátu prostého textu a klepněte na tlačítko **Odeslat**.

    Několik akcí se stane, jakmile kliknete na odeslat. Ukázka služby odešle zprávu do služby IoT hub, ke kterému má aplikace přístup z důvodu zadanýho připojovacího řetězce služby. Vaše služba IoT hub zkontroluje ID zařízení, odešle zprávu cílovému zařízení a odešle potvrzení o přijetí do zdrojového zařízení. Aplikace spuštěná na simulovaném zařízení IoT kontroluje zprávy z IoT Hubu a tiskne text z posledního na obrazovce.

    Výstup by měl vypadat jako následující příklad:

   ![Zobrazení zpráv mezi cloudy](media/iot-hub-ios-swift-c2d/view-c2d.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili, jak odesílat a přijímat zprávy z cloudu na zařízení.

Příklady kompletních komplexních řešení, která používají službu IoT Hub, najdete v dokumentaci [k akcelerátorům řešení Azure IoT.](https://azure.microsoft.com/documentation/suites/iot-suite/)

Další informace o vývoji řešení pomocí služby IoT Hub najdete v [průvodci vývojáři služby IoT Hub](iot-hub-devguide.md).
