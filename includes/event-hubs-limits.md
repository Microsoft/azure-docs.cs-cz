---
title: zahrnout soubor
description: zahrnout soubor
services: event-hubs
author: sethmanheim
ms.service: event-hubs
ms.topic: include
ms.date: 02/26/2018
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: ab4c5b98ed9f6fcc8c271797db2d81dcc7ec4449
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
ms.locfileid: "29717026"
---
Následující tabulka uvádí kvóty a omezení specifické pro [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/). Informace o cenách služby Event Hubs naleznete v tématu [cenách služby Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

| Omezení | Rozsah | Poznámky | Hodnota |
| --- | --- | --- | --- | --- |
| Počet služby event hubs na obor názvů |Obor názvů |Odeslání dalších žádostí o vytvoření nového centra událostí budou odmítnuty. |10 |
| Počet oddílů na Centrum událostí |Entita |- |32 |
| Počet skupin uživatelů na Centrum událostí |Entita |- |20 |
| Počet připojení protokolu AMQP na obor názvů |Obor názvů |Odeslání dalších žádostí o další připojení se odmítne a volající kód přijme výjimku. |5 000 |
| Maximální velikost události Event Hubs|Entita |- |256 kB |
| Maximální velikost název centra událostí |Entita |- |50 znaků |
| Počet příjemců bez epoch na skupiny uživatelů |Entita |- |5 |
| Maximální dobou uchování dat událostí |Entita |- |1-7 dní |
| Maximální počet jednotek propustnosti |Obor názvů |Překročení omezení jednotek propustnosti způsobí, že data omezeny a generuje  **[ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception)**. Můžete vyžádat větší počet jednotek propustnosti pro standardní vrstvy a podání [žádost o podporu](/azure/azure-supportability/how-to-create-azure-support-request). [Jednotky propustnosti Další](../articles/event-hubs/event-hubs-auto-inflate.md) jsou k dispozici v blocích po 20 na základě potvrdit nákupu. |20 |
| Počet pravidel autorizace na obor názvů |Obor názvů|Odeslání dalších žádostí o vytvoření pravidla autorizace budou odmítnuty.|12 |
