---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 10/20/2020
ms.openlocfilehash: a92e37326cf9db0eba186b2bdca44c452c02ea91
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92521367"
---
K dispozici jsou také tyto prostředky:

- [Referenční dokumentace sady .NET SDK](/dotnet/api/microsoft.azure.devices)
- [Ukázky klienta služby](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/service/PnpServiceSamples)
- [Vzorky digitálních vláken](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/service/DigitalTwinClientSamples)

## <a name="iot-hub-service-client-examples"></a>Příklady klienta služby IoT Hub

V této části jsou uvedeny příklady jazyka C# pomocí klienta služby IoT Hub a tříd **RegistryManager** a **ServiceClient** . Třídu **RegistryManager** použijete k interakci se stavem zařízení pomocí nevláken zařízení. Můžete také použít třídu **RegistryManager** k [dotazování registrace zařízení](../articles/iot-hub/iot-hub-devguide-query-language.md) v IoT Hub. Třídu **ServiceClient** použijete k volání příkazů na zařízení. Model [DTDL](../articles/iot-pnp/concepts-digital-twin.md) pro zařízení definuje vlastnosti a příkazy, které zařízení implementuje. V fragmentech kódu `deviceTwinId` proměnná obsahuje ID zařízení technologie Plug and Play IoT, které je zaregistrované ve službě IoT Hub.

### <a name="get-the-device-twin-and-model-id"></a>Získat ID a ID modelu zařízení

Chcete-li získat dvojitou a ID modelu zařízení IoT technologie Plug and Play, které je připojeno ke službě IoT Hub:

```csharp
RegistryManager registryManager = RegistryManager.CreateFromConnectionString(parameters.HubConnectionString);

Twin twin = await registryManager.GetTwinAsync(deviceTwinId);
Console.WriteLine($"Device twin: \n{JsonConvert.SerializeObject(twin, Formatting.Indented)}");
Console.WriteLine($"Model ID: {twin.ModelId}.");
```

### <a name="update-device-twin"></a>Aktualizovat dvojitou dvojici zařízení

Následující fragment kódu ukazuje, jak aktualizovat `targetTemperature` vlastnost na zařízení. Ukázka ukazuje, jak potřebujete získat vše, `ETag` než ho budete aktualizovat. Vlastnost je definována ve výchozí součásti zařízení:

```csharp
Twin twin = await registryManager.GetTwinAsync(deviceTwinId);

int desiredTargetTemperature = 60;

// Update the twin
var twinPatch = new Twin();
twinPatch.Properties.Desired["targetTemperature"] = desiredTargetTemperature;

Console.WriteLine($"Update the targetTemperature property to {desiredTargetTemperature}.");

await registryManager.UpdateTwinAsync(deviceTwinId, twinPatch, twin.ETag);
```

Následující fragment kódu ukazuje, jak aktualizovat `targetTemperature` vlastnost pro komponentu. Ukázka ukazuje, jak potřebujete získat vše, `ETag` než ho budete aktualizovat. Vlastnost je definována v komponentě **Thermostat1** :

```csharp
Twin twin = await registryManager.GetTwinAsync(deviceTwinId);

int desiredTargetTemperature = 60;

var twinPatch = CreatePropertyPatch("targetTemperature", desiredTargetTemperature, "thermostat1");

await registryManager.UpdateTwinAsync(deviceTwinId, twinPatch, twin.ETag);

// ...

private static Twin CreatePropertyPatch(string propertyName, object propertyValue, string componentName)
{
    var twinPatch = new Twin();
    twinPatch.Properties.Desired[componentName] = new
    {
        __t = "c"
    };
    twinPatch.Properties.Desired[componentName][propertyName] = JsonConvert.SerializeObject(propertyValue);
    return twinPatch;
}
```

V případě vlastnosti v součásti vypadá oprava vlastnosti jako v následujícím příkladu:

```json
{
"sampleComponentName":
  {
    "__t": "c",
    "samplePropertyName": 20
  }
}
```

### <a name="call-command"></a>Příkaz call

Následující fragment kódu ukazuje, jak vyvolat `getMaxMinReport` příkaz definovaný ve výchozí komponentě:

```csharp
ServiceClient serviceClient = ServiceClient.CreateFromConnectionString(parameters.HubConnectionString);

var commandInvocation = new CloudToDeviceMethod("getMaxMinReport") { ResponseTimeout = TimeSpan.FromSeconds(30) };

// Set command payload
DateTimeOffset since = DateTimeOffset.Now.Subtract(TimeSpan.FromMinutes(2));
string componentCommandPayload = JsonConvert.SerializeObject(since);
commandInvocation.SetPayloadJson(componentCommandPayload);

try
{
  CloudToDeviceMethodResult result = await serviceClient.InvokeDeviceMethodAsync(deviceTwinId, commandInvocation);

  Console.WriteLine($"Command getMaxMinReport was invoked." +
      $"\nDevice returned status: {result.Status}. \nReport: {result.GetPayloadAsJson()}");
}
catch (DeviceNotFoundException)
{
    Console.WriteLine($"Unable to execute command getMaxMinReport on {deviceTwinId}.";
}
```

Následující fragment kódu ukazuje, jak zavolat `getMaxMinReport` příkaz na komponentu. Příkaz je definován v součásti **Thermostat1** :

```csharp
// Create command name to invoke for component. The command is formatted as <component name>*<command name>
string commandToInvoke = "thermostat1*getMaxMinReport";
var commandInvocation = new CloudToDeviceMethod(commandToInvoke) { ResponseTimeout = TimeSpan.FromSeconds(30) };

// Set command payload
DateTimeOffset since = DateTimeOffset.Now.Subtract(TimeSpan.FromMinutes(2));
string componentCommandPayload = JsonConvert.SerializeObject(since);
commandInvocation.SetPayloadJson(componentCommandPayload);

try
{
    CloudToDeviceMethodResult result = await serviceClient.InvokeDeviceMethodAsync(deviceTwinId, commandInvocation);
    Console.WriteLine($"Command getMaxMinReport was invoked on component thermostat1." +
        $"\nDevice returned status: {result.Status}. \nReport: {result.GetPayloadAsJson()}");
}
catch (DeviceNotFoundException)
{
    Console.WriteLine("Unable to execute command getMaxMinReport on component thermostat1.");
}
```

## <a name="iot-hub-digital-twin-examples"></a>Příklady IoT Hub digitálních vláken

Třídu **DigitalTwinClient** použijete k interakci se stavem zařízení pomocí digitálních vláken. Model [DTDL](../articles/iot-pnp/concepts-digital-twin.md) pro zařízení definuje vlastnosti a příkazy, které zařízení implementuje.

V této části jsou uvedeny příklady jazyka C# pomocí rozhraní API digitálních vláken. Následující fragmenty kódu používají následující třídy pro reprezentaci digitálního vlákna zařízení termostatu a teplotního kontroleru:

```csharp
using Microsoft.Azure.Devices.Serialization;
using Newtonsoft.Json;
using System;

namespace Microsoft.Azure.Devices.Samples
{
  internal class ThermostatTwin : BasicDigitalTwin
  {
    [JsonProperty("$metadata")]
    public new ThermostatMetadata Metadata { get; set; }

    [JsonProperty("maxTempSinceLastReboot")]
    public double? MaxTempSinceLastReboot { get; set; }

    [JsonProperty("targetTemperature")]
    public double? TargetTemperature { get; set; }
  }

  internal class ThermostatMetadata : DigitalTwinMetadata
  {
    [JsonProperty("maxTempSinceLastReboot")]
    public ReportedPropertyMetadata MaxTempSinceLastReboot { get; set; }

    [JsonProperty("targetTemperature")]
    public WritableProperty TargetTemperature { get; set; }
  }

  internal class ReportedPropertyMetadata
  {
    [JsonProperty("lastUpdateTime")]
    public DateTimeOffset LastUpdateTime { get; set; }
  }

  internal class TemperatureControllerTwin : BasicDigitalTwin
  {
    [JsonProperty("$metadata")]
    public new TemperatureControllerMetadata Metadata { get; set; }

    [JsonProperty("serialNumber")]
    public string SerialNumber { get; set; }

    [JsonProperty("thermostat1")]
    public ThermostatTwin Thermostat1 { get; set; }

    [JsonProperty("thermostat2")]
    public ThermostatTwin Thermostat2 { get; set; }
  }

  internal class TemperatureControllerMetadata : DigitalTwinMetadata
  {
    [JsonProperty("serialNumber")]
    public ReportedPropertyMetadata SerialNumber { get; set; }

    [JsonProperty("thermostat1")]
    public WritableProperty Thermostat1 { get; set; }

    [JsonProperty("thermostat2")]
    public WritableProperty Thermostat2 { get; set; }
  }
}
```

`digitalTwinId`Proměnná obsahuje ID zařízení technologie Plug and Play IoT, které je zaregistrované ve službě IoT Hub.

### <a name="get-the-digital-twin-and-model-id"></a>Získání digitálního vlákna a ID modelu

K získání digitálního vlákna a ID modelu technologie Plug and Play zařízení IoT, které jste připojili ke službě IoT Hub:

```csharp
DigitalTwinClient digitalTwinClient = DigitalTwinClient.CreateFromConnectionString(parameters.HubConnectionString);
HttpOperationResponse<ThermostatTwin, DigitalTwinGetHeaders> getDigitalTwinResponse = await digitalTwinClient
    .GetDigitalTwinAsync<ThermostatTwin>(digitalTwinId);
ThermostatTwin thermostatTwin = getDigitalTwinResponse.Body;
Console.WriteLine($"Model ID: {thermostatTwin.Metadata.ModelId}.");
Console.WriteLine($"Digital Twin: \n{JsonConvert.SerializeObject(thermostatTwin, Formatting.Indented)}");
```

### <a name="update-digital-twin"></a>Aktualizace digitálního vlákna

Následující fragment kódu ukazuje, jak aktualizovat `targetTemperature` vlastnost na zařízení. Vlastnost je definována ve výchozí součásti zařízení:

```csharp
var updateOperation = new UpdateOperationsUtility();

int desiredTargetTemperature = 60;

// Get the current value of the targetTemperature property
HttpOperationResponse<ThermostatTwin, DigitalTwinGetHeaders> getDigitalTwinResponse = await digitalTwinClient
    .GetDigitalTwinAsync<ThermostatTwin>(digitalTwinId);
double? currentTargetTemperature = getDigitalTwinResponse.Body.TargetTemperature;

// Has the targetTemperature property previously been set?
if (currentTargetTemperature != null)
{
  // Update the existing property
  // Prepend the property path with a '/'
  updateOperation.AppendReplacePropertyOp($"/targetTemperature",   desiredTargetTemperature);
}
else
{
  // Add a new property
  // Prepend the property path with a '/'
  updateOperation.AppendAddPropertyOp($"/targetTemperature", desiredTargetTemperature);
}

// Update the targetTemperature property on the digital twin
HttpOperationHeaderResponse<DigitalTwinUpdateHeaders> updateDigitalTwinResponse = await digitalTwinClient
    .UpdateDigitalTwinAsync(digitalTwinId, updateOperation.Serialize());

Console.WriteLine($"Update {digitalTwinId} digital twin response: {updateDigitalTwinResponse.Response.StatusCode}.");
```

Následující fragment kódu ukazuje, jak aktualizovat `targetTemperature` vlastnost pro komponentu. Vlastnost je definována v komponentě **Thermostat1** :

```csharp
int desiredTargetTemperature = 60;

var updateOperation = new UpdateOperationsUtility();

// Look at when the property was updated and what was it set to.
HttpOperationResponse<TemperatureControllerTwin, DigitalTwinGetHeaders> getDigitalTwinResponse = await digitalTwinClient
  .GetDigitalTwinAsync<TemperatureControllerTwin>(digitalTwinId);

ThermostatTwin thermostat1 = getDigitalTwinResponse.Body.Thermostat1;

if (thermostat1 != null)
{
  // Thermostat1 is present in the TemperatureController twin. You can add/replace the component-level property "targetTemperature"
  double? currentComponentTargetTemperature = getDigitalTwinResponse.Body.Thermostat1.TargetTemperature;
  if (currentComponentTargetTemperature != null)
  {
      DateTimeOffset targetTemperatureDesiredLastUpdateTime = getDigitalTwinResponse.Body.Thermostat1.Metadata.TargetTemperature.LastUpdateTime;

      // The property path to be replaced should be prepended with a '/'
      updateOperation.AppendReplacePropertyOp("/thermostat1/targetTemperature", desiredTargetTemperature);
  }
  else
  {
      // The property path to be added should be prepended with a '/'
      updateOperation.AppendAddPropertyOp("/thermostat1/targetTemperature", desiredTargetTemperature);
  }
}
else
{
    // Thermostat1 is not present in the TemperatureController twin. Add the component.
    var componentProperty = new Dictionary<string, object> { { "targetTemperature", desiredTargetTemperature }, { "$metadata", new object() } };

    // The property path to be replaced should be prepended with a '/'
    updateOperation.AppendAddComponentOp("/thermostat1", componentProperty);
}

HttpOperationHeaderResponse<DigitalTwinUpdateHeaders> updateDigitalTwinResponse = await digitalTwinClient
    .UpdateDigitalTwinAsync(digitalTwinId, updateOperation.Serialize());

Console.WriteLine($"Update {digitalTwinId} digital twin response: {updateDigitalTwinResponse.Response.StatusCode}.");
```

### <a name="call-command"></a>Příkaz call

Následující fragment kódu ukazuje, jak vyvolat `getMaxMinReport` příkaz definovaný ve výchozí komponentě:

```csharp
DateTimeOffset since = DateTimeOffset.Now.Subtract(TimeSpan.FromMinutes(2));

try
{
  HttpOperationResponse<DigitalTwinCommandResponse, DigitalTwinInvokeCommandHeaders> invokeCommandResponse = await digitalTwinClient
    .InvokeCommandAsync(digitalTwinId, "getMaxMinReport", JsonConvert.SerializeObject(since));

  Console.WriteLine($"Command getMaxMinReport was invoked. \nDevice returned status: {invokeCommandResponse.Body.Status}." +
    $"\nReport: {invokeCommandResponse.Body.Payload}");
}
catch (HttpOperationException e)
{
  if (e.Response.StatusCode == HttpStatusCode.NotFound)
  {
    Console.WriteLine($"Unable to execute command getMaxMinReport on {digitalTwinId}.");
  }
}
```

Následující fragment kódu ukazuje, jak zavolat `getMaxMinReport` příkaz na komponentu. Příkaz je definován v součásti **Thermostat1** :

```csharp
DateTimeOffset since = DateTimeOffset.Now.Subtract(TimeSpan.FromMinutes(2));

try
{
    HttpOperationResponse<DigitalTwinCommandResponse, DigitalTwinInvokeCommandHeaders> invokeCommandResponse = await digitalTwinClient
        .InvokeComponentCommandAsync(digitalTwinId, "thermostat1", "getMaxMinReport", JsonConvert.SerializeObject(since));

    Console.WriteLine("Command getMaxMinReport was invoked on component thermostat1." +
        $"\nDevice returned status: {invokeCommandResponse.Body.Status}. \nReport: {invokeCommandResponse.Body.Payload}");
}
catch (HttpOperationException e)
{
    if (e.Response.StatusCode == HttpStatusCode.NotFound)
    {
        Console.WriteLine("Unable to execute command getMaxMinReport on component thermostat1.");
    }
}
```

## <a name="read-device-telemetry"></a>Čtení telemetrie zařízení

Zařízení IoT technologie Plug and Play odesílají telemetrii definovaná v modelu DTDL k IoT Hub. Ve výchozím nastavení IoT Hub směruje telemetrii do koncového bodu Event Hubs, kde ho můžete spotřebovat. Další informace najdete v tématu [použití směrování zpráv IoT Hub k posílání zpráv ze zařízení do cloudu do různých koncových bodů](../articles/iot-hub/iot-hub-devguide-messages-d2c.md).

Následující fragment kódu ukazuje, jak číst telemetrii z výchozího koncového bodu Event Hubs. Kód v tomto fragmentu kódu je pořízen z rychlého startu IoT Hub [Odeslat telemetrii ze zařízení do služby IoT Hub a přečíst si ho pomocí back-endové aplikace](../articles/iot-hub/quickstart-send-telemetry-dotnet.md):

```csharp
await using EventHubConsumerClient consumer = new EventHubConsumerClient(EventHubConsumerClient.DefaultConsumerGroupName, connectionString, EventHubName);

Console.WriteLine("Listening for messages on all partitions");

try
{
    await foreach (PartitionEvent partitionEvent in consumer.ReadEventsAsync(cancellationToken))
    {
        Console.WriteLine("Message received on partition {0}:", partitionEvent.Partition.PartitionId);

        string data = Encoding.UTF8.GetString(partitionEvent.Data.Body.ToArray());
        Console.WriteLine("\t{0}:", data);

        Console.WriteLine("Application properties (set by device):");
        foreach (var prop in partitionEvent.Data.Properties)
        {
            Console.WriteLine("\t{0}: {1}", prop.Key, prop.Value);
        }

        Console.WriteLine("System properties (set by IoT Hub):");
        foreach (var prop in partitionEvent.Data.SystemProperties)
        {
            Console.WriteLine("\t{0}: {1}", prop.Key, prop.Value);
        }
    }
}
catch (TaskCanceledException)
{
    // This is expected when the token is signaled; it should not be considered an
    // error in this scenario.
}
```

Následující výstup z předchozího kódu ukazuje telemetrii teploty odeslanou **termostatem** zařízení IoT technologie Plug and Play, který má pouze výchozí komponentu. `dt-dataschema`Vlastnost System zobrazuje ID modelu:

```cmd/sh
Message received on partition 1:
        { "temperature": 25.5 }:
Application properties (set by device):
System properties (set by IoT Hub):
        iothub-connection-device-id: my-pnp-device
        iothub-connection-auth-method: {"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}
        iothub-connection-auth-generation-id: 637375045610235418
        iothub-enqueuedtime: 05/10/2020 14:30:58
        iothub-message-source: Telemetry
        dt-dataschema: dtmi:com:example:Thermostat;1
        content-type: application/json
        content-encoding: utf-8
```

Následující výstup z předchozího kódu ukazuje telemetrii o teplotě, kterou posílá **TemperatureController** IoT technologie Plug and Play zařízení s více komponentami. `dt-subject`Vlastnost System zobrazuje název součásti, která tuto telemetrii poslala. V tomto příkladu jsou dvě komponenty `thermostat1` a jak jsou `thermostat2` definovány v modelu DTDL. `dt-dataschema`Vlastnost System zobrazuje ID modelu:

```cmd/sh
Message received on partition 1:
        {"temperature":11.1}:
Application properties (set by device):
System properties (set by IoT Hub):
        dt-subject: thermostat1
        iothub-connection-device-id: my-pnp-device
        iothub-connection-auth-method: {"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}
        iothub-connection-auth-generation-id: 637375045610235418
        iothub-enqueuedtime: 05/10/2020 14:23:36
        iothub-message-source: Telemetry
        dt-dataschema: dtmi:com:example:TemperatureController;1
        content-type: application/json
        content-encoding: utf-8
Message received on partition 1:
        {"temperature":41.2}:
Application properties (set by device):
System properties (set by IoT Hub):
        dt-subject: thermostat2
        iothub-connection-device-id: my-pnp-device
        iothub-connection-auth-method: {"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}
        iothub-connection-auth-generation-id: 637375045610235418
        iothub-enqueuedtime: 05/10/2020 14:23:36
        iothub-message-source: Telemetry
        dt-dataschema: dtmi:com:example:TemperatureController;1
        content-type: application/json
        content-encoding: utf-8
```

## <a name="read-device-twin-change-notifications"></a>Přečíst oznámení o zdvojených změnách zařízení

Můžete nakonfigurovat IoT Hub pro generování oznámení o nedoručení zařízení, která budou směrovat do podporovaného koncového bodu. Další informace najdete v tématu [použití směrování zpráv IoT Hub k posílání zpráv typu zařízení-Cloud do různých koncových bodů > události netelemetrie](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

Kód zobrazený v předchozím fragmentu kódu v jazyce C# generuje následující výstup, když IoT Hub vygeneruje oznámení o zdvojení zařízení pro termostat na zařízení bez součásti. Vlastnosti aplikace `iothub-message-schema` a `opType` poskytují informace o typu oznámení o změně:

```cmd/sh
Message received on partition 1:
        {"version":3,"properties":{"reported":{"maxTempSinceLastReboot":9.6,"$metadata":{"$lastUpdated":"2020-10-06T10:17:41.7408552Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-06T10:17:41.7408552Z"}},"$version":2}}}:
Application properties (set by device):
        hubName: my-pnp-hub
        deviceId: my-pnp-device
        operationTimestamp: 2020-10-06T10:17:41.7408552+00:00
        iothub-message-schema: twinChangeNotification
        opType: updateTwin
System properties (set by IoT Hub):
        iothub-connection-device-id: my-pnp-device
        iothub-enqueuedtime: 06/10/2020 10:17:41
        iothub-message-source: twinChangeEvents
        user-id: System.ArraySegment`1[System.Byte]
        correlation-id: 61394e8ba7d
        content-type: application/json
        content-encoding: utf-8
```

Kód zobrazený v předchozím fragmentu kódu v jazyce C# generuje následující výstup, když IoT Hub pro zařízení s komponentami generuje oznámení o zdvojených změnách zařízení. Tento příklad ukazuje výstup, když zařízení snímače teploty s termostatovou komponentou generuje oznámení. Vlastnosti aplikace `iothub-message-schema` a `opType` poskytují informace o typu oznámení o změně:

```cmd/sh
Message received on partition 1:
        {"version":5,"properties":{"reported":{"thermostat1":{"__t":"c","maxTempSinceLastReboot":9.6},"$metadata":{"$lastUpdated":"2020-10-06T10:27:59.515972Z","thermostat1":{"$lastUpdated":"2020-10-06T10:27:59.515972Z","__t":{"$lastUpdated":"2020-10-06T10:27:59.515972Z"},"maxTempSinceLastReboot":{"$lastUpdated":"2020-10-06T10:27:59.515972Z"}}},"$version":4}}}:
Application properties (set by device):
        hubName: my-pnp-hub
        deviceId: my-pnp-device
        operationTimestamp: 2020-10-06T10:27:59.5159720+00:00
        iothub-message-schema: twinChangeNotification
        opType: updateTwin
System properties (set by IoT Hub):
        iothub-connection-device-id: my-pnp-device
        iothub-enqueuedtime: 06/10/2020 10:27:59
        iothub-message-source: twinChangeEvents
        user-id: System.ArraySegment`1[System.Byte]
        correlation-id: 615051f364e
        content-type: application/json
        content-encoding: utf-8
```

## <a name="read-digital-twin-change-notifications"></a>Číst oznámení o změně digitálního vlákna

Můžete nakonfigurovat IoT Hub pro generování oznámení o změně digitálního vlákna pro směrování do podporovaného koncového bodu. Další informace najdete v tématu [použití směrování zpráv IoT Hub k posílání zpráv typu zařízení-Cloud do různých koncových bodů > události netelemetrie](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

Kód zobrazený v předchozím fragmentu kódu v jazyce C# generuje následující výstup, když IoT Hub generuje pro zařízení termostatu bez komponenty žádné oznámení o změně digitálního vlákna. Vlastnosti aplikace `iothub-message-schema` a `opType` poskytují informace o typu oznámení o změně:

```cmd/sh
Message received on partition 1:
        [{"op":"add","path":"/$metadata/maxTempSinceLastReboot","value":{"lastUpdateTime":"2020-10-06T10:39:16.0209836Z"}},{"op":"add","path":"/maxTempSinceLastReboot","value":34.9}]:
Application properties (set by device):
        hubName: my-pnp-hub
        deviceId: my-pnp-device
        operationTimestamp: 2020-10-06T10:39:16.0209836+00:00
        iothub-message-schema: digitalTwinChangeNotification
        opType: updateTwin
System properties (set by IoT Hub):
        iothub-connection-device-id: my-pnp-device
        iothub-enqueuedtime: 06/10/2020 10:39:16
        iothub-message-source: digitalTwinChangeEvents
        user-id: System.ArraySegment`1[System.Byte]
        correlation-id: 6169857bf8c
        content-type: application/json-patch+json
        content-encoding: utf-8
```

Kód zobrazený v předchozím fragmentu kódu v jazyce C# generuje následující výstup, když IoT Hub generuje upozornění na digitální dvojitou změnu pro zařízení s komponentami. Tento příklad ukazuje výstup, když zařízení snímače teploty s termostatovou komponentou generuje oznámení. Vlastnosti aplikace `iothub-message-schema` a `opType` poskytují informace o typu oznámení o změně:

```cmd/sh
Message received on partition 1:
        [{"op":"add","path":"/thermostat1","value":{"$metadata":{"maxTempSinceLastReboot":{"lastUpdateTime":"2020-10-06T10:41:44.8312666Z"}},"maxTempSinceLastReboot":29.1}}]:
Application properties (set by device):
        hubName: my-pnp-hub
        deviceId: my-pnp-device
        operationTimestamp: 2020-10-06T10:41:44.8312666+00:00
        iothub-message-schema: digitalTwinChangeNotification
        opType: updateTwin
System properties (set by IoT Hub):
        iothub-connection-device-id: my-pnp-device
        iothub-enqueuedtime: 06/10/2020 10:41:44
        iothub-message-source: digitalTwinChangeEvents
        user-id: System.ArraySegment`1[System.Byte]
        correlation-id: 616f108f0e3
        content-type: application/json-patch+json
        content-encoding: utf-8
```
