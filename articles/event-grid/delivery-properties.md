---
title: Azure Event Grid – nastavení vlastních hlaviček na doručených událostech
description: Popisuje, jak můžete pro doručené události nastavit vlastní záhlaví (nebo vlastnosti doručení).
ms.topic: conceptual
ms.date: 03/24/2021
ms.openlocfilehash: fb6f0de7919ed7cf9072c0fa35e8f9be5cb5e7db
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278282"
---
# <a name="custom-delivery-properties"></a>Vlastní vlastnosti doručování
Odběry událostí umožňují nastavit hlavičky protokolu HTTP, které jsou zahrnuté v doručených událostech. Tato funkce umožňuje nastavit vlastní hlavičky, které jsou vyžadovány cílem. Při vytváření odběru událostí můžete nastavit až 10 hlaviček. Každá hodnota hlavičky by neměla být větší než 4 096 (4K) bajtů.

Můžete nastavit vlastní hlavičky pro události, které jsou dodány do následujících umístění:

- Webhooky
- Azure Service Bus témata a fronty
- Azure Event Hubs
- Hybrid Connections přenosu

Při vytváření odběru událostí v Azure Portal můžete použít kartu **vlastnosti doručování** k nastavení vlastních hlaviček protokolu HTTP. Tato stránka umožňuje nastavit pevné a dynamické hodnoty hlaviček.

## <a name="setting-static-header-values"></a>Nastavení statických hodnot hlaviček
Chcete-li nastavit záhlaví s pevnou hodnotou, zadejte název záhlaví a jeho hodnotu do odpovídajících polí:

:::image type="content" source="./media/delivery-properties/static-header-property.png" alt-text="Vlastnosti doručování – static":::

Je možné, že budete chtít kontrolu **tajné?** při poskytování citlivých dat. Citlivá data se na Azure Portal nezobrazí. 

## <a name="setting-dynamic-header-values"></a>Nastavení dynamických hodnot hlaviček
Můžete nastavit hodnotu záhlaví na základě vlastnosti v příchozí události. Pomocí syntaxe JsonPath můžete odkazovat na hodnotu vlastnosti příchozí události, která se má použít jako hodnota pro hlavičku v odchozích požadavcích. Pokud například chcete nastavit hodnotu hlavičky s názvem **kanál** pomocí hodnoty **systému** vlastností příchozí události v datech události, nakonfigurujte odběr událostí následujícím způsobem:

:::image type="content" source="./media/delivery-properties/dynamic-header-property.png" alt-text="Vlastnosti doručování – dynamická":::

## <a name="examples"></a>Příklady
Tato část obsahuje několik příkladů použití vlastností doručení.

### <a name="setting-the-authorization-header-with-a-bearer-token-non-normative-example"></a>Nastavení autorizační hlavičky s tokenem nosiče (nenormativním příkladem)

Nastavte hodnotu na autorizační hlavičku a Identifikujte požadavek pomocí vaší obslužné rutiny Webhooku. Autorizační hlavičku můžete nastavit, pokud [nechráníte Webhook pomocí Azure Active Directory](secure-webhook-delivery.md).

| Název hlavičky   | Typ záhlaví | Hodnota hlavičky |
| :--           | :--         | :--            |
|`Authorization` | Static | `BEARER SlAV32hkKG...`|

Odchozí požadavky by nyní měly obsahovat hlavičku nastavenou v odběru události:

```console
GET /home.html HTTP/1.1

Host: acme.com

User-Agent: <user-agent goes here>

Authorization: BEARER SlAV32hkKG...
```

> [!NOTE]
> Definování autorizačních hlaviček je možnost rozumné, pokud je vaším cílem Webhook. Neměl by se používat pro [funkce odebírané ID prostředku](/rest/api/eventgrid/eventsubscriptions/createorupdate#azurefunctioneventsubscriptiondestination), Service Bus, Event Hubs a Hybrid Connections, protože tyto cíle podporují vlastní schémata ověřování při použití s Event Grid.

### <a name="service-bus-example"></a>Příklad Service Bus
Azure Service Bus podporuje použití [HLAVIČKY http BrokerProperties](/rest/api/servicebus/message-headers-and-properties#message-headers) k definování vlastností zprávy při posílání jednotlivých zpráv. Hodnota `BrokerProperties` hlavičky by měla být uvedena ve formátu JSON. Pokud například potřebujete nastavit vlastnosti zprávy při posílání jedné zprávy do Service Bus, nastavte hlavičku následujícím způsobem:

| Název hlavičky | Typ záhlaví | Hodnota hlavičky |
| :-- | :-- | :-- |
|`BrokerProperties` | Static     | `BrokerProperties:  { "MessageId": "{701332E1-B37B-4D29-AA0A-E367906C206E}", "TimeToLive" : 90}` |


### <a name="event-hubs-example"></a>Příklad Event Hubs

Pokud potřebujete publikovat události do konkrétního oddílu v centru událostí, definujte v odběru události [HLAVIČKU http BrokerProperties](/rest/api/eventhub/event-hubs-runtime-rest#common-headers) a určete klíč oddílu, který identifikuje cílový oddíl centra událostí.

| Název hlavičky | Typ záhlaví | Hodnota hlavičky                                  |
| :-- | :-- | :-- |
|`BrokerProperties` | Static | `BrokerProperties: {"PartitionKey": "0000000000-0000-0000-0000-000000000000000"}`  |


### <a name="configure-time-to-live-on-outgoing-events-to-azure-storage-queues"></a>Konfigurace doby provozu na odchozích událostech do front Azure Storage
U Azure Storagech front můžete nakonfigurovat pouze dobu do živého vysílání, po které bude odchozí zpráva doručena do fronty Azure Storage. Pokud není zadaný žádný čas, výchozí hodnota TTL této zprávy je 7 dní. Můžete také nastavit, aby událost nebyla vypršet.

:::image type="content" source="./media/delivery-properties/delivery-properties-storage-queue.png" alt-text="Vlastnosti doručení – fronta úložiště":::

## <a name="next-steps"></a>Další kroky
Další informace o doručování událostí najdete v následujícím článku:

- [Doručování a opakované pokusy](delivery-and-retry.md)
- [Doručení událostí webhooku](webhook-event-delivery.md)
- [Filtrování událostí](event-filtering.md)
