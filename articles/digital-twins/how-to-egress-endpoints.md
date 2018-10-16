---
title: Výchozí přenos dat a koncové body v digitální dvojče Azure | Dokumentace Microsoftu
description: Pokyny o tom, jak vytvořit koncové body s Dvojčaty digitální Azure
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: alinast
ms.openlocfilehash: c917fab84448684cf29af162ec0781d764605f71
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2018
ms.locfileid: "49324068"
---
# <a name="egress-and-endpoints"></a>Koncové body a výchozí přenos dat

Azure digitální dvojče podporuje koncept _koncové body_ kde představuje každý koncový bod zprostředkovatele zpráv nebo událostí v rámci předplatného Azure uživatele. Je možné odeslat události a zprávy **centra událostí**, **služby Event Grid**, a **témata služby Service Bus**.

Události se posílají do koncových bodů podle předem definovaných směrování požadavků: uživatel může určit, který koncový bod by měl přijímat některý z následujících událostí:`TopologyOperation`, `UdfCustom`, `SensorChange`, `SpaceChange`, nebo `DeviceMessage`.

Základní znalosti o směrování události a typy událostí, najdete v tématu [směrování události a zprávy](concepts-events-routing.md).

## <a name="event-types-description"></a>Popis typů událostí

Tady jsou formáty události pro každý typ události:

- `TopologyOperation`

  Platí pro změn grafu. `subject` Vlastnost určuje typ objektu vliv. Typy objektů, které tato událost může aktivovat: `Device, DeviceBlobMetadata`, `DeviceExtendedProperty`, `ExtendedPropertyKey`, `ExtendedType`, `KeyStore`, `Report`, `RoleDefinition`, `Sensor`, `SensorBlobMetadata`, `SensorExtendedProperty`, `Space` ,  `SpaceBlobMetadata`, `SpaceExtendedProperty`, `SpaceResource`, `SpaceRoleAssignment`, `System`, `User`, `UserBlobMetadata`, `UserExtendedProperty`.

  Příklad:

  ```JSON
  {
    "id": "00000000-0000-0000-0000-000000000000",
    "subject": "ExtendedPropertyKey",
    "data": {
      "SpacesToNotify": [
        "3a16d146-ca39-49ee-b803-17a18a12ba36"
      ],
      "Id": "00000000-0000-0000-0000-000000000000",
      "Type": "ExtendedPropertyKey",
      "AccessType": "Create"
    },
    "eventType": "TopologyOperation",
    "eventTime": "2018-04-17T17:41:54.9400177Z",
    "dataVersion": "1",
    "metadataVersion": "1",
    "topic": "/subscriptions/yourTopicName"
  }
  ```

    | Název vlastního atributu | Nahraďte |
    | --- | --- |
    | `yourTopicName` | Název vlastní téma |

- `UdfCustom`

  Události odeslané uživatelem definované funkce (UDF). Všimněte si, že se tato událost je explicitně posílaly z UDF, samotného.

  Příklad:

  ```JSON
  {
    "id": "568fd394-380b-46fa-925a-ebb96f658cce",
    "subject": "UdfCustom",
    "data": {
      "TopologyObjectId": "7c799bfc-1bff-4b9e-b15a-669933969d20",
      "ResourceType": "Space",
      "Payload": "\"Room is not available or air quality is poor\"",
      "CorrelationId": "568fd394-380b-46fa-925a-ebb96f658cce"
    },
    "eventType": "UdfCustom",
    "eventTime": "2018-10-02T06:50:15.198Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/yourTopicName"
  }
  ```

    | Název vlastního atributu | Nahraďte |
    | --- | --- |
    | `yourTopicName` | Název vlastní téma |

- `SensorChange`

  Aktualizace na základě telemetrických dat změn stavu senzoru.

  Příklad:

  ```JSON
  {
    "id": "60bf5336-2929-45b4-bb4c-b45699dfe95f",
    "subject": "SensorChange",
    "data": {
      "Type": "Classic",
      "DataType": "Motion",
      "Id": "60bf5336-2929-45b4-bb4c-b45699dfe95f",
      "Value": "False",
      "PreviousValue": "True",
      "EventTimestamp": "2018-04-17T17:46:15.4964262Z",
      "MessageType": "sensor",
      "Properties": {
        "ms-client-request-id": "c9e576b7-5eea-4f61-8617-92a57add5179",
        "ms-activity-id": "ct22YwXEGJ5u.605.0"
      }
    },
    "eventType": "SensorChange",
    "eventTime": "2018-04-17T17:46:18.5452993Z",
    "dataVersion": "1",
    "metadataVersion": "1",
    "topic": "/subscriptions/yourTopicName"
  }
  ```

    | Název vlastního atributu | Nahraďte |
    | --- | --- |
    | `yourTopicName` | Název vlastní téma |

- `SpaceChange`

  Aktualizace na základě telemetrických dat změn stavu místo.

  Příklad:

  ```JSON
  {
    "id": "42522e10-b1aa-42ff-a5e7-7181788ffc4b",
    "subject": "SpaceChange",
    "data": {
      "Type": null,
      "DataType": "AvailableAndFresh",
      "Id": "7c799bfc-1bff-4b9e-b15a-669933969d20",
      "Value": "Room is not available or air quality is poor",
      "PreviousValue": null,
      "RawData": null,
      "transactionId": null,
      "EventTimestamp": null,
      "MessageType": null,
      "Properties": null,
      "CorrelationId": "42522e10-b1aa-42ff-a5e7-7181788ffc4b"
    },
    "eventType": "SpaceChange",
    "eventTime": "2018-10-02T06:50:20.128Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/yourTopicName"
  }
  ```

    | Název vlastního atributu | Nahraďte |
    | --- | --- |
    | `yourTopicName` | Název vlastní téma |

- `DeviceMessage`

  Můžete zadat `EventHub` připojení, ke kterému nezpracovaná telemetrická data událostí je možné směrovat i z Azure digitální dvojče.

> [!NOTE]
> - `DeviceMessage` je kombinovatelných pouze s `EventHub`; není možné kombinovat `DeviceMessage` s některým z jiných typů událostí.
> - Bude možné zadat pouze jeden koncový bod z kombinace typu `EventHub` / `DeviceMessage`.

## <a name="configuring-endpoints"></a>Konfigurace koncových bodů

Koncový bod správy se provede prostřednictvím koncových bodů rozhraní API. Tady jsou některé příklady o tom, jak nakonfigurovat různé podporovaných koncových bodů. Věnujte zvláštní pozornost pole typů událostí, protože definují směrování pro koncový bod:

```plaintext
POST https://endpoints-demo.azuresmartspaces.net/management/api/v1.0/endpoints
```

- Směrovat **služby Service Bus** typy událostí: `SensorChange`, `SpaceChange`, `TopologyOperation`

  ```JSON
  {
    "type": "ServiceBus",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourPrimaryKey",
    "secondaryConnectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourSecondaryKey",
    "path": "yourTopicName"
  }
  ```

    | Název vlastního atributu | Nahraďte |
    | --- | --- |
    | `yourNamespace` | Obor názvů vašeho koncového bodu |
    | `yourPrimaryKey` | Primární připojovací řetězec použitý k ověření |
    | `yourSecondaryKey` | Sekundární připojovací řetězec použitý k ověření |
    | `yourTopicName` | Název vlastní téma |

- Směrovat **služby Event Grid** typy událostí: `SensorChange`, `SpaceChange`, `TopologyOperation`

  ```JSON
  {
    "type": "EventGrid",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "yourPrimaryKey",
    "secondaryConnectionString": "yourSecondaryKey",
    "path": "yourTopicName.westus-1.eventgrid.azure.net"
  }
  ```

    | Název vlastního atributu | Nahraďte |
    | --- | --- |
    | `yourPrimaryKey` | Primární připojovací řetězec použitý k ověření|
    | `yourSecondaryKey` | Sekundární připojovací řetězec použitý k ověření |
    | `yourTopicName` | Název vlastní téma |

- Směrovat **centra událostí** typy událostí: `SensorChange`, `SpaceChange`, `TopologyOperation`

  ```JSON
  {
    "type": "EventHub",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourPrimaryKey",
    "secondaryConnectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourSecondaryKey",
    "path": "yourEventHubName"
  }
  ```

    | Název vlastního atributu | Nahraďte |
    | --- | --- |
    | `yourNamespace` | Obor názvů vašeho koncového bodu |
    | `yourPrimaryKey` | Primární připojovací řetězec použitý k ověření |
    | `yourSecondaryKey` | Sekundární připojovací řetězec použitý k ověření |
    | `yourEventHubName` | Název vašeho centra událostí |

- Směrovat **centra událostí** typy událostí `DeviceMessage`. Poznámka: zahrnutí _EntityPath_ v `connectionString`, což je povinné.

  ```JSON
  {
    "type": "EventHub",
    "eventTypes": [
      "DeviceMessage"
    ],
    "connectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourPrimaryKey;EntityPath=yourEventHubName",
    "secondaryConnectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourSecondaryKey;EntityPath=yourEventHubName",
    "path": "yourEventHubName"
  }
  ```

    | Název vlastního atributu | Nahraďte |
    | --- | --- |
    | `yourNamespace` | Obor názvů vašeho koncového bodu |
    | `yourPrimaryKey` | Primární připojovací řetězec použitý k ověření |
    | `yourSecondaryKey` | Sekundární připojovací řetězec použitý k ověření |
    | `yourEventHubName` | Název vašeho centra událostí |

> [!NOTE]
> Při vytvoření nového koncového bodu může trvat 5 až 10 minut, než se začít přijímat události v koncovém bodě.

## <a name="primary-and-secondary-connection-keys"></a>Připojení primární a sekundární klíče

Jakmile je klíč primární připojení neoprávněným, systém automaticky pokusí připojení sekundární klíč. Který obsahuje zálohy a umožňuje možnost řádné ověření a aktualizovat primární klíč prostřednictvím koncových bodů rozhraní API.

Pokud připojení primární a sekundární klíče neoprávněným, systém přejde exponenciální regresní čekat až 30 minut. Události se zahodí na pokaždé, když aktivovaných regresní čekání.

Pokaždé, když je systém v back-off počkejte stavu, aktualizaci klíčů připojení prostřednictvím koncových bodů rozhraní API může trvat až 30 minut, než se projeví.

## <a name="unreachable-endpoints"></a>Nedosažitelný koncových bodů

Až koncový bod bude nedostupná, systém přejde exponenciální regresní čekat až 30 minut. Události se zahodí na pokaždé, když aktivovaných regresní čekání.

## <a name="next-steps"></a>Další postup

Další informace o použití Azure digitální dvojče Swagger, přečtěte si téma [Swagger Azure digitální dvojče](how-to-use-swagger.md).

Další informace o směrování události a zprávy v digitální dvojče Azure najdete v článku [směrování události a zprávy](concepts-events-routing.md).
