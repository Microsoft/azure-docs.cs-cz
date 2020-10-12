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
ms.openlocfilehash: dc7c86ff1df48f9ce96769098f7aab76d33c8822
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "68481423"
---
Služba Event Hubs poskytuje datový proud zpráv pomocí schématu rozdělujícího datový proud na oddíly pro jednotlivé příjemce. To zajišťuje, aby každý příjemce četl jenom konkrétní podmnožinu nebo oddíl datového proudu zpráv. Toto schéma umožňuje vodorovné škálování zpracování událostí a poskytuje další funkce zaměřené na datový proud, které nejsou ve frontách a tématech k dispozici.

Oddíl je seřazená posloupnost událostí, která se nachází v centru událostí. Události, které nově přichází, se zařazují na konec této posloupnosti. Oddíl si lze představit jako „protokol transakcí“.

![Event Hubs](./media/event-hubs-partitions/partition.png)

Event Hubs uchovává data pro nakonfigurovanou dobu uchování, která se vztahuje na všechny oddíly centra událostí. Události mizí na základě času, nemůžete je explicitně odstranit. Vzhledem k tomu, že oddíly jsou nezávislé a obsahují vlastní posloupnost dat, často se jejich velikost mění různým tempem.

![Event Hubs](./media/event-hubs-partitions/multiple-partitions.png)

Počet oddílů je určený při vytvoření a musí být v rozsahu 2 až 32. Počet oddílů není možné měnit. Proto je při nastavování počtu oddílů potřeba uvažovat z dlouhodobého hlediska. Oddíly slouží jako mechanismus pro organizaci dat a souvisí se stupněm paralelismu příjmu dat, který vyžadují přijímací aplikace. Počet oddílů v centru událostí přímo souvisí s počtem souběžných čtenářů, které plánujete mít. Pokud chcete použít vyšší počet oddílů než 32, kontaktujte tým služby Event Hubs.

Možná budete chtít nastavit, aby byla nejvyšší možná hodnota, která je v době vytváření 32. Pamatujte, že pokud bude mít více než jeden oddíl, budou události odesílány do několika oddílů bez zachování pořadí, pokud nenastavíte odesílatele tak, aby odesílali pouze jeden oddíl z 32 ponechání zbývajících 31 oddílů redundantní. V bývalém případě budete muset číst události ve všech oddílech 32. V druhém případě se od dodatečné konfigurace neúčtují žádné zjevné náklady, které musíte udělat na hostiteli procesoru událostí.

I když lze oddíly identifikovat a je možné je odeslat přímo do oddílu, nedoporučuje se odesílat přímo. Místo toho můžete použít konstrukce vyšší úrovně, které jsou představené v části [vydavatelé událostí](../articles/event-hubs/event-hubs-features.md#event-publishers) . 

Oddíly jsou vyplněny sekvencí dat událostí, která obsahují tělo události, uživatelem definovaný kontejner objektů a dat, jako je jeho posun v oddílu a jeho číslo v posloupnosti datového proudu.

Doporučujeme, abyste si vyrovnali 1:1 jednotek propustnosti a oddílů, abyste dosáhli optimálního škálování. Jeden oddíl má zaručené příchozí a odchozí přenosy až na jednu jednotku propustnosti. I když může být možné dosáhnout vyšší propustnosti oddílu, není zaručen výkon. Proto důrazně doporučujeme, aby počet oddílů v centru událostí byl větší nebo roven počtu jednotek propustnosti.

S ohledem na celkovou propustnost, kterou plánujete, potřebujete znát počet jednotek propustnosti, které požadujete, a minimální počet oddílů, ale kolik oddílů máte? Vyberte počet oddílů v závislosti na souběžném paralelismuch, které chcete dosáhnout, a také v budoucích potřebách propustnosti. Počet oddílů, které máte v centru událostí, se neúčtuje.

Další informace o oddílech a kompromisu mezi dostupností a spolehlivostí najdete v tématech [Průvodce programováním pro službu Event Hubs](../articles/event-hubs/event-hubs-programming-guide.md#partition-key) a [Dostupnost a konzistence ve službě Event Hubs](../articles/event-hubs/event-hubs-availability-and-consistency.md).
