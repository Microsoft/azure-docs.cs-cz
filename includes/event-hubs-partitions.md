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
ms.openlocfilehash: 5d6d2285680884cf20daaeef0a1ade53afad5b90
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827674"
---
Služba Event Hubs poskytuje datový proud zpráv pomocí schématu rozdělujícího datový proud na oddíly pro jednotlivé příjemce. To zajišťuje, aby každý příjemce četl jenom konkrétní podmnožinu nebo oddíl datového proudu zpráv. Toto schéma umožňuje vodorovné škálování zpracování událostí a poskytuje další funkce zaměřené na datový proud, které nejsou ve frontách a tématech k dispozici.

Oddíl je seřazená posloupnost událostí, která se nachází v centru událostí. Události, které nově přichází, se zařazují na konec této posloupnosti. Oddíl si lze představit jako „protokol transakcí“.

![Event Hubs](./media/event-hubs-partitions/partition.png)

Event Hubs uchovává data dobu uchování nakonfigurované, která se vztahuje na všechny oddíly v centra událostí. Události mizí na základě času, nemůžete je explicitně odstranit. Vzhledem k tomu, že oddíly jsou nezávislé a obsahují vlastní posloupnost dat, často se jejich velikost mění různým tempem.

![Event Hubs](./media/event-hubs-partitions/multiple_partitions.png)

Počet oddílů je určený při vytvoření a musí být v rozsahu 2 až 32. Počet oddílů není možné měnit. Proto je při nastavování počtu oddílů potřeba uvažovat z dlouhodobého hlediska. Oddíly slouží jako mechanismus pro organizaci dat a souvisí se stupněm paralelismu příjmu dat, který vyžadují přijímací aplikace. Počet oddílů v centru událostí přímo souvisí s počtem souběžných čtenářů, které plánujete mít. Pokud chcete použít vyšší počet oddílů než 32, kontaktujte tým služby Event Hubs.

I když oddíly identifikovat a je možné odeslat přímo, odesílání přímo do oddílu se nedoporučuje. Místo toho můžete použít konstrukce vyšší úrovně počínaje [zdroje událostí](../articles/event-hubs/event-hubs-features.md#event-publishers) oddílu. 

Oddíly jsou naplněné posloupností dat událostí, která obsahují tělo události, uživatelem definované vlastnosti kontejneru objektů a dat a metadat – například její posun v oddílu a pořadí v posloupnosti datového proudu.

Doporučujeme, abyste vyvážili jednotky propustnosti 1:1 a oddíly, abyste dosáhli optimálního škálování. Jednoho oddílu dovoluje zaručené příchozí a výchozí přenos maximálně jednu jednotku propustnosti. Přestože je možné dosáhnout vyšší propustnost na oddíl, není zaručeno, že výkon. To je důvod, proč důrazně doporučujeme, že počet oddílů v Centru událostí být větší než nebo rovna počtu jednotek propustnosti.

Zadaný celkovou propustnost, kterou plánujete nutnosti, víte, že počet jednotek propustnosti, které vyžadujete a minimální počet oddílů, ale počet oddílů byste měli mít? Vyberte počet oddílů podle stupněm paralelismu příjmu dat, které chcete dosáhnout, jakož i vašim potřebám propustnosti budoucí. Neplatí žádné poplatky pro počet oddílů, které máte v rámci centra událostí.

Další informace o oddílech a kompromisu mezi dostupností a spolehlivostí najdete v tématech [Průvodce programováním pro službu Event Hubs](../articles/event-hubs/event-hubs-programming-guide.md#partition-key) a [Dostupnost a konzistence ve službě Event Hubs](../articles/event-hubs/event-hubs-availability-and-consistency.md).
