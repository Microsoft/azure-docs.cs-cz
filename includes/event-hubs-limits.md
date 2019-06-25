---
title: zahrnout soubor
description: zahrnout soubor
services: event-hubs
author: sethmanheim
ms.service: event-hubs
ms.topic: include
ms.date: 05/22/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: fa6b4d6d0db09f8c4955430d6dc227356416d915
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66735994"
---
Následující tabulka uvádí kvóty a omezení na konkrétní [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/). Informace o cenách služby Event Hubs najdete v tématu [ceny služby Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

| Omezení | Scope | Poznámky | Hodnota |
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
| Počet volání getruntimeinformation – metoda | Entita | - | 50 za sekundu | 

### <a name="event-hubs-dedicated---quotas-and-limits"></a>Event Hubs Dedicated - kvóty a omezení
Event hubs úrovně Dedicated nabídka se účtuje za pevnou měsíční cenu s minimálně 4 hodin využití. Vyhrazené úroveň nabízí všechny funkce plánu Standard, ale s enterprise škálování kapacity a omezení pro zákazníky s náročné úlohy. 

| Funkce | Limits |
| --- | ---|
| Šířka pásma |  20 kapacitní jednotky |
| Obory názvů | 50 na kapacitní jednotku |
| Event Hubs |  1 000 na obor názvů |
| Události příchozího přenosu dat | Zahrnuje |
| Velikost zprávy | 1 milion bajtů |
| Oddíly | 2000 na kapacitní jednotku |
| Skupiny příjemců | Bez omezení na kapacitní jednotku, 1 000 na Centrum událostí |
| Zprostředkovaná připojení | 100 tisíc v základu |
| Uchovávání zpráv | Až na 7 dní (90 dnů uchovávání už brzo), 10 TB zahrnuté na kapacitní jednotku |
| Zachycování | Zahrnuje |
