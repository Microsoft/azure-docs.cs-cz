---
title: zahrnout soubor
description: zahrnout soubor
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 09/10/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: ea1ac064799b0cede1de82851a514a2b389f20aa
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92499246"
---
Následující tabulky poskytují kvóty a omezení specifické pro [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/). Informace o cenách Event Hubs najdete v článku [Event Hubs ceny](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="common-limits-for-all-tiers"></a>Společná omezení pro všechny úrovně
Následující omezení jsou společná napříč všemi úrovněmi. 

| Omezení |  Poznámky | Hodnota |
| --- |  --- | --- |
| Počet Event Hubs oborů názvů na předplatné |- |100 |
| Počet Center událostí na obor názvů | Následné žádosti o vytvoření nového centra událostí budou odmítnuty. |10 |
| Počet oddílů na centrum událostí |- |32 |
| Velikost názvu centra událostí |- | 256 znaků |
| Velikost názvu skupiny uživatelů |- | 256 znaků |
| Počet neepochach přijímačů na skupinu uživatelů |- |5 |
| Počet autorizačních pravidel na obor názvů | Následné žádosti o vytvoření autorizačního pravidla jsou odmítnuté.|12 |
| Počet volání metody Getruntimeinformation – |  - | 50 za sekundu | 
| Počet pravidel virtuální sítě (VNet) a konfigurace protokolu IP | - | 128 | 


### <a name="basic-and-standard-tiers"></a>Úrovně Basic a Standard
V následující tabulce jsou uvedena omezení, která se mohou lišit pro úrovně Basic a Standard. 

| Omezení | Poznámky | Basic | Standard |
| --- |  --- | -- | --- |
| Maximální velikost události Event Hubs| &nbsp; | 256 kB | 1 MB |
| Počet skupin uživatelů na centrum událostí | &nbsp; |1 |20 |
| Počet připojení AMQP na obor názvů | Následné žádosti o další připojení jsou odmítnuty a volající kód obdrží výjimku. |100 |5 000|
| Maximální doba uchování dat události | &nbsp; |1 den |1-7 dní |
| Maximální počet jednotek propustnosti |Překročení limitu jednotek propustnosti způsobí omezení vašich dat a vygeneruje [výjimku zaneprázdněnou serverem](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception). Pokud chcete požádat o větší počet jednotek propustnosti pro úroveň Standard, zaregistrujte [žádost o podporu](/azure/azure-portal/supportability/how-to-create-azure-support-request). [Další jednotky propustnosti](../articles/event-hubs/event-hubs-auto-inflate.md) jsou k dispozici v blocích po 20 na potvrzeném nákupu. |20 | 20 | 

### <a name="dedicated-tier"></a>Vyhrazená úroveň
V nabídce Event Hubs úrovně Dedicated se účtuje pevná měsíční cena s minimálním počtem 4 hodin využití. Vyhrazená úroveň nabízí všechny funkce plánu Standard, ale kapacitu a omezení na úrovni podniku pro zákazníky s náročnými úlohami. 

V tomto [dokumentu](https://docs.microsoft.com/azure/event-hubs/event-hubs-dedicated-cluster-create-portal) najdete informace o tom, jak vytvořit vyhrazený cluster Event Hubs pomocí Azure Portal.

| Funkce | Omezení |
| --- | ---|
| Šířka pásma |  20 kapacitní jednotky |
| Obory názvů | 50 za CU |
| Event Hubs |  1000 na obor názvů |
| Velikost zpráv | 1 MB |
| Oddíly | 2000 za CU |
| Skupiny uživatelů | Bez omezení na CU, 1000 na každé centrum událostí |
| Zprostředkovaná připojení | zahrnuto 100 K |
| Doba uchovávání zpráv | 90 dní, zahrnutých 10 TB za CU |
| Události příchozího přenosu dat | Zahrnuje |
| Zachytávání | Zahrnuje |


### <a name="schema-registry-limitations"></a>Omezení registru schématu

#### <a name="limits-that-are-the-same-for-standard-and-dedicated-tiers"></a>Omezení, která jsou stejná pro **standardní** a **vyhrazené** úrovně 
| Funkce | Omezení | 
| --- |  --- | -- |
| Maximální délka názvu skupiny schémat | 50 |  
| Maximální délka názvu schématu | 100 |    
| Velikost v bajtech na schéma | 1 MB |   
| Počet vlastností na skupinu schémat | 1024 |
| Velikost v bajtech na klíč vlastnosti skupiny | 256 | 
| Velikost v bajtech na hodnotu vlastnosti skupiny | 1024 | 


#### <a name="limits-that-are-different-for-standard-and-dedicated-tiers"></a>Omezení, která jsou odlišná na úrovni **Standard** a **vyhrazené** 

| Omezení | Standard | Vyhrazená | 
| --- |  --- | -- | --- |
| Velikost registru schématu (obor názvů) v megabajtů bajtech | 25 |  1024 |
| Počet skupin schémat v registru schématu (obor názvů)| 1 (Kromě výchozího nastavení) | 1000 |
| Počet verzí schématu napříč všemi skupinami schémat | 25 | 10000 |





