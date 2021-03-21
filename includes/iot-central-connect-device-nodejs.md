---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/24/2020
ms.openlocfilehash: 6a6baa14d7521f4a85350af7b08b5fcbe82ddf6b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97033827"
---
## <a name="prerequisites"></a>Předpoklady

K dokončení kroků v tomto článku budete potřebovat následující:

* Aplikace Azure IoT Central vytvořená pomocí šablony **vlastní aplikace** . Další informace najdete v [rychlém startu k vytvoření aplikace](../articles/iot-central/core/quick-deploy-iot-central.md). Aplikace musí být vytvořená na základě 14. července 2020 nebo po ní.
* Vývojový počítač s nainstalovanou verzí [Node.js](https://nodejs.org/) verze 6 nebo novější. Můžete spustit na `node --version` příkazovém řádku a ověřit svou verzi. Pokyny v tomto kurzu předpokládají, že jste spustili příkaz **Node** na příkazovém řádku Windows. Můžete však použít Node.js v mnoha dalších operačních systémech.
* Místní kopie [Microsoft Azure IoT SDK pro Node.js](https://github.com/Azure/azure-iot-sdk-node) úložiště GitHub, která obsahuje vzorový kód. Pomocí tohoto odkazu si stáhněte kopii úložiště: [Stáhnout ZIP](https://github.com/Azure/azure-iot-sdk-node/archive/master.zip). Pak soubor rozbalte do vhodného umístění v místním počítači.

## <a name="review-the-code"></a>Kontrola kódu

V rámci kopie Microsoft Azure IoT SDK pro Node.js, kterou jste předtím stáhli, otevřete soubor *Azure-IoT-SDK-Node/Device/Samples/PNP/simple_thermostat.js* v textovém editoru.

Když spustíte ukázku pro připojení k IoT Central, používá službu Device Provisioning (DPS) k registraci zařízení a vygenerování připojovacího řetězce. Ukázka načte informace o připojení DPS, které potřebuje z prostředí příkazového řádku.

`main`Metoda:

* Vytvoří `client` objekt a `dtmi:com:example:Thermostat;1` před otevřením připojení nastaví ID modelu. IoT Central používá ID modelu k identifikaci nebo generování šablony zařízení pro toto zařízení. Další informace najdete v tématu [přidružení zařízení k šabloně zařízení](../articles/iot-central/core/concepts-get-connected.md#associate-a-device-with-a-device-template).
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

`provisionDevice`Funkce ukazuje, jak zařízení používá DPS k registraci a připojení k IoT Central. Datová část zahrnuje ID modelu, které IoT Central používá k [přidružení zařízení k šabloně zařízení](../articles/iot-central/core/concepts-get-connected.md#associate-a-device-with-a-device-template):

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

[!INCLUDE [iot-central-connection-configuration](iot-central-connection-configuration.md)]

## <a name="run-the-code"></a>Spuštění kódu

Pokud chcete spustit ukázkovou aplikaci, otevřete prostředí příkazového řádku a přejděte do složky *Azure-IoT-SDK-Node/Device/Samples/PNP* , která obsahuje ukázkový soubor *simple_thermostat.js* .

[!INCLUDE [iot-central-connection-environment](iot-central-connection-environment.md)]

Nainstalujte požadované balíčky:

```cmd/sh
npm install
```

Spusťte ukázku:

```cmd/sh
node simple_thermostat.js
```

Následující výstup zobrazuje registraci zařízení a připojení k IoT Central. Ukázka pak `maxTempSinceLastReboot` před zahájením odesílání telemetrie vlastnost pošle:

```output
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

[!INCLUDE [iot-central-monitor-thermostat](iot-central-monitor-thermostat.md)]

Můžete zjistit, jak zařízení reaguje na příkazy a aktualizace vlastností:

```output
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
