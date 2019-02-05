---
title: zahrnout soubor
description: zahrnout soubor
services: iot-accelerators
author: dominicbetts
ms.service: iot-accelerators
ms.topic: include
ms.date: 07/26/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 414bb0183e68cb46e52c379ea3f7aceda5d4170e
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2019
ms.locfileid: "55701178"
---
## <a name="the-parts-of-the-device-model-schema"></a>Části schématu modelu zařízení

Každý model zařízení, jako je například chladič nebo nákladní vůz, definuje typ zařízení, které můžete simulovat službu simulace. Každý model zařízení je uložen v souboru JSON s následující nejvyšší úrovni schématu:

```json
{
  "SchemaVersion": "1.0.0",
  "Id": "elevator-01",
  "Version": "0.0.1",
  "Name": "Elevator",
  "Description": "Elevator with floor, vibration and temperature sensors.",
  "Protocol": "AMQP",
  "Simulation": {
    // Specify the simulation behavior
  },
  "Properties": {
    // Define properties
  },
  "Telemetry": [
    // Specify telemetry
  ],
  "CloudToDeviceMethods": {
    // Specify methods
  }
}
```

Můžete zobrazit soubory schémat pro zařízení s Simulovaná výchozí [devicemodels složky](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels) na Githubu.

Následující tabulka popisuje položky nejvyšší úrovně schématu:

| Položka schématu | Popis |
| -- | --- |
| `SchemaVersion` | Verze schématu je vždy `1.0.0` a je specifické pro formát tohoto souboru. |
| `Id` | Jedinečné ID pro tento model zařízení. |
| `Version` | Určuje verzi model zařízení. |
| `Name` | Popisný název pro model zařízení. |
| `Description` | Popis model zařízení. |
| `Protocol` | Protokol připojení zařízení používá. Může být jedna z `AMQP`, `MQTT`, a `HTTP`. |

Následující části popisují ostatních oddílů ve schématu JSON:

## <a name="simulation"></a>Simulace

V `Simulation` části definujete vnitřní stav simulované zařízení. Všechny hodnoty telemetrie odeslané ze zařízení musí být součástí tohoto stavu zařízení.

Definice stavu zařízení má dva prvky:

* `InitialState` Definuje počáteční hodnoty pro všechny vlastnosti objektu stavu zařízení.
* `Script` identifikuje soubor jazyka JavaScript, která běží na plán, který chcete aktualizovat stav zařízení. Tento skript můžete použít pro náhodné hodnoty telemetrie odeslané ze zařízení.

Další informace o souboru JavaScriptu, která aktualizuje objekt stavu zařízení, najdete v článku [pochopení chování modelu zařízení](../articles/iot-accelerators/iot-accelerators-device-simulation-device-behavior.md).

Následující příklad ukazuje definici objektu stavu zařízení pro zařízení s Simulovaná chladič:

```json
"Simulation": {
  "InitialState": {
    "online": true,
    "temperature": 75.0,
    "temperature_unit": "F",
    "humidity": 70.0,
    "humidity_unit": "%",
    "pressure": 150.0,
    "pressure_unit": "psig",
    "simulation_state": "normal_pressure"
  },
  "Interval": "00:00:10",
  "Scripts": {
    "Type": "javascript",
    "Path": "chiller-01-state.js"
  }
}
```

Spuštění služby simulace **chladič-01-state.js** souboru každých pět sekund na Aktualizovat stav zařízení. Zobrazí soubory jazyka JavaScript pro zařízení s Simulovaná výchozí [složky scripts](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels/scripts) na Githubu. Podle konvence mají tyto soubory jazyka JavaScript příponu **– stav** abychom je odlišili od soubory, které implementují chování metody.

## <a name="properties"></a>Vlastnosti

`Properties` Části schéma definuje hodnoty vlastností zařízení odesílá do řešení. Příklad:

```json
"Properties": {
  "Type": "Elevator",
  "Location": "Building 2",
  "Latitude": 47.640792,
  "Longitude": -122.126258
}
```

Při spuštění řešení, vyžádá si všechna Simulovaná zařízení, která sestavení seznamu `Type` hodnot pro použití v uživatelském rozhraní. Toto řešení využívá `Latitude` a `Longitude` vlastnosti, které chcete přidat umístění zařízení do mapy na řídicím panelu.

## <a name="telemetry"></a>Telemetrická data

`Telemetry` Pole uvádí všechny typy telemetrie simulované zařízení odesílá do řešení.

Následující příklad odesílá telemetrické zprávy JSON každých 10 sekund s `floor`, `vibration`, a `temperature` data ze senzorů hodnocení:

```json
"Telemetry": [
  {
    "Interval": "00:00:10",
    "MessageTemplate": "{\"floor\":${floor},\"vibration\":${vibration},\"vibration_unit\":\"${vibration_unit}\",\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\"}",
    "MessageSchema": {
      "Name": "elevator-sensors;v1",
      "Format": "JSON",
      "Fields": {
        "floor": "integer",
        "vibration": "double",
        "vibration_unit": "text",
        "temperature": "double",
        "temperature_unit": "text"
      }
    }
  }
]
```

`MessageTemplate` definuje strukturu JSON zprávy odeslané ze simulovaného zařízení. Zástupné symboly v `MessageTemplate` syntaxí `${NAME}` kde `NAME` je klíč z [objektu stavu zařízení](#simulation). Řetězce by měla být v uvozovkách, čísla by neměla.

`MessageSchema` definuje schéma zprávy odeslané ze simulovaného zařízení. Schéma zprávy, jsou publikované do služby IoT Hub umožňuje back-endové aplikace opakovaně používat informace k interpretaci příchozí telemetrická data.

V současné době můžete použít pouze schémata zpráv JSON. Polí uvedených ve schématu může být z následujících typů:

* Objekt - serializovat pomocí JSON
* Binární – serializovanou ve formátu base64
* Text
* Logická hodnota
* Integer
* Double
* DateTime

K odesílání telemetrických zpráv v různých intervalech, přidat více typy telemetrie `Telemetry` pole. Následující příklad odesílá data o teplotě a vlhkosti každých 10 sekund a stav světla každou minutu:

```json
"Telemetry": [
  {
    "Interval": "00:00:10",
    "MessageTemplate":
      "{\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\",\"humidity\":\"${humidity}\"}",
    "MessageSchema": {
      "Name": "RoomComfort;v1",
      "Format": "JSON",
      "Fields": {
        "temperature": "double",
        "temperature_unit": "text",
        "humidity": "integer"
      }
    }
  },
  {
    "Interval": "00:01:00",
    "MessageTemplate": "{\"lights\":${lights_on}}",
    "MessageSchema": {
      "Name": "RoomLights;v1",
      "Format": "JSON",
      "Fields": {
        "lights": "boolean"
      }
    }
  }
],
```

## <a name="cloudtodevicemethods"></a>CloudToDeviceMethods

Simulované zařízení může reagovat na typu cloud zařízení, které metoda volá ze služby IoT hub. `CloudToDeviceMethods` Oddílu v souboru schématu modelu zařízení:

* Definuje metody, které simulované zařízení může reagovat.
* Identifikuje soubor jazyka JavaScript, který obsahuje logiku pro spuštění.

Simulované zařízení odesílá do služby IoT hub, který je připojený k seznam metod, které podporuje.

Další informace o soubor JavaScript, který implementuje chování zařízení, najdete v článku [pochopení chování modelu zařízení](../articles/iot-accelerators/iot-accelerators-device-simulation-device-behavior.md).

Následující příklad určuje tři podporované metody a soubory jazyka JavaScript, které implementují tyto metody:

```json
"CloudToDeviceMethods": {
  "Reboot": {
    "Type": "javascript",
    "Path": "Reboot-method.js"
  },
  "EmergencyValveRelease": {
    "Type": "javascript",
    "Path": "EmergencyValveRelease-method.js"
  },
  "IncreasePressure": {
    "Type": "javascript",
    "Path": "IncreasePressure-method.js"
  }
}
```

Zobrazí soubory jazyka JavaScript pro zařízení s Simulovaná výchozí [složky scripts](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels/scripts) na Githubu. Podle konvence mají tyto soubory jazyka JavaScript příponu **– metoda** abychom je odlišili od soubory, které implementují chování stavu.