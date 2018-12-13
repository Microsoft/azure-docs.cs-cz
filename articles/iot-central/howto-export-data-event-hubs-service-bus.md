---
title: Exportovat data do služby Azure Event Hubs a Azure Service Bus | Dokumentace Microsoftu
description: Jak exportovat data z Azure IoT Central aplikace do Azure Event Hubs a Azure Service Bus
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 12/07/2018
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: 14b51f109ca76661ac10c99d42002dda45bc0500
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53318678"
---
# <a name="export-your-data-in-azure-iot-central"></a>Exportujte data v Azure IoT Central

*Toto téma se vztahuje na správce.*

Tento článek podrobně použití souvislá datová funkce exportu v Azure IoT Central exportování dat na své vlastní věnuje hlubší **Azure Event Hubs**, a **Azure Service Bus** instancí. Můžete to taky **měření**, **zařízení**, a **šablon** do vlastní cíle pro horké cesty přehledů a analýz. Jedná se o aktivaci vlastního pravidla ve službě Azure Stream Analytics, aktivaci vlastních pracovních postupů v Azure Logic Apps nebo transformace dat a předají se jí prostřednictvím Azure Functions. 

> [!Note]
> Znovu až zapnete nepřetržitý export dat, zobrazí pouze data dále v tomto okamžiku. V současné době nelze načíst data po dobu, kdy byla nepřetržitý export dat vypnout. Pokud chcete zachovat dalších historických dat, zapněte nepřetržitý export dat již v rané fázi.


## <a name="prerequisites"></a>Požadavky

- Musíte být správcem ve vaší aplikaci IoT Central

## <a name="export-to-azure-event-hubs-and-azure-service-bus"></a>Export do služby Azure Event Hubs a Azure Service Bus

Měření, zařízení a zařízení šablony data se exportují do centra událostí nebo fronty Service Bus nebo téma v téměř reálném čase. Exportované měření dat obsahuje celé zprávy zařízení odesílat IoT Central, ne jenom hodnoty měření sami. Exportovaná zařízení data obsahují změny vlastnosti a nastavení všech zařízení a zařízení exportované šablony obsahuje změny pro všechny šablony zařízení. Exportovaná data se ve vlastnosti "text" a je ve formátu JSON.

> [!NOTE]
> Když vyberete jako cíl pro export, fronty a témata služby Service Bus **nesmí mít relace nebo vyhledávání duplicit povolené**. Pokud některý z těchto možností jsou povolené, nebudou přicházet některé zprávy do fronty nebo tématu.

### <a name="measurements"></a>Měření

Nová zpráva se exportují rychle po IoT Central přijímá zprávy ze zařízení. Každá zpráva exportované do služby Event Hubs a služby Service Bus obsahuje celé zprávy zařízení odesílají v vlastnost "text" ve formátu JSON. 

> [!NOTE]
> Zařízení, které odesílají měření jsou reprezentovány v ID zařízení (viz následující části). Chcete-li získat názvy zařízení, exportovat data zařízení a korelovat každý býváte pomocí **connectionDeviceId** , který odpovídá **deviceId** zprávy typu zařízení.

Následující příklad ukazuje přijata zpráva o měření dat v Centru událostí nebo fronty Service Bus nebo téma.

```json
{
  "body": {
    "humidity": 29.06963648666288,
    "temp": 8.4503795661685,
    "pressure": 1075.8334910110093,
    "magnetometerX": 408.6966458887116,
    "magnetometerY": -532.8809796603962,
    "magnetometerZ": 174.70653875528205,
    "accelerometerX": 1481.546749013788,
    "accelerometerY": -1139.4316656437406,
    "accelerometerZ": 811.6928695575307,
    "gyroscopeX": 442.19879163299856,
    "gyroscopeY": 123.23710975717177,
    "gyroscopeZ": 708.5397575786151,
    "deviceState": "DANGER"
  },
  "annotations": {
    "iothub-connection-device-id": "<connectionDeviceId>",
    "iothub-connection-auth-method": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "iothub-connection-auth-generation-id": "<generationId>",
    "iothub-enqueuedtime": 1539381029965,
    "iothub-message-source": "Telemetry",
    "x-opt-sequence-number": 25325,
    "x-opt-offset": "<offset>",
    "x-opt-enqueued-time": 1539381030200
  },
  "sequenceNumber": 25325,
  "enqueuedTimeUtc": "2018-10-12T21:50:30.200Z",
  "offset": "<offset>",
  "properties": {
    "content_type": "application/json",
    "content_encoding": "utf-8"
  }
}
```

### <a name="devices"></a>Zařízení

Zprávy obsahující data zařízení odesílají do centra událostí nebo fronty Service Bus nebo téma každých několik minut. To znamená, že každých několik minut, dávku zpráv, které budou doručeny s daty o
- Nová zařízení, které byly přidány
- Zařízení s změněných vlastností a nastavení hodnot

Každá zpráva představuje jeden nebo více změn do zařízení od poslední exportované zprávy. Obsahuje informace, které se odesílají v každé zprávě:
- `id` zařízení v IoT Central
- `name` zařízení
- `deviceId` z [Device Provisioning Service](https://aka.ms/iotcentraldocsdps)
- Informace o šabloně zařízení
- Hodnoty vlastností
- Nastavení hodnot

> [!NOTE]
> Zařízení odstranit, protože poslední dávky neexportují. V exportovaném zprávy pro odstraněné zařízení v současné době nejsou žádné ukazatele.
>
> Šablonu zařízení, každé zařízení patří, je reprezentována ID zařízení šablony. Pokud chcete získat název šablony zařízení, je nutné exportovat data šablony zařízení moc.

Následující příklad ukazuje zpráva týkající se dat zařízení v Centru událostí nebo fronty Service Bus nebo téma:


```json
{
  "body": {
    "id": "<id>",
    "name": "<deviceName>",
    "simulated": true,
    "deviceId": "<deviceId>",
    "deviceTemplate": {
      "id": "<templateId>",
      "version": "1.0.0"
    },
    "properties": {
      "cloud": {
        "location": "Seattle"
      },
      "device": {
        "dieNumber": 5445.5862873026645
      }
    },
    "settings": {
      "device": {
        "fanSpeed": 0
      }
    }
  },
  "annotations": {
    "iotcentral-message-source": "devices",
    "x-opt-partition-key": "<partitionKey>",
    "x-opt-sequence-number": 39740,
    "x-opt-offset": "<offset>",
    "x-opt-enqueued-time": 1539274959654
  },
  "partitionKey": "<partitionKey>",
  "sequenceNumber": 39740,
  "enqueuedTimeUtc": "2018-10-11T16:22:39.654Z",
  "offset": "<offset>",
}
```

### <a name="device-templates"></a>Šablony zařízení

Zprávy obsahující data šablony zařízení odesílají do centra událostí nebo fronty Service Bus nebo téma každých několik minut. To znamená, že každých několik minut, dávku zpráv, které budou doručeny s daty o
- Nové šablony zařízení, které byly přidány
- Šablony pro zařízení s změněné měření, vlastnosti a nastavení definice

Každá zpráva představuje jeden nebo více změn do šablony zařízení od poslední exportované zprávy. Obsahuje informace, které se odesílají v každé zprávě:
- `id` šablony zařízení
- `name` šablony zařízení
- `version` šablony zařízení
- Měření datových typů a minimální/maximální hodnoty
- Vlastnost datové typy a výchozí hodnoty
- Nastavení datových typů a výchozí hodnoty

> [!NOTE]
> Šablony zařízení odstranit, protože poslední dávky neexportují. V exportované zprávy pro odstraněné zařízení šablony v současné době nejsou žádné ukazatele.

Následující příklad ukazuje zpráva týkající se data šablony zařízení v Centru událostí nebo fronty Service Bus nebo téma:

```json
{
  "body": {
    "id": "<id>",
    "version": "1.0.0",
    "name": "<templateName>",
    "measurements": {
      "telemetry": {
        "humidity": {
          "dataType": "double",
          "name": "humidity"
        },
        "pressure": {
          "dataType": "double",
          "name": "pressure"
        },
        "temp": {
          "dataType": "double",
          "name": "temperature"
        }
      }
    },
    "properties": {
      "cloud": {
        "location": {
          "dataType": "string",
          "name": "Location"
        }
      },
      "device": {
        "dieNumber": {
          "dataType": "double",
          "name": "Die Number"
        }
      }
    },
    "settings": {
      "device": {
        "fanSpeed": {
          "dataType": "double",
          "name": "Fan Speed",
          "initialValue": 0
        }
      }
    }
  },
  "annotations": {
    "iotcentral-message-source": "deviceTemplates",
    "x-opt-partition-key": "<partitionKey>",
    "x-opt-sequence-number": 25315,
    "x-opt-offset": "<offset>",
    "x-opt-enqueued-time": 1539274985085
  },
  "partitionKey": "<partitionKey>",
  "sequenceNumber": 25315,
  "enqueuedTimeUtc": "2018-10-11T16:23:05.085Z",
  "offset": "<offset>",
}
```

## <a name="next-steps"></a>Další postup

Teď, když víte, jak exportovat data do služby Azure Event Hubs a Azure Service Bus, pokračujte k dalšímu kroku:

> [!div class="nextstepaction"]
> [Jak aktivovat Azure Functions](howto-trigger-azure-functions.md)
