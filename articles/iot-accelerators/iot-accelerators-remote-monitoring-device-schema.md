---
title: Schéma zařízení v řešení vzdáleného monitorování – Azure | Dokumenty společnosti Microsoft
description: Tento článek popisuje schéma JSON, které definuje simulované zařízení v řešení vzdáleného monitorování.
author: dominicbetts
manager: philmea
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 12/18/2018
ms.topic: conceptual
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: ac681bb13ccea49c7a2f566a6fcdb6adb8cec5bb
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683749"
---
# <a name="understand-the-device-model-schema"></a>Pochopení schématu modelu zařízení

K testování jeho chování můžete použít simulovaná zařízení v řešení vzdáleného monitorování. Řešení vzdáleného monitorování zahrnuje simulační službu zařízení pro spuštění simulovaných zařízení. Při nasazení řešení vzdáleného monitorování se automaticky zřídí kolekce simulovaných zařízení. Můžete přizpůsobit stávající simulovaná zařízení nebo vytvořit vlastní.

Tento článek popisuje schéma modelu zařízení, které určuje možnosti a chování simulovaného zařízení. Model zařízení je uložen v souboru JSON.

> [!NOTE]
> Toto schéma modelu zařízení je pouze pro simulovaná zařízení hostovaná ve službě simulace zařízení. Pokud chcete vytvořit skutečné zařízení, přečtěte si informace [o připojení zařízení k akcelerátoru řešení vzdáleného monitorování](iot-accelerators-connecting-devices.md).

Následující články se vztahují k aktuálnímu článku:

* [Implementujte chování modelu zařízení](iot-accelerators-remote-monitoring-device-behavior.md) popisuje soubory JavaScriptu, které používáte k implementaci chování simulovaného zařízení.
* [Vytvořte nové simulované zařízení,](iot-accelerators-remote-monitoring-create-simulated-device.md) které to všechno dohromady dá dohromady a ukáže vám, jak nasadit nový typ simulovaného zařízení do vašeho řešení.

V tomto článku získáte informace o těchto tématech:

>[!div class="checklist"]
> * Definování simulovaného modelu zařízení pomocí souboru JSON
> * Určení vlastností simulovaného zařízení
> * Určete telemetrii, kterou simulované zařízení odesílá
> * Určete metody typu cloud-to-device, na které zařízení reaguje

## <a name="the-parts-of-the-device-model-schema"></a>Části schématu modelu zařízení

Každý model zařízení, například chladič nebo nákladní automobil, definuje typ zařízení, které může simulační služba simulovat. Každý model zařízení je uložen v souboru JSON s následujícím schématem nejvyšší úrovně:

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

Můžete zobrazit soubory schématu pro výchozí simulovaná zařízení ve [složce devicemodels](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels) na GitHubu.

Následující tabulka popisuje položky schématu nejvyšší úrovně:

| Položka schématu | Popis |
| -- | --- |
| `SchemaVersion` | Verze schématu je vždy `1.0.0` a je specifická pro formát tohoto souboru. |
| `Id` | Jedinečné ID pro tento model zařízení. |
| `Version` | Identifikuje verzi modelu zařízení. |
| `Name` | Popisný název modelu zařízení. |
| `Description` | Popis modelu zařízení. |
| `Protocol` | Protokol připojení, který zařízení používá. Může být `AMQP`jedním `MQTT`z `HTTP`, a . |

Následující části popisují další části schématu JSON:

## <a name="simulation"></a>Simulace

V `Simulation` části definujete vnitřní stav simulovaného zařízení. Všechny hodnoty telemetrie odeslané zařízením musí být součástí tohoto stavu zařízení.

Definice stavu zařízení má dva prvky:

* `InitialState`definuje počáteční hodnoty pro všechny vlastnosti objektu stavu zařízení.
* `Script`identifikuje soubor JavaScriptu, který běží podle plánu pro aktualizaci stavu zařízení. Tento soubor skriptu můžete použít k randomizaci hodnot telemetrie odeslaných zařízením.

Další informace o souboru JavaScript, který aktualizuje objekt stavu zařízení, [najdete v tématu Principy chování modelu zařízení](../../articles/iot-accelerators/iot-accelerators-device-simulation-advanced-device.md).

Následující příklad ukazuje definici objektu stavu zařízení pro simulované zařízení chladiče:

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

Simulační služba spouští každých pět sekund soubor **chiller-01-state.js,** aby aktualizovala stav zařízení. Soubory JavaScriptu pro výchozí simulovaná zařízení můžete vidět ve [složce skriptů](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels/scripts) na GitHubu. Podle konvence mají tyto soubory JavaScriptu příponu **-state,** která je odlišuje od souborů, které implementují chování metody.

## <a name="properties"></a>Vlastnosti

Část `Properties` schématu definuje hodnoty vlastností, které zařízení hlásí řešení. Příklad:

```json
"Properties": {
  "Type": "Elevator",
  "Location": "Building 2",
  "Latitude": 47.640792,
  "Longitude": -122.126258
}
```

Když se řešení spustí, dotazuje se všech simulovaných zařízení k sestavení seznamu `Type` hodnot, které se mají použít v unovém ui. Řešení používá `Latitude` vlastnosti a `Longitude` k přidání umístění zařízení do mapy na řídicím panelu.

## <a name="telemetry"></a>Telemetrie

Pole `Telemetry` obsahuje seznam všech typů telemetrie, které simulované zařízení odesílá do řešení.

Následující příklad odešle telemetrickou zprávu JSON `floor` `vibration`každých `temperature` 10 sekund s , a data ze snímačů výtahu:

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

`MessageTemplate`definuje strukturu zprávy JSON odeslané simulovaným zařízením. Zástupné symboly `MessageTemplate` používají `${NAME}` `NAME` syntaxi, kde je klíč z [objektu stavu zařízení](#simulation). Řetězce by měly být citovány, čísla by neměla.

`MessageSchema`definuje schéma zprávy odeslané simulovaným zařízením. Schéma zprávy je také publikováno do služby IoT Hub, aby mohly back-endové aplikace znovu použít informace k interpretaci příchozí telemetrie.

V současné době můžete použít pouze schémata zpráv JSON. Pole uvedená ve schématu mohou být následujících typů:

* Objekt - serializován pomocí JSON
* Binární - serializovánpomocí base64
* Text
* Logická hodnota
* Integer
* Double
* DateTime

Chcete-li odesílat telemetrické zprávy v různých intervalech, přidejte do `Telemetry` pole více typů telemetrie. Následující příklad odesílá údaje o teplotě a vlhkosti každých 10 sekund a stav světla každou minutu:

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

## <a name="cloudtodevicemethods"></a>CloudToDeviceMetody

Simulované zařízení může reagovat na metody cloud-to-device volané z centra IoT. Oddíl `CloudToDeviceMethods` v souboru schématu modelu zařízení:

* Definuje metody, na které může simulované zařízení reagovat.
* Identifikuje soubor JavaScript, který obsahuje logiku, kterou chcete spustit.

Simulované zařízení odešle seznam metod, které podporuje, do centra IoT, ke které je připojeno.

Další informace o souboru JavaScript, který implementuje chování zařízení, [najdete v tématu Principy chování modelu zařízení](../../articles/iot-accelerators/iot-accelerators-device-simulation-advanced-device.md).

Následující příklad určuje tři podporované metody a soubory JavaScriptu, které tyto metody implementují:

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

Soubory JavaScriptu pro výchozí simulovaná zařízení můžete vidět ve [složce skriptů](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels/scripts) na GitHubu. Podle konvence mají tyto soubory JavaScriptu příponu **-method,** která je odlišuje od souborů, které implementují chování stavu.

## <a name="next-steps"></a>Další kroky

Tento článek popisuje, jak vytvořit vlastní simulované zařízení modelu. Tento článek vám ukázal, jak:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Definování simulovaného modelu zařízení pomocí souboru JSON
> * Určení vlastností simulovaného zařízení
> * Určete telemetrii, kterou simulované zařízení odesílá
> * Určete metody typu cloud-to-device, na které zařízení reaguje

Nyní, když jste se dozvěděli o schématu JSON, je navrhovaným dalším krokem naučit se [implementovat chování simulovaného zařízení](iot-accelerators-remote-monitoring-device-behavior.md).

Další informace pro vývojáře o řešení vzdáleného monitorování naleznete v následujících tématech:

* [Referenční příručka pro vývojáře](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Průvodce odstraňováním potíží s vývojáři](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)
