---
title: 'Kurz: Geografická replikace & převzetí služeb při selhání na portálu'
description: Nakonfigurujte geografickou replikaci pro jednu nebo sdruženou databázi v Azure SQL Database pomocí portálu Azure portal a iniciujte převzetí služeb při selhání.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 02/13/2019
ms.openlocfilehash: 59616fb217b28a8c47d9a5d13e2f4c1b9a8f6bb3
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605229"
---
# <a name="configure-active-geo-replication-for-azure-sql-database-in-the-azure-portal-and-initiate-failover"></a>Konfigurace aktivní geografické replikace pro Azure SQL Database na webu Azure Portal a zahájení převzetí služeb při selhání

Tento článek ukazuje, jak nakonfigurovat [aktivní geografickou replikaci pro jednu a sdruženou databázi](sql-database-active-geo-replication.md#active-geo-replication-terminology-and-capabilities) v Azure SQL Database pomocí [portálu Azure](https://portal.azure.com) a zahájit převzetí služeb při selhání.

Informace o skupinách s automatickým převzetím služeb při selhání s jednou a sdruženými databázemi naleznete [v tématu Doporučené postupy používání skupin s podporou převzetí služeb při selhání s jednou a sdruženou databází](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools). Informace o skupinách s automatickým převzetím služeb při selhání se spravovanými instancemi naleznete [v tématu Doporučené postupy používání skupin s podporou převzetí služeb při selhání se spravovanými instancemi](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-managed-instances).

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat aktivní geografickou replikaci pomocí portálu Azure, potřebujete následující prostředek:

* Databáze Azure SQL: Primární databáze, kterou chcete replikovat do jiné geografické oblasti.

> [!Note]
> Při použití portálu Azure můžete vytvořit pouze sekundární databázi v rámci stejného předplatného jako primární. Pokud sekundární databáze je požadováno, aby se v jiném předplatném, použijte [vytvořit databázi REST API](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) nebo ALTER databáze [Transact-SQL API](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql).

## <a name="add-a-secondary-database"></a>Přidání sekundární databáze

Následující kroky vytvoří novou sekundární databázi v partnerství geografické replikace.  

Chcete-li přidat sekundární databázi, musíte být vlastníkem předplatného nebo spoluvlastníkem.

Sekundární databáze má stejný název jako primární databáze a má ve výchozím nastavení stejnou úroveň služby a výpočetní velikost. Sekundární databáze může být jedna databáze nebo sdružené databáze. Další informace naleznete v [nákupním modelu založeném na DTU](sql-database-service-tiers-dtu.md) a [nákupním modelu založeném na virtuálních jádrech](sql-database-service-tiers-vcore.md).
Po sekundární je vytvořen a osiva data začne replikovat z primární databáze do nové sekundární databáze.

> [!NOTE]
> Pokud databáze partnera již existuje (například v důsledku ukončení předchozího vztahu geografické replikace), příkaz se nezdaří.

1. Na [webu Azure Portal](https://portal.azure.com)přejděte do databáze, kterou chcete nastavit pro geografickou replikaci.
2. Na stránce databáze SQL vyberte **geografickou replikaci**a pak vyberte oblast, která chcete vytvořit sekundární databázi. Můžete vybrat libovolnou oblast než oblast hostující primární databázi, ale doporučujeme [spárovanou oblast](../best-practices-availability-paired-regions.md).

    ![Konfigurace geografické replikace](./media/sql-database-geo-replication-portal/configure-geo-replication.png)
3. Vyberte nebo nakonfigurujte server a cenovou úroveň pro sekundární databázi.

    ![Konfigurace sekundárního](./media/sql-database-geo-replication-portal/create-secondary.png)
4. Volitelně můžete přidat sekundární databázi do elastického fondu. Chcete-li vytvořit sekundární databázi ve fondu, klepněte na **elastický fond** a vyberte fond na cílovém serveru. Fond musí již existovat na cílovém serveru. Tento pracovní postup nevytvoří fond.
5. Kliknutím na **Vytvořit** přidejte sekundární.
6. Sekundární databáze je vytvořena a začíná proces osiva.

    ![Konfigurace sekundárního](./media/sql-database-geo-replication-portal/seeding0.png)
7. Po dokončení procesu osiva sekundární databáze zobrazí svůj stav.

    ![Výsev dokončen](./media/sql-database-geo-replication-portal/seeding-complete.png)

## <a name="initiate-a-failover"></a>Zahájení převzetí služeb při selhání

Sekundární databázi lze přepnout tak, aby se stala primární.  

1. Na [webu Azure Portal](https://portal.azure.com)přejděte na primární databázi v partnerství geografické replikace.
2. V okně Databáze SQL vyberte možnost**Geografická replikace všech** **nastavení** > .
3. V seznamu **SEKUNDÁRNÍ CHYBY** vyberte databázi, kterou chcete stát novou primární a klepněte na příkaz **Vynucené převzetí služeb při selhání**.

    ![Zabezpečení před selháním](./media/sql-database-geo-replication-failover-portal/secondaries.png)
4. Chcete-li zahájit převzetí služeb při selhání, klepněte na tlačítko **Ano.**

Příkaz okamžitě přepne sekundární databázi do primární role. Tento proces by měl být obvykle dokončen do 30 sekund nebo méně.

Je krátké období, během kterého jsou obě databáze k dispozici (v pořadí 0 až 25 sekund) při přepnutí rolí. Pokud má primární databáze více sekundárních databází, příkaz automaticky překonfiguruje ostatní sekundární databáze pro připojení k nové primární. Dokončení celé operace by za normálních okolností mělo trvat méně než minutu.

> [!NOTE]
> Tento příkaz je určen pro rychlé obnovení databáze v případě výpadku. Aktivuje převzetí služeb při selhání bez synchronizace dat (vynucené převzetí služeb při selhání).  Pokud primární je online a potvrzení transakcí při vydání příkazu může dojít ke ztrátě dat.

## <a name="remove-secondary-database"></a>Odebrání sekundární databáze

Tato operace trvale ukončí replikaci do sekundární databáze a změní roli sekundární databáze pro čtení a zápis. Pokud je přerušeno připojení k sekundární databázi, příkaz je úspěšný, ale sekundární se nestane čtení a zápisu až po obnovení připojení.  

1. Na [webu Azure Portal](https://portal.azure.com)přejděte na primární databázi v partnerství geografické replikace.
2. Na stránce databáze SQL vyberte **geografickou replikaci**.
3. V seznamu **SEKUNDÁRNÍ CHVĚTEV** vyberte databázi, kterou chcete odebrat z partnerství geografické replikace.
4. Klepněte na tlačítko **Zastavit replikaci**.

    ![Odebrat sekundární](./media/sql-database-geo-replication-portal/remove-secondary.png)
5. Otevře se potvrzovací okno. Klepnutím na tlačítko **Ano** odeberete databázi z partnerství geografické replikace. (Nastavte ji na databázi pro čtení a zápis, která není součástí žádné replikace.)

## <a name="next-steps"></a>Další kroky

* Další informace o aktivní geografické replikaci naleznete v [tématu aktivní geografická replikace](sql-database-active-geo-replication.md).
* Další informace o skupinách s automatickým převzetím služeb při selhání naleznete [v tématu Skupiny automatického převzetí služeb při selhání](sql-database-auto-failover-group.md)
* Přehled kontinuity provozu a scénáře najdete v tématu [Přehled kontinuity provozu](sql-database-business-continuity.md).
