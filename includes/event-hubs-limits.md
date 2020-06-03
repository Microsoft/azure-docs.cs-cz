---
title: zahrnout soubor
description: zahrnout soubor
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 05/22/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 333f2317fcc834a10b7336bbda9a43ba16a7ad38
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/03/2020
ms.locfileid: "84317720"
---
Následující tabulky poskytují kvóty a omezení specifické pro [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/). Informace o cenách Event Hubs najdete v článku [Event Hubs ceny](https://azure.microsoft.com/pricing/details/event-hubs/).

Následující omezení jsou společná napříč úrovněmi Basic a Standard. 

| Omezení | Rozsah | Poznámky | Hodnota |
| --- | --- | --- | --- |
| Počet Event Hubs oborů názvů na předplatné |Předplatné |- |100 |
| Počet Center událostí na obor názvů |Obor názvů |Následné žádosti o vytvoření nového centra událostí budou odmítnuty. |10 |
| Počet oddílů na centrum událostí |Entita |- |32 |
| Maximální velikost názvu centra událostí |Entita |- | 256 znaků |
| Maximální velikost názvu skupiny uživatelů |Entita |- | 256 znaků |
| Počet neepochach přijímačů na skupinu uživatelů |Entita |- |5 |
| Maximální počet jednotek propustnosti |Obor názvů |Překročení limitu jednotek propustnosti způsobí omezení vašich dat a vygeneruje [výjimku zaneprázdněnou serverem](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception). Pokud chcete požádat o větší počet jednotek propustnosti pro úroveň Standard, zaregistrujte [žádost o podporu](/azure/azure-portal/supportability/how-to-create-azure-support-request). [Další jednotky propustnosti](../articles/event-hubs/event-hubs-auto-inflate.md) jsou k dispozici v blocích po 20 na potvrzeném nákupu. |20 |
| Počet autorizačních pravidel na obor názvů |Obor názvů|Následné žádosti o vytvoření autorizačního pravidla jsou odmítnuté.|12 |
| Počet volání metody Getruntimeinformation – | Entita | - | 50 za sekundu | 
| Počet pravidel virtuální sítě (VNet) a konfigurace protokolu IP | Entita | - | 128 | 

### <a name="event-hubs-basic-and-standard---quotas-and-limits"></a>Event Hubs úrovně Basic a Standard-kvóty a omezení
| Omezení | Rozsah | Poznámky | Základní | Standard |
| --- | --- | --- | -- | --- |
| Maximální velikost události Event Hubs|Entita | &nbsp; | 256 kB | 1 MB |
| Počet skupin uživatelů na centrum událostí |Entita | &nbsp; |1 |20 |
| Počet připojení AMQP na obor názvů |Obor názvů |Následné žádosti o další připojení jsou odmítnuty a volající kód obdrží výjimku. |100 |5 000|
| Maximální doba uchování dat události |Entita | &nbsp; |1 den |1-7 dní |
|Apache Kafka povolený obor názvů|Obor názvů |Event Hubs aplikace streamování v oboru názvů pomocí protokolu Kafka |No | Ano |
|Zachycování |Entita | Když je tato možnost povolená, mikrodávky na stejném datovém proudu |No |Ano |


### <a name="event-hubs-dedicated---quotas-and-limits"></a>Event Hubs úrovně Dedicated – kvóty a omezení
V nabídce Event Hubs úrovně Dedicated se účtuje pevná měsíční cena s minimálním počtem 4 hodin využití. Vyhrazená úroveň nabízí všechny funkce plánu Standard, ale kapacitu a omezení na úrovni podniku pro zákazníky s náročnými úlohami. 

| Příznak | Omezení |
| --- | ---|
| Šířka pásma |  20 kapacitní jednotky |
| Obory názvů | 50 za CU |
| Event Hubs |  1000 na obor názvů |
| Události příchozího přenosu dat | Zahrnuje |
| Velikost zprávy | 1 MB |
| Oddíly | 2000 za CU |
| Skupiny příjemců | Bez omezení na CU, 1000 na každé centrum událostí |
| Zprostředkovaná připojení | zahrnuto 100 K |
| Uchovávání zpráv | 90 dní, zahrnutých 10 TB za CU |
| Zachycování | Zahrnuje |
