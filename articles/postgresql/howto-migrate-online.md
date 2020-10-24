---
title: Migrace s minimálními výpadky na Azure Database for PostgreSQL – jeden server
description: Tento článek popisuje, jak pomocí Azure Database Migration Service provést migraci databáze PostgreSQL s minimálním výpadkem do provozu, aby Azure Database for PostgreSQL jeden server.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: 27da5f1b731b2cdb0604f91f7f9e78b19ee2908b
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92489791"
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql---single-server"></a>Migrace s minimálními výpadky na Azure Database for PostgreSQL – jeden server
[!INCLUDE[applies-to-postgres-single-flexible-server](includes/applies-to-postgres-single-flexible-server-hyperscale.md)]

PostgreSQL Azure Database for PostgreSQL migrace můžete provádět s minimálními výpadky pomocí nově zavedené **Možnosti nepřetržité synchronizace** pro [Azure Database Migration Service](https://aka.ms/get-dms) (DMS). Tato funkce omezuje množství výpadku, které aplikace účtuje.

## <a name="overview"></a>Přehled
Azure DMS provádí počáteční zatížení vaší místní aplikace a Azure Database for PostgreSQL a potom průběžně synchronizuje všechny nové transakce do Azure, zatímco aplikace zůstane spuštěná. Po navýšení dat na cílovou stranu Azure zastavíte aplikaci za účelem krátkého chvilky (minimální prostoje), počkáte na poslední dávku dat (od okamžiku, kdy aplikaci zastavíte, dokud nebude aplikace prakticky nedostupná, aby nedošlo k žádnému novému přenosu) a pak jste aktualizovali připojovací řetězec tak, aby odkazoval na Azure. Až budete hotovi, vaše aplikace bude v Azure živá!

:::image type="content" source="./media/howto-migrate-online/ContinuousSync.png" alt-text="Průběžná synchronizace s Azure Database Migration Service":::

## <a name="next-steps"></a>Další kroky
- Podívejte se na [modernizaci aplikace video pomocí Microsoft Azure](https://medius.studios.ms/Embed/Video/BRK2102?sid=BRK2102), která obsahuje ukázku ukazující postup migrace aplikací PostgreSQL na Azure Database for PostgreSQL.
- Podívejte se na kurz [migrace PostgreSQL a Azure Database for PostgreSQL online pomocí DMS](../dms/tutorial-postgresql-azure-postgresql-online.md).