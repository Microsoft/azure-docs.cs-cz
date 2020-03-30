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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "68481423"
---
Služba Event Hubs poskytuje datový proud zpráv pomocí schématu rozdělujícího datový proud na oddíly pro jednotlivé příjemce. To zajišťuje, aby každý příjemce četl jenom konkrétní podmnožinu nebo oddíl datového proudu zpráv. Toto schéma umožňuje vodorovné škálování zpracování událostí a poskytuje další funkce zaměřené na datový proud, které nejsou ve frontách a tématech k dispozici.

Oddíl je seřazená posloupnost událostí, která se nachází v centru událostí. Události, které nově přichází, se zařazují na konec této posloupnosti. Oddíl si lze představit jako „protokol transakcí“.

![Event Hubs](./media/event-hubs-partitions/partition.png)

Centra událostí uchovávají data pro nakonfigurovaný čas uchovávání informací, který platí ve všech oddílech v centru událostí. Události mizí na základě času, nemůžete je explicitně odstranit. Vzhledem k tomu, že oddíly jsou nezávislé a obsahují vlastní posloupnost dat, často se jejich velikost mění různým tempem.

![Event Hubs](./media/event-hubs-partitions/multiple-partitions.png)

Počet oddílů je určený při vytvoření a musí být v rozsahu 2 až 32. Počet oddílů není možné měnit. Proto je při nastavování počtu oddílů potřeba uvažovat z dlouhodobého hlediska. Oddíly slouží jako mechanismus pro organizaci dat a souvisí se stupněm paralelismu příjmu dat, který vyžadují přijímací aplikace. Počet oddílů v centru událostí přímo souvisí s počtem souběžných čtenářů, které plánujete mít. Pokud chcete použít vyšší počet oddílů než 32, kontaktujte tým služby Event Hubs.

Můžete ji nastavit jako nejvyšší možnou hodnotu, která je 32, v době vytvoření. Nezapomeňte, že mít více než jeden oddíl bude mít za následek události odeslané do více oddílů bez zachování pořadí, pokud nakonfigurujete odesílatele odeslat pouze do jednoho oddílu z 32 opuštění zbývajících 31 oddílů redundantní. V prvním případě budete muset číst události ve všech oddílech 32. V druhém případě neexistuje žádné zjevné dodatečné náklady kromě další konfigurace, kterou musíte provést na hostiteli procesoru událostí.

Zatímco oddíly jsou identifikovatelné a mohou být odesílány přímo, odesílání přímo do oddílu se nedoporučuje. Místo toho můžete použít vyšší úroveň konstrukce zavedené v části [Vydavatelé událostí.](../articles/event-hubs/event-hubs-features.md#event-publishers) 

Oddíly jsou vyplněny posloupnost dat událostí, která obsahuje tělo události, uživatelem definované kontejnervlastnosti a metadata, jako je jeho posun v oddílu a jeho číslo v posloupnosti datového proudu.

Doporučujeme vyvážit jednotky propustnost 1:1 a oddíly, abyste dosáhli optimálního měřítka. Jeden oddíl má zaručený příchozí a odchozí až jednu jednotku propustnost. Zatímco můžete dosáhnout vyšší propustnost na oddíl, výkon není zaručena. To je důvod, proč důrazně doporučujeme, aby počet oddílů v centru událostí byl větší nebo roven počtu jednotek propustností.

Vzhledem k celkové propustnosti, kterou plánujete potřebovat, znáte počet jednotek propustností, které požadujete, a minimální počet oddílů, ale kolik oddílů byste měli mít? Zvolte počet oddílů na základě navazující paralelismu, kterého chcete dosáhnout, stejně jako vaše budoucí propustnost potřebuje. Počet oddílů, které máte v centru událostí, se neúčtuje.

Další informace o oddílech a kompromisu mezi dostupností a spolehlivostí najdete v tématech [Průvodce programováním pro službu Event Hubs](../articles/event-hubs/event-hubs-programming-guide.md#partition-key) a [Dostupnost a konzistence ve službě Event Hubs](../articles/event-hubs/event-hubs-availability-and-consistency.md).
