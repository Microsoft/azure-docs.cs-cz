---
title: Migrace při minimálním výpadku – Azure Database for MySQL
description: Tento článek popisuje, jak provést migraci databáze MySQL z minimálního výpadku do Azure Database for MySQL pomocí Azure Database Migration Service.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 978312c6238315cf836cf7c26a2ee63e452d3b85
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90884613"
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>Migrace s minimálními výpadky na Azure Database for MySQL
[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

Migrace MySQL můžete provádět Azure Database for MySQL s minimálními výpadky pomocí nově zavedené **Možnosti nepřetržité synchronizace** pro [Azure Database Migration Service](https://aka.ms/get-dms) (DMS). Tato funkce omezuje množství výpadku, které aplikace účtuje.

## <a name="overview"></a>Přehled
Azure DMS provádí počáteční zatížení vaší místní aplikace a Azure Database for MySQL a potom průběžně synchronizuje všechny nové transakce do Azure, zatímco aplikace zůstane spuštěná. Po navýšení dat na cílovou stranu Azure zastavíte aplikaci za účelem krátkého chvilky (minimální prostoje), počkáte na poslední dávku dat (od okamžiku, kdy aplikaci zastavíte, dokud nebude aplikace prakticky nedostupná, aby nedošlo k žádnému novému přenosu) a pak jste aktualizovali připojovací řetězec tak, aby odkazoval na Azure. Až budete hotovi, vaše aplikace bude v Azure živá!

:::image type="content" source="./media/howto-migrate-online/ContinuousSync.png" alt-text="Průběžná synchronizace s Azure Database Migration Service":::

## <a name="next-steps"></a>Další kroky
- Prohlédněte si video a [jednoduše migrujte aplikace MySQL/PostgreSQL do spravované služby Azure](https://medius.studios.ms/Embed/Video/THR2201?sid=THR2201), která obsahuje ukázku znázorňující, jak migrovat aplikace mysql do Azure Database for MySQL.
- Podívejte se na kurz [migrace MySQL a Azure Database for MySQL online pomocí DMS](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online).
