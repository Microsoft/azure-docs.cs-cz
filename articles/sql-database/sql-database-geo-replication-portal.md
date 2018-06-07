---
title: 'Portál Azure: geografická replikace databáze SQL | Microsoft Docs'
description: Konfigurace geografická replikace pro Azure SQL Database v portálu Azure a inicializovat převzetí služeb při selhání
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: e6c699fdd57eecc31c51b26ccb7c06c6edbcfd03
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34646894"
---
# <a name="configure-active-geo-replication-for-azure-sql-database-in-the-azure-portal-and-initiate-failover"></a>Konfigurace aktivní geografickou replikací pro Azure SQL Database v portálu Azure a inicializovat převzetí služeb při selhání

V tomto článku se dozvíte, jak nakonfigurovat aktivní geografickou replikací pro databázi SQL v [portál Azure](http://portal.azure.com) a k zahájení převzetí služeb při selhání.

K zahájení převzetí služeb při selhání pomocí portálu Azure, najdete v části [zahájení plánovaném nebo neplánovaném převzetí služeb při selhání pro Azure SQL Database pomocí portálu Azure](sql-database-geo-replication-portal.md).

Chcete-li nakonfigurovat aktivní geografickou replikaci pomocí portálu Azure, je třeba v následujícím zdroji:

* Azure SQL database: primární databázi, kterou chcete replikovat do jiné zeměpisné oblasti.

> [!Note]
Aktivní geografickou replikaci musí být mezi databázemi ve stejném předplatném.

## <a name="add-a-secondary-database"></a>Přidání sekundární databáze
Následující postup vytvoření nové sekundární databáze ve spolupráci se geografická replikace.  

Pokud chcete přidat sekundární databáze, musí být spoluvlastník nebo vlastníka předplatného.

Sekundární databáze má stejný název jako primární databáze a ve výchozím nastavení, má stejnou úroveň služby. Sekundární databáze může být jedné databáze nebo databáze ve fondu elastické databáze. Další informace najdete v tématu [na základě DTU nákupní model](sql-database-service-tiers-dtu.md) a [nákupní model (preview) na základě vCore](sql-database-service-tiers-vcore.md).
Po sekundární se vytvoří a nasadí, data začne replikace z primární databáze do nové sekundární databázi.

> [!NOTE]
> Pokud už existuje databáze partnera (například. v důsledku ukončení předchozí relace geografická replikace) příkaz se nezdaří.
> 

1. V [portál Azure](http://portal.azure.com), přejděte do databáze, kterou chcete nastavit pro geografická replikace.
2. Na stránce databáze SQL, vyberte **geografická replikace**a potom vyberte oblast, chcete-li vytvořit sekundární databázi. Můžete vybrat všechny oblasti jiné než oblasti hostování primární databázi, ale doporučujeme, abyste [spárované oblast](../best-practices-availability-paired-regions.md).
   
    ![Konfigurace geografické replikace](./media/sql-database-geo-replication-portal/configure-geo-replication.png)
3. Vyberte nebo nakonfigurovat server a cenovou úroveň pro sekundární databázi.
   
    ![Konfigurace sekundární](./media/sql-database-geo-replication-portal/create-secondary.png)
4. Volitelně můžete přidat sekundární databáze do pružného fondu. Chcete-li vytvořit sekundární databáze ve fondu, klikněte na tlačítko **elastický fond** a vyberte fond na cílovém serveru. Fond musí již existovat na cílovém serveru. Tento pracovní postup nevytvoří fondu.
5. Klikněte na tlačítko **vytvořit** přidat sekundární.
6. Sekundární databáze byla vytvořena a zahájí se proces synchronizace replik indexů.
   
    ![Konfigurace sekundární](./media/sql-database-geo-replication-portal/seeding0.png)
7. Po dokončení procesu synchronizace replik indexů sekundární databázi zobrazí její stav.
   
    ![Synchronizace replik indexů dokončení](./media/sql-database-geo-replication-portal/seeding-complete.png)

## <a name="initiate-a-failover"></a>Zahájení převzetí služeb při selhání

Sekundární databáze je možné přepnout stane primárním serverem.  

1. V [portál Azure](http://portal.azure.com), přejděte do primární databáze ve spolupráci se geografická replikace.
2. V okně databáze SQL vyberte **všechna nastavení** > **geografická replikace**.
3. V **SEKUNDÁRNÍCH** vyberte databáze, kterou chcete stane nový primární a klikněte na tlačítko **převzetí služeb při selhání**.
   
    ![převzetí služeb při selhání](./media/sql-database-geo-replication-failover-portal/secondaries.png)
4. Klikněte na tlačítko **Ano** zahájíte převzetí služeb při selhání.

Příkaz okamžitě přepínačů sekundární databázi do primární role. 

Není k dispozici na krátkou dobu, během které obě databáze jsou k dispozici (v řádu 0 až 25 sekund), při přepínání role. Pokud primární databáze má více sekundárních databází, příkaz automaticky změní konfiguraci dalších sekundární databáze k připojení k nové primární. Celou operaci by měl trvat méně než minutu za normálních okolností. 

> [!NOTE]
> Tento příkaz je určená pro rychlé obnovení databáze v případě výpadku. Aktivuje převzetí služeb při selhání bez synchronizace dat (Vynucené převzetí služeb při selhání).  Pokud primární je online a může dojít při vydání příkazu ke ztrátě dat. zapisování transakcí. 
> 
> 

## <a name="remove-secondary-database"></a>Odebrání sekundární databáze
Tato operace trvale ukončí replikace do sekundární databáze a změn roli sekundární regulární databáze pro čtení a zápis. Pokud se přeruší připojení k sekundární databázi, příkaz úspěšné, ale je obnovit sekundární nemá ne stane pro čtení a zápis až po připojení.  

1. V [portál Azure](http://portal.azure.com), přejděte do primární databáze ve spolupráci se geografická replikace.
2. Na stránce databáze SQL, vyberte **geografická replikace**.
3. V **SEKUNDÁRNÍCH** seznamu, vyberte databázi, kterou chcete odebrat z partnerství geografická replikace.
4. Klikněte na tlačítko **zastavení replikace**.
   
    ![Odeberte sekundární](./media/sql-database-geo-replication-portal/remove-secondary.png)
5. Otevře se okno pro potvrzení. Klikněte na tlačítko **Ano** odebrání partnerství geografická replikace databáze. (Nastavte ho na databázi pro čtení a zápis není součástí žádné replikace.)

## <a name="next-steps"></a>Další postup
* Další informace o aktivní geografickou replikaci, najdete v části [aktivní geografickou replikací](sql-database-geo-replication-overview.md).
* Přehled kontinuity obchodních a scénářů najdete v tématu [obchodní kontinuity přehled](sql-database-business-continuity.md).

