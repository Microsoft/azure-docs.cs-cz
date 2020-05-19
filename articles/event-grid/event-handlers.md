---
title: Obslužné rutiny událostí Azure Event Grid
description: Popisuje podporované obslužné rutiny událostí pro Azure Event Grid. Azure Automation, funkce, Event Hubs, Hybrid Connections, Logic Apps, Service Bus, Queue Storage, Webhooky.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: c29526bdee5ad70b3acb23117ee003877f52a55e
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592453"
---
# <a name="event-handlers-in-azure-event-grid"></a>Obslužné rutiny událostí v Azure Event Grid
Obslužná rutina události je místo, kam se událost posílá. Obslužná rutina provede několik dalších akcí zpracování události. Několik služeb Azure se automaticky konfiguruje pro zpracování událostí. Pro zpracování událostí můžete také použít jakýkoli Webhook. Webhook se nemusí hostovat v Azure a zpracovávat události. Event Grid podporuje pouze koncové body Webhooku protokolu HTTPS.

## <a name="supported-event-handlers"></a>Podporované obslužné rutiny událostí
Tady jsou podporované obslužné rutiny událostí: 

- [Webhooky](handler-webhooks.md). Azure Automation Runbooky a Logic Apps se podporují prostřednictvím webhooků. 
- [Azure Functions](handler-functions.md)
- [Centra událostí](handler-event-hubs.md)
- [Přenos hybridních připojení](handler-relay-hybrid-connections.md)
- [Service Bus fronty a témata](handler-service-bus.md)
- [Fronty úložiště](handler-storage-queues.md)

## <a name="next-steps"></a>Další kroky
- Úvod do Event Gridu najdete v článku [Informace o službě Event Grid](overview.md).
