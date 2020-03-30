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
ms.openlocfilehash: 2aca4f2c236112b80e9fc985cf80ccad6d82bde3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "75901839"
---
Následující tabulky poskytují kvóty a omezení specifická pro [Centra událostí Azure](https://azure.microsoft.com/services/event-hubs/). Informace o cenách Event Hubs najdete v [tématu Ceny Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

Následující limity jsou společné napříč základními, standardními a vyhrazenými úrovněmi. 

| Omezení | Rozsah | Poznámky | Hodnota |
| --- | --- | --- | --- |
| Počet oborů názvů Event Hubs na jedno předplatné |Předplatné |- |100 |
| Počet rozbočovačů událostí na obor názvů |Obor názvů |Následné požadavky na vytvoření nového centra událostí jsou odmítnuty. |10 |
| Počet oddílů na rozbočovač událostí |Entita |- |32 |
| Maximální velikost názvu centra událostí |Entita |- |50 znaků |
| Počet neepochních přijímačů na skupinu příjemců |Entita |- |5 |
| Jednotky maximální propustností |Obor názvů |Překročení limitu jednotky propustnost způsobí omezení dat a vygeneruje [výjimku zaneprázdněnserveru](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception). Chcete-li požádat o větší počet jednotek propustnostpro úroveň Standard, podložte [žádost o podporu](/azure/azure-portal/supportability/how-to-create-azure-support-request). [Další jednotky propustnost jsou](../articles/event-hubs/event-hubs-auto-inflate.md) k dispozici v blocích po 20 na základě potvrzeného nákupu. |20 |
| Počet autorizačních pravidel pro obor názvů |Obor názvů|Následné žádosti o vytvoření pravidla autorizace jsou odmítnuty.|12 |
| Počet volání metody GetRuntimeInformation | Entita | - | 50 za sekundu | 
| Počet pravidel konfigurace virtuální sítě (VNet) a IP konfigurace | Entita | - | 128 | 

### <a name="event-hubs-basic-and-standard---quotas-and-limits"></a>Centra událostí Basic a Standard – kvóty a limity
| Omezení | Rozsah | Poznámky | Basic | Standard |
| --- | --- | --- | -- | --- |
| Maximální velikost události Event Hubs|Entita | &nbsp; | 256 kB | 1 MB |
| Počet skupin spotřebitelů na centrum událostí |Entita | &nbsp; |1 |20 |
| Počet připojení AMQP na obor názvů |Obor názvů |Následné požadavky na další připojení jsou odmítnuty a volající kód přijímá výjimku. |100 |5 000|
| Maximální doba uchovávání údajů o událostech |Entita | &nbsp; |1 den |1-7 dní |
|Obor názvů s povoleným Apache Kafka|Obor názvů |Obor názvů Event Hubs streamuje aplikace pomocí protokolu Kafka |Ne | Ano |
|Zachycování |Entita | Pokud je tato možnost povolena, mikrodávky na stejném datovém proudu |Ne |Ano |


### <a name="event-hubs-dedicated---quotas-and-limits"></a>Centra událostí vyhrazená – kvóty a limity
Vyhrazená nabídka Event Hubs se účtuje za pevnou měsíční cenu s minimálně 4 hodinami používání. Úroveň Dedicated nabízí všechny funkce standardního plánu, ale s kapacitou podnikového škálování a omezením pro zákazníky s náročnými úlohami. 

| Funkce | Omezení |
| --- | ---|
| Šířka pásma |  20 Cu |
| Jmenné prostory | 50 za CU |
| Event Hubs |  1000 na obor názvů |
| Události příchozího přenosu dat | Zahrnuje |
| Velikost zprávy | 1 MB |
| Oddíly | 2000 za CU |
| Skupiny příjemců | Bez omezení na CU, 1000 na centrum událostí |
| Zprostředkovaná připojení | 100 K v ceně |
| Uchovávání zpráv | 90 dní, 10 TB včetně CU |
| Zachycování | Zahrnuje |
