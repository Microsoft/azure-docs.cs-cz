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
ms.openlocfilehash: 8c836582798f40cf6e9ffff264c1612cb4037f74
ms.sourcegitcommit: 9f330c3393a283faedaf9aa75b9fcfc06118b124
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2019
ms.locfileid: "71996892"
---
Následující tabulky poskytují kvóty a omezení specifické pro [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/). Informace o cenách Event Hubs najdete v článku [Event Hubs ceny](https://azure.microsoft.com/pricing/details/event-hubs/).

Následující omezení jsou společná pro základní, standardní a vyhrazené úrovně. 

| Počtu | Rozsah | Poznámky | Hodnota |
| --- | --- | --- | --- |
| Počet Event Hubs oborů názvů na předplatné |Formě |- |100 |
| Počet Center událostí na obor názvů |Obor názvů |Následné žádosti o vytvoření nového centra událostí budou odmítnuty. |10pruhový |
| Počet oddílů na centrum událostí |Entity |- |32 |
| Maximální velikost názvu centra událostí |Entity |- |50 znaků |
| Počet neepochach přijímačů na skupinu uživatelů |Entity |- |5 |
| Maximální počet jednotek propustnosti |Obor názvů |Překročení limitu jednotek propustnosti způsobí omezení vašich dat a vygeneruje [výjimku zaneprázdněnou serverem](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception). Pokud chcete požádat o větší počet jednotek propustnosti pro úroveň Standard, zaregistrujte [žádost o podporu](/azure/azure-supportability/how-to-create-azure-support-request). [Další jednotky propustnosti](../articles/event-hubs/event-hubs-auto-inflate.md) jsou k dispozici v blocích po 20 na potvrzeném nákupu. |20o |
| Počet autorizačních pravidel na obor názvů |Obor názvů|Následné žádosti o vytvoření autorizačního pravidla jsou odmítnuté.|12,5 |
| Počet volání metody Getruntimeinformation – | Entity | - | 50 za sekundu | 
| Počet pravidel virtuální sítě (VNet) a konfigurace protokolu IP | Entity | - | 128 | 

### <a name="event-hubs-basic-and-standard---quotas-and-limits"></a>Event Hubs úrovně Basic a Standard-kvóty a omezení
| Počtu | Rozsah | Poznámky | Základní | Standardní |
| --- | --- | --- | -- | --- |
| Maximální velikost události Event Hubs|Entity | &nbsp; | 256 KB | 1 MB |
| Počet skupin uživatelů na centrum událostí |Entity | &nbsp; |první |20o |
| Počet připojení AMQP na obor názvů |Obor názvů |Následné žádosti o další připojení jsou odmítnuty a volající kód obdrží výjimku. |100 |5 000|
| Maximální doba uchování dat události |Entity | &nbsp; |1 den |1-7 dní |
|Apache Kafka povolený obor názvů|Obor názvů |Event Hubs aplikace streamování v oboru názvů pomocí protokolu Kafka |Ne | Ano |
|Snímky |Entity | Když je tato možnost povolená, mikrodávky na stejném datovém proudu |Ne |Ano |


### <a name="event-hubs-dedicated---quotas-and-limits"></a>Event Hubs úrovně Dedicated – kvóty a omezení
V nabídce Event Hubs úrovně Dedicated se účtuje pevná měsíční cena s minimálním počtem 4 hodin využití. Vyhrazená úroveň nabízí všechny funkce plánu Standard, ale kapacitu a omezení na úrovni podniku pro zákazníky s náročnými úlohami. 

| Funkce | Lhůty |
| --- | ---|
| Připojení |  20 kapacitní jednotky |
| Jmenné prostory | 50 za CU |
| Event Hubs |  1000 na obor názvů |
| Události příchozího přenosu dat | Obsaženy |
| Velikost zprávy | 1 MB |
| Disk | 2000 za CU |
| Skupiny uživatelů | Bez omezení na CU, 1000 na každé centrum událostí |
| Zprostředkovaná připojení | zahrnuto 100 K |
| Uchovávání zpráv | 90 dní, zahrnutých 10 TB za CU |
| Snímky | Obsaženy |
