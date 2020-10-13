---
title: Příručka pro vývojáře zařízení (C#) – IoT technologie Plug and Play | Microsoft Docs
description: Popis technologie Plug and Play IoT pro vývojáře zařízení v C#
author: rido-min
ms.author: rmpablos
ms.date: 09/24/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: fd36006292de68e1433ccdfb721c1a4613d0658a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91579795"
---
# <a name="iot-plug-and-play-device-developer-guide-c"></a>Příručka pro vývojáře pro IoT technologie Plug and Play zařízení (C#)

[!INCLUDE [iot-pnp-device-devguide-selector.md](../../includes/iot-pnp-device-devguide-selector.md)]

[!INCLUDE [iot-pnp-device-devguide-intro.md](../../includes/iot-pnp-device-devguide-intro.md)]

## <a name="model-id-announcement"></a>Oznámení ID modelu

Aby bylo možné oznámit ID modelu, musí ho zařízení zahrnout do informací o připojení:

```csharp
DeviceClient.CreateFromConnectionString(
  connectionString,
  TransportType.Mqtt,
  new ClientOptions() { ModelId = modelId })
```

Nové `ClientOptions` přetížení je k dispozici ve všech metodách, které se `DeviceClient` používají k inicializaci připojení.

> [!TIP]
> Pro moduly a IoT Edge použijte `ModuleClient` místo `DeviceClient` .

## <a name="dps-payload"></a>Datová část DPS

Zařízení, která používají [službu Device Provisioning (DPS)](../iot-dps/about-iot-dps.md) , můžou zahrnovat, `modelId` aby se během procesu zřizování používala tato datová část JSON.

```json
{
    "modelId" : "dtmi:com:example:Thermostat;1"
}
```

## <a name="implement-telemetry-properties-and-commands"></a>Implementace telemetrie, vlastností a příkazů

Jak je popsáno v tématu [pochopení komponent v modelech IoT technologie Plug and Play](concepts-components.md), musí tvůrci zařízení rozhodnout, jestli chtějí používat komponenty k popisu jejich zařízení. Při používání komponent musí zařízení splňovat pravidla popsaná v této části.

### <a name="telemetry"></a>Telemetrie

Výchozí součást nevyžaduje žádnou zvláštní vlastnost.

Při použití vnořených komponent musí zařízení nastavit vlastnost zprávy s názvem komponenty:

```c#
public async Task SendComponentTelemetryValueAsync(string componentName, string serializedTelemetry)
{
  var message = new Message(Encoding.UTF8.GetBytes(serializedTelemetry));
  message.Properties.Add("$.sub", componentName);
  message.ContentType = "application/json";
  message.ContentEncoding = "utf-8";
  await client.SendEventAsync(message);
}
```

### <a name="read-only-properties"></a>Vlastnosti jen pro čtení

Vytváření sestav vlastnosti z výchozí komponenty nevyžaduje žádnou speciální konstrukci:

```csharp
TwinCollection reportedProperties = new TwinCollection();
reportedProperties["maxTemperature"] = 38.7;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

Nevlákenná zpráva zařízení se aktualizuje o další hlášené vlastnosti:

```json
{
  "reported": {
      "maxTemperature" : 38.7
  }
}
```

Při použití vnořených komponent musí být v rámci názvu komponenty vytvořeny vlastnosti:

```csharp
TwinCollection reportedProperties = new TwinCollection();
TwinCollection component = new TwinCollection();
component["maxTemperature"] = 38.7;
component["__t"] = "c"; // marker to identify a component
reportedProperties["thermostat1"] = component;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

Nevlákenná zpráva zařízení se aktualizuje o další hlášené vlastnosti:

```json
{
  "reported": {
    "thermostat1" : {  
      "__t" : "c",  
      "maxTemperature" : 38.7
     } 
  }
}
```

### <a name="writable-properties"></a>Vlastnosti s možností zápisu

Tyto vlastnosti může nastavit zařízení nebo aktualizovat řešení. Pokud řešení aktualizuje vlastnost, klient obdrží oznámení jako zpětné volání v `DeviceClient` nebo `ModuleClient` . Aby bylo možné postupovat podle konvencí technologie Plug and Play IoT, zařízení musí informovat službu o tom, že byla vlastnost úspěšně přijata.

#### <a name="report-a-writable-property"></a>Nahlásit vlastnost s možností zápisu

Když zařízení ohlásí vlastnost s možností zápisu, musí zahrnovat `ack` hodnoty definované v konvencích.

Chcete-li nahlásit vlastnost s možností zápisu z výchozí komponenty:

```csharp
TwinCollection reportedProperties = new TwinCollection();
TwinCollection ackProps = new TwinCollection();
ackProps["value"] = 23.2;
ackProps["ac"] = 200; // using HTTP status codes
ackProps["av"] = 0; // not readed from a desired property
ackProps["ad"] = "reported default value";
reportedProperties["targetTemperature"] = ackProps;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

Nevlákenná zpráva zařízení se aktualizuje o další hlášené vlastnosti:

```json
{
  "reported": {
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
  }
}
```

Chcete-li nahlásit vlastnost s možností zápisu z vnořené komponenty, musí mít vlákna značku:

```csharp
TwinCollection reportedProperties = new TwinCollection();
TwinCollection component = new TwinCollection();
TwinCollection ackProps = new TwinCollection();
component["__t"] = "c"; // marker to identify a component
ackProps["value"] = 23.2;
ackProps["ac"] = 200; // using HTTP status codes
ackProps["av"] = 0; // not read from a desired property
ackProps["ad"] = "reported default value";
component["targetTemperature"] = ackProps;
reportedProperties["thermostat1"] = component;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

Nevlákenná zpráva zařízení se aktualizuje o další hlášené vlastnosti:

```json
{
  "reported": {
    "thermostat1": {
      "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
    }
  }
}
```

#### <a name="subscribe-to-desired-property-updates"></a>Přihlášení k odběru požadovaných aktualizací vlastností

Služby mohou aktualizovat požadované vlastnosti, které aktivují oznámení na připojených zařízeních. Toto oznámení zahrnuje aktualizované požadované vlastnosti, včetně čísla verze identifikující aktualizaci. Zařízení musí odpovídat stejné `ack` zprávě jako hlášené vlastnosti.

Výchozí komponenta vidí jedinou vlastnost a vytvoří hlášené `ack` s obdrženou verzí:

```csharp
await client.SetDesiredPropertyUpdateCallbackAsync(async (desired, ctx) => 
{
  JValue targetTempJson = desired["targetTemperature"];
  double targetTemperature = targetTempJson.Value<double>();

  TwinCollection reportedProperties = new TwinCollection();
  TwinCollection ackProps = new TwinCollection();
  ackProps["value"] = targetTemperature;
  ackProps["ac"] = 200;
  ackProps["av"] = desired.Version; 
  ackProps["ad"] = "desired property received";
  reportedProperties["targetTemperature"] = ackProps;

  await client.UpdateReportedPropertiesAsync(reportedProperties);
}, null);
```

Doplňování zařízení zobrazuje vlastnost v požadovaných a nahlášených částech:

```json
{
  "desired" : {
    "targetTemperature": 23.2,
    "$version" : 3
  },
  "reported": {
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
  }
}
```

Vnořená komponenta přijímá požadované vlastnosti zabalené s názvem komponenty a měla by nahlásit zpět `ack` ohlášenou vlastnost:

```csharp
await client.SetDesiredPropertyUpdateCallbackAsync(async (desired, ctx) =>
{
  JObject thermostatComponent = desired["thermostat1"];
  JToken targetTempProp = thermostatComponent["targetTemperature"];
  double targetTemperature = targetTempProp.Value<double>();

  TwinCollection reportedProperties = new TwinCollection();
  TwinCollection component = new TwinCollection();
  TwinCollection ackProps = new TwinCollection();
  component["__t"] = "c"; // marker to identify a component
  ackProps["value"] = targetTemperature;
  ackProps["ac"] = 200; // using HTTP status codes
  ackProps["av"] = desired.Version; // not readed from a desired property
  ackProps["ad"] = "desired property received";
  component["targetTemperature"] = ackProps;
  reportedProperties["thermostat1"] = component;

  await client.UpdateReportedPropertiesAsync(reportedProperties);
}, null);
```

Vlákna zařízení pro vnořenou komponentu zobrazuje požadované a hlášené oddíly následujícím způsobem:

```json
{
  "desired" : {
    "thermostat1" : {
        "__t" : "c",
        "targetTemperature": 23.2,
    }
    "$version" : 3
  },
  "reported": {
    "thermostat1" : {
        "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
    }
  }
}
```

### <a name="commands"></a>Příkazy

Výchozí komponenta přijímá název příkazu, který byl vyvolán službou.

Vnořená komponenta přijímá název příkazu s předponou názvu komponenty a `*` oddělovače.

```csharp
await client.SetMethodHandlerAsync("themostat*reboot", (MethodRequest req, object ctx) =>
{
  Console.WriteLine("REBOOT");
  return Task.FromResult(new MethodResponse(200));
},
null);
```

#### <a name="request-and-response-payloads"></a>Datové části žádosti a odpovědi

Příkazy používají typy k definování svých datových částí požadavků a odpovědí. Zařízení musí deserializovat příchozí vstupní parametr a serializovat odpověď. Následující příklad ukazuje, jak implementovat příkaz se složitými typy definovanými v datových vytíženích:

```json
{
  "@type": "Command",
  "name": "start",
  "request": {
    "name": "startRequest",
    "schema": {
      "@type": "Object",
      "fields": [
        {
          "name": "startPriority",
          "schema": "integer"
        },
        {
          "name": "startMessage",
          "schema" : "string"
        }
      ]
    }
  },
  "response": {
    "name": "startReponse",
    "schema": {
      "@type": "Object",
      "fields": [
        {
            "name": "startupTime",
            "schema": "integer" 
        },
        {
          "name": "startupMessage",
          "schema": "string"
        }
      ]
    }
  }
}
```

Následující fragmenty kódu ukazují, jak zařízení implementuje tuto definici příkazu, včetně typů používaných pro povolení serializace a deserializace:

```csharp
class startRequest
{
  public int startPriority { get; set; }
  public string startMessage { get; set; }
}

class startResponse
{
  public int startupTime { get; set; }
  public string startupMessage { get; set; }
}

// ... 

await client.SetMethodHandlerAsync("start", (MethodRequest req, object ctx) =>
{
  var startRequest = JsonConvert.DeserializeObject<startRequest>(req.DataAsJson);
  Console.WriteLine($"Received start command with priority ${startRequest.startPriority} and ${startRequest.startMessage}");

  var startResponse = new startResponse
  {
    startupTime = 123,
    startupMessage = "device started with message " + startRequest.startMessage
  };

  string responsePayload = JsonConvert.SerializeObject(startResponse);
  MethodResponse response = new MethodResponse(Encoding.UTF8.GetBytes(responsePayload), 200);
  return Task.FromResult(response);
},null);
```

> [!Tip]
> Název žádosti a odpovědi nejsou k dispozici v serializovaných datových vytíženích přenášených přes tento kabel.

[!INCLUDE [iot-pnp-device-devguide-summary.md](../../includes/iot-pnp-device-devguide-summary.md)]
