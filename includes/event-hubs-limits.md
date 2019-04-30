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
ms.openlocfilehash: 9d6b54027adcf2b12c6ca4081a11208a31f620e8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61464519"
---
Následující tabulka uvádí kvóty a omezení na konkrétní [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/). Informace o cenách služby Event Hubs najdete v tématu [ceny služby Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

| Omezení | Rozsah | Poznámky | Hodnota |
| --- | --- | --- | --- |
| Počet obory názvů služby Event Hubs na předplatné |Předplatné |- |100 |
| Počet událostí centra na obor názvů |Obor názvů |Odeslání dalších žádostí o vytvoření nové Centrum událostí odmítají. |10 |
| Počet oddílů na Centrum událostí |Entita |- |32 |
| Počet skupin uživatelů na Centrum událostí |Entita |- |20 |
| Počet připojení AMQP na obor názvů |Obor názvů |Odeslání dalších žádostí o další připojení budou odmítnuty, a volající kód obdrží výjimku. |5 000 |
| Maximální velikost události služby Event Hubs|Entita |- |1 MB |
| Maximální velikost název centra událostí |Entita |- |50 znaků. |
| Number of non-epoch receivers per consumer group |Entita |- |5 |
| Maximální doba uchovávání dat událostí |Entita |- |1-7 dní |
| Maximální počet jednotek propustnosti |Obor názvů |Překročení omezení jednotek propustnosti způsobí, že se data omezí a generuje [výjimka zaneprázdněný server](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception). Chcete-li požádat o větší počet jednotek propustnosti úrovně Standard, souborů [žádost o podporu](/azure/azure-supportability/how-to-create-azure-support-request). [Další jednotky propustnosti](../articles/event-hubs/event-hubs-auto-inflate.md) jsou k dispozici v blocích po 20 na základě potvrzení nákupu. |20 |
| Počet ověřovacích pravidel na obor názvů |Obor názvů|Odeslání dalších žádostí o vytvoření pravidla autorizace odmítají.|12 |
