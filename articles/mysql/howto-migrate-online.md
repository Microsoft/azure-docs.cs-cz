---
title: Migrace s minimem výpadků ke službě Azure Database for MySQL
description: Tento článek popisuje, jak provést migraci minimálními prostoji databáze MySQL do služby Azure Database for MySQL pomocí Azure Database Migration Service.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 12/07/2018
ms.openlocfilehash: 267212d8f832b96bf66145b97c3464471e28593d
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2018
ms.locfileid: "53014187"
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>Migrace s minimem výpadků ke službě Azure Database for MySQL
Můžete provést migraci MySQL do služby Azure Database for MySQL s minimálními prostoji pomocí nově zavedená **Průběžná synchronizace schopností** pro [Azure Database Migration Service](https://aka.ms/get-dms) (DMS). Tato funkce omezuje množství prostoje, které se účtují podle aplikace.

## <a name="overview"></a>Přehled
Azure DMS provede počáteční načtení místní ke službě Azure Database for MySQL a potom průběžně synchronizuje všechny nové transakce do Azure během aplikace zůstane spuštěný. Po data vyrovnat na cílové straně Azure, zastavte aplikaci pro krátké chvíle (minimální výpadek), počkejte pro poslední dávku dat (od doby, dokud aplikace není dostupná efektivně provádět žádné nové přenosy dat se zastavit aplikaci) k zachycení až do cíle a pak aktualizujte připojovací řetězec tak, aby odkazoval do Azure. Až budete hotovi, vaše aplikace bude živě v Azure!

![Průběžná synchronizace s Azure Database Migration Service](./media/howto-migrate-online/ContinuousSync.png)

## <a name="next-steps"></a>Další postup
- Zobrazení videa [snadnou migraci MySQL nebo PostgreSQL aplikace do Azure se spravovat služby](https://medius.studios.ms/Embed/Video/THR2201?sid=THR2201), která obsahuje ukázky, ukazuje, jak migrovat aplikace MySQL do služby Azure Database for MySQL.
- Projděte si kurz [migraci MySQL do služby Azure Database for MySQL online pomocí DMS](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online).
