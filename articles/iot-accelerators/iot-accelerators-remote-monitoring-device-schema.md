---
title: Schéma zařízení v řešení vzdáleného monitorování – Azure | Microsoft Docs
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "81683749"
---
# <a name="understand-the-device-model-schema"></a>Pochopení schématu modelu zařízení

K otestování jeho chování můžete použít simulovaná zařízení v řešení vzdáleného monitorování. Řešení vzdáleného monitorování zahrnuje službu pro simulaci zařízení ke spouštění simulovaných zařízení. Když nasadíte řešení vzdáleného monitorování, kolekce simulovaných zařízení se zřídí automaticky. Můžete přizpůsobit existující simulovaná zařízení nebo vytvořit vlastní.

Tento článek popisuje schéma modelu zařízení, které určuje možnosti a chování simulovaného zařízení. Model zařízení je uložený v souboru JSON.

> [!NOTE]
> Toto schéma modelu zařízení je pouze pro simulovaná zařízení hostovaná ve službě simulace zařízení. Pokud chcete vytvořit reálné zařízení, přečtěte si téma [připojení zařízení k akcelerátoru řešení vzdáleného monitorování](iot-accelerators-connecting-devices.md).

Následující články souvisejí s aktuálním článkem:

* [Implementací chování modelu zařízení](iot-accelerators-remote-monitoring-device-behavior.md) popisuje soubory JavaScriptu, které slouží k implementaci chování simulovaného zařízení.
* [Vytvořením nového simulovaného zařízení](iot-accelerators-remote-monitoring-create-simulated-device.md) spojíte dohromady a dozvíte se, jak do vašeho řešení nasadit nový simulovaný typ zařízení.

V tomto článku získáte informace o těchto tématech:

>[!div class="checklist"]
> * Použití souboru JSON k definování simulovaného modelu zařízení
> * Zadat vlastnosti simulovaného zařízení
> * Určení telemetrie, kterou simulované zařízení odesílá
> * Zadejte metody Cloud-zařízení, na které zařízení reaguje

## <a name="the-parts-of-the-device-model-schema"></a>Části schématu modelu zařízení

Každý model zařízení, jako je například chladicí zařízení nebo automobil, definuje typ zařízení, které může simulace simulovat. Každý model zařízení je uložený v souboru JSON s následujícím schématem nejvyšší úrovně:

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

Soubory schématu pro výchozí simulovaná zařízení můžete zobrazit ve [složce DeviceModels](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels) na GitHubu.

Následující tabulka popisuje položky schématu nejvyšší úrovně:

| Položka schématu | Description |
| -- | --- |
| `SchemaVersion` | Verze schématu je vždy `1.0.0` a je specifická pro formát tohoto souboru. |
| `Id` | Jedinečné ID pro tento model zařízení. |
| `Version` | Určuje verzi modelu zařízení. |
| `Name` | Popisný název modelu zařízení. |
| `Description` | Popis modelu zařízení. |
| `Protocol` | Protokol připojení, který zařízení používá Může to být jedna z `AMQP` , a `MQTT` `HTTP` . |

Následující části popisují ostatní oddíly ve schématu JSON:

## <a name="simulation"></a>Simulace

V `Simulation` části definujete vnitřní stav simulovaného zařízení. Všechny hodnoty telemetrie odesílané zařízením musí být součástí stavu tohoto zařízení.

Definice stavu zařízení má dva prvky:

* `InitialState` Definuje počáteční hodnoty pro všechny vlastnosti objektu stavu zařízení.
* `Script` Identifikuje soubor JavaScriptu, který se spouští podle plánu, aby se aktualizoval stav zařízení. Tento soubor skriptu můžete použít k náhodnému vynechání hodnot telemetrie odesílaných zařízením.

Další informace o souboru JavaScriptu, který aktualizuje objekt stavu zařízení, najdete v tématu [pochopení chování modelu zařízení](../../articles/iot-accelerators/iot-accelerators-device-simulation-advanced-device.md).

Následující příklad ukazuje definici objektu stavu zařízení pro simulované chladicí zařízení:

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

Služba simulace spouští soubor **chiller-01-state.js** každých pět sekund, aby bylo možné aktualizovat stav zařízení. Soubory JavaScriptu pro výchozí simulovaná zařízení můžete zobrazit ve [složce Scripts](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels/scripts) na GitHubu. Podle konvence mají tyto soubory JavaScriptu **stav** přípony, aby je bylo možné odlišit od souborů, které implementují chování metody.

## <a name="properties"></a>Vlastnosti

`Properties`Oddíl schématu definuje hodnoty vlastností, které zařízení hlásí do řešení. Například:

```json
"Properties": {
  "Type": "Elevator",
  "Location": "Building 2",
  "Latitude": 47.640792,
  "Longitude": -122.126258
}
```

Po spuštění řešení se dotazuje na všechna simulovaná zařízení a vytvoří seznam `Type` hodnot, které se použijí v uživatelském rozhraní. Řešení používá `Latitude` `Longitude` vlastnosti a k přidání umístění zařízení do mapy na řídicím panelu.

## <a name="telemetry"></a>Telemetrie

`Telemetry`Pole uvádí všechny typy telemetrie, které simulované zařízení odesílá do řešení.

Následující příklad pošle zprávu telemetrie JSON každých 10 sekund s `floor` , `vibration` a `temperature` daty ze senzorů výtahu:

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

`MessageTemplate` definuje strukturu zprávy JSON odesílané simulovaným zařízením. Zástupné symboly v nástroji `MessageTemplate` používají syntaxi `${NAME}` , kde `NAME` je klíč z [objektu stavu zařízení](#simulation). Řetězce by měly být v uvozovkách, čísla by neměla.

`MessageSchema` definuje schéma zprávy odesílané simulovaným zařízením. Schéma zprávy je také Publikováno do IoT Hub, aby back-end aplikace mohly znovu použít informace k interpretaci příchozí telemetrie.

V současné době můžete použít pouze schémata zpráv JSON. Pole uvedená ve schématu mohou mít následující typy:

* Objekt – serializovaný pomocí JSON
* Binární – serializovaný pomocí Base64
* Text
* Logická hodnota
* Integer
* dvojité
* DateTime

Chcete-li odesílat zprávy telemetrie v různých intervalech, přidejte k poli více typů telemetrie `Telemetry` . Následující příklad odesílá data o teplotě a vlhkosti každých 10 sekund a stav světla každou minutu:

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

Simulované zařízení může reagovat na metody typu cloud-zařízení volané ze služby IoT Hub. `CloudToDeviceMethods`Oddíl v souboru schématu modelu zařízení:

* Definuje metody, na které simulované zařízení může reagovat.
* Identifikuje soubor JavaScriptu, který obsahuje logiku, která se má provést.

Simulované zařízení odesílá seznam metod, které podporuje, do centra IoT, ke kterému je připojený.

Další informace o souboru JavaScriptu, který implementuje chování zařízení, najdete v tématu [pochopení chování modelu zařízení](../../articles/iot-accelerators/iot-accelerators-device-simulation-advanced-device.md).

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

Soubory JavaScriptu pro výchozí simulovaná zařízení můžete zobrazit ve [složce Scripts](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels/scripts) na GitHubu. Podle konvence mají tyto soubory JavaScriptu příponu **– způsob** , jak je odlišit od souborů, které implementují chování stavu.

## <a name="next-steps"></a>Další kroky

Tento článek popisuje, jak vytvořit vlastní model simulovaného zařízení. Tento článek vám ukázal, jak:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Použití souboru JSON k definování simulovaného modelu zařízení
> * Zadat vlastnosti simulovaného zařízení
> * Určení telemetrie, kterou simulované zařízení odesílá
> * Zadejte metody Cloud-zařízení, na které zařízení reaguje

Teď, když jste se naučili o schématu JSON, je navržený další krok, kde se dozvíte, jak [implementovat chování simulovaného zařízení](iot-accelerators-remote-monitoring-device-behavior.md).

Další informace o vývojářích řešení vzdáleného monitorování najdete v těchto tématech:

* [Referenční příručka pro vývojáře](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Příručka pro řešení potíží pro vývojáře](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)
