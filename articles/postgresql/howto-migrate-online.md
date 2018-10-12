---
title: Migrace s minimem výpadků ke službě Azure Database for PostgreSQL
description: Tento článek popisuje, jak provést migraci minimálními prostoji databázi PostgreSQL do služby Azure Database for PostgreSQL s využitím Azure Database Migration Service.
services: postgresql
author: HJToland3
ms.author: jtoland
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 10/11/2018
ms.openlocfilehash: 80e5d30677735b35d90fda6288d7bf6f2ea4aa1b
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2018
ms.locfileid: "49093829"
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql"></a>Migrace s minimem výpadků ke službě Azure Database for PostgreSQL
Můžete provést migraci PostgreSQL do služby Azure Database for PostgreSQL s minimálními prostoji pomocí nově zavedená **Průběžná synchronizace schopností** pro [Azure Database Migration Service](https://aka.ms/get-dms) (DMS) . Tato funkce omezuje množství prostoje, které se účtují podle aplikace.

## <a name="overview"></a>Přehled
DMS provede počáteční načtení místní ke službě Azure Database for PostgreSQL a potom průběžně synchronizuje všechny nové transakce do Azure během aplikace zůstane spuštěný. Po data vyrovnat na cílové straně Azure, zastavte aplikaci pro krátké chvíle (minimální výpadek), počkejte pro poslední dávku dat (od doby, dokud aplikace není dostupná efektivně provádět žádné nové přenosy dat se zastavit aplikaci) k zachycení až do cíle a pak aktualizujte připojovací řetězec tak, aby odkazoval do Azure. Až budete hotovi, vaše aplikace bude živě v Azure!

![Průběžná synchronizace s Azure Database Migration Service](./media/howto-migrate-online/ContinuousSync.png)

DMS migraci zdroje pro PostgreSQL je aktuálně ve verzi preview. Pokud chcete vyzkoušet na služby migrace úloh PostgreSQL, registrace přes Azure DMS [náhled stránky](https://aka.ms/dms-preview) vyjádřit váš zájem. Vaše zpětná vazba je neocenitelný při řízení a usnadnit tak další vylepšení služby.

## <a name="next-steps"></a>Další postup
- Zobrazení videa [modernizace aplikací s Microsoft Azure](https://medius.studios.ms/Embed/Video/BRK2102?sid=BRK2102), která obsahuje ukázky, ukazuje, jak migrovat aplikace PostgreSQL do služby Azure Database for PostgreSQL.
- Projděte si kurz [PostgreSQL migrovat do služby Azure Database for PostgreSQL online pomocí DMS](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online).
