---
title: Schéma zařízení v řešení vzdáleného monitorování - Azure | Microsoft Docs
description: Tento článek popisuje schématu JSON, který definuje simulované zařízení v řešení vzdáleného monitorování.
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 01/29/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 186eaee952435573a861d144195c3165e4940cc1
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="understand-the-device-model-schema"></a>Pochopení schéma modelu zařízení

Simulovaná zařízení v řešení vzdáleného monitorování můžete použít k testování své chování. Když nasadíte řešení vzdáleného monitorování, je automaticky zajištěna kolekce Simulovaná zařízení. Můžete přizpůsobit existující Simulovaná zařízení nebo vytvořit vlastní.

Tento článek popisuje schéma modelu zařízení, který určuje možnosti a chování simulované zařízení. Model zařízení je uložený v souboru JSON.

V následujících článcích se vztahují k aktuální článku:

* [Implementace chování modelu zařízení](../iot-suite/iot-suite-remote-monitoring-device-behavior.md) popisuje soubory JavaScript, kterou použijete k implementaci chování simulované zařízení.
* [Vytvoření nového simulovaného zařízení](iot-accelerators-remote-monitoring-test.md) vloží ho všechny společně a ukazuje, jak nasadit nový typ simulované zařízení do řešení.

V tomto článku získáte informace o těchto tématech:

>[!div class="checklist"]
> * Použít soubor JSON definovat model simulovaného zařízení
> * Zadejte vlastnosti simulované zařízení
> * Zadejte, které simulované zařízení odesílá telemetrii
> * Určete, které zařízení reaguje na metody cloud zařízení

## <a name="the-parts-of-the-device-model-schema"></a>Části schématu modelu zařízení

Každý model zařízení, jako je chladič nebo vůz, definuje typ simulované zařízení pro připojení k řešení vzdáleného monitorování. Každý model zařízení je uložena v souboru JSON s nejvyšší úrovně schéma následující:

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

Soubory schématu pro výchozí simulované zařízení v můžete zobrazit [devicemodels složky](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels) na Githubu.

Následující tabulka popisuje položky nejvyšší úrovně schématu:

| Schéma vstupu | Popis |
| -- | --- |
| `SchemaVersion` | Verze schématu je vždy `1.0.0` a je specifická pro formát tohoto souboru. |
| `Id` | Jedinečné ID pro tento model zařízení. |
| `Version` | Určuje verzi model zařízení. |
| `Name` | Popisný název pro model zařízení. |
| `Description` | Popis model zařízení. |
| `Protocol` | Používá protokol připojení zařízení. Může být jedna z `AMQP`, `MQTT`, a `HTTP`. |

Následující části popisují v dalších částech schématu JSON:

## <a name="simulation"></a>Simulace

V `Simulation` části definujete vnitřní stav simulované zařízení. Všechny hodnoty telemetrie poslal zařízení musí být součástí tento stav zařízení.

Definice stavu zařízení má dva elementy:

* `InitialState` Definuje počáteční hodnoty pro všechny vlastnosti objektu stavu zařízení.
* `Script` identifikuje soubor JavaScript, která běží na plán, který chcete aktualizovat stav zařízení. Tento soubor skriptu můžete náhodně přeskupit telemetrie hodnoty poslal zařízení.

Další informace o soubor JavaScript, která aktualizuje objekt stavu zařízení, najdete v části [pochopit chování modelu zařízení](iot-accelerators-remote-monitoring-device-behavior.md).

Následující příklad ukazuje definici objektu stavu zařízení pro chladič simulované zařízení:

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

Simulace service běží **chladič-01-state.js** souboru každých pět sekund na aktualizaci stavu zařízení. Zobrazí se soubory JavaScript pro výchozí simulované zařízení v [složka skripty](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels/scripts) na Githubu. Podle konvence, tyto soubory JavaScript mají příponu **-stavu** abychom je odlišili od soubory, které implementují metoda chování.

## <a name="properties"></a>Vlastnosti

`Properties` Části schéma definuje hodnoty vlastností sestavy zařízení do řešení. Příklad:

```json
"Properties": {
  "Type": "Elevator",
  "Location": "Building 2",
  "Latitude": 47.640792,
  "Longitude": -122.126258
}
```

Když se spustí řešení, vyžádá si všechna Simulovaná zařízení k vytvoření seznamu `Type` hodnot pro použití v uživatelském rozhraní. Toto řešení využívá `Latitiude` a `Longitude` vlastnosti, které chcete přidat umístění zařízení do mapy na řídicím panelu.

## <a name="telemetry"></a>Telemetrická data

`Telemetry` Pole jsou uvedeny všechny typy telemetrie simulované zařízení odesílá do řešení.

Následující příklad odešle zprávu telemetrie JSON každých 10 sekund s `floor`, `vibration`, a `temperature` dat ze senzorů hodnocení:

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

`MessageTemplate` definuje strukturu JSON zprávy odeslané simulované zařízení. Zástupné symboly v `MessageTemplate` použijte syntaxi `${NAME}` kde `NAME` je klíč z [objekt stavu zařízení](#simulation). Musí být v uvozovkách řetězců, čísel neměli.

`MessageSchema` definuje schéma zprávy odeslané simulované zařízení. Schéma zprávy je také publikovaný do služby IoT Hub, aby znovu použít informace interpretovat příchozí telemetrii back-end aplikace.

V současné době můžete použít pouze schémata zpráva JSON. Polí uvedených ve schématu může být z následujících typů:

* Objekt – serializovanou pomocí JSON
* Binární – serializovanou pomocí formátu base64.
* Text
* Logická hodnota
* Integer
* Double
* DateTime

Posílat telemetrické zprávy v různých intervalech, přidat více typů telemetrie tak, aby `Telemetry` pole. Následující příklad odesílá data teploty a vlhkosti každých 10 sekund a stav světlým každou minutu:

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

Simulované zařízení může reagovat na cloud zařízení metody volat z řešení vzdáleného monitorování. `CloudToDeviceMethods` Oddíl v souboru schématu modelu zařízení:

* Definuje metody, které simulované zařízení může reagovat.
* Identifikuje soubor JavaScript, který obsahuje logiku pro spuštění.

Simulované zařízení odesílá seznam metod, které podporuje řešení vzdáleného monitorování.

Další informace o soubor JavaScript, který implementuje chování zařízení, najdete v části [pochopit chování modelu zařízení](iot-accelerators-remote-monitoring-device-behavior.md).

Následující příklad určuje tři podporované metody a JavaScript souborů, které implementují tyto metody:

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

Zobrazí se soubory JavaScript pro výchozí simulované zařízení v [složka skripty](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels/scripts) na Githubu. Podle konvence, tyto soubory JavaScript mají příponu **– metoda** abychom je odlišili od soubory, které implementují chování stavu.

## <a name="next-steps"></a>Další postup

Tento článek popisuje postup vytvoření vlastního modelu vlastní simulované zařízení. Tento článek ukázal, jak na:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Použít soubor JSON definovat model simulovaného zařízení
> * Zadejte vlastnosti simulované zařízení
> * Zadejte, které simulované zařízení odesílá telemetrii
> * Určete, které zařízení reaguje na metody cloud zařízení

Nyní jste se naučili o schématu JSON, navrhované dalším krokem je další postup [implementovat chování simulovaného zařízení](iot-accelerators-remote-monitoring-device-behavior.md).

Další informace pro vývojáře o řešení vzdáleného monitorování najdete v části:

* [Referenční příručka pro vývojáře](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Průvodce řešením potíží pro vývojáře](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->