---
title: Začínáme s identitou modulu Azure IoT Hub identity & modulu s dvojitou identitou (Node.js)
description: Přečtěte si, jak vytvořit identitu modulu a aktualizovat modul na vlákna pomocí sad IoT SDK pro Node.js.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/26/2018
ms.custom: amqp, devx-track-js
ms.openlocfilehash: 9de3f45a9d62a9d131583c133440ac8a311a468d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "95993274"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-nodejs"></a>Začínáme s identitou modulu IoT Hub a modulem s dvojitou identitou (Node.js)

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [Identity modulů a dvojčata modulů](iot-hub-devguide-module-twins.md) se podobají identitě zařízení a dvojčeti zařízení služby Azure IoT Hub, ale poskytují větší úroveň členitosti. I když se v Azure IoT Hub identitu zařízení a zařízení dokončí v back-endové aplikaci, aby se nakonfigurovalo zařízení a zajistilo viditelnost podmínek zařízení, identita modulu a modul, který je funkční, poskytuje tyto funkce pro jednotlivé komponenty zařízení. Na způsobilých zařízeních s několika součástmi, jako jsou zařízení s operačním systémem nebo zařízení s firmwarem, to umožňuje izolovanou konfiguraci a vhled do stavu jednotlivých součástí.

Na konci tohoto kurzu máte dvě Node.js aplikace:

* Aplikaci **CreateIdentities**, která vytvoří identitu zařízení, identitu modulu a přidružený klíč zabezpečení pro připojení klientů zařízení a modulu.

* Aplikaci **UpdateModuleTwinReportedProperties**, která do služby IoT Hub odešle aktualizované hlášené vlastnosti dvojčete modulu.

> [!NOTE]
> Informace o sadách SDK služby Azure IoT Hub, s jejichž pomocí můžete sestavit aplikace, které poběží v zařízení, i back-end vašeho řešení, najdete v tématu [Sady SDK služby IoT Hub](iot-hub-devguide-sdks.md).

## <a name="prerequisites"></a>Požadavky

* Node.js verze 10.0. x nebo novější. [Příprava vývojového prostředí](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) popisuje, jak nainstalovat Node.js pro tento kurz v systému Windows nebo Linux.

* Aktivní účet Azure. (Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) .)

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>Získání připojovacího řetězce centra IoT Hub

[!INCLUDE [iot-hub-howto-module-twin-shared-access-policy-text](../../includes/iot-hub-howto-module-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="create-a-device-identity-and-a-module-identity-in-iot-hub"></a>Vytvoření identity zařízení a identity modulu v IoT Hub

V této části vytvoříte aplikaci Node.js, která v registru identit ve službě IoT Hub vytvoří identitu zařízení a identitu modulu. Zařízení nebo modul je možné připojit k centru IoT, pouze pokud má záznam v registru identit. Další informace najdete v části registr identit v [příručce pro vývojáře IoT Hub](iot-hub-devguide-identity-registry.md). Když spustíte tuto konzolovou aplikaci, vygeneruje jedinečné ID a klíč zařízení i modulu. Vaše zařízení a modul použijí tyto hodnoty k vlastní identifikaci při odesílání zpráv typu zařízení-cloud do služby IoT Hub. V ID se rozlišují malá a velká písmena.

1. Vytvořte adresář pro uložení kódu.

2. V tomto adresáři Nejdřív spusťte **npm init-y** , aby se ve výchozím nastavení vytvořily prázdné package.js. Toto je soubor projektu pro váš kód.

3. Spuštěním **npm Install-S Azure-iothub \@ modules-Preview** nainstalujte sadu SDK služby do podadresáře **node_modules** .

    > [!NOTE]
    > Název podadresáře node_modules používá modul Word k označení "knihovna uzlů". Doba, kterou tady dělá, nemůže dělat IoT Hub moduly.

4. Vytvořte v adresáři následující soubor. js. Zavolejte **add.js**. Zkopírujte a vložte připojovací řetězec a název centra.

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

Tato aplikace vytvoří identitu zařízení s ID **myFirstDevice** a identitou modulu s ID **MyFirstModule** v části **myFirstDevice** zařízení. (Pokud toto ID modulu již v registru identit existuje, kód jednoduše načte informace o stávajícím modulu.) Aplikace pak zobrazí primární klíč pro tuto identitu. Tento klíč v aplikaci simulovaného modulu slouží k připojení k centru IoT.

Tuto rutinu spusťte pomocí add.js uzlů. Poskytne vám připojovací řetězec pro identitu zařízení a další pro identitu vašeho modulu.

> [!NOTE]
> V registru identit služby IoT Hub se uchovávají identity zařízení a modulů pouze za účelem bezpečného přístupu k centru IoT. Registr identit ukládá ID zařízení a klíče pro použití jako bezpečnostních pověření. Registr identit také ukládá povolené a zakázané příznaky pro jednotlivá zařízení, pomocí kterých můžete zakázat přístup pro dané zařízení. Pokud aplikace potřebuje pro zařízení ukládat další metadata, měla by používat úložiště pro konkrétní aplikaci. Pro identity modulů neexistuje žádný příznak povoleno/zakázáno. Další informace najdete v [Příručce pro vývojáře pro službu IoT Hub](iot-hub-devguide-identity-registry.md).

## <a name="update-the-module-twin-using-nodejs-device-sdk"></a>Aktualizujte modul s dvojitou sadou pomocí sady Node.js SDK pro zařízení

V této části vytvoříte Node.jsovou aplikaci na simulovaném zařízení, které aktualizuje nedokončené hlášené vlastnosti modulu.

1. **Získání připojovacího řetězce modulu** – Přihlaste se k [Azure Portal](https://portal.azure.com/). Přejděte do vaší služby IoT Hub a klikněte na Zařízení IoT. Najděte myFirstDevice, otevřete ho a uvidíte, že myFirstModule se úspěšně vytvořil. Zkopírujte připojovací řetězec modulu. Budete ho potřebovat v dalším kroku.

   ![Podrobnosti o modulu na webu Azure Portal](./media/iot-hub-node-node-module-twin-getstarted/module-detail.png)

2. Podobně jako v předchozím kroku vytvoříte adresář pro váš kód zařízení a pomocí NPM ho inicializujete a nainstalujete sadu SDK pro zařízení (**npm Install-S Azure-IoT-Device-AMQP \@ modules-Preview**).

   > [!NOTE]
   > Příkaz pro instalaci npm se může považovat za pomalý. Je to pacient, který z úložiště balíčků vybírá spoustu kódu.

   > [!NOTE]
   > Pokud se zobrazí chyba, která říká npm ERR! při analýze formátu JSON došlo k chybě registru, je bezpečné ho ignorovat. Pokud se zobrazí chyba, která říká npm ERR! při analýze formátu JSON došlo k chybě registru, je bezpečné ho ignorovat.

3. Vytvořte soubor s názvem twin.js. Zkopírujte řetězec identity modulu a vložte ho do něj.

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

4. Nyní spusťte tento příkaz pomocí **twin.jsho uzlu**.

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

Chcete-li pokračovat v seznamování se službou IoT Hub a prozkoumat další scénáře IoT, podívejte se na tato témata:

* [Začínáme se správou zařízení](iot-hub-node-node-device-management-get-started.md)

* [Začínáme se službou IoT Edge](../iot-edge/quickstart-linux.md)