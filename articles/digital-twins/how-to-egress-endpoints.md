---
title: Výchozí přenos dat a koncové body v digitální dvojče Azure | Dokumentace Microsoftu
description: Pokyny k vytvoření koncových bodů s Dvojčaty digitální Azure
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: alinast
ms.openlocfilehash: e140ca46a18fcab2194adb213d723ab67d40b0a8
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2018
ms.locfileid: "51615156"
---
# <a name="egress-and-endpoints"></a>Koncové body a výchozí přenos dat

Azure digitální dvojče podporuje koncept **koncové body**. Každý koncový bod reprezentuje zprostředkovatele zpráv nebo událostí v rámci předplatného Azure uživatele. Události a zprávy může být zaslána tématech týkajících se služby Azure Event Hubs, Azure Event Grid a Azure Service Bus.

Události se posílají do koncových bodů podle předdefinovaného směrování požadavků. Uživatel může určit, který koncový bod by měl přijímat některý z následujících událostí: 

- TopologyOperation
- UdfCustom
- SensorChange
- SpaceChange
- DeviceMessage

Základní znalosti o směrování události a typy událostí, najdete v tématu [směrování události a zprávy](concepts-events-routing.md).

## <a name="event-types-description"></a>Popis typů událostí

Formát událostí pro všechny typy událostí jsou popsány v následujících částech.

### <a name="topologyoperation"></a>TopologyOperation

**TopologyOperation** se vztahuje na změn grafu. **Subjektu** vlastnost určuje typ objektu vliv. Tato událost může aktivovat následující typy objektů: 

- Zařízení
- DeviceBlobMetadata
- DeviceExtendedProperty
- ExtendedPropertyKey
- Hodnotu ExtendedType
- Úložiště klíčů
- Sestava
- Rutiny RoleDefinition
- Senzor
- SensorBlobMetadata
- SensorExtendedProperty
- Kosmické aktivity
- SpaceBlobMetadata
- SpaceExtendedProperty
- SpaceResource
- SpaceRoleAssignment
- Systémový
- Uživatel
- UserBlobMetadata
- UserExtendedProperty

#### <a name="example"></a>Příklad:

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

| Název vlastního atributu | Nahradit hodnotou |
| --- | --- |
| yourTopicName | Název vlastní téma |

### <a name="udfcustom"></a>UdfCustom

**UdfCustom** události odeslané uživatelem definované funkce (UDF). 
  
> [!IMPORTANT]  
> Tato událost musí být explicitně odeslána z UDF, samotného.

#### <a name="example"></a>Příklad:

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

| Název vlastního atributu | Nahradit hodnotou |
| --- | --- |
| yourTopicName | Název vlastní téma |

### <a name="sensorchange"></a>SensorChange

**SensorChange** je aktualizace na základě telemetrických dat změn stavu senzoru.

#### <a name="example"></a>Příklad:

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

| Název vlastního atributu | Nahradit hodnotou |
| --- | --- |
| yourTopicName | Název vlastní téma |

### <a name="spacechange"></a>SpaceChange

**SpaceChange** je aktualizace na základě telemetrických dat změn stavu místo.

#### <a name="example"></a>Příklad:

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

| Název vlastního atributu | Nahradit hodnotou |
| --- | --- |
| yourTopicName | Název vlastní téma |

### <a name="devicemessage"></a>DeviceMessage

S použitím **DeviceMessage**, můžete zadat **EventHub** připojení, ke kterému nezpracovaná telemetrická data událostí je možné směrovat i z Azure digitální dvojče.

> [!NOTE]
> - **DeviceMessage** je kombinovatelných pouze s **EventHub**. Nelze kombinovat **DeviceMessage** s některým z jiných typů událostí.
> - Můžete zadat pouze jeden koncový bod z kombinace typu **EventHub** nebo **DeviceMessage**.

## <a name="configure-endpoints"></a>Konfigurace koncových bodů

Koncový bod správy se provede prostřednictvím koncových bodů rozhraní API. Následující příklady ukazují, jak nakonfigurovat různé podporovaných koncových bodů. Věnujte zvláštní pozornost pole typů událostí, protože definují směrování pro koncový bod:

```plaintext
POST https://endpoints-demo.azuresmartspaces.net/management/api/v1.0/endpoints
```

- Trasy, která má typy událostí služby Service Bus **SensorChange**, **SpaceChange**, a **TopologyOperation**:

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

    | Název vlastního atributu | Nahradit hodnotou |
    | --- | --- |
    | yourNamespace | Obor názvů vašeho koncového bodu |
    | yourPrimaryKey | Primární připojovací řetězec použitý k ověření |
    | yourSecondaryKey | Sekundární připojovací řetězec použitý k ověření |
    | yourTopicName | Název vlastní téma |

- Trasy, která má typy událostí služby Event Grid **SensorChange**, **SpaceChange**, a **TopologyOperation**:

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

    | Název vlastního atributu | Nahradit hodnotou |
    | --- | --- |
    | yourPrimaryKey | Primární připojovací řetězec použitý k ověření|
    | yourSecondaryKey | Sekundární připojovací řetězec použitý k ověření |
    | yourTopicName | Název vlastní téma |

- Trasy, která má typy událostí služby Event Hubs **SensorChange**, **SpaceChange**, a **TopologyOperation**:

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

    | Název vlastního atributu | Nahradit hodnotou |
    | --- | --- |
    | yourNamespace | Obor názvů vašeho koncového bodu |
    | yourPrimaryKey | Primární připojovací řetězec použitý k ověření |
    | yourSecondaryKey | Sekundární připojovací řetězec použitý k ověření |
    | yourEventHubName | Název vašeho centra událostí |

- Trasy, která má typ událostí služby Event Hubs **DeviceMessage**. Zahrnutí `EntityPath` v **connectionString** je povinné:

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

    | Název vlastního atributu | Nahradit hodnotou |
    | --- | --- |
    | yourNamespace | Obor názvů vašeho koncového bodu |
    | yourPrimaryKey | Primární připojovací řetězec použitý k ověření |
    | yourSecondaryKey | Sekundární připojovací řetězec použitý k ověření |
    | yourEventHubName | Název vašeho centra událostí |

> [!NOTE]  
> Při vytvoření nového koncového bodu může trvat 5 až 10 minut, než se začít přijímat události v koncovém bodě.

## <a name="primary-and-secondary-connection-keys"></a>Připojení primární a sekundární klíče

Jakmile je klíč primární připojení neoprávněným, systém automaticky pokusí připojení sekundární klíč. Který obsahuje zálohy a umožňuje možnost řádné ověření a aktualizovat primární klíč prostřednictvím koncových bodů rozhraní API.

Pokud připojení primární a sekundární klíče chybí oprávnění, systém přejde exponenciální regresní čekat až 30 minut. Události jsou vynechány na pokaždé, když aktivovaných regresní čekání.

Pokaždé, když je systém v back-off počkejte stavu, aktualizaci klíčů připojení prostřednictvím koncových bodů rozhraní API může trvat až 30 minut, než se projeví.

## <a name="unreachable-endpoints"></a>Nedosažitelný koncových bodů

Až koncový bod bude nedostupná, systém přejde exponenciální regresní čekat až 30 minut. Události jsou vynechány na pokaždé, když aktivovaných regresní čekání.

## <a name="next-steps"></a>Další postup

- Přečtěte si [jak používat Azure digitální dvojče Swagger](how-to-use-swagger.md).

- Další informace o [směrování události a zprávy](concepts-events-routing.md) v digitální dvojče Azure.
