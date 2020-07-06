---
title: Migrace s minimálními výpadky na Azure Database for PostgreSQL – jeden server
description: Tento článek popisuje, jak pomocí Azure Database Migration Service provést migraci databáze PostgreSQL s minimálním výpadkem do provozu, aby Azure Database for PostgreSQL jeden server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 93cd390889c023adf1c30a8470e1c2298598439e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "65067516"
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql---single-server"></a>Migrace s minimálními výpadky na Azure Database for PostgreSQL – jeden server
PostgreSQL Azure Database for PostgreSQL migrace můžete provádět s minimálními výpadky pomocí nově zavedené **Možnosti nepřetržité synchronizace** pro [Azure Database Migration Service](https://aka.ms/get-dms) (DMS). Tato funkce omezuje množství výpadku, které aplikace účtuje.

## <a name="overview"></a>Přehled
Azure DMS provádí počáteční zatížení vaší místní aplikace a Azure Database for PostgreSQL a potom průběžně synchronizuje všechny nové transakce do Azure, zatímco aplikace zůstane spuštěná. Po navýšení dat na cílovou stranu Azure zastavíte aplikaci za účelem krátkého chvilky (minimální prostoje), počkáte na poslední dávku dat (od okamžiku, kdy aplikaci zastavíte, dokud nebude aplikace prakticky nedostupná, aby nedošlo k žádnému novému přenosu) a pak jste aktualizovali připojovací řetězec tak, aby odkazoval na Azure. Až budete hotovi, vaše aplikace bude v Azure živá!

![Průběžná synchronizace s Azure Database Migration Service](./media/howto-migrate-online/ContinuousSync.png)

## <a name="next-steps"></a>Další kroky
- Podívejte se na [modernizaci aplikace video pomocí Microsoft Azure](https://medius.studios.ms/Embed/Video/BRK2102?sid=BRK2102), která obsahuje ukázku ukazující postup migrace aplikací PostgreSQL na Azure Database for PostgreSQL.
- Podívejte se na kurz [migrace PostgreSQL a Azure Database for PostgreSQL online pomocí DMS](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online).
