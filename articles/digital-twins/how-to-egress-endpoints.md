---
title: Výchozí přenos dat a koncové body v digitální dvojče Azure | Dokumentace Microsoftu
description: Pokyny k vytvoření koncových bodů s Dvojčaty digitální Azure.
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/31/2018
ms.author: alinast
ms.openlocfilehash: e93811a56f934a95dde45633c4fb64312b3696df
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2019
ms.locfileid: "53994810"
---
# <a name="egress-and-endpoints"></a>Koncové body a výchozí přenos dat

Azure digitální dvojče *koncové body* představují zprostředkovatele zpráv nebo událostí v rámci předplatného Azure uživatele. Události a zprávy může být zaslána tématech týkajících se služby Azure Event Hubs, Azure Event Grid a Azure Service Bus.

Události jsou směrovány do koncových bodů podle předdefinovaného směrování požadavků. Uživatelé určení *typy událostí* se může zobrazit každý koncový bod.

Další informace o událostech, směrování a typy událostí, najdete v tématu [směrování události a zprávy v Azure digitální dvojče](./concepts-events-routing.md).

## <a name="events"></a>Události

Události jsou odesílány IoT objektů (například zařízení a senzorů) pro zpracování službou Azure zprostředkovatele zprávu a události. Události jsou definovány následující [referenční dokumentace schématu událostí služby Azure Event Grid](../event-grid/event-schema.md).

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
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Atribut | Typ | Popis |
| --- | --- | --- |
| id | řetězec | Jedinečný identifikátor pro událost. |
| předmět | řetězec | Vydavatel definované cesta předmět události. |
| data | objekt | Data události specifické pro poskytovatele prostředků. |
| Typ události | řetězec | Jeden z typů registrované události pro tento zdroj událostí. |
| čas události | řetězec | Vygenerování události podle času UTC poskytovatele. |
| dataVersion | řetězec | Verze schématu datového objektu Vydavatel Určuje verzi schématu. |
| verze metadataVersion | řetězec | Verze schématu metadat události Event Grid definuje schéma vlastnosti nejvyšší úrovně. Event gridu poskytuje tuto hodnotu. |
| téma | řetězec | Úplné prostředků cesta ke zdroji události. Toto pole není zapisovatelná. Event gridu poskytuje tuto hodnotu. |

Další informace o schématu událostí služby Event Grid:

- Zkontrolujte [referenční dokumentace schématu událostí služby Azure Event Grid](../event-grid/event-schema.md).
- Přečtěte si [Azure EventGrid Node.js SDK EventGridEvent odkaz](https://docs.microsoft.com/javascript/api/azure-eventgrid/eventgridevent?view=azure-node-latest).

## <a name="event-types"></a>Typy událostí

Typy událostí klasifikovat povaze události a jsou nastaveny **eventType** pole. V následujícím seznamu jsou uvedeny typy událostí k dispozici:

- TopologyOperation
- UdfCustom
- SensorChange
- SpaceChange
- DeviceMessage

Formát událostí pro každý typ události jsou podrobně popsány v následujících podsekcí.

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
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Hodnota | Nahradit hodnotou |
| --- | --- |
| YOUR_TOPIC_NAME | Název vlastní téma |

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
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Hodnota | Nahradit hodnotou |
| --- | --- |
| YOUR_TOPIC_NAME | Název vlastní téma |

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
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Hodnota | Nahradit hodnotou |
| --- | --- |
| YOUR_TOPIC_NAME | Název vlastní téma |

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
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Hodnota | Nahradit hodnotou |
| --- | --- |
| YOUR_TOPIC_NAME | Název vlastní téma |

### <a name="devicemessage"></a>DeviceMessage

S použitím **DeviceMessage**, můžete zadat **EventHub** připojení, ke kterému nezpracovaná telemetrická data událostí je možné směrovat i z Azure digitální dvojče.

> [!NOTE]
> - **DeviceMessage** je kombinovatelných pouze s **EventHub**. Nelze kombinovat **DeviceMessage** s některým z jiných typů událostí.
> - Můžete zadat pouze jeden koncový bod z kombinace typu **EventHub** nebo **DeviceMessage**.

## <a name="configure-endpoints"></a>Konfigurace koncových bodů

Koncový bod správy se provede prostřednictvím koncových bodů rozhraní API.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Následující příklady ukazují, jak nakonfigurovat podporovaných koncových bodů.

>[!IMPORTANT]
> Věnujte pozornost pozor **eventTypes** atribut. Definuje která událost typy jsou zpracovány bodem a tím určit směrování.

Požadavek HTTP POST ověřený proti

```plaintext
YOUR_MANAGEMENT_API_URL/endpoints
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
    "connectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_PRIMARY_KEY",
    "secondaryConnectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SECONDARY_KEY",
    "path": "YOUR_TOPIC_NAME"
  }
  ```

    | Hodnota | Nahradit hodnotou |
    | --- | --- |
    | YOUR_NAMESPACE | Obor názvů vašeho koncového bodu |
    | YOUR_PRIMARY_KEY | Primární připojovací řetězec použitý k ověření |
    | YOUR_SECONDARY_KEY | Sekundární připojovací řetězec použitý k ověření |
    | YOUR_TOPIC_NAME | Název vlastní téma |

- Trasy, která má typy událostí služby Event Grid **SensorChange**, **SpaceChange**, a **TopologyOperation**:

  ```JSON
  {
    "type": "EventGrid",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "YOUR_PRIMARY_KEY",
    "secondaryConnectionString": "YOUR_SECONDARY_KEY",
    "path": "YOUR_TOPIC_NAME.westus-1.eventgrid.azure.net"
  }
  ```

    | Hodnota | Nahradit hodnotou |
    | --- | --- |
    | YOUR_PRIMARY_KEY | Primární připojovací řetězec použitý k ověření|
    | YOUR_SECONDARY_KEY | Sekundární připojovací řetězec použitý k ověření |
    | YOUR_TOPIC_NAME | Název vlastní téma |

- Trasy, která má typy událostí služby Event Hubs **SensorChange**, **SpaceChange**, a **TopologyOperation**:

  ```JSON
  {
    "type": "EventHub",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_PRIMARY_KEY",
    "secondaryConnectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SECONDARY_KEY",
    "path": "YOUR_EVENT_HUB_NAME"
  }
  ```

    | Hodnota | Nahradit hodnotou |
    | --- | --- |
    | YOUR_NAMESPACE | Obor názvů vašeho koncového bodu |
    | YOUR_PRIMARY_KEY | Primární připojovací řetězec použitý k ověření |
    | YOUR_SECONDARY_KEY | Sekundární připojovací řetězec použitý k ověření |
    | YOUR_EVENT_HUB_NAME | Název vašeho centra událostí |

- Trasy, která má typ událostí služby Event Hubs **DeviceMessage**. Zahrnutí `EntityPath` v **connectionString** je povinné:

  ```JSON
  {
    "type": "EventHub",
    "eventTypes": [
      "DeviceMessage"
    ],
    "connectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_PRIMARY_KEY;EntityPath=YOUR_EVENT_HUB_NAME",
    "secondaryConnectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SECONDARY_KEY;EntityPath=YOUR_EVENT_HUB_NAME",
    "path": "YOUR_EVENT_HUB_NAME"
  }
  ```

    | Hodnota | Nahradit hodnotou |
    | --- | --- |
    | YOUR_NAMESPACE | Obor názvů vašeho koncového bodu |
    | YOUR_PRIMARY_KEY | Primární připojovací řetězec použitý k ověření |
    | YOUR_SECONDARY_KEY | Sekundární připojovací řetězec použitý k ověření |
    | YOUR_EVENT_HUB_NAME | Název vašeho centra událostí |

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
