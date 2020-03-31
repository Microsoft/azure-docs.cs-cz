---
title: Migrace s minimálními prostoji – Azure Database for MySQL
description: Tento článek popisuje, jak provést migraci databáze MySQL s minimálními prostoji do databáze Azure pro MySQL pomocí služby Migrace databáze Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: df818fa4106aec341607d8142b2a672699b8e9d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063345"
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>Migrace s minimálními prostoji do databáze Azure pro MySQL
Migrace MySQL do databáze Azure pro MySQL můžete provádět s minimálními prostoji pomocí nově zavedené **funkce průběžné synchronizace** pro [službu Azure Database Migration Service](https://aka.ms/get-dms) (DMS). Tato funkce omezuje množství prostojů, které vznikly aplikaci.

## <a name="overview"></a>Přehled
Azure DMS provádí počáteční zatížení vašeho místního do databáze Azure pro MySQL a pak průběžně synchronizuje všechny nové transakce do Azure, zatímco aplikace zůstane spuštěná. Poté, co data dožene na cílové straně Azure, zastavíte aplikaci na krátkou chvíli (minimální prostoje), počkejte na poslední dávku dat (od okamžiku, kdy zastavíte aplikaci, dokud aplikace není prakticky nedostupná, aby přijala jakýkoli nový provoz) k zachycení nahoru v cíli a pak aktualizujte připojovací řetězec tak, aby přecštoupal na Azure. Po dokončení bude vaše aplikace v Azure aktivní!

![Průběžná synchronizace se službou migrace databáze Azure](./media/howto-migrate-online/ContinuousSync.png)

## <a name="next-steps"></a>Další kroky
- Podívejte se na video [Snadno migrovat aplikace MySQL/PostgreSQL do spravované služby Azure](https://medius.studios.ms/Embed/Video/THR2201?sid=THR2201), která obsahuje ukázku, která ukazuje, jak migrovat aplikace MySQL do Azure Database for MySQL.
- Podívejte se na kurz [Migrace MySQL do databáze Azure pro MySQL online pomocí DMS](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online).
