---
title: Odchozí a koncové body – digitální vlákna Azure | Microsoft Docs
description: Naučte se vytvářet a zamezit koncové body událostí v digitálních prozdvojeních Azure.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: 3803802a3d81655091d8be543ae9cb17221a98d8
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2020
ms.locfileid: "76511565"
---
# <a name="egress-and-endpoints-in-azure-digital-twins"></a>Odchozí a koncové body v digitálních prozdvojeních Azure

*Koncové body* digitálních vláken Azure reprezentují zprávu nebo zprostředkovatele událostí v rámci předplatného Azure uživatele. Události a zprávy je možné odesílat do Event Hubs Azure, Azure Event Grid a Azure Service Bus témata.

Události jsou směrovány do koncových bodů podle předdefinovaných předvoleb směrování. Uživatelé určují, které *typy událostí* může každý koncový bod obdržet.

Další informace o událostech, směrování a typech událostí najdete [v tématu Směrování událostí a zpráv v Azure Digital revláken](./concepts-events-routing.md).

## <a name="events"></a>Akce

Události se odesílají pomocí objektů IoT (jako jsou zařízení a senzory) pro zpracování pomocí zpráv a zprostředkovatelů událostí Azure. Události jsou definovány následujícím [odkazem schématu události Azure Event Grid](../event-grid/event-schema.md).

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
| id | string | Jedinečný identifikátor události |
| subject | string | Cesta k předmětu události, kterou definuje vydavatel. |
| data | object | Data události specifická pro poskytovatele prostředků. |
| eventType | string | Jeden z registrovaných typů události pro tento zdroj události. |
| eventTime | string | Čas, kdy se událost generuje na základě času UTC poskytovatele. |
| dataVersion | string | Verze schématu datového objektu. Verzi schématu definuje vydavatel. |
| metadataVersion | string | Verze schématu metadat události. Schéma vlastností nejvyšší úrovně definuje Event Grid. Tuto hodnotu poskytuje Event Grid. |
| téma | string | Úplná cesta prostředku ke zdroji událostí. Do tohoto pole nejde zapisovat. Tuto hodnotu poskytuje Event Grid. |

Další informace o schématu Event Grid události:

- Přečtěte si [referenční informace o schématu Azure Event Grid události](../event-grid/event-schema.md).
- Přečtěte si [referenční materiály k EVENTGRIDEVENT SDK pro Node. js pro Azure EventGrid](https://docs.microsoft.com/javascript/api/@azure/eventgrid/eventgridevent?view=azure-node-latest).

## <a name="event-types"></a>Typy událostí

Typy událostí klasifikují povahu události a jsou nastaveny v poli **EventType** . Dostupné typy událostí jsou uvedeny v následujícím seznamu:

- TopologyOperation
- UdfCustom
- SensorChange
- SpaceChange
- DeviceMessage

Formáty událostí pro každý typ události jsou dále popsány v následujících pododdílech.

### <a name="topologyoperation"></a>TopologyOperation

**TopologyOperation** se vztahuje na změny v grafu. Vlastnost **Subject** určuje typ ovlivněného objektu. Následující typy objektů mohou aktivovat tuto událost:

- Zařízení
- DeviceBlobMetadata
- DeviceExtendedProperty
- ExtendedPropertyKey
- ExtendedType
- KeyStore
- Zpráva
- Rutiny roledefinition
- Elektrické
- SensorBlobMetadata
- SensorExtendedProperty
- Kosmické aktivity
- SpaceBlobMetadata
- SpaceExtendedProperty
- SpaceResource
- SpaceRoleAssignment
- Systém
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
| YOUR_TOPIC_NAME | Název vlastního tématu |

### <a name="udfcustom"></a>UdfCustom

**UdfCustom** je událost odesílaná uživatelsky definovanou funkcí (UDF).
  
> [!IMPORTANT]  
> Tato událost musí být explicitně odeslána ze samotného formátu UDF.

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
| YOUR_TOPIC_NAME | Název vlastního tématu |

### <a name="sensorchange"></a>SensorChange

**SensorChange** je aktualizace stavu snímače na základě změn telemetrie.

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
| YOUR_TOPIC_NAME | Název vlastního tématu |

### <a name="spacechange"></a>SpaceChange

**SpaceChange** je aktualizace stavu prostoru na základě změn telemetrie.

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
| YOUR_TOPIC_NAME | Název vlastního tématu |

### <a name="devicemessage"></a>DeviceMessage

Pomocí **DeviceMessage**můžete určit připojení **EventHub** , ke kterému se mají směrovat události nezpracovaných telemetrie, i z digitálních vláken Azure.

> [!NOTE]
> - **DeviceMessage** lze kombinovat pouze s **EventHub**. **DeviceMessage** nelze kombinovat s žádným z ostatních typů událostí.
> - Můžete zadat pouze jeden koncový bod kombinace typu **EventHub** nebo **DeviceMessage**.

## <a name="configure-endpoints"></a>Konfigurace koncových bodů

Správa koncových bodů je vykonávána prostřednictvím rozhraní API koncových bodů.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Následující příklady ukazují, jak nakonfigurovat podporované koncové body.

>[!IMPORTANT]
> Věnujte pečlivou pozornost atributu **EventTypes** . Definuje, které typy událostí jsou zpracovávány koncovým bodem, a tím určí jeho směrování.

Ověřený požadavek HTTP POST s:

```URL
YOUR_MANAGEMENT_API_URL/endpoints
```

- Trasa k Service Bus typů událostí **SensorChange**, **SpaceChange**a **TopologyOperation**:

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
    | YOUR_TOPIC_NAME | Název vlastního tématu |

- Trasa k Event Grid typů událostí **SensorChange**, **SpaceChange**a **TopologyOperation**:

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
    | YOUR_TOPIC_NAME | Název vlastního tématu |

- Trasa k Event Hubs typů událostí **SensorChange**, **SpaceChange**a **TopologyOperation**:

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
    | YOUR_EVENT_HUB_NAME | Název centra událostí |

- Trasa k Event Hubs typ události **DeviceMessage**. Zahrnutí `EntityPath` v **připojovacím řetězci** je povinné:

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
    | YOUR_EVENT_HUB_NAME | Název centra událostí |

> [!NOTE]  
> Při vytváření nového koncového bodu může trvat až 5 až 10 minut, než se začnou přijímat události na koncovém bodu.

## <a name="primary-and-secondary-connection-keys"></a>Primární a sekundární klíče připojení

Když se primární připojovací klíč neautorizovaný, systém se automaticky pokusí o sekundární klíč připojení. Který poskytuje zálohu a umožňuje, aby byl primární klíč řádně ověřen a aktualizován prostřednictvím rozhraní API koncových bodů.

Pokud primární a sekundární klíče připojení jsou neautorizované, systém vstoupí do exponenciální čekací doby v intervalu až 30 minut. Události jsou vyřazeny v každé aktivované době čekání na vypnutí.

Pokaždé, když je systém v neaktivním stavu čekání, může trvat až 30 minut, než se dokončí aktualizace klíčů připojení prostřednictvím rozhraní API koncových bodů.

## <a name="unreachable-endpoints"></a>Nedosažitelné koncové body

Když se koncový bod stane nedostupným, systém vstoupí do exponenciální čekací doby v intervalu až 30 minut. Události jsou vyřazeny v každé aktivované době čekání na vypnutí.

## <a name="next-steps"></a>Další kroky

- Naučte [se používat Swagger Azure Digital](how-to-use-swagger.md)Forms.

- Přečtěte si další informace o [událostech směrování a zprávách](concepts-events-routing.md) v digitálních prozdvojeních Azure.
