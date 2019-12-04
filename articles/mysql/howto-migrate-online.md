---
title: Migrace při minimálním výpadku – Azure Database for MySQL
description: Tento článek popisuje, jak provést migraci databáze MySQL z minimálního výpadku do Azure Database for MySQL pomocí Azure Database Migration Service.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 9f166323cc72cd22cc4dd28babdfd056100a32e0
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74774139"
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>Migrace s minimálními výpadky na Azure Database for MySQL
Migrace MySQL můžete provádět Azure Database for MySQL s minimálními výpadky pomocí nově zavedené **Možnosti nepřetržité synchronizace** pro [Azure Database Migration Service](https://aka.ms/get-dms) (DMS). Tato funkce omezuje množství výpadku, které aplikace účtuje.

## <a name="overview"></a>Přehled
Azure DMS provádí počáteční zatížení vaší místní aplikace a Azure Database for MySQL a potom průběžně synchronizuje všechny nové transakce do Azure, zatímco aplikace zůstane spuštěná. Po navýšení dat na cílovou stranu Azure zastavíte aplikaci za účelem krátkého chvilky (minimální prostoje), počkáte na poslední dávku dat (od okamžiku, kdy aplikaci zastavíte, dokud nebude aplikace prakticky nedostupná, aby nedošlo k nedostupnosti nového provozu), aby mohla být zachycena. nahoru v cíli a pak aktualizujte připojovací řetězec tak, aby odkazoval na Azure. Až budete hotovi, vaše aplikace bude v Azure živá!

![Průběžná synchronizace s Azure Database Migration Service](./media/howto-migrate-online/ContinuousSync.png)

## <a name="next-steps"></a>Další kroky
- Prohlédněte si video a [jednoduše migrujte aplikace MySQL/PostgreSQL do spravované služby Azure](https://medius.studios.ms/Embed/Video/THR2201?sid=THR2201), která obsahuje ukázku znázorňující, jak migrovat aplikace mysql do Azure Database for MySQL.
- Podívejte se na kurz [migrace MySQL a Azure Database for MySQL online pomocí DMS](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online).
