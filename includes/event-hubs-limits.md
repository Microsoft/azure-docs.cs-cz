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
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2020
ms.locfileid: "75901839"
---
Následující tabulky poskytují kvóty a omezení specifické pro [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/). Informace o cenách Event Hubs najdete v článku [Event Hubs ceny](https://azure.microsoft.com/pricing/details/event-hubs/).

Následující omezení jsou společná pro základní, standardní a vyhrazené úrovně. 

| škálování | Rozsah | Poznámky | Hodnota |
| --- | --- | --- | --- |
| Počet Event Hubs oborů názvů na předplatné |Předplatné |- |100 |
| Počet Center událostí na obor názvů |Obor názvů |Následné žádosti o vytvoření nového centra událostí budou odmítnuty. |10 |
| Počet oddílů na centrum událostí |Entita |- |32 |
| Maximální velikost názvu centra událostí |Entita |- |50 znaků |
| Počet neepochach přijímačů na skupinu uživatelů |Entita |- |5 |
| Maximální počet jednotek propustnosti |Obor názvů |Překročení limitu jednotek propustnosti způsobí omezení vašich dat a vygeneruje [výjimku zaneprázdněnou serverem](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception). Pokud chcete požádat o větší počet jednotek propustnosti pro úroveň Standard, zaregistrujte [žádost o podporu](/azure/azure-portal/supportability/how-to-create-azure-support-request). [Další jednotky propustnosti](../articles/event-hubs/event-hubs-auto-inflate.md) jsou k dispozici v blocích po 20 na potvrzeném nákupu. |20 |
| Počet autorizačních pravidel na obor názvů |Obor názvů|Následné žádosti o vytvoření autorizačního pravidla jsou odmítnuté.|12 |
| Počet volání metody Getruntimeinformation – | Entita | - | 50 za sekundu | 
| Počet pravidel virtuální sítě (VNet) a konfigurace protokolu IP | Entita | - | 128 | 

### <a name="event-hubs-basic-and-standard---quotas-and-limits"></a>Event Hubs úrovně Basic a Standard-kvóty a omezení
| škálování | Rozsah | Poznámky | Úroveň Basic | Úroveň Standard |
| --- | --- | --- | -- | --- |
| Maximální velikost události Event Hubs|Entita | &nbsp; | 256 kB | 1 MB |
| Počet skupin uživatelů na centrum událostí |Entita | &nbsp; |1\. místo |20 |
| Počet připojení AMQP na obor názvů |Obor názvů |Následné žádosti o další připojení jsou odmítnuty a volající kód obdrží výjimku. |100 |5 000|
| Maximální doba uchování dat události |Entita | &nbsp; |1 den |1-7 dní |
|Apache Kafka povolený obor názvů|Obor názvů |Event Hubs aplikace streamování v oboru názvů pomocí protokolu Kafka |Ne | Ano |
|Capture |Entita | Když je tato možnost povolená, mikrodávky na stejném datovém proudu |Ne |Ano |


### <a name="event-hubs-dedicated---quotas-and-limits"></a>Event Hubs úrovně Dedicated – kvóty a omezení
V nabídce Event Hubs úrovně Dedicated se účtuje pevná měsíční cena s minimálním počtem 4 hodin využití. Vyhrazená úroveň nabízí všechny funkce plánu Standard, ale kapacitu a omezení na úrovni podniku pro zákazníky s náročnými úlohami. 

| Funkce | Omezení |
| --- | ---|
| Šířka pásma |  20 kapacitní jednotky |
| Obory názvů | 50 za CU |
| Event Hubs |  1000 na obor názvů |
| Události příchozího přenosu dat | Zahrnuté |
| Velikost zprávy | 1 MB |
| Oddíly | 2000 za CU |
| Skupiny příjemců | Bez omezení na CU, 1000 na každé centrum událostí |
| Zprostředkovaná připojení | 100 tisíc v základu |
| Uchovávání zpráv | 90 dní, zahrnutých 10 TB za CU |
| Capture | Zahrnuté |
