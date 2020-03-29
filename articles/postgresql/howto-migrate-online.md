---
title: Migrace do databáze Azure pro PostgreSQL – jeden server s minimálníprostožností
description: Tento článek popisuje, jak provést migraci databáze PostgreSQL s minimálními prostoji do databáze Azure pro PostgreSQL – jeden server pomocí služby migrace databáze Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 93cd390889c023adf1c30a8470e1c2298598439e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "65067516"
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql---single-server"></a>Migrace do databáze Azure pro PostgreSQL – jeden server s minimálníprostožností
Migrace PostgreSQL do Databáze Azure pro PostgreSQL můžete provádět s minimálními prostoji pomocí nově zavedené **funkce průběžné synchronizace** pro [službu Migrace databáze Azure](https://aka.ms/get-dms) (DMS). Tato funkce omezuje množství prostojů, které vznikly aplikaci.

## <a name="overview"></a>Přehled
Azure DMS provádí počáteční zatížení vašeho místního do databáze Azure pro PostgreSQL a pak průběžně synchronizuje všechny nové transakce do Azure, zatímco aplikace zůstane spuštěná. Poté, co data dožene na cílové straně Azure, zastavíte aplikaci na krátkou chvíli (minimální prostoje), počkejte na poslední dávku dat (od okamžiku, kdy zastavíte aplikaci, dokud aplikace není prakticky nedostupná, aby přijala jakýkoli nový provoz) k zachycení nahoru v cíli a pak aktualizujte připojovací řetězec tak, aby přecštoupal na Azure. Po dokončení bude vaše aplikace v Azure aktivní!

![Průběžná synchronizace se službou migrace databáze Azure](./media/howto-migrate-online/ContinuousSync.png)

## <a name="next-steps"></a>Další kroky
- Podívejte se na [modernizaci video aplikací s Microsoft Azure](https://medius.studios.ms/Embed/Video/BRK2102?sid=BRK2102), která obsahuje ukázku, která ukazuje, jak migrovat aplikace PostgreSQL do Azure Database for PostgreSQL.
- Podívejte se na kurz [Migrace PostgreSQL do databáze Azure pro PostgreSQL online pomocí DMS](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online).
