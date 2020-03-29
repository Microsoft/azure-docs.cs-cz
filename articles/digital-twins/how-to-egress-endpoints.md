---
title: Odchozí a koncové body – digitální dvojčata Azure | Dokumenty společnosti Microsoft
description: Zjistěte, jak vytvořit a odchozí koncové body událostí v Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: 3803802a3d81655091d8be543ae9cb17221a98d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76511565"
---
# <a name="egress-and-endpoints-in-azure-digital-twins"></a>Odchozí a koncové body v Azure Digital Twins

*Koncové body* Azure Digital Twins představují zprávu nebo zprostředkovatele událostí v rámci předplatného Azure uživatele. Události a zprávy se můžou odesílat do Azure Event Hubs, Azure Event Grid a Azure Service Bus.

Události jsou směrovány do koncových bodů podle předdefinovaných předvoleb směrování. Uživatelé *určují,* které typy událostí mohou každý koncový bod přijímat.

Další informace o událostech, směrování a typech událostí najdete [v informacích o událostech a zprávách směrování v Azure Digital Twins](./concepts-events-routing.md).

## <a name="events"></a>Události

Události jsou odesílány objekty IoT (například zařízení a senzory) pro zpracování azure zpráv a událostí zprostředkovatelů. Události jsou definovány následujícím [odkazem na schéma události služby Azure Event Grid](../event-grid/event-schema.md).

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
| id | řetězec | Jedinečný identifikátor události |
| Předmět | řetězec | Cesta k předmětu události, kterou definuje vydavatel. |
| data | objekt | Data událostí specifická pro poskytovatele prostředků. |
| Eventtype | řetězec | Jeden z registrovaných typů události pro tento zdroj události. |
| eventTime | řetězec | Čas, kdy je událost generována na základě času UTC zprostředkovatele. |
| dataVersion | řetězec | Verze schématu datového objektu. Verzi schématu definuje vydavatel. |
| metadataVersion | řetězec | Verze schématu metadat události. Schéma vlastností nejvyšší úrovně definuje Event Grid. Tuto hodnotu poskytuje Event Grid. |
| téma | řetězec | Úplná cesta k prostředku ke zdroji události. Toto pole nelze zapisovat. Tuto hodnotu poskytuje Event Grid. |

Další informace o schématu událostí Mřížka událostí:

- Zkontrolujte odkaz na schéma [události Azure Event Grid](../event-grid/event-schema.md).
- Přečtěte si [odkaz Azure EventGrid Node.js SDK EventGridEvent](https://docs.microsoft.com/javascript/api/@azure/eventgrid/eventgridevent?view=azure-node-latest).

## <a name="event-types"></a>Typy událostí

Typy událostí klasifikují povahu události a jsou nastaveny v poli **eventType.** Dostupné typy událostí jsou uvedeny v následujícím seznamu:

- Operace topologie
- UdfCustom
- Změna senzoru
- Změna prostoru
- Zpráva o zařízení

Formáty událostí pro každý typ události jsou dále popsány v následujících pododdílech.

### <a name="topologyoperation"></a>Operace topologie

**Funkce TopologyOperation** se vztahuje na změny grafu. Vlastnost **předmětu** určuje typ ovlivněného objektu. Následující typy objektů mohou tuto událost vyvolat:

- Zařízení
- DeviceBlobMetadata
- PropertyExtendedProperty
- ExtendedPropertyKey
- Rozšířený typ
- Keystore
- Sestava
- Definice role
- Senzor
- SensorBlobMetadata
- SensorExtendedProperty
- Místo
- Metadata mezery v blobmetadatech
- SpaceExtendedVlastnost
- Prostorový zdroj
- Přiřazení spacerole
- Systém
- Uživatel
- Uživatel BlobMetadata
- Vlastnost UserExtendedProperty

#### <a name="example"></a>Příklad

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
| YOUR_TOPIC_NAME | Název přizpůsobeného tématu |

### <a name="udfcustom"></a>UdfCustom

**UdfCustom** je událost odeslaná uživatelem definovanou funkcí (UDF).
  
> [!IMPORTANT]  
> Tato událost musí být explicitně odeslána ze samotného udf.

#### <a name="example"></a>Příklad

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
| YOUR_TOPIC_NAME | Název přizpůsobeného tématu |

### <a name="sensorchange"></a>Změna senzoru

**SensorChange** je aktualizace stavu senzoru na základě telemetrických změn.

#### <a name="example"></a>Příklad

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
| YOUR_TOPIC_NAME | Název přizpůsobeného tématu |

### <a name="spacechange"></a>Změna prostoru

**SpaceChange** je aktualizace stavu prostoru na základě změn telemetrie.

#### <a name="example"></a>Příklad

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
| YOUR_TOPIC_NAME | Název přizpůsobeného tématu |

### <a name="devicemessage"></a>Zpráva o zařízení

Pomocí **DeviceMessage**můžete určit připojení **EventHub,** ke kterému lze směrovat nezpracované telemetrické události také z Azure Digital Twins.

> [!NOTE]
> - **DeviceMessage** je kombinovatelný pouze s **EventHub**. DeviceMessage nelze **DeviceMessage** kombinovat s žádným jiným typem události.
> - Můžete zadat pouze jeden koncový bod kombinace typu **EventHub** nebo **DeviceMessage**.

## <a name="configure-endpoints"></a>Konfigurace koncových bodů

Správa koncových bodů se uplatňuje prostřednictvím rozhraní API koncových bodů.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Následující příklady ukazují, jak nakonfigurovat podporované koncové body.

>[!IMPORTANT]
> Věnujte pečlivou pozornost atributu **eventTypes.** Definuje, které typy událostí jsou zpracovány koncovým bodem a tím určují jeho směrování.

Ověřený požadavek HTTP POST proti:

```URL
YOUR_MANAGEMENT_API_URL/endpoints
```

- Route to Service Bus typy událostí **SensorChange**, **SpaceChange**a **TopologyOperation**:

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
    | YOUR_NAMESPACE | Obor názvů koncového bodu |
    | YOUR_PRIMARY_KEY | Primární připojovací řetězec používaný k ověření |
    | YOUR_SECONDARY_KEY | Sekundární připojovací řetězec používaný k ověření |
    | YOUR_TOPIC_NAME | Název přizpůsobeného tématu |

- Route to Event Grid typy událostí **SensorChange**, **SpaceChange**a **TopologyOperation**:

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
    | YOUR_PRIMARY_KEY | Primární připojovací řetězec používaný k ověření|
    | YOUR_SECONDARY_KEY | Sekundární připojovací řetězec používaný k ověření |
    | YOUR_TOPIC_NAME | Název přizpůsobeného tématu |

- Route to Event Hubs typy událostí **SensorChange**, **SpaceChange**a **TopologyOperation**:

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
    | YOUR_NAMESPACE | Obor názvů koncového bodu |
    | YOUR_PRIMARY_KEY | Primární připojovací řetězec používaný k ověření |
    | YOUR_SECONDARY_KEY | Sekundární připojovací řetězec používaný k ověření |
    | YOUR_EVENT_HUB_NAME | Název centra událostí |

- Route to Event Hubs type **DeviceMessage**. Zahrnutí `EntityPath` do **connectionString** je povinné:

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
    | YOUR_NAMESPACE | Obor názvů koncového bodu |
    | YOUR_PRIMARY_KEY | Primární připojovací řetězec používaný k ověření |
    | YOUR_SECONDARY_KEY | Sekundární připojovací řetězec používaný k ověření |
    | YOUR_EVENT_HUB_NAME | Název centra událostí |

> [!NOTE]  
> Při vytvoření nového koncového bodu může trvat až 5 až 10 minut začít přijímat události v koncovém bodě.

## <a name="primary-and-secondary-connection-keys"></a>Primární a sekundární klíče připojení

Pokud se primární klíč připojení stane neoprávněným, systém automaticky vyzkouší sekundární klíč připojení. To poskytuje zálohu a umožňuje možnost řádně ověřit a aktualizovat primární klíč prostřednictvím rozhraní API koncových bodů.

Pokud primární i sekundární klíče připojení jsou neoprávněné, systém zadá exponenciální back-off čekací doba až 30 minut. Události jsou vynechány na každé aktivované back-off čekací doby.

Vždy, když je systém ve stavu back-off čekání, aktualizace klíčů připojení prostřednictvím rozhraní API koncových bodů může trvat až 30 minut, než se projeví.

## <a name="unreachable-endpoints"></a>Nedosažitelné koncové body

Když koncový bod stane nedostupný, systém zadá exponenciální back-off čekací doba až 30 minut. Události jsou vynechány na každé aktivované back-off čekací doby.

## <a name="next-steps"></a>Další kroky

- Přečtěte [si, jak používat Azure Digital Twins Swagger](how-to-use-swagger.md).

- Další informace o [směrování událostí a zpráv](concepts-events-routing.md) v Azure Digital Twins.
