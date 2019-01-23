---
title: Začínáme s Azure IoT Hub identit a modul dvojče zařízení (Node.js) | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit modul identity a aktualizovat dvojče zařízení pomocí sad IoT SDK pro Node.js.
author: chrissie926
manager: ''
ms.service: iot-hub
services: iot-hub
ms.devlang: node
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: menchi
ms.openlocfilehash: ae798ecf2ba5f53cdfdef45b74677615d2e0a06d
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54446999"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-nodejs-back-end-and-nodejs-device"></a>Začínáme s IoT Hub identit a modul dvojče zařízení pomocí Node.js zařízení a back-end Node.js

> [!NOTE]
> [Identity modulů a dvojčata modulů](iot-hub-devguide-module-twins.md) se podobají identitě zařízení a dvojčeti zařízení služby Azure IoT Hub, ale poskytují větší úroveň členitosti. Zatímco identita zařízení a dvojče zařízení služby Azure IoT Hub umožňují back-endové aplikaci konfigurovat zařízení a poskytují vhled do stavu zařízení, identita modulu a dvojče modulu poskytují tyto možnosti pro jednotlivé součásti zařízení. Na způsobilých zařízeních s několika součástmi, jako jsou zařízení s operačním systémem nebo zařízení s firmwarem, to umožňuje izolovanou konfiguraci a vhled do stavu jednotlivých součástí.

Na konci tohoto kurzu budete mít dvě aplikace Node.js:

* Aplikaci **CreateIdentities**, která vytvoří identitu zařízení, identitu modulu a přidružený klíč zabezpečení pro připojení klientů zařízení a modulu.
* Aplikaci **UpdateModuleTwinReportedProperties**, která do služby IoT Hub odešle aktualizované hlášené vlastnosti dvojčete modulu.

> [!NOTE]
> Informace o sadách SDK služby Azure IoT Hub, s jejichž pomocí můžete sestavit aplikace, které poběží v zařízení, i back-end vašeho řešení, najdete v tématu [Sady SDK služby IoT Hub][lnk-hub-sdks].

Pro absolvování tohoto kurzu potřebujete:

* Aktivní účet Azure. (Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet][lnk-free-trial].)
* An IoT Hub.
* Nainstalujte nejnovější [sady Node.js SDK](https://github.com/Azure/azure-iot-sdk-node).

Nyní jste vytvořili službu IoT Hub a máte název hostitele a připojovací řetězec služby IoT Hub, které potřebujete k dokončení kurzu.

## <a name="create-a-device-identity-and-a-module-identity-in-iot-hub"></a>Vytvoření identity zařízení a modul identity ve službě IoT Hub

V této části vytvoříte aplikaci Node.js, která v registru identit ve službě IoT hub vytvoří identitu zařízení a modul identity. Zařízení nebo modul je možné připojit k centru IoT, pouze pokud má záznam v registru identit. Další informace najdete v části Registr identit v [Příručce pro vývojáře pro službu IoT Hub][lnk-devguide-identity]. Když spustíte tuto konzolovou aplikaci, vygeneruje jedinečné ID a klíč zařízení i modulu. Vaše zařízení a modul použijí tyto hodnoty k vlastní identifikaci při odesílání zpráv typu zařízení-cloud do služby IoT Hub. V ID se rozlišují malá a velká písmena.

1. Vytvořte adresář k uložení kódu.
2. V tomto adresáři při prvním spuštění **npm init -y** vytvořte prázdný soubor package.json pomocí výchozích hodnot. Toto je soubor projektu pro svůj kód.
3. Spustit **npm nainstalujte -S azure-iothub@modules-preview ** instalace sady SDK služby uvnitř **node_modules** podadresáře.

    > [!NOTE]
    > Node_modules název podadresáře používá modul slovo znamená "knihovna uzel". Termín zde nemá nic společného s moduly služby IoT Hub.

4. Ve vašem adresáři vytvořte následující soubor .js. Pojmenujte ji **add.js**. Zkopírujte a vložte připojovací řetězec centra a název centra.

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

Tato aplikace vytvoří identitu zařízení s ID **myFirstDevice** a identity modul s ID **myFirstModule** zařízení **myFirstDevice**. (Pokud toto ID modulu již v registru identit existuje, kód jednoduše načte informace o stávajícím modulu.) Aplikace pak zobrazí primární klíč pro danou identitu. Tento klíč v aplikaci simulovaného modulu slouží k připojení k centru IoT.

5. Spusťte pomocí add.js uzlu. Poskytne vám připojovací řetězec pro vaši identitu zařízení a druhou pro svoji identitu modulu.

    > [!NOTE]
    > V registru identit služby IoT Hub se uchovávají identity zařízení a modulů pouze za účelem bezpečného přístupu k centru IoT. Registr identit ukládá ID zařízení a klíče pro použití jako bezpečnostních pověření. Registr identit také ukládá povolené a zakázané příznaky pro jednotlivá zařízení, pomocí kterých můžete zakázat přístup pro dané zařízení. Pokud aplikace potřebuje pro zařízení ukládat další metadata, měla by používat úložiště pro konkrétní aplikaci. Pro identity modulů neexistuje žádný příznak povoleno/zakázáno. Další informace najdete v [Příručce pro vývojáře pro službu IoT Hub][lnk-devguide-identity].

## <a name="update-the-module-twin-using-nodejs-device-sdk"></a>Aktualizovat dvojče zařízení Node.js SDK

V této části vytvoříte Node.js aplikace na zařízení s Simulovaná, která aktualizuje dvojčete modulu ohlášené vlastnosti.

1. **Získání připojovacího řetězce modulu** – Teď se přihlaste k webu [Azure Portal][lnk-portal]. Přejděte do vaší služby IoT Hub a klikněte na Zařízení IoT. Najít myFirstDevice, otevřít a zobrazit myFirstModule byl úspěšně vytvořen. Zkopírujte připojovací řetězec modulu. Budete ho potřebovat v dalším kroku.

    ![Podrobnosti o modulu na webu Azure Portal][15]

2. . Podobá se vám to udělali v předchozím kroku, vytvořte adresář pro kód vašeho zařízení a inicializujte ho a nainstalujte sady SDK pro zařízení pomocí NPM (**npm nainstalujte -S azure-iot-device-amqp@modules-preview** ).

    > [!NOTE]
    > Mohou mít pocit instalačního příkazu npm pomalé. Buďte prosím trpěliví. to je potažením dolů velké množství kódu z úložiště balíčků.

    > [!NOTE]
    > Pokud se zobrazí chybová zpráva npm ERR! Chyba parsování json registru, toto je bezpečně ignorovat. Pokud se zobrazí chybová zpráva npm ERR! Chyba parsování json registru, toto je bezpečně ignorovat.

3. Vytvořte soubor s názvem twin.js. Zkopírujte a vložte řetězec identity vašeho modulu.

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

2. Nyní, spustit pomocí příkazu **uzel twin.js**.

    ```
    F:\temp\module_twin>node twin.js
    client opened
    twin contents:
    { reported: { update: [Function: update], '$version': 1 },
      desired: { '$version': 1 } }
    new desired properties received:
    {"$version":1}
    twin state reported
    ```

## <a name="next-steps"></a>Další postup

Chcete-li pokračovat v seznamování se službou IoT Hub a prozkoumat další scénáře IoT, podívejte se na tato témata:

* [Začínáme se správou zařízení][lnk-device-management]
* [Začínáme se službou IoT Edge][lnk-iot-edge]

<!-- Images. -->
[15]: ./media/iot-hub-csharp-csharp-module-twin-getstarted/module-detail.JPG
<!-- Links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/