---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 10/20/2020
ms.openlocfilehash: bef7807c0df580a6763a69619cdaa3d9d29f72e6
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92521365"
---
K dispozici jsou také tyto prostředky:

- [Referenční dokumentace sady Python SDK](/python/api/azure-iot-hub/azure.iot.hub?preserve-view=true&view=azure-python)
- [Ukázky klienta služby](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-hub/samples/iothub_registry_manager_method_sample.py)
- [Vzorky digitálních vláken](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-hub/samples/get_digital_twin_sample.py)

## <a name="iot-hub-service-client-examples"></a>Příklady klienta služby IoT Hub

V této části jsou uvedeny příklady Pythonu pomocí klienta služby IoT Hub a tříd **IoTHubRegistryManager** a **CloudToDeviceMethod** . Třídu **IoTHubRegistryManager** použijete k interakci se stavem zařízení pomocí nevláken zařízení. Můžete také použít třídu **IoTHubRegistryManager** k [dotazování registrace zařízení](../articles/iot-hub/iot-hub-devguide-query-language.md) v IoT Hub. Třídu **CloudToDeviceMethod** použijete k volání příkazů na zařízení. Model [DTDL](../articles/iot-pnp/concepts-digital-twin.md) pro zařízení definuje vlastnosti a příkazy, které zařízení implementuje. V fragmentech kódu `device_id` proměnná obsahuje ID zařízení technologie Plug and Play IoT, které je zaregistrované ve službě IoT Hub.

### <a name="get-the-device-twin-and-model-id"></a>Získat ID a ID modelu zařízení

Chcete-li získat dvojitou a ID modelu zařízení IoT technologie Plug and Play, které je připojeno ke službě IoT Hub:

```python
from azure.iot.hub import IoTHubRegistryManager
from azure.iot.hub.models import Twin, TwinProperties

iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

# ...

twin = iothub_registry_manager.get_twin(device_id)
print("The device twin is: ")
print("")
print(twin)
print("")

additional_props = twin.additional_properties
if "modelId" in additional_props:
    print("The Model ID for this device is:")
    print(additional_props["modelId"])
    print("")
```

### <a name="update-device-twin"></a>Aktualizovat dvojitou dvojici zařízení

Následující fragment kódu ukazuje, jak aktualizovat `targetTemperature` vlastnost na zařízení. Ukázka ukazuje, jak potřebujete získat vše, `etag` než ho budete aktualizovat. Vlastnost je definována ve výchozí součásti zařízení:

```python
iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

twin = iothub_registry_manager.get_twin(device_id)

twin_patch = Twin()

twin_patch.properties = TwinProperties(
    desired={"targetTemperature": 42}
)
updated_twin = iothub_registry_manager.update_twin(device_id, twin_patch, twin.etag)
```

Následující fragment kódu ukazuje, jak aktualizovat `targetTemperature` vlastnost pro komponentu. Ukázka ukazuje, jak potřebujete získat vše, `ETag` než ho budete aktualizovat. Vlastnost je definována v komponentě **thermostat1** :

```python
iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

twin = iothub_registry_manager.get_twin(device_id)

twin_patch = Twin()

twin_patch.properties = TwinProperties(
    desired={ "thermostat1": {
        "__t": "c",
        "targetTemperature": 42}
    }
)
updated_twin = iothub_registry_manager.update_twin(device_id, twin_patch, twin.etag)
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

```python
from azure.iot.hub import IoTHubRegistryManager
from azure.iot.hub.models import CloudToDeviceMethod

# ...

iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

method_payload = datetime.datetime.now() - datetime.timedelta(minutes=2)
device_method = CloudToDeviceMethod(method_name="getMaxMinReport", payload=method_payload)
result = iothub_registry_manager.invoke_device_method(device_id, device_method)
print(result.payload)
```

Následující fragment kódu ukazuje, jak zavolat `getMaxMinReport` příkaz na komponentu. Příkaz je definován v součásti **thermostat1** :

```python
from azure.iot.hub import IoTHubRegistryManager
from azure.iot.hub.models import CloudToDeviceMethod

# ...

iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

method_payload = datetime.datetime.now() - datetime.timedelta(minutes=2)
device_method = CloudToDeviceMethod(method_name="thermostat1*getMaxMinReport", payload=method_payload)
result = iothub_registry_manager.invoke_device_method(device_id, device_method)
print(result.payload)
```

## <a name="iot-hub-digital-twin-examples"></a>Příklady IoT Hub digitálních vláken

Třídu **DigitalTwinClient** použijete k interakci se stavem zařízení pomocí digitálních vláken. Model [DTDL](../articles/iot-pnp/concepts-digital-twin.md) pro zařízení definuje vlastnosti a příkazy, které zařízení implementuje.

`device_id`Proměnná obsahuje ID zařízení technologie Plug and Play IoT, které je zaregistrované ve službě IoT Hub.

### <a name="get-the-digital-twin-and-model-id"></a>Získání digitálního vlákna a ID modelu

K získání digitálního vlákna a ID modelu technologie Plug and Play zařízení IoT, které jste připojili ke službě IoT Hub:

```python
from azure.iot.hub import DigitalTwinClient

digital_twin_client = DigitalTwinClient(iothub_connection_str)

digital_twin = digital_twin_client.get_digital_twin(device_id)
if digital_twin:
    print(digital_twin)
    print("Model Id: " + digital_twin["$metadata"]["$model"])
else:
    print("No digital_twin found")
```

### <a name="update-digital-twin"></a>Aktualizace digitálního vlákna

Následující fragment kódu ukazuje, jak aktualizovat `targetTemperature` vlastnost na zařízení. Vlastnost je definována ve výchozí součásti zařízení:

```python
from azure.iot.hub import DigitalTwinClient

digital_twin_client = DigitalTwinClient(iothub_connection_str)

patch = [{"op": "add", "path": "/targetTemperature", "value": 42}]
digital_twin_client.update_digital_twin(device_id, patch)
```

Následující fragment kódu ukazuje, jak aktualizovat `targetTemperature` vlastnost pro komponentu. Vlastnost je definována v komponentě **thermostat1** :

```python
from azure.iot.hub import DigitalTwinClient

digital_twin_client = DigitalTwinClient(iothub_connection_str)

patch = [{"op": "add", "path": "/targetTemperature", "value": 42}]
digital_twin_client.update_digital_twin(device_id, patch)
```

### <a name="call-command"></a>Příkaz call

Následující fragment kódu ukazuje, jak vyvolat `getMaxMinReport` příkaz definovaný ve výchozí komponentě:

```python
from azure.iot.hub import DigitalTwinClient

payload = datetime.datetime.now() - datetime.timedelta(minutes=2)

connect_timeout_in_seconds = 3
response_timeout_in_seconds = 7


digital_twin_client = DigitalTwinClient(iothub_connection_str)

invoke_command_result = digital_twin_client.invoke_command(
    device_id, "getMaxMinReport", payload, connect_timeout_in_seconds, response_timeout_in_seconds
)
if invoke_command_result:
    print(invoke_command_result)
else:
    print("No invoke_command_result found")
```

Následující fragment kódu ukazuje, jak zavolat `getMaxMinReport` příkaz na komponentu. Příkaz je definován v součásti **thermostat1** :

```python
from azure.iot.hub import DigitalTwinClient

payload = datetime.datetime.now() - datetime.timedelta(minutes=2)

connect_timeout_in_seconds = 3
response_timeout_in_seconds = 7


digital_twin_client = DigitalTwinClient(iothub_connection_str)

invoke_command_result = digital_twin_client.invoke_component_command(
    device_id, "thermostat1", "getMaxMinReport", payload, connect_timeout_in_seconds, response_timeout_in_seconds
)
if invoke_command_result:
    print(invoke_command_result)
else:
    print("No invoke_command_result found")
```

## <a name="read-device-telemetry"></a>Čtení telemetrie zařízení

Zařízení IoT technologie Plug and Play odesílají telemetrii definovaná v modelu DTDL k IoT Hub. Ve výchozím nastavení IoT Hub směruje telemetrii do koncového bodu Event Hubs, kde ho můžete spotřebovat. Další informace najdete v tématu [použití směrování zpráv IoT Hub k posílání zpráv ze zařízení do cloudu do různých koncových bodů](../articles/iot-hub/iot-hub-devguide-messages-d2c.md).

Následující fragment kódu ukazuje, jak číst telemetrii z výchozího koncového bodu Event Hubs. Kód v tomto fragmentu kódu je pořízen z rychlého startu IoT Hub [Odeslat telemetrii ze zařízení do služby IoT Hub a přečíst si ho pomocí back-endové aplikace](../articles/iot-hub/quickstart-send-telemetry-python.md):

```python
import asyncio
from azure.eventhub import TransportType
from azure.eventhub.aio import EventHubConsumerClient

# Define callbacks to process events
async def on_event_batch(partition_context, events):
    for event in events:
        print("Received event from partition: {}.".format(partition_context.partition_id))
        print("Telemetry received: ", event.body_as_str())
        print("Properties (set by device): ", event.properties)
        print("System properties (set by IoT Hub): ", event.system_properties)
        print()
    await partition_context.update_checkpoint()

async def on_error(partition_context, error):
    # ...

loop = asyncio.get_event_loop()
client = EventHubConsumerClient.from_connection_string(
    conn_str=CONNECTION_STR,
    consumer_group="$default",
)

try:
    loop.run_until_complete(client.receive_batch(on_event_batch=on_event_batch, on_error=on_error))
except KeyboardInterrupt:
    print("Receiving has stopped.")
finally:
    loop.run_until_complete(client.close())
    loop.stop()
```

Následující výstup z předchozího kódu ukazuje telemetrii teploty odeslanou **termostatem** zařízení IoT technologie Plug and Play, který má pouze výchozí komponentu. `dt-dataschema`Vlastnost System zobrazuje ID modelu:

```cmd/sh
Received event from partition: 1.
Telemetry received:  {"temperature": 12}
Properties (set by device):  None
System properties (set by IoT Hub):  {b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-connection-auth-method': b'{"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}', b'iothub-connection-auth-generation-id': b'637388855582764406', b'iothub-enqueuedtime': 1603288810715, b'iothub-message-source': b'Telemetry', b'dt-dataschema': b'dtmi:com:example:Thermostat;1', b'x-opt-sequence-number': 13280, b'x-opt-offset': b'12890070640', b'x-opt-enqueued-time': 1603288810824}
```

Následující výstup z předchozího kódu ukazuje telemetrii o teplotě, kterou posílá **TemperatureController** IoT technologie Plug and Play zařízení s více komponentami. `dt-subject`Vlastnost System zobrazuje název součásti, která tuto telemetrii poslala. V tomto příkladu jsou dvě komponenty `thermostat1` a jak jsou `thermostat2` definovány v modelu DTDL. `dt-dataschema`Vlastnost System zobrazuje ID modelu:

```cmd/sh
Received event from partition: 1.
Telemetry received:  {"temperature": 45}
Properties (set by device):  None
System properties (set by IoT Hub):  {b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-connection-auth-method': b'{"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}', b'iothub-connection-auth-generation-id': b'637388858939631652', b'iothub-enqueuedtime': 1603289127844, b'iothub-message-source': b'Telemetry', b'dt-subject': b'thermostat1', b'dt-dataschema': b'dtmi:com:example:TemperatureController;1', b'x-opt-sequence-number': 13328, b'x-opt-offset': b'12890095440', b'x-opt-enqueued-time': 1603289128001}

Received event from partition: 1.
Telemetry received:  {"temperature": 49}
Properties (set by device):  None
System properties (set by IoT Hub):  {b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-connection-auth-method': b'{"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}', b'iothub-connection-auth-generation-id': b'637388858939631652', b'iothub-enqueuedtime': 1603289133017, b'iothub-message-source': b'Telemetry', b'dt-subject': b'thermostat2', b'dt-dataschema': b'dtmi:com:example:TemperatureController;1', b'x-opt-sequence-number': 13329, b'x-opt-offset': b'12890095928', b'x-opt-enqueued-time': 1603289133173}
```

## <a name="read-device-twin-change-notifications"></a>Přečíst oznámení o zdvojených změnách zařízení

Můžete nakonfigurovat IoT Hub pro generování oznámení o nedoručení zařízení, která budou směrovat do podporovaného koncového bodu. Další informace najdete v tématu [použití směrování zpráv IoT Hub k posílání zpráv typu zařízení-Cloud do různých koncových bodů > události netelemetrie](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

Kód zobrazený v předchozím fragmentu kódu Python generuje následující výstup, když IoT Hub vygeneruje oznámení o zdvojení zařízení pro zařízení termostatu bez komponent. Vlastnosti aplikace `iothub-message-schema` a `opType` poskytují informace o typu oznámení o změně:

```cmd/sh
Received event from partition: 1.
Telemetry received:  {"version":3,"properties":{"reported":{"maxTempSinceLastReboot":10.96,"$metadata":{"$lastUpdated":"2020-10-21T14:10:42.4171263Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-21T14:10:42.4171263Z"}},"$version":2}}}
Properties (set by device):  {b'hubName': b'my-pnp-hub', b'deviceId': b'my-pnp-device', b'operationTimestamp': b'2020-10-21T14:10:42.4171263+00:00', b'iothub-message-schema': b'twinChangeNotification', b'opType': b'updateTwin'}
System properties (set by IoT Hub):  {b'user-id': b'my-pnp-hub\x81\x0e\xa4\x7f', b'correlation-id': b'12104ced5402', b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-enqueuedtime': 1603289442519, b'iothub-message-source': b'twinChangeEvents', b'x-opt-sequence-number': 13332, b'x-opt-offset': b'12890097392', b'x-opt-enqueued-time': 1603289442738}
```

Kód zobrazený v předchozím fragmentu kódu Python generuje následující výstup, když IoT Hub pro zařízení s komponentami generuje oznámení o zdvojených změnách zařízení. Tento příklad ukazuje výstup, když zařízení snímače teploty s termostatovou komponentou generuje oznámení. Vlastnosti aplikace `iothub-message-schema` a `opType` poskytují informace o typu oznámení o změně:

```cmd/sh
Received event from partition: 1.
Telemetry received:  {"version":4,"properties":{"reported":{"thermostat1":{"maxTempSinceLastReboot":98.34,"__t":"c"},"$metadata":{"$lastUpdated":"2020-10-21T14:13:39.36491Z","thermostat1":{"$lastUpdated":"2020-10-21T14:13:39.36491Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-21T14:13:39.36491Z"},"__t":{"$lastUpdated":"2020-10-21T14:13:39.36491Z"}}},"$version":3}}}
Properties (set by device):  {b'hubName': b'my-pnp-hub', b'deviceId': b'my-pnp-device', b'operationTimestamp': b'2020-10-21T14:13:39.3649100+00:00', b'iothub-message-schema': b'twinChangeNotification', b'opType': b'updateTwin'}
System properties (set by IoT Hub):  {b'user-id': b'my-pnp-hub', b'correlation-id': b'1210b664ab83', b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-enqueuedtime': 1603289619481, b'iothub-message-source': b'twinChangeEvents', b'x-opt-sequence-number': 13341, b'x-opt-offset': b'12890102216', b'x-opt-enqueued-time': 1603289619668}
```

## <a name="read-digital-twin-change-notifications"></a>Číst oznámení o změně digitálního vlákna

Můžete nakonfigurovat IoT Hub pro generování oznámení o změně digitálního vlákna pro směrování do podporovaného koncového bodu. Další informace najdete v tématu [použití směrování zpráv IoT Hub k posílání zpráv typu zařízení-Cloud do různých koncových bodů > události netelemetrie](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

Kód zobrazený v předchozím fragmentu kódu Pythonu generuje následující výstup, když IoT Hub generuje pro zařízení termostatu bez komponenty žádné oznámení o změně digitálního vlákna. Vlastnosti aplikace `iothub-message-schema` a `opType` poskytují informace o typu oznámení o změně:

```cmd/sh
Received event from partition: 1.
Telemetry received:  [{"op":"add","path":"/$metadata/maxTempSinceLastReboot","value":{"lastUpdateTime":"2020-10-21T14:10:42.4171263Z"}},{"op":"add","path":"/maxTempSinceLastReboot","value":10.96}]
Properties (set by device):  {b'hubName': b'my-pnp-hub', b'deviceId': b'my-pnp-device', b'operationTimestamp': b'2020-10-21T14:10:42.4171263+00:00', b'iothub-message-schema': b'digitalTwinChangeNotification', b'opType': b'updateTwin'}
System properties (set by IoT Hub):  {b'user-id': b'my-pnp-hub\x81\x0e\xa4\x7f', b'correlation-id': b'12104ced5402', b'content-type': b'application/json-patch+json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-enqueuedtime': 1603289442519, b'iothub-message-source': b'digitalTwinChangeEvents', b'x-opt-sequence-number': 13333, b'x-opt-offset': b'12890098024', b'x-opt-enqueued-time': 1603289442738}
```

Kód zobrazený v předchozím fragmentu kódu Python generuje následující výstup, když IoT Hub pro zařízení s komponentami generuje oznámení o digitálních změnách. Tento příklad ukazuje výstup, když zařízení snímače teploty s termostatovou komponentou generuje oznámení. Vlastnosti aplikace `iothub-message-schema` a `opType` poskytují informace o typu oznámení o změně:

```cmd/sh
Received event from partition: 1.
Telemetry received:  [{"op":"add","path":"/thermostat1","value":{"$metadata":{"maxTempSinceLastReboot":{"lastUpdateTime":"2020-10-21T14:13:39.36491Z"}},"maxTempSinceLastReboot":98.34}}]
Properties (set by device):  {b'hubName': b'my-pnp-hub', b'deviceId': b'my-pnp-device', b'operationTimestamp': b'2020-10-21T14:13:39.3649100+00:00', b'iothub-message-schema': b'digitalTwinChangeNotification', b'opType': b'updateTwin'}
System properties (set by IoT Hub):  {b'user-id': b'my-pnp-hub', b'correlation-id': b'1210b664ab83', b'content-type': b'application/json-patch+json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-enqueuedtime': 1603289619481, b'iothub-message-source': b'digitalTwinChangeEvents', b'x-opt-sequence-number': 13342, b'x-opt-offset': b'12890102984', b'x-opt-enqueued-time': 1603289619668}
```
