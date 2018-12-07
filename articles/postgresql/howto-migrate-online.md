---
title: Migrace s minimem výpadků ke službě Azure Database for PostgreSQL
description: Tento článek popisuje, jak provést migraci minimálními prostoji databázi PostgreSQL do služby Azure Database for PostgreSQL s využitím Azure Database Migration Service.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 12/07/2018
ms.openlocfilehash: 0c8c3443a19c26dade9699560e883969d3c074df
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2018
ms.locfileid: "53010837"
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql"></a>Migrace s minimem výpadků ke službě Azure Database for PostgreSQL
Můžete provést migraci PostgreSQL do služby Azure Database for PostgreSQL s minimálními prostoji pomocí nově zavedená **Průběžná synchronizace schopností** pro [Azure Database Migration Service](https://aka.ms/get-dms) (DMS) . Tato funkce omezuje množství prostoje, které se účtují podle aplikace.

## <a name="overview"></a>Přehled
Azure DMS provede počáteční načtení místní ke službě Azure Database for PostgreSQL a potom průběžně synchronizuje všechny nové transakce do Azure během aplikace zůstane spuštěný. Po data vyrovnat na cílové straně Azure, zastavte aplikaci pro krátké chvíle (minimální výpadek), počkejte pro poslední dávku dat (od doby, dokud aplikace není dostupná efektivně provádět žádné nové přenosy dat se zastavit aplikaci) k zachycení až do cíle a pak aktualizujte připojovací řetězec tak, aby odkazoval do Azure. Až budete hotovi, vaše aplikace bude živě v Azure!

![Průběžná synchronizace s Azure Database Migration Service](./media/howto-migrate-online/ContinuousSync.png)

## <a name="next-steps"></a>Další postup
- Zobrazení videa [modernizace aplikací s Microsoft Azure](https://medius.studios.ms/Embed/Video/BRK2102?sid=BRK2102), která obsahuje ukázky, ukazuje, jak migrovat aplikace PostgreSQL do služby Azure Database for PostgreSQL.
- Projděte si kurz [PostgreSQL migrovat do služby Azure Database for PostgreSQL online pomocí DMS](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online).
