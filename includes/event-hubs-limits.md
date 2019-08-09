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
ms.openlocfilehash: 928b0bcfb32d1a319c811d00fc84a37f85702895
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2019
ms.locfileid: "68857349"
---
V následující tabulce je uveden seznam kvót a omezení určených pro [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/). Informace o cenách Event Hubs najdete v článku [Event Hubs ceny](https://azure.microsoft.com/pricing/details/event-hubs/).

| Omezení | Scope | Poznámky | Value |
| --- | --- | --- | --- |
| Počet Event Hubs oborů názvů na předplatné |Subscription |- |100 |
| Počet Center událostí na obor názvů |Obor názvů |Následné žádosti o vytvoření nového centra událostí budou odmítnuty. |10 |
| Počet oddílů na centrum událostí |Entita |- |32 |
| Maximální velikost události Event Hubs|Entita |- |1 MB |
| Maximální velikost názvu centra událostí |Entita |- |50 znaků |
| Počet neepochach přijímačů na skupinu uživatelů |Entita |- |5 |
| Maximální počet jednotek propustnosti |Obor názvů |Překročení limitu jednotek propustnosti způsobí omezení vašich dat a vygeneruje [výjimku zaneprázdněnou serverem](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception). Pokud chcete požádat o větší počet jednotek propustnosti pro úroveň Standard, zaregistrujte [žádost o podporu](/azure/azure-supportability/how-to-create-azure-support-request). [Další jednotky propustnosti](../articles/event-hubs/event-hubs-auto-inflate.md) jsou k dispozici v blocích po 20 na potvrzeném nákupu. |20 |
| Počet autorizačních pravidel na obor názvů |Obor názvů|Následné žádosti o vytvoření autorizačního pravidla jsou odmítnuté.|12 |
| Počet volání metody Getruntimeinformation – | Entita | - | 50 za sekundu | 
| Počet pravidel virtuální sítě (VNet) a konfigurace protokolu IP | Entita | - | 128 | 

### <a name="event-hubs-basic-and-standard---quotas-and-limits"></a>Event Hubs úrovně Basic a Standard-kvóty a omezení
| Omezení | Scope | Poznámky | Basic | Standard |
| --- | --- | --- | -- | --- |
| Počet skupin uživatelů na centrum událostí |Entita | - |1 |20 |
| Počet připojení AMQP na obor názvů |Obor názvů |Následné žádosti o další připojení jsou odmítnuty a volající kód obdrží výjimku. |100 |5,000|
| Maximální doba uchování dat události |Entita | - |1 den |1-7 dní |


### <a name="event-hubs-dedicated---quotas-and-limits"></a>Event Hubs úrovně Dedicated – kvóty a omezení
V nabídce Event Hubs úrovně Dedicated se účtuje pevná měsíční cena s minimálním počtem 4 hodin využití. Vyhrazená úroveň nabízí všechny funkce plánu Standard, ale kapacitu a omezení na úrovni podniku pro zákazníky s náročnými úlohami. 

| Funkce | Omezení |
| --- | ---|
| Šířka pásma |  20 kapacitní jednotky |
| Názvové prostory | 50 za CU |
| Event Hubs |  1000 na obor názvů |
| Události příchozího přenosu dat | Zahrnuje |
| Velikost zprávy | 1 000 000 bajtů |
| Oddíly | 2000 za CU |
| Skupiny příjemců | Bez omezení na CU, 1000 na každé centrum událostí |
| Zprostředkovaná připojení | 100 tisíc v základu |
| Uchovávání zpráv | Až 7 dní (90 dní již brzy přijde), za 10 TB zahrnuté na CU |
| Zachycování | Zahrnuje |
