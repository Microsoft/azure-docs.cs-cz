---
title: Příručka pro vývojáře – IoT technologie Plug and Play Preview | Microsoft Docs
description: Popis technologie Plug and Play IoT pro vývojáře
author: rido-min
ms.author: rmpablos
ms.date: 07/16/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: ef221ea068f2786a4a84f20a29e80dd7176f06c6
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/28/2020
ms.locfileid: "87337411"
---
# <a name="iot-plug-and-play-preview-developer-guide"></a>Příručka pro vývojáře pro IoT technologie Plug and Play Preview

IoT technologie Plug and Play Preview umožňuje vytvářet inteligentní zařízení, která budou inzerovat jejich schopnosti aplikacím Azure IoT. Zařízení IoT technologie Plug and Play nevyžadují ruční konfiguraci, když ji zákazník připojí k aplikacím s podporou IoT technologie Plug and Play.

Tato příručka popisuje základní kroky potřebné k vytvoření zařízení, které následuje po [konvencích technologie Plug and Play IoT](concepts-convention.md), a dostupná rozhraní REST API, která můžete použít ke komunikaci se zařízením.

Pokud chcete vytvořit zařízení IoT technologie Plug and Play, postupujte podle kroků následujících:

1. Ujistěte se, že zařízení používá protokol WebSockets MQTT nebo MQTT pro připojení k Azure IoT Hub.
1. Vytvořte model [DTDL (Digital s definicemi jazyků)](https://github.com/Azure/opendigitaltwins-dtdl) pro popis zařízení. Další informace najdete v tématu [pochopení komponent v modelech IoT technologie Plug and Play](concepts-components.md).
1. Aktualizujte zařízení tak, aby informovalo `model-id` jako součást připojení zařízení.
1. Implementace telemetrie, vlastností a příkazů pomocí [konvencí technologie Plug and Play IoT](concepts-convention.md)

Jakmile je vaše implementace zařízení připravená, použijte [Azure IoT Explorer](howto-use-iot-explorer.md) a ověřte, jestli zařízení dodržuje konvence technologie Plug and Play IoT.

> [!Tip]
> Všechny fragmenty kódu v tomto článku používají C#, ale koncepty platí pro všechny dostupné sady SDK pro jazyky C, Python, Node a Java.

## <a name="model-id-announcement"></a>Oznámení ID modelu

Aby bylo možné oznámit ID modelu, musí ho zařízení zahrnout do informací o připojení:

```csharp
DeviceClient.CreateFromConnectionString(
  connectionString,
  TransportType.Mqtt,
  new ClientOptions() { ModelId = modelId })
```

Nové `ClientOptions` přetížení je k dispozici ve všech metodách, které se `DeviceClient` používají k inicializaci připojení.

Oznámení ID modelu bylo přidáno do následujících verzí sad SDK.

|Sada SDK|Verze|
|---|-------|
|C-SDK|1.3.9|
|.NET|1.27.0|
|Java|1.14.0|
|Uzel|1.17.0|
|Python|2.1.4|

## <a name="implement-telemetry-properties-and-commands"></a>Implementace telemetrie, vlastností a příkazů

Jak je popsáno v tématu [pochopení komponent v modelech IoT technologie Plug and Play](concepts-components.md), musí tvůrci zařízení rozhodnout, jestli chtějí používat komponenty k popisu jejich zařízení. Při používání komponent musí zařízení splňovat pravidla popsaná v této části.

### <a name="telemetry"></a>Telemetrie

Modely bez komponent nevyžadují žádnou speciální vlastnost.

Při použití komponent musí zařízení nastavit vlastnost zprávy s názvem komponenty:

```c#
public async Task SendComponentTelemetryValueAsync(string componentName, string serializedTelemetry)
{
  var message = new Message(Encoding.UTF8.GetBytes(serializedTelemetry));
  message.Properties.Add("$.sub", componentName);
  message.ContentType = "application/json";
  message.ContentEncoding = "utf-8";
  await deviceClient.SendEventAsync(message);
}
```

### <a name="read-only-properties"></a>Vlastnosti jen pro čtení

Modely bez komponent nevyžadují žádné speciální konstrukce:

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

Při použití komponent musí být v rámci názvu komponenty vytvořeny vlastnosti:

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

Tyto vlastnosti může nastavit zařízení nebo aktualizovat řešení. Pokud řešení aktualizuje vlastnost, klient obdrží oznámení jako zpětné volání v `DeviceClient` . Aby bylo možné postupovat podle konvencí technologie Plug and Play IoT, zařízení musí informovat službu o tom, že byla vlastnost úspěšně přijata.

#### <a name="report-a-writable-property"></a>Nahlásit vlastnost s možností zápisu

Když zařízení ohlásí vlastnost s možností zápisu, musí zahrnovat `ack` hodnoty definované v konvencích.

Chcete-li ohlásit vlastnost s možností zápisu bez součástí:

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

Chcete-li nahlásit vlastnost s možností zápisu ze součásti, musí mít vlákna značku:

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

Modely bez komponent Vidí jedinou vlastnost a vytvoří hlášené `ack` s obdrženou verzí:

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

Modely s komponentami dostanou požadované vlastnosti zabalené s názvem komponenty a měly by nahlásit zpět `ack` ohlášenou vlastnost:

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

Vyplňování zařízení pro součásti zobrazuje požadované a hlášené oddíly následujícím způsobem:

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

Modely bez komponent obdrží název příkazu, jak ho služba vyvolala.

Modely s komponentami obdrží název příkazu s předponou komponenty a `*` oddělovače.

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

## <a name="interact-with-the-device"></a>Interakce se zařízením 

IoT technologie Plug and Play umožňuje používat zařízení, která oznámila ID modelu ve službě IoT Hub. Můžete například získat přímý přístup k vlastnostem a příkazům zařízení.

Pokud chcete používat technologie Plug and Play IoT, která je připojená ke službě IoT Hub, použijte buď IoT Hub REST API nebo jednu z jazykových sad IoT. V následujících příkladech se používá REST API IoT Hub. Aktuální verze rozhraní API je `2020-05-31-preview` . Připojí `?api-version=2020-05-31` se k voláním REST PI.

Pokud se zavolá zařízení termostata `t-123` , zobrazí se všechny vlastnosti všech rozhraní implementovaných vaším zařízením a volání REST API Get:

```REST
GET /digitalTwins/t-123
```

Toto volání bude obsahovat vlastnost JSON `$metadata.$model` s ID modelu oznámeným zařízením.

Všechny vlastnosti pro všechna rozhraní jsou k dispozici pomocí `GET /DigitalTwin/{device-id}` šablony REST API, kde `{device-id}` je identifikátor zařízení:

```REST
GET /digitalTwins/{device-id}
```

Můžete volat přímo technologie Plug and Play příkazy pro zařízení IoT. Pokud `Thermostat` komponenta v `t-123` zařízení obsahuje `restart` příkaz, můžete ji zavolat voláním REST API post:

```REST
POST /digitalTwins/t-123/components/Thermostat/commands/restart
```

Obecně lze příkazy volat pomocí této šablony REST API:

- `device-id`: identifikátor zařízení.
- `component-name`: název rozhraní z oddílu Implements v modelu schopností zařízení.
- `command-name`: název příkazu.

```REST
/digitalTwins/{device-id}/components/{component-name}/commands/{command-name}
```

## <a name="next-steps"></a>Další kroky

Teď, když jste se dozvěděli o modelování zařízení, tady je několik dalších prostředků:

- [Jazyk DTDL (Digital autodefinition Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl)
- [Sada SDK pro zařízení jazyka C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [REST API IoT](https://docs.microsoft.com/rest/api/iothub/device)
- [Komponenty modelu](./concepts-components.md)
