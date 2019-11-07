---
title: 'Azure Portal: SQL Database geografickou replikaci '
description: Konfigurace geografické replikace pro jednu nebo sdruženou databázi v Azure SQL Database pomocí Azure Portal a zahájení převzetí služeb při selhání
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
ms.openlocfilehash: 2a9d627cb2b51d0d7d0b07052f18a8dbe1dc2f19
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73691331"
---
# <a name="configure-active-geo-replication-for-azure-sql-database-in-the-azure-portal-and-initiate-failover"></a>Konfigurace aktivní geografické replikace pro Azure SQL Database v Azure Portal a zahájení převzetí služeb při selhání

V tomto článku se dozvíte, jak nakonfigurovat [aktivní geografickou replikaci pro databáze s jedním a fondem](sql-database-active-geo-replication.md#active-geo-replication-terminology-and-capabilities) v Azure SQL Database pomocí [Azure Portal](https://portal.azure.com) a iniciovat převzetí služeb při selhání.

Informace o skupinách automatického převzetí služeb při selhání s databázemi s jednou a ve fondu najdete v tématu [osvědčené postupy použití skupin převzetí služeb při selhání s databázemi s jedním a fondem](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools). Informace o skupinách automatického převzetí služeb při selhání se spravovanými instancemi najdete v tématu [osvědčené postupy při používání skupin převzetí služeb při selhání se spravovanými instancemi](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-managed-instances)

## <a name="prerequisites"></a>Požadavky

Pokud chcete nakonfigurovat aktivní geografickou replikaci pomocí Azure Portal, budete potřebovat následující prostředek:

* Azure SQL Database: primární databáze, kterou chcete replikovat do jiné geografické oblasti.

> [!Note]
> Při použití Azure Portal můžete vytvořit pouze sekundární databázi v rámci stejného předplatného jako primární. Pokud je nutné, aby byla sekundární databáze v jiném předplatném, použijte příkaz [Create database REST API](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) nebo [ALTER DATABASE API jazyka Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql).

## <a name="add-a-secondary-database"></a>Přidat sekundární databázi

V následujících krocích se vytvoří nová sekundární databáze v partnerství geografické replikace.  

Chcete-li přidat sekundární databázi, musíte být vlastníkem nebo spoluvlastníkem předplatného.

Sekundární databáze má stejný název jako primární databáze a má ve výchozím nastavení stejnou úroveň služby a výpočetní velikost. Sekundární databází může být jedna databáze nebo databáze ve fondu. Další informace najdete v tématu [nákupní model založený na DTU](sql-database-service-tiers-dtu.md) a [nákupní model založený na Vcore](sql-database-service-tiers-vcore.md).
Po vytvoření a osazení sekundární databáze se data začnou replikovat z primární databáze do nové sekundární databáze.

> [!NOTE]
> Pokud Partnerská databáze již existuje (například v důsledku ukončení předchozího vztahu geografické replikace), příkaz se nezdařil.

1. V [Azure Portal](https://portal.azure.com)přejděte k databázi, kterou chcete nastavit pro geografickou replikaci.
2. Na stránce databáze SQL vyberte **geografickou replikaci**a potom vyberte oblast pro vytvoření sekundární databáze. Můžete vybrat libovolnou jinou oblast, než je oblast hostující primární databázi, ale doporučujeme použít [spárovánou oblast](../best-practices-availability-paired-regions.md).

    ![Konfigurace geografické replikace](./media/sql-database-geo-replication-portal/configure-geo-replication.png)
3. Vyberte nebo nakonfigurujte server a cenovou úroveň pro sekundární databázi.

    ![Konfigurovat sekundární](./media/sql-database-geo-replication-portal/create-secondary.png)
4. Volitelně můžete do elastického fondu přidat sekundární databázi. Chcete-li vytvořit sekundární databázi ve fondu, klikněte na možnost **elastický fond** a vyberte fond na cílovém serveru. Fond již musí existovat na cílovém serveru. Tento pracovní postup nevytváří fond.
5. Kliknutím na **vytvořit** přidejte sekundárního.
6. Sekundární databáze je vytvořena a proces osazení začíná.

    ![Konfigurovat sekundární](./media/sql-database-geo-replication-portal/seeding0.png)
7. Po dokončení procesu osazení sekundární databáze zobrazí svůj stav.

    ![Osazení dokončeno](./media/sql-database-geo-replication-portal/seeding-complete.png)

## <a name="initiate-a-failover"></a>Iniciovat převzetí služeb při selhání

Sekundární databázi lze přepnout tak, aby se stala primární.  

1. V [Azure Portal](https://portal.azure.com)přejděte k primární databázi v rámci partnerství geografické replikace.
2. V okně SQL Database vyberte **všechna nastavení** > **geografickou replikaci**.
3. V seznamu **sekundární** databáze vyberte databázi, která se má stát novou primární, a klikněte na **převzetí služeb při selhání**.

    ![Převzetí služeb](./media/sql-database-geo-replication-failover-portal/secondaries.png)
4. Kliknutím na **Ano** zahájíte převzetí služeb při selhání.

Příkaz okamžitě přepne sekundární databázi do primární role. Tento proces normálně by měl být dokončen do 30 sec nebo méně.

Probíhá krátká doba, během které jsou obě databáze nedostupné (v pořadí 0 až 25 sekund) při přepínání rolí. Pokud má primární databáze více sekundárních databází, příkaz automaticky znovu nakonfiguruje ostatní sekundární sekundární připojení, aby se připojil k nové primární službě. Dokončení celé operace by za normálních okolností nemělo trvat déle než minutu.

> [!NOTE]
> Tento příkaz je určený pro rychlé obnovení databáze v případě výpadku. Vyvolá převzetí služeb při selhání bez synchronizace dat (vynucené převzetí služeb při selhání)  Pokud je primární je online a potvrzování transakcí, když je příkaz vydán, může dojít ke ztrátě dat.

## <a name="remove-secondary-database"></a>Odebrat sekundární databázi

Tato operace trvale ukončí replikaci do sekundární databáze a změní roli sekundární databáze na standardní databázi pro čtení a zápis. Pokud je připojení k sekundární databázi přerušeno, příkaz se zdaří, ale sekundární se nestane pro čtení a zápis až po obnovení připojení.  

1. V [Azure Portal](https://portal.azure.com)přejděte k primární databázi v rámci partnerství geografické replikace.
2. Na stránce databáze SQL vyberte **geografickou replikaci**.
3. V seznamu **sekundárních** umístění vyberte databázi, kterou chcete odebrat, ze partnerství geografické replikace.
4. Klikněte na **zastavit replikaci**.

    ![Odebrat sekundární](./media/sql-database-geo-replication-portal/remove-secondary.png)
5. Otevře se okno potvrzení. Kliknutím na **Ano** odeberete databázi ze partnerství geografické replikace. (Nastavte ji na databázi pro čtení i zápis, která není součástí žádné replikace.)

## <a name="next-steps"></a>Další kroky

* Další informace o aktivní geografické replikaci najdete v tématu [Aktivní geografická replikace](sql-database-active-geo-replication.md).
* Další informace o skupinách automatického převzetí služeb při selhání najdete v tématu [skupiny automatického převzetí služeb při selhání](sql-database-auto-failover-group.md) .
* Přehled provozní kontinuity a scénáře najdete v tématu [Přehled provozní kontinuity](sql-database-business-continuity.md).
