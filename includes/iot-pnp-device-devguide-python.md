---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/19/2020
ms.openlocfilehash: f4eb312aff200389f59a3e342305b8eda98f213e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104582785"
---
## <a name="model-id-announcement"></a>Oznámení ID modelu

Aby bylo možné oznámit ID modelu, musí ho zařízení zahrnout do informací o připojení:

```python
device_client = IoTHubDeviceClient.create_from_symmetric_key(
    symmetric_key=symmetric_key,
    hostname=registration_result.registration_state.assigned_hub,
    device_id=registration_result.registration_state.device_id,
    product_info=model_id,
)
```

> [!TIP]
> Pro moduly a IoT Edge použijte `IoTHubModuleClient` místo `IoTHubDeviceClient` .

> [!TIP]
> Toto je jediná doba, kterou může zařízení nastavit ID modelu. po připojení zařízení ho nejde aktualizovat.

## <a name="dps-payload"></a>Datová část DPS

Zařízení, která používají [službu Device Provisioning (DPS)](../articles/iot-dps/about-iot-dps.md) , můžou zahrnovat, `modelId` aby se během procesu zřizování používala tato datová část JSON.

```json
{
    "modelId" : "dtmi:com:example:Thermostat;1"
}
```

## <a name="implement-telemetry-properties-and-commands"></a>Implementace telemetrie, vlastností a příkazů

Jak je popsáno v tématu [pochopení komponent v modelech IoT technologie Plug and Play](../articles/iot-pnp/concepts-modeling-guide.md), musí tvůrci zařízení rozhodnout, jestli chtějí používat komponenty k popisu jejich zařízení. Při používání komponent musí zařízení splňovat pravidla popsaná v této části.

### <a name="telemetry"></a>Telemetrie

Výchozí součást nevyžaduje žádnou zvláštní vlastnost.

Při použití vnořených komponent musí zařízení nastavit vlastnost zprávy s názvem komponenty:

```python
async def send_telemetry_from_temp_controller(device_client, telemetry_msg, component_name=None):
    msg = Message(json.dumps(telemetry_msg))
    msg.content_encoding = "utf-8"
    msg.content_type = "application/json"
    if component_name:
        msg.custom_properties["$.sub"] = component_name
    await device_client.send_message(msg)
}
```

### <a name="read-only-properties"></a>Vlastnosti jen pro čtení

Vytváření sestav vlastnosti z výchozí komponenty nevyžaduje žádnou speciální konstrukci:

```python
await device_client.patch_twin_reported_properties({"maxTempSinceLastReboot": 38.7})
```

Nevlákenná zpráva zařízení se aktualizuje o další hlášené vlastnosti:

```json
{
  "reported": {
      "maxTempSinceLastReboot" : 38.7
  }
}
```

Při použití vnořených komponent musí být v rámci názvu komponenty vytvořeny vlastnosti:

```python
inner_dict = {}
inner_dict["targetTemperature"] = 38.7
inner_dict["__t"] = "c"
prop_dict = {}
prop_dict["thermostat1"] = inner_dict

await device_client.patch_twin_reported_properties(prop_dict)
```

Nevlákenná zpráva zařízení se aktualizuje o další hlášené vlastnosti:

```json
{
  "reported": {
    "thermostat1" : {  
      "__t" : "c",  
      "maxTempSinceLastReboot" : 38.7
     }
  }
}
```

### <a name="writable-properties"></a>Vlastnosti s možností zápisu

Tyto vlastnosti může nastavit zařízení nebo aktualizovat řešení. Pokud řešení aktualizuje vlastnost, klient obdrží oznámení jako zpětné volání v `IoTHubDeviceClient` nebo `IoTHubModuleClient` . Aby bylo možné postupovat podle konvencí technologie Plug and Play IoT, zařízení musí informovat službu o tom, že byla vlastnost úspěšně přijata.

#### <a name="report-a-writable-property"></a>Nahlásit vlastnost s možností zápisu

Když zařízení ohlásí vlastnost s možností zápisu, musí zahrnovat `ack` hodnoty definované v konvencích.

Chcete-li nahlásit vlastnost s možností zápisu z výchozí komponenty:

```python
prop_dict = {}
prop_dict["targetTemperature"] = {
    "ac": 200,
    "ad": "reported default value",
    "av": 0,
    "value": 23.2
}

await device_client.patch_twin_reported_properties(prop_dict)
```

Nevlákenná zpráva zařízení se aktualizuje o další hlášené vlastnosti:

```json
{
  "reported": {
    "targetTemperature": {
      "value": 23.2,
      "ac": 200,
      "av": 0,
      "ad": "reported default value"
    }
  }
}
```

Chcete-li nahlásit vlastnost s možností zápisu z vnořené komponenty, musí mít vlákna značku:

```python
inner_dict = {}
inner_dict["targetTemperature"] = {
    "ac": 200,
    "ad": "reported default value",
    "av": 0,
    "value": 23.2
}
inner_dict["__t"] = "c"
prop_dict = {}
prop_dict["thermostat1"] = inner_dict

await device_client.patch_twin_reported_properties(prop_dict)
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
          "av": 0,
          "ad": "complete"
      }
    }
  }
}
```

#### <a name="subscribe-to-desired-property-updates"></a>Přihlášení k odběru požadovaných aktualizací vlastností

Služby mohou aktualizovat požadované vlastnosti, které aktivují oznámení na připojených zařízeních. Toto oznámení zahrnuje aktualizované požadované vlastnosti, včetně čísla verze identifikující aktualizaci. Zařízení musí odpovídat stejné `ack` zprávě jako hlášené vlastnosti.

Výchozí komponenta vidí jedinou vlastnost a vytvoří hlášené `ack` s obdrženou verzí:

```python
async def execute_property_listener(device_client):
    ignore_keys = ["__t", "$version"]
    while True:
        patch = await device_client.receive_twin_desired_properties_patch()  # blocking call

        version = patch["$version"]
        prop_dict = {}

        for prop_name, prop_value in patch.items():
            if prop_name in ignore_keys:
                continue
            else:
                prop_dict[prop_name] = {
                    "ac": 200,
                    "ad": "Successfully executed patch",
                    "av": version,
                    "value": prop_value,
                }

        await device_client.patch_twin_reported_properties(prop_dict)
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

```python
def create_reported_properties_from_desired(patch):
    ignore_keys = ["__t", "$version"]
    component_prefix = list(patch.keys())[0]
    values = patch[component_prefix]

    version = patch["$version"]
    inner_dict = {}

    for prop_name, prop_value in values.items():
        if prop_name in ignore_keys:
            continue
        else:
            inner_dict["ac"] = 200
            inner_dict["ad"] = "Successfully executed patch"
            inner_dict["av"] = version
            inner_dict["value"] = prop_value
            values[prop_name] = inner_dict

    properties_dict = dict()
    if component_prefix:
        properties_dict[component_prefix] = values
    else:
        properties_dict = values

    return properties_dict

async def execute_property_listener(device_client):
    while True:
        patch = await device_client.receive_twin_desired_properties_patch()  # blocking call
        properties_dict = create_reported_properties_from_desired(patch)

        await device_client.patch_twin_reported_properties(properties_dict)
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

Výchozí komponenta přijímá název příkazu, který byl vyvolán službou.

Vnořená komponenta přijímá název příkazu s předponou názvu komponenty a `*` oddělovače.

```python
command_request = await device_client.receive_method_request("thermostat1*reboot")
```

#### <a name="request-and-response-payloads"></a>Datové části žádosti a odpovědi

Příkazy používají typy k definování svých datových částí požadavků a odpovědí. Zařízení musí deserializovat příchozí vstupní parametr a serializovat odpověď. Následující příklad ukazuje, jak implementovat příkaz se složitými typy definovanými v datových vytíženích:

```json
{
  "@type": "Command",
  "name": "getMaxMinReport",
  "displayName": "Get Max-Min report.",
  "description": "This command returns the max, min and average temperature from the specified time to the current time.",
  "request": {
    "name": "since",
    "displayName": "Since",
    "description": "Period to return the max-min report.",
    "schema": "dateTime"
  },
  "response": {
    "name" : "tempReport",
    "displayName": "Temperature Report",
    "schema": {
      "@type": "Object",
      "fields": [
        {
          "name": "maxTemp",
          "displayName": "Max temperature",
          "schema": "double"
        },
        {
          "name": "minTemp",
          "displayName": "Min temperature",
          "schema": "double"
        },
        {
          "name" : "avgTemp",
          "displayName": "Average Temperature",
          "schema": "double"
        },
        {
          "name" : "startTime",
          "displayName": "Start Time",
          "schema": "dateTime"
        },
        {
          "name" : "endTime",
          "displayName": "End Time",
          "schema": "dateTime"
        }
      ]
    }
  }
}
```

Následující fragmenty kódu ukazují, jak zařízení implementuje tuto definici příkazu, včetně typů používaných pro povolení serializace a deserializace:

```python
def create_max_min_report_response(values):
    response_dict = {
        "maxTemp": max_temp,
        "minTemp": min_temp,
        "avgTemp": sum(avg_temp_list) / moving_window_size,
        "startTime": (datetime.now() - timedelta(0, moving_window_size * 8)).isoformat(),
        "endTime": datetime.now().isoformat(),
    }
    # serialize response dictionary into a JSON formatted str
    response_payload = json.dumps(response_dict, default=lambda o: o.__dict__, sort_keys=True)
    return response_payload
```

> [!Tip]
> Název žádosti a odpovědi nejsou k dispozici v serializovaných datových vytíženích přenášených přes tento kabel.
