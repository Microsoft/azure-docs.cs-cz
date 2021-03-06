---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 10/20/2020
ms.openlocfilehash: dfeeb451912dd32770a6ae92d73de83851d9d8f6
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102244482"
---
K dispozici jsou také tyto prostředky:

- [ Referenční dokumentace k saděNode.js SDK](/javascript/api/azure-iothub)
- [Ukázky klienta služby](https://github.com/Azure/azure-iot-sdk-node/blob/master/service/samples/javascript/twin.js)
- [Vzorky digitálních vláken](https://github.com/Azure/azure-iot-sdk-node/blob/master/service/samples/javascript/get_digital_twin.js)

## <a name="iot-hub-service-client-examples"></a>Příklady klienta služby IoT Hub

V této části jsou uvedeny příklady jazyka JavaScript pomocí klienta služby IoT Hub a tříd **registru** a **klienta** . Třídu **registru** použijete k interakci se stavem zařízení pomocí nevláken zařízení. Můžete také použít třídu **registru** k [dotazování registrace zařízení](../articles/iot-hub/iot-hub-devguide-query-language.md) v IoT Hub. Třídu **Client** použijete k volání příkazů na zařízení. Model [DTDL](../articles/iot-pnp/concepts-digital-twin.md) pro zařízení definuje vlastnosti a příkazy, které zařízení implementuje. V fragmentech kódu `deviceId` proměnná obsahuje ID zařízení technologie Plug and Play IoT, které je zaregistrované ve službě IoT Hub.

### <a name="get-the-device-twin-and-model-id"></a>Získat ID a ID modelu zařízení

Chcete-li získat dvojitou a ID modelu zařízení IoT technologie Plug and Play, které je připojeno ke službě IoT Hub:

```javascript
var Registry = require('azure-iothub').Registry;

// ...

var registry = Registry.fromConnectionString(connectionString);
registry.getTwin(deviceId, function(err, twin) {
  if (err) {
    console.error(err.message);
  } else {
    console.log('Model Id: ' + twin.modelId);
    console.log(JSON.stringify(twin, null, 2));
  }
}
```

### <a name="update-device-twin"></a>Aktualizovat dvojitou dvojici zařízení

Následující fragment kódu ukazuje, jak aktualizovat `targetTemperature` vlastnost na zařízení. Ukázka ukazuje, jak potřebujete získat dvojitou dobu před tím, než ho aktualizujete. Vlastnost je definována ve výchozí součásti zařízení:

```javascript
var Registry = require('azure-iothub').Registry;
var registry = Registry.fromConnectionString(connectionString);

registry.getTwin(deviceId, function(err, twin) {
  if (err) {
    console.error(err.message);
  } else {
    var twinPatch = {
      properties: {
        desired: {
          targetTemperature: 42
        }
      }
    };
    twin.update(twinPatch, function(err, twin) {
      if (err) {
        console.error(err.message);
      } else {
        console.log(JSON.stringify(twin, null, 2));
      }
    }
  }
}
```

Následující fragment kódu ukazuje, jak aktualizovat `targetTemperature` vlastnost pro komponentu. Ukázka ukazuje, jak potřebujete získat dvojitou dobu před tím, než ho aktualizujete. Vlastnost je definována v komponentě **thermostat1** :

```javascript
var Registry = require('azure-iothub').Registry;
var registry = Registry.fromConnectionString(connectionString);

registry.getTwin(deviceId, function(err, twin) {
  if (err) {
    console.error(err.message);
  } else {
    var twinPatch = {
      properties: {
        desired: {
          thermostat1:
          {
            __t: "c",
            targetTemperature: 45
          }
        }
      }
    };
    twin.update(twinPatch, function(err, twin) {
      if (err) {
        console.error(err.message);
      } else {
        console.log(JSON.stringify(twin, null, 2));
      }
    }
  }
}
```

V případě vlastnosti v součásti vypadá oprava vlastnosti jako v následujícím příkladu:

```json
{
  "thermostat1":
  {
    "__t": "c",
    "targetTemperature": 20
  }
}
```

### <a name="call-command"></a>Příkaz call

Následující fragment kódu ukazuje, jak vyvolat `getMaxMinReport` příkaz definovaný ve výchozí komponentě:

```javascript
var Client = require('azure-iothub').Client;

// ...

var client = Client.fromConnectionString(connectionString);

var methodParams = {
  methodName: "getMaxMinReport",
  payload: new Date().getMinutes -2,
  responseTimeoutInSeconds: 15
};

client.invokeDeviceMethod(deviceId, methodParams, function (err, result) {
  if (err) {
    console.error('Failed to invoke method \'' + methodParams.methodName + '\': ' + err.message);
  } else {
    console.log(methodParams.methodName + ' on ' + deviceId + ':');
    console.log(JSON.stringify(result, null, 2));
  }
});
```

Následující fragment kódu ukazuje, jak zavolat `getMaxMinReport` příkaz na komponentu. Příkaz je definován v součásti **thermostat1** :

```javascript
var Client = require('azure-iothub').Client;

// ...

var client = Client.fromConnectionString(connectionString);

var methodParams = {
  methodName: "thermostat1*getMaxMinReport",
  payload: new Date().getMinutes -2,
  responseTimeoutInSeconds: 15
};

client.invokeDeviceMethod(deviceId, methodParams, function (err, result) {
  if (err) {
    console.error('Failed to invoke method \'' + methodParams.methodName + '\': ' + err.message);
  } else {
    console.log(methodParams.methodName + ' on ' + deviceId + ':');
    console.log(JSON.stringify(result, null, 2));
  }
});
```

## <a name="iot-hub-digital-twin-examples"></a>Příklady IoT Hub digitálních vláken

Třídu **DigitalTwinClient** použijete k interakci se stavem zařízení pomocí digitálních vláken. Model [DTDL](../articles/iot-pnp/concepts-digital-twin.md) pro zařízení definuje vlastnosti a příkazy, které zařízení implementuje.

V této části jsou uvedeny příklady jazyka JavaScript s využitím digitálního vlákna API.

`digitalTwinId`Proměnná obsahuje ID zařízení technologie Plug and Play IoT, které je zaregistrované ve službě IoT Hub.

### <a name="get-the-digital-twin-and-model-id"></a>Získání digitálního vlákna a ID modelu

K získání digitálního vlákna a ID modelu technologie Plug and Play zařízení IoT, které jste připojili ke službě IoT Hub:

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;
const { inspect } = require('util');

// ...

const credentials = new IoTHubTokenCredentials(connectionString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const digitalTwin = await digitalTwinClient.getDigitalTwin(digitalTwinId);

console.log(inspect(digitalTwin));
console.log('Model Id: ' + inspect(digitalTwin.$metadata.$model));
```

### <a name="update-digital-twin"></a>Aktualizace digitálního vlákna

Následující fragment kódu ukazuje, jak aktualizovat `targetTemperature` vlastnost na zařízení. Vlastnost je definována ve výchozí součásti zařízení:

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;

// ...

const credentials = new IoTHubTokenCredentials(connString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const patch = [{
  op: 'add',
  path: '/targetTemperature',
  value: 42
}];
await digitalTwinClient.updateDigitalTwin(digitalTwinId, patch);
```

Následující fragment kódu ukazuje, jak aktualizovat `targetTemperature` vlastnost pro komponentu. Vlastnost je definována v komponentě **thermostat1** :

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;

// ...

const credentials = new IoTHubTokenCredentials(connString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const patch = [{
  op: 'add',
  path: '/thermostat1/targetTemperature',
  value: 42
}];
await digitalTwinClient.updateDigitalTwin(digitalTwinId, patch);
```

### <a name="call-command"></a>Příkaz call

Následující fragment kódu ukazuje, jak vyvolat `getMaxMinReport` příkaz definovaný ve výchozí komponentě:

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;
const { inspect } = require('util');

// ...

const commandPayload = new Date().getMinutes -2;

const credentials = new IoTHubTokenCredentials(connectionString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const options = {
  connectTimeoutInSeconds: 30,
  responseTimeoutInSeconds: 40
};
const commandResponse = await digitalTwinClient.invokeCommand(digitalTwinId, "getMaxMinReport", commandPayload, options);

console.log(inspect(commandResponse));
```

Následující fragment kódu ukazuje, jak zavolat `getMaxMinReport` příkaz na komponentu. Příkaz je definován v součásti **thermostat1** :

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;
const { inspect } = require('util');

// ...

const commandPayload = new Date().getMinutes -2;

const credentials = new IoTHubTokenCredentials(connectionString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const options = {
  connectTimeoutInSeconds: 30,
  responseTimeoutInSeconds: 40
};
const commandResponse = await digitalTwinClient.invokeComponentCommand(digitalTwinId, "thermostat1", "getMaxMinReport", commandPayload, options);

console.log(inspect(commandResponse));
```

## <a name="read-device-telemetry"></a>Čtení telemetrie zařízení

Zařízení IoT technologie Plug and Play odesílají telemetrii definovaná v modelu DTDL k IoT Hub. Ve výchozím nastavení IoT Hub směruje telemetrii do koncového bodu Event Hubs, kde ho můžete spotřebovat. Další informace najdete v tématu [použití směrování zpráv IoT Hub k posílání zpráv ze zařízení do cloudu do různých koncových bodů](../articles/iot-hub/iot-hub-devguide-messages-d2c.md).

Následující fragment kódu ukazuje, jak číst telemetrii z výchozího koncového bodu Event Hubs. Kód v tomto fragmentu kódu je pořízen z rychlého startu IoT Hub [Odeslat telemetrii ze zařízení do služby IoT Hub a přečíst si ho pomocí back-endové aplikace](../articles/iot-hub/quickstart-send-telemetry-node.md):

```javascript
const { EventHubConsumerClient } = require("@azure/event-hubs");

var printError = function (err) {
  console.log(err.message);
};

var printMessages = function (messages) {
  for (const message of messages) {
    console.log("Telemetry received: ");
    console.log(JSON.stringify(message.body));
    console.log("Properties (set by device): ");
    console.log(JSON.stringify(message.properties));
    console.log("System properties (set by IoT Hub): ");
    console.log(JSON.stringify(message.systemProperties));
    console.log("");
  }
};

// ...

const clientOptions = {};

const consumerClient = new EventHubConsumerClient("$Default", connectionString, clientOptions);

consumerClient.subscribe({
  processEvents: printMessages,
  processError: printError,
});
```

Následující výstup z předchozího kódu ukazuje telemetrii o teplotě, kterou posílá **TemperatureController** IoT technologie Plug and Play zařízení s více komponentami. `dt-subject`Vlastnost System zobrazuje název součásti, která tuto telemetrii poslala. V tomto příkladu jsou dvě komponenty `thermostat1` a jak jsou `thermostat2` definovány v modelu DTDL. `dt-dataschema`Vlastnost System zobrazuje ID modelu:

```cmd/sh
Telemetry received:
{"temperature":68.77370855171125}
Properties (set by device):
undefined
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-connection-auth-method":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","iothub-connection-auth-generation-id":"637388034455888246","iothub-enqueuedtime":1603206669320,"iothub-message-source":"Telemetry","dt-subject":"thermostat1","dt-dataschema":"dtmi:com:example:TemperatureController;1","contentType":"application/json","contentEncoding":"utf-8"}

Telemetry received:
{"temperature":30.833394506549226}
Properties (set by device):
undefined
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-connection-auth-method":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","iothub-connection-auth-generation-id":"637388034455888246","iothub-enqueuedtime":1603206665835,"iothub-message-source":"Telemetry","dt-subject":"thermostat2","dt-dataschema":"dtmi:com:example:TemperatureController;1","contentType":"application/json","contentEncoding":"utf-8"}
```

## <a name="read-device-twin-change-notifications"></a>Přečíst oznámení o zdvojených změnách zařízení

Můžete nakonfigurovat IoT Hub pro generování oznámení o nedoručení zařízení, která budou směrovat do podporovaného koncového bodu. Další informace najdete v tématu [použití směrování zpráv IoT Hub k posílání zpráv typu zařízení-Cloud do různých koncových bodů > události netelemetrie](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

Kód zobrazený v předchozím fragmentu kódu JavaScriptu generuje následující výstup, když IoT Hub vygeneruje oznámení o zdvojení zařízení pro zařízení termostatu bez komponent. Vlastnosti aplikace `iothub-message-schema` a `opType` poskytují informace o typu oznámení o změně:

```cmd/sh
Telemetry received:
{"version":4,"properties":{"reported":{"maxTempSinceLastReboot":42.1415152639582,"$metadata":{"$lastUpdated":"2020-10-21T10:01:40.1281138Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-21T10:01:40.1281138Z"}},"$version":3}}}
Properties (set by device):
{"hubName":"my-pnp-hub","deviceId":"my-pnp-device","operationTimestamp":"2020-10-21T10:01:40.1281138+00:00","iothub-message-schema":"twinChangeNotification","opType":"updateTwin"}
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-enqueuedtime":1603274500282,"iothub-message-source":"twinChangeEvents","userId":{"type":"Buffer","data":[109,121,45,112,110,112,45,104,117,98]},"correlationId":"11ed82d13f50","contentType":"application/json","contentEncoding":"utf-8"}
```

Kód zobrazený v předchozím fragmentu kódu JavaScriptu generuje následující výstup, když IoT Hub pro zařízení s komponentami generuje oznámení o zdvojených změnách zařízení. Tento příklad ukazuje výstup, když zařízení snímače teploty s termostatovou komponentou generuje oznámení. Vlastnosti aplikace `iothub-message-schema` a `opType` poskytují informace o typu oznámení o změně:

```cmd/sh
Telemetry received:
{"version":4,"properties":{"reported":{"thermostat1":{"maxTempSinceLastReboot":3.5592971602417913,"__t":"c"},"$metadata":{"$lastUpdated":"2020-10-21T10:07:51.8284866Z","thermostat1":{"$lastUpdated":"2020-10-21T10:07:51.8284866Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-21T10:07:51.8284866Z"},"__t":{"$lastUpdated":"2020-10-21T10:07:51.8284866Z"}}},"$version":3}}}
Properties (set by device):
{"hubName":"my-pnp-hub","deviceId":"my-pnp-device","operationTimestamp":"2020-10-21T10:07:51.8284866+00:00","iothub-message-schema":"twinChangeNotification","opType":"updateTwin"}
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-enqueuedtime":1603274871951,"iothub-message-source":"twinChangeEvents","userId":{"type":"Buffer","data":[109,121,45,112,110,112,45,104,117,98]},"correlationId":"11ee605b195f","contentType":"application/json","contentEncoding":"utf-8"}
```

## <a name="read-digital-twin-change-notifications"></a>Číst oznámení o změně digitálního vlákna

Můžete nakonfigurovat IoT Hub pro generování oznámení o změně digitálního vlákna pro směrování do podporovaného koncového bodu. Další informace najdete v tématu [použití směrování zpráv IoT Hub k posílání zpráv typu zařízení-Cloud do různých koncových bodů > události netelemetrie](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

Kód zobrazený v předchozím fragmentu kódu JavaScriptu generuje následující výstup, když IoT Hub generuje pro zařízení termostatu bez komponenty žádné oznámení o změně digitálního vlákna. Vlastnosti aplikace `iothub-message-schema` a `opType` poskytují informace o typu oznámení o změně:

```cmd/sh
Telemetry received:
[{"op":"add","path":"/$metadata/maxTempSinceLastReboot","value":{"lastUpdateTime":"2020-10-21T10:01:40.1281138Z"}},{"op":"add","path":"/maxTempSinceLastReboot","value":42.1415152639582}]
Properties (set by device):
{"hubName":"my-pnp-hub","deviceId":"my-pnp-device","operationTimestamp":"2020-10-21T10:01:40.1281138+00:00","iothub-message-schema":"digitalTwinChangeNotification","opType":"updateTwin"}
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-enqueuedtime":1603274500282,"iothub-message-source":"digitalTwinChangeEvents","userId":{"type":"Buffer","data":[109,121,45,112,110,112,45,104,117,98]},"correlationId":"11ed82d13f50","contentType":"application/json-patch+json","contentEncoding":"utf-8"}
```

Kód zobrazený v předchozím fragmentu kódu JavaScriptu generuje následující výstup, když IoT Hub generuje upozornění na digitální dvojitou změnu pro zařízení s komponentami. Tento příklad ukazuje výstup, když zařízení snímače teploty s termostatovou komponentou generuje oznámení. Vlastnosti aplikace `iothub-message-schema` a `opType` poskytují informace o typu oznámení o změně:

```cmd/sh
Telemetry received:
[{"op":"add","path":"/thermostat1","value":{"$metadata":{"maxTempSinceLastReboot":{"lastUpdateTime":"2020-10-21T10:07:51.8284866Z"}},"maxTempSinceLastReboot":3.5592971602417913}}]
Properties (set by device):
{"hubName":"my-pnp-hub","deviceId":"my-pnp-device","operationTimestamp":"2020-10-21T10:07:51.8284866+00:00","iothub-message-schema":"digitalTwinChangeNotification","opType":"updateTwin"}
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-enqueuedtime":1603274871951,"iothub-message-source":"digitalTwinChangeEvents","userId":{"type":"Buffer","data":[109,121,45,112,110,112,45,104,117,98]},"correlationId":"11ee605b195f","contentType":"application/json-patch+json","contentEncoding":"utf-8"}
```
