---
title: Kurz – připojení Obecné klientské aplikace Node.js k Azure IoT Central | Microsoft Docs
description: V tomto kurzu se dozvíte, jak jako vývojář zařízení připojit zařízení s Node.js klientskou aplikací k vaší aplikaci Azure IoT Central. Automaticky vytvořenou šablonu zařízení upravíte přidáním zobrazení, která operátorovi umožní interakci s připojeným zařízením.
author: dominicbetts
ms.author: dobett
ms.date: 11/03/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom:
- mqtt
- device-developer
- devx-track-js
ms.openlocfilehash: 6175be702f0824ad2cd2b146e96641037407ca0b
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "96018796"
---
# <a name="tutorial-create-and-connect-a-client-application-to-your-azure-iot-central-application-nodejs"></a>Kurz: Vytvoření klientské aplikace a její připojení k aplikaci Azure IoT Central (Node.js)

[!INCLUDE [iot-central-selector-tutorial-connect](../../../includes/iot-central-selector-tutorial-connect.md)]

*Tento článek se týká tvůrců řešení a vývojářů zařízení.*

V tomto kurzu se dozvíte, jak jako vývojář pro zařízení připojit klienta aplikace Node.js k aplikaci Azure IoT Central. Node.js aplikace simuluje chování termostatového zařízení. Když se aplikace připojí k IoT Central, pošle ID modelu modelu zařízení termostatu. IoT Central používá ID modelu k načtení modelu zařízení a pro vás vytvoří šablonu zařízení. Přidáte do šablony zařízení vlastní nastavení a zobrazení, která operátorovi umožní komunikovat se zařízením.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořte a spusťte kód zařízení Node.js a podívejte se, jak se připojit k vaší aplikaci IoT Central.
> * Zobrazit simulovanou telemetrii odeslanou ze zařízení.
> * Přidejte vlastní zobrazení do šablony zařízení.
> * Publikujte šablonu zařízení.
> * Pomocí zobrazení můžete spravovat vlastnosti zařízení.
> * Zavolejte příkaz pro řízení zařízení.

## <a name="prerequisites"></a>Požadavky

K dokončení kroků v tomto článku budete potřebovat následující:

* Aplikace Azure IoT Central vytvořená pomocí šablony **vlastní aplikace** . Další informace najdete v [rychlém startu k vytvoření aplikace](quick-deploy-iot-central.md). Aplikace musí být vytvořená na základě 14. července 2020 nebo po ní.
* Vývojový počítač s nainstalovanou verzí [Node.js](https://nodejs.org/) verze 6 nebo novější. Můžete spustit na `node --version` příkazovém řádku a ověřit svou verzi. Pokyny v tomto kurzu předpokládají, že jste spustili příkaz **Node** na příkazovém řádku Windows. Můžete však použít Node.js v mnoha dalších operačních systémech.
* Místní kopie [Microsoft Azure IoT SDK pro Node.js](https://github.com/Azure/azure-iot-sdk-node) úložiště GitHub, která obsahuje vzorový kód. Pomocí tohoto odkazu si stáhněte kopii úložiště: [Stáhnout ZIP](https://github.com/Azure/azure-iot-sdk-node/archive/master.zip). Pak soubor rozbalte do vhodného umístění v místním počítači.

## <a name="review-the-code"></a>Kontrola kódu

V rámci kopie Microsoft Azure IoT SDK pro Node.js, kterou jste předtím stáhli, otevřete soubor *Azure-IoT-SDK-Node/Device/Samples/PNP/simple_thermostat.js* v textovém editoru.

Když spustíte ukázku pro připojení k IoT Central, používá službu Device Provisioning (DPS) k registraci zařízení a vygenerování připojovacího řetězce. Ukázka načte informace o připojení DPS, které potřebuje z prostředí příkazového řádku.

`main`Metoda:

* Vytvoří `client` objekt a `dtmi:com:example:Thermostat;1` před otevřením připojení nastaví ID modelu.
* Vytvoří obslužnou rutinu příkazu.
* Spustí smyčku, která každých 10 sekund posílá telemetrii o teplotě.
* Odešle `maxTempSinceLastReboot` vlastnost do IoT Central. IoT Central ignoruje `serialNumber` vlastnost, protože není součástí modelu zařízení.
* Vytvoří obslužnou rutinu vlastností s možností zápisu.

```javascript
async function main() {

  // ...

  // fromConnectionString must specify a transport, coming from any transport package.
  const client = Client.fromConnectionString(deviceConnectionString, Protocol);

  let resultTwin;
  try {
    // Add the modelId here
    await client.setOptions(modelIdObject);
    await client.open();

    client.onDeviceMethod(commandMaxMinReport, commandHandler);

    // Send Telemetry every 10 secs
    let index = 0;
    intervalToken = setInterval(() => {
      sendTelemetry(client, index).catch((err) => console.log('error', err.toString()));
      index += 1;
    }, telemetrySendInterval);

    // attach a standard input exit listener
    attachExitHandler(client);

    // Deal with twin
    try {
      resultTwin = await client.getTwin();
      const patchRoot = createReportPropPatch({ serialNumber: deviceSerialNum });
      const patchThermostat = createReportPropPatch({
        maxTempSinceLastReboot: deviceTemperatureSensor.getMaxTemperatureValue()
      });

      // the below things can only happen once the twin is there
      updateComponentReportedProperties(resultTwin, patchRoot);
      updateComponentReportedProperties(resultTwin, patchThermostat);

      // Setup the handler for desired properties
      desiredPropertyPatchHandler(resultTwin);

    } catch (err) {
      console.error('could not retrieve twin or report twin properties\n' + err.toString());
    }
  } catch (err) {
    console.error('could not connect Plug and Play client or could not attach interval function for telemetry\n' + err.toString());
  }
}
```

`provisionDevice`Funkce ukazuje, jak zařízení používá DPS k registraci a připojení k IoT Central. Datová část zahrnuje ID modelu:

```javascript
async function provisionDevice(payload) {
  var provSecurityClient = new SymmetricKeySecurityClient(registrationId, symmetricKey);
  var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvProtocol(), provSecurityClient);

  if (!!(payload)) {
    provisioningClient.setProvisioningPayload(payload);
  }

  try {
    let result = await provisioningClient.register();
    deviceConnectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';SharedAccessKey=' + symmetricKey;
  } catch (err) {
    console.error("error registering device: " + err.toString());
  }
}
```

`sendTelemetry`Funkce ukazuje, jak zařízení odesílá telemetrii teploty do IoT Central. `getCurrentTemperatureObject`Metoda vrátí objekt, který vypadá `{ temperature: 45.6 }` takto:

```javascript
async function sendTelemetry(deviceClient, index) {
  console.log('Sending telemetry message %d...', index);
  const msg = new Message(
    JSON.stringify(
      deviceTemperatureSensor.updateSensor().getCurrentTemperatureObject()
    )
  );
  msg.contentType = 'application/json';
  msg.contentEncoding = 'utf-8';
  await deviceClient.sendEvent(msg);
}
```

`main`Metoda používá následující dvě metody k odeslání `maxTempSinceLastReboot` vlastnosti do IoT Central. `main`Metoda volá `createReportPropPatch` s objektem, který vypadá `{maxTempSinceLastReboot: 80.9}` takto:

```javascript
const createReportPropPatch = (propertiesToReport) => {
  let patch;
  patch = { };
  patch = propertiesToReport;
  return patch;
};

const updateComponentReportedProperties = (deviceTwin, patch) => {
  deviceTwin.properties.reported.update(patch, function (err) {
    if (err) throw err;
    console.log('Properties have been reported for component');
  });
};
```

`main`Metoda pomocí následujících dvou metod zpracovává aktualizace vlastnosti _cílové teploty_ s možností zápisu z IoT Central. Všimněte si, jak `propertyUpdateHandle` sestavit odpověď s verzí a stavovým kódem:

```javascript
const desiredPropertyPatchHandler = (deviceTwin) => {
  deviceTwin.on('properties.desired', (delta) => {
    const versionProperty = delta.$version;

    Object.entries(delta).forEach(([propertyName, propertyValue]) => {
      if (propertyName !== '$version') {
        propertyUpdateHandler(deviceTwin, propertyName, null, propertyValue, versionProperty);
      }
    });
  });
};

const propertyUpdateHandler = (deviceTwin, propertyName, reportedValue, desiredValue, version) => {
  console.log('Received an update for property: ' + propertyName + ' with value: ' + JSON.stringify(desiredValue));
  const patch = createReportPropPatch(
    { [propertyName]:
      {
        'value': desiredValue,
        'ac': 200,
        'ad': 'Successfully executed patch for ' + propertyName,
        'av': version
      }
    });
  updateComponentReportedProperties(deviceTwin, patch);
  console.log('updated the property');
};
```

`main`Metoda používá následující dvě metody pro zpracování volání `getMaxMinReport` příkazu. `getMaxMinReportObject`Metoda generuje sestavu jako objekt JSON:

```javascript
const commandHandler = async (request, response) => {
  switch (request.methodName) {
  case commandMaxMinReport: {
    console.log('MaxMinReport ' + request.payload);
    await sendCommandResponse(request, response, 200, deviceTemperatureSensor.getMaxMinReportObject());
    break;
  }
  default:
    await sendCommandResponse(request, response, 404, 'unknown method');
    break;
  }
};

const sendCommandResponse = async (request, response, status, payload) => {
  try {
    await response.send(status, payload);
    console.log('Response to method \'' + request.methodName +
              '\' sent successfully.' );
  } catch (err) {
    console.error('An error ocurred when sending a method response:\n' +
              err.toString());
  }
};
```

## <a name="get-connection-information"></a>Získání informací o připojení

[!INCLUDE [iot-central-connection-configuration](../../../includes/iot-central-connection-configuration.md)]

## <a name="run-the-code"></a>Spuštění kódu

Pokud chcete spustit ukázkovou aplikaci, otevřete prostředí příkazového řádku a přejděte do složky *Azure-IoT-SDK-Node/Device/Samples/PNP* , která obsahuje ukázkový soubor *simple_thermostat.js* .

[!INCLUDE [iot-central-connection-environment](../../../includes/iot-central-connection-environment.md)]

Nainstalujte požadované balíčky:

```cmd/sh
npm install
```

Spusťte ukázku:

```cmd/sh
node simple_thermostat.js
```

Následující výstup zobrazuje registraci zařízení a připojení k IoT Central. Ukázka pak `maxTempSinceLastReboot` před zahájením odesílání telemetrie vlastnost pošle:

```cmd/sh
registration succeeded
assigned hub=iotc-.......azure-devices.net
deviceId=sample-device-01
payload=undefined
Connecting using connection string HostName=iotc-........azure-devices.net;DeviceId=sample-device-01;SharedAccessKey=Ci....=
Enabling the commands on the client
Please enter q or Q to exit sample.
The following properties will be updated for root interface:
{ maxTempSinceLastReboot: 55.20309427428496 }
Properties have been reported for component
Sending telemetry message 0...
Sending telemetry message 1...
Sending telemetry message 2...
Sending telemetry message 3...
```

[!INCLUDE [iot-central-monitor-thermostat](../../../includes/iot-central-monitor-thermostat.md)]

Můžete zjistit, jak zařízení reaguje na příkazy a aktualizace vlastností:

```cmd/sh
MaxMinReport 2020-10-15T12:00:00.000Z
Response to method 'getMaxMinReport' sent successfully.

...

Received an update for property: targetTemperature with value: {"value":86.3}
The following properties will be updated for root interface:
{
  targetTemperature: {
    value: { value: 86.3 },
    ac: 200,
    ad: 'Successfully executed patch for targetTemperature',
    av: 2
  }
}
```

## <a name="view-raw-data"></a>Zobrazit nezpracovaná data

[!INCLUDE [iot-central-monitor-thermostat-raw-data](../../../includes/iot-central-monitor-thermostat-raw-data.md)]

## <a name="next-steps"></a>Další kroky

Pokud budete chtít pokračovat v IoT Central výukových kurzů a další informace o vytváření řešení IoT Central najdete tady:

> [!div class="nextstepaction"]
> [Vytvoření šablony zařízení brány](./tutorial-define-gateway-device-type.md)

Jako vývojář zařízení teď jste se seznámili se základy vytváření zařízení pomocí Node.js, což je několik navrhovaných dalších kroků:

* Přečtěte si, [co jsou šablony zařízení?](./concepts-device-templates.md) Další informace o roli šablon zařízení při implementaci kódu zařízení.
* Další informace o registraci zařízení pomocí IoT Central a o tom, jak IoT Central zabezpečují připojení zařízení, najdete v tématu věnovaném [připojení k Azure IoT Central](./concepts-get-connected.md) .
* Další informace o datech, která zařízení vyměňuje pomocí IoT Central, najdete v tématu [telemetrie, vlastnosti a datové části příkazů](concepts-telemetry-properties-commands.md) .
