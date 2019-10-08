---
title: Začínáme s identitou modulu Azure IoT Hub a modulem bez vlákna (Node. js) | Microsoft Docs
description: Přečtěte si, jak vytvořit identitu modulu a aktualizovat modul s dvojitou identitou pomocí sad IoT SDK pro Node. js.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/26/2018
ms.openlocfilehash: e33987d382a2afd93644a413084339fcd330d2e2
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029717"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-nodejs"></a>Začínáme s identitou modulu IoT Hub a modulem bez vlákna (Node. js)

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [Identity modulů a vlákna modulu](iot-hub-devguide-module-twins.md) jsou podobné identitám zařízení IoT Hub a zařízení v Azure, ale poskytují jemnější členitost. I když se v Azure IoT Hub identitu zařízení a zařízení dokončí v back-endové aplikaci, aby se nakonfigurovalo zařízení a zajistilo viditelnost podmínek zařízení, identita modulu a modul, který je funkční, poskytuje tyto funkce pro jednotlivé komponenty zařízení. Na zařízeních s více součástmi, jako jsou zařízení založená na operačním systému nebo v zařízeních firmwaru, umožňují izolovanou konfiguraci a podmínky pro jednotlivé komponenty.

Na konci tohoto kurzu máte dvě aplikace Node. js:

* **CreateIdentities**, která vytvoří identitu zařízení, identitu modulu a přidruženou bezpečnostní klíč k připojení vašich zařízení a klientů modulů.

* **UpdateModuleTwinReportedProperties**, která posílá aktualizovanému modulu hlášené vlastnosti do IoT Hub.

> [!NOTE]
> Informace o sadách Azure IoT SDK, které můžete použít k vytváření aplikací pro spouštění na zařízeních a back-endu vašeho řešení, najdete v tématu sady [SDK Azure IoT](iot-hub-devguide-sdks.md).

## <a name="prerequisites"></a>Požadavky

* Node. js verze 10.0. x nebo novější. [Příprava vývojového prostředí](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) popisuje, jak nainstalovat Node. js pro tento kurz v systému Windows nebo Linux.

* Aktivní účet Azure. (Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) .)

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>Získání připojovacího řetězce centra IoT Hub

[!INCLUDE [iot-hub-howto-module-twin-shared-access-policy-text](../../includes/iot-hub-howto-module-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="create-a-device-identity-and-a-module-identity-in-iot-hub"></a>Vytvoření identity zařízení a identity modulu v IoT Hub

V této části vytvoříte aplikaci Node. js, která vytvoří identitu zařízení a identitu modulu v registru identit ve službě IoT Hub. Zařízení nebo modul se nemůže připojit ke službě IoT Hub, pokud neobsahuje záznam v registru identit. Další informace najdete v části registr identit v [příručce pro vývojáře IoT Hub](iot-hub-devguide-identity-registry.md). Když spustíte tuto konzolovou aplikaci, vygeneruje se jedinečné ID a klíč pro zařízení i modul. Zařízení a modul používají tyto hodnoty k tomu, aby je identifikovaly, když odesílají zprávy ze zařízení do cloudu do IoT Hub. V ID se rozlišují malá a velká písmena.

1. Vytvořte adresář pro uložení kódu.

2. V tomto adresáři Nejdřív spusťte **npm init-y** , aby se vytvořil prázdný soubor Package. JSON s výchozími hodnotami. Toto je soubor projektu pro váš kód.

3. Spusťte **npm Install-S Azure-iothub @ no__t-1modules-Preview** a nainstalujte sadu SDK služby do podadresáře **node_modules** .

    > [!NOTE]
    > Název podadresáře node_modules používá modul aplikace Word k označení "knihovna uzlů". Doba, kterou tady dělá, nemůže dělat IoT Hub moduly.

4. Vytvořte v adresáři následující soubor. js. Zavolejte ho **Add. js**. Zkopírujte a vložte připojovací řetězec a název centra.

    ```javascript
    var Registry = require('azure-iothub').Registry;
    var uuid = require('uuid');
    // Copy/paste your connection string and hub name here
    var serviceConnectionString = '<hub connection string from portal>';
    var hubName = '<hub name>.azure-devices.net';
    // Create an instance of the IoTHub registry
    var registry = Registry.fromConnectionString(serviceConnectionString);
    // Insert your device ID and moduleId here.
    var deviceId = 'myFirstDevice';
    var moduleId = 'myFirstModule';
    // Create your device as a SAS authentication device
    var primaryKey = new Buffer(uuid.v4()).toString('base64');
    var secondaryKey = new Buffer(uuid.v4()).toString('base64');
    var deviceDescription = {
      deviceId: deviceId,
      status: 'enabled',
      authentication: {
        type: 'sas',
        symmetricKey: {
          primaryKey: primaryKey,
          secondaryKey: secondaryKey
        }
      }
    };

    // First, create a device identity
    registry.create(deviceDescription, function(err) {
      if (err) {
        console.log('Error creating device identity: ' + err);
        process.exit(1);
      }
      console.log('device connection string = "HostName=' + hubName + ';DeviceId=' + deviceId + ';SharedAccessKey=' + primaryKey + '"');

      // Then add a module to that device
      registry.addModule({ deviceId: deviceId, moduleId: moduleId }, function(err) {
        if (err) {
          console.log('Error creating module identity: ' + err);
          process.exit(1);
        }

        // Finally, retrieve the module details from the hub so we can construct the connection string
        registry.getModule(deviceId, moduleId, function(err, foundModule) {
          if (err) {
            console.log('Error getting module back from hub: ' + err);
            process.exit(1);
          }
          console.log('module connection string = "HostName=' + hubName + ';DeviceId=' + foundModule.deviceId + ';ModuleId='+foundModule.moduleId+';SharedAccessKey=' + foundModule.authentication.symmetricKey.primaryKey + '"');
          process.exit(0);
        });
      });
    });

    ```

Tato aplikace vytvoří identitu zařízení s ID **myFirstDevice** a identitou modulu s ID **MyFirstModule** v části **myFirstDevice**zařízení. (Pokud toto ID modulu již v registru identit existuje, kód jednoduše načte informace o stávajícím modulu.) Aplikace pak zobrazí primární klíč pro tuto identitu. Tento klíč použijete v aplikaci simulovaného modulu pro připojení ke službě IoT Hub.

Spusťte tento příkaz pomocí node Add. js. Poskytne vám připojovací řetězec pro identitu zařízení a další pro identitu vašeho modulu.

> [!NOTE]
> Registr identit IoT Hub ukládá pouze identity zařízení a modulů, aby bylo možné povolit zabezpečený přístup ke službě IoT Hub. Registr identit ukládá ID zařízení a klíče, které se mají použít jako přihlašovací údaje zabezpečení. Registr identit také ukládá povolený nebo zakázaný příznak pro každé zařízení, které můžete použít k zakázání přístupu k tomuto zařízení. Pokud vaše aplikace potřebuje Uložit jiná metadata specifická pro zařízení, měla by používat úložiště pro konkrétní aplikaci. Pro identity modulu není povolený nebo zakázaný příznak. Další informace najdete v tématu [IoT Hub příručka pro vývojáře](iot-hub-devguide-identity-registry.md).

## <a name="update-the-module-twin-using-nodejs-device-sdk"></a>Aktualizujte modul s dvojím použitím sady SDK pro zařízení Node. js.

V této části vytvoříte aplikaci Node. js na simulovaném zařízení, které aktualizuje nedokončené hlášené vlastnosti modulu.

1. **Získání připojovacího řetězce modulu** – Přihlaste se k [Azure Portal](https://portal.azure.com/). Přejděte do IoT Hub a klikněte na zařízení IoT. Najděte myFirstDevice, otevřete ho a uvidíte, že myFirstModule se úspěšně vytvořil. Zkopírujte připojovací řetězec modulu. V dalším kroku je potřeba.

   ![Podrobnosti o modulu Azure Portal](./media/iot-hub-node-node-module-twin-getstarted/module-detail.png)

2. Podobně jako v předchozím kroku vytvoříte adresář pro váš kód zařízení a pomocí NPM ho inicializujete a nainstalujete sadu SDK pro zařízení (**npm Install-S Azure-IoT-Device-AMQP @ no__t-1modules-Preview**).

   > [!NOTE]
   > Příkaz pro instalaci npm se může považovat za pomalý. Je to pacient, který z úložiště balíčků vybírá spoustu kódu.

   > [!NOTE]
   > Pokud se zobrazí chyba, která říká npm ERR! při analýze formátu JSON došlo k chybě registru, je bezpečné ho ignorovat. Pokud se zobrazí chyba, která říká npm ERR! při analýze formátu JSON došlo k chybě registru, je bezpečné ho ignorovat.

3. Vytvořte soubor s názvem "vláken. js". Zkopírujte řetězec identity modulu a vložte ho do něj.

    ```javascript
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-amqp').Amqp;
    // Copy/paste your module connection string here.
    var connectionString = '<insert module connection string here>';
    // Create a client using the Amqp protocol.
    var client = Client.fromConnectionString(connectionString, Protocol);
    client.on('error', function (err) {
      console.error(err.message);
    });
    // connect to the hub
    client.open(function(err) {
      if (err) {
        console.error('error connecting to hub: ' + err);
        process.exit(1);
      }
      console.log('client opened');
    // Create device Twin
      client.getTwin(function(err, twin) {
        if (err) {
          console.error('error getting twin: ' + err);
          process.exit(1);
        }
        // Output the current properties
        console.log('twin contents:');
        console.log(twin.properties);
        // Add a handler for desired property changes
        twin.on('properties.desired', function(delta) {
            console.log('new desired properties received:');
            console.log(JSON.stringify(delta));
        });
        // create a patch to send to the hub
        var patch = {
          updateTime: new Date().toString(),
          firmwareVersion:'1.2.1',
          weather:{
            temperature: 72,
            humidity: 17
          }
        };
        // send the patch
        twin.properties.reported.update(patch, function(err) {
          if (err) throw err;
          console.log('twin state reported');
        });
      });
    });
    ```

4. Nyní jej spusťte pomocí příkazového řádku s **dvojitou příponou. js**.

   ```cmd/sh
   F:\temp\module_twin>node twin.js
   ```

   Pak se zobrazí:

   ```console
   client opened
   twin contents:
   { reported: { update: [Function: update], '$version': 1 },
     desired: { '$version': 1 } }
   new desired properties received:
   {"$version":1}
   twin state reported
   ```

## <a name="next-steps"></a>Další kroky

Pokud chcete pokračovat v seznámení se IoT Hub a prozkoumat další scénáře IoT, podívejte se na:

* [Začínáme se správou zařízení](iot-hub-node-node-device-management-get-started.md)

* [Začínáme s IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)